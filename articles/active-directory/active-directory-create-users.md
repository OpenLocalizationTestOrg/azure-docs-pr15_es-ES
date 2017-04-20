<properties
    pageTitle="Agregar nuevos usuarios a Azure Active Directory | Microsoft Azure"
    description="Se explica cómo agregar nuevos usuarios o cambiar la información de usuario en Active Directory de Azure."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/22/2016"
    ms.author="curtand"/>

# <a name="add-new-users--or-users-with-microsoft-accounts-to-azure-active-directory"></a>Agregar nuevos usuarios o los usuarios con cuentas de Microsoft a Azure Active Directory

Agregar usuarios para rellenar el directorio. En este artículo se explica cómo agregar nuevos usuarios de su organización y cómo agregar los usuarios que tienen cuentas de Microsoft. Para obtener más información sobre cómo agregar usuarios de otros directorios de Azure Active Directory o agregar usuarios de las empresas asociadas, consulte [Agregar usuarios de otros directorios o las empresas asociadas de Azure Active Directory](active-directory-create-users-external.md). Usuarios agregados no tienen permisos de administrador de forma predeterminada, pero puede asignar roles a ellos en cualquier momento.

## <a name="add-a-user"></a>Agregar un usuario

1. Inicie sesión en el [portal de clásica Azure](https://manage.windowsazure.com) con una cuenta que sea un administrador global para el directorio.
2. Seleccione **Active Directory**y, a continuación, seleccione el nombre del directorio de su organización.
3. Seleccione la ficha **usuarios** y, a continuación, en la barra de comandos, seleccione **Agregar usuario**.
4. En la página **Díganos sobre este usuario** , en **tipo de usuario**, seleccione:

    - **Nuevo usuario de su organización** : agrega una nueva cuenta de usuario en el directorio.
    - **Usuario con una cuenta de Microsoft existente** : agrega una cuenta existente de consumidor de Microsoft en el directorio (por ejemplo, una cuenta de Outlook)

5. Según el **tipo de usuario**, escriba un nombre de usuario (para el nuevo usuario) o una dirección de correo electrónico (para un usuario con una cuenta de Microsoft).
6. En la página de **perfil** de usuario, proporcione un nombre y apellido, nombre descriptivo y una función de usuario de la lista de **funciones** . Para obtener más información acerca de los roles de usuario y administrador, vea [asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles.md). Especifique si desea **Habilitar la autenticación multifactor** para el usuario.
7. En la página **obtener una contraseña temporal** , seleccione **crear**.

> [AZURE.IMPORTANT] Si su organización usa más de un dominio, debe conocer los siguientes problemas al agregar una cuenta de usuario:
>
> - **Para agregar cuentas de usuario con el mismo nombre principal de usuario (UPN) a través de dominios, agregar, por ejemplo,** geoffgrisso@contoso.onmicrosoft.com, **seguido** geoffgrisso@contoso.com.
> - **No** agregar geoffgrisso@contoso.com antes de agregar geoffgrisso@contoso.onmicrosoft.com. Este orden es importante y puede ser difícil de deshacer.

## <a name="change-user-information"></a>Cambiar la información de usuario

Puede cambiar cualquier atributo de usuario excepto el identificador de objeto.

1. Abra el directorio.
2. Seleccione la ficha **usuarios** y, a continuación, seleccione el nombre para mostrar del usuario que desea cambiar.
3. Complete los cambios y, a continuación, haga clic en **Guardar**.

Si el usuario que desea cambiar está sincronizado con el servicio de Active Directory local, no puede cambiar la información de usuario mediante este procedimiento. Para cambiar el usuario, use las herramientas de administración de Active Directory local.

## <a name="guest-user-management-and-limitations"></a>Administración de usuario de invitado y limitaciones

Cuentas de invitado son los usuarios de otros directorios que están invitadas a su directorio para tener acceso a documentos de SharePoint, aplicaciones u otros recursos de Azure. Una cuenta de invitado en el directorio tiene su atributo UserType subyacente establecida en "Invitado". Los usuarios normales (específicamente, los miembros de su directorio) tienen el atributo de UserType "Miembro".

Los invitados tienen un conjunto limitado de derechos en el directorio. Estos derechos limitan la capacidad para invitados descubrir información sobre otros usuarios en el directorio. Sin embargo, los usuarios invitados aún pueden interactuar con los usuarios y grupos asociados con los recursos que están trabajando. Los usuarios invitados pueden:

- Ver otros usuarios y grupos asociados con una suscripción de Azure al que está asignados
- Ver los miembros de grupos a los que pertenecen
- Buscar otros usuarios en el directorio, si sabe la dirección de correo electrónico completa del usuario
- Ver solo un conjunto de atributos de los usuarios que buscan--limitados para mostrar el nombre, dirección de correo electrónico, nombre principal de usuario (UPN) y fotos en miniatura limitado
- Obtener una lista de dominios comprobados en el directorio
- Consentimiento para aplicaciones, que se les concede el mismo acceso que los miembros tienen en su directorio

## <a name="set-guest-user-access-policies"></a>Establecer directivas de acceso de usuario de invitado

La ficha **Configurar** de un directorio incluye opciones para controlar el acceso de usuarios invitados. Estas opciones pueden cambiarse solo en Azure portal clásica por un administrador global de directorio. En este momento no hay ningún método PowerShell o API.

Para abrir la ficha **Configurar** en el portal de clásico Azure, seleccione **Active Directory**y, a continuación, seleccione el nombre del directorio.

![Configurar la ficha de Azure Active Directory][1]

Puede editar las opciones para controlar el acceso de usuarios invitados.

![Opciones de control de acceso para los usuarios invitados][2]


## <a name="whats-next"></a>¿Qué es la siguiente

- [Agregar usuarios de otros directorios o las empresas asociadas de Azure Active Directory](active-directory-create-users-external.md)
- [Administración de Azure AD](active-directory-administer.md)
- [Administrar contraseñas en Azure AD](active-directory-manage-passwords.md)
- [Administrar grupos de Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png
