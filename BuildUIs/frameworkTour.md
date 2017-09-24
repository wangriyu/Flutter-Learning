# 了解框架

- 简介Introduction
- 示例Hello World
- 基础控件Basic widgets
- 使用Material Design
- 处理手势Handling gestures
- 响应用户输入Changing widgets in response to input
- 一个包含上述介绍内容的示例Bringing it all together
- 响应控件生命周期事件Responding to widget lifecycle events

## 简介Introduction

Flutter控件是使用现代化的响应式风格框架构建的，其灵感来自react。

核心思想是通过 **控件[widgets](https://docs.flutter.io/flutter/widgets/Widget-class.html)** 来构建你的UI。

控件通过当前的配置configuration和状态state来告诉视图应该如何显示。
当一个控件的状态改变时，控件会重建其描述description，而框架framework会跟之前的描述做比对以确定底层渲染树中所需的最小变化来更新视图，
然后将状态转换到下个状态。类似于Virtual DOM的作用。

> 如果你想通过深入一些代码来更好地了解Flutter，查看[ Building Layouts in Flutter](https://flutter.io/tutorials/layout)和[Adding Interactivity to Your Flutter App](https://flutter.io/tutorials/interactive)

## 示例Hello World

一个最简单的app只需要一个控件调用 runApp 函数：

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(new Center(child: new Text('Hello, world!')));
}
```
runApp函数接受指定的控件(Widget)，并使其作为控件树(widget tree)的根控件。
控件(Widget)定义一个元素(Element)的配置，在Flutter框架的层次结构中处于核心层。
本实例中，控件树(widget tree)包含两个控件，Center控件使其子控件处于中间位置，Text控件打印文本内容。runApp函数强制将根控件覆盖屏幕，上述实例结果是屏幕中央显示语句“Hello, world!”。

在写应用程序时，经常会使用[StatelessWidget](https://docs.flutter.io/flutter/widgets/StatelessWidget-class.html)和[StatefulWidget](https://docs.flutter.io/flutter/widgets/StatefulWidget-class.html)
编写新控件，两者的差别在于你是否要管理控件的状态。一个控件的主要任务是实现build函数，定义控件中其他较低层次的控件。框架将依次构建这些控件，直到底层渲染对象。

## 基础控件Basic widgets

建议看一下文章：[布局模版](https://flutter.io/widgets/layout)。里面介绍了布局控件，比如Center。

Flutter配有一套功能强大的基本控件，下面的是其中经常用到的控件：
- **文本[Text](https://docs.flutter.io/flutter/widgets/Text-class.html)**：Text控件允许你在一个程序中创建样式文本（styled text）
- **行[Row](https://docs.flutter.io/flutter/widgets/Row-class.html)、列[Column](https://docs.flutter.io/flutter/widgets/Column-class.html)**：这两个控件允许你在水平Row和垂直Column方向上创建灵活的布局，其设计基于弹性盒布局模型（flexbox）
- **堆栈[Stack](https://docs.flutter.io/flutter/widgets/Stack-class.html)**：不同于上面的线性布局，Stack控件允许你按绘图顺序堆放控件。然后使用[Positioned](https://docs.flutter.io/flutter/widgets/Positioned-class.html)控件来定位Stack中的子控件，由top、right、bottom、left定位相对于Stack边缘的距离。Stack控件的设计基于web端absolute定位的布局模型。
- **容器[Container](https://docs.flutter.io/flutter/widgets/Container-class.html)**：Container控件允许你创建一个矩形可视元素。一个容器可以用[BoxDecoration](https://docs.flutter.io/flutter/painting/BoxDecoration-class.html)设置样式，比如背景background、边框border、阴影shadow。一个容器也可以设置margins、padding、大小限制。除此之外，容器可以使用矩阵在三维空间中进行变换。

下面是一些简单控件的应用，包含了上述控件和其他一些控件：

```dart
import 'package:flutter/material.dart';

class MyAppBar extends StatelessWidget {
  MyAppBar({this.title});

  // Widget子类中的字段始终标记为“final”
  final Widget title;

  @override
  Widget build (BuildContext context) {
    return new Container(
      height: 56.0, // in logical pixels
      padding: const EdgeInsets.symmetric(horizontal: 8.0),
      decoration: new BoxDecoration(color: Colors.blue[500]),
      // Row是一个水平方向的线性布局
      child: new Row(
        // <Widget>是列表元素的类型
        children: <Widget>[
          new IconButton(
            icon: new Icon(Icons.menu),
            tooltip: 'Navigation menu',
            onPressed: null, // null代表button不可用
          ),
          // Expanded扩展其子项以填充可用空间
          new Expanded(
            child: title,
          ),
          new IconButton(
            icon: new Icon(Icons.search),
            tooltip: 'Search',
            onPressed: null,
          ),
        ],
      ),
    );
  }
}

class MyScaffold extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // Material是UI出现的纸质风格概念
    return new Material(
      // Column是一个垂直线性布局
      child: new Column(
        children: <Widget>[
          new MyAppBar(
            title: new Text(
              'Example title',
              style: Theme.of(context).primaryTextTheme.title,
            ),
          ),
          new Expanded(
            child: new Center(
              child: new Text('Hello, world!'),
            ),
          ),
        ],
      ),
    );
  }
}

void main() {
  runApp(new MaterialApp(
    title: 'My app', // 由OS任务切换器使用
    home: new MyScaffold(),
  ));
}
```

确保在pubspec.yaml文件中的flutter部分设置了uses-material-design: true，这样才允许你使用预定义的一套[material icons](https://design.google.com/icons/)

```dart
name: my_app
flutter:
  uses-material-design: true
```
许多控件需要在[MaterialApp](https://docs.flutter.io/flutter/material/MaterialApp-class.html)内部才能正确显示，
因为这样才能继承主题数据，因此上面的例子中用MaterialApp做根控件。

**MyAppBar**控件创建了一个Container(容器)，高度为56设备无关像素(device-independent pixels)，内部左右填充（padding）8像素(pixels)。
容器内部，MyAppBar为子控件设置Row(水平)布局，中间的title控件被设置成[Expanded](https://docs.flutter.io/flutter/widgets/Expanded-class.html)，Expanded的作用是展开Row、Column和Flex的子控件，意味它可以使用剩余的所有空间。

**MyScaffold**控件为子控件设置垂直布局，在垂直顶部放置一个MyAppBar的实例，将MyAppBar的Text控件作为标题使用，将控件作为参数传递给其他控件非常方便实用的，你可以创建通用的控件，以各种方式重复的使用。最后，MyScaffold使用Expanded，用一个中心文本来填充剩余的空间。

## 使用Material Design

Material Design风格控件：[Widgets Overview - Material Design Widgets](https://flutter.io/widgets/material)

Flutter提供了许多控件，可以帮助您构建Material Design风格的应用程序。Material Design应用程序从[MaterialApp](https://docs.flutter.io/flutter/material/MaterialApp-class.html)控件开始，它在应用程序的根目录下构建了一些有用的控件，其中包括导航器[Navigator](https://docs.flutter.io/flutter/widgets/Navigator-class.html)，该导航器管理由字符串（也称为“路由”）标识的控件栈。导航器允许您在应用程序的屏幕之间平滑地过渡。使用MaterialApp控件是完全可选的，但这是一个很好的实践。

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(new MaterialApp(
    title: 'Flutter Tutorial',
    home: new TutorialHome(),
  ));
}

class TutorialHome extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // Scaffold is a layout for the major Material Design widgets.
    return new Scaffold(
      appBar: new AppBar(
        leading: new IconButton(
          icon: new Icon(Icons.menu),
          tooltip: 'Navigation menu',
          onPressed: null,
        ),
        title: new Text('Example title'),
        actions: <Widget>[
          new IconButton(
            icon: new Icon(Icons.search),
            tooltip: 'Search',
            onPressed: null,
          ),
        ],
      ),
      // body is the majority of the screen.
      body: new Center(
        child: new Text('Hello, world!'),
      ),
      floatingActionButton: new FloatingActionButton(
        tooltip: 'Add', // used by assistive technologies
        child: new Icon(Icons.add),
        onPressed: null,
      ),
    );
  }
}
```
现在我们已经从MyAppBar和MyScaffold切换到了material.dart中的[AppBar](https://docs.flutter.io/flutter/material/AppBar-class.html)和[Scaffold](https://docs.flutter.io/flutter/material/Scaffold-class.html)控件，我们的应用程序现在看起来更像Material Design。例如，应用程序顶部栏有一个阴影，标题文本会自动继承正确的样式。我们还添加了一个浮动动作按钮方便添加操作。

注意我们再次将控件作为参数传递给其他控件。 Scaffold控件采用许多不同的控件作为命名参数，每个控件都放置在Scaffold布局中的适当位置。类似地，AppBar控件中我们传入了其他控件：[leading](https://docs.flutter.io/flutter/material/AppBar-class.html#leading)、[title](https://docs.flutter.io/flutter/material/AppBar-class.html#title)和[action](https://docs.flutter.io/flutter/material/AppBar-class.html#actions)。这种模式在整个框架中还会重现，并且是设计自定义控件时可能会用到的。

## 处理手势Handling gestures

详细文章：[Gestures in Flutter](https://flutter.io/gestures/)

大部分应用程序都包含一些指定形式的用户交互，比如单击、双击、拖动等。构建交互式应用程序的第一步是检测输入手势。让我们看看如何通过创建一个简单的按钮：

```dart
class MyButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new GestureDetector(
      onTap: () {
        print('MyButton was tapped!');
      },
      child: new Container(
        height: 36.0,
        padding: const EdgeInsets.all(8.0),
        margin: const EdgeInsets.symmetric(horizontal: 8.0),
        decoration: new BoxDecoration(
          borderRadius: new BorderRadius.circular(5.0),
          color: Colors.lightGreen[500],
        ),
        child: new Center(
          child: new Text('Engage'),
        ),
      ),
    );
  }
}
```
[GestureDetector](https://docs.flutter.io/flutter/widgets/GestureDetector-class.html)控件没有视图显示，只是检测用户做出的手势。当用户点击[Container](https://docs.flutter.io/flutter/widgets/Container-class.html)时，GestureDetector将调用其`onTap`回调，然后将消息打印到控制台。您可以使用GestureDetector来检测各种输入手势，包括点击，拖动和缩放等。

许多控件都使用GestureDetector为其他控件提供可选的回调。例如，[IconButton](https://docs.flutter.io/flutter/material/IconButton-class.html)，[RaisedButton](https://docs.flutter.io/flutter/material/RaisedButton-class.html)和[FloatingActionButton](https://docs.flutter.io/flutter/material/FloatingActionButton-class.html)具有在用户点击小部件时触发onPressed的回调。

## 输入时动态改变控件Changing widgets in response to input

详细文章：[StatefulWidget](https://docs.flutter.io/flutter/widgets/StatefulWidget-class.html)、[State.setState](https://docs.flutter.io/flutter/widgets/State/setState.html)

在此之前，我们只使用了无状态的控件[StatelessWidget](https://docs.flutter.io/flutter/widgets/StatelessWidget-class.html)。StatelessWidget从其父元素控件接收参数，它们存储在[final](https://www.dartlang.org/docs/dart-up-and-running/ch02.html#final-and-const)成员变量中。当一个控件被请求构建时，它将使用这些存储的值来为其创建的控件派生新的参数。
为了构建更复杂的体验 - 例如，为了更好地响应用户输入 - 应用程序通常会承载一些状态。 Flutter使用StatefulWidgets来完成这个要求。 StatefulWidgets是特殊的控件，它们可以生成状态对象，然后用于保持状态state。思考下面这个例子，使用的是前面提到的[RaisedButton](https://docs.flutter.io/flutter/material/RaisedButton-class.html)：

```dart
class Counter extends StatefulWidget {
  // This class is the configuration for the state. It holds the
  // values (in this nothing) provided by the parent and used by the build
  // method of the State. Fields in a Widget subclass are always marked "final".

  @override
  _CounterState createState() => new _CounterState();
}

class _CounterState extends State<Counter> {
  int _counter = 0;

  void _increment() {
    setState(() {
      // This call to setState tells the Flutter framework that
      // something has changed in this State, which causes it to rerun
      // the build method below so that the display can reflect the
      // updated values. If we changed _counter without calling
      // setState(), then the build method would not be called again,
      // and so nothing would appear to happen.
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    // This method is rerun every time setState is called, for instance
    // as done by the _increment method above.
    // The Flutter framework has been optimized to make rerunning
    // build methods fast, so that you can just rebuild anything that
    // needs updating rather than having to individually change
    // instances of widgets.
    return new Row(
      children: <Widget>[
        new RaisedButton(
          onPressed: _increment,
          child: new Text('Increment'),
        ),
        new Text('Count: $_counter'),
      ],
    );
  }
}
```
你可能想知道为什么StatefulWidget和State是独立的对象。在Flutter中，这两种类型的对象具有不同的生命周期。控件是临时对象，用于在当前状态下构建应用程序的视图呈现。而状态对象在调用[build()](https://docs.flutter.io/flutter/widgets/State/build.html)期间是持久存在的，允许它们存储信息。

上面的示例接受用户输入，并直接在其构建方法中使用结果。在更复杂的应用程序中，控件层次结构的不同部分可能会对不同的问题负责;例如，一个控件可能呈现复杂的用户界面，目的是收集特定信息，例如日期或位置，而另一个控件可能会使用该信息来更改整体的呈现。

在Flutter中，更改通知会通过回调方式“上调”控件层次结构，而当前的状态“向下”流向执行呈现的无状态窗口控件StatelessWidgets。重定向此流程的公共父项是状态。让我们看看在实际中如何工作，这里是稍微更复杂的例子：

```dart
class CounterDisplay extends StatelessWidget {
  CounterDisplay({this.count});

  final int count;

  @override
  Widget build(BuildContext context) {
    return new Text('Count: $count');
  }
}

class CounterIncrementor extends StatelessWidget {
  CounterIncrementor({this.onPressed});

  final VoidCallback onPressed;

  @override
  Widget build(BuildContext context) {
    return new RaisedButton(
      onPressed: onPressed,
      child: new Text('Increment'),
    );
  }
}

class Counter extends StatefulWidget {
  @override
  _CounterState createState() => new _CounterState();
}

class _CounterState extends State<Counter> {
  int _counter = 0;

  void _increment() {
    setState(() {
      ++_counter;
    });
  }

  @override
  Widget build(BuildContext context) {
    return new Row(children: <Widget>[
      new CounterIncrementor(onPressed: _increment),
      new CounterDisplay(count: _counter),
    ]);
  }
}
```
注意上例中如何创建两个新的无状态小部件，清晰地分离显示计数器（CounterDisplay）和更改计数器（CounterIncrementor）的问题。尽管最终结果与上上个例子相同，但分离各部分允许将更大的复杂性功能封装在各个控件中，同时保持父级的简洁。

## 将以上内容结合在一起Bringing it all together

让我们考虑一个更复杂的例子，结合上面介绍的概念。我们将与一个假想的购物应用程序一起工作，该应用程序显示提供的各种产品，并维护购物车以进行预期购买。我们首先定义我们的显示类，ShoppingListItem：

```dart
class Product {
  const Product({this.name});
  final String name;
}

typedef void CartChangedCallback(Product product, bool inCart);

class ShoppingListItem extends StatelessWidget {
  ShoppingListItem({Product product, this.inCart, this.onCartChanged})
      : product = product,
        super(key: new ObjectKey(product));

  final Product product;
  final bool inCart;
  final CartChangedCallback onCartChanged;

  Color _getColor(BuildContext context) {
    // The theme depends on the BuildContext because different parts of the tree
    // can have different themes.  The BuildContext indicates where the build is
    // taking place and therefore which theme to use.

    return inCart ? Colors.black54 : Theme.of(context).primaryColor;
  }

  TextStyle _getTextStyle(BuildContext context) {
    if (!inCart) return null;

    return new TextStyle(
      color: Colors.black54,
      decoration: TextDecoration.lineThrough,
    );
  }

  @override
  Widget build(BuildContext context) {
    return new ListTile(
      onTap: () {
        onCartChanged(product, !inCart);
      },
      leading: new CircleAvatar(
        backgroundColor: _getColor(context),
        child: new Text(product.name[0]),
      ),
      title: new Text(product.name, style: _getTextStyle(context)),
    );
  }
}
```
ShoppingListItem控件遵循无状态控件的常见模式。它将其在构造函数中接收的值存储在final成员变量中，然后在其构建函数build中使用它们。例如，inCart布尔值在两个视觉外观之间切换：一个使用当前主题的主要颜色，另一个使用灰色。

当用户点击列表项时，控件不会直接修改其inCart值。相反，控件调用从其父级接收的onCartChanged函数。此模式可让您将状态存储在控件层次结构中，从而导致状态持续较长时间。在极端情况下，存储在传递给runApp的控件上的状态在应用程序的生命周期中会持续存在。

当父级接收到onCartChanged回调时，父级将更新其内部状态，这将触发父级重建并使用新的inCart值创建一个新的ShoppingListItem实例。虽然父级重建时创建了一个新的ShoppingListItem实例，但该操作实际上很小，因为框架将新构建的控件与先前构建的控件进行比较，并且仅将差异的部分应用于底层渲染对象。

一个存储可变状态的示例父级控件：

```dart
class ShoppingList extends StatefulWidget {
  ShoppingList({Key key, this.products}) : super(key: key);

  final List<Product> products;

  // The framework calls createState the first time a widget appears at a given
  // location in the tree. If the parent rebuilds and uses the same type of
  // widget (with the same key), the framework will re-use the State object
  // instead of creating a new State object.

  @override
  _ShoppingListState createState() => new _ShoppingListState();
}

class _ShoppingListState extends State<ShoppingList> {
  Set<Product> _shoppingCart = new Set<Product>();

  void _handleCartChanged(Product product, bool inCart) {
    setState(() {
      // When user changes what is in the cart, we need to change _shoppingCart
      // inside a setState call to trigger a rebuild. The framework then calls
      // build, below, which updates the visual appearance of the app.

      if (inCart)
        _shoppingCart.add(product);
      else
        _shoppingCart.remove(product);
    });
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text('Shopping List'),
      ),
      body: new ListView(
        padding: new EdgeInsets.symmetric(vertical: 8.0),
        children: widget.products.map((Product product) {
          return new ShoppingListItem(
            product: product,
            inCart: _shoppingCart.contains(product),
            onCartChanged: _handleCartChanged,
          );
        }).toList(),
      ),
    );
  }
}

void main() {
  runApp(new MaterialApp(
    title: 'Shopping App',
    home: new ShoppingList(
      products: <Product>[
        new Product(name: 'Eggs'),
        new Product(name: 'Flour'),
        new Product(name: 'Chocolate chips'),
      ],
    ),
  ));
}
```
ShoppingList类扩展了StatefulWidget，这意味着此控件存储可变状态。当ShoppingList控件首次插入到渲染树中时，框架调用createState函数来创建一个新的 `_ShoppingListState` 实例以与树中的该位置相关联。 （请注意，我们通常使用前置的下划线命名State的子类，以指示它们是私有的。）当此控件的父代重建时，父级将创建一个新的ShoppingList实例，但框架将重新使用已经存在的`_ShoppingListState`实例而不是再次调用createState。

要访问当前ShoppingList的属性，`_ShoppingListState`可以使用其widget属性。如果父代重建并创建一个新的ShoppingList，`_ShoppingListState`也将使用新控件值进行重建。如果您希望在控件属性更改时收到通知，则可以重写didUpdateWidget函数，该函数传递oldWidget，以便将旧控件与当前控件进行比较。

在处理onCartChanged回调时，`_ShoppingListState`会通过从`_shoppingCart`中添加或删除某个产品来改变其内部状态。为了向框架发信号通知它改变其内部状态，它将这些调用包装在一个setState调用中。调用setState将此控件标记，并在下次应用程序需要更新屏幕时重建它。如果在修改控件的内部状态时忘记调用setState，框架将不会标记控件，并且可能不会调用该控件的构建函数，这意味着用户界面可能无法更新以反映更改的状态。

通过这种方式管理状态，您不需要编写用于创建和更新子控件的单独代码。您只需实现构建函数，该函数会处理这两种情况。

## 响应控件的生命周期事件Responding to widget lifecycle events

详细文章：[State](https://docs.flutter.io/flutter/widgets/State-class.html)

在StatefulWidget上调用[createState](https://docs.flutter.io/flutter/widgets/StatefulWidget-class.html#createState)之后，框架将新的状态对象插入到树中，然后在状态对象上调用[initState](https://docs.flutter.io/flutter/widgets/State-class.html#initState)。状态的子类可以重写initState，以便执行需要仅发生一次的工作。例如，您可以重写initState来配置动画或订阅平台服务。 initState的实现需要通过调用[super.initState](https://docs.flutter.io/flutter/widgets/State-class.html#initState)来启动。

当一个状态对象不再需要时，框架调用[dispose](https://docs.flutter.io/flutter/widgets/State-class.html#dispose)函数来处理状态对象。您可以重写该处理函数来进行指定的清理工作。例如，您可以重写dispose以取消定时器或取消订阅平台服务。dispose的实现通常通过调用[super.dispose](https://docs.flutter.io/flutter/widgets/State-class.html#dispose)结束。

## Keys

详细文章：[Key](https://docs.flutter.io/flutter/widgets/Key-class.html)

当控件重建时，您可以使用Key来控制框架将与哪些控件匹配。默认情况下，框架根据它们的[runtimeType](https://docs.flutter.io/flutter/widgets/Widget-class.html#runtimeType)及其出现的顺序来匹配当前和之前构建的控件。要使用Key，框架要求构建前后两个控件具有相同的Key以及相同的runtimeType。

Keys在构建许多相同类型的控件实例的控件中是最有用的。例如，ShoppingList控件，它构建足够的ShoppingListItem实例来填充其可见区域：
- 没有Key时，当前构建中的第一个条目将始终与上一个构建中的第一个条目同步，即使在语义上，列表中的第一个条目只是从屏幕上滚动，且不再可见。
- 通过将列表中的每个条目分配为“semantic”Key，无限列表可以更高效，因为框架将匹配的semantic keys同步条目，以此达到类似（或相同）视觉呈现。此外，在语义semantic上同步条目意味着保留在状态子控件中的状态将保持附加到相同的语义条目上，而不是在可视区域中相同数字位置的条目。

## 全局Keys

详细文章：[GlobalKey](https://docs.flutter.io/flutter/widgets/GlobalKey-class.html)

您可以使用全局Key来唯一标识子控件。全局Keys在整个控件层次结构中必须是全局唯一的，与本地Keys不同，本地Keys只需同级之间是唯一的。因为它们是全局唯一的，所以可以使用全局Keys来检索与控件相关联的状态。
