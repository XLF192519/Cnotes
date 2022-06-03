

# 6. 指针

## 6.1关于内存

​        内存可以分为物理内存和虚拟内存，其中虚拟内存是操作系统虚拟出来的内存，在32位操作系统下，每个进程的寻址范围都是0x00000000-0xffffffff，一共4G，我们所能看到的都是虚拟内存，**虚拟内存会和物理内存进行映射，一一对应。**

​        在运行程序的时候，操作系统会将虚拟内存进行分区：

​        堆：申请动态内存的时候，会在堆里面开辟内存。

​        栈：主要存放局部变量。

​        静态全局区：分为未初始化的静态全局和初始化的静态全局区，存放全局变量和静态变量。

​        代码区：存放程序代码。

​        文字常量区：存放常量的。

​        内存是以字节来存储数据的，可以将虚拟存址空间看做一个很大的一维的字符数组

## 6.2指针的概念

​        指针就是地址，就是虚拟内存的存储单元的地址编号；

​        指针变量是个变量，用这个变量存放一个地址编号。

​       **1.无论什么类型的地址，都是存储单元的编号，在32位平台下都是4个字节，即指针变量占四个字节。**

​       **2.对应类型的指针变量只能存放对应类型变量的地址**

​       **3.如果一个变量类型占位多个字节，那么它就会有多个存储单元，多个地址编号，这个时候编号最小的当做变量的地址。**（一个存储单元就是一个字节，对应一个地址编号）

##  6.3指针变量的定义

1.简单的指针变量

​        数据类型* 指针变量名称   int* p  ,变量名是p，*是指针修饰符，说明p是个指针变量。

2.运算符

​        &取地址，*取值

                     ```c
       int a =0x12345678;
       int num；
       int *p;    //定义一个指针变量`
       p=&a;    //p保存了a的地址，也可以说p指向了a`
       num=*p;  
                     ```

​        如果说a的地址为0xbf889868,那么p存放了这个地址。在调用指针的时候，星号代表的就是取值，*p和a都是一样的。如果要定义多个指针变量，每一个变量名前面都要有星号。

```c
#include<stdio.h>
	   int main()
	   {
	   	int a = 100, b = 200;
	   	int* p1, * p2 = &b;  //p1没有赋初值，成为野指针，指向哪里不确定
	   	p1 = &a;
	   	printf("%d\n", a);
	   	printf("%d\n", *p1);
        printf("%p\n",p);  //这个语句可以查看变量的地址，p可以换做&a。
	   	printf("%d\n", b);
	   	printf("%d\n", *p2);
	   	return 0;
	   }
```

​        在32位系统下，所有类型的指针都是4个字节

## 6.4指针的分类

### 6.4.1字符指针

  `char *p`,定义了一个字符指针变量，指针变量占四个字节，而字符只占一个字节。

### 6.4.2整型指针

```c
short int *p;

int *p;

long int *p;
```

​       上面都是多字节变量，占多个存储单元，每个存储单元都有自己的地址编号，将存储单元编号最小的那个编号作为多字节变量的地址编号，p存储该编号。

### 6.4.3浮点型指针

```c
float *p;
double *p;
```

### 6.4.4其它类型

数组指针；指针的指针；函数指针；结构体指针；通用指针void *p；

## 6.5指针与变量的关系

​        在定义指针的时候，*的作用是修饰，表明p是一个指针，而在调用的时候的时候，星号是取值的意思。

```c
 #include<stdio.h>
	   int main()
	   {
	   	int* p1, * p2, temp, a, b;    //对应类型的指针只能存放对应类型的变量,除非强制类型转换
	   	p1 = &a;         //p1指向a
	   	p2 = &b;         //p2指向b
	   	printf("请输入两个数字：\n");
	   	scanf_s("%d %d", p1, p2);      //给p1p2指向的变量赋值，这里直接写a,b的地址就可以，不需要加星号，加了星号会报错
	   	temp = *p1;            
	   	*p1 = *p2;
	   	*p2 = temp;
	   	printf("a=%d b=%d\n", a, b);
	   	printf("*p1=%d *p2=%d\n", *p1, *p2); 
	   	return 0;
	   }
```

​        上例的运行结果：输入100  200，输出a=200  b=100      *p1=200  *p2=100

​        如果想要不同类型的指针相互赋值，需要进行强制类型转换，如下例：

```c
#include<stdio.h>
int main()
{
	int a = 0x12345678, b = 0xabcdef66;
	char* p1, * p2;
	p1 = (char*)&a;     //指针类型与其指向的变量类型不一样时，需要将变量类型强制转换为与指针类型一样
	p2 = (char*)&b;     //int类型占四个字节，char占1个字节，那么取地址时，会将a的低位地址编号当做地址，只取一个字节
	printf("%0x %0x\n", *p1, *p2);
	p1++;
	p2++;    //指向下一个对应类型的数据    ，涉及到了指针运算，后面详细了解
	printf("%0x %0x\n", *p1, 0xff&( * p2));  // 这里不按位与会将ef打印成四个字节，前面自动补1了，暂时还不懂
	return 0;
}
```

<img src="C:\Users\12069\AppData\Roaming\Typora\typora-user-images\image-20220517164613150.png" alt="image-20220517164613150" style="zoom:67%;" />

​        **指针++，指向下个对应类型的数据，字符型则指针存放的地址编号加1，整型则加4**

## 6.6指针与数组

```c
int a[5];
int *p;
p=&a[0];   //指针变量p保存了数组a中第0个元素的地址，即a[0]的地址，如下图所示
```

<img src="C:\Users\12069\AppData\Roaming\Typora\typora-user-images\image-20220517165218143.png" alt="image-20220517165218143" style="zoom: 67%;" />

### 6.6.1数组元素的引用方法

​    假设一个数组a[5]={0,2,1,3,4}

1. 数组名[下标]

   ```c
   int a[5];
   a[2]=1;
   ```

2. 指针名[下标]

   ```c
   int a[5];
   int *p;
   p=a;   //数组的名字就是数组的首地址，即第0个元素的地址，就是&a[0],它是个常量
   p[2]=1; //相当于a[2]=1
   ```

   **p和a仍然是不同的，p是一个指针变量，而a是一个常量。所以可以给p赋值，但是不能给常量a赋值**

3. 指针变量运算加取值

   ```c
   int a[5];
   int *p;
   p=a;   
   *(p+2)=1; //相当于a[2]=1
   ```

4. 数组名加取值

   ```c
   int a[5];   
   *(a+2)=1; //相当于a[2]=1,这里a+2是a[2]的地址，而不是给a赋值
   ```

   ```c
    指针与数组
   #include<stdio.h>
   int main()
   {
   	int a[5] = { 0,1,2,3,4 };
   	int* p;
   	p = a;
   	printf("%d\n", a[2]);
   	printf("%d\n", p[2]);
   	printf("%d\n", *(p + 2));    ///数组元素的不同引用方法
   	printf("%d\n", *(a + 2));
   	printf("%p\n", p);
   	printf("%p\n", p+2);   //指针加法指向下一个变量的地址，本例中是整型占，所以理论上加8个字节
   	return 0;
   }
   ```

### 6.6.2指针的运算

1.指针加一个整数

​        指针加一个整数，往下指几个它指向的变量，结果仍然是一个地址，整型指针p+1，则地址编号加8，字符型指针p+1，则地址编号加1。

2.两个相同类型的指针可以比较大小

​        只有两个相同类型的指针指向同一个数组的元素的时候比较大小才有意义

3.两个相同类型的指针可以做减法

​        也必须是两个相同类型的指针指向同一个数组的元素的时候，做减法才有意义，其结果是，两个指针中间有多少个元素。

```c
#include<stdio.h>
int main()
{
	int a[5];
	int* p, * q;
	p = &a[0];   //规定数组的名字就是数组的首地址，也就是第0个元素的地址，是个常量，p=a同p=&a[0];
	q = &a[4];
	printf("%Id", q - p);    //运行结果为4
	return 0;
}
```

4.两个相同类型的指针可以相互赋值

```c
int *p,*q;
int a;
p=&a;
q=p;   //p和q都指向了a；
```

## 6.7指针数组（由指针组成的数组）

定义方法： 类型名 *数组名[元素个数]       int *p[5];  每个元素都是int *类型的变量

```c
#include <stdio.h>
int main()
{
    char *name[5]={"jack","lily","rob","jenny","henry"};
    int i;
    for(i=0;i<5;i++)
    {
        printf("%s\n",name[i]);  //name[0]指向的是hello这个字符串的首地址
    }
    return 0;
}
```

## 6.8指针的指针

指针的指针存放指针的地址编号

```c
#include<stdio.h>
int main()
{
	int a = 0x12345678;
	int* p, ** q;
	p = &a;
	q = &p;
	printf("%p\n", &a);   //a的地址
	printf("%p\n", p);    //a的地址
	printf("%p\n", &p);   //p的地址
	printf("%p\n", q);    //p的地址
	printf("%x %x %x", a, *p, **q);  //都是0x12345678
	return 0;
}
```

## 6.9字符串与指针

​         字符串的地址就是第一个字符的地址，我们可以定义一个字符指针变量来保存字符串的地址，例如           char *s="hello world"。

### 6.9.1字符串的存储形式

1.存放在数组中（其实就是在内存（栈和静态全局区）中开辟了一段空间存放字符串）

```c
#include<stdio.h>
int main()
{
	char str[100] = "I love C!";          
    printf("%s\n", str);      //输出"I love C!"
	str[0] = 'y';
	printf("%s\n", str);      //输出"y love C!"      
	return 0;
}
```

​        <u>存放在数组中的字符串是可以修改的。</u>

​        **补充：普通全局数组内存分配在静态全局区，普通局部数组内存分配在栈区，静态数组（静态全局或局部）内存分配在静态全局区。**

2.存放在文字常量区

```c
  char *str = "I love C!";
  *str = 'y'; //这是错误的，存放在文字常量区的内容不可以修改。
```

​        在文字常量区开辟了一段空间存放字符串，并将字符串的首地址付给指针变量。

​        **<u>注意："I love C!"是存放在了文字常量区，而不是存放在指针变量中，指针变量只存放了这个字符串的地址。</u>**

3.存放在堆区

 ```c
#include<stdio.h>
#include<stdlib.h>  //包含malloc
#include<string.h>  //包含strcpy
int main()
{
	char* str = (char*)malloc(10);  //在动态内存中申请了10个字节的存储空间，首地址给str赋值
	strcpy(str, "I love C!");   //将字符串"I love C!"拷贝到str指向的内存里。
	printf("%s\n", str);
	*str = 'y';
	printf("%s\n", str);

	return 0;
}
 ```

​         str指向堆区的时候，str所指向的内存的内容是可以修改的。同时，指针指向堆区的时候，不能初始化，只能先给指针赋值（即分配地址，malloc），再使用strcpy、scanf等方法把字符串拷贝到堆区

### 6.6.3字符串使用时赋值

1.字符数组

```c
char str[20]="hello world";
str="hello kitty"   //这是错误的，因为str作为数组名即数组的首地址，是一个常量，不能给常量赋值
strcpy(str,"hello kitty");  //正确，修改的是数组的内容
scanf("%s",buf);    //正确，这里也是修改的数组的内容
```

2.文字常量区

```c
char *str="hello world";
str ="hello kitty";   //这是正确的，相当于指向另一个字符串
strcpy(str,"hello kitty");//错误的，文字常量区的内容不可以修改，注意strcpy的用法
```

3.堆区

```c
char *str;
str=(char*)malloc(15);
strcpy(str,"hello kitty");  // 正确
scanf_s("%s",str);  //正确
```

4.补充

```c
char buf[20]="hello world";
char *str=buf;
```

​        在上面这种情况下，可以修改buf数组的内容；可以修改str指向的内存的内容，进而修改buf数组，如*str=w；不能给buf赋值；可以给str赋值，让它指向别处。

```c
#include<stdio.h>
int main()
{
	char buf[20] = "hello world";
	char* str = buf;
	buf[0] = 'w';
	printf("%s\n", buf);   //输出 wello world
	*str = 'w';
	printf("%s\n", str);   //输出 wello world
	str = "hello kitty";
	printf("%s\n", str);   //输出 hello kitty

	return 0;

}
```

## 6.10数组指针

### 6.10.1二维数组

```c
#include<stdio.h>
int main()
{
	int a[3][5];
	printf("a=%p\n", a);  //a=000000D0FBEFF4E8
	printf("a=%p\n", a+1); //a=000000D0FBEFF4FC
	return 0;
}
```

​        二维数组由一维数组组成，每一个一维数组都是一个元素，数组的名字是数组的首地址也是第0个元素的地址，是个常量，数组名加一时指向下一个元素，即指向下一个一维数组，在例子中a+1比a多20个字节。

### 6.10.2数组指针的定义

​        数组指针本身是一个指针，指向一个数组，加1跳一个数组，指向下一个数组。

​        定义：指向数组的类型（*指针变量名）[指向的数组的元素个数]

​        int(*p)[5],定义了一个数组指针变量p，p指向的是整型的有五个元素的数组，p+1往下指5个整型，跳过一个有5个整型元素的数组。

```c
#include<stdio.h>
int main()
{
	int a[3][5];
	int(*p)[5];
	p = a;
	printf("%p\n", a); //此外需要注意，a与a[0][0]的地址编号一样，但是这两个类型不一样
                       //a+1跳20字节，a[0][0]+1跳4字节
	printf("%p\n", a + 1);
	printf("%p\n", p);
	printf("%p\n", p + 1);
	return 0;

	//00000080D532FBB8
	//00000080D532FBCC
    //00000080D532FBB8
	//00000080D532FBCC
}
```

​        下面是数组指针的用法举例

```c
#include<stdio.h>
void fun(int(*p)[5], int x, int y)   
{
	p[0][1] = 100;
}
int main()
{
	int i, j;
	int a[3][5] = {
		{1,2,3,4,5},
		{6,7,8,9,0},
		{5,4,3,2,1}
	};
	fun(a, 3, 5);
	for (i = 0; i < 3; i++)
	{
		for (j = 0; j < 5; j++)
		{
			printf("%d ", a[i][j]);
		}
		printf("\n");
	}
	return 0;
}
```

​        各类数组指针的定义：

（1）一维数组指针

​        配合每行有5个元素的二维数组使用

```c
int(*p)[5];
int a[3][5];
p=a;
int b[5][5];
p=b;      //都是可以的
```

（2）二维数组指针

​        配合三维数组使用

```c
int(*p)[4][5];
int a[3][4][5];
p=a;   //+1时直接跳80个字节
```

（3）其它类型的数组指针以此类推

### 6.10.3数组名字取地址

​         数组名字取地址就会变成数组指针，一维数组名取地址变成一维的数组指针,以此类推。

```c
#include<stdio.h>
int main()
{
	int a[10];
	printf("%p\n", a);    //000000FBBFB7F868
	printf("%p\n", a+1);  //000000FBBFB7F86C
	printf("%p\n", &a);   //000000FBBFB7F868
	printf("%p\n", &a+1); //000000FBBFB7F890    &a变成了一个int(*p)[10]类型的以为数组指针
	return 0;
}
```

### 6.10.4数组名字与指针变量的区别

```c
int a[10];
int*p;
p=a;
//a是数组的名字，是a[0]的地址，是一个常量,p=a即p保存了a[o]的地址，在引用数组时，a与p等价
//a时一个常量，不可以对其赋值，而可对p赋值
//对a取地址会变为一个一维的数组指针，对p取地址变为二次指针
```

### 6.10.5数组指针取星号

​        简单来说就是降维打击，一维数组指针取*，结果为它指向的一维数组0号元素的地址，还是指向同一个地方

二维数组取*，结果为一维数组指针，他们还是指向同一个地方，以此类推。

```c
#include<stdio.h>
int main()
{
	int a[3][5];
	int(*p)[5];
	p = a;
	printf("a=%p\n", a);//a 是一维数组指针，指向第0 个一维数组，即第0 行
	printf("*a=%p\n", *a);//*a 是第0 行第0 个元素的地址，即&a[0][0]
	printf("*a +1=%p\n", *a + 1);//*a +1 是第0 行第1 个元的地址，即&a[0][1]
	printf("p=%p\n", p);//p 是一维数组指针，指向第0 个一维数组，即第0 行
	printf("*p=%p\n", *p);//*p 是第0 行第0 个元素的地址，即&a[0][0]
	printf("*p +1=%p\n", *p + 1);//*p +1 是第0 行第1 个元的地址，即&a[0][1]
	return 0;
}
```

## 6.11指针与函数

### 6.11.1指针作为函数的参数

​        我们可以给函数传一个整型、字符型、浮点型的数据，也可以给函数传一个地址。

下面是函数传参的两个例子：

1.传数值

```c
#include<stdio.h>
void swap(int x, int y)  //x/y叫做形参
{
	int temp;
	temp = x;
	x = y;
	y = temp;
}
int main()
{
	int a = 10, b = 20;
	swap(a, b);   //a，b叫做实参
	printf("a=%d b=%d\n", a, b);//a=10 b=20
}
```

​      给被调函数传数值，只能改变形参的值，而无法改变实参的值  

2.传地址

```c
#include<stdio.h>
void swap(int* p1, int* p2)
{
	int temp;
	temp = *p1;
	*p1 = *p2;// p2 指向的变量的值，给p1 指向的变量赋值
	*p2 = temp;
	printf("%p\n", p1);
	printf("%p\n", p2);
}
int main()
{
	int a = 10, b = 20;
	swap(&a, &b);
	printf("%p\n", &a);
	printf("%p\n", &b);
	printf("a=%d b=%d\n", a, b);//结果为a=20 b=10
	return 0;
}
```

​        调用函数传地址，在被调函数中通过*+地址来改变主调函数中变量的值，而且可以从上面的例子可以看出，p1和a的地址一样，p2与b的地址一样

```c
#include<stdio.h>
void swap(int* p1, int* p2)//&a &b
{
	int* p;
	p = p1;
	p1 = p2;//p1 =&b 让p1 指向main 中的b
	p2 = p;//让p2 指向main 函数中a
	printf("%p\n", p1);
	printf("%p\n", p2);
}                 //此函数中改变的是p1 和p2 的指向，并没有给main 中的a 和b 赋值
int main()
{
	int a = 10, b = 20;
	swap(&a, &b);
	printf("%p\n", &a);
	printf("%p\n", &b);
	printf("a=%d b=%d\n", a, b);//结果为a=10 b=20
}
```

​        总结就是：要想改变主调函数的变量的值，必须传变量地址过去，而且必须通过*+地址赋值，单纯的地址赋值，只会改变被调函数中指针的指向，而不会改变主调函数中变量的值。上面的打印的地址可以发现p1=b，p2=a

```c
#include<stdio.h>
void fun(char* q)
{
	q = "hello kitty";    //q首先指向主函数字符串首地址，后来又重新指向了别的字符串，而没有改变主函数中p指向的内容
	printf("%p\n", q);    //00007FF7E0429C18
}
int main()
{
	char* p = "hello world"; //p指向字符串的首地址，是一个指针变量
	fun(p);  //传入的是字符串的首地址，不是变量的地址，变量的地址相当于指针的指针
	printf("%p\n", p);   //00007FF7E0429C28
	printf("%s\n", p);//结果为： hello world
    return 0;
}
```

```c
#include<stdio.h>
void fun(char** q)
{
	*q = "hello kitty";    //  相当于p="hello kitty"
	printf("%p\n", &(*q));    //00000037C27DF858
}
int main()
{
	char* p = "hello world"; //p指向字符串的首地址，是一个指针变量
	fun(&p);  //传入的是指针变量的地址，是一个二级指针
	printf("%p\n", &p);   //00000037C27DF858
	printf("%s\n", p);//结果为： hello kitty

	p = "abcd";
	printf("%s\n", p);   //abcd
	printf("%p\n", &p);  //00000037C27DF858
	return 0;
}
//p是字符串首地址；&p是指针p的地址；**q接收二级指针&p，相当于指向p的地址，那么*q就相当于p
```

​        无论变量是什么类型的都要给函数传地址，指针变量则传二级指针

3.传数组

​        **<u>给函数传数组时，无法将整个数组传过去，只能传数组名进去，也就是传入了数组的首地址。</u>**

传一维数组

```c
#include<stdio.h>
void fun(int* p)   //另外一种是 void fun(int p[])
{
	printf("%d\n", p[2]);
	printf("%d\n", *(p+3));
}
int main()
{
	int a[10] = { 1,2,3,4,5,6,7,8,9,0 };
	fun(a);    //这里a就是一个*p类型的变量，传入的就是地址，在被调函数中p指向的也是这个数组
	return 0;
}
```

传二维数组

```c
#include<stdio.h>
//void fun( int p[][4] )//形式1
void fun(int(*p)[4],int x,int y)//形式2 ,形参是数组指针，一个指向数组的指针
{
	int i, j;
	p[1][2] = 77;
	for (i = 0; i < x; i++)
	{
		for(j = 0; j < y;j++ )
		{
			printf("%d ", p[i][j]);
		}
		printf("\n");
	}
}
int main()
{
	int a[3][4] = { {1,2,3,4},{5,6,7,8},{9,10,11,12} };
	fun(a,3,4);
	return 0;
}
```

传指针数组（这是一个数组）

```c
#include<stdio.h>
void fun(char** q) // char *q[]
{
	int i;
	for (i = 0; i < 3; i++)
		printf("%s\n", q[i]);  //q与p是等价的
}
int main()
{
	char* p[3] = { "hello","world","kitty" }; //指针数组的名就是一个二级指针
	fun(p);
	return 0;
}
```

### 6.11.2指针作为函数的返回值

```c
#include<stdio.h>
char* fun()    //函数返回指针
{
	char str[100] = "hello world";
	return str; 
}
int main()
{
	char* p;
	p = fun(); //返回的指针指向的内容已经被释放，返回这个地址已经没有意义
	printf("%s\n", p);//p相当于一个野指针，所以打出乱码
	return 0;
}
```

​        返回地址的时候，地址指向的内容不可以被释放

1.返回静态局部数组的地址

```c
#include<stdio.h>
char* fun()
{
	static char str[100] = "hello world";//静态数组的内容，在函数结束后，仍然存在
	return str;
}
int main()
{
	char* p;
	p = fun();    
	printf("%s\n", p); //输出hello world
	return 0;
}
```

2.返回文字常量区的地址

```c
#include<stdio.h>
char* fun()
{
char*str = "hello world";  //这是存放在文字常量区的内容，不会变
return str;
}
int main()
{
	char* p;
	p = fun();    //p去接fun返回的地址
	printf("%s\n", p);//hello world
}
```

3.返回堆区的地址（堆区的内容一直存在，直到free才释放）

```c
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
char* fun()
{
	char* str;
	str = (char*)malloc(100);
	strcpy(str, "hello world");
	return str;
}
int main()
{
	char* p;
	p = fun();
	printf("%s\n", p);//hello world
	free(p);
	printf("%s\n", p);  //free后变成野指针，输入乱码
	return 0;
}
```

​        ***<u>总结：返回的地址里面的内容不能被释放才可以，这样返回的地址才有意义。*</u>**

### 6.11.3函数指针

1.概念

​        **<u>函数也是有起始地址的，c语言规定，函数名就是函数的首地址，即函数的入口地址</u>**，所以可以定义一个指针来存放函数的地址，这个指针就是函数指针。

2.定义

​        返回值类型(*函数指针变量名)(形参列表),其中形参列表只需要填入其类型即可  

​        int(*p)(int,int)，定义了一个函数指针p，p指向的函数必须有一个整型的返回值，有两个整型参数

3.函数的调用办法

​      通过函数的名字去调函数

```c
#include<stdio.h>
int max(int x, int y)
{
	int tmp;
	if (x > y)
		tmp = x;
	else
		tmp = y;

	return tmp;
}
int main()
{
	int num;
	num = max(1, 2);
	printf("%d", num);
	return 0;
}
```

​       函数指针变量取调用

```c
#include<stdio.h>
int max(int x, int y)
{
	int tmp;
	if (x > y)
		tmp = x;
	else
		tmp = y;

	return tmp;
}
int main()
{
	int num;
	int(*p)(int, int);    //函数指针的定义
	p = max;              //p存放函数的地址
	num = (*p)(1,2);
	printf("%d", num);
	return 0;
}
```

4.函数指针数组（是一个数组，里面的每一个元素都是一个函数指针）

​        数组里面的函数指针是连续存储的，其定义为： 类型名(*数组名[元素个数])(形参列表)

​        **int(*p[5])(int,int);**数组名是p，有五个元素p[0]-p[4],每个函数指针指向的函数，必须有整型的返回值，每个函数都有两个整型参数

```c
#include<stdio.h>
int max(int x, int y)
{
	int temp;
	if (x > y)
		temp = x;
	else
		temp = y;
	return temp;
}
int min(int x, int y)
{
	int temp;
	if (x < y)
		temp = x;
	else
		temp = y;
	return temp;
}
int add(int x, int y)
{
	return x + y;
}
int sub(int x, int y)
{
	return x - y;
}
int mux(int x, int y)
{
	return x * y;
}
int main()
{
	int(*p[5])(int, int) = { mux,min,add,sub,mux };
	int num;
	num = (*p[2])(10, 20);   //调用方法
	printf("num=%d\n", num);
	return 0;
}
```

5.函数指针给函数传参

```c
#include<stdio.h>
int add(int x, int y)
{
	return x + y;
}
int sub(int x, int y)
{
	return x - y;
}
int mux(int x, int y)
{
	return x * y;
}
int dive(int x, int y)
{
	return x / y;
}
int process(int (*p)(int, int), int a, int b)
{
	int ret;
	ret = (*p)(a, b);
	return ret;
}
int main()
{
	int num;
	num = process(add, 2, 3);   //函数名就相当于函数指针
	printf("num =%d\n", num);
	num = process(sub, 2, 3);
	printf("num =%d\n", num);
	num = process(mux, 2, 3);
	printf("num =%d\n", num);
	num = process(dive, 2, 3);
	printf("num =%d\n", num);
	return 0;
}
```

## 6.12特殊指针

### 6.12.1空类型指针（void*）

​        void* 是通用指针，任何类型的地址就可以给空类型的指针变量赋值，在32位系统下，也占4字节

```c
int *p;
void *q;
q=p;  //不用强制类型转换
```

### 6.12.2空指针（NULL）

​        指针哪里都不指向，或者说指向内存编号位0的存储单位，一般用于指针初始化

```c
#include<stdio.h>
int main()
{
	char* p=NULL;
	printf("%p", p);   //0000000000000000,占四字节
	return 0;
}
```

## 6.13main函数传参

```c
#include <stdio.h>
int main(int argc, char *argv[]) //argc是参数个数，argv[]表示字符串首地址
{
int i;
printf("argc=%d\n",argc);
for(i=0;i<argc;i++)
{
printf("argv[%d]=%s\n",i,argv[i]);
}
return 0;
}   //需要在终端中输入代码才有结果输出
```





# 7.动态申请内存



这里仅仅简单的做一个测试。