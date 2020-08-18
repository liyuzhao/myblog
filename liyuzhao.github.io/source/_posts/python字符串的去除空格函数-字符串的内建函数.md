---
title: python字符串的去除空格函数 字符串的内建函数
date: 2016-08-24 23:11:52
tags: python
categories: python
---

## python字符串的去除空格函数 字符串的内建函数
-----

* strip 同时去掉左右两边的空格
* lstrip 去掉左边的空格
* rstrip 去掉右边的空格

<!-- more -->
| 方法 | 描述 |
| :-----| :----|                                 
|string.capitalize()     |         把字符串的第一个字符大写|
|string.center(width)      |    返回一个原字符串居中,并使用空格填充至长度width的新字串|
|string.count(str, beg=0, end=len(string))|返回str在string里面出现的次数，如果beg或者end指定则返回指定范围内 str 出现的次数 |
|string.decode(encoding='UTF-8', errors='strict')  | 以 encoding 指定的编码格式解码string，如果出错默认报一个 ValueError的异常 ，除非errors 指定的是 'ignore''replace' string.encode(encoding='UTF-8', errors='strict')  以 encoding 指定的编码格式编码string，如果出错默认报一个ValueError 的异常，除非 errors指定的是'ignore'或者'replace'|           
|string.endswith(obj, beg=0, end=len(string))|检查字符串是否以 obj 结束，如果beg 或者 end 指定则检查指定的范围内是否以 obj 结束，如果是，返回True,否则返回 False.|
|string.expandtabs(tabsize=8) |把字符串string中的tab符号转为空格,默认的空格数tabsize是 8.|
|string.find(str, beg=0, end=len(string))|检测str是否包含在 string 中,如果 beg 和 end 指定范围，则检查是否包含在指定范围内，如果是返回开始的索引值，否则返回-1|
|string.index(str, beg=0, end=len(string))|跟find()方法一样，只不过如果str不在 string中会报一个异常.|
|string.isalnum() | R如果 string 至少有一个字符并且所有字符都是字母或数字则返回True,否则返回False|
|string.isalpha() | 如果 string 至少有一个字符并且所有字符都是字母则返回 True,否则返回 False|
|string.isdecimal() | 如果 string 只包含十进制数字则返回True 否则返回 False.|
|string.isdigit() |如果 string 只包含数字则返回 True 否则返回 False.|
|string.islower() |如果 string 中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是小写，则返回True，否则返回 False|
|string.isnumeric()|如果 string 中只包含数字字符，则返回True，否则返回 False|
|string.isspace()|如果 string 中只包含空格，则返回True，否则返回 False.|
|string.istitle()| 如果 string 是标题化的(见 title())则返回 True，否则返回 False|
|string.isupper() | 如果 string 中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是大写，则返回True，否则返回 False|
|string.join(seq)| Merges (concatenates)以 string 作为分隔符，将 seq 中所有的元素(的字符串表示)合并为一个新的字符串|
|string.ljust(width)|返回一个原字符串左对齐,并使用空格填充至长度width 的新字符串|
|string.lower() | 转换 string 中所有大写字符为小写. |
|<font color="#DC143C">string.lstrip() </font>|<font color="#DC143C">截掉 string 左边的空格.</font>|
|string.partition(str)|有点像 find()和 split()的结合体,从 str 出现的第一个位置起,把字符 串string分成一个3元素的元组(string_pre_str,str,strig_post_str),如果 string中不包含str则string_pre_str == string.|
|string.replace(str1, str2,num=string.count(str1))|把 string中的str1替换成str2,如果num指定，则替换不超过 num 次.|
|string.rfind(str, beg=0,end=len(string) )|类似于 find()函数，不过是从右边开始查找.|
|string.rindex( str, beg=0,end=len(string)) | 类似于 index()，不过是从右边开始.|
|string.rjust(width)      |   返回一个原字符串右对齐,并使用空格填充至长度width 的新字符串|
|string.rpartition(str)   |   类似于 partition()函数,不过是从右边开始查找.|
|<font color="#DC143C">string.rstrip() </font>  |  <font color="#DC143C">          删除 string 字符串末尾的空格.</font>|
|string.split(str="", num=string.count(str))|以 str 为分隔符切片string，如果num有指定值，则仅分隔num个子字符串|
|string.splitlines(num=string.count('\n'))|按照行分隔，返回一个包含各行作为元素的列表，如果num指定则仅切片 num 个行.|
|string.startswith(obj, beg=0,end=len(string))|检查字符串是否是以 obj 开头，是则返回True，否则返回 False。如果beg 和 end 指定值，则在指定范围内检查.|
|<font color="#DC143C">string.strip([obj]) </font>|  <font color="#DC143C">    在string 上执行 lstrip()和 rstrip().</font>|
|string.swapcase() |      翻转 string 中的大小写|
|string.title() |              返回"标题化"的 string,就是说所有单词都是以大写开始，其余字母均为小写(见 istitle())|
|string.translate(str, del="")|根据 str 给出的表(包含 256 个字符)转换 string 的字符,要过滤掉的字符放到 del 参数中|
|string.upper() |         转换 string 中的小写字母为大写|
|string.zfill(width) |    返回长度为width的字符串,原字符串 string 右对齐,前面填充0|
