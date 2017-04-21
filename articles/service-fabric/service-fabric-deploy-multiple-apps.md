<properties
   pageTitle="Implementar una aplicación de Node.js que usa MongoDB | Microsoft Azure"
   description="Tutorial acerca de cómo empaquetar varios ejecutables invitado para implementar un clúster de tela de servicio de Azure"
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
   ms.workload="NA"
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>


# <a name="deploy-multiple-guest-executables"></a>Implementar varios ejecutables de invitado

Este artículo le muestra cómo empaquetar e implementar varios ejecutables de invitado en tela de servicio de Azure. Para crear e implementar un paquete de servicio tela único Lea cómo [implementar invitado ejecutable de tela de servicio](service-fabric-deploy-existing-app.md).

Aunque este tutorial muestra cómo implementar una aplicación con un front-end Node.js que usa MongoDB como el almacén de datos, puede aplicar los pasos a cualquier aplicación que tenga dependencias en otra aplicación.   

Puede usar Visual Studio para generar el paquete de aplicación que contiene varios ejecutables de invitado. Consulte [Usar Visual Studio para empaquetar una aplicación existente](service-fabric-deploy-existing-app.md#using-visual-studio-to-package-an-existing-executable). Después de haber agregado el primer ejecutable de invitado, haga clic con el botón secundario en el proyecto de aplicación y seleccione la **Agregar -> tela de servicio nuevo servicio** para agregar el segundo proyecto ejecutable invitado a la solución. Nota: Si elige vincular el origen del proyecto de Visual Studio, generar la solución de Visual Studio, se asegurará que el paquete de aplicación está actualizado con los cambios en el origen. 

## <a name="manually-package-the-multiple-guest-executable-application"></a>Empaquetar manualmente la aplicación ejecutable invitado múltiples
También puede empaquetar manualmente el invitado ejecutable. Para el embalaje manual, este artículo utiliza la herramienta de empaquetado de tela de servicio, que está disponible en [http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Empaquetar la aplicación Node.js
En este artículo se supone que Node.js no está instalado en los nodos en el clúster de servicio tela. Por consiguiente, debe agregar Node.exe al directorio de raíz de la aplicación de nodo antes de empaquetar. La estructura de directorios de la aplicación Node.js (con el marco de trabajo de Express web y motor de plantilla Jade) debe ser similar a la siguiente:

```
|-- NodeApplication
  	|-- bin
        |-- www
  	|-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
  	|-- public
        |-- images
        |-- etc.
  	|-- routes
        |-- index.js
        |-- users.js
  	|-- views
        |-- index.jade
        |-- etc.
  	|-- app.js
  	|-- package.json
  	|-- node.exe
```

Como paso siguiente, cree un paquete de aplicación de la aplicación Node.js. El código siguiente crea un paquete de aplicación de servicio tela que contiene la aplicación Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

A continuación encontrará una descripción de los parámetros que se utilizan:

- **/Source** apunta al directorio de la aplicación que se debe empaquetar.
- **/ target** define el directorio en el que se debe crear el paquete. Este directorio tiene que ser diferentes desde el directorio de origen.
- **/ AppName** define el nombre de la aplicación de la aplicación existente. Es importante entender esto traduce el nombre de servicio en el manifiesto y no en el nombre de la aplicación de servicio tela.
- **/exe** define el archivo ejecutable que se supone que tela de servicio de inicio, en este caso `node.exe`.
- **/maen** define el argumento que se utiliza para iniciar el ejecutable. Como Node.js no está instalado, tela de servicio debe iniciar el servidor de web Node.js ejecutando `node.exe bin/www`.  `/ma:'bin/www'`indica a la herramienta de empaquetado usar `bin/ma` como argumento para node.exe.
- **/AppType** define el nombre del tipo de aplicación de servicio tela.

Si vaya al directorio que se especificó en el inicio de sesión del, puede ver que la herramienta ha creado un paquete de servicio tela totalmente funcional tal como se muestra a continuación:

```
|--[yourtargetdirectory]
  	|-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
El ServiceManifest.xml generado tiene ahora una sección que describe cómo se debe iniciar el servidor de web Node.js, como se muestra en el fragmento de código siguiente:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
En este ejemplo, el servidor de web Node.js escucha al puerto 3000, por lo que deberá actualizar la información del extremo en el archivo ServiceManifest.xml tal como se muestra a continuación.   

```xml
<Resources>
      <Endpoints>
        <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Empaquetar la aplicación MongoDB
Ahora que ha empaquetado la aplicación Node.js, puede continuar y empaquetar MongoDB. Como se mencionó anteriormente, los pasos que atraviesan ahora no son específicos de Node.js y MongoDB. De hecho, se aplican a todas las aplicaciones que están diseñadas para empaquetar como una aplicación de servicio tela.  

Para empaquetar MongoDB, desea asegurarse de que empaquetar Mongod.exe y Mongo.exe. Ambos archivos binarios se encuentran en la `bin` directorio de su directorio de instalación de MongoDB. La estructura de directorios es similar a la siguiente.

```
|-- MongoDB
  	|-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Tela de servicio debe comenzar MongoDB con un comando similar al siguiente, por lo que debe usar la `/ma` parámetro al empaquetar MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE] Los datos no se conservan en el caso de un error de nodo si coloca el directorio de datos MongoDB en el directorio local del nodo. Debe usar almacenamiento resistente o implementar una réplica de MongoDB establecer para evitar la pérdida de datos.  

En o el shell de comandos de PowerShell, ejecutar la herramienta de empaquetado con los parámetros siguientes:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Para agregar MongoDB para el paquete de aplicación de servicio tela, debe asegurarse de que los puntos de parámetro/Target en el mismo directorio que ya contiene la aplicación manifiestos junto con la aplicación Node.js. También debe asegurarse de que está usando el mismo nombre de ApplicationType.

¡Vaya al directorio y examinar lo que ha creado la herramienta.

```
|--[yourtargetdirectory]
  	|-- MyNodeApplication
  	|-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
Como puede ver, la herramienta agrega una nueva carpeta, MongoDB, en el directorio que contiene los archivos binarios de MongoDB. Si abre la `ApplicationManifest.xml` archivo, puede ver que el paquete contiene ahora la aplicación de Node.js y MongoDB. El código siguiente muestra el contenido del manifiesto de aplicación.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>La aplicación de publicación
El último paso es publicar la aplicación en el clúster de servicio tela local mediante las secuencias de comandos de PowerShell siguiente:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Una vez que la aplicación se ha publicado correctamente en el clúster local, puede tener acceso a la aplicación Node.js en el puerto que se ha introducido en la información de servicio de la aplicación Node.js, por ejemplo http://localhost:3000.

En este tutorial, ha visto cómo empaquetar fácilmente dos aplicaciones existentes como una aplicación de servicio tela. También ha aprendido a implementarlo en tela de servicio para que pueden beneficiarse de algunas de las características de tela de servicio, como alta disponibilidad y la integración de sistema de salud.

## <a name="next-steps"></a>Pasos siguientes

- Obtener información acerca de cómo implementar contenedores con [información general de tela de servicio y contenedores](service-fabric-containers-overview.md)
