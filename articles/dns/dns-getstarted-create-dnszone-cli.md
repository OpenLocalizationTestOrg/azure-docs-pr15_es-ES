<properties
   pageTitle="Crear una zona DNS mediante CLI | Microsoft Azure"
   description="Aprenda a crear zonas DNS en DNS Azure paso a paso para iniciar su dominio DNS mediante CLI de hospedaje"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="create-an-azure-dns-zone-using-cli"></a>Crear una zona de DNS de Azure mediante CLI


> [AZURE.SELECTOR]
- [Portal de Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [CLI de Azure](dns-getstarted-create-dnszone-cli.md)


En este artículo le guiará por los pasos para crear una zona DNS mediante CLI. También puede crear una zona DNS con PowerShell o con el portal de Azure.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## <a name="before-you-begin"></a>Antes de empezar

Estas instrucciones use Microsoft Azure CLI. Asegúrese de actualizar a la última CLI Azure (0.9.8 o posterior) para usar los comandos de Azure DNS. Tipo de `azure -v` para comprobar qué versión de Azure CLI está instalado actualmente en el equipo.

## <a name="step-1---set-up-azure-cli"></a>Paso 1: configurar CLI de Azure

### <a name="1-install-azure-cli"></a>1. instalar CLI Azure

Puede instalar Azure CLI para Windows, Linux o Mac. Los pasos siguientes deben realizarse antes de poder administrar DNS de Azure mediante CLI de Azure. Más información está disponible en [instalar CLI Azure](../xplat-cli-install.md). Los comandos DNS requieren Azure CLI versión 0.9.8 o posterior.

Todos los comandos de proveedor de red de CLI pueden encontrarse mediante el siguiente comando:

    azure network

### <a name="2-switch-cli-mode"></a>2. modo CLI conmutador

Azure DNS utiliza el Administrador de recursos de Azure. Asegúrese de que cambiar el modo CLI para usar los comandos ARM.

    azure config mode arm

### <a name="3-sign-in-to-your-azure-account"></a>3. Inicie sesión en su cuenta de Azure

Se le pedirá para autenticar con sus credenciales. Tenga en cuenta que solo puede usar cuentas ORGID.

    azure login -u "username"

### <a name="4-select-the-subscription"></a>4. Seleccione la suscripción

Elija cuál de las suscripciones de Azure usar.

    azure account set "subscription name"

### <a name="5-create-a-resource-group"></a>5. crear un grupo de recursos

Administrador de recursos de Azure requiere que todos los grupos de recursos especifican una ubicación. Se usa como ubicación predeterminada para los recursos de ese grupo de recursos. Sin embargo, dado que todos los recursos DNS son global, no regional, la opción de ubicación de grupo de recursos tiene ningún efecto en Azure DNS.

Puede omitir este paso si está utilizando un grupo de recursos existente.

    azure group create -n myresourcegroup --location "West US"


### <a name="6-register"></a>6. registrar

El proveedor de recursos de Microsoft.Network administra el servicio DNS de Azure. Su suscripción de Azure debe registrarse para usar este proveedor de recursos para poder usar DNS de Azure. Se trata de una operación de una sola vez para cada suscripción.

    azure provider register --namespace Microsoft.Network


## <a name="step-2---create-a-dns-zone"></a>Paso 2: crear una zona DNS

Se crea una zona DNS con el `azure network dns zone create` comando. También puede crear una zona DNS junto con etiquetas. Las etiquetas son una lista de pares de valor de nombre y se utilizan por el Administrador de recursos de Azure a recursos de la etiqueta con fines de facturación o agrupamiento. Para obtener más información acerca de las etiquetas, vea [usar etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

En Azure DNS, nombres de zona deben especificarse sin una terminación **'.'**. Por ejemplo, como '**contoso.com**' en lugar de '**contoso.com.**'.


### <a name="to-create-a-dns-zone"></a>Para crear una zona DNS

El ejemplo siguiente crea una zona DNS denominada *contoso.com* en el grupo de recursos denominado *MyResourceGroup*.

Use el ejemplo para crear la zona DNS, sustituir los valores de sus propios.

    azure network dns zone create myresourcegroup contoso.com

### <a name="to-create-a-dns-zone-and-tags"></a>Para crear una zona DNS y etiquetas.

Azure CLI DNS es compatible con las etiquetas de las zonas DNS especificadas mediante la opcional *-etiqueta* parámetro. En el ejemplo siguiente se muestra cómo crear una zona DNS con dos etiquetas, project = demostración y Ent = prueba.

Use el ejemplo siguiente para crear una zona DNS y etiquetas, sustituir los valores de sus propios.

    azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## <a name="view-records"></a>Ver los registros

Crear una zona DNS, también crea los siguientes registros DNS:

- El registro de 'Inicio de la autoridad' (inicio de autoridad). Existe en la raíz de cada zona DNS.

- Los registros de servidor (NS) de nombre relevantes. Estas muestran qué servidores de nombre alojan la zona. Azure DNS utiliza un grupo de servidores de nombres y tan diferentes servidores de nombres pueden asignarse a diferentes zonas en Azure DNS. Para obtener más información, vea [delegado un dominio DNS de Azure](dns-domain-delegation.md) .

Para ver estos registros, use `azure network dns-record-set show`.<BR>
*Uso: mostrar de red dns conjunto de registros < grupo de recursos >< nombre de la zona dns > <name><type>*


En el ejemplo siguiente, si ejecuta el comando con el grupo de recursos *myresourcegroup*, registro establece nombre *"@"* (para un registro de raíz) y escriba el *Inicio de autoridad*, arrojará el resultado siguiente:


    azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/SOA
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    SOA record:
    data:      Email                         : msnhst.microsoft.com
    data:      Expire time                   : 604800
    data:      Host                          : edge1.azuredns-cloud.net
    data:      Minimum TTL                   : 300
    data:      Refresh time                  : 900
    data:      Retry time                    : 300
    data:                                    :
<BR>
Para ver los registros de NS creados con la zona, use el siguiente comando:

    azure network dns record-set show myresourcegroup "contoso.com" "@" NS
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    NS records
    data:        Name server domain name     : ns1-05.azure-dns.com
    data:        Name server domain name     : ns2-05.azure-dns.net
    data:        Name server domain name     : ns3-05.azure-dns.org
    data:        Name server domain name     : ns4-05.azure-dns.info
    data:
    info:    network dns-record-set show command OK

>[AZURE.NOTE] Conjuntos de registros en la raíz (o *vértice*) de un uso de la zona DNS **@** como nombre del conjunto del registro.

## <a name="test"></a>Prueba

Puede probar la zona DNS mediante herramientas DNS, como nslookup, SOL, o la `Resolve-DnsName` cmdlet de PowerShell.

Si aún no ha delegado su dominio para usar la nueva zona de DNS de Azure, debe dirigir la consulta DNS directamente a uno de los servidores DNS de su zona. Los servidores DNS de su zona figuran en los registros NS, tal y como aparece por "mostrar de conjunto de registros de dns de red azure" por encima. Asegúrese de que la sustituir los valores correctos en la zona en el siguiente comando.

En el ejemplo siguiente se utiliza DIG para consultar el dominio contoso.com con los servidores de nombres asignados a la zona DNS. La consulta tiene para que apunten a un servidor de nombres para que se utilizó * @ * y el nombre de zona con DIG.

     <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
    Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
    flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
    WARNING: recursion requested but not available

    OPT PSEUDOSECTION:
    EDNS: version: 0, flags:; udp: 4000
    QUESTION SECTION:
    contoso.com.                        IN      A

    AUTHORITY SECTION:
    contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
    msnhst.microsoft.com. 6 900 300 604800 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>Pasos siguientes

Después de crear una zona DNS, crear [conjuntos de registros y los registros](dns-getstarted-create-recordset-cli.md) para iniciar la resolución de nombres de su dominio de Internet.
