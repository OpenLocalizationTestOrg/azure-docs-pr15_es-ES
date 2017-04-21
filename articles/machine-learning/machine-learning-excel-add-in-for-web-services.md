<properties
    pageTitle="Complemento de servicios Web de aprendizaje de máquina Excel | Microsoft Azure"
    description="Cómo usar los servicios Web de aprendizaje del equipo de Azure directamente en Excel sin escribir código."
    services="machine-learning"
    documentationCenter=""
    authors="tedway"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/05/2016"
    ms.author="tedway;garye" />

# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Complemento de Excel para los servicios Web de aprendizaje del equipo de Azure

Excel hace que sea fácil llamar a servicios Web directamente sin necesidad de escribir código.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Pasos para usar un servicio Web existente en el libro

1. Abra el [archivo de Excel de ejemplo](http://aka.ms/amlexcel-sample-2), que contiene el complemento de Excel y datos sobre personas en la Titanic.
2. Elija el servicio Web haciendo clic en ella-"predicción de supervivencia Titanic (ejemplo Excel Add-in) [puntuación]" en este ejemplo.

    ![Seleccione el servicio Web][01]

3. Esto le llevará a la sección de **predicción** .  Este libro ya contiene datos de ejemplo, pero para un libro en blanco puede seleccionar una celda en Excel y haga clic en **datos de ejemplo de uso**.
4. Seleccione los datos con los encabezados y haga clic en el icono del rango de datos de entrada.  Asegúrese de que está marcada la casilla "Mis datos tienen encabezados".
5. En **resultados**, escriba el número de celdas donde desea que el resultado como, por ejemplo "H1" aquí.
6. Haga clic en **predecir**.

    ![Predecir sección][02]

## <a name="steps-to-add-a-new-web-service"></a>Pasos para agregar un servicio Web nueva

Implementar un servicio Web o usar un servicio Web existente. Para obtener más información sobre cómo implementar un servicio Web, consulte [tutorial paso 5: implementar el servicio Web de aprendizaje de Azure máquina](machine-learning-walkthrough-5-publish-web-service.md).

Obtener la clave de la API del servicio Web. Dónde de hacerlo depende de si ha publicado un servicio Web de aprendizaje de máquina clásico de un servicio Web de aprendizaje de nuevo equipo.

**Usar un servicio Web clásico** 

1. En Studio de aprendizaje del equipo, haga clic en la sección de **Servicios WEB** en el panel izquierdo y, a continuación, seleccione el servicio Web.

    ![Seleccione Studio un servicio Web][04]

2. Copie la clave de la API del servicio Web.

    ![Clave Studio API][05]

3. En la pestaña **panel** para el servicio Web, haga clic en el vínculo de la **Solicitud y respuesta** .
4. Busque la sección de **URI de solicitud** .  Copie y guarde la dirección URL.

>[AZURE.NOTE] Ahora es posible iniciar sesión en el portal de [Servicios Web de Azure máquina aprendizaje](https://services.azureml.net) para obtener la clave de la API de un servicio Web de aprendizaje de máquina clásico.

**Usar un servicio Web nueva**

1. En el portal de [Servicios Web de Azure máquina aprendizaje](https://services.azureml.net) , haga clic en **Servicios Web**, seleccione el servicio Web. 
2. Haga clic en **consumir**.
3. Busque la sección **información de consumo básico** . Copie y guarde la **Clave principal** y la dirección URL de **Respuesta de la solicitud** .


## <a name="steps-to-add-a-new-web-service"></a>Pasos para agregar un servicio Web nueva

1. Implementar un servicio Web o usar un servicio Web existente. Para obtener más información sobre cómo implementar un servicio Web, consulte [tutorial paso 5: implementar el servicio Web de aprendizaje de Azure máquina](machine-learning-walkthrough-5-publish-web-service.md).
2. Haga clic en **consumir**.
3. Busque la sección **información de consumo básico** . Copie y guarde la **Clave principal** y la dirección URL de **Respuesta de la solicitud** .
2. En Excel, vaya a la sección de **Servicios Web** (si se encuentra en la sección de **predicción** , haga clic en la flecha Atrás para ir a la lista de servicios Web).

    ![Vaya a la selección de servicio Web][03]
    
3. Haga clic en **Agregar servicio Web**.
4. Pegue la dirección URL en el cuadro de texto complemento de Excel denominado **dirección URL**.
5. Pegue la clave principal o API en el cuadro de texto denominado **clave API**.
6. Haga clic en **Agregar**.

    ![Tecla de dirección URL y la API de un servicio Web clásico.][06]

10. Para usar el servicio Web, siga las instrucciones anteriores, "Pasos para usar un servicio de Web existente".

## <a name="sharing-your-workbook"></a>Compartir un libro

Si guarda el libro, también se guarda la clave principal o API para los servicios Web que se ha agregado. Esto significa que solo debe compartir el libro con personas de que confianza.

Hacer preguntas en la sección de comentario siguiente o en nuestro [foro](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png
