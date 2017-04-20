<properties
    pageTitle="Azure cmdlets de vista previa de Active Directory PowerShell para administrar el grupo en Azure AD | Microsoft Azure"
    description="Esta página proporciona ejemplos de PowerShell para ayudarle a administrar los grupos de Azure Active Directory"
    keywords="PowerShell de Azure AD, Azure Active Directory, administración de grupos, grupo"
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
    ms.date="09/29/2016"
    ms.author="curtand"/>

# <a name="azure-active-directory-preview-cmdlets-for-group-management"></a>Azure cmdlets de vista previa de Active Directory para la administración de grupo

> [AZURE.SELECTOR]
- [Portal de Azure](active-directory-groups-create-azure-portal.md)
- [Portal de clásico de Azure](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

El siguiente documento proporciona ejemplos de cómo usar PowerShell para administrar los grupos de Azure Active Directory (AD Azure).  También se proporciona información sobre cómo configurar el módulo de vista previa de Azure AD PowerShell. En primer lugar, debe [descargar el módulo de PowerShell de Azure AD](http://go.microsoft.com/fwlink/p/?LinkId=828627).

## <a name="installing-the-azure-ad-powershell-module"></a>Instalar el módulo de PowerShell de Azure AD

Para instalar el módulo de AzureAD PowerShell vista previa, utilice los comandos siguientes:

    PS C:\Windows\system32> install-module azureadpreview

Para comprobar que se ha instalado el módulo de vista previa, utilice el siguiente comando:

    PS C:\Windows\system32> get-module azureadpreview

    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     1.1.146.0  azureadpreview                      {Add-AzureADAdministrati...}

Ahora puede empezar a usar los cmdlets en el módulo. Para obtener una descripción completa de los cmdlets en el módulo de vista previa de AzureAD, consulte la [documentación de referencia en línea](https://msdn.microsoft.com/library/azure/mt757216.aspx).

## <a name="connecting-to-the-directory"></a>Conectar con el directorio
Antes de empezar a administrar grupos de uso de cmdlets de PowerShell de Azure AD preview, debe conectar la sesión de PowerShell en el directorio que desee administrar. Para hacerlo, utilice el siguiente comando:

    PS C:\Windows\system32> Connect-AzureAD -Force

El cmdlet le solicitará las credenciales que desea usar para tener acceso a su directorio. En este ejemplo, estamos usando karen@drumkit.onmicrosoft.com tener acceso al directorio de demostración. El cmdlet devolverá una confirmación para mostrar que la sesión se ha conectado correctamente al directorio:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Ahora puede empezar a usar los cmdlets de vista previa AzureAD para administrar grupos en el directorio.

## <a name="retrieving-groups"></a>Recuperar grupos
Puede usar el cmdlet Get-AzureADGroups para recuperar grupos existentes del directorio. Para recuperar todos los grupos en el directorio, use el cmdlet sin parámetros:

    PS C:\Windows\system32> get-azureadgroup

El cmdlet devolverá todos los grupos en el directorio conectado.

Puede usar el parámetro - Id. de objeto para recuperar un grupo específico para el que especificará Id. de objeto del grupo:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

El cmdlet devolverá el grupo cuyo Id. de objeto coincide con el valor del parámetro que haya escrito:

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Puede buscar un grupo específico mediante el filtro parámetro-. Este parámetro toma una cláusula de filtro ODATA y devuelve todos los grupos que coincidan con el filtro, como en el ejemplo siguiente:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Tenga en cuenta que los cmdlets de AzureAD PowerShell implementar el estándar de consulta de OData, puede encontrar más información [aquí](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="creating-groups"></a>Creación de grupos
Para crear un nuevo grupo en el directorio, use el cmdlet AzureADGroup de nuevo. Este cmdlet crea un nuevo grupo de seguridad denominado "Marketing":

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="updating-groups"></a>Actualizar grupos
Para actualizar un grupo existente, use el cmdlet Set-AzureADGroup. En este ejemplo, estamos cambiando la propiedad DisplayName del grupo "Administradores de Intune". En primer lugar, creamos está buscando el grupo mediante el cmdlet Get-AzureADGroup y filtrar usando el atributo DisplayName:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

A continuación, estamos cambiando la propiedad descripción para el nuevo valor "Administradores del dispositivo Intune":

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Ahora si se encuentre el grupo nuevo, podemos ver que la propiedad descripción se actualiza para reflejar el nuevo valor:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="deleting-groups"></a>Eliminación de grupos
Para eliminar grupos de su directorio, use el cmdlet quitar AzureADGroup como sigue:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="managing-members-of-groups"></a>Administrar miembros de grupos
Si necesita agregar a nuevos miembros a un grupo, use el cmdlet Add-AzureADGroupMember. Este comando agrega a un miembro al grupo de administradores de Intune que hemos usado en el ejemplo anterior:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

El parámetro - Id. de objeto es el Id. de objeto del grupo al que desea agregar a un miembro y RefObjectId - es el Id. de objeto del usuario al que desea agregar como miembro al grupo.

Para obtener a los miembros de un grupo existentes, use el cmdlet Get-AzureADGroupMember, como en este ejemplo:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                        8120cc36-64b4-4080-a9e8-23aa98e8b34f User

Para quitar al miembro que se ha agregado al grupo, use el cmdlet quitar AzureADGroupMember, como se muestra aquí:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Para comprobar la membership(s) de grupo de un usuario, use el cmdlet de Select AzureADGroupIdsUserIsMemberOf. Este cmdlet toma como parámetros el Id. de objeto del usuario para el que se comprueba la pertenencia a grupos y una lista de grupos que se comprueba la pertenencia a. La lista de grupos debe ser siempre en el formulario de una variable complejo del tipo "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", así que primero debemos creamos una variable con ese tipo:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

A continuación, le proporcionamos los valores de la groupIds a proteger el atributo "GroupIds" de esta variable de complejos:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Ahora, si desea proteger las pertenencias a grupos de un usuario con el Id. de objeto 72cd4bbd-2594-40a2-935c-016f3cfeeeea frente a los grupos en $g, debemos usar:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                               Value
    -------------                                                                                               -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


El valor devuelto es una lista de grupos de los que este usuario es miembro. También puede aplicar este método para comprobar la pertenencia a contactos, grupos o identidades de servicio para una lista de grupos, con Select AzureADGroupIdsContactIsMemberOf, seleccione AzureADGroupIdsGroupIsMemberOf o seleccione AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="managing-owners-of-groups"></a>Administrar propietarios de grupos
Para agregar los propietarios a un grupo, use el cmdlet AzureADGroupOwner agregar:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

El parámetro - Id. de objeto es el Id. de objeto del grupo al que desea agregar un propietario y RefObjectId - es el Id. de objeto del usuario al que desea agregar como propietario del grupo.

Para recuperar los propietarios de un grupo, utilice el Get-AzureADGroupOwner:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

El cmdlet devolverá la lista de propietarios del grupo especificado:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Si desea quitar un propietario de un grupo, utilice quitar AzureADGroupOwner:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar más documentación de Azure Active Directory PowerShell en [Azure Active Directory Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=808260).

* [Administrar el acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)

* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
