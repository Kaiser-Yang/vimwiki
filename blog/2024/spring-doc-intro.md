本文主要介绍如何使用 `OpenAPI 3.0` 为一个 `Spring Boot` 项目 (`web` 项目) 生成 `API` 文档。

# 常用注解
## `@Tag`
`@Tag` 用于为 `API` 文档中的 `API` 分组，可以为 `@Tag` 指定 `name` 和 `description`。`name` 相同的
`@Tag` 会被合并到一起。

通常情况下，我们会使用 `@Tag` 标识一个 `Controller` 类。

## `@Operation`
`@Operation` 用于为 `API` 文档中的 `API` 添加描述信息，可以为 `@Operation` 指定 `summary` 和 `description`。

## `@Parameters` 和 `@Parameter`
`@Parameters` 用于为 `API` 文档中的 `API` 添加参数信息，`@Parameters` 中包含多个 `@Parameter`。

`@Parameter` 用于为 `API` 文档中的 `API` 添加参数信息，可以为 `@Parameter` 指定 `name`、`in`、`description`、
`required`、`schema` 等属性。

## `@Schema`
`@Schema` 用于给对象添加描述信息，可以为 `@Schema` 指定 `name`、`title`、`description`、`example` 等属性。

## `@Hidden`, `@Parameter(hidden = true)` 和 `@Operation(hidden = true)`
`@Hidden` 用于隐藏一个 `Controller` 类，`@Parameter(hidden = true)` 用于隐藏一个参数，`@Operation(hidden = true)`
用于隐藏一个 `API` (方法)。

## `@ApiResponses` 和 `@ApiResponse`
`@ApiResponses` 用于为 `API` 文档中的 `API` 添加响应信息，`@ApiResponses` 中包含多个 `@ApiResponse`。

`@ApiResponse` 用于为 `API` 文档中的 `API` 添加响应信息，可以为 `@ApiResponse` 指定 `responseCode`、`description`、
`content` 等属性。

# 一个完整的例子
我们首先为 `DTO` 对象添加各种的 `@Schema` 注解：

```java
@Data
@Schema(description = "User Data Transfer Object")
public class UserDTO {
    @Schema(accessMode = Schema.AccessMode.READ_ONLY, description = "User ID")
    private Long id;
    @Schema(description = "Username", requiredMode = Schema.RequiredMode.REQUIRED, example = "admin")
    private String username;
    @Schema(description = "Email", requiredMode = Schema.RequiredMode.REQUIRED, example = "admin@cmipt.edu")
    private String email;
    @Schema(description = "User Password (Unencrypted)", requiredMode = Schema.RequiredMode.REQUIRED, example = "admin")
    private String userPassword;
}
```

接下来我们为一个 `Controller` 类添加 `@Tag`、`@Operation`、`@ApiResponses` 和 `@ApiResponse` 等注解：

```java
@Controller
@RequestMapping("/user")
@Tag(name = "User", description = "User Related APIs")
public class UserController {
    @Autowired
    private UserService userService;

    @PostMapping
    @Operation(
        summary = "Create a new user",
        description = "Create a new user with the given information",
        tags = { "User", "Post Method" }
    )
    @ApiResponses({
            @ApiResponse(responseCode = "200", description = "User created successfully"),
            @ApiResponse(responseCode = "400", description = "User creation failed")
    })
    public ResponseEntity<Void> createUser(@RequestBody UserDTO user) {
        // some code here
        return ResponseEntity.ok().build();
    }
}
```

经过上面的操作后，我们启动程序，访问 `http://localhost:8080/swagger-ui/index.html`，就可以看到生成的 `API` 文档了。

![](https://raw.githubusercontent.com/Kaiser-Yang/image-hosting-site/main/20240421-20250421/20240821214051.png){: .img-fluid}
