<properties
    pageTitle="Solución de administración de capacidad de análisis de registro | Microsoft Azure"
    description="Puede usar la solución de planificación de la capacidad de análisis de registro para ayudarle a entender la capacidad de los servidores de Hyper-V administradas por el Administrador de máquina Virtual de System Center"
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="capacity-management-solution-in-log-analytics"></a>Solución de administración de capacidad de análisis de registro


Puede usar la solución de planificación de la capacidad de análisis de registro para ayudarle a entender la capacidad de los servidores de Hyper-V administradas por el Administrador de máquina Virtual de System Center. Esta solución requiere System Center Operations Manager y Administrador de máquina Virtual de System Center. Planificación de capacidad no está disponible si solo utiliza a agentes conectado directamente. Instale la solución para actualizar al agente de Operations Manager. La solución lee contadores de rendimiento en el servidor supervisado y envía los datos de uso del servicio de OMS en la nube para procesamiento. Lógica se aplica a los datos de uso y el servicio de nube registra los datos. Con el tiempo, se identifican los patrones de uso y capacidad está proyectada, en función de consumo actual.

Por ejemplo, puede identificar una proyección cuando núcleos de procesador adicional o memoria adicional necesario para un servidor individual. En este ejemplo, la proyección puede indicar que de 30 días el servidor necesita memoria adicional. Esto puede ayudarle a planear una actualización de la memoria durante la ventana de mantenimiento siguiente del servidor, lo que puede ocurrir una vez cada dos semanas.

>[AZURE.NOTE] La solución de administración de la capacidad no está disponible para agregarse a áreas de trabajo. Los clientes que tengan la solución de administración de capacidad instalada pueden seguir usando la solución.  

La capacidad de solución de planeación es se está actualizando tratar al siguiente cliente notificado desafíos:

- Requisitos para usar el Administrador de la máquina Virtual y Operations Manager
- No puede personalizar o filtrar basándose en grupos
- Agregación de datos por hora no frecuentes suficiente
- No hay información de nivel VM
- Confiabilidad de los datos

Beneficios de la solución de capacidad de nuevo:

- Compatible con la recopilación de datos granulares con mayor confiabilidad y precisión
- Soporte para Hyper-V sin necesidad de VMM
- Visualización de métricas en PowerBI
- Información sobre la utilización de nivel VM


## <a name="installing-and-configuring-the-solution"></a>Instalar y configurar la solución
Use la información siguiente para instalar y configurar la solución.

- Operations Manager se requiere para la solución de administración de la capacidad.
- Administrador de la máquina virtual se requiere para la solución de administración de la capacidad.
- Se requiere conectividad de Operations Manager con Virtual Machine Manager (VMM). Para obtener información adicional acerca de la conexión de los sistemas, consulte [cómo conectar VMM con Operations Manager](http://technet.microsoft.com/library/hh882396.aspx).
- Operations Manager debe estar conectado a análisis de registro.
- Agregar la solución de administración de la capacidad al área de trabajo OMS mediante el proceso descrito en [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md).  No es necesaria realizar ninguna configuración adicional.


## <a name="capacity-management-data-collection-details"></a>Capacidad detalles del conjunto de datos de administración

Administración de la capacidad recopila datos de rendimiento, metadatos y datos de estado con los agentes que ha habilitado.

La siguiente tabla muestra los métodos de conjunto de datos y otros detalles acerca de cómo se recopilan los datos de administración de la capacidad.

| plataforma | Agente directo | Agente SCOM | Almacenamiento de Azure | ¿SCOM obligatorio? | Datos de agente SCOM enviadas por grupo de administración | frecuencia de la colección |
|---|---|---|---|---|---|---|
|Windows|![No](./media/log-analytics-capacity/oms-bullet-red.png)|![Sí](./media/log-analytics-capacity/oms-bullet-green.png)|![No](./media/log-analytics-capacity/oms-bullet-red.png)|            ![Sí](./media/log-analytics-capacity/oms-bullet-green.png)|![Sí](./media/log-analytics-capacity/oms-bullet-green.png)| cada hora|

La siguiente tabla muestra ejemplos de los tipos de datos recopilados por la administración de la capacidad de:

|**Tipo de datos**|**Campos**|
|---|---|
|Metadatos|BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, dirección IP, ForestDNSName, NombreDeEquipoNetBIOS, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, dirección IP, NetBIOSDomainName., LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime|
|Rendimiento|ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded|
|Estado|StateChangeEventId, el identificador, NewHealthState, OldHealthState, contexto, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified|

## <a name="capacity-management-page"></a>Página de administración de capacidad


 Después de instala la solución de planificación de capacidad, puede ver la capacidad de los servidores supervisados mediante el icono de **Planificación de capacidad** en la página de **información general** de OMS.

![imagen del icono de planificación de capacidad](./media/log-analytics-capacity/oms-capacity01.png)

El icono abre el panel de **Administración de la capacidad** , donde puede ver un resumen de la capacidad del servidor. La página muestra los mosaicos siguientes que puede hacer clic en:

- *Recuento de máquina virtual*: muestra el número de días restantes de la capacidad de máquinas virtuales
- *Calcular*: muestra núcleos de procesador y la memoria disponible
- *Almacenamiento*: muestra el espacio de disco utilizado y calcular el promedio de latencia de disco
- *Buscar*: el Explorador de datos que puede usar para buscar datos en el sistema OMS

![imagen del panel de administración de la capacidad de](./media/log-analytics-capacity/oms-capacity02.png)


### <a name="to-view-a-capacity-page"></a>Para ver una página de capacidad

- En la página **Introducción** , haga clic en **Administración de la capacidad**y, a continuación, haga clic en **calcular** o **almacenamiento**.

## <a name="compute-page"></a>Calcular la página

Puede usar el panel **calcular** en Microsoft Azure OMS para ver información de capacidad sobre utilización, proyectado días de capacidad y la eficacia relacionados con la infraestructura. Use el área de **utilización** ver core y la memoria de la CPU en los host de máquina virtual. Puede usar la herramienta de proyección para estimar cuánto capacidad se espera que esté disponible para un intervalo de fechas determinado. Puede usar el área de **eficiencia** para ver la eficacia de su host de máquina virtual. Puede ver detalles sobre los elementos vinculados haciendo clic en ellos.

Puede generar un libro de Excel para las siguientes categorías:

- Hosts superiores con mayor utilización del núcleo
- Hosts superiores con una utilización de memoria máxima
- Hosts superiores con ineficaz máquinas virtuales de Windows
- Superiores hosts por uso
- Hosts inferior por uso

![imagen de la página de administración de capacidad calcular](./media/log-analytics-capacity/oms-capacity03.png)


En el panel **calcular** , se muestran las siguientes áreas:

**Utilización**: uso de memoria y núcleo de CPU de la vista en los host de máquina virtual.

- *Utilizar núcleos*: suma para todos los hosts (% de la CPU utilizado multiplicado por el número de núcleos físicos en host).
- *Núcleos gratuita*: Total núcleos físicos menos núcleos usados.
- *Porcentaje núcleos disponibles*: libre núcleos físicos divididos por el número total de núcleos físicos.
- *Virtual núcleos por VM*: Total núcleos virtuales en el sistema dividido por el número total de máquinas virtuales en el sistema.
- *Núcleos virtual físico relación núcleos*: relación de total núcleos físicos a núcleos físicos utilizados por máquinas virtuales en el sistema.
- *Número de virtual núcleos disponibles*: core Virtual relación núcleos físicos multiplicado por las muestras físicas disponibles.
- *Utilizar memoria*: suma de la memoria que se utiliza en todos los hosts.
- *Memoria*: memoria física menos utilizan memoria Total.
- *Porcentaje de memoria disponible*: liberar memoria física dividida por total de memoria física.
- *Memoria virtual por VM*: memoria virtual Total en el sistema dividido por el número total de máquinas virtuales en el sistema.
- *Memoria virtual a la tasa de memoria física*: memoria virtual Total en el sistema dividido por la memoria física total en el sistema.
- *Memoria virtual disponible*: memoria Virtual a la proporción de memoria física multiplicado por la memoria física disponible.

**Herramienta de proyección**

Mediante la herramienta de proyección, puede ver las tendencias históricas para el uso de recursos. Esto incluye las tendencias de uso de máquinas virtuales, memoria, principal y almacenamiento. La capacidad de proyección usa un algoritmo de proyección para ayudarle a saber cuándo se está quedando sin cada uno de los recursos. Esto le permite calcular la capacidad correcta planificación para que sepa debe comprar más capacidad (por ejemplo, memoria, núcleos o almacenamiento).

**Eficiencia**

- *Inactivo VM*: con menos de 10% de la memoria de CPU y 10% para el período de tiempo especificado.
- *Tanto VM*: con más de un 90% de la memoria de CPU y 90% para el período de tiempo especificado.
- *Host inactivo*: con menos de 10% de la memoria de CPU y 10% para el período de tiempo especificado.
- *Tanto Host*: con más de un 90% de la memoria de CPU y 90% para el período de tiempo especificado.

### <a name="to-work-with-items-on-the-compute-page"></a>Para trabajar con elementos en la página de cálculo

1. En el panel **calcular** , en el área de **utilización** , ver información de capacidad sobre la núcleos de CPU y el uso de la memoria.
2. Haga clic en un elemento para abrirlo en la página de **búsqueda** y ver información detallada sobre él.
3. En la herramienta de **proyección** , mueva el control deslizante de fecha para mostrar una proyección de la capacidad que se utilizará en la fecha que elija.
4. En el área de la **eficiencia** , ver información de la eficacia de capacidad sobre máquinas virtuales y hosts de máquina virtual.

## <a name="direct-attached-storage-page"></a>Página de almacenamiento de conexión directa

Puede usar el panel de **Almacenamiento con conexión directa** en OMS para ver información de capacidad sobre utilización del almacenamiento, el rendimiento del disco y días proyectados de capacidad de disco. Use el área de **uso** para ver el uso del espacio en disco en los host de máquina virtual. Puede usar el área de **Rendimiento del disco** para ver el rendimiento del disco y la latencia en los host de máquina virtual. También puede usar la herramienta de proyección para estimar cuánto capacidad se espera que esté disponible para un intervalo de fechas determinado. Puede ver detalles sobre los elementos vinculados haciendo clic en ellos.

Puede generar un libro de Excel de esta información de la capacidad de las siguientes categorías:

- Uso del espacio en disco superior host
- Latencia promedio superior host

En la página de **almacenamiento** , se muestran las siguientes áreas:

- *Utilización*: ver el uso del espacio en disco en los host de máquina virtual.
- *Espacio en disco total*: suma (espacio de disco lógico) para todos los hosts
- *Espacio en disco utilizado*: suma (espacio en disco lógico utilizado) para todos los hosts
- *Espacio en disco disponible*: espacio en disco menos espacio en disco utilizado
- *Porcentaje de disco usa*: utiliza dividido por espacio en disco total de espacio en disco
- *Porcentaje de disco disponible*: espacio en disco disponible dividido por espacio en disco total

![imagen de la página de almacenamiento capacidad administración con conexión directa](./media/log-analytics-capacity/oms-capacity04.png)

**Rendimiento del disco**

Mediante OMS, puede ver la tendencia de uso histórico de espacio en disco. La capacidad de proyección usa un algoritmo de uso futuros proyectos. Para el uso de espacio en particular, la capacidad de proyección permite project cuando tiene suficiente espacio en disco. Esto le ayudará a planear almacenamiento correcto y saber cuándo debe comprar más almacenamiento.

**Herramienta de proyección**

Mediante la herramienta de proyección, puede ver las tendencias históricas para la utilización del espacio de disco. La capacidad de proyección también le permite project cuando se está quedando sin espacio en disco. Esto le ayudará a planear la capacidad adecuada y saber cuándo debe comprar más capacidad de almacenamiento.

### <a name="to-work-with-items-on-the-direct-attached-storage-page"></a>Para trabajar con elementos en la página de almacenamiento con conexión directa

1. En el panel de **Almacenamiento con conexión directa** , en el área de **uso** , puede ver la información de uso de disco.
2. Haga clic en un elemento vinculado para abrirlo en la página de **búsqueda** y ver información detallada sobre él.
3. En el área de **Rendimiento de disco** , puede ver información de rendimiento y la latencia de disco.
4. En la **herramienta de proyección**, mueva el control deslizante de fecha para mostrar una proyección de la capacidad que se utilizará en la fecha que elija.


## <a name="next-steps"></a>Pasos siguientes

- Usar [búsquedas de registro de análisis de registro](log-analytics-log-searches.md) para ver los datos de administración de capacidad detallada.
