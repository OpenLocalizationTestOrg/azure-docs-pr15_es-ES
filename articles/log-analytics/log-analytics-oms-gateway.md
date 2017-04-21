<properties
    pageTitle="Conectar dispositivos y equipos a OMS mediante la puerta de enlace de OMS | Microsoft Azure"
    description="Conectar los equipos de Operations Manager supervisados y dispositivos OMS administrados con la puerta de enlace de OMS para enviar datos al servicio de OMS cuando no tienen acceso a Internet."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>
<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="banders"/>

# <a name="connect-computers-and-devices-to-oms-using-the-oms-gateway"></a>Conectar dispositivos y equipos a OMS mediante la puerta de enlace de OMS

Este documento describe cómo los equipos de System Center Operations Manager SCOM supervisados y dispositivos administrados OMS pueden enviar datos al servicio de OMS cuando no tienen acceso a Internet. La puerta de enlace de OMS puede recopilar los datos y enviar al servicio de OMS en su nombre.

La puerta de enlace es un proxy de reenvío de HTTP que admite HTTP túnel utilizando el comando HTTP CONNECT. La puerta de enlace puede controlar hasta 2000 dispositivos OMS simultáneamente conectados cuando se ejecuta en una CPU principales de 4, 16 GB servidor que ejecuta Windows.

Por ejemplo, su empresa u organización grande podría tener servidores con conectividad de red pero que no tenga conexión a Internet. En otro ejemplo, es posible que tenga punto muchos de los dispositivos de venta con ningún medio de supervisión de directamente. Y en otro ejemplo, Operations Manager pueden utilizar la puerta de enlace de OMS como un servidor proxy. En estos ejemplos, la puerta de enlace de OMS puede transferir datos de los agentes que se instalan en estos servidores o dispositivos de punto de OMS.

En lugar de cada agente individual enviar datos directamente a OMS y requiere una conexión directa a Internet, todos los datos de agente en su lugar se envía a través de un equipo que tiene una conexión a Internet. Ese equipo es donde instalar y usar la puerta de enlace. En este escenario, puede instalar a agentes en los equipos donde desea recopilar datos. La puerta de enlace, a continuación, transfiere datos de los agentes a OMS directamente: la puerta de enlace no analizarlos datos que se transfieren.

Para supervisar la puerta de enlace de OMS y analizar el rendimiento o datos de evento para el servidor donde está instalado, debe instalar al agente OMS en el equipo donde también se instala la puerta de enlace.

La puerta de enlace debe tener acceso a Internet para enviar datos a OMS. Cada agente también tiene conectividad de red para la puerta de enlace para que los agentes automáticamente pueden transferir datos hacia y desde la puerta de enlace. Para obtener los mejores resultados, no instale la puerta de enlace en un equipo que también es un controlador de dominio.

Aquí es un diagrama que muestra el flujo de datos de agentes directos a OMS.

![diagrama de agente directo](./media/log-analytics-oms-gateway/direct-agent-diagram.png)

Aquí es un diagrama que muestra el flujo de datos de Operations Manager a OMS.

![Diagrama de Operations Manager](./media/log-analytics-oms-gateway/scom-mgt-server.png)

## <a name="install-the-oms-gateway"></a>Instalar la puerta de enlace OMS

Instalar esta puerta de enlace reemplaza las versiones anteriores de la puerta de enlace que ha instalado (reenviador de análisis de registro).

Requisitos previos: .net Framework 4.5, Windows Server 2012 R2 SP1 y anteriores

1. Descargue la última versión de la puerta de enlace de OMS desde el [Centro de descarga de Microsoft](http://download.microsoft.com/download/2/5/C/25CF992A-0347-4765-BD7D-D45D5B27F92C/OMS%20Gateway.msi).
2. Para iniciar la instalación, haga doble clic en **Gateway.msi OMS**.
3. En la página principal, **siguiente**.  
    ![Asistente de configuración de puerta de enlace](./media/log-analytics-oms-gateway/gateway-wizard01.png)
4. En la página Contrato de licencia, seleccione **acepto los términos del contrato de licencia** para aceptar el CLUF y, a continuación, en **siguiente**.
5. En la página de la dirección de puerto y proxy:
    1. Escriba el número de puerto TCP que se usará para la puerta de enlace. El programa de instalación, abrirá este número de puerto de firewall de Windows. El valor predeterminado es 8080.
    El rango válido del número de puerto es 1-65535. Si la entrada no se dividen en este intervalo, aparece un mensaje de error.
    2. De manera opcional, si el servidor donde está instalada la puerta de enlace debe usar a un servidor proxy, escriba la dirección del proxy donde se necesita la puerta de enlace para conectarse. Por ejemplo, `http://myorgname.corp.contoso.com:80` si está en blanco, la puerta de enlace intenta conectarse a Internet directamente. En caso contrario, la puerta de enlace se conecta al servidor proxy. Si su servidor proxy requiere autenticación, escriba su nombre de usuario y contraseña.
        ![Configuración de proxy del Asistente de puerta de enlace](./media/log-analytics-oms-gateway/gateway-wizard02.png)  
    3. Haga clic en **siguiente**
6. Si no tiene Microsoft Updates habilitado, aparecerá la página de Microsoft Update donde puede elegir habilitar Microsoft Updates. Realice una selección y, a continuación, haga clic en **siguiente**. En caso contrario, continúe con el paso siguiente.
7. En la página de la carpeta de destino, deje de carpeta predeterminada **%ProgramFiles%\OMS puerta de enlace** o escriba la ubicación donde desea instalar la puerta de enlace y, a continuación, haga clic en **siguiente**.
8. En la página preparado para instalar, haga clic en **instalar**. Control de cuentas de usuario puede aparecer solicitar permiso para instalar. Si es así, haga clic en **Sí**.
9. Cuando finalice la instalación, haga clic en **Finalizar**. Puede comprobar que el servicio se está ejecutando, abra el complemento services.msc y compruebe que la **Puerta de enlace de OMS** aparece en la lista de servicios.  
    ![Servicios de puerta de enlace OMS](./media/log-analytics-oms-gateway/gateway-service.png)

## <a name="install-an-agent-on-devices"></a>Instalar a un agente en dispositivos

Si es necesario, vea [Conectar Windows equipos para el análisis de registro](log-analytics-windows-agents.md) para obtener información sobre cómo instalar directamente conectados a agentes. Este artículo se describe cómo se puede instalar el agente mediante un asistente de configuración o mediante el uso de la línea de comandos.

## <a name="configure-oms-agents"></a>Configurar a agentes de OMS

Consulte [establecer la configuración del servidor proxy y el firewall con el agente de supervisión de Microsoft](log-analytics-proxy-firewall.md) para obtener información acerca de cómo configurar un agente para usar un servidor proxy, que es en este caso es la puerta de enlace.

Agentes Operations Manager envían algunos datos como alertas, evaluación de configuración, espacio de instancia y datos de capacidad, a través del servidor de administración de Operations Manager. Otros datos de gran volumen, como registros, rendimiento y seguridad IIS se envían directamente a la puerta de enlace de OMS. Vea [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md) para una lista completa de los datos que se envían a través de cada canal.

>[AZURE.NOTE]
Si tiene previsto usar la puerta de enlace con el equilibrio de carga de red, vea [configurar opcionalmente equilibrio de carga de red](#optionally-configure-network-load-balancing).

## <a name="configure-a-scom-proxy-server"></a>Configurar un servidor proxy SCOM

Configurar Operations Manager para agregar la puerta de enlace para que actúe como un servidor proxy. Al actualizar la configuración de proxy, la configuración de proxy se aplica automáticamente a todos los agentes informes Operations Manager.

Para usar la puerta de enlace para admitir Operations Manager, debe tener:

- Agente de supervisión de Microsoft (versión de agente- **8.0.10900.0** y posteriores) instalado en el servidor de puerta de enlace y configurado para las áreas de trabajo OMS con la que desea comunicarse.
- La puerta de enlace debe tener conexión a Internet o estar conectado a un servidor proxy que realiza.

### <a name="to-configure-scom-for-the-gateway"></a>Configurar SCOM para la puerta de enlace

1. Abra la consola de Operations Manager y en la **Serie de administración de operaciones**, haga clic en **conexión** y, a continuación, haga clic en **Configurar Proxy de servidor**:  
    ![Operations Manager: configurar el servidor Proxy](./media/log-analytics-oms-gateway/scom01.png)
2. Seleccione **usar un servidor proxy para tener acceso a la aplicación de gestión de operaciones** y, a continuación, escriba la dirección IP del servidor de puerta de enlace de OMS. Asegúrese de que comience con el `http://` prefijo:  
    ![Operations Manager: dirección del servidor proxy](./media/log-analytics-oms-gateway/scom02.png)
3. Haga clic en **Finalizar**. El servidor de Operations Manager está conectado a su área de trabajo OMS.

## <a name="configure-network-load-balancing"></a>Configurar el equilibrio de carga de red

Puede configurar la puerta de enlace para alta disponibilidad mediante la creación de un clúster de equilibrio de carga de red. El clúster administra el tráfico de su agentes redirigir las conexiones solicitadas desde los agentes de supervisión de Microsoft a través de sus nodos. Si un servidor de puerta de enlace se desconecta, el tráfico se redirige a otros nodos.

1. Abra el Administrador de equilibrio de carga de red y crear un clúster.
2. Haga clic en el clúster antes de agregar puertas de enlace y a continuación, seleccione **Propiedades de clúster.** Configurar el clúster para tener su propia dirección IP:  
    ![Administrador de equilibrio de carga: direcciones IP de red](./media/log-analytics-oms-gateway/nlb01.png)
3. Para conectar un servidor de puerta de enlace de OMS con el agente de supervisión de Microsoft instalado, haga clic en la dirección IP y, a continuación, haga clic en **Agregar Host al clúster**.  
    ![Carga de red equilibrio Manager: agregar Host al clúster](./media/log-analytics-oms-gateway/nlb02.png)
4. Escriba la dirección IP del servidor de la puerta de enlace que desea conectarse:  
    ![Administrador de equilibrio de carga: la red agregar Host clúster: conectar](./media/log-analytics-oms-gateway/nlb03.png)
5. En equipos que no tiene conexión a Internet, asegúrese de usar la dirección IP del clúster al configurar las **Propiedades del agente de supervisión de Microsoft**:  
    ![Propiedades de agente: la configuración de Proxy de supervisión de Microsoft](./media/log-analytics-oms-gateway/nlb04.png)

## <a name="configure-for-automation-hybrid-workers"></a>Configurar para los trabajadores de automatización híbrido

Si tiene los trabajadores de automatización híbrido en su entorno, los pasos siguientes proporcionan manuales, temporales alternativas para configurar la puerta de enlace para actualizarlos.

En los pasos siguientes, debe saber la región de Azure donde reside la cuenta de automatización. Para buscar la ubicación:

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. Seleccione el servicio de automatización de Azure.
3. Seleccione la cuenta de Azure automatización correspondiente.
4. Ver su región en la **ubicación**.  
    ![Portal de Azure: ubicación de la cuenta de automatización](./media/log-analytics-oms-gateway/location.png)

Utilice las tablas siguientes para identificar la dirección URL para cada ubicación:

**Direcciones URL de servicios de datos de trabajo en tiempo de ejecución**

| **ubicación** | **DIRECCIÓN URL** |
| --- | --- |
| Norte Central de EE. | ncus jobruntimedata ord su1.azure automation.net |
| Europa occidental | nos-jobruntimedata-ord-su1.azure-automation.net |
| Sur Central de EE. | scus jobruntimedata ord su1.azure automation.net |
| Estados Unidos oriental | eus jobruntimedata ord su1.azure automation.net |
| Canadá central | CC jobruntimedata ord su1.azure automation.net |
| Europa del Norte | ne jobruntimedata ord su1.azure automation.net |
| Sudeste asiático | mar jobruntimedata ord su1.azure automation.net |
| India central | CID jobruntimedata ord su1.azure automation.net |
| Japón | jpe jobruntimedata ord su1.azure automation.net |
| Australia | ase jobruntimedata ord su1.azure automation.net |

**Direcciones URL de servicios de agente**

| **ubicación** | **DIRECCIÓN URL** |
| --- | --- |
| Norte Central de EE. | ncus agentservice ord 1.azure automation.net |
| Europa occidental | nos-agentservice-ord-1.azure-automation.net |
| Sur Central de EE. | scus agentservice ord 1.azure automation.net |
| Estados Unidos oriental | eus2 agentservice ord 1.azure automation.net |
| Canadá central | CC agentservice ord 1.azure automation.net |
| Europa del Norte | ne agentservice ord 1.azure automation.net |
| Sudeste asiático | mar agentservice ord 1.azure automation.net |
| India central | CID agentservice ord 1.azure automation.net |
| Japón | jpe agentservice ord 1.azure automation.net |
| Australia | ase agentservice ord 1.azure automation.net |

Si su equipo está registrado como trabajador híbrido automáticamente para revisión con la solución de administración de actualizaciones, siga estos pasos:

1. Agregue las direcciones URL de servicio de datos en tiempo de ejecución de trabajo a la lista de Host permitidos de la puerta de enlace de OMS. Por ejemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Reinicie el servicio de puerta de enlace de OMS mediante el siguiente cmdlet de PowerShell:`Restart-Service OMSGatewayService`

Si su equipo es de-efectuar automatización de Azure mediante el cmdlet de registro de trabajo híbrido, siga estos pasos:

1. Agregue la URL de registro del servicio de agente a la lista de Host permitidos de la puerta de enlace de OMS. Por ejemplo:`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Agregue las direcciones URL de servicio de datos en tiempo de ejecución de trabajo a la lista de Host permitidos de la puerta de enlace de OMS. Por ejemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Reinicie el servicio de puerta de enlace OMS.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets de PowerShell útiles

Cmdlets puede ayudarle a completar las tareas necesarias para actualizar la configuración de puerta de enlace del OMS. Antes de usarlas, asegúrese de:

1. Instale la puerta de enlace OMS (MSI).
2. Abra la ventana de PowerShell.
3. Para importar el módulo, escriba el siguiente comando:`Import-Module OMSGateway`
4. Si se ha producido ningún error en el paso anterior, el módulo se ha importado correctamente y se puede usar los cmdlets. Tipo`Get-Module OMSGateway`
5. Después de realizar cambios mediante el uso de los cmdlets, asegúrese de que se reinicie el servicio de puerta de enlace.

Si recibe un error en el paso 3, no se ha importado el módulo. El error puede producirse cuando no puede encontrar el módulo de PowerShell. Puede encontrar en la ruta de instalación de la puerta de enlace: C:\Program archivos OMS Gateway\PowerShell.

| **Cmdlet** | **Parámetros** | **Descripción** | **Ejemplos** |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` | Clave (obligatorio) <br> Valor | Cambia la configuración del servicio | `Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` | Clave | Obtiene la configuración del servicio | `Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` | Dirección <br> Nombre de usuario <br> Contraseña | Establece la dirección y credenciales de proxy de retransmisión (ascendente) | 1. establecer un proxy de respuesta y la credencial:`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2. establecer a un proxy de respuesta que no necesita la autenticación:`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3. desactive la respuesta configuración de proxy, es decir, no es necesario un proxy de respuesta:`Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` |   | Obtiene la dirección del proxy de retransmisión (ascendente) | `Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` | Host (obligatorio) | Agrega el host a la lista de permitidos | `Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHos`t | Host (obligatorio) | Quita el host de la lista permitida | `Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` |   | Obtiene el host actualmente permitido (sólo configurada localmente pueden host, no incluya automáticamente descargados hosts permitidos) | `Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` | Asunto (obligatorio) | Agrega el certificado de cliente están sujetos a la lista de permitidos | `Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` | Asunto (obligatorio) | Quita al asunto del certificado de cliente de la lista permitida | `Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificat`e |   | Obtiene al cliente actualmente permitido asuntos de certificado (sólo los asuntos permitidos configurados localmente, no incluya automáticamente descargados asuntos permitidos) | `Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshoot"></a>Solución de problemas

Le recomendamos que instale al agente OMS en equipos que tienen instalada la puerta de enlace. A continuación, puede utilizar al agente para recopilar los eventos que se registran la puerta de enlace.

![Registro de la puerta de enlace OMS del Visor de sucesos](./media/log-analytics-oms-gateway/event-viewer.png)

**Identificadores de eventos de puerta de enlace de OMS y sus descripciones**

La siguiente tabla muestra los identificadores de evento y las descripciones de eventos de registro de la puerta de enlace de OMS.

| **ID.** | **Descripción** |
| --- | --- |
| 400 | Cualquier error de aplicación que no tiene un identificador específico |
| 401 | Configuración incorrecta. Por ejemplo: listenPort = "texto" en lugar de un número entero |
| 402 | Excepción en mensajes de protocolo de enlace TLS de análisis |
| 403 | Error de red. Por ejemplo: no puede conectarse al servidor de destino |
| 100 | Información general |
| 101 | Servicio se ha iniciado |
| 102 | Ha dejado de servicio |
| 103 | Recibió un comando HTTP CONNECT del cliente |
| 104 | No es un comando HTTP CONNECT |
| 105 | Servidor de destino no está en la lista permitida o el puerto de destino no es el puerto seguro (443) <br> <br> Asegúrese de que el agente MMA en su servidor de puerta de enlace y los agentes comunicar con la puerta de enlace está conectado a la misma área de trabajo de análisis de registro.|
| 105 | ERROR TcpConnection: certificado de cliente no válido: CN = puerta de enlace <br><br> Asegúrese de que: <br> <br> & #149; Está utilizando una puerta de enlace con el número de versión 1.0.395.0 o superior. <br> & #149; El agente MMA en su servidor de puerta de enlace y los agentes comunicar con la puerta de enlace está conectado a la misma área de trabajo de análisis de registro. |
| 106 | Cualquier motivo por el que la sesión TLS es sospechoso y rechazada |
| 107 | Se ha comprobado la sesión TLS |

**Contadores de rendimiento para recopilar**

La siguiente tabla muestra los contadores de rendimiento disponibles para la puerta de enlace de OMS. Puede agregar los contadores mediante el Monitor de rendimiento.

| **Nombre** | **Descripción** |
| --- | --- |
| Conexión de cliente de puerta de enlace/activo OMS | Número de conexiones de red (TCP) de cliente activas |
| Recuento de puerta de enlace o Error OMS | Número de errores |
| Cliente de puerta de enlace o conectada OMS | Número de clientes conectados |
| Recuento de puerta de enlace o rechace OMS | Número de rechazos debido a cualquier error de validación de TLS |

![Contadores de rendimiento de puerta de enlace de OMS](./media/log-analytics-oms-gateway/counters.png)


## <a name="get-assistance"></a>Obtener asistencia

Cuando ha iniciado sesión en el portal de Azure, puede crear una solicitud de asistencia con la puerta de enlace de OMS o cualquier otro servicio de Azure o característica de un servicio.
Para pedir asistencia, haga clic en el símbolo de signo de interrogación en la esquina superior derecha del portal y, a continuación, haga clic en **nueva solicitud de soporte técnico**. A continuación, complete el nuevo formulario de solicitud de soporte técnico.

![Nueva solicitud de soporte técnico](./media/log-analytics-oms-gateway/support.png)

También puede dejar comentarios sobre OMS o el análisis de registro en el [foro de comentarios de Microsoft Azure](https://feedback.azure.com/forums/267889).

## <a name="next-steps"></a>Pasos siguientes

- [Agregar orígenes de datos](log-analytics-data-sources.md) para recopilar datos de los orígenes de conectado en el área de trabajo OMS y guardarlo en el repositorio OMS.
