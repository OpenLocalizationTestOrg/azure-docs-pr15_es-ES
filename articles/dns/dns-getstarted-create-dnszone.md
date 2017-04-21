<properties
   pageTitle="Introducción a Azure DNS | Microsoft Azure"
   description="Aprenda a crear zonas DNS en DNS de Azure. Se trata de un paso a paso para obtener la primera zona DNS que se creó para iniciar su dominio DNS con PowerShell de hospedaje."
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

# <a name="create-a-dns-zone-using-powershell"></a>Crear una zona DNS con Powershell

> [AZURE.SELECTOR]
- [Portal de Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [CLI de Azure](dns-getstarted-create-dnszone-cli.md)

En este artículo le guiará por los pasos para crear una zona DNS con PowerShell. También puede crear una zona DNS con el portal de Azure o CLI.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>Sobre Etags y etiquetas

### <a name="etags"></a>ETags

Supongamos que dos personas o dos procesos intentan modificar un registro DNS al mismo tiempo. ¿Cuál gana? ¿Y el ganador sabe que solo han sobrescribir cambios creados por otra persona?

Azure DNS utiliza Etags para controlar simultáneas cambios en el mismo recurso de forma segura. Cada recurso DNS (zona o conjunto de registros) tiene un valor de Etag asociado. Siempre que un recurso se recupera, también se recupera su Etag. Cuando se actualiza un recurso, tiene la opción para devolver el valor Etag para que Azure DNS puede comprobar que Etag en las coincidencias de servidor. Puesto que provoca el Etag que se regenera cada actualización a un recurso, un error de coincidencia de Etag indica que se ha producido un cambio simultáneo. ETags también se usan cuando se crea un nuevo recurso para asegurarse de que no existe el recurso.

De forma predeterminada, Azure DNS PowerShell usa Etags para bloquear cambios simultáneas de las zonas y conjuntos de registros. El opcional *-Sobrescribir* conmutador puede usarse para suprimir Etag comprobaciones, en cuyo caso los cambios simultáneos que se han producido se sobrescribirán.

En el nivel de la API de REST de DNS de Azure, Etags se especifican mediante encabezados HTTP.  Su comportamiento se expresa en la siguiente tabla:

|Encabezado|Comportamiento|
|------|--------|
|Ninguno|SUPERPONER siempre se realiza correctamente (sin comprobaciones Etag)|
|Coincidir If<etag>|SUPERPONER solo se realiza correctamente si existe el recurso y Etag coincide con|
|Coincidir If *     | SUPERPONER solo se realiza correctamente si existe recurso|
|If lo ninguna coincidencia * |  SUPERPONER solo se realiza correctamente si el recurso no existe|

### <a name="tags"></a>Etiquetas

Etiquetas son diferentes de Etags. Las etiquetas son una lista de pares de valor de nombre y se utilizan por el Administrador de recursos de Azure a recursos de la etiqueta con fines de facturación o agrupamiento. Para obtener más información acerca de las etiquetas, vea [usar etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

Azure PowerShell DNS admite etiquetas en zonas y conjuntos de registros especificados con las opciones `-Tag` parámetro.


## <a name="before-you-begin"></a>Antes de empezar

Compruebe que tiene los siguientes elementos antes de comenzar la configuración.

- Una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar los [beneficios de suscriptor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o signo hacia arriba para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Debe instalar la última versión de los cmdlets de PowerShell de administrador de recursos de Azure (1.0 o posteriores). Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

## <a name="step-1---sign-in"></a>Paso 1: iniciar sesión

Abra la consola de PowerShell y conectarse a su cuenta. Para obtener más información, vea [Usar Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

Use el siguiente ejemplo para ayudarle a conectarse:

    Login-AzureRmAccount

Compruebe las suscripciones para la cuenta.

    Get-AzureRmSubscription

Especifique la suscripción que desea usar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="step-2---create-a-resource-group"></a>Paso 2: crear un grupo de recursos

Administrador de recursos de Azure requiere que todos los grupos de recursos especifican una ubicación. Se usa como ubicación predeterminada para los recursos de ese grupo de recursos. Sin embargo, dado que todos los recursos DNS son global, no regional, la opción de ubicación de grupo de recursos tiene ningún efecto en Azure DNS.

Puede omitir este paso si está utilizando un grupo de recursos existente.

    New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## <a name="step-3---register"></a>Paso 3: registrar

El proveedor de recursos de Microsoft.Network administra el servicio DNS de Azure. Su suscripción de Azure debe registrarse para usar este proveedor de recursos para poder usar DNS de Azure. Se trata de una operación de una sola vez para cada suscripción.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## <a name="step-4----create-a-dns-zone"></a>Paso 4: crear una zona DNS

Se crea una zona DNS mediante la `New-AzureRmDnsZone` cmdlet. Hay ejemplos siguientes para crear una zona DNS con o sin etiquetas. Para obtener más información acerca de las etiquetas, vea la sección de las [etiquetas](#tags) en este artículo.

>[AZURE.NOTE] En Azure DNS, nombres de zona deben especificarse sin una terminación **'.'**. Por ejemplo, como '**contoso.com**' en lugar de '**contoso.com.**'.

### <a name="to-create-a-dns-zone"></a>Para crear una zona DNS

El ejemplo siguiente crea una zona DNS denominada *contoso.com* en el grupo de recursos denominado *MyResourceGroup*. Use el ejemplo para crear una zona DNS, sustituir los valores de sus propios.

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-create-a-dns-zone-with-tags"></a>Para crear una zona DNS con etiquetas

En el ejemplo siguiente se muestra cómo crear una zona DNS con dos etiquetas *proyecto = demostración* y *Ent = prueba*. Use el ejemplo para crear una zona DNS, sustituir los valores de sus propios.

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## <a name="view-records"></a>Ver los registros

Crear una zona DNS, también crea los siguientes registros DNS:

- El registro de *Inicio de autoridad* (SOA). Existe en la raíz de cada zona DNS.

- Los registros de servidor (NS) de nombre relevantes. Estas muestran qué servidores de nombre alojan la zona. Azure DNS utiliza un grupo de servidores de nombres y tan diferentes servidores de nombres pueden asignarse a diferentes zonas en Azure DNS. Vea [delegar un dominio a Azure DNS](dns-domain-delegation.md) para obtener más información.

Para ver estos registros, use `Get-AzureRmDnsRecordSet`:

    Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
    RecordType        : SOA
    Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
    Tags              : {}

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
    Tags              : {}


Conjuntos de registros en la raíz (o *vértice*) de un uso de la zona DNS **@** como nombre del conjunto del registro.


## <a name="test"></a>Prueba

Puede probar la zona DNS mediante herramientas DNS, como nslookup, dig o el [cmdlet de PowerShell DnsName resolver](https://technet.microsoft.com/library/jj590781.aspx).

Si aún no ha delegado su dominio para usar la nueva zona de DNS de Azure, debe dirigir la consulta DNS directamente a uno de los servidores DNS de su zona. Los servidores DNS de su zona figuran en los registros NS, tal y como aparece por `Get-AzureRmDnsRecordSet` encima. Asegúrese de que la sustituir los valores correctos en la zona en el siguiente comando.

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = msnhst.microsoft.com
            serial  = 1
            refresh = 900 (15 mins)
            retry   = 300 (5 mins)
            expire  = 604800 (7 days)
            default TTL = 300 (5 mins)


## <a name="next-steps"></a>Pasos siguientes

Después de crear una zona DNS, crear [conjuntos de registros y los registros](dns-getstarted-create-recordset.md) para iniciar la resolución de nombres de su dominio de Internet.

