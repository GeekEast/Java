## Set up Derby Database in Netbeans
### 1. Create a new Database

<div align=center>
 <img src="https://img-blog.csdnimg.cn/20181102075242296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1plbkdfeGlhbmd0,size_16,color_FFFFFF,t_70" width="50%" alt=""/>
 <img src=" https://img-blog.csdnimg.cn/20181102074816332.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1plbkdfeGlhbmd0,size_16,color_FFFFFF,t_70" width="50%" alt=""/>
 </div>
 

### 2. Get the Database URL
<div align=center>
 <img src=" https://img-blog.csdnimg.cn/20181102075032746.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1plbkdfeGlhbmd0,size_16,color_FFFFFF,t_70" width="50%" alt=""/>
 <img src="https://img-blog.csdnimg.cn/20181102075050148.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1plbkdfeGlhbmd0,size_16,color_FFFFFF,t_70" width="50%" alt=""/>
 <img src="https://img-blog.csdnimg.cn/201811020750590.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1plbkdfeGlhbmd0,size_16,color_FFFFFF,t_70" width="50%" alt=""/>
 </div>

### 3. Add library to Java
<div align=center>
 <img src=" https://img-blog.csdnimg.cn/20181102075436571.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1plbkdfeGlhbmd0,size_16,color_FFFFFF,t_70" width="50%" alt=""/>
 <img src="https://img-blog.csdnimg.cn/20181102075448761.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1plbkdfeGlhbmd0,size_16,color_FFFFFF,t_70" width="50%" alt=""/>
 </div>

### 4.Code Skeleton
#####  Load the Database Driver
```java
// Load the derby driver
Class.forName("org.apache.derby.jdbc.EmbeddedDriver");
System.out.println("derby driver was loaded successfully.");
```
##### Connect to Database
```java
// build the connection to derby
Connection conn = DriverManager.getConnection(
        "jdbc:derby://localhost:1527/DerbyDemoDb",
        "ta",
        "123");
System.out.println("derby connection was established successfully.");
```
##### Create Table
```java
// create a new Statement object
Statement stat = conn.createStatement();

// create table
try {
    stat.executeUpdate("CREATE TABLE Persons ("
            + "PersonID int, "
            + "LastName varchar(255),"
            + "FirstName varchar(255),"
            + "Address varchar(255),"
            + "City varchar(255),"
            + "PRIMARY KEY (PersonID)"
            + ")");
} catch (SQLTransactionRollbackException e) {
    System.out.println("Table already exists.");
}
```
##### Insert Records using Statement
```java
// insert data
try {
    stat.executeUpdate("INSERT INTO Persons ("
            + "PersonID, LastName, FirstName, Address, City) "
            + "VALUES ("
            + "1, 'Tan','Xiang','CMUA','Adelaide')");

} catch (SQLIntegrityConstraintViolationException e) {
    System.out.println("Record Already exists.");
}
```
##### Insert Records using PrepareStatement
```java
// insert data
try {
    String prepareSQL = "INSERT INTO Persons ("
            + "PersonID, LastName, FirstName, Address, City) "
            + "VALUES ("
            + "?,?,?,?,?"
            + ")";
    PreparedStatement pres = conn.prepareStatement(prepareSQL);
    pres.setInt(1, 2);
    pres.setString(2, "San");
    pres.setString(3, "Xiang");
    pres.setString(4, "CMU");
    pres.setString(5, "Pittsburgh");
    pres.executeUpdate();

} catch (SQLIntegrityConstraintViolationException e) {
    System.out.println("Record Already exists.");
}
```
##### Simple Query using Statement
```java
// print out the result 
ResultSet res = stat.executeQuery("SELECT * FROM PERSONS");
while (res.next()) {
    System.out.println(res.getString("PersonID"));
}
```
##### Simple Query using PrepareStatement
```java
 // print out the result
 String prepareSQL = "SELECT * FROM Persons WHERE Lastname = ?";
 PreparedStatement pres = conn.prepareCall(prepareSQL);
 pres.setString(1, "Tan");
 ResultSet rest = pres.executeQuery();
 while (rest.next()) {
     System.out.println(rest.getString("FirstName"));
 }
```
### 6. Code Demo
```java
/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
package derbydemo;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.SQLIntegrityConstraintViolationException;
import java.sql.SQLTransactionRollbackException;
import java.sql.Statement;

/**
 *
 * @author xiangtan
 */
public class DerbyDemo {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) throws SQLException, ClassNotFoundException {

        // load the derby driver
        Class.forName("org.apache.derby.jdbc.EmbeddedDriver");
        System.out.println("derby driver was loaded successfully.");

        // build the connection to derby
        Connection conn = DriverManager.getConnection(
                "jdbc:derby://localhost:1527/DerbyDemoDb",
                "ta",
                "123");
        System.out.println("derby connection was established successfully.");

        // create a new Statement object
        Statement stat = conn.createStatement();

        // create table
        try {
            stat.executeUpdate("CREATE TABLE Persons ("
                    + "PersonID int, "
                    + "LastName varchar(255),"
                    + "FirstName varchar(255),"
                    + "Address varchar(255),"
                    + "City varchar(255),"
                    + "PRIMARY KEY (PersonID)"
                    + ")");
        } catch (SQLTransactionRollbackException e) {
            System.out.println("Table already exists.");
        }

        // insert data
        try {
            stat.executeUpdate("INSERT INTO Persons ("
                    + "PersonID, LastName, FirstName, Address, City) "
                    + "VALUES ("
                    + "1, 'Tan','Xiang','CMUA','Adelaide')");

        } catch (SQLIntegrityConstraintViolationException e) {
            System.out.println("Record Already exists.");
        }

        // print out the result 
        ResultSet res = stat.executeQuery("SELECT * FROM PERSONS");
        while (res.next()) {
            System.out.println(res.getString("PersonID"));
        }
        
        
        // insert data
        try {
            String prepareSQL = "INSERT INTO Persons ("
                    + "PersonID, LastName, FirstName, Address, City) "
                    + "VALUES ("
                    + "?,?,?,?,?"
                    + ")";
            PreparedStatement pres = conn.prepareStatement(prepareSQL);
            pres.setInt(1, 2);
            pres.setString(2, "San");
            pres.setString(3, "Xiang");
            pres.setString(4, "CMU");
            pres.setString(5, "Pittsburgh");
            pres.executeUpdate();

        } catch (SQLIntegrityConstraintViolationException e) {
            System.out.println("Record Already exists.");
        }
        
        
        // print out the result
        String prepareSQL = "SELECT * FROM Persons WHERE Lastname = ?";
        PreparedStatement pres = conn.prepareCall(prepareSQL);
        pres.setString(1, "Tan");
        ResultSet rest = pres.executeQuery();
        while (rest.next()) {
            System.out.println(rest.getString("FirstName"));
        }
    }

}
```

### 7. Summary
- PreparedStatement is precompiled and DB-side cached. **It leads to faster execution**, especially when the statement is executed multiple times. 
- PreparedStatement can automatically **prevent SQL injection** with parameterization and built-in escaping of quotes and other special characters. 
