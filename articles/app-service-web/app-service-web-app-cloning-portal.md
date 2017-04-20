<properties
    pageTitle="Clonar de aplicación Web con el Portal de Azure"
    description="Obtenga información sobre cómo clonar sus aplicaciones Web a nuevas aplicaciones de Web con el Portal de Azure."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/08/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-azure-portal"></a>Aplicación de servicio de aplicaciones de Azure clonar usando un Portal de Azure#

La función de clonación en [Azure aplicación de servicio Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) permite clonar fácilmente aplicaciones web existentes a una aplicación recién creada en una región diferente o en la misma región. Esto le permitirá a los clientes implementar un número de aplicaciones a través de diferentes regiones rápida y fácilmente.

Aplicación clonar está actualmente solo es compatible con planes de servicio de aplicación de nivel premium. La nueva característica usa las mismas limitaciones como característica de copia de seguridad de aplicaciones Web, vea [realizar copias de seguridad de una aplicación web de servicio de aplicaciones de Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 


## <a name="cloning-an-existing-app"></a>Clonar una aplicación existente ##

La aplicación web debe estar ejecutándose en el modo de **Premium** en orden para crear un duplicado de la aplicación web.

1. En el [Portal de Azure](https://portal.azure.com/), abra el módulo de la aplicación web.
2. Haga clic en **Herramientas**. A continuación, en el módulo de **Herramientas** , haga clic en **Aplicación de clonar**.

    ![][1]

    > [AZURE.NOTE]
    > Si la aplicación web no está en el modo de **Premium** , recibirá un mensaje que indica los modos compatibles para clonar de aplicación. En este momento, tiene la opción de seleccionar **Actualizar**.
    
3. En el módulo de **Aplicación de clonar** proporcione un nombre de la nueva aplicación web, grupo de recursos y Plan de servicio de aplicación. También el usuario podrá elegir si desea clonar un número de la configuración de aplicación web de origen o no.

    ![][2]

4. Tras hacer clic en **crear** la plataforma iniciará el trabajo acerca de cómo crear un duplicado de la aplicación web de origen.

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonar un entorno de aplicación de servicio de una aplicación existente##

En el módulo de **Aplicación de clonar** el cliente tendrá la opción de elegir un grupo de aplicación en un entorno de servicio de aplicación existente.

## <a name="current-restrictions"></a>Restricciones actuales ##

Esta característica está actualmente en la vista previa, estamos trabajando para agregar nuevas capacidades a lo largo del tiempo, la siguiente lista son las limitaciones conocidas de la compatibilidad actual de la aplicación clonar en el Portal de Azure:

- No se clona configuración del Administrador de tráfico de Azure
- No se clona configuración de escala automático
- No se clona configuración de programación de copia de seguridad
- No se clona configuración VNET
- Perspectivas de aplicación no se establecen automáticamente hacia arriba en la aplicación web de destino
- No se clona fácil configuración de autenticación
- No se clonar kudu extensión
- No se clonarán reglas de sugerencia
- No se clonar el contenido de la base de datos


### <a name="references"></a>Referencias ###
- [Clonar de aplicación Web con PowerShell](app-service-web-app-cloning.md)
- [Realizar copias de seguridad de una aplicación web en la aplicación de servicio de Azure](web-sites-backup.md)
- [Cómo crear un entorno de servicio de aplicaciones](app-service-web-how-to-create-an-app-service-environment.md)
- [Crear una aplicación web en un entorno de servicio de aplicación](app-service-web-how-to-create-a-web-app-in-an-ase.md)
- [Introducción al entorno de servicio de aplicaciones](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png