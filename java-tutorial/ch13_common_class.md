- [第13章 常用类](#第13章-常用类)
  - [包装类](#包装类)
    - [包装类的分类](#包装类的分类)
    - [包装类和基本数据的转换](#包装类和基本数据的转换)
    - [包装类型和String 类型的相互转换](#包装类型和string-类型的相互转换)
    - [Integer 类和Character 类的常用方法](#integer-类和character-类的常用方法)
    - [Integer 类面试题](#integer-类面试题)
  - [String 类](#string-类)
    - [String 类的理解和创建对象](#string-类的理解和创建对象)
    - [创建String 对象的两种方式](#创建string-对象的两种方式)
    - [两种创建String 对象的区别](#两种创建string-对象的区别)
    - [课堂测试题](#课堂测试题)
  - [字符串的特性](#字符串的特性)
    - [说明](#说明)
    - [面试题](#面试题)
  - [String 类的常见方法](#string-类的常见方法)
    - [说明](#说明-1)
    - [String 类的常见方法一览](#string-类的常见方法一览)
  - [StringBuffer 类](#stringbuffer-类)
    - [基本介绍](#基本介绍)
    - [String VS StringBuffer](#string-vs-stringbuffer)
    - [构造器](#构造器)
    - [String 和 StringBuffer 相互转换](#string-和-stringbuffer-相互转换)
    - [StringBuffer 类常见方法](#stringbuffer-类常见方法)
    - [StringBuffer 类测试](#stringbuffer-类测试)
    - [StringBuffer 类练习](#stringbuffer-类练习)
  - [StringBuilder 类](#stringbuilder-类)
    - [基本介绍](#基本介绍-1)
    - [String、StringBuffer 和StringBuilder 的比较](#stringstringbuffer-和stringbuilder-的比较)
    - [String、StringBuffer 和StringBuilder 的效率测试](#stringstringbuffer-和stringbuilder-的效率测试)
    - [String、StringBuffer 和 StringBuilder 的选择](#stringstringbuffer-和-stringbuilder-的选择)
# 第13章 常用类

## 包装类

### 包装类的分类

1) 针对八种基本数据类型相应的引用类型—包装类
2) 有了类的特点，就可以调用类中的方法。
### 包装类和基本数据的转换

演示包装类和基本数据类型的相互转换,这里以int和 Integer演示。

1) jdk5前的手动装箱和拆箱方式，装箱：**基本类型->包装类型**。反之拆箱。
2) jdk5以后(含jdk5)的自动装箱和拆箱方式。
3) 自动装箱底层调用的是valueOf方法，比如Integer.valueOf04)其它包装类的用法类似,不一一举例

```java
package com.hspedu.wrapper;

public class Integer01 {
    public static void main(String[] args) {
        //演示int <--> Integer 的装箱和拆箱
        //jdk5前是手动装箱和拆箱
        //手动装箱 int->Integer
        int n1 = 100;
        Integer integer = new Integer(n1);
        Integer integer1 = Integer.valueOf(n1);

        //手动拆箱
        //Integer -> int
        int i = integer.intValue();

        //jdk5后，就可以自动装箱和自动拆箱
        int n2 = 200;
        //自动装箱 int->Integer
        Integer integer2 = n2; //底层使用的是 Integer.valueOf(n2)
        //自动拆箱 Integer->int
        int n3 = integer2; //底层仍然使用的是 intValue()方法
    }
}
```

### 包装类型和String 类型的相互转换

```java
package com.hspedu.wrapper;

public class WrapperVSString {
    public static void main(String[] args) {
        //包装类(Integer)->String
        Integer i = 100;//自动装箱
        //方式1
        String str1 = i + "";
        //方式2
        String str2 = i.toString();
        //方式3
        String str3 = String.valueOf(i);

        //String -> 包装类(Integer)
        String str4 = "12345";
        Integer i2 = Integer.parseInt(str4);//使用到自动装箱
        Integer i3 = new Integer(str4);//构造器

        System.out.println("ok~~");

    }
}
```

### Integer 类和Character 类的常用方法

可以通过图查询到其含有的字段和方法，jump to source 可以查看到源码。

```java
package com.hspedu.wrapper;

public class WrapperMethod {
    public static void main(String[] args) {
        System.out.println(Integer.MIN_VALUE); //返回最小值
        System.out.println(Integer.MAX_VALUE);//返回最大值

        System.out.println(Character.isDigit('a'));//判断是不是数字
        System.out.println(Character.isLetter('a'));//判断是不是字母
        System.out.println(Character.isUpperCase('a'));//判断是不是大写
        System.out.println(Character.isLowerCase('a'));//判断是不是小写

        System.out.println(Character.isWhitespace('a'));//判断是不是空格
        System.out.println(Character.toUpperCase('a'));//转成大写
        System.out.println(Character.toLowerCase('A'));//转成小写

    }
}

```

### Integer 类面试题

看看下面代码，输出什么结果? 为什么?

```java
package com.hspedu.wrapper;

public class WrapperExercise02 {
    public static void main(String[] args) {
        Integer i = new Integer(1);
        Integer j = new Integer(1);
        System.out.println(i == j);  //False
        //所以，这里主要是看范围 -128 ~ 127 就是直接返回
        /*
        //1. 如果i 在 IntegerCache.low(-128)~IntegerCache.high(127),就直接从缓存数组返回
        //2. 如果不在 -128~127,就直接 new Integer(i)
         public static Integer valueOf(int i) {
            if (i >= IntegerCache.low && i <= IntegerCache.high)
                return IntegerCache.cache[i + (-IntegerCache.low)];
            return new Integer(i);
        }
         */
        Integer m = 1; //底层 Integer.valueOf(1); -> 阅读源码
        Integer n = 1;//底层 Integer.valueOf(1);
        System.out.println(m == n); //T
        //所以，这里主要是看范围 -128 ~ 127 就是直接返回
        //，否则，就new Integer(xx);
        Integer x = 128;//底层Integer.valueOf(1);
        Integer y = 128;//底层Integer.valueOf(1);
        System.out.println(x == y);//False

        Integer i11=127;
        int i12=127;
        //只要有基本数据类型，判断的是
        //值是否相同
        System.out.println(i11==i12); //T

        Integer i13=128;
        int i14=128;
        System.out.println(i13==i14);//T
    }
}
```

## String 类

### String 类的理解和创建对象

1) String对象用于保存字符串,也就是一组字符序列

2) 字符串常量对象是用双引号括起的字符序列。例如:"你好"、"12.97"、"boy"等

3) 字符串的字符使用Unicode字符编码，一个字符(不区分字母还是汉字)占两个字节

4) String类较常用构造器(其它看手册);

   String s1 =new String();

   String s2 = new String(String original);

   String s3 = new String(char[] a);

   String s4 = new String(char[] a, int startIndex, int count)


实现Serializable，说明可以串行化，即可以在网络上传输。

实现接口Comparable  [String 对象可以比较大小]

```java
package com.hspedu.string_;

import java.io.Serializable;

public class String01 {
    public static void main(String[] args) {
//         1.String 对象用于保存字符串，也就是一组字符序列
//         2. "jack" 字符串常量, 双引号括起的字符序列
//         3. 字符串的字符使用Unicode字符编码，一个字符(不区分字母还是汉字)占两个字节
//         4. String 类有很多构造器，构造器的重载
//           常用的有 String  s1 = new String(); //
//                      String  s2 = new String(String original);
//                      String  s3 = new String(char[] a);
//                      String  s4 =  new String(char[] a,int startIndex,int count)
//                      String  s5 = new String(byte[] b)
//        5. String 类实现了接口 Serializable【String 可以串行化:可以在网络传输】
//                         接口 Comparable [String 对象可以比较大小]
//        6. String 是 final 类，不能被其他的类继承
//        7. String 有属性 private final char value[]; 用于存放字符串内容, 说明其本质还是char数组。
//        8. 一定要注意：value 是一个final类型，不可以修改(地址不能修改)：即value不能指向新的地址，但是单个字符内容是可以变化

        String name = "jack";
        name = "tom";
        final char[] value = {'a','b','c'};
        char[] v2 = {'t','o','m'};
        value[0] = 'H';
        //value = v2; 不可以修改 value地址
        System.out.println(name); //Tom
    }
}

```

### 创建String 对象的两种方式

1) 方式一: 直接赋值String s = "hspedu";
2) 方式二: 调用构造器 String s = new String("hspedu");

### 两种创建String 对象的区别

1. 方式一:先从常量池查看是否有"hsp”数据空间,如果有，直接指向;如果
   没有则重新创建,然后指向。**s最终指向的是常量池的空间地址**。

2. 方式二:先在堆中创建空间，里面维护了value属性，指向常量池的hsp空间。

   如果常量池没有"hsp"，重新创建，如果有，直接通过value指向。最终指向的是堆中的空间地址。

3. 画出两种方式的内存分布图

   ![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230420110053585.png)

### 课堂测试题

```java
package com.hspedu.string_;

public class StringExercise01 {
    public static void main(String[] args) {
        String a = "abc";
        String b ="abc";
        // equals在string中被重写，逐个比较，相同
        System.out.println(a.equals(b));//T
        System.out.println(a==b); //T
		// 这里指向的是同一个地址，故 == 也相同
    }
}
```

```java
package com.hspedu.string_;

public class StringExercise03 {
    public static void main(String[] args) {
        String a = "hsp"; //a 指向 常量池的 “hsp”
        String b =new String("hsp");//b 指向堆中对象
        System.out.println(a.equals(b)); //T
        System.out.println(a==b); //F
        //b.intern() 方法返回常量池地址
        System.out.println(a==b.intern()); //T intern方法查看API
        System.out.println(b==b.intern()); //F
        // b 指向的是堆地址，b.intern 返回的是常量池地址
    }
}
```

当调用intern方法时，如果池已经包含一个等于此 String对象的字符串(用equals(Object)方法确定)，则返回池中的字符串。否则，将此String 对象添加到池中，并返回此 String对象的引用

b.intern方法**最终返回的是常量池的地址(对象)**

## 字符串的特性

### 说明

1) String是一个final类，代表不可变的字符序列
2) 字符串是不可变的。一个字符串对象一旦被分配，其内容是不可变的.

例：以下语句创建了几个对象?

```java
String s1 = "hello";
s1 = "haha"; //创建了2个对象，从指向hello变为了指向haha（而不是修改hello为haha）
```

### 面试题

1)题1

```java
String a ="hello" +"abc";
```

创建了几个对象?只有1个对象
String a = "hello"+"abc"; ==> 优化等价 String a = "helloabc";

分析：编译器不傻，做一个优化，判断创建的常量池对象，是否有引用指向。

2)题2 

```java
String a ="hello";//创建a对象
String b ="abc";//创建b对象
String c = a + b;
```

创建了几个对象?画出内存图? 一共有3对象,如图。

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230420145913656.png)

底层是StringBuilder sb = new StringBuilder(); sb.append(a); sb.append(b); sb是在堆中，并且append是在原来字符串的基础上追加的。

重要规则：String c1 = "ab" + "cd";常量相加，看的是池。String c1 = a+b;变量相加,是在堆中

**综合练习**

```java
package com.hspedu.string_;

public class StringExercise10 {
    public static void main(String[] args) {

    }
}

class Test1 {
    String str = new String("hsp");
    final char[] ch = {'j', 'a', 'v', 'a'};

    public void change(String str, char ch[]) {
        str = "java";
        ch[0] = 'h';
    }

    public static void main(String[] args) {
        Test1 ex = new Test1();
        ex.change(ex.str, ex.ch);
        System.out.print(ex.str + " and ");
        System.out.println(ex.ch);
    }
}
```

数组默认情况下是在堆中的，

每次调方法都会产生对应的新栈，过程如下所示：

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230420151016848.png)

## String 类的常见方法

### 说明

String类是保存字符串常量的。每次更新都需要重新开辟空间，效率较低,因此java设计者还提供了`StringBuilder` 和 `StringBuffer`来增强String的功能,并提高效率。

### String 类的常见方法一览

+ equals //区分大小写，判断内容是否相等
+ equalsIgnoreCase //忽略大小写的判断内容是否相等length/获取字符的个数,字符串的长度
+ length 获取字符的个数，字符串的长度
+ indexOf //获取字符在字符串中第1次出现的索引索引从0开始,如果找不到,返回-1
+ lastIndexOf //获取字符在字符串中最后1次出现的索引,索引从0开始,如找不到,返回-1
+ substring //截取指定范围的子串
+ trim //去前后空格
+ charAt // 获取某索引处的字符, 注意不能使用Str[index]这种方式.

```java
package com.hspedu.string_;

public class StringMethod01 {
    public static void main(String[] args) {
        // 1. equals 前面已经讲过了. 比较内容是否相同，区分大小写
        String str1 = "hello";
        String str2 = "Hello";
        System.out.println(str1.equals(str2));//

        // 2.equalsIgnoreCase 忽略大小写的判断内容是否相等
        String username = "johN";
        if ("john".equalsIgnoreCase(username)) {
            System.out.println("Success!");
        } else {
            System.out.println("Failure!");
        }
        // 3.length 获取字符的个数，字符串的长度
        System.out.println("韩顺平".length());
        // 4.indexOf 获取字符在字符串对象中第一次出现的索引，索引从0开始，如果找不到，返回-1
        String s1 = "wer@terwe@g";
        int index = s1.indexOf('@');
        System.out.println(index);// 3
        System.out.println("weIndex=" + s1.indexOf("we"));//0
        // 5.lastIndexOf 获取字符在字符串中最后一次出现的索引，索引从0开始，如果找不到，返回-1
        s1 = "wer@terwe@g@";
        index = s1.lastIndexOf('@');
        System.out.println(index);//11
        System.out.println("ter的位置=" + s1.lastIndexOf("ter"));//4
        // 6.substring 截取指定范围的子串
        String name = "hello,张三";
        // 下面name.substring(6) 从索引6开始截取后面所有的内容
        System.out.println(name.substring(6));//截取后面的字符
        // name.substring(0,5)表示从索引0开始截取，截取到索引5 - 1 = 4位置
        System.out.println(name.substring(2,5));//llo
    }
}
```

+ toUpperCase

+ toLowerCase

+ concat

+ replace 替换字符串中的字符

+ split 分割字符串,对于某些分割字符，我们需要转义比如| \\\等

  案例: String poem="锄禾日当午,汗滴未下土,谁知盘中餐,粒粒皆辛苦";和文件路径.

+ compareTo //比较两个字符串的大小

+ toCharArray //转换成字符数组

+ format //格式字符串,%s字符串%c字符%d整型%.2f 浮点型案例，将一个人的信息格式化输出.

```java
package com.hspedu.string_;

public class StringMethod02 {
    public static void main(String[] args) {
        // 1.toUpperCase转换成大写
        String s = "heLLo";
        System.out.println(s.toUpperCase());//HELLO
        // 2.toLowerCase
        System.out.println(s.toLowerCase());//hello
        // 3.concat拼接字符串
        String s1 = "宝玉";
        s1 = s1.concat("林黛玉").concat("薛宝钗").concat("together");
        System.out.println(s1);//宝玉林黛玉薛宝钗together
        // 4.replace 替换字符串中的字符
        s1 = "宝玉 and 林黛玉 林黛玉 林黛玉";
        //在s1中，将 所有的 林黛玉 替换成薛宝钗
        // 老韩解读: s1.replace() 方法执行后，返回的结果才是替换过的.
        // 注意对 s1没有任何影响
        String s11 = s1.replace("宝玉", "jack");
        System.out.println(s1);//宝玉 and 林黛玉 林黛玉 林黛玉
        System.out.println(s11);//jack and 林黛玉 林黛玉 林黛玉
        // 5.split 分割字符串, 对于某些分割字符，我们需要 转义比如 | \\等
        String poem = "锄禾日当午,汗滴禾下土,谁知盘中餐,粒粒皆辛苦";
        // 1. 以 , 为标准对 poem 进行分割 , 返回一个数组
        // 2. 在对字符串进行分割时，如果有特殊字符，需要加入 转义符 \
        String[] split = poem.split(",");
        poem = "E:\\aaa\\bbb";
        split = poem.split("\\\\");
        System.out.println("==分割后内容===");
        for (int i = 0; i < split.length; i++) {
            System.out.println(split[i]);
        }
        // 6.toCharArray 转换成字符数组
        s = "happy";
        char[] chs = s.toCharArray();
        for (int i = 0; i < chs.length; i++) {
            System.out.println(chs[i]);
        }
        // 7.compareTo 比较两个字符串的大小，如果前者大，
        // 则返回正数，后者大，则返回负数，如果相等，返回0
        // (1) 如果长度相同，并且每个字符也相同，就返回 0
        // (2) 如果长度相同或者不相同，但是在进行比较时，可以区分大小
        //     就返回 if (c1 != c2) {
        //                return c1 - c2;
        //            }
        // (3) 如果前面的部分都相同，就返回 str1.len - str2.len
        String a = "jcck";// len = 3
        String b = "jack";// len = 4
        System.out.println(a.compareTo(b)); // 返回值是 'c' - 'a' = 2的值
        // 8.format 格式字符串
        /* 占位符有:
         * %s 字符串 %c 字符 %d 整型 %.2f 浮点型
         */
        String name = "john";
        int age = 10;
        double score = 56.857;
        char gender = '男';
        //将所有的信息都拼接在一个字符串.
        String info =
                "我的姓名是" + name + "年龄是" + age + ",成绩是" + score + "性别是" + gender + "。希望大家喜欢我！";

        System.out.println(info);


        // 1. %s , %d , %.2f %c 称为占位符
        // 2. 这些占位符由后面变量来替换
        // 3. %s 表示后面由 字符串来替换
        // 4. %d 是整数来替换
        // 5. %.2f 表示使用小数来替换，替换后，只会保留小数点两位, 并且进行四舍五入的处理
        // 6. %c 使用char 类型来替换
        String formatStr = "我的姓名是%s 年龄是%d，成绩是%.2f 性别是%c.希望大家喜欢我！";

        String info2 = String.format(formatStr, name, age, score, gender);
        System.out.println("info2=" + info2);
    }
}
```

## StringBuffer 类

### 基本介绍

java.lang.StringBuffer代表可变的字符序列，可以对字符串内容进行增删.

很多方法与String相同，但StringBuffer是可变长度的。

StringBuffer是一个容器。

1. StringBuffer 的直接父类 是 AbstractStringBuilder
2. StringBuffer 实现了 Serializable, 即StringBuffer的对象可以串行化
3. 在父类中  AbstractStringBuilder 有属性 char[] value,不是final，该 value 数组存放 字符串内容，因此存放在堆中的。
4. StringBuffer 是一个 final类，不能被继承
5. **因为StringBuffer 字符内容是存在 char[] value, 所有在变化(增加/删除)不用每次都更换地址(即不是每次创建新对象)， 所以效率高于 String**。

### String VS StringBuffer

1. String保存的是字符串常量。里面的值不能更改，每次String类的更新实际上就是更改地址，效率较低 

   private final char value[];

2. StringBuffer保存的是字符串变量，里面的值可以更改，每次StringBuffer的更新实际上可以更新内容，不用每次更新地址（空间大小不够的时候才会进行扩展），效率较高。

  char[] value; 这个放在堆。

### 构造器

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230420155253011.png)

```java
package com.hspedu.stringbuffer_;

public class StringBuffer02 {
    public static void main(String[] args) {

        //构造器的使用
        //1. 创建一个 大小为 16的 char[] ,用于存放字符内容
        StringBuffer stringBuffer = new StringBuffer();

        //2 通过构造器指定 char[] 大小
        StringBuffer stringBuffer1 = new StringBuffer(100);
        //3. 通过 给一个String 创建 StringBuffer, char[] 大小就是 str.length() + 16

        StringBuffer hello = new StringBuffer("hello");
    }
}
```

### String 和 StringBuffer 相互转换

String ——> StringBuffer

+ 使用构造器
+ 使用的是 append 方法

StringBuffer ——> String

+ 使用 StringBuffer 提供的 toString 方法
+ 使用构造器来搞定

```java
package com.hspedu.stringbuffer_;

public class StringAndStringBuffer {
    public static void main(String[] args) {

        // String——>StringBuffer
        String str = "hello tom";
        //方式1 使用构造器
        //注意：返回的才是StringBuffer对象，对 str 本身没有影响
        StringBuffer stringBuffer = new StringBuffer(str);
        //方式2：使用的是append方法
        StringBuffer stringBuffer1 = new StringBuffer();
        stringBuffer1 = stringBuffer1.append(str);

        // StringBuffer ->String
        StringBuffer stringBuffer3 = new StringBuffer("timerring");
        //方式1：使用StringBuffer提供的 toString方法
        String s = stringBuffer3.toString();
        //方式2：使用构造器来搞定
        String s1 = new String(stringBuffer3);
    }
}
```

### StringBuffer 类常见方法

+ append
+ delete 删除索引为>=start && <end 处的字符
+ replace
+ insert 在索引为index的位置插入 ,原来索引为index的内容自动后移

+ length() 长度

```java
package com.hspedu.stringbuffer_;

public class StringBufferMethod {
    public static void main(String[] args) {

        StringBuffer s = new StringBuffer("hello");
        //增
        s.append(',');// "hello,"
        s.append("张三丰");//"hello,张三丰"
        s.append("赵敏").append(100).append(true).append(10.5);//"hello,张三丰赵敏100true10.5"
        System.out.println(s);//"hello,张三丰赵敏100true10.5"

        // 删
        /*
         * 删除索引为>=start && <end 处的字符
         * 解读: 删除 11~14的字符 [11, 14)
         */
        s.delete(11, 14);
        System.out.println(s);//"hello,张三丰赵敏true10.5"
        // 改
        // 使用 周芷若 替换 索引9-11的字符 [9,11)
        s.replace(9, 11, "周芷若");
        System.out.println(s);//"hello,张三丰周芷若true10.5"
        // 查找指定的子串在字符串第一次出现的索引，如果找不到返回-1
        int indexOf = s.indexOf("张三丰");
        System.out.println(indexOf);//6
        // 插
        // 在索引为9的位置插入 "赵敏",原来索引为9的内容自动后移
        s.insert(9, "赵敏");
        System.out.println(s);// "hello,张三丰赵敏周芷若true10.5"
        //长度
        System.out.println(s.length());//22
        System.out.println(s);
    }
}
```

### StringBuffer 类测试

```java
package com.hspedu.stringbuffer_;
// 分析以下代码
public class StringBufferExercise01 {
    public static void main(String[] args) {
        String str = null;// ok
        StringBuffer sb = new StringBuffer(); //ok
        sb.append(str);// 需要看源码 , 底层调用的是 AbstractStringBuilder 的 appendNull, 转为了一个字符数组。
        System.out.println(sb.length());// 4

        System.out.println(sb);// null 是一个字符数组
        //下面的构造器，会抛出 NullpointerException
        StringBuffer sb1 = new StringBuffer(str);// 看底层源码 super(str.length() + 16); 会抛出空指针异常
        System.out.println(sb1);
    }
}
```

### StringBuffer 类练习

```java
package com.hspedu.stringbuffer_;

import java.util.Scanner;

public class StringBufferExercise02 {
    public static void main(String[] args) {
        /*
        输入商品名称和商品价格，要求打印效果示例, 使用前面学习的方法完成：
        商品名	商品价格
        手机	123,564.59  //比如 价格 3,456,789.88
        要求：价格的小数点前面每三位用逗号隔开, 在输出。
        思路分析
        1. 定义一个Scanner 对象，接收用户输入的 价格(String)
        2. 希望使用到 StringBuffer的 insert ，需要将 String 转成 StringBuffer
        3. 然后使用相关方法进行字符串的处理
         */

        //new Scanner(System.in)
        String price = "8123564.59";
        StringBuffer sb = new StringBuffer(price);
        // 先完成一个最简单的实现123,564.59
        // 找到小数点的索引，然后在该位置的前3位，插入,即可
		//  int i = sb.lastIndexOf(".");
		//  sb = sb.insert(i - 3, ",");

        //上面的两步需要做一个循环处理,才是正确的
        for (int i = sb.lastIndexOf(".") - 3; i > 0; i -= 3) {
            sb = sb.insert(i, ",");
        }
        System.out.println(sb);//8,123,564.59
    }
}
```

## StringBuilder 类

### 基本介绍

1) 一个可变的字符序列。此类提供一个**与 StringBuffer兼容的API**，但不保证同步(StringBuilder不是线程安全)。该类被设计用作 StringBuffer的一个简易替换，用在字符串缓冲区被单个线程使用的时候。如果可能，建议优先采用该类。因为**在大多数实现中，它比 StringBuffer 要快**。
2) 在 StringBuilder上的主要操作是append 和 insert方法，可重载这些方法,
   以接受任意类型的数据。

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230420204712606.png)

1. StringBuilder 继承 AbstractStringBuilder 类
2. 实现了 Serializable ,说明StringBuilder对象是可以串行化(对象可以网络传输,可以保存到文件)
3. StringBuilder 是final类, 不能被继承
4. StringBuilder 对象字符序列仍然是存放在其父类 AbstractStringBuilder的 char[] value;因此，字符序列是堆中
5. StringBuilder 的方法，没有做互斥的处理，即没有synchronized 关键字,因此在单线程的情况下使用 StringBuilder

### String、StringBuffer 和StringBuilder 的比较

StringBuilder 和 StringBuffer 均代表可变的字符序列，方法是一样的，所以使用和StringBuffer一样。

1) StringBuilder和 StringBuffer非常类似，均代表可变的字符序列，而且方法也一样
2) String:不可变字符序列,效率低,但是复用率高（地址都指向它）。
3) StringBuffer:可变字符序列、效率较高(增删)、线程安全,看源码
3) StringBuilder:可变字符序列、效率最高、线程不安全
5) String使用注意说明:
string s="a";//创建了一个字符串
s +="b";//实际上原来的"a"字符串对象已经丢弃了，现在又产生了一个字符串s+"b”(也就是"ab")。如果多次执行这些改变串内容的操作，会导致大量副本字符串对象存留在内存中，降低效率。如果这样的操作放到循环中，会极大,影响程序的性能=>

**结论:如果我们对String做大量修改,不要使用String**

### String、StringBuffer 和StringBuilder 的效率测试

StringVsStringBufferVsStringBuilder.java 效率： StringBuilder > StringBuffer > String

```java
package com.hspedu.stringbuilder_;

public class StringVsStringBufferVsStringBuilder {
    public static void main(String[] args) {

        long startTime = 0L;
        long endTime = 0L;
        StringBuffer buffer = new StringBuffer("");

        startTime = System.currentTimeMillis();
        for (int i = 0; i < 80000; i++) {//StringBuffer 拼接 20000次
            buffer.append(String.valueOf(i));
        }
        endTime = System.currentTimeMillis();
        System.out.println("StringBuffer的执行时间：" + (endTime - startTime)); // 20

        StringBuilder builder = new StringBuilder("");
        startTime = System.currentTimeMillis();
        for (int i = 0; i < 80000; i++) {//StringBuilder 拼接 20000次
            builder.append(String.valueOf(i));
        }
        endTime = System.currentTimeMillis();
        System.out.println("StringBuilder的执行时间：" + (endTime - startTime)); // 11


        String text = "";
        startTime = System.currentTimeMillis();
        for (int i = 0; i < 80000; i++) {//String 拼接 20000
            text = text + i;
        }
        endTime = System.currentTimeMillis();
        System.out.println("String的执行时间：" + (endTime - startTime)); // 5428

    }
}
```

### String、StringBuffer 和 StringBuilder 的选择

使用的原则,结论:

1. 如果字符串存在大量的修改操作，一般使用StringBuffer 或StringBuilder
2. 如果字符串存在大量的修改操作，并在单线程的情况, 使用 StringBuilder
3. 如果字符串存在大量的修改操作，并在多线程的情况，使用 StringBuffer
4. 如果我们字符串很少修改。被多个对象引用，使用String, 比如配置信息等

