# $(Navigation)

大多数应用程序包含的不只是一个页面。Fuse有几个组件用于制作和自定义不同页面之间的导航。

让我们来看一个简单的例子:我们想要一组@(Page:pages)，我们可以@(SwipeNavigate:swipe)之间。

## $(基本导航)

首先，我们创建一组@(面板:面板)，代表我们的@(Page:页面)。

```
<面板>
	<面板背景=“红色”/>
	<面板背景=“蓝色”/>
	<面板背景=“绿色”/>
</ Panel>
```

目前这只是一个@(面板)里面有三个@(面板:面板)里面。为了使我们的滑动导航成为可能，我们需要添加三件事。

-  A(导航)行为
-  A(SwipeNavigate)行为
- 进入和退出动画我们的面板

我们首先添加一个@(LinearNavigation)和一个@(SwipeNavigate)行为。
@(LinearNavigation)只是可以使用的几个@(导航类型:导航类型)之一。

```
<面板>
	<LinearNavigation />
	<SwipeNavigate SwipeDirection =“左”/>
	<面板背景=“红色”/>
	<面板背景=“蓝色”/>
	<面板背景=“绿色”/>
</ Panel>
```

这增加了导航所需的所有行为，但这不足以让我们在页面之间滑动。
页面不知道当他们@(EnteringAnimation:enter)或@(ExitingAnimation:exit)时如何动画。
为此我们@(创建自定义UI组件:子类)@(Panel)，并添加一个@(EnteringAnimation)和一个@(ExitingAnimation)。

```
<面板>
	<Panel ux:Class =“NavPanel”>
		<EnteringAnimation>
			<Move X =“ -  1”RelativeTo =“ParentSize”/>
		</ EnteringAnimation>
		<ExitingAnimation>
			<Move X =“1”RelativeTo =“ParentSize”Duration =“0.5”/>
		</ ExitingAnimation>
	</ Panel>
	<LinearNavigation />
	<SwipeNavigate SwipeDirection =“左”/>
	<NavPanel Background =“Red”/>
	<NavPanel Background =“Blue”/>
	<NavPanel Background =“Green”/>
</ Panel>
```

现在@(面板:面板)将移动到它们的父容器的宽度，当他们导航到和从。

## $(Page)

当@(面板)包含@(导航)行为时，其所有直接子页面都将作为该导航上下文中的页面。因为我们通常想要的样式我们的页面有点不同于其余的@(面板:面板)，Fuse有一个`页面类型，我们可以改用。`Page`类型与@(Panel)几乎没有什么不同，但是为了方便和语义值，它带有一个`Title`属性。

## $(PageControl)

因为页面之间的滑动是这样常见的交互，所以Fuse正好有一个@(Control)。

```
<PageControl>
	<面板背景=“红色”/>
	<面板背景=“蓝色”/>
</ PageControl>
```

上面的例子说明了`PageControl`的默认行为，它是为了响应滑动手势而滑动页面。
但是，我们可以使用以下属性自定义此行为:

- `InactiveState`控制孩子离开屏幕时的“Visibility”和“IsEnabled”状态。
	- `Collapsed` _(默认)_  - 设置`Visibility =“Collapsed”`，以便隐藏非活动页面，而不计算布局。
	- `Disabled`  - 非活动页面是可见的，但通过设置`IsEnabled =“false”`禁用指针交互。
	- `Unchanged`  - 非活动页面按原样保留。
- “Transition”控制页面在导航期间的转换方式
	- `Standard` _(默认)_  - 执行标准的横向滑动动画。
	- `None`  - 不执行动画。使用它来实现您自己的动画。
- “交互”控制如何处理用户交互
	- `滑动_ _(默认)_  - 用户可以在页面之间滑动。
	- `无 - 用户不能直接与导航交互。使用它来实现您自己的导航控件/手势。

通过使用数据绑定，可以通过`Name`使用`Active`属性设置当前活动页面。
在下面的示例中，我们有三个页面和一个将用户返回到第一页的按钮。

```
<DockPanel>
	<JavaScript>
		var Observable = require(“FuseJS / Observable”);
		var currentPage = Observable(“page1”);
		function clickHandler(){
			currentPage.value =“page1”;
		}}
		module.exports = {
			clickHandler:clickHandler，
			currentPage:currentPage
		};
	</ JavaScript>
	<PageControl Active =“{currentPage}”>
		<面板名称=“page1”背景=“红色”/>
		<面板名称=“page2”背景=“绿色”/>
		<面板名称=“page3”背景=“蓝色”/>
	</ PageControl>
	<Button Text =“Home”Clicked =“{clickHandler}”Dock =“Bottom”/>
</ DockPanel>
```

看看[使用JavaScript的页面](https://www.fusetools.com/community/examples/pageslist)示例，了解如何在实践中使用它。

### $(PageIndicator)

`PageIndicator`可以与@(PageControl)一起使用，以清楚地显示所选择的页面。以下示例在@(PageControl)中有四个页面，并且每个@(Page)都有一个停靠在底部的圆。当@(Page)变为“活动”时，其相应的指示符被缩放1.3倍。

	<DockPanel>
		<PageControl ux:Name =“pageControl”>
			<Page Background =“Red”/>
			<Page Background =“Blue”/>
			<Page Background =“Green”/>
			<Page Background =“Yellow”/>
		</ PageControl>
		<PageIndicator Navigation =“pageControl”Dock =“Bottom”Alignment =“Center”>
			<Circle ux:Generate =“Factory”ux:Binding =“DotFactory”
					Fill =“Teal”Margin =“5”Width =“20”Height =“20”>
				<ActivatingAnimation>
					<比例因子=“1.3”/>
				</ ActivatingAnimation>
			</ Circle>
		</ PageIndicator>
	</ DockPanel>

必须为@(PageIndicator:页面指示符)`Navigation`属性设置一个值。在这种情况下，我们简单地将其设置为@(PageControl)。

另一个要注意的是，PageIndicator使用一个`Factory`来为每一个页面创建一个指标。在上面的例子中，我们使用@(ux:Generate)属性设置为“Factory”的@(Circle)和设置为页面指示器的“DotFactory”属性的@(ux:Binding)属性作为我们的“模板”。如果没有设置，$(PageIndicator:page indicator)将默认使用`PageIndicatorDot`，一个简单的点模板在很多情况下会执行。

## {Page}绑定


`{Page name}`语法允许链接当前活动导航页面上的资源。它的工作原理很像“{Resource name}”，除了它从当前页面而不是当前节点开始查找。一个常见的用法是显示当前页面的标题。

在下面的例子中，我们有一个带有@(Panel)和@(PageControl)的@(DockPanel)。我们希望@(Panel)的内容显示当前@(Page)的标题，所以我们将其Navigation属性设置为@(PageControl)。

```
<DockPanel>
	<Panel Navigation =“myNav”Dock =“Top”Height =“100”Background =“Teal”>
		<Text Value =“{Page title}”FontSize =“22”Alignment =“Center”/>
	</ Panel>
	<PageControl ux:Name =“myNav”>
		<Page Background =“Red”>
			<string ux:Key =“title”ux:Value =“第一页”/>
		</ Page>
		<Page Background =“Green”>
			<string ux:Key =“title”ux:Value =“第二页”/>
		</ Page>
	</ PageControl>
</ DockPanel>
```

`Navigation =“myNav”`建立此节点及其后代的导航上下文。它可以放在任何节点上，并在子节点上覆盖。引用导航的任何绑定，手势或其他，现在将引用这一个。

为了显示页面的标题，我们使用`{Page title}`binding:

```
<Text Value =“{Page title}”/>
```

`{Page title}`是一个从导航的当前页面开始的资源绑定(我们用`Navigation =“TheNav”`指定的资源绑定)。当前页面改变时，这个值也会改变。

### 定义标题

页面的标题仍需要在某处定义。我们在导航的页面上使用通用资源语法:

	<string ux:Key =“Title”ux:Value =“第一页”/>

`ux:Key =“Title”定义名称为“Title”的资源，ux:Value指定值。这些只是正常的资源 - 任何可以使用资源的东西也将通过`{Page}`绑定。

### PageIndicator

此功能可用于创建有趣的页面指示器。我们可以用每个页面的图像替换点。在页面上，我们定义我们要使用的图像源:

	<FileImageSource ux:Key =“Image”File =“icons_200 / bowling.png”/>

再次，这只是一个正常的资源绑定，恰恰是指一个图像源。

在每个页面上，我们可以创建一个使用图像的“PageIndicator”:“dots”:

	<PageIndicator Navigation =“TheNav”...>
		<Image ux:Binding =“DotFactory”ux:Generate =“Factory”Source =“{Page Image}”...>

<!--  todo:写关于工厂 -->

## $(导航类型)

有几种导航类型，它们有非常不同的行为和用例。

它们都继承于`Navigation`基类型，它暴露`Active`属性，它设置当前活动的页面。这可以是数据绑定到节点的字符串名称。

### $(LinearNavigation)

`LinearNavigation`用于每个页面应该线性布局。因此，使用滑动导航，从第1页开始，滑动到第2页，然后是第3页，依此类推。从第1页直接浏览到第3页会导致快速访问第2页。

### $(DirectNavigation)

使用`DirectNavigation`，在页面之间没有隐式流。任何@(Page)可以从任何其他@(Page)直接导航到。如果在我们的@(导航)中有5个页面，当从第1页到第5页导航时，不会滚动其他页面，因为会有@(LinearNavigation)。

### $(HierarchicalNavigation)

当存在分层的页面流时，通常使用“HierarchicalNavigation”。这通常可在iOS和Android设备上的设置应用中找到。首先选择一个主题，然后选择一个子主题等等，让我们更深入地选择选项的层次结构。在此上下文中导航是指将页面推送到一堆页面上。对于每个导航有一个自然的后退导航，带回我们导航的页面。

> ### HierarchicalNavigation示例

这是一个演示使用`HierarchicalNavigation`结合使用@(NavigateTo)，@(GoBack)，@(GoForward)和@(WhileCanGoForward)的示例。

```
<面板>
	<HierarchicalNavigation ux:Name =“nav”Active =“mainPage”/>

	<Page ux:Class =“MyPage”>
		<EnteringAnimation>
			<Move X =“1”RelativeTo =“ParentSize”/>
		</ EnteringAnimation>
		<ExitingAnimation>
			<Move X =“ -  1”RelativeTo =“ParentSize”/>
		</ ExitingAnimation>
	</ Page>

	<MyPage ux:Name =“mainPage”>
		<StackPanel>
			<Button Text =“Page 1”>
				<Clicked>
					<NavigateTo Target =“subPage1”/>
				</ Clicked>
			</ Button>
			<Button Text =“Page 2”>
				<Clicked>
					<NavigateTo Target =“subPage2”/>
				</ Clicked>
			</ Button>
			<WhileCanGoForward>
				<Button Text =“Go Forward”>
					<Clicked>
						<GoForward />
					</ Clicked>
				</ Button>
			</ WhileCanGoForward>
		</ StackPanel>
	</ MyPage>

	<MyPage ux:Name =“subPage1”ux:AutoBind =“false”>
		<StackPanel>
			<Text>欢迎来到第1页！</ Text>
			<Button Text =“Go Back”>
				<Clicked>
					<GoBack />
				</ Clicked>
			</ Button>
		</ StackPanel>
	</ MyPage>

	<MyPage ux:Name =“subPage2”ux:AutoBind =“false”>
		<StackPanel>
			<Text>欢迎来到第2页！</ Text>
			<Button Text =“Go Back”>
				<Clicked>
					<GoBack />
				</ Clicked>
			</ Button>
		</ StackPanel>
	</ MyPage>
</ Panel>
```

### $(EdgeNavigator)

当我们的目的是从一个侧面导航菜单时使用'EdgeNavigator'，如我们的[社交媒体示例](https://www.fusetools.com/examples/social-media-screen)所示。它通过在一个`EdgeNavigator`元素中在可导航区域内包含您想要的内容，并在@(面板)上设置`Edge`属性来告诉导航器您希望该面板从哪个边导航。

```
<EdgeNavigator>
	<面板宽度=“150”边缘=“左”背景=“#f63”/>
	<Panel Background =“#fff”>
		<文本对齐=“中心”>
			这是EdgeNavigator的一个示例！
		</ Text>
	</ Panel>
</ EdgeNavigator>

```

注意，在这个例子中设置第二个@(Panel)的@(Background)是有意义的，因为它能够对内部@(Panel)进行命中测试。您也可以将@(EdgeNavigator)的@(HitTestMode)设置为@(HitTestMode.LocalBoundsAndChildren)。通常这不是一个问题，因为内部面板将有内容可以测试。


### $(EdgeNavigation)

虽然@(EdgeNavigator)将在大多数情况下执行此工作，但如果您对“EdgeNavigator”的行为不满意(您可能想要更改默认动画)，则可能对使用“EdgeNavigation”感兴趣。以下示例将重新创建“EdgeNavigator”提供的效果，但具有更多的可定制性:

```
<App Theme =“Basic”>
	<DockPanel>
		<EdgeNavigation />
		<面板宽度=“150”背景=“#0F0”边缘=“左”对齐=“左”>
			<EnteringAnimation>
				<Move X =“ -  1”RelativeTo =“Size”/>
			</ EnteringAnimation>
		</ Panel>
		<面板背景=“#F00”/>
	</ DockPanel>
</ App>
```


## $(控制导航)

保险丝提供了几个@(操作:操作)，允许您执行@(导航:导航)。

所有与导航相关的@(Actions:actions)都有一个`NavigationContext`属性，允许您指定导航上下文以执行操作。
如果没有指定`NavigationContext`，它将寻找一个具有`Navigation`行为的父元素，并使用它。

> ###手动指定导航上下文

```
<DockPanel>
	<PageControl ux:Name =“navContext”>
		<Page ux:Name =“page1”Background =“#f00”/>
		<Page ux:Name =“page2”Background =“#00f”/>
	</ PageControl>
	<Grid Dock =“Bottom”Columns =“1 *，1 *”>
		<Button Text =“Go to page 1”>
			<Clicked>
				<NavigateTo Target =“page1”NavigationContext =“navContext”/>
			</ Clicked>
		</ Button>
		<Button Text =“Go to page 2”>
			<Clicked>
				<NavigateTo Target =“page2”NavigationContext =“navContext”/>
			</ Clicked>
		</ Button>
	</ Grid>
</ DockPanel>
```

### 回去

`GoBack`的行为取决于它执行的导航上下文的类型:

-  @(LinearNavigation) - 导航到当前页面之前发生的页面。
-  @(HierarchicalNavigation) - 在层次结构中向上导航一层，即最近导航到的页面。
-  @(DirectNavigation) - 什么也不做。

> ### $(WhileCanGoBack)

只要向后导航就可以使用`WhileCanGoBack`触发器。

### 前进

与@(GoBack)一样，`GoForward`也是上下文相关的:

-  @(LinearNavigation) - 导航到当前页面之后出现的页面。
-  @(HierarchicalNavigation) - 在层次结构中向下浏览一层，即用户有@(GoBack:返回)的最后一页。
-  @(DirectNavigation) - 什么也不做。

> ### $(WhileCanGoForward)

只要向前导航就可以使用`WhileCanGoForward`触发器。


### $(NavigateTo)

导航到由“Target”属性指定的特定@(页:page)。下面是使用@(PageControl)的示例。

```
<PageControl ux:Name =“nav”>
	<Page ux:Name =“page1”>
		<Button Text =“Go to page 2”Alignment =“Center”>
			<Clicked>
				<NavigateTo Target =“page2”/>
			</ Clicked>
		</ Button>
	</ Page>
	<Page ux:Name =“page2”>
		<文本对齐=“中心”>
			欢迎来到第2页！
		</ Text>
	</ Page>
</ PageControl>
```

## $(EnteringAnimation)/ $(ExitingAnimation)

如上所述，Entering-和ExitingAnimation用于指定页面和元素在导航到和导航时的行为。
当使用`Navigation`时，元素没有默认行为，除非使用了一个@(PageControl)(它在页面之间应用动画)，你必须自己设置它。

您可以扩展“面板”以应用所有页面的动画:

```
	<Page ux:Class =“AnimatedPage”>
		<EnteringAnimation>
			<Move X =“ -  1”RelativeTo =“ParentSize”Duration =“0.5”/>
		</ EnteringAnimation>
		<ExitingAnimation>
			<Move X =“1”RelativeTo =“ParentSize”Duration =“0.5”/>
		</ ExitingAnimation>
	</ Page>

	<面板>
		<LinearNavigation />
		<AnimatedPage Background =“Red”/>
	</ Panel>
```

它只是将每个页面移动其父级的宽度，这是@(LinearNavigation)情境的常见样式。

当然，在导航时可以动画的没有限制。以下示例还会在页面进入/退出时@(缩放:缩放)。

```
<Page ux:Class =“AnimatedPage”>
	<EnteringAnimation>
		<Move X =“ -  1”RelativeTo =“ParentSize”Duration =“0.5”/>
		<比例因子=“0.2”持续时间=“0.5”/>
	</ EnteringAnimation>
	<ExitingAnimation>
		<Move X =“1”RelativeTo =“ParentSize”Duration =“0.5”/>
		<比例因子=“0.6”持续时间=“0.5”/>
	</ ExitingAnimation>
</ Page>
```

## $(ActivatingAnimation)

`ActivatingAnimation`被触发，当页面被导航到和从(当它成为活动页面时)。当@(Page)被导航到，任何`ActivatingAnimation'被触发。动画从页面进入时的0进度，当页面完成进入时变为1。当页面退出`ActivatingAnimation`时，从1回到0。

这里是更高级的使用@(EnteringAnimation:Entering-)，@(ExitingAnimation:Exiting-)和@(ActivatingAnimation):

```
<Page ux:Class =“MyPage”ux:Name =“self”>
	<EnteringAnimation>
		<Move RelativeTo =“ParentSize”>
			<关键帧X =“ -  0.7”时间=“0.5”/>
			<关键帧X =“0”时间=“1”/>
		</ Move>
		<旋转>
			<关键帧角度Z =“20”时间=“0.5”/>
			<关键帧角度Z =“0”时间=“1”/>
		</ Rotate>
		<Change self.Opacity =“0”Duration =“1”/>
		<比例因子=“。5”持续时间=“1”/>
	</ EnteringAnimation>
	<ExitingAnimation>
		<Move RelativeTo =“ParentSize”>
			<关键帧X =“0.7”时间=“0.5”/>
			<关键帧X =“0”时间=“1”/>
		</ Move>
		<旋转>
			<关键帧度Z =“ -  20”时间=“0.5”/>
			<关键帧角度Z =“0”时间=“1”/>
		</ Rotate>
		<比例因子=“。5”持续时间=“1”/>
	</ ExitingAnimation>
	<WhileActive Threshold =“0.5”>
		<BringToFront />
	</ WhileActive>
</ Page>
```

<!--  TODO:记录发生了什么？ -->

## $(WhileActive)

`WhileActive`动画为一个页面变得活跃。

## $(WhileInactive)

`WhileInactive'动画为一个页面变得不活动。


## $(WhileInEnterState)

这与@(WhileInactive)相同，但不同之处在于，如果它绑定到的面板在可导航面板列表中的当前面板之后(例如，它是当前面板的_after_)，则它具有负进度值。

## $(WhileInExitState)

这也主要与@(WhileActive)相同，但是如果它在可导航面板列表中的当前导航面板之后，具有负值，这与@(WhileInEnterState)不同。

## $(WhileNavigating)

只要用户当前正在两个面板之间导航，此操作就会打开。它不提供进展。

## $(SwipeNavigate)

`SwipeNavigate`用于将滑动手势连接到@(导航)。这在@(EdgeNavigator)内部使用，但你会感兴趣的，例如，如果你想水平堆叠的页面。`SwipeNavigate`有三个属性修改其行为:

* @(SwipeDirection) - 指定用户必须滑动才能导航“前进”的方向。
* @(SwipeEnds) - 决定动画如何处理那里没有下一个或上一个元素导航到。“关闭”将动画限制到最后一个元素的结尾，“打开”允许用户无阻碍地滑动，“短”增加对动画的抵抗力，有效地在用户进一步滑动时减慢动画。
* @(VelocityThreshold) - 捕捉到下一个元素所需的最小速度。

### $(SwipeDirection)

可以有值“Down”，“Right”，“Left”和“Up”。这些值表示为了导航到下一页而滑动的方向。

### $(SwipeEnds)

可以有值“Closed”，“Open”和“Short”。

`关闭'意味着当到达第一页或最后一页时，无法进一步滑动。
`打开'意味着当到达第一页或最后一页时，可以尽可能多地进一步滑动。这并不意味着它可能导航到一个undefined @(Page)。
`Short`的行为类似于`Open`，除了只能在第一页或最后一页之外滑动一小段距离。

### $(VelocityThreshold)

`VelocityThreshold`用于决定一个页面的刷新速度，以便将其解释为导航。这意味着，一个人不必刷扫整个页面，但可以逃避一个短而快的轻弹手势。


## Databind活动页面

很有可能通过数据绑定执行导航。
为此，我们将导航“Active`属性数据绑定到JavaScript中的字符串。此字符串应对应于我们要导航到的@(页面)的名称。

<!--  TODO:创建两个页面和直接导航都是数据绑定的示例。(也许是分层？) -->
