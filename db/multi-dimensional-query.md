### 多维查询举例

也就是在多个维度聚合：

```
SELECT year, country, product, SUM(profit) AS profit
       FROM sales
       GROUP BY year, country, product;

+------+---------+------------+--------+
| year | country | product    | profit |
+------+---------+------------+--------+
| 2000 | Finland | Computer   |   1500 |
| 2000 | Finland | Phone      |    100 |
| 2000 | India   | Calculator |    150 |
| 2000 | India   | Computer   |   1200 |
| 2000 | USA     | Calculator |     75 |
| 2000 | USA     | Computer   |   1500 |
| 2001 | Finland | Phone      |     10 |
| 2001 | USA     | Calculator |     50 |
| 2001 | USA     | Computer   |   2700 |
| 2001 | USA     | TV         |    250 |
+------+---------+------------+--------+
```

### 两种实现方式

* 预计算，也称MOLAP: Multidimensional OLAP、OLAP Cube
* 实时计算：也称ROLAP: Relational OLAP

MOLAP实现时，以上面的schema为例，为每一个维度：year、country、product的取值组合计算好所有需要的聚合指标值，如SUM(profit)，查询时直接获取。

ROLAP每次查询需要执行SQL进行相关计算。

以Apache Kylin实现为例，存储：

![](https://user-images.githubusercontent.com/1244560/50264036-d5f65c80-0453-11e9-9970-79c42b7e3e85.png)

SQL查询是如何转化为HBase的Scan操作的？
假设查询SQL如下：

```
select year, sum(price)
from table
where city = "beijing"
group by year
```

这个SQL涉及维度year和city，所以其对应的cuboid是00000011，又因为city的值是确定的beijing,所以在Scan HBase时就会Scan Rowkey以00000011开头且city的值是beijing的行，取到对应指标sum(price)的值，返回给用户。



