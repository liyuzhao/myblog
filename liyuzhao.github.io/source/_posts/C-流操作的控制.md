---
title: C++流操作的控制
date: 2019-04-16 12:14:44
tags: [c++]
categories: cpp
---

在头文件 `iomainip.h` 中定义了一些控制流输出格式的函数，默认情况下整型数按十进制形式输出，也可以通过 hex 将其设置为十六进制输出。

流操作的控制具体函数如下。
<!-- more -->

##### (1) long setf(long f);

根据参数 f 设置相应的格式标志，返回此前的设置。该参数 f 所对应的实参为无名枚举类型中的枚举常量(又称格式化常量)，可以同时使用一个或多个常量，每两个常量之间要用按位或操作符连接。
如需要左对齐输出，并使数值中的字母大写时，则调用该函数的实参为 `ios::left|ios::uppercase`。

##### (2) long unsetf(long f);

根据参数 f 清除相应的格式化标志，返回此前的设置。 如果要清除此前左对齐输出设置，恢复默认的右对齐输出设置，则调用该函数的实参为 `ios::left`。

##### (3) int width();

返回当前的输出域宽。若返回数值为0，则表明没为刚才输出的数值设置输出域宽。输出域宽是指输出的值在流中所占有的字节数。

##### (4) int width(int w);

设置下一个数据值的输出域宽为w, 返回为输出上一个数据值所规定的域宽，若无规定则返回0.注意，此设置不是一直有效，而只是对下一个输出数据有效。

##### (5) setiosflags(long f);

设置f所对应的格式标志，功能与setf(long f)成员函数相同，当然，在输出该操作符后返回的是一个输出流。如果采用标准输出流 cout 输出它时，则返回 cout。输出每个操作符后都是如此，即返回输出它的流，以便向流中继续插入下一个数据。

##### (6) resetiosflags(long f);

清除 f 所对应的格式化标志，功能与 unsetf(long f)成员函数相同。输出后返回一个流。

##### (7) setfill(int c);

设置填充字符的 ASCⅡ 码为 c 的字符。

##### (8) setprecision(int n);

设置浮点数的输出精度为 n 。

##### (9) setw(int w);

设置下一个数据的输出域宽为 w 。

数据输入/输出的格式控制还有更简便的形式，就是使用头文件 iomainip.h 中提供的操作符。使用这些操作符不需要调用成员函数，只要把它们作为插入操作符 "" 的输出对象即可。

☑ dec: 转换为按十进制输出整数，是默认的输出格式。

☑ oct: 转换为按八进制输出整数。

☑ hex: 转换为按十六进制输出整数。

☑ ws: 从输出流中读取空白字符。

☑ endl: 输出换行符\n 并刷新流。刷新流是指把流缓冲区的内容立即写入到对应的物理设备上。

☑ ends: 输出一个空字符\0。

☑ flush: 只刷新一个输出流。

例如 (控制打印程序)：

```c++

#include <iostream>
#include <iomanip>
using namespace std;

void main()
{
	double a = 123.456789012345;
	cout << a << endl;
	cout << setprecision(9) << a << endl;
	cout << setprecision(6); // 恢复默认格式（精度为6）
	cout << setiosflags(ios::fixed);
	cout << setiosflags(ios::fixed) << setprecision(8) << a << endl;
	cout << setiosflags(ios::scientific) << a << endl;
	cout << setiosflags(ios::scientific) << setprecision(4) << a << endl;

}
``` 


例如 (整数输出的例子)：

```c++

#include <iostream>
#include <iomainip>
using namespace std;
void main()
{
	int b = 123456;	// 对b赋初值
	cout << b << endl;	// 输出: 123456
	cout << hex << b << endl; // 输出：1e240
	cout << setiosflags(ios::uppercase) << b << endl;  // 输出：1E240
	cout << setw(10) << b << ',' << b << endl; // 输出：    123456， 123456
	cout << setfill('*') << setw(10) << b << endl; // 输出： **** 123456
	cout << setiosflags(ios::showpos) << b << endl; // 输出：+123456

}
```

例如(输出大写的十六进制)：

```c++

#include <iostream>
#include <iomanip>
using namespace std;
void main()
{
	int i=0x2F,j=255;
	cout << i << endl;
	cout << hex << i << endl;
	cout << hex << j << endl;
	cout << hex << setiosflags(ios::uppercase)<<j<<endl;
}
```

例如(控制输出精度)：

```c++

#include <iostream>
#include <iomanip>
using namespace std;
void main()
{
	int x = 123;
	double y = 3.1415;
	cout << "x=";
	cout.width(10);
	cout << y << endl;
	cout.setf(ios::left);
	cout << "x=";
	cout.width(10);
	cout << x;
	cout << "y=";
	cout << y << endl;
	cout.fill('*');
	cout.precision(4);
	cout.setf(ios:showpos);
	cout << "x=";
	cout.width(10);
	cout << x;
	cout << "y=";
	cout.width(10);
	cout << y << endl;
}
```

例如（流输出小数点控制）：

```c++

#include <iostream>
using namespace std;
void main()
{
	float x = 20, y = -400;
	cout << x << '' << y << endl;
	cout.setf(ios::showpoint); // 强制显示小数点和无效0
	cout << x << '' << y << endl;
	cout.unsetf(ios::showpoint); 
	cout.setf(ios::scientific); // 设置按科学表示法输出
	cout << x << '' << y << endl;
	cout.setf(ios::fixed); // 设置按定点表示法输出
	cout << x << '' << y << endl;
}
```