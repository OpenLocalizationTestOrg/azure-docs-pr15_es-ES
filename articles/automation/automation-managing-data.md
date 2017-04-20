<properties 
   pageTitle="Administración de datos de Azure automatización | Microsoft Azure"
   description="Este artículo contiene varios temas para administrar un entorno de automatización de Azure.  Actualmente se incluye una copia de seguridad de recuperación de automatización de Azure en la automatización de Azure y retención de datos."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren;sngun" />

# <a name="managing-azure-automation-data"></a>Administración de datos de automatización de Azure

Este artículo contiene varios temas para administrar un entorno de automatización de Azure.

## <a name="data-retention"></a>Retención de datos

Al eliminar un recurso en la automatización de Azure, se conservará de 90 días para fines de auditoría antes de que se quita de forma permanente.  No puede ver o use el recurso durante este período.  Esta directiva también se aplica a los recursos que pertenecen a una cuenta de automatización que se elimina.

Automatización de Azure elimina automáticamente y lo elimina permanentemente trabajos de más de 90 días.

La siguiente tabla resume la directiva de retención para los distintos recursos.

|Datos|Directiva|
|:---|:---|
|Cuentas|90 días después de elimina la cuenta de un usuario se elimina permanentemente.|
|Activos|Se quitan permanentemente 90 días después de elimina un usuario activo o 90 días después de la cuenta que contiene que el activo se elimina un usuario.|
|Módulos|Se quitan permanentemente 90 días después de que se elimina el módulo de un usuario o 90 días después de la cuenta que contiene que el módulo se elimina un usuario.|
|Runbooks|Se quitan permanentemente 90 días después de que se elimina el recurso por un usuario o 90 días después de la cuenta que contiene que el recurso se elimina un usuario.|
|Trabajos|Eliminados y quitadas permanentemente 90 días después de última modificación. Esto puede deberse a después de la tarea finaliza, se detiene o se suspende.|
|Archivos de configuraciones/MOF de nodo| Configuración de nodo anterior se elimina permanentemente 90 días después de que se genera una nueva configuración de nodo.|
|DSC nodos| Quitar permanentemente 90 días después de que el nodo se anula el registro de cuenta de automatización mediante el portal de Azure o el cmdlet [Anular AzureRMAutomationDscNode](https://msdn.microsoft.com/library/mt603500.aspx) en Windows PowerShell. Los nodos se quitan permanentemente 90 días después de la cuenta que contiene que el nodo se elimina un usuario. |
|Informes de nodo| Elimina permanentemente 90 días después de que se genera un nuevo informe para ese nodo|

La directiva de retención se aplica a todos los usuarios y actualmente no se pueden personalizar.

## <a name="backing-up-azure-automation"></a>Copia de seguridad de automatización de Azure

Al eliminar una cuenta de automatización en Microsoft Azure, se eliminan todos los objetos de la cuenta incluidos runbooks, módulos, configuraciones, configuración, tareas y activos. No se puede recuperar los objetos después de elimina la cuenta.  Puede usar la siguiente información para el contenido de su cuenta de automatización de copia de seguridad antes de eliminarlo. 

### <a name="runbooks"></a>Runbooks

Puede exportar sus runbooks a archivos de secuencia de comandos con el Portal de administración de Azure o el cmdlet [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) en Windows PowerShell.  Estos archivos de script se pueden importar a otra cuenta de automatización como se describe en [crear o importar un Runbook](https://msdn.microsoft.com/library/dn643637.aspx).


### <a name="integration-modules"></a>Módulos de integración

No puede exportar módulos de integración de automatización de Azure.  Debe asegurarse de que están disponibles fuera de la cuenta de automatización.

### <a name="assets"></a>Activos

No puede exportar [activos](https://msdn.microsoft.com/library/dn939988.aspx) de automatización de Azure.  Con el Portal de administración de Azure, deben tener en cuenta los detalles de las variables, credenciales, certificados, conexiones y programaciones.  A continuación, debe crear manualmente los activos que utilizan runbooks que se importan en otra automatización.

Puede usar [cmdlets de Azure](https://msdn.microsoft.com/library/dn690262.aspx) para recuperar los detalles de activos sin cifrar y bien guardarlos en el futuro o crear activos equivalentes en otra cuenta de automatización.

No se puede recuperar el valor de variables cifradas o el campo de contraseña de uso de cmdlets de credenciales.  Si no conoce estos valores, puede recuperarlos desde un runbook usando las actividades [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) y [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) .

No puede exportar certificados de automatización de Azure.  Debe asegurarse de que todos los certificados están disponibles fuera de Azure.

### <a name="dsc-configurations"></a>Configuraciones de DSC

Puede exportar las configuraciones a archivos de secuencia de comandos con el Portal de administración de Azure o el cmdlet de [Exportación AzureRmAutomationDscConfiguration](https://msdn.microsoft.com/library/mt603485.aspx) en Windows PowerShell. Estas configuraciones se pueden importar y usar en otra cuenta de automatización.


##<a name="geo-replication-in-azure-automation"></a>Replicación de geo en automatización de Azure

Estándar en cuentas de Azure automatización geo replicación copia los datos de cuenta a una región geográfica distinta para redundancia. Puede elegir una región principal cuando configure su cuenta y, a continuación, una región secundaria se asigna automáticamente a él. Los datos del secundario, copiados de la región principal, se actualizan continuamente en caso de pérdida de datos.  

La siguiente tabla muestra los pares de área disponibles de primaria y secundaria.

|Principal            |Secundario
| ---------------   |----------------
|Sur Central de EE.   |Norte Central de EE.
|Estados Unidos oriental 2          |Central de EE.
|Europa occidental        |Europa del Norte
|Sudeste asiático    |Asia oriental
|Japón oriental         |Japón oeste

En el caso poco probable que se pierden datos de una región principal, Microsoft intenta recuperarla. Si no se puede recuperar los datos principales, a continuación, se realiza la migración tras error de geo y los clientes afectados recibirán una notificación sobre esto a través de su suscripción.

