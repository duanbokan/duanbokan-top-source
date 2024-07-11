---

title: HarmonyOS开发—基础-基础组件

date: 2023-12-22 14:54:46

tags: [HarmonyOS开发 , 基础组件]

categories: HarmonyOS

keywords: HarmonyOS , HarmonyOS开发 , HarmonyOS入门 , 基础组件

description: 基础组件

top_img: http://image.jucaiwy.com/image/Harmony/Basic/basic.png

cover: http://image.jucaiwy.com/image/Harmony/Basic/basic.png

---

## HarmonyOS开发—基础—基础组件

课程链接：

https://developer.huawei.com/consumer/cn/training/course/slightMooc/C101667360160710997



### 一、基础组件—Image

https://developer.harmonyos.com/cn/docs/documentation/doc-references-V4/ts-basic-components-image-0000001580026342-V4



Image支持 str图片链接、PixelMap、Resource三种类型，但是在使用string链接加载网络图片时，需要在module.json5文件中，增加网络访问权限：

```typescript
"requestPermissions": [
  {
    "name": "ohos.permission.INTERNET"
  }
],
```

Resource使用时，需要将图片放在resources 目录下的media文件夹中，然后使用进行展示：

```typescript
Image($r("app.media.image2"))
  .objectFit(ImageFit.Cover)
  .backgroundColor(0xCCCCCC)
  .width(100)
  .height(100)
```

图片宽高可以直接设置为数字，或者string “100VP”



### 二、基础组件—Text

针对包含文本元素的组件，例如Text、Span、Button、TextInput等，可使用fontColor、fontSize、fontStyle、 fontWeight、fontFamily这些文本样式，分别设置文本的颜色、大小、样式、粗细以及字体，文本样式的属性如下表：

| 名称         | 参数类型                       | 描述                                                         |
| ------------ | ------------------------------ | ------------------------------------------------------------ |
| fontColor    | ResourceColor                  | 设置文本颜色。                                               |
| fontSize     | Length \| Resource             | 设置文本尺寸，Length为number类型时，使用fp单位。             |
| fontStyle    | FontStyle                      | 设置文本的字体样式。默认值：FontStyle.Normal。               |
| fontWeight   | number \| FontWeight \| string | 设置文本的字体粗细，number类型取值[100, 900]，取值间隔为100，默认为400，取值越大，字体越粗。string类型仅支持number类型取值的字符串形式，例如“400”，以及“bold”、“bolder”、“lighter”、“regular”、“medium”，分别对应FontWeight中相应的枚举值。默认值：FontWeight.Normal。 |
| fontFamily   | string \| Resource             | 设置文本的字体列表。使用多个字体，使用“，”进行分割，优先级按顺序生效。例如：“Arial，sans-serif”。 |
| textOverflow |                                | {overflow:TextOverflow.Ellipsis}                             |
| maxLines     | number                         | 文本行数                                                     |

常用的文本超长展示如下：

```typescript
Text('This is the text content of Text Component This is the text content of Text Component')
  .fontSize(16)
  .maxLines(1)
  .textOverflow({overflow:TextOverflow.Ellipsis})
  .backgroundColor(0xE6F2FD) 
```

### 三、基础组件—InputText

#### 3.1 输入类型

type的参数类型为InputType，包含以下几种输入类型：

- Normal：基本输入模式。支持输入数字、字母、下划线、空格、特殊字符。
- Password：密码输入模式。
- Email：e-mail地址输入模式。
- Number：纯数字输入模式。

#### 3.2 光标设置

可以使用TextInputController动态设置光位置，下面的示例代码使用TextInputController的caretPosition方法，将光标移动到了第二个字符后。

```typescript
@Entry
@Component
struct TextInputDemo {
  controller: TextInputController = new TextInputController()
 
  build() {
    Column() {
      TextInput({ controller: this.controller })
      Button('设置光标位置')
        .onClick(() => {
          this.controller.caretPosition(2)
        })
    }
    .height('100%')
    .backgroundColor(0xE6F2FD)
  }
}
```

### 四、基础组件—Button

#### 4.1 按钮类型

Capsule：胶囊型，左右圆角

Normal：普通长方形

Circle：圆形按钮

#### 4.2 按钮点击事件

可以给Button绑定onClick事件，每当用户点击Button的时候，就会回调执行onClick方法，调用里面的逻辑代码。

```typescript
Button('登录', { type: ButtonType.Capsule, stateEffect: true })
  .onClick(() => {
  // 处理点击事件逻辑
  })
```

#### 4.3 包含子组件

Button组件可以包含子组件，让您可以开发出更丰富多样的Button，下面的示例代码中Button组件包含了一个Image组件：

```typescript
Button({ type: ButtonType.Circle, stateEffect: true }) {
  Image($r('app.media.icon_delete'))
    .width(30)
    .height(30)
}
.width(55)
.height(55)
.backgroundColor(0x317aff)
```

### 五、基础组建—容器

#### 5.1 布局容器中存在两个轴：

主轴：Column容器中，主轴为从上到下，Row容器中，主轴为从左到右

交叉轴：和主轴垂直

#### 5.2 核心API：

space：容器在主轴上的间距，可以用来做分割线；

#### 5.3 Column组件对齐方式

https://developer.harmonyos.com/cn/docs/documentation/doc-references-V4/ts-container-column-0000001579866558-V4

justifyContent：设置主轴上的对齐方式，其参数类型是FlexAlign

- Start：元素在主轴方向首端对齐，第一个元素与行首对齐，同时后续的元素与前一个对齐。
- Center：元素在主轴方向中心对齐，第一个元素与行首的距离以及最后一个元素与行尾距离相同。
- End：元素在主轴方向尾部对齐，最后一个元素与行尾对齐，其他元素与后一个对齐。
- SpaceBetween：元素在主轴方向均匀分配弹性元素，相邻元素之间距离相同。 第一个元素与行首对齐，最后一个元素与行尾对齐。
- SpaceAround：元素在主轴方向均匀分配弹性元素，相邻元素之间距离相同。 第一个元素到行首的距离和最后一个元素到行尾的距离是相邻元素之间距离的一半。
- SpaceEvenly：元素在主轴方向等间距布局，无论是相邻元素还是边界元素到容器的间距都一样。

alignltems：设置交叉轴的对齐方式，其参数类型为HorizontalAlign（水平对齐）

- Start：设置子组件在水平方向上按照起始端对齐。
- Center（默认值）：设置子组件在水平方向上居中对齐。
- End：设置子组件在水平方向上按照末端对齐。



#### 5.4 Row容器组件对齐方式

https://developer.harmonyos.com/cn/docs/documentation/doc-references-V4/ts-container-row-0000001579866566-V4

主轴和Column的主轴对齐设置方式一致：

alignItems：设置主轴上的对齐方式，其参数类型为VerticalAlign（垂直对齐）

- Top：设置子组件在垂直方向上居顶部对齐。

- Center（默认值）：设置子组件在竖直方向上居中对齐。

- Bottom：设置子组件在竖直方向上居底部对齐。

  

### 六、基础组建—列表

#### 6.1 List组建三个重要API

space：设置列表Item的间距

initialIndex：设置当前List初始化时，展示的位置

scroller：用来控制List滑动

divider：分割线

listDirection：方向



### 七、基础组建—Tabs

