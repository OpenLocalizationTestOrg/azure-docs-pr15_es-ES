
<properties
    pageTitle="Cambiar el inquilino de Azure Active Directory en Azure RemoteApp | Microsoft Azure"
    description="Obtenga información sobre cómo cambiar el inquilino de Azure Active Directory asociado a RemoteApp de Azure"
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



# <a name="change-the-azure-active-directory-tenant-in-azure-remoteapp"></a>Cambiar al inquilino de Azure Active Directory en RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

RemoteApp Azure usa Azure Active Directory (AD Azure) para permitir el acceso de usuario. El inquilino solo Azure AD que puede usar en Azure RemoteApp es el asociada con la suscripción de Azure. Puede ver la suscripción asociada en la página de **configuración** en el portal. Mire la columna de **directorio** en la ficha **suscripciones** .

> [AZURE.NOTE] Este cambio correctamente, en primer lugar, quite todos los usuarios desde el inquilino de Azure Active Directory existente de todas las colecciones de RemoteApp de Azure. Para ello, vaya al Portal de Azure, vaya a la pestaña **RemoteApp de Azure** y abra cada colección de RemoteApp de Azure. Vaya a la ficha **usuarios** y quitar los usuarios que pertenecen a su inquilino actual de Azure Active Directory. Repita para todas las colecciones de Azure RemoteApp existentes. Sin hacer esto, no podrá crear o colecciones de revisiones.

Si desea usar a un inquilino diferente, siga estos pasos para cambiar la asociación con su suscripción:

1. En el portal, quitar cualquier usuario de Azure AD a los que haya dado acceso a las colecciones de RemoteApp de Azure. (Consulte la nota anterior para conocer los pasos sobre cómo hacerlo).


2. Configurar una cuenta de Microsoft (anteriormente denominada un Live ID) como el administrador del servicio. ¿(No sabe si ya es el administrador del servicio? Puede obtener haciendo clic en **Opciones -> administradores**.) Ahora, le mostramos cómo cambiar esto:
    1. Haga clic en el usuario en la esquina superior derecha y, a continuación, haga clic en **Ver mi factura**.
    2. Haga clic en la suscripción. A continuación, en la nueva página, desplácese hacia abajo y haga clic en **Editar detalles de la suscripción** en la derecha. (Ordenar de la central inferior derecha, que le ayuda a encontrarlo.)
    3. Escriba la cuenta de Microsoft para el usuario que debe ser el Administrador de servicio.

3. Ahora, cerrar sesión en el portal y, a continuación, inicie sesión con la cuenta de Microsoft especificado en el paso anterior.


4. Haga clic en **New -> aplicación Servicios -> Active Directory -> directorio -> personalizado crear**.
5. En el **directorio**, elija **Usar directorio existente**. Vamos a tenga que cerrar sesión en el portal de ahora, así que seleccione **que estoy listo para la sesión ahora**.
6. Hacer copia de inicio de sesión en el portal como administrador global del directorio que desea agregar. (Si ya no un administrador global, estará después de una fase de inicio de sesión y, a continuación, cerrar sesión.)
7. Se le pedirá al iniciar sesión si desea usar al inquilino AD existente con su suscripción. Haga clic en **continuar**y, a continuación, haga clic en **Cerrar sesión ahora**.
5. Vuelva a iniciar sesión en y volver a **Configuración -> suscripciones**. Seleccione la suscripción y, a continuación, haga clic en **Editar directorio**. Seleccione al inquilino de Azure AD que desea usar.



Puede que el inquilino de usar la nueva Azure AD ahora para controlar el acceso a la suscripción de Azure y configurar el acceso de usuario de RemoteApp de Azure.
