# sql
basic common SQL statements review.

> 《数据库必知必会》笔记





# 第一课：简单概念 
**数据库**：保存有组织的数据的容器，通常是一个或一组文件  
**DBMS**：数据库管理系统 DataBase Management System  
**表table**：某种特定类型数据的结构化清单，使表名成为唯一的，实际上是数据库名和表名等的组合，有的数据库还使用数据库拥有者的名字作为唯一名的一部分，即虽然在相同数据库中不能两次使用相同的名字，但在不同数据库完全可以使用同名的表名  
**列column**：表中的一个字段，表由一个或多个字段组成  
**行row**：表中的一个记录  
**主键primary key**：一列或一组列，其值能唯一标识表中每一行；没有主键，更新或删除表中特定行就极为困难，虽然并非必须，但是应该总是创建主键，其条件：  
* 任意两行都不具有相同的主键 
* 每行都必须具有一个主键值 
* 主键列的值不允许修改或更新 
* 主键值不能重用，如某行从表中删除，其主键不能赋予新行 
* 若使用多个列作为主键，上述条件一起作用于这些列   

**数据类型**：定义了列可存储哪些数据种类  
**SQL Structured Query Language**：结构化查询语言，专门用来与数据库沟通，其特点如下： 
* 几乎所有重要的DBMS都支持SQL
* 简单易学，语句全部由很强描述性的英语单词组成，且数量不多
* 是一种强有力的语言，灵活使用其语言元素，可以进行非常复杂和高级的数据库操作

# 第二课：检索数据 
**检索单列**：  
```
SELECT prod_name FROM Products;  
```
多条SQL语句必须加分号;分隔，单条语句加上也没坏处，SQL语句不区分大小写，因此SELECT和select是相同的，许多SQL开发人员喜欢对SQL关键字使用大写，而对表名列名使用小写，这样代码易于阅读和调试；处理SQL语句时，所有空格都会被忽略  

**检索多列**：  
```
SELECT prod_id, prod_name, prod_price FROM Products;  
```  
列名之间必须以分号;分隔，但最后一个列名后不能加；SQL语句一般返回原始的，无格式的数据，通常很少直接使用实际检索出的数据  

**检索所有列**：  
```
SELECT * FROM Products;  
```
除非确实需要每一列，否则尽量避免使用通配符，会降低检索和应用程序的性能  

**检索不同的值**：  
```
SELECT DISTINCT vend_id FROM Products; 
```
关键字DISTINCT指示DBMS只返回具有唯一性的vend_id行，需要注意的是不能部分使用该关键字，它会作用于多列，如下语句会查询除了vend_id和prod_price都相同以外的所有行  
```
SELECT DISTINCT vend_id, prod_price FROM Products; 
```

**限制检索结果**：  
```
SELECT prod_name FROM Products LIMIT 5;  # MySql, SQLite  
SELECT prod_name FROM Products LIMIT 5  OFFSET 6;  # 从第6行开始取5行，第0行为起始行  
SELECT prod_name FROM Products LIMIT 6， 5;  # 意义同上  
SELECT TOP 5 prod_name FROM Products;	# Access, SQL Server  
SELECT prod_name FROM Products FETCH FIRST 5 ROWS ONLY;  # DB2  
SELECT prod_name FROM Products WHERE ROWNUM <= 5;	# Oracle   
```
**使用注释**：  
` 两个连字符--，表示行内注释 `  
` #表示行内注释 `  
` /* */表示多行注释 `  

# 第三课：排序检索结果
**单列排序**：  
`prod_id  vend_id  prod_name  prod_price  prod_desc`  
```
SELECT prod_name FROM Products ORDER BY prod_name;
```
如果不明确控制的话，检索结果不应该依赖默认的排序结果；在指定一条ORDER BY子句时，应保证它是SELECT语句的最后一条子句，否则将出现错误；通常，ORDER BY子句中所用的列是为显示而选择的列，但是并不一定要这样，非检索的列也可用于排序  

**多列排序**：  
```
SELECT prod_id, prod_name, prod_price FROM Products ORDER BY prod_price, prod_name;
```
首先按prod_price进行排序，若prod_price相同，再按prod_name排序

**按位置排序**：  
```
SELECT prod_id, prod_name, prod_price FROM Products ORDER BY 3, 2;
```
先按3即prod_price排序，若prod_price相同，再按2即prod_name排序，而且如果进行排序的列不在SELECT语句中，则不能使用这项技术  

**指定排序方向**：  
```
SELECT prod_id, prod_name, prod_price FROM Products ORDER BY prod_price DESC, prod_name;
```
DESC DESCENDING表示降序排序，默认是升序ASC ASCENDING排序，关键字DESC只能应用到直接位于其前面的列名，即prod_price，而prod_name仍按升序排序，如果要在多个列上进行降序排序，必须对每一列指定DESC    

```
SELECT prod_id, prod_name, prod_price FROM Products ORDER BY prod_price DESC, prod_name DESC;
```
排序时至于A和a是否一样，取决于数据库的设置方式，在字典排序中，A被视为与a相同，这是大多数DBMS的默认行为，但是许多DBMS允许修改这种行为  

# 第四课：过滤数据
**WHERE子句**：  
```
SELECT prod_name, prod_price FROM Products WHERE prod_price = 3.69;
```
在SELECT语句中，数据根据WHERE子句中指定的搜索条件进行过滤，数据也可以在应用层过滤，但是这种做法极为不妥，优化数据库后可以更快速有效的对数据进行过滤，而让客户端应用去处理数据库的工作会极大的影响应用性能，并且使应用完全不具备可伸缩性，此外，服务器还不得不发送多余的数据  

**WHERE子句操作符**：  
`= 等于    != 或 <> 不等于    BETWEEN 两值之间    IS NULL 值为NULL`  
`< 小于    > 大于    !< 不小于    !> 不大于    <= 小于等于    >= 大于等于`  
并非所有的DBMS都支持这些操作符  

**检查单值**：  
```
SELECT prod_id, prod_price FROM Products WHERE prod_price < 10;
```

**不匹配检查**：  
```
SELECT prod_name, vend_id FROM Products WHERE vend_id != 'DLL01';
```
单引号''用来限定字符串  

**范围检查**：  
```
SELECT prod_name, prod_price FROM Products WHERE prod_price BETWEEN 5 AND 10;
```

**空值检查**：  
```
SELECT cust_name FROM Customers WHERE cust_email IS NULL;
```
NULL与字段包含0，空字符串或仅仅包含空格不一样；确定值是否为NULL，不能简单的检查是否=NULL，只能用WHERE子句的IS NULL来检查；在创建表时，可以指定其中的列能否不包含值，如果可以，则称其包含空值NULL  

# 第五课：高级数据过滤
**组合WHERE子句**：  
```
SELECT prod_id, prod_name, prod_price FROM Products WHERE vend_id='DLL01' AND prod_price <= 4;
```
为了进行更强的过滤控制，SQL允许给出多个WHERE子句，它们可以使用AND或者OR连接  
```
SELECT prod_id, prod_name, prod_price FROM Products WHERE vend_id='DLL01' OR prod_price <= 4;
```
```
SELECT prod_id, prod_name, prod_price FROM Products WHERE (vend_id='DLL01' OR vend_id='BRS01') AND prod_price <= 4;
```

任何时候使用具有AND和OR的WHERE子句，都应该使用圆括号明确的分组操作符，不要过分依赖默认求值顺序  
```
SELECT prod_id, prod_name, prod_price FROM Products WHERE vend_id IN('DLL01', 'BRS01') ORDER BY prod_name;
```

IN取一组逗号分隔，括在圆括号中的合法值，IN的优点：  
1. 在有很多合法选项时，语法更清楚直观
2. 与其他AND和OR操作符组合时，求值顺序更容易管理
3. 比一组OR操作符执行的更快
4. 最大优点是可以包含其他SELECT语句，能够更动态的建立WHERE子句  

```
SELECT prod_id, prod_name, prod_price FROM Products WHERE NOT vend_id = 'DLL01';
```
NOT操作符只有一个功能，就是否定其后所跟的任何条件，作用在要过滤的列前  

```
SELECT prod_id, prod_name, prod_price FROM Products WHERE vend_id != 'DLL01'; # 同上`  
```
在更复杂的子句中，NOT非常有用，在与IN操作符联合使用时，NOT可以非常简单的找出与条件列表不匹配的行  
[回到顶部](#readme)

# 第六课：用通配符进行过滤
**LIKE操作符**：  
为了在搜索子句中使用通配符，必须使用LIKE操作符，其指示DBMS，后跟的搜索模式利用通配符匹配而不是简单的相等匹配进行比较；操作符作为谓词时不是操作符，通配符搜索只能用于文本字段，非文本数据类型不能使用通配符搜索  
**%：** 表示任何字符出现任意次数  
```
SELECT prod_id, prod_name FROM Products WHERE prod_name LIKE '%fish%';
```
```
SELECT prod_id, prod_name FROM Products WHERE prod_name LIKE 'f%h';
```
包括Access在内的很多DBMS都用空格来填补字段内容，如某列有50个字符，而存储的文本只有17个字符，则填满33个空格，这样做一般对数据及其使用没有影响，但是可能对上述SQL语句有负面影响，导致搜索失败，解决办法是给搜索模式再增加一个%，或者使用trim函数  
```
SELECT prod_id, prod_name FROM Products WHERE prod_name LIKE 'f%h%';
```
通配符%看起来可以匹配任何东西，但是无法匹配NULL，子句WHERE prod_name LIKE '%'不会匹配产品名称为NULL的行  

**\_** 当且仅当匹配一个字符，DB2不支持该操作符，Microsoft Access使用的则是?  
```
SELECT prod_id, prod_name FROM Products WHERE prod_name LIKE '__ bag%';
```

**[]：**方括号用来指定一个字符集，必须匹配指定位置的一个字符，仅Access和SQL Server支持  
```
SELECT cust_contact FROM Customers WHERE cust_contact LIKE '[JM]%'; # SQLite不支持
```
```
SELECT cust_contact FROM Customers WHERE cust_contact LIKE '[^JM]%'; # SQLite不支持
```

**通配符的技巧**：
通配符很有用，但是这种功能有代价，即比其他搜索要耗费更长的时间  
* 不要过度使用通配符，如果其他操作能达到目的，就应该使用其他操作符
* 在确实需要使用通配符时，也应该将其放在搜索模式最末，放在开始处搜索起来最慢
* 注意通配符的位置，如果放错地方，可能不会返回想要的数据

# 第七课：创建计算字段
**计算字段**：  
存储在数据表中的数据通常不是应用所需要的，我们需要直接从数据库中检索出转换、计算、格式化过的数据，而不是检索出数据然后再在客户端程序中重新格式化；只有数据库知道SELECT语句中哪些列是实际的表列，哪些是计算字段，从客户端来看，计算字段和表列的数据返回方式相同；在SQL语句内可完成的许多转换和格式化工作都可以直接在客户端应用中完成，但是一般来说，在数据库服务器上完成这些操作要比客户端快得多   

**拼接字段**：  
```
SELECT vend_name || ' (' || vend_country || ')' FROM Vendors ORDER BY vend_name;
```
Access和SQL Server使用+，SQLite等使用||，左括号左边有个空格  

**使用别名**：  
SELECT语句可以很好的进行拼接，可是拼接出来的字段没有名字，只是一个值，如果客户端需要引用，可为它起个别名，用关键字AS赋予  
```
SELECT vend_name || ' (' || vend_country || ')' AS vend_title FROM Vendors ORDER BY vend_name;
```
AS关键字是可选的，但是最好使用它；其常见用途包括在实际的表列名包含不合法的字符如空格时重新命名它，在原理名字含混或容易误解时扩充它；别名可以是一个单词，也可以是一个字符串，但不建议定义为字符串，有的地方称为导出列  

**执行算术计算**：  
```
SELECT prod_id, quantity, item_price, quantity*item_price AS total_price FROM OrderItems WHERE order_num = 20008;
```
计算字段的另一个常见用途是对检索出的数据进行算术计算  

**测试计算**：  
SELECT语句为测试、检验函数和计算提供了很好的方法，虽然它通常用了检索数据，但是省略了FROM子句后就是简单的访问和处理表达式，如： 
```
SELECT 2*3;
SELECT Trim('   abc ');
SELECT Date();
```

# 第八课：使用函数处理数据
**函数的问题**：  
与几乎所有的DBMS同等的支持SQL语句不同，每个DBMS都有特定的函数，事实上，只有很少的几个函数被所有主要的DBMS支持。为了代码的可移植，许多SQL程序猿都不赞成使用特定功能的函数，但是不使用这些函数，编写某些应用程序就会很艰难  

**文本处理函数**：  
```
SELECT vend_name, UPPER(vend_name) AS vend_name_upcase FROM Vendors ORDER BY vend_name；
```
* LEFT() 返回字符串左边的字符
* LENGTH() 字符串的长度
* LOWER() 转换为小写
* UPPER() 转换为大写
* LTRIM() 去掉左边空格
* RTRIM() 去掉右边空格
* SOUNDEX() 返回字符串的SOUNDEX值
SOUNDEX是一个将任何文本串转换为描述其语音表示的字母数字模式的算法，考虑了类似的发音字符和音节，使得能对字符串进行发音比较而不是字母比较；SOUNDEX不是SQL的概念，但是大多数DBMS都提供了支持；如果在创建SQLite时使用了SQLITE_SOUNDEX编译时选项，那么SOUNDEX就可以在SQLite中使用，SQLITE_SOUNDEX不是默认的编译时选项，所有大多SQLite的实现都不支持SOUNDEX  

```
SELECT cust_name, cust_contact FROM Customers WHERE SOUNDEX(cust_contact)=SOUNDEX('Michael Green');
```
则可搜索出'Michelle Green'  

**数值处理函数**：  
在主要DBMS的函数中，数值函数是最一致，最统一的
* ABS() 取绝对值
* SIN() 取正弦
* COS() 取余弦
* TAN() 取正切
* EXP() 取指数
* PI() 取PI
* SQRT() 取平方根  

**日期和时间处理函数**：    
日期和时间采用相应的数据类型存储在表中，每种DBMS都有自己的特殊形式  
```
SELECT order_num FROM Orders WHERE strftime('%y', order_date) = '2012';   # SQLite实现
```

# 第九课：汇总数据
经常需要汇总数据而不是把它们检索出来，比如确定表中行数、获取表中某些行的和、找出表列的最大值平均数；与数据处理函数不同，SQL的聚集函数在主要DBMS获得了相当一致的支持  

**聚集函数**：  
对某些运行的函数，计算并返回一个值  
* AVG()：返回某列的平均值  
```
SELECT AVG(prod_price) AS avg_price FROM Products;
```

也可返回特定列或行的平均数  
```
SELECT AVG(prod_price) AS avg_price FROM Products where vend_id='DLL01';
```

要获取多个列的平均值，必须使用多个AVG函数，默认忽略值为NULL的行  
* COUNT()：返回某列的行数
* COUNT(\*)：不管行的值是否为空，总是统计
* SELECT COUNT(\*) from Customers;
* COUNT(column)：对特定列仅统计值不为空的行  
```
SELECT COUNT(cust_email) AS num_email from Customers;
```
* MAX()：返回某列最大值  
```
SELECT MAX(prod_price) AS max_price from Products;
```
虽然MAX一般能找出最大的数值或日期，但许多DBMS运行将它用了返回任意列的最大值，包括文本列，此时返回的是按该列排序后的最后一行，MAX默认忽略值为NULL的行
* MIN()：返回某列最小值，默认忽略值为NULL的行  
```
SELECT MIN(prod_price) AS max_price from Products;
```
* SUM()：返回某列值之和，默认忽略值为NULL的行  
```
SELECT SUM(quantity) AS total_quantity FROM OrderItems where order_num=20005;
```
也可用来合计计算值  
```
SELECT SUM(item_price*quantity) AS total_price FROM OrderItems where order_num=20005；
```

**聚合不同值**：  
* 可对所有行执行计算，这是默认行为
* 只包括不同的列，指定DISTINCT参数  

```
SELECT AVG(DISTINCT prod_price) AS avg_price FROM Products where vend_id='DLL01';
```
会自动剔除价格相同的行；DISTINCT必须使用列名，不能用于计算或表达式；只能用于COUNT(column)，不能用于COUNT(\*)；虽然从技术上可用于MAX或MIN，但是无价值，不管是否考虑不同值，结果都是一样  

**组合聚集函数**：  
```
SELECT AVG(prod_price) AS avg_price, MAX(prod_price) AS max_price, MIN(prod_price) AS min_price, COUNT(*) AS num_items FROM Products
```
在指定别名以包含某个聚集函数的结果时，不应该使用表中实际的列名，虽然也合法；聚集函数很高效，它们返回结果一般比在客户端程序中计算要快得多

# 第十课：分组数据
**数据分组**：  
使用分组可以将数据分为多个逻辑组，对每个组进行聚集计算  

**创建分组**：  
```
SELECT vend_id, COUNT(*) AS num_prods FROM Products GROUP by vend_id;
```
GROUP BY指示DBMS按照vend_id排序并分组数据，这就对每个vend_id而不是整个表计算num_prods一次  
1. GROUP BY指示DBMS分组数据，然后对每个组而不是整个结果进行聚集
2. GROUP BY子句可以包含任意数目的列，因此可以对分组嵌套，更细致的分组数据
3. 如果GROUP BY子句中嵌套了分组，数据将在最后指定的分组上进行汇总，即建立分组时，对指定的所有列一起计算
4. GROUP BY子句列出的每一列都必须是检索列或有效的表达式，但不能是聚集函数，如果在SELECT中使用表达式，则必须在GROUP BY子句中指定相同的表达式，不能使用别名
5. 大多数SQL实现不允许GROUP BY列带有长度可变的数据类型，如文本或备注型字段
6. 除了聚集计算语句外，SELECT语句中每一列都必须在GROUP BY子句中给出
7. 如果分组列中包含具有NULL值的行，则NULL作为一个分组返回，若有多行NULL，它们被分为一组
8. GROUP BY子句必须出现在WHERE子句之后，ORDER BY子句之前  

**过滤分组**：  
目前所学过的所有类型的WHERE子句都可以用HAVING来替代，唯一差别是WHERE过滤行，HAVING过滤分组  
```
SELECT cust_id, COUNT(*) AS orders FROM Orders GROUP BY cust_id HAVING COUNT(*) <2;
```
此处WHERE将不起作用，COUNT作用于cust_id的分组；WHERE在数据分组前进行过滤，HAVING在数据分组后进行过滤，WHERE排除的行不包括在分组中，这可能会改变计算值，从而影响HAVING子句中基于这些值过滤掉的分组  
```
SELECT vend_id, prod_price, COUNT(*) AS num_prods FROM Products WHERE prod_price >= 4 GROUP BY vend_id HAVING COUNT(*) >=2;
```
先将prod_price小于4的剔除，再按vend_id分组，每个分组行数大于2的再列出来  
HAVING和WHERE非常类似，如果不指定GROUP BY，则大多数DBMS会同等对待它们，使用HAVING时应该结合GROUP BY子句，而WHERE子句只是用于标准的行级过滤  

**分组与排序**：
ORDER BY对产生的输出排序；任意列都可以使用，甚至非选择的列也可以使用；不一定需要使用  
GROUP BY对行分组，但输出可能不是分组的顺序；只可能使用选择列或表达式，而且必须使用每个选择列表达式；如果与聚集函数一起使用列或表达式，则必选使用  
一般在使用GROUP BY子句时，应该给出ORDER BY子句，这是保证数据正确排序的唯一方法，千万不能依赖GROUP BY排序顺序  
```
SELECT order_num, COUNT(*) AS items FROM OrderItems GROUP BY order_num HAVING COUNT(*) >=3 ORDER BY items, order_num;
```
[回到顶部](#readme)


# 第十一课：使用子查询
**子查询**：  
任何SQL语句都是查询，但是此术语一般特指SELECT语句，SQL允许创建子查询，即嵌套在其他查询中的查询  

**利用子查询进行过滤**：  
```
SELECT cust_name, cust_contact FROM Customers WHERE cust_id IN(SELECT cust_id FROM Orders WHERE order_num IN (SELECT order_num FROM OrderItems WHERE prod_id='RGAN01'));
```
在SQL语句中，子查询总是由内往外；在WHERE子句中使用子查询可以编写出功能强大且很灵活的SQL语句，对于嵌套子查询的数目没有限制，不过由于性能限制，不能嵌套太多；作为子查询的SELECT语句只能查询单个列，企图查询多个列将返回错误；使用子查询并不总是执行这类数据检索的最有效办法  

**作为计算字段使用子查询**：  
使用子查询的另一个方法是创建计算字段  
```
SELECT cust_name, cust_state, (SELECT COUNT(*) FROM Orders WHERE Orders.cust_id=Customers.cust_id) AS orders FROM Customers ORDER BY cust_name;
```
orders是一个计算字段，由括号中的子查询建立  

# 第十二课：联结表
关系表设计就是要把信息分解成多个表，一类数据一个表，各个表通过某些共同的值互相关联。联结是一种机制，用来在一条SELECT语句中关联表，因此称为联结，使用特殊的语法，可以联结多个表返回一组输出，使用联结表优点：  
* 信息不重复，赴会浪费时间和空间
* 如果信息变动，只需更新单个表记录
* 数据不重复，可保持数据一致性  

**创建联结**：  
```
SELECT vend_name, prod_name, prod_price FROM Vendors, Products WHERE Vendors.vend_id = Products.vend_id;
```
在联结两个表时，实际上是将第一个表中的每一行与第二个表中每一行配对，WHERE子句作为过滤条件，只包含那些匹配的行。要保证所有联结都有WHERE子句，否则DBMS将返回比想象多得多的数据  

**笛卡尔积**：  
由没有联结条件的表返回的结果，检索出的行的数目将是第一个表的行数乘以第二个表的行数  

**內联结**：  
基于两个表之间的相等测试，也叫等值联结  
```
SELECT vend_name, prod_name, prod_price FROM Vendors INNER JOIN Products ON Vendors.vend_id = Products.vend_id;
```

**联结多个表**：  
```
SELECT vend_name, prod_name, prod_price quantity FROM OrderItems, Products, Vendors WHERE Vendors.vend_id = Products.vend_id AND OrderItems.prod_id = Products.prod_id AND order_num = 20007;
```
不要联结不必要的表，联结的表越多，性能下降越厉害  
```
SELECT cust_name, cust_contact FROM Customers WHERE cust_id IN (SELECT cust_id FROM Orders WHERE order_num in (SELECT order_num FROM OrderItems WHERE prod_id = 'RGAN01'));
```
等同于：  
```
SELECT cust_name, cust_contact FROM Customers, Orders, OrderItems WHERE Customers.cust_id = Orders.cust_id AND OrderItems.order_num = Orders.order_num AND prod_id = 'RGAN01'
```
执行一个任务给定的SQL操作一般不止一种方法，很少有绝对正确或绝对错误的方法，性能可能受操作类型，所使用的DBMS，表中数据量，是否存在索引或键等条件影响

# 第十三课：创建高级联结
**使用表别名**：  
```
SELECT cust_name, cust_contact FROM Customers AS C, Orders AS O, OrderItems AS OI WHERE C.cust_id = O.cust_id AND O.order_num = OI.order_num AND prod_id = 'RGAN01'
```
可以缩短SQL语句，允许在一条SELECT语句中多次使用相同的表  
ORACLE不支持AS关键字，要在ORACLE中使用别名，可以不用AS，简单指定列名 Customers C  

**自联结**：  
```
SELECT cust_name, cust_id, cust_contact FROM Customers WHERE cust_name = (SELECT cust_name FROM Customers WHERE cust_contact = 'Jim Jones');
```
等同于：  
```
SELECT c1.cust_name, c1.cust_id, c1.cust_contact FROM Customers AS c1, Customers AS c2 WHERE c1.cust_name = c2.cust_name AND c2.cust_contact = 'Jim Jones';
```
许多DBMS处理联结比处理子查询要快得多  

**自然联结**：  
无论何时对表进行联结，应至少有一列不止出现在一个表中。标准的联结返回所有数据，相同的列甚至多次出现，自然联结排除多次出现，使每一列只返回一次，这项工作不是由系统完成，一般通过对一个表使用通配符SELECT \*，而对其他表的列使用明确的子集完成  
```
SELECT C.*, O.order_num , O.order_date, OI.prod_id, OI.quantity, OI.item_price FROM Customers AS C, Orders AS O, OrderItems AS OI WHERE C.cust_id=O.cust_id AND OI.order_num=O.order_num AND prod_id='RGAN01'
```
通配符只对第一个表使用，所有其他列明确列出，所以没有重复列被检索，我们迄今建立的每个内联结都是自然联结，很可能永远都不会用到不是自然连接的内连接  

**外联结**：  
联结中包含了那些在相关表中没有关联行的行。与內联结关联两个表中的行不同的是，外联结还包括没有关联行的行，在使用OUTER JOIN语法时，必须使用RIGHT或LEFT关键字指定包括其所有行的表。  
SQLite仅支持LEFT OUTER JOIN，左外联结和右外联结的唯一差别是所关联的表的顺序，换句话说，调整FROM或WHERE子句中表的顺序，左外联结可以转换为右外联结  

**內联结**：  
检索出所有顾客及其订单  
```
SELECT Customers.cust_id, Orders.order_num FROM Customers INNER JOIN Orders ON Customers.cust_id = Orders.cust_id
```

**外联结**：  
检索出包括没有订单的顾客在内的所有顾客  
```
SELECT Customers.cust_id, Orders.order_num FROM Customers LEFT OUTER JOIN Orders ON Customers.cust_id = Orders.cust_id
```

**全外联结**：  
它检索两个表中的所有行并关联那些可以关联的行，与左外联结或右外联结包含一个表的不关联的行不同，全外联结包含两个表的不关联的行  
```
SELECT Customers.cust_id, Orders.order_num FROM Customers FULL OUTER JOIN Orders ON Customers.cust_id = Orders.cust_id
```

**使用带聚集函数的联结**：  
如果不分组，则结果大不相同：  
```
SELECT Customers.cust_id, COUNT(Orders.order_num) AS num_ord FROM Customers INNER JOIN Orders ON Customers.cust_id = Orders.cust_id GROUP BY Customers.cust_id
```
**使用左外联结包含所有顾客，甚至包含没有任何订单的顾客**：  
```
SELECT Customers.cust_id, COUNT(Orders.order_num) AS num_ord FROM Customers LEFT OUTTER JOIN Orders ON Customers.cust_id = Orders.cust_id GROUP BY Customers.cust_id
```

# 第十四课：组合查询
SQL也允许多个查询（多条SELECT语句），并将结果作为一个查询结果返回，主要有两种使用场景：  
* 在一个查询中从不同的表返回结构数据
* 对一个表执行多个查询，按一个程序返回数据

**创建组合查询**：  
任何具有多个WHERE子句的SELECT语句都可以作为一个组合查询  
```
SELECT cust_name, cust_contact, cust_email, cust_state FROM Customers WHERE cust_state IN('IL', 'IN', 'MI') OR cust_name='Fun4All'

```

**两个WHERE子句用UNION联结**：  
```
SELECT cust_name, cust_contact, cust_email, cust_state FROM Customers WHERE cust_state IN('IL', 'IN', 'MI')
UNION
SELECT cust_name, cust_contact, cust_email, cust_state FROM Customers WHERE cust_name='Fun4All' 
```
在简单的例子中，使用UNION可能比WHERE子句更为复杂，但是对于较为复杂的过滤条件，或从多个表检索数据，使用UNION处理更简单，其使用规则：  
* 必须由两条或以上SELECT语句组成，语句间用UNION分隔
* 每个查询必须包含相同的列、表达式或聚集函数，出现次序不重要
* 列数据类型必须兼容  

**包含或取消重复的行**：  
UNION从查询结果自动去掉了重复的行，其行为与一条SELECT语句的多个WHERE子句条件一样，如果想返回所有行，可使用UNION ALL，这是WHERE子句实现不了的功能  

**对组合查询结果排序**：  
在用UNION组合查询时，只能使用一条ORDER BY子句，必须位于最后一条SELECT语句之后，但是它用来对所有的SELECT语句的返回结果进行排序

# 第十五课：插入数据
**插入完整的行**：  
```
INSERT INTO Customers VALUES('1000000006', 'Toy Land', '123 Any Street', 'New York', 'NY', '11111', 'USA', NULL, NULL)
```
没列必须提供值，如果没有，应该写NULL，各个列必须按照表中定义顺序依次填充；虽然语法简单，但是并不安全，插入数据不应该依赖表中列的次序，下次表结构变化时无法保证列的次序不变换  

**更为安全的写法**：  
```
INSERT INTO Customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact, cust_email)
VALUES('1000000007', 'Jimmy Land', '234 Any Street', 'New York', 'NY', '22222', 'USA', NULL, NULL);
```
因为提供了列名，VALUES必须以其指定的次序填充，而不是表中定义的实际次序，其优点是即使表的结构变换也不会影响该语句的正常执行  

**插入部分行**：  
```
INSERT INTO Customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country)
VALUES('1000000008', 'Lucky Land', '345 Any Street', 'New York', 'NY', '33333', 'USA');
```
省略的列必须满足以下条件：  
* 该列定义允许NULL值（无值或空值）
* 在表定义中给出默认值，如果插入数据不指定，则使用默认值  

**插入检索处理的数据**：  
```
INSERT INTO Customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country) 
Select  cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country
From CustNew
```
INSERT通常只能插入一行，但是INSERT SELECT可以一次性插入多行  

**从一个表复制到另一个表**：  
```
SELECT * INTO CustCopy FROM Customers;	--SqLite不支持Select Into
CREATE TABLE CustCopy AS SELECT * FROM Customers WHERE Customers.cust_city = 'Chicago';
```
如果只想复制部分列，需要明确指出列名，如果只想复制部分行，需要加过滤条件  
[回到顶部](#readme)


# 第十六课：更新和删除数据
**更新数据**：  
```
UPDATE Customers SET cust_email = 'sky@xunrui.com.cn', cust_contact = 'sky' WHERE cust_id = '1000000005'
```
更新多个列时，只需一个SET命令，每个列之间用逗号分隔  

**删除数据**：  
```
DELETE FROM Customers WHERE cust_id = '1000000006'
```
DELETE不需要列名或通配符，删除整行或全部行，如果要删除指定列，要使用UPDATE语句（值设置为NULL），如果要快速删除所有行，可使用TRUNCATE TABLE语句，速度更快  
使用外键确保引用完整性的一个好处是：DBMS通常可以防止删除某个关系需要用到的行  

避免删除或更新了整个表，使用UPDATE或DELETE需遵循以下原则：  
1. 除非打算更新或删除每一行，否则一定要带WHERE子句
2. 保证每个表都有主键
3. 使用强制实施引用完整性的数据库，这样DBMS将不允许删除其数据与其他表相关联的行
4. 有的DBMS允许数据库管理员施加约束，防止执行不带WHERE子句的UPDATE或DELETE语句，如果有这特性，就使用

# 第十七课：创建和操纵表
**创建表**：  
```
CREATE TABLE Products 
(
prod_id 		CHAR (10) 	NOT NULL,
vend_id 		CHAR (10) 	NOT NULL,
prod_name 	CHAR (255) 	NOT NULL,
prod_price 	DECIMAL (8, 2) NOT NULL,
prod_desc 	TEXT,
PRIMARY KEY 
(
prod_id
),
FOREIGN KEY
 (
vend_id
)
REFERENCES Vendors (vend_id)
);
```  
对于MySWL，varchar必须替换为text，对于DB2，必须去掉最后一列的NULL，在创建新的表时，指定的表名必须不存在，否则会出错，防止意外覆盖已有的表，SQL要求首先手工删除该表，然后重建它，而不是简单的用创建语句覆盖它  

**NULL**：  
就是没有值或缺值，不允许NULL的列接受没有列值的行，在插入或更新行时，必须指定列值，只有不允许NULL的列可作为主键  
SQL允许默认值，用DEFAULT指定，通常用于日期，遗憾的是，获取系统日期的命令在不同的DBMS几乎都不一样  
```
Access 	NOW()
DB2		CURRENT_DATE
MySQL	CURRENT_DATE()
Oracle	SYSDATE
SQL Server	GETDATE()
SQLite	date('now')
```  

**更新表**：  
虽然所有的DBMS都支持ALTER TABLE，但它们所允许更新的内容差别很大
1. 理想情况下，不要在表中包含数据时对其更新，应该在表的设计过程中充分考虑未来可能的需求，避免今后对表的结构做大改动
2. 所有的DBMS都允许给现有的表增加列，但对列的数据类型有限制
3. 许多DBMS不允许删除或更改表中的列
4. 多数DBMS允许重命名表中的列
5. 许多DBMS限制对已经有数据的列进行修改，但未填充数据的列集合没有限制  

```
ALTER TABLE Vendors ADD vend_phone CHAR(20)	 --这可能是所有DBMS都支持的唯一操作  
```

**复杂的表结构更改一般需要手动操作**：  
1. 用新的列布局新建一个表
2. 使用INSERT SELECT语句从旧表复制数据到新表
3. 检验包含所需数据的新表
4. 重命名旧表（如果确定，删除）
5. 用旧表名重命名新表
6. 根据需要，重新创建触发器、存储过程、索引和外键  

SQLite对使用ALTER TABLE执行的操作有所限制，最重要的一个是不支持使用ALTER TABLE定义主键和外键，这些必须在创建表时指定
使用ALTER TABLE要极为小心，应提前做好备份，如果增加了不需要的列，可能无法删除，如果删除了不应该删除的列，可能会丢失该列中的所有数据  

**删除表**：  
删除表没有确认，也不能撤销，执行这条语句将永久删除该表。许多DBMS允许强制实施有关规则，防止删除与其他表关联的表，如果有此功能，需开启   
`DROP TABLE CustCopy;`  

# 第十八课：视图
Access不支持视图，MySQL从版本5开始支持视图，SQLite仅支持只读视图  

**为什么使用视图**：  
1. 重用SQL语句
2. 简化复杂的SQL操作
3. 使用表的一部分而不是整个表
4. 保护数据，可以授予用户访问表的特定部分的权限，而不是整个表的访问权限
5. 更改数据格式和表示，视图可以返回与底层表的表示和格式不同的数据
可以用与表相同的方式使用视图，视图仅仅是用来查看存储在别处数据的一种设施，本身不包含数据，返回的数据是从其他表检索出来的；在添加或更改这些表的数据时，视图将返回改变过的数据；因为视图不包含数据，所以每次使用视图时，必须处理查询执行所需要的所有检索；如果用多个联结和过滤创建了复杂的视图或者嵌套了视图，性能可能会下降的很厉害  

**视图的规则与限制**：  
1. 与表一样，视图必须唯一命名，不能给视图取与别的视图或表相同的名字
2. 可以创建的视图数目无限制
3. 创建视图，必须有足够的访问权限，通常由数据库管理员授予
4. 视图可以嵌套，但是严重影响性能
5. 许多DBMS禁止在视图查询中使用ORDER BY子句
6. 有些DBMS要求对返回的所有列进行命名，如果列是计算字段，需要使用别名
7. 视图不能索引，也不能有关联的触发器或默认值
8. 有些DBMS只能创建只读视图
9. 有些DBMS只能创建这样的试图-不能进行导致行不再属于视图的插入或更新  

**创建视图**：  
与CREATE TABLE一样，CREATE VIEW只能创建不存在的视图  
```
CREATE VIEW ProductCustomers AS SELECT cust_name, cust_contact, prod_id FROM Customers, Orders, OrderItems WHERE Customers.cust_id=Orders.cust_id AND OrderItems.order_num=Orders.order_num; 
SELECT cust_name, cust_contact FROM ProductCustomers WHERE prod_id=\'RGAN01\'
```  

创建不绑定特定数据的视图是一个好办法，用WHERE子句再进行过滤  

**用视图重新格式化检索出的数据**：  
```
CREATE VIEW VendorLocations AS SELECT RTRIM(vend_name) || '(' || RTRIM(vend_country) || ')' AS vend_title FROM Vendors;
```

**用视图过滤不想要的数据**：  
```
CREATE VIEW CustomerEmailList AS SELECT cust_id, cust_name, cust_email FROM Customers WHERE cust_email IS NOT NULL;
```

**使用视图与计算字段**：  
```
CREATE VIEW OrderItemsExpanded AS SELECT order_num, prod_id, quantity, item_price, quantity*item_price AS expanded_price FROM OrderItems;  
SELECT * FROM OrderItemsExpanded WHERE order_num=20008;
```  
视图是虚拟的表，包含的不是数据而是根据需要检索数据的查询，视图提供了一种封装SELECT语句的方式，可以用来简化数据处理，重新格式化或保护基础数据

# 第十九课：存储过程
存储过程是为了以后使用而保存的一条或多条SQL语句，可将其视为批文件，但它们的作用不限于此，Access和SQLite不支持存储过程，MySQL版本5已经支持存储过程  

**存储过程的优势**：  
1. 通过把处理封装在一个易用的单元，可以简化复杂的操作
2. 由于不要求反复建立一系列处理步骤，因而保证了数据的一致性
3. 简化对变动的管理，如果表名、列名、业务逻辑发生变化，只需更改存储过程的代码，使用者甚至不需要知道
4. 因为存储过程通常以编译过的形式存储，所以DBMS处理命令所需的工作量小，提高了性能
5. 存在一些只能用在单个请求中的SQL元素和特性，存储过程可以使用它们来编写功能更强更灵活的代码  

**存储过程的缺陷**：  
1. 不同DBMS的存储过程语法有所不同，事实上，编写真正可移植的存储过程几乎是不可能的，不过，存储过程的自我调用可以相对保持可移植，因此要移植到别的DBMS，至少客户端代码无需改动
2. 一般来说，编写存储过程比编写基本SQL语句复杂，需要更高的技能，更丰富的经验，因此，许多数据库管理员把限制存储过程的创建作为安全措施  
多数DBMS将编写存储过程所需要的安全和访问权限与执行存储过程所需的安全和访问权限分开了，这是好事，即使不能自己编写存储过程，仍然可以在适当的时候执行存储过程  

**执行存储过程**：  
```
EXECUTE AddNewProduct('JTS01', 'Stuffed Eiffel Tower', 6.49, 'push stuffed toy with text');
```
AddNewProduct存储过程名，四个参数：供应商ID，产品名，价格和描述


**创建存储过程**：  
SQL Server的版本  
```
CREATE PROCEDURE MailingListCount AS
DECLARE @cnt INTEGER
SELECT @cnt = COUNT(*)
FROM Customers
WHERE NOT cust_email IS NULL;
RETURN @cnt
调用如下：
DECLARE @retValue INT
EXECUTE @retValue = MailingListCount;
SELECT @retValue
```  
[回到顶部](#readme)


# 第二十课：事务
事务处理用来管理INSERT、UPDATE和DELETE语句，通过确保成批的SQL操作要么完成执行，要么完全不执行，来维护数据库的完整性；如果没有错误发生，整组语句提交（写到）数据库表，如果发生错误，则进行回退，将数据库恢复到某个已知且安全的状态；管理事务的关键在于将SQL语句组分解为逻辑块，并明确规定数据何时该回退，何时不该回退  

`SQL SERVER：  `  
```
BEGIN TRANSACTION
...
COMMIT TRANSACTION
MySQL：
START TRANSACTION
...
```

`Oracle：`  
```
SET TRANSACTION
...
```  
多数DBMS没有明确表示事务处理在何时结束，事务一直存在，直到被中断  
一般的SQL语句都是针对数据库表操作的，自动的隐性提交，在事务处理中，一般都是明确使用COMMIT语句显式提交  
`SQL SERVER：`  
```
BEGIN TRANSACTION
DELETE FROM OrderItems WHERE order_num = 12345;
DELETE FROM Orders WHERE order_num = 12345
COMMIT TRANSACTION
```  

要支持回退部分事务，必须在事务处理块中的合适位置添加占位符，也称为保留点  
`SQL SERVER：`  
```
BEGIN TRANSACTION
INSERT INTO Customers(cust_id, cust_name)
VALUES('10000010', 'Toy Emporium')
SAVE TRANSACTION StartOrder
INSERT INTO Orders(order_num, order_date, cust_id)
VALUES(20100,'2010/12/12','1000000010')
IF @@ERROR <> 0 ROLLBACK TRANSACTION StartOrder
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20100, 1, 'BR01', 100, 5.49);
IF @@ERROR <> 0 ROLLBACK TRANSACTION StartOrder
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20100, 2, 'BR02', 100, 9.49);
IF @@ERROR <> 0 ROLLBACK TRANSACTION StartOrder
COMMIT TRANSACTION
```  

# 第二十一课：游标
游标是一个存储在DBMS服务器上的数据库查询，不是SELECT语句，而是被该语句检索出来的结果集，其常见特性如下：  
1. 能够标记游标为只读
2. 能控制可以执行的定向操作（向前，向后，第一，最后，绝对位置，相对位置）
3. 能标记某些列为可编辑，某些列不可编辑
4. 规定范围，使游标对创建它的特定请求或对所有请求可访问
5. 指示DBMS对检索出的数据进行复制，使数据在游标打开和访问期间不变化  
Access不支持游标，MySQL在版本5上支持，SQLite支持的游标为步骤step  

**创建游标**：  
```
DECLARE CustCursor CURSOR
FOR 
SELECT * FROM Customers
WHERE cust_email IS NULL
```  

**打开游标**：  
```
OPEN CURSOR CustCursor 
```  

**获取游标**：  
```
FETCH CustCursor 
```  

**关闭游标**：  
```
CLOSE CustCursor 
```  

# 第二十二课：高级SQL特性
**约束-主键**：  
DBMS通过在数据表上施加约束来实施引用完整性，主键是一种特殊的约束，没有主键，要安全的UPDATE或DELETE特定行而不影响其他行会非常困难，通过关键字PRIMARY KEY来定义，满足主键的条件有：
1. 任意两行的主键值都不相同
2. 每行都具有一个主键值
3. 包含主键的列从不修改或更新
4. 主键值不能重用，如果删除某行，其主键值不分配给新行  

**约束-外键**：  
外键是表中的一列，其值必须列在另一表的主键中，它是保证引用完整性的极其重要部分，如在Orders的顾客ID列上定义了外键，该列只能接受Customers表的主键值  
```
CREATE TABLE Orders (
order_num 	INT 			NOT NULL,
order_date 	DATETIME	NOT NULL,
cust_id 		CHAR (10) 	NOT NULL,
PRIMARY KEY (
order_num
),
FOREIGN KEY (
cust_id
)
REFERENCES Customers (cust_id)
);
```  
外键的另一个作用是DBMS不允许删除在另一个表中具有关联行的行，如不能删除关联订单的顾客，删除该顾客的唯一方法是首先删除相关的订单，由于需要一系列的删除，因而利用外键可以防止意外删除数据；有的DBMS支持级联删除，如果启用，该特性在从一个表中删除时会删除所有相关的数据  

**唯一约束**：  
用了保证一列中的数据是唯一的，用UNIQUE或CONSTRAINT定义，与主键类似，但有如下区别：  
1. 一个表可以包含多个唯一约束，但主键只有一个
2. 唯一约束的列可包含NULL值
3. 唯一约束的列可修改或更新
4. 唯一约束的列可重复使用
5. 与主键不一样，唯一约束的列不能用来定义外键  

**检查约束**：  
检查约束用来保证一列中的数据满足一组指定的条件，主要有以下几点：  
1. 检查最大最小值
2. 指定范围
3. 只允许特定的值  
数据类型限制了列中可保存的数据的类型，检查约束在数据类型内又做了进一步的限制，这些限制及其重要，可以确保插入数据库的数据准确，不需要依赖于客户端应用或用户来保证正确获取它，DBMS本身将会拒绝无效的数据  
```
quantity	INTEGER 	NOT NULL 	CHECK  (quantity > 0)
ADD CONSTRAINT CHECK (gender LIKE '[MF]')  
```  

**索引**：  
用来排序数据以加快搜索和排序操作的速度，主键数据总是排序的，这是DBMS的工作，按主键检索特定行总是一种快速有效的操作，但是搜索其他列的值效率不高，此时可建立索引  
1. 索引改善检索操作的性能，但降低了插入、修改和删除的性能，在执行这些操作时，DBMS必须动态的更新索引
2. 索引数据可能占据大量的存储空间
3. 并非所有的数据都适合做索引，取值不多的数据不如具有更多可能值的数据更有意义做索引
4. 索引用于数据过滤和排序
5. 可以在索引中定义多个列
6. 过去创建的理想的索引可能经过几个月数据处理后变得不再理想了，最好定期检查进行调整  
```
CREATE INDEX prod_name_idx ON Products (prod_name);
```

**触发器**：  
触发器是特殊的存储过程，在特定的数据库活动发生时自动执行，触发器可以与特定表上的INSERT、UPDATE和DELETE操作相关联；存储过程是简单的存储SQL语句，而触发器与单个表相关联；根据不同的DBMS，触发器可在特定操作执行之前或之后执行，触发器的作用主要有：  
1. 保证数据一致，如INSERT或UPDATE后将所有州名转换为大写
2. 基于某个表的变动在其他表上执行活动，如INSERT或UPDATE后将跟踪记录写入日志
3. 进行额外的验证并根据需要回退数据
4. 计算计算列的值或更新时间戳  

一般来说，约束的处理比触发器快，可能的时候，尽量使用约束  
SQL SERVER的版本对所有的INSERT和UPDATE操作，转换大小写：  
```
CREATE TRIGGER customer_state
ON Customers
FOR INSERT, UPDATE
AS 
UPDATE Customers
SET cust_state=Upper(cust_state)
WHERE Customers.cust_id=inserted.cust_id
```
[回到顶部](#readme)
