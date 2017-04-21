<properties
   pageTitle="Migrar el almacén de datos de SQL Azure existente con el almacenamiento de premium | Microsoft Azure"
   description="Instrucciones para migrar un almacén de datos de SQL existente con el almacenamiento de premium"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="nicw;barbkess;sonyama"/>

# <a name="migration-to-premium-storage-details"></a>Migración a los detalles de almacenamiento Premium
Almacén de datos SQL recientemente presentó [Almacenamiento Premium para mayor capacidad de previsión de rendimiento][].  Ahora estamos listos migrar existente de datos de almacén, actualmente en almacenamiento estándar a almacenamiento Premium.  Siga leyendo para obtener más detalles acerca de cómo y cuándo se producen migraciones automáticas y cómo migrar automática si prefiere controlar cuándo se produce el tiempo de inactividad.

Si tiene más de un almacén de datos, use la [programación de la migración automática][] a continuación para determinar cuándo también se migrará.

## <a name="determine-storage-type"></a>Determinar el tipo de almacenamiento
Si ha creado un DW antes de las fechas a continuación, se utiliza actualmente almacenamiento estándar.  Cada almacén de datos de almacenamiento estándar que está sujeta a la migración automática tiene un aviso en la parte superior de la hoja de almacén de datos en el [Portal de Azure][] que dice "*una próxima actualización al almacenamiento premium requieren una interrupción.  Obtener más ->*. "

| **Región**          | **DW creado antes de esta fecha**   |
| :------------------ | :-------------------------------- |
| Australia Oriental      | Almacenamiento de Premium no está disponible todavía |
| Australia sureste | 5 de agosto de 2016                    |
| Sur de Brasil        | 5 de agosto de 2016                    |
| Canadá Central      | 25 de mayo de 2016                      |
| Canadá oriental         | 26 de mayo de 2016                      |
| Central de EE.          | 26 de mayo de 2016                      |
| China oriental          | Almacenamiento de Premium no está disponible todavía |
| Norte de China         | Almacenamiento de Premium no está disponible todavía |
| Asia oriental           | 25 de mayo de 2016                      |
| Estados Unidos oriental             | 26 de mayo de 2016                      |
| US2 oriental            | 27 de mayo de 2016                      |
| India Central       | 27 de mayo de 2016                      |
| India sur         | 26 de mayo de 2016                      |
| India oeste          | Almacenamiento de Premium no está disponible todavía |
| Japón oriental          | 5 de agosto de 2016                    |
| Japón oeste          | Almacenamiento de Premium no está disponible todavía |
| Norte Central de EE.    | Almacenamiento de Premium no está disponible todavía |
| Europa del Norte        | 5 de agosto de 2016                    |
| Sur Central de EE.    | 27 de mayo de 2016                      |
| Sudeste asiático      | 24 de mayo de 2016                      |
| Europa occidental         | 25 de mayo de 2016                      |
| Oeste Central de EE.     | 26 de agosto de 2016                   |
| Estados Unidos occidental             | 26 de mayo de 2016                      |
| US2 occidental            | 26 de agosto de 2016                   |

## <a name="automatic-migration-details"></a>Detalles de la migración automática
De forma predeterminada, se migrará la base de datos para usted durante 6 p.m. y 6 am en la hora local de su región durante la [programación de la migración automática][] a continuación.  El almacén de datos existente será inutilizable durante la migración.  Nos estimar que la migración tardará aproximadamente una hora por TB de almacenamiento por el almacén de datos.  También se garantiza que no se carguen en alguna parte de la migración automática.

> [AZURE.NOTE] No podrá usar el almacén de datos existente durante la migración.  Una vez completada la migración, el almacén de datos será volver a conectarse.

Los detalles siguientes son pasos que Microsoft está realizando en su nombre para completar la migración y no requiere ninguna intervención por su parte.  En este ejemplo, suponga que su DW existente de almacenamiento estándar denominado "MyDW."

1.  Microsoft cambia el nombre "MyDW" a "MyDW_DO_NOT_USE_ [marca de tiempo]"
2.  Microsoft pausa "MyDW_DO_NOT_USE_ [marca de tiempo]."  Durante este período, se realiza una copia de seguridad.  Si se encuentra algún problema durante este proceso, es posible que vea varias pausa o reanuda.
3.  Microsoft crea una nueva DW denominado "MyDW" en Premium almacenamiento de la copia de seguridad realizada en el paso 2.  "MyDW" no aparecerá hasta una vez finalizada la restauración.
4.  Una vez completada la restauración, devuelve "MyDW" a la misma DWUs y el estado de pausa o active era antes de la migración.
5.  Una vez completada la migración, Microsoft elimina "MyDW_DO_NOT_USE_ [marca de tiempo]"
    
> [AZURE.NOTE] Esta configuración no se mantienen como parte de la migración:
> 
>   -  En el nivel de base de datos de auditoría debe volver a habilitar
>   -  Las reglas de Firewall en el nivel de **base de datos** que necesite ser añadir.  Las reglas de Firewall en el **servidor** no se se ven afectados.

### <a name="automatic-migration-schedule"></a>Programación de la migración automática
Migraciones automáticas se producen de 6 p.m. – 6 a.m. (hora local por región) durante la programación de interrupción siguiente.

| **Región**          | **Fecha de inicio estimada**     | **Fecha de finalización estimada**       |
| :------------------ | :--------------------------- | :--------------------------- |
| Australia Oriental      | Aún no ha determinado           | Aún no ha determinado           |
| Australia sureste | 10 de agosto de 2016              | 24 de agosto de 2016              |
| Sur de Brasil        | 10 de agosto de 2016              | 24 de agosto de 2016              |
| Canadá Central      | 23 de junio de 2016                | 1 de julio de 2016                 |
| Canadá oriental         | 23 de junio de 2016                | 1 de julio de 2016                 |
| Central de EE.          | 23 de junio de 2016                | 4 de julio de 2016                 |
| China oriental          | Aún no ha determinado           | Aún no ha determinado           |
| Norte de China         | Aún no ha determinado           | Aún no ha determinado           |
| Asia oriental           | 23 de junio de 2016                | 1 de julio de 2016                 |
| Estados Unidos oriental             | 23 de junio de 2016                | 11 de julio de 2016                |
| US2 oriental            | 23 de junio de 2016                | 8 de julio de 2016                 |
| India Central       | 23 de junio de 2016                | 1 de julio de 2016                 |
| India sur         | 23 de junio de 2016                | 1 de julio de 2016                 |
| India oeste          | Aún no ha determinado           | Aún no ha determinado           |
| Japón oriental          | 10 de agosto de 2016              | 24 de agosto de 2016              |
| Japón oeste          | Aún no ha determinado           | Aún no ha determinado           |
| Norte Central de EE.    | Aún no ha determinado           | Aún no ha determinado           |
| Europa del Norte        | 10 de agosto de 2016              | 31 de agosto de 2016              |
| Sur Central de EE.    | 23 de junio de 2016                | 2 de julio de 2016                 |
| Sudeste asiático      | 23 de junio de 2016                | 1 de julio de 2016                 |
| Europa occidental         | 23 de junio de 2016                | 8 de julio de 2016                 |
| Oeste Central de EE.     | 14 de agosto de 2016              | 31 de agosto de 2016              |
| Estados Unidos occidental             | 23 de junio de 2016                | 7 de julio de 2016                 |
| US2 occidental            | 14 de agosto de 2016              | 31 de agosto de 2016              |

## <a name="self-migration-to-premium-storage"></a>Migración automática a almacenamiento Premium
Si desea controlar cuándo se producirá tiempo de inactividad, puede usar los siguientes pasos para migrar un almacén de datos existente en el almacenamiento de estándar a almacenamiento Premium.  Si decide migrar automática, debe completar la migración automática antes de comenzar la migración automática de esa región para evitar el riesgo de la migración automática en conflicto (consulte la [programación de la migración automática][]).

### <a name="self-migration-instructions"></a>Instrucciones de migración automática
Si desea controlar el tiempo de inactividad, puede migrar automáticamente el almacén de datos mediante el uso de la copia de seguridad y restauración.  La parte de restauración de la migración debe tomar aproximadamente una hora por TB de almacenamiento por DW.  Si desea conservar el mismo nombre, una vez completada la migración, siga los pasos para [conocer los pasos para cambiar el nombre durante la migración][]. 

1.  [Pausar][] el DW que tiene una copia de seguridad automática
2.  [Restaurar][] desde la instantánea más reciente
3.  Eliminar el DW existente de almacenamiento estándar. **Si no puede realizar este paso, se cargará para ambos DWs.**

> [AZURE.NOTE] Esta configuración no se mantienen como parte de la migración:
> 
>   -  En el nivel de base de datos de auditoría debe volver a habilitar
>   -  Las reglas de Firewall en el nivel de **base de datos** que necesite ser añadir.  Las reglas de Firewall en el **servidor** no se se ven afectados.

#### <a name="optional-steps-to-rename-during-migration"></a>Opcional: pasos para cambiar el nombre durante la migración 
Dos bases de datos en el mismo servidor lógico no pueden tener el mismo nombre. Almacén de datos SQL admite ahora la posibilidad de cambiar el nombre de un DW.

En este ejemplo, suponga que su DW existente de almacenamiento estándar denominado "MyDW."

1.  Cambiar el nombre "MyDW" con el comando Modificar base de datos que sigue a algo como "MyDW_BeforeMigration."  Este comando elimina todas las transacciones existentes y debe realizarse en la base de datos sea correcta.
```
ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
```
2.  [Pausar][] "MyDW_BeforeMigration", que tiene una copia de seguridad automática
3.  [Restaurar][] a partir de la más reciente de una base de datos con el nombre que usó para tener instantáneas (ex: "MyDW")
4.  Eliminar "MyDW_BeforeMigration".  **Si no puede realizar este paso, se cargará para ambos DWs.**

> [AZURE.NOTE] Esta configuración no se mantienen como parte de la migración:
> 
>   -  En el nivel de base de datos de auditoría debe volver a habilitar
>   -  Las reglas de Firewall en el nivel de **base de datos** que necesite ser añadir.  Las reglas de Firewall en el **servidor** no se se ven afectados.

## <a name="next-steps"></a>Pasos siguientes
Con el cambio de almacenamiento Premium, también hemos aumentado el número de archivos de blobs de base de datos en la arquitectura de su almacén de datos subyacente.  Para maximizar las ventajas de rendimiento de este cambio, le recomendamos que vuelva a crear los índices de Columnstore agrupado mediante el siguiente script.  La siguiente secuencia de comandos funciona forzar algunos de los datos existentes a los BLOB adicionales.  Si no realiza ninguna acción, los datos se redistribuyan naturalmente con el tiempo como cargar más datos en las tablas del almacén de datos.

**Requisitos previos:**

1.  Almacén de datos debe ejecutarse con 1.000 DWUs o superior (consulte [escala cálculo power][])
2.  Debe ser usuario ejecutando el script en el [rol de mediumrc][] o superior
    1.  Para agregar un usuario a este rol, ejecute lo siguiente: 
        1.  ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create Table to control Index Rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select 
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from 
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go
 
--------------------------------------------------------------------------------
-- Step 2: Execute Index Rebuilds.  If script fails, the below can be rerun to restart where last left off
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Cleanup Table Created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

Si tiene problemas con el almacén de datos, [crear una incidencia de soporte técnico][] y la referencia "Almacenamiento de migración a Premium" como la causa posible.

<!--Image references-->

<!--Article references-->
[programación de la migración automática]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[crear una incidencia de soporte técnico]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pausar]: sql-data-warehouse-manage-compute-portal.md/#pause-compute
[Restaurar]: sql-data-warehouse-restore-database-portal.md
[pasos para cambiar el nombre durante la migración]: #optional-steps-to-rename-during-migration
[potencia informática de escala]: sql-data-warehouse-manage-compute-portal/#scale-compute-power
[función mediumrc]: sql-data-warehouse-develop-concurrency/#workload-management

<!--MSDN references-->


<!--Other Web references-->
[Almacenamiento de Premium para mayor capacidad de previsión de rendimiento]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Portal de Azure]: https://portal.azure.com
