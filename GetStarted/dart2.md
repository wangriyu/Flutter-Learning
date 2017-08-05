# part II

## 函数Functions

Dart是一门面向对象的语言，即使函数也是对象也有类型。这意味着函数可以分配给变量或者当作参数传给其他函数。也可以像函数一样调用一个类的实例，比如[可调用的Classes](https://www.dartlang.org/guides/language/language-tour#callable-classes)。
下面是定义了一个函数：

```dart
bool isNoble(int atomicNumber) { // 不指定函数返回类型也可以执行，但不推荐这样做
  return _nobleGases[atomicNumber] != null;
}

// 可以用箭头语法描述单个表达式，但不能使用多行的语句，比如if statement，可以用条件表达式(?:)代替if else
bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;
```

- **函数的参数有两种类型**：required 和 optional。首先列出所有必需的参数，再跟上可选的参数。

可选参数有两种指定方式：可选命名参数named和可选位置参数positional

```dart
// 定义函数时用{param1, param2, …}的形式指定named参数
enableFlags({bool bold, bool hidden}) {
  // ...
}
// 调用函数时可以使用 paramName: value的方式
enableFlags(bold: true, hidden: false);
```

用[ ]包裹可选参数：

```dart
// 定义函数时用[param1, param2, …]的形式指定positional参数
String say(String from, String msg, [String device]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  return result;
}

assert(say('Bob', 'Howdy') == 'Bob says Howdy');
assert(say('Bob', 'Howdy', 'smoke signal') ==
    'Bob says Howdy with a smoke signal');
```

- **默认参数值**

在函数定义时可以用=给参数指定默认值，这个默认值必须是编译时常量，如果没有指定默认值则为null。

下面是给named参数设置默认值的方法：

```dart
void enableFlags({bool bold = false, bool hidden = false}) {
  // ...
}
// bold will be true; hidden will be false.
enableFlags(bold: true);
```

下面是给positional参数设置默认值的方法：

```dart
String say(String from, String msg,
    [String device = 'carrier pigeon', String mood]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  if (mood != null) {
    result = '$result (in a $mood mood)';
  }
  return result;
}

assert(say('Bob', 'Howdy') ==
    'Bob says Howdy with a carrier pigeon');
```

也可以传递lists或者maps作为默认值：

```dart
void doStuff({
    List<int> list = const [1, 2, 3],
    Map<String, String> gifts = const {
      'first': 'paper',
      'second': 'cotton',
      'third': 'leather'
    }}) {
  print('list:  $list');
  print('gifts: $gifts');
}
```

- **Main()函数**

每个app都必需有一个main()函数，main()返回void而且有一个可选的 List<String>字符参数列表。

```dart
void main() {
  querySelector("#sample_text_id")
    ..text = "Click me!"
    ..onClick.listen(reverseText);
}
// ..是级联(cascade)操作符，可以对单个对象执行多个操作
```

给main()传入参数：

```dart
// 运行app时传入参数：dart args.dart 1 test
void main(List<String> arguments) {
  print(arguments);

  assert(arguments.length == 2);
  assert(int.parse(arguments[0]) == 1);
  assert(arguments[1] == 'test');
}
```

将函数作为参数传给其他函数或者赋给变量：

```dart
printElement(element) {
  print(element);
}
var list = [1, 2, 3];
list.forEach(printElement);

var loudify = (msg) => '!!! ${msg.toUpperCase()} !!!'; // 一个匿名函数
assert(loudify('hello') == '!!! HELLO !!!');
```

- **词法作用域Lexical scope**

Dart时一门词法作用域的语言，即变量的作用域是静态地决定的，由代码的布局决定的。
词法作用域的函数遇到既不是形参也不是函数内部定义的局部变量的变量时，会去函数定义时的环境中查询。

```dart
var topLevel = 1; // 如果去掉这一行，getLevel报错“No top-level getter 'topLevel' declared”
void getLevel() {
  print(topLevel);
}

main () {
  var topLevel = 2;
  getLevel(); // 输出1，因为getsome()定义时topLevel=1
}
```

- **词法闭包**

指的是一个函数可以访问其语法作用域内的变量，即使这个函数是在变量本身的作用域之外被调用的。
下面的例子中makeAdder()捕获了变量addBy，不管返回的函数在哪里被调用，它都可以使用addBy：

```dart
Function makeAdder(num addBy) {
  return (num i) => addBy + i;
}

main() {
  var add2 = makeAdder(2);
  var add4 = makeAdder(4);

  assert(add2(3) == 5);
  assert(add4(3) == 7);
}
```

- **函数的等价性测试**

下面是关于顶层函数、静态方法和实例方法的等价性测试

```dart
foo() {} // 一个顶层函数

class SomeClass {
  static void bar() {} // 一个静态方法
  void baz() {} // 一个实例方法
}

main () {
  var x;

  // 比较顶级函数
  x = foo;
  assert(foo == x); // true
     
  // 比较静态方法
  x = SomeClass.bar;
  assert(SomeClass.bar == x); // true

  // 比较实例方法
  var v = new SomeClass(); // SomeClass的实例1
  var w = new SomeClass(); // SomeClass的实例2
  var y = w;
  x = w.baz;

  // 这些闭包引用了相同的示例对象（A的实例2），所以它们是等价的
  assert(y.baz == x); // true

  // 这些闭包引用的是不同实例，所以它们不等价
  assert(v.baz != w.baz); // true
}
```

- **函数返回值**

所有函数都会返回一个值，如果没有指定返回值，函数将会在函数体末尾隐式地添加“return null”

```dart
void baz() {}
main () {
  print(baz()); // 输出null
}
```

---

## 操作符Operators

Dart定义了以下操作符，你可以[重写这些操作符](https://www.dartlang.org/guides/language/language-tour#overridable-operators)。

|   介绍    | 符号                                       |
| :-----: | :--------------------------------------- |
|  一元后缀符  | expr++  、  expr--  、  ()  、  []  、  .  、  ?. |
|  一元前缀符  | -expr  、  !expr  、  ~expr  、  ++expr  、  —expr |
|  乘法类型   | *  、  /  、  %  、  ~/                     |
|  加法类型   | +  、  -                                  |
|  位操作符   | <<  、  >>                                |
|   按位与   | &                                        |
|  按位异或   | ^                                        |
|   按为或   | \|                                       |
| 比较和类型测试 | >=  、  >  、  <=  、  <  、  as  、  is  、  is! |
|   等价    | ==  、  !=                                |
|   逻辑与   | &&                                       |
|   逻辑或   | \|\|                                     |
| null分配符 | ??                                       |
|  条件运算符  | expr1 ? expr2 : expr3                    |
|  级联运算符  | ..                                       |
|   赋值    | =  、  *=  、  /=  、  ~/=  、  %=  、  +=  、  -=  、  <<=  、  >>=  、  &=  、  ^= |

**算术运算符**中 ／ 返回double型，～／返回整型的除数，%返回除法的余数：

```dart
assert(5 / 2 == 2.5); // 结果是double类型
assert(5 ~/ 2 == 2); // 结果是一个整数
assert(5 % 2 == 1); // 余数

print('5/2 = ${5~/2} 余 ${5%2}'); // 5/2 = 2 余 1
```

**自增自减**

```dart
var a, b;

a = 0; 
b = ++a; // 在b获得其值前先自增a
assert(a == b); // 1 == 1

a = 0;
b = a++; // 在b获得其值后自增a
assert(a != b); // 1 != 0

a = 0;
b = --a; // 在b获得其值前自减a
assert(a == b); // -1 == -1

a = 0;
b = a--; // 在b获得其值后自减a
assert(a != b); // -1 != 0
```

**类型测试操作符**:

- as 类型转换
- is 当对象是相应类型时返回 true
- is！当对象不是相应类型时返回 true
  如果obj实现了T所定义的借口，那么obj is T将返回 true。比如，obj is Object必然返回 true。
  使用as操作符可以把一个对象转换为特定类型。一般来说，如果在is测试之后还有一些关于对象的表达式，你可以把as当做是is测试的一种简写:

```dart
if (emp is Person) { // 类型检查
  emp.firstName = 'Bob';
}
// as化简代码
(emp as Person).firstName = 'Bob';
```

上面两段代码并不相等。如果emp的值为 null 或者不是 Person 的一个对象，第一段代码不会做任何事情，第二段代码将会报错 。

**分配符**

```dart
a = value;   // 将value赋给a
b ??= value; // 如果b为null将value赋给b，否则b值不变
```

**条件表达式**

- condition ? expr1 : expr2条件为真返回表达式1，否则返回表达式2
- expr1 ?? expr2表达式1为null则返回表达式2，否则返回表达式1

```dart
String toString() => msg ?? super.toString();

// 等价于
String toString() => msg == null ? super.toString() : msg;
```

**级联操作符**

允许你在单个对象的成员上执行多个操作

```dart
querySelector('#button') // Get an object.
  ..text = 'Confirm'   // Use its members.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'));

// 等价于
var button = querySelector('#button');
button.text = 'Confirm';
button.classes.add('important');
button.onClick.listen((e) => window.alert('Confirmed!'));
```

级联操作符可以嵌套

```dart
final addressBook = (
  new AddressBookBuilder()
  ..name = 'jenny'
  ..email = 'jenny@example.com'
  ..phone = (
    new PhoneNumberBuilder()
    ..number = '415-555-0100'
    ..label = 'home'
  ).build()
).build();
```

在返回对象的函数上使用级联要格外注意，下面的例子中sb.write()返回void，不能在void上构建级联

```dart
var sb = new StringBuffer();
sb.write('foo')..write('bar'); // 无效
```

严格意义上讲，级联操作符..不算一个操作符，它属于Dart语法的一部分，应该算作一个语法

**其他操作符**

- 一个点.代表成员访问，比如foo.bar从foo中选择了bar属性
- 问号加上一个点?.代表条件成员访问，?.之前的操作数可以为空，比如foo?.bar从foo中选择属性bar，当foo为空时则不访问bar

---

## 控制流语句
- if else
- for循环
- while和do while循环
- break和continue
- switch case
- assert

**for循环**

Dart的for循环闭包中可以获取index的值，就像下面的例子输出下标0，1，2；而在javaScript中，下面的例子输出3，3，3，如果想输出0，1，2需要把var改成let

```dart
// in Dart
var callbacks = [];
for (var i = 0; i < 3; i++) {
  callbacks.add(() => print(i));
}
callbacks.forEach((c) => c()); // 输出0，1，2

// in javaScript
var callbacks = [];
for (var i = 0; i < 3; i++) { // 改成let输出0，1，2
  callbacks.add(() => print(i));
}
callbacks.forEach((c) => c()); // 输出3，3，3
```

可迭代的元素可以用forEach()方法遍历，也可以用for-in

```dart
var collection = [0, 1, 2];
for (var x in collection) {
  print(x);
}
```

**break和continue**

用break退出循环，循环结束

```dart
while (true) {
  if (shutDownRequested()) break;
  processIncomingRequests();
}
```

用continue跳出这一次循环，循环不结束

```dart
for (int i = 0; i < candidates.length; i++) {
  var candidate = candidates[i];
  if (candidate.yearsExperience < 5) {
    continue;
  }
  candidate.interview();
}
```

通过迭代可以简化上述代码

```dart
candidates.where((c) => c.yearsExperience >= 5).forEach((c) => c.interview());
```

**转换语句**

可以使用continue和标签来跳转

```dart
var command = 'CLOSED';
switch (command) {
  case 'CLOSED':
    executeClosed();
    continue nowClosed;
nowClosed:
  case 'NOW_CLOSED':
    // Runs for both CLOSED and NOW_CLOSED.
    executeNowClosed();
    break;
}
```

一个case分句可以含有局部变量，该局部变量仅仅只在此分句范围内可见

**assert**

如果一个布尔条件值为false，使用assert语句来中断正常执行的代码。在assert语句后面的括号中，你可以加入任何表示布尔值或者函数的表达式。如果表达式的值或者函数返回值true，则assert语句成功并继续执行代码。如果值为false，则assert语句失败并抛出一个异常 (an AssertionError)

```dart
//确保这个变量不为空值.
assert(text != null);

//确保这个变量小于100.
assert(number < 100);

//确保它是一个https协议类型的URL.
assert(urlString.startsWith(‘https’));
```

⚠️注意：assert语句仅仅只能在调试模式下使用，在生产模式下没有任何作用。

要将消息附加到断言，添加一个字符串作为第二个参数，当第一个参数为false时第二个参数会随错误一起抛出

```dart
assert(urlString.startsWith('https'), 'URL ($urlString) should start with "https".');
```

---

## 异常

**throw语句**

抛出了一个异常：

```dart
throw new FormatException('Expected at least 1 section');
```

也可以将任意对象作为异常抛出：

```dart
throw 'Out of llamas!';
```

因为抛出异常的语句是个表达式，所以可以写在箭头函数里：

```dart
distanceTo(Point other) => throw new UnimplementedError();
```

**on catch语句**

捕获一个异常:

```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  buyMoreLlamas();
}
```

为了处理含有多种类型异常的代码，你可以选择多个catch子句。第一个匹配抛出对象类型的catch子句将会处理这个异常。如果catch子句未说明所捕获的异常类型，这个子句就可处理任何被抛出的对象:

```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // 一个具体异常
  buyMoreLlamas();
} on Exception catch (e) {
  // 任意一个异常
  print('Unknown exception: $e');
} catch (e，s) {
  // 非具体类型
  print('Exception details:\n $e');
  print('Stack trace:\n $s');
}
```

catch()可以带两个参数，第二个参数代表堆栈跟踪[stack trace](https://api.dartlang.org/stable/dart-core/StackTrace-class.html)。

要处理部分异常，同时允许它传播，可以使用rethrow关键字：

```dart
final foo = '';

void misbehave() {
  try {
    foo = "You can't change a final variable's value.";
  } catch (e) {
    print('misbehave() partially handled ${e.runtimeType}.');
    rethrow; // Allow callers to see the exception.
  }
}

void main() {
  try {
    misbehave();
  } catch (e) {
    print('main() finished handling ${e.runtimeType}.');
  }
}
```

上面的例子输出两条print语句，如果去掉rethrow则只输出第一个print语句

**finally语句**

如果没有 catch 匹配子句的异常， finally子句运行以后异常将被传播：

```dart
try {
  breedMoreLlamas();
} finally {
  // 即使抛出一个异常时也会进行清理
  cleanLlamaStalls();
}

try {
  breedMoreLlamas();
} catch(e) {
  print('Error: $e');  // 先处理异常
} finally {
  cleanLlamaStalls();  // 然后清理
}
```
