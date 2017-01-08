＃$（数据绑定）

Fuse通过直接绑定，迭代和分支为使用UX标签创建数据驱动的应用程序提供一流的支持。UX还可以引用深入的复杂数据结构，因此您不必在代码中执行繁琐的数据按钮。

## $（数据上下文）

在保险丝`Node`树中的任何一点，都有一个*数据上下文*。任何节点上的数据绑定将与节点上的当前数据上下文相关。默认情况下，这个数据上下文是“null”，任何数据绑定都将返回null或空值。上下文还将沿着树向下传播，这意味着如果子节点不提供数据上下文，则它将使用其父节点的数据上下文。

要设置数据上下文，通常向提供数据上下文的节点添加* behavior *，例如`<JavaScript>`。
数据上下文也可以使用`DataContext`属性显式设置。

## JavaScript模块作为数据源

创建数据源的最简单的方法是通过JavaScript，这里是一个数据绑定的“Hello world”最小示例：

	<App>
		<JavaScript>
			module.exports = {
				问候：“你好数据世界！
			};
		</ JavaScript>
		<Text Value =“{greeting}”/>
	</ App>

类似地，您可以绑定到集合：

	<App>
		<JavaScript>
			var data = [“1”，“2”，“3”];

			module.exports = {
				data：data
			};
		</ JavaScript>
		<StackPanel>
			<Each Items =“{data}”>
				<Text Value =“{}”/>
			</ Each>
		</ StackPanel>
	</ App>

这将可预见地列出文本字符串1,2和3.当绑定“文本值”时，{}表示_this数据上下文_。通常，您将绑定到更复杂的数据源，因此每个元素都将有一些有趣的绑定到：

	<App Theme =“Basic”>
		<JavaScript>
			var Observable = require（“FuseJS / Observable”）;

			var data =
				{name：“Hubert Cumberdale”，age：12}，
				{name：“Marjory Stewart-Baxter”，age：43}，
				{name：“Jeremy Fisher”，age：25}）;

			module.exports = {
				data：data
			};
		</ JavaScript>
		<StackPanel>
			<Each Items =“{data}”>
				<DockPanel>
					<Text Dock =“Right”Value =“{age}”/>
					<Text Value =“{name}”/>
				</ DockPanel>
			</ Each>
		</ StackPanel>
	</ App>

在这种情况下，我们也做了数据源@（Observable）。这意味着它支持在运行时将更改传播到数据源。在这种情况下，集合本身是“Observable”，但项目不是。您可以绑定到孩子，但如果他们要更改，这些更改不会反映在UI中。如果你想让孩子们将他们的更改传播到UI，你也可以使他们成为“可观察”：

	<JavaScript>
		var Observable = require（“FuseJS / Observable”）;
		var data =
			{name：Observable（“Hubert”）}，
			{name：Observable（“Marjory”）}）;
		module.exports = {
			data：data
		};
	</ JavaScript>
	<StackPanel>
		<Each Items =“{data}”>
			<Text Value =“{name}”/>
		</ Each>
	</ StackPanel>

您还可以绑定到路径：

	<JavaScript>
		var complex = {
			user：{
				userinfo：{
					名称：“Bob”
				}}
			}}
		};
		module.exports = {
			复杂：复杂
		};
	</ JavaScript>
	<Text Value =“{complex.user.userinfo.name}”/>

这在绑定到任意数据源（例如从REST服务以JSON返回的数据源）时非常有用，因为它通常允许您直接绑定到复杂数据，而无需首先处理代码中的数据。[见这个深入的例子]（https://www.fusetools.com/developers/examples/newsfeed）。

###绑定到JavaScript函数

您可以连接事件处理程序，以类似的语法调用JavaScript函数：

	<JavaScript>
		module.exports = {
			clickHandler：function（args）{
				console.log（“I was clicked：”+ JSON.stringify（args））;
			}}
		};
	</ JavaScript>
	<Button Clicked =“{clickHandler}”Text =“点击我！/>

你可以在@（FuseJS）部分阅读更多。

> ##其他数据源

从@（Uno）实现的观点来看，Fuse中的数据绑定系统称为“Fuse.Reactive”。它由一组可以在@（Uno）代码中的任何数据源之上实现的接口（主要是`IObservable`）组成，这些接口又可以用作Fuse节点上的@（数据上下文）。保险丝在@（FuseJS）的@（Observable）之上提供了开箱即用的实现。

我们计划为Swift，Java和其他语言提供可观察的数据类型，可以直接在UX标记中使用数据绑定。

编写@（Uno）代码的用户可以手动实现`IObservable`，以便在定制本地数据源之上使用Fuse的数据绑定系统。

## $（每个）

`each`用于重复集合中每个项目的UX标记。

`Each`行为在其$（Items）集合中为每个项目维护一个子树的副本，并相应地从父节点添加和删除它们。`Items`集合可以是一个可以动态改变的@（Observable）。

当使用`Each`时，我们通常将`Items`属性数据绑定到数组数据源以产生一个视觉效果
节点数据源中的每个对象。

	<Each Items =“{items}”>
		<Rectangle Width =“{width}”Height =“{height}”Fill =“＃808”/>
	</ Each>

`Items`集合上的可观察添加/删除操作可以使用@（AddingAnimation），@（RemovingAnimation）和@（LayoutAnimation）

也可以嵌套`Each`行为：

```
<JavaScript>
	var Observable = require（“FuseJS / Observable”）;
	module.exports = {
		项目：[
			{
				inner：[
					{child：“John”}，
					{child：“Paul”}
				]]
			}，
			{
				inner：[
					{child：“Ringo”}，
					{child：“George”}
				]]
			}}
		]]
	};
</ JavaScript>
<ScrollViewer>
	<StackPanel>
		<Each Items =“{items}”>
			<StackPanel Orientation =“Horizo​​ntal”>
				<Each Items =“{inner}”>
					<Text Value =“{child}”Margin =“10”/>
				</ Each>
			</ StackPanel>
		</ Each>
	</ StackPanel>
</ ScrollViewer>
```

你可以使用Count属性来获取被迭代的项目数。

>注意：不可能在没有中间容器的情况下将两个`Each`行为直接嵌套在一起，如上面的`Panel`或`StackPanel`。

你也可以使用`Each`作为一个简单的中继器：

	<Grid ColumnCount =“3”>
		<Every Count =“9”>
			<Rectangle Margin =“10”Fill =“＃610”/>
		</ Each>
	</ Grid>


> ##确保订单

请注意，`Each`不一定将子节点插入`Each`节点所在的树中的确切位置。为了确保子订单，将`Each`包装在某种@（Panel）中：

	<StackPanel>
		<Text>我先走！</ Text>
		<StackPanel>
			<Each Items =“{strings}”>
				<Text Value =“{}”/>
			</ Each>
		</ StackPanel>
		<Text>我去最后！</ Text>
	</ StackPanel>

我们正在考虑增加更多的技巧，在这种情况下允许更多的灵活性，但现在这是推荐的方法。

## $（WhileCount）和$（WhileEmpty）

`WhileEmpty`和`WhileCount` @（Trigger：triggers）可用于对集合中的项目数量进行操作：

	<Each Items =“{friends}”>
		<！ -  ...列出朋友...  - >
	</ Each>
	<WhileEmpty Items =“{friends}”>
		<Text>没有朋友。：（</ Text>
	</ WhileEmpty>

`WhileEmpty`是“WhileCount”的一个特例，其中“EqualTo”属性设置为“0”。`WhileCount`接受以下属性：

- `EqualTo`  - 当集合的计数等于提供的值时处于活动状态
- `GreaterThan`  - 当集合的计数大于提供的值时处于活动状态
- `LessThan`  - 当集合的计数小于提供的值时处于活动状态

例如：

	<WhileCount Items =“{things}”EqualTo =“3”GreaterThan =“3”>
		<Text>你有3件或更多的东西。</ Text>
	</ WhileCount>

## $（选择）

如果你有一个复杂的数据上下文，想要缩小数据上下文，你可以使用`Select`：

	<JavaScript>
		module.exports = {
			complex：{
				item1：{
					subitem1：{name：“Spongebob”，age：32}
				}}
			}}
		};
	</ JavaScript>
	<Select Data =“{complex.item1.subitem1}”>
		<Text Value =“{name}”/>
		<Text Value =“{age}”/>
	</ Select>


## $（Match）和$（Case）

您可以使用“Match”和“Case”驱动哪个子树处于活动状态：

	<JavaScript>
		module.exports = {
			活动：“蓝色”
		};
	</ JavaScript>
	<Match Value =“{active}”>
		<Case String =“red”>
			<Rectangle Fill =“＃f00”Height =“50”Width =“50”/>
		</ Case>
		<Case String =“blue”>
			<Rectangle Fill =“＃00f”Height =“50”Width =“50”/>
		</ Case>
	</ Match>

`Case`的有效匹配属性是：

- `String`  - 匹配字符串
- `Number`  - 匹配一个数字
- `Bool`  - 匹配一个布尔值
- `IsDefault`  - 默认情况下，如果没有其他情况匹配

*注意：Match.Value可以是数据绑定到任何JavaScript类型，但如果使用属性绑定，必须使用专门的属性`String`，`Number`，`Integer`或`Bool`。这是因为属性绑定要求类型是相同的。

## $（DataToResource）

您可以使用@（DataToResource）绑定到定义的资源：

	<FileImageSource ux：Key =“picture”File =“Pictures / Picture1.jpg”/>
	<JavaScript>
		module.exports = {
			图片：“图片”
		}}
	</ JavaScript>
	<Image Source =“{DataToResource picture}”/>

> ##确保订单

请注意，`Each`不一定将子节点插入`Each`节点所在的树中的确切位置。为了确保子订单，将`Each`包装在某种@（Panel）中：

	<StackPanel>
		<Text>我先走！</ Text>
		<StackPanel>
			<Each Items =“{strings}”>
				<Text Value =“{}”/>
			</ Each>
		</ StackPanel>
		<Text>我去最后！</ Text>
	</ StackPanel>

我们正在考虑增加更多的技巧，在这种情况下允许更多的灵活性，但现在这是推荐的方法。
