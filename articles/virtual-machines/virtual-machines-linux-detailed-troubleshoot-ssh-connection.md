<properties
    pageTitle="Solución de problemas detallada de SSH para una máquina virtual de Azure | Microsoft Azure"
    description="Obtener más SSH pasos para problemas para conectarse a una máquina virtual Azure para solucionar problemas"
    keywords="SSH conexión rechazada, ssh error, azure ssh SSH error de conexión"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="detailed-ssh-troubleshooting-steps"></a>Pasos de solución de problemas de SSH detallados

Hay muchas razones posibles que el cliente SSH no pueda tener acceso al servicio SSH en la máquina virtual. Si ha seguido los [pasos de solución de problemas generales de SSH](virtual-machines-linux-troubleshoot-ssh-connection.md)de más, debe solucionar el problema de conexión. En este artículo le guiará por los pasos detallados de solución de problemas para determinar dónde falla la conexión SSH y cómo resolverlo.

## <a name="take-preliminary-steps"></a>Tomar algunas medidas preliminares

El siguiente diagrama muestra los elementos que intervienen.

![Diagrama que muestra los componentes del servicio SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Los siguientes pasos ayudan a aislar la causa del error y averiguar soluciones.

En primer lugar, compruebe el estado de la máquina virtual en el portal.

En el [portal de Azure](https://portal.azure.com):

1. Para las VM creadas mediante el modelo de implementación clásico, seleccione **Examinar** > **máquinas virtuales de Windows (clásico)** > *nombre de la máquina virtual*.

    -O-

    Para las VM creadas mediante el modelo de administrador de recursos, seleccione **Examinar** > **máquinas virtuales** > *nombre de la máquina virtual*.

    El panel de estado de la máquina virtual debe mostrar **ejecutando**. Desplácese hacia abajo para mostrar la actividad reciente de cálculo, almacenamiento y recursos de red.

2. Seleccione **configuración** para examinar extremos, direcciones IP y otras opciones de configuración.

    Para identificar los extremos en máquinas virtuales que se crearon usando el Administrador de recursos, compruebe que se ha definido un [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md) . Compruebe también que se han aplicado las reglas en el grupo de seguridad de la red y que está hace referencia en la subred.

En el [portal de clásico de Azure](https://manage.windowsazure.com), para máquinas virtuales que se crearon usando el modelo de implementación clásica:

1. Seleccione **máquinas virtuales** > *nombre de la máquina virtual*.
2. Seleccione de la máquina virtual **paneles** para comprobar su estado.
3. Seleccione **Monitor** para ver la actividad reciente de cálculo, almacenamiento y recursos de red.
4. Seleccione **extremos** para asegurarse de que hay un punto final para el tráfico SSH.

Para comprobar la conectividad de red, compruebe los extremos configurados y vea si puede llegar a la máquina virtual a través de otro protocolo, como HTTP u otro servicio.

Después de estos pasos, pruebe la conexión SSH.


## <a name="find-the-source-of-the-issue"></a>Encontrar el origen del problema

El cliente SSH en su equipo puede producir un error alcance el servicio SSH en la máquina virtual de Azure debido a problemas o errores de configuración de la información siguiente:

- [Equipo del cliente SSH](#source-1-ssh-client-computer)
- [Dispositivo de borde de organización](#source-2-organization-edge-device)
- [Extremo de servicio de nube y control lista de acceso (ACL)](#source-3-cloud-service-endpoint-and-acl)
- [Grupos de seguridad de red](#source-4-network-security-groups)
- [Basados en Linux Azure VM](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Fuente 1: SSH equipo cliente

Para eliminar el equipo como el origen del error, compruebe que pueden crear conexiones de SSH a otro en las instalaciones, equipo basado en Linux.

![Diagrama que destaca los componentes de los equipos cliente SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Si se produce un error en la conexión, compruebe lo siguiente en su equipo:

- Una configuración de firewall local que está bloqueando el tráfico entrante o saliente SSH (TCP 22)
- Localmente instalado software de proxy de cliente que impide SSH conexiones
- Localmente instalado software que impide SSH conexiones de red
- Otros tipos de software de seguridad que supervisión el tráfico o permiten o no permitir determinados tipos de tráfico

Si aplica una de estas condiciones, temporalmente deshabilitar el software y pruebe una conexión SSH a un equipo local para averiguar el motivo de que la conexión está bloqueada en el equipo. Trabajar con el Administrador de red para corregir la configuración de software para permitir conexiones SSH.

Si está utilizando la autenticación de certificado, compruebe que tiene los permisos siguientes a la carpeta .ssh en el directorio de inicio:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/\*.pub
- Chmod 600 ~/.ssh/id_rsa (o cualquier otro archivo que tiene las claves privadas almacenadas en ellos)
- ~/.Ssh/known_hosts 644 chmod (contiene hosts que se han conectado a través de SSH)

## <a name="source-2-organization-edge-device"></a>Origen 2: Dispositivo de borde de organización

Para eliminar el dispositivo de borde de la organización como el origen del error, compruebe que un equipo que está conectado directamente a Internet puede realizar conexiones SSH en su máquina virtual de Azure. Si tiene acceso a la máquina virtual a través de una VPN de sitio a sitio o una conexión de Azure ExpressRoute, vaya a [origen 4: grupos de seguridad de red](#nsg).

![Diagrama que resalta el dispositivo de borde de organización](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Si no tiene un equipo que está directamente conectado a Internet, crear una nueva máquina virtual de Azure en su propio grupo de recursos o servicio de nube y usarlo. Para obtener más información, vea [crear una máquina virtual con Linux en Azure](virtual-machines-linux-quick-create-cli.md). Eliminar el grupo de recursos o el servicio VM y nube cuando haya finalizado la prueba.

Si se puede crear una conexión SSH con un equipo que está conectado directamente a Internet, compruebe el dispositivo de borde de la organización para:

- Un servidor de seguridad que está bloqueando el tráfico SSH con Internet
- Un servidor proxy que impide SSH conexiones
- Detección de intrusiones o supervisión de software que se ejecuta en dispositivos en su red de borde que impide SSH conexiones de red

Trabajar con el Administrador de red para corregir la configuración de los dispositivos de borde de la organización para permitir el tráfico SSH con Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origen 3: Extremo de servicio de nube y ACL

> [AZURE.NOTE] Este origen se aplica solo a máquinas virtuales que se crearon usando el modelo de implementación clásico. Para máquinas virtuales que se crearon usando el Administrador de recursos, vaya al [de origen de 4: grupos de seguridad de red](#nsg).

Para eliminar el extremo de servicio de nube y ACL como el origen del error, compruebe que otro VM de Azure en la misma red virtual puede realizar conexiones SSH en su máquina virtual.

![Diagrama que resalta el extremo de servicio de nube y ACL](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Si no tiene otro VM en la misma red virtual, puede crear fácilmente una nueva. Para obtener más información, consulte [crear una máquina virtual de Linux en Azure con CLI](virtual-machines-linux-quick-create-cli.md). Eliminar la máquina virtual adicional cuando haya terminado con las pruebas.

Si se puede crear una conexión SSH con una máquina virtual en la misma red virtual, compruebe lo siguiente:

- **La configuración de extremo para el tráfico SSH en la máquina virtual de destino.** El puerto TCP privado del extremo debe coincidir con el puerto TCP en el que está escuchando el servicio SSH en la máquina virtual. (El puerto predeterminado es 22). Para las VM creadas mediante el modelo de implementación de administrador de recursos, compruebe el número de puerto TCP SSH en el portal de Azure seleccionando **Examinar** > **máquinas virtuales (v2)** > *nombre de VM* > **configuración** > **extremos**.

- **ACL del extremo de tráfico SSH en la máquina virtual de destino.** Una ACL permite especificar permitir o denegar el tráfico entrante de Internet, en función de su dirección IP de origen. Mal configuradas ACL pueden impedir que el tráfico SSH entrante al extremo. Compruebe su ACL para asegurarse de que el tráfico entrante desde las direcciones IP públicas del proxy o se permite otro servidor perimetral. Para obtener más información, consulte [acerca del acceso de red listas de control (ACL)](../virtual-network/virtual-networks-acl.md).

Para eliminar el extremo como un origen del problema, quite el extremo actual, crear un nuevo punto final y especifique el nombre SSH (puerto TCP 22 para el número de puerto públicas y privadas). Para obtener más información, vea [configurar extremos en una máquina virtual en Azure](virtual-machines-windows-classic-setup-endpoints.md).

<a id="nsg"></a>
## <a name="source-4-network-security-groups"></a>Origen 4: Grupos de seguridad de red

Grupos de seguridad de la red le permiten tener un control más detallado de tráfico entrante y saliente permitido. Puede crear reglas que abarcan subredes y servicios en una red virtual Azure en la nube. Comprobar las reglas de grupo de seguridad de red para asegurarse de que se permite el tráfico SSH a y desde Internet.
Para obtener más información, consulte [acerca de los grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md).

## <a name="source-5-linux-based-azure-virtual-machine"></a>Origen 5: Equipo de Azure virtual basado en Linux

La última fuente de posibles problemas es la máquina virtual Azure.

![Diagrama que resalta basados en Linux máquina virtual de Azure](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Si aún no lo ha hecho, siga las instrucciones [para restablecer una contraseña o SSH para máquinas virtuales basadas en Linux](virtual-machines-linux-classic-reset-access.md).

Intente volver a conectarse desde su equipo. Si sigue sin funcionar, a las siguientes son algunas de los posibles problemas:

- El servicio SSH no se ejecuta en la máquina virtual de destino.
- El servicio SSH no está escuchando en el puerto TCP 22. Para probar esto, instale un cliente de telnet en el equipo local y ejecutar "telnet *cloudServiceName*. cloudapp.net 22". Esto determina si la máquina virtual permite una comunicación entrante y saliente al extremo SSH.
- El firewall local en la máquina virtual de destino tiene reglas que impiden la entrada o salido tráfico SSH.
- Detección de intrusiones o software que se ejecuta en la máquina virtual de Azure de red impide SSH conexiones.


## <a name="additional-resources"></a>Recursos adicionales
Para obtener más información sobre cómo solucionar problemas de acceso de la aplicación, vea [solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual Azure](virtual-machines-linux-troubleshoot-app-connection.md)