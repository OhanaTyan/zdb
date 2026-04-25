# 视图基类

所有的视图类型均继承自该类型。

我们以这段代码为例，讲一下视图的工作流程。

```cpp
std::string sql = 
    StudentTable::setTableName("student") // 设置数据库名字
    | getAgeOver15Id() // 应用 getAgeOver15Id 视图
    | getId() // 应用 getId 视图
    .getSql(); // 获取 sql 语句
```

## 输入类型与输出类型

对于所有的视图来说，它都有一个输出类型。例如 `StudentTable::setTableName("student")` 的输出类型是 `zdb::Table<StudentPrototype>`，`getAgeOver15Id()` 的输出类型是 `zdb::Table<StudentPrototype>`，`getId()`的输出类型是 `zdb::Table<IdPrototype>`

除了表视图以外（表视图语义是从数据库中读取数据，所以它只输出数据），每个视图都有一个输入类型。使用管道符 `|` 连接的两个视图中，管道符右侧的输入类型必须和管道符左侧的输出类型相同。


## 表视图

表视图没有输入类型，只有输出类型。一个表视图与一个管道符连接得到的结果的类型是另一个表视图。

```cpp
auto a = StudentTable::setTableName("student") // 设置数据库名字
    | getAgeOver15Id();/* */
// a 的类型也是一个表视图，它没有输入类型
```

## 



