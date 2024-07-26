# Implementar más acciones

¡Muy bien! Estamos listos para añadir más acciones a nuestro juego y permitir que los jugadores elijan sus acciones.

Primero, necesitamos crear una interfaz para nuestros comandos. Para ello, dentro del directorio `ActionCommand`, vamos a crear un nuevo archivo PHP y lo llamaremos `ActionCommandInterface`. Dentro, añadiremos un único método llamado `execute()` sin argumentos.

[[[ code('1f1b3c36b1') ]]]

¡Interfaz terminada! A continuación, abramos `AttackCommand` y hagamos que implemente `ActionCommandInterface`. El método `execute()` ya está implementado aquí, así que ya está listo. ¡Qué bien!

Si te has descargado el código del curso, podemos ahorrarnos algo de tiempo cogiendo el resto de acciones que necesitamos en nuestro directorio `tutorial` en la raíz de nuestro proyecto. Copia los archivos `HealCommand` y `SurrenderCommand` en el directorio `ActionCommand`.

Vamos a comprobarlos. Dentro de `HealCommand`, podemos ver que tiene un constructor que sólo se preocupa del objeto jugador.

[[[ code('8eb0c1739e') ]]]

Y en el método `execute()`, tenemos algo de código que calcula cuánto daño se curará el jugador, y luego ajusta la salud del jugador a la nueva cantidad (sin exceder su salud máxima). Por último, imprime un mensaje en la pantalla.

[[[ code('373a6cf572') ]]]

Si echamos un vistazo a `SurrenderCommand`, el constructor aquí es el mismo que el de `HealCommand` - sólo se preocupa del objeto jugador. Y en el método `execute()`, he hecho un poco de trampa porque no hay una forma adecuada de terminar una batalla, así que me he limitado a poner la salud del jugador en `0`. Genial, ¿verdad?

[[[ code('0b9f55a9bc') ]]]

## Pedir al jugador que elija una acción

¡Muy bien! ¡Es hora de pedir al jugador que elija una acción! Antes cerraré algunos archivos. Bien, vuelve a `GameApplication`... y justo antes de definir `$playerAction`, escribe `$actionChoice` y ponlo en `GameApplication::$printer->choice()`, donde la pregunta es `Your Turn`, y las opciones son `Attack`, `Heal` y `Surrender`.

[[[ code('3f134ad613') ]]]

A continuación, sustituiremos la instanciación `AttackCommand` por una expresión `match`, pero cópiala primero, porque la necesitaremos dentro de un momento. Ahora escribe `match ($actionChoice)`. Dentro, el primer caso que queremos añadir es `Attack`, y ahora... pega. Para el segundo caso, escribe `Heal` y ponlo en `new HealCommand($player)`. El tercer y último caso es `Surrender`, y lo pondremos en `new SurrenderCommand($player)`. ¡Perfecto!

[[[ code('3ba62ad342') ]]]

Vamos a probarlo. Gira hasta tu terminal y ejecuta:

```terminal
php bin/console app:game:play
```

Esta vez, seré un "mago arquero", y... ¡mira eso! ¡Nos pregunta qué hacer! Ataquemos primero, y... ¡genial! Hicimos `12` puntos de daño y recibimos `10`, así que nuestra salud actual es `40` de `50`. Intentemos curarnos a continuación. Y... ¡fíjate! Curamos `8` puntos, y la IA hizo `0` puntos de daño porque bloqueamos su ataque, así que nuestra salud actual es `48`. ¡La acción "curar" funciona como se esperaba! Por último, intentemos rendirnos. Elige la opción `2` y... ¡fantástico! Nos rendimos y perdemos la partida. Rendirse no es algo que normalmente celebraríamos, pero en este caso, significa que nuestra acción "rendirse" está funcionando como se supone que debe hacerlo.

¡Choca esos cinco con tu patito de goma porque hemos conseguido que nuestro juego sea más interactivo! Pero... ¿no sería genial poder deshacer nuestra última acción si, digamos... la IA tuviera un poco de suerte? ¡Eso a continuación!
