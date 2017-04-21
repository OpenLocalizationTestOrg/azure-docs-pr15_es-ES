<properties 
    pageTitle="Usar parámetros de servicio Web de aprendizaje de máquina Azure | Microsoft Azure" 
    description="Cómo utilizar parámetros de servicio Web de Azure máquina aprendizaje para modificar el comportamiento del modelo cuando se tiene acceso al servicio web." 
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
    ms.author="raymondl;garye"/>

#<a name="use-azure-machine-learning-web-service-parameters"></a>Usar parámetros de servicio Web de aprendizaje de Azure automático

Un servicio web de aprendizaje del equipo de Azure se crea publicando apropiados que contiene módulos con parámetros configurables. En algunos casos, desea cambiar el comportamiento de módulo mientras se ejecuta el servicio web. *Parámetros del servicio Web* permiten realizar esta tarea. 

Un ejemplo común está configurando los [Datos de importación] [ reader] módulo para que el usuario del servicio web publicada puede especificar otro origen de datos cuando se tiene acceso al servicio web. O la configuración de la [Exportación de datos] [ writer] módulo para que se puede especificar un destino diferente. Algunos otros ejemplos cambiar el número de bits de la [Función hash] [ feature-hashing] deseado de módulo o el número de características para la [selección de característica basada en el filtro] [ filter-based-feature-selection] módulo. 

Puede establecer parámetros de servicio Web y asociarlos con uno o más parámetros de módulo en el ensayo y puede especificar si son necesarios u opcionales. El usuario del servicio web puede proporcionar valores para estos parámetros al que llaman al servicio web. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##<a name="how-to-set-and-use-web-service-parameters"></a>Cómo configurar y usar los parámetros del servicio Web

Defina un parámetro del servicio Web haciendo clic en el icono junto al parámetro para un módulo y seleccionando "Establecer como parámetro del servicio web". Esto crea un nuevo parámetro del servicio Web y conecta a dicho parámetro de módulo. A continuación, cuando se tiene acceso al servicio web, el usuario puede especificar un valor para el parámetro de servicio Web y se aplica al parámetro de módulo.

Cuando se define un parámetro del servicio Web, está disponible para cualquier otro parámetro de módulo en la prueba. Si define un parámetro del servicio Web asociado con un parámetro para un módulo, puede usar ese mismo parámetro del servicio Web de cualquier otro módulo, siempre y cuando el parámetro espera el mismo tipo de valor. Por ejemplo, si el parámetro del servicio Web es un valor numérico, a continuación, puede solo se pueden usar para los parámetros de módulo que esperan un valor numérico. Cuando el usuario establece un valor para el parámetro de servicio Web, se aplicará a todos los parámetros del módulo asociado.

Puede decidir si desea proporcionar un valor predeterminado para el parámetro de servicio Web. En este caso, el parámetro es opcional para el usuario del servicio web. Si no proporciona un valor predeterminado, a continuación, el usuario debe escribir un valor cuando se tiene acceso al servicio web.

La documentación de la API del servicio web incluye información para el usuario del servicio web acerca de cómo especificar el parámetro del servicio Web mediante programación al obtener acceso al servicio web.

>[AZURE.NOTE] El vínculo de la **página de Ayuda de la API** en el servicio web **paneles** en máquina aprendizaje Studio proporciona la documentación de la API de un servicio web clásico. La documentación de la API para un nuevo servicio web se proporciona a través del portal de [Servicios Web de Azure máquina aprendizaje](https://services.azureml.net/Quickstart) en las páginas **Consume** y **Swagger API** del servicio web.


##<a name="example"></a>Ejemplo

Por ejemplo, supongamos tenemos un experimentar con un [Exportar datos] [ writer] módulo que envía información a almacenamiento de blobs de Windows Azure. Se definirá un parámetro del servicio Web denominada "Blob ruta" que permite al usuario del servicio web cambiar la ruta de acceso para el almacenamiento de blobs cuando se tiene acceso al servicio.

1.  En Studio de aprendizaje del equipo, haga clic en los [Datos de exportación] [ writer] módulo para seleccionarla. Sus propiedades se muestran en el panel de propiedades a la derecha del lienzo de ensayo.

2.  Especifique el tipo de almacenamiento:

    - En **Especifique destino de datos**, seleccione "Almacenamiento de blobs de Windows Azure".
    - En **Especifique el tipo de autenticación**, seleccione "Cuenta".
    - Escriba la información de cuenta para el almacenamiento de blobs de Windows Azure. 
    <p />

3.  Haga clic en el icono a la derecha de la **ruta de acceso a objetos binarios que comienza con el parámetro de contenedor**. Aparece así:

    ![Icono de parámetro del servicio Web][icon]

    Seleccione "Establecer como parámetro del servicio web".

    Se agrega una entrada en los **Parámetros del servicio Web** en la parte inferior del panel de propiedades con el nombre "Ruta de acceso a objetos binarios que comienza con el contenedor". Este es el parámetro de servicio Web que ya está asociada a este [Exportar datos] [ writer] parámetro de módulo.

4.  Para cambiar el nombre del parámetro del servicio Web, haga clic en el nombre, escriba "Blob ruta" y presione la tecla **ENTRAR** . 
 
5.  Para proporcionar un valor predeterminado para el parámetro del servicio Web, haga clic en el icono situado a la derecha del nombre del, seleccione "Proporcionar valor predeterminado", escriba un valor (por ejemplo, "container1/output1.csv") y presione la tecla **ENTRAR** .

    ![Parámetro del servicio Web][parameter]

6.  Haga clic en **Ejecutar**. 

7.  Haga clic en **Servicio Web de implementar** y seleccione **Implementar servicio Web [clásica]** o **implementar Web [nuevo]** para implementar el servicio web.

El usuario del servicio web ahora puede especificar un nuevo destino para los [Datos de exportar] [ writer] módulo de acceso al servicio web.

##<a name="more-information"></a>Más información

Para obtener un ejemplo más detallado, consulte la entrada de [Parámetros de servicio Web](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) en el [Blog de aprendizaje del equipo](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Para obtener más información sobre el acceso a un servicio web de aprendizaje del equipo, consulte [cómo consumir una máquina publicada servicio web de aprendizaje](machine-learning-consume-web-services.md).



<!-- Images -->
[icon]: ./media/machine-learning-web-service-parameters/icon.png
[parameter]: ./media/machine-learning-web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 
