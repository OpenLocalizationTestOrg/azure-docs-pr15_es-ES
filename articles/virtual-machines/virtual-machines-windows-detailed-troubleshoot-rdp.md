<properties
    pageTitle="Detallada remote desktop de solución de problemas | Microsoft Azure"
    description="Revisar detalladas pasos para solucionar problemas de errores de escritorio remotos donde no tiene un máquinas virtuales de Windows en Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"
    keywords="no puede conectarse a Escritorio remoto, solucionar problemas de escritorio remoto, no puede conectarse a Escritorio remoto, errores de escritorio remotos, solución de problemas de escritorio remoto, problemas de escritorio remoto
"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Detallada pasos para solucionar problemas de conexión a Escritorio remoto en máquinas virtuales de Windows en Azure

Este artículo proporciona los pasos detallados de solución de problemas para diagnosticar y solucionar errores de escritorio remoto complejos para basado en Windows Azure máquinas virtuales de Windows.

> [AZURE.IMPORTANT] Para eliminar los errores más comunes de escritorio remoto, asegúrese de leer [el artículo de solución de problemas básico para escritorio remoto](virtual-machines-windows-troubleshoot-rdp-connection.md) antes de continuar.

Puede encontrar un mensaje de error de escritorio remoto que no son similares a cualquiera de los mensajes de error específicos cubiertos en [la Guía de solución de problemas de escritorio remoto básica](virtual-machines-windows-troubleshoot-rdp-connection.md). Siga estos pasos para determinar por qué no se puede conectar con el servicio RDP en la máquina virtual de Azure el cliente de escritorio remoto (RDP).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si necesita más ayuda en cualquier punto de este artículo, póngase en contacto con los expertos de Azure en [Azure de MSDN y los foros de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede abrir un incidente de soporte de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte**. Para obtener información sobre el uso de soporte técnico de Azure, lea las [Preguntas más frecuentes sobre soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/faq/).


## <a name="components-of-a-remote-desktop-connection"></a>Componentes de una conexión a Escritorio remoto

Los siguientes componentes intervienen en una conexión RDP:

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_0.png)

Antes de continuar, puede que sea conveniente mentalmente revise lo que ha cambiado desde la última conexión a Escritorio remoto correctamente la máquina virtual. Por ejemplo:

- Ha cambiado la dirección IP pública de la máquina virtual o el servicio de nube que contiene la máquina virtual (también denominada la dirección IP virtual [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)). RDP error puede deberse a que la caché del cliente DNS aún tiene la *dirección IP antigua* registrados para el nombre DNS. Vaciar la caché del cliente DNS e intente conectarse de nuevo la máquina virtual. O bien, intente conectarse directamente con la dirección VIP nueva.
- Está utilizando una aplicación de terceros para administrar las conexiones de escritorio remoto en lugar de usar la conexión generada por el portal de Azure. Compruebe que la configuración de la aplicación incluye el puerto TCP correcto para el tráfico de escritorio remoto. Puede comprobar este puerto para una máquina virtual clásica en el [portal de Azure](https://portal.azure.com), haciendo clic en configuración de la máquina virtual > extremos.


## <a name="preliminary-steps"></a>Pasos previos

Antes de continuar con la solución detallada

- Comprobar el estado de la máquina virtual en el portal de clásico Azure o en el portal de Azure los problemas obvios.
- Siga los [pasos de corrección rápida para los errores comunes de RDP en la guía básica de solución de problemas](virtual-machines-windows-troubleshoot-rdp-connection.md#quick-troubleshooting-steps).


Vuelva a conectar a la máquina virtual a través de escritorio remoto después de estos pasos.


## <a name="detailed-troubleshooting-steps"></a>Pasos detallados de solución de problemas

Es posible que el cliente de escritorio remoto no pueda llegar al servicio de escritorio remoto en la máquina virtual de Azure debido a problemas de los siguientes orígenes:

- [Equipo cliente de escritorio remoto](#source-1-remote-desktop-client-computer)
- [Dispositivo de borde de la intranet de organización](#source-2-organization-intranet-edge-device)
- [Extremo de servicio de nube y control lista de acceso (ACL)](#source-3-cloud-service-endpoint-and-acl)
- [Grupos de seguridad de red](#source-4-network-security-groups)
- [Basado en Windows Azure VM](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Origen 1: Equipo cliente de escritorio remoto

Compruebe que su equipo puede crear conexiones de escritorio remoto a otro en las instalaciones, equipo basado en Windows.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_1.png)

Si no tiene, compruebe la siguiente configuración en el equipo:

- Configuración de firewall local que está bloqueando el tráfico de escritorio remoto.
- Localmente instalado software de proxy de cliente que impide que las conexiones de escritorio remoto.
- Localmente instalado software que impide conexiones de escritorio remoto de supervisión de red.
- Otros tipos de software de seguridad que supervisión el tráfico o permiten o no permitir determinados tipos de tráfico que impide que las conexiones de escritorio remoto.

En todos estos casos, temporalmente deshabilitar el software e intenta conectar con un equipo local a través de escritorio remoto. Si puede averiguar la causa real de este modo, trabajar con su administrador de red para corregir la configuración de software para permitir conexiones de escritorio remoto.

## <a name="source-2-organization-intranet-edge-device"></a>Origen 2: Dispositivo de borde de la intranet de organización

Compruebe que un equipo directamente conectado a Internet puede crear conexiones de escritorio remoto a su máquina virtual Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_2.png)

Si no tiene un equipo que está conectado directamente a Internet, crear y probar con una nueva máquina virtual de Azure en un servicio de nube o de grupo de recursos. Para obtener más información, vea [crear una máquina virtual con Windows en Azure](virtual-machines-windows-hero-tutorial.md). Puede eliminar la máquina virtual y el grupo de recursos o el servicio de nube después de la prueba.

Si se puede crear una conexión a Escritorio remoto con un equipo conectado directamente a Internet, compruebe el dispositivo de borde de la intranet de organización para:

- Un firewall interno bloqueando las conexiones HTTPS a Internet.
- Un servidor proxy e impedir las conexiones de escritorio remoto.
- Detección de intrusiones o software que se ejecuta en dispositivos en su red de borde que impide que las conexiones de escritorio remoto para supervisar la red.

Trabajar con el Administrador de red para corregir la configuración de su dispositivo de borde de la intranet de organización para permitir conexiones de escritorio remoto basada en HTTPS a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origen 3: Extremo de servicio de nube y ACL

Para máquinas virtuales creadas con el modelo de implementación de clásico, compruebe que otro VM de Azure que está en el mismo servicio de nube o una red virtual puede realizar conexiones de escritorio remoto en la máquina virtual de Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_3.png)

> [AZURE.NOTE] Para máquinas virtuales que creó en el Administrador de recursos, vaya a [origen 4: grupos de seguridad de red](#source-4-network-security-groups).

Si no tiene otro equipo virtual en el mismo servicio de nube o una red virtual, puede crear uno. Siga los pasos de [crear una máquina virtual con Windows en Azure](virtual-machines-windows-hero-tutorial.md). Eliminar la máquina virtual de prueba una vez completada la prueba.

Si puede conectarse a través de escritorio remoto a una máquina virtual en el mismo servicio de nube o una red virtual, compruebe estas opciones:

- La configuración de extremo para el tráfico de escritorio remoto en la máquina virtual de destino: el puerto TCP privado del extremo debe coincidir con el puerto TCP en el que está escuchando el servicio de escritorio remoto de la máquina virtual (3389 de forma predeterminada).
- Las ACL del extremo de tráfico de escritorio remoto en la máquina virtual de destino: ACL le permiten especificar permitir o denegar el tráfico entrante de Internet según su dirección IP de origen. ACL mal configuradas pueden impedir que el tráfico de escritorio remoto entrante al extremo. Compruebe su ACL para asegurarse de que el tráfico entrante desde las direcciones IP públicas del proxy o se permite otro servidor perimetral. Para obtener más información, vea [¿Qué es una lista de Control de acceso (ACL) de red?](../virtual-network/virtual-networks-acl.md)

Para comprobar si el extremo es el origen del problema, quite el extremo actual y crear uno nuevo, elegir un puerto aleatorio en el rango de 49152 a 65535 para el número de puerto externo. Para obtener más información, consulte [cómo configurar los extremos de una máquina virtual](virtual-machines-windows-classic-setup-endpoints.md).

## <a name="source-4-network-security-groups"></a>Origen 4: Grupos de seguridad de red

Grupos de seguridad de red permiten un control más detallado de tráfico entrante y saliente permitido. Puede crear reglas de subredes y servicios en una red virtual Azure en la nube. Compruebe las reglas de grupo de seguridad de la red para asegurarse de que se permite el tráfico de escritorio remoto de Internet:

- En el portal de Azure, seleccione la máquina virtual
- Haga clic en **configuración de todos los** | **interfaces de red** y seleccione la interfaz de red.
- Haga clic en **configuración de todos los** | **grupo de seguridad de red** y seleccione el grupo de seguridad de la red.
- Haga clic en **configuración de todos los** | **reglas de seguridad entrante** y asegúrese de que tiene una regla que permita RDP en el puerto TCP 3389.
    - Si no tiene una regla, haga clic en **Agregar** para crear una regla. Escriba **TCP** para el protocolo y, a continuación, **3389** para el rango de puerto de destino.
    - Asegúrese de que la acción se establece en **Permitir** y haga clic en Aceptar para guardar la nueva regla de entrada.


Para obtener más información, vea [¿Qué es un grupo de seguridad de la red (GSN)?](../virtual-network/virtual-networks-nsg.md)

## <a name="source-5-windows-based-azure-vm"></a>Origen 5: Basado en Windows Azure VM

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_5.png)

Use el [paquete de diagnósticos de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para ver si el error es debido a la máquina virtual Azure. Si no puede resolver el problema de **conectividad de RDP en una máquina virtual de Azure (es necesario reiniciar)** este paquete Diagnósticos, siga las instrucciones de [este artículo](virtual-machines-windows-reset-rdp.md). En este artículo, se restablece el servicio de escritorio remoto en la máquina virtual:

- Habilite la regla de Firewall de Windows "Escritorio remoto" predeterminado (el puerto TCP 3389).
- Habilitar conexiones de escritorio remoto, establezca el valor del registro HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections 0.

Intente conectarse desde el equipo de nuevo. Si está todavía no puede conectarse a través de escritorio remoto, compruebe los posibles problemas siguientes:

- No se ejecuta el servicio de escritorio remoto en la máquina virtual de destino.
- El servicio de escritorio remoto no está escuchando en el puerto TCP 3389.
- Firewall de Windows u otro firewall local tiene una regla de salida que impide que el tráfico de escritorio remoto.
- Detección de intrusiones o software que se ejecuta en la máquina virtual Azure para supervisar la red impide conexiones de escritorio remoto.

Para máquinas virtuales creadas con el modelo de implementación clásico, puede usar una sesión remota de PowerShell de Azure en la máquina virtual Azure. En primer lugar, debe instalar un certificado de servicio de nube de host de la máquina virtual. Vaya a [Configurar Remote PowerShell acceso seguro a Azure máquinas virtuales de Windows](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) y descargue el archivo de comandos **InstallWinRMCertAzureVM.ps1** en el equipo local.

A continuación, instale PowerShell Azure si todavía no lo ha hecho. Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

A continuación, abra un símbolo del sistema de PowerShell de Azure y cambiar la carpeta actual a la ubicación del archivo de script **InstallWinRMCertAzureVM.ps1** . Para ejecutar una secuencia de comandos de PowerShell de Azure, debe establecer la directiva de ejecución correcta. Ejecute el comando **Get-ExecutionPolicy** para determinar el nivel actual de directiva. Para obtener información sobre cómo configurar el nivel adecuado, consulte [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

A continuación, rellene su nombre de suscripción de Azure, el nombre de servicio de nube y el nombre de la máquina virtual (quitando el < y > caracteres), y luego ejecute estos comandos.

    $subscr="<Name of your Azure subscription>"
    $serviceName="<Name of the cloud service that contains the target virtual machine>"
    $vmName="<Name of the target virtual machine>"
    .\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Puede obtener el nombre de suscripción correcta de la propiedad _SubscriptionName_ de la pantalla del comando **Get-AzureSubscription** . Puede obtener el nombre de servicio de nube para la máquina virtual de la columna _ServiceName_ en la pantalla del comando **Get-AzureVM** .

Comprobar si el nuevo certificado. Abra un complemento de certificados para el usuario actual y busque en la carpeta **Raíz certificación de confianza\certificados** . Debería ver un certificado con el nombre de su servicio de nube en la columna emitido para DNS (ejemplo: cloudservice4testing.cloudapp.net).

A continuación, iniciar una sesión remota de PowerShell de Azure mediante estos comandos.

    $uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
    $creds = Get-Credential
    Enter-PSSession -ConnectionUri $uri -Credential $creds

Después de escribir las credenciales de administrador válida, verá algo similar a la siguiente pregunta de Azure PowerShell:

    [cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

La primera parte de este mensaje es su nombre de servicio de nube que contiene el destino VM, que podrían ser diferentes a "cloudservice4testing.cloudapp.net". Ahora puede emitir Azure PowerShell comandos para este servicio de nube investigar los problemas mencionan y corregir la configuración.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Corregir manualmente los servicios de escritorio remoto escuchando el puerto TCP

Si no puede ejecutar el [paquete de diagnósticos de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para el problema de **conectividad de RDP en una máquina virtual de Azure (es necesario reiniciar)** , en el símbolo de sesión remoto de PowerShell de Azure, ejecutar este comando.

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

La propiedad de número de puerto muestra el número de puerto actual. Si es necesario, cambie el escritorio remoto puerto número nuevo a su valor predeterminado (3389) con este comando.

    Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Compruebe que el puerto se cambió a 3389 mediante este comando.

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Salir de la sesión remota de PowerShell de Azure con este comando.

    Exit-PSSession

Compruebe que el extremo de escritorio remoto para Azure VM también está usando el puerto TCP 3398 como su puerto interno. Reinicie la máquina virtual de Azure e inténtelo de nuevo la conexión a Escritorio remoto.


## <a name="additional-resources"></a>Recursos adicionales

[Paquete de diagnósticos de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Cómo restablecer una contraseña o el servicio de escritorio remoto máquinas virtuales de Windows](virtual-machines-windows-reset-rdp.md)

[Cómo instalar y configurar PowerShell de Azure](../powershell-install-configure.md)

[Solucionar problemas de conexiones de Shell seguro (SSH) a una máquina virtual para Azure basados en Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual Azure](virtual-machines-linux-troubleshoot-app-connection.md)
