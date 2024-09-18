在 [使用 `Spring-Validation` 进行参数校验](/blog/2024/spring-validation-intro) 中，我对如何使用
`Spring Validation` 进行参数校验进行了介绍。但是其中只介绍了从请求体中获取的自定义参数如何进行校验，
并没有介绍对于一些基本类型的参数如何进行校验。这些基本类型参数往往通过 `@PathVariable` 从请求路径中
获取或者通过 `@RequestParam` 从请求参数中获取。本文将介绍如何使用 `Spring Validation` 对通过
`@PathVariable` 和 `@ReqeustParam` 获取的基本类型参数进行校验。

# `@Validated`
要对通过 `@PathVariable` 和 `@RequestParam` 获取的参数进行校验，我们必须要在控制器类上添加
`@Validated` 注解。该注解由 `Spring` 提供以实现比 `JSR-303` 更加灵活的功能。

在 `@Validated` 官方的文档中，有这样一句话：
> Applying this annotation at the method level allows for overriding the validation groups for
a specific method but does not serve as a pointcut; a class-level annotation is nevertheless
necessary to trigger method validation for a specific bean to begin with.

简单来说就是当 `@Validated` 在方法上的参数使用时，可以实现组校验，而如果要启用方法级别的校验，则必须
在类上添加 `@Validated` 注解。

完成了上面的准备工作后，我们就可以在控制器类中的方法参数上添加校验注解了。例如：

```java
@Validated
@RestController
public class UserController {
    @Autowired private UserService userService;

    @GetMapping(ApiPathConstant.USER_CHECK_EMAIL_VALIDITY_API_PATH)
    public void checkEmailValidity(
            @Email(message = "USERDTO_EMAIL_EMAIL {UserDTO.email.Email}")
            @NotBlank(message = "USERDTO_EMAIL_NOTBLANK {UserDTO.email.NotBlank}")
            @RequestParam("email")
            String email) {
        QueryWrapper<UserPO> wrapper = new QueryWrapper<UserPO>();
        wrapper.eq("email", email);
        if (userService.exists(wrapper)) {
            throw new GenericException(ErrorCodeEnum.EMAIL_ALREADY_EXISTS, email);
        }
    }
}
```

这样操作后，当请求到达 `checkEmailValidity` 方法时，`Spring` 会自动对 `email` 参数进行校验。需要注意
的是：此时如果检验没有通过会抛出 `ConstraintViolationException` 而不是
`MethodArgumentNotValidException`。我们可以通过以下的方式进行全局异常处理：

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(ConstraintViolationException.class)
    public ResponseEntity<ErrorVO> handleConstraintViolationException(
            ConstraintViolationException e, HttpServletRequest request) {
        // do something
    }
}
```

# 巨人的肩膀
* [Validation with Spring Boot - the Complete Guide](https://reflectoring.io/bean-validation-with-spring-boot/#validating-path-variables-and-request-parameters)
