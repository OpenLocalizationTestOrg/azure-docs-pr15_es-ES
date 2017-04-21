<properties
    pageTitle="Prácticas recomendadas para el ajuste automático de Azure Monitor. | Microsoft Azure"
    description="Obtenga información sobre principios para utilizar eficazmente ajuste automático en el Monitor de Azure."
    authors="kamathashwin"
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
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="best-practices-for-azure-monitor-autoscaling"></a>Prácticas recomendadas para el ajuste automático de Monitor de Azure

Las siguientes secciones de este documento ayudarle a entender las prácticas recomendadas para Autoescala en Azure. Después de revisar esta información, podrá mejor para el uso eficaz Autoescala de la infraestructura de Azure.

## <a name="autoscale-concepts"></a>Autoescala conceptos

- Un recurso puede tener solo *una* configuración de Autoescala
- Puede tener una configuración Autoescala uno o más perfiles y cada perfil pueden tener una o más reglas Autoescala.
- Una configuración de Autoescala escala instancias horizontalmente, *que es aumentando *el instancias y* reduciendo el número de instancias* .
 Una configuración de Autoescala tiene un máximo, mínimo y valor predeterminado de instancias.
- Un trabajo Autoescala siempre lee la métrica asociada a escala, comprobar si ha cruzado el umbral configurado para escalado o en escala. Puede ver una lista de las mediciones de ese Autoescala puede ajustar a en [métricas comunes de ajuste automático de Azure Monitor](insights-autoscale-common-metrics.md).
- Todos los umbrales se calculan en un nivel de instancia. Por ejemplo, "escala a través de 1 instancia cuando promedio > 80% de la CPU cuando el recuento de instancia es 2", significa escalado cuando la CPU promedio en todas las instancias es superior al 80%.
- Siempre recibirá notificaciones de errores por correo electrónico. Más concretamente, el propietario, Colaborador y los lectores del recurso de destino recibirán correo electrónico. También siempre recibirá un correo electrónico de *recuperación* cuando Autoescala recupera de un error e inicia funcionando normalmente.
- Puede participar en para recibir una notificación de acción correcta escala por correo electrónico y webhooks.

## <a name="autoscale-best-practices"></a>Autoescala de prácticas recomendadas

Use las siguientes prácticas recomendadas cuando utilice Autoescala.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Asegúrese de los valores máximos y mínimos son distintos y tienen un margen adecuado entre ellas
Si tiene una configuración con mínimo = 2, máximo = 2 y actual recuento de instancia es 2, no se puede producir ninguna acción de escala. Mantener un margen adecuado entre los recuentos de instancia máximos y mínimos que están incluidos. Autoescala siempre escala entre estos límites.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Escala manual restablezca max y min Autoescala
Si actualiza manualmente el recuento de instancias de un valor por encima o por debajo del máximo, el motor Autoescala escala automáticamente al valor mínimo (si está por debajo) o el máximo (si está por encima). Por ejemplo, establecer el intervalo entre 3 y 6. Si tiene una instancia en ejecución, el motor Autoescala escala a 3 instancias en su próxima ejecución. Del mismo modo, podría escala de 8 instancias volver a 6 en su próxima ejecución.  Escala manual es muy temporal a menos que restablezca las reglas de Autoescala también.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Siempre use una combinación de regla de salida de la escala y escala en que realiza un aumentar y disminuir
Si utiliza solo una parte de la combinación, Autoescala escala de que sola, o, hasta el máximo o mínimo, se ha alcanzado.

### <a name="do-not-switch-between-the-azure-portal-and-the-azure-classic-portal-when-managing-autoscale"></a>No cambiar entre el portal de Azure y Azure portal clásico al administrar Autoescala
Para los servicios en la nube y servicios de aplicación (aplicaciones Web), use el portal de Azure (portal.azure.com) para crear y administrar la configuración de ajustar automáticamente. Para los conjuntos de escala de máquina Virtual use PoSH, CLI o API de REST para crear y administrar la configuración de ajustar automáticamente. No cambie entre el portal de Azure clásico (manage.windowsazure.com) y el portal de Azure (portal.azure.com) al administrar las configuraciones de Autoescala. El portal de clásico Azure y su back-end subyacente tiene limitaciones. Mover el portal de Azure para administrar Autoescala mediante una interfaz gráfica de usuario. Las opciones son usar la Autoescala PowerShell, CLI o API de REST (a través del explorador de recursos de Azure).

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Elija la estadística adecuada para su métrica de diagnóstico
Para métricas de diagnósticos, puede elegir entre *promedio*, *mínimo*, *máximo* y *Total* como una métrica escalar. Es la *Media de*la estadística más comunes.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Elija los umbrales cuidadosamente para todos los tipos de métrica
Se recomienda elegir cuidadosamente diferentes umbrales de escalado y basado en situaciones prácticas de escala.

Se *recomienda no* Autoescala configuración similar a los ejemplos siguientes con los valores de umbral de la misma o muy similar para y en condiciones:

- Aumentar instancias por 1 contar al número de subprocesos < = 600
- Disminuir instancias por 1 contar al número de subprocesos > = 600

Veamos un ejemplo de lo que puede producir un comportamiento que puede parecer confuso. La siguiente secuencia de cosider.

1. Suponga que hay 2 instancias comenzar y, a continuación, el promedio de subprocesos por instancia crece a 625.
2. Autoescala escala agregando una instancia 3ª.
3. A continuación, se supone que el número de subprocesos promedio a través de la instancia correspondiente a 575.
4. Antes de ajuste de escala hacia abajo, será Autoescala intenta calcular qué estado final si ajusta en. Por ejemplo, 575 x 3 (recuento de la instancia actual) = 1,725 / 2 (número final de instancias de escala hacia abajo) = 862.5 subprocesos. Esto significa Autoescala tendría que inmediatamente escalado nuevamente incluso después de que ha cambiado de tamaño, si el número de subprocesos promedio es el mismo o incluso queda solo una cantidad pequeña. Sin embargo, si escalado de nuevo, todo el proceso se repita, lo que lleva a un bucle infinito.
5. Para evitar esta situación (lo que se denomina "aleteo"), Autoescala no escala hacia abajo en absoluto. En su lugar, se omite y se vuelve a evaluar la condición la próxima vez que se ejecuta el trabajo del servicio. Esto podría confundir a muchas personas porque no aparece Autoescala trabajar cuando el número de subprocesos promedio era 575.

Estimación durante una escala en está pensada para evitar situaciones "flappy". Este comportamiento debe tener en cuenta al elegir los mismos umbrales de escalado y en.

Se recomienda elegir un margen adecuado entre el escalado y en umbrales. Como ejemplo, considere la posibilidad de la combinación de regla mejor siguiente.

- Aumentar instancias por 1 contar cuándo CPU % > = 80
- Disminuir instancias por 1 contar cuándo CPU % < = 60

En este caso  

1. Suponga que hay 2 instancias inicialmente.
2. Si el porcentaje de CPU promedio en varias instancias va a 80, Autoescala escala agregando una tercera instancia.
3. Ahora se supone que a lo largo del tiempo CPU % correspondiente a 60.
4. Escala en regla de Autoescala calcula el estado final si se tratase de escala. Por ejemplo, 60 x 3 (recuento de la instancia actual) = 180 / 2 (número final de instancias de escala hacia abajo) = 90. Para ajustar automáticamente no escala de porque tendría que escalado inmediatamente. En su lugar, se omite el ajuste de escala hacia abajo.
5. Comprueba el siguiente Autoescala de tiempo, la CPU continúa comprendidos en 50. Lo nuevo: calcula la instancia de 50 x 3 = 150 / 2 instancias = 75, que está por debajo del umbral de escalado de 80, por lo que escala en correctamente a instancias de 2.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Consideraciones para valores de umbral para métricas especiales de escala
 Para métricas especiales como métrica de longitud de almacenamiento o cola de Bus de servicio, el umbral es el promedio de los mensajes disponibles por número de instancias. Elija con cuidado el elegir el valor de umbral para esta métrica.

Vamos a ilustrar con un ejemplo para asegurarse de que comprender el comportamiento mejor.

- Aumentar instancias por el 1 número al recuento de mensajes de cola de almacenamiento > = 50
- Disminuir instancias por el 1 número al recuento de mensajes de cola de almacenamiento < = 10

Tenga en cuenta lo siguiente:

1. Hay instancias de cola de almacenamiento 2.
2. Mantienen procedentes de mensajes y al revisar la cola de almacenamiento, el recuento total lee 50. Supongamos que Autoescala debe empezar una acción de escalado. Sin embargo, tenga en cuenta que sigue siendo 50/2 = 25 mensajes por cada instancia. Por lo tanto, no se produce escalado. Para que el primer escalado a ocurrir, el número total de mensajes en la cola de almacenamiento debe ser 100.
3. A continuación, se supone que el número total de mensajes llega a 100.
4. Se agrega una instancia de cola de almacenamiento 3ª debido a una acción de escalado.  La siguiente acción de escalado no ocurrirá hasta que el número total de mensajes en la cola llega 150 porque 150/3 = 50.
5. Ahora, disminuye el número de mensajes en la cola. Con 3 instancias, la primera acción de escala sucede cuando el total de mensajes en todas las colas agrega hasta 30 porque 30/3 = 10 mensajes por instancia, que es el umbral de escala.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Consideraciones para la escala cuando se configuran varios perfiles en una configuración de Autoescala

En una configuración de Autoescala, puede elegir un perfil predeterminado, que siempre se aplica sin cualquier dependencia de programación o una hora, o puede elegir un perfil periódico o un perfil durante un período determinado con un intervalo de fecha y hora.

Cuando procesa Autoescala servicio, siempre comprueba en el siguiente orden:

1. Perfil de fecha fija
2. Perfil periódica
3. ("Siempre") perfil predeterminado

Si se cumple una condición de perfil, Autoescala no comprueba la siguiente condición de perfil que hay debajo. Autoescala procesa solo un perfil a la vez. Esto significa que si desea incluir una condición de procesamiento de un perfil de nivel inferior, debe incluir también las reglas en el perfil actual.

Vamos a revisar esto utilizando un ejemplo:

La imagen siguiente muestra una configuración de Autoescala con un perfil predeterminado de instancias mínimos = 2 y máximos instancias = 10. En este ejemplo, las reglas se haya configurado para escalado cuando el recuento de mensajes en la cola es superior a 10 y escala de cuando el recuento de mensajes en la cola es menor que 3. Ahora, el recurso puede escalar entre 2 y 10 instancias.

Además, hay un perfil periódico para el lunes. Se establece para instancias mínimos = 2 y máximos instancias = 12. Esto significa que el lunes, comprueba la primera Autoescala de tiempo para esta condición si el recuento de instancia es 2, escala para el nuevo mínimo de 3. Mientras sigue Autoescala buscar esta condición de perfil coincide (lunes), sólo procesa la CPU reglas basadas en escala de salida o de configurados para este perfil. En este momento, no comprueba la longitud de cola. Sin embargo, si también desea que la condición de longitud de cola revisarse, debe también incluir estas reglas en el perfil predeterminado en el perfil lunes.

Del mismo modo, cuando Autoescala vuelve a perfil predeterminado, comprueba primero si se cumplen las condiciones mínimas y máximos. Si el número de instancias en el momento es 12, escala en 10, el máximo permitido para el perfil predeterminado.

![configuración de Autoescala](./media/insights-autoscale-best-practices/insights-autoscale-best-practices.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Consideraciones para la escala cuando se configuran varias reglas en un perfil
Hay casos en los que puede establecer varias reglas en un perfil. El siguiente conjunto de reglas de Autoescala se usan con el uso de servicios cuando se configuran varias reglas.

En *escalado*, Autoescala se ejecuta si se cumplen todas las reglas.
*En escala*, Autoescala requieren todas las reglas que deben cumplirse.

Para ilustrar, se supone que tiene las siguientes reglas Autoescala 4:

- Si la CPU < 30%, escala en 1
- Si memoria < 50%, escala en 1
- Si la CPU > 75%, escalado por 1
- Si memoria > 75%, escalado por 1

A continuación, se produce el seguimiento:

- Si la CPU es 76% y memoria es 50%, nos escalado.
- Si la CPU es 50% y memoria es 76% nos escalado.

Por otro lado, si la CPU es 25% y memoria es 51% Autoescala no **no** escala de. Para escala de CPU debe ser 29% y memoria 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Seleccione siempre un recuento de la instancia de seguros predeterminada
El recuento de instancia predeterminada es importante Autoescala escala su servicio a ese recuento cuando métricas no están disponibles. Por lo tanto, seleccione un recuento de instancia predeterminada que es seguro para las cargas de trabajo.

### <a name="configure-autoscale-notifications"></a>Configurar notificaciones de Autoescala
Autoescala notifica a los administradores y colaboradores de los recursos por correo electrónico si se produce alguna de las condiciones siguientes:

- Autoescala servicio no se puede realizar una acción.
- Métricas no están disponibles para el servicio Autoescala tomar una decisión de escala.
- Métricas están disponible (recuperación) nuevamente para tomar una decisión de escala.
Además de las condiciones anteriores, puede configurar notificaciones de correo electrónico o webhook para obtener notificaciones de acciones de escala correcta.
