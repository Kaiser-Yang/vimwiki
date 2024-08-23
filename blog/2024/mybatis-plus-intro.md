# `MyBatis` 与 `MyBatis-Plus` 的区别
`MyBatis` 的主要功能是提供了对 `SQL` 的封装，可以通过 `XML` 或者注解的方式来配置 `SQL`，并且提供了对
`SQL` 的执行、结果映射等功能。`MyBatis` 对于所有的 `SQL` 都需要自己来编写。

`MyBatis-Plus` 可以理解成是 `MyBatis` 的增强工具，提供了很多实用的功能，比如分页、条件构造器、代码生成器等。
其中最重要的功能是提供了通用的 `CRUD` 方法，可以直接通过调用方法来实现 `CRUD` 操作，不需要自己编写 `SQL`。

除此之外，我在查阅文档的时候发现 `MyBatis-Spring` 部分的文档比较简陋，而 `MyBatis-Plus` 拥有更加详细的文档。

# 使用 `MyBatis-Plus` 实现 `CRUD`
## `@MapperScan`
与 `MyBatis` 相同的时，我们需要在启动类上添加 `@MapperScan` 注解，指定 `Mapper` 接口的包路径。例如
可以通过 `@MapperScan("edu.cmipt.gcs.dao")` 指定 `Mapper` 接口的包路径为 `edu.cmipt.gcs.dao`。这样
指定后 `MyBatis-Plus` 会自动将该路径下的 `Mapper` 接口注册到 `Spring` 容器中。

## `PO` 对象
`PO` 对象是持久化对象，对应数据库中的表。通常情况下，我们会为每个表创建一个对应的 `PO` 对象。`PO` 对象
通常包含表中的字段，以及对应的 `getter` 和 `setter` 方法。

例如一张 `user` 表的结构如下：

| 字段名  | 类型 |
| ---     | --- |
| `id`    | `BIGINT` |
| `name`  | `VARCHAR` |
| `age`   | `INT` |
| `email` | `VARCHAR` |

我们可以创建如下的 `UserPO` 类：

```java
@Data
public class UserPO {
    private Long id,
    private String name,
    private Integer age,
    private String email
}
```

### `@TableName`
上述的实例在 `MyBatis-Plus` 中对应的表名为 `user_p_o`，这是因为 `MyBatis-Plus` 默认会将驼峰命名的
字段转换为下划线分割的表名。如果我们希望使用自定义的表名，可以在 `PO` 类上添加 `@TableName` 注解，指定
表名。也就是说我们需要在类的头部添加 `@TableName("user")` 注解，指定表名为 `user`。

### `@TableID`
如果上面的例子中的 `id` 字段是主键，我们可以在 `id` 字段上添加 `@TableID` 注解，指定主键的类型。这样
指定后才能使用 `MyBatis-Plus` 提供的一些与 `ID` 相关的方法。如果表中和 `PO` 类中的主键字段都叫 `id`，
则可以不用使用该注解。

## `BaseMapper` 接口
通常情况下，我们会为每个 `PO` 对象创建一个对应的 `Mapper` 接口，继承 `BaseMapper` 接口。`BaseMapper`
接口提供了一些通用的 `CRUD` 方法，我们可以直接调用这些方法来实现 `CRUD` 操作。

例如我们可以通过以下方式创建一个 `UserMapper` 接口：

```java
public interface UserMapper extends BaseMapper<UserPO> {}
```

### `insert`
```java
// T 在这里代表继承时指定的 PO 类型，返回值代表受影响的行数
int insert(T entity);
```

我们可以配合自动装配轻松地使用 `insert` 方法，例如：

```java
@Autowired
private UserMapper userMapper;

public void insertUser(UserPO user) {
    userMapper.insert(user);
}
```

注意：上面的接口返回值代表受影响的行数，通常情况下插入成功时返回 `1`，而插入失败时返回 `0` 。

### `delete`
```java
// 根据 entity 条件，删除记录
int delete(@Param(Constants.WRAPPER) Wrapper<T> wrapper);
// 删除（根据ID 批量删除）
int deleteBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);
// 根据 ID 删除
int deleteById(Serializable id);
// 根据 columnMap 条件，删除记录
int deleteByMap(@Param(Constants.COLUMN_MAP) Map<String, Object> columnMap);
```

上面的后三种方式的使用方法应该是一目了然的，这里简单介绍一下第一种方式。`Wrapper` 是一个条件构造器，
我们可以通过 `QueryWrapper` 或者 `UpdateWrapper` 来构造条件。例如我们可以通过以下方式删除年龄大于 `18`
的用户：

```java
public void deleteUser() {
    Wrapper<UserPO> queryWrapper = new QueryWrapper<>();
    queryWrapper.gt("age", 18);
    userMapper.delete(queryWrapper);
}
```

而对于其他的方法，我们完全可以在编写的时候通过 `IDE` 的自动补全来查看使用方法。

### `update`
```java
// 根据 whereWrapper 条件，更新记录
int update(@Param(Constants.ENTITY) T updateEntity, @Param(Constants.WRAPPER) Wrapper<T> whereWrapper);
// 根据 ID 修改
int updateById(@Param(Constants.ENTITY) T entity);
```

例如，我们可以通过以下的方式修改 `age` 字段为 `20` 的用户的邮箱：

```java
public void updateUser() {
    UserPO user = new UserPO(null, null, 20, "123456@xxx.xxx");
    Wrapper<UserPO> updateWrapper = new UpdateWrapper<>();
    queryWrapper.eq("age", 20);
    userMapper.update(user, updateWrapper);
}
```

上面出现了 `UpdateWrapper` 和 `QueryWrapper` 两个类，这两个类的功能非常类似，但是一般情况下我们
会使用 `QueryWrapper` 来构造查询条件，使用 `UpdateWrapper` 来构造更新条件。`UpdateWrapper` 提供了
`set` 方法，可以设置需要更新的字段，而不需要传入一个完整的 `PO` 对象。例如上面的例子我们可以修改成：

```java
public void updateUser() {
    Wrapper<UserPO> updateWrapper = new UpdateWrapper<>();
    queryWrapper.eq("age", 20).set("email", "123456@xxx.xxx");
    userMapper.update(updateWrapper);
}
```

### `select`
```java
// 根据 ID 查询
T selectById(Serializable id);
// 根据 entity 条件，查询一条记录
T selectOne(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 查询（根据ID 批量查询）
List<T> selectBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);
// 根据 entity 条件，查询全部记录
List<T> selectList(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
// 查询（根据 columnMap 条件）
List<T> selectByMap(@Param(Constants.COLUMN_MAP) Map<String, Object> columnMap);
// 根据 Wrapper 条件，查询全部记录
List<Map<String, Object>> selectMaps(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
// 根据 Wrapper 条件，查询全部记录。注意： 只返回第一个字段的值
List<Object> selectObjs(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
// 根据 entity 条件，查询全部记录（并分页）
IPage<T> selectPage(IPage<T> page, @Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
// 根据 Wrapper 条件，查询全部记录（并分页）
IPage<Map<String, Object>> selectMapsPage(IPage<T> page, @Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
// 根据 Wrapper 条件，查询总记录数
Integer selectCount(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
```

下面详细介绍以下分页查询的使用方法：

```java
public void selectUser() {
    // 每页 10 条记录，查询第 1 页
    Page<UserPO> page = new Page<>(1, 10);
    Wrapper<UserPO> queryWrapper = new QueryWrapper<>();
    queryWrapper.gt("age", 18);
    IPage<UserPO> userPage = userMapper.selectPage(page, queryWrapper);
    List<UserPO> userList = userPage.getRecords();
    for (UserPO user : userList) {
        System.out.println(user);
    }
}
```

## `IService` 接口
通常情况下，我们会为每个 `Mapper` 接口创建一个对应的 `Service` 接口，继承 `IService` 接口。`IService`
接口提供了一些通用的 `CRUD` 方法，我们可以直接调用这些方法来实现 `CRUD` 操作。与直接调用 `Mapper` 接口
相比，使用 `Service` 接口可以更好地实现业务逻辑与数据访问的分离。

`IService` 的适用方式与 `BaseMapper` 类似，例如：

```java
public interface UserService extends IService<UserPO> {}
```

完成上述操作后，我们便可以使用 `UserService` 接口中提供的各种方法了。使用的方式与直接调用 `Mapper`
接口类似，这里不再赘述。

# 逻辑删除
逻辑删除指的是通过一个字段来标识资源是否已经被删除，而不是真的从数据库中删除资源。逻辑删除的好处是可以
保留删除的记录，方便日后的数据分析。而逻辑删除的原理就是在 `CRUD` 操作时，通过增加一个条件来判断资源
是否已经被删除。例如使用逻辑删除后的查询语句均需要增加 `where deleted = 0` 的条件 (如果
`deleted = 0` 表示未被删除，`deleted = 1` 表示逻辑删除)。而有些数据库的设计者通过设置删除时间来实现
逻辑删除，例如 `deleted_time` 字段，如果 `deleted_time` 不为 `null` 则表示已经被删除。

`MyBatis-Plus` 对逻辑删除提供了支持，可以在 `application.yml` 配置全局的逻辑删除：

```yml
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: deleted # 全局逻辑删除字段名
      logic-delete-value: 1 # 逻辑已删除值
      logic-not-delete-value: 0 # 逻辑未删除值
```

可以在 `PO` 类中添加 `@TableLogic` 注解，指定逻辑删除字段：

```java
@Data
public class UserPO {
    private Long id,
    private String name,
    private Integer age,
    private String email,
    @TableLogic private Integer deleted
}
```

注意：即使配置了全局的逻辑删除字段，也 **必须** 在 `PO` 类中添加 `@TableLogic` 注解，指定逻辑删除字段。

如果逻辑删除键使用的是类型是 `datetime` 类型，则可以使用如下的配置：

```yml
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: gmt_deleted # 全局逻辑删除字段名
      logic-delete-value: now() # 逻辑已删除值
      logic-not-delete-value: "null" # 逻辑未删除值 此处不能直接使用 null 而应该传入字符串的 "null"
```

# 扩展 `Service`
在实际的开发中，我们可能需要为 `Service` 接口添加一些自定义的方法。这里简单介绍如何进行扩展。假设我们
已经通过继承 `IService` 接口创建了一个 `UserService` 接口，我们可以通过以下方式为 `UserService` 接口
添加自定义的方法。

首先，我们需要在 `UserService` 中定义自定义的方法：

```java
public interface UserService extends IService<UserPO> {
    void customMethod();
}
```

接着，我们需要为 `UserService` 创建一个实现类，实现自定义的方法：

```java
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, UserPO> implements UserService {
    @Override
    public void customMethod() {
        // 自定义的方法实现
    }
}
```

在实现类中，我们需要继承 `ServiceImpl` 类，这个类的泛型参数分别是 `Mapper` 接口和 `PO` 类，同时我们
还需要实现 `UserService` 接口中定义的方法。

通过上述的操作后，自动装配 `UserService` 接口时会自动装配 `UserServiceImpl` 类，这样我们就可以使用
`UserService` 接口中定义的方法了。
