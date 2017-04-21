<properties 
    pageTitle="Publicar el aprendizaje servicio a Azure Marketplace web | Microsoft Azure" 
    description="Cómo publicar el servicio Web de Azure máquina aprendizaje en Azure Marketplace" 
    services="machine-learning" 
    documentationCenter="" 
    authors="BharathS" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="bharaths"/>

# <a name="publish-azure-machine-learning-web-service-to-the-azure-marketplace"></a>Publicar el servicio Web de aprendizaje de máquina Azure en Azure Marketplace 

Azure Marketplace proporciona la capacidad de publicar servicios web de aprendizaje del equipo de Azure como pagada o liberar servicios para su consumo en los clientes externos. Este artículo proporciona una descripción general del proceso con vínculos a instrucciones para ayudarle a comenzar. Con este proceso, puede hacer sus servicios web estén disponibles para otros programadores consumir en sus aplicaciones.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview-of-the-publishing-process"></a>Información general del proceso de publicación 

Estos son los pasos para publicar un servicio web de aprendizaje del equipo de Azure en Azure Marketplace:

1. Crear y publicar un servicio de aprendizaje de petición de respuesta del equipo (RR)
2. Implementar el servicio en producción y obtener la información de extremo clave API y OData.
3. Usar la dirección URL del servicio web publicados para publicar en [Azure Marketplace (Data Market)](https://publish.windowsazure.com/workspace/) 
4. Una vez enviado, se revisa la oferta y necesita se aprueben antes de sus clientes puede comenzar que adquirirlo. El proceso de publicación puede tardar unos días laborables. 

## <a name="walk-through"></a>Recorrer
###<a name="step-1-create-and-publish-a-machine-learning-request-response-service-rrs"></a>Paso 1: Crear y publicar un servicio de aprendizaje de petición de respuesta del equipo (RR)###
 Si no se ha hecho todavía, dedique un vistazo a esta [recorra](machine-learning-walkthrough-5-publish-web-service.md).

###<a name="step-2-deploy-the-service-to-production-and-obtain-the-api-key-and-odata-endpoint-information"></a>Paso 2: Implementar el servicio en producción y obtener la clave de API y la información de extremo de OData###
1. Desde el [Portal de Azure clásica](http://manage.windowsazure.com), seleccione la opción de **Aprendizaje** de la barra de navegación izquierda y seleccione el área de trabajo. 

2. Haga clic en la pestaña **Servicios WEB** y seleccione el servicio web que desea publicar en el catálogo de soluciones.

    ![Azure Marketplace][workspace]

3. Seleccione el extremo que le gustaría tener el catálogo de soluciones consumir. Si no ha creado ningún extremos adicionales, puede seleccionar el extremo **predeterminado** .

4. Una vez que haya hecho clic en el extremo, podrá ver la **Clave API**. Necesitará este fragmento de información más adelante en el paso 3, así que asegúrese de una copia de la misma.

    ![Azure Marketplace][apikey]

5. Haga clic en el método de **Solicitud/respuesta** , en este momento que no se admiten la publicación de servicios de ejecución de lote al catálogo de soluciones. Que le llevará a la página de Ayuda de la API para el método de solicitud y respuesta.

6. Copie la **Dirección de extremo de OData**, necesitará esta información más adelante en el paso 3.

    ![Azure Marketplace][odata]




implementar el servicio en producción.



###<a name="step-3-use-the-url-of-the-published-web-service-to-publish-to-azure-marketplace-datamarket"></a>Paso 3: Usar la dirección URL del servicio web publicados para publicar en Azure Marketplace (DataMarket)###

1.  Vaya a [Azure Marketplace (Data Market)](http://datamarket.azure.com/home) 
2.  Haga clic en el vínculo de la **publicación** en la parte superior de la página. Esto le llevará al [Portal de publicación de Microsoft Azure](https://publish.windowsazure.com)
3.  Haga clic en la sección **editores** registrar como un editor.
4.  Al crear una oferta de nueva, seleccione los **Servicios de datos**, haga clic en **crear un nuevo servicio de datos**. 
 
    ![Azure Marketplace][image1]

    <br />


5.  En **los planes** proporcionan información sobre la oferta, incluido un plan de precio. Decida si le ofrecerá un servicio de pago o gratuito. Para obtener pagado, proporcionar información de pago como su información bancaria y de impuestos.

6.  En **Marketing** proporcione información sobre la oferta, como el título y una descripción para la oferta.

7.  En **precios** puede establecer el precio para sus planes para países específicos o permitir que el sistema "autoprice" de la oferta.

8. En la pestaña de **Servicio de datos** , haga clic en **Servicio Web** como **Origen de datos**.

    ![Azure Marketplace][image2]

9.  Obtener la clave de API de dirección URL del servicio web desde el Portal de clásico de Azure, como se explica en el paso 2 anterior.

10. En el cuadro de diálogo Configuración de servicio de datos del catálogo de soluciones, pegue la dirección de extremo de OData en el cuadro de texto de la **Dirección URL del servicio** .

11. Para la **autenticación**, elija **encabezado** como el **Esquema de autenticación**.

    - Escriba el **nombre de encabezado**"autorización".
    - El **Valor del encabezado**, escriba "Portador" (sin comillas), haga clic en la barra **ESPACIADORA** y, a continuación, pegue la clave de API.
    - Seleccione la casilla de verificación **este servicio OData** .
    - Haga clic en **Probar conexión** para probar la conexión.

12. En **categorías**, asegúrese de que está seleccionado **El aprendizaje** .

13. Cuando termine de escribir todos los metadatos acerca de la oferta, haga clic en **Publicar**y, a continuación, **ensayo de inserción**. En este momento, se le notificará de cualquier problema restante que necesita para la corrección.

14. Después de comprobar la finalización de todos los problemas pendientes, haga clic en **Solicitar aprobación para poner en producción**. El proceso de publicación puede tardar unos días laborables. 


[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 
