# ch03_查询入门

## 3.1 查询机制
 在解析 `select` 语句之前, 或许探讨一个MySQL服务器执行查询的机制也是一件有趣的事(当然对其他数据库服务器也是如此). 首先打开 `mysql` 命令行工具, 然后使用用户名和密码登录(如果MySQL服务器运行在另一台计算机上, 还需要提供主机名). 一旦服务器通过了对用户名和密码的验证, 则为用户创建一个数据库连接. 该连接从应用程序(此处即mysql工具)发出请求后一直保持, 直到应用程序释放连接(比如输入 `quit` 命令)或者服务器关闭连接(比如服务器关机). MySQL服务器会给每个连接赋予一个标识符, 它会在首次登陆时显示:
 ```C
 Welcome TO the MySQL monitor.  Commands END WITH ; OR \g.
 Your MySQL connection id IS 4
 Server version: 5.7.11 MySQL Community Server (GPL)
  
 Copyright (c) 2000, 2016, Oracle AND/OR its affiliates. ALL rights reserved.
  
 Oracle IS a registered trademark OF Oracle Corporation AND/OR its
 affiliates. Other names may be trademarks OF their respective
 owners.
  
 TYPE 'help;' OR '\h' FOR help. TYPE '\c' TO clear the CURRENT INPUT statement.
  
 mysql>
```

本例中, 连接ID是11. 在发生异常情况时, 该信息可能会给数据库管理员带来帮助, 比如手动结束运行几个小时的有问题的查询.


在服务器验证完用户名和密码, 并且创建连接后, 用户就可以执行查询了(当然也包括其他SQL语句). 每当查询被发送到服务端时,服务器在执行语句之前将会进行下面的检查:
* 用户是否有权限执行该语句?
* 用户是否有权限访问目标数据?
* 语句的语法是否正确?


如果查询语句通过了这3个测试,就会被传递给 `查询优化器` , 它负责为查询找到最有效率的执行方式. 优化器通常会做诸如确定 `from` 子句后面各表的连接顺序, 或者可以使用哪些索引之类的工作, 然后选择一个执行方案, 以供服务器执行该查询.


当服务器执行完查询以后, 将会向调用程序(再次提示: 本例中即 `mysql` 工具)返回一个结果集. 在第一章中已经提到, 结果集实际上是一种包含行和列的表. 如果查询没有找到任何结果, 那么 `mysql` 工具将会在其后显示一条提示消息, 例如:
```C
sql> SELECT emp_id, fname, lname
FROM employee
WHERE lname = 'Bkadfl'
[2016-10-18 08:50:22] 0 ROWS retrieved IN 190ms (execution: 131ms, fetching: 59ms)
```

## 3.2 查询语句
查询语句由几个组件或者说子句构成, 不过在MySQL中, 只有一个子句是必不可少的( `select` 子句), 通常的查询语句会至少包含6个子句中的2 ~ 3个, 下面列出用于不同目的的各个子句.

子句名称	| 使用目的
--------|-----------
select	|确定结果集中应该包含哪些列
from	|指明所要提取数据的表, 以及这些是如何连接的.
where	|过滤不需要的数据
group by |	用于对具有相同列值的行进行分组
having	|过滤掉不需要的组
order by|	按一个或多个列, 对最后结果集中的行进行排序


## 3.3 `select` 子句
* 显示 `department` 表中的 `dept_id` 和 `name` 列

解决方案
```mysql
SELECT dept_id, name
FROM department;
```

### 3.3.1 列的别名
* 显示 `department` 表中 `emp_id` , 添加 `active` 字符, 将 `lname` 列全部转变为大写, 同时将上述3列分别指定别名 `status` `empid_x_pi` `last_name_upper`.

解决方案
```mysql
SELECT emp_id,
  'ACTIVE' STATUS,
  emp_id * 3.1415 empid_x_pi,
  UPPER(lname) last_name_upper
FROM employee;
```

### 3.3.2 去除重复的行
* 显示 `account` 表中的 `cust_id` 列, 去除重复的行.

解决方案
```mysql
SELECT DISTINCT cust_id
FROM account;
```

## 3.4 `from` 子句
### 3.4.1 表的概念
#### 子查询中产生的表

* `employee` 表中子查询返回5个列(`emp_id` `fname` `lname` `start_data` `title`), 在外围查询获取其中3列(`emp_id` `fname` `lname`)

解决方案
```mysql
SELECT e.emp_id, e.fname, e.lname
FROM (SELECT emp_id, fname, lname, start_date, title
    FROM employee) e;
```

#### 视图
* 创建视图, 返回 `employee` 表中的4个列(`emp_id` `fname` `lname` `start_date`) , 对此视图进行查询, 返回 `emp_id` `start_year` 列

解决方案
```mysql
CREATE VIEW employee_view AS
  SELECT emp_id, fname, lname,
    YEAR(start_date) start_year
  FROM employee;
 
SELECT emp_id, start_year
FROM employee_view;
```

### 3.4.2 表连接
* 查询同时显示 `employee表`(`emp_id` `fname` `lname`)和 `department` 表(`name`)的数据. 使用表连接查询显示

解决方案
```mysql
SELECT employee.emp_id, employee.fname, employee.lname, department.name dept_name
FROM employee INNER JOIN department
    ON employee.dept_id = department.dept_id;
```

### 3.4.3 定义表别名
* 上述的查询使用表的别名进行查询.

解决方案
```mysql
SELECT e.emp_id, e.fname, e.lname, d.name dept_name
FROM employee e INNER JOIN department d
ON e.dept_id = d.dept_id;
```

## 3.5 where子句
* 查询 `employee` 表, 但只获取头衔为 `head teller` 的雇员数据.

解决方案
```mysql
SELECT emp_id, fname, lname, start_date, title
FROM employee
WHERE title = 'Head Teller';
```


* 查询 `employee` 表, 但只获取头衔为 `head teller` 的雇员数据, 并且只包括在2002年1月之后入职的雇员.

解决方案
```mysql
SELECT emp_id, fname, lname, start_date, title
FROM employee
WHERE title = 'head teller'
AND start_date > '2002-01-01';
```

* 查询 `employee` 表, 获取头衔为 `head teller` 的雇员或在2002年1月之后入职的雇员

解决方案
```mysql
SELECT emp_id, fname, lname, start_date, title
FROM employee
WHERE title = 'head teller'
OR start_date > '2002-01-01';
```

* 查询 `employee` 表, 返回在2002年1月1日之后加入公司的 `head teller` 雇员或者在2007年1月1日后入职的 `teller` 雇员.

解决方案
```mysql
SELECT emp_id, fname, lname, title, start_date
FROM employee
WHERE (title ='head teller' AND start_date > '2002-01-01')
OR (title ='teller' AND start_date > '2007-01-01');
```

## 3.6 `group by` 和 `having` 子句
* 查询每个部门计算器所含的雇员数, 然后返回至少包含2个雇员的部门.

解决方案
```mysql
SELECT d.name, COUNT(e.emp_id) num_employees
FROM department d INNER JOIN employee e 
ON d.dept_id = e.dept_id
GROUP BY d.name
HAVING COUNT(e.emp_id) > 2;
```

## 3.7 `order by` 子句
* 查询 `account` 表, 返回每个雇员开设的账户类型(排序显示)

解决方案
```mysql
SELECT open_emp_id, product_cd
FROM account
ORDER BY open_emp_id;
```

* 查询 `account` 表, 返回每个雇员开设的账户类型(首先根据雇员的ID排序, 然后根据账户类型排序)

解决方案
```mysql
SELECT open_emp_id, product_cd
FROM account
ORDER BY open_emp_id, product_cd;
```

### 3.7.1 升序或降序排序
* 查询 `account` 表, 根据可用余额排序账户, 并且余额最高的出现在最上面.

解决方案
```mysql
SELECT account_id, product_cd, open_date, avail_balance
FROM account
ORDER BY avail_balance DESC ;
```

### 3.7.2 根据表达式排序
查询 `customer` 表, 根据客户的联邦个人识别号码的最后3位数字进行排序. 列出 `cust_id` `cust_type_cd` `city` `state` `fed_id`

解决方案
```mysql
SELECT cust_id, cust_type_cd, city, state, fed_id
FROM customer
ORDER BY RIGHT(fed_id, 3);
```

### 3.7.3 根据数字占位符排序
* 查询 `employee` 表, 返回 `emp_id` `title` `start_date` `fname` `lname` 列, 同时根据第2个和第5个列排序

解决方案
```mysql
SELECT emp_id, title, start_date, fname, lname
FROM employee
ORDER BY 2, 5;
```

## 测试
* 获取所有银行雇员的 `employee ID` , 名字(`first name`) 和姓氏(`last name`), 并先后根据姓氏和名字进行排序

解决方案
```mysql
SELECT emp_id, fname, lname
FROM employee
ORDER BY lname, fname;
```

* 获取所有状态为 `ACTIVE` 以及可以余额大于$2500的账号的 `account ID` `customer ID` 和可用余额(`available balance`)
  
解决方案
```mysql
SELECT account_id, cust_id, avail_balance
 
FROM account
WHERE STATUS = 'ACTIVE'
AND avail_balance > 2500;
```

* 针对 `account` 表编写查询, 以返回开设过账户的雇员ID(使用 `account.open_emp_id` 列), 并且结果集中每个独立的雇员只包含一行数据.
  
解决方案
```mysql
SELECT DISTINCT open_emp_id
FROM account;
```




   


  



  


