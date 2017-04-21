<properties
   pageTitle="¿Qué es el almacén de datos de SQL Azure? | Microsoft Azure"
   description="Clase empresarial distribuye capaz de procesar petabyte volúmenes de datos no relacionales y de la base de datos. Es el almacén de datos de nube primera de la industria con aumentar, reducir y haga una pausa en segundos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/27/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# <a name="what-is-azure-sql-data-warehouse"></a>¿Qué es el almacén de datos de SQL Azure?

Almacén de datos de SQL Azure es una basada en nube escalado base de datos capaz de procesar grandes volúmenes de datos relacionales y no relacionales. Basado en nuestra arquitectura de procesamiento enormemente paralelo (MPP), almacenamiento de datos de SQL puede controlar la carga de trabajo de la empresa.

Almacén de datos SQL:

- Combina la base de datos relacional de SQL Server con las capacidades de escalado de nube de Azure. Puede aumentar, disminuir, pausar o reanudar cálculo en segundos. Ahorrar costos por escalado de CPU cuando lo necesite y reducir uso durante las horas no punta.
- Aprovecha la plataforma de Windows Azure. Es fácil de implementar, mantiene sin problemas y errores totalmente tolerancia a errores debido a copias de seguridad automáticas.
- Complementa el ecosistema de SQL Server. Puede desarrollar con herramientas y familiar Transact-SQL de SQL Server (T-SQL).

En este artículo se describe las características clave de almacenamiento de datos de SQL.

## <a name="massively-parallel-processing-architecture"></a>Arquitectura de procesamiento masivo y en paralelo

Almacén de datos de SQL es que un procesamiento enormemente paralelo (MPP) distribuido el sistema de base de datos. Dividiendo los datos y capacidad de procesamiento entre varios nodos, almacén de datos SQL pueden ofrecer gran escalabilidad - más allá de cualquier sistema único.  En segundo plano, almacén de datos SQL reparte los datos entre muchos compartir nada almacenamiento y unidades de procesamiento. Los datos se almacenado en localmente redundantes Premium y vinculados para calcular los nodos de ejecución de la consulta. Con esta arquitectura, almacén de datos SQL adopta un enfoque "dividir y vencer" para ejecutar las cargas y las consultas complejas. Solicitudes de se recibidos por el nodo de Control, optimizadas y, a continuación, pasa a los nodos de cálculo para hacer su trabajo en paralelo.

Combinando arquitectura MPP y las capacidades de almacenamiento de Azure, almacenamiento de datos de SQL hacer lo siguiente:

- Aumentar o reducir almacenamiento independiente de cálculo.
- Aumentar o reducir el cálculo sin mover los datos.
- Pausar calcular capacidad y mantener datos intacta.
- Reanudar calcular capacidad en un instante.

El siguiente diagrama muestra la arquitectura con más detalle.

![Arquitectura de almacén de datos SQL][1]


**Nodo control:** El nodo de Control administra y optimiza las consultas. Es el front-end que interactúa con todas las aplicaciones y conexiones. En el almacén de datos de SQL, el nodo Control funciona con la base de datos SQL y conectarse a él ve y siente la misma. En la superficie, el nodo Control coordenadas todos los movimientos de datos y el cálculo necesarios para ejecutar consultas en paralelo de los datos distribuidos. Cuando envíe una consulta SQL T al almacén de datos de SQL, el nodo Control transforma en diferentes consultas que se ejecutan en cada nodo de cálculo en paralelo.

**Nodos de cálculo:** Los nodos de cálculo servir la potencia detrás del almacén de datos de SQL. Son las bases de datos de SQL que almacenar los datos y procesar la consulta. Cuando se agregan datos, el almacén de datos SQL distribuye las filas de los nodos de cálculo. Los nodos de cálculo son los trabajadores que se ejecutan las consultas en paralelo en los datos. Después del procesamiento, que pasan los resultados en el nodo de Control. Para finalizar la consulta, el nodo Control agrega los resultados y devuelve el resultado final.

**Almacenamiento:** Los datos se almacenan en el almacenamiento de blobs de Windows Azure. Cuando los nodos de cálculo interactúan con los datos, tener que escribir y leer directamente desde el almacenamiento de blobs y. Dado que el almacenamiento de Azure se expande transparente y ampliamente, almacén de datos SQL puede hacer lo mismo. Dado que el proceso y almacenamiento son independientes, almacén de datos SQL puede escalar automáticamente almacenamiento por separado de ajuste de escala de cálculo y viceversa. Almacenamiento de blobs de Windows Azure también está totalmente tolerancia y simplifica el proceso de copia de seguridad y restauración.

**Servicio de movimiento de datos:** Servicio de movimiento de datos (DMS) mueve datos entre los nodos. DMS le da acceso de los nodos de cálculo para que necesitan para combinaciones y agregaciones de datos. DMS no es un servicio de Azure. Es un servicio de Windows que se ejecute al lado de la base de datos SQL en todos los nodos. Dado que DMS se ejecuta en segundo plano, no interactuar con él directamente. Sin embargo, cuando examine los planes de consulta, observará incluyen algunas operaciones DMS desde el movimiento de datos es necesario ejecutar cada consulta en paralelo.


## <a name="optimized-for-data-warehouse-workloads"></a>Optimizado para las cargas de trabajo del almacén de datos

El enfoque MPP se mejora mediante un número de optimizaciones de rendimiento específicos, incluida la de almacenamiento de datos:

- Un optimizador distribuido y conjunto de estadísticas complejas en todos los datos. Con información sobre la distribución y tamaño de los datos, el servicio es capaz de optimizar consultas al evaluar el costo de las operaciones de consulta distribuida específicos.

- Algoritmos avanzados y técnicas integrada en el proceso de movimiento de datos para mover datos entre los recursos informáticos según sea necesario para realizar la consulta de forma eficaz. Estas operaciones de movimiento de datos se crean y, a continuación, todas las optimizaciones al servicio de movimiento de datos se realizarán de forma automática.

- Barras agrupadas **columnstore** índices de forma predeterminada. Mediante el uso de almacenamiento basada en la columna, almacén de datos SQL obtiene Media 5 x compresión las ganancias sobre almacenamiento tradicional orientada por filas y hasta 10 veces o más rendimiento de la consulta. Análisis de consultas que necesita para explorar una gran cantidad de trabajo de filas excelente en columnstore índices.


## <a name="predictable-and-scalable-performance"></a>Rendimiento predecible y escalabilidad

Almacén de datos SQL separa el almacenamiento y el cálculo, que permite que cada escalar de forma independiente. Almacén de datos SQL puede escalar de manera rápida y sencilla para agregar recursos de cálculo adicionales en un instante. Como complemento para esto es el uso de almacenamiento de blobs de Windows Azure. BLOB proporciona almacenamiento estable, replicada pero la infraestructura para expansión sin esfuerzo a bajo coste. Usar esta combinación de almacenamiento de la escala de la nube y cálculo Azure, almacén de datos de SQL le permite pagar de almacenamiento y rendimiento de consulta cuando lo necesite. Cambiar la cantidad de cálculo es tan fácil como mover un control deslizante en el portal de Azure a la izquierda o derecha, o también se puede programar mediante T-SQL y PowerShell.

Junto con la capacidad de controlar la cantidad de cálculo independientemente de almacenamiento por completo, almacén de datos SQL permite totalmente pausar el almacén de datos, lo que significa que no paga cálculo cuando ya no lo necesita. Mientras mantiene el almacenamiento en su lugar, calcular todos los está publicado en el grupo principal de Azure, lo que le ahorra dinero. Cuando sea necesario, simplemente reanudar el cálculo y que los datos y calcular disponibles para su carga de trabajo.

## <a name="data-warehouse-units"></a>Unidades de almacén de datos

Asignación de recursos para el almacén de datos de SQL se mide en unidades de almacén de datos (DWUs). DWUs son una medida de los recursos subyacentes como CPU, memoria, IOPS, que están asignados a su almacén de datos de SQL. Aumentar el número de DWUs aumenta recursos y el rendimiento. Más concretamente, DWUs asegurarse de que:

- Es posible escalar el almacén de datos con facilidad, sin preocuparse por el software o hardware subyacente.

- Puede predecir mejora del rendimiento de un nivel DWU antes de cambiar el tamaño de su almacén de datos.

- El hardware y software de la instancia subyacente pueden cambiar o mover sin que ello afecte el rendimiento de la carga de trabajo.

- Microsoft puede realizar ajustes en la arquitectura subyacente del servicio sin afectar al rendimiento de la carga de trabajo.

- Microsoft rápidamente puede mejorar el rendimiento en el almacén de datos de SQL, de manera que sea scalable y efectos uniformemente el sistema.

Unidades de almacén de datos proporcione una medida de tres métricas precisos que se guarda una correlación estrecha con el rendimiento de la carga de trabajo de almacenamiento de datos. El objetivo es que las siguientes métricas clave de carga de trabajo se escalar de manera lineal con la DWUs que ha elegido para el almacén de datos.

**Análisis y agregación:** Esta métrica de carga de trabajo le lleva a una consulta que analiza un gran número de filas y, a continuación, realiza una agregación compleja de almacenamiento de datos estándares. Se trata de una operación intensiva i/OS y la CPU.

**Carga:** Esta métrica mide la capacidad de recopilación de datos en el servicio. Carga se completó con PolyBase cargar un representante de conjunto de datos de almacenamiento de blobs de Windows Azure. Esta métrica está diseñada para red de carga y aspectos de CPU del servicio.

**Crear tabla como Select (CTAS):** CTAS mide la capacidad para copiar una tabla. Implica la lectura de datos de almacenamiento, distribución a través de los nodos del dispositivo y escribir en el almacenamiento de nuevo. Es una operación intensiva CPU, IO y red.

## <a name="pause-and-scale-on-demand"></a>Pausar y escala a petición

Cuando necesite resultados más rápidos, aumentar su DWUs y de pago para un mayor rendimiento. Cuando menos necesite calcular potencia, disminuir su DWUs y pagar solo por lo que necesita. Piense en cambiar su DWUs en estos escenarios:

- Cuando no es necesario ejecutar consultas, tal vez en las noches o los fines de semana, poner en modo inactivo las consultas. A continuación, haga una pausa los recursos de cálculo para evitar pagando por DWUs cuando no necesite.

- Cuando el sistema tiene poca demanda, considere la posibilidad de reducción de DWU a un tamaño pequeño. Aún puede tener acceso a los datos, pero con un ahorro significativo.

- Al realizar una operación de carga o la transformación de gran cantidad de datos, desea escalar para que los datos están disponibles más rápidamente.

Para conocer ¿qué es el valor DWU ideal, pruebe escalado hacia arriba y hacia abajo y ejecutar consultas unas después de cargar los datos. Puesto que la escala es rápido, puede intentar un número de distintos niveles de rendimiento en una hora o menos.  Tenga en cuenta que el almacén de datos de SQL está diseñado para procesar grandes cantidades de datos y ver sus verdaderas capacidades de escala, especialmente en las escalas mayores que ofrecemos desea utilizar un amplio conjunto de datos que se aproxima o supera 1 TB.


## <a name="built-on-sql-server"></a>Integrada en SQL Server

Almacén de datos de SQL se basa en el motor de base de datos relacionales de SQL Server e incluye muchas de las características que espera de un almacén de datos de la empresa. Si ya conoce SQL T, es fácil transferir información al almacén de datos de SQL. Si avanzadas o acaba de empezar, los ejemplos a través de la documentación le ayudará a empezar. En general, puede considerar la forma en que los elementos del lenguaje de almacén de datos de SQL nos hemos construye del siguiente modo:

- Almacén de datos de SQL utiliza sintaxis SQL T para muchas operaciones. También es compatible con un amplio conjunto de construcciones tradicionales de SQL, por ejemplo, procedimientos almacenados, funciones definidas por el usuario, partición de tablas, índices e intercalación.

- Almacén de datos SQL también contiene un número de nuevas características de SQL Server, incluidos: agrupadas **columnstore** índices, integración PolyBase y datos de auditoría (completa con la evaluación de la amenaza).

- Algunos elementos de idioma T SQL que son menos común para las cargas de trabajo de almacenamiento de datos, o posterior a SQL Server, no pueden ser disponibles actualmente. Para obtener más información, consulte la [documentación de migración][].

Con la característica y Transact-SQL similitudes entre SQL Server, almacenamiento de datos de SQL, base de datos SQL y el análisis de plataforma de sistema, puede desarrollar una solución que se adapte a sus necesidades de datos. Puede decidir dónde desea conservar los datos, en función de rendimiento, seguridad y requisitos de escala y, a continuación, transferir los datos según sea necesario entre los distintos sistemas.

## <a name="data-protection"></a>Protección de datos

Almacén de datos SQL almacena todos los datos en el almacenamiento de Azure Premium localmente redundante. Varias copias sincrónicos de los datos se mantienen en el centro de datos local para garantizar la protección de datos transparente en caso de errores localizados. Además, almacén de datos SQL copia automáticamente las bases de datos (no en pausa) activas a intervalos regulares mediante instantáneas de almacenamiento de Azure. Para obtener más información sobre cómo hacer copia de seguridad y restauración funciona, consulte la [información general de copia de seguridad y restauración][].

## <a name="integrated-with-microsoft-tools"></a>Integrada con las herramientas de Microsoft

Almacén de datos de SQL integra muchas de las herramientas que estén familiarizados con los usuarios de SQL Server. Se incluyen:

**Herramientas tradicional SQL Server:** Almacén de datos SQL está totalmente integrado con SQL Server Analysis Services, Integration Services y Reporting Services.

**Herramientas basadas en la nube:** Almacén de datos SQL puede usarse junto con un número de las nuevas herramientas de Azure, incluidos el generador de datos, el análisis de flujo, el aprendizaje y Power BI. Para obtener una lista más completa, vea [Introducción a las herramientas integradas][].

**Herramientas de terceros:** Una gran cantidad de proveedores de terceros ha certificado integración de las herramientas de almacenamiento de datos de SQL. Para obtener una lista completa, vea [socios de soluciones de almacenamiento de datos de SQL][].

## <a name="hybrid-data-sources-scenarios"></a>Escenarios de orígenes de datos híbrido

Usar el almacén de datos SQL con PolyBase proporciona a los usuarios sin precedentes capacidad para mover datos entre su ecosistema, desbloquear la capacidad de configurar escenarios avanzadas híbrido con no relacionales y orígenes de datos local.

Polybase le permite aprovechar los datos de orígenes diferentes usando comandos familiares de T SQL. Polybase le permite consultar datos relacionales de mantenidos en almacenamiento de blobs de Windows Azure como si fuera una tabla normal. Use Polybase para consultar los datos relacionales de o para importar datos relacionales de almacén de datos de SQL.

- PolyBase usa tablas externas para tener acceso a datos relacionales de. Las definiciones de tabla se almacenan en el almacén de datos de SQL y puede tener acceso a ellas mediante SQL y herramientas como el se obtiene acceso a datos relacionales normales.

- Polybase es independiente en su integración. Expone las mismas características y funciones para todas las fuentes que admite. Los datos leer por Polybase pueden estar en una variedad de formatos, incluidos archivos delimitados o ORC.

- PolyBase puede utilizarse para tener acceso al almacenamiento de blobs de Windows que también usa como almacenamiento de un clúster de HDInsight. Esto le proporciona acceso a los mismos datos no relacionales y herramientas.

## <a name="sla"></a>SLA

Almacén de datos SQL ofrece un contrato nivel de nivel de servicio (SLA) de producto como parte de Microsoft Online Services SLA. Para obtener más información, visite [SLA para el almacén de datos de SQL][]. Para obtener información acerca de los demás productos SLA puede visitar la Azure [Service Level Agreements] de página o descargarlos en la página de [Licencias por volumen][] . 

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya conoce un poco de almacén de datos de SQL, obtenga información sobre cómo rápidamente [crear un almacén de datos de SQL][] y [cargar los datos de ejemplo][]. Si es nuevo en Azure, es posible el [Glosario de Azure][] útiles que se pueden encontrar terminología nuevo. O bien, eche un vistazo a algunos de estos otros recursos de almacén de datos de SQL.  

- [Historias de éxito de clientes]
- [Blogs]
- [Solicitudes de características]
- [Vídeos]
- [Blogs del equipo de recomendación de cliente]
- [Crear incidencia de soporte técnico]
- [Foro de MSDN]
- [Foro de desbordamiento de pila]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Crear incidencia de soporte técnico]: ./sql-data-warehouse-get-started-create-support-ticket.md
[cargar datos de ejemplo]: ./sql-data-warehouse-load-sample-databases.md
[crear un almacén de datos de SQL]: ./sql-data-warehouse-get-started-provision.md
[Documentación de migración]: ./sql-data-warehouse-overview-migrate.md
[Socios de soluciones de almacenamiento de datos de SQL]: ./sql-data-warehouse-partner-business-intelligence.md
[Introducción a las herramientas integradas]: ./sql-data-warehouse-overview-integrate.md
[Información general de copia de seguridad y restauración]: ./sql-data-warehouse-restore-database-overview.md
[Glosario de Azure]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Historias de éxito de clientes]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs del equipo de recomendación de cliente]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitudes de características]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Foro de MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Foro de desbordamiento de pila]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA para el almacén de datos SQL]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Licencias por volumen]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Contratos de nivel de servicio]: https://azure.microsoft.com/en-us/support/legal/sla/
