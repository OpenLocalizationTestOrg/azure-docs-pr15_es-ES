<properties 
    pageTitle="Importar y exportar datos de Azure Redis caché | Microsoft Azure" 
    description="Obtenga información sobre cómo importar y exportar datos de almacenamiento de blobs de las instancias de caché de Azure Redis premium" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="import-and-export-data-in-azure-redis-cache"></a>Importar y exportar datos de Azure Redis caché

Importar o exportar es una operación de administración de datos de Azure Redis caché, que permite importar datos en caché de Azure Redis o exportar los datos de Azure Redis caché importar y exportar una instantánea de la base de datos de caché Redis (RDB) desde una caché premium a un blob de página en una cuenta de almacenamiento de Azure. Importar o exportar le permite migrar entre diferentes instancias de caché de Azure Redis o rellenar la caché de datos antes de usar.

Este artículo proporciona a una guía para importar y exportar datos con Azure Redis caché y proporciona las respuestas a las preguntas más frecuentes.

>[AZURE.IMPORTANT] Importar o exportar está en vista previa y solo está disponible para la caché de [nivel premium](cache-premium-tier-intro.md) .

## <a name="import"></a>Importar

Importar puede utilizarse para pasar los archivos Redis compatibles RDB de cualquier servidor Redis que se ejecute en cualquier nube o entorno, incluidos Redis ejecutando en Linux, Windows o en cualquier proveedor de nube como servicios Web de Amazon y otras personas. Importación de datos es una forma sencilla de crear una caché con datos rellenarán. Durante el proceso de importación Azure Redis caché carga los archivos RDB de almacenamiento de Azure en la memoria y luego inserta las teclas en la memoria caché.

>[AZURE.NOTE] Antes de comenzar la operación de importación, asegúrese de que su archivo de base de datos Redis (RDB) o archivos cargados en blobs de página en el almacenamiento de Azure, en la misma región y suscripción como la instancia de Azure Redis caché. Para obtener más información, vea [Introducción a almacenamiento de blobs de Windows Azure](../storage/storage-dotnet-how-to-use-blobs.md). Si exporta el archivo RDB usando la función [Exportar de Azure Redis caché](#export) , el archivo RDB ya está almacenado en un blob de página y esté listo para importar.

1. Para importar BLOB de caché exportados uno o más, [vaya a la memoria caché](cache-configure.md#configure-redis-cache-settings) en el portal de Azure y haga clic en **Importar datos** de la hoja de **configuración** de la instancia de caché.

    ![Importar datos][cache-import-data]

2. Haga clic en **Elegir Blob(s)** y seleccione la cuenta de almacenamiento que contiene los datos que desea importar.

    ![Elija la cuenta de almacenamiento][cache-import-choose-storage-account]

3. Haga clic en el contenedor que contiene los datos que desea importar.

    ![Elija un contenedor][cache-import-choose-container]

4. Seleccione uno o más BLOB importar haciendo clic en el área a la izquierda del nombre de blobs de Windows y, a continuación, haga clic en **Seleccionar**.

    ![Elija BLOB][cache-import-choose-blobs]

5. Haga clic en **Importar** para comenzar el proceso de importación.

    >[AZURE.IMPORTANT] La caché no es accesible para los clientes de caché durante el proceso de importación y se eliminan todos los datos existentes en la caché.

    ![Importar][cache-import-blobs]

    Puede supervisar el progreso de la operación de importación siguiendo las notificaciones desde el portal de Azure o ver los eventos en el [registro de auditoría](cache-configure.md#support-amp-troubleshooting-settings).

    ![Progreso de importación][cache-import-data-import-complete] 


## <a name="export"></a>Exportar

Exportar le permite exportar los datos almacenados en caché de Redis de Azure Redis los archivos RDB compatibles. Puede usar esta característica para mover los datos de una instancia de Azure Redis caché a otro o a otro servidor Redis. Durante el proceso de exportación, se crea un archivo temporal en la máquina virtual que hospeda la instancia del servidor de Azure Redis caché y el archivo se cargará a la cuenta de almacenamiento designado. Cuando se complete la operación de exportación con estado de éxito o error, se elimina el archivo temporal.

1. Para exportar el contenido de la memoria caché de almacenamiento, [vaya a la memoria caché](cache-configure.md#configure-redis-cache-settings) en el portal de Azure y haga clic en **Exportar datos** de la hoja de **configuración** de la instancia de caché.

    ![Elija un contenedor de almacenamiento][cache-export-data-choose-storage-container]

2. Haga clic en **Elegir el contenedor de almacenamiento** y seleccione la cuenta de almacenamiento deseada. La cuenta de almacenamiento debe estar en la misma suscripción y región como la memoria caché.

    >[AZURE.IMPORTANT] Importar o exportar funciona con BLOB de página, que es compatibles con clásica y cuentas de almacenamiento ARM, pero no es compatibles con [Blob cuentas de almacenamiento](../storage/storage-blob-storage-tiers.md#blob-storage-accounts) en este momento.

    ![Cuenta de almacenamiento][cache-export-data-choose-account]

3. Elija el contenedor de blob que desee y haga clic en **Seleccionar**. Para usar nuevo un contenedor, haga clic en **Agregar contenedor** para agregarlo en primer lugar y, a continuación, selecciónelo en la lista.

    ![Elija un contenedor de almacenamiento][cache-export-data-container]

4. Escriba un **prefijo de nombre de blobs de Windows** y haga clic en **Exportar** para iniciar el proceso de exportación. El prefijo del nombre blob se usa para incluir prefijo en los nombres de los archivos generados por esta operación de exportación.

    ![Exportar][cache-export-data]

    Puede supervisar el progreso de la operación de exportación siguiendo las notificaciones desde el portal de Azure o ver los eventos en el [registro de auditoría](cache-configure.md#support-amp-troubleshooting-settings).

    ![][cache-export-data-export-complete]

    Caché siguen estando disponibles para su uso durante el proceso de exportación.


## <a name="importexport-faq"></a>Importar o exportar preguntas más frecuentes

Esta sección contiene las preguntas más frecuentes acerca de la característica importar o exportar.

-   [¿Qué precios niveles pueden usar importar o exportar?](#what-pricing-tiers-can-use-importexport)
-   [¿Puedo importar datos desde cualquier servidor Redis?](#can-i-import-data-from-any-redis-server)
-   [¿Mi caché estará disponible durante una operación de importación o exportación?](#will-my-cache-be-available-during-an-importexport-operation)
-   [¿Puedo usar importar o exportar con clúster Redis?](#can-i-use-importexport-with-redis-cluster)
-   [¿Cómo funciona la importación y exportación con una configuración de bases de datos personalizadas?](#how-does-importexport-work-with-a-custom-databases-setting)
-   [¿Cómo es diferente guardada Redis importar o exportar?](#how-is-importexport-different-from-redis-persistence)
-   [¿Puedo automatizar con PowerShell, CLI u otros clientes de administración de importación o exportación?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
-   [He recibido un error de tiempo de espera durante la operación de importación o exportación. ¿Qué significa?](#i-received-a-timeout-error-during-my-importexport-operation.-what-does-it-mean)
-   [He recibido un error al exportar los datos a almacenamiento de blobs de Windows Azure. ¿Qué ha pasado?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage.-what-happened)


### <a name="what-pricing-tiers-can-use-importexport"></a>¿Qué precios niveles pueden usar importar o exportar?

Importar o exportar solo está disponible en la prima nivel de precios.

### <a name="can-i-import-data-from-any-redis-server"></a>¿Puedo importar datos desde cualquier servidor Redis?

Sí, además de importar los datos exportados de instancias de Azure Redis caché, puede importar archivos RDB desde cualquier servidor Redis que se ejecute en cualquier nube o entorno, como Linux, Windows, o proveedores como servicios Web de Amazon en la nube. Para ello, cargue el archivo RDB desde el servidor que quiera Redis a un blob de página en una cuenta de almacenamiento de Azure y, a continuación, importarla a su instancia de Azure Redis caché premium. Por ejemplo, que desea exportar los datos de la memoria caché de producción e importar en una caché que se usa como parte de un entorno de ensayo para pruebas o migración. 

### <a name="will-my-cache-be-available-during-an-importexport-operation"></a>¿Mi caché estará disponible durante una operación de importación o exportación?

-   **Exportar** - caché siguen estando disponibles y puede continuar usar la memoria caché durante una operación de exportación.
-   **Importar** - caché no estarán disponibles cuando se inicia una operación de importación y no estarán disponibles para su uso cuando se complete la operación de importación.


### <a name="can-i-use-importexport-with-redis-cluster"></a>¿Puedo usar importar o exportar con clúster Redis?

Sí, y puede importar o exportar entre una caché agrupada y una caché no agrupado. Desde Redis clúster [solo es compatible con la base de datos de 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), no se pueden importar los datos en bases de datos que no sea 0. Cuando se importan los datos de caché agrupadas, las teclas se redistribuyen entre el shards del clúster. 

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>¿Cómo funciona la importación y exportación con una configuración de bases de datos personalizadas?

Algunos niveles de precios tienen diferentes [límites de bases de datos](cache-configure.md#databases), por lo que hay algunas consideraciones al importar si ha configurado un valor personalizado para la `databases` configuración durante la creación de la memoria caché.

-   Cuando importa a un nivel de precios con un inferior `databases` límite del nivel desde el que exportó:
    -   Si está utilizando el número predeterminado de `databases` que es 16 para todos los niveles de precios, no se pierden datos.
    -   Si está utilizando un número personalizado de `databases` que entre dentro de los límites para la capa a la que va a importar, no se pierden datos.
    -   Si los datos exportados contenían datos en una base de datos supera los límites del nuevo nivel, no se importan los datos de las bases de datos superiores.

### <a name="how-is-importexport-different-from-redis-persistence"></a>¿Cómo es diferente guardada Redis importar o exportar?

Persistencia de caché Redis Azure le permite conservar los datos almacenados en Redis al almacenamiento de Azure. Cuando se configura persistencia, Azure Redis caché conserva una instantánea de la caché Redis en un formato binario Redis en disco basada en una frecuencia de copia de seguridad configurable. Si se produce un evento grave que deshabilita la caché principal y de réplica, los datos de la caché se restauran automáticamente con la instantánea más reciente. Para obtener más información, consulte [cómo configurar persistencia de los datos de una caché Redis Premium en Azure](cache-how-to-premium-persistence.md).

Importar / exportar le permite reunir datos en o exportación de Azure Redis caché. No configurar copia de seguridad y restauración mediante Redis persistencia.


### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>¿Puedo automatizar con PowerShell, CLI u otros clientes de administración de importación o exportación?

Sí, para PowerShell instrucciones ver [una caché Redis de importar](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) y [exportar una caché Redis](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).



### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>He recibido un error de tiempo de espera durante la operación de importación o exportación. ¿Qué significa?

Si permanecen en la hoja de **datos importar** o **Exportar datos** durante más de 15 minutos antes de iniciar la operación, recibirá un error similar al siguiente.

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Para resolver este problema, iniciar la importación o exportación antes de 15 minutos transcurrido.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>He recibido un error al exportar los datos a almacenamiento de blobs de Windows Azure. ¿Qué ha pasado?

Importar o exportar sólo funciona con archivos RDB almacenados como blobs de página. Otros tipos de blob no se admiten en este momento, incluidas las cuentas de almacenamiento de blobs con niveles de acceso rápidos y fríos.


## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre cómo usar las características premium de caché más.

-   [Introducción al nivel Premium caché Redis de Azure](cache-premium-tier-intro.md)    

  
<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png








