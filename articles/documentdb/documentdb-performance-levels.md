<properties
    pageTitle="Niveles de rendimiento en DocumentDB | Microsoft Azure"
    description="Obtenga información sobre cómo los niveles de rendimiento en DocumentDB le permiten reservar rendimiento en cada colección por."
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
    ms.date="08/26/2016"
    ms.author="mimig"/>

# <a name="performance-levels-in-documentdb"></a>Niveles de rendimiento en DocumentDB

Este artículo proporciona una descripción general de los niveles de rendimiento en [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

Después de leer este artículo, podrá responder a las preguntas siguientes:  

-   ¿Qué es un nivel de rendimiento?
-   ¿Cómo se reserva el rendimiento de una cuenta de base de datos?
-   ¿Cómo funcionan con niveles de rendimiento?
-   ¿Cómo me factura niveles de rendimiento?

## <a name="introduction-to-performance-levels"></a>Introducción a los niveles de rendimiento

Cada colección de DocumentDB creada con una cuenta estándar se aprovisiona con un nivel de rendimiento asociado. Cada colección en una base de datos puede tener un nivel de rendimiento diferentes que permite designar más rendimiento para colecciones de acceso más frecuente y menos el rendimiento para colecciones accede con poca frecuencia. DocumentDB es compatible con dos niveles de rendimiento definidas por el usuario y predefinida niveles de rendimiento.  

Cada nivel de rendimiento tiene un límite de velocidad asociadas [unidad de solicitud (RU)](documentdb-request-units.md) . Este es el rendimiento que se reservado para una colección según su nivel de rendimiento y está disponible para su uso en esa colección exclusivamente.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p>Detalles</p></td>
            <td valign="top"><p>Límites de rendimiento</p></td>
            <td valign="top"><p>Límites de almacenamiento</p></td>
            <td valign="top"><p>Versión</p></td>
            <td valign="top"><p>API</p></td>            
        </tr>
        <tr>
            <td valign="top"><p>Rendimiento definidas por el usuario</p></td>
            <td valign="top"><p>Almacenamiento de uso medido según el uso de GB.</p><p>Rendimiento en unidades de 100 RU/s</p></td>
            <td valign="top"><p>Ilimitado. 400 - 250.000 solicitar unidades/s predeterminada (superior a petición)</p></td>
            <td valign="top"><p>Ilimitado. 250 GB (superior a petición) de forma predeterminada </p></td>
            <td valign="top"><p>V2</p></td>
            <td valign="top"><p>API de 2015-12-16 y más reciente</p></td>  
        </tr>
        <tr>
            <td valign="top"><p>Rendimiento predefinido</p></td>
            <td valign="top"><p>Almacenamiento de 10 GB reservado.</p><p>S1 = s 250 RU, S2 = 1000 RU/s, S3 = 2500 RU/s</p></td>
            <td valign="top"><p>2500 RU/s</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>V1</p></td>
            <td valign="top"><p>Cualquier</p></td>  
        </tr>        
    </tbody>
</table>                


DocumentDB permite un amplio conjunto de operaciones de base de datos como consultas, procedimientos con funciones definidas por el usuario (UDF), las consultas almacenadas y desencadenadores. El coste de procesamiento asociado a cada una de estas operaciones varían en función de la CPU, IO y la memoria necesaria para completar la operación. En su lugar de pensar y administración de recursos de hardware, puede considerar que una unidad de solicitud como una medida de los recursos necesitan para realizar diversas operaciones de base de datos y una solicitud de aplicación de servicio.

Colecciones pueden crearse a través del [portal de Microsoft Azure](https://portal.azure.com), la [API de REST](https://msdn.microsoft.com/library/azure/mt489078.aspx) o cualquiera de los [SDK DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Las APIs DocumentDB le permiten especificar el nivel de rendimiento de una colección.

> [AZURE.NOTE] Puede ajustar el nivel de rendimiento de una colección mediante las API o el [portal de Microsoft Azure](https://portal.azure.com/). Los cambios de nivel de rendimiento se lo esperados en completarse dentro de 3 minutos.

## <a name="setting-performance-levels-for-collections"></a>Establecer los niveles de rendimiento de colecciones
Una vez que se crea una colección, la asignación completa de RUs basándose en el nivel de rendimiento designado están reservados para la colección.

Tenga en cuenta que, con dos niveles de rendimiento predefinidos y definidos por el usuario, DocumentDB opera basándose en reserva de rendimiento. Mediante la creación de una colección de una aplicación ha reservado y rendimiento reservado independientemente de la cantidad de que el rendimiento se usen factura. Con los niveles de rendimiento definidas por el usuario, almacenamiento se mide en función de consumo, pero con niveles de rendimiento predefinidos, 10 GB de almacenamiento está reservado en el momento de la creación de la colección.  

Después de crean colecciones, puede modificar el nivel de rendimiento a través de los SDK DocumentDB o a través del Portal de clásico de Azure.

> [AZURE.IMPORTANT] Colecciones de DocumentDB estándar se cargarán a una tasa por horas y se cargará cada colección que crea un mínimo de una hora de uso.

Si se ajusta el nivel de rendimiento de una colección dentro de una hora, se le facturará el más alto nivel de rendimiento durante la hora. Por ejemplo, si aumentar el nivel de rendimiento para una colección a 8:53 a.m. se le cobrará el nuevo nivel de inicio a 8:00 a.m.. Del mismo modo, si disminuye el nivel de rendimiento a 8:53 a.m., se aplicará la nueva tasa a 9:00 a.m..

Unidades de solicitud están reservadas para cada colección basada en el conjunto de nivel de rendimiento. Consumo de unidades de la solicitud se evalúa como un frecuencia por segundo. Aplicaciones que superen la tasa de unidad de solicitud de preparación (o el nivel de rendimiento) en una colección de reducirá hasta que la tasa cae por debajo del nivel reservado para esa colección. Si su aplicación requiere un nivel de rendimiento, puede aumentar el nivel de rendimiento para cada colección.

> [AZURE.NOTE] Cuando la aplicación supera los niveles de rendimiento de una o varias colecciones, reducirá las solicitudes de forma por colección. Esto significa que pueden tener éxito algunas solicitudes de aplicación mientras otras personas pueden ser acelerados. Se recomienda agregar un pequeño número de reintentos cuando acelerado para controlar picos de tráfico de la solicitud.

## <a name="working-with-performance-levels"></a>Trabajar con niveles de rendimiento
Colecciones de DocumentDB le permiten agrupar los datos según las necesidades de rendimiento de la aplicación y los patrones de consulta. Con de DocumentDB indización automática y soporte técnico de la consulta, es bastante habitual colocar documentos heterogéneos en la misma colección. Incluyen las consideraciones clave para decidir si se deben usar colecciones independientes:

- Consultas: una colección es el ámbito de ejecución de la consulta. Si necesita hacer consultas en un conjunto de documentos, los patrones de lectura más eficaces proceden de colocar documentos en una sola colección.
- Transacciones: todas las transacciones se aplican a dentro de una sola colección. Si tiene los documentos que se deben actualizar en un único procedimiento almacenado o desencadenador, se almacenarán en la misma colección. Más concretamente, una clave de partición dentro de una colección es el límite de la transacción. Para obtener más detalles, consulte [particiones en DocumentDB](documentdb-partition-data.md) .
- Aislamiento de rendimiento: una colección tiene un nivel de rendimiento asociado. Esto garantiza que cada colección tiene un rendimiento predecible mediante RUs reservados. Datos pueden asignarse a distintos grupos, con los niveles de rendimiento diferentes, en función de la frecuencia de acceso.

> [AZURE.IMPORTANT] Es importante comprender que se cargará a tasas estándar completo en función del número de colecciones de creado por la aplicación.

Se recomienda que la aplicación hace uso de un pequeño número de colecciones a menos que tenga requisitos de almacenamiento o rendimiento grandes. Asegúrese de que ha comprendido bien modelos de aplicación para la creación de nuevas colecciones. Puede elegir reservar la creación de la colección como una acción de administración administran fuera de la aplicación. Del mismo modo, ajuste el nivel de rendimiento para una colección de cambiará la tasa por hora en que se factura la colección. Si la aplicación ajusta estos dinámicamente debe supervisar niveles de rendimiento de la colección.

## <a id="changing-performance-levels-using-the-azure-portal"></a>Cambiar de S1, S2, S3 a rendimiento definidas por el usuario

Siga estos pasos para cambiar de uso de los niveles de rendimiento predefinidos para niveles de rendimiento definidas por el usuario en el portal de Azure. Utilizar los niveles de rendimiento definidas por el usuario, puede adaptar su rendimiento a sus necesidades. Y si aún se está utilizando una cuenta de S1, puede aumentar el rendimiento de predeterminado de 250 RU/s a 400 RU/s con unos pocos clics.

Para obtener más información sobre el rendimiento de cambios relacionados con predefinidas y definidas por el usuario precios, consulte la entrada de blog [DocumentDB: todo lo que necesita saber sobre el uso de las nuevas opciones de precios](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/).

> [AZURE.VIDEO changedocumentdbcollectionperformance]

1. En el explorador, vaya al [**portal de Azure**](https://portal.azure.com).
2. Haga clic en **Examinar** -> **DocumentDB cuentas**y, a continuación, seleccione la cuenta DocumentDB para modificar.   
3. En la lente de **bases de datos** , seleccione la base de datos para modificar y, a continuación, en el módulo de **base de datos** , seleccione la colección de escritura. Cuentas con rendimiento predefinido tienen un nivel de precios de S1, S2 o S3.

      ![Captura de pantalla del módulo de base de datos con una colección de S1](./media/documentdb-performance-levels/documentdb-change-performance-S1.png)

4. En el módulo de **colecciones** , haga clic en **más**, a continuación, la **configuración** en la barra superior.   
5. En el módulo de **configuración** , haga clic en el **Nivel de precios** y observe que la estimación de coste mensual para cada plan se muestra en el módulo de **Elegir el nivel de precios** . Para cambiar el rendimiento definidas por el usuario, haga clic en **estándar**y, a continuación, haga clic en **Seleccionar** para guardar el cambio.

      ![Captura de pantalla de la configuración de DocumentDB y elija los precios aspas de nivel](./media/documentdb-performance-levels/documentdb-change-performance.png)

6. En el módulo de **configuración** , se cambia el **Nivel de precios** a **estándar** y se muestra el cuadro de **rendimiento (RU/s)** con un valor predeterminado de 400. Establecer el rendimiento entre 400 y 10.000 [solicitar unidades](documentdb-request-units.md)segundo (RU/s). **Resumen de precios** en la parte inferior de la página se actualiza automáticamente para proporcionar una estimación del costo mensual. Haga clic en **Aceptar** para guardar los cambios.

    ![Captura de pantalla de la hoja de configuración que muestra cómo cambiar el valor de rendimiento](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

7. En el módulo de **base de datos** , puede comprobar el rendimiento de la colección de nuevo.

    ![Captura de pantalla del módulo de base de datos con la colección modificada](./media/documentdb-performance-levels/documentdb-change-performance-confirmation.png)

Si determina que necesita más de rendimiento (mayor que 10.000 RU/s) o más almacenamiento (más de 10GB) puede crear una colección de particiones. Para crear una colección dividida, vea [crear una colección](documentdb-create-collection.md).

>[AZURE.NOTE] Cambiar los niveles de rendimiento de una colección puede tardar hasta 2 minutos.

## <a name="changing-performance-levels-using-the-net-sdk"></a>Cambiar los niveles de rendimiento mediante el SDK de .NET

Otra opción para cambiar el nivel de rendimiento de las colecciones es a través de nuestro SDK. Esta sección trata solo cambiar nivel de rendimiento de la colección mediante nuestra [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx), pero el proceso es similar para nuestra otros [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Si está acostumbrado a nuestra .NET SDK, visite nuestro [tutorial de introducción](documentdb-get-started.md).

Esto es un fragmento de código para cambiar el rendimiento de la oferta a 50.000 unidades de solicitud por segundo:

    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);

    // Set the throughput to S2
    offer = new Offer(offer);
    offer.OfferType = "S2";

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);



> [AZURE.NOTE] Colecciones aprovisionadas con 10.000 en unidades de solicitud por segundo se pueden migrar entre las ofertas con rendimiento definidas por el usuario y rendimiento predefinido (S1, S2, S3) en cualquier momento. Colecciones que se aprovisiona con encima de 10.000 unidades de solicitud por segundo no se puede convertir a niveles de rendimiento predefinidos.

Visite [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) para ver más ejemplos y obtener más información sobre nuestros métodos de oferta:

- [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
- [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
- [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
- [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

## <a id="change-throughput"></a>Cambiar el rendimiento de una colección

Si ya está usando el rendimiento definidas por el usuario, puede cambiar el rendimiento de la colección mediante el procedimiento siguiente. Si necesita cambiar de un nivel de rendimiento S1, S2 o S3 (rendimiento predefinido) a rendimiento definidas por el usuario, vea [cambiar de S1, S2, S3 rendimiento definidas por el usuario](#changing-performance-levels-using-the-azure-portal).

1. En el explorador, vaya al [**portal de Azure**](https://portal.azure.com).
2. Haga clic en **Examinar** -> **DocumentDB cuentas**y, a continuación, seleccione la cuenta DocumentDB para modificar.   
3. En el módulo **DocumentDB cuenta** , en la lente de **bases de datos** , seleccione la base de datos para modificar y, a continuación, en el módulo de **base de datos** , seleccione la colección de escritura.
4. En el módulo de **colecciones** , haga clic en **configuración** en la barra superior.   
5. En el módulo de **configuración** , aumente el valor en el cuadro de **rendimiento (RU/s)** y, a continuación, haga clic en **Aceptar** para guardar el cambio. **Resumen de precios** en la parte inferior del módulo se actualiza para mostrar el nuevo coste mensual estimado de esa colección en una única región.

    ![Captura de pantalla de la hoja de configuración resaltado en el cuadro de rendimiento y resumen de precios](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

Si no sabe cuánto para aumentar el rendimiento, consulte [Estimating rendimiento necesita](documentdb-request-units.md#estimating-throughput-needs) y la [solicitud de calculadora de unidades](https://www.documentdb.com/capacityplanner).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre precios y administración de datos con DocumentDB de Azure, explore estos recursos:

- [Precios DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
- [Administrar la capacidad de DocumentDB](documentdb-manage.md)
- [Modelado de datos en DocumentDB](documentdb-modeling-data.md)
- [Partición de datos en DocumentDB](documentdb-partition-data.md)
- [Unidades de solicitud](http://go.microsoft.com/fwlink/?LinkId=735027)

Para obtener más información sobre DocumentDB, consulte la [documentación](https://azure.microsoft.com/documentation/services/documentdb/)de Azure DocumentDB.

Para empezar con escala y pruebas con DocumentDB de rendimiento, consulte [rendimiento y escala pruebas con DocumentDB de Azure](documentdb-performance-testing.md).

[1]: ./media/documentdb-performance-levels/documentdb-change-collection-performance7-9.png
[2]: ./media/documentdb-performance-levels/documentdb-change-collection-performance10-11.png
