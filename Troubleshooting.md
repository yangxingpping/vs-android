

# **READ FIRST** #
This project is supported by just one guy. I don't get paid for this, I'm just doing this project in my spare time.

By all means post in the 'Issues' section on this Google Code page. Please keep in mind though that many issues I receive through this page, email, or otherwise are not specific to 'vs-android' at all. Which is a shame. I'm not here to provide general Android programming support.

Two things I recommend:

  * Google Search! It's awesome. Paste your error in there, you likely will get a solution to your problem.

  * Using the Google-approved Eclipse, Cygwin and ndk-build setup. If you're inexperienced with Android programming, this is the place to start. When you're comfortable, switch to vs-android to get your IDE of choice back!


# Compiler/Linker #

# Debugging #

vs-android currently does not officially support any debugger.

It's left as an 'exercise for the reader'.

Various people have reported varying levels of success with off-the-shelf debuggers like gdb and WinGDB. So far, no-one as written up a walk-through of how to set this up.

I'd certainly appreciate anyone contributing such a walk-through. I may get around to looking at writing one myself at some point, but I've said that for many months now. So please don't wait for me. :)

Currently vs-android will deploy and run a built executable by default. This behavior can be changed in the project properties.


## Third-party debuggers ##

Each of these debuggers has support for vs-android, or otherwise includes a modified version of vs-android which works with their debugger:

  * WinGDB: http://www.wingdb.com/wgMobileEdition.htm
  * VisualGDB: http://visualgdb.com/tutorials/android/vs-android/
  * Nvidia NSight Tegra: https://developer.nvidia.com/nvidia-nsight-tegra
  * Intel INDE debugger plugin for Visual Studio: https://software.intel.com/en-us/articles/inde-getting-started-debugger-extension-for-vs-android

# Ant Build #


---


## ERROR: No suitable Java found. ##

The find\_java batchfile and exe tools in the Android SDK have been updated by Google, and can fail on certain Windows 7 machines now. Windows 8 has been reported to be okay.

A workaround for this is to edit the find\_java.bat file. It can be located here under where you installed the Android SDK:
```
/tools/lib/find_java.bat
```

Find this line:
```
rem Check we have a valid Java.exe in the path. The return code will
```
and paste this line just before it:
```
set arch_ext=32
```

This will force it to use the 32-bit Java checking tool, which will run properly from Visual Studio.


## The system cannot execute the specified program. ##

```

1>AntBuild:
1>  Envvar: JAVA_HOME is set to 'C:\Program Files\Java\jdk1.8.0_05'
1>  Envvar: JAVA_OPTS is set to ''
1>  D:\Android\apache-ant-1.9.4\bin\ant.bat  debug
1>  The system cannot execute the specified program.
```

This error can appear on systems using the x64 version of the Java JDK. It appears to be an issue with Windows "Side-by-side" assembly system; WinSxS.

The easy fix is to install the x86 version of the Java JDK instead. Be sure to set your JAVA\_HOME environment variable to the x86 version. The x86 version would end up installed into the "Program Files (x86)" directory (on a x64 PC).


### Do you **really** want the x64 JDK? ###

x64 JDK will likely work fine for a majority of users. If you're interested in fixing your setup to work, instead of using the x86 JDK take a look here:

  * http://blogs.msdn.com/b/nikolad/archive/2005/06/09/427101.aspx

Edit the @echo off line at the top of ant.bat, so you can more easily see what's going on. Java.exe is the culprit.

I'd be interested to hear if someone finds a fix to this.


## `[apply]` Could not create the Java virtual machine. ##
## `[dx]` Error occurred during initialization of VM ##

vs-android issue:

  * http://code.google.com/p/vs-android/issues/detail?id=15

The full error:
```

1>      [apply] Could not create the Java virtual machine.
1>      [apply] Error occurred during initialization of VM
1>      [apply] Could not reserve enough space for object heap
```

And other similar errors:
```

1>         [dx] Error occurred during initialization of VM
1>         [dx] Could not reserve enough space for 1048576KB object heap
```

You likely need to change the Java VM size.

As of vs-android v0.91 or later, it's possible to do this from within the Visual Studio IDE.

Go to the Project 'Properties', and look under 'Ant Build'->'General'. There's two settings:

  * Ant JVM Heap Initial Size (MB)
  * Ant JVM Heap Maximum Size (MB)

Those that have these problems have been setting both values to '512'. (no quotes)

Your mileage may vary, potentially you need some different values depending on your machine setup and the amount of Java code you're working with.


### Alternative ###

Others have had success with this, where the above fails. Set up a new environment variable:
```
 _JAVA_OPTIONS 
```
Set it to:
```
-Xms256m -Xmx512m 
```


---


## Unable to resolve target 'android-x' ##

Within the 'Android SDK Manager' application, you need to install the API which is missing.

It'll be listed under 'Android x.x ( API x )'. Enable the checkbox on the line "SDK Platform", and click the "Install Packages..." button.

See this link:

  * http://sagistech.blogspot.com/2010/05/android-sdk-error-unable-to-resolve.html

An alternative is to edit the 'project.properties' file. Change the target to match the latest API version of the installed SDK.

e.g. "android-17"


---


## UNEXPECTED TOP-LEVEL EXCEPTION: ##

vs-android issue:

  * http://code.google.com/p/vs-android/issues/detail?id=107

Solution, thanks to "JoeCHamm":

"I found the problem here. Nothing to do with vs-android. Apparently with Build Tools 19.0.0, all API levels must match which I hadn't been paying attention to since this never affected any of my Eclipse projects (not entirely sure why that is)."


---


## Signing ##

### Debug ###

Debug builds should work without any issue. If you're having problems the most likely cause if that your debug certificate has expired. For more, see here:
http://stackoverflow.com/questions/2194808/debug-certificate-expired-error-in-eclipse-android-plugins

### Release ###

For release builds you need to set up your own key. See this page:
http://developer.android.com/guide/publishing/app-signing.html#releasemode


---


## External .jar libraries ##

If your project requires extra .jar libraries, they should be placed in the /libs directory of your "Ant Build->Ant Build Root Path". This path defaults to:

```

* $(ProjectDir)\AndroidApk\
```

So the libs should be placed here:

```

* $(ProjectDir)\AndroidApk\libs
```


# Deploy #


---


## EXEC : error : device not found ##

vs-android issue:

  * http://code.google.com/p/vs-android/issues/detail?id=19

Deploy step fails with a 'EXEC : error : device not found' error:

```

1>  BUILD SUCCESSFUL
1>  Total time: 4 seconds
1>  Deploying C:\Projects\Sample\san-angeles\AndroidApk\bin\DemoActivity-debug.apk...
1>EXEC : error : device not found
1>C:\Program Files\MSBuild\Microsoft.Cpp\v4.0\Platforms\Android\Microsoft.Cpp.Android.Targets(239,5): error MSB3073: The command ""C:\Android\android-sdk\platform-tools\adb.exe" install -r C:\Projects\Sample\san-angeles\AndroidApk\bin\DemoActivity-debug.apk" exited with code 1.
```

You have to either:
  * Attach an Android device, with USB debugging enabled on it
  * Start a virtual Android device from the Android SDK's 'SDK Manager'.

Please use Google if you need help with this. There's plenty of information out there about attaching Android devices to deploy to them under Windows.