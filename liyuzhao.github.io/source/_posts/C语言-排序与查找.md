---
title: C语言-排序与查找
date: 2019-02-21 12:42:43
tags: [排序, 查找, c]
categories: c
---

排序与查找

<!-- more -->

### 排序

常见排序有：冒泡排序、选择排序、插入排序、快速排序。


```c
#include <stdio.h>

void swap(int *a, int *b)
{
	int tmp = *a;
	*a = *b;
	*b = tmp;
}

void bubble(int *array, int n) // 冒泡排序
{
	int i;
	int j;
	for(i = 0; i < n; i++)
	{
		for(j = 1; j < n - i; j++)
		{
			if(array[j - 1] > array[j])
			{
				swap(&array[j - 1], &array[j]);
			}

		}
	}
}

// 查找指定范围的最小值
int  minkey(int *array, int low, int high)
// 第一个参数是一个数组，第二个参数是数组的开始下标，第三个参数是数组的终止下标
// 返回值是最小元素的下标
{
	int min = low;
	int key = array[low]; // 在没有查找最小元素之前，第一个元素是最小值
	int i;
	for(i = low + 1; i < high; i++)
	{
		if(key > array[i]){
			key = array[i];
			min = i;
		}
	}
	return min;
}

// 在一个集合里面找最小的数，将最小的数放到集合的最前面
void select(int * array, int n) // 选择排序法
{
	int i;
	for(i = 0; i < n; i++)
	{
		int j = minkey(array, i, n);
		if(i != j) // 证明范围内的第一个成员不是最小的
		{
			swap(&array[i], &array[j]);
		}
	}

}

void print(int *array, int n)
{
	int i;
	for(i = 0; i < n; i++)
	{
		printf("%d\n", array[i]);
	}
}

int main(){
	int array[10] = {32,  45, 76, 21, 2 , 5, 11,  33, 9, 10};

	// bubble(array, 10);
	// printf("min = %d\n", minkey(array, 0, 10));
	select(array, 10);
	
	print(array, 10);

	return 0;
}
```

### 查找

常见查找方法有：顺序查找，二分查找。

```c
#include <stdio.h>

int count = 0;

void swap(int *a, int *b)
{
	int tmp = *a;
	*a = *b;
	*b = tmp;
}

int partition(int *array, int low, int high)
{
	int base = array[low]; // 用子表的第一个记录做枢轴(分水岭)记录
	while(low < high)
	{
		while(array[high] >= base && low < high)
		{
			high--;
		}
		swap(&array[high], &array[low]);
		while(array[low] <= base && low < high)
		{
			low++;
		}
		swap(&array[high], &array[low]);
	}
	return low;
}

void quick_sort(int *array, int low, int high)
{
	if(low < high)
	{
		int part = partition(array, low, high);
		quick_sort(array, low, part - 1);
		quick_sort(array, part + 1, high);
	}
}

void print(int *array, int n)
{
	int i;
	for(i = 0; i < n; i++)
	{
		printf("%d\n", array[i]);
	}
}

// 顺序查找
int seq(int *array, int low, int high, int key)
{// 在指定范围内寻找和key相同的值，找到返回下标，找不到返回-1
	int i;
	for(i = low; i < high; i++)
	{
		count++;
		if(array[i] == key)
		{
			return i;
		}
	}

	return -1;
}

//二分查找，在已经排序的列表中查找
int bin(int *array, int low, int high, int key)
{
	while(low <= high)
	{
		int mid = (low + high) / 2;	
		if(key == array[mid])// 中间切一刀，正好和要查找的数相等
			return mid;
		else if (key > array[mid]) // 如果要找的数大于array[mid]， 那么就在下半部分切刀
			low = mid + 1;
		else // 如果要找到的数小于array[mid], 那么就在上半部分继续切刀
			high = mid - 1;
	}
	return -1; // 没有找到数据
}

int bin_rec(int *array, int low, int high, int key)
{
	if(low <= high)
	{
		int mid = (low + high) / 2;
		if(key == array[mid])
			return mid;
		else if (key > array[mid])
			return bin_rec(array, mid + 1, high, key);
		else 
			return bin_rec(array, low, mid - 1, key);
	} else{
		return -1;
	}
}

int main(){

	int array[10] = {32, 45, 76, 21, 56, 85, 23, 89, 15, 49};
	// int array[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

	// printf("%d\n", seq(array, 0, 10, 32));
	quick_sort(array, 0, 9);
	

	printf("%d\n", bin(array, 0, 10, 56));

	printf("%d\n", bin_rec(array, 0, 10, 56));

	printf("--------------------------------------\n");
	print(array, 10);
	// printf("count = %d\n", count);

	return 0;
}

```
