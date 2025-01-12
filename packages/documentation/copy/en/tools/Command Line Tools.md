---
title: Command Line Tools
layout: docs
permalink: /docs/handbook/command-line-tools
oneline: "Command-line tools for Lingua Franca."
preamble: >
---


## Usage

Set up a Lingua Franca project by putting your program in a file with the `.lf` extension,
such as `Example.lf` and putting that file with a directory called `src`.
Then compile the program:

```sh
$ lfc src/Example.lf
```

This will create two directories in parallel with the `src` directory, `src-gen` and `bin`. If your target language is a compiled one (like C and C++), then the `bin` directory should contain an executable that you can run:

```sh
$ bin/Example
```

To see the options that can be given to `lfc`, get help:

```sh
$ lfc --help
```
