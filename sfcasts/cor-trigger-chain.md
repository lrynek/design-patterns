# Triggering Chain of Responsibility

Open up `GameApplication`, I'm going to set up the chain inside its constructor
so start by instantiating all of the handlers, write `$casinoHandler = new CasinoHandler();`,
then `$levelHandler = new LevelHandler();` and lastly, `$onFireHandler = new OnFireHandler();`.
Ok, here's where we decide the order or sequence of the chain. If your application does not need
to execute the handlers in any particular, great! You can set it up however you want, but,
in our case we know that the casino handler can affect the player in case of rolling a `7`,
so it needs to be called first, and the other two handlers can go in any order, so I'll
start with the `CasinoHandler`, write `$casinoHandler->setNext($levelHandler);`, then set up
the `LevelHandler`, `$levelHandler->setNext($onFireHandler);`, and we can leave alone
the `OnFireHandler`, it will be the last one in the chain. The last thing is to set the `CasinoHandler`
in a property of this class, so write `$this->xpBonusHandler = $casinoHandler;`, and
press "Option + Enter" to add the property. Oh, change its type to `XpBonusHandlerInterface`.

Perfect! Now, the *chain* needs to be triggered after a battle finishes, and there's a
convenient method for it, find the `endBattle()` method, and right before notifying the
observers trigger the chain by writing `$xpBonus = $this->xpBonusHandler->handle();`, where
the first argument is the `$winner` and the second one is its `$fightResult`,
so write `$fightResultSet->of($winner)`. And finally, add the extra XP points
to the `$winner` by writing `$winner->addXp($xpBonus);`. Let's give this a try!

Spin over to your terminal and run:

```terminal
php bin/console app:game:play
```

I'll be a fighter and attack until I hopefully win, and... we won! But, hmm... how
do we know what handler kicked in? Well, that's a downside of this design pattern, it's
hard to debug, any handler can be called. So, we'll print a message in each handler to make
it obvious.

## Debugging Handlers

I'll do this quickly, you can copy the code from the script below this video.

Alright! Let's play again, spin over to your terminal and run:

```terminal
php bin/console app:game:play
```

Play until the battle finishes, and... look at this, there's our message! The `LevelHandler`
kicked in and rewarded us with extra XP points.

This is cool but we can do better. We'll leverage *Symfony dependency injection* to initialize
the chain, and as a bonus topic, we'll refactor the handlers to stop checking if `$next` is set
by applying the *Null Object pattern*. That's next!