<properties
   pageTitle="Comparación de las capacidades de administración de identidades externas con Azure Active Directory | Microsoft Azure"
   description="Compara aplicación varios inquilinos, B2C y colaboración de Azure Active Directory B2B para admitir autenticación y la autorización para identidades externas"
   services="active-directory"
   documentationCenter="" 
   authors="arvindsuthar"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="02/24/2016"
   ms.author="asuthar"/>

# <a name="comparing-capabilities-for-managing-external-identities-using-azure-active-directory"></a>Comparación de las capacidades de administración de identidades externas con Azure Active Directory

Además de administrar el acceso de los empleados móviles a aplicaciones de SaaS, Azure Active Directory (AD Azure) puede ayudar a su organización compartir recursos con sus socios de negocios y ofrecer aplicaciones para empresas y los consumidores.

## <a name="developing-applications-for-businesses"></a>Desarrollo de aplicaciones para empresas

¿Proporciona un servicio o la aplicación, como un servicio de nóminas para empresas? Azure AD proporciona la plataforma de identidad que le permite crear aplicaciones que se integran con millones de las organizaciones que ya se han configurado Azure AD como parte de implementar Office 365 u otros servicios de empresa.

**Ejemplo:** Un distribuidor farmacéutico proporciona suministros médicos y sistemas de información para el sector de salud. Necesita para una aplicación de análisis en prácticas médicas y clientes deseados para administrar sus propios identidades de campo. Esta compañía eligió Azure AD como la plataforma de identidad para su aplicación de administración de la práctica, proporcionar las identidades de Azure AD a sus clientes arroba hacia arriba cuando sea necesario. Para obtener más información, consulte la [Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md).

## <a name="enabling-business-partner-access-to-your-corporate-resources"></a>Habilitar el acceso de partner de empresa a los recursos de empresa

¿Tienen socios comerciales u otras personas fuera de la empresa que necesitan tener acceso a los recursos de empresa, como un sitio de SharePoint o su sistema ERP? Azure AD permite a los administradores para conceder acceso a aplicaciones de la empresa de inicio de sesión de un solo los usuarios externos (que pueden o no exista en Azure AD). Esto mejora la seguridad como usuarios pierdan acceso cuando dejan la organización asociada, mientras usted controla las directivas de acceso de la organización. Esto también simplifica la administración como que no necesita para administrar a un socio externo directorio o por relaciones de federación de asociado.

**Ejemplo:** Una empresa de imágenes proporciona los distribuidores con foto imaging servicios y funciona la mayor red minorista de kioscos de impresión. Eligieron Azure AD para habilitar miles de usuarios en sus socios empresariales de minorista usar sus propias credenciales para descargar los materiales de marketing de partner más recientes y reordenar fotografías procesamiento suministros de extranet de proveedor de la empresa. Para obtener más información, consulte [colaboración de Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md).

## <a name="developing-applications-for-consumers"></a>Desarrollar aplicaciones para consumidores

¿Necesita segura y rentable publicar aplicaciones en línea, por ejemplo, un frontal de tienda minorista, a millones de consumidores? Azure AD proporciona una plataforma habilitar social, así como nombre de usuario y la contraseña inicio de sesión, iniciar sesión con su marca autoservicio y restablece la contraseña de autoservicio para los consumidores de la aplicación. Se incrementa comodidad para que los consumidores reduciendo carga en los desarrolladores de software.

**Ejemplo:** La \#1 franquicia en el mundo necesario para integrarse directamente con sus ventiladores 450 millones de deportes. Para ello, crea una aplicación móvil con Azure AD para el almacenamiento de autenticación y el perfil de usuario. Ventiladores obtener registro simplificado e inicio de sesión a través del uso de cuentas sociales como Facebook o pueden usar tradicional de nombre de usuario y contraseñas para una experiencia perfecta en iOS, Android y Windows teléfonos. Crear en el establecido plataforma de Azure AD reducido significativamente código personalizado mientras que le da la franquicia personalizado de marca y mitigar dudas sobre seguridad, violaciones de datos y escalabilidad. Para obtener más información, vea [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).

## <a name="comparison-of-azure-ad-capabilities"></a>Comparación de capacidades de Azure AD

Capacidades de cada uno de los casos ya analizados en este artículo se trata en Azure AD. Esta tabla debe aclarar qué capacidades son más relevantes para usted:

| **Tenga en cuenta este producto...**       | [Aplicación de Azure AD múltiples arrendatario SaaS](active-directory-developers-guide.md)    | [Colaboración de Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md)        | [B2C de Azure AD](https://azure.microsoft.com/documentation/services/active-directory-b2c/)                |
|-----------------------|-------------------------|----------------------------|------------------------|
| **Si tiene que proporcionar...** | un servicio para empresas | acceso para socios a mis aplicaciones  | un servicio a los consumidores |
| **Y estoy similar a...**  | Pharma distribuidor      | Empresa de imágenes            | Franquicia de deportes       |
| **Implementar una aplicación para...**  | Administración de la práctica     | Proveedor de extranet          | Ventiladores de fútbol            |
| **Identificación...**        | Oficinas médicas        | Socios aprobados | Todos los usuarios con correo electrónico      |
| **Accesible cuando...**      | Permisos de administrador de cliente | Invita mi administrador           | Registra el consumidor      |
