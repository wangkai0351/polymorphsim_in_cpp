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

上述定义确实学术了一些！如果你没有看懂，其实只要建立这样的一个概念就好：object就是“东西”。东西必须占用一定的空间，在电脑中也就是占用一定的存储空间，所以我们又叫它为一个实体（instance）。

上述定义有相当部分在描述“东西”的起源。它说“东西是根据一个模板产生出来的”，这个“模板”其实就是“东西的属性描述”，也就是所谓的class（上述定义使用了template一词，容易引起混淆，因为“template”在C++语言中另有所指，见本章末的template一节）。东西的属性（class）由“数据（成员）”和“用以处理该数据的程序（在电脑世界里就是函数）”两者组成。

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

Class内含两种东西：一是data members（例如上面的*_x*），一是member functions（例如上面的Cpoint()以及两个*x()*）。

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

当程序执行起来，会先给一个提示，然后接收来自键盘（）的输入，一一将这些输入放进“copy.txt”当中，直到接收^z（文件结束）才停止。

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

当程序执行起来，会先给一个提示，然后从文件“copy.txt”中一一读出每一个字，显示在屏幕上。上述L15如果改为：

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

这样的“函数名改变”行为称为“name mangling”。遗憾的是，不同的编译器有着不同的name mangling做法。这当然不好，表示我以Microsoft C++编译器开发出来的程序没办法调用一个以Borland C++编译器编译完成的函数。换句话说，函数名称并不存在着binary standard，这就严重影响了software components[^5] 的发展。

Naming Mangling不只

[^5]: Software components的美好愿景是，希望开发出来的软件组件（软件IC）能够通行于 不同的编译平台，甚至打破不同程序语言的藩篱，这就需要binary standard。请参考***Essential COM***（Don Box/Addison Wesley/1998。中译本**COM本质论**，侯俊杰译，基峰，1998）

