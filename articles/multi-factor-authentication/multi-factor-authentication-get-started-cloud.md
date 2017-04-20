<properties
    pageTitle="Obtener iniciado AMF de Azure en la nube | Microsoft Azure"
    description="Esta es la página de autenticación multifactor de Microsoft Azure que describe cómo empezar a trabajar con AMF de Azure en la nube."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Introducción a la autenticación multifactor de Azure en la nube
En este artículo se recorre cómo empezar a usar autenticación multifactor de Azure en la nube.

> [AZURE.NOTE]  La siguiente documentación proporciona información acerca de cómo permitir que los usuarios con el **Portal de Azure clásico**. Si busca información sobre cómo configurar la autenticación multifactor de Azure para usuarios de O365, consulte [Configurar autenticación multifactor para Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![AMF en la nube](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisites"></a>Requisitos previos
Los siguientes requisitos previos son necesarios para poder habilitar la autenticación multifactor de Azure para sus usuarios.


1. [Registrarse para una suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/) - si ya no tiene una suscripción de Azure, debe suscripción para uno. Si está empezando y usando Azure AMF solo puede usar una suscripción de prueba
2. [Crear un proveedor de autenticación multifactor](multi-factor-authentication-get-started-auth-provider.md) y asignar en el directorio o [asignar licencias a los usuarios](multi-factor-authentication-get-started-assign-licenses.md)

> [AZURE.NOTE]  Licencias están disponibles para los usuarios que tienen AMF Azure, Azure AD Premium o conjunto de aplicaciones de movilidad de empresa (EMS).  AMF se incluye en Azure AD Premium y el EMS. Si tiene suficientes licencias, no es necesario crear un proveedor de servicios de autenticación.


## <a name="turn-on-two-step-verification-for-users"></a>Activar la verificación en dos pasos para los usuarios
Para iniciar la necesidad de comprobación de inicio de dos en para un usuario, cambie el estado del usuario de deshabilitado a habilitado.  Para obtener más información sobre los Estados de usuario, vea [Estados de usuario en la autenticación multifactor de Azure](multi-factor-authentication-get-started-user-states.md)

Use el procedimiento siguiente para habilitar AMF para los usuarios.

### <a name="to-turn-on-multi-factor-authentication"></a>Para activar la autenticación multifactor

1.  Inicie sesión como administrador en el [portal de clásico de Azure](https://manage.windowsazure.com) .
2.  En la parte izquierda, haga clic en **Active Directory**.
3.  En el directorio, seleccione el directorio para el usuario que desea habilitar.
![Haga clic en el directorio](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En la parte superior, haga clic en **usuarios**.
5.  En la parte inferior de la página, haga clic en **Administrar la autenticación de varios factores**. Se abrirá una nueva pestaña del explorador.
![Haga clic en el directorio](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Busque el usuario que desea habilitar para la verificación en dos pasos. Debe cambiar la vista en la parte superior. Asegúrese de que el estado es **deshabilitado.** 
 ![Permitir al usuario](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Coloque un **Active** en el cuadro junto a su nombre.
7.  A la derecha, haga clic en **Habilitar**.
![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Haga clic en **Habilitar la autenticación multifactor**.
![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Observe que el estado del usuario ha cambiado de **deshabilitado** a **habilitado**.
![Permitir a los usuarios](./media/multi-factor-authentication-get-started-cloud/user.png)

Después de habilitar a los usuarios, debe notificar por correo electrónico. La próxima vez que intenten iniciar sesión, le pedirá que inscribir su cuenta para la verificación en dos pasos. Una vez que empiecen a usar la verificación en dos pasos, también deberá configurar contraseñas de aplicaciones para evitar que se bloqueen aplicaciones del explorador no.


## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>Usar PowerShell para automatizar activar activó la verificación

Para cambiar el [estado](multi-factor-authentication-whats-next.md) con [Azure AD PowerShell](../powershell-install-configure.md), puede usar el siguiente.  Puede cambiar `$st.State` igual a uno de los estados siguientes:

- Habilitado
- Aplica
- Deshabilitado  

> [AZURE.IMPORTANT]  Nos evitar frente a mover los usuarios directamente desde el estado de deshabilitar el estado de forzada. No se basan en explorador aplicaciones deja de funcionar porque el usuario no ha experimentado registro AMF y obtener una [contraseña de aplicación](multi-factor-authentication-whats-next.md#app-passwords). Si tiene aplicaciones no se basan en el explorador y requerir contraseñas de aplicaciones, se recomienda que vaya desde el estado deshabilitada en habilitado. Esto permite a los usuarios registrar y obtener sus contraseñas de aplicaciones. Después de eso, puede moverlos a forzada.

Uso de PowerShell sería una opción para permitir a los usuarios de forma masiva. Actualmente no hay ninguna característica de habilitar de forma masiva en el portal de Azure y debe seleccionar cada usuario individualmente. Si tiene muchos usuarios, esto puede ser bastante una tarea. Mediante la creación de un PowerShell de secuencias de comandos con los siguientes, puede recorrer una lista de usuarios y habilitarlas.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Aquí tenemos un ejemplo:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Para obtener más información, vea [Estados de usuario en la autenticación multifactor de Azure](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha configurado la autenticación multifactor de Azure en la nube, puede configurar y configurar la implementación. Para obtener más detalles, vea [Configurar la autenticación multifactor Azure](multi-factor-authentication-whats-next.md) .
