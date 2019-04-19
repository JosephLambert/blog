## SQL 事务

事务是在数据库上按照一定的逻辑顺序执行的任务序列，既可以由用户手动执行，也可以由某种数据库程序自动执行。

事务实际上就是对数据库的一个或者多个更改。当你在某张表上创建更新或者删除记录的时，你就已经在使用事务了。控制事务以保证数据完整性，并对数据库错误做出处理，对数据库来说非常重要。

实践中，通常会将很多 SQL 查询组合在一起，并将其作为某个事务一部分来执行。

## 事务的属性：

事务具有以下四个标准属性，通常用缩略词 ACID 来表示：

**1、A (Atomicity) 原子性**
原子性很容易理解，也就是说事务里的所有操作要么全部做完，要么都不做，事务成功的条件是事务里的所有操作都成功，只要有一个操作失败，整个事务就失败，需要回滚。

比如银行转账，从A账户转100元至B账户，分为两个步骤：1）从A账户取100元；2）存入100元至B账户。这两步要么一起完成，要么一起不完成，如果只完成第一步，第二步失败，钱会莫名其妙少了100元。

**2、C (Consistency) 一致性**
一致性也比较容易理解，也就是说数据库要一直处于一致的状态，事务的运行不会改变数据库原本的一致性约束。

例如现有完整性约束a+b=10，如果一个事务改变了a，那么必须得改变b，使得事务结束后依然满足a+b=10，否则事务失败。

**3、I (Isolation) 独立性**
所谓的独立性是指并发的事务之间不会互相影响，如果一个事务要访问的数据正在被另外一个事务修改，只要另外一个事务未提交，它所访问的数据就不受未提交事务的影响。
比如现有有个交易是从A账户转100元至B账户，在这个交易还未完成的情况下，如果此时B查询自己的账户，是看不到新增加的100元的。

**4、D (Durability) 持久性**
持久性是指一旦事务提交后，它所做的修改将会永久的保存在数据库上，即使出现宕机也不会丢失。

## 事务控制：

有四个命令用于控制事务：

- **COMMIT：**提交更改；
- **ROLLBACK：**回滚更改；
- **SAVEPOINT：**在事务内部创建一系列可以 ROLLBACK 的还原点；
- **SET TRANSACTION：**命名事务；

## COMMIT 命令：

COMMIT 命令用于保存事务对数据库所做的更改。

COMMIT 命令会将自上次 COMMIT 命令或者 ROLLBACK 命令执行以来所有的事务都保存到数据库中。

COMMIT 命令的语法如下所示：

```sql
COMMIT;
```

## 示例：

考虑 CUSTOMERS 表，表中的记录如下所示：

```sql
+----+----------+-----+-----------+----------+
| ID | NAME     | AGE | ADDRESS   | SALARY   |
+----+----------+-----+-----------+----------+
|  1 | Ramesh   |  32 | Ahmedabad |  2000.00 |
|  2 | Khilan   |  25 | Delhi     |  1500.00 |
|  3 | kaushik  |  23 | Kota      |  2000.00 |
|  4 | Chaitali |  25 | Mumbai    |  6500.00 |
|  5 | Hardik   |  27 | Bhopal    |  8500.00 |
|  6 | Komal    |  22 | MP        |  4500.00 |
|  7 | Muffy    |  24 | Indore    | 10000.00 |
+----+----------+-----+-----------+----------+
```

下面的示例将会删除表中 age=25 的记录，然后将更改提交（COMMIT）到数据库中。

```sql
SQL> DELETE FROM CUSTOMERS
     WHERE AGE = 25;
SQL> COMMIT;
```

上述语句将会从表中删除两行记录，再执行 SELECT 语句将会得到如下结果：

```sql
+----+----------+-----+-----------+----------+
| ID | NAME     | AGE | ADDRESS   | SALARY   |
+----+----------+-----+-----------+----------+
|  1 | Ramesh   |  32 | Ahmedabad |  2000.00 |
|  3 | kaushik  |  23 | Kota      |  2000.00 |
|  5 | Hardik   |  27 | Bhopal    |  8500.00 |
|  6 | Komal    |  22 | MP        |  4500.00 |
|  7 | Muffy    |  24 | Indore    | 10000.00 |
+----+----------+-----+-----------+----------+
```

## ROLLBACK 命令：

ROLLBACK 命令用于撤销尚未保存到数据库中的事务。

ROLLBACK 命令只能撤销自上次 COMMIT 命令或者 ROLLBACK 命令执行以来的事务。

ROLLBACK 命令的语法如下所示：

```sql
ROLLBACK;
```

## 示例：

考虑 CUSTOMERS 表，表中的记录如下所示：

```sql
+----+----------+-----+-----------+----------+
| ID | NAME     | AGE | ADDRESS   | SALARY   |
+----+----------+-----+-----------+----------+
|  1 | Ramesh   |  32 | Ahmedabad |  2000.00 |
|  2 | Khilan   |  25 | Delhi     |  1500.00 |
|  3 | kaushik  |  23 | Kota      |  2000.00 |
|  4 | Chaitali |  25 | Mumbai    |  6500.00 |
|  5 | Hardik   |  27 | Bhopal    |  8500.00 |
|  6 | Komal    |  22 | MP        |  4500.00 |
|  7 | Muffy    |  24 | Indore    | 10000.00 |
+----+----------+-----+-----------+----------+
```

下面的示例将会从表中删除所有 age=25 的记录，然后回滚（ROLLBACK）对数据库所做的更改。

```sql
SQL> DELETE FROM CUSTOMERS
     WHERE AGE = 25;
SQL> ROLLBACK;
```

结果是删除操作并不会对数据库产生影响。现在，执行 SELECT 语句将会得到如下结果：

```sql
+----+----------+-----+-----------+----------+
| ID | NAME     | AGE | ADDRESS   | SALARY   |
+----+----------+-----+-----------+----------+
|  1 | Ramesh   |  32 | Ahmedabad |  2000.00 |
|  2 | Khilan   |  25 | Delhi     |  1500.00 |
|  3 | kaushik  |  23 | Kota      |  2000.00 |
|  4 | Chaitali |  25 | Mumbai    |  6500.00 |
|  5 | Hardik   |  27 | Bhopal    |  8500.00 |
|  6 | Komal    |  22 | MP        |  4500.00 |
|  7 | Muffy    |  24 | Indore    | 10000.00 |
+----+----------+-----+-----------+----------+
```

## SAVEPOINT 命令：

SAVEPOINT 是事务中的一个状态点，使得我们可以将事务回滚至特定的点，而不是将整个事务都撤销。

SAVEPOINT 命令的记录如下所示：

```sql
SAVEPOINT SAVEPOINT_NAME;
```

该命令只能在事务语句之间创建保存点（SAVEPOINT）。ROLLBACK 命令可以用于撤销一系列的事务。

回滚至某一保存点的语法如下所示：

```sql
ROLLBACK TO SAVEPOINT_NAME;
```

下面的示例中，你计划从 CUSTOMERS 表中删除三条不同的记录，并在每次删除之前创建一个保存点（SAVEPOINT），从而使得你可以在任何任何时候回滚到任意的保存点，以恢复数据至其原始状态。

## 示例：

考虑 CUSTOMERS 表，表中的记录如下所示：

```sql
+----+----------+-----+-----------+----------+
| ID | NAME     | AGE | ADDRESS   | SALARY   |
+----+----------+-----+-----------+----------+
|  1 | Ramesh   |  32 | Ahmedabad |  2000.00 |
|  2 | Khilan   |  25 | Delhi     |  1500.00 |
|  3 | kaushik  |  23 | Kota      |  2000.00 |
|  4 | Chaitali |  25 | Mumbai    |  6500.00 |
|  5 | Hardik   |  27 | Bhopal    |  8500.00 |
|  6 | Komal    |  22 | MP        |  4500.00 |
|  7 | Muffy    |  24 | Indore    | 10000.00 |
+----+----------+-----+-----------+----------+
```

操作序列如下所示：

```sql
SQL> SAVEPOINT SP1;
Savepoint created.
SQL> DELETE FROM CUSTOMERS WHERE ID=1;
1 row deleted.
SQL> SAVEPOINT SP2;
Savepoint created.
SQL> DELETE FROM CUSTOMERS WHERE ID=2;
1 row deleted.
SQL> SAVEPOINT SP3;
Savepoint created.
SQL> DELETE FROM CUSTOMERS WHERE ID=3;
1 row deleted.
```

现在，三次删除操作已经生效了，如果此时你改变主意决定回滚至名字为 SP2 的保存点，由于 SP2 于第一次删除操作之后创建，所以后两次删除操作将会被撤销。

```sql
SQL> ROLLBACK TO SP2;
Rollback complete.
```

注意，由于你将数据库回滚至 SP2，所以只有第一次删除真正起效了：

```sql
SQL> SELECT * FROM CUSTOMERS;
+----+----------+-----+-----------+----------+
| ID | NAME     | AGE | ADDRESS   | SALARY   |
+----+----------+-----+-----------+----------+
|  2 | Khilan   |  25 | Delhi     |  1500.00 |
|  3 | kaushik  |  23 | Kota      |  2000.00 |
|  4 | Chaitali |  25 | Mumbai    |  6500.00 |
|  5 | Hardik   |  27 | Bhopal    |  8500.00 |
|  6 | Komal    |  22 | MP        |  4500.00 |
|  7 | Muffy    |  24 | Indore    | 10000.00 |
+----+----------+-----+-----------+----------+
6 rows selected.
```

## RELEASE SAVEPOINT 命令：

RELEASE SAVEPOINT 命令用于删除先前创建的保存点。

RELEASE SAVEPOINT 的语法如下所示：

```sql
RELEASE SAVEPOINT SAVEPOINT_NAME;
```

保存点一旦被释放，你就不能够再用 ROLLBACK 命令来撤销该保存点之后的事务了。

## SET TRANSACTION 命令：

SET TRANSACTION 命令可以用来初始化数据库事务，指定随后的事务的各种特征。

例如，你可以将某个事务指定为只读或者读写。

SET TRANSACTION 命令的语法如下所示：

```sql
SET TRANSACTION [ READ WRITE | READ ONLY ];
```

