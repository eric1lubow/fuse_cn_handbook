# $(Trigger:Triggers) and $(Animators)

Triggers provide a declarative way of creating animations with Fuse. At their most basic, triggers represent events that are triggered in response to user and/or program input. @(Trigger:Triggers) can contain @(Animators) and @(Actions) which are used to animate and manipulate elements as well as interacting with @(JavaScript).

@(Trigger)s are behaviors that live on a `Node` or UI @(Element), listen to events and perform animations and @(actions) in response.

For example, here is a @(Panel) with a @(WhilePressed) trigger causing the panel to rotate 90 degrees with a bouncy animation.

```
<Panel>
	<WhilePressed>
		<Rotate Degrees="90" Duration="0.5" Easing="BounceInOut"/>
	</WhilePressed>
</Panel>
```

> ## Video introduction to Triggers and Animators

[YOUTUBE bT1npBvXEzw]

<!-- TODO:
* Explain how triggers are a timeline, plays forwards/backwards, applies/unapplies -->

## $(Rest state) and deviation

The default layout and configuration of UX markup elements is called the rest state.
Triggers define deviations from this rest state.
Each trigger knows how to "un-apply" its own animation to return to the rest state, even if interrupted mid-animation.
This is great because it means animation is completely separated from the logical state of your program, greatly reducing the complexity of dealing with combined animation on
multiple devices, screen sizes, with real data and real user input.

### $(BackwardAnimation:Separate forward and backward animations)

Some advanced @(Trigger) animations allow separate forward and backward animations.
To do this, put the backward animation inside a `TriggerAnimation`, and bind it do the `BackwardAnimation` binding on the parent (forward) animation. The below example uses `BackwardAnimation` to animate with a different easing when the animation is going back to its idle state.

```
<WhilePressed>
	<Rotate Degrees="90" Duration="0.5" />
	<TriggerAnimation ux:Binding="BackwardAnimation" >
		<Rotate Degrees="90" Duration="1" Easing="BounceOut" />
	</TriggerAnimation>
</WhilePressed>
```

**Note:** Animations in a `BackwardAnimation` are automatically right-aligned.

## Animators

Animators are used to specify which and how @(Element:elements) are to be animated when a @(Trigger:trigger) is triggered.
There are three pairs of properties which are important for controlling the exact result of an animation.

### $(Duration)/$(DurationBack)

Animations can have different behavior when animating forward and backward. When a trigger is activated, the animation is said to play forward. When the trigger is deactivated, the animation is played backward. Duration is used to set the duration for the animation. One can set a different duration for the backward animation by using the `DurationBack` property.

When there are multiple @(Animators:animators) inside a trigger, the total duration of the trigger will be the longest duration among the animators.

In the following example, the total duration of the @(WhileTrue) trigger will be 3 seconds. If we wanted the animations to happen one after the other, we could use @(Delay).

```
<WhileTrue>
	<Move X="10" Duration="2"/>
	<Rotate Degrees="90" Duration="3"/>
</WhileTrue>
```

### $(Delay)/$(DelayBack)

Setting the `Delay` property results in the actual animation being delayed by that amount of seconds. `DelayBack` is used to set a different delay on the backward animation. The total duration of the animation becomes the delay + the duration. The following @(Change:change) animator has a total duration of 7 seconds. It waits 5 seconds after being activated and then animates its target element over 2 seconds.

```
<Change Delay="5" Duration="2" someElement.Height="100"/>
```

### $(Easing)/$(EasingBack)

Fuse comes with a standard set of predefined easing curves. Easing curves are used to control how an animation progresses over time. The default easing is set to `Linear`. With linear easing, the animation progresses at the same speed over its entire duration. This usually appears quite unnatural and fake. To gain a more natural feel, we can change the easing to `QuadraticInOut`, like so:

```
<Change Easing="QuadraticInOut" Duration="2" someElement.Property="SomeValue"/>
```

This animator will progress slowly in the beginning, faster in the middle, and then slow again in the end.

The following easing curves are defined:

- $(Linear)
- $(Quadratic)
- $(Cubic)
- $(Quartic)
- $(Quintic)
- $(Sinusoidal)
- $(Exponential)
- $(Circular)
- $(Elastic)
- $(Back)
- $(Bounce)

They each have three different versions: [easing]In, [easing]Out and [easing]InOut.
Assign easing to an animator like so:

```
<Move Easing="CubicIn" X="5" Y="10" Duration="0.5"/>
<Scale Easing="BounceOut" Factor="1.5" Duration="0.2"/>
<Rotate Easing="BounceOut" Degrees="10" Duration="0.4"/>
```

<!-- - MixOp
TODO/AUTH: Write about mixop-->

### $(Change)

`Change` temporarily changes the value of a property while its containing trigger is active. To permanently change a value, use the @(Set) animator.

The `Target` property refers to the property that we intend to animate.
The `Value` property is the value of the result of the animation.

* Note: You can specify @(Units) with `Value` as long as the unit matches the original unit of the `Target`.

Because the task of setting a target property and value is so common, UX has a special syntax for this. Instead of

```
<Change Target="target.Property" Value="Value"/>
```

one can do the following:

```
<Change target.Property="Value"/>
```

The `Change` animator can be used to animate almost any property.

```
<Panel ux:Name="somePanel" Color="Red" />

<Change somePanel.Opacity="0"/>
<Change somePanel.Color="Blue" Duration="0.3"/>
<Change somePanel.Visibility="Collapsed"/>
```

One can also animate such properties as `Width`, `Height` and `Margin`, but because these properties might affect the entire layout of your UI, this can end up being quite costly in terms of performance. There are usually more effective ways to do animations that interact with layout. Check out @(LayoutAnimation) and @(MultiLayoutPanel) for some inspiration.

> ### $(Cycle)

`Cycle` continuously animates a property between two values at a given frequency.

	<Panel>
		<Translation ux:Name="someTranslation" />
		<WhilePressed>
			<Cycle Target="someTranslation.X" Low="-20" High="20" Frequency="2" />
		</WhilePressed>
	</Panel>

You may also specify a `Duration` to control the length of the animation.

### Transform animators

Transform animators apply a transformation to an element. They do not affect layout, guaranteeing fast animations.

All transform animators accept an *optional* `Target` property, indicating which element the transform should be applied to.

#### $(Move)

The `Move` animator is used to move an element. `Move` does not affect layout, so the element will just get an offset from its actual location.

```
<Move X="50" />
```

When triggered, this will move the element by 50 points in the X direction.

You may want for an element to move relative to its own size or some other elements size.
To achieve this we can use the @(RelativeTo) property, for instance:

```
<Move X="0.5" RelativeTo="Size" />
```

When triggered, this will move the element by half of its own width in the X-direction.

$(RelativeTo) can be set to the following values:

- `Local`(default): Moves the set amount of points in the X and/or Y direction.
- `Size`: Moves the set amount times the size of the element. So X="1" moves the element by its entire width in the X direction.
- `ParentSize`: Same as `Size` but uses the elements parents size instead.
- `PositionChange`: Used in response to a @(LayoutAnimation) to move the element by the amount of change in position within it's parent.
- `WorldPositionChange`: Used in response to a @(LayoutAnimation) to move the element by the amount of change in position relative to the entire display.
- `Keyboard`: Moves the element relative to the size of the keyboard.

The `RelativeNode` property lets you move an element relative to another. In that case, you may use the following `RelativeTo` modes:

- `Size`: Works the same way it would without `RelativeNode`, but measures the size of the `RelativeNode` instead.
- `ParentSize`: Same as `Size` but measures the `RelativeNode`'s parent size instead.
- `PositionOffset`: Moves the element to be in the same position as the element specified by `RelativeNode`.
  The offset is measured as the difference in `ActualPosition` between the two elements.
- `TransformOriginOffset`: Works like `PositionOffset`, but instead measures the difference in `TransformOrigin`.

Move corresponds to adding a @(Translation) on the element and using @(Change) to animate its X and Y values. The following two examples give the same result.

```
<Panel>
	<WhilePressed>
		<Move X="100" Duration="0.2"/>
	</WhilePressed>
</Panel>
```

```
<Panel>
	<Translation ux:Name="someTranslation"/>Transition
	<WhilePressed>
		<Change someTranslation.X="100" Duration="0.2"/>
	</WhilePressed>
</Panel>
```

#### $(Scale)

`Scale` works in the same way as @(Move) except that it scales the element. Note that scale doesn't actually change the elements size. This means that the rest of the UI layout wont be affected and the animation is guaranteed to be fast.

You can scale an element uniformly along all axes by using the `Factor` property. Alternatively, you can also scale on a per-axis basis using `Vector` or `X`, `Y`, and `Z`.

```
<Scale Factor="2" Duration="0.4"/>
```

`Scale` can be used relative to something using the `RelativeTo` property. The two choices are:

* `SizeChange` - scales relative to the change in size of the element specified by the `RelativeNode` property.
* `SizeFactor` - scales with a factor relative to another element, specified by `RelativeNode`. A factor of `1` would make it the same size as the `RelativeNode`, while a factor of `0.5` would make it half the size, and so on.

#### $(Rotate)

`Rotate` rotates an Element and is equal to adding a @(Rotation) and animating it with a @(Change).

```
<Rotate Degrees="90" Duration="0.5"/>
```

Using the `Degrees` property rotates the element around the Z-axis. Alternatively, you can use `DegreesX`, `DegreesY`, and `DegreesZ` to rotate the element around a specific axis.

#### $(Resize)

When used in concert with @(LayoutAnimation), `Resize` allows you to animate the size of an element:

```
<Resize RelativeTo="SizeChange" Duration="0.5" Easing="CircularInOut" />
```

`Resize` has two options for `RelativeTo`:

* `SizeChange` - resizes relative to the change in size during a @(LayoutAnimation).
* `Size` - resizes relative to the size of the element specified by `RelativeNode`.

#### $(Spin)

`Spin` continuously rotates an element, given a `Frequency` measured in full rotations per second.

	<Panel>
		<WhilePressed>
			<Spin Frequency="2" />
		</WhilePressed>
	</Panel>

As with @(Cycle), you may also specify a `Duration` to control the length of the animation.

#### $(Skew)

`Skew` allows you to animate a skew transform on an element.

```
<Skew DegreesX="30" Duration="0.4"/>
```

You can use `DegreesX` and `DegreesY` to skew on one axis, or `DegreesXY` and `XY` to skew on both axes in degrees or radians, respectively.

### $(Attractor)

The `Attractor` is used to give a more natural movement to animations. It acts as an intermediary between an animator and its target. An `Attractor` will continuously animate its target towards its `Value` using a simple form of physics simulation. We can combine this behavior with animation by animating the attractor's `Value` property.

```
<Panel ux:Name="somePanel">
	<Translation ux:Name="someTranslation"/>
	<Attractor ux:Name="someAttractor" Target="someTranslation.X"/>
	<WhilePressed>
		<Change someAttractor.Value="100"/>
	</WhilePressed>

</Panel>
```

### $(Keyframe:Keyframes)

There are situations where we don't simply want to animate from point a to point b. For the cases where we want to specify several steps for an animation, we can use @(Keyframe:keyframes).

```
<Move RelativeTo="ParentSize">
	<Keyframe X="10" Time="0.5"/>
	<Keyframe X="15" Time="1"/>
	<Keyframe X="5" Time="2"/>
</Move>
```

This @(Move) animator will first animate X to 10 over 0.5 second, then from 10 to 15 over 0.5 second. Finally, it will go from an X of 15 to 5 over 1 second.
Here is an example of using @(Keyframe:keyframes) with a @(Change) animator:

	<Page>
		<SolidColor ux:Name="background" Color="#f00"/>
		<ActivatingAnimation>
			<Change Target="background.Color">
				<Keyframe Value="#0f0" TimeDelta="0.25"/>
				<Keyframe Value="#f00" TimeDelta="0.25"/>
				<Keyframe Value="#ff0" TimeDelta="0.25"/>
				<Keyframe Value="#0ff" TimeDelta="0.25"/>
			</Change>
		</ActivatingAnimation>
	</Page>

This time we use `TimeDelta` instead of time. With `TimeDelta` we can specify time as a relative term instead of absolute. This means that the order of the @(Keyframe:keyframes) matter, but it lets us reason about the keyframes in terms of their duration instead of their absolute time on the timeline.
<!-- TODO: Interpolation -->

### $(Nothing)

All animations for a `Trigger` share a common timeline, which ends when the last animation has completed. In some rare cases, you may want to artificially extend the timeline. This can be done using `Nothing`. Logically, it is a blank animation with a set length, forcing the length of the timeline to be at least the duration of the `Nothing`.

```
<Nothing Duration="1" />
```

## Transforms

All @(Element:elements) can have transforms applied to them in order to move, scale or rotate.
It is worth mentioning that the order of these transforms affects the order of when they are applied to the element, and therefore can lead to different results.


```
<Panel Width="100" Height="50">
	<Translation X="100"/>
	<Rotation Degrees="45"/>
</Panel>
```

```
<Panel Width="100" Height="50">
	<Rotation Degrees="45"/>
	<Translation X="100"/>
</Panel>
```

The two examples have quite different results. In the first case, the panel is first moved 100 points to the right and then rotated 45 degrees. In the other case, the panel is first rotated 45 degrees. The positive X direction is now 45 degrees downward, and so our panel ends up being moved toward the bottom right.

### $(Translation)

`Translation` moves the element in the specified X, Y, and Z direction. The following example shows a @(Rectangle) which is moved 100 points in the X-direction and 50 points in the Y-direction.

```
<Rectangle Width="50" Height="50">
	<Translation X="100" Y="50"/>
</Rectangle>
```

You can set the translation value for `Translation` with:

 * `X` or `Y`, to separately set the X and Y translations
 * `XY`, to set the translation of both axes at once
 * `Z`, to set the translation in the Z axis.

The coordinates default to being relative to the elements original position(`TranslationModes.Local`), but this can be changed using the property `RelativeTo`. Further, you can make the transform relative to another element by using `RelativeNode`.

### $(Scaling)

`Scaling` enlarges or shrinks the element by the factor specified. The following example will make the @(Rectangle) twice as big as the original size:

```
<Rectangle Width="100" Height="100">
	<Scaling Factor="2"/>
</Rectangle>
```

`Scaling` can be utilized using:

 * `Factor`, to set an universal scale on all axes
 * `Vector`, to set the scale of all three axes at once
 * `X`, `Y`, and `Z`, for control of individual axes.

<!-- TODO: Document Vector -->

### $(Rotation)

`Rotation` rotates the element by the degrees specified. Here is an example of a rectangle which is rotated by 90 degrees.

```
<Rectangle Width="100" Height="50">
	<Rotation Degrees="90"/>
</Rectangle>
```

You can rotate an element using:

 * `Degrees`, controlling rotation around the Z axis
 * `DegreesX`, `DegreesY`, `DegreesZ`, giving you individual control of all 3 axes.
 * `EulerAngle` and `EulerAngleDegrees`, letting you set the Euler angles of the element in radians or degrees, respectively.

### $(Shear)

The `Shear` animator can be used to perform a shear mapping on an element. One can use `DegreesX` and `DegreesY` to set the shear on one axis, or `Degrees` and `Vector` to set the shear in both the X and Y plane, using degrees or radians.

## $(Actions)

Triggers can contain actions too, which are one-off events that fire at a particular point in the trigger's timeline.

Note that actions, contrary to @(Animators:animators) are not reversible. This means it is not necessarily possible to return to the @(rest state) if the trigger is reversed.

Like @(Animators:animators), `Actions` can have a `Delay`. This specifies a number of seconds from the @(Trigger) is activated to the `Action` is fired. However, unlike `Animators`, they also have a property called `AtProgress` which can be set to a value between 0 and 1. It has a similar function as `Delay`, but is instead relative to the full @(Duration) of the @(Trigger). Setting `AtProgress` to 0, means the action is fired as soon as the @(Trigger) is activated. Setting it to 0.5 means it is fired half way through and so on.

### $(Action.AtProgress:AtProgress)

`Actions` also has a property called `AtProgress` which can be set to a value between 0 and 1. It has a similar function as `Delay`, but is instead relative to the full @(Duration) of the @(Trigger). Setting `AtProgress` to 0, means the action is fired as soon as the @(Trigger) is activated. Setting it to 0.5 means it is fired half way through and so on.

### $(DebugAction)

`DebugAction` lets you print a message to the Monitor/terminal when activated.
It is the equivalent of calling `debug_log` from Uno or JavaScript.

```
<Button>
	<Clicked>
		<DebugAction Message="Clicked!" />
	</Clicked>
</Button>
```

### $(Set)

Permanently changes the value of a property. If you want to just change it temporarily, use @(Change). When using `Set` on a property, the value will not be reverted back when the containing trigger is deactivated. In the following example we change the color of a rectangle by setting the value of its `SolidColor` @(Element). Multiple activations of the @(Clicked) trigger won't have any additional effect.

```
<Rectangle ux:Name="rect" Color="#00f" />
<Clicked>
	<Set rect.Color="#f00"/>
</Clicked>
```

Set may also be invoked using its `Target` and `Value` properties. The following lines are equivalent:

```
<Set Target="rect.Color" Value="#f00" />
<Set rect.Color="#f00" />
```

### $(Callback)

The `Callback` action is used to call a JavaScript function (see @(Data Binding)) when a trigger is activated.

```
<JavaScript>
	var someJSFunction = function () {
		console.log("some function called");
	}
	module.exports = { someJSFunction: someJSFunction };
</JavaScript>
<Rectangle Width="100" Height="50" Alignment="Center" Fill="Red">
	<WhilePressed>
		<Callback Handler="{someJSFunction}"/>
	</WhilePressed>
</Rectangle>
```

### $(GoForward)

Tells a @(Navigation:navigation context) or a @(WebView) to step forward in its navigation history.

	<GoForward TargetNode="myNavigation" />

	<GoForward TargetNode="myWebView" />

For more information about `GoForward` in the context of @(Navigation:navigation), see @(Controlling navigation).


### $(GoBack)

Tells a @(Navigation:navigation context) or a @(WebView) to step backward in its navigation history.

	<GoBack TargetNode="myNavigation" />

	<GoBack TargetNode="myWebView" />

For more information about `GoBack` in the context of @(Navigation:navigation), see @(Controlling navigation).


### $(Toggle)

`Toggle` is used to toggle a boolean value between `true` and `false`. If inside a @(Switch) it will toggle the value of the @(Switch). `Toggle` can also be used to activate/deactive @(WhileTrue) and @(WhileFalse) triggers like so:

```
<WhileTrue ux:Name="trueTrigger">
	...
</WhileTrue>
<Panel>
	<Clicked>
		<Toggle Target="trueTrigger"/>
	</Clicked>
</Panel>
```

### $(Pulse)

`Pulse` is used to momentarily trigger a `WhileTrue`, `WhileFalse` or `Timeline`.

	<Button Text="Pulse">
		<WhileTrue ux:Name="pulseMe" Value="false">
			<Scale Factor="1.5" Duration="0.2" />
		</WhileTrue>

		<Clicked>
			<Pulse Target="pulseMe" />
		</Clicked>
	</Button>

### $(BringIntoView)

The `BringIntoView` action is used together with the @(ScrollView) control. By setting its `TargetNode` property, we can instruct the @(ScrollView) to go to a position, making that `Node` visible.

This example shows how to use `BringIntoView` to make a @(ScrollView) automatically scroll between the top and the bottom by clicking a button:

```
<App Theme="Basic" ClearColor="#eeeeeeff">
	<DockPanel>
		<StatusBarBackground DockPanel.Dock="Top" />
		<ScrollView ClipToBounds="true">
			<StackPanel>
				<Panel ux:Name="panel1" Height="80" Background="#F44336" />
				<Panel Height="200" Background="#ddd"/>
				<Panel Height="200" Background="#bbb"/>
				<Panel Height="200" Background="#999"/>
				<Panel Height="200" Background="#777"/>
				<Panel Height="200" Background="#444"/>
				<Panel ux:Name="panel2" Height="80" Background="#3949AB" />
			</StackPanel>
		</ScrollView>
		<StackPanel Dock="Bottom" Height="60" Orientation="Horizontal" Alignment="Center">
			<Button Text="To the top">
				<Clicked>
					<BringIntoView TargetNode="panel1" />
				</Clicked>
			</Button>
			<Button Text="To the bottom">
				<Clicked>
					<BringIntoView TargetNode="panel2" />
				</Clicked>
			</Button>
		</StackPanel>
	</DockPanel>
</App>
```

### $(BringToFront)

UX is generally structured such that elements defined higher up in the document are drawn on top.
This is done to mimic popular graphics packages such as Photoshop's layer behaviour.
With `BringToFront`, one can bring the element specified by the `Target` property to the front of its siblings.

* Note: `BringToFront` only makes the element the frontmost in its parent, not the entire UX tree.

```
<DockPanel>
	<Panel Background="#F00" />
	<Panel ux:Name="bluePanel" Background="#00F" />
	<Button Text="Blue to front!" Dock="Bottom">
		<Clicked>
			<BringToFront Target="bluePanel" />
		</Clicked>
	</Button>
</DockPanel>
```

### $(TransitionLayout)

The `TransitionLayout` action lets you create a temporary layout change.
This can be used to do visual layout transitions without needing actual layout changes.

It has no noticeable effect on its own, and needs to be combined with a @(LayoutAnimation).
The @(LayoutAnimation) will in turn be triggered by this action.

	<DockPanel>
		<Panel Dock="Top" Height="20" ux:Name="originElement" />

		<Button Height="100" Dock="Bottom" Text="Transition!">
			<LayoutAnimation>
				<Move X="1" Y="1" RelativeTo="WorldPositionChange" Duration="1" />
				<Resize X="1" Y="1" RelativeTo="SizeChange" Duration="1" />
			</LayoutAnimation>
			<Clicked>
				<TransitionLayout From="originElement" />
			</Clicked>
		</Button>
	</DockPanel>

When clicked, the @(Button) in this example will perform a transition over 1 second from the position and size of `originElement` (top edge of the @(DockPanel)) to its actual position and size (bottom edge of the @(DockPanel)).

### $(NavigateToggle)

Toggles a `Navigation`. This is currently only supported in @(EdgeNavigation), and will do nothing if used on another type of navigation.

Used on an `EdgeNavigation`, it will navigate to and from a @(Panel) with `EdgeNavigation.Edge` set, specified by using the `Target` property.

## $(Gestures)

Following are triggers which react to pointer gestures.

### $(Clicked)

`Clicked` is activated in response to a click @(Gestures:gesture). What constitutes a click-event can be platform specific, but usually means that the pointer was pressed and released within the bounds of the containing element.

```
<Panel Width="50" Height="50">
	<Clicked>
		<Scale Factor="2" Duration="0.2"/>
	</Clicked>
</Panel>
```

### $(Tapped)

The `Tapped`-trigger is quite similar to the @(Clicked)-trigger. Where a click just means that the pointer has to be pressed and released on the element, a tap means that the pointer has to be released within a certain time after the pointer is pressed.

### $(DoubleClicked)

`DoubleClicked` is activated when the element has been @(Clicked) twice within a certain timeframe.

### $(DoubleTapped)

As with @(DoubleClicked), `DoubleTapped` is activated when the element has been @(Tapped) twice within a certain timeframe.

### $(WhilePressed)

`WhilePressed` is active while its containing element is being pressed and the pointer is inside its bounds.

```
<Panel Width="50" Height="50">
	<WhilePressed>
		<Scale Factor="2" Duration="0.2"/>
	</WhilePressed>
</Panel>
```

### $(WhileHovering)

`WhileHovering` is active while the pointer is within the bounds if its containing @(Element).

* Note: `WhileHovering` only has value when the device supports a hovering pointer, like the mouse pointer on desktop machines. For most smart phones this trigger won't have much value.

### $(SwipeGesture:Swipe gestures)

We use the `SwipeGesture` behavior when we want an element to handle swipe gestures.

```
<Panel>
	<SwipeGesture Direction="Right" Length="100" Type="Active" />
</Panel>
```

- `Direction="Right"` specifies that this should be a swipe-to-the-right gesture.
- `Length="100"` means that the swipe has a length of 100 points in the specified `Direction`.
- `Type="Active"` indicates that this should be a two-state swipe gesture that toggles between an inactive/active state.

`SwipeGesture` accepts the following properties:

- `Direction` specifies the direction of the swipe.
	Possible values are `Left`, `Up`, `Right` and `Down`.
- `Edge` can be used instead of `Direction`, and specifies an edge of the element that can be swiped from.
	Possible values are `Left`, `Top`, `Right` and `Bottom`.
- `HitSize` only applies when `Edge` is used, and specifies how far from the edge we can start swiping for it to be recognized.
- `Length` specifies, in points, how far we can swipe in the specified `Direction`.
- `LengthNode` can be used instead of `Length`. It references another element that should be measured to determine the length of the swipe.
- `Type`
	- `Active` indicates that swiping should toggle between an inactive/active state.
	- `Simple` indicates that swiping should invoke a single, momentary action.

The `SwipeGesture` behavior has no effect on its own. We need to apply our own triggers and animators to respond to the gesture.
In the following sub-sections we will go through different triggers and actions we can use to respond to and control @(SwipeGesture:SwipeGestures).

* Note: Since you have the possibility to attach multiple swipe gestures on the same element, the related triggers need to know which one you are referring to.
We are therefore required to set the `Source` property of all swipe-related triggers to the @(SwipeGesture) it should respond to.

#### $(SwipingAnimation)

`SwipingAnimation` performs animation in response to an element being swiped.
The most common use case is to move the element along with the pointer.

	<Panel Width="100" Height="100" Background="#000">
		<SwipeGesture ux:Name="swipe" Direction="Right" Length="200" />
		<SwipingAnimation Source="swipe">
			<Move X="200" />
		</SwipingAnimation>
	</Panel>

Instead of using a fixed length for the swipe we may also determine it from the size of another element.
This is achieved using the `LengthNode` property of @(SwipeGesture), and in this case the `RelativeNode` property of @(Move) as well.

	<Panel ux:Name="parentContainer" Margin="40">
		<Panel Width="60" Height="60" Background="#000" Alignment="Left">
			<SwipeGesture ux:Name="swipe" Direction="Right" Type="Active" LengthNode="parentContainer" />
			<SwipingAnimation Source="swipe">
				<Move X="1" RelativeTo="Size" RelativeNode="parentContainer" />
			</SwipingAnimation>
		</Panel>
	</Panel>

#### $(Swiped)

`Swiped` is a pulse trigger that is invoked when a swipe has occurred.

	<Panel Width="100" Height="100">
		<SwipeGesture ux:Name="swipe" Direction="Up" Length="50" Type="Simple" />
		<Swiped Source="swipe">
			<Scale Factor="1.5" Duration="0.4" DurationBack="0.2" />
		</Swiped>
	</Panel>

By default, `Swiped` will only trigger when swiping to the primary swipe direction (when it enters the active state).
For instance, if the @(SwipeGesture) has `Direction="Left"` it only triggers on a `Left` swipe and ignores the matching closing swipe.
We can control this behavior by setting the `How` property to either `ToActive` (default), `ToInactive` or `ToEither`.

#### $(WhileSwipeActive)

`WhileSwipeActive` is active whenever a @(SwipeGesture) is active (when the user has swiped it "open").

	<Panel Width="100" Height="100">
		<SwipeGesture ux:Name="swipe" Direction="Up" Length="50" Type="Simple" />
		<WhileSwipeActive Source="swipe">
			<Scale Factor="1.5" Duration="0.4" DurationBack="0.2" />
		</WhileSwipeActive>
	</Panel>

#### $(SetSwipeActive) and $(ToggleSwipeActive)

We can control the state of `Active` type @(SwipeGesture:SwipeGestures) by using the `SetSwipeActive` and `ToggleSwipeActive` actions.

	<SwipeGesture ux:Name="swipe" Direction="Right" Length="100" Type="Active" />
	...
	<StackPanel>
		<Button Text="Close">
			<Clicked>
				<SetSwipeActive Target="swipe" Value="false" />
			</Clicked>
		</Button>

		<Button Text="Toggle">
			<Clicked>
				<ToggleSwipeActive Target="swipe" />
			</Clicked>
		</Button>
	</StackPanel>

If we wish to bypass the animation, `SetSwipeActive` lets us do that by setting `Bypass="true"`.

## Data triggers

These triggers react to data changes, either from data binding or from the control context.

### $(WhileTrue)

`WhileTrue` is active while its `Value` property is `True` and inactive while it's false.

### $(WhileFalse)

`WhileFalse` is active while its `Value` property is `False` and inactive while it's true.

<!-- ### WhileFailed
TODO: I dont know what it does -->

## Native triggers

### $(Platform triggers)

Sometimes it can be necessary with platform specific code. This can be done by using the platform triggers `Android` and `iOS`.

In the following example, we place a red @(Panel) if on an Android device and a blue @(Panel) if on an iOS device:

```
<Panel>
	<Android>
		<Panel Background="#f00" Alignment="Center" Width="150" Height="150"/>
	</Android>
	<iOS>
		<Panel Background="#00f" Alignment="Center" Width="150" Height="150"/>
	</iOS>
</Panel>
```


### $(WhileKeyboardVisible)

`WhileKeyboardVisible` is active whenever the on-screen keyboard is visible.

<!-- TODO: Example -->

### $(TextInputActionTriggered)

`TextInputActionTriggered` is triggered when the user presses the return key while editing a @(TextInput).
The following example demonstrates how you can respond to this by releasing focus from the input, thereby hiding the on-screen keyboard.

```
<TextInput>
	<TextInputActionTriggered>
		<ReleaseFocus />
	</TextInputActionTriggered>
</TextInput>
```

### $(OnBackButton)

This trigger fires when the user presses either a physical or emulated back button on their device. The following code will flash the screen blue when the back button is pressed:

```
<Panel>
	<Rectangle ux:Name="rect" Layer="Background" Color="#F00" />
	<OnBackButton>
		<Change rect.Color="#00F" Duration="0.2" />
	</OnBackButton>
</Panel>
```

> ### $(OnKeyPress)

`OnKeyPress` is triggered when the key specified by the property `Key` is pressed.
The following example will flash the screen blue when the "menu" button (which is present on some older android devices) is pressed.

```
<Panel>
	<Rectangle ux:Name="rect" Layer="Background" Color="#F00" />
	<OnKeyPress Key="MenuButton">
		<Change rect.Color="#00F" Duration="0.2" />
	</OnKeyPress>
</Panel>
```

For a complete list of supported keys, check out the [Key enum](https://www.fusetools.com/learn/uno/api/uno/platform/key) list.

## Native actions

Fuse comes with a set of actions that invoke OS-specific behavior, such as dialing a phone number or vibrating the device.

### $(Fuse.Launcher)

The Fuse.Launcher package lets you start other activities like email, maps and web browsers.

* Note: Make sure you add Fuse.Launcher to your @(Project structure:.unoproj) file.

#### $(LaunchUri)

Requests the operating system to launch a [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier).

	<LaunchUri Uri="https://www.fusetools.com" />

An URI can be anything from a URL to a custom URI scheme registered by an app. The underlying OS is responsible for handling the request.
For instance, [here is a list of common URI schemes](http://www.iana.org/assignments/uri-schemes) that are registered with The Internet Assigned Numbers Authority (IANA).

#### $(LaunchEmail)

Launches the default email app, and starts composing a message.

	<LaunchEmail To="contact@fusetools.com" />

`LaunchEmail` accepts the following properties:

- `To` – The email address(es) of the recipient
- `CarbonCopy` – The email address(es) of whom to send a carbon copy
- `BlindCarbonCopy` – The email address(es) of whom to send a blind carbon copy
- `Subject` – The subject of the email
- `Message` – The body text of the email

#### $(LaunchMaps)

Launches the default maps app, given a latitude/longitude pair.

	<LaunchMaps Latitude="35.673813" Longitude="-36.780810" />

#### $(Call)

Issues a phone call to the specified number.

	<Call Number="123 45 678" />

### $(Vibrate)

Vibrates the device for a given number of seconds. Note: iOS will not honor any specified duration, as this is not an option in the native iOS API.

	<Vibrate Duration="0.8" />


## $(State groups)

State groups allow you to define completely custom states, with custom events.

### $(State)

A `State` consists of a set of @(Animators:animators) inside a `State` object. It acts as a normal @(Trigger), but is activated by its containing @(StateGroup).

```
<State>
	<Rotate Degrees="200" Duration="0.4"/>
	<Move X="10" Duration="0.4"/>
</State>
```

### $(StateGroup)

A `StateGroup` is used to group a set of @(State:states) together and switch between them.
`StateGroup` has an `Active` property, which is used to assign which @(State) is currently active in that group.

Here is an example of how to use a `StateGroup` to switch the color of a @(Rectangle) between three states:

```
<StackPanel>
	<Panel Width="100" Height="100">
		<SolidColor ux:Name="someColor"/>
	</Panel>
	<StateGroup ux:Name="stateGroup">
		<State ux:Name="redState">
			<Change someColor.Color="#f00" Duration="0.2"/>
		</State>
		<State ux:Name="blueState">
			<Change someColor.Color="#00f" Duration="0.2"/>
		</State>
		<State ux:Name="greenState">
			<Change someColor.Color="#0f0" Duration="0.2"/>
		</State>
	</StateGroup>
	<Grid ColumnCount="3">
		<Button Text="Red">
			<Clicked>
				<Set stateGroup.Active="redState"/>
			</Clicked>
		</Button>
		<Button Text="Blue">
			<Clicked>
				<Set stateGroup.Active="blueState"/>
			</Clicked>
		</Button>
		<Button Text="Green">
			<Clicked>
				<Set stateGroup.Active="greenState"/>
			</Clicked>
		</Button>
	</Grid>
</StackPanel>
```

One can also specify the `Transition`, which can be either `Exclusive` or `Parallel`.
`Exclusive` means that each state will have to be fully deactivated before the next state becomes active.
`Parallel` means that as one state deactivates, the next one will become active and whatever properties they animate will be interpolated between them.

## $(User events)

User events are intended for sending messages between components of your application.
They may be sent and received from UX, Uno, and JavaScript.

* Make sure you also check out the documentation on using @(UserEvents-js:FuseJS UserEvents from JavaScript).

### $(UserEvent)

User events are attached to the node they are declared in, and only that node and its children can raise and handle the event.

	<App>
		<UserEvent Name="MyEvent"/>
		...

This creates an event with the name `MyEvent`.
By putting this `UserEvent` in `App` we are essentially making it an app-wide event, since every child of App can raise and respond to this event.

* Note: Make sure you add "Fuse.UserEvents" to your .unoproj file.

### $(RaiseUserEvent:Raising user events from UX)

To raise a @(UserEvent:user event) from UX, use the `RaiseUserEvent` action.

	<Button>
		<Clicked>
			<RaiseUserEvent Name="MyEvent" />
		</Clicked>
	</Button>

### $(UserEventArg:Passing arguments)

A $(UserEvent:user event) may also include a number of arguments that can be read from either JavaScript or Uno.

	<RaiseUserEvent Name="MyEvent">
		<UserEventArg Name="message" StringValue="Hello from UX!" />
	</RaiseUserEvent>

`UserEventArg` accepts `IntValue`, `FloatValue`, `StringValue` or `BoolValue`.

### $(OnUserEvent:Responding to user events)

To respond to a @(UserEvent:user event), use the `OnUserEvent` trigger.

	<OnUserEvent Name="MyEvent">
		...
	</OnUserEvent>

By default, `OnUserEvent` will only listen for events that are declared in one of its ancestor nodes.
If you want to listen for events coming from anywhere, set the `Filter` property to `Global`.

`OnUserEvent` also lets you attach a JavaScript handler to the event.

	<OnUserEvent Name="MyEvent" Handler="{myHandler}" />

The handler function can then read the @(UserEventArg:arguments) that were passed with the event.

```javascript
function myHandler(args) {
	console.log(args.message);
}

module.exports = { myHandler: myHandler }
```

## $(Viewport triggers)

These triggers react to changes in the geometry of the screen.

### $(WhileWindowLandscape)

The `WhileWindowLandscape` trigger is active whenever the app's viewport width is larger than it's height. The following example changes the App's background color depending on its orientation:

```
<App ux:Name="app" Theme="Basic" Background="#FFF">
     <WhileWindowLandscape>
         <Change app.ClearColor="0,0,1,1" Duration="1" />
     </WhileWindowLandscape>
</App>
```

### $(WhileWindowPortrait)

The `WhileWindowPortrait` trigger is active whenever the app's viewport height is larger than, or equal to, the width.

### $(WhileWindowSize)

The `WhileWindowSize` trigger has three float2-properties that control its behavior:

 * `GreaterThan`
 * `LessThan`
 * `EqualTo`


These properties have to be larger than `0,0`, and are conditions the app viewport has to conform to in order for the trigger to be active. The following is an example that changes the background color of an app if the viewport size is greater than a certain size:

```
<App Theme="Basic">
  <Panel>
     <SolidColor ux:Name="coloredPanel" Color="#F00" />
     <WhileWindowSize GreaterThan="400,400">
       <Change coloredPanel.Color="#0F0" Duration=".5"/>
     </WhileWindowSize>
  </Panel>
</App>
```

## $(Control triggers)

### $(WhileEnabled)

The `WhileEnabled` trigger is active whenever its containing @(Element:elements) `IsEnabled` property is set to `True`.

```
<Panel  Width="50" Height="50" Background="Red" >
	<WhileEnabled>
		<Rotate Degrees="45" Duration="0.5"/>
	</WhileEnabled>
</Panel>
```

### $(WhileDisabled)

The `WhileDisabled` trigger is active whenever the `IsEnabled` property of its containing @(Element:element) is set to `False`.

## $(Focus triggers and actions)

### $(WhileFocused)

The `WhileFocused` trigger is active whenever its containing @(Element:element) is in focus.

### $(WhileNotFocused)

The opposite to @(WhileFocused), active whenever its containing @(Element:element) is _not_ in focus.

### $(WhileFocusWithin)

`WhileFocusWithin` is active whenever a child of its containing @(Element:element) is in focus.

### $(GiveFocus)

`GiveFocus` is an action that gives focus to its containing @(Element:element) when activated.
It also accepts a `Target` property, which specifies which element to give focus to.

### $(ReleaseFocus)

When activated, `ReleaseFocus` removes focus from the currently focused @(Element:element).

## WebView-specific triggers & actions

### $(PageBeginLoading)

Triggers once the @(WebView) begins loading new content.

```
<NativeViewHost>
	<WebView Url="http://fusetools.com">
		<PageBeginLoading>
			<DebugAction Message="Page began to load!"/>
		</PageBeginLoading>
	</WebView>
</NativeViewHost>
```

### $(WhilePageLoading)

Becomes active when a @(WebView) Url changes, and stays active until it has completed loading content.

### $(PageLoaded)

Triggers once the @(WebView) has completed loading content from its current Url.

```
<NativeViewHost>
	<WebView Url="http://fusetools.com">
		<PageLoaded>
			<DebugAction Message="Arrived at page!"/>
		</PageLoaded>
	</WebView>
</NativeViewHost>
```

### $(Reload)

The `Reload` action lets you tell a given WebView to reload its current location.

`<Reload TargetNode="myWebView" />`

### $(LoadUrl)

The `LoadUrl` action lets you tell a given WebView to navigate to a location.

`<LoadUrl TargetNode="myWebView" Url="http://mypage.com" />`

### $(LoadHtml)

The `LoadHtml` action lets you feed a given WebView with raw HTML to display when triggered, alongside a base url to set the HTML's context. This is either done by pointing the action's `Source` attribute to a databound HTML string, or by wrapping an @(HTML) and putting the HTML in the node body, like this:

```XML
<LoadHtml TargetNode="myWebView" BaseUrl="http://my.domain">
	<HTML>
		<![CDATA[
			<h1>Boom!</h1>
		]]>
	</HTML>
</LoadHtml>
```

> ### $(EvaluateJS)

The WebView offers limited execution of arbitrary JavaScript in the currently loaded web environment. This is done with the `<EvaluateJS/>` action. Let's look at a simplified example.

```XML
<EvaluateJS Handler="{onPageLoaded}">
	var result = {
		url : document.location.href
	};
	return result;
</EvaluateJS>
```

Note the use of a `return` statement in the script body. Implementations of JavaScript evaluation APIs generally act like a JavaScript [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop), and when evaluating multiple lines of JS the result of the last statement of the script becomes the returned value. For instance, "1+5" is completely valid JS when evaluated and returns the expected value of "6".

This can result in odd-feeling JS, where referencing an object becomes an implicit return statement, whereas an explicit return is not allowed.

```JavaScript
var result = {};
result.foo = "bar";
result; // using return here is invalid JS
```

To make this feel better and allow return, we currently inject the user's JS in the form of a function:

```JavaScript
(function() { USER_JS })();
```

#### Reading the result value

When we evaluate the JavaScript we are currently bound by platform restrictions in a key way: String is the only allowed return value type on Android, our lowest common denominator. Android allows for parity with iOS as of API level 19, which denies us good backwards compatibility. For now we must rely on the comparatively ancient [addJavaScriptInterface](http://developer.android.com/reference/android/webkit/WebView.html#addJavascriptInterface(java.lang.Object, java.lang.String)) API for backwards compatibility.

What this means is that any return value passed from the evaluated script must by necessity be returned as JSON and parsed back from it on the Fuse end. Even if all you want is the result of some arithmetic, you'd still receive it as a string and require a cast. Instead of forcing you to routinely `return JSON.stringify(foo)` from your own JS we handle this by *always* wrapping your JS in JSON.stringify before evaluation:

```JavaScript
JSON.stringify( (function() { USER_JS })() );
```

The returned JSON string here is then put into a result object with the `json` key. This is for clarity, so you never forget that the data you are receiving is a JSON string that you will need to parse.

```XML
<JavaScript>
	module.exports = {
		onPageLoaded : function(result)
		{
			var url = JSON.parse(result.json).url;
		}
	};
</JavaScript>
```

Note that of course return is optional. If you don't return anything from your evaluated JS the return value of the expression will simply be "null".


## $(Special Animations)

In Fuse terminology, *animations* are a certain category of triggers that animate in response to a higher level interpretation
of input, events and logical state changes.

Animation triggers are designed to solve quite specific animation problems that would otherwise be hard to pull off using the simpler triggers.
The flipside is that these triggers can be somewhat hard to understand and use, so make sure you read the docs and study examples before spending
too much time scratching your head.

### LayoutAnimation

The `LayoutAnimation` is triggered in response to a layout change. A layout change happens whenever an element gets a new layout by the layout system. `LayoutAnimation` is commonly used together with @(MultiLayoutPanel) for some pretty interesting animations. See @(LayoutAnimation) for more in depth documentation.

<!-- TODO: consider linking to external article here-->

### $(AddingAnimation)

<!-- TODO: Link to examples -->

`AddingAnimation` is triggered whenever the element is added to the visual tree. Adding animation is by default a backward animation, meaning it will animate from progress 1 back to 0.

<!-- TODO: Example -->

### $(RemovingAnimation)

`RemovingAnimation` is similar to @(AddingAnimation), but is triggered whenever an @(Element) is being removed from the visual tree.

`RemovingAnimation` is similar to @(AddingAnimation) but is triggered whenever the element is removed from its parent. `RemovingAnimation` progresses normally from 0 to 1 over the specified @(Duration).

In the following example, a rectangle will move in from the right side by the width of its parent container over one second when it is added to the visual tree by the @(Switch). It will move out to the left by the same distance when it is removed.

```
<App Theme="Basic" ClearColor="#eeeeeeff">
	<StackPanel Background="#ddd" Margin="10">
		<Switch>
			<WhileTrue>
				<Change whileTrue.Value="true"/>
			</WhileTrue>
		</Switch>
		<WhileTrue ux:Name="whileTrue">
			<Panel Height="50" Width="50" Background="Red">
				<AddingAnimation>
					<Move RelativeTo="ParentSize" X="1" Duration="1" Easing="BounceIn"/>
				</AddingAnimation>
				<RemovingAnimation>
					<Move RelativeTo="ParentSize" X="-1" Duration="0.5"/>
				</RemovingAnimation>
			</Panel>
		</WhileTrue>
	</StackPanel>
</App>
```

### $(ActivatingAnimation)

`ActivatingAnimation` allows for animating based on which @(Page) is active. `ActivatingAnimation` will progress from 0 to 1 as a @(Page) is being navigated to. If @(SwipeNavigate) is used, one can observe that `ActivatingAnimation` progressed from 0 as soon as the @(Page) is entering, stays at 1 as long as the @(Page) is active, and then progresses towards 0 again as the @(Page) is exiting.

### $(DeactivatingAnimation)

`DeactivatingAnimation` is just like the `ActivatingAnimation`, except that the progress is reversed. That means that the trigger will progress from 1 to 0 as a @(Page) activates, and 0 to 1 as it deactivates.

### $(ScrollingAnimation)

`ScrollingAnimation` lets us create animations in response to a @(ScrollView) being scrolled. By using the `From` and `To` properties one can define an interval on the @(ScrollView) where the trigger gets activated.

In the following example, we use `ScrollingAnimation` to @(Scale) the @(ScrollView) as it is being scrolled.

```
<ScrollView>
	<StackPanel Background="#ddd" Margin="10">
		<Panel Height="200" Background="Red"/>
		<Panel Height="200" Background="Blue"/>
		<Panel Height="200" Background="Green"/>
		<Panel Height="200" Background="Purple"/>
		<Panel Height="200" Background="Teal"/>
	</StackPanel>
	<ScrollingAnimation From="0" To="400">
		<Scale Factor="0.3" />
	</ScrollingAnimation>
</ScrollView>
```

> ### $(PullToReload)

`PullToReload` lets you easily create a "pull to reload" interaction.

It is implemented as a @(ScrollingAnimation), with a set of properties that let you bind different @(State:states) that should be triggered during different stages of interaction.

```
<ScrollView>
	<PullToReload>
		<State ux:Binding="Pulling">
			<!-- Will be active while the user is pulling downward -->
		</State>
		<State ux:Binding="PulledPastThreshold">
			<!-- Will be active when the user has pulled past the threshold -->
		</State>
		<State ux:Binding="Loading">
			<!-- Will be active when the pointer is released after pulling past the threshold -->
		</State>
	</PullToReload>
</ScrollView>
```

By data binding to the `IsLoading` and `ReloadHandler` properties, we can respond to the reload being initiated.

```
<JavaScript>
	var Observable = require("FuseJS/Observable");
	
	var isLoading = Observable(false);
	function onReload() {
		fetchDataSomehow(function() {
			isLoading.value = false;
		});
	}
	
	module.exports = { isLoading, onReload };
</JavaScript>

<ScrollView>
	<PullToReload IsLoading="{isLoading}" ReloadHandler="{onReload}" />
</ScrollView>
```

**Note:** In the above example, `IsLoading` has a two-way data binding to `isLoading`, meaning that both the `PullToReload` itself and the `isLoading` Observable can change its value.

Since `PullToReload` actually is a @(ScrollingAnimation), we can for instance use it to show a loading indicator as the user pulls downward.

```
<Panel>
	<Circle ux:Name="loadingIndicator" Width="50" Height="50" Color="Red" Alignment="Top" Offset="0,-50" />
	<ScrollView>
		<PullToReload>
			<Move Target="loadingIndicator" Y="2" RelativeTo="Size" Duration="1" />
		</PullToReload>
	</ScrollView>
</Panel>
```

### $(WhileScrollable)

`WhileScrollable` is used to animate based on whether a @(ScrollView) can be scrolled or not. Use the `ScrollDirections` property to filter the activation based which directions we care about.

`ScrollDirections` can take on any one of the following values:

- All
- Both
- Down
- Horizontal
- Left
- Right
- Up
- Vertical

In the following example, our background changes color when we reach the bottom of our @(ScrollView):

```
<ScrollViewer>
	<SolidColor ux:Name="color" Color="#000"/>
	<StackPanel Margin="10">
			<Each Count="10">
				<Panel Height="200" Background="Red" Margin="2"/>
			</Each>
		</StackPanel>
	<WhileScrollable ScrollDirections="Down">
		<Change color.Color="#ddd" Duration="0.4"/>
	</WhileScrollable>
</ScrollViewer>
```

### $(ProgressAnimation)

`ProgressAnimation` can be used together with a slider to animate elements as one slides its thumb. `ProgressAnimation` always goes from 0 to 1 as one slides the slider from its minimum value to its maximum value.

```
<Image File="someImage.png">
	<Blur ux:Name="blur" Radius="0"/>
</Image>
<Slider Width="50%" Alignment="Bottom" Margin="0,80">
	<ProgressAnimation>
		<Change blur.Radius="10"/>
	</ProgressAnimation>
</Slider>
```

### $(Timeline)

The `Timeline` allows for a nice way of grouping several animations together and separating them from the interaction logic. It can be played by animating its `TargetProgress` property between 0 and 1.

Here is an example of how we can use a timeline to animate several properties on a rectangle (its width and color), and then play between the start and end of this `Timeline` by clicking two buttons.

```
<App Theme="Basic">
	<StackPanel>
		<Rectangle ux:Name="rect" Height="40" Width="100%">
			<SolidColor ux:Name="color" Color="#f00" />
		</Rectangle>
		<Grid ColumnCount="2">
			<Button Text="Red">
				<Clicked>
					<Set timeline.TargetProgress="0" />
				</Clicked>
			</Button>
			<Button Text="Green">
				<Clicked>
					<Set timeline.TargetProgress="1" />
				</Clicked>
			</Button>
		</Grid>

		<Timeline ux:Name="timeline">
			<Change Target="rect.Width">
				<Keyframe Value="10" Time="0.3"/>
				<Keyframe Value="100" Time="0.6"/>
			</Change>
			<Change color.Color="#0f0" Duration="0.3" Delay="0.3"/>
		</Timeline>
	</StackPanel>
</App>
```

## $(RangeAdapter:Range adapter)

The `RangeAdapter` can be used to adjust the range of values used in bindings. This allows finer control over animations such as `Timeline` and `...Animation` triggers.

- `Source` the value to be adapted. This value is always updated directly, the `RangeAdapter` does not store any value internally.
- `SourceRangeMin` the minimum value to map to on the source
- `SourceRangeMax` the maximum value to map to on the source
- `ValueRangeMin` the minimum value to map to on the value. Default 0
- `ValueRangeMax` the maximum value to map to on the value. Default 1
- `Value` the value to be translated. Change this to have `Source` updated with the translated value.

In the following example, two panels are rotated. However, due to one of the panels being animated via a `RangeAdapter`, it will only rotate half of what the other panel is rotated:

```
<App Theme="Basic" Background="#fff">
  <StackPanel>
    <StackPanel Height="200">
      <Panel ux:Name="animationTarget" Color="#874cff" Width="70" Height="70" Margin="10" />
      <Panel ux:Name="animationTargetTwo" Color="#d46fff" Width="70" Height="70" Margin="10" />
    </StackPanel>
    <Button Text="Run animation" >
      <Clicked>
	<Toggle Target="actualAnim" />
      </Clicked>
    </Button>
    <Timeline ux:Name="timeline">
      <Rotate Target="animationTargetTwo" Degrees="90" Duration="1"/>
    </Timeline>
    <RangeAdapter ux:Name="range" Source="timeline.Progress" SourceRangeMin="0" SourceRangeMax="0.5" />
    <WhileTrue ux:Name="actualAnim">
      <Rotate Target="animationTarget" Degrees="90" Duration="1"/>
      <Change range.Value="1" Duration="1" />
    </WhileTrue>
  </StackPanel>
</App>
```

<!-- ## Advanced trigger usage

TODO: Show examples of
* Document Bypass
* Triggers within triggers
* Elements/nodes within triggers
* Styles within triggers (? not sure if this is currently possible) -->
