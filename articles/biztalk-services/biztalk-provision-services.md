<properties
    pageTitle="Crear Azure BizTalk Services en el portal de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo aprovisionar o crear Azure BizTalk Services en el portal de Azure MABS, WABS"
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>



# <a name="create-biztalk-services-using-the-azure-portal"></a>Crear los servicios de BizTalk con el portal de Azure

Crear Azure BizTalk Services en el portal de Azure.

> [AZURE.TIP] Para iniciar sesión el portal de Azure, necesita una cuenta de Azure y Azure suscripción. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita en unos minutos. Ver la [versión de prueba gratuita de Azure](http://go.microsoft.com/fwlink/p/?LinkID=239738).

## <a name="create-a-biztalk-service"></a>Crear un servicio de BizTalk
Según la edición que elija, no todas las configuraciones de BizTalk Service pueden estar disponibles.

1. Inicie sesión en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación de la parte inferior, seleccione **nuevo**:  
![Seleccione el botón nuevo][NEWButton]

3. Seleccione los **Servicios de aplicación** > **servicio BIZTALK** > **Crear personalizado**:  
![Seleccione BizTalk Service y seleccione Crear personalizado][NewBizTalkService]

4. Especifique la configuración de BizTalk Service:

    <table border="1">
    <tr>
    <td><strong>Nombre de servicio de BizTalk</strong></td>
    <td>Puede introducir cualquier nombre pero sea específico. Algunos ejemplos:<br/><br/>
    <em>MyCompany</em>. biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>. biztalk.windows.net<br/>
    <em>MyApplication</em>. biztalk.windows.net<br/><br/>". biztalk.windows.net" se agrega automáticamente el nombre que escriba. Esto crea una dirección URL que se utiliza para acceder a su BizTalk Service, como <strong>https://<em>myapplication</em>. biztalk.windows.net</strong>.
    </td>
    </tr>
    <tr>
    <td><strong>Edition</strong></td>
    <td>Si se encuentra en la fase de pruebas o desarrollo, elija <strong>Programador</strong>. Si se encuentra en la fase de producción, use la <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">servicios BizTalk: gráfico de ediciones</a> para determinar si <strong>Premium</strong>, <strong>estándar</strong>o <strong>básica</strong> es la opción correcta para su escenario de negocio.
    </td>
    </tr>
    <tr>
    <td><strong>Región</strong></td>
    <td>Seleccione la región geográfica para hospedar el BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>Dirección URL del dominio</strong></td>
    <td><strong>Opcional</strong>. De forma predeterminada, la dirección URL del dominio es <em>YourBizTalkServiceName</em>. biztalk.windows.net. También puede especificar un dominio personalizado. Por ejemplo, si su dominio es <em>contoso</em>, puede especificar: <br/><br/>
    <em>MyCompany</em>. contoso.com<br/>
    <em>MyCompanyMyApplication</em>. contoso.com<br/>
    <em>MyApplication</em>. contoso.com<br/>
    <em>YourBizTalkServiceName</em>. contoso.com<br/>
    </td>
    </tr>
    </table>
Haga clic en la flecha siguiente.

5. Escriba el almacenamiento y la configuración de la base de datos:

    <table border="1">
    <tr>
    <td><strong>Cuenta de almacenamiento de supervisión y archivado</strong></td>
    <td>Seleccione una cuenta de almacenamiento existente o crear una nueva cuenta de almacenamiento. <br/><br/>Si crea una nueva cuenta de almacenamiento, escriba el <strong>Nombre de la cuenta de almacenamiento</strong>.</td>
    </tr>
    <tr>
    <td><strong>Base de datos de seguimiento</strong></td>
    <td>Si usa una base de datos de SQL Azure existente, no se pueden usar por otro BizTalk Service. Es necesario el nombre de inicio de sesión y contraseña al que se creó el servidor de base de datos de SQL Azure.<br/><br/><strong>Sugerencia</strong> Crear la base de datos de seguimiento y la cuenta de almacenamiento de supervisión y archivado en la misma región como BizTalk Service.</td>
    </tr>
    </table>
Haga clic en la flecha siguiente.

6. Especificar la configuración de la base de datos:

    <table border="1">
    <tr>
    <td><strong>Nombre</strong></td>
    <td>Disponible cuando se selecciona <strong>crear una nueva instancia de base de datos SQL</strong> en la pantalla anterior.
    <br/><br/>
   Escriba un nombre de base de datos SQL para usarlo con su BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>Servidor</strong></td>
    <td>Disponible cuando se selecciona <strong>crear una nueva instancia de base de datos SQL</strong> en la pantalla anterior.
    <br/><br/>
   Seleccione un servidor de base de datos de SQL existente o cree un nuevo servidor de base de datos de SQL.</td>
    </tr>
    <tr>
    <td><strong>Nombre de inicio de sesión de servidor</strong></td>
    <td>Escriba el nombre de usuario de inicio de sesión.</td>
    </tr>
    <tr>
    <td><strong>Contraseña de inicio de sesión de servidor</strong></td>
    <td>Escriba la contraseña de inicio de sesión.</td>
    </tr>
    <tr>
    <td><strong>Región</strong></td>
    <td>Disponible cuando se selecciona <strong>crear una nueva instancia de base de datos de SQL</strong> . Seleccione la región geográfica para hospedar la base de datos de SQL.</td>
    </tr>
    </table>

Seleccione la marca de verificación para completar al asistente. Aparecerá el icono de progreso:  
![Icono de progreso muestra al terminar][ProgressComplete]

Cuando haya finalizado, el servicio de BizTalk Azure se creen y preparado para las aplicaciones. La configuración predeterminada es suficiente. Si desea cambiar la configuración predeterminada, seleccione **BIZTALK SERVICES** en el panel de navegación izquierdo y, a continuación, seleccione su BizTalk Service. Configuración adicional se muestra en las [fichas de paneles, Monitor y escala](biztalk-dashboard-monitor-scale-tabs.md) en la parte superior.

Dependiendo del estado de BizTalk Service, hay algunas operaciones que no puede completarse. Para obtener una lista de estas operaciones, vaya al [Gráfico de estado de servicios de BizTalk](biztalk-service-state-chart.md).


## <a name="post-provisioning-steps"></a>Pasos posteriores a la aprovisionamiento

-  [Instalar el certificado en un equipo local](#InstallCert)
-  [Agregar un certificado de producción listo](#AddCert)
-  [Obtener el espacio de nombres de Control de acceso](#ACS)

#### <a name="InstallCert"></a>Instalar el certificado en un equipo local
Como parte de aprovisionamiento de BizTalk Service, un certificado autofirmado es creado y asociado a su suscripción de BizTalk Service. Debe descargar este certificado e instálelo en equipos desde donde implementa aplicaciones BizTalk Service o enviar mensajes a un extremo de BizTalk Service.

1. Inicie sesión en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Seleccione **Servicios BIZTALK** en el panel de navegación izquierdo y, a continuación, seleccione la suscripción BizTalk Service.
3. Seleccione la pestaña **panel** .
4. Seleccione **Descargar certificado SSL**:  
![Modificar certificado SSL][QuickGlance]
5. Haga doble clic en el certificado y ejecute el Asistente para instalar el certificado. Asegúrese de que instalar el certificado en el almacén de **Entidades emisoras de certificados raíz de confianza** .

#### <a name="AddCert"></a>Agregar un certificado de producción listo
El certificado autofirmado que se crea automáticamente al crear BizTalk Services está pensado para su uso en entornos de desarrollo solo. Escenarios de producción, reemplácelo con un certificado de producción listo.

1. En la pestaña **panel** , seleccione el **Certificado SSL de actualización**.
2. Busque el certificado SSL privado (*CertificateName*.pfx) que incluya el nombre de BizTalk Service, escriba la contraseña y, a continuación, haga clic en la marca de verificación.

#### <a name="ACS"></a>Obtener el espacio de nombres de Control de acceso

1. Inicie sesión en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Seleccione **BIZTALK SERVICES** en el panel de navegación izquierdo y, a continuación, seleccione su BizTalk Service.
3. En la barra de tareas, seleccione **Información de conexión**:  
![Seleccione la información de conexión][ACSConnectInfo]

4. Copie los valores de Control de acceso.

Cuando se implementa un proyecto de BizTalk Service desde Visual Studio, escriba este espacio de nombres de Control de acceso. El espacio de nombres de Control de acceso se crea automáticamente para su BizTalk Service.

Los valores de Control de acceso se pueden utilizar con cualquier aplicación. Cuando se crea Azure BizTalk Services, este espacio de nombres de Control de acceso controla la autenticación con la implementación de BizTalk Service. Si desea cambiar la suscripción o administrar el espacio de nombres, seleccione **ACTIVE DIRECTORY** en el panel de navegación izquierdo y, a continuación, seleccione el espacio de nombres. La barra de tareas enumera las opciones.

Haciendo clic en **Administrar** , se abre el Portal de administración de Control de acceso. En el Portal de administración de Control de acceso, BizTalk Service usa **las identidades de servicio**:  
![Portal de administración de Control de identidades de servicio ACS en el acceso][ACSServiceIdentities]

La identidad de servicio de Control de acceso es un conjunto de credenciales que permiten a aplicaciones o clientes para autenticar directamente con Control de acceso y recibir un token.

> [AZURE.IMPORTANT]BizTalk Service usa **propietario** la identidad predeterminada del servicio y el valor de la **contraseña** . Si usa el valor de clave simétrica en lugar del valor de la contraseña, puede producirse el siguiente error.<br/><br/>*No se puede conectar a la cuenta de servicio de administración de Control de acceso con las credenciales especificadas*

[Administrar su Namespace de ACS](https://msdn.microsoft.com/library/azure/hh674478.aspx) enumeran algunas consideraciones y recomendaciones.

## <a name="requirements-explained"></a>Requisitos de explicación

Estos requisitos no se aplican a la edición gratuita.
<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Lo que necesita</strong></td>
        <td><strong>¿Por qué necesita</strong></td>
</tr>
<tr>
<td>Suscripción de Azure</td>
<td>La suscripción determina quién puede iniciar sesión en el portal de Azure. El titular que crea la suscripción en <a HREF="https://account.windowsazure.com/Subscriptions">Suscripciones de Azure</a>.
<br/><br/>
La cuenta de Azure puede tener varias suscripciones y puede administrarse por cualquier persona que está permitido. Por ejemplo, el titular de la cuenta de Azure crea una suscripción denominada <em>BizTalkServiceSubscription</em> y ofrece a los administradores de BizTalk dentro de su empresa (por ejemplo, ContosoBTSAdmins@live.com) acceso a esta suscripción. En este escenario, los administradores de BizTalk inicie sesión en el portal de Azure y tiene derechos de administrador total a todos los servicios alojados en la suscripción, incluidos los servicios de BizTalk de Azure. Los administradores de BizTalk no son los propietarios de la cuenta de Azure y, por tanto, no tiene acceso a cualquier información de facturación.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Administrar suscripciones y cuentas de almacenamiento en el portal de Azure</a> proporciona más información.
</td>
</tr>
<tr>
<td>Base de datos SQL Azure</td>
<td>Almacena las tablas, vistas y procedimientos almacenados utilizados por la BizTalk Service, incluidos los datos de seguimiento.
<br/><br/>
Cuando se crea un BizTalk Service, puede usar un servidor de SQL Azure existente, base de datos de SQL Azure, o crear automáticamente un servidor o base de datos nueva.
<br/><br/>
La escala de la base de datos SQL se configura automáticamente. Normalmente, la escala predeterminada es suficiente para un BizTalk Service. Cambiar la escala afecta a precios. Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">cuentas y facturación de base de datos SQL Azure</a>
<br/><br/>
<strong>Notas</strong>
<br/>
<ul>
<li> Cuando se crea un nuevo servidor de SQL Azure y base de datos, servicios de Azure se habilita automáticamente. BizTalk Service requiere servicios de Azure habilitarse.</li>
<li>Si crea una nueva base de datos de SQL Azure en un servidor de SQL Azure existente, no se cambian las reglas de firewall del servidor. Como resultado, es posible que otros servicios de Azure no tienen acceso a bases de datos del servidor.</li>
</ul>
</td>
</tr>
<tr>
<td>Espacio de nombres de Control de acceso Azure</td>
<td>Autentica con servicios de Azure BizTalk. Cuando se implementa un proyecto de BizTalk Service desde Visual Studio, escriba este espacio de nombres de Control de acceso. Cuando se crea un BizTalk Service, se crea automáticamente el espacio de nombres de Control de acceso.</td>
</tr>

<tr>
<td>Cuenta de almacenamiento de Azure</td>
<td>Proporciona acceso a tablas, BLOB y colas utilizadas su BizTalk Service para guardar las siguientes acciones:

<ul>
<li>Archivos de registro que supervisan BizTalk Service. Los resultados de la supervisión también se muestran en la ficha **supervisión** en el portal de Azure.</li>
<li>Al crear un X12 o contrato AS2 entre socios, puede habilitar la característica de archivado almacenar las propiedades del mensaje. Estos datos se guardan en la cuenta de almacenamiento.</li>
</ul>
<br/>
Cuando se crea un BizTalk Service, puede usar una cuenta de almacenamiento existente o crear automáticamente una nueva cuenta de almacenamiento.
<br/><br/>
La configuración predeterminada de almacenamiento es suficiente para un BizTalk Service.
<br/><br/>
Cuando se crea una cuenta de almacenamiento, se crean automáticamente una clave principal y clave secundaria. Estas teclas controlan el acceso a su cuenta de almacenamiento. BizTalk Service usa automáticamente la clave principal.
<br/><br/>
Para obtener más información, vea <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">almacenamiento</a> .
</td>
</tr>

<tr>
<td>Certificado SSL privada</td>
<td>
Cuando se crea un servicio de BizTalk de Azure, también se crea una dirección URL de HTTPS que incluye su nombre de BizTalk Service. Esta dirección URL se configura automáticamente para usar un certificado autofirmado de desarrollo de solo lectura. Para producción, es necesario un certificado SSL privado.
<br/><br/>
<strong>Información del certificado SSL importantes</strong>

<ul>
<li>La fecha de expiración del certificado debe ser inferior a 5 años.</li>
<li>Todos los certificados privados requieran una contraseña. Conozca la contraseña y como práctica recomendada, compartir esta contraseña con los administradores.</li>
<li>Certificados firmados se usan en un entorno de prueba y desarrollo. Cuando se usa certificados firmados, importar el certificado en el almacén de certificados personales y el almacén de certificados de entidades emisoras de certificados raíz de confianza.</li>
</ul>
<br/>Cuando envíe la solicitud de certificado de producción a la entidad emisora de certificados, dé a las siguientes propiedades de certificado:
<br/>

<ul>
<li><strong>Uso de clave mejorado</strong>: como mínimo, Azure BizTalk Services requiere autenticación de servidor.</li>
<li><strong>Nombre común</strong>: escriba el nombre de dominio completo (FQDN) de la dirección URL de servicio de Azure BizTalk. Consulte <a HREF="#BizTalk">crear un servicio de BizTalk</a> en este artículo.</li>
</ul>
<br/>
Una vez creado el BizTalk Service pueden agregarse un certificado nuevo o diferente.
</td>
</tr>
</table>



## <a name="hybrid-connections"></a>Conexiones de híbrido

Cuando se crea un servicio de BizTalk Azure, está disponible la pestaña **Conexiones híbrido** :

![Pestaña de conexiones híbrido][HybridConnectionTab]

Conexiones de híbrido se usan para conectarse a un sitio Web de Azure o el servicio móvil de Azure a cualquier recurso local que usa un puerto TCP estático, como SQL Server, MySQL, API de Web HTTP, servicios móviles y mayoría de los servicios Web personalizados.  Híbrido conexiones y el servicio de adaptador BizTalk son diferentes. El servicio de adaptador BizTalk se usa para conectarse a servicios de Azure BizTalk a un sistema de línea de negocio (LOB) local.

 Vea [Conexiones híbrido](integration-hybrid-connection-overview.md) para obtener más información, como crear y administrar conexiones híbrido.


## <a name="next-steps"></a>Pasos siguientes

Ahora que se ha creado un BizTalk Service, familiarícese con las diferentes [servicios BizTalk: fichas de paneles, el Monitor y escala](biztalk-dashboard-monitor-scale-tabs.md). Su BizTalk Service está listo para las aplicaciones. Para empezar a crear aplicaciones, vaya a [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Vea también
- [Servicios de BizTalk: Gráfico de ediciones](biztalk-editions-feature-chart.md)<br/>
- [Servicios de BizTalk: Gráfico de estado](biztalk-service-state-chart.md)<br/>
- [Servicios de BizTalk: Copia de seguridad y restauración](biztalk-backup-restore.md)<br/>
- [Servicios de BizTalk: límite](biztalk-throttling-thresholds.md)<br/>
- [Servicios de BizTalk: Nombre del emisor y la clave del emisor](biztalk-issuer-name-issuer-key.md)<br/>
- [¿Cómo puedo comenzar a utilizar el SDK de servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
- [Conexiones de híbrido](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
