

# Erlang database driver #

Copyright (c) 2013-2014 Beijing RYTong Information Technologies, Ltd.

__Version:__ 2.1


__Authors:__ cao.xu ([`cao.xu@rytong.com`](mailto:cao.xu@rytong.com)), deng.lifen ([`deng.lifen@rytong.com`](mailto:deng.lifen@rytong.com)), wang.meigong ([`wang.meigong@rytong.com`](mailto:wang.meigong@rytong.com)).



### <a name="Contents">Contents</a> ###


[Introduction](http://github.com/esl/edown/blob/master/doc/README.md#Introduction)
<br></br>

[Design Purpose](http://github.com/esl/edown/blob/master/doc/README.md#Design_Purpose)
<br></br>

[Download](http://github.com/esl/edown/blob/master/doc/README.md#Download)
<br></br>

[Installation](http://github.com/esl/edown/blob/master/doc/README.md#Installation)
<br></br>

[Documentation](http://github.com/esl/edown/blob/master/doc/README.md#Documentation)
<br></br>

[Configuration](http://github.com/esl/edown/blob/master/doc/README.md#Configuration)
<br></br>

[Getting Started](http://github.com/esl/edown/blob/master/doc/README.md#Getting_Started)
<br></br>

[Tests](http://github.com/esl/edown/blob/master/doc/README.md#Tests)
<br></br>

[Data Type](http://github.com/esl/edown/blob/master/doc/README.md#Data_Type)
<br></br>

[Notices](http://github.com/esl/edown/blob/master/doc/README.md#Notices)
<br></br>

[Having Problems?](http://github.com/esl/edown/blob/master/doc/README.md#Having_Problems?)
<br></br>



### <a name="Introduction">Introduction</a> ###

The db_driver is a high performance database driver based on the Erlang linked-in driver. It uses asynchronous threads to avoid IO block during the database access, the same way in which Erlang asynchronous drivers were implemented. We followed the syntax of Erlydb (erlang_mysql_driver) to design the APIs. For one database access request, the parameters will be passed to driver and processed in asynchronous threads. Then the request will be translated to respective SQL statements for different database types. After that, the work threads will call vendors' C/C++ APIs to execute these SQL statements and return the responses with ei.


Now the driver supports MySQL, Oracle, Sybase, DB2 and Informix.





### <a name="Design_Purpose">Design Purpose</a> ###

We want to support most typical database systems, such as Mysql, Oracle, Sybase, DB2 and Informix, but we don't choose ODBC because of its poor performance.


### <a name="Download">Download</a> ###

This is Download.


### <a name="Installation">Installation</a> ###
In the db_driver directory, execute

```
./configure [--with-mysql, --with-oracle, --with-sybase, --with-db2 or --with-informix]
make
sudo make install
```
If you installed rebar, you can execute

```
rebar compile
```

### <a name="Documentation">Documentation</a> ###
In the db_driver directory, execute

```
make doc
```
If you installed rebar, you can execute

```
rebar doc
```

to generate Erlang API.
The Database-Driven Documentation generated by doxygen. If you installed
doxygen, you can execute

```
make libdoc
```


to generated C API.

See [Database-Driven Documentation](http://github.com/esl/edown/blob/master/lib.md/index.html)


### <a name="Connection Parameters">Connection Parameters</a> ###
To use db_driver, you need to configure db_driver to make default database
connection for database-driven start-up. The connection parametes likes:

```
PoolId = test,
ConnArgs =  [{driver, mysql},
             {database, "test"},
             {host, "localhost"},
             {user, "root"},
             {password, ""},
             {poolsize, 8}
             ].
```


where PoolId is the name of your connection instance, the type is atom.

The following is required parameters.

```
driver::atom()          Database type. Supported mysql, oracle and sybase.
database::string()      Database name.
host::string()          Database host name or IP address.
user:string()           Database user.
password:string()       Database password.
poolsize::integer()     Connection pool size.
```


The following is optional parameters.

```
port::integer()             Database port. Default is 3306.
default_pool::boolean()     Default Connection pool.
error_handler::{Mod, Fun}   Callback Function of error handler.
```

### <a name="Getting_Started">Getting Started</a> ###

1.Starts db driver
```
%% Start db driver.
db_api:start().

%% Connection instance Id.
PoolId = 'test'.

%% Connection args.
ConnArg = [{driver, mysql},
           {database, "test"},
           {host, "localhost"},
           {user, "root"},
           {password, ""},
           {poolsize, 8},
           {default_pool, true}].

%% Add a connection pool.
db_api:add_pool(PoolId, ConnArg).

%% Execute sql string.
db_api:execute_sql("select version()").

%% If you didn't set the default pool flag, you can execute sql like this.
db_api:execute_sql("select version()", [{pool, PoolId}]).

%% Remove a connecttion pool.
db_api:remove_pool(PoolId),

%% Stop db driver.
db_api:stop().
```

If you set the dafault pool flag in several connection pools, 
the default pool is the last added pool. 


### <a name="Tests">Tests</a> ###


See test cases in module basic_SUITE, module informix_SUITE and module oracle_SUITE.


### <a name="Data_Type">Data Type</a> ###

The following is the mapping of database data type to Erlang data type.
1. MySQL data type.

```
BIT             integer()
TINYINT         integer()
BOOL, BOOLEAN   integer()
SMALLINT        integer()
MEDIUMINT       integer()
INT             integer()
INTEGER         integer()
BIGINT          integer()
FLOAT           float()
DOUBLE          float()
FLOAT           float()
DECIMAL         float()
DATE            {date, {Year::integer(), Month::integer(), Day::integer()}}
DATETIME        {datetime,
                    {{Year::integer(), Month::integer(), Day::integer()},
                     {Hour::integer(), Minute::integer(), Second::integer()}}}
TIMESTAMP       {datetime,
                    {{Year::integer(), Month::integer(), Day::integer()},
                     {Hour::integer(), Minute::integer(), Second::integer()}}}
TIME            {time, {Hour::integer(), Minute::integer(), Second::integer()}}
YEAR            integer()
CHAR            integer()
VARCHAR         string()
BINARY          string()
VARBINARY       string()
TINYBLOB        binary()
TINYTEXT        string()
BLOB            binary()
TEXT            string()
MEDIUMBLOB      binary()
MEDIUMTEXT      string()
LONGBLOB        binary()
LONGTEXT        string()
```
2. Oracle data type.

```
STRING          string()
NUMBER          number()
DATE            {datetime,
                    {{Year::integer(), Month::integer(), Day::integer()},
                     {Hour::integer(), Minute::integer(), Second::integer()}}}
TIMESTAMP       {timestamp,
                    {{Year::integer(), Month::integer(), Day::integer()},
                     {Hour::integer(),
                      Minute::integer(),
                      Second::integer(),
                      Microseconds::integer()},
                     {TimeZoneOffsetInHours::integer(),
                      TimeZoneOffsetInMinutes::integer()}}}
TIMESTAMP_Z     {timestamp,
                    {{Year::integer(), Month::integer(), Day::integer()},
                     {Hour::integer(),
                      Minute::integer(),
                      Second::integer(),
                      Microseconds::integer()},
                     {TimeZoneOffsetInHours::integer(),
                      TimeZoneOffsetInMinutes::integer()}}}
TIMESTAMP_LZ    {timestamp,
                    {{Year::integer(), Month::integer(), Day::integer()},
                     {Hour::integer(),
                      Minute::integer(),
                      Second::integer(),
                      Microseconds::integer()},
                     {TimeZoneOffsetInHours::integer(),
                      TimeZoneOffsetInMinutes::integer()}}}
BINARY          binary()
CLOB            string()
NCLOB           string()
BLOB            binary()
INTERVAL_YM     {interval_ym, {Year::integer(), Month::integer()}}}
INTERVAL_DS     {interval_ds,
                    {Day::integer(),
                     Hour::integer(),
                     Minute::integer(),
                     Second::integer(),
                     FractionalSecondComponent::integer()}}
```
3. Sybase data type.

```
VARBINARY       binary()
BIT             integer()
CHAR            string()
VARCHAR         string()
UNICHAR         list()
UNIVARCHAR      list()
DATE            {date, {Year::integer(), Month::integer(), Day::integer()}}
TIME            {time,
                    {Hour::integer(),
                     Minute::integer(),
                     Second::integer(),
                     Millisecond::integer()}}
DATETIME        {datetime,
                    {{Year::integer(), Month::integer(), Day::integer()},
                     {Hour::integer(),
                      Minute::integer(),
                      Second::integer(),
                      Millisecond::integer()}}}
SMALLDATETIME   {smalldatetime,
                    {{Year::integer(), Month::integer(), Day::integer()},
                     {Hour::integer(), Minute::integer()}}}
TINYINT         integer()
SMALLINT        integer()
INT             integer()
BIGINT          integer()
DECIMAL         {number, string()}
NUMERIC         {number, string()}
FLOAT           float()
REAL            float()
MONEY           {number, string()}
SMALLMONEY      float()
```


### <a name="Notices">Notices</a> ###


1. Oracle prepare statement.
Prepare sql is like "select ?,? from user", but prepare sql in Oracle is like "select :1,:2 from user".
You can consistently use two methods in module db_api, but you can only use the second method in module db_driver.


### <a name="Having_Problems?">Having Problems?</a> ###
This is problems.


## Modules ##


<table width="100%" border="0" summary="list of modules">
<tr><td><a href="http://github.com/esl/edown/blob/master/doc/db_api.md" class="module">db_api</a></td></tr>
<tr><td><a href="http://github.com/esl/edown/blob/master/doc/db_app.md" class="module">db_app</a></td></tr>
<tr><td><a href="http://github.com/esl/edown/blob/master/doc/db_conn.md" class="module">db_conn</a></td></tr>
<tr><td><a href="http://github.com/esl/edown/blob/master/doc/db_conn_server.md" class="module">db_conn_server</a></td></tr>
<tr><td><a href="http://github.com/esl/edown/blob/master/doc/db_stmt.md" class="module">db_stmt</a></td></tr>
<tr><td><a href="http://github.com/esl/edown/blob/master/doc/db_sup.md" class="module">db_sup</a></td></tr>
<tr><td><a href="http://github.com/esl/edown/blob/master/doc/db_util.md" class="module">db_util</a></td></tr></table>

