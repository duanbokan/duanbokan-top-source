---

title: HarmonyOS开发—基础—应用程序入口-UIAbility

date: 2023-12-29 9:52:46

tags: [HarmonyOS开发,UIAbility]

categories: HarmonyOS

keywords: HarmonyOS,HarmonyOS开发,HarmonyOS入门,UIAbility

description: UIAbility

top_img: http://image.jucaiwy.com/image/Harmony/UIAbility/UIAbility.png	

cover: http://image.jucaiwy.com/image/Harmony/UIAbility/UIAbility.png

---

## HarmonyOS开发—基础—应用程序入口-UIAbility



### 一、什么是UIAbility

UIAbility是一种包含用户界面的UI组件，主要用于和用户进行交互。一个应用，可以有一个UIAbility也可以有多个UIAbility，自鸿蒙API10以后，建议一个应用只有一个UIAbility，多个页面为**多个组件**。

![img](http://image.jucaiwy.com/image/Harmony/UIAbility/single.png)



### 二、UIAbilit之间跳转

 多个UIAbility之间跳转，API9之前推荐使用Router

```
router.pushUrl({
  url: 'pages/Second',
  params: {
    src: 'Index页面传来的数据',
  }
}, router.RouterMode.Single)
```

router.pushUrl支持两种模式跳转，分别为single和standard，设置为single时，每次会先看堆栈内是否有单个实例，没有的话，会创建一个，有的话，会推倒栈顶；设置为standard时，每次pushUrl会多一个实例

返回页面可以使用：

```
// 返回上一个页面
router.back();

// 返回指定页面
router.back({ url: 'pages/Index' });
```

API10 及之后，推荐使用Navigation进行页面之间跳转，因为此时建议一个应用内部只有一个UIAbility，所以，页面在这个UIAbility中，作为组建进行存在，此时页面跳转变成了UI组件跳转，因此推荐使用Navigation。



**案例待补充**



### 三、UIAbility生命周期

UIAbility的生命周期包括Create、Foreground、Background、Destroy四个状态，WindowStageCreate和WindowStageDestroy为窗口管理器（WindowStage）在UIAbility中管理UI界面功能的两个生命周期回调，从而实现UIAbility与窗口之间的弱耦合。

![img](http://image.jucaiwy.com/image/Harmony/UIAbility/lifecycle.png)



**Create:** 应用初始化操作，变量定义、资源加载，用于后续UI展示

**onWindowStageCreate**：UIAbility创建完成以后，进入Foreground之前，系统会创建一个WindowStage，WindowStage创建完成以后，回掉onWindowStageCreate，可以在这里进行UI页面加载、WindowStage订阅等操作，获取焦点、失去焦点、可见不可见等；通过loadContent加载指定的页面：

```typescript
onWindowStageCreate(windowStage: window.WindowStage): void {
  windowStage.loadContent('pages/Index', (err, data) => {
    if (err.code) {
      return;
    }
  });
}
```

**onForeground**：应用切换至前台时回掉，申请系统需要资源

**onBackground**：应用切换至后台时回掉，释放无用资源，执行耗时操作，例如状态保存

**onWindowStageDestroy**：释放UI界面资源，注销WindowStage相关事件

**onDestroy：**在UIAbility销毁时触发，可以在onDestroy回调中进行系统资源的释放

### 四、UIAbility启动模式：

UIAbility启动模式需要在module.json5文件中配置**lunchType**字段？

- singleton（单实例模式）
- standard（标准实例模式）
- specified（指定实例模式）

