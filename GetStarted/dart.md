# Dart语法概览

## 代码示例
```dart
// 定义一个方法
printNumber(num aNumber) {
  print('The number is $aNumber.');
}

// 主函数
main() {
  var number = 42;
  printNumber(number);
}
```

## 重要概念
- 所有变量都可以看作对象(object)，每个对象都是一个类的实例。
  包括numbers、function和null都是对象。所有对象都继承自Object类。
- 指定静态类型（比如代码示例中的num）表明你想指定的变量用途。（未指定具体类型的变量在debug时会发现被指定为一个特殊的类型：dynamic）
  为确保代码类型安全，查看[稳定模式Dart](https://www.dartlang.org/guides/language/sound-dart)
- Dart在运行前会解析所有代码。你可以先准备几点，比如使用类型和编译常量来捕获errors或者使代码运行更快。
- Dart支持顶层方法，比如main()。也可以绑定在一个类或者对象上（分别为静态和实例方法）。也可以在方法中定义方法。
- 同理，Dart也支持顶层变量，并且可以绑定到类或对象（静态和实例变量）。实例变量也称作字段或者属性。
- 不同于Java，Dart没有关键字public、protected和private。如果一个标识符以下划线_开头，则它的库使私有的。详见[库和可见性](https://www.dartlang.org/guides/language/language-tour#libraries-and-visibility)
- 标识符以字母和下划线_开头，接着字母和数字的任意组合
- Dart工具可以报告两种问题：警告Warnings和错误errors。
- Dart有两种运行模式：生产 (production) 和检查 (checked)。一般在检查模式开发和调试，最终部署到生产模式
- Production mode是Dart程序一个速度优化的默认运行模式。Production mode忽略断言语句（assert statements）和静态类型
- Checked mode是开发人员友好的方式，可以帮助你在运行时捕捉一些类型的错误。例如，如果分配一个非数字来声明为一个num变量，然后在检查模式会抛出异常

## 变量
```dart
var name = 'Voyager I';
var year = 1977;
var antennaDiameter = 3.7;
var flybyObjects = ['Jupiter', 'Saturn', 'Uranus', 'Neptune'];
var image = {
  'tags': ['saturn'],
  'url': '//path/to/saturn.jpg'
};
```
- 默认值。未初始化的变量值为null，即使指定了数字类型也是null，因为numbers也是对象。
```dart
int lineCount;
assert(lineCount == null);
// Variables (even if they will be numbers) are initially null.
// assert(condition)方法只在检查模式下有用，表达式的值或者函数返回true，则assert语句成功并继续执行代码。如果值为false，则assert语句失败并抛出一个异常 (AssertionError)
```
- Final和const。一个final变量只能设定一次且不能更改，一个const变量是编译时变量（const变量是隐式final变量）。
  一个final的顶层变量或者类变量，会在它第一次被调用时初始化。
```dart
final name = 'Bob'; // Or: final String name = 'Bob';
name = 'Alice';  // Uncommenting this causes an error
```
```dart
// []创建一个空数组，const []创建一个不可变空数组(EIA)
var foo = const [];   // foo当前是EIA
final bar = const []; // bar一直是EIA
const baz = const []; // baz是编译时的EIA

// 可以改变非final、const定义的变量，即使它之前是赋的const值，比如foo
foo = [];

// 但bar、baz不能更改
```
- 可选的静态类型。变量类型包括：numbers、strings、booleans、lists（也就是数组）、maps、runs、symbols。每个变量类型有自定义的构造函数，比如new Map()创建一个map变量。

**数字类型numbers**分int（-2e53～2e53）和double型（IEEE 754标准）,支持abs()、ceil()等[math函数](https://api.dartlang.org/stable/dart-math/dart-math-library.html)。
int型支持位运算，如<<、>>、& 和 | 
```dart
// 类型转换
// String -> int
var one = int.parse('1');
assert(one == 1);

// String -> double
var onePointOne = double.parse('1.1');
assert(onePointOne == 1.1);

// int -> String
String oneAsString = 1.toString();
assert(oneAsString == '1');

// double -> String
String piAsString = 3.14159.toStringAsFixed(2);
assert(piAsString == '3.14');
```

**字符串类型Strings**是UTF-16代码单元的序列，可以使用单引号或双引号创建一个字符串。

可以通过使用 ${expression} 把一个表达式的值放进字符串。如果表达式是一个标识符，你可以跳过{}。为了获得相应对象的字符串，Dart 调用对象的 toString（）方法。

```dart
var s = 'string interpolation';

assert('Dart has $s, which is very handy.' ==
       'Dart has string interpolation, ' +
       'which is very handy.'); // true
assert('That deserves all caps. ' +
       '${s.toUpperCase()} is very handy!' ==
       'That deserves all caps. ' +
       'STRING INTERPOLATION is very handy!'); // true
```
可以利用相邻字符串或+运算符连接字符串：
```dart
var s1 = 'String ' 'concatenation'
         " works even over line breaks.";
assert(s1 == 'String concatenation works even over '
             'line breaks.'); // true

var s2 = 'The + operator '
         + 'works, as well.';
assert(s2 == 'The + operator works, as well.'); //true
```
使用三个单或双引号创建一个多行字符串
```dart
var s1 = '''
You can create
multi-line strings like this one.
''';

var s2 = """This is also a multi-line string.""";
```
带有 r 的前缀来创建一个“raw”的字符串
```dart
var s = r"In a raw string, even \n isn't special.";
```
支持Unicode 转义
```dart
print('Unicode escapes work: \u2665');
```

**布尔类型booleans**
不同于javaScript，1、“aString”、someObject 都视为假的
```dart
var name = 'Bob';
if (name) {
  // JavaScript中会产生打印，而Dart中不会
  print('in javaScript, You have a name!');
} else {
  print('but in Dart, print this line')
}
// 在Dart生产模式下，上述条件为假；而在检查模式下，程序会抛出一个异常，因为name类型不是bool
if (1) {
  print('JS prints this line.');
} else {
  print('Dart in production mode prints this line.');
  // 但在checked模式下会抛出一个异常，因为1不是一个boolean
}
```
Dart的判断条件应该指定为布尔类型，不能使用if（nonbooleanValue），这个在检查模式下会抛出异常
```dart
// 检查一个空字符串.
var fullName = '';
assert(fullName.isEmpty);

// 检查为零.
var hitPoints = 0;
assert(hitPoints <= 0);

// 检查是否为空.
var unicorn;
assert(unicorn == null);

// 检查NaN.
var iMeantToDoThis = 0 / 0;
assert(iMeantToDoThis.isNaN);
```

**列表类型List**
在Dart，数组是列表对象，所以我们通常只是将其称为lists。
常见用法与javaScript类似：
```dart
var fruits = ['apples', 'oranges']; // 也可以用构造函数new List('apples', 'oranges')
// 如果指定类型, new List<String>('apples')或者<String>['apples']则只能存放字符类型, 否则在检查模式下加入非指定类型元素会抛出异常

fruits.add('kiwis'); // 添加元素

fruits.addAll(['grapes', 'bananas']); // 添加多个元素

assert(fruits.length == 5); // 获取长度

fruits.forEach((item) => print('I have some $item')) // forEach((key, value) => {})可以传两个参数

fruits.map((item) => print('I have some $item'))

fruits.where((item) => item === 'oranges') // where()筛选符合条件的元素

// 删除一个元素
var appleIndex = fruits.indexOf('apples');
fruits.removeAt(appleIndex);
assert(fruits.length == 4);

// 清空列表
fruits.clear();
assert(fruits.length == 0);
```
更多用法详见[泛型Generics](http://wiki.jikexueyuan.com/project/dart-language-tour/generics.html)和[集合Collections](https://www.dartlang.org/docs/dart-up-and-running/ch03.html#collections)。
**Maps类型**
一般情况下，map是一个键值对组成的对象，这两个键和值可以是任何类型的对象。
```dart
var gifts = {
  'first' : 'partridge',
  'second': 'turtledoves',
  'fifth' : 'golden rings'
};

var nobleGases = {
  2 :   'helium',
  10:   'neon',
  18:   'argon',
};

/* -----也可以使用构函数----- */
var gifts = new Map();
gifts['first'] = 'partridge'; // 直接添加键值对
gifts['second'] = 'turtledoves';
gifts['fifth'] = 'golden rings';

var nobleGases = new Map();
nobleGases[2] = 'helium';
nobleGases[10] = 'neon';
nobleGases[18] = 'argon';

assert(nobleGases.length == 3) // length返回map中键值对的数目
```
**符号类型symbols**
一个符号对象表示在Dart程序中声明的操作符或标识。你可能不会需要使用这些符号，但他们对于由名字指向的API是很有用的，因为时常要改变的是标识符的名字，而不是标识符的符号。

为了得到符号的标识，使用符号的文本，只是＃后跟标识符：
```dart
#radix
#bar
```
更多详细信息见[ Dart：镜子 - 反射](https://www.dartlang.org/docs/dart-up-and-running/ch03.html#dartmirrors---reflection)
