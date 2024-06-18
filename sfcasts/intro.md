# Introduction

Welcome back friends to episode 2 of our design patterns series! In this episode,
we'll continue our journey of building the *greatest* command-line RPG game ever!
And, for this challenging task we'll apply 5 new design patterns.

We'll learn three new *behavioral* patterns, *Command*, *Chain of Responsibility*,
and *State*. These patterns help organize code into separate classes that can
then interact with each other.

Also, we'll learn about the *Factory* pattern, which is a *creational* pattern.
These type of patterns are all about helping instantiate objects, as we've already
seen with the *builder pattern* in episode one. And, as a bonus we'll cover
one of my favorites, the *NullObject* pattern.

For more information about *types* of design patterns, I recommend you to watch
the first chapter of the previous episode.

## Reminder About Design Patterns

Before we get into the guts of this tutorial, let's have a quick recap of what
design patterns are and what we've covered so far.

In a nutshell design patterns are *battle-tested* solutions to software design problems.
Whenever you face a problem, you can look at the design patterns [catalog](https://java-design-patterns.com/patterns/)
and find an appropriate pattern for your use-case. And remember, you can always
modify the pattern in a way that fits best your application.

In episode one we covered five design patterns, *Strategy*, *Builder*, *Observer*,
*PubSub*, and *Decorator*. Those patterns are still used in our game, but you
don't need to understand them to follow this tutorial.

## Project Setup

Ok, let's get this going! I highly recommend you to download the course code from this page and
code along with me. I modified the code base in a significant way from episode one,
so if you're using the previous code, please download this new version. Anyway, after you unzip it,
you'll find a `start/` directory, open up the`README` file for the setup details.
This one is as easy as it gets, just run:

```terminal
composer install
```

And, to play the game run:

```terminal
php bin/console app:game:play
```

We have a few characters to choose from, I'll be a fighter. Sweet! We won!
There were 4 rounds of fighting, we did 78 points of damage and received 25, and
earned 30 XP points! Oh, and at the top you can see how the fight developed. 
This is exciting!

Let's take a look at how this works. Open up the `GameCommand` class. This is a Symfony command
that sets things up, initializes this global `printer` object that's very convenient for printing stuff
in any place we need. Then, it asks you which character you want to be, down below, it starts the battle by
calling `play()` on the `GameApplication` property, then it prints the results, and allows you to keep playing.
So, nothing fancy going on here. All the heavy lifting happens in the `play()` method of `GameApplication`.
I'll press "CMD + B" to go to the definition. This method takes two character objects,
the player, which is "us", and the AI, and makes them attack each other until one of them wins.

If we explore this class a bit more we'll find a few places where we've already applied
some design patterns. Search for the `createCharacter()` method, you can see how we
used the *Builder* pattern to create and configure character objects. And, almost at the bottom of the file
we have the implementation of the *Observer* pattern. We can add or remove observers, and
notify them after a fight finishes.

Alright! It's time to learn about the *Command* pattern and make our game more interactive. That's next!
