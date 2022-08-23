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
> _**常用法则:**_  
> 1.SELECT子句中可以使用常数或者表达式。  
> 2.使用比较运算符时一定要注意不等号和等号的位置。  
> 3.字符串类型的数据原则上按照字典顺序进行排序，不能与数字的大小顺序混淆。  
> 4.希望选取NULL记录时，需要在条件表达式中使用IS NULL运算符。希望选取不是NULL的记录时，需要在条件表达式中使用IS NOT NULL运算符。  

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



















