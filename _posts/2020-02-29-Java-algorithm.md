---
title: Java算法向
date:  2020-02-29 12:49:33 +0800
category: Original
tags: Java
excerpt: 记录着使用Java做算法题的基本操作
---

#### —. 在java中的基本头文件（java中叫包）

import java.io.*;//BufferedInputStream 

import java.util.*; //输入Scanner

import java.math.*; //BigInteger && BigDecimal

 

#### 二. 输入与输出

读入: Scanner cin = new Scanner (System.in);

推荐：Scanner cin = new Scanner (new BufferedInputStream (System.in));



Scanner cin = new Scanner(System.in);

类名  对象名      构造函数    参数

Scanner类提供了非常丰富的成员函数来负责读取各种数据类型:

| 返回值                      | 成员函数                                                     |
| --------------------------- | ------------------------------------------------------------ |
| boolean hasNext()           | 相当于C++的!=EOF                                             |
| String next(String pattern) | 如果下一个标记与从指定字符串构造的模式匹配，则返回下一个标记,如果参数为空,就是读取一个字符串 |
| BigDecimal nextBigDecimal() | 将输入信息的下一个标记扫描为一个 BigDecimal                  |
| BigInteger nextBigInteger() | 将输入信息的下一个标记扫描为一个 BigInteger                  |
| boolean nextBoolean()       | 扫描解释为一个布尔值的输入标记并返回该值。                   |
| byte nextByte()             | 将输入信息的下一个标记扫描为一个 byte。                      |
| double nextDouble()         | 将输入信息的下一个标记扫描为一个 double                      |
| float nextFloat()           | 将输入信息的下一个标记扫描为一个 float                       |
| int nextInt()               | 将输入信息的下一个标记扫描为一个 int                         |
| String nextLine()           | 此扫描器执行当前行，并返回跳过的输入信息                     |
| long nextLong()             | 将输入信息的下一个标记扫描为一个 long                        |
| short nextShort()           | 将输入信息的下一个标记扫描为一个 short                       |

**对于输出浮点数保留几位小数的问题，可以使用DecimalFormat类**，

import java.text.*; 

DecimalFormat f = new DecimalFormat("#.00#"); 

DecimalFormat g = new DecimalFormat("0.000"); 

double a = 123.45678, b = 0.12; 

System.out.println(f.format(a)); //123.457

System.out.println(f.format(b)); //.12

System.out.println(g.format(b)); //0.120



System.out.print(); // cout << …;

System.out.println(); //与C++的cout << … <<endl;

System.out.printf();	//与C中的printf用法类似.

 

#### 三. 定义变量

定义单个变量：

int a,b,c;      //和C++ 中无区别

BigInteger a;   //定义大数变量a

BigInteger b = BigInteger.valueOf(9);   //定义大数变量 b赋值为 9；

BigDecimal n；     //定义大浮点数类 n；

```
//BigInteger支持radix36以内的进制转换
BigInteger integer = BigInteger.valueOf(9);
System.out.println(integer.toString(2));
//输出1001
```

boolean : 布尔值，仅有两个值，true和false.

byte :字节类型值，长度8位（一个字节），范围-128至127。

short：短整型值，长度16位（两个字节），范围-32768至32767。

int：整型值，长度32位（四个字节），范围-2147483648至2147483647

long：长整型，长度64位（八个字节），范围-9223372036854775808至9223372036854775807

float：单精度浮点数，长度32位（四个字节）。

double：双精度浮点数，长度64位（八个字节）。

char：字符型，长度16位，支持所有的UCS-2和ASCII编码。

除了以上的8种基本数据类型,对于ACMer还有BigInteger,BigDecimal,String三个类经常使用.

 

#### 四.写法

import java.math.*;

import java.io.*;

import java.util.*;

 

public class Main {

​    public static void main(String[] args) {

​    }

}

 

#### 五.注意事项

1. Java 是面向对象的语言，思考方法需要变换一下，里面的函数统称为方法，不要搞错。 
2. Java 里的数组有些变动，多维数组的内部其实都是指针，所以**Java不支持fill多维数组**。数组定义后必须初始化，如 int[] a = new int[100]; 
3. 布尔类型为 boolean，只有true和false二值，在 if (...) / while (...) 等语句的条件中必须为boolean类型。 **在C/C++中的 if (n % 2) ... 在Java中无法编译通过。** 
4. 下面在java.util包里Arrays类的几个方法可替代C/C++里的**memset**、**qsort/sort** 和 bsearch: 

**Arrays.fill()**

**Arrays.sort()**

Arrays.binarySearch() 



#### 六.变量的初始化值

只有成员变量才有默认值，而局部变量必须要赋初值，为什么非怎么设置？下面我们来看一下。

| 类型           | 值                               |
| -------------- | -------------------------------- |
| Int            | 0                                |
| Long           | 0                                |
| Boolean        | false                            |
| float          | 0.0                              |
| double         | 0.0                              |
| char           | /u000(NULL)                      |
| String         | NULL                             |
| Object         | NULL                             |
| 数组(未初始化) | NULL                             |
| 数组(初始化)   | 数组各个元素的值，其类型的默认值 |



#### 七.Collection集合接口方法说明

| 方法名                                    | 说明                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| boolean add(E e)                          | 向集合添加元素e，若指定集合元素改变了则返回true              |
| boolean addAll(Collection<? extends E> c) | 把集合C中的元素全部添加到集合中，若指定集合元素改变返回true  |
| void clear()                              | 清空所有集合元素                                             |
| boolean contains(Object o)                | 判断指定集合是否包含对象o                                    |
| boolean containsAll(Collection<?> c)      | 判断指定集合是否包含集合c的所有元素                          |
| boolean isEmpty()                         | 判断指定集合的元素size是否为0                                |
| boolean remove(Object o)                  | 删除集合中的元素对象o,若集合有多个o元素，则只会删除第一个元素 |
| boolean removeAll(Collection<?> c)        | 删除指定集合包含集合c的元素                                  |
| boolean retainAll(Collection<?> c)        | 从指定集合中保留包含集合c的元素,其他元素则删除               |
| int size()                                | 集合的元素个数                                               |
| T[] toArray(T[] a)                        | 将集合转换为T类型的数组                                      |



#### 八.String的常用方法

```
//char[]和String相互转换
String str = "ggg";
char[] bm;
bm = str.toCharArray();//String->char[]
str = String.valueOf(bm);//char[]->String
```

| 方法名                                         | 说明                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ |
| char charAt(int index)                         | 返回 char指定索引处的值                                      |
| int compareTo(String anotherString)            | 按字典顺序比较两个字符串                                     |
| int compareToIgnoreCase(String str)            | 按字典顺序比较两个字符串，忽略大小写                         |
| String concat(String str)                      | 将指定的字符串连接到该字符串的末尾                           |
| boolean contains(CharSequence s)               | 当且仅当此字符串包含指定的char值序列时才返回true             |
| boolean endsWith(String suffix)                | 测试此字符串是否以指定的后缀结尾                             |
| boolean equals(Object anObject)                | 将此字符串与指定对象进行比较                                 |
| boolean equalsIgnoreCase(String anotherString) | 将此 String与其他 String比较，忽略大小写                     |
| boolean isEmpty()                              | 返回 true如果，且仅当 length()为 0                           |
| int length()                                   | 返回此字符串的长度                                           |
| boolean matches(String regex)                  | 告诉这个字符串是否匹配给定的 regular expression              |
| String replace(char oldChar, char newChar)     | 返回从替换所有出现的导致一个字符串 oldChar在此字符串 newChar |
| String substring(int beginIndex, int endIndex) | 返回一个字符串，该字符串是此字符串的子字符串                 |
| char[] toCharArray()                           | 将此字符串转换为新的字符数组                                 |
| static String valueOf(char[] data)             | 返回 char数组参数的字符串 char形式                           |