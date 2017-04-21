<properties
    pageTitle="Procedimientos de actualización de Mobile compromiso Web SDK Azure | Microsoft Azure"
    description="Las últimas actualizaciones y procedimientos para el SDK de Web para Azure Mobile contratación"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="piyushjo" />


# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Procedimientos de actualización de Azure Mobile compromiso Web SDK

Si ya ha integrado una versión anterior de Azure Mobile compromiso Web SDK en la aplicación web, debe tener en cuenta los siguientes puntos al actualizar el SDK.

Si omite varias versiones de Mobile compromiso Web SDK, debe completar varios procedimientos durante el proceso de actualización. Por ejemplo, si se migra de 1.4.0 a 1.6.0, primero siga los procedimientos para actualizar 1.4.0 a 1.5.0. A continuación, siga los procedimientos para actualizar 1.5.0 a 1.6.0.

Cualquier versión actualizar desde, reemplace cualquier versión anterior de la engagement.js de azure de archivo con la versión más reciente del archivo.

## <a name="upgrade-from-121-to-200"></a>Actualizar 1.2.1 a 2.0.0

Esta sección describe cómo migrar una integración Mobile compromiso Web SDK desde el servicio de Capptain Capptain SAS, ofrecido a una aplicación de Azure Mobile compromiso. Si va a migrar desde una versión anterior, póngase en contacto con el sitio Web de Capptain para migrar a 1.2.1 y, a continuación, aplique los siguientes procedimientos.

No es compatible con esta versión de Mobile compromiso Web SDK Samsung inteligente TV, Opera TV, webOS o la característica de alcance.

>[AZURE.IMPORTANT] Capptain y compromiso de Azure Mobile no son el mismo servicio. El procedimiento siguiente resalta solo cómo migrar la aplicación de cliente. Migrar el SDK de Mobile compromiso Web en la aplicación no se migrarán los datos desde un servidor de Capptain a un servidor de compromiso de móvil.

### <a name="javascript-files"></a>Archivos de JavaScript

Reemplazar el archivo capptain-sdk.js con la engagement.js de azure de archivo y, a continuación, actualice la importación de secuencia de comandos.

### <a name="remove-capptain-reach"></a>Quitar alcance Capptain

Esta versión del SDK Mobile compromiso Web no admite la característica de alcance. Si había integrado Capptain alcance en la aplicación, debe eliminarlo.

Quitar la importación de CSS alcance de la página y elimine el archivo .css relacionados (capptain-reach.css, de forma predeterminada).

Eliminar los siguientes recursos de alcance: la imagen cerrar (capptain-close.png, de forma predeterminada) y el icono de marca (icono capptain del notificación, de forma predeterminada).

Quitar la interfaz de usuario de llegar a las notificaciones de la aplicación. El diseño predeterminado es similar a esta:

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

Quitar la interfaz de usuario alcance de texto y anuncios de web y sondeos. El diseño predeterminado es similar a esta:

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

Quitar el `reach` objeto de la configuración, si existe. Aparece así:

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Quitar otra personalización alcance, como categorías.

### <a name="remove-deprecated-apis"></a>Quitar las API de desusados

Algunas API de Capptain está obsoletos en el SDK de Web Mobile compromiso.

Quite todas las llamadas a las siguientes API: `agent.connect`, `agent.disconnect`, `agent.pause`, y `agent.sendMessageToDevice`.

Quitar todas las instancias de las siguientes devoluciones de llamada de la configuración de Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, y `onPushMessageReceived`.

### <a name="configuration"></a>Configuración

Compromiso de Mobile utiliza una cadena de conexión para configurar identificadores SDK, por ejemplo, el identificador de aplicación.

Reemplazar el identificador de la aplicación con la cadena de conexión. Tenga en cuenta que el objeto global para la configuración de SDK cambia de `capptain` a `azureEngagement`.

Antes de la migración:

    window.capptain = {
      appId: ...,
      [...]
    };

Después de la migración:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

La cadena de conexión de la aplicación se muestra en el Portal de Azure.

### <a name="javascript-apis"></a>API de JavaScript

El objeto de JavaScript global `window.capptain` se cambió `window.azureEngagement` , pero puede usar el `window.engagement` alias para las llamadas de API. No puede utilizar el alias para definir la configuración de SDK.

Por ejemplo, `capptain.deviceId` se convierte en `engagement.deviceId`, `capptain.agent.startActivity` se convierte en `engagement.agent.startActivity`y así sucesivamente.
