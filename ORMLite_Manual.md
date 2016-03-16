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




##开始使用

以下内容可以帮助你开始使用ORMLite。Android使用者需要在阅读这些内容后，再阅读一下Android专题页。请查看[]








