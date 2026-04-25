# README

## Q&A

### 我想做什么？

我对于 sql 很不满意。所以我想做一个能够帮我生成 sql 语句的工具。

### 我想怎么做？

我现在对于编程语言很犹豫，因为我对于 c++ 和 Python 很看好。我暂时还没想好应该使用什么编程语言去实现这个项目，但我认为 c++ 和 Python 或许是很好的选择

### 项目名字？

`zdb`

### 我野心多大？

虽然我也想过这么大的野心是否可能实现，但我暂时可以先幻想着：

1. 人们发现写 zdb 比写 sql 语句更方便，于是使用 zdb 来生成 sql 语句。
2. 人们发现，与其开发一个兼容 sql 语句的数据库，不如开发一个兼容 zdb 的数据库，因为开发兼容 zdb 的数据库比开发兼容 sql 语句的数据库更方便。

## 我想把它做成大概什么样子？

目前的 sql 语句更多是贴合自然语言，很多时候很难体现数据处理的流程。所以我希望自己开发的工具能够更大程度体现数据处理的流程。

### 给个雏形

给定一个数据类型 Student，包括`id`、`name`、`age`等字段。使用 c++ 的话，可以这样定义这个数据类型

```cpp
using StudentPrototype = 
  zdb::DataPrototype<
    zdb::Field<zdb::DataType::Integer, "id">,
    zdb::Field<zdb::DataType::String, "name">,
    zdb::Field<zdb::DataType::Integer, "age">
  >;

using StudentTable = zdb::Table<StudentPrototype>;

using IdPrototype = zdb::DataPrototype<zdb::Field<zdb::DataType::Integer, "id">;

using IdTable = zdb::Table<IdPrototype>;


class getId : public zdb::OperateView<
  StudentPrototype,
  IdPrototype
>
{
    IdPrototype operate(StudentPrototype& student) override
    {
        return IdPrototype()
            .set(student.get<"id">())
            .get();
    }
};


std::string sql = 
    StudentTable::setTableName("student") // 设置表名字
    | getId() // 应用 getId 视图
    .getSql(); // 获取 sql 语句

// 或

zdb::sqlView = 
    StudentDataset | getId();

std::string sql = sqlView.setTableName("student").getSql();
```

看上去这太复杂了，因为似乎我只需要写一句 `select id from student` 就可以了，为什么要用这么复杂的东西？

### 另一个雏形

想一下另一种情况：如果我想获取所有年龄大于 15 岁的学生的 id，使用 sql 语句可以这样写：`select id from student where age > 15`。上面的例子使用 `OperateView` 用于表示对表中每个对象都进行操作，得到一个新的对象，而这个例子使用 `SelectView`，表示只保留其中一部分对象。

```cpp
class getAgeOver15Id : public zdb::SelectView<
  StudentPrototype
>
{
    zdb::Boolean operate(StudentPrototype& student) override
    {
        return student.get<"age">() > 15;
    }
};

std::string sql = 
    StudentTable::setTableName("student") // 设置数据库名字
    | getAgeOver15Id() // 应用 getAgeOver15Id 视图
    | getId() // 应用 getId 视图
    .getSql(); // 获取 sql 语句

```

### 求和操作

如果我想获取所有年龄的总和，使用 sql 语句可以这样写：`select sum(age) from student`。

``` cpp
class getAge : public zdb::OperateView<
  StudentPrototype,
  zdb::Integer
>
{
    zdb::Integer operate(StudentPrototype& student) override
    {
        return student.get<"age">();
    }
};

std::string sql = 
    StudentDataset::setDatabaseName("student") // 设置数据库名字
    | getAge() // 应用 getAge 视图
    | zdb::SumView<zdb::Integer>() // 应用 SumView 视图
    .getSql(); // 获取 sql 语句

```




## 增删改

刚才似乎只说了如何查询数据。那么接下来讲一下如何增加、删除、修改数据

### 增

```cpp 
auto table = StudentTable::setTableName("student");
using StudentData = zdb::Data<StudentPrototype>;
using DataInserter = StudentData::Inserter;

auto inserter = DataInserter();
// or
auto inserter = StudentData.getInserter();

auto student1 = inserter.getNewLine();
bool ok = student1
    .set<"id">(12)
    .set<"name">("Jack")
    .set<"age">(12)
    .check();
// check 函数用于判断插入的数据是否合法
if (ok){
    inserter.put(student1);
}

auto student2 = inserter.getNewLine();
ok = student2
    .set<"id">(13)
    .set<"name">("Eric")
    .set<"age">(13)
    .check();
if (ok){
    inserter.put(student2);
}

auto sql = inserter.getSql();
/*
INSERT INTO students (id, name, age) VALUES
(12, "Jack", 12),
(13, "Eric", 13);
*/ 
```


### 删

如果你会查询数据，那么你很容易学会删除数据。

```sql
SELECT * FROM students WHERE id=12;
DELETE FROM students WHERE id=12;
```

```cpp
std::string sql = 
    StudentTable::setTableName("student")
    | zdb::Select::Equal<"id">(12)
    .delete();

```

### 改

```cpp
class Update : public zdb::UpdateView<
  StudentPrototype
>
{
    StudentPrototype operate(StudentPrototype& student) override
    {
        return student | 
            zdb::SetValue<"age">(student.age + 1);
    }
};

// 给每个学生的年龄都加 1
std::string sql = 
    StudentTable::setTableName("student")
    | Update()
    .update();
```

## 连接两个表



## 20260425

我现在在想一个问题：非得用 c++ 的模板吗？难道不可以自己创造一个编程语言？
