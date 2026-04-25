# 字段

`zdb::Field` 是一个用于创建类型的模板。

```cpp
using id = zdb::Field<zdb::DataType::Integer, "id">;
```

这个模板表示 `id` 是一个字段，它的字段名叫 `id`，类型是 `zdb::DataType::Integer`。

`zdb::Field` 的第一个参数只接受 `zdb::DataType` 中的字段。


## 约束条件

例如来自 `https://blog.csdn.net/hqxstudying/article/details/148105468` 的片段中，一些字段有特殊属性。

```sql
CREATE TABLE IF NOT EXISTS users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(50) NOT NULL,
  age TINYINT,
  email VARCHAR(100) UNIQUE,
  create_time DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

类 `zdb::Field<datatype, name>` 需要有几个函数叫做 `set_constraint()` 和 `get_constraint()`，用于将 `zdb::Constraint` 中定义的常量传入。




