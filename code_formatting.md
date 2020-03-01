# Code Formatting

We use [clang-format](https://clang.llvm.org/docs/ClangFormat.html) to auto-format the code. 

A _.clang-format_ file must be created at the root of the project, alonside the _uproject_ file.

The last revision of that file can be found [there](.clang-format).

You can find informations about the various used options [there](https://clang.llvm.org/docs/ClangFormatStyleOptions.html).

The main things to know about the used formatting:
* Allman style, which means braces are on their own lines
* 100 characters limit horizontally
* 4 spaces and no tabs
* spaces everywhere (with parenthesis, around operators, etc...)
* sorting of the includes
* ...

### Visual Studio

You must configure Visual Studio so that it uses that file when it formats the code. 

You can do that within the options of VS. 

You can auto-format the document with the shortcut : *CTRL+K, CTRL+D*

## [cf.visibility]

Class visibility modifiers have the same indentation as the encompassing class:

    class FMGClass
    {
    public:

        FMGClass();

    private:

        float Duration;
    };

## [cf.class.members]

Declare each member on a separate line, repeating the type.
This allows smaller diffs if you need to add `UPROPERTY` after, and you can comment out individual members.

    struct FBar
    {
        int Index;
        float Duration;
        float Delay;
    };

## [cf.function.arguments]

Write all arguments on one line. If you have too many arguments, see `[cpp.function.args.readability]`.

## [cf.local.members]

Declare the variable on one line:

    const auto index = 0;

Except when you declare multiple variables with the same type:

    const AMGProp
        * prop_1 = nullptr,
        * prop_2 = nullptr;

Use one assignment for each variable, don't combine them.

This allows to comment out members individually very easily.

## [cf.template.parameters]

Declare all the template arguments from the list on a single line above the class / function definition

    template< typename ITEM_TYPE >
    void FillItems( const TArray< ITEM_TYPE > & items )
    { }

## [cf.statements.if]

Use braces for each statement, even for one-liners. Put the braces on a new line.

    if ( ItIsAlive )
    {
        Kill();
    }

## [cf.statements.switch]

Indent `case` statements, and surround the code of each `case` statement with braces.

    switch (condition)
    {
        case 1:
        {
            // ...
            // falls through
        }
        case 2:
        {
            // ...
            break;
        }
        case 4:
        case 5:
        {    
            // ...
            break;
        }
        default:
        {
            break;
        }
    }

See [cpp.statements.switch]

## [cf.statements.operators]

When using chained operators, put each operator statement on a new line, staring with the operator. Put the ending parenthesis on a new line, following the indentation.

    if ( longExpression
        + otherLongExpression
        + otherOtherLongExpression
        )
    {
    }

