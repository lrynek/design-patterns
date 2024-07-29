# Triggering Chain of Responsibility

Open up `GameApplication`. We're going to set up the chain inside its
constructor, and we'll *start* by instantiating all of the handlers.
Write `$casinoHandler = new CasinoHandler()`, `$levelHandler = new LevelHandler()`,
and finally `$onFireHandler = new OnFireHandler()`.

*Here's* where we can decide the order or *sequence* of the chain. If your
application doesn't need to execute the handlers in any particular order,
*great*! You can set this up however you want! But in *our* case, we know that
the `CasinoHandler` can impact the player if we roll a `7`,
so that needs to be called *first*. The other two handlers can go in any order,
so we'll *start* with the `CasinoHandler`.

Write `$casinoHandler->setNext($levelHandler)`, followed by
the `LevelHandler` - `$levelHandler->setNext($onFireHandler)` - and we can leave
the `OnFireHandler` alone. It'll be the last one in the chain. The last thing we
need to do is to set the `CasinoHandler` in a *property* of this class, so
write `$this->xpBonusHandler = $casinoHandler`, and hold "Option" + "Enter" to
add the property. Oh! And change its type to `XpBonusHandlerInterface`. Perfect!

Okay, this chain needs to be triggered after a battle finishes, and there's a
convenient method we can use to do that. Find the `endBattle()` method, and
right before notifying the observers, trigger the chain by
writing `$xpBonus = $this->xpBonusHandler->handle()`, where the first argument
is the `$winner` and the *second* is
its `$fightResult` - `$fightResultSet->of($winner)`. Finally, we'll add the
extra XP to the `$winner` with `$winner->addXp($xpBonus)`. Cool! Let's give this
a try!

Spin over to your terminal and run:

```terminal
php bin/console app:game:play
```

I'll be a fighter and attack until I hopefully win, and... we won! But, hmm...
how do we know which handler kicked in? Well, that's a downside of this design
pattern. It's hard to debug, since *any* handler can be called. To get around
this, we can print a message in each handler so it's obvious.

## Debugging Handlers

To do this quickly, you can copy the code from below this video, paste, and...
sweet! Let's see if that worked!

Back at your terminal, run

```terminal
php bin/console app:game:play
```

Again, play until the battle finishes, and... look at this! There's our message!
The `LevelHandler` kicked in and rewarded us with extra XP. *Awesome*!

Okay, this is *cool*, but I think we can do better. We can leverage Symfony's
*dependency injection* to *initialize* the chain. As a bonus, we'll refactor the
handlers to stop checking to see if `$next` is set by applying the 
*Null Object pattern*. Let's do this!
