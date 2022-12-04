---
title: 'Python与SQL Server的交互：pyODBC, pymssql, SQLAlchemy'
layout: post
categories: [数据分析, Python]
abbrlink: 8f1c0ee0
date: 2017-03-16 22:57:37
excerpt: Python与SQL Server的交互，利用Python连接SQL数据库和执行SQL语句。
---


Windows平台下Python读取、写入SQL Server相关的函数库，文章结构如下：

![Python+SQLserver]({{ site.baseurl }}/user-imgs/Python+SQLserver/Python+SQL_Server.png)

### Python Drivers
#### PyODBC
Annaconda下可以用`pip install pyodbc`安装，也可以到[这里](https://www.microsoft.com/en-us/download/details.aspx?id=50420)下载。

首先建立`connection`对象：
```python
import pyodbc
conn = pyodbc.connect(
    r'DRIVER={ODBC Driver 11 for SQL Server};'  #or {ODBC Driver 13 for SQL Server}
    r'SERVER=ServerHostName;'
    r'DATABASE=DBName;'
    r'UID=user;'
    r'PWD=password'
    )
```

添加游标（Cursor）对象并执行SQL查询语句：
```python
cursor = conn.cursor()
cursor.execute('SQL Query Goes Here')
for row in cursor.fetchall():
  print(rows.[column name])
```

更多信息参见[MSDN DOCs](https://docs.microsoft.com/en-us/sql/connect/python/pyodbc/python-sql-driver-pyodbc)。

#### pymssql
同样可以用`pip install pymssql`安装，也可以到[这里](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql)，然后用`pip`安装`wheel`文件。

pymssql目前还不支持Python3.6，这点要注意下。

pymssql的用法跟pyODBC很像，下面是官网给出的例子：
```python
from os import getenv
import pymssql

server = getenv("PYMSSQL_TEST_SERVER")
user = getenv("PYMSSQL_TEST_USERNAME")
password = getenv("PYMSSQL_TEST_PASSWORD")

conn = pymssql.connect(server, user, password, "tempdb")
cursor = conn.cursor()
cursor.execute("""
IF OBJECT_ID('persons', 'U') IS NOT NULL
    DROP TABLE persons
CREATE TABLE persons (
    id INT NOT NULL,
    name VARCHAR(100),
    salesrep VARCHAR(100),
    PRIMARY KEY(id)
)
""")
cursor.executemany(
    "INSERT INTO persons VALUES (%d, %s, %s)",
    [(1, 'John Smith', 'John Doe'),
     (2, 'Jane Doe', 'Joe Dog'),
     (3, 'Mike T.', 'Sarah H.')])
# you must call commit() to persist your data if you don't set autocommit to True
conn.commit()

cursor.execute('SELECT * FROM persons WHERE salesrep=%s', 'John Doe')
row = cursor.fetchone()
while row:
    print("ID=%d, Name=%s" % (row[0], row[1]))
    row = cursor.fetchone()

conn.close()
```

详细用法参见[pymssql docs](http://www.pymssql.org/en/stable/index.html)和[MSDN DOCs](https://docs.microsoft.com/en-us/sql/connect/python/pymssql/python-sql-driver-pymssql)


#### [SQLAlchemy](https://www.sqlalchemy.org/)(Python SQL Toolkit)
SQLAlchemy提供了一系列丰富、完整、（我看不懂）的API用于数据库操作。这里只谈其`create_engine`方法。

```python
from sqlalchemy import create_engine
# pyodbc
engine = create_engine('mssql+pyodbc://user:password@DSNname') #需要配置DSN，参见最后一节

# pymssql
engine = create_engine('mssql+pymssql://user:password@Hostname:port/DBname')
```

利用创建好的`engine`，可以结合pandas库进行批量的读取、写入操作。

用SQLAlchemy与其他类型的数据库建立链接的方法参见[这里](http://docs.sqlalchemy.org/en/latest/core/engines.html)。

#### Pandas
利用pyODBC和pymssql拉取的对象需要进一步处理才能进行常见的数据清洗等工作，而Pandas也提供了SQL相关的方法，在SQLAlchemy的辅助下，可以将`DataFrame`对象直接写入table。

##### 读取：pd.read_sql()
[API](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_sql.html)：
```python
pandas.read_sql(sql, con, index_col=None, coerce_float=True, params=None, parse_dates=None, columns=None, chunksize=None)
```

其中的`con`参数，可以传入SQLAlchemy建立的`engine`对象，也可以是pyODBC或者pymssql建立的`DBAPI2 connection`对象。

##### 写入:pd.DataFrame.to_sql()
[API](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.to_sql.html):
```python
DataFrame.to_sql(name, con, flavor=None, schema=None, if_exists='fail', index=True, index_label=None, chunksize=None, dtype=None)
```

这里的`con`参数，只支持sqlite3的`DBAPI2 connection`对象，支持所有的`SQLAlchemy engine`对象。
`name`参数传入表名，用`if_exists`参数控制表存在时的动作：
- `‘fail’`: 啥也不干。
- `’replace‘`: 将原有表删除，新建表，插入数据。
- `’append'`: 在表中插入数据。表不存在时新建表。

### 命令行
利用`Sqlcmd`命令，也可以在命令行下执行SQL文件，用法如下：
```bash
sqlcmd -U user -P password -S server -d DBName -i /path/to/myScript.sql
```

这样可以有如下思路，将数据写入.SQL文件，再生成.bat文件（批量）写入上述命令，之后完成执行。

### DSN
Windows下可以配置DSN(Data Source Names)预先存储数据库连接的信息，在*Control Panel* -> *Administrative Tools* -> *ODBC Data Source* 下添加即可。

配置好DSN后，pyODBC的连接过程可以简化为：
```python
conn = pyodbc.connect(r'DSN=DSNname;UID=user;PWD=password') #UID和PWD也可以在DSN中配置
```

### 拾遗
Python与文件的IO、SQL数据库的读写时有中文字符可能会有编码问题。一种方案是在中文字符串前添加N，如`N'python大法好'`；另一种方案是传入`encoding`参数，常用的中文编码有`GB2123`，`GB18030`，推荐的还是统一用`UTF-8`编码、解码。

利用如下命令，可以在SQLAlchemy中指定编码：
```python
engine = create_engine('mssql+pymssql://user:password@HostName\DBname', connect_args = {'charset':'utf-8'})
```
其他自定义`DBAPI connect()`参数的方法参见[这里](http://docs.sqlalchemy.org/en/latest/core/engines.html#custom-dbapi-connect-arguments)。
