<properties
    pageTitle="Introducción a las conexiones híbrido | Microsoft Azure"
    description="Obtenga información sobre las conexiones de híbridos, seguridad, puertos TCP y configuraciones compatibles. MABS, WABS."
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
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="ccompy"/>


# <a name="hybrid-connections-overview"></a>Introducción a las conexiones híbrido
Introducción a las conexiones híbrido, enumera las configuraciones admitidas y los puertos TCP necesarios.


## <a name="what-is-a-hybrid-connection"></a>¿Qué es una conexión híbrido

Conexiones de híbridos son una característica de Azure BizTalk Services. Conexiones híbrido proporcionan una forma fácil y conveniente para conectar la característica de aplicaciones Web de servicio de aplicación de Azure (anteriormente sitios Web) y la característica de aplicaciones móviles en el servicio de aplicación de Azure (anteriormente Mobile Services) a los recursos locales detrás del firewall.

![Conexiones de híbrido][HCImage]

Beneficios de conexiones híbrido incluyen:

- Aplicaciones Web y aplicaciones móviles pueden acceder a existente datos locales y servicios de forma segura.
- Varias aplicaciones Web o aplicaciones móviles pueden compartir una conexión híbrido para tener acceso a un recurso local.
- Mínimo puertos TCP son necesarios para acceder a su red.
- Aplicaciones con conexiones híbrido acceso a solo el recurso local específica que se publica a través de la conexión híbrido.
- Puede conectarse a cualquier recurso local que usa un puerto TCP estático, como SQL Server, MySQL, HTTP Web API y mayoría de los servicios Web personalizados.

    > [AZURE.NOTE] Servicios basados en TCP que usan puertos dinámicos (como modo FTP pasivo o modo pasivo extendido) no son compatibles actualmente. LDAP también es incompatible. LDAP utiliza un puerto TCP estático, pero también puede utilizar UDP. Por tanto, no es compatible.

- Puede utilizarse con todos los entornos compatibles con Web Apps (. NET, PHP, Java, Python, Node.js) y aplicaciones de Mobile (Node.js, .NET).
- Aplicaciones Web y aplicaciones móviles pueden acceder a los recursos locales en la misma manera como si se encuentra la Web o la aplicación Mobile en su red local. Por ejemplo, la misma conexión cadena utilizada local puede usarse en Azure.


Híbrido conexiones también proporcionan administradores de la empresa con el control y visibilidad de los recursos corporativos tiene acceso a aplicaciones híbridas, incluidos:

- Configuración de la directiva de grupo, los administradores pueden permitir conexiones híbrido en la red y también designar recursos que se pueden acceder por aplicaciones híbridas.
- Registros de auditoría y eventos en la red corporativa proporcionan visibilidad de los recursos que se tiene acceso a las conexiones de híbrido.


## <a name="example-scenarios"></a>Escenarios de ejemplo

Las siguientes combinaciones de aplicación y framework es compatible con conexiones híbrido:

- Acceso de .NET framework para SQL Server
- Acceso de .NET framework para servicios HTTP/HTTPS con WebClient
- Acceso PHP a SQL Server, MySQL
- Acceso de Java a SQL Server, MySQL y Oracle
- Acceso de Java a los servicios HTTP/HTTPS

Al utilizar conexiones híbrido para tener acceso a la implementación local de SQL Server, considere lo siguiente:

- SQL Express denominada instancias debe estar configurado para utilizar puertos estáticos. De forma predeterminada, SQL Express instancias con nombre usar puertos dinámicos.
- SQL Express predeterminado instancias utiliza un puerto estático, pero debe estar habilitado TCP. De forma predeterminada, TCP no está habilitado.
- Cuando se utiliza clústeres o grupos de disponibilidad, el `MultiSubnetFailover=true` modo no es compatible actualmente.
- La `ApplicationIntent=ReadOnly` no es compatible actualmente.
- Autenticación de SQL puede ser necesario realizar el método de autorización de llevar a cabo compatible con la aplicación de Azure y SQL server local.


## <a name="security-and-ports"></a>Seguridad y puertos

Conexiones de híbrido utilizan autorización de firma de acceso compartido (SA) para proteger las conexiones de las aplicaciones de Azure y el Administrador de conexión de implementación híbrida local a la conexión híbrido. Se crean claves de conexión independiente para la aplicación y el Administrador de conexión local híbrido. Estas teclas de conexión pueden pasadas y revocadas de forma independiente.

Conexiones híbrido proporcionan distribución transparente y segura de las claves para las aplicaciones y el Administrador de conexión local híbrido.

Consulte [crear y administrar conexiones híbrido](integration-hybrid-connection-create-manage.md).

*Autorización de la aplicación es independiente de la conexión híbrido*. Puede utilizarse cualquier método de autenticación apropiado. El método de autorización depende de los métodos de autorización de llevar a cabo compatibles a través de la nube de Azure y los componentes de local. Por ejemplo, la aplicación de Azure tiene acceso a un servidor de SQL en local. En este escenario, autorización de SQL puede ser el método de autorización es compatible to-end.

#### <a name="tcp-ports"></a>Puertos TCP
Conexiones de híbrido requieren conectividad saliente de TCP o HTTP desde la red privada. No es necesario abrir los puertos del firewall o cambiar la configuración de perímetro de la red para permitir que cualquier conectividad entrante en la red.

Conexiones de híbrido usa los siguientes puertos TCP:

Puerto | ¿Por qué necesita
--- | ---
9350 - 9354 | Estos puertos se utilizan para la transmisión de datos. El Administrador de retransmisión de Bus de servicio sondeos puerto 9350 para determinar si está disponible la conectividad de TCP. Si está disponible, se supone que el puerto 9352 también está disponible. Tráfico de datos pase por el puerto 9352. <br/><br/>Permitir conexiones salientes a estos puertos.
5671 | Cuando se utiliza el puerto 9352 para el tráfico de datos, puerto 5671 se usa como el canal de control. <br/><br/>Permitir conexiones salientes a este puerto.
80, 443 | Estos puertos se utilizan para algunas peticiones de datos en Azure. Además, si no se puede utilizar puertos 9352 y 5671, *a continuación,* los puertos 80 y 443 son los puertos reserva usados para el canal de control y la transmisión de datos.<br/><br/>Permitir conexiones salientes a estos puertos. <br/><br/>**Nota** No se recomienda usar estos como los puertos reserva en lugar de los demás puertos TCP. El HTTP/WebSocket se usa como el protocolo en lugar de TCP nativo de canales de datos. Podría provocar un rendimiento inferior.



## <a name="next-steps"></a>Pasos siguientes

[Crear y administrar conexiones híbrido](integration-hybrid-connection-create-manage.md)<br/>
[Conectar aplicaciones Web Azure a un recurso local](../app-service-web/web-sites-hybrid-connection-get-started.md)<br/>
[Conectarse a la implementación local de SQL Server desde una aplicación web de Azure](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/>


## <a name="see-also"></a>Vea también

[API de REST de administración de servicios de BizTalk en Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
[servicios BizTalk: ediciones gráfico](biztalk-editions-feature-chart.md)<br/>
[Crear un BizTalk Service con Azure portal](biztalk-provision-services.md)<br/>
[Servicios de BizTalk: Fichas de paneles, el Monitor y la escala](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
