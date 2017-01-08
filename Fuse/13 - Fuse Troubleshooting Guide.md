# $(Fuse Troubleshooting Guide)

## General tips

### Upgrading Fuse
- When you have upgraded Fuse since the last time you built a project, clean the project before building (run `uno clean` in a terminal in the project folder).

## Logs
When investigating issues it's often useful to check the logs written by Fuse and the editor plugins. These can be be found in:

- OS X: `~/.fuse/logs`
- Windows: `%localappdata%\Fusetools\Fuse\logs`

## Connection issues

### A network error occurred

You get a message saying `fuse: A network error occurred: Could not resolve host '<your hostname>' Please check your network setup and try again.`

#### Solution

There is an issue with your network. Try doing `ping $(hostname)` in a terminal. If you get an error message, you have to fix your network setup before you continue.

### FailedToConnectToDaemon

You get a stack trace with `FailedToConnectToDaemon` somewhere in it
 
#### Solution

- First, try to stop the Fuse daemon.
 - Windows: Right click on the Fuse icon in the tray, click "Exit"
 - OS X: Control-click on the Fuse icon in the menu bar, click "Quit"
- A fresh daemon will then start automatically the next time you interact with Fuse.
- If that did not help, please try logging out of and back in to Windows / OS X.
- If that did not help, please try to reboot your computer.

### Preview - Failed to connect

While previewing on an iOS or Android device, you get the message "Failed to connect"

#### Solution
- Make sure your device has WiFi enabled
- Make sure your device is connected to the same WiFi as the computer running Fuse 
- If your computer is running a firewall (such as Windows Firewall), make sure Fuse is allowed to accept incoming connections
- If you have tethering enabled on your Android device (sharing the mobile network over USB), try disabling it
- If you still have the problem, quit Fuse from the tray / menu bar icon and re-start preview

## Cannot preview or export to Android

### Symptoms
- The Android build gets stuck at "Trying to uninstall existing version of APK"
- The build finishes with "ERROR: No android devices found."

#### Solution
- Check that the device is connected with a USB cable
- Check that usb debugging is enabled on your device. Doing this can differ between different devices and OS versions so you may have to search for the specific instructions for your device, but the second point [here](http://developer.android.com/tools/device.html#setting-up) is a good place to start.
- Check that your device shows up in `adb devices`. `adb` can usually be found in
 - `C:\ProgramData\Uno\SDKs\AndroidSDK\platform-tools` (Windows)
 - `/usr/share/uno/SDKs/AndroidSDK/platform-tools` (OS X)
- If you are on Windows you should check that you have the latest USB drivers for your device. You can read more about this [here](http://developer.android.com/tools/extras/oem-usb.html)

## Cannot build for iOS

### Symptoms
- The iOS build stops at ```Code Sign error: No code signing identities found: No valid signing identities (i.e. certificate and private key pair) were found.```

#### Solution
- Start Xcode
- Create a new, blank iOS project and try to build it
- When the error dialogue with ```No provisioning profiles matching an applicaple signing identity were found.``` appears, click `Fix Issue` and let the wizard complete
- Close Xcode
- Now re-run your build/preview from Fuse

## Sublime plugin does not work

### Symptoms

- You get the message "Error loading: Packages/Fuse/UX.tmLanguage"
- You don't get syntax highlighting in Sublime

#### Solution

- Open the dashboard, click "Sublime Text Setup" and follow the wizard.
- If that didn't work, delete the files staring with `Fuse` in `%APPDATA%\Sublime Text 3\Installed Packages` (Windows) or `~/Library/Application Support/Sublime Text 3/Installed Packages` (OS X), and try the wizard again.

## My Uno code is not run/updated in preview

### Symptoms

- Changes to your Uno code is not reflected in preview
- Your Uno code is not executed in preview

#### Explanation

- Unlike ux/js, Uno-code is not refreshed when you save and auto-refresh preview, you'll have to restart Fuse. A better solution for this is coming soon.
 - Windows: Right click on the Fuse icon in the tray, click "Exit"
 - OS X: Control-click on the Fuse icon in the menu bar, click "Quit"
- In addition, any Uno-code you have in `ux.uno` files is not run at all in preview.
- All in all, it can be easier to work with Uno code through `uno build` than through preview. A better solution for this is coming soon.

## Preview says "Oops! Something went wrong here"

When previewing your app, the "Oops! Something went wrong here" screen appears.

#### Solution

- Open the Fuse [Monitor](https://www.fusetools.com/learn/guides/preview-and-export-monitor) to see details about the problem.

## Local preview does not start on Windows

If the console output contains `GL_VERSION: 1.1.0` and `GL_RENDERER: GDI Generic` the problem is most likely missing / outdated OpenGL drivers. Upgrade to the most recent drivers for your graphics adapter and try again.

This problem can also be triggered by driver issues under Windows 10 specific to the Intel HD Graphics 2000 / 3000 / 4000 graphics adapters. In this case you will not be able to do local preview with instant updates, but you can still test your app on the PC by doing a regular build: `fuse build -t=dotnetexe --run`

You can of course also still use [preview on Android and iOS devices](https://www.fusetools.com/learn/guides/preview-and-export-device-preview)
