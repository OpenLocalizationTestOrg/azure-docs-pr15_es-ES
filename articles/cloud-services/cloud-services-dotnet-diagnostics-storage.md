<properties
    pageTitle="Almacenar y ver datos de diagnóstico en el almacenamiento de Azure | Microsoft Azure"
    description="Obtener datos de diagnóstico de Azure en el almacenamiento de Azure y visualizarlo"
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="cloud-services"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="robb" />

# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Almacenar y ver datos de diagnóstico en el almacenamiento de Azure

Datos de diagnóstico no se almacenan permanentemente a menos que se transfiere al emulador de Microsoft Azure almacenamiento o para el almacenamiento de Azure. Una vez en el almacenamiento, puede verse con una de las diversas herramientas disponibles.

## <a name="specify-a-storage-account"></a>Especificar una cuenta de almacenamiento

Especifique la cuenta de almacenamiento que desee usar en el archivo ServiceConfiguration.cscfg. La información de cuenta se define como una cadena de conexión en un valor de configuración. En el ejemplo siguiente se muestra la cadena de conexión predeterminado creada para un nuevo proyecto de servicio de nube en Visual Studio:


```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Puede cambiar esta cadena de conexión para proporcionar información de cuenta para una cuenta de almacenamiento de Azure.

Según el tipo de datos de diagnóstico que se recopila, diagnósticos de Azure usa el servicio de blobs de Windows o el servicio de la tabla. La siguiente tabla muestra los orígenes de datos que se conservan y su formato.

|Origen de datos|Formato de almacenamiento|
|---|---|
|Registros de Azure|Tabla|
|Registros de IIS 7.0|BLOB|
|Registros de infraestructura de diagnósticos de Azure|Tabla|
|Error en los registros de seguimiento de solicitud|BLOB|
|Registros de eventos de Windows|Tabla|
|Contadores de rendimiento|Tabla|
|Volcados|BLOB|
|Registros de errores personalizado|BLOB|

## <a name="transfer-diagnostic-data"></a>Transferir datos de diagnóstico

SDK 2,5 y versiones posteriores, puede producirse la solicitud de transferencia de datos de diagnóstico a través del archivo de configuración. Puede transferir datos de diagnóstico en intervalos programados como se especifica en la configuración.

Para SDK 2,4 anterior y puede solicitar para transferir los datos de diagnóstico a través del archivo de configuración, así como mediante programación. El método mediante programación permite realizar a las transferencias a petición.


>[AZURE.IMPORTANT] Cuando la transferencia de datos de diagnóstico a una cuenta de almacenamiento de Azure, provoca costos para los recursos de almacenamiento que usan sus datos de diagnósticos.

## <a name="store-diagnostic-data"></a>Almacene los datos de diagnóstico

Datos de registro se almacenan en el almacenamiento de blobs o tabla con los siguientes nombres:

**Tablas**

- **WadLogsTable** - registros escritos en código mediante la escucha de seguimiento.

- **WADDiagnosticInfrastructureLogsTable** - monitor de diagnóstico y configuración cambia.

- **WADDirectoriesTable** – directorios que supervisa el monitor de diagnóstico.  Esto incluye los registros de IIS, IIS no pudo registros de la solicitud y directorios personalizados.  La ubicación del archivo de registro de blob especificada en el campo contenedor y el nombre del objeto binario se encuentra en el campo RelativePath.  El campo AbsolutePath indica la ubicación y el nombre del archivo tal y como era en la máquina virtual de Azure.

- **WADPerformanceCountersTable** : contadores de rendimiento.

- **WADWindowsEventLogsTable** : los registros de eventos de Windows.

**BLOB**

- **contenedor de control de torunda** -(solo para SDK 2,4 y anteriores) contiene los archivos de configuración de XML que controla el diagnóstico de Azure.

- **torunda-iis-failedreqlogfiles** – contiene información de error solicitar IIS registros.

- **archivos de registro torunda-iis** : contiene información acerca de IIS registros.

- **"personalizada"** : un contenedor personalizado basado en la configuración de directorios supervisados por el monitor de diagnóstico.  El nombre de este contenedor blob se especificará en WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Herramientas para ver los datos de diagnóstico
Varias herramientas están disponibles para ver los datos cuando se transfiere al almacenamiento. Por ejemplo:

- Explorador de servidores de Visual Studio - si ha instalado las herramientas de Azure para Microsoft Visual Studio, puede usar el nodo de almacenamiento de Azure en el Explorador de servidores para ver blobs de sólo lectura y datos de la tabla de sus cuentas de almacenamiento de Azure. Puede mostrar los datos de su cuenta de emulador almacenamiento local y también de cuentas de almacenamiento que ha creado para Azure. Para obtener más información, consulte [exploración y administración de recursos de almacenamiento con el Explorador de servidores](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).

- [Explorador de Microsoft Azure almacenamiento](../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente que le permite trabajar fácilmente con los datos de almacenamiento de Azure en Windows, OSX y Linux.

- [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) incluye Administrador de diagnósticos de Azure que le permite ver, descargar y administrar los datos de diagnósticos recopilados por las aplicaciones que se ejecuta en Azure.


## <a name="next-steps"></a>Pasos siguientes

[Seguimiento del flujo de una aplicación de servicios de nube con diagnósticos de Azure](cloud-services-dotnet-diagnostics-trace-flow.md)
