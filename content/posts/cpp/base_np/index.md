---
title: Linux C 基础(一) 不包含指针部分
description: Linux C 基础(一) 不包含指针部分
date: 2022-05-22
tags:
- "C/C++"
---
---
<!--more-->

### 数据类型

```c
//数据类型
void data_type(){
    //基本类型
    char ch='a';
    short sh=123;
    int i=123456;
    long l=123L;
    long long ll=123LL;
    float f=1.11f;
    double d=1.11;
    printf("%c,%hi,%d,%ld,%lld,%f,%lf\n",
    ch,sh,i,l,ll,f,d);
    printf("sizeof(ch)=%d,sizeof(sh)=%d,sizeof(i)=%d,\
    sizeof(l)=%d,sizeof(ll)=%d,sizeof(f)=%d,sizeof(d)=%d\n",
    sizeof(ch),sizeof(sh),sizeof(i),sizeof(l),sizeof(ll),sizeof(f),sizeof(d));
    //数组
    int i_arr[10]={1,2,3,4};
    printf("i_arr: addr=%p,size=%d\n",i_arr,sizeof(i_arr));
    //字符数组 字符串
    char ch_arr[10]={'a','b','c','\0','d'};
    printf("ch_arr: addr=%p,size=%d,str=%s,strlen=%d\n",
    ch_arr,sizeof(ch_arr),ch_arr,strlen(ch_arr));
}
```

输出:

```sh
a,123,123456,123,123,1.110000,1.110000
sizeof(ch)=1,sizeof(sh)=2,sizeof(i)=4,    sizeof(l)=8,sizeof(ll)=8,sizeof(f)=4,sizeof(d)=8
i_arr: addr=0x7fffffffdbd0,size=40
ch_arr: addr=0x7fffffffdbfe,size=10,str=abc,strlen=3
```

### 结构体

```c
//结构体
struct Point
{
    int x;
    int y
};
void struct_type()
{
    // Point p1;//wrong
    struct Point p1;
    printf("p1(x,y)=(%d,%d)\n",p1.x,p1.y);
    struct cicle
    {
        struct Point o;//结构体组合
        int r;
    }c1,c2;
    printf("c1((x,y),r)=((%d,%d),%d)\n",c1.o.x,c1.o.y,c1.r);
    printf("c2((x,y),r)=((%d,%d),%d)\n",c2.o.x,c2.o.y,c2.r);
    //使用 typedef 使结构体变量声明简单化
    typedef struct Point Point;
    Point p2;
    printf("p2(x,y)=(%d,%d)\n",p2.x,p2.y);
    //直接使用typedef 定义结构体别名
    typedef struct
    {
     int x;
     int y;
     int z;  
    }Point_td;
    Point_td point_td;
    printf("ptd(x,y,z)=(%d,%d,%d)\n",point_td.x,point_td.y,point_td.z);
    //创建临时使用的结构体
    struct
    {
        int work_num;//4
        int age;//4
        char name_first;//1
    }temp1,temp2;
    printf("temp1(work_num,age,name_first)=(%d,%d,%c)\n",temp1.work_num,temp1.age,temp1.name_first);
    printf("temp2(work_num,age,name_first)=(%d,%d,%c)\n",temp2.work_num,temp2.age,temp2.name_first);

    //结构体 尺寸和数据对齐
    //结构体按照四字节对齐
    printf("sizeof(temp1)=%d\n",sizeof(temp1));
}
```

输出:

```sh
p1(x,y)=(0,-136489376)
c1((x,y),r)=((32767,1),0)
c2((x,y),r)=((1,0),1431666557)
p2(x,y)=(32767,-9160)
ptd(x,y,z)=(21845,-136430784,32767)
temp1(work_num,age,name_first)=(0,0,0)
temp2(work_num,age,name_first)=(21845,1431654240,U)
sizeof(temp1)=12
```



### 重要的修饰符

C file1:

```c
int count;

int plus_count(){
    count++;
}

static static_count;
static int static_plus_count(){
    static_count++;
}

int local_static_variable(){
    static count;
    printf("local_static_variable hash been called %d times\n",++count);
}
```

C file2:

```c
//标识符
void key_word(){
    //extern
    //使用外部变量
    extern int count;
    printf("another file global variabel:count=%d\n",count);
    //使用外部函数
    // extern int plus_count();
    //函数默认是 extern的
    plus_count();
    printf("another file global variabel:count=%d\n",count);
    //static
    //static 使函数或变量私有
    // printf("another file static variable:static_count=%d\n",static_count);
    // static_plus_count();
    //static 使局部变量拥有全局生命周期
    local_static_variable();
    local_static_variable();
    //const 
    //const 修饰常量，使其不可修改
    const int i=0;
    // i=1;
}
```

输出:

```sh
another file global variabel:count=0
another file global variabel:count=1
local_static_variable hash been called 1 times
local_static_variable hash been called 2 times
```

### 分支循环

```c
//分支循环
void branch_loop()
{
    printf("for:\n");
    for (int i = 0; i < 10; i++)
    {
        printf("i=%d ", i);
    }
    printf("\nwhile:\n");
    int i = 0;
    while (i <= 10)
    {
        printf("i=%d ", i++);
    }
    printf("\ndo...while:\n");
    i = 0;
    do
    {
        printf("i=%d ", i++);
    } while (i <= 10);
    printf("\nswitch:\n");
    scanf("%d", &i);
    switch (i)
    {
    case 1:
        printf("input 1\n");
        break;
    case 0:
        printf("input 2\n");
    default:
        break;
    }

    printf("\ngoto:\n");
    int count = 0;
plus_count:
    if (count != 1)
        printf("count not 1\n");
    else
        printf("count is 1\n");
    if (count != 1)
    {
        count++;
        goto plus_count;
    }
    int exit = 1;
    if (exit)
    {
        goto end;
    }
    printf("%d\n", exit);
end:
    printf("end!\n");
}
```

输出:

```shell
for:
i=0 i=1 i=2 i=3 i=4 i=5 i=6 i=7 i=8 i=9 
while:
i=0 i=1 i=2 i=3 i=4 i=5 i=6 i=7 i=8 i=9 i=10 
do...while:
i=0 i=1 i=2 i=3 i=4 i=5 i=6 i=7 i=8 i=9 i=10 
switch:
1
input 1

goto:
count not 1
count is 1
end!
```

### 函数

```c
// int func_dynamic_param(...)//wrong
int func_dynamic_param(int n, ...)
{
    printf("dynamic param:\n");
    va_list params;
     printf("gp_offset:%d,fp_offset:%d,overflow_arg_area=%#x,reg_save_area=%#x\n",
           params[0].gp_offset, params[0].fp_offset, 
           params[0].overflow_arg_area, params[0].reg_save_area);
    va_start(params, n);//设置params状态
    printf("gp_offset:%d,fp_offset:%d,overflow_arg_area=%#x,reg_save_area=%#x\n",
           params[0].gp_offset, params[0].fp_offset, 
           params[0].overflow_arg_area, params[0].reg_save_area);
    for (int i = 0; i < n; i++)
    {
        //访问参数列表中的变量
        printf("params[%d]=%d ", i, va_arg(params, int));
    }
    va_end(params); // va_end将params赋值为NULL
    printf("\n");
    return 1;
}
//函数
void function() //无参函数
{
    int n = 100;
    //有参函数 单向值传递
    int result = func_with_param(n);
    printf("result=%d\n", result);
    printf("n=%d\n", n);
    //变参函数
    func_dynamic_param(4, 2, 3, 4, 5);
}
```

输出:

```sh
100
1000
result=5
n=100
dynamic param:
gp_offset:16,fp_offset:48,overflow_arg_area=0xffffdc00,reg_save_area=0xffffdb40
gp_offset:8,fp_offset:48,overflow_arg_area=0xffffdc00,reg_save_area=0xffffdb40
params[0]=2 params[1]=3 params[2]=4 params[3]=5
```

### 宏

```c
//宏
void macro()
{
    //宏就是字符串替换
    #define UNIT 100.0 //无参宏
    //有参宏
    #define AREA_RECTANGLE(length, width) UNIT *length *width
        printf("AREA_CIRCLE(1)=%lf\n", AREA_RECTANGLE(1, 2));
    //可变参数宏
    #define LOG(fmt, ...) printf(fmt, __VA_ARGS__)
    #define DEBUG(...) printf(__VA_ARGS__)
    LOG("this is a log from %s\n","macro test");
    DEBUG("debuf message from %s\n","macro test");
}
```

输出:

```sh
AREA_CIRCLE(1)=200.000000
this is a log from macro test
debuf message from macro test
```

### 条件编译

```c
//条件编译
void condition_build()
{
    //所在文件只编译一次 
    //可以避免使用#ifndef判断宏是否定义
    #pragma once
    //编译到此处时输出
    #pragma message("this is pragma message")
    #ifndef A
        printf("define A\n");
        #define A 'A'
    #endif
    #ifdef A
        printf("A has define\n");
    #endif
    #if A=='A'
        printf("A define correct\n");
    #elif A!='A'
        printf("A define incorrect\n");
    #endif
    const B='B';
    #if B=='B'//条件编译在编译之前作判断 不能判断变量 只能判断宏的值
        printf("B=='B'\n");
    #endif
}
```

编译时输出:

```sh
note: #pragma message: this is pragma message
```

C/C++混编条件编译写法:

```c
#ifdef __cplusplus
	extern "C" {
	#endif
        //此处不能声明c语言没有的内容 比如class等
		int sum(int,int);
		int add_one(int);
        int delta(int a, int b);
		#include <stdio.h>
		
	#ifdef __cplusplus
	}
#endif // __cplusplus
```



输出:

```sh
define A
A has define
A define correct
```



### 多文件项目结构

**include**

参考[cppreference](https://en.cppreference.com/w/c/preprocessor/include)

本质:

> `#include 相当于将要包含的源文件复制到到当前#include指令后。`

两种用法:

> `#include<xx.h>只在标准库搜索,#include"xxx.h"搜索路径包含标准库和源文件(先搜当前路径，再搜标准库)`
>
> 一般包含标准库头文件使用#include<xx.h>,包含自己写的源文件使用#include"xxx.h"

什么时候需要使用#include包含头文件:

> 保证所有需要使用到的头文件至少被包含一次即可
>
> 由于写代码时往往不会去检查其它文件是否已经包含了本文件需要包含的头文件,所以一般需要使用某个头文件的地方都会加上#include包含进来,
>
> 这样就会导致宏重复定义等问题所以头文件需要处理多次包含的问题(条件编译或者#pragma once)

**一般源文件组织方式**

了解了#include指令之后，就很容易理解源文件组织方式了。

头文件中常见的内容:

1. 宏
2. 条件编译逻辑
3. 函数声明
4. 其它#include
5. 结构体
6. 全局变量

[项目地址](https://github.com/Yanshaoshuai/CPP_DEMO)
