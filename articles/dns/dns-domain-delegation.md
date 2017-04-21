<properties
   pageTitle="Delegar el dominio DNS de Azure | Microsoft Azure"
   description="Comprender cómo cambiar la delegación de dominio y utilizar servidores de nombres DNS de Azure para proporcionar el hospedaje de dominio."
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2016"
   ms.author="sewhee"/>


# <a name="delegate-a-domain-to-azure-dns"></a>Delegar un dominio DNS de Azure

DNS Azure le permite hospedar una zona DNS y administrar los registros DNS para un dominio de Azure. En el orden de las consultas DNS para un dominio llegar a Azure DNS, el dominio tiene delegar Azure DNS del dominio principal. Tenga en cuenta Azure DNS no es el registrador de dominios. En este artículo se explica cómo funciona la delegación de dominio y cómo delegar dominios DNS de Azure.




## <a name="how-dns-delegation-works"></a>Cómo funciona la delegación DNS

### <a name="domains-and-zones"></a>Las zonas y dominios

El sistema de nombres de dominio es una jerarquía de dominios. Inicia la jerarquía del dominio 'raíz', cuyo nombre es simplemente '**.**'.  Debajo de esto vienen dominios de nivel superior, como 'com', 'net', 'org', 'uk' o 'jp'.  Bajo estos son los dominios de segundo nivel, como 'org.uk' o 'co.jp'.  Y así sucesivamente. Los dominios en la jerarquía DNS se hospedan usando independientes zonas DNS. Estas zonas se distribuyen globalmente alojado en servidores de nombres DNS todo el mundo.

**Zona DNS**

Un dominio es un nombre único en el sistema de nombres de dominio, por ejemplo contoso.com' '. Una zona DNS se utiliza para alojar los registros DNS para un dominio en particular. Por ejemplo, el dominio 'contoso.com' puede contener un número de registros DNS como 'mail.contoso.com' (para un servidor de correo) y 'www.contoso.com' (para un sitio Web).

**Registrador de dominios**

Un registrador de dominio es una compañía que puede proporcionar los nombres de dominio de Internet. Verificará si el dominio de Internet que desea usar está disponible y permite la compra. Una vez registrado el nombre de dominio, usted será el propietario legal para el nombre de dominio. Si ya tiene un dominio de Internet, usará al registrador de dominios actual al delegado a Azure DNS.

>[AZURE.NOTE] Para obtener más información acerca del propietario de un nombre de dominio determinado, o para obtener información sobre cómo comprar un dominio, consulte [administración de dominio de Internet de Azure AD](https://msdn.microsoft.com/library/azure/hh969248.aspx).

### <a name="resolution-and-delegation"></a>Resolución y la delegación

Existen dos tipos de servidores DNS:

- _Un servidor DNS_ aloja zonas DNS. Responde a las consultas DNS para los registros de esas zonas solo.
- Un servidor DNS _recursiva_ alojan zonas DNS. Responde a todas las consultas DNS al llamar a los servidores DNS autorizados para recopilar la información que necesita.

>[AZURE.NOTE] Azure DNS proporciona un servicio DNS relevantes.  No se proporciona un servicio DNS recursiva.

> Servicios de nube y máquinas virtuales en Azure se configuran automáticamente para usar un servicio DNS recursiva que se proporciona por separado como parte de la infraestructura de Azure.  Para obtener información sobre cómo cambiar esta configuración de DNS, vea [La resolución de nombres en Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

Los clientes DNS de equipos o dispositivos móviles suele llamar a un servidor DNS recursiva para realizar las consultas DNS que necesitan las aplicaciones cliente.

Cuando un servidor DNS recursiva recibe una consulta de un registro DNS como 'www.contoso.com', primero debe buscar el servidor de nombres que aloja la zona del dominio de 'contoso.com'. Para ello, se inicia en los servidores de nombres raíz y desde allí busca los servidores de nombres de la zona 'com'. A continuación, consulta los servidores de nombres 'com' para encontrar los servidores de nombres de la zona 'contoso.com'.  Por último, es posible consultar estos servidores de nombres para 'www.contoso.com'.

Esto se denomina resolución de nombre DNS. Estrictamente, resolución DNS incluye pasos adicionales como CNAME siguientes, pero que no es importante para comprender cómo funciona la delegación DNS.

¿Cómo una zona principal 'punto' a los servidores DNS para una zona secundaria? Esto ocurre con un tipo especial de registro DNS denominado registro NS (servidor DNS significa 'name server'). Por ejemplo, la zona raíz contiene los registros NS para 'com' y muestra los servidores DNS de la zona 'com'. En cambio, la zona 'com' contiene registros NS para 'contoso.com', que muestra los servidores DNS de la zona de 'contoso.com'. Configurar los registros NS para una zona secundaria en una zona principal se denomina delega el dominio.


![Servidor de nombres DNS](./media/dns-domain-delegation/image1.png)

Cada delegación tiene en realidad dos copias de los registros NS; uno en la zona principal que señalan secundario y otra en la misma zona secundarios. La zona de 'contoso.com' contiene los registros NS para 'contoso.com' (además de los registros NS en 'com'). Se denominan registros de NS relevantes y que empiecen a trabajar en el vértice de la zona secundaria.


## <a name="delegating-a-domain-to-azure-dns"></a>Delegación de un dominio DNS de Azure

Una vez que se crea la zona DNS en DNS de Azure, debe configurar los registros del servidor DNS en la zona principal para hacer el origen autorizado de resolución de nombres para la zona de DNS de Azure. Compradas un registrador de dominios, el registrador ofrecerá la opción para configurar estos registros NS.

>[AZURE.NOTE] No tiene el propietario de un dominio para poder crear una zona DNS con ese nombre de dominio en Azure DNS. Sin embargo, debe posee el dominio para configurar la delegación DNS Azure con el registrador.

Por ejemplo, suponga que compre el dominio 'contoso.com' y crea una zona con el nombre contoso.com en Azure DNS. Como propietario del dominio, el registrador le ofrecerá la opción para configurar las direcciones de servidor de nombres (es decir, los registros NS) para su dominio. El registrador almacenará estos registros DNS en el dominio principal, en este caso '.com'. Los clientes de todo el mundo, a continuación, se dirige a su dominio en la zona DNS de Azure cuando intenta resolver los registros DNS de 'contoso.com'.

### <a name="finding-the-name-server-names"></a>Buscar los nombres de servidor de nombre

Antes de que se puede delegar la zona DNS a DNS de Azure, primero debe saber los nombres de servidor de nombre de la zona. Azure DNS asigna a los servidores de nombres de un grupo cada vez que se crea una zona.

La manera más sencilla de ver los servidores de nombres asignados a la zona es a través del portal de Azure.  En este ejemplo, se ha asignado la zona 'contoso.net' servidores de nombres ' ns1-01.azure-dns.com', 'ns2-01.azure-dns .net', ' ns3-01.azure-dns.org', y ' ns4-01.azure-dns.info':

 ![Servidor de nombres DNS](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS crea automáticamente los registros NS relevantes en la zona que contiene los servidores de nombres asignados.  Para ver los nombres de servidor de nombre a través de Azure PowerShell o CLI de Azure, basta con recuperar estos registros.

Uso de PowerShell de Azure, los registros NS autorizados se pueden recuperar como sigue. Tenga en cuenta que el nombre del registro “@” se usa para hacer referencia a los registros en el vértice de la zona.

    PS> $zone = Get-AzureRmDnsZone –Name contoso.net –ResourceGroupName MyResourceGroup
    PS> Get-AzureRmDnsRecordSet –Name “@” –RecordType NS –Zone $zone

    Name              : @
    ZoneName          : contoso.net
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                        ns4-01.azure-dns.info}
    Tags              : {}

También puede utilizar la CLI de Azure entre plataformas para recuperar los registros NS relevantes y, por tanto, descubrir los servidores de nombres asignados a la zona:

    C:\> azure network dns record-set show MyResourceGroup contoso.net @ NS
    info:    Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
    data:    Id                              : /subscriptions/.../resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.net/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 172800
    data:    NS records
    data:        Name server domain name     : ns1-01.azure-dns.com.
    data:        Name server domain name     : ns2-01.azure-dns.net.
    data:        Name server domain name     : ns3-01.azure-dns.org.
    data:        Name server domain name     : ns4-01.azure-dns.info.
    data:
    info:    network dns record-set show command OK

### <a name="to-set-up-delegation"></a>Para configurar la delegación

Cada registrador tiene sus propias herramientas de administración de DNS para cambiar los registros de servidor de nombre de un dominio. En la página de administración de DNS del registrador, modifique los registros NS y reemplace los registros NS con las que Azure DNS creado.

Al delegar un dominio DNS de Azure, debe utilizar los nombres de servidor de nombre proporcionados por DNS de Azure.  Siempre debe usar todos los 4 nombre nombres de servidor, independientemente del nombre del dominio.  Delegación de dominio no requiere el nombre de servidor para usar el mismo dominio de primer nivel como su dominio.

No use 'pegue registros' para que apunten a los Azure nombres DNS server direcciones IP, puesto que estas direcciones IP pueden cambiar en futuras. Archivo con los nombres de servidor de nombre en su propia zona, a veces denominado 'servidores de nombres de cortesía', no se admite actualmente en Azure DNS.

### <a name="to-verify-name-resolution-is-working"></a>Para comprobar la resolución de nombres funciona

Después de completar la delegación, puede comprobar que la resolución de nombres funciona con una herramienta como 'nslookup' para consultar el registro de inicio de autoridad para su zona (que también crea automáticamente cuando se crea la zona).

Tenga en cuenta que no tiene que especificar los servidores de nombres DNS de Azure, ya que el proceso de resolución DNS normal encontrará los servidores de nombres automáticamente si la delegación se ha configurado correctamente.

    nslookup –type=SOA contoso.com

    Server: ns1-04.azure-dns.com
    Address: 208.76.47.4

    contoso.com
    primary name server = ns1-04.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)

## <a name="delegating-sub-domains-in-azure-dns"></a>Delegar subdominios en DNS de Azure

Si desea configurar una zona secundaria independiente, puede delegar un subdominio en Azure DNS. Por ejemplo, después de configurar y delegada 'contoso.com' en Azure DNS, suponga que desea configurar una zona secundario independiente, 'partners.contoso.com'.

Configurar un subdominio sigue un proceso similar como una delegación normal. La única diferencia es en el paso 3, que se deben crear los registros NS en la zona principal 'contoso.com' en Azure DNS, en lugar de que se establezca a través de un registrador de dominios.


1. Crear la zona secundaria 'partners.contoso.com' en Azure DNS.
2. Buscar los registros NS relevantes en la zona secundaria para obtener los servidores de nombres de la zona secundaria en Azure DNS.
3. Delegar la zona secundaria configurando los registros NS en la zona principal apuntando a la zona secundaria.


### <a name="to-delegate-a-sub-domain"></a>Delegar un subdominio

En el ejemplo de PowerShell siguiente se muestra cómo hacerlo. Los mismos pasos se pueden ejecutar a través del Portal de Azure, o mediante la CLI de Azure entre plataformas.

#### <a name="step-1-create-the-parent-and-child-zones"></a>Paso 1. Crear el elemento principal y secundario zonas

En primer lugar, creamos el principal y secundario zonas. Pueden estar en el mismo grupo de recursos o grupos de recursos diferentes.

    $parent = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName RG1
    $child = New-AzureRmDnsZone -Name partners.contoso.com -ResourceGroupName RG1

#### <a name="step-2-retrieve-ns-records"></a>Paso 2. Registros NS de recuperar

A continuación, se recuperan los registros NS relevantes de zona de secundaria tal como se muestra en el ejemplo siguiente.  Esta página contiene los servidores de nombres asignados a la zona secundaria.

    $child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

#### <a name="step-3-delegate-the-child-zone"></a>Paso 3. Delegar la zona secundaria

Crear conjunto en la zona principal para completar la delegación de registros de NS correspondiente. Tenga en cuenta que el nombre del conjunto de registros en la zona principal coincide con el nombre de la zona secundaria, en este caso "socios".

    $parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
    $parent_ns_recordset.Records = $child_ns_recordset.Records
    Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset

### <a name="to-verify-name-resolution-is-working"></a>Para comprobar la resolución de nombres funciona

Puede comprobar que todo está configurado correctamente consultando el registro de inicio de autoridad de la zona secundaria.

    nslookup –type=SOA partners.contoso.com

    Server: ns1-08.azure-dns.com
    Address: 208.76.47.8

    partners.contoso.com
        primary name server = ns1-08.azure-dns.com
        responsible mail addr = msnhst.microsoft.com
        serial = 1
        refresh = 900 (15 mins)
        retry = 300 (5 mins)
        expire = 604800 (7 days)
        default TTL = 300 (5 mins)

## <a name="next-steps"></a>Pasos siguientes

[Administrar zonas DNS](dns-operations-dnszones.md)

[Administrar registros DNS](dns-operations-recordsets.md)

