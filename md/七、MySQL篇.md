### 7.1.数据库的三范式是什么

> 第一范式：列不可再分
>
> 第二范式：行可以唯一区分，主键约束
>
> 第三范式：表的非主属性不能依赖与其他表的非主属性 外键约束
>
> 且三大范式是一级一级依赖的，第二范式建立在第一范式上，第三范式建立第一第二范式上。

### 7.2.数据库引擎有哪些

如何查看mysql提供的所有存储引擎

```shell
mysql> show engines;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/39265fd832f64c31a85bdf964ae3c75a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA55m95aSn6ZSF,size_20,color_FFFFFF,t_70,g_se,x_16)

mysql常用引擎包括：MYISAM、Innodb、Memory、MERGE

> **MYISAM**:全表锁，拥有较高的执行速度，不支持事务，不支持外键，并发性能差，占用空间相对较小，对事务完整性没有要求，以select、insert为主的应用基本上可以使用这个引擎
>
> **Innodb**:行级锁，提供了具有提交、回滚和崩溃恢复能力的事务安全，支持自动增长列，支持外键约束，并发能力强，占用空间是MYISAM的2.5倍，处理效率相对会差一些
>
> **Memory**:全表锁，存储在内存中，速度快，但会占用和数据量成正比的内存空间且数据在mysql重启时会丢失，默认使用HASH索引，检索效率非常高，但不适用于精确查找，主要用于那些内容变化不频繁的代码表
>
> **MERGE**：是一组MYISAM表的组合

### 7.3.InnoDB与MyISAM的区别

> 1.InnoDB支持事务，MyISAM不支持，对于InnoDB每一条SQL语言都默认封装成事务，自动提交，这样会影响速度，所以最好把多条SQL语言放在begin和commit之间，组成一个事务；
>
> 2.InnoDB支持外键，而MyISAM不支持。对一个包含外键的InnoDB表转为MYISAM会失败；
>
> 3.InnoDB是聚集索引，数据文件是和索引绑在一起的，必须要有主键，通过主键索引效率很高。但是辅助索引需要两次查询，先查询到主键，然后再通过主键查询到数据。因此，主键不应该过大，因为主键太大，其他索引也都会很大。而MyISAM是非聚集索引，数据文件是分离的，索引保存的是数据文件的指针。主键索引和辅助索引是独立的。
>
> 4.InnoDB不保存表的具体行数，执行select count(*) from table时需要全表扫描。而MyISAM用一个变量保存了整个表的行数，执行上述语句时只需要读出该变量即可，速度很快；
>
> 5.Innodb不支持全文索引，而MyISAM支持全文索引，查询效率上MyISAM要高；

如何选择引擎？

> 如果没有特别的需求，使用默认的 Innodb 即可。
>
> **MyISAM**：以读写插入为主的应用程序，比如博客系统、新闻门户网站。
>
> **Innodb**：更新（删除）操作频率也高，或者要保证数据的完整性；并发量高，支持事务和外键。比如OA自动化办公系统

### 7.4.数据库的事务

什么是事务？： 多条sql语句，要么全部成功，要么全部失败。

事务的特性：

数据库事务特性：**原子性(Atomic)、一致性(Consistency)、隔离性(Isolation)、持久性(Durabiliy)。简称ACID**。

> **原子性**：组成一个事务的多个数据库操作是一个不可分割的原子单元，只有所有操作都成功，整个事务才会提交。任何一个操作失败，已经执行的任何操作都必须撤销，让数据库返回初始状态。
>
> **一致性**：事务操作成功后，数据库所处的状态和它的业务规则是一致的。即数据不会被破坏。如A转账100元给B，不管操作是否成功，A和B的账户总额是不变的。
>
> **隔离性**：在并发数据操作时，不同的事务拥有各自的数据空间，它们的操作不会对彼此产生干扰。
>
> **持久性**：一旦事务提交成功，事务中的所有操作都必须持久化到数据库中。

### 7.5.索引问题

索引是对数据库表中一个或多个列的值进行排序的结构，建立索引有助于快速获取信息。

你也可以这样理解：索引就是加快检索表中数据的方法。数据库的索引类似于书籍的索引。在书籍中，索引允许用户不必翻阅完整个书就能迅速地找到所需要的信息。在数据库中，索引也允许数据库程序迅速地找到表中的数据，而不必扫描整个数据库。

mysql 有4种不同的索引：

> * **主键索引（PRIMARY）**
>
> 数据列不允许重复，不允许为NULL，一个表只能有一个主键。
>
> * **唯一索引（UNIQUE）**
>
> 数据列不允许重复，允许为NULL值，一个表允许多个列创建唯一索引。
>
> 可以通过 ALTER TABLE table_name ADD UNIQUE (column); 创建唯一索引
>
> 可以通过 ALTER TABLE table_name ADD UNIQUE (column1,column2); 创建唯一组合索引
>
> * **普通索引（INDEX）**
>
> 可以通过 ALTER TABLE table_name ADD INDEX index_name (column); 创建普通索引
>
> 可以通过 ALTER TABLE table_name ADD INDEX index_name(column1,column2,column3); 创建组合索引
>
> * **全文索引（FULLTEXT）**
>
> 可以通过 ALTER TABLE table_name ADD FULLTEXT (column); 创建全文索引

索引并非是越多越好，创建索引也需要耗费资源，一是增加了数据库的存储空间，二是在插入和删除时要花费较多的时间维护索引

> 索引加快数据库的检索速度
>
> 索引降低了插入、删除、修改等维护任务的速度
>
> 唯一索引可以确保每一行数据的唯一性
>
> 通过使用索引，可以在查询的过程中使用优化隐藏器，提高系统的性能
>
> 索引需要占物理和数据空间

### 7.6.SQL优化

> 1. 查询语句中不要使用select *
> 2. 尽量减少子查询，使用关联查询（left join,right join,inner join）替代
> 3. 减少使用IN或者NOT IN ,使用exists，not exists或者关联查询语句替代
> 4. or 的查询尽量用 union或者union all 代替(在确认没有重复数据或者不用剔除重复数据时，union all会更好)
> 5. 应尽量避免在 where 子句中使用 != 或 <> 操作符，否则将引擎放弃使用索引而进行全表扫描。
> 6. 应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫描，如： select id from t where num is null 可以在num上设置默认值0，确保表中num列没有null值，然后这样查询： select id from t where num=0

### 7.7.简单说一说drop、delete与truncate的区别

SQL中的drop、delete、truncate都表示删除，但是三者有一些差别

> delete和truncate只删除表的数据不删除表的结构
>
> 执行速度,一般来说: drop> truncate >delete
>
> delete语句是dml,这个操作会放到rollback segement中,事务提交之后才生效，如果有相应的trigger,执行的时候将被触发 
>
> truncate,drop是ddl, 操作立即生效,原数据不放到rollback segment中,不能回滚. 操作不触发trigger

### 7.8.什么是视图

视图是一种虚拟的表，具有和物理表相同的功能。可以对视图进行增，改，查，操作，视图通常是有一个表或者多个表的行或列的子集。对视图的修改不影响基本表。它使得我们获取数据更容易，相比多表查询。

### 7.9.什么是内联接、左外联接、右外联接？

> 1.内联接（Inner Join）：匹配2张表中相关联的记录。
>
> 2.左外联接（Left Outer Join）：除了匹配2张表中相关联的记录外，还会匹配左表中剩余的记录，右表中未匹配到的字段用NULL表示。
>
> 3.右外联接（Right Outer Join）：除了匹配2张表中相关联的记录外，还会匹配右表中剩余的记录，左表中未匹配到的字段用NULL表示。在判定左表和右表时，要根据表名出现在Outer Join的左右位置关系。

### 7.10.并发事务带来哪些问题?

在典型的应用程序中，多个事务并发运行，经常会操作相同的数据来完成各自的任务（多个用户对同一数据进行操作）。并发虽然是必须的，但可能会导致以下的问题。

> **脏读（Dirty read）:** 当一个事务正在访问数据并且对数据进行了修改，而这种修改还没有提交到数据库中，这时另外一个事务也访问了这个数据，然后使用了这个数据。因为这个数据是还没有提交的数据，那么另外一个事务读到的这个数据是“脏数据”，依据“脏数据”所做的操作可能是不正确的。
>
> **丢失修改（Lost to modify）:** 指在一个事务读取一个数据时，另外一个事务也访问了该数据，那么在第一个事务中修改了这个数据后，第二个事务也修改了这个数据。这样第一个事务内的修改结果就被丢失，因此称为丢失修改。 例如：事务1读取某表中的数据A=20，事务2也读取A=20，事务1修改A=A-1，事务2也修改A=A-1，最终结果A=19，事务1的修改被丢失。
>
> **不可重复读（Unrepeatableread）:** 指在一个事务内多次读同一数据。在这个事务还没有结束时，另一个事务也访问该数据。那么，在第一个事务中的两次读数据之间，由于第二个事务的修改导致第一个事务两次读取的数据可能不太一样。这就发生了在一个事务内两次读到的数据是不一样的情况，因此称为不可重复读。
>
> **幻读（Phantom read）:** 幻读与不可重复读类似。它发生在一个事务（T1）读取了几行数据，接着另一个并发事务（T2）插入了一些数据时。在随后的查询中，第一个事务（T1）就会发现多了一些原本不存在的记录，就好像发生了幻觉一样，所以称为幻读。

**不可重复读和幻读区别：**

**不可重复读**的重点是修改，比如多次读取一条记录发现其中某些列的值被修改

**幻读**的重点在于新增或者删除，比如多次读取一条记录发现记录增多或减少了

### 7.11.事务隔离级别有哪些?MySQL的默认隔离级别是?

SQL 标准定义了四个隔离级别：

> READ-UNCOMMITTED(读取未提交)： 最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读。
>
> READ-COMMITTED(读取已提交)： 允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生。
>
> REPEATABLE-READ(可重复读)： 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。
>
> SERIALIZABLE(可串行化)： 最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。

|     隔离级别     | 脏读 | 不可重复读 | 幻影读 |
| :--------------: | :--: | :--------: | :----: |
| READ-UNCOMMITTED |  √   |     √      |   √    |
|  READ-COMMITTED  |  ×   |     √      |   √    |
| REPEATABLE-READ  |  ×   |     ×      |   √    |
|   SERIALIZABLE   |  ×   |     ×      |   ×    |

MySQL InnoDB 存储引擎的默认支持的隔离级别是 REPEATABLE-READ（可重读）。我们可以通过SELECT @@tx_isolation; 命令来查看

```shel
mysql> SELECT @@tx_isolation;
+-----------------+
| @@tx_isolation |
+-----------------+
| REPEATABLE-READ |
+-----------------+
```

> 这里需要注意的是：与 SQL 标准不同的地方在于 InnoDB 存储引擎在 REPEATABLE-READ（可重读）事务隔离级别下使用的是Next-Key Lock 锁算法，因此可以避免幻读的产生，这与其他数据库系统(如SQL Server) 是不同的。所以说InnoDB 存储引擎的默认支持的隔离级别是 REPEATABLE-READ（可重读） 已经可以完全保证事务的隔离性要求，即达到了 SQL标准的 SERIALIZABLE(可串行化) 隔离级别。因为隔离级别越低，事务请求的锁越少，所以大部分数据库系统的隔离级别都是 READ-COMMITTED(读取提交内容) ，但是你要知道的是InnoDB 存储引擎默认使用 REPEAaTABLE-READ（可重读） 并不会有任何性能损失。
>
> InnoDB 存储引擎在 分布式事务 的情况下一般会用到 SERIALIZABLE(可串行化) 隔离级别。

### 7.12.大表如何优化？

当MySQL单表记录数过大时，数据库的CRUD性能会明显下降，一些常见的优化措施如下:

> 1.限定数据的范围
>
> 务必禁止不带任何限制数据范围条件的查询语句。比如：我们当用户在查询订单历史的时候，我们可以控制在一个月的范围内；
>
> 2.读/写分离
>
> 经典的数据库拆分方案，主库负责写，从库负责读；
>
> 3.垂直分区
>
> 根据数据库里面数据表的相关性进行拆分。 例如，用户表中既有用户的登录信息又有用户的基本信息，可以将用户表拆分成两个单独的表，甚至放到单独的库做分库。
>
> 简单来说垂直拆分是指数据表列的拆分，把一张列比较多的表拆分为多张表。 如下图所示，这样来说大家应该就更容易理解了。
>
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/ce7a42f1107940c1b8e233adce6fe317.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA55m95aSn6ZSF,size_20,color_FFFFFF,t_70,g_se,x_16)
>
> 垂直拆分的优点： 可以使得列数据变小，在查询时减少读取的Block数，减少I/O次数。此外，垂直分区可以简化表的结构，易于维护。
>
> 垂直拆分的缺点： 主键会出现冗余，需要管理冗余列，并会引起Join操作，可以通过在应用层进行Join来解决。此外，垂直分区会让事务变得更加复杂；
>
> 4.水平分区
>
> 保持数据表结构不变，通过某种策略存储数据分片。这样每一片数据分散到不同的表或者库中，达到了分布式的目的。 水平拆分可以支撑非常大的数据量。
>
> 水平拆分是指数据表行的拆分，表的行数超过200万行时，就会变慢，这时可以把一张的表的数据拆成多张表来存放。举个例子：我们可以将用户信息表拆分成多个用户信息表，这样就可以避免单一表数据量过大对性能造成影响
>
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/066af8e9b65445b8b3a699e27ffd9874.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA55m95aSn6ZSF,size_20,color_FFFFFF,t_70,g_se,x_16)
>
> 水平拆分可以支持非常大的数据量。需要注意的一点是：分表仅仅是解决了单一表数据过大的问题，但由于表的数据还是在同一台机器上，其实对于提升MySQL并发能力没有什么意义，所以 水平拆分最好分库 。
>
> 水平拆分能够 支持非常大的数据量存储，应用端改造也少，但 分片事务难以解决 ，跨节点Join性能较差，逻辑复杂。《Java工程师修炼之道》的作者推荐 尽量不要对数据进行分片，因为拆分会带来逻辑、部署、运维的各种复杂度 ，一般的数据表在优化得当的情况下支撑千万以下的数据量是没有太大问题的。如果实在要分片，尽量选择客户端分片架构，这样可以减少一次和中间件的网络I/O。

下面补充一下数据库分片的两种常见方案：

> 客户端代理： 分片逻辑在应用端，封装在jar包中，通过修改或者封装JDBC层来实现。 当当网的Sharding-JDBC 、阿里的TDDL是两种比较常用的实现。
>
> 中间件代理： 在应用和数据中间加了一个代理层。分片逻辑统一维护在中间件服务中。 我们现在谈的 Mycat 、360的Atlas、网易的DDB等等都是这种架构的实现。

详细内容可以参考： MySQL大表优化方案:https://segmentfault.com/a/1190000006158186

### 7.13.分库分表之后,id 主键如何处理？

因为要是分成多个表之后，每个表都是从 1 开始累加，这样是不对的，我们需要一个全局唯一的 id 来支持。

生成全局 id 有下面这几种方式：

> UUID：不适合作为主键，因为太长了，并且无序不可读，查询效率低。比较适合用于生成唯一的名字的标示比如文件的名字。
>
> 数据库自增 id : 两台数据库分别设置不同步长，生成不重复ID的策略来实现高可用。这种方式生成的 id 有序，但是需要独立部署数据库实例，成本高，还会有性能瓶颈。
>
> 利用 redis 生成 id : 性能比较好，灵活方便，不依赖于数据库。但是，引入了新的组件造成系统更加复杂，可用性降低，编码更加复杂，增加了系统成本。
>
> Twitter的snowflake算法 ：Github 地址：https://github.com/twitter-archive/snowflake。
>
> 美团的Leaf分布式ID生成系统 ：Leaf 是美团开源的分布式ID生成器，能保证全局唯一性、趋势递增、单调递增、信息安全，里面也提到了几种分布式方案的对比，但也需要依赖关系数据库、Zookeeper等中间件。感觉还不错。美团技术团队的一篇文章：https://tech.meituan.com/2017/04/21/mt-leaf.html 。

### 7.14.mysql有关权限的表都有哪几个

MySQL服务器通过权限表来控制用户对数据库的访问，权限表存放在mysql数据库里，由mysql_install_db脚本初始化。这些权限表分别user，db，table_priv，columns_priv和host。下面分别介绍一下这些表的结构和内容：

> user权限表：记录允许连接到服务器的用户帐号信息，里面的权限是全局级的。
>
> db权限表：记录各个帐号在各个数据库上的操作权限。
>
> table_priv权限表：记录数据表级的操作权限。
>
> columns_priv权限表：记录数据列级的操作权限。
>
> host权限表：配合db权限表对给定主机上数据库级操作权限作更细致的控制。这个权限表不受GRANT和REVOKE语句的影响。

### 7.15.mysql有哪些数据类型

> 1、**整数类型** ，包括TINYINT、SMALLINT、MEDIUMINT、INT、BIGINT，分别表示1字节、2字节、3字节、4字节、8字节整数。任何整数类型都可以加上UNSIGNED属性，表示数据是无符号的，即非负整数。
>
> 长度：整数类型可以被指定长度，例如：INT(11)表示长度为11的INT类型。长度在大多数场景是没有意义的，它不会限制值的合法范围，只会影响显示字符的个数，而且需要和UNSIGNED ZEROFILL属性配合使用才有意义。
>
> 例子，假定类型设定为INT(5)，属性为UNSIGNED ZEROFILL，如果用户插入的数据为12的话，那么数据库实际存储数据为00012。
>
> 2、**实数类型**，包括FLOAT、DOUBLE、DECIMAL。
>
> DECIMAL可以用于存储比BIGINT还大的整型，能存储精确的小数。
>
> 而FLOAT和DOUBLE是有取值范围的，并支持使用标准的浮点进行近似计算。
>
> 计算时FLOAT和DOUBLE相比DECIMAL效率更高一些，DECIMAL你可以理解成是用字符串进行处理。
>
> 3、**字符串类型**，包括VARCHAR、CHAR、TEXT、BLOB
>
> VARCHAR用于存储可变长字符串，它比定长类型更节省空间。
>
> VARCHAR使用额外1或2个字节存储字符串长度。列长度小于255字节时，使用1字节表示，否则使用2字节表示。
>
> VARCHAR存储的内容超出设置的长度时，内容会被截断。
>
> CHAR是定长的，根据定义的字符串长度分配足够的空间。
>
> CHAR会根据需要使用空格进行填充方便比较。
>
> CHAR适合存储很短的字符串，或者所有值都接近同一个长度。
>
> CHAR存储的内容超出设置的长度时，内容同样会被截断。
>
> 使用策略：
>
> 对于经常变更的数据来说，CHAR比VARCHAR更好，因为CHAR不容易产生碎片。
>
> 对于非常短的列，CHAR比VARCHAR在存储空间上更有效率。
>
> 使用时要注意只分配需要的空间，更长的列排序时会消耗更多内存。
>
> 尽量避免使用TEXT/BLOB类型，查询时会使用临时表，导致严重的性能开销。
>
> 4、**枚举类型**（ENUM），把不重复的数据存储为一个预定义的集合。
>
> 有时可以使用ENUM代替常用的字符串类型。
>
> ENUM存储非常紧凑，会把列表值压缩到一个或两个字节。
>
> ENUM在内部存储时，其实存的是整数。
>
> 尽量避免使用数字作为ENUM枚举的常量，因为容易混乱。
>
> 排序是按照内部存储的整数
>
> 5、**日期和时间类型**，尽量使用timestamp，空间效率高于datetime，用整数保存时间戳通常不方便处理。
>
> 如果需要存储微妙，可以使用bigint存储。

### 7.16.创建索引的三种方式，删除索引

第一种方式：在执行CREATE TABLE时创建索引

```mysql
CREATE TABLE user_index2 (
	id INT auto_increment PRIMARY KEY,
	first_name VARCHAR (16),
	last_name VARCHAR (16),
	id_card VARCHAR (18),
	information text,
	KEY name (first_name, last_name),
	FULLTEXT KEY (information),
	UNIQUE KEY (id_card)
);
```

第二种方式：使用ALTER TABLE命令去增加索引

```mysql
ALTER TABLE table_name ADD INDEX index_name (column_list);
```

> ALTER TABLE用来创建普通索引、UNIQUE索引或PRIMARY KEY索引。
>
> 其中table_name是要增加索引的表名，column_list指出对哪些列进行索引，多列时各列之间用逗号分隔。
>
> 索引名index_name可自己命名，缺省时，MySQL将根据第一个索引列赋一个名称。另外，ALTER TABLE允许在单个语句中更改多个表，因此可以在同时创建多个索引。

第三种方式：使用CREATE INDEX命令创建

```mysql
CREATE INDEX index_name ON table_name (column_list);
```

CREATE INDEX可对表增加普通索引或UNIQUE索引。（但是，不能创建PRIMARY KEY索引）

**删除索引**

根据索引名删除普通索引、唯一索引、全文索引： alter table 表名 drop KEY 索引名

```mysql
alter table user_index drop KEY name;
alter table user_index drop KEY id_card;
alter table user_index drop KEY information;
```

删除主键索引： alter table 表名 drop primary key （因为主键只有一个）。

这里值得注意的是，如果主键自增长，那么不能直接执行此操作（自增长依赖于主键索引）：

![在这里插入图片描述](https://img-blog.csdnimg.cn/e6790925d8a147c7a113822195a4645c.png)


需要取消自增长再行删除：

```mysql
alter table user_index
-- 重新定义字段
MODIFY id int,
drop PRIMARY KEY
```

但通常不会删除主键，因为设计主键一定与业务逻辑无关。

### 7.17 MYSQL索引失效的几种情况

先简单的介绍一下MySQL中的explain，explain这个命令可以查看[SQL语句](https://so.csdn.net/so/search?q=SQL语句&spm=1001.2101.3001.7020)的执行计划，结果如图所示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224002951439.png)

* id: SELECT 查询的标识符. 每个 SELECT 都会自动分配一个唯一的标识符.
* select_type: SELECT 查询的类型.
* table: 查询的是哪个表
* partitions: 匹配的分区
* type: join 类型
* possible_keys: 此次查询中可能选用的索引
* key: 此次查询中确切使用到的索引.
* ref: 哪个字段或常数与 key 一起被使用
* rows: 显示此查询一共扫描了多少行. 这个是一个估计值.
* filtered: 表示此查询条件所过滤的数据的百分比
* extra: 额外的信息

因此，要查看索引是否失效，只需要看possible_keys以及key属性就可以了，其他属性暂不赘述。

**索引失效的几种情况：**

此表gift_exchange_record作为例子，以下是当前表的索引：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224003852581.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTg0Mzcz,size_16,color_FFFFFF,t_70)

**1、索引列中使用了运算或者函数**

* 错误情况1，使用了数学运算：

![img](https://img-blog.csdnimg.cn/20200224004038769.png)

* 错误情况2，使用了函数运算：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224004158496.png)

* 正确情况：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224004227413.png)

**2、or语句中前后字段没有同时都为索引**

* 错误情况：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224004928711.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTg0Mzcz,size_16,color_FFFFFF,t_70)

* 正确情况：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224004809825.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTg0Mzcz,size_16,color_FFFFFF,t_70)

**3、数据类型出现隐式转化， 例如字符串比较没有使用单引号**

* 错误情况：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224005241455.png)

* 正确情况：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224005157182.png)

**4、like语句以%开头**

* 错误情况：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224005540395.png)

* 正确情况：

![在这里插入图片描述](https://img-blog.csdnimg.cn/202002240056484.png)

**5、索引字段中使用is null 、is not null 、！=、<>**

**6、复合索引中没有遵循最佳左前缀原则**

最佳左前缀原则即要使复合索引生效，必须要先有左边的字段，再使用右边的字段时才会生效，直接使用右边的字段索引不会生效。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224214350724.png)

* 正确情况1：只使用左边字段

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224214605852.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTg0Mzcz,size_16,color_FFFFFF,t_70)

* 正确情况2：左右字段都使用

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224214425876.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTg0Mzcz,size_16,color_FFFFFF,t_70)

* 错误情况：只使用了右边字段

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224214511376.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTg0Mzcz,size_16,color_FFFFFF,t_70)

**7、查询结果大于全表的30%**

### 7.18 什么是联合索引,为什么建议尽量用联合索引

> 为多列字段建立一个索引，称之为联合索引。
>
> 联合索引需要遵从最左前缀原则，多个单列索引在多条件查询时优化器会选择最优索引策略，可能只用一个索引，也可能将多个索引全用上！ 但多个单列索引底层会建立多个B+索引树，比较占用空间，也会浪费一定搜索效率，所以索引建议最好建联合索引

### 7.19 什么是覆盖索引，以及优点？

> 覆盖索引：一个辅助索引包含了查询结果的数据就叫做覆盖索引，即从辅助索引中就可以得到查询结果，而不需要从聚集索引中查询

### 7.20 为什么使用B+树,而不用其他的，例如红黑树做索引

> 1. 文件很大，不可能全部存储在内存中，故要存储到磁盘上
> 2. 索引的结构组织要尽量减少查找过程中磁盘I/O的存取次数(为什么使用B-/+Tree，还跟磁盘存取原理有关。)
> 3. 局部性原理与磁盘预读，预读的长度一般为页(page)的整倍数，(在许多操作系统中，页得大小通常为4k)
> 4. 数据库系统巧妙利用了磁盘预读原理，将一个节点的大小设为等于一个页,默认16k，这样每个节点只需要一次I/O就可以完全载入，(由于节点中有两个数组，所以地址连续)。而红黑树这种结构，明显要深的多。由于逻辑上很近的节点(父子)物理上可能很远，无法利用局部性

### 7.21 什么是索引下推

> 索引条件下推(Index Condition Pushdown),简称ICP。MySQL5.6新添加，用于优化数据的查询。
>
> 当你不使用ICP,通过使用非主键索引（普通索引or二级索引）进行查询，存储引擎通过索引检索数据，然后返回给MySQL服务器，服务器再判断是否符合条件。
>
> 使用ICP，当存在索引的列做为判断条件时，MySQL服务器将这一部分判断条件传递给存储引擎，然后存储引擎通过判断索引是否符合MySQL服务器传递的条件，只有当索引符合条件时才会将数据检索出来返回给MySQL服务器。

### 7.22 分区分表分库

> `分区：` 一张大表进行分区后，他还是一张表，不会变成二张表，但是他存放数据的区块变多了;**突破磁盘I/O瓶颈，想提高磁盘的读写能力**
>
> `分表:` 多个表;**单表的并发能力提高了，磁盘I/O性能也提高了**
>
> **分区和分表的测重点不同，分表重点是存取数据时，如何提高mysql并发能力上；而分区呢，如何突破磁盘的读写能力，从而达到提高mysql性能的目的。**
>
> `分库： `单机性能不够,分成多个库提升性能；
>
> `分表分库：`
>
> 垂直切分，即将表按照功能模块、关系密切程度划分出来，部署到不同的库上水平切分，当一个表中的数据量过大时，我们可以把该表的数据按照某种规则

