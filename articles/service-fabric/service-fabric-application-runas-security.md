<properties
   pageTitle="Descripción de la aplicación de servicio tela y directivas de seguridad de servicio | Microsoft Azure"
   description="Información general sobre cómo ejecutar una aplicación de servicio tela en sistema y cuentas de seguridad local, incluido el punto de SetupEntry donde debe realizar alguna acción con privilegios antes de iniciar una aplicación"
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
   ms.date="09/22/2016"
   ms.author="mfussell"/>

# <a name="configure-security-policies-for-your-application"></a>Configurar directivas de seguridad de la aplicación
Azure tela de servicio proporciona la capacidad para proteger aplicaciones que se ejecutan en el clúster en cuentas de usuario diferentes. Servicio tela también protege los recursos utilizados por las aplicaciones en el momento de la implementación en la cuenta de usuario como archivos, directorios y certificados. Esto, aplicaciones en ejecución, incluso en un entorno de host compartido seguro entre sí. 

De forma predeterminada, las aplicaciones de servicio tela ejecutan en la cuenta que ejecuta el proceso de Fabric.exe bajo. Servicio tela también proporciona la capacidad de ejecutar aplicaciones en una cuenta de usuario local o la cuenta del sistema local, especificado en el manifiesto de la aplicación. Tipos de cuenta de sistema local compatible para son **usuarioLocal**, **NetworkService**, **servicio local**y **LocalSystem**.

 Cuando se ejecuta el servicio tela en Windows Server en el centro de datos utilizando al instalador independiente, puede usar las cuentas de dominio de Active Directory (AD).

Grupos de usuarios se puede definir y crean de forma que uno o más usuarios pueden agregarse a cada grupo que se administra conjuntamente. Esto es útil cuando hay varios usuarios de puntos de entrada de servicio diferente y que necesitan tener ciertos privilegios comunes que están disponibles en el nivel de grupo.

## <a name="configure-the-policy-for-service-setupentrypoint"></a>Configurar la directiva de servicio SetupEntryPoint

Como se describe en el [modelo de aplicación](service-fabric-application-model.md) de la **SetupEntryPoint** es un punto de entrada con privilegios que se ejecuta con las mismas credenciales como servicio tela (normalmente la cuenta *NetworkService* ) antes de cualquier otro punto de entrada. El archivo ejecutable especificado por el **punto de entrada** suele ser el host de servicio de larga duración, por lo que una entrada de instalación independiente apunten evita tener que ejecutar el host del servicio ejecutable con privilegios elevados para largos períodos de tiempo. El archivo ejecutable especificado por el **punto de entrada** se ejecuta cuando sale de **SetupEntryPoint** correctamente. El proceso resultante es supervisar y reinicie, partiendo de nuevo **SetupEntryPoint**, si alguna vez termina o se bloquea.

El siguiente es un ejemplo de manifiestos de servicio sencillo que muestra la SetupEntryPoint y el punto de entrada principal para el servicio.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### <a name="configure-the-policy-using-a-local-account"></a>Configurar la directiva mediante una cuenta local

Después de configurar el servicio para que el punto de entrada de un programa de instalación, puede cambiar los permisos de seguridad que se ejecute en manifiesto de aplicación. En el ejemplo siguiente se muestra cómo configurar el servicio para ejecutarse con privilegios de cuenta de usuario administrador.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

En primer lugar, cree una sección de **identidades** con un nombre de usuario, como SetupAdminUser. Esto indica que el usuario es miembro del grupo de administradores del sistema.

A continuación, en la sección **ServiceManifestImport** , configurar una directiva para aplicar a este principal a **SetupEntryPoint**. Esto indica a tela de servicio que, cuando se ejecuta el archivo **MySetup.bat** , debe ser RunAs con privilegios de administrador. Dado que tiene *no* aplica una directiva para el punto de entrada principal, el código **MyServiceHost.exe** se ejecuta en el sistema cuenta **NetworkService** . Esta es la cuenta predeterminada que se ejecutan todos los puntos de entrada de servicio como.

Vamos a agregar ahora el archivo MySetup.bat al proyecto de Visual Studio para probar los privilegios de administrador. En Visual Studio, haga clic en el proyecto de servicio y agregar un nuevo archivo llamado MySetup.bat.

A continuación, asegúrese de que el archivo MySetup.bat se incluye en el paquete de servicio. De forma predeterminada, no es. Seleccione el archivo, haga clic en para obtener el menú contextual y elija **Propiedades**. En el cuadro de diálogo Propiedades, asegúrese de que **copie en el directorio de salida** se establece para **Copiar si es posterior**. Ver la pantalla siguiente captura.

![CopyToOutput Visual Studio para el archivo por lotes de SetupEntryPoint][image1]

Ahora, abra el archivo MySetup.bat y agregue los siguientes comandos:

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

A continuación, compile e implemente la solución un clúster de desarrollo local.  Una vez que ha iniciado el servicio, tal como se muestra en el Explorador de tela de servicio, puede ver que la MySetup.bat es correcta de una de dos maneras. Abra un símbolo del sistema de PowerShell y escriba:

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

A continuación, anote el nombre del nodo donde el servicio se ha implementado e inicia en el Explorador de tela de servicio, por ejemplo, nodo 2. A continuación, vaya a la carpeta de trabajo de instancia de aplicación para buscar el archivo out.txt que muestra el valor de **TestVariable**. Por ejemplo, si este servicio se ha implementado nodo 2 y, después, puede ir a esta ruta de acceso para el **MyApplicationType**:

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  <a name="configure-the-policy-using-local-system-accounts"></a>Configurar la directiva con cuentas de sistema local
A menudo es preferible ejecutar el script de inicio con una cuenta de sistema local en lugar de una cuenta de administradores, tal como se muestra anterior. Ejecuta el RunAs directiva como administradores normalmente no funciona bien, ya que disponen de Control de acceso de usuario (UAC) habilitado de forma predeterminada. En estos casos, **se recomienda ejecutar el SetupEntryPoint como LocalSystem en lugar de un usuario local agregado al grupo de administradores**. En el ejemplo siguiente se muestra cómo establecer la SetupEntryPoint para que se ejecute como LocalSystem.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
~~~

##  <a name="launch-powershell-commands-from-a-setupentrypoint"></a>Comandos de PowerShell en una SetupEntryPoint de inicio
Para ejecutar PowerShell desde el punto de **SetupEntryPoint** , puede ejecutar **PowerShell.exe** en un archivo por lotes que apunta a un archivo de PowerShell. En primer lugar, agregue un archivo de PowerShell para el proyecto de servicio, como **MySetup.ps1**. Recuerde que debe establecer la propiedad *Copiar si es posterior* para que el archivo también se incluye en el paquete de servicio. En el ejemplo siguiente se muestra un archivo por lotes de ejemplo para iniciar un archivo de PowerShell denominado MySetup.ps1, que se establece una variable de entorno de sistema denominada **TestVariable**.


MySetup.bat para iniciar el archivo de PowerShell.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

En el archivo de PowerShell, agregue lo siguiente para establecer una variable de entorno del sistema:

~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

**Nota:** De forma predeterminada cuando se ejecuta el archivo por lotes se busca en la carpeta de la aplicación denominada **funciona** para los archivos. En este caso, cuando se ejecuta MySetup.bat queremos para encontrar el MySetup.ps1 en la misma carpeta, que es la carpeta de **paquete de código** de la aplicación. Para cambiar esta carpeta, establezca la carpeta de trabajo, tal como se muestra en la siguiente.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
~~~

## <a name="using-console-redirection-for-local-debugging"></a>Usar la redirección de consola de depuración local
En ocasiones es útil ver el resultado de la consola de la ejecución de una secuencia de comandos con fines de depuración. Para hacer esto puede establecer una directiva de redireccionamiento de consola, que se escribe el resultado en un archivo. El resultado del archivo se escribe en la carpeta de la aplicación denominada **registro** en el nodo donde la aplicación se ha implementado y ejecutar (consulte dónde encontrar esto en el ejemplo anterior).

**Nota: nunca** usar la directiva de redireccionamiento de consola en una aplicación implementada en producción, ya que esto puede afectar a la aplicación dé. **Solo** use esta opción para fines de depuración y desarrollo local.  

En el ejemplo siguiente se muestra cómo establecer la redirección de la consola con un valor de FileRetentionCount.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
~~~

Si cambia el archivo MySetup.ps1 para escribir un comando de **eco** , escribirá el archivo de salida con fines de depuración.

~~~
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
~~~

**Una vez haya depurar la secuencia de comandos, quitar inmediatamente esta directiva de redireccionamiento de consola**

## <a name="configure-policy-for-service-code-packages"></a>Configurar la directiva de paquetes de código de servicio 
En los pasos anteriores, ha visto cómo aplicar la directiva de RunAs a SetupEntryPoint. Echemos un vistazo un poco más en cómo crear a distintos principales que se pueden aplicar como directivas del servicio.

### <a name="create-local-user-groups"></a>Crear grupos de usuarios locales
Grupos de usuarios pueden definirse y crea que permitir a uno o más usuarios que se agregarán a un grupo. Esto es especialmente útil si hay varios usuarios de puntos de entrada de servicio diferente y necesitan tener ciertos privilegios comunes que están disponibles en el nivel de grupo. En el ejemplo siguiente se muestra un grupo local llamado **LocalAdminGroup** con privilegios de administrador. Dos usuarios, Customer1 y Customer2, se convierten en miembros de este grupo local.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### <a name="create-local-users"></a>Crear usuarios locales
Puede crear un usuario local que se pueden usar para proteger un servicio en la aplicación. Cuando un tipo de cuenta **usuarioLocal** se especifica en la sección principales del manifiesto de aplicación, servicio tela crea cuentas de usuario locales en equipos donde se implementa la aplicación. De forma predeterminada, estas cuentas no tienen los mismos nombres que los especificados en el manifiesto de aplicación (por ejemplo, Customer3 en el ejemplo siguiente). En su lugar, que se generan dinámicamente y tienen contraseñas aleatorias.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

### <a name="assign-policies-to-the-service-code-packages"></a>Asignar directivas a los paquetes de código de servicio
La sección **RunAsPolicy** para un **ServiceManifestImport** especifica la cuenta de la sección de principales que debe usarse para ejecutar un paquete de código. También se asocia paquetes de código del manifiesto de servicio con cuentas de usuario en la sección principales. Puede especificar para los puntos de entrada o el programa de instalación, o puede especificar `All` para aplicar a ambos. En el ejemplo siguiente se muestra distintas directivas que se aplica:

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Si no se especifica **EntryPointType** , el valor predeterminado se establece en EntryPointType = "Principal". Especificar **SetupEntryPoint** es especialmente útil cuando desea ejecutar determinado operación de instalación de privilegios elevados en una cuenta del sistema. Puede ejecutar el código de servicio real en una cuenta con privilegios inferior.

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>Aplicar una directiva predeterminada para todos los paquetes de código de servicio
La sección **DefaultRunAsPolicy** se usa para especificar una cuenta de usuario predeterminada para todos los paquetes de código que no tengan una específica **RunAsPolicy** definido. Si necesita la mayoría de los paquetes de código especificados en el manifiesto de servicio de una aplicación para que se ejecute en el mismo usuario, la aplicación solo puede definir una directiva de RunAs de forma predeterminada con esa cuenta de usuario. En el ejemplo siguiente se especifica que si un paquete de código no tiene un **RunAsPolicy** especificado, debe ejecutar el paquete de código en el **MyDefaultAccount** especificado en la sección principales.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~
### <a name="using-an-active-directory-domain-group-or-user"></a>Usar un grupo de dominio de Active Directory o un usuario
Para tela servicio instalado en Windows Server con el instalador independiente, puede ejecutar el servicio de las credenciales para una cuenta de grupo o usuario de Active Directory (AD). Nota: Se trata de Active Directory local dentro de su dominio y no es con Azure Active Directory (AAD). Mediante el uso de un usuario de dominio o grupo, a continuación, puede acceder a otros recursos en el dominio (por ejemplo, recursos compartidos de archivos) que se le ha concedido permisos.

En el ejemplo siguiente se muestra un usuario de AD llamado *usuario de prueba* con la contraseña cifrada con un certificado denominado *MyCert*del dominio. Puede usar el `Invoke-ServiceFabricEncryptText` comandos de Powershell para crear el texto de cifrado secreta. Consulte este artículo [administrar información confidencial en las aplicaciones de servicio tela](service-fabric-application-secret-management.md) para obtener más información acerca de cómo. La clave privada del certificado para descifrar la contraseña se debe implementar en el equipo local en un método de banda (en Azure a través de administrador de recursos). A continuación, al servicio tela implementa el paquete de servicio en el equipo, es posible descifrar el secreto y junto con el nombre de usuario, autenticar con AD para que se ejecute en dichas credenciales.

~~~
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
~~~


## <a name="assign-securityaccesspolicy-for-http-and-https-endpoints"></a>Asignar SecurityAccessPolicy para extremos HTTP y HTTPS
Si aplica una directiva de RunAs a un servicio y manifiesto del servicio declara recursos de extremo con el protocolo HTTP, debe especificar un **SecurityAccessPolicy** para garantizar que los puertos asignados a estos extremos correctamente son de la cuenta de usuario de RunAs se ejecuta el servicio de control de acceso. En caso contrario, **http.sys** no tiene acceso al servicio y recibe errores con llamadas desde el cliente. En el ejemplo siguiente se aplica a la cuenta de Customer3 a un extremo denominado **ServiceEndpointName**, que le da derechos de acceso completa.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

El extremo HTTPS, también debe indicar el nombre del certificado que se va a devolver al cliente. Puede hacerlo mediante **EndpointBindingPolicy**, con el certificado que se definen en una sección de certificados manifiesto de aplicación.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## <a name="a-complete-application-manifest-example"></a>Un ejemplo de manifiestos de aplicación completa
El siguiente manifiesto de aplicación muestra muchas de las configuraciones diferentes:

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes

* [Entender el modelo de aplicación](service-fabric-application-model.md)
* [Especificar recursos en un manifiesto de servicio](service-fabric-service-manifest-resources.md)
* [Implementar una aplicación](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
