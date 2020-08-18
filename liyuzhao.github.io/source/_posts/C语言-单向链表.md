---
title: C语言-单向链表
date: 2019-02-21 15:10:22
tags: [c, 单向链表]
categories: c
---

### 单向链表：

单向链表的组成包括一个链表头(head)和若干链表元素(node),对链表的基本操作其实就是增、删、改、查。 首先说说单向链表的C语言实现方法

<!-- more -->

```c
#include <stdio.h>
#include <stdlib.h>

struct list
{
	int data; // 数据域
	struct list *next;//指针域
};

struct list *create_list() // 建立一个节点
{
	return calloc(sizeof(struct list), 1);// 在堆中创建一个节点
}


void traverse(struct list *ls) // 循环遍历链表
{
	struct list *p = ls;
	while(p)
	{
		printf("%d\n", p->data);
		p = p->next; // p指向他对应的下一个节点
	}

}

struct list *insert_list(struct list *ls, int n, int data) // 在指定位置插入元素
{
	struct list *p = ls;
	while(p && n--)
	{
		p = p->next;
	}
	if(p == NULL)
	{
		return NULL; // n的位置大于链表节点数
	}

	struct list *node = create_list();//建立新的节点
	node->data = data;
	node->next = p->next;
	p->next = node;
	return node;
}

int delete_list(struct list *ls, int n) // 删除指定位置元素
{
	struct list * p = ls;
	struct list *pre = p;
	while(p && n--)
	{
		pre = p;
		p= p->next;
	}
	if(p == NULL)
	{
		return -1; //n的位置不合适
	}
	
	pre->next = p->next;

	free(p);
	return 1;
}

int count_list(struct list *ls)// 返回链表元素个数
{
	struct list *p = ls;
	int count = 0;
	while(p)
	{
		count++;
		p = p->next;
	}
	return count;
}

void clear_list(struct list *ls) // 清空链表，只保留首节点
{
	struct list *p = ls->next;
	while(p)
	{
		struct list *tmp = p;
		p = p->next;
		free(tmp);
	}
	ls->next = NULL;
}

int empty_list(struct list *ls)// 返回链表是否为空
{
	if(ls->next)
	{
		return 0;
	}else{
		return -1;
	}

}

struct list *locale_list(struct list *ls, int n)// 返回链表指定位置节点
{
	struct list *p = ls;
	while(p && n--)
	{
		p = p->next;
	}
	if (p == NULL)
		return NULL;
	return p;
}

struct list *elem_locale(struct list *ls, int data) // 返回数据域等于data的节点
{
	struct list *p = ls;
	while(p)
	{
		if(p->data == data)
			return p;

		p = p->next;
	}
	return NULL;
}


int elem_pos(struct list *ls, int data) //返回数据域等于data的节点位置
{
	struct list *p = ls;
	int index = 0;
	while(p)
	{
		index++;
		if(p->data == data)
			return index;
		p = p->next;
	}
	return -1;
}

struct list *last_list(struct list *ls) //得到链表最后一个节点
{
	struct list *p = ls;
	while(p)
	{
		if(p->next == NULL)
			return p;
		p = p->next;
	}
	return NULL;
}

void merge_list(struct list *ls1, struct list *ls2)//合并两个链表，结果放入ls1中
{
	// 只合并链表的节点，不合并链表头
	last_list(ls1)->next = ls2->next;
	free(ls2);
}

void reverse(struct list *ls) // 链表的逆置
{
	if(ls->next == NULL) 
		return; // 只有一个首节点，不需要逆置
	if(ls->next->next == NULL)
		return; // 只有两个节点，也不需要逆置

	struct list * last = ls->next; //逆置后ls->next

	struct list * pre = ls; // 上一个节点的指针
	struct list * cur = ls->next; // 当前节点的指针
	struct list * next = NULL; // 下一个节点的指针
	while(cur)
	{
		next = cur->next;
		cur->next = pre;
		pre = cur;
		cur = next;
	}
	ls->next = pre;
	last->next = NULL;
}


int main(){

	struct list *first = create_list(); 
	struct list *second = create_list();
	struct list *third = create_list();

	first->next = second;
	second->next = third;
	third->next = NULL;
	first->data = 1;
	second->data = 2;
	third->data = 3;

	traverse(first);
	insert_list(first, 1, 10);

	traverse(first);

	printf("count = %d\n", count_list(first));

	delete_list(first, 1);

	traverse(first);

	// clear_list(first);

	printf("count = %d\n", count_list(first));

	printf("locale_list(%d)->data = %d\n", 2,  locale_list(first, 2)->data);

	printf("last_list->data = %d\n", last_list(first)->data);

	printf("---------------------------\n");
	
	struct list *first1 = create_list();
	int i;
	for(i = 0; i < 10; i++)
	{
		insert_list(first1, 0, i);
	}
	traverse(first1);
	printf("---------------------------\n");
	// merge_list(first, first1);
	traverse(first);
	reverse(first);
	printf("---------------逆置------------\n");
	traverse(first);



	return 0;
}

```