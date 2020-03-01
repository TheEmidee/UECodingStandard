# C++ Standards

Master reference tag : [cpp]

[cpp.iwyu]

Include What You Use.

https://docs.unrealengine.com/en-US/Programming/BuildTools/UnrealBuildTool/IWYU/index.html

[cpp.impl.incl]

Order should be taken care of by clang-format.

    #include "CorrespondingHeaderFile.h" // Corresponding header file

    #include <CoreMinimal.h> // Engine headers using angle brackets
    #include <Components/PrimitiveComponent.h>
    
    #include "GameHeader1.h" // Project headers using quotes
    #include "GameHeader2.h"

[cpp.header.incl]

Order should be taken care of by clang-format.

    #pragma once

    #include <CoreMinimal.h> // Engine headers using angle brackets
    #include <Components/PrimitiveComponent.h>
    
    #include "GameHeader1.h" // Project headers using quotes
    #include "GameHeader2.h"

    #include "CorrespondingHeaderFile.generated.h" // Last include is the generated UE4 file

    class ForwardDeclaration1; // Forward declarations
    struct ForwardDeclaration1;

    DECLARE_DELEGATE( DelegateName ); // Delegates

    struct FMyStruct // Types
    {

    };

[cpp.incl.unused]

To reduce compilation times, don't keep unused includes.

[cpp.header.forward_decl]

Forward declare classes and structs as much as possible instead of including their header files, to reduce compilation times.

Declare the forward declarations only under the includes, not where they are used.

You can also forward declare enums.

[cpp.header.no_impl]

Don't implement class functions in the headers. Do that in the implementation file. This allows to avoid recompiling the code which is dependent on that header when the function implementation changes.

The exception are inlined functions.

[cpp.header.inline]

Don't use the `FORCEINLINE` macro on the function declaration, but on the function implementation.

The function implementation must be written under the class in the header file, not in the class itself. This allows to easily move the function to the implementation file without updating the class declaration.

Note that this does NOT guarantee that the compiler will actually inline the function.

A general usage for inline functions are getters / setters.

    struct FMyClass
    {
        bool IsClosed() const;
    }

    FORCEINLINE bool FMyClass::IsClosed() const
    {
        return true;
    }

[cpp.header.visibility]

Specify the visibility after the `GENERATED_BODY` macro.

Order the visibility of your class members as following:

* public
* protected
* private

Don't interleave visibilities inside a class definition.

[cpp.header.order]

For each visibility, put in order:

1. Functions
2. Properties
3. Variables

Exceptions are for replicated functions, which must be written below the replicated properties, to avoid cluttering the interface.

    UPROPERTY(Transient, ReplicatedUsing = "OnRep_WantsToSprint")
	bool WantsToSprint = false;
	UFUNCTION()
	void OnRep_WantsToSprint();

Don't move functions around when you add or remove constness, or when you add the `UFUNCTION` macro. This helps code diffs to be minimal.

[cpp.header.order.cache]

* Try to order data members with cache and alignment in mind.
For ex grouping similar types will minimize the internal padding the compiler will add.
* Use the `Category` name of the property to regroup them logically in the editor details panel.
* General rule of thumb: sort in descending order by size.
* Write booleans as `uint8 Foo : 1;` to store them using only one byte.

[cpp.header.ctor.default]

Don't write an empty default constructor in the class header. `GENERATED_BODY` generates already one.

[cpp.header.ctor.explicit]

Mark constructors which accept a single argument as explicit to avoid unintended implicit type conversions.

    class AMyClass
    {
        explicit AMyClass( int argument );
    };

[cpp.header.dtor.default]

Don't write empty destructor. Add the default keyword or remove it.
	
Respect the rule of 3/5/0 http://en.cppreference.com/w/cpp/language/rule_of_three

[cpp.header.virtual]

* Don't explicitly mark up overriden methods with the `virtual` keyword.
* Do explicitely mark up overriden methods with the `override` keyword.
* Don't use the `final` specifier.
* If you need to declare a destructor, make it `virtual`. See `[cpp.header.dtor.default]`
* Group overridden functions by the class that first defined them using begin/end comments. This allows to quickly know which parent class declared the functions.

<!-- -->
	
    // Begin AActor override
	void BeginPlay() override;
	void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const override;
	// End AActor override

    virtual void Kill();

[cpp.header.gc]

Never, ever, use naked pointers to class inheriting from `UObject`. 

Use a `UPROPERTY` for objects the class owns, or a smart pointer (`TWeakObjectPtr`) for objects the class doesn't.

https://docs.unrealengine.com/latest/INT/Programming/UnrealArchitecture/SmartPointerLibrary/

[cpp.header.uproperty]

Only use the unreal property specifiers you need. Don't expose the properties to blueprints if you don't need to.

Respect the order of the modifiers:

[Visible|Edit][Anywhere|InstanceOnly|DefaultOnly] / BlueprintRead[Only|Write] / Category / meta

Some tools to help write them done:

* UE4 Intellisense https://marketplace.visualstudio.com/items?itemName=RxCompiLe.UE4Intellisense
* SpecifierTool https://marketplace.visualstudio.com/items?itemName=patience2012.SpecifierTool

[cpp.namespace]

Don't use namespaces as UnrealHeaderTool does not support them.

But you can use private namespace in the **implementation files** to wrap translation-unit local free functions. Also mark them as static to enforce internal only linkage.

[cpp.auto]

Use auto everywhere you can, except in functions you override from the engine for which you kept the implementation.

This allows to type less code for types with lengthy names (like templates), forces an initialization of the variable, and may reduce the amount of code to update if you change a variable type.

[cpp.auto.single_assignation]

An exception to the always auto rule is to avoid assigning a member twice when only one assignation could be done.

    float result;

    if ( foo )
    {
        result = 1.0f;
    }
    else if ( bar )
    {
        result = 2.0f;
    }
    else
    {
        result = 0.0f;
    }

[cpp.auto.qualifier]

ALWAYS qualify the auto variables with the correct qualifiers (const or * or & ).

auto does not deduce references or constness, so do it for every qualifiers, even for those handled by auto.

[cpp.init.if]

Use initializer in _if_ statements to restrict the scope of the variables used.

    if ( const auto is_game = FApp::IsGame() )
	{
    	if ( auto * world = GetWorld() )
        {
            // do stuff
        }
	}

[cpp.init.class_member]

Don't initialize class members in the header file. Do it in the class constructor. This allows all initialization to happen at the same place.

[cpp.init.ctor]

Initialize class members in the constructor initialization list, not using assignments in the constructor body.

Initialize the members in the order they are declared in the header.

    struct FMyClass
    {
        FMyClass();

        float Member1;
        float Member2;
    }

    FMyClass::FMyClass() :
        Member1( 0.0f ),
        Member2( 1.0f )
    {}

[cpp.init.lambda]

You can initialize members using a self calling lambda. This is useful to initialize local members with a complicated logic:

    const auto level_height = []()
        {
            float result;

            // do some complicated stuff to assign result

            return result;
        }();

[cpp.enum]

Use typed enumerations instead of old C enums or `TEnumAsByte`

    enum class EMyEnum : uint8
    {
        Value1,
        Value2
    };

You can also forward declare them instead of including the header they are declared in.

[cpp.const]

TODO

[cpp.return.early]

Use early return when possible in functions, to reduce nesting.

    void AMyCharacter::Attack()
    {
        if ( !CanAttack() )
        {
            return;
        }

        // Do stuff
    }

[cpp.return.optional]

If possible, use TOptional, instead of the pattern "return a bool and update or not a reference"

    TOptional< FVector > AMyCharacter::GetTeleportLocation()
    {
        if ( !CanTeleport() )
        {
            return TOptional< FVector >();
        }

        return GetActorLocation() + GetActorForwardVector() * 1000.0f;
    }

[cpp.return.rvo]

It's acceptable to return some complex types by copy to take advantage of Return Value Optimization ( `RVO` ) and Named Return Value Optimization (`NRVO`).

https://en.cppreference.com/w/cpp/language/copy_elision

See the implementation of `AActor::GetActorLocation`:

     FVector GetActorLocation() const
	{
		return TemplateGetActorLocation(RootComponent);
	}

[cpp.comments]

Use // to comment the code, not /*  */

Visual Studio shortcut : 
* CTRL+K, CTRL+C to comment
* CTRL+K, CTRL+U to uncomment

Don't use useless comments. The code should be self-explanatory. If not possible, use one of the keywords:

    // :NOTE: Description
    // :HACK: Description
    // :TODO: Description

[cpp.globals]

Don't use global members. They don't play well with HotReload or LiveCoding.

Exceptions:

* static variables which can be computed at compile time and are expensive to initialize (FName, FGameplayTag for example). See [cpp.constants]
* UE console commands or console variables

[cpp.containers.alloc]

You can specifiy the allocator a container will use. Use this as an advantage to allocate memory on the stack instead of on the heap:

    TInlineComponentArray<UPrimitiveComponent*> PrimComponents; 
    Actor.GetComponents(PrimComponents);

    using TCustomAlloc = TInlineAllocator<32>;
	TArray<UActorComponent *, TCustomAlloc> LocalItems;
	Actor.GetComponents<UActorComponent, TCustomAlloc>(LocalItems);

Pre-allocate enough memory for the containers to avoid if possible re-allocations when the container grows:

    PrimComponents.Reserve(64);
    PrimComponents.Init(nullptr, 64);

You can expose the allocator as a template parameter to allow the caller to decide how memory is allocated:

    template< class ALLOCATOR_TYPE >
	void GetComponents( TArray<const UActorComponent*, ALLOCATOR_TYPE> & components ) const;

[cpp.containers.reset]

Use Reset instead of Empty, to avoid deallocating memory.

[cpp.hardware.cache]

Be mindful of cache access and plan your memory access accordingly

	  1 CPU cycle
	  o
	
	  L1 cache access
	  ooo
	
	  L2 cache access
	  ooooooooo
	
	  L3 cache access
	  oooooooooooooooooooooooooooooooooooooooooo
	
	  Main memory access
	  oooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo
	
from https://twitter.com/srigi/status/917998817051541504

Some general tips
- prefer linear data structures, don't jump via pointers too much etc
- all cache access is through lines 64 bytes long, have that in mind!
- in classes put related data together, group them by algorithm/logic access
- be mindful of the invisible packing the compiler will do behind your back     don't intermingle bool's or small types willy nilly with bigger ones etc

[cpp.markup.engine]

When overriding some part of a function from the engine, surround the changes with a specific comment markup to help notice where the change takes place.

Always put those comments at the first column of the line to have it stand out in the formatting of the function.

    void UMyCharacterMovementComponent::PhysWalking()
    {
    // @ENGINE_UPDATE: - BEGIN: <description>
        // custom stuff
    // @ENGINE_UPDATE: - END
        // engine stuff
    }

If code is deleted rather than modified, discuss with lead the right approach, but commenting out the section vs actually removing it is actually better.

You can have the new code respect our naming convention, but keep the original naming convention / indentation/ formatting in the code kept from the engine.

[cpp.markup.preproc_define]

If you must use conditional preprocessor defines, don't indent the #if / #elif / #endif directives.
But keep the normal indentation in the code inside those directives.

    void AMyCharacter::DoStuff()
    {
        if ( auto * world = GetWorld() )
        {
            // Do stuff
    #if WITH_EDITOR
            // Do editor stuff
    #endif
        }
    }

[cpp.asserts]

Don't use *check* related functions, unless you have VERY good reasons. This will crash the editor if you play the game in it.

Instead use the *ensure* related functions, which will print out in the log, with the call stack, and will NOT crash the editor.

You can even use *ensure* functions in if statements:

    if ( !ensureAlwaysMsgf( must_be_true, TEXT( "Must be true" ) ) )
    {
        return;
    }

    // do stuff when is_true is true

[cpp.super]

Always call _Super::Function_ in overriden engine functions(BeginPlay / Tick / etc... )

If you must not, comment the call an explain why

[cpp.lambda]

Use lambda in functions to avoid polluting the class with helper functions.

They are also useful when you need to pass a predicate to a function, or to bind a delegate.

[cpp.lambda.dangling]

Be careful of the captured objects. They may be destroyed before the lambda gets called.

[cpp.lambda.this] 

Don't capture `this`!

Instead use the named captures to cherry pick

	auto lambda_this = [LocalCopy = this->BlueprintGroup.ShowCameraWidget]()
	{
	    // LocalCopy available irregardless of the fate of parent
	};

[cpp.lambda.=&] 

Avoid capturing everything by value or worse, by reference!

	auto lambda_avoid = [&]()
	{
	    // HERE BE DRAGONS!
	};

[cpp.lambda.auto] 

The type deduction for the captures are quite convoluted
* by reference: uses template rules; will preserve `const` and `&`
* by value: uses template rules; will preserve `const`
* init capture: uses `auto` rules; WILL MESS UP `const` and `&` (add them back manually)

<!-- comment to force the next code block to be correctly displayed -->
    
    const int Original = 0;
    const int &Reference = Original;
    auto lambda_auto =
        [Original, Duplicate = Original, &RefDuplicate = Original, NotReference = Reference]()
    {
        // Original => `const int`
        // Duplicate => `int`
        // RefDuplicate => `const int &`
        // NotReference => `int`
    };

[cpp.function.args.type]

Pass complex types by `const reference` to avoid copies of the objects and keep the object immutability.

Pass integral types by copy.

Do NOT use the move operator `&&` unless you know what you're doing. This is useful for perfect forwarding in a lambda.

https://scottmeyers.blogspot.com/2013/05/c14-lambdas-and-perfect-forwarding.html

[cpp.function.args.default]

If the function defines default arguments, keep them and comment them in the implementation file.

    void AMyCharacter::DoStuff( const bool foo, const float bar = /* 1.0f */, const int baz = /* 10 */ )
    {
    }

[cpp.function.args.unused]

If the function body does not use some arguments of the function, comment the function argument:

    float AMyCharacter::DoStuff( const bool foo, const float bar /* = 1.0f */, const int /* baz = 10 */ )
    {
        return foo
            ? bar
            : 0.0f;
    }

[cpp.function.args.readability]

If possible use `typedefs` or `enums` instead of booleans or integers to give more meaning to the function arguments:

    using TOrder = int;
	enum class ECacheFlags { Use, Disabled, Unspecified };
	enum class ELogging { Yes, No };

    void FuncNiceToReadOnCall( const TOrder order, const ECacheFlags cache_flags, const ELogging logging_type ) const;

Avoid using too many arguments in a function. Better combine all the arguments in a helper class.

    void FuncWithTooManyArgs(const FVector& Location, const FVector& Origin, const FVector& EndPoint, const FRotator& Rotation, const UPrimitiveComponent& Parent, const AActor& Owner) const;

    struct FArgs
    {
        const FVector & Location;
        const FVector & Origin;
        const FVector & EndPoint;
        const FRotator& Rotation;
        const UPrimitiveComponent& Parent;
        const AActor& Owner;
    };

    void Func( const FArgs & args ) const;

If the function is private, it's also OK to split it in multiple functions. Splitting a public function tends to make the class interface too complicated to use.

[cpp.operators]

Implement relation operators using the free binary form as it provides the most flexibility with operands order and usage.

If it needs to access private members, make it `friend` and respect inlining. See `[cpp.header.inline]`

[cpp.operators.comparison]

All comparison operators can be expressed with only `==` and `<`.

    bool operator==( const FMyClass & other ) const
    {
        return bar == other.bar;
    }

    bool operator!=( const FMyClass & other ) const
    {
        return !operator==( other );
    }

[cpp.constants]

Define constants using `constexpr` and `static`.

    static constexpr FName Name( TEXT( "ConstantName" ) );

[cpp.types]

Use portable aliases types for C++ types:

* bool for boolean values (never assume the size of bool). BOOL will not compile.
* TCHAR for a character (never assume the size of TCHAR).
* uint8 for unsigned bytes (1 byte).
* int8 for signed bytes (1 byte).
* uint16 for unsigned "shorts" (2 bytes).
* int1 for signed "shorts" (2 bytes).
* uint32 for unsigned ints (4 bytes).
* int32 for signed ints (4 bytes).
* uint64 for unsigned "quad words" (8 bytes).
* int64 for signed "quad words" (8 bytes).
* float for single precision floating point (4 bytes).
* double for double precision floating point (8 bytes).

[cpp.nullptr]

Use `nullptr` and not `NULL`.
https://en.cppreference.com/w/cpp/language/nullptr

[cpp.text]

Encompass all string literal with the `TEXT` macro to avoid string conversion when using `FString`

[cpp.cast]

* Don't use C-style casts. Use `static_cast`.
* Use the unreal `Cast` function to cast `UObject` pointers.
* Use `const_cast` only when you have to
* If you need to use `reinterpret_cast`, something is probably wrong.

[cpp.struct]

Use structures as data containers only. 

They shouldn't contain any business logic beyond simple validation or need any destructors, because it's not possible to add the `UFUNCTION` macro on functions of a structure, to expose them to the blueprint world.