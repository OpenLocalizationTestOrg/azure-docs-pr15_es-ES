<properties 
    pageTitle="Enviar notificación personalizada con compromiso de móvil de Azure" 
    description="Cómo enviar notificaciones personalizadas incluyendo información de perfil de usuario en las notificaciones como sus nombres"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="all" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="personalize-notifications-by-including-user-name"></a>Para personalizar las notificaciones, incluido el nombre de usuario

En la búsqueda para hacer que las notificaciones una apariencia más atractiva a los usuarios de la aplicación, considere la posibilidad de personalizar ellos. Un enfoque eficaz se compone de forma selectiva usando los nombres de los usuarios de la aplicación para las notificaciones para hacerlos más personal de direcciones. Una palabra de precaución aquí - se deben enfocar agregue los nombres de usuario para las notificaciones cuidadosamente porque si utiliza en exceso esta estrategia, a continuación, podría encuentre como asqueroso para algunos usuarios de la aplicación. También debe asegurarse de que son dejar que el usuario participar en proporcionarle estos detalles personales con consentimiento total en la aplicación móvil antes de empezar a usar esta. 

Técnicamente, con compromiso de Azure Mobile, puede realizar personalizar las notificaciones siguiendo los pasos que se indican en el que se utilizará el escenario de nombre de usuario, incluidos en las notificaciones. Usará el concepto de información de la aplicación o etiquetas cuyos valores se puede pasar por el SDK integrado en la aplicación móvil o a través de la API. A continuación, se utiliza estas informaciones de aplicación o etiquetas:

1. para las notificaciones a usuarios específicos en función de los valores de la información de la aplicación de destino o 
2. como marcadores de posición de las notificaciones que se reemplazará con los valores específicos del dispositivo o usuario al enviar notificaciones a ese dispositivo. 

> [AZURE.IMPORTANT] Observe que la velocidad de envío de notificaciones aparecerá una reducción debido a este proceso adicional de reemplazar los valores de la información de la aplicación con cada notificaciones. 

##<a name="register-app-info-in-the-mobile-engagement-portal"></a>Registrar información de la aplicación en el Portal de contratación móvil

1) Empezar con el registro de información de la aplicación o etiquetas en el portal de Azure. Vaya a **configuración** -> **etiqueta (información de la aplicación)** para este.  

![][1]  

2) Haga clic en **nueva etiqueta (información de la aplicación)** y proporcionar el nombre como *nombre de usuario* y el tipo de *cadena* y haga clic en **Enviar**. 

![][2]

3) Por último, verá esta información de aplicación registrada como el siguiente:

![][3]

##<a name="send-app-info-from-the-client-sdk"></a>Enviar información de la aplicación desde el SDK de cliente

Aquí estamos usando el ejemplo de aplicación de Windows Universal definiendo métodos equivalentes para nuestra otros SDK también. 

Suponiendo que dispone de un método en la aplicación móvil donde obtener la información del perfil del usuario como sus nombres probablemente después de autenticar ellos, llamará `SendAppInfo` método aquí y rellenar el valor de la `user_name` información de aplicación registrado anteriormente en el servicio de contratación Mobile back-end. 

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

##<a name="send-personalized-notifications"></a>Enviar notificaciones personalizadas

Ahora están todos configurados para enviar notificaciones con este **nombre de usuario**. 

1) Vaya al Portal de compromiso de móvil en la pestaña **llegar a** crear una notificación y puede utilizar este marcador de posición en el siguiente formato en cualquier lugar en el título de la notificación o el cuerpo. 

![][4]  

> [AZURE.NOTE] Los usuarios que no se establece la información de la aplicación de nombreDeUsuario, no recibirá ninguna notificación. Si ejecuta la campaña de notificación en modo de prueba y no tiene información de app establecer, a continuación, le enviaremos '?' carácter para reemplazar el marcador de posición. 

2) Cuando Mobile compromiso se selecciona un dispositivo para enviar esta notificación, a continuación, se mire esta información de la aplicación y reemplace el valor del marcador de posición.  
Por ejemplo, si hemos establecido `str = "Scott"` para un usuario que el dispositivo de registro obtendrán asociado a la información de la aplicación de **nombreDeUsuario = SCOTT** para este usuario y este usuario verán una fuera de la notificación de inserción de la aplicación en el siguiente formato. 

![][5]  

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png

