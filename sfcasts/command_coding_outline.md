# Adding Actions to the Game - Chapter 3

The game development department asked for a new feature "make the game more interactive".
Instead of running battles automatically, they want the player to be able to choose
what action to perform at the start of the turn. Right now, our game only
supports the **attack** action, so to make it more real we'll **also** add a couple more actions.
**Heal** and **Surrender** action.

This is a great **opportunity** to put the command pattern in action!

## Applying the Command Pattern
- first identify code that will change
- copy and replace it by the code we want to have
- Use IDE to create the command class and paste
- add the missing arguments. Where to put them?
  - constructor vs method
- Fix GameApp code
- Run the game
  
# Implement other actions
- Create CommandInterface
- make AttackCommand implement it
- copy/paste Heal action
- add Surrender
  

# Ask player to choose an action
- go back to GameApp and paste code to ask the player what action to do
- replace dummy code with real
- run the game
