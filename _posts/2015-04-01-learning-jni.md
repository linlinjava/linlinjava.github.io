---
layout: page
title: 官方JNI规范[翻译]
category: 
description: Learning and Thinking in JNI
---

# 声明

1. 基于**工匠若水**的博客内容[NDK-JNI实战教程（二） JNI官方中文资料][1]; 而根据其声明, 其博客内容则引用了**Admire**的JNI完全手册.
2. 同时基于最新官方[JNI本地接口(JNI)规范][2]英文原文跟新相关内容.

[TOC]

# 1. 介绍
本章介绍Java本地接口(Java Native Interface, JNI). JNI是本地编程接口. 它允许运行在Java虚拟机(Virtual Machine, VM)中的Java代码能够与如C, C++和汇编等其他编程语言所写的应用和库进行交互.

JNI最重要的好处是，它没有限制Java虚拟机的实现. 因此，Java虚拟机厂商可以添加JNI支持而不影响虚拟机的其他部分. 程序员可以编写一个版本的本地应用或库，同时期望它可以在所有支持JNI的Java虚拟机中的工作.

本章涉及了以下内容:

+ Java本地接口概述
+ 历史背景
    - JDK 1.0本地方法接口
    - Java运行时接口
    - 原生本地接口和Java/COM接口
+ 目标
+ Java本地接口方案
+ JNI编程
+ 变更

## 1.1 Java本地接口概述
虽然你完全可以用Java编写应用程序, 但也存在一些情况仅仅Java并不能满足用户应用的需求. 程序员使用JNI编写Java本地方法来处理这些情况应用程序不能完全写在Java。

下列例子阐述了用户需要使用Java本地方法的情况:

+ 标准Java类库不支持应用所需的平台依赖特性.
+ 用户已经用其他语言写了一个库, 因此希望能够通过JNI使得Java代码能够访问.
+ 用户希望利用汇编等低层次语言来实现一部分时间敏感的代码.

通过JNI编程, 用户可以使用本地方法:

+ 创建, 检测和跟新Java对象(包括数组和字符串).
+ 调用Java方法.
+ 捕获和抛出异常.
+ 加载类和获取类信息.
+ 执行运行时类型检查

用户还可以使用JNI的调用(Invocation)API来使得本地应用嵌入Java虚拟机. 这允许程序员很容易将他们已存在的应用Java化, 而不需要链接虚拟机源代码.

## 1.2 历史背景
不同供应商的虚拟机提供不同的本地方法接口. 这些不同的接口迫使程序员在给定的平台上产生、维护和分发多版本的本地方法库.

这里简要地研究了一些本地方法接口, 如:

+ JDK 1.0本地方法接口
+ Netscape的Java运行时接口
+ Microsoft的原生本地接口和Java/COM接口

### 1.2.1 JDK 1.0本地方法接口
JDK 1.0包含了本地方法接口. 但遗憾的是, 存在两个原因使得该接口不适合被其他Java虚拟机所采用.

首先, 本地代码是通过C结构的成员来访问Java对象的域. 然而, Java语言规范并没有定义对象是如何在内存中布局的. 如果一个Java虚拟机在内存中以不同方式布局对象, 那么程序员将不得不重新编译本地方法库.

其次, JDK 1.0的本地方法接口依赖一个保守的垃圾回收器. 例如无限制的使用`unhand`宏，使得有必要谨慎扫描本地栈。

### 1.2.2 Java运行时接口
Netscape曾提出Java运行时接口(Java Runtime Interface, JRI), 即Java虚拟机所提供服务的通用接口. JRI设计考虑了便携性--它对底层Java虚拟机的实现细节只有少量假设. JRI解决了一系列问题, 包括本地方法, 调试, 反射, 嵌入（调用）等等.

### 1.2.3 原生本地接口和Java/COM接口
Microsoft的Java虚拟机支持双本地方法接口. 在底一层次, 它提供了一种有效的原生本地接口（Raw Native Interface, RNI）. 虽然存在一个重要区别, 但是RNI提供了源代码级的对JDK本地方法接口的高度向后兼容性. 相比依靠保守的垃圾回收器, 本机代码必须使用RNI函数与垃圾收集器进行显式交互。

在高一层次, Microsoft的Java/COM接口提供了面向Java虚拟机的语言独立的标准二进制接口. Java代码可以把COM对象当做Java对象一般使用. Java类也能够作为COM类被暴露给系统的其他部分.

### 2 目标
我们相信一个统一的考虑周全的标准接口可以为每个人提供以下好处:

+ 每个虚拟机厂商可以支持更多的本地代码.
+ 工具构建者将需要维护不同类型的本地方法接口.
+ 应用开发者可以开发本地代码的一种版本, 即可使得该版本运行在不同虚拟机中.

实现标准本地方法接口的最佳途径是联合所有对Java虚拟机有兴趣的组织. 因此我们在Java许可持有者之间就统一本地方法接口的设计问题组织了一系列讨论. 从讨论中明确了标准本地方法接口必须满足以下要求:

+ 二进制兼容性 - 主要的目标是在给定平台上所有Java虚拟机实现之间的本地方法库的二进制兼容性. 对于给定平台, 程序员只需要维护他们本地方法库的一种版本. 
+ 效率 - 为了支持时间敏感的代码, 本地方法接口必须增加很少的系统开销. 所有已知的用于确保虚拟机独立性（因此具有二进制兼容性）的技术都会占用一定的系统开销. 我们必须在效率与虚拟机独立性之间进行某种折衷。 
+ 功能 - 接口必须暴露足够的Java虚拟机内部情况使得本地方法能够完成有用的任务.

### 3 Java本地接口方案
我们希望采用一种已有的方案作为标准接口, 因为这可以使得不得不学习不同虚拟机间的多种接口的程序员工作负担最轻. 遗憾的是, 已有解决方案中没有任何方案能够完全满足我们的目标.

Netscape的JRI是最接近于我们所设想的可移植本地方法接口, 并且被作为我们设计的起点. 熟悉JRI的读者将会注意到在API命名规则、方法和域ID的使用、局部和全局引用的使用等等方面的相似点. 尽管我们做了最大努力, 但是JNI和JRI并不是二进制兼容的, 即使虚拟机可以同时支持JRI和JNI.

Microsoft的RNI是对JDK 1.0的改进, 因为它解决了使用非保守垃圾收集器的本地方法的问题. 然而, RNI不适合作为虚拟机独立的本地方法接口. 与JDK类似, RNI本地方法将Java对象作为C结构来访问, 导致两个问题:

+ RNI将内部Java对象的布局暴露给了本地代码.
+ 将Java对象作为C结构直接访问使得不可能有效地加入"写屏障", 而"写屏障"在高级垃圾回收算法中是必需的.

作为二进制标准, COM确保了不同虚拟机之间的完全二进制兼容性. 调用COM方法仅仅是非直接调用, 而这占用少量系统开销. 此外, COM对象对动态链接库的版本问题解决上也有很大的改进.

然而, 有一些因素阻碍了将COM用作标准Java本地方法接口:

+ 第一, Java/COM接口缺少某些必需功能, 例如访问私有域和抛出普通异常.
+ 第二, Java/COM接口自动为Java对象提供标准IUnknown和IDispatch的COM 接口, 使得本地代码能够访问公有方法和域. 遗憾的是, IDispatch接口不能处理重载Java方法, 而且在匹配方法名称时是大小写不敏感的. 此外, 通过 IDispatch接口暴露的所有Java方法被打包来执行动态类型检查和强制转换. 这是因为IDispatch接口的设计只考虑了弱类型语言（例如 Basic）.
+ 第三, 相比处理单个低层次函数, COM允许软件组件（包括完全成熟的应用程序）一起工作. 我们认为将所有Java类或低层次本地方法都当作软件组件是不合适的. 
+ 第四, 由于缺少UNIX平台上的支持, 阻碍了直接采用COM.

虽然Java对象没有像COM对象一样暴露给本地代码, 但是JNI接口自身和COM是二进制兼容的. JNI使用与COM一样的跳转表和调用约定. 这意味着, 一旦具有对 COM的跨平台支持, JNI就能成为Java虚拟机的COM接口.

JNI不应该是给定Java虚拟机所支持的唯一本地方法接口. 标准接口能够帮助程序员加载他们的本地代码库到不同的Java虚拟机中. 在某些情况下, 程序员可能不得不使用低层次的与虚拟机相关的接口来获得高效率. 但在其它情况下, 程序员可能使用高层接口来建立软件组件. 实际上, 当Java环境和组件软件技术发展得越来越成熟, 本地方法将渐渐地丧失它们的重要性.

## 1.4 JNI编程 
本地方法程序员应利用JNI编程. JNI编程可以隔离未知条件, 例如终端用户可能正在运行的厂商的虚拟机. 遵守JNI标准是使得本地库能在给定Java虚拟机上运行的最好保证. 

如果用户正在实现Java虚拟机, 则用户应该实现JNI. JNI已经经受时间考验并确保不会占用虚拟机实现的任何系统开销或限制，包括对象表示和垃圾回收机制等。如果遇到我们可能忽视的任何问题, 请发送你的反馈给我们.

## 1.5 更改
在Java SE 6.0中, 废弃的JDK1_1InitArgs和JDK1_1AttachArgs结构已经被移除, 取而代之的是JavaVMInitArgs和JavaVMAttachArgs.

# 2. JNI设计概述
本章涉及了以下内容:

+ JNI接口函数和指针
+ 编译, 加载和链接本地方法
    + 解析本地方法名称
    + 本地方法参数
+ 引用Java对象
    + 全局和局部引用
    + 实现局部引用
+ 访问Java对象
    + 访问
    + 访问域和方法
+ Java异常

## 2.1 JNI接口函数和指针

平台相关代码是通过调用JNI函数来访问Java虚拟机功能的. JNI函数可通过接口指针来获得. 接口指针是指针的指针, 它指向一个指针数组, 而指针数组中的每个元素又指向一个接口函数. 每个接口函数都处在数组的某个预定偏移量中. 下图说明了接口指针的组织结构. 

<img src="http://docs.oracle.com/javase/8/docs/technotes/guides/jni/spec/images/designa.gif" />

JNI接口的组织类似于C++虚拟函数表或COM接口. 使用接口表而不使用硬性编入的函数表的好处是使JNI名字空间与平台相关代码分开. 虚拟机可以很容易地提供多个版本的JNI函数表. 例如, 虚拟机可支持以下两个JNI函数表:

+ 一个表对非法参数进行全面检查, 适用于调试程序. 
+ 另一个表只进行JNI规范所要求的最小程度的检查, 因此效率较高. 
	
JNI接口指针只在当前线程中有效. 因此, 本地方法不能将接口指针从一个线程传递到另一个线程中. 实现JNI的虚拟机可将本地线程的数据分配和储存在JNI接口指针所指向的区域中.

本地方法将JNI接口指针当作参数来接受. 虚拟机在从相同的Java线程中对本地方法进行多次调用时, 保证传递给该本地方法的接口指针是相同的. 但是, 一个本地方法可被不同的Java线程所调用, 因此可以接受不同的JNI接口指针.

## 2.2 编译, 加载和链接本地方法

因为Java虚拟机是多线程的, 因此本地库也应该由多线程感知本地编译器来编译和链接. 例如, `-mt`标识应该在Sun Studio编译器编译C++代码时使用. 对于GNU gcc编译器编译的代码, 标识`-D_REENTRANT`或`-D_POSIX_C_SOURCE`应该使用. 更多信息, 请参考本地编译器文档.

对本地方法的加载通过System.loadLibrary方法实现. 下例中, 类初始化方法加载了一个与平台有关的本地库, 在该本地库中给出了本地方法f的定义:

```
package pkg; 

class Cls {
	native double f(int i, String s); 
	
	static {
		System.loadLibrary("pkg_Cls"); 
	}
}
```

`System.loadLibrary`的参数是程序员任意选取的库名. 系统按照标准的但与平台有关的处理方法将该库名转换为本地库名. 例如, Solaris系统将名称`pkg_Cls`转换为`libpkg_Cls.so`, 而Win32系统将相同的名称`pkg_Cls`转换为`pkg_Cls.dll`. 

程序员可用单个库来存放任意数量的类所需的所有本地方法, 只要这些类将被相同的类加载器所加载. 虚拟机在其内部为每个类加载器保护其所加载的本地库清单. 提供者应该尽量选择能够避免名称冲突的本地库名.

一个本地库有可能会被静态链接到虚拟机中. 而以何种方式来链接库和虚拟机镜像则是实现独立的. `System.loadLibrary`或者类似的API必须成功地加载该库.

一个库L的镜像和虚拟机相联合只有在该库输出`JNI_OnLoad_L`函数时才被定义为静态链接.

如果一个静态链接的库L输出`JNI_OnLoad_L`函数和`JNI_OnLoad`函数, 那么`JNI_OnLoad`函数将会被忽略.

如果一个库L是静态链接的, 那么第一次调用`System.loadLibrary("L")`或者类似API时, `JNI_OnLoad_L`函数将会被执行, 其参数和预期返回值是和`JNI_OnLoad`函数一样.

一个静态链接的库L将会阻止一个具有相同名称的库被动态链接.

当包含一个静态链接的本地库L的类加载器进行垃圾回收时, 虚拟机将会执行`JNI_OnUnLoad_L`函数(如果这个函数存在).

如果一个静态链接库L输出`JNI_OnUnLoad_L`函数和`JNI_OnUnLoad`函数, 那么`JNI_OnUnLoad`函数将会被忽略.

程序员还可调用JNI函数`RegisterNatives`来注册与类关联的本地方法. 在与静态链接的函数一起使用时, `RegisterNatives`函数将特别有用. 

## 2.3 解析本地方法名

动态链接程序是根据项的名称来解析各项的. 本地方法名称由以下几部分串接而成:

+ 前缀 Java_
+ 全限定的类名
+ 下划线（“_”）分隔符
+ 方法名
+ 对于重载的本地方法, 加上两个下划线（“__”）, 后跟参数签名

虚拟机将为本地库中的方法查找匹配的方法名. 它首先查找短名（没有参数签名的名称）, 然后再查找带参数签名的长名称. 只有当某个本地方法被另一个本地方法重载时程序员才有必要使用长名. 但如果本地方法的名称与非本地方法的名称相同, 则不会有问题. 因为非本地方法（Java方法）并不放在本地库中. 

下例中, 不必用长名来链接本地方法g, 因为另一个方法g不是本地方法, 因而它并不在本地库中. 

```
class Cls1 {
	int g(int i); 
	native int g(double d); 
}
```

我们采取简单的名字搅乱方案, 以保证所有的Unicode字符都能被转换为有效的C函数名. 我们用下划线（“_”）字符来代替全限定的类名中的斜杠（“/”）. 由于名称或类型描述符从来不会以数字打头, 我们用 _0、...、_9 来代替转义字符序列. 


Unicode Character Translation 
| Escape Sequence | Denotes |
| --------------- | ------- |
| _0XXXX | a Unicode character XXXX. Note that lower case is used to represent non-ASCII Unicode characters, for example, _0abcd as opposed to _0ABCD. |
| _1 | the character “_” |
| _2 | the character “; ” in signatures |
| _3 |	the character “[“ in signatures |


本地方法和接口API都要遵守给定平台上的库调用标准约定. 例如, UNIX系统使用C调用约定, 而Win32系统使用 `__stdcall`. 

## 2.4 本地方法的参数

JNI接口指针是本地方法的第一个参数. 其类型是`JNIEnv`. 第二个参数随本地方法是静态还是非静态而有所不同. 非静态本地方法的第二个参数是对对象的引用, 而静态本地方法的第二个参数是对其Java类的引用. 

其余的参数对应于通常Java方法的参数. 本地方法调用利用返回值将结果传回调用程序中. 第三章 “JNI的类型和数据结构” 将描述Java类型和C类型之间的映射. 

代码示例说明了如何用C函数来实现本地方法f. 对本地方法f的声明如下:

```
package pkg; 

class Cls {
	native double f(int i, String s); 
	...
}
```

具有长名称`Java_pkg_Cls_f_ILjava_lang_String_2`的C函数实现本地方法f:

```
jdouble Java_pkg_Cls_f__ILjava_lang_String_2 (
	JNIEnv *env,		/* 接口指针 */
	jobject obj, 		/* “this”指针 */
	jint i,			/* 第一个参数 */
	jstring s) 		/* 第二个参数 */
	{
	/* 取得Java字符串的C版本 */
	const char *str = (*env)->GetStringUTFChars(env, s, 0); 
	/* 处理该字符串 */
	...
	/* 至此完成对 str 的处理 */
	(*env)->ReleaseStringUTFChars(env, s, str); 
	return ...
}
```

注意, 我们总是用接口指针env来操作Java对象. 可用C++将此代码写得稍微简洁一些, 如代码示例所示:

```
extern "C" /* 指定 C 调用约定 */
	jdouble Java_pkg_Cls_f__ILjava_lang_String_2 (
		JNIEnv *env,		/* 接口指针 */
		jobject obj,		/* “this”指针 */
		jint i,			/* 第一个参数 */
		jstring s)		/* 第二个参数 */
		{
		const char *str = env->GetStringUTFChars(s, 0); 
		...
		env->ReleaseStringUTFChars(s, str); 
		return ...
	}
```

使用C++后, 源代码变得更为直接, 且接口指针参数消失. 但是, C++的内在机制与C的完全一样. 在C++中, JNI函数被定义为内联成员函数, 它们将扩展为相应的C对应函数. 

## 2.5 引用Java对象

基本类型（如整型、字符型等）在Java和平台相关代码之间直接进行复制. 而Java对象由引用来传递. 虚拟机必须跟踪传到平台相关代码中的对象, 以使这些对象不会被垃圾收集器释放. 反之, 平台相关代码必须能用某种方式通知虚拟机它不再需要那些对象, 同时, 垃圾收集器必须能够移走被平台相关代码引用过的对象. 

### 2.5.1 全局和局部引用

JNI将平台相关代码使用的对象引用分成两类:局部引用和全局引用. 局部引用在本地方法调用期间有效, 并在本地方法返回后被自动释放掉. 全局引用将一直有效, 直到被显式释放. 

对象是被作为局部引用传递给本地方法的, 由JNI函数返回的所有Java对象也都是局部引用. JNI允许程序员从局部引用创建全局引用. 要求Java对象的JNI函数既可接受全局引用也可接受局部引用. 本地方法将局部引用或全局引用作为结果返回. 

大多数情况下, 程序员应该依靠虚拟机在本地方法返回后释放所有局部引用. 但是, 有时程序员必须显式释放某个局部引用. 例如, 考虑以下的情形:

+ 本地方法要访问一个大型Java对象, 于是创建了对该Java对象的局部引用. 然后, 本地方法要在返回调用程序之前执行其它计算. 对这个大型Java对象的局部引用将防止该对象被当作垃圾收集, 即使在剩余的运算中并不再需要该对象. 

+ 本地方法创建了大量的局部引用, 但这些局部引用并不是要同时使用. 由于虚拟机需要一定的空间来跟踪每个局部引用, 创建太多的局部引用将可能使系统耗尽内存.  例如, 本地方法要在一个大型对象数组中循环, 把取回的元素作为局部引用, 并在每次迭代时对一个元素进行操作. 每次迭代后, 程序员不再需要对该数组元素的局部引用. 

JNI允许程序员在本地方法内的任何地方对局部引用进行手工删除. 为确保程序员可以手工释放局部引用, JNI函数将不能创建额外的局部引用, 除非是这些JNI函数要作为结果返回的引用. 局部引用仅在创建它们的线程中有效. 本地方法不能将局部引用从一个线程传递到另一个线程中. 

### 2.5.2 实现局部引用

为了实现局部引用, Java虚拟机为每个从Java到本地方法的控制转换都创建了注册服务程序. 注册服务程序将不可移动的局部引用映射为Java对象, 并防止这些对象被当作垃圾收集. 所有传给本地方法的Java对象（包括那些作为JNI函数调用结果返回的对象）将被自动添加到注册服务程序中. 本地方法返回后, 注册服务程序将被删除, 其中的所有项都可以被当作垃圾来收集. 

可用各种不同的方法来实现注册服务程序, 例如, 使用表、链接列表或hash表来实现. 虽然引用计数可用来避免注册服务程序中有重复的项, 但JNI实现不是必须检测和消除重复的项. 

注意, 以保守方式扫描本地堆栈并不能如实地实现局部引用. 平台相关代码可将局部引用储存在全局或堆数据结构中. 

## 2.6 访问Java对象

JNI提供了一大批用来访问全局引用和局部引用的函数. 这意味着无论虚拟机在内部如何表示Java对象, 相同的本地方法实现都能工作. 这就是为什么JNI可被各种各样的虚拟机实现所支持的关键原因. 

通过不透明的引用来使用访问函数的开销比直接访问C数据结构的开销来得高. 我们相信, 大多数情况下, Java程序员使用本地方法是为了完成一些重要任务, 此时这种接口的开销不是首要问题. 

### 2.6.1 访问基本类型数组

对于含有大量基本数据类型（如整数数组和字符串）的Java对象来说, 这种开销将高得不可接受（考虑一下用于执行矢量和矩阵运算的本地方法的情形便知）. 对Java数组进行迭代并且要通过函数调用取回数组的每个元素, 其效率是非常低的. 

一个解决办法是引入“钉住”概念, 以使本地方法能够要求虚拟机钉住数组内容. 而后, 该本地方法将接受指向数值元素的直接指针. 但是, 这种方法包含以下两个前提:

+ 垃圾收集器必须支持钉住. 
+ 虚拟机必须在内存中连续存放基本类型数组. 虽然大多数基本类型数组都是连续存放的, 但布尔数组可以压缩或不压缩存储. 因此, 依赖于布尔数组确切存储方式的本地方法将是不可移植的. 

我们将采取折衷方法来克服上述两个问题. 

首先, 我们提供了一套函数, 用于在Java数组的一部分和本地内存缓冲之间复制基本类型数组元素. 这些函数只有在本地方法只需访问大型数组中的一小部分元素时才使用. 

其次, 程序员可用另一套函数来取回数组元素的受约束版本. 记住, 这些函数可能要求Java虚拟机分配存储空间和进行复制. 虚拟机实现将决定这些函数是否真正复制该数组, 如下所示:

+ 如果垃圾收集器支持钉住, 且数组的布局符合本地方法的要求, 则不需要进行复制. 
+ 否则, 该数组将被复制到不可移动的内存块中（例如, 复制到C堆中）, 并进行必要的格式转换, 然后返回指向该副本的指针. 

最后, 接口提供了一些函数, 用以通知虚拟机本地方法已不再需要访问这些数组元素. 当调用这些函数时, 系统或者释放数组, 或者在原始数组与其不可移动副本之间进行协调并将副本释放. 

这种处理方法具有灵活性. 垃圾收集器的算法可对每个给定的数组分别作出复制或钉住的决定. 例如, 垃圾收集器可能复制小型对象而钉住大型对象. 

JNI实现必须确保多个线程中运行的本地方法可同时访问同一数组. 例如, JNI可以为每个被钉住的数组保留一个内部计数器, 以便某个线程不会解开同时被另一个线程钉住的数组. 注意, JNI不必将基本类型数组锁住以专供某个本地方法访问. 同时从不同的线程对Java数组进行更新将导致不确定的结果. 

### 2.6.2 访问域和方法

JNI允许本地方法访问Java对象的域或调用其方法. JNI用符号名称和类型签名来识别方法和域. 从名称和签名来定位域或对象的过程可分为两步. 例如, 为调用类cls中的f方法, 平台相关代码首先要获得方法ID, 如下所示:

```
jmethodID mid = env->GetMethodID(cls, "f", "(ILjava/lang/String; )D"); 
```

然后, 平台相关代码可重复使用该方法ID而无须再查找该方法, 如下所示:

```
jdouble result = env->CallDoubleMethod(obj, mid, 10, str); 
```

域ID或方法ID并不能防止虚拟机卸载生成该ID的类. 该类被卸载之后, 该方法ID或域ID亦变成无效. 因此, 如果平台相关代码要长时间使用某个方法ID或域ID, 则它必须确保:

+ 保留对所涉及类的活引用, 
+ 或重新计算该方法ID或域ID. 

JNI对域ID和方法ID的内部实现并不施加任何限制. 

## 2.7 报告编程错误

JNI不检查诸如传递NULL指针或非法参数类型之类的编程错误. 非法的参数类型包括诸如要用Java类对象时却用了普通Java对象这样的错误. JNI不检查这些编程错误的理由如下:

+ 强迫JNI函数去检查所有可能的错误情况将降低正常（正确）的本地方法的性能. 
+ 在许多情况下, 没有足够的运行时的类型信息可供这种检查使用. 

大多数C库函数对编程错误不进行防范. 例如, `printf`函数在接到一个无效地址时通常是引起运行错而不是返回错误代码. 强迫C库函数检查所有可能的错误情况将有可能引起这种检查被重复进行--先是在用户代码中进行, 然后又在库函数中再次进行. 

程序员不得将非法指针或错误类型的参数传递给JNI函数. 否则, 可能产生意想不到的后果, 包括可能使系统状态受损或使虚拟机崩溃. 

## 2.8 Java异常

JNI允许本地方法抛出任何Java异常. 本地方法也可以处理突出的Java异常. 未被处理的Java异常将被传回虚拟机中. 

### 2.8.1 异常和错误代码

一些JNI函数使用Java异常机制来报告错误情况. 大多数情况下, JNI函数通过返回错误代码并抛出Java异常来报告错误情况. 错误代码通常是特殊的返回值（如 NULL）, 这种特殊的返回值在正常返回值范围之外. 因此, 程序员可以:

+ 快速检查上一个JNI调用所返回的值以确定是否出错, 
+ 并通过调用函数`ExceptionOccurred`来获得异常对象, 它含有对错误情况的更详细说明. 

在以下两种情况中, 程序员需要先查出异常, 然后才能检查错误代码:

+ 调用Java方法的JNI函数返回该Java方法的结果. 程序员必须调用`ExceptionOccurred` 以检查在执行Java方法期间可能发生的异常. 
+ 某些用于访问JNI数组的函数并不返回错误代码, 但可能会抛出`ArrayIndexOutOfBoundsException`或`ArrayStoreException`. 

在所有其它情况下, 返回值如果不是错误代码值就可确保没有抛出异常. 

### 2.8.2 异步异常

在多个线程的情况下, 当前线程以外的其它线程可能会抛出异步异常. 异步异常并不立即影响当前线程中平台相关代码的执行, 直到出现下列情况:

+ 该平台相关代码调用某个有可能抛出同步异常的JNI函数, 
+ 或者该平台相关代码用 `ExceptionOccurred`显式检查同步异常或异步异常. 

注意, 只有那些有可能抛出同步异常的JNI函数才检查异步异常. 

本地方法应在必要的地方（例如, 在一个没有其它异常检查的紧密循环中）插入`ExceptionOccurre`检查以确保当前线程可在适当时间内对异步异常作出响应. 

### 2.8.3 异常处理

可用两种方法来处理平台相关代码中的异常:

+ 本地方法可选择立即返回, 使异常在启动该本地方法调用的Java代码中抛出. 
+ 平台相关代码可通过调用`ExceptionClear`来清除异常, 然后执行自己的异常处理代码. 

抛出了某个异常之后, 平台相关代码必须先清除异常, 然后才能进行其它的JNI调用. 当有待定异常时, 只有以下这些JNI函数可被安全地调用:

```
	ExceptionOccurred()
	ExceptionDescribe()
	ExceptionClear()
	ExceptionCheck()
	ReleaseStringChars()
	ReleaseStringUTFChars()
	ReleaseStringCritical()
	Release<Type>ArrayElements()
	ReleasePrimitiveArrayCritical()
	DeleteLocalRef()
	DeleteGlobalRef()
	DeleteWeakGlobalRef()
	MonitorExit()
	PushLocalFrame()
	PopLocalFrame()
```

# 3 JNI类型和数据结构

本章讨论JNI如何将Java类型映射到本地C类型. 

## 3.1 基本类型

基本类型和本地等效类型表:

| **Java类型** | **本地类型** | **说明** |
| ------------ | ------------ | -------- |
| boolean | jboolean | 无符号, 8位 |
| byte | jbyte | 无符号, 8位 |
| char | jchar | 无符号, 16位 |
| short | jshort | 有符号, 16位 |
| int | jint | 有符号, 32位 |
| long | jlong | 有符号, 64位 |
| float | jfloat | 32位 |
| double | jdouble | 64位 |
| void | void | N/A |

为了使用方便, 特提供以下定义:

```
	#define JNI_FALSE 0
	#define JNI_TRUE 1
```

jsize整数类型用于描述主要指数和大小:

```
	typedef jint jsize; 
```

## 3.2 引用类型

JNI包含了很多对应于不同Java对象的引用类型. JNI引用类型的组织层次如图所示:

+ jobject
    + jclass (java.lang.Class objects)
    + jstring (java.lang.String objects)
    + jarray (arrays)
        + jobjectArray (object arrays)
        + jbooleanArray (boolean arrays)
        + jbyteArray (byte arrays)
        + jcharArray (char arrays)
        + jshortArray (short arrays)
        + jintArray (int arrays)
        + jlongArray (long arrays)
        + jfloatArray (float arrays)
        + jdoubleArray (double arrays)
    + jthrowable (java.lang.Throwable objects)


在C中, 所有其它JNI引用类型都被定义为与jobject一样. 例如:

```
typedef jobject jclass; 
```

在C++中, JNI引入了虚构类以加强子类关系. 例如:

```
class _jobject {}; 
class _jclass : public _jobject {}; 
// ...
typedef _jobject *jobject; 
typedef _jclass *jclass; 
```

## 3.3 域ID和方法ID

方法ID和域ID是常规的C指针类型:

```
struct _jfieldID;  /*不透明结构 */
typedef struct _jfieldID *jfieldID;  /* 域 ID */
struct _jmethodID;  /* 不透明结构 */
typedef struct _jmethodID *jmethodID;  /* 方法 ID */
```

## 3.4 值类型

jvalue联合类型在参数数组中用作单元类型. 其声明方式如下:

```
typedef union jvalue {
	jboolean z; 
	jbyte	 b; 
	jchar	 c; 
	jshort	 s; 
	jint	 i; 
	jlong	 j; 
	jfloat	 f; 
	jdouble	 d; 
	jobject	 l; 
} jvalue; 
```

## 3.5 类型签名

JNI使用Java虚拟机的类型签名表述. 下表列出了这些类型签名:

| **类型签名** | **Java 类型** |
| ------------ | ------------- |
| Z | boolean |
| B | byte |
| C | char |
| S | short |
| I | int |
| J | long |
| F | float |
| D | double |
| L fully-qualified-class ;  | 全限定的类 |
| [ type | type[] |
| ( arg-types ) ret-type | 方法类型 |


例如, Java方法:

```
long f (int n, String s, int[] arr); 
```

具有以下类型签名:

```
(ILjava/lang/String; [I)J
```

## 3.6 改进型UTF-8字符串

JNI采用改进型UTF-8字符串来表示各种字符串类型. 改进型UTF-8字符串和Java虚拟机所使用的一样. 改进型UTF-8字符串的编码方式使得仅包含非空ASCII字符的字符序列能够按每字符一个字节表示, 但是所有的Unicode字符都能表示.

所有在\u0001到\u007F范围内的字符都用单字节表示, 如下所示:
	
+ 0xxxxxxx
	
字节中的七位数据确定了所表示字符的值. 

空字符 (\u0000)和\u0080到\u07FF范围内的字符用一对字节表示,  即x和y, 如下所示:

+ x: 110xxxxx
+ y: 10yyyyyy

值为((x&0x1f)<<6)+(y&0x3f)的字符需用两个字节表示. 

\u0800到\uFFFF范围内的字符用三个字节表示, 即x, y和z:

+ x: 1110xxxx
+ y: 10yyyyyy
+ z: 10zzzzzz

值为((x&0xf)<<12)+(y&0x3f)<<6)+(z&0x3f)的字符需用三个字节表示. 

代码点U+FFFF之上的字符（所谓的补充字符）通过两个UTF-16编码的代理代码单元来表示. 每一个代理代码单元都有三个字节表示. 这意味着, 补充字符是由六个字节表示, 即u,v,w,x,y和z:

+ u: 11101101
+ v: 1010vvvv
+ w: 10wwwwww
+ x: 11101101
+ y: 1011yyyy
+ z: 10zzzzzz

值为0x10000+((v&0x0f)<<16)+((w&0x3f)<<10)+(y&0x0f)<<6)+(z&0x3f) 的字符需要六个字节表示.

多字节字符的字节是以大端(高字节先)顺序存储在class文件.

此格式与标准UTF-8格式之间有两个区别. 第一, 空字符(char)0使用双字节格式进行编码, 而不是单字节格式. 这意味着改进型UTF-8字符串不可能有嵌入的空值. 第二, 只使用了标准UTF-8的单字节、双字节和三字节格式. Java虚拟机不识别标准UTF-8的四字节格式; 它使用自己的两次三字节格式.. 

标准UTF-8格式的更多信息, 查阅Unicode Encoding Forms of The Unicode Standard, Version 4.0的3.9节.

# 4 JNI函数

本章是JNI函数参考章节. 其中列出了全部JNI函数, 同时也给出了JNI函数表的准确布局. 

注意:“必须”一词用于约束JNI程序员. 例如, 当说明某个JNI函数必须接收非空对象时, 就应确保不要向该JNI函数传递NULL. 因而, JNI实现将无需在该JNI函数中
执行NULL指针检查. 

本章的部分资料改编自Netscape的JRI文档. 该参考资料按用法对函数进行组织. 

参考材料按照函数用法分组. 参考章节如下列所示:

+ 接口函数表
+ 版本信息
    + GetVersion
    + Constants
+ 类操作
    + DefineClass
    + FindClass
    + GetSuperclass
    + IsAssignableFrom
+ 异常
    + Throw
    + ThrowNew
    + ExceptionOccurred
    + ExceptionDescribe
    + ExceptionClear
    + FatalError
    + ExceptionCheck
+ 全局和局部引用
    + Global References
    + NewGlobalRef
    + DeleteGlobalRef
    + Local References
    + DeleteLocalRef
    + EnsureLocalCapacity
    + PushLocalFrame
    + PopLocalFrame
    + NewLocalRef
+ 弱全局引用
    + NewWeakGlobalRef
    + DeleteWeakGlobalRef
+ 类操作
    + AllocObject
    + NewObject, NewObjectA, NewObjectV
    + GetObjectClass
    + GetObjectRefType
    + IsInstanceOf
    + IsSameObject
+ 访问对象域
    + GetFieldID
    + `Get<type>Field`例程
    + `Set<type>Field`例程
+ 调用实例方法
    + GetMethodID
    + `Call<type>Method`例程, `Call<type>MethodA`例程, `Call<type>MethodV`例程
    + `CallNonvirtual<type>Method`例程, `CallNonvirtual<type>MethodA`例程, `CallNonvirtual<type>MethodV`例程
+ 访问静态域
    + GetStaticFieldID
    + `GetStatic<type>Field`例程
    + `SetStatic<type>Field`例程
+ 调用静态方法
    + GetStaticMethodID
    + `CallStatic<type>Method`例程, `CallStatic<type>MethodA`例程, `CallStatic<type>MethodV`例程
+ 字符串操作
    + NewString
    + GetStringLength
    + GetStringChars
    + ReleaseStringChars
    + NewStringUTF
    + GetStringUTFLength
    + GetStringUTFChars
    + ReleaseStringUTFChars
    + GetStringRegion
    + GetStringUTFRegion
    + GetStringCritical, ReleaseStringCritical
+ 数组操作
    + GetArrayLength
    + NewObjectArray
    + GetObjectArrayElement
    + SetObjectArrayElement
    + `New<PrimitiveType>Array`例程
    + `Get<PrimitiveType>ArrayElements`例程
    + `Release<PrimitiveType>ArrayElements`例程
    + `Get<PrimitiveType>ArrayRegion`例程
    + `Set<PrimitiveType>ArrayRegion`例程
    + GetPrimitiveArrayCritical, ReleasePrimitiveArrayCritical
+ 注册本地方法
    + RegisterNatives
    + UnregisterNatives
+ 监控器操作
    + MonitorEnter
    + MonitorExit
+ NIO支持
    + NewDirectByteBuffer
    + GetDirectBufferAddress
    + GetDirectBufferCapacity
+ 反射支持
    + FromReflectedMethod
    + FromReflectedField
    + ToReflectedMethod
    + ToReflectedField
+ Java虚拟机接口
    + GetJavaVM

## 4.1 接口函数表

每个函数均可通过`JNIEnv`参数以固定偏移量进行访问. `JNIEnv`的类型是一个指针, 
指向存储全部JNI函数指针的结构. 它如下定义:

```
typedef const struct JNINativeInterface *JNIEnv; 
```

虚拟机初始化函数表, 如下列代码示例所示.注意:前三项留作将来与COM兼容. 此外, 我们在函数表开头部分也留出来多个NULL项, 从而可将将来与类有关的JNI操作添加到FindClass后面, 而非函数表的末尾. 

注意, 函数表可在所有JNI接口指针间共享. 

```
const struct JNINativeInterface ... = {
	NULL,
	NULL,
	NULL,
	NULL,
	GetVersion,

	DefineClass,
	FindClass,
	NULL,
	NULL,
	NULL,
	GetSuperclass,
	IsAssignableFrom,
	NULL,

	Throw,
	ThrowNew,
	ExceptionOccurred,
	ExceptionDescribe,
	ExceptionClear,
	FatalError,
	NULL,
	NULL,

	NewGlobalRef,
	DeleteGlobalRef,
	DeleteLocalRef,
	IsSameObject,
	NULL,
	NULL,
	AllocObject,

	NewObject,
	NewObjectV,
	NewObjectA,
	GetObjectClass,

	IsInstanceOf,

	GetMethodID,

	CallObjectMethod,
	CallObjectMethodV,
	CallObjectMethodA,
	CallBooleanMethod,
	CallBooleanMethodV,
	CallBooleanMethodA,
	CallByteMethod,
	CallByteMethodV,
	CallByteMethodA,
	CallCharMethod,
	CallCharMethodV,
	CallCharMethodA,
	CallShortMethod,
	CallShortMethodV,
	CallShortMethodA,
	CallIntMethod,
	CallIntMethodV,
	CallIntMethodA,
	CallLongMethod,
	CallLongMethodV,
	CallLongMethodA,
	CallFloatMethod,
	CallFloatMethodV,
	CallFloatMethodA,
	CallDoubleMethod,
	CallDoubleMethodV,
	CallDoubleMethodA,
	CallVoidMethod,
	CallVoidMethodV,
	CallVoidMethodA,

	CallNonvirtualObjectMethod,
	CallNonvirtualObjectMethodV,
	CallNonvirtualObjectMethodA,
	CallNonvirtualBooleanMethod,
	CallNonvirtualBooleanMethodV,
	CallNonvirtualBooleanMethodA,
	CallNonvirtualByteMethod,
	CallNonvirtualByteMethodV,
	CallNonvirtualByteMethodA,
	CallNonvirtualCharMethod,
	CallNonvirtualCharMethodV,
	CallNonvirtualCharMethodA,
	CallNonvirtualShortMethod,
	CallNonvirtualShortMethodV,
	CallNonvirtualShortMethodA,
	CallNonvirtualIntMethod,
	CallNonvirtualIntMethodV,
	CallNonvirtualIntMethodA,
	CallNonvirtualLongMethod,
	CallNonvirtualLongMethodV,
	CallNonvirtualLongMethodA,
	CallNonvirtualFloatMethod,
	CallNonvirtualFloatMethodV,
	CallNonvirtualFloatMethodA,
	CallNonvirtualDoubleMethod,
	CallNonvirtualDoubleMethodV,
	CallNonvirtualDoubleMethodA,
	CallNonvirtualVoidMethod,
	CallNonvirtualVoidMethodV,
	CallNonvirtualVoidMethodA,

	GetFieldID,

	GetObjectField,
	GetBooleanField,
	GetByteField,
	GetCharField,
	GetShortField,
	GetIntField,
	GetLongField,
	GetFloatField,
	GetDoubleField,
	SetObjectField,
	SetBooleanField,
	SetByteField,
	SetCharField,
	SetShortField,
	SetIntField,
	SetLongField,
	SetFloatField,
	SetDoubleField,

	GetStaticMethodID,

	CallStaticObjectMethod,
	CallStaticObjectMethodV,
	CallStaticObjectMethodA,
	CallStaticBooleanMethod,
	CallStaticBooleanMethodV,
	CallStaticBooleanMethodA,
	CallStaticByteMethod,
	CallStaticByteMethodV,
	CallStaticByteMethodA,
	CallStaticCharMethod,
	CallStaticCharMethodV,
	CallStaticCharMethodA,
	CallStaticShortMethod,
	CallStaticShortMethodV,
	CallStaticShortMethodA,
	CallStaticIntMethod,
	CallStaticIntMethodV,
	CallStaticIntMethodA,
	CallStaticLongMethod,
	CallStaticLongMethodV,
	CallStaticLongMethodA,
	CallStaticFloatMethod,
	CallStaticFloatMethodV,
	CallStaticFloatMethodA,
	CallStaticDoubleMethod,
	CallStaticDoubleMethodV,
	CallStaticDoubleMethodA,
	CallStaticVoidMethod,
	CallStaticVoidMethodV,
	CallStaticVoidMethodA,

	GetStaticFieldID,

	GetStaticObjectField,
	GetStaticBooleanField,
	GetStaticByteField,
	GetStaticCharField,
	GetStaticShortField,
	GetStaticIntField,
	GetStaticLongField,
	GetStaticFloatField,
	GetStaticDoubleField,

	SetStaticObjectField,
	SetStaticBooleanField,
	SetStaticByteField,
	SetStaticCharField,
	SetStaticShortField,
	SetStaticIntField,
	SetStaticLongField,
	SetStaticFloatField,
	SetStaticDoubleField,

	NewString,
	GetStringLength,
	GetStringChars,
	ReleaseStringChars,
	NewStringUTF,
	GetStringUTFLength,
	GetStringUTFChars,
	ReleaseStringUTFChars,

	GetArrayLength,

	NewObjectArray,
	GetObjectArrayElement,
	SetObjectArrayElement,

	NewBooleanArray,
	NewByteArray,
	NewCharArray,
	NewShortArray,
	NewIntArray,
	NewLongArray,
	NewFloatArray,
	NewDoubleArray,

	GetBooleanArrayElements,
	GetByteArrayElements,
	GetCharArrayElements,
	GetShortArrayElements,
	GetIntArrayElements,
	GetLongArrayElements,
	GetFloatArrayElements,
	GetDoubleArrayElements,

	ReleaseBooleanArrayElements,
	ReleaseByteArrayElements,
	ReleaseCharArrayElements,
	ReleaseShortArrayElements,
	ReleaseIntArrayElements,
	ReleaseLongArrayElements,
	ReleaseFloatArrayElements,
	ReleaseDoubleArrayElements,

	GetBooleanArrayRegion,
	GetByteArrayRegion,
	GetCharArrayRegion,
	GetShortArrayRegion,
	GetIntArrayRegion,
	GetLongArrayRegion,
	GetFloatArrayRegion,
	GetDoubleArrayRegion,
	SetBooleanArrayRegion,
	SetByteArrayRegion,
	SetCharArrayRegion,
	SetShortArrayRegion,
	SetIntArrayRegion,
	SetLongArrayRegion,
	SetFloatArrayRegion,
	SetDoubleArrayRegion,

	RegisterNatives,
	UnregisterNatives,

	MonitorEnter,
	MonitorExit,

	GetJavaVM,
}; 
```

## 4.2 版本信息

### 4.2.1 GetVersion
返回本地方法接口的版本. 

```
jint GetVersion(JNIEnv *env); 
```

#### 关联
JNIEnv接口函数表的项4

#### 参数
env:JNI接口指针. 
	
#### 返回值
高16位返回主版本号, 低16位返回次版本号. 
在JDK1.1 中, GetVersion()返回0x00010001. 
在JDK1.2 中, GetVersion()返回0x00010002. 
在JDK1.4 中, GetVersion()返回0x00010004. 
在JDK1.6 中, GetVersion()返回0x00010006. 

### 4.2.2 常量

#### SINCE JDK/JRE 1.2:

```
#define JNI_VERSION_1_1 0x00010001
#define JNI_VERSION_1_2 0x00010002

/* Error codes */
#define JNI_EDETACHED    (-2)              /* thread detached from the VM */
#define JNI_EVERSION     (-3)              /* JNI version error */
```

#### SINCE JDK/JRE 1.4:

```
#define JNI_VERSION_1_4 0x00010004
```

#### SINCE JDK/JRE 1.6:

```
#define JNI_VERSION_1_6 0x00010006
```

## 4.3 类操作

### 4.3.1 DefineClass

```
jclass DefineClass(JNIEnv *env, const char *name, jobject loader, const jbyte *buf, jsize bufLen); 
```
从原始类数据的缓冲区中加载类. 当DefineClass调用返回后,包含原始类数据的缓冲区不会被虚拟机引用, 而且如果需要它可能会被放弃.

#### 关联
JNIEnv接口函数表的项5

#### 参数
env: JNI接口指针. 
name: 定义的类或接口的名称. 字符串以改进型UTF-8编码.
loader: 分派给所定义的类的类加载器. 
buf: 包含.class文件数据的缓冲区. 
bufLen: 缓冲区长度. 
	
#### 返回值
返回Java类对象. 如果出错则返回NULL. 
	
#### 抛出
ClassFormatError:如果类数据指定的类无效. 
ClassCircularityError:如果类或接口是自身的超类或超接口. 
OutOfMemoryError:如果系统内存不足. 
SecurityException:如果调用者试图定义一个在"java"包树中的类.

### 4.3.2 FindClass

```
jclass FindClass(JNIEnv *env, const char *name); 
```

在JDK1.1中, 该函数用于加载本地定义的类. 它将搜索由`CLASSPATH`环境变量为具有指定名称的类所指定的目录和zip文件. 

在JDK1.2中, Java安全模型允许非系统类加载和调用本地方法. `FindClass`定位与当前本地方法相关联的类加载器, 即声明本地方法的类的类加载器. 如果本地方法属于一个系统类, 则不会涉及类加载器. 否则, 一个适当的类加载器将会被加载并链接命名类.

在JDK1.2中, 当FindClass通过调用接口被调用, 则不会有当前本地方法或者它相关联的类加载. 此时, `ClassLoader.getSystemClassLoader`则会被使用. 这是虚拟机为应用创建的类加载器, 能够在`java.class.path`属性所列路径中定位类.

`name`参数是一个全限定类名称或者一个数组类型签名. 例如, `java.lang.String`类的全限定类名称是:

```
                   "java/lang/String"
```

而数组对象java.lang.Object[]的数组类型签名是:

```
                   "[Ljava/lang/Object; "
```

#### 关联
JNIEnv接口函数表的项6

#### 参数
env:JNI接口指针. 
name:全限定类名称（即包名后跟类名, 之间由“/”分隔）. 如果该名称以“[”（数组签名字符）打头, 则返回一个数组类. 字符串以改进型UTF-8编码.

#### 返回值
返回全限定类名称的一个类对象. 如果找不到该类, 则返回NULL. 

#### 抛出
ClassFormatError:如果类数据指定的类无效. 
ClassCircularityError:如果类或接口是自身的超类或超接口. 
NoClassDefFoundError:如果找不到所请求的类或接口的定义.
OutOfMemoryError:如果系统内存不足. 

### 4.3.3 GetSuperclass

```
jclass GetSuperclass(JNIEnv *env, jclass clazz); 
```

如果`clazz`代表类而非类对象, 则该函数返回由`clazz`所指定的类的超类. 

如果`clazz`指定类对象或代表某个接口, 则该函数返回NULL. 

#### 关联
JNIEnv接口函数表的项10

#### 参数
env:JNI接口指针. 
clazz:Java类对象. 
	
#### 返回值
由clazz所代表的类的超类或NULL. 
	
### 4.3.4 IsAssignableFrom

```
jboolean IsAssignableFrom(JNIEnv *env, jclass clazz1, jclass clazz2); 
```

确定clazz1的对象是否可安全地强制转换为clazz2. 

#### 关联
JNIEnv接口函数表的项11

#### 参数
env:JNI接口指针. 
clazz1:第一个类参数. 
clazz2:第二个类参数. 
	
#### 返回值
下列某个情况为真时返回JNI_TRUE:

* 第一及第二个类参数引用同一个Java类. 
* 第一个类是第二个类的子类. 
* 第二个类是第一个类的某个接口. 
		
## 4.4 异常

### 4.4.1 Throw

```
jint Throw(JNIEnv *env, jthrowable obj); 
```

抛出`java.lang.Throwable`对象. 

#### 关联
JNIEnv接口函数表的项13

#### 参数
env:JNI接口指针. 
obj:java.lang.Throwable对象. 
	
#### 返回值
成功时返回JNI_OK, 失败时返回负数. 
	
#### 抛出
java.lang.Throwable对象obj. 
	
### 4.4.2 ThrowNew

```
jint ThrowNew(JNIEnv *env, jclass clazz, const char *message); 
```

根据指定类和message指定的消息构造异常对象并抛出该异常. 

#### 关联
JNIEnv接口函数表的项14

#### 参数
env:JNI接口指针. 
clazz:java.lang.Throwable的子类. 
message:用于构造java.lang.Throwable对象的消息. 字符串以改进型UTF-8编码.

#### 返回值
成功时返回JNI_OK, 失败时返回负数. 
	
#### 抛出
新构造的java.lang.Throwable对象. 
	
### 4.4.3 ExceptionOccurred

```
jthrowable ExceptionOccurred(JNIEnv *env); 
```

确定是否有异常正被抛出. 直到本地代码调用`ExceptionClear`或Java代码处理该异常前, 异常将始终保持抛出状态. 

#### 关联
JNIEnv接口函数表的项15

#### 参数
env:JNI接口指针. 

#### 返回值
返回正被抛出的异常对象. 如果当前无异常被抛出, 则返回NULL. 
	
### 4.4.4 ExceptionDescribe

```
void ExceptionDescribe(JNIEnv *env); 
```

将异常及堆栈的回溯输出到系统错误报告信道（例如 stderr）. 该例程可便利调试操作. 

#### 关联
JNIEnv接口函数表的项16

#### 参数
env:JNI接口指针. 
	
### 4.4.5 ExceptionClear

```
void ExceptionClear(JNIEnv *env); 
```

清除当前抛出的任何异常. 如果当前无异常被抛出, 则此程序不产生任何效果. 

#### 关联
JNIEnv接口函数表的项17

#### 参数
env:JNI接口指针. 
	
### 4.4.6 FatalError

```
void FatalError(JNIEnv *env, const char *msg); 
```

抛出致命错误并且不期望虚拟机进行修复. 该函数无返回值. 

#### 关联
JNIEnv接口函数表的项18

#### 参数
env: JNI接口指针. 
msg: 错误消息. 字符串以改进型UTF-8编码.

### 4.4.7 ExceptionCheck

```
jboolean ExceptionCheck(JNIEnv *env); 
```

我们引入一个便利的函数来检测异常, 同时不会产生指向异常对象的一个本地引用.

如果当前存在异常, 则返回JNI_TRUE;  或者返回JNI_FALSE.

#### 关联
JNIEnv接口函数表的项228

#### SINCE
JDK/JRE 1.2
	
## 4.5 全局及局部引用

全局引用

局部引用只在本机方法调用期间有效. 当本地方法返回以后, 它们会自动被释放. 每个局部引用都会消耗虚拟机的一些资源. 程序员需要确保本机方法不会过分地分配局部引用. 虽然本地方法返回到Java后局部引用会自动释放, 但是局部引用的过度分配会导致虚拟机在本地方法执行过程中耗尽内存.

弱全局引用是一种特殊的全局引用. 与普通全局引用不同, 弱全局引用允许潜在的Java对象被垃圾回收. 弱全局引用可以在全局或局部引用的任何情况下所使用. 当进行垃圾回收时, 如果对象仅仅被弱全局引用所引用, 则它会释放潜在Java对象. 一个弱全局引用指向一个已经被释放的对象, 则功能上等效于NULL. 程序员可以使用IsSameObject比较弱全局引用和NULL来检测是否一个弱全局引用指向了一个已经被释放的对象.

JNI弱全局引用是Java弱引用的一个简化版本, 参见Java 2平台API(java.lang.ref包和类).

**注意**
1. 因为垃圾回收可能会发生在本地方法运行的时段, 因此弱全局引用所指向的对象会在任何时间被释放. 尽管弱全局引用可以在全局引用使用的任何情况下使用, 但是通常这样做是不合适的, 因为弱全局引用可能会在不经意间功能上等效于NULL.
2. 尽管`IsSameObject`能够用来检测弱全局引用是否指向一个被释放的对象, 但是它并不能阻止此后对象立即被释放. 因此, 程序员最好不要依赖这个检测来确定弱全局引用(非NULL引用)能够在任何未来JNI函数调用中能够使用.
3. 为了克服这种固有的局限性, 建议同一个对象的标准（强）局部或全局引用需要使用JNI函数`NewLocalRef`或`NewGlobalRef`获得, 并且这种强引用用于访问目标对象. 如果对象已被释放, 这些函数将返回空, 否则将返回一个强引用（这将阻止该对象被释放）. 当立即访问该对象不再需要时, 新的引用应显式地删除, 从而允许对象被释放.
4. 弱全局引用是弱于其他类型的弱引用（SoftReference类或WeakReference类的Java对象）. 直到SoftReference或WeakReference对象已经清除了它们指向特定对象的引用, 指向特定对象的弱全局引用才可能功能上等效于NULL.
5. 析构时, 弱全局引用弱于对象的Java内部引用. 只有在所引用对象的析构完成以后, 弱全局引用才可能功能上等效于NULL.
6. 弱全局引用和PhantomReferences之间的相互作用是不确定的. 特别是, 一个Java虚拟机实现可能（或不可能）不会在PhantomReferences之后处理弱全局引用, 并且它可能（或不可能）用弱全局引用来保留对象, 而这个对象也会被PhantomReference对象所指向. 这种未定义弱全局引用使用方式应该被避免.

### 4.5.1 NewGlobalRef

```
jobject NewGlobalRef(JNIEnv *env, jobject obj); 
```

创建obj参数所引用对象的新全局引用. obj参数既可以是全局引用, 也可以是局部引用. 全局引用通过调用DeleteGlobalRef()来显式撤消. 

#### 关联
JNIEnv接口函数表的项21

#### 参数
env:JNI接口指针. 
obj:全局或局部引用. 

#### 返回值
返回全局引用. 如果系统内存不足则返回NULL. 
	
### 4.5.2 DeleteGlobalRef

```
void DeleteGlobalRef(JNIEnv *env, jobject globalRef); 
```

删除globalRef所指向的全局引用. 

#### 关联
JNIEnv接口函数表的项22

#### 参数
env: JNI接口指针. 
globalRef: 全局引用. 
	
### 4.5.3 DeleteLocalRef

```
void DeleteLocalRef(JNIEnv *env, jobject localRef); 
```

删除localRef所指向的局部引用. 

#### 关联
JNIEnv接口函数表的项23

#### 参数
env:JNI接口指针. 
localRef:局部引用. 
	
注意: JDK/JRE 1.1提供了上述`DeleteLocalRef`函数使得程序员能够手动删除局部引用. 例如, 如果本地方法遍历一个可能巨大的数组对象, 并且在每次迭代中都使用元素, 此时一个良好的做法是: 先删除指向不再使用数组元素的引用, 再在下一次迭代中创建一个新局部引用.

而在JDK/JRE 1.2中, 提供了一套补充函数, 用于局部引用生命周期管理. 这些函数是以下所列的四个函数.

### 4.5.4 EnsureLocalCapacity

```
jint EnsureLocalCapacity(JNIEnv *env, jint capacity); 
```

确保至少给定容量的局部引用能够在本地线程中被创建. 如果成功则返回JNI_OK;  否则返回一个负数, 同时抛出OutOfMemoryError.

在进入一个本地方法前, 虚拟机会自动确保至少有16个局部引用能够被创建.

为了向后兼容, 虚拟机会分配超过所确保容量的局部引用. (作为一个调试支持, 虚拟机会给用户警告, 即太多局部引用正在被创建. 在JDK中, 程序员可以使用`-verbose:jni`命令行选项来打开这些消息.) 如果超过所确保容量的局部引用不能够被创建, 虚拟机会调用`FatalError`.

#### 关联
JNIEnv接口函数表的项26

#### 参数
env:JNI接口指针.
capacity:需分配局部引用的容量.

#### SINCE
JDK/JRE 1.2

### 4.5.5 PushLocalFrame

```
jint PushLocalFrame(JNIEnv *env, jint capacity); 
```

创建一个新的局部引用帧, 其中至少给定容量的局部引用被创建. 如果成功则返回JNI_OK;  否则返回一个负数, 同时抛出OutOfMemoryError.

注意: 在之前局部帧中已创建的局部引用在当前局部帧中仍然有效.

#### 关联
JNIEnv接口函数表的项19

#### 参数
env:JNI接口指针.
capacity:需要分配局部引用的容量.

#### SINCE
JDK/JRE 1.2

### 4.5.6 PopLocalFrame

```
jobject PopLocalFrame(JNIEnv *env, jobject result); 
```

弹出当前局部引用帧, 释放所有局部引用, 并且返回之前局部引用帧中一个给定result对象的局部引用.

如果不需要返回之前帧的引用, result参数设置NULL.

#### 关联
JNIEnv接口函数表的项20

#### 参数
env: JNI接口指针.
result: 需分配局部引用的容量.

#### SINCE
JDK/JRE 1.2

### 4.5.7 PopLocalFrame

```
jobject NewLocalRef(JNIEnv *env, jobject ref); 
```

创建一个新的局部引用, 指向ref引用所指向的相同对象. 给定的ref可以是全局或局部引用. 如果ref指向null, 则返回NULL.

#### 关联
JNIEnv接口函数表的项25

#### 参数
env: JNI接口指针.
ref: 全局或局部引用.

#### SINCE
JDK/JRE 1.2

### 4.5.8 NewWeakGlobalRef

```
jweak NewWeakGlobalRef(JNIEnv *env, jobject obj); 
```

创建一个新的弱全局引用. 如果obj指向null或者虚拟机内存不够, 则返回NULL. 如果虚拟机内存不够, 抛出OutOfMemoryError.

#### 关联
JNIEnv接口函数表的项226

#### 参数
env: JNI接口指针.
obj: 全局或局部引用.

#### SINCE
JDK/JRE 1.2

### 4.5.9 DeleteWeakGlobalRef

```
void DeleteWeakGlobalRef(JNIEnv *env, jweak obj); 
```

删除给定弱全局引用的虚拟机资源.

#### 关联
JNIEnv接口函数表的项227

#### 参数
env: JNI接口指针.
obj: 弱全局引用.

#### SINCE
JDK/JRE 1.2

## 4.6 对象操作

### 4.6.1 AllocObject

```
jobject AllocObject(JNIEnv *env, jclass clazz); 
```

分配新Java对象而不调用该对象的任何构造函数. 返回该对象的引用. clazz参数务必不要引用数组类. 

#### 关联
JNIEnv接口函数表的项27

#### 参数
env:JNI接口指针. 
clazz:Java类对象. 
	
#### 返回值
返回Java对象. 如果无法构造该对象, 则返回NULL. 
	
#### 抛出
InstantiationException:如果该类为一个接口或抽象类. 
OutOfMemoryError:如果系统内存不足. 
	
### 4.6.2 NewObject, NewObjectA, NewObjectV 

```
jobject NewObject(JNIEnv *env, jclass clazz, jmethodID methodID, ...); 
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args); 
jobject NewObjectV(JNIEnv *env, jclass clazz, jmethodID methodID, va_list args); 
```

构造新Java对象. 方法ID指示应调用的构造函数方法. 该ID必须通过调用`GetMethodID`获得, 且调用时的方法名必须为`<init>`, 而返回类型必须为`void`(即V). 

clazz参数务必不要引用数组类. 

#### NewObject

程序员应将传递给构造函数的所有参数紧跟着放在methodID参数的后面. NewObject()收到这些参数后, 将把它们传给程序员所要调用的Java方法. 

#### 关联
JNIEnv接口函数表的项28

#### NewObjectA

程序员应将传递给构造函数的所有参数放在jvalues类型的数组args中, 该数组紧跟着放在methodID参数的后面. `NewObjectA`收到数组中的这些参数后, 将把它们传给程序员所要调用的Java方法.

#### 关联
JNIEnv接口函数表的项30

#### NewObjectV

程序员应将传递给构造函数的所有参数放在va_list类型的参数args中, 该参数紧跟着放在methodID参数的后面. `NewObjectV`收到这些参数后, 将把它们传给程序员所要调用的Java方法. 

#### 关联
JNIEnv接口函数表的项29

#### 参数
env:JNI接口指针. 
clazz:Java类对象. 
methodID:构造函数的方法ID. 

#### NewObject的其它参数
传给构造函数的参数. 

#### NewObjectA的其它参数
args:传给构造函数的参数数组. 

#### NewObjectV的其它参数
args:传给构造函数的参数va_list. 

#### 返回值
返回Java对象, 如果无法构造该对象, 则返回NULL. 

#### 抛出
InstantiationException:如果该类为接口或抽象类. 
OutOfMemoryError:如果系统内存不足. 
构造函数抛出的任何异常. 

### 4.6.3 GetObjectClass

```
jclass GetObjectClass(JNIEnv *env, jobject obj); 
```

返回对象的类. 

#### 关联
JNIEnv接口函数表的项31

#### 参数
env:JNI接口指针. 
obj:Java对象（不能为NULL）. 

#### 返回值
返回Java类对象. 

### 4.6.4 GetObjectRefType

```
jobjectRefType GetObjectRefType(JNIEnv* env, jobject obj); 
```

返回obj所指向对象的类型. obj参数可以是局部, 全局或弱全局引用. 

#### 关联
JNIEnv接口函数表的项232

#### 参数
env: JNI接口指针. 
obj: 局部, 全局或弱全局引用. 

#### 返回值
GetObjectRefType函数会返回下列jobjectRefType定义的枚举值之一:

```
JNIInvalidRefType = 0,
JNILocalRefType = 1,
JNIGlobalRefType = 2,
JNIWeakGlobalRefType = 3
```

如果obj参数是弱全局引用类型, 则返回值是JNIWeakGlobalRefType.
如果obj参数是全局引用类型, 则返回值是JNIGlobalRefType.
如果obj参数是局部引用类型, 则返回值是JNILocalRefType.
如果obj蚕食不是一个有效的引用, 则函数返回值是JNIInvalidRefType.

一个无效引用是非有效操作的引用, 即obj指针地址没有指向由Ref创建函数之一所分配或JNI函数所返回的内存中的位置.

因此, NULL是一个无效引用, 并且GetObjectRefType(env,NULL)会返回 JNIInvalidRefType.

另一方面, 一个null引用是一个指向null的引用, 因此会返回null引用原来所创建引用的类型.

GetObjectRefType不能用于已被删除的引用.

因为引用通常是实现为指向内存数据结构的指针, 可能会被虚拟机中的任何引用分配服务所重用, 因此一旦被删除, GetObjectRefType的返回值是不难以说明的.

#### SINCE
JDR/JRE 1.6

### 4.6.5 IsInstanceOf 

```
jboolean IsInstanceOf(JNIEnv *env, jobject obj, jclass clazz); 
```

测试对象是否为某个类的实例.

#### 关联
JNIEnv接口函数表的项32

#### 参数
env:JNI接口指针. 
obj:Java对象. 
clazz:Java类对象. 
	
#### 返回值
如果可将obj强制转换为clazz, 则返回JNI_TRUE. 否则返回JNI_FALSE. NULL对象可强制转换为任何类. 
	
### 4.6.6 IsSameObject

```
jboolean IsSameObject(JNIEnv *env, jobject ref1, jobject ref2); 
```

测试两个引用是否指向同一Java对象. 

#### 关联
JNIEnv接口函数表的项24

#### 参数
env:JNI接口指针. 
ref1:Java对象. 
ref2:Java对象. 
	
#### 返回值
如果ref1和ref2引用同一Java对象或均为NULL, 则返回JNI_TRUE. 否则返回JNI_FALSE. 

## 4.7 访问对象域

### 4.7.1 GetFieldID

```
jfieldID GetFieldID(JNIEnv *env, jclass clazz, const char *name, const char *sig); 
```

返回类的实例（非静态）域的域ID. 该域由其名称及签名指定. 访问器函数的`Get<type>Field`及`Set<type>Field`系列使用域ID检索对象域. 

`GetFieldID`将未初始化的类初始化. 

`GetFieldID`不能用于获取数组的长度域. 应使用`GetArrayLength`.

#### 关联
JNIEnv接口函数表的项94

#### 参数
env: JNI接口指针. 
clazz: Java类对象. 
name: 0-terminated的改进型UTF-8字符串中的域名. 
sig: 0-terminated的改进型UTF-8字符串中的域签名. 
	
#### 返回值
域ID. 如果操作失败, 则返回NULL. 
	
#### 抛出
NoSuchFieldError:如果找不到指定的域. 
ExceptionInInitializerError:如果由于异常而导致类初始化程序失败. 
OutOfMemoryError:如果系统内存不足. 

### 4.7.2 `Get<type>Field`例程

```
NativeType Get<type>Field(JNIEnv *env, jobject obj, jfieldID fieldID); 
```

该访问器例程系列返回对象的实例（非静态）域的值. 要访问的域由通过调用`GetFieldID`而得到的域ID指定. 

下表说明了`Get<type>`Field例程名及结果类型. 应将`Get<type>Field`中的type替换为域的Java类型（或使用表中的某个实际例程名）, 然后将NativeType替换为该例程对应的本地类型. 

| **`Get<type>Field`例程名** | **本地类型** |
| ------------------------ | ------------ |
| GetObjectField() | jobject |
| GetBooleanField() | jboolean |
| GetByteField() | jbyte |
| GetCharField() | jchar |
| GetShortField() | jshort |
| GetIntField() | jint |
| GetLongField() | jlong |
| GetFloatField() | jfloat |
| GetDoubleField() | jdouble |

#### 关联
JNIEnv接口函数表的项

| **`Get<type>Field`例程名** | **项** |
| ------------------------ | ------ |
| GetObjectField() | 95 |
| GetBooleanField() | 96 |
| GetByteField() | 97 |
| GetCharField() | 98 |
| GetShortField() | 99 |
| GetIntField() | 100 |
| GetLongField() | 101 |
| GetFloatField() | 102 |
| GetDoubleField() | 103 |

#### 参数
env:JNI接口指针. 
obj:Java对象（不能为 NULL）. 
fieldID:有效的域ID. 
	
#### 返回值
域的内容. 
	
### 4.7.3 `Set<type>Field`例程

```
void Set<type>Field(JNIEnv *env, jobject obj, jfieldID fieldID, NativeType value); 
```

该访问器例程系列设置对象的实例（非静态）域的值. 要访问的域由通过调用`SetFieldID`而得到的域ID指定. 

下表说明了`Set<type>Field`例程名及结果类型. 应将`Set<type>Field`中的type替换为域的Java类型（或使用表中的某个实际例程名）, 然后将NativeType替换为该例程对应的本地类型. 

| **`Set<type>Field`例程名** | **本地类型** |
| ------------------------ | ------------ |
| SetObjectField() | jobject |
| SetBooleanField() | jboolean |
| SetByteField() | jbyte |
| SetCharField() | jchar |
| SetShortField() | jshort |
| SetIntField() | jint |
| SetLongField() | jlong |
| SetFloatField() | jfloat |
| SetDoubleField() | jdouble |

#### 关联
JNIEnv接口函数表的项

| **`Get<type>Field`例程名** | **项** |
| ------------------------ | ------ |
| GetObjectField() | 104 |
| GetBooleanField() | 105 |
| GetByteField() | 106 |
| GetCharField() | 107 |
| GetShortField() | 108 |
| GetIntField() | 109 |
| GetLongField() | 110 |
| GetFloatField() | 111 |
| GetDoubleField() | 112 |

#### 参数
env:JNI接口指针. 
obj:Java对象（不能为NULL）. 
fieldID:有效的域ID. 
value:域的新值. 

## 4.8 调用实例方法

### 4.8.1 GetMethodID

```
jmethodID GetMethodID(JNIEnv *env, jclass clazz, const char *name, const char *sig); 
```

返回类或接口实例（非静态）方法的方法ID. 方法可在某个clazz的超类中定义, 也可从clazz继承. 该方法由其名称和签名决定.

GetMethodID()可使未初始化的类初始化.

要获得构造函数的方法ID, 应将<init>作为方法名, 同时将void (V)作为返回类型. 

#### 关联
JNIEnv接口函数表的项33

#### 参数
env:JNI接口指针. 
clazz:Java类对象. 
name: 0-terminated的改进型UTF-8字符串中的方法名. 
sig:  0-terminated的改进型UTF-8字符串中的方法签名. 

#### 返回值
方法ID, 如果找不到指定的方法, 则为NULL. 

#### 抛出
NoSuchMethodError:如果找不到指定方法. 
ExceptionInInitializerError:如果由于异常而导致类初始化程序失败. 
OutOfMemoryError:如果系统内存不足. 

### 4.8.2 `Call<type>Method`, `Call<type>MethodA`, `Call<type>MethodV`例程

```
NativeType Call<type>Method(JNIEnv *env, jobject obj, jmethodID methodID, ...); 
NativeType Call<type>MethodA(JNIEnv *env, jobject obj, jmethodID methodID, jvalue *args); 
NativeType Call<type>MethodV(JNIEnv *env, jobject obj, jmethodID methodID, va_list args); 
```

这三个操作的方法用于从本地方法调用Java实例方法. 它们的差别仅在于向其所调用的方法传递参数时所用的机制. 

这三个操作将根据所指定的方法ID调用Java对象的实例（非静态）方法. 参数methodID必须通过调用`GetMethodID`来获得.

当这些函数用于调用私有方法和构造函数时, 方法ID必须从obj的真实类派生而来, 而不应从其某个超类派生. 

#### `Call<type>Method`例程
程序员应将要传给方法的所有参数紧跟着放在methodID参数之后. Call<type>Method例程接受这些参数并将其传给程序员所要调用的Java方法. 

#### `Call<type>MethodA`例程
程序员应将要传给方法的所有参数放在紧跟在methodID参数之后的jvalues类型数组args中. `Call<type>MethodA`例程接受这些数组中的参数并将其传给程序员所要调用的Java方法. 

#### `Call<type>MethodV`例程
程序员将方法的所有参数放在紧跟着在methodID参数之后的va_list类型参数变量中. `Call<type>MethodV`例程接受这些参数并将其传给程序员所要调用的Java方法. 

下表根据结果类型说明了各个方法调用例程. 用户应将`Call<type>Method`中的type替换为所调用方法的Java类型（或使用表中的实际方法调用例程名）, 同时将NativeType替换为该例程相应的本地类型. 

| **`Call<type>Method`例程名** | **本地类型** |
| -------------------------- | ------------ |
| CallVoidMethod() CallVoidMethodA() CallVoidMethodV() | void |
| CallObjectMethod() CallObjectMethodA() CallObjectMethodV() | jobject |
| CallBooleanMethod() CallBooleanMethodA() CallBooleanMethodV() | jboolean |
| CallByteMethod() CallByteMethodA() CallByteMethodV() | jbyte |
| CallCharMethod() CallCharMethodA() CallCharMethodV() | jchar |
| CallShortMethod() CallShortMethodA() CallShortMethodV() | jshort |
| CallIntMethod() CallIntMethodA() CallIntMethodV() | jint |
| CallLongMethod() CallLongMethodA() CallLongMethodV() | jlong |
| CallFloatMethod() CallFloatMethodA() CallFloatMethodV() | jfloat |
| CallDoubleMethod() CallDoubleMethodA() CallDoubleMethodV() | jdouble |

#### 关联
JNIEnv接口函数表的项

| **`Call<type>Method`例程名** | **项** |
| -------------------------- | ------ |
| CallVoidMethod() CallVoidMethodA() CallVoidMethodV() | 61 63 62 |
| CallObjectMethod() CallObjectMethodA() CallObjectMethodV() | 34 36 35 |
| CallBooleanMethod() CallBooleanMethodA() CallBooleanMethodV() | 37 39 38 |
| CallByteMethod() CallByteMethodA() CallByteMethodV() | 40 42 41 |
| CallCharMethod() CallCharMethodA() CallCharMethodV() | 43 45 44 |
| CallShortMethod() CallShortMethodA() CallShortMethodV() | 46 48 47 |
| CallIntMethod() CallIntMethodA() CallIntMethodV() | 49 51 50 |
| CallLongMethod() CallLongMethodA() CallLongMethodV() | 52 54 53 |
| CallFloatMethod() CallFloatMethodA() CallFloatMethodV() | 55 57 56 |
| CallDoubleMethod() CallDoubleMethodA() CallDoubleMethodV() | 58 60 59 |

#### 参数
env:JNI接口指针. 
obj:Java对象. 
methodID:方法ID. 
	
#### `Call<type>Method`例程的其它参数
要传给Java方法的参数. 
	
#### `Call<type>MethodA`例程的其它参数
args:参数数组. 
	
#### `Call<type>MethodV`例程的其它参数
args:参数的va_list. 

#### 返回值
返回调用Java方法的结果. 

#### 抛出
执行Java方法时抛出的异常. 
	
### 4.8.3 `CallNonvirtual<type>Method`, `CallNonvirtual<type>MethodA`, `CallNonvirtual<type>MethodV`例程

```
NativeType CallNonvirtual<type>Method(JNIEnv *env, jobject obj, jclass clazz, jmethodID methodID, ...); 
NativeType CallNonvirtual<type>MethodA(JNIEnv *env, jobject obj, jclass clazz, jmethodID methodID, jvalue *args); 
NativeType CallNonvirtual<type>MethodV(JNIEnv *env, jobject obj, jclass clazz, jmethodID methodID, va_list args); 
```

这些操作根据指定的类和方法ID调用某Java对象的实例（非静态）方法. 参数methodID必须通过调用clazz类的`GetMethodID`获得. 

`CallNonvirtual<type>Method`和`Call<type>Method`例程系列并不相同. `Call<type>Method`例程根据对象的类调用方法, 而`CallNonvirtual<type>Method`例程则根据获得方法ID的（由clazz参数指定）类调用方法. 方法ID必须从对象的真实类或其某个超类获得. 

#### `CallNonvirtual<type>Method`例程
程序员应将要传给方法的所有参数紧跟着放在methodID参数之后. `CallNonvirtual<type>Method`例程接受这些参数并将其传给编程人员所要调用的Java方法. 

#### `CallNonvirtual<type>MethodA`例程
程序员应将要传给方法的所有参数放在紧跟在methodID参数之后的jvalues类型数组args中. `allNonvirtual<type>MethodA`例程接受这些数组中的参数并将其传给程序员所要调用的Java方法. 

#### CallNonvirtual<type>MethodV例程
程序员应将要传给方法的所有参数放在紧跟在methodID参数之后的va_list类型参数args中. `CallNonvirtualMethodV`例程接受这些参数并将其传给程序员所要调用的Java方法. 

下表根据结果类型说明了各个方法调用例程. 用户应将`CallNonvirtual<type>Method`中的type替换为所调用方法的Java类型（或使用
表中的实际方法调用例程名）, 同时将NativeType替换为该例程相应的本地类型. 

| **`CallNonvirtual<type>Method`例程名** | **本地类型** |
| ------------------------------------ | ------------ |
| CallNonvirtualVoidMethod() CallNonvirtualVoidMethodA() CallNonvirtualVoidMethodV() | void |
| CallNonvirtualObjectMethod() CallNonvirtualObjectMethodA() CallNonvirtualObjectMethodV() | jobject |
| CallNonvirtualBooleanMethod() CallNonvirtualBooleanMethodA() CallNonvirtualBooleanMethodV() | jboolean |
| CallNonvirtualByteMethod() CallNonvirtualByteMethodA() CallNonvirtualByteMethodV() | jbyte |
| CallNonvirtualCharMethod() CallNonvirtualCharMethodA() CallNonvirtualCharMethodV() | jchar |
| CallNonvirtualShortMethod() CallNonvirtualShortMethodA() CallNonvirtualShortMethodV() | jshort |
| CallNonvirtualIntMethod() CallNonvirtualIntMethodA() CallNonvirtualIntMethodV() | jint |
| CallNonvirtualLongMethod() CallNonvirtualLongMethodA() CallNonvirtualLongMethodV() | jlong |
| CallNonvirtualFloatMethod() CallNonvirtualFloatMethodA() CallNonvirtualFloatMethodV() | jfloat |
| CallNonvirtualDoubleMethod() CallNonvirtualDoubleMethodA() CallNonvirtualDoubleMethodV() | jdouble |

#### 关联
JNIEnv接口函数表的项

| **`CallNonvirtual<type>Method`例程名** | **项** |
| ------------------------------------ | ------ |
| CallNonvirtualVoidMethod() CallNonvirtualVoidMethodA() CallNonvirtualVoidMethodV() | 91 93 92 |
| CallNonvirtualObjectMethod() CallNonvirtualObjectMethodA() CallNonvirtualObjectMethodV() | 64 66 65 |
| CallNonvirtualBooleanMethod() CallNonvirtualBooleanMethodA() CallNonvirtualBooleanMethodV() | 67 69 68 |
| CallNonvirtualByteMethod() CallNonvirtualByteMethodA() CallNonvirtualByteMethodV() | 70 72 71 |
| CallNonvirtualCharMethod() CallNonvirtualCharMethodA() CallNonvirtualCharMethodV() | 73 75 74 |
| CallNonvirtualShortMethod() CallNonvirtualShortMethodA() CallNonvirtualShortMethodV() | 76 78 77 |
| CallNonvirtualIntMethod() CallNonvirtualIntMethodA() CallNonvirtualIntMethodV() | 79 81 80 |
| CallNonvirtualLongMethod() CallNonvirtualLongMethodA() CallNonvirtualLongMethodV() | 82 84 83 |
| CallNonvirtualFloatMethod() CallNonvirtualFloatMethodA() CallNonvirtualFloatMethodV() | 85 87 86 |
| CallNonvirtualDoubleMethod() CallNonvirtualDoubleMethodA() CallNonvirtualDoubleMethodV() | 88 90 89 |
	
#### 参数
env:JNI接口指针. 
clazz:Java类. 
obj: Java对象. 
methodID:方法ID. 
	
#### `CallNonvirtual<type>Method`例程的其它参数
要传给Java方法的参数. 
	
#### `CallNonvirtual<type>MethodA`例程的其它参数
args:参数数组. 
	
#### `CallNonvirtual<type>MethodV`例程的其它参数
args:参数的va_list. 
	
#### 返回值
调用Java方法的结果. 

#### 抛出
执行Java方法时所抛出的异常. 
	
## 4.9 访问静态域

### 4.9.1 GetStaticFieldID

```
jfieldID GetStaticFieldID(JNIEnv *env, jclass clazz, const char *name, const char *sig); 
```

返回类的静态域的域ID. 域由其名称和签名指定.`GetStatic<type>Field`和`SetStatic<type>Field`访问器函数系列使用域ID检索静态域. 

`GetStaticFieldID`将未初始化的类初始化. 

#### 关联
JNIEnv接口函数表的项144

#### 参数
env: JNI接口指针. 
clazz: Java类对象. 
name: 0-terminated的改进型UTF-8字符串中的静态域名. 
sig: 0-terminated的改进型UTF-8字符串中的域签名. 

#### 返回值
域ID. 如果找不到指定的静态域, 则为NULL. 

#### 抛出
NoSuchFieldError:如果找不到指定的静态域. 
ExceptionInInitializerError:如果由于异常而导致类初始化程序失败. 
OutOfMemoryError:如果系统内存不足. 

### 4.9.2 `GetStatic<type>Field`例程

```
NativeType GetStatic<type>Field(JNIEnv *env, jclass clazz, jfieldID fieldID); 
```

该访问器例程系列返回对象的静态域的值. 要访问的域由通过调用`GetStaticFieldID`而得到的域ID指定. 

下表说明了`GetStatic<type>Field`例程名及结果类型. 应将`GetStatic<type>Field`中的type替换为域的Java类型（或使用表中的某个实际例程名）, 然后将NativeType替换为该例程对应的本地类型. 

| **`GetStatic<type>Field`例程名** | **本地类型** |
| ------------------------------ | ------------ |
| GetStaticObjectField() | jobject |
| GetStaticBooleanField() | jboolean |
| GetStaticByteField() | jbyte |
| GetStaticCharField() | jchar |
| GetStaticShortField() | jshort |
| GetStaticIntField() | jint |
| GetStaticLongField() | jlong |
| GetStaticFloatField() | jfloat |
| GetStaticDoubleField() | jdouble |

#### 关联
JNIEnv接口函数表的项

| **`GetStatic<type>Field`例程名** | **项** |
| ------------------------------ | ------ |
| GetStaticObjectField() | 145 |
| GetStaticBooleanField() | 146 |
| GetStaticByteField() | 147 |
| GetStaticCharField() | 148 |
| GetStaticShortField() | 149 |
| GetStaticIntField() | 150 |
| GetStaticLongField() | 151 |
| GetStaticFloatField() | 152 |
| GetStaticDoubleField() | 153 |

#### 参数
env:JNI接口指针. 
clazz:Java类对象. 
fieldID:静态域ID. 

#### 返回值
静态域的内容. 
	
### 4.9.3 `SetStatic<type>Field`例程

```
void SetStatic<type>Field(JNIEnv *env, jclass clazz, jfieldID fieldID, NativeType value); 
```

该访问器例程系列设置对象的静态域的值. 要访问的域由通过调用`GetStaticFieldID`而得到的域ID指定.

下表说明了`SetStatic<type>Field`例程名及结果类型. 应将`SetStatic<type>Field`中的type替换为域的Java类型（或使用表中的某个实际例程名）, 然后将NativeType替换为该例程对应的本地类型. 

| **`SetStatic<type>Field`例程名** | **本地类型** |
| ------------------------------ | ------------ |
| SetStaticObjectField() | jobject |
| SetStaticBooleanField() | jboolean |
| SetStaticByteField() | jbyte |
| SetStaticCharField() | jchar |
| SetStaticShortField() | jshort |
| SetStaticIntField() | jint |
| SetStaticLongField() | jlong |
| SetStaticFloatField() | jfloat |
| SetStaticDoubleField() | jdouble |

#### 关联
JNIEnv接口函数表的项

| **`SetStatic<type>Field`例程名** | **项** |
| ------------------------------ | ------ |
| SetStaticObjectField() | 154 |
| SetStaticBooleanField() | 155 |
| SetStaticByteField() | 156 |
| SetStaticCharField() | 157 |
| SetStaticShortField() | 158 |
| SetStaticIntField() | 159 |
| SetStaticLongField() | 160 |
| SetStaticFloatField() | 161 |
| SetStaticDoubleField() | 162 |

#### 参数
env:JNI接口指针. 
clazz:Java类对象. 
fieldID:静态域ID. 
value:域的新值. 

## 4.10 调用静态方法

### 4.10.1 GetStaticMethodID

```
jmethodID GetStaticMethodID(JNIEnv *env, jclass clazz, const char *name, const char *sig); 
```

返回类的静态方法的方法ID. 方法由其名称和签名指定. 

`GetStaticMethodID`将未初始化的类初始化.

#### 关联
JNIEnv接口函数表的项113

#### 参数
env: JNI接口指针. 
clazz: Java类对象. 
name: 0-terminated的改进型UTF-8字符串中的静态方法名. 
sig: 0-terminated的改进型UTF-8字符串中的方法签名. 

#### 返回值
方法ID, 如果操作失败, 则为NULL. 

#### 抛出
NoSuchMethodError:如果找不到指定的静态方法. 
ExceptionInInitializerError:如果由于异常而导致类初始化程序失败. 
OutOfMemoryError:如果系统内存不足. 

### 4.10.2 `CallStatic<type>Method`, `CallStatic<type>MethodA`, `CallStatic<type>MethodV`例程

```
NativeType CallStatic<type>Method(JNIEnv *env, jclass clazz, jmethodID methodID, ...); 
NativeType CallStatic<type>MethodA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args); 
NativeType CallStatic<type>MethodV(JNIEnv *env, jclass clazz, jmethodID methodID, va_list args); 
```

这些操作将根据指定的方法ID调用Java对象的静态方法. methodID参数必须通过调用`GetStaticMethodID`得到.

方法ID必须从clazz派生, 而不能从其超类派生. 

#### `CallStatic<type>Method`例程
程序员应将要传给方法的所有参数紧跟着放在methodID参数之后. CallStatic<type>Method 例程接受这些参数并将其传给程序员所要调用的Java方法. 

#### `CallStatic<type>MethodA`例程
程序员应将要传给方法的所有参数放在紧跟在methodID参数之后的jvalues类型数组args中. `CallStaticMethodA`例程接受这些数组中的参数并将其传给程序员所要调用的Java方法. 

#### `CallStatic<type>MethodV`例程
程序员应将要传给方法的所有参数放在紧跟在methodID参数之后的va_list类型参数args中. `CallStaticMethodV`例程接受这些参数并将其传给程序员所要调用的Java方法. 

下表根据结果类型说明了各个方法调用例程. 用户应将`CallStatic<type>Method`中的type替换为所调用方法的Java类型（或使用表中的实际方法调用例程名）, 同时将NativeType替换为该例程相应的本地类型. 

| **`CallStatic<type>Method例程名`** | **本地类型** |
| -------------------------------- | ------------ |
| CallStaticVoidMethod() CallStaticVoidMethodA() CallStaticVoidMethodV() | void |
| CallStaticObjectMethod() CallStaticObjectMethodA() CallStaticObjectMethodV() | jobject |
| CallStaticBooleanMethod() CallStaticBooleanMethodA() CallStaticBooleanMethodV() | jboolean |
| CallStaticByteMethod() CallStaticByteMethodA() CallStaticByteMethodV() | jbyte |
| CallStaticCharMethod() CallStaticCharMethodA() CallStaticCharMethodV() | jchar |
| CallStaticShortMethod() CallStaticShortMethodA() CallStaticShortMethodV() | jshort |
| CallStaticIntMethod() CallStaticIntMethodA() CallStaticIntMethodV() | jint |
| CallStaticLongMethod() CallStaticLongMethodA() CallStaticLongMethodV() | jlong |
| CallStaticFloatMethod() CallStaticFloatMethodA() CallStaticFloatMethodV() | jfloat |
| CallStaticDoubleMethod() CallStaticDoubleMethodA() CallStaticDoubleMethodV() | jdouble |
	
#### 关联
JNIEnv接口函数表的项

| **`CallStatic<type>Method`例程名** | **本地类型** |
| -------------------------------- | ------------ |
| CallStaticVoidMethod() CallStaticVoidMethodA() CallStaticVoidMethodV() | 141 143 142 |
| CallStaticObjectMethod() CallStaticObjectMethodA() CallStaticObjectMethodV() | 114 116 115 |
| CallStaticBooleanMethod() CallStaticBooleanMethodA() CallStaticBooleanMethodV() | 117 119 118 |
| CallStaticByteMethod() CallStaticByteMethodA() CallStaticByteMethodV() | 120 122 121 |
| CallStaticCharMethod() CallStaticCharMethodA() CallStaticCharMethodV() | 123 125 124 |
| CallStaticShortMethod() CallStaticShortMethodA() CallStaticShortMethodV() | 126 128 127 |
| CallStaticIntMethod() CallStaticIntMethodA() CallStaticIntMethodV() | 129 131 130 |
| CallStaticLongMethod() CallStaticLongMethodA() CallStaticLongMethodV() | 132 134 133 |
| CallStaticFloatMethod() CallStaticFloatMethodA() CallStaticFloatMethodV() | 135 137 136 |
| CallStaticDoubleMethod() CallStaticDoubleMethodA() CallStaticDoubleMethodV() | 138 140 139 |

#### 参数
env:JNI接口指针. 
clazz:Java类对象. 
methodID:静态方法ID. 
	
#### `CallStatic<type>Method`例程的其它参数
要传给静态方法的参数. 
	
#### `CallStatic<type>MethodA`例程的其它参数
args:参数数组. 
	
#### `CallStatic<type>MethodV`例程的其它参数
args:参数的va_list. 
	
#### 返回值
返回调用静态Java方法的结果. 
	
#### 抛出
执行Java方法时抛出的异常. 
	
## 4.11 字符串操作

### 4.11.1 NewString

```
jstring NewString(JNIEnv *env, const jchar *unicodeChars, jsize len); 
```

利用Unicode字符数组构造新的java.lang.String对象.

#### 关联
JNIEnv接口函数表的项163

#### 参数
env: JNI接口指针. 
unicodeChars: 指向Unicode字符串的指针. 
len: Unicode字符串的长度. 

#### 返回值
Java字符串对象. 如果无法构造该字符串, 则为NULL. 

#### 抛出
OutOfMemoryError:如果系统内存不足. 
	
### 4.11.2 GetStringLength

```
jsize GetStringLength(JNIEnv *env, jstring string); 
```

返回Java字符串的长度（Unicode字符数）.

#### 关联
JNIEnv接口函数表的项164

#### 参数
env:JNI接口指针. 
string:Java字符串对象. 

#### 返回值
Java字符串的长度. 
	
### 4.11.3 GetStringChars

```
const jchar * GetStringChars(JNIEnv *env, jstring string, jboolean *isCopy); 
```

返回指向字符串的Unicode字符数组的指针. 该指针在调用ReleaseStringchars()前一直有效.

如果isCopy非空, 则在复制完成后将*isCopy设为JNI_TRUE. 如果没有复制, 则设为JNI_FALSE.

#### 关联
JNIEnv接口函数表的项165

#### 参数
env:JNI接口指针. 
string:Java字符串对象. 
isCopy:指向布尔值的指针. 

#### 返回值
指向Unicode字符串的指针, 如果操作失败, 则返回NULL. 

### 4.11.4 ReleaseStringChars 

```
void ReleaseStringChars(JNIEnv *env, jstring string, const jchar *chars); 
```

通知虚拟机本地代码无需再访问chars. 参数chars是一个指针, 可通过GetStringChars()从string获得.

#### 关联
JNIEnv接口函数表的项166

#### 参数
env:JNI接口指针. 
string:Java字符串对象. 
chars:指向Unicode字符串的指针. 
	
### NewStringUTF

```
jstring NewStringUTF(JNIEnv *env, const char *bytes); 
```

利用UTF-8字符数组构造新java.lang.String对象.

#### 关联
JNIEnv接口函数表的项167

#### 参数
env:JNI接口指针. 如果无法构造该字符串, 则为NULL. 
bytes:指向改进型UTF-8字符串的指针. 

#### 返回值
Java字符串对象. 如果无法构造该字符串, 则为NULL. 

#### 抛出
OutOfMemoryError:如果系统内存不足. 

### 4.11.5 GetStringUTFLength

```
jsize GetStringUTFLength(JNIEnv *env, jstring string); 
```

以字节为单位返回字符串的UTF-8长度.

#### 关联
JNIEnv接口函数表的项168

#### 参数
env:JNI接口指针. 
string:Java字符串对象. 

#### 返回值
返回字符串的UTF-8长度. 
	
### 4.11.6 GetStringUTFChars

```
const char* GetStringUTFChars(JNIEnv *env, jstring string, jboolean *isCopy); 
```

返回指向字符串的UTF-8字符数组的指针. 该数组在被ReleaseStringUTFChars()释放前将一直有效.

如果isCopy不是NULL, *isCopy在复制完成后即被设为JNI_TRUE. 如果未复制, 则设为JNI_FALSE.

#### 关联
JNIEnv接口函数表的项169

#### 参数
env:JNI接口指针. 
string:Java字符串对象. 
isCopy:指向布尔值的指针. 

#### 返回值
指向改进型UTF-8字符串的指针. 如果操作失败, 则为NULL. 

### 4.11.7 ReleaseStringUTFChars

```
void ReleaseStringUTFChars(JNIEnv *env, jstring string, const char *utf); 
```

通知虚拟机本地代码无需再访问utf. utf参数是一个指针, 可利用GetStringUTFChars()从string获得.

#### 关联
JNIEnv接口函数表的项170

#### 参数
env:JNI接口指针. 
string:Java字符串对象. 
utf:指向改进型UTF-8字符串的指针. 

**注意**: 在JDK/JRE 1.1中, 程序员可以在用户提供的缓冲区获取原始数组元素. 而作为JDR/JRE 1.2附加功能集, 提供了允许本地代码在用户提供的缓冲区中获取Unicode(UTF-16)编码或改进型UTF8编码的字符. 见下面的函数.

### 4.11.8 GetStringRegion

```
void GetStringRegion(JNIEnv *env, jstring str, jsize start, jsize len, jchar *buf); 
```

从start偏移开始拷贝len个Unicode字符到给定缓冲区buf中.

当索引溢出时, 抛出StringIndexOutOfBoundsException.

#### 关联
JNIEnv接口函数表的项220

#### 参数
env:JNI接口指针. 
str:Java字符串对象. 
start:偏移
len:拷贝数量
buf:指向Unicode字符串的指针

#### SINCE
JDR/JRE 1.2

### 4.11.9 GetStringUTFRegion

```
void GetStringUTFRegion(JNIEnv *env, jstring str, jsize start, jsize len, char *buf); 
```

从start偏移开始转化len个Unicode字符到改进型UTF-8编码, 并将结果输出在给定缓冲区buf中.

当下标溢出时, 抛出StringIndexOutOfBoundsException.

#### 关联
JNIEnv接口函数表的项221

#### 参数
env:JNI接口指针. 
str:Java字符串对象. 
start:偏移
len:拷贝数量
buf:指向改进型UTF-8字符串的指针

#### SINCE
JDR/JRE 1.2

### 4.11.10 GetStringCritical, ReleaseStringCritical

```
const jchar * GetStringCritical(JNIEnv *env, jstring string, jboolean *isCopy); 
void ReleaseStringCritical(JNIEnv *env, jstring string, const jchar *carray); 
```

这两个函数的语义是类似于现有的Get/ReleaseStringChars函数. 如果可能的话, 虚拟机将返回一个指向字符串元素的指针;  否则是一个副本. **然而，在这些函数的使用上仍然有显着的限制**. 在Get/ReleaseStringCritical封闭的代码段中, 本机代码不能造成任意JNI调用或使当前线程阻塞.

Get/ReleaseStringCritical上的限制和Get/ReleasePrimitiveArrayCritical相近.

#### 关联
GetStringCritical的JNIEnv接口函数表的项224.
GetStringCritical的JNIEnv接口函数表的项225.

#### SINCE
JDR/JRE 1.2
	
## 4.12 数组操作

### 4.12.1 GetArrayLength

```
jsize GetArrayLength(JNIEnv *env, jarray array); 
```

返回数组中的元素数量.

#### 关联
JNIEnv接口函数表的项171.

#### 参数
env:JNI接口指针. 
array:Java数组对象. 
	
#### 返回值
数组的长度. 
	
### 4.12.2 NewObjectArray

```
jarray NewObjectArray(JNIEnv *env, jsize length, jclass elementClass, jobject initialElement); 
```

构造新的数组, 保存elementClass类的对象. 所有元素初始值均设为initialElement.

#### 关联
JNIEnv接口函数表的项172.

#### 参数
env:JNI接口指针. 
length:数组大小. 
elementClass:数组元素类. 
initialElement:初始值. 
	
#### 返回值
Java数组对象. 如果无法构造数组, 则为NULL. 

#### 抛出
OutOfMemoryError:如果系统内存不足. 

### 4.12.3 GetObjectArrayElement

```
jobject GetObjectArrayElement(JNIEnv *env, jobjectArray array, jsize index); 
```

返回Object数组的元素.

#### 关联
JNIEnv接口函数表的项173.

#### 参数
env:JNI接口指针. 
array:Java数组. 
index:数组下标. 

#### 返回值
Java对象. 

#### 抛出
ArrayIndexOutOfBoundsException:如果index不是数组中的有效下标. 
	
### 4.12.4 SetObjectArrayElement

```
void SetObjectArrayElement(JNIEnv *env, jobjectArray array, jsize index, jobject value); 
```

设置Object数组的元素.

#### 关联
JNIEnv接口函数表的项174.

#### 参数
env:JNI接口指针. 
array:Java数组. 
index:数组下标. 
value:新值. 

#### 抛出
ArrayIndexOutOfBoundsException:如果index不是数组中的有效下标. 
ArrayStoreException:如果value的类不是数组元素类的子类. 

### 4.12.5 `New<PrimitiveType>Array`例程

```
ArrayType New<PrimitiveType>Array(JNIEnv *env, jsize length); 
```

用于构造新基本类型数组对象的一系列操作. 下表说明了特定的基本类型数组构造函数. 用户应把`New<PrimitiveType>Array`替换为某个实际的基本类型数组构造函数例程名（见下表）, 然后将ArrayType替换为该例程相应的数组类型. 
	
| **`New<PrimitiveType>Array`例程** | **数组类型** |
| ------------------------------- | ------------ |
| NewBooleanArray() | jbooleanArray |
| NewByteArray() | jbyteArray |
| NewCharArray() | jcharArray |
| NewShortArray() | jshortArray |
| NewIntArray() | jintArray |
| NewLongArray() | jlongArray |
| NewFloatArray() | jfloatArray |
| NewDoubleArray() | jdoubleArray |

#### 关联
JNIEnv接口函数表的项

| **`New<PrimitiveType>Array`例程** | **项** |
| ------------------------------- | ------ |
| NewBooleanArray() | 175 |
| NewByteArray() | 176 |
| NewCharArray() | 177 |
| NewShortArray() | 178 |
| NewIntArray() | 179 |
| NewLongArray() | 180 |
| NewFloatArray() | 181 |
| NewDoubleArray() | 182 |

#### 参数
env:JNI接口指针. 
length:数组长度. 

#### 返回值
Java数组. 如果无法构造该数组, 则为NULL. 

### 4.12.6 `Get<PrimitiveType>ArrayElements`例程

```
NativeType *Get<PrimitiveType>ArrayElements(JNIEnv *env, ArrayType array, jboolean *isCopy); 
```

一组返回基本类型数组体的函数. 结果在调用相应的`Release<PrimitiveType>ArrayElements`函数前将一直有效. 由于返回的数组
可能是Java数组的副本, 因此对返回数组的更改不必在基本类型数组中反映出来, 直到调用了`Release<PrimitiveType>ArrayElements`.

如果isCopy不是NULL, *isCopy在复制完成后即被设为JNI_TRUE. 如果未复制, 则设为JNI_FALSE. 

下表说明了特定的基本类型数组元素访问器. 应进行下列替换; 

* 将`Get<PrimitiveType>ArrayElements`替换为表中某个实际的基本类型元素访问器例程名. 
* 将ArrayType替换为对应的数组类型. 
* 将NativeType替换为该例程对应的本地类型. 

不管布尔数组在Java虚拟机中如何表示, `GetBooleanArrayElements`将始终返回一个jboolean类型的指针, 其中每一字节代表一个元素（开包表示）. 所有其它类型的数组都确保在内存中是连续的. 

| **`Get<PrimitiveType>ArrayElements`例程** | **数组类型** | **本地类型** |
| --------------------------------------- | ------------ | ------------ |
| GetBooleanArrayElements() | jbooleanArray | jboolean |
| GetByteArrayElements() | jbyteArray | jbyte |
| GetCharArrayElements() | jcharArray | jchar |
| GetShortArrayElements() | jshortArray | jshort |
| GetIntArrayElements() | jintArray | jint |
| GetLongArrayElements() | jlongArray | jlong |
| GetFloatArrayElements() | jfloatArray | jfloat |
| GetDoubleArrayElements() | jdoubleArray | jdouble |

#### 关联
JNIEnv接口函数表的项

| **`Get<PrimitiveType>ArrayElements`例程** | **数组类型** |
| --------------------------------------- | ------------ |
| GetBooleanArrayElements() | 183 |
| GetByteArrayElements() | 184 |
| GetCharArrayElements() | 185 |
| GetShortArrayElements() | 186 |
| GetIntArrayElements() | 187 |
| GetLongArrayElements() | 188 |
| GetFloatArrayElements() | 189 |
| GetDoubleArrayElements() | 190 |

#### 参数
env:JNI接口指针. 
array:Java字符串对象. 
isCopy:指向布尔值的指针. 

#### 返回值
返回指向数组元素的指针, 如果操作失败, 则为NULL. 
	
### 4.12.7 `Release<PrimitiveType>ArrayElements`例程

```
void Release<PrimitiveType>ArrayElements(JNIEnv *env, ArrayType array, NativeType *elems, jint mode); 
```

通知虚拟机本地代码无需再访问elems的一组函数. elems参数是一个通过使用对应的`Get<PrimitiveType>ArrayElements`函数由array导出的指针. 必要时, 该函数将把对elems的修改复制回基本类型数组. 

mode参数将提供有关如何释放数组缓冲区的信息. 如果elems不是array中数组元素的副本, mode将无效. 否则, mode将具有下表所述的功能:

| **mode** | **action** |
| -------- | ---------- |
| 0 | 复制回内容并释放elems缓冲区 |
| JNI_COMMIT | 复制回内容但不释放elems缓冲区 |
| JNI_ABORT | 释放缓冲区但不复制回变化 |

多数情况下, 程序员将把“0”传给mode参数以确保固定的数组和复制的数组保持一致. 其它选项可以使程序员进一步控制内存管理, 但使用时务必慎重. 

下表说明了构成基本类型数组撤消程序系列的特定例程. 应进行如下替换:
+ 将`Release<PrimitiveType>ArrayElements`替换为下表中的某个实际基本类型数组撤消程序例程名. 
+ 将ArrayType替换为对应的数组类型. 
+ 将NativeType替换为该例程对应的本地类型. 
	
| **`Release<PrimitiveType>ArrayElements`例程** | **数组类型** | **本地类型** |
| ------------------------------------------- | ------------ | ------------ |
| ReleaseBooleanArrayElements() | jbooleanArray | jboolean |
| ReleaseByteArrayElements() | jbyteArray | jbyte |
| ReleaseCharArrayElements() | jcharArray | jchar |
| ReleaseShortArrayElements() | jshortArray | jshort |
| ReleaseIntArrayElements() | jintArray | jint |
| ReleaseLongArrayElements() | jlongArray | jlong |
| ReleaseFloatArrayElements() | jfloatArray | jfloat |
| ReleaseDoubleArrayElements() | jdoubleArray | jdouble |

#### 关联
JNIEnv接口函数表的项

| **`Release<PrimitiveType>ArrayElements`例程** | **项** |
| ------------------------------------------- | ------ |
| ReleaseBooleanArrayElements() | 191 |
| ReleaseByteArrayElements() | 192 |
| ReleaseCharArrayElements() | 193 |
| ReleaseShortArrayElements() | 194 |
| ReleaseIntArrayElements() | 195 |
| ReleaseLongArrayElements() | 196 |
| ReleaseFloatArrayElements() | 197 |
| ReleaseDoubleArrayElements() | 198 |

#### 参数
env:JNI接口指针. 
array:Java数组对象. 
elems:指向数组元素的指针. 
mode:释放模式. 
	
### 4.12.8 `Get<PrimitiveType>ArrayRegion`例程

```
void Get<PrimitiveType>ArrayRegion(JNIEnv *env, ArrayType array, jsize start, jsize len, NativeType *buf); 
```
 
将基本类型数组某一区域复制到缓冲区中的一组函数. 

下表说明了特定的基本类型数组元素访问器. 应进行如下替换:
+ 将`Get<PrimitiveType>ArrayRegion`替换为表中的某个实际基本类型元素访问器例程名. 
+ 将ArrayType替换为对应的数组类型. 
+ 将NativeType替换为该例程对应的本地类型. 
	
| **`Get<PrimitiveType>ArrayRegion`例程** | **数组类型** | **本地类型** |
| ------------------------------------- | ------------ | ------------ |
| GetBooleanArrayRegion() | jbooleanArray | jboolean |
| GetByteArrayRegion() | jbyteArray | jbyte |
| GetCharArrayRegion() | jcharArray | jchar |
| GetShortArrayRegion() | jshortArray | jshort |
| GetIntArrayRegion() | jintArray | jint |
| GetLongArrayRegion() | jlongArray | jlong |
| GetFloatArrayRegion() | jfloatArray | jfloat |
| GetDoubleArrayRegion() | jdoubleArray | jdouble |	

#### 关联
JNIEnv接口函数表的项

| **`Get<PrimitiveType>ArrayRegion`例程** | **项** |
| ------------------------------------- | ------ |
| GetBooleanArrayRegion() | 199 |
| GetByteArrayRegion() | 200 |
| GetCharArrayRegion() | 201 |
| GetShortArrayRegion() | 202 |
| GetIntArrayRegion() | 203 |
| GetLongArrayRegion() | 204 |
| GetFloatArrayRegion() | 205 |
| GetDoubleArrayRegion() | 206 |	

#### 参数
env:JNI接口指针. 
array:Java指针. 
start:起始下标. 
len:要复制的元素数. 
buf:目的缓冲区. 

#### 抛出
ArrayIndexOutOfBoundsException:如果区域中的某个下标无效. 

### 4.12.9 `Set<PrimitiveType>ArrayRegion`例程

```
void Set<PrimitiveType>ArrayRegion(JNIEnv *env, ArrayType array, jsize start, jsize len, NativeType *buf); 
```

将基本类型数组的某一区域从缓冲区中复制回来的一组函数.

下表说明了特定的基本类型数组元素访问器. 应进行如下替换:
* 将`Set<PrimitiveType>ArrayRegion`替换为表中的实际基本类型元素访问器例程名. 
* 将ArrayType替换为对应的数组类型. 
* 将NativeType替换为该例程对应的本地类型. 	
	
| **`Set<PrimitiveType>ArrayRegion`例程** | **数组类型** | **本地类型** |
| ------------------------------------- | ------------ | ------------ |
| SetBooleanArrayRegion() | jbooleanArray | jboolean |
| SetByteArrayRegion() | jbyteArray | jbyte |
| SetCharArrayRegion() | jcharArray | jchar |
| SetShortArrayRegion() | jshortArray | jshort |
| SetIntArrayRegion() | jintArray | jint |
| SetLongArrayRegion() | jlongArray | jlong |
| SetFloatArrayRegion() | jfloatArray | jfloat |
| SetDoubleArrayRegion() | jdoubleArray | jdouble |		
	
#### 关联
JNIEnv接口函数表的项

| **`Set<PrimitiveType>ArrayRegion`例程** | **项** |
| ------------------------------------- | ------ |
| SetBooleanArrayRegion() | 207 |
| SetByteArrayRegion() | 208 |
| SetCharArrayRegion() | 209 |
| SetShortArrayRegion() | 210 |
| SetIntArrayRegion() | 211 |
| SetLongArrayRegion() | 212 |
| SetFloatArrayRegion() | 213 |
| SetDoubleArrayRegion() | 214 |	

#### 参数
env:JNI接口指针. 
array: Java数组. 
start:起始下标. 
len:要复制的元素数. 
buf:源缓冲区. 

#### 抛出
ArrayIndexOutOfBoundsException:如果区域中的某个下标无效. 	

**注意**: 在JDR/JRE 1.1中, 程序员能够使用`Get/Release<primitivetype>ArrayElements`函数来获取原始数组元素. 如果VM支持pinning, 则返回指向原始数据的指针;  否则, 返回一个副本.

在JDR/JRE 1.3中引入了新的函数, 允许本地方法在虚拟机不支持pinning情况下法获取数组元素.

### 4.12.10 GetPrimitiveArrayCritical, ReleasePrimitiveArrayCritical

```
void * GetPrimitiveArrayCritical(JNIEnv *env, jarray array, jboolean *isCopy); 
void ReleasePrimitiveArrayCritical(JNIEnv *env, jarray array, void *carray, jint mode); 
```

这两个函数的语义和`Get/Release<primitivetype>ArrayElements`函数非常相近. 必要时, 虚拟机会返回原始数组的指针;  否则返回一个副本. **然而, 这些函数的使用仍然有显著的限制**.

在调用`GetPrimitiveArrayCritical`以后, 本地代码在调用`ReleasePrimitiveArrayCritical`之前不能运行一段较长的时间. 我们必须把这一对函数之间的代码当做运行在"临界区"之间. 在临界区内, 本地代码不能调用其他JNI函数, 或者任何可能导致当前线程阻塞并等待其他Java线程的系统调用. (例如, 当前线程不能读正在被其他Java线程写的流).

这些限制很可能使得本地代码在虚拟机即使不支持pinning的情况下获取数组的非拷贝版. 例如, 当本地代码正持有通过`GetPrimitiveArrayCritical`获得的数组指针时, 虚拟机可能暂时取消垃圾回收.

多对`GetPrimtiveArrayCritical`和`ReleasePrimitiveArrayCritical`可能会嵌套. 例如:

```
  jint len = (*env)->GetArrayLength(env, arr1); 
  jbyte *a1 = (*env)->GetPrimitiveArrayCritical(env, arr1, 0); 
  jbyte *a2 = (*env)->GetPrimitiveArrayCritical(env, arr2, 0); 
  /* We need to check in case the VM tried to make a copy. */
  if (a1 == NULL || a2 == NULL) {
    ... /* out of memory exception thrown */
  }
  memcpy(a1, a2, len); 
  (*env)->ReleasePrimitiveArrayCritical(env, arr2, a2, 0); 
  (*env)->ReleasePrimitiveArrayCritical(env, arr1, a1, 0); 

```

注意如果虚拟机内部以不同的格式表示数组, GetPrimitiveArrayCritical可能仍会产生数组副本. 因此我们需要把它的返回值和NULL比较, 以避免内存溢出可能.

#### 关联
GetPrimitiveArrayCritical的JNIEnv接口函数表的项222
ReleasePrimitiveArrayCritical的JNIEnv接口函数表的项223

#### SINCE
JDK/JRE 1.2

## 4.13 注册本地方法

### 4.13.1 RegisterNatives

```
jint RegisterNatives(JNIEnv *env, jclass clazz, const JNINativeMethod *methods, jint nMethods); 
```

向clazz参数指定的类注册本地方法. methods参数将指定JNINativeMethod结构的数组, 其中包含本地方法的名称、签名和函数指针. JNINativeMethod结构的名称和签名域是改进型UTF-8字符串指针. nMethods参数将指定数组中的本地方法数量. JNINativeMethod 结构定义如下所示:

```
typedef struct {
	char *name; 
	char *signature; 
	void *fnPtr; 
} JNINativeMethod; 
```

函数指针通常必须有下列签名:

```
ReturnType (*fnPtr)(JNIEnv *env, jobject objectOrClass, ...); 
```

#### 关联
JNIEnv接口函数表的项215

#### 参数
env:JNI接口指针. 
clazz:Java类对象. 
methods:类中的本地方法. 
nMethods:类中的本地方法数量. 

#### 返回值
成功时返回JNI_OK;  失败时返回负数. 
	
#### 抛出
NoSuchMethodError:如果找不到指定的方法或方法不是本地方法. 

### 4.13.2 UnregisterNatives

```
jint UnregisterNatives(JNIEnv *env, jclass clazz); 
```

取消注册类的本地方法. 类将返回到链接或注册了本地方法函数前的状态.

该函数不应在常规本地代码中使用. 相反, 它是为特殊程序提供一种重新加载和重新链接本地库的途径.

#### 关联
JNIEnv接口函数表的项216

#### 参数
env:JNI接口指针. 
clazz:Java类对象. 

#### 返回值
成功时返回JNI_OK; 失败时返回负数. 	

## 4.14 监视操作

### 4.14.1 MonitorEnter

```
jint MonitorEnter(JNIEnv *env, jobject obj); 
```

进入与obj所引用的基本Java对象相关联的监视器. obj引用不能为NULL.

每个Java对象都有一个相关联的监视器. 如果当前线程已经拥有与obj相
关联的监视器, 它将使指示该线程进入监视器次数的监视器计数器增 1. 如果与 obj 相关联的监视器并非由某个线程所拥有, 则当前线程将变为该监视器的所有者, 同时将该监视器的计数器设置为 1. 如果另一个线程已拥有与 obj 关联的监视器, 则在监视器被释放前当前线程将处于等待状态. 监视器被释放后, 当前线程将尝试重新获得所有权. 

通过调用JNI函数MonitorEnter进入的监视器不会因Java虚拟机指令monitorexit或同步方法返回退出的. JNI函数MonitorEnter调用和Java虚拟机指monitorexit可能相互竞争进入同一对象相关联的监视器．

为了避免死锁, 通过调用JNI函数MonitorEnter进入的监视器必须通过调用JNI函数MonitorExit来退出, 除非DetachCurrentThread调用隐式地释放了JNI监视器.

#### 关联
JNIEnv接口函数表的项217

#### 参数
env:JNI接口指针. 
obj:常规Java对象或类对象. 
	
#### 返回值
成功时返回JNI_OK; 失败时返回负数. 

### 4.15.2 MonitorExit

```
jint MonitorExit(JNIEnv *env, jobject obj); 
```

当前线程必须是与obj所引用的基本Java对象相关联的监视器的所有者. 线程将使指示进入监视器次数的计数器减1. 如果计数器的值变为0, 当前线程释放监视器. 

本地代码不能使用MonitorExit来退出一个使用同步方法或Java虚拟机指monitorexit进入的监视器.

#### 关联
JNIEnv接口函数表的项218

#### 参数
env:JNI接口指针. 
obj:常规Java对象或类对象. 
	
#### 返回值
成功时返回JNI_OK; 失败时返回负数. 

#### 抛出
IllegalMonitorStateException: 如果当前线程没有持有监视器.

## 4.16 NIO支持
NIO相关项允许本地代码访问java.nio的直接缓冲区(direct buffer). 直缓冲区的内容可能会在普通垃圾收集堆外的本地内存中驻留. 关于直接缓冲区信息, 请参阅New I/O APIs和java.nio.bytebuffer类的规范.

在JDK/JRE 1.4中引入的三种新函数允许JNI代码创建, 检查和操纵直缓冲区:

* NewDirectByteBuffer
* GetDirectBufferAddress
* GetDirectBufferCapacity

Java虚拟机的每个实现都必须支持这些函数, 但是不是每个实现都要求支持JNI访问直缓冲区. 如果虚拟机不支持这样的访问, 那么NewDirectByteBuffer和GetDirectBufferAddress必须总是返回NULL, 同时GetDirectBufferCapacity必须总是返回-1. 如果虚拟机不支持这样的访问, 那么这三个函数必须实现能够返回合适的值.

### 4.16.1 NewDirectByteBuffer

```
jobject NewDirectByteBuffer(JNIEnv* env, void* address, jlong capacity);
```

分配java.nio.ByteBuffer对象并返回其引用, 指向一块内存, 其内存地址为address, 并延伸capacity个字节.

调用该函数并返回Java层字节缓冲区对象的本地代码必须确保缓冲区指向的是一块有效的内存区域, 即可读, 必要时也可写. Java代码尝试访问无效内存位置会返回一个任意值, 没有可见的效果, 或者导致未指定的异常.

#### 关联
JNIEnv接口函数表的项229

#### 参数
env: JNI接口指针. 
address: 内存区域的起始地址(必须非NULL). 
capacity: 内存区域的字节长度(必须正数).
	
#### 返回值
返回一个局部引用, 指向新实例化的java.nio.ByteBuffer对象. 如果产生异常或JNI访问直缓冲区不被当前虚拟机支持, 则返回NULL.

#### 抛出
OutOfMemoryError: 如果ByteBuffer对象分配失败.

#### SINCE
JDK/JRE 1.4

### 4.16.2 GetDirectBufferAddress

```
void* GetDirectBufferAddress(JNIEnv* env, jobject buf);
```

读取并返回给定直缓冲区对象的内存区域的起始地址.

该函数允许本地代码访问与Java代码通过缓冲区对象所访问的同一内存区域.

#### 关联
JNIEnv接口函数表的项230

#### 参数
env: JNI接口指针. 
buf: 直缓冲区对象(必须非NULL).
	
#### 返回值
返回缓冲区的内存区域的起始地址. 如果内存区域是未定义的, 给定对象不是有效直缓冲区对象, 或者JNI访问直缓冲区不被该虚拟机所支持, 则返回NULL.

#### SINCE
JDK/JRE 1.4

### 4.16.3 GetDirectBufferCapacity

```
jlong GetDirectBufferCapacity(JNIEnv* env, jobject buf);
```

读取并返回给定直缓冲区对象的内存区域的容量. 该容量是内存区域所包含元素的数量.

#### 关联
JNIEnv接口函数表的项231

#### 参数
env: JNI接口指针. 
buf: 直缓冲区对象(必须非NULL).
	
#### 返回值
返回缓冲区的内存区域的容量. 如果给定对象不是有效直缓冲区对象, 对象是未对齐的视图缓冲区, 处理器架构不支持未对齐访问, 或者JNI访问直缓冲区不被该虚拟机所支持, 则返回-1.

#### SINCE
JDK/JRE 1.4

## 4.17 反射支持
如果程序员知道名字的方法或域类型, 那么他们可以使用JNI调用Java方法或访问Java域. Java核心反射API允许程序员运行时内省Java类. JNI提供了一组用于JNI中的域和方法ID以及Java核心反射API中的域对象和方法对象之间的
转换函数.

### 4.17.1 FromReflectedMethod

```
jmethodID FromReflectedMethod(JNIEnv *env, jobject method);
```

把java.lang.reflect.Method或java.lang.reflect.Constructor对象转换成方法ID.

#### 关联
JNIEnv接口函数表的项7

#### SINCE
JDK/JRE 1.2

### 4.17.2 FromReflectedField

```
jfieldID FromReflectedField(JNIEnv *env, jobject field);
```

把java.lang.reflect.Field对象转换成域ID.

#### 关联
JNIEnv接口函数表的项8

#### SINCE
JDK/JRE 1.2

### 4.17.3 ToReflectedMethod

```
jobject ToReflectedMethod(JNIEnv *env, jclass cls, jmethodID methodID, jboolean isStatic);
```

把派生自cls的方法ID转换成java.lang.reflect.Method或java.lang.reflect.Constructor对象. 如果方法ID指向静态方法, 则isStatic必须设置成JNI_TRUE, 否则设置成JNI_FALSE.

如果失败, 则抛出OutOfMemoryError并返回0.

#### 关联
JNIEnv接口函数表的项9

#### SINCE
JDK/JRE 1.2

### 4.17.4 ToReflectedField

```
jobject ToReflectedField(JNIEnv *env, jclass cls, jfieldID fieldID, jboolean isStatic);
```

把派生自cls的域ID转换成java.lang.reflect.Field对象. 如果域ID指向静态域, 则isStatic必须设置成JNI_TRUE, 否则设置成JNI_FALSE.

如果失败, 则抛出OutOfMemoryError并返回0.

#### 关联
JNIEnv接口函数表的项12

#### SINCE
JDK/JRE 1.2

## 4.18 Java虚拟机接口

### 4.18.1 GetJavaVM

```
jint GetJavaVM(JNIEnv *env, JavaVM **vm); 
```

返回与当前线程相关联的Java虚拟机接口（用于调用API中）. 结果将放在第二个参数vm所指向的位置. 

#### 关联
JNIEnv接口函数表的项219

#### 参数
env:JNI接口指针. 
vm:指向放置结果的位置的指针. 

#### 返回值
成功时返回JNI_OK; 失败时返回负数. 

# 5 调用API	

调用API允许软件厂商将Java虚拟机加载到任意的本地程序中. 厂商可以交付支持Java的应用程序, 而不必链接Java虚拟机源代码. 

本章首先概述了调用API. 然后是所有调用API函数的引用页. 它涉及到下列内容:

* Overview
  * Creating the VM
  * Attaching to the VM
  * Detaching from the VM
  * Unloading the VM
* Library and Version Management
  * JNI_OnLoad
  * JNI_OnUnload
* Invocation API Functions
  * JNI_GetDefaultJavaVMInitArgs
  * JNI_GetCreatedJavaVMs
  * JNI_CreateJavaVM
  * DestroyJavaVM
  * AttachCurrentThread
  * AttachCurrentThreadAsDaemon
  * DetachCurrentThread
  * GetEnv

## 5.1 概述

以下代码示例说明了如何使用调用API中的函数. 在本例中, C++代码创建Java虚拟机并且调用名为Main.test的静态方法. 为清楚起见, 我们略去了错误检查. 

```
	#include <jni.h>	/* 其中定义了所有的事项 */
	...
	JavaVM *jvm; 	/* 表示 Java 虚拟机*/
	JNIEnv *env; 	/* 指向本地方法接口的指针 */
	JavaVMInitArgs vm_args;  /* JDK/JRE 6 虚拟机初始化参数 */
	JavaVMOption* options = new JavaVMOption[1];
	options[0].optionString = "-Djava.class.path=/usr/lib/java";
	vm_args.version = JNI_VERSION_1_6;
	vm_args.nOptions = 1;
	vm_args.options = options;
	vm_args.ignoreUnrecognized = false;
	/* 加载并初始化Java虚拟机, 返回env中的JNI 接口指针 */
	JNI_CreateJavaVM(&jvm, (void**)&env, &vm_args); 
	delete options;
	/* 用JNI调用 Main.test 方法 */
	jclass cls = env->FindClass("Main"); 
	jmethodID mid = env->GetStaticMethodID(cls, "test", "(I)V"); 
	env->CallStaticVoidMethod(cls, mid, 100); 
	/* 结束. */
	jvm->DestroyJavaVM(); 
```

本例使用了API中的三个函数. 调用API允许本地应用程序用JNI接口指针来访问虚拟机特性. 其设计类似于Netscape的JRI嵌入式接口. 

### 5.1.1 创建虚拟机
JNI_CreateJavaVM()函数加载并初始化Java虚拟机, 然后将指针返回到JNI接口指针. 调用JNI_CreateJavaVM()的线程被看作主线程. 

### 5.1.2 连接虚拟机
JNI接口指针(JNIEnv)仅在当前线程中有效. 如果另一个线程需要访问Java虚拟机, 则该线程首先必须调用AttachCurrentThread()以将自身连接到虚拟机并且获得JNI接口指针. 连接到虚拟机之后, 本地线程的工作方式就与在本地方法内运行的普通Java线程一样了. 本地线程保持与虚拟机的连接, 直到调用DetachCurrentThread()时才断开连接. 

已连接的线程必须有足够的栈空间来执行一些工作. 每个线程栈空间的分配是操作系统决定的. 例如, 使用pthreads, 栈大小可以在pthread_create中通过pthread_attr_t参数指定.

### 5.1.3 断开虚拟机
一个已连接虚拟机的本地线程必须调用DetachCurrentThread()在退出前与虚拟机分离. 如果调用栈中有Java方法, 一个线程是不能分离自身的.

### 5.1.4 卸载虚拟机
JNI_DestroyJavaVM()函数卸载Java虚拟机.

虚拟机等到主线程成为唯一的非守护的用户线程时才真正地卸载. 用户线程包括Java线程和已连接的本地线程. 之所以存在这种限制是因为Java线程或已连接的本地线程可能正占用着系统资源, 例如锁, 窗口等. 虚拟机不能自动释放这些资源. 卸载虚拟机时, 通过将主线程限制为唯一的运行线程, 使释放任意线程所占用系统资源的负担落到程序员身上.

## 5.2 库和版本管理
一旦本地库被加载，它对所有类装载器可见。因此，在不同的类装载器上的2个类可以与同一个本地方法连接。这导致了两个问题: 

* 一个类会错误地与不同类加载器里一个具有相同名称的类所加载的本地库链接.
* 本地方法会很容易地混合来自不同类加载器的类. 这会打破类加载器提供的命名空间隔离, 并且导致类型安全问题.

每个类加载器管理其自身一组本地库. **相同的JNI本地库不可能被加载到一个以上的类加载器中**. 如果这样做, 会导致UnsatisfiedLinkError抛出. 例如, 当System.loadLibrary加载一个本地库到两个类加载器中, 会抛出UnsatisfiedLinkError. 这种方式的好处是:

* 基于类加载器的命名空间隔离可以在本地库中保存. 一个本地库不可能容易地混合来自不同类加载器的类.
* 此外, 当相应的类加载器被垃圾回收时, 本地库可以被卸载.

为了方便地版本控制和资源管理，JNI库可选地导出以下两个函数：

### 5.2.1 JNI_OnLoad

```
jint JNI_OnLoad(JavaVM *vm, void *reserved);
```

当本地库被加载(如利用System.loadLibrary时, 虚拟机会调用JNI_OnLoad. JNI_OnLoad必须返回本地库所需的JNI版本.

为了能使用最新JNI函数, 本地库必须导出返回JNI_VERSION_1_2的JNI_OnLoad函数. 如果本地库没有导出JNI_OnLoad函数, 虚拟机会假定本地库仅仅需要JNI版本JNI_VERSION_1_1. 如果虚拟机不能识别JNI_OnLoad返回的版本数字, 虚拟机会卸载库并视作库从来没有被加载.

```
JNI_OnLoad_L(JavaVM *vm, void *reserved);
```

如果库L是静态链接的, 那么第一次执行System.loadLibrary("L")或类似API时, JNI_OnLoad_L函数会被执行, 并且参数和返回值都和JNI_OnLoad类似. JNI_OnLoad_L必须返回本地库所需的JNI版本. 这个版本必须是JNI_VERSION_1_8及以后. 如果虚拟机不能识别JNI_OnLoad_L返回的版本值, 则虚拟机会视作该库从来没有被加载.

#### 关联
包含本地方法实现的本地库所导出. 

### 5.2.2 JNI_OnUnload

```
void JNI_OnUnload(JavaVM *vm, void *reserved);
```

当拥有本地库的类加载器被垃圾回收时, 虚拟机将调用JNI_OnUnload函数. 该函数用于执行清理操作. 因为这个函数是在未知上下文环境(例如析构器)中调用的, 因此程序员应该保守使用Java虚拟机服务, 同时克制任意的Java回调.

注意: JNI_OnLoad和JNI_OnUnload是两个JNI库可选提供的函数, 而不是由虚拟机导出的.

```
JNI_OnUnload_L(JavaVM *vm, void *reserved);
```

当拥有静态链接本地库L的类加载器进行垃圾回收时, 如果库的JNI_OnUnload_L函数存在, 则虚拟机会执行该函数. 该函数用于执行清理操作.因为这个函数是在未知上下文环境(例如析构器)中调用的, 因此程序员应该保守使用Java虚拟机服务, 同时克制任意的Java回调.

注意: 加载本地库的行为是使得库和它的本地入口点被Java虚拟机和运行时所知并注册的完整过程. 而简单地执行操作系统层面的操作来加载本地库, 如在UNIX(R)系统中的dlopen, 是难以完全达到这个目标的. Java类加载器调用本地方法会执行一个主机操作系统的调用, 使得库加载到内存并返回本地库的句柄. 一个这个句柄会被存储并被用在随后本地库入口点的搜索中. 一旦库成功注册并返回句柄, 则Java本地类加载器将完成加载过程.

#### 关联
包含本地方法实现的本地库所导出.

### 5.3 调用API函数

JavaVM类型是指向调用API函数表的指针. 以下代码示例显示了这种函数表. 

```
typedef const struct JNIInvokeInterface *JavaVM; 

const struct JNIInvokeInterface ... = {
	NULL,
	NULL,
	NULL,
	DestroyJavaVM,
	AttachCurrentThread,
	DetachCurrentThread,
}; 
```

注意: JNI_GetDefaultJavaVMInitArgs()、JNI_GetCreatedJavaVMs()和JNI_CreateJavaVM()这三个调用API函数不是JavaVM函数表
的一部分. 不必先有JavaVM结构, 就可以使用这些函数. 

### 5.3.1 JNI_GetDefaultJavaVMInitArgs

```
jint JNI_GetDefaultJavaVMInitArgs(void *vm_args); 
```

返回Java虚拟机的缺省配置. 在调用该函数之前, 本地代码必须将vm_args->version域设置为它所期望虚拟机支持的JNI版本. 该函数返回后, 
vm_args->version会被设置为虚拟机支持的实际JNI版本. 

#### 关联
包含本地方法实现的本地库所导出.

#### 参数
vm_args: 指向JavaVMInitArgs结构的指针, 缺省参数填入该结构. 

#### 返回值
如果所请求的版本得到支持, 则返回0; 如果所请求的版本未得到支持, 则返回JNI错误码(负数). 
	
### 5.3.2 JNI_GetCreatedJavaVMs

```
jint JNI_GetCreatedJavaVMs(JavaVM **vmBuf, jsize bufLen, jsize *nVMs); 
```

返回所有已创建的Java虚拟机. 将指向虚拟机的指针依据其创建顺序写入vmBuf缓冲区. 最多写入bufLen项. 在*nVMs中返回所创建虚拟机的总数.

不支持在单个进程中创建多个虚拟机. 

#### 关联
包含本地方法实现的本地库所导出.

#### 参数
vmBuf:指向将放置虚拟机结构的缓冲区的指针. 
bufLen:缓冲区的长度. 
nVMs:指向整数的指针. 

#### 返回值
成功时返回JNI_OK; 失败则返回合适的JNI错误码(负数). 

### 5.3.3 JNI_CreateJavaVM

```
jint JNI_CreateJavaVM(JavaVM **p_vm, JNIEnv **p_env, void *vm_args); 
```

加载并初始化Java虚拟机. 当前线程成为主线程. 将env参数设置为主线程的JNI接口指针. 

不支持在单个进程中创建多个虚拟机. 

JNI_CreateJavaVM的第二个参数总是一个指向JNIEnv *的指针, 而第三个参数是一个指向JavaVMInitArgs结构的指针, 该结构使用选项字符串来编码虚拟机启动选择:

```
typedef struct JavaVMInitArgs {
    jint version;

    jint nOptions;
    JavaVMOption *options;
    jboolean ignoreUnrecognized;
} JavaVMInitArgs;
```

option域是以下类型的数组:

```
typedef struct JavaVMOption {
    char *optionString;  /* 以默认平台编码的选项字符串 */
    void *extraInfo;
} JavaVMOption;
```

数组大小由JavaVMInitArgs中的nOptions域表示. 如果ignoreUnrecognized是JNI_TRUE, 则JNI_CreateJavaVM会忽略所有不能识别的以"-X"或"_"开始的选项字符串. 如果ignoreUnrecognized是JNI_FALSE, 则JNI_CreateJavaVM 一旦遇到不能识别的选项字符串则立即返回JNI_ERR. 所有Java虚拟机必须能够识别以下一组标准字符串:

| **optionString** | **含义** |
| ---------------- | -------- |
| -D<name>=<value> | 设置系统属性 |
| -verbose[:class|gc|jni] | 启用详细输出. 该选项可以后跟逗号分隔的名称列表, 以指示什么类型的消息会被虚拟机输出. 例如, "-verbose:gc,class"指示虚拟机输出GC和类加载相关消息. 标准名称包括:gc, class和jni. 所有非标准(VM特定)名称必须以"X"开始. |
| vfprintf | extraInfo是一个指向vfprintf钩子的指针. |
| exit | extraInfo是一个指向exit钩子的指针. |
| abort | extraInfo是一个指向abort钩子的指针. |

此外, 每个虚拟机实现可能会支持一组其自身的非标准选项字符串. 非标准选项名称必须以"-X"或者"_"开始. 例如, JDK/JRE支持-Xms和-Xmx选择来允许程序员指定初始和最大的堆对象. 以"-X"开始的选项是可以被"java"命令行访问的.

这里是JDK/JRE中创建Java虚拟机的示例代码:

```
JavaVMInitArgs vm_args;
JavaVMOption options[4];

options[0].optionString = "-Djava.compiler=NONE";           /* 取消JIT */
options[1].optionString = "-Djava.class.path=c:\myclasses"; /* 用户类 */
options[2].optionString = "-Djava.library.path=c:\mylibs";  /* 本地库路径 */
options[3].optionString = "-verbose:jni";                   /* 输出JNI相关消息 */

vm_args.version = JNI_VERSION_1_2;
vm_args.options = options;
vm_args.nOptions = 4;
vm_args.ignoreUnrecognized = TRUE;

/* 注意在JDK/JRE中, 这里不需要再调用JNI_GetDefaultJavaVMInitArgs. */
res = JNI_CreateJavaVM(&vm, (void **)&env, &vm_args);
if (res < 0) ...
```

#### 关联
包含本地方法实现的本地库所导出.

#### 参数
p_vm:指向虚拟机结构所在位置的指针. 
p_env:指向主线程的JNI接口指针所在位置的指针. 
vm_args: Java 虚拟机初始化参数. 

#### 返回值
成功时返回JNI_OK; 失败则返回合适的JNI错误码(负数). 

### 5.3.4 DestroyJavaVM

```
jint DestroyJavaVM(JavaVM *vm); 
```

卸载Java虚拟机并回收资源. 

任何线程, 无论其是否连接到虚拟机, 都可以执行该函数. 如果当前线程已连接, 则虚拟机会等待当前线程成为最终唯一一个非守护的用户级Java线程. 如果当前线程没有连接, 则虚拟机会连接当前线程, 然后等待当前线程成为最终唯一一个非守护的用户级Java线程.

#### 关联
JNIEnv接口函数表的项3

#### 参数
vm:将销毁的Java虚拟机. 

#### 返回值
成功时返回JNI_OK; 失败则返回合适的JNI错误码(负数). 

### 5.3.5 AttachCurrentThread

```
jint AttachCurrentThread(JavaVM *vm, JNIEnv **p_env, void *thr_args); 
```

将当前线程连接到Java虚拟机. 在JNIEnv参数中返回JNI接口指针.

试图连接已经连接的线程将不执行任何操作.

本地线程不能同时连接到两个Java虚拟机上. 

当线程连接到虚拟机上, 上下文类加载器则是bootstrap加载器.

#### 关联
JNIEnv接口函数表的项4

#### 参数
vm: 当前线程所要连接到的虚拟机. 
p_env: 指向当前线程的JNI接口指针所在位置的指针. 
thr_args: NULL或者指向包含附加信息的JavaVMAttachArgs结构的指针. 

AttachCurrentThread的第二个参数总是指向JNIEnv的指针. 第三个参数是预留的, 应该设置成NULL.

用户可以传递一个下列结构的指针来指定附加信息:

```
typedef struct JavaVMAttachArgs {
    jint version;
    char *name;    /* 以改进型UTF-8编码的线程名称, 或NULL */
    jobject group; /* ThreadGroup对象的全局引用, 或NULL */
} JavaVMAttachArgs
```

#### 返回值
成功时返回JNI_OK; 失败则返回合适的JNI错误码(负数). 

### 5.3.6 AttachCurrentThreadAsDaemon

```
jint AttachCurrentThreadAsDaemon(JavaVM* vm, void** p_env, void* args);
```

与AttachCurrentThread语义相同, 但是新建的java.lang.Thread实例是一个守护线程.

如果线程已经通过AttachCurrentThread或AttachCurrentThreadAsDaemon关联到虚拟机, 则这个例程仅仅把当前线程的JNIEnv设置成p_env指向的值. 这种情况下, AttachCurrentThread和这个例程都对线程的守护状态不起任何作用.

#### 连接
JNIEnv接口函数表的项7

#### 参数
vm: 当前线程所要连接到的虚拟机. 
p_env: 指向当前线程的JNI接口指针所在位置的指针. 
thr_args: 指向包含附加信息的JavaVMAttachArgs结构的指针.

#### 返回值
成功时返回JNI_OK; 失败则返回合适的JNI错误码(负数).

### 5.3.7 DetachCurrentThread

```
jint DetachCurrentThread(JavaVM *vm); 
```

断开当前线程与Java虚拟机之间的连接. 释放该线程占用的所有Java监视器. 通知所有等待该线程终止的Java线程. 

主线程是不能与虚拟机断开的.

#### 关联
JNIEnv接口函数表的项5

#### 参数
vm:当前线程将断开连接的虚拟机. 

#### 返回值
成功时返回JNI_OK; 失败则返回合适的JNI错误码(负数).

### 5.8 GetEnv

```
jint GetEnv(JavaVM *vm, void **env, jint version);
```

#### 关联
JNIEnv接口函数表的项6

#### 参数
vm: 取回JNI接口的虚拟机实例.
env: 指向当前线程的JNI接口指针所在位置的指针.
version: 期望的JNI版本.

#### 返回值
如果当前线程没有连接到虚拟机, 则设置`*env`为NULL, 同时返回`JNI_EDETACHED`. 如果指定的版本不支持, 则设置`*env`为NULL, 同时返回`JNI_EVERSION`. 否则, 设置`*env`为合适的接口, 同时返回`JNI_OK`.

# 参考
1. [NDK-JNI实战教程（二） JNI官方中文资料][1]
2. [JNI本地接口(JNI)规范][2]

[1]: http://yanbober.github.io/2015/02/16/android_studio_jni_2/
[2]: http://docs.oracle.com/javase/8/docs/technotes/guides/jni/spec/jniTOC.html

