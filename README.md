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
            ADD[ COLUMN] 字段定义       -- 增加字段
                AFTER 字段名          -- 表示增加在该字段名后面
                FIRST               -- 表示增加在第一个
            ADD PRIMARY KEY(字段名)   -- 创建主键
            ADD UNIQUE [索引名] (字段名)-- 创建唯一索引
            ADD INDEX [索引名] (字段名) -- 创建普通索引
            DROP[ COLUMN] 字段名      -- 删除字段
            MODIFY[ COLUMN] 字段名 字段属性     -- 支持对字段属性进行修改，不能修改字段名(所有原有属性也需写上)
            CHANGE[ COLUMN] 原字段名 新字段名 字段属性      -- 支持对字段名修改
            DROP PRIMARY KEY    -- 删除主键(删除主键前需删除其AUTO_INCREMENT属性)
            DROP INDEX 索引名 -- 删除索引
            DROP FOREIGN KEY 外键    -- 删除外键
            
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
  DELETE FROM 表名[ 删除条件子句]
        没有条件子句，则会删除全部
  UPDATE 表名 SET 字段名=新值[, 字段名=新值] [更新条件]
  ``` 
  
  
  
