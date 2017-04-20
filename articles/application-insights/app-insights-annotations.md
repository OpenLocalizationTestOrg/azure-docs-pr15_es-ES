<properties
    pageTitle="Suelte anotaciones para impresiones de aplicación | Microsoft Azure"
    description="Agregar implementación o crear marcadores a los gráficos de explorador métricas en perspectivas de aplicación."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="awills"/>

# <a name="release-annotations-in-application-insights"></a>Anotaciones de lanzamiento de perspectivas de aplicación

Suelte anotaciones en los gráficos se muestran [Métricas Explorer](app-insights-metrics-explorer.md) donde ha implementado una nueva compilación. Hacen que sea más fácil ver si los cambios tenían ningún efecto en el rendimiento de la aplicación. Se pueden crear automáticamente por el [sistema de compilación de Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs)y también puede [crearlos de PowerShell](#create-annotations-from-powershell).

![Ejemplo de anotaciones con correlación visible con el tiempo de respuesta del servidor](./media/app-insights-annotations/00.png)

Anotaciones de lanzamiento son una característica de la compilación en la nube y suelte el servicio de Visual Studio Team Services. 

## <a name="install-the-annotations-extension-one-time"></a>Instale la extensión de anotaciones (una vez)

Para poder crear anotaciones de versión, debe instalar una de las muchas extensiones de servicio de grupo disponibles en el catálogo de soluciones de Visual Studio.

1. Inicie sesión en su proyecto de [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online) .
2. En Visual Studio mercado, en [obtener la extensión de anotaciones de versión](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)y agregar a su cuenta de servicios del equipo.

![AT superior derecha de la página web de Team Services, abrir Marketplace. Seleccione Visual de los servicios de equipo y, a continuación, en generar y versión, elija ver más.](./media/app-insights-annotations/10.png)

Solo debe hacer esto una vez para su cuenta de Visual Studio Team Services. Anotaciones de versión ahora se pueden configurar para cualquier proyecto en su cuenta. 

## <a name="get-an-api-key-from-application-insights"></a>Obtener una clave API de aplicación perspectivas

Debe hacer esto para cada plantilla de versión que desea crear anotaciones de lanzamiento.


1. Inicie sesión en el [Portal de Microsoft Azure](https://portal.azure.com) y abra el recurso de información de la aplicación que supervisa la aplicación. (O [crear uno ahora](app-insights-overview.md), si todavía no lo ha hecho todavía).
2. Abra **API Access**y realizar una copia de la **Aplicación perspectivas Id**.

    ![En portal.azure.com, abra el recurso de información de la aplicación y seleccione Configuración. Abra Access API. Copiar el identificador de aplicación](./media/app-insights-annotations/20.png)

2. En una ventana independiente del explorador, abra (o cree) en la plantilla de la versión que administra su implementaciones de Visual Studio Team Services. 

    Agregar una tarea y seleccione la tarea de anotación de lanzamiento de perspectivas de aplicación en el menú.

    Pegue el **Identificador de la aplicación** que ha copiado en el módulo de acceso a la API.

    ![En Visual Studio Team Services, abra la versión, seleccione una definición de versión y elija Editar. Haga clic en Agregar tarea y seleccione anotación de lanzamiento de perspectivas de aplicación. Pegue el identificador de aplicación perspectivas.](./media/app-insights-annotations/30.png)

3. Establecer el campo **APIKey** una variable `$(ApiKey)`.

4. En el módulo de API Access, crear una nueva clave de API y realizar una copia de la misma.

    ![En el módulo de acceso a la API en la ventana de Azure, haga clic en crear clave de API. Proporcione un comentario, comprobar las anotaciones de escritura y haga clic en generar clave. Copie la clave nueva.](./media/app-insights-annotations/40.png)

4. Abra la pestaña Configuración de la plantilla de lanzamiento.

    Crear una definición de variable para `ApiKey`.

    Pegue la clave de API a la definición de variable ApiKey.

    ![En la ventana Servicios de grupo, seleccione la pestaña Configuración y haga clic en Agregar Variable. Establecer el nombre a ApiKey y en el valor, pegue la clave que acaba de generar.](./media/app-insights-annotations/50.png)


5. Por último, **guarde** la definición de lanzamiento.

## <a name="create-annotations-from-powershell"></a>Crear anotaciones de PowerShell

También puede crear anotaciones de todos los procesos que le guste (sin usar el sistema de grupo de VS). 

Obtener el [script de Powershell de GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

Utilícelo similar a esta:

    .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }

Obtener la `applicationId` y un `apiKey` desde el recurso de aplicación perspectivas: abrir la configuración, el acceso de API y copiar el identificador de aplicación. A continuación, haga clic en crear clave de API y copie la clave. 

## <a name="release-annotations"></a>Anotaciones de lanzamiento

Ahora, siempre que use la plantilla de lanzamiento para implementar una nueva versión, una anotación se enviarán a la información de la aplicación. Las anotaciones aparecerán en los gráficos en el Explorador de métricas.

Haga clic en cualquier marcador de anotación para abrir los detalles sobre la versión, incluidas solicitante de bifurcación de control de código fuente, versión de definición, entorno y mucho más.


![Haga clic en cualquier marcador de anotación de lanzamiento.](./media/app-insights-annotations/60.png)
