# Añadir acciones al juego

Nuestros inversores nos han pedido una nueva característica: "hacer el juego más interactivo". Esos interesados son tan graciosos... De acuerdo, en lugar de que las batallas se desarrollen automáticamente, quieren que el jugador pueda elegir qué acción realizar al principio de cada turno. Ahora mismo, nuestro juego sólo admite la acción Atacar, así que, para hacerlo más real, también añadiremos un par de acciones más: 
Curar y Rendirse.

¡Esta es una gran oportunidad para utilizar el patrón de mando!

## Aplicar el patrón de comandos

El primer paso es identificar el código que hay que cambiar y encapsularlo en su propia clase comando. Abre `GameApplication` y busca el método `play()`. Dentro del bucle `while` hay un comentario que nos dice dónde empieza el turno del jugador. Selecciona el código justo antes de comprobar si el jugador ha ganado y córtalo, y ya que estamos aquí escribamos el código que queremos. Sabemos que queremos instanciar un objeto `AttackCommand` y llamar a `execute()` sobre él, así que vamos a hacerlo. Crea una variable llamada `$playerAction` y establécela en `new AttackCommand()`, esta clase aún no existe. A continuación, escribe `$playerAction->execute()`.

Lo siguiente es manejar los argumentos del comando. Para poder atacar necesitamos tanto los objetos carácter como `FightResultSet`, pero ¿dónde debemos establecerlos, en el constructor o en el método `execute`? La respuesta puede depender de las necesidades de tu aplicación, pero pasarlos al constructor suele ser mejor idea porque te permite desacoplar la instanciación de la ejecución. Puedes crear tus objetos comando en algún momento, y más tarde, si se cumplen las condiciones, ejecutarlos sin preocuparte de sus argumentos.

[[[ code('76622e675c') ]]]

¡Muy bien! Vamos a crear esta clase, pulsaré "Opción + Intro", luego seleccionaré `Create class`. La pondré en el espacio de nombres `App\ActionCommand` en lugar de simplemente `Command` porque no queremos confundirlos con los comandos de Symfony. Pulsa intro y, ¡voilá! Ahí tienes nuestra clase `AttackCommand`. Elimina las anotaciones encima del constructor porque son redundantes. A continuación, dividiré los argumentos en varias líneas y acortaré los espacios de nombres. Mientras lo hago, añadiré `private readonly` para aprovechar la promoción de propiedades del constructor, y cambiaré el nombre de la variable `$ai` por `$opponent` porque tiene más sentido en este contexto.

[[[ code('c2d534937d') ]]]

A continuación, tenemos que implementar el método `execute`. Escribe `public function execute()`, ¡y pega! Di que sí para añadir la sentencia import `GameApplication`, y ahora sólo tenemos que refactorizar las variables locales con `$this`. Ah, y no olvides cambiar `ai`por `opponent`.

[[[ code('65fd127dc3') ]]]

¡Perfecto! Nuestro `AttackCommand` está listo. Ahora, volvamos a `GameApplication` y haremos lo mismo para el turno de la IA. Desplázate un poco hacia abajo hasta que veas el comentario "Turno de la IA", selecciona todo ese código y sustitúyelo por `$aiAction = new AttackCommand()` donde el argumento del jugador es `$ai`, el del adversario es `$player` y `$fightResultSet` al final. A continuación, escribe `$aiAction->execute()`.

[[[ code('ca27dfa284') ]]]

Uf, ¡por fin! Estamos listos para probarlo. Gira hasta tu terminal y ejecuta:

```terminal
php bin/console app:game:play
```

¡Ejecuta! Y... ¡sí! ¡Hemos ganado! ¡Esto es genial! Bueno, no ha cambiado nada, pero está utilizando nuestros comandos bajo el capó.

Estamos listos para añadir más comandos y preguntar al jugador qué acción debe realizar. ¡Eso a continuación!
