## 作者：
* 孔祥生
* kongxiangsheng@163.com
* 1035349282

## 脚本说明：
* `db_export.sh` 作用是导出数据（整库导出、分表导出、按配置文件来导出指定的表）
* `db_import.sh` 作用是导入数据
* `db_import.sh` 脚本中的`IFS=$(echo -en "\n\b")`   可以避免文件名中含有空格时出现问题
* `tables_to_export.cfg` 文件支持#作为注释


db_export.sh 文件内容
```BASH
#!/bin/bash

##################   变量配置begin   ##################
# 数据库相关变量
readonly var_db_addr=10.0.0.1
readonly var_db_username=db_username
readonly var_db_password=db_passwd
readonly var_db_database=db_dbname
# 目录相关变量
# readonly var_tablelist_file=~/test/local/tables_to_export.cfg
readonly var_file_path=/server/datasql/`date "+%Y%m%d"`/
readonly var_des_name=data_`date "+%Y%m%d"`.sql
##################   变量配置end     ##################
# 创建目标文件目录
mkdir -p $var_file_path


# 读取配置文件，遍历文件中所有的表 -- begin
#
# 检查配置文件是否存在
# if [ ! -s $var_tablelist_file ]; then
# 	echo "读取配置文件失败，请检查$var_tablelist_file是否存在"
# 	exit
# fi
#
# cat $var_tablelist_file | while read line
# do
# 	if [[ $line =~ \#.* ]]; then
# 		continue
# 	else
# 		invar_table_name=$line
# 		echo "正在导出表$invar_table_name"
# 		mysqldump --extended-insert=false -h$var_db_addr -u$var_db_username -p$var_db_password $var_db_database $invar_table_name > $var_file_path/$var_des_name
# 	fi
# done
# 读取配置文件，遍历文件中所有的表 -- end

# 导出整库dump -- begin
# echo "导出crms的数据到$var_file_path/$var_des_name"
# mysqldump -h$var_db_addr -u$var_db_username -p$var_db_password $var_db_database> $var_file_path/$var_des_name
# echo "导出crms的数据完成"
# 导出整库dump -- end

# 每个表导出一个sql语句 -- begin
var_tablelist=`echo "show tables" |mysql -h$var_db_addr -u$var_db_username -p$var_db_password -D$var_db_database --default-character-set=utf8 | grep -iv Tables_in`
for invar_table_name in $var_tablelist
do
	if [[ $invar_table_name == "rbac_user" ]] || [[ $invar_table_name == "rbac_user_role" ]] || [[ $invar_table_name == "rbac_group" ]]; then
		echo "跳过表$invar_table_name"
		continue;
	else
		echo "正在导出表$invar_table_name"
		mysqldump --extended-insert=false -h$var_db_addr -u$var_db_username -p$var_db_password $var_db_database $invar_table_name > $var_file_path/$invar_table_name.sql |grep -v "Warning"
	fi
done

```

db_import.sh 文件内容
```BASH
#!/bin/bash

##################   变量配置begin   ##################
# 数据库相关变量
readonly var_db_addr=10.0.0.1
readonly var_db_username=db_username
readonly var_db_password=db_passwd
readonly var_db_database=db_dbname
# 目录相关变量
readonly var_file_path=/server/datasql/`date "+%Y%m%d"`/
readonly var_des_name=data_`date "+%Y%m%d"`.sql
##################   变量配置end     ##################

# 执行整库导出的脚本 -- begin
# mysql -h$var_db_addr -u$var_db_username -p$var_db_password -D$var_db_database --default-character-set=utf8 < $var_file_path/$var_des_name
# invar_result=$?
# if [ 0 -eq $invar_result ]; then
# 	echo "    成功执行:$var_file_path/$var_des_name"
# else
# 	echo "    !!!!执行:$var_file_path/$var_des_name遇到错误!!!!"
# fi
# 执行整库导出的脚本 -- end

# 遍历
IFS=$(echo -en "\n\b")
var_dataFiles=`ls $var_file_path`
for file in $var_dataFiles
do
	echo "        开始执行:$file"
	mysql -h$var_db_addr -u$var_db_username -p$var_db_password -D$var_db_database --default-character-set=utf8 < $var_file_path/$file
	invar_result=$?
	if [ 0 -eq $invar_result ]; then
		echo "        成功执行:$file"
	else
		echo "        !!!!执行:$file遇到错误!!!!"
	fi
done
```
tables_to_export.cfg 文件内容举例
```
# sys模块
sys_table_name1
sys_table_name1
sys_table_name1
sys_table_name1
sys_table_name1
sys_table_name1
# pm模块
pm_table_name1
pm_table_name1
pm_table_name1
pm_table_name1
pm_table_name1
pm_table_name1
pm_table_name1
```
