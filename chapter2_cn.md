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

- 继承：当·