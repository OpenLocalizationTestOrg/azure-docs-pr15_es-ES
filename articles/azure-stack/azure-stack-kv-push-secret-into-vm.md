<properties
    pageTitle="Implementar una máquina virtual con un certificado mediante depósito de clave de pila de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo implementar una máquina virtual e insertar un certificado de la cámara de clave de pila de Azure"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="create-vms-and-include-certificates-retrieved-from-key-vault"></a>Crear máquinas virtuales e incluir los certificados que se recuperen de la cámara de clave

En la pila de Azure, máquinas virtuales se implementan a través del Administrador de recursos de Azure y ahora puede almacenar certificados en depósito de clave de pila de Azure. Pila de Azure (proveedor de recursos de Microsoft.Compute sea específico) inserta ellos en sus máquinas virtuales cuando se implementan las máquinas virtuales. Certificados pueden utilizarse en muchos casos, incluidos SSL, el cifrado y la autenticación basada en certificados.

Con este método, puede mantener el certificado seguro. Ahora no está en la imagen de VM o en archivos de configuración de la aplicación o alguna otra ubicación no segura. Estableciendo la directiva de acceso adecuado para el depósito clave, también puede controlar quién obtiene acceso a su certificado. Otra ventaja es que puede administrar todos los certificados en un solo lugar en depósito de clave de pila de Azure.

Aquí tiene una descripción general del proceso:

-   Necesita un certificado en el formato .pfx.

-   Crear un depósito clave (mediante una plantilla o la siguiente secuencia de comandos).

-   Asegúrese de que ha activado el modificador EnabledForDeployment.

-   Cargar el certificado como secreto.

## <a name="deploying-vms"></a>Implementar máquinas virtuales

Esta secuencia de comandos de ejemplo crea un depósito clave y, a continuación, almacena un certificado almacenado en el archivo .pfx en un directorio local, en el depósito clave como secreto.

    $vaultName = "contosovault"
    $resourceGroup = "contosovaultrg"
    $location = "local"
    $secretName = "servicecert"
    $fileName = "keyvault.pfx"
    $certPassword = "abcd1234"

    # =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

    $fileContentBytes = get-content \$fileName -Encoding Byte

    $fileContentEncoded =
    [System.Convert\]::ToBase64String(\$fileContentBytes)
    $jsonObject = @"
    {
    "data": "\$filecontentencoded",
    "dataType" :"pfx",
    "password": "\$certPassword"
    }
    @$jsonObjectBytes = [System.Text.Encoding\]::UTF8.GetBytes(\$jsonObject)
    $jsonEncoded = \[System.Convert\]::ToBase64String(\$jsonObjectBytes)
    Switch-AzureMode -Name AzureResourceManager
    New-AzureResourceGroup -Name \$resourceGroup -Location \$location
    New-AzureKeyVault -VaultName \$vaultName -ResourceGroupName
    $resourceGroup -Location \$location -sku standard -EnabledForDeployment
    $secret = ConvertTo-SecureString -String \$jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName \$vaultName -Name \$secretName -SecretValue \$secret

La primera parte de la secuencia de comandos lee el archivo .pfx y almacena como un JSON objeto con la codificación de base64 contenido del archivo. A continuación, el objeto JSON también está codificado de base 64.

A continuación, crea un nuevo grupo de recursos y, a continuación, crea un depósito clave. Tenga en cuenta el último parámetro con el comando nuevo AzureKeyVault, '-EnabledForDeployment', que proporciona acceso a Azure (específicamente para el proveedor de recursos Microsoft.Compute) para leer la información confidencial de la clave depósito para implementaciones.

El último comando simplemente almacena el objeto JSON base 64 codificado en depósito de clave como secreto.

Esto es el resultado de ejemplo de la secuencia de comandos anterior:

    VERBOSE:  – Created resource group 'contosovaultrg' in
    location
    'eastus'
    ResourceGroupName : contosovaultrg
    Location          : eastus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions NotActions
                        ======= ==========
                        \*
    ResourceId        :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56/re
    sourceGroups/contosovaultrg
    VaultUri             : https://contosovault.vault.azure.net
    TenantId             : xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
    TenantName           : xxxxxxxx
    Sku                  : standard
    EnabledForDeployment : True
    AccessPolicies       : {xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47}
    AccessPoliciesText   :
                           Tenant ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
                           Object ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-b092cebf0c80
                           Application ID         :
                           Display Name           : Derick Developer  (derick@contoso.com)
                           Permissions to Keys    : get, create, delete,
                           list, update, import, backup, restore
                           Permissions to Secrets : all
    OriginalVault        : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId           :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56                 
    /resourceGroups/contosovaultrg/providers/Microsoft.KeyV
                           ault/vaults/contosovault
    VaultName            : contosovault
    ResourceGroupName    : contosovaultrg
    Location             : eastus
    Tags                 : {}
    TagsTable            :
    SecretValue     : System.Security.SecureString
    SecretValueText :
    ew0KImRhdGEiOiAiTUlJSkN3SUJBekNDQ01jR0NTcUdTSWIzRFFFSEFh
    Q0NDTGdFZ2dpME1JSUlzRENDQmdnR0NTcUdTSWIzRFFFSEFhQ0NCZmtF           
    Z2dYMU1JSUY4VENDQmUwR0N5cUdTSWIzRFFFTUNnRUNvSUlFL2pDQ0JQ
    &lt;&lt;&lt; Output truncated… &gt;&gt;&gt;
    Attributes      :
    Microsoft.Azure.Commands.KeyVault.Models.SecretAttributes
    VaultName       : contosovault
    Name            : servicecert
    Version         : e3391a126b65414f93f6f9806743a1f7
    Id              :
    https://contosovault.vault.azure.net:443/secrets/servicecert
                      /e3391a126b65414f93f6f9806743a1f7

Ahora estamos listos implementar una plantilla de máquina virtual. Tenga en cuenta la URI del secreto del resultado (como se resalta en el resultado anterior en verde).

Necesitará una plantilla que se encuentra aquí. Los parámetros de interés especial (además de los parámetros VM habituales) son el nombre de la cámara, el grupo de recursos de cámara y el secreto URI. Por supuesto, también puede descargar desde GitHub y modificar según sea necesario.

Cuando se implementa esta VM, Azure inserta el certificado en la máquina virtual.
En Windows, se agregarán certificados en el archivo .pfx con la clave privada no puede exportar. El certificado se agrega a la ubicación de certificado LocalMachine, con el almacén de certificados que proporciona el usuario. En Linux, el archivo de certificado se coloca en el directorio /var/lib/waagent, con el nombre de archivo &lt;UppercaseThumbprint&gt;.crt para la X509 archivo de certificado y &lt;UppercaseThumbprint&gt;.prv para la clave privada.
Ambos archivos están .pem con formato.

Normalmente, la aplicación busca el certificado utilizando la huella digital y no necesita modificación.

## <a name="retiring-certificates"></a>Retirada de certificados


En la sección anterior, le mostramos cómo insertar un nuevo certificado a sus máquinas virtuales existentes. Pero el certificado antiguo aún está en la máquina virtual y no se puede quitar. Para una mayor seguridad, puede cambiar el atributo de secreto antiguo a 'Desactivado', para que incluso si una plantilla antigua intenta crear una máquina virtual con esta versión anterior del certificado, lo hará. Le mostramos cómo establecer una versión específica secreta deshabilitarse:

    Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0

## <a name="conclusion"></a>Conclusión


Con este método nuevo, el certificado puede mantenerse independiente de la imagen de la máquina virtual o la carga de aplicación. Por lo que hemos eliminado un punto de exposición.

El certificado también se renuevan y cargado en depósito de clave sin tener que volver a crear la imagen de la máquina virtual o el paquete de implementación de la aplicación. La aplicación todavía debe proporcionarse aunque con el nuevo URI de esta nueva versión de certificado.

Al separar el certificado de la máquina virtual o la carga de la aplicación, ahora hemos reducido el número de personal que tendrá acceso directo al certificado.

Como ventaja adicional, ahora tiene un lugar cómodo en depósito de clave para administrar sus certificados, incluidas todas las versiones que se han distribuido a lo largo del tiempo.

## <a name="next-steps"></a>Pasos siguientes

[Implementar una máquina virtual con una contraseña de la cámara de clave](azure-stack-kv-deploy-vm-with-secret.md)

[Permitir que una aplicación tener acceso a la clave de cámara](azure-stack-kv-sample-app.md)