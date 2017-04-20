<properties
   pageTitle="Directrices para la implementación de Active Directory Windows Server en máquinas virtuales de Windows Azure | Microsoft Azure"
   description="Si sabe cómo implementar los servicios de dominio de Active Directory y servicios de federación de AD local, obtenga información sobre cómo funcionan en máquinas virtuales de Windows Azure."
   services="active-directory"
   documentationCenter=""
   authors="femila"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/27/2016"
   ms.author="femila"/>

# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Directrices para la implementación de Active Directory de Windows Server en máquinas virtuales de Windows Azure

Este artículo explica las diferencias importantes entre implementar Windows Server Active Directory Domain Services (AD DS) y los servicios de federación de Active Directory (AD FS) local en lugar de implementación de en máquinas virtuales de Microsoft Azure.

## <a name="scope-and-audience"></a>Ámbito y audiencia

El artículo está pensado para aquellos que ya tiene experiencia en la implementación de Active Directory local. Se tratan las diferencias entre la implementación de Active Directory en redes virtuales de Microsoft Azure máquinas virtuales/Azure y tradicional en Active Directory las implementaciones locales. Máquinas virtuales de Windows Azure y Azure redes virtuales forman parte de un infraestructura como-servicio (IaaS) que se ofrece para las organizaciones a aprovechar recursos informáticos en la nube.

Para aquellos que no está familiarizado con la implementación de AD, consulte la [Guía de implementación de AD DS](https://technet.microsoft.com/library/cc753963) o [Planear la implementación de AD FS](https://technet.microsoft.com/library/dn151324.aspx) según corresponda.

Este artículo se presupone que el lector está familiarizado con los conceptos siguientes:

- Administración e implementación de Windows Server AD DS
- Implementación y configuración de DNS para admitir una infraestructura de Windows Server AD DS
- Administración e implementación de Windows Server AD FS
- Implementar, configurar y administrar aplicaciones de terceros confianza (servicios web y sitios Web) que pueden consumir tokens Windows Server AD FS
- Conceptos generales máquina virtual, por ejemplo, cómo configurar una máquina virtual, discos virtuales y redes virtuales

En este artículo, se resalta los requisitos para un escenario de implementación híbrida Windows Server AD DS o AD FS son parcialmente implementado en local y parcialmente implementado en máquinas virtuales de Windows Azure. El documento cubre primero las diferencias importantes entre ejecutan Windows Server AD DS y AD FS en Azure máquinas virtuales frente a local y decisiones importantes que afectan a la implementación y el diseño. Directrices para cada uno de los puntos de decisión con más detalle y cómo aplicar las directrices para distintos escenarios de implementación, explica el resto del papel.

En este artículo no trata la configuración de [Azure Active Directory](http://azure.microsoft.com/services/active-directory/), que es un servicio basado en REST que proporciona la administración de identidades y las capacidades de control de acceso para las aplicaciones de la nube. Azure Active Directory (AD Azure) y Windows Server AD DS sin embargo, se, diseñados para trabajar conjuntamente para proporcionar una solución de administración de identidades y acceso híbrido de hoy TI entornos y aplicaciones modernas. Para ayudar a comprender las diferencias y las relaciones entre Windows Server AD DS y Azure AD, considere lo siguiente:

1. Puede ejecutar Windows Server AD DS en la nube en máquinas virtuales de Windows Azure cuando se usa Azure ampliar su centro de datos local en la nube.
2. Puede usar Azure AD para dar a los usuarios de inicio de sesión único a aplicaciones de Software como-servicio (SaaS). Por ejemplo, Microsoft Office 365 usa esta tecnología y aplicaciones que se ejecutan en otras plataformas de nube o Azure también pueden usar.
3. Puede usar Azure AD (su servicio de Control de acceso) para que los usuarios puedan iniciar sesión en el uso de identidades de Facebook, Google, Microsoft y otros proveedores de identidades para las aplicaciones que se hospedan en la nube o local.

Para obtener más información sobre estas diferencias, vea [Identidad de Azure](fundamentals-identity.md).

## <a name="related-resources"></a>Recursos relacionados

Puede descargar y ejecutar la [Evaluación de disponibilidad de la máquina Virtual de Azure](https://www.microsoft.com/download/details.aspx?id=40898). La evaluación automáticamente inspeccionar su entorno local y generar un informe personalizado siguiendo las instrucciones que se encuentra en este tema para ayudarle a migrar el entorno a Azure.

Le recomendamos que primero también revise los tutoriales, guías y vídeos que abarcan los siguientes temas:

- [Configurar una red Virtual de nube solo en el Portal de Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
- [Configurar una VPN de sitio a sitio en el Portal de Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)
- [Instalar un nuevo bosque de Active Directory en una red virtual de Azure](active-directory-new-forest-virtual-machine.md)
- [Instalar un controlador de dominio de Active Directory réplica en Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
- [Microsoft Azure IT Pro IaaS: aspectos básicos de la máquina (01)](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
- [Microsoft Azure IT Pro IaaS: (05) crear redes virtuales y conectividad entre local](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Introducción

Los requisitos fundamentales para la implementación de Active Directory de Windows Server en máquinas virtuales de Windows Azure difieren muy poco de implementarlo en máquinas virtuales de local (y, hasta cierto punto, equipos físicos). Por ejemplo, en el caso de Windows Server AD DS, si los controladores de dominio (DC) que se implementación en máquinas virtuales de Windows Azure son réplicas en una existente local bosque o dominio corporativo, a continuación, la implementación de Azure en gran medida puede tratar de la misma manera que podría considerar cualquier otro sitio adicional de Active Directory de Windows Server. Es decir, subredes deben definirse en Windows Server AD DS, un sitio creado, las subredes vinculadas a ese sitio y conectado a otros sitios con vínculos de sitio adecuados. Sin embargo, existen algunas diferencias que sean comunes a todas las implementaciones de Azure y algunos que varían según el escenario de implementación. A continuación se presentan dos diferencias fundamentales:

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Máquinas virtuales de Windows Azure que tenga conectividad a la red corporativa local.

Máquinas virtuales de Windows Azure a conectarse a una red corporativa de local requiere una red virtual Azure, que incluye un sitio a otro o punto de sitio virtual privada (VPN) componente de red puede conectarse sin problemas máquinas virtuales de Windows Azure y equipos locales. Este componente VPN también podría permitir a los equipos de miembros de dominio local tener acceso a un dominio de Active Directory de Windows Server cuyos controladores de dominio están hospedados exclusivamente en máquinas virtuales de Windows Azure. Es importante no obstante, tenga en cuenta que si se produce un error en la conexión VPN, autenticación y otras operaciones que dependen de Active Directory de Windows Server se también producirá un error. Mientras que los usuarios podrán iniciar sesión usando existentes en caché credenciales, todo de punto a punto o fallarán los intentos de autenticación de servidor de cliente para el que tienen vales todavía emitida o han quedado obsoletos.

Vea [Una red Virtual](http://azure.microsoft.com/documentation/services/virtual-network/) para una demostración en vídeo y una lista de tutoriales paso a paso, incluidos [Configurar una VPN de sitio a sitio en el portal de Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE] También puede implementar Active Directory de Windows Server en una red virtual Azure que no tenga conectividad con una red local. Las instrucciones de este tema, sin embargo, se suponen que se utiliza una red virtual Azure porque ofrece capacidades esenciales de Windows Server de direcciones IP.

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>Direcciones IP deben estar configuradas con PowerShell de Azure.

Direcciones dinámicas se asignan de forma predeterminada, pero use el cmdlet Set-AzureStaticVNetIP para asignar una dirección IP estática en su lugar. Se establece una dirección IP estática que se mantendrá a través de resolución de problemas de servicio y cierre o reinicio de la máquina virtual. Para obtener más información, vea [dirección IP estática interna para máquinas virtuales](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/).

## <a name="BKMK_Glossary"></a>Términos y definiciones

La siguiente es una lista no exhaustiva de términos para diversas tecnologías de Azure que se hará referencia en este artículo.

- **Máquinas virtuales de Windows Azure**: IaaS la oferta en Azure que permite a los clientes a implementar máquinas virtuales con casi cualquier tradicionalmente carga de trabajo de servidor local.

- **Una red virtual Azure**: el servicio de redes en Azure que permite a los clientes crear y administrar redes virtuales en Azure y vincularlos segura a sus propios local infraestructura de red mediante el uso de una red privada virtual (VPN).

- **Dirección IP virtual**: una dirección IP a través de internet que no está enlazada a una tarjeta de interfaz de red o equipo específica. Servicios de nube se asignan una dirección IP virtual para recibir el tráfico de red que se redirige a una máquina virtual de Azure. Una dirección IP virtual es una propiedad de un servicio de nube que puede contener una o más máquinas virtuales Azure. Tenga en cuenta que una red virtual Azure puede contener uno o más servicios de nube. Direcciones IP virtuales proporcionan las capacidades nativas de equilibrio de carga.

- **Dirección IP dinámica**: esta es la dirección IP que solo es interna. Se debe configurarse como una dirección IP estática (mediante el cmdlet Set-AzureStaticVNetIP) para máquinas virtuales que alojan los roles del servidor DNS o controlador de dominio.

- **Resolución de problemas de servicio**: el proceso en el que Azure devuelve automáticamente un servicio a un estado de ejecución nuevo cuando detecta que el servicio ha fallado. Servicio de resolución de problemas es uno de los aspectos de Azure que admite la disponibilidad y la resistencia. Aunque es poco probable, el resultado siguiendo un servicio corrector incidente para un controlador de dominio que se ejecuta en una máquina virtual es similar a un reinicio no planeado, pero tiene algunos efectos secundarios:

 - Cambia el adaptador de red virtual en la máquina virtual
 - Cambia la dirección MAC del adaptador de red virtual
 - Se cambiará el ID de procesador/CPU de la máquina virtual
 - La configuración de IP de adaptador de red virtual no cambiará mientras la máquina virtual está conectada a una red virtual y la dirección IP de la máquina virtual es estática.

 Ninguno de estos comportamientos afectará a Active Directory de Windows Server porque no tiene ninguna dependencia en la dirección MAC o el identificador del procesador/CPU y, a continuación, se recomiendan que todas las implementaciones de Active Directory de Windows Server en Azure se pueden ejecutar en una red virtual Azure descritas anteriormente.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>¿Es seguro virtualización de controladores de dominio de Active Directory de Windows Server?

Implementación de Active Directory DC de Windows Server en máquinas virtuales de Windows Azure está sujeto a las mismas directrices que ejecuta DC local en una máquina virtual. Ejecuta DC virtualizado es una práctica segura mientras se siguen directrices para realizar copias de seguridad y restauración de controladores de dominio. Para obtener más información sobre las restricciones e instrucciones para ejecutar DC virtualizado, vea [Ejecutando los controladores de dominio en Hyper-V](https://technet.microsoft.com/library/dd363553).

Hipervisores proporcionan o trivialize tecnologías que pueden causar problemas para muchos sistemas distribuidos, incluido Active Directory de Windows Server. Por ejemplo, en un servidor físico, puede clonar un disco o usar los métodos no admitidos para revertir el estado de un servidor, incluido el uso de SANs y así sucesivamente, pero esto en un servidor físico es mucho más difícil que la restauración de una instantánea de máquina virtual en un hipervisor. Azure ofrece funcionalidad que puede dar lugar a la misma condición no deseada. Por ejemplo, no debe copiar archivos de disco duro virtual de controladores de dominio en lugar de realizar copias de seguridad periódicas porque Restaurar puede dar lugar a una situación similar al uso de características de restauración instantánea.

Como deshacer presente burbujas USN que pueden llevar a Estados permanentemente divergentes entre controladores de dominio. Que pueden provocar problemas como:

- Objetos persistentes
- Contraseñas incoherentes
- Valores de atributo incoherente
- Diferencias de esquema si se deshace el maestro de esquema

Para obtener más información acerca de cómo se ven afectados los controladores de dominio, vea [USN y deshacer USN](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

Comenzando con Windows Server 2012, [medidas de seguridad adicionales están integrados en AD DS](https://technet.microsoft.com/library/hh831734.aspx). Estas medidas de seguridad ayudan a proteger los controladores de dominio virtualizado frente a los problemas mencionados anteriormente, como la plataforma de hipervisor subyacente admite VM GenerationID. Azure admite VM GenerationID, lo que significa que los controladores de dominio que ejecutan Windows Server 2012 o versiones posteriores en Azure máquinas virtuales tienen las medidas de seguridad adicionales.

> [AZURE.NOTE] Debe cerrar y reiniciar una máquina virtual que se ejecuta la función de controlador de dominio en Azure dentro del sistema operativo de invitado en lugar de usar la opción **Apagar** en el portal de clásico de Azure. En la actualidad, con el portal clásico para cerrar una máquina virtual hace que la máquina virtual de cancelar la asignación. Una manera VM tiene la ventaja de no provocar cargos, pero también restablece VM-GenerationID, que es conveniente para un controlador de dominio. Cuando se restablece el GenerationID VM, también se restablece el Id. de invocación de la base de datos de AD DS, se descarta el conjunto de RID y SYSVOL está marcado como no relevantes. Para obtener más información, vea [Introducción a la virtualización de los servicios de dominio de Active Directory (AD DS)](https://technet.microsoft.com/library/hh831734.aspx) y [DFSR de virtualización de forma segura](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx).

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>¿Por qué implementar Windows Server AD DS en Azure máquinas virtuales de Windows?

Muchos de los escenarios de implementación de Windows Server AD DS son adecuadas para su implementación como máquinas virtuales en Azure. Por ejemplo, suponga que tiene una empresa de Europa que necesita para autenticar usuarios en una ubicación remota en Asia. La empresa ha implementado anteriormente Windows Server Active Directory controladores de Asia debido al costo para implementar la experiencia de ellas y limitada para administrar la implementación posterior a la de los servidores. Como resultado, las solicitudes de autenticación de Asia son atendidas por controladores de Europa con resultados más apropiados. En este caso, puede implementar un controlador de dominio en una máquina virtual que ha especificado debe ejecutar en el centro de datos de Azure en Asia. Asociar ese DC a una red virtual Azure que está conectada directamente a la ubicación remota mejorará el rendimiento de la autenticación.

Azure también es adecuado como sustituto a sitios de recuperación (DR) desastres costosas en caso contrario. El bajo costo de hospedaje un pequeño número de controladores de dominio y una única red virtual en Azure representa una alternativa atractiva.

Por último, desea implementar una aplicación de red en Azure, como SharePoint, que requiere Windows Server Active Directory, pero no tiene ninguna dependencia en la red local o corporativo Active Directory de Windows Server. En este caso, implementar un bosque aislado en Azure para cumplir con SharePoint requisitos del servidor es óptimo. De nuevo, también se admite la implementación de aplicaciones de red que requieren conectividad con la red local y Active Directory corporativo.

> [AZURE.NOTE] Puesto que proporciona una conexión de nivel 3, el componente VPN que proporciona conectividad entre una red virtual Azure y una red local, también puede habilitar a servidores miembro que ejecuten local para aprovechar los controladores de dominio que se ejecutan como máquinas virtuales de Windows Azure en una red virtual Azure. Pero, si la conexión VPN no está disponible, la comunicación entre equipos locales y controladores de dominio basados en Azure no funciona, resultante de autenticación y varios otros errores.  

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Lo mismo entre la implementación de controladores de dominio de Active Directory de Windows Server en máquinas Azure frente a local

- Para cualquier escenario de implementación de Active Directory de Windows Server incluye más de una única VM, es necesario utilizar una red virtual Azure coherencia de dirección IP. Tenga en cuenta que esta guía se supone que se están ejecutando en una red virtual Azure DC.

- Al igual que con controladores de dominio local, se recomiendan direcciones IP. Una dirección IP estática sólo se puede configurar con PowerShell de Azure. Para obtener más detalles, consulte [dirección IP estática interna para máquinas virtuales](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) . Si tiene la supervisión de sistemas u otras soluciones de revisar la configuración de dirección IP estática en el sistema operativo invitado, puede asignar la misma dirección IP estática a las propiedades del adaptador de red de la máquina virtual. Pero tenga en cuenta que el adaptador de red se descartarán si la máquina virtual realiza la resolución de problemas de servicio o se cierra en el portal de clásico y se cancela la asignación de su dirección. En ese caso, la dirección IP estática dentro del invitado tendrán que se restablezcan.

- Implementar máquinas virtuales en una red virtual no implica (o requieren) conectividad a una red local. la red virtual permite simplemente esa posibilidad. Debe crear una red virtual privada comunicación entre Azure y su red local. Debe implementar un extremo de la VPN en la red local. La conexión VPN se abre desde Azure a la red local. Para obtener más información, vea [Introducción a la red Virtual](../virtual-network/virtual-networks-overview.md) y [Configurar una VPN de sitio a sitio en el Portal de Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE] Una opción para [crear una VPN de sitio a punto](../vpn-gateway/vpn-gateway-point-to-site-create.md) está disponible para conectar equipos basados en Windows individuales directamente a una red virtual Azure.

- Independientemente de si crea un virtual de red o no, cargos Azure para el tráfico de salida, pero no la entrada. Varias opciones de diseño de Active Directory de Windows Server pueden afectar a cuánto tráfico de salida generado por una implementación. Por ejemplo, implementar un controlador de dominio de solo lectura (RODC) limita el tráfico de salida porque no replicar saliente. Pero la decisión de implementar un RODC debe ser pesado con la necesidad de realizar operaciones de escritura en el controlador de dominio y la [compatibilidad](https://technet.microsoft.com/library/cc755190) con aplicaciones y servicios en el sitio con RODC. Para obtener más información acerca de los cargos de tráfico, consulte [Azure precios de un vistazo](http://azure.microsoft.com/pricing/).

- Mientras haya completado control sobre qué servidor tamaño de recursos para máquinas virtuales local, como RAM, disco y así sucesivamente en Azure debe seleccionar de una lista de tamaños de servidor preconfigurado. Para un controlador de dominio, se necesita un disco de datos además del disco de sistema operativo para almacenar la base de datos de Active Directory de Windows Server.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>¿Puede implementar Windows Server AD FS en máquinas virtuales de Windows Azure?

Sí, puede implementar Windows Server AD FS en máquinas virtuales de Windows Azure y [los procedimientos recomendados para la implementación de AD FS](https://technet.microsoft.com/library/dn151324.aspx) local afectan a la implementación de AD FS en Azure. Pero algunos de los procedimientos recomendados como Equilibrio de carga y alta disponibilidad requieren tecnologías más allá de lo que AD FS ofrece propiamente dicho. Debe proporcionar la infraestructura subyacente. Vamos a revisar algunas de las mejores prácticas y vea cómo puede conseguirse mediante máquinas virtuales de Azure y una red virtual Azure.

1. **Nunca se exponen servidores de servicio (STS) de token de seguridad directamente a Internet.**

    Esto es importante porque el STS emite tokens de seguridad. Como resultado, servidores de STS como servidores de AD FS se deberían tratar con el mismo nivel de protección como un controlador de dominio. Si está comprometida un STS, los usuarios malintencionados tienen la capacidad de emitir tokens de acceso que potencialmente contiene notificaciones de su elección a aplicaciones de terceros de confianza y otros servidores de STS de confiar en las organizaciones.

2. **Implementar los controladores de dominio de Active Directory para todos los dominios de usuario en la misma red que los servidores de AD FS.**

    Servidores de AD FS usar servicios de dominio de Active Directory para autenticar a los usuarios. Se recomienda implementar controladores de dominio en la misma red que los servidores de AD FS. Esto proporciona continuidad empresarial en caso de que se rompe el vínculo entre la red de Azure y su red local y permite menor latencia y rendimiento mejorado para inicios de sesión.

3. **Implemente varios nodos de AD FS para alta disponibilidad y equilibrio de la carga.**

    En la mayoría de los casos, el error de una aplicación que permite AD FS es aceptable, porque las aplicaciones que requieren el token de seguridad suelen esenciales. Como resultado, y AD FS ahora reside en la ruta crítica para tener acceso a aplicaciones importantes, el servicio de AD FS debe estar altamente disponible a través de varios servidores proxy AD FS y servidores de AD FS. Para lograr la distribución de solicitudes, equilibradores de carga se implementan normalmente delante de los servidores de proxy de AD FS y los servidores de AD FS.

4. **Implementar uno o más nodos de Proxy de la aplicación Web de acceso a internet.**

    Cuando los usuarios necesitan tener acceso a aplicaciones protegidas por el servicio de AD FS, el servicio de AD FS debe estar disponible desde internet. Esto se logra al implementar el servicio Proxy de la aplicación Web. Se recomienda encarecidamente implementar más de un nodo a los efectos de alta disponibilidad y equilibrio de carga.

5. **Restringir el acceso de los nodos de Proxy de la aplicación Web a recursos de red interna.**

    Para permitir que los usuarios externos acceder a AD FS desde internet, debe implementar nodos de Proxy de aplicación Web (o Proxy AD FS en versiones anteriores de Windows Server). Los nodos de proxy de la aplicación Web se exponen directamente a Internet. No tienen que estar unido al dominio y solo necesitan acceso a los servidores de AD FS por los puertos TCP 80 y 443 a. Se recomienda que se bloquea la comunicación a otros equipos (especialmente los controladores de dominio).

    Este es normalmente obtenida local mediante una DMZ. Los firewalls use un modo de funcionamiento de la lista blanca para restringir el tráfico de la DMZ a la red local (es decir, solo se permite el tráfico de las direcciones IP especificadas y por puertos especificados y todos los demás tráfico está bloqueado).

El siguiente diagrama se muestra un tradicional la implementación de AD FS en local.

![Diagrama de una implementación de servicios de federación de Active Directory local tradicional](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

Sin embargo, porque no proporciona nativo de Azure, capacidad de firewall completos, otras opciones sea necesitan para restringir el tráfico. La siguiente tabla muestra cada opción y sus ventajas y desventajas.

| Opción | Ventajas | Desventaja |
| ------ | --------- | ------------ |
| [ACL de red de Azure](virtual-networks-acl.md) | Configuración inicial menos costosa y más sencilla | Se requiere la configuración de ACL de red adicionales si cualquier nuevas máquinas virtuales se agregan a la implementación |
| [Firewall Barracuda NG](https://www.barracuda.com/products/ngfirewall) | Modo de lista blanca de operación y no requiere ninguna configuración de ACL de red | Aumento del costo y la complejidad de la configuración inicial |

En este caso, los pasos para implementar AD FS son los siguientes:

1. Crear una red virtual con conectividad entre local, mediante una conexión VPN o [ExpressRoute](http://azure.microsoft.com/services/expressroute/).

2. Implementar controladores de dominio en la red virtual. Este paso es opcional pero se recomienda.

3. Implementar dominio conjunto de servidores de AD FS en la red virtual.

4. Crear un [conjunto de equilibrio de carga interna](http://azure.microsoft.com/blog/internal-load-balancing/) que incluye los servidores de AD FS y utiliza una nueva dirección IP privada dentro de la red virtual (una dirección IP dinámica).

  1. Actualizar DNS para crear el FQDN para que apunten a la dirección IP (dinámica) privada del conjunto de equilibrio de carga interno.

5. Crear un servicio de nube (o una red virtual independiente) para los nodos de Proxy de la aplicación Web.

6. Implementar los nodos de Proxy de aplicación Web en el servicio de nube o una red virtual

  1. Crear un conjunto de equilibrio de carga externo que incluye los nodos de Proxy de la aplicación Web.

  2. Actualizar el nombre DNS externo (FQDN) para que apunten a la nube servicio pública dirección (la dirección IP virtual).

  3. Configure los servidores proxy AD FS para usar el FQDN que corresponde al conjunto de equilibrio de carga interna para los servidores de AD FS.

  4. Basada en notificaciones de sitios de web para usar el nombre completo externo de su proveedor de notificaciones de actualización.

7. Restringir el acceso entre Proxy de la aplicación Web en cualquier equipo en la red virtual de AD FS.

Para restringir el tráfico, debe estar configurado para el tráfico a los puertos TCP 80 y 443 solo el conjunto de equilibrio de carga para el equilibrador de carga interno Azure, y se coloca el resto de tráfico a la dirección IP dinámica interna del conjunto de equilibrio de carga.

![Diagrama de ADFS ACL de red con TCP 443 + 80 permitido.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

¿Se permita el tráfico a los servidores de AD FS solo por los siguientes orígenes:

- El equilibrador de carga interno Azure.
- La dirección IP de un administrador de la red local.

> [AZURE.WARNING] El diseño debe evitar nodos de Proxy de la aplicación Web se comunique con cualquier otros máquinas virtuales de la red virtual Azure o cualquier ubicación en la red local. Es posible que configurando reglas de firewall en el equipo local para conexiones de ruta Express o el dispositivo VPN para las conexiones VPN de sitio a otro.

Una desventaja de esta opción es necesario configurar la red ACL para varios dispositivos, incluidos equilibrador de carga interno, los servidores de AD FS y cualquier otro servidor que se agrega a la red virtual. Si cualquier dispositivo se agrega a la implementación sin configurar ACL de red para restringir el tráfico a ella, puede ser la implementación completa en riesgo. Si alguna vez cambian las direcciones IP de los nodos de Proxy de aplicación Web, se debe restablecer la red ACL (lo que significa que los servidores proxy deben estar configurados para utilizar [direcciones IP dinámicas](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)).

![ADFS en Azure con ACL de red.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Otra opción es usar el dispositivo [Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) para controlar el tráfico entre los servidores proxy AD FS y los servidores de AD FS. Esta opción cumple con los procedimientos recomendados para la seguridad y de alta disponibilidad y requiere menos administración después de la instalación inicial porque el dispositivo de Barracuda NG Firewall proporciona un modo blanca de administración de firewall y se puede instalar directamente en una red virtual Azure. Elimina la necesidad de configurar ACL de red en cualquier momento en que se agrega un servidor nuevo a la implementación. Pero esta opción agrega el costo y la complejidad de la implementación inicial.

En este caso, se implementan dos redes virtuales en lugar de uno. Le llamamos VNet1 y VNet2. VNet1 contiene a los servidores proxy y VNet2 la STSs y la conexión de red a la red corporativa. VNet1 es, por tanto, física (aunque prácticamente) aislado de VNet2 y, a su vez, de la red corporativa. VNet1 luego se conecta a VNet2 con una tecnología túnel especial conocida como transporte independiente red arquitectura (TINA). Túnel de TINA está conectado a cada una de las redes virtuales utiliza un firewall Barracuda NG: uno Barracuda en cada una de las redes virtuales.  Para alta disponibilidad, se recomienda que implemente dos Barracudas en cada red virtual; un activo, el otro pasivo. Ofrecen capacidades de utilización servidores de seguridad muy eficaces que nos permiten imitar el funcionamiento de una DMZ local tradicional en Azure.

![ADFS en Azure con firewall.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Para obtener más información, vea [AD FS: Extender una aplicación front-end de notificaciones de local a Internet](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Una alternativa a la implementación de AD FS si el objetivo es solo de SSO de Office 365

Hay otra alternativa para implementar AD por completo de FS si su objetivo es solo habilitar iniciar sesión en Office 365. En ese caso, simplemente puede implementar la sincronización de directorios con la sincronización de contraseña en local y conseguir el mismo resultado final con la complejidad de la implementación mínima porque este enfoque no requiere AD FS o Azure.

La siguiente tabla compara el funcionamiento de los procesos de inicio de sesión con y sin implementar AD FS.

| Office 365 solo con AD FS y sincronización de directorios de inicio de sesión | Office 365 misma sesión con DirSync + sincronización de la contraseña |
| ------------- | ------------- |
| 1. el usuario inicia sesión en una red corporativa y se autentica a Active Directory de Windows Server. | 1. el usuario inicia sesión en una red corporativa y se autentica a Active Directory de Windows Server. |
| 2. el usuario intenta obtener acceso a Office 365 (estoy @contoso.com). | 2. el usuario intenta obtener acceso a Office 365 (estoy @contoso.com). |
| 3. office 365 redirige al usuario a Azure AD. | 3. office 365 redirige al usuario a Azure AD. |
| 4. ya que Azure AD no puede autenticar al usuario y comprende que hay una confianza con AD FS local, redirige al usuario a AD FS. | 4. azure AD no puede aceptar vales Kerberos directamente y no existe ninguna relación de confianza para que solicita que el usuario escriba las credenciales. |
| 5. el usuario envía un vale Kerberos al STS de AD FS. | 5. el usuario escribe la misma contraseña local y Azure AD los valida contra el nombre de usuario y la contraseña que se ha sincronizado mediante la sincronización de directorios. |
| 6. AD FS transforma el vale Kerberos en el formato o reclamaciones necesarias tokens y redirige al usuario a Azure AD. | 6. azure AD redirige al usuario a Office 365. |
| 7. el usuario se autentica a Azure AD (otra transformación se produce). |  7. el usuario puede iniciar sesión en Office 365 y OWA mediante el token de Azure AD. |
| 8. azure AD redirige al usuario a Office 365. |  |
| 9. el usuario silenciosamente ha iniciado sesión Office 365. |  |

Office 365 con la sincronización de directorios con escenario de sincronización de contraseña (sin AD FS), inicio de sesión único se reemplaza por "misma sesión" donde "mismo" significa que los usuarios deben volver a escribir sus credenciales local mismo al acceder a Office 365. Tenga en cuenta que estos datos pueden recordará por el explorador del usuario para ayudar a reducir las siguientes indicaciones.

### <a name="additional-food-for-thought"></a>Ideas adicionales

- Si implementa a un proxy AD FS en una máquina virtual Azure, es necesaria la conectividad a los servidores de AD FS. Si son locales, se recomienda que aprovechar la conectividad VPN de sitio a sitio proporcionada por la red virtual para permitir que los nodos de Proxy de la aplicación Web para comunicarse con los servidores de AD FS.

- Si implementa un servidor de AD FS en una máquina virtual Azure, conectividad con controladores de dominio de Active Directory de Windows Server, atributo almacena y bases de datos de configuración es necesario y también puede requerir un ExpressRoute o una conexión VPN de sitio a sitio entre la red virtual Azure y la red local.

- Gastos se aplican a todo el tráfico de Azure máquinas virtuales de Windows (tráfico de salida). Si el coste es el factor de conducción, es aconsejable implementar los nodos de Proxy de la aplicación Web de Azure, dejando el AD FS servidores local. Si se implementan los servidores de AD FS en máquinas virtuales Azure así, se producirán costos adicionales para autenticar a los usuarios locales. Tráfico de salida incurre en un costo independientemente de si o no recorre el ExpressRoute o la conexión de sitio a sitio VPN.

- Si decide usar capacidades de alta disponibilidad de servidores de AD FS de equilibrio de carga de servidor nativo de Azure, tenga en cuenta que Equilibrio de carga proporciona sondeos que se usan para determinar el estado de las máquinas virtuales dentro del servicio de nube. En el caso de Azure máquinas virtuales de Windows (en lugar de los roles de web o de trabajo), debe usarse un sondeo personalizada desde el agente que responde a los sondeos predeterminado no está presente en máquinas virtuales de Windows Azure. Para simplificar, puede usar un sondeo TCP personalizada: sólo requiere una conexión de TCP (un segmento TCP SYN enviados y responde con un segmento TCP SYN ACK) establecerse correctamente para determinar el estado de la máquina virtual. Puede configurar el sondeo personalizada para usar cualquier puerto TCP a los que están escucha sus máquinas virtuales.

> [AZURE.NOTE] Equipos que necesitan para exponer el mismo conjunto de puertos directamente a Internet (por ejemplo, los puertos 80 y 443) no pueden compartir el mismo servicio de nube. Por lo tanto, se, recomienda que cree una nube dedicada servicio para los servidores de Windows Server AD FS para evitar posibles se superpone entre los requisitos de puerto para una aplicación y Windows Server AD FS.

## <a name="deployment-scenarios"></a>Escenarios de implementación

En la sección siguiente describe escenarios de implementación común para llamar la atención sobre aspectos importantes que se deben tener en cuenta. Cada escenario contiene vínculos a más información sobre las decisiones y factores para tener en cuenta.

1. [AD DS: Implementar una aplicación de AD DS-aware no requiere conectividad de red corporativa](#BKMK_CloudOnly)

    Por ejemplo, un servicio de SharePoint a través de Internet se implementa en una máquina virtual Azure. La aplicación no tiene dependencias en los recursos de la red corporativa. La aplicación requieren Windows Server AD DS pero no requiere Windows Server AD DS corporativo.

2. [AD FS: Extender una aplicación front-end de notificaciones de local a Internet](#BKMK_CloudOnlyFed)

    Por ejemplo, una aplicación de notificaciones que se ha implementado correctamente en local y usados por los usuarios corporativos debe ser accesible desde Internet. La aplicación debe tener acceso directamente a través de Internet con sus propias identidades corporativas de asociados de negocios y usuarios corporativos existentes.

3. [AD DS: Implementar una aplicación de Windows Server AD DS-aware que requiere conectividad con la red corporativa](#BKMK_HybridExt)

    Por ejemplo, una aplicación compatible con LDAP que admite la autenticación de Windows integrada y usa Windows Server AD DS como un repositorio de datos de configuración y el perfil de usuario se implementa en una máquina virtual Azure. Es conveniente para la aplicación aprovechar los servicios de directorio de Windows Server AD corporativa existente y proporcionar el inicio de sesión único. La aplicación no está notificaciones.

### <a name="BKMK_CloudOnly"></a>1. AD DS: Implementar una aplicación de AD DS-aware no requiere conectividad de red corporativa

![Implementación de AD DS solo nube](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**figura 1**

#### <a name="description"></a>Descripción

SharePoint se implementa en una máquina virtual Azure y la aplicación no tiene dependencias de recursos de la red corporativa. La aplicación requiere Windows Server AD DS pero no *no* requieren corporativa Windows Server AD DS. Kerberos o federadas confianzas no son necesarias porque los usuarios se aprovisiona automática a través de la aplicación en el dominio de Windows Server AD DS que también se hospeda en la nube en máquinas virtuales de Windows Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Consideraciones de escenario y cómo se aplican las áreas de tecnología al escenario

- [Topología de red](#BKMK_NetworkTopology): crear una red virtual Azure sin conexión entre local (también conocido como conectividad de sitio a sitio).

- [Configuración de implementación de CC](#BKMK_DeploymentConfig): implementar un controlador de dominio nuevo en un bosque nuevo dominio, Active Directory de Windows Server. Esto se debe implementar junto con el servidor DNS de Windows.

- [Topología de sitio de Active Directory de Windows Server](#BKMK_ADSiteTopology): utilice el sitio de Active Directory de Windows Server predeterminado (todos los equipos tendrán en nombre-predeterminado-primer-sitio).

- [Direcciones IP y DNS](#BKMK_IPAddressDNS):

 - Establecer una dirección IP estática para el DC mediante el cmdlet Set-AzureStaticVNetIP Azure PowerShell.
 - Instalar y configurar DNS de Windows Server en los controladores de dominio en Azure.
 - Configurar las propiedades de una red virtual con el nombre y la dirección IP de la máquina virtual que hospeda las funciones de servidor DNS y DC.

- [Catálogo global](#BKMK_GC): el primer DC del bosque debe ser un servidor de catálogo global. Controladores de dominio adicionales también deben configurarse como catálogos globales porque en un único bosque de dominios, el catálogo global no requiere ningún trabajo adicional en el controlador de dominio.

- [Ubicación de la base de datos de Windows Server AD DS y SYSVOL](#BKMK_PlaceDB): agregar un disco de datos a los controladores de dominio que se ejecuta como máquinas virtuales de Azure para almacenar la base de datos, los registros y SYSVOL de Active Directory de Windows Server.

- [Copia de seguridad y restauración](#BKMK_BUR): Determine dónde desea almacenar copias de seguridad de estado de sistema. Si es necesario, agregue otro disco datos VM DC almacenar copias de seguridad.

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS: Extender una aplicación front-end de notificaciones de local a Internet

![La federación con conectividad entre local](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**figura 2**

#### <a name="description"></a>Descripción

Una aplicación de notificaciones que se ha implementado correctamente en local y usados por los usuarios corporativos debe ser accesible directamente desde Internet. La aplicación actúa como un front-end de web a una base de datos SQL en la que almacena los datos. Los servidores de SQL Server usadas la aplicación también se encuentran en la red corporativa. Un equilibrador de carga y STSs de dos Windows Server AD FS han sido implementado en local para proporcionar acceso a los usuarios corporativos. La aplicación ahora debe además acceder directamente a través de Internet con sus propias identidades corporativas de asociados de negocios y usuarios corporativos existentes.

En un esfuerzo para simplificar y satisfacer las necesidades de implementación y configuración de este nuevo requisito, se decide que dos adicionales web frontends y dos servidores proxy de Windows Server AD FS esté instalado en máquinas virtuales de Windows Azure. Todas las máquinas virtuales de cuatro se exponen directamente a Internet y se proporcionará conectividad a la red local con la capacidad VPN de sitio a sitio de Azure Virtual de la red.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Consideraciones de escenario y cómo se aplican las áreas de tecnología al escenario

- [Topología de red](#BKMK_NetworkTopology): crear una red virtual Azure y [Configurar la conectividad entre local](../vpn-gateway/vpn-gateway-site-to-site-create.md).

 > [AZURE.NOTE] Para cada uno de los certificados de Windows Server AD FS, asegúrese de que el Windows Server AD FS instancias se ejecutan en Azure puede llegar a la dirección URL definida dentro de la plantilla de certificado y los certificados resultantes. Esto puede requerir conectividad entre local a partes de la infraestructura de PKI. Por ejemplo si extremo del CRL está basado en LDAP y alojado exclusivamente local, a continuación, entre locales conectividad será necesaria. Si esto no es posible, puede ser necesario usar certificados emitidos por una CA cuyo CRL es accesible a través de Internet.

- [Configuración de servicios de nube](#BKMK_CloudSvcConfig): asegúrese de que tiene dos servicios en la nube en orden proporciona dos direcciones IP virtuales equilibrio de carga. Dirección IP virtual del servicio de nube primera se dirige a las dos Windows Server AD FS proxy máquinas virtuales en los puertos 80 y 443. El proxy de Windows Server AD FS máquinas virtuales se configurará para que apunte a la dirección IP del equilibrador de carga de local que ámbitos la STSs de Windows Server AD FS. Dirección IP virtual del servicio de nube segunda se dirige a las dos máquinas virtuales volver a ejecutar el cliente de web en los puertos 80 y 443. Configurar un sondeo personalizada para garantizar que el equilibrador de carga dirige solo el tráfico al funcionamiento Windows Server AD FS proxy y web front-end máquinas virtuales.

- [Configuración del servidor de federación](#BKMK_FedSrvConfig): configurar Windows servidor AD FS como un servidor de federación (STS) para generar tokens de seguridad para el bosque de Active Directory de Windows Server creado en la nube. Federación las relaciones de confianza del proveedor de notificaciones con los diferentes socios de que desea aceptar identidades y configurar las relaciones de confianza de confianza fiesta con las diferentes aplicaciones a que desea generar tokens.

    En la mayoría de los escenarios, servidores proxy de Windows Server AD FS se implementan en una capacidad a través de Internet por motivos de seguridad mientras sus homólogos de federación de Windows Server AD FS permanecen aislados de conectividad directa a Internet. Independientemente del escenario de implementación, debe configurar su servicio de nube con una dirección IP virtual que le proporcionará una dirección IP y puerto que sea capaz de equilibrio de carga a través de los dos instancias de Windows Server AD FS STS ni instancias de proxy expuesto públicamente.

- [Configuración de alta disponibilidad de Windows Server AD FS](#BKMK_ADFSHighAvail): es aconsejable implementar una granja de servidores de Windows Server AD FS con al menos dos servidores de migración tras error y equilibrio de carga. Posible que desee considerar usar la base de datos internos de Windows (WID) de los datos de configuración de Windows Server AD FS y utilizar la capacidad de equilibrio de carga interno de Azure para distribuir las solicitudes entrantes en los servidores de la granja.

Para obtener más información, consulte la [Guía de implementación de AD DS](https://technet.microsoft.com/library/cc753963).


### <a name="BKMK_HybridExt"></a>3. AD DS: Implementar una aplicación de Windows Server AD DS-aware que requiere conectividad con la red corporativa

![Implementación de AD DS entre local](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**figura 3**

#### <a name="description"></a>Descripción

Una aplicación compatible con LDAP se implementa en una máquina virtual Azure. Admite la autenticación de Windows integrada y usa Windows Server AD DS como un repositorio de datos de perfil de usuario y la configuración. El objetivo es que la aplicación aprovechar el Windows Server AD DS corporativo existente y proporcionar el inicio de sesión único. La aplicación no está notificaciones. Los usuarios también necesitan tener acceso a la aplicación directamente desde Internet. Para optimizar el rendimiento y el costo, se decide que dos controladores de dominio adicionales que forman parte del dominio corporativo implementará junto con la aplicación en Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Consideraciones de escenario y cómo se aplican las áreas de tecnología al escenario

- [Topología de red](#BKMK_NetworkTopology): crear una red virtual Azure con [conectividad entre local](../vpn-gateway/vpn-gateway-site-to-site-create.md).

- [Método de instalación](#BKMK_InstallMethod): implementar controladores de dominio de réplica del dominio de Active Directory de Windows Server corporativa. Para una réplica DC, puede instalar Windows Server AD DS en la máquina virtual y, opcionalmente, use la característica de instalación desde medios (IFM) para reducir la cantidad de datos que se pueden replicar en el controlador de dominio durante la instalación. Para obtener un tutorial, consulte [instalar un controlador de dominio de Active Directory réplica en Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Incluso si usa IFM, puede resultar más eficaz para generar el controlador de dominio virtual local y mover el disco duro Virtual (VHD) todo a la nube en lugar de replicar Windows Server AD DS durante la instalación. Por motivos de seguridad, se recomienda que elimine el disco duro virtual de la red local una vez que se ha copiado en Azure.

- [Topología de sitio de Active Directory de Windows Server](#BKMK_ADSiteTopology): crear un nuevo sitio de Azure en sitios de Active Directory y servicios. Crear un objeto de subred de Active Directory de Windows Server para representar la red virtual Azure y agregar la subred al sitio. Crear un nuevo vínculo de sitio que contiene el nuevo sitio de Azure y el sitio en el que se encuentra para controlar y optimizar el tráfico de Active Directory de Windows Server y de Azure la red virtual Azure extremo de la VPN.

- [Direcciones IP y DNS](#BKMK_IPAddressDNS):

 - Establecer una dirección IP estática para el DC mediante el cmdlet Set-AzureStaticVNetIP Azure PowerShell.
 - Instalar y configurar DNS de Windows Server en los controladores de dominio en Azure.
 - Configurar las propiedades de una red virtual con el nombre y la dirección IP de la máquina virtual que hospeda las funciones de servidor DNS y DC.

- [Distribuido geo DC](#BKMK_DistributedDCs): configurar redes virtuales adicionales según sea necesario. Si la topología de sitio de Active Directory requiere DC en ubicaciones geográficas que corresponden a diferentes regiones Azure, que desea crear sitios de Active Directory en consecuencia.

- [Controladores de dominio de sólo lectura](#BKMK_RODC): puede implementar un RODC en el sitio de Azure, según los requisitos para la realización de escribir operaciones en el controlador de dominio y la compatibilidad de aplicaciones y servicios en el sitio con RODC. Para obtener más información sobre compatibilidad de aplicaciones, consulte la [Guía de compatibilidad de aplicaciones de controladores de dominio de sólo lectura](https://technet.microsoft.com/library/cc755190).

- [Catálogo global](#BKMK_GC): catálogos globales son necesarias para atender las solicitudes de inicio de sesión en bosques con varios dominios. Si no implementa un catálogo global en el sitio de Azure, como las solicitudes de autenticación producen consultas catálogos globales en otros sitios producirá los costos de tráfico de salida. Para reducir el tráfico, puede habilitar el almacenamiento en caché de la pertenencia a un grupo universal para el sitio de Azure en sitios de Active Directory y servicios.

    Si implementa un catálogo global, configurar vínculos a sitios y los costos de vínculos de sitio para que el catálogo global en el sitio de Azure no es preferido como origen de DC por otros catálogos globales que necesitan para replicar las particiones del mismo dominio parcial.

- [Ubicación de la base de datos de Windows Server AD DS y SYSVOL](#BKMK_PlaceDB): agregar un disco de datos a los controladores de dominio que se ejecutan en máquinas virtuales de Azure para almacenar la base de datos, los registros y SYSVOL de Active Directory de Windows Server.

- [Copia de seguridad y restauración](#BKMK_BUR): Determine dónde desea almacenar copias de seguridad de estado de sistema. Si es necesario, agregue otro disco datos VM DC almacenar copias de seguridad.

## <a name="deployment-decisions-and-factors"></a>Factores y las decisiones de implementación

Esta tabla resumen las áreas de tecnología de Active Directory de Windows Server que se ven afectadas en las situaciones anteriores y las decisiones correspondientes para tener en cuenta, con vínculos a más detalle. Algunas áreas de tecnología no sea aplicables a cada escenario de implementación y algunas áreas de tecnología pueden ser más importantes para un escenario de implementación que otras áreas de tecnología.

Por ejemplo, si implementa una réplica DC en una red virtual y su bosque tiene un solo dominio, eligiendo implementar un servidor de catálogo global en ese caso no serán crítica para el escenario de implementación porque no creará los requisitos de replicación adicional. Por otro lado, si el bosque tiene varios dominios, la decisión de implementar un catálogo global en una red virtual podría afectar ancho de banda disponible, rendimiento, autenticación, las búsquedas de directorio y así sucesivamente.

| Área de tecnología de Active Directory de Windows Server | Decisiones | Factores |
| ---- | ---- | ---- |
| [Topología de red](#BKMK_NetworkTopology) | ¿Se crea una red virtual? | <li>Requisitos de acceso a los recursos de Corp</li> <li>Autenticación</li> <li>Administración de cuentas</li> |
| [Configuración de implementación de DC](#BKMK_DeploymentConfig) | <li>¿Implementar un bosque independiente sin las relaciones de confianza?</li> <li>¿Implementar un nuevo bosque con la federación?</li> <li>¿Implementar un nuevo bosque con confianza de bosque de Active Directory de Windows Server o Kerberos?</li> <li>¿Extender bosque Corp implementando una réplica DC?</li> <li>¿Extender bosque Corp implementando un nuevo dominio secundario o un árbol de dominio?</li> | <li>Seguridad</li> <li>Cumplimiento</li> <li>Costo</li> <li>Resistencia y tolerancia a errores</li> <li>Compatibilidad de aplicaciones</li> |
| [Topología de sitio de Active Directory de Windows Server](#BKMK_ADSiteTopology) | ¿Cómo se configura subredes, sitios y vínculos a sitios con Azure una red Virtual para optimizar el tráfico y minimizar los costos? | <li>Definiciones de sitio y de subred</li> <li>Notificación de cambios y de propiedades de vínculo de sitio</li> <li>Compresión de replicación</li> |
| [Direcciones IP y DNS](#BKMK_IPAddressDNS) | ¿Cómo puedo configurar direcciones IP y resolución de nombres? | <li>Use el cmdlet de usar el AzureStaticVNetIP establecer para asignar una dirección IP estática</li> <li>Instalar a Windows servidor DNS y configurar las propiedades de una red virtual con el nombre y la dirección IP de la máquina virtual que hospeda las funciones de servidor DNS y DC</li> |
| [Distribuido geo DC](#BKMK_DistributedDCs) | ¿Cómo puedo replicar a DC en redes virtuales independientes? | Si la topología de sitio de Active Directory requiere DC en ubicaciones geográficas que corresponde a diferentes regiones Azure, que desea crear sitios de Active Directory en consecuencia. [Configurar una red virtual para la conectividad de red virtual](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) para replicar entre controladores de dominio en redes virtuales independientes. |
| [Controladores de dominio de solo lectura](#BKMK_RODC) | ¿Usar los controladores de dominio de solo lectura o de escritura? | <li>Repercusión/PII atributos de filtro</li> <li>Filtrar información confidencial</li> <li>Limitar el tráfico saliente</li> |
| [Catálogo global](#BKMK_GC) | ¿Instalar catálogo global? | <li>Bosque de dominio único, asegúrese de todos los catálogos globales de DC</li> <li>Para bosque con varios dominios, catálogos globales son necesarios para la autenticación</li> |
| [Método de instalación](#BKMK_InstallMethod) | ¿Cómo puedo instalar DC en Azure? | Ambos: <li>Instalar AD DS con Windows PowerShell o Dcpromo</li> <li>Mover el disco duro virtual de un controlador de dominio local virtual</li> |
| [Ubicación de la base de datos de Windows Server AD DS y SYSVOL](#BKMK_PlaceDB) | ¿Dónde almacenar Windows Server AD DS base de datos, los registros y SYSVOL? | Cambiar valores predeterminados de Dcpromo.exe. Estos crítica Active Directory archivos *debe* situarse en discos de datos de Azure en lugar de discos de sistema operativo que implementan la caché de escritura. |
| [Copia de seguridad y restauración](#BKMK_BUR) | ¿Cómo puedo proteger y recuperar datos? | Crear copias de seguridad de estado de sistema |
| [Configuración del servidor de federación](#BKMK_FedSrvConfig) | <li>¿Implementar un nuevo bosque con la federación en la nube?</li> <li>Implementar AD FS local y exponer a un servidor proxy en la nube?</li> | <li>Seguridad</li> <li>Cumplimiento</li> <li>Costo</li> <li>Acceso a aplicaciones de asociados de negocios</li> |
| [Configuración de servicios de nube](#BKMK_CloudSvcConfig) | Un servicio de nube implícitamente se implementa la primera vez que cree una máquina virtual. ¿Necesita implementar los servicios de nube adicionales? | <li>¿Una máquina virtual o VM requiere exposición directa a Internet?</li> <li> ¿Necesita el servicio de equilibrio de carga?</li> |
| [Requisitos de servidor de federación para IP pública y privada direccionamiento (IP dinámica frente IP virtual)](#BKMK_FedReqVIPDIP) | <li>¿Necesita la instancia de Windows Server AD FS puede llegar directamente desde Internet?</li> <li>¿La aplicación se implementa en la nube requiere su propia dirección IP a través de Internet y puerto?</li> | Crear un servicio de nube para cada dirección IP virtual que es necesario para su implementación |
| [Configuración de alta disponibilidad de Windows Server AD FS](#BKMK_ADFSHighAvail) | <li>¿Número de nodos en mi granja de servidores de Windows Server AD FS?</li> <li>¿Número de nodos a implementar en mi granja de servidores de Windows Server AD FS proxy?</li> | Resistencia y tolerancia a errores |

### <a name="BKMK_NetworkTopology"></a>Topología de red

Para cumplir con los requisitos de DNS de Windows Server AD DS y coherencia de dirección IP, es necesario crear una [red virtual Azure](../virtual-network/virtual-networks-overview.md) primero y adjuntar sus máquinas virtuales a él. Durante su creación, debe decidir si desea ampliar opcionalmente conectividad a su red corporativa local, que conecta transparente máquinas virtuales de Windows Azure a equipos locales, esto se logra con las tecnologías VPN tradicionales y requiere que un extremo de la VPN se expongan en el borde de la red corporativa. Es decir, la conexión VPN se inicia de Azure en la red corporativa, no a la inversa.

Tenga en cuenta que aplicar cargos adicionales al extender una red virtual a su red local más allá de los cargos estándares que se aplican a cada máquina virtual. Más concretamente, hay cargos para el tiempo de CPU de la puerta de enlace de red Virtual de Azure y para el tráfico de salida generado por cada VM que se comunica con equipos locales a través de la VPN. Para obtener más información acerca de los cargos de tráfico de red, vea [Azure precios de un vistazo](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Configuración de implementación de DC

La forma de configurar el controlador de dominio depende de los requisitos para el servicio que desea ejecutar en Azure. Por ejemplo, puede implementar un nuevo bosque aislado desde su propio bosque corporativo, para realizar pruebas de una prueba de concepto, una nueva aplicación u otro proyecto a corto plazo que requiere servicios de directorio, pero no es específica de acceso a recursos internos de la empresa.

Como beneficio, un bosque aislado con que DC no replicar locales DC de salida menos tráfico de red generado por el sistema, directamente reducir los costos. Para obtener más información acerca de los cargos de tráfico de red, vea [Azure precios de un vistazo](http://azure.microsoft.com/pricing/).

Otro ejemplo, supongamos que tiene requisitos de privacidad de un servicio, pero el servicio depende de acceso a su servidor de Windows interno Active Directory. Si se permite a los datos de host para el servicio en la nube, puede implementar un nuevo dominio secundario de bosque interno en Azure. En este caso, puede implementar un controlador de dominio para el nuevo dominio secundario (sin el catálogo global para ayudar a solucionar problemas de privacidad). En este escenario, junto con una implementación de DC, réplica requiere una red virtual para conectividad con los controladores de dominio local.

Si crea un nuevo bosque, elija si desea utilizar [confianzas de Active Directory](https://technet.microsoft.com/library/cc771397) o [confianzas de federación](https://technet.microsoft.com/library/dd807036). Equilibrar los requisitos dictados por compatibilidad, seguridad, cumplimiento, costo y resistencia. Por ejemplo, para aprovechar las ventajas de [la autenticación selectiva](https://technet.microsoft.com/library/cc755844) puede implementar un nuevo bosque en Azure y crear una confianza de Active Directory de Windows Server entre los bosques local y la nube. Sin embargo, si la aplicación está notificaciones, puede implementar confianzas de federación en lugar de bosques de Active Directory. Otro factor será el coste replicar más datos ampliar su en Windows Server Active Directory local en la nube o generar tráfico saliente más como resultado de la autenticación y la carga de consultas.

Requisitos de disponibilidad y tolerancia a errores también afectan a su elección. Por ejemplo, si se interrumpe el vínculo, aplicaciones para aprovechar la confianza Kerberos o una federación confían están todos probablemente completamente rotos a menos que se han implementado infraestructuras suficientes en Azure. Configuraciones de implementación alternativo como réplica DC (grabable o RODC), las probabilidades de poder tolerar interrupciones del vínculo.

### <a name="BKMK_ADSiteTopology"></a>Topología de sitio de Active Directory de Windows Server

Debe definir sitios y vínculos a sitios para optimizar el tráfico y minimizar el costo de correctamente. Sitios, vínculos a sitios y subredes afectan a la topología de replicación entre los controladores de dominio y el flujo del tráfico de autenticación. Tenga en cuenta los siguientes gastos de tráfico y, a continuación, implementar y configurar DC según los requisitos de su escenario de implementación:

- Hay una cuota nominal por hora para la puerta de enlace:

 - Pueden iniciar y detener como le convenga

 - Si detiene, máquinas virtuales de Azure están aisladas de la red corporativa

- Tráfico entrante es gratuito

- El tráfico saliente cobra según [Azure precios de un vistazo](http://azure.microsoft.com/pricing/). Puede optimizar propiedades de vínculo de sitio entre los sitios local y la nube como sigue:

 - Si usa varias redes virtuales, configurar los vínculos del sitio y sus costos correctamente para impedir que Windows Server AD DS priorizar el sitio de Azure sobre uno de los que puede proporcionar los mismos niveles de servicio sin cargo. También puede deshabilitar el puente de todo el sitio (BASL) vínculo (que está habilitado de forma predeterminada). Así se garantiza que sólo conecta directamente sitios replican entre sí. Controladores de sitios transitivamente conectados ya no son pueden replicar directamente con otros, pero deben replicar a través de un sitio común o sitios. Si los sitios intermediarios no estarán disponibles por algún motivo, no se realizará la replicación entre los controladores de sitios transitivamente conectados incluso si está disponible la conectividad entre los sitios. Por último, en las secciones de comportamiento de replicación transitivos permanecen convenientes, crear sitio puentes de vínculos que contienen los vínculos de sitio adecuados y sitios, como local, sitios de la red corporativa.

 - [Configurar los costos del vínculo de sitio](https://technet.microsoft.com/library/cc794882) adecuadamente para evitar el tráfico no intencionado. Por ejemplo, si se habilita la opción de **Intentar siguiente sitio más cercano** , asegúrese de que los sitios de una red virtual no son la cercano aumentando los costos asociados del objeto de vínculo de sitio que el sitio de Azure que se conecta a la red corporativa.

 - Configurar sitios vínculo [intervalos](https://technet.microsoft.com/library/cc794878) y [programaciones](https://technet.microsoft.com/library/cc816906) en función de los requisitos de coherencia y la tasa de cambios en el objeto. Alinear a la programación de replicación con tolerancia de latencia. DC replicarán solo el último estado de un valor para disminuir el intervalo de replicación puede ahorrar costos si hay un objeto suficiente cambiar tasa.

- Si se minimiza los costos es una prioridad, asegúrese de replicación se ha programado y notificación de cambios no está habilitada. Esta es la configuración predeterminada al replicarse entre sitios. Esto no es importante si va a implementar un RODC en una red virtual porque el RODC no replica los cambios de salida. Pero si implementa de escritura, debe asegurarse de que el vínculo de sitio no está configurado para replicar actualizaciones con frecuencia innecesario. Si implementa un servidor de catálogo global (catálogo global), asegúrese de que todos los sitios que contiene un catálogo global aplica particiones de dominio desde un origen de DC en un sitio que está conectado con un vínculo a sitios o vínculos de sitios que tienen un costo menor que el catálogo global en el sitio de Azure.


- Es posible reducir aún más el tráfico de red generado por la replicación entre sitios cambiando el algoritmo de compresión de replicación. El algoritmo de compresión está controlado por el algoritmo de compresión REG_DWORD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator de entrada de registro. El valor predeterminado es 3, que está relacionado con el algoritmo de compresión Xpress. Puede cambiar el valor a 2, lo que cambia el algoritmo a MSZip. En la mayoría de los casos, Esto aumentará la compresión, pero lo hace a costa de la CPU. Para obtener más información, vea la [topología de replicación de Active Directory cómo funciona](https://technet.microsoft.com/library/cc755994).

### <a name="BKMK_IPAddressDNS"></a>Direcciones IP y DNS

Máquinas virtuales de Windows Azure se asignan "concedida por DHCP direcciones" de forma predeterminada. Dado que las direcciones de una red virtual Azure dinámicas conservan con una máquina virtual para la duración de la máquina virtual, se cumplen los requisitos de Windows Server AD DS.

Como resultado, cuando se utiliza una dirección dinámica en Azure, se encuentra en efecto mediante una dirección IP estática porque es enrutable para el período del contrato y el período de la concesión es igual a la duración del servicio de nube.

Sin embargo, asignación se cancela la dirección dinámica si la máquina virtual se cierra. Para impedir que la dirección IP que se cancela la asignación, puede [usar Set-AzureStaticVNetIP para asignar una dirección IP estática](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Resolución de nombres, implementar sus propios (o aprovechar la existente) infraestructura de servidor DNS; DNS siempre Azure no alcanza las necesidades de resolución de nombre avanzadas de Windows Server AD DS. Por ejemplo, no admite registros SRV dinámicos y así sucesivamente. Resolución de nombres es un elemento de configuración crítica para controladores de dominio y clientes de dominio. Controladores de dominio deben ser capaces de registrar registros de recursos y resolver los registros de recursos de otros DC.
Por motivos de rendimiento y tolerancia a errores, es óptima para instalar el servicio DNS de Windows Server en los controladores de dominio que se ejecuta en Azure. Configurar las propiedades de una red virtual Azure con el nombre y la dirección IP del servidor DNS. Cuando inicia otras máquinas virtuales de la red virtual, su configuración de resolución del cliente DNS se configurará con el servidor DNS como parte de la asignación de dirección IP dinámica.

> [AZURE.NOTE] No puede unirse a equipos locales a un dominio de Active Directory de Windows Server AD DS que se hospeda en Azure directamente a través de Internet. Los requisitos de puerto para la operación de unión de dominio y de Active Directory representan práctico directamente exponen los puertos necesarios y de facto, un controlador de dominio completo a Internet.

Máquinas virtuales de registran su nombre DNS automáticamente al inicio o cuando hay un cambio de nombre.

Para obtener más información acerca de este ejemplo y otro ejemplo que muestra cómo aprovisionar la primera VM e instalar AD DS en él, consulte [instalar un nuevo bosque de Active Directory en Microsoft Azure](active-directory-new-forest-virtual-machine.md). Para obtener más información sobre el uso de Windows PowerShell, vea [Instalar Azure PowerShell](../powershell-install-configure.md) y [Los Cmdlets de administración de Azure](https://msdn.microsoft.com/library/azure/jj152841).

### <a name="BKMK_DistributedDCs"></a>Distribuido geo DC

Azure ofrece ventajas al hospedar varios DC en redes virtuales diferentes:

- Tolerancia a errores de múltiples sitios

- Proximidad física a sucursales (latencia inferior)

Para obtener información acerca de cómo configurar la comunicación directa entre redes virtuales, vea [Configurar una red virtual a la conectividad de red virtual](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Controladores de dominio de solo lectura

Debe decidir si desea implementar controladores de dominio de solo lectura o de escritura. Podría estar inclinada ángulo para implementar RODC porque no tendrá un control físico sobre ellos, pero los RODC están diseñados para ser implementado en ubicaciones donde su seguridad física es un riesgo, como sucursales.

Azure no presenta el riesgo de seguridad físico de una sucursal, pero aún pueden resultar RODC sean más eficaces debido a las características que se proporcionan son adecuadas para estos entornos aunque por razones muy diferentes. Por ejemplo, los RODC no tienen replicación saliente y pueden rellenar de forma selectiva información confidencial (contraseñas). La falta de estas información confidencial que podría requerir a petición el tráfico saliente para validar ellos como un usuario o equipo autentica la desventaja. Pero puede previamente selectivamente información confidencial y en caché.

Los RODC ofrecen una ventaja adicional en y en otros aspectos Repercusión y PII porque se pueden agregar atributos que contienen datos confidenciales en el RODC filtran el conjunto de atributos (FAS). La FAS es un conjunto de atributos que no se duplican en RODC personalizable. Puede usar el FAS como protección en caso de que no se permite o no desea almacenar PII o Repercusión en Azure. Para obtener más información, consulte [RODC filtrado atributo. establecer [(https://technet.microsoft.com/library/cc753459)]

Asegúrese de que las aplicaciones será compatibles con RODC que planea usar. Muchas aplicaciones habilitado de Active Directory de Windows Server funcionan bien con RODC, pero algunas aplicaciones pueden realizar ineficaz o fallar si no tiene acceso a un DC de escritura. Para obtener más información, consulte la [Guía de compatibilidad de aplicaciones de controladores de dominio de sólo lectura](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Catálogo global

Debe elegir si desea instalar un catálogo global (catálogo global). En un único bosque de dominios, debe configurar todos los controladores de dominio como servidores de catálogo global. No aumentará costos porque no habrá ninguna tráfico de replicación adicional.

En un bosque con varios dominios, catálogos globales son necesarios para expandir la pertenencia a grupos Universal durante el proceso de autenticación. Si no implementa un catálogo global, cargas de trabajo en la red virtual que autentican en un controlador de dominio en Azure indirectamente generará tráfico de autenticación de salida para consultar catálogos globales local durante cada intento de autenticación.

Los costos asociados con catálogos globales son menos predecibles porque hospedan cada dominio (en parte). Si la carga de trabajo hospeda un servicio a través de Internet y autentica usuarios en Windows Server AD DS, los costos podrían ser completamente imprevistos. Para ayudar a reducir las consultas del catálogo global fuera del sitio de nube durante la autenticación, puede [Habilitar la caché de pertenencia a un grupo Universal](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Método de instalación

Debe elegir cómo instalar los controladores de dominio en la red virtual:

- Promover nuevos controladores de dominio. Para obtener más información, consulte [instalar un nuevo bosque de Active Directory en una red virtual Azure](active-directory-new-forest-virtual-machine.md).

- Mover el disco duro virtual de un controlador de dominio virtual local en la nube. En este caso, debe asegurarse de que el controlador de dominio local virtual se "mover," no "copiado" o "duplicado".

Use máquinas virtuales de Windows Azure solo para los controladores de dominio (en lugar de Azure "web" o "trabajo" rol máquinas virtuales). Son resistentes y duración del estado de un controlador de dominio es necesario. Máquinas virtuales de Windows Azure están diseñados para cargas de trabajo como controladores de dominio.

No use SYSPREP para implementar o clonar DC. La capacidad para clonar DC está solo disponible a partir de Windows Server 2012. La característica clonación necesita compatibilidad con VMGenerationID en el hipervisor subyacente. Hyper-V en Windows Server 2012 y Azure redes virtuales ambos admiten VMGenerationID, así como los proveedores de software de virtualización de aplicaciones de terceros.

### <a name="BKMK_PlaceDB"></a>Ubicación de la base de datos de Windows Server AD DS y SYSVOL

Seleccione dónde desea ubicar la base de datos de Windows Server AD DS, los registros y SYSVOL. Se debe implementar en discos de datos de Azure.

> [AZURE.NOTE] Discos de datos de Azure están limitados a 1 TB.

Unidades de disco de datos hace no caché escribe de forma predeterminada. Unidades de disco de datos que se han adjuntado a una máquina virtual use escritura mediante almacenamiento en caché. Escritura a través de almacenamiento en caché hace que la escritura se compromete a almacenamiento de Azure resistente antes de la transacción se complete desde la perspectiva del sistema operativo de la máquina virtual. Proporciona duración a costa escribe algo más lento.

Esto es importante para Windows Server AD DS porque caché de disco de escritura en segundo plano invalida suposiciones realizadas por el controlador de dominio. Windows Server AD DS intenta deshabilitar almacenamiento en caché de escritura pero depende del sistema IO para tenerlo en disco. Error al deshabilitar la caché de escritura puede, en determinadas circunstancias, introducir deshacer USN de persistentes objetos y otros problemas.

Como práctica recomendada para virtual DC, haga lo siguiente:

- Establecer la configuración de preferencia de caché de Host en el disco de datos de Azure para ninguno. Esto evita problemas con la caché de escritura para operaciones de AD DS.

- Almacenar la base de datos, los registros y SYSVOL en el uno mismo disco de datos o los discos de datos independiente. Normalmente, se trata de un disco independiente desde el disco utilizado para el propio sistema operativo. El punto clave es que la base de datos de Windows Server AD DS y SYSVOL no deben almacenarse en un tipo de disco del sistema operativo de Azure. De forma predeterminada, el proceso de instalación de AD DS instala estos componentes en la carpeta de % raíz %, que no se recomienda para Azure.

### <a name="BKMK_BUR"></a>Copia de seguridad y restauración

Tenga en cuenta qué es y no es compatible con la copia de seguridad y restauración de un controlador de dominio en general y, específicamente, aquellos que se ejecutan en una máquina virtual. Consulte [copia de seguridad y restauración consideraciones para DC virtualizado](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Crear copias de seguridad de estado de sistema mediante solo una copia de seguridad software que específicamente de requisitos de copia de seguridad de Windows Server AD DS, como copia de seguridad de Windows Server.

No copiar o clonar archivos de disco duro virtual de controladores de dominio en lugar de realizar copias de seguridad periódicas. Una restauración nunca deben, hacerlo usando clonar o copiada VHD sin Windows Server 2012 y un hipervisor compatible presentará USN burbujas.

### <a name="BKMK_FedSrvConfig"></a>Configuración del servidor de federación

La configuración de servidores de federación de Windows Server AD FS (STSs) en la parte depende de si las aplicaciones que se va a implementar en Azure necesitan acceso a los recursos en su red local.

Si las aplicaciones que cumplen los criterios siguientes, puede implementar las aplicaciones de aislamiento de su red local.

- Aceptar los tokens de seguridad SAML

- Están exposable a Internet

- No acceda a los recursos locales

En este caso, configure Windows Server AD FS STSs como sigue:

1. Configurar un bosque de dominio único aislado en Azure.

2. Proporcionar acceso federado al bosque configurando una granja de servidores de federación ADFS de Windows Server.

3. Configurar Windows Server AD FS (granja de servidores de federación y conjunto de proxy de servidor de federación) del bosque local.

4. Establecer una relación de confianza de federación entre la local y Azure instancias de AD FS de Windows Server.

Por otro lado, si las aplicaciones requieren acceso a los recursos locales, puede configurar Windows Server AD FS con la aplicación en Azure como sigue:

1. Configurar la conectividad entre redes local y Azure.

2. Configurar una granja de servidores de federación de Windows Server AD FS del bosque local.

3. Configurar un conjunto de proxy de servidores de federación ADFS de Windows Server en Azure.

Esta configuración tiene la ventaja de reducir la exposición de recursos locales, similares a la configuración de Windows Server AD FS con aplicaciones en una red perimetral.

Tenga en cuenta que en cualquier caso, puede establecer relaciones de confianzas con más de los proveedores de identidades, si es necesario colaboración para empresas.

### <a name="BKMK_CloudSvcConfig"></a>Configuración de servicios de nube

Servicios de nube son necesarios si desea exponer una máquina virtual directamente a Internet o exponer una aplicación de equilibrio de carga a través de Internet. Esto es posible porque cada servicio de nube ofrece una sola configurable dirección IP virtual.

### <a name="BKMK_FedReqVIPDIP"></a>Requisitos de servidor de federación para IP pública y privada direccionamiento (IP dinámica frente IP virtual)

Cada máquina virtual Azure recibe una dirección IP dinámica. Una dirección IP dinámica es una dirección privada accesible sólo en Azure. En la mayoría de los casos, sin embargo, será necesario configurar una dirección IP virtual para las implementaciones de Windows Server AD FS. La dirección IP virtual es necesaria exponer extremos de AD FS de Windows Server a Internet y se usan los clientes y socios federados para la autenticación y la administración continua. Una dirección IP virtual es una propiedad de un servicio de nube que contiene una o más máquinas virtuales Azure. Si la aplicación notificaciones implementada en Azure y Windows Server AD FS es a través de Internet y compartir los puertos comunes, cada uno de ellos requieren una dirección IP virtual de su propia y, por tanto, será necesario crear un servicio de nube para la aplicación y otro para Windows Server AD FS.

Para obtener definiciones de los términos dirección IP virtual y la dirección IP dinámica, vea [términos y definiciones](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Configuración de alta disponibilidad de Windows Server AD FS

Si es posible implementar los servicios de federación de AD FS de Windows Server independiente, se recomienda implementar una granja de servidores con al menos dos nodos de AD FS STS y servidores proxy para entornos de producción.

Consulte [Consideraciones de topología de AD FS 2.0 implementación](https://technet.microsoft.com/library/gg982489) en la [AD FS 2.0 diseño guía](https://technet.microsoft.com/library/dd807036) para decidir qué opciones de configuración de implementación mejores satisfacer necesidades específicas.

> [AZURE.NOTE] Para obtener el equilibrio de carga para extremos de Windows Server AD FS en Azure, configurar a todos los miembros de la granja de servidores de Windows Server AD FS en el mismo servicio de nube y use la capacidad de equilibrio de carga de Azure para HTTP (de forma predeterminada, 80) y puertos HTTPS (el valor predeterminado es 443). Para obtener más información, vea el [sondeo de Azure equilibrador de carga](https://msdn.microsoft.com/library/azure/jj151530).
Equilibrio de carga de red (NLB) Windows Server no se admite en Azure.
