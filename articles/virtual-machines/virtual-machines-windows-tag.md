<properties
   pageTitle="Cómo etiquetar una máquina virtual | Microsoft Azure"
   description="Obtenga información sobre el etiquetado de una máquina virtual de Windows creada en Azure utiliza el modelo de implementación de administrador de recursos"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Cómo etiquetar una máquina virtual de Windows en Azure


Este artículo describe diferentes maneras de etiqueta una máquina virtual de Windows Azure a través del modelo de implementación de administrador de recursos. Las etiquetas son pares de clave y valor definidos por el usuario que se pueden colocar directamente en un recurso o un grupo de recursos. Azure actualmente es compatible con un máximo de 15 etiquetas por recurso y grupo de recursos. Las etiquetas se pueden colocadas en un recurso en el momento de creación o agregadas a un recurso existente. Tenga en cuenta que las etiquetas se admiten para recursos creados mediante el modelo de implementación de administrador de recursos solo. Si desea etiquetar una máquina virtual Linux, consulte [cómo etiquetar una máquina virtual de Linux en Azure](virtual-machines-linux-tag.md).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Etiquetado con PowerShell

Para crear, agregar y eliminar etiquetas a través de PowerShell, primero tiene que configurar su [entorno de PowerShell con el Administrador de recursos de Azure][]. Una vez haya completado la instalación, puede colocar las etiquetas en proceso, red y almacenamiento recursos durante la creación o después de crea el recurso a través de PowerShell. En este artículo se centrará en ver y editar etiquetas colocadas en máquinas virtuales de Windows.

En primer lugar, vaya a una máquina Virtual a través de la `Get-AzureRmVM` cmdlet.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Si su máquina Virtual ya contiene etiquetas, verá todas las etiquetas en el recurso:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Si le gustaría agregar etiquetas a través de PowerShell, puede usar el `Set-AzureRmResource` comando. Tenga en cuenta al actualizar etiquetas a través de PowerShell, las etiquetas se actualizan como un todo. Así que si va a agregar una etiqueta a un recurso que ya tiene etiquetas, debe incluir todas las etiquetas que desea colocar en el recurso. A continuación se muestra un ejemplo de cómo agregar etiquetas adicionales a un recurso a través de los Cmdlets de PowerShell.

En este primer cmdlet establece todas las etiquetas que se coloca en *MyTestVM* a la variable *$tags* , usando la `Get-AzureRmResource` y `Tags` propiedad.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

El segundo comando muestra las etiquetas para la variable dada.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment

El tercer comando agrega una etiqueta adicional a la variable *$tags* . Observe el uso de la **+=** para anexar el nuevo par de clave y valor a la lista de *$tags* .

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

El comando cuarto establece todas las etiquetas definidas en la variable *$tags* al recurso determinado. En este caso, es MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

El comando quinto muestra todas las etiquetas en el recurso. Como puede ver, *ubicación* se define ahora como una etiqueta con *MyLocation* como el valor.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment
        Value       MyLocation
        Name        Location

Para obtener más información sobre el etiquetado de PowerShell, consulte los [Cmdlets de recursos de Azure][].

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre el etiquetado de los recursos de Azure, consulte [Información general del Administrador de recursos de Azure][] y [Utilizar etiquetas para organizar los recursos de Azure][].
* Para ver cómo las etiquetas pueden ayudarle a administrar el uso de recursos de Azure, vea [comprender su factura de Azure][] y [obtener recomendaciones sobre el consumo de recursos de Microsoft Azure][].

[Entorno de PowerShell con el Administrador de recursos de Azure]: ../powershell-azure-resource-manager.md
[Cmdlets de recursos de Azure]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Información general de administrador de recursos de Azure]: ../azure-resource-manager/resource-group-overview.md
[Usar etiquetas para organizar los recursos de Azure]: ../resource-group-using-tags.md
[Descripción de la factura de Azure]: ../billing/billing-understand-your-bill.md
[Obtener información sobre el consumo de recursos de Microsoft Azure]: ../billing-usage-rate-card-overview.md
