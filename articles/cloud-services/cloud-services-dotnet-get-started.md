<properties
    pageTitle="Introducción a servicios de nube de Azure y ASP.NET | Microsoft Azure"
    description="Obtenga información sobre cómo crear una aplicación de varios niveles con ASP.NET MVC y Azure. La aplicación se ejecuta en un servicio de nube con web y función de trabajo. Utiliza BLOB y colas entidad Framework, base de datos SQL y el almacenamiento de Azure."
    services="cloud-services, storage"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/10/2016"
    ms.author="adegeo"/>

# <a name="get-started-with-azure-cloud-services-and-aspnet"></a>Introducción a servicios de nube de Azure y ASP.NET

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

## <a name="overview"></a>Información general

Este tutorial muestra cómo crear una aplicación de .NET de varios niveles con ASP.NET MVC front-end e implementar en un [servicio de nube de Azure](cloud-services-choose-me.md). La aplicación utiliza la [Base de datos de SQL Azure](http://msdn.microsoft.com/library/azure/ee336279), el [servicio de blobs de Windows Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)y el [servicio de cola de Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern). Puede [descargar el proyecto de Visual Studio](http://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) desde la Galería de código de MSDN.

El tutorial muestra cómo crear y ejecutar la aplicación localmente, cómo implementar en Azure y ejecutar en la nube y, por último, cómo crear desde cero. Puede iniciar creación desde cero y, a continuación, realice la prueba e implementar pasos después si lo prefiere.

## <a name="contoso-ads-application"></a>Aplicación de anuncios de Contoso

La aplicación es un boletín de publicidad. Los usuarios crear un anuncio escribiendo texto y cargar una imagen. Puede ver una lista de anuncios con imágenes en miniatura y la imagen de tamaño completo pueden ver cuando selecciona un anuncio para ver los detalles.

![Lista de anuncios](./media/cloud-services-dotnet-get-started/list.png)

La aplicación, utiliza el [modelo de trabajo centrada en la cola](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) para descargar el trabajo intensivo de CPU de la creación de miniaturas de un proceso de back-end.

## <a name="alternative-architecture-websites-and-webjobs"></a>Arquitectura de alternativo: WebJobs y sitios Web

Este tutorial muestra cómo ejecutar front-end y back-end en un servicio de nube de Azure. Es una alternativa a ejecutar el front-end un [sitio Web de Azure](/services/web-sites/) y usar la característica [WebJobs](http://go.microsoft.com/fwlink/?LinkId=390226) (actualmente en la vista previa) para el back-end. Para obtener un tutorial que usa WebJobs, vea [Empezar a trabajar con el SDK de WebJobs de Azure](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md). Para obtener información sobre cómo elegir los servicios que mejor se ajusten a su situación, consulte [sitios Web de Azure, servicios de nube y comparación de máquinas virtuales de Windows](../app-service-web/choose-web-site-cloud-service-vm.md).

## <a name="what-youll-learn"></a>Lo que aprenderá

* Cómo habilitar el equipo para el desarrollo de Azure instalando el SDK de Azure.
* Cómo crear un proyecto de servicio de nube de Visual Studio con ASP.NET MVC función web y una función de trabajo.
* Cómo probar el proyecto de servicio de nube localmente, uso el almacenamiento de Azure.
* Cómo publicar el proyecto de la nube en un servicio de nube de Azure y prueba con una cuenta de almacenamiento de Azure.
* Cómo cargar archivos y guardarlos en el servicio de blobs de Windows Azure.
* Cómo usar el servicio de cola de Azure para la comunicación entre niveles.

## <a name="prerequisites"></a>Requisitos previos

El tutorial se supone que conoce [los conceptos básicos sobre los servicios de nube de Azure](cloud-services-choose-me.md) como terminología *web* y *función de trabajo* .  También se supone que sabe cómo trabajar con proyectos de [ASP.NET MVC](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) o [Web Forms](http://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview) en Visual Studio. La aplicación de ejemplo utiliza MVC, pero la mayoría del tutorial también se aplica a formularios Web.

Puede ejecutar la aplicación localmente sin una suscripción de Azure, pero deberá uno para implementar la aplicación en la nube. Si no tiene una cuenta, puede [activar las ventajas de suscriptor MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A55E3C668) o [suscribirse a una prueba gratuita](/pricing/free-trial/?WT.mc_id=A55E3C668).

Las instrucciones del tutorial funcionan con cualquiera de los siguientes productos:

* 2013 de Visual Studio
* Visual Studio de 2015

Si no tiene uno de estos, 2015 de Visual Studio se instalará automáticamente cuando se instala el SDK de Azure.

## <a name="application-architecture"></a>Arquitectura de la aplicación

La aplicación almacena los anuncios en una base de datos SQL con entidad Framework Code First para crear las tablas y acceso a los datos. Para cada anuncio la base de datos almacena dos direcciones URL, uno para la imagen de tamaño completo y otro para la miniatura.

![Tabla de AD](./media/cloud-services-dotnet-get-started/adtable.png)

Cuando un usuario carga una imagen, la front-end se ejecuta en una función web almacena la imagen en un [blob de Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)y almacena la información de ad en la base de datos con una dirección URL que señala al objeto binario. Al mismo tiempo, escribe un mensaje a una cola de Azure. Un proceso de back-end que se ejecuta en una función de trabajador periódicamente explora la cola para mensajes nuevos. Cuando aparezca un mensaje nuevo, la función de trabajo crea una miniatura de la imagen y actualiza el campo de base de datos en miniatura de dirección URL para dicho anuncio. El siguiente diagrama muestra cómo interactúan los elementos de la aplicación.

![Arquitectura de anuncios de Contoso](./media/cloud-services-dotnet-get-started/apparchitecture.png)

[AZURE.INCLUDE [install-sdk](../../includes/install-sdk-2015-2013.md)]

## <a name="download-and-run-the-completed-solution"></a>Descargar y ejecutar la solución completa

1. Descargue y descomprima el [completado solución](http://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4).

2. Inicie Visual Studio.

3. En el menú **archivo** elija **Abrir proyecto**, desplácese hasta que se ha descargado la solución y, a continuación, abra el archivo de solución.

3. Presione CTRL + MAYÚS + B para generar la solución.

    De forma predeterminada, Visual Studio restaura automáticamente el contenido del paquete NuGet, que no se ha incluido en el archivo *.zip* . Si no restauran los paquetes, instalarlos manualmente mediante el cuadro de diálogo **Administrar paquetes de NuGet para la solución** y haga clic en el botón **Restaurar** en la parte superior derecha.

3. En el **Explorador de soluciones**, asegúrese de que **ContosoAdsCloudService** está seleccionado como proyecto de inicio.

2. Si está usando Visual Studio de 2015, cambie la cadena de conexión de SQL Server en el archivo *Web.config* de la aplicación del proyecto ContosoAdsWeb y en el archivo *ServiceConfiguration.Local.cscfg* del proyecto ContosoAdsCloudService. En cada caso, cambie "(localdb) \v11.0" a "\MSSQLLocalDB (localdb)".

1. Presione CTRL + F5 para ejecutar la aplicación.

    Cuando se ejecuta un proyecto de servicio de nube localmente, Visual Studio invoca automáticamente el *emulador de almacenamiento*de Azure y Azure *calcular emulador* . El emulador cálculo utiliza los recursos del equipo para simular los entornos de rol web rol y trabajo. El emulador de almacenamiento utiliza una base de datos de [SQL Server Express LocalDB](http://msdn.microsoft.com/library/hh510202.aspx) para simular almacenamiento cloud Azure.

    La primera vez que ejecute un proyecto de servicio de nube tarda un minuto más o menos para los emuladores para que se inicie. Cuando haya finalizado el inicio del emulador, se abre el explorador predeterminado a la página de inicio de la aplicación.

    ![Arquitectura de anuncios de Contoso](./media/cloud-services-dotnet-get-started/home.png)

2. Haga clic en **crear un anuncio**.

2. Escriba algunos datos de prueba, seleccione una imagen *.jpg* para cargar y, a continuación, haga clic en **crear**.

    ![Crear página](./media/cloud-services-dotnet-get-started/create.png)

    La aplicación que se va a la página de índice, pero que no muestre una miniatura para el nuevo anuncio debido a que el procesamiento no ha hecho todavía.

3. Espere un momento y, a continuación, actualice la página de índice para ver la miniatura.

    ![Página de índice](./media/cloud-services-dotnet-get-started/list.png)

4. Haga clic en **Detalles** para el anuncio para ver la imagen de tamaño completo.

    ![Página de detalles](./media/cloud-services-dotnet-get-started/details.png)

Ejecutar la aplicación por completo en el equipo local, sin conexión a la nube. El emulador almacenamiento almacena los datos de cola y blob en una base de datos de SQL Server Express LocalDB y la aplicación almacena los datos de ad en otra base de datos de LocalDB. Entidad Framework Code First crea automáticamente la base de datos de ad la primera vez que la aplicación web ha intentado obtener acceso a él.

En la siguiente sección deberá configurar la solución para usar los recursos de la nube de Azure para colas, BLOB y la base de datos de la aplicación cuando se ejecuta en la nube. Si desea seguir ejecutar localmente pero usar recursos de almacenamiento y base de datos de la nube, puede hacerlo; es cuestión de configuración de las cadenas de conexión, que verá cómo hacerlo.

## <a name="deploy-the-application-to-azure"></a>Implementar la aplicación en Azure

Tendrá que hacer lo siguiente para ejecutar la aplicación en la nube:

* Crear un servicio de nube de Azure.
* Crear una base de datos de SQL Azure.
* Cree una cuenta de almacenamiento de Azure.
* Configurar la solución para usar la base de datos de SQL Azure cuando se ejecuta en Azure.
* Configurar la solución para usar su cuenta de almacenamiento de Azure cuando se ejecuta en Azure.
* Implemente el proyecto en el servicio de nube de Azure.

### <a name="create-an-azure-cloud-service"></a>Crear un servicio de nube de Azure

Un servicio de nube Azure es el entorno de en que la aplicación se ejecutará.

1. En el explorador, abra el [portal clásica Azure](http://manage.windowsazure.com).

2. Haga clic en **Nuevo > calcular > servicio de nube > crear rápido**.

4. En el cuadro de entrada de URL, escriba un prefijo de URL.

    Esta dirección URL tiene que ser único.  Recibirá un mensaje de error si el prefijo que elige ya está en uso por otra persona.

5. Elija la región donde desee implementar la aplicación.

    Este campo especifica que se va a hospedar el servicio de nube de centro de datos. Para una aplicación de producción, elija la región más próxima a sus clientes. En este tutorial, seleccione la región más cercana a usted.

6. Haga clic en **Crear servicio de nube**.

    En la siguiente imagen, se crea un servicio de nube con la dirección URL contosoads.cloudapp.net.

    ![Nuevo servicio de nube](./media/cloud-services-dotnet-get-started/newcs.png)

### <a name="create-an-azure-sql-database"></a>Crear una base de datos de SQL Azure

Cuando se ejecuta la aplicación en la nube, usará una base de datos en la nube.

1. En el [portal de clásica Azure](http://manage.windowsazure.com), haga clic en **Nuevo > Servicios de datos > base de datos SQL > crear rápido**.

1. En el cuadro **Nombre de la base de datos** , escriba *contosoads*.

1. En la lista desplegable de **servidor** , elija **nueva base de datos de SQL server**.

    Como alternativa, si su suscripción ya tiene un servidor, puede seleccionar dicho servidor en la lista desplegable.

1. Elija la misma **región** que eligió para el servicio de nube.

    Cuando el servicio de nube y la base de datos están en centros de datos distintos (distintos), aumentará la latencia y regiones se cobrará por ancho de banda fuera del centro de datos. Ancho de banda dentro de un centro de datos es gratuita.

1. Escriba un **Nombre de inicio de sesión** de administrador y la **contraseña**.

    Si ha seleccionado la **nueva base de datos de SQL server** no escribir un nombre existente y una contraseña a continuación, se introduce un nuevo nombre y una contraseña que está definiendo ahora para usarla más tarde, cuando tenga acceso a la base de datos. Si ha seleccionado un servidor que ha creado anteriormente, se le pedirá la contraseña para la cuenta de usuario administrativo ya ha creado.

1. Haga clic en **Crear base de datos SQL**.

    ![Nueva base de datos SQL](./media/cloud-services-dotnet-get-started/newdb.png)

1. Después de Azure termine de crear la base de datos, haga clic en la pestaña de **Bases de datos SQL** en el panel izquierdo del portal y, a continuación, haga clic en el nombre de la nueva base de datos.

2. Haga clic en la pestaña **panel** .

3. Haga clic en **Administrar direcciones IP permitidas**.

4. En los **Servicios permitidos**, cambiar **Servicios de Azure** en **Sí**.

5. Haga clic en **Guardar**.

### <a name="create-an-azure-storage-account"></a>Crear una cuenta de almacenamiento de Azure

Una cuenta de almacenamiento de Azure proporciona recursos para almacenar los datos de cola y blob en la nube.

En una aplicación del mundo real, se creará normalmente independientes cuentas de aplicación de datos en comparación con el registro de datos e independiente para los datos de prueba frente a los datos de producción. En este tutorial usará una cuenta.

1. En el [portal de clásica Azure](http://manage.windowsazure.com), haga clic en **Nuevo > Servicios de datos > almacenamiento > crear rápido**.

4. En el cuadro **dirección URL** , escriba un prefijo de URL.

    Este prefijo y el texto que aparece en el cuadro será la dirección URL exclusiva a su cuenta de almacenamiento. Si ya se ha usado el prefijo que especifica por otra persona, debe elegir un prefijo diferente.

5. Establezca la lista desplegable de **región** en la misma región que eligió en el servicio de nube.

    Cuando se encuentra la cuenta de servicio y almacenamiento de la nube en centros de datos distintos (distintos), aumentará la latencia y regiones se cobrará por ancho de banda fuera del centro de datos. Ancho de banda dentro de un centro de datos es gratuita.

    Grupos de afinidad Azure proporcionan un mecanismo para reducir la distancia entre recursos en un centro de datos, lo que puede reducir la latencia. Este tutorial no utiliza grupos de afinidad. Para obtener más información, vea [cómo crear un grupo de afinidad en Azure](http://msdn.microsoft.com/library/jj156209.aspx).

6. Establecer la lista de la lista desplegable de **replicación** **redundantes localmente**.

    Cuando geo replicación está habilitada para una cuenta de almacenamiento, es replicar el contenido almacenado en un centro de datos secundario para habilitar la migración tras error a esa ubicación en el caso de un desastre importante en la ubicación principal. Replicación geo puede tengan costes adicionales. Para las cuentas de prueba y desarrollo, generalmente no desea pagar geo replicación. Para obtener más información, vea [crear, administrar, o eliminar una cuenta de almacenamiento](../storage/storage-create-storage-account.md#replication-options).

5. Haga clic en **crear cuenta de almacenamiento**.

    ![Nueva cuenta de almacenamiento](./media/cloud-services-dotnet-get-started/newstorage.png)

    En la imagen, se crea una cuenta de almacenamiento con la dirección URL `contosoads.core.windows.net`.

### <a name="configure-the-solution-to-use-your-azure-sql-database-when-it-runs-in-azure"></a>Configurar la solución para usar la base de datos de SQL Azure cuando se ejecuta en Azure

El proyecto web y el proyecto de la función de trabajo tiene su propia cadena de conexión de base de datos, y cada uno debe para que apunten a la base de datos de SQL Azure cuando la aplicación se ejecuta en Azure.

Deberá usar una [transformación de Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) para la función web y una configuración de entorno de servicio de nube para la función de trabajo.

>[AZURE.NOTE] En esta sección y la siguiente sección almacenar credenciales en archivos de proyecto. [No almacene datos confidenciales en depósitos de código fuente pública](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets).

1. En el proyecto ContosoAdsWeb, abra el archivo de transformación de *Web.Release.config* para el archivo *Web.config* de la aplicación, elimine el bloque de comentario que contiene un `<connectionStrings>` elemento y a continuación, pegue el siguiente código en su lugar.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="{connectionstring}"
        providerName="System.Data.SqlClient" xdt:Transform="SetAttributes" xdt:Locator="Match(name)"/>
    </connectionStrings>
    ```

    Deje el archivo abierto para edición.

2. En el [portal de clásica Azure](http://manage.windowsazure.com), haga clic en **Bases de datos SQL** en el panel izquierdo, haga clic en la base de datos que de este tutorial que ha creado y, a continuación, haga clic en la pestaña **panel** y, a continuación, haga clic en **mostrar cadenas de conexión**.

    ![Mostrar las cadenas de conexión](./media/cloud-services-dotnet-get-started/showcs.png)

    El portal muestra cadenas de conexión, con un marcador de posición para la contraseña.

    ![Cadenas de conexión](./media/cloud-services-dotnet-get-started/connstrings.png)

4. En el archivo de transformación *Web.Release.config* , elimine `{connectionstring}` y pegar en su lugar la cadena de conexión de ADO.NET desde el portal de clásico de Azure.

5. En la cadena de conexión que pega en el archivo de transformación *Web.Release.config* , reemplace `{your_password_here}` con la contraseña que ha creado para la nueva base de datos SQL.

7. Guarde el archivo.  

6. Seleccione y copie la cadena de conexión (sin las comillas) para su uso en los siguientes pasos para configurar el proyecto de la función de trabajo.

5. En el **Explorador de soluciones**, debajo de **Roles** en el proyecto de servicio de nube, haga clic con el botón **ContosoAdsWorker** y, a continuación, haga clic en **Propiedades**.

    ![Propiedades de la función](./media/cloud-services-dotnet-get-started/rolepropertiesworker.png)

6. Haga clic en la pestaña **configuración** .

7. Cambiar la **Configuración del servicio** en la **nube**.

7. Seleccione el campo de **valor** para el `ContosoAdsDbConnectionString` configuración y, a continuación, pegue la cadena de conexión que ha copiado de la sección anterior del tutorial.

    ![Cadena de conexión de base de datos para la función de trabajo](./media/cloud-services-dotnet-get-started/workerdbcs.png)

7. Guarde los cambios.  

### <a name="configure-the-solution-to-use-your-azure-storage-account-when-it-runs-in-azure"></a>Configurar la solución para usar su cuenta de almacenamiento de Azure cuando se ejecuta en Azure

Cadenas de conexión de cuenta de almacenamiento de Azure para el proyecto de la función de web y el proyecto de la función de trabajo se almacenan en configuración del entorno del proyecto de servicio de nube. Para cada proyecto hay un conjunto diferente de valores que se utilizarán cuando la aplicación se ejecuta localmente y cuando se ejecuta en la nube. Deberá actualizar la configuración del entorno de nube para proyectos de rol de trabajo y de web.

1. En el **Explorador de soluciones**, haga clic con el botón **ContosoAdsWeb** en **Roles** en el proyecto **ContosoAdsCloudService** y, a continuación, haga clic en **Propiedades**.

    ![Propiedades de la función](./media/cloud-services-dotnet-get-started/roleproperties.png)

2. Haga clic en la pestaña **configuración** . En el cuadro de lista desplegable **Configuración del servicio** , elija la **nube**.

    ![Configuración de la nube](./media/cloud-services-dotnet-get-started/sccloud.png)

3. Seleccione la entrada de **StorageConnectionString** y verá un botón de puntos suspensivos (**...**) en el extremo derecho de la línea. Haga clic en el botón de puntos suspensivos para abrir el cuadro de diálogo **Crear la cadena de conexión de la cuenta de almacenamiento** .

    ![Abrir cuadro crear de cadena de conexión](./media/cloud-services-dotnet-get-started/opencscreate.png)

4. En el cuadro de diálogo **Crear cadena de conexión de almacenamiento** , haga clic en **la suscripción**, elija la cuenta de almacenamiento que creó anteriormente y, a continuación, haga clic en **Aceptar**. Si aún no ha iniciado sesión, se le pedirá sus credenciales de cuenta de Azure.

    ![Crear la cadena de conexión de almacenamiento](./media/cloud-services-dotnet-get-started/createstoragecs.png)

5. Guarde los cambios.

6. Siga el mismo procedimiento que ha usado para la `StorageConnectionString` cadena de conexión para establecer la `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` cadena de conexión.

    Esta cadena de conexión se utiliza para iniciar sesión.

7. Siga el mismo procedimiento que usó para la función **ContosoAdsWeb** para establecer dos cadenas de conexión para la función **ContosoAdsWorker** . No olvide establecer la **Configuración del servicio** en la **nube**.

La configuración del entorno de rol que ha configurado con la IU de Visual Studio se almacena en los siguientes archivos en el proyecto ContosoAdsCloudService:

* *ServiceDefinition.csdef* - define los nombres de configuración.
* *ServiceConfiguration.Cloud.cscfg* - proporciona valores para cuando se ejecuta la aplicación en la nube.
* *ServiceConfiguration.Local.cscfg* - proporciona valores para cuando la aplicación se ejecuta localmente.

Por ejemplo, el ServiceDefinition.csdef incluye las siguientes definiciones.

```xml
<ConfigurationSettings>
    <Setting name="StorageConnectionString" />
    <Setting name="ContosoAdsDbConnectionString" />
</ConfigurationSettings>
```

Y el archivo *ServiceConfiguration.Cloud.cscfg* incluye los valores de esos valores en Visual Studio.

```xml
<Role name="ContosoAdsWorker">
    <Instances count="1" />
    <ConfigurationSettings>
        <Setting name="StorageConnectionString" value="{yourconnectionstring}" />
        <Setting name="ContosoAdsDbConnectionString" value="{yourconnectionstring}" />
        <!-- other settings not shown -->

    </ConfigurationSettings>
    <!-- other settings not shown -->

</Role>
```

La `<Instances>` opción especifica el número de máquinas virtuales que Azure ejecutará código de la función de trabajador en. La sección [pasos siguientes](#next-steps) incluye vínculos a más información sobre el escalado de un servicio de nube

###  <a name="deploy-the-project-to-azure"></a>Implemente el proyecto de Azure

1.  En el **Explorador de soluciones**, haga clic en el proyecto de nube **ContosoAdsCloudService** y, a continuación, seleccione **Publicar**.

    ![Menú publicar](./media/cloud-services-dotnet-get-started/pubmenu.png)

2. En el paso de **Inicio de sesión** del Asistente para **Publicar aplicaciones de Azure** , haga clic en **siguiente**.

    ![Paso de inicio de sesión](./media/cloud-services-dotnet-get-started/pubsignin.png)

3. En el paso de la **configuración** del asistente, haga clic en **siguiente**.

    ![Paso de configuración](./media/cloud-services-dotnet-get-started/pubsettings.png)

    La configuración predeterminada en la ficha **Opciones avanzadas** es correcto para este tutorial. Para obtener información acerca de la ficha Opciones avanzadas, consulte el [Asistente para publicar aplicaciones de Azure](http://msdn.microsoft.com/library/hh535756.aspx).

4. En el paso de **Resumen** , haga clic en **Publicar**.

    ![Paso de resumen](./media/cloud-services-dotnet-get-started/pubsummary.png)

   Se abre la ventana de **Registro de actividad de Azure** en Visual Studio.

5. Haga clic en el icono de flecha derecha para expandir los detalles de la implementación.

    La implementación puede tardar hasta 5 minutos o más en completarse.

    ![Ventana de registro de actividad de Azure](./media/cloud-services-dotnet-get-started/waal.png)

6. Cuando el estado de implementación esté completado, haga clic en la **dirección URL Web app** para iniciar la aplicación.

7. Ahora puede probar la aplicación al crear, ver y modificar algunos anuncios, como hizo al ejecutar la aplicación localmente.

>[AZURE.NOTE] Cuando haya terminado de realizar pruebas, eliminar o detener el servicio de nube. Incluso si no usa el servicio de nube, acumulados cargos porque están reservados recursos de máquina virtual para el mismo. Y si dejarla en ejecución, cualquier persona que se encuentra la dirección URL puede crear y ver anuncios. En el [portal de clásica Azure](http://manage.windowsazure.com), vaya a la pestaña **panel** de su servicio de nube y, a continuación, haga clic en el botón **Eliminar** en la parte inferior de la página. Desea evitar temporalmente que otros usuarios acceso al sitio, haga clic en **Detener** en su lugar. En ese caso, cargos seguirá acumular. Puede seguir un procedimiento similar para eliminar la cuenta de almacenamiento y base de datos SQL cuando ya no necesite.

## <a name="create-the-application-from-scratch"></a>Creación de la aplicación desde cero

Si ya no ha descargado [la aplicación completada](http://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4), hágalo ahora. Deberá copiar los archivos desde el proyecto descargado en el nuevo proyecto.

La creación de la aplicación de Contoso anuncios implica los siguientes pasos:

* Crear una solución de Visual Studio de servicio de nube.
* Actualizar y agregar paquetes de NuGet.
* Establecer las referencias de proyecto.
* Configurar las cadenas de conexión.
* Agregar archivos de código.

Después de crea la solución, deberá revisar el código que sea único para proyectos de servicio de nube y Azure BLOB y colas.

### <a name="create-a-cloud-service-visual-studio-solution"></a>Crear una solución de Visual Studio de servicio de nube

1. En Visual Studio, elija **Nuevo proyecto** desde el menú **archivo** .

2. En el panel izquierdo del cuadro de diálogo **Nuevo proyecto** , expanda **Visual C#** y elija plantillas de **nube** y, a continuación, elija la plantilla de **Servicio de nube de Azure** .

3. Nombre del proyecto y la solución ContosoAdsCloudService y, a continuación, haga clic en **Aceptar**.

    ![Nuevo proyecto](./media/cloud-services-dotnet-get-started/newproject.png)

4. En el cuadro de diálogo **Nuevo servicio de nube de Azure** , agregue una función web y una función de trabajo. Nombre de la función web ContosoAdsWeb y el nombre de la función de trabajo ContosoAdsWorker. (Utilice el icono de lápiz en el panel derecho para cambiar los nombres predeterminados de las funciones).

    ![Nuevo proyecto de servicio de nube](./media/cloud-services-dotnet-get-started/newcsproj.png)

5. Cuando vea el cuadro de diálogo **Nuevo proyecto de ASP.NET** para la función de web, elija la plantilla MVC y, a continuación, haga clic en **Cambiar autenticación**.

    ![Cambio de la autenticación](./media/cloud-services-dotnet-get-started/chgauth.png)

7. En el cuadro de diálogo **Cambiar autenticación** , elija **Sin autenticación**y, a continuación, haga clic en **Aceptar**.

    ![Sin autenticación](./media/cloud-services-dotnet-get-started/noauth.png)

8. En el cuadro de diálogo **Nuevo proyecto** , haga clic en **Aceptar**.

9. En el **Explorador de soluciones**, haga clic en la solución (no uno de los proyectos) y elija **Agregar - nuevo proyecto**.

11. En el cuadro de diálogo **Agregar nuevo proyecto** , elija **Windows** en **Visual C#** en el panel izquierdo y, a continuación, haga clic en la plantilla de **Biblioteca de clases** .  

10. Nombre del proyecto *ContosoAdsCommon*y, a continuación, haga clic en **Aceptar**.

    Debe hacer referencia el contexto de marco de entidad y el modelo de datos de web y trabajador proyectos de rol. Como alternativa podría definir las clases relacionadas con el EF en el proyecto de rol web y hacen referencia a ese proyecto del proyecto de la función de trabajo. Pero, en el enfoque alternativo, el proyecto de la función de trabajo tendría una referencia a ensamblados de web que no es necesario.

### <a name="update-and-add-nuget-packages"></a>Actualizar y agregar paquetes de NuGet

1. Abrir el cuadro de diálogo **Administrar paquetes de NuGet** para la solución.

2. En la parte superior de la ventana, seleccione **las actualizaciones**.

3. Busque el paquete de *WindowsAzure.Storage* y si está en la lista, selecciónelo seleccionar los proyectos web y trabajador en actualizar y, a continuación, haga clic en **Actualizar**.

    La biblioteca de cliente de almacenamiento se actualiza con más frecuencia de plantillas de proyecto de Visual Studio, por lo que a menudo encontrará la versión en un debe proyectado recién creado se pueden actualizar.

4. En la parte superior de la ventana, seleccione **Examinar**.

5. Busque el paquete de NuGet *EntityFramework* e instálelo en todos los proyectos de tres.

6. Busque el paquete de NuGet *Microsoft.WindowsAzure.ConfigurationManager* e instálelo en el proyecto de la función de trabajo.

### <a name="set-project-references"></a>Establecer las referencias de proyecto

1. En el proyecto ContosoAdsWeb, establezca una referencia al proyecto ContosoAdsCommon. Haga clic en el proyecto ContosoAdsWeb y, a continuación, haga clic en **referencias** - **Agregar referencias**. En el cuadro de diálogo **Administrador de referencia** , seleccione **solución: proyectos** en el panel izquierdo, seleccione **ContosoAdsCommon**y, a continuación, haga clic en **Aceptar**.

2. En el proyecto ContosoAdsWorker, establezca una referencia al proyecto ContosAdsCommon.

    ContosoAdsCommon contendrá la entidad Framework modelo y contexto de clase de datos, que se utilizarán en front-end y back-end.

3. En el proyecto ContosoAdsWorker, establezca una referencia a `System.Drawing`.

    Este ensamblado se utiliza por el back-end para convertir imágenes en miniatura.

### <a name="configure-connection-strings"></a>Configurar cadenas de conexión

En esta sección para configurar el almacenamiento de Azure y SQL cadenas de conexión para probar localmente. Las instrucciones de implementación anteriormente en el tutorial explican cómo configurar las cadenas de conexión para cuando se ejecuta la aplicación en la nube.

1. En el proyecto ContosoAdsWeb, abra el archivo Web.config de la aplicación e inserte el siguiente `connectionStrings` elemento después de la `configSections` elemento.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Si está usando Visual Studio de 2015, reemplace "v11.0" por "MSSQLLocalDB".

2. Guarde los cambios.

3. En el proyecto ContosoAdsCloudService, haga clic con el botón ContosoAdsWeb en **Roles**y, a continuación, haga clic en **Propiedades**.

    ![Propiedades de la función](./media/cloud-services-dotnet-get-started/roleproperties.png)

4. En la ventana de propiedades **ContosAdsWeb [función]** , haga clic en la pestaña **configuración** y, a continuación, haga clic en **Agregar valor**.

    Deje conjunto a **Todas las configuraciones**de **Configuración del servicio** .

5. Agregar una nueva opción denominada *StorageConnectionString*. Establecer **tipo de** en la *cadena de conexión*y establecer **valor** en *UseDevelopmentStorage = true*.

    ![Nueva cadena de conexión](./media/cloud-services-dotnet-get-started/scall.png)

6. Guarde los cambios.

7. Siga el mismo procedimiento para agregar una cadena de conexión de almacenamiento en las propiedades de la función ContosoAdsWorker.

8. En la ventana de propiedades **ContosoAdsWorker [función]** , agregue otra cadena de conexión:

    * Nombre: ContosoAdsDbConnectionString
    * Tipo: cadena
    * Value: Pegue la misma cadena de conexión que usó para el proyecto de la función de web. (El ejemplo siguiente es de 2013 de Visual Studio, no olvide cambiar el origen de datos si copia este ejemplo y usa 2015 de Visual Studio).

        ```
        Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;
        ```

### <a name="add-code-files"></a>Agregar archivos de código

En esta sección Copiar archivos de código de la solución descargada en la solución nueva. Las siguientes secciones se muestre y explique las partes principales de este código.

Para agregar archivos a un proyecto o una carpeta, haga clic en el proyecto o la carpeta y haga clic en **Agregar** - **Elemento existente**. Seleccione los archivos que desee y, a continuación, haga clic en **Agregar**. Si se le solicita si desea reemplazar los archivos existentes, haga clic en **Sí**.

3. En el proyecto ContosoAdsCommon, elimine el archivo *Class1.cs* y agregue en su lugar los archivos *Ad.cs* y *ContosoAdscontext.cs* desde el proyecto descargado.

3. En el proyecto ContosoAdsWeb, agregue los siguientes archivos desde el proyecto descargado.
    - *Global.asax.cs*.  
    - En la carpeta *Views\Shared* : * \_Layout.cshtml*.
    - En la carpeta *Views\Home* : *Index.cshtml*.
    - En la carpeta *controladores* : *AdController.cs*.
    - En la carpeta *Views\Ad* (cree la carpeta primero): cinco *.cshtml* archivos.

3. En el proyecto ContosoAdsWorker, agregue *WorkerRole.cs* desde el proyecto descargado.

Ahora puede crear y ejecutar la aplicación, tal como se indica más adelante en el tutorial, y utilizará la aplicación de base de datos local y recursos de emulador de almacenamiento.

Las siguientes secciones explican el código asociado a trabajar con el entorno de Azure, BLOB y colas. Este tutorial no explica cómo crear controladores MVC y vistas mediante scaffolding, cómo escribir código de marco de entidad que funciona con bases de datos de SQL Server o los conceptos básicos de la programación asincrónica en ASP.NET 4.5. Para obtener información acerca de estos temas, consulte los siguientes recursos:

* [Introducción a 5 de MVC](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Introducción a 6 EF y MVC 5](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc)
* [Introducción a asincrónico programación .NET 4.5](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).

### <a name="contosoadscommon---adcs"></a>ContosoAdsCommon - Ad.cs

El archivo Ad.cs define una enumeración de categorías de ad y una clase de entidad POCO para obtener información de ad.

```csharp
public enum Category
{
    Cars,
    [Display(Name="Real Estate")]
    RealEstate,
    [Display(Name = "Free Stuff")]
    FreeStuff
}

public class Ad
{
    public int AdId { get; set; }

    [StringLength(100)]
    public string Title { get; set; }

    public int Price { get; set; }

    [StringLength(1000)]
    [DataType(DataType.MultilineText)]
    public string Description { get; set; }

    [StringLength(1000)]
    [DisplayName("Full-size Image")]
    public string ImageURL { get; set; }

    [StringLength(1000)]
    [DisplayName("Thumbnail")]
    public string ThumbnailURL { get; set; }

    [DataType(DataType.Date)]
    [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
    public DateTime PostedDate { get; set; }

    public Category? Category { get; set; }
    [StringLength(12)]
    public string Phone { get; set; }
}
```

### <a name="contosoadscommon---contosoadscontextcs"></a>ContosoAdsCommon - ContosoAdsContext.cs

La clase ContosoAdsContext especifica que se utiliza la clase de Ad en una colección de DbSet, entidad Framework se guardará en una base de datos SQL.

```csharp
public class ContosoAdsContext : DbContext
{
    public ContosoAdsContext() : base("name=ContosoAdsContext")
    {
    }
    public ContosoAdsContext(string connString)
        : base(connString)
    {
    }
    public System.Data.Entity.DbSet<Ad> Ads { get; set; }
}
```

La clase tiene dos constructores. La primera de ellas se utiliza en el proyecto web y especifica el nombre de una cadena de conexión que se almacena en el archivo Web.config. El segundo constructor permite pasar de la cadena de conexión real. Es necesario para el proyecto de rol de trabajo, ya que no tiene un archivo Web.config. Se ha visto anteriormente donde se almacena esta cadena de conexión y verá más adelante cómo el código recupera la cadena de conexión cuando se crea una instancia de la clase DbContext.

### <a name="contosoadsweb---globalasaxcs"></a>ContosoAdsWeb - Global.asax.cs

Código que se llama desde el `Application_Start` método crea un contenedor de blobs de *imágenes* y de una cola de *imágenes* si aún no existen. Así se garantiza que cada vez que empiece con una nueva cuenta de almacenamiento o empezar a usar el emulador de almacenamiento en un equipo nuevo, el contenedor de blob necesarios y la cola se crearán automáticamente.

El código obtiene acceso a la cuenta de almacenamiento, con la cadena de conexión de almacenamiento desde el archivo *.cscfg* .

```csharp
var storageAccount = CloudStorageAccount.Parse
    (RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
```

A continuación, obtiene una referencia al contenedor de blobs de *imágenes* , se crea el contenedor si ya no existe y establece permisos de acceso en el nuevo contenedor. De forma predeterminada, nuevos contenedores solo permiten a los clientes con credenciales de cuenta de almacenamiento acceso BLOB. El sitio Web necesita el BLOB sea público para que pueda mostrar imágenes con direcciones URL que apuntan a los BLOB de imagen.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var imagesBlobContainer = blobClient.GetContainerReference("images");
if (imagesBlobContainer.CreateIfNotExists())
{
    imagesBlobContainer.SetPermissions(
        new BlobContainerPermissions
        {
            PublicAccess =BlobContainerPublicAccessType.Blob
        });
}
```

Código similar Obtiene una referencia a la cola de *imágenes* y crea una nueva cola. En este caso no se necesita ningún cambio de permisos.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
var imagesQueue = queueClient.GetQueueReference("images");
imagesQueue.CreateIfNotExists();
```

### <a name="contosoadsweb---layoutcshtml"></a>ContosoAdsWeb - \_Layout.cshtml

El archivo *_Layout.cshtml* establece el nombre de la aplicación en el encabezado y pie de página y crea una entrada de menú "Anuncios".

### <a name="contosoadsweb---viewshomeindexcshtml"></a>ContosoAdsWeb - Views\Home\Index.cshtml

El archivo *Views\Home\Index.cshtml* muestra los vínculos de categoría en la página principal. Los vínculos de pasan el valor entero de la `Category` enumeración en una variable de cadena de consulta a la página de índice de anuncios.

```razor
<li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
<li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
<li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
<li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>
```

### <a name="contosoadsweb---adcontrollercs"></a>ContosoAdsWeb - AdController.cs

En el *AdController.cs* archivo las llamadas de constructor la `InitializeStorage` método para crear objetos de la biblioteca de cliente de almacenamiento de Azure que proporcionan una API para trabajar con BLOB y colas.

A continuación, el código obtiene una referencia al contenedor de blobs de *imágenes* como hemos visto anteriormente en *Global.asax.cs*. Mientras que establece una predeterminada [Reintentar directiva](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) apropiado para una aplicación web. La directiva predeterminada de intentos de interrupción exponencial podría bloquea la aplicación web durante más de un minuto en repetidos reintentos de un error transitorio. La directiva de reintento especificada aquí espera 3 segundos después de cada uno de ellos intente para intentos hasta 3.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesBlobContainer = blobClient.GetContainerReference("images");
```

Código similar Obtiene una referencia a la cola de *imágenes* .

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesQueue = queueClient.GetQueueReference("images");
```

La mayoría del código controlador es típico para trabajar con un modelo de datos de entidad Framework con una clase DbContext. Una excepción es el HttpPost `Create` método, que se carga un archivo y se guarda en el almacenamiento de blobs. El cuaderno modelo proporciona un objeto de [HttpPostedFileBase](http://msdn.microsoft.com/library/system.web.httppostedfilebase.aspx) al método.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Create(
    [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
    HttpPostedFileBase imageFile)
```

Si el usuario selecciona un archivo para cargar, el código carga el archivo, guarda en un blob y actualiza el registro de la base de datos de Ad con una dirección URL que señala al objeto binario.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    blob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = blob.Uri.ToString();
}
```

El código que realiza la carga se encuentra en la `UploadAndSaveBlobAsync` método. Crea un nombre GUID para el blob, las cargas y guarda el archivo y devuelve una referencia al objeto binario guardada.

```csharp
private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
{
    string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
    CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
    using (var fileStream = imageFile.InputStream)
    {
        await imageBlob.UploadFromStreamAsync(fileStream);
    }
    return imageBlob;
}
```

Después de la HttpPost `Create` método blob las cargas y actualiza la base de datos, crea un mensaje de cola para informar ese proceso de back-end que una imagen está lista para la conversión a una miniatura.

```csharp
string queueMessageString = ad.AdId.ToString();
var queueMessage = new CloudQueueMessage(queueMessageString);
await queue.AddMessageAsync(queueMessage);
```

El código para el HttpPost `Edit` método es similar, excepto que si el usuario selecciona un nuevo archivo de imagen se debe eliminar cualquier BLOB que ya existe.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    await DeleteAdBlobsAsync(ad);
    imageBlob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = imageBlob.Uri.ToString();
}
```

En el ejemplo siguiente se muestra el código que elimina BLOB cuando se elimina un anuncio.

```csharp
private async Task DeleteAdBlobsAsync(Ad ad)
{
    if (!string.IsNullOrWhiteSpace(ad.ImageURL))
    {
        Uri blobUri = new Uri(ad.ImageURL);
        await DeleteAdBlobAsync(blobUri);
    }
    if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
    {
        Uri blobUri = new Uri(ad.ThumbnailURL);
        await DeleteAdBlobAsync(blobUri);
    }
}
private static async Task DeleteAdBlobAsync(Uri blobUri)
{
    string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
    CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
    await blobToDelete.DeleteAsync();
}
```

### <a name="contosoadsweb---viewsadindexcshtml-and-detailscshtml"></a>ContosoAdsWeb - Views\Ad\Index.cshtml y Details.cshtml

El archivo *Index.cshtml* muestra miniaturas con el resto de los datos de ad.

```razor
<img src="@Html.Raw(item.ThumbnailURL)" />
```

El archivo *Details.cshtml* muestra la imagen de tamaño completo.

```razor
<img src="@Html.Raw(Model.ImageURL)" />
```

### <a name="contosoadsweb---viewsadcreatecshtml-and-editcshtml"></a>ContosoAdsWeb - Views\Ad\Create.cshtml y Edit.cshtml

Los archivos *Create.cshtml* y *Edit.cshtml* especifican la codificación de formulario que habilita el controlador de obtener la `HttpPostedFileBase` objeto.

```razor
@using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))
```

Un `<input>` elemento indica el explorador para proporcionar un cuadro de diálogo de selección de archivo.

```razor
<input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />
```

### <a name="contosoadsworker---workerrolecs---onstart-method"></a>Método de ContosoAdsWorker - WorkerRole.cs - OnStart

Las llamadas de entorno de rol de trabajo Azure la `OnStart` método en la `WorkerRole` clase cuando el rol de trabajo se introducción y, a continuación, llama a la `Run` método cuando la `OnStart` método finalice.

La `OnStart` método obtiene la cadena de conexión de base de datos desde el archivo *.cscfg* y pasa a la clase de entidad Framework DbContext. El proveedor SQLClient se usa de forma predeterminada, por lo que no tiene el proveedor que se especifique.

```csharp
var dbConnString = CloudConfigurationManager.GetSetting("ContosoAdsDbConnectionString");
db = new ContosoAdsContext(dbConnString);
```

Después de que el método obtiene una referencia a la cuenta de almacenamiento y crea el contenedor de blobs de Windows y la cola si no existen. El código que es similar a lo que ya hemos visto en la función web `Application_Start` método.

### <a name="contosoadsworker---workerrolecs---run-method"></a>ContosoAdsWorker - WorkerRole.cs - Ejecutar método

La `Run` método se llama cuando la `OnStart` método finaliza su trabajo de inicialización. El método ejecuta un bucle infinito que busca nuevos mensajes de la cola y los procesa cuando lleguen.

```csharp
public override void Run()
{
    CloudQueueMessage msg = null;

    while (true)
    {
        try
        {
            msg = this.imagesQueue.GetMessage();
            if (msg != null)
            {
                ProcessQueueMessage(msg);
            }
            else
            {
                System.Threading.Thread.Sleep(1000);
            }
        }
        catch (StorageException e)
        {
            if (msg != null && msg.DequeueCount > 5)
            {
                this.imagesQueue.DeleteMessage(msg);
            }
            System.Threading.Thread.Sleep(5000);
        }
    }
}
```

Después de cada iteración del bucle, si se ha encontrado ninguna cola de mensajes, el programa se suspende durante un segundo. Impide que la función de trabajador provocar excesivo costos transacción de almacenamiento y tiempo de CPU. El equipo de recomendación de cliente de Microsoft indica un artículo sobre un desarrollador que haya olvidado para incluir esto, implementado en producción y a la izquierda para vacaciones. Cuando se consiguió atrás, su supervisión costar más que las vacaciones.

A veces el contenido de una cola de mensajes produce un error en el proceso. Esto se denomina *mensaje dudoso*y si simplemente registrado un error y reinicie el bucle, indefinidamente podría intentar procesar dicho mensaje.  Por tanto, el bloqueo de capturas incluye if instrucción que comprueba cuántas veces la aplicación ha intentado procesar el mensaje actual y si han pasado más de 5 veces, se elimina el mensaje de la cola.

`ProcessQueueMessage`se llama cuando se encuentra un mensaje de cola.

```csharp
private void ProcessQueueMessage(CloudQueueMessage msg)
{
    var adId = int.Parse(msg.AsString);
    Ad ad = db.Ads.Find(adId);
    if (ad == null)
    {
        throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", adId.ToString()));
    }

    CloudBlockBlob inputBlob = this.imagesBlobContainer.GetBlockBlobReference(ad.ImageURL);

    string thumbnailName = Path.GetFileNameWithoutExtension(inputBlob.Name) + "thumb.jpg";
    CloudBlockBlob outputBlob = this.imagesBlobContainer.GetBlockBlobReference(thumbnailName);

    using (Stream input = inputBlob.OpenRead())
    using (Stream output = outputBlob.OpenWrite())
    {
        ConvertImageToThumbnailJPG(input, output);
        outputBlob.Properties.ContentType = "image/jpeg";
    }

    ad.ThumbnailURL = outputBlob.Uri.ToString();
    db.SaveChanges();

    this.imagesQueue.DeleteMessage(msg);
}
```

Este código lee la base de datos para obtener la dirección URL de imagen, convierte la imagen en miniatura, guarda la miniatura de un blob, actualiza la base de datos con la dirección URL de blobs de miniaturas y elimina el mensaje de cola.

>[AZURE.NOTE] El código de la `ConvertImageToThumbnailJPG` método utiliza las clases en el espacio de nombres para simplificar. Sin embargo, las clases de este espacio de nombres se han diseñado para usar con formularios de Windows. No se admiten para su uso en un servicio Windows o ASP.NET. Para obtener más información acerca de las opciones de procesamiento de imagen, vea [Dinámicos de generación de imágenes](http://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx) y [Profundidad dentro de cambio de tamaño](http://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na).

## <a name="troubleshooting"></a>Solución de problemas

En caso de que algo no funciona mientras está siguiendo las instrucciones de este tutorial, estos son algunos errores comunes y cómo resolverlos.

### <a name="serviceruntimeroleenvironmentexception"></a>ServiceRuntime.RoleEnvironmentException

La `RoleEnvironment` Azure proporciona objeto cuando se ejecuta una aplicación en Azure o cuando se ejecuta localmente mediante el emulador cálculo Azure.  Si recibe este error cuando se está ejecutando localmente, asegúrese de que ha establecido el proyecto ContosoAdsCloudService como proyecto de inicio. Esto configura el proyecto para ejecutar el uso del cálculo Azure.

Una de las cosas que la aplicación utiliza la RoleEnvironment de Azure para es obtener los valores de cadena que se almacenan en los archivos *.cscfg* , por lo que otra causa de esta excepción es una cadena de conexión que falta de la conexión. Asegúrese de que crean configuraciones Local y la configuración de StorageConnectionString de nube tanto en el proyecto ContosoAdsWeb y ha creado dos cadenas de conexión para las configuraciones del proyecto ContosoAdsWorker. Si hace una búsqueda **Buscar todos** para StorageConnectionString en toda la solución, debería verlo 9 veces en archivos de 6.

### <a name="cannot-override-to-port-xxx-new-port-below-minimum-allowed-value-8080-for-protocol-http"></a>No puede reemplazar al puerto largo y LARGOB. Nuevo puerto por debajo del valor mínimo permitido 8080 para el protocolo http

Pruebe a cambiar el número de puerto que se utiliza el proyecto web. Haga clic en el proyecto ContosoAdsWeb y, a continuación, haga clic en **Propiedades**. Haga clic en la ficha **Web** y, a continuación, cambie el número de puerto en la configuración de la **Dirección Url de Project** .

Para otra alternativa que puede resolver el problema, consulte la sección siguiente.

### <a name="other-errors-when-running-locally"></a>Otros errores cuando se ejecuta localmente

Nube nueva predeterminada proyectos de servicio usan la express emulador cálculo Azure simular el entorno de Azure. Esta es una versión ligera de emulador de cálculo completa, y en algunas condiciones el emulador completo funcionará cuando la versión express no.  

Para cambiar el proyecto para usarlo completa, haga clic en el proyecto ContosoAdsCloudService y, a continuación, haga clic en **Propiedades**. En la ventana **Propiedades** haga clic en la ficha **Web** y, a continuación, haga clic en el botón de radio **Emulador completa de uso** .

Para ejecutar la aplicación con el emulador completo, debe abrir Visual Studio con privilegios de administrador.

## <a name="next-steps"></a>Pasos siguientes

La aplicación de Contoso anuncios deliberadamente se guardará simple para obtener un tutorial Introducción. Por ejemplo, no implementar la [inserción de dependencia](http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection) o el [repositorio y unidad de patrones de trabajo](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), no [usan una interfaz para iniciar sesión](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log), no utilice [EF código primera migraciones](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) para administrar los cambios del modelo de datos o [La resistencia de conexión EF](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) para administrar los errores de red transitorias y así sucesivamente.

Estas son algunas aplicaciones de ejemplo de servicio de nube que muestran más prácticas de codificación de reales, enumeradas de menos complejo al más complejo:

* [PhluffyFotos](http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31). Similar en concepto a los anuncios de Contoso pero implementa más características y prácticas de codificación más reales.
* [Aplicación de varios niveles de servicio de nube azure con tablas, colas y BLOB](http://code.msdn.microsoft.com/windowsazure/Windows-Azure-Multi-Tier-eadceb36). Presenta colas, así como BLOB y tablas de almacenamiento de Azure. Basado en una versión anterior de Azure SDK para .NET, necesitarán algunas modificaciones para que funcionen con la versión actual.
* [Conceptos básicos de servicio de nube de Microsoft Azure](http://code.msdn.microsoft.com/Cloud-Service-Fundamentals-4ca72649). Un ejemplo completo que muestra una amplia gama de recomendaciones, producidos por el grupo Microsoft Patterns y procedimientos recomendados.

Para obtener información general sobre el desarrollo de la nube, consulte [Creación de aplicaciones de nube reales con Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction).

Para un vídeo de introducción a las mejores prácticas de almacenamiento de Azure y patrones, vea [Microsoft Azure almacenamiento: ¿qué es nuevo, recomendaciones y patrones](http://channel9.msdn.com/Events/Build/2014/3-628).

Para obtener más información, consulte los siguientes recursos:

* [Servicios de nube de Azure parte 1: Introducción](http://justazure.com/microsoft-azure-cloud-services-part-1-introduction/)
* [Cómo administrar servicios en la nube](cloud-services-how-to-manage.md)
* [Almacenamiento de Azure](/documentation/services/storage/)
* [Cómo elegir un proveedor de servicios de nube](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/)
