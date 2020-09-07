---
title: '[Rust 2021] Request for a better [patch] in cargo'
mathjax: true
comments: true
date: 2020-09-07 10:06:04
tags: [Rust]
summary: Rust core team wants to getting some suggestions for their plan for Rust 2021, and I have an idea about cargo.
---

As an interning database(TiKV) developer (and I also use Rust for many other projects), I want to let Rust's package manager cargo's `[patch]` looks at the full source instead of only the URL, because patching dependencies are quite often in a large project, and `[patch]`'s lacking awareness on the full source of the package do cause some trouble for us.

A good example of the problems caused by it is [TiKV#8500](https://github.com/tikv/tikv/pull/8500#issuecomment-679539438), we want to patch `crossbeam-queue` to `v0.2.3 ` to prevent a memory leak problem of it,  but although `v0.2.3` has been already on `crates.io`, the version in crossbeam's git repo is still `v0.2.2`, and make it hard to do the patch. I think it would be great for cargo to support things like:

```toml
[patch]
crossbean-queue = {version = "0.2.3"} # from crates.io by default
```

I'm not the only one who wants that kind of things, there're already some related issues on cargo's repo, eg. [#5478](https://github.com/rust-lang/cargo/issues/5478) and [#7497](https://github.com/rust-lang/cargo/issues/7497), and it seems the developers don't have time to prioritize and implement this.



PS. However, I'm interested in taking care of this feature, but I don't know how to 😭.

---

Contact me: My email is longfangsong@icloud.com, and my username is [@longfangsong](https://github.com/longfangsong) on GitHub.

