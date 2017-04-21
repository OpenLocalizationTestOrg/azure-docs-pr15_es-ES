<properties
   pageTitle="Preguntas más frecuentes de integración de registro de Azure | Microsoft Azure"
   description="Este artículo responde a preguntas sobre la integración de registro de Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="TomSh"/>

# <a name="azure-log-integration-frequently-asked-questions-faq"></a>Preguntas más frecuentes (p+f sobre) la integración de registro de Azure

Este artículo responde a preguntas sobre la integración de registro de Azure, un servicio que le permite integrar registros sin formato de los recursos de Azure en los sistemas de gestión de eventos (SIEM) e información de seguridad local. Esta integración proporciona un panel unificado de todos los activos, local o en la nube para que puedan agregar, relacionar, analizar y alertas para los eventos de seguridad asociados con las aplicaciones.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs-from"></a>¿Cómo se puede ver las cuentas de almacenamiento desde la integración de Azure registro extrae registros de Azure VM desde?

Ejecute el comando **azlog lista de origen**.

## <a name="how-can-i-update-the-proxy-configuration"></a>¿Cómo puedo actualizar la configuración de proxy?

Si la configuración de proxy no permite el acceso de almacenamiento de Azure directamente, abra la **AZLOG. EXE. CONFIG** archivo en **c:\Program Files\Microsoft Azure registro integración**. Actualizar el archivo para incluir la sección **defaultProxy** con la dirección del proxy de su organización. Cuando haya terminado la actualización, detenga e inicie el servicio con los comandos **net stop azlog** y **azlog inicio neto**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>¿Cómo se puede ver la información de la suscripción de eventos de Windows?

Anexar la **subscriptionid** a nombre descriptivo al agregar el origen.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

El evento XML tiene los metadatos como se muestra a continuación, incluido el id de suscripción.

![Evento XML][1]

## <a name="error-messages"></a>Mensajes de error

### <a name="when-running-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Cuando ejecute el comando **azlog createazureid**, ¿por qué recibo el siguiente error?

Error:

  *Error al crear la aplicación de AAD - 72f988bf-86f1-41af-91ab-2d7cd011db37-razón de inquilinos = 'Prohibido' - mensaje = 'Privilegios suficientes para completar la operación'.*

**Azlog createazureid** intenta crear una entidad de seguridad de servicio en todos los inquilinos de Azure AD para las suscripciones en la que el inicio de sesión de Azure tiene acceso a. Si el inicio de sesión de Azure es solo un usuario de invitado en ese inquilino de Azure AD, a continuación, el comando no funciona con 'Privilegios suficientes para completar la operación'. Solicitar el Administrador de inquilinos para agregar su cuenta como un usuario en el inquilino.

### <a name="when-running-command-azlog-authorize-why-do-i-get-the-following-error"></a>Cuando se ejecuta el comando **azlog autorizar**, ¿por qué obtengo el siguiente error?

Error:

  *Advertencia de creación de la asignación de roles - AuthorizationFailed: el cliente janedo@microsoft.com' con objeto id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' no tiene autorización para realizar la acción 'Microsoft.Authorization/roleAssignments/write' sobre el ámbito '/ suscripciones/70 d. 95299-d689-4c 97-b971-0d8ff0000000'.*

**Autorizar Azlog** comando asigna la función del lector con el servicio de Azure AD principal (creada con **Azlog createazureid**) a las suscripciones proporcionadas. Si el inicio de sesión de Azure no es un administrador de compañeros o el propietario de la suscripción, se produce un error con error de autorización del mensaje de error. Control de Azure acceso basado en roles (RBAC) de compañeros administrador o propietario es necesaria para completar esta acción.

## <a name="where-can-i-find-the-definition-of-the-properties-in-audit-log"></a>¿Dónde se puede encontrar la definición de las propiedades del registro de auditoría?

Vea:

- [Operaciones de auditoría con el Administrador de recursos](../resource-group-audit.md)
- [Enumere los eventos de administración en una suscripción de la API de REST de Monitor de Azure](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>¿Dónde se puede encontrar más detalles sobre las alertas del centro de seguridad de Azure?

Consulte [administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>¿Cómo puedo modificar lo que se recopilan con diagnósticos VM?

Consulte [Usar PowerShell para habilitar diagnóstico de Azure en una máquina virtual ejecuta Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) para obtener más información sobre cómo obtener, modificar y establecer los diagnósticos de Azure en Windows *(TORUNDA)* configuración. A continuación se muestra un ejemplo:

### <a name="get-the-wad-config"></a>Obtener la configuración de TORUNDA

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### <a name="modify-the-wad-config"></a>Modificar la configuración de TORUNDA

El ejemplo siguiente es una configuración donde se recopilan sólo IdEvento 4624 e IdEvento 4625 desde el registro de eventos de seguridad. Eventos de Microsoft Antimalware se recopilan desde el registro de eventos del sistema. Vea [consumir eventos] (https://msdn.microsoft.com/library/windows/desktop/dd996910 (v=vs.85) para obtener información detallada sobre el uso de las expresiones XPath.

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### <a name="set-the-wad-configuration"></a>Establecer la configuración de TORUNDA

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Después de realizar cambios, compruebe la cuenta de almacenamiento para asegurarse de que se recopilan los eventos correctos.

Si tiene preguntas sobre la integración de registro de Azure, envíe un correo electrónico a [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
