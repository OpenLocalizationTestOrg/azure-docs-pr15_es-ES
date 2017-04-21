<properties
   pageTitle="Implementar un archivo ejecutable existente en tela de servicio de Azure | Microsoft Azure"
   description="Tutorial acerca de cómo empaquetar una aplicación existente como invitado ejecutable, por lo que se puede implementar en un clúster de tela de servicio"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>

# <a name="deploy-a-guest-executable-to-service-fabric"></a>Implementar a un invitado ejecutable en tela de servicio

Puede ejecutar cualquier tipo de aplicación, como node.js, Java o aplicaciones nativas en tela de servicio de Azure. Tela de servicio hace referencia a estos tipos de aplicaciones como invitado ejecutables.
Servicio tela trata invitado ejecutables como servicios sin estado. Por tanto, se colocan en los nodos en un clúster, en función de la disponibilidad y otras métricas. En este artículo se describe cómo empaquetar e implementar a un invitado ejecutable en un clúster de servicio tela utilizando Visual Studio o una herramienta de línea de comandos.

En este artículo se tratan los pasos para empaquetar a invitado ejecutable e implementarlo en tela de servicio.  

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Ventajas de la ejecución de invitado ejecutable en tela de servicio

Hay varias ventajas que vienen con la ejecución de invitado ejecutable en un clúster de tela de servicio:

- Alta disponibilidad. Aplicaciones que se ejecutan en tela de servicio se realizan altamente disponibles. Servicio tela garantiza que se están ejecutando instancias de una aplicación.
- Supervisión de estado. Supervisión de estado del servicio tela detecta si una aplicación se está ejecutando y proporciona información de diagnóstico si se produce un error.   
- Administración de ciclo de vida de la aplicación. Además de proporcionar actualizaciones sin tiempo de inactividad, servicio tela proporciona restauración automática a la versión anterior si hay un evento de mal estado notificado durante la actualización.    
- Densidad. Puede ejecutar varias aplicaciones en un clúster, que elimina la necesidad de cada aplicación se ejecute en su propio hardware.


## <a name="overview-of-application-and-service-manifest-files"></a>Información general sobre la aplicación y los archivos de manifiestos de servicio

Como parte de la implementación de invitado ejecutable, resulta útil entender el embalaje tela de servicio y modelo de implementación como describe [el modelo de aplicación](service-fabric-application-model.md). El modelo de empaquetado de tela de servicio se basa en dos archivos XML: los manifiestos de aplicaciones y servicios. Definición del esquema para los archivos ApplicationManifest.xml y ServiceManifest.xml se instala con el SDK de tela de servicio en *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifiestos de aplicación** El manifiesto de aplicación se usa para describir la aplicación. Muestra los servicios que componen se y otros parámetros que se utilizan para definir cómo se deben implementar los servicios de uno o más, como el número de instancias.

  En la estructura de servicio de una aplicación es una unidad de implementación y actualización. Puede actualizar una aplicación como una sola unidad donde se administra los posibles errores y posibles deshacer. Servicio tela garantiza que el proceso de actualización es correcta, o bien, si se produce un error en la actualización, no deje la aplicación en un estado estable o desconocido.

* **Servicio de manifiestos** El manifiesto del servicio describe los componentes de un servicio. Incluye datos, como el nombre y tipo de servicio y su código, configuración y datos. El manifiesto del servicio también incluye algunos parámetros adicionales que se pueden usar para configurar el servicio una vez que se ha implementado.


## <a name="application-package-file-structure"></a>Estructura de archivo de paquete de aplicación
Para implementar una aplicación de servicio tela, la aplicación debe seguir una estructura de directorio predefinidos. El siguiente ejemplo de esa estructura.

```
|-- ApplicationPackageRoot
  	|-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```

El ApplicationPackageRoot contiene el archivo ApplicationManifest.xml que define la aplicación. Se usa un subdirectorio para cada servicio que se incluyen en la aplicación que contenga todos los artefactos que requiere el servicio--el ServiceManifest.xml y, normalmente, los siguientes tres directorios:

- *Código*. Este directorio contiene el código de servicio.
- *Config*. Este directorio contiene un archivo Settings.xml (y otros archivos, si es necesario) que puede tener acceso al servicio en tiempo de ejecución para recuperar la configuración específica.
- *Datos*. Se trata de un directorio adicional para almacenar datos locales adicionales que necesite el servicio. Nota: Se deben utilizar datos para almacenar datos efímeros solo. Tela de servicio no copia o replicar cambios en el directorio de datos si el servicio se necesita reubicar: por ejemplo, durante la migración tras error.

Nota: No tiene que crear el `config` y `data` directorios si no necesita.

## <a name="packaging-an-existing-executable"></a>Empaquetar un archivo ejecutable existente

Al empaquetar un ejecutable de invitado, puede elegir uno para usar una plantilla de proyecto de Visual Studio o [crear de forma manual el paquete de aplicación](#manually). Con Visual Studio, la estructura de paquete de aplicación y archivos de manifiestos creados por el Asistente para nuevo proyecto para usted.

>[AZURE.NOTE] La manera más sencilla de empaquetar un ejecutable en un servicio de Windows existente es usar Visual Studio.

## <a name="using-visual-studio-to-package-an-existing-executable"></a>Uso de Visual Studio para empaquetar un archivo ejecutable existente

Visual Studio proporciona una plantilla de servicio del servicio tela para ayudarle a implementar a un invitado ejecutable en un clúster de tela de servicio.

Siga los pasos siguientes para completar la publicación:

1. Elija Archivo -> Nuevo proyecto y crear una aplicación de servicio de tela.
2. Elija archivo ejecutable Invitado como la plantilla de servicio.
3. Haga clic en Examinar para seleccionar la carpeta con el ejecutable y rellene el resto de los parámetros para crear el servicio.
    - *Comportamiento del paquete de código* se puede configurar para copiar todo el contenido de la carpeta en el proyecto de Visual Studio, lo que resulta útil si el archivo ejecutable no cambia. Si desea que el archivo ejecutable a cambiar y quiere que la capacidad de recoger nuevas revisiones dinámicamente, puede establecer un vínculo a la carpeta en su lugar. Tenga en cuenta que puede usar carpetas vinculadas al crear el proyecto de la aplicación en Visual Studio. Este proporciona vínculos a la ubicación de origen desde dentro del proyecto, lo que permite que actualice al invitado ejecutable en su origen de destino, tener las actualizaciones se convierten en parte del paquete de aplicación de compilación.
    - *Programa* : elija el archivo ejecutable que se debe ejecutar para iniciar el servicio.
    - *Argumentos* : especifique los argumentos que se deben pasar al archivo ejecutable. Puede ser una lista de parámetros con argumentos.
    - *WorkingFolder* - especifica el directorio de trabajo del proceso que se va a iniciar. Puede especificar tres valores:
        - `CodeBase`Especifica que el directorio de trabajo se va a establecer para el directorio de código en el paquete de aplicación (`Code` directorio en la estructura de archivo se muestra anterior).
        - `CodePackage`Especifica que el directorio de trabajo se va a establecer en la raíz del paquete de aplicación (`GuestService1Pkg` en la estructura de archivo se muestra anterior).
        - `Work`Especifica que los archivos se colocan en un subdirectorio denominado trabajo
4. Asigne un nombre a su servicio y haga clic en Aceptar.
5. Si su servicio necesita un extremo de comunicación, ahora puede agregar el protocolo, puerto y tipo al archivo ServiceManifest.xml. e.g. `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Ahora puede utilizar el paquete y publicar en el clúster local acción depurar la solución en Visual Studio. Cuando esté listo puede publicar la aplicación en un clúster remoto o verificación de la solución al control de origen.
7. Ir al final de este artículo para ver cómo ver servicio ejecutable invitado se ejecuta en el Explorador de tela de servicio.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-an-existing-executable"></a>Manualmente empaquetar e implementar un archivo ejecutable existente
El proceso de empaquetar manualmente un ejecutable invitado se basa en los siguientes pasos:

1. Crear la estructura de directorios de paquete.
2. Agregar archivos de código y la configuración de la aplicación.
3. Editar el archivo de manifiestos de servicio.
4. Editar el archivo de manifiestos de aplicación.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Crear la estructura de directorios de paquete
Puede iniciar mediante la creación de la estructura del directorio, como se describió anteriormente.

### <a name="add-the-applications-code-and-configuration-files"></a>Agregar archivos de código y la configuración de la aplicación
Después de crear la estructura del directorio, puede agregar código de la aplicación y archivos de configuración en los directorios de código y config. También puede crear directorios adicionales o subdirectorios en los directorios de código o config.

Servicio tela hace un xcopy del contenido del directorio de raíz de la aplicación, lo que no hay ninguna estructura predefinida a distinta de crear dos directorios superiores, código y la configuración. (Puede seleccionar nombres diferentes si lo desea. Más detalles están en la sección siguiente).

>[AZURE.NOTE] Asegúrese de que incluye todas las archivos o dependencias que necesita la aplicación. Servicio tela copia el contenido del paquete de aplicación en todos los nodos en el clúster donde en que se van a implementar los servicios de la aplicación. El paquete debe contener todo el código que debe ejecutar la aplicación. No se recomienda suponiendo que las dependencias ya están instaladas.

### <a name="edit-the-service-manifest-file"></a>Editar el archivo de manifiestos de servicio
El siguiente paso es editar el archivo de manifiestos de servicio para incluir la información siguiente:

- El nombre del tipo de servicio. Se trata de un identificador de tela de servicio para identificar un servicio.
- El comando se utiliza para iniciar la aplicación (ExeHost).
- Cualquier secuencia de comandos que se debe ejecutar para establecer seguridad y configurar la aplicación (SetupEntrypoint).

El siguiente es un ejemplo de un `ServiceManifest.xml` archivo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Repasemos las distintas partes del archivo que deberá actualizar:

#### <a name="updating-the-servicetypes"></a>Actualizar la ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- Puede elegir el nombre que desee para `ServiceTypeName`. Se usa el valor en el `ApplicationManifest.xml` archivo para identificar el servicio.
- Debe especificar `UseImplicitHost="true"`. Este atributo indica a tela de servicio que el servicio está basado en una aplicación independiente, por lo que todas las necesidades de servicio tela hacer es iniciar como un proceso y supervisar su estado.

#### <a name="updating-the-codepackage"></a>Actualizar la CodePackage
El elemento de CodePackage especifica la ubicación y versión del código del servicio.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

La `Name` elemento se utiliza para especificar el nombre del directorio en el paquete de aplicación que contiene el código del servicio. `CodePackage`También tiene la `version` atributo. Esto puede usarse para especificar la versión del código y también potencialmente puede usarse para actualizar del código del servicio mediante la infraestructura de administración de ciclo de vida de aplicación de servicio tela.
#### <a name="optional-updating-the-setupentrypoint"></a>Opcional: Actualizar la SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
El elemento SetupEntrypoint se utiliza para especificar cualquier archivo ejecutable o por lotes que debe ejecutarse antes de que se inicia del código del servicio. Es un paso opcional, por lo que no es necesario que se deben incluir si no hay ninguna inicialización y configuración necesaria. El SetupEntryPoint se ejecuta cada vez que se reinicia el servicio.

No hay un único SetupEntrypoint, por lo que se deben agrupar en un archivo por lotes solo si el programa de instalación de/config la aplicación requiere varias secuencias de comandos secuencias de comandos de instalación y configuración. La SetupEntrypoint puede ejecutar cualquier tipo de archivo: archivos ejecutables, archivos por lotes y cmdlets de PowerShell. Para obtener más detalles, vea [Configurar SetupEntryPoint](service-fabric-application-runas-security.md).

En el ejemplo anterior, la SetupEntrypoint ejecuta un archivo por lotes denominado `LaunchConfig.cmd` que se encuentra en la `scripts` subdirectorio del directorio de código (suponiendo que el elemento WorkingFolder se establece en código base).

#### <a name="updating-the-entrypoint"></a>Actualizar el punto de entrada

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

La `Entrypoint` elemento en el archivo de manifiestos de servicio se utiliza para especificar cómo iniciar el servicio. La `ExeHost` elemento especifica el ejecutable (y argumentos) que debe usarse para iniciar el servicio.

- `Program`Especifica el nombre del archivo ejecutable que se debe ejecutar para iniciar el servicio.
- `Arguments`Especifica los argumentos que se deben pasar al archivo ejecutable. Puede ser una lista de parámetros con argumentos.
- `WorkingFolder`Especifica el directorio de trabajo del proceso que se va a iniciar. Puede especificar tres valores:
    - `CodeBase`Especifica que el directorio de trabajo se va a establecer para el directorio de código en el paquete de aplicación (`Code` directorio en la estructura de archivo anterior).
    - `CodePackage`Especifica que el directorio de trabajo se va a establecer en la raíz del paquete de aplicación (`GuestService1Pkg` en la estructura de archivo anterior).
  - `Work`Especifica que los archivos se colocan en un subdirectorio denominado trabajo

La WorkingFolder es muy útil para configurar el directorio de trabajo correcto para que las rutas de acceso relativas pueden ser usadas por las secuencias de comandos de la aplicación o la inicialización.

#### <a name="updating-the-endpoints-and-registering-with-naming-service-for-communication"></a>Actualizar los extremos y registrar con el servicio de nombres para la comunicación

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
En el ejemplo anterior la `Endpoint` elemento especifica los extremos de la aplicación puede escuchar en. En este ejemplo, la aplicación Node.js escucha en http en el puerto 3000.

Además puede solicitar tela de servicio para publicar este extremo del servicio de nombres para que otros servicios puedan detectar la dirección de extremo a este servicio. Esto le permite comunicarse entre los servicios que son ejecutables de invitado.
Es la dirección de extremo publicada del formulario `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme`y `PathSuffix` son atributos opcionales. `IPAddressOrFQDN`es la dirección IP o el nombre de dominio completo del nodo ejecutable se coloca en y se calcula para usted.

En el ejemplo siguiente una vez que se implementa el servicio, en el Explorador de tela de servicio verá un extremo similar a `http://10.1.4.92:3000/myapp/` publicado para la instancia de servicio o si se trata de un equipo local consulte `http://localhost:3000/myapp/`. 

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Puede usar estas direcciones con el [servidor proxy inverso](service-fabric-reverseproxy.md) para la comunicación entre servicios.

### <a name="edit-the-application-manifest-file"></a>Editar el archivo de manifiestos de aplicación

Una vez que haya configurado la `Servicemanifest.xml` archivo, debe realizar los cambios a la `ApplicationManifest.xml` archivo para asegurarse de que se utilizan el tipo correcto de servicio y nombre.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

En la `ServiceManifestImport` elemento, puede especificar uno o varios de los servicios que desea incluir en la aplicación. Se hace referencia a los servicios con `ServiceManifestName`, que especifica el nombre del directorio donde la `ServiceManifest.xml` archivo se encuentra.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Configurar el registro
Para invitados ejecutables, es útil poder ver registros de la consola para averiguar si las secuencias de comandos de configuración de aplicación y mostrar los errores.
Se puede configurar el redireccionamiento de la consola en el `ServiceManifest.xml` archivo mediante la `ConsoleRedirection` elemento.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection`puede usarse para redirigir el resultado de la consola (stdout y stderr) al directorio de trabajo para que se puedan utilizar para comprobar que no hay ningún error durante la instalación o la ejecución de la aplicación en el clúster de tela de servicio.

    * `FileRetentionCount`Determina cuántos archivos se guardan en el directorio de trabajo. Un valor de 5, por ejemplo, significa que se almacenan los archivos de registro para las cinco ejecuciones anterior en el directorio de trabajo.
    * `FileMaxSizeInKb`Especifica el tamaño máximo de los archivos de registro.

Archivos de registro se guardan en uno de los directorios de trabajo del servicio. Para determinar dónde se encuentran los archivos, debe usar el Explorador de tela de servicio para determinar qué nodo que se está ejecutando el servicio y directorio de trabajo que se está usando. Este proceso se trata más adelante en este artículo.

## <a name="deployment"></a>Implementación
El último paso es implementar la aplicación. El siguiente script de PowerShell muestra cómo implementar la aplicación en el clúster de desarrollo local e iniciar un nuevo servicio de tela de servicio.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Puede implementar un servicio de servicio tela en varias "configuraciones". Por ejemplo, se puede implementar como una o varias instancias o se puede implementar de manera que hay una instancia del servicio en cada nodo del clúster tela de servicio.

La `InstanceCount` parámetro de la `New-ServiceFabricService` cmdlet se usa para especificar cuántas instancias del servicio deben iniciarse en el clúster de servicio tela. Puede establecer la `InstanceCount` valor, en función del tipo de aplicación que está implementando. Los dos escenarios más comunes son:

* `InstanceCount = "1"`. En este caso, se implementa solo una instancia del servicio en el clúster. Programador de servicio tela determina qué nodo el servicio se va a implementar en.

* `InstanceCount ="-1"`. En este caso, se implementa una instancia del servicio en todos los nodos en el clúster de servicio tela. El resultado es tener una (y sólo una) instancia del servicio para cada nodo del clúster.

Esto es útil para aplicaciones (por ejemplo, un extremo REST) porque necesitan "conectarse" a cualquiera de los nodos en el clúster para usar el punto final de las aplicaciones cliente. Esta configuración también se puede utilizar cuando, por ejemplo, todos los nodos del clúster de servicio tela están conectados a un equilibrador de carga para que el tráfico del cliente puede distribuirse entre el servicio que se ejecuta en todos los nodos en el clúster.

## <a name="check-your-running-application"></a>Comprobar la aplicación en ejecución

En el Explorador de tela de servicio, identificar el nodo donde se ejecuta el servicio. En este ejemplo, se ejecuta en el nodo 1:

![Nodo donde se ejecuta el servicio](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Si desplácese hasta el nodo y vaya a la aplicación, vea la información de nodo esenciales, incluida su ubicación en el disco.

![Ubicación en el disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Si explora el directorio mediante el Explorador de servidor, puede encontrar el directorio de trabajo y la carpeta del servicio Registro como se muestra en la siguiente imagen.

![Ubicación del registro](./media/service-fabric-deploy-existing-app/loglocation.png)


## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido empaquetar un ejecutable invitado e implementar en tela de servicio. Como paso siguiente, puede comprobar contenido adicional de este tema.

- [Ejemplo para empaquetar e implementar a un invitado ejecutable en GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), incluyendo un vínculo a la versión preliminar de la herramienta de empaquetado
- [Implementar varios ejecutables de invitado](service-fabric-deploy-multiple-apps.md)
- [Crear su primera aplicación de servicio tela mediante Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
