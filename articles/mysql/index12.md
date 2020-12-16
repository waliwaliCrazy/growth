# 1. 查询优化

<!-- TOC -->

- [1. 查询优化](#1-查询优化)
    - [1.1. 最大值和最小值的优化](#11-最大值和最小值的优化)
    - [1.2. 优化 Limit 分页](#12-优化-limit-分页)
        - [1.2.1. 使用关联查询优化](#121-使用关联查询优化)
        - [1.2.2. 使用范围查询](#122-使用范围查询)

<!-- /TOC -->

## 1.1. 最大值和最小值的优化

对于 `MIN()` 和 `MAX()` 查询，`MySQL` 的优化做的并不是太好，例如
```SQL
select MIN(id) FROM film where name = '西游记'；
```
假设表 `film` 数据如下：
 id      |  name    |  price
---------|----------|---------
 1       | 英雄本色  | 12
 2       | 哪吒传奇  | 14
 3       | 西游记    | 34
 4       | 水浒传    | 23
 5       | 红楼梦    | 34
 6       | 红与黑    | 2
 7       | 红与黑    | 4
 8       | 美人鱼    | 23
 9       | 爸爸归来  | 23
10       | 我是谁    | 12
11       | 喜羊羊    | 56
12       | 西游记    | 67

> 其中 `id` 为主键并自增，`name` 为 `varchar` 且没有索引

因为 `name` 没有索引，因为 `MySQL` 将会进行一次全表扫描。因为 `id` 为自增，那么我们可以当作，第一次找到 `name='西游记'`时，`id` 就为我们想要的结果，此时我们可以改写 `SQL` 为：
```SQL
select id FROM film where name = '西游记' limit 1；
```

此时当查到第一条记录时，就会停止继续查询，获得更高的性能。

## 1.2. 优化 Limit 分页

在系统进行分页操作的时候，当偏移量大时，例如：`limit 10000,20` 时，`MySQL` 需要查询 `10020` 条记录然后只返回 `20` 记录，前面的记录全部被舍弃，这样的代价非常高，
``` SQL
SELECT id, name, price FROM file LIMIT 10000 OFFSET 20
```
上面的 `SQL` 我想是分页常规的写法，写法没有什么错误，正如上面说到，浪费了大量的性能。

### 1.2.1. 使用关联查询优化
优化此类查询一个简单的方法就是尽可能地使用索引覆盖扫描，而不是查询所有的列，然后根据需要做一次关联操作再返回所需的列。对于偏移大的时候，这样做的效率提升非常大。
```SQL
SELECT
    id, name, price
FROM film
INNER JOIN (
    SELECT id
    FROM film
    LIMIT 10000 OFFSET 20
    ) AS LIM USING(id)
```

### 1.2.2. 使用范围查询
有时候可以将 `LIMIT` 转化为已知位置的查询，让 `MySQL` 通过范围扫描获得到对应的结果。例如，如果在一个位置列上有索引，并且预先计算出了边界值，则改写查询为：
```SQL
SELECT id, name, price
FROM film
WHERE position BETWEEN 10000 AND 10020
ORDER BY position
```