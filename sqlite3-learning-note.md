# SQLite3 学习笔记

## 从终端进入 sqlite3

```shell
hwte@sw db % sqlite3 
SQLite version 3.39.5 2022-10-14 20:58:05
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database.
sqlite>
```



## 查看可用的`点命令`清单

```shell
sqlite> .help
.auth ON|OFF             Show authorizer callbacks
.backup ?DB? FILE        Backup DB (default "main") to FILE
.bail on|off             Stop after hitting an error.  Default OFF
.binary on|off           Turn binary output on or off.  Default OFF
.cd DIRECTORY            Change the working directory to DIRECTORY
.changes on|off          Show number of rows changed by SQL
.check GLOB              Fail if output since .testcase does not match
.clone NEWDB             Clone data into NEWDB from the existing database
.connection [close] [#]  Open or close an auxiliary database connection
.databases               List names and files of attached databases
.dbconfig ?op? ?val?     List or change sqlite3_db_config() options
.dbinfo ?DB?             Show status information about the database
.dump ?OBJECTS?          Render database content as SQL
.echo on|off             Turn command echo on or off
.eqp on|off|full|...     Enable or disable automatic EXPLAIN QUERY PLAN
.excel                   Display the output of next command in spreadsheet
.exit ?CODE?             Exit this program with return-code CODE
.expert                  EXPERIMENTAL. Suggest indexes for queries
.explain ?on|off|auto?   Change the EXPLAIN formatting mode.  Default: auto
.filectrl CMD ...        Run various sqlite3_file_control() operations
.fullschema ?--indent?   Show schema and the content of sqlite_stat tables
.headers on|off          Turn display of headers on or off
.help ?-all? ?PATTERN?   Show help text for PATTERN
.hex-rekey OLD NEW NEW   Change the encryption key using hexadecimal
.import FILE TABLE       Import data from FILE into TABLE
.import FILE TABLE       Import data from FILE into TABLE
.imposter INDEX TABLE    Create imposter table TABLE on index INDEX
.indexes ?TABLE?         Show names of indexes
.limit ?LIMIT? ?VAL?     Display or change the value of an SQLITE_LIMIT
.lint OPTIONS            Report potential schema issues.
.log FILE|off            Turn logging on or off.  FILE can be stderr/stdout
.mode MODE ?OPTIONS?     Set output mode
.nonce STRING            Suspend safe mode for one command if nonce matches
.nullvalue STRING        Use STRING in place of NULL values
.once ?OPTIONS? ?FILE?   Output for the next SQL command only to FILE
.open ?OPTIONS? ?FILE?   Close existing database and reopen FILE
.output ?FILE?           Send output to FILE or stdout if FILE is omitted
.parameter CMD ...       Manage SQL parameter bindings
.print STRING...         Print literal STRING
.progress N              Invoke progress handler after every N opcodes
.prompt MAIN CONTINUE    Replace the standard prompts
.quit                    Exit this program
.read FILE               Read input from FILE or command output
.rekey OLD NEW NEW     Change the encryption key
.recover                 Recover as much data as possible from corrupt db.
.restore ?DB? FILE       Restore content of DB (default "main") from FILE
.save ?OPTIONS? FILE     Write database to FILE (an alias for .backup ...)
.scanstats on|off        Turn sqlite3_stmt_scanstatus() metrics on or off
.schema ?PATTERN?        Show the CREATE statements matching PATTERN
.selftest ?OPTIONS?      Run tests defined in the SELFTEST table
.separator COL ?ROW?     Change the column and row separators
.session ?NAME? CMD ...  Create or control sessions
.sha3sum ...             Compute a SHA3 hash of database content
.shell CMD ARGS...       Run CMD ARGS... in a system shell
.show                    Show the current values for various settings
.stats ?ARG?             Show stats or turn stats on or off
.system CMD ARGS...      Run CMD ARGS... in a system shell
.tables ?TABLE?          List names of tables matching LIKE pattern TABLE
.testcase NAME           Begin redirecting output to 'testcase-out.txt'
.testctrl CMD ...        Run various sqlite3_test_control() operations
.text-rekey OLD NEW NEW  Change the encryption key using hexadecimal
.timeout MS              Try opening locked tables for MS milliseconds
.timer on|off            Turn SQL timer on or off
.trace ?OPTIONS?         Output each SQL statement as it is run
.vfsinfo ?AUX?           Information about the top-level VFS
.vfslist                 List all available VFSes
.vfsname ?AUX?           Print the name of the VFS stack
.width NUM1 NUM2 ...     Set minimum column widths for columnar output
sqlite> 
```



## 数据库操作

### 查看数据库

```sql
sqlite> .databases
main: "" r/w
```

### 新建数据库

方式一 : 利用命令 `sqlite3 <database_name>`

```sql
hwte@sw db % sqlite3 demo.db
SQLite version 3.39.5 2022-10-14 20:58:05
Enter ".help" for usage hints.

sqlite> .databases
main: /Users/hwte/Desktop/db/demo.db r/w
```

方式二 : 利用命令 `.open` : Close existing database and reopen FILE

```sql
sqlite> .open test.db
sqlite> .databases
main: /Users/hwte/Desktop/db/test.db r/w
```

注1 : 打开已存在的数据库也使用 `.open` 命令，以上命令如果 test.db 存在则直接打开它，反之就创建它 .

注2 : 上述两种方式创建的数据库文件，存放在创建时使用 sqlite3 命令的目录下 : 

```shell
hwte@sw db % ls -la
total 0
drwxr-xr-x   4 hwte  staff   128  6 10 08:13 .
drwx------+ 79 hwte  staff  2528  6 10 08:01 ..
-rw-r--r--   1 hwte  staff     0  6 10 08:13 demo.db
-rw-r--r--   1 hwte  staff     0  6 10 08:02 test.db
```

### ⭐️重新打开数据库

虽然前文中我们在 db 目录下创建了两个数据库，但是我们在该目录下执行 `.databases` 命令后，发现依旧看不到数据库列表 : 

注 : `.databases` :  List names and files of attached databases

```sql
hwte@sw db % sqlite3 
SQLite version 3.39.5 2022-10-14 20:58:05
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database.

sqlite> .databases
main: "" r/w

sqlite>
```

由提示信息 `Use ".open FILENAME" to reopen on a persistent database.` 可知，可通过命令 `.open` 来重新打开，即**重新连接**指定数据库 : 

```sql
sqlite> .open test.db
sqlite> .databases
main: /Users/hwte/Desktop/db/test.db r/w
```

如果想要重新打开，即重新连接**非当前目录**的数据库，可以通过如下方式 : 

```sql
hwte@gl-f1227452macd db % sqlite3 
SQLite version 3.39.5 2022-10-14 20:58:05
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database.

sqlite> .databases
main: "" r/w

sqlite> .open /.../hk/ELO_portrait_ranking/fx_portrait_image6.db

sqlite> .databases
main: /.../hk/ELO_portrait_ranking/fx_portrait_image6.db r/w

sqlite> .tables
IMAGE         IMAGE_VISTOR

sqlite> .schema image
CREATE TABLE IMAGE
        (
            NAME TEXT PRIMARY KEY NOT NULL,
            CONTENT BLOB NOT NULL,
            RANKING INTEGER NOT NULL DEFAULT 1400
        );

sqlite> .schema image_vistor
CREATE TABLE IMAGE_VISTOR
        (
            IP TEXT PRIMARY KEY NOT NULL,
            PK_COUNT INTEGER NOT NULL DEFAULT 0
        );
sqlite>
```

注1 : `.tables ?TABLE?` : List names of tables matching LIKE pattern TABLE

注2 : `.schema ?PATTERN?` : Show the CREATE statements matching PATTERN

### 删除数据库

Like most relational database systems, SQLite does not use the **DROP DATABASE** command to drop a database and there is no special syntax or steps to drop the database in SQLite. You just have to delete the file **manually** :

```shell
hwte@sw db % ls
demo.db	test.db

hwte@sw db % rm test.db

hwte@sw db % ls
demo.db
```



## 表操作

### 查看表

```sql
hwte@gl-f1227452macd db % ls
demo.db	test.db

hwte@gl-f1227452macd db % sqlite3 
SQLite version 3.39.5 2022-10-14 20:58:05
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database.

sqlite> .databases
main: "" r/w

sqlite> .open demo.db

sqlite> .databases
main: /Users/hwte/Desktop/db/demo.db r/w

sqlite> .tables
sqlite>
```

### 表的数据类型

Storage Classes and Datatypes :

Each value stored in an SQLite database (or manipulated by the database engine) has one of the following storage classes:

- **NULL**. The value is a NULL value.
- **INTEGER**. The value is a signed integer, stored in 0, 1, 2, 3, 4, 6, or 8 bytes depending on the magnitude of the value.
- **REAL**. The value is a floating point value, stored as an 8-byte IEEE floating point number.
- **TEXT**. The value is a text string, stored using the database encoding (UTF-8, UTF-16BE or UTF-16LE).
- **BLOB**. The value is a blob of data, stored exactly as it was input.

A storage class is more general than a datatype. The INTEGER storage class, for example, includes 7 different integer datatypes of different lengths. [This makes a difference on disk.](https://www.sqlite.org/fileformat2.html#record_format) But as soon as INTEGER values are read off of disk and into memory for processing, they are converted to the most general datatype (8-byte signed integer). And so for the most part, "storage class" is indistinguishable from "datatype" and the two terms can be used interchangeably.

. . . for more informations please refer to : https://www.sqlite.org/datatype3.html

NOTE : BLOB stands for Binary Large Object. In SQLite, you can use the `BLOB` data type to store binary data such as images, video files, or any raw binary data.

### 新建表

```sql
sqlite> .tables

sqlite> create table table_1 (
   ...> id int primary key not null,
   ...> name text not null,
   ...> salary real not null default 8600.00,
   ...> portrait blob not null
   ...> );

sqlite> .tables
table_1
```

### 查看表结构

```sql
sqlite> .tables
table_1

sqlite> .schema table_1
CREATE TABLE table_1 (
id int primary key not null,
name text not null,
salary real not null default 8600.00,
portrait blob not null
);
```

### 删除表

```sql
sqlite> .tables
table_1

sqlite> drop table table_1;

sqlite> .tables
sqlite>
```



## 常用SQL语句

### insert

```sql
INSERT INTO table_name
(column1, column2, column3,...columnN)
VALUES
(value1, value2, value3,...valueN);
```

### delete

```sql
DELETE FROM table_name
WHERE [condition];
```

### update

```sql
UPDATE table_name
SET column1 = value1, column2 = value2...., columnN = valueN
WHERE [condition];
```

### select

```sql
SELECT column1, column2, columnN FROM table_name;
```

### Order By

```sql
SELECT column-list 
FROM table_name 
WHERE [condition] 
ORDER BY column1, column2, .. columnN [ASC / DESC];
```

- **ASC** 默认值，从小到大，升序排列
- **DESC** 从大到小，降序排列

### Limit

```sql
SELECT column1, column2, columnN 
FROM table_name
LIMIT [count of rows]
```

结合 OFFSET 子句使用 : 返回从第 OFFSET + 1 行开始，条数为 LIMIT 的数据 .

```sql
SELECT column1, column2, columnN 
FROM table_name
LIMIT [count of rows]
OFFSET [row num]
```

### Like

* `%` : 代表零个、一个或多个数字或字符
* `_` : 代表一个单一的数字或字符

```sql
SELECT column_list 
FROM table_name
WHERE column LIKE ['%XXXX%' / '%XXXX' / 'XXXX%' / ...]

or

SELECT column_list 
FROM table_name
WHERE column LIKE ['_XXXX_' / '_XXXX' / 'XXXX_' / ...]

or 

SELECT column_list 
FROM table_name
WHERE column LIKE ['_2%3' / '2___3' / '2_%_%' / ...]
```

### AND/OR

```sql
SELECT column1, column2, columnN
FROM table_name
WHERE [condition1] AND [condition2]...AND [conditionN];
```

```sql
SELECT column1, column2, columnN
FROM table_name
WHERE [condition1] OR [condition2]...OR [conditionN];
```

### Glob

与 LIKE 运算符不同的是，GLOB 是大小写敏感的 .

- `*` : 匹配零个、一个或多个数字或字符
- `?` : 匹配一个的数字或字符
- `[...]` : 匹配方括号内指定的字符集中的任何一个字符
- `[^...]` : 匹配不在方括号内指定的字符集中的任何字符

```sql
SELECT FROM table_name
WHERE column GLOB '* / ? / [...] / [^...] / ...'
```

### Group By

SQLite 的 **GROUP BY** 子句用于与 SELECT 语句一起使用，来对**相同的数据**进行**分组** .

注 : 在 SELECT 语句中，GROUP BY 子句放在 WHERE 子句之后， ORDER BY 子句之前 .

```sql
SELECT column-list
FROM table_name
WHERE [conditions]
GROUP BY column1, column2....columnN
ORDER BY column1, column2, .. columnN [ASC / DESC];
```

### Having

HAVING 子句允许指定条件来过滤将出现在最终结果中的**分组结果**，

注 : `WHERE 子句`在**所选列**上设置条件，而 `HAVING 子句`则在由 `GROUP BY 子句`创建的**分组**上设置条件 .

```sql
SELECT
FROM
WHERE [conditions]
GROUP BY column1, column2....columnN
HAVING [conditions]
ORDER BY column1, column2, .. columnN [ASC / DESC];
```

### Distinct

SQLite 的 DISTINCT 关键字与 SELECT 语句一起使用，用来消除查询到的重复记录 .

```sql
SELECT DISTINCT column1, column2,.....columnN 
FROM table_name
WHERE [condition]
```



## 常用函数

* **COUNT** : 用来计算一个数据库表中的行数
* **MAX** : 允许我们选择某列的最大值
* **MIN** : 允许我们选择某列的最小值
* **AVG** : 计算某列的平均值
* **SUM** : 允许为一个数值列计算总和
* **RANDOM** : 返回一个介于 -9223372036854775808 和 +9223372036854775807 之间的伪随机整数
* **ABS** : 返回数值参数的绝对值
* **UPPER** : 把字符串转换为大写字母
* **LOWER** : 把字符串转换为小写字母
* **LENGTH** : 返回字符串的长度
* **sqlite_version** : 返回 SQLite 库的版本
