<properties
   pageTitle="Limitaciones de vista previa actual para la colaboración de Azure Active Directory B2B | Microsoft Azure"
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
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-current-preview-limitations"></a>Vista previa colaboración de Azure AD B2B: actual obtener una vista previa de las limitaciones

- Autenticación multifactor (AMF) no se admiten en los usuarios externos. Por ejemplo, si Contoso tiene AMF, pero no Org asociado, a continuación, Partner Org usuarios no se puede conceder AMF mediante la colaboración B2B.
- Invitaciones a son posibles sólo a través de CSV; invitaciones individuales y acceso de API no son compatibles.
- Solo los administradores globales de Azure AD puede cargar archivos CSV.
- Actualmente no se admiten las invitaciones a las direcciones de correo electrónico del consumidor (como hotmail.com, Gmail.com o comcast.net).
- Acceso de usuario externo a aplicaciones no probadas en local.
- Los usuarios externos no automáticamente desaparecen cuando el usuario real se elimina de su directorio.
- No se admiten las invitaciones a las listas de distribución.
- Máximo de 2.000 registros se puede cargar a través de CSV.

## <a name="related-articles"></a>Artículos relacionados
Examinar nuestras otros artículos de colaboración de Azure AD B2B:

- [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Cómo funciona](active-directory-b2b-how-it-works.md)
- [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
- [Referencia de formato de archivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato de token de usuario externo](active-directory-b2b-references-external-user-token-format.md)
- [Cambios de atributos del objeto de usuario externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
