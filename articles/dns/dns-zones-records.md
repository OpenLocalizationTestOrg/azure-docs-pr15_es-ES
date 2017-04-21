<properties 
   pageTitle="Zonas y registros DNS | Microsoft Azure" 
   description="Descripción general de soporte técnico para alojar zonas DNS y registros de DNS de Microsoft Azure." 
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
   ms.date="10/17/2016"
   ms.author="jtuliani"/>

# <a name="dns-zones-and-records"></a>Registros y zonas DNS

Esta página explica los conceptos clave de dominios, zonas DNS y los registros DNS y conjuntos de registros y cómo se admiten en Azure DNS.

## <a name="domain-names"></a>Nombres de dominio
 
El sistema de nombres de dominio es una jerarquía de dominios. Inicia la jerarquía del dominio 'raíz', cuyo nombre es simplemente '**.**'.  Debajo de esto vienen dominios de nivel superior, como 'com', 'net', 'org', 'uk' o 'jp'.  Bajo estos son los dominios de segundo nivel, como 'org.uk' o 'co.jp'. Los dominios en la jerarquía DNS se distribuyen globalmente alojado en servidores de nombres DNS todo el mundo.

Un registrador de nombres de dominio es una organización que le permite adquirir un nombre de dominio, como 'contoso.com'.  Comprar un nombre de dominio le da derecho a controlar la jerarquía DNS con este nombre, por ejemplo permitiéndole dirigir el nombre 'www.contoso.com' para el sitio web de su empresa. El registrador puede hospedar el dominio en sus propia servidores DNS en su nombre, o también puede especificar los servidores DNS alternativos.

DNS Azure proporciona una infraestructura de servidor de nombre de distribución global, alta disponibilidad, que puede utilizar para hospedar el dominio. Al alojar sus dominios en Azure DNS, puede administrar los registros DNS con las mismas credenciales, API, herramientas, facturación y soporte técnico como los otros servicios de Azure.

Azure DNS no admite actualmente compras de nombres de dominio. Si desea comprar un nombre de dominio, debe usar a un registrador de nombres de dominio de terceros. El registrador normalmente le cobrará una pequeña cuota anual. Los dominios, a continuación, estar hospedados en Azure DNS para la administración de registros DNS. Para obtener más información, vea [delegado un dominio DNS de Azure](dns-domain-delegation.md) .

## <a name="dns-zones"></a>Zonas DNS 

Una zona DNS se utiliza para alojar los registros DNS para un dominio en particular. Para iniciar el host de su dominio en Azure DNS, debe crear una zona DNS para ese nombre de dominio. Cada registro DNS de su dominio, se crea dentro de esta zona DNS. 

Por ejemplo, el dominio 'contoso.com' puede contener varios registros DNS, como 'mail.contoso.com' (para un servidor de correo) y 'www.contoso.com' (para un sitio web).

Al crear una zona DNS en DNS de Azure, el nombre de la zona debe ser único dentro del grupo de recursos. Puede reutilizar el mismo nombre de la zona en otro grupo de recursos o una suscripción de Azure diferente. Cuando hay varias zonas que comparten el mismo nombre, se asigna a cada instancia diferentes direcciones del servidor. Puede configurar sólo un conjunto de direcciones con el registrador de nombres de dominio.

>[AZURE.NOTE] No tiene el propietario de un nombre de dominio para crear una zona DNS con el nombre del dominio en Azure DNS. Sin embargo, debe posee el dominio para configurar los servidores de nombres DNS de Azure como los servidores de nombres correctos para el nombre de dominio con el registrador de nombres de dominio.

Para obtener más información, vea [delegado un dominio DNS de Azure](dns-domain-delegation.md).

## <a name="dns-records"></a>Registros DNS

### <a name="record-types"></a>Tipos de registro

Cada registro DNS tiene un nombre y un tipo. Los registros se organizan en distintos tipos de acuerdo con los datos que contienen. El tipo más habitual es un registro "A", que se asigna un nombre a una dirección de IPv4. Otro tipo comunes es un registro "MX", que asigna un nombre a un servidor de correo.

Azure DNS es compatible con todos los tipos de registros de DNS comunes: A, AAAA, CNAME, MX, NS, PTR, inicio de autoridad, SRV y TXT.

### <a name="record-names"></a>Nombres de registro

En DNS de Azure, se especifican registros usando nombres relativos. Un nombre de dominio *completo* (FQDN) incluye el nombre de la zona, mientras que un nombre *relativo* no. Por ejemplo, el nombre del registro relativo "www" en la zona 'contoso.com' asigna el nombre de registro completo 'www.contoso.com'.

Un registro de *vértice* es un registro DNS en la raíz (o *vértice*) de una zona DNS. Por ejemplo, en la zona DNS 'contoso.com', un registro de vértice también tiene el nombre completo 'contoso.com' (a veces se denomina un dominio *vacío* ).  Por convención, el nombre relativo '@' se usa para crear registros de vértice.

### <a name="record-sets"></a>Conjuntos de registros
A veces debe crear más de un registro DNS con un nombre y tipo dados. Por ejemplo, suponga que el sitio web 'www.contoso.com' se hospeda en dos direcciones IP diferentes. El sitio Web requiere dos diferentes registros, uno para cada dirección IP. Este es un ejemplo de un conjunto de registros:

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Azure DNS administra los registros DNS con *conjuntos de registros*. Un conjunto de registro (también conocido como un *recurso de* conjunto de registros) es el conjunto de registros DNS en una zona que tienen el mismo nombre y del mismo tipo. La mayoría de conjuntos de registros contienen un único registro, pero ejemplos como esta, en la que un conjunto de registros contiene más de un registro, no son poco comunes.

Por ejemplo, supongamos que ya ha creado un registro "www" en la zona 'contoso.com', apuntando a la dirección IP dirección '134.170.185.46' (el primer registro anterior).  Para crear el segundo registro se debería agregar dicho registro para el conjunto de registros existente, en lugar de crear un nuevo conjunto de registros.

Los tipos de registro CNAME y de inicio de autoridad son excepciones. Los estándares DNS no permiten varios registros con el mismo nombre de esos tipos, por lo tanto estos conjuntos de registros sólo pueden contener un único registro.

### <a name="time-to-live"></a>Time to live

El tiempo de vida o TTL, especifica cuánto cada registro se almacena en caché los clientes antes de que se vuelva a consulta. En el ejemplo anterior, el TTL es 3.600 segundos o 1 hora.

En Azure DNS, el valor TTL especificado para el conjunto de registros, no para cada registro, para que el mismo valor que se usa para todos los registros de ese conjunto de registros.  Puede especificar cualquier valor TTL entre 1 y 2.147.483.647 segundos.

### <a name="wildcard-records"></a>Registros de caracteres comodín

Azure DNS es compatible con [los registros de caracteres comodín](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Registros de comodín se devuelven en respuesta a las consultas con un nombre coincidente (a menos que haya una coincidencia más cerca de un conjunto de registros no comodín). Conjuntos de registros de comodín son compatibles para todos los tipos de registro excepto NS y SOA.  

Para crear un conjunto de registros de caracteres comodín, utilice el nombre del conjunto de registros '\*'. Como alternativa, también puede usar un nombre con '\*'como su extremo izquierdo etiqueta, por ejemplo,'\*.foo'.

### <a name="cname-records"></a>Registros CNAME

Conjuntos de registros CNAME no coexisten con otros conjuntos de registros con el mismo nombre. Por ejemplo, no puede crear un registro CNAME conjunto con nombre relativo "www" y un registro con el nombre relativo "www" al mismo tiempo.

Dado que el vértice de zona (nombre = ‘@’) siempre contiene el inicio de autoridad y NS registran que se crearon cuando se creó la zona, no puede crear un registro CNAME establecer en el vértice de zona.

Estas restricciones surgen de los estándares de DNS y no son las limitaciones de DNS de Azure.

### <a name="ns-records"></a>Registros NS

Un conjunto de registros de NS se crea automáticamente en el vértice de cada zona (nombre = ‘@’), y se eliminan automáticamente cuando se elimina la zona (no se puede eliminar por separado).  Puede modificar el período de vida de este conjunto de registros, pero no puede modificar los registros, que están preconfigurados para hacer referencia a los servidores de nombres DNS de Azure asignados a la zona.

Puede crear y eliminar otros registros de NS dentro de la zona, no en el vértice de zona.  Esto le permite configurar zonas secundarias (consulte [delegar subdominios en Azure DNS](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns)).

### <a name="soa-records"></a>Registros de inicio de autoridad

Un conjunto de registros de inicio de autoridad se crea automáticamente en el vértice de cada zona (nombre = ‘@’), y se eliminan automáticamente cuando se elimina la zona.  Registros de inicio de autoridad no pueden crearse o eliminarse por separado. 

Puede modificar todas las propiedades del registro de inicio de autoridad excepto la propiedad 'host', preconfigurado para hacer referencia al nombre del servidor de nombres primario proporcionado por DNS de Azure.

### <a name="spf-records"></a>Registros de SPF

Registros de marco de directivas (SPF) del remitente se usan para especificar qué servidores de correo electrónico tienen permiso para enviar correo electrónico en nombre de un nombre de dominio.  Configuración correcta de los registros de SPF es importante para evitar que a los destinatarios marcar el correo electrónico como 'correo no deseado'.

Las solicitudes de cambio de DNS originalmente introdujo un nuevo tipo de registro 'SPF' para admitir este escenario. Para admitir servidores de nombres anteriores, también permite el uso del tipo de registro TXT para especificar los registros de SPF.  Esta ambigüedad dio lugar a confusión, que se ha resuelto por [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1).  Esto indica que los registros de SPF solo deben crearse utilizando el tipo de registro TXT y obsoleto el tipo de registro de SPF. 

**Registros de SPF son compatibles con DNS de Azure y deben crearse utilizando el tipo de registro TXT.** No se admite el tipo de registro de SPF obsoleto. Cuando la [importación de un DNS de archivo de zona](dns-import-export.md), los registros de SPF utilizando el tipo de registro de SPF se convierten en el tipo de registro TXT. 

### <a name="srv-records"></a>Registros SRV

[Registros SRV](https://en.wikipedia.org/wiki/SRV_record) se utilizan por diversos servicios para especificar las ubicaciones de servidor. Al especificar un registro SRV de DNS de Azure:

- El *servicio* y *protocolo* deben especificarse como parte del nombre del conjunto de registros, el prefijo con caracteres de subrayado.  Por ejemplo, '\_sip. \_tcp.name'.  Para un registro en el vértice de zona, no es necesario especificar '@' en el nombre del registro, simplemente use el servicio y protocolo, por ejemplo, '\_sip. \_tcp'.
- La *prioridad*, el *grosor*, el *puerto*y el *destino* se especifican como parámetros de cada registro en el conjunto de registros. 

## <a name="tags-and-metadata"></a>Etiquetas y metadatos

### <a name="tags"></a>Etiquetas
Las etiquetas son una lista de pares de valor de nombre y se usan por el Administrador de recursos de Azure a recursos de la etiqueta.  Administrador de recursos de Azure utiliza etiquetas para habilitar vistas filtradas de la factura de Azure y también le permite definir una directiva en la que se requieren etiquetas. Para obtener más información acerca de las etiquetas, vea [usar etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

Azure DNS admite el uso de etiquetas de administrador de recursos de Azure en recursos de zona DNS.  No se admite etiquetas en conjuntos de registros de DNS.

### <a name="metadata"></a>Metadatos

Como alternativa a etiquetas de conjunto de registros DNS de Azure admite anotar conjuntos de registros mediante 'metadatos'.  De forma similar a las etiquetas, metadatos le permite asociar pares de valor de nombre a cada conjunto de registros.  Esto puede ser útil, por ejemplo, para el propósito de cada conjunto de registros de registro.  A diferencia de las etiquetas, metadatos no se pueden usar para proporcionar una vista filtrada de la factura de Azure y no se pueden especificar en una directiva de administrador de recursos de Azure.

## <a name="limits"></a>Límites

Los límites predeterminados siguientes se aplican cuando con Azure DNS:

[AZURE.INCLUDE [dns-limits](../../includes/dns-limits.md)] 

## <a name="next-steps"></a>Pasos siguientes

- Para empezar a usar Azure DNS, obtenga información sobre cómo [crear una zona DNS](dns-getstarted-create-dnszone-portal.md) y [crear registros DNS](dns-getstarted-create-recordset-portal.md).
- Para migrar una zona DNS, obtenga información sobre cómo [Importar y archivo de zona DNS](dns-import-export.md).
