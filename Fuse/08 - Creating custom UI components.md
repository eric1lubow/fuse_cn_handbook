＃$（创建自定义UI组件）

## ux：类

在UX标记节点上指定`ux：Class`属性定义一个新类。类是一个可重用的组件，可以在项目的其他位置实例化。

`ux：Class`创建一个可以在任何地方使用的自包含组件。它没有访问`ux：Name`s本身。要创建一个在某个上下文中具有访问`ux：Name`s的类，请参见`ux：InnerClass`。

###示例

下面的代码创建一个叫“Header”的类，继承自Text。

```
<Text ux：Class =“Header”FontSize =“22”/>
```

它可以被实例化，就像任何其他Fuse元素。

```
<Header>欢迎</ Header>
```

在这种情况下，`Header`类将是与`Text`不同的类，但`Text`的所有属性也都可用。注意，类可以在单独的文件中定义，并且经常是。

自定义类可以是你想要的那样复杂：

```
<Image ux：Class =“BurgerIcon”>
	<MultiDensityImageSource>
		<FileImageSource File =“Burger.png”Density =“1”/>
		<FileImageSource File =“Burger.png@2x.png”Density =“2”/>
	</ MultiDenistyImageSoruce>
</ Image>

<！ -  ...  - >

<面板>
	<BurgerIcon />
</ Panel>
```

它们还可以包含使用@（触发器：触发器），@（动作：动作），JavaScript等的自己的“逻辑”。

注意，使用ux：Class创建的类映射到真正的Uno-classes：

```
<Panel ux：Class =“MyPanel”/>
```

将等于以下Uno类：

```
public class MyPanel：Panel {...}
```

## ux：属性

在大多数情况下，在创建自定义类时，我们需要能够为该类定义一些接口。这可以通过UX使用ux：Property语法完成。

__MyButton.ux__：

```
<Panel ux：Class =“MyButton”Text =“点击我！
	   Fill =“＃f00”TextColor =“＃000”CornerRadius =“10”>
	<string ux：Property =“Text”/>
	<float4 ux：Property =“CornerRadius”/>
	<Brush ux：Property =“Fill”/>
	<float4 ux：Property =“TextColor”/>
	<Text Alignment =“Center”TextColor =“{Property this.TextColor}”Value =“{Property this.Text}”/>
	<Rectangle Layer =“Background”CornerRadius =“{Property this.CornerRadius}”Fill =“{Property this.Fill}”/>
</ Panel>
```

这里我们创建了一个名为MyButton的新类。我们公开四个属性（Text，CornerRadius，Fill和TextColor）。这些属性然后可以使用`{Property this.PropName}`语法绑定到我们的ux：类中。

*注意，存在一个名为`this`的隐式名称，我们在`{Property this.PropName}`语法中使用。这个名称用于引用我们的`ux：Class`的根元素。

当我们实例化我们的类时，我们可以直接访问这些属性：

__MainView.ux__：

```
<App>
	<MyButton CornerRadius =“20”Text =“MyText”
	          TextColor =“＃fff”Width =“200”Height =“50”>
		<LinearGradient ux：Binding =“Fill”>
			<GradientStop Color =“＃0f0”Offset =“0”/>
			<GradientStop Color =“＃00f”Offset =“1”/>
	    </ LinearGradient>
	</ MyButton>
</ App>
```

你可以使用`ux：Binding`来设置引用类型的属性（例如`Brush`）。在上面的例子中，我们创建一个`LinearGradient`，并将其用作`Fill`。

> ### Observable / Array属性

如果你想传递一个Observable或者Array作为属性，使用`object`类型，如下图所示。

```
<Panel ux：Class =“ListView”>
	<object ux：Property =“ListItems”/>
	<StackPanel>
		<Each Items =“{Property this.ListItems}”>
			<Panel Padding =“10”>
				<Text Value =“{}”/>
			</ Panel>
		</ Each>
	</ StackPanel>
</ Panel>

<JavaScript>
	exports.items = [“Foo”，“Bar”，“Baz”];
</ JavaScript>

<ListView ListItems =“{items}”/>
```

## ux：InnerClass

内部类是属于某个范围的类，并且可以访问在该范围中声明的ux：Name。内部类只能在声明的范围中使用。

###示例

	<App Theme =“Basic”>
		<Button ux：InnerClass =“ActivateButton”ux：Name =“btn”Margin =“10”>
			<Clicked>
				<！ - 此类可以访问currentActive，因为它是App  - >的内部类
				<Set highlight.LayoutMaster =“btn”/>
			</ Clicked>
		</ Button>

		<StackPanel>
			<ActivateButton Text =“Option A”ux：Name =“defaultOption”/>
			<ActivateButton Text =“Option B”/>
			<ActivateButton Text =“Option C”/>
			<Rectangle Fill =“Red”ux：Name =“highlight”Margin =“ -  5”LayoutMaster =“defaultOption”>
				<LayoutAnimation>
					<Move RelativeTo =“PositionChange”X =“1”Y =“1”Duration =“0.4”Easing =“BackOut”/>
				</ LayoutAnimation>
			</ Rectangle>
		</ StackPanel>
	</ App>


## ux：包括

可以使用`ux：Include`将UX文件的内容插入到另一个文件中。

上面的例子，`ux：InnerClass可以拆分成一个单独的文件


	<App Theme =“Basic”>
		<ux：Include File =“ActivateButton.ux”/>

		<StackPanel>
			<ActivateButton Text =“Option A”ux：Name =“defaultOption”/>
			... ...
		</ StackPanel>
	</ App>

在`ActivateButton.ux`中：

	<Button ux：InnerClass =“ActivateButton”ux：Name =“btn”Margin =“10”>
		<Clicked>
			<！ - 此类可以访问currentActive，因为它是App  - >的内部类
			<Set highlight.LayoutMaster =“btn”/>
		</ Clicked>
	</ Button>

注意，包含的文件在根节点上不应该有ux：Class，这将在项目中创建两个类的实例。但是，包含的文件可以指定`ux：InnerClass`。这将在包含的每个位置创建内部类的本地版本。
