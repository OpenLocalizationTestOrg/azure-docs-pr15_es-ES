## <a name="about-records"></a>Acerca de registros

Cada registro DNS tiene un nombre y un tipo. Los registros se organizan en distintos tipos de acuerdo con los datos que contienen. El tipo más habitual es un registro "A", que se asigna un nombre a una dirección de IPv4. Otro tipo es un registro "MX", que asigna un nombre a un servidor de correo.

Azure DNS es compatible con todos los comunes tipos de registro DNS, incluyendo A, AAAA, CNAME, MX, NS, PTR, inicio de autoridad, SRV y TXT. Tenga en cuenta que:
- Conjuntos de registros de inicio de autoridad se crean automáticamente con cada zona, no se puede crear por separado.
- Registros de SPF deben crearse utilizando el tipo de registro TXT. Para obtener más información, consulte [esta página](http://tools.ietf.org/html/rfc7208#section-3.1).

En DNS de Azure, se especifican registros usando nombres relativos. Un nombre de "nombre" de dominio completo (FQDN) incluye el nombre de la zona, mientras que un nombre "relativo" no. Por ejemplo, el nombre del registro relativo "www" en la zona "contoso.com" le da el nombre completo de registros www.contoso.com.

## <a name="about-record-sets"></a>Acerca de los conjuntos de registros

A veces debe crear más de un registro DNS con un nombre y tipo dados. Por ejemplo, suponga que el sitio web de "www.contoso.com" se hospeda en dos direcciones IP diferentes. El sitio Web requiere dos diferentes registros, uno para cada dirección IP. Este es un ejemplo de un conjunto de registros:

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Azure DNS administra los registros DNS utilizando conjuntos de registros. Un conjunto de registros es el conjunto de registros DNS en una zona que tienen el mismo nombre y el mismo tipo. La mayoría de conjuntos de registros contienen un único registro, pero ejemplos como esta, en la que un conjunto de registros contiene más de un registro, no son poco comunes.

Conjuntos de registros CNAME y de inicio de autoridad son excepciones. Los estándares DNS no permiten varios registros con el mismo nombre de esos tipos.

El tiempo de vida o TTL, especifica cuánto cada registro se almacena en caché los clientes antes de que se vuelva a consulta. En este ejemplo, el TTL es 3.600 segundos o 1 hora. El valor TTL especificado para el conjunto de registros, no para cada registro, por lo que se utiliza el mismo valor para todos los registros de ese conjunto de registros.

#### <a name="wildcard-record-sets"></a>Conjuntos de registros de caracteres comodín

Azure DNS es compatible con [los registros de caracteres comodín](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Estos devueltos por cualquier consulta con un nombre coincidente (a menos que haya una coincidencia más cerca de un conjunto de registros no comodín). Conjuntos de registros de comodín son compatibles para todos los tipos de registro excepto NS y SOA.  

Para crear un conjunto de registros de caracteres comodín, utilice el nombre del conjunto de registros "\*". O bien, use un nombre con la etiqueta "\*", por ejemplo,"\*.foo".

#### <a name="cname-record-sets"></a>Conjuntos de registros CNAME

Conjuntos de registros CNAME no coexisten con otros conjuntos de registros con el mismo nombre. Por ejemplo, no puede crear un registro CNAME conjunto con nombre relativo "www" y un registro con el nombre relativo "www" al mismo tiempo. Dado que el vértice de zona (nombre = ‘@’) siempre contiene el inicio de autoridad y NS registran que se crearon cuando se creó la zona, no puede crear un registro CNAME establecer en el vértice de zona. Estas restricciones surgen de los estándares de DNS y no son las limitaciones de DNS de Azure.
