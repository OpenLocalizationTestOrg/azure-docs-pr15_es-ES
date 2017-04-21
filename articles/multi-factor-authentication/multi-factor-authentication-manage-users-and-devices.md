<properties 
    pageTitle="Informes de Azure autenticación multifactor"
    description="Describe cómo cambiar la configuración de usuario como obligar a los usuarios realizar de nuevo el proceso de prueba."
    documentationCenter=""
    services="multi-factor-authentication"
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

# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Administrar la configuración de usuario con autenticación multifactor de Azure en la nube

Como administrador, puede administrar la siguiente configuración de usuario y del dispositivo.  

- [Requerir que los usuarios seleccionados proporcionar métodos de contacto nuevo](#require-selected-users-to-provide-contact-methods-again)
- [Eliminar usuarios existentes contraseñas de aplicaciones](#delete-users-existing-app-passwords)
- [Restaurar AMF en todos los dispositivos suspendidos para un usuario](#restore-mfa-on-all-suspended-devices-for-a-user)






Esto es útil si un equipo o dispositivo se pierde o robe o debe quitar el acceso de usuarios.


## <a name="require-selected-users-to-provide-contact-methods-again"></a>Requerir que los usuarios seleccionados proporcionar métodos de contacto nuevo

Esta configuración de obliga al usuario para completar el proceso de registro nuevo cuando inicie la sesión de. Tenga en cuenta que las aplicaciones del explorador no continuarán funcionando si el usuario tiene contraseñas de aplicaciones para ellos.  Puede eliminar las contraseñas de los usuarios aplicación seleccionando también **Eliminar todas las contraseñas de aplicación existente generadas por los usuarios seleccionados**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Cómo requerir que los usuarios que proporcionen métodos de contacto nuevo




1. Inicie sesión en el portal de clásico de Azure.
2. En la parte izquierda, haga clic en Active Directory.
3. En, haga clic en directorio en el directorio para el usuario que desea solicitar que para proporcionar de nuevo su método de contacto.
4. En la parte superior, haga clic en usuarios.
5. En la parte inferior de la página, haga clic en administrar la autenticación de varios factores Se abrirá la página de autenticación multifactor.
6. Busque el usuario que desea administrar y coloque una marca en el cuadro situado junto al nombre. Debe cambiar la vista en la parte superior.
7. Aparecerá el vínculo **Administrar la configuración de usuario** de la derecha. Haga clic en él.
8. Marque requieren que **los usuarios seleccionados proporcionar métodos de contacto nuevo**.
![Proporcionar métodos de contacto](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
10. Haga clic en Guardar.
11. Haga clic en Cerrar

## <a name="delete-users-existing-app-passwords"></a>Eliminar usuarios existentes contraseñas de aplicaciones

Esto elimina todas las contraseñas de aplicación que ha creado un usuario. Aplicaciones de explorador sin que se asociaron con estas contraseñas de aplicación dejará de funcionar hasta que se crea una nueva contraseña de aplicación.

### <a name="how-to-delete-users-existing-app-passwords"></a>Cómo eliminar los usuarios existentes contraseñas de aplicaciones

1. Inicie sesión en el portal de clásico de Azure.
2. En la parte izquierda, haga clic en Active Directory.
3. En, haga clic en directorio en el directorio para el usuario que desea eliminar las contraseñas de la aplicación de.
4. En la parte superior, haga clic en usuarios.
5. En la parte inferior de la página, haga clic en administrar la autenticación de varios factores Se abrirá la página de autenticación multifactor.
6. Busque el usuario que desea administrar y coloque una marca en el cuadro situado junto al nombre. Debe cambiar la vista en la parte superior.
7. Aparecerá el vínculo **Administrar la configuración de usuario** de la derecha. Haga clic en él.
8. Coloque una marca en **Eliminar todas las contraseñas de aplicación existente generadas por los usuarios seleccionados**.
![Eliminar contraseñas de aplicaciones](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
10. Haga clic en Guardar.
10. Haga clic en Cerrar.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restaurar AMF en todos los dispositivos de recordar para un usuario

Los administradores tienen la capacidad para restaurar la autenticación multifactor en exploradores y dispositivos de los usuarios. Al hacerlo, se quitará la recordar AMF de todos sus dispositivos y exploradores y el usuario deberán usar AMF al iniciar sesión en la próxima vez.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Cómo restaurar AMF en todos los dispositivos suspendidos para un usuario

1. Inicie sesión en el portal de clásico de Azure.
2. En la parte izquierda, haga clic en Active Directory.
3. En, haga clic en directorio en el directorio para el usuario que desea restaurar AMF en.
4. En la parte superior, haga clic en usuarios.
5. En la parte inferior de la página, haga clic en administrar la autenticación de varios factores Se abrirá la página de autenticación multifactor.
6. Busque el usuario que desea administrar y coloque una marca en el cuadro situado junto al nombre. Debe cambiar la vista en la parte superior.
7. Aparecerá el vínculo **Administrar la configuración de usuario** de la derecha. Haga clic en él.
8. Coloque una marca en **restaurar la autenticación multifactor en todos los dispositivos de recordar**
![eliminar contraseñas de aplicaciones](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Haga clic en Guardar.
10. Haga clic en Cerrar.
