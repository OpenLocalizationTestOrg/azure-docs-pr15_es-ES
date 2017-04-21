<properties
    pageTitle="¿Cuáles son las contraseñas de aplicación en Azure AMF?"
    description="Esta página le ayudará a los usuarios a comprender qué son las contraseñas de aplicaciones y lo que se usan para con respecto a Azure AMF."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>



# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>¿Cuáles son las contraseñas de aplicaciones en la autenticación multifactor de Azure?

Determinadas aplicaciones no son exploradores, como el cliente de correo electrónico nativo de Apple que utiliza Exchange Active Sync, no se admite actualmente autenticación multifactor. Autenticación multifactor está habilitada por usuario. Esto significa que si un usuario se ha habilitado para la autenticación multifactor y está tratando de usar las aplicaciones de explorador no, no se podrán hacerlo. Contraseña de aplicación permite que se produzca.

>[AZURE.NOTE] Autenticación moderna para los clientes de Office 2013
>
> Clientes de Office 2013 (como Outlook) ahora admiten nuevos protocolos de autenticación y se pueden habilitar para admitir autenticación multifactor.  Esto significa que una vez habilitada, las contraseñas de aplicación no son necesarias para los clientes de Office 2013.  Para obtener más información, vea [Office 2013 autenticación moderna público preview anunciada](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

## <a name="how-to-use-app-passwords"></a>Cómo usar contraseñas de aplicaciones

Los siguientes son algunas de las cosas que recordar sobre cómo usar las contraseñas de aplicaciones.

- La contraseña real se genera automáticamente y no se proporciona por el usuario. Esto es porque es más difícil para un intruso adivinar la contraseña generada automáticamente y es más seguro.
- Hay un límite de 40 contraseñas por usuario. Si intenta crear uno una vez que ha alcanzado el límite, se le pedirá para eliminar una de las contraseñas de aplicación existentes para crear uno nuevo.
- Se recomienda crear contraseñas de aplicaciones por dispositivo y no por la aplicación. Por ejemplo, puede crear una contraseña de aplicación para su equipo portátil y utilizar esa contraseña de aplicación para todas las aplicaciones en ese equipo portátil.
- Tiene una contraseña de aplicación la primera vez que inicio de sesión.  Si necesita otras adicionales, puede crearlos.

![Programa de instalación](./media/multi-factor-authentication-end-user-app-passwords/app.png)

Una vez que tenga una contraseña de aplicación, se utiliza en lugar de la contraseña original con estas aplicaciones no son exploradores.  Por ejemplo, si está utilizando la autenticación multifactor y el cliente de correo electrónico nativo de Apple en su teléfono.  Utilice la contraseña de aplicación para que puede omitir la autenticación multifactor y seguir trabajando.

## <a name="creating-and-deleting-app-passwords"></a>Crear y eliminar contraseñas de aplicaciones
Durante el inicio de sesión inicial tiene una contraseña de aplicación que puede usar.  Además puede crear y eliminar las contraseñas de la aplicación más adelante.  Forma de hacerlo depende de cómo usar autenticación multifactor.  Elegir la que mejor se aplica a usted.

Cómo usar multifactor authentiation|Descripción
:------------- | :------------- |
[Usarla con Office 365](#creating-and-deleting-app-passwords-with-office-365)|  Esto significa que desea crear contraseñas de aplicación a través del portal de Office 365.
[No sé](#creating-and-deleting-app-passwords-with-myapps-portal)|Esto significa que va a crear contraseñas de aplicaciones a través de [https://myapps.microsoft.com](https://myapps.microsoft.com)
[Usarla con Microsoft Azure](#create-app-passwords-in-the-azure-portal)| Esto significa que desee crear contraseñas de aplicación a través del portal de Azure.

## <a name="creating-and-deleting-app-passwords-with-office-365"></a>Crear y eliminar contraseñas de aplicaciones con Office 365

Si utiliza la autenticación multifactor con Office 365 desea crear y eliminar las contraseñas de aplicación a través del portal de Office 365.

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Para crear contraseñas de aplicaciones en el portal de Office 365
--------------------------------------------------------------------------------

1. Inicie sesión en el [portal de Office 365](https://login.microsoftonline.com/).
2. En la esquina superior derecha del widget de selección y elija Configuración de Office 365.
3. Haga clic en comprobación de seguridad adicional.
4. A la derecha, haga clic en el vínculo que dice **Actualizar mis números de teléfono que se utiliza para la seguridad de la cuenta.** 
 ![Configuración](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Esto le llevará a la página que permite cambiar la configuración.
![Nube](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. En la parte superior, junto a la comprobación de seguridad adicionales, haga clic en **contraseñas de aplicaciones.**
7. Haga clic en **crear**.
![Nube](./media/multi-factor-authentication-end-user-app-passwords-create-o365/apppass.png)
8. Escriba un nombre para la contraseña de la aplicación y haga clic en **siguiente**.
![Crear una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. Copiar la contraseña de aplicación en el Portapapeles y pegarlo en la aplicación.
![Crear una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/create2.png)


### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Para eliminar las contraseñas de la aplicación con el portal de Office 365
--------------------------------------------------------------------------------


1. Inicie sesión en el [portal de Office 365](https://login.microsoftonline.com/).
2. En la esquina superior derecha seleccione el widget y elija Configuración de Office 365.
3. Haga clic en comprobación de seguridad adicional.
4. A la derecha, haga clic en el vínculo que dice **Actualizar mis números de teléfono que se utiliza para la seguridad de la cuenta.** 
 ![Configuración](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Esto le llevará a la página que permite cambiar la configuración.
![Nube](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. En la parte superior, junto a la comprobación de seguridad adicionales, haga clic en **contraseñas de aplicaciones.**
7. Seleccione la contraseña de aplicación que desea eliminar, haga clic en **Eliminar**.
![Eliminar una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. Confirmar la eliminación, haga clic en **Sí**.
![Confirmar la eliminación](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. Una vez que se elimina la contraseña de aplicación haga clic en **Cerrar**.
![Cerrar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="creating-and-deleting-app-passwords-with-myapps-portal"></a>Crear y eliminar contraseñas de aplicaciones con el portal de mis aplicaciones.
Si no está seguro de cómo usar autenticación multifactor, siempre puede crear y eliminar las contraseñas de aplicación a través del portal de mis aplicaciones.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Para crear una contraseña de aplicación con el portal de mis aplicaciones

1. Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. En la parte superior, seleccione el perfil.
3. Seleccione la comprobación de seguridad adicional.
![Nube](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Esto le llevará a la página que permite cambiar la configuración.
![Programa de instalación](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. En la parte superior, junto a la comprobación de seguridad adicionales, haga clic en **contraseñas de aplicaciones.**
6. Haga clic en **crear**.
![Crear una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. Escriba un nombre para la contraseña de la aplicación y haga clic en **siguiente**.
![Crear una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. Copiar la contraseña de aplicación en el Portapapeles y pegarlo en la aplicación.
![Crear una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Para eliminar una contraseña de aplicación con el portal de mis aplicaciones

1. Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. En la parte superior, seleccione el perfil.
3. Seleccione la comprobación de seguridad adicional.
![Nube](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Esto le llevará a la página que permite cambiar la configuración.
![Programa de instalación](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. En la parte superior, junto a la comprobación de seguridad adicionales, haga clic en **contraseñas de aplicaciones.**
6. Seleccione la contraseña de aplicación que desea eliminar, haga clic en **Eliminar**.
![Eliminar una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. Confirmar la eliminación, haga clic en **Sí**.
![Confirmar la eliminación](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. Una vez que se elimina la contraseña de aplicación haga clic en **Cerrar**.
![Cerrar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="create-app-passwords-in-the-azure-portal"></a>Crear contraseñas de aplicaciones en el portal de Azure

Si utiliza la autenticación multifactor con Azure desea crear contraseñas de aplicación a través del portal de Azure.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Para crear contraseñas de aplicaciones en el portal de Azure

1. Inicie sesión en el portal de administración de Azure.
2. En la parte superior, haga clic en el nombre de usuario y seleccione la comprobación de seguridad adicionales.
3. En la página proofup, en la parte superior, seleccione contraseñas de aplicaciones
4. Haga clic en **crear**.
5. Escriba un nombre para la contraseña de la aplicación y haga clic en **siguiente**
6. Copiar la contraseña de aplicación en el Portapapeles y pegarlo en la aplicación.
![Nube](./media/multi-factor-authentication-end-user-app-passwords-create-azure/app2.png)

### <a name="to-delete-app-passwords-in-the-azure-portal"></a>Para eliminar las contraseñas de aplicaciones en el portal de Azure

1. Inicie sesión en el portal de administración de Azure.
2. En la parte superior, haga clic en el nombre de usuario y seleccione la comprobación de seguridad adicionales.
3. En la parte superior, junto a la comprobación de seguridad adicionales, haga clic en **contraseñas de aplicaciones.**
4. Seleccione la contraseña de aplicación que desea eliminar, haga clic en **Eliminar**.
5. Confirmar la eliminación, haga clic en **Sí**.
6. Una vez que se elimina la contraseña de aplicación haga clic en **Cerrar**.
![Cerrar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)
