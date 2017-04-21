
<properties
   pageTitle="Crear un clúster de servicio tela seguro con el Administrador de recursos de Azure | Microsoft Azure"
   description="En este artículo se describe cómo configurar un clúster de servicio tela seguro en Azure mediante el Administrador de recursos de Azure, depósito de clave de Azure y Azure Active Directory (AAD) para la autenticación de cliente."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="create-a-service-fabric-cluster-in-azure-using-azure-resource-manager"></a>Crear un clúster de servicio tela en Azure con el Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Administrador de recursos de Azure](service-fabric-cluster-creation-via-arm.md)
- [Portal de Azure](service-fabric-cluster-creation-via-portal.md)

Esta es una guía paso a paso que le guiará por los pasos de configuración de un clúster de Azure servicio tela seguro en Azure con el Administrador de recursos de Azure. Esta guía le guiará por los pasos siguientes:

 - Configurar depósito de clave para administrar las claves de seguridad de clúster y la aplicación.
 - Crear un clúster seguro en Azure con el Administrador de recursos de Azure.
 - Autenticar a los usuarios con Azure Active Directory (AAD) para la administración de clúster.

Un clúster seguro es un clúster que evita el acceso no autorizado a las operaciones de administración, que incluye implementar, actualizar y eliminar los datos que contienen, aplicaciones y servicios. Un clúster no seguro es una que cualquier persona puede conectarse en cualquier momento y realizar operaciones de administración. Aunque es posible crear un clúster no seguro, es **muy recomendable para crear un clúster seguro**. Un clúster no seguras **no se puede proteger más adelante** - se debe crear un nuevo clúster.

Los conceptos son los mismos para crear grupos de seguridad, si los clústeres son Linux clústeres o clústeres de Windows. Para obtener más información y ayuda secuencias de comandos para crear clústeres de Linux seguros, vea [crear clústeres seguros en Linux](#secure-linux-clusters)

## <a name="log-in-to-azure"></a>Inicie sesión en Azure
Esta guía usa [PowerShell de Azure][azure-powershell]. Al iniciar una nueva sesión de PowerShell, inicie sesión en su cuenta de Azure y seleccione la suscripción antes de ejecutar comandos de Azure.

Inicie sesión su cuenta de azure:

```powershell
Login-AzureRmAccount
```

Seleccione la suscripción:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>Configurar la clave de cámara

En esta sección se recorre crear un depósito de clave para un clúster de servicio tela en Azure y para las aplicaciones de servicio tela. Para obtener una guía completa en depósito de clave, consulte la [clave depósito Guía de introducción][key-vault-get-started].

Servicio tela utiliza certificados X.509 para proteger un clúster y proporcionar características de seguridad de la aplicación. Azure depósito de clave se usa para administrar los certificados para clústeres de servicio tela en Azure. Cuando se implementa un clúster de Azure, el proveedor de recursos de Azure responsable de crear clústeres tela de servicio extrae los certificados de la cámara de clave e instala en el clúster máquinas virtuales.

En el diagrama siguiente muestra la relación entre depósito clave, un clúster de tela de servicio y el proveedor de recursos de Azure que usa certificados almacenados en depósito de clave cuando crea un clúster:

![Instalación del certificado][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

El primer paso es crear un grupo de recursos específicamente para depósito de clave. Se recomienda poner depósito de clave en su propio grupo de recursos. Esto le permite quitar los grupos de recursos de proceso y almacenamiento, incluido el grupo de recursos que tiene el clúster de servicio tela sin perder sus claves y la información confidencial. El grupo de recursos que tiene su cámara clave debe estar en la misma región como el clúster que está utilizando.

```powershell

    New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    
    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>Crear depósito clave 

Crear un depósito de clave en el nuevo grupo de recursos. La clave depósito **debe estar habilitado para su implementación** para permitir que el proveedor de recursos de tela de servicio obtener certificados de ella e instalar en nodos de clúster:

```powershell

    New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment
    
    
    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all
    
    
    Tags                             :
```

Si tiene una cámara clave existentes, puede habilitar para su implementación mediante CLI de Azure:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```

<a id="add-certificate-to-key-vault"></a>
## <a name="add-certificates-to-key-vault"></a>Agregar certificados en depósito de clave

Los certificados se usan en tela de servicio para proporcionar autenticación y cifrado para proteger distintos aspectos de un clúster y sus aplicaciones. Para obtener más información sobre cómo se usan los certificados en tela de servicio, vea [escenarios de seguridad de clúster de servicio tela][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Certificado de servidor y un clúster (obligatorio) 

Este certificado es necesario para proteger un clúster y evitar el acceso no autorizado a ella. Proporciona seguridad de clúster de varias maneras:
 
 - **Autenticación de clúster:** Autentica la comunicación de nodo a otro para la federación de clúster. Sólo los nodos que pueden probar su identidad con este certificado pueden unirse al clúster.
 - **Autenticación de servidor:** Autentica los extremos de la administración de clúster a un cliente de administración para que el cliente de administración sepa que está hablando con el clúster real. Este certificado también proporciona SSL para la API de administración de HTTPS y servicio tela Explorer sobre HTTPS.

Para fines, el certificado debe cumplir los requisitos siguientes:

 - El certificado debe contener una clave privada.
 - El certificado debe crearse para el intercambio de claves, puede exportar a un archivo de intercambio de información Personal (.pfx).
 - Nombre de asunto del certificado debe coincidir con el dominio que se usa para tener acceso al clúster de tela de servicio. Esta matchng se requiere para proporcionar SSL extremos de administración de HTTPS y el Explorador de tela de servicio del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para la `.cloudapp.azure.com` dominio. Debe adquirir un nombre de dominio personalizado para el clúster. Cuando se solicita un certificado de una entidad emisora de certificados, el nombre del certificado asunto debe coincidir con el nombre de dominio personalizado usado por el clúster.

### <a name="application-certificates-optional"></a>Certificados de la aplicación (opcionales)

Cualquier número de certificados adicionales se puede instalar en un clúster por motivos de seguridad de la aplicación. Antes de crear el clúster, tenga en cuenta los escenarios de seguridad de aplicaciones que requieren un certificado para instalarse en los nodos, como por ejemplo:

 - Cifrado y descifrado de los valores de configuración de aplicación
 - Cifrado de datos en los nodos durante la replicación 

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Formato de certificados para su uso del proveedor de recursos de Azure

Archivos de clave privados (.pfx) se pueden agregar y utilizar directamente a través de la cámara de clave. Sin embargo, el proveedor de recursos de Azure requiere claves almacenarse en un formato especial de JSON que incluye la .pfx como base 64 codificado de cadena y la contraseña de clave privada. Para adaptarse a estos requisitos, teclas deben ser coloca en una cadena JSON y, a continuación, se almacenan como *información confidencial* en depósito de clave.

Para facilitar este proceso, un módulo de PowerShell está [disponible en GitHub][service-fabric-rp-helpers]. Siga estos pasos para usar el módulo:

  1. Descargar todo el contenido de la repo en un directorio local. 
  2. Importar el módulo en la ventana de PowerShell:

  ```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
  ```
     
La `Invoke-AddCertToKeyVault` comando en este módulo de PowerShell automáticamente da formato a una clave privada de certificado en una cadena JSON y cargas en depósito de clave. Puede usarlo para agregar el certificado de clúster y los certificados de aplicación adicionales en depósito de clave. Repita este paso para los certificados adicionales que desea instalar en el clúster.

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"
    
    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault
    
    
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

Las cadenas anteriores son todos los requisitos previos de depósito clave para configurar una plantilla de administrador de recursos de clúster de tela de servicio que se instala certificados para la autenticación de nodo, la seguridad de extremo de administración y la autenticación y las características de seguridad adicionales de la aplicación que usen certificados X.509. En este momento, ahora debería tener la siguiente configuración en Azure:

 - Grupo de recursos de la cámara de clave
   - Depósito clave
     - Certificado de autenticación de servidor de clúster
     - Certificados de aplicación

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurar Azure Active Directory para la autenticación de cliente

AAD permite que las organizaciones (conocidas como inquilinos) para administrar el acceso de usuario a aplicaciones, que se dividen en aplicaciones con un inicio de sesión basados en web interfaz de usuario y una experiencia de cliente nativo. En este documento, se supone que ya ha creado a un inquilino. Si no es así, empiece por leer [cómo obtener un inquilino de Azure Active Directory][active-directory-howto-tenant].

Un clúster de servicio tela ofrece varios puntos de entrada a su funcionalidad de administración, incluido el basados en web [Servicio tela Explorer] [ service-fabric-visualizing-your-cluster] y [Visual Studio][service-fabric-manage-application-in-visual-studio]. Como resultado, crear dos aplicaciones AAD para controlar el acceso al clúster, una aplicación web y una aplicación nativa.

Para simplificar algunos de los pasos necesarios para configurar AAD con un clúster de servicio tela, hemos creado un conjunto de secuencias de comandos de Windows PowerShell.

>[AZURE.NOTE] Debe realizar estos pasos *antes de* crear el clúster lo en casos donde las secuencias de comandos esperan nombres de clúster y extremos, deben ser los valores planeados, no a los que ya ha creado.

1. [Descargar las secuencias de comandos] [ sf-aad-ps-script-download] a su equipo.

2. Haga clic en el archivo zip, elija **Propiedades**, a continuación, active la casilla de verificación **desbloquear** y aplicar.

3. Extraer el archivo zip.

4. Ejecutar `SetupApplications.ps1`, proporcionar la TenantId, nombreDeClúster y WebApplicationReplyUrl como parámetros. Por ejemplo:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Puede encontrar su **TenantId** , ejecute el comando PowerShell "Get-AzureSubscription" '. Esta opción mostrará la **TenantId** para cada suscripción.

    La **nombreDeClúster** se usa para las aplicaciones de AAD creadas por la secuencia de comandos de prefijo. No es necesario para que coincida con el nombre del clúster real tal como solo se pretende que sea más fácil de asignar artefactos AAD al que está aplicando con clúster tela de servicio.

    El **WebApplicationReplyUrl** es el extremo predeterminado que AAD devuelve a los usuarios después de completar el proceso de inicio de sesión. Debe establecer al extremo de servicio tela Explorer para el clúster, que es predeterminada:

    https://&lt;cluster_domain&gt;: 19080/explorador

    Se le pide que inicie sesión en una cuenta con privilegios de administrador para el inquilino AAD. Una vez hecho, continúa la secuencia de comandos para crear la web y aplicaciones nativas para representar el clúster tela de servicio. Si mira aplicaciones del inquilino en el [portal de clásica Azure][azure-classic-portal], verá dos nuevas entradas:

    - *NombreDeClúster*\_clúster
    - *NombreDeClúster*\_cliente

    Imprimir el script el Json requiere la plantilla de administrador de recursos de Azure cuando se crea el clúster en la siguiente sección para mantener la PowerShell abrirá la ventana.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Crear una plantilla de administrador de recursos de clúster de tela de servicio

En esta sección, el resultado de los comandos de PowerShell anteriores se usan en una plantilla de administrador de recursos de clúster de tela de servicio.

Plantillas de administrador de recursos de ejemplo están disponibles en la [Galería de plantillas de inicio rápido de Azure en GitHub][azure-quickstart-templates]. Estas plantillas se pueden usar como punto de partida para la plantilla de diseño. 

### <a name="create-the-resource-manager-template"></a>Crear la plantilla de administrador de recursos

Esta guía utiliza el [clúster de 5 nodos seguro] [ service-fabric-secure-cluster-5-node-1-nodetype-wad] plantilla de ejemplo y los parámetros de plantilla. Descargar `azuredeploy.json` y `azuredeploy.parameters.json` a su equipo y abra los archivos en el editor de texto.

### <a name="add-certificates"></a>Agregar certificados

Certificados se agregan a una plantilla de administrador de recursos de clúster mediante una referencia a la cámara de clave que contiene las claves de certificado. Se recomienda que estos valores clave depósito están colocados en un archivo de parámetros de plantilla de administrador de recursos para mantener al administrador de recursos reutilizable de archivo de plantilla y libre de valores específicos para una implementación.

#### <a name="add-all-certificates-to-the-vmss-osprofile"></a>Agregar todos los certificados a la osProfile VMSS

Todos los certificados que debe estar instalado en el clúster deben estar configurado en la sección osProfile del recurso VMSS (Microsoft.Compute/virtualMachineScaleSets). Esto indica que el proveedor de recursos para instalar el certificado en las máquinas virtuales. Esto incluye el certificado de clúster, así como los certificados de seguridad de la aplicación que planea usar para las aplicaciones:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-service-fabric-cluster-certificate"></a>Configurar certificado de clúster de tela de servicio

El certificado de autenticación de clúster debe también configurarse en el recurso de clúster de tela de servicio (Microsoft.ServiceFabric/clusters) y en la extensión de servicio tela para VMSS en el recurso VMSS. Esto permite al proveedor de recursos de servicio tela configurarlo para usar para la autenticación de servidor para los extremos de la administración y autenticación de clúster.

##### <a name="vmss-resource"></a>Recursos VMSS:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>Recursos de tela de servicio:

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-aad-config"></a>Insertar AAD config

La configuración de AAD que creó anteriormente se puede insertar directamente en la plantilla de administrador de recursos, pero se recomienda para extraer los valores a parámetros primero en un archivo de parámetros para mantener la plantilla de administrador de recursos reutilizables y gratuita de valores específicos para una implementación.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### < un "Configurar brazo" ></a>parámetros de plantilla de configurar el Administrador de recursos

Por último, utilice los valores de salida de los comandos de tecla de cámara y AAD PowerShell para rellenar el archivo de parámetros:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
En este momento, ahora debe tener el siguiente:

 - Grupo de recursos de la cámara de clave
    - Depósito clave
    - Certificado de autenticación de servidor de clúster
    - Certificado de cifrado de datos
 - Inquilino de Azure Active Directory 
    - Aplicación de AAD para administración basada en web y el Explorador de tela de servicio
    - Aplicación de AAD para la administración de cliente nativo
    - Usuarios con roles asignados 
 - Plantilla de administrador de recursos de clúster de tela de servicio
    - Certificados configurados a través de la cámara de clave
    - Azure Active Directory configurado 

El siguiente diagrama muestra dónde encajan los depósito clave y AAD la configuración en la plantilla de administrador de recursos.

![Mapa de dependencia del Administrador de recursos][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Crear el clúster

Ya está listo para crear el clúster utilizando la [implementación de ARM][resource-group-template-deploy].

#### <a name="test-it"></a>Pruébelo

Use el siguiente comando PowerShell para probar la plantilla de administrador de recursos con un archivo de parámetros:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Implementarlo

Si se pasa la prueba de la plantilla de administrador de recursos, use el siguiente comando PowerShell para implementar la plantilla de administrador de recursos con un archivo de parámetros:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>
## <a name="assign-users-to-roles"></a>Asignar a usuarios a funciones

Una vez que haya creado las aplicaciones para representar el clúster, debe asignar los usuarios a las funciones compatibles con tela de servicio: sólo lectura y administrador. Puede hacerlo con el [portal clásica Azure][azure-classic-portal].

1. Vaya a su inquilino y elija aplicaciones.
2. Elija la aplicación web, que tiene un nombre como `myTestCluster_Cluster`.
3. Haga clic en la ficha usuarios.
4. Elija un usuario para asignar y haga clic en el botón **asignar** en la parte inferior de la pantalla.

    ![Asignar a usuarios a botón de roles][assign-users-to-roles-button]

5. Seleccione el rol para asignar al usuario.

    ![Asignar a usuarios a funciones][assign-users-to-roles-dialog]

>[AZURE.NOTE] Para obtener más información acerca de las funciones en tela de servicio, consulte [control de acceso basado en roles para los clientes de servicio tela](service-fabric-cluster-security-roles.md).

 <a name="secure-linux-cluster"></a> 
##  <a name="create-secure-clusters-on-linux"></a>Crear clústeres seguros en Linux

Para facilitar el proceso, se ha proporcionado una secuencia de comandos de Ayuda [aquí](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Para usar esta secuencia de comandos auxiliar, se supone que ya tiene instalado de CLI de Azure y se encuentra en la ruta de acceso. Asegúrese de que el script tiene permisos para ejecutar ejecutando `chmod +x cert_helper.py` después de descargarlo. El primer paso es iniciar sesión en su cuenta de Azure mediante la CLI con la `azure login` comando. Después de iniciar sesión en su cuenta de Azure, use la aplicación auxiliar con el certificado autofirmado de entidad emisora de certificados, como se muestra en el siguiente comando:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]

The -ifile parameter can take a .pfx or a .pem file as input, with the certificate type (pfx or pem, or ss if it is a self-signed cert).
The parameter -h prints out the help text.
```

Este comando devuelve las tres siguientes cadenas como resultado: 

1. SourceVaultID, que es el identificador de la nueva KeyVault ResourceGroup que creó para usted. 

2. Un CertificateUrl para el acceso al certificado.

3. CertificateThumbprint, que se usa para la autenticación.


En el ejemplo siguiente se muestra cómo usar el comando:

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
Ejecutar el comando anterior ofrece tres cadenas como sigue:

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

 Nombre de asunto del certificado debe coincidir con el dominio que se usa para tener acceso al clúster de tela de servicio. Esto es necesario para proporcionar SSL extremos de administración de HTTPS y el Explorador de tela de servicio del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para la `.cloudapp.azure.com` dominio. Debe adquirir un nombre de dominio personalizado para el clúster. Cuando se solicita un certificado de una entidad emisora de certificados el nombre del certificado asunto debe coincidir con el nombre de dominio personalizado usado por el clúster.

Estas son las entradas necesarias para crear un clúster de tela de servicio seguro (sin AAD) como se describe en [parámetros de plantilla de configurar el Administrador de recursos](#configure-arm). Puede conectarse al clúster seguro a través de instrucciones de [acceso de cliente a un clúster de autenticación](service-fabric-connect-to-secure-cluster.md). Clústeres de vista previa de Linux no admiten la autenticación AAD. Puede asignar roles de administrador y cliente como se describe en la sección [asignar roles a los usuarios](#assign-roles). Cuando se especifican los roles de administrador y cliente para un clúster de vista previa de Linux, debe proporcionar los certificados de huella digital para la autenticación (en lugar del nombre de asunto, ya que no se realiza ninguna validación de la cadena o revocación en esta versión preliminar).


Si desea usar un certificado autofirmado para realizar pruebas, podría usar la misma secuencia de comandos para generar un certificado autofirmado y cárguelo en KeyVault, proporcionando la marca `ss` en lugar de proporcionar la ruta de certificado y el nombre del certificado. Por ejemplo, consulte el comando siguiente para crear y cargar un certificado autofirmado:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net" 
```

Este comando devuelve las mismas tres cadenas SourceVault, CertificateUrl y CertificateThumbprint, que se utiliza para crear un clúster de Linux seguro, junto con la ubicación donde se coloca el certificado autofirmado. Necesitará el certificado autofirmado para conectar con el clúster.  Puede conectarse al clúster seguro a través de instrucciones de [acceso de cliente a un clúster de autenticación](service-fabric-connect-to-secure-cluster.md). Nombre de asunto del certificado debe coincidir con el dominio que se usa para tener acceso al clúster de tela de servicio. Esto es necesario para proporcionar SSL extremos de administración de HTTPS y el Explorador de tela de servicio del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para la `.cloudapp.azure.com` dominio. Debe adquirir un nombre de dominio personalizado para el clúster. Cuando se solicita un certificado de una entidad emisora de certificados el nombre del certificado asunto debe coincidir con el nombre de dominio personalizado usado por el clúster.

Los parámetros proporcionados por la secuencia de comandos de la aplicación auxiliar pueden rellenarse en el portal como se describe en la sección [crear un clúster en el portal de Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-portal).

## <a name="next-steps"></a>Pasos siguientes

En este momento, tiene un clúster seguro con autenticación de Azure Active Directory proporcionar administración. [Conectarse a su clúster](service-fabric-connect-to-secure-cluster.md) de siguiente y obtenga información sobre cómo [Administrar la información confidencial de la aplicación](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Solucionar problemas de configuración de Azure Active Directory para la autenticación del cliente

Si tiene un problema al configurar Azure Active Directory para la autenticación de cliente, revise la siguiente suggestings posibles soluciones.

### <a name="service-fabric-explorer-prompts-for-selecting-certificate"></a>Servicio tela Explorer solicita seleccionar certificado

#### <a name="problem"></a>Problema

Después de iniciar sesión correctamente en la página de inicio de sesión AAD en el Explorador de tela de servicio, el explorador vuelve a la página principal, pero los avisos de un cuadro de diálogo para seleccionar un certificado.

![Cuadro de diálogo de Seleccionar certificado SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motivo

El usuario no se ha asignado una función en la aplicación de clúster AAD. Por lo tanto la autenticación de AAD falla en clúster de tela de servicio. Explorador de tela de servicio utiliza la autenticación de certificado.

#### <a name="solution"></a>Solución

Siga las instrucciones de configuración de AAD y asignar roles de usuario. Además, se recomienda "Usuario asignación obligatorio a aplicación de ACCESS" Activar como `SetupApplications.ps1` hace.

### <a name="connect-with-powershell-fails-with-error-the-specified-credentials-are-invalid"></a>Conectar con PowerShell se produce un error con error: las credenciales especificadas no son válidas

#### <a name="problem"></a>Problema

Cuándo usar PowerShell para conectar con clúster mediante "AzureActiveDirectory" modo de seguridad, después de iniciar sesión correctamente en la página de inicio de sesión AAD, conexión de un error: las credenciales especificadas no son válidas se muestra.

#### <a name="solution"></a>Solución

Igual que el anterior.

### <a name="service-fabric-explorer-signing-in-return-failure-aadsts50011"></a>Explorador de tela de servicio de firma de error: AADSTS50011

#### <a name="problem"></a>Problema

Después de iniciar sesión en la página en el Explorador de tela de servicio de inicio de sesión AAD, página devuelve el signo en error: AADSTS50011: la dirección de respuesta &lt;url&gt; no coinciden con las direcciones de respuesta configuradas para la aplicación: &lt;guid&gt;. 

![No coincidan con la dirección de respuesta SFX][sfx-reply-address-not-match]

#### <a name="reason"></a>Motivo

La aplicación cluster(web) que representa el servicio tela Explorer intenta autenticarse AAD, como parte de la solicitud proporciona la dirección URL de redirección retorno. Pero no aparece en la lista AAD aplicación 'URL de respuesta'.

#### <a name="solution"></a>Solución

Agregue la dirección url de servicio tela Explorer a la 'Dirección URL de respuesta' en la ficha configurar de aplicación cluster(web) o reemplazar uno de los elementos de la lista. A continuación, guardar.

![Dirección url de respuesta de la aplicación Web][web-application-reply-url]

### <a name="can-i-reuse-the-same-aad-tenant-for-multiple-clusters"></a>¿Volver a utilizar al mismo inquilino AAD varios clústeres?

#### <a name="answer"></a>Respuesta

Sí. Pero recuerde que debe agregar la dirección URL del servicio tela explorador a la aplicación de cluster(web) que Explorador de tela de servicio de lo contrario, no funcionará.

### <a name="why-do-i-still-need-server-certificate-while-aad-enabled"></a>¿Por qué todavía necesito certificado de servidor mientras AAD habilitado?

#### <a name="answer"></a>Respuesta

FabricClient y FabricGateway realizar autenticación mutua. En caso de autenticación de AAD, integración de AAD proporciona la identidad del cliente al servidor y certificado de servidor se usa para comprobar la identidad del servidor. Para obtener más información sobre cómo funciona el certificado en tela de servicio, compruebe [los certificados X.509 y tela de servicio][x509-certificates-and-service-fabric]

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png