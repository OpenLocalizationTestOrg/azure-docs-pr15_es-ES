<properties 
    pageTitle="Migrar una aplicación web de empresa a la aplicación de servicio de Azure" 
    description="Muestra cómo usar el Asistente para la migración de aplicaciones Web para migrar rápidamente sitios Web de IIS existente a Azure aplicación de servicio Web Apps" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    writer="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/01/2016" 
    ms.author="cephalin"/>

# <a name="migrate-an-enterprise-web-app-to-azure-app-service"></a>Migrar una aplicación web de empresa a la aplicación de servicio de Azure

Puede migrar fácilmente los sitios Web existentes que se ejecutan en servicios de Internet Information Server (IIS) 6 o posterior para la [Aplicación de servicio Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). 

>[AZURE.IMPORTANT] Windows Server 2003 se ha alcanzado el final del soporte técnico de 14 de julio de 2015. Si actualmente aloja los sitios Web en un servidor IIS es Windows Server 2003, aplicaciones Web es bajo riesgo, de bajo costo, y forma de baja fricción para mantener los sitios Web en línea y el Asistente para la migración de aplicaciones Web permiten automatizar el proceso de migración de. 

[El Asistente para la migración de aplicaciones Web](https://www.movemetothecloud.net/) puede analizar la instalación de servidor IIS, identifique los sitios que se pueden migrar a la aplicación de servicio, resaltar todos los elementos que no se pueden migrar o no son compatibles en la plataforma y, a continuación, migración los sitios Web y bases de datos asociadas a Azure.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="elements-verified-during-compatibility-analysis"></a>Elementos que se revisarán durante el análisis de compatibilidad ##
El Asistente de migración, se crea un informe de preparación para identificar las posibles causas de atención o problemas de bloqueo que pueden impedir que una migración correcta de IIS local a Azure aplicación de servicio Web Apps. Algunos de los elementos claves que debería considerar son:

-   Enlaces de puerto: Web aplicaciones solo es compatible con puertos 80 para HTTP y el puerto 443 para el tráfico HTTPS. Varias configuraciones de puertos se omitirá y se enrutar el tráfico a 80 o 443. 
-   Autenticación: Web Apps es compatible con la autenticación anónima predeterminada y la autenticación de formularios donde especificado por una aplicación. Autenticación de Windows se puede utilizar mediante la integración con Azure Active Directory y ADFS solo. Todas las otras formas de autenticación: por ejemplo, autenticación básica - no se admiten actualmente. 
-   Caché de ensamblado global (GAC): no se admite la GAC en aplicaciones Web. Si la aplicación hace referencia a ensamblados que generalmente se implementa en la GAC, debe implementar en la carpeta bin de la aplicación en las aplicaciones Web. 
-   IIS 5 Modo de compatibilidad: esto no se admite en las aplicaciones Web. 
-   Grupos de aplicaciones: en aplicaciones Web, cada sitio y sus aplicaciones secundarias se ejecutan en el mismo grupo de aplicaciones. Si su sitio tiene varias aplicaciones de secundarios utilizando varios grupos de aplicaciones, consolidarlas en un único grupo aplicación con la misma configuración o migrar cada aplicación para una aplicación web independiente.
-   Componentes de COM – Web aplicaciones no admite el registro de componentes COM en la plataforma. Si sus aplicaciones o sitios Web que utilice de los componentes COM, debe escribirlas en código administrado e implementar ellas con el sitio Web o la aplicación.
-   Filtros: Web Apps puede admitir el uso de filtros. Debe hacer lo siguiente:
    -   implementar los archivos DLL con la aplicación web 
    -   registrar los archivos DLL con [Web.config](http://www.iis.net/configreference/system.webserver/isapifilters)
    -   Coloque un archivo de applicationHost.xdt en la raíz del sitio con el siguiente contenido:

            <?xml version="1.0"?>
            <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
            <configSections>
                <sectionGroup name="system.webServer">
                  <section name="isapiFilters" xdt:Transform="SetAttributes(overrideModeDefault)" overrideModeDefault="Allow" />
                </sectionGroup>
              </configSections>
            </configuration>

        Para obtener más ejemplos de cómo utilizar las transformaciones de documento XML con el sitio Web, consulte [transformar el sitio Web de Azure de Microsoft](http://blogs.msdn.com/b/waws/archive/2014/06/17/transform-your-microsoft-azure-web-site.aspx).

-   Otros componentes, como SharePoint, extensiones de servidor de FrontPage (FPSE), FTP, no se migrará certificados SSL.

## <a name="how-to-use-the-web-apps-migration-assistant"></a>Cómo usar el Asistente para la migración de aplicaciones Web ##
Pasos de esta sección a través de un ejemplo a migrar sitios Web unos que usan una base de datos de SQL Server y ejecuta en un equipo de Windows Server 2003 R2 (IIS 6.0) local:

1.  En el servidor IIS o en el equipo cliente, vaya a [https://www.movemetothecloud.net/](https://www.movemetothecloud.net/) 

    ![](./media/web-sites-migration-from-iis-server/migration-tool-homepage.png)

2.  Instalar el Asistente para la migración de aplicaciones Web haciendo clic en el botón de **Servidor de IIS dedicado** . Más opciones serán opciones en un futuro próximo. 
4.  Haga clic en el botón de la **Herramienta de instalación** para instalar el Asistente para la migración de aplicaciones Web en su equipo.

    ![](./media/web-sites-migration-from-iis-server/install-page.png)

    >[AZURE.NOTE] También puede hacer clic en **Descargar para instalar sin conexión** para descargar un archivo ZIP para instalar en servidores no conectados a internet. O bien, puede hacer clic en **cargar un informe de disponibilidad de la migración existente**, que es una opción avanzada para trabajar con un migración de disponibilidad informe existente generado previamente (se explica más adelante).

5.  En la pantalla de **Instalación de la aplicación** , haga clic en **instalar** para instalar en su equipo. También se instalará dependencias correspondientes como Web implementar, DacFX y IIS, si es necesario. 

    ![](./media/web-sites-migration-from-iis-server/install-progress.png)

    Una vez instalado, el Asistente para la migración de aplicaciones Web se inicia automáticamente.
  
6.  Seleccione **migrar sitios y bases de datos de un servidor remoto para Azure**. Escriba las credenciales administrativas para el servidor remoto y haga clic en **continuar**. 

    ![](./media/web-sites-migration-from-iis-server/migrate-from-remote.png)

    Por supuesto, puede elegir migrar desde el servidor local. La opción remoto es útil cuando desea migrar sitios Web de un servidor IIS de producción.
 
    En este momento la herramienta de migración inspecciona la configuración de su servidor IIS, por ejemplo, sitios, aplicaciones, grupos de aplicaciones y dependencias para identificar los sitios Web de candidatos para la migración. 

8.  La siguiente captura de pantalla muestra los tres sitios Web: **sitio Web predeterminado**, **TimeTracker**y **CommerceNet4**. Todos ellos tienen una base de datos asociado que se va a migrar. Seleccione todos los sitios que le gustaría evaluar y, a continuación, haga clic en **siguiente**.

    ![](./media/web-sites-migration-from-iis-server/select-migration-candidates.png)
 
9.  Haga clic en **cargar** para cargar el informe de disponibilidad. Si hace clic en **Guardar archivo de forma local**, puede ejecutar la herramienta de migración más tarde y cargar el informe de disponibilidad de guardado como se indicó anteriormente.

    ![](./media/web-sites-migration-from-iis-server/upload-readiness-report.png)
 
    Una vez que carga el informe de disponibilidad, Azure realiza análisis de preparación y muestra los resultados. Lea los detalles de la evaluación de cada sitio Web y asegúrese de que comprender o han resuelto todos los problemas antes de continuar. 
 
    ![](./media/web-sites-migration-from-iis-server/readiness-assessment.png)

12. Haga clic en **Iniciar migración** para comenzar la migración. Se le redirigirá ahora a Azure para iniciar sesión en su cuenta. Es importante que iniciar sesión con una cuenta que tenga una suscripción activa de Azure. Si no tiene una cuenta de Azure, a continuación, puede registrarse para una gratuita prueba [aquí](https://azure.microsoft.com/pricing/free-trial/?WT.srch=1&WT.mc_ID=SEM_). 

13. Seleccione la cuenta de inquilino, suscripción Azure y de región para la web Azure migradas aplicaciones y bases de datos y, a continuación, haga clic en **Iniciar migración**. Puede seleccionar los sitios Web para migrar más adelante.

    ![](./media/web-sites-migration-from-iis-server/choose-tenant-account.png)

14. En la siguiente pantalla puede realizar cambios en la configuración de migración de forma predeterminada, como por ejemplo:

    - usar una base de datos de SQL Azure existente o crear una nueva base de datos de SQL Azure y configurar sus credenciales
    - Seleccione los sitios Web para migrar
    - definir nombres para las aplicaciones web de Azure y sus bases de datos SQL vinculadas
    - personalizar la configuración global y la configuración de nivel de sitio

    La siguiente captura de pantalla muestra todos los sitios Web seleccionados para la migración con la configuración predeterminada.

    ![](./media/web-sites-migration-from-iis-server/migration-settings.png)

    >[AZURE.NOTE] la casilla de verificación **Habilitar Azure Active Directory** en una configuración personalizada integra la aplicación web de Azure con [Azure Active Directory](active-directory-whatis.md) (el **Directorio predeterminado**). Para obtener más información sobre la sincronización Azure Active Directory con su Active Directory local, consulte [integración de directorios](http://msdn.microsoft.com/library/jj573653).

16.  Una vez que realice todos los cambios que desee, haga clic en **crear** para iniciar el proceso de migración. La herramienta de migración se crear la base de datos de SQL Azure y Azure Online y, a continuación, publicar el contenido del sitio Web y las bases de datos. El progreso de la migración claramente se muestra en la herramienta de migración y verá una pantalla de resumen al final, los detalles que se migran los sitios, si se realizaron con éxito, vínculos a las aplicaciones web de Azure recién creado. 

    Si se produce algún error durante la migración, la herramienta de migración indicará claramente el error y deshacer los cambios. También podrá enviar el informe de errores directamente al equipo de ingeniería haciendo clic en el botón **Enviar informe de errores** , con la pila de llamada de error capturada y generar el cuerpo del mensaje. 

    ![](./media/web-sites-migration-from-iis-server/migration-error-report.png)

    Si migrar se realiza correctamente sin errores, también puede hacer clic en el botón **Enviar comentarios** para proporcionar los comentarios directamente. 
 
20. Haga clic en los vínculos a las aplicaciones web de Azure y compruebe que la migración se ha realizado correctamente.

21. Ahora puede administrar las aplicaciones web migrados de servicio de aplicaciones de Azure. Para ello, inicie sesión en el [Portal de Azure](https://portal.azure.com).

22. En el Portal de Azure, abra el módulo de aplicaciones Web para ver los sitios Web migrados (que se muestra como aplicaciones web), haga clic en cualquiera de ellas para empezar a administrar la aplicación web, como la configuración de publicación continua, crear copias de seguridad, ajuste automático y supervisión uso o rendimiento.

    ![](./media/web-sites-migration-from-iis-server/TimeTrackerMigrated.png)

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
 
