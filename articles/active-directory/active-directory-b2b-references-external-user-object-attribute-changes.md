<properties
   pageTitle="Cambios de atributos de objeto de usuario externo para la vista previa de colaboración de Azure Active Directory B2B | Microsoft Azure"
   description="Azure Active B2B de directorio es compatible con las relaciones de su compañía habilitando socios empresariales de forma selectiva tengan acceso a sus aplicaciones corporativas"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-external-user-object-attribute-changes"></a>Vista previa colaboración de Azure AD B2B: cambios de atributos del objeto de usuario externo

Cada usuario en un directorio de Azure AD está representado por un objeto de usuario. El objeto de usuario en Azure AD realiza cambios de atributo en diversas fases de la colaboración B2B canjear invitar flujo. El objeto de usuario que representa el usuario de partner en el directorio tiene atributos que cambian a canjear tiempo, cuando el usuario asociado hace clic en el vínculo en el correo electrónico de invitación. Específicamente:

- Se rellenan los atributos **SignInName** y **AltSecId**
- El atributo **DisplayName** cambia del nombre Principal de usuario (user_fabrikam.com#EXT#@contoso.com) el nombre de inicio de sesión(user@fabrikam.com)

Seguimiento de estos atributos en Azure AD puede ayudar a solucionar problemas o no un usuario asociado ha canjear su invitación de colaboración B2B.

## <a name="related-articles"></a>Artículos relacionados
Examinar nuestras otros artículos de colaboración de Azure AD B2B:

- [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Cómo funciona](active-directory-b2b-how-it-works.md)
- [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
- [Referencia de formato de archivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato de token de usuario externo](active-directory-b2b-references-external-user-token-format.md)
- [Limitaciones de vista previa actual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
