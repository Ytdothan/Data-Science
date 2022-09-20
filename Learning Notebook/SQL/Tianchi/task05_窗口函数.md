[SQL训练营页面地址](https://tianchi.aliyun.com/specials/promotion/aicampsql)

# 窗口函数

**通用形式：**<窗口函数> OVER ([PARTITION BY <列名>]  # []中的内容可省略                    

​                                                     ORDER BY <排序用列名>)  

> **PARTITON BY**用来分组，即选择要看哪个窗口，类似于GROUP BY 子句的分组功能，但是PARTITION BY 子句并不具备GROUP BY 子句的汇总功能，并不会改变原始表中记录的行数。
>
> **ORDER BY**是用来排序，即决定窗口内，是按那种规则(字段)来排序的。
>
> **窗口函数只能写在select语句中**

![图片](https://img.alicdn.com/imgextra/i1/O1CN01zRGM7Q1QPqVqS4cVW_!!6000000001969-2-tps-862-572.png)

### 专用窗口函数

#### 排序窗口函数

**RANK函数**：rank()over() 。跳跃式排序，如99，99，90，89；通过函数排序为1，1，3，4

**DENSE_RANK函数**：dense_rank()over()。并列连续型排序，如99，99，90，89；通过函数排序为1，1，2，3

**ROW_NUMBER函数**：row_number()over()。连续型排序，如99，99，90，89；通过函数排序为1，2，3，4

#### 偏移分析函数

- lag(字段名,偏移量[,默认值])over()：往上偏移
- lead(字段名,偏移量[,默认值])over()：往下偏移

#### 分区最大/最小值

- first_value(字段名)over()：分组内排序后，截止到当前行第一个值
- last_value(字段名)over()：分组内排序后，截止到当前行最后一个值

### 聚合函数在窗口函数的使用

聚合函数（SUM、MAX、MIN等）结果是，按我们指定的排序，这里是product_id，**当前所在行及之前所有的行**的合计或均值。即累计到当前行的聚合。

```mysql
SELECT  product_id
       ,product_name
       ,sale_price
       ,SUM(sale_price) OVER (ORDER BY product_id) AS current_sum
       ,AVG(sale_price) OVER (ORDER BY product_id) AS current_avg  
  FROM product; 
```

![图片](https://img.alicdn.com/imgextra/i1/O1CN01kuRiqt1r7ygelUfjJ_!!6000000005585-2-tps-961-375.png)

### 计算移动平均

- 将框架指定为 “截止到之前 n 行”，加上自身行：

​			<窗口函数> OVER (ORDER BY <排序用列名>                

​                                 			 ROWS n PRECEDING )                    

- 将框架指定为 “截止到之后 n 行”，加上自身行：

​			<窗口函数> OVER (ORDER BY <排序用列名>                

​                                 			 ROWS n FOLLOWING)   

- 将框架指定为 “之前1行” + “之后1行” + “自身”：

​			<窗口函数> OVER (ORDER BY <排序用列名>                 

​                                  		ROWS BETWEEN n PRECEDING AND n FOLLOWING)

```mysql
SELECT  product_id
       ,product_name
       ,sale_price
       ,AVG(sale_price) OVER (ORDER BY product_id
                               ROWS 2 PRECEDING) AS moving_avg
       ,AVG(sale_price) OVER (ORDER BY product_id
                               ROWS BETWEEN 1 PRECEDING 
                                        AND 1 FOLLOWING) AS moving_avg  
  FROM product
```

![图片](https://img.alicdn.com/imgextra/i1/O1CN01l9qlat1JNN3u15JEj_!!6000000001016-2-tps-898-322.png)

![图片](https://img.alicdn.com/imgextra/i1/O1CN01aO6L4k1IiboQy4j3c_!!6000000000927-2-tps-920-323.png)

### Grouping运算符

#### ROLLUP——计算合计及小计

```
SELECT  product_type
       ,regist_date
       ,SUM(sale_price) AS sum_price
  FROM product
 GROUP BY product_type, regist_date WITH ROLLUP  
```

![图片](https://img.alicdn.com/imgextra/i3/O1CN01OHNT8D1ymT0dimN2J_!!6000000006621-2-tps-787-347.png)

# 练习题

```mysql
1.
SELECT  product_id
       ,product_name
       ,sale_price
       ,MAX(sale_price) OVER (ORDER BY product_id) AS Current_max_price
  FROM product
# 运行结果：Current_max_price列为依据product_id排序，当前所在行及之前所有的行中sale_price的最大值
2.
SELECT  sale_price
		,regist_date
		,SUM(sale_price)over(order by regist_date)sum
    FROM product
#order by NULL顺序见task02
3.
(1)窗⼝函数不指定 PARTITION BY 就是针对排序列进⾏全局排序。
(2)SQL语句执行顺序：FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY。窗口函数一般用于对where或者group by子句处理后的数据进行操作，所以窗口函数原则上只能写在select子句中。
```































