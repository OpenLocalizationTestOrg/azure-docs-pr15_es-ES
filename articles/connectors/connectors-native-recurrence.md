<properties
    pageTitle="Agregar el desencadenador de periodicidad en las aplicaciones de lógica | Microsoft Azure"
    description="Información general sobre el desencadenador de periodicidad y cómo usarla con una aplicación de Azure lógica."
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-recurrence-trigger"></a>Empezar a trabajar con el desencadenador de periodicidad

Con el desencadenador de periodicidad, puede crear flujos de trabajo eficaces en la nube.

Por ejemplo, puede:

- Programar un flujo de trabajo para ejecutar un procedimiento almacenado SQL cada día.
- Correo electrónico de un resumen de todos los tweets dentro de la semana pasada sobre un hashtag determinado.

Para empezar a usar el desencadenador periodicidad en una aplicación de lógica, consulte [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-a-recurrence-trigger"></a>Utilizar un desencadenador de periodicidad

Un desencadenador es un evento que se pueden usar para iniciar el flujo de trabajo que se define en una aplicación de lógica. [Obtenga más información acerca de desencadenadores](connectors-overview.md).

Esto es una secuencia de ejemplo de cómo configurar un desencadenador de periodicidad en una aplicación de lógica:

1. Agregar el desencadenador de **Periodicidad** como el primer paso en una aplicación de lógica.
2. Rellene los parámetros para el intervalo de repetición.

Ahora, la aplicación de lógica inicia una ejecución después de cada intervalo de tiempo.

![Desencadenador HTTP](./media/connectors-native-recurrence/using-trigger.png)

## <a name="trigger-details"></a>Detalles de desencadenador

El desencadenador de periodicidad tiene las siguientes propiedades que se pueden configurar.

Activa una aplicación de lógica después de un intervalo de tiempo especificado.
A * significa que es un campo obligatorio.

|Nombre para mostrar|Nombre de propiedad|Descripción|
|---|---|---|
|Frecuencia *|frecuencia|The unit of time: `Second`, `Minute`, `Hour`, `Day`, or `Year`.|
|Intervalo *|intervalo|El intervalo de la frecuencia especificada para la periodicidad.|
|Zona horaria|zona horaria|Si no se proporciona una hora de inicio sin un desplazamiento de UTC, se usará esta zona horaria.|
|Hora de inicio|hora de inicio|Hora de inicio en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).|
<br>


## <a name="next-steps"></a>Pasos siguientes

Ahora, pruebe la plataforma y [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Puede explorar los otros conectores disponibles en las aplicaciones de lógica consultando nuestra [lista de API](apis-list.md).
