---
title: C++语言ctime库
date: 2021-04-19 17:44:04
tags: [c++]
categories: cpp
---

C++语言ctime库

<!--  more -->

### 类型

`clock_t`: 是个long型，用来记录一段时间内的时钟计时单元数，即CPU的运行单元时间。

`size_t`:标准C库中定义的，应为unsigned int, 在64位系统中为long unsigned int。

`time_t`:从1970年1月1日0时0分0秒到该时间点所经过的秒数。

```
struct tm {
	int tm_sec; /* 秒 - 取值区间为[0,59] */
	int tm_min; /* 分 - 取值区间为[0,59] */
	int tm_hour; /* 时 - 取值区间为[0,23] */
	int tm_mday; /* 一个月中的日期 - 取值区间为[1,31] */
	int tm_mon; /* 月份(从一月开始，0代表一月) - 取值区间为[0,11] */
	int tm_year; /* 年份，其值等于实际年份减去1900 */
	int tm_wday; /* 星期 - 取值区间为[0,6]，其中0代表星期日，1代表星期一，以此类推。 */
	int tm_yday; /* 从每年的1月1日开始的天数 - 取值区间为[0,365]，其中0代表1月1日，1代表1月2日，以此类推。 */
	int tm_isdst; /* 夏令时标识符，实行夏令时的时候，tm_isdst为正。不实行夏令时的时候，tm_isdst为0；不了解情况时，tm_isdst()为负。 */
};

```

### 时间的操作

clock: 返回时钟计时单元数，自从这个程序开始运行。

time: 返回当前的time_t。

difftime: 计算time_t两个之间的时间差。

### 转换
mktime: 转换tm structure成time_t

asctime: 转换tm structure成字符串

ctime: 转换time_t成字符串

gmtime: 转换time_t 成tm as UTC time

localtime: 转换time_t 成tm as local time

strftime: 格式时间成字符串

转换成字符串的几个函数：asctime, ctime, strftime

### 宏

`CLOCKS_PER_SEC`: 它用来表示一秒钟会有多少个时钟计时单元。

```
#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <ctime>

using namespace std;

// 测量事件的持续时间
void test_clock_t()
{
	long i = 100000000L;
	clock_t start, finish;
	double duration;
	start = clock();

	while(i--){}

	finish = clock();
	duration = (double)(finish - start)/CLOCKS_PER_SEC;

	printf("Time to do 100000000 empty loops is %f seconds\n", duration);

}

void test_time_t() {
	time_t t = time(NULL);

	printf("The Calendar time now is %ld \n", t);
}

void test_difftime()
{
	time_t start, end;
	start = time(NULL);
	
	long i = 100000000L;
	while(i--){}


	end = time(NULL);

	printf("Time pause used %5.4f seconds.\n", difftime(end, start));
}

// 下面都是一些转换函数的应用
// mktime: tm --> time_t
void test_mktime()
{
	struct tm t;
	time_t t_of_day;
	t.tm_year = 1997 - 1990;
	t.tm_mon = 6;
	t.tm_mday = 1;
	t.tm_hour = 0;
	t.tm_sec = 1;
	t.tm_wday = 4; /* Day of the week */
	t.tm_yday = 0; /* Does not show in asctime  */
	t.tm_isdst = 0;

	t_of_day = mktime(&t);
	printf("%s",ctime(&t_of_day));

}

// localtime: time_t --> tm
void test_localtime()
{
	time_t rawtime;
	struct tm* timeinfo;

	time(&rawtime);

	timeinfo = localtime(&rawtime);

	printf("Current local time and date: %s", asctime(timeinfo));

}

// gmtime: time_t --> tm
void test_gmtime()
{
	time_t rawtime;
	struct tm* timeinfo;

	time(&rawtime);
	timeinfo = gmtime(&rawtime);

	printf("UTC time and date: %s", asctime(timeinfo));
}

// ctime: time_t --> string
void test_ctime()
{
	time_t t = time(NULL);
	std::string str = ctime(&t);
	std::cout << str << std::endl;

}

int main()
{

test_clock_t();
cout << "==========================" << endl;
test_time_t();
cout << "==========================" << endl;
test_difftime();
cout << "==========================" << endl;
test_mktime();
cout << "==========================" << endl;
test_localtime();
cout << "==========================" << endl;
test_gmtime();
cout << "==========================" << endl;
test_ctime();
	return 0;
}
```
我们可以使用strftime()函数将时间格式化为我们想要的格式。它的原型如下：

```
size_t strftime(
	char *strDest,
	size_t maxsize,
	const char *format,
	const struct tm *timeptr
);
```
我们可以根据format指向字符串中格式命令把timeptr中保存的时间信息放在strDest指向的字符串中，最多向strDest中存放maxsize个字符。该函数返回向strDest指向的字符串中放置的字符数。

函数strftime()的操作有些类似于sprintf():识别以百分号(%)开始的格式命令集合，格式化输出结果放在一个字符串中。格式化命令说明串strDest中各种日期和时间信息的确切表示方式。格式串中的其他字符原样放进串中。格式命令列在下面，它们是区分大小写的。

```
%a	星期几的简写
%A	星期几的全称
%b	月份的简写
%B	月份的全称
%c	标准的日期的时间串
%C	年份的后两位数字
%d	十进制表示的每月的第几天
%D	月/天/年
%e	在两字符域中，十进制表示的每月的第几天
%F	年-月-日
%g	年份的后两位数字，使用基于周的年
%G	年份，使用基于周的年
%h	简写的月份名
%H	24小时制的小时
%I	12小时制的小时
%j	十进制表示的每年的第几天
%m	十进制表示的月份
%M	十进制表示的分钟数
%n	新行符
%p	本地的AM或PM的等价显示
%r	12小时的时间
%R	显示小时和分钟：hh:mm
%S	十进制的秒数
%t	水平制表符
%T	显示时分秒：hh:mm:ss
%u	每周的第几天，星期一为第一天（值从0到6，星期一为0）
%U	每年的第几周，把星期日作为第一天（值从0到53）
%V	每年的第几周，使用基于周的年
%w	十进制表示的星期几（值从0到6，星期天为0）
%W	每年的第几周，把星期一作为第一天（值从0到53）
%x	标准的日期串
%X	标准的时间串
%y	不带世纪的十进制年份（值从0到99）
%Y	带世纪部分的十进制年份
%z	%Z时区名称，如果不能得到时区则返回空字符。
%%	百分号
```


#### strftime()函数的声明

```
size_t strftime(char *str, size_t maxsize, const char *format, const struct tm *timeptr)
```

- str -- 这是指向目标数组的指针，用来复制产生的 C 字符串。
- maxsize -- 这是被复制到 str 的最大字符数。
- format -- 这是 C 字符串，包含了普通字符和特殊格式说明符的任何组合。这些格式说明符由函数替换为表示 tm 中所指定时间的相对应值。格式说明符是：

```
#include <stdio.h>
#include <time.h>
 
int main ()
{
   time_t rawtime;
   struct tm *info;
   char buffer[80];
 
   time( &rawtime );
 
   info = localtime( &rawtime );
 
   strftime(buffer, 80, "%Y-%m-%d %H:%M:%S", info);
   printf("格式化的日期 & 时间 : |%s|\n", buffer );
  
   return(0);
}

```
