<properties
    pageTitle="Configurar clave depósito para máquinas virtuales de Windows en el Administrador de recursos de Azure | Microsoft Azure"
    description="Cómo configurar depósito clave para su uso con una máquina virtual de administrador de recursos de Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="singhkay"/>

# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurar clave depósito para máquinas virtuales de Windows en el Administrador de recursos de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica

En la pila de administrador de recursos de Azure, información confidencial o certificados son modelar como recursos proporcionados por el proveedor de recursos de depósito de clave. Para obtener más información acerca de la cámara de clave de Azure, consulte [¿Qué es depósito de clave de Azure?](../key-vault/key-vault-whatis.md)

En orden para clave depósito utilizar con máquinas virtuales de administrador de recursos de Azure, debe estar establecida la propiedad *EnabledForDeployment* en depósito de clave True. Puede hacerlo en varios clientes."

## <a name="use-cli-to-set-up-key-vault"></a>Utilice CLI para configurar el depósito de clave
Para crear un depósito clave mediante la interfaz de línea de comandos (CLI), vea [Administrar clave depósito con CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Para CLI, debe crear el depósito clave antes de asignar la directiva de implementación. Puede hacerlo mediante el siguiente comando:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Use plantillas para configurar la clave de cámara
Cuando utiliza una plantilla, es necesario configurar la `enabledForDeployment` propiedad `true` para el recurso de depósito de clave.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Para ver otras opciones que puede configurar cuando se crea un depósito clave con las plantillas, vea [crear un depósito clave](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
