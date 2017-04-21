<properties
   pageTitle="Conectarse a un clúster privado seguro | Microsoft Azure"
   description="En este artículo se describe cómo proteger la comunicación dentro del programa independiente o privado, así como entre los clientes y el clúster."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/08/2016"
   ms.author="dkshir"/>

# <a name="secure-a-standalone-cluster-on-windows-using-x509-certificates"></a>Proteger un clúster independiente en Windows con certificados X.509

Este artículo describe cómo proteger la comunicación entre los diversos nodos de clúster de Windows independiente, así como la forma autenticar a clientes que se conectan a este clúster con X.509 certificados. Esto garantiza que sólo los usuarios autorizados pueden tener acceso al clúster, las aplicaciones distribuidas y realizar tareas de administración.  Certificado de seguridad debe estar habilitado en el clúster cuando se crea el clúster.  

Para obtener más información sobre seguridad de clúster como nodo de seguridad, seguridad de nodo de cliente y control de acceso basado en roles, vea [escenarios de seguridad de clúster](service-fabric-cluster-security.md).

## <a name="which-certificates-will-you-need"></a>¿Los certificados que necesitará?

Para empezar con, [Descargue el paquete de clúster independiente](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) a uno de los nodos en el clúster. En el paquete descargado, encontrará un archivo **ClusterConfig.X509.MultiMachine.json** . Abra el archivo y revise la sección de **seguridad** en la sección **Propiedades** :

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
            "ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
            "HttpApplicationGatewayCertificate":{
                "Thumbprint": "[Thumbprint]",
                "X509StoreName": "My"
            }
        }
    }

Esta sección describe los certificados que necesita para proteger su clúster de Windows independiente. Para habilitar la seguridad basada en el certificado y establece los valores de **ClusterCredentialType** y **ServerCredentialType** a *X509*.

>[AZURE.NOTE] Una [huella digital](https://en.wikipedia.org/wiki/Public_key_fingerprint) es la identidad principal de un certificado. Lea [cómo recuperar la huella digital de un certificado](https://msdn.microsoft.com/library/ms734695.aspx) para averiguar la huella digital de los certificados que cree.

La siguiente tabla enumeran los certificados que va a necesitar en la configuración de clúster:

|**Configuración de CertificateInformation**|**Descripción**|
|-----------------------|--------------------------|
|ClusterCertificate|Este certificado es necesario para proteger la comunicación entre los nodos en un clúster. Puede usar dos certificados diferentes, principal y secundario para la actualización. Establecer la huella digital del certificado principal en la sección de **huella digital** y de la secundaria en las variables **ThumbprintSecondary** .|
|Certificados|Este certificado se presenta al cliente cuando intenta conectarse a este clúster. Para mayor comodidad, puede utilizar el mismo certificado para *ClusterCertificate* y *certificados*. Puede usar dos certificados de servidor diferente, principal y secundario para la actualización. Establecer la huella digital del certificado principal en la sección de **huella digital** y de la secundaria en las variables **ThumbprintSecondary** . |
|ClientCertificateThumbprints|Se trata de un conjunto de certificados que desea instalar en los clientes autenticados. Puede tener un número diferente de certificados de cliente instalados en los equipos que desea permitir el acceso al clúster. Establecer la huella digital de cada certificado en la variable **CertificateThumbprint** . Si establece la **IsAdmin** *Verdadero*, a continuación, el cliente con este certificado instalado en él puede hacer a administrador actividades de administración en el clúster. Si la **IsAdmin** es *Falso*, el cliente con este certificado solo puede realizar las acciones permitidas para los derechos de acceso de usuario, normalmente de sólo lectura. Para obtener más información acerca de las funciones de lectura [(RBAC) de control de acceso basado en roles](service-fabric-cluster-security.md/#role-based-access-control-rbac)  |
|ClientCertificateCommonNames|Establecer el nombre común del primer certificado de cliente para el **CertificateCommonName**. El **CertificateIssuerThumbprint** es la huella digital del emisor de este certificado. [Trabajar con certificados](https://msdn.microsoft.com/library/ms731899.aspx) para obtener más información sobre nombres comunes y el emisor de lectura.|
|HttpApplicationGatewayCertificate|Se trata de un certificado opcional que se puede especificar si desea proteger la puerta de enlace de la aplicación Http. Asegúrese de que reverseProxyEndpointPort está establecido en nodeTypes si usa este certificado.|

Aquí es configuración de clúster de ejemplo donde ha proporcionado los certificados de clúster, servidor y cliente.

 ```
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
      "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
      "iPAddress": "10.7.0.6",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        }
        "security": {
            "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ServerCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpoint": "19001",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="aquire-the-x509-certificates"></a>Adquirir X.509 certificados
Para proteger la comunicación dentro del clúster, primero debe obtener certificados X.509 para los nodos del clúster. Además, para limitar la conexión a este clúster a equipos o usuarios autorizados, debe obtener e instalar certificados para los equipos cliente.

Para clústeres que ejecutan cargas de trabajo de producción, debe usar una [Entidad de certificación (CA)](https://en.wikipedia.org/wiki/Certificate_authority) del certificado X.509 para proteger el clúster firmado. Para obtener más información acerca de cómo obtener estos certificados, vaya a [Cómo: obtener un certificado](http://msdn.microsoft.com/library/aa702761.aspx).

Para clústeres que usa para fines de pruebas, puede usar un certificado autofirmado.

## <a name="optional-create-a-self-signed-certificate"></a>Opcional: Crear un certificado autofirmado
Una forma de crear un certificado autofirmado que se puede proteger correctamente es usar la secuencia de comandos de *CertSetup.ps1* en la carpeta de servicio tela SDK en el directorio *C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure*. Editar el archivo y use esta opción para crear un certificado con un nombre adecuado.

Exportar el certificado a un archivo PFX con una contraseña protegida. En primer lugar debe obtener la huella digital del certificado. Ejecute la aplicación certmgr.exe. Vaya a la carpeta del **Equipo Local/personal** y busque el certificado que acaba de crear. Haga doble clic en el certificado para abrirlo, seleccione la ficha *Detalles* y desplácese hasta el campo *huella digital* . Copie el valor de huella digital en el comando PowerShell debajo, quitar los espacios.  Cambie el valor de *$pswd* a una contraseña segura de idoneidad protegerlo y ejecutar la PowerShell:

```   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

Para ver los detalles de un certificado instalado en el equipo puede ejecutar el siguiente comando PowerShell:

```
$cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
Write-Host $cert.ToString($true)
```

Como alternativa, si tiene una suscripción de Azure, consulte la sección [Agregar certificados en depósito de clave](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault).

## <a name="install-the-certificates"></a>Instalar los certificados
Una vez que tenga certificados, se puede instalar en los nodos de clúster. Los nodos deben tener la última Windows PowerShell 3.x instalado en ellos. Debe Repita estos pasos en cada nodo de clúster y servidor certificados y cualquier secundario.

1. Copiar los archivos .pfx al nodo.
2. Abra una ventana de PowerShell como administrador y escriba los siguientes comandos. Reemplazar la *$pswd* con la contraseña que utiliza para crear este certificado. Reemplace la *$PfxFilePath* por la ruta completa de la .pfx copiado a este nodo.

    ```
    $pswd = "1234"
    $PfcFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```

3. A continuación es necesario configurar el control de acceso en este certificado para que el proceso de tela de servicio, que se ejecuta en la cuenta de servicio de red, puede usar, ejecute el siguiente script. Proporcione la huella digital del certificado y "Servicio de red" de la cuenta de servicio. Puede comprobar que las ACL en el certificado son correctas mediante la herramienta certmgr.exe y examina administrar claves privadas en el certificado.

    ```
    param
    (
        [Parameter(Position=1, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$pfxThumbPrint,

        [Parameter(Position=2, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$serviceAccount
        )

        $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; };

        # Specify the user, the permissions and the permission type
        $permission = "$($serviceAccount)","FullControl","Allow"
        $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission;

        # Location of the machine related keys
        $keyPath = $env:ProgramData + "\Microsoft\Crypto\RSA\MachineKeys\";
        $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName;
        $keyFullPath = $keyPath + $keyName;

        # Get the current acl of the private key
        $acl = (Get-Item $keyFullPath).GetAccessControl('Access')

        # Add the new ace to the acl of the private key
        $acl.SetAccessRule($accessRule);

        # Write back the new acl
        Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop

        #Observe the access rights currently assigned to this certificate.
        get-acl $keyFullPath| fl
        ```

4. Repeat the steps above for each server certificate. You can also use these steps to install the client certificates on the machines that you want to allow access to the cluster.

## Create the secure cluster
After configuring the **security** section of the **ClusterConfig.X509.MultiMachine.json** file, you can proceed to [Create your cluster](service-fabric-cluster-creation-for-windows-server.md#createcluster) section to configure the nodes and create the standalone cluster. Remember to use the **ClusterConfig.X509.MultiMachine.json** file while creating the cluster. For example, your command might look like the following:

```
.\CreateServiceFabricCluster.ps1 - ClusterConfigFilePath.\ClusterConfig.X509.MultiMachine.json - MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab - AcceptEULA $true
```

Once you have the secure standalone Windows cluster successfully running, and have setup the authenticated clients to connect to it, follow the section [Connect to a secure cluster using PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) to connect to it. For example:

```
ServiceFabricCluster conectar ConnectionEndpoint - 10.7.0.4:19000 - KeepAliveIntervalInSec 10 - X509Credential - ServerCertThumbprint 057b9544a6f2733e0c8d3a60013a58948213f551 - FindType FindByThumbprint - FindValue 057b9544a6f2733e0c8d3a60013a58948213f551 - StoreLocation CurrentUser - StoreName mi
```

If you are logged on to one of the machines in the cluster, since this already has the certificate installed locally you can simply run the Powershell command to connect to the cluster and show a list of nodes:

```
ServiceFabricCluster conectar Get-ServiceFabricNode
```
To remove the cluster call the following command:

```
.\RemoveServiceFabricCluster.ps1 - ClusterConfigFilePath.\ClusterConfig.X509.MultiMachine.json - MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab
```
