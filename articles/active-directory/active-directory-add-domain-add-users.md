<properties
    pageTitle="Asignar usuarios a un dominio personalizado en Azure Active Directory | Microsoft Azure"
    description="Cómo rellenar un dominio personalizado en Azure Active Directory con cuentas de usuario."
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="assign-users-to-a-custom-domain"></a>Asignar a usuarios a un dominio personalizado

Después de haber agregado el dominio personalizado a Azure Active Directory, debe agregar las cuentas de usuario para este dominio para que pueda comenzar autenticar ellos.

## <a name="users-synced-in-from-a-directory-on-your-corporate-network"></a>Usuarios sincronizados en un directorio en su red corporativa

Si ya ha establecido una conexión entre su local en Active Directory y Azure Active Directory, la sincronización puede rellenar las cuentas. Para obtener más información sobre cómo sincronizar Azure Active Directory con su Active Directory local, consulte [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

## <a name="users-added-and-managed-in-the-cloud"></a>Usuarios agregan y administran en la nube

Para cambiar el dominio de una cuenta de usuario:

1.  Abra el portal clásico Azure con una cuenta que es un administrador global o administrador del usuario.

2.  Abra el directorio.

3.  Seleccione la ficha **usuarios** .

4.  Seleccione el usuario de la lista.

5.  Cambiar el dominio para el usuario y, a continuación, seleccione **Guardar**.

Esto también puede hacerse con [PowerShell de Microsoft](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) o con la [API de gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>Seleccione un dominio personalizado al crear un nuevo usuario

1.  Abra el portal clásico Azure con una cuenta que es un administrador global o administrador del usuario.

2.  Abra el directorio.

3.  Seleccione la ficha **usuarios** .

4.  En la barra de comandos, seleccione **Agregar**.

5.  Cuando agrega el nombre de usuario, seleccione el dominio personalizado de la lista de dominios.

6.  Seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

-   [Uso de nombres de dominio personalizado para simplificar la experiencia de inicio de sesión para los usuarios](active-directory-add-domain.md)

-   [Administrar nombres de dominio personalizado](active-directory-add-manage-domain-names.md)

-   [Obtenga más información sobre los conceptos de administración de dominio en Azure AD](active-directory-add-domain-concepts.md)
