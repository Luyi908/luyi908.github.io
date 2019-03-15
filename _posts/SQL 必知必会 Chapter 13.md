---
typora-copy-images-to: SQL 必知必会
---

[TOC]

# SQL 必知必会 Chapter 13 创建高级联结

### 13.1 使用表别名

- SQL 除了可以对列名和计算字段使用别名，还允许给表名起别名。这样
  做有两个主要理由
  - 缩短 SQL语句
  - 允许在一条 SELECT 语句中多次使用相同的表

```mysql
SELECT cust_name, cust_contact
FROM Customers AS C, Orders AS O, OrderItems AS OI
WHERE C.cust_id = O.cust_id
AND OI.order_num = O.order_num
AND prod_id = 'RGAN01';
```

- 表别名只在查询执行中使用。与列别名不一样，表别名不返回到客户端

### 13.2 使用不同类型的联结

- 自联结（self-join）、自然联结（natural join）和外联结（outer join）

```mysql
# 假如要给与 Jim Jones同一公司的所有顾客发送一封信件
SELECT cust_id, cust_name, cust_contact
FROM Customers
WHERE cust_name = (SELECT cust_name
					FROM Customers
					WHERE cust_contact = 'Jim Jones');
```

![1552614045576](C:\Users\James\Desktop\博客\SQL 必知必会\1552614045576.png)

- 自然联结

```mysql
SELECT C.*, O.order_num, O.order_date,
OI.prod_id, OI.quantity, OI.item_price
FROM Customers AS C, Orders AS O, OrderItems AS OI
WHERE C.cust_id = O.cust_id
AND OI.order_num = O.order_num
AND prod_id = 'RGAN01';
# 通配符只对第一个表使用。所有其他列明确列出，所以没有重复的列被检索出来
```

- 外联结

1. 对每个顾客下的订单进行计数，包括那些至今尚未下订单的顾客
2. 列出所有产品以及订购数量，包括没有人订购的产品
3. 计算平均销售规模，包括那些至今尚未下订单的顾客

- 在上述例子中，联结包含了那些在相关表中没有关联行的行。这种联结称为外联结

```mysql
SELECT Customers.cust_id, Orders.order_num
FROM Customers LEFT OUTER JOIN Orders
ON Customers.cust_id = Orders.cust_id;
# 外联结还包括没有关联行的行
# 在使用 OUTER JOIN 语法时，必须使用 RIGHT 或 LEFT 关键字指定包括其所有行的表
```

![1552614872443](C:\Users\James\Desktop\博客\SQL 必知必会\1552614872443.png)

### 13.3 使用带聚集函数的联结

```mysql
SELECT Customers.cust_id,
COUNT(Orders.order_num) AS num_ord
FROM Customers INNER JOIN Orders
ON Customers.cust_id = Orders.cust_id
GROUP BY Customers.cust_id;

# 这条 SELECT 语句使用 INNER JOIN 将 Customers 和 Orders 表互# 相关联GROUP BY 子句按顾客分组数据，因此，函数调用 
# COUNT(Orders.order_num)对每个顾客的订单计数，将它作为 num_ord 返回

SELECT Customers.cust_id,
COUNT(Orders.order_num) AS num_ord
FROM Customers LEFT OUTER JOIN Orders
ON Customers.cust_id = Orders.cust_id
GROUP BY Customers.cust_id;
# 外联结
```

![1552615237573](C:\Users\James\Desktop\博客\SQL 必知必会\1552615237573.png)

![1552615386024](C:\Users\James\Desktop\博客\SQL 必知必会\1552615386024.png)

- 这个例子使用左外部联结来包含所有顾客，甚至包含那些没有任何订单
  的顾客

### 13.4 使用联结和联结条件

- 注意所使用的联结类型。一般我们使用内联结，但使用外联结也有效。
  关于确切的联结语法，应该查看具体的文档，看相应的 DBMS支持何
  种语法（大多数 DBMS使用这两课中描述的某种语法）。
- 保证使用正确的联结条件（不管采用哪种语法），否则会返回不正确
  的数据。
- 应该总是提供联结条件，否则会得出笛卡儿积。
- 在一个联结中可以包含多个表，甚至可以对每个联结采用不同的联结
  类型。虽然这样做是合法的，一般也很有用，但应该在一起测试它们
  前分别测试每个联结。这会使故障排除更为简单

### 13.5 小结

- 讲授了如何以及为什么使用别名，然后讨论不同的联结类型以及每类联结所使用的语法。我们还介绍了如何与联结一起使用聚集函数，以及在使用联结时应该注意的问题