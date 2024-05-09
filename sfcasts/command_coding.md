# Adding Actions to the Game - Chapter 3

The game development department has asked us to make the game more fun and interactive. 
Instead of running the match automatically, they want the player to be able to choose 
what action to perform at the start of each turn. Right now, the only action our game 
supports is **attack**, so we'll also implement a couple more. **Heal** and **surrender**.

This is a great opportunity to put into practice the Command Pattern. We'll use it to encapsulate
the logic of actions into their own class. 
We'll start by refactoring the attack logic into a command.

The first step is to identify the code that needs to change. Open up the `GameApplication` class and
go to the `play()` method. Inside the `while` loop there's a comment telling us where the
player's turn starts. Select the code right before checking if the player won and cut it, 
and since we're already here let's write the code we know we need. We know that we want to
instantiate an `AttackCommand` object and call `execute()` on it, so let's do that. Create a 
variable called `$playerAction` and set it to `new AttackCommand()`. We'll create this class soon.
then, say `$playerAction->execute()`. 

```php
    public function play(Character $player, Character $ai, FightResultSet $fightResultSet): void
    {
        $playerAction = new AttackCommand();
        $playerAction->execute();
    }
```

Yes, I know I'm missing some arguments here, the command needs to know about 
the player object and the AI, but we'll get to that in a moment.

Ok, it's time to create the `AttackCommand` class. I'll press `Alt+Enter`, then select `Create class`, and
I'll put it on the `App\ActionCommand` namespace because `Command` it's already taken by Symfony. I'll leave
the empty constructor for now, and I'll implement the `execute()` method, then paste!

```php
    public function execute()
    {
        $damage = $player->attack();
        if ($damage === 0) {
            self::$printer->printFor($player)->exhaustedMessage();
            $fightResultSet->of($player)->addExhaustedTurn();
        }

        $damageDealt = $ai->receiveAttack($damage);
        $fightResultSet->of($player)->addDamageDealt($damageDealt);

        self::$printer->printFor($player)->attackMessage($damageDealt);
        self::$printer->writeln('');
        usleep(300000);
    }
```

Perfect! It's now obvious what arguments we're missing, the `$player`, `$ai`, and `$fightResult`. 
So now, the next question is, should we pass them to the method or to the constructor? Usually, 
the `execute()` method of the command pattern does not have any arguments because we want to
decouple the instantiation from the execution. By setting them to the constructor 
we're decoupling **when** this command is going to be run from when it's created. Also, 
it will make our life easier when implementing the undo operation. The arguments won't have
to be available at that specific moment.

Alright! Let's add these three arguments to the constructor. 

```php
class AttackCommand
{
    public function __construct(
        private readonly Character $player,
        private readonly Character $ai,
        private readonly FightResultSet $fightResultSet
    ) {
    }
}
```

Next, in the `execute()` method we need to replace the local variables with `$this`.

Perfect! Our `AttackCommand` is ready. Now, let's go back to the `GameApplication`.
Let's add the arguments to the `AttackCommand` object. `$player`, `$ai`, and `$fightResultSet`.
And, scroll down a bit, there's the AI's turn, let's use our command there too.

Great! We're ready to give it a try. Let's run the game and play a match, everything
sould work exactly as before. Exiting, right?!

## Implementing Actions as Commands

Ok, the next step is to create the other actions, heal and surrender. Let's start by
creating an interface for our commands. Call it `ActionCommandInterface` and it will have
an `execute()` method.

Next, create a PHP class called `HealCommand` and make it implement the interface. For 
the `execute()` method, I'll copy-paste some code to save us time, you'll see it in the code
blocks below the video. It just implements some randomness to determine how much health the
player will recover.

And now let's do the same for the surrender action. Create a `SurrenderCommand` class, implement
the interface, and for the implementation... I'm going to cheat, I'll set the player's health to 0,
and print a message.

Now, let's go back to the `GameApplication` and ask the player to choose an action.

```php
public function play(Character $player, Character $ai, FightResultSet $fightResultSet): void
{
    $actionChoice = GameApplication::$printer->choice('Your turn', [
        'Attack',
        'Heal',
        'Surrender',
    ]);

    $playerAction = match ($actionChoice) {
        'Attack' => new AttackCommand($player, $ai, $fightResultSet),
        'Heal' => new HealCommand($player),
        'Surrender' => new SurrenderCommand($player),
    };  
}
```

Perfect! It's time of the truth! Let's play a few rounds.

`bin/console app:game:play`

Sweet! it's asking us what to do
