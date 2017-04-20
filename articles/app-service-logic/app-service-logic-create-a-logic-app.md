<properties
    pageTitle="Crear una aplicación de lógica | Microsoft Azure"
    description="Obtenga información sobre cómo crear una aplicación de la lógica de conexión SaaS servicios"
    authors="jeffhollan"
    manager="dwrede"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="create-a-new-logic-app-connecting-saas-services"></a>Crear una nueva aplicación lógica conexión SaaS servicios

Este tema muestra cómo, en tan solo unos minutos, puede empezar a trabajar con [Aplicaciones de lógica de Azure](app-service-logic-what-are-logic-apps.md). Le guiaremos a través de un flujo de trabajo simple que le permite enviar tweets interesantes a su correo electrónico.

Para usar este escenario, necesita:

- Una suscripción de Azure
- Una cuenta de Twitter
- Un Outlook.com u hospedado buzón de Office 365

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Crear una nueva aplicación lógica para correo electrónico tweets

1. En el [panel de portal Azure](https://portal.azure.com), seleccione **nuevo**. 
2. En la barra de búsqueda, busque 'lógica app' y, a continuación, seleccione la **Aplicación de la lógica**. También puede seleccionar **nuevo** **Web + Mobile**y seleccione **Lógica de aplicación**. 
3. Escriba un nombre para la lógica de aplicación, seleccione una ubicación, el grupo de recursos y seleccione **crear**.  Si selecciona **Anclar al panel de** la aplicación de la lógica se abrirá automáticamente una vez implementado.  
4. Después de abrir la aplicación lógica por primera vez puede seleccionar desde una plantilla para empezar.  Ahora haga clic en **La aplicación de lógica en blanco** para crear esto desde cero. 
1. El primer elemento que necesita para crear es el desencadenador.  Este es el evento que se inicie la aplicación de la lógica.  Buscar en el cuadro de búsqueda de desencadenador de **twitter** y selecciónelo.
7. Ahora tendrá que escribir en un término de búsqueda para desencadenar en.  El **intervalo** de **frecuencia** y determinará la frecuencia con la aplicación de lógica buscará tweets nuevos (y todos los tweets durante el que el intervalo de tiempo de retorno).
    ![Búsqueda de Twitter](./media/app-service-logic-create-a-logic-app/twittersearch.png)

5. Seleccione el botón de **nuevo paso** y, a continuación, elija **Agregar una acción** o **Agregar una condición**
6. Cuando selecciona **Agregar una acción**, puede buscar desde los [conectores disponibles](../connectors/apis-list.md) para elegir una acción. Por ejemplo, puede seleccionar **Outlook.com: enviar correo electrónico** para enviar correo desde una dirección de outlook.com:  
    ![Acciones](./media/app-service-logic-create-a-logic-app/actions.png)

7. Ahora que tiene que rellenar los parámetros para el correo electrónico que desee:  ![parámetros](./media/app-service-logic-create-a-logic-app/parameters.png)

8. Por último, puede seleccionar **Guardar** para hacer que su aplicación lógica live.

## <a name="manage-your-logic-app-after-creation"></a>Administrar la aplicación lógica tras la creación

Ahora la aplicación lógica está funcionando. Periódicamente buscará tweets con el término de búsqueda especificado. Cuando encuentre un tweet coincidente, le enviará un correo electrónico. Por último, podrá ver cómo deshabilitar la aplicación o ver cómo está realizando cada tarea.

1. Vaya al [Portal de Azure](https://portal.azure.com)

1. Haga clic en **Examinar** en el lado izquierdo de la pantalla y seleccione **Lógica de aplicaciones**.

2. Haga clic en la nueva aplicación lógica que acaba de crear para ver el estado actual y obtener información general.

3. Para editar la nueva aplicación de lógica, haga clic en **Editar**.

5. Para desactivar la aplicación, haga clic en **Deshabilitar** en la barra de comandos.

1. Vista ejecutar y desencadenar historiales supervisar cuando se ejecuta la aplicación de la lógica.  Puede hacer clic en **Actualizar** para ver los datos más recientes.

En menos de 5 minutos podía configurar una aplicación de lógica simple en la nube. Para obtener más información sobre el uso de características de las aplicaciones de lógica, vea [características de aplicación de lógica de uso]. Para obtener información sobre las definiciones de aplicación lógica propiamente dichas, consulte [crear definiciones de lógica de aplicación](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Usar características de la aplicación de lógica]: app-service-logic-create-a-logic-app.md
