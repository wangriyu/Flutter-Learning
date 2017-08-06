# part III

## 类Class
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
---

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
---

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

---

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

main () {
  var p = new Point(2,  3);
  print(p.distanceFromOrigin);  // 输出3.605551275463989
}
```
---

**重定向构造函数**

有时一个构造函数的目的只是重定向到同一个类中的另一个构造函数。如果一个重定向的构造函数的主体为空，那么调用这个构造函数的时候，直接在冒号后面调用这个构造函数即可。

```dart
class Point {
  num x;
  num y;

  // 主构造函数
  Point(this.x, this.y);

  // 主函数的委派
  Point.alongXAxis(num x) : this(x, 0);
}
```
---

**静态构造函数**

如果你的类产生的对象永远不会改变，你可以让这些对象成为编译时常量。为此，需要定义一个 const 构造函数并确保所有的实例变量都是 final 的。

```dart
class ImmutablePoint {
  final num x;
  final num y;
  const ImmutablePoint(this.x, this.y);
  static final ImmutablePoint origin =
      const ImmutablePoint(0, 0);
}
```
---

**工厂构造函数**

当实现一个使用 factory 关键词修饰的构造函数时，这个构造函数不必创建类的新实例。例如，工厂构造函数可能从缓存返回实例，或者它可能返回子类型的实例。 下面的示例演示一个工厂构造函数从缓存返回的对象：

```dart
class Logger {
  final String name;
  bool mute = false;
  static final Map<String, Logger> _cache = <String, Logger>{}; //  _cache 是一个私有库，因为名字前有个 _ 

  factory Logger (String name)  {
    if (_cache.containsKey(name)) {
      return _cache[name];
    } else {
      final logger = new Logger._internal(name);
      _cache[name] = logger;
      return logger;
    }
  }
  Logger._internal(this.name);
  void log (String msg) {
    if (!mute) {
      print(msg);
    }
  }
}
```
⚠️注：工厂构造函数不能用 this。

调用一个工厂构造函数，你需要使用 new 关键字：

```dart
var logger = new Logger('UI');
logger.log('Button clicked');
```
---

**方法**

方法就是为对象提供行为的函数。

- 实例方法
  对象的实例方法可以访问实例变量和 this 。以下示例中的 distanceTo() 方法是实例方法的一个例子：

```dart
import 'dart:math';

class Point {
  num x;
  num y;
  Point(this.x, this.y);

  num distanceTo (Point other) {
    var dx = x - other.x;
    var dy = y - other.y;
    return sqrt(dx * dx + dy * dy);
  }
}
```
setters和Getters: 是一种提供对方法属性读和写的特殊方法。每个实例变量都有一个隐式的 getter方法，如果合适的话可以加上setter方法。你可以通过实现getters和setters来创建附加属性，也就是直接使用get和set关键词：

```dart
class Rectangle {
  num left;
  num top;
  num width;
  num height;

  Rectangle(this.left, this.top, this.width, this.height);

  // 定义两个计算属性: right and bottom
  num get right             => left + width;
      set right(num value)  => left = value - width;
  num get bottom            => top + height;
      set bottom(num value) => top = value - height;
}

main () {
  var rect = new Rectangle(3, 4, 20, 15);
  assert(rect.left == 3);
  rect.right = 12;
  assert(rect.left == -8);
}
```
借助于getter和setter，你可以直接使用实例变量，并且在不改变客户代码的情况下把他们包装成方法。

⚠️注：不论是否显式地定义了一个 getter，类似增量（++）的操作符，都能以预期的方式工作。为了避免产生任何向着不期望的方向的影响，操作符一旦调用 getter ，就会把他的值存在临时变量里。

- 抽象方法
  Instance，getter和setter方法可以是抽象的，也就是定义一个接口，但是把实现交给其他的类。要创建一个抽象方法，使用分号(;)代替方法体：

```dart
abstract class Doer {
  // ...定义实例变量和方法...
  void doSomething(); // 定义一个抽象方法，没有body
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // ...提供一个实现，所以这里的方法不是抽象的...
  }
}
```
调用抽象方法会导致运行时错误。

---

**重载操作符**

你可以重写在下表中列出的操作符。例如，如果你定义了一个向量类，你可以定义一个 + 方法来加两个向量。

|  <   |  +   |  \|  |  [ ] |
| :--: | :--: | :--: | :--: |
|  >   |  /   |  ^   | [ ]= |
|  <=  |  ~/  |  &   |  ~   |
|  >=  |  *   |  <<  |  ==  |
|  -   |  %   |  >>  |      |

以下是一个类中重写 + 和 - 操作符的例子：

```dart
class Vector {
  final int x;
  final int y;
  const Vector(this.x, this.y);

  // 重写+ (a + b).
  Vector operator +(Vector v) {
    return new Vector(x + v.x, y + v.y);
  }

  // 重写- (a - b).
  Vector operator -(Vector v) {
    return new Vector(x - v.x, y - v.y);
  }
}

main () {
  final v = new Vector(2, 3);
  final w = new Vector(2, 2);

  // v == (2, 3)
  assert(v.x == 2 && v.y == 3);

  // v + w == (4, 5)
  assert((v + w).x == 4 && (v + w).y == 5);

  // v - w == (0, 1)
  assert((v - w).x == 0 && (v - w).y == 1);
}
```
Dart中的每个对象自动提供一个整数哈希码，因此可以用作map的一个键。但是，你可以重写hashCode getter来生成自定义哈希码。如果你这样做，你可能也想重写==运算符。相等的（通过==）的对象必须具有相同的哈希码。哈希码不一定是唯一的，但是它具有良好的分布性。
重写 == 和 hashCode 例子:

```dart
class Person {
  final String firstName, lastName;

  Person(this.firstName, this.lastName);

  // 使用Effective Java中的策略重写hashCode
  int get hashCode {
    int result = 17;
    result = 37 * result + firstName.hashCode;
    result = 37 * result + lastName.hashCode;
    return result;
  }

  // 如果重写了hashCode，一般也要实现‘==''
  bool operator ==(other) {
    if (other is! Person) return false;
    Person person = other;
    return (person.firstName == firstName && person.lastName == lastName);
  }
}

main () {
  var p1 = new Person('bob', 'smith');
  var p2 = new Person('bob', 'smith');
  var p3 = 'not a person';
  assert(p1.hashCode == p2.hashCode);
  assert(p1 == p2);
  assert(p1 != p3);
}
```
更多关于重载的信息，详见[扩展一个类](https://www.dartlang.org/guides/language/language-tour#extending-a-class)

---

**抽象类**

使用 abstract 修饰符来定义一个抽象类，该类不能被实例化。抽象类在定义接口的时候非常有用，实际上抽象中也包含一些实现。如果你想让你的抽象类被实例化，请定义一个 工厂构造函数。

抽象类通常包含 抽象方法。下面是声明一个含有抽象方法的抽象类的例子：

```dart
// 这个类是抽象类，因此不能被实例化。
abstract class AbstractContainer {
  // ...定义构造函数，域，方法...
  void updateChildren(); // 抽象方法。
}
```
下面的类不是抽象类，因此它可以被实例化，即使定义了一个抽象方法：

```dart
class SpecializedContainer extends AbstractContainer {
  // ...定义更多构造函数，域，方法...
  void updateChildren() {
    // ...实现 updateChildren()...
  }

  // 抽象方法造成一个警告，但是不会阻止实例化。
  void doSomething();
}
```

---

**隐式接口**

每个类隐式的定义了一个接口，含有类的所有实例和它实现的所有接口。如果你想创建一个支持类 B 的 API 的类 A，但又不想继承类 B ，那么，类 A 应该实现类 B 的接口。
一个类实现一个或更多接口通过用 implements 子句声明，然后提供 API 接口要求。例如：

```dart
// 一个 person类 ，包含 greet() 的隐式接口。
class Person {
  // 在这个接口中，只有库中可见。
  final _name;

  // 不在接口中，因为这是个构造函数。
  Person(this._name);

  // 在这个接口中。
  String greet(who) => 'Hello, $who. I am $_name.';
}

//  Person 接口的一个实现。
class Imposter implements Person {
  // 我们不得不定义它，但不用它。
  final _name = "";

  String greet(who) => 'Hi $who. Do you know who I am?';
}

greetBob(Person person) => person.greet('bob');

main () {
  print(greetBob(new Person('kathy')));  // Hello, bob. I am kathy.
  print(greetBob(new Imposter()));  // Hi bob. Do you know who I am?
}
```
一个类实现多个接口的例子：

```dart
class Point implements Comparable, Location {
  // ...
}
```

---

**扩展一个类**

使用 extends 创建一个子类，同时 supper 将指向父类：
```dart
class Television {
  void turnOn() {
    _illuminateDisplay();
    _activateIrSensor();
  }
  // ...
}

class SmartTelevision extends Television {
  void turnOn() {
    super.turnOn();
    _bootNetworkInterface();
    _initializeMemory();
    _upgradeApps();
  }
  // ...
}
```

---

**重写成员**

子类可以重载实例方法，getters方法，setters方法。可以使用 @override 注释来显示地表明你要重写的成员：

```dart
class SmartTelevision extends Television {
  @override
  void turnOn() {
    // ...
  }
  // ...
}
```
你可以使用 covariant 关键字来缩小类型安全代码中方法参数或实例变量的类型范围。
在一些（很少见到）的编码模式中使用子类型重写一个参数类型来缩小类型范围，这在强模式Dart中是非法的。在这种情况下，您可以使用协变关键字 covariant 告诉分析器您正在进行此操作。这将删除静态错误，而在运行时检查无效的参数类型。
下面是使用 covariant 的例子：

```dart
import 'package:meta/meta.dart';

class Animal {
  void chase(Animal x) {}
}

class Mouse extends Animal {}

class Cat extends Animal {
  void chase(covariant Mouse x) {}
}
```
covariant关键字可以放置在父类或子类方法中, 通常，父类方法是放置它的最佳方法。covariant关键字适用于单个参数，并且支持setters和fields。

---

**noSuchMethod()**

当代码试图用不存在的方法或实例变量时，这个方法会被调用。

```dart
class A {  // 如果你不重写 noSuchMethod 方法，就用一个不存在的成员，会导致 NoSuchMethodError 错误。
  void noSuchMethod (Invocation mirror) {
    print('You tried to use a non-existent member:' +
          '${mirror.memberName}');
  }
}
```
如果你用 noSuchMethod() 实现每一个可能的 getter 方法，setter 方法和类的方法，那么你可以使用 @proxy 标注来避免警告。

```dart
@proxy
class A {
  void noSuchMethod(Invocation mirror) {
    // ...
  }
}
```
关于注释 annotations，详见[元数据MetaData](https://www.dartlang.org/guides/language/language-tour#metadata)

---

**枚举类型**

枚举类型，通常被称为 enumerations 或 enums ，是一种用来代表一个固定数量的常量的特殊类。

- 使用枚举
  声明一个枚举类型需要使用关键字 enum ：

```dart
enum Color {
  red,
  green,
  blue
}
```
在枚举中每个值都有一个 index getter 方法，它返回一个在枚举声明中从0开始的位置。例如，第一个值索引值为0，第二个值索引值为1。

```dart
assert(Color.red.index == 0);
assert(Color.green.index == 1);
assert(Color.blue.index == 2);

// 要得到枚举列表的所有值，可使用枚举的 values 常量
List<Color> colors = Color.values;
assert(colors[2] == Color.blue);
```
你可以在 switch 语句 中使用枚举。如果 e 在 switch (e) 是显式类型的枚举，那么如果你不处理所有的枚举值将会弹出警告：

```dart
enum Color {
  red,
  green,
  blue
}
// ...
Color aColor = Color.blue;
switch (aColor) {
  case Color.red:
    print('Red as roses!');
    break;
  case Color.green:
    print('Green as grass!');
    break;
  default: // 如果没有这个分句，将会弹出警告
    print(aColor);  // 'Color.blue'
}
```
枚举类型有以下限制
1. 你不能在子类中混合或实现一个枚举
2. 你不能显式实例化一个枚举

---

**为类添加特征：mixins**

mixins 是一种多类层次结构的类的代码重用。
要使用 mixins ，在 with 关键字后面跟一个或多个 mixin 的名字。下面的例子显示了两个使用mixins的类：

```dart
class Musician extends Performer with Musical {
  // ...
}

class Maestro extends Person with Musical, Aggressive, Demented {
  Maestro(String maestroName) {
    name = maestroName;
    canConduct = true;
  }
}
```
要实现 mixin ，就创建一个继承 Object 类的子类，不声明任何构造函数，不调用 super。例如：

```dart
abstract class Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
  bool canConduct = false;

  void entertainMe() {
    if (canPlayPiano) {
      print('Playing piano');
    } else if (canConduct) {
      print('Waving hands');
    } else {
      print('Humming to self');
    }
  }
}
```
> 从1.13起，Dart VM已经取消了对mixin的两个限制：
> 1. Mixins允许继承自类class，不局限于Object
> 2. Mixins可以调用super（）
>    “super mixins” 在dart2js不受支持，并且需要在dartanalyzer中使用--supermixin标签。更多关于mixins，详见[Mixins in Dart](https://www.dartlang.org/articles/language/mixins)


**类的变量和方法**

使用 static 关键字来实现类变量和类方法。

- 静态变量
  静态变量（类变量）对于类状态和常数是有用的：

```dart
class Color {
  static const red = const Color('red');  // 一个恒定的静态变量
  final String name;      // 一个实例变量。 
  const Color(this.name);  // 一个恒定的构造函数。
}

main() {
  assert(Color.red.name == 'red');
}
```
只有当静态变量被调用时才被初始化。

- 静态方法
  静态方法（类方法）不在一个实例上进行操作，因而不必访问 this 。例如：

```dart
import 'dart:math';

class Point {
  num x;
  num y;
  Point(this.x, this.y);

  static num distanceBetween(Point a, Point b) {
    var dx = a.x - b.x;
    var dy = a.y - b.y;
    return sqrt(dx * dx + dy * dy);
  }
}

main() {
  var a = new Point(2, 2);
  var b = new Point(4, 4);
  var distance = Point.distanceBetween(a, b);
  assert(distance < 2.9 && distance > 2.8);
}
```
你可以将静态方法作为编译时常量。例如，你可以把静态方法作为一个参数传递给静态构造函数。
