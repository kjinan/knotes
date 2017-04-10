### 内置函数（[整理自这里](http://zhangyulong.iteye.com/blog/1162387)）
#### Sequence的内置函数（注意：Sequence不能为null）
* sequence?first	返回sequence的第一个值。<br>
* sequence?last	返回sequence的最后一个值。<br>
* sequence?reverse	将sequence的现有顺序反转，即倒序排序<br>
* sequence?size	返回sequence的大小<br>
* sequence?sort	将sequence中的对象转化为字符串后顺序排序<br>
* sequence?sort_by(value)	按sequence中对象的属性value进行排序<br>

#### Hash的内置函数
* hash?keys 返回hash里的所有key,返回结果为sequence
* hash?values 返回hash里的所有value,返回结果为sequence

#### 操作字符串函数
* substring（start,end）从一个字符串中截取子串
  <br/>start:截取子串开始的索引，start必须大于等于0，小于等于end
  <br/>end: 截取子串的长度，end必须大于等于0，小于等于字符串长度，如果省略该参数，默认为字符串长度。
* cap_first 将字符串中的第一个单词的首字母变为大写。
* uncap_first将字符串中的第一个单词的首字母变为小写。
* capitalize将字符串中的所有单词的首字母变为大写
* date,time，datetime将字符串转换为日期
  <br/>注意：如果指定的字符串格式不正确将引发错误
* ends_with 判断某个字符串是否由某个子串结尾，返回布尔值
  <br/>注意：布尔值必须转换为字符串才能输出
* html 用于将字符串中的<、>、&和"替换为对应得<>&quot:&amp
* index_of（substring,start）在字符串中查找某个子串，返回找到子串的第一个字符的索引，如果没有找到子串，则返回-1。
  <br/>Start参数用于指定从字符串的那个索引处开始搜索，start为数字值。
  <br/>如果start大于字符串长度，则start取值等于字符串长度，如果start小于0，则start取值为0。
* length返回字符串的长度
* lower_case将字符串转为小写
* upper_case将字符串转为大写
* contains 判断字符中是否包含某个子串。返回布尔值
  <br/>注意：布尔值必须转换为字符串才能输出
* number将字符串转换为数字
* replace用于将字符串中的一部分从左到右替换为另外的字符串。
* split使用指定的分隔符将一个字符串拆分为一组字符串
* trim 删除字符串首尾空格

#### 操作数字
* c 用于将数字转换为字符串
* string用于将数字转换为字符串
  <br/>Freemarker中预订义了三种数字格式：number,currency（货币）和percent(百分比)其中number为默认的数字格式转换

#### 操作布尔值
* string 用于将布尔值转换为字符串输出
  <br/>true转为"true"，false转换为"false"

### 常用处理
#### 条件判断
```
<#assign x = "aa"/>  
<#if x=="aa">
	x is aa  
<#else>
	x not aa
</#if>
```
#### 遍历 listmap
```
<#list somelist as item>
	${item}
	//如果是bean
	${item.title}
</#list>
```
#### 判断list的长度
```
<#if somlist?size>0>
</#if>
```
