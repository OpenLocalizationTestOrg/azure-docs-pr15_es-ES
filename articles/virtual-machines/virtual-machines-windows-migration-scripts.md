<properties
    pageTitle="Herramientas de comunidad para la administración de servicio de Azure a la migración del Administrador de recursos de Azure"
    description="En este artículo catálogos de las herramientas que ha proporcionado por la Comunidad para ayudar con la migración de IaaS recursos de administración de servicios de Azure en la pila de administrador de recursos de Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="singhkay"/>

# <a name="community-tools-for-azure-service-management-to-azure-resource-manager-migration"></a>Herramientas de comunidad para la administración de servicio de Azure a la migración del Administrador de recursos de Azure

En este artículo catálogos de las herramientas que ha proporcionado por la Comunidad para ayudar con la migración de IaaS recursos de administración de servicios de Azure en la pila de administrador de recursos de Azure.

>[AZURE.NOTE]Estas herramientas no son compatibles oficialmente con Microsoft Support. Por lo tanto, su origen en Github están abiertos y estamos felices Aceptar PR de correcciones o escenarios adicionales. Para informar de problemas, use la característica de problemas de Github.
>
> Migrar con estas herramientas hará que el tiempo de inactividad de la máquina Virtual clásico. Si está buscando la migración de plataformas compatibles, visite 
>
>- [Migración de plataformas compatibles de recursos de IaaS de clásico a pila de administrador de recursos de Azure](./virtual-machines-windows-migration-classic-resource-manager.md)
>- [Análisis técnico detallado en plataforma admite migración de clásico para el Administrador de recursos de Azure](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
>- [Migrar recursos de IaaS de clásico a Azure Administrador de recursos con PowerShell de Azure](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## <a name="asm2arm"></a>ASM2ARM

Se trata de un módulo de script de PowerShell para migrar su **única** Máquina Virtual (VM) de la pila de administración de servicios de Azure a pila de administrador de recursos de Azure. 

[Vínculo a la documentación de la herramienta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz

migAz es una opción adicional para migrar un conjunto completo de recursos de IaaS de administración del servicio de Azure a recursos de IaaS del Administrador de recursos de Azure. La migración puede ocurrir dentro de la misma suscripción o entre varias suscripciones y tipos de suscripción (ex: suscripciones CSP).

[Vínculo a la documentación de la herramienta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)