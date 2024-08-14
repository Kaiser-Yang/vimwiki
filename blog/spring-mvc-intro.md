# `Servlet` 与 `Spring MVC`
在开始介绍 `Spring MVC` 之前，我们需要先了解一下什么 `Servlet`。

## `Servlet`
`Servlet` 是 `Java` 的一个规范，其提供了 `Java` 处理 `HTTP` 请求的能力，`Servlet` 是基于 `Java` 的
`Web` 开发的基础，通过自定义 `Servlet` 并继承 `HttpServlet` 可以处理 `HTTP` 请求。而在 `MVC` 的架构中，
`Servlet` 通常可以看成时 `Controller` 层。

## `Spring MVC`
`Spring MVC` 是 `Spring` 框架的一个模块，其提供了一个 `MVC` 的架构，可以帮助我们更好的开发 `Web` 应用。
`Spring MVC` 是基于 `Servlet` 的，其提供了一个 `DispatcherServlet` 来处理 `HTTP` 请求，`DispatcherServlet`
会根据请求的 `URL` 找到对应的 `Controller` 来处理请求，`Controller` 会返回一个 `ModelAndView` 对象，
`ModelAndView` 对象包含了 `Model` 和 `View`，`Model` 用于存放数据，`View` 用于展示数据。但是在 `Spring Boot`
中 `Controller` 通常不会返回 `ModelAndView` 对象，而是直接返回一个对象，`Spring Boot` 会自动将对象转换为 `JSON`
格式返回给客户端。

# `DispatcherServlet`
`DispatcherServlet` 是 `Spring MVC` 的核心，其继承自 `HttpServlet`，`DispatcherServlet` 会根据请求的 `URL`
找到对应的 `Controller` 来处理请求，`DispatcherServlet` 会根据请求的 `URL` 找到一个 `HandlerMapping` 对象，
`HandlerMapping` 对象会根据请求的 `URL` 找到对应的 `Controller`，`Controller` 会返回一个 `ModelAndView` 对象，
`DispatcherServlet` 会根据 `ModelAndView` 对象找到对应的 `View` 来展示数据。

不过在现在看来 `Spring MVC` 已经渐渐的失去了 `V` 的功能，`Spring MVC` 通常只用来处理 `HTTP` 请求，而
`View` 通常是前端框架来处理的，`Spring Boot` 通常会直接返回一个对象，`Spring Boot` 会自动将对象转换为
`JSON` 格式返回给客户端。

因此在介绍的时候不会完整恩介绍 `Spring MVC` 而是着重的介绍如何使用一些 `Controller` 层的注解。

# `Filters`
`Filters` 是 `Servlet` 的一个规范，其可以用来处理 `HTTP` 请求，`Filters` 通常用来处理 `HTTP` 请求的
一些预处理和后处理，`Filters` 通常可以用来处理 `HTTP` 请求的编码，`HTTP` 请求的安全等。例如我们可以
`Filter` 只允许通过 `Get` 方法访问某个 `URL`，或者我们可以在 `Filter` 中对 `HTTP` 请求的编码进行处理。
同一个 `URL` 可以有多个 `Filter`，`Filter` 会按照 `Filter` 的 `order` 属性的值来执行，`order` 的值
越小越先执行。 `Filter` 运行在 `DispatcherServlet` 之前。

# 请求与多线程
在这里不得不介绍一下 `Spring MVC` 中处理请求的多线程问题，`Spring MVC` 是基于 `Servlet` 的，`Servlet`
是多线程的，`Servlet` 会为每一个请求创建一个线程来处理请求，因此在 `Spring MVC` 中处理请求的方法是多线程的，
而线程池是由 `Tomcat` (默认的 `Web` 服务器) 来管理的。

这也就涉及到了老生常谈的问题：`Spring` 与多线程的问题。`Spring` 本身对于多线程并没有什么特殊的处理，
且在使用单俐模式创建对象时，`Spring` 不是线程安全的，而在 `Spring` 处理请求的过程中，我们知道 `Tomcat`
会为每一个 `HTTP` 请求创建一个线程进行处理，因此在 `Spring MVC` 中处理请求的方法是多线程的，这会引发
线程安全的问题，但是在实际的过程中，大多数情况下不会出现线程安全问题，这又是为什么呢？

在实际处理一个请求的 `Controller` 类中，我们会发现出现的所有的变量都是局部变量，这样的类也被称为
无状态类，无状态类是线程安全的，因为每一个线程都会有自己的局部变量，不会出现线程安全的问题。但是对于
`DAO` 层的类，我们往往需要额外考虑线程安全的问题。

# 使用注解编写 `Controller`
## `@Controller`
该注解用于标记一个类是 `Controller` 类，`Spring` 会自动扫描所有的 `Controller` 类，并将其注册到
`Spring` 容器中。

## `@RequestMapping`
该注解用于标记一个方法处理的 `URL`，`@RequestMapping` 可以标记在类上，也可以标记在方法上，`@RequestMapping`
可以接受一个 `URL`，也可以接受一个 `URL` 的数组，`@RequestMapping` 还可以接受一些参数，例如：`method`、
`consumes`、`produces`、`headers`、`params`。

`method` 用于指定请求的方法，`consumes` 用于指定请求的 `Content-Type`，`produces` 用于指定返回的
`Content-Type`，`headers` 用于指定请求的头部信息，`params` 用于指定请求的参数。

但是，一般情况下我们并不直接使用 `@RequestMapping` 注解，而是使用以下几种衍生注解：
* `@GetMapping` 用于处理 `GET` 请求，等价于 `@RequestMapping(method = RequestMethod.GET)`
* `@PostMapping` 用于处理 `POST` 请求，等价于 `@RequestMapping(method = RequestMethod.POST)`
* `@PutMapping` 用于处理 `PUT` 请求，等价于 `@RequestMapping(method = RequestMethod.PUT)`
* `@DeleteMapping` 用于处理 `DELETE` 请求，等价于 `@RequestMapping(method = RequestMethod.DELETE)`

以下的几个例子展示了如何指定请求的 `Content-Type`、`Accept`、`Headers`、`Params`：
1. `@GetMapping(path = '/test', consumes = 'application/json')`
2. `@GetMapping(path = '/test', headers = 'Content-Type=application/json')`
3. `@GetMapping(path = '/test', produces = 'application/json')`
4. `@GetMapping(path = '/test', headers = 'Accept=application/json')`
5. `@GetMapping(path = '/test', headers = 'MyHeader=myValue')`
6. `@GetMapping(path = '/test', params = 'myParam=myValue')`

上述的例子中的第一条于第二条、第三条于第四条等价。也就是说对于 `consumes` 参数而言，只有当请求头中的
`Content-Type` 与 `consumes` 参数的值相同时，才会匹配成功，对于 `produces` 参数而言，只有当请求头中的
`Accept` 与 `produces` 参数的值相同时，才会匹配成功。`Spring MVC` 官方更加推荐使用 `consumes` 和 `produces`
参数来指定请求的 `Content-Type` 和 `Accept`。

其他的几个注解可以类似地使用 `consumes`、`produces`、`headers`、`params` 参数。

NOTE: 如果一个 `Controller` 的每个方法都需要指定 `consumes`、`produces`、`headers`、`params` 参数，
那么我们可以在 `Controller` 类上使用 `consumes`、`produces`、`headers`、`params` 参数，这样在 `Controller`
类中的每个方法都会默认使用这些参数，而如果某个方法需要不同的参数，那么可以在方法上覆盖这些参数。

接下来介绍一下 `path` 参数地模糊匹配规则：
* `"?"` 匹配一个字符。
* `"*"` 匹配一个路径下的任意字符。
* `"**"` 匹配一个或者多个路径下的任意字符。
* `"/projects/{project}/versions"` 匹配后将 `project` 的值传递给 `@PathVariable` 注解的参数。
* `"/projects/{project:[a-z]+}/versions"` 匹配后将 `project` 的值传递给 `@PathVariable` 注解的参数，
且 `project` 的值必须是小写字母。
* `"/{name:[a-z-]+}-{version:\\d\\.\\d\\.\\d}{ext:\\.[a-z]+}"` 匹配后将 `name` 的值传递给 `@PathVariable`
注解的参数，`version` 的值传递给 `@PathVariable` 注解的参数，`ext` 的值传递给 `@PathVariable` 注解的参数，
`name` 的值必须是小写字母和 `-`，`version` 的值必须是 `x.x.x` 的格式，`ext` 的值必须是 `.` 开头的小写字母。

# 使用不同的注解获取参数
## `@RequestParam`
对于一些方法，我们需要获取请求参数，而 `@RequestParam` 注解可以帮助我们从请求中获取参数 (也就是 `?`
后面的参数)。`@RequestParam` 注解可以接受以下参数：
* `value` 或 `name` 用于指定参数的名称。
* `required` 用于指定参数是否必须，默认为 `true`。
* `defaultValue` 用于指定参数的默认值。

例如一个方法的参数被 `@RequestParma("id")` 修饰，而请求中包含 `?id=3` 那么这个参数将会被填入 `3`。

NOTE: 通常对于一个没有任何注解标注的参数，其默认会通过 `@RequestParam` 注解来获取参数。

NOTE: 当该注解用于一个 `Map` 类型的参数时，`Spring` 会将所有的请求参数都填入这个 `Map` 中。

## `@RequestHeader`
该注解用于获取请求头中的参数，`@RequestHeader` 注解可以接受以下参数：
* `value` 或 `name` 用于指定参数的名称。
* `required` 用于指定参数是否必须，默认为 `true`。
* `defaultValue` 用于指定参数的默认值。

NOTE: 当该注解用于一个 `Map` 类型的参数时，`Spring` 会将所有的请求参数都填入这个 `Map` 中。

## `@CookieValue`
其实该注解也是用于获取请求头中的参数，只不过该注解往往标识获取 `coockie` 信息能够增加码的可读性。其
使用方法与 `@RequestHeader` 类似。

## `@PathVariable`
该注解用于获取 `URL` 中的参数，`@PathVariable` 注解可以接受以下参数：
* `value` 用于指定参数的名称。
* `required` 用于指定参数是否必须，默认为 `true`。
* `defaultValue` 用于指定参数的默认值。

例如一个方法被 `@GetMapping("/test/{id}")`  修饰，其某个参数被 `@PathVariable("id")` 修饰，而请求中
为 `/test/3` 那么被修饰的参数将会填上 `3`。

NOTE: 当该注解用于一个 `Map` 类型的参数时，`Spring` 会将所有的请求参数都填入这个 `Map` 中。

## `@RequestPart`
该注解用于获取请求中的 `Multipart` 参数，`@RequestPart` 注解可以接受以下参数：
* `value` 用于指定参数的名称。
* `required` 用于指定参数是否必须，默认为 `true`。

该注解一般用在 `MultipartFile` 类型的参数上，用于获取上传的文件。

## `@RequestBody`
使用该注解可以获取请求体中的参数，`@RequestBody` 注解可以接受以下参数：
* `required` 用于指定参数是否必须，默认为 `true`。

该注解一般用于 `POST` 方法获取请求体中的参数。需要注意的是，`@RequestBody` 注解只能用于一个参数上，
其作用是将整个请求体封装到这个参数中。一般而言，对于 `JSON` 格式的请求体，`Spring` 会自动将其转换为
`Java` 对象，当然这得要求请求体中的键值对与 `Java` 对象的属性名一致。在两者不一致的时候，可以在 `Java`
对象的属性上使用 `@JsonProperty` 注解来指定键值对的键名。也可以使用 `@JsonAlias` 注解来指定多个键名，
但是这个注解必须要依赖于 `setter` 和 `getter`。

# `Controller` 返回数据
## `@ResponseBody`
该注解用于标记一个方法的返回值会放置到 `HTTP` 响应体中，`Spring` 会根据请求头中的 `Accept` 内容自动
选择合适的 `HttpMessageConverter` 转换返回值。当声明了 `produces="application/json"` 时，`Spring`
会自动选择 `MappingJackson2HttpMessageConverter` 来转换返回值。

## `@RestController`
该注解是 `@Controller` 和 `@ResponseBody` 的组合。标明该类的所有方法都被 `@ResponseBody` 标注。
