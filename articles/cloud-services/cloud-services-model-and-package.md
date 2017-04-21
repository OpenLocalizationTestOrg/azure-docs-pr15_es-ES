<properties
    pageTitle="¿Qué es un modelo de servicio de nube y paquete | Microsoft Azure"
    description="Describe el paquete (.cspkg) en Azure y el modelo de servicio de nube (.csdef, .cscfg)"
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>
<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>

# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>¿Qué es el modelo de servicio de nube y cómo empaquetarla?
Se crea un servicio de nube de tres componentes, la definición de servicio _(.csdef)_, la configuración de servicio _(.cscfg)_y un paquete de servicio _(.cspkg)_. Archivos de **ServiceDefinition.csdef** y de **ServiceConfig.cscfg** basado en XML y describan la estructura del servicio de nube y cómo se configura; denominan en el modelo. El **ServicePackage.cspkg** es un archivo zip que se genera desde el **ServiceDefinition.csdef** y entre otras cosas, que contiene todas las dependencias necesarias basada en binario. Azure crea un servicio de nube desde la **ServicePackage.cspkg** y la **ServiceConfig.cscfg**.

Una vez que se ejecuta el servicio de nube en Azure, puede configurarlo a través del archivo **ServiceConfig.cscfg** , pero no se puede modificar la definición.

## <a name="what-would-you-like-to-know-more-about"></a>¿Qué desea saber más sobre?

* Deseo saber más acerca de los archivos [ServiceDefinition.csdef](#csdef) y [ServiceConfig.cscfg](#cscfg) .
* Ya conocer, me ofrecen [algunos ejemplos](#next-steps) de puedo configurar.
* Deseo crear la [ServicePackage.cspkg](#cspkg).
* Estoy usando Visual Studio y deseo...
    * [Crear un nuevo servicio de nube][vs_create]
    * [Volver a configurar un servicio de nube existente][vs_reconfigure]
    * [Implementar un proyecto de servicio de nube][vs_deploy]
    * [Escritorio remoto en una instancia de servicio de nube][remotedesktop]

<a name="csdef"></a>
## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
El archivo **ServiceDefinition.csdef** especifica la configuración que se usa Azure para configurar un servicio de nube. El [Esquema de definición del servicio de Azure (.csdef archivo)](https://msdn.microsoft.com/library/azure/ee758711.aspx) proporciona el formato permitido para un archivo de definición de servicio. En el ejemplo siguiente se muestra la configuración que se puede definir para los roles de Web y trabajador:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Puede hacer referencia a la [[esquema de definición de servicio]] para comprender mejor el esquema XML utilizado aquí, sin embargo, hay una explicación rápida de algunos de los elementos:

**Sitios**  
Contiene las definiciones de aplicaciones web o para sitios Web que se hospedan en IIS 7.

**InputEndpoints**  
Contiene las definiciones de extremos que se usan para ponerse en contacto con el servicio de nube.

**InternalEndpoints**  
Contiene las definiciones de los extremos de las instancias de función sirven para comunicarse con ellos.

**ConfigurationSettings**  
Contiene las definiciones de configuración para las características de una función específica.

**Certificados**  
Contiene las definiciones de los certificados que son necesarios para una función. En el ejemplo anterior se muestra un certificado que se usa para la configuración de Azure Connect.

**LocalResources**  
Contiene las definiciones de recursos de almacenamiento local. Un recurso de almacenamiento local es un directorio reservado en el sistema de archivos de la máquina virtual en el que se está ejecutando una instancia de una función.

**Importaciones**  
Contiene las definiciones de los módulos importados. En el ejemplo anterior se muestra los módulos de conexión a Escritorio remoto y conéctese de Azure.

**Inicio**  
Contiene las tareas que se ejecutan cuando se inicia el rol. Las tareas se definen en un archivo ejecutable o de cmd.



<a name="cscfg"></a>
## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
La configuración de la configuración de su servicio de nube depende de los valores en el archivo **ServiceConfiguration.cscfg** . Especifique el número de instancias que desea implementar cada rol en este archivo. Los valores de las opciones de configuración que haya definido en el archivo de definición de servicio se agregan al archivo de configuración de servicio. Las huellas digitales de los certificados de administración que están asociados con el servicio de nube también se agregan al archivo. El [Esquema de configuración de servicio de Azure (.cscfg archivo)](https://msdn.microsoft.com/library/azure/ee758710.aspx) proporciona el formato permitido para un archivo de configuración de servicio.

El archivo de configuración de servicio no se incluye con la aplicación, pero se ha cargado en Azure como un archivo independiente y se usa para configurar el servicio de nube. Puede cargar un nuevo archivo de configuración de servicio sin volver a implementar su servicio de nube. Los valores de configuración del servicio de nube pueden cambiarse mientras se ejecuta el servicio de nube. En el ejemplo siguiente se muestra la configuración que se puede definir para los roles de Web y trabajador:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Puede consultar el [Esquema de configuración de servicio](https://msdn.microsoft.com/library/azure/ee758710.aspx) para comprender mejor el esquema XML utilizado aquí, sin embargo, hay una explicación rápida de los elementos:

**Instancias**  
Configura el número de instancias de la función en ejecución. Para evitar que el servicio de nube potencialmente no esté disponible durante las actualizaciones, es recomendable que implementa más de una instancia de los roles de orientación de la web. Al hacer esto, cumple las directrices de [Contrato de nivel de cálculo de servicio (SLA) de Azure](http://azure.microsoft.com/support/legal/sla/), que garantiza 99,95% conectividad externa para roles a través de Internet, cuando se implementan dos o más instancias de la función de un servicio.

**ConfigurationSettings**  
Configura las opciones para las instancias de ejecución de una función. El nombre de la `<Setting>` elementos deben coincidir con las definiciones de configuración en el archivo de definición de servicio.

**Certificados**  
Configura los certificados que se usan en el servicio. En el ejemplo anterior se muestra cómo definir el certificado para el módulo de acceso remoto. El valor del atributo *huella digital* debe establecerse para la huella digital del certificado para usar.

<p/>

 >[AZURE.NOTE] La huella digital del certificado se puede agregar al archivo de configuración mediante un editor de texto o el valor que se puede agregar en la ficha **certificados** de la página de **Propiedades** de la función en Visual Studio.



## <a name="defining-ports-for-role-instances"></a>Definición de puertos de las instancias de función
Azure permite solo un punto de entrada a un rol de web. Esto significa que todo el tráfico se produce a través de una dirección IP. Puede configurar sus sitios Web para compartir un puerto configurando el encabezado de host para dirigir la solicitud a la ubicación correcta. También puede configurar las aplicaciones para escuchar los puertos conocidos en la dirección IP.

En el ejemplo siguiente se muestra la configuración para un rol de web con un sitio Web y la aplicación web. El sitio Web está configurada como la ubicación de entrada predeterminado en el puerto 80 y las aplicaciones web están configuradas para recibir las solicitudes de un encabezado de host alternativo se denomina "mail.mysite.cloudapp.net".

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" <mark>port="80"</mark> />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" <mark>hostheader="mail.mysite.cloudapp.net"</mark> />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Cambiar la configuración de una función
Puede actualizar la configuración de su servicio de nube mientras se ejecuta en Azure, sin tener que realizar el servicio sin conexión. Para cambiar la información de configuración, puede cargar un nuevo archivo de configuración o editar el archivo de configuración en su lugar y aplicarlo a su servicio en ejecución. Los siguientes cambios pueden realizarse a la configuración de un servicio:

- **Cambiar los valores de configuración**  
Cuando una configuración de cambios, puede elegir una instancia de la función aplicar el cambio, mientras la instancia está en línea o para la instancia de reciclaje correctamente y aplique el cambio mientras la instancia está desconectado.

- **Cambiar la topología de servicio de las instancias de función**  
Topología cambios no afectan instancias en ejecución, excepto donde se va a quitar una instancia. Todas las instancias restantes generalmente no es necesario ser reciclados; Sin embargo, puede elegir las instancias de función en respuesta a un cambio de topología de reciclaje.

- **Cambiar la huella digital de certificado**  
Solo puede actualizar un certificado cuando una instancia de rol está sin conexión. Si un certificado se agrega, eliminar o cambiar mientras una instancia de rol está en línea, Azure correctamente desconecta la instancia para actualizar el certificado y ponerlo en línea una vez completado el cambio.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Controlar los cambios de configuración con servicio de tiempo de ejecución de eventos
La [Biblioteca de tiempo de ejecución de Azure](https://msdn.microsoft.com/library/azure/mt419365.aspx) incluye el espacio de nombres [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx) , que proporciona clases para interactuar con el entorno de Azure desde el código que se ejecuta en una instancia de una función. La clase [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) define los siguientes eventos que se producen antes y después de un cambio de configuración:

- **Evento [Changing](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx)**  
Esto ocurre antes de que el cambio de configuración se aplica a una instancia especificada de una función que le da una oportunidad de abajo las instancias de la función si es necesario.
- **Evento [Changed](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx)**  
Ocurre después de que el cambio de configuración se aplica a una instancia de una función especificada.

> [AZURE.NOTE] Debido a cambios de certificado desconectar siempre las instancias de una función, no provocan los eventos RoleEnvironment.Changing o RoleEnvironment.Changed.

<a name="cspkg"></a>
## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Para implementar una aplicación como un servicio de nube en Azure, primero debe empaquetar la aplicación en el formato apropiado. Puede usar la herramienta de línea de comandos de **CSPack** (que se instala con el [SDK de Azure](https://azure.microsoft.com/downloads/)) para crear el archivo de paquete como alternativa a Visual Studio.

**CSPack** utiliza el contenido del archivo de configuración de servicio y archivo de definición de servicio para definir el contenido del paquete. **CSPack** genera un archivo de paquete de aplicación (.cspkg) que puede cargar en Azure a través del [portal de Azure](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). De forma predeterminada, el paquete se denomina `[ServiceDefinitionFileName].cspkg`, pero puede especificar un nombre diferente mediante la `/out` opción de **CSPack**.

**CSPack** se generalmente se encuentra en  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

>[AZURE.NOTE]
CSPack.exe (en windows) está disponible, ejecute el método abreviado de **Microsoft Azure símbolo** que se instala con el SDK.  
>  
>Ejecute el programa CSPack.exe por sí mismo para ver la documentación acerca de todas las posibles modificadores y los comandos.

<p />

>[AZURE.TIP]
Ejecutar el servicio de nube localmente en la **Microsoft Azure calcular Emulator**, use la opción de **/copyonly** que esta opción copia los archivos binarios de la aplicación a un diseño de directorio desde el que se pueden ejecutar en el emulador de cálculo.

### <a name="example-command-to-package-a-cloud-service"></a>Comando de ejemplo para empaquetar un servicio de nube
En el ejemplo siguiente se crea un paquete de aplicación que contiene la información para un rol web. El comando Especifica el archivo de definición de servicio para usar el directorio donde se encuentra archivos binarios, y el nombre del archivo de paquete.

    cspack [DirectoryName]\[ServiceDefinition]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /out:[OutputFileName]

Si la aplicación contiene una función web y una función de trabajo, se usa el siguiente comando:

    cspack [DirectoryName]\[ServiceDefinition]
           /out:[OutputFileName]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]

Donde las variables se definen como sigue:

| Variable                  | Valor |
| ------------------------- | ----- |
| \[DirectoryName\]         | Subdirectorio en el directorio raíz del proyecto que contiene el archivo .csdef del proyecto Azure.|
| \[ServiceDefinition\]     | El nombre del archivo de definición del servicio. De forma predeterminada, este archivo se denomina ServiceDefinition.csdef.  |
| \[OutputFileName\]        | El nombre del archivo de paquete generado. Normalmente, esto se establece en el nombre de la aplicación. Si no se especifica ningún nombre de archivo, se crea el paquete de aplicación como \[ApplicationName\].cspkg.|
| \[Nombre de función\]              | El nombre de la función según se define en el archivo de definición de servicio.|
| \[RoleBinariesDirectory] | La ubicación de los archivos binarios de la función.|
| \[VirtualPath\]           | Los directorios físicos para cada ruta de acceso virtual definido en la sección de sitios de la definición del servicio.|
| \[PhysicalPath\]          | Los directorios físicos del contenido de cada ruta virtual definido en el nodo de sitio de la definición del servicio.|
| \[RoleAssemblyName\]      | El nombre del archivo binario para la función.| 


## <a name="next-steps"></a>Pasos siguientes

Voy a crear un paquete de servicio de nube y deseo...

* [Configuración de escritorio remoto para una instancia de servicio de nube][remotedesktop]
* [Implementar un proyecto de servicio de nube][deploy]

Estoy usando Visual Studio y deseo...

* [Crear un nuevo servicio de nube][vs_create]
* [Volver a configurar un servicio de nube existente][vs_reconfigure]
* [Implementar un proyecto de servicio de nube][vs_deploy]
* [Configuración de escritorio remoto para una instancia de servicio de nube][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop.md
[vs_remote]: ../vs-azure-tools-remote-desktop-roles.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
