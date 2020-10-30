# C++ Naming Convention

## [cpp.naming.project_prefix]

Choose an uppercase short prefix for the game. This will be used in several places, like class names and file names.

The project prefix in this document will be `MG` (for `MyGame`)

## [cpp.naming.file]

Use **PascalCase**.

The name of the file must match the name of the main type declared in it, without the type prefix.

The type `AMGCharacter` is defined in a file named `MGCharacter.h` and implemented in a file named `MGCharacter.cpp`.

## [cpp.naming.class]

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

## [cpp.naming.class.members]

Use **PascalCase**.

## [cpp.naming.functions]

Use **PascalCase**.

Underscores are allowed for UE4 auto-generated functions, like replication functions, or implementation functions.

    void AMGCharacter::Dash()
    {}

    void AMGCharacter::OnKilled_Implementation()
    {}

## [cpp.naming.functions.arguments]

Use **snake_case**.

## [cpp.naming.functions.bool]

Functions returning a `bool` should ask a question. `IsVisible()`, `AreDead()`.

## [cpp.naming.functions.ufunction]

If the function is a `UFUNCTION` with the `BlueprintImplementableEvent` or the `BlueprintNativeEvent` keywords, prefix it with the `Receive` word:

    UFUNCTION(BlueprintImplementableEvent, meta=(DisplayName = "ActorBeginOverlap"), Category="Collision")
    void ReceiveActorBeginOverlap(AActor* OtherActor);

## [cpp.naming.functions.replication]

Follow the Quick Tips section of this page : https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/RPCs/index.html

Prefix the functions used for replication by Server, Client or Multicast depending on the modifiers in the UFUNCTION macro.

## [cpp.naming.local.members]

Use **snake_case**.

    const auto index = 0;

[cpp.template.parameters]

Use **UPPER_CASE_WITH_UNDERSCORES**.

    template< typename ITEM_TYPE >
    void FillItems( const TArray< ITEM_TYPE > & items )
    { }

## [cpp.naming.constants]

Use **PascalCase**.

See `[cpp.constants]`.

    static constexpr float Duration = 10.0f;

## [cpp.naming.acronyms]

Use **UPPERCASE**.

Ex: JSON / XML / VR / FPS / etc...

## [cpp.naming.meaning]

Don't use meaningless or truncated names. Always use names which express the full intent of the variable.

    // Don't
    for ( int i = 0; i < c; ++i )
    {
    }

Exceptions to this rule are accepted for commonly used UE4 types, only if the variable has a short scope, like in a function. But not for class members.

`PlayerController` can be referenced as `pc`, `GameMode` as `gm`, `GameInstance` as `gi`

## [cpp.naming.booleans]

Don't prefix booleans with a _b_ like UE4 does.

The booleans must have the form `Subject`+`Verb`+ `Adjective` or `Verb`+`Subject`+`Adjective`.

Ex: `ItIsDead` or `are_all_players_visible`.

Don't use negations in the name because they are hard to read.

    if ( !ItIsNotVisible )
    {}

## [cpp.naming.accessors]

Getters must start with `Get` and setters with `Set` and must end with the class member name.

They also must be inlined. See `[cpp.header.inline]`

    struct FMGClass
    {
    public:

        float GetDuration() const;
        void SetDuration( const float duration );
        
    private:

        float Duration;
    };

    FORCEINLINE float FMGClass::GetDuration() const
    {
        return Duration;
    }

    FORCEINLINE void FMGClass::SetDuration( const float duration )
    {
        Duration = duration;
    }

Exception to that naming convention is for booleans. Don't repeat the subject from the boolean name:

    struct FMGClass
    {
    public:

        bool IsDead() const;
        void SetIsDead( const float is_dead );
        
    private:

        bool ItIsDead;
    };

    FORCEINLINE bool FMGClass::IsDead() const
    {
        return ItIsDead;
    }

    FORCEINLINE void FMGClass::SetIsDead( const bool is_dead )
    {
        ItIsDead = is_dead;
    }

## [cpp.naming.events]

Select the right DECLARE_DELEGATE macro: https://docs.unrealengine.com/en-US/Programming/UnrealArchitecture/Delegates/index.html

The delegate type must start with the letter `F`, then the project prefix, then the word `On` and end with `Delegate`: ex `FMGOnDamageTakenDelegate`

The delegate class member must be the delegate type name, starting with `On`: `OnDamageTakenDelegate`

The accessor to this class member must be the type name, starting with `On` and without the `Delegate` suffix: `OnDamageTaken()`

Define two functions when exposing an event to a subclass. 
1. The first function should be virtual and its name should begin with Notify: `NotifyDamageTaken`
2. The second function should be a BlueprintImplementableEvent UFUNCTION and its name should begin with Receive: `ReceiveOnDamageTaken`. See `[cpp.naming.function.ufunction]`.

The default implementation of the virtual function should be to call the `BlueprintImplementableEvent` function.

Call the virtual function starting with `Notify` before broadcasting the event.

    // Header file

    DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam( FMGOnDamageTakenDelegate, float, damage );

    UCLASS()
    class AMGClass
    {
        GENERATED_BODY()

    public:
        FMGOnDamageTakenDelegate & OnDamageTaken( float duration ) const;

    protected:
        UFUNCTION( BlueprintImplementableEvent, meta = ( DisplayName = "OnDamageTaken" ) )
        void ReceiveOnDamageTaken( float distance );

        virtual void NotifyOnDamageTaken( float damage_taken );

    private:
        UPROPERTY( BlueprintAssignable )
        FMGOnDamageTakenDelegate OnDamageTakenDelegate;
    };

    FORCEINLINE FMGOnDamageTakenDelegate & OnDamageTaken( float duration ) const
    {
        return OnDamageTakenDelegate;
    }

    // Implementation file

    void AMGClass::NotifyOnDamageTaken( float distance )
    {
        ReceiveOnDamageTaken( distance );
        OnDamageTakenDelegate.Broadcast( distance );
    }