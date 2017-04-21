<properties
    pageTitle="Prácticas de Azure DevTest preguntas más frecuentes | Microsoft Azure"
    description="Encontrar respuestas a preguntas comunes de Azure DevTest prácticas"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="tarcher"/>

# <a name="azure-devtest-labs-faq"></a>Prácticas de Azure DevTest preguntas más frecuentes

Este artículo responde a algunas de las preguntas más frecuentes acerca de prácticas de DevTest de Azure.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>General
- [¿Qué sucede si no se responde aquí mi pregunta?](#what-if-my-question-isnt-answered-here)
- [¿Por qué debo usar Azure DevTest prácticas?](#why-should-i-use-azure-devtest-labs) 
- [¿Qué significa "preocupaciones, autoservicio"?](#what-does-quotworry-free-self-servicequot-mean)
- [¿Cómo puedo utilizar prácticas de DevTest de Azure?](#how-can-i-use-azure-devtest-labs) 
- [¿Cómo me facturado Azure DevTest prácticas](#how-am-i-billed-for-azure-devtest-labs) 
 
## <a name="security"></a>Seguridad 
- [¿Cuáles son los diferentes niveles de seguridad en Azure DevTest prácticas?](#what-are-the-different-security-levels-in-azure-devtest-labs) 
- [¿Cómo creo un rol para permitir a los usuarios realizar una tarea específica?](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task) 
 
## <a name="cicd-integration--automation"></a>Automatización e integración de elementos de configuración o CD 
 
- [¿Azure DevTest prácticas se integra con mi toolchain de elemento de configuración o CD?](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain) 
 
## <a name="virtual-machines"></a>Máquinas virtuales de Windows 
 
- [¿Por qué no puedo ver determinadas máquinas virtuales en el módulo de máquinas virtuales de Azure aparece dentro de Azure DevTest prácticas?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs) 
- [¿Cuál es la diferencia entre imágenes personalizadas y las fórmulas?](#what-is-the-difference-between-custom-images-and-formulas) 
- [¿Cómo creo varias VM desde la misma plantilla a la vez?](#how-do-i-create-multiple-vms-from-the-same-template-at-once) 
- [¿Cómo puedo mover mi máquinas virtuales de Azure existentes en mi laboratorio de prácticas de DevTest de Azure?](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab) 
- [¿Puedo adjuntar varios discos a mi VM?](#can-i-attach-multiple-disks-to-my-vms) 
- [¿Cómo puedo automatizar el proceso de carga de archivos de disco duro virtual para crear imágenes personalizadas?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images) 
- [¿Cómo puedo automatizar el proceso de eliminación de todas las máquinas virtuales en mi laboratorio?](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)
 
## <a name="artifacts"></a>Artefactos 
 
- [¿Qué son artefactos?](#what-are-artifacts) 
 
## <a name="lab-configuration"></a>Configuración de la práctica 
 
- [¿Cómo creo un laboratorio desde una plantilla de administrador de recursos de Azure?](#how-do-i-create-a-lab-from-an-azure-resource-manager-template) 
- [¿Por qué mi máquinas virtuales creadas en distintos grupos de recursos con nombres arbitrarios? ¿Puedo cambiar el nombre o modificar los grupos de recursos?](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups) 
- [¿Cuántos prácticas puedo crear en la misma suscripción?](#how-many-labs-can-i-create-under-the-same-subscription)
- [¿Cuántos VM ¿crear por laboratorio?](#how-many-vms-can-i-create-per-lab)
- [¿Cómo puedo compartir un vínculo directo a mi laboratorio?](#how-do-i-share-a-direct-link-to-my-lab)
- [¿Qué es una cuenta de Microsoft?](#what-is-a-microsoft-account)
 
## <a name="troubleshooting"></a>Solución de problemas 
 
- [Mi muestra error durante la creación de la máquina virtual. ¿Cómo solucionar?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it) 
- [¿Por qué no es mi virtual red existente guardar correctamente?](#why-isnt-my-existing-virtual-network-saving-properly)  

### <a name="what-if-my-question-isnt-answered-here"></a>¿Qué sucede si no se responde aquí mi pregunta?
Si su pregunta no aparece aquí, háganoslo saber y le ayudaremos a encontrar una respuesta.

- Publique una pregunta en el [subproceso Disqus](#comments) al final de estas preguntas más frecuentes y comuníquese con el equipo de caché de Azure y otros miembros de la Comunidad acerca de este artículo.
- Para llegar a una audiencia más amplia, publique una pregunta en el [foro de MSDN de prácticas de Azure DevTest](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)y a continuación, comuníquese con el equipo de Azure DevTest prácticas y otros miembros de la Comunidad.
- Para realizar una solicitud de característica, enviar sus solicitudes e ideas a la [Voz de usuario de prácticas de Azure DevTest](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="why-should-i-use-azure-devtest-labs"></a>¿Por qué debo usar Azure DevTest prácticas? 
Prácticas de Azure DevTest puede guardar su grupo tiempo y dinero. Los programadores pueden crear sus propios entornos con varias bases de datos diferentes y usar artefactos rápidamente implementar y configurar las aplicaciones. Usar fórmulas e imágenes personalizadas, puede guardados como plantillas máquinas virtuales y reproducirlo fácilmente. Además, prácticas ofrecen varias directivas configurables que permiten a los administradores de laboratorio reducir residuos y administrar entornos del equipo. Estas directivas incluyen umbral de costo, máximas máquinas virtuales por usuario y el tamaño máximo de máquina virtual de apagado automático. Para obtener una explicación más en profundidad de Azure DevTest prácticas, lea la [información general](devtest-lab-overview.md) o consulte el [vídeo de introducción](/documentation/videos/videos/what-is-azure-devtest-labs). 

### <a name="what-does-worry-free-self-service-mean"></a>¿Qué significa "preocupaciones, autoservicio"?
"Preocupaciones, autoservicio" significa que los desarrolladores y comprobadores crean sus propios entornos según sea necesario, y los administradores tienen la seguridad de saber que ayuda a Azure DevTest prácticas minimizar residuos y controlar los costos. Los administradores pueden especificar los tamaños VM permitidos, el número máximo de VM, y cuándo se inician y apagar máquinas virtuales. Prácticas de Azure DevTest también facilita la supervisión de costos y establecer alertas para estar al tanto de cómo se utilizan los recursos en la práctica. 

### <a name="how-can-i-use-azure-devtest-labs"></a>¿Cómo puedo utilizar prácticas de DevTest de Azure? 
Prácticas de Azure DevTest es útil siempre que requiere desarrollo o entornos de prueba y desea reproducir rápidamente o administrarlos con las directivas de ahorro. 

Estas son algunas situaciones que nuestros clientes usan Azure DevTest prácticas de: 

- Administrar los entornos de prueba y desarrollo en un único lugar, utilizando las directivas para reducir el costo y las imágenes personalizadas para compartir crea entre el equipo.
- Desarrollo de aplicaciones con imágenes personalizadas para guardar el estado del disco en todas las etapas de desarrollo.
- Seguimiento del coste en relación con el progreso. 
- Crear entornos de prueba masivo de calidad.
- Usar artefactos y fórmulas para configurar fácilmente y reproducir una aplicación en distintos entornos. 
- Distribuir máquinas virtuales de hackathons (trabajo colaborativo de desarrollo o prueba) y, a continuación, fácilmente eliminar ellos cuando termina el evento. 

### <a name="how-am-i-billed-for-azure-devtest-labs"></a>¿Cómo me facturado Azure DevTest prácticas 
Prácticas de Azure DevTest es un servicio gratuito, lo que significa que las prácticas de creación y configuración de directivas, plantillas y artefactos son gratuita. Pagar solo para los recursos de Azure utilizados dentro de su laboratorio, como máquinas virtuales, cuentas de almacenamiento y redes virtuales. Para obtener más información sobre el costo de recursos de práctica, lea acerca de [precios de Azure DevTest prácticas](https://azure.microsoft.com/pricing/details/devtest-lab/). 

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs"></a>¿Cuáles son los diferentes niveles de seguridad en Azure DevTest prácticas?  
Acceso a la seguridad depende de [Control de acceso de Azure Role-Based (RBAC)](../active-directory/role-based-access-built-in-roles.md). Para comprender cómo funciona el acceso, le ayuda a comprender las diferencias entre un permiso, una función y un ámbito definida por RBAC.

- **Permisos** - un permiso es un acceso definido para una acción específica. Por ejemplo, un permiso podría ser acceso de lectura a virtual todos los equipos. 
- **Función** - una función es un conjunto de permisos que se pueden agrupar y asignado a un usuario. Por ejemplo, "propietario de suscripción" tiene acceso a todos los recursos dentro de una suscripción. 
- **Ámbito** - un ámbito es un nivel de la jerarquía de recursos de Azure. Por ejemplo, podría ser un ámbito de un grupo de recursos o un único laboratorio o toda la suscripción. 
 
En el ámbito de Azure DevTest prácticas, existen dos tipos de funciones para definir permisos de usuario: usuario de laboratorio y propietario de práctica.

- **Propietario de laboratorio** - propietario de un laboratorio tiene el acceso a los recursos dentro de la práctica. Por lo tanto, puede modificar las directivas, leer y escribir cualquier VM, cambiar la red virtual y así sucesivamente. 
- **Usuario laboratorio** - un usuario laboratorio puede ver todos los recursos de laboratorio, como máquinas virtuales, directivas y redes virtuales, pero no pueden modificar directivas o cualquier máquinas virtuales creadas por otros usuarios. También es posible crear funciones personalizadas en Azure DevTest prácticas y aprenda cómo hacer en el artículo, [conceder permisos de usuario a las directivas de laboratorio específico](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). 
 
Dado que ámbitos jerárquicos, cuando un usuario tiene permisos en un determinado ámbito, se les concede automáticamente los permisos en cada ámbito de nivel inferior incluido. Por ejemplo, si un usuario se asigna a la función de propietario de la suscripción, a continuación, que tienen acceso a todos los recursos en una suscripción. Estos recursos incluyen todos los equipos virtuales, todas las redes virtuales y todas las prácticas. Por lo tanto, un propietario de la suscripción hereda automáticamente el rol de propietario de la práctica. Sin embargo, lo contrario no es true. El propietario de un laboratorio tiene acceso a un laboratorio, que es un ámbito inferior que el nivel de suscripción. Por lo tanto, un propietario de práctica no está vean máquinas virtuales o redes virtuales o todos los recursos que están fuera de la práctica. 

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>¿Cómo creo un rol para permitir a los usuarios realizar una tarea específica?
Aquí encontrará un completo artículo acerca de cómo crear funciones personalizadas y asignar permisos a esa función. Aquí tiene un ejemplo de un script que crea el rol "DevTest prácticas avanzadas" usuario, que tiene permiso para iniciar y detener todas las máquinas virtuales de la práctica:
 
    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User" 
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
    $policyRoleDef.Id = $null 
    $policyRoleDef.Name = "DevTest Labs Advance User" 
    $policyRoleDef.IsCustom = $true 
    $policyRoleDef.AssignableScopes.Clear() 
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  
 
### <a name="does-azure-devtest-labs-integrate-with-my-cicd-toolchain"></a>¿Azure DevTest prácticas se integra con mi toolchain de elemento de configuración o CD? 
Si está utilizando VSTS, hay una [extensión de Azure DevTest prácticas tareas](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) que le permite automatizar la canalización de lanzamiento en Azure DevTest prácticas. Algunos de los usos de esta extensión son:

- Crear e implementar una máquina virtual automáticamente y configurar con la versión más reciente con la copia del archivo de Azure o PowerShell VSTS tareas. 
- Capturar automáticamente el estado de una máquina virtual después de probar para reproducir un error en la misma VM para investigación adicional. 
- Eliminar la máquina virtual al final de la canalización de lanzamiento cuando ya no es necesario. 

Las siguientes entradas de blog proporcionan orientación e información sobre el uso de la extensión VSTS:
 
- [Prácticas de Azure DevTest: extensión VSTS](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/) 
- [Implementar una nueva máquina virtual en un AzureDevTestLab existente de VSTS](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) 
- [Con la administración de la versión VSTS para implementaciones continuas a AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) 
 
Para otros toolchains CD o elemento de configuración, todos los escenarios mencionados anteriormente pueden conseguirse a través de la extensión de tareas VSTS pueden conseguirse de forma similar a través de implementar [plantillas de administrador de recursos de Azure](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) usar [cmdlets de PowerShell de Azure](../resource-group-template-deploy.md) y [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). También puede usar [Las API de REST DevTest prácticas](http://aka.ms/dtlrestapis) para integrar con su toolchain.  

### <a name="why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs"></a>¿Por qué no puedo ver determinadas máquinas virtuales en el módulo de máquinas virtuales de Azure aparece dentro de Azure DevTest prácticas?
Cuando se crea una máquina virtual en Azure DevTest prácticas, se concede permiso para tener acceso a esa máquina virtual. Es posible para verla en el módulo de prácticas y en el módulo de **máquinas virtuales de Windows** . Los usuarios de la función DevTest prácticas pueden ver todos los equipos virtuales que creó en la práctica a través del módulo de **todas las máquinas virtuales** de la práctica. Sin embargo, los usuarios de la función DevTest prácticas no reciben automáticamente el acceso de lectura a los recursos de máquina virtual que han creado otros usuarios. Por lo tanto, esas máquinas virtuales no se muestran en el módulo de **máquinas virtuales de Windows** . 

### <a name="what-is-the-difference-between-custom-images-and-formulas"></a>¿Cuál es la diferencia entre imágenes personalizadas y las fórmulas? 
Una imagen personalizada es un disco duro virtual (disco duro virtual), mientras que una fórmula es una imagen que puede configurar con opciones adicionales que puede guardar y reproducir. Una imagen personalizada puede ser preferible si desea crear rápidamente varios entornos con la misma imagen básica, inmutable. Una fórmula puede ser mejor si desea reproducir la configuración de la máquina virtual con los bits más recientes, una subred virtual o un tamaño específico. Para obtener una explicación más detallada, consulte el artículo [imágenes personalizadas de comparación y las fórmulas en las prácticas de DevTest](devtest-lab-comparing-vm-base-image-types.md). 
 
### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>¿Cómo creo varias VM desde la misma plantilla a la vez? 
Puede usar la [extensión de las tareas VSTS](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) o [generar una plantilla de administrador de recursos de Azure](devtest-lab-add-vm-with-artifacts.md#save-arm-template) al crear una máquina virtual e [implementar la plantilla de administrador de recursos de Azure desde Windows PowerShell](../resource-group-template-deploy.md). 
 
### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab"></a>¿Cómo puedo mover mi máquinas virtuales de Azure existentes en mi laboratorio de prácticas de DevTest de Azure? 
Nos estamos diseñar una solución directamente mover máquinas virtuales a Azure DevTest prácticas, pero actualmente copiar sus máquinas virtuales existentes a Azure DevTest prácticas como sigue: 

1. Copie el archivo de disco duro virtual de la máquina virtual existente con este [script de Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1) 
1. [Crear la imagen personalizada](devtest-lab-create-template.md) dentro de su laboratorio de prácticas de DevTest de Azure. 
1. Crear una máquina virtual en la práctica de la imagen personalizada 
 
### <a name="can-i-attach-multiple-disks-to-my-vms"></a>¿Puedo adjuntar varios discos a mi VM? 
Asociar varios discos a máquinas virtuales es compatible.  
 
### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>¿Cómo puedo automatizar el proceso de carga de archivos de disco duro virtual para crear imágenes personalizadas? 
Existen dos opciones:

- [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) puede utilizarse para copiar o cargar archivos de disco duro virtual a la cuenta de almacenamiento asociada con la práctica.
- [Explorador de Microsoft Azure almacenamiento](../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente que se ejecuta en Windows, OSX y Linux.   
 
Para buscar la cuenta de almacenamiento de destino asociada con la práctica, siga estos pasos:

1. Inicie sesión en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). 
1. Seleccione **Grupos de recursos** en el panel izquierdo. 
1. Busque y seleccione el grupo de recursos asociado con la práctica. 
1. En el módulo de **información general** , seleccione una de las cuentas de almacenamiento. 
1. Seleccione **BLOB**.
1. Busque cargas en la lista. Si no existe ninguna, vuelva al paso 4 y pruebe otra cuenta de almacenamiento.
1. Usar la **dirección URL** como el destino en el comando AzCopy.


### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>¿Cómo puedo automatizar el proceso de eliminación de todas las máquinas virtuales en mi laboratorio?

Además de eliminar máquinas virtuales de la práctica en el portal de Azure, puede eliminar todas las máquinas virtuales en su laboratorio mediante un script de PowerShell. En el ejemplo siguiente, basta con modificar los valores de parámetro en el comentario **para cambiar los valores** . Puede recuperar el `subscriptionId`, `labResourceGroup`, y `labName` valores que aparecen en el módulo de práctica en el portal de Azure. 


    # Delete all the VMs in a lab
    
    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount
    
    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    
    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object { 
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}
    
    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts"></a>¿Qué son artefactos? 
Artefactos son elementos personalizables que se pueden usar para implementar los bits más recientes o las herramientas de desarrollo en una máquina virtual. Están conectados a su máquina virtual durante la creación con unos pocos clics simples y una vez que se aprovisiona la máquina virtual, los artefactos implementación y configurar la máquina virtual. Hay varios artefactos preexistentes en nuestro [repositorio Github público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), pero también fácilmente puede [crear sus propio artefactos](devtest-lab-artifact-author.md). 

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template"></a>¿Cómo creo un laboratorio desde una plantilla de administrador de recursos de Azure? 
Le proporcionamos un [repositorio de Github de plantillas de administrador de recursos de Azure laboratorio](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) que se puede implementar como-está o modificar para crear plantillas personalizadas para su prácticas. Cada una de estas plantillas tiene un vínculo que puede hacer clic para implementar la práctica como-en su propia suscripción Azure, o puede personalizar la plantilla e [implementar con PowerShell o CLI de Azure](../resource-group-template-deploy.md).
 
### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>¿Por qué mi máquinas virtuales creadas en distintos grupos de recursos con nombres arbitrarios? ¿Puedo cambiar el nombre o modificar los grupos de recursos? 
Grupos de recursos se crean de este modo en orden de prácticas de Azure DevTest administrar los permisos de usuario y el acceso a máquinas virtuales. Aunque puede mover la máquina virtual a otro grupo de recursos con el nombre que desee, hacerlo así que no es recomendable. Estamos trabajando para mejorar la experiencia para permitir una mayor flexibilidad.   
 
### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>¿Cuántos prácticas puedo crear en la misma suscripción? 
No hay ningún límite específico en el número de prácticas que pueden crearse por suscripción. Sin embargo, los recursos utilizados están limitados por suscripción. Puede leer sobre las [cuotas y límites imponen en suscripciones Azure](../azure-subscription-service-limits.md) y [cómo aumentar estos límites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests). 
 
### <a name="how-many-vms-can-i-create-per-lab"></a>¿Cuántos VM ¿crear por laboratorio? 
No hay ningún límite específico en el número de máquinas virtuales que pueden crearse por laboratorio. Sin embargo, actualmente la práctica es compatible con solo unos 40 máquinas virtuales con al mismo tiempo en almacenamiento estándar y 25 máquinas virtuales ejecutándose a la vez en el almacenamiento de premium. Estamos trabajando actualmente en aumento estos límites. 

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>¿Cómo puedo compartir un vínculo directo a mi laboratorio?

Para compartir un vínculo directo a los usuarios de laboratorio puede realizar el procedimiento siguiente.

1. Vaya a la práctica en el portal de Azure.
2. Copie la dirección URL de laboratorio desde el explorador y compartirla con los usuarios de práctica. 

>[AZURE.NOTE] Si los usuarios de laboratorio son los usuarios externos con una [cuenta MSA](#what-is-a-microsoft-account) y que no pertenecen a Active directory de su empresa, puede aparecer un error al explorar el vínculo proporcionado. Si recibe un error, indique a haga clic en su nombre en la esquina superior derecha del portal de Azure y seleccione el directorio donde existe el laboratorio de la sección **Directory** del menú.

### <a name="what-is-a-microsoft-account"></a>¿Qué es una cuenta de Microsoft?

Una cuenta de Microsoft es lo que se utiliza para casi todas las que tareas con los servicios y dispositivos de Microsoft. Es una dirección de correo electrónico y contraseña que utiliza para iniciar sesión en Skype, Outlook.com, OneDrive, Windows Phone y Xbox LIVE – y significa que sus archivos, fotos, contactos y configuraciones pueden seguir para cualquier dispositivo. 

>[AZURE.NOTE] Cuenta de Microsoft utiliza al que se llama "De Windows Live ID".
 
### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Mi muestra error durante la creación de la máquina virtual. ¿Cómo solucionar? 
Para obtener información sobre cómo obtener registros de la muestra error, consulte [cómo solucionar errores defectos en AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) - escrito por uno de nuestros MVP - esta entrada de blog. 
 
### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>¿Por qué no es mi virtual red existente guardar correctamente?  
Una posibilidad es que el nombre de red virtual contiene períodos. Si es así, pruebe a quitar los períodos o reemplazándolos con guiones y, a continuación, intente guardar la red virtual de nuevo.
