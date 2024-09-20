# 起因
在开发一个 `Spring Boot` 项目的过程中，我编写了三个测试类：`AuthenticationControllerTest`,
`UserControllerTest` 以及 `RepositoryControllerTest`。我希望在我执行 `mvn test` 的时候能够控制这三个
测试类的执行顺序：
1. `AuthenticationControllerTest` 先执行以便获取后续操作的 `token`
2. 接着执行 `RepositoryControllerTest` 进行创建仓库的测试以便后续的查询的仓库列表不为空
3. 最后执行 `UserControllerTest` 进行用户相关的测试

我在网上查找了很多资料，发现大部分都是介绍的如何指定一个测试类中的测试方法的执行顺序，但是没有找到
如何指定多个测试类的执行顺序的资料。

这里简单介绍一下如何指定同一个类中的 `@Test` 修饰的方法的执行顺序：
* 在测试类上添加 `@TestMethodOrder(MethodOrderer.OrderAnnotation.class)` 并在测试方法上添加 `@Order`
注解可以实现按照 `@Order` 中的值从小到大的顺序执行测试方法。
* 在测试类上添加 `@TestMethodOrder(MethodOrderer.Random.class)` 可以实现随机执行测试方法。
* 在测试类上添加 `@TestMethodOrder(MethodOrderer.DisplayName.class)` 可以实现按照测试方法的名称的
字典序执行测试方法。

# 蓦然回首
找了很多网上的资料都没有找到解决方案，于是我决定去 `Junit5` 翻阅一下官方文档，我便在官方文档中找到了
这样一段话：
> To configure test class execution order globally for the entire test suite, use the
`junit.jupiter.testclass.order.default` configuration parameter to specify the fully qualified
class name of the ClassOrderer you would like to use. The supplied class must implement the
`ClassOrderer` interface.

随即在后文又提到了如何配置这个变量：
> For example, for the `@Order` annotation to be honored on test classes, you should configure the
`ClassOrderer.OrderAnnotation` class orderer using the configuration parameter with the
corresponding fully qualified class name (e.g., in `src/test/resources/junit-platform.properties`).

也就是说如果在 `src/test/resources/junit-platform.properties` 中添加如下内容：

```properties
junit.jupiter.testclass.order.default=org.junit.jupiter.api.ClassOrderer$OrderAnnotation
```

便可以通过在测试类上添加 `@Order` 的方式控制测试类的执行顺序。

这样一来这个问题也就解决了。但是我想每次都需要在测试类上添加 `@Order` 也是很麻烦的，于是我自定义了
一个 `SpringBootTestClassOrderer` 类，只需要按照顺序在 `classOrder` 中添加测试类即可以按照顺序执行：

```java
public class SpringBootTestClassOrderer implements ClassOrderer {

    private static final Class<?>[] classOrder = new Class[] {
        AuthenticationControllerTest.class,
        RepositoryControllerTest.class,
        UserControllerTest.class
    };

    @Override
    public void orderClasses(ClassOrdererContext classOrdererContext) {
        classOrdererContext.getClassDescriptors().sort(Comparator.comparingInt(SpringBootTestClassOrderer::getOrder));
    }

    private static int getOrder(ClassDescriptor classDescriptor) {
        for (int i = 0; i < classOrder.length; i++) {
            if (classDescriptor.getTestClass().equals(classOrder[i])) {
                return i;
            }
        }
        return Integer.MAX_VALUE;
    }
}
```

最后我的 `junit-platform.properties` 文件如下：

```properties
junit.jupiter.testclass.order.default=edu.cmipt.gcs.SpringBootTestClassOrderer
```

# 巨人的肩膀
* [Junit 5 Class Order](https://junit.org/junit5/docs/current/user-guide/#writing-tests-test-execution-order-classes)
