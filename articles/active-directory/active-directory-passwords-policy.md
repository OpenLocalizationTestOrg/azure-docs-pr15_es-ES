<properties
    pageTitle="Restricciones de Azure Active Directory y directivas de contraseña | Microsoft Azure"
    description="Describe las directivas que se aplican a las contraseñas en Azure Active Directory, incluidos los caracteres permitidos, longitud y expiración"
  services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand"/>


# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Restricciones de Azure Active Directory y directivas de contraseña

En este artículo se describe las directivas de contraseña y los requisitos de complejidad asociados con las cuentas de usuario que se almacena en su directorio de Azure AD.

> [AZURE.IMPORTANT] **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [He aquí cómo puede cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md).

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Directivas de UserPrincipalName que se aplican a todas las cuentas de usuario

Cada cuenta de usuario que debe iniciar sesión en el sistema de autenticación de Azure AD debe tener un valor de atributo de nombre principal (UPN) único usuario asociado con esa cuenta. La siguiente tabla se describe la directivas que se aplican a las cuentas de usuario de origen de Active Directory en local (sincronizado a la nube) y a las cuentas de usuario solo nube.

|   (Propiedad)           |     Requisitos de UserPrincipalName  |
|   ----------------------- |   ----------------------- |
|  Caracteres permitidos    |  <ul> <li>A – Z</li> <li>+ z </li><li>0-9</li> <li> . - \_ ! \# ^ \~</li></ul> |
|  Caracteres no permitidos  | <ul> <li>Cualquier '@' caracteres que no separa el nombre de usuario del dominio.</li> <li>No puede contener un carácter de punto '.' precede a la '@' símbolo</li></ul> |
| Restricciones de longitud  |       <ul> <li>Longitud total no debe superar los 113 caracteres</li><li>64 caracteres antes de la ‘@’ símbolo</li><li>48 caracteres después de la ‘@’ símbolo</li></ul>

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Directivas de contraseña que solo se aplican a las cuentas de usuario de nube

La siguiente tabla describe la configuración de la directiva de contraseña disponibles que se puede aplicar a las cuentas de usuario que crea y administra en Azure AD.

|  (Propiedad)       |    Requisitos          |
|   ----------------------- |   ----------------------- |
|  Caracteres permitidos   |   <ul><li>A – Z</li><li>+ z </li><li>0-9</li> <li>@# $ % ^ & * - _ ! + = [] {} & #124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
|  Caracteres no permitidos   |       <ul><li>Caracteres Unicode</li><li>Espacios</li><li> **Solo las contraseñas seguras**: no puede contener un carácter de punto '.' precede a la '@' símbolo</li></ul> |
|   Restricciones de contraseña | <ul><li>8 caracteres mínimas y máxima de 16 caracteres</li><li>**Solo las contraseñas seguras**: requiere 3 de 4 de las siguientes acciones:<ul><li>Caracteres en minúsculas</li><li>Caracteres en mayúsculas</li><li>Números (0-9)</li><li>Símbolos (consulte las restricciones de contraseña anteriores)</li></ul></li></ul> |
| Duración de expiración de contraseña      | <ul><li>Valor predeterminado: **90** días </li><li>Valor es configurable mediante el cmdlet Set-MsolPasswordPolicy de Azure Active Directory Module para Windows PowerShell.</li></ul> |
| Notificación de expiración de contraseña |  <ul><li>Valor predeterminado: **14** días (antes de que caduque la contraseña)</li><li>Valor es configurable mediante el cmdlet Set-MsolPasswordPolicy.</li></ul> |
| Expiración de contraseña |  <ul><li>Valor predeterminado: **false** días (indica que expiración de contraseña está habilitado) </li><li>Valor se puede configurar para cuentas de usuario individual mediante el cmdlet Set-MsolUser. </li></ul> |
|  Historial de contraseñas  | Última contraseña no se pueden usar de nuevo. |
|  Duración del historial de contraseña | Indefinidamente |
|  Bloqueo de cuenta | Después de 10 sesión intentos (contraseña incorrecta), el usuario se bloqueará durante un minuto. Más intentos de inicio de sesión incorrectos se bloqueará del usuario para aumentar la duración. |


## <a name="next-steps"></a>Pasos siguientes

* **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [He aquí cómo puede cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md).
* [Administrar sus contraseñas desde cualquier lugar](active-directory-passwords.md)
* [Cómo funciona la administración de contraseñas](active-directory-passwords-how-it-works.md)
* [Introducción a la administración de contraseña](active-directory-passwords-getting-started.md)
* [Personalizar la administración de contraseñas](active-directory-passwords-customize.md)
* [Mejores prácticas de administración de contraseña](active-directory-passwords-best-practices.md)
* [Cómo obtener recomendaciones operativas con informes de administración de contraseña](active-directory-passwords-get-insights.md)
* [Preguntas más frecuentes sobre la administración de contraseñas](active-directory-passwords-faq.md)
* [Solucionar problemas de administración de contraseñas](active-directory-passwords-troubleshoot.md)
* [Aprende más](active-directory-passwords-learn-more.md)
