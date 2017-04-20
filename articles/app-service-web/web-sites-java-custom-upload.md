<properties 
    pageTitle="Cargar una aplicación web de Java personalizada en Azure" 
    description="En este tutorial se muestra cómo cargar una aplicación web de Java personalizada para las aplicaciones de Azure aplicación de servicio Web." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="upload-a-custom-java-web-app-to-azure"></a>Cargar una aplicación web de Java personalizada en Azure

Este tema explica cómo cargar una aplicación web de Java personalizada en aplicaciones Web de [Servicio de aplicaciones de Azure] . Incluye información que se aplica a cualquier aplicación web o el sitio Web de Java y también algunos ejemplos de aplicaciones específicas.

Tenga en cuenta que Azure proporciona un medio para crear aplicaciones web de Java con UI de configuración del Portal de Azure y Azure Marketplace, tal como se describe en [crear una aplicación web de Java en la aplicación de servicio de Azure](web-sites-java-get-started.md). Este tutorial es para escenarios en los que no desea usar la configuración del Portal de Azure IU o Azure Marketplace.  

## <a name="configuration-guidelines"></a>Instrucciones de configuración

A continuación describe la configuración de lo esperada para aplicaciones web de Java personalizadas en Azure.

- El puerto HTTP utilizado por el proceso de Java se asigna dinámicamente.  El proceso debe utilizar el puerto de la variable de entorno `HTTP_PLATFORM_PORT`.
- Todos los puertos de escucha que no sea la única escucha HTTP deben estar deshabilitados.  En Tomcat, que incluye el cierre, HTTPS y AJP puertos.
- El contenedor debe estar configurado para el tráfico de IPv4 solo.
- El comando de **Inicio** de la aplicación debe establecerse en la configuración.
- Las aplicaciones que requieren directorios con escribir necesidad de permisos se encuentra en el directorio de contenido de la aplicación web de Azure, que es **D:\home**.  La variable de entorno `HOME` hace referencia a D:\home.  

Puede establecer las variables de entorno según sea necesario en el archivo web.config.

## <a name="webconfig-httpplatform-configuration"></a>configuración de Web.config httpPlatform

La información siguiente describe el formato de **httpPlatform** dentro de web.config.
                                 
**argumentos** (Predeterminado = ""). Argumentos para el archivo ejecutable o script especificado en la configuración de **processPath** .

Ejemplos (se muestra con **processPath** incluido):

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"
    
    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP\_PLATFORM\_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"


**processPath** - ruta de acceso al archivo ejecutable o de secuencia de comandos que se iniciará un proceso escuchando las solicitudes HTTP.

Ejemplos:


    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
                                                                                       
**rapidFailsPerMinute** (Predeterminado = 10.) Número de veces que se permite el proceso especificado en **processPath** bloqueo por minuto. Si supera este límite, **HttpPlatformHandler** dejará de iniciar el proceso para el resto de los minutos.
                                    
**requestTimeout** (Predeterminado = "00: 02:00".) Duración que **HttpPlatformHandler** esperará una respuesta del proceso escucha en `%HTTP_PLATFORM_PORT%`.

**startupRetryCount** (Predeterminado = 10.) Número de veces que se intenta iniciar el proceso especificado en **processPath** **HttpPlatformHandler** . Vea **startupTimeLimit** para obtener más detalles.

**startupTimeLimit** (Predeterminado = 10 segundos.) Duración que **HttpPlatformHandler** esperará el ejecutable o la secuencia de comandos iniciar un proceso escuchando en el puerto.  Si se supera este límite de tiempo, **HttpPlatformHandler** se terminar el proceso e intente iniciar nuevo **startupRetryCount** veces.
                                                                                      
**stdoutLogEnabled** (Predeterminado = "true".) Si es true, **stdout** y **stderr** para el proceso especificado en la configuración de **processPath** le redirigirá al archivo especificado en **stdoutLogFile** (consulte la sección **stdoutLogFile** ).
                                    
**stdoutLogFile** (Default="d:\home\LogFiles\httpPlatformStdout.log".) Ruta de acceso absoluta para el que se registrarán **stdout** y **stderr** de proceso especificado en **processPath** .
                                    
> [AZURE.NOTE] `%HTTP_PLATFORM_PORT%`es un marcador de posición especial que necesita especificado como parte de los **argumentos** o como parte de la lista de **environmentVariables** **httpPlatform** . Esto se reemplazará por un puerto generado internamente por **HttpPlatformHandler** para que el proceso especificado por **processPath** podrá escuchar en este puerto.

## <a name="deployment"></a>Implementación

Java aplicaciones basados en web se pueden implementar fácilmente a través de la mayoría de los mismos medios que se usan con Internet Information Services (IIS) en función de las aplicaciones web.  FTP, Git y Kudu se admiten como mecanismos de implementación, ya que es la capacidad SCM integrada para aplicaciones web. WebDeploy funciona como protocolo, sin embargo, como Java no está desarrollado en Visual Studio, WebDeploy no se ajusta con casos de uso de implementación de Java web app.

## <a name="application-configuration-examples"></a>Ejemplos de configuración de la aplicación

Para las siguientes aplicaciones, un archivo web.config y la aplicación de configuración se proporciona ejemplos que muestran cómo habilitar la aplicación de Java en la aplicación de servicio Web Apps.

### <a name="tomcat"></a>Tomcat
Si bien hay dos variaciones Tomcat que se proporcionan con la aplicación de servicio Web Apps, todavía es posible cargar instancias específicas del cliente. A continuación se muestra un ejemplo de una instalación de Tomcat con una máquina Virtual de Java (JVM) diferente.

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat" 
            arguments="">
          <environmentVariables>
            <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
            <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\bin\tomcat" />
            <environmentVariable name="JRE_HOME" value="%HOME%\site\wwwroot\bin\java" /> <!-- optional, if not specified, this will default to %programfiles%\Java -->
            <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
          </environmentVariables>
        </httpPlatform>
      </system.webServer>
    </configuration>

En el lado Tomcat, hay algunos cambios de configuración que hay que hacer. El archivo server.xml debe modificarse para establecer:

-   Puerto apagado = -1
-   Puerto de conector HTTP = ${port.http}
-   Dirección del conector HTTP = "127.0.0.1"
-   Comentar HTTPS y AJP conectores
-   También se puede establecer la configuración de IPv4 en el archivo catalina.properties que se pueden agregar`java.net.preferIPv4Stack=true`
    
No se admiten Direct3d llamadas en la aplicación de servicio Web Apps. Para deshabilitarlos, agregue la siguiente opción Java debe su aplicación realizar este tipo de llamadas:`-Dsun.java2d.d3d=false`

### <a name="jetty"></a>Embarcadero

Como es el caso de Tomcat, los clientes pueden cargar sus propias instancias de embarcadero. En el caso de ejecutar la instalación completa de embarcadero, la configuración tendría un aspecto similar a este:

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
             arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"
            startupTimeLimit="20"
          startupRetryCount="10"
          stdoutLogEnabled="true">
        </httpPlatform>
      </system.webServer>
    </configuration>

Es necesario cambiar en el start.ini para establecer la configuración de embarcadero `java.net.preferIPv4Stack=true`.

### <a name="springboot"></a>Springboot
Para obtener un Springboot aplicación ejecuta necesita cargar el archivo JAR o guerra y agregar el siguiente archivo web.config. El archivo web.config se coloca en la carpeta wwwroot. En el archivo web.config ajustar los argumentos para que señale al archivo JAR, en el ejemplo siguiente que se encuentra el archivo JAR en la carpeta wwwroot también.  

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe"
            arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\my-web-project.jar&quot;">
        </httpPlatform>
      </system.webServer>
    </configuration>


### <a name="hudson"></a>Hudson

Nuestra prueba usa la guerra Hudson 3.1.2 y la instancia de Tomcat 7.0.50 predeterminada pero sin utilizar la interfaz de usuario para configurar las cosas.  Dado que Hudson es la herramienta de generación de un software, se recomienda instalar en instancias dedicados donde se puede establecer el indicador **AlwaysOn** en la aplicación web.

1. En el directorio de raíz de la aplicación web, por ejemplo, **d:\home\site\wwwroot**, cree un directorio de **aplicaciones Web** (si no está presente) y coloque Hudson.war en **d:\home\site\wwwroot\webapps**.
2. Descargar a experto en apache 3.0.5 (compatible con Hudson) y colóquelo en **d:\home\site\wwwroot**.
3. Crear web.config en **d:\home\site\wwwroot** y pegue lo siguiente:
    
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>
          <system.webServer>
            <handlers>
              <add name="httppPlatformHandler" path="*" verb="*" 
        modules="httpPlatformHandler" resourceType="Unspecified" />
            </handlers>
            <httpPlatform processPath="%AZURE_TOMCAT7_HOME%\bin\startup.bat"
        startupTimeLimit="20"
        startupRetryCount="10">
        <environmentVariables>
          <environmentVariable name="HUDSON_HOME" 
        value="%HOME%\site\wwwroot\hudson_home" />
          <environmentVariable name="JAVA_OPTS" 
        value="-Djava.net.preferIPv4Stack=true -Duser.home=%HOME%/site/wwwroot/user_home -Dhudson.DNSMultiCast.disabled=true" />
        </environmentVariables>            
            </httpPlatform>
          </system.webServer>
        </configuration>

    En este momento se puede reiniciar la aplicación web para realizar los cambios.  Conectarse a http://yourwebapp/hudson para iniciar a Hudson.

4. Después de Hudson se configura, verá la siguiente pantalla:

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)
    
5. Acceso a la página de configuración de Hudson: haga clic en **Administrar Hudson**y, a continuación, haga clic en **Configurar el sistema**.
6. Configurar el JDK tal como se muestra a continuación:

    ![Configuración de Hudson](./media/web-sites-java-custom-upload/hudson2.png)

7. Configurar a experto tal como se muestra a continuación:

    ![Configuración de experto](./media/web-sites-java-custom-upload/maven.png)

8. Guardar la configuración. Ahora debería ser Hudson configurado y listo para su uso.

Para obtener información adicional sobre Hudson, consulte [http://hudson-ci.org](http://hudson-ci.org).

### <a name="liferay"></a>Liferay

Liferay es compatible con la aplicación de servicio Web Apps. Puesto que Liferay puede requerir importantes de memoria, la aplicación web debe ejecutar en un trabajo dedicado mediano o grande, lo que puede proporcionar memoria insuficiente. Liferay también tiene varios minutos para que se inicie. Por este motivo, se recomienda que configure la aplicación web **Siempre**.  

Con Liferay 6.1.2 que Comunidad Edition GA3 incluido Tomcat, los archivos siguientes se hayan editado después de descargar Liferay:

**Server.Xml**

- Cambiar el puerto de cierre en -1.
- Conector de cambio HTTP      `<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />`
- Comentar el conector AJP.

En la carpeta **liferay\tomcat-7.0.40\webapps\ROOT\WEB-INF\classes** , cree un archivo llamado **ext.properties portal**. Este archivo debe contener una línea, como se muestra aquí:

    liferay.home=%HOME%/site/wwwroot/liferay

En el mismo nivel de directorio como la carpeta tomcat 7.0.40, cree un archivo denominado **web.config** con el contenido siguiente:

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
    <add name="httpPlatformHandler" path="*" verb="*"
         modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%HOME%\site\wwwroot\tomcat-7.0.40\bin\catalina.bat" 
                      arguments="run" 
                      startupTimeLimit="10" 
                      requestTimeout="00:10:00" 
                      stdoutLogEnabled="true">
          <environmentVariables>
      <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
      <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\tomcat-7.0.40" />
            <environmentVariable name="JRE_HOME" value="D:\Program Files\Java\jdk1.7.0_51" /> 
            <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
          </environmentVariables>
        </httpPlatform>
      </system.webServer>
    </configuration>

En el bloque de **httpPlatform** la **requestTimeout** está establecida en "00:10:00".  Puede reducir pero, a continuación, es posible que vea algunos errores de tiempo de espera mientras se arranque Liferay.  Si cambia este valor, **connectionTimeout** en server.xml de tomcat también debe modificarse.  

Es importante recordar que se especifica la varariable de entorno JRE_HOME en web.config anterior para que apunten a JDK de 64 bits. El valor predeterminado es 32 bits, pero como Liferay pueden requerir niveles altos de memoria, se recomienda usar el JDK de 64 bits.

Una vez que realice estos cambios, reinicie la aplicación web ejecuta Liferay, a continuación, abra http://yourwebapp. El portal de Liferay está disponible desde la raíz de la aplicación web. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre Liferay, consulte [http://www.liferay.com](http://www.liferay.com).

Para obtener más información acerca de Java, consulte el [Centro para desarrolladores de Java](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 
<!-- External Links -->
[Servicio de aplicaciones de Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
