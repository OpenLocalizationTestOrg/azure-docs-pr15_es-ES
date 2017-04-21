<properties
   pageTitle="Plantilla de administrador de recursos para el almacenamiento de | Microsoft Azure"
   description="Muestra el esquema de administrador de recursos de implementación de cuentas de almacenamiento a través de una plantilla."
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="storage-account-template-schema"></a>Esquema de plantilla de cuenta de almacenamiento

Crea una cuenta de almacenamiento.

## <a name="schema-format"></a>Formato de esquema

Para crear una cuenta de almacenamiento, agregue el esquema siguiente a la sección de recursos de la plantilla.

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
            "accountType": string
        }
    }

## <a name="values"></a>Valores

Las tablas siguientes describen los valores que se debe establecer en el esquema.

| Nombre | Valor |
| ---- | ---- |
| tipo | Enumeración<br />Obligatorio<br />**Microsoft.Storage/storageAccounts**<br /><br />Tipo de recurso que se va a crear. |
| apiVersion | Enumeración<br />Obligatorio<br />**2015-06-15** o **vista previa de 2015-05-01**<br /><br />La versión de la API para crear el recurso. | 
| nombre | Cadena<br />Obligatorio<br />Entre 3 y 24 caracteres, sólo números y letras minúsculas.<br /><br />El nombre de la cuenta de almacenamiento para crear. El nombre debe ser único en todos los de Azure. Considere la posibilidad de usar la función [uniqueString](resource-group-template-functions.md#uniquestring) con la convención de nomenclatura tal como se muestra en el ejemplo siguiente. |
| ubicación | Cadena<br />Obligatorio<br />Región que admite cuentas de almacenamiento. Para determinar las regiones válidas, vea [admitidos regiones](resource-manager-supported-services.md#supported-regions).<br /><br />La región de la cuenta de almacenamiento de host. |
| propiedades | Objeto<br />Obligatorio<br />[objeto de propiedades](#properties)<br /><br />Un objeto que especifica el tipo de cuenta de almacenamiento para crear. |

<a id="properties" />
### <a name="properties-object"></a>objeto de propiedades

| Nombre | Valor |
| ---- | ---- | 
| accountType | Cadena<br />Obligatorio<br />**Standard_LRS**, **Standard_ZRS**, **Standard_GRS**, **Standard_RAGRS**o **Premium_LRS**<br /><br />El tipo de cuenta de almacenamiento. Los valores permitidos corresponden a estándar localmente redundantes, redundantes de zona estándar, redundantes de Geo estándar, redundantes de Geo de acceso de lectura estándar y Premium localmente redundantes. Para obtener información acerca de estos tipos de cuenta, vea [replicación de almacenamiento de Azure](./storage/storage-redundancy.md ). |

    
## <a name="examples"></a>Ejemplos

En el ejemplo siguiente se implementa una cuenta de almacenamiento estándar redundante local con un nombre único basado en el identificador del grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
                "location": "[resourceGroup().location]",
                "properties": 
            {
                    "accountType": "Standard_LRS"
            }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Tutorial rápido plantillas

Hay muchas plantillas de tutorial rápido que incluyen una cuenta de almacenamiento. Las siguientes plantillas muestran algunos escenarios comunes:

- [Crear una cuenta de almacenamiento estándar](https://azure.microsoft.com/documentation/templates/101-storage-account-create)
- [Implementación simple de una máquina virtual de Windows](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows)
- [Implementación simple de una VM Linux](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux)
- [Crear un perfil de CDN, un extremo de CDN con una cuenta de almacenamiento como origen](https://azure.microsoft.com/documentation/templates/201-cdn-with-storage-account)
- [Crear una granja de SharePoint alta disponibilidad con 9 máquinas virtuales con la extensión de DSC Powershell](https://azure.microsoft.com/documentation/templates/sharepoint-server-farm-ha)
- [Implementación simple de un 5 nodo seguro servicio tela clúster con TORUNDA habilitado](https://azure.microsoft.com/documentation/templates/service-fabric-secure-cluster-5-node-1-nodetype-wad)
- [Crear una máquina Virtual de una imagen de Windows con 4 discos de datos vacía](https://azure.microsoft.com/documentation/templates/101-vm-multiple-data-disk)


## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general acerca de almacenamiento, vea [Introducción a Microsoft Azure almacenamiento](./storage/storage-introduction.md).
- Por ejemplo plantillas que utilizar una nueva cuenta de almacenamiento con una máquina Virtual, vea [implementar un simple Linux VM](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) o [implementar una máquina virtual de Windows simple](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).
