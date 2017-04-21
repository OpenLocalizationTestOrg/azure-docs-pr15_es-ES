<properties
   pageTitle="Colecciones fiables | Microsoft Azure"
   description="Servicios de estado de servicio tela proporcionan colecciones fiables que le permite escribir aplicaciones de nube altamente disponible, scalable y baja latencia."
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introducción a colecciones confiable en services con estado de tela de servicio de Azure

Colecciones confiables permiten escribir aplicaciones de nube altamente disponible, scalable y baja latencia como si estuviera escribiendo aplicaciones único equipo. Las clases en el espacio de nombres de **Microsoft.ServiceFabric.Data.Collections** proporcionan un conjunto de colecciones de cuadro que automáticamente su estado altamente disponibles. Los desarrolladores necesitan programa solo a las API de colección confiable y permitir colecciones confiable administre el estado replicado y local.

La diferencia clave entre colecciones fiable y otras tecnologías de alta disponibilidad (como Redis, servicio de tablas de Azure y servicio de cola de Azure) es que el estado se guarda localmente en la instancia de servicio mientras también se realiza altamente disponibles. Esto significa que:

- Todas las lecturas son locales, que da como resultado una latencia baja y lee de alto rendimiento.
- Escribe todos los provoca el número mínimo de IOs de red, que da como resultado una latencia baja y escribe de alto rendimiento.

![Imagen de evolución de colecciones.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Colecciones confiables pueden considerarse como la evolución natural de las clases **System.Collections** : un nuevo conjunto de colecciones que han sido diseñados para las aplicaciones de la nube y varios equipos sin aumentar la complejidad para el desarrollador. Por lo tanto, son colecciones confiable:

- Replicar: Se duplican cambios de estado para alta disponibilidad.
- Conservar: Los datos se conservan en disco para duración contra interrupciones a gran escala (por ejemplo, un corte de energía de centro de datos).
- Asincrónico: API son asincrónicas para asegurarse de que los subprocesos no se bloquean cuando provocar IO.
- Transacciones: API utilizan la extracción de transacciones para que pueda administrar fácilmente varias colecciones confiable dentro de un servicio.

Colecciones confiables proporcionan coherencia fuerte garantiza listos para facilitar el razonamiento sobre el estado de la aplicación.
Fuerte coherencia se logra asegurándose transacción confirmaciones finalización solo después de que toda la transacción se registró en un quórum mayoría de réplicas, incluidos el principal.
Para lograr la coherencia más débil, aplicaciones pueden confirmar volver al cliente/mando antes de la confirmación asincrónica.

Las API de colecciones fiable son una evolución de la colección simultáneas API (que se encuentra en el espacio de nombres **System.Collections.Concurrent** ):

- Asincrónico: Devuelve una tarea ya que, a diferencia de las colecciones simultáneas, se replicar y se almacenan las operaciones.
- Los parámetros out no: usa `ConditionalValue<T>` para devolver un valor booleano y un valor en lugar de los parámetros de salida. `ConditionalValue<T>`es como `Nullable<T>` pero no requiere T para ser una estructura.
- Transacciones: Usa un objeto de transacción para permitir al usuario agrupar acciones en varias colecciones fiable en una transacción.

En la actualidad, **Microsoft.ServiceFabric.Data.Collections** contiene dos colecciones:

- [Diccionario fiable](https://msdn.microsoft.com/library/azure/dn971511.aspx): representa una colección replicada, transacciones y asincrónica de pares clave/valor. Similar a **ConcurrentDictionary**, la clave y el valor pueden ser de cualquier tipo.
- [Cola confiable](https://msdn.microsoft.com/library/azure/dn971527.aspx): representa una replicada, transacciones y asincrónica estricto, primero en las instrucciones cola. Similar a **ConcurrentQueue**, el valor puede ser de cualquier tipo.

## <a name="isolation-levels"></a>Niveles de aislamiento
Nivel de aislamiento define el grado en que la transacción debe aislada de las modificaciones realizadas por otras transacciones.
Hay dos niveles de aislamiento que son compatibles con colecciones confiable:

- **Lectura reproducible**: Especifica que las instrucciones no pueden leer los datos que se ha modificado pero aún no ha confirmado otras transacciones y que ninguna otra transacción puede modificar los datos que se ha leído la transacción actual hasta que finalice la transacción actual. Para obtener más detalles, consulte [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
- **Instantánea**: Especifica que los datos leídos por cualquier instrucción de una transacción será la versión coherente de los datos existentes en el inicio de la transacción.
La transacción puede reconocer sólo las modificaciones de datos que se han confirmado antes del inicio de la transacción.
Las modificaciones de datos realizadas por otras transacciones después del inicio de la transacción actual no son visibles para instrucciones ejecutar en la transacción actual.
El efecto es como si las instrucciones en una sola transacción de obtienen una instantánea de los datos confirmadas tal y como era al comienzo de la transacción.
Instantáneas son coherentes en las colecciones de confiable.
Para obtener más detalles, consulte [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Colecciones confiables elija automáticamente el nivel de aislamiento para una operación de lectura determinada según la operación y la función de la réplica en el momento de la creación de la transacción.
A continuación se muestra la tabla que muestra los valores predeterminados de nivel de aislamiento para operaciones de diccionario confiable y cola.

| Operación \ rol      | Principal          | Secundario        |
| --------------------- | :--------------- | :--------------- |
| Entidad solo lectura    | Lectura reproducible  | Instantánea         |
| Enumeración \ recuento   | Instantánea         | Instantánea         |

>[AZURE.NOTE] Ejemplos comunes para operaciones de entidad único son `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.

El diccionario confiable y la cola confiable admiten escribe su de lectura.
En otras palabras, cualquier escritura dentro de una transacción estará visible para una lectura siguiente que pertenece a la misma transacción.

## <a name="locking"></a>Bloqueo
En colecciones de fiable, todas las transacciones son dos fases: una transacción no libera los bloqueos que ha adquirido hasta que la transacción termina con una anulación o una confirmación.

Diccionario confiable utiliza bloqueos de fila para todas las operaciones de la entidad.
Cola confiable cambia simultaneidad de estricto propiedad FIFO transacciones.
Cola confiable utiliza bloqueos de nivel de operación que permite una transacción con `TryPeekAsync` o `TryDequeueAsync` y una transacción con `EnqueueAsync` a la vez.
Tenga en cuenta que para conservar FIFO, si un `TryPeekAsync` o `TryDequeueAsync` nunca se observa que la cola confiable está vacía, también bloqueará `EnqueueAsync`.

Escribir operaciones siempre obtienen bloqueos exclusivos.
Las operaciones de lectura, el bloqueo depende de dos factores.
Cualquier operación de lectura terminado, con aislamiento de instantánea es libre de bloqueo.
Cualquier operación de lectura reproducible predeterminada toma bloqueos compartidos.
Sin embargo, para cualquier operación de lectura que admite la lectura de repetición, el usuario puede solicitar un bloqueo de actualización en lugar del bloqueo compartido.
Un bloqueo de actualización es un bloqueo asimétrico utilizado para evitar que una forma común de bloqueo que se produce cuando varias transacciones bloquean recursos posibles actualizaciones en un momento posterior.

Se encuentra debajo de la matriz de compatibilidad de bloqueo:

| Solicitar \ concedido | Ninguno         | Compartido       | Actualización      | En modo exclusivo    |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| Compartido            | Ningún conflicto  | Ningún conflicto  | Conflicto    | Conflicto     |
| Actualización            | Ningún conflicto  | Ningún conflicto  | Conflicto    | Conflicto     |
| En modo exclusivo         | Ningún conflicto  | Conflicto     | Conflicto    | Conflicto     |

Tenga en cuenta que un argumento de tiempo de espera en las API de colecciones confiable se utiliza para la detección de bloqueo.
Por ejemplo, dos transacciones (T1 y T2) están intentando leer y actualizar K1.
Es posible bloqueo, ya que terminan dando el bloqueo compartido.
En este caso, una o ambas de las operaciones mostrará el tiempo de espera.

Observe que el escenario de bloqueo anterior es un buen ejemplo de cómo un bloqueo de actualización puede evitar bloqueos.

## <a name="persistence-model"></a>Modelo de persistencia
El Administrador de estado confiable y colecciones de confiable siguen un modelo sólido que se llama registro y control.
Se trata de un modelo donde se ha iniciado sesión en el disco y aplica solo en la memoria cada cambio de estado.
El propio estado completado se conserva sólo ocasionalmente (también conocido como Punto de control).
La ventaja es que delta se convierte en escribe solo para anexar secuenciales en disco para mejorar el rendimiento.

Para entender mejor el modelo de registro y control, primero veamos el escenario de disco infinito.
El Administrador de estado fiable registra cada operación antes de crear la réplica.
Permite la colección confiable aplicar solo a la operación en la memoria.
Puesto que los registros se conservan, incluso cuando la réplica se produce un error y es necesario reiniciar, el Administrador de estado fiable tiene suficiente información en sus registros de todas las operaciones que se ha perdido la réplica de reproducción.
Como el disco infinito, registros nunca deben quitarse y la colección fiable necesita para administrar el estado de en la memoria.

Ahora vamos a analizar el escenario de disco finita.
Cuando se acumulan registros, el Administrador de estado confiable se ejecutará espacio en disco.
Antes de que suceda, el Administrador de estado confiable debe truncar su registro para crear espacio para los registros más recientes.
Solicitará las colecciones confiable a control su estado en la memoria en el disco.
Es responsable de las colecciones confiable para conservar su estado hasta ese momento.
Una vez las colecciones confiable completar sus puntos de control, el Administrador de estado confiable puede truncar el registro para liberar espacio en disco.
En este modo, cuando es necesario reiniciar la réplica, colecciones confiable recuperará su estado marcada y el Administrador de estado confiable recuperar y reproducir todos los cambios de estado que se han producido desde el punto de control.

>[AZURE.NOTE] Agregar otro valor de puntos de control es que mejora el rendimiento de recuperación en casos comunes.
Esto es porque los controles contienen solo las últimas versiones.

## <a name="recommendations"></a>Recomendaciones

- No modifique un objeto de tipo personalizado devuelto por las operaciones de lectura (por ejemplo, `TryPeekAsync` o `TryGetValueAsync`). Colecciones confiables, como colecciones simultáneas, devuelven una referencia a los objetos y no una copia.
- Hacer copia en profundidad el objeto devuelto de un tipo personalizado antes de modificarlo. Puesto que estructuras y tipos integrados son paso por valor, no necesita hacer una copia en profundidad en ellas.
- No utilice `TimeSpan.MaxValue` para los tiempos de espera. Los tiempos de espera deben usarse para detectar bloqueos.
- No utilice una transacción después de haber sido confirmada, anulado o eliminado.
- No use enumeración fuera del ámbito de la transacción que se creó.
- No crear una transacción dentro de otra transacción `using` instrucción porque puede provocar bloqueos.
- Asegúrese de que su `IComparable<TKey>` implementación es correcta. El sistema toma dependencia esto para la combinación de puntos de control.
- Use bloqueo de actualización al leer un elemento con la intención de actualización para evitar que una determinada clase de bloqueos.
- Considere la posibilidad de usar copias de seguridad y restaurar la funcionalidad para que la recuperación.
- Evite mezclar operaciones de entidad y varias entidades (por ejemplo `GetCountAsync`, `CreateEnumerableAsync`) en la misma transacción debido a los niveles de aislamiento diferente.

Estas son algunas cosas que debe tener en cuenta:

- El tiempo de espera predeterminado es 4 segundos para todas las API de colección confiable. La mayoría de los usuarios no deben reemplazar este.
- El token de cancelación predeterminado es `CancellationToken.None` en todas las API de colecciones confiable.
- El parámetro de tipo de clave (*TKey*) para utilizar un diccionario confiable correctamente debe implementar `GetHashCode()` y `Equals()`. Claves deben ser inmutables.
- Para lograr alta disponibilidad para las colecciones confiable, cada servicio debe tener al menos un destino y réplica mínimo establecer tamaño de 3.
- Operaciones de lectura en el servidor secundario pueden leer versiones que no sean quórum confirmada.
Esto significa que una versión de datos que se leen desde un único secundario podría ser progresa falso.
Por supuesto, lecturas principal son siempre estables: no puede nunca se falso progreso.

## <a name="next-steps"></a>Pasos siguientes

- [Inicio rápido de servicios confiable](service-fabric-reliable-services-quick-start.md)
- [Trabajar con colecciones confiables](service-fabric-work-with-reliable-collections.md)
- [Notificaciones de servicios confiables](service-fabric-reliable-services-notifications.md)
- [Servicios confiables de copia de seguridad y restauración (recuperación)](service-fabric-reliable-services-backup-restore.md)
- [Configuración del Administrador de estado fiable](service-fabric-reliable-services-configuration.md)
- [Introducción a servicios de servicio tela Web API](service-fabric-reliable-services-communication-webapi.md)
- [Uso de los servicios fiable modelo de programación avanzado](service-fabric-reliable-services-advanced-usage.md)
- [Referencia del programador para colecciones confiable](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
