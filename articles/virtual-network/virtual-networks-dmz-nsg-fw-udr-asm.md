<properties
   pageTitle="Ejemplo de DMZ: crear una DMZ para proteger redes con un Firewall, UDR y GSN | Microsoft Azure"
   description="Crear una DMZ con un Firewall, definidas por el usuario enrutamiento (UDR) y grupos de seguridad de red (NSG)"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Ejemplo 3: crear una DMZ para proteger redes con un Firewall, UDR y NSG

[Volver a la página de prácticas recomendada de seguridad límite][HOME]

En este ejemplo se creará una DMZ con un firewall, cuatro servidores de windows, definidos por el enrutamiento de usuario, el reenvío IP y grupos de seguridad de red. También le guiará a través de cada uno de los comandos pertinentes para proporcionar un conocimiento más profundo de cada paso. También hay una sección de escenario de tráfico para proporcionar una detallada paso a paso cómo continúa el tráfico a través de los niveles de defensa en la DMZ. Por último, en las referencias de sección es el código completo e instrucciones para crear este entorno para probar y experimentar con diferentes escenarios. 

![DMZ bidireccional con NVA, NSG y UDR][1]

## <a name="environment-setup"></a>Configuración de entorno
En este ejemplo hay una suscripción que contiene lo siguiente:

- Tres servicios de nube: "SecSvc001", "FrontEnd001" y "BackEnd001"
- Una red Virtual "CorpNetwork", con tres subredes: "SecNet", "Cliente" y "Back-end"
- Un dispositivo virtual de red, en este ejemplo, un firewall, conectado a la subred SecNet
- Un servidor de Windows que representa un servidor de aplicaciones web ("IIS01")
- Los servidores de dos ventanas que representan aplicación volver fin servidores ("AppVM01", "AppVM02")
- Un servidor de Windows que representa un servidor DNS ("DNS01")

En la sección referencias a continuación hay un script de PowerShell que generará la mayoría del entorno descrito anteriormente. Crear las máquinas virtuales y redes virtuales, aunque haya terminado la secuencia de comandos de ejemplo, no se describen en detalle en este documento.

Para crear el entorno:

  1.    Guardar el archivo de xml de configuración de red incluido en la sección referencias (actualizado con IP, ubicación y nombres de direcciones para que coincida con el escenario determinado)
  2.    Actualizar las variables de usuario en la secuencia de comandos para que coincida con el entorno que es la secuencia de comandos se ejecute en (suscripciones, nombres de servicio, etcetera)
  3.    Ejecutar la secuencia de comandos de PowerShell

**Nota**: la región indicada en la secuencia de comandos de PowerShell debe coincidir con la región indicada en el archivo xml de configuración de red.

Una vez que la secuencia de comandos se ejecute correctamente, podrán tomarse los siguientes pasos posteriores a la secuencia de comandos:

1.  Configurar las reglas de firewall, se trata en la sección siguiente titulada: descripción de la regla de Firewall.
2.  Opcionalmente, en la sección referencias son dos secuencias de comandos para configurar el servidor web y el servidor de la aplicación con una aplicación web simple para permitir pruebas con esta configuración DMZ.

Una vez que la secuencia de comandos ejecute correctamente el firewall reglas tendrán que llevar a cabo, se trata en la sección titulada: reglas de Firewall.

## <a name="user-defined-routing-udr"></a>Enrutamiento (UDR) definidas por el usuario
De forma predeterminada, las rutas de sistema siguientes se definen como:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

El VNETLocal es siempre la prefix(es) dirección definido de la VNet de dicha red específica (ie cambiará de VNet a VNet según cómo se haya definido cada VNet específico). Las rutas de sistema restantes son estáticas y predeterminado como se indicó anteriormente.

En cuanto a prioridad, rutas se procesan a través del método de coincidencia de prefijo más larga (LPM), por lo tanto la ruta más específica en la tabla se aplica a la dirección de destino dado.

Por lo tanto, el tráfico (por ejemplo, en el servidor de DNS01, 10.0.2.4) destinado a la red local (10.0.0.0/16) se enrutan a través de la VNet hasta su destino debido a la ruta 10.0.0.0/16. En otras palabras, para 10.0.2.4, la ruta de 10.0.0.0/16 es la ruta más específica, aunque la 10.0.0.0/8 y 0.0.0.0/0 también pueden aplicar, pero ya que son menos específico no afectan a este tráfico. Por tanto el tráfico de 10.0.2.4 ¿tienen un salto siguiente de la VNet local y simplemente ruta al destino.

Si el tráfico destinado para 10.1.1.1 por ejemplo, no puede aplicar la ruta 10.0.0.0/16, pero la 10.0.0.0/8 sería más específico y el tráfico sería colocado ("negro holed") como el siguiente salto es Null. 

Si el destino no aplica a cualquiera de los prefijos Null o los prefijos VNETLocal, seguiría menos específica redirigir 0.0.0.0/0 y se enrutan a Internet como el próximo salto y, por tanto, borde de internet de Azure.

Si hay dos prefijos idénticos en la tabla de rutas, a continuación se muestra el orden de prioridad basada en el atributo de "origen" rutas:

1.  "VirtualAppliance" = una ruta de definido por el usuario agregado manualmente a la tabla
2.  "VPNGateway" = una ruta dinámicos (BGP cuando se utiliza con redes híbrido) agregados por un protocolo de red dinámicas, estas rutas pueden cambiar con el tiempo según el protocolo dinámico reflejará automáticamente los cambios de red peered
3.  "Predeterminado" = las rutas de sistema, la VNet local y las entradas estáticas tal como se muestra en la tabla de ruta anterior.

>[AZURE.NOTE] Ahora puede utilizar enrutamiento definidas por el usuario (UDR) con puertas de enlace de VPN y ExpressRoute para forzar el tráfico local entre entrante y saliente a ser enrutado a un dispositivo de red virtual (NVA).

#### <a name="creating-the-local-routes"></a>Crear las rutas locales

En este ejemplo, se necesitan dos tablas de enrutamiento, uno para las subredes front-end y back-end. Cada tabla se carga con rutas estáticas correspondiente a la subred determinada. En este ejemplo, cada tabla tiene tres rutas:

1. Para permitir el tráfico de subred local omitir el firewall definidas por el tráfico de subred local con siguiente sin salto
2. Tráfico de red virtual con un siguiente salto definido como firewall, anula la regla predeterminada que permite tráfico VNet local enrutar directamente
3. Tráfico todas las restante (0/0) con un siguiente salto definido como el firewall

Una vez que se crean las tablas de enrutamiento se enlazan a sus subredes. Para la subred front-end enrutamiento tabla, una vez creado y enlazado a la subred debe ser similar a esta:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


En este ejemplo, los comandos siguientes se utilizan para generar la tabla de ruta, agregue una ruta definida por el usuario y, a continuación, enlazar la tabla de rutas a una subred (Nota; los elementos por debajo de la que comienza con un signo de dólar (por ejemplo: $BESubnet) son variables definidas por el usuario de la secuencia de comandos en la sección de referencia de este documento):

1.  En primer lugar debe crearse la tabla de enrutamiento base. Este fragmento de código muestra la creación de la tabla para la subred back-end. En la secuencia de comandos, también se crea una tabla correspondiente para la subred front-end.

        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"

2.  Una vez creada la tabla de rutas, se pueden agregar rutas específicas definidas por el usuario. En esta cortados, todo el tráfico (0.0.0.0/0) se se enrutan a través del dispositivo virtual (una variable, $VMIP [0] se utiliza para pasar de la dirección IP asignada cuando el dispositivo virtual que creó anteriormente en la secuencia de comandos). En la secuencia de comandos, también se crea una regla correspondiente en la tabla de front-end.

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

3. La entrada de ruta anterior reemplazará la "0.0.0.0/0" ruta predeterminada, pero la regla de 10.0.0.0/16 predeterminada aún existente que desea que admita el tráfico dentro de la VNet para enrutar directamente en el destino y no en el dispositivo de red Virtual. A corregir este comportamiento de la regla de seguimiento debe agregarse.

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

4. En este momento hay una opción para realizar. Con las rutas de dos anteriores todo el tráfico se redirige el firewall para evaluación, incluso el tráfico dentro de una única subred. Esto posible que desee, sin embargo, para permitir el tráfico dentro de una subred para enrutar localmente sin intervención del cortafuegos una tercera, se puede agregar regla muy específica. Esta ruta enrutar directamente los Estados cualquier dirección destine para la subred local basta (NextHopType = VNETLocal).

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal

5.  Por último, con la tabla de enrutamiento crea y rellena con un rutas definidas por el usuario, la tabla debe ahora estar enlazada a una subred. En la secuencia de comandos, la tabla de rutas de front-end también enlazada a la subred front-end. Aquí tiene el script de enlace para la subred back-end.

        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
           -SubnetName $BESubnet `
           -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>Reenvío de IP
Una característica de companion a UDR, es el reenvío de IP. Se trata de una configuración en un dispositivo Virtual que le permite recibir tráfico no específicamente dirigido al dispositivo y, a continuación, reenviar que el tráfico a su destino final.

Por ejemplo, si el tráfico de AppVM01 realiza una solicitud al servidor de DNS01 UDR ' d enrutar esto al firewall. Con reenvío IP habilitado, el tráfico al destino DNS01 (10.0.2.4) se aceptarán el dispositivo (10.0.0.4) y, a continuación, reenvía a su destino final (10.0.2.4). Sin reenvío IP habilitado en el Firewall, el tráfico no debería aceptado por el dispositivo incluso si la tabla de ruta tiene el firewall como el próximo salto. 

>[AZURE.IMPORTANT] Es fundamental recordar habilitar el reenvío IP junto con el usuario definidos por el enrutamiento.

Configurar el reenvío de IP es un comando único y se puede hacer en tiempo de creación de la máquina virtual. Para el flujo de este ejemplo, el fragmento de código es hacia el final de la secuencia de comandos y agrupados con los comandos UDR:

1.  Llamar a la instancia VM en este caso es su dispositivo virtual, el firewall y habilitar el reenvío IP (Nota; cualquier elemento en rojo que comienza con un signo de dólar (por ejemplo: $VMName[0]) es una variable definida por el usuario de la secuencia de comandos en la sección de referencia de este documento. El cero entre corchetes, [0] representa la máquina virtual de uno en la matriz de VM para trabajar sin modificar la secuencia de comandos de ejemplo, la primera VM (VM 0) debe ser el firewall):

        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
           Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Grupos de seguridad de red (NSG)
En este ejemplo, un grupo de GSN está integrado y, a continuación, se cargan con una sola regla. Este grupo se enlaza únicamente a las subredes front-end y back-end (no el SecNet). Mediante declaración se genera la siguiente regla:

1.  Denegado cualquier (todos los puertos) el tráfico de Internet a la VNet completa (todas las subredes)

Aunque NSGs se utilizan en este ejemplo, su principal objetivo es como un nivel secundario de defensa frente a errores de configuración manual. Queremos bloquear todo el tráfico entrante el tráfico de internet: el front-end o subredes de back-end, el tráfico solo deben fluya a través de la subred SecNet el Firewall (y a continuación si su caso en el front-end o back-end subredes). Además, con las reglas UDR en su lugar, tráfico que realizó en el front-end o back-end subredes ' d dirigirá fuera del firewall (gracias a UDR). El firewall verá como un flujo asimétrico y desea colocar el tráfico saliente. Por lo tanto, hay tres capas de seguridad proteger el subredes front-end y back-end; 1) sin extremos abiertos en el FrontEnd001 y BackEnd001 servicios de nube, 2) NSGs Denegar tráfico de Internet, 3) el firewall prohibir asimétricos el tráfico.

Un aspecto interesante relacionada con el grupo de seguridad de red en este ejemplo es que contiene solo una regla, que se muestra a continuación, que es Denegar tráfico de internet a toda la red virtual que se incluye la subred de seguridad. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

Sin embargo, dado que la GSN sólo se enlaza a las subredes front-end y back-end, no se procesa la regla en el tráfico de entrada a la subred de seguridad. Como resultado, aunque la regla GSN no dice tráfico de Internet a cualquier dirección en la VNet, porque la GSN nunca se ha enlazado a la subred de seguridad, el tráfico fluirá a la subred de seguridad.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName
    
    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Reglas de Firewall
En el firewall, las reglas de reenvío tendrán que debe crearse. Dado que el firewall es tráfico bloqueo o reenvío todo el tráfico entrante, saliente y dentro de un VNet son necesarias muchas reglas de firewall. Además, todo el tráfico entrante visitas la dirección IP pública del servicio de seguridad (en distintos puertos), para procesar el firewall. Un procedimiento recomendado es crear diagramas de los flujos de lógicos antes de configurar las subredes y reglas de firewall para evitar rediseñar más adelante. La figura siguiente es una vista lógica de las reglas de firewall para este ejemplo:
 
![Vista lógica de las reglas de Firewall][2]

>[AZURE.NOTE] Según el dispositivo de red Virtual usado, varían los puertos de administración. En este ejemplo se hace referencia a un NextGen Firewall Barracuda que usa los puertos 22, 801 y 807. Consulte la documentación de proveedor del dispositivo para buscar los puertos exactos que se usa para la administración del dispositivo que se usa.

### <a name="logical-rule-description"></a>Descripción de la regla lógica
En el diagrama lógico anterior, la subred de seguridad no se muestra debido a que el servidor de seguridad es el único recurso de la subred y este diagrama muestra las reglas de firewall, y cómo lógicamente permitir o denegar flujos de tráfico y no de la ruta de acceso redirigida real. Además, los puertos externos seleccionados para el tráfico RDP son superiores iban puertos (8014 – 8026) y se han seleccionado para algo alinear con los dos últimos octetos de la dirección IP local para mejorar la legibilidad sea más fácil (por ejemplo, dirección del servidor local 10.0.1.4 está asociado a puerto externo 8014), sin embargo se podrían usar los puertos superior no entran en conflicto.

En este ejemplo, debemos 7 tipos de reglas, estos tipos de reglas se describen como sigue:

- Reglas externos (para el tráfico entrante):
  1.    Regla de administración de Firewall: Esta regla redirección de aplicación permite el tráfico pasar a los puertos de administración del dispositivo virtual de red.
  2.    Reglas de RDP (por cada servidor de windows): estos cuatro reglas (uno por cada servidor) le permitirá la administración de los servidores individuales a través de RDP. También se incluirá en una regla según las capacidades del dispositivo Virtual de red que se usa.
  3.    Las reglas de tráfico de aplicación: Hay dos reglas de tráfico de aplicación, la primera para el tráfico de web front-end y la segunda para el tráfico de back-end (por ejemplo, el servidor web a nivel de datos). La configuración de estas reglas se dependen de la arquitectura de red (donde se colocan los servidores) y flujos de tráfico (qué dirección se usan los flujos de tráfico y los puertos).
      - La primera regla permitirá el tráfico de la aplicación real llegar al servidor de aplicaciones. Mientras que las demás reglas permiten seguridad, administración, etc., reglas de la aplicación son los que permiten usuarios externos o servicios tener acceso a las aplicaciones. En este ejemplo, hay un único servidor web en el puerto 80, por tanto, una regla de aplicación única firewall redirige el tráfico a la dirección IP externa a la dirección IP de web servidores interna. La sesión de tráfico redirigido podría ser NAT tenía al servidor interno.
      - La segunda regla de tráfico de la aplicación es la regla de back-end para permitir que el servidor Web póngase en contacto con el servidor de AppVM01 (pero no AppVM02) a través de cualquier puerto.
- Reglas internas (para el tráfico dentro de un VNet)
  4.    Salida a Internet regla: esta regla permitirá el tráfico de cualquier red para pasar a las redes seleccionadas. Esta regla suele ser una regla predeterminada ya en el firewall, pero en un estado deshabilitado. Esta regla debe estar habilitada para este ejemplo.
  5.    Regla DNS: Esta regla permite sólo el tráfico DNS (puerto 53) pasar al servidor DNS. Para este entorno que mayor cantidad de tráfico desde el cliente para el servidor está bloqueado, esta regla permite específicamente DNS desde cualquier subred local.
  6.    Regla de subred a: esta regla es permitir que cualquier servidor en la subred back-end para conectarse a cualquier servidor en la subred front-end (pero no a la inversa).
- Regla a prueba de errores (para el tráfico que no cumple alguno de los anteriores):
  7.    Denegar todas las reglas de tráfico: Esto siempre debe ser la regla final (en términos de prioridad) y por tanto no coincide con cualquiera de las reglas anteriores se quitarán por esta regla si un tráfico fluye. Se trata de una regla predeterminada y normalmente no activado, modificaciones generalmente son necesarias.

>[AZURE.TIP] En la segunda regla de tráfico de aplicación, cualquier puerto está permitido para fácil de este ejemplo, en un escenario real el puerto más específico e intervalos de direcciones deben usarse para reducir la superficie de ataque de esta regla.

<br />

>[AZURE.IMPORTANT] Una vez que se crean todas las reglas anteriores, es importante revisar la prioridad de cada regla para asegurarse de tráfico se permitido o denegado como desee. En este ejemplo, las reglas están en orden de prioridad. Es fácil esté bloqueada en el firewall debido a mal ordenadas reglas. Como mínimo, asegúrese de que la administración del servidor de seguridad es siempre la regla de mayor prioridad absoluta.

### <a name="rule-prerequisites"></a>Requisitos previos de regla
Un requisito previo para la máquina Virtual ejecuta el firewall son extremos públicos. El firewall procesar el tráfico de los extremos adecuados públicos deben estar abiertos. Existen tres tipos de tráfico en este ejemplo; Tráfico RDP 1) tráfico de administración para controlar el firewall y las reglas de firewall, 2) para controlar el tráfico de aplicaciones 3) y servidores de windows. Estos son las tres columnas de tipos de tráfico en la parte superior de la mitad de la vista lógica de las reglas de firewall encima.

>[AZURE.IMPORTANT] Una clave takeway aquí es recordar que vaya **todo** el tráfico a través del firewall. Por lo tanto a Escritorio remoto en el servidor IIS01, incluso si está en el servicio de nube Front-End y en la subred Front-End, para tener acceso a este servidor se deberá RDP al servidor de seguridad en el puerto 8014 y, a continuación, permitir el firewall enrutar la solicitud RDP internamente al puerto RDP IIS01. Botón de "Conectar" del portal Azure no funcionará porque no hay ninguna ruta de acceso directo de RDP a IIS01 (en lo que puede ver el portal). Esto significa que todas las conexiones de internet será el servicio de seguridad y un puerto, por ejemplo, secscv001.cloudapp.net:xxxx (referencia del diagrama anterior para la asignación de puerto externo y IP interna y puerto).

Un extremo puede abrirse en el momento de la creación de la máquina virtual o publique generar mientras se realiza en la secuencia de comandos de ejemplo y se muestra a continuación en este fragmento de código (Nota; cualquier elemento que comienza con un signo de dólar (por ejemplo: $VMName[$i]) es una variable definida por el usuario de la secuencia de comandos en la sección de referencia de este documento. "$I" entre corchetes, [$i] representa el número de la matriz de una máquina virtual específica de una matriz de VM):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Aunque no claramente se muestra aquí debido al uso de las variables, pero extremos son **solo** abierto en el servicio de nube de seguridad. Esto es asegurarse de que todo el tráfico entrante se controla (enruta, NAT tenía, quitar) por el firewall.

Un cliente de administración tendrán que esté instalado en un equipo para administrar el firewall y crear las configuraciones que sea necesarias. Vea el proveedor de la documentación de su firewall (u otro NVA) acerca de cómo administrar el dispositivo. El resto de esta sección y la sección siguiente, la creación de reglas de Firewall, describe la configuración del servidor de seguridad, a través del cliente de administración de proveedores (es decir, no el portal de Azure o PowerShell).

Aquí puede encontrar instrucciones para la descarga de cliente y conectarse a la Barracuda usado en este ejemplo: [Administración de Barracuda NG](https://techlib.barracuda.com/NG61/NGAdmin)

Una vez que ha iniciado sesión en el servidor de seguridad, pero antes de crear reglas de firewall, hay dos clases de objeto como requisito previo que pueden hacer que sea más fácil; la creación de las reglas Objetos de servicio y de red.

En este ejemplo, tres objetos de red con nombre deben ser definido (para la subred front-end y la subred back-end, también un objeto de red para la dirección IP del servidor DNS). Para crear una red con nombre; empezando desde el panel de administración de NG Barracuda de cliente, vaya a la pestaña Configuración, en la sección Configuración operativas, haga clic en el conjunto de reglas, a continuación, haga clic en "Redes" en el menú de objetos de Firewall y haga clic en nuevo en el menú Editar redes. El objeto de red ahora pueden crearse, agregando el nombre y el prefijo:

![Crear un objeto de red del cliente][3]
 
Esta opción creará una red con nombre para la subred front-end, se debe crear un objeto similar para también la subred back-end. Ahora las subredes pueden hacer referencia más fácilmente por el nombre de las reglas de firewall.

Para el objeto de servidor DNS:

![Crear un objeto de servidor DNS][4]

Esta referencia de dirección IP solo se utilizarán en una regla DNS más adelante en el documento.

La segunda objetos como requisito previos son servicios. Se representan los puertos de conexión RDP por cada servidor. Dado que el objeto de servicio existente RDP enlazado al puerto predeterminado RDP, 3389, nuevos servicios pueden crearse para permitir el tráfico de los puertos externos (8014 8026). Pueden que los nuevos puertos también se agregan al servicio RDP existente, pero para facilitar la demostración, se puede crear una regla individual para cada servidor. Para crear una nueva regla RDP para un servidor; empezando desde el panel de administración de NG Barracuda de cliente, vaya a la pestaña Configuración, en la sección Configuración operativas, haga clic en el conjunto de reglas, a continuación, haga clic en "Servicios" en el menú de objetos de Firewall, desplácese por la lista de servicios y seleccione el servicio "RDP". Haga clic en y haga clic en copiar, a continuación, haga clic en y seleccione Pegar. Ahora es un objeto de servicio Copy1 RDP que se pueden editar. Haga clic con el botón Copy1 RDP y seleccione Editar, modificar objeto servicio ventana emergente tal como se muestra aquí:

![Copia de la regla RDP predeterminada][5]

A continuación, se pueden modificar los valores para representar el servicio RDP para un servidor específico. Para AppVM01 la regla RDP predeterminada anterior debe modificarse para reflejar un nuevo nombre de servicio, la descripción y el puerto RDP externo utilizados en el diagrama de figura 8 (Nota: los puertos se cambian de RDP predeterminado de 3389 al puerto externo que se usa para este servidor específico, en el caso de AppVM01 el puerto externo es 8025) a continuación se muestra el servicio de modificación :

![Regla de AppVM01][6]
 
Este proceso se debe repetir para crear RDP servicios para los demás servidores; AppVM02, DNS01 y IIS01. La creación de estos servicios hará que la creación de reglas más simples y más obvios en la siguiente sección.

>[AZURE.NOTE] Un servicio RDP para el Firewall no es necesario por dos razones; 1) primero el firewall VM es una imagen de basados en Linux para SSH se utilizará en el puerto 22 para administración de VM en lugar de RDP y (2) puerto 22 y otros dos puertos de administración se permiten en la primera regla de administración se describe a continuación para permitir la conectividad de administración.

### <a name="firewall-rules-creation"></a>Creación de reglas de Firewall
Existen tres tipos de reglas de firewall utilizadas en este ejemplo, tienen iconos distintos:

La regla de aplicación redirigir: ![Redirigir el icono de aplicación][7]

La regla de NAT de destino: ![Icono NAT de destino][8]

La regla de paso: ![Pasar icono][9]

Obtener más información sobre estas reglas se puede encontrar en el sitio web de Barracuda.

Para crear las siguientes reglas (o comprobar reglas predeterminados existentes), empezando desde el panel de administración de Barracuda NG de cliente, vaya a la pestaña Configuración, en la configuración de operaciones sección, haga clic en el conjunto de reglas. Una cuadrícula denominada, "Principales reglas" mostrará las reglas activas y desactivadas existentes en este servidor. En la esquina superior derecha de la cuadrícula es un pequeño verde "+" del botón, haga clic en esta opción para crear una nueva regla (Nota: el firewall esté "bloqueado" para los cambios, si ve un botón marcado "Bloquear" y no puede crear o editar las reglas, haga clic en este botón para "desbloquear" el conjunto de reglas y permitir la edición). Si desea editar una regla existente, seleccione esa regla, haga clic en y seleccione Editar regla.

Una vez que las reglas se ha creado o modificadas, deben inserta el firewall y, a continuación, activa, si no lo hace los cambios de la regla no surtirá efecto. El proceso de inserción y la activación se describe a continuación las descripciones de detalles de la regla.

A continuación, se describen las características específicas de cada regla necesaria para completar este ejemplo:

- **Regla de administración de Firewall**: redirigir de aplicación esta regla permite el tráfico pasar a los puertos de administración del dispositivo virtual de red, en este ejemplo, un NextGen Firewall Barracuda. Los puertos de administración son 801, 807 y, opcionalmente, 22. Los puertos externos e internos son los mismos (es decir, sin traducción de puerto). Regla de administración de configuración de acceso, es una regla predeterminada y habilitado de forma predeterminada (en la versión de Barracuda NextGen Firewall 6.1).

    ![Regla de administración de Firewall][10]

>[AZURE.TIP] El espacio de direcciones de origen en esta regla es cualquiera, si se conocen los intervalos de direcciones IP de administración, la reducción de este ámbito también reduciría la superficie de ataque a los puertos de administración.

- **Reglas de RDP**: reglas estos NAT de destino le permitirá la administración de los servidores individuales a través de RDP.
Hay cuatro campos tareas críticas necesarios para crear esta regla:
  1.    Origen de para permitir RDP desde cualquier lugar, la referencia "Cualquiera" se usa en el campo de origen.
  2.    Servicio: use el objeto de servicio apropiado creado anteriormente, en este caso, "AppVM01 RDP", los puertos externos redirección a la dirección IP local de los servidores y al puerto 3386 (el puerto predeterminado RDP). Esta regla específica es para el acceso RDP a AppVM01.
  3.    Destino: debe ser el *puerto local en el firewall*, "DCHP 1 IP Local" o eth0 si usa direcciones IP estática. El número de ordinal (eth0, eth1, etcetera) pueden diferir si su dispositivo de red tiene varias interfaces locales. Este es el puerto que se está enviando el firewall de (puede ser el mismo que el puerto que recibe), el destino enrutado real está en el campo de la lista de objetivo.
  4.    Redirección: en esta sección indica el dispositivo virtual dónde finalmente redirigir este tráfico. La redirección más sencilla es colocar el IP y el puerto (opcional) en el campo de la lista de objetivo. Si no se utiliza el puerto será el puerto de destino de la solicitud entrante utiliza (es decir, sin traducción), si un puerto es el puerto designado también será NAT junto con la dirección IP dirección.

    ![Regla de Firewall RDP][11]

    Un total de cuatro reglas RDP tendrá que crear: 

  	|   Nombre de la regla    | Servidor  |   Servicio   |  Lista de objetivo  |
  	|----------------|---------|-------------|---------------|
  	| RDP a IIS01   | IIS01   | IIS01 RDP   | 10.0.1.4:3389 |
  	| RDP a DNS01   | DNS01   | DNS01 RDP   | 10.0.2.4:3389 |
  	| RDP a AppVM01 | AppVM01 | AppVM01 RDP | 10.0.2.5:3389 |
  	| RDP a AppVM02 | AppVM02 | AppVm02 RDP | 10.0.2.6:3389 |
  
>[AZURE.TIP] Limitar el ámbito de los campos de origen y servicio reducirá la superficie de ataque. Se debe utilizar el ámbito más limitado que le permitirá funcionalidad.

- **Las reglas de tráfico de aplicación**: hay dos reglas de tráfico de aplicación, la primera para el tráfico de web front-end y la segunda para el tráfico de back-end (por ejemplo, el servidor web a nivel de datos). Estas reglas se dependen de la arquitectura de red (donde se colocan los servidores) y flujos de tráfico (qué dirección se usan los flujos de tráfico y los puertos).

    En primer lugar analizados es la regla de front-end para el tráfico web:

    ![Regla de Firewall Web][12]
 
    Esta regla NAT de destino permite el tráfico de la aplicación real llegar al servidor de aplicaciones. Mientras que las demás reglas permiten seguridad, administración, etc., reglas de la aplicación son los que permiten usuarios externos o servicios tener acceso a las aplicaciones. En este ejemplo, hay un único servidor web en el puerto 80, por tanto, la regla de aplicación única firewall redirige el tráfico a la dirección IP externa a la dirección IP de web servidores interna.

    **Nota**: si no hay ningún puerto asignado en el campo de la lista de objetivo, por tanto, se usará el puerto entrante 80 (o 443 para el servicio seleccionado) en la redirección del servidor web. Si el servidor web está escuchando en un puerto diferente, por ejemplo, el puerto 8080, el campo de la lista de objetivo se actualizarán a 10.0.1.4:8080 para permitir la redirección del puerto.

    La siguiente regla el tráfico de aplicación es la regla de back-end para permitir que el servidor Web póngase en contacto con el servidor de AppVM01 (pero no AppVM02) a través de cualquier servicio:

    ![Regla de Firewall AppVM01][13]

    Esta regla paso permite que cualquier servidor IIS en la subred front-end para llegar a la AppVM01 (dirección IP 10.0.2.5) en cualquier puerto, utilizando cualquier protocolo de acceso a los datos necesarios para la aplicación web.

    En esta captura de pantalla de un "\<explícitas dest\>" se utiliza en el campo de destino para indicar 10.0.2.5 como destino. Esto podría ser explícitas denominarán tal como se muestra, o un objeto de la red (tal como se ha realizado en los requisitos previos para el servidor DNS). Se trata de hasta el administrador del servidor de seguridad acerca de qué método se utilizará. Para agregar 10.0.2.5 como un Explict Desitnation, haga doble clic en la primera fila en blanco en \<explícitas dest\> y escriba la dirección en la ventana que aparece.

    Con esta regla pasar, NAT no es necesario ya que es el tráfico interno, por lo que se puede establecer el método de conexión en "Sin SNAT".

    **Nota**: red de la fuente en esta regla es un recurso en la subred front-end, si sólo habrá uno o un número específico de servidores web, un recurso de red objeto se puede crear para que sea más específica a las direcciones IP exactas en lugar de la subred front-end completa.

>[AZURE.TIP] Esta regla usa el servicio de "Any" para facilitar la aplicación de ejemplo configurar y utilizar, esto también le permitirá ICMPv4 (ping) en una sola regla. Sin embargo, no es una práctica recomendada. Los puertos y protocolos ("servicios") se deben reducir al mínimo posible que permite la operación de la aplicación reducir la superficie de ataque a través de este límite.

<br />

>[AZURE.TIP] Aunque esta regla muestra una referencia explícita dest que se usa, se debe utilizar un enfoque coherente en toda la configuración de firewall. Se recomienda usar el objeto de red con nombre en para obtener compatibilidad y más fácil mejorar la legibilidad. La dest-explícitas solo se usa aquí para mostrar un método de referencia alternativo y no se recomienda (especialmente para configuraciones complejas).

- **Salida a Internet regla**: pasar esta regla permitirá el tráfico de red de cualquier origen para pasar a las redes de destino seleccionadas. Esta regla es una regla predeterminada normalmente ya en el firewall Barracuda NextGen, pero está deshabilitado. El botón secundario en esta regla, puede acceder al comando de activar la regla. Se ha modificado la regla que se muestra aquí para agregar los dos máscaras de subred locales que se crearon como referencias en la sección de este documento como requisito previo para el atributo de origen de esta regla.

    ![Regla de salida de Firewall][14]

- **Regla de DNS**: pasar esta regla permite sólo el tráfico DNS (puerto 53) pasar al servidor DNS. Para este entorno que mayor cantidad de tráfico desde el cliente para el servidor está bloqueado, esta regla permite específicamente DNS.

    ![Regla de DNS de Firewall][15]

    **Nota**: en esta pantalla se incluye captura el método de conexión. Porque esta regla es IP interna a interno tráfico de direcciones IP, no es necesaria ninguna traducción, esto el método de conexión se establece en "No SNAT" para esta regla de paso.

- **Regla de subred a**: pasar esta regla es una regla de predeterminada que se ha activado y modificar para permitir cualquier servidor en la subred back-end para conectarse a cualquier servidor en la subred front-end. Esta regla es el tráfico interno para que el método de conexión que se puede establecer en No SNAT.

    ![Regla de VNet dentro de un firewall][16]

    **Nota**: la casilla de verificación bidireccional no está protegido (ni está activado en la mayoría de las reglas), esto es importante para esta regla que hace esto "uno direccional" de la regla, se puede iniciar una conexión desde la subred back-end a la red de front-end, pero no a la inversa. Si se ha activado esa casilla de verificación, esta regla haría habilitar tráfico bidireccional, que desde nuestro diagrama lógico no deseado.

- **Denegar todas las reglas de tráfico**: siempre debe ser la regla final (en términos de prioridad) y, a continuación, como tal si un tráfico fluye correctamente para que coincida con cualquiera de los últimos reglas se quitarán por esta regla. Se trata de una regla predeterminada y normalmente no activado, modificaciones generalmente son necesarias. 

    ![Regla de denegar Firewall][17]

>[AZURE.IMPORTANT] Una vez que se crean todas las reglas anteriores, es importante revisar la prioridad de cada regla para asegurarse de tráfico se permitido o denegado como desee. En este ejemplo, las reglas están en el orden en que aparecen en la cuadrícula principal de reglas en el cliente de administración de Barracuda de reenvío.

## <a name="rule-activation"></a>Activación de regla
Con el conjunto de reglas modificado la especificación del diagrama lógica, debe ser cargado en el firewall y, a continuación, activa el conjunto de reglas.

![Activación de la regla de Firewall][18]
 
En la esquina superior derecha del cliente de administración son un clúster de botones. Haga clic en el botón "Enviar cambios" para enviar las reglas modificadas el firewall y luego haga clic en el botón "Activar".
 
Con la activación del conjunto de reglas de firewall esta versión de compilación del entorno de ejemplo está completa.

## <a name="traffic-scenarios"></a>Escenarios de tráfico
>[AZURE.IMPORTANT] Una clave takeway es recordar que vaya **todo** el tráfico a través del firewall. Por lo tanto a Escritorio remoto en el servidor IIS01, incluso si está en el servicio de nube Front-End y en la subred Front-End, para tener acceso a este servidor se deberá RDP al servidor de seguridad en el puerto 8014 y, a continuación, permitir el firewall enrutar la solicitud RDP internamente al puerto RDP IIS01. Botón de "Conectar" del portal Azure no funcionará porque no hay ninguna ruta de acceso directo de RDP a IIS01 (en lo que puede ver el portal). Esto significa que todas las conexiones de internet será el servicio de seguridad y un puerto, por ejemplo, secscv001.cloudapp.net:xxxx.

Para estos escenarios, deben ser las siguientes reglas de firewall en su lugar:

1.  Administración del Firewall
2.  RDP a IIS01
3.  RDP a DNS01
4.  RDP a AppVM01
5.  RDP a AppVM02
6.  Tráfico de aplicación en la Web
7.  Tráfico de aplicación a AppVM01
8.  Salida a Internet
9.  Front-end a DNS01
10. Tráfico de subred dentro (back-end a front-end solo)
11. Denegar a todos

El conjunto de reglas de firewall real probablemente tendrá muchas otras reglas Además, las reglas en cualquier firewall determinado también tendrán números de prioridad diferente que las que se muestra aquí. Esta lista y números asociados son proporcionar relevancia entre estas reglas de once y la prioridad relativa entre ellas. En otras palabras; en el firewall real, el "RDP a IIS01" puede ser regla número 5, pero siempre y cuando está por debajo de la regla "Administración del Firewall" y encima de la regla "RDP a DNS01" ¿Alinear con la intención de esta lista. También le ayudará a la lista de los escenarios que permite la mayor brevedad; siguientes Por ejemplo, "FW regla 9 (DNS)". También por razones de brevedad, las cuatro reglas RDP colectivamente se llamará "las reglas RDP" cuando el escenario de tráfico no está relacionado con RDP.

También recuerde que los grupos de seguridad de red son en lugar de tráfico de internet en las subredes front-end y back-end.

#### <a name="allowed-internet-to-web-server"></a>(Permitido) Internet para el servidor Web
1.  Página HTTP de solicitudes de usuario de Internet de SecSvc001.CloudApp.Net (servicio de nube opuestas de Internet)
2.  Pasadas de tráfico a través de un extremo abierto en el puerto 80 a la interfaz de firewall 10.0.0.4:80 del servicio de nube
3.  Sin GSN asignado a la subred de seguridad, por lo que deberá reglas GSN de sistema que admita el tráfico a firewall
4.  Tráfico llega a dirección IP interna del servidor de seguridad (10.0.1.4)
5.  Firewall comienza el procesamiento de la regla:
  1.    No aplicar, mover a la siguiente regla FW regla 1 (administración de FW)
  2.    Reglas de 2 a 5 (RDP reglas) no aplicar, mover a la siguiente regla
  3.    FW regla 6 (aplicación: Web) aplicar, se permite el tráfico, firewall NAT que 10.0.1.4 (IIS01)
6.  La subred front-end comienza el procesamiento de la regla de entrada:
  1.    GSN regla 1 (bloque de Internet) no se aplica (este tráfico fue NAT haría el firewall, por lo tanto la dirección de origen es ahora el firewall que se encuentra en la subred de seguridad y visto por la subred front-end GSN tráfico "local" y, por tanto, se permite), mover a la siguiente regla
  2.    Reglas de GSN predeterminado que admita el tráfico subred a, se permite el tráfico, detener el procesamiento de la regla GSN
7.  Iis01 escucha el tráfico de web, recibe esta solicitud y se inicia el procesamiento de la solicitud
8.  Iis01 intenta inicia una sesión FTP a AppVM01 de subred back-end
9.  La ruta UDR de subred front-end hace que el firewall siguiente salto
10. No hay reglas de salida en subred front-end, el tráfico está permitido
11. Firewall comienza el procesamiento de la regla:
  1.    No aplicar, mover a la siguiente regla FW regla 1 (administración de FW)
  2.    No aplicar, mover a la siguiente regla FW regla 2-5 (RDP reglas)
  3.    FW regla 6 (aplicación: Web) no aplicar, mover a la siguiente regla
  4.    FW regla 7 (aplicación: back-end) aplicar, se permite el tráfico, cortafuegos reenvía el tráfico a 10.0.2.5 (AppVM01)
12. La subred de back-end comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (bloque de Internet)
  2.    Reglas de GSN predeterminado que admita el tráfico subred a, se permite el tráfico, detener el procesamiento de la regla GSN
13.  AppVM01 recibe la solicitud y se inicia la sesión y responde
14. La ruta UDR de subred back-end hace que el firewall siguiente salto
15. Puesto que no hay ninguna regla GSN saliente en la subred de back-end que se permite la respuesta
16. Como esto devuelve el tráfico en una sesión establecida el firewall pasa la respuesta al servidor web (IIS01)
17. Front-end subred comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (bloque de Internet)
  2.    Reglas de GSN predeterminado que admita el tráfico subred a, se permite el tráfico, detener el procesamiento de la regla GSN
18. El servidor IIS recibe la respuesta, completa la transacción con AppVM01 y, a continuación, finalice la creación de la respuesta HTTP, esta respuesta HTTP se envía al solicitante
19. Puesto que no hay ninguna regla GSN saliente en la subred del cliente que se permite la respuesta
20. La respuesta HTTP llega el firewall y se acepta porque es la respuesta a una sesión NAT establecida por el firewall
21. El firewall, a continuación, redirige la respuesta al usuario de Internet
22. Puesto que hay saliente no GSN reglas o saltos UDR en la subred del cliente se permite la respuesta y el usuario de Internet recibe la página web solicitado.

#### <a name="allowed-internet-rdp-to-backend"></a>(Permitido) Internet RDP a back-end
1.  Administrador de servidor en internet solicita sesión RDP AppVM01 a través de SecSvc001.CloudApp.Net:8025, donde 8025 es el número de puerto de usuario asignado para la regla de firewall "RDP a AppVM01"
2.  El servicio de nube pasa el tráfico a través del extremo abierto en el puerto 8025 a la interfaz de firewall en 10.0.0.4:8025
3.  Sin GSN asignado a la subred de seguridad, por lo que deberá reglas GSN de sistema que admita el tráfico a firewall
4.  Firewall comienza el procesamiento de la regla:
  1.    No aplicar, mover a la siguiente regla FW regla 1 (administración de FW)
  2.    No aplicar, mover a la siguiente regla FW regla 2 (RDP IIS)
  3.    No aplicar, mover a la siguiente regla FW regla 3 (RDP DNS01)
  4.    Aplicar FW regla 4 (RDP AppVM01), se permite el tráfico, firewall NAT que 10.0.2.5:3386 (puerto RDP en AppVM01)
5.  La subred de back-end comienza el procesamiento de la regla de entrada:
  1.    GSN regla 1 (bloque de Internet) no se aplica (este tráfico fue NAT haría el firewall, por lo tanto la dirección de origen es ahora el firewall que se encuentra en la subred de seguridad y visto por la subred back-end GSN tráfico "local" y, por tanto, se permite), mover a la siguiente regla
  2.    Reglas de GSN predeterminado que admita el tráfico subred a, se permite el tráfico, detener el procesamiento de la regla GSN
6.  AppVM01 escucha el tráfico RDP y responde
7.  Con ninguna regla GSN saliente, aplicarán reglas de forma predeterminada y se permite el tráfico de retorno
8.  UDR enruta el tráfico saliente para el servidor de seguridad como el próximo salto
9.  Como esto devuelve el tráfico en una sesión establecida el firewall pasa la respuesta al usuario de internet
10. Sesión RDP está habilitado
11. AppVM01 solicita contraseña de nombre de usuario

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Permitido) Búsqueda de servidor DNS de Web en el servidor DNS
1.  Web Server, IIS01, necesidades de datos de una fuente en www.data.gov, pero debe resolver la dirección.
2.  La configuración de red para las listas de VNet DNS01 (10.0.2.4 en la subred back-end) como el servidor DNS principal, IIS01 envía la solicitud DNS a DNS01
3.  UDR enruta el tráfico saliente para el servidor de seguridad como el próximo salto
4.  Ninguna regla GSN salida enlazada a la subred front-end, el tráfico está permitido
5.  Firewall comienza el procesamiento de la regla:
  1.    No aplicar, mover a la siguiente regla FW regla 1 (administración de FW)
  2.    No aplicar, mover a la siguiente regla FW regla 2-5 (RDP reglas)
  3.    FW reglas 6 y 7 (aplicación de reglas) no aplicar, mover a la siguiente regla
  4.    No aplicar, mover a la siguiente regla FW regla 8 (a Internet)
  5.    Aplicar regla 9 (DNS) de FW, se permite el tráfico, cortafuegos reenvía el tráfico a 10.0.2.4 (DNS01)
6.  La subred de back-end comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (bloque de Internet)
  2.    Reglas de GSN predeterminado que admita el tráfico subred a, se permite el tráfico, detener el procesamiento de la regla GSN
7.  Servidor DNS recibe la solicitud
8.  Servidor DNS no tiene la dirección en caché y se le pide a un servidor DNS raíz en internet
9.  UDR enruta el tráfico saliente para el servidor de seguridad como el próximo salto
10. Ninguna regla GSN saliente de subred back-end, el tráfico está permitido
11. Firewall comienza el procesamiento de la regla:
  1.    No aplicar, mover a la siguiente regla FW regla 1 (administración de FW)
  2.    No aplicar, mover a la siguiente regla FW regla 2-5 (RDP reglas)
  3.    FW reglas 6 y 7 (aplicación de reglas) no aplicar, mover a la siguiente regla
  4.     Aplicar FW regla 8 (a Internet), se permite el tráfico, sesión es SNAT el servidor DNS raíz en Internet
12. Servidor DNS de Internet responde, ya que esta sesión iniciada desde el firewall, la respuesta se acepta por el firewall
13. Como se trata de una sesión establecida, el firewall reenvía la respuesta en el servidor inicial, DNS01
14. La subred de back-end comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (bloque de Internet)
  2.    Reglas de GSN predeterminado que admita el tráfico subred a, se permite el tráfico, detener el procesamiento de la regla GSN
15. El servidor DNS recibe y almacena en caché la respuesta y, a continuación, se responde a la solicitud inicial a IIS01
16. La ruta UDR de subred back-end hace que el firewall siguiente salto
17. No salientes GSN hay reglas en la subred back-end, el tráfico está permitido
18. Se trata de una sesión establecida en el firewall, reenvía la respuesta del firewall al servidor IIS
19. Front-end subred comienza el procesamiento de la regla de entrada:
  1.    No hay ninguna regla GSN que se aplica a entrante tráfico de la subred back-end a la subred front-end, por lo que ninguna de la GSN aplican las reglas
  2.    La regla de sistema predeterminada permitir el tráfico entre subredes ¿permitir que este tráfico para que permita el tráfico
20. Iis01 recibe la respuesta DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Permitido) Servidor back-end al servidor front-end
1.  Un administrador ha iniciado sesión AppVM02 a través de RDP solicita un archivo directamente desde el servidor de IIS01 mediante el Explorador de archivos de windows
2.  La ruta UDR de subred back-end hace que el firewall siguiente salto
3.  Puesto que no hay ninguna regla GSN saliente en la subred de back-end que se permite la respuesta
4.  Firewall comienza el procesamiento de la regla:
  1.    No aplicar, mover a la siguiente regla FW regla 1 (administración de FW)
  2.    No aplicar, mover a la siguiente regla FW regla 2-5 (RDP reglas)
  3.    FW reglas 6 y 7 (aplicación de reglas) no aplicar, mover a la siguiente regla
  4.    No aplicar, mover a la siguiente regla FW regla 8 (a Internet)
  5.    No aplicar, mover a la siguiente regla FW regla 9 (DNS)
  6.    Aplicar FW regla 10 (dentro de subred), se permite el tráfico, firewall pasa tráfico a 10.0.1.4 (IIS01)
5.  Front-end subred comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (bloque de Internet)
  2.    Reglas de GSN predeterminado que admita el tráfico subred a, se permite el tráfico, detener el procesamiento de la regla GSN
6.  Suponiendo que autorización y la autenticación apropiada, IIS01 acepta la solicitud y responde
7.  La ruta UDR de subred front-end hace que el firewall siguiente salto
8.  Puesto que no hay ninguna regla GSN saliente en la subred del cliente que se permite la respuesta
9.  Como se trata de una sesión existente en el servidor de seguridad se permite esta respuesta y el firewall devuelve la respuesta al AppVM02
10. Back-end subred comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (bloque de Internet)
  2.    Reglas de GSN predeterminado que admita el tráfico subred a, se permite el tráfico, detener el procesamiento de la regla GSN
11. AppVM02 recibe la respuesta

#### <a name="denied-internet-direct-to-web-server"></a>(Denegado) Internet directa al servidor Web
1.  Usuario de Internet intenta tener acceso el servidor web, IIS01, a través del servicio de FrontEnd001.CloudApp.Net
2.  Puesto que no hay ningún extremo abierto para el tráfico HTTP, esto no debería pasar a través del servicio de nube y no puede conectar con el servidor
3.  Si los extremos estuvieran abiertos por algún motivo, el GSN (bloque de Internet) en la subred front-end bloqueará este tráfico
4.  Por último, la ruta UDR de subred front-end ' d enviar todo el tráfico saliente de IIS01 al servidor de seguridad como el próximo salto y el firewall podría ver esto como tráfico asimétrico y colocar la respuesta saliente, por tanto, hay al menos tres capas independientes de defensa entre internet y IIS01 a través de su servicio de nube de evitar el acceso no autorizado inadecuadas.

#### <a name="denied-internet-to-backend-server"></a>(Denegado) Internet para el servidor back-end
1.  Usuario de Internet intenta obtener acceso a un archivo en AppVM01 a través del servicio de BackEnd001.CloudApp.Net
2.  Puesto que no hay ningún extremo abierto para el recurso compartido de archivos, esto no podría pasar por el servicio de nube y no puede conectar con el servidor
3.  Si los extremos estuvieran abiertos por algún motivo, el GSN (bloque de Internet) bloqueará este tráfico
4.  Por último, la ruta UDR ¿enviar el tráfico saliente de AppVM01 al firewall como el próximo salto y el firewall podría ver esto como tráfico asimétrico y colocar la respuesta saliente, por tanto, hay al menos tres capas independientes de defensa entre internet y AppVM01 a través de su servicio de nube de evitar el acceso no autorizado inadecuadas.

#### <a name="denied-frontend-server-to-backend-server"></a>(Denegado) Servidor front-end al servidor back-end
1.  Supongamos que IIS01 se ha comprometido y ejecute código malintencionado intentar analizar los servidores de subred back-end.
2.  La ruta UDR de subred front-end debe enviar el tráfico saliente de IIS01 al firewall como el próximo salto. No es algo que puede ser modificado por la VM en riesgo.
3.  El firewall procesa el tráfico, si la solicitud se para AppVM01 o para el servidor DNS para las búsquedas DNS que posiblemente se permite el tráfico por el firewall (debido a FW reglas 7 y 9). Todo el tráfico se bloqueará por FW regla 11 (denegar todo).
4.  Si detección de amenaza avanzada se ha habilitado en el firewall (que no se trata en este documento, consulte la documentación del proveedor para su dispositivo de red específica amenaza capacidades avanzadas), no podrán tráfico de par podrán las reglas de reenvío básicas descritas en este documento si el tráfico contenía firmas conocidas o patrones que marcar una regla avanzada.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Denegado) Búsqueda de DNS de Internet en el servidor DNS
1.  Usuario de Internet intenta buscar un registro DNS interno en DNS01 a través del servicio de BackEnd001.CloudApp.Net 
2.  Puesto que no hay ningún extremos abiertos para el tráfico DNS, esto no debería pasar a través del servicio de nube y no puede conectar con el servidor
3.  Si los extremos estuvieran abiertos por algún motivo, la regla GSN (bloque de Internet) en la subred front-end bloqueará este tráfico
4.  Por último, la ruta de back-end subred UDR ¿enviar el tráfico saliente de DNS01 al firewall como el próximo salto y el firewall podría ver esto como tráfico asimétrico y colocar la respuesta saliente, por tanto, hay al menos tres capas independientes de defensa entre internet y DNS01 a través de su servicio de nube de evitar el acceso no autorizado inadecuadas.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Denegado) Internet Access SQL a través del Firewall
1.  Usuario de Internet solicita datos SQL de SecSvc001.CloudApp.Net (servicio de nube opuestas de Internet)
2.  Puesto que no hay ningún extremo abierto para SQL, esto podría no pasar por el servicio de nube y no alcance el firewall
3.  Si los extremos SQL estaban abiertos por algún motivo, el firewall puede comenzar procesamiento de la regla:
  1.    No aplicar, mover a la siguiente regla FW regla 1 (administración de FW)
  2.    Reglas de 2 a 5 (RDP reglas) no aplicar, mover a la siguiente regla
  3.    FW regla 6 y 7 (aplicación de reglas) no aplicar, mover a la siguiente regla
  4.    No aplicar, mover a la siguiente regla FW regla 8 (a Internet)
  5.    No aplicar, mover a la siguiente regla FW regla 9 (DNS)
  6.    No aplicar, mover a la siguiente regla FW regla 10 (dentro de subred)
  7.    Aplicar FW regla 11 (denegar a todos), el tráfico es procesamiento de la regla bloqueados, detener


## <a name="references"></a>Referencias
### <a name="main-script-and-network-config"></a>Secuencia de comandos principal y configuración de red
Guardar la secuencia de comandos completa en un archivo de script de PowerShell. Guardar la configuración de red en un archivo denominado "NetworkConf2.xml".
Modifique las variables definidas por el usuario, según sea necesario. Ejecute la secuencia de comandos, a continuación, siga las instrucciones de configuración de regla de Firewall anteriores.

#### <a name="full-script"></a>Secuencia de comandos completa
Podrá esta secuencia de comandos, en función de las variables definidas por el usuario:

1.  Conectarse a una suscripción de Azure
2.  Crear una nueva cuenta de almacenamiento
3.  Crear un nuevo VNet y tres subredes según se define en el archivo de configuración de red
4.  Crear cinco máquinas virtuales; 1 firewall y 4 windows server VM
5.  Configurar UDR incluidos:
  1.    Crear dos nuevas tablas de ruta
  2.    Agregar rutas a las tablas
  3.    Enlazar tablas a subredes adecuadas
6.  Habilitar el reenvío de IP en el NVA
7.  Configurar GSN incluidos:
  1.    Crear un GSN
  2.    Agregar una regla
  3.    Enlazar el GSN a las subredes adecuadas

Esta secuencia de comandos de PowerShell debe ejecutarse localmente en conectado a internet PC o servidor.

>[AZURE.IMPORTANT] Cuando se ejecuta esta secuencia de comandos, puede haber advertencias u otros mensajes informativos que pop en PowerShell. Sólo mensajes de error en rojo son la causa de problema.

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA
    
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.
    
      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4
     
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"
    
    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #
    
      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }
    
    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan
    
      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"
    
      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal
    
      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName
    
     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
    
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
    

#### <a name="network-config-file"></a>Archivo de configuración de red
Guardar este archivo xml con ubicación actualizada y agregar el vínculo a este archivo a la variable $NetworkConfigFile en la secuencia de comandos anterior.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Secuencias de comandos de aplicación de ejemplo
Si desea instalar una aplicación de ejemplo para este y otros ejemplos DMZ, se haya proporcionado uno en el siguiente vínculo: [Secuencia de comandos de aplicación][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "DMZ bidireccional con NVA, NSG y UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Vista lógica de las reglas de Firewall"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Crear un objeto de red del cliente"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Crear un objeto de servidor DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Copia de la regla RDP predeterminada"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Regla de AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Icono de redirección de la aplicación"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Icono NAT de destino"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Icono de paso"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Regla de administración de Firewall"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Regla de Firewall RDP"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Regla de Firewall Web"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Regla de Firewall AppVM01"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Regla de salida de Firewall"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Regla de DNS de Firewall"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Regla de VNet dentro de un firewall"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Regla de denegar Firewall"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Activación de la regla de Firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
