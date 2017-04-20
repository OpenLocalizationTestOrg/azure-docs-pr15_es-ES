<properties 
    pageTitle="Cómo usar io.js con aplicaciones de Azure aplicación de servicio Web" 
    description="Aprenda a usar una aplicación web en la aplicación de servicio de Azure con io.js." 
    services="app-service\web" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016"
    ms.author="robmcm" />

# <a name="how-to-use-iojs-with-azure-app-service-web-apps"></a>Cómo usar io.js con aplicaciones de Azure aplicación de servicio Web

La populares de bifurcación de nodo [io.js] características de diversos diferencias a de Joyent Node.js, incluso un modelo de gobierno más abierto, un ciclo de publicación más rápido y una adopción más rápida de las características nuevas y experimentación JavaScript.

Aunque [Azure aplicación de servicio](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps tiene muchas versiones de Node.js preinstaladas, también permite un binario de Node.js proporcionado por el usuario. Este artículo explica cómo habilitar el uso de io.js en aplicaciones Web de aplicación de servicio de dos métodos: el uso de un script de implementación ampliado, que configura automáticamente Azure para usar la versión más reciente de io.js disponible, así como la carga manual de un io.js binario. 

<a id="deploymentscript"></a>
## <a name="using-a-deployment-script"></a>Uso de un Script de implementación

Tras la implementación de una aplicación Node.js, aplicaciones de aplicación de servicio Web se ejecuta una serie de comandos pequeñas para asegurarse de que el entorno está configurado correctamente. Uso de un script de implementación, este proceso puede personalizarse para incluir la descarga y la configuración de io.js.

El [Script de implementación de io.js](https://github.com/felixrieseberg/iojs-azure) está disponible en GitHub. Para habilitar io.js en la aplicación web, basta con copiar **implementación.**, **deploy.cmd** y **IISNode.yml** en la raíz de su carpeta de la aplicación e implemente aplicaciones Web.  

El primer archivo, **implementación.**, indica que aplicaciones Web para ejecutar **deploy.cmd** tras la implementación. Esta secuencia de comandos ejecuta todos los pasos habituales para una aplicación Node.js, pero también descarga la última versión de io.js. Por último, **IISNode.yml** configura aplicaciones Web para usar solo la io.js binario descargados en lugar de un binario Node.js preinstalado.

> [AZURE.NOTE] Para actualizar el binario io.js usados, simplemente vuelva a implementar la aplicación - la secuencia de comandos descargará una versión nueva de io.js cada vez solo que se implementa la aplicación.

<a id="manualinstallation"></a>
## <a name="using-manual-installation"></a>Instalación Manual de uso

La instalación manual de una versión io.js personalizado incluye solo dos pasos. Primero, descargue el **win x64** binario directamente desde la [distribución de io.js]. Requiere son dos archivos: **iojs.exe** y **iojs.lib**. Guardar los archivos en una carpeta dentro de la aplicación web, por ejemplo en la **Papelera/iojs**.

Para configurar aplicaciones Web para usar **iojs.exe** en lugar de una versión de nodo preinstalada, cree un archivo de **IISNode.yml** en la raíz de la aplicación y agregue la línea siguiente.

    nodeProcessCommandLine: "D:\home\site\wwwroot\bin\iojs\iojs.exe"

<a id="nextsteps"></a>
## <a name="next-steps"></a>Pasos siguientes

En este artículo que ha aprendido a usar io.js con la aplicación de servicio Web Apps, con los dos proporcionan secuencias de comandos de implementación instalación manual, así como. 

> [AZURE.NOTE] IO.js está en desarrollo pesado y actualizado con más frecuencia que Node.js. Un número de módulos Node.js podría no funcionar con io.js - por favor, consulte [io.js en GitHub] para solucionar este problema.

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

[IO.js]: https://iojs.org
[distribución de IO.js]: https://iojs.org/dist/
[IO.js en GitHub]: https://github.com/iojs/io.js
[io.js Deployment Script]: https://github.com/felixrieseberg/iojs-azure
 