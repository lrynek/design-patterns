# Deshacer órdenes de acción

Ahí estábamos... en medio de una feroz batalla y... ¿qué? ¿Hemos perdido? ¡No puede ser! Nuestro oponente ha tenido mucha suerte. Seguro que hay una forma de deshacer esa operación e intentarlo de nuevo, ¿verdad? La hay: con el patrón de comandos. Nuestros comandos sólo necesitan recordar algún estado sobre las cosas que hay que revertir. En nuestro caso, queremos deshacer las acciones del último turno si el jugador perdió, para que tenga una segunda oportunidad de jugar mejor y ganar la batalla. ¡Hagámoslo!

Lo primero que tenemos que hacer es añadir un nuevo método a `ActionCommandInterface`. Ábrelo y, debajo de `execute()`, escribe `public function undo();` sin argumentos.

[[[ code('419537753f') ]]]

A continuación, tenemos que implementarlo en todos nuestros comandos. Empecemos por `AttackCommand`. Ábrelo y, aquí arriba, podemos ver que PHPStorm ya está enfadado con nosotros porque le falta el método `undo()`. Para implementarlo, haz clic en la interfaz y pulsa "Ctrl" + "Intro". Selecciona "Add method stubs", y como el método `undo()` ya está seleccionado, sólo tenemos que pulsar "Enter". En la parte inferior, podemos ver que se ha añadido nuestro método. Por ahora podemos dejar aquí nuestro comentario "TODO", porque aún tenemos que averiguar qué datos debe recordar el comando.

Mantén pulsado "comando" y haz clic en el método `attack()` para ir a la definición. Aquí, podemos ver que consume algo de resistencia y calcula el daño del ataque. Eso significa que necesitamos recordar la resistencia del jugador antes de un ataque, así como el daño infligido. Pero, aquí abajo, puedes ver que estamos enviando daño al jugador contrario con `receiveAttack()`. Así que el valor que realmente buscamos es la variable `$damageDealt`. Vayamos a la parte superior de esta clase y añadamos esas propiedades: `private int $damageDealt` y `private int $stamina`.

[[[ code('8e5811a48c') ]]]

Dentro de `execute()`, antes de que el jugador ataque, escribe `$this->stamina = $this->player->getStamina()`, y aquí abajo, escribe `$this->damageDealt = $damageDealt`. ¡Perfecto!

[[[ code('866cf7b7af') ]]]

Cuando deshacemos un ataque, también tenemos que restaurar la salud del adversario. Para ello, escribe `$this->opponent->setHealth($this->opponent->getCurrentHealth() + $this->damageDealt)`. Ahora, necesitamos restaurar la resistencia del jugador, escribe `$this->player->setStamina($this->stamina)`. Ah, ¡y casi nos olvidamos de revertir el `fightResultSet`! No queremos informar de datos incorrectos. Para revertir el daño infligido, escribe `$this->fightResultSet->of($this->player)->removeDamageDealt($this->damageDealt)`. Y para revertir el daño recibido por el adversario, escribe `$this->fightResultSet->of($this->opponent)->removeDamageReceived($this->damageDealt);`. ¡Genial! Esta clase está lista. ¡Sigamos adelante!

[[[ code('b50477f3a4') ]]]

Abre `HealCommand`, y haremos lo mismo aquí: añadiremos el método `undo()`, haremos clic en el nombre de la interfaz y pulsaremos "Ctrl" + "Intro" y añadiremos el stub. Ahora podemos decidir qué datos necesitamos recordar. Este comando es más sencillo -sólo cambia la salud y la resistencia del jugador-, así que almacenemos sus valores iniciales. En la parte superior de la clase, añade las propiedades `private int $currentHealth`
y `private int $stamina`. 

[[[ code('ab15dc2d0d') ]]]

A continuación, dentro de `execute()`, antes de curar al jugador, guardemos su salud actual con `$this->currentHealth = $this->player->getCurrentHealth()`. Haremos lo mismo con la resistencia: `$this->stamina = $this->player->getStamina()`.

[[[ code('3a3d12e499') ]]]

Ahora podemos implementar el método `undo()`. Sólo necesitamos revertir esas dos propiedades del jugador, así que escribe `$this->player->setHealth($this->currentHealth)`y `$this->player->setStamina($this->stamina)`. ¡Otro comando hecho! ¡Qué bien!

[[[ code('d375df7d96') ]]]

Por último, abre `SurrenderCommand` y hazlo una vez más: añade el método `undo()` y pulsa "Ctrl" + "Intro". Podemos dejar este método vacío porque sería una tontería revertir una acción de rendición.

[[[ code('c5a8d985b7') ]]]

¡Muy bien! Es hora de preguntar al jugador si quiere revertir la última acción en caso de derrota. Cerraré algunos archivos y volveré a `GameApplication`. Busca el turno de la IA, y dentro de este `if()` donde comprobamos si el jugador ha muerto, escribe `$undoChoice = GameApplication::$printer->confirm()`. La pregunta será:

`You've lost! Do you want to undo your last turn?`.

Si la respuesta es "no", tenemos que terminar la batalla y salir, así que moveré estas dos líneas dentro de `if`. 

[[[ code('68023ff3bd') ]]]

Si la respuesta es "sí", deshacemos las acciones del último turno, lo que significa que tenemos que llamar a `undo()` en los objetos de comando. Pero no podemos deshacer estos comandos en cualquier orden. Tenemos que deshacerlos en el orden inverso al que se ejecutaron... o podrían ocurrir cosas raras. Esto es básicamente una pila "FILO" - "Primero en entrar, último en salir".

[[[ code('8c9ce6bd0c') ]]]

De todos modos, deshagamos primero el ataque de la IA con `$aiAttackCommand->undo()`. Luego desharemos la acción del jugador - `$playerAction->undo()`. ¡Estupendo! Ahora podemos probarlo. Gira hasta tu terminal y ejecuta:

```terminal
php bin/console app:game:play
```

Esta vez seremos un arquero, e intentaremos perder la batalla. Quizá empecemos curándonos, y luego atacaremos hasta que, con suerte, perdamos. Y... ¡sí! ¡Hemos perdido! Ahora nos pregunta si queremos deshacer nuestro último turno. Di "sí" o pulsa "Intro" y... ¡el juego continuó! ¡Es una buena señal! Pero para estar absolutamente seguros de que esto funciona como debería, comparemos la cantidad de salud que tenemos ahora con la que teníamos hace un turno.

Vale, ahora mismo tenemos "9/50" y la IA tiene "50/60". Hace un turno, que era el segundo, teníamos "9/50" y la IA "50/60". La IA y nuestro personaje tienen la misma cantidad de salud en ambas rondas, ¡así que esto funciona como se esperaba! ¡Qué bien! Y si esta vez decimos "no" sólo para ver si la batalla concluye... ¡sí! ¡La batalla ha terminado y hemos perdido!

## Puesta en cola de las acciones de mando

Gracias al patrón Comando, pudimos revertir acciones con facilidad. Pero eso no es lo único que el patrón Comando puede hacer por nosotros. También podemos utilizarlo para poner nuestras acciones en una cola y ejecutarlas cuando queramos.

Supongamos que queremos reproducir nuestras batallas y ver cómo se desarrolla todo de nuevo. Podríamos almacenar todos los comandos que ocurrieron en una batalla en algún lugar, como una lista, una base de datos o cualquier otro mecanismo de almacenamiento. Luego, cogemos la lista y los ejecutamos uno a uno. Aunque sería superdivertido trabajar en eso, ¡tenemos más patrones que cubrir! Pero antes de pasar al siguiente, vamos a averiguar dónde y cómo aprovecha Symfony el patrón Comando. Eso a continuación.
