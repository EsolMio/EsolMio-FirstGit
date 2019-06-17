# MySql tips
- `show variables like 'char%'`，展示MySQL中配置的变量，`like`后接要查找的关键字；
- `set variables_name=...`，设置变量的值。
 ## 关于乱码问题
关于变量的具体含义，可查看[mysql set names 命令和 mysql 字符编码问题](https://www.cnblogs.com/digdeep/p/5228199.html)
- 需要将变量`character_set_client`, `character_set_connection` ,`character_set_results`设置为`utf8`或`gbk`。（可使用`set names =utf8`命令）（字符显示）。
- 需将变量`collation_connection`, `collation_database`, `collation_server`设置为`utf8_general_ci`（排序方式）

**修改后记得重启MySql**
**使用上述方法无法成功修改，重启后依旧原值，需修改文件`my.ini`**

可见[my.ini修改](https://www.cnblogs.com/houqi/p/5713176.html)
其中涉及到：
1. `show create table databasename.tablename`，展示对应表列的详细参数
2. `alter table databasename.tablename convert to character set utf8 collate utf8_general_ci`，将对应的表修改为utf8编码和utf8_general_ci排序。
3. 

## prepare 预处理
可详见 
