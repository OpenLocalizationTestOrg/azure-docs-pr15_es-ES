<properties
    pageTitle="Crear una VM Linux mediante una plantilla de Azure | Microsoft Azure"
    description="Crear una VM Linux en Azure mediante una plantilla de administrador de recursos de Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="v-livech"/>

# <a name="create-a-linux-vm-using-an-azure-template"></a>Crear una VM Linux mediante una plantilla de Azure

En este artículo muestra cómo implementar rápidamente una máquina Virtual de Linux en Azure mediante una plantilla de Azure.  El artículo requiere:

- una cuenta de Azure ([obtener una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) iniciado en con `azure login`.

- el modo de administrador de recursos de Azure Azure CLI _debe estar en_ `azure config mode arm`.

También puede implementar una plantilla de Linux VM mediante el [portal de Azure](virtual-machines-linux-quick-create-portal.md).

## <a name="quick-command-summary"></a>Resumen de comandos rápidos

```bash
azure group create \
-n myResourceGroup \
-l westus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

Las plantillas permiten crear máquinas virtuales en Azure con la configuración que desea personalizar durante el inicio, la configuración como nombres de usuario y nombres de host. En este artículo, vamos a iniciar una plantilla de Azure utilizando una VM Ubuntu junto con un grupo de seguridad de la red (GSN) con puerto 22 abierto para SSH.

Plantillas de administrador de recursos Azure son archivos JSON que se pueden usar para las tareas de un solo uso sencillas como iniciar una VM Ubuntu como listo en este artículo.  Plantillas de Azure también pueden utilizarse para crear configuraciones de Azure complejas de entornos completos como una pila de implementación de prueba, desarrollo o producción.

## <a name="create-the-linux-vm"></a>Crear la máquina virtual Linux

En el ejemplo siguiente se muestra cómo llamar `azure group create` para crear un grupo de recursos e implementar una VM Linux protegidos SSH al mismo tiempo con [esta plantilla de administrador de recursos de Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Recuerde que en el ejemplo que se debe usar nombres que son únicos en su entorno. Este ejemplo usa `myResourceGroup` como el nombre del grupo de recursos y `myVM` como el nombre de la máquina virtual.

```bash
azure group create \
--name myResourceGroup \
--location westus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

El resultado debe ser similar al siguiente bloque de resultados:

```bash
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

En el ejemplo que se implementa una máquina virtual utilizando la `--template-uri` parámetro.  También puede descargar o crear una plantilla localmente y pasar a la plantilla mediante la `--template-file` parámetro con una ruta de acceso al archivo de plantilla como argumento. CLI Azure le pide que especifique los parámetros requeridos por la plantilla.

## <a name="next-steps"></a>Pasos siguientes

Buscar en la [Galería de plantillas](https://azure.microsoft.com/documentation/templates/) para descubrir qué marcos de aplicación para implementar siguiente.
