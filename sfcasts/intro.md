# Introduction

Hey friends! Welcome back to episode *two* of our Design Patterns series! In this episode, we'll continue our journey of building the *greatest* command-line RPG game *ever*! To do that, we'll apply not *one*, not *two*, but *five* new design patterns.

We'll learn three new *behavioral* patterns: *Command*, *Chain of Responsibility*, and *State*. These patterns help organize code into separate classes that can then interact with each other.

We'll also learn about the *Factory* pattern, which is a *creational* pattern. This type of pattern is all about helping instantiate objects, just like the *builder* pattern in episode one. And, as a bonus, we'll cover one of my favorites - the *NullObject* pattern.

For more information about design patterns, check out the first chapter of the episode one.

## Reminder About Design Patterns

Before we dive in, let's recap what design patterns are and what we've covered so far.

In a nutshell, design patterns are *battle-tested* solutions to software design problems. When you *encounter* a problem, you can look at the design patterns [catalog](https://java-design-patterns.com/patterns/) and find th ideal pattern for your use case. And *remember*, you can always modify the pattern in a way that will best fit your application.

In episode one, we covered five design patterns: *Strategy*, *Builder*, *Observer*, *PubSub*, and *Decorator*. We're still using those patterns in our game, but you don't need to understand them to follow this tutorial.

## Project Setup

Okay, let's do this! I *highly* recommend that you download the course code from this page and code along with me. The code base has changed quite a bit since episode one, so if you're using the code from *that* tutorial, make sure to download this new version. After you unzip it, you'll find a `start/` directory with the same code you see here. The `README.md` file has all of the setup details you'll need.

This one is as easy as it gets. Run

```terminal
composer install
```

and, to *play* the game, run:

```terminal
php bin/console app:game:play
```

We have a few characters to choose from. I'll be a fighter. And... *sweet*! We won! There were four rounds of fighting, we did 78 points of damage, *received* 25, and earned 30 XP points! *Nice*. And up here, you can see how the fight developed. This is exciting!

So... how does this work? Open `GameCommand.php`. This is a Symfony command that sets things up, initializes this global `$printer` object, which is very convenient for printing information wherever we need it, and then it asks us which character we want to be. Below, it starts the battle by calling `play()` on the `GameApplication` property, prints the results, and allows us to keep playing.

*So*, this isn't super fancy. All the heavy lifting happens in the `play()` method of `GameApplication`. If you hold "CMD" + "B" to go to the definition, we can see that this method takes two character objects - the *player*, which is *us*, and the AI - and it makes them attack each other until one of them wins.

If we explore this class a bit more, we'll find a few places where we've already applied some design patterns. If you search for the `createCharacter()` method, you can see how we used the *Builder* pattern to create and configure character objects. And, *way* down here, we're using the *Observer* pattern, adding or removing observers, and notifying them after the fight is finished.

All right! It's time to learn about the *Command* pattern and make our game more interactive. That's *next*.
