<properties
    pageTitle="Trabajar con módulos Node.js"
    description="Obtenga información sobre cómo trabajar con módulos Node.js cuando se usa el servicio de aplicación de Azure o servicios en la nube."
    services=""
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>


# <a name="using-nodejs-modules-with-azure-applications"></a>Utilizar módulos de Node.js con aplicaciones de Azure

Este documento proporciona instrucciones sobre cómo usar Node.js módulos con aplicaciones de Azure. Proporciona orientación en garantizar que su aplicación utiliza una versión específica del módulo, así como con los módulos nativos con Azure.

Si ya está familiarizado con el uso de módulos Node.js, **package.json** y **shrinkwrap.json npm** archivos, el siguiente es un breve resumen de lo que se describe en este artículo:

* Servicio de aplicaciones de Azure comprende archivos **package.json** y **shrinkwrap.json npm** y puede instalar módulos basados en las entradas de estos archivos.
* Servicios de nube de Azure esperar todos los módulos esté instalado en el entorno de desarrollo y la **nodo\_módulos** directorio se incluye como parte del paquete de implementación. Es posible habilitar la compatibilidad con la instalación de módulos utilizando **package.json** o **npm shrinkwrap.json** archivos en servicios en la nube, pero esto requiere personalización de las secuencias de comandos predeterminados utilizados por proyectos de servicio de nube. Para obtener un ejemplo de cómo hacerlo, vea [tareas de inicio de Azure ejecutar instalación npm para evitar la implementación de módulos de nodo](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [AZURE.NOTE] Máquinas virtuales de Windows Azure no se describen en este artículo, como la experiencia de implementación de una máquina virtual dependerá del sistema operativo alojado en la máquina Virtual.

##<a name="nodejs-modules"></a>Módulos Node.js

Módulos son puede cargables paquetes de JavaScript que proporcionan funcionalidad específica de la aplicación. Un módulo se instala normalmente mediante la herramienta de línea de comandos **npm** , pero algunos (por ejemplo, el módulo de http) se proporcionan como parte del paquete de Node.js core.

Cuando se instalan los módulos, se almacenan en el **nodo\_módulos** directorio en la raíz de la estructura de directorios de la aplicación. Cada módulo dentro de la **nodo\_módulos** directory mantiene su propio **nodo\_módulos** directorio que contiene todos los módulos que depende y, a continuación, se repite nuevamente para cada módulo hacia abajo en la cadena de dependencia. Esto permite cada módulo instalado para tener sus propios requisitos de versión de los módulos depende, sin embargo puede dar lugar a una estructura de directorios grande.

Al implementar la **nodo\_módulos** directorio como parte de la aplicación, aumentará el tamaño de la implementación en comparación con mediante un archivo **package.json** o **shrinkwrap.json npm** ; Sin embargo, garantiza que la versión de los módulos de producción son iguales a los usados en el desarrollo.

###<a name="native-modules"></a>Módulos nativos

Mientras la mayoría de los módulos es archivos de texto sin formato simplemente JavaScript, algunos módulos son específicos de la plataforma de imágenes binarias. Estos módulos se compilan durante la instalación, normalmente mediante Python y nodo gyp. Puesto que los servicios de nube de Azure se basan en el **nodo\_módulos** carpeta se implementa como parte del módulo de aplicaciones, cualquier nativo que forma parte de los módulos instalados debería funcionar en un servicio de nube como estaba instalado y compilado en un sistema de desarrollo de Windows.

Azure de aplicación de servicio no es compatible con todos los módulos nativos y puede producir un error en la los requisitos previos muy específicos compilación de. Mientras algunos módulos populares como MongoDB tienen dependencias nativas opcionales y funcione bien sin ellos, dos alternativas resultó correctamente con casi todos los módulos nativos disponibles actualmente:

* Ejecutar **npm instalar** en un equipo Windows que tiene requisitos previos de todos los nativa del módulo instalados. A continuación, implementar creado **nodo\_módulos** carpeta como parte de la aplicación de servicio de aplicaciones de Azure.
* Azure de aplicación de servicio se puede configurar para ejecutar fiesta personalizado o secuencias de comandos de shell durante la implementación, que le da la oportunidad de ejecutar comandos personalizados y configurar con precisión el modo **instalar npm** se está ejecutando. Para ver un vídeo que muestra cómo hacer esto, [Scripts de implementación de sitio Web personalizada con Kudu].

###<a name="using-a-packagejson-file"></a>Con un archivo de package.json

El archivo **package.json** es una manera de especificar las dependencias de nivel superior requiere la aplicación para que pueda instalar la plataforma de hospedaje las dependencias, en lugar de tener que incluir la **nodo\_paquetes** carpeta como parte de la implementación. Después de haber implementado la aplicación, se usa el comando **npm instalar** para analizar el archivo **package.json** e instale todas las dependencias enumeradas.

Durante el desarrollo, puede usar el **--Guardar**, **: desarrollo de guardar**, o **--Guardar opcional** parámetros al instalar módulos para agregar una entrada para el módulo al archivo **package.json** automáticamente. Para obtener más información, vea [instalar npm](https://docs.npmjs.com/cli/install).

Un problema potencial con el archivo **package.json** es que solo especifica la versión para las dependencias de nivel superior. Cada módulo instalado puede o no puede especificar la versión de los módulos depende y lo que es posible que puede terminar con una cadena de dependencia diferente que el utilizado en desarrollo.

> [AZURE.NOTE]
> Cuando se implementa en el servicio de aplicación de Azure, si el archivo <b>package.json</b> hace referencia a un módulo nativo al publicar la aplicación con Git verán un error similar al siguiente:

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


###<a name="using-a-npm-shrinkwrapjson-file"></a>Con un archivo npm shrinkwrap.json

El archivo **shrinkwrap.json npm** es un intento para resolver las limitaciones de control de versiones de módulo del archivo **package.json** . Mientras el archivo **package.json** sólo incluye versiones de los módulos de nivel superior, el archivo **npm shrinkwrap.json** contiene los requisitos de versión de la cadena de dependencia de módulo completo.

Cuando la aplicación está preparada para producción, puede bloquear-abajo requisitos de versión y crear un archivo de **npm shrinkwrap.json** mediante el comando **npm precinto** . Usará las versiones instaladas actualmente en el **nodo\_módulos** carpeta y registrar en el archivo **npm shrinkwrap.json** . Después de la aplicación se ha implementado en el entorno de hospedaje, se usa el comando **npm instalar** para analizar el archivo **npm shrinkwrap.json** e instale todas las dependencias que aparece. Para obtener más información, vea [npm precinto](https://docs.npmjs.com/cli/shrinkwrap).

> [AZURE.NOTE]
>Cuando se implementa en el servicio de aplicación de Azure, si el archivo <b>npm shrinkwrap.json</b> hace referencia a un módulo nativo al publicar la aplicación con Git verán un error similar al siguiente:

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##<a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo usar Node.js módulos con Azure, obtenga información sobre cómo [especificar la versión de Node.js], [Compile e implemente una aplicación web de Node.js]y [cómo usar la interfaz de línea de comandos de Azure para Mac y Linux].

Para obtener más información, consulte el [Centro para desarrolladores de Node.js](/develop/nodejs/).

[especificar la versión de Node.js]: nodejs-specify-node-version-azure-apps.md
[Cómo usar la interfaz de línea de comandos de Azure para Mac y Linux]: xplat-cli-install.md
[Compile e implemente una aplicación web de Node.js]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[Secuencias de comandos de implementación de sitio Web personalizada con Kudu]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/
