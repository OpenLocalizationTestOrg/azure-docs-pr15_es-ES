<properties
    pageTitle="Crear un informe de historial de cambios de access | Microsoft Azure"
    description="Generar un informe que muestra todos los cambios en el acceso a las suscripciones Azure con Control de acceso basado en roles durante los últimos 90 días."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="create-an-access-change-history-report"></a>Crear un informe de historial de cambios de access

Cada vez que alguien otorga o revoca el acceso dentro de sus suscripciones registrarán los cambios en eventos de Azure. Puede crear informes de historial de cambios de access para ver todos los cambios de los últimos 90 días.

## <a name="create-a-report-with-azure-powershell"></a>Crear un informe con PowerShell de Azure
Para crear un informe de historial de cambios de access en PowerShell, use la `Get-AzureRMAuthorizationChangeLog` comando. Más detalles sobre este cmdlet están disponibles en la [Galería de PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/1.0.6/Content/ResourceManagerStartup.ps1).

Cuando llame a este comando, puede especificar qué propiedad de las asignaciones que quiere que se muestre, incluidas las siguientes:

| (Propiedad) | Descripción |
| -------- | ----------- |
| **Acción** | Si se le ha concedido o revocar acceso |
| **Autor de la llamada** | El propietario de cambio de access |
| **Fecha** | La fecha y hora que se ha cambiado a access |
| **DirectoryName** | El directorio de Azure Active Directory |
| **PrincipalName** | El nombre de usuario, grupo o aplicación |
| **PrincipalType** | Si fue la asignación para un usuario, grupo o aplicación |
| **Identificador de función** | GUID de la función que se ha concedido o ha caducado |
| **Nombre de función** | La función que se ha concedido o ha caducado |
| **ScopeName** | El nombre de la suscripción, el grupo de recursos o el recurso |
| **ScopeType** | Si la asignación fue en la suscripción, el grupo de recursos o el ámbito de recursos |
| **SubscriptionId** | GUID de la suscripción de Azure |
| **SubscriptionName** | El nombre de la suscripción de Azure |

Este comando de ejemplo enumera todos los cambios de access en la suscripción de los siete últimos días:

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - captura de pantalla](./media/role-based-access-control-configure/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>Crear un informe con CLI de Azure
Para crear un informe de historial de cambios de access en la interfaz de línea de comandos de Azure (CLI), use la `azure role assignment changelog list` comando.

## <a name="export-to-a-spreadsheet"></a>Exportar a una hoja de cálculo
Para guardar el informe, o manipular los datos, exporte los cambios de access en un archivo .csv. A continuación, puede ver el informe en una hoja de cálculo para su revisión.

![Ver como hoja de cálculo - captura de pantalla de ChangeLog](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## <a name="see-also"></a>Vea también
- Introducción a [Control de acceso de Azure Role-Based](role-based-access-control-configure.md)
- Trabajar con [las funciones personalizadas en Azure RBAC](role-based-access-control-custom-roles.md)
