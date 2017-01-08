# $(Uno Project Format Reference)

A Uno project (`.unoproj`) is a JSON document that describes your build configuration.

Uno Project files can be edited through the Fuse desktop tools, by the `fuse` command line tool, or by editing the project file by hand in a text editor.

## $(Unoproj.Macros:Macros)

The uno project format supports a basic macro system.
This feature is used to implement many of the top-level properties, such as @(Unoproj.Title:Title) and @(Unoproj.Version:Version).

For instance, say you set the `Version` property as follows:

```
{
	"Version": "0.9.4"
}
```

By default, both @(Unoproj.Android.VersionName:Android.VersionName) and @(Unoproj.iOS.BundleVersion:iOS.BundleVersion) are set as follows:

```
{
	"Android": {
		"VersionName": "$(Version)"
	},
	"iOS": {
		"BundleVersion": "$(Version)"
	}
}
```

This will make those properties inherit the top-level `Version` property.

## $(Unoproj.Includes:Includes) and $(Unoproj.Excludes:Excludes)

The `Includes` and `Excludes` properties lets you control which files to include in your project. They are both arrays of strings, as shown in the snippet below:

```
{
	"Includes": [
		"*.ux",
		"js/**/*.js",
		"SomeUnoClass.uno:Source",
		"ForeignCode.java:Java:android"
	],
	"Excludes": [
		"js/ExcludeThisFilePlease.js",
		"node_modules/"
	]
}
```

An include/exclude entry can take one of the following forms:

### `GlobPattern`

A glob pattern, as described further down in this document.

```
{
	"Includes": [
		"*.ux",
		"js/**/*.js",
		"Foo.uno"
	]
}
```

The following glob features are supported:

- Brace expansion
- Extended glob matching
- Globstar matching

Patterns that do not contain a `/` are matched recursively:

|     Pattern                    |                 Matches                                      |
|-------------------------------:|:-------------------------------------------------------------|
|                            `*` | Matches all files                                            |
|                        `*.foo` | Matches all files whose name ends in `.foo`                  |
|                        `*.bar` | Matches all files whose name ends in `.bar`                  |
| <code>*.+(bar&#124;foo)</code> | Matches all files whose name ends in either `.bar` or `.foo` |
|                       `foobar` | Matches all files named `foobar`                             |

Add `/` or `./` to disable recursion:

|  Pattern    |                            Matches                            |
|------------:|:--------------------------------------------------------------|
| `/*.png`    | Matches all PNG files found directly in the project directory |
| `Foo/*.png` | Matches all PNG files found directly in the `Foo` directory   |

Use globstar (`**`) for explicit recursion:

|     Pattern    |                                Matches                              |
|---------------:|:--------------------------------------------------------------------|
| `Foo/**/*.uno` | Matches all Uno files in the `Foo` directory and its subdirectories |

### `FileName:Type`

This tells the Uno compiler to include a single file, interpreting it as a certain @(Unoproj.AllowedIncludeTypes:type).

```
{
	"Includes": [
		"MainView.ux:UX"
	]
}
```

### `FileName:Type:Condition`

In addition to a file of a certain @(Unoproj.AllowedIncludeTypes:type), you may specify a condition that will determine if the file will be included or not.

```
{
	"Includes": [
		"AndroidOnly.java:Java:Android",
		"iOSOnly.hh:ObjCHeader:iOS",
		"iOSOnly.mm:ObjCSource:iOS"
	]
}
```

### $(Unoproj.AllowedIncludeTypes:Allowed include types)

The following values may be specified as the type of an included file.

- Glob
- Folder
- File
- UX
- Source
- Bundle
- CSource
- CHeader
- ObjCSource
- ObjCHeader
- Java
- Extensions
- Stuff

## $(Unoproj.Packages:Packages)

A list of packages to include in the build. When scaffolding your application, Fuse will include a standard set of packages to get you up and running.

```
{
	"Packages": [
		"Fuse.Animations",
		"Fuse.BasicTheme",
		"Fuse.Themes",
		"Fuse.Controls",
		"Fuse.Designer",
		"Fuse.Drawing",
		"Fuse.Drawing.Primitives",
		"Fuse.Effects",
		"Fuse.Elements",
		"Fuse.Entities",
		"Fuse.Gestures",
		"Fuse.Navigation",
		"Fuse.Scripting",
		"Fuse.Shapes",
		"Fuse.Triggers",
		"Fuse.Reactive",
		"Fuse.Android",
		"Fuse.Desktop",
		"Fuse.iOS",
		"Fuse.UserEvents",
		"FuseCore",
		"Uno.Collections",
		"Uno.Geometry"
	]
}
```

## All properties

### $(Unoproj.Title:Title)

The user-readable title of your app. Defaults to `\$(Name)`.

*unrealisticallyLongNameForAnApp.unoproj*:

```
{
	"Title": "ShorterName"
}
```

### $(Unoproj.Description:Description)

User-readable description of your app. Empty by default.

```
{
	"Description": "An app that helps you organize, track and analyze cat pictures on the internet."
}
```

### $(Unoproj.Copyright:Copyright)

Your app's copyright notice. Defaults to `Copyright (C) <current year> \$(Publisher)`.

```
{
	"Copyright": "Copyright © 2003-2016 \$(Publisher)"
}
```

### $(Unoproj.Publisher:Publisher)

The legal entity in charge of publishing the application. Defaults to `[Publisher]`.

```
{
	"Publisher": "VeryBusinessCorp Inc."
}
```

### $(Unoproj.Version:Version)

The current version of your app,  Defaults to `0.0.0`.

```
{
	"Version": "1.2.9"
}
```

### $(Unoproj.VersionCode:VersionCode)

Some platforms (currently only Android) want an integral version code in addition to a version string. Further documentation can be found [here](http://developer.Android.com/tools/publishing/versioning.html#appversioning). Defaults to `0`.

```
{
	"VersionCode": 125
}
```

### $(Unoproj.RootNamespace:RootNamespace)

The root namespace to use. Defaults to `\$(QIdentifier)`.

```
{
	"RootNamespace": "MyCompany.MyApp"
}
```

### $(Unoproj.BuildDirectory:BuildDirectory)

Where to place temporary files and executables for various build configurations and targets. Defaults to `build`.

```
{
	"BuildDirectory": "output"
}
```

### $(Unoproj.OutputDirectory:OutputDirectory)

Where to place temporary files and executables for *the current build target and configuration*.
Defaults to `\$(BuildDirectory)/\@(Target)/\@(Configuration)`.

```
{
	"OutputDirectory": "\$(BuildDirectory)/\@(Target)/\@(Configuration)"
}
```

### $(Unoproj.CacheDirectory:CacheDirectory)

Where to place Uno's cache files. Defaults to `.uno`.

```
{
	"CacheDirectory": ".cache"
}
```

> ## $(Unoproj.UnoCoreReference:UnoCoreReference)

Wether or not UnoCore should be referenced. You will probably never need this, as it's only used internally. Defaults to `true`.

```
{
	"UnoCoreReference": true
}
```

### $(Unoproj.Mobile:Mobile)

A dictionary of options that apply to all mobile targets.

#### $(Unoproj.Mobile.KeepAlive:Mobile.KeepAlive)

If set to `true`, the screen won't dim and eventually turn off while your app is open. Defaults to `false`.

```
{
	"Mobile": {
		"KeepAlive": false
	}
}
```

#### $(Unoproj.Mobile.ShowStatusbar:Mobile.ShowStatusbar)

Wether or not to show the status bar. Defaults to `true`.

```
{
	"Mobile": {
		"ShowStatusbar": true
	}
}
```

#### $(Unoproj.Mobile.RunsInBackground:Mobile.RunsInBackground)

Controls wether or not your app should continue running when the user presses the home button. Defaults to `true`.

```
{
	"Mobile": {
		"RunsInBackground": false
	}
}
```

#### $(Unoproj.Mobile.Orientations:Mobile.Orientations)

Specifies which screen orientations are allowed.

Can be one of the following values:

- `Auto` (*default*, all possible orientations)
- `Portrait`
- `PortraitUpsideDown`
- `Landscape`
- `LandscapeLeft`
- `LandscapeRight`

```
{
	"Mobile": {
		"Orientations": "Portrait"
	}
}
```

### $(Unoproj.Android:Android)

A dictionary of options that apply to Android targets only.

#### $(Unoproj.Android.ApplicationLabel:Android.ApplicationLabel)

A user-readable label for the application, specific to Android. This is the Android equivalent of the top-level @(Unoproj.Title:Title) property. Defaults to `\$(Title)`.

```
{
	"Android": {
		"ApplicationLabel": "MyFancyApp"
	}
}
```

#### $(Unoproj.Android.Description:Android.Description)

The same as @(Unoproj.Description:Description), but specific to Android. Defaults to `\$(Description)`.

```
{
	"Android": {
		"Description": "This is an Android-specific description!"
	}
}
```

#### $(Unoproj.Android.VersionCode:Android.VersionCode)

The same as @(Unoproj.VersionCode:VersionCode), but specific to Android. Defaults to `\$(VersionCode)`.

```
{
	"Android": {
		"VersionCode": 412
	}
}
```

#### $(Unoproj.Android.VersionName:Android.VersionName)

The same as @(Unoproj.Version:Version), but specific to Android. Defaults to `\$(Version)`.

```
{
	"Android": {
		"VersionName": "0.5.2"
	}
}
```

#### $(Unoproj.Android.Package:Android.Package)

The name of the java package to use for Android export. Defaults to `\$(QIdentifier)`.

```
{
	"Android": {
		"Package": "com.mycompany.myapp"
	}
}
```

#### $(Unoproj.Android.Icons:Android.Icons)

Instead of providing one uniformly sized icon to be used on all platforms, you can specify different icons for different screen densities, specific to Android. These are grouped according to Android's generalized densities, which you can read more about [here](http://developer.Android.com/guide/practices/screens_support.html#range). All of them default to `\$(Icon)`.

*Note:* This only applies to Android, however you can achieve the same on iOS using @(Unoproj.iOS.Icons:iOS.Icons).

```
{
	"Android": {
		"Icons": {
			"LDPI": "Icon-ldpi.png",
			"MDPI": "Icon-mdpi.png",
			"HDPI": "Icon-hdpi.png",
			"XHDPI": "Icon-xhdpi.png",
			"XXHDPI": "Icon-xxhdpi.png",
			"XXXHDPI": "Icon-xxxhdpi.png"
		}
	}
}
```

#### $(Unoproj.Android.Key:Android.Key)

See @(Signing for Android).

#### $(Unoproj.Android.Geo.ApiKey:Android.Geo.ApiKey)

Your Google Maps API key, for use with @(MapView). More info can be found under @(Maps on Android).

```
{
	"Android": {
		"Geo": {
			"ApiKey": "<your Google Maps API key>"
		}
	}
}
```

#### $(Unoproj.Android.NDK.PlatformVersion:Android.NDK.PlatformVersion)

The NDK platform version to use. Defaults to `9`.

```
{
	"Android": {
		"NDK": {
			"PlatformVersion": 9
		}
	}
}
```

#### $(Unoproj.Android.SDK:Android.SDK)

Specifies version constraints for the Android SDK to build against.
The following example shows the default values of each property.

```
{
	"Android": {
		"SDK": {
			"BuildToolsVersion": "23.0.0",
			"CompileVersion": 19,
			"MinVersion": 10,
			"TargetVersion": 19
		}
	}
}
```

#### $(Unoproj.iOS.BundleIdentifier:iOS.BundleIdentifier)

The iOS bundle identifier.
Defaults to `\$(QIdentifier)`.

Corresponds to [CFBundleIdentifier](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/20001431-102070).

```
{
	"iOS": {
		"BundleIdentifier": "com.mycompany.myapp"
	}
}
```

#### $(Unoproj.iOS.BundleName:iOS.BundleName)

A user-readable label for the application specific to iOS. This is the iOS equivalent of @(Unoproj.Android.ApplicationLabel:Android.ApplicationLabel).
Corresponds to [CFBundleName](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-109585).
Defaults to `\$(Title)`.

```
{
	"iOS": {
		"BundleName": "MyAwesomeApp"
	}
}
```

#### $(Unoproj.iOS.BundleVersion:iOS.BundleVersion)

The same as @(Unoproj.Version:Version), but specific to iOS.
Defaults to `\$(Version)`.

Corresponds to [CFBundleVersion](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/20001431-102364).

```
{
	"iOS": {
		"BundleVersion": "0.5.2"
	}
}
```

#### $(Unoproj.iOS.DeploymentTarget:iOS.DeploymentTarget)

The minimum iOS version your app can run on.
Defaults to `8.0`.

```
{
	"iOS": {
		"DeploymentTarget": "8.0"
	}
}
```

#### $(Unoproj.iOS.Icons:iOS.Icons)

Instead of providing one uniformly sized icon to be used on all platforms, you can specify different icons for different sizes and screen densities, specific to iOS.
All of them default to `\$(Icon)`.

Corresponds to [CFBundleIconFiles](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW10).

*Note:* This only applies to iOS, however you can achieve the same on Android using @(Unoproj.Android.Icons:Android.Icons).

```
{
	"iOS": {
		"Icons": {
			"iPhone_29_2x": "Icon-iPhone-29@2x.png",
			"iPhone_29_3x": "Icon-iPhone-29@3x.png",
			"iPhone_40_2x": "Icon-iPhone-40@2x.png",
			"iPhone_40_3x": "Icon-iPhone-40@3x.png",
			"iPhone_60_2x": "Icon-iPhone-60@2x.png",
			"iPhone_60_3x": "Icon-iPhone-60@3x.png",
			"iPad_29_1x": "Icon-iPad-29@1x.png",
			"iPad_29_2x": "Icon-iPad-29@2x.png",
			"iPad_40_1x": "Icon-iPad-40@1x.png",
			"iPad_40_2x": "Icon-iPad-40@2x.png",
			"iPad_76_1x": "Icon-iPad-76@1x.png",
			"iPad_76_2x": "Icon-iPad-76@2x.png"
		}
	}
}
```

#### $(Unoproj.iOS.LaunchImages:iOS.LaunchImages)

Specifies launch images of different sizes to be used on iOS.
Corresponds to [UILaunchImages](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28).

```
{
	"iOS": {
		"LaunchImages": {
			"iPhone_Portrait_2x": "...",   // 640x960
			"iPhone_Portrait_R4": "...",   // 640x1136
			"iPhone_Portrait_R47": "...",  // 750x1334
			"iPhone_Portrait_R55": "...",  // 1242x2208
			"iPhone_Landscape_R55": "...", // 2208x1242
			"iPad_Portrait_1x": "...",     // 768x1024
			"iPad_Portrait_2x": "...",     // 1536x2048
			"iPad_Landscape_1x": "...",    // 1024x768
			"iPad_Landscape_2x": "..."     // 2048x1536
		}
	}
}
```

#### $(Unoproj.iOS.PList:iOS.PList)

A dictionary of entries that will be included in the `Info.plist` file of the iOS bundle. Note that not all PList entries will work – only the ones that are included here.
<!-- TODO: Something about this not working for any case, only the below keys -->

##### $(Unoproj.iOS.PList.MKDirectionsApplicationSupportedModes:iOS.PList.MKDirectionsApplicationSupportedModes)

An array of strings, specifying the modes of transportation for which the app is capable of giving map directions.
Reference can be found [here](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW33).

```
{
	"iOS": {
		"PList": {
			"MKDirectionsApplicationSupportedModes": [
				"MKDirectionsModeCar",
				"MKDirectionsModeBus"
			]
		}
	}
}
```

##### $(Unoproj.iOS.PList.UIRequiredDeviceCapabilities:iOS.PList.UIRequiredDeviceCapabilities)

An array of strings, specifying which device-related capabilities must be available for the app to function.
Reference can be found [here](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW3).

```
{
	"iOS": {
		"PList": {
			"UIRequiredDeviceCapabilities": [
				"camera-flash",
				"gps"
			]
		}
	}
}
```

##### $(Unoproj.iOS.PList.NSHealthShareUsageDescription:iOS.PList.NSHealthShareUsageDescription)

A message that will be shown to the user when the app is requesting HealthKit data.

```
{
	"iOS": {
		"PList": {
			"NSHealthShareUsageDescription": "We need access to your HealthKit data because..."
		}
	}
}
```

##### $(Unoproj.iOS.PList.UIApplicationExitsOnSuspend:iOS.PList.UIApplicationExitsOnSuspend)

When `true`, the app will terminate rather than being sent to the background when the user presses the home button. Defaults to `false`.

Reference can be found [here](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW23).

```
{
	"iOS": {
		"PList": {
			"UIApplicationExitsOnSuspend": false
		}
	}
}
```

##### $(Unoproj.iOS.PList.UIFileSharingEnabled:iOS.PList.UIFileSharingEnabled)

Specifies wether the app can share files through iTunes when connected to a computer.
Defaults to `false`.

Reference can be found [here](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW20).

```
{
	"iOS": {
		"PList": {
			"UIFileSharingEnabled": true
		}
	}
}
```

##### $(Unoproj.iOS.PList.UINewsstandApp:iOS.PList.UINewsstandApp)

Specifies wether the app presents its contents in the iOS Newsstand app. Defaults to `false`.

Reference can be found [here](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW25).

```
{
	"iOS": {
		"PList": {
			"UINewsstandApp": true
		}
	}
}
```

##### $(Unoproj.iOS.PList.UIPrerenderedIcon:iOS.PList.UIPrerenderedIcon)

```
{
	"iOS": {
		"PList": {
			"UIPrerenderedIcon": true
		}
	}
}
```

##### $(Unoproj.iOS.PList.UISupportedExternalAccessoryProtocols:iOS.PList.UISupportedExternalAccessoryProtocols)

An array of strings specifying which external accessory protocols your app is capable of communicating over. Defaults to `[]`.

Reference can be found [here](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW4).

```
{
	"iOS": {
		"PList": {
			"UISupportedExternalAccessoryProtocols": [
				"my-external-accessory-protocol"
			]
		}
	}
}
```

<!--
##### $(Unoproj.iOS.PList.UIViewControllerBasedStatusBarAppearance:iOS.PList.UIViewControllerBasedStatusBarAppearance)

TODO: Figure out what this means for Fuse apps.
-->

##### $(Unoproj.iOS.PList.UIViewEdgeAntialiasing:iOS.PList.UIViewEdgeAntialiasing)

Specifies whether Core Animation layers use antialiasing when drawing a layer that is not aligned to pixel boundaries. Defaults to `false`.

Reference can be found [here](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW5).

```
{
	"iOS": {
		"PList": {
			"UIViewEdgeAntialiasing": true
		}
	}
}
```

## $(Signing for Android)

A Uno Project set up for signing can look like this:

```
{
	"Android": {
		"Key": {
			"Alias": "application",
			"AliasPassword": "Android",
			"Store": "release.keystore",
			"StorePassword": "Android"
		}
	}
}
```

A file named `release.keystore` is expected to be found in the same folder as the project.

This file can be created by running the following command in your shell (`keytool` is found in Android SDK):

```
keytool -genkey -v -keystore release.keystore \
    -alias application -keyalg RSA -keysize 2048 -validity 10000
```

Note that only release builds are signed using the specified key. Debug builds are automatically signed using a debug key. To do a release build, use `uno build --target=Android --configuration=Release`.

## $(Signing for iOS)

Run the following command in your shell:

```
uno build --target=iOS --configuration=Release -adebug
```

Then [follow the regular procedure for adding your signing certificate](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW1) and then [submitting your app](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/SubmittingTheApp.html#//apple_ref/doc/uid/TP40011225-CH33) to the iOS App Store.
