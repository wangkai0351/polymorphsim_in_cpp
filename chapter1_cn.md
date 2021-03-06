# 第一章

# C++ Classes的语法和语意

## （C++ Classes‘ Syntax and Semantics）

这本书谈得全是面向对象的**多态与虚拟**性质，这个主题可以说是C++语言的精髓。这不是一本讲解C++语法的书，不过，为了建立读者与我之间的共识，同时也引导各位在C++高阶语法上有全面性的认识，我以小小的篇幅，将本书用到的C++高阶语法，做一个整理。这些语法几乎全部围绕在class展开。

如果你希望获得这些语法更详细的资料，我推荐两本C++经典好书：

1. *The C++ Programming Language*（Addison Wesley）：这是C++语言创始者Bjarne Stroustrup所著的“C++元典”。内容极为详尽，目前为第三版（1997）。
2. *C++ Primer*（Addison Wesley）：这是世界上第一套C++编译器的实作者Stanly B.Lippman所著的经典书籍。内容极为详尽，而且排版比Bjarne的书更胜一筹，更亲和易读（我个人看法）。目前也是第三版（1998）。

## 开宗明义：Classes与Objects

本书讲的是Object-Oriented（面向对象）的最重要精神：多态和虚拟，那么我们先对objects做个了解。牛津电脑字典对于objects这个词条的解释是：

> Object：一个含义广泛的术语，用以描述软件系统中可供辨别的组件（components），如今常被用来描述一个具有某种自我包容（self-contained）意识并有着可识别界限的元件。在面向对象设计中

上述定义确实学术了一些！如果你没有看懂，其实只要建立这样的一个概念就好：object就是“东西”。东西必须占用一定的空间，在计算机中也就是占用一定的存储空间，所以我们又叫它为一个实体（instance）。

上述定义有相当部分在描述“东西”的起源。它说“东西是根据一个模板产生出来的”，这个“模板”其实就是“东西的属性描述”，也就是所谓的class（上述定义使用了template一词，容易引起混淆，因为“template”在C++语言中另有所指，见本章末的template一节）。东西的属性（class）由“数据（成员）”和“用以处理该数据的程序（在计算机世界里就是函数）”两者组成。

现在我们再看看牛津电脑字典对于class这个词条的解释：

> class：一种由程序语言SIMULA所引入的设施（facility）。class提供了一种用以描述抽象数据类型（abstract data type）的形式，它也是Smalltalk及其他object-oriented language中所谓的object概念的基础。

清注意“抽象数据类型”几个字，这道出class只是一种“属性的描述”，并非实体；根据class产生出来的object才是实体。“产生object”的动作，我们称之为实例化（instaniation）。

再让我们看看牛津电脑字典对于instantiation的解释：

> instantiation：根据一个object class（或称为generic unit或template）产生出一个实体（instance）。

object-oriented的程序，是以object为程序的行为主体。我们（程序员）可能花很多精力来描述object的属性，包括其数据和行为（处理数据的方法），并以calsses语法呈现我们的设计。生序开始执行时，便根据设计好的classes—chanshengchobjects。这些objects彼此合作，执行程序行为和作用。

从广义的角度去看，语言内置类型如int，float，long等等也都是一种class，用以描述数据的性质（但没有相关的处理函数）。因此下面的*i*和*f*都是objects：

```c++
int i;		//i是一个int object
float j;	//f是一个float object
```

现在让我以一个实例说明object和class的关系。假设我的classes设计如下：

```c++
struct Point
{
    long _x;
    long _y;
};
class CStroke
{
    public:
    void DrawStroke(/*...*/) { };	//画线条
    void Serialize( /*...*/ ) { };
    // ... User Interface（UI） functions
    
    protected:
    vector<Point*> m_Array; // C++ standard library's array
    int m_penColor;		//笔的颜色
    int m_penWidth;		//笔的宽度
    int m_penStyle;		//笔的样式
};
```

于是，每当我（程序设计者）允许使用者在屏幕上画一条线条（stroke），我便在程序中产生一个Cstroke object，其中一个array准备用来存储线条上各点的坐标，以及三个int变量分别代表笔的颜色、宽度、样式。卧铺必须设计一些UI函数，让使用者能够以滑动鼠标代替画笔，直接在屏幕上输入构成线条的每一个坐标点。以下图为例，我（程序设计者）在程序中产生了8个Cstroke objects。

![插入图片]()

## Classes 的声明与实现

### （ Declaration & Implementation ）

下面是C++ class的声明形式（declaration），通常放在.H文件中：

```c++
#0001 class CPoint {
#0002 public:
#0003 	CPoint( float x = 0.0 ) : _x( x ) { }
#0004
#0005 	float x() { return _x; }
#0006 	void x( float xval ) { _x = xval; }
#0007 	// ...
#0008 protected:
#0009 	float _x;  // data member
#0010 };
```

> 称此为class declaration的代表人物与书籍是Stanley Lippman 的 ***C++ Primer***。但是也有人（例如 Bjarne Stroustrup 在其 ***The C++ Programming Language***中）把这样的东西称为class definition。Bjarne 是 C++ 语言的创始人，Lippman是全世界第一套C++编译器（贝尔实验室的cfront）的实际作者。他们两人曾长期共事，都是C++发展过程中的关键人物，怎么会在这么基本的用词上口径不一致呢？我很纳闷。

Class内含两种东西：一是data members（例如上面的*_x*），一是member functions（例如上面的CPoint()以及两个*x()*）。

上面例子的三个member functions都直接在class中完成了实际定义，并因此的自动成为一个inline函数（inline函数将在稍后提及）。通常函数定义代码极为简易时，我们才会这么做。如果函数比较复杂，我们会在class declaration中完成member functions的声明，并在.CPP文件中完成其定义的实现，例如：

```c++
// in hello.h
//class eclaration
#0001 class CPoint {
#0002 public:
#0003 	CPoint( float x = 0.0 ) : _x( x ) { }
#0004
#0005 	float x();  // 只是 member function 的声明
#0006 	void x( float xval ); // 只是 member function 的声明
#0007 	// ...
#0008 protected:
#0009 	float _x;
#0010 };

// in hello.cpp
//class member functions) mplementation
#0001 #include "hello.h"
#0002
#0003 float CPoint::x() { return _x; } // 定义内容
#0004 void CPoint::x( float xval ) { _x = xval; } // 定义内容
```

> 称此为function implementation的代表人物与书籍是 Stanley Lippman 的 ***C++ Primer***。但是也有人（例如 Bjarne Stroustrup 在其 ***The C++ Programming Language*** 中）把上述这样的函数定义称为 function definition。再一次，我很困惑，他们两人怎么会再这么基本的用词上口径不一致呢？
>
> 一个普遍被大家接受的说法是，**会在存储介质中分配空间的，称之为definition**。

## 符号命名习惯

###  （Naming Convention）

有一种常见的符号命名习惯是：在class名称前加上“C”`2`，在data members名称前加上“m_”。至于member functions，并没有什么常见的命名习惯。

[^2]: Microsoft MFC 的所有 classes 都以“C”作为名称前置词，Borland OWL则以“T”作为所有classes的名称前置词。MFC和OWL是市场上两套有名的C++ class libraries，由于它们具备某些高阶特性，所以也被称为application framework。

## 函数重载

### （Function Overloading）

所谓重载（Function Overloading），是指不同的程序公用一个函数名称。例如稍早所提的***Cpoint***之中，有两个member functions的名称同为*x()*:

```C++
class CPoint {
public:
...
	float x();
	void x( float xval );
};
```

其定义如下：

```c++
float CPoint::x() { return _x; }
void CPoint::x( float xval ) { _x = xval; }
```

函数名称虽然相同，但（输入）参数不同（个数不同，类型也不同），函数定义也不相同。

C++之所以有function overloading的机制，是为了解决“指引数据类型不同，就必须设计算法完全相同但函数名称不同的多份函数代码”的缺憾。举个例子，加法就是加法，如果为了int、float、double、long……各设计出对应的*AddInt()*、*AddFloat()*、*AddDouble()*、*AddLong()*等加法函数，我们会觉得很琐碎，不漂亮。如果能够把它们全部都叫做*Add()*，而我们不管对哪一种数据类型做加法运算，都调用*Add()*就好，那么就逻辑层面来看就清爽多了。至于顶层机制，则由编译器根据*Add()*所获得的参数类型，决定真正该调用哪一个*Add()*函数定义。

于是，以加法为例，我们可以设计一下四个同名函数：

```c++
int Add(int a, int b);
long Add(long a, long b);
double Add(double a, double b);
float Add(float a, float b);
```

并这样使用它：

```c++
int i = Add(3, 5);
long l = Add(3L, 5L);
double d = Add(3.141592653, 5.141592653);
float f = Add(3.14, 5.14);
```

Function overloading适用于class member functions（如先前的*CPoint::x()*），也适用于一般的global functions（如上述的*Add()*）。

Function overloading无法适用于函数名称相同，（输入）参数（包括个数和类型）也完全相同，只有返回值类型不同的情况。这种情况将无法通过编译，会出现错误提示信息（以Visual C++为例）：

error C2556: 'Add' : overloaded functions only differ by return type

我想你已经看到了，所谓Function overloading，只不过是让诸多函数定义可以采用相同的名称，事实上你还是要选择好几份不同（但是逻辑十分相似）的函数代码。为了能够“以相同的算法处理不同类型的数据”，比较好的做法是，把参数的类型也看作是一种参数。C++ template可以做到这一点，稍后我来谈这个非常棒的主题。

## 运算符重载

### （Operator Overloading）

与function overloading异曲同工的是，C++提供所谓的Operator Overloading（运算符重载）。所谓operators是像+（加）-（减）*（乘）/（除）>>（右移）<<（左移）之类的符号，代表一种动作。

面对operators，我们应该把 它想象成一种函数，只不过形式上比较特殊罢了。一般函数的（输入）参数出现在括号（）中，而operator的参数围绕一个operator符号（之前或之后）。内置（bullet in）数据类型（如 int、float、long 等）的operators由语言本身提供，并非借助任何的libraries。

C++允许程序员为 class type’s objects设计专用的operators，使objects的操作能像内置类型一样地自然和顺畅。让operator以多种姿态呈现，但顾昊不变，这就是operator overloading。

Operator overloading的存在一直褒贬不一。赏识者认为它能给程序代码带来更精简漂亮的形式，批评者则认为它容易把程序员搞迷糊掉。但是，我想，谁都不否认相面这样的程序代码实在是挺“精简漂亮”的：

```c++
CString str1("Hello, I am J.J.Hou, ");
CString str2("How are you?");
CString str3 = str1 + str2; // + 是 CString 的一个 overloaded operator
// 于是 str3 为 "Hello, I am J.J.Hou, How are you?"
int i = 5;
double pi = 3.141592653;
cout << "i=" << i << " and pi=" << pi << endl;
// 于是 console 屏幕上出现 "i=5 and pi=3.141592653"。
```

现在让我示范一个overloaded operator的做法，只要在先前的*Cpoint*声明中加入以下的函数定义，即可完成*Cpoint*的“加法运算符”：

```c++
CPoint operator+(const CPoint& pt) const
{
	return CPoint(_x + pt._x);
}
```

以operator开头的函数，表示是个运算符。加法运算符需要左右两个操作数（operands），左操作数即成为本函数的this对象（关于this，第2章p.100有详细的说明），右操作数则是本函数的参数*pt*。上述函数代码如果像下面这样表示，会比较清晰，但是两者其实相同（因为this是个隐藏参数）：

```c++
CPoint operator+(const CPoint& pt) const
{
	return CPoint(this->_x + pt._x);
}
```

有了上述的Cpoint::operator+，现在你可以这么使用它：

```c++
CPoint pt1(7.2), pt2(5.3), pt3;
pt3 = pt1 + pt2;
cout << "pt3.x = " << pt3.x() << endl; // 12.5
```

最著名而且使用频率最高的overloaded operators大概非<<和>>莫属。C++ *iostream*把所有内置类型对标准输入输出的读写动作都以这两个operators表示，带给我们极大的便利，可以取代形式麻烦的*printf()*和*scanf()*。

### Insertion Operator （ << ） for stdout

<<本来是左移运算符，但是在C++标准的*iostream*中被改头换面，其左运算符（operand）被指定为cout（console output device），右运算符是一个内置类型的objects。我可以利用它很方便地对cout连续输出各种内置类型的数据（也是一种objects），不必像C程序那样需要记忆不同类型的数据在*printf()*中的格式控制符（如%s、%d、%f……）：

```C++
#include <iostream.h>
...
int i = 5;
double pi = 3.141592653;
cout << "i=" << i << " and pi=" << f << endl;
```



[^3]: &是所谓的reference，一个像pointer之类的东西。稍后我会介绍。

### Extraction Operator （ >> ） for stdin

\>>本来是右移运算符，但是在C++标准的*iostream*中被改头换面，其左操作数（operand）是cin（console input device），右操作数是一个内置类型的object。例如：

```C++
#include <iostream.h>
...
float f;
cout << "f = ? ";
cin >> f;
```

程序使用者在键盘上的输入，会经由cin装置流向*f*变量，其作用相当于C runtime library中的*scanf()*。

### Insertion Operator （ << ） for file

就像*iostream*支持对user's terminal的输入输出一样，C++另有一组 class types支持对文件的输入和输出：

1. *ifstream*衍生自*istream*，支持文件的输入。
2. *ofstream*衍生自*ostream*，支持文件的输出。
3. *fstream*衍生自*iostream*，支持文件的输入输出。

如果需要这些class type，必须包含相应的header file。请注意，*fstream*已经包含了*iostream*，所以我们不需要重复包含。

下面是将数据输入到文件的一个简单示例：

```C++
#0001 #include <fstream.h> // include iostream.h
#0002
#0003 int main()
#0004 {
#0005 	cout << "All your keyboard input in monitor "
#0006 	<< "will into copy.txt.\n"
#0007 	<< "Terminate by ^Z\n\n";
#0008
#0009 	ofstream outFile("copy.txt");
#0010 	if (!outFile) {
#0011 	cout << "Cannnot open copy.txt for output" << endl;
#0012 	return -1;
#0013 	}
#0014
#0015 	char c;
#0016 	while (cin.get(c)) // get each char from keyboard
#0017 	outFile << c; // outFile.put(c) 亦可
#0018
#0019 	return 0;
#0020 }
```

当程序运行起来，会先给一个提示，然后接收来自键盘（）的输入，一一将这些输入放进“copy.txt”当中，直到接收^z（文件结束）才停止。

### Extraction Operator （ >> ） for file

下面是将文件内容输出到屏幕上的一个简单示例：

```c++
#0001 #include <fstream.h> // include iostream.h
#0002
#0003 int main()
#0004 {
#0005 	cout << "All text in copy.txt "
#0006 	<< "will stream into monitor.\n\n";
#0007
#0008 	ifstream inFile("copy.txt");
#0009 	if (!inFile) {
#0010 	cout << "Cannnot open copy.txt for input" << endl;
#0011 	return -1;
#0012 }
#0013
#0014 	char c;
#0015 	while (inFile.get(c)) // get each char from file
#0016 	cout.put(c); // output to monitor
#0017
#0018 	return 0;
#0019 }
```

当程序运行起来，会先给一个提示，然后从文件“copy.txt”中一一读出每一个字，显示在屏幕上。上述L15如果改为：

`while (inFile >> c) // get each char from file`

虽然也能读出字符，但空白的字符会被忽略，并非我们所期望的。

在*iostream*和*fstream*之中绝不止提供operator<<和operator>>而已，还有许多其他的member functions，例如*get()*、*put()*、*peek()*、*getline()*、*putback()*……

![插入图片]()

> fstream的operator<<和operator>>只接受C++内置类型。如果你这么做：
>
> ```c++
> CPoint aPoint; // CPoint is your class type
> outFile << aPoint;  // OO persistence!
> //第5章即将要完成类似的行为
> ```
>
> 在Visual C++编译器中会得到以下错误提示信息：
>
> error C2679: binary '<<' : no operator defined which takes a right-hand operand of type 'class Object' (or there is no acceptable conversion)

## 符号的特殊处理

### （Name Mangling）

如果函数名称都相同（也就是被overloaded），编译器在面对你的函数调用动作时，究竟如何才能决定哪一个函数定义应该被“激活”呢？

事实上，编译器把所有同名的overloaded functions视为不同的函数，并且以特殊的方式对它们的函数名称做了手脚。以四个*Add()*为例：

```c++
int Add(int a, int b);
long Add(long a, long b);
double Add(double a, double b);
float Add(float a, float b);
```

编译器真正为这四个函数所编码出来的函数名称是：

?Add@@YAHHH@Z[^4] 

?Add@@YAJJJ@Z

?Add@@YANNN@Z

?Add@@YAMMM@Z

再以*Cpoint*的两个x()为例：

```c++
class CPoint {
public:
	...
	float x();
	void x( float xval );
};
```

编译器真正为它们编出来的函数名称是（其中包含class名称、参数个数与类型）

?x@CPoint@@QAEMXZ

?x@CPoint@@QAEXM@Z

[^4]: 不同的编译器有不同的函数名编码方式，本例使用Visual C++。如果你想观察这些“真正的”函数名称，它们被记录在map文件中。产生map文件，以Visual C++为例，你只需在link.exe后面加上/map:filename选项，即可导出filename.map。

这样的“函数名改变”的行为称为“name mangling”。遗憾的是，不同的编译器有着不同的name mangling做法。这当然不好，表示我以Microsoft C++编译器开发出来的程序没办法调用一个以Borland C++编译器编译完成的函数。换句话说，函数名称并不存在着binary standard，这就严重影响了software components[^5] 的发展。

Naming Mangling不只作用在overloaded functions身上，C++程序汇总所有的global函数以及classes中所有的members，都会被naming mangling作用。

如果要抑制naming mangling的作用，那么就必须加上修饰词*extern “C”*。

[^5]: Software components的美好愿景是，希望开发出来的软件组件（软件IC）能够通行于 不同的编译平台，甚至打破不同程序语言的藩篱，这就需要binary standard。请参考***Essential COM***（Don Box/Addison Wesley/1998。中译本**COM本质论**，侯俊杰译，基峰，1998）

### extern "C"

对程序员来说，本来不必在意 Naming mangling。但是如果你的C++程序想要输出（export）某些东西给外界使用， naming mangling就会带来困扰。

能够在一个C++程序中输出（export）的东西，无非是functions和classes（data的输出倒是也有可能，但是 既然提倡“数据封装”，输出data是极为罕见并且应该避免的事情）。Classes无论是否输出functions，我们就不希望它被naming mangling作用，因为我们不希望这些functions的使用者被限制使用“与functions的编程者相同的开发工具”。毕竟，到目前为止，各家C++编译器厂商并没有协调出一个统一的name mangling做法。

因此，对于即将输出（exported）的函数，为了抑制naming mangling作用，我们必须加上特殊的修饰词*extern “C”*，例如：

```c++
extern "C" int Add(int a, int b);
```

观察map文件，就可以看见这个*Add()*函数的真实名臣是为*_Add*[^6] ，而不是奇奇怪怪的 ?Add@@YAHHH@Z。如果你这么写：

```c++
extern "C" int Add(int a, int b);
extern "C" long Add(long a, long b);
```

[^6]: 在每一个函数名称之前加上‘_’是C编译器对于C函名称的传统做法；C++也延续这个传统。曾经以assembly汇编语言调用C函数的朋友，想必对此不陌生。

编译时会报错（以Visual C++为例）：

error C2733: second C linkage of overloaded function 'Add' not allowed

通常，准备输出（exported）的functions会被设计在DLLs（动态链接库）之中，因此我们比较有机会再DLLs中看到*extern “C”*的使用。举个例子，如果我以C++语言来写Windows DLLs，并且决定写*DllMain()*（这其实并非必要，视需求而定），由于这个*DllMain()*是callback函数（由操作系统调用的函数），其名称与格式都已经固定，为了阻止C++比那一起在我的*DllMain()*上施以name mangling作用，我必须加上这么一行：

```c++
extern "C"
int APIENTRY DllMain(HINSTANCE hInst, DWORD dwReason, LPVOID lpReserved)
{
	...
}
```

[^7]: APIENTRY是什么？在\MSDEV\INCLUDE\WINDEF.H中有这样的定义：#define WINAPI __stdcall （一種 calling convention，適用於 win32） #define APIENTRY WINAPI

## 三种封装层次： private 、 protected 、 public

再以*Cpoint*为例：

```c++
#0001 class CPoint {
#0002 public:
#0003 	CPoint( float x = 0.0 ) : _x( x ) { }
#0004
#0005 	float x() { return _x; }
#0006 	void x( float xval ) { _x = xval; } 
#0007 	// ...
#0008 protected:
#0009 	float _x;
#0010 };
```

我们看到calss的声明中有public:和protected:两个关键字。这一类关键字用来修饰members（包括data和functions）的访问层次，也就是封装程度。这类关键字一共有三个：

- private：表示以下members属于class私有，不允许在class范围以外被调用；
- protected：表示以下members除了对自己这个class及其derived class开放外，其余一律免谈；
- public：表示以下members对外界公开，不限于class内部使用。

下面是封装层次的一个整理：
| 封装（保护）层次 | public | protected | private |
| ------ | ------ | ------ | ------ |
| in class (or friend)： | 可存取 | 可存取 | 可存取 |
| derived class： | 可存取 | 可存取 | 不可存取 |
| 程序其他地方 | 可存取 | 不可存取 | 不可存取 |

封装程度是可以继承的。我们可以在classes的继承关系上，指定一个封装关键字，作为继承，例如：

```c++
class CPoint2d : public CPoint { ... };
```

或是

```c++
class CPoint2d : protected CPoint { ... };
```

或是

```c++
class CPoint2d : private CPoint { ... };
```

至于“大体继承”和“细节继承”之间的交互作用，可以参考下面这张表：

| class的继承方式：base class封装层次 | private | protected | public |
| ------ | ------ | ------ | ------ |
| public | private | protected | public |
| protected | private | protected | protected |
| private | N/A | N/A | N/A |

图中是“base class封装层次”与“derived class”继承方式交互作用下，base class members对于derived class的封装层次。

[^8]: class的继承，将在稍后讨论。

## Struct和Class的差异

C++的struct其实和class地位几乎完全相同，这恐怕和大家的传统印象不大相同。C++ struct一样可以有members functions，一样可以有data members。只不过class members的预设封装层次是private，而struct members的预设封装层次是public。

预设归预设，如果你有特别指定，struct和class两者可以彼此完全取代。举个例子，前面的class *CPonit*可以改写如下，意义完全相同：

```c++
#0001 struct CPoint {
#0002 public:
#0003 CPoint( float x = 0.0 ) : _x( x ) { }
#0004
#0005 	float x() { return _x; }
#0006 	void x( float xval ) { _x = xval; }
#0007 	// ...
#0008 protected:
#0009 	float _x;
#0010 };
```

C++ struct的一个最大应用，我想大概是COM interfaces了。COM（Component Object Model）要求class的接口与实现要彻底分开（这么一来，接口部分才能有binary standard），而接口部分既然是要完全对外公开，根本不会（不该）有private members，所以COM interface以C++ struct来完成是非常合适的。COM programming中有一个**interface**关键字，事实上，它就是struct。你可以在OBJBASE.H（COM程序一定要包含的一个文件）中找到这一行：

```C++
// in MSDEV\INCLUDE\OBJBASE.H
#define interface struct
```

我将在第6章谈到COM的基本精神，并实现一个COM主体程序。

## Objects的诞生

###  （ Objects Instantiation ）

根据前述对class *CPoint*的声明，我们可以产生一个object（对象），或是说产生一个instance（实例）：

```c+
CPoint aPoint(7.2); // aPoint._x 初始值为 7.2
aPoint.x(5.3); // aPoint._x 现在值为 5.3
```

这样的objects可能放在程序的stack栈中（如果上面的代码位于函数范围内），也可能放在程序的data segment中（如果上面的代码位于函数范围外）。

我也可以这样产生一个objects：

```c++
CPoint* pPoint = new CPoint(3.6); // pPoint->_x 初值為 3.6
pPoint->x(5.3); // pPoint->_x 現值為 5.3
delete pPoint; 
```

利用*new* operator所产生的objects，存放在程序的heap堆中。

不管那一种方式，我们把“根据某个class，产生出一个object”的动作称为instantiation（实例化）。Object的诞生时刻和消亡时刻，各会自动引发class中某个特殊的member function，称为constructor和destructor。稍后我再来细谈这两个特殊函数的写法。

- Constructor：object诞生时会被自动调用的class member functions。此函数命名必须与class相同，参数可自定，没有返回值。Class可以拥有一个以上的constructors。其中无参数的那个被特别称为default constructor；只有一个参数并且该参数以该class为类型，特别被称为copy constructor。
- Destructor：object生命结束时会被自动调用的class member function。一个class只能够有一个destructor，没有参数，没有返回值，其命名必须与class相同，并以~为前置符号。

## Objects的生命周期

###  （ Scope of Objects ）

由于objects可能位于stack 或 heap 或 data segment 之中，所以objects的生命周期就有了些许差异。

- 放在stack中的称为local objects，它的生命随着objects的产生而开始，随着所在函数的结束而结束。
- 放在data segment之中的称为global objects，它的生命随着程序的开始而开始（比程序进入点还早），随着程序的结束而结束。
- 放在heap之中的称为heap objects，它的生命随着*new* operator而展开，醉着*delete* operator而结束。
- 下面这个例子出现刚刚所提的三种不同生命周期的objects。从程序的运行结果，我们可以清楚看到三种objects的生命周期范围。其中，用到的constructors（构造函数）和destructors（析构函数）将在稍后详细介绍。

注意：#0027 的 static object将于本章稍后提到，请暂时略过。程序中的标记请对照运行结果。

```c++
#0001 #include <iostream.h>
#0002 #include <string.h>
#0003
#0004 class CDemo
#0005 {
#0006 public:
#0007 	CDemo(const char* str); // constructor
#0008 	~CDemo(); // destructor
#0009 private:
#0010 	char name[20];
#0011 };
#0012
#0013 CDemo::CDemo(const char* str) // constructor
#0014 {
#0015 	strncpy(name, str, 20);
#0016 	cout << "Constructor called for " << name << '\n';
#0017 }
#0018
#0019 CDemo::~CDemo() // destructor
#0020 {
#0021 cout << "Destructor called for " << name << '\n';
#0022 }
#0023
#0024 void func()
#0025 {
#0026 	CDemo LocalObjectInFunc("LocalObjectInFunc");  ⑤
#0027 	static CDemo StaticObject("StaticObject"); ⑥
#0028 	CDemo* pHeapObjectInFunc = new CDemo("HeapObjectInFunc");  ⑦
#0029
#0030 	cout << "Inside func" << endl;  ⑧
#0031 ⑨
#0032 }
#0033
#0034 	CDemo GlobalObject("GlobalObject");  ①
#0035
#0036 void main()
#0037 {
#0038 	CDemo LocalObjectInMain("LocalObjectInMain");  ②
#0039 	CDemo* pHeapObjectInMain = new CDemo(" HeapObjectInMain ");  ③
#0040
#0041 	cout << "In main, before calling func\n";  ④
#0042 	func();
#0043 	cout << "In main, after calling func\n"; ⑩
#0044 ① ② ③
#0045 }
```

运行结果如下（注意，上例中有*new*的动作，却忘了*delete*，是个错误的示范）：

```
1.  Constructor called for GlobalObject  ①
2.  Constructor called for LocalObjectInMain  ②
3.  Constructor called for HeapObjectInMain  ③
4.  In main, before calling func  ④
5.  Constructor called for LocalObjectInFunc  ⑤
6.  Constructor called for StaticObject  ⑥
7.  Constructor called for HeapObjectInFunc  ⑦
8.  Inside func ⑧
9.  Destructor called for LocalObjectInFunc  ⑨
10. In main, after calling func ⑩
11. Destructor called for LocalObjectInMain  ①
12. Destructor called for StaticObject  ②
13. Destructor called for GlobalObject  ③
```

## Pointers与References

> Pointer译为“指针”，相当贴切。Reference译为“引用”，我总觉得不妥，字里行间很容易让人搞不清楚是动词还是名词、是一般的“引用”还是一个C++专有名词。所以，我将使用英文reference。连带地，在这一节中我也使用英文pointer

### Pointers

Pointer是一个“指示器”，可以用来只想任何一个objects，包括一般的变量：

```c++
int i = 3;
int* pi = &i;
cout << pi << endl; // 0x0064FDF0
cout << *pi << endl; // 3
```

此时 *pi* 本身是 *i* 的地址（在32位系统上的地址是32位）。想要取出 *pi* 所指的object，可使用*运算符（解引用，dereference operator）。

Pointer可以用来指向任何一个class type objects。由于下面的 *pPoint* 指向一整个大结构（一个object），如果要取其中的members（data members 或 member functions皆可——只要其封装层次是public），必须使用->运算符（箭头，arrow operator），例如：

```c++
class CPoint { public: float _x, _y, _z; };
CPoint* pPoint = new CPoint;
cout << pPoint << endl; // 0x00770560
pPoint->_x = 9.28;
cout << pPoint->_x << endl; // 9.28
```

Pointer甚至可以指向一个空对象（void）：

```c++
void* pv;
pv = malloc(1024); // 配置 1024 bytes（來自 heap）
cout << pv << endl; // 0x00760A14
```

如果要提取 *pv* 指向的对象，不可以，会出现编译错误：

```c++
cout << *pv << endl; // error C2100: illegal indirection
```

也就是说，pointer本身并没有意义（好像尺度本身没有意义一样），它的意义来自于它的类型（好像尺度的意义来自于坐标单位一样）。因此，将pointer前进一个单位，究竟是前进多少个bytes呢？必须视pointer的类型而定。如果将前述的int **pi* 和CPoint *pPoint各加1，得到：

```c++
cout << ++pi << endl; // 0x0064FDF4，比原先增加 4
cout << ++pPoint << endl; // 0x0077056C，比原先增加 12
```

这是因为在32位系统中，int为4字节，而我们所设计的 *CPoint* 里面有三个float数据，大小为12bytes，所以各指针加1时，分别前进4 bytes和12bytes。

如果把一个指向空对象的指针（void*）加1，会得到什么结果呢？不会有结果，只会有编译错误：

```c++
cout << ++pv << endl; // error C2036: 'void *' : unknown size
```

当然，如果你做了强制类型转换，就可以解决空对象的问题，因为你赋予了该指针一个明确的类型，例如：

```c++
double* pd = (double*)pv; // 强制类型转换。double is 8 bytes.
cout << pd << endl; // 0x00760A14
cout << ++pd << endl; // 0x00760A1C，比原先增加 8
```

[^9]: member functions并不包括在object大小之内。第2章会详细说明所谓的对象模型。

下表是上述实验结果的一个整理：

| pointer类型               | pointer值  | ++pointer  | object大小                  | 取值（dereference）        |
| ------------------------- | ---------- | ---------- | --------------------------- | -------------------------- |
| int* pi;                  | 0x0064FDF0 | 0x0064FDF4 | 4 bytes                     | 可                         |
| CPoint* pPoint;           | 0x00770560 | 0x0077056C | 12 bytes                    | 可                         |
| void* pv;                 | 0x00760A14 | error      | error: 'void *'unknown size | error: illegal indirection |
| double* pd = (double*)pv; | 0x00760A14 | 0x00760A1C | 8 bytes                     | 可                         |

当我们开始设计classes继承体系，有许多时候需要将一个pointer指向一个类型不符的object（但彼此类型之间有继承的关系存在。这其实正是polymorphism的一个精髓，我将在第2、4章详细讨论它），这时候类型的转换就非常重要。强制类型转换（C程序总是用这一招）太过粗暴，在不够安全的时候仍然强硬转换，是有危险的。C++需要其他更精致的类型转换工具（请看第3章）。

Pointer不但可以指向object，还可以指向class的data members或member functions。它们的形式有点奇怪，结果也可能出乎意料。我将在第2章p.116和p.117详细介绍这一部分。

### References

与pointer常常相提并论并且两者常常混淆不清的是所谓的reference。Reference（&）像是一个常数指针，可以被自动取值（dereference）。下面这个例子使 *r* 成为 *x* 的一个reference：

```c++
int x;
int& r = x; // r is a reference of x
```

当一个reference产生，它必须被初始化为某个原来已经存在的object，像上面那样。如果我写：

```c++
int& q = 12;
```

那么编译器会现分配一块int内存空间，将该内存空间的值设为12，然后把 *q* 这儿reference“绑定“在该空间上。重点是，任何reference都必须被”绑定“在某个空间，成为一个”化身（副本）“。当你处理该reference，你就是在处理那个被绑定的空间。如果：

```c++
int x = 0;
int& r = x; // r is a reference of x
int* p = &x;  // p is a pointer to x
r++;  // 请注意：sizeof(r) == sizeof(x)
```

那么 *r* 和 *x* 的值都变为1，因为增加 *r* 的值就是增加 *x* 的值。

![]()

面对reference，最简单的想象就是，把它幻想成一个形式漂亮的pointer。这个”形式漂亮的pointer“好处是，我们不需要担心它是否被初始化（编译器会强制这么做），也不需要担心如何提取（dereference）它（同样地，编译器会负责）。

Reference虽然本质上是个指针，在形式上却是个object。也就是因为其形式漂亮而本质实用（用于call by reference，或称call by address），所以reference常被用于函数的参数列表（arguments list）和返回值（return value）。下面是个例子：

```c++
#0001 int* func1(int* x)
#0002 {
#0003 	(*x)++;
#0004 	return x;
#0005 }
#0006
#0007 int& func2(int& x)
#0008 {
#0009 	x++;
#0010 	return x;
#0011 }
#0012
#0013 main()
#0014 {
#0015 	int a = 0;
#0016
#0017 	// ugly but explicit，你可以清楚看到传给 func1() 的是个指针。
#0018 	// 返回值的形式也很“难看”。
#0019 	cout << *func1(&a) << endl; // output: 1
#0020
#0021 	// clean but hidden。传给 func2() 的其实是个指针（借助 reference），
#0022 	// 但你看不出来。返回的也是指针（借助 reference），你也看不出来。
#0023 	cout << func2(a) << endl; // output: 2
#0024 }
```

Reference对于copy constructor的设计十分重要，我将在稍后介绍copy constructor。至于为什么copy constructor需要reference，其底层机制请参考***Thinking in C++***（Bruce Eckel / Prentice Hall）一书第9章：Pointers & references。

## Constructors & Destructors

### 构造函数和析构函数

当object诞生，有一个名为constructor的函数会自动地运行。当object毁灭，有一个特殊的称为destructor的程序会自动运行。Constructor可以不只一个，但destructor只能有一个。

Constructor就是与class同名的那些member functions，例如 *CPoint::CPoint()* 或  *CDemo::CDemo()* 。constructor不能够指定返回值的类型，也就是说它不必（但可以）return。你可以拥有一个以上的constructors，各有不同个数以及不同类型的参数（s）。

destructor（析构函数）就是与class同名并且前面加上“~”符号的那个member function，例如*CPoint::~CPoint()* 或 *CDemo::~CDemo()*。destructor不能够指定返回值类型，也就是说它不必（但可以）return。每一个class只能拥有一个destructor，并且不能有任何参数。

由于global object的诞生比程序进入点更早，所以global object的constructor执行时机更早于程序的进入点。这一点你一定已经在p.21“object的生命”一节实例中注意到了。

### Default Constructors

所谓default constructors就是在没有指定任何参数的constructor。如果我们的class *CA* 声明如下：

```c++
class CA {
public:
	int getdata() { return _data; };
	CA() { _data = 5; }; // default constructor
	CA(int val) { _data = val; }; // constructor
protected:
	int _data;
};
```

*CA* 拥有两个constructors，分别是 *CA(int)* 和 *CA()* 。后者没有参数，正是所谓的default constructor。当我产生一个 *CA* object而没有指定任何参数：

```c++
CA aCA;
CA* pCA = new CA;
```

编译器就为我调用default constructor。

普遍存在于C++程序员之间的一个误解是：如果我们没有为某个class设计constructor，编译器就会自动为该class制造出一个default constructor来。这个说法对也不对，这里我先提示一个结论（稍后会有更多细节探讨）：

------

如果一个class没有任何user-defined constructor，那么编译器可能会为次classes自动做出一个default constructor。这一由编译器合成出来的default constructor只负责完成编译器所需的动作，无法照顾user（programmer）的可能需求。

------

什么是“编译器所需”的动作？就是隐藏在C++程序代码语句后面，让C++诸多特性得以实现的动作。包括以下三种情况：

1. class *CA* 内含class *CZ* objects

   当你产生一个*CA* object，隐藏在底层的必要操作是先将*CZ* objects初始化（因为*CA* object之中有一个*CZ* object）。

2. lass *CA* 继承自class *CZ*

   当你产生一个*CA* object，隐藏在底层的必要操作是，调用 *CZ* 的default constructor（因为*CA* object之中有一个*CZ* subobject）。

3. *CA* 是一个polymorphic class，也就是说它拥有virtual functions，或者继承自拥有virtual functions的class（es）（关于virtual，稍后有一节说明）。

   当你产生一个*CA* object，隐藏在底层的必要操作是：将虚拟机制所需的vptr和vtbr初始化（第2章对详细的介绍）。

由于这三种情况皆有所谓“底层的必要操作”，所以编译器必须自动为class *CA* 合成一个default constructor（如果没有任何user-defined constructor的话），或暗中对已有的user-defined constructor动手脚（添加一些代码）。合成出来的东西称为 implicit **nontrivial** default constructor（这是C++ Standard的用语）。

如果没有上述需求，编译器就不会为class产生一个default constructor。例如：

```c++
class CB {
public:
	int getdata() { return _data; };
	protected:
	int _data;
};
```

*CB* 没有符合虚拟机制（它没有virtual functions，也不是继承自拥有virtual functions的base classes），也没有内含embedded objects，也没有继承自其他class，所以编译器不会为它合成一个default constructor出来。于是当我这么做：

```c++
CB aCB;  // 应该调用 default constructor
cout << "aCB.getdata()=" << aCB.getdata() << endl;
```

得到的结果是：

```c++
aCB.getdata()=4211382 // 莫名其妙的初值
```

这恐怕不是我所期望的。我期望的*_data*有个初值（最起码也该为0吧），但这是程序员的需求，不是编译器的需求，所以我必须自求多福，明白地设计一个default constructor：

```c++
class CB {
public:
	int getdata() { return _data; };
	CB() { _data = 5; }; // default constructor
protected:
	int _data;
};
```

现在再执行以下操作，就符合我们对初值的期望了：

```c++
CB aCB;  // 应该调用 default constructor
cout << "aCB.getdata()=" << aCB.getdata() << endl;
// 输出结果：aCB.getdata()=5
```

注意，如果class如果有了任何constructor但非default constructor，编译器绝不会为它合成一个default constructor。如果class *CZ* 正是如此一个class，那么当你想要生成一个*CZ* object并且没有指定参数时：

```c++
CZ *aCZ; // error
```

编译器会发出抱怨的声音：

error C2512: 'CZ' : no appropriate default constructor available

> 注意：本小节对于default constructor的合成条件，只是一个概略性的介绍。*Inside the C++ Object Model* （Lippman/Addison Wesley/1996）第2章对此有非常详细的描述与归纳。

### Copy Constructors

所谓copy constructors，是指那种“众多参数之中，有一个参数的类型是其class type”的constructor。例如：

```c++
#0001 class CA {
#0002 public:
#0003 	int getdata() { return _data; };
#0004 	CA() { _data = 5; }; // default constructor
#0005 	CA(int val) { _data = val; }; // constructor
#0006 	CA(const CA& ca) { // copy constructor
#0007 		cout << "copy constructor" << endl;
#0008 		_data = 10;
#0009 	};
#0010 protected:
#0011 	int _data;
#0012 };
```

以下两种情况，会调用copy constructor：

- 情况1：将一个object当作参数传给某一个函数。
- 情况2：将一个object当作函数返回值。

例如（沿用上面的class *CA*）：

```c++
#0001 void foo2(CA aCA) // 情況 2（函数参数是个 object）
#0002 {
#0003 	cout << "in foo2(), aCA.getdata()=" << aCA.getdata() << endl;
#0004 }
#0005
#0006 CA foo3()
#0007 {
#0008 	CA aCA(3);
#0009 	cout << "in foo3(), aCA.getdata()=" << aCA.getdata() << endl;
#0010 	return aCA; // 情況 3（函数返回值是个 object）
#0011 }
#0012
#0013 void main()
#0014 {
#0015 	CA aCA1, aCA2(7);
#0016 	cout << "aCA1.getdata()=" << aCA1.getdata() << endl;
#0017 	cout << "aCA2.getdata()=" << aCA2.getdata() << endl;
#0018
#0019 	aCA2 = aCA1; // object assignment
#0020 	cout << "aCA2.getdata()=" << aCA2.getdata() << endl;
#0021
#0022 	foo2(aCA1); // 情況 1（调用之前，aCA1._data 为 5）
#0023 	aCA2 = foo3(); // 情況 2
#0024 	cout << "aCA2.getdata()=" << aCA2.getdata() << endl;
#0025 }
```

让我们看看运行结果：

```c++
aCA1.getdata()=5
aCA2.getdata()=7
aCA2.getdata()=5 // 经过 object assignment 之后
copy constructor
in foo2(), aCA.getdata()=10 // 经过情况1之后
in foo3(), aCA.getdata()=3
copy constructor
aCA2.getdata()=10 // 经过情况2之后
```

这里还有几点值得注意的地方：

- L19将一个object赋值（assign）给另一个object，这也是一种复制，但它调用的是所谓的 copy assignment operator。本例并没有特别设计 copy assignment operator。
- 调用 *foo2()* 之前，*_data* 为5，进入 *foo2()* 之后在打印，已变成10，可见 copy constructor的确在 *foo2()*的参数复制时（情况1）发生。
-  *foo3()* 内有一个 local object，其*_data* 为3，当把它作为返回值传出，却变成了10，可见 copy constructor的确在 *foo3()*的返回值复制时（情况2）发生。

当然，上述class *CA* 的copy constructor设计内容是非常失败的，故弄玄虚，简直是搬起石头砸自己的脚。为的是彰显copy constructor的发生。

许多人对于 copy constructor有着和对default constructor一样的误解，以为如果没有user-defined copy constructor，编译器会自动为我们生成一个。事实上，编译器只有在**必要的时候**，才会这么做。

什么是**必要的时候**呢？就是当class不再展现“bitwise copy”意义的时候。

### bitwise copy — 逐位拷贝

所谓“bitwise copy”，是指“一个位一个位”地完整拷贝。一般的objects拷贝当然是如此，例如本节的  class *CA*



Copy constructor 的实现与 class reference（&）有密切关联，关系到函数的调用和返回、中断发生的需求及可重入 （re-entrant）等细节。请参考***Inside The C++ Object Model***（Lippman/Addison Wesley/1996）一书第9章：Pointers & references。

> 注意，本小节对于copy constructor的合成条件，只是粗略地介绍。***Inside The C++ Object Model***（Lippman/Addison Wesley/1996）一书第2章对此有非常详细的描述与归纳。

## Class Members

在class的声明里，真正有用的两样东西（其实也就只有这两样东西）是data members和member functions：

- Data members表示根据这个class所产生的object（对象）里头会有什么东西，它事实上也是占据object内存空间的唯一物质（除非引入了虚拟机制）。通常为了数据的封装性，我们把data members声明为private或protected。
- Member functions是用来处理data members的函数。通常，为了接口的开放性，我们把member functions设计为public。

### Data Members （数据成员）

Data members的声明和一般 non-class的变量声明方式一样。以 *CPoint* 为例：

```c++
#0001 class CPoint {
#0002 public:
#0003 	CPoint( float x = 0.0 ) : _x( x ) { }
#0004
#0005 	float x() { return _x; }
#0006 	void x( float xval ) { _x = xval; } 
#0007 // ...
#0008 protected:
#0009 	float _x;  // data member
#0010 };
```

L9的 *_x* 就是一个 data members。

如果 data member不是一个一般的变量，而是一个class object，这种情况比较特殊，称为 **composition**（），而这种objects被称为 embedded objects或object member。Composition被用来描述

## Template

什么是template？重要性如何？Kaare Christian 在 1994/01/25 的 ***PC Magazine***上有一篇文章，把template说得生动又有趣，也指出了重点：

> 无性生殖并不只存在于遗传工程，对程序员而言，它也是一个由来已久的操作。过去，我们不过是以一个简单而基本的工具，也就是一个文字编辑器，剪剪贴贴地复制程序代码。今天，C++提供给我们一个更好的反之方法：template。
>
> 复制一段既有的程序代码，最平常的一个理由就是：为了改变数据类型。举个例子，假设你写了一个绘图函数，使用类型分别为long的x，y坐标；突然之间你需要相同的算法，但坐标值采用float。你当然可以使用一个文字编辑器把这段代码拷贝一份，然后改变其中的数据类型。你甚至可以使用C++的overloaded functions，那么就可以继续使用相同的函数名称。Function overloading的确使我们有比较清爽的程序代码，但你还是必须在程序的许多地方维护完全相同的算法。
>
> C语言对此问题的解决之道是：使用macros。但macros有它自己的缺点，第一，它只适用于简单的功能。第二个缺点比较严重：macros不提供参数类型检验，这与C++严格的类型检验格格不入。第三个缺点是：macros并非functions，程序中任何调用macros的地方都会被编译器的预处理器原原本本地插入macros所定义的那一段代码， 而并非只是一个function call。因此你每使用一次macros，你的执行文件就会膨胀一些。
>
> Templates提供比较好的解决方案，它把“一般性的算法”和其“对数据类型的实际依赖”区分开来。你可以先写出算法的程序代码，使用时才填入实际数据类型。C++ template使“数据类型”也以参数的姿态出现。有了template，我们可以拥有macros“只写一次”的有点，以及overloaded functions“类型检验”的优点。

Template的好处其实不仅在于，你可以利用template设计出一般化（泛型）的算法，适用于“目前存在”以及“尚未被设计出来”的某种数据类型（某种class type）。当然，如果让新开发的class type能够适用于比泛型算法，那些classes在设计时也必须配合某些事情。

稍后我就会从我所举的简单例子中看到这种伟大的弹性。

C++ template有两种，一种针对function，另一种针对class。

### Template Functions

假设我需要一个幂次方计算函数，名为 *power()* 。这个函数只接受正幂次方数，如果是负幂次方数，就让结果为0.

对于int数据，*power()* 函数应该是这样：

```c++
#0001 int power(int base, int exponent)
#0002 {
#0003 	int result = base;
#0004 	if (exponent == 0) return (int)1;
#0005 	if (exponent < 0) return (int)0;
#0006 	while (--exponent) result *= base;
#0007 		return result;
#0008 }
```

对于long数据，*power()* 函数应该是这样：

```c++
#0001 long power(long base, int exponent)
#0002 {
#0003 	long result = base;
#0004 	if (exponent == 0) return (long)1;
#0005 	if (exponent < 0) return (long)0;
#0006 	while (--exponent) result *= base;
#0007 		return result;
#0008 }
```

对于float我们因该……对于complex我们又因该……哦，我们为什么不能够把数据类型也变成参数之一，在调用函数时加以指定呢？是的，这就是template的妙用，语法如下：

```c++
template <class T> T power(T base, int exponent);
```

写成两行或许比较清楚：

```c++
template <class T>
T power(T base, int exponent);
```

这样的函数声明是以一个特殊的**template**字开始的，后面跟着一个参数列表（本例只有一个参数T）。容易让人迷惑的是其中的**class**字眼，它并不一定代表C++的class，它也可以是一个普通的（内置的）数据类型（当然啦，以退化的角度看，内置类型也是一种class）。<class T>的意思是：T是一种数据类型，次类型将在调用此函数时才由调用者指定。

下面就是 *power()* 的template版本。注意，返回值也必须温和template函数的声明：

```c++
#0001 template <class T>
#0002 T power(T base, int exponent)
#0003 {
#0004 	T result = base;
#0005 	if (exponent == 0) return (T)1;
#0006 	if (exponent < 0) return (T)0;
#0007 	while (--exponent) result *= base;
#0008 		return result;
#0009 }
```

下面是调用template函数的方法：

```c++
#0001 #include <iostream.h>
#0002 void main()
#0003 {
#0004 int i = power(5, 4);
#0005 long l = power(1000L, 3);
#0006 long double d = power((long double)1e5, 2);
#0007
#0008 cout << "i= " << i << endl;
#0001 #include <iostream.h>
#0002 void main()
#0003 {
#0004 int i = power(5, 4);
#0005 long l = power(1000L, 3);
#0006 long double d = power((long double)1e5, 2);
#0007
#0008 cout << "i= " << i << endl;
```

运行结果如下：

```
i= 625
l= 1000000000
d= 1e+010
```

在第一次调用中我指定T为int，在第二次调用中我指定T为long。而在第三次调用中T又变成了一个long double。这些template function都可以应付。但如果函数调用时调用者自己混淆了数据类型，像这样：

```c++
int i = power(1000L, 4); // 基值是個 long，傳回值卻是個 int。錯誤！
```

编译时就会出错。

Template函数的“类型参数”T究竟可以适应多少种类型？我要说，任何内置（build in）类型或用户自定（user defined）类型（也就是class，包括struct）都可以。如果是个class，此一class必须支持 *power()* 函数中对于该类型T的任何运算操作，否则编译器遇到那些运算符时，不知该怎么处理才好。

以 *power()* 为例，它对于类型为T的 *result* 变量和 *base* 变量有以下的运算：



所有的C++内置类型如int或long都支持上述运算操作。但如果你打算支持某个C++ class，该class必须拥有支持上述运算操作的member functions。

### Template Classes

除了template functions，我们还可以建立template classes，是它们能够神奇地操作任何类型的数据。下面这个例子，*CTree* 内含三个data members，*Min()* 返回其中的最小值，*Max()* 返回其中的最大值：

```c++
#0001 template <class T>
#0002 class CThree
#0003 {
#0004 public :
#0005 	CThree(T t1, T t2, T t3);
#0006 	T Min();
#0007 	T Max();
#0008 private:
#0009 	T a, b, c;
#0010 };
```

请你把T看成是熟悉的int或float，语法就不至于稀奇古怪了。以下是上述三个member functions的定义：

```c++
#0001 template <class T>
#0002 T CThree<T>::Min()
#0003 {
#0004 T minab = a < b ? a : b;
#0005 return minab < c ? minab : c;
#0006 }
#0007
#0008 template <class T>
#0009 T CThree<T>::Max()
#0010 {
#0011 T maxab = a < b ? b : a;
#0012 return maxab < c ? c : maxab;
#0013 }
#0014
#0015 template <class T>
#0016 CThree<T>::CThree(T t1, T t2, T t3) :
#0017 a(t1), b(t2), c(t3)
#0018 {
#0019 return;
#0020 }
```

请注意，每一个member function都要在最前面加上 template <class T>，而且

## Standard Template Library （ STL ） or C++ Standard Library

C++提供一套标准的library，称为C++ standard library，完全以template完成，所以又被称为 Standard Template Library（STL）。这套library专注于实现常用的数据结构（例如array、list……）以及常用的算法（例如push、pop、delete、query、retrieval……）。

一般而言，STL内含六个主要的组件：

1. containers（泛型容器）
2. generic algorithms（泛型算法）
3. iterators（泛型指针）
4. function objects
5. adaptors
6. allocators

我在这一节简单介绍前三个组件，让大家领略一下template的强大弹性。

> 如果你希望对STL有更多的了解，本书第一页推荐的两本书籍：***The C++ Programming Langauge*** 3/e 和 ***C++ Primer*** 3/e，都涵盖有这个主题。另有一本STL专著也值得参考：***STL Tutorial and Reference Guide*** - C++ Programming with
> the Standard Template Library （David R. Musser & Atul Saini / Addison Wesley /1996）

## STL Containers

STL实现了一些常用的数据结构。这些结构用来容纳许多数据，所以也被称为container（容器），主要分为两大类：

1. sequence container：内含有序而类型一致的元素。例如**vector**（也就是**array**）和**list**，以及**deque**。deque的行为类似vector，但对于插入（insertion）和删除（deletion）第一个元素特别有效率。
2. associative container：这种数据结构对于查询（query）或取出（retrieval）某一个元素的操作特别有效率。例如**map**和**set**。所谓map是key/value的成对组合：key用来查询，value内含真正的数据。电话簿和字典都适合以mao来完成，所以map又被称为dictionary。

map和set中的每一个key都只能出现一次。如果key必须出现一次以上，另有所谓的**multimap**和**multiset**适用。

以下我举一个vector实例，让大家见识一下template calsses的弹性。

```c++
#0001 // build : cl /GX vector.cpp
#0002 #include <vector> // STL's vector
#0003 #include <iostream>
#0004
#0005 // all the components of the C++ standard library are
#0006 // declared and defined within a namespace called std.
#0007 using namespace std;
#0008
#0009 void main()
#0010 {
#0011 vector<int> ivec;
#0012
#0013 cout << "ivec: size: " << ivec.size() << " "
#0014 << "capacity: " << ivec.capacity() << endl;
#0015
#0016 for (int ix = 0; ix < 24; ix++)
#0017 {
#0018 ivec.push_back( ix );
#0019 cout << "ivec: size: " << ivec.size() << " "
#0020 << "capacity: " << ivec.capacity() << endl;
#0021 }
#0022 }
```

程序解释：

- L1.要使用STL，必须将
- L2. 使用 STL vector，必须包含 <vector> 这个头文件。以Visual C++为例，你可以在 \MSDEV\INCLUDE 中找到 <vector>文件。请注意，视你手上的编译器版本而定，也可能是vector.h（早期）。
- L7. STL 中所有的 classes、functions、templates、types 都定义在一个特殊的namespace **std** 之中（关于namespace稍后另有一节说明）。L7的用意是让我们的程序能够看到那个**std** namespace。
- L11.声明一个vector，名为 *ivec* ，内放类型为int的元素。
- L13.在尚未对 *ivec* 加入任何元素之前，检查其大小与容量。所谓大小是指vector内元素的个数，所谓容量（capacity）是指这个vector目前可容纳多少元素。
- L16-L20.加入24个元素到 *ivec* 中。每加入一个元素，就检查 *ivec* 的大小与容量。循环中我们用到了三个member functions：*push_back()*、*size()*、*capacity()*。

运行结果如下：

```
ivec: size: 0 capacity: 0
ivec: size: 1 capacity: 1
ivec: size: 2 capacity: 2
ivec: size: 3 capacity: 4
ivec: size: 4 capacity: 4
ivec: size: 5 capacity: 8
ivec: size: 6 capacity: 8
ivec: size: 7 capacity: 8
ivec: size: 8 capacity: 8
ivec: size: 9 capacity: 16
ivec: size: 10 capacity: 16
ivec: size: 11 capacity: 16
ivec: size: 12 capacity: 16
ivec: size: 13 capacity: 16
ivec: size: 14 capacity: 16
ivec: size: 15 capacity: 16
ivec: size: 16 capacity: 16
ivec: size: 17 capacity: 32
ivec: size: 18 capacity: 32
ivec: size: 19 capacity: 32
ivec: size: 20 capacity: 32
ivec: size: 21 capacity: 32
ivec: size: 22 capacity: 32
ivec: size: 23 capacity: 32
ivec: size: 24 capacity: 32
```

我们可以从运行结果归纳出两个结论：

1. Visual C++自带的STL（HP授权）的vector容量是可以变化的。
2. Visual C++自带的STL（HP授权）的vector容量变化，是以power of 2累增（以本例的vector大小而言）。

### STL Generic Algorithm （泛型算法）

为了应对STL的各种container，以及语言本身的内置类型，STL提供了一组所谓的generic algorithm（泛型算法）。称之为algorithm，是因为这些函数都用来执行最普遍的操作，诸如排序、搜索、最大值、最小值等等。称之为generic（泛型）则是因为，它们适用于各种container types，包括STL的vector、list、set、map，以及语言内置的build-in array type。

STL提供的generic algorithm非常多，我只举一个简单的“字符串翻转”实例。此例使用 *reverse()* 函数：

```c++
#0001 // build : cl reverse.cpp
#0002 #include <iostream.h>
#0003 #include <algorithm> // STL
#0004 #include <string.h>
#0005
#0006 using namespace std; // STL
#0007
#0008 void main()
#0009 {
#0010 	char* string1 = "J.J.Hou was graduated from NCTU in 1983";
#0011 	cout << string1 << endl;
#0012
#0013 	int N1 = strlen(string1);
#0014 	reverse(string1, string1 + N1);
#0015
#0016 	cout << string1 << endl;
#0017 }
```

运行结果;

```c++
J.J.Hou was graduated from NCTU in 1983
3891 ni UTCN morf detaudarg saw uoH.J.J
```

程序解释：

- L1.虽然我在上一个例子中曾说过，要使用STL，必须将 exception handling打开，但因为本例只用到generic algorithm，没有用到STL container，所以不需/GX 编译选项（for Visual C++）。
- L3.使用generic algorithm，必须包含<algorithm>这个文件。以Visual C++为例，你可以在 \MSDEV\INCLUDE 中找到 <algorithm>文件。请注意，视你手上的编译器的版本而定，也可能是algo.h（早期）。
- L6.由于STL的所有符号命名在**STD**这个namespace之中，我们必须指定有L6才能够“看”到std namespace。
- L10.C++字符串有两种，一种是C-style字符串，另一种是string类型（必须包含<string>）。本例使用C-style字符串。
- L14.使用 *reverse()* （一个generic algorithm）来翻转字符串。此函数的连个参数都是操作对象（某个container）的iterator，分别指向第一个和最后一个元素。所谓iterator是一种泛型指针，稍后我会介绍。此例的操作对象是传统字符串，其iterator就是一般的字符指针。

### STL Iterators （迭代器）

上例的“字符串反转”实例使用C-style字符串。所以一般的字符指针即可代表字符串（也被视为一个container）的最前与最后两个元素。

但是STL container的弹性很大，可以让你放置自定义类型的数据。STL container之所以能够和STL generic algorithm产生关系（链接），靠的是它的一对iterator（通常称为 *first* 和 *last* ），使generic algorithm能够在container身上来回移动（traverses）。

所谓iterator，可以把它想象成是一种泛型指针。它的作用是提供一般化的方法，使我们得以存取任何一种container中的任何一个元素。Iterator其实也是一个template class，它最少必须提供四个operators（以下 *iter* 代表一个iterator）：

1. increment operator：能够用以移向下一个元素，例如 *++iter*。
2. dereference operator：用以取出真正的元素个体，例如 **iter*。
3. equlity operator：用以判断两个iterators是否相等，例如 *iter1=*=*iter2*。
4.  inequlity operator：用以判断两个iterators是否不相等，例如 *iter1*！=*iter2*。

任何一个STL container type都必须提供两个member functions：*begin()* 和 *end()* ，返回的iterator分别指向container的第一个和最后一个元素。下面是以先前的vector<int> *ivec*为例，示范iterator的声明与使用;

```c++
vector<int> ivec;
...
vector<int>::iterator iter = ivec.begin();
vector<int>::iterator iter_end = ivec.end();
for (; iter != iter_end; ++iter)
	cout << *iter << endl;
```

最后的for loop可以把 *ivec* 的所有内容显示在屏幕上。

### 一个完整实例

以下我将示范STL list的用法。这里所举的例子可以和地4章“为什么需要多态”遥相呼应。

假设我想完成一个list，每一个节点是一个指针，指向我的一个“形状对象”。形状对象的class体系图如下：

![]()

架构完成之后，我希望把整个list遍历一遍，将所有的形状显示出来。图1.1是示范程序。

图1.1 Demo程序，示范STL list的使用方法

```c++
#0001 // demo.h
#0002 class CShape
#0003 {
#0004 public:
#0005 virtual void display() { cout << "CShape" << endl; }
#0006 };
#0007
#0008 class CRect : public CShape
#0009 {
#0010 public:
#0011 void display() { cout << "CRect" << endl; }

```

Demo程序解释：

- demo.h L5：由于这个例子应用了面向对象最重要的“多态与虚拟”特性（才能够调用每一个元素的 *display（)* 时调用适当的 *display()* 操作），所以 *display()* 必须声明为virtual。我将在第4章详述“多态与虚拟”。
- demo.h L41：表示我有一个list，其中每个元素都是一个指针，指向 *CSharp* object。这其实就是polymorphism的一种应用，第4章详述。
- demo.cpp L9~L15：一共有6个shapes，每一个都以base type *CShape*指向它。这也是所谓的polymorphism，第4章详述。
- demo.cpp L19~L20：把各种不同的“形状”放进list之中。*push_back()* 可以做到这一点。
- demo.cpp L23~L27：利用iterator将list遍历一遍，并调用每一个元素的 *display()* 函数。

## MFC Collection Classes

Template classes非常好用，真的非常非常好用，效率也不断在改善之中。但是C++ standard library绝对不是你的唯一选择。早在STL问世之前，就已经有一些商品化的class library，有着类似的功能与威力，它们也不断在演化进步之中。

这一节我将以MFC(Microsoft Foundation Classes)为例，说明这类class library的使用方法。

### 认识 MFC

MFC是一套融合了视觉对象（我指的是视窗、绘图）以及数据结构（我指的是像先前所说的container那样的东西），希望让我们开发Windows应用程序时得以轻松自如的C++ Class Library。在视觉对象部分，由于内含了Windows平台特有的消息驱动机制，以及“将数据实体与展示界面分离”的 Document / View结构，所以架构复杂，许多特别的classes很难单独使用（即使勉强这么做，也是大材小用），必须一股脑儿用一大堆。因此，这类classes library又被称为application framework（内聚性很高的一组classes）。

但在MFC中，另有一大组被称为collection classes的类，作用与STL相同，都是为了实现一般化的数据结构与算法。这组classes的用法比较简单，和视觉对象一点关系也没有，适用于GUI（Graphic User Interface）环境，也适用于CUI（Command User Interface）环境。图1.3就是MFC collection classes的架构体系图。

![]()

### MFC Collection Classes 应用实例

我将以MFC Collection Classes （图1.3）重新展现上一节图1.1的class hierarchy和图1.2所展示的程序功能。

MFC Collection Classes分为两大类：

1. 简单型：
2. 指针型： 

我将尽量选用图1.3中的MFC template classes来完成图1.4的功能，因为这些classes是“type-safe”。

### 修改程序

## RTTI （ Runtime Type Identification ）

所谓RTTI，意思是，在运行期间判断某个object或某个pointer属于何种类型。它是C++许多高级技术领域的基础。RTTI的主要形式在C++中是以 *typeid* 和 *dynamic_cast* operator展现。

### typeid operator



## Namespace（命名空间）

预设情况下，每一个object、每一个function、每一种type如果声明在global scope（全局，或者称为 global namespace scope）之中，就会拥有它自己的一个所谓的global entity。在同一个程序中，不管是不是位于一份文件，objects 或 functions 或 types的名称是不能够抵触的。这意味着你必须非常小心地不要和你所使用的libraries产生符号命名上的冲突。有些时候你想要小心谨慎也不可得，因为你所使用的libraries可能并没有在其文件中详细说明它的各种命名。这种因命名而造成的冲突在我们大量适应各家厂商制造的各式各样的libraries时尤其带来困扰。这种问题被称为“global name space pollution”。

> 所谓scope：在C++语言中，scope就是以左右大括号{}涵盖起来的空间。objects的生命周期，变量的生命周期，都以scope为依据。你可以在程序中使用{}任意产生scope。

解决命名冲突的方法之一是，我们在自己程序的每一个命名符号之前，加上专属的前置词。于是名称变得又臭又长，在输入和阅读上都形成一种负担。解决方式之二就是使用自定的namespace。语法如下：

```c++
namespace polymorphism_in_cplusplus
{
class CPoint { /* ... */ };
void printall(CPoint &);
}
```

出现在namespace之中的各种元素，包括objects、template、functions、types可以用相同的命名。编译器图和对待它们呢？编译器会把上述的两个namespace members改为全名如下：

1. polymorphism_in_cplusplus::CPoint
2. polymorphism_in_cplusplus::printall()

继续使用又臭又长的符号命名规则：

```c++
using namespace std; // std is STL’s namespace
```

请参考p.78程序代码。

以下我再举个小型实例，示范namespace的其他用法：

```c++
#0001 #include <iostream.h>
#0002
#0003 namespace ns1
#0004 {
#0005 class CPoint { /* ... */ };
#0006 void print(CPoint&) { cout << "ns1::print()" << endl; };
#0007 void output() { cout << "ns1::output()" << endl; };
#0008 }
#0009
#0010 namespace ns2
#0011 {
#0012 class CPoint { /* ... */ };
#0013 void print(CPoint &) { cout << "ns2::print()" << endl; };
#0014 void output() { cout << "ns2::output()" << endl; };
#0015 }
#0016
#0017 void main()
```

