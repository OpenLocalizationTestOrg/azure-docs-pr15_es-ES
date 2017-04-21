<properties
    pageTitle="Agregar un usuario a la colección de RemoteApp de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo agregar usuarios a la colección de RemoteApp de Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="how-to-add-a-user-to-your-azure-remoteapp-collection"></a>Cómo agregar un usuario a la colección de RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Antes de que los usuarios pueden ver y usar las aplicaciones de Azure RemoteApp, debe concederles acceso a la colección. Esta es la parte fácil: en la ficha **Acceso de usuario** , escriba la información de cuenta para el usuario y, a continuación, haga clic en la marca de verificación.

¿Qué información de cuenta necesita? Que depende del tipo de colección que creó (nube o híbrido) y si está utilizando Office 365 ProPlus en esa colección.

## <a name="supported-user-identities"></a>Identidades de usuarios admitidos

Los tipos de otra colección (de nube frente híbrido) admiten el uso de identidades de usuario diferentes para tener acceso a las aplicaciones.  

Para una colección de híbrido de RemoteApp, debe configurar una infraestructura de dominio de Active Directory en un inquilino de Azure Active Directory con integración de directorios y local (y, opcionalmente, un solo inicio de sesión). Además, debe crear objetos de Active Directory en el directorio local.  

Para una colección de nube de RemoteApp, cualquier usuario con Azure Active Directory admite identidades puede conceder acceso de usuario a RemoteApp para incluir Accounts de Microsoft.  Consulte la tabla siguiente.

Los usuarios de Office 365 son Azure Active Directory. Si tienen híbrido de Azure Active Directory, directorio sincronizado cuentas, se pueden conceder acceso de usuarios en una implementación híbrida de RemoteApp.   

Puede utilizar esta tabla como referencia rápida cuya identidad es compatible con la colección y cuáles son los requisitos de Active Directory.

|Cuentas de usuario |Nube   |Híbrido|
|--------------|--------|------|
|Cuenta de Microsoft|     Sí|    No|
|Azure Active Directory (AD Azure)| | |
|Nube de AD Azure solo    |Sí    |No |
|ADsync con la sincronización de contraseña  |Sí    |Sí    |
|ADsync sin sincronización de la contraseña|  Sí |No |
|ADsync con AD FS  |Sí    |Sí    |
|[parte 3 º Azure admite proveedores de identidades](https://msdn.microsoft.com/library/azure/jj679342.aspx)  (por ejemplo Ping) |Sí    |Sí|
|Autenticación multifactor    |Sí    |Sí    |

Consulte [más información](remoteapp-ad.md) sobre la configuración de Active Directory para RemoteApp.


> [AZURE.NOTE] Los usuarios de Azure Active Directory deben estar entre el inquilino que está asociada a su suscripción. (Puede ver y modificar su suscripción en la pestaña **configuración** en el portal. Consulte [cambiar el inquilino de Azure Active Directory usa RemoteApp](remoteapp-changetenant.md) para obtener más información).

## <a name="office-365-proplus-user-account-information"></a>Información de cuenta de usuario ProPlus de Office 365
Si está utilizando la imagen de la plantilla de Office 365 ProPlus en su colección *o* si ha creado una imagen personalizada que usa Office 365, solo puede agregar usuarios de Azure Active Directory que tienen suscripciones a Office 365 para el dominio predeterminado de su suscripción. Para obtener más información, vea [usar Office 365 con RemoteApp de Azure](remoteapp-o365.md) .
