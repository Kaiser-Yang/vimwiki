对于最原始的 `Spring` 而言，`Spring` 往往指的是能够对对象进行管理的框架，`IoC` 和 `AOP` 是其两大核心思想。
而在最初的 `Spring` 中，多数是使用 `xml` 文件进行配置，不过在目前看来使用 `xml` 进行配置的方式已经不再流行，
而是使用 `Java` 配置的方式 (即通过 `Java` 注解的方式进行配置)，这样也更好的与 `Spring-Boot` 相互配合。
因此本文只是介绍一些常用的 `Spring` 注解，以及 `Spring` 的一些基本概念。

# 什么是 `Bean`
`Bean` 可以理解为 `Spring` 容器中的对象，`Spring` 容器负责创建 `Bean`，并将 `Bean` 管理起来。

再简单一点，一个 `Bean` 就是一个 `Java` 中的对象。

## `Bean` 与 `IoC`
在不使用 `Spring` 进行开发的环境中，如果需要创建一个对象，通常是通过 `new` 关键字来创建对象。这样的话，
对象的创建和对象的销毁都是由程序员来控制的。

而 `IoC` 则指的是控制反转，即对象的创建和对象的销毁不再由程序员来控制。最简单的 `IoC` 的例子是使用
`setter` 进行依赖注入。

NOTE: 在 `Spring` 中将一个类的成员成为其依赖，所谓的依赖注入指的就是为每个成员变量设置对应的值。

通过使用 `setter` 的方式进行依赖注入的好处是可以根据用户的配置文件中的不同配置决定注入的具体对象，而如果是
使用 `new` 进行对象的创建，那么对象的创建就是固定的了。

而 `Spring` 所做的一切通过读取这个配置文件来决定创建哪个对象，以及创建对象的时候需要注入哪些对象，这样就
实现了对象的创建和对象的销毁不再由程序员来控制，也就完成了 `IoC`。

## 使用注解添加 `Bean`
### `@Configuration`
`@Configuration` 注解用于指定当前类是一个配置类，相当于一个 `Spring` 配置的 `xml` 文件。在配置类中可以
使用 `@Bean` 注解向 `Spring` 容器中注册 `Bean`。

### `@Bean`
`@Bean` 注解用于指定一个方法是一个 `Bean` 的生产者，返回值是一个 `Bean`。`@Bean` 注解可以指定 `Bean`
的名称，如果不指定 `Bean` 的名称，那么 `Bean` 的名称默认为方法名。

### `@Component`
`@Component` 注解用于指定当前类是一个 `Bean`，`Spring` 会自动扫描该类，并将该类注册到 `Spring` 容器中。
`@Component` 注解可以指定 `Bean` 的名称，如果不指定 `Bean` 的名称，那么 `Bean` 的名称默认为类名，首字母
小写。

### `@Controller`
该注解与 `@Component` 类似，只是表明一个控制器(`controller`)的时候，一般使用该注解。

### `@Service`
该注解与 `@Component` 类似，只是表明一个服务(`service`)的时候，一般使用该注解。

### `@Repository`
该注解与 `@Component` 类似，只是表明一个仓库(`dao`)的时候，一般使用该注解。

### `@ComponentScan`
该注解用于指定哪些位置的类会被 `Spring` 扫描，例如 `@ComponentScan("com.example")` 表示扫描 `com.example`
通常情况下，我们并不需要使用该注解，因为 `Spring Boot` 会自动扫描 `@SpringBootApplication` 注解所在类的
包及其子包。实际上是因为 `@SpringBootApplication` 包含了 `@ComponentScan` 注解。而只有需要托管不在
`@SpringBootApplication` 所在包及其子包的类时才需要使用 `@ComponentScan`。

### `@Import`
`@Import` 注解用于导入其他配置类，例如：`@Import(XXXConfig.class)` 表示导入 `XXXConfig` 配置类。但是，
在 `Spring Boot` 中，我们很少使用到这个注解，因为 `Spring Boot` 会自动扫描 `@SpringBootApplication`
所在类的包及其子包，因此我们只需要将配置类放置在 `@SpringBootApplication` 所在包及其子包即可。

## `Bean` 作用域介绍
`Spring` 的 `Bean` 作用域有以下几种:

| 作用域        | 描述 |
| ---           | --- |
| `singleton`   | `Spring` 的默认值，一个 `IoC` 容器只有一个 `Bean` 的实例，为所有对象提供共享的 `Bean` 实例 |
| `prototype`   | 每次请求都会创建一个新的 `Bean` 实例 |
| `request`     | 每次 `HTTP` 请求中只会创建一个新的 `Bean` 实例 |
| `session`     | 每次 `HTTP` 会话中只会创建一个新的 `Bean` 实例 |
| `application` | 每个 `ServletContext` 中只会创建一个 `Bean` 实例 |
| `websockt`    | 每个 `WebSocket` 中只会创建一个 `Bean` 实例 |

这几个作用域对应的注解如下:
* `@Scope("singleton")`
* `@Scope("prototype")`
* `@RequestScope`
* `@SessionScope`
* `@ApplicationScope`
* `@Scope(scopeName = "websocket", proxyMode = ScopedProxyMode.TARGET_CLASS)`

TODO：增加解释说明为什么 `mvc` 应用的注解不同。

# 自动装配
自动装配指的是根据 `IoC` 容器中已经有的 `Bean` 进行为某些依赖自动注入的过程。`Spring` 提供了以下几种
自动装配的方式:

| 模式          | 描述 |
| ---           | --- |
| `no`          | 默认值，不进行自动装配，需要手动指定依赖 |
| `byName`      | 根据 `Bean` 的名称进行自动装配，需要包含指定名字的 `setter`，且 `Bean` 唯一匹配 |
| `byType`      | 根据 `Bean` 的类型进行自动装配，需要 `Bean` 唯一匹配 |
| `constructor` | 根据构造函数进行自动装配，需要构造函数对应的所有参数类型在容器中均有唯一的 `Bean` |

## 自动装配
### `@Autowired`
`@Autowired` 注解可以用于字段、构造函数、方法上，用于自动装配 `Bean`。`@Autowired` 注解可以用于字段、
构造函数、方法上，用于自动装配 `Bean`。

如果直接使用 `@Autowired` 注解，那么 `Spring` 会根据 `byType` 的方式进行自动装配，如果有多个 `Bean`
类型相同，那么会抛出异常。当然，如果没有一个 `Bean` 类型相同，那么也会抛出异常。

值得注意的一点是：如果 `@Autowired` 用于数组或者集合类型的字段，那么 `Spring` 会将所有匹配的 `Bean`
进行注入。如果放置在 `Map` 类型的字段上，那么 `Spring` 会将所有匹配的 `Bean` 进行注入，`Bean` 的名字
作为 `key`，`Bean` 作为 `value`。

如果想要在没有找到同类型的 `Bean` 的时候不抛出异常，可以使用 `@Autowired(required = false)`。这样的话，
如果没有找到同类型的 `Bean`，那么 `Spring` 不会进行任何操作，这意味着如果注解在方法上，那么这个方法
不会被调用，如果注解在字段上，那么这个字段的值为默认值。

### `@Primary`
如果想要在有多个类型相同的 `Bean` 的时候不抛出异常，可以使用 `@Primary` 注解，这样的话 `Spring` 会优先
选择被 `@Primary` 注解的 `Bean` 进行注入 (注意这个注解是放在被依赖的类上)。

### `@Qualifier`
如果想要获得更加精确的控制，可以使用 `@Qualifier` 注解，这样的话 `Spring` 会根据 `@Qualifier` 注解的
值进行注入，例如：如果 `@Qualifier("userDao")` 和 `@Autowired` 同时使用，那么 `Spring` 会注入名字为
`userDao` 的 `Bean`。不过在这样使用的时候需要注意的是：`@Autowired` 依然只会在类型匹配的时候进行注入。

`@Qualifier` 使用的时候也可不指定值，这样的话，作用就与 `@Primary` 一样，不过 `@Qualifier` 的优先级更高。

### `@Resource`
如果想要完全通过 `Bean` 的名字进行注入，可以使用 `JavaEE` 的 `@Resource` 注解，这样的话 `Spring` 会
根据 `@Resource` 注解的值进行注入，不过这个注解只能用于字段和 `setter` 方法上。当没有显式指定名称时，
如果该注解放置在字段上，那么字段的名称将作为 `Bean` 的名称，如果该注解放置在 `setter` 方法上，那么
`setter` 方法的名称 (去掉 `set` 后首字母小写) 将作为 `Bean` 的名称。

### `@Value`
`@Value` 注解可以用于字段、构造函数、方法上，用于注入 `Bean` 的值。`@Value` 注解有两种使用形式：
* `@Value("#{}")`
* `@Value("${}")`

`@Value("#{}")` 用于注入 `Bean` 的值，`#` 用于引用 `Spring` 的 `Bean`，`{}` 用于引用 `Bean` 的属性。
例如：`@Value("#userDao.name")` 会注入 `userDao` 的 `name` 属性，这里的 `userDao` 是一个 `Bean`。而
`@Value("#userDao.getName()")` 会注入 `userDao` 的 `getName()` 方法的返回值。这部分的更多内容可以查看
`SpEL`。

`@Value("${}")` 用于引用配置文件中的属性，例如在 `application.properties` 文件中有 `name=Tom`，那么
`@Value("${name}")` 就会注入 `Tom`。也可以用 `@Value("${name:default}")` 为找不到 `name` 时指定默认值。

使用 `@Value` 的时候需要写好配置文件，下面简单介绍一下如何读取配置文件。对于一个 `Spring Boot` 的项目，
在完成初始化后，在 `src/main/resources` 目录下会存在一个 `application.properties` 文件，这个文件就是
`Spring Boot` 的配置文件，可以在这个文件中写入一些配置信息，然后通过 `@Value` 注解进行读取。如果没有
这个文件自行创建就可以了，`Spring` 会自动加载以下路径下的 `application.properties` 或者 `application.yml`：
1. `file:./config`
2. `file:./`
3. `classpath:/config/`
4. `classpath:/`

上述路径中，`file:./config` 表示当前项目根目录下的 `config` 目录，`file:./` 表示当前项目根目录，
`classpath:/config/` 表示 `resources` 目录下的 `config` 目录，`classpath:/` 表示 `resources` 目录。
优先级从高到低，也就是说 `file:./config` 的优先级最高，`classpath:/` 的优先级最低。

当两个文件同时存在的时候，`application.properties` 会被优先加载。

通常情况下，不会将所有的配置都放置在 `application.properties` 文件中，而是将不同的配置放置在不同的配置文件中。
下面来介绍如何加载一个这样的配置文件。一般在 `src/main/resources/config` 中放入配置文件，例如
`XXXConfig.properties`，然后在启动类同级目录会创建一个 `config/XXXConfig.java` 的类去读取配置文件：

```java
@Configuration
@PropertySource("classpath:config/XXXConfig.properties")
public class XXXConfig {
}
```

通过上述的操作后，可以在任何地方使用 `@Value` 读取到配置文件中的值，新版的 `Spring` (我测试了 `Spring 6`)
现在支持自动解析 `yml` 文件，不再需要指定 `@PropertySource` 中的 `factory`。

#  其他注解
## `@Description`
该注解可以为 `Bean` 添加描述信息，这样的话可以通过 `Spring` 的 `BeanFactory` 获取到这个描述信息。

## `@Profile`
该注解用于指定 `Bean` 的环境，例如：`@Profile("dev")` 表示这个 `Bean` 只在 `dev` 环境下生效。

## `@Nullable`
该注解用于标记一个字段可以为 `null`，这样的话 `Spring` 在注入的时候如果没有找到对应的 `Bean`，那么
不会抛出异常。

## `@NonNull`
该注解用于标记一个字段不可以为 `null`，这样的话 `Spring` 在注入的时候如果没有找到对应的 `Bean`，那么
会抛出异常。

## `@NonNullApi`
该注解用于标记一个包下的所有类的字段不可以为 `null`，这样的话 `Spring` 在注入的时候如果没有找到对应的
`Bean`，那么会抛出异常。该注解一般写在 `package` 语句上方。

## `@NonNullFields`
该注解用于标记一个类的所有字段不可以为 `null`，这样的话 `Spring` 在注入的时候如果没有找到对应的 `Bean`，
那么会抛出异常。该注解一般写在 `package` 语句上方。

<!-- # `AOP` -->
<!---->
<!---->
<!---->
<!---->
<!---->
<!---->
<!---->
<!---->
<!---->
<!---->
<!---->
<!---->
<!---->
<!---->
<!---->
<!---->
<!---->
<!---->
<!---->
<!-- `@PostConstruct` 和 `@PreDestroy` -->
<!-- 众所周知，`Spring` 中 `Bean` 的生命周期可以分成以下的几个阶段： -->
<!-- 1. `Bean` 的实例化 -->
<!-- 2. `Bean` 的属性注入 -->
<!-- 3. `Bean` 的初始化 -->
<!-- 4. `Bean` 的销毁 -->
<!---->
<!-- `@PostConstruct` 和 `@PreDestroy` 注解分别用于在 `Bean` 的初始化和销毁阶段进行操作。被 `@PostConstruct` -->
<!-- 注释的方法将在依赖注入完成后调用，而被 `@PreDestroy` 注释的方法将在 `Bean` 销毁之前调用。 -->
<!---->
<!-- 在 `@Bean` 中也可以指定 `initMethod` 和 `destroyMethod` 这两者与 `@PostConstruct` 和 `@PreDestroy` -->
<!-- 的作用是一样的。 -->
<!---->
<!---->
<!-- `BeanFactoryPostProcessor postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory)` -->
<!-- `InstantiationAwareBeanPostProcessor postProcessBeforeInstantiation(Class<?> beanClass, String beanName)` -->
<!-- `InstantiationAwareBeanPostProcessor postProcessAfterInstantiation(Object bean, String beanName)` -->
<!-- `BeanPostProcessor postProcessBeforeInitialization(Object bean, String beanName)` -->
<!-- `InitializingBean afterPropertiesSet()` -->
<!-- `InitializingBean initMethod()` -->
<!-- `BeanPostProcessor postProcessAfterInitialization(Object bean, String beanName)` -->
<!---->
