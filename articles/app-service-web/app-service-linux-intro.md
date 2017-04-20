<properties 
    pageTitle="Introducción a la aplicación de servicio en Linux | Microsoft Azure" 
    description="Obtenga información sobre la aplicación de servicio en Linux." 
    keywords="servicio de aplicaciones de Azure, linux, sistemas operativos"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="introduction-to-app-service-on-linux"></a>Introducción a la aplicación de servicio en Linux
Aplicación de servicio en Linux está actualmente en la versión preliminar pública y es compatible con aplicaciones web de ejecución de forma nativa en Linux. 

## <a name="overview"></a>Información general ##
Clientes puedan usar el servicio de aplicación en Linux a aplicaciones web de host forma nativa en Linux para pilas de aplicación compatibles. La siguiente sección características enumera las pilas de aplicación actualmente admitidos.

## <a name="features"></a>Características ##
Aplicación de servicio en Linux es compatible actualmente con las siguientes pilas de aplicación

- Node.js
- PHP

Los clientes pueden implementar sus aplicaciones con

- FTP.
- Git local.
- GitHub o BitBucket.

Ajuste de escala de la aplicación


- Los clientes pueden escalar hacia arriba y abajo su aplicación web cambiando el nivel de su Plan de servicio de aplicación. 
- Los clientes pueden escalar sus aplicaciones y ejecute su aplicación en varias instancias dentro de los límites de su SKU.

Para Kudu parte de la funcionalidad básica funcionará

- Entorno.
- Implementaciones.
- Consola básica.

## <a name="limitations"></a>Limitaciones ##

El portal de administración de Azure sólo mostrar características compatibles actualmente para la aplicación de servicio en Linux y ocultar el resto. Como nuestro equipo de habilitar las características más se le mantener pensar esto en el portal de administración. Algunas características, como la integración de VNET y AAD / autenticación de terceros o extensiones de sitio Kudu no funciona actualmente. Pero como recibimos estos trabajar actualizaremos nuestra documentación y blog sobre los cambios.

Esta vista previa pública actualmente solo está disponible en las siguientes regiones

-   Oeste de Estados Unidos.
-   Europa occidental.
-   Sudeste asiático.

Aplicación Web en Linux solo es compatible en planes de servicio de aplicación dedicado y no tiene un nivel de libre o compartido. Además, planes de servicio de aplicación para normal y Linux web apps son mutuamente, por lo que no puede crear una aplicación web de Linux en un plan de servicios de aplicación no Linux.

Aplicación Web en Linux debe crearse en un grupo de recursos que no contenga no Linux web apps en la misma región.

Debido a la falta de reciclaje superpuesto de las aplicaciones web, los clientes deben esperar que obtuvo reinicia un tiempo de inactividad pequeña en caso de una aplicación web. 

## <a name="next-steps"></a>Pasos siguientes ##

Siga los siguientes vínculos para empezar con la aplicación de servicio en Linux. Publique preguntas y problemas en [nuestro foro](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Crear aplicaciones Web en la aplicación de servicio en Linux](./app-service-linux-how-to-create-a-web-app.md)
* [Usar configuración de PM2 para Node.js en las aplicaciones Web en Linux](./app-service-linux-using-nodejs-pm2.md)

