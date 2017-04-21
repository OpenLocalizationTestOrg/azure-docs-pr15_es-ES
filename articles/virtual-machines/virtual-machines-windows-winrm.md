<properties
    pageTitle="Configurar el acceso de WinRM para máquinas virtuales de Windows en el Administrador de recursos de Azure | Microsoft Azure"
    description="Cómo configurar el acceso de WinRM para su uso con una máquina virtual de administrador de recursos de Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/16/2016"
    ms.author="singhkay"/>

# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Configurar el acceso de WinRM para máquinas virtuales de Windows en el Administrador de recursos de Azure

## <a name="winrm-in-azure-service-management-vs-azure-resource-manager"></a>WinRM en administración del servicio de Azure vs Administrador de recursos de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica

* Para obtener información general del Administrador de recursos de Azure, consulte este [artículo](../azure-resource-manager/resource-group-overview.md)
* Para las diferencias entre la administración de servicios de Azure y Azure Administrador de recursos, consulte este [artículo](../resource-manager-deployment-model.md)

La diferencia clave en la configuración de la configuración de WinRM entre las dos pilas es cómo se instala el certificado en la máquina virtual. En la pila de administrador de recursos de Azure, los certificados son modelar como recursos administrados por el proveedor de recursos de clave de cámara. Por lo tanto, el usuario debe proporcionar su propio certificado y cárguelo en un depósito de clave antes de usarla en una máquina virtual.

Estos son los pasos que debe seguir para configurar una máquina virtual con conectividad WinRM

1. Crear un depósito clave
2. Crear un certificado autofirmado
3. Cargar el certificado autofirmado en depósito de clave
4. Obtener la dirección URL para el certificado autofirmado en depósito de clave
5. Hacer referencia a la dirección URL de certificados autofirmado al crear una máquina virtual

## <a name="step-1-create-a-key-vault"></a>Paso 1: Crear un depósito clave

Puede usar la debajo de comando para crear el depósito de clave

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Paso 2: Crear un certificado autofirmado
Puede crear un certificado autofirmado mediante esta secuencia de comandos de PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Paso 3: Cargar el certificado autofirmado en el depósito de clave

Antes de cargar el certificado en el depósito de clave que creó en el paso 1, debe convertir en un formato que comprenda el proveedor de recursos de Microsoft.Compute. La debajo de PowerShell script permitirá que hacerlo

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Paso 4: Obtener la dirección URL para el certificado autofirmado en depósito de clave

El proveedor de recursos de Microsoft.Compute necesita una dirección URL para el secreto dentro de la cámara de clave durante el aprovisionamiento de la máquina virtual. Esto permite que el proveedor de recursos de Microsoft.Compute descargar el secreto y crear el certificado equivalente en la máquina virtual.

>[AZURE.NOTE]La dirección URL del secreto debe incluir también la versión. Una dirección URL de ejemplo es similar a continuación https://contosovault.vault.azure.net:443/información confidencial/contososecret/01h9db0df2cd4300a20ence585a6s7ve


#### <a name="templates"></a>Plantillas

Puede obtener el vínculo a la dirección URL en la plantilla mediante la debajo de código

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell

Puede obtener esta dirección URL mediante la debajo de comandos de PowerShell

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Paso 5: Hacer referencia a la dirección URL de certificados firmados automáticamente al crear una máquina virtual

#### <a name="azure-resource-manager-templates"></a>Plantillas de administrador de recursos de Azure

Al crear una máquina virtual a través de plantillas, el certificado obtiene al que hace referencia en la sección de información confidencial y la sección winRM como sigue:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Una plantilla de ejemplo para las respuestas anteriores puede encontrarse aquí en [201 vm winrm keyvault windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Código fuente de esta plantilla se puede encontrar en [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell

    $vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Paso 6: Conectarse a la máquina virtual
Antes de poder conectarse a la máquina virtual debe asegurarse de que su equipo está configurado para la administración remota de WinRM. Iniciar PowerShell como administrador y ejecutar el debajo de comando para asegurarse de que está configurado.

    Enable-PSRemoting -Force

>[AZURE.NOTE] Debe asegurarse de que se está ejecutando el servicio WinRM si lo anterior no funciona. Puede hacer que el uso`Get-Service WinRM`

Una vez realizada la instalación, puede conectarse a la máquina virtual mediante la debajo de comando

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate