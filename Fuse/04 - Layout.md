# $(Layout)

Fuse has a powerful layout system that works across all platforms and devices, whether you
are building with @(NativeTheme:native) elements or @(BasicTheme:graphics) based elements.

> #### Video introduction to layout

[YOUTUBE x6CueKzTK68]

[YOUTUBE EDjymiMxHSw]

## $(Panels)

Panels can contain child UI elements and lay them out according to layout rules. There are several types of panels, each with different layouting rules.

For a good introductory example to basic layout, take a look [here](https://www.fusetools.com/developers/examples/layoutexample).

## $(Panel)

The most basic type of panel is the `Panel`. Children of a Panel will by default fill its entire space. If a panel contains several children, it simply layers them on top of each other. Combining this behavior with @(Alignment:alignment), @(Margin:margin) and @(Padding:padding) can be quite useful in many situations.

```
<Panel>
	<Text>This...</Text>
	<Text>...will be on top of this</Text>
	<Rectangle Alignment="BottomLeft" Height="20" Width="20" Color="#678" />
</Panel>
```

Note that the element order in a `Panel` is the same as the layer order in popular graphics packages such as Photoshop; the layer that appears first in the UX-file will be layered on top of elements appearing later in the file.

## $(StackPanel)

The StackPanel places its children in a stack. The default layout is a vertical stack, but one can use the `Orientation` property to specify that the stack should be laid out horizontally.

```
<StackPanel Orientation="Horizontal">
	... elements ...
</StackPanel>
```

Additionally, one can use the `ItemSpacing` property to make some space between elements.
It differs from setting `Margin` on each child, in that it only adjusts the space directly *between* the elements, not the space around each of them.
The following example shows three Panels in a `StackPanel`, spaced using the `ItemSpacing` property:

```
<StackPanel ItemSpacing="20">
	<Panel Height="100" Background="Red"/>
	<Panel Height="100" Background="Green"/>
	<Panel Height="100" Background="Blue"/>
</StackPanel>
```

## $(Grid)

The Grid places its children in a grid formation. The rows and columns can be specified explicitly by the @(Rows) and @(Columns) properties, or implicitly by assigning the @(RowCount) and @(ColumnCount) properties.

### $(RowCount) and $(ColumnCount)

If all that is needed is a grid of equally sized rows and columns, one can simply state the number of rows and columns using the RowCount and ColumnCount properties.

```
<Grid RowCount="4" ColumnCount="2"/>
```

### $(Rows) and $(Columns)

More fine grained control of how the rows and column sizes are calculated can be achieved with the `Rows` and `Columns` properties.
These properties are assigned to a comma separated list of values which can take on a few different forms.
The values can either be absolute, relative or automatic.

Example of a Grid with 3 rows of size 10, 10 and 50 points.

```
<Grid Rows="10,10,50"/>
```

Example of a Grid with 3 rows where the first two each occupy 20% of the available space, and the last one occupies 60%:

```
<Grid Rows="1*,1*,3*"/>
```

The rows sizes here are calculated by first summing all the values (1+1+3 = 5).
Then we divide our value by the total (1/5 = 20%, 1/5 = 20%, 3/5 = 60%).

The following grid has 3 rows where the first two rows gets the size of its largest child and the last row takes up the remaining space:

```
<Grid Rows="auto,auto,1*"/>
```

### $(Grid.Row:Placing elements in a Grid) $(Grid.Column:)

By default, elements are placed in the grid by the order they appear in the UX, from left to right, top to bottom.
One can however explicitly specify per element which grid cell they should be placed in using the `Row` and `Column` properties like so:

```
<Grid RowCount="1" ColumnCount="2">
	<Rectangle Row="0" Column="1" Color="Red"/>
	<Rectangle Row="0" Column="0" Color="Blue"/>
</Grid>
```

One can also specify the number of rows and columns an elements occupies using the $(RowSpan) and $(ColumnSpan) properties like so:

```
<Grid RowCount="2" ColumnCount="2">
	<Rectangle ColumnSpan="2" RowSpan="2" Color="Red"/>
</Grid>
```

## $(WrapPanel)

The `WrapPanel` lays out its children one after the other and wraps around whenever it reaches the end. One can specify which direction the elements are laid out in by assigning the $(FlowDirection) property. FlowDirection can either be `LeftToRight` or `RightToLeft`.

The following WrapPanel lays out its children horizontally from right to left.

```
<WrapPanel FlowDirection="RightToLeft">
	<Each Count="10">
		<Rectangle Margin="5" Width="100" Height="100" Color="Blue"/>
	</Each>
</WrapPanel>
```

The Orientation property can be used to make a vertical `WrapPanel` like so:

```
<WrapPanel Orientation="Vertical">
	<Each Count="10">
		<Rectangle Margin="5" Width="100" Height="100" Color="Blue"/>
	</Each>
</WrapPanel>
```

You can also specify the maximum area the WrapPanel will allocate an element by using the `ItemWidth` and `ItemHeight` properties.

<!-- TODO: Illustration -->

## $(DockPanel)

The DockPanel lays out its children by docking them to the different sides, one after the other. One can specify which side per element by using the $(Dock) property like so:

```
<DockPanel>
	<Rectangle Dock="Left"/>
</DockPanel>
```

The Dock property can be assigned to be either `Left`, `Right`, `Top`, `Bottom` or `Fill` (which is the default).

```
<DockPanel>
	<Rectangle ux:Class="MyRectangle" MinWidth="100" MinHeight="200" />
	<MyRectangle Color="Red" Dock="Left"/>
	<MyRectangle Color="Green" Dock="Top"/>
	<MyRectangle Color="Blue" Dock="Right"/>
	<MyRectangle Color="Yellow" Dock="Bottom"/>
	<MyRectangle Color="Teal" />
</DockPanel>
```

> ## $(Viewbox)

To make contents stretch to fit an area, you can use `Viewbox`:

```
<Viewbox>
	<Rectangle Color="#808" Width="200" Height="100" />
</Viewbox>
```

This will maintain its aspect ratio of 2:1 while stretching the `Rectangle` to be the size of the `Viewbox`.

You can set which directions you want the content to scale by setting the `StretchDirection`-property:

- `Both` - Allow both up- and downscaling
- `UpOnly` - Only upscale contents
- `DownOnly` - Only downscale contents

Note that any other setting than `DownOnly` might create pixel artifacts, as the `Viewbox` performs a bitmap stretch of its contents.

You can also set the @(StretchMode) for the contents, which defaults to `Uniform`.

## $(Layout rules)

In the previous sections, we've been talking about different types of @(Panel:panels) and how they perform layout on their children.
Under the hood, these are regular @(Panel:Panels) that have been assigned a `Layout`.

For instance, @(StackPanel) is essentially a @(Panel) whose `Layout` is set to an instance of $(StackLayout). This means that the following snippets are equivalent of each
```
<StackPanel>
	...
</StackPanel>
```

```
<Panel>
	<StackLayout />
	...
</Panel>
```

As shown in the last snippet, layout rules are automatically bound to the `Layout` property so you don't have to reference them by name.

Note that *not all layout rules have corresponding @(Panel) types*. These are documented below.

> ### $(ColumnLayout)

`ColumnLayout` lays out the children of a @(Panel) by dividing them into columns. The number of columns can be adjusted with the `ColumnCount` property.
It attempts to keep the height of the columns somewhat balanced by placing children in the shortest column available after laying out their previous siblings.

```
<Panel>
	<Panel ux:Class="MyPanel" Background="#2c3e50" Margin="5" />
	<Text ux:Class="MyText" TextColor="#fff" Alignment="Center" FontSize="20" />

	<ColumnLayout ColumnCount="3" />

	<MyPanel Height="200">
		<MyText>1</MyText>
	</MyPanel>

	<MyPanel Height="100">
		<MyText>2</MyText>
	</MyPanel>

	<MyPanel Height="300">
		<MyText>3</MyText>
	</MyPanel>

	<MyPanel Height="150">
		<MyText>4</MyText>
	</MyPanel>

	<MyPanel Height="200">
		<MyText>5</MyText>
	</MyPanel>
</Panel>
```

In this example we have divided five @(Panel:panels) of different heights into three columns.
The animation below shows the result of this, and what would happen if we were to resize the third @(Panel:panel).

[VIDEO ../media/ColumnLayout.mp4]

You can also lay out the children in rows instead of columns by setting the `Orientation` property to `Horizontal`.

A full example that uses `ColumnLayout` can be found [here](https://www.fusetools.com/examples/gallery).

> ### $(CircleLayout)

`CircleLayout` lays out the children of a @(Panel) in a circular pattern. It can be tweaked with the following properties:

- $(CircleLayout.StartAngleDegrees:StartAngleDegrees) - The angle in degrees where the first child should be placed.
- $(CircleLayout.EndAngleDegrees:EndAngleDegrees) - The angle in degrees where the last child should be placed.
- $(CircleLayout.Radius:Radius) - The distance the items are placed from the center of the @(Panel). The range is from 0 to 1 where 0 is in the center and 1 is at the edges of the @(Panel).
- $(CircleLayout.ItemSpacingDegrees:ItemSpacingDegrees) - The number of degrees between the placement of each child.

The following example places 26 red circles in a half circle formation:

```
<Panel Margin="20" Color="#ddd">
    <CircleLayout StartAngleDegrees="-180" EndAngleDegrees="0"/>

	<Each Count="26">
		<Circle Width="25" Color="#f00" Height="25"/>
	</Each>
</Panel>
```

## Element Layout

<!-- TODO: Link to video -->

Whenever a @(Panel) performs layout on its children, it has to ask each element about its layout properties.

Layout properties are assigned per element to control such things as the elements @(Width), @(Height), @(Margin) and @(Padding).
If an element doesn't specify these things, the panel performing layout on them will handle it.

<!-- TODO: Available space, @(points) (vs @(pixels)). -->

### $(Alignment)

When elements are positioned in a panel they may not require all of the space available to them. For example, a vertical stack panel will be as wide as its largest element, leaving extra space for the smaller elements. Elements can either be aligned within this space, or stretched to fill it.

We can align elements to the sides of its container by the @(Alignment) property.
In the following example, we have a @(Panel) which is 500x500 points in size, which contains a @(Rectangle) which is only 50x50 points in size. By setting alignment to BottomRight, this rectangle will be positioned by the bottom right corner of the panel.

```
<Panel>
	<Rectangle Width="50" Height="50" Alignment="BottomRight"/>
</Panel>
```

Alignment can be assigned to any one of the following values:
- $(Left)
- $(HorizontalCenter)
- $(Right)
- $(Top)
- $(VerticalCenter)
- $(Bottom)
- $(TopLeft)
- $(TopCenter)
- $(TopRight)
- $(CenterLeft)
- $(Center)
- $(CenterRight)
- $(BottomLeft)
- $(BottomCenter)
- $(BottomRight)

If you don't assign an `Alignment` explicitly, the default alignment will be to stretch the control as much as the parent control requests. For a normal `Panel`, this means that the child control will try to fill the parent `Panel`, but other containing controls might ask the children to behave differently.

### $(Visibility)

The `Visibility` property can be used to `Show`, `Hide` or `Collapse` elements. It can be set to one of the following values:
- $(Visible) - The default state where the element is visible.
- $(Hidden) - The element is not drawn, but it still takes up space.
- $(Collapsed) - The element is not visible and is will not take up any space.

In the following example, only two of the @(Rectangle:rectangles) are visible. The second @(Rectangle) is collapsed, so it's not taking up any space at all. The third @(Rectangle) is hidden, so it takes up space, but is not visible. The last @(Rectangle) has no `Visibility` set, so it defaults to being visible.

```
<StackPanel>
	<Rectangle Visibility="Visible" Color="Red" Height="50"/>
	<Rectangle Visibility="Collapsed" Color="Green" Height="50"/>
	<Rectangle Visibility="Hidden" Color="Blue" Height="50"/>
	<Rectangle Color="Yellow" Height="50"/>
</StackPanel>
```

### $(Width) and $(Height)

You can combine @(Alignment) and other layout properties with `Width` and `Height`, which will set the size of the @(Element) in the desired @(Units:Unit).

### $(Margin) and $(Padding)

Each element can specify the amount of space between it and its parent or surrounding siblings by using its @(Margin) property.

Each element can also specify how much space should be between its borders and any element inside it by using the @(Padding) property.

Both @(Margin) and @(Padding) are specified using a comma separated list of 4 values which defines the left, top, right and bottom values in that order.
Here is an example of a @(Rectangle) with a margin of 20 to the left, 30 to the top, 50 to the right and 10 to the bottom:

```
<Rectangle Margin="20,30,50,10"/>
```

Here is a rectangle where all sides have the same margin:

```
<Rectangle Margin="50"/>
```

This rectangle has a margin of 50 for its left and right, and 20 for its top and bottom:

```
<Rectangle Margin="50,20"/>
```

### $(Units)

There are multiple ways of specifying values on layout properties, points, percent and pixels.
The following properties support units
- @(Width)
- @(Height)
- MinWidth
- MinHeight
- MaxWidth
- MaxHeight
- @(Offset)
- @(Anchor)

* Note that the column and row properties of @(Grid) use their own @(Rows:system).

### Specifying units in $(points)

When setting an elements Width to a number like so `<Element Width="50"/>`, the element will become 50 points wide.

Points are different from pixels in that they will represent multiple pixels on high density displays. This way, using points will give a you a consistent look on all different screen densities.

### Specifying units in $(Percentage:percent) (%)

Specifying values using the percent sign means that this value should be a certain percentage of available space.
For example, if we place an @(Element) inside a @(Panel) which is 500 points wide, and set the elements Width property to be 50%, we get an element which is 250 points wide.

### Specifying units in $(Pixels:pixels)

We can specify values in pixels using the px suffix like so:

```
<Rectangle Width="200px"/>
```

This rectangle will be exactly 200 pixels wide, which means it will be smaller when the screen density is high.

### $(Anchor) and $(Offset)

When a @(Panel) places its children, it assumes that the "center" of that element is in the middle. However, if we want the element to be placed as if its "center" was along its left side, we could use the @(Anchor) property like so:

```
<Rectangle Anchor="0%,50%"/>
```

This puts the elements anchor in the middle of its left edge.


### $(TransformOrigin)

The `TransformOrigin` property specifies the origin of transformation used by
transformation behaviors and animators, such as @(Move), @(Scale), @(Rotation), @(Scaling), etc.

It accepts the following values:

- $(TransformOrigin.Center:Center) _(default)_ – Transforms originate at the center of the element
- $(TransformOrigin.TopLeft:TopLeft) – Transforms originate at the top left corner of the element
- $(TransformOrigin.Anchor:Anchor) – Transforms originate around the point specified by the @(Anchor) property.
- $(TransformOrigin.HorizontalBoxCenter:HorizontalBoxCenter) – Simulates the effect of the element being the front-facing side of a cube in 3D space, using the width of the element for determining the depth of the cube. Without the element being in a @(Viewport) this will have no illusion of depth, effectively rendering it useless.
- $(TransformOrigin.VerticalBoxCenter:VerticalBoxCenter) – The same as @(TransformOrigin.HorizontalBoxCenter), except that it uses the height of the element for determining depth.

> #### Using Anchor to set an arbitrary transform origin

```
<Rectangle X="50%" Y="50%" Anchor="0%,50%" TransformOrigin="Anchor"/>
```

The above example creates a rectangle who’s left edge center is placed in the middle of the parent. When rotated it will then be rotated around this point.


### $(LayoutMaster)

You can make an element inherit the layout of another using the `LayoutMaster` property.

```
<StackPanel>
	<Rectangle ux:Name="master" Height="150" Color="#f00a" />
	<Rectangle LayoutMaster="master" Color="#00fa" />
</StackPanel>
```

The above example will result in two overlapping @(Rectangle:Rectangles).

When the `LayoutMaster` of an element is changed, any @(LayoutAnimation:LayoutAnimations) on that element will be activated.

```
<Rectangle ux:Name="selection" LayoutMaster="target1">
	<Stroke Width="2" Brush="#3498db" Offset="2" />
	<LayoutAnimation>
		<Move RelativeTo="WorldPositionChange" X="1" Y="1" Duration="0.3" Easing="CubicInOut" />
		<Resize RelativeTo="SizeChange" X="1" Y="1" Duration="0.3" Easing="CubicInOut" />
	</LayoutAnimation>
</Rectangle>

<StackPanel>
	<Panel ux:Name="target1" Margin="10" Height="50" Background="#eee">
		<Text Alignment="Center">Click me</Text>
		<Clicked>
			<Set selection.LayoutMaster="target1" />
		</Clicked>
	</Panel>
	<Panel ux:Name="target2" Width="150" Height="100" Background="#eee">
		<Text Alignment="Center">Me too!</Text>
		<Clicked>
			<Set selection.LayoutMaster="target2" />
		</Clicked>
	</Panel>
</StackPanel>
```

The above example illustrates how `LayoutMaster` can be used to implement a moving selection rectangle. It consists of two panels that, when clicked, animate the `selection` @(Rectangle) to inherit their size and position.


### $(Absolute positioning) $(X:) $(Y:)

If we want to give our elements an explicit position, we can assign their `X` and `Y` properties. The `X` property will move the element relative to the left side of its container, while the `Y` property moves it relative to the top.

Be aware that absolute positioning elements should generally be avoided in favor of using layout rules. This is because when real data is used, the absolute values used might no longer be meaningful.

> ## $(Viewport)

The `Viewport` element allows you to perform 3D transformations with perspective projection.
In its current state, `Viewport` is somewhat limited.
It has to be placed directly in your `<App>` with the rest of your app inside a child @(Panel), and cannot handle node-local perspectives.

The `Perspective` property controls how far away the camera is from the `Z = 0` plane (where everything is drawn by default), in points.

	<App Theme="Basic">
		<Viewport Perspective="400">
			<Panel>
				<Rectangle Width="200" Height="200" Background="#2ecc71">
					<Clicked>
						<Rotate DegreesX="360" Duration="1.5" Easing="QuadraticInOut" DurationBack="0" />
					</Clicked>
				</Rectangle>
			</Panel>
		</Viewport>
	</App>

## Status bars

iOS and Android devices usually has a status bar aligned to the top of the screen which shows status information from the operating system like battery and network information. This status bar might or might not be visible while our app is running. We also may or may not be able to draw behind it. Android also often has an on-screen bar to handle navigation, and most devices show an on-screen keyboard when it accepts user input.

> ### Video introduction to OS elements

[YOUTUBE S_syTU44jzw]

### $(StatusBarBackground)

The @(StatusBarBackground) element is used to compensate for the status bar. It will always have the same size as the status bar across all platforms and devices. We can use a @(DockPanel) to dock this element on the top of our app and "offset" the rest of our content so it fits within the visible parts of our screen.

```
<App>
	<DockPanel>
		<StatusBarBackground Dock="Top"/>
		<Panel>
			... our apps content ...
		</Panel>
	</DockPanel>
</App>
```

### $(BottomBarBackground)

The BottomBarBackground element is quite similar to the @(StatusBarBackground) in that it takes on the same size as certain OS specific elements. The BottomBarBackground will take on the same size as the keyboard (whenever it is visible). Certain Android devices have their home button on the screen instead of as a physical button. The BottomBarBackground will also take this into account when sizing itself.

Here is how we can make sure our content is never covered by the keyboard or home button:

```
<App>
	<DockPanel>
		<Panel>
			... our apps content ...
		</Panel>
		<BottomBarBackground Dock="Bottom"/>
	</DockPanel>
</App>
```

You also have the option to not take the size of the keyboard into account:

	<BottomBarBackground IncludesKeyboard="false" />

> ### $(ClientPanel)

`ClientPanel` is a @(DockPanel) with both a @(StatusBarBackground) and a @(BottomBarBackground) docked to its top and bottom edges, respectively. It is included for convenience.
