# doris
## 新建odbc驱动
### 1. 在各个be节点安装各数据库的odbc驱动
### 2. 进入所有的be的* conf/odbcinst.ini *进行驱动配置
```linux
    [MySQL ODBC 8.0.33]
    Description     = ODBC for MySQL
    Driver          = /home/ODBCDriver/mysql/lib/libmyodbc8w.so
    FileUsage       = 1
```
### 3. 创建外部odbc连接
```linux
    CREATE EXTERNAL RESOURCE `mysql_odbc`
    PROPERTIES (
    "type" = "odbc_catalog",
    "host" = "10.1.3.132",
    "port" = "3306",
    "user" = "root",
    "password" = "Tssr@2021",
    "database" = "jcys",
    "odbc_type" = "mysql",
    "driver" = "MySQL Driver"
    );
         
    CREATE EXTERNAL TABLE `jcy_szjc_pdfocrjx` (
      id    int ,
        dwbm  varchar(100)  null ,
        bmsah varchar(100)  null ,
        wjlj  varchar(1000) null ,
        wjmc  varchar(1000) null ,
        wjnr  text       null ,
        by1   varchar(1000) null,
        by2   varchar(1000) null,
        by3   varchar(1000) null,
        by4   varchar(1000) null
    ) ENGINE=ODBC
    COMMENT "ODBC"
    PROPERTIES (
    "odbc_catalog_resource" = "mysql_odbc",
    "database" = "jcys",
    "table" = "jcy_szjc_pdfocrjx"
    );
```