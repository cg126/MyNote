# C++笔记

## 1. 基础教程

### 数据类型

**typedef 声明**

使用 **typedef** 为一个已有的类型取一个新的名字：

```C++
typedef int feet;
feet distance;
```

**枚举类型**

如果一个变量只有几种可能的值，可以定义为枚举(enumeration)类型。所谓"枚举"是指将变量的值一一列举出来，变量的值只能在列举出来的值的范围内。

```c++
enum color { red, green, blue };	// 定义枚举类型，枚举常量red=0,green=1,blue=2
color c;							// 定义变量

enum { red, green, blue } c;		// 类型与变量名同时定义，并可省略类型名
```

赋值：

```C++
c = red;		// 将枚举常量值赋给枚举变量
int i = c;		// 将枚举变量赋给整型变量，i的值为1
```



### 变量

#### 变量类型

基本变量类型：

- bool：true/false
- char
- int
- float
- double
- void
- wchar_t：宽字符类型

#### 变量作用域

三个地方可以定义变量：

- 局部变量：在**函数或一个代码块内部**声明的变量
- 全局变量：在所有**函数外部**声明的变量
- 形式参数：在**函数参数的定义中**声明的变量

### 常量

**定义常量**

两种方式：

- #define预处理器

- const 关键字

```c++
#include <iostream>
using namespace std;

#define LENGTH 10
#define WIDTH 5
#define NEWLINE '\n'
```

使用const修饰的变量不允许被修改。

```c++
int main()
{
    const int LENGTH = 10;
    const int WIDTH = 5;
    const int NEWLINE = '\n';

    // pass.
}
```

### 修饰符类型

C++ 允许在 **char、int 和 double**  数据类型前放置修饰符。

- signed

- unsigned

- long

- short

### 存储类

- auto

- register

- static

- extern

- mutable

- thread_local(C++11)

**auto存储类**

根据表达式自动推断变量类型

```C++
auto f=3.14;      //double
auto s("hello");  //const char*
auto z = new auto(9);    // int*，z是指向9的指针，z的值是变量地址，*
auto x1 = 5, x2 = 5.0, x3='r';//错误，必须是初始化为同一类型
```

**static存储类**

**static**  存储类指示编译器在程序的生命周期内保持局部变量的存在，而**不需要** 在每次它进入和离开作用域时进行**创建和销毁** 。因此，使用 static 修饰局部变量可以在函数调用之间保持局部变量的值。

static 修饰符也可以应用于全局变量。当 static 修饰全局变量时，会使变量的作用域限制在声明它的文件内。

在 C++ 中，当 static 用在类的数据成员上时，会导致仅有一个该成员的副本被类的所有对象共享。

```c++
#include <iostream>

// 函数声明 
void func(void);

static int count = 10; /* 全局变量 */

int main()
{
    while(count--)
    {
       func();
    }
    return 0;
}
// 函数定义
void func( void )
{
    static int i = 5; // 局部静态变量
    i++;
    std::cout << "变量 i 为 " << i ;
    std::cout << " , 变量 count 为 " << count << std::endl;
}
```

**extern存储类**

*extern*  是用来在**另一个文件** 中**声明** 一个全局变量或函数，extern 修饰符通常用于当有两个或多个文件共享相同的全局变量或函数的时候。

main.cpp:

```c++
#include <iostream>

int count ;
extern void write_extern();

int main()
{
   count = 5;
   write_extern();
}
```

support.cpp:

```c++
#include <iostream>

extern int count;

void write_extern(void)
{
   std::cout << "Count is " << count << std::endl;
}
```

### 运算符

**杂项运算符**

- sizeof：返回变量的大小
- condition ? X:Y：条件运算符，如果condition为真，则值为X，否则值为Y
- ,：逗号运算符，顺序执行一系列运算，整个逗号表达式的值是最后一个表达式的值
- Cast：强制转换运算符，把一种数据类型转换为另一种数据类型。例如，int(2.2000) 将返回 2。
- &：返回变量地址
- *：指向一个变量

### 数组

pass

**指向数组的指针**

Tip: 数组名是指向数组中第一个元素的常量指针。

```C++
double Array[5] = {0, 1, 2, 3, 4};
double *p;

p = Array;	// 将数组首地址赋给指针p

for (int i=0; i<5; i++)
{
    cout << *(p+i) << endl;
}

for (int i=0; i<5; i++)
{
    cout << *(Array+i) << e
}
```



### 标准库类型 vector

**vector**表示对象的集合，其中所有对象类型都相同。vector也常被称作为容器。

头文件：

```C++
#include <vector>
```

**vector对象的定义：**

```C++
// 默认初始化，创建空的vector
vector<int> veco;

const int size = 8;
const int value = 1024;
// 创建大小为8，元素初始化为0的向量
vector<int> vec1(size);

// 大小为8，元素初始化为1024
vector<int> vec2(size, value);

// 大小为4，初始化为数组的4个值
int ia[4] = {0, 1, 1, 2};
vector<int> vec3(ia, ia+4);	
// 复制向量
vector<int> vec4(vec2);		// 或者vector<int> vec4 = vec2;

// 列表初始化
vector<int> vec3{0, 1, 2, 3};	// 或者vector<int> vec3={0, 1, 2, 3};
```

**其他vector操作：**

```C++
v.empty();	// 若v不含任何元素，返回真，否则，返回假
v.size();	// 返回v中元素个数
v.push_back(t);	// 在末尾添加元素t
v[n];		// 返回第n个位置上元素的引用
v1 = v2;	// 将v2元素赋值给v1
v1 = {a, b, c};	// 用列表元素替换v1元素
```



**遍历向量——迭代器**

```C++
vector<int> vec(8);
vector<int>::iterator ter = vec.begin();    // 定义一个迭代器

// 对vec遍历赋值
for (int i = 0; iter != vec.end(); ++iter, ++i)
    *iter = i;
```

### 字符串

c++提供两种类型的字符串表示形式：

- C风格字符串
- C++引入的string类

#### **C风格字符串**

```
#include <cstring>
```

相关函数：

- strcpy(s1, s2)：复制s2到s1
- strcat(s1, s2)：连接s2到s1的末尾
- strlen(s1)：返回s1的长度
- strcmp(s1, s2)：若s1和s2相同，返回0；若s1<s2返回值小于0；若s1>s2返回值大于0
- strchr(s1, ch)：指向s1中字符ch第一次出现的位置
- strstr(s1, s2)：返回一个指针，指向s1中s2第一次出现的位置

#### **C++中的字符串**

```C++
#include <string>
```

实例：

```c++
// 传递字符串地址给函数

#include <iostream>
#include <string>
using namespace std;

void print(string *str);

int main()
{
    string greet = "hello!";

    print(&greet);

    return 0;
}

void print(string *str)
{
    cout << *str << endl;
}
```

**使用范围for语句处理每个字符**

```C++
string str("some string");

for (auto c : str)		// 通过auto关键字让编译器决定 c 的类型，此处c类型是char。
    cout << c << endl;
```



### 指针

指针是一个变量，其值是另一个变量的地址。

```C++
// 指针含义
#include <iostream>

using namespace std;

int main()
{
    int var = 20;
    int *ip = &var;

    // ip = &var;

    cout << "The value of var is: " << var << endl;
    cout << "The address stored in ip var is: " << ip <<endl;
    cout << "The value of *ip is: " << *ip << endl;

    return 0;
}
```

#### 指针数组

用于定义一个存储指针的数组

用指向字符的指针数组存储一个字符串列表

```c++
#include <iostream>

using namespace std;
const int MAX = 4;

int main ()
{
    
// 定义一个指针数组，数组的每个元素是指向一个字符串的指针，例如，names[1] = "Zara Ali"（代表字符串的首地址）
const char *names[MAX] = {
                   "Zara Ali",
                   "Hina Ali",
                   "Nuha Ali",
                   "Sara Ali",
};

for (int i = 0; i < MAX; i++)
{
    cout << "Value of names[" << i << "] = ";
    cout << names[i] << endl;
}
return 0;
}
```

#### 传递指针给函数

C++ 允许传递指针给函数，只需要简单地**声明函数参数为指针类型**，并将地址作为实参传递给函数即可。

```c++
#include <iostream>
#include <ctime>
using namespace std;

void getSeconds(unsigned long *ptr)
{
    *ptr = time(NULL);
    return;
}

int main()
{
    unsigned long sec;

    getSeconds(&sec);

    cout << "Number of seconds: " << sec << endl;

    return 0;
}
```

#### 从函数返回指针

定义一个返回值类型是指针的函数

```C++
#include <iostream>
#include <ctime>
#include <cstdlib>

using namespace std;

int *getRandom() // 函数返回值类型是指针，所以定义时要用*
{
    static int r[10];

    // 设置种子
    srand((unsigned)time(NULL));
    for (int i=0; i<10; ++i)
    {
        r[i] = rand();
        cout << r[i] << endl;
    }

    return r;
}

int main()
{
    int *p;

    p = getRandom();
    for (int i=0; i<10; i++)
    {
        cout << "*(p + " << i << ") : ";
        cout << *(p + i) << endl;
    }

    return 0;
}
```

### 引用

引用变量是某个已存在变量的另一个名字。一旦把引用初始化为某个变量，就可以使用该引用名称或变量名称来指向变量。

### 数据结构

#### struct结构体

结构是C++中另一种用户自定义的可用的数据类型，它允许存储不同类型的数据项。

**定义结构**

```c++
struct Books
{
    char title[50];
    char author[50];
    char subject[100];
    int book_id;
};
```

**结构作为函数参数**

可以把结构作为函数参数，传参方式与其他类型的变量或指针类似。

```c++
#include <iostream>
#include <cstring>
using namespace std;
void printBook( struct Books book );

// 定义一个结构体类型Books
struct Books
{
    char title[50];
    char author[50];
    char subject[100];
    int book_id;
};

int main()
{
    Books Book1;
    strcpy( Book1.title, "C++");
    strcpy( Book1.author, "Runnob" );
    strcpy(Book1.subject, "Programming");
    Book1.book_id = 123;

    printBook(Book1);

    return 0;
}

void printBook(struct Books book)
{
    cout << "Title: " << book.title << endl;
    // 以此类推
}
```

**指向结构的指针**

定义指向结构的指针，其方法与定义指向其他类型变量的指针类似。

```c++
struct Books *struct_pointer;
struct_pointer = &Book1;
```

要访问结构成员，必须使用 →运算符：

```c++
struct_pointer->title;
```

则printBook(struct Books book)可写为：

```c++
void printBook(struct Books *book)
{
    cout << "Title: " << book->title << endl;
    // 以此类推
}
```

#### union共用体（联合体）

示例：

```C++
union test
{
    char mark;
    long num;
    float score;
}
```

与结构体的区别：

​    sizeof(union test)的值为4 bytes，如果是结构体，则值为12 bytes。union中所有成员的起始地址是一样的。

---

## 2. 面向对象

### 类&对象

#### 构造函数&析构函数

**类的构造函数名称与类的名称完全相同**，且不会返回任何类型，也不返回void。构造函数可用于**为成员变量设置初始值**。

```c++
#include <iostream>
using namespace std;

class Line
{
public:
    double getLength(void);
    Line(double len); //声明构造函数
    ~Line(); //声明析构函数
private:
    double length;
};

Line::Line(double len)
{
    length = len;
}

Line::~Line(void){}
```

使用**初始化列表**初始化字段：

```c++
Line::Line(double len):length(len){}
```

#### 内联函数

用法：在函数前加上inline作为修饰符；在定义类时**定义**的函数默认是内联的。

内联是以**代码膨胀（复制）** 为代价，仅仅省去了函数调用的开销，从而提高函数的执行效率。 

#### This指针

每一个**对象**都能通过This指针访问自己的地址。

```c++
class Box
{
    ;    // pass
    int compare(Box box){
        return this->Volume() > box.Volume();    // this->Volume()意思是调用compare()的那个对象的Volume()函数
    }
}

int main()
{
    Box box1(1, 2, 3);
    Box box2(1, 1, 4);

    if (box1.compare(box2))
    {
        cout << "box2 is smaller than box1" << endl;
    }
    ;    // ...

    return 0
}
```

**扩展：**

-> 和 . 的区别：“->”用于获取**结构体指针**变量成员，“.”用于获取**结构体**变量成员。

#### 指向对象的指针

访问**指向对象的指针**的成员，需要用到成员访问运算符 -> ，就像**指向结构的指针**一样

```c++
#include <iostream>
using namespace std;

class Box
{
    public:
    ;    // pass
    double Volume()
    {
        return l * w * h;
    }
    private:
    ;    // pass
};

int main()
{
    Box box(1, 2, 3);
    Box* ptrBox;
    ptrBox = &box;
    cout << "Volume of box is: " << ptrBox->Volume() << endl;    // 访问指向对象的指针的成员

    return 0;
}
```

#### 静态成员

通过在成员的声明之前加上”static“使它与类关联在一起。静态成员被所有对象所共有。

```C++
class Account
{
public:
    void calculate(){amount += amount * interestRate;}	// 成员函数可以直接使用静态成员“interestRate”
    static double rate(){return interesRate;}
    static double rate(double);
private:
    std:string owner;
    static double interesRate;
    static double initRate();
}
```

使用作用域运算符直接访问静态成员：

```C++
double r;
r = Account::rate()
```

使用类的对象、引用或指针访问静态成员：

```C++
Account ac1;
Account *ac2 = &ac1;

r = ac1.rate();
r = ac2->rate();
```

定义静态成员：

在外部定义时，不重复使用static关键字

```C++
void Account::rate(double newRate)
{
    interestRate = newRate;
}
```

```C++
double Account::interestRate = initRate(); // initRate()已经在Account的作用域下了，所以不必再写一次“”
```



### 类的继承

例：

```C++
// 基类
class Animal
{
    // eat()函数
    // sleep()函数
};

// 派生类
class Dog ： public Animal
{
    // bark()函数
};
```



### 重载运算符和重载函数

#### **函数重载**

在同一个作用域中，可以声明几个功能相似的同名函数，要求他们的形式参数必须不同。

```C++
略
```

#### **运算符重载**

**operator**用于操作符重载（应该只有这一种用途）

**二元运算符重载**

```C++
#include <iostream>
using namespace std;

class Cor
{
public:
    Cor(int a=0, int b=0):x(a), y(b){}		// 使用初始化列表创建构造函数

    Cor operator+(const Cor& b)				// 重载“+”运算符，用于将两个对象相加，形参是引用变量
    {
        Cor c;
        c.x = this->x + b.x;
        c.y = this->y + b.y;
        return c;
    }

    int x;
    int y;
};

int main()
{
    Cor c1(1, 2);
    Cor c2(2, 2);

    c1 = c1 + c2;			// 对象c1调用 成员函数“operator+”，参数是c2

    cout << "c1 = ";
    cout << "(" << c1.x << ", " << c1.y << ")" << endl;

    return 0;
}
```



### 多态

C++调用成员函数时，根据调用函数的对象的类型来执行不同的函数。

```c++
class Shape
{
protected:
    int width, height;
public:
    Shape(int a = 0, int b = 0)
    {
        width = a;
        height = b;
    }
    virtual int area()    // 在基类中定义虚函数，以便在派生类中重写
    {
        cout << "Parent class area: " << endl;
    }
};
class Rectangle: public Shape{
public:
    Rectangle( int a=0, int b=0):Shape(a, b) { }
    int area ()
    { 
        cout << "Rectangle class area :" <<endl;
        return (width * height); 
    }
};
class Triangle: public Shape{
public:
    Triangle( int a=0, int b=0):Shape(a, b) { }
    int area ()
    { 
       cout << "Triangle class area :" <<endl;
       return (width * height / 2); 
    }
};
```

**纯虚函数**

可以把基类中的虚函数area()改写如下：

```c++
class Shape{
    // 省略
    virtual int area() = 0;        // 纯虚函数
}
```

= 0告诉编译器，函数没有主体。

### 数据封装

所有的C++程序都有一下两个基本要素：

- 程序语句：执行动作的部分，即函数

- 程序数据

数据封装是把数据和操作数据的函数捆绑在一起的机制。

设计策略：
<br />通常情况下，我们会设置成员状态为私有（private），以保证良好的封装性。这通常应用于数据成员，也同样适用于所有成员，包括虚函数。

## 3. C++高级教程

### C++文件和流

C++另一个标准库fstream

```c++
#include <iostream>        // 标准输入输出
#include <fstream>        // 文件操作
```

定义了三个新的数据类型：

- ofstream: 输出文件流，用于创建文件并写入信息

- ifstream: 输入文件流，读取信息

- fstream: 文件流，同时具有以上两种功能

**1. 打开文件** 

```c++
void open(const char *filename, ios::openmode mode);
```

其中第一个参数指定要打开文件的名字和路径，第二个参数指定文件被打开的模式。

- ios::app: 追加模式。所有写入追加到文件末尾

- ios::ate: 文件打开后定位到文件末尾

- ios::in: 打开文件用于读取

- ios::out: 打开文件用于写入

- ios::trunc: 如果该文件已经存在，其内容将在打开文件之前被截断，即把文件长度设为 0。

```c++
ofstream outfile;
outfile.open("file.dat", ios::out | ios::trunc);  // ios::trunc表示把文件截断，即新文件覆盖旧文件
```

**2. 读取&写入** 

写入（输出）

```C++
#include <fstream>
#include <iostream>
using namespace std;

int main()
{
    ofstream ofs;
    ofs.open("test.txt", ios::out);
    ofs << "hello" << endl;
    ofs << "there" << endl;
    ofs.close();

    return 0;
}
```

读入（输入）

```C++
#include <iostream>
#include <fstream>
using namespace std;

int main()
{
    ifstream ifs;
    ifs.open("test.txt");
    string words;
    ifs >> words;
    cout << words << endl;
    ifs >> words;
    cout << words << endl;
    ifs.close();

    return 0;
}
```

**3. 关闭文件** 

在程序终止前，应该关闭所有的文件。

close()是fstream、ifstream、ofstream对象的一个成员函数。

```c++
void close();
```

### 动态内存

使用特殊运算符在程序运行时**动态分配内存**

**new:**

分配特定类型的对象：

```C++
int *print = new int(1024);        // 分配了一个没有名字的值为1024的对象，唯一的访问方式是通过指针间接访问
```

分配特定类型和维数的数组：

```C++
int *pia = new int[4];        // 分配了一个含有4个整数元素的数组
```

**delete: **

```C++
delete pint;        // 删除单个对象

delete [] pia;        // 删除一个对象数组
```

用`new`分配动态内存后一定要用`delete`释放，否则会造成内存泄漏

### 命名空间

引入命名空间这个概念，用以区分不同库中相同名称的函数、类、变量等。本质上，命名空间就是定义了一个范围。

**定一个命名空间**

```c++
namespace namespace_name {
    // 声明代码
}
```

调用命名空间的函数或变量：

```c++
name::code;        // code是变量或函数，name是类？
```

**using指令**

```c++
using namespace std;    // std是一个命名空间名字
```

使用命名空间的特定项目：

```c++
using std::cout;    // 在随后代码中使用cout可以不加上std作为前缀
```

### 模板

模板是C++支持 **参数化多态** 的工具，使用模板可以使用户为类或者函数声明一种一般模式，使得类中的某些数据成员或者成员函数的参数、返回值取得任意类型。

模板是**泛型编程** 的基础，泛型编程即以一种独立于任何特定类型的方式编写代码。

#### **函数模板**

函数模板针对 **参数** 或者 **返回值** 类型不同的函数。

实例：

```c++
#include <iostream>
#include <string>
using namespace std;

template <typename T>

// 定义Max函数；inline是内联函数修饰符
inline T const& Max (T const& a, T const& b)
{
    return a < b ? b:a;        // 若a<b，则返回b，否则返回a
}

int main()
{
    int i = 39;
    int j = 20;
    cout << "Max(i, j): " << Max(i, j) << endl;

    double f1 = 13.5;
    double f2 = 20.7;
    cout << "Max(f1, f2): " << Max(f1, f2) << endl;

    string s1 = "Hello";
    string s2 = "World";
    cout << "Max(s1, s2): " << Max(s1, s2) << endl;

    return 0;
}
```

**函数重载与函数模板的区别：**

1. 区别
   1. 作用：函数重载用于定义功能相似的同名函数，提高函数的易用性；函数模板则用于为实现逻辑一样只是参数类型不同的一类函数提供统一的模板，提高函数编写的效率
   2. 形参列表：函数重载要求重载的函数的参数个数或类型不同；函数模板要求参数个数必须一样

#### **类模板**

类模板针对仅**数据成员**和**成员函数** 类型不同的类。



### C++预处理器

预处理指令：#include、#define、#if、#else、#line等

**#define**

此预处理指令用于创建符号常量。该符号常量通常称为宏

```C++
#define PI 3.14159
```

**参数宏**

```C++
#define MIN(a,b) (a<b ? a:b)

int main()
{
    int i, j;
    ;    // pass
    cout << "最小值为" << MIN(i, j) << endl;

    return 0;
}
```

**条件编译**

```C++
#ifdef DEBUG
    cout << "Trace: Inside main function" << endl;    // 如果在此前定义了符号常量DEBUG，则执行此句
#endif

#if 0
    // 此部分将被注释
#endif
```

**预定义宏**

- \__LINE__：当前行号
- \__FILE__：当前文件名
- \__DATE__：形式为 month/day/year 的字符串，源文件转换为目标代码的日期
- \__TIME__：形式为 hour:minute:second 的字符串，被编译的时间

## 4. C++资源库

### STL（标准模板库）教程

STL（标准模板库）是一个功能强大的C++模板类，提供了通用的模板类和函数，可以实现向量、链表、队列、栈等数据结构和算法。

STL有六大组件，前三个是核心组件：

- 容器：用来管理某一类对象的集合；C++提供各种不同类型的容器，deque、list、vector、map等
- 算法：各种常用的算法
- 迭代器：作用，遍历对象集合的元素
- 仿函数
- 适配器（配接器）
- 空间配置器

### C++标准库

#### 常用容器

## 5. 其他

### 头文件

头文件应该写什么？

头文件只放变量和函数的声明，而不能放定义。但是有三个例外：

- const对象的定义，static对象的定义

- 内联函数（inline）的定义

- 类（class）的定义

头文件的保护措施：

使用"#define"配合条件编译。通过#define定义一个名字，

并通过条件编译：

```C++
#ifndef HEADER_FILE
#endif HEADER_FILE

/the entire header file file

#endif
```

### 顺序容器

- vector: 可变大小数组
- deque: 双端队列
- list: 双向链表
- forward_list: 单向链表
- array: 固定大小数组
- string: 与vector相似，用于保存字符

string和vector的元素是连续存储的。由元素下标计算地址非常快。但是在容器的中间添加或删除元素很慢。

list和forward_list在容器任何位置的添加和删除操作都很快。但不支持随机访问。

deque支持快速随机访问。在deque两端添加或删除元素时很快，与list和forward_list速度相当。

**向量容器**

```C++
std::vector<type> vectorName;
```

示例：

```c++
#include <iostream>
#include <string>
#include <vector>

using namespace std;

int main()
{
    vector<string> names;
    names.push_back("小甲鱼");
    names.push_back("小由鱼");

    for (int i=0; i<names.size(); i++)
    {
        cout << names[i] << endl;
    }

    return 0;
}
```

**迭代器**

对容器里的元素进行遍历

```c++
std::vector<type>::iterator iteratorName;
```

```c++
#include <iostream>
#include <string>
#include <vector>

using namespace std;

int main()
{
    vector<string> names;
    names.push_back("xiaojiayu");
    names.push_back("xiaoyouyu");

    // for (int i=0; i<names.size(); i++)
    // {
    //     cout << names[i] << endl;
    // }

    vector<string>::iterator iter = names.begin();

    while (iter != names.end())
    {
        cout << *iter << endl;
        iter++;
    }

    return 0;
}
```
