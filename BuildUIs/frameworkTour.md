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
当一个控件的状态改变时，控件会重建其描述description，而框架framework会跟之前的描述做比对以确定底层渲染树中所需的最小变化，然后将状态转换到下个状态。

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

在写应用程序时，经常会使用[StatelessWidget](https://docs.flutter.io/flutter/widgets/StatelessWidget-class.html)和[StatefulWidget](https://docs.flutter.io/flutter/widgets/StatefulWidget-class.html)编写新控件，两者的差别在于你是否要管理控件的状态。一个控件的主要任务是实现build函数，定义控件中其他较低层次的控件。框架将依次构建这些控件，直到底层渲染对象。

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
    // Material是UI出现的纸墨风格概念
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
许多控件件需要在[MaterialApp](https://docs.flutter.io/flutter/material/MaterialApp-class.html)内部才能正确显示，因为这样才能继承主题数据，因此上面的例子中用MaterialApp做根控件。

MyAppBar控件创建了一个Container(容器)，高度为56设备无关像素(device-independent pixels)，内部左右填充（padding）8像素(pixels)。容器内部，MyAppBar为子控件设置Row(水平)布局，中间的title控件被设置成[Expanded](https://docs.flutter.io/flutter/widgets/Expanded-class.html)，Expanded的作用是展开Row、Column和Flex的子控件，意味它可以使用剩余的所有空间。

MyScaffold控件为子控件设置垂直布局，在垂直顶部放置一个MyAppBar的实例，将MyAppBar的Text控件作为标题使用，将控件作为参数传递给其他控件非常方便实用的，你可以创建通用的控件，以各种方式重复的使用。最后，MyScaffold使用Expanded，用一个中心文本来填充剩余的空间。