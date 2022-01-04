# MYSQL

- 下载地址
    https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.19-winx64.zip

1. 解压到本地

2. 配置环境变量
               

    选择PATH,在其后面添加: 你的mysql 安装文件下面的bin文件夹

3. 新建配置文件.ini结尾

skip-grant-tables 第一次安装的时候跳过密码验证


    [mysqld]
    basedir=D:\mysql-5.7\
    datadir=D:\mysql-5.7\data\
    port=3306
    skip-grant-tables

4. 启动管理员模式下的CMD，并将路径切换至mysql下的bin目录，然后输入mysqld –install (安装mysql)

开始->window系统—>命令提示符->右键以管理员身份执行

5. 再输入  mysqld --initialize-insecure --user=mysql 初始化数据文件。新建了data目录

6. net start mysql启动mysql

7. mysql -u root -p用root用户登录，-p后面不能有空格【重点】

8. 进入界面后修改密码
   update mysql.user set authentication_string=password('123456') where user='root' and Host = 'localhost';

9. 刷新权限  --flush privileges;

10. 修改 my.ini文件删除最后一句skip-grant-tables

11. 重启mysql即可正常使用

```linux
net stop mysql
net start mysql
```
12. mysql -uroot -p123456 登录一下测试，出现以下就是成功了。
    ![img.png](img.png)

## select

```sql
    SELECT [ALL | DISTINCT]
    {* | table.* | [table.field1[as alias1][,table.field2[as alias2]][,...]]}
	FROM table_name [as table_alias]
    [left | right | inner join table_name2]  -- 联合查询
	[WHERE ...]  -- 指定结果需满足的条件
	[GROUP BY ...]  -- 指定结果按照哪几个字段来分组
	[HAVING]  -- 过滤分组的记录必须满足的次要条件
	[ORDER BY ...]  -- 指定查询记录按一个或多个条件排序
	[LIMIT {[offset,]row_count | row_countOFFSET offset}];
	-- 指定查询的记录从哪条至哪条
```



## 事务

> 什么是事务

- 事务就是将一组SQL语句放在同一批次内去执行
- 如果一个SQL语句出错，则该批次内的所有SQL都将被取消执行
- MYSQL事务处理只支持InnoDB和BDB数据表类型

> 事务的ACID原则

### 原子性（Atomic）

+ 整个事务中的所有操作，要么全部完成，要么全部不完成，不能停滞在中间某个环节。事务在执行过程中发生错误，会被回滚(ROLLBACK)到事务开始前的状态，就像这个事务从来没有执行过一样。

### 一致性（Consist）

+ 一个事务可以封装状态改变（除非它是一个只读的）。事务必须始终保持系统处于一直的状态，不管在任何给定的时间并发事务有多少。也就是说：如果事务是并发多个，系统必须如同串行事务一样操作。其主要特征是保护性和不变性（Preserving and Invariant）,以转账案例为例，假设有五个账户，每个账户余额是100元，那么五个账户总额是500元，如果在这5个账户之间同时发生过个转账，无论并发多少个，比如在A与B之间转账5元，在C与D之间转账10元，在B与E之间转账15元，五个账户总额应该还是500元，这就是保护性和不变性。

### 隔离性（Isolated）

+ 隔离状态执行事务，使他们好像是系统在给定时间内执行的唯一操作。如果有两个事务，运行在相同的时间内，执行相同的功能，事务的隔离性将确保每一事务在系统中认为只有该事务在使用系统。这种属性有时称为串行化，为了防止事务操作间的混淆，必须串行化或序列化请求，使得在同一时间内仅有一个请求用于同一数据。

### 持久性（Durable）

- 在事务完成以后，该事务对数据库所作的更改便持久的保存在数据库中，并不会被回滚。

> 基本语法

--查询自动提交的开启状态

```
show VARIABLES LIKE 'autocommit'
```

![1640677141229](C:\Users\a\AppData\Roaming\Typora\typora-user-images\1640677141229.png)	



```sql
set autocommit = 0; /*关闭*/
SET autocommit = 1; /*开启*/

-- 开始一个事务，标记事务的起始点
START TRANSACTION

- 提交一个事务给数据库
COMMIT

-- 将事务回滚，数据回到本次事务的初始状态
ROLLBACK

SET autocommit = 1;

-- 保存点
SAVEPOINT 保存点名称 -- 设置一个事务保存点
ROLLBACK TO SAVEPOINT 保存点名称 -- 回滚到保存点
RELEASE SAVEPOINT 保存点名称 -- 删除保存点


```



## 索引



>  MySQL官方对索引的定义为：索引（Index）是帮助MySQL高效获取数据的数据结构。
>
> 提取句子主干，就可以得到索引的本质：索引是数据结构。 

### 索引的分类

+ 主键索引(Primary)
  - 唯一的标识，主键不可重复，只能有一个列作为主键
+ 唯一索引(Unique)
  - 避免重复的列出现，唯一索引可以重复，多个列都可以标识为唯一索引
+ 常规索引(Index)
  - 默认的，index。key关键字来设置
+ 全文索引(FullText)
  - 在特定的数据库引擎下才有，MyISAM
  - 快速定位数据

**基本语法**

```sql
/*
#方法一：创建表时
  　　CREATE TABLE 表名 (
               字段名1 数据类型 [完整性约束条件…],
               字段名2 数据类型 [完整性约束条件…],
               [UNIQUE | FULLTEXT | SPATIAL ]   INDEX | KEY
               [索引名] (字段名[(长度)] [ASC |DESC])
               );


#方法二：CREATE在已存在的表上创建索引
       CREATE [UNIQUE | FULLTEXT | SPATIAL ] INDEX 索引名
                    ON 表名 (字段名[(长度)] [ASC |DESC]) ;


#方法三：ALTER TABLE在已存在的表上创建索引
       ALTER TABLE 表名 ADD [UNIQUE | FULLTEXT | SPATIAL ] INDEX
                            索引名 (字段名[(长度)] [ASC |DESC]) ;
                           
                           
#删除索引：DROP INDEX 索引名 ON 表名字;
#删除主键索引: ALTER TABLE 表名 DROP PRIMARY KEY;


#显示索引信息: SHOW INDEX FROM student;
*/

/*增加全文索引*/
ALTER TABLE `school`.`student` ADD FULLTEXT INDEX `studentname` (`StudentName`);

/*EXPLAIN : 分析SQL语句执行性能*/
EXPLAIN SELECT * FROM student WHERE studentno='1000';

/*使用全文索引*/
-- 全文搜索通过 MATCH() 函数完成。
-- 搜索字符串作为 against() 的参数被给定。搜索以忽略字母大小写的方式执行。对于表中的每个记录行，MATCH() 返回一个相关性值。即，在搜索字符串与记录行在 MATCH() 列表中指定的列的文本之间的相似性尺度。
EXPLAIN SELECT *FROM student WHERE MATCH(studentname) AGAINST('love');

```

> 测试索引

```sql
CREATE TABLE `app_user` (
`id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
`name` varchar(50) DEFAULT '',
`email` varchar(50) NOT NULL,
`phone` varchar(20) DEFAULT '',
`gender` tinyint(4) unsigned DEFAULT '0',
`password` varchar(100) NOT NULL DEFAULT '',
`age` tinyint(4) DEFAULT NULL,
`create_time` datetime DEFAULT CURRENT_TIMESTAMP,
`update_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

插入100万条数据

```sql
-- 测试查询
EXPLAIN select * from app_user where name = '用户99999'

-- CREATE INDEX 索引名 on 表名
CREATE INDEX id_app_user_name on app_user(`NAME`);
```

![1640852392711](C:\Users\a\AppData\Roaming\Typora\typora-user-images\1640852392711.png)

![1640852573907](C:\Users\a\AppData\Roaming\Typora\typora-user-images\1640852573907.png)

加了索引后查询明显变快。

![1640852852574](C:\Users\a\AppData\Roaming\Typora\typora-user-images\1640852852574.png)

![1640852867477](C:\Users\a\AppData\Roaming\Typora\typora-user-images\1640852867477.png)

### 索引原则

- 索引不是越多越好
- 不要对经常变动的数据加索引
- 小数据量的数据不需要加索引
- 索引一般加速常用来查询的字段上



> 索引的数据结构

hash:类型的索引

Btree: InnoDB的默认数据结构

[索引的数据结构](http://blog.codinglabs.org/articles/theory-of-mysql-index.html)





## JDBC(重点)

### 数据库驱动

#### 创建一个数据库

```sql
CREATE DATABASE jdbcStudy CHARACTER SET utf8 COLLATE utf8_general_ci;

USE jdbcStudy;

CREATE TABLE `users`(
	id INT PRIMARY KEY,
	NAME VARCHAR(40),
	PASSWORD VARCHAR(40),
	email VARCHAR(60),
	birthday DATE
);

INSERT INTO `users`(id,NAME,PASSWORD,email,birthday)
VALUES(1,'zhansan','123456','zs@sina.com','1980-12-04'),
(2,'lisi','123456','lisi@sina.com','1981-12-04'),
(3,'wangwu','123456','wangwu@sina.com','1979-12-04')
```

```java
public class jdbcTest {
  public static void main(String[] args) throws ClassNotFoundException, SQLException {
        //加载驱动
        Class.forName("com.mysql.jdbc.Driver");
        //用户信息和地址
        String url = "jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true";
        String username = "root";
        String password = "123456";
        //连接成功，数据库对象Connection代表数据库
        Connection connection = DriverManager.getConnection(url, username, password);

        //执行SQL的对象去执行SQL
        Statement statement = connection.createStatement();
        String sql = "select * from users";
        //返回的结果集
        ResultSet resultSet = statement.executeQuery(sql);/
        while (resultSet.next()) {
            System.out.println("id" + resultSet.getString("id"));
        }
		//释放连接
        resultSet.close();
        statement.close();
        connection.close();
    }
}

```

#### jdbc工具类

```java
package com.Util;

import java.io.IOException;
import java.io.InputStream;
import java.net.URL;
import java.sql.*;
import java.util.Properties;

public class jdbcUtil {
    private static String driver = null;
    private static String url = null;
    private static String username = null;
    private static String password = null;
    static {
        try {
            InputStream in = jdbcUtil.class.getClassLoader().getResourceAsStream("./db.properties");
            Properties properties = new Properties();
            properties.load(in);

            driver = properties.getProperty("driver");
            url = properties.getProperty("url");
            username = properties.getProperty("username");
            password = properties.getProperty("password");

            Class.forName(driver);
        }catch (IOException e){
            e.printStackTrace();
        }catch (ClassNotFoundException e){
            e.printStackTrace();
        }
    }

    public static Connection getConnection(){
        try {
           return DriverManager.getConnection(url,username,password);
        } catch (SQLException e) {
            e.printStackTrace();
            return null;
        }
    }

    public static void release(Connection connection, Statement statement, ResultSet resultSet){
        try {
            resultSet.close();
            statement.close();
            connection.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

```

JDBC 操作事务

```java
package com.wujq;

import com.Util.jdbcUtil;

import java.sql.*;

public class jdbcTranaction {
    public static void main(String[] args) {
        Connection connection = null;
        PreparedStatement statement = null;
        ResultSet set = null;
        boolean flag = false;
        try {
            connection = jdbcUtil.getConnection();
            //关闭自动提交,会自动开启事务
            connection.setAutoCommit(false);
            String sql1 = "update account set  cash = cash+500 where name = 'A'";
            statement = connection.prepareStatement(sql1);
            int num = statement.executeUpdate();
            if(num > 0){
                flag = true;
            }

            String sql2 = "update account set  cash = cash-500 where name = 'B'";
            statement = connection.prepareStatement(sql2);
            num = statement.executeUpdate();
            if(num > 0){
                //测试业务失败，回滚
                flag = false;
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            try {
                if(flag){
                    //业务执行完毕，提交事务
                    connection.commit();
                }else{
                    //业务执行失败，回滚事务
                    connection.rollback();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}

```



## 数据库连接池

数据库---执行完毕---释放 

连接--释放 十分浪费系统资源

> 池化技术

**池化技术:准备一些预先的资源,过来就连接预先准备好的**

例如一个银行:

----开门--业务员---服务 ,又来一个人又开门-然后业务员服务,然后关门显然是不合理的。

应该是： 开门---业务员：等待---服务--

最小连接数：业务员的最少个数，根据需求来判断。根据常用的办理业务数。

最大连接数：业务最高承载上限。

排队等待：如果超过最大连接数就会等待。

等待超时：100 ms。不能一直等待

> 开源数据源实现

DBCP

C3P0

Druid: 阿里巴巴



使用了这些数据池后就不需要自己在项目中编写连接数据库的代码了。

> #### DBCP

需要用到的jar包

```xml
<!-- https://mvnrepository.com/artifact/commons-dbcp/commons-dbcp -->
<dependency>
    <groupId>commons-dbcp</groupId>
    <artifactId>commons-dbcp</artifactId>
    <version>1.4</version>
</dependency>
<!-- https://mvnrepository.com/artifact/commons-pool/commons-pool -->
<dependency>
    <groupId>commons-pool</groupId>
    <artifactId>commons-pool</artifactId>
    <version>1.6</version>
</dependency>
```

#### DBCP配置文件

```properties
#连接设置
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true
username=root
password=123456

#<!-- 初始化连接 -->
initialSize=10

#最大连接数量
maxActive=50

#<!-- 最大空闲连接 -->
maxIdle=20

#<!-- 最小空闲连接 -->
minIdle=5

#<!-- 超时等待时间以毫秒为单位 6000毫秒/1000等于60秒 -->
maxWait=60000
#JDBC驱动建立连接时附带的连接属性属性的格式必须为这样：【属性名=property;】
#注意："user" 与 "password" 两个属性会被明确地传递，因此这里不需要包含他们。
connectionProperties=useUnicode=true;characterEncoding=UTF8

#指定由连接池所创建的连接的自动提交（auto-commit）状态。
defaultAutoCommit=true

#driver default 指定由连接池所创建的连接的只读（read-only）状态。
#如果没有设置该值，则“setReadOnly”方法将不被调用。（某些驱动并不支持只读模式，如：Informix）
defaultReadOnly=

#driver default 指定由连接池所创建的连接的事务级别（TransactionIsolation）。
#可用值为下列之一：（详情可见javadoc。）NONE,READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE
defaultTransactionIsolation=READ_UNCOMMITTED
```

#### DBCP工具类

```java
package com.Util;

import java.io.IOException;
import java.io.InputStream;
import java.net.URL;
import java.sql.*;
import java.util.Properties;

public class jdbcUtil {
    private static String driver = null;
    private static String url = null;
    private static String username = null;
    private static String password = null;
    static {
        try {
            InputStream in = jdbcUtil.class.getClassLoader().getResourceAsStream("./db.properties");
            Properties properties = new Properties();
            properties.load(in);

            driver = properties.getProperty("driver");
            url = properties.getProperty("url");
            username = properties.getProperty("username");
            password = properties.getProperty("password");

            Class.forName(driver);
        }catch (IOException e){
            e.printStackTrace();
        }catch (ClassNotFoundException e){
            e.printStackTrace();
        }
    }

    public static Connection getConnection(){
        try {
           return DriverManager.getConnection(url,username,password);
        } catch (SQLException e) {
            e.printStackTrace();
            return null;
        }
    }

    public static void release(Connection connection, Statement statement, ResultSet resultSet){
        try {
            resultSet.close();
            statement.close();
            connection.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

```

#### DCBP测试类

```java
package com.wujq;


import com.Util.DbcpUtil;

import java.sql.*;
import java.util.Date;

public class dbcpTest {
    public static void main(String[] args) {
        Connection collection = null;
        PreparedStatement statement = null;
        ResultSet resultSet = null;
        try{
            collection = DbcpUtil.getConnection();
            String sql = "insert into users (id,`name`,`password`,`email`,`birthday`) VALUES (?,?,?,?,?)";
            statement = collection.prepareStatement(sql);
            statement.setInt(1,4);
            statement.setString(2,"吴佳琦");
            statement.setString(3,"1221");
            statement.setString(4,"1221@qq.com");
            statement.setDate(5,new java.sql.Date(System.currentTimeMillis()));
            int i = statement.executeUpdate();
            if(i > 0){
                System.out.println("执行成功");
            }

            //statement = collection.createStatement();
            //resultSet = statement.executeQuery("select * from users");
            //while (resultSet.next()) {
            //    System.out.println("id" + resultSet.getString("id"));
            //}

        }catch (Exception e){
            e.printStackTrace();
        }finally {
            DbcpUtil.release(collection,statement,resultSet);
        }
    }
}
```

> C3P0

C3P0 jar包

```xml
<!-- https://mvnrepository.com/artifact/com.mchange/c3p0 -->
<dependency>
    <groupId>com.mchange</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.5.5</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.mchange/mchange-commons-java -->
<dependency>
    <groupId>com.mchange</groupId>
    <artifactId>mchange-commons-java</artifactId>
    <version>0.2.19</version>
</dependency>
```

#### C3P0 配置文件,==c3p0-config.xml==

```xml
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>
    <!--
c3p0的缺省（默认）配置
如果在代码中"ComboPooledDataSource ds=new ComboPooledDataSource();"这样写就表示使用的是c3p0的缺省（默认）-->
    <default-config>
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&amp;characterEncoding=utf8&amp;uesSSL=true</property>
        <property name="user">root</property>
        <property name="password">123456</property>

        <property name="acquiredIncrement">5</property>
        <property name="initialPoolSize">10</property>
        <property name="minPoolSize">5</property>
        <property name="maxPoolSize">20</property>
    </default-config>
    <!--
          c3p0的命名配置
        如果在代码中"ComboPooledDataSource ds=new ComboPooledDataSource("MySQL");"这样写就表示使用的是mysql的缺省（默认）-->
    <named-config name="MySQL">
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/jdbcstudy?userUnicode=true&amp;characterEncoding=utf8&amp;uesSSL=true&amp;serverTimezone=UTC</property>
        <property name="user">root</property>
        <property name="password">123456</property>

        <property name="acquiredIncrement">5</property>
        <property name="initialPoolSize">10</property>
        <property name="minPoolSize">5</property>
        <property name="maxPoolSize">20</property>
    </named-config>
</c3p0-config>
```

#### C3P0工具类

```java
package com.Util;

import com.mchange.v2.c3p0.ComboPooledDataSource;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class C3P0Util {
    static ComboPooledDataSource dataSource = null;
    static {
        try {
            //代码配置
            //dataSource = new ComboPooledDataSource();
            //dataSource.setDriverClass("");
            //dataSource.setJdbcUrl("");
            //dataSource.setUser("");
            //dataSource.setPassword("");
            //dataSource.setMaxPoolSize(1);
            //dataSource.setMinPoolSize(1);

            //创建数据源,工厂模式,配置文件写法
            dataSource = new ComboPooledDataSource("MySQL");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static Connection getConnection() throws SQLException {
        //从数据源中获取连接
        return dataSource.getConnection();
    }

    public static void release(Connection con, Statement st, ResultSet res){
        try {
            if (res != null){
                res.close();
            }
            if (con != null){
                con.close();
            }
            if (st != null){
                st.close();
            }
        } catch (Exception e){
            e.printStackTrace();
        }
    }
}

```



#### C3P0 测试类

```java
package com.wujq;

import com.Util.C3P0Util;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class C3P0Test {
    public static void main(String[] args) {
        Connection collection = null;
        PreparedStatement statement = null;
        ResultSet resultSet = null;
        try{
            collection = C3P0Util.getConnection();
            String sql = "insert into users (id,`name`,`password`,`email`,`birthday`) VALUES (?,?,?,?,?)";
            statement = collection.prepareStatement(sql);
            statement.setInt(1,5);
            statement.setString(2,"吴佳琦1");
            statement.setString(3,"12212");
            statement.setString(4,"122331@qq.com");
            statement.setDate(5,new java.sql.Date(System.currentTimeMillis()));
            int i = statement.executeUpdate();
            if(i > 0){
                System.out.println("执行成功");
            }

            //statement = collection.createStatement();
            //resultSet = statement.executeQuery("select * from users");
            //while (resultSet.next()) {
            //    System.out.println("id" + resultSet.getString("id"));
            //}

        }catch (Exception e){
            e.printStackTrace();
        }finally {
            C3P0Util.release(collection,statement,resultSet);
        }
    }
}

```

