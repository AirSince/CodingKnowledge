# MySql 学习


## 单表访问

### 访问方法（access method）

#### const
通过主键或者唯一二级索引列来定位一条记录的访问方法。

```sql
EXPLAIN SELECT * FROM single_table WHERE id = 18;
```

#### ref
搜索条件为二级索引列与常数等值比较，采用二级索引来执行查询的访问方法称为：ref。


#### ref_or_null


#### range
利用索引进行范围匹配的访问方法称之为：range。

#### index

采用遍历二级索引记录的执行方式称之为：index。