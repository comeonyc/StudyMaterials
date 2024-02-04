### 1、distribute by-SQL语法
```sql
insert overwrite table my_table partition(a,b)
select ta.c as c,
       ta.d as d,
       ta.a as a,
       ta.b as b 
from origin ta
inner join data_count tb
using(a,b)
distribute by a,d
```

这段sql会解析生成的AST，先进行select中的project算子，然后在进行distribute by的repartition操作。

也就是如果要在distribute by中使用的字段，一定要在select中出现，不然distribute by会识别不到对应的project的scheme信息
