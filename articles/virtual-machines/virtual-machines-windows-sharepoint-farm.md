<properties
    pageTitle="Crear conjuntos de servidores de SharePoint | Microsoft Azure"
    description="Crear rápidamente una nueva granja de SharePoint 2013 o 2016 de SharePoint en Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="josephd"/>

# <a name="create-sharepoint-server-farms"></a>Crear conjuntos de servidores de SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo clásico.

## <a name="sharepoint-2013-farms"></a>Conjuntos de servidores de SharePoint 2013

Con el catálogo de soluciones de portal de Microsoft Azure, puede crear rápidamente preconfigurados granjas de servidores de SharePoint Server 2013. Esto puede ahorrarle una gran cantidad de tiempo cuando se necesita una granja de SharePoint básica o alta disponibilidad de un entorno de desarrollo o prueba o si va a evaluar SharePoint Server 2013 como una solución de colaboración para su organización.

> [AZURE.NOTE] Se ha quitado el elemento de la **Granja de servidores de SharePoint** en el catálogo de soluciones de Azure del portal de Azure. Se ha reemplazado con los elementos de **SharePoint 2013 no HA granja** y **HA granja de SharePoint 2013** .

El conjunto de servidores de SharePoint básico consta de tres equipos virtuales en esta configuración.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

Puede usar esta configuración de la granja de servidores para una instalación simplificada para el desarrollo de aplicaciones de SharePoint o la evaluación de la primera vez de SharePoint 2013.

Para crear el conjunto de servidores de SharePoint (servidor de tres) básica:

1. Haga clic en [aquí](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Haga clic en **desplegar**.
3. En el panel de **SharePoint 2013 no HA granjas** , haga clic en **crear**.
4. Especificar la configuración de los pasos del panel **Crear SharePoint 2013 no HA granja** y, a continuación, haga clic en **crear**.

El conjunto de servidores de SharePoint de alta disponibilidad consta de nueve máquinas virtuales en esta configuración.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

Puede usar esta configuración de la granja de servidores para probar la mayor carga de cliente, alta disponibilidad del sitio de SharePoint externo y grupos de disponibilidad de SQL Server AlwaysOn una granja de SharePoint. También puede usar esta configuración para el desarrollo de aplicaciones de SharePoint en un entorno de alta disponibilidad.

Para crear el conjunto de servidores de SharePoint de alta disponibilidad (servidor de nueve):

1. Haga clic en [aquí](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Haga clic en **desplegar**.
3. En el panel de la **Granja de servidores de SharePoint 2013 HA** , haga clic en **crear**.
4. Especificar la configuración de los siete pasos del panel **Crear 2013 HA granja de SharePoint** y, a continuación, haga clic en **crear**.

> [AZURE.NOTE] No puede crear la **Granja SharePoint 2013 no-HA** o **Granja de SharePoint 2013 HA** con una versión de prueba gratuita de Azure.

El portal de Azure crea ambas de estas granjas de servidores en una red virtual solo nube con una presencia en Internet a través de Internet. No hay ninguna conexión VPN o ExpressRoute de sitios para volver a la red de su organización.

> [AZURE.NOTE] Al crear el basic o entornos de SharePoint de alta disponibilidad con el portal de Azure, no puede especificar un grupo de recursos existente. Para evitar esta limitación, cree estos conjuntos con PowerShell de Azure. Para obtener más información, vea [crear SharePoint 2013 conjuntos de pruebas y desarrollo con PowerShell de Azure](https://technet.microsoft.com/library/mt743093.aspx#powershell).

## <a name="sharepoint-2016-farms"></a>Conjuntos de servidores de SharePoint 2016

Vea [este artículo](https://technet.microsoft.com/library/mt723354.aspx) para obtener las instrucciones crear el conjunto de servidores de SharePoint Server 2016 solo servidor siguiente.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## <a name="managing-the-sharepoint-farms"></a>Administrar los conjuntos de servidores de SharePoint

Puede administrar los servidores de estos conjuntos de servidores a través de conexiones de escritorio remoto. Para obtener más información, vea [iniciar sesión en la máquina virtual](virtual-machines-windows-hero-tutorial.md#log-on-to-the-virtual-machine).

Desde el sitio de SharePoint de Administración Central, puede configurar Mis sitios, aplicaciones de SharePoint y otras funciones. Para obtener más información, vea [Configurar SharePoint](http://technet.microsoft.com/library/ee836142.aspx).

## <a name="next-steps"></a>Pasos siguientes

- Descubra adicionales [configuraciones de SharePoint](https://technet.microsoft.com/library/dn635309.aspx) en servicios de infraestructura de Azure.
