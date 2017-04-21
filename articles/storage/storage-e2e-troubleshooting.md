<properties
    pageTitle="Solución de problemas con métrica de almacenamiento de Azure y registro, AzCopy y el analizador de mensaje final a final | Microsoft Azure"
    description="Un tutorial que muestra la solución de problemas de llevar a cabo con el análisis de almacenamiento de Azure, AzCopy y analizador de mensaje de Microsoft"
    services="storage"
    documentationCenter="dotnet"
    authors="robinsh"
    manager="carmonm"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Solución de problemas de llevar a cabo con métrica de almacenamiento de Azure y registro, AzCopy y el analizador de mensaje

[AZURE.INCLUDE [storage-selector-portal-e2e-troubleshooting](../../includes/storage-selector-portal-e2e-troubleshooting.md)]

## <a name="overview"></a>Información general

Diagnosticar y solucionar problemas son una clave aptitudes para la creación y compatibilidad de las aplicaciones de cliente con Microsoft Azure almacenamiento. Debido a la naturaleza distribuida de una aplicación de Azure, pueden ser más complejas que en entornos tradicionales de diagnóstico y solución de problemas de rendimiento y errores.

En este tutorial, mostramos cómo identificar cliente ciertos errores que pueden afectar al rendimiento y solucionar los problemas de llevar a cabo mediante herramientas proporcionadas por Microsoft y el almacenamiento de Azure, para optimizar la aplicación cliente.

Este tutorial proporciona una exploración práctica de un escenario de solución de problemas de llevar a cabo. Para obtener una guía detallada conceptual para solucionar problemas de aplicaciones de almacenamiento de Azure, consulte [Monitor, diagnosticar y solucionar problemas de Microsoft Azure almacenamiento](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Herramientas para solucionar problemas de aplicaciones de almacenamiento de Azure

Para solucionar problemas con Microsoft Azure almacenamiento de aplicaciones de cliente, puede usar una combinación de herramientas para determinar cuándo se ha producido un problema y lo que puede ser la causa del problema. Estas herramientas incluyen:

- **Análisis de almacenamiento de azure**. [Análisis de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/hh343270.aspx) proporciona métricas y registro para el almacenamiento de Azure.
    - **Métrica de almacenamiento** sigue métricas transacción y métricas de capacidad para su cuenta de almacenamiento. Métricas puede determinar cómo se comporta una aplicación según una gran variedad de medidas diferentes. Para obtener más información sobre los tipos de métricas un seguimiento de análisis de almacenamiento, consulte [Esquema de la tabla de métrica de almacenamiento análisis](http://msdn.microsoft.com/library/azure/hh343264.aspx) .

    - **Almacenamiento de registro** registra cada solicitud a los servicios de almacenamiento de Azure en un registro de servidor. El registro de seguimiento de datos detallados de cada solicitud, incluidos la operación realizada, el estado de la operación y la información de latencia. Para obtener más información acerca de los datos de la solicitud y la respuesta que se escriben en los registros de análisis de almacenamiento, consulte [Formato de registro de análisis de almacenamiento](http://msdn.microsoft.com/library/azure/hh343259.aspx) .

> [AZURE.NOTE] Cuentas de almacenamiento con un tipo de replicación de almacenamiento de zona redundantes (ZRS) no tiene la métrica o la capacidad de registro habilitado en este momento. 

- **Portal de azure**. Puede configurar el registro y métricas para su cuenta de almacenamiento en el [Portal de Azure](https://portal.azure.com). También puede ver diagramas y gráficos que muestran cómo la aplicación está realizando lo largo del tiempo y configurar alertas para que le notifique si la aplicación se realiza de forma distinta de lo esperado de una métrica específica.

    Para obtener información sobre la configuración de supervisión en el Portal de Azure, consulte [Monitor de una cuenta de almacenamiento en el Portal de Azure](storage-monitor-storage-account.md) .

- **AzCopy**. Registros del servidor para el almacenamiento de Azure se almacenan como BLOB, por lo que puede usar AzCopy para copiar el BLOB de registro en un directorio local para análisis con el analizador de mensaje de Microsoft. Para obtener más información sobre AzCopy, consulte [transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md) .

- **Analizador de mensajes de Microsoft**. Mensaje Analyzer es una herramienta que utiliza archivos de registro y muestra los datos de registro en un formato visual que hace que sea fácil de filtrar, buscar y agrupar datos del registro en conjuntos útiles que puede usar para analizar los errores y problemas de rendimiento. Consulte [La Guía de funcionamiento de analizador de mensajes de Microsoft](http://technet.microsoft.com/library/jj649776.aspx) para obtener más información sobre el analizador de mensaje.

## <a name="about-the-sample-scenario"></a>Acerca del escenario de ejemplo

En este tutorial, se deberá examinar un escenario donde métrica de almacenamiento de Azure indica una tasa de éxito de porcentaje baja para una aplicación que llama el almacenamiento de Azure. La métrica de tasa de éxito de porcentaje baja (que se muestra como **PercentSuccess** en el [Portal de Azure](https://portal.azure.com) y en las tablas de métricas) realiza un seguimiento de las operaciones que se realice correctamente, pero que devuelve un código de estado HTTP que es mayor que 299. En los archivos de registro de almacenamiento del servidor, estas operaciones se registran con un estado de transacción de **ClientOtherErrors**. Para obtener más detalles sobre la métrica de éxito de porcentaje baja, vea [métricas mostrar PercentSuccess baja o las entradas del registro de análisis tienen operaciones con el estado de la transacción de ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Operaciones de almacenamiento de Azure pueden devolver códigos de estado HTTP mayor que 299 como parte de su funcionalidad normal. Pero estos errores en algunos casos indican que es posible que pueda optimizar la aplicación cliente para mejorar el rendimiento.

En este escenario, se tratará de una tasa de éxito de porcentaje baja a ser cualquier cosa menos de 100%. Sin embargo, puede elegir un nivel de métrica diferente, según sus necesidades. Se recomienda que durante las pruebas de la aplicación, establecer una tolerancia de línea base para los indicadores de rendimiento clave. Por ejemplo, es posible que determine, basado en las pruebas, que la aplicación debería tener una tasa de éxito de porcentaje coherente de 90% o 85%. Si los datos de métricas muestran que la aplicación se desvían de ese número, a continuación, puede investigar la causa el aumento.

Para el escenario de ejemplo, una vez que nos hemos establecido que la métrica de la tasa de éxito de porcentaje es inferior al 100%, se le examine los registros para buscar los errores que relacionar con las métricas y usan para averiguar qué está causando la tasa de éxito de porcentaje inferior. Echemos un vistazo específicamente errores en el intervalo de 400. A continuación, se deberá investigar más de cerca 404 errores (no encontrado).

### <a name="some-causes-of-400-range-errors"></a>Algunas causas de errores de rango 400

Los ejemplos siguientes, se muestra una muestra de algunos errores 400 rango para las solicitudes de almacenamiento de blobs de Windows Azure y sus causas posibles. Cualquiera de estos errores, así como los errores en el intervalo de 300 y el intervalo de 500, pueden contribuir a una tasa de éxito de porcentaje baja.

Tenga en cuenta que las listas siguientes están lejos completa. Ver [estado y códigos de Error](http://msdn.microsoft.com/library/azure/dd179382.aspx) en MSDN para obtener más información acerca de errores generales de almacenamiento de Azure y errores específicos de cada uno de los servicios de almacenamiento.

**Ejemplos de código 404 (no encontrado) de estado**

Se produce cuando se produce un error en una operación de lectura en un contenedor o blob porque no se encuentra el blob o el contenedor.

- Se produce si se ha eliminado un contenedor o BLOBs por otro cliente antes de esta solicitud.
- Se produce si está utilizando una llamada de API que crea el contenedor o blob después de comprobar si existe. Las APIs CreateIfNotExists realizar una llamada de cabeza en primer lugar para comprobar la existencia del contenedor o blob; Si no existe, se devuelve un error 404 y, a continuación, se realiza una segunda llamada SUPERPONER para escribir el contenedor o blob.

**Ejemplos de 409 (conflicto) el código de estado**

- Se produce si usa una API crear para crear un nuevo contenedor o blobs, sin comprobar la existencia de primero y ya existe un contenedor o blob con ese nombre.
- Se produce si se elimina un contenedor, y se intenta crear un nuevo contenedor con el mismo nombre antes de que finalice la operación de eliminación.
- Se produce si especifica una concesión en un contenedor o blob y ya hay una concesión presentar.

**Código de estado 412 (error en la condición previa) ejemplos**

- Se produce cuando no se ha alcanzado la condición especificada en un encabezado condicional.
- Se produce cuando el identificador de concesión especificado no coincide con el identificador de concesión en el contenedor o blob.

## <a name="generate-log-files-for-analysis"></a>Generar archivos de registro para análisis

En este tutorial, usaremos analizador de mensaje para trabajar con tres tipos de archivos de registro, aunque podría elegir trabajar con cualquiera de las siguientes opciones:

- El **registro del servidor**, que se crea al habilitar el registro de almacenamiento de Azure. El registro del servidor contiene datos sobre cada operación denominada frente a uno de los servicios de almacenamiento de Azure - blobs, cola, tabla y archivo. El registro del servidor indica qué operación se denominaba y con qué código de estado devueltos, así como otros detalles sobre la solicitud y la respuesta.
- El **registro de cliente. NET**, que se crea al habilitar el registro de cliente desde dentro de la aplicación .NET. El registro de cliente incluye información detallada sobre cómo el cliente prepara la solicitud y recibe y procesa la respuesta.
- El **registro de seguimiento de red HTTP**, que obtiene los datos de HTTP/HTTPS convocatoria y respuesta datos, incluidos para operaciones de almacenamiento de Azure. En este tutorial, se generará el seguimiento de red mediante el analizador de mensaje.

### <a name="configure-server-side-logging-and-metrics"></a>Configurar el registro de servidor y mediciones

En primer lugar, se deberá configurar el registro de almacenamiento de Azure y métricas, para que podamos datos desde la aplicación cliente para analizar. Puede configurar el registro y mediciones de varias maneras: a través del [Portal de Azure](https://portal.azure.com), con PowerShell, o mediante programación. Vea [Habilitar métrica de almacenamiento y ver datos de métricas](http://msdn.microsoft.com/library/azure/dn782843.aspx) y [Habilitar el registro de almacenamiento y acceso a datos de registro](http://msdn.microsoft.com/library/azure/dn782840.aspx) en MSDN para obtener más información sobre la configuración de registro y mediciones.

**A través del Portal de Azure**

Para configurar el registro y métricas para su cuenta de almacenamiento con el [Portal de Azure](https://portal.azure.com), siga las instrucciones en [Monitor de una cuenta de almacenamiento en el Portal de Azure](storage-monitor-storage-account.md).

> [AZURE.NOTE] No es posible establecer métricas minutos con el Portal de Azure. Sin embargo, se recomienda que establecerlas los fines de este tutorial y para investigar problemas de rendimiento con la aplicación. Puede establecer métricas minutos con PowerShell como se muestra a continuación, o mediante programación utilizando la biblioteca de cliente de almacenamiento.
>
> Tenga en cuenta que el Portal de Azure no puede mostrar minutos métricas, solo por hora métricas.

**A través de PowerShell**

Para empezar a usar PowerShell para Azure, consulte [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

1. Use el cmdlet [AzureAccount agregar](http://msdn.microsoft.com/library/azure/dn722528.aspx) para agregar su cuenta de usuario de Azure en la ventana de PowerShell:

    ```
    Add-AzureAccount
    ```

2. En la ventana **iniciar sesión en Microsoft Azure** , escriba la dirección de correo electrónico y contraseña asociada a su cuenta. Azure autentica y guarda la información de credenciales y, a continuación, cierra la ventana.
3. Establecer la cuenta de almacenamiento predeterminada en la cuenta de almacenamiento que está utilizando el tutorial ejecutando estos comandos en la ventana de PowerShell:

    ```
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. Habilitar el registro de almacenamiento para el servicio de blobs de Windows:

    ```
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```
5. Habilitar métrica de almacenamiento para el servicio de blobs, y asegúrese de que sea **- MetricsType** `Minute`:

    ```
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>Configurar el registro de cliente de .NET

Para configurar el registro de cliente para una aplicación .NET, habilitar diagnósticos de .NET en el archivo de configuración de la aplicación (web.config o app.config). Vea [el registro de cliente con la biblioteca de cliente de almacenamiento de .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx) y [de cliente de registro con Microsoft Azure almacenamiento SDK para Java](http://msdn.microsoft.com/library/azure/dn782844.aspx) en MSDN para obtener información detallada.

El registro de cliente incluye información detallada sobre cómo el cliente prepara la solicitud y recibe y procesa la respuesta.

La biblioteca de cliente de almacenamiento almacena datos de registro de cliente en la ubicación especificada en el archivo de configuración de la aplicación (web.config o app.config).

### <a name="collect-a-network-trace"></a>Recopilar un seguimiento de red

Puede usar el analizador de mensaje para recopilar un seguimiento de red HTTP/HTTPS mientras se ejecuta la aplicación cliente. Analizador de mensaje usa [Fiddler](http://www.telerik.com/fiddler) en el back-end. Antes de recopilar el seguimiento de red, le recomendamos que configure Fiddler para grabar el tráfico HTTPS sin cifrar:

1. Instale [Fiddler](http://www.telerik.com/download/fiddler).
2. Inicie Fiddler.
2. Seleccione herramientas **| Opciones de Fiddler**.
3. En el cuadro de diálogo Opciones, asegúrese de que **Se conecta HTTPS de capturar** y **Descifrar el tráfico de HTTPS** están seleccionadas, como se muestra a continuación.

![Configurar las opciones de Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

El tutorial, recopilar y guardar un seguimiento de red primero en el analizador de mensaje y luego crear una sesión de análisis para analizar el seguimiento y los registros. Recopilar un seguimiento de red en el analizador de mensaje:

1. En el analizador de mensaje, seleccione **archivo | Seguimiento rápido | Sin cifrar HTTPS**.
2. El seguimiento de comenzará inmediatamente. Seleccione **Detener** para detener el seguimiento de modo que se puede configurar para el tráfico de almacenamiento de seguimiento solo.
3. Seleccione **Editar** para editar la sesión de seguimiento.
4. Seleccione el vínculo **Configurar** a la derecha del proveedor ETW **WebProxy de Pef de Microsoft** .
5. En el cuadro de diálogo **Configuración avanzada** , haga clic en la ficha **proveedor** .
6. En el campo de **Filtro de nombre de host** , especifique los extremos de almacenamiento, separados por espacios. Por ejemplo, puede especificar los extremos de las siguientes: cambiar `storagesample` el nombre de su cuenta de almacenamiento:

    ```
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Salir del cuadro de diálogo y haga clic en **reiniciar** para comenzar a recopilar el seguimiento con el filtro de nombre de host en su lugar, para que el tráfico de red de almacenamiento de Azure solo se incluye en el seguimiento.

>[AZURE.NOTE] Cuando haya terminado de recopilar el seguimiento de red, le recomendamos encarecidamente que revertir la configuración que han cambiado en Fiddler para el tráfico HTTPS descifrado. En el cuadro de diálogo Opciones de Fiddler, desactive las casillas de verificación **Capturar HTTPS conecta** y **Descifrar el tráfico de HTTPS** .

Consulte [usar las características de seguimiento de red](http://technet.microsoft.com/library/jj674819.aspx) en Technet para obtener más detalles.

## <a name="review-metrics-data-in-the-azure-portal"></a>Revisar los datos de métricas en el Portal de Azure

Una vez que la aplicación se ha estado ejecutando durante un período de tiempo, puede revisar los gráficos de métricas que aparecen en el [Portal de Azure](https://portal.azure.com) observar cómo ha estado realizando su servicio. En primer lugar, vaya a su cuenta de almacenamiento en el Portal de Azure y agregar un gráfico de la métrica de **Éxito porcentaje** .

En el Portal de Azure, ahora verá el **Porcentaje de éxito** en el gráfico de supervisión, junto con cualquier otras métricas que ha agregado. En el escenario se deberá investigar siguiente mediante el análisis de los registros en el analizador de mensaje, la tasa de éxito de porcentaje es algo por debajo del 100%.

Para obtener más detalles sobre cómo agregar métricas a la página de supervisión, vea [Cómo: agregar medidas a la tabla de métricas](storage-monitor-storage-account.md#how-to-add-metrics-to-the-metrics-table).

> [AZURE.NOTE] Puede tardar algún tiempo para sus datos de métricas que aparezca en el Portal de Azure después de habilitar métrica de almacenamiento. Esto es porque no se muestran en el Portal de Azure métricas por hora para la hora anterior hasta que haya transcurrido la hora actual. Además, minutos métricas no se muestran actualmente en el Portal de Azure. Así que dependiendo de si habilita métricas, puede tardar hasta dos horas para ver datos de métricas.

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Utilizar AzCopy para copiar los registros del servidor en un directorio local

Almacenamiento de Azure escribe datos del registro de servidor para blobs, mientras métricas se escriben en tablas. Registro BLOB está disponible en el conocido `$logs` contenedor para su cuenta de almacenamiento. Registro BLOB se denomina jerárquicamente por año, mes, día y hora, por lo que puede encontrar fácilmente el intervalo de tiempo que desea investigar. Por ejemplo, en la `storagesample` cuenta, el contenedor de los BLOB registro 01/02/2015 de 8-9 a.m., es `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Los BLOB individuales en este contenedor se denominan secuencialmente, comenzando por `000000.log`.

Puede usar la herramienta de línea de comandos AzCopy para descargar estos archivos de registro del servidor en una ubicación de su elección en el equipo local. Por ejemplo, puede usar el comando siguiente para descargar los archivos de registro de las operaciones de blob que tuvieron lugar el 2 de enero de 2015 a la carpeta `C:\Temp\Logs\Server`; reemplazar `<storageaccountname>` con el nombre de su cuenta de almacenamiento, y `<storageaccountkey>` con la clave de acceso de cuenta:

    AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V

AzCopy está disponible para su descarga en la página [Descargas de Azure](https://azure.microsoft.com/downloads/) . Para obtener información detallada sobre el uso de AzCopy, consulte [transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md).

Para obtener información adicional acerca de los registros de servidor de descarga, vea [descargar el registro de almacenamiento de datos del registro](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Usar el analizador de mensaje de Microsoft para analizar datos de registro

Analizador de mensaje de Microsoft es una herramienta para capturar, mostrar y analizar el protocolo mensajería tráfico, eventos y otros mensajes del sistema o aplicación en escenarios de diagnósticos y solución de problemas. Analizador de mensaje también le permite cargar, agregar y analizar los datos de registro y guarda archivos de seguimiento. Para obtener más información sobre el analizador de mensaje, vea [La Guía de funcionamiento de analizador de mensajes de Microsoft](http://technet.microsoft.com/library/jj649776.aspx).

Analizador de mensaje incluye activos de almacenamiento de Azure que le ayudarán a analizar servidor, cliente y registros de red. En esta sección se explica cómo usar las herramientas para solucionar el problema de éxito de porcentaje bajo en los registros de almacenamiento.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Descargar e instalar el analizador de mensaje y los activos de almacenamiento de Azure

1. Descargar [Mensaje Analyzer](http://www.microsoft.com/download/details.aspx?id=44226) desde Microsoft Download Center y ejecute al instalador.
2. Iniciar el analizador de mensaje.
3. En el menú **Herramientas** , seleccione **Administrador de activos**. En el cuadro de diálogo **Administrador de activos** , seleccione **descargas**y filtrar en **El almacenamiento de Azure**. Verá los activos de almacenamiento de Azure, tal como se muestra en la siguiente imagen.
4. Haga clic en **Sincronizar todos los elementos de muestra** para instalar a los activos de almacenamiento de Azure. Los activos disponibles son:
    - **Reglas de almacenamiento de azure Color:** Azure almacenamiento color reglas le permiten definir los filtros especiales que utilizan estilos de fuente, color y texto para resaltar los mensajes que contienen información específica en un seguimiento.
    - **Gráficos de almacenamiento de azure:** Los gráficos de almacenamiento Azure son gráficos predefinidos que servidor registro datos del gráfico. Tenga en cuenta que para usar gráficos de almacenamiento de Azure en este momento, sólo permite cargar el registro del servidor en la cuadrícula de análisis.
    - **Almacenamiento de azure analizadores:** Los analizadores de almacenamiento de Azure analizar el almacenamiento de Azure cliente, servidor y registros HTTP para mostrarlas en la cuadrícula de análisis.
    - **Filtros de almacenamiento de azure:** Los filtros de almacenamiento Azure son criterios predefinidos que puede usar para consultar los datos en la cuadrícula de análisis.
    - **Diseños de la vista de almacenamiento de azure:** Diseños de la vista de almacenamiento Azure son diseños de columna predefinidos y agrupaciones en la cuadrícula de análisis.
4. Reinicie el analizador de mensaje después de instalar a los activos.

![Administrador de activos de analizador de mensajes](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [AZURE.NOTE] Instale todos los activos de almacenamiento de Azure muestra los fines de este tutorial.

### <a name="import-your-log-files-into-message-analyzer"></a>Importar archivos de registro en el analizador de mensaje

Puede importar todos los archivos de registro guardada (servidor, cliente y red) en una única sesión en el analizador de mensaje de Microsoft para el análisis.

1. En el menú **archivo** , en el analizador de mensaje de Microsoft, haga clic en **Nueva sesión**y, a continuación, haga clic en **Sesión en blanco**. En el cuadro de diálogo **Nueva sesión** , escriba un nombre para la sesión de análisis. En el panel de **Detalles de la sesión** , haga clic en el botón de **archivos** .
1. Para cargar los datos de seguimiento de red generados por el analizador de mensaje, haga clic en **Agregar archivos**, busque la ubicación donde guardó el archivo .matp de la sesión de seguimiento de web, seleccione el archivo .matp y haga clic en **Abrir**.
1. Para cargar los datos del registro del servidor, haga clic en **Agregar archivos**, busque la ubicación donde haya descargado los registros del servidor y, a continuación, seleccione los archivos de registro del intervalo de tiempo que desea analizar y haga clic en **Abrir**. A continuación, en el panel de **Detalles de la sesión** , establezca la **Configuración de registro de texto** de la lista desplegable para cada archivo de registro de servidor **AzureStorageLog** para asegurarse de que el analizador de mensaje de Microsoft puede analizar correctamente el archivo de registro.
1. Para cargar los datos del registro de cliente, haga clic en **Agregar archivos**, busque la ubicación donde guardó los registros del cliente y, a continuación, seleccione los archivos de registro que desea analizar y haga clic en **Abrir**. A continuación, en el panel de **Detalles de la sesión** , establezca la **Configuración de registro de texto** de la lista desplegable para cada archivo de registro de cliente **AzureStorageClientDotNetV4** para asegurarse de que el analizador de mensaje de Microsoft puede analizar correctamente el archivo de registro.
1. Haga clic en **Inicio** , en el cuadro de diálogo **Nueva sesión** al cargar y analizar los datos del registro. Los datos del registro se muestran en la cuadrícula de análisis de analizador de mensaje.

La imagen siguiente muestra una sesión de ejemplo configurada con el servidor, cliente y los archivos de registro de seguimiento de red.

![Configurar la sesión de analizador de mensajes](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Tenga en cuenta que analizador de mensaje de carga de archivos de registro en la memoria. Si tiene un amplio conjunto de datos del registro, desea filtrar para obtener el mejor rendimiento de analizador de mensaje.

En primer lugar, determinar el período de tiempo que le interesa revisar y mantener este período de tiempo más pequeño posible. En muchos casos desee revisar un período de minutos u horas como máximo. Importar el conjunto de registros que se ajusten a sus necesidades más pequeño.

Si aún tiene una gran cantidad de datos del registro, desea especificar una sesión de filtro para filtrar los datos de registro antes de cargarlo. En el cuadro **Filtro de sesión** , seleccione el botón de la **biblioteca** para elegir un filtro predefinido; Por ejemplo, elija **Global tiempo filtro** de los filtros de almacenamiento de Azure filtro en un intervalo de tiempo. A continuación, puede editar los criterios de filtro para especificar las iniciales y finales de marca de tiempo para el intervalo que desea ver. También puede filtrar por un código de estado en particular; Por ejemplo, puede cargar solo las entradas de registro donde el código de estado es 404.

Para obtener más información sobre cómo importar datos del registro en el analizador de mensaje de Microsoft, consulte [Recuperar datos de los mensajes](http://technet.microsoft.com/library/dn772437.aspx) en TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Use el identificador de solicitud de cliente para relacionar datos de archivo de registro

La biblioteca de cliente de almacenamiento de Azure genera automáticamente un identificador de solicitud de cliente único para cada solicitud. Este valor se escribe en el registro de cliente, el registro del servidor y el seguimiento de red, por lo que puede usar para relacionar datos a través de los tres registros en el analizador de mensaje. Consulte [Id. de solicitud de cliente](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) para obtener información sobre la solicitud del cliente Id.

Las secciones siguientes describen cómo usar las vistas de diseño personalizado y preconfigurado para relacionar y agrupar datos basan en el ID de cliente

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Seleccionar un diseño de vista para mostrar en la cuadrícula de análisis

Los activos de almacenamiento de mensaje Analyzer incluyen diseños de la vista del almacenamiento de Azure que son vistas preconfiguradas que puede usar para mostrar los datos con agrupaciones útiles y columnas para diferentes escenarios. También puede crear diseños de vista personalizada y guardarlas para su reutilización.

La imagen siguiente muestra el menú de **Vista de diseño** disponible al seleccionar **La vista Diseño** de la cinta de opciones de la barra de herramientas. Los diseños de vista para el almacenamiento de Azure se agrupan bajo el nodo de **Almacenamiento de Azure** en el menú. Puede buscar `Azure Storage` en el cuadro de búsqueda para filtrar los datos de almacenamiento de Azure ver solo los diseños. También puede seleccionar la estrella junto a un diseño de vista para que sea un favorito y mostrar en la parte superior del menú.

![Menú de la vista Diseño](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Para empezar con, seleccione **agrupadas por ClientRequestID y módulo**. Este grupo de la vista Diseño, inicie sesión en primer lugar datos de los tres registros por Id. de solicitud de cliente y luego por archivo de registro de origen (o **módulo** en el analizador de mensaje). Con esta vista puede profundizar en un identificador de solicitud de cliente en particular y ver los datos de todos los archivos de registro de tres de solicitud de cliente Id.

La imagen siguiente muestra la vista de diseño que se aplica a los datos de registro de ejemplo con un subconjunto de columnas que se muestran. Puede ver que un identificador de solicitud de cliente en particular, la cuadrícula de análisis muestra los datos del registro de cliente, registro de servidor y seguimiento de red.

![Diseño de la vista de almacenamiento de Azure](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

>[AZURE.NOTE] Archivos de registro diferentes tienen distintas columnas para que cuando se muestran los datos de varios archivos de registro en la cuadrícula de análisis, algunas columnas no pueden contener los datos para una fila determinada. Por ejemplo, en la imagen anterior, las filas de registro de cliente no mostrar los datos de la **marca de tiempo**, **TimeElapsed**, **origen**y **destino** columnas, porque estas columnas no existen en el registro de cliente, pero en el seguimiento de red. Del mismo modo, la columna de **marca de tiempo** muestra los datos de marca de tiempo del registro del servidor, pero se muestra ningún dato para las columnas **TimeElapsed**, de **origen**y de **destino** , que no forman parte del registro del servidor.

Además de usar los diseños de vista del almacenamiento de Azure, también puede definir y guardar sus propios diseños de la vista. Puede seleccionar otros campos que desee para agrupar datos y guardar la agrupación como parte de su diseño personalizado también.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Aplicar reglas de color a la cuadrícula de análisis

Los activos de almacenamiento también incluir las reglas de color, que ofrecen que una visual significa para identificar los distintos tipos de errores en la cuadrícula de análisis. Las reglas de color predefinido que se aplican a errores HTTP, para que aparezcan solo para el seguimiento de red y el registro del servidor.

Para aplicar reglas de color, seleccione **Color de reglas** de la cinta de opciones de la barra de herramientas. Verá las reglas de color de almacenamiento de Azure en el menú. Para el tutorial, seleccione **Errores de cliente (StatusCode entre 400 y 499)**, como se muestra en la siguiente imagen.

![Diseño de la vista de almacenamiento de Azure](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Además de usar las reglas de color de almacenamiento de Azure, también puede definir y guardar sus propias reglas de color.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Grupo y filtrar los datos de registro para encontrar errores 400 rango

A continuación, deberá agrupar y filtrar los datos del registro para buscar todos los errores en el rango de 400.

1. Busque la columna **StatusCode** en la cuadrícula de análisis, haga clic en el encabezado de columna y seleccione el **grupo**.
2. Grupo siguiente, en la columna **ClientRequestId** . Verá que ahora se organizan los datos en la cuadrícula de análisis de código de estado e Id. de solicitud de cliente
1. Si no está ya abierto, mostrar la ventana de la herramienta de filtro de vista. En la cinta de opciones de la barra de herramientas, seleccione la **Herramienta de Windows**, a continuación, el **Filtro de vista**.
2. Para filtrar los datos del registro para mostrar solo los errores de intervalo 400, agregue los siguientes criterios de filtro a la ventana de **Filtro de vista** y haga clic en **Aplicar**:

        (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)

La imagen siguiente muestra los resultados de esta agrupación y filtro. Expandir el campo **ClientRequestID** debajo de la agrupación de código de estado 409, por ejemplo, se muestra una operación que ha generado ese código de estado.

![Diseño de la vista de almacenamiento de Azure](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Después de aplicar este filtro, verá que se excluyen filas desde el registro de cliente, como el cliente de registro no incluye una columna **StatusCode** . Para comenzar, revisaremos el servidor y los registros de seguimiento de red para localizar los 404 errores, y, a continuación, se volverá a examinar las operaciones de cliente dirigidas a ellos en el registro de cliente.

>[AZURE.NOTE] Puede filtrar en la columna **StatusCode** y ver datos de los tres registros, incluido el registro de cliente, si agrega una expresión para el filtro que incluye entradas del registro donde el código de estado es nulo. Para crear esta expresión de filtro, use:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Este filtro devuelve todas las filas desde el cliente de registro y solo las filas del registro del servidor y el registro HTTP donde el código de estado es mayor que 400. Si se aplica al diseño de la vista agrupado por Id. de solicitud de cliente y módulo, puede buscar o desplazarse a través de las entradas del registro para buscarlas donde se representan los tres registros.   

### <a name="filter-log-data-to-find-404-errors"></a>Filtrar los datos de registro para encontrar 404 errores

Los activos de almacenamiento incluye filtros predefinidos que puede usar para restringir los datos del registro para buscar los errores o tendencias que está buscando. A continuación, se aplican dos filtros predefinidos: uno que filtra el servidor y los registros de seguimiento de red para 404 errores y otro que filtra los datos en un intervalo de tiempo especificado.

1. Si no está ya abierto, mostrar la ventana de la herramienta de filtro de vista. En la cinta de opciones de la barra de herramientas, seleccione la **Herramienta de Windows**, a continuación, el **Filtro de vista**.
2. En la ventana de filtro de vista, seleccione **biblioteca**y buscar en `Azure Storage` para encontrar el almacenamiento de Azure filtros. Seleccione el filtro de **404 mensajes (no encontrado) en todos los registros**.
3. Mostrar el menú de la **biblioteca** de nuevo y busque y seleccione el **Filtro de tiempo Global**.
4. Editar las marcas de tiempo que se muestra en el filtro en el rango que desea ver. Esto le ayudará a reducir el rango de datos para analizar.
5. El filtro debería ser similar del siguiente ejemplo. Haga clic en **Aplicar** para aplicar el filtro a la cuadrícula de análisis.

        ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
        (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)

![Diseño de la vista de almacenamiento de Azure](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analizar los datos de registro

Ahora que ha agrupado y filtrar los datos, puede examinar los detalles de las solicitudes individuales que generan 404 errores. En el diseño de la vista actual, los datos se agrupan por ID de solicitud de cliente, luego por el origen del registro. Dado que nos estamos filtrado en las solicitudes donde el campo StatusCode contiene 404, veremos solo el servidor y los datos de seguimiento de red, no los datos del registro de cliente.

La imagen siguiente muestra una solicitud específica donde una operación de obtener Blob produjo un error 404 porque no existe el blob. Tenga en cuenta que algunas columnas se han quitado de la vista estándar para mostrar los datos pertinentes.

![Servidor filtrado y los registros de seguimiento de red](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

A continuación, se le correlación este ID de solicitud de cliente con los datos del registro de cliente para ver las acciones que el cliente estaba teniendo cuando ocurrió el error. Puede mostrar una nueva vista de cuadrícula de análisis para esta sesión ver los datos de registro de cliente, se abreen en una ficha de la segunda:

1. En primer lugar, copie el valor del campo **ClientRequestId** en el Portapapeles. Para ello, seleccione cualquier fila, localizar el campo **ClientRequestId** , el botón secundario en el valor de datos y elija **copiar 'ClientRequestId'**.
1. En la cinta de opciones de la barra de herramientas, seleccione **Nuevo visor**, seleccione **Cuadrícula de análisis** para abrir una nueva pestaña. La ficha nuevo muestra todos los datos en los archivos de registro, sin agrupar, filtrar o las reglas de color.
2. En la cinta de opciones de la barra de herramientas, seleccione **Vista Diseño**y luego seleccione **Todas las columnas de cliente de .NET** en la sección de **Almacenamiento de Azure** . Este diseño vista muestra datos desde el cliente de registro, así como los registros de seguimiento de servidor y de red. De forma predeterminada está clasificado en la columna **MessageNumber** .
3. A continuación, busque en el registro de cliente para el ID de solicitud de cliente. En la cinta de opciones de la barra de herramientas, seleccione **Buscar mensajes**y luego especifique un filtro personalizado en el ID de cliente en el campo **Buscar** . Utilice la siguiente sintaxis para el filtro, especifique su propio ID de solicitud de cliente:

        *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"

Analizador de mensaje localiza y selecciona la primera entrada de registro en los criterios de búsqueda coincide con el ID de cliente En el registro de cliente, hay varias entradas para cada ID de solicitud de cliente, por lo que desee agruparlos en el campo **ClientRequestId** para que sea más fácil verlos todo. La imagen siguiente muestra todos los mensajes en el registro de cliente para el cliente especificado Id.

![Errores de 404 que muestra de registro de cliente](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Usando los datos que se muestra en los diseños de vista en estos dos pestañas, puede analizar los datos de solicitud para determinar lo que puede haber provocado daños el error. También puede mirar las solicitudes que precedidos ésta para ver si el error 404 puede haber provocado un evento anterior. Por ejemplo, puede revisar las entradas del registro de cliente antes de este ID de solicitud de cliente para determinar si se han eliminado el blob, o si el error es debido a la aplicación cliente de llamar a una API CreateIfNotExists en un contenedor o blob. En el registro de cliente, puede encontrar la dirección del blob en el campo **Descripción** . en el servidor y los registros de seguimiento de red, esta información aparece en el campo **Resumen** .

Una vez que conozca la dirección del blob que se produjo el error 404, puede investigar más. Si busca las entradas de registro para otros mensajes relacionados con las operaciones en el mismo blob, puede comprobar si el cliente previamente eliminado la entidad.

## <a name="analyze-other-types-of-storage-errors"></a>Analizar otros tipos de errores de almacenamiento

Ahora que ya está familiarizado con el analizador de mensaje para analizar los datos de registro, puede analizar otros tipos de errores con la vista de diseños, reglas de color y búsqueda de filtrado. Las tablas siguientes enumeran algunos problemas que pueden surgir y los criterios de filtro que se puede usar para buscarlas. Para obtener más información sobre la creación de filtros y el analizador de mensaje filtrado de idioma, vea [Filtrar datos de mensaje](http://technet.microsoft.com/library/jj819365.aspx).

|    Para investigar...                                                                                               |    Use la expresión de filtro...                                                                                                                                                                                                                                        |    Expresión que se aplica al registro (cliente, servidor, red, todos)    |
|------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
|    Retrasos inesperados en la entrega de mensajes de una cola                                                              |    AzureStorageClientDotNetV4.Description contiene "Retrying error operación".                                                                                                                                                                                |    Cliente                                                        |
|    Aumento de HTTP de PercentThrottlingError                                                                       |    HTTP. Response.StatusCode == 500 & #124; & #124; HTTP. Response.StatusCode == 503                                                                                                                                                                                          |    Red                                                       |
|    Aumentar en PercentTimeoutError                                                                               |    HTTP. Response.StatusCode == 500                                                                                                                                                                                                                             |    Red                                                       |
|    Aumentar en PercentTimeoutError (todo)                                                                         |    * StatusCode == 500                                                                                                                                                                                                                                          |    Todos los                                                           |
|    Aumentar en PercentNetworkError                                                                               |    AzureStorageClientDotNetV4.EventLogEntry.Level < 2                                                                                                                                                                                                          |    Cliente                                                        |
|    Mensajes HTTP 403 (prohibido)                                                                                 |    HTTP. Response.StatusCode == 403                                                                                                                                                                                                                             |    Red                                                       |
|    HTTP 404 (no encontrado) mensajes                                                                                 |    HTTP. Response.StatusCode == 404                                                                                                                                                                                                                             |    Red                                                       |
|    404 (todas)                                                                                                     |    * StatusCode == 404                                                                                                                                                                                                                                          |    Todos los                                                           |
|    Compartir el problema de autorización de firma de Access (SA)                                                             |    AzureStorageLog.RequestStatus == "SASAuthorizationError"                                                                                                                                                                                                     |    Red                                                       |
|    HTTP 409 (conflicto) mensajes                                                                                  |    HTTP. Response.StatusCode == 409                                                                                                                                                                                                                             |    Red                                                       |
|    409 (todas)                                                                                                     |    * StatusCode == 409                                                                                                                                                                                                                                          |    Todos los                                                           |
|    Entradas del registro bajo PercentSuccess o análisis tienen operaciones con el estado de la transacción de ClientOtherErrors    |    AzureStorageLog.RequestStatus == "ClientOtherError"                                                                                                                                                                                                         |    Servidor                                                        |
|    Advertencia de Nagle                                                                                               |    ((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1,5)) y (AzureStorageLog.RequestPacketSize < 1460) y (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200)        |    Servidor                                                        |
|    Intervalo de tiempo en los registros de servidor y de red                                                                    |    #Marca de tiempo > = 2014-10-20T16:36:38 y #Timestamp < = 2014-10-20T16:36:39                                                                                                                                                                                     |    Servidor de red                                               |
|    Intervalo de tiempo en los registros del servidor                                                                                |    AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 y AzureStorageLog.Timestamp < = 2014-10-20T16:36:39                                                                                                                                                     |    Servidor                                                        |


## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de los escenarios de llevar a cabo para solucionar problemas en el almacenamiento de Azure, consulte estos recursos:

- [Supervisar, diagnosticar y solucionar problemas de Microsoft Azure almacenamiento](storage-monitoring-diagnosing-troubleshooting.md)
- [Análisis de almacenamiento](http://msdn.microsoft.com/library/azure/hh343270.aspx)
- [Monitor de una cuenta de almacenamiento en el Portal de Azure](storage-monitor-storage-account.md)
- [Transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md)
- [Guía de funcionamiento de Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx)
