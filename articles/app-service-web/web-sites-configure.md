<properties 
    pageTitle="Configurar aplicaciones web de servicio de la aplicación de Azure" 
    description="Cómo configurar una aplicación web de servicios de aplicación de Azure" 
    services="app-service\web" 
    documentationCenter="" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="configure-web-apps-in-azure-app-service"></a>Configurar aplicaciones web de servicio de la aplicación de Azure #

En este tema se explica cómo configurar una aplicación web con el [Portal de Azure].

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="application-settings"></a>Configuración de la aplicación

1. En el [Portal de Azure], abra el módulo de la aplicación web.
2. Haga clic en **todas las opciones**.
3. Haga clic en **configuración de la aplicación**.

![Configuración de la aplicación][configure01]

El módulo de **configuración de la aplicación** tiene configuración agrupados en varias categorías.

### <a name="general-settings"></a>Configuración general

**Versiones de framework**. Si su aplicación utiliza cualquiera de estos marcos, configurar estas opciones: 

- **.NET Framework**: establezca la versión de .NET framework. 
- **PHP**: establecer la versión PHP o **OFF** para deshabilitar PHP. 
- **Java**: seleccione la versión de Java o **desactivar** para deshabilitar Java. Use la opción **Web contenedor** para elegir entre versiones Tomcat y embarcadero.
- **Python**: seleccione la versión de Python o **desactivar** para deshabilitar Python.

Por motivos técnicos, habilitar Java para su aplicación deshabilita las opciones. NET, PHP y Python.

<a name="platform"></a>
**Plataforma**. Se selecciona si la aplicación web se ejecuta en un entorno de 32 bits o 64 bits. El entorno de 64 bits requiere modo Basic o estándar. Libere y modos compartido siempre se ejecutan en un entorno de 32 bits.

**Web Sockets**. Establecer **activado** para habilitar el protocolo WebSocket; Por ejemplo, si la aplicación web usa [ASP.NET SignalR] o [socket.io].

<a name="alwayson"></a>
**Siempre en**. De forma predeterminada, las aplicaciones web se descargan si están inactivas durante un período de tiempo. Permite conservar los recursos del sistema. En el modo de Basic o estándar, puede habilitar **Siempre** mantener la aplicación cargado todo el tiempo. Si la aplicación ejecuta trabajos web continuo, debe habilitar **Siempre en**o los trabajos de web no se ejecutan confiable.

**Administra la versión de canalización**. Establece el [modo de canalización]IIS. Deje este valor establecido en integrado (predeterminado) a menos que tenga una aplicación heredada que requiere una versión anterior de IIS.

**Intercambiar automática**. Si habilita intercambiar automática para un espacio de implementación, servicio de aplicación intercambiar automáticamente la aplicación web en producción al insertar una actualización para que se incluyen. Para obtener más información, vea [implementar a ensayo posiciones para aplicaciones web en la aplicación de servicio de Azure] (web-sitios-ensaya-publishing.md).

### <a name="debugging"></a>Depuración

**Depuración remota**. Habilita la depuración remota. Cuando se habilita, puede usar al depurador remoto en Visual Studio para conectarse directamente a su aplicación web. Depuración remota permanecerá habilitada 48 horas. 

### <a name="app-settings"></a>Configuración de la aplicación

Esta sección contiene pares de nombre y valor que web cargará la aplicación en iniciar hacia arriba. 

- Para las aplicaciones. NET, estos valores se insertan en la configuración de .NET `AppSettings` en tiempo de ejecución reemplazar valores existentes. 

- Aplicaciones de PHP, Python, Java y nodo pueden tener acceso a esta configuración como variables de entorno en tiempo de ejecución. Para cada configuración de aplicación, se crean dos variables de entorno; una con el nombre especificado por la entrada de configuración de aplicación y otro con el prefijo APPSETTING_. Ambos contienen el mismo valor.

### <a name="connection-strings"></a>Cadenas de conexión

Cadenas de conexión para los recursos vinculados. 

Para las aplicaciones .NET, se insertan estas cadenas de conexión en la configuración de .NET `connectionStrings` configuración en tiempo de ejecución de reemplazar las entradas existentes donde la clave coincide con el nombre de la base de datos vinculados. 

Para las aplicaciones PHP, Python, Java y nodo, esta configuración estará disponible como variables de entorno en tiempo de ejecución con el prefijo con el tipo de conexión. Los prefijos de variables de entorno son los siguientes: 

- SQL Server:`SQLCONNSTR_`
- MySQL:`MYSQLCONNSTR_`
- Base de datos SQL:`SQLAZURECONNSTR_`
- Personalizado:`CUSTOMCONNSTR_`

Por ejemplo, si se denomina una cadena de conexión MySql `connectionstring1`, podría tener acceso a través de la variable de entorno `MYSQLCONNSTR_connectionString1`.

### <a name="default-documents"></a>Documentos predeterminados

El documento predeterminado es la página web que se muestra en la dirección URL raíz de un sitio Web.  Se usa el primer archivo coincidente en la lista. 

Aplicaciones Web pueden utilizar módulos que enrutar basado en la dirección URL, en lugar de servir contenido estático, en cuyo caso hay no es ningún documento predeterminado como tales.    

### <a name="handler-mappings"></a>Asignaciones de controlador

Use esta área para agregar procesadores de secuencia de comandos personalizada para controlar las solicitudes de extensiones de archivo específicas. 

- **Extensión**. Extensión de archivo que se tratan como *.php o handler.fcgi. 
- **Ruta de acceso de procesador de secuencia de comandos**. Ruta de acceso absoluta del procesador de secuencia de comandos. Procesador de secuencia de comandos procesará las peticiones de archivos que coinciden con la extensión de archivo. Utilice la ruta `D:\home\site\wwwroot` para hacer referencia al directorio de raíz de la aplicación.
- **Los argumentos adicionales**. Argumentos de línea de comandos opcionales para el procesador de secuencia de comandos 


### <a name="virtual-applications-and-directories"></a>Directorios y aplicaciones virtuales 
 
Para configurar directorios y aplicaciones virtuales, especifique cada directorio virtual y su correspondiente ruta física relativa a la raíz del sitio Web. Si lo desea, puede seleccionar la casilla de verificación de la **aplicación** para marcar un directorio virtual como una aplicación.


## <a name="enabling-diagnostic-logs"></a>Habilitar los registros de diagnóstico

Para habilitar los registros de diagnósticos:

1. En el módulo para la aplicación web, haga clic en **todas las opciones**.
2. Haga clic en **registros de diagnóstico**. 

Opciones para escribir los registros de diagnóstico desde una aplicación web que admita el registro: 

- **Registro de la aplicación**. Escribe registros de la aplicación en el sistema de archivos. Registro dura durante un período de 12 horas. 

**Nivel**. Cuando se habilita el registro de aplicación, esta opción especifica la cantidad de información que será registrada (Error, advertencia, información o detallado).

**Registro de servidor web**. Los registros se guardan en el formato de archivo de registro extendido W3C. 

**Mensajes de error detallados**. Mensajes de error detallado de guarda archivos .htm. Los archivos se guardan en /LogFiles/DetailedErrors. 

**Seguimiento de solicitud de error**. Registros de error en las peticiones de archivos XML. Los archivos se guardan en /LogFiles/W3SVC*largo y LARGOB*, donde largo y LARGOB es un identificador único. Esta carpeta contiene un archivo XSL y uno o más archivos XML. Asegúrese de descargar el archivo XSL, ya que proporciona funcionalidad para dar formato y filtrar el contenido de los archivos XML.

Para ver los archivos de registro, debe crear las credenciales FTP, como sigue:

1. En el módulo para la aplicación web, haga clic en **todas las opciones**.
2. Haga clic en **credenciales de implementación**.
3. Escriba un nombre de usuario y contraseña.
4. Haga clic en **Guardar**.

![Establezca las credenciales de implementación][configure03]

El nombre de usuario FTP completo es "app\username" donde *app* es el nombre de la aplicación web. El nombre de usuario aparece en el módulo de aplicación web, en **Essentials**.  

![Credenciales de implementación de FTP][configure02]

## <a name="other-configuration-tasks"></a>Otras tareas de configuración

### <a name="ssl"></a>SSL 

En el modo de Basic o estándar, puede cargar certificados SSL para un dominio personalizado. Para obtener más información, vea [Habilitar HTTPS para una aplicación web]. 

Para ver sus certificados cargados, haga clic en **Configuración de todos los** > **dominios personalizados y SSL**.

### <a name="domain-names"></a>Nombres de dominio

Agregar nombres de dominio personalizado para la aplicación web. Para obtener más información, vea [configurar un nombre de dominio personalizado para una aplicación web en la aplicación de servicio de Azure].

Para ver los nombres de dominio, haga clic en **Configuración de todos los** > **dominios personalizados y SSL**.

### <a name="deployments"></a>Implementaciones

- Configurar la implementación continuo. Consulte [Usar Git para implementar aplicaciones Web en la aplicación de servicio de Azure](./web-sites-deploy.md).
- Ranuras de implementación. Vea [implementar en entornos de prueba para las aplicaciones Web en Azure de aplicación de servicio].


Para ver las ranuras de implementación, haga clic en **Configuración de todos los** > **ranuras de implementación**.

### <a name="monitoring"></a>Supervisión

En el modo de Basic o estándar, puede probar la disponibilidad de los puntos finales HTTP o HTTPS de hasta tres ubicaciones distribuidas geo. Una prueba de supervisión se produce un error si el código de respuesta HTTP es un error (4xx o 5xx) o la respuesta tarda más de 30 segundos. Se considera que un punto final está disponible si se realiza correctamente las pruebas de supervisión de todas las ubicaciones especificadas. 

Para obtener más información, vea [Cómo: controlar el estado del extremo de web].

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones], donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="next-steps"></a>Pasos siguientes

- [Configurar un nombre de dominio personalizado en la aplicación de servicio de Azure]
- [Habilitar HTTPS para una aplicación de servicio de aplicaciones de Azure]
- [Ajustar el tamaño de una aplicación web en la aplicación de servicio de Azure]
- [Conceptos básicos de supervisión de Web Apps en el servicio de aplicación de Azure]

<!-- URL List -->

[SignalR de ASP.NET]: http://www.asp.net/signalr
[Portal de Azure]: https://portal.azure.com/
[Configurar un nombre de dominio personalizado en la aplicación de servicio de Azure]: ./web-sites-custom-domain-name.md
[Implementar en entornos de prueba para las aplicaciones Web en Azure de aplicación de servicio]: ./web-sites-staged-publishing.md
[Habilitar HTTPS para una aplicación de servicio de aplicaciones de Azure]: ./web-sites-configure-ssl-certificate.md
[Cómo: controlar el estado del extremo de web]: http://go.microsoft.com/fwLink/?LinkID=279906
[Conceptos básicos de supervisión de Web Apps en el servicio de aplicación de Azure]: ./web-sites-monitor.md
[modo de canalización]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Ajustar el tamaño de una aplicación web en la aplicación de servicio de Azure]: ./web-sites-scale.md
[Socket.IO]: ./web-sites-nodejs-chat-app-socketio.md
[Pruebe la aplicación de servicio]: http://go.microsoft.com/fwlink/?LinkId=523751

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
