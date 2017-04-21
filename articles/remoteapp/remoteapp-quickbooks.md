<properties 
    pageTitle="Implementar QuickBooks en Azure RemoteApp | Microsoft Azure" 
    description="Obtenga información sobre cómo compartir QuickBooks con RemoteApp de Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-do-you-deploy-quickbooks-in-azure-remoteapp"></a>¿Cómo implementar QuickBooks en RemoteApp de Azure?

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Use la información siguiente para compartir QuickBooks como una aplicación en Azure RemoteApp.


Puede compartir QuickBooks 2015 Enterprise con RemoteApp de Azure en la colección un híbrido o la nube. El archivo debe residen en una máquina virtual que se está ejecutando el servidor de base de datos de QuickBooks independiente de los servidores de RemoteApp de Azure. Nunca almacenar el archivo de la compañía en la imagen de Azure RemoteApp: pérdida de datos que se espera si hace esto. Sólo QuickBooks Enterprise es compatible con el archivo QuickBooks en un recurso compartido externo con el servidor de base de datos de QuickBooks accesible a través de redes de Windows estándar de hospedaje.   

> [AZURE.IMPORTANT] El servidor de base de datos de QuickBooks que aloja el archivo de empresa debe encontrarse en una máquina virtual independiente dentro de la misma VNET como la colección de RemoteApp de Azure.  

## <a name="steps-to-deploy-quickbooks"></a>Pasos para implementar QuickBooks

1. Crear una máquina virtual de Azure, instale QuickBooks, servidor de base de datos de QuickBooks y coloque el archivo en una máquina virtual de Azure.  Asegúrese de configurar correctamente las reglas de firewall.
2. Instalar QuickBooks en una [imagen personalizada](remoteapp-imageoptions.md) y crear una [colección de RemoteApp de Azure](remoteapp-collections.md), nube o híbrido, dentro de la VNET mismo exacto donde reside el VM aloja el servidor de base de datos de QuickBooks con archivos de la empresa. 
3.  [Publicar](remoteapp-publish.md) Aplicación de QuickBooks a los usuarios
4.  Inicie al cliente de QuickBooks hospedado RemoteApp de Azure, desplácese con las redes de Windows estándar para la máquina virtual que aloja el servidor de base de datos de QuickBooks y abra el archivo de la compañía. 

## <a name="documentation-references"></a>Referencias de documentación

- QuickBooks [configuraciones admitidas](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top)
- [Opciones de implementación](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/) de QuickBooks

También puede comprobar la Ignite presentación, [conceptos básicos de Microsoft Azure RemoteApp de administración](https://channel9.msdn.com/Events/Ignite/2015/BRK3868) - avance rápido a 1:02:45 para obtener acceso a la parte de QuickBooks.

## <a name="deployment-architecture"></a>Arquitectura de implementación

![Implementación de Azure RemoteApp + de QuickBooks](./media/remoteapp-quickbooks/ra-quickbooks.png)