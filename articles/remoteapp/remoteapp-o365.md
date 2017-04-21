
<properties
    pageTitle="Usar Office con RemoteApp Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo funcionan conjuntamente Office y RemoteApp de Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-office-with-azure-remoteapp"></a>Usar Office con RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Tiene dos opciones para alojar aplicaciones de Office en Azure RemoteApp: Office 365 ProPlus u Office 2013 Professional Plus prueba.

**Hola, ¿sabía que tenemos una nueva mejor artículo que pronto reemplazará esto? Consulte [cómo usar su suscripción a Office 365 con RemoteApp de Azure](remoteapp-officesubscription.md). Abarca toda la información que necesita para usar Office 365 + RemoteApp Azure.**

## <a name="office-365-proplus"></a>Office 365 ProPlus
Puede crear una colección de RemoteApp utilizando la imagen de la plantilla de Office 365 ProPlus. Esta opción permite ampliar su servicio de Office 365 a RemoteApp. Debe tener un plan de suscripción existente y los usuarios deben tener una licencia para el servicio de Office 365 ProPlus alguno independiente o a través de Office 365 planes de servicio.

RemoteApp es compatible con la activación de equipo compartido de Office 365. Al habilitar la activación de equipo compartido y usar la [herramienta de implementación de Office](http://www.microsoft.com/download/details.aspx?id=36778) en la instalación, instale Office 365 ProPlus sin estar activado. Cuando un usuario firma en una colección que contiene Office 365, Office comprueba si el usuario se haya aprovisionado para Office 365. Si es así, Office temporalmente activa Office 365 ProPlus - esta activación continúa hasta que signos de los usuarios en el servicio.

Para utilizar la activación de equipo compartido de Office 365, debe crear una [plantilla personalizada](remoteapp-create-custom-image.md) e instalar Office 365 ProPlus, siguiendo [estas instrucciones](https://technet.microsoft.com/library/dn782858.aspx).

Puede administrar licencias de Office 365 de los usuarios en el [Portal de administración de Office 365](https://portal.office365.com/). Obtenga más información sobre [planes de servicio de Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx).  


## <a name="office-2013-professional-plus-trial"></a>Versión de prueba de Office 2013 Professional Plus
Durante una prueba de 30 días de RemoteApp, puede usar la imagen de plantilla Professional Plus (prueba) de Office 2013 para crear una colección de RemoteApp. Puede asignar a usuarios a esta colección de prueba con sus cuentas de Microsoft o cuentas de trabajo de Azure Active Directory. No se requiere ninguna suscripción adicional.

Esta es una buena opción para echarle y obtener un buen sentimiento de Office en RemoteApp. Sin embargo, esta opción está previsto para la evaluación y pruebas solo. Colecciones de RemoteApp creadas con la imagen de plantilla Professional Plus (prueba) de Office 2013 no pueden cambiar al modo de producción y se desactivará al final del período de prueba.

## <a name="switching-from-trial-to-production"></a>Cambiar de la versión de prueba a producción
Cuando se inicia la versión de prueba gratuita de 30 días, una nota en la sección de RemoteApp del portal le indicará cuánto queda en la versión de prueba antes de transición a una cuenta de pagada. Puede activar su cuenta y cambie al modo de producción mediante el vínculo en esta nota.

Al activar su cuenta, esto afectará a todas las colecciones de RemoteApp en su cuenta.

- Colecciones que ejecutan Windows Server 2012 R2 o las imágenes de plantilla de Office 365 ProPlus pasarán a producción sin problemas. Todos los datos de usuario y configuración, incluyendo las sesiones en curso, permanecen intactos.
- Si ha cargado imágenes de plantilla personalizada, colecciones con estas imágenes también de transición sin problemas.
- La imagen de plantilla Professional Plus (prueba) de Office 2013 está destinada solo de evaluación. Colecciones con esta imagen de plantilla no pueden ser transición a producción. Se colocarán en estado "deshabilitada".


Si no transición a modo de producción por la caducidad de la versión de prueba, se deshabilitarán las colecciones de RemoteApp. No se preocupe: la configuración y datos de los usuarios se guardan para otro 90 días, por lo que puede activar el servicio y cambiar al modo de producción sin pérdida de datos.
