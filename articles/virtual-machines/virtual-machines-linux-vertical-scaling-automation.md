<properties
    pageTitle="Escalar verticalmente Azure máquina virtual con la automatización de Azure | Microsoft Azure"
    description="Cómo escalar verticalmente una máquina Virtual Linux en respuesta a las alertas con la automatización de Azure de supervisión"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/29/2016"
    ms.author="singhkay"/>

# <a name="vertically-scale-azure-virtual-machine-with-azure-automation"></a>Escalar verticalmente Azure máquina virtual con la automatización de Azure

Escala vertical es el proceso de aumentar o disminuir los recursos de un equipo en respuesta a la carga de trabajo. En Azure puede realizarse cambiando el tamaño de la máquina Virtual. Esto puede ayudar a en las situaciones siguientes

- Si la máquina Virtual no se utiliza con frecuencia, puede cambiarlo a un tamaño menor a reducir los costos mensuales
- Si la máquina Virtual está viendo una carga de recursos asignadas, puede cambiarse a un tamaño mayor para aumentar su capacidad

El esquema de los pasos para llevar a cabo esto es como debajo

1. Automatización de Azure para tener acceso a sus máquinas virtuales de instalación
2. Importar la runbooks escala Vertical de automatización de Azure en la suscripción
3. Agregar una webhook a su runbook
4. Agregar una alerta a su equipo Virtual

> [AZURE.NOTE] Debido al tamaño de la primera máquina Virtual, los tamaños de se puede escalar, podría ser limitada debido a la disponibilidad de los otros tamaños en el clúster en que Máquina Virtual actual se implementa. En la runbooks automatización publicados usadas en este artículo se ocuparse de este caso y escalar solo dentro del debajo de pares de tamaño de máquina virtual. Esto significa que una máquina Virtual de Standard_D1v2 no repente se escalado Standard_G5 o adaptado a Basic_A0.

>| Ajuste de escala de par de tamaños VM |   |
|---|---|
|  Basic_A0 |  Basic_A4 |
|  Standard_A0 | Standard_A4 |
|  Standard_A5 | Standard_A7  |
|  Standard_A8 | Standard_A9  |
|  Standard_A10 |  Standard_A11 |
|  Standard_D1 |  Standard_D4 |
|  Standard_D11 | Standard_D14  |
|  Standard_DS1 |  Standard_DS4 |
|  Standard_DS11 | Standard_DS14  |
|  Standard_D1v2 |  Standard_D5v2 |
|  Standard_D11v2 |  Standard_D14v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Automatización de Azure para tener acceso a sus máquinas virtuales de instalación

Lo primero que debe hacer es crear una cuenta de Azure automatización que va a hospedar el runbooks usado para ajustar el tamaño de las instancias de VM escala configurada. El servicio de automatización había presentado recientemente la característica "Ejecutar como cuenta" que permite que la configuración de capital de servicio para que se ejecute automáticamente la runbooks en nombre de usuario muy fácil. Puede obtener más información sobre esto en el artículo siguiente:

* [Autenticar Runbooks con Azure ejecutar como cuenta](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar la runbooks escala Vertical de automatización de Azure en la suscripción

La runbooks que son necesarios para escalar verticalmente la máquina Virtual ya se publican en la Galería de Runbook de automatización de Azure. Debe importarlos a su suscripción. Puede aprender a importar runbooks, lea el artículo siguiente.

* [Galerías runbook y módulo de automatización de Azure](../automation/automation-runbook-gallery.md)

La runbooks que tenga que se va a importar se muestran en la imagen siguiente

![Importar runbooks](./media/virtual-machines-vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Agregar una webhook a su runbook

Una vez que haya importado la runbooks que deberá agregar una webhook a runbook por lo que pueden desencadenar una alerta de una máquina Virtual. Pueden leer los detalles de la creación de un webhook para su Runbook aquí

* [Azure webhooks de automatización](../automation/automation-webhooks.md)

Asegúrese de que copiar la webhook antes de cerrar el cuadro de diálogo webhook ya que lo necesitará en la siguiente sección.

## <a name="add-an-alert-to-your-virtual-machine"></a>Agregar una alerta a su equipo Virtual

1. Seleccione configuración de máquina Virtual
2. Seleccione "Reglas de alerta"
3. Seleccione "Agregar alerta"
4. Seleccione una métrica para desencadenar la alerta en
5. Seleccione una condición que, cuando cumpla will provocar que se aplique la alerta
6. Seleccione un umbral de la condición en el paso 5. cumplirse
7. Seleccione un período en los que se active el servicio de supervisión de la condición y umbral en los pasos 5 y 6
8. Pegue la webhook que ha copiado de la sección anterior.

![Agregar alerta a máquina Virtual 1](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-1.png)

![Agregar alerta a máquina Virtual 2](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-2.png)