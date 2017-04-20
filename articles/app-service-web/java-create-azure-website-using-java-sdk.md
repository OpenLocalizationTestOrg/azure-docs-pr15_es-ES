<properties 
    pageTitle="Crear una aplicación Web de servicio de aplicación de Azure mediante el SDK de Azure de Java" 
    description="Obtenga información sobre cómo crear una aplicación Web en el servicio de aplicación de Azure mediante programación utilizando el SDK de Azure para Java." 
    tags="azure-classic-portal"
    services="app-service\web" 
    documentationCenter="Java" 
    authors="donntrenton" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="02/25/2016" 
    ms.author="v-donntr"/>


# <a name="create-a-web-app-in-azure-app-service-using-the-azure-sdk-for-java"></a>Crear una aplicación Web de servicio de aplicación de Azure mediante el SDK de Azure de Java

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## <a name="overview"></a>Información general

En este tutorial se muestra cómo crear un SDK de Azure para aplicación Java que crea una aplicación Web en la [Aplicación de servicio de Azure][]y luego implementar una aplicación. Consta de dos partes:

- Parte 1 muestra cómo crear una aplicación de Java que crea una aplicación web.
- Parte 2 muestra cómo crear un JSP simple "Hola a todos" aplicación y luego usar FTP cliente para implementar código de servicio de aplicación.


## <a name="prerequisites"></a>Requisitos previos

### <a name="software-installations"></a>Instalaciones de software

El código de la aplicación AzureWebDemo en este artículo se escribió con Azure Java SDK 0.7.0, que se puede instalar con el [Instalador de plataforma Web][] (WebPI). Además, asegúrese de usar la versión más reciente del [Kit de herramientas de Azure para Eclipse][]. Después de instalar el SDK, actualizar las dependencias en su proyecto Eclipse ejecutando **Actualizar índice** de **Experto en repositorios**, a continuación, vuelva a agregar la última versión de cada paquete en la ventana de **dependencias** . Puede comprobar la versión del software instalado en Eclipse haciendo clic en **Ayuda > detalles de la instalación**; debe tener al menos las siguientes versiones:

- Paquete para bibliotecas de Microsoft Azure de Java 0.7.0.20150309
- Eclipse IDE EE para desarrolladores de Java 4.4.2.20150219


### <a name="create-and-configure-cloud-resources-in-azure"></a>Crear y configurar los recursos de la nube en Azure

Antes de comenzar este procedimiento, debe tener una suscripción de Azure active y configurar un valor predeterminado de Active Directory (AD) en Azure.


### <a name="create-an-active-directory-ad-in-azure"></a>Crear un Active Directory (AD) en Azure

Si todavía no tiene un Active Directory (AD) en la suscripción de Azure, inicie sesión en el [portal de clásica Azure][] con su cuenta de Microsoft. Si tiene varias suscripciones, haga clic en **suscripciones** y seleccione el directorio predeterminado para la suscripción que desea usar para este proyecto. A continuación, haga clic en **Aplicar** para cambiar a vista de la suscripción.

1. Seleccione **Active Directory** desde el menú de la parte izquierda. **Haga clic en Nuevo > directorio > Crear personalizado**.

2. En **Agregar directorio**, seleccione **Crear nuevo directorio**.

3. En **nombre**, escriba un nombre de directorio.

4. En **dominio**, escriba un nombre de dominio. Se trata de un nombre de dominio básico que se incluye de forma predeterminada con el directorio; tiene el formato `<domain_name>.onmicrosoft.com`. Puede asignarle un nombre basado en el nombre del directorio u otro nombre de dominio que es el propietario. Más adelante, puede agregar otro nombre de dominio que su organización ya usa.

5. En **país o región**, seleccione la configuración regional.

Para obtener más información sobre AD, consulte [¿Qué es un directorio de Azure AD][]?


### <a name="create-a-management-certificate-for-azure"></a>Crear un certificado de administración de Azure

El SDK de Azure de Java utiliza certificados de administración para autenticar con suscripciones a Azure. Estos son los certificados X.509 v3 que se utiliza para la autenticación de una aplicación de cliente que utiliza la API de administración de servicio para actuar en nombre del propietario de la suscripción a administrar recursos de suscripción.

El código de este procedimiento, utiliza un certificado autofirmado para autenticar con Azure. Para este procedimiento, debe crear un certificado y cargar el [portal clásica Azure][] previamente. Esto implica los siguientes pasos:

- Generar un archivo PFX que representa el certificado de cliente y guardarlo localmente.
- Generar un certificado de administración (archivos CER) desde el archivo PFX.
- Cargue el archivo .cer a su suscripción de Azure.
- Convertir el archivo PFX en almacén JKS, porque Java utiliza ese formato para autenticar mediante certificados.
- Escribir el código de autenticación de la aplicación, que hace referencia el archivo de almacén JKS local.

Cuando complete este procedimiento, el certificado CER se guardarán en la suscripción de Azure y el certificado de almacén JKS residirán en su unidad local. Para obtener más información acerca de los certificados de administración, vea [crear y cargar un certificado de administración de Azure][].


#### <a name="create-a-certificate"></a>Crear un certificado

Para crear su propio certificado autofirmado, abra la consola de comandos en el sistema operativo y ejecute los comandos siguientes.

> **Nota:**  El equipo en el que se ejecuta este comando debe tener el JDK instalado. Además, la ruta de acceso a la herramienta de claves depende de la ubicación en la que instalar el JDK. Para obtener más información, vea [clave y la herramienta de administración de certificados (herramienta de claves)][] en la documentación en línea de Java.

Para crear el archivo .pfx:

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

Para crear el archivo .cer:

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

donde:

- `<java-install-dir>`es la ruta del directorio en el que ha instalado Java.
- `<keystore-id>`es el identificador de entrada de claves (por ejemplo, `AzureRemoteAccess`).
- `<cert-store-dir>`es la ruta del directorio en el que desea almacenar certificados (por ejemplo `C:/Certificates`).
- `<cert-file-name>`es el nombre del archivo de certificado (por ejemplo `AzureWebDemoCert`).
- `<password>`es la contraseña que elija para proteger el certificado; debe tener al menos 6 caracteres. No puede escribir contraseña, aunque no se recomienda.
- `<dname>`es el nombre distintivo X.500 estar asociadas alias y se usa como los campos emisor y el asunto en el certificado autofirmado.

Para obtener más información, vea [crear y cargar un certificado de administración de Azure][].


#### <a name="upload-the-certificate"></a>Cargar el certificado

Para cargar un certificado autofirmado para Azure, vaya a la página de **configuración** en el portal de clásica y luego haga clic en la pestaña **Administración de certificados** . Haga clic en **cargar** en la parte inferior de la página y vaya a la ubicación del archivo .cer que ha creado.


#### <a name="convert-the-pfx-file-into-jks"></a>Convertir el archivo PFX en almacén JKS

En el símbolo de Windows (que se ejecuta como administrador), un cd en el directorio que contiene los certificados y ejecute el comando siguiente, donde `<java-install-dir>` es el directorio donde instaló Java en su equipo:

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. Cuando se le pida, escriba la contraseña de claves de destino; se trata de la contraseña para el archivo de almacén JKS.

2. Cuando se le pida, escriba la contraseña de claves de origen; Esta es la contraseña especificada para el archivo PFX.

Las dos contraseñas no tiene que ser el mismo. No puede escribir contraseña, aunque no se recomienda.


## <a name="build-a-web-app-creation-application"></a>Crear una aplicación de creación de la aplicación Web

### <a name="create-the-eclipse-workspace-and-maven-project"></a>Crear el área de trabajo de Eclipse y experto en Project

En esta sección se crea un área de trabajo y un proyecto de experto para la aplicación de creación de la aplicación de web, denominado AzureWebDemo.

1. Crear un nuevo proyecto de experto. Haga clic en **archivo > Nuevo > proyecto experto**. En **Nuevo proyecto a experto**, seleccione **crear un proyecto sencillo** y **ubicación de área de trabajo de uso predeterminada**.

2. En la segunda página del **Nuevo proyecto de experto en**, especifique lo siguiente:

    - Id. de grupo:`com.<username>.azure.webdemo`
    - Id. de muestra: AzureWebDemo
    - Versión: 0.0.1-SNAPSHOT
    - Empaquetar: jar
    - Nombre: AzureWebDemo

    Haga clic en **Finalizar**.

3. Abra el nuevo archivo del proyecto pom.xml en el Explorador de proyectos. Seleccione la ficha **dependencias** . Como un nuevo proyecto, no se muestran todavía ningún paquete.

4. Abrir la vista de repositorios de experto. **Haga clic en Ventana > Mostrar vista > otros > Maven > Maven repositorios** y haga clic en **Aceptar**. La vista de **Experto en repositorios** aparecerán en la parte inferior del IDE.

5. Abrir **Repositorios globales**, haga clic en el repositorio **central** y seleccione **Volver a crear el índice**.

    ![][1]
    
    Este paso puede tardar varios minutos dependiendo de la velocidad de la conexión. Cuando se vuelve a crear el índice, debería ver los paquetes de Microsoft Azure en el repositorio **central** de experto.

6. En **dependencias**, haga clic en **Agregar**. En **El grupo de entrar Id...** escriba `azure-management`. Seleccione los paquetes de administración de base y aplicación de servicio Web Apps:

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **Nota:** Si está actualizando las dependencias después de una versión nueva, debe volver a agregar cada una de las dependencias de esta lista.
    > Después haga clic en **Agregar** y seleccione cada dependencia, aparece con el nuevo número de versión en la lista **dependencias** .

Haga clic en **Aceptar**. Los paquetes de Azure aparecerán en la lista **dependencias** .


### <a name="writing-java-code-to-create-a-web-app-by-calling-the-azure-sdk"></a>Escribir el código de Java para crear una aplicación Web llamando el SDK de Azure

A continuación, escriba el código que llama las API en el SDK de Azure de Java para crear la aplicación de servicio de aplicación web.

1. Crear una clase de Java que contengan el código de punto de entrada principal. En el Explorador de proyectos, haga clic en el nodo del proyecto y seleccione **Nuevo > clase**.

2. En la **Nueva clase de Java**, asigne un nombre a la clase `WebCreator` y Active la casilla de verificación **principales de anular estático público** . Las selecciones deben aparecer como sigue:

    ![][2]

3. Haga clic en **Finalizar**. El archivo WebCreator.java aparece en el Explorador de proyectos.


### <a name="calling-the-azure-api-to-create-an-app-service-web-app"></a>Llamar a la API de Azure para crear una aplicación de servicio de aplicación Web


#### <a name="add-necessary-imports"></a>Agregar importaciones necesarias

En WebCreator.java, agregue las siguientes importaciones; estas importaciones proporcionan acceso a las clases en las bibliotecas de administración de Azure API de consumo:

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### <a name="define-the-main-entry-point-class"></a>Definir la clase de punto de entrada principal

Dado que el propósito de la aplicación AzureWebDemo es crear una aplicación de servicio Web App, nombre de la clase principal para esta aplicación `WebAppCreator`. Esta clase proporciona el código de punto de entrada principal que llama a la API de administración de servicio de Azure para crear la aplicación web.

Agregue las siguientes definiciones de parámetro para la aplicación web y webspace. Debe proporcionar su información de identificador y el certificado de Azure suscripción.

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

donde:

- `<subscription-id>`es el identificador de suscripción de Azure en la que desea crear el recurso.
- `<certificate-store-path>`es la ruta de acceso y nombre de archivo para el archivo de almacén JKS en su directorio de almacén de certificados local. Por ejemplo, `C:/Certificates/CertificateName.jks` para Linux y `C:\Certificates\CertificateName.jks` para Windows.
- `<certificate-password>`es la contraseña que especificó al crear el certificado de almacén JKS.
- `webAppName`puede ser cualquier nombre que elija; Este procedimiento utiliza el nombre `WebDemoWebApp`. El nombre de dominio completo es la `webAppName` con el `domainName` anexado, así que en este caso el dominio completo es `webdemowebapp.azurewebsites.net`.
- `domainName`deben especificarse como se indicó anteriormente.
- `webSpaceName`debe ser uno de los valores definidos en la clase [WebSpaceNames][] .
- `appServicePlanName`deben especificarse como se indicó anteriormente.

> **Nota:** Cada vez que ejecute esta aplicación, debe cambiar el valor de `webAppName` y `appServicePlanName` (o eliminar la aplicación web en el Portal de Azure) antes de ejecutar la aplicación de nuevo. En caso contrario, se producirá la ejecución porque ya existe el mismo recurso en Azure.


#### <a name="define-the-web-creation-method"></a>Definir el método de creación de web

A continuación, defina un método para crear la aplicación web. Este método, `createWebApp`, especifique los parámetros de la aplicación web y la webspace. También crea y configura al cliente de administración de aplicación de servicio Web Apps, que está definido por el objeto [WebSiteManagementClient][] . El cliente de administración es clave para crear aplicaciones Web. Proporciona servicios web REST que permiten que las aplicaciones administrar aplicaciones web (realizar operaciones como crear, actualizar y eliminar) mediante una llamada a la API de administración de servicio.

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

El código mostrará el estado HTTP de la respuesta de éxito o error y si se realiza correctamente, mostrará el nombre de la aplicación web creada.


#### <a name="define-the-main-method"></a>Definir el método main()

Proporcione el código del método main() que llama createWebApp() para crear la aplicación web.

Por último, llame a `createWebApp` de `main`:

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### <a name="run-the-application-and-verify-web-app-creation"></a>Ejecute la aplicación y comprobar la creación de aplicaciones web

Para comprobar que se ejecuta la aplicación, haga clic en **Ejecutar > ejecutar**. Cuando la aplicación finalice ejecución, verá el siguiente resultado en la consola de Eclipse:

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

Inicie sesión en el portal de clásico Azure y haga clic en **Aplicaciones Web**. La nueva aplicación web debería aparecer en la lista de aplicaciones Web dentro de unos minutos.


## <a name="deploying-an-application-to-the-web-app"></a>Implementar una aplicación a la aplicación Web

Después de ejecutar AzureWebDemo y crear la nueva aplicación web, inicie sesión en el portal clásico, haga clic en **Aplicaciones Web**y seleccione **WebDemoWebApp** en la lista de **Aplicaciones Web** . En la página de panel de la aplicación web, haga clic en **Examinar** (o haga clic en la dirección URL, `webdemowebapp.azurewebsites.net`) para desplazarse hasta él. Verá una página en blanco del marcador de posición, porque aún no se ha publicado una ningún contenido en la aplicación web.

A continuación se crea una aplicación "Hola a todos" e implementar la aplicación web.


### <a name="create-a-jsp-hello-world-application"></a>Crear una aplicación JSP Hola a todos

#### <a name="create-the-application"></a>Creación de la aplicación

Para demostrar cómo implementar una aplicación en la web, el procedimiento siguiente muestra cómo crear una aplicación de Java "Hola a todos" sencilla y cárguelo en la aplicación Web de servicio de aplicación que creó la aplicación.

1. Haga clic en **archivo > Nuevo > proyecto Web dinámicos**. Asígnele el nombre `JSPHello`. No es necesario cambiar cualquier otra configuración en este cuadro de diálogo. Haga clic en **Finalizar**.

    ![][3]

2. En el Explorador de proyectos, expanda el proyecto **JSPHello** , haga clic con el botón **ContenidoWeb**, haga clic en **Nuevo > archivo JSP**. En el cuadro de diálogo nuevo archivo JSP, asigne un nombre al nuevo archivo `index.jsp`. Haga clic en **siguiente**.

3. En el cuadro de diálogo **Seleccionar plantilla de JSP** , seleccione **Nuevo archivo JSP (html)** y haga clic en **Finalizar**.

4. En index.jsp, agregue el código siguiente en la `<head>` y `<body>` secciones de etiqueta:

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### <a name="run-the-hello-world-application-in-localhost"></a>Ejecute la aplicación de Hola a todos en el host local

Antes de ejecutar esta aplicación, debe configurar algunas propiedades.

1. Haga clic en el proyecto **JSPHello** y seleccione **Propiedades**.

2. En el cuadro de diálogo de **Propiedades** : seleccione la **Ruta de acceso de compilación de Java**, seleccione la pestaña **orden y exportar** , active **JRE biblioteca del sistema**, haga clic en **hacia arriba** para moverlo a la parte superior de la lista.

    ![][4]

3. También en el cuadro de diálogo de **Propiedades** : seleccione **Tiempos de ejecución de destino** y haga clic en **nuevo**.

4. En el cuadro de diálogo **Nuevo entorno de tiempo de ejecución de servidor** , seleccione un servidor como **v7.0 Apache Tomcat** y haga clic en **siguiente**. En el cuadro de diálogo **Servidor Tomcat** , establezca el **nombre** `Apache Tomcat v7.0`y establezca el **Directorio de instalación de Tomcat** en el directorio en el que ha instalado la versión de servidor de Tomcat que desea usar.

    ![][5]

    Haga clic en **Finalizar**.

5. A continuación, vuelva a la página de **Destino Runtime** del cuadro de diálogo **Propiedades** . Seleccione **v7.0 Apache Tomcat**y luego haga clic en **Aceptar**.

    ![][6]

6. En el menú Eclipse **Ejecutar** , haga clic en **Ejecutar**. En el cuadro de diálogo **Ejecutar como** , seleccione **ejecutar en el servidor**. En el cuadro de diálogo **ejecutar en el servidor** , seleccione **v7.0 Tomcat Server**:

    ![][7]

    Haga clic en **Finalizar**.

7. Cuando se ejecuta la aplicación, verá la página **JSPHello** aparecen en una ventana de host local en Eclipse (`http://localhost:8080/JSPHello/`), que muestra el siguiente mensaje:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="export-the-application-as-a-war"></a>Exportar la aplicación como una guerra

Exportar los archivos de proyecto web como un archivo web (guerra) para que puede implementar la aplicación web. Los siguientes archivos de proyecto web residen en la carpeta ContenidoWeb:

    META-INF
    WEB-INF
    index.jsp

1. Haga clic en la carpeta ContenidoWeb y seleccione **Exportar**.

2. En el cuadro de diálogo **Exportar seleccione** , haga clic en **Web > guerra** de archivo y luego haga clic en **siguiente**.

3. En el cuadro de diálogo **Exportar guerra** , seleccione el directorio de src en el proyecto actual y, incluya el nombre del archivo guerra al final. Por ejemplo:

    `<project-path>/JSPHello/src/JSPHello.war`

Para obtener más información sobre cómo implementar archivos de guerra, vea [Agregar una aplicación de Java para las aplicaciones de Azure aplicación de servicio Web](web-sites-java-add-app.md).


### <a name="deploying-the-hello-world-application-using-ftp"></a>Implementación de la aplicación del mundo Hola mediante FTP

Seleccione un cliente FTP de terceros para publicar la aplicación. Este procedimiento describe dos opciones: la consola de Kudu integrada en Azure; y FileZilla, una herramienta popular con una interfaz de usuario cómoda y gráfica.

> **Nota:** El Kit de herramientas de Azure para Eclipse admite implementación en las cuentas de almacenamiento y servicios en la nube, pero actualmente no admite la implementación de aplicaciones web. Puede implementar para cuentas de almacenamiento y servicios de nube mediante un proyecto de implementación de Azure tal como se describe en [crear una aplicación Hola a todos de Azure en Eclipse](http://msdn.microsoft.com/library/azure/hh690944.aspx), pero no para aplicaciones web. Usar otros métodos, como FTP o GitHub para transferir archivos a su aplicación web.

> **Nota:** No se recomienda utilizar FTP desde el símbolo del sistema de Windows (la utilidad línea de comandos FTP.EXE que se incluye con Windows). Los clientes FTP que usan FTP activo, por ejemplo, FTP.EXE, a menudo no funciona con servidores de seguridad. FTP activo especifica una dirección interna basada en LAN, a la que un servidor FTP es probable que no podrá conectarse.

Para obtener más información sobre implementación a un servicio de aplicación web app mediante FTP, consulte los siguientes temas:

- [Implementar mediante una utilidad FTP](web-sites-deploy.md)


#### <a name="set-up-deployment-credentials"></a>Configurar las credenciales de la implementación

Asegúrese de que ha ejecutado la aplicación de **AzureWebDemo** para crear una aplicación web. Se transferir archivos en esta ubicación.

1. Inicie sesión en el portal clásico y haga clic en **Aplicaciones Web**. Asegúrese de que **WebDemoWebApp** aparece en la lista de aplicaciones web y asegúrese de que se está ejecutando. Haga clic en **WebDemoWebApp** para abrir su página de **panel** .

2. En la página de **panel** , **Eche un vistazo rápido**, haga clic en **configurar sus credenciales de la implementación** (si ya tiene las credenciales de la implementación, esto lee **restablecer sus credenciales de implementación**).

    Credenciales de implementación están asociadas a una cuenta de Microsoft. Debe especificar un nombre de usuario y contraseña que puede usar para implementar mediante Git y FTP. Puede usar estas credenciales para implementar cualquier aplicación web de todas las suscripciones de Azure asociada a su cuenta de Microsoft. Proporcione credenciales de implementación de Git y FTP en el cuadro de diálogo y registrar el nombre de usuario y la contraseña para usos futuros.


#### <a name="get-ftp-connection-information"></a>Obtener información de conexión FTP

Para usar FTP para implementar los archivos de la aplicación en la aplicación web recién creado, debe obtener información de conexión. Hay dos maneras de obtener información de conexión. Una forma es visitar la página de **panel** de la aplicación web; la otra forma es descargar la web la aplicación publicar perfil. El perfil de publicación es un archivo XML que proporciona información como credenciales de inicio de sesión y nombre de host FTP para las aplicaciones web en la aplicación de servicio de Azure. Puede usar este nombre de usuario y contraseña para implementar cualquier aplicación web de todas las suscripciones asociadas con la cuenta de Azure, no solo este uno.

Para obtener información de conexión de FTP del módulo de la aplicación web en el [Portal de Azure][]:

1. En **Essentials**, busque y copie el **nombre de host FTP**. Se trata de un URI similar a `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`.

2. En **Essentials**, busque y copie **el nombre de usuario FTP e implementación**. Esto tendrá el *nombre de usuario webappname\deployment*de formulario; Por ejemplo `WebDemoWebApp\deployer77`.

Para obtener información de conexión FTP desde el perfil de publicación:

1. En el módulo de la aplicación web, haga clic en **obtener publicar perfil**. Esto descargará un archivo .publishsettings en su unidad local.

2. Abra el archivo .publishsettings en un editor XML o editor de texto y busque la `<publishProfile>` que contiene el elemento `publishMethod="FTP"`. Debe ser similar al siguiente:

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. Tenga en cuenta que la aplicación web `publishProfile` mapa de configuración para la configuración de administrador del sitio FileZilla como sigue:

- `publishUrl`es el mismo que el **nombre de host FTP**, el valor establecido en el **Host**.
- `publishMethod="FTP"`significa establecer **protocolo** **FTP: protocolo de transferencia de archivos**y **cifrado** para **Usar FTP sin formato**.
- `userName`y `userPWD` son claves de los valores de contraseña especificada al restablecer las credenciales de implementación y el nombre de usuario real. `userName`es el mismo que **implementación / FTP usuario**. Se asignan al **usuario** y **contraseña** en FileZilla.
- `ftpPassiveMode="True"`significa que el sitio FTP utiliza a transferencia FTP pasivo; Seleccione **pasivo** en la pestaña **Configuración de transferencia** .


#### <a name="configure-the-web-app-to-host-a-java-application"></a>Configurar la aplicación Web para hospedar una aplicación de Java

Antes de publicar la aplicación, debe cambiar algunas opciones de configuración para que la aplicación web puede hospedar una aplicación Java.

1. En el portal clásico, vaya a la página de **panel** de la aplicación web y haga clic en **Configurar**. En la página **Configurar** , especifique la siguiente configuración.

2. En la **versión de Java** , el valor predeterminado es **desactivar**; Seleccione la versión de Java los objetivos de la aplicación; Por ejemplo, 1.7.0_51. Después de esto, también asegurarse de que el **contenedor de Web** se establece en una versión de servidor de Tomcat.

3. En los **Documentos predeterminados**, agregue index.jsp y desplazarse hasta la parte superior de la lista. (El archivo predeterminado para las aplicaciones web es hostingstart.html).

4. Haga clic en **Guardar**.


#### <a name="publish-your-application-using-kudu"></a>Publicar la aplicación mediante Kudu

Una forma de publicar la aplicación es usar la consola de depuración Kudu integrada en Azure. Kudu se sabe que es estable y coherente con la aplicación de servicio Web Apps y servidor Tomcat. Tener acceso a la consola de la aplicación web de exploración a una dirección URL de la forma siguiente:

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. Para este procedimiento, la consola de Kudu se encuentra en la siguiente URL; Vaya a esta ubicación:

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. En el menú superior, seleccione **consola de depuración > CMD**.

3. En la línea de comandos de la consola, vaya a `/site/wwwroot` (o haga clic en `site`, a continuación, `wwwroot` en la vista de directorio en la parte superior de la página):

    `cd /site/wwwroot`

4. Después de especificar la **versión de Java**, Tomcat server debe crear un directorio de aplicaciones Web. En la línea de comandos de la consola, desplácese hasta el directorio de aplicaciones Web:

    `mkdir webapps`

    `cd webapps`

5. Arrastre JSPHello.war de `<project-path>/JSPHello/src/` y colóquelo en la vista del directorio Kudu en `/site/wwwroot/webapps`. No arrástrelo hasta el área "Arrastrar aquí para cargar y zip", porque descomprima Tomcat.

  ![][8]

En la primera JSPHello.war aparece en el área de directorio por sí mismo:

  ![][9]

En poco tiempo (probablemente menos de 5 minutos) Tomcat Server se descomprima el archivo de guerra en un directorio JSPHello descomprimir. Haga clic en el directorio raíz para ver si index.jsp ha sido descomprimir y copiarán allí. Si es así, vaya al directorio de aplicaciones Web para ver si se ha creado el directorio JSPHello descomprimir. Si no ve estos elementos, espere y repita.

  ![][10]


#### <a name="publish-your-application-using-filezilla-optional"></a>Publicar la aplicación mediante FileZilla (opcional)

Otra herramienta que puede usar para publicar la aplicación es FileZilla, un cliente FTP popular de terceros con una interfaz de usuario cómoda y gráfica. Puede descargar e instalar FileZilla desde [http://filezilla-project.org/](http://filezilla-project.org/) si no lo tiene. Para obtener más información acerca de cómo utilizar el cliente, consulte la [documentación de FileZilla](https://wiki.filezilla-project.org/Documentation) y esta entrada de blog en [clientes FTP - parte 4: FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx).

1. En FileZilla, haga clic en **archivo > Administrador del sitio**.
2. En el cuadro de diálogo **Administrador del sitio** , haga clic en **Nuevo sitio**. Un nuevo sitio FTP en blanco aparecerán en **Seleccionar entrada** que le pide que proporcione un nombre. Para este procedimiento, asígnele el nombre `AzureWebDemo-FTP`.

    En la pestaña **General** , especifique la siguiente configuración:
    - **Host:** Escriba el **Nombre de Host FTP** que ha copiado en el panel.
    - **Puerto:** (Deje esto en blanco, tal como se trata de una transferencia pasivo y el servidor determinará el puerto).
    - **Protocolo:** Protocolo de transferencia de archivos de FTP
    - **Cifrado:** Usar FTP sin formato
    - **Tipo de inicio de sesión:** Normal
    - **Usuario:** Escriba la implementación / FTP usuario que ha copiado en el panel. Este es el nombre de usuario FTP completa, que tiene el formulario *webappname\username*.
    - **Contraseña:** Escriba la contraseña que especificó al establecer las credenciales de la implementación.

    En la pestaña **Configuración de transferencia** , seleccione **pasivo**.

3. Haga clic en **Conectar**. Si es correcto, consola de FileZilla se mostrará una `Status: Connected` mensaje y emitir una `LIST` comando para mostrar el contenido del directorio.

4. En el panel de sitio **Local** , seleccione el directorio de origen en la que reside el archivo JSPHello.war; la ruta de acceso será similar al siguiente:

    `<project-path>/JSPHello/src/`

5. En el panel de sitio **remoto** , seleccione la carpeta de destino. Se implementa el archivo guerra para la `webapps` directorio en la raíz de la aplicación web. Vaya a `/site/wwwroot`, haga clic en `wwwroot`y seleccione **Crear directorio**. Nombre del directorio `webapps` y escriba ese directorio.

6. Transferir JSPHello.war a `/site/wwwroot/webapps`. Seleccione JSPHello.war en la lista de archivo **Local** , haga clic en él y seleccione **cargar**. Verá aparecen en `/site/wwwroot/webapps`.

7. Después de copiar JSPHello.war en el directorio de aplicaciones Web, servidor Tomcat se descomprimir automáticamente (descomprima) los archivos en el archivo de guerra. Aunque servidor Tomcat comienza hay problema casi inmediatamente, podría tardar mucho tiempo (posiblemente horas) de los archivos que aparecen en el cliente FTP.


#### <a name="run-the-hello-world-application-on-the-web-app"></a>Ejecute la aplicación de Hola a todos en la aplicación Web

1. Después de cargar el archivo de guerra y comprobar que el servidor de Tomcat ha creado un descomprimir `JSPHello` directorio, vaya a `http://webdemowebapp.azurewebsites.net/JSPHello` para ejecutar la aplicación.

    > **Nota:** Si hace clic en **Examinar** desde el portal de clásico, puede que aparezca la página Web de forma predeterminada, que indica "se ha creado correctamente esta aplicación web de Java según". Es posible que tenga que actualizar la página Web para poder ver el resultado de la aplicación en lugar de la página Web de forma predeterminada.

2. Cuando se ejecuta la aplicación, verá una página web con el siguiente resultado:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="clean-up-azure-resources"></a>Limpiar los recursos de Azure

Este procedimiento crea una aplicación de servicio de aplicación web. Se le facturará para el recurso, siempre y cuando existe. A menos que se va a seguir utilizando la aplicación web de pruebas o desarrollo, debe considerar detener o eliminarlo. Una aplicación web que se ha detenido aún provocará un cargo pequeño, pero puede reiniciar en cualquier momento. Eliminar una aplicación web borra todos los datos que se ha cargado.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

  [1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
  [2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
  [3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
  [4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
  [5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
  [6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
  [7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
  [8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
  [9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
  [10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png
 

[Servicio de aplicaciones de Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Instalador de plataforma Web]: http://go.microsoft.com/fwlink/?LinkID=252838
[Kit de herramientas de Azure para Eclipse]: https://msdn.microsoft.com/library/azure/hh690946.aspx
[Portal de clásico de Azure]: https://manage.windowsazure.com
[¿Qué es un directorio de Azure AD]: http://technet.microsoft.com/library/jj573650.aspx
[Crear y cargar un certificado de administración de Azure]: ../cloud-services/cloud-services-certs-create.md
[Herramienta de administración de certificados (herramienta de claves) y clave]: http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html
[WebSiteManagementClient]: http://azure.github.io/azure-sdk-for-java/com/microsoft/azure/management/websites/WebSiteManagementClient.html
[WebSpaceNames]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html
[Portal de Azure]: https://portal.azure.com
