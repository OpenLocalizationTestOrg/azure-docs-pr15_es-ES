<properties
    pageTitle="Configurar un nombre de dominio personalizado en servicios de nube | Microsoft Azure"
    description="Obtenga información sobre cómo exponer su aplicación para Azure o los datos en un dominio personalizado mediante la configuración de DNS."
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="adegeo"/>

# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Configurar un nombre de dominio personalizado para un servicio de nube de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](cloud-services-custom-domain-name-portal.md)
- [Portal de clásico de Azure](cloud-services-custom-domain-name.md)


Cuando se crea un servicio de nube, Azure asigna a un subdominio del cloudapp.net. Por ejemplo, si su servicio de nube se denomina "contoso", los usuarios podrán obtener acceso a la aplicación en una dirección URL como http://contoso.cloudapp.net. Azure también asigna una dirección IP virtual.

Sin embargo, también puede exponer su aplicación en su propio nombre de dominio, por ejemplo, contoso.com. En este artículo se explica cómo reservar o configurar un nombre de dominio personalizado para las funciones web de servicio de nube.

¿Hacer usted ya undestand qué registros CNAME y A? [Vaya más allá de la explicación las cuales](#add-a-cname-record-for-your-custom-domain).

> [AZURE.NOTE]
> ¡Ponerse en marcha con mayor rapidez! Usar el [tutorial guiado de](http://support.microsoft.com/kb/2990804)Azure. Permite asociar un nombre de dominio personalizado y proteger la comunicación (SSL) con servicios de nube de Azure o los sitios Web de Azure un complemento.

<p/>

> [AZURE.NOTE]
> Los procedimientos descritos en esta tarea se aplican a los servicios de nube de Azure. Para los servicios de aplicación, vea [este](../app-service-web/web-sites-custom-domain-name.md). Para cuentas de almacenamiento, consulte [este](../storage/storage-custom-domain-name.md).


## <a name="understand-cname-and-a-records"></a>Comprender los registros CNAME y A

CNAME (alias registros o) y un registros permiten asociar un nombre de dominio con un servidor específico (o en este caso, del servicio) no obstante funcionan de manera diferente. También hay algunas consideraciones específicas al usar registros con servicios de nube de Azure que debe tener en cuenta antes de decidir que se va a usar.

### <a name="cname-or-alias-record"></a>Registro CNAME o Alias

Un registro CNAME asigna un dominio *específico* , como **contoso.com** o **www.contoso.com**, a un nombre de dominio canónico. En este caso, el nombre de dominio canónica es la **[myapp] .cloudapp .net** nombre de dominio de su Azure hospedado aplicación. Una vez creado, el registro CNAME crea un alias para el **[myapp] .cloudapp .net**. La entrada CNAME resolverá con la dirección IP de su **[myapp] .cloudapp .net** servicio automáticamente, por lo que si cambia la dirección IP del servicio de nube, no tiene que realizar ninguna acción.

> [AZURE.NOTE]
> Solo algunos registradores de dominios permiten asignar subdominios cuando se usa un registro CNAME, por ejemplo, www.contoso.com y no los nombres de raíz, por ejemplo, contoso.com. Para obtener más información sobre registros CNAME, vea la documentación proporcionada por el registrador, [la entrada de Wikipedia en el registro CNAME](http://en.wikipedia.org/wiki/CNAME_record)o el documento de [IETF los nombres de dominio - implementación y especificación](http://tools.ietf.org/html/rfc1035) .

### <a name="a-record"></a>Un registro

Un registro a asigna un dominio, por ejemplo, **contoso.com** o **www.contoso.com**, *o un dominio comodín* como ** \*. contoso.com**, a una dirección IP. En el caso de un servicio de nube de Azure, la dirección IP virtual del servicio. Para el beneficio principal de un registro a través de un registro CNAME es que puede tener una entrada que utiliza un carácter comodín, como \* **. contoso.com**, que desea controlar las solicitudes de varios subdominios como **mail.contoso.com**, **login.contoso.com**o **www.contso.com**.

> [AZURE.NOTE]
> Dado que un registro está asignado a una dirección IP estática, no puede resolver cambios automáticamente a la dirección IP de su servicio de nube. La dirección IP utilizada por el servicio de nube se asigna la primera vez que se implementa en una franja vacía (producción o ensayo.) Si elimina la implementación de la franja, Azure libera la dirección IP y las implementaciones futuras a la franja tendrá una nueva dirección IP.
>
> Para su comodidad, la dirección IP de un intervalo de implementación determinado (producción o ensayo) se conserva cuando intercambiar entre ensayo e implementaciones de producción o realizar una actualización en lugar de una implementación existente. Para obtener más información sobre cómo realizar estas acciones, consulte [cómo administrar servicios en la nube](cloud-services-how-to-manage.md).


## <a name="add-a-cname-record-for-your-custom-domain"></a>Agregar un registro CNAME de su dominio personalizado

Para crear un registro CNAME, debe agregar una nueva entrada en la tabla DNS de su dominio personalizado mediante el uso de las herramientas proporcionadas por el registrador. Cada registrador tiene un método similar, pero ligeramente diferente para especificar un registro CNAME, pero los conceptos son los mismos.

1. Siga uno de estos métodos para buscar la **. cloudapp.net** asignado a su servicio de nube de nombre de dominio.

    * Inicie sesión en el [portal de clásica Azure], seleccione el servicio de nube, seleccione **panel**y, a continuación, busque la entrada de la **Dirección URL del sitio** en la sección **vistazo rápido** .
    
        ![sección vistazo rápido que muestra la dirección URL del sitio][csurl]
    
        **OR**  
    
    * Instalar y configurar [Azure Powershell](../powershell-install-configure.md)y, a continuación, utilice el siguiente comando:
        
        ```powershell
        Get-AzureDeployment -ServiceName yourservicename | Select Url
        ```
    
    Guardar el nombre de dominio que se usa en la dirección URL devuelta por cualquiera de los métodos, ya que lo necesitará al crear un registro CNAME.

1.  Inicie sesión en el sitio Web del registrador de su DNS y vaya a la página de administración de DNS. Buscar vínculos o áreas del sitio con la etiqueta como **Nombre de dominio**, **DNS**o un **Nombre de servidor de administración**.

2.  Buscar ahora donde puede seleccionar o escriba de CNAME. Debe seleccionar el tipo de registro de una lista desplegable o ir a una página de configuración avanzada. Debe buscar las palabras **CNAME**, **Alias**o **subdominios**.

3.  También debe proporcionar el alias de dominio o un subdominio del registro CNAME, como **www** si desea crear un alias para **www.customdomain.com**. Si desea crear un alias para el dominio raíz, puede aparecer como el '**@**' símbolo en herramientas DNS de su registrador.

4. A continuación, proporcione un nombre de host canónico, que es el dominio de la aplicación **cloudapp.net** en este caso.

Por ejemplo, el siguiente registro CNAME reenvía todo el tráfico de **www.contoso.com** a **contoso.cloudapp.net**, el nombre de dominio personalizado de la aplicación implementada:

| Nombre de Host o alias o subdominio | Dominio canónico     |
| ------------------------- | -------------------- |
| ¿"www"?                       | contoso.cloudapp.NET |

Un visitante de **www.contoso.com** nunca verán el host true (contoso.cloudapp.net), por lo que el proceso de reenvío es invisible para el usuario final.

> [AZURE.NOTE]
> El ejemplo anterior se aplica solo a tráfico en el subdominio **www** . Puesto que no puede usar caracteres comodín con registros CNAME, debe crear un CNAME para cada dominio y subdominio. Si desea dirigir el tráfico de subdominios, como por ejemplo \*. contoso.com, en la dirección cloudapp.net, puede configurar una entrada de **Redirección de URL** o **Dirección URL hacia delante** en la configuración de DNS o crear un registro.


## <a name="add-an-a-record-for-your-custom-domain"></a>Agregar un registro de su dominio personalizado

Para crear un registro, debe buscar primero la dirección IP virtual de su servicio de nube. A continuación, agregue una nueva entrada en la tabla DNS de su dominio personalizado mediante el uso de las herramientas proporcionadas por el registrador. Cada registrador tiene un método similar, pero ligeramente diferente para especificar un registro, pero los conceptos son los mismos.

1. Utilice uno de los métodos siguientes para obtener la dirección IP de su servicio de nube.
    
    * Inicie sesión en el [portal de clásica Azure], seleccione el servicio de nube, seleccione **panel**y, a continuación, busque la entrada de la **dirección IP Virtual público (VIP)** en la sección **vistazo rápido** .
    
        ![sección vistazo rápido que muestra la dirección VIP][vip]
    
        **OR**  
    
    * Instalar y configurar [Azure Powershell](../powershell-install-configure.md)y, a continuación, utilice el siguiente comando:
    
        ```powershell
        get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
        ```
    
    Si tiene varios extremos asociados con el servicio de nube, recibirá varias líneas que contiene la dirección IP, pero todos deben mostrar la misma dirección.
    
    Guarde la dirección IP, ya que lo necesitará al crear un registro.

1.  Inicie sesión en el sitio Web del registrador de su DNS y vaya a la página de administración de DNS. Buscar vínculos o áreas del sitio con la etiqueta como **Nombre de dominio**, **DNS**o un **Nombre de servidor de administración**.

2.  Buscar ahora donde puede seleccionar o escribir un registro. Debe seleccionar el tipo de registro de una lista desplegable o ir a una página de configuración avanzada.

3. Seleccione o escriba el dominio o el subdominio que usará este registro. Por ejemplo, seleccione **www** si desea crear un alias para **www.customdomain.com**. Si desea crear una entrada de caracteres comodín para todos los subdominios, escriba '__*__'. Esto cubrirá todos los subdominios, como **mail.customdomain.com**, **login.customdomain.com**y **www.customdomain.com**.

    Si desea crear un registro para el dominio raíz, puede aparecer como el '**@**' símbolo en herramientas DNS de su registrador.

4. Escriba la dirección IP de su servicio de nube en el campo correspondiente. Asocia la entrada de dominio utilizada en el registro con la dirección IP de su implementación de servicio de nube.

Por ejemplo, el siguiente que un registro reenvía todo el tráfico de **contoso.com** a **137.135.70.239**, la dirección IP de su aplicación implementada:

| Nombre de host o subdominio | Dirección IP     |
| ------------------- | -------------- |
| @                   | 137.135.70.239 |



Este ejemplo muestra cómo crear un registro para el dominio raíz. Si desea crear una entrada de caracteres comodín para cubrir todos los subdominios, debe escribir '__*__' como el subdominio.

>[AZURE.WARNING]
>Direcciones IP de Azure son dinámicas de forma predeterminada. Probablemente deseará utilizar una [dirección IP reservada](../virtual-network/virtual-networks-reserved-public-ip.md) para asegurarse de que no pueda cambiar su dirección IP.

## <a name="next-steps"></a>Pasos siguientes

* [Cómo administrar servicios en la nube](cloud-services-how-to-manage.md)
* [Cómo asignar CDN contenido a un dominio personalizado](../cdn/cdn-map-content-to-custom-domain.md)
* [Configuración general de su servicio de nube](cloud-services-how-to-configure.md).
* Obtenga información sobre cómo [implementar un servicio de nube](cloud-services-how-to-create-deploy.md).
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate.md).




[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: http://msdn.microsoft.com/library/ee517253.aspx
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Portal de clásico de Azure]: https://manage.windowsazure.com
[Validate Custom Domain dialog box]: http://i.msdn.microsoft.com/dynimg/IC544437.jpg
[vip]: ./media/cloud-services-custom-domain-name/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name/csurl.png
 