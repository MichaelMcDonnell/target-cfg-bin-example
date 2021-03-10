# target-cfg-bin-example

I would like to be able to specify different main files depending on the operating system. I ran into this problem in [Servo](https://github.com/servo/servo) where there are two main files. The [main.rs](https://github.com/servo/servo/blob/master/ports/winit/main.rs) file is for Android and includes the [main2.rs](https://github.com/servo/servo/blob/master/ports/winit/main2.rs) file for the desktop using the `include` macro:

```rust
#[cfg(not(target_os = "android"))]
include!("main2.rs");

#[cfg(target_os = "android")]
pub fn main() {
    println!(
        "Cannot start /ports/servo/ on Android. \
         Use /support/android/apk/ + /ports/libsimpleservo/ instead"
    );
}
```

Using the `include` macro is not great because it breaks my IDE. I am not able to easily jump to the code. I also suspect using macros adds a small compile time overhead.

It could be solved using [cfg_if](https://github.com/alexcrichton/cfg-if) crate but I think the code will become nested and difficult to read (I will soon create a Servo branch to explore this).

Another use case could be something like BusyBox but where certain system binaries are only needed on specific platforms. For example, something that queries Linux settings would only be compiled for Linux.

The `Cargo.toml` file shows how I would like the feature to work:

```toml
[package]
name = "target-cfg-bin-example"
version = "0.2.0"
authors = ["Michael Mc Donnell <michael@mcdonnell.dk>"]
edition = "2018"
autobins = false

# Use the standard `main.rs` for all operating systems except Android.
[[bin]]
name = "target-cfg-bin-example"
path = "src/main.rs"
target = "cfg(not(android))"

# Use the `main_android.rs` main file on Android
[[bin]]
name = "target-cfg-bin-example"
path = "src/main_android.rs"
target = "cfg(android)"

[dependencies]
```

It currently results in the following error message:

```
error: failed to parse manifest at `/home/michael/src/target-cfg-bin-example/Cargo.toml`

Caused by:
  found duplicate binary name target-cfg-bin-example, but all binary targets must have a unique name
```
