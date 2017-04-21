<properties
    pageTitle="Información general de referencia de base de datos de SQL Azure"
    description="Este tema describe la referencia de base de datos de SQL Azure usados en medir el rendimiento de la base de datos de SQL Azure."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="06/21/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-benchmark-overview"></a>Información general de referencia de base de datos de SQL Azure

## <a name="overview"></a>Información general
Base de datos de SQL de Microsoft Azure ofrece tres [niveles de servicio](sql-database-service-tiers.md) con varios niveles de rendimiento. Cada nivel de rendimiento proporciona un conjunto de aumento de recursos o 'power' diseñados para ofrecer un rendimiento cada vez superior.

Es importante poder cuantificar cómo traduce la potencia de aumenta de cada nivel de rendimiento en el rendimiento de la base de datos mayor. Para hacer que Microsoft ha desarrollado la referencia de base de datos de SQL Azure (ASDB). La comparativa ejercicios una combinación de las operaciones básicas en todas las cargas de trabajo OLTP. Evaluar el rendimiento obtenido bases de datos en cada nivel de rendimiento.

Los recursos y la potencia de cada nivel de rendimiento y los niveles de servicio se expresan en términos de [Unidades de transacción de base de datos (DTUs)](sql-database-technical-overview.md#understand-dtus). DTUs proporciona una manera de describir la capacidad relativa de un nivel de rendimiento basada en una medida combinada de CPU, memoria y leer y escribir tasas ofrecen por cada nivel de rendimiento. Duplicar la clasificación de DTU de una base de datos equivale a duplicar la potencia de base de datos. La comparativa nos permite evaluar el impacto en el rendimiento de la base de datos de la potencia creciente ofrecida por cada nivel de rendimiento por ejercicio de operaciones de base de datos real, mientras escala el tamaño de la base de datos, número de usuarios y tasas de transacción proporcionalmente a los recursos a la base de datos.

Al expresar el rendimiento del nivel de servicio básico usando las transacciones por hora, el nivel de servicio estándar mediante transacciones por minuto y el nivel de servicio Premium con transacciones por segundo, es más fácil relacionar rápidamente el rendimiento potencial de cada nivel de servicio para los requisitos de una aplicación.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Resultados de correlación comparativa a rendimiento de base de datos del mundo real
Es importante comprender que ASDB, como todas las pruebas de referencia, representante e indicativo solo. Las tasas de transacción logradas con la aplicación de referencia no estarán iguales a los que se puede lograr con otras aplicaciones. La comparativa consta de una colección de transacción diferentes tipos de ejecutan en un esquema que contiene un intervalo de tipos de datos y tablas. Mientras la comparativa ejercicios las mismas operaciones básicas que sean comunes a todas las cargas de trabajo OLTP, no se representa cualquier clase específica de la base de datos o aplicación. El objetivo de la referencia es proporcionar a una orientación razonable al rendimiento relativo de una base de datos que cabría esperar al escalar hacia arriba o hacia abajo entre los niveles de rendimiento. En realidad, bases de datos de diferentes tamaños y complejidad, encuentran diferentes combinaciones de cargas de trabajo y se responden de maneras diferentes. Por ejemplo, una aplicación de IO intensa puede presionar umbrales IO antes o una aplicación de la CPU puede alcanzado los límites de la CPU antes. No hay ninguna garantía de que cualquier base de datos determinado escala de la misma manera como referencia en aumento de carga.

El punto de referencia y su metodología se describen con más detalle.

## <a name="benchmark-summary"></a>Resumen de referencia
ASDB mide el rendimiento de una combinación de operaciones básicas de la base de datos que se producen con más frecuencia en las cargas de trabajo (OLTP) de procesamiento de transacciones en línea. Aunque la prueba comparativa está diseñada con en la cuenta, el esquema de la base de datos, la población de datos de la informática en nube y las transacciones han sido diseñadas para ser ampliamente representante de los elementos básicos más usados en las cargas de trabajo OLTP.

## <a name="schema"></a>Esquema
El esquema está diseñado para tener suficiente variedad y la complejidad para admitir una amplia gama de operaciones. La comparativa se ejecuta en una base de datos consta de los seis tablas. Las tablas se dividen en tres categorías: tamaño fijo, ajuste de escala y crecimiento. Hay dos tablas de tamaño fijo; tres tablas escalados; y una tabla de crecimiento. Tablas de tamaño fijo tienen un número constante de filas. Tablas de escalado tienen una cardinalidad que es proporcional al rendimiento de la base de datos, pero no cambia durante la referencia. Con el tamaño de la tabla creciente como una tabla de escalado de carga inicial, pero a continuación, los cambios de cardinalidad al ejecutar la prueba comparativa mientras se insertan y eliminan filas.

El esquema incluye una mezcla de tipos de datos de entero, numérico, carácter y fecha y hora. El esquema incluye claves principales y secundarias, pero no las claves externas, es decir, hay ninguna restricción de integridad referencial entre las tablas.

Un programa de generación de datos genera los datos de la base de datos inicial. Datos de entero y numéricos se generan con varias estrategias. En algunos casos, los valores se distribuyen al azar un intervalo. En otros casos, un conjunto de valores es permutado aleatoriamente para asegurarse de que se mantiene una distribución específica. Campos de texto se generan desde una lista de palabras para generar datos electrónicos realistas ponderada.

Se cambia el tamaño de la base de datos en función de "factor de escala". El factor de escala (abreviado como SF) determina la cardinalidad de la escala y el crecimiento de tablas. Como se describe en la sección usuarios y Pacing, el tamaño de la base de datos, el número de usuarios y obtener el máximo rendimiento todos escalar proporcionalmente entre sí.

## <a name="transactions"></a>Transacciones
La carga de trabajo consta de los tipos de transacciones nueve, tal como se muestra en la tabla siguiente. Cada transacción está diseñada para resaltar un determinado conjunto de características de sistema en el hardware de sistema y motor de base de datos, con contraste alto de las transacciones de otras. Este enfoque facilita evaluar el impacto de los diferentes componentes de rendimiento general. Por ejemplo, la transacción "Lectura pesados" produce un gran número de operaciones de lectura de disco.

| Tipo de transacción | Descripción |
|---|---|
| Interacción de lectura | SELECCIONE; memoria; sólo lectura |
| Medio de lectura | SELECCIONE; principalmente en memoria; sólo lectura |
| Visible de lectura | SELECCIONE; no principalmente en memoria; sólo lectura |
| Interacción de actualización | ACTUALIZAR; memoria; lectura y escritura |
| Actualizar visible | ACTUALIZAR; no principalmente en memoria; lectura y escritura |
| Insertar interacción | INSERTAR; memoria; lectura y escritura |
| Insertar visible | INSERTAR; no principalmente en memoria; lectura y escritura |
| Eliminar | ELIMINAR; combinación de en memoria y no en memoria; lectura y escritura |
| Visible de la CPU | SELECCIONE; memoria; carga de la CPU relativamente pesado; sólo lectura |

## <a name="workload-mix"></a>Mezcla de carga de trabajo
Se seleccionan al azar transacciones de una distribución ponderada con la combinación general siguiente. La mezcla global tiene una relación de lectura y escritura de aproximadamente 2:1.

| Tipo de transacción | % de combinación |
|---|---|
| Interacción de lectura | 35 |
| Medio de lectura | 20 |
| Visible de lectura | 5 |
| Interacción de actualización | 20 |
| Actualizar visible | 3 |
| Insertar interacción | 3 |
| Insertar visible | 2 |
| Eliminar | 2 |
| Visible de la CPU | 10 |

## <a name="users-and-pacing"></a>Los usuarios y el ritmo
La carga de trabajo de referencia se controla desde una herramienta que envíe transacciones a través de un conjunto de conexiones para simular el comportamiento de un número de usuarios. Aunque todas las conexiones y las transacciones son generados por el equipo, para simplificar nos referimos a estas conexiones como "usuarios". Aunque cada usuario funciona con independencia de los demás usuarios, todos los usuarios realizan el mismo ciclo de pasos que se muestra a continuación:

1. Establecer una conexión de base de datos.
2. Repita hasta que señala al salir:
    - Seleccione una transacción de forma aleatoria (una distribución ponderada).
    - Realizar la transacción seleccionada y medir el tiempo de respuesta.
    - Espere un retraso ritmo regulado.
3. Cerrar la conexión de base de datos.
4. Salir.

El retraso ritmo regulado (en el paso 2c) está seleccionado de forma aleatoria, pero con una distribución que tiene un promedio de 1,0 segundo. Por lo tanto cada usuario promedio, puede generar una transacción por segundo.

## <a name="scaling-rules"></a>Reglas de ajuste de escala
El número de usuarios es determinado por el tamaño de la base de datos (en unidades de factor de escala). Hay un usuario por cada cinco unidades factor de escala. Debido a la demora ritmo regulada, un usuario puede generar una transacción por segundo, promedio.

Por ejemplo, un factor de escala de 500 (SF = 500) tendrá 100 usuarios de base de datos y puede lograr una velocidad máxima de 100 TPS. Para conducir un TPS mayor velocidad requiere más usuarios y una base de datos más grande.

La siguiente tabla muestra el número de usuarios que realmente permanente para cada nivel de rendimiento y los niveles de servicio.

| Nivel de servicio (nivel de rendimiento) | Usuarios | Tamaño de la base de datos |
|---|---|---|
| Básico | 5 | 720 MB |
| Estándar (S0) | 10 | 1 GB DE MEMORIA |
| Estándar (S1) | 20 | 2.1 GB |
| Estándar (S2) | 50 | 7.1 GB |
| Premium (P1) | 100 | 14 GB |
| Premium (P2) | 200 | 28 GB |
| Premium (P6/3) | 800 | 114 GB |

## <a name="measurement-duration"></a>Duración de medida
Una referencia válida ejecutar requiere una duración de medición estable de al menos una hora.

## <a name="metrics"></a>Métricas
Las métricas claves en las pruebas comparativas son el rendimiento y tiempo de respuesta.

- Rendimiento es la medida de rendimiento esencial en el punto de referencia. Rendimiento se notifica en transacciones por unidad de tiempo, el recuento de todos los tipos de transacción.
- Tiempo de respuesta es una medida de la predicción de rendimiento. La restricción del tiempo de respuesta varía en función de la clase de servicio con mayor clases de servicio con un requisito de tiempo de respuesta más estricto, tal como se muestra a continuación.

| Clase de servicio  | Medida de rendimiento | Requisito de tiempo de respuesta |
|---|---|---|
| Premium | Transacciones por segundo | percentil 95 en 0,5 segundos |
| Estándar | Transacciones por minuto | percentil 90 segundos 1.0 |
| Básico | Transacciones por hora | 80. ° percentil en segundos 2.0 |

## <a name="conclusion"></a>Conclusión
La referencia de base de datos de SQL Azure mide el rendimiento relativo de ejecución por el rango de niveles de servicio disponible y los niveles de rendimiento de base de datos SQL Azure. La comparativa ejercicios una mezcla de operaciones básicas de la base de datos que se producen con más frecuencia en las cargas de trabajo (OLTP) de procesamiento de transacciones en línea. Medir el rendimiento real, la referencia proporciona una evaluación más significativa del impacto en el rendimiento de cambiar el nivel de rendimiento que es posible con una lista solo de los recursos proporcionados por cada nivel como IOPS, tamaño de la memoria y velocidad de CPU. En el futuro, seguiremos evolucionando en el punto de referencia para ampliar su ámbito y expandir los datos proporcionados.

## <a name="resources"></a>Recursos
[Introducción a la base de datos SQL](sql-database-technical-overview.md)

[Niveles de servicio y los niveles de rendimiento](sql-database-service-tiers.md)

[Guía de rendimiento de bases de datos](sql-database-performance-guidance.md)
