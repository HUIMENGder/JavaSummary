# 什么是JDBC  
JDBC(Java Database Connectivity),他代表的是独立于任何一个数据库管理系统的API，申明在java.sql和javax.sql包中，是一组规范接口。由各种数据库厂商提供实现类，这些实现类的集合提供了数据库驱动jar。

***基于druid的JDBC使用实现***
```java
package cn;

import util.jdbcutil;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class jdbcDemo7 {

    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement ptemt = null;
        try {
            conn = jdbcutil.getConntction();
            System.out.println(conn);
            String sql= "insert into user values(null,?,?)";
            ptemt = conn.prepareStatement(sql);
            ptemt.setString(1,"wangwu");
            ptemt.setInt(2,1000);
            int count = ptemt.executeUpdate();
            System.out.println(count);
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }finally {
           jdbcutil.close(ptemt,conn);
        }

    }
}

```


```JAVA
package util;

import java.io.FileReader;
import java.io.IOException;
import java.sql.*;
import java.util.Properties;

/**
 * JDBC工具类
 */
public class JdbUtil {

    private static String url;
    private static String user;
    private static String password;
    private static String driver;
    /**
     * 文件的读取，只需要读取一次即可
     */
     static {

        try {
            Properties pro = new Properties();
            pro.load(new FileReader("src/jdbc.properties"));
            url = pro.getProperty("url");
            user = pro.getProperty("user");
            password = pro.getProperty("password");
            driver = pro.getProperty("driver");
            Class.forName(driver);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
    /**
     * 获取连接
     * 返回连接对象
     */
    public static Connection getConnection(){
        try {
            return DriverManager.getConnection(url,user,password);
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
        return null;
    }
    public static void close(ResultSet rs, Statement stmt , Connection conn){
        if(rs != null)
        {
            try {
                rs.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if(stmt != null)
        {
            try {
                stmt.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if(conn != null)
        {
            try {
                conn.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```
```
//druid.properties配置文件
driverClassName=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://127.0.0.1:3306/test?useUnicode=true&characterEncoding=UTF-8&userSSL=false&serverTimezone=GMT%2B8
username=root
password=password
initialSize=5
maxActive=10
maxWait=3000
maxIdle=8
minIdle=3
```  