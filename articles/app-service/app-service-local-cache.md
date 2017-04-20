<properties
   pageTitle="Información general de caché Local de servicios de aplicación Azure | Microsoft Azure"
   description="En este artículo se describe cómo habilitar, cambiar el tamaño y consultar el estado de la característica de caché Local de servicios de aplicación de Azure"
   services="app-service"
   documentationCenter="app-service"
   authors="SyntaxC4"
   manager="yochayk"
   editor=""
   tags="optional"
   keywords=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/04/2016"
   ms.author="cfowler"/>

# <a name="azure-app-service-local-cache-overview"></a>Introducción a Azure caché Local de servicios de aplicación

Contenido de la aplicación de Azure web se almacena en el almacenamiento de Azure y está expuesto hacia arriba de manera resistente como un recurso compartido de contenido. Este diseño está diseñado para funcionar con una variedad de aplicaciones y tiene los siguientes atributos:  

* El contenido se comparte entre varias instancias de máquina virtual (VM) de la aplicación web.
* El contenido es resistente y puede modificarse mediante la ejecución de aplicaciones web.
* Archivos de registro y archivos de datos de diagnóstico están disponibles en la misma carpeta de contenido compartida.
* Publicar contenido nuevo directamente, actualiza la carpeta de contenido. Puede ver inmediatamente el mismo contenido a través del sitio Web SCM y la aplicación web ejecución (normalmente algunas tecnologías como ASP.NET iniciar un reinicio de la aplicación web en algunos cambios de archivo para obtener el contenido más reciente).

Aunque muchos aplicaciones web utilizan una o todas estas características, algunas aplicaciones web solo tenga un almacén de contenido de alto rendimiento, de sólo lectura que puede ejecutar desde con alta disponibilidad. Estas aplicaciones pueden beneficiarse de una instancia de máquina virtual de una caché local específica.

La característica de caché Local de servicios de aplicación de Azure ofrece una vista de rol web de su contenido. Este contenido es una caché de escritura-pero-descartar el contenido de almacenamiento que se crea de forma asincrónica al inicio del sitio. Cuando la caché esté lista, el sitio cambia para ejecutarse con el contenido de la caché. Aplicaciones Web que se ejecutan en la memoria caché Local tienen las siguientes ventajas:

* Son resistencia total a latencia que se produce cuando tengan acceso a contenido en el almacenamiento de Azure.
* Son resistencia total a las actualizaciones planeadas o tiempos de inactividad no planificados y otras interrupciones con el almacenamiento de Azure que se producen en los servidores que sirven la opción compartir contenida.
* Tienen menos reinicio de aplicación debido a cambios de cuota de almacenamiento.

## <a name="how-local-cache-changes-the-behavior-of-app-service"></a>Cómo la memoria caché Local cambia el comportamiento de la aplicación de servicio

* La memoria caché local es una copia de las carpetas /site y /siteextensions de la aplicación web. Se crea en la instancia VM local en el inicio de aplicación web. El tamaño de la memoria caché local por la aplicación web se limita a 300 MB de forma predeterminada, pero puede aumentar hasta 1 GB.
* La memoria caché local es de lectura y escritura. Sin embargo, se descartarán las modificaciones cuando mueve máquinas virtuales o se obtiene reinicia la aplicación web. No debe utilizar la memoria caché Local para las aplicaciones que almacenan datos importantes en el almacén de contenido.
* Aplicaciones Web pueden continuar escribiendo archivos de registro y diagnósticos datos como lo hacen actualmente. Archivos de registro y datos, sin embargo, se almacenan localmente en la máquina virtual. A continuación, se copiarán periódicamente al almacén de contenido compartido. La copia en el almacén de contenido compartido es un mejor esfuerzo--escritura realiza una copia de se puede perder debido a un bloqueo rápido de una instancia de máquina virtual.
* Hay un cambio en la estructura de carpetas de las carpetas de archivos de registro y datos de aplicaciones web que usan la memoria caché Local. Ahora hay subcarpetas en las carpetas de archivos de registro y datos de almacenamiento que sigan el patrón de nomenclatura de "identificador único" + marca de tiempo. Cada una de las subcarpetas corresponde a una instancia VM donde la aplicación web se está ejecutando o se ha ejecutado.  
* Publicación de los cambios a la aplicación web a través de los mecanismos de publicación publicará en el almacén de contenido compartido. Esto es por diseño porque queremos el contenido publicado ser resistentes. Para actualizar la memoria caché local de la aplicación web, debe reiniciar. ¿Esto parecer un paso excesivo? Para hacer que el ciclo de vida sin problemas, consulte la información más adelante en este artículo.
* D:\Home se señale la memoria caché local. D:\Local seguirá señalando el almacenamiento específico VM temporal.
* La vista de contenido predeterminado del sitio SCM seguirán de la tienda de contenido compartida.

## <a name="enable-local-cache-in-app-service"></a>Habilitar la memoria caché Local en la aplicación de servicio

Configurar la memoria caché Local mediante una combinación de configuración de la aplicación reservada. Puede configurar estas opciones de aplicación mediante los métodos siguientes:

* [Portal de Azure](#Configure-Local-Cache-Portal)
* [Administrador de recursos de Azure](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Configurar la memoria caché Local mediante el portal de Azure
<a name="Configure-Local-Cache-Portal"></a>

Habilitar la memoria caché Local en una base de la aplicación web mediante esta configuración de la aplicación:`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Configuración de la aplicación portal Azure: memoria caché Local](media/app-service-local-cache/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Configurar la memoria caché Local mediante el Administrador de recursos de Azure
<a name="Configure-Local-Cache-ARM"></a>

```
...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],
    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Cambiar el tamaño de la memoria caché Local

De forma predeterminada, el tamaño de la memoria caché local es **300 MB**. Esto incluye la /site y carpetas de /siteextensions que se copian desde el almacén de contenido, así como las carpetas de registros y datos creadas localmente. Para aumentar este límite, use la configuración de la aplicación `WEBSITE_LOCAL_CACHE_SIZEINMB`. Puede aumentar el tamaño máximo de **1 GB** (1000 MB) por la aplicación web.

## <a name="best-practices-for-using-app-service-local-cache"></a>Prácticas recomendadas para el uso de la memoria caché Local de aplicación de servicio

Se recomienda usar caché Local junto con la característica de [Entornos de ensayo](../app-service-web/web-sites-staged-publishing.md) .

* Agregue la configuración de aplicación _rápidas_ `WEBSITE_LOCAL_CACHE_OPTION` con el valor `Always` a la franja de **producción** . Si está usando `WEBSITE_LOCAL_CACHE_SIZEINMB`, también se agrega como valor rápido para la franja de producción.
* Crear un espacio de **ensayo** y publicar en la franja de ensayo. Normalmente no se establece la franja de ensayo utilizar memoria caché Local para habilitar un ciclo de vida de prueba implementar compilación fluido de ensayo si obtiene los beneficios de la memoria caché Local para la franja de producción.
*   Probar el sitio con su franja de ensayo.  
*   Cuando esté listo, emitir una [operación de intercambio](../app-service-web/web-sites-staged-publishing.md#to-swap-deployment-slots) entre las ranuras de ensayo y producción.  
*   Configuración rápidas incluir nombre y rápidas a un espacio. Así que cuando la franja de ensayo obtiene intercambiado en producción, heredarán la configuración de aplicación de la memoria caché Local. La franja de producción recién intercambiada ejecutará en la memoria caché local después de unos minutos y se pueden caliente como parte de la preparación de franja después de intercambiar. Así que cuando se complete el intercambio de franja, se ejecuta la franja de producción contra la memoria caché local.

## <a name="frequently-asked-questions-faq"></a>Preguntas más frecuentes (P+F)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>¿Cómo puedo saber si la memoria caché Local se aplica a mi aplicación web?

Si la aplicación web necesita un almacén de contenido de alto rendimiento, confiable, se utiliza el almacén de contenido para escribir datos críticos en tiempo de ejecución y tiene menos de 1 GB de tamaño total, a continuación, la respuesta es "Sí". Para obtener el tamaño total de las carpetas /site y /siteextensions, puede usar la extensión de sitio "Uso del disco de aplicaciones de Azure Web".  

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>¿Cómo puedo saber si mi sitio ha cambiado al uso de la memoria caché Local?

Si usa la característica de caché Local con entornos de ensayo, la operación de intercambio no se completará hasta que se caliente memoria caché Local. Para comprobar si su sitio se ejecuta en la memoria caché Local, puede comprobar la variable de entorno del proceso de trabajo `WEBSITE_LOCALCACHE_READY`. Utilice las instrucciones que aparecen en la página de la [variable de entorno del proceso de trabajo](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) para tener acceso a la variable de entorno del proceso de trabajo en varias instancias.  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>Acaba de publicar nuevos cambios, pero mi aplicación web parece que no tiene. ¿Por qué?

Si la aplicación web usa la memoria caché Local, debe reiniciar su sitio para obtener los cambios más recientes. ¿Desea publicar los cambios en un sitio de producción? Vea las opciones de franja en la sección sobre prácticas recomendadas anterior.

### <a name="where-are-my-logs"></a>¿Dónde están mis registros?

Con la memoria caché Local, los registros y las carpetas de datos de un aspecto algo distintas. Sin embargo, la estructura de sus subcarpetas permanece igual, salvo que las subcarpetas están anidadas en una subcarpeta con el formato "VM identificador único" + la marca de tiempo.

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Tengo caché Local habilitada, pero aún obtiene reinicia mi aplicación web. ¿Por qué? Considerar la que memoria caché Local contribuido con reiniciar aplicación frecuentes.

Memoria caché local evitar que se reinicia de aplicación web relacionado con el almacenamiento. Sin embargo, la aplicación web podría sufrir aún se reinicia durante las actualizaciones de infraestructura planeada de la máquina virtual. Reiniciar el app general que experimentar con la memoria caché Local habilitado debería menos.
