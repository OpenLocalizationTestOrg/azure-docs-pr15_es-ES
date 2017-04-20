<properties
    pageTitle="Analista de vista previa de Azure Active Directory | Microsoft Azure"
    description="Un tema que explica las diferencias entre Azure Active Directory en el portal de clásico y la vista previa de Azure Active Directory en el portal de Azure."
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
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="preview-of-the-azure-active-directory-management-experience-in-the-azure-portal"></a>Vista previa de la experiencia de administración de Azure Active Directory en el portal de Azure

La experiencia de administración de Azure Active Directory (AD Azure) está en vista previa en el portal de Azure. Puede intentar alejar iniciando sesión en [el portal de Azure](https://portal.azure.com) como administrador global del directorio. A continuación, seleccione Azure Active Directory en la lista de servicios si está visible, o seleccione **más servicios** para ver la lista de todos los servicios. No necesita una suscripción a Azure usar la Azure AD management experiencia en el portal de Azure.


## <a name="capabilities-of-the-preview-experience"></a>Capacidades de la experiencia de vista previa

La experiencia de vista previa le permite administrar muchos recursos de directorio como usuarios, grupos y aplicaciones, así como la configuración del directorio, en el portal de Azure. Estamos mejorando esta experiencia para incluir todas las capacidades que existen en el Azure AD management experiencia en el [portal de clásico de Azure](https://manage.windowsazure.com). Hasta entonces, hay algunas tareas que todavía debe completan en el portal clásico de administración de directorios.

## <a name="manage-the-same-azure-ad-tenants"></a>Administrar los inquilinos de Azure AD mismos

La experiencia de vista previa se lee y se escribe en el mismo inquilino de Azure Active Directory como el portal clásico y el centro de administración de Office 365. Los cambios realizados en cualquiera de estos portales se reflejan en todos los demás.

## <a name="use-the-same-authorization-logic"></a>Usar la misma lógica de autorización

La experiencia de vista previa usa la misma lógica de autorización como los clientes existentes de Active Directory. Los usuarios están autorizados a realizar cambios en los recursos del directorio según su función de directorio, como administrador global, Administrador de usuarios, Administrador de contraseñas. Tener un rol de recursos Azure o una suscripción de Azure no autorizar a un usuario para administrar los recursos del directorio. Para obtener más información roles de administración de Azure AD, vea [asignación de roles de administrador de Azure Active Directory](active-directory-assign-admin-roles.md). 

La experiencia de vista previa está optimizada para los administradores globales. Si utiliza la experiencia de vista previa mientras ha iniciado sesión como un usuario que no es un administrador global, puede tener una experiencia de degradado. Por ejemplo, es posible que pueda seleccionar un botón que permite iniciar una tarea que no puede completar en el directorio. Estamos mejorando esta experiencia pronto.
 
## <a name="tell-us-what-you-think"></a>Díganos qué opina

Puede proporcionar comentarios sobre la experiencia de vista previa en la sección del portal de administración del [área de votaciones de Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc).
