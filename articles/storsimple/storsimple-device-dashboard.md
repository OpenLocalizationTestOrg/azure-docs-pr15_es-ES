<properties
   pageTitle="Usar el panel de dispositivo StorSimple Manager | Microsoft Azure"
   description="Describe el panel de dispositivo de servicio de StorSimple Manager y cómo usarlo para ver métrica de almacenamiento e iniciadores conectados y buscar el número de serie y IQN."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-device-dashboard"></a>Usar el panel de dispositivo StorSimple Manager

## <a name="overview"></a>Información general

El panel de dispositivo de StorSimple Manager le ofrece una descripción general de la información para un dispositivo StorSimple específico, a diferencia de la consola de servicio, que le ofrece información acerca de todos los dispositivos que se incluyen en la solución Microsoft Azure StorSimple.

![Página de panel de dispositivo](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

El panel contiene la información siguiente:

- **Área del gráfico** , puede ver la métrica de almacenamiento correspondientes en el área de gráfico en la parte superior del panel. En este gráfico, puede ver métricas para el almacenamiento principal total (la cantidad de datos escritos hosts al dispositivo) y el almacenamiento de nube total consumido por el dispositivo durante un período de tiempo.

     En este contexto, *almacenamiento principal* hace referencia a la cantidad total de los datos escritos por el host y se pueden dividir por tipo de volumen: *almacenamiento principal* incluye tanto los datos almacenados localmente y niveles de datos en la nube; *principal localmente anclados almacenamiento* incluye sólo los datos almacenados localmente. *Almacenamiento de la nube*, por otro lado, es una medida de la cantidad total de datos almacenados en la nube. Esto incluye las copias de seguridad y niveles datos. Tenga en cuenta que los datos almacenados en la nube es desduplicados y comprimidos, mientras que almacenamiento principal indica la cantidad de almacenamiento utilizado antes de que los datos desduplicados y comprimidos. (Puede comparar estos dos números para hacerse una idea de la tasa de compresión). Para ambos primario y el almacenamiento de la nube, los importes se basa la frecuencia de seguimiento que se configure. Por ejemplo, si elige una frecuencia una semana, el gráfico muestra datos para cada día de la semana anterior.

     Puede configurar el gráfico como sigue:

     - Para ver la cantidad de almacenamiento de nube consumida a lo largo del tiempo, seleccione la opción **Utilizar de almacenamiento de nube** . Para ver el almacenamiento total que se ha escrito el host, seleccione las opciones **Principal en niveles de almacenamiento utiliza** y **principal LOCALMENTE ANCLADOS almacenamiento** . En la ilustración, ambas opciones están seleccionadas; por lo tanto, el gráfico muestra cantidades de almacenamiento para la nube y almacenamiento principal. Tenga en cuenta que cualquier almacenamiento principal usado antes de instalar la actualización 2 se representa mediante la línea **Principal en niveles de almacenamiento usan** .
     - Use el menú desplegable en la esquina superior derecha del gráfico para especificar un período de tiempo de 1 semana, 1 mes, 3 meses o años 1. Observe que el gráfico de nivel superior se actualiza solo una vez al día y, por tanto, se reflejará totales del día anterior.

     Para obtener más información, consulte [usar el servicio de administrador de StorSimple para supervisar su dispositivo StorSimple](storsimple-monitor-device.md).

- **Información general de uso** : en el área de **Visión general de uso** , puede ver el espacio de almacenamiento principal usado, la cantidad de almacenamiento de preparación y la capacidad máxima de almacenamiento de su dispositivo. Comparando estos números de uso a la cantidad máxima de almacenamiento disponible, puede ver de un vistazo si necesita obtener almacenamiento adicional. Tenga en cuenta que esta información general se actualiza cada 15 minutos y, debido a la diferencia en la frecuencia de actualización, puede mostrar distintos números a los que se muestra en el área de gráfico anterior, que se actualiza diariamente. Para obtener más información, consulte [usar el servicio de administrador de StorSimple para supervisar su dispositivo StorSimple](storsimple-monitor-device.md).


- **Alertas** : el área de **avisos** contiene información general sobre las alertas de su dispositivo. Las alertas se agrupan por gravedad y se proporciona un recuento del número de alertas en cada nivel de gravedad. Haciendo clic en la alerta de gravedad, se abre una vista específica de la ficha de alertas para mostrar solo las alertas de ese nivel de gravedad para este dispositivo.

- **Trabajos** : el área de **tareas** se muestra el resultado de la actividad de trabajo reciente. Esto puede garantizar que el sistema funciona según lo esperado, o puede dejar que sabe que debe tomar medidas correctivas. Para obtener más información acerca de los trabajos completados recientemente, haga clic en **trabajos se realizó correctamente en las últimas 24 horas**.

- El área de **un vistazo rápido** a la derecha del panel proporciona información útil, como el modelo de dispositivo, número de serie, estado, descripción y número de volúmenes.

También puede configurar la migración tras error y ver iniciadores conectados desde el panel de dispositivos.

Las tareas comunes que se pueden realizar en esta página son:

- Ver iniciadores conectados

- Buscar el número de serie de dispositivo

- Encontrar el destino del dispositivo IQN

## <a name="view-connected-initiators"></a>Ver iniciadores conectados

Puede ver los iniciadores iSCSI que están conectados a su dispositivo haciendo clic en el vínculo **Ver conectados iniciadores** proporcionado en el área de **vista rápida** del panel del dispositivo. Esta página proporciona una lista tabular de los iniciadores que ha conectado correctamente a su dispositivo. Para cada iniciador, consulte:

- El iSCSI nombre completo (IQN) del iniciador conectado.

- El nombre del registro de control de acceso (ACR) que permite este iniciador conectado.

- La dirección IP del iniciador conectado.

- Las interfaces de red que el iniciador está conectado a su dispositivo de almacenamiento. Pueden ir desde datos de 0 a 5 de datos.

- Todos los volúmenes que se permite el iniciador conectado según la configuración actual de ACR.

Si ve inesperados iniciadores de esta lista o no ve los esperados, revise la configuración de ACR. Un máximo de 512 iniciadores puede conectarse a su dispositivo.

## <a name="find-the-device-serial-number"></a>Buscar el número de serie de dispositivo

Cuando configura i/OS de múltiples rutas de Microsoft (MPIO) en el dispositivo, puede que tenga el número de serie de dispositivo. Realice los pasos siguientes para buscar el número de serie de dispositivo.

#### <a name="to-find-the-device-serial-number"></a>Para encontrar el número de serie de dispositivo

1. Vaya a **dispositivos** > **paneles**.

2. En el panel derecho del escritorio, busque el área de **un vistazo rápido** .

3. Desplácese hacia abajo y busque el número de serie.

## <a name="find-the-device-target-iqn"></a>Encontrar el destino del dispositivo IQN

Puede que tenga el destino del dispositivo IQN al configurar el protocolo de autenticación por desafío mutuo (CHAP) en el dispositivo StorSimple. Realice los pasos siguientes para buscar el destino del dispositivo IQN.

### <a name="to-find-the-device-target-iqn"></a>Para encontrar el destino del dispositivo IQN

1. Vaya a **dispositivos** > **paneles**.

1. En el panel derecho del escritorio, busque el área de **un vistazo rápido** .

1. Desplácese hacia abajo y busque el destino IQN.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [panel de StorSimple administrador del servicio](storsimple-service-dashboard.md).
- Más información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).
