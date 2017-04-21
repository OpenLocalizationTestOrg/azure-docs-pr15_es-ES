<properties
   pageTitle="Modelo de aplicación de servicio tela | Microsoft Azure"
   description="Cómo modelo y describir aplicaciones y servicios en tela de servicio."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"   
   ms.author="seanmck"/>

# <a name="model-an-application-in-service-fabric"></a>Una aplicación de servicio tela del modelo

En este artículo se proporciona información general sobre el modelo de aplicación de Azure servicio tela. También se describe cómo definir una aplicación y el servicio a través de los archivos de manifiestos y obtener la aplicación empaquetado y listo para su implementación.

## <a name="understand-the-application-model"></a>Entender el modelo de aplicación

Una aplicación es una colección de servicios constituyentes que lleve a cabo una determinada función o funciones. Un servicio realiza una función independiente y completa (puede iniciar y ejecutar independientemente de otros servicios) y se compone de código, configuración y datos. Para cada servicio, código consta de los archivos binarios ejecutables, configuración consta de configuración del servicio que se puede cargar en tiempo de ejecución y datos constan de los datos estáticos arbitrarios para ser consumido por el servicio. En este modelo de aplicación jerárquica cada componente puede ser una versión y actualizado por separado.

![El modelo de aplicación de servicio tela][appmodel-diagram]


Un tipo de aplicación es una clasificación de una aplicación y consta de un paquete de tipos de servicio. Un tipo de servicio es una clasificación de un servicio. La categorización puede tener diferentes configuraciones y configuraciones, pero la funcionalidad básica es el mismo. Las instancias de un servicio son las variaciones de la configuración de servicio diferente del mismo tipo de servicio.  

Clases (o "tipos") de aplicaciones y servicios que se describen a través de archivos XML (manifiestos de aplicación y servicio manifiestos) que se encuentran las plantillas que se pueden crear instancias de almacén de imágenes del clúster aplicaciones. Definición del esquema para el archivo ServiceManifest.xml y ApplicationManifest.xml se instala con el servicio tela SDK y herramientas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

El código de instancias diferentes de la aplicación se ejecutará como procesos independientes, incluso cuando se hospeda en el mismo nodo de servicio tela. Además, se puede administrar el ciclo de vida de cada instancia de la aplicación (es decir actualizar) por separado. El siguiente diagrama muestra cómo tipos de aplicación se componen de los tipos de servicio, que a su vez están compuestos de código, configuración y paquetes. Para simplificar el diagrama, solo el código / / datos de configuración de paquetes de `ServiceType4` se muestran, aunque cada tipo de servicio incluiría algunos o todos los tipos de paquetes.

![Tipos de aplicaciones de servicio tela y tipos de servicio][cluster-imagestore-apptypes]

Dos archivos diferentes de manifiestos se utilizan para describir las aplicaciones y servicios: el servicio manifiestos y aplicación. Estos se tratan en detalle en las secciones subsiguientes.

Puede haber una o varias instancias de un tipo de servicio activo en el clúster. Por ejemplo, las instancias de estado del servicio, o réplicas, lograr alta confiabilidad mediante la replicación de estado entre réplicas ubicadas en distintos nodos en el clúster. Esta replicación esencialmente proporciona redundancia para el servicio esté disponible incluso si se produce un error en un nodo en un clúster. Un [servicio con particiones](service-fabric-concepts-partitioning.md) más divide su estado (y patrones de acceso a ese estado) en los nodos en el clúster.

En el diagrama siguiente muestra la relación entre las aplicaciones y servicios, particiones y réplicas.

![Particiones y réplicas dentro de un servicio][cluster-application-instances]


>[AZURE.TIP] Puede ver el diseño de aplicaciones en un clúster mediante la herramienta de servicio tela Explorer disponible en http://&lt;yourclusteraddress&gt;: 19080/explorador. Para obtener más información, consulte [visualizar el clúster con el Explorador de tela de servicio](service-fabric-visualizing-your-cluster.md).

## <a name="describe-a-service"></a>Describir un servicio

El manifiesto del servicio mediante declaración define el tipo de servicio y la versión. Especifica como tipo de servicio, propiedades de mantenimiento, métrica de equilibrio de carga, binarios de servicio y archivos de configuración de metadatos del servicio.  Dicho de otro modo, se describen los paquetes de código, configuración y datos que componen un paquete de servicio para uno o más tipos de servicio de soporte técnico. Esto es un manifiesto de servicio de ejemplo sencillo:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
~~~

Los atributos de **versión** son cadenas no estructuradas y no analizar por el sistema. Se trata de utilizar cada componente a la versión para actualizaciones.

**ServiceTypes** declara qué tipos de servicio son compatibles con **CodePackages** de este manifiesto. Cuando se crea una instancia de un servicio en uno de estos tipos de servicio, todos los paquetes de código declarados en este manifiesto se activan ejecutando sus puntos de entrada. Los procesos resultantes se espera registrar los tipos de servicio admitido en tiempo de ejecución. Observe que los tipos de servicio se declara en el nivel de manifiestos y no el nivel de paquete de código. Así que si hay varios paquetes de código, se todos activan siempre que el sistema busca cualquiera de los tipos de servicio declarado.

**SetupEntryPoint** es un punto de entrada con privilegios que se ejecuta con las mismas credenciales como servicio tela (normalmente la cuenta *LocalSystem* ) antes de cualquier otro punto de entrada. El archivo ejecutable especificado por el **punto de entrada** normalmente es el host de servicio de ejecución larga. La presencia de un punto de entrada del programa de instalación independiente evita tener que ejecutar el host del servicio con privilegios elevados para largos períodos de tiempo. El archivo ejecutable especificado por el **punto de entrada** se ejecuta cuando sale de **SetupEntryPoint** correctamente. El proceso resultante se supervisa y reinicia (comenzando con **SetupEntryPoint**) si alguna vez termina o se bloquea.

**DataPackage** declara una carpeta, denominada por el atributo de **nombre** , que contiene datos estáticos arbitrarios para ser consumido por el proceso en tiempo de ejecución.

**ConfigPackage** declara una carpeta, denominada por el atributo de **nombre** , que contiene un archivo *Settings.xml* . Este archivo contiene secciones de los valores de par definidas por el usuario, el valor de clave que puede leer el proceso en tiempo de ejecución. Durante la actualización, si solo ha cambiado la **versión** de **ConfigPackage** , a continuación, el proceso en ejecución no se reinicia. En su lugar, una devolución de llamada notifica el proceso que han cambiado los valores de configuración para que puedan volver a cargar dinámicamente. Aquí es un ejemplo de archivo *Settings.xml* :

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
~~~

> [AZURE.NOTE] Un manifiesto de servicio puede contener varias código, configuración y paquetes de datos. Cada uno de esos pueden versiones independientemente.

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>Describir una aplicación


El manifiesto de aplicación mediante declaración describe el tipo de aplicación y la versión. Especifica el servicio de metadatos de composición como nombres estables, partición de esquema, factor de recuento y replicación de instancia, directiva de seguridad y aislamiento, las restricciones de posición, configuración reemplaza y tipos de servicio constituyentes. También se describen los dominios de equilibrio de carga en el que se coloca la aplicación.

Así, un manifiesto de aplicación describe los elementos en el nivel de aplicación y se hace referencia a uno o varios manifiestos de servicio para redactar un tipo de aplicación. Esto es un manifiesto de aplicación de ejemplo sencillo:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
~~~

Como servicio manifiestos, los atributos de **versión** son cadenas no estructuradas y no se analizan por el sistema. También están destinados a versión cada componente actualizaciones.

**ServiceManifestImport** contiene referencias a manifiestos de servicio que componen este tipo de aplicación. Servicio importados manifiestos determinan qué tipos de servicio son válidas en este tipo de aplicación.

**DefaultServices** declara instancias de servicio que se crean automáticamente cada vez que se crea una instancia de una aplicación contra este tipo de aplicación. Servicios predeterminados son simplemente una comodidad y se comportan de modo similar a servicios normales en todos los aspectos después de haber creado. Que se actualizan junto con otros servicios de la instancia de aplicación y también se pueden quitar.

> [AZURE.NOTE] Un manifiesto de aplicación puede contener varias importaciones manifiestos de servicio y servicios predeterminados. Cada importación manifiestos de servicio puede ser una versión por separado.

Para obtener información sobre cómo mantener la aplicación diferente y los parámetros de servicio para entornos individuales, vea [parámetros de la aplicación de administración para múltiples entornos](service-fabric-manage-multiple-environment-app-configuration.md).

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->

## <a name="package-an-application"></a>Empaquetar una aplicación

### <a name="package-layout"></a>Diseño de paquete

El manifiesto de aplicación, servicio manifiestos y otros archivos de paquete necesarios deben organizarse en un diseño específico para su implementación en un clúster de tela de servicio. Manifiestos de ejemplo en este artículo necesarias para organizarse en la estructura de directorio siguiente:

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
~~~

Las carpetas se denominan para que coincida con los atributos de **nombre** de cada elemento correspondiente. Por ejemplo, si el manifiesto del servicio contiene dos paquetes de código con los nombres **MyCodeA** y **MyCodeB**, a continuación, dos carpetas con los mismos nombres contendrá los archivos binarios necesarios para cada paquete de código.

### <a name="use-setupentrypoint"></a>Usar SetupEntryPoint

Los escenarios de uso **SetupEntryPoint** están cuando que necesita para ejecutar un archivo ejecutable antes de iniciar el servicio o debe realizar una operación con privilegios elevados. Por ejemplo:

- Configurar e inicialización de variables de entorno que necesita el archivo ejecutable del servicio. Esto no se limita a sólo ejecutables escrito a través de los modelos de programación de servicio tela. Por ejemplo, npm.exe necesita algunas variables de entorno configurados para implementar una aplicación de node.js.

- Configurar el control de acceso al instalar certificados de seguridad.

### <a name="build-a-package-by-using-visual-studio"></a>Crear un paquete mediante Visual Studio

Si utiliza 2015 de Visual Studio para crear la aplicación, puede usar el comando de paquete para crear automáticamente un paquete que coincida con el diseño que se indica más arriba.

Para crear un paquete, haga clic en el proyecto de aplicación en el Explorador de soluciones y elija el comando del paquete, tal como se muestra a continuación:

![Empaquetar una aplicación con Visual Studio][vs-package-command]

Una vez finalizada embalaje, encontrará la ubicación del paquete en la ventana de **resultados** . Observe que el paso de embalaje produce automáticamente al implementar o depurar la aplicación en Visual Studio.

### <a name="test-the-package"></a>Probar el paquete

Puede comprobar la estructura de paquete localmente a través de PowerShell mediante el comando **ServiceFabricApplicationPackage de prueba** . Este comando se compruebe de manifiesto problemas de análisis y todas las referencias. Tenga en cuenta que este comando solo comprueba la corrección estructural de los directorios y archivos en el paquete. No verificará cualquiera del código o datos de contenido del paquete más allá de comprobar que todos los archivos necesarios están presentes.

~~~
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

Este error se muestra que falta el archivo *MySetup.bat* que se hace referencia en el manifiesto del servicio **SetupEntryPoint** del paquete de código. Después de agrega el archivo que falta, pasa la comprobación de la aplicación:

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
~~~

Una vez que la aplicación se ha empaquetado correctamente y pasa la comprobación, ya está listo para su implementación.

## <a name="next-steps"></a>Pasos siguientes

[Implementar y quitar aplicaciones][10]

[Administrar los parámetros de la aplicación para múltiples entornos][11]

[RunAs: Ejecute una aplicación de servicio tela con permisos de seguridad diferentes][12]

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
[vs-package-command]: ./media/service-fabric-application-model/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
