# Module

## Android Module

We can't create an android module without creating android project, so we're
going to create an android project, create an android module, then remove the
application part and keep the library part.

Create android project in the `glue` folder, choose the `No Activity` Template.

### Create Android Project

Create android project:

1. Choose `No Activity` Template
2. Name the app something other than `exa` and the package to something other
   than `com.example.exa` because we're going to use those for the android
   library. (I'll call it `tempexa` since its going to be removed after
   making the module)
3. Place it in the `glue` folder and name it `android_module`, so the location
   should be ending with `exa-lib/glue/android_module`

### Create Android Module

To create an android module:

1. Click **File > New > New Module**
2. Choose **Android Library** template
3. Name the module `exa`
4. Name the package `com.example.exa`
5. Finish

### Remove the Application Module

1. Remove `include ':app'` from `settings.gradle`
2. Delete the `app` directory recursively `rm -rf ./glue/android_module/app`
3. Remove `id 'com.android.application` from `glue/android_module/build.gradle`,
   as its no longer needed

### Adding the JNI Method

Now we should respect the conventions that we've talked about earlier.

First create a (kotlin) class named `ExaLogger` inside `com.example.exa`, inside
of it we need to have an external function and load the library.

```kotlin
package com.example.exa

class ExaLogger {
    companion object {
        init {
            System.loadLibrary("exa")
        }
    }

    public external fun initLogger()
}
```
