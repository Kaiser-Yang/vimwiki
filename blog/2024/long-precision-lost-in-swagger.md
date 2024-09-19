# 起因
某次通过 `Swagger` 在项目中进行测试的时候，发现响应体中返回的 `id` (`Long` 类型，由雪花算法生成) 与
数据库中存储的 `id` 始终差那么一点点。

# 排查
起先我认为是后端传得数据本身就有问题，于是我在 `Controller` 中进行返回数据的时候，将 `id` 的值
打印出来，发现 `id` 的值是正确的。于是我又在终端中单独执行了 `curl` 命令去获取数据，发现 `id` 的
值也是正确的。这时候我就很疑惑了，为什么在 `Swagger` 中获取的数据会有问题呢？

接着我猜测是出现了精度丢失的问题，我猜想 `Long` 类型可能不能完全在 `js` 中表示，于是 `Swagger` 在
对返回值进行解析的时候，不得不舍弃一部分。于是我通过关键字 `Long 精度丢失` 便找到了相关的内容：
> `JavaScript` 的 `Number` 类型是基于 `IEEE 754` 标准的双精度浮点数格式，只能安全地表示 `53` 位
二进制数字，也就是 `Number.MAX_SAFE_INTEGER` 的值 `9007199254740991`。

# 解决方法
网上的解决方法有很多，例如可以通过在类中 `Long` 字段上添加
`@JsonsSerialize(using = ToStringSerializer.class)` 注解，将 `Long` 类型转换为 `String` 类型。

我并没有采取这样的解决方法，主要是考虑到还需要从请求体中接收参数，为了方便前端发送，我决定直接将
在从前端接收的 `DTO` 对象和发送给前端的 `VO` 对象中的 `id` 字段更改成 `String` 类型，然后在从 `DTO`
类型构造 `PO` 对象的时候，将 `String` 类型的 `id` 字段转换为 `Long` 类型，在从 `PO` 对象构造 `VO`
对象的时候，将 `Long` 类型的 `id` 字段转换为 `String` 类型。这样的好处在于，对于前端而言，`id` 确确
实实是一个 `String` 类型的字段，前端并不会知道 `id` 实际上是以 `Long` 类型进行存储的。

```java
// 通过 UserPO 构造 UserVO
public UserVO(UserPO userPO) {
    this(userPO.getId().toString());
}

// 通过 UserDTO 构造 UserPO
public UserPO(UserDTO userDTO) {
    try {
        this.id = Long.valueOf(userDTO.id());
    } catch (NumberFormatException e) {
        this.id = null;
    }
}
```

# 巨人的肩膀
* [长得太长也是错？——后端 Long 型 ID 精度丢失的“奇妙”修复之旅](https://cloud.tencent.com/developer/article/2445079)
