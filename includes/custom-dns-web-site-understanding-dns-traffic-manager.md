El sistema de nombres de dominio (DNS) se usa para buscar cosas en internet. Por ejemplo, cuando escribe una dirección en el explorador, o haga clic en un vínculo en una página web, utiliza DNS para traducir el dominio a una dirección IP. La dirección IP es algo parecido a una dirección, pero no es muy humana descriptivo. Por ejemplo, es mucho más fácil de recordar un nombre DNS como **contoso.com** ampliamente recordar una dirección IP como 192.168.1.88 o 2001:0:4137:1f67:24a2:3888:9cce:fea3.

El sistema DNS se basa en *registros*. Registros asociar un determinado *nombre*, como **contoso.com**, con una dirección IP u otro nombre DNS. Cuando una aplicación, como un explorador web, busque un nombre en DNS, encuentre el registro y usa cualquier cosa apunta a la dirección. Si el valor que señala es una dirección IP, el explorador usará ese valor. Apunta a otro nombre DNS, la aplicación debe realizar la resolución de nuevo. Por último, la resolución de nombres terminará en una dirección IP.

Cuando se crea un sitio Web de Azure, un nombre DNS se asigna automáticamente al sitio. Este nombre tiene el formato de ** &lt;yoursitename&gt;. azurewebsites.net**. Cuando agrega el sitio Web como un extremo de administrador de tráfico de Azure, su sitio Web es accesible a través de la ** &lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** dominio.

> [AZURE.NOTE] Cuando su sitio Web está configurado como un extremo de administrador de tráfico, utilice la **. trafficmanager.net** al crear registros DNS de direcciones.

> Solo puede usar registros CNAME con el Administrador de tráfico

También hay varios tipos de registros, cada una con sus propias funciones y limitaciones, pero para sitios Web está configurado para como administrador de tráfico extremos, nos solo importa aproximadamente uno; Registros *CNAME* .

###<a name="cname-or-alias-record"></a>Registro CNAME o Alias

Un registro CNAME asigna un nombre DNS *específico* , como **mail.contoso.com** o **www.contoso.com**, a otro nombre de dominio (canónico). En el caso de los sitios Web de Azure con el Administrador de tráfico, el nombre de dominio canónica es la ** &lt;myapp >. trafficmanager.net** nombre de dominio de su perfil de administrador de tráfico. Una vez creado, el registro CNAME crea un alias para la ** &lt;myapp >. trafficmanager.net** nombre de dominio. La entrada CNAME resolverá con la dirección IP de su ** &lt;myapp >. trafficmanager.net** nombre de dominio automáticamente, por lo que si cambia la dirección IP del sitio Web, no tiene que realizar ninguna acción.

Una vez que el tráfico llega en el Administrador de tráfico, a continuación, enruta el tráfico al sitio Web, con el está configurada para el método de equilibrio de carga. Esto es completamente transparente a los visitantes a su sitio Web. Solo verán el nombre de dominio personalizado en el explorador.

> [AZURE.NOTE] Solo algunos registradores de dominios permiten asignar subdominios cuando se usa un registro CNAME, por ejemplo, **www.contoso.com**y no los nombres de raíz, por ejemplo, **contoso.com**. Para obtener más información sobre registros CNAME, vea la documentación proporcionada por el registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">la entrada de Wikipedia en el registro CNAME</a>o el documento de <a href="http://tools.ietf.org/html/rfc1035">IETF los nombres de dominio - implementación y especificación</a> .
