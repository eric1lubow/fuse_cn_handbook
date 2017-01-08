# Working with foreign code

While we're trying to wrap as much native cross-platform functionality as we can into Fuse, there are naturally going to be features that haven't been implemented yet.
This guide explains how you can use the Foreign Code feature in Uno to reach into your platform's native functionality yourself.

_Note:_ This is a young feature, intended to replace the old way of doing interop through language bindings. If you want to know more about our reasons for doing so, we have written a [standalone post](https://medium.com/@fusetools/a-sane-way-of-mixing-languages-in-fuse-660b351c2f96) on the subject.


## What is Foreign Code?

Foreign Code allows you to write a Uno method body in a different language. Consider the following example:

```
using Uno.Compiler.ExportTargetInterop;

class Example
{
	[Foreign(Language.Java)]
	public static extern(Android) void Log(string message)
	@{
		android.util.Log.d("ForeignCodeExample", message);
	@}

	[Foreign(Language.ObjC)]
	public static extern(iOS) void Log(string message)
	@{
		NSLog(@"%@", message);
	@}
}
```

Here we wanted to avoid reinventing the wheel and instead use Java and Objective-C's own Logging. By tagging our Uno method with the `Foreign` attribute, and picking a language, we are then able to write the chosen language between the `@{` & `@}` braces.

You are probably wondering why we have strange braces around our foreign method body. This simply tells the compiler "Don't try and read this bit as Uno".

The two languages we currently support in the `Foreign` attribute are `Objective-C` (on iOS) and `Java` (on Android) which you write as `[Foreign(Language.ObjC)]` and `[Foreign(Language.Java)]` respectively.

The `Foreign` attribute lives in the `Uno.Compiler.ExportTargetInterop` namespace, so make sure you import that with `using` at the top of your Uno file.

The eagle-eyed among you will also have noticed that we have two methods with the same name and signature in the same class. We can do this since `extern(...)` strips out code that does not match the target platform in a very early stage of compilation.


## Type Conversion

One of the more mind-numbing things to deal with when using some 'foreign function interfaces' is dealing with types.

In Foreign Code arguments and return values are automatically converted between their Uno representation and their corresponding Java/Objective-C representation.


#### Primitive Types

Primitive types (`int`, `float`, `char`, etc) are converted as you would expect with the one exception that java does not have primitive unsigned bytes, so a `byte` in java is an `sbyte` in Uno.

So you can write this:

```
	[Foreign(Language.Java)]
	public static extern(Android) void Foo(double x, long y)
	@{
		android.util.Log.d("ForeignCodeExample", "Well look at this:" + x + "and" + y);
	@}
```

And call it in Uno like this:

```
    Foo(1.2, 12345678);
```

#### Strings

Strings are just too common for us not to handle automatically. So Uno strings map to:

- `java.lang.String` in Java
- `NSString*` in Objective-C

So again you can write something like this:

```
	[Foreign(Language.ObjC)]
	public static extern(iOS) void Log(string message)
	@{
		NSLog(@"%@", message);
	@}
```

And calling it in Uno like this:

```
    Log("Type magic everywhere!");
```

#### Foreign objects passed to Uno

So what happens when we want to return a Java or Objective-C object from a foreign method? The answer is you get an opaque object representing the foreign type.

For Objective-C you get a `ObjC.Object` and for Java you get a `Java.Object`.

You may wonder why not try and map more types; it's probably easy to think of a few more types that it would be nice to have converted automatically. The full answer for this can be found the [medium article](https://medium.com/@fusetools/a-sane-way-of-mixing-languages-in-fuse-660b351c2f96) mentioned earlier. To summarize:

> Mapping object models in the general case does not work, even in languages as syntactically similar as Java and Uno. The differences necessary in a translation are a mental overhead for the programmer using Foreign Code. We choose instead to provide a predicable interface and require that working with the internals of a foreign object is done in foreign code.

Here's how it looks like to get an object from Java:

```
	[Foreign(Language.Java)]
	public static extern(Android) Java.Object Test(int texName)
	@{
        return new android.graphics.SurfaceTexture(texName);
	@}
```

And calling it:

```
    var stex = Test(1);
```

#### And the other way? (Uno objects passed into Foreign Code)

Predictably we take the same approach. An Uno passed to Java or Objective-C is boxed inside an opaque object.

In Objective-C the type of that box is `id<UnoObject>` and in Java it is `com.uno.UnoObject`


Here is an example of an Uno object being passed in:

```
	[Foreign(Language.Java)]
	public static extern(Android) void Test(SomeFancyType soFancy)
	@{
        // Inside here the type of soFancy is `UnoObject`
		android.util.Log.d("ForeignCodeExample", "Here it is" + soFancy);
	@}
```

Call it is how you would expect:

```
    var v = Test(new SomeFancyType(1, 2, 3));
```

#### Ok but what about passing a `Java.Object` back to Java or a `ObjC.Object` back to Objective-C?

We just do the sane thing and un-box the foreign objects again, so no need to worry about some box-of-box-of-box of some Java type!

Just to clarify:
- An `ObjC.Object` passed back to Objective-C becomes an `id`
- A `Java.Object` passed back to Java becomes an `Object`

Of course you can still cast the objects back to their original types.



## Arrays

This feature allows you to pass Uno arrays to Foreign Code. As ever, we are performance conscious so we don't just make a copy of the data straight away. We pass a handle to the Uno array that you can use directly in your foreign code. If you need a full copy of the data just call `arr.copyArray()` (in Java) or `[arr copyArray]` (in Objective-C) and you will be given the native version of that type.The full details are below.

**Objective-C:**

Arrays are converted to an object of type `id<UnoArray>` which is a wrapper around the Uno array. It can be indexed and updated with the familiar `arr[i]` syntax and has a `count` method (called with `[arr count]`) that returns an `NSUInteger`. Note that updates to it are reflected in the original Uno array --- it's a wrapper, not a copy.  As mentioned, it's also possible to copy the `id<UnoArray>` to an `NSArray*` by calling `[xs copyArray]`.

 Since Objective-C lacks generics, indexing into the `id<UnoArray>` object to get an element returns `id` regardless of the element type of the array on the Uno side. This `id` is a _boxed_ representation of the element type according to the following table:

$(ForeignObjCTypeTable:)

| Uno                         | Objective-C           | Boxed array element |
|-----------------------------|-----------------------|---------------------|
| `int`, `bool`, `char`, etc. | `int`, `bool`, `char` | `NSNumber*`         |
| `string`                    | `NSString*`           | `NSString*`         |
| `ObjC.Object`               | `id`                  | `id`                |
| `object`                    | `id<UnoObject>`       | `id<UnoObject>`     |
| `Func<string, int>` etc.    | `^ int(NSString*)`    | `^ int(NSString*)`  |
| `SomeType[]`                | `id<UnoArray>`        | `id<UnoArray>`      |

Most types are already boxed, but note that primitive types like `int`, `bool`, and `char` are boxed as `NSNumber*` when accessed in a wrapped array. This means that to update an Uno array argument `int[] x` on the Objective-C side, we have to write e.g. `x[index] = @42;`. When copying an array, the resulting `NSArray*`'s elements are also boxed following the same rules.

It's possible to circumvent the boxing behaviour by using UXL macros. The following examples contrast the two ways to use arrays in foreign Objective-C code:

```
[Foreign(Language.ObjC)]
public static extern(iOS) void ForeignIntArray(int[] xs)
@{
	@{int[]:Of(xs).Set(3, 123)};
	for (int i = 0; i < @{int[]:Of(xs).Length:Get()}; ++i)
	{
		NSLog(@"array[%d]=%d", i, @{int[]:Of(xs).Get(i)});
	}
@}
```

```
[Foreign(Language.ObjC)]
public static extern(iOS) void ForeignIntArray(int[] xs)
@{
	xs[3] = @123;
	for (int i = 0; i < [xs count]; ++i)
	{
		NSLog(@"array[%d]=%@", i, xs[i]);
	}
@}
```

**Java:**

First off we try to avoid copying massive ammounts of data by default, so we pass up a boxed uno array. You can then call `.copyArray()` on that to get the native array (at which point the data is copied)

The conversions look like this (Note: Java does not allow generics of primitives types)

| Uno Type                | Boxed Java Type        | Unboxed Java Type     |
|-------------------------|------------------------|-----------------------|
| `bool[]`                | `com.uno.BoolArray`    | `bool[]`              |
| `sbyte[]`               | `com.uno.ByteArray`    | `byte[]`              |
| `char[]`                | `com.uno.CharArray`    | `char[]`              |
| `short[]`               | `com.uno.ShortArray`   | `short[]`             |
| `int[]`                 | `com.uno.IntArray`     | `int[]`               |
| `long[]`                | `com.uno.LongArray`    | `long[]`              |
| `float[]`               | `com.uno.FloatArray`   | `float[]`             |
| `double[]`              | `com.uno.DoubleArray`  | `double[]`            |
| `string[]`              | `com.uno.StringArray`  | `String[]`            |
| `anyOtherType[]`        | `com.uno.ObjectArray`  | `com.uno.UnoObject[]` |

Java also doesn't allow operator overloading so to get the first `int` from an IntArray called `x` use `x.get(0)`. To set first value in the `IntArray` `x` to `10` use `x.set(0, 10)`

## Delegates

Foreign Code also allows you to pass delegates to your foreign methods. Whilst the end result is the same, the details vary a little between Java & Objective-C so let's tackle them separately:

**Objective-C:**

Delegates get converted to an Objective-C block of the corresponding type. As an example, an argument of the type `Action<string, int>` becomes a block of type `^ void(NSString*, int)`. The argument and return types of the block use the same type conversions as arguments to foreign functions normally do.

Here is a simple example of this in action:

```
[Foreign(Language.ObjC)]
public static extern(iOS) double DelegateArgument(Func<int, double> f)
@{
	 // f is of type `^ double(int)` here, so can be called like any function
	 return f(12);
@}
```

**Java:**

If you define a delegate like this:

```
namespace Foo
{
	public delegate void Bar(float x, float y, float z);
}
```
Then you can do this:

```
[Foreign(Language.Java)]
public static extern(Android) void ForeignDelegate(Bar x)
@{
	x.run(1.0f, 2.0f, 3.0f);
@}
```
Now 'Java < 8' doesn't have lambdas, and runnable and callables don't take arguments, so behind the scenes the compiler makes a Java class called `com.foreign.Foo.Bar` with a `public void run(float x, float y, float z) { ... }` method.

The usual type conversions for primitive, string, objects and arrays still apply to arguments of delegates.

You can also pass Uno `Action`s to Java. Again because Java delegates don't support primitives we have to generate a class for this.

The type conversions follow this pattern:

```
Action -> com.foreign.Uno.Action
Action<int> -> com.foreign.Uno.Action_int
Action<int[],int> -> com.foreign.Uno.Action_IntArray_int
```


## Out/ref parameters

Out and ref parameters are supported in foreign Objective-C functions.
The Objective-C type for such a parameter is a pointer to the Objective-C type of the parameter according the @(ForeignObjCTypeTable:rules for Objective-C/Uno type conversion).

The following two examples show how it works:

```
[Foreign(Language.ObjC)]
extern(iOS) void PrimitiveOutParam(ref int m, out int n)
@{
    // m and n are of type `int*` here.
    *m = 222;
    *n = 123;
@}

[Foreign(Language.ObjC)]
extern(iOS) void StringOutParam(ref string m, out string n)
@{
    // m and n are of type `NSString**` here.
    *m = @"Out1";
    *n = @"Out2";
@}
```

As Java doesn't have out/ref parameters, it is unlikely that we will support this for Java.


#### A note on objects from the old bindings

Rather than just remove the old style bindings and force you into Foreign Code immediately, we are taking a more measured approach.

Any object created using the old bindings can be passed up to Java in the same way as `Java.Object`.
Objective-C bindings objects can be passed as `ObjC.Object`.

## Talking back to Uno

Sometimes it is not enough to be able to return values from Foreign Code to Uno. Sometimes it makes more sense to interact with Uno from inside Foreign Code. We need to be very clear to tell the compiler what parts of our Foreign Code are actually calls into Uno. To do this we use our UXL syntax.

Using UXL we can do two main things from Foreign Code:

- Access Uno fields
- Call Uno methods

If you haven't seen UXL before, and/or are someone who likes getting all the details on something, then check out our fairly extensive [UXL Handbook](/learn/guides/uxl-handbook#uxl-macros). For this guide however we will just show lots of small examples:


### `Get` and `Set` Static Uno Fields

_Note:_ Woops, looks like we have a bug with Foreign Code accessing static fields on Android. This will be fixed in an upcoming release. Once it is out this note will be removed

Let's start nice and simple. We can get or set the value of a property using the `Get` and `Set` macros.

The anatomy of a UXL `Get` expression is as follows:

```
v¯¯¯¯¯¯¯¯¯¯v¯ The `@{` `}` syntax means its a uxl macro
@{Foo:Get()}
   ^
 field name
```

And for `Set` expressions it looks like thus

```
          v¯¯¯¯¯¯ this is a foreign value
@{Foo:Set(20)}
   ^
 field name
```

Let's see this in some example code:


```
using Uno.Compiler.ExportTargetInterop;

public class Example
{
	public static int SomeValue = 7;

	[Foreign(Language.Java)]
	public extern(Android) void Doubler()
	@{
		int originalVal = @{SomeValue:Get()};
		@{SomeValue:Set(originalVal * 2)};
	@}

	[Foreign(Language.ObjC)]
	public extern(iOS) void Doubler()
	@{
		int originalVal = @{SomeValue:Get()};
		@{SomeValue:Set(originalVal * 2)};
	@}
}
```


### `Get` and `Set` Uno Instance Fields

If you have an instance method you would expect it to have a `this` pointer. Of course `this` is a reserved name in most languages so we have to use something else.

Foreign instance methods have access to `_this`, which refers to the object they are called on, wrapped as `UnoObject`.

Since the type can't automatically be inferred from foreign data, you have to use the `:Of` macro to interact with it:

```
@{MyClass:Of(_this)}
```

The above code simple says *"Hey, the Uno type of `_this` is `MyClass`"*

So let's see how we use this is a similar example to the last one:

```
using Uno.Compiler.ExportTargetInterop;

public class Example
{
	public string MyString = "This is all native";

	[Foreign(Language.Java)]
	public extern(Android) void AddExcitement()
	@{
		String originalString = @{Example:Of(_this).MyString:Get()};
		String newString = originalString + "!!!";
		@{Example:Of(_this).MyString:Set(newString)};
	@}

	[Foreign(Language.ObjC)]
	public extern(iOS) void AddExcitement()
	@{
		NSString* originalString = @{Example:Of(_this).MyString:Get()};
		NSString* newString = [originalString stringByAppendingString: @"!!!"];
		@{Example:Of(_this).MyString:Set(newString)};
	@}
}
```

### Calling Static Uno Methods

UXL's `Call` macro lets you call Uno methods (and other foreign methods) from inside a foreign method.

Once again let's start with a little anatomy:

```
 the uno method name          The foriegn values to be passed as arguments
      v                         v  v
@{Foobernator(int,string):Call(1, "jam")}
               ^    ^
     The uno types of the arguments
        of the method 'Foobernator'

```

Let's see a simple example of this is action:

```
using Uno.Compiler.ExportTargetInterop;

public class Example
{
	public void Angrify (string str)
	{
		debug_log "ARGHHH! " + str + "ARGGHHH!";
	}

	[Foreign(Language.Java)]
	public extern(Android) void Rage()
	@{
		@{Angrify(string):Call("JAVA!")};
	@}

	[Foreign(Language.ObjC)]
	public extern(iOS) void Rage()
	@{
		@{Angrify(string):Call(@"OBJECTIVE-C!")}; // :p
	@}
}
```

### Calling Uno Instance Method

I bet at this point you can see exactly where this is going! Time to use the `Of` macro with `_this` again:

```
using Uno.Compiler.ExportTargetInterop;

class Example
{
	string deviceModel;

	static void Log(string message)
	{
		debug_log(message);
	}

	void SetDeviceModel(string model)
	{
		deviceModel = model;
	}

	[Foreign(Language.Java)]
	extern(Android) void LogDeviceModel()
	{
		String deviceModel = android.os.Build.MODEL;

		// Call instance method
		@{Example:Of(_this).SetDeviceModel(string):Call(deviceModel)};

		// Call static method
		@{Example.Log(string):Call(deviceModel)};
	}

	[Foreign(Language.ObjC)]
	extern(iOS) void LogDeviceModel()
	{
		NSString* deviceModel = [[UIDevice currentDevice] model];

		// Call instance method
		@{Example:Of(_this).SetDeviceModel(string):Call(deviceModel)};

		// Call static method
		@{Example.Log(string):Call(deviceModel)};
	}
}
```


## External source files

A lot of Fuse's philosophy revolves around the idea of 'using the right tool for the right job'. Foreign Code makes it really easy to call into java to get something done fast. However we also believe that, if you need to write a lot of java, it's best to do it in java. To this end, we now let you add `.java`, `.mm` & `.hh` files to your `.unoproj` to get them included in the build.

You add Java and Objective-C files like this:

```
{
	...

	"Includes": [
		"*",
		"Example.hh:ObjCHeader:iOS",
		"Example.mm:ObjCSource:iOS",
		"Example.java:Java:Android"
	]
}
```

This also allows your Objective-C code to use UXL macros to talk back to Uno just like in foreign code blocks. If you use macros in such a file, it has to be an Objective-C++ file (`.mm`) since the macro expansion uses features from both Objective-C and C++ to interoperate with Uno code, and the file has to include `uObjC.Foreign.h`. To include any Uno classes used in the macros, it's possible to use the UXL macro `@{Your.Uno.Class:IncludeDirective}`. If you are not using UXL macros you can use the `CSource` and `CHeader` file types instead. Processing of Java files is not yet possible, but will be coming soon.

Note that the wildcard (`*`) does not automatically set the file type for files that are not Fuse files. With wildcards files in foreign languages will be included in the project, but with the anonymous `File` type. This means that you have to explicitly add foreign files to your project and set the correct file type to ensure that they are processed and added to the project. This is super helpful as it means you can have a valid Java (or Objective-C) project inside you app hierarchy (with tests etc) and only include the files you actually need in your app.


### Java

With Java you normally have to worry about your folder structure matching your package hierarchy, but not in Fuse. We will parse the `package` statement in your java files and ensure the hierarchy is correct in the project emitted by our compiler.

_Note:_ With foreign Java, there is currently no way to `import` a package or class. For that reason you have to reference classes by their fully qualified name.

You can use the `ForeignInclude` attribute to add imports in Java. It can only be used on classes. The includes affect all Foreign methods in the uno class.

```
[ForeignInclude(Language.Java, "java.lang.Runnable", "java.lang.Boolean", "android.app.Activity")]
public class SomeUnoClass : Uno.Application
{
    ...
}
```



### Objective-C

To use external Objective-C headers, you need to include them in your class, like so:

```
[ForeignInclude(Language.ObjC, "Example.hh")]
class Example
{
	...
}
```

_Note:_ Beware of naming collisions! An Objective-C class can't have the same name as an Uno class in the global namespace.

--------------------------------------------------------------------------------

# Coming Soon!

As mentioned before, Foreign Code is a young feature and we are excited to discuss with you where and how it could grow.

But here are a could of things coming down 'the pipes' soon.


## Processed Java files

We want to be able to use UXL macros to call back to Uno from separate Java files, just like we can in foreign code blocks and in processed Objective-C files.

## Foreign support libraries

There are many platform specific things you end up doing quite frequently that should be easier in Foreign Code. For example in Java you often want to get the `Context` for the application. We are going to slowly provide support libraries around Foreign Code to make things like this much easier.

Watch this space!
