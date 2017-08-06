# part V

## 分离Isolates

现在的网页浏览器，甚至是移动平台上的，运行在多核 CPU 之上。为了充分利用多核心的优势，开发人员通常对共享内存的线程采取并行策略。然而，在共享状态下使用并发容易出错并且会使代码复杂化。
Dart 在代码中使用 isolates 来替代线程。每个 isolate 有自己的内存堆，以确保 isolate 的状态不能被其他任何 isolate 访问。

---

## 类型定义Typedefs

在 Dart 中，方法是对象，就像字符串和数字也是对象。typedef ,又被称作函数类型别名，让你可以为函数类型命名，并且该命名可以在声明字段和返回类型的时候使用。当一种函数类型被分配给一个变量的时候，typedef 会记录原本的类型信息。
考虑下面的代码，哪一个没有使用 typedef：

```dart
class SortedCollection {
  Function compare;

  SortedCollection(int f(Object a, Object b)) {
    compare = f;
  }
}

 // Initial, broken implementation.
 int sort(Object a, Object b) => 0;

main() {
  SortedCollection coll = new SortedCollection(sort);

  // All we know is that compare is a function,
  // but what type of function?
  assert(coll.compare is Function); // true
}
```
当 f 分配到 compare 的时候类型信息丢失了。f的类型是 (Object, Object) → int(→ 意味着返回的)，然而compare 的类型是方法。如果我们使用显式的名字更改代码并保留类型信息，则开发者和工具都可以使用这些信息。

```dart
typedef int Compare(Object a, Object b);

class SortedCollection {
  Compare compare;

  SortedCollection(this.compare);
}

 // Initial, broken implementation.
 int sort(Object a, Object b) => 0;

main() {
  SortedCollection coll = new SortedCollection(sort);
  assert(coll.compare is Function); // true
  assert(coll.compare is Compare); // true
}
```
> 目前 typedefs 仅限于函数类型，以后可能会有所改变。

typedefs 是简单的别名，所以它提供了一种方法来检查任何函数的类型。比如：

```dart
typedef int Compare(int a, int b);

int sort(int a, int b) => a - b;

main() {
  assert(sort is Compare); // True 
}
```

---

## 元数据Metadata

使用元数据来给你的代码提供附加信息。
元数据注解以 @ 字符开头，后面跟一个编译时的常量引用（例如 deprecated）或者调用常量构造器的语句。
所有的 Dart 代码中支持三个注解：@deprecated，@override 和@proxy。@override 和@proxy的用法示例，请查看[类的继承](https://www.dartlang.org/guides/language/language-tour#extending-a-class)。以下是 @deprecated 用法的示例：

```dart
class Television {
  // Deprecated: Use [turnOn] instead
  @deprecated
  void activate() {
    turnOn();
  }

  // Turns the TV's power on.
  void turnOn() {
    print('on!');
  }
}
```
你可以定义你自己的元数据注解。下面的例子定义了一个有两个参数的 @todo 注解：

```dart
library todo;

class todo {
  final String who;
  final String what;

  const todo(this.who, this.what);
}
```
下面是使用 @todo 注解的例子：

```dart
import 'todo.dart';

@todo('seth', 'make this do something')
void doSomething() {
  print('do something');
}
```
元数据可以出现在库、类、typedef、类型参数、构造器、工厂、函数、属性、参数、变量声明、import 或 export 指令之前。你可以在运行时通过反射来取回元数据。

---

## 注释Comments

Dart 支持单行注释、多行注释和文档注释。
- 单行注释由 // 开始

- 多行注释由 /* 开始，由 */ 结束，多行注释可以嵌套

- 文档注释是由 /// 或 /\*\* 开始的多行或单行注释
  在连续的行上使用 /// 的效果等同于多行注释。
  在一段文档注释中，Dart 编译器忽略所有除括号内的文本。你可以使用括号来引用类、方法、属性、顶级变量、函数和参数。括号中的名字会在被文档化程序元素的词法范围内解析。
  下面是一个引用了其它类和参数的文档注释的例子：

```dart
/// A domesticated South American camelid (Lama glama).
///
/// Andean cultures have used llamas as meat and pack
/// animals since pre-Hispanic times.
class Llama {
  String name;

  /// Feeds your llama [Food].
  ///
  /// The typical llama eats one bale of hay per week.
  void feed(Food food) {
    // ...
  }

  /// Exercises your llama with an [activity] for
  /// [timeLimit] minutes.
  void exercise(Activity activity, int timeLimit) {
    // ...
  }
}
```
在生成的文档中， [food] 变成了指向 Food 类的 API 文档连接。

---

**更多内容**
- [文章](https://www.dartlang.org/articles)
- [代码用法示例](https://www.dartlang.org/dart-vm/dart-by-example)
- [Dart核心库](https://www.dartlang.org/guides/libraries/library-tour)
- [Effective Dart](https://www.dartlang.org/guides/language/effective-dart)
- [Futures教程](https://www.dartlang.org/tutorials/language/futures)
- [Streams教程](https://www.dartlang.org/docs/tutorials/streams)
