# UI组件

Fuse附带了许多可用于构建用户界面的UI组件。在UX中，您可以通过将UI组件包含在标记中来添加它们:

    <Image File =“MyImage.png”/>
    <Rectangle Width =“50”Height =“50”Fill =“#888”/>
    <Text> Hello world！</ Text>

注意，只是因为某物被包含在标签中，并不一定意味着它必须是一个UI组件。UX对其他概念也使用标签，例如@(Trigger:Triggers)和@(Actions)。

## $(Text)

这里是一个微小的应用程序，呈现文本:

	<App>
		<Text> Hello，world！</ Text>
	</ App>

这是伟大的，但当你有更长的文本段落，如_Lorem Ipsum_，你最有可能想要启用字包装。在Fuse中，这是通过`Text`控件上的`$(TextWrapping)`属性来完成的:

	<Text TextWrapping =“Wrap”> Lorem Ipsum(...)</ Text>

如果你仍然发现包装文本仍然很难显示你想要的所有内容，你可能想考虑将内容添加到@(ScrollView)或更改@(FontSize)。`TextWrapping`可以设置为`Wrap`或`NoWrap`(默认)。

### $(字体)

您可以从包含TrueType字体的ttf文件导入字体。由于字体通常在整个应用程序中引用，因此最好简单地为其创建一个_ @(全局资源:全局资源)_。

	<App>
		<Font File =“Roboto-Medium.ttf”ux:Global =“Medium”/>
		<Font File =“Roboto-Light.ttf”ux:Global =“Light”/>
		<StackPanel>
			<Text Font =“Light”> Roboto Light </ Text>
			<Text Font =“Medium”> Roboto Medium </ Text>
			<Text Font =“Light”> Roboto Light再次</ Text>
		</ StackPanel>
	</ App>

在此示例中，字体位于与相关UX文件相同的目录中。这种导入字体的方式确保字体在整个项目中可用，并且只加载一次。

iOS和Android都支持使用多字节字符集的文本呈现。这意味着emojis在设备上工作精细渲染。

_Note！_目前有一些问题使用桌面预览呈现多字节字符集。不要惊讶，如果桌面渲染不匹配设备呈现100％。这是一个正在处理的问题。

### 文本属性

为了进一步控制如何渲染文本，可以设置`$(TextAlignment)`，`$(TextColor)`，`$(FontSize)`和`$(LineSpacing)`:

	<Text TextColor =“#999”>左</ Text>
	<Text TextAlignment =“Center”>中心</ Text>
	<Text FontSize =“24”TextAlignment =“Right”> Right </ Text>
	<Text LineSpacing =“10”>
	多
	线
	</ Text>

在本示例中，第一个文本元素将左对齐，因为这是默认值，并且其颜色设置为中间浅灰色。第二个文本将居中对齐。第三个将右对齐，并具有较大的字体。第四个将跨越两条线，其间有10个空间。`TextAlignment'的有效值为`Left`(默认)，`Right`和`Center`。

> ### $(Number)

虽然@(文本)是在大多数情况下，你可以使用`Number`来显示一个格式化的数值。
通过“Format”属性指定的格式必须是[标准数字格式字符串](https://msdn.microsoft.com/en-us/library/dwhawy9k.aspx)的形式。

`Number`当前支持的唯一格式是`F`，它截断要显示的小数位数。

下面的代码将渲染`3.141`:

```
<Number Value =“3.14159265359”Format =“F03”/>
```

## $(图片)

保险丝支持本地显示PNG和JPEG文件(在RGB颜色空间中)。显示图像:

	<App>
		<Image File =“FuseLogo.png”/>
	</ App>

此代码假定文件`FuseLogo.png`与UX文件存在于同一目录中。如果你想从互联网加载图像的内容，只需:

	<Image Url =“http:// path_to_image”/>

_Note！_如果你是从web开发人员的背景，你可能习惯于分配一个URL到src`属性。虽然`Image`有一个`Source`属性，它用于为一个图像分配一个`Resource`。在这个上下文中，这个“Resource”是一个“HttpImageSource”，但是它是为你自动在封面后面创建的，所以坚持使用`Url`-property从网页加载内容。

> ###来自资源的图像内容

对于其他方法加载图像数据的一个小例子，这里是一个小例子，也使用JavaScript中的数据绑定:

	<FileImageSourceux:Key =“pic2”File =“Images / Image2.jpg”/>
	<StackPanel>
		<JavaScript>
			module.exports = {
				imageResource:“pic2”，
				url:“http://somewhereontheinternet/Cute-Cat.jpg”
			}}
		</ JavaScript>
		<Image File =“Images / Image1.jpg”/>
		<Image Source =“{DataToResource imageResource}”/>
		<Image Url =“{url}”/>
	</ StackPanel>

此代码将显示三个图像堆叠在彼此的顶部。最上面的图像将作为项目的一部分的文件提取。在顶部，我们通过创建一个`FileImageSource`来引用一个文件，我们使用`DataToResource`绑定到一个图像。这将从它从JavaScript获取的键查找资源。我们还得到一个图片在网络上的URL并绑定到一个`图像'的`Url`属性。如果这看起来很复杂，不要担心:我们稍后将更多地看看@(数据绑定)和@(JavaScript)。

> ###图像颜色

可以通过使用“颜色”属性来对“图像”进行着色。请注意，这将以最可预测的方式影响最接近白色的图像部分。例如:

	<Image File =“WhiteIcon.png”Color =“#f00”/>

这将使白色图标变红。

<!-- 删除由于resampleMode被损坏的ATM
> ###自定义重新采样模式

在某些情况下，例如显示低分辨率的图片时，您可能需要控制重新取样模式。这是可行的使用`ResampleMode`属性。有了它，你有以下选择:

* 线性 - 基于图像上最接近的样本线性地采样颜色
* Mipmap  - 对来自mipmap的颜色进行采样，该颜色最接近屏幕上图像的分辨率
* 最近 - 对最接近预期采样点的颜色进行采样

在下面的例子中，我们使用了一个带有“最近”重采样模式的“Image”来绘制低分辨率的图像，像像素艺术，没有得到一个模糊的图像:

```
<Image ResampleMode =“Nearest”File =“Art.png”/>
```
-->

> ### $(StretchMode)

当添加到一个容器，一个`Image`将默认尝试尽可能显示自己。如果图像与其容器不是同一方面，则将存在将不被覆盖的容器的部分。

有许多方法来解决这个问题。你可以在你的`Image`上设置`StretchMode`属性，使它的行为不同。这里有不同的模式:

- `Fill`  - 填充容器中的可用区域，而不必保留宽高比。
- `PixelPrecise`  - 使用图像中的像素作为单位，以确保图像在所有设备上清脆。这意味着图像在不同设备上的大小会不同。它忽略了`Image`容器的大小。
- `PointPrecise`  - 这使用图像源的大小作为`图像'的点，这保证它将在所有设备上相同。它忽略了`Image`容器的大小。例如，如果图像的大小为64x64像素，则生成的控件将为64x64 @(点)
- `PointPrefer`  - “PointPrefer”拉伸模式将优先使用“PointPrecise”拉伸模式获得图像大小正确，但是在PixelPrecise将创建一个大约合适大小的屏幕图像的情况下，它将使用PixelPrecise以增加图像的清晰度。
- `Scale9`  - 如果你使用`Scale9`，`Image`将根据它的$(Scale9Margin)。这个边缘将决定哪些像素将被拖曳和多少。边缘将图像分为9个区域，其中角将保留其原始方面，其余区域将被拉伸以适应所需的图像大小。
- “制服” - 这将使图片尽可能大，同时保持长宽比。这通常会使“Image”不覆盖整个父类。
- `UniformToFill`  - 填充父容器，同时保留宽高比。这通常意味着图片的部分被省略，由父母剪裁

#### $(StretchDirection)

您可以通过设置“StretchDirection”属性来设置图像缩放的方向:

- `Both`  - 允许上下调整
- `UpOnly`  - 只有高档内容
- `DownOnly`  - 只有缩小内容

> ### $(ContentAlignment)

您可以在图像上设置对齐，这将使图像在屏幕上的矩形内对齐，如果它在其矩形中填充更多或更少的可用空间。这在使用@(StretchDirection)和@(StretchMode)时可能很常见:

- `默认'
- `左'
- `Horizo​​ntalCenter`
- “右”
- `Top`
- `VerticalCenter`
- `Bottom`
- `TopLeft`
- `TopCenter`
- `TopRight`
- `CenterLeft`
- `中心'
- `CenterRight`
- `BottomLeft`
- `BottomCenter`
- `BottomRight`

### $(图片来源)

可以使用`File`或`Url`属性直接在@(Image)上指定图像源。
然而，这减少了对图像的寿命的几个方面的控制。

使用$(ImageSource)对象，而不是直接在@(Image)属性上指定源
允许您在整个应用程序中将图像源命名和重用为资源，而不会重复
路径和元数据。

以下示例说明如何将图像声明为资源:

	<FileImageSource ux:Global =“CloseIcon”File =“close.png”Density =“2”/>

然后你可以在项目的任何地方使用`CloseIcon`:

	<Image Source =“CloseIcon”/>

保险丝当前支持以下图像源类型:

* `FileImageSource`  - 指定单个本地映像文件及其密度
* @(HttpImageSource) - 指定来自URL及其密度的单个图像
* @(MultiDensityImageSource) - 允许您指定同一图像的多个版本以用于不同的屏幕密度。

> ### $(MultiDensityImageSource)

由于设备的像素密度差别很大，Fuse允许您为同一个逻辑“图像”指定多个图像资源:

	<Image StretchMode =“PointPrefer”>
		<MultiDensityImageSource>
			<FileImageSource File =“Icon.png”Density =“1”/>
			<FileImageSource File =“Icon.png@2x.png”Density =“2”/>
		</ MultiDensityImageSource>
	</ Image>

然后，保险丝将选择最适合屏幕的资源，尊重图像的$(StretchMode)。

> ### $(HttpImageSource)

`HttpImageSource`允许你指定一个从HTTP获取的图像，并以异步方式显示。

	<Image>
		<MultiDensityImageSource>
			<HttpImageSource Url =“{image_url_1x}”Density =“1”/>
			<HttpImageSource Url =“{image_url_2x}”Density =“2”/>
		</ MultiDensityImageSource>
	</ Image>

或者，如果要使用固定密度图像，则可以直接使用较短的形式:

	<Image Url =“{image_url}”/>

请注意，从Http获取的图像可能需要一些时间加载，直到加载，Fuse不知道图像的尺寸。因此，您应该谨慎地将应用程序的布局基于HTTP提取的图像的维度。

## $(视频)

显示视频:

	<Video File =“fuse_video.mp4”/>

视频允许从文件或流通过其属性`File`和`Url`播放视频。视频类似于@(Image)，它们共享属性@(StretchMode)，@(StretchDirection)和@(ContentAlignment)，它们以相同的方式工作。

### $(视频属性)

视频带有一组属性，可用于配置或控制它，除了与@(图像)共享的属性:

- `Volume`:范围从`0.0`到`1.0`，默认是`1.0`
- `Duration`:视频的持续时间(以秒为单位)
- “位置”:视频的当前位置(以秒为单位)
- `IsLooping`:一个`bool`指定视频是否应该循环，默认是`false`

### $(Video-Triggers:可用于视频的有用触发器)

	<视频>
		<WhilePlaying /> <!-- 正在播放视频时有效 -->
		<WhilePaused /> <!-- 视频暂停时处于活动状态 -->
		<WhileCompleted /> <!-- 在视频播放完成时激活 -->
		<WhileLoading /> <!-- 在视频加载时处于活动状态 -->
		<WhileFailed /> <!-- 如果视频加载失败或出现错误，则处于活动状态 -->
	</ Video>

### $(Video-Actions:可用于控制视频的有用操作)
保险丝提供了一组可用于控制视频播放的操作。它们都有一个共同的`Target`属性，指定它们控制哪个$(Video)元素。

```
<暂停/> <!-- 暂停播放，保留当前位置为 -->
<停止/> <!-- 停止播放并返回到视频的开头 -->
<继续/> <!-- 从当前位置恢复播放 -->
<PlayTo Progress =“...”/> <!-- 播放到由进度属性指定的时间 -->
```

### 支持的格式

视频通过使用导出目标提供的视频解码器实现，因此支持任何平台支持。
请注意，Windows，OS X，Android和iOS可能不会共享对某些格式的支持

-  [Android supported formats](http://developer.android.com/guide/appendix/media-formats.html)
-  [iOS和OS X支持的格式(在'public.movi​​e'下)](https://developer.apple.com/library/mac/documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html)
-  [Windows支持的格式](https://msdn.microsoft.com/en-us/library/cc189080%28v=vs.95%29.aspx)

> ### $(视频示例)

	<App Theme =“Basic”>
		<DockPanel>
			<Video ux:Name =“video”Dock =“Fill”File =“fuse_video.mp4”IsLooping =“true”StretchMode =“UniformToFill”>
				<ProgressAnimation>
					<Change progressBar.Width =“100”/>
				</ ProgressAnimation>
			</ Video>
			<Rectangle ux:Name =“progressBar”Dock =“Bottom”Fill =“#f00”Width =“0％”Height =“10”/>
			<Grid Dock =“Bottom”ColumnCount =“2”RowCount =“1”>
				<Button Text =“Play”>
					<Clicked>
						<Resume Target =“video”/>
					</ Clicked>
				</ Button>
				<按钮>
					<Clicked>
						<暂停目标=“视频”/>
					</ Clicked>
				</ Button>
			</ Grid>
		</ DockPanel>
	</ App>


## $(形状)

保险丝可以渲染`Rectangle`s和`Circle`s。这些形状可以有多个@(Fill:Fills)和@(Stroke:Strokes)。这些将在彼此的顶部分层。

### $(Rectangle)

绘制一个`Rectangle`:

	<Rectangle Fill =“#f00”/>

在这个例子中，`Rectangle`会占用父元素所允许的空间，并用红色@(SolidColor)填充。

如果你想让`Rectangle`限制自己的大小，你可以添加`Width`和`Height`:

	<Rectangle Fill =“#f00”Width =“50”Height =“50”CornerRadius =“5”/>

这将呈现一个带有圆角的红色“矩形”。请注意，这些@(单位:单位)是@(点)，而不是@(像素)，并且在大多数设备上，“矩形”看起来大致相同的大小，而不管像素密度和屏幕大小。

### $(Circle)

绘制一个“Circle”同样简单:


	<Circle Fill =“#f00”Width =“50”Height =“50”>
		<笔画宽度=“5”Brush =“#ff0”/>
	</ Circle>

在这个例子中，我们更进一步，我们添加了一个黄色@(Stroke)。

#### $(StartAngle)/ $(EndAngle)

`StartAngle`和`EndAngle`只能用来绘制一个@(Circle)的切片。
有6个不同的属性可以用于以不同的方式控制。

* `StartAngle`  - 切片开始的以弧度表示的角度
* `EndAngle`  - 以弧度表示切片结束的角度
* $(StartAngleDegrees) - 切片开始的角度
* $(EndAngleDegrees) - 切片结束的角度
* $(LengthAngle) - 以@(StartAngle)为单位的弧度偏移量。这可以用来代替@(EndAngle)
* $(LengthAngleDegrees) - 与@(StartAngle)的偏移量。这可以用来代替@(EndAngleDegrees)

注意，例如在同一个@(Circle)上使用@(StartAngle)和@(StartAngleDegrees)都会有一个未定义的行为。


> ### $(RegularPolygon)

“RegularPolygon”是所有角度相等的凸形，并且所有边具有相等的长度。
三角形，六边形和八边形都是正多边形。
侧面的数量可以用`Sides`属性控制。

	<RegularPolygon Sides =“6”Fill =“#000”Width =“50”Height =“50”/>

> ### $(Ellipse)

当你想要一个非常数半径的@(圆)时，使用`椭圆'。

	<Ellipse Width =“100”Height =“50”Fill =“#f00”/>

### 样式化形状

为了风格化形状，我们提供两个属性:

* `Fill`  - 样式的形状的背景
* `Stroke`  - 样式的形状的边框

#### $(Fill:Fills)

我们已经看到形状接受简单的`Fill`属性:

	<Rectangle Fill =“#f00”/>

可以使用其他种类的刷子来填充形状。例如:


	<StackPanel>
		<圆宽=“150”高=“150”>
			<ImageFill File =“Pictures / Picture1.jpg”/>
		</ Circle>
		<Rectangle Height =“150”>
			<LinearGradient StartPoint =“0,0”EndPoint =“1,0.75”>
				<GradientStop Offset =“0”Color =“#FC3C47”/>
				<GradientStop Offset =“1”Color =“#B73070”/>
			</ LinearGradient>
		</ Rectangle>
	</ StackPanel>

在这里，我们创建了一个“Circle”，它已经填充了一个“ImageFill”笔刷，非常适合在社交应用程序中创建典型的个人资料图片。然后在它下面，我们创建了一个@(Rectangle)，它有一个漂亮和微妙的“LinearGradient”。

#### $(Stroke:Strokes)

笔划接受刷子的方式与@(Fill)相同:

	<StackPanel>
		<圆宽=“150”高=“150”>
			<行程宽度=“10”>
				<ImageFill File =“Pictures / Picture1.jpg”/>
			</ Stroke>
		</ Circle>
		<Rectangle Height =“150”>
			<行程宽度=“15”>
				<LinearGradient StartPoint =“0,0”EndPoint =“1,0.75”>
					<GradientStop Offset =“0”Color =“#FC3C47”/>
					<GradientStop Offset =“1”Color =“#B73070”/>
				</ LinearGradient>
			</ Stroke>
		</ Rectangle>
	</ StackPanel>

它也可以设置为@(SolidColor:SolidColor-brush)使用`Brush`-property:

	<Rectangle Fill =“#f00”Width =“50”Height =“50”>
		<笔画宽度=“5”Brush =“#ff0”/>
	</ Rectangle>


##### $(StrokeAlignment:Alignment)

@(行程)可以对齐:

	<描边对齐=“中心”/>

有效值为“中心”，“内部”和“外部”。

##### $(Stroke.Offset)

@(形状:Shape)的@(笔画)可以是'偏移':

	<行程宽度=“10”偏移=“10”>
		<ImageFill File =“Pictures / Picture1.jpg”/>
	</ Stroke>

正的“偏移”将使“描边”出现在“形状”外，而负的“偏移”将使它出现在内部。


### $(Brush:Brushes)

“Brush”是一个如何着色的定义。它可以直接应用到@(Shapes)`Fill`属性，也可以应用到@(Stroke)来为形状的边框设置样式。

这是你将如何使用画笔来填充形状的样式。注意，`SolidColor`刷子绑定到矩形的`Fill`属性:

```
<Rectangle>
	<SolidColor Color =“#f00”/>
</ Rectangle>
```

这是你将如何使用画笔来定义一个形状的边框:

```
<Rectangle>
	<行程>
		<SolidColor Color =“#0f0”/>
	</ Stroke>
</ Rectangle>
```

#### $(SolidColor)

如果你想做一个简单的连续颜色，你可以使用一个`SolidColor`:

```
<SolidColor Color =“#00f”/>
```

这将创建一个画笔，可以分配到任何接受画笔的地方，例如@(矩形):

```
<Rectangle>
	<SolidColor Color =“#00f”/>
</ Rectangle>
```

注意这相当于写作:

```
<Rectangle Fill =“#00f”/>
```

> #### $(IsCompletelyTransparent:查明SolidColor是否完全透明)

如果颜色的alpha值为0，那么`IsCompletelyTransparent`属性将为true。


#### $(ImageFill)

`ImageFill`是一个@(Brush)，用图像填充父@(Shapes:shape)。它很像一个@(Image)在很多方面，但它有一个非常重要的区别:`ImageFill`是一个画笔，并应该为一个形状，因此不工作本身。下面的示例将填充一个`Circle`与从文件`Portrait.png`加载的图像:

```
<Circle Width =“160”Height =“160”>
	<ImageFill File =“Portrait.png”/>
</ Circle>
```

#### $(LinearGradient)

您可以使用“LinearGradient”和“GradientStop”描述一个“LinearGradient”画笔。例如，要创建一个灰色斜坡，其顶部为白色，底部为黑色:

	<LinearGradient StartPoint =“0,0”EndPoint =“0,1”>
		<GradientStop Offset =“0”Color =“#fff”/>
		<GradientStop Offset =“1”Color =“#000”/>
	</ LinearGradient>

`StartPoint`和`EndPoint`都是使用画刷的@(形状:Shape)中的X和Y偏移，因此可以使用`StartPoint =“0,0”EndPoint =“1指定一个对角画笔， 1“。

而不是`StartPoint`和`EndPoint`，你也可以指定一个角度。这可以是使用`Angle`属性的弧度，或者使用`AngleDegrees`属性的度数。

	<LinearGradient Angle =“2.4”/>

	             要么

	<LinearGradient AngleDegrees =“45”>

## $(Button)

<!--  TODO:删除DebugAction和或重命名为<Debug Message =，这有一个pull请求，但是这些例子需要一个搜索/替换DebugAction  --> Debug  -->

按钮是可点击的控件，它们的外观和感觉从@(主题)。

	<App Theme =“Basic”>
		<Button Text =“点击我！ux:Name =“button1”>
			<Clicked>
				<Set button1.Text =“Clicked！” />
			</ Clicked>
		</ Button>
	</ App>

这个小例子将创建一个覆盖整个屏幕的“Button”。当您点击它时，其标签将从“点击我！到“点击！”。

在Fuse，几乎任何东西都可以很容易地做@(点击:可点击)(和@(Tapped:tappable)等)。因此，如果您想为按钮设计自定义的外观和感觉，可以使用任何组件:

	<App>
		<Rectangle Fill =“#309”>
			<Clicked>
				<DebugAction Message =“Rectangle got clicked”/>
			</ Clicked>
		</ Rectangle>
	</ App>

当您单击矩形时，如果您在预览模式下运行，则“Message”输出将显示在[Monitor](https://www.fusetools.com/learn/guides/preview-and-export-monitor)中。它也将显示在标准设备日志中，或者，如果您从命令行启动预览过程，在标准控制台中。


### $(事件触发器)

“Button”也可以接受“Clicked”作为_event-trigger_:

	<App Theme =“Basic”>
		<JavaScript>
			module.exports = {
				buttonClick:function(args){console.log(“Button was clicked”); }}
			}}
		</ JavaScript>
		<Button Text =“点击我！Clicked =“{buttonClick}”/>
	</ App>

当你想从事件中驱动业务逻辑时，这是很方便的。

## $(Switch)

为了接受开/关式输入，Fuse有一个`Switch`-control:

	<开关/>

要使其对打开响应，您可以使用@(WhileTrue:WhileTrue-trigger):

	<App Theme =“Basic”>
		<StackPanel>
			<Switch>
				<WhileTrue>
					<Change rectangle.Width =“160”Duration =“0.5”
						Easing =“CircularInOut”/>
				</ WhileTrue>
			</ Switch>
			<Rectangle ux:Name =“rectangle”Width =“70”Height =“70”Fill =“#909”/>
		</ StackPanel>
	</ App>

要使它处于相反的状态，可以使用@(WhileFalse)或`WhileTrue Invert =“true”`。

如果你想要“开关”开始被激活:

	<Switch Value =“true”/>

`Switch`发出的事件也可以从JavaScript处理:

	<App Theme =“Basic”>
		<JavaScript>
			module.exports = {
				switchChanged:function(args){
					console.log(“Switch value is:”+ args.value);
				}}
			};
		</ JavaScript>
		<StackPanel>
			<Switch Value =“true”ValueChanged =“{switchChanged}”/>
		</ StackPanel>
	</ App>

> ###数据绑定开关

你也可以使用它的'Value`属性来数据绑定`Switch`:

	<App Theme =“Basic”>
		<JavaScript>
			var Observable = require(“FuseJS / Observable”);

			var switchValue = Observable(false);

			module.exports = {
				switchValue:switchValue，
				enableSwitch:function(){switchValue.value = true; }，
				disableSwitch:function(){switchValue.value = false; }，
				switchChanged:function(args){
					console.log(“Switch value is:”+ args.value);
				}}
			};
		</ JavaScript>
		<StackPanel>
			<Switch Value =“{switchValue}”ValueChanged =“{switchChanged}”/>
			<Grid ColumnCount =“2”>
				<Button Text =“Disable”Clicked =“{disableSwitch}”/>
				<Button Text =“Enable”Clicked =“{enableSwitch}”/>
			</ Grid>
		</ StackPanel>
	</ App>

## $(滑块)

要显示滑块:

	<Slider />

要对正在移动的滑块作出反应，请使用`ProgressAnimation`。考虑这个代码，它允许你从0到90度旋转`Rectangle`:

	<StackPanel>
		<Slider>
			<ProgressAnimation>
				<Rotate Target =“rectangle”Degrees =“90”/>
			</ ProgressAnimation>
		</ Slider>
		<Rectangle ux:Name =“rectangle”Width =“100”Height =“100”Fill =“#808”/>
	</ StackPanel>

你也可以听'ValueChanged`事件:

	<App Theme =“Basic”>
		<JavaScript>
			module.exports = {
				sliderValueChanged:function(args)
				{
					console.log(“Value:”+ args.value);
				}}
			};
		</ JavaScript>
		<Slider ValueChanged =“{sliderValueChanged}”/>
	</ App>

当将滑块从最左侧移动到最右侧时，控制台将输出0到100之间的浮点数，这是默认的“最小值”和“最大值”。这些属性也可以设置:

	<滑块最小值=“4”最大值=“42”/>

> ###数据绑定滑块

也可以对滑块位置进行数据绑定:

	<App Theme =“Basic”>
		<JavaScript>
			var Observable = require(“FuseJS / Observable”);

			var sliderValue = Observable(42);
			var sliderLabel = sliderValue.map(function(val){
				return“当前位置:”+ val;
			});

			module.exports = {
				sliderValue:sliderValue，
				sliderLabel:sliderLabel
			};
		</ JavaScript>
		<StackPanel>
			<Slider Value =“{sliderValue}”/>
			<Text TextAlignment =“Center”Value =“{sliderLabel}”/>
		</ StackPanel>
	</ App>

## $(TextInput)和$(TextEdit)

保险丝提供两种不同的控制输入和编辑文本:


* `TextInput`是一个根据当前@(Theme)装饰的文本编辑器。
* `TextEdit`是没有视觉装饰的纯文本编辑器，否则等同于`TextInput`。

在下面的例子中，`TextInput`和`TextEdit`可以互换使用:

	<JavaScript>
		var valueChanged = function(args){
			console.log(args.value);
		}}

		module.exports = {
			valueChanged:valueChanged
		};
	</ JavaScript>

	<TextInput ValueChanged =“{valueChanged}”/>

你也可以轻松做双向数据绑定:

	<App Theme =“Basic”>
		<JavaScript>
			var Observable = require(“FuseJS / Observable”);

			var name = Observable(“”);

			var greeting = name.map(function(name){
				if(name ==“”){
					return“输入你的名字”;
				} else {
					return“Hello there，”+ name +“！”;
				}}
			});

			var clearName = function(){
				name.value =“”;
			}}

			module.exports = {
				name:name，
				问候:问候，
				clearName:clearName
			};
		</ JavaScript>

		<StackPanel>
			<StatusBarBackground />
			<DockPanel>
				<Text Dock =“Left”Alignment =“VerticalCenter”> Name:</ Text>
				<TextInput Value =“{name}”Alignment =“VerticalCenter”/>
			</ DockPanel>
			<Text TextAlignment =“Center”Value =“{greeting}”/>
			<Button Clicked =“{clearName}”Text =“Clear”/>
		</ StackPanel>
	</ App>

如果要接受密码，您可能需要屏蔽用户输入:

	<TextInput IsPassword =“true”/>

如果你想接受数值主要，你可以设置一个`InputHint`:

	<TextInput InputHint =“Integer”/>

`InputHint的有效值是`Default`，`Email`，`Integer`，`Decimal`，`Phone`，`Url`。这些被称为“提示”，因为他们可能不会做任何事情，这取决于你在哪个平台。例如，当在桌面上时，键盘将是相同的，无论是什么提示添加到`TextInput`。

`TextInput`也允许您通过多行输入内容，而不是向右滚动，默认情况下:

	<TextInput IsMultiline =“true”/>

您也可以选择完全停用编辑功能:

	<TextInput IsReadOnly =“true”/>

当一个`TextInput`获得焦点时，它通常会召唤设备的屏幕键盘。Fuse提供了许多机制来响应这个事件，其中一个是`WhileKeyboardVisible`:

	<文本值=“某种说明”>
		<WhileKeyboardVisible>
			<Move Y =“ -  1”RelativeTo =“Keyboard”/>
		</ WhileKeyboardVisible>
	</ Text>
	<TextInput />

正如你可以看到，`WhileKeyboardVisible`可以附加到一个任意的元素，你可以做任何你想要的，作为对屏幕键盘占用屏幕上的空间的响应。

只读的“RenderValue”属性给你实际上正在绘制的字符串。例如，如果`IsPassword`为真，它将包含掩码密码(即``••••••``)。

> ### Styling TextInput

`CaretColor`属性允许您更改插入符的颜色:

	<TextInput CaretColor =“#ff0000”/>

如果你想改变选择的颜色，`SelectionColor`使你能做到:

	<TextInput SelectionColor =“#00ffaa”/>

如果希望在TextInput为空时显示某个默认字符串，可以添加占位符值

	<TextInput PlaceholderText =“我的占位符文本”/>

它也可以有一个单独的颜色

	<TextInput PlaceholderText =“我的占位符文本”PlaceholderColor =“#eee”/>


<!-- 
- 虽然重点TODO:我不知道这是什么应该证明
-  WhileEmpty TODO:这不存在，应该吗？它对于实现占位符数据很有好处
- 链接到样式？
- 文本编辑TODO:这是什么？

TODO:不要在这里记录这些，但在触发器和动画师 -->章节

## $(ScrollView)

保险丝有一个`ScrollView`，可以用于导航大于可用大小的内容。

	<ScrollView>
		<Panel 宽度=“2000”高度=“2000”/>
	</ ScrollView>

为了限制`ScrollView`的行为，你可以设置ScrollDirection:

	<ScrollView AllowedScrollDirections =“Horizo​​ntal”>
		<!--  Contents  -->
	</ ScrollView>

`AllowedScrollDirections`的有效设置包括`Horizo​​ntal`，`Both`和`Vertical`(默认)。<!--  TODO:检查这是真的。还有一堆AllowedScrollDirections在枚举中我相信没有使用 -->

> ### $(ScrollingAnimation)

可以基于绝对`ScrollView`位置来动画属性。例如，让我们删除一个顶部窗口，一个`ScrollView`向下滚动:
<!--  TODO:链接到触发器和动画 -->

	<App Theme =“Basic”Background =“#fff”>
		<Panel >
			<Panel 对齐=“顶部”高度=“50”ux:名称=“ledge”>
				<Text Alignment =“Center”TextAlignment =“Center”TextColor =“#fff”Value =“TopLedge”/>
				<Rectangle Fill =“#000”/>
			</ Panel>
			<ScrollView>
				<ScrollingAnimation From =“0”To =“50”>
					<Change ledge.Opacity =“0”/>
				</ ScrollingAnimation>
				<StackPanel>
					<!-- 阻止在scrollview中的顶部 -->
					<Panel 高度=“50”/>
					<!--  ... Content ...  -->
				</ StackPanel>
			</ ScrollView>
		</ Panel>
	</ App>

## $(NativeViewHost)

某些视图仅作为本机组件可用。这些显然是与本机主题的开箱即用，但我们如何可以将它们与基于`GraphicsTheme`的应用程序中的自定义组件一起使用？通过合成它们与NativeViewHost！例如，这里是一个@(WebView)添加了NativeViewHost:

```
<App Theme =“Basic”>
	<Panel >
		<NativeViewHost>
			<WebView Url =“http://interwebs.com”/>
		</ NativeViewHost>
	</ Panel>
</ App>
```

请注意，在NativeViewHost中添加的视图总是呈现在您的其他Graphics主题内容的_in front_，因此您使用深度和渲染顺序需要考虑这一点。(例如:像`<BringToFront>`和`<SendToBack>这样的东西可能不会像你期望的那样。)

要实现相反的目的，在Native主题中添加自定义组件和视图，请继续阅读。

## $(GraphicsView)

`GraphicsView`是@(NativeViewHost)的对应部分，并允许您使用Native主题向@(App)添加Fuse视图。

```
<App Theme =“Native”>
	<StackPanel>
		<Button Text =“我是原生按钮！/>
		<GraphicsView>
			<Button Text =“我是保险丝按钮！/>
		</ GraphicsView>
	</ StackPanel>
</ App>
```

和@(NativeViewHost)一样，注意当混合Native和Fuse视图时，深度排序会有不同的行为。


## $(WebView)

包括Web内容Fuse为Android和iOS提供了一个本地WebView组件。WebView只是本地的，因此需要包含在@(NativeViewHost)中，如果您希望使用它与图形主题。

WebView可以用于通过http协议或通过加载HTML作为字符串来呈现Web内容，并且钩住一些有用的触发器来构建定制的浏览体验，例如@(PageBeginLoading)，@(WhilePageLoading)和@(PageLoaded )。导航触发器像@(GoBack)和@(GoForward)与WebView特定的，如@(重新加载)，@(LoadUrl)和@(LoadHtml)的补充。它也可以用于馈送@(ProgressAnimation)。

@(EvaluateJS)触发器是值得注意的，因为它允许在WebView的上下文中运行任意的JavaScript，并将结果数据反馈到Fuse中:

```XML
<App Theme =“Native”Background =“#333”>
	<JavaScript>
			module.exports = {
				onPageLoaded:function(res){
					console.log(“WebView reached at”+ JSON.parse(res.json).url);
			}}
		};
	</ JavaScript>
	<DockPanel>
		<StatusBarBackground Dock =“Top”/>
		<NativeViewHost>
			<WebView Dock =“Fill”Url =“http://www.google.com”>
				<PageLoaded>
					<EvaluateJS Handler =“{onPageLoaded}”>
						var result = {
							url:document.location.href
						};
						返回结果;
					</ EvaluateJS>
				</ PageLoaded>
			</ WebView>
		</ NativeViewHost>

		<BottomBarBackground Dock =“Bottom”/>
	</ DockPanel>
</ App>
```

WebView还可以通过包装@(HTML)节点或通过@(LoadHtml)触发器操作来提供原始HTML以显示:

`<LoadHtml TargetNode =“myWebView”BaseUrl =“http://my.domain”Source =“{html}”/>`

### JavaScript API
WebView的某些方法通过JavaScript公开

- `goto(“http://myurl.com”)`
- `loadHtml(“my html string”)`
- `loadHtml(“my html string”，“http://my.baseurl.com”)`
- `setBaseUrl(“http://my.baseurl.com”)`

### $(HTML)
`<HTML />`是一个语义实用程序节点，用于使用原始HTML来提供@(WebView)组件或@(LoadHtml)操作:

```XML
<NativeViewHost>
	<WebView>
		<HTML>
			<！[CDATA [
				<h1> Boom！</ h1>
			]]>
		</ HTML>
	</ WebView>
</ NativeViewHost>

<LoadHtml>
	<HTML>
		<！[CDATA [
			<h1> Bang！</ h1>
		]]>
	</ HTML>
</ LoadHtml>
```

## $(MapView)
`MapView`允许您使用平台本地的映射API，在Android上使用Google地图和在iOS上使用苹果地图，向用户展示带注释，互动的全球范围的地图。

`MapView`是一个本地控件，因此需要包含在一个@(NativeViewHost)中以与Graphics主题一起显示。与其他本地移动控件一样，目前没有`MapView'可用于桌面目标。

获取一个`MapView`包含在你的应用程序是简单的:简单地包括节点在你的UX中，你通常将与本机控件:

```XML
<NativeViewHost>
	<MapView />
</ NativeViewHost>
```

要初始化和操作地图摄像机，请使用“纬度”，“经度”，“缩放”，“倾斜”和“轴承”属性，所有这些都是双向绑定。`Zoom`采用平台特定范围内的值，iOS上的米高于地面，Android上为“缩放因子”。

通过使用“Style”属性和“MapStyle”枚举设置渲染样式，可以进一步自定义地图。选项是“Normal”，“Satellite”和“Hybrid”。

> ### $(Android上的地图)

Google地图需要以下内容:

- 在Unoproj中的`Fuse.Maps`的@(Unoproj.Packages:包引用)
-  Google Play图书馆。有关安装说明，请参阅[本指南](/ learn / guides / installing-google-play-services)
- 有效的Google Maps API金钥。按照[Google文件](https://developers.google.com/maps/android/)取得一项设定。

一旦你有你的密钥，它必须添加到你的项目文件，如下所示:

```json
“Android”:{
	“Geo”:{
		“ApiKey”:“your_key_here”
	}}
}}
```

### JavaScript API
MapView的某些方法通过JavaScript公开。

- `setMarkers([{latitude:0，longitude:0，label:“Zero”}])`
- `setLocation(latitude，longitude)`
- `setTilt(0.0)`
- `setZoom(1.0)`
- `setBearing(0.0)`

### $(MapMarker)
要注释地图，您必须使用“MapMarker”节点来装饰它。`MapMarker`节点是包含“纬度”，“经度”和“标签”的简单值对象

```HTML
<NativeViewHost>
	<MapView>
		<MapMarker Label =“Fuse HQ”Latitude =“59.9115573”Longitude =“10.73888”/>
	</ MapView>
</ NativeViewHost>
```

如果你需要从JS动态生成MapMarkers，数据绑定和@(每个)都是你的朋友。在我们编写脚本时，我们可能会勾选“MarkerTapped”事件来检测用户何时选择了标记。

```HTML
<JavaScript>
	var Observable = require(“FuseJS / Observable”);

	exports.markers = Observable({latitude:30.282786，longitude:-97.741736，label:“Austin”});

	exports.onMarkerTapped = function(args)
	{
		console.log(“Marker press:”+ args.label);
	}}
</ JavaScript>

<NativeViewHost>
	<MapView MarkerTapped = {onMarkerTapped}>
		<每个项目= {markers}>
			<MapMarker Latitude =“{latitude}”Longitude =“{longitude}”Label =“{label}”/>
		</ Each>
	</ MapView>
</ NativeViewHost>
```

## $(Element)

这里有一些常见的所有`Element`类型的属性:

### $(HitTestMode)

当与元素交互时，有时期望能够区分哪些元素可以与其交互以及如何进行交互。这通常被称为“命中测试”。在Fuse中，元素如何与用户输入交互可以使用`HitTestMode`设置。

考虑这个代码:

	<Grid ColumnCount =“2”>
		<Rectangle Width =“100”Height =“100”Fill =“#808”>
			<Clicked>
				<DebugAction Message =“点击左侧”/>
			</ Clicked>
		</ Rectangle>
		<Rectangle Width =“100”Height =“100”Fill =“#808”HitTestMode =“None”>
			<Clicked>
				<DebugAction Message =“点击右键”/>
			</ Clicked>
		</ Rectangle>
	</ Grid>

它将布局两个`Rectangle`s并将`Clicked`触发器添加到它们。但是，当点击时，只有左边的一个会输出任何内容，因为在右边的矩形上显式地禁用了命中测试。如果你有视觉元素遮挡下面的元素，你想让下面的元素响应输入，这可能是非常有帮助的。

`HitTestMode`的有效值为:

-  $(HitTestMode.None:None) - 这个元素不会做命中测试
-  $(HitTestMode.LocalBounds:LocalBounds) - 这个元素将根据其大小进行命中测试
-  $(HitTestMode.LocalVisual:LocalVisual) - 这个元素将根据其外观进行命中测试
-  $(HitTestMode.LocalBoundsAndChildren:LocalBoundsAndChildren) - 点击测试将包括元素及其子元素的边界
-  $(HitTestMode.LocalVisualAndChildren:LocalVisualAndChildren) - 命中测试将包括元素及其子元素的外观

### $(ClipToBounds)

通常，当将一个元素布置在另一个元素内部时，内部元素可以自由地居住在父元素之外:

	<Panel 宽度=“100”高度=“100”>
			<Image Margin =“ -  100”File =“Pictures / Picture1.jpg”
				StretchMode =“UniformToFill”/>
	</ Panel>

这个“图像”将显示为300pt宽和高，因为“Panel ”不会将孩子剪裁到其边界。

如果你打算把`Image`剪辑修改为它的父大小，只需在`Panel`中添加$(ClipToBounds)即可:

		<Panel 宽度=“100”高度=“100”ClipToBounds =“true”>
			<Image Margin =“ -  100”File =“Pictures / Picture1.jpg”
				StretchMode =“UniformToFill”/>
		</ Panel>

现在，`Image`不会溢出`Panel`的边界。

### $(Opacity)

你可以使用`Opacity`属性设置对象的透明度。

	<Panel 不透明度=“0.5” />

当“Opacity”设置为0.0时，元素是完全透明的，但仍会响应@(HitTestMode:hit tests)。当“Opacity”设置为1.0时，元素将处于其默认状态。

### $(Layer)

而不是分开布局，元素可以通过使用`Layer`属性作为父母的背景或叠加。

在以下示例中，按钮的文本将显示在矩形上方。

```
<Button Text =“Hello！”>
	<Rectangle Fill =“#931”Layer =“Background”/>
</ Button>
```

`Layer`的有效值为:

- `Layout` _(默认)_  - 元素在布局中像往常一样，在“Background”和“Overlay”之间绘制
- `Background`  - 元素是在* Layout`层后面绘制的，并不影响布局
- `Overlay`  - 元素在* Layout`层的顶部绘制*，并不影响布局

> ### $(ZOffset)

通常，元素按照在UX中显示的顺序绘制。
你可以通过使用`ZOffset`属性(默认为0)来影响这个排序。
具有较高“ZOffset”值的元素绘制在具有较低值的那些值之上。然而，他们仍然在他们的@(层)。

在下面的示例中，蓝色矩形将出现在红色矩形的上方，即使它们的顺序告诉我们。

```
<Panel >
    <Rectangle ZOffset =“1”Color =“Red”/>
    <Rectangle ZOffset =“2”Color =“Blue”/>
</ Panel>
```

* 注意:*儿童的Z顺序完全独立于3D中的Z轴。元素仍然可以转换为任何Z轴位置，旋转到Z维度，或具有实际深度，而与其子Z轴顺序无关。

## 关于$(控制:控制)

在Fuse中，我们使用_control_来描述一个具有语义功能的UI组件，但在视觉外观上可能有很大的变化。

例如，@(Button)定义了某些属性和事件，例如@(Clicked)，但不同的@(Theme:themes)或@(Style:styles)之间的按钮外观和感觉可能会有很大的不同。


## $(效果)

保险丝具有渲染一组可以添加到大多数控件的视觉效果的能力。重要的是要理解，为了使这些工作，你需要在图形模式; 原生主题在它们呈现这些效果的能力方面受到限制。

### $(DropShadow)

向元素添加`DropShadow`:

	<Rectangle Width =“50”Height =“50”Fill =“#808”>
		<DropShadow />
	</ Rectangle>

从一个软的“DropShadow”从上到下:

	<Rectangle Width =“50”Height =“50”Fill =“#808”>
		<DropShadow Angle =“90”Distance =“12”Size =“20”Spread =“0.1”/>
	</ Rectangle>

它也可以用于创造艺术效果，如外部发光:

	<Panel Background =“#000”>
		<Circle Width =“50”Height =“50”Fill =“#808”>
			<DropShadow Distance =“0”Size =“50”Spread =“0.2”Color =“#ff06”/>
		</ Circle>
	</ Panel>

`DropShadow`有以下属性:

- `角度' - 光从哪个方向来:
	-  0  - 右
	-  90  - 顶
	-  180  - 左
	-  270  - 底
- `距离' - 从阴影源的点的距离
- `Size`  - 阴影的大小
- `Spread`  - 阴影如何掉落。越接近0，线性越多。保持这个值低(实验低于1.0)，否则你会得到工件
- `颜色' -  dropshadow应该有哪种颜色。注意，这也支持Alpha通道调整阴影透明度(使用十六进制颜色值的第四个数字声明，例如#FFF0到#FFFF)。

### $(Blur)

模糊元素:

	<Text Value =“Hello there！”>
		<Blur Ra​​dius =“0.9”/>
	</ Text>

注意，虽然'Blur'的`Radius'可以像大多数其他属性一样动画，这可能是一个昂贵的操作，并且应该在设备上测试，以确保它的行为正确。

<!-- 原作者担心在文档中有参数，他不能充满信心地说话。没有这样的参数 -->

### $(Desaturate)

为了“去饱和”一个元素，完全或部分:

	<Image File =“Pictures / Picture1.jpg”>
		<去饱和量=“0.4”/>
	</ Image>

1.0的量将完全“去饱和”元素。

### $(半色调)

添加经典半色调效果:

	<Image File =“Pictures / Picture1.jpg”>
		<半色调/>
	</ Image>

`Halftone`接受:

- `Spacing`  - 点之间的距离(`float`，默认为0.5)
- `平滑度' - 点边缘的硬或软的程度(`float`，默认为2)
- `Intensity`  - 应用了多少效果(`float`，默认为1)
- `DotTint`  - 点的色调量(`float`，默认0.5)
- `PaperTint`  - 纸张的色泽量(`float`，默认值0.2)

### $(Mask)

保险丝允许你用一个图像或`ImageSource`来屏蔽一个元素。

	<Rectangle Width =“50”Height =“50”>
		<Mask File =“Masks / Flower.png”/>
	</ Rectangle>

“Mask”效果接受以下属性:

- `Mode`  - 如何解释掩码源
	- `RGBA`(默认) - 使用源图像的Alpha通道作为掩码，并将掩码中的RGB值与要屏蔽的元素的RGB值相乘
	- “Alpha” - 使用源图像的Alpha通道，而不接触来自遮罩元素的RGB值
	- “灰度” - 使用灰度掩码的颜色分量作为与原始Alpha值的乘法因子
- `File`  - 如果你有一个图片捆​​绑作为项目的一部分
- `Source`  - 如果你有另一个来源`Image`，例如@(HttpImageSource)

如果您使用带有alpha通道的白色图像，“RGBA”和“Alpha”将具有相同的结果。

掩码将总是伸展自身以匹配要掩蔽的元素的大小。
