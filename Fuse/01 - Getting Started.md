<documentProperties pageTitle="Learn Fuse" />

# $(Getting Started)

Getting up and running with Fuse is super easy. First of all, [download the latest version of Fuse!](https://www.fusetools.com/downloads)

## $(What is Fuse?)

Fuse is a toolkit that developers and designers use to create native, cross-platform mobile apps for iOS and Android.

You express your user interfaces in markup (which we call "UX") which makes it easy to make animated and responsive UIs. Your app logic is written in JavaScript which is interpreted, and **all rendering is compiled to native code for optimal performance.** You can choose between real native UI elements and customizable cross platform elements. Fuse is not a "black box", but a flexible toolkit and can be extended using Uno (a C#-dialect). Fuse does _not_ generate HTML5/hybrid apps, it exports fully native mobile apps.

While you're developing your app, **your changes are reflected in real time** both on your computer and on your devices, with **no need to recompile or re-deploy.** While it's wonderful to prototype in Fuse, it is _not_ a prototyping tool, it is a fully-fledged app development tool that needs no further coding in XCode or Android studio. Apps built purely with Fuse are already in app stores today, and more and more apps are being developed every day.

## $(Setup)

We're always working hard on making this process smooth as silk, but occasionally things can still go wrong. If something iffy happens, please [let us know!](https://www.fusetools.com/contact)

Setup instructions for [OS X](https://www.fusetools.com/developers/guides/setup/install-osx) and [Windows](https://www.fusetools.com/developers/guides/setup/install-win). There's also these [uninstall instructions for OS X](https://gist.github.com/Tapped/daa78c08882f33b0c7c3) if you need them.

## Tutorial

We suggest you start by [following our tutorial](https://www.fusetools.com/developers/guides/tutorial). You can also find a wide range of ready-to-run examples [on our Examples-page](https://www.fusetools.com/examples), as well as a (constantly growing) list of [tutorials on Youtube](https://www.youtube.com/playlist?list=PLdlqWm6b-XALJgM3fGa4q95Yipsgb8Q1o).

## $(Project structure)

After having created a new project, either by using the dashboard or the `fuse` command line command, you will find two files in the project directory:

- `ProjectName.unoproj` - This is the project file, and basically keeps track of which files compose the project, which packages it depends on and also other handy values like your API keys.
- `MainView.ux` - This is the main starting point for your app, mainly because it contains the `App`-tag. Under normal circumstances you will delete most of the contents of this file, but feel free to examine the default application and see what is needed to make a bare bones surface with some controls.

Head to the @(Uno Project Format Reference) for a complete reference of the `unoproj` format.

Note: JavaScript files do not need to be referenced from the `unoproj`-file as JavaScript files are referenced directly from UX markup.

> ### $(Sublime Text projects)

When you drop a folder into Sublime Text 3, it will by default search through all files in all subfolders. When building a Fuse project, this isn't always what you want.

If you create a file called `ProjectName.sublime-project`, you can drop this into it to make it ignore the `.uno` and `build` directories:

```
{
	"folders":
	[
		{
			"folder_exclude_patterns":
			[
				"build",
				".uno",
			],
			"path": "."
		}
	]
}
```

This file can then be opened from the `Project` -> `Open Project...`-dialog.

> ### Git

If you are using Git for version control, you can put the following in your .gitignore file.

	build
	.uno

## Preview

Live preview is a key feature of Fuse. You can live preview simultaneously on multiple devices (and in the desktop simulator), so you no longer need to build your project for specific devices between edits; just save and they will appear instantly on all of your devices.

Follow these instructions to [enable iOS preview](https://www.fusetools.com/developers/guides/previewandexport/devicepreview) and [Android preview](https://www.fusetools.com/developers/guides/previewandexport/devicepreview).

Starting a preview of your project on your desktop can be done in a couple of ways. If you have Sublime Text 3 installed with the supported plugin, you can right click on the UX-file and select "Begin Fuse preview" and select the "Local" option. Preview will then open the simulator, and it will sync automatically as you edit your files.

Alternatively, you can navigate on the command line to your project root directory and type `fuse preview`.

Read more about using [Fuse preview on the desktop](https://www.fusetools.com/developers/guides/previewandexport/toolpreview).

## Export

When running preview, Fuse will create a shell application on your device that connects to the Fuse daemon running on your system to quickly display changes you make to the project. However, if you want to bring the device with you to a meeting or to show people away from your development environment, you will need to export the project to the device you are targeting.

> ### Exporting to iOS

For iOS, you need to have a machine running OS X and Xcode installed (as for preview). You also need an Apple developer account. In the project root, simply type:

`fuse build --target=iOS --run` to build and run the app.

If you instead want to open the generated project in Xcode you simply do `fuse build --target=iOS -adebug`

> ### Exporting to Android

You must first have installed [the Android SDK/NDK packages](https://www.fusetools.com/learn/guides/preview-and-export-android) (just as you would for preview). You can then go to the project root and type:

`fuse build --target=Android --run`

This will deploy and start the project on your connected Android device.
