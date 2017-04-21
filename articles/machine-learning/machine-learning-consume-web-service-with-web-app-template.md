<properties
    pageTitle="Utilizar un servicio web de aprendizaje del equipo con una plantilla de aplicación web | Microsoft Azure"
    description="Usar una plantilla de aplicación web de Azure Marketplace para consumir un servicio web predictiva en Azure el aprendizaje."
    keywords="aprendizaje de máquina de servicio Web, operationalization, API de REST"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="garye;raymondl"/>

# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Utilizar un servicio web de aprendizaje del equipo de Azure con una plantilla de aplicación web

>[AZURE.NOTE] Este tema describe técnicas aplicables a un servicio web clásico. 

Una vez ha desarrollado su modelo predictivo e implementado como un servicio web Azure mediante Studio de aprendizaje del equipo o con herramientas como R o Python, puede obtener acceso al modelo de operationalized con la API de REST.

Hay varias maneras de utilizar la API de REST y tener acceso al servicio web. Por ejemplo, puede escribir una aplicación en C#, R o Python utilizando el código de ejemplo que se genera cuando se implementa el servicio web (disponible en la página de Ayuda de la API en el panel de servicio web en equipo aprendizaje Studio). O bien, puede usar el libro de Microsoft Excel de ejemplo creado por usted (también está disponible en el panel de servicio web en Studio).

Pero la forma más rápida y fácil tener acceso al servicio web es a través de las plantillas de aplicación Web disponibles en la [Aplicación de Web de Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>El equipo de Azure plantillas de aplicaciones Web de aprendizaje

Las plantillas de la aplicación web disponibles en el catálogo de soluciones de Azure pueden crear una aplicación web personalizada que conozca los datos de entrada y los resultados esperados de su servicio web. Todo lo que debe hacer es conceder el acceso de la aplicación web al servicio web y los datos y hace el resto de la plantilla.

Existen dos plantillas:

- [Plantilla de aplicación de respuesta de solicitud de m Azure servicio Web](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Plantilla de aplicación de Azure lote m ejecución servicio Web](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Cada plantilla crea una aplicación de ASP.NET de ejemplo con la API URI y la clave para el servicio web e implementa como un sitio web para Azure. La plantilla de respuesta de solicitud de servicio (RR) crea una aplicación web que le permite enviar una sola fila de datos al servicio web para obtener un resultado único. La plantilla de servicio de ejecución de proceso por lotes (BES) crea una aplicación web que le permite enviar muchas filas de datos para obtener los resultados de varias.

Codificación no es necesario para usar estas plantillas. Solo proporciona la API URI y clave y la aplicación para basa la plantilla.

## <a name="how-to-use-the-request-response-service-rrs-template"></a>Cómo usar la plantilla de respuesta de solicitud de servicio (RR)

Una vez que se ha implementado el servicio web, puede seguir los pasos siguientes para utilizar la plantilla de aplicación web de RR, tal como se muestra en el diagrama siguiente.

![Proceso para usar la plantilla de web RR][image1]

1. En Studio de aprendizaje del equipo, abra la pestaña **Servicios Web** y, a continuación, abra el servicio web que desea tener acceso. Copie la clave que aparece en la **clave API** y guárdelo.

    ![Clave API][image3]

2. Abra la página de Ayuda de la API de **Solicitud y respuesta** . En la parte superior de la página de ayuda, en la **solicitud**, copie el valor de **URI de la solicitud** y guárdelo. Este valor tendrá este aspecto:

        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

    ![URI de la solicitud][image4]

3. Vaya al [portal de Azure](https://portal.azure.com), **Inicio de sesión**, haga clic en **nuevo**, busque y seleccione **Azure m respuesta de solicitud de servicio Web App**y luego haga clic en **crear**. 

    - Dé un nombre único de la aplicación web. La dirección URL de la aplicación web será este nombre seguido de `.azurewebsites.net.` por ejemplo,`http://carprediction.azurewebsites.net.`

    - Seleccione la suscripción de Azure y servicios en la que se está ejecutando el servicio web.

    - Haga clic en **crear**.

    ![Crear aplicaciones web][image5]

4. Cuando haya terminado Azure implementar la aplicación web, haga clic en la **dirección URL** en la página de configuración de aplicación web en Azure o escriba la dirección URL en un explorador web. Por ejemplo,`http://carprediction.azurewebsites.net.`

5. Cuando la aplicación web se ejecuta en primer lugar le pedirá la **Dirección URL de publicación de API** y la **Clave API**.
Escriba los valores que ha guardado anteriormente:
    - **Solicitar URI** de la página de Ayuda de la API de la **dirección URL de la API de publicación**
    - **Clave API** desde el panel de servicio web para la **Clave API**.

    Haga clic en **Enviar**.

    ![Escriba publicación URI y clave API][image6]

6. La aplicación web muestra su página de **Configuración de aplicación Web** con la configuración actual del servicio web. Aquí puede realizar cambios en la configuración de la aplicación web.

    > [AZURE.NOTE] Cambiar la configuración de aquí únicamente la cambia para esta aplicación web. No cambia la configuración predeterminada del servicio web. Por ejemplo, si cambia la **Descripción** aquí no cambia la descripción que se muestra en el panel de servicio web en Studio de aprendizaje del equipo.

    Cuando haya terminado, haga clic en **Guardar cambios**y, a continuación, haga clic en **Ir a página principal**.

7. Desde la página de inicio, puede introducir valores para enviar al servicio web, haga clic en **Enviar**y se devolverá el resultado.

Si desea volver a la página de **configuración** , vaya a la `setting.aspx` página de la aplicación web. Por ejemplo: `http://carprediction.azurewebsites.net/setting.aspx.` le pedirá que vuelva a escribir la clave API, tiene que tener acceso a la página y actualizar la configuración.

Puede detener, reiniciar o eliminar la aplicación web en el portal de Azure como cualquier otra aplicación web. Mientras se está ejecutando puede vaya a la dirección web de inicio y escriba los nuevos valores.

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>Cómo usar la plantilla de servicio de ejecución de proceso por lotes (BES)

Puede usar la plantilla de aplicación web de BES de la misma manera que la plantilla RR, salvo que la aplicación web que se crea le permitirá varias filas de datos de envío y recepción varios resultados.

Los resultados de un servicio web de ejecución de lote se almacenan en un contenedor de almacenamiento de Azure; los valores de entrada pueden provenir de almacenamiento de Azure o un archivo local.
Por lo tanto, necesitará un contenedor de almacenamiento de Azure para almacenar los resultados de la aplicación web y debe preparar los datos de entrada.

![Proceso para usar la plantilla de web BES][image2]

1. Siga el mismo procedimiento para crear la aplicación web de BES en cuanto a la plantilla RR, excepto:
    - Obtener el **URI de solicitud** de la página de Ayuda de la API de **Ejecución por lotes** del servicio web.
    - Vaya a la [Plantilla de aplicación Web de Azure m lote ejecución del servicio](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) para abrir la plantilla de BES en Azure Marketplace y haga clic en **Crear Web App**.

2. Para especificar dónde desea que los resultados almacenados, escriba la información del contenedor de destino en la página de inicio de aplicación web. Especifique también en la aplicación web pueda acceder a los valores de entrada, en un archivo local o en un contenedor de almacenamiento de Azure.
Haga clic en **Enviar**.

    ![Información de almacenamiento][image7]

La aplicación web mostrará una página con el estado del trabajo.
Cuando haya finalizado el trabajo recibirá la ubicación de los resultados en el almacenamiento de blobs de Windows Azure. También tiene la opción de descarga de los resultados en un archivo local.

## <a name="for-more-information"></a>Para obtener más información

Para obtener más información sobre...

- crear un ensayo de aprendizaje de máquina con Machine aprendizaje Studio, vea [crear su primer ensayo en Azure máquina aprendizaje Studio](machine-learning-create-experiment.md)

- cómo implementar el aprendizaje de máquina experimentar como un servicio web, vea [implementar un servicio web de aprendizaje del equipo de Azure](machine-learning-publish-a-machine-learning-web-service.md)

- otras maneras de obtener acceso a su servicio web, vea [cómo utilizar un servicio web de aprendizaje del equipo de Azure](machine-learning-consume-web-services.md)


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png
