# MySQL 指令速查笔记

## 一、连接与退出
| 操作 | 指令 |
|------|------|
| 登录 | `mysql -u root -p` |
| 退出 | `exit;` 或 `\q` |

---

## 二、数据库操作
| 操作 | 指令 |
|------|------|
| 创建数据库 | `CREATE DATABASE db_name;` |
| 查看所有库 | `SHOW DATABASES;` |
| 使用数据库 | `USE db_name;` |
| 删除数据库 | `DROP DATABASE db_name;` |

---

## 三、数据表操作
### 1. 建表示例
```sql

CREATE TABLE table_name (

id INT NOT NULL AUTO_INCREMENT COMMENT '主键',

name VARCHAR(50) DEFAULT NULL,

age INT,

PRIMARY KEY (id),

INDEX idx_name (name)

) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

### 2. 表管理
| 操作 | 指令 |
|------|------|
| 查看所有表 | `SHOW TABLES;` |
| 查看表结构 | `DESC table_name;` |
| 删除表 | `DROP TABLE table_name;` |

---

## 四、数据 CRUD
| 操作 | 语法 |
|------|------|
| 插入 | `INSERT INTO table_name (col1, col2) VALUES (val1, val2);` |
| 修改 | `UPDATE table_name SET col=val WHERE 条件;` |
| 删除 | `DELETE FROM table_name WHERE 条件;` |

⚠️ 注意：`DELETE` 不带 `WHERE` 会清空整表数据！

---

## 五、查询语句（SELECT）
### 基础语法
```sql
SELECT 字段 FROM 表名
WHERE 条件
GROUP BY 字段
HAVING 筛选
ORDER BY 字段 DESC
LIMIT 起始位置, 条数;
```
### WHERE 条件速查
| 类型 | 示例 |
|------|------|
| 比较 | `=`, `>`, `<`, `!=` |
| 逻辑 | `AND`, `OR`, `NOT` |
| 范围 | `BETWEEN 1 AND 10` |
| 集合 | `IN (1,2,3)` |
| 模糊 | `LIKE '%abc%'` |
| 空值 | `IS NULL`, `IS NOT NULL` |

### 聚合函数
`COUNT()`, `SUM()`, `AVG()`, `MAX()`, `MIN()`

---

## 六、多表连接
| 类型 | 语法 |
|------|------|
| 内连接 | `SELECT * FROM a INNER JOIN b ON a.id=b.id;` |
| 左连接 | `SELECT * FROM a LEFT JOIN b ON a.id=b.id;` |
| 右连接 | `SELECT * FROM a RIGHT JOIN b ON a.id=b.id;` |
| 自然连接 | `SELECT * FROM a NATURAL JOIN b;` |

---

## 七、索引
| 类型 | 指令 |
|------|------|
| 普通索引 | `ALTER TABLE t ADD INDEX idx_name (col);` |
| 唯一索引 | `ALTER TABLE t ADD UNIQUE KEY uk_name (col);` |
| 全文索引 | `ALTER TABLE t ADD FULLTEXT ft_name (col);` |

---

## 八、事务
```sql
BEGIN;      -- 或 START TRANSACTION;
COMMIT;     -- 提交
ROLLBACK;   -- 回滚
```
---

## 九、备份与恢复
| 操作 | 指令 |
|------|------|
| 导出表 | `mysqldump -u root -p db table > file.sql` |
| 导出库 | `mysqldump -u root -p db > file.sql` |

---

## 十、用户与权限
| 操作 | 指令 |
|------|------|
| 创建用户 | `CREATE USER 'user'@'%' IDENTIFIED BY 'pwd';` |
| 授权 | `GRANT ALL ON *.* TO 'user'@'%';` |
| 刷权限 | `FLUSH PRIVILEGES;` |
| 删用户 | `DROP USER 'user'@'%';` |