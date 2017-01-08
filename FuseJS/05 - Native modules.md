# $(Creating native JavaScript Modules in Uno)

`Fuse.Reactive.JavaScript` allows you to define native code modules in Uno and use them
from JavaScript. This is how all native modules (like Camera, Vibrate etc.) in FuseJS
are implemented.

A module is a JavaScript object that can be acquired through the `require` function.

## Defining a native module

To define a native module, you create an instance of `Fuse.Scripting.NativeModule`. Remember to also add a reference to *Fuse.Scripting* in your .unoproj-file if it isn't already added.

To the constructor we can pass a list of members. We can also add additional members in the
constructor through the `AddMember()` method.

Currently, two types of members are supported:

* `NativeFunction` - wraps an Uno delegate as a JavaScript function
* `NativePromise` - wraps an Uno `Promise<T>` as an EcmaScript 6 style `Promise`

Here is a simple example, exposing a custom logging function:

	using Fuse;
	using Fuse.Scripting;
	using Fuse.Reactive;

	public class MyLogModule : NativeModule
	{
		public MyLogModule()
		{
			AddMember(new NativeFunction("Log", (NativeCallback)Log));
		}

		static object Log(Context c, object[] args)
		{
			foreach (var arg in args)
				debug_log arg;

			return null;
		}
	}

To make the module available to `require()` globally in our app, we use the `ux:Global` attribute
on an instance of the module.

	<App>
		<Panel>
			<MyLogModule ux:Global="MyLogModule" />
			<JavaScript>

				var Log = require("MyLogModule").Log;

				Log("Hello from JavaScript!");

			</JavaScript>
		</Panel>
	</App>

## NativeFunction

Native functions are defined by providing a `Fuse.Scripting.NativeCallback` delegate, which
has the following signature:

	public delegate object NativeCallback(Context c, object[] args);

Where `args` is the list of arguments passed to the JS function, and `object` can be one of:

* A boxed number of type `double`, `int`, `uint` or `float`.
  You can the use `Fuse.Reactive.Marshal` class to correctly unbox to a specific desired value, e.g.

	`var number = Marshal.ToInt(args[0]);`

* A boxed `bool`
* A `string`
* A `Fuse.Scripting.Array`
* A `Fuse.Scripting.Object`
* A `Fuse.Scrpting.Function`
* A `Fuse.Scripting.Callback`
* A `Fuse.Reactive.IObservable`, if the passed object is an `Observable`
* `null` if an argument was `null` or `undefined`.

The return value for your callback follows the same rules. If you return `null`, the corresponding
JS function will return `undefined`.


## NativeEvent

Native events can be used to call back to JavaScript from Uno code. This is done by adding a `NativeEvent` to the `NativeModule`.
The `NativeEvent` can then be called using `yourNativeEvent.RaiseAsync(args)`.

The following example illustrates how you create a `NativeModule` echoes any message sent to it using a `send` function and an `onMessageReceived` event.

```
using Fuse;
using Fuse.Scripting;
using Fuse.Reactive;

public class Chat : NativeModule
{
    NativeEvent _nativeEvent;

    public Chat()
    {
        AddMember(new NativeFunction("send", (NativeCallback)SendMessage));
        _nativeEvent = new NativeEvent("onMessageReceived");
        AddMember(_nativeEvent);
    }

    object[] SendMessage(Fuse.Scripting.Context context, object[] args)
    {
        var arg = args[0] as string;
        if(arg != null)
            _nativeEvent.RaiseAsync(arg);
        else
            _nativeEvent.RaiseAsync("----");

        return null;
    }
}

```
In UX, add:
```
<Chat ux:Global="chat" />
```

The `chat` module can then be used from JavaScript like so:
```
var chat = require("chat")
function send() {
    chat.send(new Date().toString());
}

chat.onMessageReceived = function(message) {
    console.log("onMessageReceived " + message);
};
```



## NativePromise

Promises are objects that represent objects that may become available in the future, and can either succeed
or fail in retrieving that object. Examples are results of HTTP requests, dialog boxes or taking pictures
with the camera.

Native modules support wrapping Uno promises in JS promises automatically, through the `NativePromise` class.

The syntax is:

	new NativePromise<TUno, TJavaScript>(name, promiseFactory [, converter])

or

	new NativePromise<TUno, TJavaScript>(name, resultFactory [, converter])

Where:

* `TUno` is the type the promise produces in Uno
* `TJavaScript` is the type the promise produces in JavaScript. If `converter` is omitted, TUno and TJavaScript must be the same type (string, numeric or boolean).
* `name` is the name of the member in the module
* `promiseFactory` is a delegate that produces a `Future<TUno>` from an `object[]`, which is the set of arguments
   passed in from JavaScript (following the same convention as arguments to `NativeFunction`). Signature:

   `public delegate Future<TUno> PromiseFactory(object[] args);`

* `resultFactory` is a delegate that produces a `TUno` from an `object[]`.

	`public delegate Future<TUno> ResultFactory(object[] args);`

* `converter` (optional) is a method that converts `TUno` to `TJavaScript` given a `Fuse.Scripting.Context`. Signature:

   `public delegate TJavaScript Converter(Context c, TUno value);`

The first version wraps an existing `Future<TUno>`, and optionally converts it to a corresponding JavaScript type.

The second version wraps a simple function that produces `TUno`. This function will be executed in a separate thread
automatically, and the result will be dispatched back to the JavaScript thread when done. This offers a simpler way
of implementing simple promises. To reject the promise, throw an `Exception` from the resultFactory.


Note that `Future<T>` and `Promise<T>` are Uno types currently defined in the `Experimental.Threading` package.

Complete example from `FuseJS`:

	static class Camera
	{
		public static IModule CreateModule()
		{
			return new NativeModule(
				new NativePromise<Fuse.Native.Camera.PictureResult, Scripting.Object>("takePicture", TakePicture, Converter));
		}

		static Future<Fuse.Native.Camera.PictureResult> TakePicture(object[] args)
	    {
	    	int width = 10;
	    	int height = 10;

	    	if (args.Length > 0) width = Marshal.ToInt(args[0]);
	    	if (args.Length > 1) height = Marshal.ToInt(args[1]);

	    	return Fuse.Native.Camera.TakePicture(width, height);
	    }

	    static Scripting.Object Converter(Context context, Fuse.Native.Camera.PictureResult result)
	    {
	    	var func = (Scripting.Function)context.GlobalObject["File"];
	    	var file = (Scripting.Object)func.Construct();
	    	file["path"] = result.Path;
	    	file["name"] = Path.GetFileName(result.Path);
	    	return file;
	    }
	}
