# part IV

## 泛型Generics

如果你在API文档寻找基本数组类型或者 List 类型，你将会看到该类型实际上为List<E>,其中<...>标记表示此表为一个泛型类型（或为参数化结构）—— 一种含有正规类型参数的类型。按照惯例，类型变量通常为单字符名称，例如E, T, S, K 以及 V。

**为何要使用泛型？**

因为在Dart中类型是可选的，你不一定要使用泛型。或许你想用，可是，因为一些相同的原因你会想在代码中使用其他的类型：这些类型（泛型或者其他类型）可以记录并注释你的代码，使你的意图更加清晰。

比如，如果你打算使用一个仅仅包含字符串的List，你可以声明它为List<String>（可理解为“字符串类型组成的List”），通过这种方式，你的程序员同事，以及你的工具（比如Dart编辑器和调试模式下的Dart虚拟机）能检测到将一个非字符串的变量分配到List中很可能是错误的，这里给出一个样例：

```dart
var names = new List<String>();
names.addAll(['Seth', 'Kathy', 'Lars']);
// ...
names.add(42); // 在调试模式中失败 (在生产模式中成功)
```
另外一个使用泛型的原因是为了减少代码的重复。泛型可以让你能共享多个类型的一个接口和实现方式，它在调试模式以及静态分析的错误预警中仍然很有优势。举个例子，当你在创建一个接口来缓存一个对象时：

```dart
abstract class ObjectCache{
  object getByKey(String key);   
  setByKey(String key,Object value);   
} 
```
你发现你想要一个字符串专用的接口，所以你创建了另外一个接口：

```dart
abstract class StringCache{
  string getByKey(String key);
  setByKey(String key,String value);
}
```
当你想要一个这种接口的数字专用的接口，又要创建不同类型的接口。
泛型类型可以减少你创建这些接口的麻烦。取而代之的是，你只需要创建一个带有一个类型参数的接口即可：

```dart
abstract class Cache<T>{
  T getByKey(String key);
  setByKey(String key,T value);
}
```
在这个代码中，T是一个替代类型，即占位符，你可以将他视为后续被开发者定义的类型。

**使用集合常量**

Lis常量以及map常量都能被参数化，参数常量就像你已经见过的常量那样，除非你在左 方括号之前添加<type>(对于List）或者<keyType,valuetype>(对于map）。当你需要避免调试模式下的类型警告，你或许可以使用参数常量。这里有一个使用常量类型的例子：

```dart
var names = <String>['Seth', 'Kathy', 'Lars'];
var pages = <String, String>{
  'index.html': 'Homepage',
  'robots.txt': 'Hints for web robots',
  'humans.txt': 'We are people, not machines'
};
```
**使用带构造器的参数化类型**

为了在使用构造器时详细说明一个或多个类型，将类型放入类名后的三角括号（<...>）中，举个例子：

```dart
var names = new List<String>();
names.addAll([‘Seth’, ‘Kathy’ , ‘Lars’]);
var nameSet = new Set<String>.from(names);
```
下列代码创建了一个含有整型的键以及值为View的map：

```dart
var views = new Map<int,view>();
```
**泛型集合及其包含的类型**

Dart泛型类型是被具体化的，意思就是它们在整个运行时间中都携带着类型信息。举个例子，你可以测试一个集合中的类型甚至是在生产模式中：

```dart
var names = new List<String>();
names.addAll(['Seth', 'Kathy', 'Lars']);
print(names is List<String>); // true
```
然而，is表达式检查的仅仅是集合中的类型并不是其中的对象。在生产模式下，一个List<String>中可能含有一些非字符项，解决方法可以是逐项检查其类型或者在异常处理程序中加入数据项操作代码(参见[异常]())。

> 相比之下，Java中的泛型使用擦除，这意味着通用类型参数在运行时被删除。在Java中，您可以测试对象是否为List，但是不能测试它是否为List <String>

**限制参数化类型**

实现通用类型时，可能需要限制其参数的类型。你可以使用extends来做到这一点

```dart
// T必须是某个类或者类的后代
class Foo<T extends SomeBaseClass> {...}

class Extender extends SomeBaseClass {...}

void main () {
  // 在<>中使用某个类或者其子类都可以
  var someBaseClassFoo = new Foo<SomeBaseClass>();
  var extenderFoo = new Foo<Extender>();

  // 不使用<>也可以
  var foo = new Foo();

  // 指定任何非SomeBaseClass类型会导致警告（检查模式中是运行时错误runtime error）
  // var objectFoo = new Foo<Object>();
}
```
**使用泛型函数**

最初，Dart的泛型支持仅限于类。一种较新的语法（称为泛型方法，SDK1.21之后）允许在方法和函数上使用类型参数：

```dart
T first<T>(List<T> ts) {
  // ...一些初始化工作或者错误检查...
  T tmp = ts[0];
  // ...做一些额外的检查或处理...
  return tmp;
}
```
泛型参数（<T>）允许你在多个位置使用类型参数 T：
- 函数的返回类型(T)
- 参数类型 (List<T>)
- 局部变量类型 (T tmp)
  更多关于泛型函数，详见[泛型方法](https://github.com/dart-lang/sdk/blob/master/pkg/dev_compiler/doc/GENERIC_METHODS.md)

---

## 库和可见性Libraries and visibility

import，library指令可以帮助创建一个模块化的，可共享的代码库。库不仅提供了API，还提供隐私单元：以下划线( _ )开头的标识符只对内部库可见。每个Dartapp就是一个库，即使它不使用库指令。

库可以分布式使用包。见 [Pub Package and Asset Manager](https://www.dartlang.org/tools/pub) 中有关pub(SDK中的一个包管理器）。

**使用库**

使用 import 来指定如何从一个库命名空间用于其他库的范围。

```dart
import 'dart:html'; // Dart Web应用一般采用这个库 dart:html
```
唯一需要 import 的参数是一个指向库的 URI。对于内置库，URI中具有特殊dart: scheme。对于其他库，你可以使用文件系统路径或package: scheme。包 package：scheme 指定由程序包管理器（如pub工具）提供的库。例如:

```dart
import 'dart:io';
import 'package:mylib/mylib.dart';
import 'package:utils/utils.dart';
```
> 注：URI代表统一资源标识符。网址（统一资源定位器）是一种常见的URI的。

**指定库前缀**

如果导入两个库是有冲突的标识符，那么你可以指定一个或两个库的前缀。例如，如果 library1 和 library2 都有一个元素类，那么你可能有这样的代码：

```dart
import 'package:lib1/lib1.dart';
import 'package:lib2/lib2.dart' as lib2;
// ...
var element1 = new Element(); // 使用lib1里的元素
var element2 = new lib2.Element();       // 使用lib2里的元素
```
**导入部分库**

如果想使用的库一部分，你可以选择性导入库。例如：

```dart
// 只导入foo库
import 'package:lib1/lib1.dart' show foo;

//导入所有除了foo
import 'package:lib2/lib2.dart' hide foo;
```
**延迟加载库**

延迟(deferred)加载（也称为延迟(lazy)加载）允许应用程序按需加载库。下面是当你可能会使用延迟加载某些情况：
- 为了减少应用程序的初始启动时间
- 例如，要执行A / B测试( A/B testing) - 尝试算法的替代实现
- 加载很少使用的功能，例如可选的屏幕和对话框

为了延迟加载一个库，你必须使用 deferred as 先导入它:

```dart
import 'package:deferred/hello.dart' deferred as hello;
```
当需要库时，使用该库的调用标识符调用 LoadLibrary（）:

```dart
greet() async {
  await hello.loadLibrary();
  hello.printGreeting();
}
```
在上述代码中，await关键字将暂停执行，直到库被加载。更多关于 async 和 await 详见[异步支持](https://www.dartlang.org/guides/language/language-tour#asynchrony-support)

您可以在库中多次调用loadLibrary( )。该库仅加载一次。
当您使用延迟加载，要注意一下几点：
- 延迟库的常量在其作为导入文件时不是常量。记住，这些常量不存在，直到迟库被加载完成。
- 你不能在导入文件中使用延迟库常量的类型。相反，考虑将接口类型移到同时由延迟库和导入文件导入的库。
- Dart隐式地将loadLibrary（）插入到使用deferred作为命名空间定义的命名空间中。调用LoadLibrary（）函数返回一个Future对象（类似于JS的promise对象）。

**库的实现**

详见[创建库文件包](https://www.dartlang.org/guides/libraries/create-library-packages)

---

## 异步的支持Asynchrony support

Dart 添加了一些新的语言特性用于支持异步编程。最通常使用的特性是 async 方法和 await 表达式。Dart 库大多方法返回 Future 和 Stream 对象。这些方法是异步的：它们在设置一个可能的耗时操作（比如 I/O 操作）之后返回，而无需等待操作完成。

当你需要使用 Future 来表示一个值时，你有两个选择：
- 使用 async 和 await
- 使用 [Future API](https://www.dartlang.org/guides/libraries/library-tour#future)

同样的，当你需要从 Stream 获取值的时候，你有两个选择:
- 使用 async 和一个异步的 for 循环 (await for)
- 使用 [Stream API](https://www.dartlang.org/guides/libraries/library-tour#stream)

要使用 await，代码必须用 async 标记函数:

```dart
checkVersion() async {
  var version = await lookUpVersion();
  if (version == expectedVersion) {
    // Do something.
  } else {
    // Do something else.
  }
}
```
你可以使用 try, catch, 和 finally 来处理错误并清理使用了 await 的代码:

```dart
try {
  server = await HttpServer.bind(InternetAddress.LOOPBACK_IP_V4, 4044);
} catch (e) {
  // 响应无法绑定到端口...
}
```
**声明异步函数**

一个异步函数是一个由 async 修饰符标记的函数。虽然一个异步函数可能在操作上比较耗时，但是它可以在任何方法体执行之前立即返回。

```dart
checkVersion() async {
  // ...
}

lookUpVersion() async => /* ... */;
```
在函数中添加关键字 async 使得它返回一个 Future对象。
比如，考虑一下这个同步函数，它将返回一个字符串：
String lookUpVersionSync() => '1.0.0';
更改它成为异步方法-因为在之后的实现中将会非常耗时-它的返回值是一个 Future 对象：
Future<String> lookUpVersion() async => '1.0.0';
请注意函数体不需要使用 Future API，如果必要的话 Dart 将会自己创建 Future 对象。

**使用带 future 的 await 表达式**

在异步方法中你可以使用 await 多次。比如，下列代码为了得到函数的结果一共等待了三次：

```dart
var entrypoint = await findEntrypoint();
var exitCode = await runExecutable(entrypoint, args);
await flushThenExit(exitCode);
```
在 await 表达式中， 表达式 的值通常是一个 Future 对象；如果不是，那么这个值会自动转为 Future。这个 Future 对象表明了表达式应该返回一个对象。await 表达式 的值就是返回的一个对象。在对象可用之前，await 表达式将会一直处于暂停状态。

如果 await 没有起作用，请确认它是一个异步方法。比如，在你的 main() 函数里面使用await，main() 的函数体必须被 async 标记:

```dart
main() async {
  checkVersion();
  print('In main: version is ${await lookUpVersion()}');
}
```

**结合 streams 使用异步循环**

一个异步循环具有以下形式：

```dart
await for (variable declaration in expression) {
  // 每次stream发出一个值时执行
}
```
表达式 的值the value of expression必须有Stream 类型（流类型）。执行过程如下：
- 在 stream 发出一个值之前等待
- 执行 for 循环的主体，把变量设置为发出的值
- 重复 1 和 2，直到 Stream 关闭

如果要停止监听 stream ，你可以使用 break 或者 return 语句，跳出循环并取消来自 stream 的订阅 。
如果一个异步 for 循环没有正常运行，请确认它是一个异步方法。 比如，在应用的 main() 方法中使用异步的 for 循环时，main() 的方法体必须被 async 标记：

```dart
main() async {
  ...
  await for (var request in requestServer) {
    handleRequest(request);
  }
  ...
}
```
更多关于异步编程的信息，请看 [dart:async](https://www.dartlang.org/guides/libraries/library-tour#dartasync---asynchronous-programming) 库的介绍。
也可以看文章 [Dart Language Asynchrony Support: Phase 1](https://www.dartlang.org/articles/language/await-async) 和 [Dart Language Asynchrony Support: Phase 2](https://www.dartlang.org/articles/language/beyond-async) 和 [the Dart language specification](https://www.dartlang.org/guides/language/spec)。

---

## 可调用类Callable classes

为了允许你的Dart类可以像函数一样被调用，实现了call（）方法。
在下面的例子中，WannabeFunction类定义了一个call（）函数，它需要三个字符串并用空格串联它们，并加上感叹号：

```dart
class WannabeFunction {
  call(String a, String b, String c) => '$a $b $c!';
}

main() {
  var wf = new WannabeFunction();
  var out = wf("Hi","there,","gang");
  print('$out');  // Hi there, gang!
}
```
更多关于类函数，详见[将类用作函数](https://www.dartlang.org/articles/language/emulating-functions)。
