<properties
    pageTitle="Prueba de carga de la aplicación mediante Visual Studio Team Services | Microsoft Azure"
    description="Aprenda a realizar pruebas de esfuerzo las aplicaciones de Azure servicio tela mediante Visual Studio Team Services."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>Prueba de carga de la aplicación mediante Visual Studio Team Services

Este artículo le muestra cómo usar las características de prueba de carga de Microsoft Visual Studio para realizar pruebas de esfuerzo de una aplicación. Utiliza una estructura de servicio de Azure con estado del servicio back-end y un estado del servicio web front-end. La aplicación de ejemplo que se utiliza aquí es un simulador de ubicación de avión. Proporciona un identificador de avión, hora de salida y de destino. Back-end de la aplicación procesa las solicitudes y el front-end que muestra en un mapa del avión que coincida con los criterios.

El siguiente diagrama muestra la aplicación de servicio tela que se va a probar.

![Diagrama de la aplicación de ubicación de avión de ejemplo][0]

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar, debe hacer lo siguiente:

- Obtener una cuenta de Visual Studio Team Services. Puede obtener uno gratis en [Visual Studio Team Services](https://www.visualstudio.com).
- Obtener e instalar 2013 de Visual Studio o Visual Studio de 2015. En este artículo usa Visual Studio 2015 Enterprise edition, pero deberían funcionar del mismo modo 2013 de Visual Studio y otras ediciones.
- Implementar la aplicación en un entorno de ensayo. Aprenda [a implementar aplicaciones en un clúster remoto mediante Visual Studio](service-fabric-publish-app-remote-cluster.md) para obtener información acerca de este.
- Entender el modelo de uso de la aplicación. Esta información se usa para simular el modelo de carga.
- Comprender el objetivo de las pruebas de carga. Esto le permite interpretar y analizar los resultados de pruebas de carga.

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>Crear y ejecutar el proyecto de prueba de carga y rendimiento Web

### <a name="create-a-web-performance-and-load-test-project"></a>Crear un proyecto de prueba de carga y rendimiento Web

1. Abra Visual Studio de 2015. Elija **archivo** > **nuevo** > **proyecto** en la barra de menús para abrir el cuadro de diálogo **Nuevo proyecto** .

2. Expanda el nodo **Visual C#** y elija **prueba** > **proyecto de prueba de carga y rendimiento Web**. Asigne un nombre al proyecto y, a continuación, elija el botón **Aceptar** .

    ![Captura de pantalla del cuadro de diálogo nuevo proyecto][1]

    Verá un nuevo proyecto de prueba de carga y rendimiento Web en el Explorador de soluciones.

    ![Captura de pantalla del explorador de soluciones, que muestra el nuevo proyecto][2]

### <a name="record-a-web-performance-test"></a>Grabar una prueba de rendimiento web

1. Abra el proyecto .webtest.

2. Elija el icono **Agregar grabación** para iniciar una sesión de grabación en el explorador.

    ![Captura de pantalla del icono Agregar grabación en un explorador][3]

    ![Captura de pantalla del botón de registro en un explorador][4]

3. Vaya a la aplicación de servicio tela. El panel de grabación debe mostrar las solicitudes de web.

    ![Captura de pantalla de las solicitudes de web en el panel de grabación][5]

4. Realizar una secuencia de acciones que esperan los usuarios realicen. Estas acciones se usan como un patrón para generar la carga.

5. Cuando haya terminado, elija el botón **Detener** para detener la grabación.

    ![Captura de pantalla del botón de detención][6]

    El proyecto .webtest en Visual Studio debe ha capturado una serie de solicitudes. Parámetros dinámicos se reemplazan automáticamente. En este momento, puede eliminar las solicitudes de dependencia adicional, repetidas que no forman parte de su escenario de prueba.

6. Guarde el proyecto y, a continuación, elija el comando **Ejecutar prueba** para ejecutar la prueba de rendimiento web localmente y asegúrese de que todo funciona correctamente.

    ![Captura de pantalla del comando Ejecutar prueba][7]

### <a name="parameterize-the-web-performance-test"></a>Agregar parámetros a la prueba de rendimiento web

Se puede agregar parámetros a la prueba de rendimiento web convirtiéndolo a una prueba de rendimiento web codificada y, a continuación, puede editar el código. Como alternativa, puede enlazar la prueba de rendimiento web a una lista de datos para que la prueba se itera a través de los datos. Para obtener más información acerca de cómo convertir la prueba de rendimiento web a una prueba codificada, vea [generar y ejecutar una prueba de rendimiento web codificada](https://msdn.microsoft.com/library/ms182552.aspx) . Para obtener información sobre cómo vincular datos a una prueba de rendimiento web, vea [Agregar un origen de datos para un rendimiento web probar](https://msdn.microsoft.com/library/ms243142.aspx) .

En este ejemplo, se deberá convertir la prueba de rendimiento web a una prueba codificada para reemplazar el identificador de avión con un GUID generado y agregar más solicitudes de enviar vuelos a ubicaciones diferentes.

### <a name="create-a-load-test-project"></a>Crear un proyecto de prueba de carga

Un proyecto de prueba de carga se compone de uno o varios de los escenarios descritos por la prueba de rendimiento web y pruebas de unidad, junto con la configuración de prueba de carga especificado adicionales. Los pasos siguientes muestran cómo crear un proyecto de prueba de carga:

1. En el menú contextual de su proyecto de prueba de carga y rendimiento Web, elija **Agregar** > **Prueba de carga**. En el Asistente para **La prueba de carga** , elija el botón **siguiente** para configurar las opciones de prueba.

2. En la sección **Modelo de carga** , elija si desea una carga de usuarios constante o una carga de paso, comience con unos pocos usuarios y aumenta los usuarios a lo largo del tiempo.

    Si tiene una buena estimación de la cantidad de carga de usuario y desea ver cómo se realiza el sistema actual, elija **Cargar constante**. Si su objetivo es saber si el sistema realiza coherente con diversas cargas, elija **Paso carga**.

3. En la sección de **Combinación de pruebas** , seleccione el botón **Agregar** y, a continuación, seleccione la prueba que desea incluir en la prueba de carga. Puede usar la columna de **distribución** para especificar el porcentaje de total de pruebas ejecutar para cada prueba.

4. En la sección **Configuración de ejecución** , especifique la duración de la prueba de carga.

    >[AZURE.NOTE] La opción de **Iteraciones de prueba** está disponible solo cuando se ejecuta una prueba de carga localmente mediante Visual Studio.

5. En la sección **ubicación** de la **Configuración de ejecución**, especifique la ubicación donde se generan solicitudes de prueba de carga. El asistente puede pedirle que inicie sesión en su cuenta de servicios del equipo. Inicie sesión y, a continuación, elija una ubicación geográfica. Cuando haya terminado, seleccione el botón **Finalizar** .

6. Después de crea la prueba de carga, abra el proyecto LoadTest y elija el valor, como la **Configuración de ejecución**de ejecución actual > **Run Settings1 [Active]**. Se abrirá la configuración de ejecución en la ventana **Propiedades** .

7. En la sección de **resultados** de la ventana de propiedades de **Configuración de ejecución** , la configuración de **Almacenamiento de detalles de tiempo** debe tener **Ninguno** como el valor predeterminado. Cambie este valor a **Todos los detalles individuales** para obtener más información sobre la carga de resultados de la prueba. Para obtener más información sobre cómo conectarse a Visual Studio Team Services y ejecutar una prueba de carga, vea [Cargar pruebas](https://www.visualstudio.com/load-testing.aspx) .

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>Ejecutar la prueba de carga mediante Visual Studio Team Services

Elegir el comando **Ejecutar la prueba de carga** para iniciar la ejecución de prueba.

![Captura de pantalla del comando ejecutar la prueba de carga][8]

## <a name="view-and-analyze-the-load-test-results"></a>Ver y analizar los resultados de pruebas de carga

Como la carga de prueba progresa, que se representa gráficamente la información de rendimiento. Verá algo similar a la siguiente gráfico.

![Captura de pantalla de gráfico de rendimiento para los resultados de pruebas de carga][9]

1. Elija el vínculo **Descargar informe** cerca de la parte superior de la página. Después de descarga el informe, elija el botón **Ver informe** .

    En la ficha **gráfico** puede ver los gráficos de varios contadores de rendimiento. En la pestaña **Resumen** , aparecerán los resultados de pruebas general. La pestaña **tablas** , muestra el número total de pruebas de carga pasado y errores.

2. Elija el número de vínculos en la **prueba** > **error** y los **errores** > **contar** columnas para ver los detalles del error.

    La ficha **Detalles** muestra información de escenario de usuario y de prueba virtual solicitudes erróneas. Estos datos pueden ser útiles si la prueba de carga incluye varios escenarios.

Ver [Análisis de cargar resultados de pruebas en la vista de gráficos de cargar analizador de pruebas](https://www.visualstudio.com/load-testing.aspx) para obtener más información acerca de cómo ver los resultados de la prueba de carga.

## <a name="automate-your-load-test"></a>Automatizar la prueba de carga

Cargar pruebas de equipo servicios de Visual Studio proporciona las API para ayudarle a administrar las pruebas de carga y analizar los resultados en una cuenta de servicios del equipo. Para obtener más información, vea [API de Rest de pruebas de carga de nube](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) .

## <a name="next-steps"></a>Pasos siguientes
- [Supervisar y diagnosticar servicios en una configuración de desarrollo de equipo local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png
