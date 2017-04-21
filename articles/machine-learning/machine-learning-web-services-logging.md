<properties 
    pageTitle="Registro de servicios web de aprendizaje de equipo | Microsoft Azure" 
    description="Obtenga información sobre cómo habilitar el registro para los servicios web de aprendizaje del equipo. Registro de información proporciona información adicional para ayudar a solucionar problemas de las API." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data" 
    ms.date="10/05/2016"
    ms.author="raymondl;garye"/>

# <a name="enable-logging-for-machine-learning-web-services"></a>Habilitar el registro para los servicios web de aprendizaje de equipo  

Este documento proporciona información sobre la capacidad de registro de servicios Web clásica. Habilitar el registro de los servicios Web proporciona información adicional, más allá de solo un número de error y un mensaje, que puede ayudar a solucionar problemas de las llamadas a las API de aprendizaje del equipo.  

Para habilitar el registro de los servicios Web en el portal de clásico Azure:   

1.  Inicie sesión en [Azure portal clásica](https://manage.windowsazure.com/)
2.  En la columna izquierda de las características, haga clic en **El aprendizaje**.
3.  Haga clic en el área de trabajo, a continuación, **Los servicios WEB**.
4.  En la lista de servicios Web, haga clic en el nombre del servicio Web.
5.  En la lista de extremos, haga clic en el nombre del extremo.
6.  Haga clic en **Configurar**.
7.  Establecer **Nivel de seguimiento de diagnóstico** a *todos*o de *Error* y luego haga clic en **Guardar**.

Para habilitar el registro en el portal de servicios Web de Azure máquina aprendizaje.

1. Iniciar sesión en el portal de [Servicios Web de Azure máquina aprendizaje](https://services.azureml.net) .
2. Haga clic en servicios Web clásica.
3.  En la lista de servicios Web, haga clic en el nombre del servicio Web.
4.  En la lista de extremos, haga clic en el nombre del extremo.
5.  Haga clic en **Configurar**.
6.  Establezca el **registro de** *Error* o *todos*, haga clic en **Guardar**.

## <a name="the-effects-of-enabling-logging"></a>Los efectos de habilitar el registro

Cuando se habilita el registro, todos los errores desde el extremo seleccionado y diagnósticos se registran en la cuenta de almacenamiento de Azure vinculan con el área de trabajo del usuario. Puede ver esta cuenta de almacenamiento en el portal de clásica Azure vista de paneles (parte inferior de la sección Eche un vistazo rápido) de su área de trabajo.  

Los registros pueden verse con cualquiera de las diversas herramientas disponibles para una cuenta de almacenamiento de Azure 'explorar'. La forma más fácil puede ser simplemente vaya a la cuenta de almacenamiento en el portal de clásico Azure y, a continuación, haga clic en **contenedores**. A continuación, verá un contenedor denominado **m diagnósticos**. Este contenedor contiene toda la información de diagnóstico para todos los extremos de servicio web para todas las áreas de trabajo asociadas a esta cuenta de almacenamiento. 
 
## <a name="log-blob-detail-information"></a>Información de detalle de blobs de registro

Cada blob en el contenedor contiene la información de diagnóstico exactamente uno de estos procedimientos:

-   Ejecución del método de ejecución por lotes  
-   Ejecución del método de solicitud de respuesta  
-   Inicialización de un contenedor de solicitud de respuesta
  
El nombre de cada blob tiene un prefijo de la forma siguiente: 

{Id. de área de trabajo}-{Id. de servicio Web}-{identificador de extremo} / {tipo de registro}  

En tipo de registro es uno de los siguientes valores:  

- proceso por lotes  
- puntuación/solicitudes  
- puntuación/inicialización  