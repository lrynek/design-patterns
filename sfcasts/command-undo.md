# Undoing Action Commands

With the command pattern is quite simple to support *undoable* operations. Your
commands only need to remember some *state* about the things that need to be reverted. In
our case, we want to undo the last turn's actions if the player lost, so they can get a second
chance to play better and win the battle.

Ok! The first thing is adding a new method to the `ActionCommandInterface`. Open it up
and below `execute()` write `public function undo()`, this one also has no arguments.

The next step is to implement it in all of our commands. Let's start with `AttackCommand`,
so open it up, go to the top of the file, and you can see how PHPStorm is mad at us because
it's missing the `undo()` method. To implement it I'll click on the interface and then
press "Alt + Enter", select "Add method stubs",  and the `undo()` method is already selected,
so just press "Enter". You can see how the method was added at the bottom, I'll leave
this TODO for now because we need to figure out first what things need to be reverted,
or saying it in another way, what data the command needs to remember. Hold "cmd" and click
on the `attack()` method to go to the definition. Here, we can see that it consumes some
stamina and calculates the attack damage, so we need to remember the player's stamina
before attacking. 

Ok, close this file and go back to `GameApplicaton`. To revert the damage done we can't
just store the player's attack damage because down here we make the opponent receive the attack
so it goes through its defense. The value we're after is this `$damageDealt` variable.

Go to the top of the class and add these two properties, `private int $damageDealt`, 
and `private int $stamina`. And, inside `execute()`, before the player attacks,
write `$this->stamina = $this->player->getStamina();`, and down below
write `$this->damageDealt = $damageDealt;`. 

Perfect! We're ready to implement the `undo()` method. Here we need to restore the
opponent's health. So, write `$this->opponent->setHealth($this->opponent->getCurrentHealth() + $this->damageDealt);`
and then restore the player's stamina, write `$this->player->setStamina($this->stamina);`.
Oh! don't forget to also revert the `FightResultSet`, we don't want to report wrong data.
To revert the damage dealt, write `$this->fightResultSet->of($this->player)->removeDamageDealt($this->damageDealt);`,
and also revert the damage received by the opponent,
write `$this->fightResultSet->of($this->opponent)->removeDamageReceived($this->damageDealt);`.
Great! This class it's ready.

Let's keep going, open up `HealCommand`, I'll do the same thing to add the `undo()` method,
click on the name of the interface and press "Alt + Enter". Now, let's review
what data we need to remember. This command is a bit simpler, it just changes the player's health
and stamina, so let's store their starting values. Go to the top of the class and add both
properties, `private int $currentHealth` and `private int $stamina`. Next, inside `execute()`,
before healing the player, save its current health, write `$this->currentHealth = $this->player->getCurrentHealth();`
and the same for the stamina `$this->stamina = $this->player->getStamina();`.

Next, implement the `undo()` method. We only need to revert those two player's properties,
so write `$this->player->setHealth($this->currentHealth);` and `$this->player->setStamina($this->stamina);`.
Perfect! Another command done!

Lastly, open up `SurrenderCommand`, add the `undo()` method pressing "Alt + Enter".
Ok, we can leave this method empty because it would be funny to revert a surrender action.

Alright! It's time to ask the player if they want to revert the last action in case it was defeated.
Let me close a file files and go back to `GameApplication`. Find the AI's turn, and inside
this `if` where we check if the player died, write `$undoChoice = GameApplication::$printer->confirm();`.
The question will be "You've lost! Do you want to undo your last turn?". Then, if the answer is "no",
we need to end the battle and exit, so I'll move this two lines inside the `if`, and if the answer is "yes",
we undo the last turn actions, so we need to call `undo()` on the command objects,
but here's a small detail, these commands need to be undone in reverse order they were executed
or weird things may happen, you can see this as a *FILO* stack "first in, last out".
Anyway, let's undo the AI's attack first, so write `$aiAttackCommand->undo();` and then
the player's action, write `$playerAction->undo();`.

Perfect! Let's give this a try, spin over to your terminal and run:

```terminal
php bin/console app:game:play
```

I'll be an archer, and I'll try to lose the battle, so I'll start by healing myself, and then
attack until I hopefully lose. Yes, we lost! and it is asking if I want to undo my last turn. 
Say yes or press "Enter" and... the game continued, that's a good signal! To be certain
that this is completely working, let's compare how much health we have now to what we had one
turn ago. Ok, right now we have "9/50" and the AI has "50/60", and one turn ago,
which is round two, we had "9/50" and the AI had "50/60". The AI and I have the same health
in both rounds, so this is working as expected! Let's keep playing just to see that
the battle finishes if we say "no" this time. And... yes! the battle ended and we lost.

## Queuing Command Actions

Thanks to the command pattern we were able to revert actions very easily,
that's so nice! but you know what? That's not all what the pattern can do for us.
We can also use it to put our actions in a queue and execute them whenever we want.

Let's suppose we want to "replay" our battles, which is a very common feature in video games.
For this purpose we could store somewhere all the commands that happened in a battle, it could be
in a list, database, or any other storage mechanism. Then, we grab the list and execute
them one by one. Darn! that would be so much fun to do! But, there are more patterns
to cover.

Anyway, before we move onto the next pattern, let's discover where and how Symfony
leverages the command pattern. That's next!
