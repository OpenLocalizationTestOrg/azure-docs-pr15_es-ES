<properties
    pageTitle="Licencias de RemoteApp Azure | Microsoft Azure"
    description="Obtenga información sobre cómo funciona la licencia en Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-does-licensing-work-in-azure-remoteapp"></a>¿Cómo licencias funciona en RemoteApp de Azure?

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Por lo que ha configurado el servicio de RemoteApp de Azure, creado sus plantillas y esté listo para publicar aplicaciones a los usuarios. Pero todavía hay una cosa más que averiguar: licencias. ¿Simplemente cómo funciona licencias para RemoteApp y para las aplicaciones que comparte a través de RemoteApp?

RemoteApp no requiere las licencias de Windows o CAL de escritorio remoto. La suscripción se encarga del lado RemoteApp propiamente dicho. (Consulte los detalles de los [planes de precios](https://azure.microsoft.com/pricing/details/remoteapp)).

Si usa una de las imágenes que se incluye en la suscripción, puede compartir cualquiera de las aplicaciones instaladas en la imagen sin necesidad de una licencia independiente. Por ejemplo, si utiliza la imagen de la plantilla de Windows Server 2012 R2 para crear la colección, puede compartir sistema Centro Endpoint Protection con los usuarios. La única excepción a esta regla son Office 365 ProPlus, que requiere una suscripción independiente, y Office 2013, que no se pueden compartir en una colección de producción.

Si desea usar la imagen de la plantilla de Office 365 incluye RemoteApp, debe tener un plan de Office 365 ProPlus *existente* . El mismo es true para cualquier aplicación de Office 365 que publicar mediante una plantilla personalizada. Debe activar las aplicaciones con su propia suscripción. Esto es verdadero para suscripciones de prueba y de pagadas. Si desea usar la imagen de la plantilla de Office 365 durante la evaluación, *y todavía no tiene una suscripción*, vaya a la página de Office 365 para [registrarse](https://go.microsoft.com/fwlink/p/?LinkID=403802) para una suscripción de prueba. Vea [cómo RemoteApp y Office funcionan conjuntamente?](remoteapp-o365.md) para obtener más información.

Si, durante el período de prueba, no desea obtener una suscripción de prueba de Office 365, use el Office 2013 Professional Plus de imagen de plantilla que se suministra con RemoteApp. Imagen de la plantilla sólo puede usarse durante 30 días y no se puede convertir en una colección de pagada.

Para otras aplicaciones, debe asegurarse de que tiene la licencia para compartir la aplicación.

¿Tenga sentido, derecha? Puede publicar cualquier aplicación de legal tiene derecho a compartir. Y es necesario para asegurarse de que realmente tiene derecho a compartir programas.

Tenga en cuenta que no puede usar un contrato de licencia por volumen de CAL de una colección de la nube. *Puede* utilizar un contrato de licencia por volumen para activar las aplicaciones de la colección de híbrido (excepto para Office). Necesitará instalarlas en su imagen de la plantilla de los medios de licencias por volumen. Siga la información del proveedor de aplicación para instalar licencias en un entorno de escritorio remoto.
