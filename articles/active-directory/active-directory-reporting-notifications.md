<properties
    pageTitle="Notificaciones de informes de Azure Active Directory"
    description="Cómo usar el informe de las notificaciones de inicio de sesión sospechoso de Azure Active Directory ins."
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/07/2016"
    ms.author="dhanyahk"/>

# <a name="azure-active-directory-reporting-notifications"></a>Notificaciones de informes de Azure Active Directory

## <a name="what-reports-generate-email-notifications"></a>¿Qué informes generan notificaciones de correo electrónico

En este momento, solo el inicio de sesión irregulares en desencadenadores de informe de actividad notificaciones por correo electrónico.

## <a name="what-is-an-irregular-sign-in"></a>¿Qué es un "signo Irregular en"?

Inicios de sesión irregulares son aquellos que se han identificado por nuestro equipo algoritmos, basándose en una condición de "viajes impide" junto con una ubicación de inicio de sesión irregulares y dispositivo de aprendizaje. Es posible que un intruso se ha intentando iniciar sesión utilizando esta cuenta.

## <a name="who-receives-the-email-notifications"></a>¿Quién recibe las notificaciones de correo electrónico?

El correo electrónico se envía a todos los administradores globales que se le ha asignado una licencia de Premium de Active Directory. Para asegurarse de que se ha entregado, se envía a la dirección de correo electrónico alternativa de administradores también. Los administradores deben contener aad-alerts-noreply@mail.windowsazure.com en su lista de remitentes seguros para que no pierda el correo electrónico.

## <a name="how-often-are-these-emails-sent"></a>¿Con qué frecuencia son estos correos electrónicos enviados?

Se envía el correo electrónico si 10 nuevas irregulares inicio de sesión se producen actividades en los últimos 30 días o desde que se envió el último correo electrónico, lo que sea menor.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>¿Cómo puedo acceder al informe mencionado en el correo electrónico?

Al hacer clic en el vínculo, se le redirigirá a la página del informe en el portal de clásico de Azure. Para obtener acceso al informe, debe ser:

- Un administrador o co-Administrador de su suscripción de Azure

- Un administrador global en el directorio y le asigna una licencia de Premium de Active Directory. Para obtener más información, vea [ediciones de Azure Active Directory](active-directory-editions.md).

## <a name="can-i-turn-off-these-emails"></a>¿Puedo desactivar estos mensajes de correo electrónico?

Sí, para desactivar las notificaciones relacionadas con inicios de sesión irregulares dentro del portal Azure clásico, haga clic en **Configurar**y, a continuación, seleccione **deshabilitado** en la sección **notificaciones** .

## <a name="whats-next"></a>¿Qué es la siguiente
- ¿Curiosidad por qué informes de seguridad, auditoría y actividad están disponibles? Vea [seguridad de Azure AD, auditoría e informes de actividad](active-directory-view-access-usage-reports.md)
- [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Agregar la marca para iniciar sesión y Panel de acceso a las páginas de la empresa](active-directory-add-company-branding.md)
