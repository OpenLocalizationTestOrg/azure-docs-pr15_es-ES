<properties
    pageTitle="Modelos de aprendizaje de reciclaje mediante programación | Microsoft Azure"
    description="Aprenda a un modelo de reciclaje y actualizar el servicio web para usar el modelo recién capacitado en Azure el aprendizaje mediante programación."
    services="machine-learning"
    documentationCenter=""
    authors="raymondlaghaeian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="raymondl;garye;v-donglo"/>


# <a name="retrain-machine-learning-models-programmatically"></a>Modelos de aprendizaje de reciclaje mediante programación  

En este tutorial, aprenderá a un servicio Web de Azure máquina aprendizaje mediante C# y el servicio de ejecución de máquina aprendizaje por lotes de reciclaje mediante programación.

Una vez haya correo el modelo, los siguientes tutoriales muestran cómo actualizar el modelo en el servicio web predictiva:

- Si implementa un servicio web de clásico en el portal de servicios Web de aprendizaje de equipo, consulte a [un servicio web de clásico de reciclaje](machine-learning-retrain-a-classic-web-service.md). 
- Si implementa un servicio web nuevo, vea [un nuevo servicio web mediante los cmdlets de administración de aprendizaje de máquinas de reciclaje](machine-learning-retrain-new-web-service-using-powershell.md).

Para obtener información general del proceso de reconversión, consulte [reciclaje de un modelo de aprendizaje del equipo](machine-learning-retrain-machine-learning-model.md).

Si desea empezar con el servicio web de nuevo administrador de recursos de Azure según existente, vea [un servicio web predictiva existente de reciclaje](machine-learning-retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Crear un ensayo de formación
 
En este ejemplo, que se usa "ejemplo 5: tren, prueba, Evaluate para clasificación binario: conjunto de datos para adultos" de los ejemplos de aprendizaje de Microsoft Azure máquina. 
    
Para crear el ensayo:

1.  Iniciar sesión en Microsoft Azure de máquina Studio de aprendizaje. 
2.  En la esquina inferior derecha del panel, haga clic en **nuevo**.
3.  Desde Microsoft Samples, seleccione ejemplo 5.
4.  Para cambiar el nombre de la misma, en la parte superior del lienzo de ensayo, seleccione el nombre de ensayo "ejemplo 5: tren, prueba, Evaluate para clasificación binario: conjunto de datos para adultos".
5.  Modelo de censo de tipo.
6.  En la parte inferior de la ensayo lienzo, haga clic en **Ejecutar**.
7.  Haga clic en **Configurar el servicio web** y seleccione el **servicio web de Retraining**. 

    ![Primero.][2]

Diagrama 2: Inicial del ensayo.

## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Crear un ensayo predictivo y publicar como un servicio web  

A continuación cree un ensayo Predicative.

1.  En la parte inferior del lienzo de ensayo, haga clic en **Configurar servicio Web** y seleccione **Predictiva servicio Web**. Esto guarda el modelo como modelo capacitado y agrega módulos de entrada y salida de servicio web. 
2.  Haga clic en **Ejecutar**. 
3.  Después de que el ensayo ha terminado de ejecutarse, haga clic en **Desplegar servicio Web [clásica]** o **implementar Web [New]**.

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Implementar ensayo de aprendizaje como un servicio web de formación

Para el modelo de formación de reciclaje, debe implementar el ensayo de aprendizaje que ha creado como servicio web Retraining. Este servicio web necesita un módulo de *Salida del servicio Web* conectado a la * [Tren modelo] [ train-model] * módulo, para poder generar nuevos modelos capacitados.

1. Para volver a ensayo de formación, haga clic en el icono de experimentación en el panel izquierdo, haga clic en el ensayo denominado censo de modelo.  
2. En el cuadro de búsqueda de elementos de ensayo de búsqueda, escriba el servicio web. 
3. Arrastre un módulo de *Entrada de servicio Web* en el lienzo de ensayo y conectar su resultado en el módulo de *Limpiar datos que faltan* .  Así se garantiza que los datos reconversión se procesarán del mismo modo que los datos originales de aprendizaje.
4. Arrastre dos módulos de *servicio web de salida* hasta el lienzo de ensayo. Conecte la salida del módulo *Tren modelo* a uno y el resultado del módulo *Evaluar modelo* a otra. El resultado del servicio web para **El modelo de tren** nos ofrece el nuevo modelo capacitado. El resultado vinculado al **Modelo de evaluar** devuelve el resultado de ese módulo, que es el resultado de rendimiento.
5. Haga clic en **Ejecutar**. 

A continuación debe implementar el ensayo formación como un servicio web que genera un modelo de formación y los resultados de la evaluación de modelo. Para ello, el siguiente conjunto de acciones dependen de si está trabajando con un servicio web de clásico o un nuevo servicio web.  
  
**Servicio web clásico**

En la parte inferior del lienzo de ensayo, haga clic en **Configurar servicio Web** y seleccione **Implementar servicios Web [clásica]**. El **panel** de servicio Web se muestra con la clave de API y la página de Ayuda de la API para la ejecución del lote. El método de la ejecución del lote puede utilizarse para crear modelos capacitado.

**Nuevo servicio web**

En la parte inferior del lienzo de ensayo, haga clic en **Configurar servicio Web** y seleccione **Implementar [New] servicio Web**. El portal de aprendizaje de máquina Azure servicio de Web, servicios Web se abre en la página de servicio web de implementación. Escriba un nombre para el servicio web y elija un plan de pago, haga clic en **implementar**. El método de la ejecución del lote puede utilizarse para crear modelos capacitado

En cualquier caso, después de ensayo haya terminado de ejecutarse, el flujo de trabajo resultante debe ser como sigue:

![Flujo de trabajo resultante después de ejecutar.][4]

Diagrama 3: Resultante después de ejecutar del flujo de trabajo.

## <a name="retrain-the-model-with-new-data-using-bes"></a>El modelo de reciclaje con nuevos datos usando BES

En este ejemplo, está utilizando C# para crear la aplicación reconversión. También puede usar el código de ejemplo Python o R para llevar a cabo esta tarea.

Llamar a las API de formación:

1. Crear una aplicación de consola de C# en Visual Studio (nuevo -> proyecto -> Windows Desktop -> aplicación de consola).
2.  Inicie sesión en el portal de servicio Web de aprendizaje de equipo.
3.  Si está trabajando con un servicio web de clásico, haga clic en **Los servicios Web clásica**.
    1.  Haga clic en el servicio web que está trabajando.
    2.  Haga clic en el extremo predeterminado.
    3.  Haga clic en **consumir**.
    4.  En la parte inferior de la página **Consume** , en la sección de **Código de ejemplo** , haga clic en **lote**.
    5.  Continúe con el paso 5 de este procedimiento.
4.  Si está trabajando con un servicio web nuevo, haga clic en **Servicios Web**.
    1.  Haga clic en el servicio web que está trabajando.
    2.  Haga clic en **consumir**.
    3.  En la parte inferior de la página Consume, en la sección de **Código de ejemplo** , haga clic en **lote**.
5.  Copie el código C# de ejemplo para la ejecución del lote y pegarlo en el archivo Program.cs, que asegurarse de que el espacio de nombres permanece intacta.

Agregue el paquete de Nuget Microsoft.AspNet.WebApi.Client según lo especificado en los comentarios. Para agregar la referencia a Microsoft.WindowsAzure.Storage.dll, primero debe instalar la biblioteca de cliente para servicios de almacenamiento de Microsoft Azure. Para obtener más información, vea [Servicios de almacenamiento de Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Actualizar la declaración de apikey

Busque la declaración de **apikey** .

    const string apiKey = "abc123"; // Replace this with the API key for the web service

En la sección **información de consumo básico** de la página **Consume** , busque la clave principal y copiar a la declaración de **apikey** .

### <a name="update-the-azure-storage-information"></a>Actualizar la información de almacenamiento de Azure

El código de ejemplo BES carga un archivo de una unidad local (por ejemplo "C:\temp\CensusIpnput.csv") para el almacenamiento de Azure, procesa y vuelve a escribir los resultados en el almacenamiento de Azure.  

Para realizar esta tarea, debe recuperar la información de nombre, tecla y el contenedor de la cuenta de almacenamiento para su cuenta de almacenamiento desde el portal de Azure clásico y la actualización de los valores correspondientes en el código. 

1. Inicie sesión en el portal de Azure clásico.
1. En la columna de navegación izquierdo, haga clic en **almacenamiento**.
1. En la lista de cuentas de almacenamiento, seleccione uno para almacenar el modelo retrained.
1. En la parte inferior de la página, haga clic en **Administrar las teclas de acceso**.
1. Copie y guarde la **Clave principal de Access** y cierre el cuadro de diálogo. 
1. En la parte superior de la página, haga clic en **contenedores**.
1. Seleccione un contenedor existente o cree uno nuevo y guarde el nombre.

Busque las declaraciones *StorageAccountName*, *StorageAccountKey*y *StorageContainerName* y actualizar los valores que se guardó en el portal de Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name
            
También debe asegurarse del que archivo de entrada está disponible en la ubicación que especifique en el código. 

### <a name="specify-the-output-location"></a>Especifique la ubicación de salida

Al especificar la ubicación de salida en la solicitud, se debe especificar la extensión del archivo especificado en *RelativeLocation* como ilearner. 

Consulte el ejemplo siguiente:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

>[AZURE.NOTE] Los nombres de las ubicaciones de salida pueden diferir de las de este tutorial basándose en el orden en que se agregaron los módulos de salida del servicio web. Dado que configurar este experimentar de formación con dos salidas, los resultados incluyen información de la ubicación de almacenamiento para ambos.  

![Formación de salida][6]

Diagrama 4: Formación salida.

## <a name="evaluate-the-retraining-results"></a>Evaluar los resultados de reconversión
 
Cuando se ejecuta la aplicación, el resultado incluye el token de dirección URL y SA necesario para tener acceso a los resultados de la evaluación.

Puede ver los resultados de rendimiento del modelo retrained combinando los *BaseLocation*, *RelativeLocation*y *SasBlobToken* desde los resultados de *output2* (como se muestra en la imagen de salida reconversión anterior) y pegar la dirección URL completa en la barra de direcciones del explorador.  

Examine los resultados para determinar si el modelo recién capacitado funciona bien para reemplazar el existente.

Copie la *BaseLocation*, *RelativeLocation*y *SasBlobToken* de los resultados, que usa durante el proceso de reconversión.

## <a name="next-steps"></a>Pasos siguientes

Si implementa el servicio web predictiva haciendo clic en **Servicio de Web implementar [clásica]**, consulte [un servicio web de clásico de reciclaje](machine-learning-retrain-a-classic-web-service.md).

Si implementa el servicio web predictiva haciendo clic en **Desplegar [New] servicio Web**, vea [un nuevo servicio web mediante los cmdlets de administración de aprendizaje de máquinas de reciclaje](machine-learning-retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/