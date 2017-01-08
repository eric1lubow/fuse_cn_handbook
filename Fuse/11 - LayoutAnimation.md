＃$（LayoutAnimation）和$（MultiLayoutPanel）

有某种动画值得特别注意。当@（元素）具有某些属性，如@（Width），@（Height）或@（Margin）（统称为“布局属性”）改变或当它在视图树中的位置改变时，布局动画。

计算大型UX文档的布局可能相当昂贵。当使用@（更改）动画制作动画布局属性时，我们运行强制每个框架计算新布局的风险。这很容易导致帧丢失。

保险丝提供了一个整洁的替代品：@（LayoutAnimation）触发器。
例如，我们可以使用@（Set）并使用@（LayoutAnimation）对这个变化作出反应，而不是使用@（变化）来动画化一个@（元素）的@（宽度）。在`LayoutAnimation'中，我们指定元素如何从上一个位置移动/调整大小到新位置。
这里有一个简单的例子：

	<面板>
		<Panel ux：Name =“panel”Width =“50”Height =“50”Background =“Teal”>
			<LayoutAnimation>
				<Resize X =“1”Y =“1”RelativeTo =“LayoutChange”Duration =“0.5”/>
				<Move X =“1”Y =“1”RelativeTo =“LayoutChange”Duration =“0.5”/>
			</ LayoutAnimation>
			<Clicked>
				<Set panel.Width =“200”/>
				<Set panel.Height =“300”/>
			</ Clicked>
		</ Panel>
	</ Panel>

@（已单击）触发器中有两个@（设置）操作。他们设置@（面板）的@（宽度）和@（高度）。
@（Panel）有一个@（LayoutAnimation），它有一个@（调整大小）和@（移动）动画。这两个动画都有自己的“RelativeTo”属性设置为“LayoutChange”。这意味着它们将从计算新布局之前的任何值生成到任何新值。因为改变@（面板）的@（宽度）和@（高度）导致它的位置改变，我们还使用@（移动）动画制作一个平滑的动画。

##动画其他布局属性

@（LayoutAnimation）打开了一个全世界的可能性。我们可以例如使用它来动画化一个@（Element：elements）对齐：

	<面板>
		<Panel ux：Name =“panel”Width =“50”Height =“50”Background =“Teal”Alignment =“Center”>
			<LayoutAnimation>
				<Move X =“1”Y =“1”RelativeTo =“LayoutChange”Duration =“0.5”/>
			</ LayoutAnimation>
			<Clicked>
				<Set panel.Alignment =“BottomRight”/>
			</ Clicked>
		</ Panel>
	</ Panel>

在本示例中，我们使用@（设置）操作将@（面板）的@（对齐）从@（中心）更改为@（BottomRight）。我们使用@（布局动画）和@（移动），以使它移动到新的位置。

## $（MultiLayoutPanel）

$（MultiLayout）允许我们在不同的@（布局：布局）之间移动@（元素：元素）。这允许我们在视觉树中的不同位置之间@（移动）元素，还可以在特定布局之间切换。

## $（占位符）

`Placeholder`元素用于引用视觉树中的其他@（Element：元素）。“占位符”本身是一个@（Element），它可以有自己的布局属性。

当我们想为同一个@（Element）定义两个不同的位置或布局时，使用“占位符”。我们使用占位符`Target`属性来引用我们想要作为plceholder的@（Element）。@（元素）定义本身也必须包装在一个`Placeholder`中。把它当作一个@（元素）从一个“Placeholder”转移到另一个。

	<Placeholder Height =“50”>
		<Rectangle ux：Name =“rectangle1”Fill =“Red”/>
	</ Placeholder>

	<Placeholder Target =“rectangle1”Height =“100”/>

请注意，第二个“占位符”引用@（Rectangle），而不是其他的“占位符”。

要在两个占位符之间切换，我们需要使用@（MultiLayout）。我们可以将`MultiLayout`应用于任何@（Panel）类型或使用@（MultiLayoutPanel）。下面是我们如何将上面的例子应用到@（StackPanel）：

	<StackPanel>
		<MultiLayout LayoutElement =“placeholder2”/>
		<Placeholder Name =“placeholder1”Height =“70”>
			<Rectangle Name =“rectangle1”Fill =“Red”Margin =“25,5”/>
		</ Placeholder>
		<Placeholder Name =“placeholder2”Target =“rectangle1”Height =“100”/>
	</ StackPanel>

通过更改MultiLayout的`LayoutElement`属性，我们可以在“placeholder1”和“placeholder2”之间移动@（Rectangle）。请注意，@（Rectangle）具有其@（Margin）属性设置。因此，当它移动到另一个@（Placeholder）时，该属性仍然存在。然而，@（高度）是在@（占位符：占位符）本身设置的，因此会改变。


这里有一个快速示例，显示了如何将@（Rectangle）从一个@（Panel）移动到另一个。在这个例子中，我们使用一个@（MultilayoutPanel）和一个`DockLayout`。我们把三个@（Panel：panel）放在里面，docket到不同的位置。我们使用@（占位符：占位符）来引用一个@（Rectangle），并在三个@（Panel：panel）之间移动。

	<MultiLayoutPanel ux：Name =“multiLayoutPanel”>
		<DockLayout />
		<Panel ux：Name =“panel1”>
			<Placeholder Width =“50”Height =“50”>
				<Rectangle ux：Name =“rect”Fill =“Teal”>
					<LayoutAnimation>
						<Move X =“1”Y =“1”RelativeTo =“LayoutChange”Duration =“1”/>
						<Resize X =“1”Y =“1”RelativeTo =“LayoutChange”Duration =“1”/>
					</ LayoutAnimation>
				</ Rectangle>
				<Clicked>
					<Set multiLayoutPanel.LayoutElement =“panel2”/>
				</ Clicked>
			</ Placeholder>
		</ Panel>
		<Panel ux：Name =“panel2”Dock =“Right”Width =“200”>
			<Placeholder Target =“rect”>
				<Clicked>
					<Set multiLayoutPanel.LayoutElement =“panel3”/>
				</ Clicked>
			</ Placeholder>
		</ Panel>
		<Panel ux：Name =“panel3”Dock =“Bottom”Height =“100”>
			<Placeholder Target =“rect”>
				<Clicked>
					<Set multiLayoutPanel.LayoutElement =“panel1”/>
				</ Clicked>
			</ Placeholder>
		</ Panel>
	</ MultiLayoutPanel>

查看这些例子以了解使用@（LayoutAnimation）和@（MultiLayout）的灵感。

* [ScrollingAnimation]（https://www.fusetools.com/developers/examples/scrollinganimation）
* [Tabbar using MultiLayout]（https://www.fusetools.com/developers/examples/tabsmultilayout）
