# INSTALL_RPATH_USE_LINK_PATH Demo #

This project demonstrates the (unexpected) behavior of the CMake target property
`INSTALL_RPATH_USE_LINK_PATH`, when the install prefix of the dependency is
inside the source directory of the dependent project.

## Usage ##

First, build and install ProjectB in `./project-a/3rdparty` with

```shell
$ cmake --preset local-install
$ cmake --build --preset local-install
$ cmake --preset external-install
$ cmake --build --preset external-install
```

This builds and installs `libB` to `./project-a/3rdparty/install` (local) and
`./install` (external, outside the source of ProjectA).

Next, build and install ProjectA in `./project-a` with

```shell
$ cmake --preset local-dependency
$ cmake --build --preset local-dependency
```

Check, that no RPATH has been added with

```shell
$ objdump -x ./install/lib/libA.1.so | grep RPATH
```

on Linux or

```shell
$ otool -l ./install/lib/libA.1.dylib | grep LC_RPATH -A2
```

on Mac OS X.

Then proceed to build and install it again (overwrite) with

```shell
$ cmake --preset external-dependency
$ cmake --build --preset external-dependency
```

and check for RPATH again. The following RPATH has been set: `<path to demo
root>/install/lib`.

## Problem ##

For the external install, linking `libA` into an executable will work fine due
to the RPATH being set. For the local install (meaning the dependency `libB` has
been installed into an install prefix located inside the source directory of
`libA`), the same executable would not be able to find `libB` due to the missing
RPATH.
