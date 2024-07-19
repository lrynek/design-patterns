# Chain of Responsibility

Time for design pattern number two - the Chain of Responsibility pattern. Sometimes
there's no an official definition for patterns, so this is how I like to define it:

## Definition

> Chain of responsibility it's just a way to set up a sequence of methods to be executed,
where each method can decide to execute the next one in the chain or stop the sequence.

This pattern solves the problem of needing to run a sequence of checks in order 
to determine what to do. Let's suppose we want to check if a comment is spam or not,
and we have 5 different algorithms to check for that. If any of them returns `true`, it means
the comment is spam and we should stop the process because running the algorithms is
expensive. For this purpose, we'd need to encapsulate each algorithm into a "handler" class,
set up the chain, and run it.

## Pattern Anatomy

You might be wondering what's a "handler" class, and what do I mean by "chain". 
Those are great questions. Let's review the anatomy of the pattern.

It's composed of three parts:

First, a `HandlerInterface`, usually with two methods: `setNext()` and `handle()`.

The `setNext()` method receives another `HandlerInterface` object. This allows you
to set up a sequence of handlers, choosing what handlers you want in the sequence
and in what order. This sequence is called *chain*. And, the `handle()` method
is just where your business logic goes.

Ok, the second part is the *concrete handlers*, they implement the `HandlerInterface`.
They hold a `HandlerInterface` object, and decide if the next handler should be called or not.

And third, a *client* that sets up the chain, ensuring that the sequence it's in the right order,
and triggers the first handler.

If you got more questions than when we started, don't worry! It's time to see this in action.

## The Real-Life Challenge

For our next challenge we want to boost the player's level, so we'll reward them with
extra XP points after a battle. But, we want to reward them in different ways, and only one
should apply. Here are the conditions to reward the player:

1) If the player is level 1.
2) If the player has won 3 times or more in a row.
3) And, to add some randomness, the player will throw 2 6-sided dice. They win
if roll a pair, but if the result is 7, we exit immediately.

Alright, let's do this! The first thing we need to do is to create an interface
for our handlers, so inside the `src/` directory, create a new folder called `ChainHandler`
and inside it, add a new PHP class called, what about? `XpBonusHandlerInterface`. 
As a recommendation try including the name of the pattern as part of the interface name
so is pretty obvious what pattern we are using.

Ok, add the first method `public function handle()`, and as arguments, we'll
have `Character $player`, and `FightResult $fightResult`. We're using `FightResult`
here because we're only interested in the data of this `$player` object. Don't forget
to add the return type `int`.

For the next method write `public function setNext(XpBonusHandlerInterface $next): void`.
Oh, I almost forgot to add the `Character` import statement, press "Option + Enter" and
select "Import class".

Ok! The interface is ready, time to add some *handlers*. Inside the `ChainHandler/` directory
add a PHP class. The first condition to reward players is if they're level one, so I'll
call this `LevelHandler`.

Start by implementing the `XpBonusHandlerInterface`, and as you've already seen this before
I'll press "Option + Enter" to add both methods. I'll deal with `setNext()` first so inside
write `$this->next = $next;`. Then, to add the property press "Option + Enter"
on `$this->next` and select "Add property". Perfect! Now the `handle()` method. We'll reward
the player if is level 1, so write `if ($player->getLevel() === 1)` and inside `return 25;`.
If it's not, we'll call the next handler but only if it's set, so write
`if (isset($this->next))`, and inside call it `return $this->next->handle($player, $fightResult)`.
Then, at the bottom we just `return 0;`, that means we got to the end of the *chain*
and none of the handlers applied.

Let's keep going. Add another PHP class to deal with our second winning condition, which 
is when the player has won 3 or more times in a row, so I'll call it `OnFireHandler`.
Implement the interface and do the same trick "Option + Enter" to add the methods. The 
`setNext()` method is going to be the same thing, write `$this->next = $next;`, and 
press "Option + Enter" to add the property. In the `handle()` method we need to check
for the player's win-streak, that value is inside `$fightResult`, so write
`if ($fightResult->getWinStreak() >= 3` and inside, reward the player by returning `25`.
Below that, add the same check before calling the next handler `if (isset($this->next))`
and inside, call it just as before `return $this->next->handle($player, $fightResult)`.
Hmm, I'm not liking this repetition... can we do better? Actually, we can! but that's
for later. Ok, finish up this method by returning `0` at the bottom.

And now the last handler, on this one we need to roll dice and check
if we rolled a pair or a 7, so I'll call it `CasinoHandler` - who does not like gambling?!

We'll start in the same way, implementing the interface and adding the methods
by pressing "Options + Enter". Also implement `setNext()` as before, inside write
`$this->next = $next` and add the property on top. And now to the guts of the `handle()` method.
Roll a couple of 6-sided dice by writing `$dice1 = Dice::roll(6);` and `$dice2 = Dice::roll(6);`.
Then, the first thing to check is if we rolled 7 because we need to exit immediately, so write
`if ($dice1 + $dice2 === 7)`, and inside return `0`. Below we'll check if we rolled a pair
so we reward the player. Write `if ($dice1 === $dice2)`, and inside return `25`.
If we didn't roll anything good we'll call the next handler, so once again check if it is set
by writing `if (isset($this->next))` and inside write `return $this->next->handle($player, $fightResult)`,
and return `0` at the bottom.

Phew! We finished implementing our handlers, but before we can give this a try
we need to initialize the chain, and while we do so we'll discover a downside of this pattern.
Let's do that next!
