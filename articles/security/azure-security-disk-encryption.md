<properties
   pageTitle="Cifrado de disco Azure para Windows y máquinas virtuales de Linux IaaS | Microsoft Azure"
   description="Papel proporciona una descripción general de Microsoft Azure disco cifrado para Windows y máquinas virtuales de IaaS Linux."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="krkhan"/>


#<a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Cifrado de disco Azure para Windows y máquinas virtuales de Linux IaaS

Microsoft Azure se encarecidamente compromete a garantizar la privacidad de los datos, soberanía de datos y permite controlar su Azure hospedado datos a través de un rango de avanzadas tecnologías para cifrar, controlar y administrar las claves de cifrado, acceso de control y auditoría de datos. Esto proporciona a los clientes de Azure la flexibilidad para elegir la solución que mejor se adapte a sus necesidades empresariales. En este artículo, se explicará se a una solución de tecnología "Azure disco cifrado para Windows y de Linux IaaS VM" nueva para ayudar a proteger y proteger sus datos para cumplir con la seguridad de la organización y los compromisos de cumplimiento. Papel proporciona instrucciones detalladas sobre cómo usar las características de cifrado disco Azure, incluidos los escenarios compatibles y el usuario experiencias.

**Nota**: pueden provocar determinadas recomendaciones contenidas de datos mayores, red o uso de recursos de cálculo de costes adicionales de licencia o suscripción.

## <a name="overview"></a>Información general

Cifrado de disco Azure es una nueva funcionalidad que le permite cifrar los discos de máquina virtual de Windows y Linux IaaS. Cifrado de disco Azure aprovecha la característica [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) estándar de la industria de Windows y la característica de [Cifrado DM](https://en.wikipedia.org/wiki/Dm-crypt) de Linux para proporcionar cifrado de volumen para el sistema operativo y los discos de datos. La solución se integra con depósito de [Azure clave](https://azure.microsoft.com/documentation/services/key-vault/) para ayudarle a controlar y administrar las claves de cifrado de disco y la información confidencial en su suscripción de depósito clave, mientras le garantiza que todos los datos en los discos de la máquina virtual se cifran almacenados en el almacenamiento de Azure.

Cifrado de disco Azure para Windows y Linux IaaS VM está ahora en **La disponibilidad General** en todas las Azure públicos regiones para VM estándar y máquinas virtuales con almacenamiento premium.

### <a name="encryption-scenarios"></a>Escenarios de cifrado

La solución de cifrado de disco de Azure es compatible con los siguientes escenarios de cliente:

- Habilitar el cifrado en nuevas máquinas virtuales de IaaS creado a partir de disco duro de virtual previamente cifrados y claves de cifrado
- Habilitar el cifrado en nuevas máquinas virtuales de IaaS creado a partir de las imágenes de la Galería de Azure
- Habilitar el cifrado en máquinas virtuales de IaaS existentes ejecuta en Azure
- Deshabilitar el cifrado en máquinas virtuales de Windows IaaS
- Deshabilitar el cifrado en unidades de datos para máquinas virtuales de Linux IaaS

La solución es compatible con las siguientes acciones para máquinas virtuales de IaaS cuando habilitadas en Microsoft Azure:

- Integración con Azure depósito clave
- Nivel estándar de máquinas virtuales - [A, D, DS, G, GS etcetera serie IaaS VM](https://azure.microsoft.com/pricing/details/virtual-machines/)
- Habilitar el cifrado en Windows y Linux IaaS VM
- Deshabilitar el cifrado de las unidades de sistema operativo y datos para máquinas virtuales de Windows IaaS
- Deshabilitar el cifrado en unidades de datos para máquinas virtuales de Linux IaaS
- Habilitar el cifrado en máquinas virtuales de IaaS ejecutando el sistema operativo del cliente de Windows
- Habilitar el cifrado en volúmenes con rutas de montaje
- Habilitar el cifrado en máquinas virtuales de Linux configurada con RAID basado en Software de sistema 
- Habilitar el cifrado en máquinas virtuales de Windows configurados con espacios de almacenamiento
- Son compatibles con todas las regiones públicas de Azure

La solución no admite las siguientes situaciones, características y tecnología en la versión:

- Máquinas virtuales de IaaS de nivel básico
- Deshabilitar el cifrado de unidad de sistema operativo Linux IaaS VM
- Máquinas virtuales de IaaS creados con el método clásico de creación de VM
- Integración con el servicio de administración de claves local
- Windows Server 2016 Technical Preview no es compatible con esta versión
- Azure archivos (compartir archivo Azure), sistema de archivos de red (NFS), volúmenes dinámicos, máquinas virtuales de Windows configurados con sistemas RAID basado en Software


### <a name="encryption-features"></a>Características de cifrado

Cuando habilita e implementar cifrado de disco Azure para máquinas virtuales de IaaS de Azure, se habilitan las siguientes capacidades, según la configuración que se proporcionan:

- Cifrado de volumen del sistema operativo para proteger el volumen de inicio en el resto de almacenamiento de cliente
- Cifrado de volumen de datos/s para proteger los volúmenes de datos en el resto de almacenamiento de cliente
- Deshabilitar el cifrado de las unidades de sistema operativo y datos para máquinas virtuales de Windows IaaS
- Deshabilitar el cifrado en unidades de datos para máquinas virtuales de Linux IaaS
- Proteger las claves de cifrado y la información confidencial en la suscripción de Azure depósito clave de cliente
- Informar del estado de cifrado de cifrado IaaS VM
- Eliminación de opciones de configuración de cifrado de disco de la máquina virtual de IaaS

El cifrado de disco Azure para IaaS VMS para la solución de Windows y Linux incluye la extensión de cifrado de disco para Windows, la extensión de cifrado de disco para Linux, cmdlets de PowerShell de cifrado de disco, disco cifrado CLI cmdlets y plantillas de administrador de recursos de Azure de cifrado de disco. La solución de cifrado de disco Azure es compatible en máquinas virtuales de IaaS ejecutando Windows o sistema operativo Linux. Para obtener más detalles sobre los sistemas operativos compatibles, consulte la sección de requisitos previos a continuación.

**Nota**: no hay cargo para cifrar discos de VM con Azure disco cifrado.

### <a name="value-proposition"></a>Propuesta de valor

La solución de administración de cifrado de disco de Azure permite las siguientes necesidades empresariales en la nube:

-   IaaS VM están protegidos almacenados con la tecnología de cifrado estándar de la industria a requisitos de cumplimiento y seguridad de la organización dirección.
-   Inicio de IaaS VM cliente controlados claves y directivas y, a continuación, puede auditar su uso en depósito de clave.


### <a name="encryption-workflow"></a>Flujo de trabajo de cifrado

Los pasos de alto niveles necesarios para habilitar el cifrado de disco para Windows y de Linux VM son:

1. Cliente elige un escenario de cifrado de los escenarios de cifrado anteriores
2. Cliente opta en habilitar el cifrado de disco a través de la plantilla de administrador de recursos de cifrado de disco Azure o PS cmdlets o comando CLI y especifica la configuración de cifrado

    - Para el escenario de disco duro virtual de cliente cifrado, las cargas de cliente el disco duro virtual cifrada a su almacenamiento cuenta y cifrado material clave a su clave depósito y proporcionan la configuración de cifrado para habilitar el cifrado en una nueva VM IaaS
    - Para crea la nueva máquina virtual desde la Galería de Azure y máquina virtual existente ya en ejecución en Azure, cliente proporcionar la configuración de cifrado para habilitar el cifrado en la VM IaaS

3. Cliente concede acceso a la plataforma Windows Azure para leer el material de clave de cifrado (frase de contraseña para Linux y sistemas de claves de cifrado de BitLocker para Windows) de su cámara clave para habilitar el cifrado en la VM IaaS
4. El cliente proporcionar la identidad de la aplicación de Azure AD para escribir el material de clave de cifrado en su cámara clave para habilitar el cifrado en la IaaS VM para escenarios mencionados en #2 anteriores
5.  Azure actualiza el modelo de servicio VM con cifrado y configuración del depósito clave y disposiciones cifran VM para el cliente

![Microsoft Antimalware en Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Flujo de trabajo de descifrado

Los pasos de alto niveles necesarios para deshabilitar el cifrado de disco de IaaS VM son:

1. Cliente decide deshabilitar el cifrado (descifrado) en una VM IaaS en ejecución en Azure a través de la plantilla de administrador de recursos de cifrado de disco Azure o PS cmdlets y especifica la configuración de descifrado.
2. El paso de cifrado de deshabilitar deshabilita el cifrado de volumen de sistema operativo o datos o ambos en la máquina virtual está ejecutando IaaS de Windows. Sin embargo no se admite OS deshabilitar disco cifrado para Linux como se mencionó en la documentación anterior. Se permite el paso de deshabilitar sólo para unidades de datos en máquinas virtuales de Linux. 
4. Azure actualiza el modelo de servicio VM y la VM IaaS está marcada descifrada. El contenido de la máquina virtual no se cifra al resto ya.
5. La operación de cifrado deshabilitar no elimina el depósito de clave de cliente y el material de clave de cifrado,-claves de cifrado de BitLocker para Windows o frase de contraseña para Linux.

## <a name="prerequisites"></a>Requisitos previos

Los siguientes son los requisitos previos para habilitar el cifrado de disco de Azure en máquinas virtuales de Azure IaaS para las situaciones admitidas resaltadas en la sección información general

- Usuario debe tener una suscripción válida de Azure active para crear recursos en Azure en las regiones compatibles
- Cifrado de disco Azure es compatible con el siguiente servidor Windows SKU - Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2. Windows Server 2016 Technical Preview no es compatible con esta versión.
- Cifrado de disco Azure es compatible con el siguiente cliente Windows SKU: cliente de Windows 10 y Windows 8.

**Nota**: debe estar instalado para Windows Server 2008 R2, .net framework 4.5 antes de habilitar el cifrado en Azure. Puede instalarlo desde Windows update al instalar la actualización opcional "Microsoft .NET Framework 4.5.2 para Windows Server 2008 R2 x 64-sistemas ([KB2901983](https://support.microsoft.com/kb/2901983))"

- Cifrado de disco Azure es compatible con el siguiente servidor Linux SKU - Ubuntu, CentOS, SUSE y SUSE Linux Enterprise Server (SLES) y rojo sombrero Enterprise Linux.

**Nota**: el cifrado de disco Linux OS es compatible actualmente en los siguientes CentOS de distribuciones - 7.2 RHEL, Linux 7.2, Ubuntu 16.04

- Todos los recursos (Ex: depósito de clave, almacenamiento de cuenta, VM, etc.) debe pertenecer a la misma región de Azure y de suscripción.

**Nota**: el cifrado de disco Azure requiere que la cámara clave y las máquinas virtuales residen en la misma región de Azure. Configuración de región independiente de provocará error para habilitar la característica de cifrado de disco Azure.

- Para instalar y configurar depósito de Azure clave para el uso del cifrado de disco Azure, consulte la sección **configuración y configuración de Azure clave depósito para uso del cifrado de disco Azure** en la sección *requisitos previos* de este artículo.
- Para instalar y configurar la aplicación de Azure AD en Azure Active directory para el uso del cifrado de disco Azure, consulte la sección **configuración de la aplicación de AD Azure en Azure Active Directory** en la sección *requisitos previos* de este artículo.
- Para instalar y configurar la directiva de acceso de la cámara de clave para la aplicación de AD Azure, consulte la sección de la **Directiva de acceso de depósito de clave de configuración de la aplicación de AD Azure** en la sección *requisitos previos* de este artículo.
- Para preparar un disco duro virtual previamente cifrados de Windows, vea la sección **preparar un disco duro virtual de Windows previamente cifrados** en el apéndice de este artículo.
- Para preparar un VHD Linux previamente cifrados, vea la sección **preparar un disco duro virtual de Linux previamente cifrados** en el apéndice de este artículo.
- Plataforma Windows Azure necesita acceso a las claves de cifrado o información confidencial en depósito de Azure clave de cliente para que estén disponibles en la máquina virtual para arrancar y descifrar el volumen del sistema operativo del equipo virtual. Para conceder permisos a la plataforma Windows Azure para tener acceso al depósito de clave de cliente, propiedad de **enabledForDiskEncryption** debe estar establecida en el depósito de clave de este requisito. Consulte la sección **configuración y configuración de Azure clave depósito para uso del cifrado de disco Azure** en el apéndice de este artículo para obtener más detalles.
- El secreto de depósito clave y la clave cifrado de claves (KEK) URL deben ser una versión. Azure exige esta restricción del control de versiones. Vea ejemplos de secreto válida y la dirección URL de KEK a continuación:
    - Ejemplo de dirección URL secreta válida:   *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
    - Ejemplo de válida KEK KRK:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
- Cifrado de disco Azure no admite los números de puerto que se especificado como parte del secreto depósito clave y direcciones URL KEK. Siga leyendo ejemplos de la dirección URL de depósito de clave compatibles.
    - No aceptado clave depósito URL   *https://contosovault.vault.azure.net:443/información confidencial/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
    - Dirección URL del depósito clave aceptada:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
- Para habilitar el cifrado de disco de Azure característica, las máquinas virtuales de IaaS debe cumplir los siguientes requisitos de configuración de extremo de red: 
    - La VM IaaS puedan conectarse al extremo de Azure Active Directory \[Login.windows.net\] para obtener un token para conectarse a Azure depósito clave
    - La VM IaaS puedan conectarse al extremo de Azure clave depósito para escribir las claves de cifrado en depósito de clave de cliente
    - La VM IaaS puedan conectarse al extremo de almacenamiento de Azure que hospeda el repositorio de Azure extensión y una cuenta de almacenamiento de Azure que hospeda los archivos de disco duro virtual

**Nota:** Si la directiva de seguridad limita el acceso de Azure VM a Internet, puede resolver URI anterior a la que tenga conectividad y configurar una regla específica para permitir la conectividad de salida a las direcciones IP.

- Use la versión más reciente de Azure SDK de PowerShell para configurar el cifrado de disco de Azure. Descargar la última versión de [lanzamiento de PowerShell de Azure](https://github.com/Azure/azure-powershell/releases)

**Nota:** No se admite el cifrado de disco Azure en [Azure PowerShell SDK versión 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Si recibe un error relacionados con el uso de PowerShell de Azure 1.1.0, vea el artículo [Azure disco cifrado Error relacionados con PowerShell Azure 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

- Para ejecutar cualquiera de los comandos de CLI de Azure y asociar con su suscripción de Azure, debe instalar primero Azure CLI versión:
    - Para instalar CLI de Azure y asociar con su suscripción de Azure, consulte [cómo instalar y configurar CLI de Azure](../xplat-cli-install.md)
    - Mediante el uso de la CLI de Azure para Mac, Linux y Windows con el Administrador de recursos de Azure, vea [aquí](azure-cli-arm-commands.md)
- Solución de cifrado de disco Azure usar BitLocker protector de clave externo para máquinas virtuales de Windows IaaS. Si sus máquinas virtuales son la pertenencia a un dominio, no insertar las directivas de grupo que exigir protectores TPM. Consulte [este artículo](https://technet.microsoft.com/library/ee706521) para obtener detalles sobre la directiva de grupo para "Permitir BitLocker sin un TPM compatible".
- El script de PowerShell como requisito previo de Azure disco cifrado para crear la aplicación de Azure AD, crear nuevo depósito clave o depósito de clave existente de instalación y habilitar el cifrado se encuentra [aquí](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).

#### <a name="setup-the-azure-ad-application-in-azure-active-directory"></a>Configuración de la aplicación de Azure AD de Azure Active Directory

Cuando cifrado debe estar habilitado en una VM en ejecución en Azure, cifrado de disco Azure genera y escribe las claves de cifrado a su depósito de clave. Administración de claves de cifrado en depósito de clave necesita autenticación de Azure AD.

Para ello, se debe crear una aplicación de Azure AD. Encontrará instrucciones detalladas para registrar una aplicación pueden encontrarse aquí, en la sección "Obtener una identidad de la aplicación" sección en esta [entrada de blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx).  Esta entrada también contiene un número de útiles ejemplos sobre aprovisionamiento y configuración de la cámara de clave. Para la autenticación, autenticación basada en secreto cliente o puede utilizar autenticación basada en certificados Azure AD de cliente.

##### <a name="client-secret-based-authentication-for-azure-ad"></a>Secreto del cliente en función de autenticación para Azure AD

Las secciones siguientes tienen los pasos necesarios para configurar una autenticación basada en secreto de cliente para Azure AD.

##### <a name="create-a-new-azure-ad-app-using-azure-powershell"></a>Crear una nueva aplicación de Azure AD con PowerShell de Azure

Usar el siguiente cmdlet de PowerShell para crear una nueva aplicación de Azure AD:

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

**Nota:** $azureAdApplication.ApplicationId es Azure AD ClientID y $aadClientSecret es el cliente secreto que se debe usar para habilitar ADE más adelante. Debe proteger el secreto de cliente de Azure AD adecuadamente.


##### <a name="provisioning-the-azure-ad-client-id-and-secret-from-the-azure-classic-deployment-model-portal"></a>El identificador de cliente de Azure AD y el secreto del modelo de implementación de Azure clásica Portal de aprovisionamiento

Azure ID de cliente de AD y secreto también se pueden aprovisionar utiliza el modelo de implementación de Azure clásica Portal en https://manage.windowsazure.com, siga los pasos siguientes para realizar esta tarea:

1. Haga clic en la pestaña de Active Directory, como se muestra en la figura siguiente:

![Cifrado de disco de Azure](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2.Haga clic en Agregar aplicación y escriba el nombre de la aplicación que se muestra a continuación:

![Cifrado de disco de Azure](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3. Haga clic en el botón de flecha y configurar propiedades de la aplicación, tal como se muestra a continuación:

![Cifrado de disco de Azure](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4. Haga clic en la marca de verificación en la esquina inferior izquierda para finalizar. Aparece la página de configuración de la aplicación. Observe que el identificador de cliente de Azure AD se encuentra en la parte inferior de la página como se muestra en la siguiente ilustración.

![Cifrado de disco de Azure](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5. para guardar el secreto de cliente de Azure AD, haga clic en el botón Guardar. Haga clic en el botón y observe el secreto desde el cuadro de texto de teclas, este es el secreto de cliente de Azure AD. Debe proteger el secreto de cliente de Azure AD adecuadamente.

![Cifrado de disco de Azure](./media/azure-security-disk-encryption/disk-encryption-fig7.png)


**Nota:** este flujo anterior no es compatible con el Portal.

##### <a name="use-an-existing-app"></a>Usar una aplicación existente

Para ejecutar los siguientes comandos necesita el módulo Azure AD PowerShell, que puede obtenerse desde [aquí](https://technet.microsoft.com/library/jj151815.aspx).

**Nota:** deben ejecutar los siguientes comandos desde una nueva ventana de PowerShell. No use Azure PowerShell o en la ventana Administrador de recursos de Azure para ejecutar estos comandos. El motivo de esta recomendación es porque estos cmdlets están en el módulo MSOnline o Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Autenticación basada en certificados para Azure AD

> [AZURE.NOTE] Autenticación basada en certificados AAD no es compatible actualmente en máquinas virtuales de Linux.

Las secciones siguientes tienen los pasos necesarios para configurar una autenticación basada en certificados para Azure AD.

##### <a name="create-a-new-azure-ad-app"></a>Crear una nueva aplicación de Azure AD

Ejecutar los cmdlets de PowerShell para crear una nueva aplicación de Azure AD:

**Nota:** Reemplazar `yourpassword` debajo de cadena con la contraseña segura y proteger la contraseña.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Cuando haya terminado este paso, cargar un archivo .pfx en depósito de clave y habilite la directiva de acceso es necesaria para implementar el certificado en una máquina virtual.

##### <a name="use-an-existing-azure-ad-app"></a>Usar una aplicación de Azure AD existente
Si va a configurar la autenticación basada en certificados para una aplicación existente, use los siguientes cmdlets de PowerShell. Asegúrese de que se ejecuten desde una nueva ventana de PowerShell.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Cuando haya terminado este paso, cargar un archivo .pfx en depósito de clave y habilite la directiva de acceso es necesaria para implementar el certificado en una máquina virtual.

##### <a name="upload-a-pfx-file-to-key-vault"></a>Cargar un archivo PFX en depósito de clave
Puede leer esta [entrada de blog](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) para explicación detallada sobre el funcionamiento de este proceso. Sin embargo, los cmdlets de PowerShell siguientes son todo lo que necesita para esta tarea. Asegúrese de que se ejecuten desde la consola de PowerShell de Azure:

**Nota:** Reemplazar `yourpassword` debajo de cadena con la contraseña segura y proteger la contraseña.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-key-vault-to-an-existing-vm"></a>Implementar un certificado en depósito de clave en una máquina virtual existente
Después de terminar de cargar el PFX, realice los siguientes pasos para implementar un certificado en depósito de clave a una máquina virtual existente:

    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName


#### <a name="setting-key-vault-access-policy-for-the-azure-ad-application"></a>Configurar la directiva de acceso de la cámara de clave para la aplicación de AD Azure

La aplicación de Azure AD necesita derechos de acceso a las teclas o información confidencial en la cámara. Usar el cmdlet [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) para conceder permisos a la aplicación con el identificador de cliente (que se genera cuando la aplicación se registró) como el valor del parámetro – principal de servicio. [Esta entrada de blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx) para ver algunos ejemplos en los puede leer. A continuación tiene un ejemplo de cómo llevar a cabo esta tarea a través de PowerShell:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

**Nota**: cifrado de disco Azure, debe configurar las siguientes directivas de acceso a la aplicación cliente de AAD - permisos 'WrapKey' y 'Set'

## <a name="terminology"></a>Terminología

Use la tabla terminología como referencia para comprender algunos de los términos comunes usados por esta tecnología:

| Terminología           | Definición                                                                                                                                                                                                                                   |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure AD                   | Azure AD es [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Cuenta de Azure AD es un requisito previo para autenticar, almacenar y recuperar información confidencial de la cámara de clave.                                                                                                        |
| Depósito clave Azure [AKV] | Depósito de clave Azure es un servicio de administración de claves de cifrado basado en validar FIPS módulos de seguridad de Hardware para proteger sus claves de cifrado y la información confidencial confidencial de forma segura., consulte la documentación de la [Cámara de clave](https://azure.microsoft.com/services/key-vault/) para obtener más detalles.          |
| ARM                   | Administrador de recursos de Azure                                                                                                                                                                                                                       |
| BitLocker             | [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) es que un sector reconoce tecnología de cifrado de volumen de Windows para habilitar el cifrado de disco en máquinas virtuales de Windows IaaS                                                                                                                  |
| BEK                   | Claves de cifrado de BitLocker se usan para cifrar el volumen de inicio del sistema operativo y volúmenes de datos. Las claves de BitLocker son proteger en depósito de clave Azure del cliente como información confidencial.                                                                              |
| CLI                   | [Interfaz de línea de comandos de Azure](../xplat-cli-install.md)                                                                                                                                                                                                                 |
| Cifrado de DM              | [Cifrado de DM](https://en.wikipedia.org/wiki/Dm-crypt) es el subsistema de cifrado de disco transparente basados en Linux para habilitar el cifrado de disco en máquinas virtuales de Linux IaaS                                                                                                                           |
| KEK                   | Clave de cifrado es la clave asimétrica (RSA 2048) que se usan para proteger o ajustar el secreto si lo desea. Puede proporcionar una tecla protegido HSM o protegido por software. Para obtener más detalles, consulte la documentación de [Azure depósito de clave](https://azure.microsoft.com/services/key-vault/) para obtener más detalles |
| PS cmdlets            | [Cmdlets de PowerShell de Azure](powershell-install-configure.md)                                                                                                                                                                                                                                           |

### <a name="setting-and-configuring-azure-key-vault-for-azure-disk-encryption-usage"></a>Uso del cifrado en disco configuración y configuración de Azure clave depósito de Azure

Cifrado de disco Azure protege las claves de cifrado de disco y la información confidencial en su clave de Azure. Siga los pasos de cada una de las siguientes secciones para la instalación depósito de clave para el uso del cifrado de disco Azure.

#### <a name="create-a-new-key-vault"></a>Crear un nuevo depósito clave
Para crear un nuevo depósito de clave, utilice una de las siguientes opciones:

- Usar la plantilla de administrador de recursos "101-crear-KeyVault" encuentra [aquí](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json)
- Usar los [cmdlets de depósito de clave](https://msdn.microsoft.com/library/dn868052.aspx)de PowerShell de Azure.
- Usar el portal de administrador de recursos de Azure.

**Nota:** Si ya tiene una configuración de cámara de clave para la suscripción, vaya a la siguiente sección.

![Depósito de clave de Azure](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="provisioning-a-key-encryption-key-optional"></a>Aprovisionamiento de una clave de cifrado de clave (opcional)

Si desea usar una clave de cifrado de clave (KEK) para un nivel de seguridad adicional para ajustar las claves de cifrado de BitLocker, deberá agregar un KEK a su depósito clave para su uso en el proceso de aprovisionamiento.  Usar el cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868048.aspx) para crear una nueva clave de cifrado de clave en depósito de clave. También puede importar KEK desde la administración de claves local HSM. Para obtener más detalles, consulte la [documentación de depósito de clave](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

La KEK puede agregarse desde el portal de administrador de recursos de Azure también mediante depósito UX clave de Azure.

![Depósito de clave de Azure](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions-to-allow-the-azure-platform-access-to-the-keys-and-secrets"></a>Establecer permisos de clave depósito para permitir el acceso a las claves y la información confidencial en la plataforma Windows Azure

La plataforma de Windows Azure necesita acceso a las claves de cifrado o información confidencial en su clave de Azure para que estén disponibles para la máquina virtual para arrancar y descifrar los volúmenes. Para conceder permisos a la plataforma de Windows Azure para que puede acceder a la cámara de clave, la propiedad de *enabledForDiskEncryption* debe estar establecida en depósito de clave. Puede establecer la propiedad enabledForDiskEncryption en su clave depósito con la cámara clave PS cmdlet:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

También puede establecer la propiedad *enabledForDiskEncryption* visitando https://resources.azure.com. Debe establecer la propiedad *enabledForDiskEncryption* en su cámara clave como se mencionó anteriormente. En caso contrario, se producirá un error en la implementación.

Puede configurar las directivas de acceso para la aplicación de AAD con la UX depósito de clave:

![Depósito de clave de Azure](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Depósito de clave de Azure](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

Asegúrese de que está habilitada depósito de clave para el cifrado de disco en "Directivas de acceso avanzadas":

![Depósito de clave de Azure](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Escenarios de implementación de cifrado de disco y experiencias de usuario

Hay muchos escenarios que puede habilitar el cifrado de disco y los pasos pueden variar según el escenario. Las secciones siguientes cubrirá en más detalle estos escenarios.

### <a name="enable-encryption-on-new-iaas-vms-created-from-the-azure-gallery"></a>Habilitar el cifrado en de nuevo IaaS VM creado a partir de la Galería de Azure

Cifrado de disco puede habilitarse en máquina virtual de Windows IaaS nuevo desde la Galería de Azure en Azure con la plantilla de administrador de recursos publicados [aquí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image). Haga clic en el botón "Implementar a Azure" en la plantilla de Azure tutorial, configuración de cifrado de entrada en el módulo de parámetros y haga clic en Aceptar. Seleccione la suscripción, grupo de recursos, ubicación de grupo de recursos, condiciones legales y contrato y haga clic en el botón Crear para habilitar el cifrado en una nueva VM IaaS.

**Nota:** Esta plantilla crea una nueva VM Windows cifrada con la imagen de la Galería de Windows Server 2012.

Cifrado de disco puede habilitarse en nueva IaaS RedHat Linux 7.2 máquinas virtuales con una matriz de RAID-0 de 200 GB con [esta](https://aka.ms/fde-rhel) plantilla de administrador de recursos. Después de que la plantilla se implementa, compruebe el estado de cifrado VM utilizando la `Get-AzureRmVmDiskEncryptionStatus` cmdlet como se describe en la sección "[unidad de cifrado OS en máquinas virtuales Linux ejecución](#encrypting-os-drive-on-a-running-linux-vm)". Cuando el equipo devuelve estado `VMRestartPending`, reinicie la máquina virtual.

Puede ver los detalles de parámetros de plantilla de administrador de recursos para nueva máquina virtual de escenario de Azure galería con identificador de cliente de AD de Azure en la tabla siguiente:

| Parámetro                        | Descripción|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| adminUserName                 | Nombre de usuario de administración de la máquina virtual                                                                                                                           |
| adminPassword                 | Contraseña de usuario de administración de la máquina virtual                                                                                                                       |
| newStorageAccountName         | Nombre de la cuenta de almacenamiento para almacenar datos VHD y OS                                                                                                             |
| vmSize                        | Tamaño de la máquina virtual. Actualmente, se admite sólo serie estándar A, D y G                                                                                          |
| virtualNetworkName            | Nombre de la VNet a los que debería pertenecer la NIC VM.                                                                                                            |
| subnetName                    | Nombre de la subred en la vNet a los que debería pertenecer la NIC VM                                                                                               |
| AADClientID                   | Identificador de cliente de la aplicación de Azure AD que tiene permisos para escribir información confidencial en depósito de clave                                                                                       |
| AADClientSecret               | Secreto de cliente de la aplicación de Azure AD que tiene permisos para escribir información confidencial en depósito de clave                                                                                   |
| keyVaultURL                   | Dirección URL del depósito clave a qué BitLocker clave debe cargarse en. Puede obtener mediante el cmdlet: (Get-AzureRmKeyVault - VaultName-ResourceGroupName). VaultURI |
| keyEncryptionKeyURL           | Dirección URL de la clave de cifrado de clave que se utiliza para cifrar la clave de BitLocker generada. Esto es opcional.                                                               |
| keyVaultResourceGroup         | Grupo de recursos del depósito clave                                                               |
| vmName                        | Nombre de la máquina virtual de en qué cifrado operación es llevarse a cabo


**Nota:** KeyEncryptionKeyURL es un parámetro opcional. Puede poner su propio KEK en proteger más la clave de cifrado de datos (frase de contraseña secreta) en depósito de clave.

### <a name="enable-encryption-on-new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Habilitar el cifrado en de nuevo IaaS VM creado a partir de las claves de cifrado y disco duro virtual el cifrado de cliente

En este escenario puede habilitar cifrado mediante la plantilla de administrador de recursos, cmdlets de PowerShell o comandos de CLI. Las siguientes secciones se explica en más detalles de la plantilla de administrador de recursos y comandos CLI.

Siga las instrucciones de una de estas secciones para preparar previamente cifradas imágenes que se pueden usar en Azure. Una vez creada la imagen, los pasos de la sección siguiente pueden utilizarse para crear una máquina virtual de Azure cifrados.

- [Preparar un disco duro virtual previamente cifrados de Windows](#preparing-a-pre-encrypted-windows-vhd)
- [Preparar un VHD Linux previamente cifrados](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-resource-manager-template"></a>Usar la plantilla de administrador de recursos

Cifrado de disco puede habilitarse en cliente cifrada con la plantilla de administrador de recursos de disco duro virtual publicados [aquí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm). Haga clic en el botón "Implementar a Azure" en la plantilla de Azure tutorial, configuración de cifrado de entrada en el módulo de parámetros y haga clic en Aceptar. Seleccione la suscripción, grupo de recursos, ubicación de grupo de recursos, condiciones legales y contrato y haga clic en el botón Crear para habilitar el cifrado en nueva VM IaaS.

En la tabla siguiente se describen los detalles de parámetros de plantilla de administrador de recursos de escenario de disco duro virtual de cifrado de cliente:

| Parámetro                        | Descripción|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| newStorageAccountName | Nombre de la cuenta de almacenamiento para almacenar cifrado OS disco duro virtual. Esta cuenta de almacenamiento debe ya creada en el mismo grupo de recursos y la misma ubicación que la máquina virtual                                                     |
| osVhdUri              | Disco duro virtual de URI de sistema operativo de la cuenta de almacenamiento                                                                                                                                                                                      |
| osType                | Tipo de producto de sistema operativo (Windows o Linux)                                                                                                                                                                                         |
| virtualNetworkName    | Nombre de la VNet a los que debería pertenecer la NIC VM. Esto debería haberse ya creado en el mismo grupo de recursos y la misma ubicación que la máquina virtual                                                                     |
| subnetName            | Nombre de la subred en la vNet a los que debería pertenecer la NIC VM                                                                                                                                                     |
| vmSize                | Tamaño de la máquina virtual. Actualmente, se admite sólo serie estándar A, D y G                                                                                                                                                |
| keyVaultResourceID    | Identifica el recurso de depósito clave en el BRAZO de ResourceID. Puede obtener mediante el cmdlet de PowerShell: (Get-AzureRmKeyVault - VaultName &lt;yourKeyVaultName&gt; - ResourceGroupName &lt;yourResourceGroupName&gt;). ResourceId |
| keyVaultSecretUrl     | Dirección URL de la clave de cifrado de disco que se aprovisione en depósito de clave                                                                                                                                                                |
| keyVaultKekUrl        | Dirección URL de la clave de cifrado de clave es cifrar la clave de cifrado de disco generada                                                                                                                                       |
| vmName               | Nombre de la máquina virtual de IaaS   |


#### <a name="using-powershell-cmdlets"></a>Uso de cmdlets de PowerShell

Cifrado de disco puede habilitarse en cliente cifrada disco duro virtual mediante los cmdlets PS publicados [aquí](https://msdn.microsoft.com/library/azure/mt603746.aspx).  

#### <a name="using-cli-commands"></a>Con los comandos CLI

Siga los pasos siguientes para habilitar el cifrado de disco para este escenario con los comandos CLI:

1. Establecer directivas de acceso en depósito de clave:
    - Establecer marca de 'EnabledForDiskEncryption':`azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
    - Establecer permisos de aplicación de Azure AD para escribir información confidencial en KeyVault:`azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]`
2. Para habilitar el cifrado en una máquina virtual de existente o en ejecución, escriba:  *azure vm habilitar disco-cifrado: grupo de recursos <resourceGroupName> --nombre <vmName> identificador de cliente--aad <aadClientId> secreto de cliente--aad <aadClientSecret> --disco cifrado clave depósito url <keyVaultURL> --disco cifrado clave depósito id <keyVaultResourceId> *
3. Obtener el estado de cifrado: *"azure vm mostrar-disco-cifrado-status: grupo de recursos <resourceGroupName> --nombre <vmName> --json"*
4. Para habilitar el cifrado en una nueva máquina virtual de cliente cifrado disco duro virtual, use los siguientes parámetros con el comando "azure vm crear":
    - disco-cifrado-clave-depósito-id < disco cifrado id--clave-depósito->
    - disco cifrado clave-url < disco cifrado-clave url >
    - clave de cifrado clave-depósito-identificador de < clave de cifrado id--clave-depósito->
    - clave de cifrado clave-url < clave de cifrado-clave url >


### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Habilitar el cifrado en existente o ejecutando IaaS Windows VM de Azure

En este escenario puede habilitar cifrado mediante la plantilla de administrador de recursos, cmdlets de PowerShell o comandos de CLI. Las siguientes secciones se explicará en más detalle cómo habilitar mediante los comandos CLI y la plantilla de administrador de recursos.

#### <a name="using-resource-manager-template"></a>Usar la plantilla de administrador de recursos

Cifrado de disco puede habilitarse en VM existente o en ejecución en Windows IaaS en Azure con la plantilla de administrador de recursos publicados [aquí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm). Haga clic en el botón "Implementar a Azure" en la plantilla de Azure tutorial, configuración de cifrado de entrada en el módulo de parámetros y haga clic en Aceptar. Seleccione la suscripción, grupo de recursos, ubicación de grupo de recursos, condiciones legales y contrato y haga clic en el botón Crear para habilitar el cifrado en VM de IaaS existente o en ejecución.

Los detalles de parámetros de plantilla de administrador de recursos de escenario VM existentes o en ejecución con el ID de cliente de Azure AD están disponibles en la tabla siguiente:

| Parámetro                 | Descripción|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AADClientID            | Identificador de cliente de la aplicación de Azure AD que tiene permisos para escribir información confidencial en depósito de clave                                                                                                                                              |
| AADClientSecret         | Secreto de cliente de la aplicación de Azure AD que tiene permisos para escribir información confidencial en depósito de clave                                                                                                                                          |
| keyVaultName | Nombre del depósito clave a qué BitLocker clave debe cargarse en. Puede obtener mediante el cmdlet: (Get-AzureRmKeyVault - ResourceGroupName <yourResourceGroupName>). Vaultname   |
|  keyEncryptionKeyURL   | Dirección URL de la clave de cifrado de clave que se utiliza para cifrar la clave de BitLocker generada. Esto es opcional, si selecciona `nokek` en la lista desplegable de UseExistingKek. Si selecciona `kek` en la lista desplegable UseExistingKek, debe escribir el valor de keyEncryptionKeyURL                                                                                                                        |
| volumeType             | Tipo de volumen en qué cifrado se realiza la operación. Los valores válidos son "OS", "Datos", "Todos"                                                                                                                     |
| sequenceVersion         | Versión de secuencia de la operación de BitLocker. Incrementar el número de versión cada vez que se realiza una operación de cifrado de disco en el misma VM                                                                             |
| vmName                 | Nombre de la máquina virtual de en qué cifrado operación es llevarse a cabo


**Nota:** KeyEncryptionKeyURL es un parámetro opcional. Puede poner su propio KEK en proteger más la clave de cifrado de datos (secreto de cifrado BitLocker) en depósito de clave.

#### <a name="using-powershell-cmdlets"></a>Uso de cmdlets de PowerShell

Consulte el **cifrado de disco explorar Azure con PowerShell Azure** blog post [parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) y [parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) para obtener más información sobre cómo habilitar cifrado utilizando el cifrado de disco de Azure usar cmdlets de PS.

#### <a name="using-cli-commands"></a>Con los comandos CLI

Siga los pasos siguientes para habilitar el cifrado en existente o ejecución de máquinas virtuales de Windows IaaS en Azure con los comandos CLI:

1. Establecer directivas de acceso en depósito de clave:
    - Establecer marca 'EnabledForDiskEncryption': "Directiva de conjunto de azure keyvault--nombre de depósito <keyVaultName> --habilitado para-disco-cifrado verdadero"
    - Establecer permisos de aplicación de Azure AD para escribir información confidencial en KeyVault: "Directiva de conjunto de azure keyvault--nombre de depósito <keyVaultName> --spn <aadClientID> --teclas de permisos [\"todos los\"]--permisos para la información confidencial [\"todos los\"]"
2. Para habilitar el cifrado en una máquina virtual de existente o en ejecución, escriba:  *azure vm habilitar disco-cifrado: grupo de recursos <resourceGroupName> --nombre <vmName> identificador de cliente--aad <aadClientId> secreto de cliente--aad <aadClientSecret> --disco cifrado clave depósito url <keyVaultURL> --disco cifrado clave depósito id <keyVaultResourceId> *
3. Obtener el estado de cifrado: *"azure vm mostrar-disco-cifrado-status: grupo de recursos <resourceGroupName> --nombre <vmName> --json"*
4. Para habilitar el cifrado en una nueva máquina virtual de cliente cifrado disco duro virtual, use los siguientes parámetros con el comando "azure vm crear":
    - disco-cifrado-clave-depósito-id < disco cifrado id--clave-depósito->
    - disco cifrado clave-url < disco cifrado-clave url >
    - clave de cifrado clave-depósito-identificador de < clave de cifrado id--clave-depósito->
    - clave de cifrado clave-url < clave de cifrado-clave url >


### <a name="enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure"></a>Habilitar el cifrado en existente o ejecutando IaaS Linux VM de Azure

Cifrado de disco puede habilitarse en ejecución existente o IaaS Linux VM en Azure con la plantilla de administrador de recursos publicados [aquí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm). Haga clic en el botón "Implementar a Azure" en la plantilla de Azure tutorial, configuración de cifrado de entrada en el módulo de parámetros y haga clic en Aceptar. Seleccione la suscripción, grupo de recursos, ubicación de grupo de recursos, condiciones legales y contrato y haga clic en el botón Crear para habilitar el cifrado en VM de IaaS existente o en ejecución.

En la tabla siguiente se describen los detalles de parámetros de plantilla de administrador de recursos de escenario VM existentes o en ejecución con el ID de cliente de Azure AD:

| Parámetro                 | Descripción|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AADClientID            | Identificador de cliente de la aplicación de Azure AD que tiene permisos para escribir información confidencial en depósito de clave                                                                                                                                              |
| AADClientSecret         | Secreto de cliente de la aplicación de Azure AD que tiene permisos para escribir información confidencial en depósito de clave                                                                                                                                          |
| keyVaultName | Nombre del depósito clave a qué BitLocker clave debe cargarse en. Puede obtener mediante el cmdlet: (Get-AzureRmKeyVault - ResourceGroupName <yourResourceGroupName>). Vaultname   |
|  keyEncryptionKeyURL   | Dirección URL de la clave de cifrado de clave que se utiliza para cifrar la clave de BitLocker generada. Esto es opcional, si selecciona "nokek" en la lista desplegable UseExistingKek. Si selecciona "kek" en la lista desplegable UseExistingKek, deberá especificar el valor de keyEncryptionKeyURL                                                                                                                        |
| volumeType             | Tipo de volumen en qué cifrado se realiza la operación. Los valores compatibles válidos son "OS" o "Todos" (para RHEL 7.2, 7.2 CentOS & Ubuntu 16.04) y "Datos" para todos los demás distros.                                                                                                                      |
| sequenceVersion         | Versión de secuencia de la operación de BitLocker. Incrementar el número de versión cada vez que se realiza una operación de cifrado de disco en el misma VM                                                                             |
| vmName                 | Nombre de la máquina virtual de en qué cifrado operación es llevarse a cabo
| frase de contraseña              | Escriba una frase de contraseña segura como la clave de cifrado de datos                                                                                                                                                                       |                                                                                                                                                                                                                                                      

**Nota:** KeyEncryptionKeyURL es un parámetro opcional. Puede poner su propio KEK en proteger más la clave de cifrado de datos (frase de contraseña secreta) en depósito de clave.

#### <a name="cli-commands"></a>Comandos CLI

Cifrado de disco puede habilitarse en cliente cifrada disco duro virtual con el comando CLI instalado desde [aquí](../xplat-cli-install.md). Siga los pasos siguientes para habilitar el cifrado en existente o en ejecución IaaS Linux VM en Azure con los comandos CLI:

1. Establecer directivas de acceso en depósito de clave:
    - Establecer marca 'EnabledForDiskEncryption': "Directiva de conjunto de azure keyvault--nombre de depósito <keyVaultName> --habilitado para-disco-cifrado verdadero"
    - Establecer permisos de aplicación de Azure AD para escribir información confidencial en KeyVault: "Directiva de conjunto de azure keyvault--nombre de depósito <keyVaultName> --spn <aadClientID> --teclas de permisos [\"todos los\"]--permisos para la información confidencial [\"todos los\"]"
2. Para habilitar el cifrado en una máquina virtual de existente o en ejecución, escriba:  *azure vm habilitar disco-cifrado: grupo de recursos <resourceGroupName> --nombre <vmName> identificador de cliente--aad <aadClientId> secreto de cliente--aad <aadClientSecret> --disco cifrado clave depósito url <keyVaultURL> --disco cifrado clave depósito id <keyVaultResourceId> *
3. Obtener el estado de cifrado: "azure vm mostrar-disco-cifrado-status: grupo de recursos <resourceGroupName> --nombre <vmName> --json"
4. Para habilitar el cifrado en una nueva máquina virtual de cliente cifrado disco duro virtual, use los siguientes parámetros con el comando "azure vm crear".
    - *disco-cifrado-clave-depósito-id < disco cifrado id--clave-depósito->*
    - *disco cifrado clave-url < disco cifrado-clave url >*
    - *clave de cifrado clave-depósito-identificador de < clave de cifrado id--clave-depósito->*
    - *clave de cifrado clave-url < clave de cifrado-clave url >*

### <a name="get-encryption-status-of-an-encrypted-iaas-vm"></a>Obtener el estado de cifrado de una VM IaaS cifrada

Puede obtener el estado de cifrado con el portal de administrador de recursos de Azure, [cmdlets de PowerShell](https://msdn.microsoft.com/library/azure/mt622700.aspx) o comandos de CLI. Las siguientes secciones se explica cómo usar el portal de Azure y los comandos CLI para conocer el estado de cifrado.

#### <a name="get-encryption-status-of-an-encrypted-windows-vm-using-azure-resource-manager-portal"></a>Obtener el estado de cifrado de una máquina virtual de Windows cifrada con el portal de administrador de recursos de Azure

Puede obtener el estado de cifrado de VM IaaS desde el portal de administrador de recursos de Azure. Iniciar sesión en el portal de Azure en https://portal.azure.com/, haga clic en el vínculo de máquinas virtuales en el menú de la izquierda para ver la vista de resumen de las máquinas virtuales de su suscripción. Puede filtrar la vista de máquinas virtuales de Windows seleccionando el nombre de la suscripción en la lista desplegable de la suscripción. Haga clic en columnas que se encuentra en la parte superior del menú de la página de máquinas virtuales de Windows. Seleccionar columna de cifrado de disco en el módulo de columna disponibles y haga clic en actualizar. Verá la columna de cifrado de disco que muestra el estado de cifrado "Habilitado" o "No habilitada" para cada VM tal como se muestra en la figura siguiente.

![Microsoft Antimalware en Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-encryption-status-of-an-encrypted-windowslinux-iaas-vm-using-disk-encryption-ps-cmdlet"></a>Obtener el estado de cifrado de una VM de IaaS (Windows o Linux) cifrada con cifrado de disco PS Cmdlet
Puede obtener el estado de cifrado de VM IaaS de disco cifrado PS cmdlet "Get-AzureRmVMDiskEncryptionStatus". Para obtener la configuración de cifrado de la máquina virtual, escriba en la sesión de PowerShell de Azure:

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName
    
    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

Puede inspeccionar el resultado de Get-AzureRmVMDiskEncryptionStatus para el cifrado de clave direcciones URL.
    
    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMNam
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey
    
    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

El valor de configuración OSVolumeEncrypted y DataVolumesEncrypted se establece en "Cifrado" que muestra que tanto los volúmenes se cifran utilizando cifrado de disco Azure. Consulte el **cifrado de disco explorar Azure con PowerShell Azure** blog post [parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) y [parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) para obtener más información sobre cómo habilitar cifrado utilizando el cifrado de disco de Azure usar cmdlets de PS.

**Nota**: en Linux VM, la `Get-AzureRmVMDiskEncryptionStatus` cmdlet tarda minutos de 3 y 4 para informar del estado de cifrado.

#### <a name="get-encryption-status-of-the-iaas-vm-from-disk-encryption-cli-command"></a>Obtener el estado de cifrado de VM IaaS de cifrado de disco comando CLI

Puede obtener el estado de cifrado de VM IaaS de cifrado de disco del comando CLI *azure vm mostrar-disco--estado de cifrado*. Para obtener la configuración de cifrado de la máquina virtual, escriba en la sesión de Azure CLI:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Deshabilitar el cifrado ejecutando Windows IaaS VM

Puede deshabilitar el cifrado en un ejecutando Windows o Linux IaaS VM a través de la plantilla de administrador de recursos de cifrado de disco Azure o PS cmdlets y especifica la configuración de descifrado.


##### <a name="windows-vm"></a>VM de Windows

El paso de cifrado de deshabilitar deshabilita el cifrado de volumen de sistema operativo o datos o ambos en la máquina virtual está ejecutando IaaS de Windows. No se puede deshabilitar el volumen del sistema operativo y deje el volumen de datos cifrado. Cuando se realiza el paso de cifrado de deshabilitar, modelo de implementación clásica Azure actualiza el modelo de servicio VM y la máquina virtual IaaS de Windows se marca descifrada. El contenido de la máquina virtual no se cifra al resto ya. No eliminar el cifrado de deshabilitar el depósito de clave de cliente y el material de clave de cifrado, las claves de cifrado de BitLocker para Windows y la frase de contraseña para Linux. 

##### <a name="linux-vm"></a>Máquina virtual Linux

El paso de cifrado de deshabilitar deshabilita el cifrado del volumen de datos en la máquina virtual IaaS de ejecución Linux

**Nota**: deshabilitar el cifrado en el disco de sistema operativo no está permitido en máquinas virtuales de Linux.

##### <a name="disable-encryption-on-existingrunning-iaas-vm-in-azure-using-resource-manager-template"></a>Deshabilitar el cifrado en ejecución existente o IaaS VM en Azure con plantilla de administrador de recursos

Cifrado de disco puede deshabilitarse ejecutando Windows IaaS VM con la plantilla de administrador de recursos publicados [aquí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm). Haga clic en el botón "Implementar a Azure" en la plantilla de Azure tutorial, configuración de entrada descifrado en el módulo de parámetros y haga clic en Aceptar. Seleccione la suscripción, grupo de recursos, ubicación de grupo de recursos, condiciones legales y contrato y haga clic en el botón Crear para habilitar el cifrado en una nueva VM IaaS.

Para Linux VM, [esta](https://aka.ms/decrypt-linuxvm) plantilla puede usarse para deshabilitar el cifrado.

Detalles de parámetros de la plantilla de administrador de recursos para deshabilitar el cifrado de ejecución de IaaS VM:

| vmName         | Nombre de la máquina virtual de en qué cifrado operación es llevarse a cabo                                                                                                                                                                       |
|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| volumeType     | Tipo de volumen en qué descifrado se realiza la operación. Los valores válidos son "OS", "Datos", "Todos". **Nota:** No se puede deshabilitar cifrado en ejecutando el volumen de inicio o sistema operativo Windows IaaS VM sin deshabilitar el cifrado de volumen "Datos". **Nota**: deshabilitar el cifrado en el disco de sistema operativo no está permitido en máquinas virtuales de Linux. |
| sequenceVersion | Versión de secuencia de la operación de BitLocker. Incrementar el número de versión cada vez que se realiza una operación de descifrado del disco en el misma VM                                                                                          |

##### <a name="disable-encryption-on-existingrunning-iaas-vm-in-azure-using-ps-cmdlet"></a>Deshabilitar el cifrado en existente o en ejecución IaaS VM en Azure con PS cmdlet

Para deshabilitar mediante el cmdlet PS, [Deshabilitar AzureRmVMDiskEncryption](https://msdn.microsoft.com/library/azure/mt715776.aspx) cmdlet deshabilita cifrado en una infraestructura como una máquina virtual de servicio (IaaS). Este cmdlet es compatible con Windows y máquinas virtuales de Linux. Este cmdlet instala una extensión de la máquina virtual para deshabilitar el cifrado. Si no se especifica el parámetro de nombre, se crea una extensión con el nombre predeterminado "AzureDiskEncryption para Windows máquinas virtuales". 

En máquinas virtuales de Linux, se usa la extensión "AzureDiskEncryptionForLinux".

**Nota**: este cmdlet reinicia la máquina virtual. 

## <a name="appendix"></a>Apéndice

### <a name="connect-to-your-subscription"></a>Conectarse a su suscripción

Asegúrese de que revise la sección *requisitos previos* de este documento antes de continuar. Después de asegurarse de que se han entregado todos los requisitos previos, siga los pasos siguientes para conectarse a su suscripción:

1 iniciar una sesión de Azure PowerShell e inicie sesión en su cuenta de Azure con el siguiente comando:

    Login-AzureRmAccount

2.haga si tiene varias suscripciones y desea especificar una específica para usar, escriba lo siguiente para ver las suscripciones para su cuenta:

    Get-AzureRmSubscription

3.en para especificar la suscripción que desea utilizar, escriba:

    Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>

4 para comprobar la suscripción configurada es correcta, escriba:

    Get-AzureRmSubscription

5. para confirmar que están instalados los cmdlets de cifrado de disco de Azure, escriba:

    Get-command *diskencryption*

6 debe ver la debajo de confirmación de la instalación de Azure disco cifrado PowerShell de salida:

    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     

### <a name="preparing-a-pre-encrypted-windows-vhd"></a>Preparar un disco duro virtual previamente cifrados de Windows
Las secciones siguientes son necesarias para preparar un disco duro virtual de Windows previamente cifrados implementación como un disco duro virtual cifrado en Azure IaaS. Los pasos se usan para preparar e inicie una nueva windows VM (disco duro virtual) en Hyper-V o Azure.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Actualizar la directiva de grupo para permitir que no son TPM para la protección del sistema operativo
Necesita configurar la configuración de directiva de grupo de BitLocker denominada unidad BitLocker, ubicado en la directiva de equipo Local \Computer Configuration\Administrative administrativas\Componentes componentes. Cambiar esta configuración para: *Unidades de sistema operativo - requerir autenticación adicional al inicio: Permitir BitLocker sin un TPM compatible* , tal como se muestra en la figura siguiente:

![Microsoft Antimalware en Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Instalar componentes de la característica de BitLocker
Para Windows Server 2012 y sobre el uso del debajo de comando:

    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

Para usar Windows Server 2008 R2 la debajo de comando:

    ServerManagerCmd -install BitLockers

#### <a name="prepare-os-volume-for-bitlocker-using-bdehdcfg"></a>Preparar el volumen del sistema operativo para el uso de BitLocker`bdehdcfg`

Ejecute el comando siguiente para comprimir la partición del sistema operativo y preparar el equipo para BitLocker.

    bdehdcfg -target c: shrink -quiet

#### <a name="using-bitlocker-to-protect-the-os-volume"></a>Usar BitLocker para proteger el volumen del sistema operativo
Usar el [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) comando para habilitar el cifrado en el volumen de inicio con un protector de clave externo y colocar la clave externa (archivo .bek) en el volumen o una unidad externa. Cifrado se habilitarán en el volumen de inicio o del sistema después del siguiente reinicio.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**Nota:** La máquina virtual debe estar preparado con un disco duro virtual de datos independiente o recurso para obtener la clave externa con BitLocker.

#### <a name="encrypting-os-drive-on-a-running-linux-vm"></a>Cifrado de unidad de sistema operativo en una VM Linux en ejecución

Cifrado de unidad de sistema operativo en una VM en ejecución Linux es compatible con la distros siguientes:

- RHEL 7.2
- CentOS 7.2
- Ubuntu 16.04

Requisitos previos para el cifrado de disco del sistema operativo:

- VM debe crearse desde la Galería de Azure en el portal de administrador de recursos de Azure.
- Azure VM con al menos 4 GB de memoria RAM (se recomienda tamaño es 7 GB).
- (Para RHEL y CentOS) SELinux debe estar [deshabilitada](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) en la máquina virtual. Debe reiniciar la máquina virtual al menos una vez después de deshabilitar SELinux.


##### <a name="steps"></a>Pasos

1. crear una máquina virtual usando uno de los distros especificada anteriormente.

Para CentOS 7.2, el cifrado de disco del sistema operativo es compatible a través de una imagen especial. Para usar esta imagen, especifique "7.2n" como la Sku al crear la máquina virtual:

    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"

2.Haga configurar la máquina virtual según sus necesidades. Si va a cifrar todos los datos que necesitan estar especificada y montaje de/etc/fstab unidades unidades de (OS + datos).

> [AZURE.NOTE] Debe utilizar UUID =... para especificar datos unidades en/etcetera/fstab en lugar de especificar el nombre del dispositivo bloque, por ejemplo, / dev/sdb1. Durante el cifrado se cambiará el orden de las unidades en la máquina virtual. Si su máquina virtual se basa en un orden específico de dispositivos de bloque se producirá un error de montaje después del cifrado.

3.en sesiones de cierre de sesión SSH.

4 para cifrar el sistema operativo, especifique volumeType como "todos" o "OS" al [Habilitar el cifrado](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

> [AZURE.NOTE] Todos los procesos de espacio de usuario que no se ejecutan como `systemd` servicios se eliminará con un `SIGKILL`. Deberá reiniciar la máquina virtual. Planee en tiempo de inactividad de la máquina virtual cuando OS habilitar disco cifrado en una VM en ejecución.

5. periódicamente supervisar el progreso de cifrado siguiendo las instrucciones en la [sección siguiente](#monitoring-os-encryption-progress).

6. una vez Get-AzureRmVmDiskEncryptionStatus muestra "VMRestartPending", reinicie la máquina virtual alguno iniciando sesión en él o a través de PowerShell/Portal/CLI.

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName
    
    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, please reboot the VM

Se recomienda guardar [Diagnósticos de inicio](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/) de la máquina virtual *antes de* reiniciar.

#### <a name="monitoring-os-encryption-progress"></a>Supervisar el progreso de cifrado de sistema operativo

Hay tres maneras de supervisar el progreso de cifrado de sistema operativo.

1 use el cmdlet Get-AzureRmVmDiskEncryptionStatus e inspeccionar el campo ProgressMessage: 
 
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started

Una vez que la máquina virtual alcanza "OS disco cifrado iniciado" tardará aproximadamente 40-50 minutos en un almacenamiento Premium copia VM.

Debido a [problemas #388](https://github.com/Azure/WALinuxAgent/issues/388) en WALinuxAgent, `OsVolumeEncrypted` y `DataVolumesEncrypted` mostrarse como `Unknown` en algunos distros. Con WALinuxAgent versión 2.1.5 y encima de esto se solucionará automáticamente. En caso de que ve `Unknown` en el resultado, puede comprobar el estado de cifrado de disco mediante el Visor de recursos de Azure.

Vaya a [Azure Visor de recursos](https://resources.azure.com/)y, a continuación, expanda esta jerarquía en el panel de selección a la izquierda:

~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

En la InstanceView, desplácese hacia abajo para ver el estado de cifrado de las unidades.

![Vista de instancia VM](./media/azure-security-disk-encryption/vm-instanceview.png)

2.Haga mire [Diagnósticos de inicio](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/). Mensajes de extensión ADE deberán ir precedidos `[AzureDiskEncryption]`.

3.en Inicio de sesión en la máquina virtual a través de SSH y obtener el registro de la extensión de

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

No se recomienda para iniciar sesión en la máquina virtual mientras OS cifrado está en curso. Por lo tanto, se deben copiar los registros solo cuando han fallado otros dos métodos.

#### <a name="preparing-a-pre-encrypted-linux-vhd"></a>Preparar un VHD Linux previamente cifrados

##### <a name="ubuntu-16"></a>Ubuntu 16

###### <a name="configure-encryption-during-distro-install"></a>Configurar el cifrado durante la instalación de distro

1 cuando seleccione la "Configurar volúmenes encriptados" partición de discos.

![Configuración de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2.Haga crear una unidad de arranque independiente que no debe estar cifrada. Cifrar la unidad raíz.

![Configuración de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3.en proporcionan una frase de contraseña. Esta es la contraseña que se cargará en KeyVault.

![Configuración de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4 finalización particiones.

![Configuración de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. al iniciar la máquina virtual, se le pedirá para una frase de contraseña. Utilice la contraseña suministrada en el paso 3.

![Configuración de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. preparar VM para cargar en Azure con [estas instrucciones](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). No se ejecutan el último paso (baja la máquina virtual) todavía.

###### <a name="configure-encryption-to-work-with-azure"></a>Configurar el cifrado para que funcione con Azure

1. crear un archivo en /usr/local/sbin/azure_crypt_key.sh, con el contenido de la siguiente secuencia de comandos. Preste atención a la KeyFileName, porque es el nombre de archivo de frase de contraseña con Azure.

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do
        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi


2.haga cambiar la configuración de cifrado en */etc/crypttab*. Debe ser similar a esta:

    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3.en si edita la *azure_crypt_key.sh* en Windows y copiado a Linux, no olvide ejecutar *dos2unix /usr/local/sbin/azure_crypt_key.sh*.

4 Agregar permisos ejecutables a la secuencia de comandos:

    chmod +x /usr/local/sbin/azure_crypt_key.sh

4. editar */etc/initramfs-tools/modules* agregando líneas:

    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1

5. ejecute `update-initramfs -u -k all` para actualizar la initramfs para realizar la `keyscript` surta efecto.
6. ahora puede anular la máquina virtual.

![Configuración de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

7. seguir siguiente paso y [cargar su disco duro virtual](#upload-encrypted-vhd-to-an-azure-storage-account) en Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2

###### <a name="configure-encryption-during-distro-install"></a>Configurar el cifrado durante la instalación de distro

1. Seleccione "Grupo de volumen cifrar" al crear particiones en discos. Proporcionar una frase de contraseña. Esta es la contraseña que se cargará en KeyVault.

![Configuración de openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2.Haga arranque la máquina virtual con la frase de contraseña.

![Configuración de openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3.en preparar VM para cargar en Azure con [estas instrucciones](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). No se ejecutan el último paso (baja la máquina virtual) todavía.

###### <a name="configure-encryption-to-work-with-azure"></a>Configurar el cifrado para que funcione con Azure

1. editar el /etc/dracut.conf y agregue la línea siguiente:

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

2.Haga comentar estas líneas al final del archivo "/ usr/lib/dracut/modules.d/90crypt/module-setup.sh":

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3.en la siguiente línea al principio del archivo de datos anexados "/ usr/lib/dracut/modules.d/90crypt/parse-crypt.sh"

    DRACUT_SYSTEMD=0

cambiar todas las apariciones de

    if [ -z "$DRACUT_SYSTEMD" ]; then

Para

    if [ 1 ]; then

4. Editar /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh y anexa después el "dispositivo de abrir LUKS #"

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5. ejecute la "/ usr/sbin/dracut - f - v" para actualizar la initrd.

6. ahora puede anular la máquina virtual y [cargar su disco duro virtual](#upload-encrypted-vhd-to-an-azure-storage-account) en Azure.

##### <a name="centos-7"></a>CentOS 7

###### <a name="configure-encryption-during-distro-install"></a>Configurar el cifrado durante la instalación de distro

1 Seleccione "cifrar mis datos" al crear particiones en discos.

![Configuración de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2.Haga Asegúrese de que "Cifrar" está activada para partición raíz.

![Configuración de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3.en proporcionan una frase de contraseña. Esta es la contraseña que se cargará en KeyVault.

![Configuración de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4.aparecerá al iniciar la máquina virtual, se le pedirá para una frase de contraseña. Utilice la contraseña suministrada en el paso 3.

![Configuración de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. preparar VM para cargar en Azure con [estas instrucciones](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). No se ejecutan el último paso (baja la máquina virtual) todavía.

6. ahora puede anular la máquina virtual y [cargar su disco duro virtual](#upload-encrypted-vhd-to-an-azure-storage-account) en Azure.

###### <a name="configure-encryption-to-work-with-azure"></a>Configurar el cifrado para que funcione con Azure

1. editar el /etc/dracut.conf y agregue la línea siguiente:

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

2.Haga comentar estas líneas al final del archivo "/ usr/lib/dracut/modules.d/90crypt/module-setup.sh":

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3.en la siguiente línea al principio del archivo de datos anexados "/ usr/lib/dracut/modules.d/90crypt/parse-crypt.sh"

    DRACUT_SYSTEMD=0

cambiar todas las apariciones de

    if [ -z "$DRACUT_SYSTEMD" ]; then

Para

    if [ 1 ]; then

4. Editar /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh y anexa después el "dispositivo de abrir LUKS #"

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5. ejecute la "/ usr/sbin/dracut - f - v" para actualizar la initrd.

![Configuración de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Cargar el cifrado de disco duro virtual a una cuenta de almacenamiento de Azure
Después de habilita BitLocker cifrado pr DM cifrado cifrado, el disco duro de virtual cifrado local debe cargarse a su cuenta de almacenamiento.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-disk-encryption-secret-for-the-pre-encrypted-vm-to-key-vault"></a>Cargar secreto cifrado de disco de la VM previamente cifrada en depósito de clave
El secreto de cifrado de disco obtenido previamente debe cargarse como un secreto en depósito de clave. Depósito de clave debe tener permisos habilitados para su cliente AAD así como el cifrado de disco.


    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $KeyVault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Secreto de cifrado de disco no cifrado con un KEK
Usar [Conjunto AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) proporcionando el secreto en depósito de clave. En caso de una máquina virtual de Windows, el archivo bek se codifica como una cadena de base 64 y luego cargado en depósito clave mediante el cmdlet Set-AzureKeyVaultSecret. Para Linux, la frase de contraseña se codifica como una cadena de base 64 y, a continuación, cargar en depósito de clave. Además, asegúrese de que las etiquetas siguientes están establecidas al crear el secreto en depósito de clave.

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

La `$secretUrl` se utilizarán en el paso siguiente para [asociar el disco OS sin usar KEK](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Secreto de cifrado de disco cifrado con un KEK

El secreto, opcionalmente, se puede cifrar con una clave de cifrado de clave antes de cargar en depósito de clave. Use el ajuste [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) para cifrar el secreto mediante la clave de cifrado de clave. El resultado de esta operación de ajuste es una cadena de URL codificada en base 64 que, a continuación, se carga como un secreto mediante el cmdlet [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) .

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

La `$KeyEncryptionKey` y `$secretUrl` se utilizarán en el paso siguiente para [asociar el disco de sistema operativo mediante KEK](#using-a-kek).

### <a name="specify-secret-url-when-attaching-os-disk"></a>Especificar dirección URL secreto al asociar OS disco

#### <a name="without-using-a-kek"></a>Sin usar un KEK

Al adjuntar el disco de sistema operativo, `$secretUrl` debe pasar. En la sección ["secreto cifrado de disco no cifrado con un KEK"](#disk-encryption-secret-not-encrypted-with-a-kek)se ha generado la dirección URL.

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>Usar un KEK

Al adjuntar el disco de sistema operativo, `$KeyEncryptionKey` y `$secretUrl` necesita pasar. En la sección ["secreto cifrado de disco cifrados con un KEK"](#disk-encryption-secret-encrypted-with-a-kek)se ha generado la dirección URL.

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="download-this-guide"></a>Descargue esta guía
Puede descargar a esta guía desde la [Galería de TechNet](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).


## <a name="for-more-information"></a>Para obtener más información
[Explorar el disco Azure cifrado con PowerShell Azure](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)

[Explorar el disco Azure cifrado con PowerShell Azure - parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
