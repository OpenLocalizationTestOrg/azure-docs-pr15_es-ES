<properties
   pageTitle="Solución de problemas: Elemento 'Active Directory' falta o está no disponible | Microsoft Azure "
   description="Qué hacer cuando el elemento de menú de Active Directory no aparece en el Portal de administración de Azure."
   services="active-directory"
   documentationCenter="na"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Solución de problemas: Elemento 'Active Directory' falta o está no disponible

Muchas de las instrucciones para el uso de servicios y las características de Azure Active Directory empiezan con "Vaya al Portal de administración de Azure y haga clic en **Active Directory"**. Pero, ¿qué hacer si no aparece el elemento de menú o la extensión de Active Directory o si está marcado **No están disponibles**? Este tema está diseñado para ayudar a. Describe las condiciones que **Active Directory** no aparecen o no está disponible y se explica cómo proceder.

## <a name="active-directory-is-missing"></a>Falta de Active Directory

Normalmente, un elemento de **Active Directory** aparece en el menú de navegación izquierdo. Las instrucciones de procedimientos de Azure Active Directory se supone que este elemento se encuentra en la vista.

![Captura de pantalla: Active Directory en Azure](./media/active-directory-troubleshooting/typical-view.png)

El elemento de Active Directory aparece en el menú de navegación izquierdo cuando cualquiera de las siguientes condiciones sea verdadera. En caso contrario, no aparece el elemento.

* El usuario actual ha iniciado sesión con una cuenta de Microsoft (anteriormente conocida como Windows Live ID).

    OR

* El inquilino Azure tiene un directorio y la cuenta actual es un administrador de directorios.

    OR

* El inquilino Azure tiene al menos un espacio de nombres de Control de acceso de Azure AD (ACS). Para obtener más información, vea [Namespace de Control de acceso](https://msdn.microsoft.com/library/azure/gg185908.aspx).

    OR

* El inquilino Azure tiene al menos un proveedor de autenticación multifactor de Azure. Para obtener más información, vea [Administrar los proveedores de autenticación multifactor Azure](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

Para crear un espacio de nombres de Control de acceso o un proveedor de autenticación multifactor, haga clic en **+ nuevo** > **Servicios de aplicación** > **Active Directory**.

Para obtener derechos administrativos a un directorio, tiene un administrador de asignar un rol de administrador a su cuenta. Para obtener más información, vea [asignar roles de administrador](active-directory-assign-admin-roles.md).

## <a name="active-directory-is-not-available"></a>Active Directory no está disponible

Al hacer clic en **+ nuevo** > aparece de**Servicios de aplicación**, un elemento de **Active Directory** . Más concretamente, el elemento de Active Directory aparece cuando cualquiera de las características de Active Directory, como directorio, Control de acceso o proveedor de servicios de autenticación multifactor, están disponible para el usuario actual.

Sin embargo, mientras se está cargando la página, el elemento está atenuado y se marca como **No disponible**. Este es un estado temporal. Si espera unos segundos, el elemento vuelve a estar disponible. Si se prolonga el tiempo de retraso, actualizar la página web con qué frecuencia resuelve el problema.

![Captura de pantalla: Active Directory no está disponible](./media/active-directory-troubleshooting/not-available.png)
