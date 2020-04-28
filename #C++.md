# C++ 笔记

# 1.创建test1.cpp



~~~ C++
#include <stdio.h>
#include <iostream>
using namespace std;

int main(){
    cout << "111";
    return 0;
}

~~~

 

# 2.编译文件

` D:\c++\test> g++ test1.cpp -o test.exe   `

> 其中： -o ：表示编译生成目标文件

 `执行文件: .\test.exe`

![1581080917001](C:\Users\haoguangsu\Pictures\Camera Roll\1581080917001.png) 



# 3.问题

> > windows下使用MinGw编译出现中文乱码问题解决:
> >
> > 通过给g++编译器加两个参数即可
> >
> > > -fexec-charset=GBK
> > > -finput-charset=UTF-8
> >
> > ### 如果你是直接编译
> >
> > > g++ -fexec-charset=GBK test.cpp -o test



# 4.cin.get()

~~~C++
#include <cstring>
#include <iostream>
using namespace std;

int main()
{
    const int ArSize = 5;
    char name[ArSize];
    char food[ArSize];
    // getline
    // cout << "Please input your name:" << endl;
    // cin.getline(name, ArSize);
    // cout << "Please input your favorite food:" << endl;
    // cin.getline(food, ArSize);
    // cout << "Hello " << name << ", your favorite food is" << food << ".";

    //get
    // cout << "Please input your name:" << endl;
    // cin.get(name, ArSize).get();
    // cout << "Please input your favorite food:" << endl;
    // cin.get(food, ArSize).get();

    // cout << "Hello " << name << ", your favorite food is" << food << ".";

    //混合输入字符串和数字
    cout << "What year was your house built?" << endl;
    int year;
    cin >> year;
    cout << "What is its street address?" << endl;
    cin.get();   // 在读取下一行输入之前，先把上一行输入的换行符给读取并丢弃掉！！
    char address[60];
    cin.get(address, 60).get();
    cout << "Year built :" << year << endl;
    cout << "Address: " << address << endl;
    cout << "Done!" << endl;
    return 0;
}
~~~



# 5.c++内存被分为5个区

### 栈区：

> 我们在代码中声明的局部变量，以及函数的形式参数，都保留在栈区中，这部分内存在程序运行的时候会自动分配，而在不需要的时候也会自动释放，并不需要去手动维护

### 堆区：

> 我们使用new或者malloc函数进行动态内存分配后，系统为我们划分的内存空间，就来自于堆区，因为程序自己不知道这些动态内存到什么时候会派不上用场，所以程序并不会对这部分分配的内存做任何处理。

### 全局区/静态区

### 常量区

### 自由存储区





# 6.C++用delete释放对象之后，为什么还可以继续使用该对象

~~~c++
   int a, *p;
   p = new int;
   a = 5;
   *p = a;
   delete p;
   cout << *p << endl;
//输出结果：5
//即使delete掉p之后，仍然可以使用
~~~



> delete只是释放堆中的空间，并没有将p指针的值改为NULL，所以p仍指向堆中的相应位置（但此位置已被收回可以随时被分配给别的对象，而被更改为其他值）



# 7.指针和数组

~~~C++
double wages[3] = {1000.0, 2000.0, 3000.0};
double *pw = wages;
// pw = 0x28ccf0 , *pw = 1000
pw = pw + 1;
// pw = 0x28ccf8 , *pw = 2000

~~~

> 在多数情况下，C++将数组名解释为数组第一个元素的地址，所以上面的语句，将pw声明为指向double类型的指针，然后将它初始化为wages——wages数组中第一个元素的地址。

> wages = &wages[0] = 数组第一个元素的地址。

> 将指针变量加1后，其增加的值等于指向的类型占用的字节数。

> C++将数组名解释为地址，解释为数组第一个元素的地址。

> 在cout和多数C++表达式中，char数组名、char指针以及用引号括起来的字符串常量都被解释为字符串第一个字符的地址。

# 8.cout如何打印boolean为true或false

~~~C++
cout.setf(ios_base::boolalpha);
cout << (1 > 2);
~~~



# 9.虚函数和纯虚函数

为了让基类指针能够访问派生类的成员函数，[C++](http://c.biancheng.net/cplus/) 增加了**虚函数（Virtual Function）**。使用虚函数非常简单，只需要在函数声明前面增加 virtual 关键字。

~~~C++
#include <iostream>
using namespace std;
//基类People
class People{
public:
    People(char *name, int age);
    virtual void display();  //声明为虚函数
protected:
    char *m_name;
    int m_age;
};
People::People(char *name, int age): m_name(name), m_age(age){}
void People::display(){
    cout<<m_name<<"今年"<<m_age<<"岁了，是个无业游民。"<<endl;
}
//派生类Teacher
class Teacher: public People{
public:
    Teacher(char *name, int age, int salary);
    virtual void display();  //声明为虚函数
private:
    int m_salary;
};
Teacher::Teacher(char *name, int age, int salary): People(name, age), m_salary(salary){}
void Teacher::display(){
    cout<<m_name<<"今年"<<m_age<<"岁了，是一名教师，每月有"<<m_salary<<"元的收入。"<<endl;
}
int main(){
    People *p = new People("王志刚", 23);
    p -> display();
    p = new Teacher("赵宏佳", 45, 8200);
    p -> display();
    return 0;
}
运行结果：
王志刚今年23岁了，是个无业游民。
赵宏佳今年45岁了，是一名教师，每月有8200元的收入。

~~~

有了虚函数，基类指针指向基类对象时就使用基类的成员（包括成员函数和成员变量），指向派生类对象时就使用派生类的成员。换句话说，基类指针可以按照基类的方式来做事，也可以按照派生类的方式来做事，它有多种形态，或者说有多种表现方式，我们将这种现象称为**多态（Polymorphism）**。多态是面向对象编程的主要特征之一，C++中虚函数的唯一用处就是构成多态。

> C++提供多态的目的是：可以通过基类指针对所有派生类（包括直接派生和间接派生）的成员变量和成员函数进行“全方位”的访问，尤其是成员函数。如果没有多态，我们只能访问成员变量。 



### 纯虚函数：

纯虚函数是在基类中声明的虚函数，它在基类中没有定义，但要求任何派生类都要定义自己的实现方法。在基类中实现纯虚函数的方法是在函数原型后加"=0"，例如：

~~~C++ 
virtual void func() =0；
~~~



声明了纯虚函数的类是一个抽象类。所以，用户不能创建类的实例，只能创建它的派生类的实例。所以类纯虚函数的声明就是在告诉子类的设计者，“你必须提供一个纯虚函数的实现，但我不知道你会怎样实现它”。 



> 虚函数是为了构成多态；纯虚函数就是作为接口而存在， 纯虚函数不具备函数的功能，一般不能直接被调用。



从基类继承来的纯虚函数，在派生类中仍是虚函数。如果一个类中至少有一个纯虚函数，那么这个类被称为抽象类（abstract class）。

抽象类中不仅包括纯虚函数，也可包括虚函数。抽象类必须用作派生其他类的基类，而不能用于直接创建对象实例。但仍可使用指向抽象类的指针支持运行时多态性。 

# 10.& 和 && 的区别：

>   1）& 表示“按位与"，这里的”位“是指二进制位（bit）。
> 例：十进制数字8 转化为二进制是：1000  ；数字9 转化为二进制是1001 。
> 则如有以下程序：
> public class Test {
>     public static void main(String[]args) {
>        System.out.println(9 & 8);
>     }
> }
> 输出结果应该是：8
> 原因：1001 & 1000 =  1000  。 计算机中一般1表示真，0表示假。最左边一位1&1=1，最右边一位1&0 = 0.
>
> 2) && 表示逻辑”与“ ，即java中的boolean值才可以存在于&&符号的左右两侧。
> true  &&  false =  false  ，true  && true  =  true， 依旧是"一假必假"。
> 值的注意的是：&& 符号有所谓的“短路原则”，当 A && B 出现时，如果A经判断是假，那么B表达式将不会获得执行或被判断的机会。直接结果就为假。  



~~~C++
#include <iostream>
using namespace std;
int func(int x)
{
    int countx = 0;
    while (x)
    {
        countx++;
        x = x & (x - 1);
        cout << "countx = " << countx << endl;
        cout << "x = " << x << endl;
    }
    return countx;
}
int main()
{
    int x;
    cin >> x;

    cout << func(x);
    return 1;
}


//func函数的作用:将x转化为2进制，看含有的1的个数
//假定x = 9999。 答案：8
~~~



# 11.自动存储/静态存储/动态存储

## 1.自动存储

自动变量，自动存储实际上就是局部变量，其作用域在代码块里，也就是“栈”，“后进先出（LIFO）”

## 2.静态存储

使变量成为静态存储有两种方式，一种是在代码块外定义，一种是使用关键字static：

~~~ C++
static int a = 1;		
~~~

## 3.动态存储

由new和delete运算符提供的一种比自动存储和静态存储更灵活的方法，它管理一个内存池，也就是“堆”。





# 12.struct的使用

~~~ c++
#include <iostream>
using namespace std;
int main()
{
    struct test6
    {
        int a;
        char b;
    };
    //使用成员运算符访问其成员
    test6 t1;
    t1.a = 1;
    t1.b = 'b';
    cout << t1.a << "  " << t1.b << endl;

    //可创建指向这种结构的指针，使用间接成员运算符访问成员
    test6 t2;
    test6 *t3 = &t2;
    t3->a = 1;
    t3->b = 'b';
    cout << t3->a << "  " << t3->b << endl;
    return 0;
}
~~~



# 13.C++标准版本

C++真正正式公布的标准就三个：C++98、C++03、[C++11](https://www.baidu.com/s?wd=C%2B%2B11&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)。

> 至于什么C89、C99这些，是C语言的标准，不是C++的标准。



# 14.C++数组

~~~ C++
#include <array>
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    //C, original C++
    double a1[4] = {1.1, 1.2, 1.3, 1.4};

    //C++ 98 STL
    vector<double> v1(4);
    v1[0] = 1.5;
    v1[1] = 1.6;
    v1[2] = 1.7;
    v1[3] = 1.8;

    //C++ 11
    array<double, 4> ar1 = {1.9, 2.1, 2.3, 2.4};
    array<double, 4> ar2;
    ar2 = ar1;
    cout << "a1[2]: " << a1[2] << " at " << &a1[2] << endl;
    cout << "v1[2]: " << v1[2] << " at " << &v1[2] << endl;
    cout << "ar1[2]: " << ar1[2] << " at " << &ar1[2] << endl;
    cout << "ar2[2]: " << ar2[2] << " at " << &ar2[2] << endl;
    return 0;
}
-->
//a1[2]: 1.3 at 0x61fef8
//v1[2]: 1.7 at 0xfc6db8
//ar1[2]: 2.3 at 0x61fec8
//ar2[2]: 2.3 at 0x61fea8
~~~



# 15.函数

## 1.函数的返回类型

函数的返回值不能是数组，可以是其他任何类型--整数/浮点数/指针，甚至可以是结构和对象。（虽然C++函数不能直接返回数组，但是可以将数组作为结构或对象组成部分来返回。）

## 2.参数列表

原型中的变量名不必与定义中的变量名相同，而且可以省略

~~~C++
int main()
{
    int f1(int, double);
    //...
}
int f1(int a, double b)
{
 //....   
}
~~~



## 3.关于数组的两个恒等式

> arr[ i ] == *(arr + i)
>
> &arr[ i ] == arr + i
>
> 记住：将指针（包括数组名）加1，实际上是加上了一个与指针指向的类型的长度（以字节为单位）相等的值。

## 4.将数组作为参数意味着什么

传递常规变量时，函数将使用该变量的拷贝；但传递数组时，函数将使用原来的数组。

数组名与指针对应的好处：将数组地址作为参数可以节省复制整个数组所需的时间和内存。而且避免了使用原始数据增加的破坏数据的风险。

## 5.在调用函数时，在函数内部里直接对函数进行sizeof的话，获取的不是数组的实际长度，而是指针对用的地址长度。

~~~C++
#include <cstring>
#include <iostream>
using namespace std;
int sum_arr(int arr[], int n);
int main()
{
    int ar[] = {1, 2, 3, 4, 5};
    int size = sizeof ar;
    cout << "sizeof ar1[] is :" << sizeof ar << endl;
    sum_arr(ar, size);
}
int sum_arr(int *arr, int n)
{
    cout << "sizeof arr is :" << sizeof arr << endl;
    cout << "sizeof ar2[] is :" << n << endl;
    return 0;
}
--->
//sizeof ar1[] is :20
//sizeof arr is :4
//sizeof ar2[] is :20
~~~

> 在将数组作为入参时，调用函数时最好把数组和数组长度值一起传入：
>
> typename fnName(int arr[], sizeof arr)



## 6.保护数组

为防止函数无意中修改入参数组的内容，可以在声明形参时使用关键字const：

~~~C++
void show_array(const double ar[], int size);

//该声明表示，指针ar指向的是常量数据。这意味着在函数中对ar数组只有访问的权限，不能修改和删除数组。
~~~



## 7.尽可能使用const

将指针参数声明为指向常量数据的指针有两条理由：

> 这样可以避免由于无意间修改数据而导致的编程错误。

> 使用const使得函数能够处理const和非const实参，否则将只能接受非const数据。

如果条件允许，则应将指针形参声明为指向const的指针。

## 8.两种声明const的方式

~~~C++
int sloth = 3;
const int *ps = &sloth;
int * const finger = &sloth;

//在最后一个声明中，关键字const的位置与以前不同。这种声明格式使得finger只能指向sloth，但允许使用finger来修改sloth的值。中间的声明不允许使用ps来修改sloth的值，但允许将ps指向另一个位置。简而言之，finger和*ps都是const，但*finger和ps不是。

//如果愿意，还可以声明指向const对象的const指针：
double a  = 2.0E30;
const double * const strick = &a;
//其中，stick只能指向a，而stick不能用来修改a的值。简而言之，stick和*stick都是const。
    
~~~

## 9.二维数组作为入参

~~~C++
int data[3][4] = {{1,2,3,4},{5,6,7,8},{1,3,4,5}};
int total = sum(data, 3);

//sum原型
int sum(int ar2[][4], int size);

ar2[r][c] == *(*(ar2 + r) + c)

~~~

## 10.传递结构的地址

> 1.使用结构编程时，最直接的方式是像处理基本类型那样来处理结构；也就是说，将结构作为参数传递，并在需要时将结构用作返回值使用。
>
> 2.然而，按值传递结构有一个缺点。如果结构非常大，则复制结构将增加内存要求，降低系统运行的速度。出于这些原因，我们倾向于传递结构的地址，然后使用指针来访问结构的内容。
>
> 3.C++还提供了第三种选择------按引用传递。



传递结构的地址：

> 1.调用函数时，将结构的地址（&param1）而不是结构本身（param1）传递给它；
>
> 2.将形参声明为指向param的指针，即 param*类型；由于函数不应该修改结构，因此推荐使用const修饰符；
>
> 3.由于形参时指针而不是结构，因此应使用间接成员运算符（->），而不是成员运算符（.）；





~~~C++
#inculde<iostream>
using namespace std;

struct STRUCT    //定义一个结构，名称为STRUCT
{
    int hour;
    int minute;
}
void fn1(const STRUCT*);  // 定义一个方法，入参为const STRUCT*，其中const表示不能修改STRUCT的值，STRUCT*表示传递的是地址

void main()
{
    STRUCT s1 = {5, 45};
    fn1(&s1);         // 因为fn1的入参是地址，所以要用&来取变量的地址
}
   
void fn1(const STRUCT* s)
{
    cout << s->hour;               //因为传递的是STRUCT的地址，所以要用 ->  来取值
}
~~~



## 11.函数和array对象

> array位于名称空间std中。

~~~C++
using namespace std;
//定义一个array对象：
array<double, 4> express; //定义了一个长度为4，存放double类型的array

//如果函数来显示express的内容，可按照值传递
show(express);

//如果函数要修改对象express，则需将该对象的地址传递给函数
fill(&express);


~~~





	## 12.函数指针

### 1.获取函数地址

获取函数地址非常的简单：只要使用函数名（后面不跟参数）即可。也就是说，如果think()时一个函数，则think就是该函数的地址。要将函数作为参数进行传递，必须传递函数名。一定要区分传递的是函数地址，还是函数的返回值：

> process(think);     // 传递的是函数地址
>
> process(think());  //传递的是函数返回值 



### 2.声明函数指针

声明指向函数的指针时，必须指定指针指向的函数类型。这意味着声明应指定函数的返回类型以及函数的特征标（参数列表）。

> int (*fptr)(int, int);   //为提供正确的运算符优先级，必须在声明中使用括号将 *fptr括起。
>
>  *fptr (int, int)  意味着是一个返回指针的函数
>
>  (*fptr)(int, int) 意味着是fptr是一个指向函数的指针
>
> 以上声明了一个函数指针，其中指针名为fptr，它指向的函数的参数列表为(int, int)，它的返回参数是 int类型；
>
> 提示：通常，要声明指向特性类型的函数指针，可以首先编写这种函数（pf）的原型，然后用（*pf）来替换函数名，这样pf就是这类函数的指针。



### 3.使用指针来调用函数

指针变量名 = 被指向函数名

> fptr = maxx;   将fptr这个函数指针，指向了名为maxx的函数；

函数调用：指针变量名（函数调用时的实际参数）

> int x = fptr(5, 8);    <==>   int x = maxx(5, 8);



~~~C++
#include <iostream>
using namespace std;
int maxx(int, int);
int main()
{
    int (*pstr)(int, int);
    pstr = maxx;
    cout << "This is from pstr: " << pstr(5, 8) << endl;
    cout << "This is from maxx: " << maxx(5, 8) << endl;
}
int maxx(int a, int b)
{
    if (a >= b)
    {
        return a;
    }
    else
    {
        return b;
    }
}
//This is from pstr: 8
//This is from maxx: 8
~~~



## 12.指针函数 （返回指针的函数）

~~~ C++
#include <iostream>
using namespace std;
int main()
{

    char a = 'A';
    cout << getWord(a);
    //-> :A
    //Apple
    //注意点：1.getWord函数返回的是字符串的首地址；
    //       2."Apple"/"Banana"/"None"这三个字符串是常量，生命周期非常的长，就算函数已结束，它们依然存在，所以才可以返回上面的结果；

    char b = 'A';
    cout << getWord1(b);
    //直接编译报错
    //注意：因为在getWord1里，str1/str2/str3这些临时变量，在函数结束时，它们也随着消失，除非添加static关键字

    char c = 'A';
    cout << getWord2(c);
    // -> :A
    // Apple
}
char *getWord(char c)
{
    switch (c)
    {
    case 'A':
        return "Apple";
    case 'B':
        return "Banana";
    default:
        return "None";
    }
}
char *getWord1(char c)
{
    char str1[] = "Apple";
    char str2[] = "Banana";
    char str3[] = "None";
    switch (c)
    {
    case 'A':
        return str1;
    case 'B':
        return str2;
    default:
        return str3;
    }
}
char *getWord2(char c)
{
    static char str1[] = "Apple";
    static char str2[] = "Banana";
    static char str3[] = "None";
    switch (c)
    {
    case 'A':
        return (char *)str1;
    case 'B':
        return (char *)str2;
    default:
        return (char *)str3;
    }
}
~~~





## 13.函数总结

>   函数是C++的编程模块。要使用函数，必须提供定义和原型，并调用该函数。函数定义是实现函数功能的代码；函数原型描述了函数的接口：传递给函数的值的数目和种类以及函数的返回类型。函数调用使得程序将参数传递给函数，并执行函数的代码。
>
>   在默认情况下，C++函数按值传递参数。这意味着函数定义中的形参是新的变量，它们被初始化为函数调用所提供的值。因此，C++函数通过使用拷贝，保护了原始数据的完整性。
>
>   C++将数组名参数视为数组第一个元素的地址。从技术上讲，这仍然是按值传递的，因为指针是原始地址的拷贝，但函数将使用指针来访问原始数组的内容。当且仅当声明函数的形参时，下面两个声明才是等价的：
>
> ​	typeName arr[];
>
> ​	typeName * arr;
>
>   这两个声明都表明，arr是指向typeName的指针，但在编写函数代码时，可以像使用数组名那样使用arr来访问元素：arr[i]。即使在传递指针时，也可以将形参声明为const指针，来保护原始数据的完整性。由于传递数据的地址时，并不会传输有关数组长度的信息，因此通常将数组长度作为独立的参数来传递。另外，也可以传递两个指针（其中一个指向数组开头，另一个指向数组末尾的下一个元素），以指定一个范围，就像STL使用的算法一样。
>
>   C++提供了3种表示C-风格字符串的方法：字符数组/字符串常量和字符串指针。它们的类型都是char *（char指针），因此被作为char *类型参数传递给函数。C++使用空值字符（\0）来结束字符串，因此字符串函数检测空值字符来确定字符串的结尾。
>
>   C++还提供了string类，用于表示字符串。函数可以接受string对象作为参数以及将string对象作为返回值。string类的方法size()可用于判断其存储的字符串的长度。
>
>   C++处理结构的方式与基本类型完全相同，这意味着可以按值传递结构，并将其用作函数返回类型。但是如果结构非常大，则传递结构指针的效率将更高，同时函数能够使用原始数据。这些考虑因素也适用于类对象。
>
>   C++函数可以是递归的，也就是说，函数代码中可以包括对函数本身的调用。
>
>   C++函数名与函数地址的作用相同，通过将函数指针作为参数，可以传递要调用的函数的名称。
>
> 

问题1：

C++程序可使用哪3中C-风格字符串格式？

字符串可以存储在char数组中，可以用带双引号的字符串来表示，也可以用指向字符串第一个字符的指针来表示

~~~C++
char c1[] = "abcd";
cout << "c1 is " << c1 << endl;
char *c2 = c1;
cout << "c2 is " << c2 << endl;
string c3 = "hig";
cout << "c3 is " << c3 << endl;
~~~



# 16.C++新的函数特性

## 1.内联函数

要使用内联函数，必须采取下述措施之一：

> 1.在函数声明前加上关键字：inline；
>
> 2.在函数定义前加上关键字：inline。

​	通常的做法是省略原型，将整个定义（即函数头和所有函数代码）放在本应提供原型的地方。

​	程序员请求将函数作为内联函数时，编译器不一定会满足这种要求。它可能认为该函数过大或者注意到函数调用了自己 （<u>**内联函数不能递归**</u>） ，因此不将其作为内敛函数；而有些编译器没有启用或实现这种特性。

> 通常会将内联函数与宏进行比较：
>
> 1.内联函数在运行时可调试，而宏定义不可以;
> 2.编译器会对内联函数的参数类型做安全检查或自动类型转换（同普通函数），而宏定义则不会； 
> 3.内联函数可以访问类的成员变量，宏定义则不能； 
> 4.在类中声明同时定义的成员函数，自动转化为内联函数。



​	inline一般只用于如下情况：

> （1）一个函数不断被重复调用。
>
> （2）函数只有简单的几行，且函数不包含for、while、switch语句。



​	关于宏：

> 关于宏的第一规则是：绝不应该去使用它，除非你不得不这样做!
>
> **<u>宏是在代码处不加任何验证的简单替代</u>，而内联函数是将代码直接插入调用处，而减少了普通函数调用时的资源消耗。**
>
> **宏不是函数，只是在编译前（编译预处理阶段）将程序中有关字符串替换成宏体**。
>
> **inline函数是函数，但在编译中不单独产生代码，而是将有关代码嵌入到调用处。**



## 2.引用变量

### 1.创建引用变量

​	C和C++使用 **&** 符号来指示变量的地址。C++给**&**符号赋予了另一个含义，将其用来声明引用。

~~~C++
int rats;
int & rodents = rats;   //这里用 & ，将rodents定义成了rats的一个引用，它们指向相同的值和内存单元。
~~~

注意：**必须在声明引用变量时进行初始化**；

~~~C++
int a;
int &b;  // 错
int &b = a; //对

~~~



**引用只能通过初始化声明，不能通过赋值来设置；**

~~~C++
#include <iostream>
using namespace std;
int main()
{
    int a = 101;
    int *p = &a;
    int &b = *p;
    int c = 50;
    p = &c;
    cout << "a is " << a << ", b is " << b << ", p is " << *p << endl;
    return 0;
}

//a is 101, b is 101, p is 50
//这说明b初始化为*p使得b指向a，接下来将p改为指向c，并不能改变b指向a的事实，即b会‘从一而终’的指向a。


~~~





> 提到引用，一定要说一下引用和指针的区别：
>
> ​	**一、指针和引用的定义和性质区别：**
>
> (1) 指针：指针是一个变量，只不过这个变量存储的是一个地址，指向内存的一个存储单元，即：**指针是一个实体**；而**引用**跟原来的变量实质上是同一个东西，只不过是原变量的一个**别名**而已。如：
>
> ```C++
> int a=1;int *p=&a;
> 
> int a=1;int &b=a;
> ```
>
> 上面定义了一个整形变量和一个指针变量p，该指针变量指向a的存储单元，即p的值是a存储单元的地址。
>
> 而下面2句定义了一个整形变量a和这个整形a的引用b，事实上a和b是同一个东西，在内存占有同一个存储单元。
>
> (2) 可以**有const指针**，但是**没有const引用**；
>
> (3) **指针可以有多级**，但是**引用只能是一级**（int **p，合法； 而 int &&a是不合法的）
>
> (4) **指针的值可以为空**，但是**引用**的值**不能为NULL**，并且**引用在定义的时候必须初始化**；
>
> (5) **指针**的值在初始化后**可以改变**，即指向其它的存储单元，而**引用**在进行初始化后就不会再改变了，**从一而终**。
>
> (6)”sizeof引用”得到的是所指向的变量(对象)的大小，而”sizeof指针”得到的是指针本身的大小；
>
> (7)指针和引用的自增(++)运算意义不一样；
>
> **二、相同点**
>
> (1) 都是地址的概念；
>
> (2) 指针指向一块内存，它的内容是所指内存的地址；
>
> (3) 引用是某块内存的别名。
>
> **三、联系**
>
> 1、**引用在语言内部用指针实现**（如何实现？）。
>
> 2、对一般应用而言，把引用理解为指针，不会犯严重语义错误。**引用是操作受限了的指针**（仅容许取内容操作）。
>
> 引用是C++中的概念，初学者容易把引用和指针混淆一起。以下程序中，n是m的一个引用（reference），m 是被引用物（referent）。
>
> ```c++
> int m;
> int &n = m;
> ```
>
> n 相当于m 的别名（绰号），**对n 的任何操作就是对m 的操作**。
>
> 引用的一些规则如下：
>
> ```
> （1）引用被创建的同时必须被初始化（指针则可以在任何时候被初始化）。
> （2）不能有NULL 引用，引用必须与合法的存储单元关联（指针则可以是NULL）。
> （3）一旦引用被初始化，就不能改变引用的关系（指针则可以随时改变所指的对象）。123
> ```
>
> 以下示例程序中，k 被初始化为i 的引用。语句k = j 是把k 的值改变成为6，由于k 是i 的引用，所以i 的值也变成了6.
>
> ```
> int i = 5;
> int j = 6;
> int &k = i;
> k = j; // k 和i 的值都变成了61234
> ```
>
> 上面的程序看起来象在玩文字游戏，没有体现出引用的价值。**引用的主要功能是传递函数的参数和返回值**。C++语言中，**函数的参数和返回值的传递方式有三种：值传递、指针传递和引用传递。**
>
> “引用传递”的性质像“指针传递”，而书写方式像“值传递”。实际上“引用”可以做的任何事情“指针”也都能够做，**为什么还要“引用”这东西**？
>
> 答案是“用适当的工具做恰如其分的工作”。
>
> **指针能够毫无约束地操作内存中的如何东西，尽管指针功能强大，但是非常危险**。
>
> 就象一把刀，它可以用来砍树、裁纸、修指甲、理发等等，谁敢这样用？
>
> **如果的确只需要借用一下某个对象的“别名”，那么就用“引用”，而不要用“指针”，以免发生意外。**比如说，某人需要一份证明，本来在文件上盖上公章的印子就行了，如果把取公章的钥匙交给他，那么他就获得了不该有的权利。
>
> **总的来说，在以下情况下你应该使用指针：**
>
> 一是你考虑到存在不指向任何对象的可能（在这种情况下，你能够设置指针为空），
>
> 二是你需要能够在不同的时刻指向不同的对象（在这种情况下，你能改变指针的指向）。如果总是指向一个对象并且一旦指向一个对象后就不会改变指向，那么你应该使用引用。
>
> 还有一种情况，就是当你重载某个操作符时，你应该使用引用。
>
> **尽可能使用引用，不得已时使用指针。**
>
> 当你**不需要“重新指向”时，引用一般优先于指针被选用。**这通常意味着**引用用于类的公有接口时更有用。引用出现的典型场合是对象的表面，而指针用于对象内部。**



### 2.将引用用作函数参数

以交换两个变量的值为例：

~~~C++
#include <iostream>
using namespace std;
void exchangeV(int, int);
void exchangeP(int *, int *);
void exchangeR(int &, int &);
int main()
{
    int a = 1;
    int b = 2;
    cout << "a : " << a << ", b :" << b << endl;

    exchangeV(a, b);
    cout << "a1 : " << a << ", b1 :" << b << endl;

    exchangeP(&a, &b);
    cout << "a2 : " << a << ", b2 :" << b << endl;

    exchangeR(a, b);
    cout << "a3 : " << a << ", b3 :" << b << endl;

    return 0;
}
void exchangeV(int a, int b)
{
    int c = a;
    a = b;
    b = c;
}
void exchangeP(int *a, int *b)
{
    int c = *a;
    *a = *b;
    *b = c;
}
void exchangeR(int &a, int &b)
{
    int c = a;
    a = b;
    b = c;
}
-->
a : 1, b :2
a1 : 1, b1 :2
a2 : 2, b2 :1
a3 : 1, b3 :2
    
~~~





### 3.引用的属性和特别之处

~~~C++
#include <iostream>
using namespace std;

double pfv(double);
double pfr(double &);
double pfcr(const double &);
int main()
{
    double c = 3;
    cout << "pfv is " << pfv(c) << ", c is " << c << endl;
    c = 3;
    cout << "pfr is " << pfr(c) << ", c is " << c << endl;
    c = 3;
    cout << "pfcr is " << pfcr(c) << ", c is " << c << endl;

    return 0;
}
double pfv(double a)
{
    a *= a * a;
    return a;
}
double pfr(double &ra)
{
    ra *= ra * ra;
    return ra;
}
double pfcr(const double &ra)
{
    //ra *= ra * ra;  在编译过程中这里会报错，报ra是const类型，不能修改

    return ra * ra * ra;
}
~~~



如果程序员的意图是让函数使用传递给他的信息，而不对这些信息进行修改，同时又想使用引用，则应使用**常量引用**;

> 注意：如果函数调用的参数不是左值或与相应的const引用参数的类型不匹配，则C++将创建类型正确的匿名变量，将函数调用的参数的值传递给该匿名变量，并让参数来引用该变量。
>
> 应尽可能使用const：
>
> **1.使用const可以避免无意中修改数据的编程错误；**
>
> **2.使用const使函数能够处理const和非const实参，否则只能接受非const数据；**
>
> **3.使用const引用使函数能够正确生成并使用临时变量。**
>
> 因此，应尽可能将引用形参声明为const。



### 4.左值和右值

> **左值和右值的区分标准在于能否获取地址。**
>
> 在C++11中，区别表达式是左值或右值可以做这样的总结：**当一个对象被用作右值的时候，用的是对象的值（内容）；当对象被用作左值的时候，用的是对象的身份（在内存中的位置）。左值有持久的状态，而右值要么是字面常量，要么是在表达式求值过程中创建的对象，即左值持久，右值短暂**。

### 5.左值引用和右值引用

~~~C++
int &d = 10;       //报错，左边是一个左值引用，而右边是一个右值，无法将左值引用绑定到一个右值上。这里的10是一个右值
const int &d = 10;  //正确，如果是一个const的左值引用，是可以绑定到右值上的。

int && ii = 10;  //正确，右值引用关联到右值时，右值被存储到特定位置，右值引用指向该特定位置，也就是说，右值虽然无法获取地址，但是右值引用是可以获取地址的，该地址表示临时对象的存储位置。 
~~~



### 6.将引用用于结构

​	引用非常设计和用于结构和类，确实，引入引用主要是为了用于这些类型的，而不是基本的内置类型。

​	使用结构引用参数的方式与使用基本变量引用相同，只需要在声明结构参数时使用引用运算符&即可；

> 1. 返回引用时应该避免返回指向临时变量的指针。为了避免这种问题，最简单的方法是，返回一个作为参数传递给函数的引用。作为参数的引用将指向调用函数使用的数据，因此返回的引用也将指向这些数据。
> 2. 为了避免不必要的问题，在设计中，最好将返回类型声明为const引用。



### 7.对象/继承/引用

​	继承的另一个特征是，基类引用可以指向派生类对象，而无需进行强制类型转换。这种特征的一个实际结果是，可以定义一个接受基类引用作为参数的函数，调用该函数时，可以将积累对象作为参数，也可以将派生类对象作为参数。



### 8.何时使用引用参数

​	使用引用参数的主要原因有两个：

> 1.程序员能够修改调用函数中的数据对象；
>
> 2.通过传递引用而不是整个数据对象，可以提高程序的运行速度。

​	指导原则：

> 1.如果数据对象很小，如内置数据类型或小型结构，则按值传递；
>
> 2.如果数据对象是数组，则使用指针，因为这是唯一的选择，并将指针声明为指向const的指针‘
>
> 3.如果数据对象是较大的结构，则使用const指针或const引用，以提高程序的效率。这样可以节省复制结构所需的时间和空间；
>
> 4.如果数据对象是类对象，则使用const引用。类设计的语义常常要求使用引用，这是C++新增这项特性的主要原因，因此，传递类对象参数的标准方式是按引用传递；

 	对于修改调用函数中数据的函数：

> 1.如果数据对象是内置数据类型，则使用指针。如果看到注入fixit(&x)这样的代码（其中x是int），则很明显，该函数将修改x；
>
> 2.如果数据对象是数组，则只能使用数组；
>
> 3.如果数据对象是结构，则使用引用或指针；
>
> 4.如果数据对象是类对象，则使用引用。

​	

## 3.默认参数

​	为何要用默认参数？

> 提供了一种便捷方式，通过使用默认参数，可以减少要定义的析构函数、方法以及方法重载的数量。

​	如何设置默认参数？

> 必须通过函数原型

~~~C++
#include <cstring>
#include <iostream>
#include <math.h>
using namespace std;
void display(int i = 0);
int main()
{
    display();
    display(2); 
    return 0;
}
void display(int i)
{
    cout << "i is :" << i << endl;
} 
//i is :0
//i is :2
~~~



​	对于带参数列表的函数，**必须从右向左添加默认值**。也就是说，要为某个参数设置默认值，则必须为它右边的所有参数提供默认值：

~~~C++
int f1(int n, int m = 4, int j = 5);   //valid
int f2(int n, int m = 6, int j);  //invalid
int f3(int n = 0, int m = 2, int j = 5);  //valid
~~~



## 4.函数重载（多态）

​	函数重载（多态）可以使用多个同名的函数——它们完成相同的工作，但使用不同的参数列表；

​	函数重载的关键是函数的参数列表——也称为**函数特征标**。

​	请记住，是特征标，而不是函数类型使得可以对函数进行重载，例如，下面两个是互斥的：

~~~C++
long gronk(int n, int m);
double gronk(int n, int m);//编译报错，与上面的冲突 
//error: ambiguating new declaration of 'double gronk(int, int)'
//double gronk(int n, int m);
//test11.cpp:17:6: note: old declaration 'long int gronk(int, int)'
 //long gronk(int n, int m);
~~~

​	**返回类型可以不同，但是特征标必须不同！**



### 重载引用参数

> ​                                              **重载引用参数**
>
> 类设计和STL经常使用引用参数，因此知道不同引用类型的重载很有用。请看下面三个原型：
>
> ~~~C++
> void sink(double &r1);  
> void sank(const double &r2);
> void sunk(double &&r3);
> ~~~
>
> 1.左值引用参数`r1 `与可修改的左值参数（如double变量）匹配；
>
> 2.const左值引用参数`r2`与可修改的左值参数、const左值参数、右值参数（如两个double值的和）匹配；
>
> 3.左值引用参数`r3`与左值匹配；
>
> 注意到与`r1`	或`r3`匹配的参数都与`r2`匹配，这就带来了一个问题：如果重载使用这三种参数的函数，结果将如何？
>
> 答案是：将调用最匹配的版本：
>
> ~~~C++
> void staff(double &rs);  //matchses modifiable lvalue
> void staff(const double &rcs); //matches rvalue, const lvalue
> void stove(double &r1); // matches modifiable lvaule
> void stove(double &&r3); // matches rvalue
> ~~~
>
> 这能让我们根据参数是左值、const还是右值来定制函数的行为：
>
> ~~~C++
> double x = 55.5;
> 
> const double y = 32.0;
> 
> stove(x); // call stove(double &);
> stove(y); // call stove(const double &);
> stove(x+y); // call stove(double &&);
> ~~~
>
> 如果没有定义函数stove(double &&)，stove(x+y)将调用函数stove(const double &);



 **何时使用函数重载？**

> 仅当函数基本上执行相同的任务，但使用不同形式的数据时，才应采用函数重载。



## 5.函数模板

> 定义：函数模板时通用的函数描述，也就是说，它们使用泛型来定义函数，其中的**`泛型`**可用具体的类型（如int或double）替换。通过将类型作为参数传递给模板，可使编译器生成该类型的函数。由于模板允许以泛型（而不是具体类型）的方式编写程序，因此有时也被称为通用编程。由于类型时用参数表示的，因此模板特性有时也被称为参数化类型。
>
> <--类似与java中`泛型`-->

定义函数模板：

~~~C++
template <typename T>
T fnName(T a, T b,...)
{
    //do something
}
~~~

其中，

​	**`template`**和**`typename`**都是关键字，**`typename`**还可以用**`class`**替换， `T`和**`fnName`**可以随意起；**其中typename是新标准，class是为了向前兼容。因此写class较为通用。**

示例：

~~~C++
#include <cstring>
#include <iostream>
#include <math.h>
using namespace std; 

template <typename T>
T getMax(T a, T b);
int main()
{
    cout << "Max is :" << getMax(1, 2) << endl;
    cout << "Max is :" << getMax(10, 2) << endl;
    cout << "Max is :" << getMax(1.1, 2.2) << endl;
    return 0;
}

template <class T>
T getMax(T a, T b)
{
    return (a > b) ? a : b;
}
// Max is :2
// Max is :10
// Max is :2.2

~~~



注意：函数模板不能缩短可执行程序，最终的代码不包含任何模板，而只包含为程序生成的实际函数。使用模板的好处是，它使生成多个函数定义更简单、更可靠。



### **模板的局限性**

> 编写的模板函数很可能无法处理某些类型

假设有如下模板函数：

~~~C++
template <typename T>
void f(T a, T b)
{...}
~~~

通常，代码假定可执行哪些操作。例如，下面的代码假定定义了复制，但如果T为数组，这种假设将不成立：

~~~C++
a = b;
~~~

同样，下面的语句假设定义了`<`，但如果T为结构，该假设便不成立：

~~~C++
if ( a > b)
~~~

所以编写的模板函数很可能无法处理某些类型。



### **模板显示具体化**

> 对于给定的函数名，可以有非模板函数，模板函数和显示具体化模板函数集他们的重载 版本。
> 显示具体化的原型和定义应该以template<>打头 ，并通过名称来指出类型。
> **具体化优先于常规模板，而非模板函数优先于具体化和常规模板。**
>
> 调用优先级：**普通函数>显式具体化>显式实例化>普通模版**
>
> 显式实例化用于人为选择特定的类型！
>
> 显式具体化用于修改某类型的函数具体功能！
>
> **模版匹配的方式非常复杂！慎用不同参数个数的模版重载！**

举例：

~~~C++
class job
{
    char name[32];
    double salary;
    int floor
};

//1.非模板函数/普通函数
void swap(job &a, job &b)
{
    job temp = a;
    a = b;
    b = temp;
}

//2.模板函数/常规模板
template <typename T>
void swap(T &a, T &b)
{
    T temp = a;
    a = b; 
    b = temp;
}

//3.显式具体化
template <> void swap<job>(job &a, job &b)
{
    double s;
    int f;

    s = a.salary;
    a.salary = b.salary;
    b.salary =s;

    f = a.floor;
    a.floor = b.floor;
    b.floor = f;
}

int main()
{
    double u,v = 10.0;
    swap(u, v);	//use template 2
    
    job a,b;
    ...
    swap(a, b);  //use void swap(job &, job &) 1    
}
~~~



### 实例化和具体化

> ​	为了进一步了解模板，必须理解术语：**`实例化`**和**`具体化`** 。记住，在代码中包含函数模板本身并不会生成函数定义，它只是一个用于生成函数定义的方案。编译器使用模板为特定类型生成函数定义时，得到的时模板实例。例如上面例子中，函数调用swap(u,v)，导致编译器生成swap的一个实例，该实例使用double类型。模板并非函数定义，但使用double的模板实例时函数定义。这种实例化方式被称为**`隐式实例化`**，因为编辑器之所以知道需要进行定义，是由于程序调用swap(u,v)时提供了double参数。
>
> ​	最初，编译器只能通过隐式实例化，来使用模板生成函数定义，但现在C++还允许显式实例化。这意味着可以直接命令编译器创建特定的实例，如swap<int>( )。其语法是，声明所需的种类——用<>符号指示类型，并在声明前加上关键字**`template`**。
>
> ~~~C++
> template void swap<int>(int,int); //显式实例化，使用swap()模板生成int类型的函数定义。
> ~~~
>
> ​	实现了这种特性的编译器看到上述声明后，将使用swap( )模板生成一个使用int类型的实例。也就是说，该声明的意思是“**`使用swap()模板生成int类型的函数定义。`**”
>
> ​	与显式实例化不同的是，显式具体化使用下面两个等价的声明之一：
>
> ~~~C++
> template <> void swap<int>(int &, int &);  //显式具体化
> <==>
> template <> void swap(int &, int &);   //显式具体化，不要使用swap()模板来生成函数定义，而应使用专门为int类型显式地定义的函数定义
> ~~~
>
> ​	区别在于，这些声明的意思是“**`不要使用swap()模板来生成函数定义，而应使用专门为int类型显式地定义的函数定义`**。这些原型必须有自己的函数定义。
>
> ​	显式具体化声明在关键字`template`后包含`<>`，而显式实例化没有。



> **警告：试图在同一个文件（或转换单元）中使用同一种类型的显式实例和显式具体化，将出错。**



### 关键字decltype

> 作用：在函数模板中定义左值需要确定类型但类型未知的时候：
>
> 例：
>
> ~~~C++
> int x;
> decltype(x) y;  // make y the same type as x
> ~~~
>
> ~~~C++
> template <class T1, class T2>
> void ft(T1 x, T2 y)
> {
>     ...
>    decltype(x + y) xpy = x + y;
> }
> ~~~



**核对表的简化版**

> 这个知识点是为了后面理解在使用decltype这个字段的时候，它将var确定成哪种类型，知识点后面有面试题，可以结合起来看

​	假设有如下声明：

~~~C++
decltype(expression) var;
~~~

​	第一步：如果expression是一个没有用括号括起的标识符，则var的类型与该标识符的类型相同，包括const等限定符：

~~~C++
double x = 5.5;
double y = 7.9;
double &rx = x;
const double *pd;

decltype(x) w;   // w的类型是 double
decltype(rx) u = y; // u的类型是 double &
decltype(pd) v; // v的类型是 const double *
~~~

​	第二步：如果expression是一个函数调用，则var的类型与函数的返回类型相同：

~~~C++
long indeed(int);
decltype (indeed(3)) m; // m是long类型
~~~

> 注意，这里并不会实际调用函数。编译器通过查看函数的原型来获悉返回类型，而无需实际调用函数。

​	第三步：如果expression是一个左值，则var为指向其类型的引用。这好像意味着前面的w应为引用类型，因为x是一个左值。但是别忘了，这种情况已经在第一步处理过了。要进入第三步，expression不能是未用括号括起的标识符。那么，expression是什么情况下进入第三步呢？一种显而易见的情况是，expression是用括号括起的标识符：

~~~C++
double xx = 4.4;
decltype((xx)) r2 = xx;  //r2的类型是 double &   // 第三步
decltype(xx) w = xx;  //w的类型是 double  //第一步

~~~

​	顺便说一句，括号并不会改变表达式的值和左值性。例如，下面两条语句等效：

~~~C++
xx = 98.6;
(xx) = 98.6; // 不影响使用xx
~~~

​	第四步：如果前面的条件都不满足，则var的类型与expression的类型相同：

~~~C++
int j = 3;
int &k = j;
int &n = j;
decltype(j + 6) i1;  //i1的类型是 int
decltype(100L) i2;	//i2的类型是 long
decltype(k + n) i3;	//i3的类型是 int
~~~

> 请注意，虽然k和n都是引用，但表达式k+n不是引用；它是两个int的和，因此类型为int。

​	如果需要多次声明，可结合使用typedef和decltype：

~~~C++
template<class T1, class T2>
void ft(T1 x, T2 y)
{
    typedef decltype(x + y) xytype;
    xytype xpy = x + y;
    xytype arr[10];
    xytype &rxy = arr[2];  //rxy是一个引用
}
~~~







## 6.总结

> ​	C++扩展了C语言的函数功能。通过将**`inline`**关键字用于函数定义，并在首次调用该函数前提供其函数定义，可以使得C++编译器将该函数是为内联函数。也就是说，编译器不是让程序跳到独立的代码段来执行函数，而是用相应的代码替换函数调用。只有在函数很短时才能采用内联方式。
>
> ​	引用变量是一种伪装指针，它允许为变量创建别名。引用变量主要被用作处理结构和类对象的函数的参数。通常，被声明为特定类型引用的标识符只能指向这种类型的数据；然而，如果一个类是从另一个类派生出来的，则基类引用可以指向派生类对象。
>
> ​	C++原型让您能够定义参数的默认值。如果函数调用省略了相应的参数，则程序将使用默认值；如果函数调用提供了参数值，则程序将使用这个值（而不是默认值）。只能在参数列表中从右到左提供默认参数。因此，如果为某个参数提供了默认值，则必须为该参数右边所有的参数提供默认值。
>
> ​	函数的特征标是其参数列表。程序员可以定义两个同名函数，只要其特征标不同。这杯称为函数多态或函数重载。通常，通过重载函数来为不同的数据类型提供相同的服务。
>
> ​	函数模板自动完成重载函数的过程。只需使用泛型和具体算法来定义函数，编译器将为程序中使用的特性参数类型生成正确的函数定义。





# 17.内存模型和名称空间

## 1.单独编译

> 这一节主要是讲如何组织一个程序的内容，一个程序该由哪几部分组成比较合理，释义为主，可以重点学习，必要时可以自己按照笔记手动敲一遍来理解并加深印象。

​	和C语言一样，C++也允许甚至鼓励程序员将组件函数放在独立的文件中。

​	为了减少不必要的麻烦，我们一般将程序分成三部分：

1. ​		头文件：包含结构声明和使用这些结构的函数的原型；
2. ​		源代码文件：包含与结构有关的函数的代码；
3. ​		源代码文件：包含调用与结构相关的函数的代码。

> ​	注意：请不要将函数定义或变量声明放到头文件中。这样做对于简单的情况可能是可行的，但通常会引来麻烦。例如，如果在头文件包含一个函数定义，然后在其他两个文件（属于同一程序）中包含该头文件，则同一个程序中将包含同一个函数的两个定义，除非函数是内联的，否则这将出错。



**头文件**

​	头文件中常包含的内容：

> - 函数原型。
> - 使用#define或const定义的符号常量。
> - 结构声明。
> - 类声明。
> - 模板声明。
> - 内联函数。

​	将**`结构声明`**放在头文件中是可以的，因为它们不创建变量，而只是在源代码文件中声明结构变量时，告诉编译器如何创建该结构变量。同样，**`模板声明`**不是将被编译的代码，它们指示编译器如何生成与源代码中的函数调用相匹配的函数定义。**`被声明为const的数据`**和**`内联函数`**有特殊的链接属性，因此也是可以将其放在头文件中，而不会引起问题。

> 注意：在包含头文件时，我们使用“coordin.h”，而不是<coordin.h>。如果文件名包含在尖括号中，则C++编译器将在存储标准头文件的主机系统的文件系统中查找；但如果文件名包含在双引号中，则编译器将首先查找当前的工作目录或源代码目录（或其他目录，这取决于编译器）。如果没有在那里找到头文件，则将在标准位置查找。**因此在包含自己的头文件时，应使用引号而不是尖括号。**
>
> ​	只需要将源代码文件加入到项目中，而不用加入头文件。这是因为#include指令管理头文件。另外，不要使用#include来包含源代码文件，这样做将导致多重声明。

**头文件管理**

> 在同一个文件中只能将同一个头文件包含一次。为了避免犯错，我们引入一种技术：基于预处理器编译指令**`ifndef`**(即 if not defined)。
>
> ~~~C++
> #ifndef COORDIN_H_
> ...
> #endif
> ~~~
>
> 这段代码意味着：仅当以前没有使用预处理器编译指令#define定义名称COORDIN_H_时，才处理**`#ifndef`**和**`#endif`**之间的语句。

示例：

![](C:\Users\haoguangsu\Pictures\Camera Roll\1586589587276.png)

~~~C++
//coordin.h
#ifndef COORDIN_H_
#define COORDIN_H_
struct polar	//结构声明
{
    double distance;
    double angle;
};

struct rect		//结构声明
{
    double x;
    double y;
};

polar rect_to_polar(rect xypos); //函数原型
void show_polar(polar dapos); //函数原型
#endif

~~~

~~~C++
//file1.cpp
#include "coordin.h"    // 这里使用的是双引号，编译器会直接从文件系统中查找
#include <iostream>
using namespace std;
int main()
{
    rect rplace;
    polar pplace;
    cout << "Enter the x and y values: ";
    while (cin >> rplace.x >> rplace.y)
    {
        pplace = rect_to_polar(rplace);
        show_polar(pplace);
        cout << "Next two numbers (q to quit): ";
    }
    cout << "Bye!" << endl;
}
~~~

~~~C++
//file2.cpp
#include "coordin.h"
#include <cmath>
#include <iostream>
using namespace std;
polar rect_to_polar(rect xypos)
{
    polar answer;
    answer.distance = sqrt(xypos.x * xypos.x + xypos.y * xypos.y);
    answer.angle = atan2(xypos.y, xypos.x);
    return answer;
}

void show_polar(polar dapos)
{
    const double Rad_to_deg = 57.29577951;
    cout << "distance = " << dapos.distance;
    cout << ", angle = " << dapos.angle * Rad_to_deg;
    cout << " degrees" << endl;
}
~~~

> 编译指令：
>
> **串联编译：**
>
> PS D:\c++\test\test1> g++  file2.cpp file1.cpp **-o** a.exe
>
> 或者
>
> **分开编译再串联：**
>
> g++ **-c** file1.cpp **-o** 1.o
>
> g++ **-c** file2.cpp **-o** 2.o
>
> g++ 1.o  2.o  **-o** a.exe
>
> 执行结果：
>
> PS D:\c++\test\test1> .\a.exe
> Enter the x and y values: 12 12
> distance = 16.9706, angle = 45 degrees
> Next two numbers (q to quit): q
> Bye!



## 2.存储持续性、作用域和链接性

> 内存知识背景：
>
> ​	C++使用三种不同的方案来存储数据（在C++11中是四种），这些方案的区别就在于数据保留在内存中的时间。
>
> ​	1.**`自动存储持续性`**：在函数定义中声明的变量（包括函数参数）存储持续性为自动的。它们在程序开始执行其所属的函数或代码块时被创建，在执行完函数或代码块时，它们使用的内存被释放。C++有两种存储持续性为自动的变量。
>
> ​	2.**`静态存储持续性`**：在函数定义外定义的变量和使用关键字**`static`**定义的变量的存储持续性都为静态。它们在程序整个运行过程中都存在。C++有3种存储持续性为静态的变量。
>
> ​	3.**`线程存储持续性`**(C++11)：当前，多核处理器很常见，这些CPU可同时处理多个执行任务。这让程序能够将计算放在可并行处理的不同线程中。如果变量是使用关键字thread_local声明的，则其生命周期与所属的线程一样长。
>
> ​	4.**`动态存储持续性`**：用new运算符分配的内存将一直存在，知道使用delete运算符将其释放或程序结束为止。这种内存的存储持续性为动态，有时被称为**`自由存储`**或**`堆`**;

### 1.作用域和链接

​	变量的作用域可分为：局部作用域、全局作用域；

​	函数的作用域可以是整个类或整个名称空间（包括全局的），但不能是局部的（因为不能在代码块内定义函数，如果函数的作用域为局部，则只对他自己是可见的，因此不能被其他函数调用。这样的函数将无法运行）。

​	链接性描述了名称如何在不同单元间共享。链接性为外部的名称可在文件间共享，链接性为内部的名称只能由一个文件中的函数共享。自动变量的名称没有链接性，因为它们不能共享。

### 2.自动存储持续性

​	在默认情况下，在函数中声明的函数参数和变量的存储持续性为**自动**，作用域为局部，没有链接性。不同函数内部定义了相同名称的变量，它们是相对独立的。

​	当全局变量与局部变量重名时，在局部代码块中，会使用局部变量，出了代码块后，重新使用全局变量。

~~~C++
#include <iostream>
using namespace std;
void display(int);
int main()
{
    int a = 0;
    cout << "1.a is :" << a << endl;
    display(a);
    cout << "3.a is :" << a << endl;

    return 0;
}
void display(int a)
{
    a = 1;
    cout << "2.a is :" << a << endl;
}
//1.a is :0
//2.a is :1
//3.a is :0
~~~



> 自动变量和栈（这一段对于理解栈的概念有帮助，建议多读几遍）
>
> ​	了解典型的C++编译器如何实现自动变量有助于更深入地了解自动变量。由于自动变量的数目随函数的开始和结束而增减，因此程序必须在运行时对自动变量进行管理。常见的方法时留出一段内存，并将其视为栈，以管理变量的增减。之所以被称为栈，是由于新数据被象征性地放在原有数据的上面（也就是说，在相邻的内存单元中，而不是在同一个内存单元中），当程序使用完后，将其从栈中删除。栈的默认长度取决于实现，但编译器通常提供改变栈长度的选项。程序使用两个指针来跟踪栈，一个指针指向栈底——栈的开始位置，另一个指针指向堆顶——下一个可用内存单元。当函数被调用时，其自动变量将被加入到栈中，从而释放新变量使用的内存。
>
> ​	栈时LIFO(后进先出)的，即最后加入到栈中的变量首先被弹出。这种设计简化了参数传递。函数调用将其参数的值放在栈顶，然后重新设置栈顶指针。被调用的函数根据其形参描述来确定每个参数的地址。

### 3.静态持续变量

> ​	和C语言一样，C++也为静态存储持续性变量提供了3种链接性：**`外部链接性`**（可在其他文件中访问）、**`内部链接性`**（只能在当前文件中访问）和**`无链接性`**（只能在当前函数或代码块中访问）。这3种链接性都在整个程序执行期间存在，与自动变量相比，它们的寿命更长。由于**静态变量的数目在程序运行期间是不变的**，因此程序不需要使用特殊的装置（如栈）来管理它们。**编译器将分配固定的内存块来存储所有的静态变量，这些变量在整个程序执行期间一直存在**。另外，如果没有显式地初始化静态变量，编译器将把它设置为0。在默认情况下，静态数组和结构将每个元素或成员的所有位都设置为0。

​	如何创建这3种静态持续变量以及它们的特点：

​	1.创建链接性为外部的静态持续变量，必须在**代码块的外面**声明它。

​	2.创建链接性为内部的静态持续变量，必须在**代码块的外面**声明它，并使用**static**限定符。

​	3.创建没有链接性的静态持续变量，必须在**代码块内**声明它，并使用**static**限定符。

~~~C++
int global = 1000;      //第一种
static int one_file = 50;	//第二种，用static限定符
int main()
{
    ...
}
void fn1(int n)
{
    static int count = 0;  	//第三种，在代码块内部，用static限定符
    int llama = 0;
}
~~~

​	第一种，global为外部链接性，所以它的作用域为整个文件，而且可以在程序的其他文件中使用它；

​	第二种，one_file为内部链接性，所以它的作用域为整个文件；

​	第三种，count为无链接性，所以它的作用域只在fn1函数内。但是不同于llama，就算没有调用fn1函数，count也存在与内存中了。



​	所有的静态持续变量都有下述初始化特征：未被初始化的静态变量的所有位都被设置位0。这种变量被称为**`零初始化的`**。

​											**5种变量存储方式**

| 存储描述         | 持续性 | 作用域 | 链接性 | 如何声明                         |
| ---------------- | ------ | ------ | ------ | -------------------------------- |
| 自动             | 自动   | 代码块 | 无     | 在代码块中                       |
| 寄存器           | 自动   | 代码块 | 无     | 在代码块中，使用关键字register   |
| 静态，无链接性   | 静态   | 代码块 | 无     | 在代码块中，使用关键字static     |
| 静态，外部链接性 | 静态   | 文件   | 外部   | 不在任何函数内                   |
| 静态，内部链接性 | 静态   | 文件   | 内部   | 不在任何函数内，使用关键字static |



### 4.静态持续性、外部链接性

> 链接性为外部的变量通常简称为**`外部变量`**，它们的存储持续性为静态，作用域为整个文件。外部变量是在函数外部定义的，因此对所有函数而言都是外部的。

​	1.单定义规则

​	一方面，在每个使用外部变量的文件中，都必须声明它；另一方面，C++有“单定义规则”，**变量只能有一次定义**。为了满足这种需求，C++提供了两种变量声明。一种是**`定义声明`**（定义），**它给变量分配存储空间**；另一种是**`引用声明`**（声明），**它不给变量分配存储空间**，因为它引用已有的变量。

​	引用声明使用关键字**`extern`**，且不进行初始化；否则，声明为定义，导致分配存储空间。

​	

​	如果要在多个文件中使用外部变量，只需在一个文件中包含该变量的定义（单定义规则），但在使用该变量的其他所有文件中，都必须使用关键字**extern**声明它；



![1586769902897](C:\Users\haoguangsu\Pictures\Camera Roll\1586769902897.png)

~~~C++
//test1.cpp
#include <iostream>
using namespace std;
int abc = 100;
~~~



~~~C++
//test2.cpp
#include <iostream>
using namespace std;
extern int abc;    //引用声明，且不能初始化，否则编译报错
int main()
{
    cout << "abc is " << abc << endl;
    return 0;
}
~~~

>  g++ -fexec-charset=GBK test1.cpp test2.cpp test3.cpp -o a.exe
>
> // abc is 100
>
> 问题1：如果在同一个文件系统里，两个文件里不能定义相同的外部链接的变量
>
> ~~~C++
> //test3.cpp
> #include <iostream>
> using namespace std;
> int abc = 1001;   // 如果在另一个文件里也定义了abc，编译会报错
> ~~~
>
> // multiple definition of `abc';
>
> 问题2：引用声明，且不能初始化，否则编译报错
>
> 如果在test2.cpp里引用声明，并且初始化的话，编译会报错
>
> ~~~C++
> #include <iostream>
> using namespace std;
> extern int abc = 22;
> int main()
> {
>     cout << "abc is " << abc << endl;
>     return 0;
> }
> 
> 
> ~~~



​	C++比C语言更近了一步——它提供了**作用域解析运算符**(**`::`**)。放在变量名前时，该运算符表示使用变量的全局版本。

~~~C++
#include <iostream>
using namespace std;
void display(int);
int a;
int main()
{
    a = 10;
    display(a);
    return 0;
}
void display(int a)
{
    a = 11;
    cout << "global a :" << ::a << endl;  //这里用了作用域解析运算符，所以显式的是全局作用域里的a(10)，而不是局部作用域里的a(11)
    cout << "part a :" << a << endl;
}
//global a :10
//part a :11
~~~



### 5.静态持续性、内部链接性

​	将**`static`**限定符用于作用域为整个文件的变量时，该变量的链接性将为**内部的**。在多文件程序中，内部链接性和外部连结性之间的差别很有意义。链接性为内部的变量只能在其所属的文件中使用；但常规外部变量都具有外部链接性，既可以在其他文件中使用。

~~~C++
//test1.cpp
...
int a;
...
    
//test2.cpp
...
int a;
...
    
//当test1.cpp和test2.cpp在同一个程序中时，编译会报错：a first defined here
//因为这违反了单定义规则。test2中的定义试图创建一个外部变量
~~~

​	但如果文件定义了一个静态外部变量，其名称与另一个文件中声明的常规外部变量相同，则在该文件中，静态变量将隐藏常规外部变量。

~~~C++
//test1.cpp
...
static int a;   //在这里添加static关键字，将a变成静态变量，编译就不会报错，就会隐藏来自test2的常规外部变量a。这没有违反单定义规则，因为关键字static指出标识符a的链接性为内部，只对当前的test1.cpp有效，因此并非要提供外部定义。
...
    
//test2.cpp
...
int a;
...
~~~

> 在多文件程序中，可以在一个文件（且只能在一个文件）中定义一个外部变量。使用该变量的其他文件必须使用关键字**extern**声明它。

​	可使用外部变量在多文件程序的不同部分之间共享数据；可使用链接性为内部的静态变量在同一个文件中的多个函数之间共享数据（名称空间提供了另外一种共享数据的方法）。另外，如果将作用域为整个文件的变量变为静态的，就不必担心其名称与其他文件中的作用域为正给文件的变量发生冲突。

> 总结：
>
> ​	多文件之间共享数据——使用外部变量 (其他文件用**extern**声明使用)
>
> ​	单文件内部多函数之间共享数据——使用静态变量（关键字 **static**）
>
> ​	单文件全局变量——使用静态变量（关键字 **static**）

​	

### 6.静态存储持续性、无链接性

​	无链接性的局部变量——将**`static`**限定符用于在代码块中定义的变量。

​	在代码块中使用**`static`**时，将导致局部变量的存储持续性为**`静态的`**。这意味这虽然该变量只在该代码块中可用，但它在该代码块不处于活动状态时仍然存在。因此**在两次函数调用之间**，静态局部变量的值将保持不变。另外，如果初始化了静态局部变量，则程序只在启动时进行一次初始化。以后再调用函数时，将不会像自动变量那样再次被初始化。



### 7.说明符和限定符

​	有些被称为**`存储说明符`**或**`cv-限定符`**的C++关键字提供了其他有关存储的信息。

> **`auto`**：在C++11之前，指出变量为自动变量，在C++11中用于自动类型推断。
>
> **`register`**：   用于在声明中指示寄存器存储，在C++11中，它只是显式的指出变量是自动的。
>
> **`static`**：被用在作用域为整个文件的声明中时，表示内部链接性；被用于局部声明中，表示局部变量的存储持续性为静态的。
>
> **`extern`**：表明时引用声明，即声明引用在其他地方定义的变量。
>
> **`thread_local`**（C++新增的）：指出变脸过的持续性与其所属线程的持续性相同。thread_local变量之于线程，犹如常规静态变量之于整个程序。
>
> **`mutable`**：可以用它来指出，即使结构（或类）变量为const，其某个成员也可以被修改。下面会结合cv限定符来解释。

​	**注：在同一个声明中不能使用多个说明符，但thread_local除外，它可与static或extern结合使用。**



​	**cv-限定符**

> **`const`**：表明内存被初始化后，程序便不能在对它进行修改。
>
> **`volatile`**：表明即使程序代码没有对内存单元进行修改，其值也可能发生变化。是防止在共享的空间发生读取的错误。只保证其可见性，不保证原子性；使用volatile指每次从内存中读取数据，而不是从编译器优化后的缓存中读取数据，简单来讲就是防止编译器优化。加关键字后，就在RAM中读取变量的值，而不是直接在寄存器中取值。
>
> 
>
> 回到**`mutable`**，
>
> ~~~C++
> #include <cstring>
> #include <iostream>
> using namespace std; 
> struct student
> {
>     string name;
>     int age;
>     mutable double goal;
> };
> 
> int main()
> {
>     const student hgs = {"haoguangsu", 21, 99.5};
>     hgs.goal = 100;  //正确，因为在goal前添加了mutable，所以虽然hgs前有const，但是仍然可以修改goal。
>     hgs.age = 100; 	//编译错误，hgs已经被const，而且age前没有mutable，所以不能修改。
>     return 0;
> } 
> ~~~
>



​	**`const`**	

​	一般情况下只能有一个文件可以包含前面的声明，而其他文件必须使用**extern**关键字来提供引用声明。另外，只有未使用extern关键字的声明才能进行初始化：

~~~C++
extern const int fingers;  //引用其他文件里的变量，此处不能初始化
~~~

​	因此，需要为某个文件使用一组定义，而其他文件使用另一组声明。然而，由于外部定义的**const**数据的链接性为内部的，因此可以在所有文件中使用相同的声明。

~~~C++
//test1.cpp
const int a = 10;
int main()
{
    cout << "a is " << a;
}
// a is 10

//test2.cpp
const int a = 11;
int main()
{
    cout << "a is " << a;
}
// a is 11
~~~

​	

​	内部链接性还意味着，每个文件都有自己的一组常量，而不是所有文件共享一组常量。每个定义都是其所属文件私有的，这就是能够将常量定义放在头文件中的原因。这样，只要在两个源代码文件中包括同一个头文件，则它们将获得同一组常量。





### 8.函数和链接性

​	和变量一样，函数也有链接性，虽然可以选择的范围比变量小。和C语言一样，C++不允许在一个函数中定义另外一个函数，因此所有函数的存储持续性都自动为静态的，即在整个程序执行期间都一直存在。在默认情况下，函数的链接性为外部的，即可以在文件间共享。实际上，可以在函数原型中使用关键字**`extern`**来指出函数时在另一个文件中定义的，不过这是可选的（要让程序在另一个文件中查找函数，该文件必须作为程序的组成部分被编译，或者时由链接程序搜索的库文件）。还可以使用关键字**`static`**将函数的链接性设置为内部的，**使之只能在一个文件中使用，必须同时在原型和函数定义中使用该关键字**：

~~~C++
static int private(double x);
...
static int private(double x)
{
    ...
}
~~~

​	这意味着该函数只在这个文件中可见，还意味着可以在其他文件中定义同名的函数。和变量一样，在定义静态函数的文件中，静态函数将覆盖外部定义，因此即使在外部定义了同名的函数，该文件仍使用静态函数。

~~~C++
//test1.cpp
...
void display()
{
    ...
}

//test2.cpp
...
void display()
{
    ...
}

//g++ test1.cpp test2.cpp -o a.exe 编译会报错，因为两个文件中存在同名非静态函数，如果要编译通过，在两个函数声明和定义的前面都加上static关键字
~~~

> **C++在哪里查找函数？**
>
> ​	假设在程序的某个文件中调用一个函数，如果该文件中的函数原型指出该函数是i静态的，则编译器将只在该文件中查找函数定义；否则，编译器将在所有程序文件中查找。如果找到两个定义，编译器将发出错误信息，因为每个外部函数只能有一个定义。如果在程序文件中没有找到，编译器将在库中搜索。这意味着如果定义了一个与库函数同名的函数，编译器将使用程序员定义的版本，而不是库函数。（然而，C++保留了标准库函数的名称，即程序员不应使用它们）。有些编译器-链接程序要求显式地指出要搜索哪些库。
>



### 9.语言链接性

​	另一种形式的链接性——称为语言链接性也对函数由影响。首先介绍一些背景只是。链接程序要求每个不同的函数都有不同的符号名。在C语言中，一个名称值对应一个函数，因此这很容易实现。为满足内部需要，C语言编译器可能将spiff这样的函数名翻译为_spiff。这种方法被称为C语言链接性。但在C++中，**同一个名称可能对应多个函数，必须将这些函数翻译为不同的符号名称**。因此**`C++编译器执行名称矫正或名称修饰，为重载函数生成不同的符号名称`**。例如，可能将spiff(int)转换为 _spiff_i ，而将spiff(double, double)转换为 _spiff_d_d，这种方法被称为C++语言链接。

​	链接程序寻找与C++函数调用匹配的函数时，使用的方法与C语言不同。但如果要在C++程序中使用C库中预编译的函数，将出现什么情况呢？例如，假设由下面的代码：

~~~C++
spiff(22);
~~~

​	它在C库文件中的符号名称为 _spiff，但对于我们假设的链接程序来说，C++查询约定时查找符号名称 _spiff_i，为解决这种问题，可以用函数原型来指出要使用的约定：

~~~C++
extern "C" void spiff(int);
extern void spoff(int);
extern "C++" void spaff(int);	
~~~

​	第一个原型使用C语言链接性；而后面的两个使用C++语言链接性。第二个原型时通过默认方式指出这一点的，而第三个显式地指出了这一点。

​	C和C++链接性是C++标准指定的说明符，但实现可提供其他语言链接性说明符。



### 10.存储方案和动态分配(new)

​	前面介绍C++用来为变量（包括数组和结构）分配内存的5中方案（线程内存除外），它们不适用于使用C++运算发new（或C函数malloc()）分配的内存，这种内存被称为**`动态内存`**。动态内存由运算符**`new`**和**`delete`**控制，而不是由作用域和链接性规则控制。因此，可以在一个函数中分配动态内存，而在另一个函数中将其释放。与自动内存不同，动态内存不是LIFO，其分配和释放顺序要取决与new和delete在核实以何种方式被使用。通常，编译器使用三块独立的内存：一块用于**`静态变量`**（可能再细分），一块用于**`自动变量`**，另一块用于**`动态存储`**。

​	虽然存储方案概念不适用于动态内存，但是用于用来跟踪动态内存的自动和静态指针变量。例如，假设在一个函数中包含下面的语句：

~~~c++
float *p_fees = new float[20];
~~~

​	由new分配的80个字节（假设float为4个字节）的内存将一直保留在内存中，直到使用delete运算符将其释放。但当包含该声明的语句块执行完毕，p_fees指针将消失。如果希望另一个函数能够使用这80个字节中的内容，则必须将其地址传递或返回给该函数。另一方面，如果将p_fees的链接性声明为外部的，则问家中位于该声明后面的所有函数都可以使用它。另外，通过在另一个文件中使用下述声明，便可在其中使用该该指针：

~~~C++
extern float *p_fees;
~~~

> 注意：在程序结束时，由new分配的内存通常都将被释放，不过情况也并不总是这样。例如，在不那么健壮的操作系统中，在某些情况下，请求大型内存块将导致该代码块在程序结束不会被自动释放。最佳的做法是，使用delete来释放new分配的内存。



​	**1.使用new运算符初始化**

~~~C++
int *pi = new int(6);  // 创建一个*pi，并初始化为6
int *ar = new int[4] {1,2,3,4}; //创建一个int类型的数组，并初始化

~~~



​	**2.new失败时**

​	new可能找不到请求的内存量。在最初的10年中，C++在这种情况下让new返回空指针，但现在将引发异常**std::bad_alloc**。所以存在new失败的情况，所以一般new，我们一般推荐如下标准写法：

~~~C++
//标准写法：
try
{
    double *ptr = new double[1000000];
}
catch (std::bad_alloc &memExp)
{
    //失败以后，要么abort要么重分配
    cerr<<memExp.what()<<endl;
}
~~~



> 通常，我们在使用new进行内存分配的时候，会采用以下的处理方式：
>
> ~~~C++
> char *pStr = new string[SIZE];
> if (pStr == NULL)
> {
>     // Error processing
>     return false;
> }
> ~~~
>
> 
>
> 你能发现上述代码中存在的问题吗？这是一个隐蔽性极强的臭虫（Bug）。
>
> 我们沿用了C时代的良好传统：使用 malloc 等分配内存的函数时，一定要检查其返回值是否为“空指针”，并以此作为检查分配内存操作是否成功的依据，这种Test-for-NULL代码形式是一种良好的编程习惯，也是编写可靠程序所必需的。可是，这种完美的处理形式必须有一个前提：若new失败，其返回值必须是NULL。只有这样才能保证上述看似“逻辑正确、风格良好”的代码可以正确运行。
>
> 那么new失败后编译器到底是怎么处理的？在很久之前，即C++编译器的蛮荒时代，C++编译器保留了C编译器的处理方式：当operator new不能满足一个内存分配请求时，它返回一个NULL 指针。这曾经是对C的malloc函数的合理扩展。然而，随着技术的发展，标准的更新，编译器具有了更强大的功能，类也被设计得更漂亮，新时代的new在申请内存失败时具备了新的处理方式：抛出一个bad_alloc exception（异常）。所以，在新的标准里，上述Test-for-NULL处理方式不再被推荐和支持。



**3.new : 运算符、函数和替换函数**

​	运算符new和new[ ]分别调用如下函数：

~~~C++
void *operator new (std::size_t);
void *operator new[](std::size_t);
~~~

​	这些函数被称为分配函数，它们位于全局名称空间中。同样，也有由delete和delete[ ]调用的释放函数：

~~~C++
void operator delete(void *);
void operator delete[](void *);
~~~

​	

​	**4.定位new运算符**

​	通常，new负责在**堆（heap）**中找到一个足以能够满足要求的内存块。new运算符还有另一种变体，被称为**定位new运算符**，**它让您能够指定要使用的位置**。程序员可能使用这种特性来设置其内存管理规程、处理需要通过特定地址进行访问的硬件或在特定位置创建对象。

​	要使用定位new特性，首先需要包含**头文件new**，它提供了这种版本的new运算符的原型；然后将new运算符用于提供了所需地址的参数。除需要指定参数外，句法与常规new运算符相同。具体地说，使用定位new运算符时，变量后面可以由方括号，也可以没有。下面的代码段演示了new运算符的4种用法：

~~~C++
#include<new>
struct chaff
{
    char dross[20];
    int slag;
};
char buffer1[50];
char buffer2[500];
int main()
{
    chaff *p1, *p2;
    int *p3, *p4;
    //常规用法
    p1 = new chaff; //从堆中分配内存给结构
    p3 = new int[20]; //从堆中分配内存给数组
    
    //定位运算符
    p2 = new (buffer1) chaff;  //从buffer1中分配空间给结构chaff
    p4 = new (buffer2) int[20]; //从buffer2中分配空间给一个包含20个元素的int数组
 
}
~~~

​	

​	**5.定位new的其他形式**

​	就像常规new调用一个接收一个参数的new()函数一样，标准定位new调用一个接收两个参数的new()函数：

~~~C++
int *p1 = new int; //调用 new(sizeof(int))
int *p2 = new(buffer) int;  //调用 new(sizeof(int), buffer)
int *p3 = new(buffer) int[40];  //调用 new(40*sizeof(int), buffer)
~~~





## 3.名称空间

> **术语**
>
> ​	1.**`声明区域`**：是可以在其中进行声明的区域。例如，可以在函数外面声明全局变量，对于这种变量，其声明区域为其声明所在的文件。对于在函数中声明的变量，其声明区域为其声明所在的代码块。
>
> ​	2.**`潜在作用域`**：变量的潜在作用域从**声明点**开始，到其声明区域的结尾。因此潜在作用域比声明区域小，这是由于变量必须定义后才能使用。
>
> ​	然而，变量并非在其潜在作用域内的任何位置都是可见的。例如，它可能被另一个在嵌套声明区域中声明的同名变量隐藏。例如，在函数中声明的局部变量（对于这种变量，声明区域为整个函数）将隐藏在同一个文件中声明的全局变量（对于这种变量，声明区域为整个文件）。变量对程序而言可见的范围被称为**`作用域`**。



​	**1.新的名称空间特性**

​	C++新增了这样一种功能，即通过定义一种新的声明区域来创建命名的名称空间，这样做的目的之一是提供一个声明名称的区域。一个名称空间中的名称不会与另外一个名称空间的相同名称发生冲突，同时允许程序的其他部分使用该名称空间中声明的东西

~~~C++
namespace Jack{
    double pail;
    int pal;
    void fetch();
    struct well{...};
}

namespace Jill{
    double fetch;
    int pal;
    double bucket(double n){...};
    struct Hill{...};
}
~~~



​	名称空间可以是全局的，也可以位于另一个名称空间中，但不能位于代码块中。因此，在默认情况下，在名称空间中声明的名称的链接性为外部的（除非它引用了常量）。

​	除了用户定义的名称空间外，还存在另一个名称空间——**`全局名称空间`**。它对应于文件级声明区域，因此前面所说的全局变量现在被描述为位于全局名称空间中。

​	**任何名称空间中的名称都不会与其他名称空间中的名称发生冲突**。因此，Jack中的fetch可以与Jill中的fetch冲突，Jill中的Hill可以与外部Hill共存。**名称空间中的声明和定义规则同全局声明和定义规则相同。**

​	名称空间是开放的，即可以把名称加入到已有的名称空间中。例如，下面这条语句将名称goose添加到Jill中已有的名称列表中：

~~~C++
namespace Jill{
    char *goose(const char *);
}
~~~

​	同样，原来的Jack名称空间为fetch()函数提供了原型。可以在该文件后面（或另外一个文件中）再次使用Jack名称空间来提供该函数的代码：

~~~C++
namespace Jack{
    void fetch()
    {
        ...
    }
}
~~~

​	当然，需要有一种方法来访问给定名称空间中的名称。最简单的方法是，通过**作用域解析运算符**`::`，使用名称空间来限定该名称：

~~~C++
Jack::pail = 12.34;   //引用变量
Jill::Hill mole;	//引用结构
Jack::fetch();	//引用函数
~~~

​	未被装饰的名称（如pail）称为**`未限定的名称`**；包含名称空间的名称（如Jack::pail)  称为**`限定的名称`**。



​	**1.using声明和using编译指令**

​	我们并不希望每次使用名称时都对它进行限定，因此C++提供了两种机制（**`using声明`**和**`using编译指令`**）来简化对名称空间中名称的使用。**using声明**使特定的标识符可用，**using编译指令**使整个名称空间可用。

​	**using声明**由被限定的名称和它前面的关键字using组成：

~~~C++
using Jill::fetch;
~~~

​	**using声明**将特定的名称添加到它所属的声明区域中。例如main()中的using声明`Jill::fetch`将fetch添加到main()定义的声明区域中。完成该声明后，便可以使用名称fetch代替`Jill::fetch`。下面的代码段说明了这几点：

~~~C++
namespace Jill{
    double bucket(double n){...};
    double fetch;
    struct Hill{...};
}
char fetch;
int main()
{
    using Jill::fetch;	//将Jill里的fetch添加到本地命令空间
    double fetch;	//会编译报错，因为已经有一个本地的fetch
    cin >> fetch;	//读取一个值到Jill::fetch
    cin >> ::fetch;	//读取一个值到全局 char fetch；
}
~~~

​	由于**using声明**将名称添加到局部声明区域中，因此这个示例避免了将另一个局部变量也命名为fetch。另外，和其他局部变量一样，fetch也将覆盖同名的全局变量。

​	在函数的外面使用**using声明**时，将把名称添加到全局名称空间中：

~~~C++
void other();
namespace Jill{
    double bucket(double n){...};
    double fetch;
    struct Hill{...};
}
using Jill::fetch;	//将fetch添加到全局命名空间
int main()
{
    cin >> fetch;	//输入到Jill::fetch
    other();
...     
}

void other()
{
    cout << fetch;	//输出Jill::fetch
}
~~~

​	**using声明**使一个名称可用，而**using编译指令**使所有的名称都可用。**using编译指令**由名称空间名和它前面的关键字using namespace组成，它使名称空间中的所有名称都可用，而不需要使用作用域解析运算符：

~~~C++
using namespace Jack;
~~~

​	在全局声明区域中使用**using编译指令**，将使该名称空间的名称全局可用。这种情况已出现过多次：

~~~C++
#include <iostream>
using namespace std;
~~~

​	在函数中使用**using编译指令**，将使其中的名称在该函数中可用，下面是一个例子：

~~~C++
int main()
{
    using namespace Jack;
    
}
~~~

​	有关**using编译指令**和**using声明**，需要记住的一点是，它们增加了名称冲突的可能性。也就是说，如果有名称空间`Jack`和`Jill`，并在代码中使用作用域解析运算符，则不会存在二义性：

~~~C++
Jack::pal = 3;
Jill::pal = 10;
~~~

​	变量`Jack::pal`和`Jill::pal`是不同的标识符，表示不同的内存单元。然而，如果使用**using声明**，情况将发生变化：

~~~C++
using Jack::pal;
using Jill::pal;
pal = 4;  //这里编译会报错，因为不知道到底是哪一个pal
~~~

​	**事实上，编译器不允许您同时使用上述两个using声明，因为这将导致二义性。**



​	**2.using编译指令和using声明的区别**

​	使用**using编译指令**导入一个名称空间中所有的名称与使用多个**using声明**是不一样的，而更像是大量使用作用域解析运算符。使用**using声明**时，就好像声明了相应的名称一样。如果某个名称已经在函数中声明了，则不能用**using声明**导入相同的名称。然而，使用**using编译指令**时，将进行名称解析，就像在包含**using声明**和名称空间本身的最小声明区域中声明了名称一样。在下面的示例中，名称空间为全局的。如果使用**using编译指令**导入一个已经在函数中声明的名称，则局部名称将隐藏名称空间名，就像隐藏同名的全局变量一样。不过仍可以像下面的示例中那样使用作用域解析运算符：

~~~C++
namespace Jill{
    double bucket(double n){...};
    double fetch;
    struct Hill{...};
}
char fetch;	//全局命令空间
int main()
{
    using namespace Jill;	//导入Jill命名空间里的所有名称
    Hill Thrill;	//创建一个Jill::Hill结构
    double water = bucket(2);	//使用Jill::bucket()
    double fetch;	//不报错，使用的是隐藏名称 Jill::fetch
    cin >> fetch;	//给局部fetch变量赋值
    cin >> ::fetch;	//给全局fetch变量赋值
    ...
}
    
int foom()
{
    Hill top;	//编译报错
    Jill::Hill crest;	//合法的
}
~~~





​	



# 知识点

## 1.size_t

​	**std::size_t**是一个typedef；

​	32位编译器 std::size_t == unsigned;

​	64位编译器 std::size_t == unsigned long

​	为了使自己的程序有很好的移植性，c++程序员应该尽量使用size_t和size_type而不是int, unsigned