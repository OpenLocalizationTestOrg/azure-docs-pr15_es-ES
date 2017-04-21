<properties
    pageTitle="Crear una solución IoT mediante el análisis de secuencia | Microsoft Azure"
    description="Tutorial: Introducción para la solución IoT de análisis de secuencia de un escenario de peaje"
    keywords="solución IOT, funciones de la ventana"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Crear una solución IoT mediante el análisis de secuencia

## <a name="introduction"></a>Introducción

En este tutorial, aprenderá cómo usar el análisis de secuencia de Azure para obtener recomendaciones en tiempo real de los datos. Los programadores pueden combinar fácilmente flujos de datos, como secuencias de clic, los registros y los eventos generados por el dispositivo, con los registros históricos o datos de referencia para obtener información de la empresa. Como un servicio de cálculo de secuencia totalmente administrado y en tiempo real está alojado en Microsoft Azure, análisis de secuencia de Azure proporciona la resistencia integrada, baja latencia y escalabilidad para poner el funcionamiento en minutos.

Después de completar este tutorial, podrá:

-   Familiarícese con el portal de análisis de secuencia de Azure.
-   Configurar e implementar un flujo de trabajo.
-   Articular problemas del mundo real y solucionarlos mediante el lenguaje de consulta de análisis de la secuencia.
-   Desarrollar transmisión soluciones para sus clientes mediante el análisis de flujo con confianza.
-   Usar la supervisión y el registro de la experiencia para solucionar problemas.

## <a name="prerequisites"></a>Requisitos previos

Necesitará los siguientes requisitos previos para completar este tutorial:

-   La última versión de [PowerShell de Azure](../powershell-install-configure.md)
-   2015 de Visual Studio o de la [Comunidad de Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) de libre
-   Una [suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Privilegios de administrador en el equipo
-   Descarga de [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) desde el centro de descarga de Microsoft
-   Opcional: Código de origen para el generador de evento TollApp en [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction-hello-toll"></a>Introducción de escenario: "Hola a todos, pago!"


Una estación de pago es un fenómeno común. Se encuentre en muchos destinada, puentes y túneles en todo el mundo. Cada estación pago tiene varias cabinas de pago. En stands manuales, detener para pagar la llamada a un operador. Al stands automatizados, un sensor sobre cada cabina examina una tarjeta de radio frecuencia ID adherida a la velocidad del vehículo al pasar la cabina de pago. Es fácil de visualizar el paso de vehículos a través de estas estaciones de pago como una secuencia de evento en los que se pueden realizar operaciones interesantes.

![Imagen de coches en stands de pago](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Datos entrantes

En este tutorial se trabaja con dos secuencias de datos. Sensores instalados en la entrada y salida de las estaciones de pago producen la primera secuencia. La segunda secuencia es un conjunto de datos de búsqueda estático que tiene datos de registro de vehículo.

### <a name="entry-data-stream"></a>Flujo de datos de entrada

La secuencia de entrada de datos contiene información sobre automóviles según entran estaciones de pago.


| TollID | EntryTime               | LicensePlate | Estado | Asegúrese de   | Modelo   | VehicleType | VehicleWeight | Pago | Etiqueta       |
|--------|-------------------------|--------------|-------|--------|---------|-------------|---------------|------|-----------|
| 1      | 12:01:00.000 de 2014-09-10 | JNB 7001     | NY    | Honda  | CRV     | 1           | 0             | 7    |           |
| 1      | 12:02:00.000 de 2014-09-10 | YXZ 1001     | NY    | Toyota | Camry   | 1           | 0             | 4    | 123456789 |
| 3      | 12:02:00.000 de 2014-09-10 | ABC 1004     | CT    | Ford   | Taurus  | 1           | 0             | 5    | 456789123 |
| 2      | 12:03:00.000 de 2014-09-10 | XYZ 1003     | CT    | Toyota | Corolla | 1           | 0             | 4    |           |
| 1      | 12:03:00.000 de 2014-09-10 | BNJ 1007     | NY    | Honda  | CRV     | 1           | 0             | 5    | 789123456 |
| 2      | 12:05:00.000 de 2014-09-10 | CDE 1007     | NJ    | Toyota | 4 x 4     | 1           | 0             | 6    | 321987654 |


Aquí es una breve descripción de las columnas:

| Columna | Descripción |
|--------------|--------------------------------------------------------------------|
| TollID       | El identificador de la cabina de pago que identifica una cabina de pago            |
| EntryTime    | La fecha y hora de entrada del vehículo para la cabina de pago en UTC |
| LicensePlate | El número de placa de licencia del vehículo                            |
| Estado        | Un estado de Estados Unidos                                           |
| Asegúrese de         | El fabricante del automóvil                                 |
| Modelo        | El número de modelo del automóvil                                 |
| VehicleType  | 1 para vehículos de pasajeros o 2 para vehículos comerciales       |
| WeightType   | Peso del vehículo en toneladas; 0 para vehículos de pasajeros                   |
| Pago         | El valor de pago en USD                                              |
| Etiqueta          | La etiqueta e en el automóvil que automatizan pago; en blanco cuando el pago se ha realizado manualmente |


### <a name="exit-data-stream"></a>Flujo de datos de salida

La secuencia de salida de datos contiene información sobre automóviles salir de la estación de pago.


| **TollId** | **ExitTime**                 | **LicensePlate** |
|------------|------------------------------|------------------|
| 1          | 2014-09-10T12:03:00.0000000Z | JNB 7001         |
| 1          | 2014-09-10T12:03:00.0000000Z | YXZ 1001         |
| 3          | 2014-09-10T12:04:00.0000000Z | ABC 1004         |
| 2          | 2014-09-10T12:07:00.0000000Z | XYZ 1003         |
| 1          | 2014-09-10T12:08:00.0000000Z | BNJ 1007         |
| 2          | 2014-09-10T12:07:00.0000000Z | CDE 1007         |

Aquí es una breve descripción de las columnas:


| Columna | Descripción |
|--------------|-----------------------------------------------------------------|
| TollID       | El identificador de la cabina de pago que identifica una cabina de pago         |
| ExitTime     | La fecha y hora de salida del vehículo de cabina de pago en UTC |
| LicensePlate | El número de placa de licencia del vehículo                         |

### <a name="commercial-vehicle-registration-data"></a>Datos de registro de vehículos comerciales

El tutorial utiliza una instantánea estática de una base de datos de registro de vehículos comerciales.


| LicensePlate | RegistrationId | Caducado |
|--------------|----------------|---------|
| SVT 6023     | 285429838      | 1       |
| XLZ 3463     | 362715656      | 0       |
| CPF 1005     | 876133137      | 1       |
| RIV 8632     | 992711956      | 0       |
| SNY 7188     | 592133890      | 0       |
| ELH 9896     | 678427724      | 1       |

Aquí es una breve descripción de las columnas:


| Columna | Descripción |
|--------------|-----------------------------------------------------------------|
| LicensePlate | El número de placa de licencia del vehículo                         |
| RegistrationId     | Identificador de registro del vehículo |
| Caducado | El estado de registro del vehículo: 0 si está activo, registro de vehículo 1 si caducado registro                 |


## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurar el entorno para el análisis de secuencia de Azure

Para completar este tutorial, necesita una suscripción de Microsoft Azure. Microsoft ofrece una versión de prueba gratuita para los servicios de Microsoft Azure.

Si no tiene una cuenta de Azure, puede [solicitud una versión de prueba gratuita](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Para suscribirse a una prueba gratuita, necesita un dispositivo móvil que pueda recibir mensajes de texto y tarjeta de crédito.

Asegúrese de seguir los pasos de la sección "Limpiar su cuenta de Azure" al final de este artículo para que pueda hacer el mejor uso de su 200 $ libre crédito Azure.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Aprovisionar Azure recursos necesarios para el tutorial

Este tutorial requiere dos hubs de evento para recibir flujos de datos de *entrada* y *Salir de ella* . Base de datos de SQL Azure proporciona los resultados de los trabajos de análisis de la secuencia. Almacenamiento de Azure almacena los datos de referencia sobre los registros de vehículo.

Puede usar la secuencia de comandos de Setup.ps1 en la carpeta TollApp GitHub para crear todos los recursos necesarios. Por razones de tiempo, se recomienda que se ejecute. Si desea obtener más información sobre cómo configurar estos recursos en el portal de Azure, consulte el apéndice "Configurar tutoriales recursos en el portal de Azure".

Descargue y guarde los archivos y la carpeta de archivos auxiliares [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) .

Abra un **Microsoft Azure PowerShell** ventana _como administrador_. Si aún no dispone de PowerShell de Azure, siga las instrucciones de [instalar y configurar Azure PowerShell](../powershell-install-configure.md) para instalarlo.

Puesto que Windows bloquea automáticamente. ps1, DLL y archivos .exe, debe establecer la directiva de ejecución antes de ejecutar la secuencia de comandos. Asegúrese de que la ventana de PowerShell de Azure ejecuta _como administrador_. Ejecute **Set-ExecutionPolicy sin restricciones**. Cuando se le solicite, escriba **Y**.

![Captura de pantalla de "Set-ExecutionPolicy sin restricciones" ejecutar en la ventana de PowerShell de Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Ejecutar **Get-ExecutionPolicy** para asegurarse de que ha realizado el comando.

![Captura de pantalla de "Get-ExecutionPolicy" ejecutar en la ventana de PowerShell de Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Vaya al directorio que tiene las secuencias de comandos y la aplicación de generador.

![Captura de pantalla de "cd .\TollApp\TollApp" ejecutar en la ventana de PowerShell de Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Tipo de **.\\ Setup.ps1** para configurar su cuenta de Azure, crear y configurar todos los recursos necesarios y empezar a generar eventos. La secuencia de comandos toma aleatoriamente una región para crear los recursos. Para especificar explícitamente una región, puede pasar el **-ubicación** parámetro como en el ejemplo siguiente:

**. \\Setup.ps1-ubicación "Central de EE."**

![Captura de pantalla de la página de inicio de sesión de Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

La secuencia de comandos, abrirá la página de **Inicio de sesión** de Microsoft Azure. Escriba sus credenciales de cuenta.

> [AZURE.NOTE] Si su cuenta tiene acceso a varias suscripciones, le pedirá que escriba el nombre de la suscripción que desea usar para el tutorial.

La secuencia de comandos puede tardar varios minutos en ejecutar. Al finalizar, el resultado debe ser similar a la siguiente captura de pantalla.

![Captura de pantalla del resultado de la secuencia de comandos en la ventana de PowerShell de Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

También verá otra ventana es similar a la siguiente captura de pantalla. Esta aplicación está enviando eventos a los centros de evento de Azure, que es necesario para ejecutar el tutorial. Por lo tanto, no se detenga la aplicación o cerrar esta ventana hasta que finalice el tutorial.

![Captura de pantalla de "Enviar datos de concentrador de eventos"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Debería poder ver todos los recursos creados en el portal de Azure ahora. Vaya a <https://manage.windowsazure.com>e inicie sesión con sus credenciales de cuenta.

### <a name="azure-event-hubs"></a>Evento Azure Hubs

Haga clic en **BUS de servicio** en el lado izquierdo del portal de Azure para ver hubs de evento que creó el script en la sección anterior.

![Bus de servicio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Verá todos los espacios de nombres disponibles en su suscripción. Haga clic en el que comienza con *tolldata* (tolldata4637388511 en nuestro ejemplo). Haga clic en la pestaña **Evento HUBS** .

![Ficha de Hubs de evento en el portal de Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Verá dos hubs de evento con el nombre de *entrada* y *Salir* creado en este espacio de nombres.

![Captura de pantalla de "entrada" y "salida" hubs de evento en el portal de Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### <a name="azure-storage-container"></a>Contenedor de almacenamiento de Azure

1. Haga clic en **almacenamiento** en el lado izquierdo del portal de Azure para ver el contenedor de almacenamiento de Azure que se usa en el tutorial.

    ![Elemento de menú de almacenamiento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

2. Haga clic en el que comienzan con *tolldata* (tolldata4637388511 en nuestro ejemplo). Haga clic en la pestaña de **contenedores** para ver el contenedor creado.

    ![Ficha contenedores en el portal de Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

3. Haga clic en el contenedor de **tolldata** para ver el archivo cargado de JSON que tiene datos de registro de vehículo.

    ![Captura de pantalla del archivo registration.json en el contenedor](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### <a name="azure-sql-database"></a>Base de datos SQL Azure

1. Haga clic en **Bases de datos SQL** en el lado izquierdo del portal de Azure para ver la base de datos SQL que se utilizará en el tutorial.

    ![Bases de datos SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

2. Haga clic en **tolldatadb**.

    ![Captura de pantalla de la base de datos SQL creada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

3. Copiar el nombre del servidor sin el número de puerto (*nombre_de_servidor*. database.windows.net, por ejemplo).

## <a name="connect-to-the-database-from-visual-studio"></a>Conectarse a la base de datos de Visual Studio

Use Visual Studio para tener acceso a los resultados de la consulta en la base de datos de salida.

Conectarse a la base de datos SQL (el destino) de Visual Studio:

1. Abra Visual Studio y, a continuación, haga clic en **Herramientas** > **conectarse a la base de datos**.

2. Si se le solicita, haga clic en **Microsoft SQL Server** como origen de datos.

    ![Cuadro de diálogo Cambiar origen de datos](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3. En el campo **nombre de servidor** , pegue el nombre que ha copiado en la sección anterior desde el portal de Azure (es decir, *nombre_de_servidor*. database.windows.net).

4. Haga clic en **autenticación de SQL Server**.

5. Escriba **tolladmin** en el campo **nombre de usuario** y **123toll!** en el campo **contraseña** .

6. Haga clic en **Seleccione o escriba un nombre de base de datos**y seleccione **TollDataDB** como la base de datos.

    ![Agregar el cuadro de diálogo de conexión](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)

7. Haga clic en **Aceptar**.

8. Abra el Explorador de servidores.

    ![Explorador de servidores](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)

9. Vea cuatro tablas en la base de datos de TollDataDB.

    ![Tablas en la base de datos TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>Generador de eventos: TollApp proyecto de ejemplo

La secuencia de comandos de PowerShell se inicia automáticamente enviar eventos mediante el programa de aplicación de ejemplo de TollApp. No es necesario realizar pasos adicionales.

Sin embargo, si está interesado en los detalles de la implementación, puede encontrar el código fuente de la aplicación TollApp en GitHub [Ejemplos/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Captura de pantalla del código de ejemplo mostrado en Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Crear un análisis de flujo de trabajo

1. En el portal de Azure, abra el análisis de secuencia y haga clic en **nuevo** en la esquina inferior izquierda de la página para crear una nueva tarea de análisis.

    ![Botón nuevo](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

2. Haga clic en **crear rápido**. Seleccione la misma región donde se crean los otros recursos de la secuencia de comandos.

3. Para la configuración de la **Cuenta de almacenamiento de supervisión REGIONAL** , seleccione **Crear una nueva cuenta de almacenamiento** y asígnele un nombre único. Análisis de secuencia de Azure usará esta cuenta para almacenar la información de supervisión para todos los trabajos futuros.

4. Haga clic en **Crear trabajo de análisis de flujo** en la parte inferior de la página.

    ![Crear la opción de trabajo de análisis de secuencia](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Defina los orígenes de entrada

1. Haga clic en el trabajo de análisis creado en el portal.

    ![Captura de pantalla de la tarea de análisis en el portal](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

2. Haga clic en la ficha de **entradas** para definir el origen de datos.

    ![La ficha de entradas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

3. Haga clic en **Agregar una entrada**.

    ![Agregar una opción de entrada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

4. Haga clic en **flujo de datos** en la primera página.

    ![La opción de flujo de datos](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

5. En la segunda página del asistente, haga clic en **Evento concentrador** .

    ![La opción de concentrador de evento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

6. Escriba **EntryStream** como **ALIAS de entrada**.

7. Haga clic en la lista desplegable de **Concentrador de evento** y seleccione el elemento que comienza con "TollData" (por ejemplo, TollData9518658221).

8. Seleccione la **entrada** como el nombre del evento concentrador y **todas** como el nombre de la directiva de concentrador de evento.

    La configuración tendrá un aspecto similar:

    ![Configuración de concentrador de evento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

9. En la siguiente página, seleccione **JSON** para el **Formato de SERIALIZACIÓN de evento** y **UTF8** de **codificación**.

    ![Configuración de serialización](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

10. Haga clic en **Aceptar** en la parte inferior de la página para finalizar al asistente.

    ![Captura de pantalla de EntryStream entrada en el portal de Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

    Ahora que ha creado la secuencia de entrada, se siga los mismos pasos para crear la secuencia de salida. En la tercera página del asistente, asegúrese de escribir valores como en la siguiente captura de pantalla.

    ![Configuración de la secuencia de salida](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

    Ha definido dos secuencias de entrada:

    ![Secuencias de entrada definidas en el portal de Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

    A continuación, agregará la entrada de datos de referencia para el archivo blob que contiene los datos de registro de coches.

11. Haga clic en **Agregar entrada**y, a continuación, haga clic en **Datos de referencia**.

    ![Opciones de "Agregar una entrada" con los datos de referencia seleccionados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

12. En la siguiente página, seleccione la cuenta de almacenamiento que comienza con **tolldata**. El nombre del contenedor debe ser **tolldata**y el nombre de blobs de Windows en **El patrón de ruta de acceso** debe ser **registration.json**. El nombre de archivo distingue mayúsculas de minúsculas y debe ser minúscula.

    ![Configuración de almacenamiento de blog](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

13. Seleccione los valores tal como se muestra en la siguiente captura de pantalla en la página siguiente y, a continuación, haga clic en **Aceptar** para finalizar al asistente.

    ![Selección de JSON para "formato de par serialización" y UTF8 para "Codificación"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

Ahora se definen todas las entradas.

![Captura de pantalla de las tres entradas definidas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## <a name="define-output"></a>Definir salida

1. Haga clic en la pestaña de **salida** y, a continuación, haga clic en **Agregar un resultado**.

    ![La pestaña de salida y la opción "Agregar un resultado"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

2. Haga clic en **base de datos SQL**.

3. Seleccione el nombre del servidor que se usó en la sección "Conectar a base de datos de Visual Studio" de este artículo. El nombre de la base de datos es **TollDataDB**.

4. Escriba **tolladmin** en el campo **nombre de usuario** , **123toll!** en el campo de **contraseña** y **TollDataRefJoin** en el campo de **tabla** .

    ![Configuración de la base de datos SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## <a name="azure-stream-analytics-query"></a>Consulta de análisis de secuencia Azure

La ficha **consulta** contiene una consulta SQL que transforma los datos entrantes.

![Una consulta agregada a la pestaña de la consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

Este tutorial intenta responder a varias preguntas empresariales relacionados con datos y construcciones de análisis de secuencia de consultas puede usarse en análisis de secuencia de Azure para proporcionar una respuesta relevante de pago.

Antes de comenzar la primera tarea de análisis de secuencia, vamos a explorar algunos escenarios y la sintaxis de la consulta.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Introducción al lenguaje de consulta de análisis de secuencia de Azure
-----------------------------------------------------

Supongamos que necesita contar el número de vehículos que introduzca una cabina de pago. Porque se trata de una secuencia continua de eventos, debe definir un "período de tiempo." Vamos a modificar la pregunta para utilizarlo como "¿cuántos vehículos escribir una cabina pago cada tres minutos?". Normalmente se conoce como el recuento de rotación.

Echemos un vistazo a la consulta de análisis de secuencia de Azure que responda a esta pregunta:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Como puede ver, el análisis de secuencia de Azure usa un lenguaje de consulta como SQL y agrega unas extensiones para especificar aspectos relacionados con el tiempo de la consulta.

Para obtener más información, lea acerca de construcciones de [Administración del tiempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) y [ventanas](https://msdn.microsoft.com/library/azure/dn835019.aspx) que se usan en la consulta de MSDN.

## <a name="testing-azure-stream-analytics-queries"></a>Prueba de Azure secuencia análisis de consultas

Ahora que ha escrito la primera consulta de análisis de secuencia de Azure, es hora para probar usando los archivos de datos de ejemplo ubicados en la carpeta TollApp en la siguiente ruta:

**.. \\TollApp\\TollApp\\datos**

Esta carpeta contiene los archivos siguientes:

- Entry.JSON
- Exit.JSON
- Registration.JSON

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>Pregunta 1: Número de vehículos introducir una cabina de pago

1. Abra el portal de Azure y vaya a su trabajo de análisis de secuencia de Azure creado. Haga clic en la ficha **consulta** y pegue la consulta de la sección anterior.

    ![Consulta que se pega en la pestaña de la consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

2. Para validar esta consulta con los datos de ejemplo, haga clic en el botón de **prueba** . En el cuadro de diálogo que se abre, vaya a Entry.json, un archivo que tiene datos de ejemplo de la secuencia de eventos **EntryTime** .

    ![Captura de pantalla del archivo Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

3. Validar el resultado de la consulta según lo esperado:

    ![Resultados de la prueba.](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Pregunta 2: Informe tiempo total de cada coche pase a través de la cabina de pago

El tiempo promedio necesario para que un coche pase por el número de pago ayuda a evaluar la eficiencia del proceso y de la experiencia del cliente.

Para buscar el tiempo total, debe unirse a la secuencia de EntryTime con la secuencia ExitTime. Combinará las secuencias en las columnas TollId y LicencePlate. El operador **JOIN** requiere que especifique libertad temporal que describa la diferencia de tiempo aceptable entre los eventos combinados. Función **DATEDIFF** usará para especificar que los eventos deben ser no más de 15 minutos entre sí. También se aplicarán las horas de entrada para calcular el tiempo real que emplea un coche en la estación de pago y la función **DATEDIFF** para salir. Tenga en cuenta la diferencia entre el uso de **DIFFECHA** cuando se usa en una instrucción **SELECT** en lugar de una condición de **combinación** .

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Para probar esta consulta, actualice la consulta en la ficha **consulta** del trabajo:

    ![Consulta actualizado en la pestaña de la consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

2. Haga clic en **probar** y especifique los archivos de entrada de muestra para EntryTime y ExitTime.

    ![Captura de pantalla de los archivos seleccionados de entrada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

3. Seleccione la casilla de verificación para probar la consulta y ver los resultados:

    ![Resultados de la prueba.](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>Pregunta 3: Informar a todos los vehículos comerciales con registro caducado

Análisis de secuencia de Azure puede usar estáticas instantáneas de datos para unirse con flujos de datos temporal. Para demostrar esta capacidad, utilice la siguiente pregunta de ejemplo.

Si un vehículo comercial está registrado con la empresa de pago, puede pasar a través de la cabina de pago sin detenido para inspección. Tabla de consulta de registro de vehículos comerciales usará para identificar todos los vehículos comerciales que haya expirado registros.

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Para probar una consulta con los datos de referencia, debe definir un origen de entrada para los datos de referencia, que se ha hecho todavía.

1. Para probar esta consulta, pegue la consulta en la pestaña de la **consulta** , haga clic en **probar**y especificar los dos orígenes de entrada:

    ![Captura de pantalla de los archivos seleccionados de entrada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

2. Ver el resultado de la consulta:

    ![Captura de pantalla del resultado de consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## <a name="start-the-stream-analytics-job"></a>Iniciar el trabajo de análisis de secuencia


Ahora es el momento para finalizar la configuración e iniciar el trabajo. Guardar la consulta de pregunta 3, que generará un resultado que coincida con el esquema de la tabla de resultados de **TollDataRefJoin** .

Vaya al **panel**de trabajo y haga clic en **Inicio**.

![Captura de pantalla del botón de inicio en el panel de trabajo](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

En el cuadro de diálogo que se abre, cambie la hora de **Inicio de salida** a **la hora personalizados**. Cambiar la hora a una hora antes de la hora actual. Este cambio garantiza que se procesan todos los eventos del concentrador de evento inició generar los eventos al principio del tutorial. Ahora haga clic en la marca de verificación para iniciar el trabajo.

![Selección de tiempo personalizado](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Iniciar la tarea puede tardar unos minutos. Puede ver el estado en la página de nivel superior para el análisis de secuencia.

![Captura de pantalla del estado de la tarea](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## <a name="check-results-in-visual-studio"></a>Resultados de la comprobación en Visual Studio

1. Abra el Explorador de servidores de Visual Studio y haga clic en la tabla **TollDataRefJoin** .
2. Haga clic en **Mostrar datos de tabla** para ver el resultado de su trabajo.

    ![Selección de "Mostrar datos de tabla" en el Explorador de servidores](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Escalar trabajos de análisis de secuencia de Azure

Análisis de secuencia de Azure está diseñado para elástica escala para que pueda gestionar una gran cantidad de datos. La consulta de análisis de secuencia de Azure puede usar una cláusula **Partición por** para indicar al sistema que este paso se escala. **PartitionId** es una columna especial que agrega el sistema para que coincida con el ID de la entrada (concentrador de evento).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Detener el trabajo actual, actualice la consulta en la ficha **consulta** y abrir la pestaña **escala** .

    **Unidades de transmisión** definir la cantidad de energía de cálculo que pueda recibir el trabajo.

2. Mueva el control deslizante al 6.

    ![Captura de pantalla de selección de unidades streaming 6](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

3. Vaya a la pestaña **salidas** y cambie el nombre de la tabla SQL a **TollDataTumblingCountPartitioned**.

Ahora, si inicia el trabajo de análisis de secuencia de Azure puede distribuir el trabajo entre más recursos de cálculo y lograr un mejor rendimiento. Tenga en cuenta que la aplicación de TollApp también está enviando eventos divididos por TollId.

## <a name="monitor"></a>Monitor

La pestaña **MONITOR** contiene estadísticas sobre el trabajo en ejecución.

![Captura de pantalla de estadísticas acerca de la ejecución de trabajos](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

Puede acceder a **Registros de las operaciones** de la pestaña **panel** .

![La opción "Operación registros"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![Captura de pantalla de los registros de operación donde puede ver el estado de tareas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Para ver información adicional acerca de un evento determinado, haga clic en el evento y, a continuación, haga clic en el botón **Detalles** .

![Captura de pantalla de detalles sobre un evento seleccionado](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## <a name="conclusion"></a>Conclusión

En este tutorial se presenta el servicio de análisis de secuencia de Azure. Muestra cómo configurar las entradas y los resultados de la tarea de análisis de secuencia. Usando el escenario de datos de pago, el tutorial explica tipos comunes de problemas que puedan aparecer en el espacio de datos en movimiento y cómo puede resolverse con consultas de tipo SQL simples en análisis de secuencia de Azure. El tutorial describe construcciones de extensión SQL para trabajar con datos temporales. Mostramos cómo participar en flujos de datos, cómo mejorar el flujo de datos con datos de referencia estática y cómo escalar una consulta para lograr un mayor rendimiento.

Aunque este tutorial proporciona una buena introducción, no está completa, por cualquier medio. Puede encontrar más patrones de consulta mediante el lenguaje SAQL en [los ejemplos de consulta para patrones de uso de análisis de secuencia comunes](stream-analytics-stream-analytics-query-patterns.md).
Consulte la [documentación en línea](https://azure.microsoft.com/documentation/services/stream-analytics/) para obtener más información sobre el análisis de secuencia de Azure.

## <a name="clean-up-your-azure-account"></a>Limpiar su cuenta de Azure

1. Detener el trabajo de análisis de secuencia en el portal de Azure.

    La secuencia de comandos Setup.ps1 crea dos hubs de evento y una base de datos SQL. Las siguientes instrucciones le ayude a limpiar recursos al final del curso.

2. En una ventana de PowerShell, escriba **.\\ Cleanup.ps1** para iniciar la secuencia de comandos que elimina los recursos utilizados en el tutorial.

    > [AZURE.NOTE] Recursos identificados por el nombre. Asegúrese de que detenidamente cada elemento antes de confirmar la eliminación.

    ![Captura de pantalla del proceso de limpieza](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)
