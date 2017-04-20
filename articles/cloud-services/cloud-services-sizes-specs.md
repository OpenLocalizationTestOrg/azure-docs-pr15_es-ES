<properties
 pageTitle="Tamaños de los servicios de nube | Microsoft Azure"
 description="Muestra los tamaños de máquina virtual diferente (e identificadores) para las funciones de web y trabajador de servicio de nube Azure."
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="10/27/2016"
 ms.author="adegeo"/>

# <a name="sizes-for-cloud-services"></a>Tamaños de los servicios en la nube

Este tema describe las opciones para las instancias de función de servicio de nube (funciones web y roles de trabajo) y tamaños disponibles. También proporciona las consideraciones de implementación para tener en cuenta al planear usar estos recursos. Cada tamaño tiene un identificador que se coloca en el [archivo de definición de servicio](cloud-services-model-and-package.md#csdef).

Servicios de nube es uno de los distintos tipos de recursos de cálculo ofrecidos por Azure. Haga clic [aquí](cloud-services-choose-me.md) para obtener más información acerca de los servicios de nube.

> [AZURE.NOTE]Para ver los límites de Azure relacionados, vea [suscripción de Azure y los límites del servicio, cuotas y restricciones](../azure-subscription-service-limits.md)

## <a name="sizes-for-web-and-worker-role-instances"></a>Tamaños de web y trabajador instancias de función

Existen varios tamaños estándar para elegir en Azure. Consideraciones para algunos de estos tamaños incluyen:

* Máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que requieren mayor potencia de cálculo y rendimiento de disco temporal. Máquinas virtuales de serie D proporcionan procesadores más rápidos, una relación de memoria a core mayor y una unidad de estado sólido (SSD) para el disco temporal. Para obtener información detallada, consulte el anuncio en el blog de Azure [Nuevos tamaños de máquina Virtual de serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

* Características de serie de Dv2, una evolución de la serie D original, una CPU más eficaz. La CPU Dv2 serie es 35% más rápido que la CPU D serie. Se basa en la última generación v3 de 2,4 GHz Intel Xeon® E5-2673 procesador (Haswell) y con el aumento de tecnología de Intel Turbo 2.0, puede ir 3.1 GHz. La serie de Dv2 tiene las mismas configuraciones de memoria y disco como la serie D.

*   Máquinas virtuales de serie G ofrecen más memoria y ejecutan en hosts que tengan procesadores de la familia Intel Xeon E5 V3.

*   Las máquinas virtuales de una serie se pueden implementar en una variedad de tipos de hardware y procesadores. El tamaño está limitado, según el hardware, para ofrecer el rendimiento del procesador coherentes para la instancia en ejecución, independientemente del hardware se implementa en. Para determinar el hardware físico en el que se implementa este tamaño, consultar el hardware virtual desde dentro de la máquina Virtual.

*   El tamaño A0 es excesivo suscrito en el hardware físico. Para este específico tamaño, otras implementaciones de cliente pueden afectar al rendimiento de la carga de trabajo en ejecución. A continuación se describe el rendimiento relativo como la línea de base esperado, sujeto a una dispersión aproximado de 15 por ciento.


El tamaño de la máquina virtual afecta a los precios. El tamaño también afecta a la capacidad de procesamiento, memoria y almacenamiento de la máquina virtual. Los costos de almacenamiento se calculan por separado basándose en páginas utilizadas en la cuenta de almacenamiento. Para obtener más información, consulte [Detalles de precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/) y [Precios de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/). 


Las siguientes consideraciones pueden ayudarle a decidir sobre un tamaño:


* Los tamaños A8 A11 y H serie también conocido como son *instancias de descarga intensiva*. El hardware que se ejecute estos tamaños está diseñado y optimizado para descarga intensiva y aplicaciones de red intensiva, incluyendo la informática de alto rendimiento (HPC) clúster simulaciones, modelado y aplicaciones. La serie de A8 A11 utiliza Intel Xeon E5-2670 @ 2.6 GHZ y la serie de H utiliza Intel Xeon E5-2667 v3 @ 3,2 GHz. Para obtener información detallada y consideraciones sobre el uso de estos tamaños, vea [acerca de las máquinas virtuales de serie de una serie de H y descarga intensiva](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md). 

* Serie Dv2, serie D, G-series, es ideal para las aplicaciones que demandan CPU más rápidas, local mejor rendimiento en disco o tienen demandas de memoria superior.  Ofrecen una combinación eficaces para muchas aplicaciones empresariales.

*   Algunos de los hosts físicos en centros de datos de Azure no admiten tamaños de máquina virtual más grandes, como A5: A11. Como resultado, es posible que vea el mensaje de error **no se pudo configurar máquina virtual {nombre del equipo}** o **no se pudo crear máquina virtual {nombre del equipo}** al cambiar el tamaño de un equipo virtual existente a un nuevo tamaño. crear una nueva máquina virtual en una red virtual creada antes del 16 de abril de 2013; o agregar una nueva máquina virtual a un servicio de nube existente. Consulte [Error: "Error al configurar la máquina virtual"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) en el foro de soporte técnico para alternativas para cada escenario de implementación.  

* La suscripción también podría limitar el número de núcleos que puede implementar en ciertas familias de tamaño. Para aumentar la cuota, póngase en contacto con soporte técnico de Azure.


## <a name="performance-considerations"></a>Consideraciones de rendimiento

Hemos creado el concepto de la Azure calcular unidad (ACU) para proporcionar una manera de comparar el rendimiento de cálculo (CPU) a través de Azure SKU. Esto le ayudará a identificar fácilmente que SKU es más probable satisfacer sus necesidades de rendimiento.  Actualmente se normalizados ACU en un pequeño (Standard_A1) VM está 100 y todos los SKU luego representa aproximadamente cuánto más rápido que SKU puede ejecutar una prueba comparativa estándar. 

>[AZURE.IMPORTANT] La ACU es sólo de referencia.  Los resultados de la carga de trabajo pueden variar. 

<br>

|Familia SKU |ACU o Core |
|---|---|
|[Standard_A0](#a-series)   |50 |
|[Standard_A1-4](#a-series) |100 |
|[Standard_A5-7](#a-series) |100 |
|[A8 A11](#a-series)    |225 *|
|[D1 14](#d-series) |160 |
|[D1 15v2](#dv2-series) |210 - 250 *|
|[G1-5](#g-series)  |180 - 240 *|
|[H](#h-series) |290 - 300 *|

ACUs marcados con un * utilizar la tecnología de Intel® Turbo para aumentar la frecuencia de CPU y proporcione una mejora del rendimiento.  La cantidad de la amplificación puede variar según el tamaño de la máquina virtual, carga de trabajo y otras cargas de trabajo que se ejecuta en el mismo host.

## <a name="size-tables"></a>Tamaño de tablas

En las siguientes tablas se muestran los tamaños y las capacidades que proporcionan.

* Se muestra la capacidad de almacenamiento en unidades de GiB o 1024 ^ 3 bytes. Cuando la comparación de discos mide en GB (1000 ^ 3 bytes) a discos mide en GiB (1024 ^ 3) Recuerde que los números de capacidad determinados GiB pueden aparecer más pequeños. Por ejemplo, GiB 1023 = 1098.4 GB

* Rendimiento del disco se mide en operaciones de entrada y salida por segundo (IOPS) y MBps donde MBps = 10 ^ 6 bytes/seg.

* Discos de datos pueden funcionar en los modos de caché o. Para la operación de disco de datos en caché, el modo caché de host se establece en **sólo lectura** o **ReadWrite**.  Para la operación de disco de datos sin almacenar en caché, el modo caché de host se establece en **Ninguno**.

* Ancho de banda de red máximo es el máximo ancho de banda agregado asignados y asignadas por tipo de máquina virtual. El ancho de banda máximo proporciona directrices para seleccionar el tipo de máquina virtual adecuado para garantizar la capacidad de red adecuada están disponible. Al mover entre bajo, moderado, alto y muy alto, el rendimiento aumentará en consecuencia. Rendimiento de la red real dependerá de muchos factores como la red y cargas de aplicaciones y configuración de red de la aplicación.


## <a name="a-series"></a>Serie

| Tamaño        | Núcleos de CPU | Memoria: GiB | Unidad de disco duro: GiB | Discos de datos MAX | Rendimiento del disco de datos MAX: IOPS | Max NIC / ancho de banda de red |
|-------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A0 | 1         | 0.768        | 20                    | 1              | 1 x 500              | 1 / baja                   |
| Standard_A1 | 1         | 1,75         | 70                    | 2              | 2 x 500              | 1 / moderado              |
| Standard_A2 | 2         | 3,5 GB       | 135                   | 4              | 4 x 500              | 1 / moderado              |
| Standard_A3 | 4         | 7            | 285                   | 8              | 8 x 500              | 2 / alta                  |
| Standard_A4 | 8         | 14           | 605                   | 16             | 16 x 500             | 4 / alta                  |
| Standard_A5 | 2         | 14           | 135                   | 4              | 4 X 500              | 1 / moderado              |
| Standard_A6 | 4         | 28           | 285                   | 8              | 8 x 500              | 2 / alta                  |
| Standard_A7 | 8         | 56           | 605                   | 16             | 16 x 500             | 4 / alta                  |

## <a name="a-series---compute-intensive-instances"></a>Serie de instancias de descarga intensiva

Para obtener información y consideraciones sobre el uso de estos tamaños, consulte [acerca de las máquinas virtuales de serie de una serie de H y descarga intensiva](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).


| Tamaño         | Núcleos de CPU | Memoria: GiB | Unidad de disco duro: GiB | Discos de datos MAX | Rendimiento del disco de datos MAX: IOPS | Max NIC / ancho de banda de red |
|--------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A8 * | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / alta                  |
| Standard_A9 * | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / muy alta             |
| Standard_A10 | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / alta                  |
| Standard_A11 | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / muy alta             |

* RDMA capaz

## <a name="d-series"></a>Serie D


| Tamaño         | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos MAX | Rendimiento del disco de datos MAX: IOPS | Max NIC / ancho de banda de red |
|--------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / moderado              |
| Standard_D2  | 2         | 7            | 100                  | 4              | 500 x 4              | 2 / alta                  |
| Standard_D3  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / alta                  |
| Standard_D4  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / alta                  |
| Standard_D11 | 2         | 14           | 100                  | 4              | 4 x 500              | 2 / alta                  |
| Standard_D12 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / alta                  |
| Standard_D13 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / alta                  |
| Standard_D14 | 16        | 112          | 800                  | 32             | 32 x 500             | 8 / muy alta             |

## <a name="dv2-series"></a>Serie Dv2

| Tamaño            | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos MAX | Rendimiento del disco de datos MAX: IOPS | Max NIC / ancho de banda de red |
|-----------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1_v2  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / moderado              |
| Standard_D2_v2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2 / alta                  |
| Standard_D3_v2  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / alta                  |
| Standard_D4_v2  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / alta                  |
| Standard_D5_v2  | 16        | 56           | 800                  | 32             | 32 x 500             | 8 / muy alta        |
| Standard_D11_v2 | 2         | 14           | 100                  | 4              | 4 x 500              | 2 / alta                  |
| Standard_D12_v2 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / alta                  |
| Standard_D13_v2 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / alta                  |
| Standard_D14_v2 | 16        | 112          | 800                  | 32             | 32 x 500             | 8 / muy alta        |
| Standard_D15_v2 | 20        | 140          | 1.000                | 40             | 40 x 500             | 8 / muy alta        |

## <a name="g-series"></a>G-series

| Tamaño        | Núcleos de CPU | Memoria: GiB  | SSD local: GiB  | Discos de datos MAX | Rendimiento del disco Max: IOPS | Max NIC / ancho de banda de red |
|-------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_G1 | 2         | 28           | 384                  | 4              | 4 x 500            | 1 / alta                  |
| Standard_G2 | 4         | 56           | 768                  | 8              | 8 x 500            | 2 / alta                  |
| Standard_G3 | 8         | 112          | 1.536                | 16             | 16 x 500           | 4 / muy alta             |
| Standard_G4 | 16        | 224          | 3.072                | 32             | 32 x 500           | 8 / muy alta        |
| Standard_G5 | 32        | 448          | 6,144                | 64             | 64 x 500           | 8 / muy alta        |


## <a name="h-series"></a>Serie de H

Azure máquinas virtuales de H serie son la próxima generación informática de alto rendimiento que máquinas virtuales destinadas a las necesidades de cálculo de gama alta, como modelado molecular y dinámica de fluidos. Estas 8 y máquinas virtuales de 16 principales se basan en la tecnología de procesador Intel Haswell E5 2667 V3 con DDR4 memoria y almacenamiento SSD según local. 

Además de la CPU considerable, la serie de H ofrece diversas opciones para redes de latencia baja RDMA usar FDR InfiniBand y varias configuraciones de memoria para admitir requisitos de cálculo intensivo de memoria.


| Tamaño           | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos MAX | Rendimiento del disco Max: IOPS | Max NIC / ancho de banda de red |
|----------------|-----------|-------------|--------------------------|----------------|---------------------------|------------------------------|
| Standard_H8    | 8         | 56          | 1000                     | 16             | 16 x 500                    | 8 / alta                      |
| Standard_H16   | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / muy alta                  |
| Standard_H8m   | 8         | 112         | 1000                     | 16             | 16 x 500                    | 8 / alta                      |
| Standard_H16m  | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / muy alta                 |
| Standard_H16r * | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / muy alta                  |
| Standard_H16mr * | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / muy alta                  |


* RDMA capaz

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Notas: Estándar A0 - A4 mediante CLI y PowerShell 

En el modelo de implementación clásica, algunos nombres de tamaño de la máquina virtual son ligeramente diferentes en CLI y PowerShell:

* Standard_A0 es ExtraSmall 
* Standard_A1 es pequeño
* Standard_A2 es medio
* Standard_A3 es grande
* Standard_A4 es ExtraLarge

## <a name="configure-sizes-for-cloud-services"></a>Configurar tamaños para los servicios de nube

Puede especificar el tamaño de la máquina Virtual de una instancia de funciones como parte del modelo de servicio que describe el [archivo de definición de servicio](cloud-services-model-and-package.md#csdef). El tamaño de la función determina el número de núcleos de CPU, la capacidad de memoria y el tamaño del sistema de archivos local que está asignado a una instancia de ejecución. Elija el tamaño de la función basándose en requisitos de recursos de la aplicación.

Aquí tenemos un ejemplo para configurar el tamaño de la función sea [Standard_D2](#general-purpose-d) para una instancia de rol Web:

```xml
<WorkerRole name="Worker1" vmsize="<mark>Standard_D2</mark>">
...
</WorkerRole>
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre la [suscripción de azure y los límites del servicio, cuotas y restricciones](../azure-subscription-service-limits.md).
- Obtenga más información [acerca de la serie de H y descarga intensiva máquinas virtuales de una serie](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) de cargas de trabajo como de alto rendimiento informática (HPC).

