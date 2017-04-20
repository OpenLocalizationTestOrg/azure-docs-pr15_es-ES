<properties 
    pageTitle="Implementar WebJobs mediante Visual Studio" 
    description="Obtenga información sobre cómo implementar WebJobs de Azure en Azure aplicación de servicio Web Apps con Visual Studio." 
    services="app-service" 
    documentationCenter="" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="na" 
    ms.date="04/27/2016" 
    ms.author="tdykstra"/>

# <a name="deploy-webjobs-using-visual-studio"></a>Implementar WebJobs mediante Visual Studio

## <a name="overview"></a>Información general

En este tema se explica cómo usar Visual Studio para implementar un proyecto de aplicación de consola en una aplicación web en la [Aplicación de servicio](http://go.microsoft.com/fwlink/?LinkId=529714) , como un [WebJob de Azure](http://go.microsoft.com/fwlink/?LinkId=390226). Para obtener información sobre cómo implementar WebJobs mediante el [Portal de Azure](https://portal.azure.com), vea [tareas en segundo plano de ejecutar con WebJobs](web-sites-create-web-jobs.md).

Cuando Visual Studio implementa un proyecto de aplicación de consola WebJobs habilitado, realiza dos tareas:

* Copia los archivos en tiempo de ejecución a la carpeta correspondiente en la aplicación web (*App_Data o trabajos/continuo* para WebJobs continuo, *App_Data, trabajos/activa* para WebJobs programada y a petición).
* Configurará [los trabajos del programador de Azure](#scheduler) WebJobs que están programadas para que se ejecute en un momento determinado. (Esto no es necesario para WebJobs continua).

Un proyecto habilitado WebJobs tiene los siguientes elementos agregados:

* El paquete de NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* Archivo [settings.json publicar webjob](#publishsettings) que contiene la configuración de implementación y programador. 

![Diagrama que muestra lo que se agregan a una aplicación de consola para habilitar la implementación como un WebJob](./media/websites-dotnet-deploy-webjobs/convert.png)

Puede agregar estos elementos a un proyecto de aplicación de consola existente o usar una plantilla para crear un nuevo proyecto de aplicación de consola WebJobs habilitado. 

Puede implementar un proyecto como una WebJob por sí mismo o vincular a un proyecto web para que se implemente automáticamente cada vez que se implementa el proyecto web. Para vincular proyectos, Visual Studio incluye el nombre del proyecto habilitado WebJobs en un archivo de [webjobs list.json](#webjobslist) en el proyecto web.

![Diagrama que muestra project WebJob vincular al proyecto web](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>Requisitos previos

Características de implementación de WebJobs están disponibles en Visual Studio de 2015 cuando se instala el SDK de Azure para .NET:

* [Azure SDK para .NET (Visual Studio 2015)](http://go.microsoft.com/fwlink/?linkid=518003).

## <a id="convert"></a>Habilitar la implementación de WebJobs para un proyecto de aplicación de consola existente

Tiene dos opciones:

* [Habilitar la distribución automática con un proyecto web](#convertlink).

    Configurar un proyecto existente de la aplicación de consola de modo que instala automáticamente como un WebJob cuando se implementa un proyecto web. Use esta opción si desea ejecutar el WebJob en la misma aplicación web en el que se ejecute la aplicación web relacionados.

* [Habilitar la implementación sin un proyecto web](#convertnolink).

    Configurar un proyecto de aplicación de consola existente para implementar como un WebJob por sí mismo, sin vínculo a un proyecto web. Use esta opción si desea ejecutar una WebJob en una aplicación web, con ninguna aplicación web que se ejecuta en la aplicación web. Es recomendable hacer esto para poder ajustar los recursos WebJob independientemente de los recursos de la aplicación web.

### <a id="convertlink"></a>Habilitar la distribución automática de WebJobs con un proyecto web
  
1. Haga clic en el proyecto web en el **Explorador de soluciones**y, a continuación, haga clic en **Agregar** > **Proyecto existente como WebJob de Azure**.

    ![Proyecto existente como WebJob de Azure](./media/websites-dotnet-deploy-webjobs/eawj.png)
    
    Aparece el cuadro de diálogo [Agregar WebJob de Azure](#configure) .

1. En la lista desplegable **nombre del proyecto** , seleccione el proyecto de aplicación de consola de agregar como un WebJob.

    ![Seleccione el proyecto en el cuadro de diálogo de agregar WebJob de Azure](./media/websites-dotnet-deploy-webjobs/aaw1.png)

2. Complete el cuadro de diálogo [Agregar WebJob de Azure](#configure) y, a continuación, haga clic en **Aceptar**. 

### <a id="convertnolink"></a>Habilitar la implementación de WebJobs sin un proyecto web
  
1. Haga clic en el proyecto de aplicación de consola del **Explorador de soluciones**y, a continuación, haga clic en **Publicar como WebJob de Azure**. 

    ![Publicar como WebJob de Azure](./media/websites-dotnet-deploy-webjobs/paw.png)
    
    Aparece el cuadro de diálogo [Agregar WebJob de Azure](#configure) , con el proyecto seleccionado en el cuadro **nombre del proyecto** .

2.  Complete el cuadro de diálogo [Agregar WebJob de Azure](#configure) y, a continuación, haga clic en **Aceptar**.

    Aparecerá el Asistente de **Publicación Web** .  Si no desea publicar inmediatamente, cierre al asistente. La configuración que ha introducido se guarda para cuando desee [implementar el proyecto](#deploy).

## <a id="create"></a>Crear un nuevo proyecto habilitado WebJobs

Para crear un nuevo proyecto habilitado WebJobs, puede usar la plantilla de proyecto de aplicación de consola y habilitar la implementación de WebJobs como se explica en [la sección anterior](#convert). Como alternativa, puede usar la plantilla de proyecto nuevo WebJobs:

* [Usar la plantilla de proyecto nuevo WebJobs para un WebJob independiente](#createnolink)

    Crear un proyecto y configúrelo para implementar por sí mismo como un WebJob sin vínculo a un proyecto web. Use esta opción si desea ejecutar una WebJob en una aplicación web, con ninguna aplicación web que se ejecuta en la aplicación web. Es recomendable hacer esto para poder ajustar los recursos WebJob independientemente de los recursos de la aplicación web.

* [Usar la plantilla de proyecto nuevo WebJobs para un WebJob vinculada a un proyecto de web](#createlink)

    Crear un proyecto que está configurado para implementar automáticamente como un WebJob cuando se implementa un proyecto web en la misma solución. Use esta opción si desea ejecutar el WebJob en la misma aplicación web en el que se ejecute la aplicación web relacionados.

> [AZURE.NOTE] La plantilla de proyecto nuevo WebJobs automáticamente instala paquetes de NuGet e incluye código en *Program.cs* para el [SDK de WebJobs](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Si no desea usar el SDK WebJobs o desea usar un WebJob programada en lugar de continuo, quitar o cambiar la `host.RunAndBlock` instrucción en *Program.cs*.

### <a id="createnolink"></a>Usar la plantilla de proyecto nuevo WebJobs para un WebJob independiente
  
1. Haga clic en **archivo** > **Nuevo proyecto**y, a continuación, en el cuadro de diálogo **Nuevo proyecto** , haga clic en **nube** > **WebJob de Microsoft Azure**.

    ![Nuevo cuadro de diálogo de proyecto que muestra la plantilla de WebJob](./media/websites-dotnet-deploy-webjobs/np.png)
    
2. Siga las instrucciones que se muestra una versión anterior para [hacer que la aplicación de consola de un proyecto WebJobs independiente del proyecto](#convertnolink).

### <a id="createlink"></a>Usar la plantilla de proyecto nuevo WebJobs para un WebJob vinculada a un proyecto de web

1. Haga clic en el proyecto web en el **Explorador de soluciones**y, a continuación, haga clic en **Agregar** > **Nuevo proyecto WebJob de Azure**.

    ![Nueva entrada de menú proyecto WebJob de Azure](./media/websites-dotnet-deploy-webjobs/nawj.png)

    Aparece el cuadro de diálogo [Agregar WebJob de Azure](#configure) .

2. Complete el cuadro de diálogo [Agregar WebJob de Azure](#configure) y, a continuación, haga clic en **Aceptar**.

## <a id="configure"></a>El cuadro de diálogo Agregar WebJob de Azure

El cuadro de diálogo **Agregar WebJob de Azure** le permite introducir nombre de WebJob y la configuración para su WebJob de la programación. 

![Agregar el cuadro de diálogo de WebJob de Azure](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Los campos de este cuadro de diálogo corresponden a los campos en el cuadro de diálogo **Nuevo trabajo** del Portal de Azure. Para obtener más información, vea [tareas en segundo plano de ejecutar con WebJobs](web-sites-create-web-jobs.md).

Para un WebJob programada (no disponible para WebJobs continuo), Visual Studio crea una colección de trabajo del [Programador de Azure](/services/scheduler/) si todavía no existe uno y, a continuación, crea un trabajo en la colección:

* La colección de trabajo de programador se denomina *WebJobs-{regionname}* donde *{regionname}* hace referencia a la región de la web app está alojado en. Por ejemplo: WebJobs WestUS.
* El trabajo del programador se denomina *{webappname}-{webjobname}*. Por ejemplo: MyWebJob MyWebApp. 
 
>[AZURE.NOTE]
> 
>* Para obtener información acerca de la implementación de línea de comandos, consulte [activación de línea de comandos o entrega continua de Azure WebJobs](/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
>* Si configura una **Tarea periódica** y establezca la frecuencia de repetición en un número de minutos, el servicio Programador de Azure no está libre. Otras frecuencias (días, horas etc.) son gratuitas.
>* Si implementa una WebJob y, a continuación, decide que desea cambiar el tipo de WebJob y bola, debe eliminar el archivo settings.json publicar webjobs. Esto hará que Visual Studio mostrar las opciones de publicación de nuevo, para que pueda cambiar el tipo de WebJob.
>* Si implementa una WebJob y posteriormente cambiar el modo de ejecución de continuo a no continua o viceversa, Visual Studio crea un nuevo WebJob en Azure al implementar de nuevo. Si cambiar otras opciones de configuración de programación pero dejar ejecuta el modo de la misma o cambiar entre programada y a petición, Visual Studio actualiza el trabajo existente en lugar de crear uno nuevo.

## <a id="publishsettings"></a>settings.json publicar webjob

Al configurar una aplicación de consola de implementación de WebJobs, Visual Studio instala el paquete de NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) y almacena información de programación en un archivo de *settings.json publicar webjob* en la carpeta de *Propiedades* del proyecto del proyecto WebJobs. Aquí tiene un ejemplo de dicho archivo:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "2014-06-23T00:00:00-08:00",
          "endTime": "2014-06-27T00:00:00-08:00",
          "jobRecurrenceFrequency": "Minute",
          "interval": 5,
          "runMode": "Scheduled"
        }

Este archivo se puede editar directamente y Visual Studio proporciona IntelliSense. El esquema de archivo se almacena en [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) y se podrán ver allí.  

>[AZURE.NOTE]
>
>* Si configura una **Tarea periódica** y establezca la frecuencia de repetición en un número de minutos, el servicio Programador de Azure no está libre. Otras frecuencias (días, horas etc.) son gratuitas.

## <a id="webjobslist"></a>webjobs list.json

Cuando se vincula un proyecto habilitado WebJobs a un proyecto web, Visual Studio almacena el nombre del proyecto WebJobs en un archivo de *webjobs list.json* en la carpeta de *Propiedades* del proyecto de web. La lista puede contener varios proyectos WebJobs, tal como se muestra en el ejemplo siguiente:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Este archivo se puede editar directamente y Visual Studio proporciona IntelliSense. El esquema de archivo se almacena en [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) y se podrán ver allí.
  
## <a id="deploy"></a>Implementar un proyecto WebJobs

Un proyecto WebJobs que ha vinculado a un proyecto web implementa automáticamente con el proyecto web. Para obtener información acerca de la implementación de proyectos web, vea [cómo implementar en aplicaciones Web](web-sites-deploy.md).

Implementar un proyecto WebJobs por sí mismo, haga clic en el proyecto en el **Explorador de soluciones**y haga clic en **Publicar como WebJob de Azure**. 

![Publicar como WebJob de Azure](./media/websites-dotnet-deploy-webjobs/paw.png)
    
Para un WebJob independiente, el mismo asistente de **Publicación Web** que se usa para los proyectos web aparece, pero con menos opciones disponibles para cambiar.

## <a id="nextsteps"></a>Pasos siguientes

En este artículo se explica cómo implementar WebJobs mediante Visual Studio. Para obtener más información sobre cómo implementar WebJobs de Azure, vea [implementación de Azure WebJobs - recursos recomendado](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources#deploying).
