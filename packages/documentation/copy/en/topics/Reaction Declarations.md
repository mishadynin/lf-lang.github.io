---
title: "Reaction Declarations"
layout: docs
permalink: /docs/handbook/reaction-declarations
oneline: "Reaction declarations in Lingua Franca."
preamble: >
---

Sometimes, it is inconvenient to mix Lingua Franca code with target code. Rather than _defining_ reactions (i.e., complete with inlined target code), it is also possible to just _declare_ them and provide implementations in a separate file. The syntax of reaction declarations is the same as for reaction definitions, except they have no implementation. Reaction declarations can be thought of as function prototypes.

<div class="lf-c">

## Example

Consider the following program that has a single reaction named `hello` and is triggered at startup.
It has no implementation.

```lf-c
target C {
  cmake-include: ["hello.cmake"],
  files: ["hello.c"]
}

main reactor HelloDecl {

  reaction hello(startup)

}
```

The `cmake-include` target property is used to make the build system aware of an externally supplied implementation. The contents of `hello.cmake` is as follows:

```cmake
target_sources(${LF_MAIN_TARGET} PRIVATE hello.c)
```

The `files` target property is used to make the file that has the implementation in `hello.c` accessible,
which could look something like this:

```c
#include <stdio.h>
#include "../include/HelloDecl/HelloDecl.h"

void hello(hellodecl_self_t* self) {
    printf("Hello declaration!\n");
}
```

## File Structure

In the above example, the C file used `#include` to import a file called `HelloDecl.h`. This file
was generated from the Lingua Franca source file when the LF program was compiled. The file
`HelloDecl.h` is named after the main reactor, which is called `HelloDecl`, and its parent
directory, `include/HelloDecl`, is named after the file, `HelloDecl.lf`.

In general, compiling a Lingua Franca program that uses reaction declarations will always generate a
directory in the `include` directory for each file in the program. This directory will contain a
header file for each reactor that is defined in the file.

As another example, if an LF program consists of files `F1` and `F2`, where `F1` defines reactors
`A` and `B` and `F2` defines the reactor `C` and the main reactor `F2`, then the directory structure
will look something like this:

```
include/
├ F1/
│ ├ A.h
│ └ B.h
└ F2/
  ├ C.h
  └ F2.h
src/
├ F1.lf  // defines A and B
└ F2.lf  // defines C and F2
src-gen/
```

There is no particular location where you are required to place your C files or your CMake files.
For example, you may choose to place them in a directory called `c` that is a sibling of the `src`
directory.

## The Generated Header Files

The generated header files are necessary in order to separate your C code from your LF code because
the describe the signatures of the reaction functions that you must implement.

In addition, they define structs that will be referenced by the reaction bodies. This includes the
`self` struct of the reactor to which the header file corresponds, as well as structs for its ports,
its actions, and the ports of its child reactors.

As with preambles, programmer discipline is required to avoid breaking the deterministic semantics
of Lingua Franca. In particular, although the information exposed in these header files allows
regular C code to operate on ports and self structs, such information must not be saved in global or
static variables.

## Linking Your C Code

As with any Lingua Franca project that uses external C files, projects involving external reactions
must use the `cmake-include` target property to link those files into the main target.

This is done using the syntax

```cmake
target_sources(${LF_MAIN_TARGET} PRIVATE <files>)
```

where `<files>` is a list of the C files you need to link, with paths given relative to the project
root (the parent of the `src` directory).

</div>

<div class="lf-cpp">

## Example

Consider the following program that has a single reaction named `hello` and is triggered at startup.
It has no implementation.

```lf-cpp
target Cpp {
  cmake-include: ["hello.cmake"],
}

main reactor HelloDecl {

  reaction hello(startup)

}
```

The behavior of the `hello` reaction is provided using a method definition in an external C++ file `hello.cc`.

```cpp
#include "HelloDecl/HelloDecl.hh" // include the code generated reactor class

// define the reaction implementation
void HelloDecl::Inner::hello([[maybe_unused]] const reactor::StartupTrigger& startup) {
  std::cout << "Hello World." << std::endl;
}
```

Using the `cmake-include` target property, we can make the build system aware of this externally supplied implementation. The contents of `hello.cmake` is as follows:

```cmake
target_sources(${LF_MAIN_TARGET} PRIVATE hello.cc)
```
Note that this mechanism can be used to add arbitrary additional resources such as additional headers and implementation files or 3rd party libraries to the compilation.

## Header Files and Method Signatures

In order to provide an implementation of a reaction method, it is important to know the header file that declares the reactor class as well as the precise signature of the method implementing the reaction body.

The LF compiler generates a header file for each reactor that gets defined in LF code. The header file is named after the corresponding reactor and prefixed by the path to the LF file that defines the reactor. Consider the following example project structure:
```
src/
├ A.lf   // defines Foo
└ sub/
  └ B.lf // defines Bar
```
In this case, the compiler will generate two header files `A/Foo.hh` and `sub/B/Bar.hh`, which would need to be included by an external implementation file.

The precise method signature depends on the name of the reactor, the name of the reactions, and the precise triggers, sources, and effects that are defined in the LF code.
The return type is always void. A reaction `foo` in a reactor `Bar` will be named `Bar::Inner::foo`. Note that each reactor class in the C++ target defines an `Inner` class which contains all reactions as well as the parameters and state variables. This is done to deliberately restrict the scope of reaction bodies in order to avoid accidental violations of reactor semantics.
Any declared triggers, sources or effects are given to the reaction method via method arguments. The precise arguments and their types depend on the LF code. If in doubt, please check the signature used in the generated header file under `src-gen/<lf-file>`, where `<lf-file>` corresponds to the LF file that you are compiling.
</div>

<div class="lf-py lf-ts lf-rs">

The $target-language$ target does not currently support reaction declarations.

</div>
