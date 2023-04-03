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

## using

C中常用typedef A B这样的语法，将B定义为A类型，也就是给A类型一个别名B

对应typedef A B，使用using B=A可以进行同样的操作。

```c++
typedef vector<int> V1; 
using V2 = vector<int>;
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
 const int& rd = d; 
```

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

   1). 千万别返回局部对象的引用，当函数执行完局部对象也就被销毁，栈空间被释放，从而返 回的地址已经不存在，导致后面执行出错。

   2). 返回堆区对象的引用，这种情况要特别注意，这时候返回函数的引用是作为一个临时变量出现，没有将它赋值给一个实际存在的变量，那么这个堆区对象的内存空间就没有释放，可能造成内存泄漏。有人说这样做是非法的？其实不是绝对的，只能说这种编程习惯很不好，这样做只是容易造成内存泄漏，只是我们要记住，我们在申请堆内存以后必须记得去释放这块内存。



3. 返回函数的引用去初始化一个新的引用

   这个和前面一样，都是不会产生副本，但是现在是用返回值去初始化一个引用声明c，也就是说这时候变成了变量temp的别名，在c的生命周期内temp是一直有效的，这样做完全可以。

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

## vector

标准库类型`vector`表示对象的集合，也叫做容器（container），定义在头文件`vector`中。`vector`中所有对象的类型都相同，每个对象都有一个索引与之对应并用于访问该对象。

`vector`是模板（template）而非类型，由`vector`生成的类型必须包含`vector`中元素的类型，如`vector<int>`。

因为引用不是对象，所以不存在包含引用的`vector`。

在早期的C++标准中，如果`vector`的元素还是`vector`，定义时必须在外层`vector`对象的右尖括号和其元素类型之间添加一个空格，如`vector<vector<int> >`。但是在C++11标准中，可以直接写成`vector<vector<int>>`，不需要添加空格。

### 初始化

初始化`vector`对象的方法

| 方法                        | 解释                                                         |
| --------------------------- | ------------------------------------------------------------ |
| `vector<T> v1`              | `v1`是一个空`vector`，它潜在的元素是`T`类型的，执行默认初始化 |
| `vector<T> v2(v1)`          | `v2`中包含有`v1`所有元素的副本                               |
| `vector<T> v2 = v1`         | 等价于`v2(v1)`，`v2`中包含`v1`所有元素的副本                 |
| `vector<T> v3(n, val)`      | `v3`包含了n个重复的元素，每个元素的值都是`val`               |
| `vector<T> v4(n)`           | `v4`包含了n个重复地执行了值初始化的对象                      |
| `vector<T> v5{a, b, c...}`  | `v5`包含了初始值个数的元素，每个元素被赋予相应的初始值       |
| `vector<T> v5={a, b, c...}` | 等价于`v5{a, b, c...}`                                       |

在（C++11）中新增：

列表初始化： `vector<string> v{"a", "an", "the"};` 

### vector操作

| 作                 | 解释                                                         |
| ------------------ | ------------------------------------------------------------ |
| `v.emtpy()`        | 如果`v`不含有任何元素，返回真；否则返回假                    |
| `v.size()`         | 返回`v`中元素的个数                                          |
| `v.push_back(t)`   | 向`v`的尾端添加一个值为`t`的元素                             |
| `v[n]`             | 返回`v`中第`n`个位置上元素的引用                             |
| `v1 = v2`          | 用`v2`中的元素拷贝替换`v1`中的元素                           |
| `v1 = {a,b,c...}`  | 用列表中元素的拷贝替换`v1`中的元素                           |
| `v1 == v2`         | `v1`和`v2`相等当且仅当它们的元素数量相同且对应位置的元素值都相同 |
| `v1 != v2`         | 同上                                                         |
| `<`,`<=`,`>`, `>=` | 以字典顺序进行比较                                           |

`vector`对象（以及`string`对象）的下标运算符，只能对确知已存在的元素执行下标操作，不能用于添加元素。

## 迭代器

- 所有标准库容器都可以使用迭代器。
- 类似于指针类型，迭代器也提供了对对象的间接访问。

迭代器按照定义方式分成以下四种。

1) 正向迭代器，定义方法如下：

   容器类名::iterator 迭代器名;

2) 常量正向迭代器，定义方法如下：

   容器类名::const_iterator 迭代器名;

3) 反向迭代器，定义方法如下：

   容器类名::reverse_iterator 迭代器名;

4) 常量反向迭代器，定义方法如下：

   容器类名::const_reverse_iterator 迭代器名;

### 使用迭代器

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

## 表达式

[Cpp_Primer_Practice/ch04.md at master · applenob/Cpp_Primer_Practice (github.com)](https://github.com/applenob/Cpp_Primer_Practice/blob/master/notes/ch04.md)

## 结构

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

### constexpr函数

- 指能用于常量表达式的函数。
- `constexpr int new_sz() {return 42;}`
- 函数的返回类型及所有形参类型都要是字面值类型。
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

## 类

- 成员函数的**声明**必须在类的内部。
- 成员函数的**定义**既可以在类的内部也可以在外部。
- 使用点运算符 `.` 调用成员函数。
- 必须对任何`const`或引用类型成员以及没有默认构造函数的类类型的任何成员使用初始化式。
- 成员函数作为内联函数
  - 在类的内部，常有一些规模较小的函数适合于被声明成内联函数。
  - **定义**在类内部的函数是**自动内联**的。
  - 在类外部定义的成员函数，也可以在声明时显式地加上 `inline`。

- 访问说明符

  （access specifiers）：

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

static 修饰成员变量
静态变量，是在编译阶段就分配空间，对象还没有创建时，就已经分配空间。
**static静态成员变量不能在类的内部初始化。在类的内部只是声明，定义必须在类定义体的外部，通常在类的实现文件中初始化。**
静态数据成员不属于某个对象，在为对象分配空间中不包括静态成员所占空间。

如果一个类的成员，既要实现共享，又要实现不可改变，那就用 static const 修饰

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

## const

常类型是指使用类型修饰符**const**说明的类型，常类型的变量或对象的值是不能被更新的。

- 可以节省空间，避免不必要的内存分配
- const定义常量从汇编的角度来看，只是给出了对应的内存地址，而不是像`#define`一样给出的是立即数。
- const定义的常量在程序运行过程中只有一份拷贝，而`#define`定义的常量在内存中有若干个拷贝。

### const与指针

```c++
int a = 10, b = 20;
int* const pc = &a;//pc是const指针，或者是常指针。
pc = &b;//错误pc 是只读的
pc++;//错误pc是只读的
*pc = 100;//正确，a被修改
//pc是指向int型对象的const指针
//不能使pc再被赋值指向其他对象，任何企图给const指针赋值的操作都会导致编译错误
//但是可以通过pc间接引用修改该对象的值

const char * pc1; //指向const对象的指针或者说指向常量的指针。
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









