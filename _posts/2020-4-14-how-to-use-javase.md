---
layout: post
title: JAVASE总结
tags: java
categories: backends
---
JRE、JDK区别、安装、设置环境变量、快速开始、数据类型、变量、类型转换、ASCII码、表达式、运算符、方法、程序逻辑、数组、内存的5个部分、类与对象、多线程、File类、函数式接口、Stream流


**JRE、JDK区别**

JRE是指java运行的必要环境。JDK是指java的开发工具包，包含JRE及开发相关的工具、关键字、常量、

---

**安装**

官网下载，点击下一步。

> 注意：安装路径不要含有冒号和空格，取消公共JRE项
>
> ---

**设置环境变量**

系统属性->高级->环境变量

系统变量：

新建->-JAVAA_HOME对应java的路径目录

双击path，新增记录%JAVAA_HOME%bin;

重新打开cmd，输入java，验证是否配置好

---

**快速开始**

`1`创建名为HelloWorld.java的文件

`2`文件内写入代码

```java
public class HelloWorld{//注意：类名必须和文件名一致
    public static void main(String args[]){
        System.out.println("helloword");
    }
}
```

`3`编译

```
javac HelloWorld.java
```

生成HelloWorld.class文件

`4`运行

```
java HelloWorld
```

---

**关键字**

具有特殊含义的单词，如static,public

---

标识符

为特定代码指定的唯一名称。标识符设定可以是字母、数字、下划线，但不可以数字开头，也不能用关键字

---

**常量**

不可被改变的量

| 常量分类   | 解释                                   |
| ---------- | -------------------------------------- |
| 字符串常量 | 双引号引起来的部分，可以为“”，不会报错 |
| 整数常量   |                                        |
| 浮点数常量 |                                        |
| 字符常量   | 单引号引起来的部分，不可为‘’，会报错   |
| 布尔常量   |                                        |
| 空常量     | null没有任何数据，不可直接打印输出     |

---

**数据类型**

分为基本类型和引用类型

`1`基本类型

| 类型            | 占用字节 | 范围         |
| --------------- | -------- | ------------ |
| byte（整数）    | 1        | -128~127     |
| short（整数）   | 2        | -32768~32767 |
| int（整数）     | 4        | -21亿~21亿   |
| long（整数）    | 8        | -            |
| float（浮点）   | 4        | -            |
| double（浮点）  | 8        | -            |
| char（字符）    | 2        | 0~65535      |
| boolean（布尔） | -        | -            |

> float的范围比long的范围大，所以并非占用字节越多能表示的数据范围就一定大

`2`引用类型

字符串、数组、类、接口、lambda

数据类型总结：

- 字符串属于引用类型
- 浮点数表示的是一个近似值
- 数据范围与占用的字节数不一定正相关
- 小数默认为double类型，要使用float类型，需要在后面加上F
- 整数默认为int类型，要使用long类型，需要在后面加上L

---

**变量**

程序运行过程中，内容可发生变化的量

`1`创建步骤：

```java
int num;//创建变量
num=20;//存入数据

int num1=20;//简写
```

> 注意
>
> 1. 右值不可超出数据类型规定的范围
> 2. 变量名不可重复
> 3. 对于数据类型是float,long的数据，后面的F,L不可丢掉
> 4. 未赋值变量不可使用
> 5. 变量的使用不可超过作用域

`2`编译常量优化

给变量赋值后，右侧表达式如果全是常量，编译器会将这里的常量进行计算后得到的结果赋值给变量

```java
public class HelloWorld{
    public static void main(String args[]){
        byte a=27+100;
        short b=100+20;
        char c=110+1;//这里会在编译时进行计算得到结果赋值给c
        long sum=a+1;//这里编译器不会计算，sum的类型如果不是int及以上就报错
        System.out.println(sum);
    }

}
```



---

**类型转换**

为变量赋值时，左值和右值类型不一致，需要将右值转换成和左值相同的类型

`1`自动类型转换

代码不需要进行特殊处理，自动完成。前提是，右值可表示的范围小于左值

```java
double num1=2.5F;
System.out.println(num1);
```

`2`强制类型转换

```java
int num;
double num1=20000000000F;
num=(int) num1;
System.out.println(num);//2147483647
```

> 注意
>
> 1. 强制类型转换需要慎用，可能会出现溢出或精度损失
> 2. byte,short,char这三种类型可发生属性运算，在运算时会提升为int类型
> 3. boolean不能发生数据类型转换

---

**ASCII码**

计算机只能处理数字，将字符与数字对应而创造的表。其中，数字0对应48，字母A对应65，字母a对应97

---

**表达式**

操作数与运算符的组合

---

**运算符**

分为赋值运算符、算术运算符、关系(比较)运算符、逻辑运算符等

| 运算符           | 举例                  | 备注                            |
| ---------------- | --------------------- | ------------------------------- |
| 赋值运算符       | =，+=，/=,*=,%=,++,-- |                                 |
| 算术运算符       | +，-，*，/，%         |                                 |
| 关系(比较)运算符 | >，<，<=，>=，==，!=  | 多次比较不可连写，结果为boolean |
| 逻辑运算符       | &，&&，\|，\|\|，!    | 操作数只能时boolean             |

*多元运算符概念*

一元运算符：只需要一个数据就能操作的运算符，如取反，++，--

二元运算符：两个数据就可操作的运算符，如+，=

三元运算符：三个数据才可进行操作的运算符，?:

*三元运算*

类型 变量名=条件判断?表达式A:表达式B

```
int num=1;
String result=num>0?"num取值大于0":"num取值小于0";
System.out.println(result);//num取值大于0
```

> 注意
>
> - 表达式A、B搜应满足左侧数据类型的要求
> - 三元运算的结果必须使用

---

**方法**

由于java是完全面向对象的语言，所以java里没有函数，只有方法

```java
public class HelloWorld{
    public static void main(String args[]){
        run();
        System.out.println(num());
    }
    public static void run(){//该方法必须是静态方法，否则在main函数中无法调用，无返回值
        System.out.println("run");
    }
    public static int num(){//返回值为int类型
        return 6;
    }
}
//1.方法定义的先后顺序无所谓
//2.方法定义不能产生嵌套包含关系
//3.返回数据的方法，只能有一个返回值
```

`1`方法重载

方法名相同，但方法的参数不同

```java
public class HelloWorld{
    public static void main(String args[]){
        run();//执行第一个run方法
        run(5);//执行第二个run方法
    }
    public static void run(){
        System.out.println("run");
    }
    public static void run(int a){
        System.out.println(a);
        System.out.println("run");
    }
}
```

`2`递归

```java
public class HelloWorld{
    public static void main(String args[]){
        System.out.println(Multiplicative(5));
    }
    public static int Multiplicative(int a){//定义累乘
        if(a==1){//结束条件
            return 1;
        }
        a--;//将参数逐渐逼近至结束条件
        return a*Multiplicative(a);
    }
}
```

`3`可变参数

jdk1.5后出现的新特性，当方法的参数列表数据类型已确定，但参数个数不确定，可使用可变参数

底层是一个数组，根据参数个数不同，创建不同长度的数组来存放这些参数

```java
public class Hello {
    public static void main(String[] args) {
        message("a","b","c","d");
    }
    public static void message(String ...args){//定义含可变参数的方法
        for (String arg:
             args) {
            System.out.println(arg);
        }

    }
	/*
    注意：
    1.一个方法的参数列表，只能有一个可变参数
    2.如果方法的参数有多个，可变参数必须在末尾
    */
}
```



---

**程序逻辑**

| 逻辑结构 | 使用形式               | 备注                                   |
| -------- | ---------------------- | -------------------------------------- |
| 顺序结构 |                        | 程序从上至下顺序执行                   |
| 分支结构 | if…else…及switch…case… | 程序根据条件选择性执行代码段           |
| 循环结构 | while…及for…           | 程序在满足前置条件下循环重复执行代码段 |

---

**数组**

是一种引用类型，其中的数据的类型必须同一，长度被声明后不可修改

`1`初始化

1）动态初始化

创建数组时，直接指定数组中元素的个数

```java
int[] array=new int[9];
```

2）静态初始化

创建数组时，不指定长度，直接将数据放入

```java
int[] array=new int[]{1,2,3,4,5,6,7,8,9};
//可简写为 int[] array={1,2,3,4,5,6,7,8,9};
```

初始化完成后，得到一个数组的指针，数组内未赋值的位置会得到一个默认值

| 数组类型 | 默认值   |
| -------- | -------- |
| 整数型   | 0        |
| 浮点型   | 0.0      |
| 字符型   | '\u0000' |
| 布尔型   | false    |
| 引用型   | null     |

数组未初始化时，访问会报NullPointerException错误

数组一旦被创建，长度无法改变

`2`赋值

```java
int[] arr = new int[3];
arr[0] = 1;//赋值
System.out.println(arr[0]);
```



---

**内存的5个部分**

`1`栈（Stack）

存放的是方法中的局部变量

`2`堆（Heap）

凡是new出来的数据都在堆中，且都有一个16进制的地址值，同时所有数据都有一个默认值

`3`方法区（Method Area）

存储.class相关信息，包含方法的信息

`4`本地方法栈（Native Method Stack）

与操作系统相关

`5`寄存器（PC register）

与CPU相关

---

**类与对象**

类是对象的模板，对象是类的实例

```java
import java.util.Arrays;//使用Arrays对象完成对数组的打印

public class HelloWorld {
    public static void main(String args[]) {
        int[] arr = new int[3];
        System.out.println(Arrays.toString(arr));
    }

}
```

`1`创建类

```java
public class student{
    String name;
    int age;
    public  void sayhi(){
        System.out.println("hi , i'm "+this.name);
    }
}
```

创建一个为student.java的文件，包含以上代码

`2`将类实例化

```java
public class HelloWorld {
    public static void main(String args[]) {
        student s=new student();//通过new实例化
        s.sayhi();
    }

}
```

`3`成员变量和局部变量

| 区别     | 成员变量                     | 局部变量                     |
| -------- | ---------------------------- | ---------------------------- |
| 定义位置 | 方法外部                     | 方法内部                     |
| 作用范围 | 类的里全局                   | 仅限于方法内                 |
| 默认值   | 有默认值                     | 无默认值，未赋值不可用       |
| 内存位置 | 堆内存                       | 栈内存                       |
| 生命周期 | 随对象创建而诞生，回收而消失 | 随方法进栈而诞生，出栈而消失 |

`4`private属性

使用private修饰后，超出本类范围将不可被访问。为了访问private修饰的属性，需要定义一对Getter/Setter方法，命名遵循setXxx或getXxx。

对于Getter来说，不能有参数，返回值类型和成员变量对应；

对于Setter来说，不能有参数，参数类型和成员变量对应；

对于基本类型中的boolean，Getter方法命名为isXxx；

`5`this关键字

方法的局部变量和成员变量重名时，根据就近原则会优先使用局部变量。为了对两者进行区分，在使用成员变量时，变量名前加上.this

this的指向：谁调用，this指向谁

`6`构造方法

构造方法的方法名和类名一致，在类被实例化时，会执行构造方法

特点：

1）构造方法无返回值

2）如未编写任何构造方法，编译器在执行时会自动给类加上构造方法。一旦用户添写构造方法，编译器便不会自动添加构造方法

3）构造方法支持重载

`7`标准类（Java Bean）

1）所有成员变量都要使用private关键字修饰

2）每一个成员变量，都有一对Setter/Getter方法

3）有一个无参数的构造方法

4）有一个全参数的构造方法

`8`导包

将类文件导入的过程称为导包

```java
import packagedir.classname//包路径.类名
```

java.lang包下不需要导包，目标类和当前类处于同一包下也不需要导包

`9`Scanner类

```java
import java.util.Scanner;//导包
public class HelloWorld {
    public static void main(String args[]) {
        Scanner sc=new Scanner(System.in);//实例化
        String a=sc.next();//使用
        //sc.next()是输入字符串，sc.nextInt()是输入数字
        System.out.println(a);
    }

}
```

`10`Random类

```java
import java.util.Random;//导包
public class HelloWorld {
    public static void main(String args[]) {
        Random rd=new Random();//实例化
        int a=rd.nextInt(10);
        //这个方法有个可选参数 int boundary，表示随机数的最大值不超过这个数，这里的最大值是9
        System.out.println(a);
    }

}

```

`11`数组列表ArrayList

```java
import java.util.ArrayList;//导包
public class HelloWorld {
    public static void main(String args[]) {
        ArrayList<String> list=new ArrayList<>();//String表示列表里只能是String类型的数据
        /*
        <E>表示泛型，泛型E只能是引用数据类型，不可是基本类型，如果想用基本类型需要使用基本类型的包装类
        */
        list.add("zhang");
        //返回boolean
        list.add("huan");
        for (int i = 0; i < list.size(); i++) {
            //size()返回数组的长度值
            System.out.println(list.get(i));
        }
        list.remove(0);
        //返回删除的数据值
        System.out.println(list);//[huan]
    }

}
```

各基本类型对应的包装类

| 基本类型 | 包装类          |
| -------- | --------------- |
| byte     | Byte            |
| short    | Short           |
| int      | Integer(特殊)   |
| long     | Long            |
| float    | Float           |
| double   | Double          |
| char     | Character(特殊) |
| boolean  | Boolean         |

`12`字符串

1）创建

（1）直接创建：用双引号包裹，java就默认其未字符串

（2）构造方法创建：使用new进行创建

构造方法：

```java
public String();//创建空字符串
public String(char[] array);//字符数组进行创建
public String(byte[] array);//字节数组进行创建
```

原理：

```java
public class HelloWorld {
    public static void main(String args[]) {
        String st1="aaa";
        String st2="aaa";
        char[] charArray={'a','a','a'};
        String st3=new String(charArray);
        System.out.println("st1是否等于st2:");
        System.out.println(st1==st2);
        //true
        //直接创建的字符串都存在对内存的字符串常量池，如果在创建字符串时发现前面已经创建了，将不再重新创建，而是把已经存在的字符串地址赋给变量
        System.out.println("st1是否等于st3:");
        System.out.println(st1==st3);
        //false
        //
    }

}
```

2）equals方法

```java
public class HelloWorld {
    public static void main(String args[]) {
        String st1="aaa";
        String st2="aaa";
        char[] charArray={'a','a','a'};
        String st3=new String(charArray);
        System.out.println(st1.equals(st2));
        System.out.println(st1.equals(st3));
        //只要值相同equals方法返回的结果就是true
    }

}
```

3）字符串常用方法

（1）public int length()字符串长度

（2）public String concat(String str)合并字符串

（3）public char charAt(int index)字符串某位置的字符

（4）public int indexOf(String str)某字符串在该字符串的位置

（5）public String substring(int index)或public String substring(int begin,int end)截取字符串某一段

（6）public String replace(charSequence oldString,charSequence newString)替换

（7）String[] array=str.split(",")以“,”为切割点进行切割，参数是一个正则表达式，切割英文的“.”，需要转译

（8）public char[] toCharArray()或public byte[] toByteArray()转换

`13`static关键字

有此关键字的属性或方法，不需要进行实例化，直接用类名称进行调用

> 注意：
>
> 1.静态方法不能直接访问成员方法或成员变量
>
> 2.静态方法不能使用this关键字
>
> 3.带有static关键字的方法、属性存放在内存的方法区

*静态代码块*

```java
static{
	System.out.println("静态代码块");
}
//当第一次使用到本类时，静态代码块优先于其他方法执行一次，以后再使用本类将不再执行。主要用于一次性对静态变量赋值。
```

`14`Arrays类

主要用它的静态方法

1)public static String toString(数组)

```java
int[] arr={1,4,22,5,13,2};
String arr_to_string=Arrays.toString(arr);//将转换成字符串
System.out.println(arr_to_string);//[1, 4, 22, 5, 13, 2]
```

2)public static void sort(数组)

```java
int[] arr={1,4,22,5,13,2};
Arrays.sort(arr);//将数组排序
String arr_to_string1=Arrays.toString(arr);
System.out.println(arr_to_string1);//[1, 2, 4, 5, 13, 22]
/*
1.如果是数值，按照从小到大顺序排列
2.如果是字符串，按照字母升序排列
3.如果是自定义类型，需有Comparable或Comparator接口支持
*/
```

`15`Math类

```java
//public static double abs(double num)绝对值
System.out.println(Math.abs(-5));

//public static double ceil(double num)向上取整
System.out.println(Math.ceil(5.8));

//public static double floor(double num)向下取整
System.out.println(Math.floor(5.8));

//public static longh round(double num)四舍五入
System.out.println(Math.round(5.8));
```

`16`继承

主要解决的问题是共性抽取，其中被继承的类称为父类（基类、超类），进行继承操作的类被称为子类（派生类），子类可拥有父类的属性、方法，子类也可以有自己独有的属性、方法。

java语言是单继承的，但可多级继承。即一个子类的直接父类是唯一的，但父类可有很多子类。

```java
//定义父类
public class People {
    String name;
    int age;
    public People(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

```java
//定义子类
public class Student extends People {//通过extends关键字实现继承操作
    //此时该类拥有People类的属性、方法，但不拥有父类的构造方法，若要使用父类构造方法，需定义自己的构造方法，并使用super关键字进行调用
    public Student(String name, int age) {
        super(name, age);//在自己构造方法中通过super关键字调用父类的构造方法
    }
}
```

1）父类和子类成员变量重名问题

在继承关系中可能出现父类和子类的属性或方法重名，默认情况下，有两种访问方式

(1)通过子类对象直接访问属性

就近原则，先从子类找，如果子类没有，便到上一级父类查找，一旦找到便返回（方法也如此）

(2)通过子类对象的成员方法间接访问属性

该成员方法属于谁，就优先访问谁的属性

2）变量

| 访问方式  | 变量类型       |
| --------- | -------------- |
| num       | 局部变量       |
| this.num  | 本类的成员变量 |
| super.num | 父类的成员变量 |

3）重写

(1)重载和重写区别

| 名称           | 特点                   |
| -------------- | ---------------------- |
| 重载(Overload) | 方法名一样，参数不一样 |
| 重写(Override) | 方法名一样，参数也相同 |

(2)重写注意事项

【1】子类方法返回值范围小于父类方法

【2】子类方法的权限修饰符必须大于等于父类方法的权限修饰符，如父类是protected，子类只能是public

public>protected>default>private

【3】方法重写时，应该在重写的方法上加@Override进行验证

4）super关键字解决父子类共性问题

对于已投入使用的类，尽量不要进行修改，推荐定义一个新的类，来重复利用其中的共性内容，并添加新的内容

```java
//父类
public class Phone{
    public void show(){
        System.out.println("父类的show方法");
    }
}
```

```java
//子类
public class NewPhone extends Phone{
    public void show(){
        super.show();//将父类方法重用
        System.out.println("子类扩展的内容");
    }
}
```

5）父、子类构造方法访问特点

(1)子类构造方法中有一个默认隐含的super()调用，所以一定是先调用父类的构造方法，后执行子类的构造方法

(2)子类通过super()调用父类的重载方法，实现重载

(3)super()必须是子类构造方法的第一个语句，而且只能是唯一一个

(4)构造方法可不写，编译器会工作时为自动为这个类添加上构造方法；如果写了，编译器将不再给这个类添加构造方法

6）super关键字和this关键字总结

| super                          | this                                   |
| ------------------------------ | -------------------------------------- |
| 子类成员方法中访问父类成员变量 | 本类成员方法中访问本类成员变量         |
| 子类成员方法中访问父类成员方法 | 本类成员方法中访问本类另一个成员法方法 |
| 子类构造方法中访问父类构造方法 | 本类构造方法中访问本类另一个构造方法   |

在本类的构造方法中使用this(…)访问另一个构造方法时，this(…)必须时构造方法的第一个语句，也只能使用一次。由此可知，super(…)和this(…)不可以同时使用。

`17`抽象方法和抽象类

有抽象方法的类就是且只能是抽象类，抽象类中不一定有抽象方法

```java
//无抽象方法的抽象类
public abstract class Amimal {
}
```

```java
//有抽象方法的抽象类
public abstract class Amimal {
    public abstract void eat();//后序继承的类，想要实例化，必须实现该抽象方法
}
```

抽象方法的实现，其实就是将抽象类中的抽象方法进行覆盖重写（子类去掉abstract，补上方法体大括号）

总结：

(1)抽象类不能实例化

(2)抽象类可以有构造方法，子类实例化时会调用

(3)抽象类中不一定有抽象方法

(4)抽象类的字类，如果想实例化，必须重写父类所有的抽象方法

`18`接口

一种引用的数据类型，最重要的就是其中的抽象方法。抽象类其实是定义了一种类的规范，是一种对继承类的约束。

```java
public interface Graph {
    int num=10;//定义接口中的常量
    public abstract void CalcMianji();//public abstract可省略，因为接口中所有方法都是抽象方法
    void CalcZhouchang();
}
```

1）使用接口的注意事项

(1)接口不能直接使用，必须有一个实现类来实现接口

(2)接口的实现类必须实现接口中所有的抽象方法

(3)未完全实现接口中所有抽象方法的类是抽象类

2）各版本对接口的支持

| JAVA7    | JAVA8    | JAVA9    |
| -------- | -------- | -------- |
| 常量     | 常量     | 常量     |
| 抽象方法 | 抽象方法 | 抽象方法 |
|          | 默认方法 | 默认方法 |
|          | 静态方法 | 静态方法 |
|          |          | 私有方法 |

3）默认方法

在之前定义好的接口上扩展新的方法，为保证之前的实现类正常使用，该方法需定义成默认方法。

可通过实现类对象直接调用，也可以被接口实现类覆盖重写。

```java
//定义图形接口
public interface Graph {
    int num=10;
    public abstract void CalcMianji();
    void CalcZhouchang();
    default void gogogo(){//定义的默认方法
        System.out.println("接口中新增的默认方法");
    }
}
```

```java
//定义三角形实现图形接口
public class Triangle implements Graph {
    @Override
    public void CalcMianji() {
        System.out.println("三角形计算面积");
    }

    @Override
    public void CalcZhouchang() {
        System.out.println("三角形计算周长");
    }
}
```

```java
//入口类
public class Hello {
    public static void main(String[] args) {
        Triangle t=new Triangle();
        t.gogogo();//调用默认方法
    }
}
```

4）静态方法

带有static关键字，且有方法体的方法。通过接口名，直接调用静态方法。

```java
//定义图形接口
public interface Graph {
    int num=10;
    public abstract void CalcMianji();
    void CalcZhouchang();
    static void dododo(){//定义静态方法
        System.out.println("接口中新增的静态方法");
    }
}
```

```java
//入口类
public class Hello {
    public static void main(String[] argss) {
        Graph.dododo();//直接通过接口名调用
        //注意：不可通过实例化的对象调用，会报错
    }
}
```

5）私有方法

我们需要抽取一个共有方法，来解决两个默认或静态方法之间的重复代码问题，但共有方法不应让实现类使用，应该私有，所以应该定义私有方法。

```
private 返回值类型 方法名称 (参数列表){
	//方法体
}
```

```
private static 返回值类型 方法名称 (参数列表){
	//方法体
}
```

6）接口中定义常量

```java
public interface Graph {
    public static final int NUM_1=10;//其中public static final可省略
    int NUM_2=20;
}
```

> 注意:
>
> 接口中的常量，必须赋值，常量名称全部大写并用"_"进行分割

7）接口相关注意事项

(1)接口中不能有静态代码块，不能有构造方法

(2)类的直接父类唯一，但可实现多个接口

(3)实现类实现了多个接口，接口之间定义的默认方法发生冲突，该实现类必须重写默认方法

(4)父类方法和接口的默认方法冲突，优先用父类的方法

(5)实现类实现了多个接口，多个接口的抽象方法冲突，没关系

`18`多态

父类引用指向子类对象

```
父类名称 对象名=new 子类名称();
接口名称 对象名=new 实现类名称();
```

直接通过对象名称访问成员变量：看等号左边是谁就优先用谁

简介通过成员方法向上查找：该方法属于谁就优先用谁，没有向上找

小结：

成员变量--编译看左边，运行还看左边

成员方法--编译看左边，运行看右边

```java
//Graph接口
public interface Graph {
    int num1=10;
    void CalcMianji();
    void CalcZhouchang();
    void GetNum1();

}
```

```java
//定义Triangle类实现Graph接口
public class Triangle implements Graph {
    int num1=100000;
    @Override
    public void CalcMianji() {
        System.out.println("三角形计算面积");
    }

    @Override
    public void CalcZhouchang() {
        System.out.println("三角形计算周长");
    }

    @Override
    public void GetNum1() {
        System.out.println(num1);
    }
}
```

```java
//入口类
public class Hello {
    public static void main(String[] args) {
        Graph t=new Triangle();

        //直接通过对象名访问成员变量
        System.out.println(t.num1);//输出Graph的num1 10

        //间接通过成员方法访问成员变量
        t.GetNum1();//输出Triangle的num1 100000
    }
}
```

1）对象向上转型

其实是多态写法

```
父类名称 对象名=new 子类名称();
```

创建子类对象，把它当作父类使用，一定是安全的，小范围转向大范围

2）对象向下转型

其实是一个还原动作，将大范围还原成小范围

```
子类名称 对象名=(子类名称) 父类对象；
```

将父类对象还原成子类对象

3）判断对象是否是某个类的实例

```
对象 incetanceof 类名称
```

该语句返回布尔值

4）final关键字

(1)修饰类

```
public final class 类名称{//当前这个类不能有子类
}
```

(2)修饰方法

```
public final void method(){//该方法不能被覆盖重写
}
```

abstract修饰方法表示此方法必须要在实现类里进行重写，而final修饰方法表示该方法不能被重写，两者在理论上矛盾，因此abstract和final同时修饰一个方法

(3)修饰局部变量

```java
//基本类型变量
final int num=100;//对于num里的值，一次赋值，终身不变

//引用类型变量
final String str=new String("abc");
/*
str=new String("def");
不可以再赋值，final表示str对应的地址值不变
*/
```

(4)修饰成员变量

一般成员变量可以不赋值，编译器会给一个默认值。但是用final修饰后，要求成员变量必须要赋值，有两种赋值方式：

【1】声明的时候，直接赋值

```java
public class Triangle{
    final int num1=222;
}
```

【2】声明时暂时不赋值，但在构造方法里进行赋值

```java
public class Triangle{
    final int num1;
    public Triangle() {
        num1=222;
    }
}
```

final修饰后的成员变量一旦被赋值，将不再改变

`19`权限修饰符

|                | public | protected | (default) | private  |
| -------------- | ------ | --------- | --------- | -------- |
| 同一类里       |        |           |           |          |
| 同包类实例     |        |           |           | 不可访问 |
| 不同包子类     |        |           | 不可访问  | 不可访问 |
| 不同包子类实例 |        | 不可访问  | 不可访问  | 不可访问 |

`20`内部类

一个类里包含另一个类

1）成员内部类

把这个内部类看作是外部类的成员变量

```java
//定义成员内部类
public class Body {
    //相当于创建一个成员变量，而这个成员变量指向一个类
    public class Heart{
        public void work(){
            System.out.println("心脏跳动，咚咚……");
        }
    }
    public Heart create_heart(){//实例化内部类
        return new Heart();
    }
}
```

```java
//入口类
public class Hello {
    public static void main(String[] args) {
        //间接方式
        //通过外部类的成员方法实例化内部类，调用work()
        Body b=new Body();
        b.create_heart().work();

        //直接方式
        //通过new Body().new Heart()创建内部类实例，调用work()
        new Body().new Heart().work();

    }
}
```

2）局部内部类

一个定义在成员方法里的类，把这个内部类看作是这个方法里的局部变量

```java
//定义局部内部类
public class Body {

    public void create_heart(){//实例化内部类
        class Heart{//局部类不可有任何修饰符
            public void work(){
                System.out.println("心脏跳动，咚咚……");
            }
        }
        new Heart().work();//实例化，并调用其内部成员方法
    }
}
```

局部内部类访问的局部变量不可变问题

局部类访问了该方法里的局部变量，要求该局部变量不可变。从Java8开始，只要局部变量事实不变，final关键字可省略

```java
public class Body {

    public void create_heart(){
        int num=1;//因为内部类访问了该局部变量，所以该局部变量相当于加了final关键字
        //num++;//不可有这一句，会报错
        class Heart{
            public void work(){
                //局部类访问局部变量，要求改局部变量事实不变
                System.out.println(num);
            }
        }
        new Heart().work();
    }
}
```

3）匿名内部类

直接new接口，跟上大括号，同时在大括号里实现接口里的抽象方法，这个大括号及大括号里的内容称为匿名内部类。此过程省略了类的名称，注意和匿名对象区分（省略了对象的命名）

```java
public static void main(String[] args) {
        new Graph(){
            @Override
            public void CalcMianji() {
            }

            @Override
            public void CalcZhouchang() {
            }

            @Override
            public void GetNum1() {
                System.out.println(num1);
            }
        }.GetNum1();
```

4）不同类可用的权限修饰符

| 类名称     | 可用的权限修饰符                   |
| ---------- | ---------------------------------- |
| 外部类     | public/(default)                   |
| 成员内部类 | public/protected/(default)/private |
| 局部内部类 | 无                                 |

`21`equals方法

源码

```java
public boolean equals(Object obj){
	return (this==obj);
}
```

对于基础数据类型，equals比较的是变量的值；对于引用数据类型，equals比较的是变量的存放的地址值

```java
//注意：String类型，equals方法比较的是值
String str1=new String("abc");
String str2=new String("abc");
System.out.println(str1.equals(str2));//true
```

对于自定义的对象，如果要根据两个对象的成员变量来作为判定两对象相等的条件，需要重写equals方法

通过Objects的静态equals方法判断两对象是否相等

Objects的静态equals方法源码

```java
public static boolean equals(Object a,Object b){
	return (a==b)||(a!=null&&a.equals(b));
}
//防止了a和b中任一为null导致报错
```

`22`时间相关的类

1）Date类

```java
import java.util.Date;//导包
public class Hello {
    public static void main(String[] args) {
        Date dt=new Date();
        //可精确到毫秒，对时间日期进行计算
        System.out.println(dt);//Thu Mar 19 22:13:42 CST 2020
        System.out.println(System.currentTimeMillis());
        //获取从1970.1.1到现在经历了多少毫秒
    }
}
```

Date类带参数的构造方法

```java
Date(long date);//传入毫秒值，会将毫秒值转换成Date日期
```

Date类将日期转换成毫秒值函数

```java
getTime();
```

2）抽象类DateFormat及其实现类SimpleDateFormat

(1)抽象类DateFormat的方法

```java
//源码摘取
abstract class DateFormat{
	public final String format(Date date)//将日期对象格式化成字符串
    {
        return format(date, new StringBuffer(),
        DontCareFieldPosition.INSTANCE).toString();
    }
    public Date parse(String source) throws ParseException//将格式化的字符串转换成日期
    {
        ParsePosition pos = new ParsePosition(0);
        Date result = parse(source, pos);
        if (pos.index == 0)
            throw new ParseException("Unparseable date: \"" + source + "\"" ,
                pos.errorIndex);
        return result;
    }
}
```

(2)实现类SimpleDateFormat

```java
import java.text.ParseException;//将异常的类导入
import java.text.SimpleDateFormat;
import java.util.Date;

public class Hello {
    public static void main(String[] args) throws ParseException {//因为parse方法会抛错，所以这里要将抛错的语句写上
        Date dt=new Date(System.currentTimeMillis());
        
        SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        
        //将日期对象格式化成字符串
        System.out.println(sdf.format(dt));//2020-03-19 22:46:09      
        //将格式化的字符串转换成日期
        Date dt1=sdf.parse("2020-03-19 22:42:37");
        System.out.println(dt1);//Thu Mar 19 22:42:37 CST 2020
    }
}
```

3）Callendar抽象类

提供很多操作日历的方法，YEAR,MONTH,DAY_OF_MONTH等

由于Callendar是抽象类，无法进行实例化，但它有个静态方法可以直接实例化出子类对象

```java
import java.util.Calendar;

public class Hello {
    public static void main(String[] args) {
        Calendar cld=Calendar.getInstance();//通过此方法直接实例化出一个子类对象
    }
}
```

(1)获取和设置

```java
import java.util.Calendar;

public class Hello {
    public static void main(String[] args) {
        Calendar cld=Calendar.getInstance();
        /*获取当前日期
        * public int get(int field)
        */
        int year=cld.get(Calendar.YEAR);
        System.out.println(year);
        /*设置日期
        * public void set(int field,int value)
        */
        cld.set(Calendar.YEAR,2021);
        System.out.println(cld.get(Calendar.YEAR));
        
        //设置日期的重载方法
        cld.set(2016,0,25);
        System.out.println(cld.get(Calendar.YEAR));
    }
}
```

(2)日期增减

```java
import java.util.Calendar;

public class Hello {
    public static void main(String[] args) {
        Calendar cld=Calendar.getInstance();
        /*
        public absract void add(int field,int amount)
        */

        //增加日期
        cld.add(Calendar.YEAR,1);
        System.out.println(cld.get(Calendar.YEAR));//2021

        //减少日期
        cld.add(Calendar.YEAR,-2);
        System.out.println(cld.get(Calendar.YEAR));//2019
    }
}
```

(3)返回一个表示此日期的Date对象

```java
import java.util.Calendar;

public class Hello {
    public static void main(String[] args) {
        Calendar cld=Calendar.getInstance();
        /*
        public Date getTime()
         */
        System.out.println(cld.getTime());//Thu Mar 19 23:18:32 CST 2020
    }
}
```

`23`System类

```java
import java.util.Arrays;

public class Hello {
    public static void main(String[] args) {
        /*
        public static native long currentTimeMillis();
        以毫秒数返回当前时间，一般用来测试程序执行时间
         */
        System.out.println(System.currentTimeMillis());//1584631889876

        /*
        public static native void arraycopy(Object src,int  srcPos,Object dest, int destPos,int length);
        在两个数组之间进行元素复制粘贴
        参数：源数组，起始位置，目标数组，起始位置，要赋值的元素个数
         */
        int[] ar1={1,2,3,4,5};
        int[] ar2={6,7,8,9,10};
        System.arraycopy(ar1,0,ar2,3,2);
        //相当于将ar1的前两个元素，复制粘贴到ar2的最后两个元素的位置
        System.out.println(Arrays.toString(ar1));
        System.out.println(Arrays.toString(ar2));
    }
}
```

`24`String类和StringBuilder类

| String类                                                     | StringBuilder类                             |
| ------------------------------------------------------------ | ------------------------------------------- |
| 底层是一个被final修饰的数组，不可改变长度                    | 无final修饰，可改变长度                     |
| 字符串连接会导致内存里会有多个字符串，导致空间占用多，效率低下 | 通过append(…)进行字符串来连接，实现链式编程 |

```java
public class Hello {
    public static void main(String[] args) {
        StringBuilder str=new StringBuilder("abc");
        str.append("def").append("def").append("def");
        char[] c={'h','i'};
        str.append(c);
        System.out.println(str);//abcdefdefdefhi
    }
}
```

String类和StringBuilder类构造方法类似，两者可相互转化

```java
public class Hello {
    public static void main(String[] args) {
        String str="abc";
        StringBuilder strb=new StringBuilder(str);//通过构造方法，将String转换成StringBuilder
        System.out.println(strb.toString());//通过成员方法toString()，将StringBuilder转换成String
    }
}
```

`25`包装类

基本数据类型是一个精简后的类，如果要让其拥有一个类的完整的属性、方法，需要使用其完整功能的类，称为包装类。将基本数据类型转换成包装类过程称为装箱，将包装类转换成基本数据类型的过程称为拆箱。

```java
import java.util.ArrayList;

public class Hello {
    public static void main(String[] args) {
        //装箱
        Integer in=new Integer(3);//参数可为数字或数字字符串
        //拆箱
        int i=in.intValue();

        //自动装拆
        //自动装箱1
        Integer i1=20;//将int数据20自动装箱成Integer数据存放在i1中
        //自动装箱2
        ArrayList<Integer> list=new ArrayList<>();
        list.add(1);//这里将int数据1，自动装箱成Integer数据1
        //自动拆箱
        int a=list.get(0).intValue();//这里也可这样写int a=list.get(0).intValue()由于自动拆箱，这里省去拆箱过程

    }
}
```

`26`基本类型数据和字符串的相互转化

1）基本类型转换成字符串

(1)基本类型+“”

(2)包装类的静态方法toString(参数)

(3)String类的静态方法Valueof(参数)

```java
public class Hello {
    public static void main(String[] args) {
        int i=50;
        
        //基本类型+“”
        String str1=i+"";
        System.out.println(str1+20);//5020
        
        //包装类的静态方法toString(参数)
        String str2=Integer.toString(i);
        System.out.println(str2+20);//5020
        
        //String类的静态方法Valueof(参数)
        String str3=String.valueOf(i);
        System.out.println(str2+20);//5020
    }
}
```

2）基本类型转换成字符串

Integer类的parseInt(String s)

```java
public class Hello {
    public static void main(String[] args) {
        String str="500";
        int i=Integer.parseInt(str);
        System.out.println(i+20);//520
    }
}
```

`27`Collection接口

Collection接口有两个子接口，分别是List接口和Set接口

1）List接口

该接口定义了一类集合有以下特点：

(1)有序的集合

(2)允许存储重复元素

(3)有索引，可以使用普通的for循环遍历

包含的实现类：

Vector

```java
//实现可增长的对象数组，单线程，已被取代
```

ArrayList

```java
//底层是一个数据结构，查询快,增删慢
//无特有方法
```

Linklist

```java
//底层是一个链表结构：查询慢，增删快
//里面包含大量操作首尾元素的方法
//里面有特有方法，故不可用多态
public void addFirst(E e);//将指定元素插入此列表的开头
public void addLast(E e);//将指定元素插入此列表的结尾
public E getFirst();//返回此列表第一个元素
public E getLast();//返回此列表最后一个元素
public E removeFirst();//返回此列表第一个元素
public E removeLast();//返回此列表最后一个元素
public E pop();//从此列表表示的堆栈中弹出一个元素
public E push(E e);//从此列表表示的堆栈中压入一个元素
```

2）Set接口

该接口定义了一类集合有以下特点：

(1)无序的集合

(2)不允许存储重复元素

(3)无索引，不可以使用普通的for循环遍历

*哈希值*

一个由系统计算出来唯一的整数值，每个对象都有一个哈希值，可通过该对象的hashCode()调用

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("重地".hashCode());//1179395
        System.out.println("通话".hashCode());//1179395
    }
}
//特例，字符串值不同，hash值一样
```

*哈希表*

一个由hash值和数据一一对应的表

在java1.8前，哈希表由数组和链表构成。1.8之后，链表长度小于8，则由数组和链表构成，否则由数组和红黑树构成

包含的实现类：

TreeSet

TreeSet是SortedSet接口的唯一实现类，TreeSet可以确保集合元素处于排序状态，不允许放入null值，以下是存放一个自定义类的案例

```java
//自定义一个类，需要实现 Comparable接口
public class Person implements Comparable<Person>{
    String name;
    int age;

    public Person() {
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public int compareTo(Person o) {
        return this.getAge()-o.getAge();
    }
}
```

```java
//入口文件
import java.util.*;

public class Hello {
    public static void main(String[] args) {
        TreeSet<Person> set1=new TreeSet<>();
        set1.add(new Person("zhanghuan",20));
        set1.add(new Person("zhanghuan5",25));
        set1.add(new Person("zhanghuan2",22));
        set1.add(new Person("zhanghuan4",24));
        set1.add(new Person("zhanghuan3",23));
        System.out.println(set1);
        //[Person{name='zhanghuan', age=20}, Person{name='zhanghuan2', age=22}, Person{name='zhanghuan3', age=23}, Person{name='zhanghuan4', age=24}, Person{name='zhanghuan5', age=25}]
    }
}
```

HashSet

*add方法执行原理*

调用该方法时，会先执行传入元素的hashCode方法，看是否与集合中元素冲突。如果不冲突，将该元素加入。如果冲突，继续调用equals方法进行比较，如果返回为false，将该元素加入。如果返回为true，则该元素抛弃掉。以下是存放一个自定义类的案例

```java
//定义自定义类，需要重写hashCode方法和equals方法
import java.util.Objects;

public class Person{
    String name;
    int age;

    public Person() {
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age &&
                Objects.equals(name, person.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}
```

```java
//入口文件
import java.util.*;

public class Hello {
    public static void main(String[] args) {
        HashSet<Person> set=new HashSet<>();
        set.add(new Person("zhanghuan",25));
        set.add(new Person("zhanghuan",25));
        set.add(new Person("zhanghuan",25));
        set.add(new Person("zhanghuan",26));
        System.out.println(set);
        //[Person{name='zhanghuan', age=26}, Person{name='zhanghuan', age=25}]
    }
}
```

LinkedHashSet（有序集合）

```java
//继承自HashSet,存入的数据有序(区别于HashSet存入数据无序)
```

3）Collection的共性方法

public boolean add(E e)

public void add(int index, E element)（列表）

public E set(int index, E element)（列表）

public void clear()

public boolean remove(int  index)（列表）

public boolean contains(E e)

public boolean isEmpty()

public int size()

public Object[] toArray()

```java
import java.util.ArrayList;
import java.util.Arrays;

public class Hello {
    public static void main(String[] args) {
        ArrayList arr=new ArrayList();
        arr.add(2);
        arr.add(3);
        arr.add(4);
        arr.add(5);
        System.out.println(arr);//[2, 3, 4, 5]
        
        arr.remove(0);
        System.out.println(arr);//[3, 4, 5]
        
        System.out.println(arr.contains(2));//false
        
        System.out.println(arr.size());//3
        
        Object[] arr2=arr.toArray();
        System.out.println(Arrays.toString(arr2));//[3, 4, 5]
        
        arr.clear();
        System.out.println(arr.isEmpty());//true
    }
}
```

`28`迭代器

Iterator是一个位于java.util.Iterator的接口，因此无法直接实例化使用。但所有的collection接口的实现类都有一个方法iterator()，调用此方法将得到一个迭代器的实现类对象。

1)迭代器的使用

```java
import java.util.Iterator;

public class Hello {
    public static void main(String[] args) {
        ArrayList arr=new ArrayList();
        arr.add(2);
        arr.add(3);
        arr.add(4);
        arr.add(5);
        Iterator it=arr.iterator();//通过多态方式将Iterator子类对象赋值给变量it
        while (it.hasNext()){//hasNext()判断迭代器里是否还有下一个元素
            //next(E e)取出下一元素，如果没有则报错
            System.out.println(it.next());
            /*输出的结果
            2
            3
            4
            5
             */
            /*
            原理：未执行next()前，指针指向集合的-1位置，每执行一次next(),指针后移一位并取出指针对应的元素
             */
        }
    }
}
```

2）迭代器的推广，增强for循环

```java
import java.util.ArrayList;

public class Hello {
    public static void main(String[] args) {
        ArrayList arr=new ArrayList();//定义一个集合
        arr.add(2);
        arr.add(3);
        arr.add(4);
        arr.add(5);
        for (Object i:arr) {
            System.out.println(i);
        }
        //该for循环的内部原理是迭代器，使用该for循环以简化迭代器书写，可遍历集合和数组
    }
}
```

`29`泛型

一种未知的数据类型，泛型可看作一个变量，用来接收临时传入的数据类型

1）泛型接口和其实现类

```java
//定义泛型接口
public interface GenericInterface<I> {
    public abstract I method(I i);
}
```

```java
//第一种实现类，在实现时确定泛型的数据类型
public class Generic1 implements GenericInterface<String> {
    @Override
    public String method(String s) {
        return s;
    }
}
```

```java
//第二种实现类，在实现时保留泛型
public class Generic2<I> implements GenericInterface<I> {
    @Override
    public I method(I i) {
        return i;
    }
}
```

```java
public class Hello {
    public static void main(String[] args) {
        //实例化第一种实现类
        GenericInterface<String> s1=new Generic1();
        System.out.println(s1.method("50")+20);//5020
		
        //实例化第二种实现类
        GenericInterface<Integer> s2=new Generic2();//泛型定义为Integer
        System.out.println(s2.method(50)+20);//70

        GenericInterface<String> s3=new Generic2();//泛型定义为String
        System.out.println(s3.method("50")+20);//5020
    }
}
```

2）定义包含泛型方法的类

```java
public class Generic {
    //含有泛型的成员方法
    public <M>M method(M m){
        return m;
    }
    //含有泛型的静态方法
    public static  <M>M method1(M m){
        return m;
    }
}
```

```java
public class Hello {
    public static void main(String[] args) {
        Generic g=new Generic();
        System.out.println(g.method(50)+20);//70
        System.out.println(g.method("50")+20);//5020

        System.out.println(Generic.method1(50)+20);//70
        System.out.println(Generic.method1("50")+20);//5020
    }
}
```

3）泛型的通配符"?"

应用场景：定义一个能遍历Arraylist<泛型>的方法，此时不知道Arraylist对象里的数据类型，可用通配符"?"来接收数据类型

```java
import java.util.ArrayList;
import java.util.Iterator;

public class Hello {
    public static void main(String[] args) {
        ArrayList<Integer> arr1=new ArrayList<>();
        arr1.add(1);
        arr1.add(2);
        arr1.add(3);
        ArrayList<String> arr2=new ArrayList<>();
        arr2.add("A");
        arr2.add("B");
        arr2.add("C");
        printArray(arr1);
        printArray(arr2);
    }
    public static void printArray(ArrayList<?> arr){
        Iterator<?> it=arr.iterator();
        while (it.hasNext()){
            System.out.println(it.next());
        }
    }
}
```

4）泛型上限、下限限定

```java
public class Hello {
    public static void main(String[] args) {
        Collection<Integer> list1=new ArrayList<>();
        Collection<String> list2=new ArrayList<>();
        Collection<Number> list3=new ArrayList<>();
        Collection<Object> list4=new ArrayList<>();

        getElemnt1(list1);//list1泛型对应的数据类型是Integer，是Number的子类
        getElemnt2(list1);//list1泛型对应的数据类型是Integer，不是Number的父类，故报错
    }
    
    //接收的参数对象的泛型类型是Number及其子类
    public  static  void getElemnt1(Collection<?extends Number> c){}

    //接收的参数对象的泛型类型是Number及其父类
    public  static  void getElemnt2(Collection<?super Number> c){}
    
}
```

 `30`集合相关的静态方法

1）public static void shuffle(List<?> list)

```java
//核心代码
ArrayList ar=new ArrayList();
ar.add(1);
Collections.addAll(ar,2,3,4,5);
Collections.shuffle(ar);//打乱顺序
System.out.println(ar);//[2, 4, 5, 1, 3]
```

2）public static <T> boolean addAll(Collection<? super T> c, T... elements)

```java
//核心代码
ArrayList ar=new ArrayList();
ar.add(1);
Collections.addAll(ar,2,3,4,5);//批量添加
System.out.println(ar);//[1, 2, 3, 4, 5]
```

3）public static <T extends Comparable<? super T>> void sort(List<T> list)

```java
//核心代码
ArrayList ar=new ArrayList();
ar.add(1);
Collections.addAll(ar,2,3,4,5);
Collections.shuffle(ar);//打乱顺序
Collections.sort(ar);//重写排序
System.out.println(ar);//[1, 2, 3, 4, 5]
```

*排序自定义类*

方法一：将自定义类实现 Comparable接口

方法二：public static <T> void sort(List<T> list, Comparator<? super T> c) 

```java
import java.util.*;

public class Hello {
    public static void main(String[] args) {
        ArrayList ar=new ArrayList();
        ar.add(new Person("zhanghuan1",21));
        ar.add(new Person("ahanghuan2",21));//第一个字是a，排在第一
        ar.add(new Person("zhanghuan3",23));
        ar.add(new Person("zhanghuan4",24));
        ar.add(new Person("zhanghuan5",25));
        Collections.shuffle(ar);
        
        //第一个参数是集合对象，第二个是匿名Comparator对象
        Collections.sort(ar, new Comparator<Person>() {
            @Override
            public int compare(Person o1, Person o2) {
                //年龄不同按年龄排序，年龄相同的情况下按姓名的第一个字排序
                int result=o1.getAge()-o2.getAge();
                if(result==0){
                    result=o1.getName().charAt(0)-o2.getName().charAt(0);
                }
                return result;
            }
        });
        System.out.println(ar);
    }


}
```

`31`Map<K,V>接口的实现类

1）HashMap类

底层是hash表，查询速度快，是一个无序集合，存储元素的顺序与遍历后的顺序不一致

2）LinkedHashMap类

底层是hash表，查询速度快，是一个有序集合，存储元素的顺序与遍历后的顺序一致

3）HashTable类

线程安全，不能存储null值。和Vector一样，在jdk1.2版本被取代。但HashTable的子类Properties仍然活跃在历史舞台

4）常用方法

```java
import java.util.*;

public class Hello {
    public static void main(String[] args) {
        HashMap<String,String> map=new HashMap<>();
        
        //public V put(K key, V value)
        //往HashMap实现类中存放数据，也可以用该方法进行修改
        map.put("a","1");
        map.put("b","2");
        map.put("c","3");
        map.put("d","4");
        map.put("a","1111");//修改
        System.out.println(map);//{a=1111, b=2, c=3, d=4}
        
        //public V get(Object key)
        //获取该键对应的值
        System.out.println(map.get("a"));//1111
        
        //public V remove(Object key)
        //删除键值对
        map.remove("b");
        System.out.println(map);//{a=1111, c=3, d=4}
        
        //public boolean containsKey(Object key)
        //判断是否包含某个键
        System.out.println(map.containsKey("c"));//true

    }
    
}
```

5）HashMap的computeIfAbsent方法

```java
//省略部分代码
    @Test
    public void test2(){
        Map<String,String> map=new HashMap<>();
        //判断name是否存在，如果不存在设置为zhanghuan,如果存在就什么都不做
        map.computeIfAbsent("name",s->"zhanghaun");//第二个参数是lambda表达式
        map.computeIfAbsent("name",s->"lili");
        System.out.println(map);
    }
```

6）遍历

```java
import java.util.*;

public class Hello {
    public static void main(String[] args) {
        HashMap<String,Integer> map=new HashMap<>();
        map.put("a",1);
        map.put("b",2);
        map.put("c",3);
        map.put("d",4);

        //通过获取键集合，遍历HashMap
        Set keys=map.keySet();
        for (Object key:
             keys) {
            System.out.println(key+"="+map.get(key));
        }

        //通过图的内置entrySet对象，遍历HashMap里的元素
        Set<Map.Entry<String,Integer>> set=map.entrySet();
        for (Map.Entry<String,Integer> i:
                set ) {
            System.out.println(i.getKey()+"="+i.getValue());
        }
    }

}
```

7）接口的of方法

JDK9中，List接口，Set接口，Map接口，有个静态方法of，可给集合一次性添加多个元素；要求集合中存储的元素个数已确定，不再改变。

```java
import java.util.*;

public class Hello {
    public static void main(String[] args) {
        List<Integer> list=List.of(1,2,3,4);
        System.out.println(list.getClass());//存放数据的集合类名class java.util.ImmutableCollections$ListN
        System.out.println(list);//[1, 2, 3, 4]
    }

}
```

> 注意
>
> 1.of方法只适用于List接口，Set接口，Map接口，不适用于接口的实现类
>
> 2.of方法返回值是一个不能改变的集合，不能用add，put方法
>
> 3.Set接口和Map接口在调用of方法时，不能有重复元素，否则会运行时报错

---

**Debug调试程序**

可让代码顺序执行，查看执行过程

行号上左键点击，添加断点

| 快捷键   | 功能           |
| -------- | -------------- |
| F8       | 运行执行       |
| F7       | 进入到方法中   |
| Shift+F8 | 跳出此方法     |
| F9       | 跳到下一个端点 |
| Ctrl+F2  | 退出           |

---

**Throwable超类**

位于jva.lang.Throwable，有三个子类Error（必须修改源代码）、Exception（编译器异常）、RuntimeException（运行期异常）

`1`处理异常的两种方式

1）外部方法名后加throws 异常名

```java
import java.util.*;
import java.text.SimpleDateFormat;
import java.text.ParseException;

public class Hello {
    public static void main(String[] args) throws ParseException{//处理方法类名后加throws ParseException
        SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date dt1=sdf.parse("2020-03-19 22:42:37");//可能抛出错误
        System.out.println(dt1);//Thu Mar 19 22:42:37 CST 2020
    }

}
//出现异常后，后面代码将不再执行
```

2）使用try…catch…

```java
import java.util.*;
import java.text.SimpleDateFormat;
import java.text.ParseException;

public class Hello {
    public static void main(String[] args){//处理方法类名后加throws ParseException
        SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date dt1=new Date();
        try{
            dt1=sdf.parse("2020-03-19 22:42:37");//可能抛出错误
        }catch (ParseException err){
            System.out.println(err);
            //return 使用写上后将停止执行后面的代码
        }
        System.out.println(dt1);//Thu Mar 19 22:42:37 CST 2020
    }

}
//出现异常后，后面代码将继续执行
/*注意 
try…catch…，可以有多个catch来捕获异常，但如果异常对象之间存在父子关系，子对象的捕获需写在前面
*/
```

`2`错误案例

```java
public class Hello {
    public static void main(String[] args){
        int[] ar=new int[1024*1024*1024];
        /*内存溢出错误
        Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
	    at aaa.Hello.main(Hello.java:10)
         */
    }
}
```

`3`抛错案例及流程解析

```java
public class Hello {
    public static void main(String[] args){
        int[] ar={1,2,3};
        getEle(ar,3);//Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: 3
    }
    public static int getEle(int[] arr,int index){
        return arr[index];
    }
}
/*
案例访问了数组的索引3，而数组没有索引3，jvm就会检测出程序会出现异常
1.程序执行到getEle(ar,3)时，jvm会根据异常产生原因创建一个异常对象（内容、原因、位置）并抛出，此时该方法中无异常处理逻辑，将继续抛给其调用者main方法来处理
2.main方法接收到了这个异常对象，但main方法也没有异常处理逻辑，将继续抛出给main方法的调用者jvm处理
3.jvm接收到了这个异常对象，将以红字异常对象打印在控制台，并终止正在执行的java程序
*/
```

`4`throw关键字

我们在定义方法时，必须对方法传递过来的参数进行合法性校验，如果参数不合法，我们必须使用抛出异常的方式，告知调用者

```java
public class Hello {
    public static void main(String[] args){
        int[] ar={1,2,3};
        getEle(null,3);
    }
    public static int getEle(int[] arr,int index){
        if(arr==null){
            throw new NullPointerException("不能传递空指针");//Exception in thread "main" java.lang.NullPointerException: 不能传递空指针
        }
        return arr[index];
    }
}
```

1)Objects非空判断静态方法

```java
//源码摘取
public static <T> T requireNonNull(T obj) {
	if (obj == null)
		throw new NullPointerException();
	return obj;
}
```

2）throws关键字注意问题

(1)throws关键字必须写在方法声明处

(2)throws关键词后声明的异常必须时Exception或Exception的子类

(3)如果抛出异常，大部分情况我们需要处理这个异常对象，用throws关键字或try…catch…。但如果这个异常对象时RuntimeException及其子类对象，可不处理，由jvm处理

`5`Throwable类的3个异常详情打印方法

```java
//源码摘取
public String getMessage() {//返回此Throwable的简短描述
	return detailMessage;
}
public String toString() {//返回此Throwable的详细描述
	String s = getClass().getName();
	String message = getLocalizedMessage();
	return (message != null) ? (s + ": " + message) : s;
}
public void printStackTrace() {//打印异常对象，信息最全面
	printStackTrace(System.err);
}
```

`6`finally关键字

用在try…catch…后，无论程序是否报错，都会执行，一般用于资源释放

```
try{
	//可能报错的代码
}catch(Exception e){
	e.printStackTrace();//打印错误
}finally{
	//资源释放代码
}
```

小技巧：finally后面尽量不要写return

`7`父子类方法中抛出的异常

```java
class father{
    public void show01() throws NullPointerException,ClassCastException{};
    public void show02() throws IndexOutOfBoundsException{};
    public void show03() throws IndexOutOfBoundsException{};
    public void show04(){};
}

class child extends father{
    //1.和父类保持一致
    public void show01() throws NullPointerException,ClassCastException{};

    //2.抛出错误是父类抛出错误的子类
    public void show02() throws ArrayIndexOutOfBoundsException{};
    
    //3.不抛出错误
    public void show03(){};

    //4.仅子类抛出错误
    public void show04() throws ArrayIndexOutOfBoundsException{};

}
```

`8`自定义异常

java提供的异常不够用，我们需要自己定义异常。自定义异常必须继承Exception或RuntimeException，内部包含两个构造方法，一是空参数的构造方法，二是带异常信息的构造方法

```java
public class AbcException extends Exception{
    public AbcException() {
        super();
    }

    public AbcException(String message) {
        super(message);
    }
}
```

---

**多线程**

`1`并发和并行

并发：两个或多个事件在同一时间段发生

并行：两个或多个事件在同一时刻发生

`2`进程和线程

进程：进程是资源（CPU、内存等）分配的基本单位，它是程序执行时的一个实例。程序运行时系统就会创建一个进程，并为它分配资源，然后把该进程放入进程就绪队列，进程调度器选中它的时候就会为它分配CPU时间，程序开始真正运行。

线程：线程是程序执行时的最小单位，它是进程的一个执行流，是CPU调度和分派的基本单位，一个进程可以由很多个线程组成，线程间共享进程的所有资源，每个线程有自己的堆栈和局部变量。线程由CPU独立调度执行，在多CPU环境下就允许多个线程同时运行。同样多线程也可以实现并发操作，每个请求分配一个线程来处理。

`3`线程调度

分时调度、优先级抢占式调度（注意和操作系统里不一致）

`4`创建线程

1）方式一

创建一个类继承Thread，并重写run方法。然后将该类实例化，并且调用该实例的start方法

```java
public class Hello {
    public static void main(String[] args){
        Thread t1=new myThread();
        t1.run();//单线程方式
        t1.start();//多线程方式，开辟了新的栈空间
        for (int i = 0; i <20 ; i++) {
            System.out.println("main"+i);
        }
    }
}

class myThread extends Thread{//定义线程
    @Override
    public void run() {//重写run方法
        for (int i = 0; i <20 ; i++) {
            System.out.println("mythread"+i);
        }
    }
}
```

2）方式二

创建一个Runable接口的实现类，并重写run方法。再创建一个Thread对象，将Runable的实现类对象传入，调用Thread对象的start方法

```java
public class Hello {
    public static void main(String[] args){
        Thread t1=new Thread(new myThread1());//将Runable的实现类对象传入
        t1.start();
        for (int i = 0; i <20 ; i++) {
            System.out.println("main"+i);
        }
    }
}

class myThread1 implements Runnable{//定义线程
    @Override
    public void run() {//重写run方法
        for (int i = 0; i <20 ; i++) {
            System.out.println("mythread1-"+i);
        }
    }
}
```

此种方式创建多线程优势：

(1)避免了单继承的局限性

(2)将设置线程和开启线程进行分离，降低程序耦合性

3）匿名内部类实现多线程

```java
public class Hello {
    public static void main(String[] args){
        Thread t1=new Thread(){
            @Override
            public void run() {//重写run方法
                for (int i = 0; i <20 ; i++) {
                    System.out.println("mythread"+i);
                }
            }
        };
        t1.start();
        for (int i = 0; i <20 ; i++) {
            System.out.println("main"+i);
        }
    }
}
```

```java
public class Hello {
    public static void main(String[] args){
        Thread t1=new Thread(new Runnable(){
            @Override
            public void run() {//重写run方法
                for (int i = 0; i <20 ; i++) {
                    System.out.println("mythread1-"+i);
                }
            }
        });
        t1.start();
        for (int i = 0; i <20 ; i++) {
            System.out.println("main"+i);
        }
    }
}
```

`5`线程内常用方法

1）获取当前线程

```
public static native Thread currentThread();
```

```java
public class Hello {
    public static void main(String[] args){
        System.out.println(Thread.currentThread());//Thread[main,5,main]
    }
}
```

2）获取线程名称

```
public final String getName()
```

```java
public class Hello {
    public static void main(String[] args){
        Thread t1=new Thread(){
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName());//Thread-0
            }
        };
        System.out.println(t1.getName());//Thread-0
        t1.start();
    }
}
```

3）设置线程名称

方式一：

在线程内调用setName方法

```java
public class Hello {
    public static void main(String[] args){
        Thread t1=new Thread(){
            @Override
            public void run() {
                Thread.currentThread().setName("thread-zhanghuan");
                System.out.println(Thread.currentThread().getName());
            }
        };
        t1.start();//thread-zhanghuan
    }
}

```

方式二：

在创建线程时将线程名传入

```java
public class Hello {
    public static void main(String[] args){
        Thread t1=new Thread("zhanghuan123"){//创建线程时传入
            @Override
            public void run() {
            }
        };
        t1.start();
        System.out.println(t1.getName());//zhanghuan123
    }
}
```

3）线程睡眠

暂停一段时间后，线程继续执行

```java
public class Hello {
    public static void main(String[] args){
        Thread t1=new Thread(){
            @Override
            public void run() {
                System.out.println("线程开始睡眠了");
                try {
                    Thread.sleep(5000);//会抛出异常
                }catch (Exception e){
                    System.out.println(e);
                }
                System.out.println("5秒后线程睡醒了");
            }
        };
        t1.start();
    }
}
```

`6`线程锁

多线程访问共享数据会产生安全问题，有以下三种解决办法

1）同步代码块

```java
public class Hello {
    public static void main(String[] args){
        myThread t=new myThread();

        Thread t1=new Thread(t);
        t1.start();
        Thread t2=new Thread(t);
        t2.start();
        
    }
}

class myThread implements Runnable{
    int tikect_nmu=100;
    Object obj=new Object();
    @Override
    public void run() {
        while (tikect_nmu>0){
            synchronized (obj){//同步代码块
                System.out.println(Thread.currentThread().getName()+"卖出第"+tikect_nmu+"张票");
                tikect_nmu--;
            }
        }
    }
}
//同步内的线程，未执行完毕不会释放锁，同步外的线程只可等待
```

2）同步方法

```java
public class Hello {
    public static void main(String[] args){
        myThread t=new myThread();

        Thread t1=new Thread(t);
        t1.start();
        Thread t2=new Thread(t);
        t2.start();

    }
}

class myThread implements Runnable{
    int tikect_nmu=100;
    Object obj=new Object();
    @Override
    public void run() {
        while (tikect_nmu>0){
            sale();
        }
    }
    synchronized void sale(){//同步方法
        System.out.println(Thread.currentThread().getName()+"卖出第"+tikect_nmu+"张票");
        tikect_nmu--;
    }
}
//锁对象是实现类对象this
```

3）静态方法

```java
public class Hello {
    public static void main(String[] args){
        myThread t=new myThread();

        Thread t1=new Thread(t);
        t1.start();
        Thread t2=new Thread(t);
        t2.start();

    }
}

class myThread implements Runnable{
    static int tikect_nmu=100;//静态方法访问的变量必须是静态变量
    Object obj=new Object();
    @Override
    public void run() {
        while (tikect_nmu>0){
            sale();
        }
    }
    static synchronized void sale(){//静态方法
        System.out.println(Thread.currentThread().getName()+"卖出第"+tikect_nmu+"张票");
        tikect_nmu--;
    }
}
//锁对象是本类的class属性
```

4）Lock锁

Lock是一个接口，实现类是ReentrantLock。先创建ReentrantLock对象，在可能出现问题的代码前通过ReentrantLock对象调用lock()，在可能出现问题的代码后通过ReentrantLock对象调用unlock()

```java
import java.util.concurrent.locks.ReentrantLock;

public class Hello {
    public static void main(String[] args){
        myThread t=new myThread();

        Thread t1=new Thread(t);
        t1.start();
        Thread t2=new Thread(t);
        t2.start();


    }
}

class myThread implements Runnable{
    int tikect_nmu=100;
    ReentrantLock lock=new ReentrantLock();
    @Override
    public void run() {
        while (tikect_nmu>0){
            try{//用try…catch…finally…来提高效率
                lock.lock();//关锁
                System.out.println(Thread.currentThread().getName()+"卖出第"+tikect_nmu+"张票");
                tikect_nmu--;
            }catch (Exception e){
                System.out.println(e);
            }finally {
                lock.unlock();//开锁
            }
        }
    }

}
```

`7`线程状态

NEW 新建状态

BLOCKED 阻塞状态

RUNNABLE 运行状态

TERMINATED 死亡状态

TIME_WATING 休眠状态

WAITING 无限等待状态

```java
public class Hello {
    public static void main(String[] args){
        Object obj=new Object();
        Thread t0=new Thread(){
            @Override
            public void run() {
                System.out.println("顾客来到店里，告诉老板自己要吃包子了");
                synchronized (obj){
                    while (true){
                        try {
                            //Thread.sleep(1000);
                            //1s后线程会自动唤醒，无法通过obj（锁）进行唤醒，在等待过程中仍然持有锁

                            obj.wait();//通过锁，让线程进入等待状态，并释放持有的锁。可传入参数（毫秒），时间过了会自动唤醒线程
                            //notify是不能唤醒sleep的，而且wait、notify、notifyall需要在同步代码中使用，sleep不需要，如果sleep在notify之前使用，sleep不会释放锁
                            System.out.println("顾客拿到一个包子，一口吞下去了");
                            System.out.println("---------------华丽的分割线----------------");
                        }catch (Exception e){
                            System.out.println(e);
                        }
                    }
                }
            }
        };
        Thread t1=new Thread(){
            @Override
            public void run() {
                while (true){
                    try{
                        Thread.sleep(5000);
                        System.out.println("5秒后包子做好了1个包子，呼叫顾客取包子……");
                        synchronized (obj){
                            obj.notify();//通过锁，唤醒线程

                            //obj.notifyAll();
                            //如果有多个线程在等待，可唤醒锁下所有等待的线程
                        }
                    }catch (Exception e){
                        System.out.println(e);
                    }
                }
            }
        };
        t0.start();
        t1.start();
    }
}
```

`8`线程通信案例（生产者消费者模型）

```java
public class Hello {
    public static void main(String[] args){
        baozi bz=new baozi();//线程之间竞争的临界资源

        baozipu bzp=new baozipu(bz);
        chihuo ch=new chihuo(bz);
        bzp.start();
        ch.start();
    }
}

class baozi{
    String pi;
    String xian;
    boolean flag=false;
}

class baozipu extends Thread{
    private baozi bz;
    private int count;
    public baozipu(baozi bz){
        this.bz=bz;
    }
    @Override
    public void run() {
        while (true){
            synchronized (bz){
                if(bz.flag==true){
                    try {
                        bz.wait();
                    }catch (Exception e){
                        System.out.println(e);
                    }
                }
                if(count%2==0){
                    bz.pi="薄皮";
                    bz.xian="牛肉陷";
                }else {
                    bz.pi="冰皮";
                    bz.xian="羊肉陷";
                }
                bz.flag=true;
                System.out.println("包子铺生成了一个"+bz.pi+bz.xian+"的包子");
                count++;
            }
        }
    }
}

class chihuo extends Thread{
    private baozi bz;
    public chihuo(baozi bz){
        this.bz=bz;
    }
    @Override
    public void run() {
        while (true){
            synchronized (bz){
                if(bz.flag==true){
                    System.out.println("吃货吃了一个"+bz.pi+bz.xian+"的包子");
                }
                bz.flag=false;
                try{
                    bz.notify();
                }catch (Exception e){
                    System.out.println(e);
                }

            }
        }
    }
}
```

`9`线程池

容纳多个线程的容器，其中的线程可反复使用，省去频繁创建线程对象的操作，无需反复创建线程而消耗过多资源，线程池工厂位于java.util.concurrent.Executors，JDK1.5之后提供

线程池工厂Executors提供创建线程池的静态方法

```
public static ExecutorService newFixedThreadPool(int nThreads)
/*
参数：线程池中包含的线程个数
返回值：ExecutorService接口的实现类对象
*/
```

ExecutorService接口的实现类对象提供的方法

```
submit(Runnable task);//向线程池提交任务，让执行，执行完成后线程会继续等待新任务
void shutdown();//关闭线程池，不建议执行
```

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Hello {
    public static void main(String[] args){
        ExecutorService pools=Executors.newFixedThreadPool(2);//通过线程池工厂Executors创建线程池
        pools.submit(new Runnable() {//向线程池提交任务
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+"运行了");
            }
        });
        pools.submit(new Runnable() {//向线程池提交任务
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+"运行了");
            }
        });

    }
}

```

---

**Lambda表达式**

`1`面向对象和面向过程

面向对象：做一件事，找一个能解决这个事情的对象，调用对象方法完成任务

面向过程：做一件事，注重结果，怎么做、谁做不重要

`2`实现接口的匿名内部类改写成Lambda表达式

```java
public class Hello {
    public static void main(String[] args){
        //实现接口的匿名内部类方式创建线程
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+"运行起来了");
            }
        }).start();

        //改写成Lambda表达式
        new Thread(()->{
            System.out.println(Thread.currentThread().getName()+"运行起来了");
        }).start();
        new Thread(()-> System.out.println(Thread.currentThread().getName()+"运行起来了")).start();//Lambda表达式进一步简写
    }
}
/*运行结果
Thread-0运行起来了
Thread-1运行起来了
Thread-2运行起来了
*/
```

`3`Lambda表达式使用注意事项

1）使用前提

(1)Lambda省略的对象只能是接口的匿名实现类，且接口有且仅有一个抽象方法

(2)Lambda中传递的参数必须与接口相对应

备注：有且仅有一个抽象方法的接口称为函数式接口

2）可省略内容

Lambda表达式可推导可省略，即凡是可以根据上下文推导出的内容可省略不写

(1)参数列表

括号中参数列表的数据类型可省略，括号中参数只有一个，括号可省略

(2)部分代码

大括号中代码只有一行，可同时胜率大括号，return，分号（注意：三者必须同时省略）

---

**File类**

java把电脑里的文件和文件夹封装成一个File类，可使用File类对文件和文件夹进行操作

`1`分隔符

1）路径分隔符

```
System.out.println(File.pathSeparator);
```

在windows中，为";"分号，在linux中，为":"冒号

2）文件分割符

```
System.out.println(File.separator);
```

在windows中，为"\\"反斜杠，在linux中，为"/"正斜杠

> 注意
>
> 1.路径不区分大小写
>
> 2.windows中文件分隔符与转译字符冲突，故windows中分隔符使用“\\\”

`2`构造方法

1）public File(String pathname)

参数是路径名称，路径可指向文件也可指向文件夹，可是相对路径也可是绝对路径，可存在也可不存在

```java
import java.io.File;

public class Hello {
    public static void main(String[] args){
        File f=new File("C:\\Users\\admin\\Desktop\\vue");
        System.out.println(f);//C:\Users\admin\Desktop\vue
    }
}
```

2）public File(String parent, String child)

将路径分为两个部分

3）public File(File parent, String child)

将路径分为两个部分

`3`常用方法

```java
import java.io.File;

public class Hello {
    public static void main(String[] args){
        File f=new File("src\\aaa\\123.txt");
        
        //获取绝对路径
        System.out.println(f.getAbsolutePath());//C:\Users\admin\IdeaProjects\untitled1\src\aaa\123.txt

        //获取路径
        //如果传入的路径是绝对路径这里获取的就是绝对路径，如果传入的路径是相对路径这里获取的就是相对路径
        //File类的toString调用的就是getPath()
        System.out.println(f.getPath());//src\aaa\123.txt

        //获取路径结尾部分
        System.out.println(f.getName());//123.txt

        //获取文件大小，以字节为单位
        //路径不存在或路径指向文件夹，返回0
        System.out.println(f.length());//3242
    }
}
```

`4`判断方法

```java
import java.io.File;

public class Hello {
    public static void main(String[] args){
        File f=new File("src\\aaa\\123.txt");
        //判断路径是否存在
        System.out.println(f.exists());
        
        //判断路径是否是文件夹
        //如果路径不是文件夹或路径不存在，返回false
        System.out.println(f.isDirectory());
        
        //判断路径是否是文件
        //如果路径不是文件或路径不存在，返回false
        System.out.println(f.isFile());
    }
}
```

`5`创建删除方法

1）创建文件

```
public boolean createNewFile() throws IOException
```

返回值：

文件存在，返回false；文件不存在，创建文件，返回true

此方法只能创建文件，不能创建文件夹；创建文件路径必须存在，否则抛出异常

2）创建文件夹

(1)创建单级空文件夹

```
public boolean mkdir()
```

(2)创建多级、单级空文件夹

```
public boolean mkdirs()
```

返回值：

文件夹存在，返回false；文件夹不存在，创建文件，返回true

此方法只能创建文件夹，不能创建文件

3）删除文件或文件夹

```
public boolean delete()
```

返回值：

文件、文件夹删除成功返回true，路径不存在或删除文件夹时文件夹中有文件或文件夹，返回false

直接硬盘删除，不走回收站

`6`遍历文件夹

```
public String[] list()
```

列出该文件夹下这一层级所有的文件和文件夹，返回值为String[]

```
public File[] listFiles()
```

列出该文件夹下这一层级所有的文件和文件夹，返回值为File[]

如果目标文件夹不存在，返回空指针异常

`7`递归

1）递归分类

直接递归、间接递归

2）递归使用注意事项

使用前提：方法主体不变，每次调用参数不同

(1)递归一定要有条件限定，保证能停下来，否则会导致栈内存溢出

(2)递归次数不能太多，否则会导致栈内存溢出

(3)构造方法不可递归

(4)递归回导致不断入栈出栈，因此效率很低

3）递归遍历文件夹案例

```java
import java.io.File;

public class Hello {
    public static void main(String[] args){
        File f=new File("C:\\Users\\admin\\Desktop\\mangodb_datas");
        getfile(f);
    }
    public static void getfile(File file){
        File[] files=file.listFiles();
        for (File f:
             files) {
            if(f.isDirectory()){
                System.out.println(f);
                getfile(f);//调用自己
            }else{
                System.out.println(f);
            }
        }
    }
}
```

`8`文件过滤器

1）FileFilter接口

```
public File[] listFiles(FileFilter filter)
```

```java
//源码摘取
public interface FileFilter {

    /**
     * Tests whether or not the specified abstract pathname should be
     * included in a pathname list.
     *
     * @param  pathname  The abstract pathname to be tested
     * @return  <code>true</code> if and only if <code>pathname</code>
     *          should be included
     */
    boolean accept(File pathname);
}
```

在遍历文件时，每找到一个文件都会通过过滤器进行判断，过滤器里的accept方法里会接受到每一个遍历到的文件，如果该方法返回的值是true将会通过过滤器，否则不通过

```java
import java.io.File;

public class Hello {
    public static void main(String[] args){
        File f=new File("C:\\Users\\admin\\Desktop\\java_base");
        getfile(f);
    }
    public static void getfile(File file){//找出文件夹里所有以java结尾的文件
        File[] files=file.listFiles((pathname)->{
            if(pathname.getAbsolutePath().endsWith(".java")){
                return true;
            }
            return false;
        });
        for (File f:
             files) {
            if(f.isDirectory()){
                System.out.println(f);
                getfile(f);
            }else{
                System.out.println(f);
            }
        }
    }
}
```

2）FilenameFilter接口

```
public File[] listFiles(FilenameFilter filter)
```

```java
//源码摘取
public interface FilenameFilter {
    /**
     * Tests if a specified file should be included in a file list.
     *
     * @param   dir    the directory in which the file was found.
     * @param   name   the name of the file.
     * @return  <code>true</code> if and only if the name should be
     * included in the file list; <code>false</code> otherwise.
     */
    boolean accept(File dir, String name);
}
```

和FileFilter类似，参数不同。

```java
import java.io.File;

public class Hello {
    public static void main(String[] args){
        File f=new File("C:\\Users\\admin\\Desktop\\java_base");
        getfile(f);
    }
    public static void getfile(File file){//找出文件夹里所有以java结尾的文件
        File[] files=file.listFiles((dir,name)->{
            if(name.endsWith(".java")){
                return true;
            }
            return false;
        });
        for (File f:
             files) {
            if(f.isDirectory()){
                System.out.println(f);
                getfile(f);
            }else{
                System.out.println(f);
            }
        }
    }
}
```

`9`字节输出流

以内存为中心向外输出字节

1）超类OutStream

位于java.io.OutStream，此抽象类是表示输出字节流的所有类的超类，以下是常用方法

```
public abstract void write(int b) throws IOException;//向流中写数字
public void write(byte b[]) throws IOException;//向流中写字节数组
public void write(byte b[], int off, int len) throws IOException;//向流中写字节数组的一部分
```

```
void flush() throws IOException;//继承的Flushable接口中的方法
```

```
public void close() throws IOException;//继承的Closeable接口中的方法
```

2）文件字节输出流FileOutputStream

把内存中的数据写到硬盘中

(1)构造方法

```java
//源码摘取
public FileOutputStream(String name) throws FileNotFoundException {//参数是字符串
        this(name != null ? new File(name) : null, false);
}
```

```java
//源码摘取
public FileOutputStream(File file) throws FileNotFoundException {///参数是文件对象，重载方法
        this(file, false);
}
```

```java
//源码摘取
public FileOutputStream(String name, boolean append)//重载方法,多一个参数append，表示追加的意思
        throws FileNotFoundException
    {
        this(name != null ? new File(name) : null, append);
    }
```

执行构造方法后首先会创建FileOutputStream对象，然后根据传来的字符串路径创建空文件对象，并将FileOutputStream对象的输出目标指向该文件对象

(2)写入方法

```
public abstract void write(int b) throws IOException;//向流中写数字
public void write(byte b[]) throws IOException;//向流中写字节数组
public void write(byte b[], int off, int len) throws IOException;//向流中写字节数组的一部分
```

这些都是继承的超类的方法，将字节写入到文件中

一次写入多个字节，如果第一个字节是整数（0-127），显示时会查询ASC表；如果第一个字节是负数，显示时会以两个字节为一个字符查询系统默认的GBK编码表

(3)追加

```
public FileOutputStream(String name, boolean append)
```

默认情况下调用流对象会将之前已存在文件中的数据清空覆盖，但使用这个构造方法，传入的append值为true，将会以追加方式写入

(4)换行

不同系统的换行字符不一样，windows（\r\n），linux（/n），mac（/r）

`10`字节输入流

将外部数据以字节流方式输入到内存

1）超类InputStream

位于java.io.InputStream，此抽象类表示输入流的所有类的超类，以下是常用方法

```
public abstract int read() throws IOException;//每次读取一个字节
public int read(byte b[]) throws IOException;//每次读取b.length个字节,并将读取到的结果放在b中
//注意：byte b[]和byte[] b等同
```

2）文件字节输入流FileInputStream

(1)构造方法

```
public FileInputStream(String name) throws FileNotFoundException;
```

```
public FileInputStream(File file) throws FileNotFoundException;
```

执行构造方法后首先会创建FileInputStream对象，然后根据传来的字符串路径创建文件对象，并将FileInputStream对象的输入源指向该文件对象

(2)读取方法

```
public abstract int read() throws IOException;//每次读取一个字节
public int read(byte b[]) throws IOException;//每次读取b.length个字节,并将读取到的结果放在b中
```

这些都是继承的超类的方法，将源对象中的数据读取到内存中

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) throws FileNotFoundException,IOException{
        FileInputStream fls=new FileInputStream("C:\\Users\\admin\\Desktop\\java_base\\test.txt");
        int b;
        while ((b=fls.read())!=-1){//每次读取一个字节
            System.out.println(b);
        }
    }
    
}
```

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) throws FileNotFoundException,IOException{
        FileInputStream fls=new FileInputStream("C:\\Users\\admin\\Desktop\\java_base\\test.txt");
        byte[] b=new byte[1024];
        byte[] allData=new byte[0];//获取到的字节缓存到这里
        int len;
        while ((len=fls.read(b))!=-1){
            allData=ByteArrayConcat(allData,b);
        }
        System.out.println(new String(allData));
    }
    public static byte[] ByteArrayConcat(byte[] a,byte[] b){//字节数组合并方法
        byte[] c=new byte[a.length+b.length];
        System.arraycopy(a,0,c,0,a.length);
        System.arraycopy(b,0,c,a.length,b.length);
        return c;
    }
}
```

`11`字符输入流

1）Reader超类

位于java.io.Reader，此抽象类表示字符输入流的所有类的超类，以下是常用方法

```
public int read() throws IOException;//读取单个字符并返回
public int read(char cbuf[]) throws IOException;//读取多个字符，放入数组
```

2）文件字符输入流FileReader

```
java.io.FileReader extends InputStreamReader extends Reader
```

(1)构造方法

```
public FileReader(String fileName) throws FileNotFoundException;
```

```
public FileReader(File file) throws FileNotFoundException;
```

执行构造方法后首先会创建FileReader对象，然后根据传来的字符串路径创建文件对象，并将FileReader对象的输入源指向该文件对象

(2)读取方法

```
public int read() throws IOException;//继承的InputStreamReader的方法
```

```
public int read(char cbuf[]) throws IOException;//继承的Reader的方法
```

`12`字符输出流

1）Writer超类

位于java.io.Writer，此抽象类表示字符输出流的所有类的超类，以下是常用方法

```
public void write(int c) throws IOException;//写一个字符
public void write(char cbuf[]) throws IOException;//写入一个字符数组
public void write(char cbuf[], int off, int len) throws IOException;//OutputStreamWriter里的方法，向流中写字符数组的一部分
public void flush() throws IOException;//刷新
```

2）文件字符输出流FileWriter

```
java.io.FileWriter extends OutputStreamWriter extends Writer
```

(1)构造方法

```
public FileWriter(String fileName) throws IOException;
public FileWriter(String fileName, boolean append) throws IOException;
public FileWriter(File file) throws IOException;
public FileWriter(File file, boolean append) throws IOException;
```

(2)写入方法

```
public void write(int c) throws IOException;//OutputStreamWriter里重写Writer的方法
public void write(char cbuf[], int off, int len) throws IOException;//OutputStreamWriter里重写Writer的方法
public void write(String str, int off, int len) throws IOException;//OutputStreamWriter里重写Writer的方法
public void write(char cbuf[]) throws IOException;//Writer里的方法
public void write(String str) throws IOException;//Writer里的方法
```

3）try…catch…finally…

一般情况下使用try…catch…finally…捕获异常

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) {
        FileWriter fw=null;
        try{
            fw=new FileWriter("C:\\Users\\admin\\Desktop\\java_base\\456.txt");
            for (int i = 0; i <10 ; i++) {
                fw.write("你好啊");
                fw.write("\r\n");
            }
        }catch (IOException e){
            System.out.println(e);
        }finally {
            if(fw!=null){
                try {
                    fw.close();
                }catch (IOException e){
                    System.out.println(e);
                }
            }
        }

    }
    
}
```

JDK7新特性

在try后加一个()，并在()里定义流对象，try…catch…程序执行完毕，将自动释放流对象，不用写finally

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) {
        try(FileWriter fw=new FileWriter("C:\\Users\\admin\\Desktop\\java_base\\456.txt")){
            for (int i = 0; i <10 ; i++) {
                fw.write("你好啊");
                fw.write("\r\n");
            }
        }catch (IOException e){
            System.out.println(e);
        }
    }

}
```

JDK9新特性

在try后加一个()，并在()里将定义流对象传入，try…catch…程序执行完毕，将自动释放流对象，不用写finally

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) throws IOException{//实际上不好用
        FileWriter fw=new FileWriter("C:\\Users\\admin\\Desktop\\java_base\\456.txt");
        try(fw){
            for (int i = 0; i <10 ; i++) {
                fw.write("你好啊");
                fw.write("\r\n");
            }
        }catch (IOException e){
            System.out.println(e);
        }
    }

}
```

`13`Properties集合

```
Properties extends Hashtable<Object,Object> implements Map<K,V>
```

该集合是唯一一个与流相结合的集合，其store方法将数据存放到硬盘中，其load方法将数据读取到集合中

```java
import java.io.*;
import java.util.Properties;
import java.util.Set;

public class Hello {
    public static void main(String[] args) throws IOException{
        Properties p=new Properties();

        //设置键值对
        p.setProperty("name","zhanghuan");
        p.setProperty("age","22");
        p.setProperty("sex","male");

        //获取键对应的值
        System.out.println(p.getProperty("name"));//zhanghuan

        //获取集合中所有的键
        Set<String> set=p.stringPropertyNames();
        for (String item:
             set) {
            System.out.println(item+":"+p.getProperty(item));
        }
        /*该循环执行结果
        sex:male
        name:zhanghuan
        age:22
         */

        //将键值对存入硬盘
        FileWriter fw=new FileWriter("C:\\Users\\admin\\Desktop\\java_base\\456.txt");
        p.store(fw,"save data");
        /*执行后C:\Users\admin\Desktop\java_base\456.txt文件里的内容
        #save data
        #Wed Apr 08 01:13:36 CST 2020
        sex=male
        name=zhanghuan
        age=22
         */

        //将键值从硬盘读取出来
        Properties p1=new Properties();//新建一个集合对象来接受从硬盘读取的数据
        p1.load(new FileReader("C:\\Users\\admin\\Desktop\\java_base\\456.txt"));
        //将读取的结果打印出来
        Set<String> set1=p1.stringPropertyNames();
        for (String item:
                set1) {
            System.out.println(item+":"+p1.getProperty(item));
        }
    }

}
```

`14`字节缓冲输出流BufferedOutputStream

```
java.io.BufferedOutputStream extends FilterOutputStream extends OutputStream
```

其继承的父类共性方法不再赘述

1）构造方法

```java
//源码摘取
public BufferedOutputStream(OutputStream out) {
	this(out, 8192);
}
public BufferedOutputStream(OutputStream out, int size) {
    super(out);
    if (size <= 0) {
        throw new IllegalArgumentException("Buffer size <= 0");
    }
    buf = new byte[size];
}
//size参数是设置缓冲区大小，默认8192
```

2）使用步骤

(1)创建FileOutputStream对象

(2)创建BufferedOutputStream对象，并将刚创建好的FileOutputStream对象作为参数传入

(3)如操作OutputStream对象一样操作BufferedOutputStream对象

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) throws IOException {
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("C:\\Users\\admin\\Desktop\\java_base\\123.txt"));
        byte[] b={97,98,99};
        bos.write(b);
        bos.close();
    }

}
```

`15`字节缓冲输入流BufferedInputStream

```
java.io.BufferedInputStream extends FilterInputStream extends InputStream
```

1）构造方法

```java
//源码摘取
public BufferedInputStream(InputStream in) {
	this(in, DEFAULT_BUFFER_SIZE);
}
public BufferedInputStream(InputStream in, int size) {
	super(in);
	if (size <= 0) {
		throw new IllegalArgumentException("Buffer size <= 0");
	}
	buf = new byte[size];
}
```

2）使用步骤

不再赘述

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) throws IOException {
        BufferedInputStream bis=new BufferedInputStream(new FileInputStream("C:\\\\Users\\\\admin\\\\Desktop\\\\java_base\\\\123.txt"));
        byte[] b=new byte[1024];
        int len;
        while ((len=bis.read(b))!=-1){
            System.out.println(new String(b));
        }
        bis.close();
    }

}
```

`16`字符缓存输出流

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) throws IOException {
        BufferedWriter bw=new BufferedWriter(new FileWriter("C:\\Users\\admin\\Desktop\\java_base\\789.txt"));
        for (int i = 0; i <10 ; i++) {
            bw.write("abcdefg");
            
            //特有方法，写一个分隔符，对于不同系统，写出不同分隔符
            bw.newLine();
        }
        bw.close();
    }

}
```

`17`字符缓存输入流

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) throws IOException {
        BufferedReader br=new BufferedReader(new FileReader("C:\\\\Users\\\\admin\\\\Desktop\\\\java_base\\\\789.txt"));
        String line;
        
        //特有成员方法readLine，只读取内容，不读取终止符，读到文件末尾返回null
        while((line=br.readLine())!=null){
            System.out.println(line);
        }
        br.close();
    }

}
```

`18`转换流

1）字符编码和字符计

(1)字符编码

自然语言和二进制数的对应规则

(2)字符集

| 大类     | 小类                  |
| -------- | --------------------- |
| 美国ASC  | ASCII                 |
| 欧洲编码 | ISO-8859-1兼容ASCII   |
| 中国编码 | GB2312、GBK、GB18030  |
| 万国码   | UTF-8、UTF-16、UTF-32 |

2）UTF-8编码规则

(1)128个ASCII字符占用1个字节

(2)拉丁文占用2个字节

(3)大部分中文常用字占用3个字节

(4)其他辅助字符占用4个字节

3）字符输出转换流OutputStreamWriter

(1)构造方法

```
public OutputStreamWriter(OutputStream out);
 public OutputStreamWriter(OutputStream out, String charsetName) throws UnsupportedEncodingException;
```

(2)案例

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) throws IOException{
        write_utf8();
    }
    public static void write_utf8() throws FileNotFoundException,IOException{
        OutputStreamWriter osw=new OutputStreamWriter(new FileOutputStream("abc.txt"),"utf8");
        for (int i = 0; i <10 ; i++) {
            osw.write("你好");
            osw.write("\r\n");
        }
        osw.close();
    }

}
```

4）字符输入转换流InputStreamWriter

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) throws IOException {
        read_utf8();
    }

    private static void read_utf8()  throws FileNotFoundException, UnsupportedEncodingException,IOException{
        InputStreamReader isr=new InputStreamReader(new FileInputStream("abc.txt"),"utf8");
        char[] c=new char[1024];
        int len;
        while((len=isr.read(c))!=-1){
            System.out.println(c);
        }
    }

}
```

`19`序列化

把对象以流的方式写入到文件中保存。其中序列化是指将对象写入到文件中，反序列化是指从文件中读取出对象。

1） ObjectOutputStream对象

```
 java.io.ObjectOutputStream extends OutputStream implements ObjectOutput, ObjectStreamConstants
```

特有成员方法

```
public final void writeObject(Object obj) throws IOException;
```

此方法将对象写入到文件中，需注意的是写入到文件中的对象必须实现 Serializable接口，否则会抛出NotSerializableException错误

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) throws IOException{
        ObjectOutputStream oos=new ObjectOutputStream(new FileOutputStream("C:\\Users\\admin\\Desktop\\java_base\\xlh.txt"));
        Person p=new Person("zhanghuan",20);//Person类必须实现 Serializable接口
        oos.writeObject(p);
        oos.close();
    }

}
```

2） ObjectInputStream对象

```
java.io.ObjectInputStream extends InputStream implements ObjectInput, ObjectStreamConstants
```

特有成员方法

```
public final Object readObject() throws IOException, ClassNotFoundException;
```

此方法从文件中读取对象，读取出来的对象是Object需要进行强制转换成想要的类型

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) throws IOException,ClassNotFoundException{
        ObjectInputStream ois=new ObjectInputStream(new FileInputStream("C:\\Users\\admin\\Desktop\\java_base\\xlh.txt"));
        Person p= (Person) ois.readObject();
        System.out.println(p);//Person{name='zhanghuan', age=20}
        ois.close();
    }

}
```

3）序列化注意事项

(1)static修饰的成员不可序列化

(2)transient修饰的成员也不能序列化，对于对象中不想序列化的成员用这个关键字修饰

4）其他

每次在修改类的定义时都会给class文件生成一个序列号，由于修改前后的序列号不同，在反序列化时会出现反序列化失败的问题，所以我们会给每一个要进行序列化的类手动添加一个序列号

```
//给要序列化的类加一个成员属性
private static final long serialVersionUID=1L;
```

`20`打印流

```
java.io.PrintStream extends OutputStream implements Appendable, Closeable
```

有以下特点：

1.只负责输出，不负责读取

2.永不抛出IOException异常

3.特有的print和println方法，参数可以是任意类型

1）构造方法

```
public PrintStream(String fileName) throws FileNotFoundException;
public PrintStream(File file) throws FileNotFoundException;
public PrintStream(OutputStream out);
```

2）输出数据

用继承的write方法写数据，查看时会自动查询编码表

用自带print或println方法输出数据，查看时会原样输出

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) throws IOException,ClassNotFoundException{
        PrintStream p=new PrintStream("C:\\Users\\admin\\Desktop\\java_base\\ps.txt");
        p.write(97);//输出a
        p.print(97);//输出97
        p.close();
    }

}
```

3）System类改变输出目的地

```
//System类的一个成员方法
public static void setOut(PrintStream out);
```

```java
import java.io.*;

public class Hello {
    public static void main(String[] args) throws IOException,ClassNotFoundException{
        PrintStream p=new PrintStream("C:\\Users\\admin\\Desktop\\java_base\\ps.txt");
        System.setOut(p);
        System.out.println("abcdefg");//将不会在控制台输出，输出的内容会存到目的文件中
    }

}

```

`21`网络流

1）客户端和服务端

(1)客户端和服务端通信需要4个IO流对象

(2)多个客户端和服务端通信，服务端通过accpt方法获取到发出请求的客户端对象

(3)服务端和客户端通信使用的IO流是客户端的

2）套接字

包含IP地址和端口号的网络单位

3）Socket

位于java.net.Socket，客户端套接字对象，可获取其中的输入输出流对象，向服务端进行数据交互

```java
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.Socket;

public class Client {
    public static void main(String[] args) throws IOException {
        Socket s=new Socket("localhost",8080);//创建客户端套接字，传入ip地址和端口
        OutputStream os=s.getOutputStream();
        os.write("你好，服务器".getBytes());
        InputStream is=s.getInputStream();
        s.close();
    }
}
/*
注意：
1.客户端和服务端进行通信时，必须使用Soket提供的网络流对象，不可使用自己创建的流对象
2.创建Socket对象时，服务端如果未启动，客户端会抛出异常
 */
```

4）ServerSocket

位于java.net.ServerSocket，服务端端套接字对象，可获取其中的输入输出流对象，向客户端进行数据交互

```java
import java.io.IOException;
import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {
    public static void main(String[] args) throws IOException {
        ServerSocket ss=new ServerSocket(8080);
        Socket s=ss.accept();
        byte[] b=new byte[1024];
        InputStream is=s.getInputStream();
        is.read(b);
        System.out.println(new String(b));
        s.close();
        ss.close();
    }
}
```

5）文件上传案例

(1)简易版本

```java
//客户端
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.Socket;

public class Client {
    public static void main(String[] args) throws IOException {
        Socket s=new Socket("localhost",8080);
        OutputStream os=s.getOutputStream();

        InputStream f_is= new FileInputStream("C:\\Users\\admin\\Desktop\\java_base\\15.jpg");
        byte[] b=new byte[1024];
        int len;
        while ((len=f_is.read(b))!=-1){
            os.write(b,0,len);
        }
        InputStream is=s.getInputStream();
        f_is.close();
        s.close();
    }
}
```

```java
//服务端
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {
    public static void main(String[] args) throws IOException {
        ServerSocket ss=new ServerSocket(8080);
        Socket s=ss.accept();
        InputStream is=s.getInputStream();
        OutputStream f_os=new FileOutputStream("C:\\Users\\admin\\Desktop\\java_base\\123.jpg");

        byte[] b=new byte[1024];
        int len;
        while ((len=is.read(b))!=-1){
            f_os.write(b,0,len);
        }
        f_os.close();
        is.close();
        s.close();
        ss.close();
    }
}
```

(2)升级版

```java
//服务端
//持续监听
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.Random;

public class Server {
    public static void main(String[] args) throws IOException {
        ServerSocket ss=new ServerSocket(8080);

        while (true){//服务器持续监听
            Socket s=ss.accept();
            System.out.println("开始上传");
            InputStream is=s.getInputStream();
            OutputStream f_os=new FileOutputStream("C:\\Users\\admin\\Desktop\\java_base\\"+create_random_filename()+".jpg");

            byte[] b=new byte[1024];
            int len;
            while ((len=is.read(b))!=-1){
                f_os.write(b,0,len);
            }
            f_os.close();
            is.close();
            s.close();
            System.out.println("结束上传");
        }
    }
    public static String create_random_filename(){
        return "fl"+System.currentTimeMillis()+ new Random().nextInt();
    }
}
```

```java
//客户端
//结束标识符
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.Socket;

public class Client {
    public static void main(String[] args) throws IOException {
        Socket s=new Socket("localhost",8080);
        OutputStream os=s.getOutputStream();

        InputStream f_is= new FileInputStream("C:\\Users\\admin\\Desktop\\java_base\\15.jpg");
        byte[] b=new byte[1024];
        int len;
        while (f_is.available()!=0){
            if((len=f_is.read(b))!=-1){
                os.write(b,0,len);
            }
        }
        s.shutdownOutput();//文件传输完成，告诉服务端文件已经传输完成
        f_is.close();
        s.close();
    }
}
```

(3)多线程版本

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.Random;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class Server {
    public static void main(String[] args) throws IOException {
        serve();
    }
    public static String create_random_filename(){
        return "fl"+System.currentTimeMillis()+ new Random().nextInt();
    }
    public static void serve() throws IOException{
        ServerSocket ss=new ServerSocket(8080);

        ExecutorService pool=Executors.newFixedThreadPool(2);//线程池
        while (true){
            Socket s=ss.accept();
            pool.submit(new Runnable() {//来一个链接就提交一个任务到线程池
                @Override
                public void run() {
                    try{
                        System.out.println("开始上传");
                        InputStream is=s.getInputStream();
                        OutputStream f_os=new FileOutputStream("C:\\Users\\admin\\Desktop\\java_base\\"+create_random_filename()+".jpg");

                        byte[] b=new byte[1024];
                        int len;
                        while ((len=is.read(b))!=-1){
                            f_os.write(b,0,len);
                        }
                        f_os.close();
                        is.close();
                        s.close();
                        System.out.println("结束上传");
                    }catch (Exception e){
                        System.out.println(e);
                    }
                }
            });
        }
        
    }
    
}

```

6）静态服务器案例

```java
import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class Server {
    public static void main(String[] args) throws IOException {
        serve();
    }
    public static void serve() throws IOException{
        ServerSocket ss=new ServerSocket(8080);
        
        ExecutorService pool=Executors.newFixedThreadPool(2);
        while (true){
            Socket s=ss.accept();
            pool.submit(new Runnable() {
                @Override
                public void run() {
                    try{
                        //获取请求中的路径
                        InputStream is=s.getInputStream();
                        BufferedReader br=new BufferedReader(new InputStreamReader(is));
                        String line=br.readLine();
                        String dirname=line.split(" ")[1].substring(1);

                        //获取请求文件的后缀
                        String[] dirname_array=dirname.split("\\.");
                        String suffix=dirname_array[dirname_array.length-1];


                        //根据请求找到对应的文件并创建流
                        String filename="C:\\Users\\admin\\Desktop\\java_base\\static\\"+dirname;
                        File f=new File(filename);
                        FileInputStream fis;
                        if(f.isFile()){
                            fis=new FileInputStream(filename);
                            suffix="html";
                        }else{
                            fis=new FileInputStream("C:\\Users\\admin\\Desktop\\java_base\\static\\index.html");
                        }


                        //响应请求
                        OutputStream os=s.getOutputStream();
                        os.write("HTTP/1.1 200 OK\r\n".getBytes());
                        os.write(("Content-Type:"+get_response_type(suffix)+"\r\n").getBytes());
                        os.write("\r\n".getBytes());
                        byte[] b=new byte[1024];
                        int len;
                        while((len=fis.read(b))!=-1){
                            os.write(b,0,len);
                        }
                        os.close();
                        is.close();
                        s.close();
                    }catch (Exception e){
                        System.out.println(e);
                    }
                }
            });
        }

    }
    public static String get_response_type(String suffix){//定义方法，根据后缀名确定返回的Content-Type
        String type="text/html";
        System.out.println(suffix);
        if(suffix=="html"){
            type="text/html";
        }
        System.out.println(type);
        return type;
    }

}
```

---

**函数式接口**

有且仅有一个抽象方法的接口

`1`@FunctionalInterface注解

检测接口是否是函数式接口

`2`语法糖

使用更方便但原理不变的语法。例如for-each语法，底层仍是迭代器。Java的lambda表达式可看作（严格意义上不是）匿名内部类的语法糖，但二者原理上不同

`3`使用函数式接口的三种方式

```java
//定义函数式接口
@FunctionalInterface
public interface MyFunctionalInterface {
    public void mymethod();
}

```

```java
//定义接口的实现类
public class myIMP implements MyFunctionalInterface{
    @Override
    public void mymethod() {
        System.out.println("this is mymethod from myIMP");
    }
}
```

```java
public class Hello {
    public static void show(MyFunctionalInterface mif){
        mif.mymethod();
    }
    public static void main(String[] args) {
        //传入接口的实现类对象
        show(new myIMP());

        //传入接口的匿名内部类对象
        show(new MyFunctionalInterface() {
            @Override
            public void mymethod() {
                System.out.println("this is mymethod from 匿名内部类");
            }
        });

        //传入lambda表达式
        show(()->System.out.println("this is mymethod from lambda表达式"));
    }

}
```

`4`日志优化案例

(1)优化前

```java
public class Logger {
    public static void main(String[] args) {
        String msg1="你好";
        String msg2="hello";
        showlog(1,msg1+msg2);
        /*
        调用show方法时，会先将第二个参数的字符串拼接好，再传入执行。如果leval不是1，也会先拼接字符串，这样就造成性能浪费。
         */
    }
    public static void showlog(int leval,String msg){
        if(leval==1){
            System.out.println(msg);
        }
    }
}
```

(2)优化后

```java
public class Logger {
    public static void main(String[] args) {
        String msg1="你好";
        String msg2="hello";
        showlog(1,()->System.out.println(msg1+msg2));//直接使用lambda表达式
        /*
        只有条件满足时(level==1)，才会执行lambda表达式进行字符串拼接，不存在性能浪费
         */
    }
    public static void showlog(int leval,MyFunctionalInterface mfl){//传入一个函数式接口
        if(leval==1){
            mfl.mymethod();
        }
    }
}
```

`5`常见的函数式接口

(1)Runnable接口

位于java.lang.Runnable，主要用于创建多线程

```java
public class Hello {
    public static void main(String[] args) {
        new Thread(()->{
            System.out.println(Thread.currentThread().getName());//Thread-0
        }).start();
    }

}
```

(2)Comparator<T>接口

位于java.util.Comparator<T>，凡是要用Arrays.sort()排序，第二个参数则是Comparator<T>接口的实现类

```java
//常规版本
import java.util.Arrays;
import java.util.Comparator;

public class Hello {
    public static void main(String[] args) {
        Integer[] arr={100,25,68,45,1,25,99,145};
        Arrays.sort(arr,getComparator());
        System.out.println(Arrays.toString(arr));//[1, 25, 25, 45, 68, 99, 100, 145]
    }
    public static Comparator<Integer> getComparator(){
        return new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o1.intValue()-o2.intValue();
            }
        };
    }
}
```

```java
//lambda表达式版本
import java.util.Arrays;
import java.util.Comparator;

public class Hello {
    public static void main(String[] args) {
        Integer[] arr={100,25,68,45,1,25,99,145};
        Arrays.sort(arr,getComparator());
        System.out.println(Arrays.toString(arr));//[1, 25, 25, 45, 68, 99, 100, 145]
    }
    public static Comparator<Integer> getComparator(){
        return (o1,o2)->{
            return o1.intValue()-o2.intValue();//lambda表达式
        };
    }
}
```

(3)Supplier<T>接口

位于java.util.function.Supplier<T>，生产型接口

```java
import java.util.function.Supplier;

public class Hello {
    public static void main(String[] args) {
        int[] arr={100,25,68,45,1,25,99,145};
        int max=getMax(()->{
            int _max=arr[0];
            for (int i:
                 arr) {
                if(i>_max){
                    _max=i;
                }
            }
            return _max;
        });
        System.out.println(max);//145
    }
    public static int getMax(Supplier<Integer> sup){//生产一个最大值
        return sup.get();
    }
}
```

(4)Consumer<T>接口

位于java.util.function.Consumer<T>，消费型接口

```java
import java.util.function.Consumer;

public class Hello {
    public static void main(String[] args) {
        String[] arr={"赵丽颖，25","迪丽热巴，22","蔡徐坤，21"};
        printData(arr,(msg)->{
            String s=msg.split("，")[0];
            System.out.print("姓名："+s+" ");
        },(msg)->{
            String s=msg.split("，")[1];
            System.out.print("年龄："+s+"\r\n");
        });
        /*执行结果
        姓名：赵丽颖 年龄：25
        姓名：迪丽热巴 年龄：22
        姓名：蔡徐坤 年龄：21
         */
    }
    public static void printData(String[] ar,Consumer<String> cs1,Consumer<String> cs2){
        for (String msg:
             ar) {
            cs1.andThen(cs2).accept(msg);//andThen连接两个消费者，将传入过来的数据消费掉
            //andThen连接消费者的方法
            //accpt消费数据的方法
        }
    }
}
```

(5)Predicate<T>接口

位于java.util.function.Predicate<T>，其test方法需要返回boolean，该接口用于判断

```java
import java.util.function.Predicate;

public class Hello {
    public static void main(String[] args) {
        String st1="afdcc";
        boolean result=test_string(st1,str->str.contains("a"));//判断字符串是否包含"a"
        System.out.println(result);//true
    }
    public static boolean test_string(String str,Predicate<String> pd){
        return pd.test(str);
    }
}
```

and()，or()，negate()方法

```java
import java.util.function.Predicate;

public class Hello {
    public static void main(String[] args) {
        String st1="afdcc";

        boolean result1=test_string_and(st1,str->str.contains("a"),str->str.length()>6);//判断字符串是否包含"a",且字符串长度大于6
        System.out.println(result1);//false

        boolean result2=test_string_or(st1,str->str.contains("a"),str->str.length()>6);//判断字符串是否包含"a",且字符串长度大于6
        System.out.println(result2);//true

        boolean result3=test_string_negate(st1,str->str.contains("a"));//判断字符串是否包含"a",判断结果取反
        System.out.println(result3);//false

    }
    public static boolean test_string_and(String str,Predicate<String> pd1,Predicate<String> pd2){
        return pd1.and(pd2).test(str);//两个判断接口且的关系
    }
    public static boolean test_string_or(String str,Predicate<String> pd1,Predicate<String> pd2){
        return pd1.or(pd2).test(str);//两个判断接口或的关系
    }
    public static boolean test_string_negate(String str,Predicate<String> pd){
        return pd.negate().test(str);//判断接口取反
    }
}
```

(6)Function<T,R>接口

位于java.util.function.Function<T,R>，其方法为`R apply(T t);`，根据一个数据类型转换成另一个数据类型

```java
import java.util.function.Function;

public class Hello {
    public static void main(String[] args) {
        String st1="100";
        int num=transform_from_string_to_int(st1,str->Integer.parseInt(str)+20);//将字符串转换成数字后，再加上20
        System.out.println(num);//120
    }
    public static int transform_from_string_to_int(String str, Function<String,Integer> fc){
        return fc.apply(str);
    }

}
```

andThen()方法

```java
import java.util.function.Function;

public class Hello {
    public static void main(String[] args) {
        String st1="100";
        String s=transform(st1,str->Integer.parseInt(str)+20,str->(str+""));//将字符串转换成数字后，再加上20,再转换成字符串
        System.out.println(s);//120
        System.out.println(s.getClass());//class java.lang.String
    }
    public static String transform(String str, Function<String,Integer> fc1,Function<Integer,String> fc2){
        return fc1.andThen(fc2).apply(str);
    }

}
```

`6`方法引用

双冒号"::"为引用运算符，它所在的表达式称为方法引用

例如：

lambda表达式为s->System.out.println(s)

方法引用表达式System.out::println

注意：lambda中传递的参数一定是方法引用中那个方法可接收的类型，否则抛出异常

1）通过对象名引用成员方法

```java
public class Hello {
    public static void main(String[] args) {
        say(()->{
            Person p=new Person("zhanghuan",22);
            p.say();
        });
        //my name is zhanghuan,and i am 22 years old.
        
        say(new Person("zhanghuan",25)::say);
        //my name is zhanghuan,and i am 25 years old.
    }
    public static void say(MyFunctionalInterface mif){
        mif.mymethod();
    }

}
```

2）通过类名引用静态方法

```java
public class Hello {
    public static void main(String[] args) {
        int res1=math_abs(-20,num->{
            return Math.abs(num);
        });
        System.out.println(res1);//20
        
        int res2=math_abs(-20,Math::abs);
        System.out.println(res2);//20
        
    }
    public static int math_abs(int num,MyFunctionalInterface mif){
        return mif.mymethod1(num);
    }

}
```

3）super引用父类成员方法

```java
public class Amimal {
    public void eat(){
        System.out.println("animal is hungry,is eating food");
    }
}
```

```java
public class Hello extends Amimal {
    public static void main(String[] args) {
        new Hello().show();
    }
    public static void eatfood(MyFunctionalInterface mif){
        mif.mymethod();
    }

    public void show(){
        eatfood(()->{
            new Amimal().eat();
        });
        //animal is hungry,is eating food

        eatfood(super::eat);
        //animal is hungry,is eating food
    }

}
```

4）this引用父类成员方法

```java
public class Hello{
    public static void main(String[] args) {
        new Hello().show();
    }
    public static void dosleep(MyFunctionalInterface mif){
        mif.mymethod();
    }

    public void sleep(){
        System.out.println("this class is sleeping now");
    }

    public void show(){
        dosleep(()->{
            new Hello().sleep();
        });
        //this class is sleeping now

        dosleep(this::sleep);
        //this class is sleeping now
    }

}
```

5）类的构造器引用

```java
public class Person{
    String name;
    int age;

    public Person() {
        super();
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

}
```

```java
public class Hello{
    public static void main(String[] args) {
        new_person("zhanghuan",25,(name,age)->{
            return new Person(name,age);
        });
        //Person{name='zhanghuan', age=25}
        
        new_person("zhangh",22,Person::new);
        //Person{name='zhangh', age=22}
    }
    public static void new_person(String name,int age,MyFunctionalInterface mif){
        Person p=mif.mymethod2(name,age);
        System.out.println(p);
    }

}
```

6）数组构造器引用

```java
public class Hello{
    public static void main(String[] args) {
        new_array(10,(length)->{
            return new int[10];
        });
        //数组的长度为：10
        
        new_array(10,int[]::new);
        //数组的长度为：10
    }
    public static void new_array(int length,MyFunctionalInterface mif){
        int[] arr=mif.mymethod3(length);
        System.out.println("数组的长度为："+arr.length);
    }

}
```

---

**Stream流**

将集合和数组转换成流，用lambda表达式进行操作，简化对数组或集合的操作。在JDK1.8后出现，关注做什么而不是怎么做

`1`对集合数据进行筛选的案例

1）传统方式

```java
public class Hello {
    public static void main(String[] args) {
        ArrayList<String> names=new ArrayList<>();
        names.add("赵丽颖");
        names.add("张三丰");
        names.add("张无忌");
        names.add("张飞");
        names.add("马超");

        ArrayList<String> arr1=fileter_name_length_gt_2(names);//过滤出长度大于2的姓名
        System.out.println(arr1);//[赵丽颖, 张三丰, 张无忌]
        ArrayList<String> arr2=fileter_name_contains_zhang(arr1);//在第一过滤的基础上，过滤出包含"张"的姓名
        System.out.println(arr2);//[张三丰, 张无忌]
    }
    public static ArrayList<String> fileter_name_length_gt_2( ArrayList<String> ar){
        ArrayList<String> arr=new ArrayList<>();
        for (String item:
             ar) {
            if(item.length()>2){
                arr.add(item);
            }
        }
        return arr;
    }
    public static ArrayList<String> fileter_name_contains_zhang( ArrayList<String> ar){
        ArrayList<String> arr=new ArrayList<>();
        for (String item:
                ar) {
            if(item.contains("张")){
                arr.add(item);
            }
        }
        return arr;
    }
}
```

2）使用流进行过滤

```java
import java.util.ArrayList;
import java.util.stream.Stream;

public class Hello {
    public static void main(String[] args) {
        ArrayList<String> names=new ArrayList<>();
        names.add("赵丽颖");
        names.add("张三丰");
        names.add("张无忌");
        names.add("张飞");
        names.add("马超");

        Stream<String> stm=names.stream();
        stm.filter(str->str.length()>2).filter(str->str.contains("张")).forEach(name->System.out.println(name));
        /*执行结果
        张三丰
        张无忌
         */
    }

}
```

`2`流的理论知识

1）流式模型

建立一条生产线，在生产线上进行每一步操作来生产产品，一般有过滤、映射、跳过、计数四个流程

2）流的来源和特征

流来源于集合和数组，有两个特征：pipelining（多个操作串成一条管道）、内部迭代

`3`获取流

1）Collection的Stream方法

```
default Stream<E> stream();
```

2）Strem接口的静态方法of

```
 public static<T> Stream<T> of(T... values)
```

参数为可变参数，即数组

```java
import java.util.*;
import java.util.stream.Stream;

public class Hello {
    public static void main(String[] args) {
        //第一种方式获取流
        List<String> list=new ArrayList<>();
        Stream<String> stm1=list.stream();

        Set<String> set=new HashSet<>();
        Stream<String> stm2=set.stream();

        Map<String,String> map=new HashMap<>();
        Set<String> keysets=map.keySet();
        Stream<String> stm3=keysets.stream();

        Collection<String> values=map.values();
        Stream<String> stm4=values.stream();

        Set<Map.Entry<String,String>> entries=map.entrySet();
        Stream<Map.Entry<String,String>> stm5=entries.stream();

        //第二种方式获取流
        Stream<String> stm6=Stream.of("a","b","c");

        String[] str_arry={"a","b","c"};
        Stream<String> stm7=Stream.of(str_arry);
    }

}
```

`4`常用方法

分为延迟方法和终结方法；延迟方法是指返回值是Strem接口自身类型的方法，支持链式调用；终结方法是指返回值不是Strem接口自身类型的方法，不支持链式调用；

1）foreach遍历

```
void forEach(Consumer<? super T> action);//终结方法
```

2）fileter过滤

```
Stream<T> filter(Predicate<? super T> predicate);
```

Steam流属于管道流，只可被消费一次

3）map映射

将一个流映射(转换)成另一流

```
<R> Stream<R> map(Function<? super T, ? extends R> mapper)
```

4）count计数

```
long count();//终结方法
```

5）limit截取前几个元素

```
Stream<T> limit(long maxSize);
```

6）skip跳过前几个元素

```
Stream<T> skip(long n);
```

7）Stream接口中的静态方法concat合并流

```
public static <T> Stream<T> concat(Stream<? extends T> a, Stream<? extends T> b);
```

8）统计计算

(1)定义实体类

```java
package com.zhanghuan.scheduler;


public class Book {
    private int price;

    public int getPrice() {
        return price;
    }

    public void setPrice(int price) {
        this.price = price;
    }
}

```

(2)使用

```java
package com.zhanghuan.scheduler;

import java.util.ArrayList;
import java.util.IntSummaryStatistics;
import java.util.List;
import java.util.stream.Collectors;


public class Test {

    @org.junit.jupiter.api.Test
    public void test(){
        List<Book> books = new ArrayList<>();
        for(int i=0;i<100;i++){
            Book book = new Book();
            book.setPrice(i);
            books.add(book);
        }
        IntSummaryStatistics statistics = books.stream().collect(Collectors.summarizingInt(Book::getPrice));
        System.out.println(statistics.getAverage());
        System.out.println(statistics.getCount());
        System.out.println(statistics.getMax());
        System.out.println(statistics.getMin());
        System.out.println(statistics.getSum());
    }
}

```

