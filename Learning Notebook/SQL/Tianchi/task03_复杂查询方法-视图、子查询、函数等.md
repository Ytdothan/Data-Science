[SQL训练营页面地址](https://tianchi.aliyun.com/specials/promotion/aicampsql)

## 视图

视图是一个虚拟的表，不同于直接操作数据表，依据SELECT语句来创建，操作视图时会根据创建视图的SELECT语句生成一张虚拟表，然后在这张虚拟表上做SQL操作。

**创建视图：**create view <视图名称>(<column1>,<column2>,...) as <select语句>

```mysql
#创建基于单表的视图
creat view productsum (product_type, cnt_product)
as
select product_type, count(*)
from product
group by product_type
#创建多表视图
creat view view_shop_product(product_type,sale_price,shop_name)
as
select product_type,sale_price,shop_name
from product,shop_product
where product.product_id = shop_product.product_id
```

**修改视图：**alter view <视图名称> as <select语句>

**更新视图：**视图只是原表的一个窗口，对视图的操作就是对底层基础表的操作，更新视图时也只能更新透过窗口能看到的内容。如果包含以下结构的任意一种都是不可以被更新的：

- 聚合函数 SUM()、MIN()、MAX()、COUNT() 等。
- DISTINCT 关键字。
- GROUP BY 子句。
- HAVING 子句。
- UNION 或 UNION ALL 运算符。
- FROM 子句中包含多个表。

```mysql
UPDATE productsum
   SET sale_price = '5000'
 WHERE product_type = '办公用品'
```

**删除视图：**drop view <视图名称> 

## 子查询

子查询指一个查询语句嵌套在另一个查询语句内部的查询。

### 标量子查询&关联子查询

```mysql
#查询出注册日期最晚的那个商品（标量/单一）
SELECT product_id, product_name, regist_date
  FROM product
 WHERE regist_date = (SELECT MAX(regist_date) FROM product)
#查询出销售单价高于平均销售单价的商品（标量/单一）
SELECT product_id, product_name, sale_price
  FROM product
 WHERE sale_price > (SELECT AVG(sale_price) FROM product)
#选取出各商品种类中高于该商品种类的平均销售单价的商品（关联）
SELECT product_type, product_name, sale_price
  FROM product AS p1
 WHERE sale_price > (SELECT AVG(sale_price)
                       FROM product AS p2
                      WHERE p1.product_type = p2.product_type #如果没有where语句，select子查询中avg值有三个，无法执行比较运算符“>”
                     GROUP BY product_type)
```

> **书写顺序：**
>
> **SELECT**》FROM 》WHERE》GROUP BY》HAVE》ORDER BY
>
> **执行顺序：**
>
> FROM 》WHERE》GROUP BY》HAVE》**SELECT**》ORDER BY

[关联子查询执行逻辑](https://zhuanlan.zhihu.com/p/41844742?spm=5176.21852664.0.0.40d411b79rj82b)：

（1）先从主查询的Product表中product _type列取出第一个值，进入子查询中，得到子查询结果，然后返回父查询，判断父查询的where子句条件，则返回整个语句的第1条结果。

（2）重复上述操作，直到所有主查询中的Product表中product _type列记录取完为止。得出整个语句的结果集，就是最后的答案。

#### 练习题

```mysql
3.1
create view ViewPractice5_1(product_name,sale_price,regist_date)
as
(select product_name,sale_price,regist_date
	from product
	where sale_price>=1000
		and regist_date = '2009-09-20');
select * from ViewPractice5_1;

3.2
INSERT INTO ViewPractice5_1 VALUES (' 刀子 ', 300, '2009-11-02');
# [HY000][1423] Field of view 'shop.viewpractice5_1' underlying table doesn't have a default value
#视图插⼊数据时，原表也会插⼊数据，原表product有三个带有 NOT NULL 约束的字段,因此插入不满⾜约束条件，所以会报错。

3.3
select product_id,
	product_name,
	product_type,
	sale_price,
	(select avg(sale_price)
    	from product p) sale_price_all
    from product;

3.4
create view AvgPriceByType
as
select product_id,
		product_name,
		product_type,
		sale_price,
		(select avg(sale_price)
        from product p2
        where p1.product_type = p2.product_type
        group by product_type)
     from  product p1
select * from AvgPriceByType;
```

## 函数

### 算术函数

- **`ABS( 数值 )`**：绝对值；当 ABS 函数的参数为`NULL`时，返回值也是`NULL`
- **`MOD( 被除数，除数 )`**：求余数
- **`ROUND(x,y)`**：对x值进行四舍五入，精确到小数点后y位；
  - y为负值时，保留小数点左边相应的位数为0，不进行四舍五入；
  - 例如：round(3.15,1)返回3.2，round(14.15,-1)返回10

### **字符串函数**

- **CONCAT(str1,str2,str3,...)**：拼接字符串
  - 任一参数为null时，则返回null
  - 例如：`concat('My',' ','SQL')`返回My SQL，`concat('My',null,'SQL')`返回null
- **length(str)**：字符串长度
- **lower(str)**：将参数中的字符串全都转换为小写，只针对英文字母
- **REPLACE(s,s1,s2)**：使用字符串s2代替s中**所有**的s1。
  - 例如：`replace('MySQLMySQL','SQL','sql')`返回MysqlMysql
- **left(s,n)、right(s,n)&substring(s,n,len)**：截取字符串函数
  - left函数返回字符串s最左边n个字符；eg：`left('abcdefg',3)`返回abc
  - right函数返回字符串s最右边n个字符，eg：`right('abcdefg',3)`返回efg
  - substring函数返回字符串**s**从第**n**个字符起取长度为**len**的子字符串，n也可以为负值，则从**倒数**第n个字符起取长度为len的子字符串，没有len值则取从第n个字符起到最后一位
    - `substring('abcdefg',2,3)`返回bcd；
    - `substring('abcdefg',-2,3)`返回fg；
    - `substring('abcdefg',2)`返回bcdefg
- **SUBSTRING_INDEX (原始字符串， 分隔符，n)**：获取原始字符串按照分隔符分割后，第 n 个分隔符之前（或之后）的子字符串，支持正向和反向索引，索引起始值分别为 1 和 -1。
  - `SUBSTRING_INDEX('www.mysql.com', '.', 2)` 返回www.mysql
  - `SUBSTRING_INDEX('www.mysql.com', '.', -2)`返回mysql.com 
  - 可多次拆分`SUBSTRING_INDEX(SUBSTRING_INDEX('www.mysql.com', '.', 2), '.', -1)`返回mysql

### 日期函数

- **year(date)、month(date)&day(date)**：获取年月日的函数

  - date可以是年月日组成的日期，也可以是年月日时分秒组成的日期时间

  - year(date)返回日期格式中的年份；month(date)返回日期格式中的月份；day(date)返回年日期格式中的日份

  - 例如：year('2021-08-03')返回2021，month('2021-08-03')返回8，day('2021-08-03')返回3

- **date_add(date,interval expr type)&date_sub(date,interval expr type)**：对指定起始时间进行加减操作

  - date用来指定起始时间

  - expr用来指定从起始时间添加或减去的时间间隔，数值

  - type指示expr被解释的方式，type可以可以是以下值
    - 主要使用红框中的值![img](https://api2.mubu.com/v3/document_image/342ef187-60e2-4b9e-a0bd-f763812759a3-9404487.jpg)

  - **`date_add`**函数对起始时间进行加操作，**`date_sub`**函数对起始时间进行减操作

  - 例如：`date_add('2021-08-03 23:59:59',interval 1 second)`返回2021-08-04 24:00:00，`date_sub('2021-08-03 23:59:59',interval 2 month)`返回2021-06-03 23:59:59

- **datediff(date1,date2)**：计算两个日期之间间隔的天数

  - datediff函数由date1-date2计算出间隔的时间，**只有date的日期部分参与计算**，时间不参与

  - 例如：`datediff('2021-06-08','2021-06-01')`返回7，`datediff('2021-06-08 23:59:59','2021-06-01 21:00:00')`返回7，`datediff('2021-06-01','2021-06-08')`返回-7

- **date_format(date,format)**：将日期和时间格式化

  - date_format函数根据format指定的格式显示date值

  - 可以换使用的格式有
    - ![img](https://api2.mubu.com/v3/document_image/3a48a766-8d12-49e5-9f53-8bec62b4791d-9404487.jpg)

  - 例如：`date_format('2018-06-01 16:23:12','%b %d %Y %h:%i %p')`返回 Jun 01 2018 04:23 PM，`date_format('2018-06-01 16:23:12','%Y/%d/%m')`返回2018/01/06

### 转换函数

- **cast(x as type)**：转换数据类型的函数

  - cast函数将一个类型的x值转换为另一个类型的值

  - type参数可以填写char(n)、date、time、datetime、decimal等转换为对应的数据类型

- **COALESCE(数据1，数据2，数据3……)**：COALESCE 是 SQL 特有的函数。该函数会返回可变参数 A 中左侧开始第 1个不是NULL的值。参数个数是可变的，因此可以根据需要无限增加。

## 谓词

> 谓词就是返回值为真值的函数。包括`TRUE / FALSE / UNKNOWN`。
>
> 谓词主要有以下几个：
>
> - LIKE
> - BETWEEN
> - IS NULL、IS NOT NULL
> - IN
> - EXISTS (只在右侧书写 1 个参数，该参数通常都会是一个子查询)

- **`%`**是代表“零个或多个任意字符串”的特殊符号

- **`_`**下划线匹配任意 1 个字符

```mysql
# 取出大阪在售商品的销售单价
#in谓词子查询
SELECT product_name, sale_price
FROM product
WHERE product_id IN (SELECT product_id
 						 FROM shopproduct
                       WHERE shop_id = '000C');
#join表连接查询
select product_name, sale_price 
from shopproduct sp join product p on sp.product_id = p.product_id
where shop_id ='000c';
#exist谓词子查询
SELECT product_name, sale_price
  FROM product AS p
 WHERE EXISTS (SELECT *
                 FROM shopproduct AS sp
                WHERE sp.shop_id = '000C'
                  AND sp.product_id = p.product_id);
```

### 条件函数

- **if(expr,v1,v2)**

  - 如果表达式expr是true返回值v1，否则返回v2

  - 例如：`if(1<2,'Y','N')`返回Y，`if(1>2,'Y','N')`返回N

- **case when**

  - case expr when v1 then r1 [when v2 then r2] ...[else rn] end

    - 例如：case 2 when 1 then 'one' when 2 then 'two' else 'more' end 返回two

    - case后面的值为2，与第二条分支语句when后面的值相等相等，因此返回two

  - case when v1 then r1 [when v2 then r2]...[else rn] end

    - 例如：case when 1<0 then 'T' else 'F' end返回F

    - 1<0的结果为false，因此函数返回值为else后面的F

```mysql
SELECT  product_name,
        CASE WHEN product_type = '衣服' 
        	THEN CONCAT('A：',product_type)
             WHEN product_type = '办公用品' 
             	THEN CONCAT('B：',product_type)
             WHEN product_type = '厨房用具' 
             	THEN CONCAT('C：',product_type)
             ELSE NULL#ELSE 子句也可以省略不写，这时会被默认为 ELSE NULL
        END AS abc_product_type
  FROM  product;
```

#### 练习题

```mysql
3.5
#运算或者函数中含有 NULL 时，结果全都会变为NULL？
√

3.6
SELECT product_name, purchase_price
  FROM product
 WHERE purchase_price NOT IN (500, 2800, 5000)
+--------------+----------------+
| product_name | purchase_price |
+--------------+----------------+
| 打孔器        |      320       | 
| 擦菜板        |      790       |                
+--------------+----------------+
#该查询语句仅仅取出了 purchase_price 不是 500、2800、5000 的商品，⽽不包含purchase_price 为 NULL 的商品，这是因为 谓词⽆法与 NULL 进⾏⽐较。
SELECT product_name, purchase_price
  FROM product
 WHERE purchase_price NOT IN (500, 2800, 5000, NULL)
 # Empty set 
 # 返回了零条记录，这是因为 NOT IN 的参数中不能包含 NULL ，否则，查询结果通常为空。
 
 3.7
 select 
 count(case when sale_price <= 1000 then 1 else null end) low_price,
 count(case when sale_price between 1001 and 3000 then 1 else null end)mid_price,
 count(case when sale_price >= 3001 then 1 else null end) high_price
 from product;
```













