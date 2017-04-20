<properties
    pageTitle="Sincronización de datos sin conexión en aplicaciones móviles Azure | Microsoft Azure"
    description="Referencia conceptual e información general sobre la característica de sincronización de datos sin conexión para las aplicaciones móviles de Azure"
    documentationCenter="windows"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="offline-data-sync-in-azure-mobile-apps"></a>Sincronización de datos sin conexión en aplicaciones móviles de Azure

## <a name="what-is-offline-data-sync"></a>¿Qué es la sincronización de datos sin conexión?

Sincronización de datos sin conexión es un cliente y un servidor característica SDK de aplicaciones móviles de Azure que facilita a los desarrolladores crear aplicaciones que son funcionales sin una conexión de red.

Cuando la aplicación está en modo sin conexión, los usuarios aún pueden crear y modificar los datos, que se guardan en un almacén local. Cuando la aplicación es volver a conectarse, puede sincronizar los cambios locales con el servidor de la aplicación móvil de Azure. La función también incluye compatibilidad para detectar conflictos cuando se cambia el mismo registro en el cliente y el back-end. A continuación, se pueden controlar conflictos en el servidor o el cliente.

Sincronización sin conexión tiene varias ventajas:

* Mejorar la capacidad de respuesta de la aplicación mediante la caché de datos de servidor localmente en el dispositivo
* Crear aplicaciones sólidas que siga siendo útiles si hay problemas de red
* Permitir que los usuarios finales crear y modificar datos, incluso cuando no hay ningún acceso a la red, admitir escenarios con poco o ningún conectividad
* Sincronizar los datos en varios dispositivos y detectar conflictos cuando se modifica el mismo registro por dos dispositivos
* Limitar el uso de la red en redes de latencia alta o de uso medidas

Los siguientes tutoriales muestran cómo agregar la sincronización sin conexión a los clientes móviles con aplicaciones de Azure Mobile:

* [Android: Habilitar la sincronización sin conexión]
* [iOS: habilitar la sincronización sin conexión]
* [IOS Xamarin: habilitar la sincronización sin conexión]
* [Xamarin Android: Habilitar la sincronización sin conexión]
* [Xamarin.Forms: Sincronización sin conexión habilitar](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Plataforma Windows universal: Habilitar la sincronización sin conexión]

## <a name="what-is-a-sync-table"></a>¿Qué es una tabla de sincronización?

Para obtener acceso el extremo "/ tablas", el cliente móvil de Azure SDK proporciona interfaces como `IMobileServiceTable` (cliente .NET SDK) o `MSTable` (iOS cliente). Estas API conectan directamente con el servidor de la aplicación móvil de Azure y se producirá un error si el dispositivo cliente no tiene una conexión de red.

Para admitir el uso sin conexión, la aplicación debería en su lugar use la *tabla de sincronización* API, como `IMobileServiceSyncTable` (cliente .NET SDK) o `MSSyncTable` (iOS cliente). Las mismas operaciones de CRUD (creación, lectura, actualización, eliminación) funcionan con tabla de sincronización API, excepto ahora se leen desde o escribir en un *almacén local*. Antes de pueden realizar las operaciones de sincronización de la tabla, debe inicializarse el almacén local.

## <a name="what-is-a-local-store"></a>¿Qué es un almacén local?

Un almacén local es la capa de persistencia de datos en el dispositivo cliente. El SDK de cliente de aplicaciones móviles de Azure proporciona una implementación de almacén local de forma predeterminada. En Windows, Xamarin y Android, que se basa SQLite; en iOS, se basa en datos básicos.

Para utilizar la implementación de SQLite en Windows Phone o Windows 8.1 de almacenamiento, debe instalar una extensión de SQLite. Para obtener más detalles, consulte [Universal plataforma de Windows: habilitar la sincronización sin conexión]. Android y iOS suministradas con una versión de SQLite en el sistema operativo del dispositivo, por lo que no es necesario hacer referencia a su propia versión de SQLite.

Los programadores también pueden implementar su propio almacén local. Por ejemplo, si desea almacenar datos en un formato cifrado en el cliente móvil, puede definir un almacén local que usa SQLCipher para el cifrado.

## <a name="what-is-a-sync-context"></a>¿Qué es un contexto de sincronización?

Un *contexto de sincronización* está asociado a un objeto de cliente móvil (por ejemplo, `IMobileServiceClient` o `MSClient`) y realiza el seguimiento de cambios realizados con tablas de sincronización. El contexto de sincronización mantiene la configuración de una *cola de operación* que mantiene una lista ordenada de operaciones CUD (crear, actualizar, eliminar) que se enviará más adelante en el servidor.

Un almacén local está asociado con el contexto de sincronización con un método de inicialización como `IMobileServicesSyncContext.InitializeAsync(localstore)` en el [cliente de .NET SDK].

## <a name="how-sync-works"></a>Funciona la sincronización sin conexión

Al utilizar tablas de sincronización, el código de cliente controles cuando cambios locales se sincronizan con un servidor de la aplicación móvil de Azure. Nada se envía a la back-end hasta que haya una llamada a cambios locales de *inserción* . Asimismo, el almacén local se rellena con los nuevos datos solo cuando hay una llamada para *extraer* datos.

* **Inserción**: inserción es una operación en el contexto de sincronización y envía todos los cambios CUD desde la última inserción. Tenga en cuenta que no es posible enviar sólo los cambios de la tabla individuales, porque de lo contrario operaciones podrían enviarse en el orden correcto. Inserción ejecuta una serie de llamadas de resto a la aplicación móvil de Azure copia back-end, que a su vez modifique la base de datos del servidor.

* **Extraer**: extracción se realiza en una base de cada tabla y puede personalizarse con una consulta para recuperar solo un subconjunto de los datos del servidor. El SDK de cliente móvil de Azure, a continuación, inserte los datos resultantes en el almacén local.

* **Inserta implícita**: si una extracción se ejecuta en una tabla que tiene actualizaciones locales pendientes, la extracción primero ejecutará push en el contexto de sincronización. Esto le permite minimizar los conflictos entre los cambios que ya están en la cola y los nuevos datos desde el servidor.

* **Sincronización incremental**: el primer parámetro de la operación de extracción es un *nombre de la consulta* que se usa solo en el cliente. Si usa un nombre de consulta no es null, el SDK de Mobile Azure realizará una *sincronización incremental*.
  Cada vez que una operación de extracción devuelve un conjunto de resultados, la última `updatedAt` marca de tiempo de ese conjunto de resultados se almacena en las tablas de sistema local SDK. Operaciones de extracción sólo recuperará registros después de esa marca de hora.

  Para usar la sincronización incremental, el servidor debe devolver significativo `updatedAt` valores y también debe admitir ordenar por este campo. Sin embargo, dado que el SDK agrega su propia ordenación en el campo updatedAt, no puede usar una consulta de extracción que tiene su propio `$orderBy$` cláusula.

  El nombre de la consulta puede ser cualquier cadena que elija, pero debe ser única para cada consulta lógica en la aplicación.
  En caso contrario, las operaciones de extracción diferentes podrían sobrescribir la misma marca de tiempo de sincronización incremental y las consultas pueden devolver resultados incorrectos.

  Si la consulta tiene un parámetro, es una forma de crear un nombre de consulta únicas incorporar el valor del parámetro.
  Por ejemplo, si va a filtrar en ID de usuario, el nombre de la consulta podría ser como sigue (en C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Si desea no participar en la sincronización incremental, pasar `null` como el identificador de la consulta. En este caso, se recuperarán todos los registros en todas las llamadas a `PullAsync`, que es potencialmente ineficaz.

* **Purging**: puede borrar el contenido de la tienda con `IMobileServiceSyncTable.PurgeAsync`.
  Esto puede ser necesario si tiene datos obsoletos en la base de datos de cliente, o si desea descartar todos los cambios pendientes.

  Purga desactiva una tabla desde el almacén local. Si hay operaciones pendientes de sincronización con la base de datos de servidor, la purga producirá una excepción a menos que se establece el parámetro *Forzar purgar* .

  Datos obsoletos en el cliente, por ejemplo, supongamos que en el ejemplo "lista de tareas pendientes", dispositivo1 extrae sólo los elementos que no se han completado. A continuación, un todoitem "Comprar leche" esté marcado como completado en el servidor por otro dispositivo. Sin embargo, dispositivo1 todavía tendrá la todoitem "Comprar leche" en el almacén local porque extrae sólo los elementos que no están marcadas como completados. Purga desactive este elemento obsoleto.

## <a name="next-steps"></a>Pasos siguientes

* [iOS: habilitar la sincronización sin conexión]
* [IOS Xamarin: habilitar la sincronización sin conexión]
* [Xamarin Android: Habilitar la sincronización sin conexión]
* [Plataforma Windows universal: Habilitar la sincronización sin conexión]

<!-- Links -->
[Cliente de .NET SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Habilitar la sincronización sin conexión]: app-service-mobile-android-get-started-offline-data.md
[iOS: habilitar la sincronización sin conexión]: app-service-mobile-ios-get-started-offline-data.md
[IOS Xamarin: habilitar la sincronización sin conexión]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Habilitar la sincronización sin conexión]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Plataforma Windows universal: Habilitar la sincronización sin conexión]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
