# target-cfg-bin-example

This is an example of how I would like Cargo to support target configurations
for binaries. It is currently not implemented.

I would like to be able to specify different main files depending on the
operating system. I ran into this problem in `servo` where it was solved using
the `include_bytes` macro. Using the `include_bytes` macro is not great because
it breaks my IDE. I am not able to easily jump to the code. I also suspect
using macros adds a small compile time overhead.

Another use case could be something like BusyBox but where certain system
binaries are only needed on specific platforms. For example, something that
queries Linux settings would only be compiled for Linux.

The `Cargo.toml` file shows how I would like the feature to work:

```toml
[package]
name = "target-cfg-bin-example"
version = "0.1.0"
authors = ["Michael Mc Donnell <michael@mcdonnell.dk>"]
edition = "2018"
autobins = false

# Use the standard `main.rs` for all operating systems except Android.
[[target.'cfg(not(android))'.bin]]
name = "target-cfg-bin-example"
path = "src/main.rs"

# Use the `main_android.rs` main file on Android
[[target.'cfg(android)'.bin]]
name = "target-cfg-bin-example"
path = "src/main_android.rs"

[dependencies]
```

It currently results in the following error message:

```
error: failed to parse manifest at `/home/michael/src/target-cfg-bin-example/Cargo.toml`

Caused by:
  no targets specified in the manifest
  either src/lib.rs, src/main.rs, a [lib] section, or [[bin]] section must be present
```