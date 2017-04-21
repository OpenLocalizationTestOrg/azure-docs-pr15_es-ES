<properties
    pageTitle="Replicar máquinas virtuales de VMware en Azure mediante recuperación del sitio con DSC de automatización de Azure | Microsoft Azure"
    description="Describe cómo utilizar DSC de automatización de Azure para implementar automáticamente el servicio de movilidad de recuperación de sitio de Azure y Azure agente para máquinas virtuales o físicas en Azure."
    services="site-recovery"
    documentationCenter=""
    authors="krnese"
    manager="lorenr"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="krnese"/>

# <a name="replicate-vmware-virtual-machines-to-azure-by-using-site-recovery-with-azure-automation-dsc"></a>Replicar máquinas virtuales de VMware en Azure mediante recuperación del sitio con DSC de automatización de Azure

En el conjunto de aplicaciones de administración de operaciones, le proporcionamos una copia de seguridad completa y la solución de recuperación de desastres que puede usar como parte de su plan de continuidad empresarial.

Se inicia este viaje junto con Hyper-V mediante Hyper-V réplica. Pero hemos ampliado para admitir una configuración heterogénea porque los clientes tienen varias plataformas e hipervisores en sus nubes.

Si se están ejecutando actualmente VMware cargas de trabajo o servidores físicos, un servidor de administración de todos los componentes de recuperación de sitios de Azure ejecuta en su entorno para controlar la replicación de comunicación y los datos con Azure, cuando Azure es el destino.

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>Implementar el servicio de movilidad de recuperación del sitio mediante la automatización DSC
Empezaremos haciendo un desglose rápido de lo que hace este servidor de administración.

El servidor de administración ejecuta varias funciones de servidor. Una de estas funciones es de *configuración*, que coordenadas comunicaciones y administra los procesos de replicación y la recuperación de datos.

Además, la función de *proceso* actúa como una puerta de enlace de replicación. Esta función recibe datos de replicación de equipos de origen protegida, optimiza con almacenamiento en caché, compresión y cifrado y, a continuación, envía a una cuenta de almacenamiento de Azure. Una de las funciones para la función de proceso es también insertar instalación del servicio de movilidad en equipos protegidos y realizar la detección automática de máquinas virtuales de VMware.

Si hay una recuperación de Azure, la función de *destino maestra* controlará los datos de replicación como parte de esta operación.

Para los equipos protegidos, confía en el *servicio de movilidad*. Este componente se implementa en cada equipo (VMware VM o servidor físico) que desea replicar en Azure. Captura de escritura de datos en el equipo y reenvía al servidor de administración (rol de proceso).

Cuando está trabajando con continuidad empresarial, es importante conocer las cargas de trabajo, la infraestructura y los componentes implicados. A continuación, puede cumplir los requisitos de su objetivo de tiempo de recuperación (RTO) y objetivo de punto de recuperación (RPO). En este contexto, el servicio de movilidad es clave para garantizar que las cargas de trabajo están protegidas como cabría esperar.

¿Cómo puede, de forma optimizada, asegúrese de que tiene una configuración confiable protegida con la Ayuda de algunos componentes del conjunto de aplicaciones de administración de operaciones?

En este artículo se proporciona un ejemplo de cómo puede usar Azure automatización deseado estado configuración (DSC), junto con el sitio de recuperación, para asegurarse de que:

- El servicio de movilidad y el agente de Azure VM se implementan en los equipos de Windows que desea proteger.
- El servicio de movilidad y agente de Azure VM siempre se ejecutan cuando Azure es el objetivo de replicación.

## <a name="prerequisites"></a>Requisitos previos

- Un repositorio para almacenar la configuración necesaria
- Un repositorio para almacenar la contraseña necesaria para registrar con el servidor de administración

 > [AZURE.NOTE] Se genera una frase de contraseña único por cada servidor de administración. Si va a implementar varios servidores de administración, debe asegurarse de que la contraseña correcta se almacena en el archivo passphrase.txt.

- Marco de trabajo de administración de Windows (WMF) 5.0 instalado en los equipos que desea habilitar la protección (un requisito de automatización DSC)

 > [AZURE.NOTE] Si desea utilizar máquinas DSC para Windows que instalado WMF 4.0, vea la sección [Usar DSC en entornos desconectados](#Use DSC in disconnected environments).

El servicio de movilidad se puede instalar a través de la línea de comandos y acepta varios argumentos. Por eso debe tener los archivos binarios (después de extraer ellos desde su programa de instalación) y guardarlos en un lugar donde se pueden recuperar mediante una configuración de DSC.

## <a name="step-1-extract-binaries"></a>Paso 1: Archivos binarios de extracto

1. Para extraer los archivos que necesita para esta configuración, vaya al siguiente directorio de su servidor de administración:

    **\Microsoft Recovery\home\svsystems\pushinstallsvc\repository de sitio de azure**

    En esta carpeta, debería ver un archivo MSI con nombre:

    **Microsoft ASR_UA_version_Windows_GA_date_Release.exe**

    Utilice el comando siguiente para extraer al instalador:

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe/q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**

2. Seleccione todos los archivos y enviarlos a una carpeta comprimida (zip).

Ahora tiene los archivos binarios que necesita para automatizar la configuración del servicio de movilidad con DSC de automatización.

### <a name="passphrase"></a>Frase de contraseña

A continuación, debe determinar dónde desea colocar esta carpeta comprimida. Puede usar una cuenta de almacenamiento de Azure, tal como se muestra una versión posterior para almacenar la contraseña que necesita para la configuración. A continuación, se registrará el agente con el servidor de administración como parte del proceso.

La frase de contraseña que obtuvo cuando se implementa el servidor de administración se puede guardar en un archivo de texto como passphrase.txt.

Coloque la carpeta comprimida y la frase de contraseña en un contenedor dedicado en la cuenta de almacenamiento de Azure.

![Ubicación de carpeta](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Si prefiere mantener estos archivos en un recurso compartido de la red, puede hacerlo. ¿Necesita asegurarse de que el recurso de DSC que va a usar más tarde tiene acceso y puede obtener la configuración y la frase de contraseña.

## <a name="step-2-create-the-dsc-configuration"></a>Paso 2: Crear la configuración de DSC

El programa de instalación depende de WMF 5.0. En la máquina éxito al aplicar la configuración a través de automatización DSC, WMF 5.0 debe estar presente.

El entorno usa la siguiente configuración DSC de ejemplo:

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
La configuración hará lo siguiente:

- Las variables le indicará la configuración dónde puede obtener los archivos binarios para el servicio de movilidad y el agente de Azure VM dónde puede obtener la frase de contraseña y dónde desea almacenar el resultado.
- La configuración importará el recurso xPSDesiredStateConfiguration DSC, por lo que puede usar `xRemoteFile` para descargar los archivos del repositorio.
- La configuración creará un directorio donde desea almacenar los archivos binarios.
- El recurso de archivo extrae los archivos de la carpeta comprimida.
- El paquete de instalación de recursos instalará el servicio de movilidad de la UNIFIEDAGENT. Instalador EXE con los argumentos específicos. (Las variables que construcción los argumentos deba modificarse para reflejar el entorno).
- El paquete AzureAgent recursos instalará al agente de Azure VM, que se recomienda en cada máquina virtual que se ejecuta en Azure. El agente de Azure VM también permite agregar extensiones a la máquina virtual después de la migración.
- Los recursos de servicio se asegurará de que siempre se están ejecutando los servicios de movilidad relacionados y los servicios de Azure.

Guardar la configuración como **ASRMobilityService**.

>[AZURE.NOTE] No se olvide de reemplazar la CSIP en la configuración para reflejar el servidor de administración real, para que el agente se conectará correctamente y utilizará la contraseña correcta.

## <a name="step-3-upload-to-automation-dsc"></a>Paso 3: Cargar automatización DSC

Dado que la configuración de DSC que haya hecho importará un módulo de recursos DSC necesario (xPSDesiredStateConfiguration), debe importar ese módulo de automatización antes de cargar la configuración de DSC.

Inicie sesión con su cuenta de automatización, vaya a **activos** > **módulos**y haga clic en **Explorar la Galería**.

Aquí puede buscar el módulo e importar a su cuenta.

![Módulo de importación](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Cuando termine de esto, vaya a su equipo que tenga instalados los módulos de administrador de recursos de Azure y continuar para importar la configuración de DSC recién creada.

### <a name="import-cmdlets"></a>Cmdlets de importación

En PowerShell, inicie sesión en su suscripción de Azure. Modificar los cmdlets para reflejar el entorno y capturar información de su cuenta de automatización en una variable:

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Cargar la configuración en DSC de automatización mediante el siguiente cmdlet:

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>Compilar la configuración en DSC de automatización

A continuación, debe compilar la configuración en DSC de automatización, para que pueda comenzar a registrar nodos en él. Conseguir ejecutando el siguiente cmdlet:

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Esto puede tardar unos minutos, porque básicamente está implementar la configuración en el servicio de extracción DSC alojado.

Después de compilar la configuración, puede recuperar la información del trabajo con PowerShell (Get-AzureRmAutomationDscCompilationJob) o a través del [portal de Azure](https://portal.azure.com/).

![Recuperar el trabajo](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Ahora ha publicado y cargar su configuración DSC en DSC de automatización.

## <a name="step-4-onboard-machines-to-automation-dsc"></a>Paso 4: Máquinas incorporadas DSC de automatización
>[AZURE.NOTE] Uno de los requisitos previos para completar este escenario es que los equipos de Windows estén actualizados con la última versión de WMF. Puede descargar e instalar la versión correcta para su plataforma desde el [Centro de descarga](https://www.microsoft.com/download/details.aspx?id=50395).

Ahora cree una metaconfig para DSC que se aplicarán a los nodos. Para tener éxito con esto, debe recuperar la dirección URL del extremo y la clave principal de la cuenta de automatización seleccionada en Azure. Puede encontrar estos valores de **teclas** en el módulo de **toda la configuración** de la cuenta de automatización.

![Valores de clave](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

En este ejemplo, tiene un servidor de Windows Server 2012 R2 físico que desea proteger mediante el sitio de recuperación.

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>Busque las pendientes operaciones de cambio de nombre de archivo en el registro

Antes de empezar a asociar el servidor con el extremo de automatización DSC, le recomendamos que compruebe para pendiente operaciones de cambio de nombre de archivo en el registro. Puede impedir que el programa de instalación de acabado debido a un reinicio pendiente.

Ejecute el cmdlet siguiente para comprobar que no hay ningún reinicio pendiente en el servidor:

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Si se muestra vacío, son Aceptar para continuar. Si no es así, debería resolver este problema si al reiniciar el servidor durante una ventana de mantenimiento.

Para aplicar la configuración del servidor, inicie el entorno de Scripting integrado (ISE) de PowerShell y ejecute el siguiente script. Esto es, esencialmente, una configuración local DSC que indicará el motor de administrador de configuración Local para registrar con el servicio de automatización DSC y recuperar la configuración específica (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Esta configuración hará que el motor de administrador de configuración Local para registrarse con DSC de automatización. También determinará el funcionamiento del motor, lo que debería hacer si hay un desplazamiento de configuración (ApplyAndAutoCorrect) y cómo proceder con la configuración si es necesario reiniciar.

Después de ejecutar esta secuencia de comandos, debe empezar el nodo registrar con DSC de automatización.

![Registro de nodo en curso](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Si vuelve al portal de Azure, puede ver que el nodo recién registrado ahora ha aparecido en el portal.

![Nodo registrado en el portal](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

En el servidor, puede ejecutar el siguiente cmdlet de PowerShell para comprobar que el nodo se ha registrado correctamente:

```powershell
Get-DscLocalConfigurationManager
```

Una vez colocada y aplicada al servidor de la configuración, puede comprobar ejecutando el siguiente cmdlet:

```powershell
Get-DscConfigurationStatus
```

El resultado muestra que el servidor ha colocados correctamente su configuración:

![Salida](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Además, la configuración del servicio de movilidad tiene su propio registro que se encuentra en *unidadSistema*\ProgramData\ASRSetupLogs.

Eso es todo. Ahora ha implementado y registrado el servicio de movilidad en el equipo que desea proteger mediante el sitio de recuperación. DSC se asegurará de que siempre se están ejecutando los servicios necesarios.

![Implementación correcta](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Una vez que el servidor de administración detecta la implementación correcta, puede configurar la protección y Habilitar replicación en el equipo mediante el sitio de recuperación.

## <a name="use-dsc-in-disconnected-environments"></a>Usar DSC en entornos desconectados

Si los equipos no están conectados a Internet, aún puede confiar en DSC implementar y configurar el servicio de movilidad de las cargas de trabajo que desea proteger.

Para crear una instancia de su servidor de extracción DSC en su entorno para proporcionar esencialmente las mismas capacidades que recibe de automatización DSC. Es decir, los clientes extraerá la configuración (después de que se registra) al extremo DSC. Sin embargo, otra opción es insertar manualmente la configuración de DSC a los equipos, ya sea de forma local o remota.

Tenga en cuenta que en este ejemplo, hay un parámetro adicional para el nombre del equipo. Los archivos remotos ahora se encuentran en un recurso compartido remoto que debe ser accesible por los equipos que desea proteger. Final de la secuencia de comandos implementa la configuración y, a continuación, comienza a aplicar la configuración de DSC al equipo de destino.

### <a name="prerequisites"></a>Requisitos previos

Asegúrese de que está instalado el módulo de PowerShell xPSDesiredStateConfiguration. Para equipos de Windows donde está instalado WMF 5.0, puede instalar el módulo xPSDesiredStateConfiguration, ejecute el cmdlet siguiente en los equipos de destino:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

También puede descargar y guarde el módulo en caso de que necesite distribuir a los equipos de Windows que tiene WMF 4.0. Ejecutar este cmdlet en un equipo donde está presente PowerShellGet (WMF 5.0):

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

También para WMF 4.0, asegúrese de que el [Windows 8.1 Actualizar KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) está instalado en los equipos.

La siguiente configuración puede enviarse a los equipos de Windows que tengan WMF 5.0 y 4.0 WMF:

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Si desea crear una instancia de su propio servidor de extracción DSC en su red corporativa para imitar las capacidades que puede acceder desde DSC de automatización, consulte [configurar un servidor de extracción DSC web](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Opcional: Implementar una configuración de DSC mediante una plantilla de administrador de recursos de Azure

En este artículo se ha centrado sobre cómo puede crear su propia configuración de DLC para implementar el servicio de movilidad y el agente de VM Azure--automáticamente y asegúrese de que se están ejecutando en los equipos que desea proteger. También tenemos una plantilla de administrador de recursos de Azure que se implementará esta configuración DSC en una cuenta de Azure automatización nueva o existente. La plantilla utiliza parámetros de entrada para crear los activos de automatización que contengan las variables para su entorno.

Después de implementar la plantilla, puede consultar simplemente al paso 4 de esta guía para incorporado sus equipos.

La plantilla realice lo siguiente:

1. Usar una cuenta de automatización existente o cree uno nuevo
2. Tomar parámetros de entrada para:
    - ASRRemoteFile: la ubicación donde haya almacenado la configuración del servicio de movilidad
    - ASRPassphrase: la ubicación donde haya almacenado el archivo passphrase.txt
    - ASRCSEndpoint: la dirección IP de su servidor de administración
3. Importar el módulo de PowerShell xPSDesiredStateConfiguration
4. Crear y compilar la configuración de DSC

Todos los pasos anteriores sucederá en el orden correcto, para que pueda iniciar integrado sus equipos para la protección.

La plantilla con instrucciones para la implementación, se encuentra en [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Implementar la plantilla con PowerShell:

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Pasos siguientes

Después de implementar a los agentes de servicio de movilidad, puede [Habilitar la replicación](site-recovery-vmware-to-azure.md#step-6-replicate-applications) de los equipos virtuales.
