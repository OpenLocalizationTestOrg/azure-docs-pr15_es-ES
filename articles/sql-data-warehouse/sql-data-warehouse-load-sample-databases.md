<properties
   pageTitle="Cargar datos de ejemplo en el almacén de datos SQL | Microsoft Azure"
   description="Cargar datos de ejemplo en el almacén de datos SQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="load-sample-data-into-sql-data-warehouse"></a>Cargar datos de ejemplo en el almacén de datos SQL

Siga estos pasos sencillos para cargar y consultar la base de datos de ejemplo de Adventure Works. Estas secuencias de comandos primero usan sqlcmd para ejecutar SQL que se crea tablas y vistas. Una vez que se han creado tablas, las secuencias de comandos usará bcp para cargar los datos.  Si todavía no tiene sqlcmd y bcp instalado, siga estos vínculos para [instalar bcp][] e [instalar sqlcmd][].

##<a name="load-sample-data"></a>Cargar datos de ejemplo

1. Descargue el archivo zip de [Secuencias de comandos de ejemplo de Adventure Works para el almacén de datos de SQL][] .

2. Extraer los archivos de zip descargado en un directorio de su equipo local.

3. Editar el archivo extraído de aw_create.bat y establecer las siguientes variables que se encuentra en la parte superior del archivo.  No olvide no dejar espacios en blanco entre el "=" y el parámetro.  A continuación se muestran ejemplos del aspecto de las ediciones.

    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```

4. Desde un símbolo del sistema de Windows cmd, ejecute el aw_create.bat editado.  Asegúrese de que se encuentra en el directorio donde guardó la versión modificada de aw_create.bat.
Esta secuencia de comandos le...
    * Quitar tablas de Adventure Works ni las vistas que ya existen en la base de datos
    * Crear las tablas de Adventure Works y vistas
    * Cargar cada tabla de Adventure Works con bcp
    * Validar los recuentos de filas de cada tabla de Adventure Works
    * Recopilar estadísticas en todas las columnas de cada tabla de Adventure Works


##<a name="query-sample-data"></a>Datos de ejemplo de consulta

Una vez que haya cargado algunos datos de ejemplo en el almacén de datos de SQL, puede ejecutar consultas unas rápidamente.  Para ejecutar una consulta, conectarse a la base de datos de Adventure Works recién creado en DW de SQL Azure mediante Visual Studio y SSDT, como se describe en el documento [de la consulta con Visual Studio][] .

Ejemplo de una instrucción select simple para obtener toda la información de los empleados:

```sql
SELECT * FROM DimEmployee;
```

Ejemplo de una consulta más compleja con construcciones como agrupar por para ver la cantidad total de todas las ventas de cada día:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Ejemplo de una instrucción SELECT con una cláusula WHERE para filtrar los pedidos de antes de una fecha determinada:

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Almacén de datos SQL admite casi todas las construcciones de T SQL que es compatible con SQL Server.  Las diferencias son documentadas en nuestra documentación [migrar código][] .

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha tenido ocasión de probar algunas consultas con datos de ejemplo, consulte cómo [desarrollar][], [cargar][]o [migrar][] al almacén de datos de SQL.

<!--Image references-->

<!--Article references-->
[migrar]: sql-data-warehouse-overview-migrate.md
[desarrollar]: sql-data-warehouse-overview-develop.md
[cargar]: sql-data-warehouse-overview-load.md
[consulta con Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migrar código]: sql-data-warehouse-migrate-code.md
[instalar bcp]: sql-data-warehouse-load-with-bcp.md
[instalar sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Las secuencias de comandos de ejemplo de Adventure Works para el almacén de datos SQL]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip
