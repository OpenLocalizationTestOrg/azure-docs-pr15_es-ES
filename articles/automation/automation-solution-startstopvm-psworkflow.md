<properties 
    pageTitle="Iniciar y detener máquinas virtuales con la automatización de Azure - flujo de trabajo de PowerShell | Microsoft Azure"
    description="Versión gráfica del escenario de automatización de Azure incluidos runbooks para iniciar y detener máquinas virtuales de Windows clásico."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="07/06/2016"
    ms.author="bwren" />

# <a name="azure-automation-scenario---starting-and-stopping-virtual-machines"></a>Escenario de automatización Azure - iniciar y detener máquinas virtuales de Windows

En este escenario de automatización de Azure incluye runbooks para iniciar y detener máquinas virtuales de Windows clásico.  Puede usar este escenario por cualquiera de las siguientes acciones:  

- Use la runbooks sin modificaciones en su entorno. 
- Modificar la runbooks para llevar a cabo la funcionalidad personalizada.  
- Llamar a la runbooks desde otro runbook como parte de una solución general. 
- Use la runbooks como tutoriales para aprender a runbook conceptos de creación. 

> [AZURE.SELECTOR]
- [Gráfica](automation-solution-startstopvm-graphical.md)
- [Flujo de trabajo de PowerShell](automation-solution-startstopvm-psworkflow.md)

Esta es la versión de flujo de trabajo de PowerShell runbook de este escenario. También está disponible con [runbooks gráfica](automation-solution-startstopvm-graphical.md).

## <a name="getting-the-scenario"></a>Obtener el escenario

Este escenario consta de dos runbooks de flujo de trabajo de PowerShell que puede descargarse desde los siguientes vínculos.  Ver la [versión de gráficos](automation-solution-startstopvm-graphical.md) de este escenario para vínculos a los gráficos runbooks.

| Runbook | Vínculo | Tipo | Descripción |
|:---|:---|:---|:---|
| Inicio AzureVMs | [Iniciar Azure VM clásicas](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | Flujo de trabajo de PowerShell | Inicia todos los equipos virtuales clásicos en un subscriptionor Azure todos los equipos virtuales con un nombre de servicio en particular. |
| Detener AzureVMs | [Dejar de Azure VM clásicas](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | Flujo de trabajo de PowerShell | Detiene todos los equipos virtuales en una cuenta de automatización o todos los equipos virtuales con un nombre de servicio en particular.  |


## <a name="installing-and-configuring-the-scenario"></a>Instalar y configurar el escenario

### <a name="1-install-the-runbooks"></a>1. Instale el runbooks

Después de descargar el runbooks, puede importarlos usando el procedimiento de [importación de un Runbook](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook).

### <a name="2-review-the-description-and-requirements"></a>2. Revise los requisitos y descripción
La runbooks incluir texto de ayuda comentadas que incluye una descripción y activos necesarios.  También puede obtener la misma información de este artículo. 

### <a name="3-configure-assets"></a>3. configurar activos
La runbooks requieren los siguientes activos que debe crear y rellenar con los valores apropiados.

| Tipo de activos | Nombre de activos | Descripción |
|:---|:---|:---|:---|
| Credenciales | AzureCredential | Contiene las credenciales para una cuenta que tenga autoridad para iniciar y detener máquinas virtuales de la suscripción de Azure.  Como alternativa, puede especificar otro activo de credenciales en el parámetro de **credenciales** de la actividad de **Agregar AzureAccount** . |
| Variable | AzureSubscriptionId | Contiene el identificador de la suscripción de su suscripción de Azure. |

## <a name="using-the-scenario"></a>Usar el escenario

### <a name="parameters"></a>Parámetros

La runbooks tiene los siguientes parámetros.  Debe proporcionar los valores para los parámetros obligatorios y puede proporcionar valores para otros parámetros según sus necesidades.

| Parámetro | Tipo | Obligatorio | Descripción |
|:---|:---|:---|:---|
| ServiceName | cadena | No | Si se proporciona un valor, se inician todos los equipos virtuales con ese nombre de servicio o se detiene.  Si no se proporciona ningún valor, se inician todos los equipos virtuales clásicos en la suscripción de Azure o se detiene. |
| AzureSubscriptionIdAssetName | cadena | No | Contiene el nombre de la [variable activo](#installing-and-configuring-the-scenario) que contiene el identificador de la suscripción de su suscripción de Azure.  Si no especifica un valor, se utiliza *AzureSubscriptionId* .  |
| AzureCredentialAssetName | cadena | No | Contiene el nombre del [activo de credenciales](#installing-and-configuring-the-scenario) que contiene las credenciales de runbook usar.  Si no especifica un valor, se utiliza *AzureCredential* .  |

### <a name="starting-the-runbooks"></a>Iniciar la runbooks

Puede utilizar cualquiera de los métodos en el [Inicio de un runbook de automatización de Azure](automation-starting-a-runbook.md) para iniciar uno de los runbooks en este escenario.

Los comandos de ejemplo siguiente utiliza Windows PowerShell para ejecutar **StartAzureVMs** para iniciar todas las máquinas virtuales con el nombre del servicio *MyVMService*.

    $params = @{"ServiceName"="MyVMService"}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### <a name="output"></a>Salida

La runbooks mostrará [un mensaje de salida](automation-runbook-output-and-messages.md) para cada máquina virtual que indica si no se envió correctamente la instrucción de iniciar o detener.  Puede buscar una cadena específica en el resultado para determinar el resultado para cada runbook.  Las cadenas de resultados posibles se muestran en la tabla siguiente.

| Runbook | Condición | Mensaje |
|:---|:---|:---|
| Inicio AzureVMs | Máquina virtual ya se está ejecutando  | MyVM ya se está ejecutando |
| Inicio AzureVMs | Solicitud de inicio de máquina virtual enviado correctamente | Se ha iniciado MyVM |
| Inicio AzureVMs | Error de solicitud de inicio de máquina virtual  | No se pudo iniciar MyVM |
| Detener AzureVMs | Máquina virtual ya se ha detenido  | MyVM ya se ha detenido |
| Detener AzureVMs | Detener la solicitud de máquina virtual enviado correctamente | Se ha detenido MyVM |
| Detener AzureVMs | Error de solicitud de detención de máquina virtual  | No se pudo detener MyVM |

Por ejemplo, el siguiente fragmento de código de un runbook intenta iniciar todas las máquinas virtuales con el nombre de servicio *MyServiceName*.  Si alguno de los errores de solicitudes de inicio, pueden realizarse acciones de error. 

    $results = Start-AzureVMs -ServiceName "MyServiceName"
    foreach ($result in $results) {
        if ($result -like "* has been started" ) {
            # Action to take in case of success.
        }
        else {
            # Action to take in case of error.
        }
    }


## <a name="detailed-breakdown"></a>Desglose detallado

A continuación se muestra un desglose detallado de la runbooks en este escenario.  Puede usar esta información para personalizar el runbooks o solamente para aprender de ellas para crear sus propios escenarios de automatización.

### <a name="parameters"></a>Parámetros

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

El flujo de trabajo se inicia al obtener los valores para los [parámetros de entrada](#using-the-scenario).  Si no se proporcionan los nombres de los activos se utilizan nombres predeterminados.

### <a name="output"></a>Salida

    # Returns strings with status messages
    [OutputType([String])]

Esta línea declara que el resultado del runbook será una cadena.  No es necesario, pero es un procedimiento recomendado para cuando runbook se utiliza como un [runbook secundarios](automation-child-runbooks.md) para que sepa el tipo de resultado que va a pasar un runbook primario.

### <a name="authentication"></a>Autenticación

    # Connect to Azure and select the subscription to work against
    $Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
    $null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
    $SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

Las siguientes líneas establecer las [credenciales](automation-configuring.md#configuring-authentication-to-azure-resources) y la suscripción de Azure que se utilizará para el resto del runbook.
En primer lugar, usamos **Get-AutomationPSCredential** para obtener el activo que contiene las credenciales con acceso a iniciar y detener máquinas virtuales de la suscripción de Azure. **Agregar AzureAccount** utiliza este activo para establecer las credenciales.  El resultado se asigna a una variable ficticia para que no se incluye en los resultados de runbook.  

La variable activo con el identificador de la suscripción se recuperará con **Get-AutomationVariable** y la suscripción con **AzureSubscription seleccionar**.

### <a name="get-vms"></a>Obtener VM

    # If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
    { 
        $VMs = Get-AzureVM -ServiceName $ServiceName
    }
    else 
    { 
        $VMs = Get-AzureVM
    }

**Get-AzureVM** se usa para recuperar los equipos virtuales con que runbook funcionarán.  Si se proporciona un valor en la variable de entrada **ServiceName** , se recuperan sólo las máquinas virtuales con ese nombre de servicio.  Si el **nombre de servicio** es vacío, se recuperan todos los equipos virtuales.

### <a name="startstop-virtual-machines-and-send-output"></a>Iniciar o detener máquinas virtuales y enviar los resultados

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
        if ($VM.PowerState -eq "Started")
        {
            # The VM is already started, so send notice
            Write-Output ($VM.InstanceName + " is already running")
        }
        else
        {
            # The VM needs to be started
            $StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

            if ($StartRtn.OperationStatus -ne 'Succeeded')
            {
                # The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
            }
            else
            {
                # The VM started, so send notice
                Write-Output ($VM.InstanceName + " has been started")
            }
        }
    }

El siguiente paso de líneas por cada máquina virtual.  En primer lugar se comprueba para ver si se está ejecutando el **PowerState** de la máquina virtual o se detiene, dependiendo del runbook.  Si ya está en el estado de destino, se envía un mensaje a imprimir y los extremos runbook.  Si no es así, a continuación, se utiliza **AzureVM iniciar** o **Detener AzureVM** a intentar iniciar o detener la máquina virtual con el resultado de la solicitud que se almacena en una variable.  A continuación, se envía un mensaje para especificar si se envió la solicitud para iniciar o detener correctamente.


## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre cómo trabajar con runbooks secundarios, vea [runbooks secundarios en automatización de Azure](automation-child-runbooks.md) 
- Para obtener más información acerca de los mensajes de salida durante la ejecución de runbook y registro para ayudar a solucionar problemas, consulte [Runbook salida y mensajes de automatización de Azure](automation-runbook-output-and-messages.md)
