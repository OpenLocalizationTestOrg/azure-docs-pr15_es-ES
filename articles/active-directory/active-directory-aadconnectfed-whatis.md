<properties
    pageTitle="Azure AD Connect y federación | Microsoft Azure"
    description="Esta página es la ubicación central para toda la documentación relacionada con operaciones de AD FS mediante Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="anandy"/>


# <a name="azure-ad-connect-and-federation"></a>Federación y Azure AD Connect

Azure AD Connect le permite configurar la federación con las instalaciones AD FS y Azure AD. Con la federación de sesión, puede habilitar usuarios a iniciar sesión servicios de Azure AD según con sus contraseñas local y, en la red corporativa, sin tener que escribir sus contraseñas de nuevo. La opción de la federación con AD FS le permite implementar una nueva o especifique un AD FS existente en el conjunto de servidores de Windows Server 2012 R2.

Este tema es la página principal para obtener información sobre la federación relacionados con funcionalidades de Azure AD Connect y listas de vínculos a todos los otros temas relacionados con él. Para obtener vínculos a Azure AD Connect, consulte integrar las identidades locales con Azure Active Directory.

## <a name="azure-ad-connect---federation-topics"></a>Azure AD Connect - temas de federación

| Tema | ¿Qué cubre y cuándo leer |
|:------|:-----------|
| **Azure AD Connect sesión opciones de usuario** ||
| [Descripción de opciones de inicio de sesión de usuario](active-directory-aadconnect-user-signin.md) | Descripción de distintas opciones de inicio de sesión de usuario y cómo afectan a la experiencia del usuario de inicio de sesión de Azure |
| **Instalación de AD FS con Azure AD Connect**||
| [Requisitos previos](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | Requisitos previos para una instalación correcta de AD FS a través Azure AD Connect|
| [Configurar el conjunto de servidores de AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) | Cómo instalar una nueva granja de AD FS con Azure AD Connect |
| **Modificar la configuración de AD FS** | |
| [Reparar la confianza](active-directory-aadconnect-federation-management.md#reparing-the-trust) | Cómo reparar actual confianza entre local AD FS y Office 365 y Azure |
| [Agregar un nuevo servidor AD FS](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | Expandir el conjunto de servidores de AD FS con AD FS servidor publicación inicial instalación adicionales |
| [Agregar un nuevo servidor AD FS WAP](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | Expandir el conjunto de servidores de AD FS con WAP servidor publicación inicial instalación adicionales |
| [Agregar un nuevo dominio federado](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | Agregar otro dominio a ser federado con Azure AD |
|**Tareas posteriores a la instalación**||
| [Agregar un logotipo personalizado o ilustración](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| Modificar la experiencia de inicio de sesión especificando el logotipo personalizado que se mostrará en la página de inicio de sesión de AD FS |
| [Agregar una descripción de inicio de sesión](active-directory-aadconnect-federation-management.md#add-sign-in-description) | Cambiar la descripción de inicio de sesión en la página de inicio de sesión de AD FS | 
| [Modificar AD FS reclamación reglas](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | Modificar o agregar reglas de la reclamación de AD FS correspondiente a la configuración de sincronización de Azure AD Connect |


## <a name="additional-resources"></a>Recursos adicionales

* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
* [Implementación de AD FS en Azure](active-directory-aadconnect-azure-adfs.md)
* [Implementación de alta disponibilidad entre geográficos AD FS en Azure con el administrador del tráfico de Azure](active-directory-adfs-in-azure-with-azure-traffic-manager.md)


