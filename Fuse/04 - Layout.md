# $(Layout)

Fuse有一个强大的布局系统，可以工作在所有平台和设备，无论你
正在使用@(NativeTheme:native)元素或@(BasicTheme:graphics)基础元素构建。

> ####视频介绍布局

[YOUTUBE x6CueKzTK68]

[YOUTUBE EDjymiMxHSw]

## $(面板)

面板可以包含子UI用户界面元素，并根据布局规则进行布局。有几种类型的面板，每个面板具有不同的布局规则。

对于基本布局的一个好的介绍性的例子，看看这里[https://www.fusetools.com/developers/examples/layoutexample)。

## $(Panel)

最基本的面板类型是“面板”。面板的孩子将默认填充整个空间。如果面板包含几个孩子，它只是将它们叠加在彼此的顶部。将此行为与@(对齐:对齐)，@(Margin:margin)和@(Padding:padding)组合在许多情况下可能非常有用。

```
<面板>
	<Text> This ... </ Text>
	<Text> ...将位于此</ Text>之上
	<Rectangle Alignment =“BottomLeft”Height =“20”Width =“20”Color =“#678”/>
</ Panel>
```

请注意，`Panel`中的元素顺序与流行的图形包(如Photoshop)中的层顺序相同; 在UX文件中首先出现的图层将分层显示在文件中后面出现的元素上。

## $(StackPanel)

StackPanel将它的孩子放在堆栈中。默认布局是垂直堆栈，但是可以使用“Orientation”属性指定堆栈应该水平布局。

```
<StackPanel Orientation =“Horizo​​ntal”>
	...元素...
</ StackPanel>
```

另外，可以使用`ItemSpacing`属性在元素之间添加一些空格。
它不同于在每个孩子上设置“边距”，因为它只调整*元素之间的空间*，而不是每个元素周围的空间。
以下示例显示了一个“StackPanel”中的三个面板，使用“ItemSpacing”属性隔开:

```
<StackPanel ItemSpacing =“20”>
	<面板高度=“100”背景=“红色”/>
	<面板高度=“100”背景=“绿色”/>
	<面板高度=“100”背景=“蓝色”/>
</ StackPanel>
```

## $(Grid)

网格将它的孩子放在网格中。行和列可以通过@(Rows)和@(Columns)属性显式指定，或者通过分配@(RowCount)和@(ColumnCount)属性隐式指定。

### $(RowCount)和$(ColumnCount)

如果只需要一个具有相同大小的行和列的网格，则可以使用RowCount和ColumnCount属性简单地指定行数和列数。

```
<Grid RowCount =“4”ColumnCount =“2”/>
```

### $(Rows)和$(Columns)

使用“Rows”和“Columns”属性可以更好地控制行和列大小的计算方式。
这些属性分配到逗号分隔的值列表，这些值可以采用几种不同的形式。
这些值可以是绝对值，相对值或自动值。

具有3行大小为10,10和50点的网格的示例。

```
<Grid Rows =“10,10,50”/>
```

具有3行的网格的示例，其中前两个占据可用空间的20％，并且最后一个占据60％:

```
<Grid Rows =“1 *，1 *，3 *”/>
```

这里的行大小通过首先对所有值(1 + 1 + 3 = 5)求和来计算。
然后我们将值除以总数(1/5 = 20％，1/5 = 20％，3/5 = 60％)。

以下网格有3行，其中前两行获取其最大子级的大小，最后一行占用剩余空间:

```
<Grid Rows =“auto，auto，1 *”/>
```

### $(Grid.Row:在网格中放置元素)$(Grid.Column :)

默认情况下，元素按照它们在UX中显示的顺序放置在网格中，从左到右，从上到下。
然而，我们可以使用`Row`和`Column`属性来明确指定每个元素应该放置哪些网格单元格，如下所示:

```
<Grid RowCount =“1”ColumnCount =“2”>
	<Rectangle Row =“0”Column =“1”Color =“Red”/>
	<Rectangle Row =“0”Column =“0”Color =“Blue”/>
</ Grid>
```

还可以使用$(RowSpan)和$(ColumnSpan)属性指定元素占用的行数和列数，如:

```
<Grid RowCount =“2”ColumnCount =“2”>
	<Rectangle ColumnSpan =“2”RowSpan =“2”Color =“Red”/>
</ Grid>
```

## $(WrapPanel)

“WrapPanel”一个接一个地放置它的孩子，并且每当它到达结束时包裹。可以通过分配$(FlowDirection)属性来指定元素的布局方向。FlowDirection可以是“LeftToRight”或“RightToLeft”。

以下WrapPanel从右到左水平放置其子项。

```
<WrapPanel FlowDirection =“RightToLeft”>
	<Every Count =“10”>
		<Rectangle Margin =“5”Width =“100”Height =“100”Color =“Blue”/>
	</ Each>
</ WrapPanel>
```

Orientation属性可以用来创建一个垂直的“WrapPanel”，如下所示:

```
<WrapPanel Orientation =“Vertical”>
	<Every Count =“10”>
		<Rectangle Margin =“5”Width =“100”Height =“100”Color =“Blue”/>
	</ Each>
</ WrapPanel>
```

你也可以通过使用ItemWidth和ItemHeight属性指定WrapPanel分配元素的最大面积。

<!--  TODO:Illustration  -->

## $(DockPanel)

DockPanel通过将它们连接到不同的侧面，一个接一个地放置它的孩子。可以通过使用$(Dock)属性来指定每个元素的边:

```
<DockPanel>
	<Rectangle Dock =“Left”/>
</ DockPanel>
```

Dock属性可以分配为`Left`，`Right`，`Top`，`Bottom`或`Fill`(这是默认值)。

```
<DockPanel>
	<Rectangle ux:Class =“MyRectangle”MinWidth =“100”MinHeight =“200”/>
	<MyRectangle Color =“Red”Dock =“Left”/>
	<MyRectangle Color =“Green”Dock =“Top”/>
	<MyRectangle Color =“Blue”Dock =“Right”/>
	<MyRectangle Color =“Yellow”Dock =“Bottom”/>
	<MyRectangle Color =“Teal”/>
</ DockPanel>
```

> ## $(Viewbox)

要使内容拉伸到适合某个区域，可以使用`Viewbox`:

```
<Viewbox>
	<Rectangle Color =“#808”Width =“200”Height =“100”/>
</ Viewbox>
```

这将保持其纵横比为2:1，同时将“Rectangle”拉伸为“Viewbox”的大小。

您可以通过设置“StretchDirection”属性来设置要对内容进行缩放的方向:

- `Both`  - 允许上下调整
- `UpOnly`  - 只有高档内容
- `DownOnly`  - 只有缩小内容

注意，除了`DownOnly`之外的任何其他设置可能创建像素工件，因为`Viewbox`执行其内容的位图扩展。

您还可以为内容设置@(StretchMode)，默认为“Uniform”。

## $(布局规则)

在前面的章节中，我们一直在讨论不同类型的@(面板:面板)以及如何在他们的孩子上执行布局。
在引擎盖下，这些是常规@(面板:面板)，已分配了一个“布局”。

例如，@(StackPanel)本质上是一个@(Panel)，它的Layout被设置为$(StackLayout)的一个实例。这意味着以下片段相当于每个片段
```
<StackPanel>
	... ...
</ StackPanel>
```

```
<面板>
	<StackLayout />
	... ...
</ Panel>
```

如上一个代码段所示，布局规则自动绑定到“Layout”属性，因此您不必按名称引用它们。

注意*不是所有的布局规则都有相应的@(Panel)类型*。这些记录如下。

> ### $(ColumnLayout)

`ColumnLayout`通过将@(Panel)的子项分成列来显示它们。列数可以使用`ColumnCount`属性进行调整。
它试图通过在放置他们以前的兄弟姐妹之后将孩子放在最短的列中来保持柱的高度有些平衡。

```
<面板>
	<Panel ux:Class =“MyPanel”Background =“#2c3e50”Margin =“5”/>
	<Text ux:Class =“MyText”TextColor =“#fff”Alignment =“Center”FontSize =“20”/>

	<ColumnLayout ColumnCount =“3”/>

	<MyPanel Height =“200”>
		<MyText> 1 </ MyText>
	</ MyPanel>

	<MyPanel Height =“100”>
		<MyText> 2 </ MyText>
	</ MyPanel>

	<MyPanel Height =“300”>
		<MyText> 3 </ MyText>
	</ MyPanel>

	<MyPanel Height =“150”>
		<MyText> 4 </ MyText>
	</ MyPanel>

	<MyPanel Height =“200”>
		<MyText> 5 </ MyText>
	</ MyPanel>
</ Panel>
```

在这个例子中，我们将不同高度的五个@(面板:面板)分成三列。
下面的动画显示了这个的结果，以及如果我们调整第三个@(面板:面板)的大小会发生什么。

[VIDEO ../media/ColumnLayout.mp4]

您还可以通过将“Orientation”属性设置为“Horizo​​ntal”，以行而不是列来布置子项。

使用`ColumnLayout`的完整示例可以在这里找到[https://www.fusetools.com/examples/gallery]。

> ### $(CircleLayout)

`CircleLayout`以圆形模式布置@(Panel)的子项。它可以调整以下属性:

-  $(CircleLayout.StartAngleDegrees:StartAngleDegrees) - 第一个孩子应该放置的角度。
-  $(CircleLayout.EndAngleDegrees:EndAngleDegrees) - 应该放置最后一个孩子的角度。
-  $(CircleLayout.Radius:Radius) - 项目从@(面板)的中心放置的距离。范围为0到1，其中0在中心，1在@(面板)的边缘。
-  $(CircleLayout.ItemSpacingDegrees:ItemSpacingDegrees) - 每个孩子的位置之间的度数。

以下示例将26个红色圆圈置于半圆形式中:

```
<Panel Margin =“20”Color =“#ddd”>
    <CircleLayout StartAngleDegrees =“ -  180”EndAngleDegrees =“0”/>

	<Every Count =“26”>
		<Circle Width =“25”Color =“#f00”Height =“25”/>
	</ Each>
</ Panel>
```

## 元素布局

<!--  TODO:链接到视频 -->

每当一个@(Panel)对它的孩子执行布局时，它必须询问每个元素它的布局属性。

为每个元素分配布局属性以控制元素@(Width)，@(Height)，@(Margin)和@(Padding)等元素。
如果一个元素没有指定这些东西，执行布局的面板将处理它。

<!--  TODO:可用空间，@(点)(vs @(像素))。 -->

### $(对齐)

当元件位于面板中时，它们可能不需要所有可用的空间。例如，垂直堆栈面板将与其最大元素一样宽，为较小元素留出额外的空间。元素可以在此空间中对齐，或者拉伸以填充它。

我们可以通过@(Alignment)属性将元素对齐到容器的两侧。
在下面的示例中，我们有一个尺寸为500x500的@(Panel)，它包含一个尺寸为50x50的@(Rectangle)。通过将对齐设置为BottomRight，此矩形将由面板的右下角定位。

```
<面板>
	<Rectangle Width =“50”Height =“50”Alignment =“BottomRight”/>
</ Panel>
```

对齐可以分配到以下值中的任何一个:
-  $(左)
-  $(Horizo​​ntalCenter)
-  $(右)
-  $(顶部)
-  $(VerticalCenter)
-  $(底部)
-  $(TopLeft)
-  $(TopCenter)
-  $(TopRight)
-  $(CenterLeft)
-  $(中心)
-  $(CenterRight)
-  $(BottomLeft)
-  $(BottomCenter)
-  $(BottomRight)

如果你没有明确指定一个`Alignment`，默认的对齐方式就是将控件伸展到父控件请求的位置。对于正常的“Panel”，这意味着子控件将尝试填充父`Panel`，但是其他包含控件的可能会要求子进程的行为不同。

### $(Visibility)

`Visibility`属性可以用于`Show`，`Hide`或`Collapse`元素。它可以设置为以下值之一:
-  $(Visible) - 元素可见的默认状态。
-  $(隐藏) - 元素不绘制，但它仍然占用空间。
-  $(Collapsed) - 元素不可见，不占用任何空间。

在下面的示例中，只有两个@(矩形:矩形)可见。第二个@(Rectangle)已折叠，因此它不占用任何空间。第三个@(Rectangle)是隐藏的，所以它占用空间，但不可见。最后的@(Rectangle)没有设置“Visibility”，所以默认为可见。

```
<StackPanel>
	<Rectangle Visibility =“Visible”Color =“Red”Height =“50”/>
	<Rectangle Visibility =“Collapsed”Color =“Green”Height =“50”/>
	<Rectangle Visibility =“Hidden”Color =“Blue”Height =“50”/>
	<矩形颜色=“黄色”高度=“50”/>
</ StackPanel>
```

### $(Width)和$(Height)

您可以使用`Width`和`Height`组合@(对齐)和其他布局属性，这将在所需的@(单位:单位)中设置@(元素)的大小。

### $(Margin)和$(Padding)

每个元素可以通过使用其@(Margin)属性来指定它与父级或周围兄弟元素之间的空间量。

每个元素还可以通过使用@(Padding)属性指定其边框和其中的任何元素之间应该有多少空间。

使用以逗号分隔的4个值的列表来指定@(Margin)和@(Padding)，其以该顺序定义左，上，右和底值。
这里是一个@(矩形)的例子，边距为左边20，顶部30，右边50，底部10:

```
<Rectangle Margin =“20,30,50,10”/>
```

这里是一个矩形，其中所有边有相同的边距:

```
<Rectangle Margin =“50”/>
```

该矩形的左右边界为50，上下边界为20:

```
<Rectangle Margin =“50,20”/>
```

### $(Units)

有多种方法可以指定布局属性，点，百分比和像素值。
以下属性支持单位
-  @(宽度)
-  @(高度)
-  MinWidth
-  MinHeight
-  MaxWidth
-  MaxHeight
-  @(偏移)
-  @(Anchor)

* 请注意，@(Grid)的列和行属性使用自己的@(Rows:system)。

### 指定单位在$(点)

当将元素Width设置为像`<Element Width =“50”/>`这样的数字时，元素将变为50点宽。

点与像素不同，因为它们将表示高密度显示器上的多个像素。这样，使用点将给你在所有不同的屏幕密度一致的外观。

### 指定单位在$(百分比:百分比)(％)

使用百分号指定值表示此值应为可用空间的特定百分比。
例如，如果我们在宽度为500点的@(面板)中放置一个@(Element)，并将元素Width属性设置为50％，我们得到一个宽度为250点的元素。

### 指定单位为$(像素:像素)

我们可以使用px后缀来指定像素值，如:

```
<Rectangle Width =“200px”/>
```

此矩形将正好是200像素宽，这意味着当屏幕密度较高时，它将更小。

### $(Anchor)和$(Offset)

当@(Panel)放置它的孩子时，它假定该元素的“中心”在中间。但是，如果我们想要放置元素，好像它的“center”沿着它的左侧，我们可以使用@(Anchor)属性，如:

```
<Rectangle Anchor =“0％，50％”/>
```

这将元素锚定在其左边缘的中间。


### $(TransformOrigin)

“TransformOrigin”属性指定所使用的转换的起源
转换行为和动画，如@(移动)，@(缩放)，@(旋转)，@(缩放)等。

它接受以下值:

-  $(TransformOrigin.Center:Center)_(默认)_  - 转换源于元素的中心
-  $(TransformOrigin.TopLeft:TopLeft) - 转换源于元素的左上角
-  $(TransformOrigin.Anchor:Anchor) - 转换源于由@(Anchor)属性指定的点周围。
-  $(TransformOrigin.Horizo​​ntalBoxCenter:Horizo​​ntalBoxCenter) - 模拟在3D空间中作为立方体的前面的元素的效果，使用元素的宽度来确定立方体的深度。没有元素在@(视口)，这将没有深度的错觉，有效地使它无用。
-  $(TransformOrigin.VerticalBoxCenter:VerticalBoxCenter) - 与@(TransformOrigin.Horizo​​ntalBoxCenter)相同，除了它使用元素的高度来确定深度。

> ####使用Anchor设置任意变换原点

```
<Rectangle X =“50％”Y =“50％”Anchor =“0％，50％”TransformOrigin =“Anchor”/>
```

上面的例子创建了一个矩形，它的左边缘中心放在父母的中间。当旋转时，它将围绕该点旋转。


### $(LayoutMaster)

你可以使一个元素继承另一个布局使用`LayoutMaster`属性。

```
<StackPanel>
	<Rectangle ux:Name =“master”Height =“150”Color =“#f00a”/>
	<Rectangle LayoutMaster =“master”Color =“#00fa”/>
</ StackPanel>
```

上面的例子将导致两个重叠的@(Rectangle:Rectangles)。

当一个元素的LayoutMaster更改时，该元素上的任何@(LayoutAnimation:LayoutAnimations)将被激活。

```
<Rectangle ux:Name =“selection”LayoutMaster =“target1”>
	<笔画宽度=“2”Brush =“#3498db”Offset =“2”/>
	<LayoutAnimation>
		<Move RelativeTo =“WorldPositionChange”X =“1”Y =“1”Duration =“0.3”Easing =“CubicInOut”/>
		<Resize RelativeTo =“SizeChange”X =“1”Y =“1”Duration =“0.3”Easing =“CubicInOut”/>
	</ LayoutAnimation>
</ Rectangle>

<StackPanel>
	<Panel ux:Name =“target1”Margin =“10”Height =“50”Background =“#eee”>
		<文本对齐=“中心”>单击我</文本>
		<Clicked>
			<Set selection.LayoutMaster =“target1”/>
		</ Clicked>
	</ Panel>
	<Panel ux:Name =“target2”Width =“150”Height =“100”Background =“#eee”>
		<Text Alignment =“Center”>我也！</ Text>
		<Clicked>
			<Set selection.LayoutMaster =“target2”/>
		</ Clicked>
	</ Panel>
</ StackPanel>
```

上面的例子说明了如何使用`LayoutMaster`来实现一个移动选择矩形。它由两个面板组成，当单击时，动画“选择”@(矩形)以继承它们的大小和位置。


### $(绝对定位)$(X :) $(Y :)

如果我们想给我们的元素一个明确的位置，我们可以分配他们的X和Y属性。`X`属性将相对于容器的左侧移动元素，而`Y`属性相对于顶部移动元素。

请注意，绝对定位元素通常应避免使用布局规则。这是因为当使用实际数据时，所使用的绝对值可能不再有意义。

> ## $(Viewport)

`Viewport`元素允许您使用透视投影执行3D变换。
在当前状态下，`Viewport`有点有限。
它必须直接放在你的`<App>`中，而你的应用程序的其余部分放在一个child @(Panel)中，并且不能处理节点本地的透视图。

“透视”属性控制摄像机距离“Z = 0”平面(默认情况下绘制所有内容)的距离，以点为单位。

	<App Theme =“Basic”>
		<Viewport Perspective =“400”>
			<面板>
				<Rectangle Width =“200”Height =“200”Background =“#2ecc71”>
					<Clicked>
						<Rotate DegreesX =“360”Duration =“1.5”Easing =“QuadraticInOut”DurationBack =“0”/>
					</ Clicked>
				</ Rectangle>
			</ Panel>
		</ Viewport>
	</ App>

## 状态栏

iOS和Android设备通常具有与屏幕顶部对齐的状态栏，其显示来自操作系统的状态信息，如电池和网络信息。在我们的应用运行时，此状态栏可能会或可能不会显示。我们也可能或可能不能吸引。Android还经常有一个屏幕上的栏来处理导航，大多数设备在接受用户输入时显示屏幕键盘。

> ###视频介绍操作系统元素

[YOUTUBE S_syTU44jzw]

### $(StatusBarBackground)

@(StatusBarBackground)元素用于补偿状态栏。它将始终具有与所有平台和设备上的状态栏相同的大小。我们可以使用@(DockPanel)将此元素停靠在我们应用的顶部，并“偏移”我们的内容的其余部分，使其适合我们屏幕的可见部分。

```
<App>
	<DockPanel>
		<StatusBarBackground Dock =“Top”/>
		<面板>
			...我们的应用内容...
		</ Panel>
	</ DockPanel>
</ App>
```

### $(BottomBarBackground)

BottomBarBackground元素与@(StatusBarBackground)非常相似，因为它与某些特定于OS的元素具有相同的大小。BottomBarBackground将采用与键盘相同的大小(无论何时它是可见的)。某些Android设备的主屏幕按钮不是物理按钮。BottomBarBackground也会在调整大小时考虑这一点。

以下是我们如何确保我们的内容从来没有被键盘或主页按钮覆盖:

```
<App>
	<DockPanel>
		<面板>
			...我们的应用内容...
		</ Panel>
		<BottomBarBackground Dock =“Bottom”/>
	</ DockPanel>
</ App>
```

您还可以选择不考虑键盘尺寸:

	<BottomBarBackground IncludesKeyboard =“false”/>

> ### $(ClientPanel)

`ClientPanel`是一个@(DockPanel)，它的顶部和底部边缘分别对接一个@(StatusBarBackground)和一个@(BottomBarBackground)。为方便起见，包括在内。
