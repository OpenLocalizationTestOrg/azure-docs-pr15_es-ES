<properties 
    pageTitle="Uso del Administrador de conexión híbrido | Microsoft Azure" 
    description="Instalar y configurar el Administrador de conexión híbrido para conectarse a los conectores locales en las aplicaciones de lógica" 
    services="app-service\logic" 
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/>

# <a name="connect-to-on-premises-connectors-using-the-hybrid-connection-manager"></a>Conectar con conectores local mediante el Administrador de conexión híbrido

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2014 12-01-vista previa de aplicaciones de lógica. Lógica aplicaciones de la disponibilidad general (GA), utiliza una puerta de enlace para conectividad local. Más información sobre la nueva [puerta de enlace](app-service-logic-gateway-connection.md) y [GA de lógica de aplicaciones](https://azure.microsoft.com/documentation/services/logic-apps/).

Para usar un sistema local, aplicaciones de lógica utiliza el Administrador de conexión de híbrido. Algunos conectores pueden conectarse a un sistema local, como SQL Server, SAP, SharePoint y así sucesivamente. 

Administrador de conexión de híbrido (HCM) es un clic-instalador de una vez que se instala en un servidor IIS dentro de su red, tras el firewall. Usar una retransmisión de Bus de servicio de Azure, gestión RR. autentica el sistema local con el conector en Azure. 

> [AZURE.NOTE] Administrador de conexiones de híbrido se requiere solo si se está conectando a un recurso local detrás del firewall. Si no se conecta a un sistema local, no necesita el Administrador de conexión híbrido.

Para empezar, debe:

- Azure Bus de servicio retransmisión nombres SA cadena de conexión. Ver [Tarifas de Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/) para determinar qué nivel incluye transmisiones.
- Inicio de sesión de información del sistema, incluyendo el nombre de usuario y contraseña locales. Por ejemplo, si se está conectando a un servidor de SQL en local, debe la cuenta de inicio de sesión de SQL Server y la contraseña.
- Información del servidor, incluido el puerto número y nombre de servidor local. Por ejemplo, si se está conectando a un servidor local SQL Server, necesita el nombre de SQL Server y el número de puerto TCP.

## <a name="get-the-service-bus-connection-string"></a>Obtener la cadena de conexión de Bus de servicio

En el portal de Azure, copie la cadena de conexión SA de raíz de Bus de servicio. Esta cadena de conexión que conecta el conector de Azure a su sistema local. 

1. En el [portal de clásica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885), seleccione el espacio de nombres de Bus de servicio y seleccione **Información de conexión**:

    ![][SB_ConnectInfo]

2. Copie la cadena de conexión SA:

    ![][SB_SAS]

## <a name="install-the-hybrid-connection-manager"></a>Instalar al administrador de conexión híbrido

1. En el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), seleccione el conector que ha creado. Para abrirlo, seleccione **Examinar**, seleccione **API de aplicaciones**y, a continuación, seleccione el conector o API App. 
<br/><br/>
En **Conexión híbrido**, el programa de instalación está **incompleta**:
<br/>
![][2] 

2. Seleccione la **conexión de híbrido**. Se muestra la cadena de conexión de Bus de servicio que haya escrito previamente.
3. Copie la **cadena de configuración principal**:
<br/>
![][PrimaryConfigString]

4. En **El Administrador de conexión de local híbrido**, puede descargar el Administrador de conexión híbrido o instalar directamente desde el portal. 
<br/><br/>
Para instalar directamente desde el portal, vaya a su servidor IIS local, busque en el portal y seleccione **descargar y configurar**.
<br/><br/>
Para descargar el Administrador de conexión híbrido, vaya a su servidor IIS local y vaya a la **aplicación ClickOnce** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application). La instalación se inicia automáticamente para que pueda ejecutar.

5. En la ventana de **Configuración de escucha** , escriba la **Cadena de configuración principal** que pegó anteriormente (en el paso 3) y seleccione **instalar**.

Cuando la configuración está completa, el siguiente muestra:
<br/>
![][3] 

Ahora cuando examine el conector nuevo, el estado de conexión híbrido está **conectado**. Es posible que el conector de cerrar y volver a abrirlo:
<br/>
![][4] 

> [AZURE.NOTE] Para cambiar a la cadena de conexión secundaria, vuelva a ejecutar la configuración de conexión híbrido y escriba la **Cadena de configuración secundario**.


## <a name="tcp-ports-and-security"></a>Puertos TCP y seguridad

Cuando se crea una conexión híbrido, se crea un sitio Web en su servidor IIS local en local. El servidor IIS puede estar en una DMZ. Se incluyen los requisitos de puerto TCP en el servidor IIS:

Puerto TCP | ¿Por qué
--- | ---
 | No hay puertos TCP entrantes son necesarios.
9350 - 9354 | Estos puertos se utilizan para la transmisión de datos. El Administrador de retransmisión de Bus de servicio sondeos puerto 9350 para determinar si está disponible la conectividad de TCP. Si está disponible, se supone que el puerto 9352 también está disponible. Tráfico de datos pase por el puerto 9352. <br/><br/>Permitir conexiones salientes a estos puertos.
5671 | Cuando se utiliza el puerto 9352 para el tráfico de datos, puerto 5671 se usa como el canal de control. <br/><br/>Permitir conexiones salientes a este puerto. 
80, 443 | Si no se puede utilizar puertos 9352 y 5671, *a continuación,* los puertos 80 y 443 son los puertos reserva usados para el canal de control y la transmisión de datos.<br/><br/>Permitir conexiones salientes a estos puertos.
Puerto del sistema local | En el sistema local, abra el puerto usado por el sistema. Por ejemplo, SQL Server usa normalmente el puerto 1433. Abra este puerto TCP.

[Detrás de un Firewall con Bus de servicio de hospedaje](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## <a name="troubleshooting"></a>Solución de problemas

![][HCMFlow]

### <a name="on-premises-troubleshooting"></a>Solución de problemas en local

1. En el servidor IIS, confirme el rol web IIS está instalado y se inician los servicios IIS.
2. En el servidor IIS, confirme el Administrador de conexión híbrido está instalado y ejecutándose:
 - En el Administrador de IIS (inetmgr), el sitio Web de ***MicrosoftAzureBizTalkHybridListener*** debe aparecer y ejecutar. 
 - Este sitio Web usa ***HybridListenerAppPool*** que se ejecuta como la cuenta de usuario locales integradas *NetworkService* . También se debe iniciar este AppPool.
3. En el servidor IIS, confirme que el conector se instala y ejecuta: 
 - Se crea un sitio Web para su conector. Por ejemplo, si ha creado un conector SQL, es un sitio Web de ***MicrosoftSqlConnector_nnn*** . En el Administrador de IIS (inetmgr), confirme que este sitio Web se enumeran y se inicia. 
 - Este sitio Web utiliza su propio grupo de aplicaciones de IIS denominado ***HybridAppPoolnnn***. Este AppPool se ejecuta como la cuenta de usuario locales integradas *NetworkService* . Deben ser iniciado este sitio Web y AppPool. 
 - Busque el conector local. Por ejemplo, si su sitio Web de conector utiliza el puerto 6569, vaya a http://localhost:6569. Un documento predeterminado no está configurado para un `HTTP Error 403.14 - Forbidden error` se espera.
4. En el firewall, compruebe los puertos TCP que se muestran en este tema están abiertos.
5. Mire el sistema de origen o de destino:
 - Algunos sistemas locales requieren archivos de dependencia adicional. Por ejemplo, si se está conectando a SAP local, deben estar instalados en el servidor IIS algunos archivos adicionales de SAP.
 - Compruebe la conectividad con el sistema con la cuenta de inicio de sesión. Por ejemplo, el puerto TCP usado por el sistema debe estar abierto, como el puerto 1433 para SQL Server. La cuenta de inicio de sesión especificada en el portal de Azure debe tener acceso al sistema.
6. En el servidor IIS, compruebe los registros de eventos para los errores. 
7. Liberador de espacio y vuelva a instalar el Administrador de conexión híbrido: 
 - En IIS, elimine manualmente el sitio Web de conector y su grupo de aplicaciones. 
 - Vuelva a ejecutar el Administrador de conexión híbrido y confirme que se introduce la correcta **Principal cadena de configuración** para el conector.



### <a name="in-the-azure-classic-portal"></a>En el portal de clásico de Azure

1. Confirme que el espacio de nombres de Bus de servicio tiene un estado **activo** .
2. Al crear el conector, escriba la cadena de conexión SA de Bus de servicio. No escriba la cadena de conexión de ACS.


## <a name="faq"></a>Preguntas más frecuentes

**Pregunta**: existen dos administradores de conexión híbrido. ¿Cuál es la diferencia? 

**Respuesta**: hay la tecnología de [Conexiones híbrido](../biztalk-services/integration-hybrid-connection-overview.md) que utiliza principalmente Web Apps (anteriormente sitios Web) y aplicaciones de Mobile (servicios móviles anteriormente) para conectarse a local. El Administrador de conexiones de híbrido es su propia [configuración](../biztalk-services/integration-hybrid-connection-create-manage.md) y usa un servicio de BizTalk Azure (segundo plano). Admite protocolos de TCP y HTTP.

Con conectores de servicio de la aplicación de Azure, también tenemos un administrador de conexión híbrido.  ¿Es este administrador de conexión híbrido *no* usar un BizTalk Azure del servicio (segundo plano) y es compatible con más de los protocolos TCP y HTTP. Vea la [lista de aplicaciones de API y conectores](app-service-logic-connectors-list.md).

Ambos utilizan Bus de servicio de Azure para conectarse al sistema local.

**Pregunta**: cuando se crea un App API personalizado, ¿puedo usar el Administrador de conexión de aplicación de servicio híbrido para conectarse a local? 

**Respuesta**: no está en el sentido tradicional. Puede utilizar un conector integrado, configurar el Administrador de conexión de aplicación de servicio híbrido para conectarse al sistema local. A continuación, use este conector con su App API personalizado, posiblemente mediante una aplicación lógica. Actualmente, no puede desarrollar o cree su propio híbrido App API (por ejemplo, el conector SQL o el conector de archivo).

Si la API personalizada utiliza un puerto TCP o HTTP, puede usar [Conexiones híbrido](../biztalk-services/integration-hybrid-connection-overview.md) y su administrador de conexión híbrido. En este escenario, se utiliza un servicio de BizTalk de Azure. Puede ayudarle a [conectarse a la implementación local de SQL Server desde una aplicación web](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) .  


## <a name="read-more"></a>Obtener más información

[Supervisar las aplicaciones de lógica](app-service-logic-monitor-your-logic-apps.md)<br/>
[Precios de Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 
