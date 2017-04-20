<properties
    pageTitle="Servicios de dominio de Azure Active Directory: Guía de administración | Microsoft Azure"
    description="Unirse a una máquina virtual de Windows a un dominio administrado usando PowerShell de Azure y el modelo de implementación clásico."
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="maheshu"/>


# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>Unirse a una máquina virtual de Windows Server a un dominio administrado con PowerShell

> [AZURE.SELECTOR]
- [Azure portal clásica - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

> [AZURE.IMPORTANT] Azure tiene dos diferentes modelos de implementación para crear y trabajar con recursos: [Administrador de recursos y clásica](../resource-manager-deployment-model.md). En este artículo trata sobre utiliza el modelo de implementación clásico. Servicios de dominio de Azure AD actualmente no admite el modelo de administrador de recursos.

Estos pasos le muestran cómo personalizar un conjunto de comandos de PowerShell de Azure que crear y configurar previamente una máquina virtual para Azure basado en Windows con un enfoque de bloque de creación. Estos pasos ayudan a crear una máquina virtual para Azure basado en Windows y unirse a un dominio administrado de servicios de dominio de Active Directory de Azure.

Estos pasos siguen un enfoque rellenar en blanco para crear conjuntos de comandos de PowerShell de Azure. Este enfoque puede ser útil si está familiarizado con PowerShell o desea saber qué valores debe para especificar para una configuración correcta. Los usuarios avanzados de PowerShell pueden tomar los comandos y sustituir sus propios valores para las variables (las líneas que comiencen por "$").

Si aún no lo ha hecho, use las instrucciones en [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para instalar Azure PowerShell en el equipo local. A continuación, abra un símbolo del sistema de Windows PowerShell.

## <a name="step-1-add-your-account"></a>Paso 1: Agregar su cuenta

1. En el símbolo del sistema de PowerShell, escriba **AzureAccount agregar** y haga clic en **ENTRAR**.
2. Escriba la dirección de correo electrónico asociada a su suscripción de Azure y haga clic en **continuar**.
3. Escriba la contraseña de su cuenta.
4. Haga clic en **iniciar sesión**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Paso 2: Configurar su cuenta de almacenamiento y de suscripción

Configurar su cuenta de almacenamiento y de suscripción Azure ejecutando estos comandos en el símbolo del sistema de Windows PowerShell. Reemplazar todo el contenido de las comillas, incluyendo la < y > caracteres, con los nombres correctos.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Puede obtener el nombre de suscripción correcta de la propiedad SubscriptionName del resultado del comando **Get-AzureSubscription** . Puede obtener el nombre de la cuenta de almacenamiento correcto de la propiedad de etiqueta de la salida del comando **Get-AzureStorageAccount** después de ejecutar el comando **Seleccionar AzureSubscription** .


## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>Paso 3: Tutorial paso a paso - aprovisionar la máquina virtual y unirse al dominio administrado
Aquí es el conjunto crear esta máquina virtual, con líneas en blanco entre cada bloque para mejorar la legibilidad de comandos de PowerShell de Azure correspondiente.

Especificar información sobre la máquina virtual de Windows estén configurados.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

Para los valores de InstanceSize de D, DS o máquinas virtuales de serie G, vea [Máquina Virtual y los tamaños de servicio de nube para Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Proporcionar información sobre el dominio administrado.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

Especifique el nombre del servicio de nube.

    $svcname="Contoso100-test"

Especifique el nombre de la red virtual al que debe combinarse la máquina virtual. Asegúrese de que el dominio administrado AAD DS está disponible en esta red virtual.

    $vnetname="MyPreviewVnet"

Seleccione la imagen de VM que se usará para aprovisionar la máquina virtual.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Configurar la VM - nombre del conjunto de máquina virtual, tamaño de la instancia e imagen para usarse.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Obtener las credenciales de administrador local para la máquina virtual. Elegir una contraseña de administrador local seguras.

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

Obtener credenciales para una cuenta de usuario que pertenecen al grupo 'AAD DC administradores' a una máquina virtual al dominio administrado. No especifique el nombre de dominio: por ejemplo, en nuestro ejemplo, hemos especifique 'bob' como nombre de usuario.

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

Configurar la máquina virtual: especifique el requisito de unirse al dominio y las credenciales necesarias.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

Establecer una subred de la máquina virtual.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

Opcional: Seleccione la dirección IP del dominio. Si establece las direcciones IP de servicios de dominio de Active Directory de Azure dominio administrado que los servidores DNS de la red virtual, este paso no es necesario.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

Ahora, aprovisionar la máquina virtual Unidos a un dominio de Windows.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>Secuencia de comandos para aprovisionar una máquina virtual de Windows y automáticamente se une a un dominio administrado de servicios de dominio de AAD
Este conjunto de comandos de PowerShell, crea una máquina virtual para un servidor de línea de negocio:

- Utiliza la imagen del centro de datos de Windows Server 2012 R2.
- Es una máquina virtual extra pequeña.
- Tiene la prueba de contoso nombre.
- Está automáticamente dominio unido al dominio contoso100 administrado.
- Se agrega a la misma red virtual que el dominio administrado.

Aquí tiene el script de ejemplo completo para crear la máquina virtual de Windows y se unen automáticamente en el dominio de servicios de dominio de Active Directory de Azure administrado.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>Contenido relacionado
- [Servicios de dominio de Azure AD - Guía de introducción](./active-directory-ds-getting-started.md)

- [Administrar un dominio administrado de servicios de dominio de Active Directory de Azure](./active-directory-ds-admin-guide-administer-domain.md)
