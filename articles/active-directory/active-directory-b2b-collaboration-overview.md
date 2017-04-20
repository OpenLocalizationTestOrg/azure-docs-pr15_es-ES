<properties
   pageTitle="Colaboración de B2B de Active Directory Azure | Microsoft Azure"
   description="Colaboración de Azure B2B de Active Directory permite socios tener acceso a sus aplicaciones de la empresa, con cada uno de sus usuarios representados por una sola Azure AD cuenta"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="azure-active-directory-b2b-collaboration"></a>Colaboración de Azure B2B de Active Directory

Colaboración de Azure Active Directory (AD Azure) B2B le permite habilitar el acceso a sus aplicaciones de empresa de identidades administradas asociado. Puede crear relaciones de la compañía invita a y la autorización de usuarios de las empresas asociadas para tener acceso a los recursos. Complejidad se reduce porque cada empresa una vez federa con Azure Active Directory y cada usuario está representado por una sola cuenta de Azure AD. Si sus colaboradores administración sus cuentas de Azure AD porque se revoca el acceso al usuarios partner terminan de su organización y, a continuación, se evita el acceso no deseado a través de la pertenencia en directorios internos aumenta la seguridad. Para socios que ya no tienen Azure AD, colaboración B2B tiene una experiencia simplificada suscripción para proporcionar cuentas de Azure AD a sus colaboradores.

-   Sus colaboradores usan sus propias credenciales de inicio de sesión, lo que libera de administración de un directorio de socios externos y de la necesidad de quitar el acceso cuando los usuarios dejan la organización de partner.

-   Administrar el acceso a sus aplicaciones, independientemente del ciclo de vida de cuenta de su empresa compañero. Por ejemplo, esto significa que puede revocar el acceso sin tener que hablar con el departamento de TI de su asociado de negocios que hacer nada.

## <a name="capabilities"></a>Capacidades de

Colaboración B2B simplifica la administración y mejora la seguridad de acceso para socios a recursos corporativos incluidos SaaS aplicaciones como Office 365, Salesforce, servicios de Azure y cada mobile, en la nube y aplicación de notificaciones local. Socios de B2B colaboración permite administración sus propias cuentas y las empresas pueden aplicar directivas de seguridad de Access asociado.

Azure Active B2B de directorio de colaboración es fácil de configurar con simplificado suscripción para partners de todos los tamaños, incluso si no tiene su propio Azure Active Directory a través de un proceso comprobado por correo electrónico. También es fácil de mantener con ningún directorios externos o por configuraciones de federación de asociado.

## <a name="b2b-collaboration-process"></a>Proceso de colaboración B2B

1. Colaboración de Azure AD B2B permite que un administrador de empresa invitar y autorizar un conjunto de usuarios externos al cargar un archivo de valores separados por comas (CSV) de no más de 2000 líneas en el portal de colaboración B2B.

  ![Diálogo de carga de archivo CSV](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. El portal enviar invitaciones de correo electrónico a estos usuarios externos.

3. El usuario invitado se inicie sesión en una cuenta de trabajo existente con Microsoft (administrado en Azure AD) o, obtener una nueva cuenta de trabajo en Azure AD.

4. Una vez que haya iniciado sesión, el usuario se redirigirá a la aplicación que se comparte con ellos.

Invitaciones a las direcciones de correo electrónico de consumidor (por ejemplo, Gmail o [*comcast.net*](http://comcast.net/)) no se admiten actualmente.

Para obtener más información sobre el funcionamiento de la colaboración B2B, consulte [este vídeo](http://aka.ms/aadshowb2b).

## <a name="next-steps"></a>Pasos siguientes
Examinar nuestras otros artículos de colaboración de Azure AD B2B.

- [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Cómo funciona](active-directory-b2b-how-it-works.md)
- [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
- [Referencia de formato de archivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato de token de usuario externo](active-directory-b2b-references-external-user-token-format.md)
- [Cambios de atributos del objeto de usuario externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitaciones de vista previa actual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
