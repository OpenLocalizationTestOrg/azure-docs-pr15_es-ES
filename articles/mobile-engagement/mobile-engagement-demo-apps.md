<properties
    pageTitle="Aplicación de demostración de contratación Mobile Azure | Microsoft Azure"
    description="Describe dónde descargar, cómo usar y las ventajas de usar la aplicación de demostración de compromiso de móvil de Azure"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/10/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-demo-app"></a>Aplicación de demostración de Azure compromiso de móvil

Hemos publicado una aplicación de demostración de Azure Mobile compromiso de **iOS**, **Android**y plataformas **Windows** para ayudarle a buscar recursos útiles y obtener más información sobre el compromiso de móvil.

La aplicación le ayuda a:

- Encontrar fácilmente vínculos útiles a recursos de contratación móvil como vídeos, documentación, el foro de soporte técnico y dónde ir para generar solicitudes de características.
- Notificaciones de ejemplo de experiencia que son compatibles con el compromiso de móvil para obtener ideas para sus propias aplicaciones móviles.
- Utilice una implementación de referencia para estudiar cómo implementar compromiso Mobile en su propia aplicación. Puede obtener en:

    - Recopilar datos de análisis.
    - Implementar escenarios de notificación avanzada de tipos como *intersticiales de pantalla completa* o *emergente*.
    - Implementar encuestas y sondeos.
    - Implementar escenarios de datos y la inserción de inserción silencioso.   

## <a name="app-installation"></a>Instalación de la aplicación
Esta aplicación está disponible en el siguiente almacena de aplicación:

- **Aplicación de demostración Universal de Windows**:

    - Descargue la aplicación en el [almacén de aplicación de Windows](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
    - La aplicación se ha desarrollado como una aplicación de Windows 10 Universal. El código fuente está disponible en [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).

- **aplicación de demostración de iOS**:

    - Descargue la aplicación en el [almacén de Apple](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
    - La aplicación se ha desarrollado en iOS Swift. El código fuente está disponible en [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).

- **Aplicación de demostración android**:

    - Descargue la aplicación en el [almacén de Google Play](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
    - El código fuente está disponible en [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![Aplicación de demostración Universal de Windows][1]

![aplicación de demostración de iOS][2]
![aplicación de demostración de Android][3]


## <a name="usage"></a>Uso

Puede usar esta aplicación de las siguientes maneras:

**Descargar la aplicación en el dispositivo de los vínculos de la tienda de aplicaciones (proporcionados anteriormente):**

>[AZURE.IMPORTANT] No necesita una cuenta de Azure o la necesidad de conectar la aplicación a un back-end. La aplicación funciona de forma independiente.

- Una vez que la aplicación en su dispositivo, puede ir a través de los vínculos en el menú de la izquierda para encontrar los recursos útiles sobre compromiso de móvil.
- Hemos agregado la [fuente RSS del servicio](https://aka.ms/azmerssfeed) en esta aplicación para que siempre se actualizan sobre las últimas actualizaciones de producto.
- También puede ir a través de los escenarios de notificación de muestra para experimentar el tipo de notificaciones que son compatibles con el compromiso de Mobile para cada plataforma. Estas notificaciones pueden sufrir localmente--que es, puede hacer clic en los botones en las pantallas para mostrar la experiencia de notificaciones, que es igual a enviar las notificaciones de la plataforma de compromiso de móvil.

![Menú de la aplicación para Windows][4]

![Menú de la aplicación para iOS][5]
![menú de la aplicación para Android][6]

**Descargar el código fuente de los vínculos de GitHub (proporcionados anteriormente):**

- Una vez que haya descargado el código fuente, ábralo en el entorno de desarrollo respectivos--XCode para iOS, Android Studio para Android y Visual Studio para Windows.
- A continuación debería seguir los [pasos básicos de integración de SDK](mobile-engagement-windows-store-dotnet-get-started.md) para que pueda conectarse esta aplicación a su propia instancia de back-end de compromiso de móvil.
    - Necesita configurar una cadena de conexión en la aplicación.
    - También debe configurar la plataforma de notificación de inserción de la aplicación.
- Observe que la aplicación cuenta con el compromiso de móvil. Por lo tanto, cuando se abre la aplicación después de conectar al back-end, podrá ver la sesión de usuario, actividades, eventos etc., en la pestaña **Monitor** .
- También podrá enviar notificaciones para esta aplicación desde su propia instancia de compromiso de móvil, en lugar de usar notificaciones locales.
    - Aquí puede agregar el dispositivo como un dispositivo de prueba mediante el elemento de menú **obtener el identificador de dispositivo** en la aplicación. Esto le da un identificador de dispositivo registrar, a continuación, como un dispositivo de prueba con la instancia de back-end de la plataforma.

    ![Id. de dispositivo de Windows][7]

    ![Id. de dispositivo en iOS][8]
    ![ID de dispositivo en Android][9]

## <a name="key-features-of-the-demo-app"></a>Características clave de la aplicación de demostración

- Como se mencionó anteriormente, con esta aplicación tiene todos los recursos claves para compromiso de móvil en la mano. Puede ir a través de los vínculos en el menú de la izquierda.

- Puede experimentar las notificaciones de fuera de la aplicación para cada plataforma. Estas notificaciones se pueden enviar como **sólo notificación**, donde haciendo clic en la notificación simplemente abre una pantalla de la aplicación nativa (mediante **vinculación profunda**), o como un **anuncio de Web**, donde puede ofrecer contenido HTML adicional desde el back-end compromiso móvil para que se muestre cuando se hace clic en la notificación.

    ![Notificaciones de fuera de la aplicación][29]

- En iOS, tiene que cerrar la aplicación para ver las notificaciones de inserción de la aplicación o sistema. Puede buscar en la implementación aquí para agregar **botones de acción**, como los que se agregan a esta notificación de la aplicación para *comentarios* y *Compartir* (de modo que el usuario tenga derecho de acción de la notificación).

    ![Notificaciones de fuera de la aplicación en iOS][11] ![Visualización de notificación de la aplicación en iOS][14]

- En Android, las opciones que son compatibles con agrega texto multilínea (**Texto grande**) o una imagen de notificación (**Panorama**) a la notificación, junto con los **botones de acción** (como compatible con iOS).

    ![Notificaciones de fuera de la aplicación en Android][12] ![Visualización de notificación de la aplicación en Android][15]

- En Windows 10, puede ver el aspecto de las notificaciones en el equipo. Esta notificación también aparece en el **Centro de notificaciones**de Windows 10. No hay ninguna compatibilidad para agregar **botones de acción** en el momento en el SDK de Windows.

    ![Notificaciones de fuera de la aplicación en Windows][10] ![Visualización de la aplicación en Windows][13]

- Puede experimentar notificaciones de "de la aplicación" predeterminado para cada plataforma. Esta es una experiencia de dos pasos donde se muestra una ventana de **notificación** en primer lugar. Cuando hace clic en él, se abre una pantalla completa de **anuncio**, como se muestra en la siguiente captura de pantalla. El contenido de esta presentación proviene de la instancia de back-end de compromiso de móvil. El SDK tiene las plantillas para las notificaciones y anuncios. Puede personalizar, fácilmente como se muestra en esta aplicación de demostración con la adición de nuestro logotipo y los colores.  

    ![Notificaciones de la aplicación en Windows][16]

    ![Notificaciones de la aplicación en iOS][17]  ![Notificaciones de la aplicación en Android][18]

    **iOS**, **Android**

- También puede usar la característica de **categoría** de contratación Mobile para personalizar esta experiencia predeterminado. En la aplicación de la demostración, nos hemos mostrado dos formas comunes para cambiar la experiencia de las notificaciones. Tenga en cuenta que no es compatible todavía la característica de categoría en el SDK de Windows.

    **Intersticiales de pantalla completa:**

    ![Notificación de la aplicación: categoría intersticial][30]

    ![Categoría intersticial en iOS][21]  ![Categoría intersticial en Android][22]

    **Notificación emergente:**

    ![Notificación de la aplicación: categoría emergente][31]

    ![Notificación emergente en iOS][19]   ![Notificación emergente en Android][20]

**iOS**, **Android**

- Compromiso de Mobile también es compatible con un tipo de notificación de la aplicación denominado **sondeos**especializado. Esto le permite enviar encuestas rápidas a los usuarios de la aplicación segmentado. Puede agregar preguntas y opciones para cada pregunta como en la siguiente captura de pantalla. A continuación, esta se mostrará como una notificación de la aplicación para el usuario de la aplicación.   

    ![Notificaciones de sondeo][32]

    ![Encuesta en Windows][26]

    ![IOS de encuesta][27]   ![Encuesta en Android][28]

**iOS**, **Android**

- Compromiso de Mobile también admite enviar notificaciones de **Inserción de datos** silenciosas. Con estas notificaciones, puede enviar los datos de su servicio (por ejemplo, los datos JSON en el ejemplo siguiente), que puede controlar en su aplicación y tomar algunas medidas. Un ejemplo es cómo estamos cambiando selectivamente el precio de un elemento mediante el uso de notificaciones de inserción de datos.

    ![Notificación de inserción de datos][33]

    ![Notificación de inserción de datos en Windows][23]

    ![Notificación de inserción de datos en iOS][24]  ![Notificación de inserción de datos en Android][25]

**iOS**, **Android**

> [AZURE.NOTE] Puede ver instrucciones detalladas para cualquiera de estas notificaciones haciendo clic en **haga clic aquí para obtener instrucciones sobre cómo enviar estas notificaciones de plataforma de contratación Mobile** en cualquier pantalla de notificación de muestra.


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
