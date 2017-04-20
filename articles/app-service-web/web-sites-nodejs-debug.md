<properties
    pageTitle="Cómo depurar una aplicación web de Node.js en la aplicación de servicio de Azure"
    description="Obtenga información sobre cómo depurar una aplicación web de Node.js en la aplicación de servicio de Azure."
    tags="azure-portal"
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
    ms.author="robmcm"/>

# <a name="how-to-debug-a-nodejs-web-app-in-azure-app-service"></a>Cómo depurar una aplicación web de Node.js en la aplicación de servicio de Azure

Azure proporciona diagnósticos integrados para ayudar a depurar aplicaciones Node.js alojadas en aplicaciones Web de [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) . En este artículo, aprenderá cómo habilitar el registro de stdout y stderr, mostrar información de error en el explorador y cómo descargar y ver archivos de registro.

[IISNode]proporciona diagnóstico para aplicaciones Node.js hospedado en Azure. Mientras este artículo describe la configuración más comunes para recopilar información de diagnóstico, no proporciona una referencia completa para trabajar con IISNode. Para obtener más información sobre cómo trabajar con IISNode, vea el [Archivo Léame de IISNode] en GitHub.

<a id="enablelogging"></a>
## <a name="enable-logging"></a>Habilitar el registro

De forma predeterminada, una aplicación de servicio de aplicación web sólo captura información de diagnóstico sobre implementaciones, por ejemplo, cuando se implementa una aplicación web mediante Git. Esta información es útil si tiene problemas durante la implementación, por ejemplo, un error al instalar un módulo que se hace referencia en **package.json**, o si está utilizando un script de implementación personalizado.

Para habilitar el registro de secuencias stdout y stderr, debe crear un archivo **IISNode.yml** en la raíz de la aplicación Node.js y agregue lo siguiente:

    loggingEnabled: true

Esto habilita el registro de stderr y stdout desde la aplicación Node.js.

El archivo **IISNode.yml** puede usarse para controlar si descriptivos errores o programador se devuelven al explorador cuando se produce un error. Para habilitar los errores de desarrollador, agregue la línea siguiente al archivo **IISNode.yml** :

    devErrorsEnabled: true

Una vez que esta opción está activada, IISNode devolverá el último 64 KB de la información enviada a stderr en lugar de un error descriptivo, como "se produjo un error de servidor interno".

> [AZURE.NOTE] Mientras devErrorsEnabled es útil cuando diagnosticar problemas durante el desarrollo, habilitar en un entorno de producción puede producir errores de desarrollo enviados a los usuarios finales.

Si el archivo de **IISNode.yml** ya no existe en la aplicación, debe reiniciar la aplicación web después de publicar la aplicación actualizada. Si simplemente desea cambiar la configuración en un archivo de **IISNode.yml** existente que anteriormente se ha publicado, no es necesario reiniciar.

> [AZURE.NOTE] Si la aplicación web se creó usando las herramientas de línea de comandos de Azure o los Cmdlets de PowerShell de Azure, se crea automáticamente un archivo de **IISNode.yml** predeterminado.

Para reiniciar la aplicación web, seleccione la aplicación web en el [Portal de Azure](https://portal.azure.com)y, a continuación, haga clic en botón **reiniciar** :

![Reinicie el botón][restart-button]

Si están instaladas las herramientas de línea de comandos de Azure en su entorno de desarrollo, puede usar el comando siguiente para reiniciar la aplicación web:

    azure site restart [sitename]

> [AZURE.NOTE] Aunque loggingEnabled y devErrorsEnabled son las más utilizadas IISNode.yml las opciones de configuración para capturar información de diagnóstico, IISNode.yml puede usarse para configurar una gran variedad de opciones en su entorno de hospedaje. Para obtener una lista completa de las opciones de configuración, consulte el archivo [iisnode_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml) .

<a id="viewlogs"></a>
## <a name="accessing-logs"></a>Acceso a registros

Pueden tener acceso a los registros de diagnóstico de tres maneras; Usar el archivo de protocolo de transferencia (FTP), descargar un archivo Zip, o como un live actualizado secuencia del registro (también conocido como una cola). Descargar el archivo Zip de los archivos de registro o ver la secuencia en directo requieren las herramientas de línea de comandos de Azure. Estos se pueden instalar mediante el comando siguiente:

    npm install azure-cli -g

Una vez instalado, se pueden acceder a las herramientas mediante el comando 'azure'. Las herramientas de línea de comandos en primer lugar deben estar configuradas para usar su suscripción de Azure. Para obtener información sobre cómo llevar a cabo esta tarea, consulte la **cómo descargar y configuración de publicación de importación** sección del artículo [cómo usar las herramientas de línea de comandos de Azure](../xplat-cli-connect.md) .

###<a name="ftp"></a>FTP

Para obtener acceso a la información de diagnóstico a través de FTP, visite el [Portal de Azure](https://portal.azure.com), seleccione la aplicación web y, a continuación, seleccione el **panel**. En la sección **vínculos rápidos** , los vínculos de **Registros de diagnóstico FTP** y **FTPS diagnóstico** proporcionan acceso a los registros mediante el protocolo FTP.

> [AZURE.NOTE] Si no ha previamente configurado el nombre de usuario y contraseña para FTP o implementación, puede hacerlo desde la página de administración de **tutorial** seleccionando **configurar las credenciales de la implementación**.

La dirección URL de FTP devuelto en el panel es para el directorio de **archivos de registro** que contiene los subdirectorios siguientes:

* [Método de implementación](web-sites-deploy.md) - si utiliza un método de implementación como Git, un directorio del mismo nombre, se creará y contendrá información relacionada con implementaciones.

* nodejs - Stdout y stderr información genera de todas las instancias de la aplicación (cuando loggingEnabled es true).

###<a name="zip-archive"></a>Archivo zip

Para descargar un archivo Zip de los registros de diagnóstico, utilice el comando siguiente desde las herramientas de línea de comandos de Azure:

    azure site log download [sitename]

Esto descargará una **diagnostics.zip** en el directorio actual. Este archivo contiene la estructura de directorio siguiente:

* implementaciones - un registro de información sobre implementaciones de la aplicación

* Archivos de registro

    * [Método de implementación](web-sites-deploy.md) - si utiliza un método de implementación como Git, un directorio del mismo nombre, se creará y contendrá información relacionada con implementaciones.

    * nodejs - Stdout y stderr información genera de todas las instancias de la aplicación (cuando loggingEnabled es true).

###<a name="live-stream-tail"></a>Secuencia en directo (cola)

Para ver una secuencia en directo de la información de registro de diagnóstico, utilice el comando siguiente desde las herramientas de línea de comandos de Azure:

    azure site log tail [sitename]

Esto devolverá una secuencia de eventos de registro que se actualizan a medida que se producen en el servidor. Esta secuencia devolverá información sobre la implementación, así como información stdout y stderr (cuando loggingEnabled es true).

<a id="nextsteps"></a>
## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido cómo habilitar y obtener acceso a información de diagnóstico para Azure. Aunque esta información es útil en los problemas de descripción que se producen con la aplicación, puede apuntar a un problema con un módulo está usando o que la versión de Node.js usado por aplicaciones de aplicación de servicio Web es diferente a la que se utilizan en su entorno de implementación.

Para obtener información en trabajar con módulos en Azure, vea [Usar Node.js módulos con aplicaciones de Azure](../nodejs-use-node-modules-azure-apps.md).

Para obtener información sobre cómo especificar una versión de Node.js para la aplicación, vea [especificar una versión de Node.js en una aplicación de Azure].

Para obtener más información, vea también el [Centro para desarrolladores de Node.js](/develop/nodejs/).

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

[IISNode]: https://github.com/tjanczuk/iisnode
[Léame de IISNode]: https://github.com/tjanczuk/iisnode#readme
[How to Use The Azure Command-Line Interface]: ../xplat-cli-install.md
[Using Node.js Modules with Azure Applications]: ../nodejs-use-node-modules-azure-apps.md
[Especificar una versión de Node.js en una aplicación de Azure]: ../nodejs-specify-node-version-azure-apps.md

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png
 
