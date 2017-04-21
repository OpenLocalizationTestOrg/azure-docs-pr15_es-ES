<properties
    pageTitle="Aplicar directivas a Azure Administrador de recursos máquinas virtuales de Windows | Microsoft Azure"
    description="Cómo aplicar una directiva para una máquina Virtual en Windows Administrador de recursos de Azure"
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
    ms.date="04/13/2016"
    ms.author="singhkay"/>

# <a name="apply-policies-to-azure-resource-manager-virtual-machines"></a>Aplicar directivas a Azure Administrador de recursos máquinas virtuales de Windows

Mediante las directivas, puede aplicar una organización diversas convenciones y reglas de toda la empresa. Aplicación del comportamiento deseado puede ayudar a mitigar el riesgo que contribuyen al éxito de la organización. En este artículo, describiremos cómo puede usar el Administrador de recursos de Azure directivas para definir el comportamiento deseado para máquinas virtuales de su organización.

El esquema de los pasos para llevar a cabo esto es como debajo

1. Directiva de administrador de recursos de Azure 101
2. Definir una directiva para el equipo Virtual
3. Crear la directiva
4. Aplicar la directiva

## <a name="azure-resource-manager-policy-101"></a>Directiva de administrador de recursos de Azure 101

Para empezar a usar directivas del Administrador de recursos de Azure, se recomienda leer el artículo siguiente y, a continuación, continúe con los pasos de este artículo. El siguiente artículo describe la definición básica y la estructura de una directiva, cómo directivas se evalúa y proporciona varios ejemplos de definiciones de directiva.

* [Usar la directiva de administración de recursos y controlar el acceso](../resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>Definir una directiva para el equipo Virtual

Uno de los escenarios comunes para la empresa puede ser solo permite a los usuarios crear máquinas virtuales de los sistemas operativos específicos que se han probado para que sea compatible con una aplicación empresarial. Utilizar una directiva de administrador de recursos de Azure esta tarea puede realizarse en unos cuantos pasos. En este ejemplo de directiva, vamos a permitir solo Windows Server 2012 R2 centro de datos máquinas virtuales que debe crearse. La definición de la directiva es similar a continuación

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "MicrosoftWindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "WindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "2012-R2-Datacenter"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

La directiva anterior se puede modificar fácilmente a un escenario donde desea permitir que cualquier imagen en el centro de datos de Windows Server que se usará para una implementación de máquina Virtual con la debajo de cambio

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*Datacenter"
}
```

#### <a name="virtual-machine-property-fields"></a>Campos de propiedades de la máquina virtual

La siguiente tabla describe las propiedades de la máquina Virtual que se pueden usar como campos en la definición de una directiva. Para más información sobre los campos de la directiva, consulte el artículo siguiente:

* [Campos y orígenes](../resource-manager-policy.md#fields-and-sources)


| Nombre de campo     | Descripción                                        |
|----------------|----------------------------------------------------|
| imagePublisher | Especifica el Editor de la imagen               |
| imageOffer     | Especifica la oferta para el Editor de imagen seleccionado |
| imageSku       | Especifica la SKU para la oferta elegida             |
| imageVersion   | Especifica la versión de la imagen para la SKU seleccionada     |

## <a name="create-the-policy"></a>Crear la directiva

Una directiva fácilmente pueden crearse con la API de REST directamente o los cmdlets de PowerShell. Para crear la directiva, consulte el artículo siguiente:

* [Creación de una directiva](../resource-manager-policy.md#creating-a-policy)


## <a name="apply-the-policy"></a>Aplicar la directiva

Después de crear la directiva debe aplicar en un ámbito definido. El ámbito puede ser una suscripción, grupo de recursos o incluso el recurso. Para aplicar la directiva, consulte el artículo siguiente:

* [Creación de una directiva](../resource-manager-policy.md#applying-a-policy)
