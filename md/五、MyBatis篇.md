### 5.1.什么是MyBatis

> （1）Mybatis是一个半ORM（对象关系映射）框架，它内部封装了JDBC，开发时只需要关注SQL语句本身，不需要花费精力去处理加载驱动、创建连接、创建statement等繁杂的过程。程序员直接编写原生态sql，可以严格控制sql执行性能，灵活度高。
>
> （2）MyBatis 可以使用 XML 或注解来配置和映射原生信息，将 POJO映射成数据库中的记录，避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。
>
> （3）通过xml 文件或注解的方式将要执行的各种 statement 配置起来，并通过java对象和 statement中sql的动态参数进行映射生成最终执行的sql语句，最后由mybatis框架执行sql并将结果映射为java对象并返回。（从执行sql到返回result的过程）。

### 5.2.MyBatis的优点和缺点

优点：

> （1）基于SQL语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL写在XML里，解除sql与程序代码的耦合，便于统一管理；提供XML标签，支持编写动态SQL语句，并可重用。
>
> （2）与JDBC相比，减少了50%以上的代码量，消除了JDBC大量冗余的代码，不需要手动开关连接；
>
> （3）很好的与各种数据库兼容（因为MyBatis使用JDBC来连接数据库，所以只要JDBC支持的数据库MyBatis都支持）。
>
> （4）能够与Spring很好的集成；
>
> （5）提供映射标签，支持对象与数据库的ORM字段关系映射；提供对象关系映射标签，支持对象关系组件维护。

缺点:

> （1）SQL语句的编写工作量较大，尤其当字段多、关联表多时，对开发人员编写SQL语句的功底有一定要求。
>
> （2）SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库。

### 5.3.#{}和${}的区别是什么？

> #{}是预编译处理，$ {}是字符串替换。
>
> Mybatis在处理#{}时，会将sql中的#{}替换为?号，调用PreparedStatement的set方法来赋值；
>
> Mybatis在处理$ {}时，就是把$ {}替换成变量的值。
>
> 使用#{}可以有效的防止SQL注入，提高系统安全性。

### 5.4.当实体类中的属性名和表中的字段名不一样 ，怎么办 ？

第1种： 通过在查询的sql语句中定义字段名的别名，让字段名的别名和实体类的属性名一致。

```xml
<select id=”selectorder” parametertype=”int” resultetype=”me.gacl.domain.order”>
   select order_id id, order_no orderno ,order_price price 
    from orders 
    where
	order_id=#{id};
</select>
```


第2种： 通过来映射字段名和实体类属性名的一一对应的关系。

```xml
<select id="getOrder" parameterType="int" resultMap="orderresultmap">
   select * from orders where order_id=#{id}
</select>
<resultMap type="me.gacl.domain.order" id="orderresultmap">
   <!–用id属性来映射主键字段–>
   <id property="id" column="order_id">
   <!–用result属性来映射非主键字段，property为实体类属性名，column为数据表中的属性–>
   <result property = “orderno" column =”order_no"/>
   <result property="price" column="order_price" />
</reslutMap>
```

### 5.5.Mybatis是如何进行分页的？分页插件的原理是什么？

> Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页。可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。
> 
> 分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。

### 5.6.Mybatis是如何将sql执行结果封装为目标对象并返回的？都有哪些映射形式？

> 第一种是使用标签，逐一定义数据库列名和对象属性名之间的映射关系。<br>
> 第二种是使用sql列的别名功能，将列的别名书写为对象属性名。<br>
> 有了列名与属性名的映射关系后，Mybatis通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。

### 5.7.如何执行批量插入？

首先,创建一个简单的insert语句:

```java
<insert id="insertname">
	insert into names (name) values (#{value})
</insert>
```

然后在java代码中像下面这样执行批处理插入:

```java
list<string> names = new arraylist();
  names.add(“fred”);
  names.add(“barney”);
  names.add(“betty”);
  names.add(“wilma”);
  // 注意这里 executortype.batch
  sqlsession sqlsession = sqlsessionfactory.opensession(executortype.batch);
  try {
  namemapper mapper = sqlsession.getmapper(namemapper.class);
  for (string name : names) {
    mapper.insertname(name);
  }
  sqlsession.commit();
 }catch(Exception e){
  e.printStackTrace();
  sqlSession.rollback();
  throw e;
    }
    finally {
      sqlsession.close();
 }
```

### 5.8.MyBatis实现一对一有几种方式?具体怎么操作的？

> 有联合查询和嵌套查询。
>
> 联合查询是几个表联合查询,只查询一次, 通过在resultMap里面配置association节点配置一对一的类就可以完成；嵌套查询是先查一个表，根据这个表里面的结果的 外键id，去再另外一个表里面查询数据,也是通过association配置，但另外一个表的查询通过select属性配置。

### 5.9.Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？

Mybatis仅支持association关联对象和collection关联集合对象的延迟加载。

association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。

它的原理是，使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。

当然了，不光是Mybatis，几乎所有的包括Hibernate，支持延迟加载的原理都是一样的

### 5.10.Mybatis的一级、二级缓存:

> 1）一级缓存: 基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当 Session flush 或 close 之后，该 Session 中的所有 Cache 就将清空，默认打开一级缓存。
>
> 2）二级缓存与一级缓存其机制相同，默认也是采用 PerpetualCache，HashMap 存储，不同在于其存储作用域为 Mapper(Namespace)，并且可自定义存储源，如 Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态),可在它的映射文件中配置 ；
>
> 3）对于缓存数据更新机制，当某一个作用域(一级缓存 Session/二级缓存Namespaces)的进行了C/U/D操作后，默认该作用域下所有 select 中的缓存将被 clear 掉并重新更新，如果开启了二级缓存，则只根据配置判断是否刷新

### 5.11.通常一个mapper.xml文件，都会对应一个Dao接口，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？

（详细的工作原理请参考这篇文章：[Mybatis中 Dao接口和XML文件的SQL如何建立关联_张维鹏的博客-CSDN博客_dao层接口与xml文件怎么关联](https://blog.csdn.net/a745233700/article/details/89308762)）

* **Mapper 接口的工作原理**

> JDK动态代理，Mybatis运行时会使用JDK动态代理为Mapper接口生成代理对象proxy，代理对象会拦截接口方法，根据类的全限定名+方法名，唯一定位到一个MapperStatement并调用执行器执行所代表的sql，然后将sql执行结果返回。

* Mapper接口里的方法，是不能重载的，因为是使用 全限名+方法名 的保存和寻找策略。

> Dao接口即Mapper接口。接口的全限名，就是映射文件中的namespace的值；接口的方法名，就是映射文件中Mapper的Statement的id值；接口方法内的参数，就是传递给sql的参数。
>
> 当调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MapperStatement。在Mybatis中，每一个SQL标签，都会被解析为一个MapperStatement对象。
>
> 举例：com.mybatis3.mappers.StudentDao.findStudentById，可以唯一找到namespace为com.mybatis3.mappers.StudentDao下面 id 为 findStudentById 的 MapperStatement。

### 5.12.Mybatis的Xml映射文件中，不同的Xml映射文件，id是否可以重复？

不同的Xml映射文件，如果配置了namespace，那么id可以重复；如果没有配置namespace，那么id不能重复；

原因就是namespace+id是作为Map的key使用的，如果没有namespace，就剩下id，那么，id重复会导致数据互相覆盖。有了namespace，自然id就可以重复，namespace不同，namespace+id自然也就不同。

备注：在旧版本的Mybatis中，namespace是可选的，不过新版本的namespace已经是必须的了。
