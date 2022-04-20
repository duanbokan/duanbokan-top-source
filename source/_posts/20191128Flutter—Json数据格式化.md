---

title: Flutter—Json数据格式化

date: 2019-11-28 14:54:46

tags: [Flutter ,Json ,序列化和反序列化]

categories: Flutter

keywords: Flutter,Json,序列化和反序列化

description: Flutter—Json数据格式化

top_img: http://image.jucaiwy.com/image/20191128/flutter_json.jpeg

cover: http://image.jucaiwy.com/image/20191128/flutter_json.jpeg

---

因为Flutter中禁止使用运行时反射，因为运行时反射回干扰Dart的**tree shaking**(具体是什么，我也不知道)，所以之前在Java中使用Gson等序列化工具，无法在Flutter中正常使用。同样的道理，Dart中支持的dartson，也是使用了运行时反射，所以也不能在Flutter中使用。

在Flutter中，我们一般使用自动化源码生成器[ json_serializable package ](https://pub.dartlang.org/packages/json_serializable)包，在开发阶段，生成Json序列化和反序列化的模版。具体使用方式如下：

### 1. 在项目中配置json_serializable的依赖
想将json_serializable引入到我们的项目中，需要完成两个依赖，一个是开发依赖，一个是常规依赖，如下：

```dart
dependencies:
  # Your other regular dependencies here
  json_annotation: ^2.0.0

dev_dependencies:
  # Your other dev_dependencies here
  build_runner: ^1.1.3
  json_serializable: ^2.0.0
```
配置完成以后，需要执行** flutter packages get **命令进行相关依赖包的导入。

### 2. 以json_serializable的方式创建Model类

使用json_serializable方式创建Model类，可以按照以下几步进行操作：
 
#### 2.1 创建Model类文件 test_user.dart

#### 2.2 创建Model类，包含相关字段。
```dart
/// 用户信息
class UserInfo {
  // 名字
  String name;

  // 年龄
  int userAge;

  // 是否结婚
  bool isMarried;
}
```
#### 2.3 给Model类添加json_serializable的注解@JsonSerializable()

```dart
import 'package:json_annotation/json_annotation.dart';

/// 用户信息
@JsonSerializable()
class UserInfo {
  // 名字
  String name;

  // 年龄
  int userAge;

  // 是否结婚
  bool isMarried;
}
```
#### 2.4 代码引入part，引入fromJson和toJson方法
这里part好像是需要自己手动写上，还有下边的fromJson和toJson方法也是需要自己进行手动添加，不知道是否有快捷键，没找到，暂且手写吧。

**这里需要注意的是，一定要添加默认构造方法，否则会编译不通过。**

```dart
import 'package:json_annotation/json_annotation.dart';

part 'test_user.g.dart';

/// 用户信息
@JsonSerializable()
class UserInfo {
  // 名字
  String name;

  // 年龄
  int userAge;

  // 是否结婚
  bool isMarried;

  // 默认构造函数
  UserInfo(this.name, this.userAge, this.isMarried);

  factory UserInfo.fromJson(Map<String, dynamic> json) =>
      _$UserInfoFromJson(json);

  Map<String, dynamic> toJson() => _$UserInfoToJson(this);
}

```
加入这些方法后，发现新加入的part以及fromJson及toJson方法无法找到，这时候我们需要使用**flutter packages**命令进行生成相应的test_user.g.dart类。

这里可以使用单次build命令进行生成：

```bash
flutter packages pub run build_runner build
```
也可以使用watch模式，进行监控生成，在所有的JsonBean对象都生成以后，使用control+c退出命令行模式，会生成相对应的.g.dart文件。
```bash
flutter packages pub run build_runner build
```
此时，之前新建的**test_user.dart**同级目录下，会生成之前part中输入的**test_user.g.dart**文件，截止目前，Flutter可以使用的Json序列化对象，已经构建完成。

### 3. FlutterJson对象使用方法
使用时，直接可以使用UserInfo.fromJson，进行json解析，或者是userInfo.toJson，进行json序列化。需要注意以下几点：

    1. Flutter中json将数据格式比较严格，不像Gson中，double的值，也可使用String去解析，必须一一对应。

    2. 需要解析的对象中，如果有定义了一个服务端么有下发的bool值，并赋初始值为false时，经过json解析后，为null。没明白为啥，但是的确是这样子，bool可以为bull


![](http://image.jucaiwy.com/image/20191128/flutter_json.jpeg)




