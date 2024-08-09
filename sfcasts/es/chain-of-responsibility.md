# Cadena de responsabilidad

Es hora del patrón de diseño número dos: el patrón Cadena de Responsabilidad. A veces, no existe una definición oficial para un patrón. Ésta no es una excepción, así que aquí tienes mi definición. En pocas palabras, la Cadena de Responsabilidad es una forma de establecer una secuencia de métodos a ejecutar, donde cada método puede decidir ejecutar el siguiente de la cadena o detener la secuencia por completo.

Cuando tenemos que ejecutar una secuencia de comprobaciones para determinar qué hacer a continuación, este patrón puede ayudarnos a hacerlo. Supongamos que queremos comprobar si un comentario es spam o no, y tenemos cinco algoritmos diferentes para ayudarnos a tomar esa determinación. Si alguno de ellos devuelve `true`, significa que el comentario es spam y debemos detener el proceso porque ejecutar algoritmos es caro. En una situación como ésta, tenemos que encapsular cada algoritmo en una clase "manejadora", configurar la cadena y ejecutarla.

Ahora bien, si te preguntas qué es una clase "manejadora" o a qué me refiero con "cadena", ¡grandes preguntas! Echemos un vistazo más de cerca a la anatomía del patrón.

## Anatomía del patrón

La Cadena de Responsabilidades se compone de tres partes:

En primer lugar, tiene un `HandlerInterface`, que suele contener dos métodos: `setNext()` y `handle()`. El método `setNext()` recibe un nuevo objeto `HandlerInterface`. Esto nos permite establecer una secuencia de manejadores, eligiendo qué manejadores queremos en la secuencia y en qué orden aparecen. Esta secuencia se llama cadena. El método `handle()` es donde ponemos nuestra lógica de negocio.

En segundo lugar están los manejadores concretos, que implementan el método `HandlerInterface`. Pueden contener el siguiente objeto `HandlerInterface` (añadido con `setNext()`) y decidir si debe ser llamado o no. Si no contienen el siguiente manejador, este manejador es el último eslabón de la cadena.

Por último, tenemos un cliente que establece la cadena, asegurándose de que la secuencia está en el orden correcto y activa el primer controlador.

Si ahora tienes más preguntas que cuando empezamos, ¡no te preocupes! Tendrá más sentido cuando lo veamos en acción.

## El reto de la vida real

Para nuestro siguiente reto, vamos a aumentar el nivel del jugador. Para ello, recompensaremos a los jugadores con XP extra después de una batalla. Podemos recompensarles de varias formas distintas, pero sólo se debe aplicar una a la vez. Las condiciones para las recompensas de XP son las siguientes:

Uno: Si el jugador es de nivel 1. Dos: Si el jugador ha ganado 3 veces o más seguidas. Y tres: Para añadir algo de aleatoriedad, el jugador lanzará dos dados de seis caras. Ganará si sale un par, pero si el resultado es 7, no.

Cada condición recompensará al jugador con 25 XP.

Bien, ¡hagámoslo! El primer paso que tenemos que dar es crear una interfaz para nuestros manejadores. Dentro del directorio `src/`, crea una nueva carpeta llamada `ChainHandler/`. Y dentro de ella, añadiremos una nueva clase PHP llamada... ¿qué tal `XpBonusHandlerInterface`. Recomiendo incluir el nombre del patrón como parte del nombre de la interfaz para que sea más obvio qué patrón estamos utilizando.

Ahora podemos añadir el primer método - `public function handle()` - y los argumentos - `Character $player` y `FightResult $fightResult`. Estos son los dos objetos necesarios para calcular todas las condiciones anteriores.

Y no olvides añadir el tipo de retorno `int`. Éste será el XP.

Para el siguiente método escribe `public function setNext(XpBonusHandlerInterface $next): void`. Y casi se me olvida añadir la sentencia import `Character`. Pulsa "Opción" + "Intro" y selecciona "Importar clase".

[[[ code('b09d162e41') ]]]

Bien, ¡la interfaz está lista! Es hora de añadir algunos manejadores. Dentro del directorio `ChainHandler/`, añade una clase PHP. La primera condición para recompensar a los jugadores es que sean de nivel 1, así que la llamaremos `LevelHandler`. Ahora tenemos que implementar el `XpBonusHandlerInterface`. ¡Ya hemos visto esto antes! Mantén pulsadas las teclas "Opción" + "Enter" para añadir ambos métodos.

[[[ code('33dbfef597') ]]]

Primero trabajaremos en `setNext()`. Dentro, escribe `$this->next = $next;`. Luego, para añadir la propiedad, haz clic en `$this->next`, pulsa "Opción" + "Intro", y selecciona "Añadir propiedad". 

[[[ code('3fa77b2094') ]]]

¡Perfecto! Ahora vamos a trabajar en el método `handle()`. Queremos recompensar al jugador si su nivel es 1, así que escribe `if ($player->getLevel() === 1)` y, dentro, escribiremos `return 25;`. Si el nivel del jugador no es 1, llamaremos al siguiente manejador, pero sólo si está establecido, así que escribe `if (isset($this->next))`. Dentro, escribiremos `return $this->next->handle($player, $fightResult)`. Al final, escribiremos `return 0;`. Eso significa que hemos llegado al final de la cadena y no se ha aplicado ninguno de los manejadores.

[[[ code('ce1208a9bb') ]]]

¡Sigamos así! Añade otra clase PHP para nuestra segunda condición ganadora -cuando el jugador haya ganado 3 o más veces seguidas- y la llamaremos `OnFireHandler`. Implementa la interfaz... y utiliza el mismo truco con "Opción" + "Intro" para añadir los métodos. 

[[[ code('12978a5278') ]]]

Haremos lo mismo con `setNext()`. Escribe `$this->next = $next`, y mantén pulsada "Opción" + "Intro" para añadir la propiedad.

[[[ code('576b3e127b') ]]]

En el método `handle()`, tenemos que comprobar la racha de victorias del jugador. Ese valor está dentro de `$fightResult`, así que escribe `if ($fightResult->getWinStreak() >= 3)`. Dentro, recompensa al jugador devolviendo `25`. A continuación, añade la misma comprobación que antes, llamando al siguiente controlador: `if (isset($this->next))`... y `return $this->next->handle($player, $fightResult)`.

[[[ code('519ff58562') ]]]

Así que... no me gusta esta repetición. Seguro que hay una forma mejor de hacerlo, ¿verdad? La hay, y hablaremos de ella más adelante, pero por ahora, terminemos este método y devolvamos `0` al final.

[[[ code('37b7dbe1ab') ]]]

Pasemos a la última condición del manejador, en la que tiramos dos dados y comprobamos si hemos sacado un par o un 7. Llamémosla `CasinoHandler`, ya que estamos apostando un poco. Empezaremos de la misma forma, implementando la interfaz y añadiendo los métodos manteniendo pulsadas las teclas "Opción" + "Intro".

[[[ code('849b092e5a') ]]]

Después, igual que antes, implementa `setNext()`. Dentro, escribe `$this->next = $next`y añade la propiedad encima.

[[[ code('13fa412ed8') ]]]

Ahora vamos a trabajar en el método `handle()`. Tira un par de dados de seis caras escribiendo `$dice1 = Dice::roll(6)` y `$dice2 = Dice::roll(6)`. Lo primero que tenemos que hacer aquí es comprobar si hemos sacado 7, porque si es así, tenemos que salir inmediatamente. Escribe `if ($dice1 + $dice2 === 7)` y, dentro, devuelve `0`. A continuación, comprobaremos si hemos sacado un par para poder recompensar al jugador. Escribe `if ($dice1 === $dice2)` y, dentro, devuelve `25`. Si no hemos sacado bien, llamaremos al siguiente controlador, así que, una vez más, comprueba si está activado escribiendo `if (isset($this->next))`. Dentro, escribe `return $this->next->handle($player, $fightResult)`... y devuelve `0` al final.

[[[ code('fbc7f5019f') ]]]

¡Uf! ¡Hemos terminado de implementar nuestros manejadores! Pero antes de probarlo, tendremos que inicializar la cadena. Cuando lo hagamos, veremos una desventaja de este patrón. Hagámoslo a continuación.
