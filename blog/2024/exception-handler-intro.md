在之前的 [Spring-Validation](/blog/2024/spring-validation-intro) 中简单介绍了如何通过
`@RestControllerAdvice` 或者 `@ControllerAdvice` 注解来处理全局异常，但是这种方式只能
处理 `Controller` 层抛出的异常。

这篇文章介绍如何处理自定义 `Filter` 抛出的异常。

# 自定义 `Filter`
例如我们可能需要一个 `Filter` 来检验 `Token` 是否合法，而检查的过程中发现 `Token` 不合法，则抛出异常。

```java
@WebFilter
public class JwtFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
            throws ServletException, IOException {
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
}
```

注意：通过 `@WebFilter` 注释的 `Filter` 需要在 `@SpringBootApplication` 启动类中添加
`@ServletComponentScan("xxx.xxx.xx")` 注解指定 `Filter` 的查找路径。

上面过程中抛出的异常不会被 `@RestControllerAdvice` 或者 `@ControllerAdvice` 捕获。

# `HandlerExceptionResolver`
实际上 `Spring Boot` 的异常处理是通过 `HandlerExceptionResolver` 来实现的。`Spring Boot` 中已经存在
两个默认的 `HandlerExceptionResolver`，我们可以通过 `@Qualifier("HandlerExceptionResolver")` 配合
`@Autowired` 获取到负责处理异常的 `HandlerExceptionResolver`，然后显式调用 `resolveException` 方法来处理异常。

# 解决方案
要实现这些，我们只需要自定义一个 `Filter` 并让其第一个执行，对捕获到的异常显式调用 `resolveException`
进行处理就可以触发全局定义的异常处理器：

```java
@Component
@Order(Ordered.HIGHEST_PRECEDENCE)
public class ExceptionHandlerFiter extends OncePerRequestFilter {
    @Autowired
    @Qualifier("handlerExceptionResolver")
    private HandlerExceptionResolver resolver;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
            throws ServletException, IOException {
        try {
            filterChain.doFilter(request, response);
        } catch (Exception e) {
            resolver.resolveException(request, response, null, e);
        }
    }
}
```

注意：`@WebFilter` 不能指定 `Filter` 的执行顺序 (实际上是可以的，通过指定 `@WebFilter` 中的
`filterName`，执行顺序是按照该字段的字典序)，要指定 `Filter` 的执行顺序，我们可以通 `@Order` 和
`@Component` 来实现，`@Order` 的值越小，执行顺序越靠前，上述例子的 `Ordered.HIGHEST_PRECEDENCE`
实际上是 `Integer.MIN_VALUE`。

`resolveException` 会自动根据异常的类型找到对应的异常处理器进行处理。这样，我们就可以处理 `Filter`
抛出的异常了：

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ResponseStatus(HttpStatus.UNAUTHORIZED)
    @ExceptionHandler(JwtException.class)
    public ErrorVO handleJwtException(JwtException e, HttpServletRequest request) {
        logger.error("Invalid Token from {}", request.getRemoteAddr());
        return new ErrorVO(ErrorMessageConstant.INVALID_TOKEN);
    }
}
```
