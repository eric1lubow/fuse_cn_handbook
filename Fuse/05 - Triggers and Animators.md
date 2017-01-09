# $(Trigger:Triggers)和$(Animators)

触发器提供了使用Fuse创建动画的声明式方法。最基本的，触发器表示响应于用户和/或程序输入而触发的事件。@(Trigger:Triggers)可以包含@(Animators)和@(Actions)，用于动画和操作元素以及与@(JavaScript)交互。

@(Trigger)是生活在Node或UI @(Element)上的行为，监听事件并执行动画和@(动作)作为响应。

例如，这里是一个@(面板)与@(WhilePressed)触发器，使面板旋转90度与弹跳动画。

```
<面板>
	<WhilePressed>
		<旋转角度=“90”Duration =“0.5”Easing =“BounceInOut”/>
	</ WhilePressed>
</ Panel>
```

> ##视频介绍触发器和动画

[YOUTUBE bT1npBvXEzw]

<!--  TODO:
* 解释触发器是时间轴，向前/向后播放，应用/未应用 -->

## $(静止状态)和偏差

UX标记元素的默认布局和配置称为休眠状态。
触发器定义与该静止状态的偏差。
每个触发器知道如何“取消应用”其自己的动画返回到休息状态，即使中间动画中断。
这是伟大的，因为它意味着动画完全从你的程序的逻辑状态分离，大大降低了处理组合动画的复杂性
多个设备，屏幕尺寸，真实数据和真实用户输入。

### $(BackwardAnimation:单独的向前和向后动画)

一些高级@(触发器)动画允许单独的向前和向后动画。
要做到这一点，将后向动画放在一个`TriggerAnimation`里，绑定它做父动(向前)动画上的`BackwardAnimation`绑定。下面的例子使用`BackwardAnimation`在动画回到空闲状态时用不同的缓动动画。

```
<WhilePressed>
	<旋转角度=“90”持续时间=“0.5”/>
	<TriggerAnimation ux:Binding =“BackwardAnimation”>
		<旋转角度=“90”Duration =“1”Easing =“BounceOut”/>
	</ TriggerAnimation>
</ WhilePressed>
```

** 注意:**“BackwardAnimation”中的动画自动右对齐。

## 动画师

动画师用于指定在触发@(触发器:触发器)时要触发@(Element:元素)的动画。
有三对属性对于控制动画的确切结果很重要。

### $(Duration)/ $(DurationBack)

当动画向前和向后动画时，动画可以具有不同的行为。当触发器被激活时，动画被称为向前播放。当触发器被禁用时，动画向后播放。持续时间用于设置动画的持续时间。可以使用`DurationBack`属性为后向动画设置不同的持续时间。

当触发器中有多个@(Animators:animator)时，触发器的总持续时间将是动画器中最长的持续时间。

在以下示例中，@(WhileTrue)触发器的总持续时间将为3秒。如果我们想让动画一个接一个地发生，我们可以使用@(延迟)。

```
<WhileTrue>
	<Move X =“10”Duration =“2”/>
	<旋转角度=“90”持续时间=“3”/>
</ WhileTrue>
```

### $(Delay)/ $(DelayBack)

设置“Delay”属性会导致实际动画被延迟该秒数。`DelayBack`用于对后向动画设置不同的延迟。动画的总持续时间变为延迟+持续时间。以下@(更改:更改)动画师的总持续时间为7秒。它在激活后等待5秒钟，然后在2秒内对其目标元素进行动画处理。

```
<Change Delay =“5”Duration =“2”someElement.Height =“100”/>
```

### $(Easing)/ $(EasingBack)

保险丝带有一组标准的预定义缓动曲线。缓动曲线用于控制动画随时间推移的过程。默认缓动设置为“线性”。使用线性宽松，动画在其整个持续时间上以相同的速度进行。这通常显得很不自然和假。为了获得更自然的感觉，我们可以将缓动改变为“QuadraticInOut”，如下:

```
<Change Easing =“QuadraticInOut”Duration =“2”someElement.Property =“SomeValue”/>
```

这个动画师将在开始时缓慢地进行，在中间更快地进行，然后在最后再次慢慢地进行。

定义以下缓动曲线:

-  $(线性)
-  $(二次)
-  $(Cubic)
-  $(Quartic)
-  $(Quintic)
-  $(正弦)
-  $(指数)
-  $(循环)
-  $(弹性)
-  $(返回)
-  $(Bounce)

它们各有三个不同的版本:[缓动] In，[缓和] Out和[缓和] InOut。
为动画师分配缓动，像这样:

```
<Move Easing =“CubicIn”X =“5”Y =“10”Duration =“0.5”/>
<Scale Easing =“BounceOut”Factor =“1.5”Duration =“0.2”/>
<旋转松动=“BounceOut”度=“10”持续时间=“0.4”/>
```

<!--   -  MixOp
TODO / AUTH:写关于mixop  -->

### $(更改)

`Change`在包含触发器的时候临时改变属性的值。要永久更改值，请使用@(设置)动画。

Target属性指的是我们想要动画的属性。
`Value`属性是动画结果的值。

* 注意:只要单元与目标的原始单元匹配，您就可以用`Value`指定@(Units)。

因为设置目标属性和值的任务如此常见，所以UX具有特殊的语法。代替

```
<Change Target =“target.Property”Value =“Value”/>
```

可以执行以下操作:

```
<Change target.Property =“Value”/>
```

`Change`动画可以用来动画几乎任何属性。

```
<Panel ux:Name =“somePanel”Color =“Red”/>

<更改somePanel.Opacity =“0”/>
<Change somePanel.Color =“Blue”Duration =“0.3”/>
<更改somePanel.Visibility =“折叠”/>
```

还可以激活“宽度”，“高度”和“边距”等属性，但是因为这些属性可能会影响您的UI的整个布局，这可能最终在性能方面相当昂贵。通常有更有效的方法来做与布局交互的动画。查看@(LayoutAnimation)和@(MultiLayoutPanel)的一些灵感。

> ### $(Cycle)

“Cycle”连续地在给定频率上对两个值之间的属性进行动画处理。

	<面板>
		<Translation ux:Name =“someTranslation”/>
		<WhilePressed>
			<Cycle Target =“someTranslation.X”Low =“ -  20”High =“20”Frequency =“2”/>
		</ WhilePressed>
	</ Panel>

您还可以指定一个“Duration”来控制动画的长度。

### Transform animators

变换动画师对元素应用变换。它们不影响布局，保证快速动画。

所有变换动画都接受*可选*`Target`属性，指示应该应用变换的元素。

#### $(Move)

`Move` animator用于移动元素。`Move`不会影响布局，因此元素只会从其实际位置获得偏移量。

```
<Move X =“50”/>
```

当触发时，将沿X方向将元素移动50点。

您可能希望元素相对于其自身大小或某些其他元素大小移动。
要实现这一点，我们可以使用@(RelativeTo)属性，例如:

```
<Move X =“0.5”RelativeTo =“Size”/>
```

当触发时，将沿X方向将元素移动其自身宽度的一半。

$(RelativeTo)可以设置为以下值:

- `Local`(默认):在X和/或Y方向上移动设置的点数。
- `Size`:移动设置的量乘以元素的大小。因此，X =“1”将元素在X方向上移动其整个宽度。
- `ParentSize`:与“Size”相同，但是使用元素parent size。
- `PositionChange`:用于响应@(LayoutAnimation)将元素移动其父级内位置的变化量。
- `WorldPositionChange`:用于响应@(LayoutAnimation)，将元素移动相对于整个显示的位置变化量。
- `Keyboard`:相对于键盘的大小移动元素。

`RelativeNode`属性允许你相对于另一个元素移动一个元素。在这种情况下，您可以使用以下`RelativeTo`模式:

- `Size`:工作方式与没有`RelativeNode`时相同，但是测量`RelativeNode`的大小。
- `ParentSize`:与“Size”相同，但是测量“RelativeNode”的父大小。
- `PositionOffset`:将元素移动到与`RelativeNode`指定的元素相同的位置。
  偏移量被测量为两个元素之间的“ActualPosition”中的差异。
- `TransformOriginOffset`:像`PositionOffset`，但是测量`TransformOrigin`中的差异。

移动对应于在元素上添加@(翻译)，并使用@(更改)为其X和Y值设置动画。以下两个示例给出相同的结果。

```
<面板>
	<WhilePressed>
		<Move X =“100”Duration =“0.2”/>
	</ WhilePressed>
</ Panel>
```

```
<面板>
	<Translation ux:Name =“someTranslation”/> Transition
	<WhilePressed>
		<Change someTranslation.X =“100”Duration =“0.2”/>
	</ WhilePressed>
</ Panel>
```

#### $(Scale)

`Scale`的工作方式与@(Move)相同，只是它缩放元素。请注意，scale不会实际更改元素大小。这意味着UI布局的其余部分不会受到影响，并且动画保证是快速的。

您可以使用“Factor”属性沿所有轴均匀缩放元素。或者，您也可以使用`Vector`或`X`，`Y`和`Z`在每个轴上缩放。

```
<比例因子=“2”持续时间=“0.4”/>
```

`Scale`可以使用相对于某些东西使用`RelativeTo`属性。两个选择是:

* `SizeChange`  - 相对于由`RelativeNode`属性指定的元素大小的变化。
* `SizeFactor`  - 用相对于另一个元素的因子进行缩放，由`RelativeNode`指定。“1”的系数将使其与“RelativeNode”的大小相同，而“0.5”的系数将使其为大小的一半，依此类推。

#### $(Rotate)

`Rotate`旋转一个Element，等于添加一个@(Rotation)并用@(Change)来动画。

```
<旋转角度=“90”持续时间=“0.5”/>
```

使用`Degrees`属性可以围绕Z轴旋转元素。或者，您可以使用“DegreesX”，“DegreesY”和“DegreesZ”来围绕特定轴旋转元素。

#### $(Resize)

当与@(LayoutAnimation)一起使用时，`Resize`允许你设置一个元素的大小:

```
<Resize RelativeTo =“SizeChange”Duration =“0.5”Easing =“CircularInOut”/>
```

`Resize`有两个选项`RelativeTo`:

* `SizeChange`  - 在@(LayoutAnimation)期间相对于大小的变化调整大小。
* `Size`  - 相对于由`RelativeNode`指定的元素的大小来调整大小。

#### $(Spin)

`旋转连续旋转元素，给定一个“频率”以每秒全旋转度量。

	<面板>
		<WhilePressed>
			<Spin Frequency =“2”/>
		</ WhilePressed>
	</ Panel>

与@(Cycle)一样，您也可以指定一个“Duration”来控制动画的长度。

#### $(Skew)

`Skew'允许你对一个元素的倾斜变换进行动画处理。

```
<倾斜度X =“30”持续时间=“0.4”/>
```

您可以使用“DegreesX”和“DegreesY”在一个轴上倾斜，或者“DegreesXY”和“XY”分别以度或弧度在两个轴上倾斜。

### $(Attractor)

“Attractor”用于给动画更自然的运动。它充当动画师和其目标之间的中介。一个“Attractor”将使用一种简单的物理模拟形式，连续地将它的目标动态化为它的“Value”。我们可以通过使吸引子的Value属性动画化来将这种行为与动画结合。

```
<Panel ux:Name =“somePanel”>
	<Translation ux:Name =“someTranslation”/>
	<Attractor ux:Name =“someAttractor”Target =“someTranslation.X”/>
	<WhilePressed>
		<Change someAttractor.Value =“100”/>
	</ WhilePressed>

</ Panel>
```

### $(关键帧:关键帧)

有些情况下，我们不想简单地从点a动画到点b。对于我们想要为动画指定几个步骤的情况，我们可以使用@(关键帧:关键帧)。

```
<Move RelativeTo =“ParentSize”>
	<关键帧X =“10”时间=“0.5”/>
	<关键帧X =“15”时间=“1”/>
	<关键帧X =“5”时间=“2”/>
</ Move>
```

这个@(移动)动画将首先动画X到10超过0.5秒，然后从10到15超过0.5秒。最后，它将从15的X变为5超过1秒。
下面是使用@(关键帧:关键帧)和@(更改)动画制作者的示例:

	<Page>
		<SolidColor ux:Name =“background”Color =“#f00”/>
		<ActivatingAnimation>
			<Change Target =“background.Color”>
				<关键帧值=“#0f0”TimeDelta =“0.25”/>
				<关键帧值=“#f00”TimeDelta =“0.25”/>
				<Keyframe Value =“#ff0”TimeDelta =“0.25”/>
				<关键帧值=“#0ff”TimeDelta =“0.25”/>
			</ Change>
		</ ActivatingAnimation>
	</ Page>

这次我们使用TimeDelta代替时间。使用`TimeDelta`，我们可以将时间指定为相对项而不是绝对值。这意味着@(关键帧:关键帧)的顺序很重要，但它让我们推测关键帧在它们的持续时间，而不是它们在时间线上的绝对时间。
<!--  TODO:Interpolation  -->

### $(Nothing)

“触发器”的所有动画共享一个公共时间轴，当最后一个动画完成时结束。在一些罕见的情况下，你可能想人为地延长时间表。这可以使用`Nothing`来完成。逻辑上，它是一个具有设置长度的空白动画，强制时间线的长度至少为“Nothing”的持续时间。

```
<Nothing Duration =“1”/>
```

## 转换

所有@(元素:元素)可以应用变换，以便移动，缩放或旋转。
值得一提的是，这些变换的顺序影响了它们应用于元素的顺序，因此可能导致不同的结果。


```
<面板宽度=“100”高度=“50”>
	<Translation X =“100”/>
	<旋转角度=“45”/>
</ Panel>
```

```
<面板宽度=“100”高度=“50”>
	<旋转角度=“45”/>
	<Translation X =“100”/>
</ Panel>
```

这两个例子有非常不同的结果。在第一种情况下，首先将面板向右移动100点，然后旋转45度。在另一种情况下，首先将面板旋转45度。正X方向现在向下45度，因此我们的面板最终向右下移动。

### $(翻译)

`Translation`在指定的X，Y和Z方向移动元素。以下示例显示了一个@(矩形)，它在X方向上移动了100个点，在Y方向上移动了50个点。

```
<Rectangle Width =“50”Height =“50”>
	<Translation X =“100”Y =“50”/>
</ Rectangle>
```

您可以使用以下设置“翻译”的翻译值:

 * `X`或`Y`，分别设置X和Y平移
 * `XY`，立即设置两个轴的平移
 * `Z`，设置Z轴的平移。

坐标默认为相对于元素的原始位置(`TranslationModes.Local`)，但这可以使用属性`RelativeTo`来更改。此外，您可以使用`RelativeNode`相对于另一个元素进行转换。

### $(缩放)

“缩放”按照指定的因子放大或缩小元素。以下示例将使@(Rectangle)的大小是原始大小的两倍:

```
<Rectangle Width =“100”Height =“100”>
	<比例因子=“2”/>
</ Rectangle>
```

`缩放'可以使用:

 * `Factor`，在所有轴上设置通用刻度
 * `Vector`，立即设置所有三个轴的比例
 * `X`，`Y`和`Z`，用于控制各个轴。

<!--  TODO:Document Vector  -->

### $(Rotation)

`Rotation`旋转元素所指定的度数。这里是一个旋转90度的矩形的例子。

```
<Rectangle Width =“100”Height =“50”>
	<旋转角度=“90”/>
</ Rectangle>
```

您可以使用以下方式旋转元素:

 * `度'，控制围绕Z轴的旋转
 * `DegreesX`，`DegreesY`，`DegreesZ`，给你个人控制所有3个轴。
 * `EulerAngle`和`EulerAngleDegrees`，让你设置元素的欧拉角度，分别为弧度或度。

### $(Shear)

剪切动画可用于对元素执行剪切映射。可以使用“DegreesX”和“DegreesY”在一个轴上设置剪切，或者“度数”和“矢量”使用度数或弧度在X和Y平面中设置剪切。

## $(Actions)

触发器还可以包含动作，这些动作是在触发器时间轴中特定点触发的一次性事件。

注意，与@(动画:动画)相反的动作是不可逆的。这意味着如果触发器反转，则不一定可以返回到@(静止状态)。

像@(Animators:animators)，`Actions`可以有一个`Delay`。这指定从激活@(触发器)到激活“动作”的秒数。然而，与'Animators'不同，它们还有一个称为AtProgress的属性，它可以设置为0和1之间的值。它具有与Delay相似的功能，但相对于完整的@(Duration) @(触发器)。将`AtProgress`设置为0，意味着@(触发器)激活时，触发操作。设置为0.5意味着它被点火一半，等等。

### $(Action.AtProgress:AtProgress)

`Actions也有一个名为`AtProgress`的属性，它可以设置为0和1之间的一个值。它具有与Delay相似的功能，但相对于@(Trigger)的完整@(Duration) 。将`AtProgress`设置为0，意味着@(触发器)激活时，触发操作。设置为0.5意味着它被点火一半，等等。

### $(DebugAction)

`DebugAction`允许您在激活时向Monitor /终端打印消息。
它相当于从Uno或JavaScript调用`debug_log`。

```
<按钮>
	<Clicked>
		<DebugAction Message =“点击！/>
	</ Clicked>
</ Button>
```

### $(Set)

永久更改属性的值。如果您只想暂时更改它，请使用@(更改)。当在属性上使用`Set`时，当包含触发器被取消激活时，该值不会被还原。在下面的示例中，我们通过设置其“SolidColor”@(Element)的值来更改矩形的颜色。多次激活@(已点击)触发器将不会产生任何其他效果。

```
<Rectangle ux:Name =“rect”Color =“#00f”/>
<Clicked>
	<Set rect.Color =“#f00”/>
</ Clicked>
```

也可以使用它的“Target”和“Value”属性来调用集合。以下行是等效的:

```
<Set Target =“rect.Color”Value =“#f00”/>
<Set rect.Color =“#f00”/>
```

### $(Callback)

`Callback`操作用于在激活触发器时调用JavaScript函数(参见@(Data Binding))。

```
<JavaScript>
	var someJSFunction = function(){
		console.log(“some function called”);
	}}
	module.exports = {someJSFunction:someJSFunction};
</ JavaScript>
<Rectangle Width =“100”Height =“50”Alignment =“Center”Fill =“Red”>
	<WhilePressed>
		<Callback Handler =“{someJSFunction}”/>
	</ WhilePressed>
</ Rectangle>
```

### $(GoForward)

在其导航历史记录中指示@(导航:导航上下文)或@(WebView)前进。

	<GoForward TargetNode =“myNavigation”/>

	<GoForward TargetNode =“myWebView”/>

有关@(导航:导航)上下文中的`GoForward`的更多信息，请参阅@(控制导航)。


### $(GoBack)

告诉@(导航:导航上下文)或@(WebView)在其导航历史记录中向后退。

	<GoBack TargetNode =“myNavigation”/>

	<GoBack TargetNode =“myWebView”/>

有关@(导航:导航)上下文中的`GoBack`的更多信息，请参阅@(控制导航)。


### $(切换)

`Toggle`用于在`true`和`false`之间切换布尔值。如果在@(开关)内，它将切换@(开关)的值。`Toggle`也可以用来激活/ deactive @(WhileTrue)和@(WhileFalse)触发器，如下所示:

```
<WhileTrue ux:Name =“trueTrigger”>
	... ...
</ WhileTrue>
<面板>
	<Clicked>
		<切换目标=“trueTrigger”/>
	</ Clicked>
</ Panel>
```

### $(Pulse)

`Pulse`用于暂时触发`WhileTrue`，`WhileFalse或`Timeline`。

	<Button Text =“Pulse”>
		<WhileTrue ux:Name =“pulseMe”Value =“false”>
			<比例因子=“1.5”持续时间=“0.2”/>
		</ WhileTrue>

		<Clicked>
			<脉冲目标=“pulseMe”/>
		</ Clicked>
	</ Button>

### $(BringIntoView)

`BringIntoView`操作与@(ScrollView)控件一起使用。通过设置它的TargetNode属性，我们可以指示@(ScrollView)去一个位置，使得Node可见。

这个例子显示如何使用`BringIntoView`使@(ScrollView)通过点击按钮自动在顶部和底部之间滚动:

```
<App Theme =“Basic”ClearColor =“#eeeeeeff”>
	<DockPanel>
		<StatusBarBackground DockPanel.Dock =“Top”/>
		<ScrollView ClipToBounds =“true”>
			<StackPanel>
				<Panel ux:Name =“panel1”Height =“80”Background =“#F44336”/>
				<面板高度=“200”背景=“#ddd”/>
				<面板高度=“200”背景=“#bbb”/>
				<面板高度=“200”背景=“#999”/>
				<面板高度=“200”背景=“#777”/>
				<面板高度=“200”背景=“#444”/>
				<Panel ux:Name =“panel2”Height =“80”Background =“#3949AB”/>
			</ StackPanel>
		</ ScrollView>
		<StackPanel Dock =“Bottom”Height =“60”Orientation =“Horizo​​ntal”Alignment =“Center”>
			<Button Text =“To the top”>
				<Clicked>
					<BringIntoView TargetNode =“panel1”/>
				</ Clicked>
			</ Button>
			<Button Text =“到底部”>
				<Clicked>
					<BringIntoView TargetNode =“panel2”/>
				</ Clicked>
			</ Button>
		</ StackPanel>
	</ DockPanel>
</ App>
```

### $(BringToFront)

UX通常是结构化的，使得在文档中更高层定义的元素在顶部绘制。
这是为了模仿流行的图形包，如Photoshop的图层行为。
使用`BringToFront`，可以将`Target`属性指定的元素置于其兄弟的前面。

* 注意:`BringToFront`只使元素在其父对象中最前面，而不是整个UX树。

```
<DockPanel>
	<面板背景=“#F00”/>
	<Panel ux:Name =“bluePanel”Background =“#00F”/>
	<Button Text =“Blue to front！” Dock =“Bottom”>
		<Clicked>
			<BringToFront Target =“bluePanel”/>
		</ Clicked>
	</ Button>
</ DockPanel>
```

### $(TransitionLayout)

`TransitionLayout`操作允许您创建临时布局更改。
这可以用于进行可视布局转换，而不需要实际的布局更改。

它对自己没有明显的影响，需要与@(LayoutAnimation)结合。
@(LayoutAnimation)将会被这个动作触发。

	<DockPanel>
		<Panel Dock =“Top”Height =“20”ux:Name =“originElement”/>

		<Button Height =“100”Dock =“Bottom”Text =“Transition！”>
			<LayoutAnimation>
				<Move X =“1”Y =“1”RelativeTo =“WorldPositionChange”Duration =“1”/>
				<Resize X =“1”Y =“1”RelativeTo =“SizeChange”Duration =“1”/>
			</ LayoutAnimation>
			<Clicked>
				<TransitionLayout From =“originElement”/>
			</ Clicked>
		</ Button>
	</ DockPanel>

单击时，此示例中的@(按钮)将执行从“originElement”(@(DockPanel)的上边缘)的位置和大小到其实际位置和大小(@( DockPanel))。

### $(NavigateToggle)

切换`Navigation`。这当前仅在@(EdgeNavigation)中支持，如果在其他类型的导航上使用，则不会执行任何操作。

用于`EdgeNavigation`，它将使用`Target`属性指定的`EdgeNavigation.Edge`集合导航到一个@(Panel)。

## $(Gestures)

以下是对指针手势做出反应的触发器。

### $(点击)

“点击”被激活以响应点击@(手势:手势)。什么构成点击事件可以是平台特定的，但通常意味着指针在包含元素的边界内被按下和释放。

```
<面板宽度=“50”高度=“50”>
	<Clicked>
		<比例因子=“2”持续时间=“0.2”/>
	</ Clicked>
</ Panel>
```

### $(Tapped)

“Tapped”触发器与@(Clicked)触发器非常相似。其中，点击仅意味着指针必须在元素上被按下和释放，点击意味着指针必须在按下指针之后的特定时间内被释放。

### $(DoubleClicked)

当元素在特定时间范围内@(点击)两次时，“DoubleClicked”被激活。

### $(DoubleTapped)

与@(DoubleClicked)一样，当元素在特定时间范围内被@(敲击)两次时，“DoubleTapped”被激活。

### $(WhilePressed)

`WhilePressed`是活动的，而其包含的元素被按下，并且指针在其边界内。

```
<面板宽度=“50”高度=“50”>
	<WhilePressed>
		<比例因子=“2”持续时间=“0.2”/>
	</ WhilePressed>
</ Panel>
```

### $(WhileHovering)

`WhileHovering'是活动的，而指针在bounds内，如果它包含@(Element)。

* 注意:`WhileHovering只有当设备支持悬停指针时才有价值，就像桌面机上的鼠标指针一样。对于大多数智能手机，这个触发器没有太多价值。

### $(SwipeGesture:滑动手势)

当我们想要一个元素处理滑动手势时，我们使用`SwipeGesture`行为。

```
<面板>
	<SwipeGesture Direction =“Right”Length =“100”Type =“Active”/>
</ Panel>
```

- `Direction =“Right”`指定这应该是一个向右滑动手势。
- `Length =“100”`表示滑动在指定的“方向”中的长度为100点。
- `Type =“Active”`表示这应该是一个双态滑动手势，在非活动/活动状态之间切换。

`SwipeGesture`接受以下属性:

- `方向'指定滑动的方向。
	可能的值为`Left`，`Up`，`Right`和`Down`。
- 可以使用`Edge`代替`Direction`，并指定可以刷新的元素的边。
	可能的值为`Left`，`Top`，`Right`和`Bottom`。
- `HitSize`仅适用于使用“边缘”时，并指定从边缘离开多远，我们可以开始滑动，以便识别。
- `Length'指定了我们在指定的“方向”上滑动的距离。
- `LengthNode`可以用来代替`Length`。它引用另一个应该测量的元素来确定滑动的长度。
- `Type`
	- “活动”表示滑动应在非活动/活动状态之间切换。
	- `简单'表示滑动应该调用单一的瞬间动作。

`SwipeGesture`行为本身没有影响。我们需要应用我们自己的触发器和动画师来响应手势。
在下面的小节中，我们将通过不同的触发器和动作来回应和控制@(SwipeGesture:SwipeGestures)。

* 注意:由于您可以在同一元素上附加多个滑动手势，相关的触发器需要知道您要引用的是哪一个。
因此，我们需要将所有滑动相关触发器的`Source`属性设置为它应该响应的@(SwipeGesture)。

#### $(SwipingAnimation)

`SwipingAnimation`执行动画以响应正在滑动的元素。
最常见的用例是将元素与指针一起移动。

	<面板宽度=“100”高度=“100”背景=“#000”>
		<SwipeGesture ux:Name =“swipe”Direction =“Right”Length =“200”/>
		<SwipingAnimation Source =“swipe”>
			<Move X =“200”/>
		</ SwipingAnimation>
	</ Panel>

代替使用固定长度的滑动，我们也可以从另一个元素的大小来确定它。
这是使用@(SwipeGesture)的`LengthNode`属性实现的，在这种情况下也是@(Move)的RelativeNode属性。

	<Panel ux:Name =“parentContainer”Margin =“40”>
		<面板宽度=“60”高度=“60”背景=“#000”对齐=“左”>
			<SwipeGesture ux:Name =“swipe”Direction =“Right”Type =“Active”LengthNode =“parentContainer”/>
			<SwipingAnimation Source =“swipe”>
				<Move X =“1”RelativeTo =“Size”RelativeNode =“parentContainer”/>
			</ SwipingAnimation>
		</ Panel>
	</ Panel>

#### $(Swiped)

`Swiped`是一个脉冲触发器，当发生滑动时被调用。

	<面板宽度=“100”高度=“100”>
		<SwipeGesture ux:Name =“swipe”Direction =“Up”Length =“50”Type =“Simple”slashb
		<Swiped Source =“swipe”>
			<比例因子=“1.5”持续时间=“0.4”DurationBack =“0.2”/>
		</ Swiped>
	</ Panel>

默认情况下，“Swiped”只会在向主滑动方向滑动时(当它进入活动状态时)触发。
例如，如果@(SwipeGesture)有方向=“左”，它只触发一个`左'滑动，忽略匹配关闭滑动。
我们可以通过将`How`属性设置为`ToActive`(默认)，`ToInactive`或`ToEither'来控制这种行为。

#### $(WhileSwipeActive)

`WhileSwipeActive`当@(SwipeGesture)处于活动状态(当用户已经将其“打开”时)。

	<面板宽度=“100”高度=“100”>
		<SwipeGesture ux:Name =“swipe”Direction =“Up”Length =“50”Type =“Simple”slashb
		<WhileSwipeActive Source =“swipe”>
			<比例因子=“1.5”持续时间=“0.4”DurationBack =“0.2”/>
		</ WhileSwipeActive>
	</ Panel>

#### $(SetSwipeActive)和$(ToggleSwipeActive)

我们可以通过使用`SetSwipeActive`和`ToggleSwipeActive`动作来控制`Active` type @(SwipeGesture:SwipeGestures)的状态。

	<SwipeGesture ux:Name =“swipe”Direction =“Right”Length =“100”Type =“Active”/>
	... ...
	<StackPanel>
		<Button Text =“关闭”>
			<Clicked>
				<SetSwipeActive Target =“swipe”Value =“false”/>
			</ Clicked>
		</ Button>

		<Button Text =“Toggle”>
			<Clicked>
				<ToggleSwipeActive Target =“swipe”/>
			</ Clicked>
		</ Button>
	</ StackPanel>

如果我们想绕过动画，`SetSwipeActive`允许我们通过设置`Bypass =“true”`来实现。

## 数据触发器

这些触发器对数据更改做出反应，无论是从数据绑定还是从控制上下文。

### $(WhileTrue)

`WhileTrue`是活动的，而其`Value`属性是`True`，而当它为false时是非活动的。

### $(WhileFalse)

`WhileFalse`是活动的，而其`Value`属性是`False`，而在它为真时是非活动的。

<!--  ### WhileFailed
TODO:我不知道它做什么 -->

## 本机触发器

### $(平台触发器)

有时它可能需要平台特定的代码。这可以通过使用平台触发器“Android”和“iOS”来完成。

在下面的示例中，我们在Android设备上放置红色@(Panel)if，在iOS设备上放置blue @(Panel)if:

```
<面板>
	<Android>
		<面板背景=“#f00”对齐=“中心”宽度=“150”高度=“150”/>
	</ Android>
	<iOS>
		<面板背景=“#00f”对齐=“中心”宽度=“150”高度=“150”/>
	</ iOS>
</ Panel>
```


### $(WhileKeyboardVisible)

`WhileKeyboardVisible`在屏幕键盘可见时处于活动状态。

<!--  TODO:Example  -->

### $(TextInputActionTriggered)

当用户在编辑@(TextInput)时按下返回键时，会触发TextInputActionTriggered。
以下示例演示了如何通过从输入释放焦点，从而隐藏屏幕键盘来响应此问题。

```
<TextInput>
	<TextInputActionTriggered>
		<ReleaseFocus />
	</ TextInputActionTriggered>
</ TextInput>
```

### $(OnBackButton)

当用户按下其设备上的物理或仿真后退按钮时，将触发此触发器。当按下返回按钮时，以下代码将使屏幕闪烁蓝色:

```
<面板>
	<Rectangle ux:Name =“rect”Layer =“Background”Color =“#F00”/>
	<OnBackButton>
		<Change rect.Color =“#00F”Duration =“0.2”/>
	</ OnBackButton>
</ Panel>
```

> ### $(OnKeyPress)

当按下属性“Key”指定的键时，触发OnKeyPress。
以下示例将按下“菜单”按钮(在一些较旧的Android设备上存在)时，将屏幕蓝色闪烁。

```
<面板>
	<Rectangle ux:Name =“rect”Layer =“Background”Color =“#F00”/>
	<OnKeyPress Key =“MenuButton”>
		<Change rect.Color =“#00F”Duration =“0.2”/>
	</ OnKeyPress>
</ Panel>
```

有关支持的键的完整列表，请查看[Key enum](https://www.fusetools.com/learn/uno/api/uno/platform/key)列表。

## 本机操作

保险丝提供了一组调用操作系统特定行为的操作，例如拨打电话号码或振动设备。

### $(Fuse.Launcher)

Fuse.Launcher包允许您启动其他活动，如电子邮件，地图和网络浏览器。

* 注意:确保您将Fuse.Launcher添加到您的@(项目结构:.unoproj)文件。

#### $(LaunchUri)

请操作系统启动[URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)。

	<LaunchUri Uri =“https://www.fusetools.com”/>

URI可以是从URL到应用程序注册的自定义URI方案的任何内容。底层操作系统负责处理请求。
例如，[这里是一个常见的URI方案列表](http://www.iana.org/assignments/uri-schemes)，在互联网号码分配机构(IANA)注册。

#### $(LaunchEmail)

启动默认电子邮件应用程序，并开始撰写邮件。

	<LaunchEmail To =“contact@fusetools.com”/>

`LaunchEmail`接受下列属性:

- `To' - 收件人的电子邮件地址
- `CarbonCopy`  - 要发送副本的电子邮件地址
- “BlindCarbonCopy” - 发送盲目拷贝的电子邮件地址
- `主题`电子邮件的主题
- `Message`  - 电子邮件的正文文本

#### $(LaunchMaps)

启用默认地图应用程序，给定纬度/经度对。

	<LaunchMaps Latitude =“35.673813”Longitude =“ -  36.780810”/>

#### $(Call)

向指定的号码发出电话呼叫。

	<Call Number =“123 45 678”/>

### $(Vibrate)

振动设备达到给定的秒数。注意:iOS不会遵守任何指定的时间长度，因为这不是原生iOS API中的选项。

	<振动持续时间=“0.8”/>


## $(州组)

状态组允许您使用自定义事件定义完全自定义状态。

### $(State)

一个`State`由一个`State`对象中的一组@(Animators:animators)组成。它作为一个正常的@(触发器)，但是由它包含@(StateGroup)激活。

```
<状态>
	<旋转角度=“200”持续时间=“0.4”/>
	<Move X =“10”Duration =“0.4”/>
</ State>
```

### $(StateGroup)

`StateGroup`用于将一组@(State:states)组合在一起并在它们之间切换。
`StateGroup`有一个`Active`属性，用于分配哪个@(State)当前在该组中处于活动状态。

下面是一个如何使用`StateGroup`来切换@(Rectangle)在三种状态之间的颜色的例子:

```
<StackPanel>
	<面板宽度=“100”高度=“100”>
		<SolidColor ux:Name =“someColor”/>
	</ Panel>
	<StateGroup ux:Name =“stateGroup”>
		<State ux:Name =“redState”>
			<Change someColor.Color =“#f00”Duration =“0.2”/>
		</ State>
		<状态ux:Name =“blueState”>
			<Change someColor.Color =“#00f”Duration =“0.2”/>
		</ State>
		<状态ux:Name =“greenState”>
			<Change someColor.Color =“#0f0”Duration =“0.2”/>
		</ State>
	</ StateGroup>
	<Grid ColumnCount =“3”>
		<Button Text =“Red”>
			<Clicked>
				<Set stateGroup.Active =“redState”/>
			</ Clicked>
		</ Button>
		<Button Text =“Blue”>
			<Clicked>
				<Set stateGroup.Active =“blueState”/>
			</ Clicked>
		</ Button>
		<Button Text =“Green”>
			<Clicked>
				<Set stateGroup.Active =“greenState”/>
			</ Clicked>
		</ Button>
	</ Grid>
</ StackPanel>
```

还可以指定“Transition”，它可以是“Exclusive”或“Parallel”。
`Exclusive`意味着在下一个状态变为活动状态之前，每个状态必须完全禁用。
“并行”意味着当一个状态去激活时，下一个状态将变为活动状态，并且它们动画的任何属性将在它们之间内插。

## $(用户事件)

用户事件用于在应用程序的组件之间发送消息。
它们可以从UX，Uno和JavaScript发送和接收。

* 确保你也看看关于使用@(UserEvents-js:FuseJS从JavaScript的用户事件)的文档。

### $(UserEvent)

用户事件附加到它们声明的节点，并且只有该节点及其子节点可以引发和处理事件。

	<App>
		<UserEvent Name =“MyEvent”/>
		... ...

这将创建一个名为`MyEvent`的事件。
通过将这个`UserEvent`放在`App`中，我们基本上把它作为一个app的事件，因为App的每个孩子都可以提出并响应这个事件。

* 注意:确保向.unoproj文件中添加“Fuse.UserEvents”。

### $(RaiseUserEvent:从UX提高用户事件)

要从UX引发@(UserEvent:user事件)，请使用`RaiseUserEvent`操作。

	<按钮>
		<Clicked>
			<RaiseUserEvent Name =“MyEvent”/>
		</ Clicked>
	</ Button>

### $(UserEventArg:传递参数)

A $(UserEvent:user事件)还可以包括可以从JavaScript或Uno读取的多个参数。

	<RaiseUserEvent Name =“MyEvent”>
		<UserEventArg Name =“message”StringValue =“Hello from UX！” />
	</ RaiseUserEvent>

`UserEventArg`接受`IntValue`，`FloatValue`，`StringValue`或`BoolValue`。

### $(OnUserEvent:响应用户事件)

要响应@(UserEvent:user事件)，请使用`OnUserEvent`触发器。

	<OnUserEvent Name =“MyEvent”>
		... ...
	</ OnUserEvent>

默认情况下，`OnUserEvent`只监听在其祖先节点之一中声明的事件。
如果你想监听来自任何地方的事件，将Filter属性设置为“Global”。

`OnUserEvent`也允许你为事件附加一个JavaScript处理程序。

	<OnUserEvent Name =“MyEvent”Handler =“{myHandler}”/>

然后，处理函数可以读取与事件一起传递的@(UserEventArg:arguments)。

```javascript
function myHandler(args){
	console.log(args.message);
}}

module.exports = {myHandler:myHandler}
```

## $(Viewport triggers)

这些触发器对屏幕几何形状的变化做出反应。

### $(WhileWindowLandscape)

当应用程序的视口宽度大于它的高度时，`WhileWindowLandscape`触发器是活动的。以下示例根据其方向更改应用程序的背景颜色:

```
<App ux:Name =“app”Theme =“Basic”Background =“#FFF”>
     <WhileWindowLandscape>
         <Change app.ClearColor =“0,0,1,1”Duration =“1”/>
     </ WhileWindowLandscape>
</ App>
```

### $(WhileWindowPortrait)

当应用程序的视口高度大于或等于宽度时，`WhileWindowPortrait`触发器是活动的。

### $(WhileWindowSize)

`WhileWindowSize`触发器有三个float2属性控制它的行为:

 * `GreaterThan`
 * `LessThan
 * `EqualTo


这些属性必须大于`0,0'，并且是应用程序视口必须符合的条件，以便触发器处于活动状态。以下是一个示例，如果视口大小大于某个大小，则更改应用程序的背景颜色:

```
<App Theme =“Basic”>
  <面板>
     <SolidColor ux:Name =“coloredPanel”Color =“#F00”/>
     <WhileWindowSize GreaterThan =“400,400”>
       <Change coloredPanel.Color =“#0F0”Duration =“。5”/>
     </ WhileWindowSize>
  </ Panel>
</ App>
```

## $(控制触发器)

### $(WhileEnabled)

每当包含@(Element:elements)`IsEnabled`属性设置为`True`时，`WhileEnabled`触发器是活动的。

```
<面板宽度=“50”高度=“50”背景=“红色”>
	<WhileEnabled>
		<旋转角度=“45”持续时间=“0.5”/>
	</ WhileEnabled>
</ Panel>
```

### $(WhileDisabled)

`WhileDisabled`触发器在其包含@(Element:element)的`IsEnabled`属性设置为`False'时处于活动状态。

## $(聚焦触发器和操作)

### $(WhileFocused)

`WhileFocused`触发器在其包含@(Element:element)被聚焦时是活动的。

### $(WhileNotFocused)

与@(WhileFocused)相反，当它包含@(Element:element)的时候是_not_焦点。

### $(WhileFocusWithin)

`WhileFocusWithin`是活动的，当它的包含@(Element:element)的一个孩子在焦点。

### $(GiveFocus)

`GiveFocus`是一个动作，当激活时，它将焦点放在其包含的@(Element:element)中。
它还接受一个`Target`属性，它指定要给予焦点的元素。

### $(ReleaseFocus)

当激活时，`ReleaseFocus`从当前聚焦的@(Element:元素)中移除焦点。

## WebView特定的触发器和操作

### $(PageBeginLoading)

@(WebView)开始加载新内容时触发。

```
<NativeViewHost>
	<WebView Url =“http://fusetools.com”>
		<PageBeginLoading>
			<DebugAction Message =“页面开始加载！”/>
		</ PageBeginLoading>
	</ WebView>
</ NativeViewHost>
```

### $(WhilePageLoading)

在@(WebView)网址更改时变为活动状态，并保持活动状态，直到完成加载内容。

### $(PageLoaded)

@(WebView)完成从当前网址加载内容后触发。

```
<NativeViewHost>
	<WebView Url =“http://fusetools.com”>
		<PageLoaded>
			<DebugAction Message =“到达页面！”/>
		</ PageLoaded>
	</ WebView>
</ NativeViewHost>
```

### $(Reload)

`Reload`操作允许你告诉给定的WebView重新加载它的当前位置。

`<Reload TargetNode =“myWebView”/>`

### $(LoadUrl)

`LoadUrl`操作允许你告诉给定的WebView导航到一个位置。

`<LoadUrl TargetNode =“myWebView”Url =“http://mypage.com”/>`

### $(LoadHtml)

`LoadHtml`操作允许你为一个给定的WebView提供原始HTML，以便在触发时显示，同时还有一个基本url来设置HTML的上下文。这可以通过将操作的“Source”属性指向数据绑定的HTML字符串，或者通过包装一个@(HTML)并将HTML放在节点体中来实现，如下所示:

```XML
<LoadHtml TargetNode =“myWebView”BaseUrl =“http://my.domain”>
	<HTML>
		<！[CDATA [
			<h1> Boom！</ h1>
		]]>
	</ HTML>
</ LoadHtml>
```

> ### $(EvaluateJS)

WebView在当前加载的Web环境中提供有限的任意JavaScript执行。这是通过`<EvaluateJS />`动作完成的。让我们来看一个简单的例子。

```XML
<EvaluateJS Handler =“{onPageLoaded}”>
	var result = {
		url:document.location.href
	};
	返回结果;
</ EvaluateJS>
```

注意在脚本主体中使用`return`语句。JavaScript评估API的实现通常像JavaScript [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop)，在评估多行JS时，结果的脚本的最后一条语句成为返回值。例如，“1 + 5”在计算时是完全有效的JS，并返回期望值“6”。

这可能导致奇怪的JS，其中引用对象变成一个隐式return语句，而不允许显式返回。

```JavaScript
var result = {};
result.foo =“bar”;
结果; // using return这里是无效的JS
```

为了让这种感觉更好并允许返回，我们目前以一个函数的形式注入用户的JS:

```JavaScript
(function(){USER_JS})();
```

#### 读取结果值

当我们评估JavaScript时，我们目前受到平台限制的关键方式:String是Android上唯一允许的返回值类型，我们的最低公分母。Android允许与API相同的API级别19，这否认我们良好的向后兼容性。现在，我们必须依靠比较古老的[addJavaScriptInterface](http://developer.android.com/reference/android/webkit/WebView.html#addJavascriptInterface(java.lang.Object，java.lang.String))API向后兼容。



```JavaScript
JSON.stringify((function(){USER_JS})());
```

这里返回的JSON字符串然后放入一个结果对象与`json`键。这是为了清楚，所以你永远不会忘记，你接收的数据是一个JSON字符串，你将需要解析。

```XML
<JavaScript>
	module.exports = {
		onPageLoaded:function(result)
		{
			var url = JSON.parse(result.json).url;
		}}
	};
</ JavaScript>
```

注意，当然返回是可选的。如果你不返回任何从你的计算JS，表达式的返回值将只是“null”。


## $(特殊动画)

在保险丝术语中，*动画*是某种类型的触发器，其响应于更高级别的解释而动画化
的输入，事件和逻辑状态更改。

动画触发器被设计为解决相当具体的动画问题，否则将难以使用更简单的触发器。
另一方面，这些触发器可能有点难以理解和使用，所以请确保您在开始之前阅读文档和学习示例
太多的时间抓你的头。

### LayoutAnimation

“LayoutAnimation”是响应布局更改而触发的。每当元素通过布局系统获取新布局时发生布局更改。`LayoutAnimation`通常与@(MultiLayoutPanel)一起使用，用于一些非常有趣的动画。有关更多深入文档，请参阅@(LayoutAnimation)。

<!--  TODO:考虑链接到外部文章 -->

### $(AddingAnimation)

<!--  TODO:链接到示例 -->

每当将元素添加到视觉树中时，就会触发“AddingAnimation”。默认情况下添加动画是一种向后动画，这意味着它将从进度1动画到0。

<!--  TODO:Example  -->

### $(RemovingAnimation)

`RemovingAnimation`类似于@(AddingAnimation)，但是当从可视树中删除一个@(Element)时触发。

`RemovingAnimation`类似于@(AddingAnimation)，但是当元素从其父元素中删除时触发。`RemovingAnimation`通常在指定的@(Duration)上从0到1。

在以下示例中，当通过@(开关)将矩形添加到视图树中时，矩形将从右侧移动其父容器的宽度一秒钟。当它被移除时，它将向左移动相同的距离。

```
<App Theme =“Basic”ClearColor =“#eeeeeeff”>
	<StackPanel Background =“#ddd”Margin =“10”>
		<Switch>
			<WhileTrue>
				<Change whileTrue.Value =“true”/>
			</ WhileTrue>
		</ Switch>
		<WhileTrue ux:Name =“whileTrue”>
			<面板高度=“50”宽度=“50”背景=“红色”>
				<AddingAnimation>
					<Move RelativeTo =“ParentSize”X =“1”Duration =“1”Easing =“BounceIn”/>
				</ AddingAnimation>
				<删除动画>
					<Move RelativeTo =“ParentSize”X =“ -  1”Duration =“0.5”/>
				</ RemovingAnimation>
			</ Panel>
		</ WhileTrue>
	</ StackPanel>
</ App>
```

### $(ActivatingAnimation)

`ActivatingAnimation`允许根据@(Page)是活动的动画。`ActivatingAnimation`将在@(页面)导航到时从0进展到1。如果使用@(SwipeNavigate)，可以观察到`ActivatingAnimation'一旦进入@(页面)就从0开始，只要@(Page)被激活，就保持在1，然后再次向0 @(Page)退出。

### $(DeactivatingAnimation)

`DeactivatingAnimation`就像`ActivatingAnimation`，除了进度是相反的。这意味着触发器将在@(页面)激活时从1进展到0，当它取消激活时，触发器从0到1。

### $(ScrollingAnimation)

`ScrollingAnimation`允许我们创建动画以响应正在滚动的@(ScrollView)。通过使用“From”和“To”属性，可以在@(ScrollView)上定义触发器被激活的间隔。

在下面的示例中，我们使用`ScrollingAnimation`来@(ScrollView)在滚动时@(缩放)。

```
<ScrollView>
	<StackPanel Background =“#ddd”Margin =“10”>
		<面板高度=“200”背景=“红色”/>
		<面板高度=“200”背景=“蓝色”/>
		<面板高度=“200”背景=“绿色”/>
		<面板高度=“200”背景=“紫色”/>
		<面板高度=“200”背景=“青色”/>
	</ StackPanel>
	<ScrollingAnimation From =“0”To =“400”>
		<比例因子=“0.3”/>
	</ ScrollingAnimation>
</ ScrollView>
```

> ### $(PullToReload)

`PullToReload`允许你轻松创建一个“pull to reload”交互。



```
<ScrollView>
	<PullToReload>
		<状态ux:绑定=“拉”>
			<!-- 将在用户向下拉时激活 -->
		</ State>
		<状态ux:Binding =“PulledPastThreshold”>
			<!-- 当用户已经超过阈值时将处于活动状态 -->
		</ State>
		<状态ux:Binding =“加载”>
			<!-- 当指针在超过阈值后释放时将处于活动状态 -->
		</ State>
	</ PullToReload>
</ ScrollView>
```

通过数据绑定到`IsLoading`和`ReloadHandler`属性，我们可以响应重启正在启动。

```
<JavaScript>
	var Observable = require(“FuseJS / Observable”);
	
	var isLoading = Observable(false);
	function onReload(){
		fetchDataSomehow(function(){
			isLoading.value = false;
		});
	}}
	
	module.exports = {isLoading，onReload};
</ JavaScript>

<ScrollView>
	<PullToReload IsLoading =“{isLoading}”ReloadHandler =“{onReload}”/>
</ ScrollView>
```

** 注意:**在上面的例子中，`IsLoading`有一个双向数据绑定到`isLoading`，这意味着`PullToReload`本身和`isLoading` Observable都可以改变它的值。

因为`PullToReload`实际上是一个@(ScrollingAnimation)，我们可以使用它来显示一个负载指示器，当用户向下拉。

```
<面板>
	<Circle ux:Name =“loadingIndicator”Width =“50”Height =“50”Color =“Red”Alignment =“Top”Offset =“0，-50”/>
	<ScrollView>
		<PullToReload>
			<Move Target =“loadingIndicator”Y =“2”RelativeTo =“Size”Duration =“1”/>
		</ PullToReload>
	</ ScrollView>
</ Panel>
```

### $(WhileScrollable)

`WhileScrollable'用于根据是否可以滚动@(ScrollView)来动画。使用`ScrollDirections`属性根据我们关心的方向过滤激活。

`ScrollDirections`可以取以下任意一个值:

- 全部
- 两者
- 下
- 水平
- 剩下
- 对
- 向上
- 垂直

在下面的示例中，当我们到达@(ScrollView)的底部时，我们的背景颜色会改变:

```
<ScrollViewer>
	<SolidColor ux:Name =“color”Color =“#000”/>
	<StackPanel Margin =“10”>
			<Every Count =“10”>
				<面板高度=“200”背景=“红色”margin =“2”/>
			</ Each>
		</ StackPanel>
	<WhileScrollable ScrollDirections =“Down”>
		<Change color.Color =“#ddd”Duration =“0.4”/>
	</ WhileScrollable>
</ ScrollViewer>
```

### $(ProgressAnimation)

`ProgressAnimation`可以与滑块一起使用，以便在元素滑动时滑动其大拇指。`ProgressAnimation`总是从0到1，因为滑块从最小值滑到最大值。

```
<Image File =“someImage.png”>
	<Blur ux:Name =“blur”Radius =“0”/>
</ Image>
<滑块宽度=“50％”对齐=“底部”Margin =“0,80”>
	<ProgressAnimation>
		<Change blur.Radius =“10”/>
	</ ProgressAnimation>
</ Slider>
```

### $(时间轴)

“时间轴”允许一个很好的方式将几个动画组合在一起，并将它们与交互逻辑分开。它可以通过在0到1之间的“TargetProgress”属性进行动画播放。



```
<App Theme =“Basic”>
	<StackPanel>
		<Rectangle ux:Name =“rect”Height =“40”Width =“100％”>
			<SolidColor ux:Name =“color”Color =“#f00”/>
		</ Rectangle>
		<Grid ColumnCount =“2”>
			<Button Text =“Red”>
				<Clicked>
					<Set timeline.TargetProgress =“0”/>
				</ Clicked>
			</ Button>
			<Button Text =“Green”>
				<Clicked>
					<Set timeline.TargetProgress =“1”/>
				</ Clicked>
			</ Button>
		</ Grid>

		<Timeline ux:Name =“timeline”>
			<Change Target =“rect.Width”>
				<关键帧值=“10”时间=“0.3”/>
				<关键帧值=“100”时间=“0.6”/>
			</ Change>
			<Change color.Color =“#0f0”Duration =“0.3”Delay =“0.3”/>
		</ Timeline>
	</ StackPanel>
</ App>
```

## $(RangeAdapter:Range adapter)

`RangeAdapter`可用于调整绑定中使用的值的范围。这允许更好地控制动画，如“时间轴”和“...动画”触发器。

- `Source'要修改的值。这个值总是直接更新，`RangeAdapter`不会在内部存储任何值。
- `SourceRangeMin`映射到源的最小值
- `SourceRangeMax`要映射到源的最大值
- `ValueRangeMin`映射到值的最小值。默认值0
- `ValueRangeMax`要映射到的值的最大值。默认值1
- 'Value'是要翻译的值。将此更改为使用已翻译的值更新“源”。

在以下示例中，旋转了两个面板。然而，由于其中一个面板通过“RangeAdapter”进行动画处理，它只会旋转另一个面板旋转的一半:

```
<App Theme =“Basic”Background =“#fff”>
  <StackPanel>
    <StackPanel Height =“200”>
      <Panel ux:Name =“animationTarget”Color =“#874cff”Width =“70”Height =“70”Margin =“10”/>
      <Panel ux:Name =“animationTargetTwo”Color =“#d46fff”Width =“70”Height =“70”Margin =“10”/>
    </ StackPanel>
    <Button Text =“运行动画”>
      <Clicked>
	<切换目标=“actualAnim”/>
      </ Clicked>
    </ Button>
    <Timeline ux:Name =“timeline”>
      <Rotate Target =“animationTargetTwo”Degrees =“90”Duration =“1”/>
    </ Timeline>
    <RangeAdapter ux:Name =“range”Source =“timeline.Progress”SourceRangeMin =“0”SourceRangeMax =“0.5”/>
    <WhileTrue ux:Name =“actualAnim”>
      <Rotate Target =“animationTarget”Degrees =“90”Duration =“1”/>
      <Change range.Value =“1”Duration =“1”/>
    </ WhileTrue>
  </ StackPanel>
</ App>
```

<!--  ##高级触发器使用

TODO:显示示例
* 文档旁路
* 触发器内的触发器
* 触发器中的元素/节点
* 触发器中的样式(？不确定这是否可能) -->
