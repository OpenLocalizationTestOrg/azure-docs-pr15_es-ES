<properties
    pageTitle="Ejemplo de MyDriving Azure IoT: generar | Microsoft Azure"
    description="Crear una aplicación que es una demostración completa de cómo diseñar un sistema IoT usando Microsoft Azure, incluidos el análisis de flujo, el aprendizaje y Hubs de evento."
    services=""
    documentationCenter=".net"
    suite=""
    authors="harikmenon"
    manager="douge"/>

<tags
    ms.service="multiple"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="03/25/2016"
    ms.author="harikm"/>


# <a name="build-and-deploy-the-mydriving-solution-to-your-environment"></a>Compile e implemente la solución de MyDriving su entorno

MyDriving es una solución de Internet de cosas (IoT) que recopila datos de su coche, procesa usando el aprendizaje y presenta en su teléfono móvil. El back-end consta de una variedad de servicios de Microsoft Azure. Los clientes pueden teléfonos Android, iOS o Windows 10.

Que se creó la solución MyDriving para darle un jumpstart para crear su propio sistema IoT. Desde el [repositorio de MyDriving de GitHub](https://github.com/Azure-Samples/MyDriving), puede obtener scripts del Administrador de recursos de Azure para implementar la arquitectura de back-end en su cuenta de Azure. Desde ese momento, puede volver a configurar los distintos servicios, modifique las consultas para adaptarlos a sus propios datos y así sucesivamente. Puede encontrar estas secuencias de comandos, junto con el código de la aplicación móvil, el proyecto de la API del servicio de aplicación de Azure y mucho más, en el repositorio de MyDriving.

Si aún no ha probado la aplicación, busque la [Guía de introducción de obtener](iot-solution-get-started.md).

Hay una cuenta detallada de la arquitectura de la [Guía de referencia de MyDriving](http://aka.ms/mydrivingdocs). En resumen, hay varias partes que hemos configurados y que podría configurado para crear un proyecto similar:

* Una **aplicación de cliente** se ejecuta en teléfonos Android, iOS y Windows 10. Usamos la plataforma Xamarin compartir gran parte del código, que se almacena en GitHub en `src/MobileApp`. La aplicación realmente realiza dos funciones distintas:
 * Transmite desde el dispositivo de diagnóstico a bordo (DAB) y desde su propio servicio de ubicación para nube back-end del sistema de telemetría.
 * Es una interfaz de usuario, donde los usuarios pueden consultar sobre sus viajes grabados.
* Un **servicio de nube** recopila los datos de viaje de carretera en tiempo real y lo procesa. El trabajo principal de la creación de este servicio es elegir, agregar parámetros y conectar una gran variedad de servicios de Azure. Algunas de las partes requieren secuencias de comandos para filtrar y procesar los datos entrantes. Usamos una plantilla de administrador de recursos de Azure para configurar todos los elementos.
* Una **aplicación de servicio móvil** es el servicio web detrás de la parte de la interfaz de usuario de la aplicación del dispositivo. Su trabajo principal es la base de datos de los datos almacenados, procesados de la consulta. El código se encuentra en GitHub en `src/MobileAppService`.
* **Visual Studio con Xamarin** es nuestro entorno de desarrollo. Xamarin, que existe como un componente de Visual Studio y de un entorno de desarrollo integrado independiente (IDE), se usa para generar el código del dispositivo entre plataformas. Para generar el código de iOS, es necesario disponer de una instancia de Xamarin que se ejecuta en un equipo de OS X. Si es necesario, se puede ejecutar como un agente, administrado de Visual Studio.
* **Pruebas de unidad** de las aplicaciones de dispositivo se realiza en la nube de prueba Xamarin.
* **GitHub** es el lugar donde se almacenan los de código, secuencias de comandos y plantillas de repositorio.
* **Visual Studio Team Services** es un servicio de nube que se usa para administrar la compilación continua y prueba de las aplicaciones web de servicio y dispositivo.
* **HockeyApp** se usa para distribuir versiones del código de dispositivo. También recopilará bloqueo y el uso de informes y comentarios de los usuarios.
* **Perspectivas de aplicación de Visual Studio** supervisa el servicio web móvil.

Veamos cómo en nos configurar todo eso. Tenga en cuenta que muchos de los pasos son opcionales.

## <a name="sign-up-for-accounts"></a>Iniciar sesión en cuentas

-   [Aspectos básicos de desarrollo de visual Studio](https://www.visualstudio.com/products/visual-studio-dev-essentials-vs.aspx). Este programa gratuito proporciona acceso fácil a muchas de las herramientas de desarrollador y servicios, incluyendo Visual Studio, Visual Studio Team Services y Azure. Le proporciona un crédito de 25 $ al mes en Azure de 12 meses. También incluye las suscripciones a Pluralsight formación y Xamarin universitarios. Puede también registrarse por separado para niveles libres de [Azure](https://azure.com) y [Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs.aspx), pero estas no proporcionan créditos de Azure.

-   [HockeyApp](https://rink.hockeyapp.net/) (opcional), para administrar la distribución de las pruebas de aplicaciones móviles y de recopilación de telemetría.

-   [Xamarin](https://xamarin.com/) (obligatorio), para generar la aplicación móvil y ejecutar se ejecuta de depuración y pruebas de [Nube de prueba Xamarin](https://xamarin.com/test-cloud).

-   [GitHub](https://github.com/Azure-Samples/MyDriving/) (opcional), crear repositorios públicos gratuitos para su propio código (se pagan repositorios privados). Como alternativa, puede usar el plan básico en Visual Studio Team Services para repositorios privados.

-   [Power BI](https://powerbi.microsoft.com/) (opcional), crear eficaces visualizaciones de datos en todo el sistema.

> [AZURE.NOTE] No necesita una cuenta de GitHub para tener acceso al código MyDriving en [el repositorio de GitHub MyDriving](https://github.com/Azure-Samples/MyDriving).

## <a name="install-development-tools"></a>Instalar herramientas de desarrollo

La siguiente configuración está diseñado para desarrollar la solución completa: un iOS, Android y Windows 10 Mobile aplicación entre plataformas, con un Azure back-end.

Como alternativa, puede usar Xamarin Studio en Mac o Windows para desarrollar las aplicaciones móviles si no está trabajando en la Azure back-end.

Hay una [descripción más larga de esta configuración](https://msdn.microsoft.com/library/mt613162.aspx).

### <a name="windows-development-machine"></a>Equipo de desarrollo de Windows

La herramienta central en Windows es Visual Studio para trabajar con la aplicación MyDriving para Android y Windows, el proyecto de la API del servicio de aplicación y extensiones de microservice.

Xamarin, Git, emuladores y otros componentes útiles se integran con Visual Studio.

Instalar:

-   [2015 de Visual Studio con Xamarin](https://www.visualstudio.com/products/visual-studio-community-vs) (cualquier edición--Comunidad es gratuita).

-   [SQLite para plataforma Windows Universal](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936). Necesario para generar el código de Windows 10 Mobile.

-   [Azure SDK de Visual Studio de 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409). Proporciona el SDK para ejecutar aplicaciones de Azure, junto con las herramientas de línea de comandos de administración de Azure.

-   [Servicio de azure tela SDK](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric). Necesario para generar la extensión [microservice](../service-fabric/service-fabric-get-started.md) .

Asegúrese de que tiene las extensiones de Visual Studio derecha. Compruebe que en **Herramientas**, vea **Android, iOS, Xamarin...**. Si no es así, abra el Panel de Control y, a continuación, seleccione **programas y características** > **Microsoft** > **2015 de Visual Studio** > **Modificar**. En **desarrollo entre plataformas**, seleccione **C\#/.Net (Xamarin)**. Mientras está allí, compruebe que está instalado **Git para Windows** .

### <a name="mac-development-machine"></a>Equipo de desarrollo de Mac

Mac (Yosemite o posterior) es necesario si desea desarrollar para iOS. Aunque usamos Visual Studio con Xamarin en Windows para desarrollar y administrar la totalidad del código, Xamarin utiliza a un agente instalado en un equipo Mac con el fin de generar y firmar el código de iOS.

![Desarrollar en Windows y generar en Mac](./media/iot-solution-build-system/image1.png)

(Como alternativa, puede usar Xamarin Studio directamente en el Mac desarrollar aplicaciones entre plataformas.)

No es necesario el Mac si no desea incluir iOS como una plataforma de destino.

Instalar:

-   [Xamarin Studio para iOS](https://developer.xamarin.com/guides/ios/getting_started/installation/mac/). También puede configurar Visual Studio y Xamarin en un equipo Mac que se está ejecutando una máquina virtual de Windows. Consulte [el programa de instalación, instalar y las comprobaciones para los usuarios de Mac](https://msdn.microsoft.com/library/mt488770.aspx) en MSDN.

-   [Herramientas de desarrollo de Azure](https://azure.microsoft.com/downloads/) (opcional).

Habilitar inicio de sesión remoto en el Mac. Abra **Preferencias del sistema** > **Compartir**y, a continuación, seleccione **Inicio de sesión remota**.

Al abrir un proyecto de iOS en Visual Studio en Windows, el complemento Xamarin le pedirá que asigne el identificador de Mac.

## <a name="fetch-the-github-repository"></a>Capturar el repositorio de GitHub

Recuperar una copia local del [repositorio de GitHub MyDriving](https://github.com/Azure-Samples/MyDriving) mediante el botón **Descargar ZIP** en GitHub, Visual Studio o en otro cliente Git.

Descomprima el archivo en una carpeta con un nombre de ruta corto, como C:\\código.

Como alternativa, si desea mantener actualizada con o contribuir a nuestro código, clonar el repositorio como sigue:

**GIT clonar https://github.com/Azure-Samples/MyDriving.git**

## <a name="get-a-bing-maps-api-key"></a>Obtener a un Bing clave de la API de mapas

[Registrarse para una clave de la API de Bing Maps](https://msdn.microsoft.com/library/ff428642.aspx).

Debe reemplazarse en línea 22 `src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs`.



## <a name="build-the-demo-app"></a>Crear la aplicación de demostración

Abrir estas soluciones en Visual Studio:

-   src\MobileApps\MyDriving.sln

-   src\MobileAppService\MyDrivingService.sln

-   src\Extensions\ServiceFabric\VINLookUpApplication\VINLookUpApplication.sln

Se le pregunta si desea:

-   Confiar en algunos proyectos potencialmente no confiables. Elija esta opción para abrirlos si desea continuar.

-   Establecer el modo para programadores si está trabajando en un equipo de Windows 10 actualizado.

-   Proporcionar sus credenciales de Xamarin.

-   Conectarse a Mac Xamarin. Si no tiene un equipo Mac, haga clic en el proyecto de iOS en Visual Studio y, a continuación, seleccione **descargar el proyecto**.

Volver a crear la solución.

Si tiene problemas al generar, pruebe las soluciones a modo de interpretación que hemos encontrado:

-   *No se carga VINLookupApplication project*: asegúrese de que ha instalado el [SDK de Azure para Visual Studio de 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

-   *No crea proyecto tela de servicio*: generar los proyectos de la interfaz en primer lugar y a continuación, asegúrese de que ha instalado el SDK de tela de servicio.

-   *No crea aplicación android*:

    -   Abra **Herramientas** > **Android** > **Android SDK administrador**y asegúrese de que 6 Android (API 23) / plataforma SDK está instalado.

    -   Eliminar este directorio y, a continuación, volver a crear:<br/>
        `%LocalAppData%\Xamarin\zips`

## <a name="get-to-know-the-code"></a>Familiarizarse con el código

En la solución, encontrará:

-   Extensiones de Azure: tela de servicio.

-   HDInsight de Azure: Secuencias de comandos para procesar datos de ida y vuelta en Azure.

-   Aplicaciones móviles: Las aplicaciones del dispositivo.

-   MobileAppsService/MyDrivingService: Final de la web volver.

-   Power BI: La definición de informe.

-   Secuencias de comandos:

    -   Administrador de recursos: plantillas para crear los recursos de Azure.

    -   PowerShell: Secuencias de comandos para ejecutar las plantillas de administrador de recursos.

    -   Base de datos SQL Azure: Depuración de bases de datos.

-   Base de datos SQL: CreateTables: definiciones de esquema.

-   Análisis de secuencia de Azure: Consultas transforman la secuencia de datos entrantes.

## <a name="run-the-apps-in-development-mode"></a>Ejecutar las aplicaciones en modo de desarrollo

Tomar medidas para ejecutar las aplicaciones, en función del dispositivo que está usando:

-  Back-end: MyDrivingService establecer como proyecto de inicio y presione F5 para ejecutar el servicio web de back-end. Se abrirá una vista de explorador de la lista de la API.

-  Los clientes móviles: la [se desarrollan aplicaciones móviles en Xamarin](https://developer.xamarin.com/guides/cross-platform/deployment,_testing,_and_metrics/debugging_with_xamarin/).
 -  Android: Para obtener más información, vea [Depuración Android en Xamarin](http://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debugging_with_xamarin_android/).

 -  iOS: para obtener más información, vea [depuración en iOS](http://developer.xamarin.com/guides/ios/deployment,_testing,_and_metrics/debugging_in_xamarin_ios/).

 -  Windows Phone: Para obtener información detallada, consulte [Xamarin + de Windows Phone](https://developer.xamarin.com/guides/cross-platform/windows/phone/).

## <a name="upload-the-mobile-app-to-hockeyapp"></a>Cargar la aplicación móvil a HockeyApp

HockeyApp administra la distribución de su aplicación Android, iOS o Windows para comprobar los usuarios, notificar a los usuarios de nuevas versiones. También recopila los informes de bloqueo útil, comentarios de los usuarios con capturas de pantalla y métricas de uso.

[Empiece por al cargar](http://support.hockeyapp.net/kb/app-management-2/how-to-create-a-new-app) la aplicación de compilación. A continuación, inicie sesión en [HockeyApp](https://rink.hockeyapp.net) desde su equipo de desarrollo. En el panel del programador, haga clic en **Nueva aplicación**y, a continuación, arrastre los archivos creados en la ventana. (Más adelante, puede automatizar el servicio de compilación para ello).

Ahora ya está en el panel de la aplicación.

![Pestaña de descripción general en el panel de la aplicación](./media/iot-solution-build-system/image2.png)

Repita el proceso para cada plataforma que se inicia la aplicación. A continuación, puede hacer lo siguiente:

-  Use el [identificador de aplicación](http://support.hockeyapp.net/kb/app-management-2/how-to-find-the-app-id) del panel para enviar comentarios y datos de bloqueo de la aplicación. En MyDriving, actualice los identificadores de src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs.

-  [Invitar a usuarios de prueba](http://support.hockeyapp.net/kb/app-management-2/how-to-invite-beta-testers). Obtener una dirección URL para contratar a los usuarios de pruebas. Podrá iniciar sesión en su equipo, descargue la aplicación y enviar comentarios.

-  Si prefiere una versión beta más abierta, establezca la distribución al público. Haga clic en **Administrar la aplicación** > **distribución** > **Descargar = Public**. Ahora todos los usuarios pueden descargar la aplicación y enviar comentarios, y verá una notificación cuando se publique una nueva versión. También puede producirse algunos informes de bloqueo de ellos.

    ![Grupos en el panel](./media/iot-solution-build-system/image3.png)

-  [Informes de bloqueo de vínculo para Visual Studio Team Services](http://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs). Haga clic en **Administrar la aplicación** > **Visual Studio Team Services**. HockeyApp puede crear automáticamente elementos de trabajo en equipo servicios cuando hay informes de bloqueo o cuando se reciben comentarios.

Más en el [sitio HockeyApp](https://hockeyapp.net).

## <a name="test-the-mobile-app-on-xamarin-test-cloud"></a>Probar la aplicación móvil de nube de prueba de Xamarin

[Nube de prueba Xamarin](https://developer.xamarin.com/guides/testcloud/introduction-to-test-cloud/) automatiza UI pruebas en dispositivos reales en la nube. Mediante el marco de NUnit, escriba pruebas que ejecutan la aplicación a través de la interfaz de usuario.

Para usar Xamarin, incorpore [Xamarin.UITests](https://developer.xamarin.com/guides/testcloud/uitest/intro-to-uitest/) SDK a la aplicación, que se incluye como un paquete de NuGet. Encontrará en la aplicación de demostración y desea incluirlo al crear nuevos proyectos de prueba con las plantillas de Xamarin.

![Dónde encontrar el SDK de varias plataformas en la interfaz](./media/iot-solution-build-system/image4.png)

Un proyecto de prueba de ejemplo se incluye con la aplicación en el repositorio. En [MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService), busque /MobileApps/ [src](https://github.com/Azure-Samples/MyDriving/tree/master/src)[MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileApps/MyDriving)/MyDriving.UITests/.

Si usa una versión de Visual Studio Team Services, es fácil de escribir Xamarin UI pruebas y ejecutar como parte de la compilación.

## <a name="deploy-azure-services"></a>Implementar los servicios de Azure

Para llevar a cabo una implementación automática de los servicios de Azure y servicios de compilación de Team Services, consulte las instrucciones detalladas en **scripts/README.md**.

Microsoft Azure proporciona un amplio abanico de servicios diferentes que puede usar para crear aplicaciones de la nube. Aunque muchos pueden utilizarse individualmente (como aplicaciones de servicio Web App), que se encuentren en su mejor cuando está interconectados al formulario, como un sistema integrado que usamos en MyDriving.

Es posible crear e interconexión Azure servicios manualmente, pero es mucho más rápidas y más fiables utilizar el Administrador de recursos de Azure plantillas. [Administrador de recursos](../azure-resource-manager/resource-group-overview.md) automatiza la implementación de una solución recursos y hacer que las interconexiones entre ellas.

Encontrará la plantilla para el sistema de MyDriving en el repositorio de GitHub en [Las secuencias de comandos y el BRAZO](https://github.com/Azure-Samples/MyDriving/tree/master/scripts/ARM). Proporciona una vista completa y concisa de cómo los distintos servicios en nuestra arquitectura interconectados. Se explican todas estas en detalle en la [Guía de referencia de MyDriving](http://aka.ms/mydrivingdocs), pero puede obtener un lote haciendo leyendo la propia plantilla.

> [AZURE.NOTE] Servicios más Azure tienen un costo asociado, según el nivel de precios. Si es nuevo en Azure, puede [Pruébelo de forma gratuita](https://azure.microsoft.com/free/). Sin embargo, si no planea usar algunos componentes del sistema de MyDriving, asegúrese de eliminarlos para evitar provocar costos. La sección "Estimar los costos operativos" más adelante en este artículo proporciona un resumen de gastos típico del servicio.

### <a name="edit-the-template"></a>Editar la plantilla

Para personalizar la implementación, tal vez para quitar los componentes innecesarios o para agregar otros usuarios, primero asegúrese de una copia de escenario de\_complete.params.json y escenario\_complete.json en la que desea realizar cambios.

Puede usar el escenario\_archivo complete.params.json reemplazar varios valores predeterminados, como el servicio SKU o el tipo de replicación de almacenamiento, como se describe en la tabla siguiente. Los valores predeterminados seleccione las opciones de menor costo.

| **Parámetro**         | **Descripción**                | **Valor predeterminado** |
|--------|---------|-------|
| Concentrador IoT SKU           | Nivel de servicio de Azure IoT concentrador | F1                |
| Tipo de cuenta de almacenamiento  | Tipo de réplica de almacenamiento       | LRS estándar      |
| Objetivo de servicio SQL | Consumo de franja de simultaneidad   | DW100             |
| Plan de hospedaje SKU      | Plan de servicios de aplicación de servicio   | F1                |

En el escenario\_complete.json:

-   Buscar "nombre"base y cambiar a un nombre que prefiera.

-   Búsqueda de "Crear". Cada una de estas secciones crea un recurso.

-   Establecer sqlServerAdminLogin y sqlServerAdminPassword valores adecuado.

-   Antes de eliminar una sección que crea un recurso, compruebe si tiene dependientes buscando su nombre en otro lugar en el archivo. Tenga en cuenta que cada sección que crea un servicio incluye una sección *dependsOn* que enumera sus dependencias.

Esto es lo que se configura la plantilla. Detalles están en la [Guía de referencia](http://aka.ms/mydrivingdocs).

| **Servicio**                 | **Descripción y detalles**  
|---|----
| Cuentas de almacenamiento            | La plantilla crea tres cuentas:                                                                                                                                                                       
|| -Una base de datos SQL que recibe telemetría agregado de análisis de secuencia y sirve como el almacén de respaldo para tablas de servicio de la aplicación de Azure que exponen estos datos a través de los extremos de la API.                      
|| -Almacenamiento de blobs que se acumula datos históricos desde otra tarea de análisis de secuencia, procese HDInsight.                                                                                         
|| -Una base de datos SQL que recibe resultados procesados por HDInsight para su uso con Power BI.                                                                                                                 
| Hub de Azure IoT                     | Establece una conexión bidireccional en cada dispositivo conectado. En la solución MyDriving, la aplicación móvil actúa como una puerta de enlace de campo para enviar datos a Azure IoT concentrador. Hub de Azure IoT sirve como una entrada de análisis de secuencia. |
| Evento Azure Hubs                   | Un resultado para una tarea de análisis de secuencia colas la salida extensiones que se crean con tela de servicio de Azure.                                                                                               
| Almacén de datos SQL Azure          |                                                                                                                                                                                                            
| Tareas de análisis de secuencia | Conectar entradas y los resultados con una consulta que se usa para agregar tanto datos históricas y en tiempo real para la aplicación API de servicio, el aprendizaje Azure, extensiones y Power BI.                               
| Área de trabajo de aprendizaje de equipo  | Incluye experimentación, código R y servicio de la API.                                                                                                                                                              
| Generador de datos de Azure                | Formación de aprendizaje de máquina programada.                                                                                                                                                                     
| Plan de servicio tela hospedaje | Para las extensiones.                                                                                                                                                                                            
| Aplicación de servicio ("aplicación móvil")  | Hospeda el proyecto de la API de aplicaciones móviles que proporciona los extremos de la aplicación móvil. El código de la API debe implementar aplicación de servicio de Visual Studio.                                                         
| Reglas de alertas                 | Envía por correo electrónico si las respuestas de aplicación indican errores.                                                                                                                                            
| Información de la aplicación        | Para supervisar el rendimiento de las API de la aplicación de servicio. Debe configurar la conexión de Visual Studio.                                                                                          
| Depósito de clave de Azure                   | Para guardar el certificado de clúster del servicio web.                                                                                                                                                                

### <a name="run-the-template"></a>Ejecutar la plantilla

En **scripts/README.md**, hay instrucciones detalladas para ejecutar la plantilla.

Para aprovisionar todos estos servicios en su cuenta de Azure mediante la secuencia de comandos, siga uno de estos procedimientos:

-   Usar PowerShell:

    ```

    cd scripts/PowerShell;
    deploy.ps1 *location* *resourceGroupName*
    ```

 -   *ubicación* es la [ubicación de Azure](https://azure.microsoft.com/regions/), como por ejemplo `North Europe` o `West US`. Usar `Get-AzureLocation` para obtener una lista de ubicaciones disponibles.

 -   *resourceGroupName* es el nombre que desea asignar al grupo al que pertenecerán todos los recursos. Cuando haya terminado con los recursos, puede eliminarlos juntos todos los eliminar este grupo.

-   Ejecutar DeploymentScripts/Bash/deploy.sh con intensiva.

-   Abra y genere la solución de Visual Studio DeploymentScripts/VS/DeployARM.sln.

Tenga en cuenta que cada vez que se ejecuta la plantilla, crea un nuevo conjunto de recursos con los nuevos nombres. Para eliminar los recursos, vaya al portal y elimine el grupo de recursos.

Si se produce un error en la secuencia de comandos por cualquier motivo, puede volver a ejecutarlo.

La secuencia de comandos ofrece la opción de configuración de la integración continua en Visual Studio Team Services. Si ha configurado un proyecto de servicios de equipo, tendrá una dirección URL: https://yourAccountName.visualstudio.com. Cuando se le solicita, escriba la dirección URL completa. Puede dar un nombre nuevo o existente para un proyecto de Team Services.

## <a name="set-up-build-and-test-definitions-in-visual-studio-team-services"></a>Configurar generación y probar definiciones en Visual Studio Team Services

Se emplean Team Services en este proyecto principalmente para su generación y pruebe las características. Pero también proporciona soporte de colaboración excelente, como la administración de tareas con los paneles de Kanban, crea la revisión de código integradas con tareas y control de código fuente y a continuación, puertas de enlace. Se integra bien con otras herramientas como GitHub, Xamarin, HockeyApp y, por supuesto, en Visual Studio. Puede tener acceso a través de la interfaz web o Visual Studio, lo que es más cómodo en cualquier momento.

Los pasos descritos en las definiciones de compilación y lanzamiento utilizan una variedad de complementos servicios que están disponibles en el [catálogo de soluciones](https://marketplace.visualstudio.com/VSTS)de servicios de grupo. Además de utilidades básicas para ejecutar líneas de comando o copiar archivos, hay servicios que invocar versiones Xamarin, Android y otros proveedores y que se conecten a HockeyApp.

![Crear opciones de servicios de equipo](./media/iot-solution-build-system/image5.png)

### <a name="build-definitions"></a>Las definiciones de compilación

Tenemos definiciones de compilación para cada uno de los objetivos principales. También tenemos variaciones de la característica y pruebas de regresión. Nos ofrece:

-   MyDriving.Services (la aplicación web de back-end para la aplicación móvil)

-   MyDriving.Xamarin.Android

    -   Característica MyDriving.Xamarin.Android

    -   MyDriving.Xamarin.Android de regresión.

-   MyDriving.Xamarin.iOS

    -   Característica MyDriving.Xamarin.iOS

    -   MyDriving.Xamarin.iOS de regresión.

-   MyDriving.Xamarin.UWP

    -   Característica MyDriving.Xamarin.UWP

    -   MyDriving.Xamarin.UWP de regresión.

Si desea ver los detalles completos de nuestra configuración, consulte la sección 4.7 de la [Guía de referencia de MyDriving](http://aka.ms/mydrivingdocs), "Generación y configuración de Release". Siguen el mismo modelo general. La secuencia de comandos:

1.  Restaura el paquete NuGet. No mantendremos código compilado en el repositorio, por lo que son los primeros pasos de cada compilación para restaurar los paquetes de NuGet necesarios.

2.  Activa la licencia. La compilación se realiza en la nube, donde se necesita una licencia, en particular, para el servicio de compilación Xamarin--tenemos activar nuestra licencia en el equipo de compilación actual. A continuación, nos desactivarlo inmediatamente después, para permitir que se pueden usar en otro equipo.

3.  Crea el servicio adecuado. Usamos Xamarin versiones para las aplicaciones móviles y Visual Studio genera para el servicio web de back-end.

4.  Crea pruebas.

5.  Ejecuta pruebas. Ejecutar las pruebas de la aplicación móvil en la nube de prueba Xamarin.

6.  El resultado de compilación se publica en la ubicación de destino.

El desencadenador para las versiones principales se establece en la integración continua. Es decir, la compilación se ejecutará cada vez código está protegido con la bifurcación principal.

![Interfaz donde se establece el desencadenador para la integración continua](./media/iot-solution-build-system/image6.png)

### <a name="release-definitions"></a>Definiciones de lanzamiento

Definiciones de lanzamiento están configuradas en gran parte del mismo modo.

Para el servicio web, establezca la implementación como una aplicación web de Azure:

![Interfaz para configurar la implementación como una aplicación web de Azure](./media/iot-solution-build-system/image7.png)

Y se establece el desencadenador de lanzamiento para su implementación continuo. Es decir, cada comprobación en seguido de resultados de la compilación correcta de una actualización de la aplicación web.

![Interfaz para establecer el desencadenador de lanzamiento para su implementación continua](./media/iot-solution-build-system/image8.png)

Para las aplicaciones móviles, implementar a HockeyApp:

![Interfaz para implementar una aplicación móvil a HockeyApp](./media/iot-solution-build-system/image9.png)

## <a name="explore-telemetry-by-using-application-insights"></a>Explorar telemetría con perspectivas de aplicación

[Perspectivas de aplicación](../application-insights/app-insights-overview.md) recopila telemetría sobre el rendimiento y el uso de los servicios web. El SDK de perspectivas de aplicación envía telemetría del servicio para el recurso de información de la aplicación en Azure.

Busque el recurso de información de la aplicación que configurar la plantilla. Allí, puede explorar gráficos del rendimiento del [proyecto del servicio móvil de la aplicación](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService). Que muestren las solicitudes de servidor y tiempos de respuesta, errores, y cuenta de excepción. También hay gráficos de dependencia tiempos de respuesta, es decir, llamadas a la base de datos y las API de REST como el aprendizaje. Si hay algún problema de rendimiento, podrá ver qué parte de su sistema está causando ellos.

![Gráfico de rendimiento de ejemplo](./media/iot-solution-build-system/image11.png)

Si tiene un servicio web que haya configurado a mano, es fácil obtener los gráficos de la misma. En el módulo de servicio web, haga clic en **Herramientas de** > **extensiones** > **Agregar**. Seleccione la **información de la aplicación**.

![Interfaz para seleccionar información de la aplicación para obtener los gráficos](./media/iot-solution-build-system/image12.png)

La característica funciona mediante la instrumentación de la aplicación con el SDK de perspectivas de aplicación.

Puede agregar telemetría personalizado (o instrumento una aplicación que se está ejecutando en algún lugar fuera de Azure) agregando [El SDK de perspectivas de aplicación](../application-insights/app-insights-asp-net.md) en tiempo de desarrollo. Esto es útil a las mediciones de registro que dependen de la aplicación, como la longitud media de ida y vuelta de los usuarios o Kilometraje total. En Visual Studio, haga clic en el proyecto y, a continuación, seleccione **Agregar perspectivas de aplicación**.

![Interfaz para seleccionar agregar perspectivas de aplicación para agregar telemetría personalizado](./media/iot-solution-build-system/image10.png)

Información de la aplicación le enviará correos electrónicos de alerta si ve inusuales números de respuestas de error. También puede configurar sus propias alertas en varias métricas, como los tiempos de respuesta.

Solo para asegurarse de que el servicio web esté siempre hacia arriba y ejecutando, puede configurar [pruebas de disponibilidad](../application-insights/app-insights-monitor-web-app-availability.md). Estas pruebas ping a su sitio desde diversas ubicaciones del mundo cada 15 minutos. De nuevo, recibirá un correo electrónico si parece haber un problema.

## <a name="estimate-operational-costs"></a>Calcular los costos de operaciones

Es muy económico ejecutar una aplicación como este en pequeña escala. Muchos de los servicios tienen libres niveles de nivel de entrada, de modo que desarrollo y funcionamiento pequeños costo muy poco. Y, por supuesto, no tienen sus propio aplicaciones usar todas las características que se muestra en MyDriving.

Aquí es una estimación aproximada de nuestros costos en la configuración de la configuración de desarrollo de MyDriving. También observamos algunas alternativas que nosotros *no* usar. Esta información puede ser útil como estimar los costos de sus propio.

Suponemos que:

-   Un grupo de no más de cinco (más observando las partes interesadas).

-   Ejecutándose sobre un mes.

-   100 usuarios con cuatro viajes por día.

>[AZURE.NOTE] Si es nuevo en Azure, hay una [cuenta gratuita](https://azure.microsoft.com/free/).

| **Componente del servicio**  | **Notas** | **Costo por mes** |
|--------|--------|----------------|
| [Comunidad visual Studio 2015](https://www.visualstudio.com/products/visual-studio-community-vs) con [Xamarin](https://visualstudiogallery.msdn.microsoft.com/dcd5b7bd-48f0-4245-80b6-002d22ea6eee) <br/>Entorno de desarrollo entre plataformas| Comunidad de Visual Studio. (Tenga en [Visual Studio Professional](https://www.visualstudio.com/vs-2015-product-editions) para [Xamarin.Forms](https://xamarin.com/forms)para diseñar y plataformas de una sola base de código).  | $0   |
| [Hub de Azure IoT](https://azure.microsoft.com/pricing/details/iot-hub/) <br/>Conexión de datos bidireccional con dispositivos | liberar 8.000 mensajes + 0,5 KB o mensaje. | $0             |
| [Análisis de secuencia](https://azure.microsoft.com/pricing/details/stream-analytics/)  <br/>   Procesamiento de datos de gran volumen de secuencias                                                                                                                                                              | Cargo de $0.031 por transmisión unidad por hora, mientras habilitado. Elija el número de transmisiones unidades que desee. más escalar. | $23            |
| [Aprendizaje de equipo](https://azure.microsoft.com/documentation/services/machine-learning/)<br/> Respuestas adaptación                                                                                                                                                                              |  10 $, puesto y mes. <br/>                                                                                                                                                                                 + 3 horas ensayo \* $1 / experimentar hora. <br/>                                                                                                                                                           + hora 3.5 API CPU \* $2 / hora de la CPU de producción. <br/>                                                                                                                                                          Tiempo de CPU API supone min 5/día reciclaje, aunque esto aumentaría con más datos de entrada.                   <br/>                                                                                                                                                                     + día 2 min. puntuación para procesar 400 viajes por día.  | 20 $            |
| [Aplicación de servicio](https://azure.microsoft.com/pricing/details/app-service/)  <br/> Host para móvil back-end                                                                                                                                                                              | Nivel B1: aplicaciones web de producción. | 56 $            |
| [Servicios de equipo de Visual Studio](https://azure.microsoft.com/pricing/details/visual-studio-team-services/)  <br/> Crear prueba de unidades y administración de la versión; administración de tareas | Agentes privados, cinco usuarios.| $0             |
| [Información de la aplicación](https://azure.microsoft.com/pricing/details/application-insights/) <br/>Supervisión de rendimiento y el uso de servicios web y sitios| Nivel de gratuita.  | $0             |
| [HockeyApp](http://hockeyapp.net/pricing/) <br/> Distribución de aplicaciones beta, además de la colección de comentarios, uso y datos de bloqueo                                                                                                                                      | Dos aplicaciones gratuitas para nuevos usuarios.<br/> 30 $ al mes posteriormente.  | $0    |
| [Xamarin](https://store.xamarin.com/)<br/> El código en una plataforma uniforme para varios dispositivos | Versión de prueba gratuita. <br/>25 $ al mes posteriormente.| $0    |
| [Base de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/) Azure de aplicación de servicio| Nivel básico. modelo de base de datos única. | $5             |
| [Tela de servicio](https://azure.microsoft.com/pricing/details/service-fabric/) (opcional)  | Ejecutar un clúster local. | $0             |
| [Power BI](https://powerbi.microsoft.com/pricing/)<br/> Muestra flexible e investigación de datos estáticos y transmitidos| Nivel gratuita: 1 GB, 10.000 filas por hora, actualización diaria. <br/> $10/usuario/mes para [límites mayor](https://powerbi.microsoft.com/documentation/powerbi-power-bi-pro-content-what-is-it/), más opciones de conexión y colaboración.    | $0             |
| [Almacenamiento de información](https://azure.microsoft.com/pricing/details/storage/)   | L (localmente redundante) &lt; 100 G 0.024 $/ GB.  | $3             |
| [Generador de datos](https://azure.microsoft.com/pricing/details/data-factory/)                                                                                                                       | 0,60 $ por actividad \* (8-5 FOC).| $2             |
| [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) <br/>  Clúster a petición de un nuevo aprendizaje diaria   | Tres nodos de A3 a $ 0,32/hora para 1 hora diaria * 31 días. | 30 $            |
| [Evento Hubs](https://azure.microsoft.com/pricing/details/event-hubs/)  | Basic con unidad de rendimiento 11 $ al mes + entrada 0.028 $. | $11            |
| Dispositivo de DAB  || $12            |
| **Total**|    | **$157**       |

Para obtener más información, consulte:

-   Resumen de [los límites y las cuotas de servicio de Azure](../azure-subscription-service-limits.md#iot-hub-limits)

-   [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/) de Azure

## <a name="send-us-your-feedback"></a>Envíenos sus comentarios

Debido a que se creó MyDriving para ayudarle a comenzar a sus propios sistemas IoT, por supuesto queremos conocer su opinión sobre cómo funciona. Díganos si:

-  Ejecutar en dificultades o desafíos.

-  Hay un punto de extensión que le resulte más adecuado para su situación.

-  Buscar una forma más eficaz para llevar a cabo determinadas necesidades.

-  Tiene cualquier otras sugerencias para mejorar MyDriving o esta documentación.

Para proporcionar comentarios, se archivan [problema en GitHub] y deja un comentario siguiente (en-us edition).

¡Esperamos su audiencia!

## <a name="next-steps"></a>Pasos siguientes

Se recomienda la [Guía de referencia de MyDriving](http://aka.ms/mydrivingdocs), que es una descripción completa del diseño del sistema y sus componentes.
