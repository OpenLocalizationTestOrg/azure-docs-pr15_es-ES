<properties
    pageTitle="Solución de problemas de diagnóstico de Azure"
    description="Solucionar problemas al usar diagnóstico de Azure en servicios de nube de Azure, máquinas virtuales y "
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="robb"/>


# <a name="azure-diagnostics-troubleshooting"></a>Solución de problemas de diagnóstico de Azure
Solución de problemas de la información relevante para el uso de diagnósticos de Azure. Para obtener más información de diagnóstico de Azure, vea [Información general de diagnósticos de Azure](azure-diagnostics.md#cloud-services).

## <a name="azure-diagnostics-is-not-starting"></a>No se inicia el diagnóstico de Azure

Diagnósticos consta de dos componentes: un complemento de agente de invitado y el agente de supervisión. Puede comprobar los archivos de registro **DiagnosticsPluginLauncher.log** y **DiagnosticsPlugin.log** para obtener información sobre por qué diagnósticos no se puede iniciar.  
  
En una función de servicio de nube, archivos de registro para el complemento de agente de invitado se encuentran en: 

``` 
C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.6.3.0\ 
``` 

En una máquina Virtual de Azure, archivos de registro para el complemento de agente de invitado se encuentran en: 

``` 
C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.6.3.0\Logs\ 
``` 
 
La última línea de los archivos de registro contendrá el código de salida.  

``` 
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0 
``` 

El complemento devuelve los siguientes códigos de salida:

Código de salida|Descripción
---|---
0|Correcto.
-1|Error genérico.
-2|No se puede cargar el archivo rcf.<p>Se trata de un error interno que solo debe ocurrir si incorrectamente, el selector de complemento de agente de invitado manualmente se invoca en la máquina virtual.
-3|No se puede cargar el archivo de configuración de diagnósticos.<p><p>Solución: Esto es el resultado de un archivo de configuración no supere la validación de esquema. La solución consiste en proporcionar un archivo de configuración que cumpla con el esquema.
-4|Otra instancia del agente de supervisión diagnósticos ya está usando el directorio de recursos local.<p><p>Solución: Especifique un valor diferente para **LocalResourceDirectory**.
-6|El selector de complemento de agente de invitado intentó iniciar diagnósticos con una línea de comandos no válido.<p><p>Se trata de un error interno que solo debe ocurrir si incorrectamente, el selector de complemento de agente de invitado manualmente se invoca en la máquina virtual.
-10|El complemento de diagnósticos terminó con una excepción no controlada.
-11|El agente de invitado no pudo crear el proceso responsable de iniciar y supervisar al agente de supervisión.<p><p>Solución: Compruebe que suficientes recursos del sistema están disponibles para iniciar nuevos procesos.<p>
-101|Argumentos no válidos al llamar al complemento de diagnóstico.<p><p>Se trata de un error interno que solo debe ocurrir si incorrectamente, el selector de complemento de agente de invitado manualmente se invoca en la máquina virtual.
-102|El proceso de complemento no logra inicializarse.<p><p>Solución: Compruebe que suficientes recursos del sistema están disponibles para iniciar nuevos procesos.
-103|El proceso de complemento no logra inicializarse. Específicamente es no se puede crear el objeto de registrador.<p><p>Solución: Compruebe que suficientes recursos del sistema están disponibles para iniciar nuevos procesos.
-104|No se puede cargar el archivo rcf proporcionado por el agente de invitado.<p><p>Se trata de un error interno que solo debe ocurrir si incorrectamente, el selector de complemento de agente de invitado manualmente se invoca en la máquina virtual.
-105|El complemento de diagnósticos no puede abrir el archivo de configuración de diagnósticos.<p><p>Se trata de un error interno que solo debe ocurrir si incorrectamente, el complemento de diagnóstico manualmente se invoca en la máquina virtual.
-106|No puede leer el archivo de configuración de diagnósticos.<p><p>Solución: Esto es el resultado de un archivo de configuración no supere la validación de esquema. La solución es proporcionar un archivo de configuración que cumpla con el esquema. Puede encontrar el XML que se ha entregado a la extensión de diagnóstico en la carpeta *%SystemDrive%\WindowsAzure\Config* en la máquina virtual. Abra el archivo XML y búsqueda para **Microsoft.Azure.Diagnostics**, a continuación, en el campo **xmlCfg** adecuada. Los datos son base 64 codificado por lo que tendrá que [descodificar él](http://www.bing.com/search?q=base64+decoder) ver el XML que se cargó por diagnósticos.<p>
-107|No es válida para el agente de supervisión de la comprobación de directorio de recursos.<p><p>Se trata de un error interno que solo debe ocurrir si incorrectamente, el agente de supervisión manualmente se invoca en la máquina virtual.</p>
-108    |No se puede convertir el archivo de configuración de diagnósticos en el archivo de configuración de agente de supervisión.<p><p>Se trata de un error interno que solo debe ocurrir si el complemento de diagnósticos se invoca manualmente con un archivo de configuración no válidos.
-110|Error de configuración de diagnósticos de general.<p><p>Se trata de un error interno que solo debe ocurrir si el complemento de diagnósticos se invoca manualmente con un archivo de configuración no válidos.
-111|No se puede iniciar al agente de supervisión.<p><p>Solución: Compruebe que están disponibles suficientes recursos del sistema.
-112|Error general


## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Datos de diagnósticos están que no ha iniciado sesión con el almacenamiento de Azure
Diagnóstico de Azure almacena todos los datos en el almacenamiento de Azure.

La causa más común de perder los datos del evento es la información de cuenta de almacenamiento definido incorrectamente.

Solución: Corregir el archivo de configuración de diagnósticos y volver a instalar diagnósticos.
Si el problema persiste después de volver a instalar la extensión de diagnósticos, a continuación, puede que tenga que depurar más mirando a través de los errores de agente cualquier supervisión. Antes de carguen los datos de eventos a su cuenta de almacenamiento de información se almacena en la LocalResourceDirectory.

Rol de servicio de nube es la LocalResourceDirectory:

    C:\Resources\Directory\<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Máquinas virtuales la LocalResourceDirectory es:

    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

Si no hay ningún archivo en la carpeta LocalResourceDirectory, es no se puede iniciar el agente de supervisión. Esto suele estar causado por un archivo de configuración no válida, un evento que debería aparecer en la CommandExecution.log.

Si el agente de supervisión correctamente es recopilar datos de eventos, verá los archivos .tsf para cada evento definido en el archivo de configuración. El agente de supervisión registra sus errores en el archivo MaEventTable.tsf. Para examinar el contenido de este archivo puede utilizar la aplicación tabel2csv para convertir el archivo .tsf en un archivo de values(.csv) separados por comas:

En una función de servicio de nube:

    %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*UnidadDeSistema* en una función de servicio de nube suele ser D:

En una máquina Virtual:

    C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

Los comandos anteriores genera de archivo de registro *maeventtable.csv*, que puede abrir y busque mensajes de error.    


## <a name="diagnostics-data-tables-not-found"></a>Datos de diagnósticos tablas no encontrados
Las tablas de almacenamiento de Azure almacenamiento de datos de diagnóstico de Azure se denominan con el siguiente código:

        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;

Aquí tenemos un ejemplo:

        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>

Que generará 4 tablas:

Evento|Nombre de tabla
---|---
proveedor = "prov1" &lt;Id. de evento = "1" /&gt;|WADEvent + MD5("prov1") + "1"
proveedor = "prov1" &lt;Id. de evento = eventDestination "2" = "dest1" /&gt;|WADdest1
proveedor = "prov1" &lt;DefaultEvents /&gt;|WADDefault+MD5("prov1")
proveedor = "prov2" &lt;DefaultEvents eventDestination = "dest2" /&gt;|WADdest2
