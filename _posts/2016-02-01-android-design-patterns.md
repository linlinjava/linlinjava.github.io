---
layout: page
title: Android设计模式
category: 
description: Learning and Thinking in Android Design Patterns
---

# 0

参考 https://github.com/iluwatar/java-design-patterns


1. 解耦分离: 用户只需要了解产品API的含义, 而不需要了解具体类是如何实现的, 甚至用户都不知道当前API的实现类, 即可方便获得服务.
2. 更为重要的是在用户的代码中, 很少或者根本不会出现具体类的名称, 因此解耦了用户和实际类, 这会带来极大的灵活性.

* 用户项目可以切换到不同的环境下, 只需要简单的设置成新的具体类.
* 框架类的版本更替也会带来较小的影响, 无论是在原有子类


# 1. Abstract Factory

抽象工厂模式

## 1.1 模型

## 1.2 代码

### 1.2.1 API设计

```
package package.api;

public interface ProductA
{
}

public interface ProductB
{

}

public interface Factory
{
  public ProductA createProductA ();
  public ProductB createProductB ();
}
```

### 1.2.2 API实现

#### 实现包1

```
package package.impl1;

public class ConcreteProductA1 extends ProductA
{

}

public class ConcreteProductB1 extends ProductB
{
}

public class ConcreteFactory1 implements Factory
{
  public ProductA createProductA ()
  {
    return new ConcreteProductA1 ();
  }

  public ProducetB createProductB ()
  {
    return new ConcreteProducetB1 ();
  }
}
```

#### 实现包2

```
package package.impl1;

public class ConcreteProductA2 extends ProductA
{

}

public class ConcreteProductB2 extends ProductB
{
}

public class ConcreteFactory2 implements Factory
{
  public ProductA createProductA ()
  {
    return new ConcreteProductA2 ();
  }

  public ProducetB createProductB ()
  {
    return new ConcreteProducetB2 ();
  }
}


在Factory子类的`createXXX`方法中:

* 可能每次调用都会创建一个新对象;
* 也有可能采用单例模式, 每次调用都仅仅返回单例对象.

### 1.2.3 API使用

```
package package.main;

public class App
{

  public static void main(String args[])
  {
    Factory factory = getFactory ();
`   process (factory);
  }

  private Factory getFactory ()
  {
    Factory factory = null;
    ...
    return factory;
  }

  private void process (Factory factory)
  {
    ProductA factoryA = factory.createProductA ();
    ProductB factoryB = factory.createProductB ();
    ...
  }
}
```

在`getFactory`方法中, 用户可以采取:

1. 直接使用所需的Factory子类来实例化Factory对象;
2. 也可以采用传递一个类名称String对象, 基于反射机制实例化Factory对象;
3. 更可以进一步在2基础上, 扩展成多种途径, 如在一个项目清单文件(XML格式,JSON格式等)中命名当前项目所使用的Factory类名称.

## 1.4 评价

优点:

1. 
2. 

## 1.5 应用

# 2. Factory Method

工厂方法模型

## 2.1 模型

## 2.2 代码

### 2.2.1 API

```
public interface Product
{
}

public class Creater
{
  public Product CreateProduct
```

### 2.2.2 API实现

### 2.2.3 API使用

## 2.3 评价

## 2.4 应用

# 1. 名称

## 1.1 模型

## 1.2 代码

## 1.3 评价

## 1.4 应用

