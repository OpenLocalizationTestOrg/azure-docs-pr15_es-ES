
<properties 
    pageTitle="Cómo usar RemoteApp de Azure con cuentas de usuario de Office 365 | Microsoft Azure"
    description="Aprenda a usar RemoteApp de Azure con Mis cuentas de usuario de Office 365"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-azure-remoteapp-with-office-365-user-accounts"></a>Cómo usar RemoteApp de Azure con cuentas de usuario de Office 365

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Si tiene una suscripción de Office 365 tiene un Azure Active Directory que almacena los nombres de usuario y contraseñas usadas para tener acceso a servicios de Office 365. Por ejemplo, cuando los usuarios activar Office 365 ProPlus autenticarse Azure AD para comprobar licencias. La mayoría de los clientes le gustaría usar el mismo directorio con RemoteApp de Azure.

Si va a implementar RemoteApp de Azure más probable es que está utilizando una suscripción de Azure que está asociada con un anuncio de Azure diferente. Para utilizar el directorio de Office 365, deberá mover la suscripción de Azure en ese directorio.

Para obtener información sobre cómo implementar aplicaciones cliente de Office 365, vea [cómo usar su suscripción a Office 365 con RemoteApp de Azure](remoteapp-officesubscription.md).
 
## <a name="phase-1-register-your-free-office-365-azure-active-directory-subscription"></a>Fase 1: Registrar su suscripción gratuita de Office 365 Azure Active Directory
Si está utilizando el portal clásico Azure, realice los pasos de [registrar su suscripción gratuita de Azure Active Directory](https://technet.microsoft.com/library/dn832618.aspx) para obtener acceso administrativo a su Azure AD a través del Portal de administración de Azure. Como resultado de este proceso debería poder iniciar sesión en el portal de Azure y consulte su directorio, en este momento no verá mucho más debido a la suscripción completa Azure que usa con Azure RemoteApp está en un directorio diferente.

Recuerde que el nombre y la contraseña de la cuenta de administrador creada en este paso: se necesitará en la fase 2.

Si está utilizando el portal de Azure, consulte [cómo registrar y activar una gratuita Azure Active Directory mediante el portal de Office 365](http://azureblogger.com/2016/01/how-to-register-and-activate-a-free-azure-active-directory-using-office-365-portal/).

## <a name="phase-2-change-the-azure-ad-associated-with-your-azure-subscription"></a>Fase 2: Cambiar el Azure AD asociada a su suscripción de Azure.
Vamos a cambiar su suscripción de Azure desde el directorio actual en el directorio de Office 365 que hemos trabajado con en la fase 1.

Siga las instrucciones descritas en [el inquilino de Azure Active Directory en Azure RemoteApp de cambio](remoteapp-changetenant.md). Preste atención especial a los siguientes pasos:

- Paso #1: Si ha implementado Azure RemoteApp (ARA) en esta suscripción, asegúrese de que quitar todas las cuentas de usuario de Azure AD de las colecciones de ARA en primer lugar, antes de intentar nada. Como alternativa, puede eliminar cualquier colecciones existentes.
- Paso #2: Este es un paso crítico. Debe usar una cuenta de Microsoft (por ejemplo, @outlook.com) como un administrador de servicios de suscripción; esto es porque no podemos tenemos las cuentas de usuario de Azure AD existente vinculado a la suscripción: si se hace, no podremos moverla a un anuncio de Azure diferente.
- Paso 4 #: Al agregar un directorio existente, el sistema le pedirá que inicie sesión con la cuenta de administrador para ese directorio. Asegúrese de usar la cuenta de administrador de la fase 1.
- Paso 5 #: Cambiar el directorio principal de la suscripción al directorio de Office 365. El resultado final debe ser que en Configuración -> suscripciones su suscripción muestra el directorio de Office 365. 
![Cambiar el directorio principal de la suscripción](./media/remoteapp-o365user/settings.png)
 

En este momento su suscripción de Azure RemoteApp está asociada a su Office 365 Azure AD; ¡Puede usar las cuentas de usuario de Office 365 existentes con Azure RemoteApp!




