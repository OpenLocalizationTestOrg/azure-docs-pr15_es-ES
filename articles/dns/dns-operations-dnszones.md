<properties
   pageTitle="Administrar zonas DNS con PowerShell | Microsoft Azure"
   description="Puede administrar zonas DNS con Powershell de Azure. Cómo actualizar, eliminar y crear zonas DNS en DNS de Azure"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="how-to-manage-dns-zones-using-powershell"></a>Cómo administrar zonas DNS con PowerShell

> [AZURE.SELECTOR]
- [CLI de Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)



En este artículo le mostrará cómo usar PowerShell para administrar la zona DNS. Para poder seguir estos pasos, debe instalar la última versión de los cmdlets de PowerShell de administrador de recursos de Azure (1.0 o posteriores). Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .


## <a name="create-a-new-dns-zone"></a>Crear una nueva zona DNS

Para crear una zona DNS, vea [crear una zona DNS con PowerShell](dns-getstarted-create-dnszone.md).

## <a name="get-a-dns-zone"></a>Obtener una zona DNS

Para recuperar una zona DNS, utilice el `Get-AzureRmDnsZone` cmdlet. Esta operación devuelve un objeto de zona DNS correspondiente a una zona existente en Azure DNS. El objeto contiene datos sobre la zona (como el número de conjuntos de registros), pero no contiene los conjuntos de registro a sí mismos.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

## <a name="list-dns-zones"></a>Zonas DNS de la lista

Si se omite el nombre de la zona de `Get-AzureRmDnsZone`, se pueden enumerar todas las zonas de un grupo de recursos. Esta operación devuelve una matriz de objetos de zona.

    $zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

## <a name="update-a-dns-zone"></a>Actualizar una zona DNS

Se pueden realizar cambios en un recurso de zona DNS usando `Set-AzureRmDnsZone`. No se actualiza cualquiera de los conjuntos de registros de DNS dentro de la zona (consulte [cómo administrar DNS records](dns-operations-recordsets.md)). Solo se utiliza para actualizar las propiedades del recurso de zona propiamente dicho. Esto es actualmente limitado para el Administrador de recursos de Azure 'etiquetas' para el recurso de zona. Para obtener más información, vea [Etags y etiquetas](dns-getstarted-create-dnszone.md#Etags-and-tags) .

Use uno de los siguientes dos formas de actualización de zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Especificar la zona utilizando el grupo de recursos y el nombre de zona

    Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### <a name="specify-the-zone-using-a-zone-object"></a>Especificar la zona utilizando un objeto $zone

Especificar la zona utilizando un objeto de $zone de `Get-AzureRmDnsZone`. Cuando se usa `Set-AzureRmDnsZone` con un objeto $zone, se usará Etag comprobaciones para asegurarse de que no se sobrescriben los cambios simultáneas. Puede usar la opcional *-Sobrescribir* cambiar suprimir estas comprobaciones. Para obtener más información, vea [Etags y etiquetas](dns-getstarted-create-dnszone.md#Etags-and-tags) .


    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    <..modify $zone.Tags here...>
    Set-AzureRmDnsZone -Zone $zone [-Overwrite]


## <a name="delete-a-dns-zone"></a>Eliminar una zona de DNS

Las zonas DNS pueden eliminarse mediante el cmdlet quitar AzureRmDnsZone.

Antes de eliminar una zona DNS en DNS de Azure, debe eliminar todos los conjuntos de registros, salvo los registros NS y SOA en la raíz de la zona creados automáticamente cuando se creó la zona.

Utilice una de las dos maneras siguientes para quitar una zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Especificar la zona utilizando el nombre de la zona y el nombre del grupo de recursos

Esta operación tiene opcional *-Forzar* conmutador que se muestre el mensaje para confirmar que desea quitar la zona DNS.

    Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]

### <a name="specify-the-zone-using-a-zone-object"></a>Especificar la zona utilizando un objeto $zone

Especificar la zona utilizando un objeto de $zone de `Get-AzureRmDnsZone`. Esta operación tiene opcional *-Forzar* conmutador que se muestre el mensaje para confirmar que desea quitar la zona DNS. Como ocurre con `Set-AzureRmDnsZone`, que especifica la zona mediante un objeto $zone habilita comprobaciones de Etag para asegurarse de que no se eliminan los cambios simultáneas. <BR>
El opcional *-Sobrescribir* marca suprime estas comprobaciones. Para obtener más información, vea [Etags y etiquetas](dns-getstarted-create-dnszone.md#Etags-and-tags) .

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]



El objeto de zona también se puede por departamento en lugar de que se pasa como un parámetro:

    Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## <a name="next-steps"></a>Pasos siguientes

Después de crear una zona DNS, crear [conjuntos de registros y los registros](dns-getstarted-create-recordset.md) para iniciar la resolución de nombres de su dominio de Internet.