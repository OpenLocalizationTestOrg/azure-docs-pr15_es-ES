<properties 
    pageTitle="Matraz y el almacenamiento de tablas de Azure en Azure con herramientas de Python 2.2 para Visual Studio" 
    description="Obtenga información sobre cómo usar las herramientas de Python para Visual Studio para crear una aplicación web de matraz que almacena los datos en el almacenamiento de tablas de Azure e implementar a Azure aplicación de servicio Web Apps." 
    services="app-service\web"
    tags="python"
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="07/07/2016"
    ms.author="huvalo"/>




# <a name="flask-and-azure-table-storage-on-azure-with-python-tools-22-for-visual-studio"></a>Matraz y el almacenamiento de tablas de Azure en Azure con herramientas de Python 2.2 para Visual Studio 

En este tutorial, usaremos [Python Tools para Visual Studio] para crear una aplicación web de sondeos simple mediante una de las plantillas de ejemplo PTVS. Este tutorial también está disponible como un [vídeo](https://www.youtube.com/watch?v=qUtZWtPwbTk).

La aplicación web de sondeos define una abstracción para su repositorio, lo que puede cambiar fácilmente entre los distintos tipos de repositorios (en memoria, almacenamiento de tablas de Azure MongoDB).

Nos aprenderá cómo crear una cuenta de almacenamiento de Azure, cómo configurar la aplicación web para usar el almacenamiento de tablas de Azure y cómo publicar la aplicación web en la [Aplicación de servicio Web Apps de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Consulte el [Centro para desarrolladores de Python] para ver más artículos que tratan el desarrollo de aplicaciones de Azure aplicación de servicio Web con PTVS con botella, matraz y Django marcos web, con los servicios de MongoDB, almacenamiento de tablas de Azure, MySQL y base de datos de SQL. Mientras en este artículo se centra en la aplicación de servicio, los pasos son similares al desarrollar [Servicios de nube de Azure].

## <a name="prerequisites"></a>Requisitos previos

 - Visual Studio de 2015
 - [Python 2.2 las herramientas de Visual Studio]
 - [Python herramientas 2.2 de ejemplos de Visual Studio VSIX]
 - [Herramientas del SDK Azure VS 2015]
 - [Python 2.7 32 bits] o [Python 3.4 32 bits]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="create-the-project"></a>Crear el proyecto

En esta sección, se creará un proyecto de Visual Studio con una plantilla de ejemplo. Se creará un entorno virtual e instalar paquetes necesarios. A continuación, se deberá ejecutar la aplicación localmente utilizando el repositorio de en memoria predeterminado.

1.  En Visual Studio, seleccione **archivo**, **Nuevo proyecto**.

1.  Las plantillas de proyecto de la [Python 2.2 de herramientas de Visual Studio ejemplos VSIX] están disponibles en **Python**, **ejemplos**. Seleccione **Sondeos matraz Web Project** y haga clic en Aceptar para crear el proyecto.

    ![Cuadro de diálogo nuevo proyecto](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)

1.  Se le pedirá que instalar paquetes externos. Seleccione **instalar en un entorno virtual**.

    ![Cuadro de diálogo paquetes externos](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)

1.  Seleccione **Python 2.7** o **Python 3.4** como intérprete de base.

    ![Agregar el cuadro de diálogo de entorno Virtual](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)

1.  Confirme que la aplicación funciona presionando `F5`. De forma predeterminada, la aplicación utiliza un repositorio de memoria que no se requiere ninguna configuración. Todos los datos se pierden cuando se detiene el servidor web.

1.  Haga clic en **Crear sondeos de ejemplo**, haga clic en un sondeo y voto.

    ![Explorador Web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

## <a name="create-an-azure-storage-account"></a>Crear una cuenta de almacenamiento de Azure

Para usar las operaciones de almacenamiento, necesita una cuenta de almacenamiento de Azure. Puede crear una cuenta de almacenamiento siguiendo estos pasos.

1.  Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Haga clic en el icono de **nuevo** en la parte superior izquierdo del Portal y, a continuación, haga clic en **datos + almacenamiento** > **Cuenta de almacenamiento**. Haga clic en **crear**, a continuación, dé un nombre único de la cuenta de almacenamiento y cree un nuevo [grupo de recursos](../azure-resource-manager/resource-group-overview.md) .

    ![Creación rápida](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)

    Cuando se ha creado la cuenta de almacenamiento, el botón **notificaciones** flash verde **éxito** y módulos de la cuenta de almacenamiento está abierto para mostrar que pertenece el nuevo grupo de recursos que ha creado.

5. Haga clic en el elemento de **Las teclas de acceso** en el módulo de la cuenta de almacenamiento. Anote el nombre de cuenta y el clave1.

    ![Teclas](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageKeys.png)

    Necesitamos esta información para configurar su proyecto en la siguiente sección.

## <a name="configure-the-project"></a>Configurar el proyecto

En esta sección, se deberá configurar la aplicación para usar la cuenta de almacenamiento que acaba de crear. Veremos cómo obtener la configuración de conexión desde el Portal de Azure. A continuación, deberá ejecutar la aplicación localmente.

1.  En Visual Studio, haga clic en el nodo de proyecto en el Explorador de soluciones y seleccione **Propiedades**. Haga clic en la ficha **Depurar** .

    ![Configuración de depuración de Project](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)

1.  Establecer los valores de variables de entorno necesarios para la aplicación en el **Comando de depuración de servidor**, **entorno**.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    Esta acción establecerá las variables de entorno cuando se **Inicie de depuración**. Si desea que las variables se almacenen al **Iniciar sin depurar**, establecer los mismos valores en **Ejecutar el comando de servidor** también.

    Como alternativa, puede definir variables de entorno mediante el Panel de Control de Windows. Esta es una opción mejor si desea evitar almacenar credenciales en el código fuente o archivo de proyecto. Tenga en cuenta que debe reiniciar Visual Studio para los nuevos valores de entorno para que esté disponible para la aplicación.

1.  El código que implementa el repositorio de almacenamiento de tablas de Azure se encuentra en **models/azuretablestorage.py**. Consulte la [documentación] para obtener más información sobre cómo usar el servicio de la tabla de Python.

1.  Ejecute la aplicación con `F5`. Sondeos que se crean con **Crear sondeos de ejemplo** y los datos enviados por voto se serializarán en Azure almacenamiento de tablas.

    > [AZURE.NOTE] El entorno Virtual de Python 2.7 podría provocar un salto de excepción en Visual Studio.  Presione `F5` para continuar cargando el proyecto web.

1.  Vaya a la página **acerca de** para comprobar que la aplicación está utilizando el repositorio de **Almacenamiento de tablas de Azure** .

    ![Explorador Web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

## <a name="explore-the-azure-table-storage"></a>Explorar el almacenamiento de tablas de Azure

Es fácil ver y editar tablas de almacenamiento mediante el Explorador de nube de Visual Studio. En esta sección usaremos Explorador de servidores para ver el contenido de las tablas de aplicación de sondeos.

> [AZURE.NOTE] Esto requiere Microsoft Azure instalar herramientas, que están disponibles como parte del [SDK de Azure para .NET].

1.  Abra el **Explorador de la nube**. Expandir **Cuentas de almacenamiento**, su cuenta de almacenamiento, a continuación, **tablas**.

    ![Explorador de nube](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png)

1.  Haga doble clic en la tabla **sondeos** u **Opciones** para ver el contenido de la tabla en una ventana de documento, así como entidades de agregar o quitar o modificar.

    ![Resultados de la consulta de tabla](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Publicar la aplicación web en la aplicación de servicio de Azure

Azure .NET SDK proporciona una forma sencilla para implementar la aplicación web de servicio de aplicaciones de Azure.

1.  En el **Explorador de soluciones**, haga clic en el nodo del proyecto y seleccione **Publicar**.

    ![Publicar el cuadro de diálogo Web](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  Haga clic en **aplicaciones Web de Microsoft Azure**.

1.  Haga clic en **nuevo** para crear una nueva aplicación web.

1.  Rellene los siguientes campos y haga clic en **crear**.
    -   **Nombre de la aplicación Web**
    -   **Plan de servicios de aplicación**
    -   **Grupo de recursos**
    -   **Región**
    -   Deje el **servidor de base de datos** establecido en **ninguna base de datos**

1.  Aceptar todos los demás valores predeterminados y haga clic en **Publicar**.

1.  El explorador web se abrirá automáticamente a la aplicación web publicado. Si se desplaza a la página, verá que utiliza el repositorio **En memoria** , no en el repositorio de **Almacenamiento de tablas de Azure** .

    Eso es porque no se establecen las variables de entorno en la instancia de aplicaciones Web de servicio de la aplicación de Azure, para usar los valores predeterminados especificados en **settings.py**.

## <a name="configure-the-web-apps-instance"></a>Configurar la instancia de aplicaciones Web

En esta sección, se deberá configurar variables de entorno para la instancia de aplicaciones Web.

1.  En [El Portal de Azure](https://portal.azure.com), abra el módulo de la aplicación web haciendo clic en **Examinar** > **Servicios de aplicación** > el nombre de la aplicación web.

1.  En el módulo de la aplicación web, haga clic en **Toda la configuración**, haga clic en **Configuración de la aplicación**.

1.  Desplácese hacia abajo hasta la sección **configuración de la aplicación** y establecer los valores de **repositorio\_nombre**, **almacenamiento\_nombre** y **almacenamiento\_clave** como se describe en la sección **Configurar el proyecto** anterior.

    ![Configuración de la aplicación](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1.  Haga clic en **Guardar**. Después de que se han recibido las notificaciones que se han aplicado los cambios, haga clic en **Examinar** en el módulo principal de aplicación Web.

1.  Verá la aplicación web funciona como se espera, con el repositorio de **Almacenamiento de tablas de Azure** .

    ¡Felicidades!

    ![Explorador Web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

## <a name="next-steps"></a>Pasos siguientes

Siga estos vínculos para obtener más información sobre las herramientas de Python para Visual Studio, matraz y almacenamiento de tablas de Azure.

- [Python documentación Tools para Visual Studio]
  - [Proyectos Web]
  - [Proyectos de servicio de nube]
  - [Depuración remota en Microsoft Azure]
- [Matraz documentación]
- [Almacenamiento de Azure]
- [Azure SDK para Python]
- [Cómo usar el servicio de almacenamiento de la tabla de Python]

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Centro para desarrolladores de Python]: /develop/python/
[Servicios de nube de Azure]: ../cloud-services-python-ptvs.md
[documentación]: ../storage-python-how-to-use-table-storage.md
[Cómo usar el servicio de almacenamiento de la tabla de Python]: ../storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Azure Portal]: https://portal.azure.com
[Azure SDK para .NET]: http://azure.microsoft.com/downloads/
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python 2.2 las herramientas de Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python herramientas 2.2 de ejemplos de Visual Studio VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Herramientas del SDK Azure VS 2015]: http://go.microsoft.com/fwlink/?linkid=518003
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python documentación Tools para Visual Studio]: http://aka.ms/ptvsdocs
[Matraz documentación]: http://flask.pocoo.org/
[Depuración remota en Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Proyectos Web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Proyectos de servicio de nube]: http://go.microsoft.com/fwlink/?LinkId=624028
[Almacenamiento de Azure]: http://azure.microsoft.com/documentation/services/storage/
[Azure SDK para Python]: https://github.com/Azure/azure-sdk-for-python
 
