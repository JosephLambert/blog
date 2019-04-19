

#### Mac进入MySQL服务器

1. `PATH="$PATH":/usr/local/mysql/bin;`
2. `mysql -u root -p`使用root账户登陆服务器
3. 输入密码进入服务器即可使用SQL命令行交互
4. `exit`退出服务器

**关于mysqladmin命令**

SQL指令的命令行接口，可以在不登陆服务器的前提下操作数据库，例如：

```shell
[root@host]# mysqladmin -u root -p create DBname
Enter password:******
```

**获取服务器元数据**

| 命令               | 描述                      |
| :----------------- | :------------------------ |
| SELECT VERSION( )  | 服务器版本信息            |
| SELECT DATABASE( ) | 当前数据库名 (或者返回空) |
| SELECT USER( )     | 当前用户名                |
| SHOW STATUS        | 服务器状态                |
| SHOW VARIABLES     | 服务器配置变量            |

#### 设置MySQL用户

如果你需要添加 MySQL 用户，你只需要在 MySQL 数据库中的 user 表添加新用户即可。

以下为添加用户的的实例，用户名为guest，密码为guest123，并授权用户可进行 SELECT, INSERT 和 UPDATE操作权限：

```mysql
root@host# mysql -u root -p
Enter password:*******
mysql> use mysql;
Database changed

mysql> INSERT INTO user 
          (host, user, password, 
           select_priv, insert_priv, update_priv) 
           VALUES ('localhost', 'guest', 
           PASSWORD('guest123'), 'Y', 'Y', 'Y');
Query OK, 1 row affected (0.20 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 1 row affected (0.01 sec)

mysql> SELECT host, user, password FROM user WHERE user = 'guest';
+-----------+---------+------------------+
| host      | user    | password         |
+-----------+---------+------------------+
| localhost | guest | 6f8c114b58f2ce9e |
+-----------+---------+------------------+
1 row in set (0.00 sec)
```

#### 常用的MySQL数据库命令

- **SHOW DATABASES;** 列出 MySQL 数据库管理系统的数据库列表。
- **USE mysql;** :选择要操作的MySQL数据库，使用该命令后所有MySQL命令都只针对该数据库。
- **SHOW TABLES;** 显示指定数据库的所有表，使用该命令前需要使用 use 命令来选择要操作的数据库。
- **SHOW COLUMNS FROM Tablename;** 显示数据表的属性，属性类型，主键信息 ，是否为 NULL，默认值等其他信息。
- **SHOW INDEX FROM Tablename;** 显示数据表的详细索引信息，包括PRIMARY KEY（主键）
- **SHOW TABLE STATUS FROM Dbname LIKE 'Tablename%'\G;** 该命令将输出Dbname数据库中表名是以Tablename开头的所有表的管理系统的性能及统计信息。
- **SELECT...INTO OUTFILE**以及**mysqldump**：导出数据
- **LOAD DATA INFILE**以及**mysqlimport**：导入数据
- 其他通用SQL指令...

