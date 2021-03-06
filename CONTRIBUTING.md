How to do a syntex\_syntax Release
==================================

First, we need to prep the Rust repository. Check out Rust:

```
$ git clone https://github.com/rust-lang/rust
$ cd rust
```

Next, update the nightly version, and determine it's SHA1. This is simple
with [rustup](http://rustup.rs/):

```
rust$ rustup update nightly
rust$ rustup run nightly rustc --version
rustc 1.11.0-nightly (7746a334d 2016-05-28)
rust$ export RUST_SHA=7746a334d
```

Finally, checkout that version:

```
rust$ git checkout $RUST_SHA
```

---

Now that Rust is ready, get Syntex ready. First, check out Syntex:

```
rust$ cd ..
$ git clone https://github.com/serde-rs/syntex
$ cd syntex
```

Check out the `rust` branch, which tracks the upstream Rust `libsyntax`. Delete
the `syntex_syntax/src` and replace it with the rust `libsyntax`.

```
syntex$ git checkout rust
syntex$ rm -r syntex_syntax/src
syntex$ cp -r ../rust/src/libsyntax syntex_syntax/src
syntex$ git commit -a -m "Sync with rust HEAD ($RUST_SHA)"
```

Switch back to the master branch, merge it in, and resolve any conflicts:

```
syntex$ git checkout master
syntex$ git merge rust
# ... conflict resolution :-)
```

At this point, syntex will probably compile on Nightly, but not necessarily on
Stable. Check this by building the `hello_world` example:

```
syntex$ cd hello_world
syntex/hello_world$ rustup run stable cargo run
syntex/hello_world$ rustup run beta cargo run
syntex/hello_world$ rustup run nightly cargo run
```

Resolve any errors that come up. Once that's good, bump the version number, and
push up the `rust` and `master` branches for review. Once it lands and passes
the travis builds, tag it, then publish it:

```
syntex/hello_world$ cd ..
syntex$ git tag -s -m "Tagging for release" v0.14.0
syntex$ git push origin v0.14.0
syntex$ cd syntex_syntax
syntex/syntex_syntax$ cargo publish
```
