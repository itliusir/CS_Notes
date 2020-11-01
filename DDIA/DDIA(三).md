# 编码与演化

编码也被称为序列化，例如 java 中把内存中对象编码为字节的有 `java.io.Serializable` 。虽然各种语言自带的编码库很方便，但是也存在一些别的问题：

1. 与特定语言深度绑定，其他语言很难读取这种数据
2. 解码过程存在安全问题
3. 因为使用的简单，容易让研发人员忽略版本控制，直到后期遇到前向后向兼容性带来的问题
4. 效率不好

## 常见编码方式

- XML
- JSON
- 二进制(主要采用接口定义语言，IDL来描述模式)
  - Thrift(binary、compact、dense)
  - Protocol Buffers
  - Avro

## 二进制编码方式如何应对模式的演变

### Thrift IDL

```c
struct Person {
    1: required string       userName,
    2: optional i64          favoriteNumber,
    3: optional list<string> interests
}
```

### Protocol Buffers IDL

```c
message Person {
    required string user_name       = 1;
    optional int64  favorite_number = 2;
    repeated string interests       = 3;
}
```



可以看到 Thrift 和 Protocol Buffers 的每个字段都有**标签号码**标识(1,2,3)，然后使用数据类型注释。

这个标识至关重要，你可以改变字段名称但不能更改标记，如果要**新加字段**就分配一个**新的标签号码**。

1. 如果旧的版本代码要读取遇到不能识别的可以简单忽略，这样保证了 **向前兼容**

2. 然后因为标签号是唯一的，新的代码总是可以读取旧的数据，这样保证了 **向后兼容**

   > 除非新的字段要求了必需，旧的版本代码会写入失败



### Avro IDL

```c
record Person {
    string                userName;
    union { null, long }  favoriteNumber = null;
    array<string>         interests;
}
```



可以看到 Avro 并没有所谓的**标签号码** ，那它是如何应对模式的演变呢？

暂时没看懂。。。



## REST & SOAP

REST 并不是一个协议，而是用来指导现代Web架构设计的一个哲学，强调通用和简单。也并不只是局限于 HTTP，你也可以基于其他的协议来设计。

相比之下，SOAP 就是一个标准的基于 XML 的协议，其 API 使用了 **Web服务描述语言(WSDL)** 来描述，WSDL 支持代码生成。