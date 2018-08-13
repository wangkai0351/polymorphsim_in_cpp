# 第2章

# C++对象模型

## （ C++ Object Model ）

### 幕帘重重：“伟大”的C++编译器

当我第一次接触C++的时候，我非常不能够适应，常常出现“雄兔脚扑朔，雌兔眼迷离”的情况。或许因为我的血液里已经充满了sequential language的元素，总奇怪为什么明明很多事情我没有做，却都会自动执行（好比说constructor）？为什么有些操作虽然符合需要，却是那么离奇（好比说visual functions）？

慢慢地，当我对C++的语法（syntax）和语意（semantic）更了解，也学习了所谓的C++对象模型之后，疑云也就一层一层地褪去了。计算机的世界，说到底，没有什么是会自动发生的。我（程序员）没有指定的事情，之所以能够发生，是因为C++编译器幕后做的手脚、牵的线。于是，我开始喜欢形容C++是一个幕布太多的语言：编译器为我们设下太多的幕布，使面向对象的舞台变化万千，但是幕布与幕布之间，像是米诺托斯（Minotauros，希腊神话中的牛头人身怪）的迷宫，初学者极易迷惘（甚至丧生）于其中，或是在（为什么会这样，为什么会那样）的情绪中郁郁寡欢。

我已经在第一章将C++ class的各种语法，都做了梗概整理。其中也揭露了一些C++编译器的幕后蛛丝马迹（例如default constructor和copy constructor）。现在，我要把另一些我认为很重要的C++幕布揭开。

### C++对象模型（ C++ Object Model ）

C++编译器必须设计出一套架构，使objects的产生、毁灭、继承、虚拟、指针、RTTI、exception handling、template……有一个合理而可实现的操作类型，得以完成C++的诸多面向对象的性质。此即所谓的C++对象模型。

对象模型的设计与实现，可能会随着不同的编译器而有所不同。C++ Standard只规定C++的语法（syntax）和语意（semantic），并没有示范什么样的语意必须用什么样的技术（对象模型）来完成。本章的各个实例探讨，除非有特别说明，都是以Microsoft C++编译器（Visual C++）为对象。

既然C++对象模型并不存在所谓的标准（也就是binary standard），我们探讨这个主题，意义何在？

### 热身

首先让我再把inheritance（继承）、virtual functions（虚函数）、polymorphic class（多态类型）的概念做个整理。我以class *B* 代表base class，class *D* 代表derived class，*vfunc()* 代表virtual function：

- **inheritance**：当 *D* 继承了 *B*，*D* 所继承的是 *B* 全部的data members以及所有的 member functions。*D* object的大小应该是 *B* object大小（也就是 *B* subobject）加上 *D* data members的大小。至于所谓“继承了member functions”，并不是指在object内部复制一份函数实体，而是指 *D* object拥有“调用 *B* member functions权力”的意思。

- **virtual functions**：在member functions声明之前加上virtual关键字，即成为virtual functions。这种函数的特色是，*D* 继承之后，如果重新定义（override），内存空间中会出现 *B::vfunc()* 和 *D::vfunc()* 两个函数。当我们以object指针来调用virtual functions，如果指针实际指向 *D* object，调用的就是 *D::vfunc()* ，如果指针实际指向 *B* object，调用的就是*B::vfunc()* 

  ```
  
  ```

  。

- *polymorphic class* ：具有virtual funcion(s) 的 class（不论是继承而来或是本身就有），称为polymorphic class。

## this指针— Data members 与 member functions  的桥梁

让我们从隐藏的 *this* 指针开始这趟奇妙之旅。

当我产生一个object，class member functions并不占用object内存空间，而是独立在外。不管同一个class有多个objects被产生出来，这个class的member functions都只有一份实体。

同一份member function函数实体，如何能够处理不同的objects？唯一的可能是从参数中识别，但我们从member functions的参数中看不出任何端倪呀。

事情的真相是，每一个member functions都有一个隐藏参数，称之为 *this* 。说它是“隐藏”参数，因为这是编译器为我们加上去的，从C++程序代码中看不出来。假设我有一个class声明为：

```c++
class CRect
{
protected:
	int m_color;
public:
	void setcolor(int color) { m_color = color; }
};
```

当我这么做

```c++
CRect aRect1, aRect2;
aRect1.setcolor(2);
aRect2.setcolor(3);
```

C++编译器为我实现的代码其实是：

```c++
CRect::setcolor(2, (CRect*)&aRect1);
CRect::setcolor(3, (CRect*)&aRect2);
```

新增的第二个参数，就是所谓的this指针。至于 *CRect* 中的 *setcolor()* 定义：

```c++
class CRect
{
...
public:
	void setcolor(int color) { m_color = color; }
};
```

则被编译器改为：

```c++
class CRect
{
...
public:
	void setcolor(int color, (CRect*)this 1 ) { this->m_color = color; }
};
```

下图可以说明 *this* 指针的作用

![]()

[^1]: C++ Standard并没有规定 *this* 指针应该被编译器加在第一个参数或是最后一个参数。

## sizeof的疑惑

从这一节开始，我们对于objects在内存空间的布局（也就是class data members、vptr、vtbl）以及 member functions的地址，有许多探讨。这些地址及其内容都是轻则编码探索而得。然而现代化软件开发集成环境所包含的调试器（debugger）其实能够很方便地让我们随时随地查看objects的布局和函数地址。我将在本章的最后一节介绍Visual C++的调试环境。

假设我有一个classes hierarchy如图2.1

```c++
#0001 class ClassA
#0002 {
#0003 public:
#0004 int m_data1;
#0005 int m_data2;
#0006 void func1() { cout << "ClassA::func1" << endl; }
#0007 void func2() { cout << "ClassA::func2" << endl; }
#0008 virtual void vfunc1() { cout << "ClassA::vfunc1" << endl; }
#0009 virtual void vfunc2() { cout << "ClassA::vfunc2" << endl; }
#0010 };
#0011
#0012 class ClassB : public ClassA
#0013 {
#0014 public:
#0015 int m_data3;
#0016 void func2() { cout << "ClassB::func2" << endl; }
#0017 virtual void vfunc1() { cout << "ClassB::vfunc1" << endl; }
#0018 };
#0019
#0020 class ClassC : public ClassB
#0021 {
#0022 public:
#0023 int m_data1;
#0024 int m_data4;
#0025 void func2() { cout << "ClassC::func2" << endl; }
#0026 virtual void vfunc1() { cout << "ClassC::vfunc1" << endl; }
#0027 };
```

现在让我 *ClassA* 、 *ClassB* 、 *ClassC*的大小：

 

```c++
cout << "sizeof(ClassA)=" << sizeof(ClassA) << endl;
cout << "sizeof(ClassB)=" << sizeof(ClassB) << endl;
cout << "sizeof(ClassC)=" << sizeof(ClassC) << endl;
```

得到的结果是：

```
sizeof(ClassA)=12
sizeof(ClassB)=16
sizeof(ClassC)=24
```

但如果我把程序代码L8，L9，L17，L26的virtual关键字去掉，得到的结果是：

```
sizeof(ClassA)=8
sizeof(ClassB)=12
sizeof(ClassC)=20
```

为什么virtual修饰词的有无，会造成object大小的改变？member functions实现代码不是独立鱼object之外的吗？

## 对象布局大局观：数据与函数

先前我一再强调过一个概念：一个object实体内只内含class data members；class member functions独立于object实体之外，是独一无二一份函数实体。因为，在没有virtual functions的情况下，如果我产生三个objects：

```c++
ClassA a;
ClassB b;
ClassC c;
```

这三个objects的对象布局如图2.2右。C++中凡是处于同一个access section的数据，必定以其声明次序依次出现在内存布局当中。但如果是被放置在多个access section中的各个数据，排列次序就不一定。

加上virtual functions之后（成为所谓的polymorphic class），三个objects的大小都多了4 bytes（如图2.2左）。而且只要base class有一个virtual function，就会造成这种后果——不论derived classes有没有改写（override）继承得来的virtual functions或者创造它自己新的virtual functions。多出来的4 bytes，是个指针（或称为virtual pointer，vptr），指向一个由函数指针所形成的数组（表格，或称为virtual table，vtbl）。表格中每一个函数指针指向一个virtual function函数实体，如图2.3。

vptr 和 vtbl 正是形成曼妙的虚拟机制的关键。

![]()

