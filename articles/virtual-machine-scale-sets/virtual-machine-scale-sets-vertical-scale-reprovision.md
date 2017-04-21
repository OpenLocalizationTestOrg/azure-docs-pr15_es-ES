<properties
    pageTitle="Escalar verticalmente conjuntos de escala de Azure máquina virtual | Microsoft Azure"
    description="Cómo escalar verticalmente una máquina Virtual en respuesta a las alertas con la automatización de Azure de supervisión"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="madhana"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="guybo"/>

# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Autoescala vertical con conjuntos de escala de máquina Virtual

En este artículo se describe cómo escalar verticalmente Azure [Conjuntos de escala de máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/) con o sin reaprovisionamiento. Escala de máquinas virtuales que no están en conjuntos de escala vertical, consulte [escalar verticalmente Azure máquina virtual con la automatización de Azure](../virtual-machines/virtual-machines-windows-vertical-scaling-automation.md).

Escala vertical, también conocido como _escalar hacia arriba_ y _hacia abajo_, significa aumentar o reducir el tamaño de la máquina virtual (VM) en respuesta a una carga de trabajo. Compare esto con [escala horizontal](./virtual-machine-scale-sets-autoscale-overview.md), también conocida como _escala horizontal_ y _escala en_, donde se modifica el número de máquinas virtuales según la carga de trabajo.

Reaprovisionamiento significa quitar una máquina virtual existente y reemplazarlo con uno nuevo. Al aumentar o disminuir el tamaño de máquinas virtuales de un conjunto de escala de VM, en algunos casos que desea cambiar el tamaño de las máquinas virtuales existentes y conservar los datos, mientras en otros casos debe implementar nuevas máquinas virtuales de nuevo tamaño. Este documento trata sobre ambos casos.

Escala vertical puede ser útil cuando:

- Un servicio integrado en máquinas virtuales es utilizado en (por ejemplo, en los fines de semana). Reducir el tamaño de la máquina virtual puede reducir los costos mensuales.
- Aumentar tamaño VM a lidiar con mayor demanda sin crear máquinas virtuales adicionales.

Puede configurar la escala vertical ser desencadenadas alertas basadas en función de métrica de su conjunto de escala de máquina virtual. Cuando se activa la alerta desencadene una webhook que activó un runbook que puede ajustar la escala de establece hacia arriba o hacia abajo. Puede configurar la escala vertical siguiendo estos pasos:

1. Cree una cuenta de Azure automatización con capacidad de ejecutar como.
2. Importar runbooks escala Vertical de automatización de Azure VM conjuntos de escala de la suscripción.
3. Agregar una webhook a su runbook.
4. Agregar una alerta para la escala de VM establecer mediante una notificación de webhook.

> [AZURE.NOTE] Ajuste automático vertical sólo puede producirse en determinados intervalos de tamaños de máquina virtual. Comparar las especificaciones de cada tamaño antes de decidir escalar de uno a otro (número mayor no siempre indica mayor tamaño VM). Puede elegir escalar entre los siguientes pares de tamaño:

>| Ajuste de escala de par de tamaños VM |   |
|---|---|
|  Standard_A0 | Standard_A11 |
|  Standard_D1 |  Standard_D14 |
|  Standard_DS1 |  Standard_DS14 |
|  Standard_D1v2 |  Standard_D15v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Crear una cuenta de Azure automatización con capacidad de ejecutar como

Lo primero que debe hacer es crear una cuenta de Azure automatización que va a hospedar el runbooks usado para ajustar el tamaño de las instancias de VM escala configurada. [Automatización de Azure](https://azure.microsoft.com/services/automation/) había presentado recientemente la característica "Ejecutar como cuenta" que permite que la configuración de capital de servicio para ejecutar automáticamente la runbooks en nombre de usuario muy fácil. Puede obtener más información sobre esto en el artículo siguiente:

* [Autenticar Runbooks con Azure ejecutar como cuenta](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar runbooks escala Vertical de automatización de Azure en la suscripción

La runbooks necesarios para escalar verticalmente los conjuntos de escala VM ya se publican en la Galería de Runbook de automatización de Azure. Para importarlos a su suscripción, siga los pasos de este artículo:

* [Galerías runbook y módulo de automatización de Azure](../automation/automation-runbook-gallery.md)

Elija la opción Examinar Galería en el menú Runbooks:

![Runbooks que desea importar][runbooks]

Se muestran los runbooks que tenga que se va a importar. Seleccione runbook en función de si desea vertical escala con o sin reaprovisionamiento:

![Galería de runbooks][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Agregar una webhook a su runbook

Una vez que haya importado la runbooks que deberá agregar una webhook a runbook por lo que pueden desencadenar una alerta de un conjunto de escala de máquina virtual. En este artículo se describen los detalles de la creación de un webhook para su Runbook:

* [Azure webhooks de automatización](../automation/automation-webhooks.md)

> [AZURE.NOTE] Asegúrese de que copiar la webhook URI antes de cerrar el cuadro de diálogo webhook ya que lo necesitará en la siguiente sección.

## <a name="add-an-alert-to-your-vm-scale-set"></a>Agregar una alerta para el conjunto de escala de VM

A continuación, encontrará una secuencia de comandos de PowerShell que muestra cómo agregar una alerta para un conjunto de escala de máquina virtual. Consulte el siguiente artículo para obtener el nombre de la métrica para desencadenar la alerta en: [métricas comunes de ajuste automático de Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [AZURE.NOTE] Se recomienda para configurar una ventana de tiempo razonable para la alerta con el fin de evitar que se activen escala vertical y cualquier interrupción de servicio asociado con demasiada frecuencia. Considere la posibilidad de una ventana de menos de 20 a 30 minutos o más. Considere la posibilidad de escalado si necesita evitar interrupciones horizontal.

Para obtener más información sobre cómo crear alertas, consulte los siguientes artículos:

* [Ejemplos de inicio rápido de Azure Monitor PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Ejemplos de inicio rápido de Azure Monitor entre plataformas CLI](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Resumen

En este artículo mostraba los ejemplos de escalado verticales simples. Con estos bloques de creación - cuenta de automatización, runbooks, webhooks, alertas - puede conectar una gran variedad de eventos con un conjunto de acciones personalizada.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
