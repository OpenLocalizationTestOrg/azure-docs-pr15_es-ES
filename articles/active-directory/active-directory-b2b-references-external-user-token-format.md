<properties
   pageTitle="Formato de token de usuario externo para la vista previa de colaboración de Azure Active Directory B2B | Microsoft Azure"
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

# <a name="azure-ad-b2b-collaboration-preview-external-user-token-format"></a>Vista previa colaboración de Azure AD B2B: formato de token de usuario externo

Las notificaciones para un anuncio de Azure estándar token se describen en el artículo [Token compatibles y los tipos de notificación](active-directory-token-and-claims.md) en azure.microsoft.com.

Las notificaciones que son diferentes para un usuario externo de colaboración B2B autenticado son las siguientes:<br/>
- **OID:** el identificador de objeto de inquilino de recursos<br/>
- **TID**: ID de inquilinos de inquilino de recursos<br/>
- **Emisor**: este es el inquilino de recursos<br/>
- **IDP**: este es el inquilino principal del usuario<br/>
- **AltSecId**: este es el identificador de seguridad alternativo, que es opaco para usted<br/>

## <a name="related-articles"></a>Artículos relacionados
Examinar nuestras otros artículos de colaboración de Azure AD B2B:

- [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Cómo funciona](active-directory-b2b-how-it-works.md)
- [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
- [Referencia de formato de archivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Cambios de atributos del objeto de usuario externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitaciones de vista previa actual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
