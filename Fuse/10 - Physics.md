# $(Physics)

Some types of animation ([like this one](https://www.fusetools.com/developers/examples/swipeplaces)) are best described using Physics simulation. Fuse comes with a set of physics based triggers and behaviors which can be used for these situations.

_Note: When using physics, make sure your @(Project structure:project) has a reference to the Fuse.Physics package._

## $(Physics Rules)

Physics simulation is achieved by using different types of physics behaviors to create spaces in which elements are affected dynamically in various ways. Each physics behavior knows how to simulate a certain kind of force, and how to apply this to each frame of the animation. Affected elements can respond to multiple forces at a time. We can add additional customization to the animation by using the related @(Trigger:triggers) to animate based on certain physics events. Physics behaviors are categorized

### $(Draggable)

The `Draggable` behavior lets you move an @(Element) using the pointer by dragging it.

```
<Panel>
	<Rectangle Width="100" Height="100" Fill="Red">
		<Draggable/>
	</Rectangle>
</Panel>
```

#### $(WhileDragging)

The `WhileDragging` trigger is active while the @(Element) is being dragged and deactivates as it gets released.

```
<Panel>
	<Rectangle Background="Blue" Width="50" Height="100">
		<Draggable/>
		<WhileDragging>
			<Rotate Degrees="90" Duration="0.8"
			        Easing="BounceOut" EasingBack="BounceIn"/>
		</WhileDragging>
	</Rectangle>
</Panel>
```

## $(Force fields)
Force fields are physics behaviors that continously affect elements within their defined area of influence.

#### $(PointAttractor)

The `PointAttractor` is used to create a circular area of influence that pulls idle @(Draggable:draggable) @(Element:elements) towards its center.

*Properties:*
- Offset - Set the position of the `PointAttractor` relative to its container
- Radius - The radius from the point where the attractor will have an effect.
- Strength - How much attraction is applied to the affected elements.
- Exclusive - Whether this attractor will be exclusively applied when it is considered the strongest @(Physics Rules:rule) applying force.

Multiple `PointAttractors` can overlap, creating potentially interesting behaviors.

The [Swipe places](https://www.fusetools.com/examples/swipe-places) example is a good example of the `PointAttractor` in use.

<!-- ### $(Spring)
The Spring @(Rule:rule) is used

- Target
- Length
- Stiffness
AUTH: Buggy?
-->

### $(Force field triggers)

Force field triggers activate based on whether their parent @(Element) is affected by a force field or not.

*Common properties:*
- `ForceField` - The force field needed to enter to activate this @(Trigger).

#### $(EnteredForceField)

`EnteredForceField` is a pulse trigger which which activates when the @(Element) enters the specified `ForceField`.
Being a pulse trigger, means that the @(Animators:animations)/@(Actions:actions) inside the trigger will be played forwards and then backwards in one continuous run. You can modify the `Threshold` attribute to specify how close the @(Element) has to be to activate the trigger, working in unison with the `Radius` property of the force field in question.

#### $(ExitedForceField)

`ExitedForceField` works like @(EnteredForceField), but activates on elements _leaving_ the specified `ForceField`.

#### $(InForceFieldAnimation)

`InForceFieldAnimation` is used to animate @(Element:elements) as they enter a force field. You can modify the `From` and `To` properties to control the range along the distance from the center within the force field to its outer edge in which the animation should occur.

- `From` & `To` - Number between 0 and 1, 0 being the edge of the forcefield and 1 at the center.

	<Panel>
		<Panel Alignment="Top">
			<Rectangle Background="Blue" Width="50" Height="100" Margin="0,100">
				<Draggable/>
				<InForceFieldAnimation ForceField="attractor" From="0.5" To="1">
					<Rotate Degrees="360"/>
				</InForceFieldAnimation>
				<InForceFieldAnimation ForceField="attractor" From="0" To="1">
					<Change circleColor.Color="#f00"/>
				</InForceFieldAnimation>
			</Rectangle>
		</Panel>
		<Panel Alignment="BottomCenter" MaxHeight="10000" MaxWidth="10000"
		       Height="800" Width="800" Y="50%">
			<Panel>
				<PointAttractor ux:Name="attractor" Radius="400" Strength="200"/>
			</Panel>
			<Circle>
				<SolidColor ux:Name="circleColor" Color="#ddd"/>
			</Circle>
		</Panel>
	</Panel>
