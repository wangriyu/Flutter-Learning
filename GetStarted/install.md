# 安装
---

## 1. 系统要求
均以macOS为例，硬盘空间至少700MB以上，需要以下命令行工具:
```
bash, mkdir, rm, git, curl, unzip, which
```
## 2. 获取Flutter SDK
克隆下面这个仓库:
```
$ git clone -b alpha https://github.com/flutter/flutter.git
```
添加环境变量:
```
打开 ～/.bash_profile
添加 export PATH=$HOME/flutter/bin:$PATH
退出执行 source ～/.bash_profile
输入 echo $PATH 查看变量添加是否成功
```
终端输入flutter doctor命令显示缺省信息并下载:
```
$ flutter doctor
```
更新Flutter
```
$ flutter upgrade
```
指定Flutter SDK, 每个项目的pubspec.yaml文件可以指定SDK中的依赖，下面的片段指定flutter 和 flutter_test包
```
name: hello_world
dependencies:
  flutter:
    sdk: flutter
dev_dependencies:
  flutter_test:
    sdk: flutter
```
修改pubspec.yaml文件后，通过下面的命令可以更新依赖包
```
$ flutter packages get // 获取pubspec.yaml文件中的所有依赖项

$ flutter packages upgrade // 获取依赖项的更新
```
## 3. IDE配置
- 在 Plugins 中下载安装Flutter 和 Dart 插件
- 配置Flutter SDK path
  选择 Languages & Frameworks>Flutter，添加flutter目录，比如 /Users/obiwan/flutter（不含/bin）
## 4. 其他设置
macOS同时支持ios和android开发
- 安装 Xcode(7.2以上)。配置最新的Xcode命令行工具（command-line tools），可以执行下面的命令，绝大多数情况下可行，但最新的版本可能要视情况而定。需要iPhone5s及以上的设备或虚拟机
```
$ sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
```
确保Xcode许可协议已签名, 输入下面的命令确认
```
$ sudo xcodebuild -license
```
输入 flutter run 开始安装测试 
- 安装Android Studio并安装好相应SDK
   需要一个Android 4.1 (API level 16)以上的设备或虚拟机用来测试
```
$ flutter devices // 确认连接的android设备
```
输入 flutter run 开始安装测试