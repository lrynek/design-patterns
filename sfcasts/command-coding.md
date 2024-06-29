# Adding Actions to the Game

The game development department asked for a new feature "make the game more interactive".
Those stakeholders are so funny... Ok so, instead of running battles automatically, they want the player 
to be able to choose what action to perform at the start of each turn. Right now, our game only 
supports the *attack* action, so to make it more real we'll also add a couple more actions. 
*Heal* and *Surrender* action.

This is a great opportunity to put the *command* pattern in action!

## Applying the Command Pattern

The first step is to identify the code that needs to *change* and encapsulate it into
its own command class. Open up `GameApplication` and find the `play()` method.
Inside the `while` loop there's a comment telling us where the player's turn starts.
Select the code right before checking if the player won and cut it, 
and since we're already here let's write the code we want to have here. We know that we want to
instantiate an `AttackCommand` object and call `execute()` on it, so let's do that. Create a 
variable called `$playerAction` and set it to `new AttackCommand()`, this class does not exist yet.
then, say `$playerAction->execute()`.

The next thing is to handle the arguments of the command. To be able to attack we need both
character objects and the `FightResultSet`, but where should we put them, on the constructor or
on the `execute` method? The answer may depend on your application's needs, but passing them to
the constructor is usually a better idea because it allows you to *decouple* instantiation
from execution. You can create your command objects at some point, and then later, and if the
conditions met, execute them without worrying about their arguments.

Alright! Let's create this class, I'll press "Alt + Enter", then select `Create class`.
I'll put it on the `App\ActionCommand` namespace because `Command` it's already taken by Symfony.
Press enter and voila! There is our `AttackCommand` class. Remove the annotations on top of the
constructor because they're redundant. Next, I'll split the arguments into multiple lines
and shorten the namespaces, while doing so, I'll add `private readonly` to leverage
the constructor property promotion feature, and rename the `$ai` variable to `$opponent` because
it makes more sense in this context.

Next, we need to implement the `execute` method. Say `public function execute()`,
and paste! Say yes to add the `GameApplication` import statement, and now we just need to
refactor the local variables with `$this`. Oh, and don't forget to change `ai`
to `opponent`.

Perfect! Our `AttackCommand` is ready. Now, let's go back to `GameApplication`, we'll
do the same thing for the AI's turn. Scroll down a bit until you see the comment "AI's turn",
select all that code and replace with `$aiAction = new AttackCommand()` where the player argument
is `$ai`, the opponent is `$player` and `$fightResultSet` at the end. Then, say `$aiAction->execute()`

Phew, finally! We're ready to give it a try. Spin over to your terminal and run:

```terminal
php bin/console app:game:play
```

It's running! I'll be a fighter, and... yes! We won!
This is great! Well, nothing changed, but it is using our commands under the hood.

We're ready to add more commands and *ask* the player what action to perform.
That's next!
