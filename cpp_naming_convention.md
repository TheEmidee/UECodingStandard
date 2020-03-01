# C++ Naming Convention

[cpp.naming.project_prefix]

Choose an uppercase short prefix for the game. This will be used in several places, like class names and file names.

The project prefix in this document will be `MG` (for `MyGame`)

[cpp.naming.file]

Use **PascalCase**.

The name of the file must match the name of the main type declared in it, without the type prefix.

The type `AMGCharacter` is defined in a file named `MGCharacter.h` and implemented in a file named `MGCharacter.cpp`.

[cpp.naming.class]

Use **PascalCase**.

They must start with the type prefix, then the project short name, and end with the class name.

Ex: `AMGCharacter`, `UMGGameInstance`.

* Template classes are prefixed by T.
* Classes that inherit from UObject are prefixed by U.
* Classes that inherit from AActor are prefixed by A.
* Classes that inherit from SWidget are prefixed by S.
* Classes that are abstract interfaces are prefixed by I.
* Enums are prefixed by E.
* Most other classes are prefixed by F, though some subsystems use other letters.

[cpp.naming.class.members]

Use **PascalCase**.

[cpp.naming.functions]

Use **PascalCase**.

Underscores are allowed for UE4 auto-generated functions, like replication functions, or implementation functions.

[cpp.naming.functions.arguments]

Use **snake_case**.

Write all arguments on one line. If you have too many arguments, see `[cpp.function.args.readability]`.

[cpp.naming.local.members]

Use **snake_case**.

Declare the variable on one line:

    const auto index = 0;

Except when you declare multiple variables with the same type:

    const AMGProp
        * prop_1 = nullptr,
        * prop_2 = nullptr;

Use one assignment for each variable, don't combine them.

[cpp.template.parameters]

Use **UPPER_CASE_WITH_UNDERSCORES**.

[cpp.naming.constants]

Use **PascalCase**.

[cpp.naming.acronyms]

Use **UPPERCASE**.

Ex: JSON / XML / VR / FPS / etc...

[cpp.naming.meaning]

Don't use meaningless or truncated names.

    for ( int index = 0; index < item_count; ++i )
    {
    }

[cpp.naming.booleans]

Don't prefix booleans with a _b_ like UE4 does.