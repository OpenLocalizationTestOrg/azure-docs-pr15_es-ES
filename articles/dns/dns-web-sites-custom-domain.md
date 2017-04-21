<properties
   pageTitle="Crear registros DNS personalizados para una aplicación web | Microsoft Azure  "
   description="Cómo crear registros DNS con DNS Azure la aplicación web de dominio personalizado."
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

# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Crear registros DNS para una aplicación web en un dominio personalizado

Puede usar el DNS de Azure para hospedar un dominio personalizado para las aplicaciones web. Por ejemplo, va a crear una aplicación web de Azure y desea que los usuarios accedan a ella, ya sea por utilizando contoso.com o www.contoso.com como un nombre de dominio completo.

Para ello, tiene que crear dos registros:

- Un registro de raíz "A" apuntando a contoso.com
- Un registro de "CNAME" para el nombre de www que el registro de destino

Tenga en cuenta que si crea un registro para una aplicación web en Azure, debe ser manualmente el registro actualiza si la subyacente dirección IP para que los cambios de la aplicación web.

## <a name="before-you-begin"></a>Antes de empezar

Antes de empezar, primero debe crear una zona DNS en DNS de Azure y delegar la zona en el registrador de Azure DNS.

1. Para crear una zona DNS, siga los pasos de [crear una zona DNS](dns-getstarted-create-dnszone.md).
2. Para delegar DNS a Azure DNS, siga los pasos de [la delegación de dominio DNS](dns-domain-delegation.md).

Después de crear una zona y delegar Azure DNS, a continuación, puede crear registros de su dominio personalizado.


## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. crear un registro de su dominio personalizado

Un registro se usa para asignar un nombre a su dirección IP. En el ejemplo siguiente se asignará @ como un registro a una dirección de IPv4:

### <a name="step-1"></a>Paso 1

Crear un registro y asignar a una variable $rs

    $rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600

### <a name="step-2"></a>Paso 2

Agregar el valor de IPv4 para el conjunto de registros creado previamente "@" mediante la variable $rs asignada. El valor de IPv4 asignado será la dirección IP de su aplicación web.

Para encontrar la dirección IP de una aplicación web, siga los pasos de [configurar un nombre de dominio personalizado en la aplicación de servicio de Azure](../web-sites-custom-domain-name.md#Find-the-virtual-IP-address).

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### <a name="step-3"></a>Paso 3

Confirme los cambios en el conjunto de registros. Usar `Set-AzureRMDnsRecordSet` para cargar los cambios en el registro de DNS de Azure:

    Set-AzureRMDnsRecordSet -RecordSet $rs

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Cree un registro CNAME de su dominio personalizado

Si el dominio ya se administra DNS de Azure (Véase [delegación de dominio DNS](dns-domain-delegation.md), puede usar el siguiente el ejemplo para crear un registro CNAME para contoso.azurewebsites.net.

### <a name="step-1"></a>Paso 1

Abra PowerShell y crear un nuevo conjunto de registros CNAME y asignar a una variable $rs. Este ejemplo creará un tipo de conjunto de registros CNAME con un "tiempo de vida" de 600 segundos en la zona DNS denominada "contoso.com".

    $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600

    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
    RecordType        : CNAME
    Records           : {}
    Tags              : {}


### <a name="step-2"></a>Paso 2

Una vez creado el conjunto de registros CNAME, debe crear un valor de alias que se seleccione la aplicación web.

Con la variable previamente asignada "$rs" puede usar el siguiente comando PowerShell para crear el alias de la contoso.azurewebsites.net de la aplicación web.

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"

    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}

### <a name="step-3"></a>Paso 3

Confirme los cambios usando la `Set-AzureRMDnsRecordSet` cmdlet:

    Set-AzureRMDnsRecordSet -RecordSet $rs

Puede validar el registro creado correctamente consultando "www.contoso.com" mediante nslookup, tal como se muestra a continuación:

    PS C:\> nslookup
    Default Server:  Default
    Address:  192.168.0.1

    > www.contoso.com
    Server:  default server
    Address:  192.168.0.1

    Non-authoritative answer:
    Name:    <instance of web app service>.cloudapp.net
    Address:  <ip of web app service>
    Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## <a name="create-an-awverify-record-for-web-apps"></a>Crear un registro de "awverify" para aplicaciones web


Si decide usar un registro para la aplicación web, debe ir a través de un proceso de comprobación para asegurarse de que el propietario del dominio personalizado. Este paso de comprobación se realiza mediante la creación de un registro CNAME especial denominado "awverify". En esta sección se aplica a un solo los registros.


### <a name="step-1"></a>Paso 1

Crear el registro de "awverify". En el ejemplo siguiente, se creará el registro de "aweverify" para contoso.com para comprobar la propiedad del dominio personalizado.

    $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600

    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
    RecordType        : CNAME
    Records           : {}
    Tags              : {}


### <a name="step-2"></a>Paso 2

Una vez creado el conjunto de registros "awverify", asignar el registro CNAME, establecer el alias. En el ejemplo siguiente, se asignará el registro CNAMe alias establecido awverify.contoso.azurewebsites.net.

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"

    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}

### <a name="step-3"></a>Paso 3

Confirme los cambios usando la `Set-AzureRMDnsRecordSet cmdlet`, como se muestra en el siguiente comando.

    Set-AzureRMDnsRecordSet -RecordSet $rs



## <a name="next-steps"></a>Pasos siguientes

Siga los pasos para [configurar un nombre de dominio personalizado para la aplicación de servicio](../app-service-web/web-sites-custom-domain-name.md) para configurar la aplicación web para usar un dominio personalizado.








