# Configuring CoR with Symfony

Let's take advantage that this is a Symfony app and use the *autoconfigure* feature
to set up the chain. There is a super useful `Autoconfigure` attribute that we can use
in our handler classes.

Open up `CasinoHandler` and on top of the class name write the attribute `#[Autoconfigure()]`.
Ok, when this class is instantiated we want to call `setNext()` and pass another
handler object. To do that we'll use the `calls` option, so inside write
`calls: [['setNext' => ['@'.LevelHandler::class]]]`, and that's it! Whenever Symfony
instantiates this class, it will call `setNext()` and pass a `LevelHandler` object.

Let's do the same thing on the `LevelHandler` class. Open it up and
write `#[Autoconfigure()]`, then `calls: [['setNext' => ['@'.OnFireHandler::class]]]`.
We can leave the `OnFireHandler` as is because it's the last handler in the chain.

Perfect! The chain it's set up, we can remove the code in `GameApplication` that initializes it
manually. Open it up and delete everything inside its constructor but leave this line.
Ok, the final step is to configure the `$xpBonusHandler` property. Add it to the constructor
by writing `private readonly XpBonusHandlerInterface $xpBonusHandler`, on top of it
use the `#[Autowire]` attribute, and inside write `service: CasinoHandler::class`.

Alright! Let's give it a try, spin over to your terminal and run:

```terminal
php bin/console app:game:play
```

Ok! The game is running that's a good signal, and if we play the battle... we lost!
Well, the good part is there's our *info* message saying that the `CasinoHandler`
kicked in, so we didn't get any extra XP.

## Bonus: Null Object Pattern

Alright! Are you ready for a bonus topic? It's time to talk about the *Null Object pattern*,
we'll use it to remove those pesky `if` checks in our handlers.

So, what's the Null Object pattern? In a nutshell, is a smart way to avoid `null` checks.
Instead of checking if a property is `null`, as we've done, you create a "null object"
that implements the same interface and does nothing in their methods. What I mean is if
a method returns a value, it will return as close to `null` as possible. For example,
if it returns an `array`, you can return an empty `array`, or an empty string, or a 0 for `int`.
It can get more complicated that this, but you get the idea.

Ok! Let's get to it. Go back to your IDE and find that `if` we've been talking about
inside any handler. What we want to do is remove this `if` and call directly the next handler.
Easy peasy! Right? Create a new handler class and name it `NullHandler`, make it
implement the `XpBonusHandlerInterface` and hold "Option" + "Enter" to implement the methods.
And now... let's do nothing! The `setNext()` method is pretty easy
because it returns nothing, so we can leave it empty, but the `handle()` method
returns an `int`. When you find a method that returns something you should always
ask yourself "how is this value being used?" answering to this question will help
you identify the closest-to-null value to return. In our case we can return 0
and we'll be fine because it represents the extra mount of XP that the player will earn,
but what if this value would be used for multiplication? Let's say this value multiplies
the XP earned for this match, returning 0 would cause a huge problem.

Alright, let keep going. Open up `CasinoHandler` and add a constructor where we'll
initialize `$this->next` to a `new NullHandler()` object. Copy the constructor because
we'll need it for the other handlers. Inside the `handle()` method, find that pesky `if`
and remove it, and also remove the `return 0` at the bottom. We can now always return the
output of the next handler. This is the beauty of the *Null Object* pattern. Ok, let's
do the same thing to the other handlers.

Perfect, let's give this a try, spin over to your terminal and run:

```terminal
php bin/console app:game:play
```

Yeah, we won! And earned extra XP thanks to the `LevelHandler`. So all this is working
nicely and the code looks great, although it could get even nicer if we do a little trick.
We could make the `$next` handler property a constructor argument and inject the `NullHandler`
to the last one in the chain using the `Autowire` attribute we've seen before.
Like this:

```php
class OnFireHandler implements XpBonusHanderInterface
{
    public function __construct(
        #[Autowire(service: NullHandler::class)]
        private readonly XpBonusHanderInterface $next
    ) {
    }
}
```

This would allow us to even remove the `setNext()` method from the interface,
but I don't want to deviate that much from the original pattern's design so let me
undo that.

Ok! coming next: meet the Chain of Responsibility cousin, the *Middleware* pattern!
