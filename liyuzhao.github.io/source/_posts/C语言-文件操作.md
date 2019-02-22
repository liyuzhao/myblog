---
title: C语言-文件操作
date: 2019-02-18 17:31:11
tags: [c, file]
categories: c
---

文件操作

### fopen

r 以只读方式打开文件， 该文件必须存在
r+ 以可读方式打开文件，该文件必须存在
rb+ 读写打开一个二进制文件，允许读写数据，文件必须存在
rw+ 读写打开一个文本文件，允许读和写
w 	打开只写文件，若文件存在则文件长度清为0，即该文件内容会消失，若文件不存在则建立该文件。
w+	打开可读写文件，若文件存在则文件长度清为零，即该文件内容会消失，若文件不存在则建立该文件。

a 	以追加的方式打开只写文件，若文件不存在，则会建立该文件，如果文件存在，写入的数据会被追加到文件末尾，即文件原先的内容不会被覆盖。(EOF符保留)

a+ 	以追加的方式打开可读写的文件。若文件不存在，则会建立该文件，如果文件存在，写入的数据会被追加到文件末尾，即文件原先的内容不会被覆盖。(原先的EOF符不保留)

### EOF与feof 函数文件结尾

如果已经是文件尾， feof 函数返回true.
EOF 用于一个字符一个字符输出时，当获取到的字符是EOF(其中EOF真实为-1)，则代表已经是文件尾。


<!-- more -->

### fprintf, fscanf, fgets, fputs 函数

这些函数都是通过 FILE * 来对文件进行读写

### stat 函数

```
#include <sys/stat.h>

int stat(const char* _fileName, struct stat * _stat);

```

stat.st_size; // 文件大小，单位：字节

函数的第一个参数代表文件名， 第二个参数是 struct stat 结构
得到的文件属性，包括文件建立时间，文件大小等信息。


### fread 和 fwrite 函数 

```
size_t fread(void *buffer, size_t size, size_t count, FILE * stream);

size_t fwrite(const void * buffer, size_t size, size_t count, FILE * stream);

```

**注意**：这个函数以二进制形式对文件进行操作，不局限于文本文件

返回值：返回实际写入的数据块数目


### fread 与 feof

注意一下两段代码的区别

```
	while(!feof(p))
	{
		fread(&buf, 1, sizeof(buf),p);
	}

```

```
	while(fread(&buf, 1, sizeof(buf), p))
```

### 通过 fwrite 将结构保存到二进制文件中

### fseek 函数

```
int fseek(FILE * _file, long _offset, int _orgin);
```

函数设置文件指针 steam的位置。如果执行成功， stream将指向以fromwhere 为基准，偏移offset(指针偏移量) 个字节的位置， 函数返回 0. 如果执行失败则不改变 stream 指向的位置，函数返回一个非0值。

实验得出，超过文件末尾位置，还是返回0，往回偏移超过首位置，还是返回0，请小心使用。

```
第一个参数， stream为文件指针
第二个参数， offset 为偏移量，正数表示正向偏移，负数表示负向偏移。
第三个参数， origin 设定从文件的哪里开始偏移，可能取值为：SEEK_CUR, SEEK_END 或 SEEK_SET。

SEEK_SET: 文件开头
SEEK_CUR: 当前位置
SEEK_END: 文件末尾
```

### ftell 函数

函数 ftell 用于得到文件位置指针当前位置相对于文件首的偏移字节数。在随机方式存取文件时，由于文件位置频繁的前后移动，程序不容易确定文件的当前位置。

```
long len = ftell(fp);
```

### fflush 函数

fflush 函数可以将缓冲区中任何未写入的数据写入文件中。

成功返回 0， 失败返回 EOF.

```
int fflush(FILE * _file);
```

### remove 函数

remove函数 删除指定文件

```
int remove(const char * _fileName);
```

参数filename为指定的要删除的文件名，如果是windows 下文件名与路径可以用反斜杠‘\’分隔，也可以用斜杠'/'分隔。


### rename 函数

rename 函数将指定文件改名

```
int rename(const char * _oldFilename, const char * _newFilename);
```

参数oldFilename为指定的要修改的文件名，newFilename 为修改后的文件名，如果是windows 下文件名与路径可以用反斜杠'\'分隔，也可以用斜杠'/'分隔。



### 输出错误信息（排错）：

```
include <errno.h>
strerror(errno) 
```




```c

#include <stdio.h>
#include <stdlib.h>
#include <string.h>


int main01(){
	FILE * p = fopen("tfile.txt", "w");// 用写的方式打开一个文件
	// 'w' 如果文件不存在，则创建文件，如果文件存在就覆盖
	fputs("hello world\n", p); // 向文件写入一个字符串
	fputs("aaabbb\n", p); // 向文件写入一个字符串
	fclose(p); // 关闭这个文件

	return 0;
}


int main02(){ // 写文件
	FILE * p = fopen("tfile.txt", "w");

	char s[1024] = { 0 };
	while(1){
		memset(s, 0, sizeof(s));
		//scanf("%s", s);
		gets(s);
		if(strcmp(s, "exit") == 0){
			break; // 如果用户输入的是exit, 那么循环退出
		}
		int len = strlen(s);
		s[len] = '\n';
		s[len + 1] = '\0';
		fputs(s, p);
	}
	fclose(p); // 关闭这个文件
	printf("complete\n");
	return 0;
}


int main03(){ // 读文件
	FILE *p = fopen("tfile.txt", "r");
	char s[1024] = {0};
	//feof(p); // 如果已经到了文件结尾，函数返回真
	while(!feof(p)){ // 如果没有到文件结尾，那么就一直循环
		memset(s, 0, sizeof(s));
		fgets(s, sizeof(s), p);
		printf("%s", s);
	}
	//fgets(s, sizeof(s), p); // 第一个参数是一个内存地址，第二个参数是这个内存的大小，第三个参数是文件指针
	//printf("%s\n", s);
	fclose(p);

	return 0;
}

void code(char * s)
{
	while(*s){
		(*s)++;
		s++;
	}
}

int main04(){ // 加密
	FILE *p = fopen("a.txt", "r");
	FILE *pw = fopen("b.txt", "w");
	char s[1024] = {0};
	//feof(p); // 如果已经到了文件结尾，函数返回真
	while(!feof(p)){ // 如果没有到文件结尾，那么就一直循环
		memset(s, 0, sizeof(s));
		fgets(s, sizeof(s), p);
		code(s);
		fputs(s, pw);
	}
	//fgets(s, sizeof(s), p); // 第一个参数是一个内存地址，第二个参数是这个内存的大小，第三个参数是文件指针
	//printf("%s\n", s);
	fclose(p);
	fclose(pw);

	return 0;
}

void decode(char *s)
{
	while(*s){
		(*s)--;
		s++;
	}
}
int main05(){ // 解密

	FILE *p = fopen("b.txt", "r");
	FILE *pw = fopen("c.txt", "w");
	char s[1024] = {0};
	//feof(p); // 如果已经到了文件结尾，函数返回真
	while(!feof(p)){ // 如果没有到文件结尾，那么就一直循环
		memset(s, 0, sizeof(s));
		fgets(s, sizeof(s), p);
		decode(s);
		fputs(s, pw);
	}
	//fgets(s, sizeof(s), p); // 第一个参数是一个内存地址，第二个参数是这个内存的大小，第三个参数是文件指针
	//printf("%s\n", s);
	fclose(p);
	fclose(pw);
	return 0;
}



int main06(){
	FILE *p = fopen("a.txt", "a");
	// 'a' 的含义是，如果文件不存在，那么就创建文件，如果文件存在，就在文件结尾追加数据
	if(p == NULL)
	{
		printf("file open fail\n");
	} else {
		fputs("hello", p);
		fclose(p);
	}

	printf("end\n");
}

int main07(){
	FILE *fp = fopen("a.txt", "r");
	if(fp == NULL)
	{
		printf("error\n");
	} else {
		//char c = getc(p); // 一次只读一个字符
		char c;
		while((c = getc(fp))!= EOF)
		{
			printf("%c", c);
		}
		fclose(fp);
	}
	printf("end\n");

	return 0;
}

int main(){ // 写一个字符
	FILE *p = fopen("a.txt", "w");
	if(p == NULL)
	{
		printf("error\n");
	} else {
		putc('a', p);
		putc('b', p);
		putc('c', p);

		fclose(p);
	}

	return 0;
}

```


```

#include <stdio.h>
#include <stdlib.h>
#include <string.h>


int main01(){
	FILE *p = fopen("a.txt", "r");
	while(!feof(p))
	{
		char buf[100] = { 0 };
		//fgets(buf, sizeof(buf), p);
		//fscanf(p, "%s", buf); // fscanf与scanf用法一致，fscanf是从一个文件读取输入
		int a = 0;
		int b = 0;
		fscanf(p, "%d + %d = ", &a, &b);
		printf("a = %d, b = %d\n", a, b);

	}
	fclose(p);
	return 0;
}

int main02(){
	FILE *p = fopen("a.txt", "w");
	char buf[100] = "hello world";
	int a = 6;
	int b = 7;

	// fprintf(p, "%s, %d, %d", buf, a, b); // 和printf功能一样，fprintf将数据输入到文件里面

	int i;
	for(i = 0; i < 10; i++)
	{
		fprintf(p, "array[%d] = %d\n", i, i);
	}

	fclose(p);


	return 0;
}
```


```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

int main01(){
	FILE *p = fopen("a.txt", "rb"); // 用读二进制的方式打开一个文件
	while(!feof(p))
	{
		char buf[1024] = {0};
		size_t res = fread(buf, sizeof(char), sizeof(buf), p); //第一个参数是缓冲区，第二个参数是读取的时候最小单位大小，第三个参数是一次读取几个单位，第四个参数是打开的文件指针。
		// fread的返回值代表读取了多少记录数
		int i;
		for(i = 0; i < res; i++)
		{
			printf("buf[%d] = %x\n", i, buf[i]);
		}
		//printf("%s", buf);
	}
	fclose(p);
	return 0;
}

int main02(){
	FILE *p = fopen("a.dat", "wb");
	char buf[1024] = {0};
	buf[0] = 'a';
	buf[1] = 'b';
	fwrite(buf, sizeof(char), 10, p);
	fclose(p);

	return 0;
}

void encode(char *p, size_t n)
{
	size_t i;
	for(i = 0; i < n; i++)
	{
		p[i] += 3;
	}
}

void decode(char *p, size_t n)
{
	size_t i;
	for(i = 0; i < n; i++)
	{
		p[i] -= 3;
	}
}

int main03(){ // 带加密
	// FILE *p = fopen("m.wmv", "rb");
	// FILE *p1 = fopen("n.wmv", "wb");
	FILE *p = fopen("a.txt", "rb");
	FILE *p1 = fopen("b.txt", "wb");

	char buf[1024 * 4];
	while(!feof(p))
	{
		memset(buf, 0, sizeof(buf));
		size_t res = fread(buf, sizeof(char), sizeof(buf), p);
		encode(buf, res);
		fwrite(buf, sizeof(char), res, p1);
	}
	fclose(p);
	fclose(p1);

	return 0;
}

int main(){ 
	clock_t c1 = clock();
	// 带解密
	// FILE *p = fopen("m.wmv", "rb");
	// FILE *p1 = fopen("n.wmv", "wb");
	FILE *p = fopen("b.txt", "rb");
	FILE *p1 = fopen("d.txt", "wb");

	char buf[1024 * 4];
	while(!feof(p))
	{
		memset(buf, 0, sizeof(buf));
		size_t res = fread(buf, sizeof(char), sizeof(buf), p);
		decode(buf, res);
		fwrite(buf, sizeof(char), res, p1);
	}
	fclose(p);
	fclose(p1);
	clock_t c2 = clock();
	printf("end %ld\n", (c2 - c1));

	return 0;
}

```

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>


struct student
{
	char name[100];
	int age;
};


int main01(){

	struct student st = {"刘德华", 30};
	FILE *p = fopen("a.dat", "wb");
	fwrite(&st, sizeof(st), 1, p);

	fclose(p);

	return 0;
}
int main(){
	struct student st = {0};
	FILE *p = fopen("a.dat", "rb");
	fread(&st, sizeof(st), 1, p);
	fclose(p);
	printf("name = %s, age = %d\n", st.name, st.age);

	return 0;
}
```

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>


struct student 
{
	char name[10];
	int age;
};


int main01(){

	struct student st[10] = { 0 };
 	int i;
 	for(i = 0; i < 10; i++)
 	{
 		printf("please input name:");
 		scanf("%s", st[i].name);
		printf("please input age:");
		scanf("%d", &st[i].age);
 	}

 	FILE *p = fopen("a.dat", "wb");
 	fwrite(st, sizeof(struct student), 10, p);
 	fclose(p);
	return 0;
}

int main02(){

	struct student st = { 0 };
 	
 	FILE *p = fopen("a.dat", "rb");

 	while(1)
 	{
 		memset(&st, 0, sizeof(struct student));
 		size_t res = fread(&st, sizeof(struct student), 1, p);
 		if(res == 0){
 			break;
 		}
 		printf("name = %s, age = %d\n", st.name, st.age);
 	}

 	fclose(p);
	return 0;
}

int main(){ // seek 

	struct student st = { 0 };
 	
 	FILE *p = fopen("a.dat", "rb");

 	// 从文件开始位置向后偏移结构student的大小
 	// fseek(p, sizeof(struct student), SEEK_SET);
 	// 第二个参数：偏移字节数
 	// 第三个参数：SEEK_SET, SEEK_CUR, SEEK_END

	memset(&st, 0, sizeof(struct student));
 	fread(&st, sizeof(struct student), 1, p);
 	printf("name = %s, age = %d\n", st.name, st.age);

 	fseek(p, -sizeof(struct student), SEEK_CUR);//从当前位置往回偏移

 	memset(&st, 0, sizeof(struct student));
 	fread(&st, sizeof(struct student), 1, p);
 	printf("name = %s, age = %d\n", st.name, st.age);

 	fseek(p, -sizeof(struct student), SEEK_END);//从文件结尾往回偏移

 	memset(&st, 0, sizeof(struct student));
 	fread(&st, sizeof(struct student), 1, p);
 	printf("name = %s, age = %d\n", st.name, st.age);

 	printf("ftell = %ld\n", ftell(p)); // ftell当前指针在哪一个字节

 	fclose(p);
	return 0;
}
```

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/stat.h>
#include <time.h>


int main(){

	
	char *fileName = "m.wmv";
	struct stat st = { 0 }; // 定义一个结构，名字叫st
	stat(fileName, &st); // 调用完stat函数后，文件相关的信息就保存在了st结构中
	//st.st_size 得到文件的大小
	// printf("%lld\n", st.st_size);

	char * array = malloc(st.st_size); // 根据文件大小在堆中动态的分配一块内存
	clock_t c1 = clock(); //得到系统当前时间，单位毫秒
	FILE * p = fopen(fileName, "rb");
	fread(array, sizeof(char), st.st_size, p);// 相当于一下整个文件放入内存
	fclose(p);

	p = fopen("w.wmv", "wb");
	fwrite(array, sizeof(char), st.st_size, p); // 将堆中的信息一下都写入文件
	fclose(p);
	clock_t c2 = clock();



	printf("end %ld\n", (c2 - c1));

	return 0;
}
```

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main01(){
	
	FILE *p = fopen("a.txt", "w");

	while(1){
		char buf[100] = {0};
		scanf("%s", buf);
		if(strcmp("exit", buf) == 0){
			break;
		}

		fprintf(p, "%s\n", buf);
		fflush(p); // fflush 将缓冲区的内容立刻写入文件
		//优势是，不会因为停电，或者电脑死机等故障导致缓冲区的内容丢失
		//缺点是，硬盘读写次数增加，导致程序效率低下，同时硬盘寿命变短
	}

	fclose(p);
	return 0;
}


int main02(){ // 删除某个文件

	remove("a.txt");
	printf("end\n");

	return 0;
}

int main(){ // 将指定文件改名

	rename("c.txt", "a.txt");
	printf("end\n");
	return 0;
}
```