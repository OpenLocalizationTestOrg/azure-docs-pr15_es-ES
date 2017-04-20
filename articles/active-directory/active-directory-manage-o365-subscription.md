<properties
   pageTitle="Administrar el directorio de suscripción de Office 365 en Azure | Microsoft Azure"
   description="Administración de un directorio de suscripción de Office 365 con Azure Active Directory y el portal de clásico de Azure"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Administrar el directorio de suscripción de Office 365 en Azure

En este artículo se describe cómo administrar un directorio que se creó para una suscripción de Office 365, con el portal de clásico de Azure. Debe ser administrador del servicio o un administrador de colaboración de una suscripción de Azure para iniciar sesión en el portal de clásico de Azure. Si todavía no tiene una suscripción de Azure, puede registrarse para una [prueba gratuita de 30 días](https://azure.microsoft.com/trial/get-started-active-directory/) hoy e implementar la solución de nube primera en menos de 5 minutos, con este vínculo. Asegúrese de usar la cuenta profesional o educativa que usa para iniciar sesión en Office 365.

Después de completar la suscripción de Azure, puede iniciar sesión en el portal de clásico Azure y acceder a los servicios de Azure. Haga clic en la extensión de Active Directory para administrar el mismo directorio que autentica a los usuarios de Office 365.

Si ya tiene una suscripción de Azure, el proceso para administrar un directorio adicional también es sencillo. Por ejemplo, Michael Smith posible que tenga una suscripción a Office 365 para Contoso.com. También tiene una suscripción de Azure iniciado sesión con su cuenta de Microsoft, msmith@hotmail.com. En este caso, encarga de los dos directorios.

  Suscripción |  Office 365  |  Azure
  -------------- | ------------- | -------------------------------
  Nombre para mostrar |  Contoso  |     Directorio de Azure Active Directory (AD Azure) predeterminado
  Nombre de dominio  |  contoso.com  | msmithhotmail.onmicrosoft.com

Quiere administrar las identidades de usuario en el directorio de Contoso, mientras que ha iniciado sesión en Azure con su cuenta de Microsoft, para que puede habilitar las características de Azure AD como la autenticación. El siguiente diagrama puede ayudar a ilustrar el proceso.

![Diagrama para administrar dos directorios independientes](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

En este caso, los dos directorios son independientes entre sí.

## <a name="to-manage-two-independent-directories"></a>Administrar dos directorios independientes
En orden de Michael Smith administrar ambos directorios mientras que ha iniciado sesión en Azure como msmith@hotmail.com, debe completar los pasos siguientes:

> [AZURE.NOTE]
> Pueden completar estos pasos únicamente cuando un usuario ha iniciado sesión con una cuenta de Microsoft. Si el usuario ha iniciado sesión con un trabajo o escuela, la opción para **Usar directorio existente** no está disponible. Una cuenta profesional o educativa sólo puede autenticar su directorio de inicio (es decir, el directorio donde está almacenada la cuenta profesional o educativa, y que es propietario de la empresa o escuela).

1.  Inicie sesión en el [portal de clásica Azure](https://manage.windowsazure.com) como msmith@hotmail.com.
2.  Haga clic en **nuevo** > **Servicios de aplicación** > **Active Directory** > **directorio** > **Crear personalizado**.
3.  Haga clic en Usar directorio existente y seleccione la casilla de verificación **estoy listo para la sesión ahora** .
4.  Inicie sesión en el portal de clásico Azure como administrador global de Contoso.onmicrosoft.com (por ejemplo, msmith@contoso.com).
5.  Cuando se le indique que **utilizar el directorio de Contoso con Azure?**, haga clic en **continuar**.
6.  Haga clic en **Cerrar sesión ahora**.
7.  Inicie sesión en el portal de Azure clásico como msmith@hotmail.com. El directorio de Contoso y el directorio predeterminado aparecen en la extensión de Active Directory.

Tras completar estos pasos, msmith@hotmail.com es un administrador global en el directorio de Contoso.

## <a name="to-administer-resources-as-the-global-admin"></a>Administrar recursos como administrador global
Ahora supongamos que Jane Doe necesita administrar los recursos de base de datos y sitios Web que están asociados a la suscripción de Azure para msmith@hotmail.com. Antes de que puede hacerlo, Michael Smith deben completar estos pasos adicionales:

1.  Inicie sesión en el [portal de clásica Azure](https://manage.windowsazure.com) con la cuenta de administrador de servicio para la suscripción de Azure (en este ejemplo, msmith@hotmail.com).
2.  Transferir la suscripción en el directorio de Contoso: haga clic en **configuración** > **suscripciones** > seleccione la suscripción > **Editar directorio** > Seleccionar **Contoso (Contoso.com)**. Como parte de la transferencia, todo el trabajo o escuela se quitan las cuentas que están coadministradores de la suscripción.
3.  Agregar Jane Doe como compañero administrador de la suscripción: haga clic en **configuración** > **administradores** > seleccione la suscripción > **Agregar** > tipo **JohnDoe@Contoso.com**.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre la relación entre las suscripciones y directorios, vea [cómo se asocia a un directorio de una suscripción](active-directory-how-subscriptions-associated-directory.md).
