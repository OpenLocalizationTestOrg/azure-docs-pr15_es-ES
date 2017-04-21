<properties
    pageTitle="Modelado de varias empresas en búsqueda de Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Conozca los patrones de diseño comunes para las aplicaciones SaaS multiempresa con el uso de la búsqueda de Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/26/2016"
    ms.author="ashmaka"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Diseñar modelos para las aplicaciones SaaS multiempresa y búsqueda de Azure

Una aplicación multiempresa es uno que proporciona los mismos servicios y las capacidades a cualquier número de inquilinos que no puede ver ni compartir los datos de cualquier otro inquilino. Este documento describe estrategias de aislamiento de inquilinos para multiempresa aplicaciones creadas con la búsqueda de Azure.

## <a name="azure-search-concepts"></a>Conceptos de la búsqueda de Azure
Como una solución de búsqueda como servicio Azure búsqueda permite a los desarrolladores agregar experiencias enriquecidas de búsqueda a aplicaciones sin administrar cualquier infraestructura o convertirse en un experto en búsqueda. Datos cargados en el servicio y, a continuación, se almacena en la nube. Usa solicitudes sencillas a la API de búsqueda de Azure, los datos pueden, a continuación, modificar y realizar la búsqueda. Información general sobre el servicio se puede encontrar en [este artículo](http://aka.ms/whatisazsearch). Antes de tratar los patrones de diseño, es importante comprender algunos conceptos de búsqueda de Azure.

### <a name="search-services-indexes-fields-and-documents"></a>Servicios de búsqueda, índices, campos y documentos
Cuando se utiliza la búsqueda de Azure, uno que se ha suscrito a un _servicio de búsqueda_. Como datos cargados en búsqueda de Azure, se almacena en un _índice_ en el servicio de búsqueda. Puede haber un número de índices dentro de un servicio. Para usar los conceptos habituales de bases de datos, el servicio de búsqueda puede asemejarse a una base de datos mientras los índices dentro de un servicio pueden asemejarse a tablas desde una base de datos.

Cada índice dentro de un servicio de búsqueda tiene su propio esquema, que está definida por un número de _campos_de personalizables. Se agregan datos a un índice de búsqueda de Azure en el formulario de los distintos _documentos_. Cada documento debe cargarse en un índice en particular y debe ajustarse el esquema de índice. Al buscar datos con búsqueda de Azure, se emiten las consultas de búsqueda de texto en un índice en particular.  Para comparar estos conceptos a las de una base de datos, campos pueden asemejarse a columnas de una tabla y documentos pueden asemejarse a filas.

### <a name="scalability"></a>Escalabilidad
Puede ajustar el tamaño de cualquier servicio de búsqueda de Azure en el [nivel de precios](https://azure.microsoft.com/pricing/details/search/) estándar en dos dimensiones: almacenamiento y disponibilidad.
* Pueden agregarse _particiones_ para aumentar el almacenamiento de un servicio de búsqueda.
* _Las réplicas_ se pueden agregar a un servicio para aumentar el rendimiento de las solicitudes que puede controlar un servicio de búsqueda.

Agregar y quitar las particiones y réplicas en permitirá la capacidad del servicio de búsqueda que crece con la cantidad de datos y el tráfico de los requisitos de la aplicación. En el orden de un servicio de búsqueda lograr una lectura [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), requiere dos réplicas. En el orden de un servicio lograr un [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)de lectura y escritura, requiere tres réplicas.


### <a name="service-and-index-limits-in-azure-search"></a>Límites de servicio e índice de búsqueda de Azure
Hay algunos diferentes [niveles de precios](https://azure.microsoft.com/pricing/details/search/) en búsqueda de Azure, cada uno de los niveles tiene diferentes [cuotas y límites](search-limits-quotas-capacity.md). Algunos de estos límites en el nivel de servicio, algunos están en el nivel de índice, y algunos están en el nivel de partición.


|                                  | Básico     | Standard1   | Standard2   | Standard3   | Standard3 HD  |
|----------------------------------|-----------|-------------|-------------|-------------|---------------|
| Máximas réplicas por servicio     | 3         | 12          | 12          | 12          | 12            |
| Máximas particiones por cada servicio   | 1         | 12          | 12          | 12          | 1             |
| Unidades de búsqueda máxima (réplicas * particiones) por cada servicio | 3         | 36          | 36          | 36          | 36 (max 3 particiones)            |
| Máximo de documentos por cada servicio    | 1 millón | millones de 180 | 720 millones | 1,4 mil millones | 600 millones   |
| Almacenamiento máximo por cada servicio      | 2 GB      | 300 GB      | 1.2 TB      | 2,4 TB      | 600 GB        |
| Máximo de documentos por partición  | 1 millón | 15 millones  | 60 millones  | 120 millones | 200 millones   |
| Almacenamiento máximo por partición    | 2 GB      | 25 GB       | 100 GB      | 200 GB      | 200 GB        |
| Índices máximos por cada servicio      | 5         | 50          | 200         | 200         | 3000 (máximo 1000 índices o partición)          |


#### <a name="s3-high-density"></a>S3 Alta densidad '
En el nivel de precios de la búsqueda de Azure S3, hay una opción para el modo de alta densidad (HD) diseñado específicamente para escenarios multiempresa. En muchos casos, es necesario admitir una gran cantidad de inquilinos más pequeños en un único servicio para lograr los beneficios de y simplificar la rentabilidad.

S3 HD permite que los índices pequeños muchos empaquetar en la administración de un servicio de búsqueda única por cotización la capacidad de escalar índices con particiones para poder alojar más índices en un único servicio.

Concretamente, podría tener un servicio de S3 entre 1 y 200 índices que juntos podrían alojar documentos hasta 1,4 mil millones. Un HD S3 por otro lado permita índices individuales a solo vaya hasta 1 millón de documentos, pero puede controlar hasta 1000 índices por partición (hasta 3000 por servicio) con un recuento total del documento de 200 millones por partición (hasta 600 millones por cada servicio).



## <a name="considerations-for-multitenant-applications"></a>Consideraciones para las aplicaciones multiempresa
Aplicaciones multiempresa eficaz deben distribuir recursos entre los inquilinos conservando algún nivel de privacidad entre los inquilinos distintos. Hay algunas consideraciones al diseñar la arquitectura de este tipo de aplicación:

* _Aislamiento de inquilinos:_ Los desarrolladores de aplicaciones deben tomar medidas adecuadas para garantizar que ninguna inquilinos no autorizado o no deseados acceso a los datos de otros inquilinos. Más allá de la perspectiva de privacidad de los datos, estrategias de aislamiento de inquilinos requieren administración eficaz de los recursos compartidos y protección de ruido vecinos.
* _Costo del recurso en la nube:_ Como ocurre con cualquier otra aplicación, las soluciones de software deben permanecer costo competitivo como un componente de una aplicación multiempresa.
* _Facilidad de operaciones:_ Al desarrollar una arquitectura multiempresa, el impacto de las operaciones y la complejidad de la aplicación es un factor importante. Búsqueda de Azure tiene un [SLA 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* _Espacio global:_ Aplicaciones multiempresa necesite eficaz servir a inquilinos distribuidos en todo el mundo.
* _Escalabilidad:_ Los desarrolladores de aplicaciones deben tener en cuenta cómo conciliar entre mantener suficientemente bajo nivel de complejidad de la aplicación y diseñar la aplicación para cambiar la escala con el número de inquilinos y el tamaño de los datos y la carga de trabajo de inquilinos.

Búsqueda de Azure ofrece unos límites que pueden usarse para aislar los datos y la carga de trabajo de inquilinos.

## <a name="modeling-multitenancy-with-azure-search"></a>Varias empresas de modelado con búsqueda de Azure
En el caso de un escenario multiempresa, el desarrollador de la aplicación consume uno o más servicios de búsqueda y dividir los inquilinos entre servicios, índices o ambos. Búsqueda de Azure tiene unos patrones comunes al modelar un escenario multiempresa:

1. _Índice por inquilino:_ Cada inquilino tiene su propio índice dentro de un servicio de búsqueda que se comparte con otros inquilinos.
1. _Servicio por inquilino:_ Cada inquilino tiene su propio servicio de búsqueda de Azure dedicado, que ofrece más alto nivel de separación de datos y la carga de trabajo.
1. _Mezcla de ambas:_ Mayores, más activo inquilinos se asignan servicios dedicados mientras inquilinos más pequeños se asignan índices individuales dentro de servicios compartidos.

## <a name="1-index-per-tenant"></a>1. indizar por inquilino
![Una representación del modelo de índice por inquilino](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

En un modelo de índice por inquilino, varios inquilinos ocupan un único servicio de búsqueda de Azure donde cada inquilino tiene su propio índice.

Los inquilinos conseguir el aislamiento de datos porque todas las solicitudes de búsqueda y se emiten operaciones de documento en un nivel de índice de búsqueda de Azure. En el nivel de aplicación, hay el conocimiento necesario para dirigir el tráfico de los inquilinos distintos a los índices adecuados mientras se administran también recursos en el nivel de servicio a través de todos los inquilinos.

Atributo de clave del modelo de índice por cada inquilino es la capacidad de suscribir la capacidad de un servicio de búsqueda entre los inquilinos de la aplicación en el desarrollador de la aplicación. Si los inquilinos tienen una distribución desigual de carga de trabajo, la combinación óptima de inquilinos puede distribuirse entre los índices de un servicio de búsqueda para adaptarse a un número de inquilinos muy activos, intensivo mientras simultáneamente servir una larga cola de inquilinos menos activos. El equilibrio es la incapacidad del modelo a enfrentarse a situaciones donde cada inquilino simultáneamente es muy activo.

El modelo de índice por inquilino proporciona la base de un modelo de costo variable, donde todo el servicio de búsqueda de Azure se compró por adelantado y, a continuación, posteriormente se rellena con los inquilinos. Esto permite capacidad no utilizada que debe asignarse para versiones de prueba y las cuentas gratuitas.

Para las aplicaciones con presencia internacional, el modelo de índice por inquilino no puede ser más eficaz. Si los inquilinos de la aplicación se distribuye en todo el mundo, un servicio independiente puede ser necesario para cada región que puede duplicar los costos a través de cada uno de ellos.

Búsqueda de Azure permite a la escala de los índices individuales y el número total de índices crecer. Si un precio adecuado se elige nivel particiones y réplicas pueden agregarse al servicio de búsqueda completa cuando un índice individual dentro del servicio se hace demasiado grande en términos de almacenamiento o tráfico.

Si el número total de índices se hace demasiado grande para un solo servicio, tiene otro servicio estén configurados para acomodar a los inquilinos nuevos. Si tienen índices se mueven entre servicios de búsqueda cuando se agregan nuevos servicios, los datos del índice tienen un índice manualmente copiar a otra como búsqueda de Azure no permite un índice que desea mover.


## <a name="2-service-per-tenant"></a>2. servicio por inquilino
![Una representación del modelo de servicio por inquilino](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

En una arquitectura de servicio por inquilino, cada inquilino tiene su propio servicio de búsqueda.

En este modelo, la aplicación consigue el máximo nivel de aislamiento para sus inquilinos. Ha dedicado cada servicio de almacenamiento y el rendimiento para controlar la solicitud de búsqueda, así como claves de API independientes.

Para las aplicaciones, donde cada inquilino tiene un tamaño grande o la carga de trabajo pequeños cambios de inquilino a inquilinos, el modelo de servicio por inquilino es una opción eficaz como recursos no se comparten entre las cargas de trabajo de distintos de los inquilinos.

Un servicio por modelo inquilino también ofrece la ventaja de un modelo de costos fijos, predecible. No hay ninguna inversión inicial en un servicio de búsqueda completa hasta que haya un inquilino para rellenarlo, pero el costo por cada inquilino es mayor que un modelo de índice por inquilino.

El modelo de servicio por cada inquilino es una opción eficaz para las aplicaciones con un espacio global. Con distribuido geográfico inquilinos, es fácil que el servicio de cada del inquilino en su región.

Los desafíos en este modelo de ajuste de escala se producen cuando los inquilinos individuales superan su servicio. Búsqueda de Azure no admite actualmente actualizar el nivel de precio de un servicio de búsqueda, por lo que todos los datos tendría que copiar manualmente a un nuevo servicio.

## <a name="3-mixing-both-models"></a>3. ambos modelos mezcla de
Otro patrón de modelado varias empresas es una combinación de estrategias de índice por inquilino y servicio por inquilino.

Mediante la mezcla de los dos patrones, los inquilinos mayor de la aplicación pueden ocupar servicios dedicados mientras la cola larga de inquilinos menos activos, más pequeños puede ocupar índices en un servicio compartido. En este modelo garantiza que los inquilinos mayor constantemente alto rendimiento del servicio al ayudar a proteger a los inquilinos más pequeños de cualquier ruidos vecinos.

Sin embargo, esta estrategia basa previsión de predecir qué inquilinos necesitarán un servicio dedicado frente a un índice en un servicio compartido. Aumenta la complejidad de la aplicación con la necesidad de administrar ambas de estas modelos multitenancy.

## <a name="achieving-even-finer-granularity"></a>Nivel de detalle implementado lograr
Los patrones de diseño anteriores modelar escenarios multiempresa en búsqueda de Azure, suponen un ámbito uniforme donde cada inquilino es una instancia completa de una aplicación. Sin embargo, aplicaciones a veces pueden controlar muchos ámbitos más pequeños.

Si los modelos de servicio por inquilino e índice por inquilino no son lo suficientemente pequeños ámbitos, es posible modelar un índice para lograr un mayor incluso grado de detalle.

Para tener un índice único tienen un comportamiento diferente para los extremos de cliente diferente, puede agregarse un campo a un índice que designa un valor determinado de cada cliente posible. Cada vez que llama a un cliente de búsqueda de Azure para consultar o modificar un índice, el código de la aplicación cliente especifica el valor apropiado para ese campo mediante la capacidad de los [filtros](https://msdn.microsoft.com/library/azure/dn798921.aspx) de búsqueda de Azure en tiempo de consulta.

Este método puede usarse para lograr la funcionalidad de cuentas de usuario independientes, niveles de permisos separados y separar incluso por completo de aplicaciones.

## <a name="next-steps"></a>Pasos siguientes
Búsqueda de Azure es una opción atractiva para muchas aplicaciones, [Obtenga más información sobre las capacidades del servicio sólidos](http://aka.ms/whatisazsearch). Al evaluar los distintos modelos de diseño para aplicaciones multiempresa, considere la posibilidad de los [distintos niveles de precios](https://azure.microsoft.com/pricing/details/search/) y los respectivos [límites de servicio](search-limits-quotas-capacity.md) para adaptar mejor búsqueda de Azure para ajustar las cargas de trabajo de la aplicación y arquitecturas de todos los tamaños.

Dudas sobre la búsqueda de Azure y escenarios multiempresa pueden dirigirá a azuresearch_contact@microsoft.com.
