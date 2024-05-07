# Pattern Definition - Chapter 2
In this episode we are going to talk about the command pattern, which is a behavioral pattern.
If you don't remember what it means, what are you doing here? Go back to episode 1! I'm just kidding.
Here's a quick reminder. Behavioral patterns are those that help you design classes with specific 
responsibilities that can then work together, instead of putting all of that code into one giant class.

Official definition: The command pattern encapsulates a request as a stand-alone object,
allowing parameterization of clients with different requests, queueing or logging requests,
and support undoable operations.
Ok, I'm more confused now! Let's try it again.

A more friendly definition: The command pattern encapsulates a task as a stand-alone object,
decoupling what it does, how it does it, and when it gets done. Also, it allows to undo its actions
by remembering enough information of what things need to be reverted.
Ok, I think it makes more sense now.

# Pattern Anatomy
Let's see the parts that compose the command pattern:
- It has a Command Interface with a single public method that's usually called `execute()`, it can
  hold parameters if needed, but those need to be used by all the commands.
- Concrete Commands which implement the Command Interface, and hold the task's logic.
- An Invoker which holds a command reference and calls `execute()` at some point.

If you look over the internet you'll find that there are two other parts for this pattern,
the receiver and client. Receiver is the object that contains the business logic,
and client is the one that creates the commands.
In my opinion those two parts only increases the complexity of the pattern. They might be useful
in heavy object-oriented applications, but in our case, we would be over-engineering, so we'll
keep it simple and ignore those two parts.

Let's see an example, suppose that we want to implement a remote control for a TV.
It has many buttons to interact with the TV, turn volume up or down, power it on or off, etc.
Without much thinking, we could easily implement a `switch-case` statement for each button
and perform the required actions right there.

Here's an example. We have a `switch` and a `case` for each button, and also the logic lives
just right there. We could make it better by refactoring the logic into private methods, but we would
just be hiding the mess.

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

So, as you can see this approach will get messy pretty quickly, also it's hard to maintain and reuse.

A better approach would be to implement the command pattern by encapsulating the logic of each button
into *command* classes.

(show a little animation: Transform `case` blocks into UML rectangles)

You might be wondering, how do I instantiate the commands? Well, that can be addressed in many ways
but there are other patterns to deal with instantiation in a fashion way, for example the factory pattern.
We'll talk about it later.

Another question you may have is **how** do I use the commands? In our remote control example, we could have
a list of commands indexed by their button name (**turnOn**, **turnOff**, etc.) and call `execute()`.

Like this:

```php
public function pressButton(string $button)
{
    if (!isset($this->commands[$button])) {
        throw new NotSupportedButtonException($button);
    }
    
    $this->commands[$button]->execute($this->tv);
}
```

You can start seeing the benefits of the command pattern. We can add or remove commands
without touching the code of the `pressButton()` method. We've just made it OCP compliant!

Ok, let's go deeper and implement the command pattern in our game application.
