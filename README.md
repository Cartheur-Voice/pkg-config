## pkg-config

pkg-config is a helper tool used when compiling applications and libraries. It helps you insert the correct compiler options on the command line so an application can use gcc -o test test.c `pkg-config --libs --cflags glib-2.0` for instance, rather than hard-coding values on where to find glib (or other libraries). It is language-agnostic, so it can be used for defining the location of documentation tools, for instance.

### Introduction

pkg-config is a script to make putting together all the build flags when compiling/linking a lot easier.

To use pkg-config, do something like the following in your `configure.ac`

    PKG_CHECK_MODULES([GNOME], [gtk > 1.2.8 gnomeui >= 1.2.0])

This puts the necesary include flags to compile/link something against libgnomeui and all its dependencies in $(GNOME_CFLAGS), and the -L/-l flags for linking in $(GNOME_LIBS).

Users can define the PKG_CONFIG environment variable to point at the right one, or if they cross-compile and have a correctly named pkg-config (eg. arm-linux-pkg-config) in their PATH that will be used in preference. Users can also define the GNOME_CFLAGS and GNOME_LIBS environment variables if they think they know better, pkg-config will not be called if they do that.

The "gtk > 1.2.8" part is only necessary if you want to specifically check if libgtk is version 1.2.8 or higher. Otherwise, the flags for gtk will be included automatically, since libgnomeui depends on gtk. So you could just say:

     PKG_CHECK_MODULES([GNOME], [gnomeui])

for any version of gnomeui. For more info, there's even a man page, try `man pkg-config`.

### Building

pkg-config depends on glib.  Note that glib build-depends on pkg-config, but you can just set the corresponding environment variables (ZLIB_LIBS, ZLIB_CFLAGS are the only needed ones when this is written) to build it.

pkg-config also either needs an earlier version of itself to find glib or you need to set GLIB_CFLAGS and GLIB_LIBS to the correct values for where it's installed in your system.

If this requirement is too cumbersome, a bundled copy of a recent glib stable release is included. Pass --with-internal-glib to configure to use this copy.

If you're cross-compiling and you need to build the bundled glib, refer to the glib documentation for cross-compiling glib. In short, this will require setting some autoconf cache variables in cases where glib would need to run a program to determine the correct value. See the glib documentation:

https://developer.gnome.org/glib/stable/glib-cross-compiling.html

If you need to use the bundled glib on Mac OS X, you'll most likely need to build for a single architecture rather than as a universal binary. This is because glib (as of version 2.32) does not support building for multiple architectures out of the box. The glib2 from MacPorts or Homebrew may be available as a universal binary and usable for pkg-config as described above. Nothing in pkg-config itself precludes being built as a universal binary.
