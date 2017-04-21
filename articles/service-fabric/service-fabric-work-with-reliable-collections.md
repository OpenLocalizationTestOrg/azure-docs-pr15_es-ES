<properties
    pageTitle="Trabajar con colecciones fiables | Microsoft Azure"
    description="Obtenga información sobre los procedimientos recomendados para trabajar con colecciones fiable."
    services="service-fabric"
    documentationCenter=".net"
    authors="JeffreyRichter"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="03/28/2016"
    ms.author="jeffreyr" />

# <a name="working-with-reliable-collections"></a>Trabajar con colecciones confiables

Servicio tela ofrece un modelo de programación con estado para desarrolladores de .NET a través de colecciones de confiable. Más concretamente, servicio tela proporciona diccionario confiable y clases de cola confiable. Cuando utiliza estas clases, su estado es dividido (escalabilidad), replicar (para disponibilidad) y dentro de una partición (para semántica ACID). Veamos un uso típico de un objeto de diccionario confiable y vea qué su haciendo realmente.

~~~
retry:
try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to  
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) { 
   await Task.Delay(100, cancellationToken); goto retry; 
}
~~~

Todas las operaciones en los objetos de diccionario confiable (excepto ClearAsync que no se puede deshacer), se requiere un objeto ITransaction. Este objeto está asociada a ella cualquiera y todos los cambios que está intentando realizar a cualquier diccionario fiable o confiable cola objetos dentro de una sola partición. Adquirir un ITransaction objeto mediante una llamada a la partición del método de CreateTransaction de StateManager.
 
En el código anterior, el objeto ITransaction se pasa al método de AddAsync del diccionario de confiable. Internamente, los métodos de diccionario que acepta una clave toman un bloqueo de lectura o escritura asociado a la clave. Si el método modifica el valor de clave, el método tiene un bloqueo de escritura en la clave y si solo lee el método de valor de la clave, se obtiene un bloqueo de lectura en la clave. Puesto que AddAsync modifica el valor de clave para el nuevo valor pasado, se toma el bloqueo de escritura de la clave. Por lo tanto, si intentan subprocesos 2 (o más) agregar los valores con la misma clave al mismo tiempo, un subproceso va a adquirir el bloqueo de escritura y los otros subprocesos se bloquean. De forma predeterminada, un bloque de métodos de un máximo de 4 segundos adquirir el bloqueo; Después de 4 segundos, se inician TimeoutException. Sobrecargas del método existen que le permite pasar un valor de tiempo de espera explícitas si lo prefiere.
 
Normalmente, escribir el código para que TimeoutException captura y volver a intentar toda la operación (como se muestra en el código anterior). En mi código simple, simplemente lo llamo Task.Delay pasando 100 milisegundos cada vez. Pero, en realidad, podría ser mejor con algún tipo de retraso de interrupción exponencial en su lugar.
 
Una vez que se adquiere el bloqueo, AddAsync agrega las referencias de objeto clave y el valor a un diccionario temporal interno asociado con el objeto de ITransaction. Esto se hace para proporcionarle semántica de lectura-su-propietario-escribe. Es decir, después de llamar a AddAsync, una llamada posterior al TryGetValueAsync (con el mismo objeto ITransaction) devolverá el valor, incluso si aún no confirmado la transacción. A continuación, AddAsync serializa la clave y valor objetos en matrices de bytes y anexa estas matrices de bytes a un archivo de registro en el nodo local. Por último, AddAsync envía las matrices byte a todas las réplicas secundarias por lo que tienen la misma información de clave y valor. Aunque la información de clave y valor se ha escrito en un archivo de registro, la información no se considera parte del diccionario hasta que se ha confirmado la transacción que están asociados. 

En el código anterior, la llamada a CommitAsync confirma todas las operaciones de la transacción. Más concretamente, anexa información de confirmación en el archivo de registro en el nodo local y también envía el registro de confirmación para todas las réplicas secundarias. Una vez que ha respondido quórum (mayoría) de las réplicas, todos los cambios de datos se consideran permanentes y los bloqueos asociados con las teclas que se han manipular mediante el objeto ITransaction se publican para que otras transacciones de subprocesos puede manipular las mismas teclas y sus valores.

Si no se llama CommitAsync (normalmente debido a una excepción), el objeto de ITransaction obtiene eliminado. Al eliminar un objeto de ITransaction sin confirmar, tela de servicio agrega anulación de la información al archivo de registro del nodo local y nada debe que se envíen a cualquiera de las réplicas secundarias. Y, a continuación, se publican los bloqueos asociados con las teclas que se han manipulado a través de la transacción.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Problemas comunes y cómo evitar estos
Ahora que sabe cómo funcionan internamente las colecciones confiables, echemos un vistazo a algunos inadecuados comunes de ellos. Vea el siguiente código:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes, 
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
~~~

Cuando trabaje con un diccionario de .NET normal, puede agregar un valor de clave para el diccionario y, a continuación, cambie el valor de una propiedad (como LastLogin). Sin embargo, este código no funcionará correctamente con un diccionario confiable. Recuerde de la discusión anterior, la llamada a AddAsync serializa los objetos de clave y valor en matrices de bytes y, a continuación, guarda las matrices en un archivo local y también envía a las réplicas secundarias. Si más adelante cambia una propiedad, esto cambia el valor de propiedad en la memoria impacto en el archivo local o los datos que se enviarán a las réplicas. Si el proceso se bloquea, lo que está en la memoria se inicia inmediatamente. Cuando se inicia un nuevo proceso u otra réplica se convierte en principal, el valor de propiedad antiguo es lo que está disponible. 

No haga hincapié suficientemente lo fácil que es realizar el tipo de error mostrado anteriormente. Y sólo aprenderá el error caso que disminuye el proceso. La manera correcta de escribir el código es invertir las dos líneas:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync(); 
}
~~~

Este es otro ejemplo que muestra un error común:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync(); 
   }
}
~~~

De nuevo, con diccionarios .NET normales, el código anterior funciona bien y es un patrón común: el desarrollador utiliza una clave para buscar un valor. Si el valor existe, el desarrollador cambia el valor de la propiedad. Sin embargo, con colecciones confiables, este código tiene el mismo problema como ya ha comentado: __no debe modificar un objeto una vez que haya dado a una colección fiable.__
 
Es la forma correcta para actualizar un valor en una colección confiable obtener una referencia al valor existente y considere el objeto que hace referencia esta referencia inmutable. A continuación, cree un nuevo objeto que es una copia exacta del objeto original. Ahora, puede modificar el estado de este objeto nuevo y escriba el nuevo objeto a la colección de modo que se obtiene serie a matrices de bytes, anexa al archivo local y enviarán a las réplicas. Después de confirmar los cambios, los objetos en memoria, el archivo local y todas las réplicas tengan el mismo estado exacto. Todo es buena!

El código siguiente muestra la manera adecuada para actualizar un valor en una colección de confiable:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire 
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync(); 
   }
}
~~~

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definir tipos de datos no modificable para evitar errores de programador

Lo ideal, sería el compilador informe de errores cuando se crean accidentalmente código que muta el estado de un objeto que se supone que deben contemplarse inmutable. Sin embargo, el compilador C# no tiene la capacidad de hacer esto. Por lo tanto, para evitar posibles errores de programador, se recomienda que defina los tipos de usar con colecciones confiables tipos inmutables. Más concretamente, esto significa que limitarse a tipos de valor principales (como números [Int32, UInt64, etc.], DateTime, Guid, intervalo de tiempo y similares). Y, por supuesto, también puede usar cadena. Es mejor evitar las propiedades de la colección como serializar y deserializar puede con frecuencia puede afectar al rendimiento. Sin embargo, si desea usar propiedades de la colección, se recomienda el uso de. Biblioteca de colecciones inmutable de red (System.Collections.Immutable). Esta biblioteca está disponible para su descarga desde http://nuget.org. También se recomienda cierre sus clases y hacer que los campos de sólo lectura siempre que sea posible.

El tipo de información del usuario a continuación muestra cómo definir un tipo inmutable aprovechar las recomendaciones anteriores.

~~~
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;
 
   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }
 
   [DataMember]
   public readonly String Email;
 
   // Ideally, this would be a readonly field but it can't be because OnDeserialized 
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
~~~

El tipo de ItemId también es un tipo inmutable, como se muestra aquí:

~~~
[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
~~~

## <a name="schema-versioning-upgrades"></a>Control de versiones de esquema (actualizaciones)

Internamente, colecciones confiable serializar los objetos con. DataContractSerializer de red. El número de serie de los objetos se conservan en el disco local de la réplica principal y también se transmitirán a las réplicas secundarias. A medida que crezca su servicio, es probable que desee cambiar el tipo de datos (esquema), que el servicio requiere. Debe abordar el control de versiones de los datos con cuidado. Primero y más importante, siempre debe ser deserializar datos antiguos. Más concretamente, esto significa que el código de deserialización debe ser compatible con versiones anteriores indefinidamente: 333 de versión de su código de servicio debe poder funcionar en datos colocados en una colección de confiable mediante la versión 1 de su código de servicio 5 años.

Además, el código de servicio es actualizado un dominio de actualización en un momento. Por lo tanto, durante la actualización, tiene dos versiones diferentes de su código de servicio que se ejecute simultáneamente. Debe evitar tener la nueva versión de su código de servicio de usar el nuevo esquema como versiones antiguas de su código de servicio no podrá controlar el nuevo esquema. Cuando sea posible, debe diseñar cada versión de su servicio de ser compatible con la versión 1. Más concretamente, esto significa que V1 de su código de servicio podrá pasar por alto los elementos del esquema no controlar explícitamente. Sin embargo, debe poder guardar todos los datos no conoce explícitamente y simplemente escribirá cuando se actualiza un valor o clave de diccionario. 

>[AZURE.WARNING] Aunque puede modificar el esquema de una clave, debe asegurarse de que el código de hash de la clave y algoritmos igual a son estables. Si cambia alguno de estos algoritmos funcionamiento, no podrá buscar la clave del diccionario fiable nunca más.
  
Como alternativa, puede realizar lo que normalmente se conoce como una actualización de la fase 2. Con una actualización de la fase 2, actualice su servicio de V1 a V2: V2 contiene el código que sabe cómo tratar el cambio de esquema nuevo pero no ejecuta este código. Cuando el código de V2 lee datos V1, que funciona en y escribe datos V1. A continuación, tras finalizar la actualización actualizar todos los dominios, es posible algún modo indicar a las instancias de V2 ejecución que la actualización está completa. (Una forma de señal es para dar a conocer una actualización de la configuración, esto es lo que hace esto una actualización de la fase 2). Ahora, las instancias de V2 pueden leer datos V1, convertir a datos V2, trabajar con ella y escribir como datos V2. Cuando otras instancias leer datos V2, no necesitan convertirla, solo funcionan en él y escribir datos V2.

## <a name="next-steps"></a>Pasos siguientes
Para obtener información sobre la creación de contratos de reenvío de datos compatibles, vea [Contratos de datos compatibles con el reenvío](https://msdn.microsoft.com/library/ms731083.aspx).

Para obtener recomendaciones sobre los contratos de datos de control de versiones, vea [Versiones de contratos de datos](https://msdn.microsoft.com/library/ms731138.aspx). 

Para obtener información sobre cómo implementar contratos de datos con tolerancia a errores de versión, consulte [Tolerancia a errores de versión devoluciones de llamada de serialización](https://msdn.microsoft.com/library/ms733734.aspx). 

Para obtener información sobre cómo proporcionar una estructura de datos que puede interactuar entre varias versiones, vea [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
