<properties
    pageTitle="Con SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory para las aplicaciones de | Microsoft Azure"
    description="Azure Active Directory pueden aprovisionar automáticamente los usuarios y grupos a cualquier aplicación o identidad almacén que está representado por un servicio Web con la interfaz definida en la especificación de protocolo SCIM"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="using-scim-to-enable-automatic-provisioning-of-users-and-groups-from-azure-active-directory-to-applications"></a>Con SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory para las aplicaciones

##<a name="overview"></a>Información general

Azure Active Directory pueden aprovisionar automáticamente los usuarios y grupos a cualquier aplicación o identidad almacén que está representado por un servicio Web con la interfaz definida en la [especificación de protocolo SCIM 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). Azure Active Directory pueden enviar solicitudes para crear, modificar y eliminar usuarios y grupos para este servicio Web, que, a continuación, se puede traducir dichas solicitudes a las operaciones de la tienda de identidad de destino asignados. 

![][1]
*Ilustración: Aprovisionamiento de Azure Active Directory para un almacén de identidades a través de un servicio Web*

Esta capacidad puede usarse junto con la capacidad de "[Traer su propia aplicación](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)" en Azure AD para habilitar el inicio de sesión único y aprovisionamiento de aplicaciones que proporcionan o representadas por un servicio web SCIM automática de usuario.

Hay dos casos de uso para SCIM de Azure Active Directory:

* **Aprovisionamiento de usuarios y grupos para las aplicaciones que son compatibles con SCIM** - aplicaciones que admiten SCIM 2.0 y utilizar tokens portador de OAuth para la autenticación funcionarán con Azure AD del cuadro.

* **Crear su propia solución de aprovisionamiento para las aplicaciones que admitan otros basada en API** - para aplicaciones no SCIM, puede crear un extremo SCIM para traducir entre Azure AD extremo SCIM y lo que es compatible con la aplicación de la API para el aprovisionamiento de usuario.  Para facilitar el desarrollo de un extremo SCIM, le proporcionamos bibliotecas de CLI junto con ejemplos de código que muestran cómo proporcionar un extremo SCIM y traducir mensajes SCIM.  

##<a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Aprovisionamiento de usuarios y grupos para las aplicaciones que son compatibles con SCIM

Se pueden configurar Azure Active Directory aprovisionamiento asignado automáticamente de los usuarios y grupos para las aplicaciones que implementan un sitio Web de [sistema de administración de identidades entre dominios 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) del servicio y aceptan tokens portador de OAuth para la autenticación. Dentro de la especificación SCIM 2.0, las aplicaciones deben cumplir estos requisitos:

* Es compatible con la creación de usuarios o grupos, según la sección 3.3 del protocolo SCIM.  

* Es compatible con la modificación de los usuarios o grupos con solicitudes de revisión según la sección 3.5.2 del protocolo SCIM.  

* Admite la recuperación de un recurso conocido punto 3.4.1 del protocolo SCIM.  

*  Es compatible con consultas de usuarios o grupos, como por sección 3.4.2 del protocolo SCIM.  De forma predeterminada, por ExternalID no se consultan con los usuarios y grupos se consultan displayName.  

* Es compatible con la consulta de usuario por ID y por el Administrador de acuerdo con el punto 3.4.2 del protocolo SCIM.  

* Es compatible con consultar grupos por ID y por miembro de acuerdo con el punto 3.4.2 del protocolo SCIM.  

* Acepta tokens portador de OAuth de autorización de acuerdo con el punto 2.1 del protocolo SCIM.

Debe comprobar con su proveedor de aplicación o la documentación de su proveedor de aplicación para las instrucciones de la compatibilidad con estos requisitos.
 
###<a name="getting-started"></a>Introducción

Aplicaciones que son compatibles con el perfil SCIM descrito anteriormente se pueden conectar a Azure Active Directory mediante la característica de aplicación "personalizada" en la Galería de la aplicación de Azure AD. Una vez conectado, Azure AD ejecuta un proceso de sincronización cada 5 minutos donde consulta extremo SCIM de la aplicación para usuarios y grupos, asignados y crea o modifica según los detalles de la asignación.

**Para conectar una aplicación que admita SCIM:**

1.  En un explorador web, abra el portal de administración de Azure en https://manage.windowsazure.com.
2.  Vaya a **Active Directory > directorio > [su directorio] > aplicaciones**y seleccione **Agregar > Agregar una aplicación desde la Galería de**.
3.  Seleccione la pestaña **personalizado** a la izquierda, escriba un nombre para la aplicación y haga clic en el icono de marca de verificación para crear un objeto de la aplicación.

![][2]

4.  En la pantalla resultante, seleccione el segundo botón de **aprovisionamiento de cuentas de configurar** .
5.  En el campo **Dirección URL del extremo de aprovisionamiento** , escriba la dirección URL del extremo SCIM de la aplicación.
6.  Si el extremo SCIM requiere un token portador de OAuth de un emisor que no sea de Azure AD, a continuación, copie el token portador de OAuth necesario en el campo **Token de autenticación (opcional)** . Es este campo está en blanco y luego Azure AD incluirá un token portador de OAuth emitido de Azure AD con cada solicitud. Aplicaciones que utilicen Azure AD como un proveedor de servicios de idenity puede validar este Azure AD-emitido token.
7.  Haga clic en **siguiente**y haga clic en el botón **Iniciar prueba** tener Azure Active Directory intenta conectarse al extremo SCIM. Si los intentos de un error, se mostrará información de diagnóstico.  
8.  Si intenta conectar con succeed de aplicación, a continuación, haga clic en **siguiente** en el restante pantallas y haga clic en **completado** para salir del cuadro de diálogo.
9.  En la pantalla resultante, seleccione el tercer botón **Asignar cuentas** . En la sección usuarios y grupos resultante, asignar los usuarios o grupos que desee a disposición a la aplicación.
10. Una vez que se asignan los usuarios y grupos, haga clic en la ficha **Configurar** cerca de la parte superior de la pantalla.
11. En **El aprovisionamiento de cuenta**, compruebe que el estado se establece en. 
12. En **Herramientas**, haga clic en **reiniciar el aprovisionamiento de cuenta** para iniciar el proceso de aprovisionamiento.

Tenga en cuenta que puede transcurrir 5 y 10 minutos antes de que el proceso de aprovisionamiento comenzará a enviar las solicitudes al extremo SCIM.  Se proporciona un resumen de intentos de conexión en la pestaña del panel de la aplicación y un informe de aprovisionamiento de la actividad y los errores de aprovisionamiento pueden descargarse desde la ficha de informes del directorio.

##<a name="building-your-own-provisioning-solution-for-any-application"></a>Crear su propia solución de aprovisionamiento de cualquier aplicación

Mediante la creación de un servicio web SCIM que interactúa con Azure Active Directory, puede habilitar el único usuario de inicio de sesión y automática de aprovisionamiento de prácticamente cualquier aplicación que proporciona una API de aprovisionamiento de usuarios resto o SOAP.

Así es cómo funciona:

1.  Azure AD proporciona una biblioteca de infraestructura de lenguaje común denominada [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Los programadores e integradores pueden usar esta biblioteca para crear e implementar un extremo de servicio web basadas en SCIM puede conectarse Azure AD al almacén de identidades de la aplicación.
2.  Las asignaciones se ha implementado en el servicio web para asignar el esquema de usuario estándar en el esquema de usuario y el protocolo de la aplicación.
3.  La dirección URL del extremo está registrada en Azure AD como parte de una aplicación personalizada en la Galería de aplicación.
4.  Usuarios y grupos se asignan a esta aplicación en Azure AD. Tras la asignación, se colocan en cola para sincronizarse con la aplicación de destino. El proceso de sincronización controla la cola se ejecutará cada 5 minutos.

###<a name="code-samples"></a>Ejemplos de código

Para facilitar este proceso, un conjunto de [ejemplos de código](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) son siempre crear un extremo de servicio web SCIM y demostrar aprovisionamiento automático. Es un ejemplo de un proveedor que mantiene un archivo con filas de valores separados por comas que representan los usuarios y grupos.  La otra es de un proveedor que funciona en el servicio de administración de acceso y la identidad de servicios Web de Amazon.  

**Requisitos previos**

* Visual Studio 2013 o posterior
* [Azure SDK para .NET](https://azure.microsoft.com/downloads/)
* Equipo de Windows que admite la ASP.NET framework 4.5 que se usará como el extremo SCIM. Este equipo debe ser accesible desde la nube
* [Una suscripción de Azure con una versión de prueba o con licencia de Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* El ejemplo de Amazon AWS requiere bibliotecas de [AWS Kit de herramientas de Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Vea el archivo Léame que se incluyen con el ejemplo para obtener más información

###<a name="getting-started"></a>Introducción

La manera más sencilla de implementar un extremo SCIM que puede aceptar solicitudes de aprovisionamiento de Azure AD es Compile e implemente el código de ejemplo que muestra los resultados los usuarios aprovisionados a un archivo de valores separados por comas (CSV).

**Para crear un extremo SCIM de ejemplo:**

1.  Descargar el paquete de ejemplo de código en [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2.  Descomprima el paquete y colóquelo en el equipo de Windows en una ubicación como C:\AzureAD-BYOA-Provisioning-Samples\.
3.  En esta carpeta, abra la solución FileProvisioningAgent en Visual Studio.
4.  Seleccione **Herramientas > Administrador de paquetes de biblioteca > consola del Administrador de paquete**y ejecute los siguientes comandos para el proyecto FileProvisioningAgent resolver las referencias de solución:

    Paquete de instalación de Microsoft.IdentityModel.Clients.ActiveDirectory paquete de instalación de Microsoft.Owin.Diagnostics Microsoft.Owin.Host.SystemWeb de paquete de instalación Microsoft.SystemForCrossDomainIdentityManagement paquete de instalación

5.  Genere el proyecto FileProvisioningAgent.
6.  Iniciar la aplicación de símbolo del sistema de Windows (como administrador) y utilice el comando **cd** para cambiar el directorio a la carpeta **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** .
7.  Ejecute el comando siguiente, reemplazando < dirección ip > con el nombre de dominio o IP del equipo de Windows.

    FileAgnt.exe http://<ip-address>:9000 TargetFile.csv

8.  En Windows en **configuración de Windows > configuración de Internet y de red**, seleccione la **Firewall de Windows > Configuración avanzada de**y cree una **Regla de entrada** que permite el acceso entrante al puerto 9000.
9.  Si el equipo de Windows está detrás de un enrutador, el enrutador tendrán que estar configurado para realizar la traducción de acceso de red entre el puerto 9000 que se exponen en internet y puerto 9000 en el equipo de Windows. Esto es necesario para Azure AD puedan tener acceso a este extremo en la nube.


**Para registrar el extremo SCIM de ejemplo en Azure AD:**

1.  En un explorador web, abra el portal de administración de Azure en https://manage.windowsazure.com.
2.  Vaya a **Active Directory > directorio > [su directorio] > aplicaciones**y seleccione **Agregar > Agregar una aplicación desde la Galería de**.
3.  Seleccione la pestaña **personalizado** a la izquierda, escriba un nombre, como "Aplicación de prueba de SCIM" y haga clic en el icono de marca de verificación para crear un objeto de la aplicación. Tenga en cuenta que creó el objeto de la aplicación se va a representar la aplicación de destino que desea aprovisionamiento a y aplicación de inicio de sesión único para y no solo en el extremo SCIM.

![][2]

4.  En la pantalla resultante, seleccione el segundo botón de **aprovisionamiento de cuentas de configurar** .
5.  En el cuadro de diálogo, escriba la dirección URL y puerto del extremo SCIM expuesta a internet. Esto sería algo como http://testmachine.contoso.com:9000 o http://<ip-address>:9000/, donde < dirección ip > es internet expuestas IP dirección.  
6.  Haga clic en **siguiente**y haga clic en el botón **Iniciar prueba** tener Azure Active Directory intenta conectarse al extremo SCIM. Si los intentos de un error, se mostrará información de diagnóstico.  
7.  Si intenta conectar con el servicio Web se realiza correctamente, haga clic en **siguiente** en las pantallas restantes y haga clic en **completado** para salir del cuadro de diálogo.
8.  En la pantalla resultante, seleccione el tercer botón **Asignar cuentas** . En la sección usuarios y grupos resultante, asignar los usuarios o grupos que desee a disposición a la aplicación.
9.  Una vez que se asignan los usuarios y grupos, haga clic en la ficha **Configurar** cerca de la parte superior de la pantalla.
10. En **El aprovisionamiento de cuenta**, compruebe que el estado se establece en. 
11. En **Herramientas**, haga clic en **reiniciar el aprovisionamiento de cuenta** para iniciar el proceso de aprovisionamiento.

Tenga en cuenta que puede transcurrir 5 y 10 minutos antes de que el proceso de aprovisionamiento comenzará a enviar las solicitudes al extremo SCIM.  Se proporciona un resumen de intentos de conexión en la pestaña del panel de la aplicación y un informe de aprovisionamiento de la actividad y los errores de aprovisionamiento pueden descargarse desde la ficha de informes del directorio.

El paso final en la comprobación de la muestra es abrir el archivo TargetFile.csv en la carpeta \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug en el equipo de Windows. Después de ejecuta el proceso de aprovisionamiento, este archivo muestra los detalles de todos los asignan y aprovisionado usuarios y grupos.

###<a name="development-libraries"></a>Bibliotecas de desarrollo

Para desarrollar su propio servicio Web que se ajusta a la especificación de SCIM, familiarizarse con las siguientes bibliotecas proporcionadas por Microsoft para ayudar a acelerar el proceso de desarrollo: 

**1:**  Bibliotecas comunes de infraestructura de idioma están disponibles para usar con idiomas basados en esa infraestructura, como C#.  Una de esas bibliotecas, [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), declara una interfaz Microsoft.SystemForCrossDomainIdentityManagement.IProvider, que se muestra en la siguiente ilustración.  Un desarrollador con las bibliotecas implemente esa interfaz con una clase que se puede hacer referencia, de forma general, como un proveedor.  Las bibliotecas permiten a los desarrolladores implementar fácilmente un servicio Web que se ajusta a la especificación de SCIM, ya sea alojado en Internet Information Services o cualquier ensamblado ejecutable de infraestructura de lenguaje común.  Las solicitudes de servicio Web se traducirá en llamadas a métodos del proveedor, que debería programar por el programador para que funcione en algunos almacén de identidades.    

![][3]

**2:** [Controladores de ruta Express](http://expressjs.com/guide/routing.html) están disponibles para analizar los objetos de solicitud de node.js que representa llamadas (tal como se define en la especificación de SCIM), realizados en un servicio Web node.js.     

###<a name="building-a-custom-scim-endpoint"></a>Creación de un extremo SCIM personalizado

Usa las bibliotecas que se describió anteriormente, los desarrolladores con esas bibliotecas pueden hospedar sus servicios en cualquier ensamblado ejecutable de infraestructura de lenguaje común, o en Internet Information Services.  Aquí es el código de ejemplo de un servicio dentro de un ensamblado ejecutable, en la dirección http://localhost: 9000 de hospedaje: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  
    
    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Es importante que tenga en cuenta que este servicio debe tener un HTTP dirección y servidor certificado de autenticación de que la entidad emisora de certificados raíz es uno de estos procedimientos: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Un certificado de autenticación de servidor puede estar enlazado a un puerto en un host de Windows mediante la utilidad de shell de red, así: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  
 
En este caso, el valor proporcionado por el argumento certhash es la huella digital del certificado, mientras que el valor proporcionado por el argumento ID es un identificador único global arbitrario.  

Para hospedar el servicio de Internet Information Services, un desarrollador crearía un ensamblado de biblioteca de código de infraestructura de lenguaje común con una clase denominada inicio en el espacio de nombres predeterminado del ensamblado.  Aquí es un ejemplo de este tipo de clase: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

###<a name="handling-endpoint-authentication"></a>Autenticación de extremo de control

Solicitudes de Azure Active Directory incluyen un token portador de OAuth 2.0.   Cualquier servicio de recepción de la solicitud debe autenticar al emisor como Azure Active Directory en nombre del inquilino de Azure Active Directory esperado, para obtener acceso al servicio Web de gráfico de Azure Active Directory.  En el símbolo, el emisor se identifica por una notificación de iss, como "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  En este ejemplo, la dirección base del valor de notificación, https://sts.windows.net, identifica Azure Active Directory como el emisor, mientras que el segmento de dirección relativa, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, es un identificador único del inquilino de Azure Active Directory en cuyo nombre fue emitido el token.  Si el token emitido para tener acceso al servicio Web de gráfico de Azure Active Directory, el identificador de ese servicio, 00000002-0000-0000-c000-000000000000, debería estar en el valor de y notificación del token.  

Los desarrolladores que usan las bibliotecas de infraestructura de lenguaje común proporcionadas por Microsoft para la creación de un servicio SCIM pueden autenticar solicitudes de Azure Active Directory con el paquete de Microsoft.Owin.Security.ActiveDirectory siguiendo estos pasos: 

**1:**  En un proveedor, implemente la propiedad Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior estableciendo que devolver un método al que se llama siempre que se inicia el servicio: 

    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }

**2:**  Agregue el código siguiente a ese método tener cualquier solicitud de cualquiera de los extremos del servicio autenticados como teniendo un token emitido por Azure Active Directory en nombre de un inquilino especificado, para obtener acceso al servicio Web de gráfico de Azure Active Directory: 

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }

##<a name="user-and-group-schema"></a>Esquemas de grupo y de usuario

Azure Active Directory pueden aprovisionar dos tipos de recursos para los servicios Web SCIM.  Los tipos de recursos son los usuarios y grupos.  

Recursos de usuario se identifican mediante el identificador de esquema, urn: ietf:params:scim:schemas:extension:enterprise:2.0:User, que se incluye en esta especificación de protocolo: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  La asignación predeterminada de los atributos de los usuarios de Azure Active Directory los atributos de recursos urn: ietf:params:scim:schemas:extension:enterprise:2.0:User se proporciona en la tabla 1, a continuación.  

Agrupar los recursos identificados por el identificador de esquema, http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Tabla 2, se muestra la asignación predeterminada de los atributos de grupos de Azure Active Directory para los atributos de recursos de http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  

###<a name="table-1-default-user-attribute-mapping"></a>La tabla 1: Asignación de atributos de usuario predeterminado

| Usuario de Azure Active Directory | urn: ietf:params:scim:schemas:extension:enterprise:2.0:User |
| ------------- | ------------- |
| IsSoftDeleted | Active |
| displayName | displayName |
| TelephoneNumber de fax | .valoran phoneNumbers [tipo EC "fax"] |
| givenName | name.givenName |
| Puesto temporal | título |
| correo | .valoran correos electrónicos [tipo EC "trabajo"] |
| mailNickname | ExternalID no |
| Administrador | Administrador |
| móvil | .valoran phoneNumbers [tipo EC "móvil"] |
| Id. de objeto | Id. |
| código postal | .postalCode de direcciones [tipo EC "trabajo"] |
| Direcciones del proxy | correos electrónicos [Escriba EC "otro"]. Valor |
| OfficeName de entrega de físico | direcciones [Escriba EC "otro"]. Con formato |
| Dirección | .streetAddress de direcciones [tipo EC "trabajo"] |
| apellido | name.familyName |
| Número de teléfono | .valoran phoneNumbers [tipo EC "trabajo"] |
| usuario PrincipalName | nombre de usuario |


###<a name="table-2-default-group-attribute-mapping"></a>Tabla 2: Asignación de atributos de grupo predeterminado

| Grupo de Azure Active Directory | http://schemas.Microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| ------------- | ------------- |
| displayName | ExternalID no |
| correo | .valoran correos electrónicos [tipo EC "trabajo"] |
| mailNickname | displayName |
| miembros | miembros |
| Id. de objeto | Id. |
| proxyAddresses | correos electrónicos [Escriba EC "otro"]. Valor |


##<a name="user-provisioning-and-de-provisioning"></a>Aprovisionamiento de usuario y eliminar

La figura siguiente muestra los mensajes que Azure Active Directory enviará a un servicio SCIM para administrar el ciclo de vida de un usuario de otra identidad almacenan.  El diagrama también muestra cómo un servicio SCIM implementado mediante las bibliotecas de infraestructura de lenguaje común proporcionadas por Microsoft para la creación de dichos servicios traducirá las solicitudes en llamadas a los métodos de un proveedor.  

![][4]
*Ilustración: Aprovisionamiento de usuarios y anular el aprovisionamiento de secuencia*

**1:**  Azure Active Directory consultará el servicio para un usuario con un valor de atributo ExternalID no coincide con el valor de atributo mailNickname de un usuario de Azure Active Directory.  La consulta se expresarse como una solicitud de protocolo de transferencia de hipertexto como este, donde jyoung es un ejemplo de un mailNickname de un usuario de Azure Active Directory: 

    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...

Si el servicio se creó con las bibliotecas de infraestructura de lenguaje común proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se convertirá en una llamada al método de consulta de proveedor del servicio.  Esta es la firma de ese método: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);

Esta es la definición de la interfaz de Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 

    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }
    
    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }

En el caso del ejemplo anterior de una consulta para un usuario con un valor determinado para el atributo ExternalID no, valores de los argumentos pasados al método consulta será como sigue: 

* parámetros. AlternateFilters.Count: 1
* parámetros. AlternateFilters.ElementAt(0). AttributePath: ExternalID "no"
* parámetros. AlternateFilters.ElementAt(0). ComparisonOperator: ComparisonOperator.Equals
* parámetros. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin. ID"] 

**2:**  Si la respuesta a una consulta para el servicio para un usuario con un valor de atributo ExternalID no coincide con el valor de atributo mailNickname de un usuario de Azure Active Directory no devuelve todos los usuarios, Azure Active Directory solicitará que el servicio de aprovisionamiento de un usuario correspondiente a uno de Azure Active Directory.  Aquí tiene un ejemplo de la solicitud: 

    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}

Las bibliotecas de infraestructura de lenguaje común proporcionadas por Microsoft para implementar servicios SCIM traducen solicitud en una llamada al método Create del proveedor del servicio.  El método Create tiene esta firma: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);

En el caso de una solicitud de aprovisionamiento de un usuario, el valor del argumento recursos será una instancia de la Microsoft.SystemForCrossDomainIdentityManagement. Clase Core2EnterpriseUser, definido en la biblioteca de Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Si la solicitud proporcionando el usuario se realiza correctamente, vuelva a la implementación del método debe devolver una instancia de la la Microsoft.SystemForCrossDomainIdentityManagement. Clase Core2EnterpriseUser, con el valor de la propiedad de identificador establecida en el identificador único del usuario recién se aprovisione.  

**3:**  Para actualizar un usuario que sabe que existe en un almacén de identidades representado por una SCIM, Azure Active Directory continuará solicitando el estado actual de ese usuario del servicio con una solicitud como este: 

    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...

En un servicio integrado con las bibliotecas de infraestructura de lenguaje común proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se convertirá a una llamada al método recuperar del proveedor de servicio.  Esta es la firma del método recuperar: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);
    
    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }

En el caso de ejemplo anterior de una solicitud para recuperar el estado actual de un usuario, los valores de las propiedades del objeto proporcionado como el valor del argumento parámetros serán como sigue: 

* Identificador: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

**4:**  Si es un atributo de referencia que se actualizará, Azure Active Directory se consultará el servicio para determinar si el valor actual del atributo de referencia en el almacén de identidades representado por el servicio ya coincide con el valor del atributo en Azure Active Directory.  En el caso de los usuarios, el atributo solo de las cuales se van a consultar el valor actual de esta forma es el atributo de administrador.  Aquí tiene un ejemplo de una solicitud para determinar si el atributo de administrador de un objeto de usuario en particular actualmente tiene un valor determinado: 

    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...

El valor del parámetro de consulta de atributos, el identificador, significa que si existe un objeto de usuario que satisface la expresión proporcionada como el valor del parámetro de consulta de filtro, a continuación, el servicio se espera para responder con un recurso urn: ietf:params:scim:schemas:core:2.0:User o urn: ietf:params:scim:schemas:extension:enterprise:2.0:User, incluyendo solo el valor de atributo de id de ese recurso.  Por supuesto, se conoce el valor del atributo id al solicitante, se incluye en el valor del parámetro de consulta de filtro; el propósito de pedir realmente es solicitar una representación mínima de un recurso que cumplan la expresión de filtro como una indicación de si existen o no dichos objetos.   

Si el servicio se creó con las bibliotecas de infraestructura de lenguaje común proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se convertirá en una llamada al método de consulta de proveedor del servicio.  El valor de las propiedades del objeto proporcionado como el valor del argumento parámetros será como sigue: 

* parámetros. AlternateFilters.Count: 2
* parámetros. AlternateFilters.ElementAt(x). AttributePath: "id"
* parámetros. AlternateFilters.ElementAt(x). ComparisonOperator: ComparisonOperator.Equals
* parámetros. AlternateFilter.ElementAt(x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parámetros. AlternateFilters.ElementAt(y). AttributePath: administrador ""
* parámetros. AlternateFilters.ElementAt(y). ComparisonOperator: ComparisonOperator.Equals
* parámetros. AlternateFilter.ElementAt(y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parámetros. RequestedAttributePaths.ElementAt(0): "id"
* parámetros. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

En este caso, el valor del índice x puede ser 0 y el valor del índice y puede ser 1, o el valor de x puede ser 1 y el valor de y puede ser 0, dependiendo del orden de las expresiones de parámetro de consulta de filtro.   

**5:**  Aquí es un ejemplo de una solicitud de Azure Active Directory a un servicio SCIM para actualizar un usuario: 

    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}

Las bibliotecas de infraestructura de lenguaje común de Microsoft para implementar servicios SCIM traducen la solicitud en una llamada al método Update del proveedor del servicio.  Esta es la firma de ese método: 

    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }
    
    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }
      
      public string Value
      { get; set; }
    }



En el caso de ejemplo anterior de una solicitud de actualización de un usuario, tendrá el objeto proporcionado como el valor del argumento revisión estos valores de propiedad: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest como PatchRequest2). Operations.Count: 1
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). Path.AttributePath: administrador ""
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.Count: 1
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referencia: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Valor: 2819c223-7f76-453a-919d-413861904646

**6:**  Para anular aprovisionar a un usuario de un almacén de identidades representado por un servicio SCIM, Azure Active Directory le enviará una solicitud como este: 

    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    
Si el servicio se creó con las bibliotecas de infraestructura de lenguaje común proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se convertirá en una llamada al método de eliminación del proveedor del servicio.   Este método tiene esta firma: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
 
El objeto proporcionado como el valor del argumento resourceIdentifier tendrá estos valores de propiedad en el caso en el ejemplo anterior de una solicitud de aprovisionamiento anule la de un usuario: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

##<a name="group-provisioning-and-de-provisioning"></a>Aprovisionamiento de grupo y eliminar

La figura siguiente muestra los mensajes que Azure Active Directory enviará a un servicio SCIM para administrar el ciclo de vida de un grupo en otra identidad almacenan.  Los mensajes se diferencian de los mensajes relativos a los usuarios de tres maneras: 

* El esquema de un recurso de grupo se identifica como http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  
* Solicitudes para recuperar grupos se medida en que el atributo de los miembros que desea excluir de cualquier recurso que se proporcionan en respuesta a la solicitud.  
* Solicitudes para determinar si un atributo de referencia tiene un valor determinado será solicitudes sobre el atributo de miembros.  

![][5]
*Ilustración: Aprovisionamiento de grupo y anular el aprovisionamiento de secuencia*

##<a name="related-articles"></a>Artículos relacionados

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
- [Automatizar usuario aprovisionamiento y baja a aplicaciones de SaaS](active-directory-saas-app-provisioning.md)
- [Personalizar las asignaciones de atributo para el aprovisionamiento de usuario](active-directory-saas-customizing-attribute-mappings.md)
- [Escritura de expresiones para asignaciones de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Especificar el ámbito de filtros para el aprovisionamiento de usuario](active-directory-saas-scoping-filters.md)
- [Notificaciones de aprovisionamiento de la cuenta](active-directory-saas-account-provisioning-notifications.md)
- [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS](active-directory-saas-tutorial-list.md)


    
<!--Image references-->
[1]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
