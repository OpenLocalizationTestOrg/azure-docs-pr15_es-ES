<properties
   pageTitle="Visualizar datos de almacén de datos de SQL con Microsoft Azure de Power BI"
   description="Visualizar datos de almacén de datos SQL con Power BI"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="" />

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="lodipalm;barbkess;sonyama" />

# <a name="visualize-data-with-power-bi"></a>Visualizar datos con Power BI

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Aprendizaje del equipo de Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

En este tutorial se muestra cómo usar Power BI para conectarse al almacén de datos de SQL y crear algunas visualizaciones básicas.

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## <a name="prerequisites"></a>Requisitos previos

Paso a través de este tutorial, necesita:

- Un almacén de datos de SQL se ha cargado previamente con la base de datos de AdventureWorksDW. Para aprovisionar esto, vea [crear un almacén de datos de SQL][] y elija cargar los datos de ejemplo. Si ya tiene un almacén de datos, pero no tiene datos de ejemplo, puede [cargar los datos de ejemplo manualmente][].


## <a name="1-connect-to-your-database"></a>1. Conéctese a la base de datos

Abra Power BI y conectarse a la base de datos de AdventureWorksDW:

1. Iniciar sesión en el [portal de Azure][].
2. Haga clic en **bases de datos SQL** y elija la base de datos de almacén de datos de SQL de AdventureWorks.

    ![Buscar la base de datos][1]

3. Haga clic en el botón "Abrir en Power BI".

    ![Botón de Power BI][2]

4. Ahora debe ver la página de conexión de almacén de datos SQL que muestra la dirección web de su base de datos. Haga clic en siguiente.

    ![Conexión de Power BI][3]

6. Escriba el nombre de usuario del servidor de SQL Azure y contraseña y se conectará totalmente a la base de datos de almacén de datos de SQL.

    ![Power BI inicio de sesión][4]

7. Una vez que haya iniciado sesión en Power BI, haga clic en el conjunto de datos AdventureWorksDW en el módulo de la izquierda. Esto abrirá la base de datos.

    ![Power BI abrir AdventureWorksDW][5]



## <a name="2-create-a-report"></a>2. crear un informe

Ya está listo para usar Power BI para analizar los datos de ejemplo AdventureWorksDW. Para realizar el análisis, AdventureWorksDW tiene una vista denominada AggregateSales. Esta vista contiene algunas de las métricas claves para analizar las ventas de la compañía.

1. Para crear un mapa del importe de ventas según el código postal, en el panel derecho de campos, haga clic en la vista AggregateSales para expandirla. Haga clic en las columnas CódigoPostal y SalesAmount para seleccionarlos.

    ![Power BI seleccione AggregateSales][6]

    Power BI reconoce automáticamente es datos geográficos y colocarlo en un mapa para usted.

    ![Mapa de Power BI][7]

2. Este paso crea un gráfico de barras que muestra la cantidad de ventas por los ingresos del cliente. Para crear este ir a la vista de AggregateSales expandida. Haga clic en el campo SalesAmount. Arrastre el campo ingresos del cliente a la izquierda y colóquelos en eje.

    ![Eje de selección de Power BI][8]

    Se mueve el gráfico de barras por encima de la izquierda.

    ![Barra de Power BI][9]

3. Este paso crea un gráfico de líneas que muestra el importe de ventas por fecha de pedido. Para crear este ir a la vista de AggregateSales expandida. Haga clic en SalesAmount y FechaPedido. En las visualizaciones columna, haga clic en el icono de gráfico de líneas. Este es el primer icono en la segunda línea en visualizaciones.

    ![Gráfico de líneas de selección de Power BI][10]

    Ahora tiene un informe que muestra tres diferentes visualizaciones de datos.

    ![Línea de Power BI][11]

Puede guardar el progreso en cualquier momento haciendo clic en **archivo** y seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes
Ahora que hemos dado algún tiempo de preparación con los datos de ejemplo, consulte cómo [desarrollar][], [cargar][]o [migrar][]. O bien, eche un vistazo al [sitio Web de Power BI][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migrar]: sql-data-warehouse-overview-migrate.md
[desarrollar]: sql-data-warehouse-overview-develop.md
[cargar]: sql-data-warehouse-overview-load.md
[cargar datos de ejemplo manualmente]: sql-data-warehouse-load-sample-databases.md
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Crear un almacén de datos SQL]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Portal de Azure]: https://portal.azure.com/
[Sitio Web de Power BI]: http://www.powerbi.com/
