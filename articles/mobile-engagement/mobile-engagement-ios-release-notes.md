<properties
    pageTitle="Azure compromiso móvil iOS notas de la versión de SDK | Microsoft Azure"
    description="Últimas actualizaciones y procedimientos para iOS SDK para Azure Mobile contratación"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="piyushjo" />

#<a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Notas de la versión de Azure compromiso móvil iOS SDK

##<a name="400-09122016"></a>4.0.0 (09/12/2016)

-   Notificación fija no accionada en dispositivos iOS 10.
-   Descartar XCode 7.

##<a name="324-06302016"></a>3.2.4 (30/06/2016)

-   Agregación fija entre registros técnicos y otros registros.

##<a name="323-06072016"></a>3.2.3 (06/07/2016)

-   Corregir el error donde comentarios de entrega no se notifican cuando la aplicación se está ejecutando en segundo plano.
-   Optimizar el envío de registros técnicos.

##<a name="322-04072016"></a>3.2.2 (04/07/2016)

-   Error corregido en cancelación de solicitud HTTP lo que lleva a veces se bloquea.

##<a name="321-12112015"></a>3.2.1 (12/11/2015)

-   Fija el retraso cuando se activa una nueva instancia de la aplicación con una notificación con vínculos de profundidad

##<a name="320-10082015"></a>3.2.0 (08/10/2015)

-   Habilitar Bitcode en el SDK para que funcione con **Xcode 7**.
-   Errores corregidos relacionados con las notificaciones de la aplicación.
-   Realiza las notificaciones de la aplicación más fiable en caso de batería baja y otros escenarios.
-   Quita registros de consola adicionales generados por 3ª biblioteca de terceros.

##<a name="310-08262015"></a>3.1.0 (26/08/2015)

-   Corregir el error de compatibilidad de iOS 9 con una biblioteca de terceros. Ha causando bloqueos mientras enviar explora resultados, información de la aplicación o datos adicionales.

##<a name="300-06192015"></a>3.0.0 (19/06/2015)

-   Compromiso de móvil usa silencioso notificaciones de inserción.
-   En el texto de soporte técnico para iOS 4.X. Desde esta versión de destino de implementación de la aplicación debe tener al menos iOS 6.

##<a name="220-05212015"></a>2.2.0 (21/05/2015)

-   El identificador del dispositivo móvil compromiso para dispositivos < iOS 6 ahora se basa en un GUID generado durante la instalación.

##<a name="210-04242015"></a>2.1.0 (24/04/2015)

-   Compatibilidad de rápida agregada.
-   Al hacer clic en una notificación, ejecuta la acción que dirección URL es ahora derecha después de abre la aplicación.
-   Falta encabezado archivo agregado en el paquete SDK.
-   Corregir un problema cuando se haya deshabilitado el redactor de bloqueo de contratación Mobile.

##<a name="200-02172015"></a>2.0.0 (17/02/2015)

-   Versión inicial de Azure compromiso móvil
-   configuración de ID/sdkKey se reemplaza por una configuración de la cadena de conexión.
-   Quitar API para enviar y recibir mensajes XMPP arbitrarios de entidades XMPP arbitrarias.
-   Quitar API para enviar y recibir mensajes entre dispositivos.
-   Mejoras de seguridad.
-   Quita el seguimiento de SmartAd.
