<properties
   pageTitle="Implementación del Administrador de recursos y clásica | Microsoft Azure"
   description="Describe las diferencias entre el modelo de implementación de administrador de recursos y clásica (o administración del servicio) modelo de implementación."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/27/2016"
   ms.author="tomfitz"/>

# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Administrador de recursos de Azure frente a la implementación clásica: comprender los modelos de implementación y el estado de los recursos

En este tema, obtenga información sobre el Administrador de recursos de Azure y los modelos de implementación clásica, el estado de los recursos, y por qué se implementaron los recursos con uno o el otro. El Administrador de recursos y los modelos de implementación clásica representan dos formas diferentes de implementar y administrar las soluciones de Azure. Trabajar con ellos a través de dos conjuntos de API diferentes, y los recursos implementados pueden contener diferencias importantes. Los dos modelos no son totalmente compatibles entre sí. Este tema describe las diferencias.

Para simplificar la implementación y administración de recursos, Microsoft recomienda usar el Administrador de recursos para todos los recursos nuevos. Si es posible, Microsoft recomienda que vuelva a implementar los recursos existentes a través del Administrador de recursos.

Si es nuevo en el Administrador de recursos, desea revisar la terminología definida en la [información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md).

## <a name="history-of-the-deployment-models"></a>Historial de los modelos de implementación

Azure había proporcionada originalmente el modelo de implementación clásico. En este modelo, cada recurso existía independencia. no había forma Agrupar recursos relacionados. En su lugar, había que realizar un seguimiento de los recursos que formado de su solución o aplicación manualmente y recuerde administrarlos en un planteamiento coordinado. Para implementar una solución, había que crear cada recurso individualmente a través del portal clásico o crear un script que implementa todos los recursos en el orden correcto. Para eliminar una solución, había que eliminar de forma individual cada recurso. Puede aplicar y actualizar las directivas de control de acceso para los recursos relacionados no fácilmente. Por último, no se puede aplicar etiquetas a los recursos para etiquetarlas con términos que le ayudarán a supervisar los recursos y administrar la facturación.

En 2014 Azure presentó al administrador de recursos, que agrega el concepto de un grupo de recursos. Un grupo de recursos es un contenedor de recursos que comparten un ciclo de vida comunes. El modelo de implementación de administrador de recursos proporciona varias ventajas:

- Puede implementar, administrar y supervisar todos los servicios para la solución como un grupo, en lugar de administración de estos servicios individualmente.
- Varias veces, puede implementar la solución a lo largo de su ciclo de vida y tiene confianza que se implementan los recursos en un estado coherente.
- Puede aplicar control de acceso a todos los recursos en el grupo de recursos y estas directivas se aplican automáticamente cuando se agregan nuevos recursos al grupo de recursos.
- Puede aplicar etiquetas a los recursos para organizar lógicamente todos los recursos de la suscripción.
- Puede usar la notación de objetos JavaScript (JSON) para definir la infraestructura para la solución. El archivo JSON se conoce como una plantilla de administrador de recursos.
- Puede definir las dependencias entre los recursos para que se implementen en el orden correcto.

Cuando se agregó el Administrador de recursos, todos los recursos retroactivamente se agregaron a grupos de recursos de forma predeterminada. Si crea un recurso a través de implementación clásica ahora, el recurso se crea automáticamente dentro de un grupo de recursos predeterminado de ese servicio, aunque no se ha especificado ese grupo de recursos en la implementación. Sin embargo, simplemente existentes en un grupo de recursos no significan que el recurso se ha convertido en el modelo de administrador de recursos. Veremos cómo cada servicio trata los modelos de implementación de dos en la siguiente sección. 

## <a name="understand-support-for-the-models"></a>Comprender la compatibilidad con los modelos 

Al decidir qué modelo de implementación que se usa para los recursos, hay tres escenarios que debería considerar:

1. El servicio es compatible con el Administrador de recursos y proporciona un único tipo.
2. El servicio Administrador de recursos pero proporciona dos tipos: uno para el Administrador de recursos y otro para clásico. Este escenario se aplica solo a máquinas virtuales, cuentas de almacenamiento y redes virtuales.
3. El servicio no admite el Administrador de recursos.

Para averiguar si un servicio admite el Administrador de recursos, consulte [proveedores compatibles con el Administrador de recursos](resource-manager-supported-services.md).

Si el servicio que desea usar no admite el Administrador de recursos, debe continuar con la implementación del clásico.

Si es compatible con el servicio Administrador de recursos y **no es** una máquina virtual, cuenta de almacenamiento o una red virtual, puede usar el Administrador de recursos sin las complicaciones.

Para equipos virtuales, cuentas de almacenamiento y redes virtuales, si el recurso se ha creado a través de implementación clásica, deberá seguir funcionando en él a través de operaciones clásicas. Si la máquina virtual, la cuenta de almacenamiento o la red virtual creada a través de la implementación del Administrador de recursos, deberá seguir utilizando operaciones de administrador de recursos. Esta distinción puede resultar confusa cuando su suscripción contiene una mezcla de recursos creados mediante el Administrador de recursos y de implementación clásico. Esta combinación de recursos puede crear resultados inesperados porque los recursos no admite las mismas operaciones.

En algunos casos, un comando de administrador de recursos puede recuperar información sobre un recurso creado a través de implementación clásica, o puede realizar una tarea administrativa, como mover un recurso clásico a otro grupo de recursos. Sin embargo, estos casos deben dar la impresión de que el tipo admite operaciones del Administrador de recursos. Por ejemplo, suponga que tiene un grupo de recursos que contiene una máquina virtual que se creó con implementación clásico. Si ejecuta el siguiente comando PowerShell Administrador de recursos:

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

Devuelve la máquina virtual:
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

Sin embargo, el Administrador de recursos del cmdlet **Get-AzureRmVM** devuelve solo máquinas virtuales implementados mediante el Administrador de recursos. El siguiente comando no devuelve la máquina virtual creada a través de implementación clásico.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

Solo los recursos creados a través de las etiquetas de soporte técnico de administrador de recursos. No se puede aplicar etiquetas a los recursos clásicos.

## <a name="resource-manager-characteristics"></a>Características del Administrador de recursos

Para ayudarle a entender los dos modelos, vamos a revisar las características de los tipos de administrador de recursos:

- Creado a través del [portal de Azure](https://portal.azure.com/).

     ![Portal de Azure](./media/resource-manager-deployment-model/portal.png)

     Para el cálculo, almacenamiento y recursos de red, tiene la opción de uso de la implementación del Administrador de recursos o clásico. Seleccione **El Administrador de recursos**.

     ![Implementación del Administrador de recursos](./media/resource-manager-deployment-model/select-resource-manager.png)

- Creado con la versión de administrador de recursos de los cmdlets de PowerShell de Azure. Estos comandos tienen el formato *AzureRmNoun verbo*.

        New-AzureRmResourceGroupDeployment

- Creado a través de la [REST API de administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) para operaciones de resto.

- Creado a través de comandos de Azure CLI ejecutar en el modo de **arm** .

        azure config mode arm
        azure group deployment create 

- El tipo de recurso no incluye **(clásico)** en el nombre. La imagen siguiente muestra el tipo de cuenta de **almacenamiento**.

    ![aplicación Web](./media/resource-manager-deployment-model/resource-manager-type.png)

## <a name="classic-deployment-characteristics"></a>Características de implementación clásica

También puede saber el modelo de implementación clásico como el modelo de administración de servicio.

Recursos creados en el modelo de implementación clásica comparten las siguientes características:

- Creado a través del [portal clásica](https://manage.windowsazure.com)

     ![Portal clásica](./media/resource-manager-deployment-model/classic-portal.png)

     O bien, el portal de Azure y especifica implementación **clásica** (para el cálculo, almacenamiento y redes).

     ![Implementación clásica](./media/resource-manager-deployment-model/select-classic.png)

- Creado a través de la versión de administración de servicios de los cmdlets de PowerShell de Azure. Estos nombres de comando tienen el formato *AzureNoun verbo*.

        New-AzureVM 

- Creado a través de la [API de REST de administración de servicio](https://msdn.microsoft.com/library/azure/ee460799.aspx) para operaciones de resto.
- Creado a través de los comandos de Azure CLI ejecutar en modo de **asm** .

        azure config mode asm
        azure vm create 

- El tipo de recurso incluye **(clásico)** en el nombre. La imagen siguiente muestra el tipo de cuenta de **almacenamiento (clásico)**.

    ![tipo clásico](./media/resource-manager-deployment-model/classic-type.png)

Puede usar el portal de Azure para administrar los recursos que se crearon a través de implementación clásica.

## <a name="changes-for-compute-network-and-storage"></a>Cambios de proceso, red y almacenamiento

El siguiente diagrama muestra los recursos de proceso, red y almacenamiento implementados mediante el Administrador de recursos.

![Arquitectura de administrador de recursos](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Tenga en cuenta las relaciones entre los recursos siguientes:

- Todos los recursos existen dentro de un grupo de recursos.
- La máquina virtual depende de una cuenta de almacenamiento específico definida en el proveedor de recursos de almacenamiento para almacenar sus discos de almacenamiento de blobs (obligatorio).
- La máquina virtual hace referencia a una NIC específica definida en el proveedor de recursos de red (obligatorio) y una disponibilidad establecer definido en el proveedor de recursos de cálculo (opcional).
- La NIC referencias dirección IP asignada de la máquina virtual (obligatoria), la subred de la red virtual para la máquina virtual (obligatorio) y a un grupo de seguridad de red (opcional).
- La subred dentro de una red virtual hace referencia a un grupo de seguridad de red (opcional).
- El grupo de back-end de direcciones IP que incluye la NIC de una máquina virtual (opcional) y hace referencia a una carga equilibrador pública o privada dirección IP (opcional) hace referencia a la instancia del equilibrador de carga.

Estos son los componentes y sus relaciones para su implementación clásica:

![arquitectura clásica](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

La solución clásica para hospedar una máquina virtual incluye:

- Un servicio de nube requiere que actúa como un contenedor para hospedar máquinas virtuales (cálculo). Máquinas virtuales de Windows se proporcionan automáticamente con una tarjeta de interfaz de red (NIC) y una dirección IP asignada por Azure. Además, el servicio de nube contiene una instancia de equilibrador de carga externo, una dirección IP pública y extremos predeterminados para permitir el tráfico de PowerShell remoto y de escritorio remoto para máquinas virtuales basadas en Windows y Shell seguro (SSH) para máquinas virtuales basadas en Linux.
- Una cuenta de almacenamiento necesario que almacena los VHD de una máquina virtual, incluidos el sistema operativo, los discos de datos temporales y adicionales (almacenamiento).
- Una red virtual opcional que actúa como un contenedor adicional, en el que puede crear una estructura de subred y designar la subred en la que se encuentra la máquina virtual (red).

La siguiente tabla describe los cambios en cómo interactúan los proveedores de recursos de proceso, red y almacenamiento:

 Elemento | Clásico | Administrador de recursos
 ---|---|---
| Servicio de nube para máquinas virtuales |  Servicio de nube era un contenedor para almacenar las máquinas virtuales que requiere la disponibilidad de la plataforma y el equilibrio de carga. | Servicio de nube ya no es necesario para crear una máquina Virtual usando el nuevo modelo de objeto. |
| Redes virtuales | Una red virtual es opcional para la máquina virtual. Si se incluye, no se puede implementar con el Administrador de recursos en la red virtual. | Máquina virtual requiere una red virtual que se ha implementado con el Administrador de recursos. |
| Cuentas de almacenamiento | La máquina virtual requiere una cuenta de almacenamiento que almacena los VHD para el sistema operativo, discos de datos temporales y adicionales. | La máquina virtual requiere una cuenta de almacenamiento para almacenar sus discos de almacenamiento de blobs. |
| Conjuntos de disponibilidad | Disponibilidad de la plataforma se ha indicado mediante la configuración de la misma "AvailabilitySetName" en las máquinas virtuales. El número máximo de dominios de error fue 2. | Conjunto de disponibilidad es un recurso expuesto por el proveedor de Microsoft.Compute. Máquinas virtuales de Windows que requieren alta disponibilidad deben incluirse en el conjunto de disponibilidad. El número máximo de dominios de error ahora es 3. |
| Grupos de afinidad | Grupos de afinidad eran necesarios para crear redes virtuales. Sin embargo, con la introducción de redes virtuales Regional, que no se necesita ya. |Para simplificar, el concepto de grupos de afinidad no existe en la API expuestas por el Administrador de recursos de Azure. |
| Equilibrio de carga    | Creación de un servicio de nube proporciona un equilibrador de carga implícitos para las máquinas virtuales implementado. | El equilibrador de carga es un recurso que expone el proveedor de Microsoft.Network. La interfaz de red principal de los equipos virtuales que debe ser equilibrio de carga debe hacer referencia a equilibrador de carga. Equilibradores de carga pueden ser interna o externa. El grupo de back-end de direcciones IP que incluye la NIC de una máquina virtual (opcional) y hace referencia a una carga equilibrador pública o privada dirección IP (opcional) hace referencia a una instancia de equilibrador de carga. [Obtener más información.](../articles/resource-groups-networking.md) |
|Dirección IP virtual | Servicios de nube Obtén a predeterminado VIP (dirección IP Virtual) cuando se agrega una máquina virtual a un servicio de nube. La dirección IP Virtual es la dirección asociada con el equilibrador de carga implícitos.    | Dirección IP pública es un recurso que expone el proveedor de Microsoft.Network. Puede ser la dirección IP pública (reservado) de estática o dinámica. Dinámica direcciones IP públicas se pueden asignar a un equilibrador de carga. Direcciones IP pública se puede proteger mediante grupos de seguridad. |
|Dirección IP reservada|   Puede reservar una dirección IP en Azure y asociarlo con un servicio de nube para asegurarse de que la dirección IP es rápida.   | Dirección IP pública pueden crearse en modo "Estático" y ofrece la misma capacidad como un "dirección IP reservada". Estático de direcciones IP públicas solo se puede asignar ahora a un equilibrador de carga. |
|Dirección IP pública (puntos) por máquina virtual | Direcciones IP públicas también se pueden asociadas a una máquina virtual directamente. | Dirección IP pública es un recurso que expone el proveedor de Microsoft.Network. Puede ser la dirección IP pública (reservado) de estática o dinámica. Sin embargo, sólo direcciones IP públicas dinámico puede asignarse a una interfaz de red para obtener una dirección IP pública por máquina virtual ahora mismo. |
|Extremos| Entrada extremos necesarias estén configurados en una máquina Virtual que se abra la conectividad de determinados puertos. Uno de los modos comunes de conectarse a máquinas virtuales acabados configurando extremos de entrada. | Las reglas de entrada NAT pueden configurarse en equilibradores de carga para lograr la misma capacidad de activar extremos de puertos específicos para conectarse a las máquinas virtuales. |
|Nombre DNS| Un servicio de nube obtendría un nombre de DNS implícitos globalmente único. Por ejemplo: `mycoffeeshop.cloudapp.net`. | Los nombres DNS son parámetros opcionales que se pueden especificar en un recurso de dirección IP pública. FQDN está en el siguiente formato: `<domainlabel>.<region>.cloudapp.azure.com`. |
|Interfaces de red | Principal y secundario de interfaz de red y sus propiedades se definieron como la configuración de red de una máquina Virtual. | Interfaz de red es un recurso que expone el proveedor de Microsoft.Network. El ciclo de vida de la interfaz de red no está vinculado a una máquina Virtual. Hace referencia a dirección IP asignada la máquina virtual (obligatoria), la subred de la red virtual para la máquina virtual (obligatorio) y a un grupo de seguridad de red (opcional). |

Para obtener información acerca de la conexión de redes virtuales de diferentes modelos de implementación, vea [conectar redes virtuales de diferentes modelos de implementación en el portal](./vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="migrate-from-classic-to-resource-manager"></a>Migrar desde estándar para el Administrador de recursos

Si está listo para migrar los recursos de implementación clásico a la implementación del Administrador de recursos, consulte:

1. [Análisis técnico detallado de migración compatibles con la plataforma de clásico para el Administrador de recursos de Azure](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [Plataformas compatibles con la migración de recursos de IaaS de clásico para el Administrador de recursos de Azure](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Migrar recursos de IaaS de clásico para el Administrador de recursos de Azure con PowerShell de Azure](./virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Migrar los recursos de IaaS de clásico para el Administrador de recursos de Azure mediante CLI de Azure](./virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿Puedo crear una máquina virtual mediante el Administrador de recursos de Azure para implementar en una red virtual o una cuenta de almacenamiento creados con la implementación clásica?**

Esto no es compatible. No puede usar el Administrador de recursos de Azure para implementar una máquina virtual en una red virtual que se creó usando implementación clásico.

**¿Puedo crear una máquina virtual mediante el Administrador de recursos de Azure desde una imagen de usuario que se creó usando las API de administración de servicio de Azure?**

Esto no es compatible. Sin embargo, puede copiar los archivos de disco duro virtual de una cuenta de almacenamiento que se creó usando las API de administración de servicio y agregarlos a una cuenta nueva creada mediante el Administrador de recursos de Azure.

**¿Cuál es el impacto de la cuota de mi suscripción?**

Las cuotas para máquinas virtuales, redes virtuales y cuentas de almacenamiento creadas por el Administrador de recursos de Azure son independientes de otras cuotas. Cada suscripción obtiene cuotas para crear los recursos mediante las API de nuevas. Puede obtener más información acerca de las cuotas adicionales [aquí](../articles/azure-subscription-service-limits.md).

**¿Puedo seguir usar mi secuencias de comandos automatizadas para aprovisionar máquinas virtuales, redes virtuales y cuentas de almacenamiento a través de la API del Administrador de recursos?**

Todas las secuencias de comandos que ha creado y automatización continuarán trabajando con los equipos virtuales existentes, redes virtuales creadas en el modo de administración de servicio de Azure. Sin embargo, las secuencias de comandos deben actualizarse para utilizar el esquema nuevo para crear los mismos recursos a través del modo de administrador de recursos.

**¿Dónde puedo encontrar ejemplos de plantillas de administrador de recursos de Azure?**

Un conjunto de plantillas de starter puede encontrarse en [Plantillas de tutorial rápido del Administrador de recursos de Azure](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Pasos siguientes

- Para recorrer la creación de la plantilla que define una máquina virtual, la cuenta de almacenamiento y la red virtual, consulte [el tutorial de plantilla de administrador de recursos](resource-manager-template-walkthrough.md).
- Para ver los comandos para implementar una plantilla, vea [implementar una aplicación con el Administrador de recursos de Azure plantilla](resource-group-template-deploy.md).
