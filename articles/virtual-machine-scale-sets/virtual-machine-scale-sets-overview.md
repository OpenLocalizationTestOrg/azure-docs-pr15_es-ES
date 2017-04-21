<properties
    pageTitle="Información general sobre conjuntos de máquina virtual escala | Microsoft Azure"
    description="Más información acerca de los conjuntos de escala de máquina Virtual"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/13/2016"
    ms.author="guybo"/>

# <a name="virtual-machine-scale-sets-overview"></a>Información general de conjuntos de escala de máquina virtual

Conjuntos de escala de máquina virtual son un recurso de Azure calcular que puede usar para implementar y administrar un conjunto de máquinas virtuales idénticos. Con todas las máquinas virtuales configuradas la misma escala VM conjuntos están diseñados para admitir Autoescala true-sin previo aprovisionamiento de máquinas virtuales es necesaria y como tales facilita la generar servicios a gran escala destino cálculo grande, datos grandes y contenedores de cargas de trabajo.

Para las aplicaciones que necesita escalar recursos de cálculo y en escala de operaciones están equilibradas implícitamente en errores y actualización de dominios. Para obtener una introducción a la escala VM conjuntos consulte el [blog de Azure anuncio](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Eche un vistazo a estos vídeos para obtener más información acerca de los conjuntos de escala VM:

 - [Marcar Russinovich habla conjuntos de escala de Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  

 - [Conjuntos de escala de la máquina virtual con chico Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>Conjuntos de creación y administración de la escala VM

Puede crear un conjunto de escala de VM en el [portal de Azure](https://portal.azure.com) seleccionando _nuevo_ y escribiendo en "escala" en la barra de búsqueda. Verá "máquina Virtual escala" establecer en los resultados. Desde allí puede rellenar los campos necesarios para personalizar y distribuir el conjunto de escala. 

Escala VM conjuntos también se pueden definir e implementan mediante plantillas JSON y [Las API de REST](https://msdn.microsoft.com/library/mt589023.aspx) simplemente como máquinas virtuales de administrador de recursos individuales de Azure. Por lo tanto, pueden utilizarse cualquier métodos de implementación de administrador de recursos de Azure estándar. Para obtener más información sobre las plantillas, vea [plantillas de administrador de recursos de Azure de creación](../resource-group-authoring-templates.md).

Un conjunto de plantillas de ejemplo para los conjuntos de escala VM puede encontrarse en el repositorio de GitHub de plantillas de Azure tutorial [aquí.](https://github.com/Azure/azure-quickstart-templates) (busque plantillas con _vmss_ en el título)

En las páginas de detalles de estas plantillas, verá un botón que se vincula a la característica de implementación de portal. Para implementar el conjunto de escala de la máquina virtual, haga clic en el botón y, después, rellene los parámetros que son necesarios en el portal. Si no está seguro de si un recurso es compatible con superior o mayúsculas y minúsculas es más seguro usar siempre los valores de parámetro de minúsculas. También es una práctica disección de vídeo de una plantilla de conjunto de escala VM aquí:

[Escala VM establece disección de plantilla](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>Ajuste de escala de una escala VM establece y en

Para aumentar o disminuir el número de máquinas virtuales de un conjunto de escala VM, cambie la propiedad de _capacidad_ y volver a la plantilla. Este simplificar facilita la escritura a su propia capa de escala personalizada si desea definir eventos de escala personalizada que no son compatibles con Azure Autoescala.

Si va a redistribuir una plantilla para cambiar la capacidad, puede definir una plantilla mucho más pequeña que incluye solo el SKU y la capacidad actualizada. Se muestra un ejemplo de este [aquí.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)

Para seguir los pasos que se crea un conjunto de escala que se ajusta automáticamente, vea [Automáticamente los equipos de escala de un conjunto de escala de máquina Virtual](virtual-machine-scale-sets-windows-autoscale.md)

## <a name="monitoring-your-vm-scale-set"></a>Establece la supervisión de su escala VM

Los conjuntos de escala de listas de [portal de Azure](https://portal.azure.com) y muestra las propiedades básicas, así como listado máquinas virtuales en el conjunto. Puede usar el [Explorador de recursos de Azure](https://resources.azure.com) para ver los conjuntos de escala de máquina virtual para obtener más detalles. Conjuntos de escala VM son un recurso en Microsoft.Compute, para este sitio pueda ver, expanda los siguientes vínculos:

    subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## <a name="vm-scale-set-scenarios"></a>Escala VM establece escenarios

Esta sección muestra algunos escenarios típicos de conjunto de escala de máquina virtual. Algunos servicios de Azure nivel superior (por ejemplo, lote, tela de servicio, servicio de contenedor de Azure) usará estos escenarios.

 - **RDP / SSH a escala VM establece instancias** - VM A escala conjunto se crea dentro de un VNET y máquinas virtuales individuales en el conjunto de escala no se asignan direcciones IP públicas. Esta es una buena porque generalmente no desea la sobrecarga de gastos y administración de la asignación de direcciones IP independientes para todos los recursos independientes en la cuadrícula de cálculo y, a continuación, puede conectarse fácilmente a estas máquinas virtuales de otros recursos en su VNET los que tienen las direcciones IP públicas como equilibradores de carga o máquinas virtuales de independiente incluidos.

 - **Conectarse a máquinas virtuales usando las reglas NAT** - puede crear una dirección IP pública, lo asigna a un equilibrador de carga y definir reglas NAT entrantes que asignan un puerto en la dirección IP a un puerto en una máquina virtual en el conjunto de escala de la máquina virtual. Por ejemplo:
 
    Origen | Puerto de origen | Destino | Puerto de destino
    --- | --- | --- | ---
    IP pública | Puerto 50000 | vmss\_0 | Puerto 22
    IP pública | Puerto 50001 | vmss\_1 | Puerto 22
    IP pública | Puerto 50002 | vmss\_2 | Puerto 22

    Aquí tenemos un ejemplo de la creación de un conjunto de escala de máquina virtual que usa reglas NAT Habilitar conexión SSH para cada VM en una escala de establecer mediante una sola IP pública: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

    Aquí tenemos un ejemplo de hacer lo mismo con RDP y Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

 - **Conectarse a máquinas virtuales usando un "jumpbox"** - si crea una escala VM establecer y una máquina virtual en el mismo VNET, el VM independiente y el conjunto de escala de máquina virtual que VM pueden conectar entre sí mediante su IP interna independiente direcciones definida por el VNET o subred. Si crea una dirección IP pública y asignarlo a la VM independiente puede RDP o SSH a la VM independiente y, a continuación, conectarse desde ese equipo a las instancias de conjunto de escala de máquina virtual. En este momento quizás observe que un conjunto de escala VM simple es inherente más seguro que una simple independiente VM con una dirección IP pública en su configuración predeterminada.

    [Para obtener un ejemplo de este método, esta plantilla crea un clúster de Mesos simple formada por una VM patrón que administra un clúster basadas en VM conjunto de escala de VM independiente.](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **Equilibrio de carga en una escala VM establece instancias** - si desea ofrecer trabajo a un clúster de cálculo de máquinas virtuales con un enfoque "turnos", puede configurar un equilibrador de carga de Azure con reglas de equilibrio de carga en consecuencia. Puede definir sondeos para comprobar que la aplicación se está ejecutando haciendo ping puertos con una ruta de acceso de protocolo, intervalo y solicitud especificada. La [Puerta de enlace de aplicación](https://azure.microsoft.com/services/application-gateway/) de Azure también es compatible con conjuntos de escala, junto con escenarios de equilibrio de carga más complejas.

    [Aquí tenemos un ejemplo que crea un conjunto de escala de la máquina virtual de máquinas virtuales con el servidor web de IIS y usa un equilibrador de carga para equilibrar la carga que recibe cada máquina virtual. También se usa el protocolo HTTP para hacer ping a una dirección URL específica en cada máquina virtual.](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) (vea el tipo de recurso Microsoft.Network/loadBalancers y los networkProfile y extensionProfile en la virtualMachineScaleSet)

 - **Implementar una escala VM establece como un clúster de cálculo en un administrador de clúster de PaaS** - escala VM conjuntos a veces se describen como una función de trabajador de la última generación. Es una descripción válida pero también funciona el riesgo de escala confusa conjunto de características con características de funciones de trabajo de PaaS v1. En un sentido escala VM conjuntos proporcionan un verdadero "función de trabajador" o un recurso de trabajo, ya que proporcionan un recurso de cálculo generalizado que es la plataforma/tiempo de ejecución independiente, personalizables y se integra en Azure Administrador de recursos IaaS.

    Una función de trabajador de v1 PaaS, mientras limitada en términos de soporte de plataforma/runtime (sólo imágenes de plataforma de Windows) también incluye servicios como intercambiar VIP, configuración de actualización, configuración específica de tiempo de ejecución/aplicación implementación que no _todavía_ disponible en la máquina virtual escalar conjuntos o se van a entregar por otros servicios de PaaS de nivel superior como servicio tela. Con esto en cuenta que puede mirar conjuntos de escala de máquina virtual como una infraestructura que admite PaaS. Es decir Soluciones de PaaS como servicio tela o administradores de clúster como Mesos pueden generar sobre VM escalar conjuntos como una capa scalable cálculo.

    [Para obtener un ejemplo de este método, esta plantilla crea un clúster de Mesos simple formada por una VM patrón que administra un clúster basadas en VM conjunto de escala de VM independiente.](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json) Las versiones futuras del [Servicio del contenedor de Azure](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) implementará versiones más complejo y consolidado de este escenario basada en conjuntos de escala de máquina virtual.

## <a name="vm-scale-set-performance-and-scale-guidance"></a>Conjunto de escala rendimiento y escala orientación de VM

- No crear máquinas virtuales de más de 500 en varios conjuntos de escala de VM a la vez.
- Plan para máquinas virtuales de no más de 20 por cuenta de almacenamiento (a menos que establezca la propiedad _overprovision_ en "falso", en cuyo caso se puede ir al 40).
- Separar los las primeras letras de los nombres de cuenta de almacenamiento tanto como sea posibles.  Las plantillas de ejemplo VMSS en [plantillas de Azure tutorial](https://github.com/Azure/azure-quickstart-templates/) proporcionan ejemplos de cómo hacerlo.
- Si usa máquinas virtuales personalizadas, planear no más de 40 máquinas virtuales por conjunto de escala VM, en una cuenta de almacenamiento única.  Necesitará la imagen copiada previamente en la cuenta de almacenamiento antes de comenzar la implementación de conjunto de escala de máquina virtual. Consulte las preguntas más frecuentes para obtener más información.
- Plan de no más de 4096 máquinas virtuales por VNET.
- El número de máquinas virtuales que puede crear está limitado por la cuota de core en la región en la que está implementando. Debe ponerse en contacto con soporte al cliente para aumentar el límite de cuota de cálculo aumentado incluso si tiene un límite de alto de núcleos para su uso con servicios de nube o IaaS v1 hoy. Para consultar la cuota puede ejecutar el siguiente comando CLI Azure: `azure vm list-usage`y el siguiente comando PowerShell: `Get-AzureRmVMUsage` (si usa una versión de PowerShell debajo de 1,0 uso `Get-AzureVMUsage`).

## <a name="vm-scale-set-frequently-asked-questions"></a>Escala VM establecer preguntas más frecuentes

**PREGUNTAS.** ¿Máquinas virtuales de cuántos pueden tener en un conjunto de escala VM?

**A.** 100 si usa imágenes de plataforma que pueden distribuirse entre varias cuentas de almacenamiento. Si usa imágenes personalizadas, hasta 40 (si la propiedad _overprovision_ se establece en "false", 20 de forma predeterminada), desde imágenes personalizadas están limitadas actualmente a una cuenta de almacenamiento única.

**Preguntas** qué otros límites de recursos existen para conjuntos de escala VM?

**A.** Está limitado a la creación de máquinas virtuales de no más de 500 en varios conjuntos de escala por región durante un período de 10 minutos. El existente [límites de servicio de suscripción de Azure /](../azure-subscription-service-limits.md) aplicar.

**PREGUNTAS.** ¿Son discos de datos admitidos en conjuntos de escala VM?

**A.** No en la versión inicial. Las opciones de almacenamiento de datos son:

- Archivos de Azure (unidades SMB compartido)

- Unidad de sistema operativo

- Unidad TEMP (local, no se copia en el almacenamiento de Azure)

- Servicio de datos de Azure (por ejemplo, tablas de Azure, Azure BLOB)

- Servicio de datos externos (por ejemplo, remoto DB)

**PREGUNTAS.** ¿Qué regiones Azure son compatibles con conjuntos de escala VM?

**A.** Cualquier región que admite el Administrador de recursos de Azure es compatible con conjuntos de escala de máquina virtual.

**PREGUNTAS.** ¿Cómo se crea una escala VM establezca con una imagen personalizada?

**A.** Deje la propiedad vhdContainers en blanco, por ejemplo:

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": "https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd"
            },
            "osType": "Windows"
        }
    },


**PREGUNTAS.** ¿Si reducir mi escala VM establece la capacidad de 20 a 15, lo que se quitarán máquinas virtuales?

**A.** Máquinas virtuales se quitan de la escala de configurar uniformemente entre dominios de actualización y errores para maximizar la disponibilidad. Máquinas virtuales con el mayor identificador se quitan en primer lugar.

**PREGUNTAS.** ¿Qué en caso de que aumente la capacidad de 15 a 18?

**A.** Si aumenta la capacidad de 18, se creará 3 nuevas máquinas virtuales. Cada vez que se incrementará el identificador de instancia VM desde el valor más alto anterior (por ejemplo, 20, 21, 22). Máquinas virtuales se equilibran entre FDs y UDs.

**PREGUNTAS.** Si usa varias extensiones en un conjunto de escala de la máquina virtual, ¿puedo exigir una secuencia de ejecución?

**A.** No directamente, pero para la extensión customScript, la secuencia de comandos podría esperar otra extensión completar ([por ejemplo, supervisar el registro de extensión](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). Encontrará instrucciones adicionales en la secuencia de extensión en esta entrada de blog: [Secuencia de extensión en conjuntos de Azure VM escala](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).

**PREGUNTAS.** ¿Conjuntos de escala VM funcionan con conjuntos de disponibilidad de Azure?

**A.** Sí. Un conjunto de escala VM es una disponibilidad implícita conjunto con 5 FDs y 5 UDs. No es necesario configurar nada en virtualMachineProfile. En futuras versiones, conjuntos de escala VM están probable que abarcan a varios inquilinos, pero ahora establece una escala es un conjunto único de disponibilidad.
