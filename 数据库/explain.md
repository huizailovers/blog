### select_type

```sql
//指的是查询的类型是子查询，普通查询，还是联合查询等
SIMPLE 普通查询 select * from user;
PRIMARY 包含子查询 外层查询
UNION 连接两个select 查询

```

### type

```sql
//除了all之外 其他type都用到了 索引
//除了 index_merge 其他都用到了一个索引
const select * from user where id =1 ;//唯一索引

eq_ref 多表的join查询 select * from user a left join b on a.id = b.id

ref 非唯一索引，使用了等值索引 select * from user where name = 'zhangsan' 

index 结果列中使用到了索引，select name from user ; //虽然索引没在具体的查字段上，但是在查询的结果中。

all 使用的全表扫描  select * from user;

```

### extra

```sql
using index 
//查询的时候不需要回表查询，直接通过索引获取查询的结果
explain select  id,name,age form mylock;

using where（没有使用索引）
//查询的时候，mysql对storage engine 提起的结果进行过滤， 过滤字段没有使用索引

using fileSort（没有使用到索引）
explain select * from mylock order by address；



```

