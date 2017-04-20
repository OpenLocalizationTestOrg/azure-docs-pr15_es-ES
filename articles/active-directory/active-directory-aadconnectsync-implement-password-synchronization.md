<properties
    pageTitle="Implementar la sincronización de contraseña con la sincronización de Azure AD Connect | Microsoft Azure"
    description="Proporciona información sobre cómo funciona la sincronización de contraseña y cómo habilitarlo."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="markusvi;andkjell"/>


# <a name="implementing-password-synchronization-with-azure-ad-connect-sync"></a>Implementar la sincronización de contraseña con la sincronización de Azure AD Connect
Este tema le proporciona la información que necesita sincronizar las contraseñas de usuario de un local Active Directory (AD) a una basada en nube Azure Active Directory (AD Azure).

## <a name="what-is-password-synchronization"></a>¿Qué es la sincronización de contraseñas
La probabilidad de que están bloqueados terminen su trabajo debido a una contraseña olvidada relacionado con el número de contraseñas diferentes que debe recordar. Las contraseñas más que debe recordar, mayor será la probabilidad olvida uno. Preguntas y llamadas de restablecimiento de contraseñas y otros problemas relacionados con la contraseña demandan más recursos del departamento de soporte técnico.

Sincronización de contraseñas es una característica para sincronizar las contraseñas de usuario desde Active Directory local una basada en nube Azure Active Directory (AD Azure).
Esta característica le permite iniciar sesión en servicios de Azure Active Directory (por ejemplo, Office 365, Microsoft Intune, CRM Online y servicios de dominio de Active Directory de Azure) con la misma contraseña que usa para iniciar sesión en su Active Directory local.

![¿Qué es Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

Reduciendo el número de contraseñas de que los usuarios necesitan mantener a solo una sincronización de contraseñas le ayuda a:

- Mejorar la productividad de los usuarios
- Reducir los costos del departamento de soporte técnico  

Además, si selecciona para utilizar [**la federación con AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), opcionalmente, puede habilitar la sincronización de contraseñas como una copia de seguridad en caso de que se produce un error en la infraestructura de AD FS.

Sincronización de contraseñas es una extensión a la característica de sincronización de directorios implementada con sincronización de Azure AD Connect. Para usar la sincronización de contraseñas en su entorno, debe:

- Conectarse instalar Azure AD  
- Configurar la sincronización de directorio entre su local AD y Azure Active Directory
- Habilitar la sincronización de contraseñas

Para obtener más detalles, consulte [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

> [AZURE.NOTE] Para obtener más detalles sobre los servicios de dominio de Active Directory que se han configurado para la sincronización de FIPS y la contraseña, consulte [sincronización de la contraseña y FIPS](#password-synchronization-and-fips).

## <a name="how-password-synchronization-works"></a>Cómo funciona la sincronización de contraseña
El servicio de dominio de Active Directory almacena las contraseñas en forma de una representación de valor de hash de la contraseña de usuario real. Un valor de tipo hash es un resultado de una función matemática unidireccional ("*algoritmo hash*"). No hay ningún método para revertir el resultado de una función unidireccional a la versión de texto sin formato de la contraseña. No puede usar un hash de contraseña para iniciar sesión en su red local.

Para sincronizar su contraseña, sincronización de Azure AD Connect extrae el hash de contraseña de Active Directory local. Procesamiento de seguridad adicional se aplica a lo hash de contraseña antes de que se sincroniza con el servicio de autenticación de Azure Active Directory. Las contraseñas se sincronizarán en por usuario y por orden cronológico.

El flujo de datos reales del proceso de sincronización de contraseña es similar a la sincronización de datos de usuario como DisplayName o direcciones de correo electrónico. Sin embargo, las contraseñas se sincronizarán con más frecuencia de la ventana de sincronización de directorio estándar para otros atributos. El proceso de sincronización de la contraseña se ejecute cada 2 minutos. No se puede modificar la frecuencia de este proceso. Cuando sincroniza una contraseña, se sobrescribe la contraseña existente de la nube.

La primera vez, habilitar la característica de sincronización de contraseña, se realiza una sincronización inicial de las contraseñas de todos los usuarios en el ámbito. No puede definir explícitamente un subconjunto de las contraseñas de usuario que desea sincronizar.

Cuando cambie una contraseña local, la contraseña actualizada se sincroniza, con mayor frecuencia en cuestión de minutos.
La característica de sincronización de contraseña reintentos automáticamente los intentos de error de sincronización. Si se produce un error al intentar sincronizar una contraseña, se registra un error en el Visor de eventos.

La sincronización de la contraseña tiene ningún efecto en el usuario que ha iniciado sesión.
Un cambio de contraseña sincronizados que se produce cuando ha iniciado sesión en un servicio de nube no afecta inmediatamente la sesión actual de servicio de nube. Sin embargo, cuando el servicio de nube requiere que autenticarse de nuevo, debe proporcionar la nueva contraseña.

> [AZURE.NOTE] Solo se admite la sincronización de contraseña para el usuario de tipo de objeto de Active Directory. No se admite para el tipo de objeto iNetOrgPerson.

### <a name="how-password-synchronization-works-with-azure-ad-domain-services"></a>Cómo funciona la sincronización de contraseña con los servicios de dominio de Active Directory de Azure
También puede usar la característica de sincronización de contraseña para sincronizar sus contraseñas local a los [Servicios de dominio de Active Directory de Azure](../active-directory-domain-services/active-directory-ds-overview.md). Este escenario permite a los servicios de dominio de Azure AD autenticar los usuarios en la nube con todos los métodos disponibles en sus instalaciones AD. La experiencia de este escenario es similar al uso de la herramienta de migración de Active Directory (ADMT) en un entorno local.

### <a name="security-considerations"></a>Consideraciones de seguridad
Al sincronizar contraseñas, la versión de texto sin formato de la contraseña no se expone a la característica de sincronización de contraseña a Azure AD o cualquiera de los servicios asociados.

Además, no es necesario en Active Directory local para almacenar la contraseña en un formato de cifrado reversible. Un resumen de hash de contraseña de Active Directory se usa para la transmisión entre las instalaciones AD y Azure Active Directory. El resumen del hash de contraseña no se pueden usar para tener acceso a los recursos en su entorno local.

### <a name="password-policy-considerations"></a>Consideraciones de la directiva de contraseña
Existen dos tipos de directivas de contraseña que se ven afectadas por habilitar la sincronización de contraseña:

1. Directiva de complejidad de contraseña
2. Directiva de expiración de contraseña

**Directiva de complejidad de contraseña**  
Al habilitar la sincronización de contraseñas, las directivas de complejidad de contraseña en su Active Directory local reemplazan las directivas de complejidad en la nube para usuarios sincronizados. Puede utilizar todas las contraseñas válidas de Active Directory local para acceder a los servicios de Azure AD.

> [AZURE.NOTE] Las contraseñas de los usuarios que se crean directamente en la nube están aún están sujetos a las directivas de contraseña según se define en la nube.

**Directiva de expiración de contraseña**  
Si un usuario está en el ámbito de sincronización de contraseñas, la contraseña de la cuenta de nube se establece en "*Nunca Expire*".
Puede continuar iniciar sesión en los servicios de nube utilizando una contraseña sincronizada que ha caducado en su entorno local. La contraseña de la nube se actualiza la próxima vez que cambie la contraseña en el entorno local.

### <a name="overwriting-synchronized-passwords"></a>Sobrescribir sincronizados contraseñas
Un administrador puede restablecer manualmente la contraseña con Windows PowerShell.

En este caso, la nueva contraseña reemplaza la contraseña sincronizada y todas las directivas de contraseña definidas en la nube se aplican a la nueva contraseña.

Si cambia la contraseña local de nuevo, la nueva contraseña se sincroniza con la nube y reemplaza la contraseña manualmente actualizada.

## <a name="enabling-password-synchronization"></a>Habilitar la sincronización de contraseña
Sincronización de contraseñas se habilita automáticamente cuando se instala Azure AD Connect utilizando la **Configuración rápida**. Para obtener más detalles, consulte [Introducción a Azure AD Connect usando la configuración de express](./connect/active-directory-aadconnect-get-started-express.md).

Si usa una configuración personalizada al instalar Azure AD Connect, habilitar la sincronización de contraseña en la página de inicio de sesión de usuario. Para obtener más detalles, consulte [instalación personalizada de Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).

![Habilitar la sincronización de contraseña](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### <a name="password-synchronization-and-fips"></a>Sincronización de contraseñas y FIPS
Si su servidor se ha bloqueado según información procesamiento estándar Federal (FIPS), MD5 se ha deshabilitado.

**Para habilitar MD5 para la sincronización de contraseñas, siga estos pasos:**

1. Vaya a **Sync\Bin %programfiles%\Azure AD**.
2. Abra **miiserver.exe.config**.
3. Vaya al nodo **configuración/tiempo de ejecución** (al final del archivo).
4. Agregar el nodo siguiente:`<enforceFIPSPolicy enabled="false"/>`
5. Guarde los cambios.

Para referencia, este recorte es cómo debe aspecto:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Para obtener información sobre la seguridad y FIPS vea [cumplimiento de sincronización de AAD la contraseña, el cifrado y FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshooting-password-synchronization"></a>Solución de problemas de sincronización de contraseñas
Si no se sincronizan las contraseñas según lo esperado, lo puede ser un subconjunto de los usuarios o para todos los usuarios.

- Si tiene problemas con los objetos individuales, consulte [solucionar problemas de un objeto que no se sincroniza las contraseñas](#troubleshoot-one-object-that-is-not-synchronizing-passwords).
- Si tiene un problema donde no se sincronizan contraseñas, vea [solucionar problemas donde no se sincronizan contraseñas](#troubleshoot-issues-where-no-passwords-are-synchronized).

### <a name="troubleshoot-one-object-that-is-not-synchronizing-passwords"></a>Solucionar problemas de un objeto que no se sincroniza las contraseñas
Para solucionar problemas de sincronización de contraseña fácilmente puede revisar el estado de un objeto.

Iniciar en **equipos y usuarios de Active Directory**. Busque el usuario y compruebe que el **que usuario debe cambiar la contraseña en el inicio de sesión siguiente** está seleccionada.
![Contraseñas productivas de Active Directory](./media/active-directory-aadconnectsync-implement-password-synchronization/adprodpassword.png)  
Si se selecciona, a continuación, pídale al usuario para iniciar sesión y cambiar la contraseña. Las contraseñas temporales no se sincronizan con Azure AD.

Si es correcto en Active Directory, el siguiente paso es seguir al usuario en el motor de sincronización. Siguiendo el usuario de local de Active Directory en Azure AD, puede ver si hay un error descriptivo en el objeto.

1. Inicie el **[Administrador de servicios de sincronización](active-directory-aadconnectsync-service-manager-ui.md)**.
2. Haga clic en **conectores**.
3. Seleccione el **Conector de Active Directory** , el usuario se encuentra en.
4. Seleccione **el espacio del conector de búsqueda**.
5. Busque el usuario que está buscando.
6. Seleccione la pestaña **linaje** y asegúrese de que al menos una regla de sincronización muestra la **Sincronización de la contraseña** como **True**. En la configuración predeterminada, el nombre de la regla de sincronización es **en de AD - usuario AccountEnabled**.  
    ![Información de linaje de un usuario](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)  
7. A continuación, [siga el usuario](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) metaverse hasta el espacio de Azure AD conector. El objeto de espacio de conector debe tener una regla de salida con la **Sincronización de la contraseña** establecida en **True**. En la configuración predeterminada, el nombre de la regla de sincronización es **Salí a AAD - unirse al usuario**.  
    ![Propiedades del espacio de conector de un usuario](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)  
8. Para ver los detalles de la sincronización de contraseña del objeto de la semana pasada, haga clic en **log...**.  
    ![Detalles del objeto de registro](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)  

La columna Estado puede tener los valores siguientes:

Estado | Descripción
---- | -----
Éxito | Contraseña se ha sincronizado correctamente.
FilteredByTarget | Contraseña está configurada para **el usuario debe cambiar la contraseña en el siguiente inicio de sesión**. Contraseña no se ha sincronizado.
NoTargetConnection | No hay ningún objeto en el metaverso o en el espacio del conector de Azure AD.
SourceConnectorNotPresent | No hay ningún objeto que se encuentra en el espacio del conector de Active Directory local.
TargetNotExportedToDirectory | Aún no se ha exportado el objeto en el espacio de conector de Azure AD.
MigratedCheckDetailsForMoreInfo | Entrada del registro se creó antes de generar 1.0.9125.0 y se muestra en su estado heredado.

### <a name="troubleshoot-issues-where-no-passwords-are-synchronized"></a>Solucionar problemas de donde no se sincronizan contraseñas
Empiece ejecutando el script en la sección [conocer el estado de la configuración de sincronización de contraseña](#get-the-status-of-password-sync-settings). Le proporciona información general sobre la configuración de sincronización de contraseña.  
![Resultados de la secuencia de comandos de PowerShell de configuración de sincronización de contraseña](./media/active-directory-aadconnectsync-implement-password-synchronization/psverifyconfig.png)  
Si la característica no está habilitada en Azure AD o si no está habilitado el estado de canal de sincronización, a continuación, ejecute al Asistente para instalación de conectarse. Seleccione **las opciones de sincronización de personalizar** y anular la selección de sincronización de la contraseña. Este cambio deshabilita temporalmente la característica. A continuación, vuelva a ejecutar al asistente y volver a habilitar la sincronización de la contraseña. Ejecute la secuencia de comandos para comprobar que la configuración es correcta.

Si muestra la secuencia de comandos que no hay ningún latido, a continuación, ejecute el script en [desencadenar una sincronización completa de todas las contraseñas](#trigger-a-full-sync-of-all-passwords). Esta secuencia de comandos también puede usarse para otros escenarios donde la configuración es correcta, pero no se sincronizan las contraseñas.

#### <a name="get-the-status-of-password-sync-settings"></a>Obtener el estado de la configuración de sincronización de contraseña

```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Desencadenar una sincronización completa de todas las contraseñas
Pueden desencadenar una sincronización completa de todas las contraseñas mediante el siguiente script:

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Pasos siguientes

* [Sincronización de conectar Azure AD: Personalizar las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
