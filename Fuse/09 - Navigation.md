# $(Navigation)

Most apps contains more than just one page. Fuse has several components for making and customizing navigation between different pages.

Lets take a look at a simple case: we want a set of @(Page:pages) which we can @(SwipeNavigate:swipe) between.

## $(Basic navigation)

To start off, we make a set of @(Panel:panels) which represents our @(Page:pages).

```
<Panel>
	<Panel Background="Red"/>
	<Panel Background="Blue"/>
	<Panel Background="Green"/>
</Panel>
```

Currently this is just a @(Panel) with three @(Panel:panels) inside it. To make our swipe navigation possible, we need to add three things.

- A @(Navigation) behavior
- A @(SwipeNavigate) behavior
- Entering- and ExitingAnimation to our Panels

We start off by adding a @(LinearNavigation)- and a @(SwipeNavigate) behavior.
The @(LinearNavigation) is just one of several @(Navigation types:types of navigation) that can be used.

```
<Panel>
	<LinearNavigation />
	<SwipeNavigate SwipeDirection="Left"/>
	<Panel Background="Red"/>
	<Panel Background="Blue"/>
	<Panel Background="Green"/>
</Panel>
```

This adds all the behavior we need for navigation, but this is not enough to let us swipe between pages.
The pages don't know how to animate themselves when they @(EnteringAnimation:enter) or @(ExitingAnimation:exit).
For that we @(Creating custom UI components:subclass) @(Panel) and add both an @(EnteringAnimation) and an @(ExitingAnimation).

```
<Panel>
	<Panel ux:Class="NavPanel">
		<EnteringAnimation>
			<Move X="-1" RelativeTo="ParentSize"/>
		</EnteringAnimation>
		<ExitingAnimation>
			<Move X="1" RelativeTo="ParentSize" Duration="0.5"/>
		</ExitingAnimation>
	</Panel>
	<LinearNavigation />
	<SwipeNavigate SwipeDirection="Left"/>
	<NavPanel Background="Red"/>
	<NavPanel Background="Blue"/>
	<NavPanel Background="Green"/>
</Panel>
```

Now the @(Panel:panels) will move to the side by the width of their parent container when they are navigated to and from.

## $(Page)

When a @(Panel) contains a @(Navigation) behavior, all its direct children act as pages in that navigation context. Since we usually want to style our pages a bit differently than the rest of our @(Panel:panels), Fuse comes with a `Page` type which we can use instead. The `Page` type is almost no different than a @(Panel), but comes with a `Title` property for convenience and semantic value.

## $(PageControl)

Since swiping between pages is such a common interaction, Fuse comes with a @(Control) for exactly this.

```
<PageControl>
	<Panel Background="Red"/>
	<Panel Background="Blue"/>
</PageControl>
```

The above example illustrates the default behavior of `PageControl`, which is to slide the pages in response to swipe gestures.
We can however customize this behavior with the following properties:

- `InactiveState` controls the `Visibility` and `IsEnabled` status of children when they go off-screen.
	- `Collapsed` _(default)_ – Sets `Visibility="Collapsed"` so that inactive pages are hidden and no layout is calculated on them.
	- `Disabled` – Inactive pages are visible, but pointer interaction is disabled by setting `IsEnabled="false"`.
	- `Unchanged` – Inactive pages are left as-is.
- `Transition` controls how pages are transitioned during navigation
	- `Standard` _(default)_ – Performs a standard sideways sliding animation.
	- `None` – Does not perform animation. Use this to implement your own animation.
- `Interaction` controls how user interaction is handled
	- `Swipe` _(default)_ – The user can swipe between pages.
	- `None` – The user cannot directly interact with navigation. Use this to implement your own navigation controls/gestures.

By using data binding, you can set the currently active page by `Name` using the `Active` property.
In the following example, We have three pages and a button that returns the user to the first page.

```
<DockPanel>
	<JavaScript>
		var Observable = require("FuseJS/Observable");
		var currentPage = Observable("page1");
		function clickHandler() {
			currentPage.value = "page1";
		}
		module.exports = {
			clickHandler: clickHandler,
			currentPage: currentPage
		};
	</JavaScript>
	<PageControl Active="{currentPage}">
		<Panel Name="page1" Background="Red"/>
		<Panel Name="page2" Background="Green"/>
		<Panel Name="page3" Background="Blue"/>
	</PageControl>
	<Button Text="Home" Clicked="{clickHandler}" Dock="Bottom"/>
</DockPanel>
```

Take a look at the [Pages using JavaScript](https://www.fusetools.com/community/examples/pageslist) example to see how this can be used in practice.

### $(PageIndicator)

The `PageIndicator` can be used together with @(PageControl) to show clearly which page is selected. The following example has four pages in a @(PageControl) and a circle docked to the bottom for each @(Page). When a @(Page) becomes `Active` its corresponding indicator is scaled by a factor of 1.3.

	<DockPanel>
		<PageControl ux:Name="pageControl">
			<Page Background="Red"/>
			<Page Background="Blue"/>
			<Page Background="Green"/>
			<Page Background="Yellow"/>
		</PageControl>
		<PageIndicator Navigation="pageControl" Dock="Bottom" Alignment="Center">
			<Circle ux:Generate="Factory" ux:Binding="DotFactory"
					Fill="Teal" Margin="5" Width="20" Height="20">
				<ActivatingAnimation>
					<Scale Factor="1.3"/>
				</ActivatingAnimation>
			</Circle>
		</PageIndicator>
	</DockPanel>

It is mandatory to set a value for the @(PageIndicator:page indicators) `Navigation` property. In this case we simply set it to be the @(PageControl).

Another thing to note is that the `PageIndicator` uses a `Factory` to create an indicator for each page. In the example above, we use a @(Circle) with the @(ux:Generate) property set to `Factory` and the @(ux:Binding) property set to the page indicator's `DotFactory` property as our "template". If nothing is set, the $(PageIndicator:page indicator) will default to using a `PageIndicatorDot`, a simple dot template that will do the trick in many cases.

## {Page} Binding


The `{Page name}` syntax allows linking with resources on the currently active navigation page. It works much like `{Resource name}`, except that it starts looking from the current page instead of the current node. A common use for this is to show a title for the current page.

In the following example, we have a @(DockPanel) with a @(Panel) and a @(PageControl). We would like the contents of our @(Panel) to display the title of the current @(Page), so we set its `Navigation` property to be the @(PageControl).

```
<DockPanel>
	<Panel Navigation="myNav" Dock="Top" Height="100" Background="Teal">
		<Text Value="{Page title}" FontSize="22" Alignment="Center"/>
	</Panel>
	<PageControl ux:Name="myNav">
		<Page Background="Red">
			<string ux:Key="title" ux:Value="First page"/>
		</Page>
		<Page Background="Green">
			<string ux:Key="title" ux:Value="Second page"/>
		</Page>
	</PageControl>
</DockPanel>
```

`Navigation="myNav"` establishes the navigation context for this node and its descendants. It can be placed on any node, and overridden on the children. Any binding, gesture, or otherwise that refers to a navigation will now refer to this one.

To show the title of the page we use the `{Page title}` binding:

```
<Text Value="{Page title}"/>
```

`{Page title}` is a resource binding that starts at the current page of the navigation (the one we specified with `Navigation="TheNav"`. Any time the current page changes this value will also change.

### Defining Title

The title of the page still needs to be defined somewhere. We do this on the pages of in our navigation using the generic resource syntax:

	<string ux:Key="Title" ux:Value="The First Page"/>

`ux:Key="Title"` defines a resource with the name `Title` and the `ux:Value` specifies the value. These are just normal resources -- anything that can use resources will also work through the `{Page}` binding.

### PageIndicator

This feature can be used to create an interesting page indicator. We can replace the dots with images for each of the pages. On the page we define the image source we want to use:

	<FileImageSource ux:Key="Image" File="icons_200/bowling.png"/>

Again, this is just a normal resource binding that happens to refer to an image source.

With these on each page we can create a `PageIndicator` that uses images for the "dots":

	<PageIndicator Navigation="TheNav" ...>
		<Image ux:Binding="DotFactory" ux:Generate="Factory" Source="{Page Image}" ...>

<!-- todo: Write about Factory -->

## $(Navigation types)

There are several navigation types, and they have quite different behaviors and use cases.

Each of them inherit from the `Navigation` base type, which exposes the `Active` property, which sets the currently active page. This can be data-bound to the string name of the node.

### $(LinearNavigation)

`LinearNavigation` is used when each page should be laid out linearly. So with a swipe navigation, one would start from page 1, swipe to page 2, then page 3 and so on. Navigating directly to page 3 from page 1 would cause a quick visit to page 2 on the way.

### $(DirectNavigation)

With `DirectNavigation` there is no implicit flow between pages. Any @(Page) can be directly navigated to from any other @(Page). If we have 5 pages in our @(Navigation), there would be no scrolling over the other pages when navigating from page 1 to page 5 as there would be with a @(LinearNavigation).

### $(HierarchicalNavigation)

A `HierarchicalNavigation` is commonly used when there is a hierarchical flow of pages. This is commonly found in the settings apps on iOS and Android devices. One first picks a topic, then a subtopic and so on, taking us deeper into the hierarchy of options. Navigating in this context means pushing a page onto a stack of pages. For each navigation there is a natural back navigation which takes us back the the page navigated from.

> ### HierarchicalNavigation example

This is an example that demonstrates usage of `HierarchicalNavigation` in conjunction with @(NavigateTo), @(GoBack), @(GoForward), and @(WhileCanGoForward).

```
<Panel>
	<HierarchicalNavigation ux:Name="nav" Active="mainPage" />

	<Page ux:Class="MyPage">
		<EnteringAnimation>
			<Move X="1" RelativeTo="ParentSize" />
		</EnteringAnimation>
		<ExitingAnimation>
			<Move X="-1" RelativeTo="ParentSize" />
		</ExitingAnimation>
	</Page>

	<MyPage ux:Name="mainPage">
		<StackPanel>
			<Button Text="Page 1">
				<Clicked>
					<NavigateTo Target="subPage1" />
				</Clicked>
			</Button>
			<Button Text="Page 2">
				<Clicked>
					<NavigateTo Target="subPage2" />
				</Clicked>
			</Button>
			<WhileCanGoForward>
				<Button Text="Go Forward">
					<Clicked>
						<GoForward />
					</Clicked>
				</Button>
			</WhileCanGoForward>
		</StackPanel>
	</MyPage>

	<MyPage ux:Name="subPage1" ux:AutoBind="false">
		<StackPanel>
			<Text>Welcome to page 1!</Text>
			<Button Text="Go Back">
				<Clicked>
					<GoBack />
				</Clicked>
			</Button>
		</StackPanel>
	</MyPage>

	<MyPage ux:Name="subPage2" ux:AutoBind="false">
		<StackPanel>
			<Text>Welcome to page 2!</Text>
			<Button Text="Go Back">
				<Clicked>
					<GoBack />
				</Clicked>
			</Button>
		</StackPanel>
	</MyPage>
</Panel>
```

### $(EdgeNavigator)

`EdgeNavigator` is used when the intent is to navigate a menu in from a side, as shown in our [Social media example](https://www.fusetools.com/examples/social-media-screen). It is used by encasing the content you want inside the navigatable area in an `EdgeNavigator` element, and setting the `Edge` property on a @(Panel) to tell the navigator which edge you want said panel to navigate in from.

```
<EdgeNavigator>
	<Panel Width="150" Edge="Left" Background="#f63" />
	<Panel Background="#fff">
		<Text Alignment="Center">
			This is an example of EdgeNavigator!
		</Text>
	</Panel>
</EdgeNavigator>

```

Note that setting the @(Background) of the second @(Panel) in this example is of significance, as it enables hit testing of the inner @(Panel). You could alternatively set the @(HitTestMode) of the @(EdgeNavigator) to be @(HitTestMode.LocalBoundsAndChildren). Normally this isn't an issue, as the inner panel will have content that is hit testable.


### $(EdgeNavigation)

While @(EdgeNavigator) will do the job in most cases, you might be interested in using `EdgeNavigation` if you are not happy with the behaviour of the `EdgeNavigator`(you might e.g. want to change the default animation). The following example wil re-create the effect `EdgeNavigator` provides, but with more customisability:

```
<App Theme="Basic">
	<DockPanel>
		<EdgeNavigation />
		<Panel Width="150" Background="#0F0" Edge="Left" Alignment="Left">
			<EnteringAnimation>
				<Move X="-1" RelativeTo="Size" />
			</EnteringAnimation>
		</Panel>
		<Panel Background="#F00" />
	</DockPanel>
</App>
```


## $(Controlling navigation)

Fuse provides several @(Actions:actions) that allow you to perform @(Navigation:navigation).

All navigation-related @(Actions:actions) have a `NavigationContext` property that lets you specify the navigation context to perform the action on.
If `NavigationContext` is not specified, it will look for a parent element with a `Navigation` behavior and use that.

> ### Manually specifying a navigation context

```
<DockPanel>
	<PageControl ux:Name="navContext">
		<Page ux:Name="page1" Background="#f00" />
		<Page ux:Name="page2" Background="#00f" />
	</PageControl>
	<Grid Dock="Bottom" Columns="1*,1*">
		<Button Text="Go to page 1">
			<Clicked>
				<NavigateTo Target="page1" NavigationContext="navContext" />
			</Clicked>
		</Button>
		<Button Text="Go to page 2">
			<Clicked>
				<NavigateTo Target="page2" NavigationContext="navContext" />
			</Clicked>
		</Button>
	</Grid>
</DockPanel>
```

### GoBack

The behavior of `GoBack` depends on the type of navigation context it's performed on:

- @(LinearNavigation) – Navigates to the page occurring before the current page.
- @(HierarchicalNavigation) – Navigates one level up in the hierarchy, i.e. the page most recently navigated to.
- @(DirectNavigation) – Does nothing.

> ### $(WhileCanGoBack)

The `WhileCanGoBack` trigger is active whenever navigating backward is possible.

### GoForward

As with @(GoBack), `GoForward` is also context-sensitive:

- @(LinearNavigation) – Navigates to the page occurring after the current page.
- @(HierarchicalNavigation) – Navigates one level down in the hierarchy, i.e. the last page the user has @(GoBack:gone back) from.
- @(DirectNavigation) – Does nothing.

> ### $(WhileCanGoForward)

The `WhileCanGoForward` trigger is active whenever navigating forward is possible.


### $(NavigateTo)

Navigates to a specific @(Page:page), specified by the `Target` property. Below is an example using a @(PageControl).

```
<PageControl ux:Name="nav">
	<Page ux:Name="page1">
		<Button Text="Go to page 2" Alignment="Center">
			<Clicked>
				<NavigateTo Target="page2" />
			</Clicked>
		</Button>
	</Page>
	<Page ux:Name="page2">
		<Text Alignment="Center">
			Welcome to page 2!
		</Text>
	</Page>
</PageControl>
```

## $(EnteringAnimation) / $(ExitingAnimation)

As seen above, Entering- and ExitingAnimation are used to specify how pages and elements behave when they are being navigated to and from.
There is no default behavior for elements when using `Navigation` so unless a @(PageControl) is being used (which does apply an animation between pages), you have to set this up yourself.

You can extend `Panel` to apply animation for all pages:

```
	<Page ux:Class="AnimatedPage">
		<EnteringAnimation>
			<Move X="-1" RelativeTo="ParentSize" Duration="0.5"/>
		</EnteringAnimation>
		<ExitingAnimation>
			<Move X="1" RelativeTo="ParentSize" Duration="0.5"/>
		</ExitingAnimation>
	</Page>

	<Panel>
		<LinearNavigation />
		<AnimatedPage Background="Red" />
	</Panel>
```

It just moves each page by the width of its parent, which is a common style for @(LinearNavigation) situations.

There are of course no limits to what can be animated while navigating. The following example also @(Scale:scales) the pages as they enter/exit.

```
<Page ux:Class="AnimatedPage">
	<EnteringAnimation>
		<Move X="-1" RelativeTo="ParentSize" Duration="0.5"/>
		<Scale Factor="0.2" Duration="0.5"/>
	</EnteringAnimation>
	<ExitingAnimation>
		<Move X="1" RelativeTo="ParentSize" Duration="0.5"/>
		<Scale Factor="0.6" Duration="0.5"/>
	</ExitingAnimation>
</Page>
```

## $(ActivatingAnimation)

`ActivatingAnimation` is triggered as at page is navigated to and from (when it is becoming the active page). As a @(Page) is navigated to, any `ActivatingAnimation` is triggered. The animation progresses from 0 as the page is entering and becomes 1 when the page is done entering. As the page exits `ActivatingAnimation` progresses from 1 back to 0.

Here is a more advanced use of @(EnteringAnimation:Entering-), @(ExitingAnimation:Exiting-) and @(ActivatingAnimation):

```
<Page ux:Class="MyPage" ux:Name="self">
	<EnteringAnimation>
		<Move RelativeTo="ParentSize">
			<Keyframe X="-0.7" Time="0.5"/>
			<Keyframe X="0" Time="1"/>
		</Move>
		<Rotate>
			<Keyframe DegreesZ="20" Time="0.5"/>
			<Keyframe DegreesZ="0" Time="1"/>
		</Rotate>
		<Change self.Opacity="0" Duration="1"/>
		<Scale Factor=".5" Duration="1"/>
	</EnteringAnimation>
	<ExitingAnimation>
		<Move RelativeTo="ParentSize">
			<Keyframe X="0.7" Time="0.5"/>
			<Keyframe X="0" Time="1"/>
		</Move>
		<Rotate>
			<Keyframe DegreesZ="-20" Time="0.5"/>
			<Keyframe DegreesZ="0" Time="1"/>
		</Rotate>
		<Scale Factor=".5" Duration="1"/>
	</ExitingAnimation>
	<WhileActive Threshold="0.5">
		<BringToFront/>
	</WhileActive>
</Page>
```

<!-- TODO: Document whats going on? -->

## $(WhileActive)

`WhileActive` animates as a page is becoming active.

## $(WhileInactive)

`WhileInactive` animates as a page is becoming inactive.


## $(WhileInEnterState)

This does the same as @(WhileInactive), but differs in that it has a negative progress value if the panel it is bound to is after the current panel in the list of navigatable panels(e.g it is _after_ the current panel).

## $(WhileInExitState)

This also mostly does the same as @(WhileActive), but differs by having a negative value if it is after the currently navigated panel in the list of navigatable panels, just like @(WhileInEnterState).

## $(WhileNavigating)

This will be on as long as the user is currently navigating in between two panels. It does not provide progress.

## $(SwipeNavigate)

`SwipeNavigate` is used to connect swiping gestures to @(Navigation). This is used internally by @(EdgeNavigator), but you would be interested in it e.g. if you want horizontally stacked pages. `SwipeNavigate` has three properties which modify its behavior:

* @(SwipeDirection) - Specifies the direction the user has to swipe in order to navigate "forwards".
* @(SwipeEnds) - Decides how the animation handles there not being a next or previous element to navigate to. `Closed` hard-limits the animation to the end of the last element, `Open` lets the user swipe unhindered, `Short` adds resistance to the animation, effectively slowing the animation down the further the user swipes.
* @(VelocityThreshold) - Minimum velocity required to snap to the next element.

### $(SwipeDirection)

Can have the values `Down`, `Right`, `Left` and `Up`. These values indicate which direction one swipes in order to navigate to the next page.

### $(SwipeEnds)

Can have the values `Closed`, `Open` and `Short`.

`Closed` means that when one reaches the first or last page, one cannot swipe further.
`Open` means that when one reaches the first or last page, one can swipe further as much as is possible. This does not mean that its possible to navigate to an undefined @(Page) however.
`Short` acts similarly to `Open` except that one can only swipe a a short distance beyond the first or last page.

### $(VelocityThreshold)

`VelocityThreshold` is used to decide how fast one has to swipe a page in order for it to be interpreted as a navigation. This means that one does not have to swipe the whole page away, but can get away with a short but fast flick gesture.


## Databind active page

It is quite possible to perform navigation through data-binding.
To do this, we data-bind the navigations `Active` property to a string in JavaScript. This string should correspond to the Name of the @(Page) we intend to navigate to.

<!-- TODO: Create example where both pages and direct navigation is databound. (perhaps hierarchical?) -->
