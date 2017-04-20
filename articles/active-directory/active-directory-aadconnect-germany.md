<properties
    pageTitle="Azure AD Connect en Alemania de nube de Microsoft"
    description="Azure AD Connect integrará los directorios locales con Azure Active Directory. Esto le permite proporcionar una identidad común para las aplicaciones de Office 365, Azure y SaaS integradas con Azure AD."
    keywords="Introducción a Azure AD Connect, información general de Azure AD Connect, ¿qué es Azure AD Connect, instalar active directory, Alemania, negro bosque"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/08/2016"
    ms.author="billmath"/>

#<a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect en la nube de Microsoft Alemania - Public Preview

## <a name="introduction"></a>Introducción
Azure AD Connect proporciona sincronización entre su local de Active Directory y Azure Active Directory.
Actualmente, muchos de los escenarios de [nube de Microsoft Alemania](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) deben ser realizados por el operador. Cuando se usa Microsoft Cloud Alemania, debe tener en cuenta lo siguiente:


- Las siguientes direcciones URL deben abrirse en un servidor proxy para que se realizan correctamente la sincronización:
    - *. microsoftonline.de
    - *. windows.net
    - + Listas de revocación de certificados

- Cuando inicie sesión en su directorio de Azure AD, debe utilizar una cuenta en el dominio onmicrosoft.de.
- Las siguientes características no están disponibles:
    - Azure AD Connect estado
    - Actualizaciones automáticas
    - Reescritura de contraseña

## <a name="download"></a>Descargar
Puede descargar Azure AD Connect desde el módulo Azure AD Connect dentro del portal.  Utilice las instrucciones siguientes para localizar el módulo Azure AD Connect.

### <a name="the-azure-ad-connect-blade"></a>La Azure AD Connect placa

Una vez que haya iniciado sesión en el portal de Azure, haga lo siguiente:

1. Vaya a examinar
2.  Seleccione Azure Active Directory
3.  A continuación, seleccione Azure AD Connect

Verá lo siguiente:

![Azure AD Connect placa](media\active-directory-aadconnect-germany\germany1.png)

 
La siguiente tabla describe las características que se muestra en el módulo.


Título|Descripción|
----- | ----- |
ESTADO DE SINCRONIZACIÓN|Vamos a sabe si la sincronización está activada o desactivada.|
ÚLTIMA SINCRONIZACIÓN|La última vez una sincronización correcta completada.|
DOMINIOS FEDERADOS|Muestra el número de dominios federados están configuradas.|


## <a name="installation"></a>Instalación
Para instalar Azure AD Connect, puede utilizar la documentación [aquí](active-directory-aadconnect.md#install-azure-ad-connect).

## <a name="advanced-features-and-additional-information"></a>Características avanzadas e información adicional
Para obtener más información e instrucciones en una configuración personalizada o configuraciones avanzadas, comience con [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).  Esta página proporciona información y vínculos a instrucciones adicionales.
