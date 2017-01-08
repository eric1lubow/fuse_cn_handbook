# $(Creating custom UI components)

## ux:Class

Specifying the `ux:Class` attribute on an UX markup node defines a new class. A class is a reusable component that can be instantiated elsewhere in your project.

`ux:Class` creates a self-contained component that can be used anywhere. It has no acces to `ux:Name`s outside itself. To create a class that has access to `ux:Name`s in a certain context, see `ux:InnerClass`.

### Example

The following code creates a class called `Header`, which inherits from `Text`.

```
<Text ux:Class="Header" FontSize="22" />
```

It can be instantiated, just like any other Fuse element.

```
<Header>Welcome</Header>
```

In this case, the `Header`-class will be a different class from `Text`, although all properties from `Text` will also be available. Note that the class can be defined in a separate file, and often is.

Custom classes can be as complex as you want:

```
<Image ux:Class="BurgerIcon">
	<MultiDensityImageSource>
		<FileImageSource File="Burger.png" Density="1"/>
		<FileImageSource File="Burger.png@2x.png" Density="2"/>
	</MultiDenistyImageSoruce>
</Image>

<!-- ... -->

<Panel>
	<BurgerIcon />
</Panel>
```

They may also contain their own "logic" using @(Triggers:triggers), @(Actions:actions), `JavaScript` and so on.

Note that classes created with ux:Class map to real Uno-classes:

```
<Panel ux:Class="MyPanel"/>
```

would be equal to the following Uno-class:

```
public class MyPanel : Panel { ... }
```

## ux:Property

In most cases, when creating custom classes, we need to be able to define some interface to that class. This can be done through UX using the ux:Property syntax.

__MyButton.ux__:

```
<Panel ux:Class="MyButton" Text="Click me!"
	   Fill="#f00" TextColor="#000" CornerRadius="10">
	<string ux:Property="Text"/>
	<float4 ux:Property="CornerRadius" />
	<Brush ux:Property="Fill" />
	<float4 ux:Property="TextColor"/>
	<Text Alignment="Center" TextColor="{Property this.TextColor}" Value="{Property this.Text}"/>
	<Rectangle Layer="Background" CornerRadius="{Property this.CornerRadius}" Fill="{Property this.Fill}" />
</Panel>
```

Here we have created a new class called MyButton. We expose four properties (Text, CornerRadius, Fill and TextColor). These properties can then be bound to within our ux:Class using the `{Property this.PropName}` syntax.

* Notice that there exists an implicit name called `this` which we are using inside the `{Property this.PropName}` syntax. This name is used to refer to the root element of our `ux:Class`.

When we instantiate our class, we can then access these properties directly:

__MainView.ux__:

```
<App>
	<MyButton CornerRadius="20" Text="MyText"
	          TextColor="#fff" Width="200" Height="50">
		<LinearGradient ux:Binding="Fill">
			<GradientStop Color="#0f0" Offset="0" />
			<GradientStop Color="#00f" Offset="1" />
	    </LinearGradient>
	</MyButton>
</App>
```

You can set properties of reference types (such as `Brush`) by using `ux:Binding`. In the above example, we create a `LinearGradient` and use that as `Fill`.

> ### Observable/Array properties

If you want to pass in an Observable or Array as a property, use the `object` type, as illustrated below.

```
<Panel ux:Class="ListView">
	<object ux:Property="ListItems" />
	<StackPanel>
		<Each Items="{Property this.ListItems}">
			<Panel Padding="10">
				<Text Value="{}" />
			</Panel>
		</Each>
	</StackPanel>
</Panel>

<JavaScript>
	exports.items = ["Foo", "Bar", "Baz"];
</JavaScript>

<ListView ListItems="{items}" />
```

## ux:InnerClass

An inner class is a class that belongs to a certain scope and has access to `ux:Name`s declared in that scope. An inner class can only be used in the scope where it is declared.

### Example

	<App Theme="Basic">
		<Button ux:InnerClass="ActivateButton" ux:Name="btn" Margin="10">
			<Clicked>
				<!-- This class has access to currentActive because it is an inner class of App -->
				<Set highlight.LayoutMaster="btn" />
			</Clicked>
		</Button>

		<StackPanel>
			<ActivateButton Text="Option A" ux:Name="defaultOption"/>
			<ActivateButton Text="Option B" />
			<ActivateButton Text="Option C" />
			<Rectangle Fill="Red" ux:Name="highlight" Margin="-5" LayoutMaster="defaultOption">
				<LayoutAnimation>
					<Move RelativeTo="PositionChange" X="1" Y="1" Duration="0.4" Easing="BackOut" />
				</LayoutAnimation>
			</Rectangle>
		</StackPanel>
	</App>


## ux:Include

You can insert the contents of a UX file into another by using `ux:Include`.

The above example, the `ux:InnerClass can be split into a separate file


	<App Theme="Basic">
		<ux:Include File="ActivateButton.ux" />

		<StackPanel>
			<ActivateButton Text="Option A" ux:Name="defaultOption"/>
			...
		</StackPanel>
	</App>

In `ActivateButton.ux`:

	<Button ux:InnerClass="ActivateButton" ux:Name="btn" Margin="10">
		<Clicked>
			<!-- This class has access to currentActive because it is an inner class of App -->
			<Set highlight.LayoutMaster="btn" />
		</Clicked>
	</Button>

Note that included files should not have `ux:Class` on the root node, that would create two instances of the class in your project. However, included files can specify `ux:InnerClass`. This will create a local version of the inner class in each location it is included.
