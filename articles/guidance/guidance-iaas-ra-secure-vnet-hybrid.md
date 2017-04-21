<properties
   pageTitle="Implementar una arquitectura de red seguro híbrido en Azure | Microsoft Azure"
   description="Cómo implementar una arquitectura de red seguro híbrido en Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-dmz-between-azure-and-your-on-premises-datacenter"></a>Implementación de una DMZ entre Azure y su centro de datos local

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artículo describe los procedimientos recomendados para implementar una red híbrida seguro que amplía una red local a Azure. Esta arquitectura de referencia implementa una DMZ entre una red local y una red virtual Azure con rutas definidas por el usuario (UDRs). La DMZ incluye accesorios virtuales de red altamente disponible (NVAs) que implementan la funcionalidad de seguridad, como firewalls y la inspección de paquetes. Todo el tráfico saliente de la VNet es túnel forzar a Internet a través de la red local para que se pueden realizar auditorías. 

Esta arquitectura requiere una conexión a su centro de datos local implementado mediante una [puerta de enlace VPN][ra-vpn], o una [ExpressRoute] [ ra-expressroute] conexión.

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación: [El Administrador de recursos] [ resource-manager-overview] y clásica. Esta arquitectura de referencia utiliza el Administrador de recursos, que Microsoft recomienda para implementaciones nuevas.

Casos de uso común para esta arquitectura incluyen:

- Aplicaciones híbridas donde ejecutar las cargas de trabajo en parte local y en parte de Azure.

- Infraestructura de Azure que enruta el tráfico de internet y local.

- Aplicaciones necesarios para el tráfico saliente de auditoría. Esto suele ser un requisito normativo de muchos sistemas comerciales y puede ayudar a evitar la divulgación pública de información privada.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

El siguiente diagrama resalta los componentes importantes en esta arquitectura:

> Un documento de Visio que incluye este diagrama de arquitectura está disponible para su descarga en el [Centro de descarga de Microsoft][visio-download]. Este diagrama muestra en la página "DMZ – privado".

[! [0]][0]

- **Red local.** Se trata de una red de equipos y dispositivos conectados a través de una privada implementado en una organización de red de área local.

- **Red virtual Azure (VNet).** La VNet hospeda la aplicación y otros recursos que se ejecutan en la nube.

- **Puerta de enlace.** La puerta de enlace proporciona conectividad entre los enrutadores en la red local y la VNet.

- **Dispositivo virtual de red (NVA).** NVA es un término genérico que describa una máquina virtual realizar tareas como permitir o denegar el acceso como un firewall, optimizadas operaciones WAN (incluida la compresión de red), la distribución personalizada u otras funciones de red. 

- **Nivel de Web, nivel empresarial y subredes de nivel de datos.** Estos son subredes aloja las máquinas virtuales y servicios que implementan una aplicación de nivel 3 de ejemplo en la nube. Consulte [Ejecutar máquinas virtuales de Windows para una arquitectura de N niveles en Azure] [ ra-n-tier] para obtener más información.

- **Rutas definidas por el usuario (UDR).** [Rutas definidas por el usuario] [ udr-overview] definir el flujo del tráfico IP dentro de VNets de Azure. 

> [AZURE.NOTE] Dependiendo de los requisitos de la conexión VPN, puede configurar las rutas de protocolo de puerta de enlace de borde (BGP) como una alternativa a volver a utilizar UDRs para implementar las reglas de reenvío que dirigir el tráfico a través de la red local.

- **Subred de administración.** Esta subred contiene máquinas virtuales que implementan capacidades de administración y supervisión de los componentes se ejecutan en el VNet. 

## <a name="recommendations"></a>Recomendaciones

Azure ofrece numerosos recursos distintos y tipos de recursos, por lo que puede ser esta arquitectura de referencia se aprovisione varias formas diferentes. Proporcionamos una plantilla de administrador de recursos de Azure para instalar la arquitectura de referencia que sigue estas recomendaciones. Si elige crear su propia arquitectura de referencia debería seguir estas recomendaciones a menos que tenga un requisito específico que no se ajusta una recomendación.

### <a name="rbac-recommendations"></a>Recomendaciones RBAC

Crear varias funciones RBAC para administrar los recursos en la aplicación. Considere la posibilidad de crear un [rol personalizado] de DevOps[ rbac-custom-roles] con permisos para administrar la infraestructura de la aplicación. Considere la posibilidad de crear un administrador de TI centralizada [función personalizada] [ rbac-custom-roles] para administrar recursos de red y seguridad independiente del Administrador de TI [funciones personalizado] [ rbac-custom-roles] para administrar los recursos de red segura, como la NVAs. 

La función DevOps debe incluir permisos para implementar los componentes de la aplicación, así como supervisar y reiniciar máquinas virtuales. El rol de administrador de TI centralizado debe incluir permisos para supervisar los recursos de red. Ninguna de estas funciones deberían tener acceso a los recursos NVA como debe ser restringida al rol de administrador de TI de seguridad.

### <a name="resource-group-recommendations"></a>Recomendaciones de grupo de recursos

Azure recursos como máquinas virtuales, VNets y equilibradores de carga se pueden administrar fácilmente agrupándolas en grupos de recursos. A continuación, puede asignar las funciones anteriormente para cada grupo de recursos para restringir el acceso.

Se recomienda la creación de los procedimientos siguientes:

- Un grupo de recursos que contiene las subredes (excepto las VM), NSGs y los recursos de la puerta de enlace para conectarse a la red local. Asignar el rol de administrador de TI centralizado para este grupo de recursos.

- Un grupo de recursos que contiene las máquinas virtuales de la NVAs (incluido el equilibrador de carga), el cuadro saltar y otras máquinas virtuales de administración y la UDR para la subred de puerta de enlace que fuerza todo el tráfico a través de la NVAs. Asignar el rol de administrador de TI de seguridad a este grupo de recursos.

- Grupos de recursos independientes para cada nivel de aplicación que contienen el equilibrador de carga y máquinas virtuales. Tenga en cuenta que este grupo de recursos no debe incluir las subredes para cada nivel. Asignar el rol de DevOps a este grupo de recursos.

### <a name="virtual-network-gateway-recommendations"></a>Recomendaciones de puerta de enlace de red virtual

Tráfico local pasa a la VNet a través de una puerta de enlace de red virtual. Se recomienda una [puerta de enlace VPN de Azure] [ guidance-vpn-gateway] o una [puerta de enlace de Azure ExpressRoute][guidance-expressroute]. 

### <a name="nva-recommendations"></a>Recomendaciones de NVA

NVAs proporciona servicios diferentes para administrar y supervisar el tráfico de red. Azure Marketplace ofrece diversas NVAs proveedor de terceros, incluidos:

- [Servidor de aplicaciones Web de Barracuda] [ barracuda-waf] y [Barracuda NextGen Firewall][barracuda-nf]

- [Redes coherente VNS3 enrutador/Firewall/VPN][vns3]

- [Fortinet FortiGate-VM][fortinet]

- [Servidor de aplicaciones Web SecureSphere][securesphere]

- [Servidor de aplicaciones Web DenyAll][denyall]

- [Punto de verificación vSEC][checkpoint]

Si ninguno de estos NVAs de terceros que satisfaga sus necesidades, puede crear un NVA personalizado con máquinas virtuales. Para obtener un ejemplo de creación de NVAs personalizados, vea la DMZ en esta arquitectura de referencia que implementa las siguientes funciones:

- Se enruta el tráfico con [reenvío IP] [ ip-forwarding] en NIC de NVA.

- Se permite el tráfico pase a través de la NVA solo si es necesario. Cada VM NVA en la arquitectura de referencia es un simple enrutador de Linux con el tráfico entrante que llegan a la red interfaz *eth0*y reglas de coincidencia de tráfico saliente definidas por scripts personalizados que se enviará a través de la interfaz de red *eth1*. 

- Enrutadas a la subred de administración de tráfico no atraviesa la NVAs y la NVAs sólo pueden configurarse desde la subred de administración. Si el tráfico a la subred de administración es necesario para enrutar a través de la NVAs, no hay ninguna ruta a la subred de administración para corregir el NVAs si debe producirá un error.  

- Las máquinas virtuales de la NVA se incluyen en una disponibilidad establecer detrás de un equilibrador de carga. El UDR en la subred de puerta de enlace dirige las solicitudes NVA al equilibrador de carga.

Otra recomendación para tener en cuenta conecta varios NVAs en serie con cada NVA llevar a cabo una tarea de seguridad especializado. Esto permite cada función de seguridad que se administra en una base por NVA. Por ejemplo, una NVA implementar un firewall podría colocarse en serie con un NVA ejecutando los servicios de identidad. El compromiso para facilitar la administración es la adición de saltos de red adicionales que puede aumentar la latencia, así que asegúrese de que no se ve afectado el rendimiento de la aplicación.

### <a name="nsg-recommendations"></a>Recomendaciones GSN

La puerta de enlace VPN expone una dirección IP pública para la conexión a la red local. Recomendamos crear un grupo de seguridad de la red (GSN) la NVA subred implementar las reglas de entrada bloquear todo el tráfico no que provienen de la red local.

También se recomienda que implemente NSGs para cada subred para proporcionar un segundo nivel de protección contra el tráfico omita un NVA configurado incorrectamente o deshabilitado. Por ejemplo, la subred del nivel de web en la arquitectura de referencia implementa una GSN con una regla para omitir todas las solicitudes de distintos de los recibidos de la red local (192.168.0.0/16) o la VNet y otra regla que pasa por alto todas las solicitudes no realizadas en el puerto 80. 

### <a name="internet-access-recommendations"></a>Recomendaciones de acceso a Internet

[Túnel de fuerza] [ azure-forced-tunneling] todo el tráfico saliente de internet a través de su red local mediante el túnel VPN de sitio a sitio y la ruta a internet utilizando la red dirección tranlation (NAT). Esto tanto evitará filtrado accidental de información confidencial almacenada en el nivel de datos y también permiten inspección y auditoría de todo el tráfico saliente.

> [AZURE.NOTE] No bloquear el tráfico de Internet de los niveles de web, business y aplicación completa. Si estos niveles usa servicios de Azure PaaS que se basan en las direcciones IP públicas para el registro de diagnóstico de máquina virtual, descargue de extensiones VM y otras funciones. Diagnóstico de Azure también requiere que los componentes pueden leer y escribir a una cuenta de almacenamiento de Azure dependen de internet.

Aún más, le recomendamos que compruebe el tráfico saliente de internet está túnel forzar correctamente. Si está usando una conexión VPN con el [servicio de acceso remoto y enrutamiento] [ routing-and-remote-access-service] en un servidor local, utilice una herramienta como [WireShark] [ wireshark] o el [Analizador de mensaje de Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=44226).

### <a name="management-subnet-recommendations"></a>Recomendaciones de administración de subred

La subred administración contiene un cuadro de salto que se ejecuta la administración y supervisión de funcionalidad. Implementar las siguientes recomendaciones para el cuadro saltar:
- No crear una dirección IP pública del cuadro de salto. 
- Crear una ruta para tener acceso al cuadro de salto a través de la puerta de enlace entrante e implementar un GSN en la subred administración sólo responder a solicitudes de la ruta permitida.
- Restringir la ejecución de todas las tareas de administración segura al cuadro salto.

### <a name="nva-recommendations"></a>Recomendaciones de NVA

Incluir una capa 7 NVA para terminar de conexiones de la aplicación en el nivel NVA y mantener la afinidad con los niveles de back-end. Así garantiza simétrica conectividad en el que se devuelve el tráfico de respuesta de los niveles de back-end a través de la NVA.

## <a name="scalability-considerations"></a>Consideraciones de escalabilidad

La arquitectura de referencia implementa un equilibrador de carga dirigir el tráfico de red local a un grupo de dispositivos NVA. Como se indicó anteriormente, los dispositivos NVA son máquinas virtuales ejecutar reglas de enrutamiento de tráfico de red y se implementan en un [conjunto de disponibilidad][availability-set]. Este diseño permite supervisar el rendimiento de la NVAs a lo largo del tiempo y agregar dispositivos NVA en respuesta a aumentos de carga.

La puerta de enlace VPN SKU estándar es compatible con el rendimiento continuado de hasta 100 Mbps. El alto SKU de rendimiento proporciona un máximo de 200 Mbps. Anchos de banda mayores, considere la posibilidad de actualizar a una puerta de enlace ExpressRoute. ExpressRoute proporciona el ancho de banda de hasta 10 GB/s menor latencia que una conexión VPN.

> [AZURE.NOTE] Los artículos de la [implementación de una arquitectura de red híbrido con Azure y VPN local] [ guidance-vpn-gateway] e [implementar una arquitectura de red híbrida con Azure ExpressRoute] [ guidance-expressroute] describen problemas relacionados con la escalabilidad de Azure puertas de enlace.

## <a name="availability-considerations"></a>Consideraciones de disponibilidad

La arquitectura de referencia implementa un equilibrador de carga distribuir solicitudes locales a un grupo de dispositivos NVA en Azure. Los dispositivos NVA son máquinas virtuales ejecutar reglas de enrutamiento de tráfico de red y se implementan en un [conjunto de disponibilidad][availability-set]. El equilibrador de carga con regularidad consulta un sondeo de estado implementado en cada NVA y quitará cualquier NVAs no responde del grupo. 

Si está usando Azure ExpressRoute para proporcionar conectividad entre la red VNet y local, [configure una puerta de enlace VPN para proporcionar la migración tras error] [ guidance-vpn-failover] si la conexión de ExpressRoute no está disponible.

Para obtener información específica sobre el mantenimiento de disponibilidad para conexiones VPN y ExpressRoute, vea los artículos de la [implementación de una arquitectura de red híbrido con Azure y VPN local] [ guidance-vpn-gateway] e [implementar una arquitectura de red híbrida con Azure ExpressRoute][guidance-expressroute].

## <a name="manageability-considerations"></a>Consideraciones de capacidad de administración

El cuadro saltar en la subred administración deben realizar todas las aplicaciones y la supervisión de recursos. Según las necesidades de la aplicación, debe agregar recursos de supervisión adicionales en la subred de administración, pero vuelva a cualquiera de estos recursos adicionales debe tener acceso a través del cuadro de salto.

Si la conectividad de puerta de enlace de su red local a Azure es hacia abajo, aún puede ponerse en el cuadro saltar al implementar una puntos, agregar al cuadro saltar y acceso remoto en desde internet.

Subred de cada nivel de la arquitectura de referencia está protegido por reglas GSN y puede ser necesario crear una regla para abrir el puerto 3389 para el acceso RDP en máquinas virtuales de Windows o 22 para el acceso SSH en máquinas virtuales de Linux. Reglas para abrir puertos adicionales pueden obligarle a otras herramientas de supervisión y administración.

Si usa ExpressRoute para proporcionar la conectividad entre el centro de datos local y Azure, utilice el [Kit de herramientas de conectividad de Azure (AzureCT)] [ azurect] para supervisar y solucionar problemas de conexión.

> [AZURE.NOTE] Puede encontrar información adicional específicamente destinada a supervisar y administrar conexiones VPN y ExpressRoute en los artículos de [implementación de una arquitectura de red híbrido con Azure y VPN local] [ guidance-vpn-gateway] e [implementar una arquitectura de red híbrida con Azure ExpressRoute][guidance-expressroute].

## <a name="security-considerations"></a>Consideraciones de seguridad

Esta arquitectura de referencia implementa varios niveles de seguridad: 

### <a name="routing-all-on-premises-user-requests-through-the-nva"></a>Enrutamiento de todas las solicitudes de usuario local a través de la NVA

El UDR en la subred de puerta de enlace bloquea todas las solicitudes de usuario que no sean los recibidos de local. Las pasadas UDR permiten solicitudes a la NVAs en la subred DMZ privada y, a continuación, estas solicitudes se pasan a la aplicación si permitidos de las reglas NVA. Otras rutas pueden agregarse a la UDR, pero asegúrese de que no omitir accidentalmente el NVAs o bloquear el tráfico administrativo destinados a la subred de administración.

El equilibrador de carga delante de la NVAs también actúa como un dispositivo de seguridad omitiendo el tráfico en puertos que no están abiertos en las reglas de equilibrio de carga. Solo escuchan los equilibradores de carga en la arquitectura de referencia para las solicitudes HTTP en el puerto 80 y HTTPS en el puerto 443. Deben documentar las reglas adicionales a los equilibradores de carga y el tráfico se debe supervisar para asegurarse de que no hay ningún problema de seguridad.

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>Usar NSGs para bloquear/pass tráfico entre niveles de la aplicación

Cada uno de los niveles de web, business y datos restringen el tráfico entre ellos mediante NSGs. Es decir, el nivel de empresa utiliza un GSN para bloquear todo el tráfico que no se ha originado en el nivel de web y el nivel de datos utiliza un GSN para bloquear todo el tráfico que no se ha originado en el nivel empresarial. Si tiene un requisito para expandir las reglas de GSN para permitir el acceso más amplio a estos niveles, sopese estos requisitos frente a los riesgos de seguridad. Cada nuevo camino entrante representa una oportunidad accidentales o intencionados daños de datos fuga o aplicación.

### <a name="devops-access"></a>Acceso DevOps

Restringir las operaciones que puede llevar a cabo DevOps cada nivel utilizando [RBAC] [ rbac] para administrar los permisos. Al conceder permisos, utilice el [principio de menor privilegio][security-principle-of-least-privilege]. Iniciar todas las operaciones administrativas y realice auditorías periódicas para garantizar que los cambios de configuración planeados.

> [AZURE.NOTE] Para información más amplia, ejemplos y escenarios sobre la administración de seguridad de la red con Azure, vea [Servicios de nube de Microsoft y seguridad de la red][cloud-services-network-security]. Para obtener información detallada sobre cómo proteger los recursos en la nube, consulte [Introducción a la seguridad de Microsoft Azure][getting-started-with-azure-security]. Para obtener más información en resolver los problemas de seguridad a través de una conexión de puerta de enlace de Azure, vea [implementar una arquitectura de red híbrido con Azure y VPN local] [ guidance-vpn-gateway] e [implementar una arquitectura de red híbrida con Azure ExpressRoute][guidance-expressroute].

## <a name="solution-deployment"></a>Implementación de soluciones

Una implementación de una arquitectura de referencia que implementa estas recomendaciones está disponible en Github. Esta arquitectura de referencia incluye una red virtual (VNet), grupo de seguridad de la red (GSN), equilibrador de carga y dos máquinas virtuales (VM).

La arquitectura de referencia se puede implementar con Windows o Linux VM siguiendo las instrucciones siguientes: 

1. Haga clic con el botón secundario del mouse en el botón de abajo y seleccione uno "Abrir vínculo en una nueva pestaña" o "Abrir vínculo en ventana nueva":  
[![Implementar en Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet%2Fazuredeploy.json)

2. Una vez que ha abierto el vínculo en el portal de Azure, debe especificar valores para algunas de las opciones: 
    - El nombre del **grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Crear nuevo** y escriba `ra-private-dmz-rg` en el cuadro de texto.
    - Seleccione la región en el cuadro desplegable de **ubicación** .
    - No modifique la **Plantilla raíz Uri** o los cuadros de texto de **Uri raíz del parámetro** .
    - Revise los términos y condiciones y luego haga clic en la casilla de verificación **que acepto los términos y condiciones indicadas arriba** .
    - Haga clic en el botón de **compra** .

3. Espere a que la implementación completar.

4. Los archivos de parámetro incluyen nombre de usuario administrador modificable y la contraseña para todas las máquinas virtuales y se recomienda cambiar inmediatamente ambas. Para cada máquina virtual de la implementación, selecciónelo en el portal de Azure y, a continuación, haga clic en **Restablecer la contraseña** en el módulo de **compatibilidad + solución de problemas** . Seleccione **Restablecer la contraseña** en el cuadro de lista desplegable de **modo** , seleccione un nuevo **nombre de usuario** y **contraseña**. Haga clic en el botón **Actualizar** para conservar.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo implementar una [DMZ entre Azure e Internet](./guidance-iaas-ra-secure-vnet-dmz.md).
- Aprenda a implementar una [arquitectura de red híbrida altamente disponibles](./guidance-hybrid-network-expressroute-vpn-failover.md).

<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-create-availability-set.md
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[azure-forced-tunneling]: https://azure.microsoft.com/en-gb/documentation/articles/vpn-gateway-forced-tunneling-rm/
[barracuda-nf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/barracuda-ng-firewall/
[barracuda-waf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/
[checkpoint]: https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/
[cloud-services-network-security]: https://azure.microsoft.com/documentation/articles/best-practices-network-security/
[denyall]: https://azure.microsoft.com/marketplace/partners/denyall/denyall-web-application-firewall/
[fortinet]: https://azure.microsoft.com/marketplace/partners/fortinet/fortinet-fortigate-singlevmfortigate-singlevm/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[ip-forwarding]: ../virtual-network/virtual-networks-udr-overview.md#IP-forwarding
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[ra-n-tier]: ./guidance-compute-n-tier-vm.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[rbac-custom-roles]: ../active-directory/role-based-access-control-custom-roles.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[routing-and-remote-access-service]: https://technet.microsoft.com/library/dd469790(v=ws.11).aspx
[securesphere]: https://azure.microsoft.com/marketplace/partners/imperva/securesphere-waf-for-azr/
[security-principle-of-least-privilege]: https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vns3]: https://azure.microsoft.com/marketplace/partners/cohesive/cohesiveft-vns3-for-azure/
[wireshark]: https://www.wireshark.org/
[0]: ./media/blueprints/hybrid-network-secure-vnet.png "Arquitectura de red híbrida segura"
