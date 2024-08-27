在实际上开发中我们可能会需要涉及到用户的认证和授权功能，`Spring Security` 当然可以实现这一点，但是
对于一些简单的开发场景 (不区分角色)，我们实际上只需要利用 `JJWT` 就能实现。

# 需求
考虑这样的场景，用户登录成功后，后续的操作不再需要用户名和密码，可以通过 `token` 来进行访问，`token`
一定时间内有效，过期后需要重新登录。

对于一些修改要求，例如修改用户信息，我们需要保证只有用户自己才能修改自己的信息，这时候我们可以对 `token` 进行
解析，获取用户信息，然后进行比对。

## 双 `token` 模式
对于 `token` 的设计，我们可以采用双 `token` 模式，即一个 `access token` 和一个 `refresh token`，`access token`
用于访问，`refresh token` 用于刷新 `access token`。

这样的设计可以有效的保证安全性，因为 `access token` 的有效时间较短，即使被盗取，也只能在有效时间内使用，
而 `refresh token` 通常有效时间较长，但是只能用于刷新 `access token`，不能用于访问，且 `refresh token`
只有在每次 `access token` 过期后才会被传递，这样 `refresh token` 暴露的风险较小。

# 实现
## `JwtUtil`
我们首先需要自定义一个工具类，该工具类可以生成和解析 `token`：

```java
public class JwtUtil {
    private static final String TOKEN_TYPE_CLAIM = "tokenType";
    private static final String ID_CLAIM = "id";
    private static final SecretKey SECRET_KEY = Jwts.SIG.HS256.key().build();

    public static String generateToken(long id, TokenTypeEnum tokenType) {
        return Jwts.builder().issuedAt(new Date())
                .expiration(new Date(System.currentTimeMillis() +
                    (tokenType == TokenTypeEnum.ACCESS_TOKEN ?
                     ApplicationConstant.ACCESS_TOKEN_EXPIRATION :
                     ApplicationConstant.REFRESH_TOKEN_EXPIRATION)))
                .claim(ID_CLAIM, id)
                .claim(TOKEN_TYPE_CLAIM, tokenType.name())
                .signWith(SECRET_KEY)
                .compact();
    }

    public static String generateToken(String id, TokenTypeEnum tokenType) {
        return generateToken(Long.valueOf(id), tokenType);
    }

    public static String getID(String token) {
        return String.valueOf(
                Jwts.parser()
                    .verifyWith(SECRET_KEY)
                    .build()
                    .parseSignedClaims(token)
                    .getPayload()
                    .get(ID_CLAIM, Long.class));
    }

    public static TokenTypeEnum getTokenType(String token) {
        return TokenTypeEnum.valueOf(
                Jwts.parser()
                    .verifyWith(SECRET_KEY)
                    .build()
                    .parseSignedClaims(token)
                    .getPayload()
                    .get(TOKEN_TYPE_CLAIM, String.class));
    }
}
```

## `Filter`
要实现认证和授权，我们可以通过 `Filter` 拦截所有的非登录和注册的业务请求，然后进行 `token` 的解析和验证：

```java
@Component
@Order(Ordered.LOWEST_PRECEDENCE)
public class JwtFilter extends OncePerRequestFilter {
    private Set<String> ignorePath = Set.of(
            ApiPathConstant.AUTHENTICATION_SIGN_UP_API_PATH,
            ApiPathConstant.AUTHENTICATION_SIGN_IN_API_PATH,
    );

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
        FilterChain filterChain) throws ServletException, IOException {
        // ignore non business api and some special api
        if (!request.getRequestURI().startsWith(ApiPathConstant.ALL_API_PREFIX) ||
            ignorePath.contains(request.getRequestURI())) {
            filterChain.doFilter(request, response);
            return;
        }
        // throw exception if authorization failed
        authorize(request, request.getHeader("Token"));
        filterChain.doFilter(request, response);
    }

    private void authorize(HttpServletRequest request, String token) {
        switch (JwtUtil.getTokenType(token)) {
            case ACCESS_TOKEN:
                // ACCESS_TOKEN can not be used for refresh
                if (request.getRequestURI().equals(
                    ApiPathConstant.AUTHENTICATION_REFRESH_API_PATH)) {
                    throw new AccessDeniedException(ErrorMessageConstant.ACCESS_DENIED);
                }
                String idInToken = JwtUtil.getID(token);
                switch (request.getMethod()) {
                    case "GET":
                        break;
                    case "POST":
                        // User can not update other user's information
                        if (request.getRequestURI().startsWith(ApiPathConstant.USER_API_PREFIX) &&
                            !idInToken.equals(getFromRequestBody(request, "id"))) {
                            throw new AccessDeniedException(ErrorMessageConstant.ACCESS_DENIED);
                        }
                        break;
                    default:
                        throw new AccessDeniedException(ErrorMessageConstant.ACCESS_DENIED);
                }
                break;
            case REFRESH_TOKEN:
                // REFRESH_TOKEN can only be used for refresh
                if (!request.getRequestURI().equals(
                    ApiPathConstant.AUTHENTICATION_REFRESH_API_PATH)) {
                    throw new AccessDeniedException(ErrorMessageConstant.ACCESS_DENIED);
                }
                break;
            default:
                throw new AccessDeniedException(ErrorMessageConstant.ACCESS_DENIED);
        }
    }

    private String getFromRequestBody(HttpServletRequest request, String key) {
        try {
            BufferedReader reader = request.getReader();
            StringBuilder builder = new StringBuilder();
            String line = reader.readLine();
            while (line != null) {
                builder.append(line);
                line = reader.readLine();
            }
            reader.close();
            var json = JsonParserFactory.getJsonParser().parseMap(builder.toString());
            return json.get(key).toString();
        } catch (Exception e) {
            // unlikely to happen
            return null;
        }
    }
}
```

## `SignIn`
对于登录接口，我们只需要返回额外 `access token` 和 `refresh token`，对于刷新服务我们直接返回新的
`access token` 即可：

```java
// 我们在 UserVO 中保存 token
public record UserVO(
    Long id, String username, String email, String accessToken, String refreshToken) {
    public UserVO(UserPO userPO) {
        this(userPO.getId(), userPO.getUsername(), userPO.getEmail(),
                JwtUtil.generateToken(
                        userPO.getId(), TokenTypeEnum.ACCESS_TOKEN),
                JwtUtil.generateToken(
                        userPO.getId(), TokenTypeEnum.REFRESH_TOKEN));
    }
}

@RestController
public class AuthenticationController {
    @Autowired
    private UserService userService;

    @PostMapping(ApiPathConstant.AUTHENTICATION_SIGN_IN_API_PATH)
    public ResponseEntity<?> signIn(@Validated @RequestBody UserSignInDTO user) {
        QueryWrapper<UserPO> wrapper = new QueryWrapper<UserPO>();
        wrapper.eq("username", user.username());
        wrapper.eq("user_password", MD5Converter.convertToMD5(user.userPassword()));
        if (!userService.exists(wrapper)) {
            throw new IllegalArgumentException(ErrorMessageConstant.WRONG_SIGN_IN_INFORMATION);
        }
        return ResponseEntity.ok(new UserVO(userService.getOne(wrapper)));
    }

    @GetMapping(ApiPathConstant.AUTHENTICATION_REFRESH_API_PATH)
    public String refreshToken(@RequestHeader("Token") String token) {
        return JwtUtil.generateToken(JwtUtil.getID(token), TokenTypeEnum.ACCESS_TOKEN);
    }
}
```

# 问题
## 如何让 `token` 在用户修改密码后失效
一个情况是当用户发现自己的 `token` 或者密码泄漏后，希望马上进行密码的修改且修改后之前产生的 `token` 失效。

这里给出一种简单的解决方案：我们需要缓存机制，保存用户的 `id` 和密码修改的时间，每次验证 `token` 时，我们
需要验证 `token` 的发布时间是否在密码修改时间之前，如果是则说明 `token` 失效。

## 如何在用户进行登出操作后使 `token` 失效
对于主动让一个 `token` 失效，我们可以采用黑名单机制，即将失效的 `token` 加入黑名单，每次验证 `token` 时，
我们需要验证 `token` 是否在黑名单中，如果是则说明 `token` 失效。
