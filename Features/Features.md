<documentProperties pageTitle="Features" />

# Feature status

Keep track of current and upcoming Fuse features as we evolve through our beta.

This overview is updated for each release. **Latest update: 12th of April 2016.**

## Legend

<table>
	<tr>
		<td class="feature__mature">Mature</td>
		<td>The feature is done, tested, documented and can generally be used in production apps. If you run into problems with features marked as **Mature**, the Fuse team will deal with bug reports quickly. </td>
	</tr>
	<tr>
		<td class="feature__experimental">Experimental</td>
		<td>The feature is shipped in some form, but may be incomplete. Documentation can be limited or only available to a particular group of testers. The API or behavior is subject to change in the future based on feedback from testing. If you are using an experimental feature, make sure to check the changelog for updates when upgrading Fuse. </td>
	</tr>
	<tr>
		<td class="feature__planned">In progress</td>
		<td>The feature is actively being worked on.</td>
	</tr>
	<tr>
		<td class="feature__planned">Planned</td>
		<td>The feature is being planned and work on it will begin soon.</td>
	</tr>
	<tr>
		<td class="feature__backlog">Backlog</td>
		<td>The feature is on our todo-list for the future, but implementation has not yet been planned.</td>
	</tr>
	<tr>
		<td class="feature__n-a">N/A</td>
		<td>The feature is either not possible or desirable to implement on the target platform or configuration.</td>
	</tr>
</table>


## Cross-platform UX Components

These UX components are the basic building blocks for creating cross-platform app screens. These components work
across all themes, in both `Native` mode and `Graphics` mode. The look & feel of certain controls (such as Button) may vary in different
themes.

<table>
	<thead>
		<tr>
			<th></th>
			<th><b>Graphics Mode</b></th>
			<th><b>Native iOS</b></th>
			<th><b>Native Android</b></th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>
				@(Layout)
				<p>Define flexible, responsive layout for your app screens.<p>
			</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
		</tr>
		<tr>
			<td>
				@(Shapes)
				<p>Build UI designs with basic (but fast) vector shapes to easily adapt your design to any screen size and density.<p>
			</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
		</tr>
		<tr>
			<td>
				@(Image)
				<p>Display images and icons with multi-density support to adapt to any screen size and density.<p>
			</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
		</tr>
		<tr>
			<td>
				@(Video)
				<p>Play videos, either as a media player or as part of your UI design.<p>
			</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__planned">Planned</td>
			<td class="feature__planned">Planned</td>
		</tr>

		<tr>
			<td>
				@(Navigation)
				<p>General purpose, cross-platform navigation system which lets you add custom gesture-driven animated navigation to any part of your app.</p>
			</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
		</tr>
		<tr>
			<td>
				TextEdit
				<p>A plain text input field without a theme or styling.</p>
			</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
		</tr>
		<tr>
			<td>
				@(TextInput)
				<p>A text input field decorated with look and feel from the theme.</p>
			</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
		</tr>
		<tr>
			<td>
				@(Button)
				<p>Basic clickable control.</p>
			</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
		</tr>
		<tr>
			<td>
				@(Slider)
				<p>Lets you specify a numeric value with a track and knob.</p>
			</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
		</tr>
		<tr>
			<td>
				@(Switch)
				<p>Lets you specify an on/off boolean value.</p>
			</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
		</tr>
		<tr>
			<td>
				CameraFeed
				<p>Display a live feed from the device camera as a UI element.</p>
			</td>
			<td class="feature__planned">Planned</td>
			<td class="feature__n-a">N/A</td>
			<td class="feature__n-a">N/A</td>
		</tr>
	</tbody>
</table>


## Native UX Components

These components are available in Fuse through the `Native` theme, or inside a `NativeViewHost`. They are
not available in `Graphics` themes, and can not interact with the Fuse effect system.

Their features, function and appearance may vary between platforms.

<table>
	<thead>
		<tr>
			<th></th>
			<th><b>iOS</b></th>
			<th><b>Android</b></th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>
				@(WebView)
				<p>Display a browser in your app, or create certain parts of your app using HTML5.</p>
			</td>
			<td class="feature__experimental">Experimental</td>
			<td class="feature__experimental">Experimental</td>
		</tr>
		<tr>
			<td>
				@(MapView)
				<p>Display the OS native maps.</p>
			</td>
			<td class="feature__experimental">Experimental</td>
			<td class="feature__experimental">Experimental</td>
		</tr>

		<tr>
			<td>
				Native Pickers
				<p>Display the native OS pickers, date picker, value picker etc.</p>
			</td>
			<td class="feature__planned">Planned</td>
			<td class="feature__planned">Planned</td>
		</tr>

		<tr>
			<td>
				Native UI Controls 
				<p>Other native controls and widgets. Covers modal dialogs, tab bars and more. Detailed feature-list to be published when the underlying tech is ready.</p>
			</td>
			<td class="feature__planned">In progress</td>
			<td class="feature__planned">In progress</td>
		</tr>

		<tr>
			<td>
				Audio
				<p>Play sounds as part of UX markup triggers.</p>
			</td>
			<td class="feature__planned">Planned</td>
			<td class="feature__planned">Planned</td>
		</tr>

		<tr>
			<td>
				iOS Navigation
				<p>Create a native navigation system based on the iOS built-in navigator controls and native transitions.</p>
			</td>
			<td class="feature__planned">Planned</td>
			<td class="feature__n-a">N/A</td>
		</tr>

		<tr>
			<td>
				Android Material Design Navigation
				<p>Create a native navigation system based on the Material Design component pack provided by Google.</p>
			</td>
			<td class="feature__n-a">N/A</td>
			<td class="feature__planned">Planned</td>
		</tr>
	</tbody>
</table>


## FuseJS Features

<table>
	<thead>
		<tr>
			<th></th>
			<th><b>Native iOS</b></th>
			<th><b>Native Android</b></th>
		</tr>
	</thead>

	<tbody>
		<tr>
			<td>
				@(Observable) - Reactive data binding
			</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
		</tr>

		<tr>
			<td>
				<a href="/learn/fusejs#xmlhttprequest">XMLHttpRequest</a>
			</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
		</tr>

		<tr>
			<td>
				@(fetch) (Promise-based HTTP)
			</td>
			<td class="feature__mature">Mature</td>
			<td class="feature__mature">Mature</td>
		</tr>

		<tr>
			<td>
				<a href="/learn/fusejs#push-notifications">Push Notifications</a>
			</td>
			<td class="feature__experimental">Experimental</td>
			<td class="feature__experimental">Experimental</td>
		</tr>

		<tr>
			<td>
				<a href="/learn/fusejs#the-notification">System Notifications</a>
			</td>
			<td class="feature__experimental">Experimental</td>
			<td class="feature__experimental">Experimental</td>
		</tr>

		<tr>
			<td>
				<a href="/learn/fusejs#takepicture-function">Take photo with device camera</a>
			</td>
			<td class="feature__experimental">Experimental</td>
			<td class="feature__experimental">Experimental</td>
		</tr>

		<tr>
			<td>
				<a href="/learn/fusejs#geolocation">Geolocation</a>
			</td>
			<td class="feature__experimental">Experimental</td>
			<td class="feature__experimental">Experimental</td>
		</tr>

		<tr>
			<td>
				<a href="/learn/fusejs#storage">Local Storage</a>
			</td>
			<td class="feature__experimental">Experimental</td>
			<td class="feature__experimental">Experimental</td>
		</tr>

		<tr>
			<td>
				<a href="/learn/fusejs#storage">WebSocket</a>
			</td>
			<td class="feature__planned">In progress</td>
			<td class="feature__planned">In progress</td>
		</tr>
	</tbody>
</table>

## Native Interop

Fuse has several ways in which you can interop with native APIs, existing native code and third-party SDKs.

<table name="nativeinterop">
	<thead>
		<tr>
			<th></th>
			<th><b>iOS</b></th>
			<th><b>Android</b></th>
		</tr>
	</thead>

	<tbody>
		<tr>
			<td>
				<b>Foreign Code</b>
				<p>Put native Java, Objective-C and C++ code directly in your Fuse project, and expose new functionality to FuseJS.</p>
			</td>
			<td class="feature__experimental">Experimental</td>
			<td class="feature__experimental">Experimental</td>
		</tr>

		<tr>
			<td>
				<b>Uno Inline Foreign Code</b>
				<p>Implement Uno methods directly in a native platform language: Java, Objective-C or C++.</p>
			</td>
			<td class="feature__experimental">Experimental</td>
			<td class="feature__experimental">Experimental</td>
		</tr>

		<tr>
			<td>
				<a href="https://www.fusetools.com/learn/uno#working-with-native-apis-from-uno">Uno Native API Bindings</a>
				<p>Call any Android and iOS API directly from Uno in plain syntax.</p>
			</td>
			<td class="feature__experimental">Mature (Deprecated)</td>
			<td class="feature__experimental">Mature (Deprecated)</td>
		</tr>

		<tr>
			<td>
				<a href="/learn/guides/uxl-handbook">Uno Extension Layer (UXL)</a>
				<p>Various low-level methods for advanced interop between Uno and the native target language.</p>
			</td>
			<td class="feature__experimental">Mature (Deprecated)</td>
			<td class="feature__experimental">Mature (Deprecated)</td>
		</tr>
	</tbody>
</table>

## Third-party library wrappers

Fuse will wrap the most popular 3rd party SDKs into easy to use UX/JS APIs. Many third-party JavaScript SDKs will work out of the box.

<b>SDKs and APIs not officially wrapped by Fuse can still be used through <a href="#native-interop">Native Interop</a></b>

<table>
	<thead>
		<tr>
			<th></th>
			<th><b>iOS</b></th>
			<th><b>Android</b></th>
		</tr>
	</thead>

	<tbody>
		<tr>
			<td>
				<b>Parse (JavaScript SDK)</b>
				<p>Data, push and analytics for your app through cliend-side JS.</p>
			</td>
			<td class="feature__mature">Works as-is</td>
			<td class="feature__mature">Works as-is</td>
		</tr>

		<tr>
			<td>
				<b>Facebook SDK Wrapper</b>
				<p>Basic features from the Facebook SDKs exposed as FuseJS APIs and UX Markup components.</p>
			</td>
			<td class="feature__planned">Planned</td>
			<td class="feature__planned">Planned</td>
		</tr>
		<tr>
			<td>
				<a href="https://github.com/bolav/fuse-sqlite">SQLite Wrapper</a>
				<p>Local on-device database wrapper.<em>The current experimental implementation is provided by a third party, full integration into Fuse is planned.</em></p>
			</td>
			<td class="feature__experimental">Experimental</td>
			<td class="feature__experimental">Experimental</td>
		</tr>
	</tbody>
</table>

> Missing something? Please make a <a href="https://www.fusetools.com/community/forums/feature_requests">feature request in our forums</a>.



## Real-time Graphics Effects

Real-time graphics effects takes advantage of Fuse’s powerful Graphics Mode to apply GPU effects to UI elements in real-time. This gives designers a new realm of new possibilities to shape visual expressions, animations and transitions.

<table>
	<thead>
		<tr>
			<th></th>
			<th><b>Graphics Mode</b></th>
		</tr>
	</thead>

	<tbody>
		<tr>
			<td>
				<b>Blur</b>
				<p>Apply real-time blur to any UI element.</p>
			</td>
			<td class="feature__mature">Mature</td>
		</tr>
		<tr>
			<td>
				<b>DropShadow</b>
				<p>Put real-time soft drop shadows on any UI element.</p>
			</td>
			<td class="feature__mature">Mature</td>
		</tr>
		<tr>
			<td>
				<b>Desaturate</b>
				<p>Fade any element into grayscale as a transition or permanent effect.</p>
			</td>
			<td class="feature__mature">Mature</td>
		</tr>
		<tr>
			<td>
				<b>HalfTone</b>
				<p>Applies a configurable halftone pattern to any UI element.</p>
			</td>
			<td class="feature__experimental">Experimental</td>
		</tr>
		<tr>
			<td>
				<b>Glass</b>
				<p>Apply real-time blur and color grading to the background of any UI element to give it the appearance of 				glass.</p>
			</td>
			<td class="feature__planned">Planned</td>
		</tr>
		<tr>
			<td>
				<b>ColorCorrect</b>
				<p>Apply color correction to any UI element in real-time.</p>
			</td>
			<td class="feature__planned">Planned</td>
		</tr>
		<tr>
			<td>
				<b>Custom Shader Effects</b>
				<p>The Fuse effect system is generalized and you can easily create custom effects with Uno code. Fork one of the existing effects and or extend the [Fuse.Effects](/learn/uno/api/fuse/effects/effect) class to create GPU shader effects.</p>
			</td>
			<td class="feature__mature">Mature</td>
		</tr>
	</tbody>
</table>


> Note that GPU effects have a performance cost and all combinations and parameters might not be suitable for all devices. However, applied with care all effects can be done in 60 FPS even on low-end devices. It is not recommended to use effects for essential features in your app, as you may need to disable them on less powerful devices.



## Fuse Tools

<table>
	<thead>
		<tr>
			<th></th>
			<th><b>Status</b></th>
		</tr>
	</thead>

	<tbody>
		<tr>
			<td>
				<b>Fuse CLI Tools</b>
				<p>The `fuse` command for creating, building and previewing Fuse projects</p>
			</td>
			<td class="feature__mature">Mature</td>
		</tr>

		<tr>
			<td>
				<b>Fuse Desktop Preview</b>
				<p>Preview your apps on OS X and Windows with smooth, 60 FPS performance. Does not require the iOS or Android 			emulator/simulator.</p>
			</td>
			<td class="feature__mature">Mature</td>
		</tr>

		<tr>
			<td>
				<b>Fuse On-Device Preview</b>
				<p>Preview your apps live on physical iOS and Android devices.</p>
			</td>
			<td class="feature__mature">Mature</td>
		</tr>

		<tr>
			<td>
				<b>Fuse Monitor</b>
				<p>Gather all output and diagnostics information from apps running in preview on desktop, on device or in emulators.</p>
			</td>
			<td class="feature__experimental">Experimental</td>
		</tr>



		<tr>
			<td>
				<b>Fuse Inspector</b>
				<p>Tweak UX parameters in real-time with visual controls.</p>
			</td>
			<td class="feature__experimental">Experimental</td>
		</tr>

		<tr>
			<td>
				<b>Sketch Importer</b>
				<p>The `fuse import` command automatically creates and updates UX classes and image resources from a .sketch file.</p>
			</td>
			<td class="feature__experimental">Experimental</td>
		</tr>

		<tr>
			<td>
				<b>Fuse Profiler</b>
				<p>Visualize performance data in real-time for UX markup nodes to easily identify UI performance bottlenecks.</p>
			</td>
			<td class="feature__planned">Planned</td>
		</tr>
		
		<tr>
			<td>
				<b>Fuse Package System</b>
				<p>Share Fuse packages, components and project files more easily.</p>
			</td>
			<td class="feature__planned">Planned</td>
		</tr>
	</tbody>
</table>

# Something missing?

If there's something that's not on our feature roadmap that you feel should be, then don't be shy and please let us know by making a a <a href="https://www.fusetools.com/community/forums/feature_requests">feature request!</a> We're more than happy to hear from you.
