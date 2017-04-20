<properties 
    pageTitle="Tutorial: Supervisar Microsoft Dynamics CRM con recomendaciones de aplicación" 
    description="Obtén telemetría de Microsoft Dynamics CRM Online con información de la aplicación. Tutorial de la instalación de obtención de datos, visualización y exportar." 
    services="application-insights" 
    documentationCenter=""
    authors="mazharmicrosoft" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="awills"/>
 
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Tutorial: Habilitar telemetría para Microsoft Dynamics CRM Online con información de la aplicación

En este artículo muestra cómo obtener datos de telemetría de [Microsoft Dynamics CRM Online](https://www.dynamics.com/) con [Perspectivas de aplicación de Visual Studio](https://azure.microsoft.com/services/application-insights/). Le guiaremos a través del proceso completo de la adición de secuencias de comandos de aplicación perspectivas a la aplicación captura los datos y la visualización de datos.

>[AZURE.NOTE] [Busque la solución de ejemplo](https://dynamicsandappinsights.codeplex.com/).

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Agregar perspectivas de aplicación a la instancia de CRM Online nueva o existente 

Para supervisar la aplicación, agregue un SDK perspectivas de aplicación a la aplicación. El SDK envía telemetría el [portal de información de la aplicación](https://portal.azure.com), donde puede usar nuestro análisis eficaces y herramientas de diagnóstico o exporte los datos al almacenamiento.

### <a name="create-an-application-insights-resource-in-azure"></a>Crear un recurso de aplicación perspectivas en Azure

1. Obtener [una cuenta de Microsoft Azure](http://azure.com/pricing). 
2. Inicie sesión en el [portal de Azure](https://portal.azure.com) y agregue un nuevo recurso de información de la aplicación. Esto es donde se procesa y muestra los datos.

    ![Haga clic en +, Developer Services, perspectivas de aplicación.](./media/app-insights-sample-mscrm/01.png)

    Elija ASP.NET como el tipo de aplicación.

3. Abra la ficha Inicio rápido y la secuencia de comandos de código.

    ![](./media/app-insights-sample-mscrm/03.png)

**Mantener abierta la página de códigos** mientras realice el siguiente paso en otra ventana del explorador. Pronto tendrá el código. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Crear un recurso de web JavaScript en Microsoft Dynamics CRM

1. Abra su instancia CRM Online e inicie sesión con privilegios de administrador.
2. Abrir Microsoft Dynamics CRM configuración, personalización, personalizar el sistema

    ![](./media/app-insights-sample-mscrm/04.png)
    
    ![](./media/app-insights-sample-mscrm/05.png)


    ![](./media/app-insights-sample-mscrm/06.png)

3. Crear un recurso de JavaScript.

    ![](./media/app-insights-sample-mscrm/07.png)

    Asigne un nombre, seleccione **Script (JScript)** y abra el editor de texto.

    ![](./media/app-insights-sample-mscrm/08.png)
    
4. Copie el código de aplicación perspectivas. Al copiar Asegúrese de pasar por alto las etiquetas de script. Consulte a continuación la captura de pantalla:

    ![](./media/app-insights-sample-mscrm/09.png)

    El código incluye la clave de instrumentación que identifica el recurso de información de aplicación.

5. Guardar y publicar.

    ![](./media/app-insights-sample-mscrm/10.png)

### <a name="instrument-forms"></a>Formularios de instrumento

1. En Microsoft CRM Online, abra el formulario de cuenta

    ![](./media/app-insights-sample-mscrm/11.png)

2. Abrir el formulario de propiedades

    ![](./media/app-insights-sample-mscrm/12.png)

3. Agregar recurso web JavaScript que ha creado

    ![](./media/app-insights-sample-mscrm/13.png)

    ![](./media/app-insights-sample-mscrm/14.png)

4. Guardar y publicar las personalizaciones de formulario.


## <a name="metrics-captured"></a>Métricas capturadas

Ahora ha configurado captura de telemetría para el formulario. Siempre que se utiliza, los datos se enviarán a su recurso de información de la aplicación.

A continuación se ofrecen ejemplos de los datos que verá.

#### <a name="application-health"></a>Estado de la aplicación

![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

Excepciones de explorador:

![](./media/app-insights-sample-mscrm/17.png)

Haga clic en el gráfico para obtener más detalles:

![](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Uso

![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Exploradores compatibles

![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Ubicación geográfica

![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Solicitud de vista de página interno

![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Código de ejemplo

[Busque el código de ejemplo](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI

Puede realizar un análisis más profundo incluso si [Exportar datos a Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Solución de ejemplo Microsoft Dynamics CRM

[Aquí es la solución de ejemplo que se ha implementado en Microsoft Dynamics CRM] (https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Aprende más

* [¿Qué es la información de la aplicación?](app-insights-overview.md)
* [Información de la aplicación para las páginas web](app-insights-javascript.md)
* [Más ejemplos y tutoriales](app-insights-code-samples.md)

 
