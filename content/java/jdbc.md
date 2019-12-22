---
layout: default
title:  "JDBC"
---

# JDBC
{:.no_toc}

* TOC
{:toc}

## Overview
- Found in [java.sql](https://docs.oracle.com/javase/8/docs/api/java/sql/package-summary.html)
- JDBC 4.1 Specification accessible [here](https://download.oracle.com/otn-pub/jcp/jdbc-4_1-mrel-spec/jdbc4.1-fr-spec.pdf)

## JDBC 4 Compliance
- Must support auto loading of the driver
  - Done by including a `META-INF/services/java.sql.Driver` file
- Must implement the following Interfaces
  - Driver
  - Connection
  - ResultSet
  - Statement
- Must support transactions
- Must support Entry Level SQL92

## Hello World Example
```java
// You need to have h2.jar in the same folder
// Compile and run: javac App.java; java -cp ".:h2.jar" App
Connection conn = DriverManager.getConnection("jdbc:h2:mem:test");

Statement s = conn.createStatement();
s.execute("CREATE TABLE person(id int, name varchar, lastname varchar);");
s.execute("INSERT INTO person VALUES(1, 'Koray', 'Tugay');");
s.execute("INSERT INTO person VALUES(2, 'John', 'Doe');");
s.execute("INSERT INTO person VALUES(3, 'Jane', 'Doe');");
s.execute("INSERT INTO person VALUES(4, 'Pinar', 'Tugay');");

ResultSet rs = s.executeQuery("SELECT * FROM person");

ResultSetMetaData rsmd = rs.getMetaData();
int colCount = rsmd.getColumnCount();

String[] columnNames = new String[colCount];
for (int i = 1; i <= colCount; i++) 
    columnNames[i-1] = rsmd.getColumnName(i);

String header = Stream.of(columnNames).collect(Collectors.joining("\t"));
System.out.println(header);

while (rs.next()) {
    for (String columnName : columnNames)
        System.out.print(rs.getObject(columnName) + "\t");
    System.out.println("");            
}
```
### Output
```bash
ID	NAME	LASTNAME
1	Koray	Tugay	
2	John	Doe	
3	Jane	Doe	
4	Pinar	Tugay	
```

### Notes on Hello World
- To obtain a `Connection`, call `DriverManager.getConnection(String jdbcUrl)`
  - There are other versions with additional properties such as username and password
- To create a `Statement`, call `Connection.createStatement()`
- To execute a query that will return a `ResultSet`, call `Statement.executeQuery(String query)`
  - `executeQuery` returns a `ResultSet` object that you can extract further information from
- To execute a query that will not return a `ResultSet`, call `Statement.executeUpdate(String query)`
  - `executeUpdate` returns number of rows affected
- To execute a dynamic query (such as input from user) that will return either a `ResultSet` or number of affected rows, call `Statement.execute(String query)`
  - `executeQuery` returns a boolean value: `true` if a ResultSet is returned, `false` if number of affected rows is returned
  - `getResultSet` or `getUpdateCount` can then be called based on the the returning boolean value
  
## ResultSet
### ResultSetType 
- `TYPE_FORWARD_ONLY`
- `TYPE_SCROLL_INSENSITIVE`
- `TYPE_SCROLL_SENSITIVE`

### ResultSetConcurrency
- `CONCUR_READ_ONLY`
- `CONCUR_UPDATABLE`

#### Finding Number of Rows Using TYPE_SCROLL_INSENSITIVE
```java
Connection con = DriverManager.getConnection("jdbc:derby:mydb;create=true");

Statement st = con.createStatement();
try {
    st.execute("DROP TABLE person");
} catch (Exception ignored) {}
st.execute("CREATE TABLE person(Id INT, Name VARCHAR(255))");
st.execute("INSERT INTO person VALUES(1, 'Koray')");
st.execute("INSERT INTO person VALUES(2, 'Deniz')");
st.execute("INSERT INTO person VALUES(3, 'Toprak')");
st.execute("INSERT INTO person VALUES(4, 'Pinar')");
st.close();

// ResultSet.TYPE_SCROLL_INSENSITIVE lets you move cursor around contrary to FORWARD_ONLY
// ResultSet.CONCUR_READ_ONLY is the default where we can only read data and not update it
st = con.createStatement(ResultSet.TYPE_SCROLL_INSENSITIVE, ResultSet.CONCUR_READ_ONLY);
ResultSet rs = st.executeQuery("SELECT * FROM person");
rs.last();                  // Move to last row
int count = rs.getRow();    // Get row number, in this example 4
rs.beforeFirst();           // Position cursor back

st.close();
con.close();
```

## Transaction Isolation
- Constants found in [java.sql.Connection](https://docs.oracle.com/javase/8/docs/api/java/sql/Connection.html#field.summary)
### Transaction Isolation Levels Example with Derby
- Make sure you have `derby.jar` in the folder
- Run either by passing the argument true or not
  - `killall -9 java; clear; javac App.java; java -cp ".:derby.jar" App;`
  - `killall -9 java; clear; javac App.java; java -cp ".:derby.jar" App true;`
```java
import java.sql.*;
import java.util.*;

class App {
    static Map<Integer, String> isolationLevels = new HashMap<>();
    static {
        isolationLevels.put(Connection.TRANSACTION_READ_COMMITTED, "Connection.TRANSACTION_READ_COMMITTED");
        isolationLevels.put(Connection.TRANSACTION_READ_UNCOMMITTED, "Connection.TRANSACTION_READ_UNCOMMITTED");
    }

    public static void main(String[] args) throws SQLException {
        String url = "jdbc:derby:coffeshop;create=true";
        Connection con = DriverManager.getConnection(url);
        
        // Default for Derby is Connection.TRANSACTION_READ_COMMITTED
        System.out.println("Default: " + isolationLevels.get(con.getTransactionIsolation()));

        Statement st = con.createStatement();
        try {
            st.execute("DROP TABLE coffee");
        } catch (Exception ignored) {}

        st.execute("CREATE TABLE coffee(Type VARCHAR(255), Price INT)");
        st.executeUpdate("INSERT INTO coffee VALUES ('Blonde', 10)");

        st.close();
        con.close();

        // Example
        Connection supplier = DriverManager.getConnection(url);
        Connection consumer = DriverManager.getConnection(url);

        supplier.setAutoCommit(false);
        supplier.createStatement().executeUpdate("UPDATE coffee SET Price = 20");

        if (args.length > 0 && args[0].equals("true")) {
            // Allow consumer to read uncommitted data
            consumer.setTransactionIsolation(Connection.TRANSACTION_READ_UNCOMMITTED);
        }
        // Application blocks here if consumers transaction isolation is TRANSACTION_READ_COMMITTED
        ResultSet rs = consumer.createStatement().executeQuery("SELECT Price FROM coffee");
        rs.next();
        
        // Prints 10 if consumers transaction isolation is TRANSACTION_READ_UNCOMMITTED
        // This is potentially dirty data since supplier may choose to rollback
        System.out.println(rs.getInt("Price")); 

        supplier.rollback();
        supplier.close();
        consumer.close();
    }
}
```
#### Notes on the Example
- Default isolation level for Derby is `TRANSACTION_READ_COMMITTED`
- When you run the program above without true, this isolation level is kept intact
- `supplier` is __not__ in auto commit mode, and is not committing its changes
- This causes `consumer` (and the whole application) to block since it is only allowed to read committed data
- If you provide the `true` argument when calling the program, `consumer`s isolation level will be `TRANSACTION_READ_UNCOMMITTED`
- This will allow `consumer` to read uncommitted, potentially dirty data