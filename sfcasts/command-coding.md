# Adding Actions to the Game

The game development department asked for a new feature "make the game more interactive".
Ohh stakeholders they are so funny... So, instead of running battles automatically, they want the player 
to be able to choose what action to perform at the start of each turn. Right now, our game only 
supports the *attack* action, so to make it more real we'll also add a couple more actions. 
*Heal* and *Surrender* action.

This is a great opportunity to put the *command* pattern in action!

## Applying the Command Pattern

The first step is to identify the code that needs to change and encapsulate it into
its own command class. Open up the `GameApplication` class and go to the `play()` method.
Inside the `while` loop there's a comment telling us where the
player's turn starts. Select the code right before checking if the player won and cut it, 
and since we're already here let's write the code we know we need. We know that we want to
instantiate an `AttackCommand` object and call `execute()` on it, so let's do that. Create a 
variable called `$playerAction` and set it to `new AttackCommand()`. We'll create this class soon.
then, say `$playerAction->execute()`. 

```php
    public function play(Character $player, Character $ai, FightResultSet $fightResultSet): void
    {
        while (true) {
            ...
            $playerAction = new AttackCommand($player, $ai, $fightResultSet);
            $playerAction->execute();
        }
    }
```

Yes, I know I'm missing some arguments, to be able to attack we need to know about 
the player and AI objects, but we'll get to that in a moment.

I'll press `Alt+Enter`, then select `Create class`, and
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
we're decoupling *when* this command is going to be run from when it's created. Also, 
it will make our life easier when implementing the undo operation. The arguments won't have
to be available at that specific moment.

Alright! Let's add these three arguments to the constructor. 

```php
class AttackCommand
{
    public function __construct(
        private readonly Character $player,
        private readonly Character $opponent,
        private readonly FightResultSet $fightResultSet
    ) {
    }
}
```

Next, in the `execute()` method we need to replace the local variables with `$this`.

```php
class AttackCommand
{
    public function execute()
    {
        $damage = $this->player->attack();
        if ($damage === 0) {
            GameApplication::$printer->printFor($this->player)->exhaustedMessage();
            $this->fightResultSet->of($this->player)->addExhaustedTurn();
        }

        $damageDealt = $this->opponent->receiveAttack($damage);
        $this->fightResultSet->of($this->player)->addDamageDealt($damageDealt);

        GameApplication::$printer->printFor($this->player)->attackMessage($damageDealt);
        GameApplication::$printer->writeln('');
        usleep(300000);
    }
}
```

Perfect! Our `AttackCommand` is ready. Now, let's go back to the `GameApplication`.
Let's add the arguments to the `AttackCommand` object. `$player`, `$opponent`, and `$fightResultSet`.

```php
    public function play(Character $player, Character $ai, FightResultSet $fightResultSet): void
    {
        while (true) {
            $playerAction = new AttackCommand($player, $ai, $fightResultSet);
            $playerAction->execute();
        }
    }
```

And, scroll down a bit, there's the AI's turn, let's use our command there too.

```php
    public function play(Character $player, Character $ai, FightResultSet $fightResultSet): void
    {
        while (true) {
            // AI's turn
            $aiAttackCommand = new AttackCommand($ai, $player, $fightResultSet);
            $aiAttackCommand->execute();
        }
    }
```

Great! We're ready to give it a try. Let's run the game and play a match, everything
should work exactly as before. Exiting, right?!

## Implementing more Actions

* Create other actions. Heal and Surrender
* First create an interface `ActionCommandInterface`
```php
interface ActionInterface
{
    public function execute(): void;
}
```

* Make `AttackCommand` implement it
```php
class AttackCommand implements ActionCommandInterface
```

* Create HealAction class
* copy-paste files from the `tutorial` directory

* Heal Explain constructor:
  - Benefits of putting the arguments on the constructor instead of on the interface
  The constructor for this action has fewer arguments than the `AttackCommand`, it only cares about the player.
  And this is another benefit of putting the arguments in the constructor instead of the interface method
  because this help us keeping the interface simple and avoid having arguments that are not
  used by all the commands.
* Explain `execute()` method: 
  - Rolling a dice to add some randomness
  - Set the player's health to the new amount but not more than the max health

* Surrender Explanation: For this action I'll cheat a bit because there's no proper way to end the match, so I'll
set the player's health to 0

* ask player to choose an action
* add `match` to create command object based on selection
* try it

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

## Undo Feature
Explain feature 
