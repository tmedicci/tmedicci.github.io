---
layout: post
title:  ":uk: Apache NuttX: Porting Python to NuttX"
author: Tiago
date:   2025-01-08 07:00:00 -0300
categories: articles
tags: nuttx embedded python
background: '/img/posts/2025-01-08-python_on_nuttx/nuttx_python.webp'
---

Say Hello to Python on NuttX!
=============================

Yes, you heard it right: Apache NuttX now supports the Python interpreter!

NuttX was already known as a platform that can run applications built with programming languages other than traditional C. C++, Zig, Rust are compiled programming languages supported on NuttX. Considering the interpreted languages, NuttX also supports Lua, BASIC and, now, **Python**.

According to the [IEEE Spectrum’s 11th annual rankings](https://spectrum.ieee.org/top-programming-languages-2024), Python is the most used programming language among a typical IEEE member, being used more than twice the second place in the ranking. The [2024 Stack Overflow Developer Survey](https://survey.stackoverflow.co/2024/technology/#1-programming-scripting-and-markup-languages) states that Python was used by more than 51% percent of the developers in the previous year, being the highest-ranked language not specific for web development.

## Why Python on NuttX?

As one of the most used programming languages, using Python on NuttX allows developers from areas other than the embedded programming - makers included, of course - to have a previously known environment to develop their embedded applications backed by the amazing Python ecosystem, like third-party libraries and a huge set of applications publicly available. On the other hand, NuttX provides a POSIX-compatible interface that enables the Python project to be ported to it - yes, Python uses POSIX interfaces - but also allows Python applications to use socket interfaces and read/write to characters drivers attached to the actual hardware.

In summary, Python on NuttX provides a standardized interface to manipulate the actual hardware supported by NuttX. Buses and other peripherals can be accessed directly by the Python applications!

One can argue that Python wasn't designed to run on resource-constrained devices - like the ones that are supported by the NuttX RTOS - and that there are other alternatives for developing embedded applications. This isn't true anymore. Recent changes to the Python project, especially targeting WebAssembly, made Python more "friendly" regarding memory usage and other system requirements, making it more suitable for resource-constrained devices. Why not try to explore it on NuttX?

## How Python on NuttX?

The Python interpreter and its internal library are provided by the [CPython](https://github.com/python/cpython) project. As expected, its core components are written in C and use POSIX interfaces to access system-provided resources.

Being a POSIX-compatible RTOS, it's expected that NuttX RTOS could be a target system for building Python!

### Cross-compiling Python

By checking Python's documentation, one can check that the usual steps to build Python for Unix systems are, basically, running Python's `configure` tool and, then, running the `make` command, as described at the [`Setup and building`](https://devguide.python.org/getting-started/setup-building/#compiling) of the `Python Developer’s Guide`. However, building it for NuttX is not that straightforward: Python needs to be *cross-compiled* for our target hardware that will run the NuttX RTOS.

[`WASI`](https://devguide.python.org/getting-started/setup-building/#wasi) build may give us some hint on how to do that, since it compiles Python (on a *Unix* system, for instance) to a *WebAssembly* target. The documentation states that:

> Building for WASI requires doing a cross-build where you have a *build* Python to help produce a WASI build of CPython (technically it’s a “host x host” cross-build because the *build* Python is also the target Python while the host *build* is the WASI build). This means you effectively build CPython twice: once to have a version of Python for the build system to use and another that’s the build you ultimately care about (that is, the build Python is not meant for use by you directly, only the build system).

The same is valid for building Python on NuttX: *build* Python is needed for building a cross-compiled version of Python (that will effectively run on NuttX).

#### Python's Standard Library (stdlib)

According to the documentation ([*The Python Standard Library*](https://docs.python.org/3/library/index.html)):

> Python’s standard library is very extensive, offering a wide range of facilities as indicated by the long table of contents listed below. The library contains built-in modules (written in C) that provide access to system functionality such as file I/O that would otherwise be inaccessible to Python programmers, as well as modules written in Python that provide standardized solutions for many problems that occur in everyday programming. Some of these modules are explicitly designed to encourage and enhance the portability of Python programs by abstracting away platform-specifics into platform-neutral APIs.

*WASI* build encapsulates the dependencies of the Python's Standard Library (the built-in and the Python modules) in a zip file that contains the modules compiled into `.pyc` files (see [*py_compile — Compile Python source files*](https://docs.python.org/3/library/py_compile.html)).

Based on a similar approach of [`cpython-emscripten`](https://github.com/dgym/cpython-emscripten/blob/master/3.6.13/Makefile) and [`pyodide`](https://github.com/pyodide/pyodide/blob/main/cpython/Makefile), Python for NuttX can be built using - adding some tweaks and hacks - the `WASI` build.

Python's Standard Library won't be built by NuttX directly. Instead, Python's `configure` command will still be used to build the library using the same toolchain and flags used by NuttX's build system. This process, however, is integrated into NuttX's build system. Let's check how it was done.

#### The Build Process

Python for NuttX is available as an app (on [*NuttX Apps*](https://github.com/apache/nuttx-apps/) repository). The main reference for it can be found at [`interpreters/python/Makefile`](https://github.com/apache/nuttx-apps/blob/d3102f0dcef047a760e22202d042d17fc3082845/interpreters/python/Makefile).

The build process of NuttX starts with the `context` and `depend` recipes:

```
context:: $(CPYTHON_UNPACKNAME)

depend:: romfs_cpython_modules.h
```

Referring to the [`Unix.mk`](https://github.com/apache/nuttx/blob/3c4053a3850091a039abb3af29be33a2a8c8bb5e/tools/Unix.mk#L438) file of the NuttX repository, one can check that `context` phase is executed before the `depend` phase. In the `context` phase for the Python build, the Python package (the source code downloaded from the `CPython` project) is retrieved. Along with this phase, a series of patches are applied to the downloaded source code:

```
$(CPYTHON_UNPACKNAME): $(CPYTHON_ZIP)
	@echo "Unpacking: $(CPYTHON_ZIP) -> $(CPYTHON_UNPACKNAME)"
	$(Q) $(UNPACK) $(CPYTHON_ZIP)
	$(Q) mv	cpython-$(CPYTHON_VERSION) $(CPYTHON_UNPACKNAME)
	@echo "Patching $(CPYTHON_UNPACKNAME)"
	$(Q) patch -p1 -d $(CPYTHON_UNPACKNAME) < patch$(DELIM)0001-workaround-newlib-resource.h-limitations.patch
	$(Q) patch -p1 -d $(CPYTHON_UNPACKNAME) < patch$(DELIM)0002-fix-various-uint32_t-unsigned-int-type-mismatch-issu.patch
	$(Q) patch -p1 -d $(CPYTHON_UNPACKNAME) < patch$(DELIM)0003-reuse-wasm_assets.py-for-generating-an-archive-of-py.patch
	$(Q) patch -p1 -d $(CPYTHON_UNPACKNAME) < patch$(DELIM)0004-recognize-nuttx-as-a-supported-OS.patch
	$(Q) patch -p1 -d $(CPYTHON_UNPACKNAME) < patch$(DELIM)0005-gh-122907-Fix-Builds-Without-HAVE_DYNAMIC_LOADING-Se.patch
	$(Q) patch -p1 -d $(CPYTHON_UNPACKNAME) < patch$(DELIM)0006-change-var-name-to-avoid-conflict-with-nuttx-unused_.patch
	$(Q) patch -p1 -d $(CPYTHON_UNPACKNAME) < patch$(DELIM)0007-undef-atexit_register.patch
	$(Q) patch -p1 -d $(CPYTHON_UNPACKNAME) < patch$(DELIM)0008-declare-struct-timeval.patch
	$(Q) patch -p1 -d $(CPYTHON_UNPACKNAME) < patch$(DELIM)0009-include-nuttx-sys-select-header-to-define-FD_SETSIZE.patch
	$(Q) patch -p1 -d $(CPYTHON_UNPACKNAME) < patch$(DELIM)0010-check-for-the-d_ino-member-of-the-structure-dirent.patch
	$(Q) patch -p1 -d $(CPYTHON_UNPACKNAME) < patch$(DELIM)0011-avoid-redefinition-warning-if-UNUSED-is-already-defi.patch
```

These patches tweak the WASI build to include a new target system which will be used by NuttX.

The `depend` recipe triggers the actual building of Python's Standard Library, checking for the `romfs_cpython_modules.h` file at the end of the build process.

Finally, the Python interpreter is the only application that is built by NuttX directly: it's registered by the following line:

```
MAINSRC += python.c
```

Note that this application, available at [`Programs/python.c`](https://github.com/python/cpython/blob/v3.13.0/Programs/python.c) on CPython's source code, calls functions of the Python's Standard Library - which will be available as an external dependency of the NuttX's Python application, as can be checked at [`apps/interpreters/python/Make.defs`](https://github.com/apache/nuttx-apps/blob/d3102f0dcef047a760e22202d042d17fc3082845/interpreters/python/Make.defs#L31):

```
EXTRA_LIBS += -lpython$(CPYTHON_VERSION_MINOR)
```

##### In-Depth Details

Well, let's dive deeper into Python's build process. The dependency tree in the NuttX Apps `interpreters/python/Makefile` is as follows (auxiliary recipes will be ignored):

The `depend` (NuttX's recipe) depends on (will be represented by `→`) `romfs_cpython_modules.h`, and, then:

`romfs_cpython_modules.h` → `romfs_cpython_modules.img` → `$(TARGETLIBPYTHON)` → `$(TARGETBUILD)/Makefile` → `$(HOSTPYTHON)`.

The `$(HOSTPYTHON)` recipe builds CPython for the build system, i.e., the Python that will be used to build the target (NuttX) build. Please note that it runs Python's `configure` script and, then, runs `make install` to build and install CPython in `$(HOSTBUILD)` folder:

```
$(HOSTPYTHON):
	mkdir -p $(HOSTBUILD)
	mkdir -p $(HOSTINSTALL)
	$(Q) ( \
			cd $(HOSTBUILD) && $(CPYTHON_PATH)/configure \
			 --with-pydebug \
			 --prefix=$(HOSTINSTALL) \
		 )
	$(MAKE) -C $(HOSTBUILD) install
```

The next step is configuring the target build. This is done by the `$(TARGETBUILD)/Makefile` recipe. This recipe generates CPython's Makefile by calling the `configure` script:

```
$(TARGETBUILD)/Makefile: $(HOSTPYTHON)
 $(Q) mkdir -p $(TARGETBUILD)/Modules
 $(Q) mkdir -p $(TARGETMODULES)/python$(CPYTHON_VERSION_MINOR)
 $(Q) ( cp Setup.local $(TARGETBUILD)/Modules/Setup.local )
 $(Q) ( \
 cd $(TARGETBUILD); \
 CFLAGS="$(CFLAGS)"; \
 ARCH=$(CONFIG_ARCH); \
 ARCH_CHIP=$(CONFIG_ARCH_CHIP); \
 ARCH="$${ARCH//-/}"; \
 ARCH_CHIP="$${ARCH_CHIP//-/}"; \
 CFLAGS="$$(echo "$${CFLAGS}" | sed 's/-Os //')" \
 CC="$(CC)" \
 CXX="$(CXX)" \
 AR="$(AR)" \
 ARFLAGS=" " \
 MACHDEP="$(MACHDEP)" \
 OPT="-g -O0 -Wall" \
 CONFIG_SITE="$(CONFIG_SITE)" \
 $(CPYTHON_PATH)/configure \
 --prefix=${TARGETINSTALL} \
 --disable-shared \
 --host=$${ARCH}-$${ARCH_CHIP}-nuttx \
 --build=$(shell $(CPYTHON_PATH)/config.guess) \
 --with-build-python=${HOSTPYTHON} \
 --without-mimalloc \
 --without-pymalloc \
 --disable-test-modules \
 )
```

Note that the file [`Setup.local`](https://github.com/apache/nuttx-apps/blob/a2e527559af7f75b525c203158038cc65870cc02/interpreters/python/Setup.local) is copied to the CPython source code. This file configures the modules that would be built with Python's Standard Library. In our case, some modules were disabled by adding them after the `*disabled*` keyword in that file.

The flags `CFLAGS`, `ARCH`, `ARCH_CHIP`, `CC`, `CXX`, and `AR` are inherited from the NuttX build system and correspond to the NuttX's target architecture. `MACHDEP` is defined as `nuttx`: this "target" was added as a valid target for building Python when the patches in the `$(CPYTHON_UNPACKNAME)` recipe were applied. The `CONFIG_SITE` refers to the [`config.site`](https://github.com/apache/nuttx-apps/blob/a2e527559af7f75b525c203158038cc65870cc02/interpreters/python/config.site) file. This file is used by the `configure` script while checking for the system's requirements. The `configure` script checks a set of functions and header files definitions to figure out the resources and requirements the system offers to generate the CPython's `Makefile`. This is done, for instance, by trying to build a small test application to check if a function is available for use, but this test may not be valid as CPython is being built for another target (NuttX), so it's necessary to explicitly tell the script whether some function or resource is (or not) available within NuttX. This is the purpose of the `config.site` file. This file also sets `MODULE_BUILDTYPE="static"` to tell the CPython to build the modules as static libraries.

Finally, we run the `configure` script with the following arguments (its output will be the CPython's Makefile to build the target (NuttX) *build*, i.e., the `$(TARGETBUILD)/Makefile`):
- `--disable-shared` to disable the usage of shared libraries on target (NuttX);
- `--host=$${ARCH}-$${ARCH_CHIP}-nuttx` to set the host for the NuttX *build*: the support for it was added as a patch on CPython's source code;
- `--build=$(shell $(CPYTHON_PATH)/config.guess)`: the host system being used to build NuttX (and CPython);
- `--with-build-python=${HOSTPYTHON}`: location of the *build* Python used to build the target (NuttX) *build*;
- `--without-mimalloc` and `--without-pymalloc`: disable specific memory allocators. Please note that the memory will be managed by Python's Raw Memory Management, which will be allocated from the system's heap (with `malloc`);

The `$(TARGETLIBPYTHON)` recipe will build Python's Standard Library and the Python modules:

```
$(TARGETLIBPYTHON): $(TARGETBUILD)/Makefile
	$(MAKE) -C $(TARGETBUILD) regen-frozen
	$(MAKE) -C $(TARGETBUILD) libpython$(CPYTHON_VERSION_MINOR).a wasm_stdlib
	$(Q) ( cp $(TARGETBUILD)/libpython$(CPYTHON_VERSION_MINOR).a $(TARGETLIBPYTHON) )
	$(Q) $(UNPACK) $(TARGETMODULESPACK) -d $(TARGETMODULES)/python$(CPYTHON_VERSION_MINOR)
```

- `$(MAKE) -C $(TARGETBUILD) regen-frozen` will regenerate the frozen modules if there are changes on their source code;
- `$(MAKE) -C $(TARGETBUILD) libpython$(CPYTHON_VERSION_MINOR).a wasm_stdlib` will build the Python's Standard Library (`libpython3.13.a`) and the Python's modules (`wasm_stdlib`);
- `$(Q) ( cp $(TARGETBUILD)/libpython$(CPYTHON_VERSION_MINOR).a $(TARGETLIBPYTHON) )` and `$(Q) $(UNPACK) $(TARGETMODULESPACK) -d $(TARGETMODULES)/python$(CPYTHON_VERSION_MINOR)` copies the library into its final location and unpacks the compressed Python's modules;

The `romfs_cpython_modules.img` recipe creates a ROMFS image containing the Python's modules. This ROMFS image is embedded into the final firmware and Python's modules will be loaded by the Python interpreter on runtime;

Finally, the `romfs_cpython_modules.h` is the header file that will be used to embed the Python's modules into the final NuttX firmware.

### The Python's Modules

The target *build* (NuttX) - based on *WebAssembly* - generates the Python modules in pre-compiled byte code (`*.pyc`). These modules are loaded by the Python interpreter and must be available to the system on runtime as regular files. This is done by embedding them into the firmware as a ROMFS image. Before running the Python interpreter, this ROMFS image is mounted by the [`apps/interpreters/python/mount_modules.c`](https://github.com/apache/nuttx-apps/blob/d3102f0dcef047a760e22202d042d17fc3082845/interpreters/python/mount_modules.c) application.

### Wrapping Up...

The other files at [`apps/interpreters/python`](https://github.com/apache/nuttx-apps/tree/d3102f0dcef047a760e22202d042d17fc3082845/interpreters/python), like [`apps/interpreters/python/Kconfig`](https://github.com/apache/nuttx-apps/blob/d3102f0dcef047a760e22202d042d17fc3082845/interpreters/python/Kconfig) and [`apps/interpreters/python/Make.defs`](https://github.com/apache/nuttx-apps/blob/d3102f0dcef047a760e22202d042d17fc3082845/interpreters/python/Make.defs), respectively, define the NuttX's configs to enable/disable the Python interpreter and defines the libraries used to compile the Python interpreter.

Finally, build Python on NuttX:

<script src="https://asciinema.org/a/szuacnhLiCP6X9x1Ywd5J1Ba9.js" id="asciicast-szuacnhLiCP6X9x1Ywd5J1Ba9" async="true"></script>

*Yes, it took 2 minutes to compile Python on NuttX. Impressive considering that the CPython project was built twice!*

## Running the Python Interpreter on NuttX

Following is a quick demonstration of how to run Python on NuttX using the RISC-V QEMU:

<script src="https://asciinema.org/a/bYYy1fyIOQ3hOY4lJ7L3WFcNb.js" id="asciicast-bYYy1fyIOQ3hOY4lJ7L3WFcNb" async="true"></script>

As can be seen in the previous record, there are some steps to run Python on NuttX:
1. Run `python_mount_modules` to mount the ROMFS image at `/usr/local/lib` on NuttX with the Python modules;
2. Set the `PYTHONHOME` environment variable to `/usr/local`. This will be used by the Python interpreter to locate the modules' location;
3. Set the `PYTHON_BASIC_REPL` environment variable to `1`. This will tell the Python interpreter to use the basic REPL.

After these steps, the Python interpreter can be run by simply typing `python`, as usual...

Finally, **Say hello to Python on NuttX**!

## Future Work

Porting Python to NuttX is not as simple as the work I've done so far... it's a long journey to everything work as expected and to understand the system's limitations (and the necessary adjustments to it). Currently, Python on NuttX was tested only with the RISC-V QEMU, but it's necessary to expand to more platforms and real hardware.

To map the next challenges and concentrate the discussion about the roadmap, I've created an issue to track all the missing work and discussions about Python: *[[FEATURE] Python on NuttX: known issues, "TO-DO" list, and general enhancements ](https://github.com/apache/nuttx-apps/issues/2884)*.

I hope this article may be useful for other users to start contributing to Python on NuttX and make embedded programming easier!
