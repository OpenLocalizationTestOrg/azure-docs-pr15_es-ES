<properties
   pageTitle="Notificaciones de servicios fiables | Microsoft Azure"
   description="Documentación conceptual para las notificaciones de servicio tela fiable servicios"
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
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="reliable-services-notifications"></a>Notificaciones de servicios confiables

Notificaciones de permiten a los clientes realizar un seguimiento de los cambios realizados a un objeto que le interesadas. Notificaciones de soporte técnico de dos tipos de objetos: *Administrador de estado confiable* y *Diccionario confiable*.

Causas comunes de uso de notificaciones son:

- Creación materializa vistas, como índices secundarios o había agregado vistas filtradas del estado de la réplica. Un ejemplo es un índice ordenado de todas las claves de diccionario confiable.
- Enviar datos supervisión, como el número de usuarios que se agregan en la última hora.

Las notificaciones se activan como parte de la aplicación de operaciones. Debido a, deben corregirse notificaciones de forma tan rápida como eventos posibles y sincrónicos no deben incluir cualquier operaciones costosas.

## <a name="reliable-state-manager-notifications"></a>Notificaciones de estado de fiable Manager

Administrador de estado fiable proporciona notificaciones para los siguientes eventos:

- Transacción
    - Confirmar
- Administrador de estado
    - Volver a generar
    - Adición de un estado confiable
    - Eliminación de un estado confiable

Administrador de estado fiable realiza un seguimiento de las transacciones de inflight actual. El único cambio de estado de la transacción que hace que una notificación se activen es una transacción se confirma.

Administrador de estado fiable mantiene una colección de Estados fiables como diccionario confiable y cola confiable. Administrador de estado fiable activa notificaciones cuando cambia esta colección: se agrega o quita un estado fiable o volver a generar toda la colección.
Se vuelve a generar la colección de administrador de estado fiable en tres casos:

- Recuperación: Cuando se inicia una réplica, recupera su estado anterior desde el disco. Al final de recuperación utiliza **NotifyStateManagerChangedEventArgs** para desencadenar un evento que contiene el conjunto de Estados fiables recuperados.
- Copia completa: antes de que una réplica puede unirse a la definición de configuración, tiene que estar integrado. A veces, esto requiere una copia completa del estado del jefe estado fiable desde la réplica principal que se aplique a la réplica secundaria inactiva. Administrador de estado fiable en la segunda réplica usa **NotifyStateManagerChangedEventArgs** para desencadenar un evento que contiene el conjunto de Estados confiables que adquirió de réplica principal.
- Restaurar: En escenarios de recuperación de desastres, estado de la réplica puede restaurarse desde una copia de seguridad a través de **RestoreAsync**. En estos casos, fiable Administrador de estado en la réplica principal usa **NotifyStateManagerChangedEventArgs** para desencadenar un evento que contiene el conjunto de los Estados confiables restablecerlo desde la copia de seguridad.

Si desea registrarse para las notificaciones de transacciones o notificaciones del Administrador de estado, necesita registrar con los eventos **TransactionChanged** o **StateManagerChanged** en el Administrador de estado confiable. Un lugar común para registrar con estos controladores de eventos es el constructor de su servicio de estado. Cuando se registre en el constructor, no perderá ninguna notificación que se deba a un cambio durante la duración de **IReliableStateManager**.

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

El controlador de eventos de **TransactionChanged** utiliza **NotifyTransactionChangedEventArgs** para proporcionar detalles sobre el evento. Contiene la propiedad de acción (por ejemplo, **NotifyTransactionChangedAction.Commit**) que especifica el tipo de cambio. También contiene la propiedad de transacción que proporciona una referencia a la transacción que ha cambiado.

>[AZURE.NOTE] En la actualidad, se producen eventos **TransactionChanged** solo si se confirma la transacción. La acción es igual a **NotifyTransactionChangedAction.Commit**. Pero, en el futuro, se podrían provoca los eventos para otros tipos de cambios de estado de la transacción. Se recomienda comprobar la acción y procesar el evento solo si es uno que espera.

A continuación se muestra un controlador de eventos de ejemplo **TransactionChanged** .

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

El controlador de eventos de **StateManagerChanged** utiliza **NotifyStateManagerChangedEventArgs** para proporcionar detalles sobre el evento.
**NotifyStateManagerChangedEventArgs** tiene dos subclases: **NotifyStateManagerRebuildEventArgs** y **NotifyStateManagerSingleEntityChangedEventArgs**.
Utilice la propiedad de acción en **NotifyStateManagerChangedEventArgs** para convertir **NotifyStateManagerChangedEventArgs** a la subclase correcta:

- **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
- **NotifyStateManagerChangedAction.Add** y **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

A continuación se muestra un controlador de notificación de **StateManagerChanged** de ejemplo.

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Notificaciones de diccionario confiables

Diccionario confiable proporciona notificaciones para los siguientes eventos:

- Volver a generar: Llama cuando **ReliableDictionary** ha recuperado su estado de una copia de seguridad o recuperado o copiada estado local.
- Borrar: Se llama cuando se ha desactivado el estado de **ReliableDictionary** a través del método **ClearAsync** .
- Agregar: Llama cuando un elemento se ha agregado a **ReliableDictionary**.
- Actualización de llamada cuando se ha actualizado un elemento de **IReliableDictionary** .
- Quitar: Llama cuando se ha eliminado un elemento de **IReliableDictionary** .

Para obtener notificaciones de diccionario fiable, necesita registrar con el controlador de eventos **DictionaryChanged** en **IReliableDictionary**. Es un lugar común para registrar con estos controladores de eventos en el **ReliableStateManager.StateManagerChanged** agregar la notificación.
Cuando se agrega **IReliableDictionary** a **IReliableStateManager** , asegura que no pierda las notificaciones.

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

>[AZURE.NOTE] **ProcessStateManagerSingleEntityNotification** es el método de muestra que el ejemplo anterior de **OnStateManagerChangedHandler** llama.

El código anterior establece la interfaz de **IReliableNotificationAsyncCallback** , junto con **DictionaryChanged**. Dado que **NotifyDictionaryRebuildEventArgs** contiene una interfaz **IAsyncEnumerable** --qué necesidades enumerar asincrónica--reconstruir notificaciones se activan a través de **RebuildNotificationAsyncCallback** en lugar de **OnDictionaryChangedHandler**.

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

>[AZURE.NOTE] En el código anterior, como parte de la notificación de regeneración de procesamiento primero se desactiva el estado agregado mantienen. La colección confiable es que se vuelven a generar con un nuevo estado, todas las notificaciones anteriores son relevante.

El controlador de eventos de **DictionaryChanged** utiliza **NotifyDictionaryChangedEventArgs** para proporcionar detalles sobre el evento.
**NotifyDictionaryChangedEventArgs** tiene cinco subclases. Utilice la propiedad de acción en **NotifyDictionaryChangedEventArgs** convertir **NotifyDictionaryChangedEventArgs** a la subclase correcta:

- **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
- **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
- **NotifyDictionaryChangedAction.Add** y **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
- **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
- **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Recomendaciones

- *Hacer* completar eventos de notificación tan rápido como sea posible.
- *No* ejecutar las operaciones costosas (por ejemplo, operaciones de i/OS) como parte de eventos sincrónicos.
- *Hacer* Compruebe el tipo de acción antes de procesar el evento. Pueden agregar nuevos tipos de acción en el futuro.

Estas son algunas cosas que debe tener en cuenta:

- Como parte de la ejecución de una operación, se activan las notificaciones. Por ejemplo, se activa una notificación de restauración como el último paso de una operación de restauración. Una restauración no se completará hasta que se procesa el evento de notificación.
- Porque se activan las notificaciones como parte de las operaciones de liquidación, clientes ver solo las notificaciones de operaciones confirmadas localmente. Y porque operaciones se garantiza que solo se localmente confirmada (es decir, ha iniciado sesión), podrían no estar o deshacer en el futuro.
- En la ruta de rehacer una sola notificación se activa para cada operación aplicado. Esto significa que si transacción T1 incluye Create(X), Delete(X) y Create(X), recibirá una notificación para la creación de X, uno para la eliminación y otro para la creación de nuevo, en ese orden.
- Para las transacciones que contienen varias operaciones, se aplican las operaciones en el orden en que se recibieron en la réplica principal del usuario.
- Como parte del procesamiento de progreso false, algunas operaciones posible deshacer. Se producen notificaciones para estas operaciones de deshacer, revertir el estado de la réplica a un punto estable. Una diferencia importante de notificaciones de deshacer es que se agregan los eventos que tienen claves duplicadas. Por ejemplo, si es que se pueden deshacer transacción T1, verá una única notificación a Delete(X).

## <a name="next-steps"></a>Pasos siguientes

- [Colecciones confiables](service-fabric-work-with-reliable-collections.md)
- [Inicio rápido de servicios confiable](service-fabric-reliable-services-quick-start.md)
- [Servicios confiables de copia de seguridad y restauración (recuperación)](service-fabric-reliable-services-backup-restore.md)
- [Referencia del programador para colecciones confiable](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
