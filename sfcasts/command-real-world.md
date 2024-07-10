# Command Pattern in the Real World

Now that we're pros at the *command pattern*, can you guess where does Symfony uses it?
If you said Symfony Console, you're right! And, we've been using it all this time.
Open up `GameCommand`, we can see that it extends from this `Command` base class which
belongs to Symfony and comes with a bunch of goodies. Let's take a look, open it up by
holding "Cmd" and clicking on the class name. It has a lot of stuff! And  it makes sense, it
needs to handle a lot of things like options, arguments, and more. It cannot be as simple
as our commands because it needs to be extensible and handle a lot of different use cases. 
The good part is we're only interested in the `execute()` method. Let's find it and see what it does. 
Aha! It does nothing! Well, it forces us to implement it and that's great because this is
our *entry point* to the command system. Symfony does not care what code we put here, we can get
as wild as we want.

Another place where the *command pattern* is used in Symfony is the Messenger component.
It's not that obvious because Messenger is more about handling events, queues, workers and more,
and it leverages other patterns like *middleware*, *event dispatcher*, etc. But, if we pay attention
to "Message Handlers", we can tell that they are quite similar to our action commands. Look
at this example, the constructor can have any dependency, and the `__invoke()` method
is our `execute()` method, our business logic goes there. The only thing it's missing
is an interface, which it used to have in a previous version but got replaced by
PHP attributes because it's only purpose was to tag the class as a message handler.

## Conclusion

Ok, that's all about the *command pattern*. Let's recap its pros and cons:

Pros:
- The *command pattern* is a great way to encapsulate methods into objects.
- Allow us to *decouple* the what from the how and when.
- It's handy when you need to support *undoable* operations.
- It is well accepted by libraries and frameworks.
- And, it leverages the *Single Responsibility Principle* and the *Open/Closed Principle*.

Cons:
- Increases code complexity because we're introducing a new layer. And as each command
requires a separate class, it can introduce a significant amount of additional classes.

Alright! It's time to reward the player after finishing a battle, but there are
different kinds of rewards and only one should apply. We'll leverage
the *chain of responsibility* pattern to do so. 
That's next!
