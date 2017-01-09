# 导入资产

保险丝正在努力，使容易从伟大的第三方设计工具到Fuse(进口)资产和设计。本节包含有关如何使用它们的信息。

## From $(Sketch)(experimental)

素描是一个伟大的矢量设计工具为Mac。如果您尚未尝试，请<a href="https://www.sketchapp.com">查看</a>！

保险丝可以:

* 将资源从`.sketch`文件导入到切片图像，字体和现成的UX标签中。
* 如果在初始导入后更改草图文件，则实时更新资源。


> *注意*:导入`.sketch`文件仅适用于Mac OSX(如Sketch本身)，目前是实验性的。

> ###最佳做法

当在`.sketch`文件中使用Fuse时，请记住以下几点:


* 保险丝标签名称将对应于Sketch中的图层名称，因此请保持图层名称有意义
* 使用与目标设备匹配的画板尺寸。例如，如果为iPhone 6设计，请在Sketch中使用iPhone 6画板模板。这确保在导出时，资产变为正确的大小
* Sk​​etch中的画板可以(但不一定)对应于Fuse中的页面


### 导入

要使用草图导入器，您首先需要通过Bohemian Coding安装SketchTool。您可以<a href="https://bohemiancoding.com/sketch/tool/">在此下载</a>。
解压缩zip并运行安装脚本。

要导入草图文档，请说“MyDesign.sketch”，首先将文件复制到保险丝项目文件夹中。

然后使用终端类型:

	保险丝导入MyDesign.sketch

导入可能需要一段时间，具体取决于文档中的资产数量。

> ###使用拖放进行导入

作为使用终端的替代方法，您可以将`.sketch`文件拖放到保险丝预览窗口中。这将把草图文件复制到项目的根文件夹(如果它还没有)，然后做同样的事情。任何现有的具有相同名称的`.sketch`文件将被覆盖。

### 资源库 

当导入完成 - 如果一切顺利 - 保险丝已经生成一个名为`MyDesign.sketch.ux`的文件在你的`.sketch`文件旁边。在文本编辑器中打开它，看看里面有什么。

此文件是一个资源库，其中包含Fuse能够从Sketch文档中提取的所有资源。它会自动包含在您的项目中，您可以开始使用资源。

本身，资源库不执行任何操作，也不会添加到导出的应用程序大小。只有从其他UX文件引用的资源才会包含在最终应用程序中，其余的将被删除。


### 图片

草图文档中的图像将被表示为扩展@(Image)的类(`ux:Class`)，并使用@(MultiDensityImageSource)指向已渲染的资源。

类的名称对应于Sketch中的图层名称。如果你的文件叫做“MyDesign.sketch”，它包含一个名为“Screen1”的画板，它包含一个名为SomeGroup的图层组和一个名为SomeLayer的图层，您可以像这样创建这个图片的实例:

	<MyDesign.Screen1.SomeGroup.SomeLayer />

此标记是@(图像)，因此它支持与@(图像)相同的属性，例如@(对齐)，@(边距)，@(StretchMode)和@(Scale9Margin)。

这是可能的，因为在资源库文件(`.sketch.ux`)中，你现在有一个生成的类看起来像:

	<Image ux:Class =“MyDesign.Screen1.SomeGroup.SomeLayer”>
	  <MultiDensityImageSource>
	   <FileImageSource File =“MyDesign.sketch-assets/MyDesign.Screen1.SomeGroup.SomeLayer@1x.png”Density =“1.0”/>
	   <FileImageSource File =“MyDesign.sketch-assets/MyDesign.Screen1.SomeGroup.SomeLayer@2x.png”Density =“2.0”/>
	  </ MultiDensityImageSource>
	</ Image>

图像文件放在`.sketch`文件中的`MyDesign.sketch-assets /`文件夹中。

### 字体

保险丝将自动从您的系统中提取所需的字体，并将它们放在`-sassch`文件夹旁边的`-assets /`文件夹中。它还将它们声明为资源库中的全局资源。

在资源库中，它们将如下所示:

	<Font File =“MyDesign.sketch-assets / HelveticaNeue.ttf”ux:Global =“HelveticaNeue”/>

这意味着你可以这样使用它们:

	<Text Font =“HelveticaNeue”> Hello，world！</ Text>



### 自动生成的应用布局

保险丝还可以基于导入的资源库生成应用布局，所以你得到正确的保险丝屏幕看起来完全像你的素描设计。这包括@Text元素以及形状和图片，并且可以是创建响应式应用布局的良好起点。

通过为`fuse import`指定`--app`或(`--overwrite-app`)选项来实现。例:

	保险丝导入MyDesign.sketch --app MyDesign.ux

这将生成`MyDesign.ux`，它将在您的草图文件中包含一个@App标记，一个@PageControl和一个@Page。

保险丝将尽可能准确地从您的草图文件中重现布局。这当前意味着绝对定位所有元素以精确匹配草图文件。

> Fuse正在使用优化UX代码的工具来自动检测布局规则等。检查更新！

> ### $(sketch-update:资产的实时更新)

如果在原始导入后再次运行`fuse import`，资源库将被更新(覆盖)最新的资产，并且项目中使用的任何资源将在所有预览会话中自动更新。

类的名称保持不变，只要Sketch图层尚未重命名。

> ###图像密度

您可以通过将选项传递给import命令来控制导入Sketch文档时Fuse生成的映像密度。例如:

	保险丝导入MyDesign.sketch --1x --1.5x --2x

将分别导入1.0,1.5和2.0密度的所有图像资产。如果你没有指定任何东西，保险丝默认渲染1.0和2.0密度。

#### 资产别名

有时，当您导入`.sketch`文件时，您的资产可能会获得很多长而不实用的名称。这是因为没有人认为文件将被导入到保险丝，当它被制作，因此没有正确命名图层。

你通常会得到如下的东西:

	<Image ux:Class =“activity.Activity ___ 6.Rectangle_91 ___ Bitmap_2”>
		<MultiDensityImageSource>
			<FileImageSource File =“activity.sketch-assets / Activity ___ 6.Rectangle_91___Bitmap_2@1x.png”Density =“1”/>
			<FileImageSource File =“activity.sketch-assets / Activity ___ 6.Rectangle_91___Bitmap_2@2x.png”Density =“2”/>
		</ MultiDensityImageSource>
	</ Image>

这将使你的UX代码看起来不太好，当你开始使用这些。同时，我们不想重命名类，因为我们失去了到.sketch文件的链接，并且不能再获得@(sketch-update:live updates)。

为了解决这个问题，让自己成为一个额外的UX文件，例如叫做“AssetAliases.ux”。例如，您可以使用“Panel”作为根标记。

在上面的例子中，我们知道这个位图实际上是整个屏幕的背景。因此我们可以给它一个更合理的名称，如`ActivityBackground`。

在`AssetAliases.ux`中:

	<面板>
		<activity.Activity ___ 6.Rectangle_91 ___ Bitmap_2 ux:Class =“ActivityBackground”/>
	</ Panel>

您想要别名的所有其他资产只能添加到此列表。

然后，您可以在实际的应用代码中使用这个更好的标签:

	<ActivityBackground />
