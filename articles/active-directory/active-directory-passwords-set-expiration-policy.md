<properties
    pageTitle="Establecer directivas de expiración de contraseñas en Azure Active Directory | Microsoft Azure"
    description="Obtenga información sobre cómo comprobar las directivas de expiración y cambiar la caducidad de la contraseña de usuario individualmente o en bloque de contraseñas de Azure Active directory"
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
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand"/>


# <a name="set-password-expiration-policies-in-azure-active-directory"></a>Establecer directivas de expiración de contraseña en Azure Active Directory

> [AZURE.IMPORTANT] **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [He aquí cómo puede cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md).

Como administrador global de un servicio de nube de Microsoft, puede usar la Microsoft Azure Active Directory módulo para Windows PowerShell para configurar las contraseñas de usuario no a punto de expirar. También puede usar cmdlets de Windows PowerShell para quitar la nunca-caduca configuración o para ver qué usuario contraseñas están configuradas no expire. En este artículo se proporciona ayuda para los servicios de nube, como Intune de Microsoft y Office 365, que se basan en Microsoft Azure Active Directory para los servicios de identidad y directorio.

  > [AZURE.NOTE] Solo las contraseñas de cuentas de usuario que no están sincronizadas mediante sincronización de directorios se pueden configurar para que no expire. Para obtener más información acerca de la sincronización de directorios, consulte la lista de temas de [mapa de ruta de sincronización de directorios](https://msdn.microsoft.com/library/azure/hh967642.aspx).

Para usar cmdlets de Windows PowerShell, primero debe instalar ellos.

## <a name="what-do-you-want-to-do"></a>¿Qué quieres hacer?

- [¿Cómo puedo comprobar la directiva de expiración de contraseña](#how-to-check-expiration-policy-for-a-password)

- [Establecer una contraseña para que expire](#set-a-password-to-expire)

- [Establecer una contraseña para que no caducará](#set-a-password-to-never-expire)

## <a name="how-to-check-expiration-policy-for-a-password"></a>¿Cómo puedo comprobar la directiva de expiración de contraseña

1.  Conectarse a Windows PowerShell con sus credenciales de administrador de empresa.

2.  Siga uno de estos procedimientos:

    - Para ver si la contraseña de un usuario único está establecida para que nunca expire, ejecute el cmdlet siguiente con el nombre principal de usuario (UPN) (por ejemplo, aprilr@contoso.onmicrosoft.com) o el identificador de usuario del usuario que desea comprobar:`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`

    - Para ver la opción "La contraseña nunca expira" para todos los usuarios, ejecute el siguiente cmdlet:`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## <a name="set-a-password-to-expire"></a>Establecer una contraseña para que expire

1.  Conectarse a Windows PowerShell con sus credenciales de administrador de empresa.

2.  Siga uno de estos procedimientos:

    - Para definir la contraseña de un usuario para que la contraseña caducará, ejecute el cmdlet siguiente usando el nombre principal de usuario (UPN) o el identificador de usuario del usuario:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`

    - Para definir las contraseñas de todos los usuarios de la organización para que va a expirar, use el siguiente cmdlet:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

## <a name="set-a-password-to-never-expire"></a>Establecer una contraseña para que nunca expire

1. Conectarse a Windows PowerShell con sus credenciales de administrador de empresa.

2.  Siga uno de estos procedimientos:

    - Para establecer la contraseña de un usuario para que nunca expire, ejecute el cmdlet siguiente con el nombre principal de usuario (UPN) o el identificador de usuario del usuario:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`

    - Para configurar las contraseñas de todos los usuarios de una organización para que nunca expire, ejecute el siguiente cmdlet:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Pasos siguientes

* **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [He aquí cómo puede cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md).
