<properties
   pageTitle="Analizar datos en el almacén de datos de lago mediante Power BI | Microsoft Azure"
   description="Usar Power BI para analizar los datos almacenados en el almacén de lago de datos de Azure"
   services="data-lake-store" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Analizar datos en el almacén de datos de lago mediante Power BI

En este artículo aprenderá cómo usar Power BI Desktop para analizar y visualizar datos almacenados en el almacén de lago de datos de Azure.

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

- **Cuenta de la tienda de lago de datos de azure**. Siga las instrucciones en [Introducción a Azure datos lago tienda con el Portal de Azure](data-lake-store-get-started-portal.md). En este artículo se supone que ya ha creado una cuenta de almacén de datos lago, denominada **mybidatalakestore**y cargar un archivo de datos de ejemplo (**Drivers.txt**) al mismo. Este archivo de ejemplo está disponible para su descarga desde [Lago Git repositorio de datos de Azure](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).

- **Power BI Desktop**. Se pueden descargar desde el [Centro de descarga de Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 


## <a name="create-a-report-in-power-bi-desktop"></a>Crear un informe en Power BI Desktop

1. Inicie Power BI Desktop en el equipo.

2. En la cinta **Inicio** , haga clic en **Obtener datos**y, a continuación, haga clic en más. En el cuadro de diálogo **Obtener datos** , haga clic en **Azure**, haga clic en **Almacén de lago de datos de Azure**y, a continuación, haga clic en **Conectar**.

    ![Conectarse a datos de la tienda lago] (./media/data-lake-store-power-bi/get-data-lake-store-account.png "Conectarse a datos de la tienda lago")

3. Si aparece un cuadro de diálogo sobre el conector que se está en la fase de desarrollo, elegir para continuar.

4. En el cuadro de diálogo **Almacén de lago de datos de Microsoft Azure** , proporcionar la dirección URL para su cuenta de almacén de datos lago y, a continuación, haga clic en **Aceptar**.

    ![Dirección URL para el almacén de datos de lago] (./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Dirección URL para el almacén de datos de lago")

5. En el cuadro de diálogo siguiente, haga clic en **iniciar sesión** para iniciar sesión en la cuenta de la tienda de lago de datos. Se le redirigirá a la página de inicio de sesión de su organización. Siga las indicaciones para iniciar sesión en la cuenta.

    ![Inicie sesión en el almacén de datos de lago] (./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Inicie sesión en el almacén de datos de lago")

6. Una vez haya iniciado sesión correctamente, haga clic en **Conectar**.

    ![Conectarse a datos de la tienda lago] (./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Conectarse a datos de la tienda lago")

7. El cuadro de diálogo siguiente muestra el archivo que ha cargado en su cuenta de almacén de datos lago. Compruebe la información y, a continuación, haga clic en **cargar**.

    ![Cargar datos del almacén de datos lago] (./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Cargar datos del almacén de datos lago")

8. Una vez cargado correctamente los datos en Power BI, verá los siguientes campos en la pestaña **campos** .

    ![Campos importados] (./media/data-lake-store-power-bi/imported-fields.png "Campos importados")

    Sin embargo, para visualizar y analizar los datos, es mejor los datos estén disponibles por los siguientes campos

    ![Campos deseado] (./media/data-lake-store-power-bi/desired-fields.png "Campos deseado")

    En los pasos siguientes, actualizaremos la consulta para convertir los datos importados en el formato deseado.

9. En la cinta **Inicio** , haga clic en **Editar consultas**.

    ![Editar consultas] (./media/data-lake-store-power-bi/edit-queries.png "Editar consultas")

10. En el Editor de consultas, en la columna de **contenido** , haga clic en **binario**.

    ![Editar consultas] (./media/data-lake-store-power-bi/convert-query1.png "Editar consultas")

11. Verá un icono de archivo, que representa el archivo **Drivers.txt** que ha cargado. Haga clic en el archivo y haga clic en **CSV**.  

    ![Editar consultas] (./media/data-lake-store-power-bi/convert-query2.png "Editar consultas")

12. Debe ver un resultado como se muestra a continuación. Ahora están disponibles en un formato que puede usar para crear visualizaciones de datos.

    ![Editar consultas] (./media/data-lake-store-power-bi/convert-query3.png "Editar consultas")

13. En la cinta **Inicio** , haga clic en **Cerrar y aplicar**y, a continuación, haga clic en **Cerrar y volver a aplicar**.

    ![Editar consultas] (./media/data-lake-store-power-bi/load-edited-query.png "Editar consultas")

14. Una vez que se actualiza la consulta, la ficha **campos** mostrará los nuevos campos disponibles para la visualización.

    ![Campos de actualización] (./media/data-lake-store-power-bi/updated-query-fields.png "Campos de actualización")

15. Deje que nosotros crear un gráfico circular para representar los controladores de cada ciudad para un determinado país. Para ello, realice las selecciones siguientes.

    1. En la pestaña de visualizaciones, haga clic en el símbolo de un gráfico circular.

        ![Crear un gráfico circular] (./media/data-lake-store-power-bi/create-pie-chart.png "Crear un gráfico circular")

    2. Las columnas que se va a usar son **columna 4** (nombre de la ciudad) y la **columna 7** (nombre del país). Arrastre estas columnas de **campos** de la pestaña a la ficha **visualizaciones** tal como se muestra a continuación.

        ![Crear visualizaciones] (./media/data-lake-store-power-bi/create-visualizations.png "Crear visualizaciones")

    3. Gráfico circular ahora debería ser similar a como se muestra a continuación.

        ![Gráfico circular] (./media/data-lake-store-power-bi/pie-chart.png "Crear visualizaciones")

16. Si selecciona un país específico de los filtros de nivel de página, ahora puede ver el número de controladores en cada ciudad del país seleccionado. Por ejemplo, en la pestaña de **visualizaciones** , en **filtros de nivel de página**, seleccione **Brasil**.

    ![Seleccionar un país] (./media/data-lake-store-power-bi/select-country.png "Seleccionar un país")

17. Gráfico circular se actualiza automáticamente para mostrar los controladores de las ciudades de Brasil.

    ![Controladores en un país] (./media/data-lake-store-power-bi/driver-per-country.png "Controladores por país")

18. En el menú **archivo** , haga clic en **Guardar** para guardar la visualización como un archivo de Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publicar el informe al servicio de Power BI

Una vez que haya creado las visualizaciones en Power BI Desktop, puede compartirlo con otras personas mediante su publicación en el servicio Power BI. Para obtener instrucciones sobre cómo hacerlo, consulte [publicar de Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Vea también

* [Analizar datos en el almacén de lago de datos mediante el análisis de datos lago](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
