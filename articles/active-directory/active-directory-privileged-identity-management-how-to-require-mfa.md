<properties
   pageTitle="Requerir autenticación multifactor | Microsoft Azure"
   description="Obtenga información sobre cómo requerir autenticación multifactor (AMF) para identidades con privilegios con la extensión de Azure Active Directory con privilegios la administración de identidades."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Cómo requerir AMF en la administración de identidades con privilegios de Azure AD

Le recomendamos que requieren autenticación multifactor (AMF) para todos los administradores. Esto reduce el riesgo de un ataque debido a una contraseña comprometido.

Puede requerir que los usuarios rellenan un reto AMF cuando iniciar sesión. La entrada de blog [AMF para Office 365 y AMF para Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) compara el contenido de suscripciones de Office y Azure, con las características de la oferta de Microsoft Azure con autenticación multifactor.

También puede requerir que los usuarios rellenan un reto AMF cuando activa una función de Azure AD PIM. De este modo, si el usuario no completa un reto AMF cuando ha iniciado sesión en, se le pedirá que lo haga por PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Requerir AMF en administración de identidades de Azure con privilegios de AD

Al administrar identidades en PIM como administrador de la función privilegios, es posible que vea alertas que recomienda AMF para cuentas con privilegios. Haga clic en la alerta de seguridad en el panel PIM y se abrirá un nuevo módulo con una lista de las cuentas de administrador que debe requieren AMF.  Puede requerir AMF seleccionando varias funciones y, a continuación, haga clic en el botón **corregir** o puede haga clic en los puntos suspensivos junto a funciones individuales y, a continuación, haga clic en el botón **corregir** .

> [AZURE.IMPORTANT] Ahora, Azure AMF sólo funciona con el trabajo o escuela cuentas, no Microsoft (generalmente una cuenta personal que se utiliza para iniciar sesión en servicios de Microsoft como Skype, Xbox, Outlook.com, etcetera.) a la derecha. Debido a esto, cualquier persona con una cuenta de Microsoft no puede ser elegible administrador porque no se utilizan AMF activar sus funciones. Si estos usuarios necesitan continuar administrando las cargas de trabajo con una cuenta de Microsoft, elevar a los administradores permanentes por ahora.

Además, puede cambiar el requisito de AMF para un rol específico haciendo clic en ella en la sección Roles del panel PIM. A continuación, haga clic en **configuración** en el módulo de rol y, a continuación, seleccione **Habilitar** en autenticación multifactor.

## <a name="how-azure-ad-pim-validates-mfa"></a>¿Cómo Azure AD PIM valida AMF

Existen dos opciones para validar AMF cuando un usuario activa una función.

La opción más sencilla es dependen de Azure AMF para los usuarios que se están activando una función privilegios. Para ello, primero compruebe que los usuarios licencia, si es necesario y que haya registrado para Azure AMF. Obtener más información sobre cómo hacer esto es en [Introducción a la autenticación multifactor de Azure en la nube](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md). Se recomienda, pero no es necesario, configurar Azure AD para exigir AMF para estos usuarios cuando iniciar sesión. Esto es porque las comprobaciones AMF se realizará por Azure AD PIM propiamente dicho.

Como alternativa, si usuarios autenticados local puede hacer que su proveedor de identidades responsable AMF. Por ejemplo, si ha configurado AD a los servicios de federación requieren autenticación basada en la tarjeta inteligente antes de acceder a [recursos de la nube de seguridad con autenticación multifactor de Azure y AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) de Azure AD incluye instrucciones para configurar AD FS para enviar notificaciones de Azure AD. Cuando un usuario intenta activar una función, se aceptarán Azure AD PIM que AMF ya ha validar para el usuario una vez que recibe las notificaciones adecuadas.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
