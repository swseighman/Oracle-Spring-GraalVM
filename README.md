# Simple Oracle Database / Spring Native (GraalVM) Example


### Starting the Oracle Database

For simplicity, we'll use a container to deploy the Oracle Database.  In this exaample, we'll use Oracle Database XE 21c (Express Edition).

```
$ docker run -d -p 1521:1521 -p 5500:5500 -e ORACLE_PASSWORD=password -v oracle-volume:/opt/oracle/oradata gvenzl/oracle-xe
```

For testing purposes, you can download the Oracle Instant Client [here](https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html).  You'll need both the **Basic** and **SQL*Plus** `rpm` files.

First, as a prerequisite, install the `libaio` package. 

This is called `libaio1` on some Linux distributions. On Oracle Linux 8 prior to Instant Client 21 you also need the `libnsl` package.

On (Oracle) Linux, run:

```
$ sudo dnf install libaio
```

Then install the downloaded packages:
```
$ sudo rpm -ihv oracle-instantclient-sqlplus-21.5.0.0.0-1.el8.x86_64.rpm
$ sudo rpm -ihv oracle-instantclient-basic-21.5.0.0.0-1.el8.x86_64.rpm
 ```
 
 ### Building the Sample Project
 
 Clone this repository:
 
 ```
 $ git clone https://github.com/swseighman/Oracle-Spring-GraalVM.git
 ```
 
 Build the project:
 ```
 $ cd Oracle-Spring-GraalVM
 $ mvn clean package
 ```
 
 Run the example:
 ```
$ time java -jar target/datasourcesample-0.0.1-SNAPSHOT.jar "SELECT TO_CHAR (SYSDATE, 'MM-DD-YYYY HH24:MI:SS') \"NOW\" FROM DUAL"

real    0m1.036s
user    0m2.017s
sys     0m0.261s
 ```
Notice the SELECT takes **1036ms** to return a value (your time may vary).

 Now build the native image executable:
 ```
 $ mvn package -Pnative
```

And run the native image executable using the same query:
```
$ time target/datasourcesample "SELECT TO_CHAR (SYSDATE, 'MM-DD-YYYY HH24:MI:SS') \"NOW\" FROM DUAL"
Driver Name: Oracle JDBC driver
Driver Version: 21.3.0.0.0
Default Row Prefetch Value is: 20
Database Username is: SYSTEM

'SELECT TO_CHAR (SYSDATE, 'MM-DD-YYYY HH24:MI:SS') "NOW" FROM DUAL' returned: 02-07-2022 17:43:22

real    0m0.039s
user    0m0.012s
sys     0m0.006s
 ```
With the native image executable, the same query returns a value in only **39ms** (your time may vary). 
 