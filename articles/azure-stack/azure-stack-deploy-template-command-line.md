<properties
    pageTitle="Implementar plantillas de la línea de comandos en la pila de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo usar la interfaz de línea de comandos de varias plataformas (CLI) para implementar plantillas desde dentro de la ClientVM o después de usar la conexión VPN para conectarse a la pila de Azure."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Implementar plantillas de pila de Azure mediante la línea de comandos

Utilice la línea de comandos para implementar plantillas de administrador de recursos de Azure en la prueba de concepto de pila de Azure. Plantillas de administrador de recursos de Azure implementación y aprovisionar todos los recursos de la aplicación en una única operación coordinada.

## <a name="download-template"></a>Descargar plantilla        
Para probar una implementación de CLI, descargar los archivos azuredeploy.json y azuredeploy.parameters.json desde la [plantilla de ejemplo de cuenta de almacenamiento de crear](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Implementar plantilla
Vaya a la carpeta donde se descarga y ejecute el comando siguiente para implementar la plantilla de estos archivos:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Este comando implementa la plantilla en el grupo de recursos **cliRG** en la ubicación predeterminada del concepto pila de Azure.

## <a name="validate-template-deployment"></a>Validar la implementación de plantilla
Para ver esta cuenta de almacenamiento y de grupo de recursos, utilice los comandos siguientes:

    azure group list

    azure storage account list

## <a name="next-steps"></a>Pasos siguientes

[Administrar permisos de usuario](azure-stack-manage-permissions.md)
