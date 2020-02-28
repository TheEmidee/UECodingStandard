# C++ Naming Convention

[cpp.naming.project_prefix]

Choose an uppercase short prefix for the game. This will be used in several places, like class names and file names.

The project prefix in this document will be `MG` (for `MyGame`)

[cpp.naming.file]

Use pascal cased 

The name of the file must match the name of the main type declared in it, without the type prefix.

The type `AMGCharacter` is defined in a file named `MGCharacter.h` and implemented in a file named `MGCharacter.cpp`.

[cpp.naming.class]

Use pascal case.

They must start with the type prefix, then the project short name, and end with the class name.

Ex: `AMGCharacter`, `UMGGameInstance`.