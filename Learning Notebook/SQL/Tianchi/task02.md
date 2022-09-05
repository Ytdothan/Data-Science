本笔记为[阿里云天池龙珠计划SQL训练营](https://tianchi.aliyun.com/specials/promotion/aicampsql)的学习内容

## 运算符
**算术运算符**
| 含义   | 加法 | 减法 | 乘法 | 除法|
| :---: | :---:|:---:| :---:|:---:|
| 运算符 | +    |-    |*    |/    |  

**比较运算符**  
| 含义   | 相等 |不相等 | 大于等于| 大于|小于等于| 小于|
| :---: | :---:|:---:| :---:|:---:| :---:|:---:|
| 运算符 | =   | <>    |  >=  | >   | <=  | <   |  

**常用法则:**  
1. SELECT子句中可以使用常数或者表达式。  
2. 使用比较运算符时一定要注意不等号和等号的位置。  
3. 字符串类型的数据原则上按照字典顺序进行排序，不能与数字的大小顺序混淆。  
4. 希望选取NULL记录时，需要在条件表达式中使用IS NULL运算符。希望选取不是NULL的记录时，需要在条件表达式中使用IS NOT NULL运算符。  

**逻辑运算符**  
NOT、AND 和 OR 称为逻辑运算符。这里所说的逻辑就是对真值进行操作的意思。真值就是值为真（TRUE）或假 （FALSE）其中之一的值。
**AND运算符优先于OR运算符**，想要优先执行OR运算，可以使用括号
```
# Q: “商品种类为办公用品”并且“登记日期是 2009 年 9 月 11 日或者 2009 年 9 月 20 日”
# A: 通过使用括号让OR运算符先于AND运算符执行
select product_name, product_type, regist_date
from product
where product_type = '办公用品'
  and (regist_date = '2009-09-11'
       or regist_date = '2009-09-20')
```
**练习题**
```
# 1
select product_name, regist_date
from product
where regist_date > '2009-04-28'

# 2 换成 is null 或者 is not null
# 3
select product_name, sale_price, purchase_price
from product
having (sale_price - purchase_price) > 500

# 4
select product_name, product_type,
     (sale_price*0.9-purchase_price)profit
from product
where product_type = '办公用品' or product_type = '厨房用具'
having profit >= 100
```

## 聚合查询
> COUNT：计算表中的记录数（行数）  
> SUM：计算表中数值列中数据的合计值  
> AVG：计算表中数值列中数据的平均值  
> MAX：求出表中任意列中数据的最大值  
> MIN：求出表中任意列中数据的最小值  

**常用法则：**  
- COUNT函数的结果根据参数的不同而不同。COUNT(*)会得到包含NULL的数据行数，而COUNT(<列名>)会得到NULL之外的数据行数。  
- 聚合函数会将NULL排除在外。但COUNT(*)例外，并不会排除NULL。  
- MAX/MIN函数几乎适用于所有数据类型的列。SUM/AVG函数只适用于数值类型的列。  
- 想要计算值的种类时，可以在COUNT函数的参数中使用DISTINCT。  
- 在聚合函数的参数中使用DISTINCT，可以删除重复数据。  

**练习题**
```
1.
# 1. SELECT → 2. FROM → 3. WHERE → 4. GROUP BY

2. select product_type,
sum(sale_price),
sum(purchase_price)
from product
having sum(sale_price) > sum(purchase_price)*1.5
group by product_type

3.
select *
from product
order by if(isnull(regist_date),0,1),regist_date desc, sale_price
<!-- 
【MySql 结论】
order by colum asc 时，null默认被放在最前
order by colum desc 时，null默认被放在最后
ORDER BY IF(ISNULL(update_date),0,1) null被强制放在最前，不为null的按声明顺序[asc|desc]进行排序
ORDER BY IF(ISNULL(update_date),1,0) null被强制放在最后，不为null的按声明顺序[asc|desc]进行排序 -->
```















