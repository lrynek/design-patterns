# Configuring CoR with Symfony

- Explain what to do
- use Autoconfigure to set up the chain
<<<<<<< Updated upstream
```php
#[Autoconfigure(
    calls: [['setNext' => ['@'.LevelHandler::class]]]
)]
class CasinoHandler implements XpBonusHandlerInterface

#[Autoconfigure(
    calls: [['setNext' => ['@'.OnFireHandler::class]]]
)]
class LevelHandler implements XpBonusHandlerInterface
```

- Remove init code in `GameApplication`
- Configure `$bonusHandler`
```php
    public function __construct(
        #[Autowire(service: CasinoHandler::class)]
        private readonly XpBonusHandlerInterface $xpBonusHandler
    ) {
    }
```

=======
- refactor GameApp constructor
>>>>>>> Stashed changes
- give it a try

## Bonus: Null Object Pattern

<<<<<<< Updated upstream
- Write a good description of what we want to do and why
  - say something about using inheritance but that's boring and I like to avoid it whenever possible
  - say that we'll even remove the `setNext()` method
- Explain the pattern
- add NullHandler
- refactor other handlers so they inject it into the constructor
- remove `setNext()` method from all places including interface
- give it a try
=======
- Write a good description of what we want to do
  - Remove checking if `$next` is set up in handlers
  - say something about using inheritance but that's boring and I like to avoid it whenever possible
  - say that we'll even remove the `setNext()` method
  - explain the pattern
  - add NullHandler
  - refactor other handlers so they inject it into the constructor
  - remove `setNext()` method from all places
  - give it a try

>>>>>>> Stashed changes
