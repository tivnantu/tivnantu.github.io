---
title: 【技术】在 iOS 开发过程中的 MVC 和 MVP 架构
description: 简单讲讲对 MVC 和 MVP 的学习和理解
tags:
  - 架构
  - MVC
  - iOS 开发
categories: 技术
abbrlink: fd807deb
date: 2023-06-24 01:20:04
updated: 2023-06-24 01:20:04
index_img: /image/MVCMVP/image4.png
banner_img: /image/MVCMVP/image4.png
---

{% note success %}

年初写的文章，整理下发下博客。

{% endnote %}

## 一、MVC

### 1.1 历史

MVC 模式最早由 Trygve Reenskaug 在 1978 年提出，距今已有接近 50 年的历史。MVC 最开始是为程序语言 Smalltalk 所设置的一种软件架构，得益于其所提供的编码便利，MVC 逐渐风靡 GUI 编程领域。

早期 Web 端网页开发（以 JSP 为代表）、桌面程序开发（以 MFC 为代表）以及移动端程序都对 MVC 有这良好的应用以及实践，MVC 也是苹果官方推荐的 App 开发框架。在 MVC 不断发展中，也逐渐衍生了许多 MVC 的变种，如 MVCS、MVA、MVP、MVVM。

### 1.2 架构组成

在[维基百科](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller?useskin=vector)的词条中，MVC 将一个 App 划分为三部分：

- Model：数据层，封装与业务逻辑相关的数据以及对数据的处理方法。拥有访问和调整数据的能力
- View：视图层，封装数据的目的显示逻辑，与具体 UI 绘制有关，展现受到 M 数据层的影响
- Controller：控制层，封装业务逻辑，是 M 和 V 的中间人角色，串联起 M 和 V，将 M 中的数据展现到 V 中，将 V 所产生的事件反映到 M 中

MVC 三个层次的职责含义比较容易理解，比较容易产生异议的是三个层次之间的具体交互关系。MVC 并没有给出标准的层次交互关系，网上流传的诸多 MVC 架构图也是形态各异，无法指明哪种才是标准的 MVC 实现。这里给出几份流传度较大、接受度较大的说法。

#### 1.2.1 苹果官方推荐的 MVC 架构

苹果官方所提供的 cocoa touch 一直遵循 MVC 开发模式，也提倡开发者沿用其开发模式。在[苹果开发者文档](https://developer.apple.com/library/archive/documentation/General/Conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)中，苹果给出了 cocoa touch 眼中的 MVC 开发架构图。

![image](/image/MVCMVP/image1.png)

可以很清晰地从图中看出，C 在整个架构中是属于中心主导角色的。用户在 UI 上（V 层）所进行的动作如点击、滑动，会传导 C 上，C 上针对不同的动作更新 M 以及 V，给予用户 UI 上的反馈。

在上图中，还可以清晰的看出，V 和 M 层是完全隔离的，V 不能直接操控 M，M 也不能直接触发 V 的更新。

本文也主要是基于苹果官方给出的 MVC 架构进行拓展解析。

#### 1.2.2 维基百科给出的 MVC 架构

维基百科给出了和苹果不太一样的架构图：

![image](../image/MVCMVP/image2.png)

维基百科给出的架构图相对来说更加清晰，针对每一层级的的交互进行了解析：

- 用户看到视图 View
- 用户操控 Controlloer
- Controller 更新数据 Model
- Model 数据更新视图 View

对比上面的 MVC，读者可能会产生两个疑问：

1. 为什么用户控制的不是 View 而是 Controller？
2. 为什么是数据 Model 更新 View 而不是 Controller 更新 View？

遗憾的是，维基百科没有对这张图进行更进一步的解析，仅仅只是进行了列举。但是从其词条的其他描述，我们可以进行这样的脑补理解：这张图中的连接线并不是代表各个层级的具体交互动作，而是各个层级的交互内涵。

- 用户操作 View，在 View 上的点击、滑动，最终是要落到 Controller 上的，在 Controller 上完成“用户动作” -> “应用响应”的转变，所以用户本质上是通过 Controller 来管理到整个 App 的
- 同理，View 的更新，本质上是来源于 Model 的更新，也就是，图中 M -> V 的“update”表示的是 V 和 M 的内在关系，不是表示真正的 update 操作，Model 并不会真的持有 View，也不会真去调动 View 的 UI 更新接口

#### 1.2.3 大多数博客给出的 MVC 架构图

大多数博客会给出下图所示的架构图：

![image](/image/MVCMVP/image3.png)

这个也是最简陋的 MVC 架构图，图中只展示了几个对象和之间的箭头。对该图比较公认的解析是：Controlleer 负责对 Model 中的数据进行更新，而视图向模型中请求数据；当有用户的行为触发操作时，会有 Controller 更新 Model，并通知视图进行更新，在这时 View 向 Model 请求新的数据。

该图和苹果 MVC 架构图最主要的区别是，该图中的视图和模型是存在关联的。[ASP.Net](https://zh.wikipedia.org/wiki/ASP.NET_MVC_Framework?useskin=vector) 实现的 MVC 是存在这种关联操作行为的，但是与之相对应的苹果官方所提倡的 MVC，却并不提倡这种关联操作。

#### 1.2.4 总结

从这几份架构图可以看出，不同框架、不同领域，对于 MVC 架构的理解是不一样的。笔者的建议是不要被“标准答案”所干扰，关键目光要聚焦在数据 M、视图 V、逻辑 C 三个对象本身上，针对这三个对象在代码中形成清晰明确、便于维护阅读的结构，那就是好的 MVC 架构。

对于这个三个对象之间的关联关系，可以自取所需做点增删，根据项目所用工具所用语言，选择不同的实现就好。使用 MVC 架构是为了给项目做服务，只要一个项目内的各个模块保持统一的原则，做到高内聚低耦合，那就达到了 MVC 的目标。

### 1.3 MVC 实现

得益于Objective/Swift提供的强大编程特性，不同项目在MVC的实现方式也会存在差异。以下以两个项目实例进行解析。

#### 1.3.1 Standford CS193P - FaceIt

斯坦福开设的 Standford CS193P 课程给出了在苹果官方 MVC 架构图上的 MVC 实现图，实现了一个[表情编辑项目](https://github.com/MyColourfulLife/FaceIt)。实现图如下：

![image](/image/MVCMVP/image4.png)

- Controller -> View：Controller 可以完全知道视图的所有行为，具有完全控制权。这里提到了 outlet，outlet 是 OC 的一种特殊属性，用关键字 IBOutlet 声明。可以用 outlet 对象指向 IB 中的任何一个 button，通过 outlet 对象，就可以获得该 button 的相关属性
  ![image](/image/MVCMVP/image5.png)

- View -> Controller：View 对具体 Controller 是一无所知的，所以 View 是不能直接调用 Controller 的。Objc 主要通过三种方法进行 View 到 Controller 的通信
  
  - Target 方式：将位于 Controller 的目标方法（target），绑定到 View 的行为（action）。当 View 触发了对应行为，View 会发送消息给 Controller 的目标告诉有人对它进行了操作，进而让 Controller 做出响应

    注意，结合 Mainstoryboard 方式进行开发，IBAction 属性相当于将 addtarget 方式进行简写，本文将其视作同一种方式

    ![image](/image/MVCMVP/image6.png)

    ![image](/image/MVCMVP/image7.png)
  
  - Delegate 方式：Controller 实现代理协议接口，当 View 触发了某一个行为，View 会发消息其所持有的 delegate 对象，让 delegate 对象通过协议代理方法去响应行为。View 对于 Controller 依旧是一无所知的，它只知道它本身持有了一个 delegate 对象，这个 delegate 对象可以辅助传递行为
    ![image](/image/MVCMVP/image8.png)
  
  - DataSource 方式：View 不该持有它本身的视图 Model，否则会造成 View 和 Model 之间的耦合。数据应该位于 Model 中，有 UI 无关。所以 Controller 就必须告诉视图它有什么数据。DataSource 是一种特殊的代理，用来给 View 从 Controller 获取数据
    ![image](/image/MVCMVP/image9.png)

- Controller -> Model：Controller 同样可以完全控制 Model，可以知道 Model 中的一切；在示例中，表情数据并没有接入网络，仅仅只是手动写死在代码中，这也是一种完全控制的表现
  ![image](/image/MVCMVP/image10.png)

- Model -> Controller：通常情况下，Model 是不会直接喝 Controller 直接通信的，也就是说 Model 不直接持有 Controller 对象。当 Model 中的数据更新时候，Model 可以通过消息通知或 KVO 两种方式告知 Controller，让 Controller 将数据变化反应到 View 中

简单来说，就是 Controller 可以直接调用 View 和 Model，View 通过 Delegate 方式和 Target 方式“调用”Controller，Model 通过 KVO 方式或者消息通知方式“调用”Controller。

当然这只是提倡的做法，并不意味着一定要这样应用，在实现的时候，我们完全可以只选择其中一种方式完全应用。比如说 View 不通过 Delegate 方式与 Controller 通信，而是使用消息通知方式，再比如说 View 转而使用 block 方式……只要有清晰的脉络或分层，那也是无可厚非的。下面就介绍一个完全利用 KVO 方式实现 MVC 的框架。

#### 1.3.2 MBMVC

[MBMVC](https://github.com/southpeak/Minya) 是 Alibaba 开源的一套简单的 Objc 框架，基于 KVO 实现 MVC 模式。

这里不针对 MBMVC 框架进行详细解析，可以大致理解其基于 KVO 实现了 Controller、View、Model 之间的通信。示例 Demo 比较简单，界面中有一个按钮，点击按钮弹出弹窗，弹窗展示当前时间，数据源是时间，示例 Demo 架构如下，从图中可以看出，这里实现的是 1.2.3 的 MVC 架构：

![image](/image/MVCMVP/image11.png)

首先 View 和 Controller 之间，通过数据 Bind 的方式达到完全解耦，Controller 不再直接持有 View。当然初始化 View 等工作，还是在 Controller 中做的。

![image](/image/MVCMVP/image12.png)

View 对应操作触发时（点击“展示按钮”），更改 KVO 对象标志位。Controller 接收到 KVO 通知，并进行对应操作更新 Model。

![image](/image/MVCMVP/image13.png)

![image](/image/MVCMVP/image14.png)

这里关注一下 Controller 如何对 Model 进行操作。首先 Controller 不直接操控 Model，而是通过中间对象 Proxy。Proxy 是 Controller 以及 Model 之间关系的一种抽象，这里不去深究细节，大致理解成持有了 Controller 和 Model 的部分 handler 以及 block 即可。

Model 中直接获取当前日期作为业务数据，然后更新业务 KVO 对象，通知到 View 进行下一步操作（弹出日期弹窗）。注意，这里 Model 是直接通过 KVO 通知到 View 的

![image](/image/MVCMVP/image15.png)

![image](/image/MVCMVP/image16.png)

MBMVC 实现的较为复杂，导致在编写一个功能反而没有 FaceIt 那么简单，代码可读性也没有那么好。在实现功能的时候，还必须要引入一个业务层的 KVO 对象用以供各方绑定。demo 所涉及的数据变化不多，但是如果是一个存在复杂操作的页面的话，这个 KVO 对象想必会变得很臃肿。

![image](/image/MVCMVP/image17.png)

这里也提供另外一个基于 KVO 实现的“MVC”模式简单框架--[Minya](https://github.com/southpeak/Minya)。Minya 更“纯粹”一点，没有 MBMVC 中 proxy、拦截器等等概念，只有几个 KVO 绑定和更新的 sgar 方法，所以看起来可读性还更高一点。

Minya 实现的不是 MVC 模式而是 MVCS 模式。但是 MVCS 和 MVC 区别不大，忽略 S 数据存储部分，那它就和 MVC 基本一样了。简单介绍下 MVCS，MVCS 基于 MVC 简单衍生出来的一套架构方案，各个层次的含义基本含义基本相同。从概念上来说，它拆分的部分是 Model 部分，拆出来一 Store，用来专门负责数据存取（但从实际操作的角度来讲，它拆开的是 Controller）。

#### 1.3.3 总结

MVC 的实现方式多种多样，即可以想 stanford 那样中规中矩进行编写，也可以接入 MBMVC 或者 Minya 利用 KVO 搞定，当然也可以自己写一些 sugar 方法，巧妙利用 KVO、block、notification 达到目标……各位也可以把目光放到其他领域，去了解一下 JSP、ASP.NET、Rails 等等知名 MVC 框架是怎么实现 MVC 的，比较一下异同，这里就不过多展开了。

不过纵观上诉例子，虽然实现方法大相径庭，但是还是没有偏离 MVC 三个层次的规则，代码之间的结构也还是比较清晰的，MVC 的目标还是基本达成了。

## 二、MVP

### 2.1 MVC 遇到的问题

MVC 模式是一个非常经典且精简的设计模式，常见的 iOS 项目基本上都采用了这种模式进行开发。然而，随着项目规模的增长，MVC 模式已经无法很好地帮助我们解耦，构建一个易维护的项目变得愈发艰难。

#### 2.1.1 巨型 ViewController

业务逻辑愈加复杂，导致项目的代码量剧增是最直接的原因。某个 View Controller  可能会有几千行代码，这样的项目代码易读性非常差，维护难度也会增大。厚重的 View Controller 很难维护（由于其庞大的规模）；包含几十个属性，使他们的状态难以管理；遵循许多协议（protocol），导致协议的响应代码和 controller 的逻辑代码混淆在一起……

巨型 Controller 一般包含以下内容：

1. 生命周期
2. UI 排版
3. UI 交互
4. 数据管理
5. 业务逻辑
6. 数据上报
7. ……

由于 ViewController 在 MVC 架构中间是逻辑关系主要的承接方，因此 ViewController 随着业务的增加必定会持续增长变得臃肿。

也有一些给 ViewController“减肥”的措施，例如 objc 中借助分类将 ViewController 拆成多个部分，或者 Rails 中建议的 [Fat models, Skinny controllers](https://dev.to/kputra/rails-skinny-controller-skinny-model-5f2k#phase-2) 尝试，但是这只是治标不治本的方法，Controller 依旧在膨胀。

#### 2.1.2 强耦合的 View 和 Controller

在 iOS 里面 MVC 的实现方式很难做到如 1.2.1 架构图的那样，因为由于 Apple 的规范，一个界面的呈现都需要构建一个 viewcontroller，而每个 viewcontroller 都带有一个根 view，这就导致 C 和 V 紧密耦合在一起构成了 iOS 里面的 C 层。

![image](/image/MVCMVP/image19.png)

强耦合的 View 和 Controller，没有区分业务逻辑和业务展示，这对单元测试&自动化测试并不是特别友好。为了测试业务逻辑（页面跳转、点赞、分享），我们必须先生成对应的 View，然后才能进行测试，显然这并不合理。业务逻辑最终改变的是 Model，我们关注点应该在 Controller 和 Model 上，而不是外在 View。

#### 2.1.3 难以放置的数据逻辑

按照苹果 MVC，所有对象可以被归类为一个 model，一个 view 或者一个 controller，就这些。那么和一个外部 API 通信（网络通信、数据库）的逻辑应该放到哪？

你可以放在 Model 中，但是网络调用/外部调用，应该是需要异步进行的，这样如果一个网络请求比持有它的 Model 生命周期更长，事情将会变得复杂。那如果放在 Controller 中呢？这也不是一个合适的选择，因为这加剧了 Controller 的臃肿为题，而且还相当于将部分 Model 的概念移动到 Controller 中了，Model 和 Controller 强耦合起来了。

同理，还有数据格式化逻辑、数据处理逻辑等等一堆难以抉择的问题。

### 2.2 MVC 到 MVP

针对以上 MVC 所带来的问题，IBM 子公司 Taligent 在 90 年代提出了 MVC 的衍生模式——MVP，MVP 进过不断的发展应用，逐渐成为了完善的 MVC 解决方案。MVP 的核心就是：让 M 和 V 完全解耦，通过 Presenter 统一调度管理。

### 2.3 架构组成

下图展示了 MVP 和 MVC 之前的架构区别。MVP 和 MVC 很类似，MVP 的三个层次与 MVC 三个层次基本相同，唯一区别是 Model 和 View 之间不进行通讯，完全是通过 Presenter 完成：

![image](/image/MVCMVP/image20.png)

- Model：数据模型，和 MVC 中的 Model 一样
- View：对应 UI 界面（包括 ViewController、所有视图），负责 View 的绘制以及与用户交互
- Presenter：调度者，负责完成 View 和 Model 间的交互

从字面意思来理解，MVP 即 Model View Presenter（模型 视图 协调器）。MVP 实现了 Cocoa 的 MVC 的愿景。MVP 的协调器 Presenter 并没有对 ViewController 的声明周期做任何改变，因此 View 可以很容易的被模拟出来。在 Presenter 中根本没有和布局有关的代码，但是它却负责更新 View 的数据和状态。

简单理解，将一个以 MVC 实现的 App 中 ViewController 的 UI 管理、UI 交互、生命周期逻辑剥离，剩下的 ViewController 就成为了 Presenter。当然 Presenter 一般不会持有所有的 View 对象，而是持有 ViewController 对象，ViewController 是 Presenter 和真正 UI 对象的中间人。为了 Presenter 的通用性，Presenter 一般也不直接持有实际 ViewController 对象并进行调用，而是通过协议接口对 ViewController 进行管理。

### 2.4 MVP 实现

和 MVC 一样，MVP 也有许多种实现方式，即可以使用通知方式实现三个层次之间的通信，也可以利用 KVO 达成目标，或者利用协议以及闭包方式。

这里以一个比较简单的例子 [ios-mvp-swift（邮箱通讯录）](https://github.com/nandawperdana/ios-mvp-swift)进行以协议接口方式实现的 MVP 方案。

- Presenter -> View：可以看到 Demo 中，仅包含了一个业务 Presenter。Presenter 首先定义了 View 所需要遵循的协议接口。同时 Presenter 持有一个 var 对象，用于后续绑定具体 ViewController。Presenter 不直接持有具体对象，这给 Presenter 的复用带来了极大的方便，而且也极大方便了 Presenter 的单元测试
  ![image](/image/MVCMVP/image21.png)

- View -> Presenter：View 持有 presneter 对象，在生命周期中，View 也通过 presenter 提供的 attachView 方法进行绑定，建立 View 和 Presenter 的链接。一个 View 可以绑定多个 Presenter，这样一整个 App 就可以切成多个不同的业务点放置到不同 Presenter 中。同时 View 也实现 Presenter 所定义的视图接口，
    ![image](/image/MVCMVP/image22.png)
    ![image](/image/MVCMVP/image23.png)

简单理解一下，即当 View 触发动作时，如页面加载完成，就会直接调用 presenter 所提供的数据获取方法。

在 demo 中，Presenter 通过 getPeople 方法实现了了 View 和 Model 的解耦，我们来看下是怎么做的。

![image](/image/MVCMVP/image24.png)
![image](/image/MVCMVP/image25.png)

- Presenter 通过调用 view 对象的 startLoading 接口，view 开始展示加载态
- Presenter 调用 Service 的数据加载逻辑，Service 会加载网络数据并且通过回调形式返回 Model 对象
- Presenter 在 Service 的请求闭包中，会根据 Model 内容控制 View 的展示，人数等于 0，View 展示空白态，否则展示具体内容。

结合整个流程，View 和 Prensenter 通过协议接口建立联系，Presenter 和 Model 通过闭包方式建立联系。Model 全程不知道 View 的存在，View 也全程不知道 Model 的存在，两者隔离。

![image](/image/MVCMVP/image26.png)

### 2.5 总结

对比使用 MVC，MVP 可以更好地为代码进行划分。在上面的例子中，如果使用 MVC，那么业务逻辑就会放置在 ViewController 中，简单的功能点还好，但是如果后续继续拓展起来就不好维护了。

当然 MVP 也存在一些劣势，比如接口和类数量的增加，但是对比起几千行的 ViewController 来说，这也是值得的。此外使用 MVP 也存在如何划分 Presenter 粒度的问题，粒度过小，presenter 数量膨胀，粒度过大，presenter 又会像 ViewController 一样臃肿，所以如何划分粒度也是值得去实践和考虑的。

## 二、MVVM

### 3.1 历史

MVVM 同样也是一个 MVC 改良型架构。MVVM 最早于 2005 年被微软的 WPF 和 Silverlight 的架构师 John Gossman 提出，并且应用在微软的软件开发中。

### 3.2 架构组成

下图是大多数博客以及文章介绍 MVVM 所使用的架构图：

![image](/image/MVCMVP/image27.png)

从图中可以看出，MVVM 和 MVP 非常相似，MVVM 模式将 Presener 改名为 View Model，除此之外基本上与 MVP 模式完全一致。唯一的区别是，它采用双向绑定（data-binding）：View 的变动，自动反映在 ViewModel，反之亦然。

在阅读资料的时候，笔者比较倾向于将 MVVM 理解成 MVP 的技术进阶版，也就是在 MVP 基础上使用诸如 RAC、双向绑定的具体能力达到抽离 Controller 逻辑的目标。MVVM 不是一个纯的抽象概念，需要附以具体的框架来进行理解。从这个角度来说，MVVM = MVP + 具体技术。

3.3 MVVM 实现

笔者暂时还没去深入学习过 MVVM 的实现，就不班门弄斧了。推荐读者自行搜索学习一下

## 四、总结

从代码角度上来说，MVC、MVP、MVVM 本质上没什么，侧重点在于将如何同类型的代码（UI、逻辑、数据）封装到不同的层次中，并且利用各种技术进行层次之间的解耦，方便开发和调试。

笔者看来，RAC 可以用在 MVP 上，消息通知也可以放在 MVC、MVVM 上，MVVM 上使用 block 和 delegate 也没有什么错误。关键是能不能准确清晰的表达自己的逻辑、UI、数据。能把这三部分切割分离，做到开发者可以清晰地感知各个部分的作用和能力，那在具体实现上也可以稍微搞点外门邪道。同时合适的架构并不一定意味着代码量的减少，有时候反而代码量和文件数会增加一些。但是采用新的架构的原则一定是这个架构可以让我们的项目结构变得清晰，维护成本大大降低。

当然，由于 MVC/MVP/MVVM 实现方式的侧重点不同，不同类型的项目还是存在模式选择差异的：

- 如果项目比较简单，复杂性角度，迭代更改稍，那就可以不用管设计模式了，将每个模块封装好，方便调用就好
- 在上述基础上，如果只是存在部分复杂逻辑的情况，或者存在复用性要求，可以使用 MVC 来进一步强化项目的结构。MVC 也是 MVP、MVVM 的基础，在 MVC 基础上进行改进，最终也可能会演进成 MVP、MVVM
- 如果偏向展示型的项目，绝大多数业务逻辑都是后端负责处理，app 主要功能是展示数据和交互，可以考虑使用 MVVM
- 对于工具类或者本地存在比较多业务逻辑的项目，可以考虑 MVP 或 MVVM

不管是哪种模式，只要使用得当，都可以达到想要的结果。
