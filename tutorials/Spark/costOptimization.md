**1. Collect, infer and propagate table and column statistics on source and intermediate data**

```hql
ANALYZE TABLE table_name COMPUTE STATISTICS

ANALYZE TABLE table_name COMPUTE STATISTICS FOR COLUMNS col_name1, col_name2 ...
```

![](img/getStatistics.png)


**2. Filter cardinality estimation**

```
// <, <=, >, >=, <=> : use for Integers, Double, Date, Timestamp
// =, <=> : use for String, Integers, Double, Date, Timestamp
```

![](img/cardinEx.png)


**3. Join cardinality estimation**

Use formula for speculating (note: Table.colN - columns are used in WHERE condition):

- inner join:
count(rows T1 || rows T2) = ((count(rows T1) *  count(rows T2)) / max(distinct(rows T1.col1), distinct(rows T2.col1), distinct(rows T1.col2), distinct(rows T2.col2), ...))

- left join:
count(rows T1 || rows T2) = ((count(rows T1) *  (count(rows T2) / max(distinct(rows T1.col1), distinct(rows T2.col1), distinct(rows T1.col2), distinct(rows T2.col2), ...)))

**4. Change joins**

- try to use `hash join`: for using this type of join you have to vary joins into select scripts. Otherwise, you must join abiding by rules as you usually use with hash join

- calculate cost of join due to the next formula (where weight = `spark.sql.cbo.joinReorder.card.weight` = 0.7 default):
`COST`= weight * cost(cpu) + cost(io) * (1 - weight) = weight * rows(into the huges table) * (1 - weight) = weight * cardinality + size * (1 - weight)

- tune parameter: `spark.sql.cbo.joinReorder.card.weight`
