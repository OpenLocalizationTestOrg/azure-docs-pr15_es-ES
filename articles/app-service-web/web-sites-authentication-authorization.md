<properties 
    pageTitle="Autenticar con local de Active Directory en la aplicación de Azure | Microsoft Azure" 
    description="Obtenga más información sobre las distintas opciones para las aplicaciones de línea de negocio de servicio de la aplicación de Azure para autenticar con local de Active Directory" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="web" 
    ms.date="08/31/2016" 
    ms.author="cephalin"/>

# <a name="authenticate-with-on-premises-active-directory-in-your-azure-app"></a>Autenticar con local de Active Directory en la aplicación de Azure #

Este artículo muestra cómo autenticar con Active Directory (AD) en el [Servicio de aplicación de Azure](../app-service/app-service-value-prop-what-is.md)en local. Una aplicación de Azure está alojada en la nube, pero hay métodos de autenticación local usuarios de AD segura. 

## <a name="authenticate-through-azure-active-directory"></a>Autenticar a través de Azure Active Directory
Puede ser un inquilino de Azure Active Directory directory sincronizado con en local AD. Este enfoque permite a los usuarios de AD obtener acceso a la aplicación desde internet y autenticar con sus credenciales local. Además, servicio de aplicación de Azure proporciona una [solución llave para este método](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). Con unos pocos clics de un botón, puede habilitar la autenticación con un inquilino sincronizado de directorio de la aplicación de Azure. Este enfoque tiene las siguientes ventajas:

-   No requiere cualquier código de autenticación en la aplicación. Le permiten aplicación de servicio no la autenticación para usted y su tiempo en proporcionar funcionalidad en la aplicación.
-   [API de Azure AD Graph](http://msdn.microsoft.com/library/azure/hh974476.aspx) permite el acceso a los datos del directorio de la aplicación de Azure.
-   Proporciona SSO a [todas las aplicaciones compatibles con Azure Active Directory](/marketplace/active-directory/), incluidos Office 365, Dynamics CRM Online, Microsoft Intune y miles de aplicaciones de nube de Microsoft no. 
-   Azure Active Directory es compatible con control de acceso basado en roles. Puede usar el patrón de [Authorize(Roles="X")] con un mínimo de cambios en el código.

Para ver cómo escribir una aplicación de Azure de línea de negocio que autentica con Azure Active Directory, vea [crear una aplicación de Azure de línea de negocio con autenticación de Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md).

## <a name="authenticate-through-an-on-premises-sts"></a>Autenticar a través de un STS local
Si tiene un local servicio de token seguro (STS) como servicios de federación de Active Directory (AD FS), puede usar federar autenticación para su aplicación de Azure. Este enfoque es el mejor cuando la directiva de empresa impide que los datos de AD no pueden almacenarse en Azure. Sin embargo, tenga en cuenta lo siguiente:

-   Topología de STS debe estar implementado en las instalaciones, con sobrecarga de costo y la administración.
-   Solo los administradores de AD FS pueden configurar [confianzas de entidades de confianza y las reglas de notificación](http://technet.microsoft.com/library/dd807108.aspx), que pueden limitar las opciones del desarrollador. Por otro lado, es posible administrar y personalizar [notificaciones](http://technet.microsoft.com/library/ee913571.aspx) sobre según la aplicación.
-   Acceso a local datos AD requieren una solución independiente a través del firewall corporativo.

Para ver cómo escribir una aplicación de Azure de línea de negocio que se autentica con un STS local, consulte [crear una aplicación de Azure de línea de negocio con la autenticación de AD FS](web-sites-dotnet-lob-application-adfs.md).
 
