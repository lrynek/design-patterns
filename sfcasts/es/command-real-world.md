# El patrón Command en el mundo real

Ahora que somos profesionales en la aplicación del patrón Command, ¿puedes adivinar dónde lo utiliza Symfony? Si has dicho "Consola Symfony", ¡estás en lo cierto! Y, lo hemos estado utilizando todo el tiempo. Abre `GameCommand`. Podemos ver que extiende a partir de esta clase base `Command`. 

[[[ code('ad32a2b203') ]]]

Esto pertenece a Symfony, y viene con un montón de cosas buenas. ¡Míralo! Mantén pulsado "comando" y haz clic en el nombre de la clase para abrirla. Tiene un montón de cosas, y eso tiene sentido. Tiene que manejar un montón de cosas, como opciones, argumentos y demás. No puede ser tan simple como nuestros comandos porque tiene que ser ampliable y manejar un montón de casos de uso diferentes. Por suerte para nosotros, sólo nos interesa el método `execute()`. Vamos a buscarlo y a ver qué hace. No hace nada. Bueno, nos obliga a implementarlo, y eso es genial porque este es nuestro punto de entrada al sistema de comandos. A Symfony no le importa el código que pongamos aquí, así que podemos hacer lo que queramos. ¡Genial!

Otro lugar donde se utiliza el patrón Comando en Symfony es en el componente Messenger. No es super obvio porque Messenger maneja eventos, colas, trabajadores y demás, y aprovecha otros patrones como el middleware, el despachador de eventos, etc, pero si prestamos atención a los "Manejadores de mensajes", podemos darnos cuenta de que son bastante similares a nuestros comandos de acción. Por ejemplo, el constructor puede tener cualquier dependencia, y el método `__invoke()` es nuestro método `execute()`. Nuestra lógica de negocio va ahí. Lo único que le falta es una interfaz. Y, por cierto, solía tenerla en una versión anterior, pero fue sustituida por atributos PHP porque su único propósito era etiquetar la clase como manejadora de mensajes.

```php
#[AsMessageHandler]
class SendOrderEmailHandler
{
    public function __construct(
        private readonly Mailer $mailer,
        private readonly OrderRepository $orderRepository
    ) {}

    public function __invoke(SendOrderEmail $message): void
    {
        $order = $this->orderRepository->find($message->getOrderId());
        if (!$order) {
            throw new UnrecoverableMessageHandlingException(
                sprintf('Order ID %s not found', $message->getOrderId())
            );
        }

        $this->mailer->sendSubscriptionPaidMail($order);
        $order->setEmailSent(true);
        $this->orderRepository->save($order);
    }
}
```

## Conclusión

Bien, ¡éste es el patrón Comando! Recapitulemos lo que hace:

Es una forma estupenda de encapsular métodos en objetos. ✅ Permite desacoplar el qué del cómo y el cuándo. ✅ Admite operaciones deshechables. ✅ Y aprovecha el Principio de Responsabilidad Única y el Principio Abierto/Cerrado. ✅

Pero, aumenta la complejidad del código porque estamos introduciendo una nueva capa. ❌ Y, como cada comando requiere una clase independiente, puede introducir un número significativo de clases adicionales. ❌

Muy bien ¡Es hora de recompensar a los jugadores tras una dura batalla! Pero hay distintas clases de recompensas y sólo debe aplicarse una. Para ello, aprovecharemos el patrón Cadena de responsabilidades. Eso a continuación.
