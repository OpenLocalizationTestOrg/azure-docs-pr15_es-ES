<properties 
    pageTitle="Integrar una aplicación con una red Virtual de Azure" 
    description="Muestra cómo conectarse a una aplicación de servicio de la aplicación de Azure a una red Azure virtual nueva o existente" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor="cephalin"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="ccompy"/>

# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar su aplicación con una red Virtual de Azure #

Este documento describe la característica de integración de red virtual de servicio de la aplicación de Azure y muestra cómo configurar con aplicaciones de [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).  Si no está familiarizado con las redes de Azure Virtual (VNETs), esta es una función que permite colocar muchos de los recursos de Azure en una red de enrutables de internet no puede controlar el acceso a.  Estas redes, a continuación, se pueden conectar con sus redes local activado con una amplia variedad de tecnologías VPN.  Para obtener más información acerca de las redes virtuales Azure iniciar con la información aquí: [Información general de red Virtual Azure][VNETOverview].  

El servicio de aplicación de Azure tiene dos formas.  

1. Los sistemas de múltiples arrendatarios que admiten la completa gama de planes de precios
1. La característica de premium entorno de servicio de aplicación (ASE) que se implementa en su VNET.  

Este documento trata mediante la integración de VNET y no el entorno de servicios de aplicación.  Si desea obtener más información sobre la característica ASE, a continuación, comience con la información aquí: [Introducción de la aplicación de servicio de entorno][ASEintro].

Integración de VNET permite el acceso de la aplicación web a los recursos de su red virtual pero no concede acceso privado a su aplicación web de la red virtual.  Acceso a sitios privada solo está disponible con un ASE configurado con un equilibrador de carga interno (ILB).  Para obtener información detallada sobre el uso de un ASE ILB, comience con el artículo aquí: [crear y usar un ASE ILB][ILBASE]. 

Un escenario común donde usaría VNET integración es permitir el acceso desde la aplicación web a una base de datos o un servicios web que se ejecutan en una máquina virtual de su red virtual Azure.  Con la integración de VNET no es necesario exponer un extremo público para aplicaciones en la máquina virtual pero se puede utilizar las direcciones de internet no enrutables privadas en su lugar.  

La característica de integración de VNET:

- requiere un estándar o Premium plan de precios 
- funcionará con Classic(V1) o VNET Manager(V2) de recursos 
- es compatible con TCP y UDP
- funciona con las aplicaciones Web, móvil y API
- permite a una aplicación para conectarse a VNET sólo 1 a la vez
- permite un máximo de 5 VNETs que se integra con en un Plan de servicio de aplicación 
- permite la misma VNET usarlo con varias aplicaciones en un Plan de servicio de aplicación
- es compatible con un SLA 99,9% debido a una dependencia de la puerta de enlace VNET

Hay algunas cosas que no admite VNET integración incluidos:

- montaje de una unidad
- Integración de AD 
- NetBios
- acceso al sitio privado

### <a name="getting-started"></a>Introducción ###
Estas son algunas cosas que debe tener en cuenta antes de la aplicación web de conectarse a una red virtual:

- Integración de VNET solo funciona con aplicaciones en **Standard** o **Premium** plan de precios.  Si habilita la característica y cambiar la escala de su Plan de servicios de aplicación a un plan de precio no admitido sus aplicaciones perderán sus conexiones con el VNETs están usando.  
- Si su red virtual de destino ya existe, debe tener VPN de sitio a punto habilitado con una puerta de enlace enrutamiento dinámico antes de que se pueden conectar a una aplicación.  No se puede habilitar la red privada Virtual (VPN) de sitio a punto si la puerta de enlace está configurado con enrutamiento estático.
- El VNET debe estar en la misma suscripción como su Plan(ASP) de servicio de aplicación.  
- Las aplicaciones que se integran con un VNET usará el DNS especificado para ese VNET.
- De forma predeterminada las aplicaciones de integración solo enruta el tráfico a su VNET según las rutas que se definen en la VNET.  


## <a name="enabling-vnet-integration"></a>Habilitar la integración de VNET ##

Este documento se centra principalmente en con el Portal de Azure para la integración de VNET.  Para habilitar la integración de VNET con la aplicación con PowerShell, siga las instrucciones aquí: [conectar la aplicación a su red virtual con PowerShell][IntPowershell].

Tiene la opción para conectar la aplicación a una red virtual nueva o existente.  Si crea una nueva red como parte de la integración, a continuación, además de crear el VNET, una puerta de enlace enrutamiento dinámico será preconfigurado para usted y se habilitarán, seleccione sitio VPN.  

>[AZURE.NOTE] Configurar una nueva integración de una red virtual puede tardar varios minutos.  

Para habilitar integración VNET abrir la aplicación de configuración y, a continuación, seleccione red.  La interfaz de usuario que abre ofrece tres opciones de red.  Esta guía sólo va a VNET integración aunque híbrido conexiones y entornos de aplicación de servicio se describen más adelante en este documento.  

Si su aplicación es no en el correcto de precios plan de la interfaz de usuario ofrece ayuda permiten ajustar el tamaño de su plan a un plan de precio superior de su elección.


![][1]
 
###<a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Habilitar la integración de VNET con un VNET ya existente###
La interfaz de usuario de integración de VNET le permite seleccionar de una lista de sus VNETs.  La VNETs clásica indicará que son como con la palabra "Clásico" entre paréntesis junto al nombre VNET.  Por ejemplo, que el Administrador de recursos de VNETs aparece en primer lugar, se ordena la lista.  En la imagen que se muestra a continuación puede ver que solo VNET se puede seleccionar.  Existen varios motivos que un VNET estará desactivada incluidos:

- la VNET está en otra suscripción de su cuenta tiene acceso a
- la VNET no tiene un punto al sitio habilitado
- la VNET no tiene una puerta de enlace enrutamiento dinámico


![][2]

Para habilitar la integración simplemente haga clic en el VNET que desea integrar con.  Después de seleccionar la VNET, la aplicación se iniciará automáticamente para que los cambios surtan efecto.  

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Habilitar, seleccione sitio en un VNET clásico #####
Si no tiene una puerta de enlace de su VNET ni con punto al sitio tiene configurar primero.  Para hacer esto para un VNET clásica, vaya al [Portal de Azure] [ AzurePortal] y visualice la lista de Networks(classic) Virtual.  Desde aquí, haga clic en la red que desea integrar con y haga clic en el cuadro grande en Essentials denominado conexiones VPN.  Desde aquí puede crear su punto para VPN del sitio e incluso que crear una puerta de enlace.  Después de ir al sitio de la experiencia de creación de puerta de enlace a través del punto será unos 30 minutos antes de que esté listo.  

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Habilitar, seleccione sitio en un VNET Administrador de recursos #####

Configurar un VNET Administrador de recursos con una puerta de enlace y elija sitio debe usar PowerShell como se indica a continuación, [Configurar una conexión punto a sitio a una red virtual con PowerShell][V2VNETP2S].  La interfaz de usuario para realizar esta función no está disponible. 

### <a name="creating-a-pre-configured-vnet"></a>Crear un entorno VNET ###
Si desea crear un nuevo VNET que está configurado con una puerta de enlace y punto al sitio, el servicio de aplicación de interfaz de usuario de red tiene la capacidad de hacer pero sólo para un administrador de recursos VNET.  Si desea crear un VNET clásica con una puerta de enlace y un sitio de punto debe hacerlo manualmente mediante la interfaz de usuario de red. 

Para crear un VNET Administrador de recursos a través de la interfaz de usuario de integración de VNET, simplemente seleccione **Crear nueva red Virtual** y proporcione la:

- Nombre de red virtual
- Bloque de direcciones de red virtual
- Nombre de subred
- Bloque de direcciones de subred
- Bloque de direcciones de la puerta de enlace
- Bloque de direcciones del sitio de punto

Si desea que esta VNET para conectarse a cualquiera de sus otras redes debe evitar espacio de direcciones IP que se superpone con las redes de selección.  

>[AZURE.NOTE] Creación de VNET Administrador de recursos con una puerta de enlace tardará unos 30 minutos y actualmente no integrará la VNET con la aplicación.  Una vez creado el VNET con la puerta de enlace debe volver a la aplicación de la interfaz de usuario de integración de VNET y seleccione el nuevo VNET.

![][3]

Azure VNETs normalmente se crean en las direcciones de una red privada.  De forma predeterminada, la integración de VNET característica usará para enrutar el tráfico destinado para los intervalos de direcciones IP en su VNET.  Los intervalos de direcciones IP privados son:

- 10.0.0.0/8 - es el mismo que 10.0.0.0 - 10.255.255.255
- 172.16.0.0/12 - es el mismo que 172.16.0.0 - 172.31.255.255 
- 192.168.0.0/16 - es el mismo que 192.168.0.0 - 192.168.255.255
 
El espacio de direcciones VNET debe especificarse en notación CIDR.  Si no está familiarizado con la notación CIDR, es un método para especificar los bloques de direcciones con una dirección IP y un número entero que representa la máscara de red. Como referencia rápida, considere la posibilidad de que 10.1.0.0/24 sería 256 direcciones y 10.1.0.0/25 sería 128 direcciones.  Una dirección de IPv4 con un /32 sería simplemente 1 dirección.  

Si establece la información del servidor DNS aquí que se establecerá para su VNET.  Después de la creación de VNET puede editar esta información de la experiencia de usuario VNET.

Cuando se crea un VNET clásico mediante la interfaz de usuario de integración de VNET, creará un VNET en el mismo grupo de recursos que la aplicación. 

## <a name="how-the-system-works"></a>Cómo funciona el sistema ##
En el interior esta característica se basa en la tecnología de punto a sitio VPN para conectar la aplicación a su VNET.   Aplicaciones de servicio de la aplicación de Azure tienen una arquitectura de sistema de múltiples arrendatarios que evite el aprovisionamiento de una aplicación directamente en un VNET tal como se hace con máquinas virtuales de Windows.  Mediante la creación de la tecnología de punto a sitio se limitar el acceso de red a solo la máquina virtual de la aplicación de hospedaje.  Acceso a la red está aún más restringido de esos hosts de aplicación para que sus aplicaciones solo pueden tener acceso a las redes que configurarlos para acceder a.  

![][4]
 
Si todavía no lo ha configurado un servidor DNS con su red virtual debe utilizar direcciones IP.  Con el uso de direcciones IP, recuerde que las principales ventajas de esta característica le permite utilizar las direcciones privadas dentro de su red privada.  Si ha configurado su aplicación para que use las direcciones IP pública para uno de sus máquinas virtuales y no está usando la característica de integración de VNET y se comunican a través de internet.


##<a name="managing-the-vnet-integrations"></a>Administrar las integraciones VNET##

La capacidad de conectar y desconectar a un VNET está en un nivel de aplicación.  Las operaciones que pueden afectar a la integración de VNET entre varias aplicaciones están en un nivel ASP.  Puede obtener detalles en su VNET de la interfaz de usuario que se muestra en el nivel de aplicación.  La mayoría de la misma información también se muestra en el nivel ASP.  

![][5]

Desde la página de estado de la característica de red puede ver si la aplicación está conectada a su VNET.  Si la puerta de enlace VNET es hacia abajo para cualquier razón, a continuación, se mostrará como no conectado.  

La información que tiene ahora disponibles en la aplicación de que interfaz de usuario de nivel VNET integración es idéntica a la información de detalle que se obtiene desde ASP.  Estos son los elementos:

- Nombre VNET - este vínculo abre la la interfaz de usuario de red
- Ubicación: Esto refleja la ubicación de su VNET.  Es posible integrar con un VNET en otra ubicación.
- Estado de certificado - hay certificados que se utilizan para proteger la conexión VPN entre la aplicación y la VNET.  Esto refleja una prueba para asegurarse de que están sincronizados.
- Estado de la puerta de enlace - las puertas de enlace debería hacia abajo por cualquier razón, a continuación, la aplicación no puede acceder a recursos en el VNET.  
- Espacio de direcciones VNET - este es el espacio de direcciones IP para su VNET.  
- Elija espacio de dirección del sitio: este es el punto de espacio de direcciones IP de sitio para su VNET.  La aplicación mostrará comunicación como procedentes de una de las direcciones IP en el espacio de direcciones.  
- Sitio de espacio de dirección del sitio: puede usar VPN de sitio a sitio para su VNET para conectarse a los recursos locales en o a otros VNETs.  Si tiene que configurar, a continuación, los intervalos de IP definen con conexión VPN mostrará aquí.
- Servidores DNS - si ha configurado con su VNET, a continuación, se enumeran los servidores DNS.
- Direcciones IP que se enrutan a la VNET - allí son una lista de direcciones IP que su VNET tiene definido para el enrutamiento.  Las direcciones se mostrarán aquí.  

La única operación que puede realizar en la vista de la aplicación de la integración de VNET es que desconectar el VNET está conectado actualmente a la aplicación.  Para hacer esto simplemente haga clic en Desconectar en la parte superior.  Esta acción no cambia su VNET.  La configuración de VNET y su incluidas las puertas de enlace permanece sin modificar.  Si desea eliminar su VNET debe eliminar primero los recursos, incluidas las puertas de enlace.  

La vista Plan de servicio de aplicación tiene un número de operaciones adicionales.  También se accede diferente que desde la aplicación.  Para llegar a la interfaz de usuario de la red de ASP simplemente abra su interfaz de usuario de ASP y desplácese hacia abajo.  Hay un elemento de interfaz de usuario llamado el estado de la característica de red.  Le dará algunos detalles secundarias alrededor de la integración de VNET.  Al hacer clic en esta interfaz de usuario, abre la interfaz de usuario de estado de característica de red.  Si hace clic en "Haga clic aquí para administrar" se abrirá la interfaz de usuario que enumera las integraciones VNET en este ASP.

![][6]

La ubicación del ASP es una buena idea recordar cuando examina las ubicaciones de la VNETs se integra con.  Cuando el VNET está en otra ubicación es mucho más probable que ver problemas de latencia.  

El VNETs integrados con es un aviso en cuántos VNETs sus aplicaciones se integran con en este ASP y cuántas puede tener.  

Para ver los detalles adicionales en cada VNET, haga clic en el VNET que le interesa.  Además de los detalles que se han indicado que también verá una lista de las aplicaciones en este ASP que utiliza ese VNET.  

Con respecto a acciones existen dos acciones principales.  La primera es la capacidad de agregar rutas que conducir tráfico salir de la aplicación en su VNET.  La segunda acción es la capacidad para sincronizar la información de la red y los certificados.

![][7]

**Enrutamiento** Como se mencionó anteriormente las rutas que se definen en su VNET son lo que se usa para dirigir el tráfico a su VNET desde la aplicación.  Hay algunos usos aunque donde los clientes desean enviar el tráfico saliente adicional desde una aplicación en la VNET y para ellos esta capacidad se proporciona.  ¿Qué sucede con el tráfico después de depende de cómo el cliente configura su VNET.  

**Certificados** El estado de certificado refleja una comprobación llevando a cabo el servicio de aplicación para validar que los certificados que usamos para la conexión VPN están conveniente todavía.  Cuando se habilita la integración de VNET, a continuación, si es la primera integración a ese VNET desde las aplicaciones en este ASP, hay un exchange requiere de certificados para garantizar la seguridad de la conexión.  Junto con los certificados que disponer de la configuración de DNS, rutas y otras cosas similares que describen la red.
Si cambia los certificados o la información de la red deberá hacer clic en "Red de sincronización".  **Nota**: al hacer clic en "Red de sincronización", a continuación, provocará una breve interrupción de conectividad entre su aplicación y su VNET.  Mientras que la aplicación no se iniciará la pérdida de conectividad podría provocar su sitio no funcionen correctamente.  

##<a name="accessing-on-premise-resources"></a>Obtener acceso a los recursos locales##

Una de las ventajas de la característica de integración de VNET es si su VNET está conectado a su red local activado con una VPN de sitio a otro, a continuación, las aplicaciones pueden tener acceso a los recursos locales en desde la aplicación.  Para que funcione aunque tendrá que actualizar la puerta de enlace VPN local en con las rutas de su sitio IP, seleccione rango.  Cuando la conexión VPN para cada sitio se configure en primer lugar deben establecer las secuencias de comandos que se usa para configurarla rutas incluyendo el punto de la VPN de sitio.  Si agrega el punto de VPN de sitio después de su crear su VPN de sitio a otro, a continuación, deberá actualizar las rutas de forma manual.  Obtener más información sobre cómo hacer varía por la puerta de enlace y no se describe aquí.  

>[AZURE.NOTE] Mientras que la característica de integración de VNET funcionará con una VPN de sitio a sitio para tener acceso a los recursos locales que actualmente no funciona con una VPN ExpressRoute para hacer lo mismo.  Esto es verdadero cuando se integra con un clásico o el Administrador de recursos VNET.  Si necesita tener acceso a los recursos a través de una VPN ExpressRoute puede usar un ASE que puede ejecutar en su VNET. 

##<a name="pricing-details"></a>Detalles de precios##
Hay algunos matices que debe tener en cuenta al usar la característica de integración de VNET de precios.  Hay 3 cargos relacionados con el uso de esta característica:

- Requisitos del nivel de precios de ASP
- Costes de transferencia de datos
- Costos de puerta de enlace VPN.

Para que las aplicaciones poder usar esta característica, deben estar en un Plan de servicio de aplicación Premium o estándar.  Puede ver más detalles sobre esos costos aquí: [Aplicación de servicio de precios][ASPricing]. 

Debido a la forma punto VPN sitio está controlado, siempre tiene un cargo para datos salientes a través de su conexión de integración de VNET incluso si la VNET está en el mismo centro de datos.  Para ver qué esos gastos se Eche un vistazo aquí: [Detalles de precios de transferencia de datos][DataPricing].  

El último elemento es el coste de las puertas de enlace VNET.  Si no necesita las puertas de enlace algo como VPN de sitio a sitio está pagando para que puertas de enlace admitir la característica de integración de VNET.  No hay detalles en los costes aquí: [Precios de puerta de enlace VPN][VNETPricing].  

##<a name="troubleshooting"></a>Solución de problemas##

Mientras que la característica es fácil de configurar que no significa que su experiencia será problema gratuita.  Si encuentra problemas de acceso a su extremo deseado allí son algunas herramientas que puede usar para probar la conectividad desde la consola de aplicación.  Existen dos experiencias de consola que puede usar.  Una es desde la consola de Kudu y la otra es la consola que puede ponerse en contacto en el Portal de Azure.  Para ir a la consola de Kudu desde la aplicación vaya a Herramientas -> Kudu.  Esto es lo mismo que va a [nombre]. scm.azurewebsites.net.  Una vez que se abre simplemente vaya a la pestaña de la consola de depuración.  Para obtener la consola hospedado portal Azure, a continuación, desde la aplicación vaya a Herramientas -> consola.  


####<a name="tools"></a>Herramientas####

Las herramientas ping, nslookup y tracert no funcionarán a través de la consola debido a las restricciones de seguridad.  Para rellenar la allí anular han sido dos herramientas separadas agregados.  Para probar la funcionalidad DNS hemos agregado una herramienta denominada nameresolver.exe.  La sintaxis es:

    nameresolver.exe hostname [optional: DNS Server]

Puede usar nameresolver para comprobar los nombres de host que depende de la aplicación.  Esta forma puede probar si tiene consecuencias mal configurados con DNS o tal vez no tiene acceso al servidor DNS.

La herramienta siguiente le permite probar la conectividad TCP en una combinación de host y puerto.  Esta herramienta se denomina tcpping.exe y la sintaxis es:

    tcpping.exe hostname [optional: port]

Esta herramienta le indicará que si puede llegar a un host específico y puerto pero no se realizará la misma tarea que obtiene con el ICMP según la utilidad ping.  La utilidad de ping ICMP le indicará si el host es hacia arriba.  Con tcpping Descubra si puede obtener acceso a un puerto específico en un host.  


####<a name="debugging-access-to-vnet-hosted-resources"></a>Acceso a VNET de depuración hospedado recursos####

Hay una serie de cosas que pueden impedir que la aplicación se comunique con un host específico y puerto.  La mayoría de las veces es una de las tres acciones:

- **Hay un firewall de la forma**  Si tiene un firewall de la forma se presionar el tiempo de espera TCP.  En este caso es 21 segundos.  Use la herramienta tcpping para probar la conectividad.  Tiempos de espera TCP pueden deberse a muchas cosas más allá de los firewalls pero iniciar allí.  
- **DNS es no accesibles**  El tiempo de espera DNS es de 3 segundos por el servidor DNS.  Si dispone de 2 servidores DNS que es de 6 segundos.  Use nameresolver para ver si funciona DNS.  Recuerde que no puede utilizar nslookup como que no utilice el DNS de su VNET está configurado con.
- **Intervalo de IP P2S no válida** El punto de intervalo IP de sitio debe estar en los intervalos de IP privados RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255) si el rango usa direcciones IP fuera de que, a continuación, cosas no funcionarán.  

Si esos elementos no responden a su problema, busque en primer lugar las cosas sencillas como:  

- ¿Mostrar la puerta de enlace como hacia arriba en el Portal?
- ¿Certificados que se muestran como sincronizada?
- ¿Cualquier persona que cambió la configuración de red sin hacer una red de sincronización de"" en las ASP afectadas? 

Si la puerta de enlace está presionada ponerlo copia de seguridad.  Si los certificados están sincronizados ir a la vista ASP de la integración de VNET y posicionamiento "Red de sincronización".  Si sospecha que ha habido un cambio realizado en la configuración de VNET y no tenían sincronización haría con su ASP, a continuación, vaya a la vista ASP de la integración de VNET y posicionamiento Just "Red de sincronización" como un aviso, provocará una breve interrupción con su conexión de VNET y las aplicaciones.  

Si todo es correcto debe profundizar un poco más:

- ¿Hay otras aplicaciones de mediante la integración de VNET para llegar a los recursos en el mismo VNET? 
- ¿Puede ir a la consola de aplicación y usar tcpping para llegar a los otros recursos de su VNET?  

Si se cumple alguna de las afirmaciones anteriores, la integración de VNET es correcta y el problema está en otro lugar.  Esto es donde obtiene sean más de un reto porque no hay ninguna forma sencilla de ver por qué no se puede llegar a un host: puerto.  Algunas de las causas se incluyen:

- tiene un firewall en su host de evitar el acceso al puerto de aplicación desde el punto de intervalo IP del sitio.  Paso subredes a menudo requiere acceso público.
- el host de destino es hacia abajo
- la aplicación está hacia abajo
- tiene la dirección IP o el nombre de host incorrecto
- la aplicación está escuchando en un puerto diferente lo esperado.  Puede comprobar vaya en ese host y use "netstat - aon" desde el símbolo del sistema.  Esto le mostrará qué proceso ID está escuchando en qué puerto.  
- los grupos de seguridad de la red están configurados de forma que impide acceso a su aplicación host y puerto, seleccione el intervalo IP de sitio

Recuerde que no sabe qué IP en su punto al rango de IP de sitio que usará la aplicación de manera que sea necesario permitir el acceso de todo el rango.  

Pasos de depuración adicionales incluyen:

- Inicie sesión en otra VM en su VNET e intente llegar a su host: puerto recursos desde allí.  Existen algunas utilidades de ping TCP que puede utilizar para ello o incluso puede utilizar telnet si es necesario.  El propósito aquí es únicamente determinar si está disponible conectividad desde esta otra máquina virtual. 
- abrir una aplicación de access VM y pruebe otra para ese host y puerto desde la consola de la aplicación  
####<a name="on-premise-resources"></a>Recursos de instalación local####
Si el no puede llegar a recursos local, a continuación, lo primero que debe comprobar es si puede llegar a un recurso en su VNET.  Si está trabajando que los pasos siguientes son bastante fáciles.  Desde una máquina virtual en su VNET debe intentar conectarse a la aplicación local activado.  Puede usar telnet o un programa de ping TCP.  Si su máquina virtual no alcanzar el recurso local activado, primero asegúrese de que funciona la conexión VPN de sitio a otro.  Si está trabajando, compruebe lo mismo que se indicó anteriormente, así como la configuración de puerta de enlace local y el estado de activado.  

Ahora si hospeda su VNET VM puede llegar a su sistema local en pero la aplicación no se puede, a continuación, el motivo probablemente es uno de estos procedimientos:
- las rutas no están configuradas con su punto de intervalos IP de sitio en la puerta de enlace local activado
- los grupos de seguridad de red están bloqueando el acceso de su punto de intervalo de IP de sitio
- los servidores de seguridad local en están bloqueando el tráfico desde el punto en el rango de IP de sitio
- tiene una Route(UDR) de definidas por el usuario en su VNET que evita su punto de tráfico del sitio según se comunique con su red local en

## <a name="hybrid-connections-and-app-service-environments"></a>Conexiones de híbrido y entornos de servicio de aplicaciones##
Hay 3 características que permiten el acceso a los recursos VNET hospedado.  Son:

- Integración de VNET
- Conexiones de híbrido
- Entornos de servicio de aplicaciones

Conexiones de híbrido requiere que instale un agente de retransmisión denominado la Manager(HCM) de conexión híbrido en su red.  La gestión de RR. debe poder conectarse a Azure y también a la aplicación.  Esta solución es especialmente excelente desde una red remota como su red local en o incluso otro nube hospedado red ya no requiere un extremo de accesibilidad de internet.  La gestión de RR. solo se ejecuta en Windows y puede tener un máximo de 5 instancias que se ejecutan para proporcionar alta disponibilidad.  Híbrido conexiones solo admite TCP aunque y cada extremo HC debe coincidir con una combinación de host: puerto específico.  

La característica entorno de servicio de la aplicación le permite ejecutar una instancia del servicio de aplicación de Azure en su VNET.  Esto le permite los acceso a los recursos aplicaciones en su VNET sin ningún paso adicional.  Algunas de las ventajas de un entorno de servicio de la aplicación es que puede usar los trabajadores de core dedica 8 con 14 GB de RAM.  Otra ventaja es que puede escalar el sistema para satisfacer sus necesidades.  A diferencia de los entornos de múltiples arrendatarios donde ASP tiene un tamaño limitada, en un ASE puede controlar la cantidad de recursos que desea dar al sistema.  Con respecto a la atención de red de este documento, una de las ventajas que obtiene con una ASE que no con integración de VNET es que puede trabajar con una VPN ExpressRoute.  

Aunque hay que algunas usa una superposición de mayúsculas y minúsculas, ninguna de estas características puede reemplazar cualquiera de los demás.  Saber qué característica usar está ligada a sus necesidades y cómo desea usarlo.  Por ejemplo:

- Si es programador y simplemente desea ejecutar un sitio en Azure y tiene acceso a la base de datos en la estación de trabajo en su escritorio, lo más sencillo usar es híbrido conexiones.  
- Si es una gran organización que desea colocar una gran cantidad de propiedades de web en público en la nube y administran en la red, a continuación, que quiere ir con el entorno de servicios de aplicación.  
- Si tiene un número de aplicación de servicio hospedado de aplicaciones y simplemente desea tener acceso a los recursos en su VNET VNET integración es la mejor opción.  

Más allá de los casos de uso que existen algunos simplificar relacionadas con aspectos.  Si su VNET ya está conectado a su red local activado, a continuación, mediante la integración de VNET o un entorno de servicio de la aplicación es una forma sencilla para consumir recursos local.  Por otro lado, si no está conectado a su red local en su VNET es mucho más sobrecarga para configurar una VPN de sitio a sitio con su VNET en comparación con la instalación de la gestión de RR..  

Más allá de las diferencias funcionales hay también diferencias de precios.  La característica entorno de servicio de la aplicación es una prima oferta de servicio pero ofrece red más posibilidades de configuración además de otras características.  Integración de VNET pueden utilizarse con estándar o Premium ASP y es perfecta para consume recursos en su VNET de la aplicación de servicio del inquilino múltiples de forma segura.  Híbrido conexiones actualmente depende de un cuenta que tiene precios niveles que comience gratuita y, a continuación, especifique cada vez más costosas en función de la cantidad que necesita de BizTalk.  Cuando se trata de trabajar en muchas redes no obstante, no hay ninguna otra función como conexiones híbrido que podrá tener acceso a recursos de más de 100 redes independientes.    


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[IntPowershell]: http://azure.microsoft.com/documentation/articles/app-service-vnet-integration-powershell/
[ASEintro]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
