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


# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Agregar automatización Azure runbooks a planes de recuperación


Este tutorial describe cómo Azure sitio recuperación se integra con la automatización de Azure para proporcionar capacidad de ampliación de planes de recuperación. Planes de recuperación pueden coordinar la recuperación de sus máquinas virtuales protegidos con Azure sitio recuperación para replicación en nube secundario y replicación de Azure escenarios. También ayudan a realizar la recuperación **coherentes y precisos**, **repetición**y **automatizada**. Si se producen errores en sus máquinas virtuales a Azure, integración con la automatización de Azure extiende los planes de recuperación y le ofrece la capacidad de ejecutar runbooks, lo que permite a las tareas de automatización eficaces.

Si no ha oído hablar de automatización de Azure todavía, registrarse [aquí](https://azure.microsoft.com/services/automation/) y descargar sus secuencias de comandos de ejemplo [aquí](https://azure.microsoft.com/documentation/scripts/). Obtenga más información acerca de la [Recuperación de sitio de Azure](https://azure.microsoft.com/services/site-recovery/) y cómo coordinar la recuperación de Azure con los planes de recuperación [aquí](https://azure.microsoft.com/blog/?p=166264).

En este tutorial, veremos cómo puede integrar runbooks automatización de Azure en planes de recuperación. Se le automatizar tareas sencillas que anteriormente requirieron intervención manual y vea cómo convertir una recuperación de varios pasos en una acción de recuperación de un solo clic. Veremos cómo puede solucionar un script sencillo si algo va mal.

## <a name="customize-the-recovery-plan"></a>Personalizar el plan de recuperación

1. Deje que nosotros comience por operning la hoja de recursos de plan de recuperación. Puede ver que el plan de recuperación tiene dos máquinas virtuales que se agregan para la recuperación. 

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
---------------------

2. Haga clic en el botón Personalizar para comenzar a agregar un runbook. Se abrirá el plan de recuperación Personalizar módulo.


    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


3. Haga clic con el botón secundario en el grupo de inicio 1 y seleccione esta opción para agregar una entrada de la acción del complemento.

4. Seleccione Elegir una secuencia de comandos en el nuevo módulo.

5. Asigne un nombre a la secuencia de comandos 'Hola a todos'.

6. Elija un nombre de cuenta de automatización. Esta es la cuenta de automatización de Azure. Tenga en cuenta que esta cuenta puede estar en cualquier ubicación geográfica Azure, pero tiene que estar en la misma suscripción como depósito de recuperación del sitio.

7. Seleccione un runbook de la cuenta de automatización. Esta es la secuencia de comandos que se ejecutará durante la ejecución del plan de recuperación después de la recuperación del primer grupo.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)


8. Seleccione Aceptar para guardar la secuencia de comandos. Esto agregará la secuencia de comandos al grupo de acción de entrada del grupo 1: iniciar.


    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Puntos destacados de la adición de una secuencia de comandos

1. Puede haga clic con el botón secundario en la secuencia de comandos y elija 'Eliminar paso' o 'Actualizar script'.

2. Una secuencia de comandos puede ejecutar en el Azure durante la migración tras error de local a Azure y puede ejecutar en Azure como una secuencia de comandos principal antes del cierre, durante la recuperación de Azure a local.

3. Cuando se ejecuta una secuencia de comandos, insertará el contexto de un plan de recuperación.

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
CloudServiceName (en el modelo de implementación de administrador de recursos) | Nombre del grupo de recursos de Azure en la que se crea la máquina virtual.


## <a name="using-complex-variables-per-recovery-plan"></a>Uso de variables de complejo por el plan de recuperación

A veces, un runbook requiere más información que solo la RecoveryPlanContext. No hay ningún mecanismo de primera clase para pasar un parámetro a un runbook. Sin embargo, si desea usar la misma secuencia de comandos a través de los planes de recuperación puede utilizar la variable de contexto de Plan de recuperación 'RecoveryPlanName' y utilizar la debajo de experimentación técnica para usar una variable de complejo de automatización de Azure en un runbook. El ejemplo siguiente muestra cómo puede crear tres activos de otra variable complejo y usarlos en runbook basado en el nombre del plan de recuperación.

Tenga en cuenta que desea usar 3 parámetros adicionales en un runbook. Deje que nosotros los codificar en un formulario JSON {"Var1": "testautomation", "Var2": "No planeado", "Var3": "PrimaryToSecondary"}

Use [AA complejo variable](../automation/automation-variables.md#variable-types Complex variable) para crear un nuevo activo de automatización.
Nombre de la variable como <RecoveryPlanName>- parámetros.
Puede usar la referencia aquí para crear una [variable complejo](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396).

Para planes de recuperación diferentes, asigne un nombre a la variable como

1. recoveryPlanName1 >-parámetros
2. recoveryPlanName2 >-parámetros
3. recoveryPlanName3 >-parámetros

Ahora, en la secuencia de comandos hacen referencia a los parámetros como

1. Obtener el nombre de punto de reunión de la $rpname = $Recoveryplancontext variable
2. Obtener activo de $paramValue = "$($rpname) parámetros"
3. Use esto como una variable complejo para el plan de recuperación llamando Get-AzureAutomationVariable [-AutomationAccountName] <String> -$paramValue el nombre.

Por ejemplo, para obtener el parámetro de variable complejo para el plan de recuperación de SharepointApp, cree una variable de complejo de automatización de Azure denominada 'SharepointApp parámetros'.

Usar en el plan de recuperación mediante la extracción de la variable del activo mediante la instrucción Get-AzureAutomationVariable [-AutomationAccountName] <String> [-nombre] $paramValue. [Esta referencia para obtener más detalles](https://msdn.microsoft.com/library/dn913772.aspx)

De este modo, la misma secuencia de comandos puede utilizarse para el plan de recuperación diferentes almacenando la variable complejo específica de plan en los activos.

## <a name="sample-scripts"></a>Secuencias de comandos de ejemplo

Para un repositorio de secuencias de comandos que se pueden importar directamente a su cuenta de automatización, consulte [repositorio OMS de Kristian Nese de secuencias de comandos](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

La secuencia de comandos aquí es una plantilla de administrador de recursos de Azure que se implementará todos los debajo de secuencias de comandos

* GSN

Runbook GSN asignará direcciones IP públicas a cada VM en el Plan de recuperación y adjuntar sus adaptadores de red virtual a un grupo de seguridad de red que le permitirá comunicaciones predeterminado

* PublicIP

IP pública runbook asignará direcciones IP públicas a cada VM en el Plan de recuperación. Acceso a las aplicaciones y equipos dependerá de la configuración del firewall dentro de cada invitado


* CustomScript

Runbook CustomScript asignará direcciones IP públicas a cada VM en el Plan de recuperación e instalar una extensión de scripts personalizados que extraerá la secuencia de comandos que hacen referencia a durante la implementación de la plantilla

* NSGwithCustomScript

La NSGwithCustomScript runbook asignará direcciones IP públicas a cada VM en el Plan de recuperación, instalar un personalizado con la extensión de secuencias de comandos y una red virtual conectarlas hasta un GSN permitir predeterminado comunicación entrante y saliente para el acceso remoto

## <a name="additional-resources"></a>Recursos adicionales

[Servicio de automatización Azure ejecutar como cuenta](../automation/automation-sec-configure-azure-runas-account.md)

[Información general sobre automatización de Azure] (http://msdn.microsoft.com/library/azure/dn643629.aspx "Información general sobre automatización de Azure")

[Secuencias de comandos de automatización Azure de ejemplo] (http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Secuencias de comandos de automatización Azure de ejemplo")
