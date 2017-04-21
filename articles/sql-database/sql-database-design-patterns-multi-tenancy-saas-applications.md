<properties
   pageTitle="Diseñar modelos para las aplicaciones SaaS multiempresa y base de datos de SQL Azure | Microsoft Azure"
   description="En este artículo se describe los requisitos y patrones de arquitectura de datos comunes de base de datos multiempresa aplicaciones que se ejecutan en un entorno de nube deben tener en cuenta y las compensaciones diversos asociadas con estos modelos. También se explica cómo base de datos de SQL Azure, con sus grupos elásticas y herramientas elásticos, ayudar a estos requisitos de forma sin compromisos de direcciones."
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-design"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-sql-database"></a>Diseñar modelos para las aplicaciones SaaS multiempresa y base de datos de SQL Azure

En este artículo, puede obtener información sobre los requisitos y datos comunes patrones de arquitectura de software multiempresa como un aplicaciones de base de datos de servicio (SaaS) que se ejecutan en un entorno de nube. También se explican los factores que debe tener en cuenta y las ventajas y desventajas de los patrones de diseño diferente. Grupos elásticos y elásticas herramientas de base de datos de SQL Azure pueden ayudarle a satisfacer sus necesidades específicas sin afectar a otros objetivos.

A veces, los desarrolladores tomar decisiones que funcionan con sus intereses a largo plazo al diseñar modelos de usuarios para los niveles de datos de aplicaciones multiempresa. Inicialmente, al menos, un desarrollador puede creen facilidad de desarrollo y menores costos de proveedor de servicio de nube como más importante que el inquilino aislamiento o la escalabilidad de una aplicación. Esta opción puede provocar problemas de satisfacción del cliente y un rumbo costosa más adelante.

Una aplicación multiempresa es una aplicación alojada en un entorno de nube y que proporciona el mismo conjunto de servicios a cientos o miles de inquilinos que no compartir o ver datos de los demás. Un ejemplo es una aplicación de SaaS que proporciona servicios a los inquilinos en un entorno hospedada en la nube.

## <a name="multitenant-applications"></a>Aplicaciones multiempresa

En las aplicaciones multiempresa, datos y carga de trabajo pueden estar fácilmente divididas. Puede dividir datos y carga de trabajo, por ejemplo, a lo largo de los límites del inquilino, porque la mayoría de las solicitudes se producen dentro de los límites de un inquilino. Propiedad es inherente en los datos y la carga de trabajo y, a continuación, elige los modelos de aplicación analizados en este artículo.

Los desarrolladores utilizar este tipo de aplicación en todo el espectro de aplicaciones basadas en la nube, incluidos:

- Aplicaciones de base de datos de socios son transición a la nube como aplicaciones SaaS
- Aplicaciones de SaaS creadas para la nube desde el principio
- Aplicaciones directas orientados al cliente
- Orientación de empleado las aplicaciones empresariales

SaaS aplicaciones que han sido diseñadas para la nube y aquellos con raíces como aplicaciones de base de datos del partner normalmente son multiempresa. Estas aplicaciones SaaS ofrecen una aplicación de software especializado como servicio a sus inquilinos. Los inquilinos pueden tener acceso al servicio de la aplicación y tener la propiedad completa de datos asociados almacenados como parte de la aplicación. Pero, para aprovechar las ventajas de SaaS inquilinos deben entregar algún control sobre sus propios datos. Confían en el proveedor de servicios de SaaS para mantener sus datos seguros y aislados de los datos de otros inquilinos. Ejemplos de este tipo de aplicación de SaaS multiempresa son MYOB, SnelStart y Salesforce.com. Cada una de estas aplicaciones puede dividirse a lo largo de los límites del inquilino y soporte técnico que trataremos en este artículo de modelos de diseño de la aplicación.

Las aplicaciones que proporcione un servicio directo a clientes o a los empleados de una organización (a menudo esto se conoce como usuarios, en lugar de inquilinos) son otra categoría en el espectro de aplicación multiempresa. Los clientes suscripción al servicio y no es propietario de los datos que el proveedor de servicios recopila y almacena. Proveedores de servicios tienen requisitos reducidos para mantener los datos de sus clientes aislados entre sí más allá de reglamentaciones gubernamentales privacidad. Ejemplos de este tipo de aplicación de multiempresa orientados al cliente son proveedores de contenido multimedia como Netflix, Spotify y Xbox LIVE. Otros ejemplos de aplicaciones fácilmente caso son orientados al cliente, aplicaciones para Internet o aplicaciones de Internet de cosas (IoT) en lo que puede de cada cliente o dispositivo servir como una partición. Límites de partición pueden separar los usuarios y dispositivos.

Partición de aplicaciones no todos fácilmente a lo largo de una sola propiedad como inquilino, cliente, usuario o dispositivo. Un recurso de empresa complejos planeación de la aplicación (ERP), por ejemplo, tiene productos, pedidos y clientes. Normalmente tiene un esquema complejo con miles de tablas muy interconectados.

Ninguna estrategia de partición solo puede aplicar a todas las tablas y trabajar en una carga de trabajo. En este artículo se centra en aplicaciones multiempresa que tienen cargas de trabajo y datos fácilmente caso.

## <a name="multitenant-application-design-trade-offs"></a>Aplicación multiempresa diseño compensaciones

El modelo de diseño que un desarrollador de aplicaciones multiempresa elige normalmente se basa en tener en cuenta los siguientes factores:

-   **Aislamiento del inquilino**. El programador debe asegurarse de que ninguna inquilino tiene acceso no deseado a los datos de otros inquilinos. El requisito de aislamiento extiende a otras propiedades, como la protección de ruido vecinos, para poder restaurar los datos del inquilino e implementar personalizaciones específicas del inquilino.
-   **Costo de recursos de la nube**. Una aplicación de SaaS debe estar costo competitivo. Un desarrollador de aplicaciones multiempresa puede optimizar para coste inferior en el uso de recursos de la nube, como almacenamiento y calcular los costos.
-   **Facilidad de DevOps**. Un desarrollador de aplicaciones multiempresa debe incorporar protección de aislamiento, mantener y supervisar el estado de su aplicación y el esquema de la base de datos y solucionar problemas del inquilino. Complejidad de desarrollo de aplicaciones y la operación se traduce directamente en mayor costo y desafíos con la satisfacción del inquilino.
-   **Escalabilidad**. La capacidad para agregar gradualmente más inquilinos y la capacidad de inquilinos que lo necesiten es fundamental una operación de SaaS correcta.

Cada uno de estos factores tiene ventajas y desventajas con respecto a otro. Nube de menor costo ofreciendo podrían no ofrecer la experiencia de desarrollo más adecuada. Es importante para un desarrollador realizar elecciones informadas sobre estas opciones y sus ventajas y desventajas durante el proceso de diseño de la aplicación.

Un modelo de desarrollo populares es empaquetar a varios inquilinos en una o varias bases de datos. Las ventajas de este enfoque son un coste inferior porque pagar para algunas bases de datos y la simplificar relativa de trabajar con un número limitado de bases de datos. Pero con el tiempo, un desarrollador de la aplicación multiempresa SaaS obtendrán que esta opción tiene desventajas importantes en aislamiento inquilino y escalabilidad. Si el inquilino aislamiento es importante, esfuerzo adicional es necesario para proteger los datos de inquilinos de almacenamiento compartido de acceso no autorizado o ruidos vecinos. Este esfuerzo adicional puede aumentar considerablemente los esfuerzos de desarrollo y los costos de mantenimiento de aislamiento. Del mismo modo, si es necesario agregar los inquilinos, este patrón de diseño normalmente requiere experiencia para redistribuir los datos del inquilino en bases de datos escalar correctamente el nivel de datos de una aplicación.  

Aislamiento del inquilino a menudo es un requisito fundamental en aplicaciones de multiempresa SaaS a empresas y organizaciones. Un desarrollador puede sentirse tentado por ventajas observadas en simplificar y costo sobre aislamiento inquilino y escalabilidad. Puede probar a este intercambio complejas y caras que el servicio crece y se convierten en requisitos de aislamiento del inquilino más importantes y administrada en el nivel de aplicación. Sin embargo, en aplicaciones multiempresa que proporcionan un servicio para consumidores directo a los clientes, aislamiento inquilino podría ser una prioridad inferior a la optimización del costo de recursos de la nube.

## <a name="multitenant-data-models"></a>Modelos de datos multiempresa

Prácticas de diseño comunes para colocar los datos del inquilino siguen tres modelos distintos, que se muestra en la figura 1.


  ![Modelos de datos de aplicación multiempresa](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)
 figura 1: prácticas de diseño comunes para modelos de datos multiempresa

-   **Base de datos por inquilino**. Cada inquilino tiene su propia base de datos. Todos los datos específicos del inquilino está limitado a la base de datos del inquilino y aislado de otros inquilinos y sus datos.
-   **Sharded para la base de datos compartida**. Varios inquilinos de compartan una de varias bases de datos. Un conjunto determinado de inquilinos se asigna a cada base de datos mediante una estrategia de división como hash, intervalo o lista particiones. Esta estrategia de distribución de datos a menudo se denomina sharding.
-   **Simple de base de datos compartida**. Una base de datos única, a veces es grande, contiene datos para todos los inquilinos, que se elimina la ambigüedad en una columna de Id. de inquilinos.

> [AZURE.NOTE] Un desarrollador de aplicaciones puede colocar a los inquilinos diferentes en los esquemas de base de datos diferente y, a continuación, use el nombre del esquema para eliminar la ambigüedad a los inquilinos diferentes. No se recomienda este método porque normalmente requiere el uso de SQL dinámico y no puede ser eficaz en el plan de almacenamiento en caché. En el resto de este artículo, se centrará en el método de tabla compartida para esta categoría de aplicación multiempresa.

## <a name="popular-multitenant-data-models"></a>Modelos de datos multiempresa populares

Es importante evaluar los diferentes tipos de modelos de datos multiempresa en cuanto a la aplicación diseño compensaciones que ya hemos identificado. Estos factores ayuda caracterizan los tres modelos de datos multiempresa más comunes que se ha descrito anteriormente y su uso de la base de datos tal como se muestra en la figura 2.

-   **Aislamiento**. El nivel de aislamiento entre inquilinos puede ser una medida de cuánto aislamiento inquilino logra un modelo de datos.
-   **Costo de recursos de la nube**. La cantidad de recursos compartidos entre inquilinos puede optimizar el costo de recursos de la nube. Puede definirse un recurso como el coste de proceso y almacenamiento.
-   **Costo de DevOps**. La facilidad de desarrollo de aplicaciones, implementación y administración reduce el costo total de la operación de SaaS.  

En la figura 2, el eje Y muestra el nivel de aislamiento de inquilinos. El eje X muestra el nivel de uso compartido de recursos. Gris, flecha diagonal en el medio indica la dirección de costos DevOps, a aumentar o disminuir.

![Patrones de diseño de la aplicación multiempresa populares](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png) figura 2: modelos de datos multiempresa populares

La fase inferior derecha en la figura 2 muestra un patrón de aplicación que usa un potencialmente grande sola base de datos compartida y la tabla compartido (o esquema independiente) enfoque. Es conveniente para los recursos compartidos porque todos los inquilinos utilizan los mismos recursos de base de datos (CPU, memoria, entrada y salida) en una base de datos. Pero aislamiento del inquilino está limitado. Puede que tenga que realizar pasos adicionales para proteger los inquilinos entre sí en el nivel de aplicación. Estos pasos adicionales pueden aumentar considerablemente el coste DevOps de desarrollo y administración de la aplicación. Escalabilidad está limitada por la escala del hardware que hospeda la base de datos.

La fase de abajo a la izquierda en la figura 2 ilustra varios inquilinos sharded múltiples bases de datos (normalmente, hardware diferente escalar unidades). Cada base de datos contiene un subconjunto de los inquilinos, que resuelve el problema de escalabilidad de otros modelos. Si se requiere para los inquilinos más más capacidad, puede colocar a los inquilinos en nuevas bases de datos asignados a las unidades de escala de hardware nuevo. Sin embargo, se reduce la cantidad de uso compartido de recursos. Solo los inquilinos habían realizada en las mismas unidades de escala compartir recursos. Este enfoque proporciona poca mejora para aislamiento de inquilinos porque muchos de los inquilinos aún están ubicados sin automáticamente está protegida de acciones de la otra. Complejidad de la aplicación se mantiene alta.

La fase de la esquina superior izquierda en la figura 2 es el enfoque terceros. Datos de cada inquilino coloca en su propia base de datos. Este enfoque tiene las propiedades de aislamiento de inquilinos buena, pero los límites de los recursos compartidos cuando cada base de datos tiene sus propio recursos dedicados. Este enfoque es útil si todos los inquilinos tienen cargas de trabajo predecibles. Si se convierten en cargas de trabajo del inquilino menos predecibles, no puede optimizar el proveedor de uso compartido de recursos. Inesperadas es común para las aplicaciones SaaS. El proveedor debe en exceso aprovisionar para satisfacer necesidades o recursos inferiores. Cualquiera de estas acciones da como resultado costos más altos o inferior satisfacción del inquilino. Se convierte en un alto grado de recursos compartidos por los inquilinos conveniente para poner la solución más rentables. También se aumenta el número de bases de datos aumenta DevOps costo para implementar y mantener la aplicación. A pesar de estos problemas, este método proporciona el mejor y más sencilla de aislamiento de inquilinos.

Estos factores también influyen en el modelo de diseño de que un cliente elige:

-   **La propiedad de datos del inquilino**. Una aplicación en la que los inquilinos conservan la propiedad de sus propios datos promueve el patrón de una base de datos por cada inquilino.
-   **Escala**. Una aplicación destinada a cientos de miles o millones de inquilinos promueve enfoques como sharding de uso compartido de la base de datos. Requisitos de aislamiento pueden suponer retos.
-   **Valor y el modelo de empresa**. Si una aplicación ingresos por inquilino si pequeña (menos de un dólar), los requisitos de aislamiento se vuelven menos críticos y una base de datos compartida tiene sentido. Si los ingresos por cada inquilino son unos dólares o más, un modelo de base de datos por cada inquilino es más factible. Puede ayudar a reducir los costos de desarrollo.

Dada la compensaciones diseño se muestra en la figura 2, un modelo de multiempresa ideal debe incorporar las propiedades de aislamiento de buena inquilino con óptimo de los recursos compartidos entre los inquilinos. En este modelo se adapta en la categoría que se describe en la fase superior derecha de la figura 2.

## <a name="multitenancy-support-in-azure-sql-database"></a>Compatibilidad con multitenancy en base de datos de SQL Azure

Base de datos de SQL Azure es compatible con todos los patrones de aplicación multiempresa descritos en la figura 2. Con grupos elásticos, también es compatible con un modelo de aplicación que combina buen recurso de uso compartido y las ventajas de aislamiento de la base de datos por inquilino enfoque (consulte la fase de la esquina superior derecha en la figura 3). Herramientas de base de datos elásticas y las capacidades de base de datos de SQL ayudar a reducir los costos para desarrollar y utilizar una aplicación que tiene muchas bases de datos (que se muestra en el área sombreada en la figura 3). Estas herramientas pueden ayudarle a crear y administrar aplicaciones que utilicen cualquiera de los modelos de bases de datos.

![Patrones de base de datos de SQL Azure](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png) figura 3: modelos de aplicación multiempresa en base de datos de SQL Azure

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>Modelo de base de datos por inquilino con agrupaciones elásticas y herramientas

Elásticos grupos en la base de datos SQL combinan aislamiento inquilino con recursos compartidos entre las bases de datos de inquilinos para mejorar el enfoque de la base de datos por inquilino. Base de datos de SQL es una solución de nivel de datos para los proveedores de SaaS que crean aplicaciones multiempresa. La carga de recursos compartidos entre inquilinos se desplaza desde el nivel de aplicación a la capa de servicio de base de datos. La complejidad de la administración y consultas a escala a través de bases de datos se ha simplificado con trabajos elásticos, consulta elástico, transacciones elásticas y la biblioteca de cliente de base de datos elástico.

| Requisitos de la aplicación | Funciones de base de datos SQL |
| ------------------------ | ------------------------- |
| Compartir recursos y aislamiento de inquilinos | [Grupos de elásticos](sql-database-elastic-pool.md): asignar un grupo de recursos de base de datos SQL y compartir los recursos entre diversas bases de datos. Además, bases de datos individuales pueden extraer tantos recursos del grupo según sea necesario para acomodar los picos de petición de capacidad debido a cambios en las cargas de trabajo del inquilino. El propio grupo elástico puede ampliarse hacia arriba o hacia abajo, según sea necesario. Agrupaciones elásticos también proporcionan facilidad de administración y supervisión y solución de problemas en el nivel de grupo. |
| Facilidad de DevOps en bases de datos | [Grupos de elásticos](sql-database-elastic-pool.md): como se indicó anteriormente.|
||[Consulta elástico](sql-database-elastic-query-horizontal-partitioning.md): consulta entre bases de datos para los informes o entre el inquilino de análisis.|
||[Trabajos elásticos](sql-database-elastic-jobs-overview.md): paquete e implementar confiable las operaciones de mantenimiento de base de datos o cambios de esquema de la base de datos en varias bases de datos.|
||[Transacciones elásticas](sql-database-elastic-transactions-overview.md): proceso cambia a varias bases de datos de una forma atómica y aislada. Transacciones elásticas son necesarias cuando las aplicaciones necesitan garantías de "todo o nada" diversas operaciones de base de datos. |
||[Biblioteca de cliente de base de datos elástico](sql-database-elastic-database-client-library.md): administrar las distribuciones de datos y asignar los inquilinos a bases de datos. |

## <a name="shared-models"></a>Modelos compartidos

Como se describió anteriormente, para la mayoría de los proveedores de SaaS, puede suponer un enfoque modelo compartido inquilino aislamiento problemas y complejidades con el desarrollo de aplicaciones y el mantenimiento. Sin embargo, para aplicaciones multiempresa que proporcionan un servicio directamente a los consumidores, requisitos de aislamiento de inquilino no pueden ser una prioridad alta como minimizar el costo. Podría podrán empaquetar inquilinos en una o más bases de datos a una alta densidad a reducir los costos. Modelos de bases de datos compartido con una base de datos o varias bases de datos sharded pueden ocasionar eficacia adicional de costo del recurso para compartir y general. Base de datos de SQL Azure proporciona aislamiento para mejorar la seguridad y administración a escala en el nivel de datos de generación de algunas características que ayudan a los clientes.

| Requisitos de la aplicación | Funciones de base de datos SQL |
| ------------------------ | ------------------------- |
| Características de aislamiento de seguridad | [Seguridad a nivel de fila](https://msdn.microsoft.com/library/dn765131.aspx) |
|| [Esquema de la base de datos](https://msdn.microsoft.com/library/dd207005.aspx) |
| Facilidad de DevOps en bases de datos | [Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) |
|| [Elásticos trabajos](sql-database-elastic-jobs-overview.md) |
|| [Transacciones elásticas](sql-database-elastic-transactions-overview.md) |
|| [Biblioteca de cliente de base de datos elástico](sql-database-elastic-database-client-library.md) |
|| [Base de datos elástico división y combinación](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>Resumen

Requisitos de aislamiento inquilino son importantes para la mayoría de las aplicaciones SaaS multiempresa. La mejor opción para proporcionar aislamiento se basa en gran medida hacia el enfoque de la base de datos por inquilino. Los otros dos enfoques requieren inversiones en capas de aplicación compleja que requieren personal de desarrollo capacitado para proporcionar aislamiento, lo que aumenta significativamente el costo y el riesgo. Si los requisitos de aislamiento no se contabilizan para temprano en el desarrollo de servicio, puede ser aún más costosa en los dos primeros modelos instalación de accesorios ellos. Los principales inconvenientes asociados con el modelo de base de datos por inquilino están relacionadas con los costos de recursos de nube aumentada debido a la reducción de uso compartido y mantener y administrar muchas bases de datos. Los desarrolladores de aplicaciones de SaaS a menudo tienen dificultades cuando hagan estas ventajas y desventajas.

Aunque compensaciones podrían ser principales obstáculos con proveedores de servicios de base de datos de nube mayoría, base de datos de SQL Azure elimina los obstáculos con su grupo elástica y capacidades elástica de la base de datos. Los desarrolladores de SaaS pueden combinar las características de aislamiento de un modelo de base de datos por inquilino y optimizar las mejoras de facilidad de uso de muchas bases de datos y compartir recursos mediante grupos elásticas y herramientas asociadas.

Proveedores de aplicación multiempresa que no tienen inquilino aislamiento requisitos y quién puede empaquetar los inquilinos en una base de datos a una alta densidad posible modelos resultado eficiencia adicional en recursos compartidos de datos compartidos y reducen los costes generales. Herramientas de elásticos de la base de datos de base de datos de SQL Azure, bibliotecas de sharding y características de seguridad ayudan a los proveedores de SaaS crear y administrar aplicaciones multiempresa.

## <a name="next-steps"></a>Pasos siguientes

[Introducción a las herramientas de base de datos elástico](sql-database-elastic-scale-get-started.md) con una aplicación de ejemplo que muestra la biblioteca de cliente.

Crear un [panel personalizado de grupo elástica para SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) con una aplicación de ejemplo que utiliza grupos elásticas para una solución rentable, scalable de base de datos.

Use las herramientas de base de datos de SQL Azure para [migrar bases de datos existentes escalar](sql-database-elastic-convert-to-use-elastic-tools.md).

Vea nuestro tutorial sobre cómo [crear un grupo de elástico](sql-database-elastic-pool-create-portal.md).  

Obtenga información sobre cómo [supervisar y administrar un grupo elástico](sql-database-elastic-pool-manage-portal.md).

## <a name="additional-resources"></a>Recursos adicionales

- [¿Qué es un grupo de elástico Azure?](sql-database-elastic-pool.md)
- [Escalar con la base de datos de SQL Azure](sql-database-elastic-scale-introduction.md)
- [Aplicaciones multiempresa con herramientas de base de datos elásticas y seguridad a nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md)
- [Autenticación en las aplicaciones multiempresa con Azure Active Directory y conectar OpenID](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplicación de encuestas Tailspin](../guidance/guidance-multitenant-identity-tailspin.md)
- [Inicio rápido de soluciones](sql-database-solution-quick-starts.md)

## <a name="questions-and-feature-requests"></a>Preguntas y solicitudes de características

Si tiene preguntas, busque en el [foro de la base de datos de SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Agregar una solicitud de característica en el [foro de comentarios de la base de datos de SQL](https://feedback.azure.com/forums/217321-sql-database/).
