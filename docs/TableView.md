# 表视图

## 只读表视图

只读表视图的存在是为了安全。例如我们知道，如果用户希望只对一个表进行读操作而不希望进行写操作，那么可以创建一个只读表视图，这样当用户的代码无意间对一个表进行 `DELETE` 等操作时，会使用报错等方式提醒用户。

所以这里这样进行设计：设计一种只读视图，和读写视图。

```cpp
using StudentPrototype = 
  zdb::DataPrototype<
    zdb::Field<zdb::DataType::Integer, "id">,
    zdb::Field<zdb::DataType::String, "name">,
    zdb::Field<zdb::DataType::Integer, "age">
  >;

// way 1
using StudentTable = zdb::Table<StudentPrototype>;
StudentTable table = StudentTable("students");
auto rotable = table.getView();

// way 2
using StudentROTable = zdb::TableView<StudentPrototype>;
auto rotable = StudentTableView("students");
```

## 将会怎样报错？

TODO:
