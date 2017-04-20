<properties
    pageTitle="Referencia de protocolo de SAML de Azure AD | Microsoft Azure"
    description="Este artículo proporciona una descripción general de los perfiles de inicio de sesión único y solo SAML Sign-Out de Azure Active Directory."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/23/2016"
    ms.author="priyamo"/>


# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Cómo Azure Active Directory usa el protocolo SAML

Protocolo de usos SAML 2.0 de Azure Active Directory (AD Azure) para habilitar las aplicaciones proporcionar una experiencia de inicio de sesión único a sus usuarios. Los perfiles de [Inicio de sesión único](active-directory-single-sign-on-protocol-reference.md) y [cierre de sesión único](active-directory-single-sign-out-protocol-reference.md) SAML de Azure AD explican cómo se usan aserciones SAML, protocolos y enlaces en el servicio de proveedor de identidades.

Protocolo de SAML requiere el proveedor de identidades (Azure AD) y el proveedor de servicios (la aplicación) para intercambiar información sobre ellos mismos.

Cuando se registra una aplicación con Azure AD, el desarrollador de la aplicación registra información relacionada con la federación con Azure AD. Esto incluye la **Redirección URI** y **URI de metadatos** de la aplicación.

Azure AD utiliza el **URI de metadatos** del servicio de nube para recuperar la clave de firma y el URI del servicio de nube de cierre de sesión. Si la aplicación no admite un URI de metadatos, el desarrollador debe ponerse en contacto con soporte técnico de Microsoft para proporcionar el URI de cierre de sesión y clave de firma.

Azure Active Directory expone comunes y específicos del inquilino (independiente del inquilino) solo inicio de sesión único cierre de sesión extremos y. Estas direcciones URL representan ubicaciones posibles: no son solo identificadores, por lo que puede ir al extremo para leer los metadatos.

 - Se encuentra en el extremo del inquilino específicas `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  La <TenantDomainName> marcador de posición representa un nombre de dominio registrado o TenantID GUID de un inquilino de Azure AD. Por ejemplo, los metadatos de federación del inquilino contoso.com se están en: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

- Se encuentra en el extremo del inquilino independiente `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. En esta dirección de extremo **comunes** aparece, en lugar de un nombre de dominio de inquilinos o el Id.

Para obtener información acerca de los documentos de metadatos de federación publica Azure AD, vea [Los metadatos de federación](active-directory-federation-metadata.md).
