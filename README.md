# musl 1.2.2 CMake

## What is this all about?
This repository contains musl libc in version 1.2.2 converted
to the CMake build system. I did that because it was easy and
it integrates better with my hobby operating system. 

musl's website: http://www.musl-libc.org/

## Does it lack any features?
Most things *should* work fine, except that I removed code for
any other architecture than `arm`, `aarch64`, `i386` or `x86_64`,
because the other ones are quite exotic to me and I'm anyway
only interested in getting my OS to work for those four architectures.
However, it shouldn't be difficult to re-add the other ones.

Files and features I didn't include in this repo:
* `compat/` as I don't know what it does and I didn't run into problems, yet
* `crt/` because my OS has it's own crt0.c
* `ldso/` because I'm not yet interested in dynamic linking
* most `tools/`: didn't need them, yet.
* Everything that has to do with autotools, for obvious reasons

Of course it could happen that I made some mistakes in my CMakeLists.txt
so that some features don't work anymore. But if that is the case I
likely didn't even notice it so far. PR's are welcome!

## Building
Building musl with CMake is as easy as it gets.

```
$ git clone https://github.com/ulmer-a/musl
$ mkdir -p musl/build && cd musl/build
$ cmake ..
$ make -j
```

Depending on your computer's speed this will take some 30
seconds and you'll be left with a `libmusl.a` static library
in your build directory.
