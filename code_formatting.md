# Code Formatting

There is only one tag for all code-formatting references : [cf]

We use [clang-format](https://clang.llvm.org/docs/ClangFormat.html) to auto-format the code. 

A _.clang-format_ file must be created at the root of the project, alonside the _uproject_ file.

The last revision of that file can be found [there](.clang-format).

You can find informations about the various used options [there](https://clang.llvm.org/docs/ClangFormatStyleOptions.html).

The main things to know about the used formatting:
* Allman style, which means braces are on their own lines
* 100 characters limit horizontally
* 4 spaces and no tabs
* spaces everywhere (with parenthesis, around operators, etc...)
* include sorting
* ...

## Visual Studio

You must configure Visual Studio so that it uses that file when it formats the code. 

You can do that within the options of VS. 

You can auto-format the document with the shortcut : *CTRL+K, CTRL+D*