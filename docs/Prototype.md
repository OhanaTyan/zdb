# 原型

`zdb` 中的原型记载一个表中所有的字段名字及其类型。用法如下：

```cpp
using StudentPrototype = 
  zdb::DataPrototype<
    zdb::Field<zdb::DataType::Integer, "id">,
    zdb::Field<zdb::DataType::String, "name">,
    zdb::Field<zdb::DataType::Integer, "age">
  >;
```

可以很容易从这些代码看出来，`Student` 类型有三个字段，也很容易看出来这几个字段的名字。

## 语法糖

如果一个表只有一个字段，可以这样声明：

```cpp
using IdPrototype = zdb::DataPrototype<zdb::DataType::Integer, "id">;

// 相当于 
using IdPrototype = zdb::DataPrototype<
    zdb::Field<zdb::DataType::Integer, "id">
>;

```


