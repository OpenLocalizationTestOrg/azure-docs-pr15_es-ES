<properties
   pageTitle="Cómo migrar y publicar una aplicación Web a un servicio de nube Azure desde Visual Studio | Microsoft Azure"
   description="Obtenga información sobre cómo migrar y publicar la aplicación web a un servicio de nube Azure mediante Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Cómo: migrar y publicar una aplicación Web a un servicio de nube Azure desde Visual Studio

Para aprovechar las ventajas de los servicios de hospedaje y escalabilidad de Azure, podría desea migrar y publicar la aplicación web a un servicio de nube de Azure. Puede ejecutar una aplicación web en Azure con un mínimo de cambios en la aplicación existente.

>[AZURE.NOTE] Este tema es acerca de la implementación a servicios en la nube, no a los sitios web. Para obtener información sobre cómo implementar en sitios web, vea [implementar una aplicación web de servicio de aplicaciones de Azure](./app-service-web/web-sites-deploy.md).

Para obtener una lista de plantillas específicas que son compatibles con Visual C# y Visual Basic, vea la sección **Plantillas de proyecto compatibles** más adelante en este tema.

En primer lugar debe habilitar la aplicación web de Azure de Visual Studio. La ilustración siguiente muestra los pasos clave para publicar la aplicación web existente mediante la adición de un proyecto de Azure usar para su implementación. Este proceso agrega un proyecto Azure con el rol web necesarios para la solución. En función del tipo de proyecto web que tiene, las propiedades del proyecto para ensamblados también se actualizan si el paquete de servicio requiere ensamblados adicionales para su implementación.

![Publicar una aplicación Web en Microsoft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

>[AZURE.NOTE] **Convertir**, comando **convertir en proyecto de servicio de nube de Azure** se muestra solo para el proyecto web en la solución. Por ejemplo, el comando no está disponible para un proyecto de Silverlight en su solución.
Al crear un paquete de servicio o publicar su aplicación en Azure, pueden producirse errores o advertencias. Estos errores y advertencias pueden ayudarle a corregir problemas antes de implementar en Azure. Por ejemplo, podría recibir una advertencia sobre un ensamblado ausente. Para obtener más información acerca de cómo tratar las advertencias como errores, consulte [configurar un proyecto de servicio de nube de Azure con Visual Studio](vs-azure-tools-configuring-an-azure-project.md). Si genera la aplicación, ejecutarlo localmente mediante el emulador de cálculo o publicar en Azure, es posible que vea el siguiente error en la ventana de la **Lista de errores** : **la ruta de acceso, nombre de archivo o ambos son demasiado largas**. Este error se produce porque la longitud del nombre del proyecto completo de Azure es demasiado larga. La longitud del nombre del proyecto, incluyendo la ruta completa, no puede tener más de 146 caracteres. Por ejemplo, esto es el nombre del proyecto completo incluyendo la ruta de acceso de un proyecto de Azure que se creó para una aplicación de Silverlight: `c:\users\<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Es posible que tenga que mover la solución en un directorio diferente que tiene una ruta más corta para reducir la longitud del nombre del proyecto completo.

Para migrar y publicar una aplicación web en Azure desde Visual Studio, siga estos pasos.

## <a name="enable-a-web-application-for-deployment-to-azure"></a>Habilitar una aplicación Web para su implementación en Azure

### <a name="to-enable-a-web-application-for-deployment-to-azure"></a>Para habilitar una aplicación web para su implementación en Azure

1. Para habilitar la aplicación web para su implementación en Azure, abra el menú contextual para un proyecto web en la solución y elija Agregar un proyecto de implementación de Azure.

    Se producen las siguientes acciones:

    - Un proyecto de Azure denominado `<name of the web project>.Azure` se agrega a la solución para la aplicación.

    - Una función de web para el proyecto web se agrega a este proyecto Azure.

    - La propiedad **Copia Local** se establece en true para todos los ensamblados necesarios para MVC MVC 2, MVC 3, 4 y aplicaciones empresariales de Silverlight. Esto agrega estos ensamblados del paquete de servicio que se utiliza para su implementación.

  >[AZURE.IMPORTANT] Si tiene otros ensamblados o archivos necesarios para esta aplicación web, debe establecer manualmente las propiedades de estos archivos. Para obtener información sobre cómo configurar estas propiedades, vea la sección **Incluir archivos en el paquete de servicio** más adelante en este artículo.

  >[AZURE.NOTE] Si ya existe un rol de web para un proyecto web específico en un proyecto de Azure en la solución, **convertir**, **convertir en proyecto de servicio de nube de Azure** no aparece en el menú contextual para este proyecto web.

  Si tiene varios proyectos web en la aplicación web y que desea crear las funciones web para todos los proyectos, debe realizar los pasos de este procedimiento para cada proyecto web. Esto crea proyectos de Azure independientes para cada rol de web. Cada proyecto web se puede publicar por separado. Como alternativa, puede agregar manualmente otro rol web a un proyecto existente de Azure en la aplicación web. Para ello, abra el menú contextual para la carpeta de **Roles** en su proyecto Azure, elija **Agregar**y, a continuación, **Proyecto de rol Web de solución**, elija el proyecto para agregar como una función web y, a continuación, elija el botón **Aceptar** .

## <a name="use-an-azure-sql-database-for-your-application"></a>Usar una base de datos SQL Azure para la aplicación

Si tiene una cadena de conexión para la aplicación web que usa una base de datos de SQL Server que se encuentra en el local, debe cambiar esta cadena de conexión para usar una instancia de base de datos SQL que hospeda Azure en su lugar.

>[AZURE.IMPORTANT] Debe habilitar la suscripción que usar la base de datos de SQL. Si tiene acceso a la suscripción desde el [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), puede determinar qué servicios ofrece la suscripción. Las siguientes instrucciones se aplican al [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885)publicadas. Si está utilizando el [portal de Azure](http://portal.microsoft.com), omita el procedimiento siguiente.

### <a name="to-use-a-sql-database-instance-in-your-web-role-for-your-connection-string"></a>Para usar una instancia de base de datos SQL en su rol de web para la cadena de conexión

1. Para crear una instancia de base de datos SQL en el [portal de clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), siga los pasos en el siguiente artículo: [crear una base de datos de SQL Server](http://go.microsoft.com/fwlink/?LinkId=225109).

    >[AZURE.NOTE] Al configurar las reglas de firewall para la instancia de base de datos de SQL, debe seleccionar la casilla de verificación **Permitir que otros servicios de Azure tener acceso a este servidor** .

1. Para crear una instancia de base de datos de SQL para la cadena de conexión, siga los pasos de la siguiente sección en el siguiente artículo: [crear una base de datos de SQL](http://go.microsoft.com/fwlink/?LinkId=225110).

1. Para copiar la cadena de conexión de ADO.NET para la cadena de conexión, realice los pasos siguientes en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkID=213885).  

  1. Elija el botón de la **base de datos** y, a continuación, abra el nodo de la suscripción que usó para crear la instancia de base de datos de SQL.

  1. Para mostrar las instancias disponibles de la base de datos de SQL, elija el nodo de **Bases de datos de SQL** .

  1. Para mostrar las propiedades de la base de datos, elija la base de datos. Aparece la vista de **Propiedades** .

      >[AZURE.NOTE] Si no aparece la vista de **Propiedades** , debe abrirlo con el divisor.

  1. Para mostrar las cadenas de conexión, seleccione el botón de puntos suspensivos (...) junto a la vista.

    Aparece el cuadro de diálogo de **Cadenas de conexión** .

  1. Para copiar la cadena de conexión ADO.NET, resalte el texto y elija las teclas Ctrl + C.

  1. Para cerrar el cuadro de diálogo, elija el botón **Cerrar** .

1. Para reemplazar la cadena de conexión en el archivo web.config para usar esta instancia de base de datos de SQL, abra el archivo web.config, resalte la entrada de cadena de conexión existente y, a continuación, elija las teclas Ctrl + V. La cadena de conexión de ADO.NET de la instancia de base de datos SQL reemplaza la cadena de conexión existente.

1. También debe agregar el parámetro `MultipleActiveResultSets=True` con la cadena de conexión. La cadena de conexión debe tener el siguiente formato:

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```

1. (Opcional) Es un método alternativo para cambiar la cadena de conexión directamente en el archivo web.config agregar una sección en uno de los archivos de transformación de web.config, dependiendo de la configuración de compilación que usa para crear el paquete de servicio. Abra el archivo Web.Debug.Config o el archivo Web.Release.Config. Agregue la siguiente sección en este archivo:

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```

1. Guarde el archivo que modificó y volver a publicar la aplicación.

### <a name="to-use-an-instance-of-sql-database-by-using-the-azure-classic-portal"></a>Usar una instancia de base de datos de SQL mediante el portal de clásico de Azure

1. En el [portal de clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), elija el nodo de bases de datos de SQL.

  - Si aparece la instancia de base de datos de SQL que desea usar, elija para abrirlo.

  - Si todavía no lo ha creado todas las instancias, elija el vínculo que corresponda y, a continuación, crear una instancia.

1. Una vez que abra o cree una instancia de base de datos, elija el vínculo de **Cadenas de conexión** .

1. En la parte inferior de la página, seleccione el vínculo para configurar el firewall y acepte los valores predeterminados o configurar los valores que necesita.

1. Copiar la cadena de conexión ADO.NET, péguela en el archivo web.config sobre la cadena de conexión anterior para la base de datos local y asegúrese de agregar `MultipleActiveResultSets=True`.

## <a name="publish-a-web-application-to-azure"></a>Publicar una aplicación Web en Azure

### <a name="to-publish-a-web-application-to-azure"></a>Publicar una aplicación Web en Azure

1. Para probar la aplicación en el desarrollo local entorno usando la Azure calcula emulador, abra el menú contextual para el proyecto Azure del rol de web y elija **establecer como proyecto de inicio**. A continuación, elija **Depurar**, **Iniciar depuración** (teclado: **F5**).

    Se abre el cuadro de diálogo **iniciar el entorno de depuración de Azure** y se inicia la aplicación en el explorador. Para obtener detalles específicos acerca de cómo iniciar cada tipo de aplicación web en el emulador de cálculo, vea la tabla de esta sección.

1. Para configurar los servicios de la aplicación publicar en Azure, debe tener una cuenta de Microsoft y una suscripción de Azure. Siga los pasos en el tema siguiente para configurar los servicios: [Preparar para publicar o implemente una aplicación de Visual Studio Azure](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Para publicar la aplicación web a Azure, abra el menú contextual para el proyecto web y elija **publicar para Azure**.

    Se abre el cuadro de diálogo **Publicar aplicaciones de Azure** y Visual Studio inicia el proceso de implementación. Para obtener más información sobre cómo publicar la aplicación, vea la sección **publicar una aplicación de Azure desde Visual Studio** en [un servicio de nube con las herramientas de Azure de publicación](vs-azure-tools-publishing-a-cloud-service.md).

    >[AZURE.NOTE] También puede publicar la aplicación web del proyecto de Azure. Para ello, abra el menú contextual para el proyecto de Azure y elija **Publicar**.

1. Para ver el progreso de la implementación, puede ver la ventana de **Registro de actividad de Azure** . Este registro se muestra automáticamente cuando se inicia el proceso de implementación. Puede expandir el elemento de línea en el registro de actividad para mostrar información detallada, tal como se muestra en la siguiente ilustración:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Opcional) Para cancelar el proceso de implementación, abra el menú contextual para el elemento de línea en el registro de actividad y elija **Cancelar y quitar**. Esto detiene el proceso de implementación y elimina el entorno de implementación de Azure.

    >[AZURE.NOTE] Para quitar este entorno de implementación después de que se ha implementado, debe usar el [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Opcional) Una vez han iniciado las instancias de la función, Visual Studio muestra automáticamente el entorno de implementación en el nodo **Calcular de Azure** en el **Explorador de nube** o el **Explorador de servidores**. Desde aquí puede ver el estado de las instancias de la función individuales.

    La ilustración siguiente muestra las instancias de la función en el **Explorador de servidor** mientras se encuentran en estado Inicializando:

    ![VST_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)

1. Para obtener acceso a la aplicación después de la implementación, elija la flecha situada junto a la implementación al estado **completado** aparece en el **registro de actividad de Azure**. Muestra la dirección URL de la aplicación web de Azure. Vea la siguiente tabla para los detalles acerca de cómo iniciar un tipo específico de la aplicación web de Azure.

    La siguiente tabla enumeran los detalles acerca de cómo iniciar aplicaciones web específicas de Azure o ejecutar o depurar una aplicación web localmente mediante el emulador de Azure calcular:

  	|Tipo de aplicación Web|Ejecutar o depurar localmente mediante el emulador de cálculo|Ejecuta en Azure|
  	|---|---|---|
  	|Aplicación Web de ASP.NET|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5** .).|Seleccione el hipervínculo de dirección URL que aparece en la pestaña de **implementación** para el **registro de actividad de Azure** cargar la página de inicio en el explorador.|
  	|Aplicación Web de ASP.NET MVC 2|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5** .).|Seleccione el hipervínculo de dirección URL que aparece en la pestaña de **implementación** para el **registro de actividad de Azure** cargar la página de inicio en el explorador.|
  	|Aplicación Web de ASP.NET MVC 3|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5** .).|Seleccione el hipervínculo de dirección URL que aparece en la pestaña de **implementación** para el **registro de actividad de Azure** cargar la página de inicio en el explorador.|
  	|Aplicación Web de ASP.NET MVC 4|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5** .).|Seleccione el hipervínculo de dirección URL que aparece en la pestaña de **implementación** para el **registro de actividad de Azure** cargar la página de inicio en el explorador.|
  	|Aplicación Web vacía de ASP.NET|Debe agregar una página .aspx en la aplicación que establecer como página de inicio para el proyecto web. A continuación, en la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5** .).|Si tiene una página .aspx predeterminada en la aplicación, seleccione el hipervínculo de la dirección URL que se muestran en la ficha de **implementación** para el **registro de actividad de Azure** y esta página se carga en el explorador. Si tiene una página .aspx diferentes, debe ir a esta página específica con el siguiente formato para la dirección url:`<url for deployment>/<name of page>.aspx`|
  	|Aplicación de Silverlight|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5** .).|Debe ir a la página específica de la aplicación con el siguiente formato para la dirección url:`<url for deployment>/<name of page>.aspx`|
  	|Aplicación de empresa de Silverlight|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5** .).|Debe ir a la página específica de la aplicación con el siguiente formato para la dirección url:`<url for deployment>/<name of page>.aspx`|
  	|Aplicación de navegación de Silverlight|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5** .).|Debe ir a la página específica de la aplicación con el siguiente formato para la dirección url:`<url for deployment>/<name of page>.aspx`|
  	|Aplicación de servicio WCF|El archivo svc debe establecer como página de inicio para el proyecto de servicio WCF. A continuación, en la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5** .).|Debe ir al archivo svc para su aplicación utilizando el siguiente formato para la dirección url:`<url for deployment>/<name of service file>.svc`|
  	|Aplicación de servicio de flujo de trabajo WCF|El archivo svc debe establecer como página de inicio para el proyecto de servicio WCF. A continuación, en la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5** .).|Debe ir al archivo svc para su aplicación utilizando el siguiente formato para la dirección url:`<url for deployment>/<name of service file>.svc`|
  	|Entidades dinámicos de ASP.NET|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5** .).|Debe actualizar la cadena de conexión (consulte la sección siguiente). También debe ir a la página específica de la aplicación con el siguiente formato para la dirección url:`<url for deployment>/<name of page>.aspx`|
  	|Linq to SQL datos dinámicos de ASP.NET|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5** .).|Debe seguir los pasos de este procedimiento: usar una base de datos de SQL Azure para la aplicación (vea la sección anterior de este tema). También debe ir a la página específica de la aplicación con el siguiente formato para la dirección url:`<url for deployment>/<name of page>.aspx`|

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Actualizar una cadena de conexión para entidades dinámicos de ASP.NET

### <a name="to-update-a-connection-string-for-aspnet-dynamic-entities"></a>Para actualizar una cadena de conexión para entidades dinámicos de ASP.NET

1. Para crear una base de datos de SQL Azure que se pueden usar para una aplicación web de ASP.NET dinámico entidades, siga los pasos del procedimiento que **Use una base de datos de SQL Azure para su aplicación** más adelante en este tema.

1. Agregue las tablas y campos que necesita para esta base de datos desde el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. La cadena de conexión para este tipo de aplicación tiene el siguiente formato en el archivo web.config:  

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Actualizar el valor de *cadena de conexión* con la cadena de conexión de ADO.NET para la base de datos de SQL Azure como sigue:

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

1. Para guardar el archivo web.config con los cambios realizados en la cadena de conexión, en la barra de menús elija **archivo**, **guarde el archivo web.config**.

## <a name="supported-project-templates"></a>Plantillas de proyecto compatibles

Para publicar una aplicación web en Azure, la aplicación debe utilizar una de las plantillas de proyecto para C# o Visual Basic que se enumeran en la tabla siguiente.

|Grupo de la plantilla de proyecto|Plantilla de proyecto|
|---|---|
|Web|Aplicación Web de ASP.NET|
|Web|Aplicación Web de ASP.NET MVC 2|
|Web|Aplicación Web de ASP.NET MVC 3|
|Web|Aplicación Web de ASP.NET MVC4|
|Web|Aplicación Web vacía de ASP.NET|
|Web|Aplicación Web vacía de ASP.NET MVC 2|
|Web|Aplicación Web de entidades de datos dinámicos de ASP.NET|
|Web|Linq to SQL Web Application datos dinámicos de ASP.NET|
|Silverlight|Aplicación de Silverlight|
|Silverlight|Aplicación de empresa de Silverlight|
|Silverlight|Aplicación de navegación de Silverlight|
|WCF|Aplicación de servicio WCF|
|WCF|Aplicación de servicio de flujo de trabajo WCF|
|Flujo de trabajo|Aplicación de servicio de flujo de trabajo WCF|

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre la publicación, vea [Preparar a publicar o implemente una aplicación de Azure de Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Consulte también [Configuración de credenciales de autenticación con nombre](vs-azure-tools-setting-up-named-authentication-credentials.md).
