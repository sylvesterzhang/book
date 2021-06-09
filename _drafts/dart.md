**安装**

`1`dart环境

1）windows

下载地址

https://gekorm.com/dart-windows/

2）苹果

https://brew.sh

```
brew tap dart-lang/dart
brew install dart
```

`2`配置vscode

1）安装dart插件

2）安装code runner

---

**Helloword**

```dart
main() {
  print("are you ok");
}
```

---

**定义变量**

1）var声明

```dart
void main() {
  //var 声明变量，自动推断变量类型
  var str = "are you ok";
  var num = 1234;
  print(str);
  print(num);
}
```

2）定义常量

```dart
void main() {
  //通过const定义常量
  const a = "abc";
  print(a);

  //通过final定义常量
  final b = "efg";
  print(b);
}

```

final和const区别

```dart
void main() {
  final now = new DateTime.now(); //用const会报错
  print(now);
}
```

final和const都是在被赋值后，不可再去赋值。区别是final可以在声明后不进行赋值，调用时再赋值，const在声明后必须被赋值，否则会报错。

3）变量类型

(1)字符串

```dart
void main() {

  //1.字符串

  //1）单引号和双引号一样
  String str1 = "abcdefg";
  String str2 = "abc";
  print(str1);
  print(str2);
  print(str1+str2);//字符串拼接
  //2）多行文本
  String str3 = '''
  are
  you
  ok
  ''';
  print(str3);
  
}

```

(2)数字

```dart
void main() {
  //2.数字

  //1）int
  int a = 123;
  a = 40;
  print(a);
  //2）double 既可以赋值整形，又可以赋值浮点型
  double b = 22.1;
  b = 24; //24.0
  b = 11.5;
  print(b);

  //计算
  print(a + b); //结果为浮点型，51.5
}

```

(3)布尔

```dart
void main() {
  //3.布尔值

  bool b = true;
  print(b);

  var num1 = 123;
  var num2 = "123";
  print(num1 == num2);//false

}

```

(4)列表

```dart
void main() {
  //4.列表

  //定义方式1
  List list = ["abc", 123];
  print(list);

  //定义方式2
  List list1 = new List();
  list1.add("abc");
  list1.add(123);
  print(list1);

  //指定类型
  List list2 = new List<String>();
  list2.add("aaa");
  list2.add("bbb");
  print(list2);
}

```

```dart
void main() {
  List myList = [];
  myList.add("value1");
  myList.addAll(["value2", "value3"]);//拼接数组
  print(myList.length); //1
  print(myList.isEmpty); //false
  print(myList.isNotEmpty); //true
  print(myList.indexOf("value1")); //0 value1在列表中的位置
  myList.removeAt(1);//删除下标为1的数据
  myList.fillRange(1,3,"222");//将下标1，3的数据修改为222
  myList.insert(1,"aaa");//在下标1的位置插入一条数据aaa
  myList.insertAll(1,["aaa","bbb"]);//在下标1的位置插入列表

  var newList = myList.reversed.toList(); //列表倒序转换成新列表
  print(newList);
}

```

转换和切割

```dart
void main() {
  //切割
  var str = "香蕉-苹果-西瓜";
  var list = str.split("-");
  print(list);

  //拼接
  var str1 = list.join("%%");
  print(str1);

  //set和list相互转换
  list.toSet(); //转换成set，去掉重复的数据
}

```

(5)map

```dart
void main() {
  //4.map

  //定义方式1
  var map = {"name": "zhanghuan", "age": 22};
  print(map);

  //定义方式2
  Map map1 = new Map();
  map1["name"] = "zhanghuan";
  map1["age"] = 22;
  print(map1);

  //判断类型
  print(map1 is Map); //true
}

```

4）类型转换

```dart
void main() {
  //字符串转数字，空字符串会报错
  String str = '123';
  int num = int.parse(str);

  //数字转字符串
  int num1 = 10;
  String str1 = num1.toString();

  //判断字符串是否为空
  String str2 = "";
  bool empty = str2.isEmpty;

  //NaN类型
  var myNum = 0 / 0;
  if (myNum.isNaN) {
    print("NaN");
  }
}

```

5）++a和a++

```dart
void main() {
  var a;
  var b;

  a = 10;
  b = a++;
  print(b); //10

  a = 10;
  b = ++a;
  print(b); //11
}

```



---

**命名规则**

(1)变量名由数字、字母、下划线和美元符号组成，不可以数字开头

(2)标识符不能是保留字和关键字

(3)变量名区分大小写

(4)标识符一定要见名思意，变量名用名词，方法名用动词

4）运算符

| 运算符 | 含义       |
| ------ | ---------- |
| =      | 赋值       |
| ??=    | 非空赋值   |
| +=     | 加完后赋值 |
| !=     | 不等于     |
| ==     | 等于       |
| >      | 大于       |
| <      | 小于       |
| >=     | 大于等于   |
| <=     | 小于等于   |
| !      | 取反       |
| &&     | 并且       |

三元运算符

```dart
void main() {
  var flag = true;
  var c = flag ? 'flag是true' : 'flag是false';
  print(c);
}
```

非空赋值

```dart
void main() {
  var a;
  var b = a ?? 10;//判断a是否为空，如果a为空会将10赋值给b
  print(b);
}

```

---

**控制语句**

1）if…else…

```dart
void main() {
  
  var sex = "男";
  if (sex == "男") {
    print("男");
  } else {
    print("女");
  }
  
}
```

2）switch…case…

```dart
void main() {
  
  var sex = "男";
  switch (sex) {
    case "男":
      print("男");
      break;
    case "女":
      print("女");
      break;
    default:
      print("参数错误");
  }

}
```

3）for循环

```dart
void main() {
  for (var i = 0; i < 10; i++) {
    print(i);
  }
}

```

4）while循环

```dart
void main() {
  int i = 0;
  while (i < 10) {
    print(i);
    i++;
  }
}

```

---

**常用方法**

1）forEach

```dart
void main() {
  var list = ["a", "b", "c", "d"];
  list.forEach((element) {
    print(element);
  });
}

```

2）where

```dart
void main() {
  var list = ["a", "b", "c", "d"];
  var res = list.where((element) => element.isNotEmpty);//将满足条件的过滤出来生成一个新集合
  print(res); //(a, b, c, d)
}

```

3）any

```dart
void main() {
  var list = ["a", "b", "c", "d"];
  var res = list.any((element) => element.isNotEmpty);//有一个不为空（满足条件）返回true
  print(res); //true
}

```

4）every

```dart
void main() {
  var list = ["a", "b", "c", "d"];
  var res = list.every((element) => element.isNotEmpty);//每一个不为空返回true
  print(res); //true
}

```

---

**方法**

dart语言中类似C语言，需要有main方法

作用域类似js语言

可选参数

```dart
void main() {
  printuserInfo("zhanghuan");//姓名：zhanghuan年龄为：保密
  printuserInfo("zhanghuan", 22);//姓名：zhanghuan年龄为：22
}

String printuserInfo(String name, [int age]) {
  if (age != null) {
    print("姓名：${name}" + "年龄为：${age}");
  } else {
    print("姓名：${name}" + "年龄为：保密");
  }
}

```

具名参数

```dart
void main() {
  printuserInfo("zhanghuan");
  printuserInfo("zhanghuan", age: 22); //参数必须以这种形式传递
}

String printuserInfo(String name, {int age}) {
  if (age != null) {
    print("姓名：${name}" + "年龄为：${age}");
  } else {
    print("姓名：${name}" + "年龄为：保密");
  }
}

```

箭头函数

```dart
void main() {
  List list = ["苹果", "香蕉", "栗子"];
  list.forEach((element) {
    print(element);
  });
  //改为箭头函数
  list.forEach((element) => print(element));
}

```

匿名函数

```dart
void main() {
  var fnc = () {
    print("object");
  };
  fnc();
}

```

自执行方法

```dart
void main() {
  (() {
    print("object");
  })();
}

```

闭包

```dart
void main() {
  var fuc = () {
    var a = 1;
    return () {
      a++;
      print(a);
    };
  };

  var b = fuc();
  b(); //2
  b(); //3
}

```

函数内嵌套一个函数，并将此函数返回，里面的变量具有局部变量和全局变量的特点

---

**面向对象**

`1`构造函数简写

```dart
class Person {
  var name;
  var age;
  Person(String name) {
    this.name = name;
    this.age = age;
  }
}
```

```dart
class Person {
  var name;
  var age;
  //构造函数简写
  Person(this.name, this.age);
}
```

默认构造函数只可写一个，不支持重载

`2`命名构造函数

```dart
class Person {
  var name;
  var age;
  Person(this.name, this.age);
  Person.now() {
    print("这是命名构造函数");
  }
}
```

命名构造函数可以写多个

`3`类的导入

将每一个类单独放在一个dart文件内，便于维护，通过import关键字导入

```dart
import 'lib/Person.dart';

void main(){
    Person p1=new Person();
    p1.printInfo();
}
```

`4`属性、方法的私有

dart语言中没有public、private等关键字修饰方法、属性，如果要将方法、属性定义成私有，在方法名或属性名前加上"_"，并且类必须单独存在于一个文件中，否则不生效

```dart
class Person {
  var _name;
  var _age;
  Person(this._name, this._age);
  Person.now() {
    print("这是命名构造函数");
  }
  info() {
    print("我是${_name},年龄${_age}");
  }
}

```

`5`访问属性方式访问计算值

一般情况想要在类里进行属性值计算，需要定义一个方法，外部再调用该类实例的方法，获取到计算值。但再dart里还有一种方式，可直接通过获取属性值的形似获取到计算值

```dart
class React {
  double _width;
  double _height;
  React(this._width, this._height);
  get area {
    //获取值，可以是属性值，也可以是属性的计算值
    return this._width * this._height * 0.5;
  }

  set height(double height) {
    //设置属性
    this._height = height;
  }
}

```

```dart

void main() {
  var r = new React(20, 10);
  print(r.area); //100
  r.height = 5;
  print(r.area); //50
}

```

`6`实例化之前赋值

```dart
class React {
  double _width;
  double _height;
  React():_height=10,_width=20{//实例化之间进行赋值
  }
}
```

`7`静态属性

```dart
class Person {
  static String name = "zhang";
}
void main() {
  print(Person.name); //静态属性、方法只可通过类进行访问，不可通过对象访问
}
```

在同一个类中，静态方法只可访问静态的方法、属性，不可访问非静态的方法、属性。而非静态的方法，可访问静态的方法、属性

```dart
class Person {
  static String name = "zhang";
  int age = 20;
  info() {
    print(age); //可加this，也可省略
    print(name); //不可加this
  }
}
```

```dart
void main() {
  Person p = new Person();
  p.info(); //20 zhang
}
```

`8`非空判断

```dart
import 'Person.dart';

void main() {
  Person p;
  p?.info(); //?表示判断是否为空，如果非空执行?后面的方法
}
```

`9`类型转换

```dart
import 'Person.dart';

void main() {
  var p;
  p = "";
  p = new Person();
  (p as Person).info();
}
```

`10`判断类型

```dart
import 'Person.dart';

void main() {
  var p;
  p = new Person();
  print(p is Person); //true
}

```

`11`继承

```dart
class React {
  double _width;
  double _height;
  React(height, width) {
    this._width = width;
    this._height = height;
  }
}

class NewReact extends React {
  String _color;
  NewReact(double height, double width, String color) : super(height, width) {
    this._color = color;
  }
}

void main() {
  var react = new NewReact(10, 20, "red");
}
```

```dart
class Animal {
  run() {
    print("animal is runing");
  }
}

class Huamn extends Animal {
  @override
  run() {//覆盖父类方法
    print("human is running");
  }
}
```

