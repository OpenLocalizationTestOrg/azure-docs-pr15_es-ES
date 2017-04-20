<properties 
    pageTitle="Cómo administrar cuentas de usuario de administración de la API de Azure | Microsoft Azure" 
    description="Aprenda a crear o invitar a los usuarios de administración de la API de Azure" 
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

# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Cómo administrar cuentas de usuario de administración de la API de Azure

En administración de la API, los desarrolladores son los usuarios de la API que se exponen con la API de administración. Esta guía le muestra cómo crear e invitar a los desarrolladores usar las API y los productos que hacer a su disposición con la instancia de administración de la API. Para obtener información sobre cómo administrar las cuentas de usuario mediante programación, consulte la documentación de la [entidad de usuario](https://msdn.microsoft.com/library/azure/dn776330.aspx) en la referencia de la [API de REST de administración](https://msdn.microsoft.com/library/azure/dn776326.aspx) .

## <a name="create-developer"> </a>Crear un nuevo desarrollador

Para crear un nuevo desarrollador, haga clic en **Administrar** en el Portal de Azure clásico de su servicio de administración de la API. Se abrirá el portal de administración de la API de publisher. Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia de servicio de administración de la API][] en el tutorial de [Introducción a la administración de la API de Azure][] .

![Portal de Publisher][api-management-management-console]

Haga clic en **usuarios** en el menú de **Administración de la API** de la izquierda y, a continuación, haga clic en **Agregar usuario**.

![Crear programador][api-management-create-developer]

Escriba el **correo electrónico**, la **contraseña**y el **nombre** para el desarrollador de nuevo y haga clic en **Guardar**.

![Crear programador][api-management-add-new-user]

De forma predeterminada, programador recién creado cuentas están **activos**y asociados con el grupo de **programadores** .

![Programador nuevo][api-management-new-developer]

Cuentas de desarrollador que están en un estado **activo** pueden utilizarse para tener acceso a todas las API para los que tienen suscripciones. Para asociar el desarrollador recién creado grupos adicionales, vea [cómo asociar los grupos con los desarrolladores][].

## <a name="invite-developer"> </a>Invitar a un desarrollador

Para invitar a un desarrollador, haga clic en **usuarios** en el menú de **Administración de la API** de la izquierda y, a continuación, haga clic en **Invitar a usuario**.

![Invitar a programador][api-management-invite-developer]

Escriba la dirección de correo electrónico y nombre del programador y haga clic en **Invitar**.

![Invitar a programador][api-management-invite-developer-window]

Se muestra un mensaje de confirmación, pero el desarrollador recién invitado no aparece en la lista hasta que aceptar la invitación. 

![Invitar a confirmación][api-management-invite-developer-confirmation]

Cuando se invita a un desarrollador, se envía un correo electrónico para el desarrollador. Este mensaje se genera con una plantilla y es personalizable. Para obtener más información, vea [Configurar plantillas de correo electrónico][].

Una vez se haya aceptado la invitación, la cuenta se convierte en activada.

## <a name="block-developer"></a> Desactivar o reactivar una cuenta de desarrollador

De forma predeterminada, cuentas de programador recién creado o invitados están **activas**. Para desactivar una cuenta de desarrollador, haga clic en **bloque**. Para reactivar una cuenta de desarrollador bloqueados, haga clic en **Activar**. Una cuenta de desarrollador bloqueados no puede acceder al portal de programador o ninguna de las API de llamadas. Para eliminar una cuenta de usuario, haga clic en **Eliminar**.

![Programador de bloque][api-management-new-developer]

## <a name="reset-a-user-password"></a>Restablecer una contraseña de usuario

Para restablecer la contraseña de una cuenta de usuario, haga clic en el nombre de la cuenta.

![Restablecer la contraseña][api-management-view-developer]

Haga clic en **Restablecer la contraseña** para enviar un vínculo al usuario para restablecer su contraseña.

![Restablecer la contraseña][api-management-reset-password]

Para trabajar con cuentas de usuario mediante programación, consulte la documentación de la [entidad de usuario](https://msdn.microsoft.com/library/azure/dn776330.aspx) en la referencia de la [API de REST de administración](https://msdn.microsoft.com/library/azure/dn776326.aspx) . Para restablecer la contraseña de una cuenta de usuario en un valor específico, puede usar la operación de [actualización de un usuario](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) y especifique la contraseña que desee.

## <a name="pending-verification"></a>Comprobación pendiente

![Comprobación pendiente][api-management-pending-verification]

## <a name="next-steps"> </a>Pasos siguientes

Una vez que se crea una cuenta de desarrollador, puede asociar a funciones y suscribirse a los productos y las API. Para obtener más información, vea [cómo crear y usar grupos][].


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png
[]: ./media/api-management-howto-create-or-invite-developers/.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[Cómo crear y usar grupos]: api-management-howto-create-groups.md
[Cómo asociar los grupos a los desarrolladores]: api-management-howto-create-groups.md#associate-group-developer

[Introducción a la administración de la API de Azure]: api-management-get-started.md
[Crear una instancia de servicio de administración de la API]: api-management-get-started.md#create-service-instance
[Configurar plantillas de correo electrónico]: api-management-howto-configure-notifications.md#email-templates