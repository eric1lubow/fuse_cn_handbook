# Hello，$(UX Markup)！

在Fuse中工作的主要方式是通过编辑UX Markup  - 一种基于XML的文件格式，这是为应用程序开发量身定制的。布局，交互和动画是以清晰的声明语法编写的。在UX Markup中工作的主要好处是它可以开辟更深更快更快的开发，并为设计师和开发人员提供一种更好的方式，一起工作，更轻松地共享代码和设计。

UX Markup用于在Fuse中构建组件，UI视图，设计，原型和完整应用程序，并编译为C ++本地代码。UX Markup是用JavaScript编写的业务逻辑的补充，它在自己的线程上运行，让您的UI和动画更加流畅。

要深入了解UX Markup的功能，请参阅[UX标记指南](https://www.fusetools.com/learn/guides/uxmarkup)。

## $(创建应用):$(应用)

当在Fuse中创建独立的应用程序或原型时，我们使用`<App>`标签。

	<App Background =“#436EEE”>
		<Text> Hello，world！</ Text>
	</ App>

在你的`<App>`标签中，你可以放置任何`Node`，`Behavior`或`Theme`标签。上面的例子
只需使用默认字体显示文本。

`<App>`标签本身引导应用程序，并负责应用程序生命周期和@(主题)。

$(Background)属性控制应用程序的根视图的颜色。


## UX标签

UX文档由XML标记组成。每个可用的UX标签对应于在Uno代码中实现的*类*。每个标记对应一个(或多个)运行时对象。

可用的标签(类)分为以下类别:

* @(App)类是应用程序的根，并且可以只包含一个节点
* 许多`Node`类型，大多数是@(Element:UI Elements)
* 修改节点的行为。行为有多种风格:
  * 手势
  * 触发器
  * 脚本
  * Visual @(效果)，可以应用于元素
* $(样式:样式)，允许组件的一致的外观和感觉，而不重复数据

## $(主题:主题)

`App`提供了一个名为“Theme”的设置，它指定了标准组件的方式
该应用程序将外观和感觉。你这样设置:

	<App Theme =“.. name of theme ..”>

`Theme`可以有以下设置:

* `基础` - 建议。使用基于OpenGL的图形在每个平台上渲染具有相同外观的控件。
* `Native`  - 默认使用本机iOS和Android平台控件。请注意，这不会在本地桌面预览中工作。
* `NativeWithFallback`  - 在iOS和Android设备上使用`Native`主题，而在本地桌面预览中使用`Basic`。
* *默认*  - 如果没有指定，Fuse使用基于OpenGL的图形，但是对于标准控件(例如`Button`和`Slider')提供*没有默认外观*。

### $(NativeTheme:Native Theme)

当使用`Theme =“Native”`时，Fuse将使用目标平台的本地控件。要使用本机主题，请执行:

	<App Theme =“Native”>
		<StackPanel>
			<Button />
			<开关/>
			<Slider />
		</ StackPanel>
	</ App>

在此示例中，显示的控件在iOS和Android上具有本机外观，在桌面预览中没有外观*。

>如果您仍然希望在使用本机控件时使用桌面预览，则可以使用“NativeWithFallback”主题。这将给你的iOS和Android的原生控件，而回到一个(未指定)基本主题在桌面上。

### $(BasicTheme:Basic Theme)

“Basic”主题通过OpenGL呈现控件，为控件提供由材质设计启发的外观和感觉。

用法示例:

	<App Theme =“Basic”>

使用`Theme =“Basic”`这将给你的应用程序在所有平台上相同的外观，除了:

* 状态栏在不同平台上的行为不同
* `TextInput`是和其他更高级别的控件将映射到平台特定的控件。

使用`Basic`主题的主要好处是:

* 您可以在Mac和PC上使用Fuse的实时预览窗口预览您的应用程序，
  它提供了比iOS和Android模拟器更平滑的体验。
* 您的设计和动画在所有平台上的外观和行为相同。

## $(ux:AutoBind)

@(ux:AutoBind)控制对象是否应自动“连接”到父节点。默认情况下'ux:AutoBind =“true”`，所以你只需要使用这个，如果你想做一些特别的事情。

例如:
```
<Panel ux:Name =“panel1”>
    <Panel ux:AutoBind =“false”ux:Name =“panel2”/>
</ Panel>
```

在这种情况下，`panel2`不会成为`panel1`的子级。相反，它将保持与对象树分离。这只有在您打算以后添加时才有意义，例如通过在“HierarchicalNavigation”中导航到它。
