<properties
   pageTitle="Agregar automatización Azure runbooks a planes de recuperación | Microsoft Azure"
   description="En este artículo se describe cómo Azure sitio recuperación ahora le permite ampliar los planes de recuperación mediante la automatización de Azure para completar tareas complejas durante la recuperación de Azure"
   services="site-recovery"
   documentationCenter=""
   authors="ruturaj"
   manager="gauravd"
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required"
   ms.date="10/23/2016"
   ms.author="ruturajd@microsoft.com"/>


# <a name="add-azure-automation-runbooks-to-recovery-plans---classic"></a>Agregar automatización Azure runbooks a planes de recuperación - clásica


Este tutorial describe cómo Azure sitio recuperación se integra con la automatización de Azure para proporcionar capacidad de ampliación de planes de recuperación. Planes de recuperación pueden coordinar la recuperación de sus máquinas virtuales protegidos con Azure sitio recuperación para replicación en nube secundario y replicación de Azure escenarios. También ayudan a realizar la recuperación **coherentes y precisos**, **repetición**y **automatizada**. Si se producen errores en sus máquinas virtuales a Azure, integración con la automatización de Azure extiende los planes de recuperación y le ofrece la capacidad de ejecutar runbooks, lo que permite a las tareas de automatización eficaces.

Si no ha oído hablar de automatización de Azure todavía, registrarse [aquí](https://azure.microsoft.com/services/automation/) y descargar sus secuencias de comandos de ejemplo [aquí](https://azure.microsoft.com/documentation/scripts/). Obtenga más información acerca de la [Recuperación de sitio de Azure](https://azure.microsoft.com/services/site-recovery/) y cómo coordinar la recuperación de Azure con los planes de recuperación [aquí](https://azure.microsoft.com/blog/?p=166264).

En este breve tutorial, veremos cómo puede integrar runbooks automatización de Azure en planes de recuperación. Se le automatizar tareas sencillas que anteriormente requirieron intervención manual y vea cómo convertir una recuperación de paso múltiple en una acción de recuperación de un solo clic. Veremos cómo puede solucionar un script sencillo si algo va mal.

## <a name="protect-the-application-to-azure"></a>Proteger la aplicación de Azure

Deje que nosotros comenzar con una aplicación simple que consta de dos máquinas virtuales. Aquí tenemos una aplicación HRweb de Fabrikam. Fabrikam-HRweb-front-end y back Fabrikam Hrweb-end son las dos máquinas virtuales protegidas a Azure mediante la recuperación de sitio de Azure. Para proteger los equipos virtuales con recuperación de sitio de Azure, siga los pasos siguientes.

1.  Habilitar la protección de sus máquinas virtuales.

2.  Asegúrese de que los equipos virtuales haya completado la replicación inicial y va a duplicar.

3.  Espere a que finalice la replicación inicial y el estado de replicación indica protegida.

![](media/site-recovery-runbook-automation/01.png)
---------------------

En este tutorial, se creará un plan de recuperación de la aplicación de Fabrikam HRweb para la recuperación ante la aplicación de Azure. A continuación, se integrará con un runbook que creará un extremo en el error sobre Azure máquina virtual servir páginas web en el puerto 80.

En primer lugar, vamos a crear un plan de recuperación para nuestra aplicación.

## <a name="create-the-recovery-plan"></a>Crear el plan de recuperación

Para recuperar la aplicación en Azure, debe crear un plan de recuperación.
Usando un plan de recuperación, que puede especificar el orden de recuperación de los equipos virtuales. La máquina virtual que se coloca en el grupo 1 se recuperar y se inician primera y, a continuación, seguirá la máquina virtual en el grupo 2.

Crear un Plan de recuperación es similar a continuación.

![](media/site-recovery-runbook-automation/12.png)

Para obtener más información acerca de los planes de recuperación, lea la documentación [aquí](https://msdn.microsoft.com/library/azure/dn788799.aspx "aquí").

A continuación, vamos a crear los artefactos necesarios en la automatización de Azure.

## <a name="create-the-automation-account-and-its-assets"></a>Crear la cuenta de automatización y sus activos

Necesita una cuenta de Azure automatización para crear runbooks. Si no dispone de una cuenta, vaya a ficha automatización de Azure denotada con ![](media/site-recovery-runbook-automation/02.png)y cree una nueva cuenta.

1.  Asigne un nombre para identificar con a la cuenta.

2.  Especificar una región geográfica donde desea colocar la cuenta.

Se recomienda para colocar la cuenta en la misma región como depósito de ASR.

![](media/site-recovery-runbook-automation/03.png)

A continuación, cree los siguientes activos en la cuenta.

### <a name="add-a-subscription-name-as-asset"></a>Agregar un nombre de suscripción como activo

1.  Agregar un nuevo valor ![](media/site-recovery-runbook-automation/04.png) en los activos de automatización de Azure y seleccione esta opción para![](media/site-recovery-runbook-automation/05.png)

2.  Seleccione el tipo de variable como **cadena**

3.  Especifique el nombre de variable como **AzureSubscriptionName**

    ![](media/site-recovery-runbook-automation/06.png)

4.  Especifique el nombre de la suscripción de Azure real como el valor de la variable.

    ![](media/site-recovery-runbook-automation/07_1.png)

Puede identificar el nombre de la suscripción desde la página de configuración de su cuenta en el portal de Azure.

### <a name="add-an-azure-login-credential-as-asset"></a>Agregar una credencial de inicio de sesión de Azure como activo

Automatización de Azure utiliza Azure PowerShell para conectarse a la suscripción y opera en los artefactos allí. Para ello, debe autenticar con su cuenta de Microsoft o una cuenta profesional o educativa.
Puede almacenar las credenciales de cuenta de un activo para usarlo de forma segura con runbook.

1.  Agregar un nuevo valor ![](media/site-recovery-runbook-automation/04.png) en los activos de automatización de Azure y seleccione![](media/site-recovery-runbook-automation/09.png)

2.  Seleccione el tipo de credencial como **Credenciales de Windows PowerShell**

3.  Especifique el nombre como **AzureCredential**

    ![](media/site-recovery-runbook-automation/10.png)

4.  Especifique el nombre de usuario y contraseña para iniciar sesión con.

Ahora estas opciones de configuración están disponibles en los activos.

![](media/site-recovery-runbook-automation/11.png)

Se proporciona más información sobre cómo conectarse a su suscripción a través de PowerShell [aquí](../powershell-install-configure.md).

A continuación, se creará un runbook en automatización de Azure que puede agregar un extremo de la máquina virtual front-end después de la migración.

## <a name="azure-automation-context"></a>Contexto de automatización de Azure

ASR pasa una variable de contexto runbook para ayudarle a escribir secuencias de comandos deterministas. Se puede concluir que los nombres de servicio de nube y la máquina Virtual son predecibles, pero ocurre que no siempre es el caso debido a determinados escenarios, como la donde puede haber cambiado el nombre de la máquina virtual debido a los caracteres no admitidos en Azure. Por lo tanto, esta información se pasa al plan de recuperación de ASR como parte del *contexto*.

A continuación se muestra un ejemplo del aspecto de la variable de contexto.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


La siguiente tabla contiene el nombre y una descripción para cada variable en el contexto.

**Nombre de la variable** | **Descripción**
---|---
RecoveryPlanName | Nombre del plan de ejecución. Le ayuda a realizar acciones en función del nombre con la misma secuencia de comandos
FailoverType | Especifica si la migración tras error se prueba, planificado o.
FailoverDirection | Especifique si la recuperación se en primaria o secundaria
Id | Identificar el número de grupo en el plan de recuperación cuando se está ejecutando el plan
VmMap | Matriz de todas las máquinas virtuales en el grupo
VMMap clave | Clave única (GUID) para cada máquina virtual. Es el mismo que el identificador de VMM de la máquina virtual donde sea aplicable.
Nombre de función | Nombre de la máquina virtual de Azure que se está recuperando
CloudServiceName | Nombre de servicio de nube Azure en la que se crea la máquina virtual.


Para identificar el VmMap Key en el contexto que podría ir a la página de propiedades de la máquina virtual de ASR y observe la propiedad VM GUID.

![](media/site-recovery-runbook-automation/13.png)

## <a name="author-an-automation-runbook"></a>Crear un runbook de automatización

Ahora cree runbook para abrir el puerto 80 en la máquina virtual front-end.

1.  Crear un runbook nuevo en la cuenta de Azure automatización con el nombre **OpenPort80**

    ![](media/site-recovery-runbook-automation/14.png)

2.  Vaya a la vista de autor del runbook y entrar en el modo borrador.

3.  Especificar la variable para usar como el contexto del plan de recuperación

    ```
        param (
            [Object]$RecoveryPlanContext
        )

    ```

4.  Conectar junto a la suscripción con el nombre de suscripción y credenciales

    ```
        $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

        # Connect to Azure
        $AzureAccount = Add-AzureAccount -Credential $Cred
        $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
        Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    ```

    Tenga en cuenta que usa los activos de Azure: **AzureCredential** y **AzureSubscriptionName** aquí.

5.  Especificar los detalles del extremo y el GUID de la máquina virtual para el que desea exponer el extremo. En este caso, la máquina virtual front-end.

    ```
        # Specify the parameters to be used by the script
        $AEProtocol = "TCP"
        $AELocalPort = 80
        $AEPublicPort = 80
        $AEName = "Port 80 for HTTP"
        $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
    ```

    Especifica el protocolo de extremo de Azure, puerto local en la máquina virtual y su puerto público asignado. Estas variables son necesarios para que los comandos de Azure que agregar extremos a máquinas virtuales de parámetros. El VMGUID contiene el GUID de la máquina virtual que necesita para que funcione en.

6.  La secuencia de comandos ahora extraer el contexto para el GUID VM determinado y crear un extremo de la máquina virtual hace referencia a él.

    ```
        #Read the VM GUID from the context
        $VM = $RecoveryPlanContext.VmMap.$VMGUID

        if ($VM -ne $null)
        {
            # Invoke pipeline commands within an InlineScript

            $EndpointStatus = InlineScript {
                # Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
                # Commands include: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including parameters)

                $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                    Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                    Update-AzureVM
                Write-Output $Status
            }
        }
    ```

7. Una vez finalizado, presionar publicar ![](media/site-recovery-runbook-automation/20.png) para permitir que la secuencia de comandos estén disponibles para su ejecución.

A continuación se ofrece la secuencia de comandos completa de referencia

```
  workflow OpenPort80
  {
    param (
        [Object]$RecoveryPlanContext
    )

    $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

    # Connect to Azure
    $AzureAccount = Add-AzureAccount -Credential $Cred
    $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

    # Specify the parameters to be used by the script
    $AEProtocol = "TCP"
    $AELocalPort = 80
    $AEPublicPort = 80
    $AEName = "Port 80 for HTTP"
    $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"

    #Read the VM GUID from the context
    $VM = $RecoveryPlanContext.VmMap.$VMGUID

    if ($VM -ne $null)
    {
        # Invoke pipeline commands within an InlineScript

        $EndpointStatus = InlineScript {
            # Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
            # This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

            $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                Update-AzureVM
            Write-Output $Status
        }
    }
  }
```

## <a name="add-the-script-to-the-recovery-plan"></a>Agregue la secuencia de comandos para el plan de recuperación

Una vez que el script esté listo, puede agregar al plan de recuperación que creó anteriormente.

1.  En el plan de recuperación que creó, elija Agregar una secuencia de comandos después del grupo 2. ![](media/site-recovery-runbook-automation/15.png)

2.  Especifique un nombre de secuencia de comandos. Esto es solo un nombre descriptivo para esta secuencia de comandos para mostrar en el plan de recuperación.

3.  En la migración tras error de script de Azure: seleccione el nombre de cuenta de automatización de Azure.

4.  En la Runbooks Azure, seleccione runbook que escribió.

![](media/site-recovery-runbook-automation/16.png)

## <a name="primary-side-scripts"></a>Secuencias de comandos principal

Cuando se está ejecutando una migración tras error en Azure, también puede elegir ejecutar secuencias de comandos principal. Estas secuencias de comandos se ejecutarán en el servidor VMM durante la migración tras error.
Secuencias de comandos principal solo están disponibles solo para apagado y publican etapas de cierre. Esto es porque esperamos que el sitio primario a estar disponible normalmente cuando se produzca un desastre.
Durante una migración tras error planificado, solo si participar en las operaciones del sitio primario, intentará ejecutar las secuencias de comandos principal. Si no están accesibles o tiempo de espera, la migración tras error seguirá recuperar los equipos virtuales.
Secuencias de comandos principal están no disponibles para los sitios de VMware/físico/Hyper-v sin VMM protegido a Azure - mientras failover en Azure.
Sin embargo, cuando recuperación de Azure a local, secuencias de comandos principal (Runbooks) puede utilizarse para todos los destinos excepto VMware.

## <a name="test-the-recovery-plan"></a>Probar el plan de recuperación

Una vez haya agregado runbook al plan puede iniciar un error de prueba y véalo en acción. Siempre se recomienda ejecutar un error de prueba para probar la aplicación y el plan de recuperación para asegurarse de que no hay ningún error.

1.  Seleccione el plan de recuperación e iniciar un error de prueba.

2.  Durante la ejecución del plan, puede ver si se ha ejecutado runbook o no a través de su estado.

    ![](media/site-recovery-runbook-automation/17.png)

3.  También puede ver el estado de ejecución de runbook detallada en la página de trabajos de automatización de Azure para runbook.

    ![](media/site-recovery-runbook-automation/18.png)

4.  Una vez completada la migración tras error, aparte de resultado de la ejecución de runbook, puede ver si la ejecución es correcta o no al visitar la página de Azure máquina virtual y examina los extremos.

![](media/site-recovery-runbook-automation/19.png)

## <a name="sample-scripts"></a>Secuencias de comandos de ejemplo

Mientras describimos automatizar uno comúnmente usados tareas de la adición de un extremo a una máquina virtual Azure en este tutorial, puede hacer un número de otras tareas de automatización eficaz mediante la automatización de Azure. Microsoft y la Comunidad de automatización de Azure proporcionan runbooks muestra que puede ayudarle a empezar a crear su propia utilidad runbooks, que puede usar como bloques de creación para las tareas de automatización más grandes y soluciones. Comenzar con ellos desde la galería y crear planes de recuperación de un solo clic eficaces para las aplicaciones con la recuperación de sitios de Azure.

## <a name="additional-resources"></a>Recursos adicionales

[Información general sobre automatización de Azure] (http://msdn.microsoft.com/library/azure/dn643629.aspx "Información general sobre automatización de Azure")

[Secuencias de comandos de automatización Azure de ejemplo] (http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Secuencias de comandos de automatización Azure de ejemplo")
