# Pattern Definition - Chapter 2

Are you ready for a new design patterns episode? Go grab a cup of coffee and sit tight 
because we'll take a deep dive into the command pattern. 

Let's start with the boring part first, the definition and theory, and later we'll
have some fun applying it into our game application.

The command pattern is a behavioral kind of pattern. If you already forgot 
what it means, just like me, here's a quick reminder. 
Behavioral patterns are those that help you design classes with specific responsibilities 
that can then work together, instead of putting all of that code into one giant class.

The official definition says that the command pattern **encapsulates** a request 
as a stand-alone object, allowing parameterization of clients with different requests, 
queueing or logging requests, and support undoable operations.

Uhm, what?. Ok, let's try it again with a more friendly definition.

The command pattern encapsulates a task into an object, **decoupling** what it does, 
how it does it, and when it gets done. Also, it allows to undo its actions
by **remembering** enough information of what things need to be reverted.

Yea... I didn't get it too, again, but don't worry, it will get better when we see it in action.

## Pattern Anatomy

The pattern is composed by three main parts:
- It has a Command Interface with a single public method that's usually called `execute()`. 
- There are concrete commands which implement the Command Interface and hold the task's logic.
- And third, there's an **Invoker** object which holds a command reference and calls `execute()` at some point.

If you look over the internet you'll discover that I didn't mention two other parts,
the **receiver** and **client**. The receiver is the object that contains the business logic,
and the client is the one that's on charge of **creating** command objects.

In my opinion, those parts increases the complexity of the design and are not totally required. 
They might be useful in a heavy object-oriented application so the responsibilities are split better, 
but in our case, we would be over-engineering our solution, so we'll
keep it simple and just ignore them.

Ok, that's enough theory. Let's see an example. Suppose that we want to implement a remote control for a TV.
It has many buttons to interact with it, it can turn the volume up or down, power it on or off, and so on.

Without much thinking, we could easily implement it with a `switch-case` statement, where each `case`
would represent a button's action containing all the logic.

```php
public function pressButton(string $button)
{
    switch ($button) {
        case 'turnOn':
            $this->powerOnDevice($this->tv->getAddress())
            $this->tv->initializeSettings();
            $this->tv->turnOnScreen();
            $this->tv->openDefaultChannel();
            break;
        case 'turnOff':
            $this->tv->closeApps();
            $this->tv->turnOffScreen();
            $this->powerOffDevice($this->tv->getAddress())
            break;
        case 'mute':
            $this->tv->toggleMute();
            break;
        ...
    }
}
```

As we keep implementing more buttons this approach will become a mess pretty quickly. Also, 
it would be aa maintenance nightmare and impossible to reuse the code.

A better approach would be to... of course! Implement the command pattern. We could encapsulate 
the logic of each button into their own *command* object. 

(show a little animation: Transform `case` blocks into UML rectangles)

And, in the `pressButton` method we would just call `execute()` on 
the **right** command instance.

Our code would look like this:

```php
public function pressButton(string $button)
{
    if (!isset($this->commands[$button])) {
        throw new NotSupportedButtonException($button);
    }
    
    $this->commands[$button]->execute($this->tv);
}
```

The `commands` property holds a list of command objects, and if the button name
is in the list, we call `execute()`.

Now, you might be wondering, how do I instantiate the commands? 
Well, creating objects can be addressed in many ways but there are excellent patterns 
that deal with instantiation in a fashion way, for example, my favorite, the **factory** pattern.
In our example, we could introduce a `CommandFactory` and delegate it all the instantiation logic,
but wait, that's for another chapter.

From this very moment you can start notice the benefits of the command pattern. We can add or remove buttons
without touching the code of the `pressButton` method. Also, all the logic is encapsulated
into their own class, making it easier to maintain and reuse. And... as a nice side effect, 
we've successfully applied the **Open-Close** principle. This method is now open for extension
but closed for modification.

Next, let's see the command pattern in action and implement it into our game application!
