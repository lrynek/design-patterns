# Implementing more Actions

* Create other actions. Heal and Surrender
* First create an interface `ActionCommandInterface`
```php
interface ActionCommandInterface
{
    public function execute(): void;
}
```

* Make `AttackCommand` implement it
```php
class AttackCommand implements ActionCommandInterface
```

* copy-paste files from the `tutorial` directory

* Heal Explain constructor:
    - Benefits of putting the arguments on the constructor instead of on the interface
      The constructor for this action has fewer arguments than the `AttackCommand`, it only cares about the player.
      And this is another benefit of putting the arguments in the constructor instead of the interface method
      because this help us keep the interface simple and avoid having arguments that are not
      used by all the commands.
* Explain `execute()` method:
    - Rolling a dice to add some randomness
    - Set the player's health to the new amount but not more than the max health

* Surrender Explanation: For this action I'll cheat a bit because there's no proper way to end the match, so I'll
  set the player's health to 0

* ask player to choose an action
* add `match` to create command object based on selection
* try it

Now, let's go back to the `GameApplication` and ask the player to choose an action.

```php
public function play(Character $player, Character $ai, FightResultSet $fightResultSet): void
{
    $actionChoice = GameApplication::$printer->choice('Your turn', [
        'Attack',
        'Heal',
        'Surrender',
    ]);

    $playerAction = match ($actionChoice) {
        'Attack' => new AttackCommand($player, $ai, $fightResultSet),
        'Heal' => new HealCommand($player),
        'Surrender' => new SurrenderCommand($player),
    };  
}
```

Perfect! It's time of the truth! Let's play a few rounds.

`bin/console app:game:play`

Sweet! it's asking us what to do

attack, then heal, then surrender

## Undo Feature
Explain feature 