# JNI

## JNI setup

### Logger

Let us start simple by creating a logger that logs to logcat.

Create `glue/android/src/logger.rs` file, we will configure the logger there

```rust
use android_logger::Config;
use jni::objects::JClass;
use jni::JNIEnv;
use log::Level;

#[no_mangle]
#[allow(non_snake_case)]
pub extern "system" fn Java_com_example_exa_ExaLogger_initLogger(
    _env: JNIEnv,
    _: JClass
) {
    android_logger::init_once(
        Config::default()
            .with_min_level(Level::Trace)
            .with_tag("Exa"),
    );
    log_panics::init();
    info!("ExaLogger initialized");
}
```

Before explaining why we named the function using Pascal_snake_CamelCase if
that's a thing; let us add this as a public module in the `lib.rs`

```rust
// glue/android/src/lib.rs
#[macro_use]
extern crate log;

pub mod logger;
```

The first two lines are there so we can use `info!()` without importing it in
every single file when logging an info message.

### JNI Conventions

So in the previous section we create this function `Java_com_example_exa_ExaLogger_initLogger`
, in order to call a native function from JVM we should respect the JNI's
convention (at least some of them for our use case).

1) The function should start with `Java`
2) We should provide the path to the class, if the class is in the package
    `com.example.exa` then we should add `com_example_exa` in the function name
3) Add the Java class name
4) Add the function name to the signature we have

So looking at this signature, the JVM is going to look for a class called
`ExaLogger` in the `com.example.exa` package, and execute a function called `initLogger`.

Last but not least, we should always pass the enviroment and the class, even if
we're not using any of them.

Now we run into an issue of how we make it reusable, if we take this library and
put it in another project it's not going to work, as that project has a different
package names and such.

As a solution we can make an android module, when we build that module we produce
an android archive that can be used on multiple projects; `.aar` file.
