# Implementing more Actions

Alright! We're ready to add more actions to the game and allow the player to choose
what to do.

The first thing we need to do is to create an *interface* for our commands. Create a new
PHP file inside the `ActionCommand` directory, name it `ActionCommandInterface`, and
press "Enter". This interface will have a single method called `execute()` with no arguments.

Interface, done! Now, open `AttackCommand` and make it implement 
the `ActionCommandInterface`, the `execute` method it's already implemented so we're done
here too. 

For the other actions, I already have them prepared to save us some time. If
you downloaded the course code, you should have a `tutorial` directory at the root of the project,
open it up and copy the `HealCommand` and `SurrenderCommand` files into the `ActionCommand` directory.

Let's take a look at them, open up `HealCommand` first. It has a constructor that *only* cares about
the player object, and in the `execute` method we have some randomness to calculate how much
damage the player will heal, then we set the player's health to the new amount but
not more than its max health, and at the end we print a message on the screen.

Now, open up `SurrenderCommand`. The constructor is the same as the `HealCommand`, it only
works with the player object, and in the `execute` method I cheated a little bit because
there's no proper way to end a battle, so I just set the player's health to `0`.
Cool! right?.

## Asking the Player to Choose an Action

Ok, it's time to ask the player to choose an action. Let me close a file files.
Go back to the `GameApplication` and right before we define `$playerAction`,
write `$actionChoice` and set it to `GameApplication::$printer->choice()` where 
the question is `Your Turn`, and choices are `Attack`, `Heal`, and `Surrender`. Then,
we'll replace the `AttackCommand` instantiation with a `match` expression, but copy it first
because we'll need it in a moment. Now write `match ($actionChoice)`, inside, the first
case is `Attack`, and paste. The second case is going to be `Heal`, set it to `new HealCommand($player)`,
and lastly the `Surrender` case, set it to `new SurrenderCommand($player)`.

Perfect! Let's give it a try. Spin over to your terminal and run:

```terminal
php bin/console app:game:play
```

I'll be a "mage archer" this time, and... look at that! It's asking us what to do. Let's
attack first. We did `12` points of damage, and received `10` so our current health is `40` out of `50`.
Next, I'm going to heal... we got healed by `8` points, the AI did `0` damage, I assume we blocked its attack,
so our current health is `48`. The heal action is working as expected! Lastly, let's
try to surrender, choose option `2`, and... awesome! We lost immediately! 

High five your rubber duck because we've accomplished our goal of making the game more interactive!
But wouldn't it be nice to *undo* our last action? Perhaps the AI got too lucky and we want to try again. 
So that's what we'll do next!
