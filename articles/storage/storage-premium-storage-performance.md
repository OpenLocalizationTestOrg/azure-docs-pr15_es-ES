<properties
    pageTitle="Almacenamiento de Azure Premium: Diseñar el rendimiento | Microsoft Azure"
    description="Diseñar aplicaciones de alto rendimiento con el almacenamiento de Azure Premium. Almacenamiento de Premium ofrece soporte de disco de alto rendimiento, baja latencia para las cargas de trabajo O-intensa ejecuta en Azure máquinas virtuales de Windows."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="aungoo"/>

# <a name="azure-premium-storage-design-for-high-performance"></a>Almacenamiento de Azure Premium: Diseño de alto rendimiento

## <a name="overview"></a>Información general  
Este artículo proporciona instrucciones para crear aplicaciones de alto rendimiento con el almacenamiento de Azure Premium. Puede usar las instrucciones de este documento combinado con mejores prácticas de performance aplicables a tecnologías utilizadas por la aplicación. Para ilustrar las directrices, hemos utilizado SQL Server en almacenamiento Premium como ejemplo en este documento.

Mientras se soluciona escenarios de rendimiento para la capa de almacenamiento en este artículo, debe optimizar el nivel de aplicación. Por ejemplo, si va a hospedar una granja de SharePoint en el almacenamiento de Azure Premium, puede usar los ejemplos de SQL Server de este artículo para optimizar el servidor de base de datos. Además, optimizar el servidor Web y el servidor de aplicaciones para obtener el máximo rendimiento de la granja de SharePoint.

En este artículo le ayudará a answer siguiendo preguntas comunes sobre la optimización del rendimiento de la aplicación en el almacenamiento de Azure Premium

-   ¿Cómo puedo medir el rendimiento de la aplicación?  
-   ¿Por qué no se ve esperados de alto rendimiento?  
-   ¿Los factores que influyen en el rendimiento de la aplicación de almacenamiento de Premium?  
-   ¿Cómo influyen estos factores en rendimiento de la aplicación de almacenamiento de Premium?  
-   ¿Cómo puede optimizar para IOPS, ancho de banda y latencia?  

Proporcionamos estas directrices específicamente para el almacenamiento de Premium porque las cargas de trabajo que se ejecuta en Storage Premium están muy confidencial de rendimiento. Proporcionamos ejemplos donde corresponda. También puede aplicar algunas de estas instrucciones a aplicaciones que se ejecutan en máquinas virtuales de IaaS con discos de almacenamiento estándar.

Antes de comenzar, si es nuevo en almacenamiento Premium, lea primero el [almacenamiento Premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](storage-premium-storage.md) artículo y [objetivos de rendimiento y escalabilidad de almacenamiento de Premium de Azure](storage-scalability-targets.md#premium-storage-accounts).

## <a name="application-performance-indicators"></a>Indicadores de rendimiento de la aplicación  
Se calcula si una aplicación está realizando bien o no usar como indicadores de rendimiento, la velocidad procesa una solicitud de usuario, la cantidad de datos que procesa la aplicación por cada solicitud de la aplicación, cuántas solicitudes es un proceso de aplicación en un período específico de tiempo, ¿cuánto tiempo que un usuario tiene que esperar para obtener una respuesta después de enviar su solicitud. Son las condiciones técnicas de estos indicadores de rendimiento, IOPS, el rendimiento o el ancho de banda y latencia.

En esta sección, se describen los indicadores de rendimiento comunes en el contexto de almacenamiento de Premium. En la siguiente sección, requisitos de la aplicación de recopilación, aprenderá cómo medir estos indicadores de rendimiento de la aplicación. Más adelante en la optimización del rendimiento de la aplicación, aprenderá sobre los factores que afectan a estos indicadores de rendimiento y recomendaciones para optimizar ellos.

## <a name="iops"></a>IOPS  
IOPS es el número de solicitudes de que su aplicación se envíe a los discos de almacenamiento en un segundo. Una operación de entrada y salida se puede leer o escribir, secuencial o aleatorio. Aplicaciones de OLTP como una tienda en línea que necesite procesar muchas solicitudes de usuario simultáneas inmediatamente. Las solicitudes de usuario son insertar y actualizan las transacciones de bases de datos, que debe procesar rápidamente la aplicación. Por lo tanto, aplicaciones OLTP requieren IOPS muy alta. Estas aplicaciones controlan millones de solicitudes de IO pequeñas y aleatorias. Si tiene una aplicación, debe diseñar la infraestructura de aplicaciones para optimizar el IOPS. En la sección posterior, la *Optimización del rendimiento de la aplicación*, se describen en detalle todos los factores que debe considerar obtener IOPS alta.

Cuando adjunta un disco de almacenamiento premium a gran escala VM, Azure disposiciones para que un número garantizado de IOPS según la especificación de disco. Por ejemplo, un disco P30 aprovisiona 5000 IOPS. Cada escala de gran tamaño de memoria virtual también tiene un límite IOPS específico puede mantener. Por ejemplo, una máquina virtual de GS5 estándar tiene 80.000 IOPS limitar.

## <a name="throughput"></a>Rendimiento  
Rendimiento o ancho de banda es la cantidad de datos que la aplicación está enviando a los discos de almacenamiento en un intervalo especificado. Si la aplicación está realizando operaciones de entrada y salida con tamaños de unidad IO grandes, requiere alto rendimiento. Aplicaciones de almacén de datos suelen emitir operaciones de intensiva de análisis que tener acceso a grandes conjuntos de datos en un momento y suele realizan operaciones de forma masiva. En otras palabras, estas aplicaciones requieren un rendimiento mayor. Si tiene una aplicación, debe diseñar su infraestructura para optimizar el rendimiento. En la siguiente sección, trataremos en detalle los factores que debe optimizar para ello.

Cuando adjunta un disco de almacenamiento premium a una escala de alta VM, Azure disposiciones rendimiento según esa especificación de disco. Por ejemplo, un disco P30 aprovisiona 200 MB por segundo disco rendimiento. Cada escala de gran tamaño VM también tiene como límite específico de rendimiento que puede admitir. Por ejemplo, VM GS5 estándar tiene un rendimiento máximo de 2.000 MB por segundo.

Hay una relación entre el rendimiento y IOPS tal como se muestra en la siguiente fórmula.

![](media/storage-premium-storage-performance/image1.png)

Por lo tanto, es importante determinar los valores IOPS y rendimiento óptimo que requiere la aplicación. Mientras intenta optimizar uno, el otro también obtiene afectado. En una sección posterior, la *Optimización del rendimiento de la aplicación*, trataremos con más detalles acerca de cómo optimizar IOPS y rendimiento.

## <a name="latency"></a>Latencia  
La latencia es el tiempo que tarda una aplicación para recibir una solicitud, envíe a los discos de almacenamiento y enviar la respuesta al cliente. Esta es una medida crítica de rendimiento de la aplicación además de IOPS y rendimiento. La latencia de un disco de almacenamiento de premium es el tiempo que se tarda en recuperar la información de una solicitud y comunicar a la aplicación. Almacenamiento de Premium proporciona latencia baja coherente. Si habilita el almacenamiento en caché en discos de almacenamiento de premium de host de sólo lectura, puede obtener mucho menor latencia de lectura. Trataremos la caché de disco con más detalle en la sección posterior de *Optimización del rendimiento de la aplicación*.

Optimización de su aplicación para obtener IOPS y rendimiento más altos, afectará a la latencia de la aplicación. Después de ajustar el rendimiento de la aplicación, siempre se evalúan la latencia de la aplicación para evitar el comportamiento de latencia alta inesperados.

## <a name="gather-application-performance-requirements"></a>Recopilar los requisitos de rendimiento de la aplicación  
Es el primer paso en el diseño de aplicaciones de alto rendimiento que se ejecutan en el almacenamiento de Azure Premium, para conocer los requisitos de rendimiento de la aplicación. Una vez recopilada requisitos de rendimiento, puede optimizar la aplicación para lograr un rendimiento óptimo.

En la sección anterior, explicamos los indicadores de rendimiento comunes, IOPS, el rendimiento y la latencia. Debe identificar cuál de estos indicadores de rendimiento son fundamentales para su aplicación para ofrecer la experiencia del usuario que desee. Por ejemplo, alta IOPS más importante para las aplicaciones de OLTP procesamiento millones de transacciones en un segundo. Mientras que el alto rendimiento es fundamental para aplicaciones de almacén de datos de procesamiento de grandes cantidades de datos en un segundo. Muy baja latencia es fundamental para las aplicaciones en tiempo real como sitios Web de transmisión de vídeo en directo.

A continuación, mida los requisitos de rendimiento máximo de la aplicación a lo largo de su duración. Use la lista de comprobación muestra como un inicio. Grabar los requisitos de rendimiento máximo durante normal, pico y laborables períodos de carga de trabajo. Identificando requisitos para todos los niveles de cargas de trabajo, podrá determinar el requisito de rendimiento general de la aplicación. Por ejemplo, la carga de trabajo normal de un sitio Web de comercio electrónico serán las transacciones sirve durante casi todos los días de un año. La carga de trabajo de recursos asignadas del sitio Web serán las transacciones sirve durante las vacaciones o eventos de venta especial. La carga de trabajo de recursos asignadas es normalmente experimentado durante un período limitado, pero puede que la aplicación deba ajustar su funcionamiento normal de dos o más veces. Averigüe el percentil 50, percentil 90 y requisitos de percentil 99. Esto le permite filtrar los valores atípicos en los requisitos de rendimiento y puede centrar sus esfuerzos en optimizar para los valores correctos.

**Lista de comprobación de requisitos de rendimiento de aplicación**

| **Requisitos de rendimiento** | **Percentil 50** | **Percentil 90** | **Percentil 99** |
|---|---|---|---|
| Max. Transacciones por segundo | | | |
| % De las operaciones de lectura            | | | |
| % De las operaciones de escritura           | | | |
| Operaciones aleatorias de %          | | | |
| % De operaciones secuenciales      | | | |
| Tamaño de la solicitud de IO              | | | |
| Rendimiento medio           | | | |
| Max. Rendimiento              | | | |
| Min. Latencia                 | | | |
| Latencia promedio              | | | |
| Max. CPU                     | | | |
| CPU promedio                  | | | |
| Max. Memoria                  | | | |
| Memoria promedio               | | | |
| Profundidad de cola                  | | | |

>**Nota importante:**  
>Considere la posibilidad de escalado estos números basándose en aumento futuro esperado de la aplicación. Es una buena idea planificar para el crecimiento antelación, porque podría ser más difícil cambiar la infraestructura para mejorar el rendimiento más adelante.

Si tiene una aplicación existente y desea mover a almacenamiento Premium, cree primero la lista de comprobación encima de la aplicación existente. A continuación, crear un prototipo de la aplicación de almacenamiento de Premium y diseñar la aplicación basándose en las instrucciones descritas en la *Optimización del rendimiento de la aplicación* en una sección posterior de este documento. La siguiente sección describe las herramientas que puede usar para recopilar las medidas de rendimiento.

Crear una lista de comprobación similar a la aplicación existente del prototipo. Usar herramientas de Benchmarking puede simular las cargas de trabajo y medir el rendimiento de la aplicación de prototipo. Consulte la sección sobre [Benchmarking](#benchmarking) para obtener más información. Por lo que puede determinar si almacenamiento Premium puede asociar o supere los requisitos de rendimiento de la aplicación. A continuación puede aplicar las mismas directrices para la aplicación de producción.

### <a name="counters-to-measure-application-performance-requirements"></a>Contadores para medir los requisitos de rendimiento de la aplicación  
La mejor manera para medir los requisitos de rendimiento de la aplicación es usar herramientas de supervisión de rendimiento proporcionadas por el sistema operativo del servidor. Puede usar el Monitor de rendimiento para Windows y iostat para Linux. Estas herramientas capturan contadores correspondiente a cada medida que se explica en la sección anterior. Debe capturar los valores de estos contadores cuando la aplicación está ejecutando su normal, pico y laborables cargas de trabajo.

Los contadores están disponibles para procesador, memoria, cada disco lógico y disco físico de su servidor. Cuando utiliza discos de almacenamiento de premium con una máquina virtual, son los contadores disco físico para cada disco de almacenamiento premium y contadores son para cada volumen que creó en los discos de almacenamiento de premium. Debe capturar los valores de los discos que hospeda la carga de trabajo de la aplicación. Si hay una asignación de uno a uno entre discos lógicos y físicos, puede hacer referencia a contadores de disco físico; en caso contrario, consulte los contadores disco lógico. En Linux, el comando iostat genera un informe de uso de CPU y de disco. El informe de uso de disco proporciona estadísticas por dispositivo físico o partición. Si tiene un servidor de base de datos con sus datos y el registro en discos independientes, recopilar estos datos para ambos discos. Debajo de la tabla se describen otros sobre discos, procesador y memoria:

| Contador | Descripción | Monitor de rendimiento | Iostat |
|---|---|---|---|
| **IOPS o transacciones por segundo** | Número de solicitudes de i/OS emitido en el disco de almacenamiento por segundo. | Lecturas <br> Disco/seg | TPS <br> r/s <br> w/s |
| **Disco lectura y escritura** | % de lectura y escritura de las operaciones realizadas en el disco. | % De tiempo de lectura de disco <br> % De tiempo de escritura de disco | r/s <br> w/s |
| **Rendimiento** | Cantidad de datos leer o escribir en el disco por segundo. | Disco Bytes s. <br> Bytes de escritura de disco por segundo | kB_read/s <br> kB_wrtn/s |
| **Latencia** | Tiempo total para completar una solicitud de IO de disco. | Disco promedio/seg <br> Promedio de disco/seg | espera <br> svctm |
| **Tamaño de IO** | El tamaño de i/OS solicita problemas a los discos de almacenamiento. | Promedio Bytes de lectura <br> Promedio Bytes de escritura | avgrq sz |
| **Profundidad de cola** | Número de pendientes i/OS solicitudes pendientes de formulario de leer o escribir en el disco de almacenamiento. | Longitud de cola de disco actual | avgqu sz |
| **Max. Memoria** | Cantidad de memoria necesaria para ejecutar la aplicación sin problemas | % De Bytes ejecutados en uso | Usar vmstat |
| **Max. CPU** | Cantidad de CPU necesaria para ejecutar la aplicación sin problemas | % De tiempo de procesador | % utilizada |

Obtenga más información sobre [iostat](http://linuxcommand.org/man_pages/iostat1.html) y [rendimiento](https://msdn.microsoft.com/library/aa645516.aspx).


## <a name="optimizing-application-performance"></a>Optimizar el rendimiento de la aplicación  
Los principales factores que influyen en el rendimiento de una aplicación que se ejecuta en Storage Premium son naturaleza de IO solicitudes, tamaño de memoria virtual, el tamaño de disco, número de discos, la caché de disco, el Multithreading y profundidad de cola. Puede controlar algunos de estos factores con botones proporcionados por el sistema. La mayoría de las aplicaciones no pueden dar una opción para modificar el tamaño de IO y de profundidad de cola directamente. Por ejemplo, si está utilizando SQL Server, puede elegir la profundidad de cola y tamaño de IO. SQL Server elige la óptima IO cola profundidad valores de tamaño y para obtener el máximo rendimiento. Es importante comprender los efectos de ambos tipos de factores de rendimiento de su aplicación para que pueden aprovisionar recursos adecuados para satisfacer las necesidades de rendimiento.

En esta sección, consulte la lista de comprobación de requisitos de aplicación que ha creado para identificar cuánto debe optimizar el rendimiento de la aplicación. En función de ello, podrá determinar qué factores de esta sección debe ajustar. Asistir a los efectos de cada factor en el rendimiento de la aplicación, ejecutar herramientas de referencia en la configuración de la aplicación. Consulte la sección [Benchmarking](#Benchmarking) al final de este artículo para conocer los pasos ejecutar las herramientas de referencia comunes en Windows y máquinas virtuales de Linux.

### <a name="optimizing-iops-throughput-and-latency-at-a-glance"></a>Optimizar IOPS, el rendimiento y la latencia de un vistazo  
La siguiente tabla resume los factores de rendimiento y los pasos para optimizar IOPS, el rendimiento y la latencia. Las secciones siguientes este resumen describen cada factor es mucho más detalle.

| | **IOPS** | **Rendimiento** | **Latencia** |
|---|---|---|---|
| **Escenario de ejemplo** | Aplicación de empresa OLTP que requieren transacciones muy altas frecuencia por segundo.                                                                                                                                 | Procesamiento de grandes cantidades de datos de aplicación de almacenamiento de datos de empresa. | Cerca de aplicaciones en tiempo real que requieren instantáneas respuestas a solicitudes de usuario, como juegos en línea. |
| Factores de rendimiento  | | | |
| **Tamaño de IO** | Tamaño de IO da como resultado IOPS superior.                                                                                                                                                                           | Tamaño de IO más grande que da como resultado un rendimiento más alto. | |
| **Tamaño de memoria virtual** | Usar un tamaño de memoria virtual que ofrece IOPS mayor que el requisito de la aplicación. Vea tamaños de máquina virtual y los límites IOPS aquí. | Usar un tamaño VM con límite de rendimiento mayor que el requisito de la aplicación. Vea tamaños VM y sus los límites de rendimiento. | Usar un tamaño de memoria virtual que ofrece escala límites mayores que el requisito de la aplicación. Vea tamaños VM y sus límites aquí. |
| **Tamaño del disco** | Usar un tamaño de disco que ofrece IOPS mayor que el requisito de la aplicación. Vea los tamaños de disco y sus límites IOPS aquí. | Usar un tamaño de disco con límite de rendimiento mayor que el requisito de la aplicación. Vea tamaños de disco y sus los límites de rendimiento. | Utilice un tamaño de disco que ofrece escala límites mayores que el requisito de la aplicación. Vea los tamaños de disco y sus límites aquí. |
| **Máquina virtual y los límites de escala de disco** | Límite IOPS del tamaño VM elegido debe ser mayor que el total IOPS controlados por discos de almacenamiento de premium adjuntados. | Límite de rendimiento del tamaño VM elegido debe ser mayor que el rendimiento total controlado por discos de almacenamiento de premium adjuntados. | Límites de escala del tamaño VM elegido deben ser mayores que los límites de escala total premium adjunto de discos de almacenamiento. |
| **Almacenamiento en caché de disco** | Habilitar la caché de sólo lectura en discos de almacenamiento de premium con grandes operaciones de lectura para obtener mayor IOPS de lectura. | | Habilitar la caché de sólo lectura en discos de almacenamiento de premium con operaciones grandes listos para obtener lectura muy baja latencia. |
| **Bandas de disco** | Uso de varios discos y bandas de para obtener un límite superior combinado IOPS y rendimiento. Tenga en cuenta que el límite combinado por máquina virtual debe ser mayor que los límites combinados de discos premium adjunto. | |
| **Tamaño de las bandas** | Bandas más pequeños para aleatorio patrón IO pequeña visto en aplicaciones OLTP. Por ejemplo, utilice el tamaño de las bandas de 64KB para aplicación OLTP de SQL Server. | Mayor tamaño de las bandas secuenciales patrón IO grandes verse en aplicaciones de almacén de datos. Por ejemplo, utilice el tamaño de las bandas de 256KB para la aplicación de almacenamiento de datos de SQL Server. | |
| **Multithreading** | Uso de multithreading para un número mayor de solicitudes de inserción al almacenamiento de Premium que llevará a mayor IOPS y rendimiento. Por ejemplo, en SQL Server establecer un valor MAXDOP alto que desea asignar más CPU a SQL Server. | |
| **Profundidad de cola** | Mayor profundidad de cola da como resultado IOPS superior. | Mayor profundidad de cola da como resultado un rendimiento más alto. | Menor profundidad de cola da como resultado una latencia inferior. |

## <a name="nature-of-io-requests"></a>Naturaleza de solicitudes de IO  
Una solicitud de IO es una unidad de operación de entrada y salida que va a realizar la aplicación. Identificar la naturaleza de las solicitudes de IO, aleatorias o secuenciales, lectura o escritura, pequeño o grande, podrá determinar los requisitos de rendimiento de la aplicación. Es muy importante comprender la naturaleza de solicitudes de IO decisiones acertadas al diseñar la infraestructura de aplicaciones.

Tamaño de IO es uno de los factores más importantes. El tamaño de IO es el tamaño de la solicitud de operación de entrada y salida generado por la aplicación. El tamaño de IO tiene un impacto significativo en el rendimiento, especialmente en el IOPS y ancho de banda de la aplicación es capaz de lograr. La fórmula siguiente muestra la relación entre IOPS, ancho de banda y rendimiento y tamaño de IO.  
    ![](media/storage-premium-storage-performance/image1.png)

Algunas aplicaciones permiten cambiar su tamaño IO, mientras que algunas aplicaciones no. Por ejemplo, SQL Server determina el tamaño de IO óptimo propio y no proporcionar a los usuarios con los botones cambiarlo. Por otro lado, Oracle proporciona un parámetro denominado [DB\_bloquear\_tamaño](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) con que puede configurar el tamaño de la solicitud de i/OS de la base de datos.

Si está utilizando una aplicación, que no le permite cambiar el tamaño de IO, use las instrucciones de este artículo para optimizar el rendimiento KPI que es más importante para la aplicación. Por ejemplo,

-   Una aplicación OLTP genera millones de solicitudes de IO pequeñas y aleatorias. Para controlar estos tipos de solicitudes de IO, debe diseñar su infraestructura de la aplicación obtener IOPS superior.  
-   Una aplicación de almacenamiento de datos generan solicitudes de IO grandes y secuenciales. Para controlar estos tipos de solicitudes de IO, debe diseñar la infraestructura de aplicaciones para obtener mayor ancho de banda o el rendimiento.

Si está utilizando una aplicación, que le permite cambiar el tamaño de IO, utilice esta regla general para el tamaño de IO además de otras directrices de rendimiento

-   IO más pequeños para obtener IOPS superior. Por ejemplo, 8 KB para una aplicación OLTP.  
-   Tamaño de IO más grande para obtener un mayor ancho de banda y rendimiento. Por ejemplo, 1024 KB para una aplicación de almacén de datos.

Aquí tenemos un ejemplo de cómo puede calcular la IOPS y ancho de banda o el rendimiento de la aplicación. Considere la posibilidad de una aplicación con un disco P30. El máximo que pueden lograr IOPS y rendimiento o un disco P30 de ancho de banda es 5000 IOPS y 200 MB por segundo respectivamente. Ahora, si la aplicación requiere la máxima IOPS desde el disco de P30 y usar un tamaño de IO como 8 KB, el ancho de banda resultante podrá obtener es 40 MB por segundo. Sin embargo, si la aplicación requiere el máximo rendimiento y ancho de banda de disco P30 y usar un tamaño de IO más grande como 1024 KB, la IOPS resultantes será menor, 200 IOPS. Por lo tanto, ajuste el tamaño de IO tal que cumple con los requisitos de IOPS y ancho de banda o el rendimiento de ambas de la aplicación. Tabla siguiente resume los distintos tamaños de IO sus correspondientes IOPS y rendimiento para un disco P30.

| **Requisito de la aplicación** | **Tamaño de i/OS** | **IOPS** | **Rendimiento y ancho de banda** |
|-----------------------------|--------------|----------|--------------------------|
| Max IOPS                    | 8 KB         | 5.000    | 40 MB por segundo         |
| Rendimiento máximo              | 1024 KB      | 200      | 200 MB por segundo        |
| Max Throughput + IOPS alta  | 64 KB        | 3.200    | 200 MB por segundo        |
| Max IOPS + de alto rendimiento  | 32 KB        | 5.000    | 160 MB por segundo        |

Para obtener IOPS y ancho de banda mayor que el valor máximo de un disco de almacenamiento de prima única, use varios discos de premium seccionados juntos. Por ejemplo, bandas dos P30 discos para obtener un IOPS combinada de 10.000 IOPS o un rendimiento combinado de 400 MB por segundo. Como se explica en la siguiente sección, debe usar un tamaño de memoria virtual que admite la combinación IOPS y rendimiento de disco.

>**Nota:**  
>A medida que aumente IOPS o el otro también aumenta el rendimiento, asegúrese de que no toca rendimiento o límites IOPS del disco o VM al aumentar cualquiera de ellas.

Asistir a los efectos del tamaño de IO en el rendimiento de la aplicación, puede ejecutar herramientas de análisis de la máquina virtual y los discos. Crear varias ejecuciones de prueba y usar tamaño de IO diferente para cada ejecutar para ver el impacto. Consulte la sección [Benchmarking](#Benchmarking) al final de este artículo para obtener más detalles.

## <a name="high-scale-vm-sizes"></a>Tamaños de la máquina virtual de alta escala  
Al empezar a diseñar una aplicación, una de las primeras cosas que hacer es, elija una máquina virtual para hospedar la aplicación. Almacenamiento de Premium incluye tamaños de alta VM de escala que se pueden ejecutar aplicaciones que requieren mayor potencia de cálculo y un alto rendimiento de disco local. Estas máquinas virtuales proporcionan procesadores más rápidos, una relación de memoria a core mayor y una unidad de Solid-State (SSD) para el disco local. Ejemplos de alta máquinas virtuales de escala de soporte Premium almacenamiento son la serie DS, DSv2 y GS máquinas virtuales.

Máquinas virtuales de escala alta están disponibles en diferentes tamaños con un número diferente de CPU núcleos, memoria, sistema operativo y tamaño del disco temporal. El tamaño de cada VM también tiene el número máximo de discos de datos que puede adjuntar a la máquina virtual. Por lo tanto, el tamaño de la memoria virtual elegido afecta a la cantidad de proceso, memoria, y la capacidad de almacenamiento está disponible para su aplicación. También afecta el cálculo y costos de almacenamiento. Por ejemplo, a continuación encontrará las especificaciones del mayor tamaño VM en una serie de DS, DSv2 serie y una serie de GS:

| Tamaño de memoria virtual | Núcleos de CPU | Memoria | Tamaños de disco VM | Max. discos de datos | Tamaño de caché | IOPS | Límites de ancho de banda IO de caché |
|---|---|---|---|---|---|---|---|
| Standard_DS14 | 16 | 112 GB | OS = 1023 GB <br> SSD local = 224 GB | 32 | 576 GB | 50.000 IOP <br> Se recomienda 512 MB por segundo | 4.000 IOPS y 33 MB por segundo |
| Standard_GS5 | 32 | 448 GB | OS = 1023 GB <br> SSD local = 896 GB | 64 | 4224 GB | 80.000 IOPS <br> 2.000 MB por segundo | 5.000 IOPS y 50 MB por segundo |

Para ver una lista completa de todos los tamaños de Azure VM disponibles, consulte [tamaños de máquina virtual de Windows](../virtual-machines/virtual-machines-windows-sizes.md) o [tamaños de máquina virtual Linux](../virtual-machines/virtual-machines-linux-sizes.md). Elija un tamaño de memoria virtual que puede reunirse y escalar a sus requisitos de rendimiento de la aplicación que desee. Además, tenga en cuenta siguiendo consideraciones al elegir tamaños de máquina virtual.


*Límites de escala*  
Los límites máximos de IOPS por disco y VM son diferentes e independientes entre sí. Asegúrese de que la aplicación está dictando IOPS dentro de los límites de la máquina virtual, así como los discos premium adjuntos. En caso contrario, el rendimiento de la aplicación experimentará limitación.

Por ejemplo, supongamos que un requisito de la aplicación es un máximo de 4.000 IOPS. Para ello, proporcionando un disco P30 en una máquina virtual de DS1. El disco P30 puede ofrecer hasta 5.000 IOPS. Sin embargo, la VM DS1 se limita a 3.200 IOPS. Por consiguiente, el rendimiento de la aplicación se restringe por el límite VM en 3.200 IOPS y habrá disminución del rendimiento. Para evitar esta situación, elija un tamaño de disco y VM que ambos cumple los requisitos de aplicación.

*Costo de operación*  
En muchos casos, es posible que el costo total de la operación con el almacenamiento de Premium es menor que usa almacenamiento estándar.

Por ejemplo, considere una aplicación que requieren 16000 IOPS. Para lograr este rendimiento, necesitará un estándar\_D14 Azure IaaS VM, que puede dar una IOPS máxima de 16.000 con 32 discos de 1TB de almacenamiento estándar. Cada disco de almacenamiento estándar de 1TB puede lograr un máximo de 500 IOPS. El costo estimado de este VM al mes será 1,570 $. El coste mensual de 32 discos de almacenamiento estándar será 1,638 $. El costo mensual total estimado será 3,208 $.

Sin embargo, si hospeda la misma aplicación en el almacenamiento de Premium, necesitará un pequeño VM y menos discos de almacenamiento de premium, lo que reduce el costo total. Un estándar\_DS13 VM puede cumplir los requisitos de IOPS 16000 con cuatro discos P30. La máquina virtual de DS13 tiene una máximo de IOPS de 25,600 y cada disco P30 tiene una máximo de IOPS de 5.000. En general, esta configuración puede lograr 5.000 x 4 = 20.000 IOPS. El costo estimado de este VM al mes será 1,003 $. El coste mensual de discos de almacenamiento de cuatro P30 premium será 544.34 $. El costo mensual total estimado será 1,544 $.

La tabla siguiente resume el análisis de costos de este escenario para estándar y Premium almacenamiento.

| | **Estándar** | **Premium** |
|---|---|---|
| **Costo de VM por mes.** | 1,570.58 $ (estándar\_D14)   | 1,003.66 $ (estándar\_DS13) |
| **Costo de discos por mes.** | 1,638.40 $ (discos 32 x 1 TB) | 544.34 $ (4 x P30 discos) |
| **Costo total por mes.**  | $3,208.98 | $1,544.34 |

*Linux Distros*  

Con el almacenamiento de Azure Premium, obtendrá el mismo nivel de rendimiento para máquinas virtuales ejecutando Windows y Linux. Se admiten distintas versiones de Linux distros y, a continuación, puede ver la lista completa de [aquí](../virtual-machines/virtual-machines-linux-endorsed-distros.md). Es importante que tenga en cuenta que distros diferentes se ajusten mejor para distintos tipos de cargas de trabajo. Verá diferentes niveles de rendimiento en función de la distro en que la carga de trabajo se ejecuta. Probar la distros Linux con la aplicación y elija el elemento que funciona mejor.


Cuando se ejecuta Linux con almacenamiento Premium, comprobar las actualizaciones más recientes acerca de los controladores necesarios para asegurarse de alto rendimiento.

## <a name="premium-storage-disk-sizes"></a>Tamaños de disco de almacenamiento de Premium  
Almacenamiento de Azure Premium ofrece tres tamaños de disco actualmente. Tamaño de cada disco tiene un límite de escala diferente para IOPS, ancho de banda y almacenamiento. Elija el tamaño del disco de almacenamiento de Premium según los requisitos de la aplicación y el tamaño de memoria virtual de alta escala derecha. La siguiente tabla muestra los tamaños de tres discos y sus capacidades.

| **Tipo de disco**       | **P10**           | **P20**           | **P30**           |
|---------------------|-------------------|-------------------|-------------------|
| Tamaño del disco           | GiB 128           | 512 giB           | 1024 giB (1 TB)   |
| IOPS por disco       | 500               | 2300              | 5000              |
| Rendimiento por disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo |

El tamaño de cuántos discos que elija depende del disco elegido. Puede utilizar un disco P30 único o múltiples discos P10 para cumplir con el requisito de la aplicación. Tenga en cuenta consideraciones que aparece debajo de la elección.

*Límites de escala (IOPS y rendimiento)*  
Los límites de IOPS y rendimiento de tamaño de cada disco Premium es diferente e independiente de los límites de escala de la máquina virtual. Asegúrese de que el IOPS total y el rendimiento de los discos se encuentran dentro de límites de escala del tamaño VM elegido.

Por ejemplo, si un requisito de la aplicación es un máximo de 250 MB/seg rendimiento y está utilizando una máquina virtual de DS4 con un solo disco P30. La máquina virtual de DS4 puede asignar hasta 256 MB/seg rendimiento. Sin embargo, un único disco P30 tiene límite de rendimiento de 200 MB/seg. Por lo tanto, la aplicación se restringe en 200 MB/seg debido al límite de disco. Para superar este límite, aprovisionar más de uno de los discos de datos de la máquina virtual.

>**Nota:**  
>Lecturas servidas por la caché no se incluyen en el disco IOPS y rendimiento, por lo tanto, no está sujeto a los límites de disco. Caché tiene su límite IOPS y rendimiento separada por máquina virtual.
>
>Por ejemplo, inicialmente su lectura y escritura es 60MB/y 40MB por segundo respectivamente. Con el tiempo, la caché vez preparada y sirve cada vez más de las lecturas de la memoria caché. A continuación, puede obtener el rendimiento de escritura superior desde el disco.

*Número de discos*  
Determinar el número de discos que necesitará evaluar los requisitos de la aplicación. El tamaño de cada VM también tiene un límite del número de discos que puede adjuntar a la máquina virtual. Normalmente, esto es dos veces el número de núcleos. Asegúrese de que el tamaño de la memoria virtual que elija puede admitir el número de discos necesarios.

Recuerde que los discos de almacenamiento de Premium tener mayor capacidades de rendimiento en comparación con discos de almacenamiento estándar. Por lo tanto, si va a migrar su aplicación de Azure IaaS VM mediante almacenamiento estándar para el almacenamiento de Premium, es probable que necesitará menos discos premium para lograr el rendimiento igual o superior de la aplicación.

## <a name="disk-caching"></a>Almacenamiento en caché de disco  
Alta máquinas virtuales de escala que aprovechar el almacenamiento de Azure Premium tiene una tecnología de almacenamiento en caché de varios niveles denominada BlobCache. BlobCache utiliza una combinación de la máquina Virtual de RAM y SSD local para almacenar en caché. Esta caché está disponible para el almacenamiento de Premium persistente discos y la máquina virtual local. De forma predeterminada, esta configuración de caché se establece en lectura y escritura para discos de sistemas operativos como de sólo lectura para los discos de datos hospedados en almacenamiento Premium. Con disco habilitada la caché en los discos de almacenamiento de Premium, la escala alta máquinas virtuales puede lograr muy altos niveles de rendimiento que superan el rendimiento de disco subyacente.

>[AZURE.WARNING] Cambiar la configuración de caché de un disco de Azure desconecta y vuelve a conecta el disco de destino. Si es el disco de sistema operativo, se reinicia la máquina virtual. Detener todas las aplicaciones y servicios que pueden verse afectados por este interrupción antes de cambiar la configuración de caché de disco.

Para obtener más información sobre cómo funciona BlobCache, consulte el interior entrada de blog de [Almacenamiento de Azure Premium](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) .

Es importante habilitar la caché en el conjunto de discos adecuado. Si debe habilitar almacenamiento en caché de disco en un disco premium o no dependerá de la trama de carga de trabajo que el disco a controlar. La tabla siguiente muestra la caché valores predeterminados para discos de sistema operativo y los datos.

| **Tipo de disco** | **Configuración de caché predeterminada** |
|---|---|
| Disco de sistema operativo | ReadWrite |
| Disco de datos | Ninguno |

Siguiente es la configuración de la caché de disco recomendado para discos de datos

| **Configuración de caché de disco** | **Recomendaciones sobre cuándo usar esta configuración** |
|---|---|
| Ninguno | Configurar la caché de host como ninguno discos sólo escritura y escritura visible. |
| Sólo lectura | Configurar caché host como sólo lectura para discos de solo lectura y de lectura y escritura. |
| ReadWrite | Configurar caché host como ReadWrite solo si su aplicación controla correctamente a escribir datos en caché persistentes discos cuando sea necesario. |

*Sólo lectura*  
Mediante la configuración del almacenamiento en caché de datos de almacenamiento de Premium discos de sólo lectura, puede lograr baja latencia de lectura y obtener muy alta IOPS de lectura y el rendimiento de la aplicación. Se trata de dos razones de vencimiento

1.  Lecturas realizadas de caché, que se encuentra en la memoria VM y SSD local, son mucho más rápidas que lee desde el disco de datos, que se encuentra en el almacenamiento de blobs de Windows Azure.  
2.  Almacenamiento de Premium no cuenta las lecturas atendidas desde la caché, hacia el IOPS del disco y el rendimiento. Por lo tanto, la aplicación es capaz de lograr mayores IOPS total y el rendimiento.

*ReadWrite*  
De forma predeterminada, los discos de sistema operativo tienen habilitada la caché de ReadWrite. Soporte técnico para ReadWrite almacenamiento en caché de datos discos también hemos agregado recientemente. Si está utilizando ReadWrite almacenamiento en caché, debe tener una forma adecuada para escribir los datos de caché a discos persistentes. Por ejemplo, SQL Server controla la escritura de datos en caché en los discos de almacenamiento persistente en su propia. Usar caché ReadWrite con una aplicación que no controla conservar los datos necesarios puede producir pérdida de datos, si se bloquea la máquina virtual.

Por ejemplo, puede aplicar estas instrucciones a SQL Server en almacenamiento Premium haciendo lo siguiente

1.  Configurar la caché de "Sólo lectura" en discos de almacenamiento de premium aloja archivos de datos.  
    una.  El fast lee de caché inferior el tiempo de consulta de SQL Server desde páginas de datos se recuperan mucho más rápido de la caché en comparación con directamente desde los datos de discos.  
    b.  Servir lecturas de caché, significa que hay un rendimiento adicional de discos de datos premium. SQL Server puede utilizar este rendimiento adicional hacia recuperar más páginas de datos y otras operaciones como copia de seguridad y restauración, lote cargas y regenera el índice.  
2.  Configurar "Ninguno" caché en discos de almacenamiento de premium los archivos de registro de hospedaje.  
    una.  Archivos de registro tienen principalmente operaciones de escritura visible. Por lo tanto, no aprovecharse de la caché de sólo lectura.

## <a name="disk-striping"></a>Bandas de disco  
Cuando una escala alta que VM está asociada con varios discos persistente almacenamiento de premium, los discos pueden ser seccionados juntos para agregar sus IOPs, el ancho de banda y la capacidad de almacenamiento.

En Windows, puede usar espacios de almacenamiento para bandas discos juntos. Debe configurar una columna para cada disco en un grupo. En caso contrario, el rendimiento general del volumen seccionado puede ser menor de lo esperado, debido a la distribución desigual de tráfico a través de los discos.

Importante: Usando UI administrador del servidor, puede establecer el número total de columnas hasta 8 para un volumen seccionado. Cuando vincula más de 8 discos, usar PowerShell para crear el volumen. Uso de PowerShell, puede establecer el número de columnas igual que el número de discos. Por ejemplo, si hay 16 discos en un conjunto único de bandas; especificar 16 columnas en el parámetro *NumberOfColumns* del cmdlet de PowerShell *VirtualDisk de nuevo* .


En Linux, use la utilidad MDADM bandas discos juntos. Para conocer los pasos detallados en discos bandas Linux consulte [Configurar RAID de Software en Linux](../virtual-machines/virtual-machines-linux-configure-raid.md).


*Tamaño de las bandas*  
Una configuración importante en bandas de disco es el tamaño de las bandas. El tamaño de las bandas o el tamaño del bloque es el más pequeño fragmento de datos que puede tratar la aplicación en un volumen seccionado. El tamaño de las bandas que configurar depende del tipo de aplicación y su modelo de solicitud. Si elige el tamaño de las bandas incorrecta, podría producir la alineación IO, lo que lleva a degradar el rendimiento de la aplicación.

Por ejemplo, si una solicitud de IO generada por la aplicación es mayor que el tamaño de las bandas de disco, el sistema de almacenamiento escribe en los límites de unidad bandas en más de un disco. Cuando es el momento para tener acceso a datos, tendrá que buscar en todas las unidades de más de un bandas para completar la solicitud. El efecto acumulado de dicho comportamiento puede producir degradación del rendimiento significativas. Por otro lado, si el tamaño de la solicitud de IO es menor que el tamaño de las bandas y es aleatoria por naturaleza, pueden agregar las solicitudes de IO hacia arriba en el mismo disco causando una botella y finalmente degradar el rendimiento de IO.


Según el tipo de carga de trabajo que se ejecuta la aplicación, elija un tamaño apropiado bandas. Para solicitudes de IO pequeñas aleatorias, utilice un tamaño de las bandas. Mientras que para grande IO secuencial solicitudes utilizan un tamaño de las bandas más grande. Descubra las recomendaciones de tamaño bandas de la aplicación que se ejecute en almacenamiento Premium. Para SQL Server, configure el tamaño de las bandas de 64KB para cargas de trabajo OLTP y 256KB para cargas de trabajo de almacenamiento de datos. Consulte [los procedimientos recomendados para SQL Server en máquinas virtuales de Azure rendimiento](../virtual-machines/virtual-machines-windows-sql-performance.md#disks-and-performance-considerations) para obtener más información.


>**Nota:**  
>Puede bandas juntos un máximo de 32 premium almacenamiento discos de una serie de DS VM y 64 premium almacenamiento de una serie de GS VM.

## <a name="multi-threading"></a>Multiproceso  
Azure ha diseñado la plataforma de almacenamiento de Premium ser masivos en paralelo. Por lo tanto, una aplicación multiproceso logra rendimiento mucho mayor que una aplicación de subproceso único. Una aplicación multiproceso divide sus tareas entre varios subprocesos y aumenta la eficiencia de su ejecución mediante el uso de la máquina virtual y recursos de disco al máximo.

Por ejemplo, si la aplicación se está ejecutando en un núcleo VM con dos subprocesos, la CPU puede cambiar entre los dos subprocesos para lograr la eficiencia. Mientras se espera un subproceso en un disco IO para completar, puede cambiar la CPU el otro subproceso. De este modo, pueden realizar dos subprocesos más que sería un único subproceso. Si la máquina virtual tiene más de un núcleo, disminuye aún más el tiempo de ejecución ya que cada núcleo puede ejecutar tareas en paralelo.

Es podrán que no pueda cambiar la forma en una aplicación estándar implementa solo multithreading o multiproceso. Por ejemplo, SQL Server es capaz de controlar multinúcleo y CPU. Sin embargo, SQL Server decide en qué condiciones ofrecerá uno o más subprocesos para procesar una consulta. Puede ejecutar consultas y generar índices mediante multiproceso. Una consulta que implica la combinación de tablas de gran tamaño y ordenar los datos antes de volver al usuario, es probable que SQL Server utiliza varios subprocesos. Sin embargo, un usuario no puede controlar si SQL Server ejecuta una consulta con un único subproceso o varios subprocesos.

Existen opciones de configuración que puede modificar para influir en esta multiproceso o en paralelo de procesamiento de una aplicación. Por ejemplo, en el caso de SQL Server es la configuración de grado de paralelismo máxima. Procesamiento de esta configuración denominada MAXDOP, le permite configurar el número máximo de procesadores que puede usar SQL Server cuando paralelo. Puede configurar MAXDOP para operaciones de índice o consultas individuales. Esto es útil cuando desee equilibrar los recursos del sistema para una aplicación crítica de rendimiento.

Por ejemplo, supongamos que su aplicación con SQL Server ejecuta una consulta grande y una operación de índice al mismo tiempo. Supongamos que desea que la operación de índice sea más eficaz con respecto a la consulta grande. En este caso, puede establecer valor MAXDOP de la operación de índice sea mayor que el valor MAXDOP para la consulta. De este modo, SQL Server tenga un mayor número de procesadores que puede aprovechar para la operación de índice en comparación con el número de procesadores que puede se dedicará a la consulta grande. Recuerde que no se controlan el número de subprocesos de SQL Server se usan para cada operación. Puede controlar el número máximo de procesadores está dedicado para multiproceso.

Más información sobre los [Grados de paralelismo](https://technet.microsoft.com/library/ms188611.aspx) en SQL Server. Descubra la configuración que influyen multiproceso en la aplicación y sus configuraciones para optimizar el rendimiento.

## <a name="queue-depth"></a>Profundidad de cola  
La profundidad de cola o la longitud de cola o el tamaño de la cola es el número de solicitudes de IO pendientes en el sistema. El valor de profundidad de cola determina cuántas operaciones de IO su aplicación puede alinear, que se procesamiento los discos de almacenamiento. Afecta a todos los indicadores de rendimiento de tres aplicación explicamos en este como artículo, IOPS, el rendimiento y la latencia.

Cola de profundidad y multiproceso están estrechamente relacionados. El valor de profundidad de cola indica cuánto multiproceso puede conseguirse por la aplicación. Si la profundidad de cola es grande, aplicación puede ejecutar más operaciones simultáneamente, es decir, más multiproceso. Si la profundidad de cola es pequeña, aunque la aplicación es multiproceso, no tendrá suficiente solicitudes alineados para ejecución simultánea.

Normalmente, desactivar la repisa aplicaciones no le permite cambiar la profundidad de la cola, porque si establecer de forma incorrecta que hará más daño que beneficio. Aplicaciones establecerán el valor correcto de profundidad de cola para obtener un rendimiento óptimo. Sin embargo, es importante comprender este concepto de modo que puede solucionar problemas de rendimiento con la aplicación. También puede observar los efectos de profundidad de cola ejecutando herramientas de referencia en el sistema.

Algunas aplicaciones proporcionan configuración para influir en la cola de profundidad. Por ejemplo, la configuración de MAXDOP (grado máximo de paralelismo) en SQL Server se explica en la sección anterior. MAXDOP es una manera de influir en profundidad de la cola y multiproceso, aunque no cambia el valor de profundidad de cola de SQL Server directamente.

*Profundidad de cola de alto*  
Una profundidad de cola de alto alinee más operaciones en el disco. El disco sabe la siguiente solicitud en la cola antes de tiempo. Por lo tanto, el disco puede programar operaciones adelantado y procesan en una secuencia óptima. Puesto que la aplicación está enviando solicitudes más en el disco, el disco puede procesar más IOs paralelo. Por último, la aplicación podrá lograr IOPS superior. Puesto que procesa más solicitudes de la aplicación, también aumenta el rendimiento total de la aplicación.

Normalmente, una aplicación puede lograr un rendimiento máximo con 8-16 + pendientes IOs por disco adjunto. Si una profundidad de cola es uno, aplicación no presión suficiente IOs al sistema y procesará menor cantidad de en un período determinado. En otras palabras, menos rendimiento.

Por ejemplo, en SQL Server, el valor de MAXDOP de una consulta para "4" informa a SQL Server que puede utilizar un máximo de cuatro núcleos para ejecutar la consulta. SQL Server determinará qué es mejor valor de profundidad de cola y el número de núcleos de ejecución de la consulta.

*Profundidad de cola óptimo*  
Valor de profundidad de cola muy alta también tiene inconvenientes. Si el valor de profundidad de cola es demasiado alto, la aplicación intentará unidad IOPS muy alta. A menos que la aplicación tiene discos persistentes con IOPS aprovisiona suficiente, esto puede afectar negativo latencia de aplicación. Después de la fórmula, muestran la relación entre IOPS, latencia y profundidad de cola.  
    ![](media/storage-premium-storage-performance/image6.png)

No debe configurar profundidad de cola a cualquier valor alto, pero a un valor óptimo, que puede ofrecer suficiente IOPS para la aplicación sin que ello afecte latencia. Por ejemplo, si la latencia de aplicación debe ser 1 milisegundos, la cola de profundidad para conseguir 5.000 IOPS es, QD = 5000 x 0.001 = 5.

*Profundidad de cola para el volumen con bandas*  
Para un volumen seccionado, mantener una profundidad de cola suficientemente alto que cada disco tiene una profundidad de cola de recursos asignadas individualmente. Por ejemplo, considere una aplicación que inserta una profundidad de cola de 2 y hay 4 discos en bandas. Las dos solicitudes de IO irán a dos discos y restante dos discos se inactivo. Por consiguiente, debe configurar la profundidad de cola tal que todos los discos pueden ser ocupados. Fórmula siguiente muestra cómo determinar la profundidad de cola de volúmenes con bandas.  
    ![](media/storage-premium-storage-performance/image7.png)

## <a name="throttling"></a>Límite  
Disposiciones de Azure almacenamiento Premium número especifican de IOPS y rendimiento dependiendo de los tamaños de la máquina virtual y los tamaños de disco que elija. Cada vez que la aplicación intenta unidad IOPS o rendimiento por encima de estos límites de la máquina virtual o el disco puede controlar, almacenamiento Premium se limitar él. Esto manifiestos en forma de disminución del rendimiento de la aplicación. Esto puede significar latencia mayor, disminuir el rendimiento o reducir IOPS. Si no limitar el almacenamiento de Premium, la aplicación puede fallar completamente por sobrepasan ¿cuáles son capaces de conseguir sus recursos. Por lo tanto, para evitar problemas de rendimiento debido a la limitación, siempre aprovisionar recursos suficientes para su aplicación. Tenga en cuenta que descrito en los tamaños VM y las secciones de tamaños de disco anteriores. Pruebas es la mejor manera para averiguar qué recursos necesitará la aplicación de host.

## <a name="benchmarking"></a>Banco de pruebas  
Pruebas son el proceso de simulación de diferentes cargas de trabajo en la aplicación y medir el rendimiento de la aplicación para cada carga de trabajo. Siga los pasos descritos en una sección anterior, ha recopilado los requisitos de rendimiento de la aplicación. Si ejecuta herramientas de referencia en máquinas virtuales de la aplicación de hospedaje, puede determinar los niveles de rendimiento que puede obtener la aplicación con almacenamiento Premium. En esta sección se proporcionan ejemplos de pruebas de una VM DS14 estándar aprovisionado con discos de almacenamiento de Azure Premium.

Herramientas de referencia comunes Iometer y FIO, hemos utilizado para Windows y Linux respectivamente. Estas herramientas generan varios subprocesos simular una producción como cargas de trabajo y medir el rendimiento del sistema. Con las herramientas puede configurar parámetros como profundidad cola y tamaño del bloque, que normalmente no se puede cambiar de una aplicación. Esto le da una mayor flexibilidad a conducir el rendimiento máximo en una escala de alta VM aprovisionado con discos premium para distintos tipos de cargas de trabajo de aplicación. Para obtener más información sobre cada herramienta de pruebas comparativas visitan [Iometer](http://www.iometer.org/) y [FIO](http://freecode.com/projects/fio).

Para seguir los ejemplos siguientes, cree una VM DS14 estándar y adjuntar 11 discos de almacenamiento de Premium VM. De 11 discos, configure 10 discos con host de almacenamiento en caché como "None" y bandas de en un volumen denominado NoCacheWrites. Configurar host caché como "Sólo lectura" en el disco restante y crear un volumen denominado CacheReads con este disco. Con esta configuración, podrá ver el rendimiento de lectura y escritura máximo de una VM DS14 estándar. Para conocer los pasos detallados sobre la creación de una máquina virtual de DS14 con discos premium, vaya a [crear y usar cuenta de almacenamiento Premium para un disco de datos de la máquina virtual](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

*Preparando la caché*  
El disco con almacenamiento en caché de host de sólo lectura podrán dar IOPS mayor que el límite de disco. Para obtener este máximo rendimiento de lectura de la caché de host, primero debe caliente la memoria caché de disco. Así se garantiza que el IOs lectura determinarán qué herramienta de referencia en CacheReads volumen realmente llega la caché y no el disco directamente. El resultado de visitas de caché en IOPS adicionales de la memoria caché solo habilitado disco.

>**Importante:**  
>Debe caliente la caché antes de ejecutar pruebas, cada vez que se reinicia la máquina virtual.

#### <a name="iometer"></a>Iometer   
[Descargue la herramienta Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) en la máquina virtual.

*Archivo de prueba*  
Iometer usa un archivo de prueba que se almacena en el volumen en el que se ejecutará la prueba de referencia. Unidades lee y escribe en este archivo de prueba para medir el disco IOPS y rendimiento. Iometer crea este archivo de prueba si no ha proporcionado ninguno. Crear un archivo de prueba de 200GB denominado iobw.tst en los volúmenes CacheReads y NoCacheWrites.

*Especificaciones de Access*  
Las especificaciones solicitar tamaño IO, % de lectura y escritura, % aleatorio o secuencial se configuran mediante la ficha "Especificaciones de Access" Iometer. Crear una especificación de acceso para cada uno de los escenarios que se describen a continuación. Crear las especificaciones de access y "Guardar" con un nombre como – RandomWrites\_8K, RandomReads\_8K. Seleccione la especificación correspondiente cuando se ejecuta el escenario de prueba.

Un ejemplo de especificaciones de access para el escenario de escribir IOPS máximo se muestra a continuación,  
    ![](media/storage-premium-storage-performance/image8.png)

*Especificaciones de prueba IOPS máximo*  
Para demostrar IOPs máximas, use menor tamaño de la solicitud. Usar el tamaño de la solicitud de 8K y crear especificaciones para lecturas y escribe aleatorio.

| Especificación de acceso | Tamaño de la solicitud | Aleatorio % | % De lectura |
|----------------------|--------------|----------|--------|
| RandomWrites\_8K     | 8K           | 100      | 0      |
| RandomReads\_8K      | 8K           | 100      | 100    |

*Especificaciones de la prueba de rendimiento máximo*  
Para mostrar el rendimiento máximo, utilice el mayor tamaño de la solicitud. Usar el tamaño de la solicitud de 64 KB y crear especificaciones para lecturas y escribe aleatorio.

| Especificación de acceso | Tamaño de la solicitud | Aleatorio % | % De lectura |
|----------------------|--------------|----------|--------|
| RandomWrites\_64K    | 64K          | 100      | 0      |
| RandomReads\_64K     | 64K          | 100      | 100    |

*Ejecutar la prueba Iometer*  
Realice los pasos siguientes para caliente caché

1.  Crear dos especificaciones de access con los valores que se muestra a continuación,

  	| Nombre              | Tamaño de la solicitud | Aleatorio % | % De lectura |
  	|-------------------|--------------|----------|--------|
  	| RandomWrites\_1MB | 1MB          | 100      | 0      |
  	| RandomReads\_1MB  | 1MB          | 100      | 100    |

2.  Ejecute la prueba de Iometer de inicialización de disco de la caché con los siguientes parámetros. Usar tres subprocesos para el volumen de destino y una profundidad de cola de 128. Establecer la duración "Tiempo de ejecución" de la prueba a 2hrs en la pestaña "Probar configuración".

  	| Escenario              | Volumen de destino | Nombre              | Duración |
  	|-----------------------|---------------|-------------------|----------|
  	| Inicializar disco de caché | CacheReads    | RandomWrites\_1MB | 2hrs     |

3.  Ejecute la prueba Iometer para Preparando disco de la caché con los siguientes parámetros. Usar tres subprocesos para el volumen de destino y una profundidad de cola de 128. Establecer la duración "Tiempo de ejecución" de la prueba a 2hrs en la pestaña "Probar configuración".

  	| Escenario           | Volumen de destino | Nombre             | Duración |
  	|--------------------|---------------|------------------|----------|
  	| Preparación del disco de caché | CacheReads    | RandomReads\_1MB | 2hrs     |

Después de la caché de disco esté caliente, continúe con los escenarios de prueba que se muestran a continuación. Para ejecutar la prueba Iometer, use al menos tres subprocesos para **cada** volumen de destino. Para cada subproceso de trabajo, seleccione el volumen de destino, establecer profundidad de la cola y seleccione una de las especificaciones de prueba guardado, tal como se muestra en la tabla siguiente, para ejecutar el escenario de prueba correspondiente. La tabla también muestra resultados esperados IOPS y rendimiento al ejecutar estas pruebas. Para todos los casos, se utilizan un tamaño de IO pequeño de 8KB y una profundidad de cola de alto de 128.

| Escenario de prueba      | Volumen de destino | Nombre              | Resultado       |
|--------------------|---------------|-------------------|--------------|
| Max. IOPS de lectura     | CacheReads    | RandomWrites\_8K  | 50.000 IOP  |
| Max. IOPS de escritura    | NoCacheWrites | RandomReads\_8K   | 64.000 IOPS  |
| Max. IOPS combinada | CacheReads    | RandomWrites\_8K  | 100000 IOPS |
|                    | NoCacheWrites | RandomReads\_8K   |              |
| Max. Leer MB/seg   | CacheReads    | RandomWrites\_64K | 524 MB/seg.   |
| Max. Escritura MB/seg  | NoCacheWrites | RandomReads\_64K  | 524 MB/seg.   |
| MB/seg combinada    | CacheReads    | RandomWrites\_64K | 1000 MB/seg.  |
|                    | NoCacheWrites | RandomReads\_64K  |              |

A continuación se muestran capturas de pantalla de la Iometer resultados de pruebas de escenarios IOPS y rendimiento combinados.

*IOPS máximas combinado lectura y escritura*  
![](media/storage-premium-storage-performance/image9.png)

*Rendimiento máximo combinado lectura y escritura*  
![](media/storage-premium-storage-performance/image10.png)

### <a name="fio"></a>FIO  
FIO es una herramienta popular a almacenamiento de referencia en las máquinas virtuales de Linux. Tiene la flexibilidad para seleccionar distintos tamaños de IO, secuenciales o lecturas aleatorias y escribe. Que genera subprocesos o procesos para realizar las operaciones de i/OS especificadas. Puede especificar el tipo de operaciones de i/OS que cada subproceso de trabajo debe realizar al usar archivos de trabajo. Que se creó un archivo de trabajo por escenario que se muestra en los ejemplos siguientes. Puede cambiar las especificaciones en estos archivos de trabajo para realizar pruebas comparativas diferentes cargas de trabajo almacenamiento Premium. En los ejemplos, estamos usando una VM estándar de 14 DS ejecutando **Ubuntu**. Use la misma configuración que se describe en el principio de la [sección Benchmarking](#Benchmarking) y caliente la caché antes de ejecutar las pruebas de referencia.

Antes de comenzar, [Descargue FIO](https://github.com/axboe/fio) e instálelo en su máquina virtual.

Ejecute el comando siguiente para Ubuntu,

        apt-get install fio

Usaremos cuatro subprocesos de trabajo para realizar operaciones de escritura y cuatro subprocesos de trabajo para las operaciones de lectura en los discos. Se llevando a los trabajadores de escritura tráfico en el volumen "nocache", que tiene 10 discos con caché establecido en "None". Se llevando a los trabajadores de lectura tráfico en el volumen "readcache", que tiene un 1 disco con conjunto de caché a "Sólo lectura".

*Máximo IOPS de escritura*  
Crear el archivo de trabajo con las siguientes especificaciones para obtener el máximo IOPS de escritura. El nombre "fiowrite.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Tenga en cuenta los aspectos fundamentales de seguimiento que estén en sintonía con las instrucciones de diseño descritas en las secciones anteriores. Estas especificaciones son fundamentales para conducir IOPS máximas,  
-   Profundidad de cola de alto de 256.  
-   Tamaño de un bloque pequeño de 8KB.  
-   Escribe varios subprocesos efectuar aleatorio.

Ejecute el comando siguiente para empezar la prueba FIO de 30 segundos  

    sudo fio --runtime 30 fiowrite.ini

Mientras se ejecuta la prueba, podrá ver el número de escritura IOPS la máquina virtual y se realiza discos Premium. Como se muestra en el ejemplo siguiente, la máquina virtual de DS14 ofrece su máxima límite IOPS de 50.000 IOPS de escritura.  
    ![](media/storage-premium-storage-performance/image11.png)

*Lectura máxima IOPS*  
Crear el archivo de trabajo con las siguientes especificaciones para obtener el máximo IOPS de lectura. El nombre "fioread.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Tenga en cuenta los aspectos fundamentales de seguimiento que estén en sintonía con las instrucciones de diseño descritas en las secciones anteriores. Estas especificaciones son fundamentales para conducir IOPS máximas,

-   Profundidad de cola de alto de 256.  
-   Tamaño de un bloque pequeño de 8KB.  
-   Escribe varios subprocesos efectuar aleatorio.

Ejecute el comando siguiente para empezar la prueba FIO de 30 segundos

    sudo fio --runtime 30 fioread.ini

Mientras se ejecuta la prueba, podrá ver IOPS leer el número de la máquina virtual y se realiza discos Premium. Como se muestra en el ejemplo siguiente, la máquina virtual de DS14 ofrece más de 64.000 IOPS de lectura. Esta es una combinación del disco y el rendimiento de la caché.  
    ![](media/storage-premium-storage-performance/image12.png)

*IOPS máxima de lectura y escritura*  
Crear el archivo de trabajo con las siguientes especificaciones para obtener el máximo combinación de lectura y escritura IOPS. El nombre "fioreadwrite.ini".

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Tenga en cuenta los aspectos fundamentales de seguimiento que estén en sintonía con las instrucciones de diseño descritas en las secciones anteriores. Estas especificaciones son fundamentales para conducir IOPS máximas,

-   Profundidad de cola de alto de 128.  
-   Un tamaño pequeño de bloque de 4KB.  
-   Varios subprocesos efectuar aleatorio lee y escribe.

Ejecute el comando siguiente para empezar la prueba FIO de 30 segundos

    sudo fio --runtime 30 fioreadwrite.ini

Mientras se ejecuta la prueba, podrá ver el número de lectura combinada y IOPS de escritura de la máquina virtual y se realiza discos Premium. Como se muestra en el ejemplo siguiente, la máquina virtual de DS14 ofrece más de 100000 lectura combinada y escribir IOPS. Esta es una combinación del disco y el rendimiento de la caché.  
    ![](media/storage-premium-storage-performance/image13.png)

*Máximo combina rendimiento*  
Para obtener el máximo combinado de lectura y escritura rendimiento, utilice un tamaño de bloque más grande y de profundidad de cola grande con varios subprocesos realización de lectura y escritura. Puede usar un tamaño de bloque de 64KB y de profundidad de cola de 128.

## <a name="next-steps"></a>Pasos siguientes  

Más información sobre el almacenamiento de Azure Premium:

- [Almacenamiento de Premium: El almacenamiento de alto rendimiento para cargas de trabajo de Azure Máquina Virtual](storage-premium-storage.md)  

Para usuarios de SQL Server, puede consultar en las mejores prácticas de rendimiento para SQL Server:

- [Prácticas recomendadas de rendimiento para SQL Server en máquinas virtuales de Windows Azure](../virtual-machines/virtual-machines-windows-sql-performance.md)
- [Almacenamiento de Azure Premium proporciona un rendimiento más alto para SQL Server en máquina virtual de Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx) 
