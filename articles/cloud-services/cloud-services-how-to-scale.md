<properties
    pageTitle="Automáticamente ajustar un servicio de nube en el portal de | Microsoft Azure"
    description="(clásico) Obtenga información sobre cómo usar el portal clásico para configurar reglas de escala automático para una función de web del servicio de nube o trabajo en Azure."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="how-to-auto-scale-a-cloud-service"></a>Cómo Autoescala un servicio de nube

> [AZURE.SELECTOR]
- [Portal de Azure](cloud-services-how-to-scale-portal.md)
- [Portal de clásico de Azure](cloud-services-how-to-scale.md)

En la página de la escala del portal clásica Azure, puede escalar manualmente la función web o trabajo, o puede habilitar el ajuste de escala automático en función de la carga de la CPU o de una cola de mensajes.

>[AZURE.NOTE] En este artículo se centra en los roles de web y trabajador de servicio de nube. Cuando se crea una máquina virtual (clásico) directamente, está alojado en un servicio de nube. Parte de esta información se aplica a estos tipos de máquinas virtuales de Windows. Ajuste de escala de un conjunto de disponibilidad de máquinas virtuales es simplemente cerrando ellos activar y desactivar según las reglas de escala que configura. Para obtener más información acerca de máquinas virtuales y conjuntos de disponibilidad, consulte [Administrar la disponibilidad de las máquinas virtuales](../virtual-machines/virtual-machines-windows-classic-configure-availability.md)

Debe tener en cuenta la siguiente información antes de configurar la escala de la aplicación:

- Uso de core afecta a escala. Las instancias de función mayores use más núcleos. Puede ajustar una aplicación solo dentro del límite de núcleos para su suscripción. Por ejemplo, si su suscripción tiene un límite de veinte núcleos y ejecutar una aplicación con dos medio tamaño (un total de cuatro núcleos) de servicios de nube, solo puede escalar el otras implementaciones de servicio de nube en su suscripción por la 16 extracción de. Para obtener más información sobre los tamaños, vea [Tamaños de servicio de nube](cloud-services-sizes-specs.md) .

- Debe crear una cola y asociar con una función antes de que puede ajustar el tamaño de una aplicación basada en un umbral de mensajes. Para obtener más información, vea [cómo usar el servicio de almacenamiento de cola](../storage/storage-dotnet-how-to-use-queues.md).

- Puede ajustar el tamaño de los recursos que estén vinculados a su servicio de nube. Para obtener más información acerca de la vinculación de recursos, vea [Cómo: vincular un recurso a un servicio de nube](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

- Para habilitar alta disponibilidad de la aplicación, debe asegurarse de que se ha implementado con dos o más instancias de la función. Para obtener más información, consulte [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).



## <a name="schedule-scaling"></a>Ajuste de escala de programación

De forma predeterminada, todos los roles no siguen una programación específica. Por lo tanto, cualquier configuración cambiada se aplica a todas las horas y todos los días a lo largo del año. Si lo desea, puede configurar la escala manual o automática para:

- Días laborables
- Fines de semana
- Noches de semana
- Por la mañana semana
- Fechas específicas
- Intervalos de fechas específico

Esta es conigured en el [portal de clásico de Azure](https://manage.windowsazure.com/) en la  
**Servicios de nube** > **\[su servicio de nube\]** > **escala** > **\[de producción o ensayo\] ** página.

Haga clic en el botón **configurar horas de planificación** para cada rol que desea cambiar.

![Escala automática del servicio de nube basada en una programación][scale_schedules]



## <a name="manual-scale"></a>Escala manual

En la página de **escala** manualmente puede aumentar o disminuir el número de instancias de ejecución en un servicio de nube. Si no ha creado una programación de este está configurado para cada plan que haya creado o para todo el tiempo.

1. En el [portal de clásica Azure](https://manage.windowsazure.com/), haga clic en **Servicios en la nube**y, a continuación, haga clic en el nombre del servicio de nube para abrir el panel.

    > [AZURE.TIP] Si no ve su servicio de nube, debe cambiar de **producción** a **ensayo** o viceversa.

2. Haga clic en **escala**.

3. Seleccione el plan que desea cambiar las opciones de escala de. Valores predeterminados para *n horas programadas* si no tiene se definieron agendas.

4. Busque la sección de **escala métrica** y seleccione **Ninguno**. Esta es la configuración predeterminada para todos los roles.

5. Cada rol en el servicio de nube tiene un control deslizante para cambiar el número de instancias que desee usar.

    ![Ajustar manualmente el tamaño de una función de servicio de nube][manual_scale]

    Si necesita más instancias, debe cambiar el [tamaño de máquina virtual de servicio de nube](cloud-services-sizes-specs.md).

6. Haga clic en **Guardar**.  
Instancias de rol se pueden agregar o quitar en función de las selecciones.

>[AZURE.TIP] Siempre que vea ![][tip_icon] mueva el mouse a él y puede obtener ayuda sobre lo que un valor específico hace.


## <a name="automatic-scale---cpu"></a>Escala automático - CPU

Se cambia de tamaño si el porcentaje de uso de CPU pasa por encima o por debajo de los umbrales especificados; las instancias de función se crean o se eliminan.

1. En el [portal de clásica Azure](https://manage.windowsazure.com/), haga clic en **Servicios en la nube**y, a continuación, haga clic en el nombre del servicio de nube para abrir el panel.

    > [AZURE.TIP] Si no ve su servicio de nube, debe cambiar de **producción** a **ensayo** o viceversa.

2. Haga clic en **escala**.

3. Seleccione el plan que desea cambiar las opciones de escala de. Valores predeterminados para *n horas programadas* si no tiene se definieron agendas.

4. Busque la sección de **escala métrica** y seleccione **la CPU**.

5. Ahora puede configurar un rango máximo y mínimo de instancias de roles, el uso de CPU (para desencadenar una escala de arriba) de destino y cuántas instancias escalar hacia arriba y hacia abajo.

![Ajustar el tamaño de una función de servicio de nube por la carga de la cpu][cpu_scale]

>[AZURE.TIP] Siempre que vea ![][tip_icon] mueva el mouse a él y puede obtener ayuda sobre lo que un valor específico hace.





## <a name="automatic-scale---queue"></a>Escala automático - cola

Esto se ajusta automáticamente si el número de mensajes de una cola pasa por encima o por debajo del umbral especificado; las instancias de función se crean o se eliminan.

1. En el [portal de clásica Azure](https://manage.windowsazure.com/), haga clic en **Servicios en la nube**y, a continuación, haga clic en el nombre del servicio de nube para abrir el panel.

    > [AZURE.TIP] Si no ve su servicio de nube, debe cambiar de **producción** a **ensayo** o viceversa.

2. Haga clic en **escala**.

3. Busque la sección de **escala métrica** y seleccione **la CPU**.

4. Ahora puede configurar un rango máximo y mínimo de instancias de roles, la cola y la cantidad de mensajes en cola para procesar para que cada instancia y cuántas instancias escalar hacia arriba y hacia abajo.

![Ajustar el tamaño de una función de servicio de nube por una cola de mensajes][queue_scale]

>[AZURE.TIP] Siempre que vea ![][tip_icon] mueva el mouse a él y puede obtener ayuda sobre lo que un valor específico hace.


## <a name="scale-linked-resources"></a>Escalar recursos vinculados

A menudo al cambiar la escala de una función, es conveniente ajustar el tamaño de la base de datos que está utilizando también la aplicación. Si se vincula a la base de datos del servicio de nube, puede acceder a la configuración de escalado de ese recurso haciendo clic en el vínculo correspondiente.

1. En el [portal de clásica Azure](https://manage.windowsazure.com/), haga clic en **Servicios en la nube**y, a continuación, haga clic en el nombre del servicio de nube para abrir el panel.

    > [AZURE.TIP] Si no ve su servicio de nube, debe cambiar de **producción** a **ensayo** o viceversa.

2. Haga clic en **escala**.

3. Busque la sección de **recursos vinculados** y hace clic en **Administrar escala para esta base de datos**.

    > [AZURE.NOTE] Si no ve ninguna sección de **recursos vinculados** , probablemente no tiene ningún recursos vinculados.

![][linked_resource]


[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png
