#MySQL语法

1. MySQL运算符

    * 比较运算符
        
        运算符 | 说明 
        -------- | ---------
        <       |    小于
        <=      |    小于等于
        =       |    等于
        IN      |    在可枚举的结合内
        != 或 <> | 不等于
        >=       | 大于等于
        >        |  大于
        BETWEEN  |  在某个范围内
    
    * 逻辑运算符
        
        运算符 |   说明
        -------- | --------
        NOT 或 ！ |  非
        OR 或 \|\|  |  或
        AND 或 &   | 与


2. [MySQL简单使用][mysql-base-use]
3. 数据库修改的注意点

    1. 数字可加可不加单引号，字符串必须加
    2. rows 和 values 必须要严格对应上，即使在简写的INSERT语句中，自增的主键也要给value
4. 查询别名

        SELECT goods.goods_id AS '商品名称', goods.goods_name AS '商品名称', 
        goods.shop_price - goods.market_price AS '便宜' FROM goods;
5. 查询的时候，完全可以把对应的列名作为变量使用

        1.SELECT goods.goods_id, goods.goods_name, goods.goods_number * 100 AS '数
        量 * 100' FROM goods;
        2.SELECT goods.goods_id, substr(goods.goods_name, 1, 4) AS '限定名字' FROM 
        goods; # substr函数在mysql中是从1开始的
        3.SELECT goods.goods_id, concat(goods.goods_name,'周伟克') AS '字符串拼接' 
        FROM goods;
        4.UPDATE goods SET goods.shop_price = goods.shop_price + 5;
        
6. 占位符`%`、`_`

        # 查询goods_name以诺开头的记录
        SELECT * FROM goods WHERE goods.goods_name LIKE '诺%';
        # 查询goods_name以电器结尾的记录
        SELECT * FROM goods WHERE goods.goods_name LIKE '%电器';
        # 查询goods_name包含电器的记录
        SELECT * FROM goods WHERE goods.goods_name LIKE '%电器%';
        # _的数量即表示了对应字符的数量
        # 查询goods_name以诺基亚开头，切后面有两个字符的记录
        SELECT * FROM goods WHERE goods.goods_name LIKE '诺基亚__';
        
7. 稍复杂SELECT语句

        SELECT goods_id, goods_name, click_count,shop_price FROM goods WHERE 
        (shop_price <10 OR shop_price > 100) AND click_count = 119;

8. MySQL默认是开启`严格模式的`，可通过`select @@sql_mode`查看MySQL当前模式，`set global SQL_MODE="NO_ENGINE_SUBSTITUTION"`修改为`松散模式`，`set global SQL_MODE="NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES"`开启`严格模式`。
    `严格模式`的部分应用场景:
    
    * 不支持对not null字段插入null值
    * 不支持对自增长字段插入”值
    * 不支持text字段有默认值

9. MySQL函数[^function]`MAX, MIN, COUNT, SUM, AVG,COUNT`的一条使用注意

        SELECT goods_id, max(goods.shop_price) FROM goods;
        SELECT * FROM goods WHERE shop_price < (SELECT avg(shop_price) FROM 
        goods);
        # max(goods.shop_price)是正确的，但是goods.goods_id是不对应的，另外几个内置函数也
        是如此，就像取一些人的平均身高avg(height),那么得出平均身高，取谁的名字呢，取谁的都不合
        适。在oracle和sql server 这种sql代码是会触发语法错误的，但是mysql是可以的，mysql在
        自然排序下，取第一次的出现的goods.goods_id
        
        SELECT COUNT(goods_id) FROM goods;
        # 返回指定列的值的数目，null不计入
        SELECT count(*) FROM goods;
        # 返回所有的记录数量
        SELECT count(DISTINCT cat_id) FROM goods;
        # 返回指定列不同值得列数
10. group_by [group_by介绍w3c][group_by]
    > <span style='font-size: 14px'>GROUP BY 语句用于结合合计函数，根据一个或多个列对结果集进行分组</span>  
        
        SELECT cat_id AS '分类', sum(goods_number) FROM goods GROUP BY cat_id;
        # 查询每个分类下，商品的总数
11. HAVING筛选 
        
        # 查询比市场价便宜20的商品
        SELECT goods.goods_id,goods.goods_name,(goods.shop_price - 
        goods.market_price) FROM goods WHERE (goods.shop_price - 
        goods.market_price) > 20;
        # (goods.shop_price - goods.market_price)出现了两次，浪费
        
        
    布尔教育的介绍：HAVING把结果集当成表，做进一步筛选
        
        SELECT goods.goods_id,goods.goods_name,(goods.shop_price - 
        goods.market_price) AS 'less' FROM goods HAVING less > 20;
        
    [W3C介绍][having]: 
    > <span style='font-size: 12px'>在 SQL 中增加 HAVING 子句原因是，WHERE 关键字无法与
    合计函数一起使用 SELECT column_name, aggregate_function(column_name)
    FROM table_name WHERE column_name operator value GROUP BY column_name
    HAVING aggregate_function(column_name) operator value</span>
        
        SELECT cat_id AS '分类', sum(goods_number) FROM goods GROUP BY cat_id 
        HAVING sum(goods_number) > 1000;
        # 查询商品总数量大于1000的分类
        
12. 控制大小写的输出

        SELECT UCASE(column_name) FROM table_name
        SELECT LCASE(column_name) FROM table_name
        
13. 字符串截取`SUBSTR(COLUMN,POSITION,LENGTH)`,`MID(COLUMN,POSITION,LENGTH)`

        SELECT mid(goods_name, 1, 2) FROM goods;
        SELECT SUBSTR(goods_name, 1, 2) FROM goods;
14. ROUND

        控制小数点后面的位数，四舍五入
        SELECT goods.goods_name as '商品名称', round(shop_price, 1) FROM goods;
15. JOIN
    > <span style="font-size: 14px">用于根据两个或多个表中的列之间的关系，从这些表中查询数据</span>
    
    * INNER JOIN

            SELECT column_name(s)
            FROM table_name1
            INNER JOIN table_name2 
            ON 
            table_name1.column_name=table_name2.column_name
    `column_name`两表均能匹配
    
    * LEFT JOIN

            SELECT column_name(s)
            FROM table_name1
            LEFT JOIN table_name2 
            ON 
            table_name1.column_name=table_name2.column_name
        `column_name` 返回左表所有的行，即使右表不匹配
        
    * RIGHT JOIN

            SELECT column_name(s)
            FROM table_name1
            RIGHT JOIN table_name2 
            ON 
            table_name1.column_name=table_name2.column_name
        `column_name` 返回右表所有行，即使左表不匹配
        
    * FULL JOIN

            SELECT column_name(s)
            FROM table_name1
            FULL JOIN table_name2 
            ON
            table_name1.column_name=table_name2.column_name
        `column_name` 只要任一一个表匹配，即可返回行。关键字会从左表 (Persons) 和右表 (Orders) 那里返回所有的行。如果 "Persons" 中的行在表 "Orders" 中没有匹配，或者如果 "Orders" 中的行在表 "Persons" 中没有匹配，这些行同样会列出。
        
16. UNION
    > <span style="font-size: 14px">UNION 操作符用于合并两个或多个 SELECT 语句的结果集. 请注意，UNION 内部的 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。~~同时，每条 SELECT 语句中的列的顺序必须相同</span>  
    
    
    > <span style='font-size: 14px'>UNION 结果集中的列名总是等于 UNION 中第一个 SELECT 语句中的列名,所以查询的顺序必须相同，否则会出现value和row 错乱的情况，即总是对应到第一个select语句</span> 
    
        SELECT E_ID, E_Name FROM 
        Employees_China
        UNION
        SELECT E_Name, E_ID FROM 
        Employees_USA     
    ![union_orderError_pic]
    
    `UNION`自动过滤完全相同的结果，`UNION ALL`不过滤
            
17. UNIQUE: 约束的别名可加可不加，通过`alert table…………`， 关键字`CONSTRAINT`可加可不加
    * 针对列做约束
            
            tid INT UNIQUE
            #OR
            tid INT,
            UNIQUE (tid)
            UNIQUE id(tid)
            CONSTRAINT _id UNIQUE (tid)
            #OR
            ALTER TABLE t2 ADD UNIQUE (tid) # 列名不能用表
            ALTER TABLE t5 ADD UNIQUE id(tid)
            ALTER TABLE t4 ADD CONSTRAINT UNIQUE id (tid)
            名修饰
            # 删除约束
            ALTER TABLE t1 DROP INDEX t1.tid
            ALTER TABLE t1 DROP INDEX id # id约束别名
    * 组合列做修饰
        
            tid INT,
            tname varchar(20)
            UNIQUE id_name(tid,tname)
            CONSTRAINT uc_PersonID UNIQUE 
            (Id_P,LastName)
            # OR
            ALTER TABLE t1 ADD CONSTRAINT 
            UNIQUE (tid, tname)
            ALTER TABLE t1 ADD CONSTRAINT
             id_name UNIQUE 
            (tid,tname)
            ALTER TABLE t1 ADD UNIQUE id_name
             (tid, tname)
            # 删除约束
            ALTER TABLE t1 DROP INDEX id_name
            
18. PRIMAY KEY的添加方式和`UNIQUE`类似，只不过把`UNIQUE` -> `PRIMAY KEY`, 删除方式为：

        ALTER TABLE Persons DROP PRIMARY KEY
        
19. FOREIGN KEY约束： 
    * 用于预防破坏表之间连接的动作
    * 防止非法数据插入外键列，因为它必须是它指向的那个表中的值之一,如果外键对应的值不存在，那么无法插入
    * (个人尝试似乎外键不起别名无法删除啊, 所以外键的创建方式只用下面俩种，虽然[w3c][w3c_foreignkey]介绍有多种创建方式)

            CREATE TABLE Orders
            (
            Id_O int NOT NULL PRIMARY KEY,
            OrderNo int NOT NULL,
            Id_P int FOREIGN KEY REFERENCES Persons(Id_P)
            # 外键别名
            CONSTRAINT fk_PerOrders FOREIGN KEY (Id_P)
            REFERENCES Persons(Id_P)
            )
            
            INSERT INTO Orders VALUES (5,2222,99)
            # Id_P 在Persons中不存在，所以无法插入
            
            
            ALTER TABLE Orders ADD CONSTRAINT fk_PerOrders
            FOREIGN KEY (Id_P) REFERENCES Persons(Id_P)
            
            ALTER TABLE Orders DROP FOREIGN KEY fk_PerOrders            
   
    
子句的查询陷阱

><span style="font-size: 15px;">查询每个栏目下最新的产品(goods_id 最大即为最新) 要求: 只出现 1 次 select 查询</span>

* 常见错误1：虽然能查出内容,但是语句是有问题的

```
select max(goods_id),goods_name,shop_price,market_price,cat_id from goods group by cat_id;
#取出的每行的内容是不正确的,max(goods_id)正确,
#但是对应的goods_name,shop_price,market_price确实不对的;
#它取出的是,按照cat_id分组排序后,第一个出现行的#goods_name,shop_price,market_price
```

* 常见错误2：先按照goods_id排序再分组

```
select max(goods_id),goods_name,cat_id from goods order by goods_id desc group by cat_id;
#直接报错，where,group by,having,order by,limit 
#五种子句有严格的顺序，必须要按照如上顺序
```
* WHERE子查询

```
SELECT goods_id, goods_name,cat_id FROM goods WHERE goods_id in (SELECT max(goods_id) FROM goods GROUP BY cat_id);
#  (SELECT max(goods_id) FROM goods GROUP BY cat_id) 按照cat_id分组，并且取每组中goods_id最大的goods_id组成集合 只能取goods_id的结果集，不能包括其他字段
# 查询goods_id, goods_name,cat_id， 并且goods_id在集合(SELECT max(goods_id) FROM goods GROUP BY cat_id)中
```
* FROM子查询

```
SELECT goods_id,goods_name,cat_id FROM
 (SELECT goods_id,goods_name,cat_id FROM goods ORDER BY cat_id ASC, goods_id DESC) as temp
  GROUP BY cat_id;
#将子查询 (SELECT goods_id,goods_name,cat_id FROM goods ORDER BY cat_id ASC, goods_id DESC)结果记录形成一个临时表Temp(AS Temp 不能少，否则报错)，再从Temp中进行查询操作
```

* EXISTS子查询

><span style="font-size: 15px;">查询有商品的栏目</span>

```
SELECT * FROM category WHERE 
exists(SELECT * FROM goods WHERE goods.cat_id = category.cat_id)
#goods.cat_id,category.cat_id前缀防止冲突
```

多表联查

* 内连接插叙
    
><span style="font-size: 15px;">商品及其分类信息</span>

```
SELECT goods_id,goods_name,goods.cat_id,cat_name FROM goods JOIN 
category on goods.cat_id = category.cat_id;
#JOIN TABLE ON CONDITION 搭配
```

* 左连接 LEFT JOIN TABLE ON

```
SELECT goods_id,goods_name,goods.cat_id,cat_name FROM goods 
LEFT JOIN category on goods.cat_id = category.cat_id;

#以左表为准(这条MySQL中goods)为准，将左表的数据全部获取，并且和右表进行配对，右表返回的数据可能为null。 这条MySQL中，对于goods中category不存在的category_id，相关记录category有关字段返回null
```

* 右连接 RIGHT JOIN TABLE ON 

```
SELECT goods_id, goods_name,goods.cat_id,cat_name FROM goods RIGHT JOIN category 
on goods.cat_id = category.cat_id;
# 和左连接相反，以右表category为准，获取category中所有的数据，对于category中goods不存在的cat_id，相关记录goods有关字段返回null

```

UNION
作用：把N(N >= 2)SQL查询的结果合并，SQL1 获取记录N条，SQL2  获取记录M条，UNION 之后 总结果集M + N条 (M  N  中没有完全重复的)

><span style="font-size: 15px;">商品及其分类信息</span>

```
SELECT goods_id, goods_name,cat_id FROM goods WHERE cat_id = 2
UNION
SELECT goods_id,goods_name, cat_id FROM goods WHERE cat_id = 3
#SQL 查询语句列名可以不一样(不一样显示第一条的)，但是列数必须相同
#UNION 会有去重，存在对比操作，比较耗时
#UNION ALL 不会去重，不对比，比较不耗时
```
```
(SELECT goods_id, goods_name,cat_id FROM goods WHERE cat_id = 3 ORDER BY goods_id ASC ) 
UNION ALL
(SELECT goods_id,goods_name, cat_id FROM goods WHERE cat_id = 3 ORDER BY goods_id ASC )
# UNION 每个SQL语句中加入排序是没有效果的(不加“()”提示错误)，因为UNION会自己对结果集再次排序
```
```

(SELECT goods_id, goods_name,cat_id FROM goods WHERE cat_id = 3 )
UNION ALL
(SELECT goods_id,goods_name, cat_id FROM goods WHERE cat_id = 3 ) ORDER BY goods_id ASC ;
# 对UNION结果集进行排序操作是有效的
```

UPDATE

```
update 表名 set 列1 = 新值1, 列2 = 新值2 where expr
```
处理查询结果，并将相对应的记录更新为处理后的数据

```
UPDATE goods SET goods_name = concat('zhou', substring(goods_name, 2)) WHERE goods_name LIKE '香%';
```

`WHERE` 谓词有一种特殊情况，允许字段A为null，并且插入的时候并没有给A赋值(即这条记录A=null)，如果使用以下查询,查询记录数量均为0，A=null 或者 A!=null 的记录都不能查询出

```
SELECT * FROM Temp WHERE name = NULL;  #无记录
SELECT * FROM Temp WHERE name != NULL;  #无记录
```
对于NULL字段有特有的谓词`IS NULL` `IS NOT NULL`

```
SELECT * FROM Temp WHERE name IS NULL;
SELECT * FROM Temp WHERE name IS NOT NULL;
```

[^function]: http://www.w3school.com.cn/sql/sql_functions.asp
[mysql-base-use]: http://www.zhimengzhe.com/shujuku/MySQL/296006.html 'MySQL基础练习'
[having]: http://www.w3school.com.cn/sql/sql_having.asp 'having介绍'
[group_by]: http://www.w3school.com.cn/sql/sql_groupby.asp 'group_by介绍'
[w3c_union]: http://www.w3school.com.cn/sql/sql_union.asp 'union介绍'
[union_orderError_pic]: https://github.com/neverstoplearn/Note/blob/master/ImgsForMySQL/Img_8.png?raw=true 'union插叙顺序错误情况'
[w3c_foreignkey]: http://www.w3school.com.cn/sql/sql_foreignkey.asp 'w3c关于外键的介绍'


