# The Chain of Responsibility's *Cousin* - the *Middleware* Pattern

Did you know that the Chain of Responsibility pattern has a cousin? It's called the
Middleware pattern. The two patterns are very similar, but the Middleware pattern
has a subtle difference, this pattern always gets to the end of the chain, in other words,
all handlers (or middlewares) will always be executed. It's a way to introduce
more actions or logic when processing a request. For example, if you use middleware
to handle user registrations, you could have a middleware for sending analytics to an API,
another one for tracking a query parameter in the request for marketing purposes, and so on.

In our game application, we could use it for finish building our character objects.
We could have a middleware that adds a random weapon to the character, or increase
their level, or boost up their health. The takeaway is that all middlewares will be executed,
and it is a great way to make your application more flexible.

## Chain of Responsibility in the Real World - Symfony Voters

Okay, before we move on to the next pattern, let's take a look at a real-world example.
Symfony leverages Chain of Responsibility in its security component. It uses a
concept called "Voters" to determine if a user has access to a specific resource or not.
But Symfony implements this pattern in a slightly different way.
Instead of having explicit references to other voters, Symfony handles
the iteration and execution of voters in the chain internally. Voters themselves
do not have direct references to other voters in the chain.

Let's take a look at how Voters are used in Symfony. Open up your browser and go to
GitHub, inside find the *Symfony/security-core* repository. I already have it open here.
Now search for the `AccessDecisionManager` class. If we pay attention to its constructor
we'll see that it receives a list of *voters*, and at some point, it iterates over them
and calls `vote()`. Of course this class does a lot more than that, but we can see
the essence of Chain of Responsibility.

## Conclusion

Okay, that's the pattern! Let's recap what it does:

It allows you to add or remove handlers dynamically by changing the members or
order of the chain ✅
It leverages the *Single Responsibility Principle*. You can decouple classes that do some work
from classes that uses them ✅
And also leverages the *Open/Closed Principle*. You can introduce new handlers into the 
app without touching existing code ✅

But! It can be hard to debug, as we've already seen ❌
A request may end up unhandled if no object handles it ❌
And, it can affect performance if the chain is too long ❌

Alright! Let's move on to the *State* pattern and handle difficulty levels in a
fashion way!
