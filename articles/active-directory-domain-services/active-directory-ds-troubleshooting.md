<properties
    pageTitle="Servicios de dominio de Azure Active Directory: Guía de solución de problemas | Microsoft Azure"
    description="Guía de solución de problemas para los servicios de dominio de Active Directory de Azure"
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
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Servicios de dominio de Azure AD - Guía de solución de problemas
Este artículo proporciona sugerencias para solucionar problemas que pueden surgir al configurar o administrar servicios de dominio de Azure Active Directory (AD).


## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>No se puede habilitar servicios de dominio de Active Directory de Azure para el directorio de Azure AD
Esta sección le ayuda a solucionar errores al intentar activar servicios de dominio de Active Directory de Azure para el directorio y se produce un error u obtiene alternar volver a 'Desactivado'.

Seleccione los pasos para solucionar problemas que se corresponden con el mensaje de error que se encuentra.

|**Mensaje de error**|**Resolución**|
|---|:---|
|*El nombre contoso100.com ya está en uso en esta red. Especifique un nombre que no está en uso.*|[Conflicto de nombre de dominio en la red virtual](active-directory-ds-troubleshooting.md#domain-name-conflict)
|*No se pueden habilitar servicios de dominio en este inquilino de Azure AD. El servicio no tiene los permisos adecuados para la aplicación denominada 'Sync de servicios de dominio de Active Directory de Azure'. Eliminar la aplicación denominada 'Sync de servicios de dominio de Active Directory de Azure' e intente activar servicios de dominio de su inquilino de Azure AD.*|[Servicios de dominio no tiene los permisos adecuados para la aplicación de sincronización de servicios de dominio de Active Directory de Azure](active-directory-ds-troubleshooting.md#inadequate-permissions)|
|*No se pueden habilitar servicios de dominio en este inquilino de Azure AD. La aplicación de servicios de dominio de su inquilino de Azure AD no tiene los permisos necesarios para habilitar los servicios de dominio. Eliminar la aplicación con la d87dcbc6-a371-462e-88e3-28ad15ec4e64 identificador de aplicación y, a continuación, intente habilitar servicios de dominio de su inquilino de Azure AD.*|[La aplicación de servicios de dominio no está configurada correctamente en el inquilino](active-directory-ds-troubleshooting.md#invalid-configuration)|
|*No se pueden habilitar servicios de dominio en este inquilino de Azure AD. La aplicación de Microsoft Azure AD está deshabilitada en su inquilino de Azure AD. Habilitar la aplicación con la 00000002-0000-0000-c000-000000000000 identificador de aplicación y, a continuación, intente habilitar servicios de dominio de su inquilino de Azure AD.*|[La aplicación de Microsoft Graph está deshabilitada en su inquilino de Azure AD](active-directory-ds-troubleshooting.md#microsoft-graph-disabled)|


### <a name="domain-name-conflict"></a>Conflicto de nombre de dominio
**Mensaje de error:**

*El nombre contoso100.com ya está en uso en esta red. Especifique un nombre que no está en uso.*

**Corrección:**

Asegúrese de que no tiene un dominio existente con el mismo nombre de dominio disponible en una red virtual. Por ejemplo, suponga que tiene un dominio llamado 'contoso.com' ya está disponible en la red virtual seleccionada. Más adelante, pruebe a habilitar un dominio de servicios de dominio de Active Directory de Azure administrado con el mismo nombre de dominio (es decir, ' contoso.com') en una red virtual. Producir un error al intentar activar servicios de dominio de Active Directory de Azure.

Este error es debido a los conflictos de nombres para el nombre de dominio en una red virtual. En esta situación, debe usar un nombre diferente para configurar el dominio de servicios de dominio de Active Directory de Azure administrado. Como alternativa, puede anular aprovisionar el dominio existente y luego proceda para habilitar los servicios de dominio de Active Directory de Azure.


### <a name="inadequate-permissions"></a>Permisos suficientes
**Mensaje de error:**

*No se pueden habilitar servicios de dominio en este inquilino de Azure AD. El servicio no tiene los permisos adecuados para la aplicación denominada 'Sync de servicios de dominio de Active Directory de Azure'. Eliminar la aplicación denominada 'Sync de servicios de dominio de Active Directory de Azure' e intente activar servicios de dominio de su inquilino de Azure AD.*

**Corrección:**

Compruebe si hay una aplicación con el nombre 'Sync de servicios de dominio de Active Directory de Azure' en su directorio de Azure AD. Si existe esta aplicación, elimínelo y, a continuación, volver a habilitar servicios de dominio de Active Directory de Azure.

Siga estos pasos para comprobar la presencia de la aplicación y eliminar, si existe la aplicación:

  1. Desplácese hasta el **portal clásica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. Seleccione el nodo de **Active Directory** en el panel izquierdo.
  3. Seleccione al inquilino de Azure AD (directorio) para el que desea habilitar servicios de dominio de Active Directory de Azure.
  4. Vaya a la pestaña de **aplicaciones** .
  5. Seleccione la opción de **Mi empresa posee las aplicaciones** en la lista desplegable.
  6. Compruebe si una aplicación de **sincronización de servicios de dominio de Active Directory de Azure**. Si la aplicación existe, continúe para eliminarla.
  7. Una vez que haya eliminado la aplicación, intente volver a habilitar servicios de dominio de Active Directory de Azure una vez.


### <a name="invalid-configuration"></a>Configuración no válida
**Mensaje de error:**

*No se pueden habilitar servicios de dominio en este inquilino de Azure AD. La aplicación de servicios de dominio de su inquilino de Azure AD no tiene los permisos necesarios para habilitar los servicios de dominio. Eliminar la aplicación con la d87dcbc6-a371-462e-88e3-28ad15ec4e64 identificador de aplicación y, a continuación, intente habilitar servicios de dominio de su inquilino de Azure AD.*

**Corrección:**

Compruebe si tiene una aplicación con el nombre 'AzureActiveDirectoryDomainControllerServices' (con un identificador de aplicación de d87dcbc6-a371-462e-88e3-28ad15ec4e64) en el directorio de Azure AD. Si existe esta aplicación, debe eliminar y, a continuación, volver a habilitar servicios de dominio de Active Directory de Azure.

Use el siguiente script de PowerShell para encontrar la aplicación y elimínelo.

> [AZURE.NOTE] Esta secuencia de comandos usa cmdlets de **PowerShell de Azure AD versión 2** . Para obtener una lista completa de todos los cmdlets disponibles y para descargar el módulo, lea la [documentación de referencia de AzureAD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx).

```
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph deshabilitado
**Mensaje de error:**

No se pueden habilitar servicios de dominio en este inquilino de Azure AD. La aplicación de Microsoft Azure AD está deshabilitada en su inquilino de Azure AD. Habilitar la aplicación con la 00000002-0000-0000-c000-000000000000 identificador de aplicación y, a continuación, intente habilitar servicios de dominio de su inquilino de Azure AD.

**Corrección:**

Compruebe si deshabilitó una aplicación con el identificador 00000002 0000 0000 c000 000000000000. Esta aplicación es la aplicación de Microsoft Azure AD y proporciona acceso a las API de gráfico a su inquilino de Azure AD. Servicios de dominio de Active Directory Azure necesita esta aplicación estén habilitadas para sincronizar su inquilino de Azure AD a su dominio administrado.

Para resolver este error, habilitar esta aplicación y, a continuación, intente habilitar servicios de dominio de su inquilino de Azure AD.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Los usuarios son no se puede iniciar sesión en el dominio administrado de servicios de dominio de Active Directory de Azure
Si no puedes iniciar sesión en el dominio administrado recién creado uno o más usuarios de su inquilino de Azure AD, realice los siguientes pasos de solución de problemas:

- **Inicio de sesión con el formato UPN:** Intenta iniciar sesión con el formato UPN (por ejemplo, 'joeuser@contoso.com') en lugar del formato de SAMAccountName ('CONTOSO\joeuser'). SAMAccountName se puede generar automáticamente para los usuarios cuyo prefijo UPN es demasiado larga o es igual a otro usuario en el dominio administrado. El formato UPN se garantiza que es único dentro de un inquilino de Azure AD.

> [AZURE.NOTE] Se recomienda usar el formato UPN para iniciar sesión en el dominio de servicios de dominio de Active Directory de Azure administrado.

- Asegurarse de que tiene [habilitada la sincronización de contraseñas](active-directory-ds-getting-started-password-sync.md) según los pasos indicados en la Guía de introducción.

- **Cuentas externas:** Asegúrese de que la cuenta de usuario afectado no es una cuenta externa en el inquilino de Azure AD. Ejemplos de cuentas externas, cuentas de Microsoft (por ejemplo, 'joe@live.com') o cuentas de usuario de externos directorio de Azure AD. Puesto que los servicios de dominio de Active Directory de Azure no tiene las credenciales de estas cuentas de usuario, estos usuarios no pueden iniciar sesión en el dominio administrado.

- **Sincronizado cuentas:** Si las cuentas de usuario afectado se sincronizan desde un directorio local, compruebe lo siguiente:
    - Ha implementado o actualizado a la [versión más reciente recomendados de Azure AD Connect](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect).

    - Ha configurado Azure AD Connect para [realizar una sincronización completa](active-directory-ds-getting-started-password-sync.md).

    - Dependiendo del tamaño de su directorio, puede tardar un rato para cuentas de usuario y credenciales hash esté disponible en servicios de dominio de Active Directory de Azure. Asegúrese de que se espera tiempo suficiente antes de reintentar la autenticación (dependiendo del tamaño de su directorio - unas horas en un día o dos para grandes directorios).

    - Si el problema persiste después de comprobar los pasos anteriores, intente reiniciar el servicio de Microsoft Azure AD Sync. Desde su equipo de sincronización, inicie un símbolo del sistema y ejecute los comandos siguientes:
      1. net stop 'Microsoft Azure AD Sync'
      2. inicio neto 'Microsoft Azure AD Sync'

- **Cuentas sólo nube**: si la cuenta de usuario afectados es una cuenta de usuario solo nube, asegúrese de que el usuario ha cambiado su contraseña después de habilitar servicios de dominio de Active Directory de Azure. Este paso hace que la credencial hash necesarios para que los servicios de dominio de Active Directory de Azure a generarse.


## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Los usuarios que se quita de su inquilino de Azure AD no se quitan de su dominio administrado
Azure AD protege contra eliminación accidental de objetos de usuario. Al eliminar una cuenta de usuario de su inquilino de Azure AD, se mueve el objeto de usuario correspondiente a la Papelera de reciclaje. Cuando esta operación de eliminación se sincroniza con su dominio administrado, hace que la cuenta de usuario correspondiente marcarse deshabilitado. Esta característica le ayuda a recuperar o restaurar la cuenta de usuario más adelante.

Para quitar la cuenta de usuario completo de su dominio administrado, elimine el usuario permanentemente de su inquilino de Azure AD. Usar el cmdlet de PowerShell quitar MsolUser con la '-RemoveFromRecycleBin' opción, como se describe en este [artículo de MSDN](https://msdn.microsoft.com/library/azure/dn194132.aspx).


## <a name="contact-us"></a>Póngase en contacto con nosotros
Póngase en contacto con el equipo de producto de Azure Active Directory Domain Services a [compartir comentarios o asistencia] (activo-directorio-ds-contacto-us.md).
