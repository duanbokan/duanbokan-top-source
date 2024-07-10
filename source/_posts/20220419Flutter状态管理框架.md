---

title: Flutter状态管理

date: 2022-04-19 14:54:46

tags: [Flutter ,状态管理 ,Provider,Stream]

categories: Flutter

keywords: Flutter,状态管理,Provider

description: Flutter—状态管理机制

top_img: http://image.jucaiwy.com/image/20220419/flutter_state_cover.jpeg

cover: http://image.jucaiwy.com/image/20220419/flutter_state_cover.jpeg

---

## Flutter状态管理

在响应式编程中，状态即数据，状态变化，页面即发生变化，Flutter作为响应式开发框架，状态管理是Flutter开发过程中代码架构的重点，本文中，我们将通过分析常用的Flutter状态管理框架，给大家深入解析状态管理的核心实现方法，方便大家在后续开发中，挑选合适的状态管理框架。

Flutter本身已经给我们提供了一个状态管理方式，即Flutter自带的StatefulWidget，但是我们在应用过程中，会发现，这个状态仅仅适合在单个StatefulWidget中进行维护，当我们需要一个跨组件状态时，StatefulWidget将不再是一个好的选择，虽然我们可以使用callBack进行解决，但这个方式一旦业务增长到一定程度，嵌套较深的时候，将会造成很大的代码耦合，因此，我们需要使用一个状态管理组件进行维护这些状态。

常用的状态管理组件，包含了ScopedModel，BLoC，RxDart，Provider等，今天我们将在这里对这些状态管理机制的使用方法及原理进行剖析。

### 一、 状态管理分类：

首先，常用的状态管理，按照范围可以划分为局部状态管理和全局状态管理：

#### 局部状态：

Flutter提供了类似StatefulWidget、InheritWidget组件来实现局部状态管理，当这些Widget发生变化时，所有子树中依赖其数据的widget都会进行rebuild。

#### 全局状态：

Flutter没有提供原生的全局状态管理机制，虽然可以在根布局控件使用InheritWidget来实现全局状态管理，但是这样会存在类似依赖传递过深等问题。因此大多数情况下，需要依赖一些第三方库实现全局状态管理

#### 最简单的状态管理

我们可以使用 State + InheritedWidget实现最简单的状态管理机制。


### 二、 状态管理——Stream

Stream在Flutter中标志着的事件流或者管道一类的概念，通过Stream可以快速的实现给予事件流驱动的业务逻辑。界面通过订阅事件，并针对各个事件进行变化处理，实现响应式更新界面。 

```dart
/// 展示文本
String textString = "等待接收文本";

/// 单订阅Stream
Stream<String> stream = new Stream.fromFuture(_doFutureTask());

@override
void initState() {
StreamSubscription subscription = stream.listen((data) {
  textString = data;
  print("接收数据成功");
}, onDone: () {
  print("流处理完成");
}, onError: () {
  print("流处理出现异常");
});

/// Subscription_API
subscription.cancel();
subscription.pause();
subscription.resume();
}

/// 异步任务
static _doFutureTask() {
	return Future.delayed(Duration(seconds: 5), () {
  	print('Hello World');
	});
}

```

这是一个最简单的，通过异步任务创建流对象的方法，包含了流的创建、监听、管理，Stream流，分为单订阅流和广播流


#### 单订阅流

单订阅流只允许在该Stream的整个生命周期，注册一个监听器，即使第一个监听器被取消了，也无法在这个时间流中，监听到第二次事件。

```dart
// 初始化
StreamController<String> singleStream = StreamController();

// 消息发送
singleStream.add('Hello World');

```

使用过程中，经常会在log平台输出： Bad state: Stream has already been listened to.
其含义就是指：单订阅流不能有多个收听者
	

#### 广播流 

广播流，和单订阅流不同，允许任意个数的监听者，可以随时随地为其添加监听器，只要新的监听器，被添加进去，就可以收到新的事件

```dart
// 初始化
StreamController<int> singleStream = StreamController.broadcast();

// 消息发送
singleStream.sink.add(6);
```

StreamController的构造函数是一个泛型，意味着StreamController可以往流上推送任意类型的数据，当然这里需要考虑接收时候的数据类型处理。

#### StreamBuilder如何实现刷新

在上边的例子中，我们看到了在页面中有使用一个StreamBuilder，来构建一个UI展示：

```dart
StreamBuilder<String>(
        builder: (context, snapshot) {
          if (snapshot == null || !snapshot.hasData) {
            return CircularProgressIndicator();
          } else {
            if (snapshot.hasError) {
              return Text("发生错误");
            } else {
              return Text(snapshot.data);
            }
          }
        },
        stream: singleStream.stream,
      )
```

那么StreamBuilder和Widget的刷新，是怎么关联起来的呢？通过阅读代码，我们发现，其实StreamBuilder的主要逻辑在_StreamBuilderBaseState中，_StreamBuilderBaseState在initState、didUpdateWidget中会调用_subscribe方法，从而调用Stream的listen，然后通过setState更新UI。
	
```dart
void _subscribe() {
if (widget.stream != null) {
  _subscription = widget.stream.listen((T data) {
    setState(() {
      _summary = widget.afterData(_summary, data);
    });
  }, onError: (Object error) {
    setState(() {
      _summary = widget.afterError(_summary, error);
    });
  }, onDone: () {
    setState(() {
      _summary = widget.afterDone(_summary);
    });
  });
  _summary = widget.afterConnected(_summary);
}
```
  }


而setState的刷新机制，其实我们大家应该都知道，实质上是调用了markNeedsBuild，markNeedsBuild方法会标记element为dirty，这样在下一帧WidgetsBinding.drawFrame的时候，会进行绘制
	
	
#### StreamController整体架构

从上边几个demo中，我们看到Flutter的Stream流中，存在Stream、StreamController、Sink、以及StreamSubscription这样四个比较关键的组件，那么这四个组件是以一个什么样子的形式互相结合起来的呢。我们通过下边这样一副图，进行说明：

![](http://image.jucaiwy.com/image/20220419/stream_pic.png)


整个流程，概括起来就是：StreamController作为一个统筹管理的“Boss”，主要负责协调和维护整个事件流的输入和输出，StreamController暴露了一个Sink属性，主要负责事件流的输入，在这里输入事件。暴露一个Stream属性，主要负责流事件的输出，除自身提供了事件转换方法，例如where、take等，主要进行事件流的转换。同时，Stream对外提供了事件的监听，分别可以处理在收到事件以后的处理onData，事件处理完成以后的onDone以及事件处理异常的onError等方法，通过注册这样的监听，我们又可以得到StreamSubscription这个属性，其功能，主要管理事件的订阅，包含取消、暂停、恢复等操作

#### StreamController同步、异步处理

在初始化StreamController的时候，我们可以看到，有一个构造参数，sync，针对我们传递的sync值，决定使用同步流还是异步流。

```dart
return sync
    ? new _SyncStreamController<T>(onListen, onPause, onResume, onCancel)
    : new _AsyncStreamController<T>(onListen, onPause, onResume, onCancel);
```

这里具体怎么实现的呢，我们一起来看一下。在同步流中，直接调用了subscription的_add方法，直接将数据添加进事件回掉监听中，实现同步：

```dart
void _sendData(T data) {
    if (_isEmpty) return;
    if (_hasOneListener) {
	      _state |= _BroadcastStreamController._STATE_FIRING;
	      _BroadcastSubscription<T> subscription = _firstSubscription;
	      subscription._add(data);
	      _state &= ~_BroadcastStreamController._STATE_FIRING;
	      if (_isEmpty) {
	        _callOnCancel();
	      }
	      return;		
    }
    _forEachListener((_BufferingStreamSubscription<T> subscription) {
      	   subscription._add(data);
    });
}
```


​	
而在异步流中，则是使用_addPending方法，添加了一个继承自_DelayedEvent的方法，实现异步：

```dart
abstract class _AsyncStreamControllerDispatch<T>
implements _StreamController<T> {
	void _sendData(T data) {
		_subscription._addPending(new _DelayedData<T>(data));
	}

  	void _sendError(Object error, StackTrace stackTrace) {
    	_subscription._addPending(new _DelayedError(error, stackTrace));
  	}

  	void _sendDone() {
    	_subscription._addPending(const _DelayedDone());
  	}
}
```

那么Flutter是怎么实现异步的呢。我们继续深入挖掘，发现在异步任务中：

```dart
void schedule(_EventDispatch<T> dispatch) {
    if (isScheduled) return;
    assert(!isEmpty);
    if (_eventScheduled) {
      assert(_state == _STATE_CANCELED);
      _state = _STATE_SCHEDULED;
      return;
    }
    scheduleMicrotask(() {
      int oldState = _state;
      _state = _STATE_UNSCHEDULED;
      if (oldState == _STATE_CANCELED) return;
      handleNext(dispatch);
    });
    _state = _STATE_SCHEDULED;
  }
```

因此，Flutter异步任务执行，实际上相当于在Microtask任务队列里边添加了一个任务，而我们都知道，Flutter为单线程应用，和大多数单线程应用一样，通过消息循环机制来运行，这个机制中，主要包含了两个任务队列，一个是microtask内部队列，一个是event外部队列，而microtask的优先级又高于event。Stream通过这样的方式实现了异步

![](http://image.jucaiwy.com/image/20220419/flutter_thread.png)

至此，我们已经基本了解了Stream的原理及一些常用的api，这方便我们对后续的RxDart、Provider等进行分析


### 三、状态管理——RxDart

说到RxDart就不得不提一下ReactiveX，[http://reactivex.io/](http://reactivex.io/)  

在其官网上，对ReactiveX的介绍为

```dart
“An API for asynchronous programming with observable streams”
```

用于可观察流的异步编程的API，一句话概括了ReactiveX的核心设计思想，为各个平台提供了异步编程的可观察流API。当然在Dart上也不例外。

```dart
/// 创建一个Subject
var subject = PublishSubject<String>();

/// 通过listen实现订阅
subject.listen((String data) {
  print("OnData " + data);
}, onError: () {
  print("onError ");
}, onDone: () {
  print("onDone ");
});

/// 使用完成，关闭
subject.close();
```

这里是一个最简单的RxDart的使用方法，细心的同学可能已经发现了，这不就是之前的StreamController么。的确是这样的，RxDart内部，其实也是对Stream的一个封装。PublishSubject的内部实现，其实也是一个广播类型的StreamController：

```dart
factory PublishSubject({void onListen(), void onCancel(), bool sync = false}) {
    // ignore: close_sinks
    final controller = StreamController<T>.broadcast(
      onListen: onListen,
      onCancel: onCancel,
      sync: sync,
    );

    return PublishSubject<T>._(
      controller,
      Observable<T>(controller.stream),
    );
}
```


  	
至此我们也明白了：

1. 在RxDart中Subject，无论是PublishSubject还是BehaviorSubject(只保留最后一个值的特殊流)，其核心其实还是StreamController

2. RxDart中的Observable实质上相当于一个Stream

总结一下，RxDart，实际是对上边所说的Stream进行了概念转换，变成了ReactiveX用户熟悉的对象和操作符，本质上还是一个Stream，这也是为什么可以直接在StreamBuilder中使用RxDart。


### 四、状态管理——Provider

Provider是Flutter官方推荐的状态管理方式之一，它的特点是，不复杂，好理解，可控度较高。

#### Provider使用

第一步	添加依赖

```dart
provider: ^2.0.1+1
```

第二步  创建数据模型

```dart
/// 计数module
class CounterModel extends ChangeNotifier {
  /// 计数
  int _count = 0;
  int get value => _count;

  void increment() {
    _count++;
    notifyListeners();
  }
}
```

该Module继承自ChangeNotifier，主要负责数据模型保存和管理，同时暴露出来的notifyListeners()方法，在调用后，可以自动更新其所有的监听者。

第三步  创建全局共享数据依赖

```dart
void main() {
  /// 数据模型
  final counter = CounterModel();
  
  runApp(
    ChangeNotifierProvider.value(
      notifier: counter,
      child: MyApp(),
    ),
  );
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: ThemeData.dark(),
      home: FirstScreen(),
    );
  }
}
```

在这里我们使用ChangeNotifierProvider，并设置其value为之前创建的数据模型。ChangeNotifierProvider<T>.value类型的数据，不仅可以将数据共享给其所有的子节点进行使用，同时还可以在数据发生变化时，通过调用之前数据模型中的notifyListeners()方法进行刷新。

第四步  在其他页面中获取共享的状态

这里我们通过两个页面，相互之间共同持有一份数据的例子，来看看Provider是怎么处理两个页面之间数据的共享的，首先我们创建第一个页面

```dart
/// Provider 页面
class FirstScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
  	print("Provider " + "FirstScreen build");
 
    final _counter = Provider.of<CounterModel>(context);

    return Scaffold(
      appBar: AppBar(title: Text('FirstPage')),
      body: Center(
          child: GestureDetector(
              onTap: () => {_counter.increment()},
              child: Text(
                'Value: ${_counter.value}',
                style: TextStyle(fontSize: 48),
              ))),
      floatingActionButton: FloatingActionButton(
        onPressed: () => Navigator.of(context)
            .push(MaterialPageRoute(builder: (context) => SecondPage())),
        child: Icon(Icons.navigate_next),
      ),
    );
  }
}
```

在这个页面中，我们使用Provider.of<T>(context)方法来向上寻找最近存储了T的祖先节点数据。我们这里获取到了存储的CounterModel，并对其属性value进行展示。然后在点击文本的时候，自增，希望在第二个页面中，可以拿到并展示。

```dart
/// Provider 页面
class SecondPage extends StatelessWidget {
  @override
  Widget build(BuildContext buildContext) {
    print("Provider " + "SecondPage build");
    return Scaffold(
        appBar: AppBar(
          title: Text('Second Page'),
        ),
        body: Consumer<CounterModel>(
          builder: (context, CounterModel counter, _) => Center(
              child: GestureDetector(
                  onTap: () => {counter.increment()},
                  child: Text(
                    'Value: ${counter.value}',
                    style: TextStyle(
                      fontSize: 48,
                    ),
                  ))),
        ));
  }
}
```

在第二个页面中，我们并没有像在第一个页面中一样，使用Provider.of<T>(context)方式去获取Provider中共享的数据，而是使用了Consumer这个方式去获取，这两个有什么不一样呢，先看完演示结果，然后继续往下分析。


#### Consumer

在上边的例子中，我们使用了Consumer获取Provider中共享的数据模型，Consumer使用了Builder模式，收到更新通知就会通过builder重新构建。Consumer<T>代表了它要获取哪一个祖先中的Model。

```dart
final Widget Function(BuildContext context, T value, Widget child) builder;
```

从Consumer的构造方法中，我们可以看到，其builder实际上就是一个Funcation，它接受三个参数，用于构建自身。同样原理的还有Consumer2，和Consumer类似，只是入参的泛型，变成了两个：
	
```dart
final Widget Function(BuildContext context, A value, B value2, Widget child) builder;
```

其实在源码中，这里最多可以到6个，大家在使用的过程中，可以根据自己需要进行选取。

那么为什么需要区分这两个获取数据类型的方法呢，我们在上述两个页面的build方法中，分别添加了log，进行日志打印，操作步骤为：

```dart
1. 进入第一个页面，点击两次Value，使数据自增
2. 点击进入下一个页面
3. 在第二个页面，同样点击两次Value，使数据自增
4. 返回第一个页面，点击一次Value，自增
```


​	
我们可以看到Log打印如下：

```dart
2020-12-12 18:30:26.873 4273-6507/jd.com.state.flutter_state_manager I/flutter: Provider FirstScreen build
2020-12-12 18:30:27.777 4273-6507/jd.com.state.flutter_state_manager I/flutter: Provider FirstScreen build
2020-12-12 18:30:29.290 4273-6507/jd.com.state.flutter_state_manager I/flutter: Provider SecondPage build
2020-12-12 18:30:30.203 4273-6507/jd.com.state.flutter_state_manager I/flutter: Provider FirstScreen build
2020-12-12 18:30:31.726 4273-6507/jd.com.state.flutter_state_manager I/flutter: Provider FirstScreen build
2020-12-12 18:30:35.223 4273-6507/jd.com.state.flutter_state_manager I/flutter: Provider FirstScreen build
```

可以看到使用Provider.of<T>(context)的第一个页面，在每一次点击按钮的时候，都会重新build，而页面二，则没有。也就是说，使用Consumer进行数据共享的时候，仅仅只更新自身的Widget。那么为什么Consumer可以做到局部更新呢，我们来看一下Consumer的内部构造：
	
```dart
@override
  Widget build(BuildContext context) {
    return builder(
      context,
      Provider.of<T>(context),
      child,
    );
  }
```

也就是在Consumer中，实际上，也是通过使用Provider.of<T>(context),来实现的。那么问题就来了，同样是Provider.of<T>(context)实现的，为什么Consumer就可以实现局部刷新呢？
	
这里我们可以看一下在第二个页面中，我们在构建Consumer的时候，传递的context，并不是Widget中build方法提供的buildContext，而是使用了自己的context，所以在刷新的时候，可以做到局部刷新，这样就方便我们在Flutter开发的时候，使用局部刷新进行页面性能优化。

![](http://image.jucaiwy.com/image/20220419/flutter_state_code.png)
	
至此，我们已经基本上了解到了Provider的基础用法，当然Provider还提供了核心的dispose方法，方便用户进行回收，这里因为还没有彻底搞清楚，所以暂不进行介绍。

Provider还有更多，更详尽的用法，比如 ValueListenableProvider、FutureProvider、StreamProvider等多种Provider，可见整个Provider的设计上更贴近Flutter的原生特性，同时设计也更好理解，并且兼顾了性能等问题。这些后边在使用到的时候，我们在进行逐步分析和解析


### 五、 总结
总结上述所有的状态管理机制，无论是Flutter原生提供的Stream，还是ReactiveX提供的RxDart，亦或是Provider，以及没有在文章中出现的scoped_model、阿里开源的fish_redux，这一系列的组件，都为我们提供了一个很好的状态管理机制，而我们在使用过程中，大可通过自身业务需求，按需选型。


​	



