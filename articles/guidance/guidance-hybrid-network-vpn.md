<properties
   pageTitle="Implementar una arquitectura de red híbrido con Azure y local VPN | Microsoft Azure"
   description="Cómo implementar una arquitectura de red de sitio a sitio segura que abarca una red virtual Azure y una red local conectado mediante una VPN."
   services=""
   documentationCenter="na"
   authors="RohitSharma-pnp"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="roshar"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-and-on-premises-vpn"></a>Implementar una arquitectura de red híbrido con Azure y VPN local

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo se describe un conjunto de prácticas para ampliar una red local en Azure con una red privada virtual (VPN) de sitio a otro. Flujo del tráfico entre la red local y una red Virtual de Azure (VNet) a través de un túnel VPN IPSec. Esta arquitectura es adecuada para aplicaciones híbridas con las características siguientes:

- Partes de la aplicación ejecutan local mientras otras personas se ejecutan en Azure.

- El tráfico entre hardware local y la nube es probable que sea claro o es conveniente comercio latencia ligeramente ampliada de la flexibilidad y la capacidad de procesamiento de la nube.

- La red ampliada constituye un sistema cerrado. No hay ninguna ruta de acceso directo de Internet a VNet de Azure.

- Los usuarios conectarse a la red local para usar los servicios alojados en Azure. El puente entre la red local y los servicios que se ejecuta en Azure es transparente para los usuarios.

Ejemplos de escenarios que se ajustan a este perfil:

- Aplicaciones de línea de negocio utilizadas dentro de una organización, donde parte de la funcionalidad se ha migrado a la nube.

- Cargas de trabajo de desarrollo/prueba.

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación: [El Administrador de recursos de Azure] [ resource-manager-overview] y clásica. Esta guía utiliza el Administrador de recursos, Microsoft recomienda para implementaciones nuevas.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

El siguiente diagrama resalta los componentes de esta arquitectura:

> Un documento de Visio que incluye este diagrama de arquitectura está disponible para su descarga en el [Centro de descarga de Microsoft][visio-download]. Este diagrama es "red híbrida - VPN" página.

![[0]][0]

- **Red local.** Se trata de una red de equipos y dispositivos, conectados a través de una red de área local privada ejecutando dentro de una organización.

- **Dispositivo VPN.** Se trata de un dispositivo o servicio que proporciona conectividad externa a la red local. El dispositivo VPN puede ser un dispositivo de hardware, o puede ser una solución de software como el enrutamiento y acceso remoto (RRAS) en Windows Server 2012.

> [AZURE.NOTE] Para obtener una lista de dispositivos de VPN compatibles e información sobre cómo configurar dispositivos de VPN seleccionados para conectarse a una puerta de enlace de VPN de Azure, consulte las instrucciones del dispositivo adecuado en la [lista de dispositivos VPN compatibles con Azure][vpn-appliance].

- **Aplicación de nube de capas.** Esta es la aplicación hospedada en Azure. Es posible que incluya varios niveles, con varias subredes conectados a través de equilibradores de carga de Azure. El tráfico en cada subred puede ser sujeto a las reglas definidas mediante el uso de [Grupos de seguridad de red de Azure (NSGs)][azure-network-security-group]. Para obtener más información, vea [Introducción a la seguridad de Microsoft Azure][getting-started-with-azure-security].

> [AZURE.NOTE] Este artículo describe la aplicación de nube como una entidad única. Vea [ejecutar una arquitectura de N niveles en Azure] [ implementing-a-multi-tier-architecture-on-Azure] para obtener información detallada.

- **[Red virtual (VNet)][azure-virtual-network].** La aplicación de la nube y los componentes de la puerta de enlace de VPN Azure residen en el mismo VNet.

- **[Puerta de enlace VPN Azure][azure-vpn-gateway].** El servicio de puerta de enlace VPN le permite conectarse la VNet a la red local a través de un dispositivo VPN. Para obtener más información, vea [Conectar una red local a una red virtual de Microsoft Azure][connect-to-an-Azure-vnet]. La puerta de enlace VPN consta de los siguientes elementos:

  - **Puerta de enlace de red virtual.** Se trata de un recurso que le ofrece un dispositivo VPN virtual para el VNet. Es responsable de enrutar el tráfico de la red local a la VNet.

  - **Puerta de enlace de la red local.** Se trata de una extracción de dispositivo VPN local. Tráfico de red de la aplicación de nube a la red local se enruta a través de esta puerta de enlace.

  - **Conexión.** La conexión tiene propiedades que especifican el tipo de conexión (IPSec) y la clave que se comparte con el dispositivo de VPN local para cifrar el tráfico.

  - **Subred de puerta de enlace.** La puerta de enlace de red virtual se guarda en su propia subred, lo que está sujeto a requisitos distintos, como se describe en la sección de recomendaciones más adelante.

- **Equilibrador de carga interno.** Tráfico de red de la puerta de enlace VPN se dirige a la aplicación de nube mediante un equilibrador de carga interno. El equilibrador de carga se encuentra en la subred front-end de la aplicación.

## <a name="recommendations"></a>Recomendaciones

Azure ofrece numerosos recursos distintos y tipos de recursos, por lo que puede ser esta arquitectura de referencia se aprovisione varias formas diferentes. Proporcionamos una plantilla de administrador de recursos de Azure para instalar la arquitectura de referencia que sigue estas recomendaciones. Si elige crear su propia arquitectura de referencia debería seguir estas recomendaciones a menos que tenga un requisito específico que no se ajusta una recomendación.

### <a name="vnet-and-gateway-subnet"></a>Subred VNet y puerta de enlace

Crear un VNet de Azure para almacenar los componentes en la nube. El espacio de direcciones de la VNet de Azure debe ser suficientemente grande para acomodar las direcciones usadas las máquinas virtuales y subredes en la VNet. Asegúrese de que el espacio de direcciones VNet tiene suficiente espacio para el crecimiento si máquinas virtuales adicionales es probable que se necesita en el futuro. El espacio de direcciones de la VNet no debe coincidir con la red local. Por ejemplo, el diagrama anterior utiliza la 10.20.0.0/16 espacio dirección para la VNet.

Crear una subred denominada _GatewaySubnet_, con un intervalo de direcciones de /27. La puerta de enlace de red virtual requiere esta subred y asignar 32 direcciones a esta subred ayudará a evitar que se ejecuta frente a las limitaciones de tamaño de puerta de enlace posibles en el futuro. Evite colocar esta subred en el medio del espacio de direcciones. Es una buena práctica establecer el espacio de dirección de la subred de puerta de enlace en el extremo superior del espacio de direcciones VNet. El ejemplo que se muestra en el diagrama usa 10.20.255.224/27.  Un procedimiento rápido para calcular el CIDR es la siguiente:

1. Establecer los bits variables en el espacio de direcciones de la VNet a 1, hasta los bits que se utiliza la subred de puerta de enlace, a continuación, establezca los bits restantes en 0.

2. Convertir los bits resultantes en decimal y expresar como un espacio de direcciones con el conjunto de longitud de prefijo hasta el tamaño de la subred de puerta de enlace.

Por ejemplo, para un VNet con un intervalo de direcciones IP de 10.20.0.0/16, aplicar paso #1 anterior se convierte en 10.20.0b11111111.0b11100000.  Conversión en decimal y se expresa como un espacio de direcciones da como resultado 10.20.255.224/27

> [AZURE.WARNING] Implementar otros equipos virtuales o instancias de la función a la subred de puerta de enlace. Además, no se asigna un GSN a esta subred, ya que la puerta de enlace deje de funcionar.

### <a name="virtual-network-gateway"></a>Puerta de enlace de red virtual

Asignar una dirección IP pública para la puerta de enlace de red virtual.

Crear la puerta de enlace de red virtual en la subred de puerta de enlace y asignar la dirección IP pública recién asignada. Utilice el tipo de puerta de enlace que mejor coincida con los requisitos y que se activa mediante su dispositivo VPN:

Crear una [puerta de enlace de directiva] [ policy-based-routing] si necesita controlar cómo se enrutan las solicitudes de cerca. en función de criterios de directiva como prefijos de direcciones. Puertas de enlace de directiva utilizan el enrutamiento estático y solo funcionan con conexiones de sitio a sitio.

Crear una [puerta de enlace de ruta] [ route-based-routing] si se conecta a la red local con RRAS, admitir conexiones múltiples sitios o entre región o implementar conexiones de VNet a VNet (incluidas las rutas que atraviesan VNets varios). Puertas de enlace de ruta utilizan el enrutamiento dinámico para dirigir el tráfico entre redes. Su tolerancia a errores en la ruta de acceso de red mejor que las rutas estáticas, porque pueden intentar rutas alternativas. Puertas de enlace de ruta también pueden reducir la sobrecarga de administración, porque no es necesario que las rutas se pueden actualizar manualmente cuando cambien las direcciones de red.

Para obtener una lista de dispositivos de VPN compatibles, vea [dispositivos de VPN acerca de conexiones de puerta de enlace VPN de sitio a sitio][vpn-appliances].

> [AZURE.NOTE] Después de crear la puerta de enlace, no puede cambiar entre los tipos de puerta de enlace sin eliminar y volver a crear la puerta de enlace.

Seleccione el SKU de puerta de enlace de VPN de Azure que mejor coincida con los requisitos de rendimiento. La puerta de enlace de VPN Azure está disponible en tres SKU mostrados en la tabla siguiente. Cada SKU proporciona rendimiento diferente, [se cobra cargos] [ azure-gateway-charges] en función de la cantidad de tiempo que se aprovisiona la puerta de enlace y disponibles.

| SKU | Rendimiento VPN | Establece un túnel Max IPSec|
|-----|----------------|------------------|
| Básico | 100 Mbps | 10 |
| Estándar | 100 Mbps | 10 |
| Alto rendimiento | 200 Mbps | 30 |

> [AZURE.NOTE] El SKU básica no es compatible con Azure ExpressRoute. Puede [cambiar el SKU] [ changing-SKUs] después de la puerta de enlace se ha creado.

Crear reglas de enrutamiento para la subred de puerta de enlace que directa tráfico de aplicación de la puerta de enlace del equilibrador de carga interno en lugar de permitir solicitudes pasar directamente a las máquinas virtuales que implementan la aplicación.

### <a name="on-premises-network-connection"></a>Conexión de red local

Crear una puerta de enlace de red local. Especifique la dirección IP pública del dispositivo VPN local y el espacio de direcciones de la red local. Observe que el dispositivo de VPN local debe tener una dirección IP pública que puede tener acceso a la puerta de enlace de Azure VPN de puerta de enlace de red local. No se encuentra el dispositivo VPN detrás de un dispositivo NAT.

Crear una conexión de sitio a sitio para la puerta de enlace de red virtual y la puerta de enlace de red local. Seleccione el tipo de conexión de sitio a sitio (IPSec) y especificar la clave compartida. Cifrado de sitio a sitio con la puerta de enlace de VPN de Azure se basa en el protocolo IPSec, uso de claves previamente compartidas para la autenticación. Especificar la clave cuando se crea la puerta de enlace de VPN de Azure. Debe configurar el dispositivo VPN ejecuta local con la misma clave. Otros mecanismos de autenticación no se admiten actualmente.

Asegúrese de que la infraestructura de enrutamiento está configurada para reenviar solicitudes destinadas a las direcciones de la VNet de Azure en el dispositivo VPN en local.

Abra los puertos necesarios para la aplicación de nube en la red local.

Probar la conexión para comprobar:

- El dispositivo de VPN local correctamente enruta el tráfico a la aplicación de nube a través de la puerta de enlace de VPN de Azure.

- La VNet correctamente enruta el tráfico a la red local.

- Prohibido tráfico en ambas direcciones se bloquea correctamente.

## <a name="scalability-considerations"></a>Consideraciones de escalabilidad

Puede lograr escalabilidad vertical limitada moviendo desde el básico o SKU de puerta de enlace VPN estándar al alto rendimiento VPN SKU.

Para VNets espera un gran volumen de tráfico VPN, considere la posibilidad de distribuir las cargas de trabajo diferentes en independiente VNets más pequeño y configurar una puerta de enlace VPN para cada uno de ellos.

Se puede dividir el VNet horizontal o verticalmente. Para dividir horizontalmente, mover algunas instancias VM desde cada nivel en subredes de la nueva VNet. El resultado es que cada VNet tiene la misma estructura y funcionalidad. Para dividir verticalmente, vuelva a diseñar cada nivel para dividir la funcionalidad en diferentes áreas lógicas (por ejemplo, el tratamiento de pedidos, facturación, administración de cuentas de cliente y así sucesivamente). Cada área funcional, a continuación, se coloca en su propio VNet.

Replicar un controlador de dominio de Active Directory local en la VNet y la implementación de DNS en el VNet, pueden ayudar a reducir parte del tráfico relacionados con la seguridad y administrativo que fluye locales a la nube.

## <a name="availability-considerations"></a>Consideraciones de disponibilidad

Si necesita para asegurarse de que la red local sigue estando disponible a la puerta de enlace de Azure VPN, implementar un clúster de la puerta de enlace de VPN local.

Si su organización tiene varios sitios local, crear [conexiones múltiples sitios] [ vpn-gateway-multi-site] a uno o más VNets de Azure. Este enfoque requiere enrutamiento dinámico (basada en ruta), así que asegúrese de que la puerta de enlace de VPN local admite esta característica.

Vea [SLA de puerta de enlace VPN] [ sla-for-vpn-gateway] para los detalles sobre el SLA de puerta de enlace VPN.

## <a name="manageability-considerations"></a>Consideraciones de capacidad de administración

Revisar la información de diagnóstico de dispositivos de VPN local. Este proceso depende de las características proporcionadas por el dispositivo VPN. Por ejemplo, si está utilizando el enrutamiento y el servicio de acceso remoto en Windows Server 2012, debe habilitar [el registro de RRAS][rras-logging].

Usar los [Diagnósticos de puerta de enlace de Azure VPN] [ gateway-diagnostic-logs] para capturar información sobre los problemas de conectividad. Estos registros pueden utilizarse para realizar un seguimiento de información como el origen y destino de conexión solicitudes, el protocolo que se usó y cómo se estableció la conexión (o por qué no se pudo).

Supervisar los registros de funcionamiento de la puerta de enlace de VPN de Azure con los registros de auditoría disponibles en el portal de Azure. Registros independientes están disponibles para la puerta de enlace de red local, la puerta de enlace de red Azure y la conexión. Esta información puede utilizarse para realizar un seguimiento de los cambios realizados en la puerta de enlace y puede ser útil si una puerta de enlace funciona previamente deja de funcionar por algún motivo.

![[2]][2]

Supervisar la conectividad y realizar un seguimiento de eventos de error de conectividad. Puede usar un paquete de supervisión como [Nagios] [ nagios] para capturar y enviar esta información.

## <a name="security-considerations"></a>Consideraciones de seguridad

Generar una clave compartida diferente para cada puerta de enlace VPN. Use una clave compartida fuerte para resistir ataques.

> [AZURE.NOTE] Actualmente, no puede usar Azure clave depósito a puerta de enlace de Azure VPN claves previamente compartidas.

Asegúrese de que el dispositivo de VPN local usa un método de cifrado que sea [compatible con la puerta de enlace de Azure VPN][vpn-appliance-ipsec]. Para enrutamiento basado en la directiva de la puerta de enlace de Azure VPN es compatible con los algoritmos de cifrado AES256, AES128 y 3DES. Puertas de enlace de ruta admiten AES256 y 3DES.

Si su dispositivo de VPN local está en una red perimetral que tiene un firewall entre la red perimetral e Internet, debe configurar [reglas de firewall adicional] [ additional-firewall-rules] para permitir la conexión VPN de sitio a otro.

Si la aplicación en la VNet envía datos a Internet, considere la posibilidad de [implementar túnel forzado] [ forced-tunneling] para dirigir todo el tráfico de Internet enlazado a través de la red local. Este enfoque permite auditar solicitudes salientes realizadas por la aplicación de la infraestructura local.

> [AZURE.NOTE] Túnel forzado puede afectar conectividad con servicios de Azure (por ejemplo, el servicio de almacenamiento) y el Administrador de licencias de Windows.

## <a name="troubleshooting-considerations"></a>Consideraciones de solución de problemas

> [AZURE.NOTE] Visite el Blog de acceso remoto y enrutamiento para obtener información general acerca de [cómo solucionar errores comunes relacionados con la VPN][troubleshooting-vpn-errors].

Si no puede recorrer la conexión VPN tráfico, compruebe lo siguiente:

### <a name="on-premises-vpn-appliance"></a>Dispositivo de VPN local:

**¿Funciona correctamente el dispositivo de VPN local?**

Verificación cualquiera archivos de registro generados por el dispositivo VPN errores y errores. La ubicación de esta información varían según el dispositivo. Por ejemplo, si está utilizando RRAS en Windows Server 2012, puede usar el siguiente comando PowerShell para mostrar información de eventos de error para el servicio RRAS:

```
Get-EventLog -LogName System -EntryType Error -Source RemoteAccess | Format-List -Property *
```

La propiedad de _mensaje_ de cada entrada proporciona una descripción del error. Algunos ejemplos comunes son:

- No se puede conectar, posiblemente debido a una dirección IP incorrecta especificada para la puerta de enlace de VPN de Azure en la configuración de la interfaz de red VPN de RRAS:

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {41, 3, 0, 0}
Index              : 14231
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: The network connection between your computer and
                     the VPN server could not be established because the remote server is not responding. This could
                     be because one of the network devices (e.g, firewalls, NAT, routers, etc) between your computer
                     and the remote server is not configured to allow VPN connections. Please contact your
                     Administrator or your service provider to determine which device may be causing the problem.
Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, The network connection between
                     your computer and the VPN server could not be established because the remote server is not
                     responding. This could be because one of the network devices (e.g, firewalls, NAT, routers, etc)
                     between your computer and the remote server is not configured to allow VPN connections. Please
                     contact your Administrator or your service provider to determine which device may be causing the
                     problem.}
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:26:02 PM
TimeWritten        : 3/18/2016 1:26:02 PM
UserName           :
Site               :
Container          :
```

- La clave compartida incorrecta se especifica en la configuración de la interfaz de red VPN de RRAS:

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {233, 53, 0, 0}
Index              : 14245
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: IKE authentication credentials are unacceptable

Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, IKE authentication credentials are
                     unacceptable
                     }
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:34:22 PM
TimeWritten        : 3/18/2016 1:34:22 PM
UserName           :
Site               :
Container          :
```

También puede obtener información de registro de eventos acerca de los intentos para conectarse a través del servicio RRAS mediante el siguiente comando PowerShell:

```
Get-EventLog -LogName Application -Source RasClient | Format-List -Property *
```

Si se produce un error al conectarse, este registro contendrá errores que tengan un aspecto similares al siguiente:

```
EventID            : 20227
MachineName        : on-prem-vm
Data               : {}
Index              : 4203
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : CoId={B4000371-A67F-452F-AA4C-3125AA9CFC78}: The user SYSTEM dialed a connection named
                     AzureGateway which has failed. The error code returned on failure is 809.
Source             : RasClient
ReplacementStrings : {{B4000371-A67F-452F-AA4C-3125AA9CFC78}, SYSTEM, AzureGateway, 809}
InstanceId         : 20227
TimeGenerated      : 3/18/2016 1:29:21 PM
TimeWritten        : 3/18/2016 1:29:21 PM
UserName           :
Site               :
Container          :
```

**¿Es la VPN dispositivo correctamente enrutar el tráfico a través de la puerta de enlace de VPN de Azure?**

Utilice una herramienta como [PsPing] [ psping] para comprobar la conectividad y el enrutamiento a través de la puerta de enlace VPN. Por ejemplo, para probar la conectividad desde un equipo local a un servidor web que se encuentra en la VNet, ejecute el siguiente comando (reemplazar `<<web-server-address>>` con la dirección del servidor web):

```
PsPing -t <<web-server-address>>:80
```

Si el equipo local puede enrutar el tráfico al servidor web, verá un resultado similar al siguiente:

```
D:\PSTools>psping -t 10.20.0.5:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.0.5:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.0.5:80 (warmup): 6.21ms
Connecting to 10.20.0.5:80: 3.79ms
Connecting to 10.20.0.5:80: 3.44ms
Connecting to 10.20.0.5:80: 4.81ms

  Sent = 3, Received = 3, Lost = 0 (0% loss),
  Minimum = 3.44ms, Maximum = 4.81ms, Average = 4.01ms
```

Si el equipo local no se puede comunicar con el destino especificado, verá mensajes similar a esta:

```
D:\PSTools>psping -t 10.20.1.6:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.1.6:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.1.6:80 (warmup): This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80:
  Sent = 3, Received = 0, Lost = 3 (100% loss),
  Minimum = 0.00ms, Maximum = 0.00ms, Average = 0.00ms
```

**¿El firewall local que admita el tráfico VPN pase a través de? ¿Se han abierto los puertos correctos?**

Compruebe que el dispositivo de VPN local usa un método de cifrado que sea [compatible con la puerta de enlace de Azure VPN][vpn-appliance].

Para enrutamiento basado en la directiva de la puerta de enlace de Azure VPN es compatible con los algoritmos de cifrado AES256, AES128 y 3DES. Puertas de enlace de ruta admiten AES256 y 3DES.

### <a name="azure-vnet-gateway"></a>Puerta de enlace VNet Azure:

Examinar [los registros de diagnóstico de puerta de enlace de Azure VPN] [ gateway-diagnostic-logs] posibles problemas.

**¿Son la puerta de enlace de Azure VPN y dispositivo de VPN local configurado con la misma clave de autenticación compartida?**

Puede ver la clave compartida almacenada por la puerta de enlace de Azure VPN mediante el siguiente comando CLI de Azure:

```
azure network vpn-connection shared-key show <<resource-group>> <<vpn-connection-name>>
```

Use el comando apropiado para su dispositivo de VPN local para mostrar la clave compartida configurada para ese dispositivo.

Compruebe la subred _GatewaySubnet_ manteniendo que la puerta de enlace de VPN Azure no está asociada con un GSN.

Puede ver los detalles de subred mediante el siguiente comando CLI de Azure:

```
azure network vnet subnet show -g <<resource-group>> -e <<vnet-name>> -n GatewaySubnet
```

Asegúrese de que no existe ningún campo de datos denominado _Id. de grupo de seguridad de red_. En el ejemplo siguiente se muestra los resultados de ejemplo de _GatewaySubnet_ que tiene un GSN asignado (_VPN puerta de enlace_). Esto puede provocar que impedir que la puerta de enlace de funcionar correctamente si se han definido para este GSN estas reglas:

```
C:\>azure network vnet subnet show -g profx-prod-rg -e profx-vnet -n GatewaySubnet
    info:    Executing command network vnet subnet show
    + Looking up virtual network "profx-vnet"
    + Looking up the subnet "GatewaySubnet"
    data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/virtualNetworks/profx-vnet/subnets/GatewaySubnet
    data:    Name                            : GatewaySubnet
    data:    Provisioning state              : Succeeded
    data:    Address prefix                  : 10.20.3.0/27
    data:    Network Security Group id       : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/networkSecurityGroups/VPN-Gateway-Group
    info:    network vnet subnet show command OK
```

**¿Son las máquinas virtuales de VNet de Azure configuradas para permitir el tráfico procedentes de fuera de la VNet? Comprueba las reglas de GSN asociadas que contiene estas máquinas virtuales de subredes.**

Puede ver todas las reglas de GSN mediante el siguiente comando CLI de Azure:

```
azure network nsg show -g <<resource-group>> -n <<nsg-name>>
```

**¿Se ha desconectado la puerta de enlace de VPN de Azure?**

Puede usar el siguiente comando PowerShell Azure para comprobar el estado actual de la conexión VPN de Azure. La `<<connection-name>>` parámetro es el nombre de la conexión VPN de Azure que lleve la puerta de enlace de red virtual y la puerta de enlace local.

```
Get-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> - ResourceGroupName <<resource-group>>
```

Los siguientes fragmentos de resaltar el resultado generado si la puerta de enlace está conectado (el primer ejemplo) y se desconecta (el segundo ejemplo):

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : Connected
EgressBytesTransferred     : 55254803
IngressBytesTransferred    : 32227221
ProvisioningState          : Succeeded
...
```

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection2 -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : NotConnected
EgressBytesTransferred     : 0
IngressBytesTransferred    : 0
ProvisioningState          : Succeeded
...
```

### <a name="host-vm-configuration-network-bandwidth-utilization-and-application-performance"></a>Configuración de la máquina host, uso de ancho de banda de red y rendimiento de la aplicación:

Compruebe que el servidor de seguridad en el sistema operativo de invitado ejecutando en las máquinas virtuales de Azure en la subred están configurados correctamente para permitir el tráfico permitido de los intervalos de IP local.

**¿Es el volumen de tráfico cerca del límite del ancho de banda disponible para la puerta de enlace de VPN de Azure?**

Herramientas depende de las instalaciones disponibles para su dispositivo VPN ejecuta local. Por ejemplo, si está utilizando RRAS en Windows Server 2012, puede usar al Monitor de rendimiento para realizar un seguimiento del volumen de datos que se recibió y se transmite a través de la conexión VPN; uso del objeto _RAS Total_ , seleccione los contadores _Bytes recibidos por segundo_ y _Bytes transmitidos por segundo_ :

![[3]][3]

Debe comparar los resultados con el ancho de banda disponible para la puerta de enlace VPN (100 Mbps para la Basic y SKU estándar y 200 Mbps de alto rendimiento SKU):

![[4]][4]

**¿Está ejecutando cualquiera de las máquinas virtuales de VNet de Azure lentamente? ¿Son que sobrecargue, son suficientemente de ellos para controlar la carga, hay todos los equilibradores de carga configurados correctamente?**

Esto requiere [capturar y analizar información de diagnóstico][azure-vm-diagnostics]. Puede examinar los resultados con el portal de Azure, pero muchas herramientas de terceros también están disponibles que pueden proporcionar información detallada en los datos de rendimiento.

**¿Es la aplicación de mejorar la eficacia de uso de recursos de la nube?**

Código de la aplicación instrumento ejecutando en cada máquina virtual para determinar si las aplicaciones para realizar el mejor uso de recursos. Puede usar herramientas como [Aplicación perspectivas] [ application-insights] para ayudar a realizar estas tareas.

## <a name="solution-deployment"></a>Implementación de soluciones

Si tiene una infraestructura local existente ya está configurada con un dispositivo VPN, puede implementar la arquitectura de referencia siguiendo estos pasos:

1. Haga clic con el botón secundario del mouse en el botón de abajo y seleccione uno "Abrir vínculo en una nueva pestaña" o "Abrir vínculo en ventana nueva":  
[![Implementar en Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn%2Fazuredeploy.json)

2. Espere a que el vínculo para abrir en el portal de Azure, a continuación, siga estos pasos: 
    - El nombre del **grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Crear nuevo** y escriba `ra-hybrid-vpn-rg` en el cuadro de texto.
    - Seleccione la región en el cuadro desplegable de **ubicación** .
    - No modifique la **Plantilla raíz Uri** o los cuadros de texto de **Uri raíz del parámetro** .
    - Revise los términos y condiciones y luego haga clic en la casilla de verificación **que acepto los términos y condiciones indicadas arriba** .
    - Haga clic en el botón de **compra** .

3. Espere a que la implementación completar.

## <a name="next-steps"></a>Pasos siguientes

- [Instalar los controladores de dominio adicionales para un dominio de Active Directory local con máquinas virtuales en un VNet de Azure][installing-ad].

- [Directrices para la implementación de Active Directory Windows Server en máquinas virtuales de Azure][deploying-ad].

- [Creación de un servidor DNS en un VNet][creating-dns].

- [Configuración y administración de DNS en un VNet][configuring-dns].

- [Con los dispositivos de seguridad de red de Stormshield local a través de una VPN][stormshield].

- [Implementar una arquitectura de red híbrido con Azure ExpressRoute][expressroute].

<!-- links -->

[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[arm-templates]: ../resource-group-authoring-templates.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-virtual-network]: ../virtual-network/virtual-networks-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[azure-network-security-group]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: ../vpn-gateway/vpn-gateway-multi-site.md
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/v1_0/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[azure-vpn-gateway-diagnostics]: http://blogs.technet.com/b/keithmayer/archive/2014/12/18/diagnose-azure-virtual-network-vpn-connectivity-issues-with-powershell.aspx
[ping]: https://technet.microsoft.com/library/ff961503.aspx
[tracert]: https://technet.microsoft.com/library/ff961507.aspx
[psping]: http://technet.microsoft.com/sysinternals/jj729731.aspx
[nmap]: http://nmap.org
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: http://blogs.technet.com/b/keithmayer/archive/2015/12/07/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs.aspx
[troubleshooting-vpn-errors]: https://blogs.technet.microsoft.com/rrasblog/2009/08/12/troubleshooting-common-vpn-related-errors/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[create-on-prem-network]: https://technet.microsoft.com/library/dn786406.aspx#routing
[create-azure-vnet]: ../virtual-network/virtual-networks-create-vnet-classic-cli.md
[azure-vm-diagnostics]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[application-insights]: ../application-insights/app-insights-overview-usage.md
[forced-tunneling]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-forced-tunneling/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[vpn-appliances]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[installing-ad]: ../active-directory/active-directory-install-replica-active-directory-domain-controller.md
[deploying-ad]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[creating-dns]: https://blogs.msdn.microsoft.com/mcsuksoldev/2014/03/04/creating-a-dns-server-in-azure-iaas/
[configuring-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[stormshield]: https://azure.microsoft.com/marketplace/partners/stormshield/stormshield-network-security-for-cloud/
[vpn-appliance-ipsec]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec-parameters
[expressroute]: ./guidance-hybrid-network-expressroute.md
[naming conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/deploy-reference-architecture.sh
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetwork.parameters.json
[virtualNetworkGateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetworkGateway.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/blueprints/hybrid-network-vpn.png "Estructura de un híbrido que ocupan las instalaciones de red y de infraestructuras de nube"
[1]: ./media/guidance-hybrid-network-vpn/partitioned-vpn.png "Partición una VNet para mejorar la escalabilidad"
[2]: ./media/guidance-hybrid-network-vpn/audit-logs.png "Registros de auditoría en el portal de Azure"
[3]: ./media/guidance-hybrid-network-vpn/RRAS-perf-counters.png "Contadores de rendimiento para supervisar el tráfico de red VPN"
[4]: ./media/guidance-hybrid-network-vpn/RRAS-perf-graph.png "Gráfico de rendimiento de red VPN de ejemplo"