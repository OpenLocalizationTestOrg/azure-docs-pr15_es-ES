<properties
   pageTitle="Configurar MPIO en el host StorSimple Linux | Microsoft Azure"
   description="Configurar MPIO en StorSimple conectado a un host Linux ejecutando CentOS 6.6"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Configurar MPIO en un host de StorSimple ejecutando CentOS

Este artículo explica los pasos necesarios para configurar IO de múltiples rutas (MPIO) en el servidor de host de Centos 6.6. El servidor host está conectado a su dispositivo de Microsoft Azure StorSimple para alta disponibilidad a través de iniciadores iSCSI. Describe detalladamente la detección automática de dispositivos de múltiples rutas y la configuración específica únicamente para volúmenes de StorSimple.

Este procedimiento es aplicable a todos los modelos de dispositivos de la serie 8000 StorSimple.

>[AZURE.NOTE] No se puede utilizar este procedimiento para un dispositivo virtual de StorSimple. Para obtener más información, consulte cómo configurar los servidores de host de su dispositivo virtual.

## <a name="about-multipathing"></a>Acerca de múltiples rutas

La característica de múltiples rutas permite configurar varias rutas de i/OS entre un servidor host y un dispositivo de almacenamiento. Estas rutas de i/OS son conexiones físicas de SAN que pueden incluir cables independientes, modificadores, interfaces de red y controladores. Múltiples rutas agrega las rutas de acceso de i/OS, para configurar un nuevo dispositivo que está asociado con todas las rutas de agregado.

El propósito de múltiples rutas es doble:

- **Alta disponibilidad**: proporciona una ruta alternativa si se produce un error en cualquier elemento de la ruta de i/OS (por ejemplo, un cable, cambiar, interfaz de red o controlador).

- **Equilibrio de carga**: según la configuración de su dispositivo de almacenamiento, puede mejorar el rendimiento mediante la detección de cargas en las rutas de acceso de i/OS y volver a equilibrar las cargas dinámicamente.


### <a name="about-multipathing-components"></a>Acerca de los componentes de múltiples rutas

Múltiples rutas en Linux consta de los componentes de núcleo y espacio del usuario como tabular debajo.

- **Núcleo**: el componente principal es el *dispositivo asignador* que redirige i/OS y admite migración tras error de rutas y grupos de la ruta de acceso.

1. **Espacio de usuario**: *Herramientas de múltiples rutas* que administrar dispositivos de stmsboot indicando el módulo de múltiples rutas de dispositivo asignador a qué hacer. Las herramientas constan de:

    - **Múltiples rutas**: muestra y configura dispositivos multipathed.

    - **Multipathd**: daemon que ejecuta múltiples rutas y supervisa las rutas de acceso.

    - **Nombre de Devmap**: proporciona un nombre de dispositivo significativo a udev para devmaps.

    - **Kpartx**: devmaps lineal se asigna a las particiones del dispositivo para realizar varias rutas mapas caso.

    - **Multipath.conf**: archivo de configuración de múltiples rutas daemon que se utiliza para sobrescribir la tabla de configuración integrado.

### <a name="about-the-multipathconf-configuration-file"></a>Acerca del archivo de configuración de multipath.conf

El archivo de configuración `/etc/multipath.conf` realiza muchas de las características de múltiples rutas configurable por el usuario. La `multipath` de comandos y el daemon kernel `multipathd` usar la información contenida en este archivo. El archivo de consulta solo durante la configuración de los dispositivos de múltiples rutas. Asegúrese de que todos los cambios realizados antes de ejecutar el `multipath` comando. Si modifica el archivo posteriormente, debe detener e iniciar multipathd nuevamente para que los cambios surtan efecto.

El multipath.conf tiene cinco secciones:

- **Valores predeterminados de nivel de sistema** *(predeterminado)*: se pueden reemplazar los valores predeterminados de nivel de sistema.

1. **Dispositivos bloqueado** *(lista negra)*: puede especificar la lista de dispositivos que no deben controlar asignador de dispositivo.

1. **Excepciones de la lista negra** *(blacklist_exceptions)*: identificar los dispositivos específicos se traten como dispositivos múltiples rutas incluso si aparece en la lista negra.

1. **Configuración específica del controlador de almacenamiento** *(dispositivos)*: puede especificar opciones de configuración que se aplica a los dispositivos que tienen información de producto y proveedor.

1. **Configuración específica del dispositivo** *(multipaths)*: puede utilizar esta sección para ajustar la configuración de LUN individual.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Configurar múltiples rutas en StorSimple conectado al host Linux

Un dispositivo StorSimple conectado a un host Linux se puede configurar para alta disponibilidad y equilibrio de carga. Por ejemplo, si el host de Linux tiene dos interfaces conectados al SAN y el dispositivo tiene dos interfaces conectados al SAN tal que estas interfaces están en la misma subred, a continuación, habrá 4 rutas disponibles. Sin embargo, si cada interfaz de datos en la interfaz del dispositivo y host está en una subred IP diferente (y no enrutables), a continuación, 2 solo rutas estarán disponibles. Puede configurar múltiples rutas para automáticamente descubrir todas las rutas disponibles, elija un algoritmo de equilibrio de carga para las rutas de acceso, aplicar configuración específica para volúmenes solo StorSimple y, a continuación, habilitar y comprobar múltiples rutas.

El procedimiento siguiente describe cómo configurar múltiples rutas cuando un dispositivo StorSimple con dos interfaces de red está conectado a un host con dos interfaces de red.

## <a name="prerequisites"></a>Requisitos previos

Esta sección describe los requisitos previos de configuración de servidor de CentOS y el dispositivo de StorSimple.

### <a name="on-centos-host"></a>En el host de CentOS



1. Asegúrese de que el host de CentOS tiene 2 interfaces de red habilitado. Tipo:

    `ifconfig`

    En el ejemplo siguiente se muestra el resultado cuando dos interfaces de red (`eth0` y `eth1`) están presentes en el host.

        [root@centosSS ~]# ifconfig
        eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
        inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
        inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
        inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
        UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
        RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
        TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)

        eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
        inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
        inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
        inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
        UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
        RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
        TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)

        loLink encap:Local Loopback  
        inet addr:127.0.0.1  Mask:255.0.0.0
        inet6 addr: ::1/128 Scope:Host
        UP LOOPBACK RUNNING  MTU:65536  Metric:1
        RX packets:12 errors:0 dropped:0 overruns:0 frame:0
        TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:0
        RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)


1. Instalar *Utilidades de iniciador de iSCSI* en el servidor de CentOS. Realice los pasos siguientes para instalar *Utilidades de iniciador de iSCSI*.

    1. Inicie sesión como `root` en el host de CentOS.

    1. Instalar el *iniciador de iSCSI de utilidades*. Tipo:

        `yum install iscsi-initiator-utils`


    1. Después de instalar correctamente el *iniciador de iSCSI de utilidades* , inicie el servicio iSCSI. Tipo:

        `service iscsid start`

        En ocasiones, `iscsid` realmente no se inicia y la `--force` opción puede ser necesario

    1. Para asegurarse de que el iniciador iSCSI está habilitado durante el inicio, utilice la `chkconfig` comando para habilitar el servicio.

        `chkconfig iscsi on`


    1. Para comprobar que se ha instalado por correctamente, ejecute el comando:

        `chkconfig --list | grep iscsi`

        A continuación se muestra un resultado de ejemplo.

            iscsi   0:off   1:off   2:on3:on4:on5:on6:off
            iscsid  0:off   1:off   2:on3:on4:on5:on6:off

        En el ejemplo anterior, puede ver que el entorno iSCSI se ejecutará en tiempo de arranque en ejecución niveles 2, 3, 4 y 5.


1. Instalar *múltiples rutas de asignador de dispositivo*. Tipo:

    `yum install device-mapper-multipath`

    Se iniciará la instalación. Escriba **Y** para continuar cuando se le solicite confirmación.



### <a name="on-storsimple-device"></a>En el dispositivo de StorSimple

Debe tener el dispositivo StorSimple:

- Un mínimo de dos interfaces habilitados para iSCSI. Para comprobar que dos interfaces están habilitados para iSCSI en el dispositivo StorSimple, realice los pasos siguientes en el portal de Azure clásico de su dispositivo StorSimple:

    1. Inicie sesión en el portal de su dispositivo StorSimple clásico.

    1. Seleccione el servicio de administrador de StorSimple, haga clic en **dispositivos** y elija el dispositivo StorSimple específico. Haga clic en **Configurar** y comprobar la configuración de la interfaz de red. A continuación se muestra una captura de pantalla con dos interfaces de red habilitado para iSCSI. Aquí datos 2 y 3 de datos, ambos 10 GbE interfaces están habilitadas para iSCSI.

        ![Configuración de MPIO StorsSimple datos 2](./media/storsimple-configure-mpio-on-linux/IC761347.png)

        ![MPIO StorSimple datos 3 Config](./media/storsimple-configure-mpio-on-linux/IC761348.png)

        En la página **Configurar**

        1. Asegúrese de que las dos interfaces de red están habilitados para iSCSI. El campo **habilitado iSCSI** se debe establecer en **Sí**.
        2. Asegúrese de que las interfaces de red tienen la misma velocidad, debe ser 1 GbE o 10 GbE.
        3. Tenga en cuenta las direcciones IPv4 de las interfaces habilitadas para iSCSI y guardar para su uso posterior en el host.


- Las interfaces de iSCSI en el dispositivo StorSimple deben ser accesibles desde el servidor de CentOS.

    Para comprobarlo, debe proporcionar las direcciones IP de las interfaces de red habilitado para iSCSI StorSimple en su servidor de host. Los comandos usados y el resultado correspondiente con DATA2 (10.126.162.25) y DATA3 (10.126.162.26) se muestra a continuación:

        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target


### <a name="hardware-configuration"></a>Configuración de hardware

Le recomendamos que conecte las dos interfaces de red iSCSI rutas independientes para redundancia de. La figura siguiente muestra la configuración de hardware recomendada para alta disponibilidad y múltiples rutas de equilibrio de carga de su servidor de CentOS y StorSimple dispositivo.

![Configuración de hardware MPIO para StorSimple al host Linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Como se muestra en la figura anterior:

- El dispositivo StorSimple se encuentra en una configuración de activo y pasivo con dos controladores.

- Dos modificadores de SAN están conectados a los controladores del dispositivo.

- Dos iniciadores iSCSI están habilitados en el dispositivo StorSimple.

- Dos interfaces de red están habilitados en el host de CentOS.

La configuración anterior arrojará 4 rutas independientes entre el dispositivo y el host si las interfaces de host y los datos son enrutables.

>[AZURE.IMPORTANT]
>
>- Se recomienda no mezclar 1 GbE y 10 interfaces de red GbE múltiples rutas. Cuando se usa dos interfaces de red, ambos las interfaces deben ser del tipo idénticos.
>- En el dispositivo StorSimple, DATA0, DATA1, DATA4 y DATA5 son interfaces de 1 GbE mientras que DATA2 y DATA3 son interfaces de red de 10 GbE. |

## <a name="configuration-steps"></a>Pasos de configuración

Los pasos de configuración para múltiples rutas implican la configuración de las rutas de acceso disponibles para la detección automática, que especifica el algoritmo de equilibrio de carga para usar, habilitar múltiples rutas y finalmente comprobar la configuración. Cada uno de estos pasos se explica detalladamente en las secciones siguientes.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Paso 1: Configurar múltiples rutas para la detección automática

Los dispositivos compatibles con múltiples rutas pueden detectar y configurar automáticamente.

1. Inicializar `/etc/multipath.conf` archivo. Tipo:

     `Copy mpathconf --enable`

    El comando anterior se creará un `sample/etc/multipath.conf` archivo.


1. Inicie el servicio de varios path. Tipo:

    ``Copy service multipathd start``

    Verá el resultado siguiente:

    `Starting multipathd daemon:`

1. Habilitar la detección automática de multipaths. Tipo:

    `mpathconf --find_multipaths y`

    Esto modificará la sección valores predeterminados de su `multipath.conf` tal como se muestra a continuación:

        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Paso 2: Configurar múltiples rutas para volúmenes StorSimple

De forma predeterminada, todos los dispositivos son negros enumerados en el archivo multipath.conf y se omitirán. Debe crear lista negra excepciones para permitir que múltiples rutas para volúmenes desde dispositivos StorSimple.

1. Editar la `/etc/mulitpath.conf` archivo. Tipo:

    `vi /etc/multipath.conf`

1. Busque la sección blacklist_exceptions del archivo multipath.conf. El dispositivo StorSimple debe figurar como una excepción de lista negra en esta sección. Puede quite líneas correspondientes de este archivo para modificar como se muestra a continuación (usar solo el modelo específico del dispositivo que está utilizando):

        blacklist_exceptions {
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8100*"
            }
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8600*"
            }
           }

### <a name="step-3-configure-round-robin-multipathing"></a>Paso 3: Configurar múltiples rutas de turnos

Este algoritmo de equilibrio de carga, usa todo el multipaths disponibles en el controlador de active de manera equilibrada turnos.

1. Editar la `/etc/multipath.conf` archivo. Tipo:

    `vi /etc/multipath.conf`

1. En la `defaults` sección, establezca la `path_grouping_policy` a `multibus`. La `path_grouping_policy` especifica la ruta de acceso predeterminada aplicar multipaths sin especificar directiva de agrupamiento. La sección de valores predeterminados tendrá un aspecto como se muestra a continuación.

        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }



> [AZURE.NOTE]
> Los valores más comunes de `path_grouping_policy` incluyen:

> - migración tras error = 1 ruta por cada grupo de prioridad
> - multibus = todas las rutas de acceso válidas en grupo de 1 prioridad

### <a name="step-4-enable-multipathing"></a>Paso 4: Habilitar multipathing

1. Reinicie el `multipathd` daemon. Tipo:

    `service multipathd restart`

1. El resultado será tal como se muestra a continuación:

        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]




### <a name="step-5-verify-multipathing"></a>Paso 5: Comprobar múltiples rutas

1. En primer lugar, asegúrese de que iSCSI se establece conexión con el dispositivo StorSimple como sigue:


    1. Descubra el dispositivo StorSimple. Tipo:

        `iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>`

        Resultado de la dirección IP de DATA0 es 10.126.162.25 y puerto 3260 está abierto en el dispositivo StorSimple para el tráfico saliente iSCSI es tal como se muestra a continuación:

            10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
            10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target


        Copiar el IQN del dispositivo StorSimple, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, desde el resultado anterior.



    1. Conecte el dispositivo usando el destino IQN. El dispositivo de StorSimple es el destino iSCSI aquí. Tipo:

        `iscsiadm -m node --login -T <IQN of iSCSI target>`

        En el ejemplo siguiente se muestra el resultado con un destino IQN de `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. El resultado indica que ha conectado correctamente a las dos interfaces de red habilitado para iSCSI en el dispositivo.

            Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
            Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
            Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
            Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
            Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
            Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
            Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
                Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.


        Si ve interfaz host solo una y dos rutas aquí, debe habilitar tanto las interfaces de host para iSCSI. Puede seguir las [instrucciones detalladas en la documentación de Linux](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).


    1. Un volumen se expone en el servidor de CentOS desde el dispositivo de StorSimple. Para obtener más información, vea [paso 6: crear un volumen](storsimple-deployment-walkthrough.md#step-6-create-a-volume) a través del portal clásico Azure en el dispositivo StorSimple.

    1. Compruebe las rutas de acceso disponibles. Tipo:

        `multipath –l`

        En el ejemplo siguiente se muestra el resultado de dos interfaces de red en un dispositivo StorSimple conectado a una interfaz de red único host con dos rutas de acceso disponibles.

            mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
            size=100G features='0' hwhandler='0' wp=rw
            `-+- policy='round-robin 0' prio=0 status=active
              |- 7:0:0:1 sdc 8:32 active undef running
              `- 6:0:0:1 sdd 8:48 active undef running

        En el ejemplo siguiente se muestra el resultado de dos interfaces de red en un dispositivo StorSimple conectado a dos interfaces de red de host con cuatro rutas disponibles.

            mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
            size=100G features='0' hwhandler='0' wp=rw
            `-+- policy='round-robin 0' prio=0 status=active
              |- 17:0:0:0 sdb 8:16 active undef running
              |- 15:0:0:0 sdd 8:48 active undef running
              |- 14:0:0:0 sdc 8:32 active undef running
              `- 16:0:0:0 sde 8:64 active undef running

        Después de configuran las rutas de acceso, consulte las instrucciones específicas de su sistema operativo de host (Centos 6.6) para montaje y dar formato a este volumen.


## <a name="troubleshoot-multipathing"></a>Solucionar problemas de múltiples rutas

Esta sección proporciona algunos consejos útiles si tiene problemas durante la configuración de múltiples rutas.

PREGUNTAS. No ver los cambios en `multipath.conf` archivo surtir efecto.

A. Si ha realizado cambios en el `multipath.conf` archivo, debe reiniciar el servicio de múltiples rutas. Escriba el siguiente comando:

    service multipathd restart

PREGUNTAS. He habilitado dos interfaces de red en el dispositivo StorSimple y dos interfaces de red en el host. Cuando la lista de las rutas de acceso disponibles veo solo dos rutas de acceso. Esperaba ver cuatro rutas disponibles.

A. Asegúrese de que las rutas de dos están en la misma subred y enrutables. Si las interfaces de red están en diferentes VLAN y no enrutables, verá solo dos rutas de acceso. Una forma de comprobarlo es para asegurarse de que puede llegar a las interfaces de host de una interfaz de red en el dispositivo StorSimple. Necesita ponerse en [contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) como esta comprobación puede se hará a través de una sesión de asistencia.

PREGUNTAS. Cuando la lista de rutas disponibles no veo ningún resultado.

A. Normalmente, no ver las rutas stmsboot sugiere un problema con el daemon de múltiples rutas y es probable que cualquier problema aquí se encuentra la `multipath.conf` archivo.

También sería conveniente comprobar que realmente puede ver algunos discos después de conectar con el destino, como ninguna respuesta de las listas de múltiples rutas también podría deberse a que no tiene ningún disco.

- Use el comando siguiente para volver a examinar el bus SCSI:

    `$ rescan-scsi-bus.sh `(parte del paquete de sg3_utils)

- Escriba los siguientes comandos:

    `$ dmesg | grep sd*`

- O

    `$ fdisk –l`

    Estos devolverá detalles de discos recientemente agregados.

- Para determinar si se trata de un disco StorSimple, use los comandos siguientes:

    `cat /sys/block/<DISK>/device/model`

    Esto devolverá una cadena, que determina si se trata de un disco StorSimple.

A menos causa posible pero es muy probable que también podría ser iscsid desfasado pid. Use el comando siguiente para cerrar la sesión de las sesiones iSCSI:

    iscsiadm -m node --logout -p <Target_IP>

Repita este comando para todas las interfaces de red conectada en el destino iSCSI, que es el dispositivo de StorSimple. Una vez que se ha cerrado la sesión de todas las sesiones iSCSI, use el destino iSCSI IQN para restablecer la sesión de iSCSI. Escriba el siguiente comando:

    iscsiadm -m node --login -T <TARGET_IQN>


PREGUNTAS. No estoy seguro de si el dispositivo aparece en la lista blanca.

A. Para comprobar si el dispositivo está en la lista blanca, utilice el siguiente comando interactivo de solución de problemas:

    multipathd –k
    multipathd> show devices
    available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


Para obtener más información, vaya a [utilizar para solucionar problemas comando interactivo múltiples rutas](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html).

## <a name="list-of-useful-commands"></a>Lista de comandos útiles

|Tipo|Comando|Descripción|
|---|---|---|
|**iSCSI**|`service iscsid start`|Iniciar el servicio iSCSI|
|&nbsp;|`service iscsid stop`|Detener el servicio iSCSI|
|&nbsp;|`service iscsid restart`|Reinicie el servicio iSCSI|
|&nbsp;|`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>`|Descubrir destinos disponibles en la dirección especificada|
|&nbsp;|`iscsiadm -m node --login -T <TARGET_IQN>`|Inicie sesión en el destino iSCSI|
|&nbsp;|`iscsiadm -m node --logout -p <Target_IP>`|Cerrar sesión en el destino iSCSI|
|&nbsp;|`cat /etc/iscsi/initiatorname.iscsi`|Imprimir el nombre del iniciador iSCSI|
|&nbsp;|`iscsiadm –m session –s <sessionid> -P 3`|Comprobar el estado de la sesión de iSCSI y el volumen encontrados en el host|
|&nbsp;|`iscsi –m session`|Muestra todas las sesiones iSCSI establecidas entre el host y el dispositivo StorSimple|
| | | |
|**Múltiples rutas**|`service multipathd start`|Daemon de múltiples rutas de inicio|
|&nbsp;|`service multipathd stop`|Detener daemon de múltiples rutas|
|&nbsp;|`service multipathd restart`|Reinicie daemon varias rutas|
|&nbsp;|`chkconfig multipathd on` </br> OR </br> `mpathconf –with_chkconfig y`|Habilitar múltiples rutas daemon al principio de la hora de inicio|
|&nbsp;|`multipathd –k`|Iniciar la consola interactiva de solución de problemas|
|&nbsp;|`multipath –l`|Conexiones múltiples rutas de lista y dispositivos|
|&nbsp;|`mpathconf --enable`|Crear un archivo de ejemplo mulitpath.conf en`/etc/mulitpath.conf`|
||||

## <a name="next-steps"></a>Pasos siguientes

Mientras está configurando MPIO en el host Linux, también debe hacer referencia a los documentos de CentoS 6.6 siguientes:

- [Configurar MPIO en CentOS](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
- [Guía de aprendizaje de Linux](http://linux-training.be/files/books/LinuxAdm.pdf)
