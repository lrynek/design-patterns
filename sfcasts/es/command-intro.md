# Patrón de mando

¿Listo para un nuevo episodio de patrones de diseño? Coge una taza de café y acomódate, ¡porque vamos a sumergirnos a fondo en el patrón comando! Empezaremos por lo básico: la definición y la teoría. Después, nos divertiremos aplicando lo aprendido a nuestra aplicación de juego.

Empecemos por la pregunta más obvia: ¿qué es el patrón de comandos? ¿Qué es el patrón de mando? El patrón de mando es un patrón de comportamiento. Si has olvidado lo que eso significa, aquí tienes un repaso. Los patrones de comportamiento nos ayudan a diseñar clases con responsabilidades específicas que pueden trabajar juntas, en lugar de poner todo ese código en una clase gigante.

La definición oficial de patrón de mando dice que "encapsulan una petición como un objeto independiente, permitiendo la parametrización de clientes con distintas peticiones, poniendo en cola o registrando peticiones, y soportan operaciones deshacibles"

¿Qué? Vale, intentémoslo de nuevo con una definición menos confusa: "El patrón de comandos encapsula una tarea en un objeto, desacoplando lo que hace, cómo lo hace y cuándo lo hace. También facilita deshacer acciones porque puede mantener un historial de cambios"

¿Aún confuso? No te preocupes Esto tendrá mucho más sentido cuando lo veamos en acción.

## Anatomía del patrón

El patrón de comandos se compone de tres partes principales:

La primera es la "Interfaz de Comandos", que tiene un único método público que suele llamarse `execute()`.

En segundo lugar están los comandos concretos, que implementan la Interfaz de Comandos y contienen la lógica de la tarea.

Por último, tiene un objeto invocador que mantiene una referencia al comando y, en algún momento, llama a `execute()`.

Si has leído sobre esto en Internet, te habrás dado cuenta de que no he mencionado otras dos partes: el receptor y el cliente. El receptor es el objeto que contiene la lógica de negocio, y el cliente se encarga de crear objetos comando.

En mi opinión, esos elementos aumentan la complejidad del diseño y no son supernecesarios. Pueden ser útiles en aplicaciones pesadas, orientadas a objetos, para que las responsabilidades estén mejor organizadas, pero en nuestro caso, utilizarlos sobredimensionaría nuestra aplicación. Así que, por simplicidad, vamos a ignorarlas.

## Ejemplo imaginario

Bien, ahora que ya hemos cubierto el aspecto teórico, veamos un ejemplo. Supongamos que queremos implementar un mando a distancia para un televisor. Nuestro mando a distancia tiene varios botones que nos permiten interactuar con nuestro televisor, como subir o bajar el volumen, encenderlo o apagarlo, etc.

Una forma fácil de hacerlo es con una declaración `switch-case`, donde cada `case`representa la acción de un botón con toda la lógica que necesita para realizar esa acción.

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

Eso es bastante sencillo, pero a medida que añadamos más y más botones, esto se va a complicar. Es desordenado, difícil de mantener y realmente no podremos reutilizar este código en ningún otro sitio.

Tiene que haber una forma mejor de hacerlo... y la hay: con el patrón comando. Podemos agrupar la lógica de cada botón en su propio objeto comando. Entonces, en nuestro método `pressButton()`, sólo tendríamos que llamar a `execute()` en el comando que queramos ejecutar.

Se parece a esto

```php
/**
 * @param array<string, ButtonCommandInterface> $commands
 */
public function __construct(private array $commands)
{
}

public function pressButton(string $button)
{
    if (!isset($this->commands[$button])) {
        throw new NotSupportedButtonException($button);
    }
    
    $this->commands[$button]->execute($this->tv);
}
```

La propiedad `commands` es una matriz de objetos comando de botón, cuya clave es una representación de cadena del botón. Al llamar a `pressButton()`, buscamos el nombre del botón pasado en esta matriz y llamamos a `execute()`. ¡Muy útil!

Instanciar este objeto mando a distancia de TV (y los comandos de botón), y luego utilizarlo, sería algo parecido a esto:

```php
$remote = new Remote([
    'turnOn' => new TurnOnCommand(),
    'turnOff' => new TurnOffCommand(),
    'mute' => new MuteCommand(),
]);

$remote->pressButton('mute'); // executes MuteCommand logic
```

El patrón de comandos es genial, y ya podemos ver lo útil que puede ser. Podemos añadir o eliminar botones sin tocar el código de nuestro método `pressButton()`, y toda la lógica está encapsulada en clases separadas, lo que facilita el mantenimiento y la reutilización de nuestro código. Y, como extra, hemos aplicado con éxito el principio Abrir/Cerrar. Ahora este método está abierto a ampliaciones, pero cerrado a modificaciones.

A continuación: ¡Veamos el patrón comando en acción e implementémoslo en nuestra aplicación!
