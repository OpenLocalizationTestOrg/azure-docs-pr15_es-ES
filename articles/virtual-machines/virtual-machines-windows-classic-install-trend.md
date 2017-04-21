<properties
    pageTitle="Instalar seguridad Micro detallado de tendencias en una máquina virtual | Microsoft Azure"
    description="En este artículo se describe cómo instalar y configurar la seguridad de tendencia Micro en una máquina virtual creada con el modelo de implementación clásico de Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="iainfou"/>


# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Cómo instalar y configurar la seguridad de profundidad de tendencia Micro como un servicio en una máquina virtual de Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Este artículo le muestra cómo instalar y configurar la seguridad de profundidad de tendencia Micro como un servicio en una nueva o existente máquina virtual (VM) ejecutan Windows Server. Profundidad seguridad como servicio incluye protección antimalware un firewall, un sistema de prevención de intrusiones y supervisión de integridad.

El cliente se instala como una extensión de seguridad a través del agente de máquina virtual. En un nuevo equipo virtual, instalar al agente de máquina virtual junto con el agente de seguridad de profundidad. En un equipo virtual existente que no tienen el agente de VM, debe descargar e instalar primero. En este artículo trata sobre ambos casos.

Si tiene una suscripción existente de tendencia Micro para una solución local, puede usar para ayudar a proteger sus máquinas virtuales Azure. Si no está un cliente todavía, puede registrarse para una suscripción de prueba. Para obtener más información sobre esta solución, consulte la tendencia Micro blog [Microsoft Azure VM agente de extensión de profundidad seguridad](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Instalar al agente de seguridad de profundidad en una nueva máquina virtual

El [portal clásica Azure](http://manage.windowsazure.com) le permite instalar el agente de máquina virtual y la extensión de seguridad de tendencia Micro cuando utiliza la opción **De la Galería** para crear la máquina virtual. Si está creando una sola máquina virtual, con el portal es una forma sencilla de agregar la protección de tendencia Micro.

Esta opción **De la Galería** abrirá a un asistente que le ayuda a configurar la máquina virtual. Use la última página del Asistente para instalar el agente de máquina virtual y la extensión de seguridad de tendencia Micro. Para obtener instrucciones generales, vea [crear una máquina virtual con Windows en el portal de clásico de Azure](virtual-machines-windows-classic-tutorial.md). Cuando llegue a la última página del asistente, haga lo siguiente:

1.  En el **Agente de VM**, active **Instalar agente de máquina virtual**.

2.  En **Extensiones de seguridad**, active el **Agente de seguridad de tendencia Micro detallado**.

    ![Instale el agente VM y el agente de seguridad de profundidad](./media/virtual-machines-windows-classic-install-trend/InstallVMAgentandTrend.png)

3.  Haga clic en la marca de verificación para crear la máquina virtual.

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instalar al agente de seguridad de profundidad en una máquina virtual existente

Para instalar al agente en una máquina virtual existente, se necesita lo siguiente:

- El módulo de PowerShell de Azure, versión 0.8.2 o posterior, instalado en el equipo local. Puede comprobar la versión de PowerShell de Azure que han instalado mediante la **azure Get-módulo | versión de formato de tabla** comando. Para obtener instrucciones y un vínculo a la última versión, consulte [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md). Inicie sesión en su suscripción de Azure usando `Add-AzureAccount`.

- VM instalado el agente en la máquina virtual de destino.

En primer lugar, compruebe que ya está instalado el agente de máquina virtual. Rellene el nombre de servicio de nube y máquina virtual y, a continuación, ejecute los comandos siguientes en un símbolo de Azure PowerShell de nivel de administrador. Reemplazar todo el contenido de las comillas, incluidos los caracteres < y >.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Si no conoce el servicio de nube y el nombre de la máquina virtual, ejecute **Get-AzureVM** para mostrar que la información de todas las máquinas virtuales de su suscripción actual.

Si el comando de **escritura host** devuelve **True**, está instalado el agente de máquina virtual. Si devuelve **False**, consulte las instrucciones y un vínculo a la descarga en el blog de Azure publicar [VM agente y extensiones - parte 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Si está instalado el agente de VM, ejecute estos comandos.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Pasos siguientes

Se necesitan unos minutos para el agente iniciar la ejecución cuando se instala. Después de eso, debe activar la seguridad de profundidad en la máquina virtual para que se puede administrar un administrador de seguridad detallado. Para obtener más instrucciones, vea:

- Artículo de la línea de tendencia sobre esta solución, [Instant-On de seguridad de nube de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
- Una [secuencia de comandos de Windows PowerShell](http://go.microsoft.com/fwlink/?LinkId=404100) para configurar la máquina virtual
- [Instrucciones](http://go.microsoft.com/fwlink/?LinkId=404099) para los datos de ejemplo

## <a name="additional-resources"></a>Recursos adicionales

[Cómo iniciar sesión en una máquina virtual ejecuta Windows Server]

[Características y extensiones de Azure VM]


<!--Link references-->
[Cómo iniciar sesión en una máquina virtual ejecuta Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Características y extensiones de Azure VM]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
