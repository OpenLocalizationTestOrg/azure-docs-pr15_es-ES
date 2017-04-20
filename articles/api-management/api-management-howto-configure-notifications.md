<properties 
    pageTitle="Cómo configurar las notificaciones y plantillas de correo electrónico en administración de la API de Azure" 
    description="Obtenga información sobre cómo configurar las notificaciones y plantillas en Azure API de administración de correo electrónico." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Cómo configurar las notificaciones y plantillas de correo electrónico en administración de la API de Azure

Administración de la API proporciona la capacidad para configurar las notificaciones de eventos específicos y para configurar las plantillas de correo electrónico que se usan para comunicarse con los administradores y los desarrolladores de una instancia de administración de la API. Este tema muestra cómo configurar las notificaciones de los eventos disponibles y ofrece una descripción general de la configuración de las plantillas de correo electrónico utilizadas para estos eventos.

## <a name="publisher-notifications"> </a>Configurar notificaciones de publisher

Para configurar las notificaciones, haga clic en **Administrar** en el Portal de Azure clásico de su servicio de administración de la API. Se abrirá el portal de administración de la API de publisher.

![Portal de Publisher][api-management-management-console]

>Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia de servicio de administración de la API][] en el tutorial de [Introducción a la administración de la API de Azure][] .

En el menú de **Administración de la API** de la izquierda para ver las notificaciones disponibles, haga clic en **notificaciones** .

![Notificaciones de Publisher][api-management-publisher-notifications]

La siguiente lista de eventos se puede configurar para las notificaciones.

-   **Solicitudes de suscripción (que requieren aprobación)** - los usuarios y los destinatarios de correo electrónico especificada recibirá notificaciones de correo electrónico acerca de las solicitudes de suscripción para productos de API requerir aprobación.
-   **Suscripciones nuevas** - los usuarios y los destinatarios de correo electrónico especificada recibirá notificaciones de correo electrónico acerca de las suscripciones API de producto nuevas.
-   **Las solicitudes de galería de aplicación** : los usuarios y los destinatarios de correo electrónico especificada recibirá notificaciones de correo electrónico cuando se envían las nuevas aplicaciones a la Galería de aplicación.
-   **CCO** : los usuarios y los destinatarios de correo electrónico especificada recibirá copias ocultas de correo electrónico de todos los correos electrónicos enviados a los desarrolladores.
-   **Nuevo problema o comentario** - los usuarios y los destinatarios de correo electrónico especificada recibirá notificaciones de correo electrónico cuando un problema de nuevo o se envía el comentario en el portal de programadores.
-   **Mensaje de cerrar cuenta** - los usuarios y los destinatarios de correo electrónico especificada recibirá notificaciones de correo electrónico cuando se cierra una cuenta.
-   **Límite de cuota de suscripción Approaching** - los siguientes destinatarios de correo electrónico y los usuarios recibirán notificaciones de correo electrónico al uso de la suscripción se obtiene cerca de la cuota de uso.

Para cada evento, puede especificar a los destinatarios de correo electrónico mediante el cuadro de texto dirección de correo electrónico o puede seleccionar los usuarios de una lista.

Para especificar las direcciones de correo electrónico que se le notifique, escriba en el cuadro de texto dirección de correo electrónico. Si tiene varias direcciones de correo electrónico, sepárelos con comas.

![Destinatarios de notificación][api-management-email-addresses]

Para especificar los usuarios que se le notifique, haga clic en **Agregar a destinatario**, active la casilla junto a los usuarios que se le notifique y haga clic en **Aceptar**.

>Tenga en cuenta que solo los administradores se muestran en la lista.

Después de configurar a los destinatarios de notificación, haga clic en **Guardar** para aplicar a los destinatarios de notificación actualizada.

>Si sale de la ficha **Notificaciones de Publisher** el portal de publisher le avisa si hay cambios sin guardar.

## <a name="email-templates"> </a>Configurar plantillas de correo electrónico

Administración de la API proporciona plantillas de correo electrónico de los mensajes de correo electrónico que se envían durante la administración y usando el servicio. Se proporcionan las siguientes plantillas de correo electrónico.

-   Envío de la Galería de aplicación aprobado
-   Letra despedida de desarrollador
-   Límite de cuota de desarrollador alcanzar notificación
-   Invitar a usuarios
-   Nuevo comentario agregado a un problema
-   Nuevo problema recibido
-   Nueva suscripción activa
-   Confirmación de la suscripción se renueve
-   Rechaza la solicitud de suscripción
-   Solicitud de suscripción recibida

Estas plantillas se pueden modificar como se desee.

Para ver y configurar las plantillas de correo electrónico para su instancia de administración de la API, haga clic en **notificaciones** en el menú de **Administración de la API** de la izquierda y seleccione la ficha **Plantillas de correo electrónico** .

![Plantillas de correo electrónico][api-management-email-templates]

Para ver o modificar una plantilla determinada, selecciónela en la lista desplegable de **plantillas** .

![Lista de plantillas de correo electrónico][api-management-email-templates-list]

Cada plantilla de correo electrónico tiene un asunto en texto sin formato y a continuación, una definición de cuerpo en formato HTML. Cada elemento que se puede personalizar como se desee.

![Editor de plantillas de correo electrónico][api-management-email-template]

La lista de **parámetros** contiene una lista de parámetros, que, cuando se inserta en el asunto o el cuerpo, estará reemplaza el valor designado cuando se envía el correo electrónico. Para insertar un parámetro, coloque el cursor donde desea que el parámetro para ir y haga clic en la flecha situada a la izquierda del nombre del parámetro.

Haga clic en **vista previa** o **Enviar una prueba** para ver cómo el correo electrónico se buscar o enviar un correo electrónico de prueba.

>Tenga en cuenta que los parámetros no se reemplazan por los valores reales al obtener una vista previa o enviar una prueba.
>
>Para guardar los cambios a la plantilla de correo electrónico, haga clic en **Guardar**, o para cancelar los cambios, haga clic en **Cancelar**.



[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Introducción a la administración de la API de Azure]: api-management-get-started.md
[Crear una instancia de servicio de administración de la API]: api-management-get-started.md#create-service-instance