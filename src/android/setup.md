# Android Setup

## Library Target

Inside `glue/ios/Cargo.toml` add the library target and make the crate type a
static library

```toml [hl,6-8]
[package]
name = "android"
version = "0.1.0"
edition = "2021"

[lib]
name = "proximon"
crate_type = ["staticlib", "dylib"]
```

## Dependencies

- `exa_core`; our core library
- `jni`; java native interface
- `openssl-sys`
- `log`
- `log-panics`
- `android_logger`

Run

```shell
# From the project root directory
cargo add -p android --path ./exa_core/
cargo add -p android jni log log-panics android_logger
cargo add -p android openssl-sys --features vendored
```
