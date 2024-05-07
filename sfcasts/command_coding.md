# Chapter 3
# Implementing Actions as Commands

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
So now, the next question is, should I pass them to the method or to the constructor? Usually, 
the `execute()` method of the command pattern does not have any arguments because we want to
decouple the instantiation from the execution. By setting them to the constructor 
we're decoupling **when** this command is going to run from when it's created. Also, 
it will make our life easier when we implement the undo operation. The arguments won't have
to be available at that specific moment.

Alright! Let's update our constructor. 

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

update the execute method to use $this

update GameApplication
update AI turn

give it a try. Everything works just like before

Next, implement other actions
start by creating the interface, make the AttackCommand implement it
create other two derivatives, copy-paste their code explaining quickly how it works

update GameApplication, ask player to choose an action
quickly talk about $printer object


# Chapter 4
## Undo feature

implement undo feature

## Using Command for queuing actions

## Where do we see this in the real world?

## Conclusion