---
title: Code Extension
layout: docs
permalink: /docs/handbook/code-extension
oneline: "Visual Studio Code Extension for Lingua Franca."
preamble: >
---

The Lingua Franca extension for Visual Studio Code (VS Code) provides syntax-directed editing capability, compilation, and diagram synthesis for Lingua Franca programs.

## Usage

### Creating a new file

To create a new LF file, go to <kbd>File > New File...</kbd> and select `New Lingua Franca File`. When saving the file, save it in a directory called `src` to make sure that generated code is placed conveniently in an adjacent `src-gen` directory. For instance, for a file called `Foo.lf`, the directory structure after building should look something like this:

```
bin/Foo
src/
└ Foo.lf
src-gen/Foo
```

### Rendering diagrams

To show the diagram for the currently active Lingua Franca file, click on the diagrams icon at the upper right:

<img src="../../../../../img/vs_code/diagrams_icon.png" class="icon">

### Compilation

To compile the `.lf` source, open the command palette (<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd>) and then enter `Lingua Franca: Build`.

### Running

You can also build and immediately afterwards run your code by opening the command palette (<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd>) and then entering `Lingua Franca: Build and Run`.
Running the code can also be done from the VS Code terminal by executing the generated file in `./bin`.

## Notes

### For Python Users

Users who edit LF programs with a Python target will benefit the most from Python linting by installing Pylint 2.12.2 or later.
