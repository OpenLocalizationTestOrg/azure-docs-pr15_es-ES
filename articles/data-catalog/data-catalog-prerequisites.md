<properties
   pageTitle="Requisitos previos de catálogo de datos Azure | Microsoft Azure"
   description="Azure catálogo de datos requisitos previos - lo que necesita para empezar con el catálogo de datos de Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-prerequisites"></a>Requisitos previos de catálogo de datos Azure

## <a name="what-do-i-need-to-get-started-with-azure-data-catalog"></a>¿Qué es necesario empezar a trabajar con el catálogo de datos de Azure?

Hay algunas cosas que debe ocuparse de antes, puede configurar el **Catálogo de datos de Azure**. No se preocupe, no tardar!

## <a name="azure-subscription"></a>Suscripción de Azure
Para configurar el catálogo de datos de Azure, debe ser el propietario o el copropietario de una suscripción de Azure.

Suscripciones de Azure ayudarle a organizar el acceso a los recursos de servicio de nube como catálogo de datos de Azure. También ayudan a controlar cómo se notifica el uso de recursos, facturado y de pago. Cada suscripción puede tener una instalación diferente de pago y facturación, por lo que puede tener varias suscripciones y distintos planes por departamento, project, oficina y así sucesivamente. Cada servicio de nube pertenece a una suscripción y, a continuación, debe tener una suscripción antes de configurar el catálogo de datos de Azure. Para obtener más información, vea [Administrar cuentas, suscripciones y funciones administrativas](../active-directory/active-directory-assign-admin-roles.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Para configurar el catálogo de datos de Azure, debe haber iniciado sesión con una cuenta de usuario de Azure Active Directory.

Azure Active Directory (AD Azure) proporciona una forma sencilla de su empresa administrar la identidad y acceso, tanto en la nube y locales. Los usuarios pueden usar una sola cuenta profesional o educativa para el inicio de sesión único en cualquier nube y local de aplicación web. Catálogo de datos de Azure usa Azure AD para autenticar inicio de sesión. Para obtener más información, consulte [¿Qué es Azure Active Directory](../active-directory/active-directory-whatis.md).

> [AZURE.NOTE] El [portal de Azure](http://portal.azure.com/) permite a los usuarios iniciar sesión con una Account personal de Microsoft o de un trabajo de Azure Active Directory cuenta o la escuela. Para configurar el catálogo de datos de Azure con el portal de Azure o con el [portal de catálogo de datos](http://www.azuredatacatalog.com) debe haber iniciado sesión con una cuenta de Azure Active Directory, no una cuenta personal.

## <a name="active-directory-policy-configuration"></a>Configuración de directiva de Active Directory

En algunos casos, los usuarios pueden encontrar una situación donde puede iniciar sesión en el portal del catálogo de datos de Azure, pero al intentar iniciar sesión en la herramienta de registro de origen de datos que se encuentra en un mensaje de error que impide que iniciar sesión. Este problema puede ocurrir únicamente cuando el usuario está en la red de la empresa o puede producirse solo cuando el usuario se conecta desde fuera de la red de la compañía.

La herramienta de registro de origen de datos utiliza la autenticación de formularios para validar los inicios de sesión de usuario en Active Directory. Para iniciar sesión correctamente, debe habilitarse la autenticación de formularios en la directiva de autenticación Global un administrador de Active Directory.

La directiva de autenticación Global permite métodos de autenticación habilitarse por separado para intranet y extranets conexiones, tal como se muestra a continuación. Si no está habilitada la autenticación de formularios de la red desde la que el usuario se conecta, pueden producirse errores de inicio de sesión.

 ![Directiva de autenticación Global de Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Para obtener más información, consulte [Configuración de directivas de autenticación](https://technet.microsoft.com/library/dn486781.aspx).
