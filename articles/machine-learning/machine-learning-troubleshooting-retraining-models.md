<properties
    pageTitle="Solución de problemas de la Retraining de un servicio Web clásico de aprendizaje de máquina de Azure | Microsoft Azure"
    description="Identificar y corregir encontrado comunes de problemas cuando se reciclaje el modelo de un servicio Web de Azure máquina aprendizaje."
    services="machine-learning"
    documentationCenter=""
    authors="VDonGlover"
   manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Solución de problemas de reciclaje de un servicio Web clásico de aprendizaje de máquina de Azure

## <a name="retraining-overview"></a>Información general de formación

Cuando se implementa un ensayo predictivo como un servicio web puntuación es un modelo estático. Nuevos datos vuelve a estar disponibles o cuando el usuario de la API tiene sus propios datos, el modelo debe ser correo. 

Para obtener un tutorial completo del proceso de reconversión de un servicio Web clásico, vea [Reciclaje máquina aprendizaje modelos mediante programación](machine-learning-retrain-models-programmatically.md).

## <a name="retraining-process"></a>Proceso de reciclaje

Cuando necesite el servicio Web de reciclaje, debe agregar algunos elementos adicionales:

* Un servicio Web implementado desde el ensayo de aprendizaje. El ensayo debe tener un módulo de **Salida del servicio Web** adjunto a la salida del módulo de **Modelo de tren** .  

    ![Adjuntar el resultado del servicio web en el modelo de tren.][image1]

* Un nuevo punto final agregado a su servicio Web puntuación.  Puede agregar el extremo mediante programación utilizando el código de ejemplo que se hace referencia en los modelos de aprendizaje de reciclaje mediante programación tema o a través del portal clásico Azure.

A continuación, puede usar el código de ejemplo C# desde la página de Ayuda de API del servicio Web de aprendizaje al modelo de reciclaje. Una vez que haya evaluado los resultados y esté satisfecho con ellos, actualizar el modelo capacitado puntuación servicio web usando el nuevo extremo que ha agregado.

Con todos los elementos en su lugar, los pasos principales que debe realizar para el modelo de reciclaje son las siguientes:

1.  Llamar al servicio Web de aprendizaje: la llamada es para el lote de ejecución servicio (BES), no la solicitud de respuesta servicio (RR). Puede usar el código C# de ejemplo en la página de Ayuda de la API para realizar la llamada. 
2.  Buscar los valores para *BaseLocation*, *RelativeLocation*y *SasBlobToken*: estos valores se devuelven en el resultado de la llamada al servicio Web de aprendizaje. 
      ![Muestra el resultado de la muestra de reconversión y los valores BaseLocation, RelativeLocation y SasBlobToken.][image6]
3.  Actualizar el extremo del servicio web puntuación agregado con el nuevo modelo de formación: usar el código de ejemplo que se proporcionan en los modelos de aprendizaje de reciclaje mediante programación, actualizar el nuevo extremo agregado al modelo de puntuación con el modelo recién cualificado del servicio Web de aprendizaje.

## <a name="common-obstacles"></a>Obstáculos más comunes

### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Compruebe si tiene la dirección URL correcta de revisiones

La dirección URL de revisiones que usa debe ser la asociado con el extremo de puntuación nuevo que haya agregado a la puntuación servicio Web. Hay varias maneras de obtener la dirección URL de revisión:

**Opción 1: mediante programación**

Para obtener la dirección URL correcta de revisiones:

1.  Ejecutar el código de ejemplo [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
2.  Desde la salida de AddEndpoint, busque el valor de *HelpLocation* y copie la dirección URL.

    ![HelpLocation en los resultados de la muestra addEndpoint.][image2]

3.  Pegue la dirección URL en un explorador para ir a una página que proporciona vínculos de la ayuda para el servicio Web.
4.  Haga clic en el vínculo de **Recursos de actualización** para abrir la página de Ayuda de revisión.

**Opción 2: Usar el portal de clásico de Azure**

1.  Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com).
2.  Abra la pestaña de aprendizaje del equipo. 
     ![Ficha de inclinada del equipo.][image4]
3.  Haga clic en el nombre de área de trabajo, a continuación, **Los servicios Web**.
4.  Haga clic en el servicio Web puntuación que está trabajando. (Si no modificó el nombre predeterminado del servicio web, terminará en [puntuación Exp].)
5.  Haga clic en **Agregar extremo**.
6.  Después de agrega el punto final, haga clic en el nombre del extremo. A continuación, haga clic en **Recurso de actualización** para abrir la página de Ayuda de revisión.

>[AZURE.NOTE] Si ha agregado el extremo del servicio Web de formación en lugar del servicio Web predictiva, recibirá el siguiente error al hacer clic en el vínculo de **Recursos de actualización** : lo sentimos, pero esta característica no está disponible en este contexto o no compatible. Este servicio Web no tiene actualizables recursos. Hemos interrumpido y estamos trabajando en mejorar este flujo de trabajo.

![Nuevo panel de extremo.][image3]

La página de Ayuda de revisión contiene la dirección URL de revisiones debe utilizar y proporciona código de ejemplo que se puede usar para llamar.

![Dirección URL de revisión.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Compruebe que está actualizando el extremo correcto de puntuación

* No revisar el servicio Web de aprendizaje: debe realizarse la operación de revisión en el servicio Web de resultados.
* No revisar el extremo predeterminado en el servicio Web: la operación de revisión debe realizarse en el extremo de servicio de Web puntuación nuevo que haya agregado.

Puede comprobar qué servicio Web el extremo es en visitando el portal de clásico de Azure. 

>[AZURE.NOTE] Asegúrese de que agrega el extremo al servicio Web predictiva, no el servicio Web de aprendizaje. Si ha implementado correctamente una formación y un servicio Web predictiva, verá dos servicios Web independientes que aparece. El servicio Web predictiva debe terminar en "[exp predictiva.]".

1.  Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com).
2.  Abra la pestaña de aprendizaje del equipo. 
     ![Área de trabajo de aprendizaje de máquina interfaz de usuario.][image4]
3.  Seleccione el área de trabajo.
4.  Haga clic en **servicios Web**.
5.  Seleccione el servicio Web predictiva.
6.  Compruebe que se ha agregado el nuevo extremo del servicio Web.

### <a name="check-the-workspace-that-your-web-service-is-in-to-ensure-it-is-in-the-correct-region"></a>Comprobar el área de trabajo que el servicio web para asegurarse de que está en el área correcto

1.  Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com).
2.  Seleccione el aprendizaje en el menú.
      ![Región de aprendizaje de máquina interfaz de usuario.][image4]
3.  Compruebe la ubicación del área de trabajo.

<!-- Image Links -->

[image1]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/machine-learning-troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/machine-learning-troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/machine-learning-troubleshooting-retraining-a-model/web-services-tab.png