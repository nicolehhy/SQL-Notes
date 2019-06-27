# SQL-Notes
A quick review of SQL query usage. In this section, I summarize SQL startup, database usage, data changes, and many more. Every part will be given `command` and `example`.

===============================================================================
## Windows Service
  * Start MySQL
  ```SQL
  net start mysql
  ```
  * Create Windows Service
  ```SQL
  sc create mysql binPath= mysqld_bin_path  # a space follows the equal sign
  ```
  * Connect and disconnect servers
  ```SQL
  mysql -h address -P port -u username -p pw
  ```
  * Check the status
  ```SQL
  SHOW PROCESSLIST # show which cases are in progress
  SHOW VARIABLES # Display system variable information
  ```
 ## Database operation
  * View the current database
  ```SQL
  SELECT DATABASE();
  ```
  * Display current time, username, database version
  ```SQL
  SELECT now(), user(), version();
  ```
  * Create Database
  ```SQL
  CREATE DATABASE[ IF NOT EXISTS] databse_name 
        CHARACTER SET charset_name
        COLLATE collation_name
  ```
  * View existing libraries
  ```SQL
  SHOW DATABASES[ LIKE 'PATTERN']
  ``` 
  * View the current library information
  ```SQL
  SHOW CREATE DATABASES db_name
  ``` 
  * Modify library options information
  ```SQL
  ALTER DATABASE db_name
    [[DEFAULT] CHARACTER SET charset_name]
    [[DEFAULT] COLLATE collation_name]
  ``` 
  * View existing libraries
  ```Delete database 
  DROP DATABASE[ IF EXISTS] db_name
  ``` 
 ## Table operation
  * Create a table 
  ```SQL
  CREATE [TEMPORARY] TABLE[ IF NOT EXISTS] [Database.]table_name ( definition )[ option]
  [NOT NULL | NULL] [DEFAULT default_value] [AUTO_INCREMENT] [UNIQUE [KEY] | 
  [PRIMARY] KEY] [COMMENT 'string']
  -- table option
  CHARSET = charset_name
  ENGINE = engine_name
  [SHOW ENGINE eg_name {LOGS|STATUS}]
  AUTO_INCREMENT = line_number -- primary key won't be repicated
  DATA DIRECTORY = '目录'
  INDEX DIRECTORY = '目录'
  COMMENT = 'string' -- table comment
  PARTITION BY ...  
  ```
  * Example to create a table
      ```SQL
      CREATE TABLE stu(
      sid INT PRIMARY KEY AUTO_INCREMENT,
      sname	VARCHAR(20),
      age	INT,
      gender	VARCHAR(10)
      );
      ```
  * View all the tables
  ```SQL
  SHOW TABLES[ LIKE 'pattern']
  SHOW TABLES FROM tb_name
  ```
  * View table structure
  ```SQL
 SHOW CREATE TABLE tb_name （informatin is more detailed）
    DESC tb_name / DESCRIBE tb_name / EXPLAIN tb_name / SHOW COLUMNS FROM tb_name [LIKE 'PATTERN']
    SHOW TABLE STATUS [FROM db_name] [LIKE 'pattern']
  ```
  * Modify Table
  ```SQL
  ALTER TABLE tb_name 
        eg: ALTER TABLE tb_name ENGINE=MYISAM;
        RENAME TABLE old_name TO new_name
        RENAME TABLE old_name TO db.new_name （move the table to another database）
        -- operation name
            ADD[ COLUMN] 字段定义       -- add a new column
                AFTER 字段名          -- add a new column that follows specific one
                FIRST               -- add a new column as the first one
            ADD PRIMARY KEY(字段名)   -- create a primary key
            ADD UNIQUE [索引名] (字段名)-- create unique index
            ADD INDEX [索引名] (字段名) -- create regular index
            DROP[ COLUMN] 字段名      -- delete a column
            MODIFY[ COLUMN] 字段名 字段属性     -- 支持对字段属性进行修改，不能修改字段名(所有原有属性也需写上)
            CHANGE[ COLUMN] 原字段名 新字段名 字段属性      -- 支持对字段名修改
            DROP PRIMARY KEY    -- 删除主键(删除主键前需删除其AUTO_INCREMENT属性)
            DROP INDEX 索引名 -- delete the index
            DROP FOREIGN KEY 外键    -- delete the foreign key
            
  -- Delete table
    DROP TABLE[ IF EXISTS] tb_name ...
  -- remove the data in the table
    TRUNCATE [TABLE] tb_name
  -- copy table structure from another table
    CREATE TABLE tb_name LIKE db.tb2_name
  -- copy everything
    CREATE TABLE tb_name [AS] SELECT * FROM db.tb2_name
  -- Check the error
    CHECK TABLE tbl_name [, tbl_name] ... [option] ...
  -- Optimize table
    OPTIMIZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ...
  -- Repair
    REPAIR [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ... [QUICK] [EXTENDED] [USE_FRM]
  -- Analyze 
    ANALYZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ...
  ``` 
   ## Data operation
   * Insert data
  ```SQL
  INSERT INTO table_name (column1, column2, column3, ...)-- 如果要插入的值列表包含所有字段并且顺序一致，则可以省略字段列表。
  VALUES (value1, value2, value3, ...);  -- 可同时插入多条数据记录！
  -- REPLACE 与 INSERT 完全一样，可互换。
  INSERT [INTO] 表名 SET 字段名=值[, 字段名=值, ...];
  --- example
  INSERT INTO tbl_name
    (a,b,c)
  VALUES
    (1,2,3),
    (4,5,6),
    (7,8,9);
  ``` 
  * Check/Delete/Modify the data
  ```SQL 
  SELECT col_name from tb_name;
  DELETE FROM tb_name[ 删除条件子句]
        没有条件子句，则会删除全部
  UPDATE tb_name SET col_name=new_value[, 字段名=新值] [更新条件]
  ``` 
  
  ## Character encoding
  -- MySQL, database, table, field can be set to encode
  -- Data encoding and client encoding do not need to be consistent
  
  * View all the character set <br>
  -- character_set_client (客户端向服务器发送数据时使用的编码| encoding used for the clients sending information to the server) <br>
  -- character_set_results (服务器端将结果返回给客户端所使用的编码| encoding used for the server sending information to the clients) <br>
  -- character_set_connection (连接层编码| encoding used for connection)
  ```SQL
  SHOW VARIABLES LIKE 'character_set_%' -- View all the character set
  SET 变量名 = 变量值
  SET character_set_client = gbk;
  SET character_set_results = gbk;
  SET character_set_connection = gbk;
  SET NAMES GBK; -- （相当于完成以上三个设置| equals to complete the three steps above)
  ```
 
 * Proofreading set
 -- 校对集用以排序
  ```SQL
 SHOW CHARACTER SET [LIKE 'pattern']/SHOW CHARSET [LIKE 'pattern'] 查看所有字符集
 SHOW COLLATION [LIKE 'pattern'] 查看所有校对集 
 CHARSET 字符集编码 设置字符集编码
 COLLATE 校对集编码 设置校对集编码
  ```
  
  ## Data type (column type)
  * Numerical type <br>
  -- a. Int ---------- 
  ```SQL
    Type        Byte     Range（有符号位）
    tinyint     1Byte    -128 ~ 127      无符号位：0 ~ 255 
    smallint    2Byte    -32768 ~ 32767
    mediumint   3Byte    -8388608 ~ 8388607 
    int         4Byte
    bigint      8Byte
    int(M)  M表示总位数
    - 默认存在符号位，unsigned 属性修改
    - 显示宽度，如果某个数不够定义字段时设置的位数，则前面以0补填，zerofill 属性修改
        例：int(5)   插入一个数'123'，补填后为'00123'
    - 在满足要求的情况下，越小越好。
    - 1表示bool值真，0表示bool值假。MySQL没有布尔类型，通过整型0和1表示。常用tinyint(1)表示布尔型。
  ```
  
   -- b. Floating point ----------
  ```SQL
    Type             Byte     Range
    float(single精度)     4字节
    double(double精度)    8字节
    浮点型既支持符号位 unsigned 属性，也支持显示宽度 zerofill 属性。
        不同于整型，前后均会补填0.
    定义浮点型时，需指定总位数和小数位数。
        float(M, D)     double(M, D)
        M表示总位数，D表示小数位数。
        M和D的大小会决定浮点数的范围。不同于整型的固定范围。
        M既表示总位数（不包括小数点和正负号），也表示显示宽度（所有显示符号均包括）。
        支持科学计数法表示。
        浮点数表示近似值。
  ```
   -- c. Fixed point ----------
   ```SQL
    decimal -- 可变长度
    decimal(M, D)   M也表示总位数，D表示小数位数。
    保存一个精确的数值，不会发生数据的改变，不同于浮点数的四舍五入。
    将浮点数转换为字符串来保存，每9位数字保存为4个字节。
  ```
  * String <br>
  -- a. char, varchar ----------
  ```SQL
    char    定长字符串，速度快，但浪费空间
    varchar 变长字符串，速度慢，但节省空间
    M表示能存储的最大长度，此长度是字符数，非字节数。
    不同的编码，所占用的空间不同。
    char,最多255个字符，与编码无关。
    varchar,最多65535字符，与编码有关。
    一条有效记录最大不能超过65535个字节。
        utf8 最大为21844个字符，gbk 最大为32766个字符，latin1 最大为65532个字符
    varchar 是变长的，需要利用存储空间保存 varchar 的长度，如果数据小于255个字节，则采用一个字节来保存长度，反之需要两个字节来保存。
    varchar 的最大有效长度由最大行大小和使用的字符集确定。
    最大有效长度是65532字节，因为在varchar存字符串时，第一个字节是空的，不存在任何数据，然后还需两个字节来存放字符串的长度，所以有效长度是64432-1-2=65532字节。
    例：若一个表定义为 CREATE TABLE tb(c1 int, c2 char(30), c3 varchar(N)) charset=utf8; 问N的最大值是多少？ 答：(65535-1-2-4-30*3)/3
```
-- b. blob, text ----------
```sql
    blob 二进制字符串（字节字符串）
        tinyblob, blob, mediumblob, longblob
    text 非二进制字符串（字符字符串）
        tinytext, text, mediumtext, longtext
    text 在定义时，不需要定义长度，也不会计算总长度。
    text 类型在定义时，不可给default值
```
-- c. binary, varbinary ----------
```SQL
    类似于char和varchar，用于保存二进制字符串，也就是保存字节字符串而非字符字符串。
    char, varchar, text 对应 binary, varbinary, blob.
```
* Time
```SQL
一般用整型保存时间戳，因为PHP可以很方便的将时间戳进行格式化。
    datetime    8字节    日期及时间     1000-01-01 00:00:00 到 9999-12-31 23:59:59
    date        3字节    日期         1000-01-01 到 9999-12-31
    timestamp   4字节    时间戳        19700101000000 到 2038-01-19 03:14:07
    time        3字节    时间         -838:59:59 到 838:59:59
    year        1字节    年份         1901 - 2155
datetime    YYYY-MM-DD hh:mm:ss
timestamp   YY-MM-DD hh:mm:ss
            YYYYMMDDhhmmss
            YYMMDDhhmmss
            YYYYMMDDhhmmss
            YYMMDDhhmmss
date        YYYY-MM-DD
            YY-MM-DD
            YYYYMMDD
            YYMMDD
            YYYYMMDD
            YYMMDD
time        hh:mm:ss
            hhmmss
            hhmmss
year        YYYY
            YY
            YYYY
            YY
```

* Enumeration and Collection

-- 枚举(enum) ----------
```SQL
enum(val1, val2, val3...)
    在已知的值中进行单选。最大数量为65535.
    枚举值在保存时，以2个字节的整型(smallint)保存。每个枚举值，按保存的位置顺序，从1开始逐一递增。
    表现为字符串类型，存储却是整型。
    NULL值的索引是NULL。
    空字符串错误值的索引值是0。
```

-- 集合（set） ----------
```sql
set(val1, val2, val3...)
    create table tab ( gender set('男', '女', '无') );
    insert into tab values ('男, 女');
    最多可以有64个不同的成员。以bigint存储，共8个字节。采取位运算的形式。
    当创建表时，SET成员值的尾部空格将自动被删除。
```

## Column attributes
* PRIMARY 主键
```SQL
    - 能唯一标识记录的字段，可以作为主键。
    - 一个表只能有一个主键。
    - 主键具有唯一性。
    - 声明字段时，用 primary key 标识。
        也可以在字段列表之后声明
            例：create table tab ( id int, stu varchar(10), primary key (id));
    - 主键字段的值不能为null。
    - 主键可以由多个字段共同组成。此时需要在字段列表后声明的方法。
        例：create table tab ( id int, stu varchar(10), age int, primary key (stu, age));
```
* UNIQUE 唯一索引（唯一约束）<br>
    使得某字段的值也不能重复。
   
* NULL 约束
```SQL
    null不是数据类型，是列的一个属性。
    表示当前列是否可以为null，表示什么都没有。
    null, 允许为空。默认。
    not null, 不允许为空。
    insert into tab values (null, 'val');
        -- 此时表示将第一个字段的值设为null, 取决于该字段是否允许为null
```        
* DEFAULT 默认值属性
```SQL
    当前字段的默认值。
    insert into tab values (default, 'val');    -- 此时表示强制使用默认值。
    create table tab ( add_time timestamp default current_timestamp );
        -- 表示将当前时间的时间戳设为默认值。
        current_date, current_time
```         
* AUTO_INCREMENT 自动增长约束
```SQL
    自动增长必须为索引（主键或unique）
    只能存在一个字段为自动增长。
    默认为1开始自动增长。可以通过表属性 auto_increment = x进行设置，或 alter table tbl auto_increment = x;
```     
* COMMENT 注释 <br>
    例：
    ```SQL
    -- Comment table
    create table tab ( id int ) comment '注释内容'; 
    -- Comment column    
        create table test( 
        id int not null default 0 comment '用户id' ) 
    -- Comment table and column 
        create table test1 ( 
        field_name int comment '字段的注释' 
        )comment='表的注释'; 
    ```    
* FOREIGN KEY 外键约束 <br>
1. The FOREIGN KEY constraint is used to prevent actions that would destroy links between tables. <br>
2. The FOREIGN KEY constraint also prevents invalid data from being inserted into the foreign key column, <br>
   because it has to be one of the values contained in the table it points to. <br>
  * Example in `Create`: <br>
-- The following SQL 'CREATES' a FOREIGN KEY on the "PersonID" column when the "Orders" table is created:
    * MySQL
    ```SQL
    CREATE TABLE Orders (
    OrderID int NOT NULL,
    OrderNumber int NOT NULL,
    PersonID int,
    PRIMARY KEY (OrderID),
    FOREIGN KEY (PersonID) REFERENCES Persons(PersonID)
    );
    ```
    * SQL Server / Oracle / MS Access
    ```SQL
    CREATE TABLE Orders (
    OrderID int NOT NULL PRIMARY KEY,
    OrderNumber int NOT NULL,
    PersonID int FOREIGN KEY REFERENCES Persons(PersonID)
    );
    ```
  * Example in `Creating Constraint`: <br>
-- To allow naming of a FOREIGN KEY constraint, and for defining a FOREIGN KEY constraint on multiple columns <br>
     * MySQL / SQL Server / Oracle / MS Access
    ```SQL
    CREATE TABLE Orders (
    OrderID int NOT NULL,
    OrderNumber int NOT NULL,
    PersonID int,
    PRIMARY KEY (OrderID),
    CONSTRAINT FK_PersonOrder FOREIGN KEY (PersonID)
    REFERENCES Persons(PersonID)
    );
    ```
    <br>
  * Example in `Alter`: <br>
-- To create a FOREIGN KEY constraint on the "PersonID" column when the "Orders" table `is already created`  <br>
    * MySQL/ SQL Server / Oracle / MS Access:
    ```SQL
    ALTER TABLE Orders
    ADD FOREIGN KEY (PersonID) REFERENCES Persons(PersonID);
    ```
    * MySQL / SQL Server / Oracle / MS Access to allow naming of a FOREIGN KEY constraint:
    ```SQL
    ALTER TABLE Orders
    ADD CONSTRAINT FK_PersonOrder
    FOREIGN KEY (PersonID) REFERENCES Persons(PersonID);
    ```
   * Example in `Drop`: <br>
-- To drop a FOREIGN KEY constraint, use the following SQL: <br>
     * MySQL
    ```SQL
    ALTER TABLE Orders 
    DROP FOREIGN KEY FK_PersonOrder;
    ```
     * SQL Server / Oracle / MS Access
    ```SQL
    ALTER TABLE Orders
    DROP CONSTRAINT FK_PersonOrder;
    ```
  * Summary: <br>
   ```SQL
    -- 将表t1的t1_id外键关联到表t2的id字段。
    -- 每个外键都有一个名字，可以通过 constraint 指定
    存在外键的表，称之为从表（子表），外键指向的表，称之为主表（父表）。
    作用：保持数据一致性，完整性，主要目的是控制存储在外键表（从表）中的数据。
    MySQL中，可以对InnoDB引擎使用外键约束：
    语法：
    foreign key (外键字段） references 主表名 (关联字段) [主表记录删除时的动作] [主表记录更新时的动作]
    此时需要检测一个从表的外键需要约束为主表的已存在的值。外键在没有关联的情况下，可以设置为null.前提是该外键列，没有not null。
    可以不指定主表记录更改或更新时的动作，那么此时主表的操作被拒绝。
    
    如果指定了 on update 或 on delete：在删除或更新时，有如下几个操作可以选择：
  --  1. cascade，级联操作------ 
    主表数据被更新（主键值更新），从表也被更新（外键值更新）。主表记录被删除，从表相关记录也被删除。
   --   2. set null，设置为null------ 
   主表数据被更新（主键值更新），从表的外键被设置为null。主表记录被删除，从表相关记录外键被设置成null。但注意，要求该外键列，没有not null属性约束。
    -- 3. restrict，拒绝父表删除和更新------ 
    注意，外键只被InnoDB存储引擎所支持。其他引擎是不支持的。
 ```
 
 ## Select 
 SELECT [ALL|DISTINCT] select_expr FROM -> WHERE -> GROUP BY [合计函数] -> HAVING -> ORDER BY -> LIMIT <br>
* select_expr
```sql
    -- 可以用 * 表示所有字段。
        select * from tb;
    -- 可以使用表达式（计算公式、函数调用、字段也是个表达式）
        select stu, 29+25, now() from tb;
    -- 可以为每个列使用别名。适用于简化列标识，避免多个列标识符重复。
        - 使用 as 关键字，也可省略 as.
        select stu+10 as add10 from tb;
```        
* FROM 子句
```sql
    用于标识查询来源。
    -- 可以为表起别名。使用as关键字。
        SELECT * FROM tb1 AS tt, tb2 AS bb;
    -- from子句后，可以同时出现多个表。
        -- 多个表会横向叠加到一起，而数据会形成一个笛卡尔积。
        SELECT * FROM tb1, tb2;
    -- 向优化符提示如何选择索引
        USE INDEX、IGNORE INDEX、FORCE INDEX
        SELECT * FROM table1 USE INDEX (key1,key2) WHERE key1=1 AND key2=2 AND key3=3;
        SELECT * FROM table1 IGNORE INDEX (key3) WHERE key1=1 AND key2=2 AND key3=3;
```         
* WHERE 子句
```sql
    -- 从from获得的数据源中进行筛选。
    -- 整型1表示真，0表示假。
    -- 表达式由运算符和运算数组成。
        -- 运算数：变量（字段）、值、函数返回值
        -- 运算符：
            =, <=>, <>, !=, <=, <, >=, >, !, &&, ||,
            in (not) null, (not) like, (not) in, (not) between and, is (not), and, or, not, xor
            is/is not 加上ture/false/unknown，检验某个值的真假
            <=>与<>功能相同，<=>可用于null比较
```              
* GROUP BY 子句, 分组子句
```sql
    GROUP BY 字段/别名 [排序方式]
    分组后会进行排序。升序：ASC，降序：DESC
    以下[合计函数]需配合 GROUP BY 使用：
    count 返回不同的非NULL值数目  count(*)、count(字段)
    sum 求和
    max 求最大值
    min 求最小值
    avg 求平均值
    group_concat 返回带有来自一个组的连接的非NULL值的字符串结果。组内字符串连接。
```
* HAVING 子句，条件子句
```sql
    与 where 功能、用法相同，执行时机不同。
    where 在开始时执行检测数据，对原数据进行过滤。
    having 对筛选出的结果再次进行过滤。
    having 字段必须是查询出来的，where 字段必须是数据表存在的。
    where 不可以使用字段的别名，having 可以。因为执行WHERE代码时，可能尚未确定列值。
    where 不可以使用合计函数。一般需用合计函数才会用 having
    SQL标准要求HAVING必须引用GROUP BY子句中的列或用于合计函数中的列。
```
* ORDER BY 子句，排序子句
```sql
    order by 排序字段/别名 排序方式 [,排序字段/别名 排序方式]...
    升序：ASC，降序：DESC
    支持多个字段的排序。
```    
* LIMIT 子句，限制结果数量子句
```sql
    仅对处理好的结果进行数量限制。将处理好的结果的看作是一个集合，按照记录出现的顺序，索引从0开始。
    limit 起始位置, 获取条数
    省略第一个参数，表示从索引0开始。limit 获取条数
    支持多个字段的排序。
```  
* DISTINCT, ALL 选项
```sql
    distinct 去除重复记录
    默认为 all, 全部记录
```
## UNION
* Combine all the results using `SELECT` multiple times 
``` SQL 
SELECT ... UNION [ALL|DISTINCT] SELECT ...
-- Example:
SELECT City, Country FROM Customers
WHERE Country='Germany'
UNION ALL
SELECT City, Country FROM Suppliers
WHERE Country='Germany'
ORDER BY City;
``` 
* Tips
```SQL
1. 默认 DISTINCT 方式，即所有返回的行都是唯一的
 
2. 建议，对每个SELECT查询加上小括号包裹。
 
3. ORDER BY 排序时，需加上 LIMIT 进行结合。
 
4. 需要各select查询的字段数量一样。
 
5. 每个select查询的字段列表(数量、类型)应一致，因为结果中的字段名以第一条select语句为准。
```

## Subquery
- 子查询需用括号包裹。 
* From 型
from后要求是一个表，必须给子查询结果取个别名。from型需将结果生成一个临时表格，可用以原表的锁定的释放。
```SQL 
SELECT A.user_id, A.city
FROM(
SELECT operation_id as user_id，city
FROM Order
) A
WHERE A.user_id is not null;
```
```SQL
select * from (select * from tb where id>0) as subfrom where id>1;
``` 
* Where 型 
子查询返回一个值，标量子查询。不需要给子查询取别名。where子查询内的表，不能直接用以更新。
```SQL 
select * from tb where money = (select max(money) from tb);
``` 
* Column 型查询
如果子查询结果返回的是一列。使用 in 或 not in 完成查询，exists 和 not exists 条件。<br>
如果子查询返回数据，则返回1或0。常用于判断条件。
```SQL 
select column1 from t1 where exists (select * from t2);
``` 
* Row 型查询 
查询条件是一个行。
```SQL 
select * from t1 where (id, gender) in (select id, gender from t2);
``` 
行构造符：(col1, col2, ...) 或 ROW(col1, col2, ...) <br>
行构造符通常用于与对能返回两个或两个以上列的子查询进行比较。
 
* Special Sign for Calculation 特殊运算符
```SQL 
!= all() 相当于 not in 
= some() 相当于 in。any 是 some 的别名 
!= some() 不等同于 not in，不等于其中某一个。 
all, some 可以配合其他运算符一起使用。
```
## Join 
Here are the different types of the JOINs in SQL: <br>

-- 1. (INNER) JOIN: Returns records that have matching values in both tables <br>
```SQL
The following SQL statement selects all orders with customer and shipper information:
SELECT Orders.OrderID, Customers.CustomerName, Shippers.ShipperName
FROM ((Orders
INNER JOIN Customers ON Orders.CustomerID = Customers.CustomerID)
INNER JOIN Shippers ON Orders.ShipperID = Shippers.ShipperID);
```
-- 2. LEFT (OUTER) JOIN: Returns all records from the left table, and the matched records from the right table <br>
-- 3. RIGHT (OUTER) JOIN: Returns all records from the right table, and the matched records from the left table <br>
-- 4. FULL (OUTER) JOIN: Returns all records when there is a match in either left or right table <br>
-- 5. SELF JOIN : A self JOIN is a regular join, but the table is joined with itself <br>
```SQL
The following SQL statement matches customers that are from the same city:
SELECT A.CustomerName AS CustomerName1, B.CustomerName AS CustomerName2, A.City
FROM Customers A, Customers B
WHERE A.CustomerID <> B.CustomerID
AND A.City = B.City 
ORDER BY A.City;
```
-- 自然连接(natural join) <br>
```SQL
    自动判断连接条件完成连接。
    相当于省略了using，会自动查找相同字段名。
    natural join
    natural left join
    natural right join

select info.id, info.name, info.stu_num, extra_info.hobby, extra_info.sex from info, extra_info where info.stu_num = extra_info.stu_id;
```

## Insert
```SQL
select语句获得的数据可以用insert插入。
可以省略对列的指定，要求 values () 括号内，提供给了按照列顺序出现的所有字段的值。
    或者使用set语法。
    INSERT INTO tbl_name SET field=value,...；
可以一次性使用多个值，采用(), (), ();的形式。
    INSERT INTO tbl_name VALUES (), (), ();
可以在列值指定时，使用表达式。
    INSERT INTO tbl_name VALUES (field_value, 10+10, now());
可以使用一个特殊值 DEFAULT，表示该列使用默认值。
    INSERT INTO tbl_name VALUES (field_value, DEFAULT);
可以通过一个查询的结果，作为需要插入的值。
    INSERT INTO tbl_name SELECT ...;
可以指定在插入的值出现主键（或唯一索引）冲突时，更新其他非主键列的信息。
    INSERT INTO tbl_name VALUES/SET/SELECT ON DUPLICATE KEY UPDATE 字段=值, …;
```

## Delete
```SQL
DELETE FROM tbl_name [WHERE where_definition] [ORDER BY ...] [LIMIT row_count]
按照条件删除。where
指定删除的最多记录数。limit
可以通过排序条件删除。order by + limit
支持多表删除，使用类似连接语法。
delete from 需要删除数据多表1，表2 using 表连接操作 条件。
```

## Truncate
```SQL
TRUNCATE [TABLE] tbl_name
清空数据
删除重建表
区别：
1，truncate 是删除表再创建，delete 是逐条删除
2，truncate 重置auto_increment的值。而delete不会
3，truncate 不知道删除了几条，而delete知道。
4，当被用于带分区的表时，truncate 会保留分区
```
