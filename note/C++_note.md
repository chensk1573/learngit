# Hello World

```c++
#include <iostream>

int main()
{
    std::cout << "Hello World!" << std::endl;
    return 0;
}
```

# 数据类型

## 简单变量

### 整型

```
type       length                       max
bool       1bit                         true, false，数字、指针可以隐式转换为bool
char       8bit                         127 or 255(有无符号取决于编译器)
short      >= 16bit                     32767
int        depend on system             2.15e9 (32bit)
long       >= min(32, 8*sizeof(int))    2.15e9 (32bit)
long long  >= min(64, 8*sizeof(long))   9.22e18 (64bit)

unsigned int等无符号类型不能存储负数，最大值翻倍。当unsigned溢出时，会去到另一头，有符号类型溢出时不保证
通常情况都用int，超过20亿用long long，存储小数字且必须节省内存时使用short和char

不同进制的字面值表示
0xAA(hex), 071(oct)

C++11新增的类型
char16_t  16bit  例：u"c16"
char32_t  32bit  例：U"c32"
```

### 浮点数

```
float
double
long double
```

## 复合类型

### 数组

```c++
const int SIZE = 4;
int iL[SIZE] = {1, 2, 4, 8};
float fL[] = {0.1, 0.2, 0.3};
```

### 字符串

```c++
char s[8] = {'e', 'n', 'd', '\0'};
char t[] = "string literal";
//'c'表示它的值，"c"表示字符串所在地址
```

### string类

看上去应该是一个给人用的字符串类型

### 结构体(structure)

```c++
//声明(declare)结构体
struct category {
    char name[20];
    float price;
};

//定义(define)&初始化结构体变量
category c1 = {"stuffed bear", 10.5};
cout << "you can have " << c1.name;
cout << "for $" << c1.price << endl;

//结构体变量成员赋值(memberwise assignment)
category c2 = c1;

//结构体指针的箭头成员运算符
category* c = new category;
c->name = "beer";
(*c).price = 3.5;
```

### 共用体(union)

```c++
//共用体占用空间是其最大成员的长度，在任意时刻只能存储其中一个成员
//当数据可能轮流使用多种数据格式时能够节省空间
union one_for_all{
    int int_val;
    double double_val;
};

one_for_all pail;
pail.int_val = 15;
cout << pail.int_val;     //15
pail.double_val = 10.5;
cout << pail.double_val;  //此时输出int_val的值不确定

//匿名共用体
struct widget{
    char brand[20];
    int type;
    union{
        long id_num;
        char id_char[20];
    };
};
//嵌套在widget中间的共用体匿名，id_num与id_char成为widget的成员
//它们占用相同的地址，每刻只有其中一个作为成员
```

### 枚举(enumeration)

```c++
enum weekdays {Mon, Tue, Wed, Thu, Fri, Sat, Sum};
enum {zero, null=0, one, uno=1};
//Mon, null等值成为符号常量，称枚举量(enumerator)
//不设置值，枚举量的取值为0，1，……；设置的值必须是整数或者枚举量
//枚举量可以隐式地转化为int
//可以定义枚举类的变量并为它赋一个枚举量，但是一般都不这么做
```

### 指针(pointer)

```c++
//声明与初始化
int* p, i;    //p为指针，i为整数
p = &i;       //取地址运算符&
*p = 5;       //间接寻址运算符*

//指针与内存管理
int *p = new int;  //常规定义的变量在栈中，而使用new定义的内存在堆中
delete p;
    //释放p指向的内存，不删除p，尝试释放一块不是new来的内存后果不确定
    //释放空指针是安全的，释放栈空间中的地址会引发错误
int *p_arr = new int[10];  //使用new生成动态数组(dynamic array)
delete [] p;               //new和delete时的方括号如果不匹配，行为无定义

//指针算数(pointer arithmetic)
int *p = new int[10];
p_arr[3] = 3;
cout << *(p_arr+3);  //3

/*
数组和指针的区别：
1.数组值不能改，指针作为变量可以随意更改
2.sizeof(int*) = 指针的长度，sizeof(int[10]) = 10*sizeof(int)
*/

//C++的内存处理简介
//自动存储：函数内部定义的变量使用自动存储空间（栈空间），称自动变量(automatic variable)，是一个局部变量，作用域是包含它的代码块（即大括号），执行代码块时变量以此被放进栈空间，代码块结束时被依次释放
//静态存储：在函数外定义的变量/用关键词static声明的变量，整个程序执行期间存在
//动态变量：使用new分配的空间，在自由存储空间(free store)，或称堆空间，生存期可以随意控制
```

# 函数

## 基本知识

```c++
//函数原型(prototype)，作用是声明函数，并允许编译器进行静态类型检查(static type checking)
int func(int, double);

int main(){
    //调用函数
    //调用时给的输入称作实参(argument)，它被赋值给函数内部的形参(parameter)
    //parameter是函数私有的局部变量，函数结束时被释放
    func(0, 1.5);
}

int func(int a, double b){
    //do something
    return 0;
}
```

## 函数重载(overload)

函数的参数列表（类型、数量、顺序，const、引用变量和返回值类型不算）称作函数的特征标(function signature)，C++允许定义名称相同而特征标不同的函数，称作多态/函数重载，调用时会选择匹配的一个来用。如果有多个完全匹配/可以进行隐式转换的，编译器会通过重载解析(overloading resolution)找出最匹配的，如果找不出一个最匹配的，可能会按照某种规则选一个，或者警告ambiguous

## 函数模板(template)

可以使用模板定义一个泛型的函数，调用时可以用任意类型的参数。在编译时，编译器根据实参类型产生合适的不同版本的函数，称为隐式实例化(implicit instantiation)

```c++
//声明
template <typename T1, typename T2>
void Func(T1, T2, int);

//显式实例化(explicit instantiation)
template <> void Func(int, int *, int);

//也可以在调用的时候显式实例化，如
Func<double>(1.3, pt, 1);

//定义
template <typename T1, template T2>  //or class T, T could be any valid name
void Func(T1 a, T2 b, int n)
{
    //do something
}

//模板的重载
template <typename T>
void Func(T a[], T b[], int len)
{
    //do something
}

//具体化(specialization)
//以结构体job为例的显式具体化例子
template <> void Func<job>(job &j1, job &j2)
{
    //do something
}
/*
对同一个函数名，可以有非模板函数、显式具体化模板函数、常规模板以及它们的重载版本，三者优先级递减
隐式实例化、显式实例化和显式具体化统称具体化
*/
```

c++11新增的特性

```c++
template <class T1, class T2>
auto Func(T1 x, T2 y) -> decltype(x+y)
{
    decltype(x+y) sum;  //decltype(x+y)即x+y的类型
    sum = x + y;
    return sum;
}
```

## 杂项

### 内联函数(inline function)

程序运行期间顺序执行指令（储存在内存中的机器指令）。常规函数调用时，先跳到该函数的地址，执行完毕后再跳回原来的地址；使用内联函数，则在编译时直接在调用的位置后面加上这个函数的指令，于是运行时就不用跳来跳去了，可以节省一点时间，但代价是每处调用都要一份副本，增加了内存消耗

效果类似C语言用宏来计算，但是比宏安全得多。

只有被调函数执行时间很短、被调用次数很多时才有较高使用价值。习惯上把内联函数直接定义在最开头一行写完。需要多行的函数一般也不适合做内联函数

```c++
inline double square(double x){return x*x;}
```

### 引用变量(reference argument)

```c++
int a;
int & b = a;   //int &是复合类型，是指向int的引用，这样定义的b成为a的别名
//引用变量必须在声明的同时初始化，且关联之后不能更改

//引用传参
void f(int &i)
{
    x = 1;
    /*
    不同于按值传递，被调用函数内的变量和进行调用处的变量是同一个东西
    它最大的价值在于引用结构和类
    相比于按值传递，它不需要复制，可以节省时间和内存
    相比于用指针传参，它更加方便
    引用传参的实参必须是一个变量，不能是表达式或常量
    */
}

//使用const的引用传参
void f(const double & a)
{
    std::cout << a;
    /*
    如果希望参数值不被改变，最好使用const限定词
    a的值在函数内部不能被修改，否则会报错（一些老的编译器上会出现一些奇怪结果）
    如果实参不是左值/需要隐式转换，会为它创建一个临时变量
    有const才会有临时变量，因为没有const时，如果创建了临时变量，可能我以为我改了
    某个变量而实际上时临时变量被改了，原变量没动；有const时，无论如何我都不能改原
    变量，所以就无所谓了
    */
}
```

### 默认参数

```c++
int func(i, j=0, k=0)
{
    //do something
}
//类似python，必须是先有无默认值的、再是有默认值的
//不同的是，调用的时候参数必须从左向右填，而且不允许跳过，比如func(1, , 2)是非法的
```

# 内存模型与名称空间

## 内存模型

1. 堆空间：在函数内定义的自动变量，作用域是所在的代码块，进入代码块时push，离开代码块时pop

2. 栈空间：从new开始存续到delete为止

3. 静态存储空间：函数外定义的变量以及用static定义的变量，整个程序运行过程中存在
4. 线程持续性存储空间：生命周期和所属线程一样长，用thread_local声明

## 名称空间(namespace)

```c++
//定义
namespace name
{
    int a;
    struct foo(int a);
    int zero() {return 0;};
    namespace nested {int a;}   //名称空间嵌套
    using std::cout;            //在名称空间内使用using声明
    using namespace std;        //在名称空间内使用using编译指令
}

namespace name {double x;}      //名称空间是开放的，可随时添加东西
namespace N = name;             //别名
namespace {double time;}        //unnamed namespace，不能被外部调用，可以充当内部变量

//使用
name::nested::a;    //用解析运算符访问名称空间中的标识符
using name::zero;
zero();             //用using声明访问名称空间中指定标识符
using namespace name;
a;                  //用using指令访问名称空间内全部标识符

/*
使用using声明往往比using指令更加安全，因为使用using声明时，编译器会自动检查有没有冲突的名称，
而使用using指令时编译器不会（往往也难以）检查
*/
```

### 使用名称空间的一些原则

名称空间的最大价值在大型项目的管理，做项目时应该遵守这些规则，而一个文件的简单程序可以随意一点。但如果考虑以后的代码复用，即使是简单的项目，遵循这些原则也有好处

1. 把全局变量都装进名称空间
2. 把函数库装进名称空间
3. 不要用全局的using指令/声明，尤其不要把using放进头文件
4. using声明优于using指令

## 单独编译

C++允许将源代码分散在多个文件中，分别编译后连接起来，其好处是结构清晰管理方便，并且方便代码复用。一种典型的分割方法是：

* 头文件：写结构体声明和函数原型，在需要使用这些的文件头都include头文件，这样，修改函数时只需要修改头文件中的原型。不要包含函数的定义，因为如果头文件被include多次，重复定义会引起错误
* 结构：函数的代码
* 调用：调用函数的代码

# 类(class)

## 基本知识

```c++
//声明
class Stock   //习惯上类的首字母大写
{
private:      //私有成员，编译器只允许从成员函数访问私有成员，属于数据保护封装。可以省略private
    std::string company;
    int number;
    double price;
    double total_val;
public:       //公有成员
    Stock(const std::string& co = "ERROR", long n = 0, double price_ = 0.0);
    ~Stock();
    void acquire(const std::string &co, long n, double price);
    void buy(long num, double price);
    void sell(long num, double price);
    void show() const;   //const表示方法不会修改对象，const对象只能使用const的方法
    double total() const {return total_val;} //内联方法，直接声明的同时定义
};

//构造函数(constructor)
Stock::Stock(const std::string & co, long n, double price_)
{
    /*
    initialize，相当于python的__init__
    构造函数没有返回值类型，名称与类名相同。如果没有构造函数，类不会被初始化
    提供构造函数时必须同时提供一个不带参数的默认构造函数（默认值or重载）
    当没有（显示或者隐式地）调用构造函数时，会使用默认构造函数，
    形参名避免和成员名相同，可以前缀m_或者后缀下划线
    */
}

//析构函数(destructor)
Stock::~Stock()
{
    /*
    对象过期时自动调用析构函数（过期判定与编译器有关），一般不会主动调用它
    常用来delete掉构造函数里面new出来的内存，可以不显式提供析构函数
    */
}

//定义成员函数（方法）
void Stock::buy(long num, double price)
{
    //do something
    //Stock::buy是函数限定名(qualified name)，而buy是非限定名，只能在类作用域中使用
}

//使用类
Stock mystock("NanoSmart", 20, 12.5);
Stock *ptstock = new Stock("TI", 18, 19.0);
mystock.buy(100, 12.2);
```

## this指针

```c++
const Stock & Stock::topval(const Stock & s) const
{
    //this指针指向自身，相当于指针版本的python的self（不过是固定名字的）
    if (that.totalval > totalval) return s;
    else return *this;  //this指向自己，*this就是自己
}
```

## 运算符重载

```c++
//假设重载+运算符实现时间类Time（时分秒）的运算

//类成员的运算符重载（需要加进类声明的public！）
Time Time::operator+(double m) const
{
    Time result;
    //运算
    return result;
}

//非成员重载运算符
Time operator*(double m, const Time & t)
{
    return t * m;
}

/*
重载之后，t * m将会被理解为t1.operator*(m)，m * t将被理解为operator*(m, t)
复合运算时照一般的结合律来，如t1+t2+t3 = t1.operator+(t2+t3) = ...
重载要求至少一个操作数是自定义类型，并且不能改变原本的语法规则（比如不能把双目运算符重载成单目的）
而且不能修改优先级，不能创建运算符，有的运算符不能重载
*/
```

## 友元(friend)

包括友元函数、友元类、友元成员函数，友元有和类成员函数同等的访问权限（即可以访问private成员），声明方式是在类声明中加入

```c++
friend /*声明*/;
```

### 重载<<运算符

std::cout是一个ostream对象，ostream已经重载了<<，ostream << 基本类型变量都能得到输出数据流，因此通过重载<<把自定义的类转换成基本类型，就能实现自定义类的输出。又因为一般想把cout作为左操作数，常使用友元函数来重载

```c++
class Time
{
    //略
    friend ostream& operator<<(std::ostream& os, const time& t);
}

ostream& operator<<(std::ostream& os, const time& t)
{
    /*
    注意！这个函数返回了一个引用，这个引用是它引用传参引过来的东西
    如果返回一个局部变量引用，则退出函数后局部变量消失，引用也就无效了
    */
    os << t.hours << ':' << t.minutes;
    return os;
}
```

## 类型转换

# 动态内存分配