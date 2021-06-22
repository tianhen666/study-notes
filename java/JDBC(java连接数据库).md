#### 什么是JDBC

- JDBC(Java Database Connectivity)是一个独立于特定数据库管理系统、通用的SQL数据库存取和操作的公共接口（一组API），定义了用来访问数据库的标准Java类库，（java.sql,javax.sql）使用这些类库可以以一种标准的方法、方便地访问数据库资源。

- JDBC为访问不同的数据库提供了一种统一的途径，为开发者屏蔽了一些细节问题。

- JDBC的目标是使Java程序员使用JDBC可以连接任何提供了JDBC驱动程序的数据库系统，这样就使得程序员无需对特定的数据库系统的特点有过多的了解，从而大大简化和加快了开发过程。
- 有JDBC，那么Java程序访问数据库时是这样的：

![JDBC1](../img/JDBC1.png)



[管理mySQL图形界面软件](https://sqlyog.en.softonic.com/)

[参考文档一](https://blog.csdn.net/qq_40280582/article/details/103700466)

需要包支持：**mysql-java-api.jar**



#### JDBC体系结构

JDBC接口（API）包括两个层次：

+ **面向应用的API**：Java API，抽象接口，供应用程序开发人员使用（连接数据库，执行SQL语句，获得结果）。
+ **面向数据库的API**：Java Driver API，供开发商开发数据库驱动程序用。

> JDBC是sun公司提供一套用于数据库操作的接口，java程序员只需要面向这套接口编程即可。
>
> 不同的数据库厂商，需要针对这套接口，提供不同实现。不同的实现的集合，即为不同数据库的驱动。 ————面向接口编程

#### JDBC程序编写步骤

![JDBC2](../img/JDBC2.png)

> 补充：ODBC(**Open Database Connectivity**，开放式数据库连接)，是微软在Windows平台下推出的。使用者在程序中只需要调用ODBC API，由 ODBC 驱动程序将调用转换成为对特定的数据库的调用请求。

#### JDBC链接数据库的几种方式

##### 方式一

```java
@Test
public void testConnection1() {
    try {
        //1.提供java.sql.Driver接口实现类的对象
        Driver driver = null;
        driver = new com.mysql.jdbc.Driver();

        //2.提供url，指明具体操作的数据
        String url = "jdbc:mysql://localhost:3306/test";

        //3.提供Properties的对象，指明用户名和密码
        Properties info = new Properties();
        info.setProperty("user", "root");
        info.setProperty("password", "abc123");

        //4.调用driver的connect()，获取连接
        Connection conn = driver.connect(url, info);
        System.out.println(conn);
    } catch (SQLException e) {
        e.printStackTrace();
    }
}

```

> 说明：上述代码中显式出现了第三方数据库的API

##### 方式二

```java
@Test
public void testConnection2() {
    try {
        //1.实例化Driver
        String className = "com.mysql.jdbc.Driver";
        Class clazz = Class.forName(className);
        Driver driver = (Driver) clazz.newInstance();

        //2.提供url，指明具体操作的数据
        String url = "jdbc:mysql://localhost:3306/test";

        //3.提供Properties的对象，指明用户名和密码
        Properties info = new Properties();
        info.setProperty("user", "root");
        info.setProperty("password", "abc123");

        //4.调用driver的connect()，获取连接
        Connection conn = driver.connect(url, info);
        System.out.println(conn);

    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

> 说明：相较于方式一，这里使用反射实例化Driver，不在代码中体现第三方数据库的API。体现了面向接口编程思想。

##### 方式三

```java
@Test
public void testConnection3() {
    try {
        //1.数据库连接的4个基本要素：
        String url = "jdbc:mysql://localhost:3306/test";
        String user = "root";
        String password = "abc123";
        String driverName = "com.mysql.jdbc.Driver";

        //2.实例化Driver
        Class clazz = Class.forName(driverName);
        Driver driver = (Driver) clazz.newInstance();
        //3.注册驱动
        DriverManager.registerDriver(driver);
        //4.获取连接
        Connection conn = DriverManager.getConnection(url, user, password);
        System.out.println(conn);
    } catch (Exception e) {
        e.printStackTrace();
    }

}
```

> 说明：使用DriverManager实现数据库的连接。体会获取连接必要的4个基本要素。

##### 方式四

```java
@Test
public void testConnection4() {
    try {
        //1.数据库连接的4个基本要素：
        String url = "jdbc:mysql://localhost:3306/test";
        String user = "root";
        String password = "abc123";
        String driverName = "com.mysql.jdbc.Driver";

        //2.加载驱动 （①实例化Driver ②注册驱动）
        Class.forName(driverName);


        //Driver driver = (Driver) clazz.newInstance();
        //3.注册驱动
        //DriverManager.registerDriver(driver);
        /*
            可以注释掉上述代码的原因，是因为在mysql的Driver类中声明有：
            static {
                try {
                    DriverManager.registerDriver(new Driver());
                } catch (SQLException var1) {
                    throw new RuntimeException("Can't register driver!");
                }
            }

             */


        //3.获取连接
        Connection conn = DriverManager.getConnection(url, user, password);
        System.out.println(conn);
    } catch (Exception e) {
        e.printStackTrace();
    }

}
```

> 说明：不必显式的注册驱动了。因为在DriverManager的源码中已经存在静态代码块，实现了驱动的注册。

##### 方式五（最终版）

```java
@Test
public  void testConnection5() throws Exception {
    //1.加载配置文件
    InputStream is = ConnectionTest.class.getClassLoader().getResourceAsStream("jdbc.properties");
    Properties pros = new Properties();
    pros.load(is);

    //2.读取配置信息
    String user = pros.getProperty("user");
    String password = pros.getProperty("password");
    String url = pros.getProperty("url");
    String driverClass = pros.getProperty("driverClass");

    //3.加载驱动
    Class.forName(driverClass);

    //4.获取连接
    Connection conn = DriverManager.getConnection(url,user,password);
    System.out.println(conn);

}

```

其中，配置文件声明在工程的src目录下：【jdbc.properties】

```properties
user=root
password=abc123
url=jdbc:mysql://localhost:3306/test
driverClass=com.mysql.jdbc.Driver
```

> 说明：使用配置文件的方式保存配置信息，在代码中加载配置文件
>
> **使用配置文件的好处：**
>
> ①实现了代码和数据的分离，如果需要修改配置信息，直接在配置文件中修改，不需要深入代码
> ②如果修改了配置信息，省去重新编译的过程。

#### JDBC事务



