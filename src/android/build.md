# Build

## Building the Library

Finally this section 😂

> _You can write your own scripts for building the rust code using android ndk and
> get `.so` files out of it, if you're going that way keep an eye on `libunwind.a`
> issue._
>
> [libunwind issue][1]
>
> [possible solution][2]

### Setup Gradle

1. In the top level `build.gradle` (`glue/android_module/build.gradle`) add
   `id "org.mozilla.rust-android-gradle.rust-android" version "0.9.3" apply true`
2. In the `exa` module `build.gradlew` (`glue/android_module/exa/build.gradle`)
   add the following:

```gradle {hl_lines=[4,10, "35-66"],linenostart=1}
plugins {
    id 'com.android.library'
    id 'org.jetbrains.kotlin.android'
    id 'org.mozilla.rust-android-gradle.rust-android'
}

android {
    namespace 'com.example.exa'
    compileSdk 32
    ndkVersion "25.0.8775105"

    defaultConfig {
        minSdk 21
        targetSdk 32

        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
        consumerProguardFiles "consumer-rules.pro"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = '1.8'
    }
}

cargo {
    module  = "../.."
    targets = ["arm64", "arm", "x86_64", "x86"]
    libname = "exa"
    profile = "release"
    pythonCommand = "python3"
    extraCargoBuildArguments = ['-p', 'android']
}

dependencies {
    implementation 'androidx.core:core-ktx:1.7.0'
    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.ext:junit:1.1.3'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.4.0'
}

tasks.whenTaskAdded { task ->
    if ((task.name == 'javaPreCompileDebug' || task.name == 'javaPreCompileRelease')) {
        task.dependsOn 'cargoBuild'
    }
}

afterEvaluate {
    android.libraryVariants.all { variant ->
        def productFlavor = ""
        variant.productFlavors.each {
            productFlavor += "${it.name.capitalize()}"
        }
        def buildType = "${variant.buildType.name.capitalize()}"
        tasks["generate${productFlavor}${buildType}Assets"].dependsOn(tasks["cargoBuild"])
    }
}
```

I'm using version `25.0.8775105` of NDK, change that with what you have on your
machine, but make sure its >= version 23

[1]: https://github.com/rust-lang/rust/issues/103673
[2]: https://stackoverflow.com/questions/68873570/how-do-i-fix-ld-error-unable-to-find-library-lgcc-when-cross-compiling-rust
