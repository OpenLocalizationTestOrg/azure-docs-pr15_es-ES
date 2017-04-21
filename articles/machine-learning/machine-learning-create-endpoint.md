<properties
    pageTitle="Crear extremos de servicios Web en el aprendizaje | Microsoft Azure"
    description="Crear extremos de servicios Web de aprendizaje del equipo de Azure"
    services="machine-learning"
    documentationCenter=""
    authors="hiteshmadan"
    manager="padou"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="10/04/2016"
    ms.author="himad"/>


# <a name="creating-endpoints"></a>Creación de extremos

>[AZURE.NOTE] Este tema describe técnicas aplicables a un servicio Web clásico.

Al crear servicios Web que se venden reenviar a los clientes, debe proporcionar modelos capacitados a cada cliente que aún están vinculadas a la ensayo desde la que se creó el servicio Web. Además, las actualizaciones para el ensayo deben aplicarse selectivamente a un punto final sin sobrescribir las personalizaciones.

Para ello, el aprendizaje Azure le permite crear varios extremos de un servicio Web implementado. Cada extremo del servicio Web por separado es dirigido, acelerado y administrada. Cada extremo es una dirección URL única y una clave de autorización que puede distribuir a sus clientes.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Agregar extremos a un servicio Web

Hay tres maneras de agregar un punto final a un servicio Web.

* Mediante programación
* A través del portal de servicios Web de Azure máquina aprendizaje
* Aunque el portal de clásico de Azure

Una vez creado el extremo, puede consumir a través de la API sincrónicas, lote API y hojas de cálculo de excel. Además de agregar extremos a través de esta interfaz de usuario, también puede usar las API de administración de extremo mediante programación agregar extremos.

 >[AZURE.NOTE] Si ha agregado extremos adicionales al servicio Web, no puede eliminar el extremo predeterminado.

## <a name="adding-an-endpoint-programmatically"></a>Agregar un extremo mediante programación

Puede agregar un extremo al servicio Web mediante programación utilizando el código de ejemplo [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Agregar un extremo con el portal de servicios Web de Azure máquina aprendizaje

1. Studio de aprendizaje del equipo, en la columna de navegación izquierdo, haga clic en servicios Web.
2. En la parte inferior del panel de servicio Web, haga clic en **Administrar extremos**. El portal de servicios Web de Azure máquina aprendizaje se abrirá la página de extremos para el servicio Web.
3. Haga clic en **nuevo**.
4. Escriba un nombre y una descripción para el nuevo extremo. Los nombres de extremo deben ser 24 caracteres o menos de longitud y deben estar formados por un alfabeto minúscula o números. Seleccione el nivel de registro y si se habilita los datos de ejemplo. Para obtener más información sobre el registro, vea [Habilitar el registro para los servicios Web de aprendizaje del equipo](machine-learning-web-services-logging.md).

## <a name="adding-an-endpoint-using-the-azure-classic-portal"></a>Agregar un extremo con el portal de clásico de Azure


1. Inicie sesión en el [portal de clásica Azure](http://manage.windowsazure.com), haga clic en **El aprendizaje** en la columna izquierda. Haga clic en el área de trabajo que contiene el servicio Web en el que está interesado.

    ![Vaya al área de trabajo](./media/machine-learning-create-endpoint/figure-1.png)

2. Haga clic en **servicios Web**.

    ![Vaya a servicios Web](./media/machine-learning-create-endpoint/figure-2.png)

3. Haga clic en el servicio Web que le interesa para ver la lista de extremos disponibles.

    ![Desplácese al extremo](./media/machine-learning-create-endpoint/figure-3.png)

4. En la parte inferior de la página, haga clic en **Agregar extremo**. Escriba un nombre y una descripción, asegúrese de que no hay ningún otros extremos con el mismo nombre en este servicio Web. A menos que tenga necesidades especiales, deje el nivel de acelerador con su valor predeterminado. Para obtener más información acerca del límite, consulte [Escala API extremos](machine-learning-scaling-webservice.md).

    ![Crear punto final](./media/machine-learning-create-endpoint/figure-4.png)

## <a name="next-steps"></a>Pasos siguientes

[Cómo utilizar un servicio Web de aprendizaje del equipo de Azure publicado](machine-learning-consume-web-services.md).
