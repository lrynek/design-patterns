# Desencadenar la cadena de responsabilidad

Abre `GameApplication`. Vamos a configurar la cadena dentro de su constructor, y empezaremos por instanciar todos los manejadores. Escribe `$casinoHandler = new CasinoHandler()`, `$levelHandler = new LevelHandler()`, y finalmente `$onFireHandler = new OnFireHandler()`.

[[[ code('6404b77347') ]]]

Aquí es donde podemos decidir el orden o secuencia de la cadena. Si tu aplicación no necesita ejecutar los manejadores en ningún orden concreto, ¡genial! ¡Puedes configurarlo como quieras! Pero en nuestro caso, sabemos que el `CasinoHandler` puede afectar al jugador si sacamos un `7`, así que hay que llamarlo primero. Los otros dos controladores pueden ir en cualquier orden, así que empezaremos con el `CasinoHandler`.

Escribe `$casinoHandler->setNext($levelHandler)`, seguido de `LevelHandler` - `$levelHandler->setNext($onFireHandler)` - y podemos dejar solo el `OnFireHandler`. Será el último de la cadena. Lo último que tenemos que hacer es establecer el `CasinoHandler` en una propiedad de esta clase, así que escribe `$this->xpBonusHandler = $casinoHandler`, y mantén pulsadas las teclas "Opción" + "Intro" para añadir la propiedad. Y cambia su tipo a `XpBonusHandlerInterface`. ¡Perfecto!

[[[ code('e306ae3b22') ]]]

Vale, esta cadena tiene que activarse cuando termine una batalla, y hay un método muy práctico que podemos utilizar para hacerlo. Busca el método `endBattle()`, y justo antes de notificar a los observadores, activa la cadena escribiendo `$xpBonus = $this->xpBonusHandler->handle()`, donde el primer argumento es `$winner` y el segundo es `$fightResultSet->of($winner)`. Por último, añadiremos la XP extra al `$winner` con `$winner->addXp($xpBonus)`. 

[[[ code('9baa52c9ef') ]]]

¡Genial! ¡Vamos a probarlo!

Gira hasta tu terminal y ejecuta:

```terminal
php bin/console app:game:play
```

Seré un luchador y atacaré hasta que, con suerte, gane, y... ¡ganamos! Pero, hmm... ¿cómo sabemos qué manejador ha entrado en acción? Bueno, ése es un inconveniente de este patrón de diseño. Es difícil de depurar, ya que se puede llamar a cualquier controlador. Para evitarlo, podemos imprimir un mensaje en cada manejador para que sea obvio.

## Depuración de manejadores

Para hacerlo rápidamente, puedes copiar el código de debajo de este vídeo, pegarlo y... ¡listo! ¡Veamos si ha funcionado!

[[[ code('5e342e52da') ]]]

[[[ code('c31a007407') ]]]

[[[ code('8f2f82456a') ]]]

De vuelta a tu terminal, ejecuta

```terminal
php bin/console app:game:play
```

De nuevo, juega hasta que termine la batalla, y... ¡mira esto! Ahí está nuestro mensaje! El `LevelHandler` se activó y nos recompensó con XP extra. ¡Fantástico!

Vale, esto es genial, pero creo que podemos hacerlo mejor. Podemos aprovechar la inyección de dependencias de Symfony para inicializar la cadena. Como extra, refactorizaremos los manejadores para que dejen de comprobar si `$next` está establecido aplicando el patrón Objeto Nulo. ¡Vamos a hacerlo!
