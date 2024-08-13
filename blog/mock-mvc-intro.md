# `MockMvc` 简介
`MockMvc` 是 `Spring MVC` 框架提供的一个用于测试控制器的工具类，它可以模拟发送 `HTTP` 请求并接收 `HTTP` 响应，
从而可以方便地测试控制器的功能。`MockMvc` 的使用非常简单，只需要通过 `MockMvcBuilders` 类的 `standaloneSetup`
方法创建一个 `MockMvc` 对象，然后使用 `MockMvc` 对象的 `perform` 方法发送 `HTTP` 请求，即可得到 `HTTP` 响应。
`MockMvc` 对象还提供了一系列的方法，可以用来验证 `HTTP` 响应的状态码、响应头、响应体等信息。

# `MockMvc` 的使用
前面提到了可以使用 `MockMvcBuilders` 类的 `standaloneSetup` 方法创建一个 `MockMvc` 对象，但是如果我们
在一个 `Spring Boot` 项目中，可以直接使用 `@AutoConfigureMockMvc` 注解注入一个 `MockMvc` 对象，这样就
不需要手动创建 `MockMvc` 对象了。

接下来简单介绍一下 `MockMvc` 的基本步骤：
1. 对要测试的 `Controller` 创建一个对应的测试类；通常命名为原来的控制器类名后面加上 `Test`，
如 `UserController` 的测试类命名为 `UserControllerTest`；
2. 为这个类添加 `@SpringBootTest` 注解，表明这是一个 `Spring Boot` 测试类；
3. 为这个类添加 `@AutoConfigureMockMvc` 注解，自动向 `IoC` 容器中注入一个 `MockMvc` 对象；
4. 在测试类中定义一个 `MockMvc` 对象，并使用 `@Autowired` 注解注入；
5. 添加测试方法，测试方法使用 `@Test` 进行标注；

经过上述的步骤，我可以得到一个类似下面的代码：

```java
@SpringBootTest
@AutoConfigureMockMvc
public class UserControllerTest {

    @Autowired private MockMvc mockMvc;

    @Test
    public void testGetUser() throws Exception {
    }
}
```

接下来需要编写测试方法的逻辑，一般情况下我们只需要按照以下的顺序编写即可：
1. 发送请求；
2. 验证响应状态码；
3. 验证响应头；
4. 验证响应体。

下面我们以此介绍这几个步骤。对于发送请求，我们主要使用 `MockMvc` 对象的 `perform` 方法，该方法接收一个
`RequestBuilder` 对象，`RequestBuilder` 对象可以通过 `MockMvcRequestBuilders` 类的静态方法创建，
如 `get`、`post`、`put`、`delete` 等方法。`RequestBuilder` 对象还提供了一系列的方法，可以设置请求的
路径、请求参数、请求头等信息。下面的代码展示了如何模拟一个带有 `token` 的 `GET` 请求：

```java
@Test
public void testGetUser() throws Exception {
    mockMvc.perform(get("/user/{id}, 1").header("token", "123456"));
}
```

`mockMvc.perform` 会返回一个 `ResultActions` 对象，该对象提供以下的几种方法：
* `andDo(ResultHandler)`
* `andExpect(ResultMatcher)`
* `andExpectAll(ResultMatcher...)`
* `andReturn()`

上面的几个方法均会再次返回一个 `ResultActions` 对象，这意味着我们可以进行链式调用。

接下来我们只需要搞清楚 `ResultHandler` 和 `ResultMatcher` 的用法即可。`ResultHandler` 用于处理
`MockMvc` 的结果，`ResultMatcher` 用于验证 `MockMvc` 的结果。`ResultHandler` 和 `ResultMatcher`
均是函数式接口，可以使用 `lambda` 表达式来实现。

对于 `RequestHandler` 接口，我们只需要实现一个 `handle` 方法，该方法接收一个 `MvcResult` 对象，表明
我们希望对结果做什么操作，常见的时候我们会使用 `print` 方法打印结果：

```java
@Test
public void testGetUser() throws Exception {
    mockMvc.perform(get("/user/{id}, 1").header("token", "123456"))
            .andDo(print());
}
```

上面的 `print()` 是 `MockMvcResultHandlers` 类的静态方法，用于打印结果。`MockMvcResultHandlers` 类
还提供了其他的方法，如 `log()`、`logIfError()`、`logDebug()` 等。

同样的 `MockMvcResultMatches` 中也提供了许多静态方法进行结果的验证，如 `status()`、`content()`、
`jsonPath()` 等。下面的代码展示了如何验证响应的状态码：

```java
@Test
public void testGetUser() throws Exception {
    mockMvc.perform(get("/user/{id}, 1").header("token", "123456"))
            .andDo(print())
            .andExpect(status().isOk());
}
```

上面的代码中，`status().isOk()` 方法用于验证响应的状态码是否为 `200`。`status()` 方法还提供了其他的
方法，如 `isBadRequest()`、`isNotFound()`、`isInternalServerError()` 等。

接下来我们可以验证响应头，`MockMvcResultMatchers` 类中提供了 `header()` 方法，用于验证响应头的信息。
下面的代码展示了如何验证响应头：

```java
@Test
public void testGetUser() throws Exception {
    mockMvc.perform(get("/user/{id}, 1").header("token", "123456"))
            .andDo(print())
            .andExpect(status().isOk())
            .andExpect(header().string("Content-Type", "application/json"));
}
```

上面的代码中，`header().string("Content-Type", "application/json")` 方法用于验证响应头中 `Content-Type`
的值是否为 `application/json`。

最后我们可以验证响应体，`MockMvcResultMatchers` 类中提供了 `content()` 方法，用于验证响应体的信息。
下面的代码展示了如何验证响应体：

```java
@Test
public void testGetUser() throws Exception {
    mockMvc.perform(get("/user/{id}", 1).header("token", "123456"))
            .andDo(print())
            .andExpect(status().isOk())
            .andExpect(header().string("Content-Type", "application/json"))
            .andExpect(content().json("{\"id\":1,\"name\":\"kaiser\"}"));
}
```

上面的代码中，`content().json("{\"id\":1,\"name\":\"kaiser\"}")` 方法用于验证响应体是否为 `{"id":1,"name":"kaiser"}`。

上面的代码过于冗长，我们可以使用 `andExpectAll` 方法将所有的验证合并到一起，如下所示：

```java
@Test
public void testGetUser() throws Exception {
    mockMvc.perform(get("/user/{id}, 1").header("token", "123456"))
            .andDo(print())
            .andExpectAll(
                    status().isOk(),
                    header().string("Content-Type", "application/json"),
                    content().json("{\"id\":1,\"name\":\"kaiser\"}")
            );
}
```

# 文件上传
文件上传是指客户端向服务器发送文件，通常使用 `POST` 请求，请求头中的 `Content-Type` 为 `multipart/form-data`。
`MockMvc` 提供了 `file` 方法用于模拟文件上传，下面的代码展示了如何模拟文件上传：

```java
@Test
public void testUploadFile() throws Exception {
    mockMvc.perform(multipart("/file/upload")
            .file(new MockMultipartFile("file", "test.txt", "text/plain", "hello".getBytes("UTF-8"))))
            .andDo(print())
            .andExpect(status().isOk())
            .andExpect(content().string("success"));
}

// 也可以使用以下的方式：
@Test
public void testUploadFile() throws Exception {
    mockMvc.perform(multipart("/file/upload")
            .file("file", "hello".getBytes("UTF-8")))
            .andDo(print())
            .andExpect(status().isOk())
            .andExpect(content().string("success"));
}
```

上面的代码中，`multipart("/file/upload")` 方法用于模拟一个 `POST` 请求，请求路径为 `/file/upload`，
`file` 方法用于模拟文件上传，`new MockMultipartFile("file", "test.txt", "text/plain", "hello".getBytes())`
用于模拟一个名为 `test.txt`，文件类型为 `text/plain`，文件内容为 `hello`。

上面的第二种方式中，`file("file", "hello".getBytes())` 方法用于模拟一个名为 `file`，文件内容为 `hello`。
