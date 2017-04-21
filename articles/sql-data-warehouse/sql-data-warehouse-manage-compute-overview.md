<properties
   pageTitle="Administrar la potencia de la informática en el almacén de datos de SQL Azure (información general) | Microsoft Azure"
   description="Escala de rendimiento capacidades en el almacén de datos de SQL Azure. Cambiar la escala ajustando DWUs o pausar y reanudar recursos de cálculo para ahorrar costos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/03/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Administrar la potencia de la informática en el almacén de datos de SQL Azure (información general)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

La arquitectura de almacén de datos SQL separa el almacenamiento y el cálculo, que les permita escalar de forma independiente. Como resultado, puede escalar el rendimiento y ahorrar costos pagando solo para el rendimiento cuando lo necesite. 

Esta información general describe las siguientes capacidades de escalado de rendimiento del almacén de datos de SQL y proporciona recomendaciones sobre cómo y cuándo usarlos. 

- Escala potencia ajustando el [almacén de datos de unidades (DWUs)][]
- Pausar o reanudar recursos de cálculo

<a name="scale-performance-bk"></a>

## <a name="scale-performance"></a>Ajustar el rendimiento

En el almacén de datos de SQL, puede escalar rápidamente rendimiento out o por crecientes o decrecientes recursos de cálculo de CPU, memoria y ancho de banda de i/OS. Para ajustar el rendimiento, todo lo que debe hacer es ajustar el número de [unidades (DWUs) del almacén de datos][] que asigna el almacén de datos de SQL a la base de datos. Almacén de datos SQL rápidamente realiza el cambio y controla todos los cambios subyacentes a hardware y software.

Lejos quedan los días dónde debe buscar referencias del tipo de procesadores, la cantidad de memoria o qué tipo de almacenamiento debe tener un gran rendimiento en el almacén de datos. Si coloca el almacén de datos en la nube, ya no tiene que solucionar los problemas de hardware de bajo nivel. En su lugar, almacén de datos SQL hace esta pregunta: ¿con qué rapidez desea analizar los datos? 

### <a name="how-do-i-scale-performance"></a>¿Cómo ajustar el rendimiento?

Para elástica aumentar o reducir la potencia de la informática, simplemente cambie la configuración de [unidades (DWUs) del almacén de datos][] de la base de datos. Rendimiento aumentará lineal a medida que agrega más DWU.  En los niveles superiores de DWU, debe agregar más de 100 DWUs para observar una mejora significativa en el rendimiento. Para ayudarle a seleccionar saltos significativos en DWUs, le ofrecemos los niveles DWU que dan los mejores resultados.
 
Para ajustar DWUs, puede utilizar cualquiera de estos métodos individuales.

- [Escala potencia con el portal de Azure][]
- [Escala potencia con PowerShell][]
- [Escala de energía de cálculo con la API de REST][]
- [Escala potencia con TSQL][]

### <a name="how-many-dwus-should-i-use"></a>¿Cuántos DWUs debo usar?
 
Rendimiento en el almacén de datos de SQL escala lineal, y el cambio de una cálculo escala otra (por ejemplo desde 100 DWUs a DWUs 2000) sucede en segundos. Esto le da la flexibilidad a experimentar con diferentes configuraciones de DWU hasta que determine el ajuste perfecto de su situación.

Para conocer ¿qué es el valor DWU ideal, pruebe escalado hacia arriba y hacia abajo y ejecutar consultas unas después de cargar los datos. Puesto que la escala es rápido, puede intentar un número de distintos niveles de rendimiento en una hora o menos. Tenga en cuenta que el almacén de datos de SQL está diseñado para procesar grandes cantidades de datos y ver sus verdaderas capacidades de escala, especialmente en las escalas mayores que ofrecemos desea utilizar un amplio conjunto de datos que se aproxima o supera 1 TB.

Recomendaciones para encontrar la mejor DWU para la carga de trabajo:

1. Para un almacén de datos en desarrollo, empiece por seleccionar un pequeño número de DWUs.  Un buen punto de partida es DW400 o DW200.
2. Supervisar el rendimiento de la aplicación, observa el número de DWUs seleccionado en comparación con el rendimiento que observa.
3. Determinar cuánto rendimiento más lento o más rápido para que alcancen el nivel de rendimiento óptimo para sus requisitos asumiendo escala lineal.
4. Aumente o reduzca el número de DWUs proporcionalmente cómo mucho más rápido o más despacio que desea llevar a cabo la carga de trabajo. El servicio responder rápidamente y ajustar los recursos de cálculo para satisfacer los requisitos de DWU de nuevo.
5. Continuar la realización de ajustes hasta llegar a un nivel de rendimiento óptimo para sus necesidades empresariales.

### <a name="when-should-i-scale-dwus"></a>¿Cuándo debo escala DWUs?

Cuando necesite resultados más rápidos, aumentar su DWUs y de pago para un mayor rendimiento.  Cuando menos necesite calcular potencia, disminuir su DWUs y pagar solo por lo que necesita. 

Recomendaciones sobre cuándo escalar DWUs:

1. Si su aplicación tiene una carga de trabajo fluctuación de escala DWU niveles hacia arriba o hacia abajo para acomodar picos y puntos bajos. Por ejemplo, si la carga de trabajo picos normalmente al final del mes, va a agregar más DWUs durante los días de máximo y luego escalar hacia abajo una vez que el periodo de recursos asignadas.
2. Antes de realizar una operación de carga o la transformación de gran cantidad de datos, escalar DWUs para que los datos están disponibles más rápidamente.

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Cálculo de pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar una base de datos, utilice cualquiera de estos métodos individuales.

- [Cálculo de pausa con el portal de Azure][]
- [Cálculo de pausa con PowerShell][]
- [Cálculo de pausa con las API de REST][]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Cálculo de currículo

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para reanudar una base de datos, utilice cualquiera de estos métodos individuales.

- [Cálculo de currículum vítae con el portal de Azure][]
- [Cálculo de currículum vítae con PowerShell][]
- [Cálculo de currículum vítae con las API de REST][]

## <a name="permissions"></a>Permisos

Ajuste de escala de la base de datos necesitarán los permisos que se describe en [Modificar base de datos][].  Pausar y reanudar necesitarán el permiso de [Colaborador DB de SQL][] , específicamente Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos siguientes para ayudarle a entender algunos conceptos clave de rendimiento adicionales:

- [Administración de carga de trabajo y simultaneidad][]
- [Introducción al diseño de tabla][]
- [Distribución de tablas][]
- [Indización de tabla][]
- [Partición de tablas][]
- [Estadísticas de tabla][]
- [Prácticas recomendadas][]

<!--Image reference-->

<!--Article references-->
[unidades de almacén de datos (DWUs)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[Escala potencia con el portal de Azure]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[Escala potencia con PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Escala de energía de cálculo con la API de REST]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Escala potencia con TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Cálculo de pausa con el portal de Azure]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Cálculo de pausa con PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Cálculo de pausa con las API de REST]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Cálculo de currículum vítae con el portal de Azure]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Cálculo de currículum vítae con PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Cálculo de currículum vítae con las API de REST]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Administración de carga de trabajo y simultaneidad]: ./sql-data-warehouse-develop-concurrency.md
[Introducción al diseño de tabla]: ./sql-data-warehouse-tables-overview.md
[Distribución de tablas]: ./sql-data-warehouse-tables-distribute.md
[Indización de tabla]: ./sql-data-warehouse-tables-index.md
[Partición de tablas]: ./sql-data-warehouse-tables-partition.md
[Estadísticas de tabla]: ./sql-data-warehouse-tables-statistics.md
[Prácticas recomendadas]: ./sql-data-warehouse-best-practices.md 
[development overview]: ./sql-data-warehouse-overview-develop.md

[Colaborador de DB de SQL]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[MODIFICAR BASE DE DATOS]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
