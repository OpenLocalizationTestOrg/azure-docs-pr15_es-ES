<properties 
    pageTitle="Obtenga su cuenta DocumentDB S1 | Microsoft Azure" 
    description="Aprovechar las ventajas de rendimiento mejorado en su cuenta de DocumentDB S1 realizando algunos cambios simples en el portal de Azure." 
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="mimig"/>

# <a name="supercharge-your-documentdb-account"></a>Obtenga su cuenta de DocumentDB

Siga estos pasos para aprovechar las ventajas de rendimiento mejorado para su cuenta de Azure DocumentDB S1. Con poca o sin costo adicional, puede mejorar el rendimiento de su cuenta de S1 existente de 250 [RU/s](documentdb-request-units.md) 400 RU/s o más!  

> [AZURE.VIDEO changedocumentdbcollectionperformance]

## <a name="change-to-user-defined-performance-in-the-azure-portal"></a>Cambiar a rendimiento definidas por el usuario en el portal de Azure

1. En el explorador, vaya al [**portal de Azure**](https://portal.azure.com). 
2. Haga clic en **Examinar** -> **DocumentDB (NoSQL)**y, a continuación, seleccione la cuenta DocumentDB para modificar.   
3. En la lente de **bases de datos** , seleccione la base de datos para modificar y, a continuación, en el módulo de **base de datos** , seleccione la colección con el nivel de precios S1.

      ![Captura de pantalla del módulo de base de datos con una colección de S1](./media/documentdb-supercharge-your-account/documentdb-change-performance-S1.png)

4. En el módulo de la **colección** , haga clic en **más**y, a continuación, haga clic en **configuración**.   
5. En el módulo de **configuración** , haga clic en el **Nivel de precios** y observe que se muestra la estimación de coste mensual para cada plan. En el módulo de **Elegir el nivel de precios** , haga clic en **estándar**y, a continuación, haga clic en **Seleccionar** para guardar el cambio.

      ![Captura de pantalla de la configuración de DocumentDB y elija los precios aspas de nivel](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)

6. En el módulo de **configuración** , se cambia el **Nivel de precios** a **estándar** y se muestra el cuadro de **rendimiento (RU/s)** con un valor predeterminado de 400. Haga clic en **Aceptar** para guardar los cambios. 

    > [AZURE.NOTE] Puede establecer el rendimiento entre 400 y 10.000 [solicitar unidades](../articles/documentdb/documentdb-request-units.md)segundo (RU/s). **Resumen de precios** en la parte inferior de la página se actualiza automáticamente para proporcionar una estimación del costo mensual.
    
    ![Captura de pantalla de la hoja de configuración que muestra cómo cambiar el valor de rendimiento](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)

8. En el módulo de **base de datos** , puede comprobar el rendimiento de la colección sobrealimentado. 

    ![Captura de pantalla del módulo de base de datos con la colección modificada](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

Para obtener más información acerca de los cambios relacionados con la velocidad predefinida y definidas por el usuario, consulte la entrada de blog [DocumentDB: todo lo que necesita saber sobre el uso de las nuevas opciones de precios](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/).

## <a name="next-steps"></a>Pasos siguientes

Si determina que necesita más de rendimiento (mayor que 10.000 RU/s) o más almacenamiento (más de 10GB) puede crear una colección de particiones. Para crear una colección dividida, vea [crear una colección](documentdb-create-collection.md).