<properties
   pageTitle="Ajustar el clúster ACS con CLI Azure | Microsoft Azure"
   description="Cómo ampliar su clúster de servicio del contenedor de Azure mediante la CLI de Azure."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenedores, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/03/2016"
   ms.author="timlt"/>

# <a name="scale-an-azure-container-service"></a>Ajustar el tamaño de un servicio de contenedor de Azure

Puede escalar el número de nodos que tiene el servicio de contenedor de Azure (ACS) mediante la herramienta de Azure CLI. Cuando utiliza la CLI Azure escalar, la herramienta devuelve un nuevo archivo de configuración que representa el cambio aplicado en el contenedor.

## <a name="about-the-command"></a>Acerca del comando

CLI Azure debe estar en modo de administrador de recursos de Azure para interactuar con contenedores de Azure. Puede cambiar al modo de administrador de recursos llamando `azure config mode arm`. La `acs` comando tiene un comando secundario denominado `scale` que realiza todas las operaciones de escala de un servicio de contenedor. Puede obtener ayuda sobre los distintos parámetros que se usan en el comando escala ejecutando `azure acs scale --help`, que genera algo parecido a este:

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>Use el comando para cambiar la escala

Para cambiar la escala de un servicio de contenedor, primero debe saber el **grupo de recursos** y el **nombre de servicio de contenedor de Azure (ACS)**y a continuación, especificar el nuevo recuento de agentes. Mediante el uso de una cantidad mayor o menor, puede cambiar la escala o Bajar respectivamente.

Desea saber qué el recuento actual de agentes de un servicio de contenedor antes de escala. Usar el `azure acs show <resource group> <ACS name>` comando para devolver la configuración de ACS. Tenga en cuenta el resultado de <mark>recuento</mark> .

#### <a name="see-current-count"></a>Vea recuento actual

```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>Ajustar el nuevo recuento

Como probablemente ya evidente, puede escalar el servicio de contenedor llamando `azure acs scale` y proporcionando el **grupo de recursos**, el **nombre de ACS**y el **recuento de agente**. Al cambiar la escala de un servicio de contenedor, CLI Azure devuelve una cadena JSON que representa la nueva configuración del servicio de contenedor, incluido el nuevo recuento de agente.

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>Pasos siguientes

- [Implementar un clúster](container-service-deployment.md)