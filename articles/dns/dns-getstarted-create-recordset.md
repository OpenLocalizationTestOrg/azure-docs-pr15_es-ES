<properties
   pageTitle="Crear un conjunto de registros y registros de una zona DNS con PowerShell | Microsoft Azure"
   description="Cómo crear registros de host DNS de Azure. Se establece la configuración de registro y registros con PowerShell"
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



# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>Crear conjuntos de registros de DNS y registros con PowerShell


> [AZURE.SELECTOR]
- [Portal de Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI de Azure](dns-getstarted-create-recordset-cli.md)

En este artículo le guiará por el proceso de creación de registros y conjuntos de registros mediante Windows PowerShell. Después de crear la zona DNS, agregue los registros DNS para su dominio. Para ello, debe comprender los registros DNS y los conjuntos de registros.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="verify-that-you-have-the-latest-version-of-powershell"></a>Compruebe que tiene la versión más reciente de PowerShell

Compruebe que ha instalado la última versión de los cmdlets de PowerShell de administrador de recursos de Azure. Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

## <a name="create-a-record-set-and-record"></a>Crear un conjunto de registros y registro

Esta sección describe cómo crear un conjunto de registros y el registro.


### <a name="1-connect-to-your-subscription"></a>1. conectar a su suscripción

Abra la consola de PowerShell y conectarse a su cuenta. Use el siguiente ejemplo para ayudarle a conectarse:

    Login-AzureRmAccount

Compruebe las suscripciones para la cuenta.

    Get-AzureRmSubscription

Especifique la suscripción que desea usar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Para obtener más información sobre cómo trabajar con PowerShell, vea [Usar Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).


### <a name="2-create-a-record-set"></a>2. crear un conjunto de registros

Crear conjuntos de registros mediante la `New-AzureRmDnsRecordSet` cmdlet. Al crear un conjunto de registros, debe especificar que el registro del conjunto de nombre, la zona, el tiempo de vida (TTL) y el tipo de registro.

Para crear un conjunto de registros del vértice de la zona (en este caso, "contoso.com"), use el nombre de registro "@", incluidas las comillas. Se trata de una convención DNS comunes.

En el ejemplo siguiente se crea un conjunto de registros con el nombre relativo "www" de la zona DNS "contoso.com". El nombre completo del conjunto de registros es "www.contoso.com". El tipo de registro es "A", y el valor TTL es 60 segundos. Después de completar este paso, tendrá un conjunto de registros vacío "www" que se ha asignado a la variable *$rs*.

    $rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### <a name="if-a-record-set-already-exists"></a>Si un registro se establece ya existe

Si un registro ya existe, se produce un error en el comando a menos que la *-Sobrescribir* modificador se utiliza. La *-Sobrescribir* opción activa un mensaje de confirmación, que se puede suprimir mediante el uso de la *-Forzar* cambiar.


    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


En este ejemplo, se especifica la zona utilizando el nombre de la zona y el nombre del grupo de recursos. Como alternativa, puede especificar un objeto de zona, tal como lo devuelve `Get-AzureRmDnsZone` o `New-AzureRmDnsZone`.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

`New-AzureRmDnsRecordSet`Devuelve un objeto local que representa el conjunto de registros que se creó en Azure DNS.

### <a name="3-add-a-record"></a>3. agregar un registro

Para usar el conjunto de registros recién creado "www", debe agregar registros a él. Puede agregar IPv4 *registros al registro "www"* establecen mediante el siguiente ejemplo. En este ejemplo se basa en la variable *$rs* que haya configurado en el paso anterior.

Agregar registros a un registro de establece mediante `Add-AzureRmDnsRecordConfig` es una operación sin conexión. Solo el local variable *$rs* se actualiza.


    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

### <a name="4-commit-the-changes"></a>4. confirmar los cambios

Confirme los cambios en el conjunto de registros. Usar `Set-AzureRmDnsRecordSet` para cargar los cambios en el registro de DNS de Azure.

    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="5-retrieve-the-record-set"></a>5. recuperar el conjunto de registros

Puede recuperar el conjunto de registros de DNS de Azure por usando `Get-AzureRmDnsRecordSet` como se muestra en el ejemplo siguiente.


    Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : MyAzureResourceGroup
    Ttl               : 3600
    Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
    RecordType        : A
    Records           : {134.170.185.46, 134.170.188.221}
    Tags              : {}


También puede usar la herramienta nslookup u otras herramientas DNS para el nuevo conjunto de registros de la consulta.

Si aún no ha delegado el dominio a los servidores de nombres DNS de Azure, debe especificar explícitamente el nombre, el servidor y la dirección de la zona.


    nslookup www.contoso.com ns1-01.azure-dns.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    Name:    www.contoso.com
    Addresses:  134.170.185.46
                134.170.188.221

## <a name="create-a-record-set-of-each-type-with-a-single-record"></a>Crear un conjunto de registros de cada tipo con un solo registro


Los siguientes ejemplos muestran cómo crear un conjunto de registro de cada tipo de registro. Cada conjunto de registros contiene un único registro.

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## <a name="next-steps"></a>Pasos siguientes

[Cómo administrar zonas DNS con PowerShell](dns-operations-dnszones.md)

[Administrar los registros DNS y los conjuntos de registros con PowerShell](dns-operations-recordsets.md)

[Automatizar las operaciones de Azure con .NET SDK](dns-sdk.md)
