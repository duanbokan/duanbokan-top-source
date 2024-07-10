---

title: HarmonyOS开发—进阶—ArkUI进阶

date: 2024-02-17 14:12:26

tags: [HarmonyOS开发,自定义组件]

categories: HarmonyOS

keywords: HarmonyOS,HarmonyOS开发,HarmonyOS入门,ArkUI进阶

description: ArkUI进阶

top_img: http://image.jucaiwy.com/image/Harmony/ArkUI/harmony-ui.jpg

cover: http://image.jucaiwy.com/image/Harmony/ArkUI/harmony-ui.jpg

---

## HarmonyOS开发—进阶—ArkUI进阶

依赖安装：ohpm install ../library

### 一、什么是ArkUI

方舟开发框架，对标iOS里边的 Swift UI， 对表Android里边的Jetpack Compose ，一种声明式的开发范式。

![img](http://image.jucaiwy.com/image/Harmony/ArkUI/ArkUI.png)



### 二、声明式的开发范式优点

语法语义上更为简洁，代码量更少，更新机制更简洁

类自然语言的UI描述和组合

开箱即用的多态组件，自定义组件

@State（修饰符，数据变化，UI会跟随变化）

@sharedState（跨设备状态管理）等状态管理注解



### 三、长列表加载性能优化实践

#### 3.1 ForEach

对于有限的可以预测数量的列表，建议使用ForEach进行，例如List的Item可以使用ForEach进行包装，提高加载性能



```typescript
List() {
  ForEach(this.floors, (item: BaseFloorData) => {
    ListItem() {
      buildFloor(item.mId, JSON.stringify(item.data))
    }
  })
}
```

同样，ForEach是一个接口，可以用于List，也可以用于包装任意Item



```typescript
Swiper() {
  ForEach(this.floorTemplate.imageList, (item: string) => {
    Image(item)
      .width("100%")
      .height(300)
      .objectFit(ImageFit.Cover)
  }, (item: string) => JSON.stringify(item))
}
```

#### 3.2 LazyForeach

LazyForeach对比Android系统的RecyclerView，具备以下特性：

1. 对于不可预估数量的列表加载，比如推荐位、首页上拉加载更多这种场景，可以使用LazyForeach，该框架会根据滚动容器的可视区域进行按需加载，可视区域外会自动进行销毁或者回收，从而降低内存消耗
2. 对于不可滚动的容器中，使用LazyForeach  系统会自动降级为Foreach
3. 使用LazyForeach的数据源需要继承自IDatasource接口

其中IDatasource 类似RecyclerView的Adapter，提供了以下四个接口，需要继承者实现：

```typescript
totalCount(): number {
  return this.imageDataList.length
}

getData(index: number): string {
  return this.imageDataList[index]
}

registerDataChangeListener(listener: DataChangeListener): void {
  listener.onDataReloaded()
}

unregisterDataChangeListener(listener: DataChangeListener): void {

}
```

对比ForEach，加载时会全部将所有的View挂在到组件树上，而不是按需挂载，导致ForEach加载较慢

而LazyForeach 为按需加载，只挂载可视区域范围内的组件，类似RecyclerView



```typescript
WaterFlow() {
  LazyForEach(this.dataSource, (item: string) => {
    FlowItem() {
      Image(item).width(300).height(300)
    }
  }, (item: string) => JSON.stringify(item))
}.width("100%").height("100%")
```

### 四、性能优化指标

华为内部测量性能优化三个关键指标，我们也可以进行借鉴

1. 完全显示所用时间（Time To Full Display，TTFD）：从应用生成到第一帧展示所用的时间
2. 丢帧率（Janky Frames）：一个周期内的丢帧比率，HarmonyOS要求每一帧都在11.1ms内绘制完成，也就是要求90Hz的刷新率，如果11.1ms没有绘制完成，就会丢帧。单帧丢失用户无感知，连续丢帧会有感知
3. 独占内存（Unique Set Size，USS）一个进程所占用的私有内存，即该进程独占的内存，反应一个进程的真实成本

### 五、优化方案

1. 使用LazyForeach替代ForEach，加载性能更高
2. LazyForeach上设置缓存加载数，可以预加载，避免出现白块的问题，一般来讲cachedCount的数量设置为n/2，此时丢帧率最小。例如当前屏幕展示10个，则设置cachedCount的数量为5
3. 组件复用：鸿蒙提供了组件敷用的能力，可复用组件，从树上的移除时，也就是划出屏幕时，会先进入回收缓存区，**此时会对数据进行回收，但是组件节点不会被回收**，后续创建新的节点时，会优先敷用缓存区中的节点，节约组件重新创建的时间，提高加载速度和响应速度

```typescript
@Component
@Reusable  // 1. 通过@Reusable 设置组件可以复用
struct Index {
  @State message: string = '楼层化SDK';
  dataSource = OrderCenterJsonString.generateWaterFlowData()
  ........
  // 3. 数据更新和重新绑定
  aboutToReuse(): void {
  }
  ........
  WaterFlow() {
    LazyForEach(this.dataSource, (item: string) => {
      FlowItem() {
        // 2. 设置组件复用ID .reuseId("order_detail_string_floor")
        Image(item).width(300).height(300).reuseId("order_detail_string_floor")
      }
    }, (item: string) => JSON.stringify(item))
  }
  .width("100%").height("100%")
}
```

4. 布局优化，通过层级优化，进而对布局进行优化
