---
title: Gyp-学习记录
date: 2017-12-25 20:12:20
tags: [gyp]
categories: c++
---
Gyp 学习记录

GYP(Generate Your Projects)是由 Chromium 团队开发的跨平台自动化项目构建工具。

##### gyp简述
GYP的输入是.gyp和.gypi文件，.gypi文件是用于.gyp文件include使用的。.gyp文件就是符合特定格式的json文件。

gyp文件中包含target，Visual C++下，gyp生成sln，target生成vcproj或vxcproj。

<!-- more -->

##### 1.1 设计目标
gyp设计针对目标就是为了解决chromium浏览器构建问题，最重要的就是支持多平台的构建。因为Chromium内部都是c/c++文件，因此主要考虑方便c/c++程序的构建。设计时候还考虑到下面这些问题：

* debug vs release
* cross compile
* toolchain interface

##### 1.2 构建文件
构建文件名字不固定，但是后缀通常是.gyp和.gypi(gyp included).构建文件内容就是python的一个数据结构.
example:

```
	{ 
		'target_defaults':{
			'defines':['DEBUG'],
		},
		'targets':[
			{
				'target_name':'test', #生成的文件.
				'type':'executable', #可执行程序.
				'sources':['test.cc'],
				'defines':['FOO']
			}
		]
	}
```
##### 1.3 .gyp文件剖析
整个构建文件最顶层是一个字典，包含了下面这些key:

- conditions //条件判断
- includes // 包含的构建文件
- target <sub>defualt</sub> //构建目标默认属性
- targets // 构建目标列表
- variables // 构建文件使用的变量

###### 1.3.1 conditions
conditions分两种行为。 普通的conditions就在load构建文件之后立即计算，另外一种是target(conditions)是在计算完成依赖之后然后来进行计算的，两个过程分别就是early and late phases阶段。对于conditions写法非常简单

```
	'conditions':[
		['OS==Linux', {'sources':['linux_interface.cc']}]
	]
```
对于condition的判断，主要还是为了能够修改一些描述属性。从文档上来看，默认提供的条件就是OS判断，其他判断应该都是变量的判断。

###### 1.3.2 targets

target部分的话对target(default)里面设定的内容默认进行merge。比如上面例子的话，对于target/test来说，使用的define就会是-DDEBUG -DFOO.当然对于这种东西是可以进行其他策略选择的，比如如果修改成下面格式，那么就是直接替换：

```
'defines=':['FOO']
```
生成的defines就是-DFOO了。或者是可以剔除掉：

```
'defines!':['DEBUG']
```
生成的defines就没有任何内容了。通过在选项key后面添加操作符号来达到自定义目的(相对于全局环境).
对于一个target包括了下面这些重要属性：

- actions(list) //执行命令
- all<sub>dependentsettings</sub>(dict) //如果依赖这个target的话，需要使用的设置
- configurations(dict) 配置
- defines(list) //对于C/C++的defines
- dependencies(list) //依赖对象。如果是本文件的话那么直接引用，如果是其他文件的话，使用path/xxx.gyp:target.
- direct<sub>dependentsetting</sub>(dict) //直接依赖这个target的话，需要使用的设置
- include<sub>dirs</sub>(list) // 头文件目录
- libraries(list)//目标需要链接的库
- link<sub>settings</sub> //依赖这个target,需要使用的链接参数
- sources(list) //源文件
- target<sub>conditions</sub>(list) // 和conditions类似，但是是在完全计算之后然后来判断
- target<sub>name</sub>(string) //名字
- type //目标类型，现在只是支持static<sub>library</sub>,shared<sub>library</sub>,executable和none

###### 1.3.3 includes
gyp倾向的组织就是在toplevel上面存在一个gyp文件，可以存在子目录下面，但是子目录下面并不存放一个完整的构建文件， 通常只是存放构建文件的片段。为了区分，后缀为gypi。本身来说，这个gypi并不可以直接被gyp所接受生成native构建系统文件， 唯一的作用就是被toplevel的gyp文件进行include。如果对于Linux系统来说，最终生成的Makefile应该是一份大Makefile并且没有递归make的操作。 关于构造一个没有递归的Makefile是非常有价值的，不管是对于调试还是提升编译速度方面。可以参考文章Recusive Make Considered Harmful.
一旦我们允许include子目录的gypi文件进来，我们就必须规定哪些字段应该是文件。原因是假设存在src目录下面有src/BUILD.gypi这样一个文件，sources内容如下:

```
'sources':['src.cc']
```
而在上层BUILD.gyp文件里面，使用includes语法：

```
'includes':['./src/BUILD.gypi']
```
那么在生成大Makefile的时候，我们必须清楚'sources'字段里面内容都是文件，不应该直接使用src.cc， 相反应该加上目录前缀src，
最终应该使用src/src.cc这样一个文件。关于哪些字段里面内容是路径， 这个在gyp里面有详细规定，在后面小节里面我们也会提到。

###### 1.3.4 actions
actions是targets里面的一个特殊属性，主要是用来进行target的自定义操作的.
每个action是一个dict，主要包含4个属性：

- action<sub>name</sub>(string). //操作名称
- input(list) //输入文件
- outputs (list) //输出文件
- actions(list) //命令

有了这些属性就可以做一个完整的操作抽象。
###### 1.3.5 variable
variables这个小节里面是进行变量的定义，格式是dict。下面是一个例子：

```
'variables':{
    'common_files':['src/common.cc','src/interface.cc'],
}
```
为了引用变量，我们可以这样写：

```
<(common_files)
<@(common_files)
>(common_files)
>@(common_files)
```

总之引用变量必须加上(),同时在前面加上<,<@,>,>@的4种中一种前缀符号。关于前缀符号的含义， 会在后面的operator小节里面说明。
对于变量类型，一共分为3类：

- predefined variables //预定义变量
- user-defined variables //用户定义变量
- automatic variables //自动变量

预定义变量比如OS(系统环境),EXECUTABLE<sub>SUFFIX</sub>(可执行文件后缀).用户自定义变量就不再赘述。
自动变量类似于Makefile里面的$@,$这样的变量，好比反射。比如在target<sub>conditions</sub>部门的话，我们根据不同类型程序来做不同的condition:

```
'target_conditions':[
	['_type==static_library',{'source':['func.cc']}]
]
```
这样对于target为static<sub>library</sub>都会联编func.cc这个文件了，自动变量就是属性名称之前加上构成的。
存在自动变量非常必要。有时候我们在全局环境中，希望根据不同的条件来定义不同的行为，并且是在计算的同时在来做条件判断的。 这样就提出一个要求就是，条件判断部分必须有能力知道，当前到底在计算什么东西(反射)。
##### 1.4 early and late phases
对于变量展开和条件判断有两个不同的阶段：
载入文件之后进行，就是early phase。
计算完成之后进行，就是late phase。
对于两个阶段允许不同操作是非常必要的。对于early phase这个肯定需要，而对于late phase的话， 有时候我们是希望了解到gyp处理完成某个target之后所有信息，然后进行判断的。
ps:comake2在设计的时候，就没有考虑late phase这个功能。造成没有办法在应用层添加延迟计算这样一个特性。 最终只能够是修改comake2代码来完成需求。

##### 1.5 operator
关于每个操作符号的含义：

* x= //字段内容进行覆盖
* x? //如果字段没有定义，那么就进行覆盖
* x+ //字段内容进行merge
* < (x) //early phase计算变量x，并且以string类型返回结果
* > (x) //late phase计算变量x，并且以string类型返回结果
* < @(x) //early phase计算变量x，并且以list类型返回结果
* > @(x) //late phase计算变量x，并且以list类型返回结果
* x! //从已有的x字段中排除部分
* x/ //操作允许使用include/exclude，内容是一个正则表达式来进行包含和排除列表里面内容
* < !(x) //认为x是一个shell command，得到执行结果作为string类型返回
* < !@(x) //认为x是一个shell command，得到执行结果作为list类型返回

##### 1.6 路径内容属性
在includes这个小节提到了，gyp规定了某些属性的内容必须为路径。这些属性是：

* files.
* include<sub>dirs</sub>.
* inputs.
* libraries.
* outputs.
* sources.

但是gyp对于里面的内容也做了一些特殊处理。对于内容来说，如果以下面这些字符开头：

* / //绝对路径
* $ //变量
* - //链接参数比如-lm
* < ,>,! //operator
* 其他作为相对路径

##### 1.7 在Ubuntu下使用gyp
>安装工具： sudo apt-get install gyp

###### 1.7.1 简单实例
hello.c

```
#include <stdio.h>  
  
int main(){  
    printf("hello gyp\n");  
    return 0;  
} 
```

main.gyp

```
{    
   'targets': [    
     {    
       'target_name': 'hello',    
       'type': 'executable',    
       'sources': [    
         'hello.c',    
       ],    
     },    
   ],    
 }  
```

构建：

```
gyp --depth=./ main.gyp
```
编译
``` 
make
```
运行
```
./hello
```

###### 1.7.2 改进
把上面的构建命令做成脚本 genPrj.sh

```
#!/bin/bash
gyp --depth=./ --generator-output=./build main.gyp

if [ -d build ]; then
	cd build
	make
fi

```

所有生成非源码文件都生成到了build目录下，看起来比较干净，在做一个清除脚本，清除就更省事了。do_clean.sh:

```
#!/bin/bash
rm -rf build
```

###### 1.7.3 c++实例
目录结构

```
|-- do_clean.sh
|-- genPrj.sh
|---src
     |-- hello.cc
     |-- main.gyp
     |-- my_class
           |-- my_class.cc
           |-- my_class.h


```

genPrj.sh

```
#!/bin/bash
gyp --depth=. --generator-output=build src/main.gyp

if [ -d build ]; then
	cd bulid
	make
fi
```

hello.cc

```
#include <stdio.h>
#include "my_class/my_class.h"
int main(int argc, char** argv){
	printf("Hello world\n");
	MyClass my_class(100);
	my_class.Fun1();
}
```
main.gyp

```
{    
   'targets': [    
     {    
       'target_name': 'my_class',    
       'type': 'executable',    
       'sources': [    
         'hello.cc',    
         'my_class/my_class.h',    
         'my_class/my_class.cc',    
       ],    
     },    
   ],    
 } 
```
my_class.cc

```
#include "my_class.h"    
#include <stdio.h>    
void MyClass::Fun1() {    
  printf("the value is %d\n", value_);    
}  
```

my_class.h

```
class MyClass {    
public:    
  MyClass(int value) : value_(value) {}    
  void Fun1();    
private:    
  int value_;    
}; 
```

##### 1.8 ninja

gyp是google为chromium项目开发的管理工具，功能类似于cmake。gyp只能产生编译脚本，真正的编译工作还有靠其他工具，例如: ninja。
###### 1.8.1 安装gyp和ninja

```
sudo apt-get install gyp
sudo apt-get install ninja-build
```
###### 1.8.2 一个简单的例子
1. 首先准备一个源文件，我写了template_sample.cpp,代码如下：

	```
		#include <iostream>  
		using namespace std;  
		  
		template<typename T>  
		T add(T a, T b){ return a+b; }  
		  
		int main()  
		{  
		     cout<< add<int>(5,6)<<endl;  
		     cout<< add<double>(3.4,4.0)<<endl;  
		     return 0;  
		}   	
	```
2. 编写gyp使用的文件，build.gyp（后缀是.gyp即可，名字随意）

	```
		{  
		   'targets':[  
		        {  
		            'target_name':'an',  
		            'type':'executable',  
		            'dependencies':[],  
		            'defines':[],  
		            'include_dirs':[],  
		            'sources':[  
		                'template_sample.cpp',  
		            ],  
		            'conditions':[]  
		        }  
		    ],    
		}  
	
	```
3. 生成ninja脚本，命令如下：

	```
	gyp -depth=. -format=ninja ./build.gyp
	```
	
	其中`-depth=.` 表示在当前文件夹下寻找开始的gyp脚本，必须显示的指出gyp没有默认设置。 `-format=ninja` 表示要生成ninja的脚本，默认生成的是Makefile
	执行后，会在当前目录下生成一个out目录。其结构如下：
	
	```
		out
		|-- Default
				|-- an
				|-- build.ninja
				|-- obj
					  |-- an.ninja
					  |-- an.template_sample.o
	```

4. 编译，执行如下命令：
	
	```
		ninja -C out/Default
	```
	
	**注意**: -C是大写，执行成功后会在out/Default目录下生成一个叫做an的可执行文件，这个an就是在target_name字段指定的名字。
	
5. clean整个工程重新编译
	
	目前，gyp没有提供clean的方案，所以只能手动删除out目录。
	
###### 1.8.3 gyp脚本的编写

1. 如何增加编译、链接参数
	编译参数一般增加在conditions字段，根据系统的不同增加cflags和ldflags,如下：
	
	```
	{  
	   'targets':[  
	        {  
	            'target_name':'an',  
	            'type':'executable',  
	            'dependencies':[],  
	            'defines':[],  
	            'include_dirs':[],  
	            'sources':[  
	                'template_sample.cpp',  
	            ],  
	            'conditions':[  
	              ['OS=="win"',{  
	                             'cflags':[],  
	                             'ldflags':[]  
	                         },{  
	                              'cflags':[  
	                                   '--std=c++11',  
	                               ],  
	                              'ldflags':[]  
	                         }  
	              ],  
	            ]  
	        }  
	    ],  
	}  
	
	```
	
	注意conditions字段的变化：`'OS="win"'`是条件语句的后要有`,`号，第一个括号内表示条件为真时的参数，第二个括号表示条件为假时的参数。上面的例子中，我在不是win的系统上增加了`--std=c++11`这个参数。
	另外，这里有必要提一下链接顺序问题。在链接的时候，链接器对符号表的寻找是按照你输入的命令的顺序进行的，比如a.o需要libm.so的函数，那么libm.so在链接命令里就必须写在a.o的后面，因为链接器发现a.o有函数没有找到，就只会去他后面的库里找，如果你把libm.so写在前面，自然就找不到。所以在增加链接库的时候，不要在ldflags里增加-l类的参数，这类参数要加到libraries字段里 如：
	
	```
		{  
		   'targets':[  
		        {  
		            'target_name':'an',  
		            'type':'executable',  
		            'dependencies':[],  
		            'defines':[],  
		            'include_dirs':[],  
		            'sources':[  
		                'template_sample.cpp',  
		            ],  
		            'conditions':[  
		              ['OS=="win"',{  
		                             'cflags':[],  
		                             'ldflags':[]  
		                         },{  
		                              'cflags':[  
		                                   '--std=c++11',  
		                               ],  
		                              'ldflags':[]，  
		                              'libraries':[  
		                                  '-lm'  
		                              ]  
		                         }  
		              ],  
		            ]  
		        }  
		    ],  
		}  
	```
	
2. 头文件路径设定 --include_dirs字段的使用
	有如下工程目录结构
	
	```
		|-- build.gyp
		|-- include
		|		|-- define.h
		|-- main.cpp
	```
	
	main.cpp的代码如下
	
	```
		#include "defines.h"  
		#include <iostream>  
		using namespace std;  
		  
		int main(){  
		    cout<< MY_NUMBER <<endl;  
		    return 1;  
		}  
	```
	可见main.cpp文件引用了defines.h这个头文件，所以在编译的时候需要告诉编译系统到哪里去寻找这个defines.h文件。为此buid.gyp如下：
	
	```
		{  
		   'targets':[  
		        {  
		            'target_name':'an',  
		            'type':'executable',  
		            'dependencies':[],  
		            'defines':[],  
		            'include_dirs':[  
		                'include'  
		            ],  
		            'sources':[  
		                'main.cpp',  
		            ],  
		            'conditions':[]  
		        }  
		    ],    
		}  
	
	```
	可以看出头文件的位置include在include_dirs字段中被指出。这里需要注意，两个单引号之间一定是文件的路径，不要包含不必要的空格等其他字符。
	
3. 编译参数增加宏定义 --defines字段使用
	测试程序的源码如下：
	
	```
		#include <iostream>  
		using namespace std;  
		  
		#ifdef BIG_NUMBER  
		int out_number = 10;   
		#else  
		int out_number = 1;  
		#endif  
		int main(){  
		    cout<< out_number <<endl;  
		    return 1;  
		}  
	```
	可以看出，如果BIG_NUMBER宏被定义，则输出为10 ，否则为1。在使用Makefile的时候，我们可以通过为gcc增加-DBIG_NUBMER参数的方式来定义这个宏。那在gyp管理的时候，我们就要使用defines字段，代码如下：
	
	```
		{  
		   'targets':[  
		        {  
		            'target_name':'an',  
		            'type':'executable',  
		            'dependencies':[],  
		            'defines':[  
		                 'BIG_NUMBER'  
		            ],  
		            'include_dirs':[],  
		            'sources':[  
		                'main.cpp',  
		            ],  
		            'conditions':[]  
		        }  
		    ],    
		}  
	
	```
4. 编译静态库(动态库)
	
	有时我们不是要输出一个可执行文件，而是要编译一个静态库或动态库。这就要修改type字段，上文中type字段使用了executable，实际还可以设置为`static_library`,修改后的gyp脚本如下：
	
	```
		{  
		   'targets':[  
		        {  
		            'target_name':'an',  
		            'type':'static_library',  
		            'dependencies':[],  
		            'defines':[],  
		            'include_dirs':[],  
		            'sources':[  
		                'main.cpp',  
		            ],  
		            'conditions':[]  
		        }  
		    ],    
		}  
	```
这样在out/Default/obj的目录下就会生成一个liban.a的静态库。
	
	动态库的方式类似，只需把type字段设置为shared_library即可。
	另外还有一种使用变量的方法，在这里提前介绍一下。就是将type字段设置为`<(library)`,完整的代码如下：
	
	```
		{  
		   'targets':[  
		        {  
		            'target_name':'an',  
		            'type':'<(library)',  
		            'dependencies':[],  
		            'defines':[],  
		            'include_dirs':[],  
		            'sources':[  
		                'main.cpp',  
		            ],  
		            'conditions':[]  
		        }  
		    ],    
		}  

	```
	然后在使用gyp工具的时候指定这个library的值，如：
	
	```
		gyp -depth=. -format=ninja -Dlibrary=static_library ./build.gyp
	```
	
	这样生成ninja脚本后，也可以生成静态库，这样做的好处是你可以在真正输出时再决定输出静态库还是动态库。尤其对于要一次生成多个库的情况下，这种方法尤其好用。

##### 1.9 更多功能介绍

###### 设计准则：
- 关键字一致性：所有的关键字都和平台项目的配置字段相同
- 通过前缀表明配置属于的特定平台。比如：`msvs_disabled_warnings`,`xcode_framework_dirs`

**样例：**

```
	{
    "target_defaults": {
        "defines": [
            "U_STATIC_IMPLEMENTATION",
            [
                "LOGFILE",
                "foo.log"
            ]
        ],
        "include_dirs": [
            ".."
        ]
    },
    "targets": [
        {
            "target_name": "foo",
            "type": "static_library",
            "sources": [
                "foo/src/foo.cc",
                "foo/src/foo_main.cc"
            ],
            "include_dirs": [
                "foo",
                "foo/include"
            ],
            "conditions": [
                [
                    "OS==mac",
                    {
                        "sources": [
                            "platform_test_mac.mm"
                        ]
                    }
                ]
            ],
            "direct_dependent_settings": {
                "defines": [
                    "UNIT_TEST"
                ],
                "include_dirs": [
                    "foo",
                    "foo/include"
                ]
            }
        }
    ]
}
```

**结构元素**

.gyp文件中定义了一些targets和构建规则。

下面的关键字可以定义在最顶层：

- conditions:条件定义。 
- includes:包含.gypi文件的列表
	
	```
		{
			"targets": [
			  {
				"target_name": "Thread",
				"type": "executable",
				"includes": [
				"../common.gypi",
				"./thread.gypi"
				]
				...
			  }
			]
		}
	```
- target_defaults:默认的项目配置，每个项目(targets)的配置都需要从这个配置继承
- targets:项目列表

	```
		{
			"targets": [
			{
				"target_name": "hello1",
				"product_extension": "stuff",
				"type": "executable",
				"sources": [
				"hello.c"
				]
			},
			{
				"target_name": "hello2",
				"target_extension": "stuff",
				"type": "executable",
				"sources": [
				"hello.c"
				]
			}
			]
		}
	```
- variable: 定义了键值对，可以被其他地方以`<(varname)`的方式引用

	```
		{
		  'variables': {
		    'pi': 'import math; print math.pi',
		    'third_letters': "<(other_letters)HIJK",
		    'letters_list': 'ABCD',
		    'other_letters': '<(letters_list)EFG',
		    'check_included': '<(included_variable)',
		    'check_lists': [
		      '<(included_variable)',
		      '<(third_letters)',
		      
		    ],
		    'check_int': 5,
		    'check_str_int': '6',
		    'check_list_int': [
		      7,
		      '8',
		      9,
		      
		    ],
		    'not_int_1': ' 10',
		    'not_int_2': '11 ',
		    'not_int_3': '012',
		    'not_int_4': '13.0',
		    'not_int_5': '+14',
		    'negative_int': '-15',
		    'zero_int': '0',
		    
		  },
		  ...
		}
	```
	
**项目(targets)**

.gyp文件定义的一套构建项目的规则。targets中也可以包含`includes`,`conditions`和`variables`。下面的是一些targets中的专有字段：

- target_name: 指定定义的target的名称
- type: target的类型。支持`executable`、`static_library`、`shared_library`和`none`.其中`none`类型也很有用，可以作为处理资源、文档等特别项目的类型。例如，在Windows中,`none`将作为工具集类型的项目存在
- product_extension:指定target生成目标的扩展名，不包含'.'
- product_name: 指定tareget生成目标的文件名，与 `product_extension` 组成一个文件全名
- dependencies:指定target依赖的其他target

	```
		{
		  'targets': [
		    {
		      'target_name': 'a',
		      'type': 'static_library',
		      'dependencies': [
		        'b/b.gyp:b',
		        'c/c.gyp:*'
		      ],
		      'sources': [
		        'a.c',
		        
		      ],
		      
		    },
		    
		  ],
		  
		}
		}
	```
	
- defines: 定义了预处理宏。类似于C/C++命令行编译中的-D或者/D选项

	```
		{
		  'targets': [
		    {
		      'target_name': 'defines',
		      'type': 'executable',
		      'defines': [
		        'FOO',
		        'VALUE=1',
		        'PAREN_VALUE=(1+2+3)',
		        'HASH_VALUE="a#1"',
		        
		      ],
		      'sources': [
		        'defines.c',
		        
		      ],
		      
		    },
		    
		  ]
		}
	
	```
	
- include_dirs: 指定了包含文件的查找目录。类似于C/C++命令行编译中的-I或/I选项

	```
		{
		  'targets': [
		    {
		      'target_name': 'includes',
		      'type': 'executable',
		      'dependencies': [
		        'subdir/subdir_includes.gyp:subdir_includes',
		        
		      ],
		      'cflags': [
		        '-Ishadow1',
		        
		      ],
		      'include_dirs': [
		        '.',
		        'inc1',
		        'shadow2',
		        'subdir/inc2',
		        
		      ],
		      'sources': [
		        'includes.c',
		        
		      ],
		      
		    },
		    
		  ],
		  
		}
	
	```
- sources:列出了项目中的代码文件和一些项目相关的文件.`sources!`段中可以指定被排除的文件
- configurations:为targets定义的一套构建配置。
- link\_settings:指定target需要链接的库。`executable`和`shared_library` 类型的target需要指定链接库。这个段内可以指定target中可包含的除了`configurations`、`target_name`和`type`的所有配置。可以与`all_dependent_settings`、`direct_dependent_setting`做对比
- direct\_dependent\_settings:指定依赖本target的target设置。这个段内可以指定target中可包含的除了`configurations`、`target_name`和`type`的所有属性。可以与`all_dependent_settings`、`link_settings`做对比
- all\_dependent\_settings:
- libraries:指定target依赖的库，见`link_settings>libraries`

	```
		...

		'link_settings': {
		
		'libraries': [
		
		'libiconv.dylib',
		
		'<(ZLIB_DIR)contrib/minizip/libminizip.a',
		
		'libcurl.dylib',],
		
		},
		
		...

	
	```
- actions:针对输入的文件，定义了一组自定义的构建动作。
- copies:定义了一套拷贝动作。
- rules:
- target\_conditions:类似于conditions,但是起左右的时间比conditions晚
- msvs\_precompiled\_header:指定预编译头文件。只能用于Visual Studio
- msvs\_precompiled\_source:指定预编译源文件。只能用于Visual Studio
- msvs\_prebuild:生成之前事件。只能用于Visual Studio
- msvs\_postbuild:生成之后事件。只能用于Visual Studio

	```
		'msvs_postbuild':r'copy "${OutDir}${TargetName}" "C:\${TargetName}"'
	```
	
- msvs\_props:指定target的属性页文件(.vsprops)。只能用于Visual Studio
- msvs\_cygwin\_shell:指定action运行在cygwin下。只能用于Visual Studio
- msvs\_cygwin\_dirs:指定cygwin的目录。只能用于Visual Studio
- xcode\_config\_file:在xcode中，指定target的配置文件(.xcconfig)。只能用于xcode
- xcode\_framework\_dirs:在xcode中，指定框架的目录。只能用于xcode
