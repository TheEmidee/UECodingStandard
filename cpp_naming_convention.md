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

Declare each member on a separate line, repeating the type.
This allows smaller diffs if you need to add `UPROPERTY` after, and you can comment out individual members.

    struct FBar
    {
        int Index;
        float Duration;
        float Delay;
    };

[cpp.naming.functions]

Use **PascalCase**.

Underscores are allowed for UE4 auto-generated functions, like replication functions, or implementation functions.

    void AMGCharacter::Dash()
    {}

    void AMGCharacter::OnKilled_Implementation()
    {}

[cpp.naming.functions.arguments]

Use **snake_case**.

Write all arguments on one line. If you have too many arguments, see `[cpp.function.args.readability]`.

[cpp.naming.functions.ufunction]

If the function is a `UFUNCTION` with the `BlueprintImplementableEvent` or the `BlueprintNativeEvent` keywords, prefix it with the `Receive` word:

    UFUNCTION(BlueprintImplementableEvent, meta=(DisplayName = "ActorBeginOverlap"), Category="Collision")
    void ReceiveActorBeginOverlap(AActor* OtherActor);

[cpp.naming.local.members]

Use **snake_case**.

Declare the variable on one line:

    const auto index = 0;

Except when you declare multiple variables with the same type:

    const AMGProp
        * prop_1 = nullptr,
        * prop_2 = nullptr;

Use one assignment for each variable, don't combine them.

This allows to comment out members individually very easily.

[cpp.template.parameters]

Use **UPPER_CASE_WITH_UNDERSCORES**.

    template< typename ITEM_TYPE >
    void FillItems( const TArray< ITEM_TYPE > & items )
    { }

[cpp.naming.constants]

Use **PascalCase**.

See `[cpp.constants]`.

    static constexpr float Duration = 10.0f;

[cpp.naming.acronyms]

Use **UPPERCASE**.

Ex: JSON / XML / VR / FPS / etc...

[cpp.naming.meaning]

Don't use meaningless or truncated names.

    // Don't
    for ( int i = 0; i < c; ++i )
    {
    }

[cpp.naming.booleans]

Don't prefix booleans with a _b_ like UE4 does.

The booleans must have the form `Subject`+`Verb`+ `Adjective` or `Verb`+`Subject`+`Adjective`.

Ex: `ItIsDead` or `are_all_players_visible`.

Don't use negations in the name because they are hard to read.

    if ( !ItIsNotVisible )
    {}

[cpp.naming.accessors]

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

[cpp.naming.events]

Select the right DECLARE_DELEGATE macro: https://docs.unrealengine.com/en-US/Programming/UnrealArchitecture/Delegates/index.html

The delegate type must start with the letter `F`, then the project prefix, then the word `On` and end with `Delegate`:

    DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam( FMGOnDamageTakenDelegate, float, Damage );

The delegate class member must be the delegate type name, starting with `On`:

    class AMGCharacter
    {
        UPROPERTY( BlueprintAssignable )
        FMGOnDamageTakenDelegate OnDamageTakenDelegate;
    };

The accessor to this class member must be the type name, starting with `On` and without the `Delegate`suffix:

    class AMGCharacter
    {
        FMGOnDamageTakenDelegate & OnDamageTaken();
    };

Define two functions when exposing an event to a subclass. 
1. The first function should be virtual and its name should begin with Notify. 
2. The second function should be a BlueprintImplementableEvent UFUNCTION and its name should begin with Receive. See `[cpp.naming.function.ufunction]`.

The default implementation of the virtual function should be to call the BlueprintImplementableEvent function:

	virtual void NotifyActorBeginOverlap(AActor* OtherActor);
    
	UFUNCTION(BlueprintImplementableEvent, meta=(DisplayName = "ActorBeginOverlap"), Category="Collision")
	void ReceiveActorBeginOverlap(AActor* OtherActor);

    // --

    void AActor::NotifyActorBeginOverlap(AActor* OtherActor)
    {   
	    ReceiveActorBeginOverlap(OtherActor);
    }

Call the virtual function starting with `Notify` before broadcasting the event, if both are defined:


    FORMAT CORRECTLY BELOW






    DECLARE_DYNAMIC_MULTICAST_DELEGATE_ThreeParams(FHoatActorGraphConnectivityChangedSignature, AActor*, Source, AActor*, Target, float, Distance);

    /** Event when the connectivity of an observed source vertex has changed. */
    virtual void NotifyOnConnectivityChanged(AActor* Source, AActor* Target, float Distance);

    /** Event when the connectivity of an observed source vertex has changed. */
    UFUNCTION(BlueprintImplementableEvent, Category = Graph, meta = (DisplayName = "OnConnectivityChanged"))
    void ReceiveOnConnectivityChanged(AActor* Source, AActor* Target, float Distance);

    /** Event when the connectivity of an observed source vertex has changed. */
    UPROPERTY(BlueprintAssignable)
    FHoatActorGraphConnectivityChangedSignature OnConnectivityChanged;


    void AHoatActorGraph::NotifyOnConnectivityChanged(AActor* Source, AActor* Target, float Distance)
    {
    ReceiveOnConnectivityChanged(Source, Target, Distance);
    OnConnectivityChanged.Broadcast(Source, Target, Distance);

    HOAT_LOG(hoat, Log, TEXT("%s changed the connectivity of vertex %s: Distance to target %s changed to %f."),
            *GetName(), *Source->GetName(), *Target->GetName(), Distance);
    }