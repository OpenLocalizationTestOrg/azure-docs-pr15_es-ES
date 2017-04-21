<properties
   pageTitle="Crear FQDN para una máquina virtual en el portal de Azure | Microsoft Azure"
   description="Aprenda a crear un nombre de dominio completo o FQDN para un administrador de recursos en función de la máquina virtual en el portal de Azure."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="iainfou"/>

# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Crear un nombre de dominio completo en el portal de Azure
Cuando se crea una máquina virtual (VM) en el [portal de Azure](https://portal.azure.com) utiliza el modelo de implementación de administrador de recursos, se crea automáticamente un recurso IP público para la máquina virtual. Utilice esta dirección IP para tener acceso remoto a la máquina virtual. Aunque el portal no crear un [nombre de dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)o FQDN, de forma predeterminada, puede agregar uno una vez creada la máquina virtual. Este artículo muestra los pasos para crear un nombre DNS o el nombre de dominio completo.

[AZURE.INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Ahora puede conectarse remotamente a la máquina virtual con este nombre DNS como con `ssh adminuser@testdnslabel.centralus.cloudapp.azure.com`.

## <a name="next-steps"></a>Pasos siguientes
Ahora que la máquina virtual tiene un nombre DNS e IP público, puede implementar comunes marcos de aplicaciones o servicios como nginx, MongoDB, Docker, etcetera.

También puede obtener más información sobre cómo [con el Administrador de recursos](../azure-resource-manager/resource-group-overview.md) para obtener sugerencias sobre la creación de las instalaciones de Azure.