<properties
   pageTitle="Requisitos del sistema StorSimple | Microsoft Azure"
   description="Describe los procedimientos recomendados para una solución de Microsoft Azure StorSimple, redes y los requisitos de alta disponibilidad y software."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/31/2016"
   ms.author="alkohli"/>

# <a name="storsimple-software-high-availability-and-networking-requirements"></a>StorSimple software, alta disponibilidad y requisitos de red

## <a name="overview"></a>Información general

Bienvenido a Microsoft Azure StorSimple. Este artículo describe los requisitos del sistema importantes y procedimientos recomendados para su dispositivo StorSimple y para los clientes de almacenamiento acceso al dispositivo. Se recomienda revisar la información detenidamente antes de implementar el sistema de StorSimple y, a continuación, consulte volver a él según sea necesario durante la implementación y las operaciones subsiguientes.

Se incluyen los requisitos del sistema:

- **Requisitos de software para clientes de almacenamiento** - describe los sistemas operativos compatibles y los requisitos adicionales para los sistemas operativos.
- **Requisitos para el dispositivo StorSimple a redes** : proporciona información sobre los puertos que deben estar abiertos en el firewall para permitir el tráfico de administración, nube o iSCSI.
- **Requisitos de alta disponibilidad para StorSimple** - describe los procedimientos recomendados para su StorSimple dispositivo y host del equipo y los requisitos de alta disponibilidad. 


## <a name="software-requirements-for-storage-clients"></a>Requisitos de software para clientes de almacenamiento

Son los siguientes requisitos de software para los clientes de almacenamiento que tienen acceso a su dispositivo StorSimple.

| Sistemas operativos compatibles | Versión requerida | Requisitos adicionales y notas |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008R2 SP1, 2012, 2012R2 |StorSimple iSCSI volúmenes son compatibles para su uso en sólo los siguientes tipos de disco de Windows:<ul><li>Volumen simple en disco básico</li><li>Volumen simple y reflejado en disco dinámico</li></ul>Windows Server 2012 fina aprovisionamiento y características ODX son compatibles si está utilizando un volumen de iSCSI StorSimple.<br><br>Puede crear StorSimple thin preparación y totalmente aprovisionado volúmenes. No puede crear volúmenes aprovisionados parcialmente.<br><br>Volver a formatear un volumen thin aprovisiona puede tardar mucho tiempo. Se recomienda eliminar el volumen y, a continuación, crear una nueva en lugar de volver a dar formato. Sin embargo, si todavía prefiere volver a formatear un volumen:<ul><li>Ejecute el siguiente comando antes el nuevo formato a evitar retrasos de Reclamación de espacio: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Una vez completado el formato, use el comando siguiente para volver a habilitar la recuperación de espacio:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Aplicar la revisión de Windows Server 2012 como se describe en [KB 2878635](https://support.microsoft.com/kb/2870270) a su equipo con Windows Server.</li></ul></li></ul></ul> Si está configurando Administrador de instantáneas StorSimple o StorSimple adaptador de SharePoint, vaya a [requisitos de Software para componentes opcionales](#software-requirements-for-optional-components).|
| VMWare ESX | 5.5 y 6.0 | Compatible con VMWare vSphere como cliente de iSCSI. Característica de bloque de VAAI es compatible con VMware vSphere en dispositivos StorSimple.
| Linux RHEL/CentOS | 5, 6 y 7 | Compatibilidad con clientes de Linux iSCSI con versiones de iniciador iSCSI abierto 5, 6 y 7. |
| Linux | SUSE Linux 11 | |
 > [AZURE.NOTE] IBM AIX no es compatible actualmente con StorSimple.

## <a name="software-requirements-for-optional-components"></a>Requisitos de software para componentes opcionales

Son los siguientes requisitos de software para los componentes StorSimple opcionales (Administrador de instantáneas de StorSimple y StorSimple adaptador para SharePoint).

| Componente | Plataforma de host | Requisitos adicionales y notas |
| --------------------------- | ---------------- | ------------- |
| Administrador de instantáneas StorSimple | Windows Server 2008R2 SP1, 2012, 2012R2 | Uso del Administrador de instantáneas de StorSimple en Windows Server es necesaria para copia de seguridad y restauración de discos dinámicos reflejados y para las copias de seguridad coherentes para la aplicación.<br> Administrador de instantáneas StorSimple sólo se admite en Windows Server 2008 R2 SP1 (64 bits), Windows 2012 R2 y Windows Server 2012.<ul><li>Si está utilizando la ventana Server 2012, debe instalar .NET 3.5 – 4.5 antes de instalar el Administrador de instantáneas StorSimple.</li><li>Si usa Windows Server 2008 R2 SP1, debe instalar Windows Management Framework 3.0 antes de instalar el Administrador de instantáneas StorSimple.</li></ul> |
| Adaptador StorSimple para SharePoint | Windows Server 2008R2 SP1, 2012, 2012R2 |<ul><li>Adaptador StorSimple de SharePoint solo es compatible con SharePoint 2010 y SharePoint 2013.</li><li>EDR requiere SQL Server Enterprise Edition, versión 2008 R2 o 2012.</li></ul>|

## <a name="networking-requirements-for-your-storsimple-device"></a>Requisitos de red de su dispositivo StorSimple

El dispositivo de StorSimple es un dispositivo bloqueado. Sin embargo, puertos necesitan abrirse en el firewall para permitir el tráfico de administración, nube y iSCSI. La siguiente tabla enumeran los puertos que deben estar abiertos en el firewall. En esta tabla, *en* o *entrante* se refiere a la dirección desde la que las solicitudes entrantes de cliente, tener acceso a su dispositivo. *Out* o *saliente* hace referencia a la dirección en que el dispositivo StorSimple envía datos externamente, más allá de la implementación: por ejemplo, de salida a Internet.

| Nº de puerto<sup>1,2</sup> | Entrada o de salida | Ámbito de puerto | Obligatorio | Notas |
|------------------------|-----------|------------|----------|-------|
|TCP 80 (HTTP)<sup>3</sup>|  Hacia afuera |  WAN | No |<ul><li>Puerto de salida se usa para el acceso a Internet para recuperar las actualizaciones.</li><li>El proxy web saliente es configurable por el usuario.</li><li>Para permitir que las actualizaciones del sistema, este puerto también debe estar abierto para el controlador fijo direcciones IP.</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| Hacia afuera | WAN | Sí |<ul><li>Puerto de salida se usa para obtener acceso a datos en la nube.</li><li>El proxy web saliente es configurable por el usuario.</li><li>Para permitir que las actualizaciones del sistema, este puerto también debe estar abierto para el controlador fijo direcciones IP.</li><li>Este puerto también se utiliza en los controladores de ambos recolección.</li></ul>|
|UDP 53 (DNS) | Hacia afuera | WAN | En algunos casos; Consulte las notas. |Este puerto es necesario únicamente si usa un servidor DNS basado en Internet. |
| UDP 123 (NTP) | Hacia afuera | WAN | En algunos casos; Consulte las notas. |Este puerto es necesario únicamente si usa un servidor NTP basados en Internet. |
| TCP 9354 | Hacia afuera | WAN | Sí |El puerto de salida se usa el dispositivo StorSimple para comunicarse con el servicio Administrador de StorSimple. |
| 3260 (iSCSI) | En | LAN | No | Este puerto se usa para tener acceso a datos sobre iSCSI.|
| 5985 | En | LAN | No | Puerto de entrada se usa por el Administrador de StorSimple instantánea para comunicarse con el dispositivo de StorSimple.<br>Este puerto también se usa cuando remotamente conectar a Windows PowerShell para StorSimple sobre HTTP. |
| 5986 | En | LAN | No | Este puerto se usa cuando remotamente conectar a Windows PowerShell para StorSimple sobre HTTPS. |

<sup>1</sup> hay puertos entrantes deben estar abiertos en Internet.

<sup>2</sup> si varios puertos llevar a cabo una configuración de puerta de enlace, el orden de tráfico saliente de enrutado vendrá determinado basándose en el orden de enrutamiento de puerto que se describe en el [enrutamiento de puerto](#routing-metric), a continuación.

<sup>3</sup> el controlador fijado direcciones IP en el dispositivo StorSimple debe ser enrutables y puede conectarse a Internet. Las direcciones IP fijas se usan para atender las actualizaciones en el dispositivo. Si los controladores del dispositivo no pueden conectarse a Internet a través de las direcciones IP fija, no podrá actualizar el dispositivo StorSimple.

> [AZURE.IMPORTANT] Asegúrese de que el firewall no modificar o descifrar cualquier tráfico SSL entre el dispositivo de StorSimple y Azure.

### <a name="url-patterns-for-firewall-rules"></a>Modelos de dirección URL para las reglas de firewall

Los administradores de red a menudo pueden configurar reglas de firewall avanzado basadas en modelos de dirección URL para filtrar la entrada y el tráfico saliente. El dispositivo de StorSimple y el servicio Administrador de StorSimple dependen de otras aplicaciones de Microsoft como Bus de servicio de Azure, Control de acceso de Azure Active Directory, cuentas de almacenamiento y servidores de Microsoft Update. Los modelos de dirección URL asociados con estas aplicaciones pueden utilizarse para configurar las reglas de firewall. Es importante entender que pueden cambiar los patrones de URL asociados con estas aplicaciones. A su vez se necesita el Administrador de red supervisar y actualizar reglas de firewall para su StorSimple como y cuando sea necesario.

Le recomendamos que configure las reglas de firewall para el tráfico saliente, en función de StorSimple fijado direcciones IP, libremente en la mayoría de los casos. Sin embargo, puede usar la información siguiente para establecer reglas de firewall avanzado que son necesarios para crear entornos seguros.

> [AZURE.NOTE] El dispositivo (de origen) direcciones IP siempre debe establecerse a todas las interfaces de red habilitado. El destino que se debe establecer direcciones IP en [intervalos de IP del centro de datos de Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).

#### <a name="url-patterns-for-azure-portal"></a>Modelos de dirección URL para el portal de Azure
| Modelo de dirección URL                                                      | Funcionalidad del componente                                           | Direcciones IP de dispositivo                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | Servicio de administrador de StorSimple<br>Servicio de Control de acceso<br>Bus de servicio de Azure| Interfaces de red habilitados para la nube        |
|`https://*.backup.windowsazure.com`|Registro de dispositivo| DATOS 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Revocación de certificados |Interfaces de red habilitados para la nube |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Cuentas de almacenamiento de Azure y supervisión | Interfaces de red habilitados para la nube        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Servidores de Microsoft Update<br>                             | Controlador fijo direcciones IP solo               |
| `http://*.deploy.akamaitechnologies.com`                         |CDN de Akamai |Controlador fijo direcciones IP solo   |
| `https://*.partners.extranet.microsoft.com/*`                    | Paquete de compatibilidad                                                  | Interfaces de red habilitados para la nube        |

#### <a name="url-patterns-for-azure-government-portal"></a>Modelos de dirección URL para el portal de administración pública de Azure
| Modelo de dirección URL                                                      | Funcionalidad del componente                                           | Direcciones IP de dispositivo                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`   | Servicio de administrador de StorSimple<br>Servicio de Control de acceso<br>Bus de servicio de Azure| Interfaces de red habilitados para la nube        |
|`https://*.backup.windowsazure.us`|Registro de dispositivo| DATOS 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Revocación de certificados |Interfaces de red habilitados para la nube |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Cuentas de almacenamiento de Azure y supervisión | Interfaces de red habilitados para la nube        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Servidores de Microsoft Update<br>                             | Controlador fijo direcciones IP solo               |
| `http://*.deploy.akamaitechnologies.com`                         |CDN de Akamai |Controlador fijo direcciones IP solo   |
| `https://*.partners.extranet.microsoft.com/*`                    | Paquete de compatibilidad                                                  | Interfaces de red habilitados para la nube        |

### <a name="routing-metric"></a>Métrica de enrutamiento

Métrica de enrutamiento está asociada a las interfaces y la puerta de enlace para enrutar los datos a las redes especificadas. Métrica de enrutamiento se usa el protocolo de enrutamiento para calcular la mejor ruta de acceso a un destino determinado, si aprende que existen varias rutas al mismo destino. La parte inferior la métrica de enrutamiento, mayor será la preferencia.

En el contexto de StorSimple, si se han configurado varias puertas de enlace y las interfaces de red para el tráfico de canal, la métrica de enrutamiento deben tener en cuenta para determinar el orden relativo en que obtener utilizará las interfaces. No se puede cambiar la métrica de enrutamiento por el usuario. Sin embargo, puede usar el `Get-HcsRoutingTable` cmdlet para imprimir la tabla de enrutamiento (y métricas) en el dispositivo StorSimple. Más información sobre el cmdlet Get-HcsRoutingTable de [implementación de solución de problemas StorSimple](storsimple-troubleshoot-deployment.md).

Los algoritmos métricos enrutamiento son diferentes según la versión de software que se ejecuta en el dispositivo StorSimple.

**Versiones anteriores al 1 de actualización**

Esto incluye versiones de software antes del 1 de actualización como el administrador global, 0,1, versión 0,2 o 0,3. El orden según métrica de enrutamiento es la siguiente:

   *Última configurado la interfaz de red de 10 GbE > interfaz de red de otros GbE 10 > última configurado 1 interfaz de red GbE > interfaz de red de otros GbE 1*


**Versiones a partir de 1 de actualización y antes de la actualización 2**

Esto incluye versiones de software como 1, 1.1 o 1.2. El orden según métrica de enrutamiento se decide como sigue:

   *DATOS 0 > última configurado la interfaz de red de 10 GbE > interfaz de red de otros GbE 10 > última configurado 1 interfaz de red GbE > interfaz de red de otros GbE 1*

   Actualización de 1, se realiza la métrica de enrutamiento de datos 0 más bajos; por lo tanto, todo el tráfico nube se enruta a través de datos 0. Si hay más de una interfaz de red habilitados para la nube en su dispositivo StorSimple, tome nota de este.


**Versiones a partir de la actualización 2**

Actualización 2 tiene varias mejoras de redes y la métrica de enrutamiento ha cambiado. El comportamiento puede explicación de la manera siguiente.

- Un conjunto de valores predeterminados se han asignado a interfaces de red.   

- Considere la posibilidad de una tabla de ejemplo que se muestra a continuación con valores asignados a las distintas interfaces de red cuando se hayan nube-habilitado o deshabilitado en la nube, pero con una puerta de enlace configurada. Tenga en cuenta los valores asignados aquí son solo los valores de ejemplo.


  	| Interfaz de red | Nube habilitada | Nube deshabilitado con la puerta de enlace |
  	|-----|---------------|---------------------------|
  	| Datos 0  | 1            | -                        |
  	| Datos 1  | 2            | 20                       |
  	| Datos 2  | 3            | 30                       |
  	| Datos 3  | 4            | 40                       |
  	| Datos 4  | 5            | 50                       |
  	| Datos 5  | 6            | 60                       |


- Es el orden en que se enrutar el tráfico de la nube mediante las interfaces de red:

    *Datos 0 > datos 1 > fecha 2 > datos 3 > datos 4 > datos 5*

    Esto puede se explica en el ejemplo siguiente.

    Considere la posibilidad de un dispositivo StorSimple con dos interfaces de red habilitados para la nube, datos 0 y 5 de datos. Datos de 1 a 4 de datos están deshabilitados nube pero tienen una puerta de enlace configurada. Será el orden en que se enrutar el tráfico para este dispositivo:

    *Datos 0 (1) > 5 (6) de datos > datos 1 (20) > datos 2 (30) > datos 3 (40) > datos 4 (50)*

    *donde los números entre paréntesis indican la métrica de enrutamiento respectiva.*

    Si se produce un error de datos 0, el tráfico de nube se dirijan a 5 de datos. Dado que una puerta de enlace está configurado en otros de red, si se tratase de datos 0 y 5 de datos no se realice correctamente, el tráfico de nube pasará a través de datos 1.


- Si se produce un error en una interfaz de red habilitados para la nube, a continuación, son 3 reintentos con un retraso de la segunda 30 para conectarse a la interfaz. Si se produce un error en todos los reintentos, el tráfico se dirige a la siguiente disponible nube habilitada interfaz según la tabla de enrutamiento. Si la red nube habilitada interfaces éxito, el dispositivo se conmutar a otro controlador (sin reiniciar en este caso).

- Si hay un error VIP para una interfaz de red habilitado para iSCSI, habrá 3 reintentos con un retraso de 2 segundos. Este comportamiento ha permaneció desde las versiones anteriores. Si se produce un error en todas las interfaces de red iSCSI, se producirá un error de controlador (acompañados reiniciar el equipo).


- También se generará una alerta en el dispositivo StorSimple cuando hay un error VIP. Para obtener más información, vaya a la [alerta de referencia rápida](storsimple-manage-alerts.md).

- En términos de reintentos, iSCSI tienen prioridad sobre la nube.

    Considere el siguiente ejemplo: StorSimple un dispositivo tiene dos interfaces de red habilitadas, datos 0 y 1 de datos. Datos 0 están habilitada nube, mientras que Data 1 es ambos nube y habilitado iSCSI. Otras interfaces de red en este dispositivo están habilitados para la nube o iSCSI.

    Si falla datos 1, dado es la interfaz de red iSCSI última, esto dará como resultado un error de controlador a 1 de datos en el otro controlador.


### <a name="networking-best-practices"></a>Prácticas recomendadas de redes

Además de los requisitos de red anteriores, para un rendimiento óptimo de la solución StorSimple, tenga en cuenta las siguientes prácticas recomendadas:

- Asegúrese de que el dispositivo StorSimple tiene un ancho de banda Mbps 40 dedicado (o más) disponible en todo momento. No se debe compartir este ancho de banda (o debe garantiza la asignación mediante el uso de las directivas de QoS) con otras aplicaciones.

- Asegúrese de que la conectividad de red a Internet está disponible en todo momento. Conexiones a Internet se o poco confiables a los dispositivos, no incluidos conexión a Internet alguna, se producen una configuración no compatible.

- Aislar el tráfico iSCSI y nube dedicados interfaces de red en el dispositivo para el acceso iSCSI y la nube. Para obtener más información, consulte cómo [modificar interfaces de red](storsimple-modify-device-config.md#modify-network-interfaces) en el dispositivo StorSimple.

- No use una configuración de protocolo de Control de agregación de vínculos (LACP) para las interfaces de red. Se trata de una configuración no compatible.


## <a name="high-availability-requirements-for-storsimple"></a>Requisitos de alta disponibilidad para StorSimple

La plataforma de hardware que se incluye con la solución de StorSimple tiene características de disponibilidad y confiabilidad que proporcionan una base para una infraestructura de almacenamiento altamente disponible, tolerancia a errores en el centro de datos. Sin embargo, hay requisitos y los procedimientos recomendados que deben cumplir para ayudar a asegurar la disponibilidad de la solución StorSimple. Antes de implementar StorSimple, atentamente los siguientes requisitos y los procedimientos recomendados para el dispositivo StorSimple y equipos host conectados.

Para obtener más información sobre la supervisión y el mantenimiento de los componentes de hardware de su dispositivo StorSimple, vaya a [usar el servicio de administrador de StorSimple para supervisar el estado y componentes de hardware](storsimple-monitor-hardware-status.md) y [reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Requisitos de alta disponibilidad y procedimientos de su dispositivo StorSimple

Revise la información siguiente con cuidado para garantizar la alta disponibilidad del dispositivo StorSimple.

#### <a name="pcms"></a>PCMs

Dispositivos de StorSimple incluyen alimentación redundantes intercambiables y refrigeración módulos (PCMs). Cada PCM tiene suficiente capacidad para proporcionar servicio para los bastidores todo. Para asegurarse de alta disponibilidad, ambos PCMs deben estar instalados.

- Conecte su PCMs a diferentes fuentes de alimentación para proporcionar disponibilidad si se produce un error en una fuente de alimentación.
- Si se produce un error en un PCM, Solicitar reemplazo inmediatamente.
- Quitar un error PCM solo cuando tiene la sustitución y esté listo para instalarlo.
- No se elimina tanto PCMs simultáneamente. El módulo PCM incluye el módulo de batería de copia de seguridad. Quitar de la PCMs se traducirán en un apagado sin la protección de la batería y el estado del dispositivo no se guardarán. Para obtener más información acerca de la batería, vaya a [mantener el módulo de batería de copia de seguridad](storsimple-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Módulos de controlador

Dispositivos de StorSimple incluyen módulos controlador redundantes, intercambiables. Trabajar con los módulos del controlador en modo activo/pasivo. En cualquier momento, un módulo de controlador está activo y proporciona servicio, mientras que el módulo del controlador es pasivo. El módulo de controlador pasivo está encendido y en funcionamiento si el módulo de controlador active falla o se elimina. Cada módulo de controlador tiene suficiente capacidad para proporcionar servicio para los bastidores todo. Ambos módulos controlador deben estar instalados para garantizar alta disponibilidad.

- Asegúrese de que ambos módulos controlador están instalados en todo momento.

- Si se produce un error en un módulo de controlador, Solicitar reemplazo inmediatamente.

- Quitar un módulo de controlador error cuando se tiene la sustitución y esté listo para instalarlo. Quitar un módulo de períodos ampliados afectará el flujo de aire y por tanto la refrigeración del sistema.

- Asegúrese de que las conexiones de red para ambos módulos controlador son idénticas, y las interfaces de red conectada tienen una configuración de red idénticos.

- Si un módulo de controlador se produce un error o necesita reemplazo, asegúrese de que el módulo del controlador se encuentra en un estado activo antes de reemplazar el módulo de controlador errores. Para comprobar que un controlador está activo, vaya a [identificar el controlador activo en el dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- No quite ambos módulos controlador al mismo tiempo. Si un error del controlador está en curso, no cierre el módulo de controlador de reserva ni quitar del chasis.

- Después de la migración de controlador, espere al menos cinco minutos antes de quitar cualquier módulo del controlador.

#### <a name="network-interfaces"></a>Interfaces de red

StorSimple dispositivo controlador módulos cada tienen cuatro 1 Gigabit y dos 10 interfaces de red Gigabit Ethernet.

- Asegúrese de que las conexiones de red para ambos módulos controlador son idénticas y las interfaces de la red que están conectadas las interfaces de módulo controlador para tener una configuración de red idénticos.

- Cuando sea posible, implementar conexiones de red entre diferentes modificadores para garantizar la disponibilidad de servicio en caso de un error de dispositivo de red.

- Cuando desconectar el único o la interfaz de habilitado iSCSI restante última (con direcciones IP asignada), deshabilitar la interfaz en primer lugar y, a continuación, desconecte los cables. Si la interfaz se desconecta en primer lugar, hará que el controlador de active conmutar en el controlador de pasivo. Si el controlador pasivo también tiene sus correspondientes interfaces desconectados, tanto los controladores se reinicia varias veces antes de establecerse en un controlador.

- Conectarse al menos dos interfaces de datos a la red de cada módulo de controlador.

- Si ha activado las dos interfaces de 10 GbE, implementar las existentes entre distintos modificadores.

- Cuando sea posible, use MPIO en servidores para asegurarse de que los servidores pueden tolerar un vínculo, la red o el error de la interfaz.

Para obtener más información acerca de las redes el dispositivo para alta disponibilidad y rendimiento, vaya a [instalar el dispositivo 8100 StorSimple](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) o [el dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSDs y unidades de disco duro

Dispositivos de StorSimple incluyen discos de estado sólido (SSD) y unidades de disco duro (disco duro) que están protegidas mediante refleja los espacios. Uso de espacios reflejados garantiza que el dispositivo está puede tolerar el error de SSDs o unidades de disco duro.

- Asegúrese de que están instalados todos los módulos SSD y el disco duro.

- Si se produce un error en una unidad de disco duro o un SSD, Solicitar reemplazo inmediatamente.

- Si un SSD o una unidad de disco duro falla o requiere el reemplazo, asegúrese de que lo quite solo SSD o la unidad de disco duro que requiere la sustitución.

- No quite uno o más SSD o unidad de disco duro del sistema en cualquier punto en el tiempo.
Un error de 2 o más discos de tipo determinado (unidad de disco duro, SSD) o errores consecutivos dentro de un período de tiempo corto pueden traducirse sistema un funcionamiento incorrecto y posibles pérdidas de datos. Si es así, [póngase en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para obtener ayuda.

- Durante el reemplazo, supervisar el **Estado de Hardware** en la página de **Mantenimiento** de las unidades de la SSDs y unidades de disco duro. El estado de marca de verificación verde indica que los discos son correcto o Aceptar, mientras que seleccione un signo de exclamación rojo indica un error de SSD o unidad de disco duro.

- Le recomendamos que configure instantáneas de nube para todos los volúmenes que necesita proteger en el caso de un error del sistema.

#### <a name="ebod-enclosure"></a>Caracteres enmarcados EBOD

Modelo de dispositivo StorSimple 8600 incluye enmarcado extendido un montón de discos (EBOD) además de los caracteres enmarcados principal. Un EBOD contiene controladores EBOD y unidades de disco duro (disco duro) que están protegidas mediante refleja los espacios. Uso de espacios reflejados garantiza que el dispositivo está puede tolerar el error de uno o más unidades de disco duro. Los caracteres enmarcados EBOD está conectado al entorno principal a través de los cables de SA redundantes.

- Asegúrese de que ambos módulos de controlador EBOD caracteres enmarcados, tanto cables SA y todas las unidades de disco duro están instaladas en todo momento.

- Si se produce un error en un módulo de controlador EBOD caracteres enmarcados, Solicitar reemplazo inmediatamente.

- Si se produce un error en un módulo de controlador EBOD caracteres enmarcados, asegúrese de que el módulo del controlador está activo antes de reemplazar el módulo que ha fallado. Para comprobar que un controlador está activo, vaya a [identificar el controlador activo en el dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Durante una EBOD controlador módulo de reemplazo, supervisar continuamente el estado del componente en el servicio Administrador de StorSimple accediendo **Mantenimiento** > **estado de Hardware**.

- Si un cable SA falla o requiere reemplazo (Microsoft Support deben participar para realizar dicha determinación), asegúrese de que lo quite el cable SA requiere reemplazo.

- No simultáneamente quitar ambos cables SA del sistema en cualquier punto en el tiempo.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Recomendaciones de alta disponibilidad de los equipos de host

Detenidamente estos procedimientos recomendados para garantizar la alta disponibilidad de hosts conectados a su dispositivo StorSimple.

- Configurar StorSimple con [las configuraciones de clúster de servidor de dos nodos archivo][1]. Quitando puntos únicos de error y la creación de redundancia en el host, toda la solución esté altamente disponible.

- Usar continuamente disponibles (CA) se encuentra disponible con Windows Server 2012 (SMB 3.0) para alta disponibilidad durante la migración tras error de los controladores de almacenamiento. Para obtener información adicional para configurar clústeres de servidor de archivos y recursos compartidos continuamente disponible con Windows Server 2012, consulte este [vídeo de demostración](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Pasos siguientes

- [Obtenga más información sobre los límites de sistema StorSimple](storsimple-limits.md).
- [Obtenga información sobre cómo implementar la solución StorSimple](storsimple-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
