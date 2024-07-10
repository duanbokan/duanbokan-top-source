---

title: HarmonyOS开发—基础—ArkTS语言介绍

date: 2023-12-23 16:52:46

tags: [HarmonyOS开发,ArkTS语言介绍]

categories: HarmonyOS

keywords: HarmonyOS,HarmonyOS开发,HarmonyOS入门,ArkTS语言介绍

description: ArkTS语言介绍

top_img: http://image.jucaiwy.com/image/20240710/ArkTS.png

cover: http://image.jucaiwy.com/image/20240710/ArkTS.png

---

## HarmonyOS开发—基础—ArkTS语言介绍



HarmonyOS使用ArkTS语言开发，ArkTS语言是TypeScript的基础上的拓展，而TypeScript是JavaScript的超集。

### 一、JavaScript

JavaScript（通常缩写为JS）是用来和HTML和CSS结合创建交互式网页，是一门基于原型和头等函数的多范式高级解释型编程语言，它支持面向对象程序设计、指令式编程和函数式编程。它提供了操作文本、数组、日期以及正则表达式等能力。JavaScript虽然不支持I/O，比如网络、存储和图形等，但这些都可以由它的宿主环境提供支持。它由Ecma通过ECMAScript实现语言的标准化（ES6）。目前，它被世界上的绝大多数网站所使用，也被世界主流浏览器（Chrome、IE、Firefox、Safari和Opera）所支持。



对于客户端来说，JavaScript通常被实现为一门解释语言，但如今它已经可以被即时编译（JIT， Just-in-Time）。随着HTML5和CSS3语言标准的推行，它还可以用于游戏、桌面和移动应用程序的开发，以及在服务器端网络环境运行（如Node.js）。

### 二、TypeScript

TypeScript是由微软进行开发和维护的一种开源的编程语言。提到TypeScript，第一个解释均为：TypeScript是JavaScript的超集。是的，随着JavaScript项目的复杂性呈指数级地增长，JavaScript用于编辑大规模应用程序时，在服务端会变得混乱和复杂，难以维护，此时TypeScript应运而生。TypeScript更强调显式类型，使研发人员能掌握各种组件的交互方式，并且TypeScript支持编译时调试，对于处理大型复杂应用程序的团队而言，这无疑是一个优点。

![img](http://image.jucaiwy.com/image/Harmony/ArkTs/ArkTs.png)



#### 2.1 TypeScript基础语法-基础类型

**boolean**：TypeScript中使用boolean来标志布尔类型，可以为true和false

**number**：TypeScript中所有的数字均为浮点型，这些浮点型的类型为number，除了支持十进制、二进制、八进制还支持了十六进制。

**string**：字符串，使用string标志为文本类型。TypeScript中可以使用单引号或者双引号标记字符串

**数组**：第一种，在元素后边加上方括号，表示为数组：let list1: number[] = [1,2,3]  另外一种，范型let list2: Array<number> = [1,2,3] 

**元组**：元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。let x :[string,number]

**枚举**：enum类型是对JavaScript基础类型的一个补充，使用枚举可以给一组数据类型友好的赋予一个名字。

**unknow**：有时候我们编程的时候，不清楚数据可能出现的类型，可以用unknow进行定义。unknow可以是string、boolean、number

**void**:当一个函数没有返回值时，这个类型为void

**undefined 和null** ：在TypeScript中，undefined和null是两种不同的基础类型，需要注意

**联合类型**：取值可以为多种类型中的一种  let state:string|number 这时  state可以被赋值为string或者number。



#### 2.2 TypeScript基础语法-条件

TypeScript支持按照不同的条件执行不同的逻辑代码，和Java类似，也支持if else 

```typescript
/**
 * IfElse示例代码
 */
static testIfElse() {
  let num: number = 5;
  if (num > 0) {
    console.log(num + "大于0");
  } else if (num = 0) {
    console.log(num + "等于0");
  } else {
    console.log(num + "小于0");
  }
}
```

同样也支持Switch语法

```typescript
/**
 * Switch示例代码
 */
static testSwitch() {
  let num: number = 5;
  switch (num) {
    case 1: {
      console.log("当前数字是：1")
    }
      break;
    case 1: {
      console.log("当前数字是：2")
    }
      break;
    default:
      break;
  }
}
```

#### 2.3 TypeScript基础语法-函数

```typescript
/**
 * 有名函数
 * @param x
 * @param y
 * @returns
 */
function add(x: number, y: number): number {
  return x + y;
}

/**
 * 匿名函数，没有函数名
 * @param x
 * @param y
 * @returns
 */
let addResult = function (x: number, y: number): number {
  return x + y
}
```

#### 2.4 TypeScript基础语法-可选参数

TypeScript中，函数构造参数可以使用？标记当前参数为可选参数：

```typescript
/**
 * 入参带着age，表明是可选参数
 * @param name
 * @param age
 * @returns 
 */
function appendStr(name: string, age?: string): string {
  if (age) {
    return name + age;
  } else {
    return name
  }
}

// 这两种调用都可以
appendStr("duanbokan")
appendStr("duanbokan","18")
```

#### 2.5 TypeScript基础语法-箭头函数

ES6版本的TypeScript提供了箭头函数，省略了function关键字，其函数是一个语句块：

```typescript
/**
 * 箭头函数
 * @param name
 * @param age
 */
(name: string, age: string) => {

}

/**
 * 箭头函数赋值
 * @param name
 * @param age
 */
let appendResult = (name: string, age: string) => {
  return name + age
}
```

#### 2.6 TypeScript基础语法-类

class 开头，直接跟着类名，类可以是public、private、通过这个关键字，控制类的访问权限



#### 2.7 TypeScript基础语法-继承

class 支持继承，对应关键字是extends



#### 2.8 TypeScript基础语法-模块

1. 应用越来越大的时候，可以通过使用模块（module）进行拆分，module可以相互加载、可以通过export和import来交换功能，即调用从另外一个模块的函数。
2. 两个模块是通过文件上的引用进行import和export的。
3. 正常模块中的变量、函数、类都不可以被别的模块访问，除非添加export
4. import以后，可以访问其变量、函数、类



#### 2.9 TypeScript基础语法-迭代器

迭代器用来循环和遍历数组for of 语句和for in语句 

for in语句：遍历数组的下标

for of语句： 遍历数组的元素

 

### 三、ArkTS

ArkTS是HarmonyOS及整个鸿蒙生态优选的主力应用开发语言，它是在保持TypeScript（简称TS）基本语法风格的基础上，对TS的动态类型特性施加更严格的约束，引入静态类型。同时，提供了**声明式UI**、**状态管理**等相应的能力，让开发者可以以更简洁、更自然的方式开发高性能应用，可以这么说，ArkTSTypeScript的超集。

当前，ArkTS在TS的基础上主要扩展了如下能力：

- [基本语法](https://developer.harmonyos.com/cn/docs/documentation/doc-guides-V3/arkts-basic-syntax-overview-0000001531611153-V3)：ArkTS定义了声明式UI描述、自定义组件和动态扩展UI元素的能力，再配合ArkUI开发框架中的系统组件及其相关的事件方法、属性方法等共同构成了UI开发的主体。
- [状态管理](https://developer.harmonyos.com/cn/docs/documentation/doc-guides-V3/arkts-state-management-overview-0000001524537145-V3)：ArkTS提供了多维度的状态管理机制。在UI开发框架中，与UI相关联的数据可以在组件内使用，也可以在不同组件层级间传递，比如父子组件之间、爷孙组件之间，还可以在应用全局范围内传递或跨设备传递。另外，从数据的传递形式来看，可分为只读的单向传递和可变更的双向传递。开发者可以灵活地利用这些能力来实现数据和UI的联动。
- [渲染控制](https://developer.harmonyos.com/cn/docs/documentation/doc-guides-V3/arkts-rendering-control-overview-0000001543911149-V3)：ArkTS提供了渲染控制的能力。条件渲染可根据应用的不同状态，渲染对应状态下的UI内容。循环渲染可从数据源中迭代获取数据，并在每次迭代过程中创建相应的组件。数据懒加载从数据源中按需迭代数据，并在每次迭代过程中创建相应的组件。

未来，ArkTS会结合应用开发/运行的需求持续演进，逐步提供并行和并发能力增强、系统类型增强、分布式开发范式等更多特性。

#### 3.1 装饰器：

装饰类、结构、方法、变量，赋予其特殊的含义

@Component 表示自定义组件装饰器，需要添加export关键字，进行导出

@Entry  修饰的自定义组件作为页面的入口，加载页面时，首先家在@Entry修饰的自定义组件页面

@Component struct 自定义结构体

@State  状态管理装饰器

@Link 可以和父组件中@State修饰的变量建立双向绑定，任何一方数据修改都会反馈另外一方，需要使用$进行引用

![img](http://image.jucaiwy.com/image/Harmony/ArkTs/Compont.png)



#### 3.2  @Component自定义组件的生命周期：

aboutToAppear：自定义组件被加载时调用，一般在这里进行数据处理和赋值

aboutToDisAppear:  自定义组件销毁时调用，一般在这里进行数据回收，避免资源泄漏



#### 3.3  @Entry修饰的页面入口组件生命周期

![img](http://image.jucaiwy.com/image/Harmony/ArkTs/Entry.png)



#### 3.3  条件渲染

#### 3.4  ForEach

第一个参数为数据源、

![img](http://image.jucaiwy.com/image/Harmony/ArkTs/ForEach.png)



#### 3.5  声明式UI特征

1. 声名式描述
2. 状态驱动视图更新

