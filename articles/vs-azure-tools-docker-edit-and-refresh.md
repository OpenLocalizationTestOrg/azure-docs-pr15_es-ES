<properties
   pageTitle="Depuración de aplicaciones en un contenedor de Docker local | Microsoft Azure"
   description="Obtenga información sobre cómo modificar una aplicación que se ejecuta en un contenedor de Docker local, actualice el contenedor a través de edición y actualización y establecer puntos de interrupción de depuración"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/22/2016"
   ms.author="mlearned" />

# <a name="debugging-apps-in-a-local-docker-container"></a>Depuración de aplicaciones en un contenedor de Docker local

## <a name="overview"></a>Información general
Visual Studio Tools para Docker proporciona una manera coherente desarrollar una y validar la aplicación localmente en un contenedor de Linux Docker.
No tiene que reiniciar el contenedor cada vez que realice un código de cambio.
En este artículo se explica cómo usar la característica "Editar y actualizar" para iniciar una aplicación Web de ASP.NET principales en un contenedor de Docker local, realice los cambios necesarios y, a continuación, actualice el explorador para ver los cambios.
También muestra cómo establecer puntos de interrupción de depuración.

> [AZURE.NOTE] Compatibilidad con el contenedor de Windows llegarán en una versión futura

## <a name="prerequisites"></a>Requisitos previos
Las siguientes herramientas deben instalarse.

- [Actualización de 2015 de Visual Studio 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- Instalar la [Actualización de 2015 de Visual Studio 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [SDK básico de Microsoft ASP.NET 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)

Para ejecutar contenedores Docker localmente, necesitará a un cliente docker local.
Puede usar publicadas [Docker herramientas](https://www.docker.com/products/overview#/docker_toolbox) que requiere Hyper-V para deshabilitar, o puede usar [Docker para Windows Beta](https://beta.docker.com) que usa Hyper-V y requiere Windows 10.

Si usa herramientas Docker, deberá configurar [el cliente de Docker](./vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. crear una aplicación web

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. agregar soporte Docker

[AZURE.INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]


## <a name="3-edit-your-code-and-refresh"></a>3. editar el código y la actualización

Para repetir rápidamente los cambios, puede iniciar la aplicación dentro de un contenedor y continuar realizar cambios, visualización de como lo haría con IIS Express.

1. Establecer la configuración de soluciones `Debug` y presione ** &lt;CTRL + F5 >** para generar la imagen docker y ejecutar de forma local.

    Una vez que la imagen de contenedor se ha creado y se ejecuta en un contenedor de Docker, Visual Studio iniciará la aplicación Web en el explorador predeterminado.
    Si está usando el explorador Microsoft Edge o en caso contrario, tiene errores, consulte la sección de [solución de problemas](vs-azure-tools-docker-troubleshooting-docker-errors.md) .

1. Vaya a la página sobre, que es donde vamos a realizar los cambios.

1. Vuelva a Visual Studio y abra `Views\Home\About.cshtml`.

1. Agregue el contenido HTML siguiente al final del archivo y guarde los cambios.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```

1.  Visualización de la ventana de resultados cuando se complete la compilación .NET y ver estas líneas, vuelva a su explorador y actualice la página acerca de.

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.  ¡Los cambios se han aplicado!

## <a name="4-debug-with-breakpoints"></a>4. depurar con puntos de interrupción

A menudo, cambios tendrán más inspección, sacar provecho de las características de depuración de Visual Studio.

1.  Vuelva a Visual Studio y abra`Controllers\HomeController.cs`

1.  Reemplace el contenido del método About() con lo siguiente:

    ```
    string message = "Your application description page from wthin a Container";
    ViewData["Message"] = message;
    ````

1.  Establecer un punto de interrupción a la izquierda de la `string message`... línea.

1.  Visitas ** &lt;F5 >** para iniciar la depuración.

1.  Vaya a la página acerca de visita el punto de interrupción.

1.  Cambie a Visual Studio para ver el punto de interrupción y examinar el valor de mensaje.

    ![][2]

##<a name="summary"></a>Resumen

Con las [Herramientas de 2015 de Visual Studio para Docker](https://aka.ms/DockerToolsForVS), puede obtener la productividad de trabajar de forma local, con el realismo de producción de desarrollo dentro de un contenedor Docker.

## <a name="troubleshooting"></a>Solución de problemas

[Solución de problemas de desarrollo de Visual Studio Docker](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Más información sobre Docker con Visual Studio, Windows y Azure

- [Docker Tools para Visual Studio](http://aka.ms/dockertoolsforvs) - desarrollar el código de .NET principales en un contenedor
- [Herramientas de docker para Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) - Compile e implemente contenedores docker
- [Herramientas de docker para código de Visual Studio](http://aka.ms/dockertoolsforvscode) - servicios de idioma para editar archivos de docker, con más escenarios de e2e pronto estará disponible
- [Información del contenedor de Windows](http://aka.ms/containers)- Windows Server y la información del servidor Nano
- [Servicio de Azure contenedor](https://azure.microsoft.com/services/container-service/) - [contenido de servicio del contenedor de Azure](http://aka.ms/AzureContainerService)
-    Para obtener más ejemplos sobre cómo trabajar con Docker, consulte [trabajar con Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) de la conexión de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [demostración](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Para más tutoriales de la demostración HealthClinic.biz, vea [Tutoriales de herramientas de desarrollador de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## <a name="various-docker-tools"></a>Varias herramientas de Docker

[Algunas herramientas excelentes docker (blog de Steve Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Buenas artículos

[Introducción a Microservices desde NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Presentaciones

- [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [Introducción a ASP.NET Core @ generar 2016 - donde se en demostración](https://channel9.msdn.com/Events/Build/2016/B810)
- [Desarrollo de aplicaciones .NET en contenedores, 9 de canal](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
