<properties
    pageTitle="Información general sobre Autoescala en máquinas virtuales de Microsoft Azure, servicios en la nube y aplicaciones Web | Microsoft Azure"
    description="Información general sobre Autoescala en Microsoft Azure. Se aplica a máquinas virtuales, servicios en la nube y aplicaciones Web."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="robb"/>

# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Información general sobre Autoescala en máquinas virtuales de Microsoft Azure, servicios en la nube y aplicaciones Web

En este artículo se describe qué Autoescala de Microsoft Azure, sus ventajas y cómo empezar a usarlo.  

Autoescala de Azure Monitor solo se aplica a [Conjuntos de escala de máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Servicios en la nube](https://azure.microsoft.com/services/cloud-services/)y [Aplicación de servicio - aplicaciones Web](https://azure.microsoft.com/services/app-service/web/).

>[AZURE.NOTE] Azure tiene dos métodos de Autoescala. Una versión anterior de Autoescala se aplica a máquinas virtuales (conjuntos de disponibilidad). Esta característica proporciona compatibilidad limitada y se recomienda migrar a conjuntos de escala de VM asistencia Autoescala más rápido y confiable. En este artículo, se incluye un vínculo sobre cómo utilizar la tecnología más antigua.  


## <a name="what-is-autoscale"></a>¿Qué es Autoescala?

Autoescala le permite tener la cantidad de recursos que se ejecutan para administrar la carga de la aplicación correcta. Permite agregar recursos para manejar el aumento de carga y guardar también dinero eliminando los recursos que se encuentran inactivo. Especifique un número máximo y mínimo de instancias para ejecutar y agregar o quitar máquinas virtuales automáticamente en función de un conjunto de reglas. Tener un mínimo hace que la aplicación se está ejecutando siempre incluso en ninguna carga. Tener un máximo limita el coste por hora posible total. Escalar automáticamente entre estos dos extremos usando las reglas que cree.

 ![Autoescala explicada. Agregar y quitar máquinas virtuales](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

Cuando se cumplen las condiciones de regla, se activa una o varias acciones Autoescala. Puede agregar y quitar máquinas virtuales o realizar otras acciones. El siguiente diagrama conceptual muestra este proceso.  

 ![Autoescala conceptual de diagrama de flujo](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)


## <a name="autoscale-process-explained"></a>Explicación de proceso Autoescala
La siguiente explicación se aplican a las partes del diagrama anterior.   

### <a name="resource-metrics"></a>Métrica de recursos
Recursos emiten métricas, que más adelante se procesan las reglas. Métricas vienen a través de los diferentes métodos.
Conjuntos de escala de VM utiliza los datos de telemetría de agentes de diagnóstico de Azure mientras que viene de telemetría para aplicaciones Web y servicios de nube directamente desde la infraestructura de Azure. Algunas estadísticas utilizados con frecuencia incluyen el uso de CPU, uso de la memoria, recuentos de subproceso, longitud de la cola y el uso de disco. Para obtener una lista de lo que puede usar los datos de telemetría, vea [Autoescala de métricas comunes](insights-autoscale-common-metrics.md).

### <a name="time"></a>Hora
Reglas basadas en programación se basan en UTC. Debe establecer su zona horaria correctamente al configurar las reglas.  

### <a name="rules"></a>Reglas
El diagrama muestra solo una regla Autoescala, pero puede tener muchos de ellos. Puede crear reglas superpuestas complejas según sea necesario para su situación.  Incluyen tipos de reglas  

 - **Basado en métrica** - por ejemplo, realizar esta acción cuando el uso de CPU es superior al 50%.
 - **Tiempo** - por ejemplo, desencadenar un webhook cada 8 am sábado en una zona horaria determinada.

Reglas basadas en métrica medir la carga de la aplicación y agregar o quitar VM basadas en la carga. Reglas basadas en programación permiten escalar cuando vea modelos de tiempo en la carga y desea escalar antes de un aumento de carga posible o disminuir se produce.  


### <a name="actions-and-automation"></a>Automatización y acciones

Las reglas pueden desencadenar uno o varios tipos de acciones.

- **Escala** - escala VM o alejar
- **Correo electrónico** : enviar un correo electrónico a los administradores de la suscripción, administradores co o la dirección de correo electrónico adicional que especifique
- **Automatizar a través de webhooks** - webhooks de llamada, que pueden desencadenar varias acciones complejas dentro o fuera de Azure. Dentro de Azure, puede iniciar un runbook automatización de Azure, la función de Azure o la aplicación de lógica de Azure. Ejemplo 3ª parte URL fuera de Azure incluir servicios como Twilio y margen de demora.


## <a name="autoscale-settings"></a>Configuración de Autoescala
Autoescala use la terminología y la estructura.

- Una **configuración de Autoescala** lee el motor Autoescala para determinar si se escala hacia arriba o hacia abajo. Contiene uno o más perfiles, información sobre el recurso de destino y la configuración de notificaciones.
    - Un **perfil de Autoescala** es una combinación de la capacidad de configurar un conjunto de reglas que rigen los desencadenadores y las acciones de escala para el perfil y a continuación, una periodicidad. Puede tener varios perfiles, que permiten que ocuparse de diferentes requisitos superpuestos.
        - Una **configuración de capacidad** indica el mínimo, máximo y valores predeterminados para el número de instancias. [lugar adecuado para usar figura 1]
        - Una **regla** incluye un desencadenador: un desencadenador métrico o un desencadenador de tiempo y una acción de escala, que indica si debe escalar Autoescala hacia arriba o hacia abajo cuando se cumple esa regla.
        - Una **Periodicidad** indica cuando Autoescala debe poner este perfil en vigor. Puede tener perfiles Autoescala diferentes para diferentes horas del día o días de la semana, por ejemplo.
- Una **configuración de notificación** define qué notificaciones deben ocurrir cuando se produce en función de que cumplen los criterios de uno de los perfiles de Autoescala de la configuración de un evento de Autoescala. Autoescala puede notificar a una o más direcciones de correo electrónico o realizar llamadas a uno o más webhooks.

![Configuración de Azure Autoescala, el perfil y la estructura de regla](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure3.png)

La lista completa de campos configurables y descripciones está disponible en la [API de REST Autoescala](https://msdn.microsoft.com/library/dn931928.aspx).

Para obtener ejemplos de código, vea

* [Configuración avanzada de Autoescala con plantillas de administrador de recursos para conjuntos de escala de VM](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [API de REST Autoescala](https://msdn.microsoft.com/library/dn931953.aspx)



## <a name="horizontal-vs-vertical-scaling"></a>Ajuste de escala vertical frente a horizontal

Autoescala aumenta recursos en escalas solo horizontalmente, que es un aumento ("a") o reducir ("en") en el número de instancias de máquina virtual.  Escala horizontal, que es más flexible en una situación de nube, ya que permite ejecutar potencialmente miles de máquinas virtuales para controlar la carga. Escala vertical es diferente. Mantiene el mismo número de máquinas virtuales, pero permite VM más ("hacia arriba") o menos ("abajo") eficaces. Power se mide en memoria, la velocidad de CPU, espacio en disco, etcetera.  Escala vertical tiene limitaciones más. Depende de la disponibilidad de hardware más grande, que puede variar por región y llega rápidamente y límite superior. Escala vertical también normalmente requiere detener VM e inicio. Para obtener más información, vea [escalar verticalmente Azure máquina virtual con la automatización de Azure](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md).


## <a name="methods-of-access"></a>Métodos de acceso
Puede configurar Autoescala a través de

- [Portal de Azure](insights-how-to-scale.md)
- [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
- [Interfaz de línea de comandos de varias plataformas (CLI)](insights-cli-samples.md#autoscale )
- [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931953.aspx )

## <a name="supported-services-for-autoscale"></a>Servicios compatibles para ajustar automáticamente


| Servicio                              | Esquema y documentos                                       |
|--------------------------------------|-----------------------------------------------------|
| Aplicaciones Web                             | [Ajuste de escala de aplicaciones Web](insights-how-to-scale.md)              |
| Servicios de nube                       | [Autoescala un servicio de nube](../cloud-services/cloud-services-how-to-scale.md) |
| Máquinas virtuales de Windows: clásico           | [Conjuntos de disponibilidad de escalado clásica Máquina Virtual](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Máquinas virtuales de Windows: Conjuntos de escala de Windows| [Escala escala VM establece en Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)  |
| Máquinas virtuales de Windows: Conjuntos de escala de Linux  | [Escala escala VM establece en Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Máquinas virtuales de Windows: Ejemplo de Windows   | [Configuración avanzada de Autoescala con plantillas de administrador de recursos para conjuntos de escala de VM](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo ajustar automáticamente, use la Autoscale Walkthroughs enumeradas anteriormente o consulte los recursos siguientes:

- [Medidas comunes de Azure Monitor Autoescala](insights-autoscale-common-metrics.md)
- [Prácticas recomendadas para ajustar automáticamente el Monitor de Azure](insights-autoscale-best-practices.md)
- [Usar acciones de Autoescala para enviar correo electrónico y webhook notificaciones de alerta](insights-autoscale-to-webhook-email.md)
- [API de REST Autoescala](https://msdn.microsoft.com/library/dn931953.aspx)
- [Solución de problemas Autoescala de conjuntos de escala de máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
