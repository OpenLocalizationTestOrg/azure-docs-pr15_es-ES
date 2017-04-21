<properties
   pageTitle="Vistas en el almacén de datos SQL | Microsoft Azure"
   description="Sugerencias para usar vistas de Transact-SQL en el almacén de datos de SQL Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/01/2016"
   ms.author="jrj;barbkess;sonyama"/>


# <a name="views-in-sql-data-warehouse"></a>Vistas en el almacén de datos SQL

Vistas son particularmente útiles en el almacén de datos de SQL. Se puede utilizar en un número de diferentes formas para mejorar la calidad de la solución.  En este artículo se resalta algunos ejemplos de cómo mejorar su solución con vistas, así como las limitaciones que se deben tener en cuenta.

> [AZURE.NOTE] Sintaxis para `CREATE VIEW` no se describe en este artículo. Consulte el artículo [Crear vista][] en MSDN para obtener la información de referencia.

## <a name="architectural-abstraction"></a>Abstracción de arquitectura
Un modelo de aplicación muy común es volver a crear tablas con crear tabla AS seleccione (CTAS) seguido de un objeto al cambiar el nombre de patrón durante la carga de datos.

El ejemplo siguiente agrega registros de la nueva fecha a una dimensión de fecha. Observe cómo una nueva tabble, DimDate_New, se crea por primera vez y cambiar el nombre para reemplazar la versión original de la tabla.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Sin embargo, este enfoque puede dar lugar a tablas que aparecen y desaparecen de la vista del usuario, así como mensajes de error "la tabla no existe". Vistas pueden utilizarse para proporcionar a los usuarios con una capa de presentación coherente mientras se cambia el nombre de los objetos subyacentes. Proporcionando a los usuarios acceso a los datos a través de una de las vistas, significa que los usuarios no necesitan tener visibilidad de las tablas subyacentes. Esto ofrece una experiencia de usuario coherente mientras puede garantizar que los datos del almacén diseñadores evolucionando en el modelo de datos y maximizar el rendimiento utilizando CTAS durante el proceso de carga de datos.    

## <a name="performance-optimization"></a>Optimización del rendimiento
También se pueden utilizar las vistas para aplicar combinaciones de rendimiento óptimo entre las tablas. Por ejemplo, una vista puede incorporar una clave de distribución redundantes como parte de los criterios de unión para reducir el movimiento de datos.  Otra ventaja de una vista puede forzar una consulta específica o una sugerencia de combinación. Uso de vistas de esta manera garantiza que siempre se realizan las combinaciones de manera óptima, evitando la necesidad de recordar la construcción correcta para sus combinaciones para los usuarios.

## <a name="limitations"></a>Limitaciones
Vistas en el almacén de datos de SQL son sólo metadatos.  Por consiguiente no están disponibles las siguientes opciones:

-   No hay ninguna opción de enlace de esquema
-   Tablas base no se puede actualizar a través de la vista
-   No se pueden crear vistas sobre tablas temporales
-   Hay no compatible con la expandir / sugerencias NOEXPAND
-   No hay ninguna vista indizada en el almacén de datos de SQL


## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias de desarrollo, vea [información general sobre el desarrollo de almacén de datos de SQL][].
Para `CREATE VIEW` sintaxis, consulte [Crear vista][].

<!--Image references-->

<!--Article references-->
[Información general sobre el desarrollo de almacén de datos SQL]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CREAR VISTA]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->
