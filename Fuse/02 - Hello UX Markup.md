# Hello, $(UX Markup)!

The primary way of working in Fuse is by editing UX Markup - an XML-based file format that's tailor-made for app development. Layout, interaction and animation is written in a clear, declarative syntax. The main benefits of working in UX Markup is that it opens up for much deeper and faster development, and offers a better way for designers and developers to work together and more easily share code and designs.

UX Markup is used for building components, UI views, designs, prototypes and full apps in Fuse and is compiled down to C++ native code. UX Markup is complemented by business logic written in JavaScript, which runs on its own thread, leaving your UI and animation nice and smooth.

For an in-depth look at the features of UX Markup, see the [UX Markup Guide](https://www.fusetools.com/learn/guides/uxmarkup).

## $(Creating apps) : $(App)

When creating stand-alone apps or prototypes in Fuse, we use the `<App>` tag.

	<App Background="#436EEE">
		<Text>Hello, world!</Text>
	</App>

Inside your `<App>` tag you can put any `Node`, `Behavior` or `Theme` tag. The above example
simply displays text using the default font.

The `<App>` tag itself bootstraps the app and takes care of application lifecycle and @(Theme).

The $(Background) property controls the color of the root view of the app.


## UX tags

UX documents consists of XML tags. Each available UX tag corresponds to a *class* implemented in Uno code. Each tag corresponds to one (or multiple) runtime objects.

The available tags (classes) come in these categories:

* The @(App) class is the root of the app, and can contain exactly one Node
* Many `Node` types, most of which are @(Element:UI Elements)
* Behaviors which modify nodes. Behaviors come in many flavors:
  * Gestures
  * Triggers
  * Scripts
  * Visual @(effects) which can be applied to elements
* $(Styles:Styling) which allow consistent look and feel of components without repeating data

## $(Theme:Themes)

`App` offers a setting called `Theme`, which specifies how standard components in
the app will look and feel. You set it like this:

	<App Theme="..name of theme..">

`Theme` can have the following settings:

* `Basic` - Reccommended. Uses OpenGL based graphics to render controls with identical appearance on each platform.
* `Native` - Uses the native iOS and Android platform controls by default. Note that this will not work in local desktop preview.
* `NativeWithFallback` - Uses `Native` theme on iOS and Android devices, but `Basic` in local desktop preview.
* *Default* - If nothing is specified, Fuse uses OpenGL based graphics, but provides *no default appearance* for standard controls (such as `Button` and `Slider`).

### $(NativeTheme: Native Theme)

When using `Theme="Native"`, Fuse will use the native controls from the target platform. To use a native theme, do:

	<App Theme="Native">
		<StackPanel>
			<Button />
			<Switch />
			<Slider />
		</StackPanel>
	</App>

In this example, the displayed controls will have native appearance on iOS and Android, and *no appearance* in desktop preview.

> If you still want to make use of desktop preview while working with native controls, you can use the `NativeWithFallback` theme. This will give you native controls on iOS and Android, while falling back to a (unspecified) basic theme on desktop.

### $(BasicTheme: Basic Theme)

The `Basic` theme renders controls with OpenGL, gives controls a look and feel inspired by material design.

Example usage:

	<App Theme="Basic">

Using `Theme="Basic"` which will give your app an identical look on all platforms, with the exception of:

* Status bars will behave differently across platforms
* `TextInput` is and other higher level controls will map to the platform-specific controls.

The main benefits of working with `Basic` theme are:

* You can preview your app on Mac and PC using Fuse's realtime preview window,
  which offers a much smoother experience than the iOS and Android emulators.
* Your designs and animations will look and behave identically on all platforms.

## $(ux:AutoBind)

@(ux:AutoBind) controls whether an object should automatically be "connected" to the parent node. By default `ux:AutoBind="true"`, so you only need to use this if you want to do something special.

For example:
```
<Panel ux:Name="panel1" >
    <Panel ux:AutoBind="false" ux:Name="panel2" />
</Panel>
```

In this case, `panel2` will not become a child of `panel1`. Instead, it will remain detached from the object tree. This is only meaningful if you intend to add it later, for example by navigating to it in a `HierarchicalNavigation`.
