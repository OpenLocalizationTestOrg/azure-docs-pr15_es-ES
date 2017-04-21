<properties
    pageTitle="Agregar un retraso en las aplicaciones de lógica | Microsoft Azure"
    description="Información general sobre el retraso y retardo-hasta cómo usarlas con una aplicación de Azure lógica y acciones."
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

# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Empezar a usar el retraso y retraso-hasta acciones

Usando el retraso y "retraso-hasta" acciones, puede completar los escenarios de flujo de trabajo.

Por ejemplo, puede:

- Espere hasta un día laborable para enviar una actualización de estado a través de correo electrónico.
- Retrasar el flujo de trabajo hasta que una llamada HTTP tiene tiempo para finalizar antes de reanudar y recuperar el resultado.

Para empezar a usar la acción de retraso en una aplicación de lógica, consulte [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-delay-actions"></a>Usar las acciones de retraso

Una acción es una operación que se lleva a cabo por el flujo de trabajo que se define en una aplicación de lógica. [Más información acerca de las acciones](connectors-overview.md).

Esto es una secuencia de ejemplo de cómo usar un paso de retraso en una aplicación de lógica:

1. Después de agregar un desencadenador, haga clic en **Nuevo paso** para agregar una acción.
2. Buscar **retraso** para que aparezca las acciones de retraso. En este ejemplo, seleccionaremos **retraso**.

    ![Acciones de retraso](./media/connectors-native-delay/using-action-1.png)

3. Realice cualquiera de las propiedades de la acción para configurar el tiempo de retraso.

    ![Configuración de retraso](./media/connectors-native-delay/using-action-2.png)

4. Haga clic en **Guardar** para publicar y activar la aplicación de la lógica.


## <a name="action-details"></a>Detalles de acción

El desencadenador de periodicidad tiene las siguientes propiedades que se pueden configurar.

### <a name="delay-action"></a>Acción de retraso

Esta acción retrasos en la ejecución de un intervalo de tiempo determinado.
A * significa que es un campo obligatorio.

|Nombre para mostrar|Nombre de propiedad|Descripción|
|---|---|---|
|Recuento *|recuento|El número de unidades de tiempo de demora|
|Unidad *|unidad|La unidad de tiempo: `Second`, `Minute`, `Hour`, o`Day`|
<br>

### <a name="delay-until-action"></a>Retraso-hasta la acción

Esta acción retrasos en la ejecución de una fecha y hora especificada.
A * significa que es un campo obligatorio.

|Nombre para mostrar|Nombre de propiedad|Descripción|
|---|---|---|
|Año *|marca de tiempo|El año de demora hasta (GMT)|
|Mes *|marca de tiempo|El mes de demora hasta (GMT)|
|Día *|marca de tiempo|El día de demora hasta (GMT)|
<br>


## <a name="next-steps"></a>Pasos siguientes

Ahora, pruebe la plataforma y [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Puede explorar los otros conectores disponibles en las aplicaciones de lógica consultando nuestra [lista de API](apis-list.md).
