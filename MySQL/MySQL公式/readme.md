# mysql 查询的公式
```sql
 - select
 - \*默认是\*号 \*代表所有字段，但是可以替换未列名或者统计函数
 - from
 - 表名，根据自己需求进行替换
 - where 可选项 ，如果存在条件筛选，那么where必须在第五个位置
 - group by 可选项 ，如果存在分组，那么group by 必须在第六个位置
 - having 可选项，只有分租后，才会存在having
 - order by 可选项
 - limit 可选项
 ```