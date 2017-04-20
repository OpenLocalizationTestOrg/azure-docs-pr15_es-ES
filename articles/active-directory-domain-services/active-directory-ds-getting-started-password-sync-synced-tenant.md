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
En tareas anteriores, había habilitado Servicios de dominio de Active Directory de Azure para su inquilino de Azure AD. La siguiente tarea es habilitar la sincronización de contraseñas en servicios de dominio de Active Directory de Azure. Una vez configurada la sincronización de credenciales, los usuarios pueden iniciar sesión en el dominio administrado usando sus credenciales corporativas.

Los pasos son diferentes en función de si su organización tiene un solo nube Azure AD de inquilinos o está configurado para sincronizarse con el directorio local con Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Solo nube Azure AD de inquilinos](active-directory-ds-getting-started-password-sync.md)
- [Sincronizado el inquilino de Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>Tarea 5: Habilitar la sincronización de contraseña a los servicios de dominio de AAD para un Azure sincronizada inquilino AD
A sincronizado Azure inquilino AD está configurado para sincronizarse con el directorio local de su organización con Azure AD Connect. Azure AD Connect no sincroniza NTLM y Kerberos credenciales hash de Azure AD de forma predeterminada. Para usar los servicios de dominio de Active Directory de Azure, debe configurar Azure AD Connect para sincronizar hash credencial necesarios para la autenticación Kerberos y NTLM. Los siguientes pasos habilitar la sincronización de los valores de hash requiere credenciales en el inquilino de Azure AD.


### <a name="install-or-update-azure-ad-connect"></a>Instalar o actualizar Azure AD Connect
Instalar la versión más reciente recomendada de Azure AD Connect en equipos unidos a un dominio. Si tiene una instancia existente del programa de instalación de Azure AD Connect, deberá actualizar para usar la última versión de Azure AD Connect. Para evitar problemas y errores conocidos que pueden ya se han corregido, asegúrese de que utiliza siempre la versión más reciente de Azure AD Connect.

**[Descargar Azure AD conectar](http://www.microsoft.com/download/details.aspx?id=47594)**

Se recomienda versión: **1.1.281.0** - publicado en 7 de septiembre de 2016.

  > [AZURE.WARNING] DEBE instalar la versión más reciente recomendada de Azure AD Connect para habilitar las credenciales de contraseña heredados (obligatorio para la autenticación Kerberos y NTLM) para sincronizar en el inquilino de Azure AD. Esta función no está disponible en versiones anteriores de Azure AD Connect o con la herramienta de sincronización de directorios heredada.

Instrucciones de instalación para Azure AD Connect están disponibles en el siguiente artículo: [Introducción a Azure AD Connect](../active-directory/active-directory-aadconnect.md)


### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Habilitar la sincronización de hash de credenciales NTLM y Kerberos a Azure AD
Ejecute el siguiente script de PowerShell en cada bosque de AD para forzar la sincronización de contraseña completo y habilite hash de credenciales de todos los usuarios locales a sincronizar a su inquilino de Azure AD. Esta secuencia de comandos permite a los valores de hash credencial necesarios para que la autenticación de NTLM o Kerberos para sincronizarse con el inquilino de Azure AD.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

Dependiendo del tamaño de su directorio (número de usuarios, grupos etc.), la sincronización de hash credencial a Azure AD tarda. Las contraseñas se podrán utilizables en el dominio de servicios de dominio de Active Directory de Azure administrado poco después de que se hayan sincronizado los hash credencial a Azure AD.


<br>

## <a name="related-content"></a>Contenido relacionado

- [Habilitar la sincronización de contraseña a los servicios de dominio de AAD para un solo nube Azure directorio de AD](active-directory-ds-getting-started-password-sync.md)

- [Administrar un dominio administrado de servicios de dominio de Active Directory de Azure](active-directory-ds-admin-guide-administer-domain.md)

- [Unirse a una máquina virtual de Windows a un dominio administrado de servicios de dominio de Active Directory de Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Unirse a una máquina virtual rojos sombrero Enterprise Linux a un dominio administrado de servicios de dominio de Active Directory de Azure](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
