[天池龙珠计划SQL训练营](https://tianchi.aliyun.com/forum/postDetail?spm=5176.20222307.J_9059755190.29.36264cb3TDpXjV&postId=167461)

## 表的加减法

### 表的加法

#### union

 UNION 会对两个查询的结果集进行合并和去重，这种去重不仅会去掉两个结果集相互重复，还会去掉一个结果集中的重复行。但在实践中有时候需要需要不去重的并集。UNION ALL不去重。

**table A :** 

| product_id | product_name | product_type | sale_price | purchase_price |
| ---------- | ------------ | ------------ | ---------- | -------------- |
| 0002       | 打孔器       | 办公用品     | 500        | 320            |
| 0006       | 叉子         | 厨房用具     | 500        | null           |
| 0008       | 圆珠笔       | 办公用品     | 100        | null           |

**table B：**

| product_id | product_name | product_type | sale_price | purchase_price |
| ---------- | ------------ | ------------ | ---------- | -------------- |
| 0002       | 打孔器       | 办公用品     | 500        | 320            |
| 0007       | 擦菜板       | 厨房用具     | 880        | 790            |

------

table A **union** table B：

| product_id | product_name | product_type | sale_price | purchase_price |
| ---------- | ------------ | ------------ | ---------- | -------------- |
| 0002       | 打孔器       | 办公用品     | 500        | 320            |
| 0006       | 叉子         | 厨房用具     | 500        | null           |
| 0008       | 圆珠笔       | 办公用品     | 100        | null           |
| 0007       | 擦菜板       | 厨房用具     | 880        | 790            |

table A **union  all** table B：

| product_id | product_name | product_type | sale_price | purchase_price |
| ---------- | ------------ | ------------ | ---------- | -------------- |
| 0002       | 打孔器       | 办公用品     | 500        | 320            |
| 0006       | 叉子         | 厨房用具     | 500        | null           |
| 0008       | 圆珠笔       | 办公用品     | 100        | null           |
| 0002       | 打孔器       | 办公用品     | 500        | 320            |
| 0007       | 擦菜板       | 厨房用具     | 880        | 790            |

**bag模型与set模型**  

Bag 是和 set 类似的一种数学结构，不一样的地方在于：bag 里面允许存在重复元素，如果同一个元素被加入多次，则袋子里就有多个该元素。

- 两个bag交集运算
  - **该元素是否至少在一个 bag 里出现过**
  - **该元素在两个 bag 中的最大出现次数** 
    - eg： A = {1,1,1,2,3,5,7}, B = {1,1,2,2,4,6,8} 两个 bag, 它们的并就等于 {1,1,1,2,2,3,4,5,6,7,8}.
- 两个bag的差
  - **1.该元素是否属于作为被减数的 bag**
  - **2.该元素在两个 bag 中的出现次数**。 只有属于被减数的bag的元素才参与EXCEP ALL运算, 并且差bag中的次数,等于该元素在两个bag的出现次数之差(差为零或负数则不出现). 
    -  A = {1,1,1,2,3,5,7}, B = {1,1,2,2,4,6,8} 两个 bag, 它们的差就等于 {1,3,5,7}.

## 连结（JOIN）

**内连结（inner join）**

`FROM <tb_1> INNER JOIN <tb_2> ON <condition(s)>`

**左连结（left join）**      

`FROM <tb_1> LEFT  OUTER JOIN <tb_2> ON <condition(s)>` 

**右连结（right join）**      

`FROM <tb_1> RIGHT OUTER JOIN <tb_2> ON <condition(s)>` 

**全外连结（outer join）** 

`FROM <tb_1> FULL  OUTER JOIN <tb_2> ON <condition(s)>`

#### ON子句—非等值连接

除了使用相等判断的等值连结，也可以使用比较运算符来进行连接。实际上，包括比较运算符(<,<=,>,>=, BETWEEN)和谓词运算(LIKE, IN, NOT 等等)在内的所有的逻辑运算都可以放在 ON 子句内作为连结条件。

**练习题**

```mysql
#每个商店中, 售价最高的商品的售价分别是多少?
SELECT SP.shop_id
      ,SP.shop_name
      ,MAX(P.sale_price) AS max_price
  FROM shopproduct AS SP
 INNER JOINproduct AS P
    ON SP.product_id = P.product_id
 GROUP BY SP.shop_id,SP.shop_name
```

```mysql
4.1
select *
from product
where sale_price > 500
union all
select *
from product2
where sale_price >500

4.2 
#借助对称差（并集减交集），求交集
select *
from (select * from product UNION select * from product2)a 
where product_id NOT IN
(select product_id 
from product
where  product_id NOT IN (SELECT product_id FROM product2)
union
select product_id
from product2 
where product_id NOT IN (SELECT product_id FROM product))
#借助谓词IN求交集
select *
from product
where product_id IN (select product_id from product2)

4.3
select distinct sp.shop_id, sp.shop_name,max.product_id,max.product_type,max.max_sale_price
from shopproduct sp
    join
    (select p1.product_id,p1.product_type,p1.sale_price, p2.max_sale_price
    from product p1
        join
        (select product_type, max(sale_price)max_sale_price
      from product
      group by product_type)p2
            on p1.product_type=p2.product_type
    and p1.sale_price=p2.max_sale_price)max
on sp.product_id = max.product_id

4.4 
#内连结
select p1.product_id,p1.product_type,p1.sale_price, p2.max_sale_price
from product p1
join
(select product_type, max(sale_price)max_sale_price
from product
group by product_type)p2
   on p1.product_type=p2.product_type
   and p1.sale_price=p2.max_sale_price)max
#子查询
select product_id,p1.product_type,sale_price
       from product p1
       where p1.sale_price in 
       (select max(sale_price)
             from product p2
             where p1.product_type = p2.product_type
        #关联子查询先执行主查询，product_type与max sale_price对应
             group by product_type)
             
4.5
SELECT  product_id
       ,product_name
       ,sale_price
       ,SUM(p2_price) AS sum_price
  FROM (
        SELECT P1.product_id
               ,P1.product_name
               ,P1.sale_price
               ,P2.product_id AS P2_id
               ,P2.product_name AS P2_name
               ,P2.sale_price AS P2_price 
          FROM product AS P1 
          LEFT OUTER JOIN product AS P2 
            ON (
                (P1.sale_price > P2.sale_price) 
				OR (P1.sale_price = P2.sale_price 
					AND P1.product_id <= P2.product_id)
               ) 
		  ORDER BY P1.sale_price,P1.product_id 
  		) AS X
 GROUP BY product_id, product_name, sale_price
 ORDER BY salw_price; 
```



























































