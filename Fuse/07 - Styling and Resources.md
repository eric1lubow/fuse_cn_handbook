# $(样式)和资源

在Fuse中，样式是指在整个应用程序中以一致的方式设置属性，如字体，颜色，边距和外观。

## 从'$(Style)'迁移

`Style`已经被弃用了！现在通过使用`ux:Class`来设置多个元素的属性的正确方法。
无论你有什么样的属性和子元素应用于某个类型的每个元素，你现在必须显式地子类化和使用它。

这意味着以下:

```
<StackPanel>
	<Style>
		<Rectangle Background =“#f00”Width =“100”Height =“100”>
			<Clicked>
				<比例因子=“1.2”持续时间=“0.2”/>
			</ Clicked>
		</ Rectangle>
	</ Style>

	<Rectangle />
	<Rectangle />
</ StackPanel>
```

应重写为:

```
<StackPanel>
	<Rectangle ux:Class =“FancyRectangle”Background =“#f00”Width =“100”Height =“100”>
		<Clicked>
			<比例因子=“1.2”持续时间=“0.2”/>
		</ Clicked>
	</ Rectangle>

	<FancyRectangle />
	<FancyRectangle />
</ StackPanel>
```

关于`ux:Class`的进一步参考可以在@(创建自定义UI组件)下找到。

## $(Theme)

`App`的`Theme`属性决定你的应用程序是否设置有*本地UI控件*或* OpenGL ES强大的图形*作为根级别表示技术，以及@(控制)的默认外观和感觉。

有关详细信息，请参阅@(主题)。

## $(全球资源)

全局资源是一次创建并在整个应用程序中使用的共享对象。对于在应用程序生命周期中从不更改的对象(例如字体，颜色和声音)，建议使用全局资源。

使用“ux:Global”属性在UX标记中创建全局资源。`ux:Global`创建一个全局别名。这意味着你可以通过它的“ResourceAlias”来引用它。

资源别名是资源类型的本地资源。例如，如果你有一个名为`Coral`的`float4`颜色资源和名为`Coral`的`SolidColor`资源，Fuse将从上下文中理解它是哪个。

这是如何导入TTF文件并从中创建全局资源:

	<Font File =“HelveticaNeue-MediumCondensed.ttf”ux:Global =“Helvetica”/>

上面这行可以放在项目的任何文件中，但是你通常把它们放在你的Style UX文件或专用于资源的特殊UX文件中。

一旦你创建了一个`ux:Global`别名，你就可以通过代码中任何地方的别名来引用你的字体:

	<Text Font =“Helvetica”>您好，是的，这是文本。</ Text>

## $(Resource)s和$(ux:Key)

要声明动态资源，只需:

```
	<string ux:Key =“WelcomeMessage”ux:Value =“Hello！”>
```

除了对象外，当前支持以下类型:

* string  - `“string”`
* bool  - `true`或`false`
* float  - `1`，`0.2`，`.2`
* float2  - `1，1`
* float3  - `0，1，0.2`
* float4  - `1，2，3.2，1`

然后，资源将在其定义的树中的下面的任何节点可用。它可以用作:

	<Text Value =“{Resource WelcomeMessage}”/>

### $(本地化)

字符串，值，图标和其他资源的本地化可以使用* resources *轻松实现。

    <StateGroup Active =“{language}”>
		<State Name =“norwegian”>
			<ResourceString Key =“hello”Value =“hei”/>
		</ State>
		<State Name =“english”>
			<ResourceString Key =“hello”Value =“hello”/>
		</ State>
		<State Name =“spanish”>
			<ResourceString Key =“hello”Value =“hola”/>
		</ State>
	</ StateGroup>

然后，不是直接在整个应用程序中指定问候字符串，而是可以使用资源绑定:

	<Text Value =“{Resource hello}”/>

* 请注意，我们这里使用`ResourceString`而不是`string`。`ResourceString`实际上是一个行为，它将其值作为资源时应用root，这就是为什么我们可以使用它与一个StateGroup。
