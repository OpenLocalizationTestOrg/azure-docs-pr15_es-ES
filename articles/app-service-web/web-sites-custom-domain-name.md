<properties
    pageTitle="Asignar un nombre de dominio personalizado a una aplicación de Azure"
    description="Obtenga información sobre cómo asignar un nombre de dominio personalizado (dominio de cortesía) a la aplicación de servicio de aplicaciones de Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"
    tags="top-support-issue"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/27/2016"
    ms.author="cephalin"/>

# <a name="map-a-custom-domain-name-to-an-azure-app"></a>Asignar un nombre de dominio personalizado a una aplicación de Azure

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Este artículo le muestra cómo asignar manualmente un nombre de dominio personalizado a su aplicación web, una aplicación móvil back-end o API de aplicación de [Servicio de la aplicación de Azure](../app-service/app-service-value-prop-what-is.md). 

La aplicación ya incluye un subdominio único de azurewebsites.net. Por ejemplo, si el nombre de la aplicación es **contoso**, su nombre de dominio es **contoso.azurewebsites.net**. Sin embargo, puede asignar un dominio personalizado nombre de aplicación para que la dirección URL, como `www.contoso.com`, refleja su marca.

>[AZURE.NOTE] Obtén ayuda de Azure expertos en los [foros de Azure](https://azure.microsoft.com/support/forums/). Incluso mayor nivel de soporte técnico, vaya al [sitio de soporte de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## <a name="buy-a-new-custom-domain-in-azure-portal"></a>Comprar un nuevo dominio personalizado en el portal de Azure

Si todavía no lo ha adquirido un nombre de dominio personalizado, puede comprar uno y administrar directamente en la configuración de la aplicación en el [portal de Azure](https://portal.azure.com). Esta opción facilita asignar un dominio personalizado a la aplicación, si su aplicación utiliza el [Administrador de tráfico de Azure](web-sites-traffic-manager-custom-domain-name.md) o no. 

Para obtener instrucciones, consulte [comprar un nombre de dominio personalizado para la aplicación de servicio](custom-dns-web-site-buydomains-web-app.md).

## <a name="map-a-custom-domain-you-purchased-externally"></a>Asignar un dominio personalizado que ha comprado externamente

Si ya ha adquirido un dominio personalizado de [Azure DNS](https://azure.microsoft.com/services/dns/) o de un proveedor de terceros, hay tres pasos principales para asignar el dominio personalizado a la aplicación:

1. [La dirección IP *(registro solo)* obtener la aplicación](#vip).
2. [Crear los registros DNS que asignen el dominio a su aplicación](#createdns). 
    - **Donde**: la herramienta de administración del registrador de dominio (por ejemplo, DNS de Azure, GoDaddy, etcetera).
    - **¿Por qué**: para que sepa de registrador se resuelva el dominio personalizado que desee a su aplicación de Azure.
1. [Habilitar el nombre de dominio personalizado para su aplicación de Azure](#enable).
    - **Donde**: el [portal de Azure](https://portal.azure.com).
    - **¿Por qué**: para que la aplicación sepa para responder a solicitudes de nombre de dominio personalizado.
3. [Comprobar DNS propagación](#verify).

### <a name="types-of-domains-you-can-map"></a>Tipos de dominios que puede asignar

Servicio de aplicaciones de Azure le permite asignar las siguientes categorías de dominios personalizados a la aplicación.

- **Dominio raíz** : el nombre de dominio reservado con el registrador (representada por la `@` hospedar registro, normalmente). Por ejemplo, **contoso.com**.
- **Subdominio** : cualquier dominio que está debajo de su dominio raíz. Por ejemplo, **www.contoso.com** (representada por la `www` registro de host).  Puede asignar diferentes subdominios del mismo dominio raíz a diferentes aplicaciones de Azure.
- **Dominio comodín** - [cualquier subdominio cuya etiqueta de la izquierda DNS es `*` ](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por ejemplo, los registros de host `*` y `*.blogs`). Por ejemplo, ** \*. contoso.com**.

### <a name="types-of-dns-records-you-can-use"></a>Tipos de registros DNS que se puede usar

Según sus necesidades, puede usar dos tipos de registros DNS estándares para asignar el dominio personalizado: 

- [A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) - asignaciones de nombre de dominio personalizado de la aplicación de Azure virtual IP dirección directamente. 
- [CNAME](https://en.wikipedia.org/wiki/CNAME_record) - asigna el nombre de dominio personalizado al nombre de dominio Azure de la aplicación, * *&lt;*nombre de aplicación*>. azurewebsites.net**. 

La ventaja de CNAME es que se mantiene a través de los cambios de direcciones IP. Si eliminar y volver a crear la aplicación o cambiar de un nivel superior de precios al nivel de **compartido** , puede cambiar la dirección IP virtual de la aplicación. A través de este cambio, un registro CNAME sigue siendo válido, mientras que un registro requiere una actualización. 

El tutorial muestra los pasos para usar el registro y también para usar el registro CNAME.

>[AZURE.IMPORTANT] No crear un registro CNAME para el dominio raíz (es decir, "registro de raíz"). Para obtener más información, consulte [¿por qué no se puede utilizar un registro CNAME en el dominio raíz](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain).
Para asignar un dominio raíz de la aplicación de Azure, use un registro en su lugar.

<a name="vip"></a>
## <a name="step-1-a-record-only-get-apps-ip-address"></a>Paso 1. *(Solo un registro)* Obtener la dirección IP de la aplicación
Para asignar un nombre de dominio personalizado mediante un registro, tiene la dirección IP de su aplicación de Azure. Si se asignará con un registro CNAME en su lugar, omita este paso y pasar a la siguiente sección.

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com).

2.  En el menú de la izquierda, haga clic en **Servicios de aplicación** .

4.  Haga clic en la aplicación y luego haga clic en **dominios personalizados**.

6.  Tome nota de la dirección IP por encima de la sección de nombres de host...

    ![Nombre de dominio personalizado de mapa con un registro: dirección IP obtener de la aplicación de servicio de la aplicación de Azure](./media/web-sites-custom-domain-name/virtual-ip-address.png)

7.  Mantener este módulo portal abierta. Tendrá que vuelva a él después de crear los registros DNS.

<a name="createdns"></a>
## <a name="step-2-create-the-dns-records"></a>Paso 2. Crear los registros DNS

Inicie sesión en el registrador de dominios y use las herramientas para agregar un registro a o registro CNAME. Interfaz de usuario de cada registrador es ligeramente diferente, por lo que debe consultar la documentación del proveedor. Sin embargo, hay algunas instrucciones generales.

1.  Busque la página de administración de registros DNS. Buscar vínculos o áreas del sitio de la etiqueta de **Nombre de dominio**, **DNS**o **Nombre de servidor de administración**. A menudo, puede encontrar el vínculo Ver información de su cuenta y, a continuación, buscando un vínculo como **My domains**.
2.  Busque un vínculo que le permite agregar o editar registros DNS. Puede ser un **archivo de zona** o vínculo de **Registros DNS** o un vínculo de configuración **avanzada** .
3.  Crear el registro y guardar los cambios.
    - [Instrucciones para un registro se están aquí](#a).
    - [Instrucciones para un registro CNAME están aquí](#cname).

<a name="a"></a>
### <a name="create-an-a-record"></a>Crear un registro

Para usar un registro para asignar a la dirección IP de su aplicación de Azure, realmente necesita crear un registro y un registro TXT. El registro es para la resolución del DNS y el registro TXT es para que Azure para verificar que posee el nombre de dominio personalizado. 

Configurar el registro de manera (@ normalmente representa el dominio raíz):
 
<table cellspacing="0" border="1">
  <tr>
    <th>Ejemplo de nombre de dominio completo</th>
    <th>Un Host</th>
    <th>Un valor</th>
  </tr>
  <tr>
    <td>contoso.com (raíz)</td>
    <td>@</td>
    <td>Dirección IP de <a href="#vip">paso 1</a></td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>¿"www"?</td>
    <td>Dirección IP de <a href="#vip">paso 1</a></td>
  </tr>
  <tr>
    <td>*. contoso.com (comodín)</td>
    <td>*</td>
    <td>Dirección IP de <a href="#vip">paso 1</a></td>
  </tr>
</table>

El registro TXT adicional adopta la convención de mapas de &lt; *subdominio*>. &lt; *dominio raíz*> a &lt; *nombre de aplicación*>. azurewebsites.net. Configurar el registro TXT de la siguiente manera:

<table cellspacing="0" border="1">
  <tr>
    <th>Ejemplo de nombre de dominio completo</th>
    <th>TXT Host</th>
    <th>Valor de TXT</th>
  </tr>
  <tr>
    <td>contoso.com (raíz)</td>
    <td>@</td>
    <td>&lt;<i>nombre de aplicación</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>¿"www"?</td>
    <td>&lt;<i>nombre de aplicación</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>*. contoso.com (comodín)</td>
    <td>*</td>
    <td>&lt;<i>nombre de aplicación</i>>. azurewebsites.net</td>
  </tr>
</table>

<a name="cname"></a>
###Cree un registro CNAME

Si utiliza un registro CNAME para asignar al nombre de dominio predeterminado de la aplicación de Azure, no es necesario un registro TXT adicional igual que con un registro. 

>[AZURE.IMPORTANT] No crear un registro CNAME para el dominio raíz (es decir, "registro de raíz"). Para obtener más información, consulte [¿por qué no se puede utilizar un registro CNAME en el dominio raíz](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain).
Para asignar un dominio raíz de la aplicación de Azure, use un [registro](#a) en su lugar.

Configurar el registro CNAME de manera (@ normalmente representa el dominio raíz):

<table cellspacing="0" border="1">
  <tr>
    <th>Ejemplo de nombre de dominio completo</th>
    <th>Host CNAME</th>
    <th>Valor CNAME</th>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>¿"www"?</td>
    <td>&lt;<i>nombre de aplicación</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>*. contoso.com (comodín)</td>
    <td>*</td>
    <td>&lt;<i>nombre de aplicación</i>>. azurewebsites.net</td>
  </tr>
</table>

<a name="enable"></a>
##Paso 3. Habilitar el nombre de dominio personalizado para la aplicación

En el módulo de **Dominios personalizados** en el portal de Azure (vea [paso 1](#vip)), debe agregar el nombre de dominio completo (FQDN) de su dominio personalizado a la lista.

1.  Si todavía no lo ha hecho, inicie sesión en el [portal de Azure](https://portal.azure.com).

2.  En el portal de Azure, haga clic en **Servicios de aplicación** en el menú de la izquierda.

3.  Haga clic en la aplicación, haga clic en **dominios personalizados** > **Agregar hostname**.

4.  Agregar el FQDN de su dominio personalizado a la lista (por ejemplo, **www.contoso.com**).

    ![Asignar un nombre de dominio personalizado a una aplicación de Azure: agregar a la lista de nombres de dominio](./media/web-sites-custom-domain-name/add-custom-domain.png)

    >[AZURE.NOTE] Azure intentará comprobar el nombre de dominio que utilice aquí. Asegúrese de que es el mismo nombre de dominio para el que creó un registro DNS en el [paso 2](#createdns). 

5.  Haga clic en **Validar**.

6.  Al hacer clic en **Validar** Azure se lanzará el flujo de trabajo de comprobación de dominio. Esto buscará propiedad del dominio, así como Hostname disponibilidad e informe detallada error o con orienteción normativa sobre cómo corregir el error.    

7.  Tras **Agregar hostname** una validación correcta botón estará activo y podrá para el nombre de host de asignar. 

8.  Una vez Azure termina de configurar su nuevo nombre de dominio personalizado, vaya a su nombre de dominio personalizado en un explorador. El explorador debe abrir la aplicación de Azure, lo que significa que su nombre de dominio personalizado está configurado correctamente.

> [AZURE.NOTE] Si el registro DNS ya está en usar (escenario de tráfico de dominio de active Servers) y necesita IntruShield enlazar la aplicación web para la verificación del dominio, simplemente crear registros TXT como ejemplos que se muestra en la tabla siguiente. El registro TXT adicional adopta la convención de mapas de &lt; *subdominio*>. &lt; *dominio raíz*> a &lt; *nombre de aplicación*>. azurewebsites.net. 
> <table cellspacing="0" border="1">
  <tr>
    <th>Ejemplo de nombre de dominio completo</th>
    <th>TXT Host</th>
    <th>Valor de TXT</th>
  </tr>
  <tr>
    <td>contoso.com (raíz)</td>
    <td>awverify.contoso.com</td>
    <td>&lt;<i>nombre de aplicación</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>awverify.www.contoso.com</td>
    <td>&lt;<i>nombre de aplicación</i>>. azurewebsites.net</td>
  </tr>
    <tr>
    <td>*. contoso.com (sub)</td>
    <td>awverify.*.contoso.com</td>
    <td>&lt;<i>nombre de aplicación</i>>. azurewebsites.net</td>
  </tr>
</table>
Una vez creado este registro DNS, volver al portal de Azure y agregue su nombre de dominio personalizado a su aplicación web.
 

<a name="verify"></a>
##Comprobar que la propagación DNS

Cuando termine de los pasos de configuración, puede tardar algún tiempo para que los cambios en propagarse, dependiendo de su proveedor DNS. Puede comprobar que la propagación DNS funciona como se esperaba usando [http://digwebinterface.com/](http://digwebinterface.com/). Después vaya al sitio, especifique los nombres de host en el cuadro de texto y haga clic en **Ir**. Comprobar los resultados para confirmar si se han aplicado los cambios recientes.  

![Asignar un nombre de dominio personalizado a una aplicación de Azure: comprobar propagación de DNS](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE] La propagación de las entradas DNS puede tardar hasta 48 horas (a veces ya). Si ha configurado todo correctamente, necesita esperar de la multiplicación correctamente.

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre cómo proteger su nombre de dominio personalizado con HTTPS [adquirir un certificado SSL en Azure](web-sites-purchase-ssl-web-site.md) o [utilizando un certificado SSL desde cualquier lugar](web-sites-configure-ssl-certificate.md).

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

[Introducción a Azure DNS](../dns/dns-getstarted-create-dnszone.md)  
[Crear registros DNS para una aplicación web en un dominio personalizado](../dns/dns-web-sites-custom-domain.md)  
[Dominio de delegado a Azure DNS](../dns/dns-domain-delegation.md)


<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png
