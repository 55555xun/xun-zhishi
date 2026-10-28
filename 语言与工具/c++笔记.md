# c++

```c++
#include<iostream>
using namespace std;


int main()
{
   
    return 0;
}
```

-------

## 基本

### 数据类型

| 类型   | 整型 | 浮点型               | 布尔型 | 字符型 | 无类型 | 宽字符型 |      |
| ------ | ---- | -------------------- | ------ | ------ | ------ | -------- | ---- |
| 关键字 | int  | float(单),double(双) | bool   | char   | void   | wchar_t  |      |
| 字节   | 4    | 4，8                 |        | 1      |        |          |      |

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-08-11 140601.png)

---

### 转义字符

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-08-11 140723.png)

---

### 运算符

#### 赋值运算符

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-08-11 141302.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-08-11 141404.png)

#### 比较运算符

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-08-11 141623.png)

#### 逻辑运算符

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-08-11 141737.png)

#### 运算符优先级

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-08-11 141917.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-08-11 142035.png)

-------

## 分支控制

### 1.if语句

```c++
单分支
if(条件)
{
    
}

双分支
if()
{
    
}else if()
{
    
}

多分支
 ...
```

---

### 2.switch语句

```c++
switch(n)
{
    case constant1:    	break;
        .
        .
        .
    case constantn:    	break;
    default:	`		break;
        如果n都不匹配
            
}
```

-----

### 3.while语句

```c++
while(条件)  //先判
{
    
}
-----------
do{			//先运
    
}while(cond)
    
```

----

### 4.for语句

```c++
for(初始;条件;运行)
{
    
}
```

----

### 5.break和continue

```
bresk 终止循环
continue 终止该次循环
```

---

### 6.随机数

rand()伪随机数

```c++
#include<iostream>
#include<cstdlib>
using namespace std;


int main()
{
    cout << rand() << endl;
    return 0;
}
 
```

srand()

```c++
//#include<ctime> 获取系统时间
#include<iostream>
#include<cstdlib>
#include<ctime>
using namespace std;


int main()
{
    srand(time(0));
    cout << rand() << endl;
    return 0;
}
```

---

## 数组和字符

### 一维数组

```c++
int a[100];

```

### 字符数组

```c++
char a[count];
char a[count]="a,s,d";
char a[count]={'a','d','s'};
char a[count]={"asfdfsd"};
//取地址(void*)a
//字符数组输入输出
 	gets_s(a);//带空格也行
	puts_s(a);


```

### 字符数组操作

```c++
#include<iostream>
#include<cstring>
using namespace std;
int main()
{
    //字符数组元素个数
    int t=strlen(a);
    
    //复制
    strcpy(a1,a2);//2->1
    
    //前n个复制
    strncpy(a1,a2,n);
    
    //比较
    strcmp(a1,a2);//a1>a2-->1;a1==a2-->0;a1<a2-->-1
    
    //比较（不计大小写）
    stricmp(a1,a2);
    strnicmp(a1,a2,n)//比前n个
        
    //连接
    strcat(a1,a2);//a1a2
    strncat(a2,a2,n)//前n个
        
    //查找字符
    strchr(a1,'x');//左到右
    strrchr(a1,'x')//从右到左  
        
    //大小写转化    
    strlwr(a)//小
    strupr(a)//大    
    
    // 初始化   
    memset(a1,'a',sizeof(a1));  
    
    //字符检测
    isalpha(a);//是否为字母
    isupper(a);//是否为大写
    islower(a);//是否为小写
    isdigit(a);//是否为数字
    isxdigit();//是否为十六进制数字表示的有效字符
    issspace();//是否为空格类字符
    iscntrl();//是否为控制字符
    ispunct();//是否为标点符号
    isalunm();//是否为字母数字
    isprint();//是否为可打印字符
    isgraph();//是否为图形字符
        
        
    return 0;
}
```

----

### 字符串

```c++
#include<iostream>
#include<string>
#include<algorithm>
using namespace std;


int main()
{
    //构造函数
    string a1("hello");
    string a2(10, 'a');
    string a3(a1);
    string a4;
    a4 = "hello";


    //求长度
    int n = a1.size();
    int n1 = a1.length();

    //复制
    string a5;
    a5 = a1;

    //拼接
    a5 = a1 + a2;//a1=a1+a2

    a1.append(a2);//a1a2

    a1.push_back('h');//后加一个字符

    //比较（==，!=，<=,>=,>,<)

    int n1 = a1.compare(a2);
    int n2 = a1.compare(1, 3, a2);
    int n3 = a1.compare(1, 3, a2, 1, 2);

    //截取子串
    string a6 = a1.substr(0, 5);

    //访问
    a1[0];
    a1.at(1);

    //查找
    int n = a1.find("asd");
    int n = a1.find("asd", 0, 2);
    int n = a1.find('c', 0);
    int n = a1.rfind("as");//从右往左
    int n = a1.find_first_of("jkd");//任意一个字符第一次出现的位置
    int n = a1.find_first_not_of("jkd");//查参数外第一个字符
    int n = a1.find_last_of("jkd");//任意一个字符最后一次出现的位置
    int n = a1.find_last_of("jkd");//查参数外最后一个字符

    //插入
    a1.insert(1, "123");//1是位置
    a1.insert(1, "123", 2, 3);//在1位置插入2到3的字符
    a1.insert(1, "123", 2);//在1位置插入前两个字符
    a1.insert(2, 10, 'a');//在2的位置插入10个字符
    a1.insert(a1.begin() + 1, 3, 'H');

    //删除
    a1.erase(5);//删除n位置以后的数据
    a1.erase(a1.begin() + 5);//只删除n位置
    a1.erase(a1.begin() + 5,a1.begin()+5+3);//删除n到n+N位置
   //常用
    string::iterator start = a1.begin() + a1.find("abc");//找到我要删除的位置
    a1.erase(start, start + 3);

    a1.erase(a1.find("abc"), 3);//删除区间内的字符（n,m)

    //判空
    a1.empty();

    //交换
    a1.swap(a2);//交换a1和a2

    //输入输出
    /*
    * 分割符
    * 传统：空格回车，输入内容都原封不动地存入容器中指导用户敲入回车
    *   cin  空格
    *   getline(cin,a1)  换行
    */
    getline(cin, a1);

    //替换
    a1.replace(1,3,"mj");//把1到3位置替换为mj

    //字符串反转 #include<algorithm>
    reverse(a1.begin(), a1.end());
   
    return 0;
}
```

---

## 函数

```c++
void  a(int b);//声明

void a(int b)//定义
{
    
}

```

定义时在数据类型后，变量名前加“&”来定义引用类型（别名）

---

内联函数：用inline修饰符（简单函数使用）

```c++
inline void foo()
```

---

函数重载
函数重载是C++允许在同一作用域中声明几个类似的同名函数,这些同名函数的形参列表(参数个数,类型,顺序)必须不同,常用来处理实现功能类似数据类型不同的问题。在C++中不仅函数可似重载,运算符也可以重载。
函数重载规则
1.函数名称必须相同。
2.参数列表必须不同(个数不同、类型不同、参数排列顺序不司等)。
3.函数的返回类型可以相同也可以不相同。
4.仅仅返回类型不同不足以成为函数的重载。

---

## 变量的作用域

static变量在函数内定义局部变量的时候,作用域只在其函数内,但是生存周期为整个源程序。即只可以在定义该变量的函数内进行使用,但是退出该函数后,该变量一直存在,但不可以进行使用。

---



### C++存储类

存储类用于定义C++程序中变量和函数的生命周期和可见性。
寿命是指变量保持活动的时间段,可见性是指可访问变量的程序序的
模块。
有五种类型的存储类,可以在C++程序中使用
1.自动(Automatic)
2.寄存器(Register)
3.静态(Static)
4.外部(External)
5.可变(Mutable)

----

防止头文件重复包含

1.使用宏定义避免重复引入

#ifndef_NAME_H

#define _NAME_H

//头文件内容

#endif

2.使用#pragma once避免重复引入

#pragma once

//放在文件开头位置

3.用_Pragma操作符

_Pragma("once")

//放在文件开头位置

---

void指针使用
1.在C++中在任何时候都可以用void类型的指针来代替其他类型的的指针,void指针可以指向
任何数据类型的变量。
2.如果要通过void指针去获取它所指向的变量值时候,需要先将void指针强制类型转换成和
变量名类型相匹配的数据类型指针后再进行操作。
3.任何类型的指针都可以赋值给void指针,无需进行强制类型转报换。

-----

## 结构体

```c++
struct name{
    int a;
    double b;
}
```

### 共用体

```c++
union unionMame{
    int a;
}
```

### 枚举类型

只能取常量

枚举变量可以直接输出,但不能直接输入。

不能直接将常量赋给枚举变量。

不同类型的枚举变量之间不能相互赋值。

枚举变量的输入输出一般都采用switch语句将其转换为字符或字符串。

```c++
enum  color{red=1,gree,blue};
				1	2	3
```

---



## 预处理

文件包含类

#include	包含一个源代码文件,头文件

宏定义类

#define  宏名	定义宏

#undef  宏名	取消已定义的宏

条件编译类

#if	如果给定条件为真,则编译下面代码

#ifdef	如果宏已经定义,则编译下面代码

#ifndef	如果宏没有定义,则编译下面代码

#elif	如果#if条件不为真,当前条件为真,则编译下面代码

#endif	结束#if...到 #else条件编译块

#error	用于在编译期间给出一个错误信息,并终止程序的编译。同时,#error后面的错误信息不需要使用双引号。

-----

## 位运算

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-08-21 170741.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-08-21 171849.png)

----

## 面向对象

### 类

定义类使用class关键字，类名首字母一般大写

语法：

```c++
class ClassName
{
  	PropertyModifier:	//PropertyModifier属性修饰符,有public(公共)、protected(保护)、private(私有)。
    propertoys	// propertoys 属性列表。
    functions	//  functions函数列表。
};	//注意分号
```

---

### 拷贝构造函数

拷贝构造函数通常用于:

​	1.通过使用另一个同类型的对象来初始化新创建的对象。

​	2.复制对象把它作为参数传递给函数。

​	3.复制对象,并从函数返回这个对象。

### 析构函数

1.用于清理工作：释放内存，关闭打开的文件

2.没有参数，不能被重载，一个类只能有一个

----

### 动态创建对象new/delete

```c++
 int a=new int;
 delete a;
//创建对象数组
//栈上必须提供一个默认构造函数，删除要用 delete[]pstu;
//创建数组
int *p1=new char[50]
delete[] p1;

```

---

### 成员变量与成员函数

#### 成员变量

```c++
class ClassName
{
    public:
    	Type paramter;
    //成员变量的访问只能使用对象访问 instance.paramter
};
```

####  静态成员变量

1.静态成员可以实现多个对象之间的数据共享,并且使理静态数据成员还不会破坏隐藏的原则,即保证了安全性。因此,静态成员是类的所有对象中共享的成员,而不是某个对象的成员。

2.定义或说明前加static

3.可以用类名访问，也可以用对象名访问

```c++
ClassName::paramter
a1.paramter    
```

4.初始化

```c++
Type ClassName::paramter=c++;//主函数外
```

### 静态成员函数

1.静态成员函数只能访问静态成员

2.定义或说明前加static

3.可以用类名访问，也可以用对象名访问

### 常成员函数

1.可以访问类中的所有的成员变量，但不可以修改他们的值（为保护数据而设置）

2.在函数头部结尾加const

----

### this指针

1.在C++中,有一个this关键字,其是一个指针,同时也是一个const指针,它指向当前对象(也就是当前正在使用的对象),通过它可以访问当前对象的所有成员（不能在static函数中使用）

```c++
this->
```



----

### 修饰符

访问限定符：pulic,protectedd,private

1.public

可以被该类中的函数、子类的函数、其友元函数访问,也可以由该类的对象访问。

2.protected

可以被该类中的函数、子类的函数、以及其友元函数访问,但不能被该类的对象访问。

3.private

只能由该类中的函数、其友元函数访问,不能被任何其他访问,该类的对象也不能访问。

---

### 友元函数

1.类的友元函数是定义在类外部,但有权访问类的所有私有(private)成员和保护(protected)成员。尽管友元函数的原型有在类的定义中出现过,但是友元函数并不是成员函数。

2.使用friend定义

```c++
class ClassName
{
    public:
    friend void func();
};
void func()
{
    
}
```

---

### 运算符重载

运算符重载的本质为函数重载,但有一定的规则需要遵循。

1.重载运算符时,运算符的运算顺序和优先级不变,操作数个数不变。

2.不能创造新的运算符,只能重载C++中已有的运算符,并且规定有6个运算符不能重载,如表所示

3.运算符重载是针对新类型的实际需求,对原有的运算符进行适当的改造。一般来讲,重载后的运算符的功能应当与运算符的实际意义相符。

```c++
//成员函数
函数类型 operator 运算符（形参表）
{
    函数体;
}

//友元函数
friend 函数类型 operator 运算符（形参表）
{
    函数体;
}
```

-----

### 继承

1.在C++中,派生(Derive)和继承是一个概念,只是站的角度不同。继承是儿子接收父亲的产业,派生是父亲把产业传承给儿子。

```c++
//继承语法
class derived-class:access-specifier base-class
```

2.C++中的继承有三种方式,即public、protected和private,此项是可选项,如果不写,默认为private,也就是成员变量和成员函数默认也是private。

 public继承方式

​	1.基类中所有public成员在派生类中为public属性;

​	2.基类中所有 protected成员在派生类中为protected属性;

​	3.基类中所有private成员在派生类中不能使用。
​	protected继承方式

​	1.基类中的所有public成员在派生类中为protected属性;

​	2.基类中的所有 protected成员在派生类中为protected属性;

​	3.基类中的所有private成员在派生类中不能使用。

private继承方式

​	1.基类中的所有public成员在派生类中均为private属性;

​	2.基类中的所有 protected成员在派生类中均为 private属性;

​	3.基类中的所有private成员在派生类中不能使用。

3.C++中,在使用继承时,子类可以继承所有父类的非private的成员函数和成员变量,但子类不能继承父类的构造函数。

```c++
//子类调用父类构造函数
ChildClass::ChildClass(string name,int age,float score):SuperClass(name ,age),m_score(score){}
```

---

### 多继承

```c++
//语法
class D:public A,private B,protected C{
    
}
```

#### 菱形继承

在C++中,在使用多继承时,如果发生了如果类A派生出类B和类C,类D继承自类B和类C,这时候就发生了菱形继承。

#### 虚继承

在C++中,在使用多继承时,如果发生了菱形继承,那么就会出现数据冗余的问题,为了解决菱形继承出现的数据冗余的问题,C++提出了虚继承,虚继承使得派生类中只保留一份间接基类的成员。

```c++
class B: virtual public A{
    
}
```

---

### 多态

1.C++提供多态的目的是:可以通过基类指针对所有派生类(包括直接派生和间接派生)的成员变量和成员函数进行"全方位"的访问,尤其是成员函数。如果没有多态,只能访问成员变量

2.构成多态的条件

​	1.必须存在继承关系;

​	2.继承关系中必须有同名的虚函数,并且它们是覆盖关系(函数)原型相同)。

​	3.存在基类的指针,通过该指针调用虚函数。

#### 虚析构

1.在C++中,使用virtual关键字修饰的函数被称为虚函数,C++的构造函数不可以被声明为虚函数,但析构函数可以被声明为虚函数,并且有时候必须将析构声明为虚函数。

2.用C++开发的时候,用来做基类的类的析构函数一般都是虚函数。

```c++
//虚析构
virtual ~p
```

#### 虚函数表

1.在C++中,多态是由虚函数实现的,而虚函数主要是通过虚函数表(V-Table)来实现的。对象不包含虚函数表,只有虚指针,类才包含虚函数表,派生类会生成一个兼容基类的虚函数表。

2.如果一个类中包含虚函数(virtual 修饰的函数),那么这个类就会包含一张虚函数表,虚函数表存储的每一项是一个虚函数的地址。

### 纯虚函数

```c++
virtual type funcName()=0
```

#### 抽象基类

基类中添加了至少一个纯虚函数的基类称为抽象基类。

---

### 异常处理

异常提供了一种转移程序控制权

try:try块中的代码标识将被激活的特定异常。它后面通常跟着一个或多个catch块

catch:在您想要处理问题的地方,通过异常处理程序捕获异常。catch关键字用于捕获异常。

throw:当问题出现时,程序会抛出一个异常。这是通过使用throw关键字来完成的。

```c++
#include<exception>
try
{
    
}catch
{
     
}
```

---

### 嵌套类

1.外围类需要使用嵌套类对象作为底层实现,并且该嵌套类只用于外围类的实现,且同时可以对用户隐藏该底层实现。

2.从作用域的角度看,嵌套类被隐藏在外围类之中,该类名只能在外围类中使用。如果在外围类之外的作用域使用该类名时,需要加名字限定。

3.嵌套类中的成员函数可以在它的类体外定义。

4.嵌套类的成员函数对外围类的私有成员没有访问权,反之亦然。

5.嵌套类仅仅只是语法上的嵌入。

### 局部类

1.类也可以定义在函数体内,这样的类被称为局部类。局部类只在定义它的局部域内可见。

2.局部类的成员函数必须被定义在类体中。

3.局部类中不能有静态成员函数。

4.在实践中,局部类很少使用。

---

## 模板

### 函数模板

```c++
//T代表的是一种类型
//也可以用class来代替typenanme
template<typename T1,typename  T2,...,typename T3>
返回类型 函数名（参数列表）
{
    //函数体
}

//例子
template<typename T>
T daa(T &v1,T &v2)
{
    return v1+v2;
}
```

### 类模板

```c++
template<class T1,class T2,...,class Tn>
class 类模板名
{
    //类内成员声明
};
```

---

## STL(标准模板库)

1.容器(Container),是一种数据结构,如list,vector,和deques,以模板类的方法提供。为了访问容器中的数据,可以使用由容器类输出的迭代器;

2.迭代器(Iterator),提供了访问容器中对象的方法。例如,可以使用一T对迭代器指定list或vector中的一定范围的对象。迭代器就如同一个指针，事实上,C++的指针也是一种迭代器。但是,迭代器也可以是那些定义了operator*()以及其他类似于指针的操作符地方法的类对象;

3.算法(Algorithm),是用来操作容器中的数据的模板函数。例如,STU用sort()来对一个vector中的数据进行排序,用find()来搜索一个list中的对象,函数本身与他们操作的数据的结构和类型无关,因此他们可以在从简单数组到高度复杂容器的任何数据结构上使用;

----

### string容器

1.必须包含头文件<string>

```c++
#include<string>

//默认初始化,一个空字符串s1
string s1;

//s2是s1的副本
string s2(s1);

//等价于s3(s1),s3是s1的副本
string s3 = s1;

//s4是字面值"hello",
string s4("hello");

//等价于上行代码
string s5 = "hello";

//把s6初始化为由连续的n个字符c组成的串
string s6(n,'a');


```

2.string容器的输入输出

```c++
//1.cin输入
string s1;
cin>>s1;//遇到空格就终止

//2.getline读取整行
string s1;
getline(cin,s1);
```

3.string可以使用比较符号

```c++
string s="asb";
string a="abs";
if(s>a)//<,==
    
```

4.string容器的链接

```c++
string s1="abc";
string s2="def";
string s3=s1+s2;//可以用加号连接
```

5.string容器之获取字符

```c++
//1.使用C++11新特性的for
string s1 = "abcdefg";
for (auto c:s1){
cout << C <<"";
    
//2.使用运算符[] +size()函数
s1[0];
s1[1];
s1[2];
    
//3.使用迭代器
string s1 = "hello world";
for(auto i=s1.begin();i!=s1.end();i++){
cout << *i <<" ";
```

6.拷贝string对象

```c++
string s(s1,pos)
//第一个将s1从下标pos开始拷贝到结尾。当pos>s1.size()时,为未定义行为;当pos-s1.size(),拷贝一个空字符
    
string s(s1,pos,len)
//第二个将s1从下标pos开始拷贝,拷贝len个字符。当pos>s1.size()时,为未定义《行为;当pos=s2.size(),拷贝一个空字符
    
string s1("hello");
(1)strings2(s1,1);//s2为"ello",长度为4
(2)strings3(s1,5);//s3为"",长度为0
(3)strings8(s1,6);//错误,未定义的行为,抛出异常
(4)strings4(s1,1,3);//s4为"ell",长度为3
(5)strings5(s1,1,8);//正确,s5为"ello",长度为4
(6)strings6(s1,5,8);//s6为",长度为0
(7)strings7(s1,6,1);//错误,未定义的行为,抛出异常
```

7.substr函数

```c++
s.substr(pos,n)
//返回一个string对象,返回的对象包含s从pos下标开始的n个字符。pos和n均为可选参数。pos默认为下标0;n默认为s.size()-pos。
    
    
string s ("value");
(1)string s2=s.substr();//s2为"value",大小为5
(2)string s3=s.substr(2);//s3为"lue",大小为3
(3)string s4=s.substr(5);//s3为"",大小为0
(4)strings5 = s.substr(6);//错误,s5的大小为pos=5,小于s.size()
(5)string s6=s.substr(1,2);//s6为"al",大小为2
(6)string s7=s.substr(1,7);//s7为"alue",大小为4
(7)string s8=s.substr(5,7);//s8为"",大小为0
(8)strings9=s.substr(6,7);//错误,s9的大小为pos=5,小于s.size()
```

8.insert函数

```c++
1. iterator insert( iterator pos, CharT ch )
2. void insert(iterator pos, size_type count, CharT charT ch)
3. void insert( iterator pos, Inputlt first, Inputlt last )
4.插入初始化列表
    
string s1("value");
s1.insert(s1.begin(),'s');//执行后,s1为"svalue"
s1.insert(s1.begin(),1,'s');//执行后,s为"ssvalue"
s1.insert(s1.begin(),s1.begin(),++s1.begin());//执行后,s1为"sssvalue"
s1.insert(s1.end(),{'1','2'});//执行后,s1为"sssvalue12"
```

9.erase函数

```c++
basic_string & erase(size_type pos=0, size_type n=npos)
//删除s从pos下标开始的n个字符,并返回删除后的s。当pos>s.size()时报错
    
iterator erase(const_iterator position)
//删除s迭代器position位置的字符,并返回下一个字符的迭代器。
    
iterator erase(const_iterator first,const_iterator last)
//删除s迭代器[first,last)区间的字符,并返回last字符的迭代器。
    
    
string s1("value");
string s2("value");
string s3("value");
string s4("value");
s1.erase();//执行后,s1为空
s2.erase(0,2);//执行后,s2为"lue"
s3.erase(s3.begin());//执行后,s3为"alue"
s4.erase(s4.begin(),++s4.begin());//执行后,s4为"alue"
```

10.append函数

```c++
//append是在string对象的末尾进行插入操作。这一点使用+运算符也能做倒.
string s="C++";
s.append("program");//执行完后,s="C++program"
```

11.replace函数

```c++
string s("i very love China!");

const char* cp1 = "truly";
const char* cp2 = "truly!!!";

string str1 = "really";
string str2="really!!!!!!";

//1.将s从下标2开始删除4个字符,删除后在下标2处插入cp1
s.replace(2,4,cp1);//s=" i truly love China!"

//2.将s从下标2开始删除5个字符,删除后在下标2插入cp2的前5个字符
s.replace(2,5,cp2,5); //s=" i truly love China!"

//3.将s从下标2开始删除5个字符,删除后在下标2插入str1
s.replace(2, 5, str1);//s="i really love China!"

//4.将s从下标2开始删除6个字符,删除后在下标2插入str2从下标0开始的6个字符
s.replace(2, 6, str2,0,6);//s="i really love China!"

//5.将s从下标2开始删除6个字符,删除后在下标2插入4个*'字符
s.replace(2, 6, 4, '*);//s="i **** love China!"
```

---

### vector容器

1.使用必须包含头文件

```c++
#include<vector>
```

2.声明与初始化

```c++
//整形数组
vector <int> vec = { 1,2,3,4,5};
//char型数组
vector <char> vec1= {'h','e','l','l' ,'o' };
//double
vector<double> vec2 = {1.1,2.2,3.3};

//初始化
vector():创建一个空vector 
vector(int nSize):创建一个vector,元素个数为nSize
vector(int nSize,const t):创建一个vector,元素个数为nSize,且值均为0t
vector(const vector&):复制构造函数
vector(begin,end):复制[begin,end)区间内另一个数组的元素到vector中
                     
                     
vector<int>vec(10);//定义一个含10个变量的整型数组vec,默认直都为0
vector<int>vec(10,2);//定义一个含10个变量的整型数组vec,默认值都为2
vector<int>vec(a);//其中a也是vector,把a整体赋值给vec
vector<int>vec(a.begin(),a.begin+1);//把a的从开始到第二个赋值给vec
```

3.遍历函数

```c++
reference at(int pos):返回pos位置元素的引用
reference front():返回首元素的引用
reference back():返回尾元素的引用
iterator begin():返回向量头指针,指向第一个元
iterator end():返回向量尾指针,指向向量最后一个元素的下一个位置置
reverse_iterator rbegin():反向选代器,指向最后一个元素
reverse_iterator rend():反向选代器,指向第一个元素之前的位置
    
vector<int> v = { 1,2,3,4,5,6 };
cout << v.at(1)<<endl; //打印2
cout << v.front() << endl; //打印1
cout << v.back() << endl; //打印6

for (auto i = v.begin(); i != v.end(); i++) {
cout << *i<<"";
//输出123456
    
cout << endl;
for (auto i = v.rbegin(); i != v.rend(); i++) {
cout<<*i<<"";
//输出654321
```

4.增加函数

```c++
void push_back(const T& x)
//向量尾部增加一个元素X
iterator insert(iteratorit,const T& x)
//向量中迭代器指向元素前增加一个元素x
iterator insert(iterator it,int n,const T& x)
//向量中迭代器指向元素前增加n个相同的元素x
iterator insert(iterator it,const_iterator first,const_iterator last)
//向量中迭代器指向元素前插入另一个相同类型向量的[first,last)间的数据
    
vector<int> v = { 1,2 };
vector<int> v2 = { 100,200 };
v.push_back(3);//2的后面添加3
v.insert(v.begin(), 11);//在1的前面插入11
v.insert(v.begin() + 1, 3, 22);//在和11之间插入3个22
v.insert(v.begin(), v2.begin(), v2.end());//把v2的所有元素插入到v的前面

for (auto i = v.begin(); i != v.end(); i++) {
    cout<<*i<<" ";
}
```

5.删除函数

```c++
iterator erase(iteratorit):删除向量中迭代器指向元素
iterator erase(iterator first,iterator last):删除向量中[first,last)中元素
void pop_back():删除向量中最后一个元素
void clear():清空向量中所有元素
                                           vector<int> v = { 1,2,3,4,5,6 };
v.erase(v.begin()+1);//删除2
v.erase(v.begin()+2, v.begin()+4);//删除4,5
v.pop_back();//删除最后一个元素
v.clear();//清空所有元素
for (auto i = v.begin(); i != v.end(); i++) {
"cout << *i <<""";        
}                                    
```

6.判断函数

```c++
boolempty()const:判断向量是否为空,若为空,则向量中无元素
cout << v.empty() << endl;
```

7.大小函数、

```c++
intsize() const:返回向量中元素的个数
int capacity()const:返回当前向量所能容纳的最大元素值
int max_size() const:返回最大可允许的vector元素数量值
    
vector<int>v={1,2,3,4,5,6}
cout << v.size() << endl;
cout << v.capacity() << endl;
cout << v.max_size()<<endl;
```

8.其他函数

```c++
void swap(vector&)
//交换两个同类型向量的数据
void assign(int n,const T& x)
//设置向量中前n个元素的值为x
void assign(const_iterator first,const_iterator last)
//向量中[first,last)中元素设置成当前向量元素
```

---

### stack容器

1.是一种先进后出的容器，两端只有一个开口，只能从这一个开口插入和删除数据，使用是包含头文件

```c++
#include<stack>
```

2.构造函数和赋值函数

```c++
//构造函数
stack<T>stkT;//stack采用模板类实现,stack对象的默认构造形式:
stack(const stack &stk);//拷贝构造函数

//赋值函数
stack&operator=(const stack &stk);//重载等号操作符

stack<int>sl://采用默认构造函数实现
stack<int>s2(s1)://拷贝构造函数实现
stack<int>s3=s1://赋值函数
```

3.数据存取

```c++
push(elem);//向栈顶添加元素
pop();//从栈顶移除第一个元素
top();//返回栈顶元素

stack<int> s;
s.push(111);//添加元素111
s.push(222);//添加元素222
s.push(333);//添加元素333
s.pop();//弹出333
int n = s.top(); //n = 222
```

4.大小操作

```c++
empty();//判断堆栈是否为空
size();//返回堆栈的大小

```

---

### queue容器

1.是一种先进先出的数据结构，有两个出口

```c++
#include<queue>


//声明
queue<int>q1;
queue<double>q2;
```

2.性质

​	1.队列容器允许从一端新增元素,从另一端移除元素

​	2.队列中只有对头和队尾才可以被外界使用,因此队列不允许有遍历的行为

​	3.队列中进数据称为-入队push

​	4.队列中出数据称为-出队pop

3.构造函数

```c++
queue<T>queT;//queue采用模板类实现,queue对象的默认构造形式:
queue(const queue &que);//拷贝构造函数


queue<int> q1;
queue<int> q2(q1);
queue<int>q3=q1;
```

4.相关函数

```
push(elem);//往队尾添加元素 入队
pop();//从队头移除第一个元素 出队
back()/返回最后一个元素
front();//返回第一个元素
empty();//判断队列是否为空
size();//返回队列的大小
```

---

### deque容器

1.双端队列可以对头端和尾端进行插入删除操作

```
#include<deque>
```

2.构造函数

```c++
deque<T>deqT;//默认构造形式
deque(beg,end);//构造函数将[beg,end）区间中的元素拷贝给本身
deque(n,elem);//构造函数将n个elem拷贝给本身
deque(const deque &deq);//拷贝构造函数

deque<int>a;//定义一个int类型的双端队列a
deque<int> a(10);
//定义一个int类型的双端队列a,并设置初始大小为10
"deque<int> a(10, 1);"
//定义一个int类型的双端队列a,并设置初始大小为10且初始值都为1
deque<int> b(a);
//定义并用双端队列a初始化双端队列b
deque<int> b(a.begin(), a.begin()+3);
//将双端队列a中从第0个到第2个(共3个)作为双端队列b的初始值
```

3.添加函数

```c++
d.push_front(const T& x);
//头部添加元素
d.push_back(const T& x);
//末尾添加元素
d.insert(iterator it, const T& x);
//任意位置插入一个元素
d.insert(iterator it, int n, const T& x);
//任意位置插入n个相同元素
d.insert(iterator it, iterator first, iterator last);
//插入另一个向量的[forst,last]间的数据

deque<int> d1;
//头部增加元素
d1.push_front(4);
//末尾添加元素
d1.push_back(5);
//任意位置插入一个元素
deque<int>::iterator it=d1.begin();
d1.insert(it, 2);
//任意位置插入n个相同元素
it = d1.begin();
d1.insert(it, 3,9);
//插入另一个向量的[forst,last]间的数据
deque<int> d2(5, 8);
it = d1.begin();
d1.insert(it, d2.end() - 1, d2.end());
//在d1的bein()的位置插入d2的队尾元素

//遍历显示
for (it = d1.begin(); it != d1.end(); it++)
cout<<*it<<"";//输出:8999245
cout << endl;
```

4.删除函数

```c++
d1.pop_front();
//头部删除元素
d1.pop_back();
//末尾删除元素
d1.erase(iterator it);
//任意位置删除一个元素
d1.erase(iterator first, iterator last);
//删除[first,last]之间的元素
d1.clear();
//清空所有元素:

deque<int> d1;
for (inti=0;i<8;i++)
d1.push_back(i);
//头部删除元素
d1.pop_front();
//末尾删除元素
d1.pop_back();
//任意位置删除一个元素
deque<int>::iterator it = d1.begin();
d1.erase(it);
//删除[first,last]之间的元素
d1.erase(d1.begin(), d1.begin() + 1);
//遍历显示
for (it = d1.begin(); it != d1.end(); it++)
cout << *it<<" ";//输出:3456
cout << endl;
//清空所有元素
d1.clear();
//遍历显示
for (it = d1.begin(); it != d1.end(); it++)
cout<<*it<<"";
cout<<endl;
```

5.访问函数

```c++
下标访问:deq[1];//并不会检查是否越界
at方法访问:deq.at(1);
//以上两者的区别就是at会检查是否越界,是则抛出out ofrange异常
访问第一个元素:deq.front();
访问最后一个元素:deq.back();

//下标访问
cout <<d1[0] <<endl;//输出:0
//at方法访问
cout <<d1.at(0)<<endl;//输出:0
//访问第一个元素
cout << d1.front()<<endl;//输出:0
//访问最后一个元素
cout<<d1.back()<<endl;//输出:5
```

6.容量函数

```c++
d.size();
//容器大小
d.max_size();
//容器最大容量
d.resize();
//更改容器大小
deq.empty();
//容器判空
deq.shrink_to_fit();
//减少容器大小到满足元素所占存储空间的大小
```

7.其他函数

```c++
deq.assign(int nSize,constT&x);//多个元素赋值
//类似于初始化时用数组进行赋值
swap(deque&);//交换两个同类型容器的元素
```

---

### list容器

1.头文件

```c++
#include<list>
```

2.初始化

```c++
//创建一个空链表
list<int> l1;
//创建一个链表其有10个空元素
list<int> l2(10);
list<int>l3(5,20);//创建一个链表其有5个元素内容为20
list<int>14(13.begin(),l3.end());/创建一个链表其内容为13的内容
//创建一个链表其内容为14的内容
list<int> l5(l4);
```

3.迭代器

```c++
遍历元素
list<int> li ={1,2,3,4,5,6};
for(list<int>::iterator it=li.begin();it!=li.end();it++){
cout<<*it<<” “; 
    
//也可以用auto
for(autoi=lst.begin():i!=lst.end():i++) : i++) {
cout<< *i<< endl;
}
```

4.常用函数

```c++
1.
empty()
//返回一个bool类型的值,只存在真和假,当链表为空时为真,不为空时为假
if(li.empty()){ //当链表为空的时候执行
cout<<"is empty()"<<endl;
}else{
cout<<"not empty()"<<endl;

2.    
size()
//返回链表元素的个数
cout<<li.size()<<endl;   
  
3.    
push_front()//链表前插入
pop_front()//删除
push_front()//表示在链表最前端插入一个数据
pop_front()//表示在链表最前端删除一个数据。
li.push_front(111);
li.pop_front();    

4.
push_back()//链表后插入
pop_back()//删除
push_back()//表示在链表尾插入一个数据
pop_back()//表示将链表尾删除一个数据。
li.push_back(222);
li.pop_back();    
 
5.    
insert()//插入
//插入元素到指定位置,通过在元素之前在指定位置插入新元素来扩展向量,从而有效地增加容器大小所插入的元素数量。    
//插入单一数据到指定位置:
iterator insert (iterator position, const value_type& val);   
//插入一段数据到指定位置:
void insert (iterator position, size_type n, const value_type& val):    
//插入一段别的容器的数据到指定位置:
template <class Inputlterator>
void insert (iterator position, Inputlterator first, Inputlterrator last);
    
li.insert(li.begin(),100); //在链表最前端插入数据100
li.insert(li.begin(),3,200);//在链表最前端插入3个数据内容为200
list<int>k(2,50);//创建一个新的链表k,其拥有2个元素内容均为50
li.insert(li.begin(),li.begin(),li.end());//在链表上K的全部内容   
  
6.
erase(）
//删除一个元素,或者是一段区间的元素,将会自动缩减空间使用。
iterator erase(iterator position);
iterator erase(iterator first, iterator last);

li.erase(li.begin()); //删除
li.erase(li.begin(),--li.end());//删除前n-1个元素
      
7.
sort()//排序
//让整个链表变成升序状态,或者变成自定义的排序状态
template <class Compare> void sort (Compare comp);  
      
8.
reverse()
//反转函数,如果是一个升序列表,则使用该函数完成数据降序操作,类似于sort一样直接使用即可。
li.reverse();
      
9.
Lst1.assign()//给list赋值
Lst1.back()//返回最后一个元素
Lst1.begin()//返回指向第一个元素的迭代器
Lst1.clear()//删除所有元素
Lst1.empty()//如果list是空的则返回true
Lst1.end()//返回末尾的迭代器
Lst1.erase()//删除一个元素
Lst1.front()//返回第一个元素
Lst1.get_allocator()//返回list的配置器
Lst1.insert()//插入一个元素到list中
Lst1.max_size()//返回list能容纳的最大元素数量
Lst1.merge()//合并两个list
```

---

### set容器

1.set/multiset属于关联式容器,底层结构使用二叉树实现的。

set/multiset的特点是:所有的元素在插入时会自动被排序。

而set与multiset容器的区别就是:set容器中不允许有重复的元素,而multiset允许容器中有重复的元素。

```
#include<set>
```

2.构造与赋值

```c++
//默认构造函数
set<T> st
set(const set &st)//拷贝构造函数
set&operator=(const set &st)//重载等号操作符
```

3.插入与删除

```c++
insert(elem)//在容器中插入元素
clear()//清除所有元素
erase(pos)//删除pos迭代器所指的元素,返回下一个元素的迭代器
erase(beg,end)//删除区间[beg,end)的所有元素,返回下一个元素的迭代器
erase(elem)//删除容器中值为elem的元素
```

4.大小与交换

```c++
size()//返回容器中元素的数目
empty()//判断容器是否为空
swap(st)//交换两个集合容器
```

5.查找与统计

```c++
find(key)//查找key是否存在,若存在返回该键的元素的迭代器;若不存在,返回set.end()
count(key)//统计key的元素个数
```

---

### multiset容器

1.头文件

```c++
set
```

2.构造函数

```c++
//1.默认构造函数
multiset<int>ms;
//2.拷贝构造函数
multiset<int>ms2(ms1):
//3.重载=
multiset<int>ms3 = ms2:
```

3.插入与删除函数

```c++
multiset<int> ms;
//1.insert操作
ms.insert(3);

//2.删除
ms.erase(ms.begin());//删除开头
ms.erase(ms.begin(),ms.begin()+2);//删除区间
ms.erase(5);//删除某个元素
ms.clear;//清空
```

4.大小与判空与交换

```c++
multiset<int>ms={2,3,1,5,4,2,1};
//size()元素个数
cout <<"ms.size():"<<ms.size()<<endl;
//count统计元素个数
cout<<"5的个数:"ms.count(5)<<endl:
//empty是否为空
if(ms.empty()){
cout <<"ms为空"<<endl;
}
else {
		cout<<ms不为空"<<endl:
}
multiset<int>ms2={11,33,22,55,44 }:
ms. swap (ms2);
cout <<"ms:"<<endl:
printMulset(ms):
cout<<"ms2:" <<< endl;

```

5.查找

```c++
multiset<int>ms = { 3,1,4,5,4,2,1,3,4,5 } ;
int pos = 0;
for (autoi=ms.begin();i != ms.find(3);i++,pos++);
cout << pos << endl;


```

---

### map/multimap容器

1.头文件

```c++
#include<utility>
```

2.初始化与访问

```c++
pair<int,int> p;
pair<int,int> p(10,20);
//或者是
map<char,int> m;
m.insert(pair<char,int>('a',10));

//访问
pair<string,int>pl("诸葛亮",300):
cout <<"名字叫:"<<pl.first<<"年龄:"<<pl.second<<endl;

//赋值
pair<int,int>pl:
pl = make_pair(100, 200);
```

3.构造，赋值

```c++
map<int, int>m={pair<int,int>(1,10),pair<int, int>(2,20)}:
//map<int, int>m={{1,10},{2,20} }；

//map<int,int>m:
//m.insert(pair<int,int>(1,10)):
//m.insert(pair<int,int>(2,20)):

map<int,int>m2(m):
automt2 = m2.begin():
cout<<mt2->first<<" " <<mt2->second<<endl:

map<int, int>m3=m2;
automt3=m3.begin():
cout <<mt3->first<<" "<<mt3->second<<endl:
```

4.大小交换

```c++
//返回容器中元素的数目
size()
//判断容器中是否为空
empty()
//交换两个集合容器
swap(st)
```

6.插入与删除

```c++
//在容器中插入元素
insert(elem)
//清除所有元素
clear()
//删除pos迭代器所指的元素,返回下一个元素的迭代器
erase(pos)
//删除区间[beg,end)的所有元素,返回下一个元素的迭代器
erase(beg,end)
//删除容器中值为key的元素
erase(key)
    
    
map<int,int>ml
//1.通过pair
ml.insert(pair<int,int>(1,11):
printMap(ml):
//2.make_pair
ml.insert(make_pair(2,22)):
printMap(ml):
//3.map<int,int>::value_type()
ml.insert(map<int,int>::value_type(3,33)):
printMap(ml):
//4.[]
m1[6]=66://key不存在就创建，存在就是修改value
printMap (m1);
          
//删除
ml.erase(ml.begin())://通过迭代器位置
printMap(ml):
          
//根据key来删
ml.erase(2):
printMap(m1):
          
//根据迭代区间
ml.erase(ml.begin(),ml.end())://类似于ml.clear()
printMap(ml):          
```

7.查找与统计

```c++
//查找key是否存在,返回该键的元素的迭代器;若不存在返回map.end10
find(key)
//统计key的元素的个数
count(key)
    
    
map<int,int>m={{1,11},{2,22}, {3,33}}}:
//map<int,int>::iterator it=m.find(3):
auto it=m.find(3);
if(it!=m.end()){
cout<<it->first<<" "<<it->second<<endl:
}
else {
cout<<"未找到"<<endl:
}
```

---

### multimap容器

Multimap时map映射容器的一种,其拥有map的全部内容,并在此基础之上,multimap还具有了可以重复保存元素的功能,与上文的mutliset差不多一任何进行访问单个值得语句访问均只会返回第一个位置,

```c++
//默认构造函数
multimap<int, int> ml;
//赋值构造函数
multimap<int, int>m2(m1):
//重载=
multimap<int, int>m3 = m2;
//插入
multimap<int, int> m:
m.insert(make_pair(1,11)):
m.insert(make_pair(2, 22)):

//1.指定值删除
m.erase(4):
printMulmap(m):
//2.位置删除
m.erase(m.begin()):
printMulmap(m):
//区间删除
m.erase(m.begin(), m.end())://m.clear()
printMulmap(m):	

```

---

### STL常用算法

算法主要是由头文件<algorithm><functional><numeric>组成
1.<algorithm>是所有STL头文件中最大的一个,范围涉及到比较、查找、遍历操作、复
制、修改等等
2.<numeric>体积很小,只包括几个再序列上面进行简单数学运算的模板函数
3.<functional>定义了一些模板类,用以声明函数对象

#### 常用查找算法

```c++
for_each//遍历容器
    
transform//搬运容器到另一个容器中
    
find//查找元素
auto pos=find(v.begin(),v.end(),n)
    
find_if//按条件查找元素
auto pos=find_if(v.begin,v.end(),条件);    
    
adjacent_find//查找相邻重复元素
adjacent_find(v.begin(),v.end())
    
binary_search//二分查找法
bool ret=binary_search(v.begin(),v.end())
    
count//统计元素个数
int a=count(v.begin(),v.end(),n)
    
count_if//按条件统计元素个数    
int a=count_if(v.begin(),v.end(),条件)
    
```

#### 常用排序算法

```c++
sort//对容器内元素进行排序
sort(v.begin(),v.end());
    
random_shuffle//洗牌指定范围内的元素随机调整次序
fandom_shuffle(v.begin(),v.end()）
    
merge//容器（有序）元素合并,并存储到另一容器中
merge(v1.begin(),v1.end(),v2.begin(),v2.end(),v3.begin())               
               
reverse//反转指定范围的元素
reverse(v.begin(),v.end())               
               
```

#### 常用拷贝和替换算法

```c++
copy//容器内指定范围的元素拷贝到另一个容器中
copy(v1.begin(),v1.end(),v2.begin());    
    
replace//将容器内指定范围的旧元素修改为新元素
replace(v.begin(),v.end(),旧元素，新元素);    
    
replace_if//容器内指定范围满足条件的元素替换为新元素
replace_if(v.begin(),v.end(),条件，新元素)；    
    
swap//交换两个容器的元素
swap(v1,v2);
    
```

#### 常用算术生成算法

```c++
#include<numeric>

accumulate//计算容器元素累计总和
int a=accumlate(v.begin(),v.end(),初始值) 
    
fill//向容器中添加元素
fill(v.begin(),v.end(),填充值);    
```

#### 常用集合算法

```c++
set_intersection//求两个容器(有序)的交集
set_intersection(v1.begin(),v1.end(),v2.begin(),v2.end(),v3);    
    
set_union//求两个容器的并集
    
set_difference//求两个容器的差集
```

-----

### 文件操作

![image-20260906162410210](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260906162410210.png)

![image-20260906162451437](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260906162451437.png)

![image-20260906160949852](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260906160949852.png)

![image-20260906162540704](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260906162540704.png)

![image-20260906162606899](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260906162606899.png)

![image-20260906162630810](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260906162630810.png)

![image-20260906162650247](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260906162650247.png)

![image-20260906162723398](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260906162723398.png)

![image-20260906162743567](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260906162743567.png)

![image-20260906162805635](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260906162805635.png)

![image-20260906162824317](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260906162824317.png)

文件写入

```c++
#include <iostream>
#include <fstream>//1.头文件
using namespace std;
int main()
{
        //2.创建流对象
        ofstream outf;
        //3.打开文件
   		 outf.open("file.txt");//outf.open("file.txt",ios::out);
        //4.写入内容
        outf <<"山村咏怀"<<endl;
        outf <<"邵雍"<<endl;
        outf<<"一去二三里,烟村四五家。"<<endl;
        outf<<"亭台六七座,八九十枝花。"<<endl;
        //5.关闭文件
        outf.close();
}
return 0;
```

文件读取

```c++
#include <iostream>
#include <fstream>
using namespace std;
int main() {
    //charcf[100];//字符数组不能读全部内容
    stringcf;//字符串
    ifstream infile;
 	infile.open("file.txt",ios::in);//inf.open("file.txt")
    infile >> cf;
    infile.close();
    cout << cf;
    return 0;
}
```

```c++
//在c++中使用ifstream读文件的时候会以空格为分隔符,遇到空格就不读取了。这时候可以调用get函数来读取内容。
#include<iostream>
#include<fstream>//文件流的头文件
using namespace std;
int main()
{
    char a;
    ifstream infile;//定义ifstream类(输入文件流类)对象outfile
    infile.open("file.txt");//打开文件 使文件流与c++.txt文件建立关联
    while (!infile.eof())
    {
        infile.get(a);//依次获取文件中每个字符并输出
        cout << a;
    }
    //关闭文件使文件流与c++.txt文件断开关联
    infile.close();
}
```

![image-20260906163952076](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260906163952076.png)

---

#### 二进制文件读写

![image-20260908193907228](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260908193907228.png)

![image-20260908194045703](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260908194045703.png)

![image-20260908194519116](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260908194519116.png)

![image-20260908194615694](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260908194615694.png)

---

#### fstream文件操作

![image-20260908195221887](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260908195221887.png)

![image-20260908195253610](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260908195253610.png)

![image-20260908195333552](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260908195333552.png)

![image-20260908195440357](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260908195440357.png)

![image-20260908195509115](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260908195509115.png)

![image-20260908195546933](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260908195546933.png)

![image-20260908195753713](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260908195753713.png)

-----

### 基于字符串的iod操作

```c++
iostream  头文件
    
    
    
```

-----

## c++11常用新特性

### auto

1.自动推导类型

```c++
void test01(){
    vector<int>vc={1,2,3,4,51  } ;
    //for (vector<int>::iterator it= vc.begin(); it !=vc.end();it++)
    for(auto it=vc.begin();it!=vc.end();it++)
    {
        cout << *it <<endl;
    }
}
```

2.auto与const

​	1.当类型不为引用时，auto的推导结果将不保留表达式的const属性

​	2.当类型为引用时，auto的推导结果将保留表达式的const属性

3.auto的高级用法

​	还可以和某些具体类型混合使用，这样auto表示的就是“半个”类型，而不是完整的类型

4.auto的限制

​	1.auto不能在函数的参数中使用（c++14开始可以）

​	2.auto不能作用于类的非静态成员变量（就是没有stati关键字修饰的成员变量）

​	3.auto不能作用于模板参数

​	4.auto不能用于推导数组类型

---

### decltype

1.作为操作符，用于查询表达式的数据类型

```
auto num1=100;
auto num2=200;
decltype(num1+num2)mun3;
```

2.在泛型编程中,可能需要通过参数的运算来得到返回值的类型

---

### 右值引用

1.右值引用也是别名，但只能对右值引用

2.右值与左值

​	1.一般认为:左值可放在赋值符号的左边,右值可以放在赋值符号号的右边;或者能够取地址的称为左值,不能取地址的称为右值

​	2.左值也能放在赋值符号的右边,右值只能放在赋值符号的右边

---

### 移动语义

转移语义可以将资源(堆、系统对象等)从一个对象转移到另一个对象,过这样可以减少不必要的临时对象的创建、拷贝及销毁。移动语义与拷贝语义是相对时的,可以类比文件的剪切和拷贝。在现有的C++机制中,自定义的类要实现转移语义需要定义移动构造函数,还可以定义转移赋值操作符。

1. 参数(右值)的符号必须是&&
2. 参数(右值)不可以是常量,因为我们需要修改右值
3. 参数(右值)的资源链接和标记必须修改,否则,右值的析构函数就会释放资源。转移到新对象的资源也就无效了。

---

### 列表初始化

使用初始化列表时，可以用等号，也可以不用

-----

### Lambas表达式

lambda表达实际上是一个匿名类函数,在编译时会将表达式转换为匿名类函数。

```c++
[capture-list] (parameters) mutable -> return-type {statement}
[捕获列表](参数)->返回值{函数体};
```

![image-20260913160207396](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260913160207396.png)

![image-20260913160527628](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260913160527628.png)

![image-20260913160550405](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260913160550405.png)

![image-20260913162108102](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260913162108102.png)

![image-20260913162124197](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260913162124197.png)

---

### 智能指针

unique_ptr,shared_ptr,weak_ptr

智能指针在合适的时候自动释放内存

![image-20260915192233133](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260915192233133.png)

![image-20260915192429664](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260915192429664.png)

![image-20260915201945115](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260915201945115.png)

![image-20260915202151839](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260915202151839.png)

![image-20260915202259652](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260915202259652.png)

![image-20260915202620307](C:\Users\5\AppData\Roaming\Typora\typora-user-images\image-20260915202620307.png)
