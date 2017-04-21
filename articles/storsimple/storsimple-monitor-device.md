<properties 
   pageTitle="Supervisar el dispositivo StorSimple | Microsoft Azure"
   description="Describe cómo usar el servicio Administrador de StorSimple para supervisar el rendimiento de i/OS, utilización de la capacidad, rendimiento de la red y rendimiento del dispositivo."
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
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-monitor-your-storsimple-device"></a>Usar el servicio Administrador de StorSimple para supervisar su dispositivo StorSimple 

## <a name="overview"></a>Información general

Puede utilizar el servicio Administrador de StorSimple para supervisar dispositivos determinados dentro de la solución StorSimple. Puede crear gráficos personalizados en función de rendimiento i/OS, utilización de la capacidad, rendimiento de la red y medidas de rendimiento del dispositivo. 

Para ver la información de supervisión para un dispositivo específico, en el portal de clásico Azure, seleccione el servicio Administrador de StorSimple. Haga clic en la ficha **Monitor** y, a continuación, seleccione en la lista de dispositivos. La página **Monitor** contiene la siguiente información.

## <a name="io-performance"></a>Rendimiento 

**Rendimiento** pistas métricas relacionado con el número de leer y escribir operaciones entre cualquiera de las interfaces de iniciador iSCSI en el servidor host y el dispositivo o el dispositivo y la nube. Este rendimiento se puede medir un volumen específico, un contenedor de volumen específico o todos los contenedores de volumen.

El siguiente gráfico muestra las i/OS para el iniciador de su dispositivo para todos los volúmenes de un dispositivo de producción. La métrica de trazado es leer y escribe bytes por segundo, lea y operaciones de escritura IO por segundo y lee y escribe latencia.

![Rendimiento de IO de iniciador al dispositivo](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

En el mismo dispositivo, las operaciones de i/OS se trazan los datos desde el dispositivo de la nube para todos los contenedores de volumen. En este dispositivo, los datos están sólo en el nivel de lineal y nada ha derramado en la nube. No hay ninguna operación de lectura y escritura transcurridos desde el dispositivo a la nube. Por lo tanto, los picos del gráfico están en un intervalo de 5 minutos que corresponde a la frecuencia en el que está activado el latido entre el dispositivo y el servicio. 

![Rendimiento de IO desde el dispositivo a la nube](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


Para el mismo dispositivo, se realizó una instantánea de nube datos de volumen empezando por 2:00 p.m.. Esto ha generado datos que fluyen desde el dispositivo a la nube. Lee escribe estaba servicio en la nube en esta duración. El gráfico de IO muestra un máximo de las distintas métricas correspondiente a la hora cuando se realizó la instantánea. 

![Rendimiento de IO de dispositivo en la nube después de instantánea de la nube](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## <a name="capacity-utilization"></a>Utilización de la capacidad 

**Utilización de la capacidad** realiza un seguimiento de las medidas relacionadas con la cantidad de espacio de almacenamiento de datos que se utiliza por volúmenes, contenedores de volumen o dispositivo. Puede crear informes basados en la utilización de la capacidad de su almacenamiento principal, el almacenamiento de nube o el almacenamiento de dispositivo. Utilización de la capacidad se puede medir en un volumen específico, un contenedor de volumen específico o en todos los contenedores de volumen.


La nube primaria, y la capacidad de almacenamiento de dispositivo se puede describir como sigue:

###<a name="primary-storage-capacity-utilization"></a>Utilización de la capacidad de almacenamiento de información primario
 
Estos gráficos muestran la cantidad de datos que se escriben en volúmenes de StorSimple antes de que los datos desduplicados y comprimidos. Puede ver la utilización de almacenamiento de información primario por todos los volúmenes o un único volumen.

Al ver los gráficos de utilización de capacidad de volumen de almacenamiento principal para todos los volúmenes frente a cada uno de los volúmenes individuales y sumar los datos principales en ambos estos casos, es posible que no coinciden entre los dos números. No se pueden agregar los datos principales total de todos los volúmenes hacia arriba a la suma total de los datos principales de los volúmenes individuales. Esto puede deberse a uno de estos procedimientos:

- **Incluidas para todos los volúmenes de datos de instantánea**: se ve este comportamiento solo si se está ejecutando una versión anterior a la actualización 3. Los datos principales que se muestran para todos los volúmenes están la suma de los datos principales para cada volumen y los datos de instantánea. Los datos principales que se muestran para un volumen determinado corresponde a solo la cantidad de datos asignados en el volumen (y no incluye los datos de instantánea de volumen correspondientes).

    Esto también se explica por la siguiente ecuación:

    *Datos primarios (todos los volúmenes) = suma (datos principal (volumen i) + tamaño de los datos de instantáneas (volumen i))*
    
    *dónde, datos principal (volumen i) = tamaño de datos principales asignados a volumen i*
 
    Si se eliminan las instantáneas a través del servicio, la eliminación se realiza de forma asincrónica en segundo plano. Puede tardar algún tiempo para el tamaño de datos de volumen que se actualicen tras la eliminación de instantánea. 

    Si ejecuta actualización 3 o posterior, los datos de instantánea no se incluyen en los datos del volumen. Y la utilización principal se calcula como sigue:

    * Datos principal (todos los volúmenes) = suma (principal datos (volumen i)
    
    *dónde, datos principal (volumen i) = tamaño de datos principales asignados a volumen i*
 
- **Volúmenes con supervisión deshabilitado incluidos en todos los volúmenes**: si tiene volúmenes en el dispositivo cuya supervisión está desactivada, los datos de supervisión para estos volúmenes individuales no estarán disponibles en los gráficos. Sin embargo, los datos de todos los volúmenes en el gráfico incluyen los volúmenes cuya supervisión está desactivado. 
 
- **Eliminar volúmenes con copias de seguridad asociados directo incluidas para todos los volúmenes**: si se eliminan volúmenes que contienen datos de instantánea pero las instantáneas asociadas todavía existen, es posible que vea un error de coincidencia.

- **Volúmenes eliminados incluidos para todos los volúmenes**: en algunos casos, pueden existir volúmenes antiguos aunque estos se han eliminado. No se ve el efecto de eliminación y el dispositivo puede mostrar menor capacidad disponible. Debe ponerse en contacto con Microsoft Support para quitar estos volúmenes.

Los gráficos siguientes muestran la utilización de la capacidad de almacenamiento principal de un dispositivo StorSimple antes y después de que se realizó una instantánea de la nube. Como solo los datos de volumen, una instantánea de la nube no debe cambiar el almacenamiento principal. Como puede ver, el gráfico no muestra ninguna diferencia en el uso de la capacidad principal como resultado de tomar una instantánea de la nube. Inicia la instantánea de la nube a aproximadamente 2:00 p.m. en ese dispositivo.

![Utilización de la capacidad principal antes de instantánea de la nube](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![Utilización de la capacidad principal después de instantánea de la nube](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

Si está ejecutando actualizar 2 o posterior, puede dividir la utilización de la capacidad de almacenamiento de información primario por volumen individual, todos los volúmenes, todos los volúmenes en niveles y todos los volúmenes localmente anclados tal como se muestra a continuación. Desglosar por todos los volúmenes localmente anclados le permitirá determinar rápidamente cuánto el nivel local se usa hacia arriba.

![Utilización de la capacidad principal para todos los volúmenes localmente anclados](./media/storsimple-monitor-device/localvolumes.png)


###<a name="cloud-storage-capacity-utilization"></a>Utilización de capacidad de almacenamiento de nube

Estos gráficos muestran la cantidad de almacenamiento de nube utilizado. Estos datos desduplicados y comprimidos. Este importe incluye instantáneas de nube que pueden contener datos que no se reflejan en cualquier volumen principal y se mantienen con fines de retención necesarios o heredados. Puede comparar el principal y la nube las cifras de consumo de almacenamiento para hacerse una idea de la tasa de reducción de datos, aunque el número no será exacto. Los gráficos siguientes muestran el uso de la capacidad de almacenamiento de nube de un dispositivo StorSimple antes y después de que se realizó una instantánea de la nube. Instantánea de nube iniciada en torno 2:00 p.m. en ese dispositivo y puede ver el uso de la capacidad de nube captura al mismo tiempo, aumento de MB 5,73 4.04 GB.

![Utilización de la capacidad de nube antes de instantánea de la nube](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![Utilización de la capacidad de nube después de instantánea de la nube](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


###<a name="device-storage-capacity-utilization"></a>Utilización de capacidad de almacenamiento de dispositivo

Estos gráficos muestran la utilización total del dispositivo, que será la utilización del almacenamiento de más de principal porque incluye el nivel SSD lineal. Este nivel contiene una cantidad de datos que también existe en el dispositivo de otros niveles. La capacidad en el nivel SSD lineal se activa para que cuando llegan nuevos datos, los datos antiguos se mueven al nivel de la unidad de disco duro (en qué momento es desduplicado y comprimido) y, posteriormente, en la nube.

Con el tiempo, utilización de la capacidad principal y utilización de la capacidad de dispositivo probablemente aumentará juntos hasta que los datos empiezan a ser niveles en la nube. En ese momento, la utilización de la capacidad de dispositivo probablemente comenzará a meseta, pero aumentará la utilización de la capacidad principal mientras se escriben más datos.

Los gráficos siguientes muestran la utilización de la capacidad de almacenamiento principal de un dispositivo StorSimple antes y después de que se realizó una instantánea de la nube. Iniciar la instantánea de la nube a 2:00 p.m. y la utilización de la capacidad de dispositivo a disminuir en ese momento. El uso de la capacidad de almacenamiento de dispositivo se desactivó desde GB 11.58 7.48 GB. Esto indica que más probable es que los datos sin comprimir en el nivel SSD lineal se desduplicados, comprimidos y movidos en el nivel de la unidad de disco duro. Tenga en cuenta que si el dispositivo ya tiene una gran cantidad de datos en los niveles de la SSD y la unidad de disco duro, quizás no pueda ver esta disminución. En este ejemplo, el dispositivo tiene una cantidad pequeña de datos.

![Utilización de la capacidad de dispositivo antes de instantánea de la nube](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![Utilización de la capacidad de dispositivo después de instantánea de la nube](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## <a name="network-throughput"></a>Rendimiento de la red

**Rendimiento de la red** realiza un seguimiento de las medidas relacionadas con la cantidad de datos que se transfieren desde las interfaces de red del iniciador de iSCSI en el servidor host y el dispositivo y entre el dispositivo y la nube. Puede supervisar esta métrica para cada una de las interfaces de red iSCSI en el dispositivo.

Los gráficos siguientes muestran el rendimiento de la red de datos 0 y 4 de datos, dos interfaces de red GbE 1 en su dispositivo. En este caso, datos 0 nube-habilitó mientras que 4 de datos era habilitado iSCSI. También puede consultar la entrada y el tráfico saliente de su dispositivo StorSimple. El hecho de que solo cada 5 minutos de recopilación de datos y se debe omitir está en función de la línea plana en el gráfico a partir de 3:24 pm. 

![Rendimiento de la red para Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Rendimiento de la red para Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## <a name="device-performance"></a>Rendimiento del dispositivo 

**Rendimiento del dispositivo** realiza un seguimiento de las medidas relacionadas con el rendimiento de su dispositivo. El siguiente gráfico muestra las estadísticas de uso de CPU de un dispositivo de producción.

![Uso de CPU de dispositivo](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [usar el panel de dispositivo de servicio de administrador de StorSimple](storsimple-device-dashboard.md).

- Obtenga información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).
