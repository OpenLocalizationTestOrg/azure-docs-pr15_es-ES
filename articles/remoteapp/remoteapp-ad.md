
<properties 
    pageTitle="Azure AD + requisitos de Active Directory para RemoteApp de Azure | Microsoft Azure" 
    description="Aprenda a configurar Active Directory para trabajar con RemoteApp de Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="azure-ad--active-directory-requirements-for-azure-remoteapp"></a>Azure AD + requisitos de Active Directory para RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.


Para la colección de híbrido RemoteApp de Azure o una colección de nube que desee federarse con AD Connect, debe hacer lo siguiente.

### <a name="connect-azure-ad-and-active-directory"></a>Conectar Azure AD y Active Directory

Si desea conectar el inquilino de Azure AD y los entornos de Active Directory local, use AD Connect. Esta acción tardará solo [4 clics](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) para conectar los dos directorios.

Nota: se requiere colecciones híbrido de sincronización de directorios.

### <a name="make-sure-your-domaincom-match"></a>Asegúrese de que su "@domain.com" coincide con
Antes de empezar, asegúrese de que el UPN del bosque local coincide con el sufijo del dominio Azure AD. 

Después de configurar el sufijo UPN del dominio en Azure AD, todos los usuarios iniciar sesión en Azure RemoteApp se inicien sesión como “user@ <the suffix you set up>". Asegúrese de que los usuarios también pueden iniciar con el mismo user@suffix en el dominio local. En algunos casos puede configurar un nombre de dominio en Azure AD mientras que especifica un sufijo de dominio diferente para el usuario prem. En este caso, los usuarios no podrán conectarse a los equipos de dominio o los recursos a través de RemoteApp de Azure.

Por ejemplo, si configura el sufijo UPN en AAD como contoso.com, pero algunos usuarios en local/anuncio están configurados para iniciar sesión en @contoso.uk, , a continuación, los usuarios no podrán correctamente, inicie sesión en la colección de ARA. Los usuarios UPN en AAD y AD deben ser el mismo para el inicio de sesión que sea posible"

### <a name="create-objects-for-azure-remoteapp"></a>Crear objetos para RemoteApp de Azure
También debe crear objetos de Active Directory de las instalaciones siguientes:

- Una cuenta de servicio para proporcionar acceso a los recursos de dominio para programas de RemoteApp uniendo puntos finales RDSH al dominio local.
- Una unidad organizativa (OU) que contenga los objetos de equipo de RemoteApp. Uso de la unidad organizativa se recomienda (aunque no es necesario) para aislar las cuentas y las directivas que se usa con RemoteApp.

Necesita estos dos objetos cuando se crea la colección de RemoteApp, así que asegúrese de realizar estos pasos en primer lugar.