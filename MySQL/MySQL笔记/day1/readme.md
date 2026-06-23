# MySQL

## 数据类型

- 整形`int`(长度),最大位数为长度-1位
- `float`(整数部分长度,小数部分长度)
- 年月日时分秒`datetime` 格式: 2026-06-22 11: 38: 26
- 年月日`date`
- 时分秒`time`
- 弹性字符串`varchar`(长度)
- 定长字符串`char`(长度)
- 枚举类型，二选一`enum`('男'，'女')
- 高精度浮点型`decimal`(整数部分长度,小数部分长度)

## 进入`mysql -uroot -p`

### 数据库databases

- 展示数据库`show databases;`
- 创建数据库`create database 数据库名;`
  - 设置字符集 `charset=utf8/utf8mb4`
- 使用/进入`use` 数据库名;

#### 创建数据表 create table 表名 (同时声明字段名和字段类型);

- 设置自增 字段名 数据类型 `auto_increment primary key`
- 向表内添加数据`insert into` 表名 value(符合数据类型的值，按顺序依次加入);
- 展示表内数据`select * from` 表名;
- 创建格式详解 `create table `表明 (字段名 数据类型，字段名 数据类型);
- 表内额外新增内容` alert table `表名 `add` 字段名 字段类型;
- 修改表内字段` alter table `表名 `change`原来的字段名 修改后的字段名 修改后的数据类型;
- 修改表名`rename table`旧表名 `to`新表名

#### 展示数据表

- 展示表的数据库`show tables;`
- 展示表的数据类型`desc`表名;

#### 删除数据表

-`drop table`表名;