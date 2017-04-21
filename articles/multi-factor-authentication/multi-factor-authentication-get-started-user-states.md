<properties 
    pageTitle="Estados de usuario de autenticación multifactor de Microsoft Azure"
    description="Obtenga información sobre los Estados de usuario en Azure AMF."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="user-states-in-azure-multi-factor-authentication"></a>Estados de usuario en Azure autenticación multifactor

Cuentas de usuario de autenticación multifactor de Azure tienen los siguientes tres estados diferentes:

Estado | Descripción |Aplicaciones de explorador no afectadas| Notas
:-------------: | :-------------: |:-------------: |:-------------: |
Deshabilitado | El estado predeterminado para un nuevo usuario que no se ha inscrito en autenticación multifactor.|No|El usuario no está utilizando la autenticación multifactor.
Habilitado |El usuario se ha inscrito en autenticación multifactor.|No.  Siguen funcionando hasta que se complete el proceso de registro.|El usuario está habilitado, pero no ha finalizado el proceso de registro. Se le pedirá que completar el proceso de inicio de sesión en la siguiente.
Aplica|El usuario se ha inscrito y ha finalizado el proceso de registro para el uso de la autenticación multifactor.|Sí.  Aplicaciones requieren contraseñas de aplicaciones. | El usuario puede o no haya completado el registro. Si se han completado el proceso de registro, que están usando autenticación multifactor. En caso contrario, se le indicará al usuario para completar el proceso de inicio de sesión en la siguiente.

## <a name="changing-a-user-state"></a>Cambiar el estado de un usuario
Cambios de estado de los usuarios dependiendo de si ha sido el programa de instalación para AMF y si el usuario ha completado el proceso.  Cuando se activa AMF para un usuario, los usuarios que se cambiará el estado de deshabilitan a habilitado.  Una vez que el usuario, cuyo estado se ha cambiado a habilitada, inicia sesión y finaliza el proceso, su estado cambia a obligatoria.  

### <a name="to-view-a-users-state"></a>Para ver el estado de un usuario
--------------------------------------------------------------------------------
1.  Inicie sesión como administrador en el **portal de clásico de Azure** .
2.  En la parte izquierda, haga clic en **Active Directory**.
3.  En, haga clic en **directorio** en el directorio para el usuario que desea habilitar.
![Haga clic en el directorio](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En la parte superior, haga clic en **usuarios**.
5.  En la parte inferior de la página, haga clic en **Administrar la autenticación de varios factores**.
![Haga clic en el directorio](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Se abrirá una nueva pestaña del explorador.  Podrá ver el estado de los usuarios.
![Haga clic en el directorio](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

###<a name="to-change-the-state-from-disabled-to-enabled"></a>Para cambiar el estado de deshabilitado habilitado
1.  Inicie sesión como administrador en el **portal de clásico de Azure** .
2.  En la parte izquierda, haga clic en **Active Directory**.
3.  En, haga clic en **directorio** en el directorio para el usuario que desea habilitar.
![Haga clic en el directorio](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En la parte superior, haga clic en **usuarios**.
5.  En la parte inferior de la página, haga clic en **Administrar la autenticación de varios factores**.
![Haga clic en el directorio](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Se abrirá una nueva pestaña del explorador.  Busque el usuario que desea habilitar la autenticación multifactor. Debe cambiar la vista en la parte superior. Asegúrese de que el estado es **deshabilitado.** 
 ![Permitir al usuario](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Coloque un **Active** en el cuadro junto a su nombre.
7.  A la derecha, haga clic en **Habilitar**.
![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Haga clic en **Habilitar la autenticación multifactor**.
![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Tenga en cuenta que el estado del usuario ha cambiado de **deshabilitado** a **habilitado**.
![Permitir a los usuarios](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Después de habilitar a los usuarios, se recomienda que se les notifique por correo electrónico.  También debe informar a ellos cómo pueden usar las aplicaciones de explorador no para evitar que se bloquee.

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>Para cambiar el estado de habilitado de aplica a deshabilitado
1.  Inicie sesión como administrador en el **portal de clásico de Azure** .
2.  En la parte izquierda, haga clic en **Active Directory**.
3.  En, haga clic en **directorio** en el directorio para el usuario que desea habilitar.
![Haga clic en el directorio](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En la parte superior, haga clic en **usuarios**.
5.  En la parte inferior de la página, haga clic en **Administrar la autenticación de varios factores**.
![Haga clic en el directorio](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Se abrirá una nueva pestaña del explorador.  Busque el usuario que desea desactivar. Debe cambiar la vista en la parte superior. Asegúrese de que el estado es cualquiera **habilitado** o **aplica.**
7.  Coloque un **Active** en el cuadro junto a su nombre.
7.  A la derecha, haga clic en **Deshabilitar**.
![Deshabilitar usuario](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
8.  Se le pedirá que confirme este.  Haga clic en **Sí**.
![Deshabilitar usuario](./media/multi-factor-authentication-get-started-user-states/userstate3.png)
9.  A continuación, verá que se realizó correctamente.  Haga clic en **Cerrar.** 
 ![Deshabilitar usuario](./media/multi-factor-authentication-get-started-user-states/userstate4.png)
