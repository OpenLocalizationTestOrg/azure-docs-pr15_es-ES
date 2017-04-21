<properties
   pageTitle="Usar Elasticsearch como un almacén de seguimiento de aplicación de servicio tela | Microsoft Azure"
   description="Describe cómo pueden usar aplicaciones de servicio tela Elasticsearch y Kibana para almacenar, índice y búsquedas rápidas seguimientos de aplicación (registros)"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/09/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="use-elasticsearch-as-a-service-fabric-application-trace-store"></a>Usar Elasticsearch como un seguimiento de la aplicación de servicio tela almacenar
## <a name="introduction"></a>Introducción
Este artículo describe cómo pueden utilizar aplicaciones de [Azure servicio tela](https://azure.microsoft.com/documentation/services/service-fabric/) **Elasticsearch** y **Kibana** para el almacenamiento de seguimiento de aplicación, indización y búsqueda. [Elasticsearch](https://www.elastic.co/guide/index.html) es un código abierto, distribuido y scalable en tiempo real búsqueda y análisis motor es ideal para esta tarea. Se puede instalar en Windows y Linux máquinas virtuales ejecutando en Microsoft Azure. Elasticsearch puede procesar de forma eficaz *estructuradas* trazas creados mediante tecnologías como el **Seguimiento de eventos de Windows (ETW)**.

ETW se usa en tiempo de ejecución de servicio tela a información de diagnóstico de origen (trazas). Es el método recomendado para las aplicaciones de servicio tela a su información de diagnóstico de origen demasiado. Usar el mismo mecanismo permite de correlación entre trazas proporcionado por el tiempo de ejecución y proporcionados por la aplicación y hace de solución de problemas. Plantillas de proyecto de servicio tela en Visual Studio incluyen una API de registro (según la clase de **origen de eventos** de .NET) que emite seguimientos de ETW de forma predeterminada. Para obtener una descripción general del seguimiento de aplicaciones de servicio tela usando ETW, vea [supervisar y diagnosticar servicios en una configuración de desarrollo de equipo local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Para que los seguimientos aparezca en Elasticsearch, que necesitan se capturan en los nodos de clúster de tela de servicio en tiempo real (mientras se ejecuta la aplicación) y se envían a un extremo de Elasticsearch. Existen dos opciones principales para capturar el seguimiento:

+ **Capturar el seguimiento en proceso**  
La aplicación, o con mayor precisión, proceso del servicio es responsable de envío de los datos de diagnóstico para el almacén de seguimiento (Elasticsearch).

+ **Capturar el seguimiento de fuera de proceso**  
Capturar trazas de proceso de servicio o procesos y enviarlos a la tienda de seguimiento un agente independiente.

A continuación, se describe cómo configurar Elasticsearch en Azure, describa las ventajas y desventajas ambas opciones de captura y explican cómo configurar un servicio de tela de servicio para enviar datos a Elasticsearch.


## <a name="set-up-elasticsearch-on-azure"></a>Configurar Elasticsearch en Azure
La manera más sencilla de configurar el servicio de Elasticsearch en Azure es a través de [**plantillas de administrador de recursos de Azure**](../azure-resource-manager/resource-group-overview.md). [Plantilla de administrador de recursos de Azure tutorial rápido para Elasticsearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) completa está disponible desde repositorio de plantillas de Azure tutorial. Esta plantilla utiliza cuentas de almacenamiento independientes para las unidades de escala (grupos de nodos). También puede configurar cliente independiente y nodos de servidor con diferentes configuraciones y números distintos de los discos de datos adjuntados.

Aquí, usamos otra plantilla, denominado **ES de múltiples nodos** del [repositorio de herramientas de diagnóstico de Azure](https://github.com/Azure/azure-diagnostics-tools). Esta plantilla es más fácil de usar y crea un clúster de Elasticsearch protegido por autenticación básica de HTTP. Antes de continuar, descargar el repositorio de GitHub a su equipo (por clonar el repositorio o descargar un archivo zip). La plantilla ES de múltiples nodos se encuentra en la carpeta con el mismo nombre.

### <a name="prepare-a-machine-to-run-elasticsearch-installation-scripts"></a>Preparar un equipo para ejecutar las secuencias de comandos de instalación de Elasticsearch
La manera más sencilla de usar la plantilla ES de múltiples nodos es a través de una secuencia de comandos de PowerShell de Azure proporcionada denominado `CreateElasticSearchCluster`. Para usar esta secuencia de comandos, debe instalar módulos de PowerShell y una herramienta denominada **openssl**. Esto último es necesaria para crear una clave SSH que puede usarse para administrar el clúster Elasticsearch de forma remota.

`CreateElasticSearchCluster`secuencia de comandos está diseñada para facilitar su uso con la plantilla ES de múltiples nodos desde un equipo Windows. Es posible usar la plantilla en un equipo distinto de Windows, pero este escenario está fuera del ámbito de este artículo.

1. Si no ha instalado ellos ya, instale [**módulos de PowerShell de Azure**](http://aka.ms/webpi-azps). Cuando se le solicite, haga clic en **Ejecutar**, a continuación, **instalar**. Se requiere Azure PowerShell 1.3 o una versión posterior.

2. La herramienta de **openssl** se incluye en la distribución de [**Git para Windows**](http://www.git-scm.com/downloads). Si aún no lo ha hecho, instale ahora [Git para Windows](http://www.git-scm.com/downloads) . (Las opciones de instalación predeterminadas son Aceptar).

3. Suponiendo que se ha instalado pero no incluido en la ruta del sistema Git, abra una ventana de Microsoft Azure PowerShell y ejecute los comandos siguientes:

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Reemplazar el `<Git installation folder>` con la ubicación de Git en su equipo; el valor predeterminado es **"C:\Program Files\Git"**. Tenga en cuenta el carácter de punto y coma al principio de la primera trayectoria.

4. Asegúrese de que ha iniciado sesión en Azure (a través de [`Add-AzureRmAccount`](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet) y que ha seleccionado la suscripción que se debe utilizar para crear el clúster de búsqueda elástico. Puede comprobar que la suscripción correcta está seleccionada con `Get-AzureRmContext` y `Get-AzureRmSubscription` cmdlets.

5. Si aún no lo ha hecho, cambie el directorio actual a la carpeta ES de múltiples nodos.

### <a name="run-the-createelasticsearchcluster-script"></a>Ejecutar la secuencia de comandos de CreateElasticSearchCluster
Antes de ejecutar la secuencia de comandos, abra la `azuredeploy-parameters.json` de archivos y comprobar o proporcionar los valores para los parámetros de script. Se proporcionan los siguientes parámetros:

|Nombre de parámetro           |Descripción|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |El nombre que se usa para crear el nombre DNS públicamente visible para el clúster de búsqueda elásticas (agregando el dominio de la región de Azure en el nombre proporcionado). Por ejemplo, si el valor del parámetro es "myBigCluster" y la región de Azure elegida es occidental de Estados Unidos, el nombre resultante de DNS para el clúster es myBigCluster.westus.cloudapp.azure.com. <br /><br />Este nombre también sirve como un nombre de raíz para muchos artefactos asociado con el clúster de búsqueda elástico, como los nombres de nodo de datos.|
|adminUsername           |El nombre de la cuenta de administrador para administrar el clúster de búsqueda elásticas (claves SSH correspondientes se generan automáticamente).|
|dataNodeCount           |El número de nodos en el clúster de búsqueda elástico. La versión actual de la secuencia de comandos no distingue entre los nodos de datos y la consulta; todos los nodos reproducen ambas funciones. El valor predeterminado es 3 nodos.|
|dataDiskSize            |El tamaño de los discos de datos (en GB) que está asignado para cada nodo de datos. Cada nodo recibe 4 discos de datos, exclusivamente dedicados al servicio de búsqueda elástico.|
|región                  |El nombre de Azure región donde debe ubicarse el clúster de búsqueda elástico.|
|esUserName              |El nombre de usuario del usuario que está configurado para tener acceso al clúster ES (sujeto a autenticación básica de HTTP). La contraseña no forma parte del archivo de parámetros y debe proporcionarse cuando `CreateElasticSearchCluster` se invoca script.|
|vmSizeDataNodes         |El tamaño de Azure máquina virtual para nodos de clúster de búsqueda elástico. El valor predeterminado es Standard_D2.|

Ahora ya está listo para ejecutar la secuencia de comandos. Ejecute el siguiente comando:

```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name> -Region <azure-region> -EsPassword <es-password>
```

donde 

|Nombre de parámetro de secuencia de comandos    |Descripción|
|-----------------------  |--------------------------|
|`<es-group-name>`        |El nombre del grupo de recursos de Azure que contendrá todos los recursos de clúster de búsqueda elástico.|
|`<azure-region>`         |El nombre de la región de Azure donde se debe crear el clúster de búsqueda elástico.|         
|`<es-password>`          |La contraseña para el usuario de búsqueda elástico.|

>[AZURE.NOTE] Si recibe una excepción NullReferenceException del cmdlet AzureResourceGroup de prueba, ha olvidado iniciar sesión en Azure (`Add-AzureRmAccount`).

Si recibe un error de ejecución de la secuencia de comandos y determinar que el error se produjo por un valor de parámetro de plantilla incorrecta, corrija el archivo de parámetro y ejecutar la secuencia de comandos de nuevo con un nombre de grupo de recursos. También puede volver a utilizar el mismo nombre de grupo de recursos y hacer que la secuencia de comandos limpiar unas agregando el `-RemoveExistingResourceGroup` parámetro a la llamada de script.

### <a name="result-of-running-the-createelasticsearchcluster-script"></a>Resultado de la ejecución de la secuencia de comandos de CreateElasticSearchCluster
Después de ejecutar el `CreateElasticSearchCluster` secuencia de comandos, se creará los siguientes artefactos principales. En este ejemplo se asume que ha utilizado "myBigCluster" como el valor de la `dnsNameForLoadBalancerIP` parámetro y que la región donde la creó el clúster es occidental de Estados Unidos.

|Muestra|Nombre, ubicación y comentarios|
|----------------------------------|----------------------------------|
|Tecla SSH para la administración remota |archivo de myBigCluster.key (en el directorio desde el que se ejecuta la CreateElasticSearchCluster). <br /><br />Este archivo de clave se puede usar para conectar con el nodo de administración y (mediante el nodo Administración) para los nodos de datos en el clúster.|
|Nodo de administración                        |myBigCluster admin.westus.cloudapp.azure.com <br /><br />Una VM dedicada para la administración de clúster Elasticsearch remota--el único que permita las conexiones de SSH externas. Se ejecuta en la misma red virtual que todos los nodos de clúster Elasticsearch, pero no se ejecutan los servicios de Elasticsearch.|
|Nodos de datos                        |myBigCluster1... myBigCluster*N* <br /><br />Nodos de datos que se están ejecutando los servicios de Elasticsearch y Kibana. Puede conectarse a través de SSH para cada nodo, pero solo mediante el nodo de administración.|
|Clúster de Elasticsearch             |http://myBigCluster.westus.cloudapp.Azure.com/es/ <br /><br />Extremo principal para el clúster de Elasticsearch (tenga en cuenta el sufijo/es). Está protegido por autenticación básica de HTTP (las credenciales son los parámetros de esUserName/esPassword especificados de la plantilla ES de múltiples nodos). El clúster también tiene la cabeza complemento instalada (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) para la administración de clúster básico.|
|Servicio de Kibana                    |http://myBigCluster.westus.cloudapp.Azure.com <br /><br />El servicio Kibana está configurado para mostrar los datos de clúster Elasticsearch creado. Está protegido por las mismas credenciales de autenticación que el clúster propiamente dicho.|

## <a name="in-process-versus-out-of-process-trace-capturing"></a>En proceso frente a capturar el seguimiento de fuera de proceso
En la introducción, se mencionan dos formas básicas de recopilación de datos de diagnóstico: en proceso y fuera de proceso. Cada una tiene puntos fuertes y débiles.

Ventajas del **seguimiento captura en proceso** son:

1. *Fácil configuración e implementación*

    * La configuración de recopilación de datos de diagnóstico es solo parte de la configuración de la aplicación. Es fácil siempre mantener "sincronizado" con el resto de la aplicación.

    * Configuración por aplicación o por servicio es puede llevar a cabo.

    * Traza de fuera de proceso captura, normalmente requiere una implementación independiente y configuración de agente de diagnóstico, que es una tarea administrativa adicional y una fuente potencial de errores. A menudo, la tecnología de agente determinado permite solo una instancia del agente por máquina virtual (nodo). Esto significa que la configuración de la colección de la configuración de diagnóstico se comparte entre todas las aplicaciones y servicios que se ejecutan en ese nodo.

2. *Flexibilidad*

    * La aplicación puede enviar los datos donde tiene que ir, siempre y cuando hay una biblioteca de cliente que admita el sistema de almacenamiento de datos destino. Nuevos receptores pueden agregarse como deseado.

    * Pueden implementar reglas de captura, el filtrado y la agregación de datos complejos.

    * Una traza de fuera de proceso captura a menudo está limitado por los receptores de datos que admite el agente. Algunos agentes son extensibles.

3. *Acceso a datos de la aplicación interna y contexto*

    * El subsistema de diagnóstico ejecutando dentro del proceso de aplicación o servicio puede ampliar fácilmente los seguimientos con información contextual.

    * En el enfoque fuera de proceso, los datos deben enviarse a un agente a través de algún mecanismo de comunicación entre procesos, como el seguimiento de eventos de Windows. Este mecanismo podría imponer limitaciones adicionales.

Ventajas de la **captura de seguimiento de fuera de proceso** son:

1. *La capacidad para supervisar la aplicación y recopilar volcados*

    * Capturar el seguimiento en proceso pueden fallar si la aplicación no se puede iniciar o se bloquea. Un agente independiente tiene un más posibilidades de capturar información crucial de solución de problemas.<br /><br />

2. *Vencimiento, estabilidad y rendimiento comprobada*

    * Ha sido un agente desarrollado por un proveedor de plataforma (por ejemplo, un agente de diagnósticos de Microsoft Azure) sujeto a pruebas rigurosa y batalla consolidación.

    * Con seguimiento de en proceso captura, debe tener cuidado para asegurarse de que la actividad de envío de datos de diagnóstico desde un proceso de aplicación no interfiera con las tareas principales de la aplicación o presente problemas de rendimiento o intervalos. Un agente de ejecución de forma independiente es menos susceptible a estos problemas y está diseñado para limitar el impacto en el sistema.

Es posible combinar y sacar partido de ambos métodos. De hecho, puede ser la mejor solución para muchas aplicaciones.

A continuación, usamos la **biblioteca de Microsoft.Diagnostic.Listeners** y el seguimiento en proceso captura para enviar datos desde una aplicación de servicio tela a un clúster de Elasticsearch.

## <a name="use-the-listeners-library-to-send-diagnostic-data-to-elasticsearch"></a>Usar la biblioteca de agentes de escucha para enviar los datos de diagnóstico a Elasticsearch
La biblioteca de Microsoft.Diagnostic.Listeners es parte de la aplicación de servicio tela de ejemplo PartyCluster. Se usa:

1. Descargue [el ejemplo PartyCluster](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) desde GitHub.

2. Copiar los proyectos Microsoft.Diagnostics.Listeners y Microsoft.Diagnostics.Listeners.Fabric (carpetas enteras) desde el directorio de ejemplo PartyCluster en la carpeta de soluciones de la aplicación que se supone que envía los datos a Elasticsearch.

3. Abra la solución de destino, haga clic en el nodo de solución en el Explorador de soluciones y elija **Agregar proyecto existente**. Agregar el proyecto Microsoft.Diagnostics.Listeners a la solución. Repita el mismo procedimiento para el proyecto Microsoft.Diagnostics.Listeners.Fabric.

4. Agregue una referencia de proyecto de sus proyectos de servicio para los dos proyectos agregados. (Cada servicio que se supone que para enviar datos a Elasticsearch debe hacer referencia a Microsoft.Diagnostics.EventListeners y Microsoft.Diagnostics.EventListeners.Fabric).

    ![Referencias de proyecto a las bibliotecas Microsoft.Diagnostics.EventListeners y Microsoft.Diagnostics.EventListeners.Fabric][1]

### <a name="service-fabric-general-availability-release-and-microsoftdiagnosticstracing-nuget-package"></a>Lanzamiento de disponibilidad General de la estructura de servicio y paquete Microsoft.Diagnostics.Tracing Nuget
Las aplicaciones creadas con la versión de disponibilidad General de tela de servicio (2.0.135, publicado el 31 de marzo de 2016) destino **4.5.2 de .NET Framework**. Esta versión es la última versión de .NET Framework compatible con Azure en el momento de la versión GA. Desgraciadamente, esta versión de framework carece de determinadas EventListener APIs que necesita de la biblioteca Microsoft.Diagnostics.Listeners. Como origen de eventos (el componente que constituye la base de registro API en aplicaciones de tela) y EventListener se acoplan estrechamente, todos los proyectos que utiliza la biblioteca de Microsoft.Diagnostics.Listeners deben utilizar una implementación alternativa del origen de eventos. Esta implementación proporciona el **paquete Microsoft.Diagnostics.Tracing Nuget**, creados por Microsoft. El paquete es totalmente compatible con el origen de eventos que se incluyen en el marco de trabajo, por lo que no hay cambios en el código deberían ser necesarios distinto de cambios de espacio de nombres que se hace referencia.

Para empezar a usar la implementación de Microsoft.Diagnostics.Tracing de la clase de origen de eventos, siga estos pasos para cada proyecto de servicio que necesita para enviar datos a Elasticsearch:

1. Haga clic en el proyecto de servicio y elija **Administrar paquetes de Nuget**.

2. Cambiar al origen del paquete nuget.org (si no está ya seleccionada) y busque "**Microsoft.Diagnostics.Tracing**".

3. Instalar el `Microsoft.Diagnostics.Tracing.EventSource` paquete (y sus dependencias).

4. Abra el archivo **ServiceEventSource.cs** o **ActorEventSource.cs** en el proyecto de servicio y reemplace la `using System.Diagnostics.Tracing` directiva en la parte superior del archivo con el `using Microsoft.Diagnostics.Tracing` directiva.

Estos pasos no será necesarios una vez que el **.NET Framework 4.6** es compatible con Microsoft Azure.

### <a name="elasticsearch-listener-instantiation-and-configuration"></a>Configuración y creación de instancia de escucha Elasticsearch
El paso final para el envío de datos de diagnóstico para Elasticsearch es crear una instancia de `ElasticSearchListener` y configurar con datos de la conexión de Elasticsearch. El oyente captura automáticamente todos los eventos elevados a través de clases de origen de eventos definidas en el proyecto de servicio. Debe estar activo durante la duración del servicio, por lo que es el mejor lugar para crear el código de inicialización de servicio. Esto es el aspecto que podría el código de inicialización de un servicio independiente después de los cambios necesarios (adiciones señalaron en comentarios, comenzando con `****`):

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Runtime;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>        
        private static void Main()
        {
            try
            {
                // **** Instantiate ElasticSearchListener
                var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                ElasticSearchListener esListener = null;
                if (configProvider.HasConfiguration)
                {
                    esListener = new ElasticSearchListener(configProvider);
                }

                // The ServiceManifest.XML file defines one or more service type names.
                // Registering a service maps a service type name to a .NET type.
                // When Service Fabric creates an instance of this service type,
                // an instance of the class is created in this host process.

                ServiceRuntime.RegisterServiceAsync("Stateless1Type", 
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                // Prevents this host process from terminating so services keep running.
                Thread.Sleep(Timeout.Infinite);

                // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                GC.KeepAlive(esListener);
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Datos de la conexión de Elasticsearch se deben incluir en una sección independiente en el archivo de configuración de servicio (**PackageRoot\Config\Settings.xml**). El nombre de la sección deberá corresponderse con el valor pasa a la `FabricConfigurationProvider` constructor, por ejemplo:

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
Los valores de `serviceUri`, `userName` y `password` parámetros corresponden a la dirección de extremo de clúster Elasticsearch, Elasticsearch nombre de usuario y contraseña, respectivamente. `indexNamePrefix`es el prefijo de índices de Elasticsearch; la biblioteca de Microsoft.Diagnostics.Listeners crea un nuevo índice para sus datos diariamente.

### <a name="verification"></a>Comprobación
¡Eso es todo! Ahora, siempre que se ejecuta el servicio, comienza enviando trazas al servicio Elasticsearch especificado en la configuración. Puede comprobar esta abriendo la UI Kibana asociado con la instancia de Elasticsearch de destino. En nuestro ejemplo, la dirección de la página es http://myBigCluster.westus.cloudapp.azure.com/. Compruebe que los índices con el prefijo de nombre elegido para el `ElasticSearchListener` instancia de hecho se han creado y rellenado con datos.

![Eventos de la aplicación que muestra Kibana PartyCluster][2]

## <a name="next-steps"></a>Pasos siguientes
- [Más información sobre diagnosticar y supervisar el servicio de tela de servicio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png
