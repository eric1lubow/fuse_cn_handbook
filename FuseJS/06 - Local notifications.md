# Local Notifications

Sometimes you need to alert your user to an event in your app even when your app is not running in the foreground. For this most mobile devices have some concept of Notifications. Here we are going to discuss 'Local Notifications' which are notifications scheduled from the app itself. 'Push Notifications' are notifications sent from a server elsewhere and are covered in the next chapter.

As with many of our bindings over OS features we like to start with a light API and build up, We are very interested in comments & requests so do drop by the forums and let us know.

## Getting Set Up

Include the Fuse local notification library by adding the following to your `.unoproj` file

    "Packages": [
        ...
        "Fuse.LocalNotifications",
        ...
    ],

This is enough to start using this feature in your apps. So let's look at that now.


## App Example

Here is a full Fuse app that uses Local Notifications:

    <App Theme="Basic" ClearColor="1, 1, 1, 1">
        <FuseJS.LocalNotify ux:Global="LocalNotify" />
        <JavaScript>
            var LocalNotify = require("LocalNotify");

            LocalNotify.onReceivedMessage = function(payload) {
                console.log ("Recieved Local Notification: " + payload);
            };

            function sendLater() {
                LocalNotify.later(4, "Finally!", "4 seconds is a long time", "hmm?", true);
            }

            function sendNow() {
                LocalNotify.now("Boom!", "Just like that", "payload", true);
            }

            module.exports = {
                sendNow: sendNow,
                sendLater: sendLater
            };
        </JavaScript>
        <DockPanel>
            <TopFrameBackground DockPanel.Dock="Top" />
            <ScrollViewer>
                <StackPanel>
                    <Button Clicked="{sendNow}" Text="Send notification now" Height="60"/>
                    <Button Clicked="{sendLater}" Text="Send notification in 4 seconds" Height="60"/>
                </StackPanel>
            </ScrollViewer>
            <BottomBarBackground DockPanel.Dock="Bottom" />
        </DockPanel>
    </App>

Let's break down what is happening here.

## How it works

We will skip the `module.exports` and stuff inside the `DockPanel` as that is better explained in other guides. Let's instead go through the JS.

After `require`ing our module like normal we set up a function this will deliver a notification 4 seconds in the future.

    function sendLater() {
        LocalNotify.later(4, "Finally!", "4 seconds is a long time", "hmm?", true);
    }

The `later` function take the following parameters:
- `secondsFromNow`: How long in seconds until the notification fires
- `title`: the `string` that will be the title in the notification when it shows in your device's notification bar
- `body`: the `string` that will be the body of the notification when it shows in your device's notification bar
- `payload`: a string that is not shown in the notification itself, but will be present in the callback.
- `sound`: a bool saying whether the device should make the default notification sound when it is shown in the notification bar
- `badgeNumber`: An optional parameter that is only used on iOS to put a badge number against the apps icon. This is often used for showing the quantity of 'things' that need the user's attention. For example an email app could show the number of unread emails.


    function sendNow() {
        LocalNotify.now("Boom!", "Just like that", "payload", true);
    }

The `now` function is almost identical to the `later` function, except that it doesnt have the `secondsFromNow` parameter.


Finally we set up the function that will be called whenever we get a notification.

    LocalNotify.onReceivedMessage = function(payload) {
        console.log ("Recieved Local Notification: " + payload);
    };

The `payload` will be a string in JSON format containing the following keys:
- `'title'`: the value is the notification's title as a string
- `'body'`: the body text of the notification as a string
- `'payload'`: the string of data that was sent with the notification.


## Lifecyle Behaviour

How your notification is treated by the OS depends on the state of the app. If the app is `Interactive` then the notification does no appear but is instead delivered straight to your running app. If it is not interactive then the OS will create a notification based on the parameters you gave to the `later` or `not` functions. `Interactive` means that not only is your app in the `Foreground` but it is also not being obscured by other windows. One example of being in the `Foreground` and not `Interactive` is when you swipe the status-bar to open the 'Notification Center/Drawer'.

You can try this with the example app above, hit the `Send notification in 4 seconds` button an open the 'Notification Center/Drawer'
