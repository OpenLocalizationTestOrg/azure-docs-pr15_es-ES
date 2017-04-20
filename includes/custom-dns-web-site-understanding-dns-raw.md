El sistema de nombres de dominio (DNS) se usa para buscar recursos en internet. Por ejemplo, cuando escriba una dirección de la aplicación web en el explorador, o haga clic en un vínculo en una página web, utiliza DNS para traducir el dominio a una dirección IP. La dirección IP es algo parecido a una dirección, pero no es muy humana descriptivo. Por ejemplo, es mucho más fácil de recordar un nombre DNS como **contoso.com** ampliamente recordar una dirección IP como 192.168.1.88 o 2001:0:4137:1f67:24a2:3888:9cce:fea3.

El sistema DNS se basa en *registros*. Registros asociar un determinado *nombre*, como **contoso.com**, con una dirección IP u otro nombre DNS. Cuando una aplicación, como un explorador web, busque un nombre en DNS, encuentre el registro y usa cualquier cosa apunta a la dirección. Si el valor que señala es una dirección IP, el explorador usará ese valor. Apunta a otro nombre DNS, la aplicación debe realizar la resolución de nuevo. Por último, finalizará la resolución de nombres en una dirección IP.

Cuando se crea una aplicación web en la aplicación de servicio, un nombre DNS se asigna automáticamente a la aplicación web. Este nombre tiene el formato de ** &lt;yourwebappname&gt;. azurewebsites.net**. También hay una dirección IP virtual disponible para su uso cuando los registros de DNS de creación, por lo que puede crear registros que apuntan a la **. azurewebsites.net**, o puede apuntar a la dirección IP.

> [AZURE.NOTE] Si eliminar y volver a crear la aplicación web o cambiar el modo de plan de servicio de aplicaciones en **libre** después de que se estableció en **básica**, **compartida**o **estándar**, cambiará la dirección IP de su aplicación web.

También existen varios tipos de registros, cada una con sus propias funciones y limitaciones, pero para las aplicaciones web sólo nos interesan dos registros *A* y *CNAME* .

###<a name="address-record-a-record"></a>Registro de dirección (registro a)

Un registro a asigna un dominio, por ejemplo, **contoso.com** o **www.contoso.com**, *o un dominio comodín* como ** \*. contoso.com**, a una dirección IP. En el caso de una aplicación web en la aplicación de servicio, la dirección IP virtual del servicio o una dirección IP específica de direcciones que se adquirieron para la aplicación web.

Son las ventajas principales de un registro a través de un registro CNAME:

* Puede asignar un dominio raíz como **contoso.com** a una dirección IP. muchos registradores de permiten solo este utilizando un registros

* Puede tener una entrada que utiliza un carácter comodín, como ** \*. contoso.com**, que desea controlar las solicitudes de varios subdominios como **mail.contoso.com**, **blogs.contoso.com**o **www.contso.com**.

> [AZURE.NOTE] Dado que un registro está asignado a una dirección IP estática, no puede resolver cambios automáticamente a la dirección IP de su aplicación web. Una dirección IP para su uso con los registros que se proporciona al configurar las opciones de nombre de dominio personalizado para la aplicación web; Sin embargo, puede cambiar este valor si eliminar y volver a crear la aplicación web o cambiar el modo de plan de servicio de la aplicación para volver a **libre**.

###<a name="alias-record-cname-record"></a>Registro de alias (CNAME registro)

Un registro CNAME asigna un nombre DNS *específico* , como **mail.contoso.com** o **www.contoso.com**, a otro nombre de dominio (canónico). En el caso de aplicación de servicio Web Apps, el nombre de dominio canónica es la ** &lt;yourwebappname >. azurewebsites.net** nombre de dominio de la aplicación web. Una vez creado, el registro CNAME crea un alias para la ** &lt;yourwebappname >. azurewebsites.net** nombre de dominio. La entrada CNAME resolverá con la dirección IP de su ** &lt;yourwebappname >. azurewebsites.net** nombre de dominio automáticamente, por lo que si cambia la dirección IP de la aplicación web, no tiene que realizar ninguna acción.

> [AZURE.NOTE] Solo algunos registradores de dominios permiten asignar subdominios cuando se usa un registro CNAME, por ejemplo, **www.contoso.com**y no los nombres de raíz, por ejemplo, **contoso.com**. Para obtener más información sobre registros CNAME, vea la documentación proporcionada por el registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">la entrada de Wikipedia en el registro CNAME</a>o el documento de <a href="http://tools.ietf.org/html/rfc1035">IETF los nombres de dominio - implementación y especificación</a> .

###<a name="web-app-dns-specifics"></a>Características específicas DNS de aplicación Web

Usa un registro con aplicaciones Web, debe crear primero uno de los siguientes registros TXT:

* **Para el dominio raíz** - TXT DNS de un registro de **@** a ** &lt;yourwebappname&gt;. azurewebsites.net**.

* **Para un subdominio específico** - A nombre DNS de ** &lt;subdominio >** a ** &lt;yourwebappname&gt;. azurewebsites.net**. Por ejemplo, **blogs** si el registro es **blogs.contoso.com**.

* **Para los caracteres comodín sub-dodmains** - TXT DNS de un registro de *** a ** &lt;yourwebappname&gt;. azurewebsites.net**.

Este registro TXT se usa para verificar que posee el dominio que está intentando usar. Esto es además de crear un registro que apunta a la dirección IP virtual de la aplicación web.

Puede encontrar la dirección IP y **. azurewebsites.net** nombres para la aplicación web mediante los pasos siguientes:

1. En el explorador, abra el [Portal de Azure](https://portal.azure.com).

2. En el módulo de **Aplicaciones Web** , haga clic en el nombre de la aplicación web de y, a continuación, seleccione **dominios personalizados** desde la parte inferior de la página.

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. En el módulo de **dominios personalizados** , verá la dirección IP virtual. Guardar esta información, tal como se usará al crear registros DNS

    ![](./media/custom-dns-web-site/virtual-ip-address.png)

    > [AZURE.NOTE] No puede utilizar nombres de dominio personalizado con una aplicación web de **Free** y debe actualizar el plan de servicios de aplicación a **Shared**, **básico**, **estándar**o nivel **Premium** . Para obtener más información sobre el plan de servicios de aplicación precios niveles, incluido cómo cambiar el nivel de precio de la aplicación web, vea [cómo ampliar aplicaciones web](../articles/web-sites-scale.md).
