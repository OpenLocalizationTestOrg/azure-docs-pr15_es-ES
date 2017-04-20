<properties
   pageTitle="El Asistente para seguridad de la administración de identidades con privilegios de Azure AD"
   description="La primera vez que usa la extensión de Azure Active Directory con privilegios la administración de identidades, aparecerá el Asistente de seguridad. En este artículo se describe los pasos para usar al asistente."
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

# <a name="the-azure-ad-privileged-identity-management-security-wizard"></a>El Asistente para seguridad de la administración de identidades con privilegios de Azure AD

Si es la primera persona para ejecutar la administración de identidades con privilegios de Azure (PIM) para su organización, verá un asistente. El asistente le ayuda a comprender los riesgos de seguridad de identidades con privilegios y cómo usar PIM para reducir estos riesgos. No es necesario realizar los cambios en las asignaciones de roles existentes en el asistente, si prefiere hacerlo más tarde.

## <a name="what-to-expect"></a>Qué puede esperar

Antes de que su organización inicia con PIM, todas las asignaciones de roles son permanentes: los usuarios siempre están en estas funciones incluso si actualmente no necesita sus privilegios.  El primer paso del asistente muestra una lista de funciones con privilegios elevados y cuántos usuarios están actualmente en esos roles. Es posible agregar detalles una función concreta para obtener más información acerca de los usuarios si uno o más de ellas están familiarizados.

El segundo paso del asistente le ofrece la oportunidad de cambiar las asignaciones de roles de administrador.  

> [AZURE.WARNING]Es importante que tenga al menos un administrador global y más de un administrador de la función privilegios con una cuenta de la organización (no una cuenta de Microsoft). Si hay sólo un administrador de la función privilegios, la organización no podrán administrar PIM si se elimina esa cuenta.
> Además, mantener las asignaciones de roles permanente si un usuario tiene una cuenta de Microsoft (una cuenta que usarán para iniciar sesión servicios de Microsoft como Skype y Outlook.com). Si va a requerir AMF para la activación de ese rol, se bloqueará ese usuario.


Una vez haya realizado los cambios, ya no se mostrará el asistente. La próxima vez que usted u otro administrador de la función privilegios utilizar PIM, verá el panel PIM.  

- Si desea agregar o quitar usuarios de funciones o cambiar las asignaciones de permanente a elegible, obtenga más información en [cómo agregar o quitar un rol de usuario](active-directory-privileged-identity-management-how-to-add-role-to-user.md).
- Si le gustaría dar acceso a administrar PIM más usuarios, más cómo [conceder acceso a administrar en PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).



## <a name="next-steps"></a>Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
