<properties
    pageTitle="Secuencia de comandos de desarrollo de acción con HDInsight | Microsoft Azure"
    description="Obtenga información sobre cómo personalizar clústeres Hadoop con la acción de secuencia de comandos. Acción de secuencia de comandos puede utilizarse para instalar software adicional que se ejecuta en un clúster de Hadoop o cambiar la configuración de las aplicaciones instaladas en un clúster."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Desarrollar secuencias de comandos de acción de secuencia de comandos para clústeres basados en HDInsight de Windows

Obtenga información sobre cómo escribir secuencias de comandos de acción de secuencia de comandos para HDInsight. Para obtener información sobre el uso de secuencias de comandos de acción de secuencia de comandos, consulte [clústeres de personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster.md). Para el mismo artículo escrito para clústeres basados en Linux HDInsight, vea [secuencias de comandos de acción de secuencia de comandos de desarrollar para HDInsight](hdinsight-hadoop-script-actions-linux.md).

> [AZURE.NOTE] La información de este documento es específica para clústeres HDInsight basado en Windows. Para obtener información sobre cómo utilizar las acciones de script con clústeres basados en Windows, vea [desarrollo de acción de secuencia de comandos con HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).


Acción de secuencia de comandos puede utilizarse para instalar software adicional que se ejecuta en un clúster de Hadoop o cambiar la configuración de las aplicaciones instaladas en un clúster. Acciones de script son secuencias de comandos que se ejecutan en los nodos del clúster cuando se implementan clústeres HDInsight y se ejecutan una vez nodos en el clúster completar la configuración de HDInsight. Una acción de secuencia de comandos se ejecuta con privilegios de cuenta de administrador de sistema y proporciona derechos de acceso completo a los nodos de clúster. Cada clúster puede proporcionarse con una lista de acciones de secuencia de comandos que se ejecutan en el orden en que se especifican.

> [AZURE.NOTE] Si experimenta el siguiente mensaje de error:
>
>     System.Management.Automation.CommandNotFoundException; ExceptionMessage : The term 'Save-HDIFile' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
> Es porque no incluye los métodos auxiliares.  Consulte [métodos de aplicación auxiliar de scripts personalizados](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).

## <a name="sample-scripts"></a>Secuencias de comandos de ejemplo

Para crear clústeres de HDInsight en el sistema operativo Windows, la acción de secuencia de comandos es script de PowerShell de Azure. El siguiente es un ejemplo de secuencia de comandos para configurar los archivos de configuración del sitio:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

La secuencia de comandos toma cuatro parámetros, el nombre de archivo de configuración, la propiedad que desea modificar, el valor que desea establecer, y una descripción. Por ejemplo:

    hive-site.xml hive.metastore.client.socket.timeout 90

Estos parámetros establecerán el valor de hive.metastore.client.socket.timeout 90 en el archivo de subárbol site.xml.  El valor predeterminado es 60 segundos.

Esta secuencia de comandos también se encuentra en [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight proporciona varias secuencias de comandos para instalar componentes adicionales en clústeres HDInsight:

Nombre | Secuencia de comandos
----- | -----
**Instalar motor** | https://hdiconfigactions.BLOB.Core.Windows.NET/sparkconfigactionv03/Spark-Installer-v03.ps1. Vea [instalar y usar despierten en clústeres HDInsight][hdinsight-install-spark].
**Instalar R** | https://hdiconfigactions.BLOB.Core.Windows.NET/rconfigactionv02/r-Installer-v02.ps1. Consulte [instalar y usar R en clústeres HDInsight][hdinsight-r-scripts].
**Instalar Solr** | https://hdiconfigactions.BLOB.Core.Windows.NET/solrconfigactionv01/solr-Installer-V01.ps1. Vea [instalar y usar clústeres Solr en HDInsight](hdinsight-hadoop-solr-install.md).
- **Instalar Giraph** | https://hdiconfigactions.BLOB.Core.Windows.NET/giraphconfigactionv01/giraph-Installer-V01.ps1. Vea [instalar y usar clústeres Giraph en HDInsight](hdinsight-hadoop-giraph-install.md).

Acción de secuencia de comandos se puede implementar desde el portal de Azure, Azure PowerShell o mediante el SDK de .NET HDInsight.  Para obtener más información, consulte [clústeres de personalizar HDInsight con la acción de secuencia de comandos][hdinsight-cluster-customize].

> [AZURE.NOTE] Las secuencias de comandos de ejemplo funcionan sólo con la versión de clúster HDInsight 3.1 o superior. Para obtener más información sobre versiones de clúster HDInsight, consulte [versiones de clúster HDInsight](hdinsight-component-versioning.md).





## <a name="helper-methods-for-custom-scripts"></a>Métodos de aplicación auxiliar de scripts personalizados

Métodos de secuencia de comandos acción auxiliares son utilidades que puede usar al escribir scripts personalizados. Estos se definen en [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)y se pueden incluir en las secuencias de comandos con los siguientes:

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Estos son los métodos auxiliares proporcionados por esta secuencia de comandos:

Método auxiliar | Descripción
-------------- | -----------
**Guardar HDIFile** | Descargar un archivo desde el identificador uniforme de recursos (URI) especificado a una ubicación en el disco local que está asociado con el nodo de Azure VM asignado al clúster.
**HDIZippedFile expandir** | Descomprima un archivo comprimido.
**HDICmdScript invocar** | Ejecutar una secuencia de comandos de cmd.exe.
**HDILog de escritura** | Escribir los resultados de la secuencia de comandos personalizado utilizado para una acción de secuencia de comandos.
**Obtener servicios** | Obtener una lista de servicios que se ejecutan en el equipo donde se ejecuta el script.
**Get-Service** | Con el nombre de servicio específico como entrada, obtener información detallada de un servicio específico (nombre del servicio, procesar ID, estado, etc.) en el equipo donde se ejecuta el script.
**Get-HDIServices** | Obtener una lista de servicios de HDInsight que se ejecuta en el equipo donde se ejecuta el script.
**Get-HDIService** | Con el nombre de servicio HDInsight específico como entrada, obtener información detallada de un servicio específico (nombre del servicio, procesar ID, estado, etc.) en el equipo donde se ejecuta el script.
**Get-ServicesRunning** | Obtener una lista de servicios que se ejecutan en el equipo donde se ejecuta el script.
**Get-ServiceRunning** | Compruebe si se está ejecutando un servicio específico (por nombre) en el equipo donde se ejecuta el script.
**Get-HDIServicesRunning** | Obtener una lista de servicios de HDInsight que se ejecuta en el equipo donde se ejecuta el script.
**Get-HDIServiceRunning** | Compruebe si se está ejecutando un servicio de HDInsight específico (por nombre) en el equipo donde se ejecuta el script.
**Get-HDIHadoopVersion** | Obtener la versión de Hadoop instalada en el equipo donde se ejecuta el script.
**IsHDIHeadNode de prueba** | Comprobar si el equipo donde se ejecuta el script es un nodo principal.
**IsActiveHDIHeadNode de prueba** | Comprobar si el equipo donde se ejecuta el script es un nodo de cabeza activo.
**IsHDIDataNode de prueba** | Comprobar si el equipo donde se ejecuta el script es un nodo de datos.
**Editar HDIConfigFile** | Modificar configuración de archivos subárbol site.xml, core site.xml, site.xml hdfs, mapred site.xml o hilo site.xml.


## <a name="best-practices-for-script-development"></a>Prácticas recomendadas para el desarrollo de secuencias de comandos

Al desarrollar una secuencia de comandos personalizada para un clúster de HDInsight, hay varias prácticas recomendadas para tener en cuenta:

- Comprobar la versión de Hadoop

    Solo HDInsight versión 3.1 (2,4 Hadoop) y versiones posteriores admite con la acción de secuencia de comandos para instalar componentes personalizados en un clúster. En el script personalizado, debe utilizar el método de aplicación auxiliar de **Get-HDIHadoopVersion** para comprobar la versión de Hadoop antes de continuar con la realización de otras tareas en la secuencia de comandos.


- Proporcionar estable vínculos a recursos de script

    Los usuarios deben asegurarse de que todas las secuencias de comandos y otros defectos utilizados en la personalización de un clúster siguen estando disponibles en toda la duración del clúster y que las versiones de estos archivos no cambian para la duración. Estos recursos son obligatorios si crear una nueva imagen de nodos en el clúster es necesaria. Lo mejor es descargar y archivar todo el contenido de una cuenta de almacenamiento que el usuario controla. Esto puede ser la cuenta de almacenamiento predeterminada o alguna de las cuentas de almacenamiento adicionales especificadas en el momento de implementación para un clúster personalizado.
    En el motor y R personalizado muestras de clúster siempre en la documentación, por ejemplo, hemos una copia local de los recursos en esta cuenta de almacenamiento: https://hdiconfigactions.blob.core.windows.net/.


- Asegúrese de que la secuencia de comandos de personalización de clúster es idempotente

    Debe esperar a que los nodos de un clúster de HDInsight se renuevan durante la duración de clúster. La secuencia de comandos de personalización de clúster se ejecuta siempre que un clúster se renuevan. Esta secuencia de comandos debe diseñarse sean idempotentes en el sentido que al volver a imágenes, la secuencia de comandos debe asegurarse de que el clúster se devuelve el mismo Personalizar estado que estaba después de la secuencia de comandos ejecutó por primera vez cuando se ha creado el clúster. Por ejemplo, si un script personalizado instalado una aplicación en D:\AppLocation en su primera ejecución y luego en cada ejecución de las siguientes, al volver a imágenes, la secuencia de comandos debe comprobar si existe la aplicación en la ubicación de D:\AppLocation antes de continuar con otros pasos de la secuencia de comandos.


- Instalar componentes personalizados en la ubicación óptima

    Cuando se renuevan nodos de clúster, la unidad de recursos C:\ y la unidad de sistema D:\ pueden ser volverá a dar formato, lo que es el resultado de la pérdida de datos y las aplicaciones que se ha instalado en las unidades. Esto también podría ocurrir si un nodo de Azure máquina virtual () que forma parte del clúster deja de funcionar y se reemplaza por un nodo nuevo. Puede instalar componentes en la unidad D:\ o en la ubicación de C:\apps en el clúster. Todas las otras ubicaciones en la unidad C:\ están reservadas. Especifique la ubicación donde se instalará en el clúster de secuencia de comandos de personalización aplicaciones o bibliotecas.


- Alta disponibilidad de la arquitectura de clúster

    Hdinsight cuenta con una arquitectura activo pasivo de alta disponibilidad, en los que uno nodo principal está en modo activo (donde se están ejecutando los servicios de HDInsight) y el nodo de cabeza está en modo de espera (en qué HDInsight no se están ejecutando). Los nodos de cambiar los modos activos y pasivos si se interrupción HDInsight servicios. Si se utiliza una acción de secuencia de comandos para instalar servicios en ambos nodos cabezas de alta disponibilidad, tenga en cuenta que el mecanismo de migración tras error HDInsight no podrá conmutar automáticamente estos servicios instalados por el usuario. Servicios instalados por el usuario por tanto en los nodos de cabeza HDInsight que deben estar altamente disponibles deben tener su propio mecanismo de migración tras error en el modo activo pasivo o esté en modo activo / activo.

    Un comando de acción de secuencia de comandos de HDInsight se ejecuta en ambos nodos cabezales cuando el rol de nodo principal se especifica como un valor en el parámetro *ClusterRoleCollection* . Por tanto cuando diseñe un script personalizado, asegúrese de que la secuencia de comandos tiene constancia de esta configuración. No debe ejecutar problemas donde los mismos servicios instalados e iniciados en ambos nodos del cabezales y terminarán competir entre sí. Además, tenga en cuenta que los datos se perderán durante volver a imágenes, por lo que el software instalado mediante una acción de secuencia de comandos tiene que ser resistentes a los eventos. Aplicaciones deben diseñarse para trabajar con datos altamente disponibles que se distribuyan entre muchos nodos. Tenga en cuenta que como 1/5 de los nodos en un clúster puede volver a crear la imagen a la vez.


- Configurar los componentes personalizados para utilizar el almacenamiento de blobs de Windows Azure

    Los componentes personalizados que se instalan en los nodos del clúster podrían tener una configuración predeterminada para usar el almacenamiento de sistema de archivos distribuido Hadoop (HDFS). Debe cambiar la configuración para usar el almacenamiento de blobs de Windows Azure en su lugar. En una clúster crear una nueva imagen, el sistema de archivos HDFS obtiene con formato y se pierden todos los datos que se almacenan allí. En su lugar mediante el almacenamiento de blobs de Windows Azure garantiza que se conservarán los datos.

## <a name="common-usage-patterns"></a>Patrones de uso común

Esta sección proporciona instrucciones sobre cómo implementar algunos de los patrones de uso común que pueden surgir al escribir su propio script personalizado.

### <a name="configure-environment-variables"></a>Configurar variables de entorno

A menudo en el desarrollo de la acción de secuencia de comandos, se sentirá la necesidad de establecer variables de entorno. Por ejemplo, un escenario más probable es cuando descargue un archivo binario desde un sitio externo, instalarlo en el clúster y agregue la ubicación de donde está instalado para la variable de entorno 'PATH'. El fragmento de código siguiente muestra cómo establecer variables de entorno en el script personalizado.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Esta declaración establece la variable de entorno **MDS_RUNNER_CUSTOM_CLUSTER** en el valor 'true' y también establece el ámbito de esta variable para que sea todo el equipo. A veces es importante que se establecen las variables de entorno en el ámbito adecuado: equipo o usuario. Consulte [aquí] [ 1] para obtener más información sobre cómo configurar las variables de entorno.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acceso a ubicaciones donde se almacenan los scripts personalizados

Las secuencias de comandos que se usa para personalizar un clúster deben ser: en la cuenta de almacenamiento predeterminada para el clúster o en un contenedor de sólo lectura público en cualquier otra cuenta de almacenamiento. Si la secuencia de comandos tiene acceso a los recursos que se encuentra en otro punto estos necesitan estar en accesible públicamente (al menos pública de solo lectura). Por ejemplo desea tener acceso a un archivo y guárdelo con el comando HDI SaveFile.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

En este ejemplo, debe asegurarse de que el contenedor 'somecontainer' en la cuenta de almacenamiento 'somestorageaccount' es accesible públicamente. En caso contrario, la secuencia de comandos se inicia una excepción 'No se encontró' y no se realice correctamente.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Especificar los parámetros del cmdlet agregar AzureRmHDInsightScriptAction

Para pasar varios parámetros al cmdlet Add-AzureRmHDInsightScriptAction, debe dar formato al valor de cadena para que contenga todos los parámetros de la secuencia de comandos. Por ejemplo:

    "-CertifcateUri wasbs:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

o

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Excepción para la implementación de clúster con errores

Si desea obtener notificaciones con precisión del hecho de que clúster de personalización no se realizó correctamente según lo esperado, es importante iniciar una excepción y no se realice correctamente la creación de clúster. Por ejemplo, desea procesar un archivo si existe y controlar el caso de error en el archivo no existe. ¿Asegúrese de que el script finalice sin problemas y el estado del clúster correctamente se conoce. El fragmento de código siguiente proporciona un ejemplo de cómo conseguirlo:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

En este fragmento de código, si el archivo no existe, puede llevar a un estado donde la secuencia de comandos realmente sale correctamente después de imprimir el mensaje de error y el clúster alcanza suponiendo que "correctamente" complete el proceso de personalización de clúster de estado de ejecución. Si desea notificar con precisión el hecho de que clúster personalización esencialmente no se realizó correctamente según lo esperado debido a un archivo que falta, es más apropiado iniciar una excepción y no se realice correctamente el paso de la personalización de clúster. Para ello debe utilizar el siguiente fragmento de código de ejemplo en su lugar.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Lista de comprobación para implementar una acción de secuencia de comandos
Estos son los pasos que realizó al prepararse para implementar estas secuencias de comandos:

1. Coloque los archivos que contienen los scripts personalizados en un lugar accesible por los nodos de clúster durante la implementación. Esto puede representar cualquiera de los predeterminados o cuentas adicionales de almacenamiento especificadas en el momento de la implementación de clúster o cualquier otro contenedor de almacenamiento accesible públicamente.
2. Agregar controles a secuencias de comandos para asegurarse de que se ejecute idempotently, para que la secuencia de comandos se puede ejecutar varias veces en el mismo nodo.
3. Use el cmdlet de PowerShell de Azure de **Salida de escritura** para imprimir a STDOUT como STDERR. No use **Host de escritura**.
4. Usar una carpeta de archivos temporales, como $env: TEMP para mantener el archivo descargado usado por las secuencias de comandos y, a continuación, limpiarlos después de que han ejecutado secuencias de comandos.
5. Instalar el software personalizado solamente en D:\ o C:\apps. Otras ubicaciones en la unidad C: no se recomienda como estén reservadas. Tenga en cuenta que instalar archivos en la unidad C: fuera de la carpeta C:\apps puede producir errores de instalación en durante volver a las imágenes del nodo.
6. En caso de que se han cambiado la configuración de nivel de sistema operativo o archivos de configuración del servicio de Hadoop, desea reiniciar servicios HDInsight para que puede seleccionar cualquier configuración de nivel de sistema operativo, como las variables de entorno establecidas en las secuencias de comandos.

## <a name="debug-custom-scripts"></a>Depurar secuencias de comandos personalizadas

Se almacenan los registros de errores de script, junto con otros resultados, en la cuenta de almacenamiento predeterminada especificada para el clúster en su creación. Los registros se almacenan en una tabla con el nombre *u < \cluster-name-fragment >< \time-stamp > setuplog*. Estos son los registros de agregado que tengan registros de todos los nodos (nodo principal y los nodos de trabajo) en el que se ejecuta la secuencia de comandos en el clúster.
Compruebe los registros de manera más sencilla es usar herramientas de HDInsight para Visual Studio. Para instalar las herramientas, vea [Introducción al uso de Hadoop de Visual Studio tools para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#install-hdinsight-tools-for-visual-studio)

**Para comprobar el registro mediante Visual Studio**

1. Abra Visual Studio.
2. Haga clic en **vista**y, a continuación, haga clic en **Explorador de servidores**.
3. Haga clic con el botón "Azure", haga clic en conectar con **Microsoft Azure suscripciones**y, a continuación, escriba sus credenciales.
4. Expanda **almacenamiento**, expanda la cuenta de almacenamiento de Azure usada como el sistema de archivos de forma predeterminada, expanda **tablas**y, a continuación, haga doble clic en el nombre de tabla.


Puede también remoto en los nodos del clúster para ver la ambos STDOUT y STDERR de scripts personalizados. Los registros en cada nodo son específicos de ese nodo y se ha conectado a **C:\HDInsightLogs\DeploymentAgent.log**. Estos archivos registran todos los resultados de la secuencia de comandos personalizada. Un fragmento de código de registro de ejemplo para una acción de secuencia de comandos de motor es similar a esta:

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


En este registro, es obvio que ha ejecutado la acción de secuencia de comandos de motor en la máquina virtual denominada HEADNODE0 y que no se inician excepciones durante la ejecución.

En caso de que se produce un error de ejecución, también se incluirán el resultado que se describe en este archivo de registro. La información de estos registros debe ser útiles para depurar problemas de secuencia de comandos que pueden surgir.


## <a name="see-also"></a>Vea también

- [Personalizar clústeres HDInsight con la acción de secuencia de comandos][hdinsight-cluster-customize]
- [Instalar y usar el motor en clústeres HDInsight][hdinsight-install-spark]
- [Instalar y usar R en clústeres HDInsight][hdinsight-r-scripts]
- [Instalar y usar clústeres Solr en HDInsight](hdinsight-hadoop-solr-install.md).
- [Instalar y usar clústeres Giraph en HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-r-scripts]: hdinsight-hadoop-r-scripts.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
