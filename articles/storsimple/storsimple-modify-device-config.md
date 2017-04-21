<properties 
   pageTitle="Modificar la configuración de dispositivo StorSimple | Microsoft Azure" 
   description="Describe cómo utilizar el servicio Administrador de StorSimple volver a configurar un dispositivo de StorSimple que ya se ha implementado." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="09/29/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>Usar el servicio de administrador de StorSimple para modificar la configuración del dispositivo StorSimple

## <a name="overview"></a>Información general 

El portal clásico Azure **Configurar** página contiene todos los parámetros de dispositivo que puede cambiar la configuración en un dispositivo StorSimple administrado por un servicio de administrador de StorSimple. En este tutorial se explica cómo puede usar la página **Configurar** para realizar las siguientes tareas de nivel de dispositivo:

- Modificar la configuración de dispositivo 
- Modificar la configuración de hora 
- Modificar la configuración de DNS 
- Modificar interfaces de red
- Intercambiar o reasignar direcciones IP

## <a name="modify-device-settings"></a>Modificar la configuración de dispositivo

La configuración de dispositivo incluye el nombre descriptivo del dispositivo y la descripción del dispositivo.

Un dispositivo de StorSimple que está conectado al servicio de administrador de StorSimple se asigna un nombre predeterminado. El nombre predeterminado refleja normalmente el número de serie del dispositivo. Por ejemplo, un nombre de dispositivo predeterminado que es de 15 caracteres, como 8600-SHX0991003G44HT indica lo siguiente:

- **8600** -indica el modelo de dispositivo.
- **SHX** : indica el sitio de la fábrica.
- **0991003** - indica un producto específico.
- **G44HT**- los últimos 5 dígitos se incrementan para crear números de serie únicos. No puede ser un conjunto secuencial.

Puede usar el portal de clásico Azure para cambiar el nombre del dispositivo y asígnele un nombre descriptivo único de su elección. El nombre descriptivo puede contener caracteres y puede tener un máximo de 64 caracteres.

También puede especificar una descripción del dispositivo. Descripción de un dispositivo normalmente le ayuda a identificar la ubicación física del dispositivo y el propietario. El campo Descripción debe contener menos de 256 caracteres.
 
## <a name="modify-time-settings"></a>Modificar la configuración de hora

El dispositivo debe sincronizar tiempo para autenticar con el proveedor de servicios de almacenamiento de nube. Seleccione la zona horaria de la lista desplegable y especificar hasta dos servidores de protocolo de tiempo de red (NTP). El servidor NTP principal es necesario y se especifica cuándo usar Windows PowerShell para StorSimple para configurar el dispositivo. Puede especificar de Windows Server predeterminada **time.windows.com** como su servidor NTP. Puede ver la configuración del servidor NTP principal a través del portal clásica Azure, pero debe usar la interfaz de Windows PowerShell para cambiarlo.

La configuración del servidor NTP secundaria es opcional. Puede usar el portal clásico para configurar un servidor NTP secundario. 

Al configurar el servidor NTP, asegúrese de que su red permite el tráfico NTP pasar de su centro de datos a Internet. Al especificar un servidor NTP público, debe asegurarse de que los firewalls de red y otros dispositivos de seguridad están configurados para permitir NTP tráfico a y desde la red externa. Si no se permite el tráfico NTP bidireccional, debe usar un servidor NTP interno (esta función proporciona un controlador de dominio de Windows). Si el dispositivo no puede sincronizar la hora, es posible que no pueda comunicarse con el proveedor de almacenamiento de la nube.

Para ver una lista de los servidores NTP públicos, vaya a la [NTP servidores Web](http://support.ntp.org/bin/view/Servers/WebHome). 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>¿Qué sucede si el dispositivo se implementa en una zona horaria diferente?

Si el dispositivo se implementa en una zona horaria diferente, cambiará la zona horaria de dispositivo. Dado que las directivas de copia de seguridad usa la zona horaria de dispositivo, las directivas de copia de seguridad se ajustarán automáticamente conforme a la nueva zona horaria. Intervención del usuario no es necesaria.

## <a name="modify-dns-settings"></a>Modificar la configuración de DNS

Cuando el dispositivo intenta comunicarse con el proveedor de servicios de almacenamiento de nube, se usa un servidor DNS. Para alta disponibilidad, debe configurar principal y los servidores DNS secundarios durante la implementación inicial del dispositivo. Para volver a configurar el servidor DNS principal, debe usar la interfaz de Windows PowerShell en el dispositivo StorSimple.

Para modificar el servidor DNS secundario, puede usar el portal de clásico de Azure.



## <a name="modify-network-interfaces"></a>Modificar interfaces de red

El dispositivo tiene seis interfaces de red de dispositivo, cuatro de las cuales son 1 GbE y dos de las cuales son 10 GbE. Estas interfaces etiquetadas como datos 0: 5 de datos. DATOS 0, 1 de datos, datos 4 y 5 de datos son 1 GbE, mientras que DATA 2 y 3 de datos son interfaces de red de 10 GbE.

Configurar **Opciones de interfaz de red** para cada una de las interfaces para usarse. Para asegurarse de alta disponibilidad, se recomienda tener al menos dos interfaces de iSCSI y dos interfaces de nube habilitada en el dispositivo. Recomendamos, pero no requieren que se ha deshabilitado interfaces sin usar.

Al configurar cualquiera de las interfaces de red, debe configurar una dirección IP virtual (VIP).

DATOS 0 están la nube habilitada de forma predeterminada. Al configurar datos 0, también debe configurar dos direcciones IP fijas, uno para cada controlador. Estas direcciones IP fijas pueden usarse para obtener acceso a los controladores de dispositivo directamente y son útiles para instalar actualizaciones en el dispositivo o cuando se tiene acceso a los controladores para solucionar problemas.

En StorSimple 8000 Series Update 1, se establece la métrica de enrutamiento de datos de 0 a menor; por lo tanto, si el dispositivo está ejecutando StorSimple 8000 Series actualización 1, se enrutar el tráfico de nube mediante datos 0. Si tiene más de una interfaz de red habilitados para la nube en su dispositivo StorSimple, tome nota de este.

>[AZURE.NOTE] Las direcciones IP fijas para el controlador se usan para atender las actualizaciones en el dispositivo. Por lo tanto, las direcciones IP fija deben ser enrutables y puede conectarse a Internet.

Para cada interfaz de red, se muestran los parámetros siguientes:

- **Velocidad** : no es un parámetro configurable por el usuario. DATOS 0, 1 de datos, datos 4 y 5 de datos son siempre 1 GbE, mientras que DATA 2 y 3 de datos son interfaces de 10 GbE.

     >[AZURE.NOTE] Velocidad y dúplex son siempre automática-negociar. No se admiten las tramas Jumbo.
 
- **Estado de la interfaz** : una interfaz se puede habilitar o deshabilitar. Si está habilitado, el dispositivo intentará utilizar la interfaz. Le recomendamos que aquellas interfaces que están conectados a la red y utiliza esté habilitado. Deshabilitar las interfaces que no está utilizando.

- **Tipo de interfaz** : este parámetro permite aislar el tráfico iSCSI del tráfico de almacenamiento de la nube. Este parámetro puede ser uno de estos procedimientos:

    - **Nube habilitada** , cuando habilitado, el dispositivo usará esta interfaz para comunicarse con la nube.
    - **iSCSI habilitado** : cuando está habilitado, el dispositivo usará esta interfaz para comunicarse con el host de iSCSI.

    Le recomendamos que aislar tráfico iSCSI del tráfico de almacenamiento de la nube. Recuerde que si su host dentro de la misma subred que el dispositivo, no es necesario asignar una puerta de enlace; Sin embargo, si el host está en una subred diferente de su dispositivo, debe asignar una puerta de enlace.

- **Dirección IP** : puede ser IPv4 o IPv6 o ambos. Familias de direcciones IPv4 e IPv6 son compatibles para las interfaces de red del dispositivo. Cuando se utiliza IPv4, especifique una dirección IP de 32 bits (*xxx.xxx.xxx.xxx*) en notación de punto decimal. Al utilizar IPv6, simplemente proporcionar un prefijo de 4 dígitos y una dirección de 128 bits se generará automáticamente para la interfaz de red del dispositivo basada en dicho prefijo.

- **Subred** : Esto hace referencia a la máscara de subred y se configura mediante la interfaz de Windows PowerShell.

- **Puerta de enlace** : se trata de la puerta de enlace predeterminada que se debe utilizar esta interfaz cuando intenta comunicarse con nodos que no están en el mismo espacio de direcciones IP (subred). La puerta de enlace debe estar en el mismo espacio de dirección (subred) que la interfaz de dirección IP, según lo determinado por la máscara de subred.

- **Dirección IP fijo** : este campo está disponible sólo mientras configura los datos 0 interfaz. Para operaciones como las actualizaciones o solución de problemas del dispositivo, debe conectar directamente con el controlador de dispositivo. La dirección IP fija puede utilizarse para acceder a la activa y el controlador de pasivo en el dispositivo.

Volver a configurar controlador 0 y 1 de controlador a través del portal clásico Azure.

>[AZURE.NOTE] 
>
>- Para garantizar el funcionamiento correcto, compruebe la velocidad de la interfaz y dúplex en el conmutador de la interfaz de cada dispositivo está conectada a. Cambiar interfaces bien debe negociar con o esté configurados para Gigabit Ethernet (1000 Mbps) y ser dúplex completo. Interfaces operativo a velocidades menores o en medio dúplex provocará problemas de rendimiento.
>
>- Para minimizar las interrupciones y tiempo de inactividad, le recomendamos que habilite portfast en cada uno de los puertos del conmutador que se conectan a la interfaz de red iSCSI del dispositivo. Esto se asegurará de que la conectividad de red se puede establecer rápidamente si se produce un error.
 
## <a name="swap-or-reassign-ips"></a>Intercambiar o reasignar direcciones IP

Actualmente, si cualquier interfaz de red en el controlador se asigna a una VIP que está en uso (mediante el mismo dispositivo u otro dispositivo en la red), el controlador provocarán sobre. Por lo tanto, se deben seguir el procedimiento adecuado si se intercambiar a VIP para la interfaz de red del dispositivo, porque se creará una situación IP duplicada.

Siga estos pasos para intercambiar o reasignar a la VIP para cualquiera de las interfaces de red:

#### <a name="to-reassign-ips"></a>Para reasignar direcciones IP

1. Desactive la dirección IP de ambas interfaces.

2. Después de que están desactivadas las direcciones IP, asignar las nuevas direcciones IP a las interfaces respectivas.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [Configurar MPIO para su dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).

- Obtenga información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).
     
