<properties
   pageTitle="Vista previa colaboración de Azure AD B2B: cómo funciona | Microsoft Azure"
   description="Describe cómo colaboración B2B de Azure Active Directory admite las relaciones de su compañía habilitando socios empresariales de forma selectiva tengan acceso a sus aplicaciones corporativas"
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

# <a name="azure-ad-b2b-collaboration-preview-how-it-works"></a>Vista previa colaboración de Azure AD B2B: cómo funciona
Colaboración de Azure AD B2B se basa en una invitación y canjear modelo. Proporcionan las direcciones de correo electrónico de las partes que desea trabajar, junto con las aplicaciones que desee usar. Azure AD envía una invitación de correo electrónico con un vínculo. El usuario asociado sigue el vínculo y se le pide que inicie sesión con su cuenta de Azure AD o inicio de sesión para una nueva Azure AD de la cuenta.

1. El administrador invita a los usuarios asociado al cargar [un archivo .csv estructuradas](active-directory-b2b-references-csv-file-format.md) con el portal de Azure.
2. El portal envía invitar a los correos electrónicos a estos usuarios asociado.
3. Los usuarios de partner haga clic en el vínculo del correo electrónico y le pide que inicie sesión con sus credenciales de trabajo (si están ya en Azure AD), o para iniciar sesión como un usuario de colaboración de Azure AD B2B.
4. Los usuarios de Partner se redirigen a la aplicación que se invitaran donde ya tienen acceso.

## <a name="directory-operations"></a>Operaciones de directorio
Los usuarios de Partner existen en su Azure AD como usuarios externos. Esto significa que el administrador puede proporcionando licencias, asignar la pertenencia a grupos y aún más conceder acceso a aplicaciones corporativos a través del portal de Azure o con PowerShell de Azure como para los usuarios de su compañía.

Mientras un pago Azure AD suscripción (Basic o Premium) no es necesario utilizar Azure AD B2B, los inquilinos que tengan un pago suscripción de Azure AD (Basic o Premium) recibir las siguientes ventajas adicionales:

 - Los administradores pueden asignar a grupos de aplicaciones, que permite la administración más simple de acceso de usuarios invitados.
 - Inquilino de administración de marca se usa para personalizar los mensajes de correo electrónico de invitación y experiencia de amortización, proporcionar más contexto para invitar usuarios asociado.

## <a name="related-articles"></a>Artículos relacionados
 Examinar nuestras otros artículos de colaboración de Azure AD B2B

 - [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
 - [Referencia de formato de archivo CSV](active-directory-b2b-references-csv-file-format.md)
 - [Formato de token de usuario externo](active-directory-b2b-references-external-user-token-format.md)
 - [Cambios de atributos del objeto de usuario externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [Limitaciones de vista previa actual](active-directory-b2b-current-preview-limitations.md)
 - [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
