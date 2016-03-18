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




