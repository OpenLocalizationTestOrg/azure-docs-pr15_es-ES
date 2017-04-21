<properties
    pageTitle="Conectar Excel a la base de datos SQL | Microsoft Azure"
    description="Obtenga información sobre cómo conectar Microsoft Excel a la base de datos de SQL Azure en la nube. Importar datos en Excel para la exploración de datos e informes."
    services="sql-database"
    keywords="conectar a sql de excel, importar datos a excel"
    documentationCenter=""
    authors="joseidz"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/05/2016"
    ms.author="joseidz"/>


# <a name="sql-database-tutorial-connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Tutorial de base de datos SQL: conectar Excel a una base de datos de SQL Azure y crear un informe

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Obtenga información sobre cómo conectar Excel a una base de datos SQL en la nube para poder importar datos y crear tablas y gráficos en función de los valores de la base de datos. En este tutorial que va a configurar la conexión entre Excel y una tabla de base de datos, guarde el archivo que almacena datos y la información de conexión de Excel y, a continuación, crear un gráfico dinámico de los valores de la base de datos.

Antes de empezar, necesitará una base de datos SQL Azure. Si no tiene una, vea [crear la primera base de datos SQL](sql-database-get-started.md) para obtener una base de datos con datos de ejemplo hacia arriba y ejecutando en unos minutos. En este artículo, deberá importar datos de ejemplo en Excel de este artículo, pero puede seguir pasos similares en sus propios datos.

También necesitará una copia de Excel. En este artículo utiliza [Microsoft Excel 2016](https://products.office.com/en-US/).

## <a name="connect-excel-to-a-sql-database-and-create-an-odc-file"></a>Conectar Excel a una base de datos SQL y crear un archivo odc

1.  Para conectar Excel a la base de datos SQL, abra Excel y cree un nuevo libro o abra un libro de Excel existente.

2.  En la barra de menús en la parte superior de la página, haga clic en **datos**, haga clic en **Desde otros orígenes**y, a continuación, haga clic en **Desde SQL Server**.

    ![Seleccionar origen de datos: conectar Excel a la base de datos SQL.](./media/sql-database-connect-excel/excel_data_source.png)

    Se abre el Asistente para la conexión de datos.

3.  En el cuadro de diálogo **Conectar con el servidor de base de datos** , escriba la base de datos SQL **nombre del servidor** que desea conectarse en el formulario <*nombre_de_servidor*>**. database.windows.net**. Por ejemplo, **adworkserver.database.windows.net**.

4.  En **las credenciales de inicio de sesión**, haga clic en **usar el siguiente nombre de usuario y contraseña**, escriba el **Nombre de usuario** y **contraseña** configurado para el servidor de base de datos SQL cuando se ha creado y, a continuación, haga clic en **siguiente**.

    ![Escriba la credenciales de inicio de sesión y nombre de servidor](./media/sql-database-connect-excel/connect-to-server.png)

    > [AZURE.TIP] Dependiendo de su entorno de red, no podrá conectar o se pierda la conexión si el servidor de base de datos SQL no permite el tráfico de la dirección IP del cliente. Vaya al [portal de Azure](https://portal.azure.com/), haga clic en servidores de SQL Server, haga clic en el servidor, haga clic en firewall en configuración y agregar la dirección IP del cliente. Para obtener más información, consulte [cómo configurar el firewall](sql-database-configure-firewall-settings.md) .

5. En el cuadro de diálogo **Seleccionar base de datos y tabla** , seleccione la base de datos que desea trabajar con desde la lista y, a continuación, haga clic en las tablas o vistas que desea trabajar con (elegimos **vGetAllCategories**) y, a continuación, haga clic en **siguiente**.

    ![Seleccione una base de datos y la tabla.](./media/sql-database-connect-excel/select-database-and-table.png)

    Se abre el cuadro de diálogo **Guardar archivo de conexión de datos y finalizar** , donde se proporciona información sobre el archivo de conexión (*.odc) de base de datos de Office que utiliza Excel. Puede dejar la configuración predeterminada o personalizar las selecciones.

6. Puede dejar los valores predeterminados, pero anote el **Nombre de archivo** en particular. Una **Descripción**, un **Nombre descriptivo**y **Palabras clave de búsqueda** ayudan y recuerde que otros usuarios lo que va a conectarse a y busque la conexión. Haga clic en **intentar utilizar este archivo para actualizar los datos siempre** si desea que la información de conexión almacenada en el archivo odc para que pueda actualizar cuando se conecte a él y, a continuación, haga clic en **Finalizar**.

    ![Guardar un archivo odc](./media/sql-database-connect-excel/save-odc-file.png)

    Aparece el cuadro de diálogo **Importar datos** .

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importar los datos en Excel y crear un gráfico dinámico
Ahora que ha establecido la conexión y crea el archivo con información de conexión y los datos, está leyendo para importar los datos.

1. En el cuadro de diálogo **Importar datos** , haga clic en la opción que desee para presentar los datos en la hoja de cálculo y, a continuación, haga clic en **Aceptar**. Elegimos **gráfico dinámico**. También puede elegir crear una **nueva hoja de cálculo** o para **Agregar datos a un modelo de datos**. Para obtener más información sobre los modelos de datos, vea [crear un modelo de datos en Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). Haga clic en **Propiedades** para explorar la información sobre el archivo odc que creó en el paso anterior y elija las opciones para actualizar los datos.

    ![Elegir el formato de datos en Excel](./media/sql-database-connect-excel/import-data.png)

    Ahora, la hoja de cálculo contiene una tabla dinámica vacía y el gráfico.

8. En **Campos de tabla dinámica**, seleccione todas las casillas de los campos que desea ver.

    ![Configurar el informe de la base de datos.](./media/sql-database-connect-excel/power-pivot-results.png)

> [AZURE.TIP]Si desea conectarse a otros libros de Excel y hojas de cálculo a la base de datos, haga clic en **datos**, haga clic en **conexiones**, haga clic en **Agregar**, seleccione la conexión que creó en la lista y, a continuación, haga clic en **Abrir**.
> ![Abrir una conexión de otro libro](./media/sql-database-connect-excel/open-from-another-workbook.png)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [conectarse a la base de datos de SQL con SQL Server Management Studio](sql-database-connect-query-ssms.md) para consultas avanzadas y análisis.
- Obtenga información sobre las ventajas de los [grupos de elásticos](sql-database-elastic-pool.md).
- Obtenga información sobre cómo [crear una aplicación web que se conecte a la base de datos de SQL en el servidor back-end](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
