#<a name="configuring-a-custom-domain-name-for-an-azure-website"></a>Configurar un nombre de dominio personalizado para un sitio Web de Azure

Cuando se crea un sitio Web, Azure proporciona un subdominio descriptivo en el dominio azurewebsites.net para los usuarios pueden tener acceso a su sitio Web con una dirección URL como http://&lt;misitio >. azurewebsites.net. Sin embargo, si configura los sitios Web de compartido o el modo estándar, puede asignar su sitio Web a su propio nombre de dominio.

Si lo desea, puede utilizar el Administrador de tráfico de Azure para cargar saldo el tráfico entrante a su sitio Web. Para obtener más información sobre el funcionamiento del Administrador de tráfico con sitios Web, vea [Controlar el tráfico de sitios Web de Azure con el administrador del tráfico de Azure][trafficmanager].

> [AZURE.NOTE] Los procedimientos descritos en esta tarea se aplican a sitios Web de Azure; para servicios de nube, consulte <a href="/develop/net/common-tasks/custom-dns/">configurar un nombre de dominio personalizado en Azure</a>.

> [AZURE.NOTE] Los pasos de esta tarea requieren que configurar los sitios Web para compartido o modo estándar, que puede cambiar la cantidad de facturación de la suscripción. Para obtener más información, consulte <a href="/pricing/details/web-sites/">Detalles de precios de sitios Web</a> .

En este artículo:

-   [Descripción de los registros CNAME y A](#understanding-records)
-   [Configurar sus sitios web para el modo estándar o compartido](#bkmk_configsharedmode)
-   [Agregar al administrador de tráfico de los sitios web](#trafficmanager)
-   [Agregar un CNAME de su dominio personalizado](#bkmk_configurecname)
-   [Agregar un registro de su dominio personalizado](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>Comprender los registros CNAME y A</h2>

CNAME (alias registros o) y un registros permiten asociar un nombre de dominio a un sitio Web, sin embargo, cada uno de ellos funciona de forma diferente.

###<a name="cname-or-alias-record"></a>Registro CNAME o Alias

Un registro CNAME asigna un dominio *específico* , como **contoso.com** o **www.contoso.com**, a un nombre de dominio canónico. En este caso, el nombre de dominio canónica es la la ** &lt;myapp >. azurewebsites.net** nombre de dominio de su sitio Web Azure o la ** &lt;myapp >. trafficmgr.com** nombre de dominio de su perfil de administrador de tráfico. Una vez creado, el registro CNAME crea un alias para la ** &lt;myapp >. azurewebsites.net** o ** &lt;myapp >. trafficmgr.com** nombre de dominio. La entrada CNAME resolverá con la dirección IP de su ** &lt;myapp >. azurewebsites.net** o ** &lt;myapp >. trafficmgr.com** nombre de dominio automáticamente, por lo que si cambia la dirección IP del sitio Web, no tiene que realizar ninguna acción.

> [AZURE.NOTE] Solo algunos registradores de dominios permiten asignar subdominios cuando se usa un registro CNAME, por ejemplo, www.contoso.com y no los nombres de raíz, por ejemplo, contoso.com. Para obtener más información sobre registros CNAME, vea la documentación proporcionada por el registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">la entrada de Wikipedia en el registro CNAME</a>o el documento de <a href="http://tools.ietf.org/html/rfc1035">IETF los nombres de dominio - implementación y especificación</a> .

###<a name="a-record"></a>Un registro

Un registro a asigna un dominio, por ejemplo, **contoso.com** o **www.contoso.com**, *o un dominio comodín* como ** \*. contoso.com**, a una dirección IP. En el caso de un sitio Web de Azure, la dirección IP virtual del servicio o una dirección IP específica de direcciones que se adquirieron para el sitio Web. Para el beneficio principal de un registro a través de un registro CNAME es que puede tener una entrada que utiliza un carácter comodín, como * **. contoso.com**, que podría tratar las solicitudes de varios subdominios como * *mail.contoso.com**, * *login.contoso.com**, o * *www.contso.com**.

> [AZURE.NOTE] Dado que un registro está asignado a una dirección IP estática, no puede resolver cambios automáticamente a la dirección IP de su sitio Web. Una dirección IP para su uso con los registros que se proporciona al configurar las opciones de nombre de dominio personalizado para su sitio Web; Sin embargo, puede cambiar este valor si eliminar y volver a crear el sitio Web o cambiar el modo de sitio Web al fondo para liberar.

> [AZURE.NOTE] Los registros no se pueden usar con el Administrador de tráfico de equilibrio de carga. Para obtener más información, vea [Controlar el tráfico de sitios Web de Azure con el administrador del tráfico de Azure][trafficmanager].

<a name="bkmk_configsharedmode"></a><h2>Configurar los sitios Web para el modo estándar o compartido</h2>

Establecer un nombre de dominio personalizado en un sitio Web sólo está disponible para los modos estándares para sitios Web de Azure y el compartido. Antes de cambiar de un sitio Web desde el modo de sitio Web gratuita para el compartido o sitio Web estándar, primero debe quitar mayúsculas gastos en lugar para la suscripción de sitio Web. Para obtener más información sobre precios de modo compartido y estándar, consulte [Detalles de precios][PricingDetails].

1. En el explorador, abra el [Portal de administración de][portal].
2. En la ficha **sitios Web** , haga clic en el nombre del sitio.

    ![][standardmode1]

3. Haga clic en la ficha **escala** .

    ![][standardmode2]


4. En la sección **general** , establezca el modo de sitio Web, haga clic en **compartido**.

    ![][standardmode3]

    > [AZURE.NOTE] Si va a utilizar el Administrador de tráfico con este sitio Web, debe utilizar seleccione modo estándar en lugar de compartidos.

5. Haga clic en **Guardar**.
6. Cuando se le pregunte sobre el aumento de costo de modo compartido (o modo estándar si elige estándar), haga clic en **Sí** si está de acuerdo.

    <!--![][standardmode4]-->

    **Nota**<br />
   Si recibe un error "Configurar escala de sitio Web 'nombre del sitio Web' error", puede usar el botón Detalles para obtener más información.

<a name="trafficmanager"></a><h2>(Opcional) Agregar al administrador de tráfico de los sitios Web</h2>

Si desea usar su sitio Web con el Administrador de tráfico, realice los pasos siguientes.

1. Si no dispone de un perfil de administrador de tráfico, use la información en [crear un perfil de administrador de tráfico mediante la creación rápida] [ createprofile] para crear uno. Nota la **. trafficmgr.com** nombre de dominio asociado con el perfil de administrador de tráfico. Se usará en un paso posterior.

2. Use la información en [Agregar o eliminar extremos] [ addendpoint] para agregar un sitio Web como un extremo en el perfil de administrador de tráfico.

    > [AZURE.NOTE] Si su sitio Web no aparece al agregar un punto final, compruebe que está configurado para el modo estándar. Debe usar el modo estándar para el sitio Web para poder trabajar con el Administrador de tráfico.

3. Inicie sesión en el sitio Web del registrador de su DNS y vaya a la página de administración de DNS. Buscar vínculos o áreas del sitio con la etiqueta como **Nombre de dominio**, **DNS**o un **Nombre de servidor de administración**.

4. Buscar ahora donde puede seleccionar o insertar registros CNAME. Debe seleccionar el tipo de registro de una lista desplegable o ir a una página de configuración avanzada. Debe buscar las palabras **CNAME**, **Alias**o **subdominios**.

5. También debe proporcionar el alias de dominio o un subdominio del registro CNAME. Por ejemplo, **www** si desea crear un alias para **www.customdomain.com**.

5. También debe proporcionar un nombre de host es el nombre de dominio canónico para este alias CNAME. Esta es la **. trafficmgr.com** nombre para el sitio Web.

Por ejemplo, el siguiente registro CNAME reenvía todo el tráfico de **www.contoso.com** a **contoso.trafficmgr.com**, el nombre de dominio de un sitio Web:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nombre de Host o alias o subdominio</strong></td>
<td><strong>Dominio canónico</strong></td>
</tr>
<tr>
<td>¿"www"?</td>
<td>contoso.trafficmgr.com</td>
</tr>
</table>

Un visitante de **www.contoso.com** nunca verán el host true (contoso.azurewebsite.net), por lo que el proceso de reenvío es invisible para el usuario final.

> [AZURE.NOTE] Si está utilizando el Administrador de tráfico con un sitio Web, no es necesario que siga las instrucciones en las secciones siguientes, '**Agregar un CNAME de su dominio personalizado**' y '**Agregar un registro de su dominio personalizado**'. El registro CNAME creado en los pasos anteriores usará para enrutar el tráfico entrante para el Administrador de tráfico, que enruta el tráfico a los extremos de sitio Web.

<a name="bkmk_configurecname"></a><h2>Agregar un CNAME de su dominio personalizado</h2>

Para crear un registro CNAME, debe agregar una nueva entrada en la tabla DNS de su dominio personalizado mediante herramientas proporcionadas por el registrador. Cada registrador tiene un método similar, pero ligeramente diferente para especificar un registro CNAME, pero los conceptos son los mismos.

1. Siga uno de estos métodos para buscar la **. azurewebsite.net** nombre de dominio asignado a su sitio Web.

    * Inicie sesión en el [Portal de administración de Azure][portal], seleccione el sitio Web, seleccione **paneles**y, a continuación, busque la entrada de la **Dirección URL del sitio** en la sección **vistazo rápido** .

    * Instalar y configurar [Azure Powershell](/manage/install-and-configure-windows-powershell/)y, a continuación, utilice el siguiente comando:

            get-azurewebsite yoursitename | select hostnames

    * Instalar y configurar la [interfaz de línea de comandos de Azure](/manage/install-and-configure-cli/)y, a continuación, utilice el siguiente comando:

            azure site domain list yoursitename

    Guardar este **. azurewebsite.net** nombre, que se utilizará en los pasos siguientes.

3. Inicie sesión en el sitio Web del registrador de su DNS y vaya a la página de administración de DNS. Buscar vínculos o áreas del sitio con la etiqueta como **Nombre de dominio**, **DNS**o un **Nombre de servidor de administración**.

4. Buscar ahora donde puede seleccionar o insertar registros CNAME. Debe seleccionar el tipo de registro de una lista desplegable o ir a una página de configuración avanzada. Debe buscar las palabras **CNAME**, **Alias**o **subdominios**.

5. También debe proporcionar el alias de dominio o un subdominio del registro CNAME. Por ejemplo, **www** si desea crear un alias para **www.customdomain.com**. Si desea crear un alias para el dominio raíz, puede aparecer como el '**@**' símbolo en herramientas DNS de su registrador.

5. También debe proporcionar un nombre de host es el nombre de dominio canónico para este alias CNAME. Esta es la **. azurewebsite.net** nombre para el sitio Web.

Por ejemplo, el siguiente registro CNAME reenvía todo el tráfico de **www.contoso.com** a **contoso.azurewebsite.net**, el nombre de dominio de un sitio Web:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nombre de Host o alias o subdominio</strong></td>
<td><strong>Dominio canónico</strong></td>
</tr>
<tr>
<td>¿"www"?</td>
<td>contoso.azurewebsite.NET</td>
</tr>
</table>

Un visitante de **www.contoso.com** nunca verán el host true (contoso.azurewebsite.net), por lo que el proceso de reenvío es invisible para el usuario final.

> [AZURE.NOTE] El ejemplo anterior se aplica solo a tráfico en el subdominio __www__ . Puesto que no puede usar caracteres comodín con registros CNAME, debe crear un CNAME para cada dominio y subdominio. Si desea dirigir el tráfico de subdominios, como *. contoso.com, en la dirección azurewebsite.net, puede configurar una entrada de __Redirección de URL__ o __Dirección URL hacia delante__ en la configuración de DNS o crear un registro.

> [AZURE.NOTE] Puede tardar algún tiempo para su CNAME en propagarse a través del sistema DNS. No puede establecer el registro CNAME para el sitio Web hasta que haya propagado el registro CNAME. Puede usar un servicio como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para comprobar que el registro CNAME está disponible.

###<a name="add-the-domain-name-to-your-website"></a>Agregar el nombre de dominio a su sitio Web

Una vez haya propagado el registro CNAME para el nombre de dominio, debe asociar con el sitio Web. Puede agregar el nombre de dominio personalizado definido por el registro CNAME a su sitio Web mediante alguno la Azure de línea de comandos interfaz Azure o a través del Portal de administración de Azure.

**Para agregar un nombre de dominio con las herramientas de línea de comandos**

Instalar y configurar la [interfaz de línea de comandos de Azure](/manage/install-and-configure-cli/)y, a continuación, utilice el siguiente comando:

    azure site domain add customdomain yoursitename

Por ejemplo, el siguiente agregará un nombre de dominio personalizado de **www.contoso.com** al sitio Web de **contoso.azurewebsite.net** :

    azure site domain add www.contoso.com contoso

Puede confirmar que se ha agregado el nombre de dominio personalizado para el sitio Web mediante el comando siguiente:

    azure site domain list yoursitename

La lista devuelta por este comando debe contener el nombre de dominio personalizado, así como el valor predeterminado **. azurewebsite.net** entrada.

**Para agregar un nombre de dominio con el Portal de administración de Azure**

1. En el explorador, abra el [Portal de administración de Azure][portal].

2. En la ficha **sitios Web** , haga clic en el nombre del sitio, seleccione **paneles**y, a continuación, seleccione **Administrar dominios** desde la parte inferior de la página.

    ![][setcname2]

6. En el cuadro de texto de **Los nombres de dominio** , escriba el nombre de dominio que haya configurado.

    ![][setcname3]

6. Haga clic en la marca de verificación para aceptar el nombre de dominio.

Una vez completada la configuración, se mostrará el nombre de dominio personalizado en la sección de **nombres de dominio** de **la página de su sitio Web** .

<a name="bkmk_configurearecord"></a><h2>Agregar un registro de su dominio personalizado</h2>

Para crear un registro, debe buscar primero la dirección IP de su sitio Web. A continuación, agregue una entrada en la tabla DNS de su dominio personalizado mediante el uso de las herramientas proporcionadas por el registrador. Cada registrador tiene un método similar, pero ligeramente diferente para especificar un registro, pero los conceptos son los mismos. Además de crear un registro, también debe crear un registro CNAME que Azure se usa para comprobar el registro.

1. En el explorador, abra el [Portal de administración de Azure][portal].

2. En la ficha **sitios Web** , haga clic en el nombre del sitio, seleccione **paneles**y, a continuación, seleccione **Administrar dominios** desde la parte inferior de la pantalla.

    ![][setcname2]

5. En el cuadro de diálogo **administrar los dominios personalizados** , busque **La dirección IP al configurar los registros**. Copie la dirección IP. Se usará al crear el registro.

5. En el cuadro de diálogo **administrar los dominios personalizados** , anote el nombre de dominio de awverify al final del texto en la parte superior del cuadro de diálogo. Debe ser **awverify.mysite.azurewebsites.net** donde **Mi sitio** es el nombre de su sitio Web. Copiar, ya que es el nombre de dominio que se utiliza cuando se crea la verificación del registro CNAME.

6. Inicie sesión en el sitio Web del registrador de su DNS y vaya a la página de administración de DNS. Buscar vínculos o áreas del sitio con la etiqueta como **Nombre de dominio**, **DNS**o un **Nombre de servidor de administración**.

6. Buscar donde puede seleccionar o insertar registros A y CNAME. Debe seleccionar el tipo de registro de una lista desplegable o ir a una página de configuración avanzada.

7. Realice los pasos siguientes para crear el registro:

    1. Seleccione o escriba el dominio o el subdominio que usará el registro. Por ejemplo, seleccione **www** si desea crear un alias para **www.customdomain.com**. Si desea crear una entrada de caracteres comodín para todos los subdominios, escriba '__*__'. Esto cubrirá todos los subdominios, como **mail.customdomain.com**, **login.customdomain.com**y **www.customdomain.com**.

        Si desea crear un registro para el dominio raíz, puede aparecer como el '**@**' símbolo en herramientas DNS de su registrador.

    2. Escriba la dirección IP de su servicio de nube en el campo correspondiente. Asocia la entrada de dominio utilizada en el registro con la dirección IP de su implementación de servicio de nube.

        Por ejemplo, el siguiente que un registro reenvía todo el tráfico de **contoso.com** a **137.135.70.239**, la dirección IP de nuestra aplicación implementada:

        <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
        <tr>
        <td><strong>Nombre de host o subdominio</strong></td>
        <td><strong>Dirección IP</strong></td>
        </tr>
        <tr>
        <td>@</td>
        <td>137.135.70.239</td>
        </tr>
        </table>

        Este ejemplo muestra cómo crear un registro para el dominio raíz. Si desea crear una entrada de caracteres comodín para cubrir todos los subdominios, debe escribir '__*__' como el subdominio.

7. A continuación, cree un registro CNAME con un alias de **awverify**y un dominio canónico de **awverify.mysite.azurewebsites.net** que obtuvo anteriormente.

    > [AZURE.NOTE] Aunque un alias de awverify puede trabajar para una lista de registradores, otros usuarios pueden requerir el nombre de dominio completo alias de awverify.www.customdomainname.com o awverify.customdomainname.com.

    Por ejemplo, el siguiente creará un registro CNAME Azure puede utilizar para comprobar la configuración de registro A.

    <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
    <tr>
    <td><strong>Nombre de Host o alias o subdominio</strong></td>
    <td><strong>Dominio canónico</strong></td>
    </tr>
    <tr>
    <td>awverify</td>
    <td>awverify.contoso.azurewebsites.NET</td>
    </tr>
    </table>

> [AZURE.NOTE] Puede tardar algún tiempo para la awverify CNAME en propagarse a través del sistema DNS. No puede establecer el nombre de dominio personalizado definido por el registro para el sitio Web hasta que haya propagado la awverify CNAME. Puede usar un servicio como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para comprobar que el registro CNAME está disponible.

###<a name="add-the-domain-name-to-your-website"></a>Agregar el nombre de dominio a su sitio Web

Una vez haya propagado CNAME **awverify** de nombre de dominio, a continuación, puede asociar el dominio personalizado definido por el registro con el sitio Web. Puede agregar el nombre de dominio personalizado definido por el registro a su sitio Web mediante la CLI de Azure o a través del Portal de administración de Azure.

**Para agregar un nombre de dominio mediante la interfaz de línea de comandos de Azure (Azure CLI)**

Instalar y configurar [CLI de Azure](/manage/install-and-configure-cli/)y, a continuación, utilice el siguiente comando:

    azure site domain add customdomain yoursitename

Por ejemplo, el siguiente agregará un nombre de dominio personalizado de **contoso.com** para el sitio Web de **contoso.azurewebsite.net** :

    azure site domain add contoso.com contoso

Puede confirmar que se ha agregado el nombre de dominio personalizado para el sitio Web mediante el comando siguiente:

    azure site domain list yoursitename

La lista devuelta por este comando debe contener el nombre de dominio personalizado, así como el valor predeterminado **. azurewebsite.net** entrada.

**Para agregar un nombre de dominio con el Portal de administración de Azure**

1. En el explorador, abra el [Portal de administración de Azure][portal].

2. En la ficha **sitios Web** , haga clic en el nombre del sitio, seleccione **paneles**y, a continuación, seleccione **Administrar dominios** desde la parte inferior de la página.

    ![][setcname2]

6. En el cuadro de texto de **Los nombres de dominio** , escriba el nombre de dominio que haya configurado.

    ![][setcname3]

6. Haga clic en la marca de verificación para aceptar el nombre de dominio.

Una vez completada la configuración, se mostrará el nombre de dominio personalizado en la sección de **nombres de dominio** de **la página de su sitio Web** .

> [AZURE.NOTE] Después de agregar el nombre de dominio personalizado definido por el registro a su sitio Web, puede quitar el registro CNAME de awverify con las herramientas proporcionadas por el registrador. Sin embargo, si desea agregar A otro registro en el futuro, tendrá que volver a crear la awverify registro para poder asociar el nuevo nombre de dominio definida por el nuevo un registro con el sitio Web.

## <a name="next-steps"></a>Pasos siguientes

-   [Cómo administrar sitios web](/manage/services/web-sites/how-to-manage-websites/)

-   [Configurar un certificado SSL para sitios Web](/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]:#bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../articles/app-service-web/web-sites-traffic-manager.md
[addendpoint]: ../articles/traffic-manager/traffic-manager-endpoints.md
[createprofile]: ../articles/traffic-manager/traffic-manager-manage-profiles.md

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png
