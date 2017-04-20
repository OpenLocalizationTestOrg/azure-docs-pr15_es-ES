<properties
    pageTitle="WebJobs en Azure de aplicación de servicio"
    description="Obtenga información sobre cómo crear WebJobs para ejecutar las pruebas de fondo, interactuar con servicios como almacenamiento y Bus de servicio y crear tareas programadas."
    services="app-service"
    documentationCenter=""
    authors="christopheranderson"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="chrande"/>

# <a name="using-webjobs-in-azure-app-service"></a>Uso de WebJobs en Azure de aplicación de servicio

Este artículo proporciona vínculos a recursos de documentación sobre cómo usar el SDK de WebJobs de Azure y Azure WebJobs. WebJobs Azure proporcionan una forma sencilla de ejecutar secuencias de comandos o programas como procesos en segundo plano en la [Aplicación de servicio Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Puede cargar y ejecutar un archivo ejecutable como como cmd, bat exe (. NET), ps1, sh, php, copiar, js y jar. Estos programas ejecutan como WebJobs en una programación (cron) o de forma continua.

El SDK WebJobs facilita la usar el almacenamiento de Azure. El SDK WebJobs tiene un enlace y un sistema de desencadenador que funciona con Microsoft Azure almacenamiento de Blobs, colas y tablas, así como colas de Bus de servicio.

Crear, implementar y administrar WebJobs están perfecto con las herramientas integradas en Visual Studio. Puede crear WebJobs a partir de plantillas, publicar y administrar (ejecutar, detener, monitor/depurar) ellos.

El panel WebJobs en el portal de Azure proporciona poderosas capacidades de administración que proporcionan control total sobre la ejecución de WebJobs, incluida la capacidad de invocación de funciones individuales en WebJobs. El panel también muestra el tiempo de ejecución de función y los resultados del registro.

[AZURE.INCLUDE [app-service-blueprint-webjobs](../../includes/app-service-blueprint-webjobs.md)]
