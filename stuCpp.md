# 1

## 命名空间

在C/C++中，变量、函数和类都是大量存在的，这些变量、函数和类的名称都将作用于全局作用域中，可能会导致很多命名冲突。使用命名空间的目的就是对标识符和名称进行本地化，以避免命名冲突或名字污染，namespace关键字的出现就是针对这种问题的。

定义命名空间，需要使用到namespace关键字，后面跟命名空间的名字，然后接一对{}即可，{}中即为命名空间的成员。一个命名空间就定义了一个新的作用域，命名空间中的所有内容都局限于该命名空间中。

```c++
namespace xjt    
{
	int printf = 1;
	int rand = 2;
	int Add(int a, int b)
	{
		return a + b;
	}
}
```

命名空间可以嵌套；同一个工程中允许存在多个相同名称的命名空间,编译器最后会合成同一个命名空间中。

### 作用域限定符

作用域限定符为两个冒号：：，其作用是通知编译器应从作用域限定符左侧的名字所示的作用域中寻找右侧那个名字，即指定访问哪个名字空间的哪个成员。

```c++
namespace my
{
    int cout = 1;
}
namespace my_1
{
    int cin = 2;
}
int test(){
    std::cout<<my_1::cin<<std::endl;
    return 0;
}

using namespace my;

int main()
{
    std::cout<<cout<<std::endl; // 1
    test();  // 2
}
```

```c++
#include <iostream>
namespace ns {
    int var = 0;
    void fun (void){
        int a = 10;
        std::cout << "a = " << a << std::endl;
    }
} 
int main(int argc, const char * argv[])
{
    
    std::cout << "ns::var = " << ns::var << std::endl;
    ns::fun();
    return 0;
}
```

## 输入输出

```c++
#include <iostream>
using namespace std;

int main() {
    int a = 1;
    float b = 2.1;
    double c= 2.111;
    char arr[10] = { 0 };
    char d[] = "hello world";
    /*
     * 在C++中使用cin和cout，需要包含头文件iostream以及std标准命名空间
     * cin是以遇到空格，tab或者换行符作为分隔符的，因此这儿输入hello world会被空格符分隔开来
     * arr只会是hello
     */
    cin >> arr;
    cout << arr << endl;
    cout << a << endl;
    cout << b << endl;
    cout << c << endl;
    cout << d << endl;  
    return 0;
}
```



## sizeof

```c++
    cout << "short 类型所占内存空间为： " << sizeof(short) << endl;
    cout << "int 类型所占内存空间为： " << sizeof(int) << endl;
    cout << "long 类型所占内存空间为： " << sizeof(long) << endl;
    cout << "long long 类型所占内存空间为： " << sizeof(long long) << endl;
//    short 类型所占内存空间为： 2
//    int 类型所占内存空间为： 4
//    long 类型所占内存空间为： 4
//    long long 类型所占内存空间为： 8
```

## 引用

引用不是新定义一个变量，而是给已存在变量取了一个别名，编译器不会为引用变量开辟内存空间，它和它引用的变量共用同一块内存空间。

引用在定义时必须初始化；一个变量可以有多个引用；引用一旦引用了一个实体，就不可以再引用其他实体。

```c++
int a = 1;
int&b = a; //相当于给a起了一个别名为b，int是b的类型
cout << a << endl;  // 1
cout << b << endl;  // 1
b = 3;  //改变b也就相当于改变了a
cout << b << endl;  // 3
cout << a << endl;  //3

 const int a = 10;
 //int& ra = a; // 该语句编译时会出错，a为常量
 const int& ra = a;
 // int& b = 10; // 该语句编译时会出错，b为常量
 const int& b = 10;
 double d = 12.34;
 //int& rd = d; // 该语句编译时会出错，类型不同
 const int& rd = d;  // 12 
```

引用是可以作为函数的返回值存在的。

```c++
int temp;
int fun1()
{
    temp = 10;
    return temp;
}

int& fun2()
{
    temp = 10;
    return temp;
}

int main()
{
    int a = 0, b = 0;
    // 1. 返回函数的普通类型
    a = fun1();
    // 2. 返回函数的引用
    b = fun2();
    // 3. 返回函数的引用去初始化一个新的引用
    int &c = fun2();

    cout << "a = " << a << endl;  //10
    cout << "b = " << b << endl;  //10
    cout << "c = " << c << endl;  //10

    return 0;
}
```

1. 返回函数的普通类型 

   返回普通类型对象其实是返回这个对象的拷贝，c++其实会创建一个临时变量，这个临时变量被隐藏了，它会把temp的值拷贝给这个临时变量，当执行语句“a = fun1()；”的时候就会把临时变量的值再拷贝给a，假设这个临时变量是t，相当于做了这两个赋值的步骤：t = temp; a = t;

2. 返回函数的引用

   返回引用实际返回的是一个指向返回值的隐式指针，在内存中不会产生副本，是直接将temp拷贝给a，这样就避免产生临时变量，相比返回普通类型的执行效率更高，而且这个返回引用的函数也可以作为赋值运算符的左操作数，但是这时候需要注意以下两个问题：

   1). 千万别返回局部对象的引用，当函数执行完局部对象也就被销毁，栈空间被释放，从而返 回的地址已经不存在，导致后面执行出错。（上面的例子temp是全局变量）

   2). 返回堆区对象的引用，这种情况要特别注意，这时候返回函数的引用是作为一个临时变量出现，没有将它赋值给一个实际存在的变量，那么这个堆区对象的内存空间就没有释放，可能造成内存泄漏。有人说这样做是非法的？其实不是绝对的，只能说这种编程习惯很不好，这样做只是容易造成内存泄漏，只是我们要记住，我们在申请堆内存以后必须记得去释放这块内存。

3. 返回函数的引用去初始化一个新的引用

   这个和前面一样，都是不会产生副本，但是现在是用返回值去初始化一个引用声明c，也就是说这时候变成了变量temp的别名，在c的生命周期内temp是一直有效的，这样做完全可以。



当引用出现在函数的参数列表中时：通过引用参数产生的效果同按地址传递是一样的，引用的语法更清楚简单。

```c++
//地址传递
void mySwap02(int* a, int* b) {
	int temp = *a;
	*a = *b;
	*b = temp;
}

//引用传递
void mySwap03(int& a, int& b) {
	int temp = a;
	a = b;
	b = temp;
}
```



### 常量引用

```c++
//int& ref = 10;  引用本身需要一个合法的内存空间，因此这行错误
//加入const就可以了，编译器优化代码，int temp = 10; const int& ref = temp;
const int& ref = 10;

//ref = 100;  //加入const后不可以修改变量
cout << ref << endl;
```

## 指针

与引用类似，指针也实现了对其他对象的间接访问。

- 指针本身就是一个对象，允许对指针赋值和拷贝，而且在生命周期内它可以先后指向不同的对象。
- 指针无须在定义时赋初值。和其他内置类型一样，在块作用域内定义的指针如果没有被初始化，也将拥有一个不确定的值。

通过将声明符写成`*d`的形式来定义指针类型，其中`d`是变量名称。如果在一条语句中定义了多个指针变量，则每个量前都必须有符号`*`



指针存放某个对象的地址，要想获取对象的地址，需要使用取地址符`&`。

```c++
int ival = 42;
int *p = &ival; // p holds the address of ival; p is a pointer to ival
```

因为引用不是对象，没有实际地址，所以不能定义指向引用的指针。

声明语句中指针的类型实际上被用于指定它所指向的对象类型。大部分情况下，指针的类型要和它指向的对象严格匹配。

指针的值（即地址）应属于下列状态之一：

- 指向一个对象。
- 指向紧邻对象所占空间的下一个位置。
- 空指针，即指针没有指向任何对象。
- 无效指针，即上述情况之外的其他值。

试图拷贝或以其他方式访问无效指针的值都会引发错误。

如果指针指向一个对象，可以使用解引用（dereference）符`*`来访问该对象。

```c++
int ival = 42;
int *p = &ival; // p holds the address of ival; p is a pointer to ival
cout << *p;     // * yields the object to which p points; prints 42
```

空指针

```c++
int *p1 = nullptr;  // equivalent to int *p1 = 0;
int *p2 = 0;        // directly initializes p2 from the literal constant 0
// must #include cstdlib
int *p3 = NULL;     // equivalent to int *p3 = 0;
```



### 指针与数组

a是一个一维数组，p是指针变量，且p=a；下面我们来访问一个数组元素 a[i]；

（1）数组下标法：a[i];

（2）指针下标法：p[i];  p里面已经放了数组的地址了，因此数组名和p 是等价的所以  p[i]与a[i]含义相同

（3）地址引用法：\*（a+i);  a表示的是下标为0 的元素的地址  (a+i) 即为a这个数组往后数第 i  个元素的地址 即第 i 个元素的地址  那么\*（a+i)相当于地址再加一个星号表示的就是这个地址对应的存储单元，或者说对应的对像即为 a[i]这个元素

（4）指针引用法：\*（p+i）；将a替换成p跟（3）含义相同



### 指针与字符串

```c++
char str[] = "C Language", * p = str;//p指向字符串的指针相当于p指向了str[0]
cout << p << endl;//输出：C Language  跟cout<<str<<endl；效果相同
cout << p + 2 << endl;//输出：Language 从字符L开始输出直到结束
cout << &str[7] << endl;//输出：age   从第7个元素开始输出
```

### 指针与函数

```c++
int add(int a,int b)		//函数的定义
{
	return a + b;
}

int (*function)(int,int); 	//函数指针的声明

function = add；		//给函数指针赋值
function = &add;		//跟上面是一样的

int c = function(1,2);	 	//跟函数名一样使用
int d = (*function)(1,2);	//跟上面的调用是一样的

```





## 结构

> 总结1：定义结构体时的关键字是struct，不可省略

> 总结2：创建结构体变量时，关键字struct可以省略

> 总结3：结构体变量利用操作符 ''.'' 访问成员

```c++
struct Book{
    char title[50];
    char author[50];
    char subject[100];
    int book_id;
};


int main() {
    Book book1;        // 定义结构体类型 Books 的变量 Book1
    Book book2;        // 定义结构体类型 Books 的变量 Book2
    // Book1 详述
    strcpy(book1.title, "C++ 教程");
    strcpy(book1.author, "Runoob");
    strcpy(book1.subject, "编程语言");
    book2.book_id = 12345;
    // Book2 详述
    strcpy(book2.title, "CSS 教程");
    strcpy(book2.author, "Runoob");
    strcpy(book2.subject, "前端技术");
    book2.book_id = 12346;
    // 输出 Book1 信息
    cout << "第一本书标题 : " << book1.title << endl;
    cout << "第一本书作者 : " << book1.author << endl;
    cout << "第一本书类目 : " << book1.subject << endl;
    cout << "第一本书 ID : " << book1.book_id << endl;
    // 输出 Book2 信息
    cout << "第二本书标题 : " << book2.title << endl;
    cout << "第二本书作者 : " << book2.author << endl;
    cout << "第二本书类目 : " << book2.subject << endl;
    cout << "第二本书 ID : " << book2.book_id << endl;
}

void printBook(struct Book book){
    cout<<book.book_id<<endl;
}
```

### typedef

```c++
typedef struct {
    char title[50];
    char author[50];
    char subject[100];
    int book_id;
} Books;
```

以直接使用 *Books* 来定义 *Books* 类型的变量，而不需要使用 struct 关键字。

### 结构体数组

**语法：**` struct  结构体名 数组名[元素个数] = {  {} , {} , ... {} }`

**示例：**

```c++
//结构体定义
struct student
{
	//成员列表
	string name;  //姓名
	int age;      //年龄
	int score;    //分数
}

int main() {
	
	//结构体数组
	struct student arr[3]=
	{
		{"张三",18,80 },
		{"李四",19,60 },
		{"王五",20,70 }
	};

	for (int i = 0; i < 3; i++)
	{
		cout << "姓名：" << arr[i].name << " 年龄：" << arr[i].age << " 分数：" << arr[i].score << endl;
	}

	return 0;
}
```

### 结构体做函数参数

**示例：**

```c++
//学生结构体定义
struct student {
    //成员列表
    string name;  //姓名
    int age;      //年龄
    int score;    //分数
};

//值传递
void printStudent(student stu) {
    stu.age = 28;
    cout << "子函数中 姓名：" << stu.name << " 年龄： " << stu.age << " 分数：" << stu.score << endl;
}

//地址传递
void printStudent2(student *stu) {
    stu->age = 28;
    cout << "子函数中 姓名：" << stu->name << " 年龄： " << stu->age << " 分数：" << stu->score << endl;
}

int main() {
    student stu = {"张三", 18, 100};

    //值传递
    printStudent(stu);
    cout << "主函数中 姓名：" << stu.name << " 年龄： " << stu.age << " 分数：" << stu.score << endl;
//    子函数中 姓名：张三 年龄： 28 分数：100
//    主函数中 姓名：张三 年龄： 18 分数：100

    //地址传递
    printStudent2(&stu);
    cout << "主函数中 姓名：" << stu.name << " 年龄： " << stu.age << " 分数：" << stu.score << endl;
//    子函数中 姓名：张三 年龄： 28 分数：100
//    主函数中 姓名：张三 年龄： 28 分数：100

    return 0;
}
```



> 总结：如果不想修改主函数中的数据，用值传递，反之用地址传递

## 内联函数

以`inline`修饰的函数叫做内联函数，编译时C++编译器会在调用内联函数的地方展开，没有函数压栈的开销，内联函数提升程序运行的效率。

```c++
inline int add(int x,int y){
    return x+y;
}

int main()
{
    int a = 0, b = 10;
    cout<<add(a,b)<<endl;
    return 0;
}
```

## auto

因此auto并非是一种“类型”的声明，而是一个类型声明时的“占位符”，编译器在编译期会将auto替换为变量实际的类型。

```c++
	int a = 10;
	auto b = &a;   //自动推导出b的类型为int*
	auto* c = &a;  //自动推导出c的类型为int*
	auto& d = a;   //自动推导出d的类型为int
	//打印变量b,c,d的类型
	cout << typeid(b).name() << endl;//打印结果为int*
	cout << typeid(c).name() << endl;//打印结果为int*
	cout << typeid(d).name() << endl;//打印结果为int

```

auto不能作为函数形参的类型；auto不能用来直接声明数组

```c++
int array[5] = {1,2,3,4,5};
for (auto&e:array){
    e*=2;
}
for (auto e:array){
    cout<<e<<endl;
}
```

## 类型别名

类型别名是某种类型的同义词，传统方法是使用关键字`typedef`定义类型别名。

```c++
typedef double wages;   // wages is a synonym for double
typedef wages base, *p; // base is a synonym for double, p for double*
```



C++11使用关键字`using`进行别名声明（alias declaration），作用是把等号左侧的名字规定成等号右侧类型的别名。

```c++
using SI = Sales_item; // SI is a synonym for Sales_item
```

### using

C中常用typedef A B这样的语法，将B定义为A类型，也就是给A类型一个别名B

对应typedef A B，使用using B=A可以进行同样的操作。

```c++
typedef vector<int> V1; 
using V2 = vector<int>;
```



## string



```c++
    string string1 = "abc";
    string string2(string1);
    string string3("abc");
    string string4(3,'a');  //aaa


    cout<<string1.empty()<<endl;//0
    cout<<string2.size()<<endl;//3 等价于 string2.length()
    cout<<string3+string4<<endl;//abcaaa
    cout<<string1[1]<<endl;//b

    string1+='d';
    string1.append(string2);
    cout<<string2.at(2)<<endl;

    string a = "abc";
    string b = "bc";
    int pos = a.find(b) ;
    string c = a.substr(2,1);
    
    if(islower(string1.at(3))){
        cout<<"yes";
    }
```



### string转int

可以使用C++标准库中的`stoi`函数将字符串转换为整数。例如：

```c++
#include <iostream>
#include <string>

using namespace std;

int main() {
    string str = "123";
    int num = stoi(str);
    cout << num << endl; // 输出123
    return 0;
}
```

### int转string

我们可以使用C++标准库中的std::to_string(), 这个是自11年以来添加到C++标准库中的。如果项目使用C++ 11 和之后的标准，建议使用这个方法。

```cpp
std::string to_string( int value );
```



## 数组

相当于vector的低级版，长度固定。

- 初始化：`char input_buffer[buffer_size];`，长度必须是const表达式，或者不写，让编译器自己推断。

- 数组不允许直接赋值给另一个数组。

- 用数组初始化 `vector`： `int a[] = {1,2,3,4,5}; vector<int> v(begin(a), end(a));`

- 使用数组时，编译器一般会把它转换成指针。

- 标准库类型限定使用的下标必须是无符号类型，而内置的下标可以处理负值。

- **指针访问数组**：在表达式中使用数组名时，名字会自动转换成指向数组的第一个元素的指针。

- 字符数组以`\0`结束

- **多维数组的初始化**： `int ia[3][4] = {{0,1,2,3}, ...}`。

- 使用范围for语句时，除了最内层的循环外，其他所有循环的控制变量都应该是**引用**类型。

- 允许定义动态数组

  ```c++
  int *a = new int[x];
  delete []a;
  ```

### 数组定义

```c++
//定义方式1
int score[10];
//利用下标赋值
score[0] = 100;
score[1] = 99;
score[2] = 85;

//第二种定义方式
//数据类型 数组名[元素个数] =  {值1，值2 ，值3 ...};
//如果{}内不足10个数据，剩余数据用0补全
int score2[10] = { 100, 90,80,70,60,50,40,30,20,10 };

//定义方式3
//数据类型 数组名[] =  {值1，值2 ，值3 ...};
int score3[] = { 100,90,80,70,60,50,40,30,20,10 };
```



## 表达式

[Cpp_Primer_Practice/ch04.md at master · applenob/Cpp_Primer_Practice (github.com)](https://github.com/applenob/Cpp_Primer_Practice/blob/master/notes/ch04.md)



## 函数

- **函数声明**：函数的声明和定义唯一的区别是声明无需函数体，用一个分号替代。函数声明主要用于描述函数的接口，也称**函数原型**。
- **在头文件中进行函数声明**：建议变量在头文件中声明；在源文件中定义。

- 通过使用引用形参，允许函数改变一个或多个实参的值。
- 引用形参直接关联到绑定的对象，而非对象的副本。
- 使用引用形参可以用于**返回额外的信息**。
- 经常用引用形参来避免不必要的复制。
- `void swap(int &v1, int &v2)`
- 如果无需改变引用形参的值，最好将其声明为常量引用。



- **不要返回局部对象的引用或指针**。
- **列表初始化返回值**：函数可以返回花括号包围的值的列表。（`C++11`）
- **主函数main的返回值**：如果结尾没有`return`，编译器将隐式地插入一条返回0的`return`语句。返回0代表执行成功。

### 函数的分文件编写

函数分文件编写一般有4个步骤

1. 创建后缀名为.h的头文件
2. 创建后缀名为.cpp的源文件
3. 在头文件中写函数的声明
4. 在源文件中写函数的定义

```c++
//swap.h文件
#include<iostream>
using namespace std;

//实现两个数字交换的函数声明
void swap(int &a, int &b);
```

```c++
//swap.cpp文件
#include "swap.h"

void swap(int &a, int &b)
{
	int temp = a;
	a = b;
	b = temp;

	cout << "a = " << a << endl;
	cout << "b = " << b << endl;
}
```

```c++
//main函数文件
#include "swap.h"
int main() {

	int a = 100;
	int b = 200;
	swap(a, b);

	return 0;
}
```

### constexpr函数

- 指能用于常量表达式的函数。
- `constexpr int new_sz() {return 42;}`
- **函数的返回类型及所有形参类型都要是字面值类型。**
- `constexpr`函数应该在头文件中定义。

### 缺省参数

缺省参数是声明或定义函数时为函数的参数指定一个默认值。在调用该函数时，如果没有指定实参则采用该默认值，否则使用指定的实参。

### 函数重载

函数重载:是函数的一种特殊情况，C++允许在同一作用域中声明几个功能类似的同名函数，这些同名函数的形参列表(参数个数 或 类型 或 顺序)必须不同，常用来处理实现功能类似数据类型不同的问题。



### 可变形参

#### 可变参数宏

1. 函数原型中使用省略号；
2. 函数定义中创建一个va_list变量；
3. 初始化va_list变量；
4. 访问参数列表；
5. 完成清理工作；

```C++
/* --sum.cpp-- 可变参数宏实现求任意个整形值得和 */
#include <stdarg.h>

int sum(int count, ...);    　　//原型中使用省略号

int sum(int count, ...){    　　//count 表示可变参数个数
    va_list ap;　　　　　　　　　　//声明一个va_list变量
    va_start(ap, count);　　 　　//初始化，第二个参数为最后一个确定的形参

    int sum = 0;  
    for(int i = 0; i < count; i++)          
        sum += va_arg(ap, int); //读取可变参数，的二个参数为可变参数的类型

    va_end(ap);          　　　　//清理工作 
    return sum;
}
```

使用这种方法需要注意一下几点：
　　1. 函数原型中，省略号必须在参数列表的末尾：也就是说，在函数原型中参数列表省略号的右边不能再出现确定参数；
　　2. 运行时，函数必须能够根据已有信息（既有约定，或确定实参）确定可变参数的具体个数与类型：函数定义需要知道可变参数的具体类型、个数，这些信息是在运行时确定的，那么显然应该由实参来确定。在上面的例子中count传递了可变参数的个数，而参数类型则是既有约定（整形）；
　　3. 使用完成时需要用va_end()做清理工作，可变参数宏可能使用了动态分配的内存，忘记执行清理操作有可能导致内存泄漏等问题；
　　4. 可变参数宏只能实现顺序访问可变参数，无法后退访问，但是可以在清理操作完成后重新使用va_start初始化va_list变量，重新遍历形参表；
　　5. 该方法是极不安全的，宏本身无法提供任何安全性保证，他总是按照既定代码“自作多情”的认为实参就应该是那么多，即使实参并不是那么多。这就要求所有安全性必须由程序员来保证。例如，在以上的示例代码中，如果调用时指定count为10，但实际上只给出9个可变形参，那么函数还是会读取10个参数，显然第十次读取是多余的，多余的操作一般不会有什么好结果，当然如果实参过多，多余的实参也不会被读取而是被忽略。

　　使用这种方法的一个实例是printf()函数。printf()函数通过分析第一个字符串参数中的占位符个数来确定形参的个数；通过占位符的不同来确定参数类型（%d表示int类型、%s表示char *）；它也有上述提到的安全问题，如果不小心少提供了个实参，那么越界访问就会发生。

#### initializer_list标准库类型

1. 函数原型中使用实例化**initializer_list**模板代表可变参数列表；
2. 使用迭代器访问initializer_list中的参数；
3. 传入实参写在{}之内。
4. 与vector等容器一样initializer_list也支持**begin()和end()**操作，返回指向首元素的迭代器和尾后迭代器。
5.  函数原型initializer_list与普通形参无异。这表明形参列表中可以包含其他类型参数且位置不限

```c++
/* --sum.cpp-- 利用initializer_list模板实现求人一个整形值得和 */
#include <initializer_list>

int sum(initializer_list<int> il);  //函数原型用int实例化initializer_list作为形参

int sum(inttializer_list<int> il){
    int sum = 0;
    for(auto p = il.begin(); p != il.end(); p++)   //使用迭代器访问参数
        sum += *p;
    return sum;
}
```

### lambda表达式

Lambda表达式的基本语法如下：

```
[capture clause] (parameter list) ->return type {function body}
```

这是一个基本的lambda表达式的例子：

~~~c++
#include <iostream>
using namespace std;

void for_each(int* arr, int len, void(*func)(int))
{
    for(int i = 0; i < len; ++i)
        func(arr[i]);
}

int main()
{
    int arr[] = {1, 2, 3, 4, 5};
    int len = sizeof(arr) / sizeof(int);

    /* 使用Lambda表达式作为回调函数 */
    for_each(arr, len, [](int x){
        cout << x << " ";
    });

    return 0;
}
~~~

首先，看一下捕获列表：**引用捕获和值捕获不同的一点就是，对应的成员是否为引用类型。**

**[]** 什么也不捕获，无法lambda函数体使用任何表达式外的变量

**[=]** 按值的方式捕获所有变量

**[&]** 按引用的方式捕获所有变量

**[=, &a]** 除了变量a之外，按值的方式捕获所有局部变量，变量a使用引用的方式来捕获。这里可以按引用捕获多个，例如 [=, &a, &b,&c]。这里注意，如果前面加了=，后面加的具体的参数必须以引用的方式来捕获，否则会报错。

**[&, a]** 除了变量a之外，按引用的方式捕获所有局部变量，变量a使用值的方式来捕获。这里后面的参数也可以多个，例如 [&, a, b, c]。这里注意，如果前面加了&，后面加的具体的参数必须以值的方式来捕获。

**[a, &b]** 以值的方式捕获a，引用的方式捕获b，也可以捕获多个。

**[this]** 在成员函数中，也可以直接捕获this指针，其实在成员函数中，[=]和[&]也会捕获this指针。

```C++
int a = 10;
int b = 20;

// 按值来捕获
auto func1 = [a] { cout << a << endl; };
func1();//10

// 按值来捕获
auto func2 = [=] { cout << a << " " << b << endl; };
func2();//10 20

// 按引用来捕获
auto func3 = [&a] {
    cout << a << endl;
    a = 0;
};
func3();//10

// 按引用来捕获
auto func4 = [&] { cout << a << " " << b << endl; };
func4();//0 20
```

从编译器的角度来看我们自己定义的lambda表达式：编译器会把我们写的lambda表达式翻译成一个类，并重载 operator()来实现。

```c++
int x = 1; int y = 2;
auto plus = [=] (int a, int b) -> int { return x + y + a + b; };
int c = plus(1, 2);

//从编译器的角度看，我们的lambda表达式会如下：
class LambdaClass{
public:
    LambdaClass(int xx, int yy): x(xx), y(yy) {}

    int operator () (int a, int b) const{
        return x + y + a + b;
    }

private:
    int x;
    int y;
}

int x = 1; int y = 2;
LambdaClass plus(x, y);
int c = plus(1, 2);
```

其实这里就可以看出，值捕获时，编译器会把捕获到的值作为类的成员变量，**并且变量是以值的方式传递的**

值的方式捕获是不可以在表达式内部修改捕获列表参数的，会报错，如果不适用引用方式的捕获，就加一个`mutable`

```C++
int x = 1; int y = 2;
auto plus = [&] (int a, int b) -> int { x++; return x + y + a + b;};
int c = plus(1, 2);
cout<<x<<endl;//2

auto plus1 = [=] (int a, int b) mutable -> int { x++; return x + y + a + b; };
int d = plus(1, 2);
cout<<x<<endl;//3
```

被`lambda`捕获的变量的值是在`lambda`创建时拷贝，而不是调用时拷贝。在`lambda`创建后修改局部变量不会影响`lambda`内对应的值。

```C++
size_t v1 = 42; // local variable
// copies v1 into the callable object named f
auto f = [v1] { return v1; };
v1 = 0;
auto j = f();   // j is 42; f stored a copy of v1 when we created it
```

## const

常类型是指使用类型修饰符**const**说明的类型，常类型的变量或对象的值是不能被更新的。

- 可以节省空间，避免不必要的内存分配
- const定义常量从汇编的角度来看，只是给出了对应的内存地址，而不是像`#define`一样给出的是立即数。
- const定义的常量在程序运行过程中只有一份拷贝，而`#define`定义的常量在内存中有若干个拷贝。

### const与指针

这里有两个概念需要区分：指针常量/常量指针

const右侧紧跟着的是指针还是常量, 是指针就是常量指针，是常量就是指针常量

**常量指针：指针指向可以改，指针指向的值不可以更改**

**指针常量：指针指向不可以改，指针指向的值可以更改**

```c++
int a = 10, b = 20;
int* const pc = &a;//pc是指针常量。
pc = &b;//错误，pc指向不可以更改
pc++;//错误，pc是只读的
*pc = 100;//正确，a被修改
//pc是指向int型对象的const指针
//不能使pc再被赋值指向其他对象，任何企图给const指针赋值的操作都会导致编译错误
//但是可以通过pc间接引用修改该对象的值

const char * pc1; //pc是常量指针。
char const * pc1; //同上
*pc1 = 10;//错误，不能通过ptr去修改所指对象的值。
//允许把非const对象的地址赋值给const对象的指针，如果要修改指针所指向的对象值，
//必须通过其他方式修改，不能直接通过当前指针直接修改。
const int *ptr;
int val = 3;
ptr = &val; //ok

const char * const pc2; //指向const对象的const指针
const int n = 1;
pc2 = &n;
```

### 函数中的const

（1）**const int**

```c++
const int func1();
```

这个本身无意义，因为参数返回本身就是赋值给其他的变量！

（2）**const int***

```c++
const int* func2();
```

指针指向的内容不变。

（3）int *const

```c++
int *const func2();
```

指针本身不可变。



（1）**参数指针所指内容为常量不可变**

```c++
void StringCopy(char *dst, const char *src);
```

其中src 是输入参数，dst 是输出参数。给src加上const修饰后，如果函数体内的语句试图改动src的内容，编译器将指出错误。这就是加了const的作用之一。

（2）**参数为引用，为了增加效率同时防止修改。**

```c++
void func(const A &a)
```

对于非内部数据类型的参数而言，像void func(A a) 这样声明的函数注定效率比较低。因为函数体内将产生A 类型的临时对象用于复制参数a，而临时对象的构造、复制、析构过程都将消耗时间。

为了提高效率，可以将函数声明改为void func(A &a)，因为“引用传递”仅借用一下参数的别名而已，不需要产生临 时对象。

> 但是函数void func(A &a) 存在一个缺点：
>
> “引用传递”有可能改变参数a，这是我们不期望的。解决这个问题很容易，加const修饰即可，因此函数最终成为 void func(const A &a)。

以此类推，是否应将void func(int x) 改写为void func(const int &x)，以便提高效率？完全没有必要，因为内部数据类型的参数不存在构造、析构的过程，而复制也非常快，“值传递”和“引用传递”的效率几乎相当。

> 小结：
> 1.对于非内部数据类型的输入参数，应该将“值传递”的方式改为“const 引用传递”，目的是提高效率。例如将void func(A a) 改为void func(const A &a)。
>
> 2.对于内部数据类型的输入参数，不要将“值传递”的方式改为“const 引用传递”。否则既达不到提高效率的目的，又降低了函数的可理解性。例如void func(int& x) 不应该改为void func(const int &x)。

### 类中的const

对于类中的const成员变量必须通过初始化列表进行初始化，如下所示：

```c++
class Apple{
private:
    int people[100];
public:
    Apple(int i); 
    const int apple_number;
};

Apple::Apple(int i):apple_number(i)
{

}
```



```c++
class Apple {
private:
    int people[100];
public:
    Apple(int i);

    const int apple_number;

    void take(int num) const;

    int add();

    int add(int num) const;

    int getCount() const;

};

// apple.cpp
// 定义构造函数，使用初始化列表初始化常量成员apple_number
Apple::Apple(int i) : apple_number(i) {
}

//定义成员函数add，使用常成员函数take
int Apple::add(int num) const {
    take(num);
    return 0;
}

void Apple::take(int num) const {
    std::cout << "take func " << num << std::endl;
}

int Apple::getCount() const {
    //const成员函数只能访问const成员函数。
    take(1);
    //    add(); //error
    return apple_number;
}

int main() {
    Apple a(2);
    cout << a.getCount() << endl;
    a.add(10);
    const Apple b(3);
    b.add(100);
    return 0;
}
```











# 2

## 类

- 成员函数的**声明**必须在类的内部。
- 成员函数的**定义**既可以在类的内部也可以在外部。
- 使用点运算符 `.` 调用成员函数。
- 必须对任何`const`或引用类型成员以及没有默认构造函数的类类型的任何成员使用初始化式。
- 成员函数作为内联函数
  - 在类的内部，常有一些规模较小的函数适合于被声明成内联函数。
  - 定义在类内部的函数是自动内联的。
  - 在类外部定义的成员函数，也可以在声明时显式地加上 `inline`。

- 访问说明符（access specifiers）：

  - `public`：定义在 `public`后面的成员在整个程序内可以被访问； `public`成员定义类的接口。
  - `private`：定义在 `private`后面的成员可以被类的成员函数访问，但不能被使用该类的代码访问； `private`隐藏了类的实现细节
  
- struct和`class`都可以被用于定义一个类。唯一的却别在于访问权限。

- 使用 `class`：在第一个访问说明符之前的成员是 `priavte`的。
- 使用 `struct`：在第一个访问说明符之前的成员是 `public`的。

- `this`
  - 每个成员函数都有一个额外的，隐含的形参`this`。
  - `this`总是指向当前对象，因此`this`是一个常量指针。
  - 形参表后面的`const`，改变了隐含的`this`形参的类型，如 `bool same_isbn(const Sales_item &rhs) const`，这种函数称为“常量成员函数”（`this`指向的当前对象是常量）。
  - `return *this;`可以让成员函数连续调用。
  - 普通的非`const`成员函数：`this`是指向类类型的`const`指针（可以改变`this`所指向的值，不能改变`this`保存的地址）。
  - `const`成员函数：`this`是指向const类类型的`const`指针（既不能改变`this`所指向的值，也不能改变`this`保存的地址）。

### 构造函数

- 类通过一个或者几个特殊的成员函数来控制其对象的初始化过程，这些函数叫做**构造函数**。
- 构造函数是特殊的成员函数。
- 构造函数放在类的`public`部分。
- 与类同名的成员函数。

### 类的实现

```C++
class Person {
public:
    Person(int age, string name) {
        this->age = age;
        this->name = name;
    }

    //在类内对成员函数进行声明
    void run() {
        cout << age << endl;
    }

    void play();

    friend void fun();//声明Person类的友元函数

private:
    int age;
    string name;
};

//通过作用域操作符::实现play函数
void Person::play() {
    cout << "play" << endl;
}

void fun() {
    Person person(2, "kk");
    cout << person.age << endl;
    //fun函数是一个普通的全局函数的定义，但其实是友元
    //友元函数的作用：使得指定的非成员函数能够访问类的私有成员。
}

int main() {
    Person p(1, "as");//创建对象
    p.play();
    p.run();
    fun();
}
```

### 委托构造函数

如果我们需要多个构造函数内部重复执行相同的代码逻辑，可以使用委托构造函数，委托构造函数是指在一个构造函数中调用其他构造函数来完成相同或相关的初始化工作。

被委托的构造函数在委托构造函数的初始化列表里被调用，而不是在委托构造函数的函数体里被调用。

委托构造函数的初始值列表中，只允许出现被委托的构造函数，而不能直接给成员变量进行初始化。

先执行被委托构造函数的初始化列表，然后执行被委托构造函数的函数体，最后返回执行委托构造函数的函数体。

```c++
class Person {
public:
    //委托构造函数
    Person(int age) : Person(age, "ben") {
        cout << "initialize_delegating constructor" << endl;
    }
    //普通的构造函数
    Person(int age, string name) : age(age), name(name) {
        cout << "initialize..." << endl;
    }
    void play();
    
private:
    int age;
    string name;
};

//通过作用域操作符::实现play函数
void Person::play() {
    cout << this->name << "  " << this->age << endl;
}


int main() {
    Person p(1, "aka");//创建对象
    Person p1(2);
    p.play();
    p1.play();
//    initialize...
//    initialize...
//    initialize_delegating constructor
//    aka  1
//    ben  2
}
```

### 析构函数

析构函数（destructor）是成员函数的一种，它的名字与类名相同，但前面要加`~`，没有参数和返回值。一个类有且仅有一个析构函数。如果定义类时没写析构函数，则编译器生成默认析构函数。如果定义了析构函数，则编译器不生成默认析构函数。

- 构造函数：主要作用在于创建对象时为对象的成员属性赋值，构造函数由编译器自动调用，无须手动调用。
- 析构函数：主要作用在于对象**销毁前**系统自动调用，执行一些清理工作。



- 析构函数，没有返回值也不写void
- 函数名称与类名相同,在名称前加上符号 ~
- 析构函数不可以有参数，因此不可以发生重载
- 程序在对象销毁前会自动调用析构，无须手动调用,而且只会调用一次

```c++
class String{
private:
    char* p;
public:
    String(int n);
    ~String();
};
String::~String(){
    delete[] p;
}
String::String(int n){
    p = new char[n];
}
```

析构函数在对象消亡时即自动被调用。可以定义析构函数在对象消亡前做善后工作。例如，对象如果在生存期间用 new 运算符动态分配了内存，则在各处写 delete 语句以确保程序的每条执行路径都能释放这片内存是比较麻烦的事情。有了析构函数，只要在析构函数中调用 delete 语句，就能确保对象运行中用 new 运算符分配的空间在对象消亡时被释放。

### 聚合类

聚合类是 C++ 中的一个特殊类，它没有用户定义的构造函数、析构函数、基类和虚函数，也没有私有成员，可以直接通过其成员名进行访问。聚合类的定义形式如下：

```c++
struct Classname {
    MemberType1 member1;
    MemberType2 member2;
    // ...
};
```

### 类的静态成员

static声明的称为静态成员。 不管这个类创建了多少个对象，静态成员只有一个拷贝，这个拷贝被所有属于这个类的对象共享。

使用`static` 修饰成员变量。静态变量，是在编译阶段就分配空间，对象还没有创建时，就已经分配空间。
**static静态成员变量不能在类的内部初始化。在类的内部只是声明，定义必须在类定义体的外部，通常在类的实现文件中初始化。**
静态数据成员不属于某个对象，在为对象分配空间中不包括静态成员所占空间。

如果一个类的成员，既要实现共享，又要实现不可改变，那就用 `static const` 修饰

```c++
class Person {
public:
    int get_data(){
        return data;
    }
    static int  getData_static(){
        return data;
    }

private:
    static int data;
};

int Person::data = 1;

int main() {
    Person p;
    cout<<p.get_data()<<endl;//普通成员函数 可以操作 静态成员数据 非静态成员数据。
    cout<<Person::getData_static()<<endl;//静态成员函数的目的 操作静态成员数据
}
```

### 深拷贝与浅拷贝

**浅拷贝只复制指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存。但深拷贝会另外创造一个一模一样的对象，新对象跟原对象不共享内存，修改新对象不会改到原对象。**

浅拷贝：简单的赋值拷贝操作

深拷贝：在堆区重新申请空间，进行拷贝操作



首先给出一个浅拷贝的例子：

```c++
class Test {
private:
    char *p;
public:
    Test(const char *str) {
        p = new char[strlen(str) + 1];
        strcpy(p, str);
        printf("Test cntr.%s\n", p);
    }

    ~Test() {
        printf("~Test cntr.%s\n", p);
        delete[]p;
        p = NULL;
    }
};

int main() {
//    我们的程序并没有手动实现类的拷贝构造函数，系统自动生成了缺省拷贝构造函数，只是对对象按位复制
//    1.p 和 t2.p指向的是同一块地址，所以构造函数只调用了一次，析构时先释放了 t2.p 指向的内存，
//     再析构 t1.p 时则要访问同一个已经被释放了的内存单元，这时则出现了内存管理错误

    Test t1("hello");
    Test t2 = t1;

//    Test cntr.hello
//    ~Test cntr.hello
//    ~Test cntr. f&╗

}
```

自行实现一个拷贝构造函数，可实现深拷贝

```c++
class Test {
private:
    char *p;
public:
    Test(const char *str) {
        p = new char[strlen(str) + 1];
        strcpy(p, str);
        printf("Test cntr.%s\n", p);
    }

    Test(const Test &t) {
        p = new char[strlen(t.p + 1)];
        strcpy(p, t.p);
        printf("Test copy.%s\n", p);
    }

    ~Test() {
        printf("~Test cntr.%s\n", p);
        delete[]p;
        p = NULL;
    }

};

int main() {
    Test t1("hello");
    Test t2(t1);

//    Test cntr.hello
//    Test copy.hello
//    ~Test cntr.hello
//    ~Test cntr.hello

}
```



### 运算符重载

类似于这种，以下实现了一个加号运算符重载。

```c++
class Person {
public:
    Person() {};

    Person(int a, int b) {
        this->m_A = a;
        this->m_B = b;
    }

    //成员函数实现 + 号运算符重载
    Person operator+(const Person &p) {
        Person temp;
        temp.m_A = this->m_A + p.m_A;
        temp.m_B = this->m_B + p.m_B;
        return temp;
    }


public:
    int m_A;
    int m_B;
};

//全局函数实现 + 号运算符重载
//Person operator+(const Person& p1, const Person& p2) {
// Person temp(0, 0);
// temp.m_A = p1.m_A + p2.m_A;
// temp.m_B = p1.m_B + p2.m_B;
// return temp;
//}

//运算符重载 可以发生函数重载
Person operator+(const Person &p2, int val) {
    Person temp;
    temp.m_A = p2.m_A + val;
    temp.m_B = p2.m_B + val;
    return temp;
}

void test() {

    Person p1(10, 10);
    Person p2(20, 20);

    //成员函数方式
    Person p3 = p2 + p1;  //相当于 p2.operaor+(p1)
    cout << "mA:" << p3.m_A << " mB:" << p3.m_B << endl;


    Person p4 = p3 + 10; //相当于 operator+(p3,10)
    cout << "mA:" << p4.m_A << " mB:" << p4.m_B << endl;

}

int main() {
    test();
    return 0;
}
```



## 继承

```c++
// 基类
class Animal {
public:
    void eat() {
        cout << "Animal is eating." << endl;
    }
};

// 派生类
class Dog : public Animal {
public:
    void bark() {
        cout << "Dog is barking." << endl;
    }
};

int main() {
    Dog dog;
    dog.eat();  // 调用基类函数
    dog.bark(); // 调用派生类函数
    return 0;
}
```

### 继承同名成员处理方式

- 访问子类同名成员 直接访问即可
- 访问父类同名成员 需要加作用域

```c++
class Base {
public:
    Base() {
        m_A = 100;
    }

    void func() {
        cout << "Base - func()调用" << endl;
    }

    void func(int a) {
        cout << "Base - func(int a)调用" << endl;
    }

public:
    int m_A;
};


class Son : public Base {
public:
    Son() {
        m_A = 200;
    }

    //当子类与父类拥有同名的成员函数，子类会隐藏父类中所有版本的同名成员函数
    //如果想访问父类中被隐藏的同名成员函数，需要加父类的作用域
    void func() {
        cout << "Son - func()调用" << endl;
    }

public:
    int m_A;
};

void test01() {
    Son s;

    cout << "Son下的m_A = " << s.m_A << endl;
    cout << "Base下的m_A = " << s.Base::m_A << endl;

    s.func();
    s.Base::func();
    s.Base::func(10);

}

int main() {

    test01();

    return EXIT_SUCCESS;
}
```

1. 子类对象可以直接访问到子类中同名成员
2. 子类对象加作用域可以访问到父类同名成员
3. 当子类与父类拥有同名的成员函数，子类会隐藏父类中同名成员函数，加作用域可以访问到父类中同名函数

###  继承同名静态成员处理方式

```c++
class Base {
public:
    static void func() {
        cout << "Base - static void func()" << endl;
    }

    static void func(int a) {
        cout << "Base - static void func(int a)" << endl;
    }

    static int m_A;
};

int Base::m_A = 100;

class Son : public Base {
public:
    static void func() {
        cout << "Son - static void func()" << endl;
    }

    static int m_A;
};

int Son::m_A = 200;

//同名成员属性
void test01() {
    //通过对象访问
    cout << "通过对象访问： " << endl;
    Son s;
    cout << "Son  下 m_A = " << s.m_A << endl;
    cout << "Base 下 m_A = " << s.Base::m_A << endl;

    //通过类名访问
    cout << "通过类名访问： " << endl;
    cout << "Son  下 m_A = " << Son::m_A << endl;
    cout << "Base 下 m_A = " << Son::Base::m_A << endl;
}

//同名静态成员函数
void test02() {
    //通过对象访问
    cout << "通过对象访问： " << endl;
    Son s;
    s.func();
    s.Base::func();

    cout << "通过类名访问： " << endl;
    Son::func();
    Son::Base::func();
    //出现同名，子类会隐藏掉父类中所有同名成员函数，需要加作作用域访问
    Son::Base::func(100);
}

int main() {
    test01();
    test02();

    return 0;
}
```

### 多继承

C++允许一个类继承多个类，多继承可能会引发父类中有同名成员出现，需要加作用域区分。

### 菱形继承&虚继承

两个派生类继承同一个基类，又有某个类同时继承者两个派生类。这种继承被称为菱形继承，或者钻石继承

- 菱形继承带来的主要问题是子类继承两份相同的数据，导致资源浪费以及毫无意义
- 利用虚继承可以解决菱形继承问题

```
class Animal {
public:
    int m_Age;
};

//继承前加virtual关键字后，变为虚继承
//此时公共的父类Animal称为虚基类
class Sheep : virtual public Animal {
};

class Tuo : virtual public Animal {
};

class SheepTuo : public Sheep, public Tuo {
};

int main() {
    SheepTuo sheepTuo;
    sheepTuo.Sheep::m_Age = 100;
    sheepTuo.Tuo::m_Age = 20;
    cout << sheepTuo.Sheep::m_Age << endl;//20
    cout << sheepTuo.Tuo::m_Age << endl;//20
    cout << sheepTuo.m_Age << endl;//20
}
```

Sheep和Tuo都采用的虚继承，因此SheepTuo中只保留了一个共同基类Animal的对象。

## 多态

多态分为两类

- 静态多态: 函数重载 和 运算符重载属于静态多态，复用函数名
- 动态多态: 派生类和虚函数实现运行时多态

静态多态和动态多态区别：

- 静态多态的函数地址早绑定 - 编译阶段确定函数地址
- 动态多态的函数地址晚绑定 - 运行阶段确定函数地址

```c++
class Animal {
public:
    //Speak函数就是虚函数
    //函数前面加上virtual关键字，变成虚函数，那么编译器在编译的时候就不能确定函数调用了。
    virtual void speak() {
        cout << "动物在说话" << endl;
    }
};

class Cat : public Animal {
public:
    void speak() {
        cout << "小猫在说话" << endl;
    }
};

class Dog : public Animal {
public:

    void speak() {
        cout << "小狗在说话" << endl;
    }

};
//我们希望传入什么对象，那么就调用什么对象的函数
//如果函数地址在编译阶段就能确定，那么静态联编
//如果函数地址在运行阶段才能确定，就是动态联编

void DoSpeak(Animal &animal) {
    animal.speak();
}
//
//多态满足条件：
//1、有继承关系
//2、子类重写父类中的虚函数
//多态使用：
//父类指针或引用指向子类对象

int main() {

    Cat cat;
    DoSpeak(cat);
    
    Dog dog;
    DoSpeak(dog);

    return 0;
}
```

### 多态方式实现计算器

```c++
class AbstractCalculator {
public :

    virtual int getResult() {
        return 0;
    }

    int m_Num1;
    int m_Num2;
};

//加法计算器
class AddCalculator : public AbstractCalculator {
public:
    int getResult() {
        return m_Num1 + m_Num2;
    }
};

//减法计算器
class SubCalculator : public AbstractCalculator {
public:
    int getResult() {
        return m_Num1 - m_Num2;
    }
};

//乘法计算器
class MulCalculator : public AbstractCalculator {
public:
    int getResult() {
        return m_Num1 * m_Num2;
    }
};

int main() {
	//创建加法计算器
    AbstractCalculator *abc = new AddCalculator;
    abc->m_Num1 = 10;
    abc->m_Num2 = 10;
    cout << abc->m_Num1 << " + " << abc->m_Num2 << " = " << abc->getResult() << endl;
    delete abc;  //用完了记得销毁

    //创建减法计算器
    abc = new SubCalculator;
    abc->m_Num1 = 10;
    abc->m_Num2 = 10;
    cout << abc->m_Num1 << " - " << abc->m_Num2 << " = " << abc->getResult() << endl;
    delete abc;

    //创建乘法计算器
    abc = new MulCalculator;
    abc->m_Num1 = 10;
    abc->m_Num2 = 10;
    cout << abc->m_Num1 << " * " << abc->m_Num2 << " = " << abc->getResult() << endl;
    delete abc;

    return 0;
}
```

### 纯虚函数&抽象类

在多态中，通常父类中虚函数的实现是毫无意义的，主要都是调用子类重写的内容

因此可以将虚函数改为**纯虚函数**

纯虚函数语法：`virtual 返回值类型 函数名 （参数列表）= 0 ;`

当类中有了纯虚函数，这个类也称为抽象类

**抽象类特点**：

- 无法实例化对象
- 子类必须重写抽象类中的纯虚函数，否则也属于抽象类

```c++
class Base
{
public:
	//纯虚函数
	//类中只要有一个纯虚函数就称为抽象类
	//抽象类无法实例化对象
	//子类必须重写父类中的纯虚函数，否则也属于抽象类
	virtual void func() = 0;
};

class Son :public Base
{
public:
	virtual void func() 
	{
		cout << "func调用" << endl;
	};
};

int main() {
	Base * base = NULL;
	//base = new Base; // 错误，抽象类无法实例化对象
	base = new Son;
	base->func();
	delete base;//记得销毁
}
```

### 多态案例

```c++
class AbstractDrinking {
public:
    //烧水
    virtual void Boil() = 0;

    //冲泡
    virtual void Brew() = 0;

    //倒入杯中
    virtual void PourInCup() = 0;

    //加入辅料
    virtual void PutSomething() = 0;

    //规定流程
    void MakeDrink() {
        Boil();
        Brew();
        PourInCup();
        PutSomething();
    }
};

//制作咖啡
class Coffee : public AbstractDrinking {
public:
    //烧水
    virtual void Boil() {
        cout << "煮农夫山泉!" << endl;
    }

    //冲泡
    virtual void Brew() {
        cout << "冲泡咖啡!" << endl;
    }

    //倒入杯中
    virtual void PourInCup() {
        cout << "将咖啡倒入杯中!" << endl;
    }

    //加入辅料
    virtual void PutSomething() {
        cout << "加入牛奶!" << endl;
    }
};

//制作茶水
class Tea : public AbstractDrinking {
public:
    //烧水
    virtual void Boil() {
        cout << "煮自来水!" << endl;
    }

    //冲泡
    virtual void Brew() {
        cout << "冲泡茶叶!" << endl;
    }

    //倒入杯中
    virtual void PourInCup() {
        cout << "将茶水倒入杯中!" << endl;
    }

    //加入辅料
    virtual void PutSomething() {
        cout << "加入枸杞!" << endl;
    }
};

//业务函数
void DoWork(AbstractDrinking *drink) {//抽象类是无法被实例化的
    drink->MakeDrink();
    delete drink;
}


int main() {
    DoWork(new Coffee);
    cout << "--------------" << endl;
    DoWork(new Tea);
}
```

### 虚析构&纯虚析构

多态使用时，如果子类中有属性开辟到堆区，那么父类指针在释放时无法调用到子类的析构代码

解决方式：将父类中的析构函数改为**虚析构**或者**纯虚析构**

虚析构和纯虚析构共性：

- 可以解决父类指针释放子类对象
- 都需要有具体的函数实现

虚析构和纯虚析构区别：

- 如果是纯虚析构，该类属于抽象类，无法实例化对象

虚析构语法：

```c++
virtual ~类名(){}
```

纯虚析构语法：

```c++
virtual ~类名() = 0;
类名::~类名(){}
```

 1. **虚析构或纯虚析构就是用来解决通过父类指针释放子类对象**

 2. 如果子类中没有堆区数据，可以不写为虚析构或纯虚析构

 3. 拥有纯虚析构函数的类也属于抽象类

```c++
class Animal {
public:

    Animal() {
        cout << "Animal 构造函数调用！" << endl;
    }

    virtual void Speak() = 0;

    //析构函数加上virtual关键字，变成虚析构函数
    //virtual ~Animal()
    //{
    // cout << "Animal虚析构函数调用！" << endl;
    //}


    virtual ~Animal() = 0;
};

Animal::~Animal() {
    cout << "Animal 纯虚析构函数调用！" << endl;
}

//和包含普通纯虚函数的类一样，包含了纯虚析构函数的类也是一个抽象类。不能够被实例化。

class Cat : public Animal {
public:
    Cat(string name) {
        cout << "Cat构造函数调用！" << endl;
        m_Name = new string(name);
    }

    virtual void Speak() {
        cout << *m_Name << "小猫在说话!" << endl;
    }

    ~Cat() {
        cout << "Cat析构函数调用!" << endl;
        if (this->m_Name != NULL) {
            delete m_Name;
            m_Name = NULL;
        }
    }

public:
    string *m_Name;
};

int main() {
    Animal *animal = new Cat("Tom");
    animal->Speak();

    //通过父类指针去释放，会导致子类对象可能清理不干净，造成内存泄漏
    //怎么解决？给基类增加一个虚析构函数
    //虚析构函数就是用来解决通过父类指针释放子类对象
    delete animal;
}
```





## 顺序容器

| 类型           | 特性                                                         |
| -------------- | ------------------------------------------------------------ |
| `vector`       | 可变大小数组。支持快速随机访问。在尾部之外的位置插入/删除元素可能很慢 |
| `deque`        | 双端队列。支持快速随机访问。在头尾位置插入/删除速度很快      |
| `list`         | 双向链表。只支持双向顺序访问。在任何位置插入/删除速度都很快  |
| `forward_list` | 单向链表。只支持单向顺序访问。在任何位置插入/删除速度都很快  |
| `array`        | 固定大小数组。支持快速随机访问。不能添加/删除元素            |
| `string`       | 类似`vector`，但用于保存字符。支持快速随机访问。在尾部插入/删除速度很快 |

- 除非有合适的理由选择其他容器，否则应该使用`vector`。
- 如果程序有很多小的元素，且空间的额外开销很重要，则不要使用`list`或`forward_list`。
- 如果程序要求随机访问容器元素，则应该使用`vector`或`deque`。
- 如果程序需要在容器头尾位置插入/删除元素，但不会在中间位置操作，则应该使用`deque`。
- 如果程序只有在读取输入时才需要在容器中间位置插入元素，之后需要随机访问元素。则：
  - 先确定是否真的需要在容器中间位置插入元素。当处理输入数据时，可以先向`vector`追加数据，再调用标准库的`sort`函数重排元素，从而避免在中间位置添加元素。
  - 如果必须在中间位置插入元素，可以在输入阶段使用`list`。输入完成后将`list`中的内容拷贝到`vector`中。
- 不确定应该使用哪种容器时，可以先只使用`vector`和`list`的公共操作：使用迭代器，不使用下标操作，避免随机访问。这样在必要时选择`vector`或`list`都很方便。





### 容器的初始化

**将一个容器初始化为另一个容器的拷贝时，两个容器的容器类型和元素类型都必须相同。**

**传递迭代器参数来拷贝一个范围时，不要求容器类型相同，而且新容器和原容器中的元素类型也可以不同，但是要能进行类型转换。**

基本上所有的容器初始化方式可参考：

| 方法                        | 解释                                                         |
| --------------------------- | ------------------------------------------------------------ |
| `vector<T> v1`              | `v1`是一个空`vector`，它潜在的元素是`T`类型的，执行默认初始化 |
| `vector<T> v2(v1)`          | `v2`中包含有`v1`所有元素的副本                               |
| `vector<T> v2 = v1`         | 等价于`v2(v1)`，`v2`中包含`v1`所有元素的副本                 |
| `vector<T> v3(n, val)`      | `v3`包含了n个重复的元素，每个元素的值都是`val`               |
| `vector<T> v4(n)`           | `v4`包含了n个重复地执行了值初始化的对象                      |
| `vector<T> v5{a, b, c...}`  | `v5`包含了初始值个数的元素，每个元素被赋予相应的初始值       |
| `vector<T> v5={a, b, c...}` | 等价于`v5{a, b, c...}`                                       |

```c++
// 将一个容器初始化为另一容器的副本
list<string> authors = {"Milton", "Shakespeare", "Austen"};
vector<const char*> articles = {"a", "an", "the"};
list<string> list2(authors);        // ok: types match
deque<string> authList(authors);    // error: container types don't match
vector<string> words(articles);     // error: element types must match

//传递迭代器参数
forward_list<string> words(articles.begin(), articles.end());//虽然元素类型不同，但能进行类型转换

//初始化为一段元素的副本
vector<string> svec;
list<string> slist(svec.begin(), svec.end());
vector<string>::iterator mid = svec.begin() + svec.size()/2;
deque<string> front(svec.begin(), mid);
deque<string> back(mid, svec.end());

// 直接初始化方式
list<string> authors = {"Milton", "Shakespeare", "Austen"};
//list<string> authors{"Milton", "Shakespeare", "Austen"};
vector<const char*> articles = {"a", "an", "the"};

//带有容器大小的初始化
vector<int> v1(5); // 带有容器大小的初始化，v1中包含5个元素，但元素的值未指定
list<string> l1(3); // 带有容器大小的初始化，l1中包含3个元素，但元素的值未指定

//带有容器大小和元素值的初始化
vector<int> v1(5, 0); // 带有容器大小和元素值的初始化，v1中包含5个值为0的元素
list<string> l1(3, "hello"); // 带有容器大小和元素值的初始化，l1中包含3个值为"hello"的元素

//定义和使用array类型时，需要同时指定元素类型和容器大小
array<int, 42>      // type is: array that holds 42 ints
array<string, 10>   // type is: array that holds 10 strings
```



### 赋值&swap

```c++
list<string> names;
vector<const char*> oldstyle;
names = oldstyle;   // error: container types don't match
names.assign(oldstyle.cbegin(), oldstyle.cend());
```

`swap`交换两个相同类型容器的内容。除`array`外，`swap`不对任何元素进行拷贝、删除或插入操作，只交换两个容器的内部数据结构，因此可以保证快速完成。



对于`array`，`swap`会真正交换它们的元素。因此在`swap`操作后，指针、引用和迭代器所绑定的元素不变，但元素值已经被交换。

对于其他容器类型（除`string`），指针、引用和迭代器在`swap`操作后仍指向操作前的元素，但这些元素已经属于不同的容器了。

```c++
vector<int> a = { 1, 2, 3 };
vector<int> b = { 4, 5, 6 };
auto p = a.cbegin(), q = a.cend();
a.swap(b);
// 输出交换前的值，即1、2、3
while (p != q)
{
    cout << *p << endl;
    ++p;
}	
```



### 两个容器的比较

两个容器的比较实际上是元素的逐对比较，其工作方式与`string`的关系运算符类似：

- 如果两个容器大小相同且所有元素对应相等，则这两个容器相等。
- 如果两个容器大小不同，但较小容器中的每个元素都等于较大容器中的对应元素，则较小容器小于较大容器。
- 如果两个容器都不是对方的前缀子序列，则两个容器的比较结果取决于第一个不等元素的比较结果。

```c++
vector<int> v1 = { 1, 3, 5, 7, 9, 12 };
vector<int> v2 = { 1, 3, 9 };
vector<int> v3 = { 1, 3, 5, 7 };
vector<int> v4 = { 1, 3, 5, 7, 9, 12 };
v1 < v2     // true; v1 and v2 differ at element [2]: v1[2] is less than v2[2]
v1 < v3     // false; all elements are equal, but v3 has fewer of them;
v1 == v4    // true; each element is equal and v1 and v4 have the same size()
v1 == v2    // false; v2 has fewer elements than v1
```



### 访问元素

每个顺序容器都有一个`front`成员函数，而除了`forward_list`之外的顺序容器还有一个`back`成员函数。这两个操作分别返回首元素和尾元素的引用。

在调用`front`和`back`之前，要确保容器非空。

在容器中访问元素的成员函数都返回引用类型。如果容器是`const`对象，则返回`const`引用，否则返回普通引用。

可以快速随机访问的容器（`string`、`vector`、`deque`和`array`）都提供下标运算符。保证下标有效是程序员的责任。如果希望确保下标合法，可以使用`at`成员函数。`at`类似下标运算，但如果下标越界，`at`会抛出`out_of_range`异常。



### 增删操作

不同容器的增加元素操作略有不同：

vector中插入主要是使用push_back()，也可使用insert()；

对于deque来说，插入和删除时一个亮眼的地方，因为首尾均可操作，有头插push_front()，头删pop_front()，尾插push_back()，尾删pop_back()，以及inset()插入和erase()删除；

对于list来说，可以理解为双向循环列表，因此插入方式有：`push_back(elem)`，`push_front(elem)`，`insert(pos,elem)`,`insert(pos,n,elem)`,`insert(pos,beg,end)`。其删除方式有多的一种是`remove(elem)`，删除容器中与elem值匹配的元素。





`c.erase(p)`          

 删除迭代器p指向的元素。返回一个迭代器，它指向被删除元素后面的元素。如果p指向容器内最后一个元素，则返回的迭代器指向容器的超出末端的下一位置 如果p本身就是超出容器末端的下一个位置，则该函数未定义

`c.erase(b,e)`         

删除迭代器b和e所标记的范围内所有元素。返回一个迭代器，它指向被删除元素段后面的元素。如果e本身就是指向超出容器末端的下一个位置，则返回的迭代器也指向容器末端的下一个位置

`c.clear()`            

删除容器c内的所有元素。返回void

`c.pop_back()`         

删除容器c的最后一个元素。返回void。如果容器为空，则该函数未定义

`c.pop_front()`        

删除容器c的第一个元素。返回void。如果容器为空，则该函数未定义。只适用于list和deque容器





### 迭代器

- 所有标准库容器都可以使用迭代器。
- 类似于指针类型，迭代器也提供了对对象的间接访问。

一个迭代器范围（iterator range）由一对迭代器表示。这两个迭代器通常被称为`begin`和`end`，分别指向同一个容器中的元素或尾后地址。`end`迭代器不会指向范围中的最后一个元素，而是指向尾元素之后的位置。这种元素范围被称为左闭合区间（left-inclusive interval），其标准数学描述为`[begin，end）`。迭代器`begin`和`end`必须指向相同的容器，`end`可以与`begin`指向相同的位置，但不能指向`begin`之前的位置（由程序员确保）。

- 如果`begin`等于`end`，则范围为空。
- 如果`begin`不等于`end`，则范围内至少包含一个元素，且`begin`指向该范围内的第一个元素。
- 可以递增`begin`若干次，令`begin`等于`end`。

`begin`和`end`操作生成指向容器中第一个元素和尾后地址的迭代器。其常见用途是形成一个包含容器中所有元素的迭代器范围。

`begin`和`end`操作有多个版本：带`r`的版本返回反向迭代器。以`c`开头的版本（C++11新增）返回`const`迭代器。不以`c`开头的版本都是重载的，当对非常量对象调用这些成员时，返回普通迭代器，对`const`对象调用时，返回`const`迭代器。

```c++
list<string> a = {"Milton", "Shakespeare", "Austen"};
auto it1 = a.begin();    // list<string>::iterator
auto it2 = a.rbegin();   // list<string>::reverse_iterator
auto it3 = a.cbegin();   // list<string>::const_iterator
auto it4 = a.crbegin();  // list<string>::const_reverse_iterator
```



#### 使用迭代器

- `vector<int>::iterator iter`。
- `auto b = v.begin();`返回指向第一个元素的迭代器。
- `auto e = v.end();`返回指向最后一个元素的下一个（尾后,one past the end）的迭代器（off the end）。
- 如果容器为空， `begin()`和 `end()`返回的是同一个迭代器，都是尾后迭代器。
- 使用解引用符`*`访问迭代器指向的元素。
- 养成使用迭代器和`!=`的习惯（泛型编程）。
- **容器**：可以包含其他对象；但所有的对象必须类型相同。
- **迭代器（iterator）**：每种标准容器都有自己的迭代器。`C++`倾向于用迭代器而不是下标遍历元素。
- `const_iterator`：只能读取容器内元素不能改变。
- **箭头运算符**： 解引用 + 成员访问，`it->mem`等价于 `(*it).mem`
- **谨记**：但凡是使用了**迭代器**的循环体，都**不要**向迭代器所属的容器**添加元素**。

```C++
	vector<int> v;  //v是存放int类型变量的可变长数组，开始时没有元素
    for (int n = 0; n<5; ++n)
        v.push_back(n);  //push_back成员函数在vector容器尾部添加一个元素
    vector<int>::iterator i;  //定义正向迭代器
    for (i = v.begin(); i != v.end(); ++i) {  //用迭代器遍历容器
        cout << *i << " ";  //*i 就是迭代器i指向的元素
        *i *= 2;  //每个元素变为原来的2倍
    }
    cout << endl;
    //用反向迭代器遍历容器
    for (vector<int>::reverse_iterator j = v.rbegin(); j != v.rend(); ++j)
        cout << *j << " ";
```

| 运算符               | 解释                                                         |
| -------------------- | ------------------------------------------------------------ |
| `iter + n`           | 迭代器加上一个整数值仍得到一个迭代器，迭代器指示的新位置和原来相比向前移动了若干个元素。结果迭代器或者指示容器内的一个元素，或者指示容器尾元素的下一位置。 |
| `iter - n`           | 迭代器减去一个整数仍得到一个迭代器，迭代器指示的新位置比原来向后移动了若干个元素。结果迭代器或者指向容器内的一个元素，或者指示容器尾元素的下一位置。 |
| `iter1 += n`         | 迭代器加法的复合赋值语句，将`iter1`加n的结果赋给`iter1`      |
| `iter1 -= n`         | 迭代器减法的复合赋值语句，将`iter2`减n的加过赋给`iter1`      |
| `iter1 - iter2`      | 两个迭代器相减的结果是它们之间的距离，也就是说，将运算符右侧的迭代器向前移动差值个元素后得到左侧的迭代器。参与运算的两个迭代器必须指向的是同一个容器中的元素或者尾元素的下一位置。 |
| `>`、`>=`、`<`、`<=` | 迭代器的关系运算符，如果某迭代器                             |
| `*iter`              | 返回迭代器`iter`所指向的**元素的引用**                       |





### 容器适配器

标准库定义了`stack`、`queue`和`priority_queue`三种容器适配器。容器适配器可以改变已有容器的工作机制。

| 容器           | 简介说明                                                     |
| -------------- | ------------------------------------------------------------ |
| stack          | 堆栈。其原理是先进后出（FILO），其底层容器可以是任何标准的容器适配器，默认为deque双端队列 |
| queue          | 队列。其原理是先进先出（FIFO），只有队头和队尾可以被访问，故不可有遍历行为，默认也为deque双端队列 |
| pirority_queue | 优先队列。它的第一个元素总是它所包含的元素中优先级最高的，就像数据结构里的堆，会默认形成大堆，还可以使用仿函数来控制生成大根堆还是生成小根堆，若没定义，默认使用vector容器 |

默认情况下，`stack`和`queue`是基于`deque`实现的，`priority_queue`是基于`vector`实现的。可以在创建适配器时将一个命名的顺序容器作为第二个类型参数，来重载默认容器类型。

```c++
// empty stack implemented on top of vector
stack<string, vector<string>> str_stk;
// str_stk2 is implemented on top of vector and initially holds a copy of svec
stack<string, vector<string>> str_stk2(svec);
```

#### stack

```c++
stack<int> s;

s.push(10);
s.push(20);
s.push(30);
s.push(40); 

cout << "栈的大小\t栈顶元素" << endl;
while (!s.empty()) {
    cout << s.size() << "\t\t" << s.top() << endl;
    s.pop();    
}

cout << "出栈后的大小为：" << s.size() << endl;
```



#### queue

1、queue可以访问两端但是只能修改队头，而deque可以访问两端并且可以在队首和队尾删除和插入元素

2、deque可以从两端入队，但是queue只能从队尾入队，

3、对于弹出队内元素，deque拥有pop_front(删除队头元素)以及pop_back(删除队尾元素)

`push()`就是入队

`pop()`便是出队，

`empty()`判断容器是否为空，

`size()`是返回其大小，

`front()`是返回队首元素

`back()`是返回队尾元素



#### priority_queue

```C++
priority_queue<Type, Container, Functional>
    
//升序队列（小顶堆）- 优先输出最小的
priority_queue <int,vector<int>,greater<int> > q;
//降序队列（大顶堆）- 优先输出最大的[默认]
priority_queue <int,vector<int>,less<int> >q;
```

```C++
priority_queue<int> q;

q.push(9);
q.push(2);
q.push(7);
q.push(3);
q.push(-8);
q.push(1);

while (!q.empty())
{
    cout << q.top() << " ";
    q.pop();
}
cout<<endl;//9 7 3 2 1 -8

priority_queue<int,vector<int>,greater<int>> q;
q.push(9);
q.push(2);
q.push(7);
q.push(3);
q.push(-8);
q.push(1);

while (!q.empty())
{
    cout << q.top() << " ";
    q.pop();
}
cout<<endl;//-8 1 2 3 7 9

```



## 泛型算法

### 只读算法

`accumulate`函数（定义在头文件`numeric`中）用于计算一个序列的和。它接受三个参数，前两个参数指定需要求和的元素范围，第三个参数是和的初值（决定加法运算类型和返回值类型）。**建议在只读算法中使用`cbegin`和`cend`函数。**

```c++
// sum the elements in vec starting the summation with the value 0
int sum = accumulate(vec.cbegin(), vec.cend(), 0);
string sum = accumulate(v.cbegin(), v.cend(), string(""));
// error: no + on const char*
string sum = accumulate(v.cbegin(), v.cend(), "");
```

`equal`函数用于确定两个序列是否保存相同的值。它接受三个迭代器参数，前两个参数指定第一个序列范围，第三个参数指定第二个序列的首元素。`equal`函数假定第二个序列至少与第一个序列一样长。

```c++
// roster2 should have at least as many elements as roster1
equal(roster1.cbegin(), roster1.cend(), roster2.cbegin());
```

只接受单一迭代器表示第二个操作序列的算法都假定第二个序列至少与第一个序列一样长。

### 写容器元素的算法

`fill`函数接受两个迭代器参数表示序列范围，还接受一个值作为第三个参数，它将给定值赋予范围内的每个元素。

```c++
// reset each element to 0
fill(vec.begin(), vec.end(), 0);
```



`fill_n`函数接受单个迭代器参数、一个计数值和一个值，它将给定值赋予迭代器指向位置开始的指定个元素。

```c++
// reset all the elements of vec to 0
fill_n(vec.begin(), vec.size(), 0);
```



插入迭代器`back_inserter`函数（定义在头文件`iterator`中）接受一个指向容器的引用，返回与该容器绑定的插入迭代器。通过此迭代器赋值时，赋值运算符会调用`push_back`将一个具有给定值的元素添加到容器中。

```c++
vector<int> vec;    // empty vector
auto it = back_inserter(vec);   // assigning through it adds elements to vec
*it = 42;   // vec now has one element with value 42
// ok: back_inserter creates an insert iterator that adds elements to vec
fill_n(back_inserter(vec), 10, 0);  // appends ten elements to vec
```

`copy`函数接受三个迭代器参数，前两个参数指定输入序列，第三个参数指定目的序列的起始位置。它将输入序列中的元素拷贝到目的序列中，返回目的位置迭代器（递增后）的值。

```c++
int a1[] = { 0,1,2,3,4,5,6,7,8,9 };
int a2[sizeof(a1) / sizeof(*a1)];     // a2 has the same size as a1
// ret points just past the last element copied into a2
auto ret = copy(begin(a1), end(a1), a2);    // copy a1 into a2
```



`replace`函数接受四个参数，前两个迭代器参数指定输入序列，后两个参数指定要搜索的值和替换值。它将序列中所有等于第一个值的元素都替换为第二个值。

```c++
// replace any element with the value 0 with 42
replace(ilst.begin(), ilst.end(), 0, 42);
```



相对于`replace`，`replace_copy`函数可以保留原序列不变。它接受第三个迭代器参数，指定调整后序列的保存位置。

```c++
// use back_inserter to grow destination as needed
replace_copy(ilst.cbegin(), ilst.cend(), back_inserter(ivec), 0, 42);
```

很多算法都提供“copy”版本，这些版本不会将新元素放回输入序列，而是创建一个新序列保存结果。

```c++
vector<int> words{1,1,1,3,4,5,1};
vector<int> new_words;
replace_copy(words.begin(),words.end(),back_inserter(new_words),1,0);
```

### 重排容器元素的算法

`sort`函数接受两个迭代器参数，指定排序范围。它利用元素类型的`<`运算符重新排列元素。

```C++
void elimDups(vector<string> &words)
{
    sort(words.begin(), words.end());

    auto end_unique = unique(words.begin(), words.end());
	//unique函数重排输入序列，消除相邻的重复项，返回指向不重复值范围末尾的迭代器。
    words.erase(end_unique, words.end());
}
```

### lambda表达式

`find_if`函数接受两个迭代器参数和一个谓词参数。迭代器参数用于指定序列范围，之后对序列中的每个元素调用给定谓词，并返回第一个使谓词返回非0值的元素。如果不存在，则返回尾迭代器。

对于一个对象或表达式，如果可以对其使用调用运算符`()`，则称它为可调用对象（callable object）。可以向算法传递任何类别的可调用对象。

一个`lambda`表达式表示一个可调用的代码单元，类似未命名的内联函数，但可以定义在函数内部。

```c++
vector<int> words{1, -1, 1, 3, 4, -5, -1};
int num = 2;
auto p = find_if(words.begin(), words.end(), [num](const int &a) { return a >= num; });
for_each(words.begin(), words.end(), [](const int &s) { cout << s << " "; });
transform(words.begin(), words.end(), words.begin(), [](int i) -> int { if (i < 0) return -i; else return i; });
```

### 参数绑定

`bind`函数定义在头文件`functional`中，相当于一个函数适配器，它接受一个可调用对象，生成一个新的可调用对象来适配原对象的参数列表。一般形式如下：

```c++
auto newCallable = bind(callable, arg_list);
```

其中，`newCallable`本身是一个可调用对象，`arg_list`是一个以逗号分隔的参数列表，对应给定的`callable`的参数。之后调用`newCallable`时，`newCallable`会再调用`callable`，并传递给它`arg_list`中的参数。`arg_list`中可能包含形如`_n`的名字，其中`n`是一个整数。这些参数是占位符，表示`newCallable`的参数，它们占据了传递给`newCallable`的参数的位置。数值`n`表示生成的可调用对象中参数的位置：`_1`为`newCallable`的第一个参数，`_2`为`newCallable`的第二个参数，依次类推。这些名字都定义在命名空间`placeholders`中，它又定义在命名空间`std`中，因此使用时应该进行双重限定。

```c++
using namespace std;
using std::placeholders::_1;
using namespace std::placeholders;

bool check_size(const string &s, string::size_type sz) {
    return s.size() > sz;
}

int main() {

    auto check6 = bind(check_size, _1, 6);
    string s = "hello";
    bool b1 = check6(s);

}
```

```c++
// sort on word length, shortest to longest
sort(words.begin(), words.end(), isShorter);
// sort on word length, longest to shortest
sort(words.begin(), words.end(), bind(isShorter, _2, _1));
```



### 迭代器

除了为每种容器定义的迭代器之外，标准库还在头文件`iterator`中定义了另外几种迭代器。

- 插入迭代器（insert iterator）：该类型迭代器被绑定到容器对象上，可用来向容器中插入元素。
- 流迭代器（stream iterator）：该类型迭代器被绑定到输入或输出流上，可用来遍历所关联的IO流。
- 反向迭代器（reverse iterator）：该类型迭代器向后而不是向前移动。除了`forward_list`之外的标准库容器都有反向迭代器。
- 移动迭代器（move iterator）：该类型迭代器用来移动容器元素。

#### 插入迭代器（Insert Iterators）

插入器是一种迭代器适配器，它接受一个容器参数，生成一个插入迭代器。通过插入迭代器**赋值**时，该迭代器调用容器操作向给定容器的指定位置插入一个元素。

- `back_inserter`：创建一个调用`push_back`操作的迭代器。
- `front_inserter`：创建一个调用`push_front`操作的迭代器。
- `inserter`：创建一个调用`insert`操作的迭代器。此函数接受第二个参数，该参数必须是一个指向给定容器的迭代器，元素会被插入到该参数指向的元素之前。

```c++
list<int> lst = { 1,2,3,4 };
list<int> lst2, lst3;   // empty lists
// after copy completes, lst2 contains 4 3 2 1
copy(lst.cbegin(), lst.cend(), front_inserter(lst2));
// after copy completes, lst3 contains 1 2 3 4
copy(lst.cbegin(), lst.cend(), inserter(lst3, lst3.begin()));
```



### 泛型算法结构

#### 算法形参模式

大多数算法的形参模式是以下四种形式之一：

```c++
alg(beg, end, other args);
alg(beg, end, dest, other args);
alg(beg, end, beg2, other args);
alg(beg, end, beg2, end2, other args);
```

其中`alg`是算法名称，`beg`和`end`表示算法所操作的输入范围。几乎所有算法都接受一个输入范围，是否有其他参数依赖于算法操作。`dest`表示输出范围，`beg2`和`end2`表示第二个输入范围。

向输出迭代器写入数据的算法都假定目标空间足够容纳要写入的数据。

接受单独一个迭代器参数表示第二个输入范围的算法都假定从迭代器参数开始的序列至少与第一个输入范围一样大。

#### 算法命名规范

接受谓词参数的算法都有附加的`_if`后缀。

```c++
find(beg, end, val);       // find the first instance of val in the input range
find_if(beg, end, pred);   // find the first instance for which pred is true
```

将执行结果写入额外目的空间的算法都有`_copy`后缀。

```c++
reverse(beg, end);              // reverse the elements in the input range
reverse_copy(beg, end, dest);   // copy elements in reverse order into dest
```

一些算法同时提供`_copy`和`_if`版本。

### 特定容器算法

对于`list`和`forward_list`类型，应该优先使用成员函数版本的算法，而非通用算法。

`list`和`forward_list`成员函数版本的算法：

```C++
//    list.merge(ls,comp)两个容器要有序再merge，比较器可以不指定
    list<int> l1{8, 5, 1};
    list<int> l2{6, 2, 0};
    l2.merge(l1, [](int a, int b) { return a > b; });

//    list.remove(elem)
    list<int> l1{1,2,3,4,5,6,7,8,9,10};
    l1.remove(8);
    l1.remove_if([](int n){return n%2==1;});

//    list.reverse()
    list<int> l1{1,2,3,4,5,6,7,8,9,10};
    l1.reverse();

//    list.sort() 可以加比较器参数
    list<int> l1{1,3,2,6,5,4,7,8,9,10};
    l1.sort();

//    list.unique()移除相邻的重复元素,因此list一定要事先有序
    list<int> l1{1,5,2,6,8,6,7,8,2,10};
    l1.sort();
    l1.unique();
//    list.unique()可以加pred参数 
    list<float> l2{0.1,0.22,0.90,0.91,0.23,1.01,0.45,0.46,0.24,0.99};
    l2.sort();
    l2.unique([](float a,float b){return (abs(a-b)<=0.02);});

//splice()函数是list中的一个剪贴函数，将另外一个list中的元素剪贴到本list中
 	//list1.splice(position, list2): 将list2中的所有元素剪贴到list1的position位置；
    //list1.splice(position, list2, iter): 将list2中某个位置的迭代器iter指向的元素剪贴到list1中的position位置；
    //list1.splice(position, list2, iter1, iter2): 将list2中的某一段位置iter1 ~ iter2的元素剪贴到list1中的position位置
    list<int> list1 = { 1, 2, 3, 4, 5 };
    list<int> list2 = { 11, 12, 13, 14, 15 };
    list<int>::iterator iter = list1.begin();
    iter++;
    //把list2全部剪贴到list1的iter位置
    list1.splice(iter, list2);
    //list1: 1 11 12 13 14 15 2 3 4 5
    //list2:

    list<int> list3 = { 1, 2, 3, 4, 5 };
    list<int> list4 = { 11, 12, 13, 14, 15 };
    list<int>::iterator iter1 = list4.begin();
    iter1++;
    //把list4中iter1位置处的值剪贴到list3的list3.begin()位置
    list3.splice(list3.begin(), list4, iter1);
    //list3: 12 1 2 3 4 5
    //list4: 11 13 14 15

    list<int> list5 = { 1, 2, 3, 4, 5 };
    list<int> list6 = { 11, 12, 13, 14, 15 };
    list<int>::iterator iter2 = list6.begin();
    list<int>::iterator iter3 = iter2;
    iter3++; iter3++; iter3++;
    //把list6中从iter2到iter3的元素剪贴到list5中的list5.begin()处
    list5.splice(list5.begin(), list6, iter2, iter3);
    //list5: 11 12 13 1 2 3 4 5
    //list6: 14 15
```







## 关联容器

关联容器支持高效的关键字查找和访问操作。2个主要的关联容器（associative-container）类型是`map`和`set`。

- `map`中的元素是一些键值对（key-value）：关键字起索引作用，值表示与索引相关联的数据。
- `set`中每个元素只包含一个关键字，支持高效的关键字查询操作：检查一个给定关键字是否在`set`中。

标准库提供了8个关联容器，它们之间的不同体现在三个方面：

- 是`map`还是`set`类型。
- 是否允许保存重复的关键字。
- 是否按顺序保存元素。

允许重复保存关键字的容器名字都包含单词`multi`；无序保存元素的容器名字都以单词`unordered`开头。

`map`和`multimap`类型定义在头文件`map`中；`set`和`multiset`类型定义在头文件`set`中；无序容器定义在头文件`unordered_map`和`unordered_set`中。

map ：关联数组；保存关键字-值对；数据的存放是有序的
multimap：关键字可以重复出现的map
unordered_map：用哈希函数组织的map；容器中的数据存放是无序的
unordered_multimap：哈希组织的map；关键字可以重复出现

set : 关键字即值，即只保存关键字的容器
multiset : 关键字可重复出现的set
unordered_set: 无序的set
unordered_multiset: 关键字可以重复出现的无序的set

### pair

它定义在头文件utility中。
一个pair保存两个数据成员。类似容器，pair是一个用来生成特定类型的模板。当创建一个pair时，我们必须提供两个类型名，pair的数据成员将具有对应的类型。可以这么认为，一个pair类型的对象，其实存储的是一个键值对（key-value）

```c++
pair<string,string>anon;    //保存两个string
pair<string,size_t> word_count;   //保存一个string和一个size_t
pair<string,vector<int>> line;     //保存string和vector<int>

pair<string,string> author{"James","Joyce"};
//pair<string,string> author = {"James","Joyce"};

cout<<w.first<<" "<<w.second<<endl;

//定义一个函数返回类型是pair
pair<string,int> process(vector<string> &v)
{
   //处理V
   if(!v.empty())
      return {v.back(),v.back().size()};   //列表初始化，返回
   else
      return pair<string,int>();  //隐式构造一个空pair，返回
}
```

###  map

类似顺序容器，关联容器也是模板。当定义一个map时，必须指明关键字类型又指明值类型。每个关联容器都定义了一个默认构造函数，它创建一个指定类型的空容器。我们也可以将关联容器初始化为另一个同类型容器的拷贝，或是从一个值范围来初始化关联容器，只要这些值可以转化为容器所需类型就可以。

```C++
map<string,size_t> word_count;  //string到size_t的空map
map<string,string> authors1={{"Joyce","James"},{"Austen","Jane"}};
map<string,string>  authors2=authors1;
```

map容器额外的类型别名

```c++
map<string,int>::value_type v1;   //v1是一个pair<const string,int>
map<string,int>::key_type v2;     //v2是一个string
map<string,int>::mapped_type v3;   //v3是一个int
```

#### map容器的操作

```c++
c.insert(v)          v是value_type类型的对象
c.emplace(args)      args用来构造一个value_type类型的对象
c.insert(b,e)        b,e是迭代器，表示一个c::value_type类型值的范围
c.insert(il)         il代表花括号列表，花括号里是一个pair
c.insert(p,v)        类似insert(v)，迭代器p指出从哪里开始搜索新元素应该存储的位置
c.emplace(p,args)    类似emplace(args)，迭代器p指出从哪里开始搜索新元素应该存储的位置

//向map容器添加一个元素
map<string,size_t> word_count;
string word("fengxin");
word_count.insert({word,1});
   //等价于 word_count.insert(make_pair(word,1));
   // 等价于  word_count.insert(pair<string,size_t>(word,1));
   // 等价于   word_count.insert(map<string,size_t>::value_type(word,1));
   // 等价于   word_count.emplace(word,1);

//word_count是一个map容器，对改map进行遍历
auto map_it=word_count.cbegin();//获得一个指向首元素的const迭代器
for(map_it;map_it!=word_count.cend();map_it++)
   cout<<map_it->first<<" "<<map_it->second<<endl;

//下标操作
map<string,size_t> word_count;   //empty map
word_count["Anna"]=1; //word_count中并没有关键字为Anna的元素，所以会自动创建一个关键字为Anna，值为1的键值对插入到map中
cout<<word_count["Anna"]<<endl;     //输出1


//map.find(k)         返回一个迭代器，指向关键字为k的元素。若k不在容器中，则返回尾后迭代器
//map.count(k)        返回关键字等于k的元素数量。对于map而言，返回值永远是0或1。
auto it =  word_count.find("Anna");
if ( it==word_count.end()){
    cout<<"no Anna"<<endl;
} else{
   	cout<<it->first<< "  " <<it->second<<endl;
}
auto num = word_count.count("Anna");

//删除元素
//同添加元素一样，关联容器并不支持顺序容器的pop_front()和pop_back()操作。
c.erase(k)   //从c中删除关键字为k的元素。返回一个size_type的值，指出删除元素数量
c.erase(p)   //从c中删除迭代器p指定的元素。
c.erase(b,e) //删除迭代器b和e表示的范围中的元素。返回e
```



#### mutimap

和map容器类似，区别在于map容器中的关键字必须是唯一的，对于一个给定的关键字，只能有一个元素的关键字等于它。而multimap对此没有限制，允许多个元素具有相同的关键字。

```C++
authors.insert({"Barth","sot-weed factor"});
//添加第二个元素，关键字也是Barth
authors.insert({"Barth","Lost in the Funhouse"});
```

**map是支持下标访问的，使用起来很方便。但是由于multimap中的一个关键字可能对应多个值，所以multimap并不支持下标操作**

**如果multimap中有多个元素具有相同的关键字，则这些关键字在容器中会相邻存储。**我们可以通过这一特性，将一个关键字对应的多个值全部找出来。

```c++
multimap<int,string> an;
an.insert({1,"a"});
an.insert({1,"b"});
an.insert({2,"x"});
int n = an.count(1);
auto it = an.find(1);
while (n){
    cout<<it->second<<endl;
    it++;
    n--;
}

//以下方式也很好
for(auto beg=an.lower_bound(1),end=an.upper_bound(1);beg!=end;beg++){
        cout<<beg->second<<endl;
}
```

`lower_bound`和`upper_bound`操作都接受一个关键字，返回一个迭代器。如果关键字在容器中，`lower_bound`返回的迭代器会指向第一个匹配给定关键字的元素，而`upper_bound`返回的迭代器则指向最后一个匹配元素之后的位置。如果关键字不在`multimap`中，则`lower_bound`和`upper_bound`会返回相等的迭代器，指向一个不影响排序的关键字插入位置。因此用相同的关键字调用`lower_bound`和`upper_bound`会得到一个迭代器范围，表示所有具有该关键字的元素范围。

`lower_bound`和`upper_bound`有可能返回尾后迭代器。如果查找的元素具有容器中最大的关键字，则`upper_bound`返回尾后迭代器。如果关键字不存在，且大于容器中任何关键字，则`lower_bound`也返回尾后迭代器。

### set

同map容器，定义set容器时，我们需要指定关键字的类型，即set集合存储的元素的类型。由于set是有序的关联容器，容器内部会按字典顺序自动为元素进行排序，如果我们需要将数据按顺序存储起来，使用set容器是一个非常不错的选择。数据初始化与map类似。

```C++
set<string> a1={"fengxin","666"}; 
set<string> a2=a1;
```

#### set操作

```
set<string> a;  //empty set
a.insert("fengxin");  // 插入一个元素
a.emplace("123");   //插入
a.erase("123");    //删除关键字为123的元素

//遍历操作
set<int> iset={0,1,2,3,4,5,6,7,8,9};
auto it=iset.begin();
if(it!=iset.end())
{
     *it=10;  //错误：set中关键字是只读的
     cout<<*it<<endl;
}

//查找操作
set<int> iset={0,1,2,3,4,5,6,7,8,9};
auto it=iset.find(6);
```

由于set中存储的只有关键字，所以set容器并不支持下标操作。
set同样可以使用find函数进行对关键字的查找，此时函数返回指向关键字的迭代器。

set和multiset容器区别同map与multimap容器。

### 无序容器

新标准库定义了4个无序关联容器（unordered associative container），这些容器使用哈希函数（hash function）和关键字类型的`==`运算符组织元素。

无序容器和对应的有序容器通常可以相互替换。但是由于元素未按顺序存储，使用无序容器的程序输出一般会与有序容器的版本不同。

无序容器在存储上组织为一组桶，每个桶保存零或多个元素。无序容器使用一个哈希函数将元素映射到桶。为了访问一个元素，容器首先计算元素的哈希值，它指出应该搜索哪个桶。容器将具有一个特定哈希值的所有元素都保存在相同的桶中。因此无序容器的性能依赖于哈希函数的质量和桶的数量及大小。







## 动态内存



C++中的动态内存管理通过一对运算符完成：`new`在动态内存中为对象分配空间并返回指向该对象的指针，可以选择对对象进行初始化；`delete`接受一个动态对象的指针，销毁该对象并释放与之关联的内存。

### 智能指针

新标准库提供了两种智能指针（smart pointer）类型来管理动态对象。智能指针的行为类似常规指针，但它自动释放所指向的对象。这两种智能指针的区别在于管理底层指针的方式：`shared_ptr`允许多个指针指向同一个对象；`unique_ptr`独占所指向的对象。标准库还定义了一个名为`weak_ptr`的伴随类，它是一种弱引用，指向`shared_ptr`所管理的对象。这三种类型都定义在头文件`memory`中。

类似vector，智能指针也是模板。

#### unique_ptr

简单说，当我们独占资源的所有权的时候，可以使用 `unique_ptr` 对资源进行管理——离开 unique_ptr 对象的作用域时，会自动释放资源。

```C++
//使用裸指针 要记得释放内存
{
    int* p = new int(100);
    // ...
    delete p;  
}

//使用unique_ptr自动管理内存
{
    std::unique_ptr<int> uptr = std::make_unique<int>(200);
    //...
    // 离开 uptr 的作用域的时候自动释放内存
}

{
    std::unique_ptr<int> uptr = std::make_unique<int>(200);
    std::unique_ptr<int> uptr1 = uptr;  // 编译错误，std::unique_ptr<T> 是 move-only 的

    std::unique_ptr<int> uptr2 = std::move(uptr);
    assert(uptr == nullptr);
}

//指向一个数组
{
    std::unique_ptr<int[]> uptr = std::make_unique<int[]>(10);
    for (int i = 0; i < 10; i++) {
        uptr[i] = i * i;
    }   
    for (int i = 0; i < 10; i++) {
        std::cout << uptr[i] << std::endl;
    }   
}
```

#### shared_ptr

`shared_ptr` 其实就是对资源做引用计数——当引用计数为 0 的时候，自动释放资源。



```C++
shared_ptr<string> p1;      // shared_ptr that can point at a string
shared_ptr<list<int>> p2;   // shared_ptr that can point at a list of ints
```

```C++
//在动态内存中分配一个对象并初始化它，返回指向该对象的shared_ptr
// shared_ptr that points to an int with value 42
shared_ptr<int> p3 = make_shared<int>(42);
// p4 points to a string with value 9999999999
shared_ptr<string> p4 = make_shared<string>(10, '9');
// p5 points to an int that is value initialized
shared_ptr<int> p5 = make_shared<int>();


//进行拷贝或赋值操作时，每个shared_ptr会记录有多少个其他shared_ptr与其指向相同的对象。
auto p = make_shared<int>(42);  // object to which p points has one user
auto q(p);  // p and q point to the same object
            // object to which p and q point has two users

{
    std::shared_ptr<int> sptr = std::make_shared<int>(200);
    assert(sptr.use_count() == 1);  // 此时引用计数为 1
    {   
        std::shared_ptr<int> sptr1 = sptr;
        assert(sptr.get() == sptr1.get());
        assert(sptr.use_count() == 2);   // sptr 和 sptr1 共享资源，引用计数为 2
    }   
    assert(sptr.use_count() == 1);   // sptr1 已经释放
}
// use_count 为 0 时自动释放内存

```

#### weak_ptr

`weak_ptr` 要与 `shared_ptr` 一起使用。 一个 `weak_ptr` 对象看做是 `shared_ptr` 对象管理的资源的观察者，它不影响共享资源的生命周期：

1. 如果需要使用 `weak_ptr` 正在观察的资源，可以将 `weak_ptr` 提升为 `shared_ptr`。
2. 当 `shared_ptr` 管理的资源被释放时，`weak_ptr` 会自动变成 nullptr。

```C++
void Observe(std::weak_ptr<int> wptr) {
    if (auto sptr = wptr.lock()) {
        std::cout << "value: " << *sptr << std::endl;
    } else {
        std::cout << "wptr lock fail" << std::endl;
    }
}

std::weak_ptr<int> wptr;
{
    auto sptr = std::make_shared<int>(111);
    wptr = sptr;
    Observe(wptr);  // sptr 指向的资源没被释放，wptr 可以成功提升为 shared_ptr
}
Observe(wptr);  // sptr 指向的资源已被释放，wptr 无法提升为 shared_ptr
```



智能指针，本质上是对资源所有权和生命周期管理的抽象：

1. 当资源是被独占时，使用 std::unique_ptr 对资源进行管理。
2. 当资源会被共享时，使用 std::shared_ptr 对资源进行管理。
3. 使用 std::weak_ptr 作为 std::shared_ptr 管理对象的观察者。
4. 通过继承 std::enable_shared_from_this 来获取 this 的 std::shared_ptr 对象。
