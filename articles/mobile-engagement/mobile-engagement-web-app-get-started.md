<properties
    pageTitle="Introducción a Azure Mobile compromiso para aplicaciones Web | Microsoft Azure"
    description="Obtenga información sobre cómo usar Azure Mobile compromiso con las notificaciones de inserción y analíticas para las aplicaciones Web de."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="js"
    ms.topic="hero-article"
    ms.date="06/01/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Introducción a Azure Mobile compromiso para aplicaciones Web

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tema muestra cómo usar el compromiso de Azure Mobile para comprender el uso de la aplicación Web.

Este tutorial requiere lo siguiente:

+ 2015 de Visual Studio o cualquier otro editor de su elección
+ [Web SDK](http://aka.ms/P7b453) 

Este SDK Web está en vista previa y solo admite el análisis en el momento y no admite las notificaciones de inserción explorador o en la aplicación de envío todavía. 

> [AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).

##<a name="setup-mobile-engagement-for-your-web-app"></a>Compromiso de móvil de configuración para la aplicación Web

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar la aplicación con el compromiso de móvil de back-end

Este tutorial presenta una "integración básica", que es el conjunto mínimo necesario para recopilar datos.

Se crea una aplicación web básica con Visual Studio para demostrar la integración, aunque puede seguir los pasos con cualquier aplicación web creado fuera de Visual Studio también. 

###<a name="create-a-new-web-app"></a>Crear una nueva aplicación Web

Los siguientes pasos suponen el uso de Visual Studio de 2015, aunque los pasos son similares en versiones anteriores de Visual Studio. 

1. Inicie Visual Studio y, en la pantalla **principal** , seleccione **Nuevo proyecto**.

2. En el menú emergente, seleccione **Web** -> **Aplicación Web de ASP.Net**. Rellene la aplicación de **nombre**, **ubicación** y el **nombre de la solución**y, a continuación, haga clic en **Aceptar**.

3. En el menú emergente **Seleccione una plantilla** , seleccione **vacía** en **ASP.Net 4.5 plantillas** y haga clic en **Aceptar**. 

Acaba de crear un nuevo proyecto de aplicación Web en blanco en el que se integrará el SDK de Azure Mobile compromiso Web.

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar la aplicación a Mobile compromiso back-end

1. Crear una nueva carpeta llamada **javascript** en la solución y agregue el archivo de Web SDK JS **engagement.js de azure** en él. 

2. Agregar un nuevo archivo llamado **main.js** en esta carpeta de javascript con el siguiente código. Asegúrese de actualizar la cadena de conexión. Esto `azureEngagement` objeto se usará para obtener acceso a métodos Web SDK. 

        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };

    ![Visual Studio con archivos de js][1]

##<a name="enable-real-time-monitoring"></a>Habilitar la supervisión en tiempo real

Para iniciar el envío de datos y garantizar que los usuarios están activos, debe enviar al menos una actividad del back-end de compromiso de móvil. Una actividad en el contexto de una aplicación web es una página web. 

1. Crear una nueva página denominada **home.html** en la solución y establecerlo como la página de inicio para la aplicación web. 
2. Incluir los dos archivos de JavaScript que hemos agregado anteriormente en esta página agregando el siguiente dentro de la etiqueta del cuerpo. 

        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>

3. Actualizar la etiqueta de cuerpo para llamar de EngagementAgent `startActivity` método
        
        <body onload="engagement.agent.startActivity('Home')">

4. Esto es el aspecto que tendrá su **home.html**
        
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

##<a name="connect-app-with-real-time-monitoring"></a>Conectar la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

![][2]

##<a name="extend-analytics"></a>Ampliar el análisis

Estas son están disponibles con el SDK de Web que puede usar para el análisis de todos los métodos:

1. Páginas Web y actividades:

        engagement.agent.startActivity(name);
        engagement.agent.endActivity();

2. Eventos
        
        engagement.agent.sendEvent(name, extras);

3. Errores

        engagement.agent.sendError(name, extras);

4. Trabajos

        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

