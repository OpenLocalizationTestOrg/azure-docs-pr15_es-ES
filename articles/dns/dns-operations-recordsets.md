<properties
   pageTitle="Administrar conjuntos de registros de DNS y registros mediante el portal de Azure | Microsoft Azure"
   description="Administrar conjuntos de registros de DNS y los registros de DNS de Azure cuando su dominio en Azure DNS de host. Todos los comandos de PowerShell para operaciones de conjuntos de registros y registros."
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

# <a name="manage-dns-records-and-record-sets-by-using-powershell"></a>Administrar los registros DNS y los conjuntos de registros con PowerShell



> [AZURE.SELECTOR]
- [Portal de Azure](dns-operations-recordsets-portal.md)
- [CLI de Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



Este artículo muestra cómo administrar los registros de la zona DNS y conjuntos de registros mediante Windows PowerShell.

Es importante comprender la diferencia entre los conjuntos de registros de DNS y los registros DNS individuales. Un conjunto de registros es el conjunto de registros de una zona que tienen el mismo nombre y el mismo tipo. Para obtener más información, consulte [registros mediante el portal de Azure y conjuntos de registros de DNS de crear](dns-getstarted-create-recordset-portal.md).

Para administrar los conjuntos de registros y los registros, debe obtener la última versión de los cmdlets de PowerShell de administrador de recursos de Azure. Para obtener más información, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md). Para obtener más información sobre cómo trabajar con PowerShell, vea [Usar PowerShell de Azure con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).



## <a name="create-a-new-record-set-and-a-record"></a>Crear un nuevo conjunto de registros y un registro

Para crear un registro que establezca con PowerShell, vea [registros con PowerShell y conjuntos de registros de DNS de crear](dns-getstarted-create-recordset.md).

## <a name="get-a-record-set"></a>Obtener un conjunto de registros

Para recuperar un conjunto de registros existente, utilice `Get-AzureRmDnsRecordSet`. Especificar que el registro del conjunto de nombre relativo, el tipo de registro y la zona.

    $rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Como ocurre con `New-AzureRmDnsRecordSet`, el nombre de registro debe ser un nombre relativo, lo que significa que debe excluir el nombre de la zona.

Puede especificar la zona utilizando el nombre de la zona y el nombre del grupo de recursos, o mediante un objeto de zona:

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone

`Get-AzureRmDnsRecordSet`Devuelve un objeto local que representa el conjunto de registros que se creó en Azure DNS.

## <a name="list-record-sets"></a>Conjuntos de registros de la lista

También puede usar`Get-AzureRmDnsRecordSet` a conjuntos de registros de lista si se omite la *– nombre* o parámetros *, El tipo de registro* .

### <a name="to-list-all-record-sets"></a>Para obtener una lista de todos los conjuntos de registros

Este ejemplo devuelve todos los conjuntos de registros, independientemente del nombre o el tipo de registro:

    $list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-list-record-sets-of-a-given-record-type"></a>Para los conjuntos de registros de lista de un determinado tipo de registro

Este ejemplo devuelve todos los conjuntos de registros que coincidan con el tipo de registro especificado. En este caso, el registro de conjunto de devuelta es registros "A":

    $list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

La zona puede especificarse utilizando alguno los *Nombre_zona-* y *-ResourceGroupName* parámetros (como se muestra), o que especifica un objeto de zona:

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $list = Get-AzureRmDnsRecordSet -Zone $zone

## <a name="add-a-record-to-a-record-set"></a>Agregar un registro a un conjunto de registros

Agregar registros a conjuntos de registros mediante la `Add-AzureRmDnsRecordConfig` cmdlet. Se trata de una operación sin conexión. Se cambia únicamente el objeto local que representa el conjunto de registros.

Los parámetros para agregar registros a un conjunto de registros varían según el tipo de conjunto de registros. Por ejemplo, cuando se usa un conjunto de registros de tipo "A", sólo puede especificar los registros con el parámetro *-IPv4Address*.

Registros adicionales pueden agregarse a cada conjunto de registros por llamadas adicionales a `Add-AzureRmDnsRecordConfig`. Puede agregar hasta 20 registros a cualquier conjunto de registros. Sin embargo, conjuntos de registros de tipo "CNAME" pueden contener un máximo de un registro y un conjunto de registros no puede contener dos registros idénticos. Conjuntos de registros vacíos (con cero registros) se pueden crear, pero no aparecen en los servidores de nombres DNS de Azure.

Después de que el conjunto de registros contiene la colección deseada de registros, debe confirmar utilizando la `Set-AzureRmDnsRecordSet` cmdlet. Una vez se ha confirmado un conjunto de registros, reemplaza el registro existente que se establece en Azure DNS.

### <a name="to-create-an-a-record-set-with-a-single-record"></a>Para crear un registro a establecer con un solo registro

    $rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

La secuencia de operaciones para crear un registro también pueden *por departamento*, es decir, que pase el objeto de conjunto de registros mediante la canalización en lugar de pasar como un parámetro. Por ejemplo:

    New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="additional-record-type-examples"></a>Ejemplos de tipo de registro adicionales

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="modify-existing-record-sets"></a>Modificar los conjuntos de registros existentes

Los pasos para modificar un conjunto de registros existente son similares a los pasos que realice cuando cree registros. La secuencia de operaciones es la siguiente:

1.  Recuperar el registro existente establecer mediante `Get-AzureRmDnsRecordSet`.

2.  Modificar el conjunto, ya sea agregando registros, quitar registros, cambiar los parámetros de registro de registros o cambiar el registro Ajuste período de vida (TTL). Se trata de una operación sin conexión. Se cambia únicamente el objeto local que representa el conjunto de registros.

3.  Confirmar los cambios mediante el uso de la `Set-AzureRmDnsRecordSet` cmdlet. Esto reemplazará la existente conjunto de registros de DNS de Azure.


### <a name="to-update-a-record-in-an-existing-record-set"></a>Actualizar un registro en un conjunto de registros existente

En este ejemplo, se cambia la dirección IP de un registro "A" existente:

    $rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Ipv4Address = "134.170.185.46"
    Set-AzureRmDnsRecordSet -RecordSet $rs

La `Set-AzureRmDnsRecordSet` cmdlet usa etag comprobaciones para asegurarse de que no se sobrescriben los cambios simultáneas. Usar el *-Sobrescribir* marca suprimir estas comprobaciones. Para obtener más información, vea [información sobre etiquetas y etags](dns-getstarted-create-dnszone.md#tagetag).

### <a name="to-modify-an-soa-record"></a>Para modificar un registro de inicio de autoridad

No puede agregar o quitar registros de establecer en el vértice de zona el registro de inicio de autoridad creada automáticamente (nombre = "@"). Sin embargo, puede modificar cualquiera de los parámetros en el registro de inicio de autoridad (excepto "Host") y el registro establece TTL.

En el ejemplo siguiente se muestra cómo cambiar la propiedad de *correo electrónico* del registro de inicio de autoridad:

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Email = "admin.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar los registros del servidor DNS en el vértice de zona

No puede agregar, para quitar o modificar los registros de creados automáticamente NS conjunto de registros en el vértice de zona (nombre = "@"). Es el único cambio que está permitido modificar el conjunto de registros TTL.

En el ejemplo siguiente se muestra cómo cambiar la propiedad TTL del conjunto de registros de NS:

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Ttl = 300
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-add-records-to-an-existing-record-set"></a>Agregar registros a un conjunto de registros existente.

En este ejemplo, hemos agregar dos registros MX adicionales para el conjunto de registros existente:

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="remove-a-record-from-an-existing-record-set"></a>Quitar un registro de un conjunto de registros existente.

Se pueden quitar los registros de un registro de establecer mediante `Remove-AzureRmDnsRecordConfig`. El registro que se va a quitar debe ser una coincidencia exacta con un registro existente en todos los parámetros. Se deben confirmar cambios usando `Set-AzureRmDnsRecordSet`.

Quitar el último registro de un conjunto de registros, no elimina el conjunto de registros. Para obtener más información, vea [Eliminar un conjunto de registro](#delete-a-record-set) a continuación.


    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

La secuencia de operaciones para quitar un registro de un conjunto de registros puede también se por departamento, es decir, que pase el objeto de conjunto de registros mediante la canalización en lugar de pasar como un parámetro. Por ejemplo:

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Quitar un registro AAAA de un conjunto de registros

    $rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-cname-record-from-a-record-set"></a>Quitar un registro CNAME de un conjunto de registros

Como un conjunto de registros CNAME puede contener un máximo de un registro, quitar dicho registro deja un conjunto de registros vacío.

    $rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-mx-record-from-a-record-set"></a>Quitar un registro MX de un conjunto de registros

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-ns-record-from-record-set"></a>Quitar un registro NS de conjunto de registros.

    $rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-srv-record-from-a-record-set"></a>Quitar un registro SRV de un conjunto de registros

    $rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-txt-record-from-a-record-set"></a>Quitar un registro TXT de un conjunto de registros

    $rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="delete-a-record-set"></a>Eliminar un conjunto de registros

Conjuntos de registros pueden eliminarse mediante la `Remove-AzureRmDnsRecordSet` cmdlet. No se puede eliminar el inicio de autoridad y registro NS se establece en el vértice de zona (nombre = "@") que se crearon automáticamente cuando se creó la zona. Se eliminarán automáticamente si se elimina la zona.

Para quitar un conjunto de registros, use uno de los tres métodos siguientes:

### <a name="specify-all-the-parameters-by-name"></a>Especificar todos los parámetros por nombre

El opcional *-Forzar* conmutador puede usarse para suprimir el mensaje de confirmación.

    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### <a name="specify-the-record-set-by-name-and-type-and-specify-the-zone-by-object"></a>Especificar el conjunto de registros por nombre y tipo y especifica la zona de objeto

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### <a name="specify-the-record-set-by-object"></a>Especificar el conjunto de registros por objeto

    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Cuando se especifica el registro que se establece mediante un objeto, habilita comprobaciones etag para asegurarse de que no se eliminan los cambios simultáneas. El opcional *-Sobrescribir* marca suprime estas comprobaciones. Para obtener más información, vea [Etags y etiquetas](dns-getstarted-create-dnszone.md#tagetag) .

También se puede por departamento el objeto de conjunto de registros en lugar de que se pasa como un parámetro:

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de DNS de Azure, consulte [Introducción a Azure DNS](dns-overview.md). Para obtener información sobre la automatización de DNS, vea [zonas DNS de creación y registro establece mediante .NET SDK](dns-sdk.md).

Para obtener más información acerca de los registros DNS inversos, consulte [cómo administrar los registros DNS inversos de los servicios con PowerShell](dns-reverse-dns-record-operations-ps.md).
