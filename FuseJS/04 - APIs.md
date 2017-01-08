# APIs

<!-- ## EcmaScript features

TODO for the below: write some basic inline docs & examples, then point to the MDN docs for more info

> * : May have limited functionality compared to browser implementations -->

## Polyfills

FuseJS executes in a (minimum) EcmaScript 5.1 environment on all supported platforms.
There is no web browser involved, FuseJS only provides a subset of the browser's standard libraries. FuseJS provides polyfills for some features (typically browser features) that are mainly provided to make $(third party libraries) work. These implementations are not neccessarily complete at this point so please report any bug and feature requests in the [forums](https://www.fusetools.com/community/forums).

## $(fetch)

This is the main way to do HTTP requests.

This is an example on how to take a JavaScript object, POST it as JSON and receive JSON data which is turned back into a JavaScript object using `fetch`:

	var status = 0;
	var response_ok = false;

	fetch('http://example.com', {
		method: 'POST',
		headers: { "Content-type": "application/json"},
		body: JSON.stringify(requestObject)
	}).then(function(response) {
		status = response.status;  // Get the HTTP status code
		response_ok = response.ok; // Is response.status in the 200-range?
		return response.json();    // This returns a promise
	}).then(function(responseObject) {
		// Do something with the result
    }).catch(function(err) {
    	// An error occured parsing Json
	});

* Complete documentation for [`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)


> ### $(XMLHttpRequest)

FuseJS supports `XMLHttpRequest`, go here for more information:

* https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest for more info


> ### $(Promise)

FuseJS has support for promises, go here for more information:

* [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

<!-- TODO: Cleanup the difference between polyfill and API -->

> ### $(setTimeout)

For the sake of compatibility with javascript APIs that rely on functions from standard browser APIs, we include `setTimeout` as a way to schedule tasks. To call a function in 2 seconds:

```
setTimeout(function() { alert("Alert"); }, 2000);
```

Please note that `FuseJS/Timer` is the preferred way of doing this, and that `setTimeout` is only included for the sake of compatability.

You can also use `setTimeout` to create loops:

	function poller() {
		// Do work (...)

		// And again in 1 second
		setTimeout(poller, 1000);
	}

* [`setTimeout`](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setTimeout)

## Storage

The storage API allows you to save text to files in the application directory.

	var storage = require('FuseJS/Storage');

Please note that while debugging inside Windows / OSX machines the files will be stored according to the system application data.

* **Windows**: `C:Users\(user name)\AppData\Local`.
* **OSX**: `~/.local/share/`.

### $(storage.write:write)

Write a string value to specified file.

	storage.write(filename, value)

* `filename` - Desired name of the file
* `value` - The content that will be written to the file

Returns a `Promise` with a `bool` which tells if the content was successfully written.

	storage.write("filename.txt", "filecontent");

### $(Storage.read:read)

	storage.read(filename)

* `filename` - The file that should be read

Returns a `Promise` with a `string` containing the content of the file.

	storage.read("filename.txt").then(function(content) {
		console.log(content);
	}, function(error) {
		console.log(error);
	});


The actual folder where the file will be saved will vary depending on platform.

### `writeSync`

Synchrounously write data to the application folder, returning `true` on a successful write:

	var wasWritten = storage.writeSync("filename.txt", "filecontent");

> Warning: This call will block, if you are writing large amounts of data, use @(Storage.write).

### `readSync`

Synchronously read data from a file in the application folder:

	var data = storage.readSync("filename.txt");

> Warning: This call will block, if you are reading large amounts of data, use @(Storage.read).

### `deleteSync`

Delete a file from the application folder, returning `true` on a success:

```
var wasDeleted = storage.deleteSync("filename.txt");
```

## Bundle

The bundle API allows you to read files that is bundled with the application defined in the project file (using `<somename.extension>:Bundle`).

	var bundle = require('FuseJS/Bundle');

### $(Bundle.read:read)

	bundle.read(fileName)

* `fileName` - Name of the bundled file that should be read

Returns a `Promise` with a `string` containing the content of the file.

	bundle.read("bundlename.json").then(function(content) {
		console.log(content);
	}, function(error) {
		console.log(error);
	});

### `readSync`

Synchronously read data from a bundled file:

	var data = bundle.readSync("bundlename.json");

> Warning: This call will block, if you are reading large amounts of data, use @(Bundle.read).

## Environment

The Environment API let's you check which platform the code is running on and implement different code paths or features accordingly.

```
var Environment = require('FuseJS/Environment');

if(Environment.iOS)		{console.log("Running on iOS");}
if(Environment.android)	{console.log("Running on Android");}
if(Environment.preview)	{console.log("Running in preview mode");}
if(Environment.mobile)	{console.log("Running on iOS or Android");}
if(Environment.desktop)	{console.log("Running on desktop");}
```

## $(Timer)

You can schedule tasks to be executed after specific time using the `Timer` API:

```
var Timer = require('FuseJS/Timer');
var timer = Timer.create(function(){
	doTask();
}, 1000, true);
```

`Timer.Create` requires three arguments:

* The function to be run when the timer has reached the specified time.
* The amount of time to wait before running the function, in milliseconds.
* If the timer repeats or not. `true` will repeat the timer.

When called, it will return an id corresponding to the created timer. This can be used to delete the timer, like this:

```
Timer.delete(timer);
```


## $(Lifecycle)

You can react to lifecycle events using JavaScript:

	var lifecycle = require('FuseJS/Lifecycle');
	lifecycle.onEnteringForeground = function() {
  		initialize();
	};

The following lifecycle events will be raised:

The app start event is implicit as this is when your js code is first evaluated.

- $(onEnteringForeground) - The app has left the suspended state and now is running. You will get this event when the app starts.
- $(onEnteringBackground) - The app is leaving the running state and is about to be suspended.
- $(onEnteringInteractive) - The app is entering a state where it is fully focused and receiving events.
- $(onExitedInteractive) - The app is partially obscured or is no longer the focus (e.g. when you drag open the notification bar)

See a [complete code example](https://github.com/fusetools/fuse-samples/tree/master/Samples/Lifecycle).

## $(Phone)

You can use the Phone-API to request a $(Phone.call:call) initiation:

	var phone = require('FuseJS/Phone');
	phone.call("number");

## Camera

Allows you to take pictures with the device's camera.

	var camera = require('FuseJS/Camera');

Use it like so:

	camera.takePicture({ targetWidth: 640, targetHeight: 360}).then(function(file)
	{
		// file is a standard JavaScript File object
		// file.path contains the path to the saved image on the device
	}).catch(function(e) {
		console.log(e);
	});

You can optionally add a `correctOrientation`-value to the original request if you don't want the call to automatically rotate the picture according to EXIF data. This will be done by default.

### `takePicture` function

Takes a picture with the device's camera.

	camera.takePicture({ targetWidth: width, targetHeight: height, correctOrientation: shouldCorrect })

The option object can contain the following properties:

* `targetWidth` - Desired width of the picture, in pixels. If omitted, the default is used.
* `targetHeight` - Desired height of the picture, in pixels. If omitted, the default is used.
* `correctOrientation` - True if you want to correct orientation of the picture

The method returns a `Promise` that if successful yields a `File` object that points to the
image saved as a JPG image on disk.

Note that the `targetWith` and `targetHeight` are just hints, the returned image can be any size.

## Vibration

Allows you to use the devices vibration functionality.

	var vibration = require('FuseJS/Vibration');

User it like so:

	vibration.vibrate(0.8);

Vibrates for 0.8 seconds.

<!-- TODO: Document localstorage -->


## InterApp

The InterApp module is designed to simplify communication between apps.

	var interApp = require('FuseJS/InterApp');

Right now it handles two cases:

- Request launching another app with a Uri
- Receiving Uri launch requests from other apps

This is exposed via a method and a callback.

### launchUri(uri)

Call this method with a string and fuse will request the system to open the app that handles that Uri.

```
interApp.launchUri ('purple://some/uri')
```

### onReceivedUri(uri)

Is a callback telling you if another app launches your app with a Uri. To receive this first you need to give your app a custom Uri scheme. We can do this easily by adding the "UriScheme" element to the "Mobile" section of the unoproj file.

After you have done that your "Mobile" section will look something like this

```
"Mobile": {
  "UriScheme": "YourScheme",
  ...
},
```

From now on, any time a Uri is opened on the device starting with `YourScheme://` your app will be opened and the `onReceivedUri` callback will be fired with the full Uri.

If your app is already open `onReceivedUri` will just fire. Your app may momentarily leave interactive mode, but this is perfectly normal.

To use the callback in your javascript code you can simply do the following:

```
var interApp = require('FuseJS/InterApp');

interApp.onReceivedUri = function(uri) {
    console.log ("js recieved Uri: " + uri);
};
```

## $(UserEvents-js:UserEvents)

The `UserEvents` module allows you to raise @(User events:user events) from JavaScript.

	var UserEvents = require('FuseJS/UserEvents');

	// Raise an event
	UserEvents.raise("MyEvent");

	// Raise an event with arguments
	UserEvents.raise("MyEvent", {
		message: "Hello from JS!"
	});

At the moment events raised from JavaScript are global only, and not associated with a node.
We are working to correct this, however in the meantime you need to put a global filter on the handler to respond to an event raised from JavaScript:

	<OnUserEvent Name="MyEvent" Filter="Global">
		...
	</OnUserEvent>

_Note_: User events can also be responded to from within JavaScript, see @(OnUserEvent).


## Geolocation

Get the geographic location of the user.

```
var geoLocation = require('FuseJS/GeoLocation');
```

The location API has three different ways of getting the device location, as described in the sections further down.
All of them result in a location object that has the following format:

```
{
    latitude: 59.91149,
    longitude: 10.740902,
    accuracy: 11
}
```

### location

If this or some other application recently requested a location update, this property will immediately get the last known position. If no location data is available this property returns `null`.

```
if(geoLocation.location != null)
    console.log(JSON.stringify(geoLocation.location));
```

### Single update: getLocation(timeout)

Get most accurate position possible within the given timeout.

```
var timeout = 2000;
geoLocation.getLocation(timeout).then(function(location) {
    console.log(JSON.stringify(location));
});
```

### Continuous updates: onChanged, startListening, stopListening

Start listening for position changes continuously using the specified settings.
Keep in mind that getting a lot of location data with a short interval and high accuracy will drain the battery fast.

```
geoLocation.onChanged = function(location) {
    console.log(JSON.stringify(location));
};

var minimumReportInterval = 1000, desiredAccuracyInMeters = 10;
geoLocation.startListening(minimumReportInterval, desiredAccuracyInMeters);

function stop() {
   geoLocation.stopListening();
}
```
