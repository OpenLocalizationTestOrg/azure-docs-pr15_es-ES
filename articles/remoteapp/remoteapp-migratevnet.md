<properties
    pageTitle="Cómo migrar desde un VNET RemoteApp a un VNET de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo migrar desde un VNET RemoteApp a un VNET de Azure"
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



# <a name="how-to-migrate-a-hybrid-collection-from-a-remoteapp-vnet-to-an-azure-vnet"></a>Cómo migrar una colección híbrido de un VNET RemoteApp a un VNET de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Enhorabuena. Hemos habilitado implementar colecciones de RemoteApp híbrido directamente en su Azure virtuales redes existentes (VNETs) en lugar de crear VNETs RemoteApp específicos. Esto le permite aprovechar las ventajas de las características más recientes de VNET (por ejemplo, ExpressRoute) y dar a las colecciones de híbrido acceso directo de red a otros servicios de Azure y máquinas virtuales implementadas en ese VNET.  (Esto le mejor rendimiento y configuración más fácil que las configuraciones de VNET a VNET).


Supongamos que ya ha creado una colección de RemoteApp denominada *OriginalCollection* con un RemoteApp VNET denominado *RemoteAppVNET*híbrida. Estos son los pasos para migrar a un nuevo VNET de Azure denominado *AzureVNET*.

1.  En la pestaña de **redes** en el [portal de administración](http://manage.windowsazure.com/), cree una VNET llamado *AzureVNET*, con la misma ubicación, la configuración de DNS y el espacio de direcciones (por lo menos una subred *AzureVNET* ) que utilizó para *RemoteAppVNET*.
2.  Configurar *AzureVNET* a cualquier host o tiene conectividad de red con la implementación de Active Directory *OriginalCollection* es el dominio que se une.
3.  En la pestaña **RemoteApps** , crear una nueva colección de RemoteApp denominada *Nueva colección*. (Use la opción de **crear con VNET** , no **Crear rápido**).
3.  Configurar *NewCollection* para su implementación en una subred en *AzureVNET*.
4.  Configurar *NewCollection* para usar la misma imagen y la información de la combinación de dominio que utilizó para *OriginalCollection*.
5.  Después de unas horas, *NewCollection* aparecerá en la lista de la colección con un estado activo.

Ahora, si no necesita migrar cualquier información de usuario de la colección original a la nueva colección, siga estos pasos a continuación:

6.  Eliminar *OriginalCollection*.
7.  Eliminar *RemoteAppVNET*.

Y haya acabado!

Como alternativa, si necesita migrar información de usuario de la colección original a la nueva colección, siga estos pasos a continuación:

6.  Enviar un correo electrónico a [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration) con su identificador de suscripción de Azure, el nombre de la colección original y el nombre de la nueva colección y pídale que migrar su información de usuario.
7.  Días laborables 2 al equipo de RemoteApp moverá la lista de acceso de usuario y todos los documentos de usuario y configuración de usuario de la colección original a la nueva colección.
8.  Eliminar *OriginalCollection*.
9.  Eliminar *RemoteAppVNET*.

Y ahora, ya ha terminado!

Si tiene alguna pregunta o necesita ayuda especial, correo electrónico [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help).
