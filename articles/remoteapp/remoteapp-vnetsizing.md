
<properties
    pageTitle="Tamaño de la información para un VNET en Azure RemoteApp | Microsoft Azure"
    description="Obtenga más información sobre los requisitos de la dirección IP de RemoteApp de Azure con un VNET"
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



# <a name="sizing-information-for-a-vnet-in-azure-remoteapp"></a>Información de tamaño de una VNET de RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Al usar RemoteApp de Azure con una red virtual (VNET), RemoteApp usa direcciones IP dentro de la subred. En función de la escala de su servicio de RemoteApp, debe asegurarse de que la subred tenga suficientes direcciones IP disponibles para máquinas virtuales de RemoteApp. Aunque esta guía de tamaño no es perfecta dado cómo RemoteApp dinámicamente gira y gira hacia abajo máquinas virtuales dentro de una colección, le ayudará estimar el intervalo de subred. Esto es especialmente importante, ya que, una vez que un servicio de RemoteApp se coloca en una VNET, no se puede aumentar el tamaño de subred sin eliminar RemoteApp.

Para cada colección de RemoteApp que desea ejecutar en la capacidad máxima, debe tener 100 direcciones IP disponibles. Por ejemplo, si tiene una colección de RemoteApp en el plan estándar y desea que tengan el número máximo de 500 usuarios, debe tener 100 direcciones IP para esa colección. Asimismo, necesita 100 direcciones IP para una colección de RemoteApp en el plan básico que tiene 800 usuarios. Si piensa tiene menos usuarios (menor que el valor máximo), puede reducir las direcciones IP es necesarios por colección. El requisito de tamaño mínimo subred es 30 direcciones IP (/ 27).

Consulte la información siguiente para asegurarse de que su VNET está correctamente configurado y en funcionamiento:

- [Migrar desde un VNET personal a un VNET de Azure](remoteapp-migratevnet.md)
- [Validar la VNET de Azure para usar con RemoteApp de Azure](remoteapp-vnet.md)
