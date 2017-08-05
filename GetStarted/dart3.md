# part III

## 类
Dart 是一种面向对象语言，包含类和基于 mixin 的继承两部分。每个对象是一个类的实例，并且 Object 是所有类的父类。基于 mixin 的继承指的是每个类（除了 Object ）都只有一个父类，类体还可以在多个类继承中被重用。

要创建一个对象，你可以使用 new 关键词并在其后跟上一个构造函数。构造函数可以写成<类名>或者<类名.标识符>形式。例如:

```dart
var jsonData = JSON.decode('{"x":1, "y":2}');

//用 Point() 创建一个点。
var p1 = new Point(2, 2);

// 用 Point().fromJson() 创建一个点。
var p2 = new Point.fromJson(jsonData);
```
对象的成员分为函数和数据两类（各自的方法和实例变量）。当你调用一个方法时，通过一个对象来调用它的：该方法可访问该对象的方法和数据。用 . 指向对象的方法和数据成员:
```dart
var p = new Point(2, 2);

// 给 y赋值。
p.y = 3;

// 获取 y 的值。
assert(p.y == 3);

// 用 p 对象调用 distanceTo() 。
num distance = p.distanceTo(new Point(4, 4));

// 使用?.代替.来避免操作对象为null时产生的异常
p?.y = 4; // 如果p不为空则设置p.y等于4
```
当你想对一个对象的成员进行一系列操作时，用级联操作（ cascade ）：
```dart
querySelector('#button') // 获取一个对象。
    ..text = 'Confirm'   // 调用他的成员。
    ..classes.add('important')
    ..onClick.listen((e) => window.alert('Confirmed!'));
```
一些类提供常量构造函数，要创建一个编译时常量构造函数，使用 const 关键字代替 new ：
```dart
var a = const ImmutablePoint(1, 1);
var b = const ImmutablePoint(1, 1);

assert(identical(a, b)); // 他们是相同的实例！
```
如果想在运行时获取对象的类型，可以使用runtimeType属性：
```dart
print('The type of a is ${a.runtimeType}');
```

**实例变量**
声明实例变量：
```dart
class Point {
  num x; // 声明实例变量 x ，默认值为 null 。
  num y; // 声明实例变量 y ，默认值为 null 。
  num z = 0; // 声明实例变量 z ，初始化为 0 。
}
```
所有的实例变量会自动生成一个隐式的 getter 方法。 Non-final 实例变量也会自动生成一个隐式的 setter 方法。有关详细信息，参见[getter&setter](https://www.dartlang.org/guides/language/language-tour#getters-and-setters)。

```dart
class Point {
  num x;
  num y;
}

main() {
  var point = new Point();
  point.x = 4;          // 用 setter 方法得到 x 。
  assert(point.x == 4); // 用 getter 方法得到 x 。
  assert(point.y == null); // 值为 null 。
}
```

**构造函数**
要声明一个构造函数，只需创建一个与类同名的方法（或者加上一个额外的标识符命名构造函数的描述）。构造函数最常见的形式，就是自动生成的构造函数，下面创建一个类的新实例：
```dart
class Point {
  num x;
  num y;

  Point(num x, num y) {
    this.x = x;
    this.y = y;
  }
  // 有个更好的方法来实现。用语法糖来设置x，y: Point(this.x, this.y)
}
```
- 默认构造函数:如果你不声明一个构造函数，系统会提供默认构造函数。默认构造函数没有参数，它将调用父类的无参数构造函数。

- 子类不继承父类的构造函数。子类只有默认构造函数。（无参数，没有名字的构造函数）

- 命名构造函数。使用命名构造函数可以为一个类声明多个构造函数，或者说是提供额外的声明
  ```dart
  class Point {
  num x;
  num y;

  Point(this.x, this.y);

  // 命名构造函数
  Point.fromJson(Map json) {
    x = json['x'];
    y = json['y'];
  }
  }
  ```
- 调用非默认的父类的构造函数。默认情况下，在子类的构造函数将会调用父类的无参数默认构造函数。如果父类没有构造函数，则必须手动调用父类的构造函数中的一个。在冒号（：）之后、构造函数之前指定父类的构造函数（如果有的话）。
```dart
class Person {
  String firstName;
  Person.fromJson(Map data) {
    print('in Person');
  }
}

class Employee extends Person {
  // Person没有默认构造函数，子类必需调用super.<命名构造函数>
  Employee.fromJson(Map data) : super.fromJson(data) {
    print('in Employee');
  }
}

main() {
  var emp = new Employee.fromJson({});
  // 输出:
  // in Person
  // in Employee
  if (emp is Person) { // 类型检查
    emp.firstName = 'Bob';
  }
  (emp as Person).firstName = 'Bob';
}
```
在调用父类构造函数前会检测参数，这个参数可以是一个表达式作为函数调用：

```dart
class Employee extends Person {
  // ...
  Employee() : super.fromJson(findDefaultData());
}
```
⚠️警告：父类构造函数的参数不能访问this 。例如，参数可调用静态方法但是不能调用实例方法。

**初始化列表**

除了调用父类构造函数，你也可以在构造函数体运行之前初始化实例变量。用逗号隔开使其分别初始化。

```dart
class Point {
  num x;
  num y;

  Point(this.x, this.y);

  // 初始化列表在构造函数运行前设置实例变量。

  Point.fromJson(Map jsonMap)
      : x = jsonMap['x'],
        y = jsonMap['y'] {
    print('In Point.fromJson(): ($x, $y)');
  }
}
```
⚠️警告：右手边的初始化程序无法访问 this 关键字

以下示例在初始化程序列表中初始化三个final字段
```dart
import 'dart:math';

class Point {
  final num x;
  final num y;
  final num distanceFromOrigin;

  Point(x, y)
      : x = x,
        y = y,
        distanceFromOrigin = sqrt(x * x + y * y);
}

main() {
  var p = new Point(2, 3);
  print(p.distanceFromOrigin);  // 输出3.605551275463989
}
```

