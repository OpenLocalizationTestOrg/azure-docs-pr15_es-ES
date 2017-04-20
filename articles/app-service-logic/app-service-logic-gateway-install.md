<properties
   pageTitle="Aplicaciones de lógica instalación puerta de enlace de datos local | Microsoft Azure"
   description="Obtener información sobre cómo instalar la puerta de enlace de datos local para su uso en una aplicación de lógica."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="install-the-on-premises-data-gateway-for-logic-apps"></a>Instalar la puerta de enlace de datos local para las aplicaciones de lógica

## <a name="installation-and-configuration"></a>Instalación y configuración

### <a name="prerequisites"></a>Requisitos previos

Mínimo:

* 4.5 de .NET framework
* versión de 64 bits de Windows 7 o Windows Server 2008 R2 (o posterior)

Recomendado:

* CPU núcleo 8
* 8 GB de memoria
* versión de 64 bits de Windows 2012 R2 (o posterior)

Consideraciones relacionadas:

* No puede instalar una puerta de enlace en un controlador de dominio.
* No debe instalar una puerta de enlace en un equipo portátil que puede estar desactivado, dormido, o no está conectado a Internet porque no se puede ejecutar la puerta de enlace en cualquiera de las circunstancias. Además, puede verse afectado el rendimiento de puerta de enlace en una red inalámbrica.

### <a name="install-a-gateway"></a>Instalar una puerta de enlace

Puede obtener el [Instalador de gateway datos locales aquí](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

Especificar la **puerta de enlace de datos local** como modo, inicie sesión con su trabajo o escuela cuenta y, a continuación, configurar una nueva puerta de enlace o migrar, restaurar o tomar el control de una puerta de enlace existente.

* Para configurar una puerta de enlace, escriba un **nombre** para él y una **clave de recuperación**, haga clic o puntee en **Configurar**.

    Especifique una clave de recuperación que contiene al menos ocho caracteres y mantener en un lugar seguro. Necesitará esta clave si desea migrar, restaurar o tomar el control de la puerta de enlace.

* Para migrar, restaurar o tomar el control de una puerta de enlace existente, introduzca la clave de recuperación que se especificó cuando se creó la puerta de enlace.

### <a name="restart-the-gateway"></a>Reiniciar la puerta de enlace

La puerta de enlace se ejecuta como un servicio de Windows y, al igual que con cualquier otro servicio de Windows, puede iniciar y detener de diferentes maneras. Por ejemplo, puede abrir un símbolo del sistema con privilegios elevados permisos en el equipo donde se ejecuta la puerta de enlace y, a continuación, ejecute cualquiera de estos comandos:

* Para detener el servicio, ejecute este comando:

    `net stop PBIEgwService`

* Para iniciar el servicio, ejecute este comando:

    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>Configurar un servidor proxy o firewall

Para obtener información sobre cómo proporcionar información de proxy de la puerta de enlace, vea [establecer la configuración de proxy](https://powerbi.microsoft.com/en-us/documentation/powerbi-gateway-proxy/).

Puede comprobar si el servidor proxy o firewall puede estar bloqueando las conexiones ejecutando el siguiente comando desde un símbolo del sistema de PowerShell. Esto probar la conectividad con el Bus de servicio de Azure. Este sólo las pruebas de conectividad de red y no tiene consecuencias sobre el servicio de nube o la puerta de enlace. Le ayuda a determinar si su equipo puede obtener realmente a internet.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

Los resultados deberían ser similares a este ejemplo. Si no se cumple **TcpTestSucceeded** , puede estar bloqueado por un firewall.

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Si desea que sean la exhaustiva, sustituir los valores **nombreDeEquipo** y **puerto** con las que aparecen en [Configurar puertos](#configure-ports) más adelante en este tema.

El firewall también está bloqueando las conexiones que hace que el Bus de servicio de Azure a los centros de datos de Azure. Si es así, deseará blanca (desbloquear) todas las direcciones IP de su región para los centros de datos. Puede obtener una lista de [direcciones IP Azure aquí](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="configure-ports"></a>Configurar los puertos

La puerta de enlace crea una conexión saliente con Bus de servicio de Azure. Se comunica en puertos de salida: 443 TCP (predeterminado), 5671, 5672, 9350 hasta 9354. La puerta de enlace no requiere puertos de entrada.

Más información acerca de las [soluciones de implementación híbrida](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMBRES DE DOMINIO | PUERTOS DE SALIDA | DESCRIPCIÓN |
| ----- | ------ | ------ |
| *. analysis.windows.net | 443 | HTTPS |
| *. login.windows.net | 443 | HTTPS |
| *. servicebus.windows.net |5671 5672 | Mensaje avanzada Queue protocolo (AMQP) |
| *. servicebus.windows.net | 443, 9350 9354 | Escucha en servicio de retransmisión de Bus sobre TCP (requiere 443 para adquisición de token de Control de acceso) |
| *. frontend.clouddatahub.net | 443 | HTTPS |
| *. Core.Windows.NET. | 443 | HTTPS |
| ://Login.microsoftonline.com) | 443 | HTTPS |
| *. msftncsi.com | 443 | Se usa para probar la conectividad de internet si la puerta de enlace no es accesible por el servicio de Power BI. |

Si necesita direcciones IP de lista de notas en lugar de los dominios, puede descargar y usar la [lista de intervalos de IP de centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). En algunos casos, las conexiones de Bus de servicio de Azure se realizará con la dirección IP en lugar de los nombres de dominio completo.

### <a name="sign-in-account"></a>Cuenta de inicio de sesión

Los usuarios sesión ya sea un trabajo o escuela cuenta. Esta es la cuenta de la organización. Si dispone de una oferta de Office 365 y no ha proporcionado el correo electrónico de trabajo real, puede aspecto jeff@contoso.onmicrosoft.com. Su cuenta, dentro de un servicio de nube, se almacena en un inquilino de Azure Active Directory (AAD). En la mayoría de los casos, UPN de la cuenta AAD se coincide con la dirección de correo electrónico.

### <a name="windows-service-account"></a>Cuenta de servicio de Windows

La puerta de enlace de datos local está configurado para usar las credenciales de inicio de sesión de servicio de Windows NT SERVICE\PBIEgwService. De forma predeterminada, tiene el derecho de registro en como un servicio. Se trata en el contexto de la máquina en la que va a instalar la puerta de enlace.

No es la cuenta utilizada para conectarse a orígenes de datos locales o la cuenta del trabajo o escuela con la que iniciar sesión en servicios de nube.

##<a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="general"></a>General

**Pregunta**: ¿qué orígenes de datos admite la puerta de enlace?<br/>
**Respuesta**: esta fecha, SQL Server.

**Pregunta**: ¿necesita una puerta de enlace para orígenes de datos en la nube como SQL Azure? <br/>
**Respuesta**: no. Una puerta de enlace se conecta a solo los orígenes de datos locales.

**Pregunta**: ¿qué es el servicio de Windows real denominado?<br/>
**Respuesta**: en servicios, la puerta de enlace se denomina servicio de puerta de enlace de Power BI empresarial.

**Pregunta**: ¿hay todas las conexiones entrantes a la puerta de enlace de la nube? <br/>
**Respuesta**: no. La puerta de enlace utiliza conexiones salientes a Bus de servicio de Azure.

**Pregunta**: ¿bloquear conexiones salientes? ¿Qué debo abrir? <br/>
**Respuesta**: vea los puertos y hosts que utiliza la puerta de enlace.


**Pregunta**: ¿tiene la puerta de enlace esté instalado en el mismo equipo que el origen de datos? <br/>
**Respuesta**: no. La puerta de enlace se conectará al origen de datos con la información de conexión que se proporcionó. Piense en la puerta de enlace como una aplicación de cliente en este sentido. ¿Se necesita puedan conectar con el nombre del servidor que se proporcionó.


**Pregunta**: ¿qué es la latencia para ejecutar consultas a un origen de datos de la puerta de enlace? ¿Qué es la mejor arquitectura? <br/>
**Respuesta**: para reducir la latencia de red, instale la puerta de enlace lo más cerca del origen de datos como sea posible. Si puede instalar la puerta de enlace en el origen de datos real, minimizará la latencia introducida. Tenga en cuenta también los centros de datos. Por ejemplo, si el servicio está utilizando el centro de datos de Estados Unidos oeste y tiene SQL Server alojado en una máquina virtual de Azure, desea tener la máquina virtual de Azure en Estados Unidos oeste también. Esto Minimizar latencia y evitar gastos de salida de la máquina virtual de Azure.


**Pregunta**: ¿existen requisitos de ancho de banda de red? <br/>
**Respuesta**: se recomienda tenga buen rendimiento para la conexión de red. Cada entorno es diferente, y la cantidad de datos que se envían afectará a los resultados. Usar ExpressRoute puede ayudar a garantizar un nivel de rendimiento entre los centros de datos de Azure y local.

Puede usar la aplicación de prueba de velocidad de Azure herramienta de terceros para ayudar a medir el rendimiento que es.


**¿Pregunta**: puede ejecutar la puerta de enlace de servicio de Windows con una cuenta de Azure Active Directory? <br/>
**Respuesta**: no. El servicio de Windows debe tener una cuenta de Windows válida. De forma predeterminada, se ejecutará con el SID de servicio, NT SERVICE\PBIEgwService.


**Pregunta**: ¿cómo se envían los resultados a la nube? <br/>
**Respuesta**: para ello mediante el Bus de servicio de Azure. Para obtener más información, consulte cómo funciona.


**Pregunta**: ¿dónde se almacenan las credenciales? <br/>
**Respuesta**: las credenciales que especifica para un origen de datos se almacenan cifrados en el servicio de nube de puerta de enlace. Se descifran las credenciales en la puerta de enlace local.

### <a name="high-availabilitydisaster-recovery"></a>Disponibilidad alta/recuperación.

**Pregunta**: ¿existen planes para habilitar escenarios de alta disponibilidad con la puerta de enlace? <br/>
**Respuesta**: este es el plan, pero aún no tenemos una escala de tiempo.


**Pregunta**: ¿qué opciones están disponibles para recuperación? <br/>
**Respuesta**: puede usar la clave de recuperación para restaurar o mover una puerta de enlace. Cuando se instala la puerta de enlace, especifique la clave de recuperación.


**Pregunta**: ¿cuál es el beneficio de la clave de recuperación? <br/>
**Respuesta**: proporciona una manera de migrar o recuperar la configuración de puerta de enlace después de un desastre.

### <a name="troubleshooting"></a>Solución de problemas

**Pregunta**: ¿dónde están los registros de la puerta de enlace? <br/>
**Respuesta**: Ver herramientas más adelante en este tema.


**Pregunta**: ¿cómo se puede ver qué consultas que se envían al origen de datos local? <br/>
**Respuesta**: puede habilitar el seguimiento de la consulta, que incluye las consultas que se envían. Recuerde que debe cambiar al valor original cuando finalizado la solución de problemas. Salir de consulta seguimiento habilitado hará que los registros que sea mayor.

También puede mirar herramientas que tiene el origen de datos para las consultas de seguimiento. Por ejemplo, puede usar eventos extendido o el analizador de SQL para SQL Server y Analysis Services.

## <a name="how-the-gateway-works"></a>Funcionamiento de la puerta de enlace

Cuando un usuario interactúa con un elemento que está conectado a un origen de datos local:

1. El servicio de nube crea una consulta, junto con las credenciales cifradas del origen de datos y la consulta envía a la cola de la puerta de enlace procesar.
1. El servicio analiza la consulta e inserta la solicitud al Bus de servicio de Azure.
1. La puerta de enlace de datos local explora el Bus de servicio de Azure para solicitudes pendientes.
1. La puerta de enlace obtiene la consulta, descifra las credenciales y se conecta a los orígenes de datos con dichas credenciales.
1. La puerta de enlace, envía la consulta al origen de datos para la ejecución.
1. Los resultados se envían desde el origen de datos a la puerta de enlace y, a continuación, en el servicio de nube. El servicio utiliza los resultados.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="update-to-the-latest-version"></a>Actualizar a la versión más reciente

Cuando la versión de la puerta de enlace no está actualizada, puede tener una gran cantidad de problemas.  Es una buena práctica general para asegurarse de que se encuentra en la versión más reciente.  Si todavía no lo ha actualizado la puerta de enlace para un mes o más, es aconsejable instale la última versión de la puerta de enlace y vea si puede reproducir el problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users-"></a>Error: Error al agregar usuario al grupo. (Usuarios del registro de rendimiento de-2147463168 PBIEgwService)

Puede recibir este error si intenta instalar la puerta de enlace en un controlador de dominio que no es compatible. Debe implementar la puerta de enlace en un equipo que no sea un controlador de dominio.

## <a name="tools"></a>Herramientas

### <a name="collecting-logs-from-the-gateway-configurator"></a>Recopilar registros de la configuración de puerta de enlace

Puede recopilar varios registros de la puerta de enlace. Inicie siempre con los registros.

#### <a name="installer-logs"></a>Registros de instalador

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Registros de configuración

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Registros del servicio de puerta de enlace de empresa

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Registros de eventos

Los registros de Data Management Gateway y PowerBIGateway están presentes en la **aplicación y los registros de los servicios**.

### <a name="fiddler-trace"></a>Seguimiento de Fiddler

[Fiddler](http://www.telerik.com/fiddler) es una herramienta gratuita de Telerik que supervisa el tráfico HTTP.  Puede ver el fondo y cuarto de servicio con Power BI desde el equipo cliente. Puede mostrar a errores y otra información relacionada.

## <a name="next-steps"></a>Pasos siguientes
- [Crear una conexión local a aplicaciones de lógica](app-service-logic-gateway-connection.md)
- [Características de integración de Enterprise](app-service-logic-enterprise-integration-overview.md)
- [Conectores de aplicaciones de lógica](../connectors/apis-list.md)