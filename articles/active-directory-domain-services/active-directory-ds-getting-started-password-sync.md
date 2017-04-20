<properties
    pageTitle="Servicios de dominio de Azure AD: Habilitar la sincronización de contraseña | Microsoft Azure"
    description="Introducción a servicios de dominio de Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Habilitar la sincronización de contraseña a los servicios de dominio de Active Directory de Azure
En tareas anteriores, había habilitado Servicios de dominio de Active Directory de Azure para su inquilino de Azure AD. La siguiente tarea es habilitar hash credencial necesarios para que la autenticación Kerberos y NTLM sincronizar los servicios de dominio de Active Directory de Azure. Una vez configurada la sincronización de credenciales, los usuarios pueden iniciar sesión en el dominio administrado usando sus credenciales corporativas.

Los pasos son diferentes en función de si su organización tiene un solo nube Azure AD de inquilinos o está configurado para sincronizarse con el directorio local con Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Solo nube Azure AD de inquilinos](active-directory-ds-getting-started-password-sync.md)
- [Sincronizado el inquilino de Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Tarea 5: Habilitar la sincronización de contraseña a los servicios de dominio de AAD para un solo nube Azure inquilino AD
Servicios de dominio de Active Directory Azure necesitan credenciales hash en un formato adecuado para la autenticación Kerberos y NTLM, para autenticar a los usuarios en el dominio administrado. A menos que habilite AAD servicios de dominio de su inquilino, Azure AD genere ni almacenar credenciales hash en el formato requerido para la autenticación de NTLM o Kerberos. Por motivos de seguridad obvios Azure AD también no almacenar credenciales en forma de texto sin cifrar. Por lo tanto, Azure AD no tiene una forma de generar estos hash credenciales NTLM o Kerberos en función de las credenciales de los usuarios existentes.

> [AZURE.NOTE] Si su organización tiene una nube solo inquilino de Azure AD, los usuarios que necesiten para usar los servicios de dominio de Active Directory de Azure debe cambiar sus contraseñas.

Este proceso de cambio de contraseña hace que la credencial hash necesarios para servicios de dominio de Active Directory de Azure para la autenticación Kerberos y NTLM a generarse en Azure AD. O bien puede caducar las contraseñas de todos los usuarios en el inquilino que necesite usar servicios de dominio de Active Directory de Azure o indicar a los usuarios cambiar sus contraseñas.


### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Habilitar la generación de hash credenciales NTLM y Kerberos para un solo nube Azure inquilino AD
Estas son instrucciones que debe proporcionar a los usuarios finales, para que puedan cambiar sus contraseñas:

1. Vaya a la página de Panel de Azure AD acceso para su organización en [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Seleccione la ficha de **perfil** en esta página.

3. Haga clic en el mosaico de **cambiar la contraseña** en esta página.

    ![Crear una red virtual para los servicios de dominio de Active Directory de Azure.](./media/active-directory-domain-services-getting-started/user-change-password.png)

    > [AZURE.NOTE] Si no ve la opción **Cambiar contraseña** en la página Panel de acceso, asegúrese de que su organización ha configurado [la administración de contraseñas en Azure AD](../active-directory/active-directory-passwords-getting-started.md).

4. En la página **Cambiar contraseña** , escriba la contraseña existente (antigua) y, a continuación, escriba una nueva contraseña y confírmela. Haga clic en **Enviar**.

    ![Crear una red virtual para los servicios de dominio de Active Directory de Azure.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Después de haber cambiado la contraseña, la nueva contraseña podrán utilizarse en servicios de dominio de Active Directory de Azure breve. Después de unos minutos (normalmente, minutos aproximadamente 20), puede iniciar sesión en equipos unidos al dominio administrado utilizando la contraseña ha cambiado.

<br>

## <a name="related-content"></a>Contenido relacionado

- [Cómo actualizar su propia contraseña](../active-directory/active-directory-passwords-update-your-own-password.md)

- [Introducción a la administración de contraseñas en Azure AD](../active-directory/active-directory-passwords-getting-started.md).

- [Habilitar la sincronización de contraseña a los servicios de dominio de AAD para un Azure sincronizada inquilino AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Administrar un dominio administrado de servicios de dominio de Active Directory de Azure](active-directory-ds-admin-guide-administer-domain.md)

- [Unirse a una máquina virtual de Windows a un dominio administrado de servicios de dominio de Active Directory de Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Unirse a una máquina virtual rojos sombrero Enterprise Linux a un dominio administrado de servicios de dominio de Active Directory de Azure](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
