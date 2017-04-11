## 作者：
* 孔祥生
* kongxiangsheng@163.com
* 1035349282

## 脚本说明：
* 该脚本的作用是从远程ftp主机下载sql脚本，并且自动在指定的数据库中执行。
* 脚本运行过程中会打印日志，也会同步写入日志文件中

## 使用说明：
* sql.sh的作用是用于记录日志
* 为避免脚本被误删除或误修改，建议将两个脚本文件增加只读属性
```Bash
# 下面命令可以增加只读属性
chattr +i .exec_sql.sh
chattr +i sql.sh
# 下面命令用于取消只读属性
chattr -i .exec_sql.sh
chattr -i sql.sh
```
****
sql.sh 文件内容
```Bash
#!/bin/bash
bash .exec_sql.sh $1 |tee sql.log
```
.exec_sql.sh 文件内容
```Bash
#!/bin/bash

# 参数校验，必须有一个参数
if [ 1 -ne $#  ]; then
	echo "    !!!!参数有误，请输入版本号（例如20170406）!!!!"
	exit
fi

# 重置本地的sql文件目录
echo "    正在重置本地目录"
rm -rf /localPath/scripts*
mkdir -p /localPath/scripts_$1/tab
mkdir -p /localPath/scripts_$1/data

# 从ftp下载文件
echo "    正在从ftp服务器下载sql脚本"
ftp -n >null <<!
	open xxx.xxx.xxx.xxx
	user xxxxxx xxxxxx
	binary
	cd /db_$1/tab
	lcd /localPath/scripts_$1/tab
	prompt
	mget *
	cd /db_$1/data
	lcd /localPath/scripts_$1/data
	mget *
	close
	bye
!

# 检查脚本是否存在
echo "    正在检查下载结果"
## 检查总条数
var_totalCount=`ls -lR /localPath/scripts_$1 |grep ^- |grep -i sql$ |wc -l`
if [ 0 -eq $var_totalCount ]; then
	echo "    !!!!下载脚本失败，请确认脚本是否已经提交!!!!"
	exit 0 ;
fi
## 分别获取tab条数和data条数
var_tabCount=`ls -lR /localPath/scripts_$1/tab |grep ^- |grep -i sql$ |wc -l`
var_dataCount=`ls -lR /localPath/scripts_$1/data |grep ^- |grep -i sql$ |wc -l`
echo "    下载成功，tab[$var_tabCount]条，data[$var_dataCount]条"

# 执行tab下的脚本
echo "    开始执行tab脚本"
var_tabFiles=`ls /localPath/scripts_$1/tab/`
for file in $var_tabFiles
do
	mysql -hxxx.xxx.xxx.xxx -uxxxxxx -pxxxxxx -Dxxxxxx --default-character-set=utf8 < /localPath/scripts_$1/tab/$file
	invar_result=$?
	if [ 0 -eq $invar_result ]; then
		echo "        成功执行tab:$file"
	else
		echo "        !!!!执行tab:$file遇到错误!!!!"
	fi
done

# 执行data下的脚本
echo "    开始执行data脚本"
var_dataFiles=`ls /localPath/scripts_$1/data/`
for file in $var_dataFiles
do
	mysql -hxxx.xxx.xxx.xxx -uxxxxxx -pxxxxxx -Dxxxxxx --default-character-set=utf8 < /localPath/scripts_$1/data/$file
	invar_result=$?
	if [ 0 -eq $invar_result ]; then
		echo "        成功执行data:$file"
	else
		echo "        !!!!执行data:$file遇到错误!!!!"
	fi
done
```
