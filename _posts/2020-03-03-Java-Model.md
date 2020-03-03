---
title: Java设计模式
date:  2020-03-03 03:49:33 +0800
category: Reference
tags: Java
excerpt: 对Java常用设计模式的汇总，记录对Java设计模式学习过程
---

[TOC]



#### 策略模式

以商城为例，结算时调用不同的支付系统可以用策略模式。

在有多种算法相似的情况下，使用 if...else 所带来的复杂和难以维护。

1. 创建一个接口

   ```
   public interface Strategy {
      public int doOperation(int num1, int num2);
   }
   ```

2. 创建实现接口的实体类。

   ```
   public class OperationAdd implements Strategy{
      @Override
      public int doOperation(int num1, int num2) {
         return num1 + num2;
      }
   }
   ```

   ```
   public class OperationSubstract implements Strategy{
      @Override
      public int doOperation(int num1, int num2) {
         return num1 - num2;
      }
   }
   ```

   ```
   public class OperationMultiply implements Strategy{
      @Override
      public int doOperation(int num1, int num2) {
         return num1 * num2;
      }
   }
   ```

3. 创建 *Context* 类。

   ```
   public class Context {
      private Strategy strategy;
    
      public Context(Strategy strategy){
         this.strategy = strategy;
      }
    
      public int executeStrategy(int num1, int num2){
         return strategy.doOperation(num1, num2);
      }
   }
   ```

4. 使用 *Context* 来查看当它改变策略 *Strategy* 时的行为变化。

   ```
   public class StrategyPatternDemo {
      public static void main(String[] args) {
         Context context = new Context(new OperationAdd());    
         System.out.println("10 + 5 = " + context.executeStrategy(10, 5));
    
         context = new Context(new OperationSubstract());      
         System.out.println("10 - 5 = " + context.executeStrategy(10, 5));
    
         context = new Context(new OperationMultiply());    
         System.out.println("10 * 5 = " + context.executeStrategy(10, 5));
      }
   }
   ```

##### 输出

```
10 + 5 = 15
10 - 5 = 5
10 * 5 = 50
```



#### 单例模式

线程安全，利用懒加载方式创建一个 *SingleObject* 类

线程安全地调用工具类

1. 双重检测机制

   ```
   public class Singleton{
   	private Singleton(){}
   	private volatile static Singleton INSTANCE = null;
   	public static Singleton getInstance(){
   		if(INSTANCE == null){
   			synchronized(Singleton.class){
   				if(INSTANCE == null){
   					INSTANCE = new Singleton();
   				}
   			}
   		}
   		return INSTANCE;
   	}
   }
   ```

2. 登记式/静态内部类

   ```
   public class Singleton{
   	private Singleton(){}
   	private static class SingletonHolder{
   		static final Singleton INSTANCE = new Singleton();
   	}
   	publice static Singleton getInstance(){
   		return SingletonHolder.INSTANCE;
   	}
   }
   ```

#### 工厂模式

您需要一辆汽车，可以直接从工厂里面提货，而不用去管这辆汽车是怎么做出来的，以及这个汽车里面的具体实现。

1. 创建一个接口

   ```
   public interface Shape {
      void draw();
   }
   ```

2. 创建实现接口的实体类

   ```
   public class Rectangle implements Shape {
    
      @Override
      public void draw() {
         System.out.println("Inside Rectangle::draw() method.");
      }
   }
   ```

   ```
   public class Square implements Shape {
    
      @Override
      public void draw() {
         System.out.println("Inside Square::draw() method.");
      }
   }
   ```

   ```
   public class Circle implements Shape {
    
      @Override
      public void draw() {
         System.out.println("Inside Circle::draw() method.");
      }
   }
   ```

3. 创建一个工厂，生成基于给定信息的实体类的对象

   ```
   public class ShapeFactory {
       
      //使用 getShape 方法获取形状类型的对象
      public Shape getShape(String shapeType){
         if(shapeType == null){
            return null;
         }        
         if(shapeType.equalsIgnoreCase("CIRCLE")){
            return new Circle();
         } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
            return new Rectangle();
         } else if(shapeType.equalsIgnoreCase("SQUARE")){
            return new Square();
         }
         return null;
      }
   }
   ```

4. 使用该工厂，通过传递类型信息来获取实体类的对象

   ```
   public class FactoryPatternDemo {
    
      public static void main(String[] args) {
         ShapeFactory shapeFactory = new ShapeFactory();
    
         //获取 Circle 的对象，并调用它的 draw 方法
         Shape shape1 = shapeFactory.getShape("CIRCLE");
    
         //调用 Circle 的 draw 方法
         shape1.draw();
    
         //获取 Rectangle 的对象，并调用它的 draw 方法
         Shape shape2 = shapeFactory.getShape("RECTANGLE");
    
         //调用 Rectangle 的 draw 方法
         shape2.draw();
    
         //获取 Square 的对象，并调用它的 draw 方法
         Shape shape3 = shapeFactory.getShape("SQUARE");
    
         //调用 Square 的 draw 方法
         shape3.draw();
      }
   }
   ```

##### 输出

```
Inside Circle::draw() method.
Inside Rectangle::draw() method.
Inside Square::draw() method.
```



#### 装饰器模式

孙悟空有 72 变，当他变成"庙宇"后，他的根本还是一只猴子，但是他又有了庙宇的功能。

可以在不想增加很多子类的情况下扩展类。

1. 创建一个接口

   ```
   public interface Shape {
      void draw();
   }
   ```

2. 创建实现接口的实体类

   ```
   public class Rectangle implements Shape {
    
      @Override
      public void draw() {
         System.out.println("Shape: Rectangle");
      }
   }
   ```

   ```
   public class Circle implements Shape {
    
      @Override
      public void draw() {
         System.out.println("Shape: Circle");
      }
   }
   ```

3. 创建实现了 *Shape* 接口的抽象装饰类

   ```
   public abstract class ShapeDecorator implements Shape {
      protected Shape decoratedShape;
    
      public ShapeDecorator(Shape decoratedShape){
         this.decoratedShape = decoratedShape;
      }
    
      public void draw(){
         decoratedShape.draw();
      }  
   }
   ```

4. 创建扩展了 *ShapeDecorator* 类的实体装饰类

   ```
   public class RedShapeDecorator extends ShapeDecorator {
    
      public RedShapeDecorator(Shape decoratedShape) {
         super(decoratedShape);     
      }
    
      @Override
      public void draw() {
         decoratedShape.draw();         
         setRedBorder(decoratedShape);
      }
    
      private void setRedBorder(Shape decoratedShape){
         System.out.println("Border Color: Red");
      }
   }
   ```

5. 使用 *RedShapeDecorator* 来装饰 *Shape* 对象

   ```
   public class DecoratorPatternDemo {
      public static void main(String[] args) {
    
         Shape circle = new Circle();
         ShapeDecorator redCircle = new RedShapeDecorator(new Circle());
         ShapeDecorator redRectangle = new RedShapeDecorator(new Rectangle());
         //Shape redCircle = new RedShapeDecorator(new Circle());
         //Shape redRectangle = new RedShapeDecorator(new Rectangle());
         System.out.println("Circle with normal border");
         circle.draw();
    
         System.out.println("\nCircle of red border");
         redCircle.draw();
    
         System.out.println("\nRectangle of red border");
         redRectangle.draw();
      }
   }
   ```

##### 输出

```
Circle with normal border
Shape: Circle

Circle of red border
Shape: Circle
Border Color: Red

Rectangle of red border
Shape: Rectangle
Border Color: Red
```

