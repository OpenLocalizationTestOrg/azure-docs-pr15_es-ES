<properties
    pageTitle="Panel de Power BI en secuencia análisis | Microsoft Azure"
    description="Usar un panel de Power BI transmisiones en tiempo real para recopilar inteligencia empresarial y analizar datos de gran volumen de trabajo de análisis de la secuencia."
    keywords="panel de análisis de panel en tiempo real"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

#  <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Transmita análisis y Power BI: un panel de análisis en tiempo real para la transmisión de datos

Análisis de secuencia de Azure le permite aprovechar las ventajas de una de las principales herramientas de inteligencia empresarial, Microsoft Power BI. Obtenga información sobre cómo usar el análisis de secuencia de Azure para analizar datos y obtener los conocimientos en un panel de análisis de Power BI en tiempo real de gran volumen, transmisión por secuencias.

Utilizar [Microsoft Power BI](https://powerbi.com/) para crear un panel en vivo rápidamente. [Vea un vídeo que ilustra el escenario](https://www.youtube.com/watch?v=SGUpT-a99MA).

En este artículo, obtenga información sobre cómo crear sus propias herramientas de inteligencia empresarial personalizada con Power BI como salida para los trabajos de análisis de secuencia de Azure y utilizar un panel en tiempo real.

## <a name="prerequisites"></a>Requisitos previos

* Cuenta de Microsoft Azure
* Entrada para el trabajo de análisis de secuencia consumir la transmisión de datos de. Análisis de secuencia acepta la entrada de almacenamiento de Azure evento Hubs o blobs de Windows Azure.  
* Cuenta profesional o educativa para Power BI

## <a name="create-azure-stream-analytics-job"></a>Crear tarea de análisis de secuencia de Azure

Desde el [Portal de Azure clásica](https://manage.windowsazure.com), haga clic en **nuevo, servicios de datos, el análisis de secuencia, crear rápido**.

Especifique los valores siguientes, a continuación, haga clic en **crear análisis de flujo de trabajo**:

* **Nombre del trabajo** : escriba un nombre de trabajo. Por ejemplo, **DeviceTemperatures**.
* **Región** : seleccione la región donde desea que el trabajo que se encuentra. Considere la posibilidad de realizar el trabajo y el concentrador de evento en la misma región para garantizar un rendimiento óptimo y evitar provocar los costes de transferencia de datos entre regiones.
* **Cuenta de almacenamiento** , elija la cuenta de almacenamiento que desea usar para almacenar los datos del seguimiento de todos los trabajos de análisis de secuencia ejecutando dentro de esta área. Tiene la opción de elegir una cuenta de almacenamiento existente o cree uno nuevo.

En el panel izquierdo para enumerar los trabajos de análisis de secuencia, haga clic en **Análisis de secuencia** .

![Gráfico1][graphic1]

> [AZURE.TIP] El nuevo trabajo se mostrará con el estado **No comenzada**. Observe que el botón **Inicio** en la parte inferior de la página está deshabilitado. Este es el comportamiento esperado como debe configurar la entrada de trabajo, resultados, consulta, y así sucesivamente antes de empezar el trabajo.

## <a name="specify-job-input"></a>Especificar la entrada de trabajo

Para este tutorial, suponemos que usa concentrador de evento como una entrada con serialización JSON y codificación UTF-8.

* Haga clic en el nombre del trabajo.
* Haga clic en **entradas** de la parte superior de la página y, a continuación, haga clic en **Agregar entrada**. El cuadro de diálogo que se abre le guiará a través de un número de pasos para configurar la entrada.
*   Seleccione el **flujo de datos**y, a continuación, haga clic en el botón derecho.
*   Seleccione **Hub de evento**y, a continuación, haga clic en el botón derecho.
*   Escriba o seleccione los valores siguientes en la tercera página:
  * **Alias de entrada** : escriba un nombre descriptivo para este trabajo de entrada. Tenga en cuenta que va a utilizar este nombre en la consulta más adelante.
  * **Evento concentrador** - si el concentrador de eventos que creó en la misma suscripción como el trabajo de análisis de secuencia, seleccione el espacio de nombres que en el hub de evento.
*   Si es su centro de evento en una suscripción diferente, seleccione **Usar evento concentrador de otra suscripción** e introducir manualmente información para **Namespace de Bus de servicio**, **El nombre del concentrador de evento**, **Nombre de la directiva de evento concentrador**, **Clave de directiva de concentrador de evento**y **Evento concentrador partición recuento**.

> [AZURE.NOTE]  Este ejemplo utiliza el número predeterminado de particiones, 16.

* **Nombre del evento concentrador** : seleccione el nombre del concentrador de evento de Azure tiene.
* **Nombre de la directiva de evento concentrador** : seleccione la directiva de concentrador de evento para el concentrador de evento que está utilizando. Asegúrese de que esta directiva se administrar permisos.
*   **Grupo de consumidores de concentrador de eventos** : puede dejarlo vacío o especifique un grupo de consumidor tiene en su centro de evento. Observe que cada grupo de consumidores de un concentrador de evento puede tener los lectores solo 5 a la vez. Por lo tanto, decida el grupo consumidor adecuada para su trabajo según corresponda. Si deja el campo en blanco, usará el grupo de consumidor predeterminado.

*   Haga clic en el botón derecho.
*   Especifique los valores siguientes:
  * **Evento serializador formato** - JSON
  * **Codificación** - UTF8
*   Haga clic en el botón Comprobar para agregar esta fuente y compruebe que el análisis de secuencia puede conectarse correctamente al evento concentrador.

## <a name="add-power-bi-output"></a>Agregar el resultado de Power BI

1.  Haga clic en **resultados** de la parte superior de la página y, a continuación, haga clic en **Agregar resultado**. Verá que Power BI se enumera como una opción de resultado.

    ![Gráfico2][graphic2]  

2.  Seleccione **Power BI** y, a continuación, haga clic en el botón derecho.
3.  Verá una pantalla como la siguiente:

    ![graphic3][graphic3]  

4.  En este paso, proporcionar una cuenta profesional o educativa para la salida de trabajo de análisis de la secuencia. Si ya tiene cuenta de Power BI, seleccione **Autorizar ahora**. Si no es así, elija **Regístrese ahora**. [Esto es un buen blog algunos detalles de Power BI registrarse](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).

    ![graphic11][graphic11]  

5.  A continuación verá una pantalla como la siguiente:

    ![graphic4][graphic4]  

Proporcionar valores como sigue:

* **Alias de salida** : se puede incluir cualquier alias de salida que sea fácil de hacer referencia a. Este alias de salida es especialmente útil si decide tener varias salidas para su trabajo. En ese caso, debe hacer referencia a este resultado de la consulta. Por ejemplo, vamos a usar el valor de alias de salida = "OutPbi".
* **Nombre del conjunto de datos** : proporcione un nombre de conjunto de datos que desee de Power BI de salida tener. Por ejemplo, vamos a utilizar "pbidemo".
*   **Nombre de tabla** : proporcione un nombre de tabla en el conjunto de datos de la salida de Power BI. Supongamos que denominamos "pbidemo". Actualmente, los resultados de Power BI de trabajos de análisis de secuencia sólo pueden tener una tabla en un conjunto de datos.
*   **Área de trabajo** : seleccione un área de trabajo de su inquilino de Power BI en la que se creará el conjunto de datos.

>   [AZURE.NOTE] No debería explícitamente crear este conjunto de datos y tabla en su cuenta de Power BI. Se crearán automáticamente cuando se inicia el trabajo de análisis de secuencia y el trabajo inicia salida bombeo en Power BI. Si la consulta de trabajo no devuelve ningún resultado, no se creará el conjunto de datos y la tabla.

*   Haga clic en **Aceptar**, **Conexión de prueba** y ahora resultados configuración se completa.

>   [AZURE.WARNING] También tenga en cuenta que si Power BI ya tiene un conjunto de datos y una tabla con el mismo nombre que se que proporcionan en esta tarea de análisis de secuencia, se sobrescribirán los datos existentes.


## <a name="write-query"></a>Escribir consulta

Vaya a la pestaña **consulta** de su trabajo. Escribir la consulta, que el resultado de la que desee en Power BI. Por ejemplo, podría ser algo como la siguiente consulta SQL:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,1),
        dspl



Iniciar el trabajo. Validar que su centro de evento está recibiendo eventos y la consulta genera los resultados esperados. Si la consulta salidas 0 filas, tablas y conjunto de datos de Power BI no se creará automáticamente.

## <a name="create-the-dashboard-in-power-bi"></a>Crear el panel de Power BI

Vaya a [Powerbi.com](https://powerbi.com) e inicie sesión con su cuenta profesional o educativa. Si la consulta de la tarea de análisis de secuencia proporciona los resultados, verá que ya se ha creado el conjunto de datos:

![graphic5][graphic5]

Para crear el panel, vaya a la opción de paneles y cree un nuevo panel.

![graphic6][graphic6]

En este ejemplo vamos a etiqueta que "Demostración paneles".

Ahora haga clic en el conjunto de datos creado por el trabajo de análisis de secuencia (pbidemo en nuestro ejemplo actual). Se le dirigirá a una página para crear un gráfico en la parte superior este conjunto de datos. A continuación se muestra un ejemplo de los informes que puede crear:

Seleccione a Σ temp y campos de tiempo. Pasará automáticamente al valor y el eje del gráfico:

![graphic7][graphic7]

De este modo, se proporciona automáticamente un gráfico como sigue:

![graphic8][graphic8]

En la sección valor, haga clic en la lista hacia abajo para temp y elija **promedio** para la temperatura y en el gráfico, haga clic en **visualización** y elija el **gráfico de líneas**:

![graphic9][graphic9]

Ahora obtendrá un gráfico de líneas de promedio lo largo del tiempo.  Mediante la opción de pin como siguiente, puede anclar esto al panel del que creó anteriormente:

![graphic10][graphic10]

Ahora cuando se ve el panel con este informe anclada, verá informe actualización en tiempo real. Pruebe a cambiar los datos de sus eventos – temp punta o algo similar y verá el efecto en tiempo real de la que se reflejan en el panel.

Nota que este tutorial muestra cómo crear, pero un tipo de gráfico para un conjunto de datos. Power BI puede ayudarle a crear otras herramientas de inteligencia empresarial de atención al cliente para su organización. Para obtener otro ejemplo de un panel de Power BI, vea el vídeo de [Introducción a Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Para obtener más información sobre cómo configurar un resultado de Power BI y usar grupos de Power BI, revise la [sección de Power BI](stream-analytics-define-outputs.md#power-bi) de [que análisis de secuencia de descripción envía](stream-analytics-define-outputs.md "salidas de análisis de secuencia de descripción"). Otro recurso útil para obtener más información sobre la creación de paneles con Power BI es [paneles en Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="limitations-and-best-practices"></a>Prácticas recomendadas y limitaciones

Power BI emplea las restricciones de simultaneidad y rendimiento, como se describe a continuación: [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Precios de Power BI")

Debido a las Power BI entre sí más naturalmente casos donde el análisis de secuencia de Azure hace una reducción de carga de datos importantes.
Se recomienda usar TumblingWindow o HoppingWindow para asegurarse de que insertar datos sería como máximo 1 inserción por segundo y que la consulta cae en los requisitos de rendimiento, puede usar la siguiente ecuación para calcular el valor para dar a la ventana en segundos:

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Por ejemplo: si tiene 1.000 dispositivos enviar datos cada segundo, se encuentra en Power BI Pro SKU que admita 1.000.000 filas por hora y desea obtener el promedio de los datos por dispositivo en Power BI puede hacer como máximo push cada 4 segundos por dispositivo (tal como se muestra a continuación):  

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Lo que significa que se desea cambiar la consulta original para:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl

### <a name="powerbi-view-refresh"></a>Actualización de la vista de PowerBI

Una pregunta habitual es "¿por qué no el panel de la actualización automática de PowerBI?".

Para ello, en PowerBI utilizar preguntas y respuestas y plantea una pregunta como "Valor máximo por temporal donde se encuentra hoy marca de hora" y anclar ese mosaico al escritorio.

### <a name="renew-authorization"></a>Renovar autorización

Debe volver a autenticar su cuenta de Power BI si ha cambiado su contraseña desde su trabajo se creó o autenticado por última vez. Si se configura la autenticación multifactor (AMF) en su inquilino de Azure Active Directory (AAD) también debe renovar autorización de Power BI cada 2 semanas. Un síntoma de este problema es ningún resultado de trabajo y un "error de usuario de autenticar" en los registros de operación:

![graphic12][graphic12]

Del mismo modo, si una tarea intenta iniciar mientras el token ha caducado, se producirá un error y se producirá un error en el inicio del trabajo. El error tendrá un aspecto como las siguientes:

![Error de validación de PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-expire.png) 
 

Para resolver este problema, detenga el trabajo en ejecución y vaya a la salida de Power BI.  Haga clic en el vínculo "Autorización de renovar" y reiniciar el trabajo desde la última vez detenido para evitar la pérdida de datos.

![Renovación de validación de PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renew.png) 

Una vez que se actualiza la autorización con Power BI se muestra una alerta en el área de autorización verde:

![Renovación de validación de PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renewed.png) 

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png
[graphic11]: ./media/stream-analytics-power-bi-dashboard/11-stream-analytics-power-bi-dashboard.png
[graphic12]: ./media/stream-analytics-power-bi-dashboard/12-stream-analytics-power-bi-dashboard.png
[graphic13]: ./media/stream-analytics-power-bi-dashboard/13-stream-analytics-power-bi-dashboard.png
