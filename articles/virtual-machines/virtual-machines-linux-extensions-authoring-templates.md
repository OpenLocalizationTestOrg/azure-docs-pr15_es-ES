<properties
   pageTitle="Creación de plantillas con las extensiones de Linux VM | Microsoft Azure"
   description="Obtenga más información sobre la creación de plantillas de administrador de recursos de Azure con extensiones para máquinas virtuales de Linux"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="authoring-azure-resource-manager-templates-with-linux-vm-extensions"></a>Creación de plantillas de administrador de recursos de Azure con extensiones Linux VM

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Desde CLI de Azure, ejecute el siguiente %1"...cerrando:

      Azure VM extension list

Este comando devuelve el nombre de publisher, el nombre de la extensión y la versión como la siguiente:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Asignan estas tres propiedades "publisher", "tipo" y "typeHandlerVersion" respectivamente en el fragmento de código de la plantilla anterior.

>[AZURE.NOTE]Siempre se recomienda usar la última versión de extensión para obtener la funcionalidad más actualizada.

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identifica el esquema para los parámetros de configuración de extensión

El paso siguiente con la creación de una plantilla de extensión es identificar el formato para proporcionar parámetros de configuración. Cada extensión es compatible con su propio conjunto de parámetros.

Para ver ejemplos de configuraciones para las extensiones de Linux, haga clic en la documentación para ver [ejemplos de eExtensions Linux](virtual-machines-linux-extensions-configuration-samples.md).

Consulte los procedimientos siguientes para obtener una plantilla se completará con VM extensiones.

[Extensión de scripts personalizados en una VM Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Después de crear la plantilla, puede implementar mediante la CLI de Azure.
