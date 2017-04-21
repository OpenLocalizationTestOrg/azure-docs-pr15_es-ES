<properties
   pageTitle="Administrar conjuntos de registros de DNS y los registros de DNS de Azure mediante CLI de Azure | Microsoft Azure"
   description="Administrar conjuntos de registros de DNS y los registros de DNS de Azure cuando su dominio en Azure DNS de host. Todos los comandos CLI para operaciones de conjuntos de registros y registros."
   services="dns"
   documentationCenter="na"
   authors="jtuliani"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="jtuliani"/>

# <a name="manage-dns-records-and-record-sets-by-using-cli"></a>Administrar los registros DNS y los conjuntos de registros mediante CLI


> [AZURE.SELECTOR]
- [Portal de Azure](dns-operations-recordsets-portal.md)
- [CLI de Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Este artículo le muestra cómo administrar los registros de la zona DNS y conjuntos de registros mediante la interfaz de línea de comandos de Azure entre plataformas (CLI).

Es importante comprender la diferencia entre los conjuntos de registros de DNS y los registros DNS individuales. Un conjunto de registros es una colección de registros de una zona que tienen el mismo nombre y el mismo tipo. Para obtener más información, vea [registros y conjuntos de registros de descripción](dns-getstarted-create-recordset-cli.md).


## <a name="configure-the-cross-platform-azure-cli"></a>Configurar la CLI de Azure entre plataformas

Azure DNS es un servicio Azure solo el Administrador de recursos. No tiene un API de administración de servicio de Azure. Asegúrese de que la CLI Azure está configurada para usar el modo de administrador de recursos, usando la `azure config mode arm` comando.

Si ve **error: 'dns' no es un comando de azure**, es muy probable porque utiliza CLI de Azure en modo de administración de servicios de Azure, no en modo de administrador de recursos.

## <a name="create-a-new-record-set-and-record"></a>Crear un nuevo conjunto de registros y registro

Para crear un nuevo registro establecido en el portal de Azure, vea [crear un conjunto de registros y registros](dns-getstarted-create-recordset-cli.md).


## <a name="retrieve-a-record-set"></a>Recuperar un conjunto de registros

Para recuperar un conjunto de registros existente, utilice `azure network dns record-set show`. Especificar el grupo de recursos, el nombre de la zona, establezca registro relativa nombre y tipo de registro. Use el ejemplo siguiente, reemplazando los valores por la suya propia.

    azure network dns record-set show myresourcegroup contoso.com www A


## <a name="list-record-sets"></a>Conjuntos de registros de la lista

Lista de todos los registros en una zona DNS utilizando la `azure network dns record-set list` comando. Debe especificar el nombre del grupo de recursos y el nombre de la zona.

### <a name="to-list-all-record-sets"></a>Para obtener una lista de todos los conjuntos de registros

Este ejemplo devuelve todos los conjuntos de registros, independientemente del nombre o el tipo de registro:

    azure network dns record-set list myresourcegroup contoso.com

### <a name="to-list-record-sets-of-a-given-type"></a>Para los conjuntos de registros de lista de un tipo determinado

Este ejemplo devuelve todos los conjuntos de registros que coincidan con el tipo de registro determinado (en este caso, "A" registros):

    azure network dns record-set list myresourcegroup contoso.com A


## <a name="add-a-record-to-a-record-set"></a>Agregar un registro a un conjunto de registros

Agregar registros a conjuntos de registros mediante la `azure network dns record-set add-record`comando. Los parámetros para agregar registros a un conjunto de registros varían según el tipo de registro que se va a establecer. Por ejemplo, cuando se utiliza un conjunto de registros de tipo "A", sólo puede especificar los registros con el parámetro `-a <IPv4 address>`.

Para crear un conjunto de registros, use la `azure network dns record-set create`comando. Especificar el grupo de recursos, el nombre de la zona, establezca registro nombre relativo, tipo de registro y tiempo de vida (TTL). Si el `--ttl` parámetro no está definido, el valor (en segundos) predeterminado es cuatro.

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300


Después de crear el conjunto de registros "A", agregue la dirección IPv4 utilizando la `azure network dns record-set add-record`comando.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1


Los siguientes ejemplos muestran cómo crear un conjunto de registro de cada tipo de registro. Cada conjunto de registros contiene un único registro.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]


## <a name="update-a-record-in-a-record-set"></a>Actualizar un registro en un conjunto de registros

### <a name="to-add-another-ip-address-1234-to-an-existing-a-record-set-www"></a>Para agregar otra dirección IP (1.2.3.4) a un registro "A" establece ("www"):

    azure network dns record-set add-record  myresourcegroup contoso.com  A
    -a 1.2.3.4
    info:    Executing command network dns record-set add-record
    Record set name: www
    + Looking up the dns zone "contoso.com"
    + Looking up the DNS record set "www"
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/a
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:        IPv4 address                : 192.168.1.1
    data:        IPv4 address                : 1.2.3.4
    data:
    info:    network dns record-set add-record command OK

### <a name="to-remove-an-existing-value-from-a-record-set"></a>Para quitar un valor existente de un conjunto de registros
Usar `azure network dns record-set delete-record`.

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
    info:    Executing command network dns record-set delete-record
    + Looking up the DNS record set "www"
    Delete DNS record? [y/n] y
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/A
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:    IPv4 address                    : 192.168.1.1
    data:
    info:    network dns record-set delete-record command OK



## <a name="remove-a-record-from-a-record-set"></a>Quitar un registro de un conjunto de registros

Se pueden quitar los registros de un registro de establecer mediante `azure network dns record-set delete-record`. El registro que se va a quitar debe ser una coincidencia exacta con un registro existente en todos los parámetros.

Quitar el último registro de un conjunto de registros, no elimina el conjunto de registros. Para obtener más información, vea la sección de este artículo denominado [Eliminar un conjunto de registros](#delete).

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

    azure network dns record-set delete myresourcegroup contoso.com www A

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Quitar un registro AAAA de un conjunto de registros

    azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### <a name="remove-a-cname-record-from-a-record-set"></a>Quitar un registro CNAME de un conjunto de registros

    azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com


### <a name="remove-an-mx-record-from-a-record-set"></a>Quitar un registro MX de un conjunto de registros

    azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### <a name="remove-an-ns-record-from-record-set"></a>Quitar un registro NS de conjunto de registros.

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### <a name="remove-a-ptr-record-from-a-record-set"></a>Quitar un registro PTR de un conjunto de registros
En este caso ' Mi-arpa-zone.com' representa la zona ARPA que representa el intervalo de IP.  Cada registro PTR establecer en esta zona corresponde a una dirección IP dentro de este intervalo IP.

    azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"

### <a name="remove-an-srv-record-from-a-record-set"></a>Quitar un registro SRV de un conjunto de registros

    azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"

### <a name="remove-a-txt-record-from-a-record-set"></a>Quitar un registro TXT de un conjunto de registros

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## <a name="delete"></a>Eliminar un conjunto de registros

Conjuntos de registros pueden eliminarse mediante la `Remove-AzureRmDnsRecordSet` cmdlet. No se puede eliminar el inicio de autoridad y registro NS se establece en el vértice de zona (nombre = "@") que se crearon automáticamente cuando se creó la zona. Se eliminarán automáticamente si se elimina la zona.

En el ejemplo siguiente, el registro "A" establecer "prueba a" ya figurará en la zona DNS "contoso.com":

    azure network dns record-set delete myresourcegroup contoso.com  "test-a" A

El modificador opcional *- q* puede usarse para suprimir el mensaje de confirmación.


## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de DNS de Azure, consulte [Introducción a Azure DNS](dns-overview.md). Para obtener información sobre la automatización de DNS, vea [zonas DNS de creación y registro establece mediante .NET SDK](dns-sdk.md).

Si desea trabajar con los registros DNS inversos, consulte [cómo administrar los registros DNS inversos de los servicios con la CLI de Azure](dns-reverse-dns-record-operations-cli.md).
