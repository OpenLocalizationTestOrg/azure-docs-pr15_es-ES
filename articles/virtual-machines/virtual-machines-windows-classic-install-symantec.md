<properties
    pageTitle="Instalar Symantec Endpoint Protection en una máquina virtual | Microsoft Azure"
    description="Obtenga información sobre cómo instalar y configurar la extensión de seguridad de Symantec Endpoint Protection en una nueva o existente Azure máquina virtual creada con el modelo de implementación clásico."
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

# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Cómo instalar y configurar Symantec Endpoint Protection en una máquina virtual de Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Este artículo le muestra cómo instalar y configurar al cliente de Symantec Endpoint Protection en una máquina virtual (VM) existente con Windows Server. Este es el cliente completo, que incluye servicios como virus y protección de spyware, firewall y prevención de intrusiones. El cliente se instala como una extensión de seguridad mediante el agente de máquina virtual.

Si tiene una suscripción existente de Symantec para una solución local, puede usarlo para proteger sus máquinas virtuales Azure. Si no está un cliente todavía, puede registrarse para una suscripción de prueba. Para obtener más información sobre esta solución, consulte [Symantec Endpoint Protection en la plataforma de Microsoft Azure][Symantec]. Esta página también contiene vínculos a información sobre licencia e instrucciones para instalar al cliente si ya es un cliente de Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Instalar Symantec Endpoint Protection en una máquina virtual existente

Antes de empezar, necesita lo siguiente:

- El módulo de PowerShell de Azure, versión 0.8.2 o posterior, en el equipo de trabajo. Puede comprobar la versión de PowerShell de Azure que ha instalado con el **azure Get-módulo | versión de formato de tabla** comando. Para obtener instrucciones y un vínculo a la última versión, consulte [cómo instalar y configurar Azure PowerShell][PS]. Inicie sesión en su suscripción de Azure usando `Add-AzureAccount`.

- Agente de VM que se ejecuta en la máquina Virtual de Azure.

En primer lugar, compruebe que el agente de VM ya está instalado en el equipo virtual. Rellene el nombre de servicio de nube y máquina virtual y, a continuación, ejecute los comandos siguientes en un símbolo de Azure PowerShell de nivel de administrador. Reemplazar todo el contenido de las comillas, incluidos los caracteres < y >.

> [AZURE.TIP] Si no conoce los nombres de máquina virtual y servicio de nube, ejecute **Get-AzureVM** para obtener una lista de los nombres de todas las máquinas virtuales de su suscripción actual.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Si el comando de **escritura host** muestra **True**, está instalado el agente de máquina virtual. Si muestra **False**, consulte las instrucciones y un vínculo a la descarga en el blog de Azure publicar [VM agente y extensiones - parte 2][Agent].

Si está instalado el agente de VM, ejecute los comandos para instalar al agente de Symantec Endpoint Protection.

    $Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

    Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Para comprobar que la extensión de seguridad de Symantec se ha instalado y está actualizada:

1.  Inicie sesión en la máquina virtual. Para obtener instrucciones, vea [cómo iniciar sesión en una máquina Virtual que ejecuta Windows Server][Logon].
2.  Para Windows Server 2008 R2, haga clic en **Inicio > Symantec Endpoint Protection**. Para Windows Server 2012 o Windows Server 2012 R2, desde la pantalla de inicio, escriba **Symantec**y, a continuación, haga clic en **Symantec Endpoint Protection**.
3.  En la pestaña de **estado** de la ventana de **Estado Symantec Endpoint Protection** , aplicar actualizaciones o reiniciar si es necesario.

## <a name="additional-resources"></a>Recursos adicionales

[Cómo iniciar sesión en una máquina Virtual ejecuta Windows Server][Logon]

[Características y extensiones de azure VM][Ext]


<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-classic-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493