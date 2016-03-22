# ORMLite 使用说明包

--
>* 版本号：4.48
>* 2013年12月
>* 作者：Gray Watson



此使用手册的使用得到了 Gray Watson 关于 Creative Commons Attribution-Share Alike 3.0 许可条款的许可。
在保留之前版本和当前版本许可证的前提下，允许制作和发行本使用手册的副本。


##目录



[MENU]



##ORMLite

版本号：4.48 2013年12月

ORMLite 在java类和Sqlite数据库之间提供了一个轻量级对象关系映射。当然，现在已经有更成熟的 ORM 框架提供了这样的功能，例如 Hibernate 和 iBatis。但是，Hibernate 和 iBatis 存在很多复杂的依赖，作者想参考JDBC的功能做一个简单但是功能强大的ORM框架。

ORMLite 对 MySQL, Postgres, H2, SQLite, Derby, HSQLDB, Microsoft SQL Server 提供 JDBC 连接，并且可以相对容易的增加新的扩展。ORMLite 在Android系统上同样支持调用本地数据库。要支持一个数据库，都需要修改代码，但是作者还是对DB2, Oracle, 通用的 ODBC, 和 Netezza 的支持提供了初步的实现。如果ORMLite不支持你使用的数据库，请联系作者。

为了快速开始使用ORMLite,请参考[Chapter 1\[Getting Started\],page 2](#point1).Android 使用者需要查看Android专题页。请参考[Chapter 4\[Use With Android\]page 49](#point2).你也可以查看文档的实例章节，那里有各种各样可运行的代码包和Android应用。请参考[Chapter 7\[Examples\],page 78](#point3)。这里还有一个[HTML 格式的文档](#point4)。

Gray Watson [http://256.com/gray](http://256.com/gray)




##1.开始使用

以下内容可以帮助你开始使用ORMLite。Android使用者需要在阅读这些内容后，再阅读一下Android专题页。请查看[Chapter 4\[Use With Android\],page 49](#point5)。

###1.1 下载 ORMLite Jar 包

开始使用ORMLite之前，你需要先下载 jar 包文件。[ORMLite release page](#point) 是默认的仓库地址，这些 jar 包可以从 [central maven repository]()和[Sourceforge]()这两个仓库下载。

如果用户通过 JDBC 连接 SQL 数据库，需要下载 ormlite-jdbc-4.48.jar 和 ormlite-core-4.48.jar 这两个 jar 包。如果是Android应用，则需要选择 ormlite-android-4.48.jar 和 ormlite-core- 4.48.jar 这两个 jar 包。无论是 JDBC 还是 Android ，都需要 ormlite-core release 版，因为它包含了 ORMLite 的后端实现。虽然 ORMLite 有一些你想使用的可选包，但是它没有任何外部的必须依赖。请参考 [Section 5.6 \[Dependencies\],page 66]()。它的代码可以运行在 Java 5 及以后的版本上。

###1.2 配置一个 Class

下面是一个通过注解配置，需要保存到数据库的 class 示例。@DatabaseTable 这个注解配置 Account class 存储到数据库时表名为 accounts。@DatabaseField 这个在 Account 类字段上的注解，提供一个映射用于在数据库表上创建与字段名相同的列。

被注解修饰的字段被配置为 id=true 代表它为数据库的主键。同样，需要注意的是，必须有一个无参的构造方法，用于数据库查询是返回对象使用。更多信息（JPA 注解 和其他配置class 的方法）请在本使用手册的后面查看 class 的设置信息。请参考[Section 2.1\[Class Setup\],page 5]()。

```
@DatabaseTable(tableName = "accounts")     public class Account {         @DatabaseField(id = true)         private String name;         @DatabaseField         private String password;         public Account() {             // ORMLite needs a no-arg constructor         }         public Account(String name, String password) {             this.name = name;             this.password = password;         }
         public String getName() {             return name;         }         public void setName(String name) {             this.name = name;         }         public String getPassword() {             return password;         }         public void setPassword(String password) {             this.password = password;         }     }

```

###1.3 配置一个 DAO

一个典型的 Java 模式是在数据访问对象（DAO）中隔离数据库操作。每一个DAO都提供了增、删、改、查 等类型的操作功能，并且专门将这些操作都放在一个单独常驻内存的class中。（原文：Each DAO provides create, delete, update, etc. type of functionality and specializes in the handling a single persisted class. ）一个简单的创建DAO的方法就是用DaoManager中的静态方法createDao进行创建。例如，创建一个上面声明的Account类的DAO，你可以按如下操作：

```
     Dao<Account, String> accountDao =       DaoManager.createDao(connectionSource, Account.class);     Dao<Order, Integer> orderDao =       DaoManager.createDao(connectionSource, Order.class);
```

更多关于构建DAO的内容，请参考本手册后面的章节。请参考[Section 2.4\[DAO Setup\],page 19](#point)。

###1.4 代码示例

这个例子用本地的Java H2 数据库创建一个存在于内存的测试数据库。如果你想运行这个例子，你需要下载这个H2 jar 并且将其添加到你的classpath下。请参考[H2 home page](#)。*注意：*Android使用者需要阅读手册后面Android特定的文档。请参考[Chapter 4\[Use With Android\],page 49]()。那里也有完整的示例代码可以参考使用。请参考[ Chapter 7 \[Examples\], page 78]()

示例代码执行了下面的步骤
>* 它创建了一个连接源用户处理与数据库之间的连接。
* 它为Account对象实例化了一个DAO。
>* accounts 数据库表创建完成。如果这个表已经存在，这一步可以省略。

```
public class AccountApp {         public static void main(String[] args) throws Exception {
             // this uses h2 by default but change to match your database             String databaseUrl = "jdbc:h2:mem:account";             // create a connection source to our database             ConnectionSource connectionSource =                 new JdbcConnectionSource(databaseUrl);             // instantiate the dao             Dao<Account, String> accountDao =                 DaoManager.createDao(connectionSource, Account.class);             // if you need to create the ’accounts’ table make this call             TableUtils.createTable(connectionSource, Account.class);
             
             Once we have configured our database objects, we can use them to persist an Account to the database and query for it from the database by its ID:

             // create an instance of Account             Account account = new Account();             account.setName("Jim Coakley");             // persist the account object to the database             accountDao.create(account);             // retrieve the account from the database by its id field (name)             Account account2 = accountDao.queryForId("Jim Coakley");             System.out.println("Account: " + account2.getName());             // close the connection source             connectionSource.close();         }}
```

通过这个点的学习，你需要有能力开始使用ORMLite。（原文：You should be able to get started using ORMLite by this point.）想了解ORMLite更多的功能，请继续阅读下一章节。请参考 [Chapter 2 \[Using\], page 5.]()

需要更多的可执行的代码示例和Android工程，请参考[ Chap- ter 7 \[Examples\], page 78.]()



## 怎么使用

这一章将讲述关于 ORMLite 多种特性的细节。

### 2.1 构建你的 Class

如果需要讲你的class持久化存储，你需要做下面几件事：

- 添加 @DatabaseTable 注解到每一个calss的顶部。你也可以使用@Entity。
- 在每个字段被持久化存储之前，给其添加 @DatabaseField 注解。你也可以使用@Column 或者其他的。
- 给每一个class添加一个无参的构造方法，访问声明至少是包可见（也就是不能为private）。


#### 2.1.1 添加 ORMLite 注解

Java 5 以后就开始支持注解，它提供了关于类、方法和字段的元信息的特殊标记。ORMLite 提供了自己的注解（@DatabaseTable 和 DatabaseField）或者 javax.persitence 包里标准的注解。请参考[ Section 2.1.2 \[Javax Persistence Annotations\], page 11]()。注解是配置你的 class 最容易的方式，但是你也可以用 Java 代码和 Spring XML 配置。请参考[ Section 5.2 \[Class Configuration\], page 56]()。

使用 ORMlite 注解时，每一个你持久化到数据库的 Java class ，你都需要正确的添加 @DatabaseTable 注解到 public class 这一行的上面。每一个被这类注解标记的 class 持久化到数据库中时都会被创建一个自己的 table 。例如：
```
 	@DatabaseTable(tableName = "accounts")     public class Account {     ...
```

@DatabaseTable 注解有一个可选参数 tableName 用于指定和该 class 相关联的数据库表。如果没有指定 tableName 这个参数，根据规范 class 的名字将作为默认值。上面的例子中，每一个 Account 对象都会作为一行持久化到数据库的表中。如果 tableName 如果没有被指定，account 将会作为表名。（原文：If the tableName was not specified, the account table would be used instead.）

更多高级开发者也许希望添加一个 daoClass 参数用户指定操作这个 class 的 DAO 对象。实例化 DAO 是通过 DaoManager 内部进行的。请参考[\[DaoManager\], page 19.]()。

另外，在每一个 class 中，你还需要给每一个你需要持久化到数据库的字段添加 @DatabaseField 注解。每一个字段都会作为数据库表中一行的一列被持久化到数据库。例如：

```
	  @DatabaseTable(tableName = "accounts")      public class Account {         @DatabaseField(id = true)         private String name;         @DatabaseField(canBeNull = false)         private String password;         ...

```

在上面的例子中，account 表中的每一个行都有两列：

- name 是一个 string 类型的列，并且也是数据库中的一行的 id（identity）
- password 也是一个 string 类型的列，可以为 null 。

@DatabaseField 注解可以有下面这些属性字段：

**columnName**

可以通过这个属性，设置该字段在数据库中的列名。（原文：String name of the column in the database that will hold this field.）如果没有设置该字段，根据规范，field 的名字将设置为列名。

**dataType**

设置字段的类型，是一个 DataType 类对象。通常情况下，整个类型都是通过字段的Java类型设定，不需要单独指定。此属性和SQL类型一致。请参考[See Section 2.2 \[Persisted Types\], page 14.]()

**defaultValue**

当我们在数据库中创建一行新记录时的默认值。默认是空。

**width**

字段的 Integer 宽度，主要是作用于 string 类型的字段。默认值是0，代表设置 data-type 和 database-specific 为默认值。如果该字段是字符串则默认值为255个字符串，虽然一些数据库不支持这个属性。（原文：Default is 0 which means to take the data-type and database-specific default. For strings that means 255 characters although some databases do not support this.）

**canBeNull**

Boolean 类型的属性值，用于指定该字段是否可以为null。默认值是true。如果设置为 false ，当每一个对象插入数据库时，你都需要给该字段提供一个值。

**id**

Boolean 类型的属性，控制此字段是否为数据库表的id字段。默认值是 false 。一个 class 中只有一个字段可以设置这个属性。id 属性字段唯一标志数据库表的一行，并且如果你有通过id执行query、update、refresh 和 delete 操作，此属性是必须的。这个属性和generatedId, generatedIdSequence 三者只能指定一个。请参考[Section 2.8.1 \[Id Column\], page 24.]()

**generatedId**

Boolean 类型的属性，控制此字段是否为一个自动生成的id字段。默认是false。一个 class 中只有一个字段可以设置该属性。这个属性告诉数据库给每一行插入的记录自动生成一个相应的id。当一个对象用 Dao.create() 生成了一个id，数据库就会生成为将要被返回的这一行生成一个id，并且将此id通过这个create方法设置到该对象中。（原文：When an object with a generated- id is created using the Dao.create() method,the database will generate an id for the row which will be returned and set in the object by the create method. ）有一些数据库需要自动生成的id的序列，在这种情况下，序列的名会被自动创建。通过 generatedIdSequence 可以指定序列的名字。generatedId 、 id 和 generatedIdSequence 三个属性只有一个可以被设置。请查阅[ Section 2.8.2 \[GeneratedId Column\], page 25.]()

**generatedIdSequence**

将使用序列号的字符串类型名字作为它的值。（原文：String name of the sequence number to be used to generate this value.）此属性和 generatedId 属性功能相同，但是你可以指定使用的序列名字。默认值为空。一个class中只有一个字段可以设置此属性。只有当数据库需要生成的id的序列时才需要此属性。如果你使用 generatedId 替代此属性，序列名会被自动创建。generatedIdSequence 、 id 和 generatedId 三个属性只有一个可以被设置。 请查阅[Section 2.8.3 \[GeneratedIdSequence Column\], page 25.]()

**foreign**

Boolean 类型属性，用于指定此字段和数据库中存储的另外一个class相对应关联。默认值是false。这个属性必须是一个基本类型。另外一个被关联的class必须有一个id字段（id、generatedId 或者 generatedIdSequence）存储到这个数据库表中。当一次查询返回一个对象时，这个外键对象只有id字段会被赋值。请参考[Section 2.12 \[Foreign Objects\], page 32]()

**useGetSet**

Boolean 类型属性，告诉ORMLite需要通过 get 和 set 方法访问此字段。默认值是false，使用Java反射代替直接访问该字段。如果你要存储的对象有访问保护，这个属性是必要的。

*NOTE：* get 方法名字必须符合 getXxx() 格式，这个字段的名字首字母大写就是Xxx。 get 方法*必须*返回一个与这个字段完全匹配的class。和此字段完全匹配的class的set方法*必须*符合setXxx()格式，并且返回值为void。例如：

```
@DatabaseField(useGetSet = true)     private Integer orderCount;     public Integer getOrderCount() {       return orderCount;}     public void setOrderCount(Integer orderCount) {       this.orderCount = orderCount;}
```

**unknownEnumName**

当这个字段是Java的枚举类型，如果数据库表中一行记录的值在枚举类型中没有找到，你可以指定这个枚举值的名字。（原文：If the field is a Java enumerated type then you can specify the name of a enumerated value which will be used if the value of a database row is not found in the enumerated type.）当读取数据库表中一行记录时，如果*含有*一个不可识别的列名或者序列号值（原文：contain an unknown name or ordinal value），并且这个属性没有被设置，那么就会抛出一个SQLException。当向前兼容处理数据库中过期数据，或者向前兼容老软件访问最新数据时，或者你必须回滚到一个稳定版本时，这个属性是非常有用的。

**throwIfNull**

Boolean类型属性，告诉ORMLite一个基本类型字段试图存储null值到数据库时，是否抛出异常。默认值是false。当此属性为false，并且数据库字段是null时，这个基本类型字段的值会被设置为0（false，null 或者其他）。这个属性只能被用于基本类型字段。

**persisted**

如果不需要存储这个字段到数据库，则设置此属性的值为false（默认是true）。当你想给每个字段都添加这个注解，但是想把一部分字段不存储到数据库，这个属性是很有用的。

**format**

这个属性允许给特殊字段指定格式化信息。目前只有以下两种类型可用：
- DATE_STRING 用于格式化存储到数据库中的时间字符串。
- STRING_BYTES 用于指定一个编码string字符串为bteys数组的Charset字符集。（原文：STRING_BYTES for specifying the Charset used to encode the string as an array of bytes）

**unique**

给表添加一个约束，指定此字段在表的所有行中唯一。例如：你有一个Account 类，它生成了一个唯一的account_id,但是你还需要一个email地址字段在所有的 Account 对象中唯一。如果一个表中有多个字段被标记为唯一，他们每一个都要保证自己在同类字段中唯一。例如：如果你有 firstName 和 lastName 两个字段都配置了 unique=true 属性，并且数据库中有一个“Bob”，“Smith”的记录，那么“Bob”，“Jones”和“Kevin”，“Smith”都无法存储到数据库。

一个以上字段的*combination*(组合？)需要保证各自唯一，请查阅 uniqueCombo 的设置。你也可以使用 uniqueIndexName 给这个字段设置一个index。

**uniqueCombo**

添加一个表的约束，用于保证组合中所有字段都设置 uniqueCombo 为true时，它必须是表中所有行唯一。（原文：Adds a constraint to the table so that a combination of all fields that have uniqueCombo set to true has to be unique across all rows in the table.）例如：如果你有 firstName 和 lastName 字段，都设置了 uniqueCombo=true，并且数据库中已经存储了“Bob”，“Smith”，你无法再添加另一个“Bob”，“Smith”，但是可以插入“Bob”，“Jones” 和 “Kevin”，“Smith”。

如果想保证多个字段各自唯一，请查看 unique 设置。你也可以使用 uniqueIndexName 给这个字段创建一个索引。

**index**

Boolean类型的属性（默认值是false），通知数据库给这个字段添加一个索引。当配置index=true，数据库会创建一个列名加“_inx”后缀的索引。可以通过 indexName 指定创建索引的名字，或者索引多个字段。（原文：To specify a specific name of the index or to index multiple fields, use the indexName field.） 

**uniqueIndex**

Boolean类型的属性（默认值是false），用于指定数据库给这个字段添加唯一索引。作用和index类似，但是它可以确保这个index中的所有值都唯一。如果只是为了保证字段的唯一性，可以使用 unique 代替此属性。

**indexName**

String 类型的属性（默认值是空），用于指定数据库给这个字段添加索引的名字。有了此属性，就不需要再指定 index 的 boolean 值了。如果需要创建多个字段联合索引，那么每个字段都需要设置相同的 indexName。

**uniqueIndexName**

String 类型的属性（默认是空），用于指定数据库给这个字段段添加唯一索引的名字。和 index 属性的功能类似，不过此属性会保证该索引下的所有值唯一。例如：在此属性为true时，你可以插入（"pittsburgh", "pa") 、 ("harrisburg", "pa") 和 ("pittsburgh", "tx") ，但是无法插入另一个 ("pittsburgh", "pa")。

**foreignAutoRefresh**

设置此属性为true（默认值是false），可以使查询对象时，自动刷新外键字段。为默认值时只会
在检索对象中设置ID字段，调用方需要自己通过正确的DAO调用refresh来填充对象的其他字段。如果这个属性被设置为true，当查询一个对象时，会通过一个内部的DAO开启另外一个数据库查询用于填充外键对象的其他字段。*注意：*在创建一个对象时，此属性为true也不会自动创建外键对象。

*注意：*自动填充外键对象会另外创建一个内部DAO对象，所以在低内存设备上也许需要手动的调用refresh。

*注意：*为了防止递归，有一些地方的自动刷新是被限制执行的。如果你 auto—refreshing 一个字段已经设置了 foreignAutoRefresh=true 的类，或者你 auto—refreshing 一个有集合外键的类，在这些情况下，这些字段会被设置为null并且不会执行 auto—refreshed。（原文：If you are auto-refreshing a class that itself has field with foreignAutoRefresh set to true or if you are auto-refreshing a class with a foreign collection, in both cases the resulting field will be set to null and not auto-refreshed.）你还可以直接在字段上调用refresh。

*注意：*如果你有一个 auto-refreshed 字段是一个对象，这个对象也有一个 auto-refreshed 字段，你应该调整 maxForeignAutoRefreshLevel 的值。请看下面。

**maxForeignAutoRefreshLevel**

这个属性可以设置配置外键的层级最大值。举个例子，如果一个Question，有一个外键字段是最好的 Answer，并且这个 Answer 有一个外键是和它匹配的 question，这个配置前后循环会变得很大。当你查询一个 Question 时，有一个 auto-refreshed 字段就是一个严重的问题，它会引起无限循环。默认情况下，ORMLite 只允许2级循环，但是你可以将其减少为1（0是非法的）或者增加它。当你加载 Question 时，这个数字越高，数据库的事务发生的就会越频繁。

在我们的例子中，Question 和 Answer 中的外键都可以设置为 auto-refresh。如果auto-refresh设置为true，并且 maxForeignAutoRefreshLevel 设置为1时，当你查询一个 Question ，它的 Answer 字段会被 auto-refreshed，但是 Answer 中的 Question 字段*只有*id字段会被赋值。它不会被自动填充。




















