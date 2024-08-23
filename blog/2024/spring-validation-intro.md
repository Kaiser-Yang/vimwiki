在开发 `web` 项目的过程中，我们往往需要对从前端接收到的数据进行校验，如果我们使用大量的 `if else`
来进行校验，那么代码会变得非常臃肿，而且不易维护。这时候我们可以使用 `Spring-Validation` 来进行数据
校验。

# 使用 `JSR 303` 注解

`Spring-Validation` 提供了对于 `JSR 303` 中的注解的支持，我们可以使用 `JSR 303` 中的注解来对数据进行校验。

简单来讲，对于一个 `DTO` 对象，我们可以在其属性上添加 `JSR 303` 中的注解，然后在 `Controller` 中对
参数使用 `@Valid` 或者 `@Validated` 注解来对数据进行校验。

例如，在进行用户注册的时候，我们从前端接收用户的用户名、密码和邮箱，我们可以创建如下的 `DTO` 类：

```java
@Data
public class UserDTO {
    @Size(min = 1, max = 20, message = "Username length must be between 1 and 20 characters")
    private String username;

    @Email(message = "Email format is incorrect")
    @NotBlank(message = "Email cannot be blank")
    private String email;

    @Size(min = 6, max = 20, message = "Password length must be between 6 and 20 characters")
    private String userPassword;
}
```

上面的注解的意思是不言而喻的。接着我们只需要在需要校验的地方使用 `@Valid` 或者 `@Validated` 注解即可：

```java
@Controller
@RequestMapping("/user")
public class UserController {
    @Autowired private UserService userService;

    @PostMapping
    public ResponseEntity<Void> createUser(@Valid @RequestBody UserDTO user) {}
}
```

通过上面的操作后，如果前端传入的数据不符合要求，那么 `Spring` 会抛出一个
`MethodArgumentNotValidException` 异常，这个异常的默认处理方式是返回一个 `400` 的状态码和一个
`JSON` 格式的错误信息。我们可以自己定义异常处理器。

# 自定义异常处理器

异常处理器的定义有两种模式，一种是全局异常处理器，一种是局部异常处理器。局部异常处理器只会处理当前
`Controller` 中的异常，而全局异常处理器会处理所有的异常。局部异常处理器具有更高的优先级。

## 全局异常处理器

要定义一个全局异常处理器，我们可以创建一个类并且添加 `@RestControllerAdvice` 或
`@ControllerAdvice` 注解：

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public Map<String, String> handleMethodArgumentNotValidException(MethodArgumentNotValidException e) {
        Map<String, String> errors = new HashMap<>();
        e.getBindingResult().getFieldErrors().forEach((error) -> {
            String fieldName = error.getField();
            String errorMessage = error.getDefaultMessage();
            errors.put(fieldName, errorMessage);
        });
        return errors;
    }
}
```

在上面的例子中，我们只将异常中的字段名和错误信息提取出来，然后返回一个 `Map` 对象。这样我们就可以
很清楚的从错误信息中看到哪个字段出现了错误。例如在用户名为空的时候，我们可以在响应体中看到：

```json
{
  "username": "Username length must be between 1 and 20 characters"
}
```

## 局部异常处理器

局部异常处理器的定义只需要将异常处理器定义在某个 `Controller` 中即可。

# 分组校验
现在我们为上面的 `DTO` 对象添加一个 `id` 字段，在创建用户的时候，不需要指定 `id` 字段，但是在更新
用户的时候，`id` 字段是必须的。这时候我们可以使用分组校验来解决这个问题。

首先我们需要在 `DTO` 对象中定义一个分组：(这里使用内部类进行演示)

```java
@Data
public class UserDTO {
    @NotNull(groups = Update.class)
    @Null(groups = Create.class)
    private Long id;

    @Size(
        groups = {Update.class, Create.class},
        min = 1, max = 20,
        message = "Username length must be between 1 and 20 characters")
    private String username;

    @Email(groups = {Update.class, Create.class}, message = "Email format is incorrect")
    @NotBlank(groups = {Update.class, Create.class}, message = "Email cannot be blank")
    private String email;

    @Size(
        groups = {Update.class, Create.class},
        min = 6, max = 20,
        message = "Password length must be between 6 and 20 characters")
    private String userPassword;
    
    public interface Update {}

    public interface Create {}
}
```

然后我们在 `Controller` 中使用 `@Validated` (此时不能使用 `@Valid` 注解) 注解来指定分组：

```java
@Controller
@RequestMapping("/user")
public class UserController {
    @Autowired private UserService userService;

    @PostMapping
    public ResponseEntity<Void> createUser(@Validated(UserDTO.Create.class) @RequestBody UserDTO user) {
        return null;
    }

    @PatchMapping
    public ResponseEntity<Void> updateUser(@Validated(UserDTO.Update.class) @RequestBody UserDTO user) {
        return null;
    }
}
```

# 常用注解

`JSR 303` 中定义了很多注解，这里列出一些常用的注解：

| 注解           | 说明 |
| ---            | --- |
| `@Null`        | 验证对象是否为 `null` |
| `@NotNull`     | 验证对象是否不为 `null`，无法查检长度为 `0`的字符串 |
| `@NotBlank`    | 验证 `String` 对象是否不为 `null`、长度是否大于 `0`、是否包含至少一个非空白字符 |
| `@AssertFalse` | 验证 `Boolean` 对象是否为 `false` |
| `@AssertTrue`  | 验证 `Boolean` 对象是否为 `true` |
| `@Min`         | 验证 `Number` 对象是否大于等于指定的值 |
| `@Max`         | 验证 `Number` 对象是否小于等于指定的值 |
| `@DecimalMin`  | 验证 `Number` 和 `String` 对象是否大于等于指定的值 |
| `@DecimalMax`  | 验证 `Number` 和 `String` 对象是否小于等于指定的值 |
| `@Digits`      | 验证 `Number` 和 `String` 对象是否是指定范围内的数字 |
| `@Past`        | 验证 `Date` 对象是否是在当前时间之前 |
| `@Future`      | 验证 `Date` 对象是否是在当前时间之后 |
| `@Size`        | 验证对象（`Array`、`Collection`、`Map`、`String`）长度是否在给定的范围之内 |
| `@Pattern`     | 验证 `String` 对象是否符合正则表达式的规则 |

下面的注解是 `Hibernate Validator` 中定义的注解：

| 注解        | 说明 |
| ---         | --- |
| `@Email`    | 验证 `String` 对象是否是一个合法的电子邮件地址 |
| `@Length`   | 验证 `String` 对象是否是在给定的范围之内 |
| `@NotEmpty` | 验证对象是否不为 `null` 或者 `""` |
| `@Range`    | 验证 `Number` 对象是否在给定的范围之内 |

# `MessageSource` 管理错误信息
在上面的例子中，我们直接在注解中写入了错误信息，这样的做法并不利于开发。例如，我们如果在编写测试的时候
需要检验错误信息是否一直，我们不得不再次写入错误信息。而修改的时候，我们则需要对多处进行修改。

为了解决这个问题，我们可以使用 `MessageSource` 来管理错误信息。

首先我们在 `application.yml` ( 或者 `application.properties` ) 中添加如下配置：

```yaml
spring:
  messages:
    basename: message/message
    encoding: UTF-8
```

标识我们的错误信息文件在 `resources` 目录下的 `message` 目录中，文件名为 `message.properties` ( 只能
使用 `properties` 文件 )。

接下来，我们在 `resources` 目录下创建一个 `message` 目录，并且在其中创建一个 `message.properties` 文件，
在文件中添加如下内容：

```properties
# UserDTO validation messages
UserDTO.username.Size=Username must be between {min} and {max} characters
UserDTO.email.NotBlank=Email cannot be blank
UserDTO.email.Email=Email must be a valid email address
UserDTO.userPassword.Size=Password must be between {min} and {max} characters
```

然后我们可以直接在注解中的 `message` 部分使用 `MessageSource` 的 `key` 来引用错误信息：

```java
@Data
public class UserDTO {
    @Size(
            groups = {UpdateGroup.class, CreateGroup.class},
            min = ConstantProperty.MIN_USERNAME_LENGTH,
            max = ConstantProperty.MAX_USERNAME_LENGTH,
            message = "{UserDTO.username.Size}")
    private String username;

    @Email(
            groups = {UpdateGroup.class, CreateGroup.class},
            message = "{UserDTO.email.Email}")
    @NotBlank(
            groups = {UpdateGroup.class, CreateGroup.class},
            message = "{UserDTO.email.NotBlank}")
    private String email;

    @Size(
            groups = {UpdateGroup.class, CreateGroup.class},
            min = ConstantProperty.MIN_PASSWORD_LENGTH,
            max = ConstantProperty.MAX_PASSWORD_LENGTH,
            message = "{UserDTO.userPassword.Size}")
    private String userPassword;
}
```

上面通过 `message = "{xxx.xxx.xxx}"` 的方式获取变量是 `Hibernate` 提供的支持。而我们编写的字符串中
的 `{min}` 和 `{max}` 会被替换成注解中的 `min` 和 `max` 的值。

但是我们在使用 `MessageSource` 的时候并不支持这种自动替换的方式，因此我们需要自己书写一个工具类来
替换这些变量：

```java
@Component
public class MessageSourceUtil {
    private static MessageSource messageSource;

    // 通过构造器注入 MessageSource
    MessageSourceUtil(MessageSource messageSource) {
        MessageSourceUtil.messageSource = messageSource;
    }

    public static String getMessage(String code, Object[] args) {
        String message = messageSource.getMessage(code, null, null);
        // 获取到的 message 中包含了 {min} 和 {max} 这样的变量，我们需要将其替换成实际的值
        Pattern pattern = Pattern.compile("\\{.*?\\}");
        Matcher matcher = pattern.matcher(message);
        int i = 0;
        while (matcher.find()) {
            message = message.replace(matcher.group(), args[i].toString());
            i++;
        }
        return message;
    }
}
```

这样我们可以在 `Controller` 中使用 `MessageSourceUtil` 来获取错误信息：

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@AutoConfigureMockMvc
public class UserControllerTest {
    @Autowired private MockMvc mvc;

    @Test
    public void testCreateUserInvalid() throws Exception {
        String user =
                """
                {
                    "name": "test",
                    "email": "invalid email address",
                    "userPassword": ""
                }
                """;
        mvc.perform(
                        MockMvcRequestBuilders.post("/user")
                                .contentType(MediaType.APPLICATION_JSON)
                                .content(user))
                .andExpectAll(
                        status().isBadRequest(),
                        jsonPath(
                                "$.email",
                                equalTo(MessageSourceUtil.getMessage("UserDTO.email.Email"))),
                        jsonPath(
                                "$.userPassword",
                                equalTo(
                                        MessageSourceUtil.getMessage(
                                                "UserDTO.userPassword.Size",
                                                ConstantProperty.MIN_PASSWORD_LENGTH,
                                                ConstantProperty.MAX_PASSWORD_LENGTH
                                                ))),
                        content().contentType(MediaType.APPLICATION_JSON));
    }
```

在上面的代码中，我们同样将密码长度等常量信息放在了 `ConstantProperty` 类中，这样我们可以在测试中直接
引用这些常量。

## 国际化
实际上，`MessageSource` 是用来支持国际化的。在上面的例子中，我们只是使用了一个 `properties` 文件，
实际上我们可以使用多个 `properties` 文件来支持多种语言。

我们只需要在 `resources/message` (取决于我们指定的 `basename` ) 目录下创建多个 `properties` 文件，
例如 `message.properties`、`message_en.properties`、`message_zh.properties` 等等。这样在发送请求的
时候，我们可以在请求头中添加 `Accept-Language` 来指定语言，`Spring` 会根据请求头中的语言来选择对应的
`properties` 文件中的信息。
