<properties
    pageTitle="Cmdlets de Azure Active Directory para configurar las opciones del grupo | Microsoft Azure"
    description="Cómo administrar la configuración de grupos de uso de cmdlets de Azure Active Directory."
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
    ms.date="09/22/2016"
    ms.author="curtand"/>


# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlets de Azure Active Directory para configurar las opciones de grupo

Los siguientes ajustes para grupos unificados se pueden configurar en su directorio:

1.  Clasificaciones: valores separados por comas lista de las clasificaciones de que los usuarios pueden establecer en un grupo. Ejemplos sería "Clasificados", "" y "Superior secreto."

2.  Dirección URL de las directrices de uso: una dirección URL que señala los usuarios a los términos de uso para el uso de grupos de unificada definida por su organización. Esta dirección URL se muestra en la interfaz de usuario, donde los usuarios usar grupos.

3.  Agrupar creación habilitada: Si ninguno, algunos o todos los usuarios pueden crear grupos unificada. Cuando se establece en, todos los usuarios pueden crear grupos. Cuando se establece en desactivado, ningún usuario puede crear grupos. Cuando off, también puede especificar grupos cuyos usuarios que todavía tiene permiso para crear un grupo de seguridad.

Esta configuración se configura mediante una configuración y objetos SettingsTemplate. Inicialmente, no podrá ver los objetos de configuración en el directorio. Esto significa que el directorio está configurado con la configuración predeterminada. Para cambiar la configuración predeterminada, creará un nuevo objeto de configuración con una plantilla de configuración. Plantillas de configuración están definidas por Microsoft.

Puede descargar el módulo que contiene los cmdlets que se usan para estas operaciones desde el [sitio de Microsoft Connect](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).

## <a name="create-settings-at-the-directory-level"></a>Crear la configuración en el nivel de directorio

Estos pasos crean configuración en el nivel de directorio, que se aplican a todos los grupos de Office en el directorio.

1. Si no sabe qué SettingTemplate usar, este cmdlet devuelve la lista de plantillas de configuración:

    `Get-MsolAllSettingTemplate`

    ![Lista de plantillas de configuración](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)

2. Para agregar una dirección URL de uso de la instrucción, primero debe obtener el objeto SettingsTemplate que define el valor de dirección URL de uso criterio; es decir, la plantilla de Group.Unified:

    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`

3. A continuación, cree un nuevo objeto de configuración basado en esa plantilla:

    `$setting = $template.CreateSettingsObject()`

4. A continuación, actualice el valor de criterio de uso:

    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`

5. Por último, aplicar la configuración:

    `New-MsolSettings –SettingsObject $setting`

    ![Agregar una dirección URL de uso de la instrucción](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

Estas son la configuración definida en la Group.Unified SettingsTemplate.

 **Configuración**                          | **Descripción**                                                                                             
--------------------------------------|-----------------------------------------------
 <ul><li>ClassificationList<li>Tipo: cadena<li>Valor predeterminado: ""                  | Una lista delimitada por comas de los valores de clasificación válida que se pueden aplicar a grupos unificada.                
 <ul><li>EnableGroupCreation<li>Tipo: booleano<li>Valor predeterminado: True              | El marcador que indica si se permite la creación de grupos de unificado en el directorio.                               
 <ul><li>GroupCreationAllowedGroupId<li>Tipo: cadena<li>Valor predeterminado: ""         | GUID del grupo de seguridad que se puede crear grupos unificada incluso cuando EnableGroupCreation == false.
 <ul><li>UsageGuidelinesUrl<li>Tipo: cadena<li>Valor predeterminado: ""                  | Vínculo a las instrucciones de uso de grupo.                                                                       

## <a name="read-settings-at-the-directory-level"></a>Configuración de lectura en el nivel de directorio

Estos pasos lea configuración en el nivel de directorio, que se aplica a todos los grupos de Office en el directorio.

1. Leer todos los valores de directorio existentes:

    `Get-MsolAllSettings`

2. Leer todos los valores de un grupo específico:

    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`

3. Leer directorio específico de configuración, SettingId GUID:

    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

    ![GUID de Id. de configuración](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>Actualizar la configuración en el nivel de directorio

Estos pasos actualización la configuración en el nivel de directorio, que se aplica a todos los grupos de Office en el directorio.

1. Obtener el objeto de configuración existente:

    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

2. Obtener el valor que desea actualizar:

    `$value = $Setting.GetSettingsValue()`

3. Actualizar el valor:

    `$value["AllowToAddGuests"] = "false"`

4. Actualizar la configuración:

    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>Eliminar la configuración en el nivel de directorio

Este paso elimina la configuración en el nivel de directorio, que se aplica a todos los grupos de Office en el directorio.

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Referencia de sintaxis de cmdlet

Puede encontrar más documentación de Azure Active Directory PowerShell en [Azure Active Directory Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=808260).

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>Referencia de objeto SettingsTemplate (Group.Unified SettingsTemplate objeto)

- "nombre": "EnableGroupCreation", "tipo": "System.Boolean", "defaultValue": "true", "Descripción": "Indicador de tipo boolean que indica si la característica de creación de grupo unificado está activada."

- "nombre": "GroupCreationAllowedGroupId", "tipo": "System.Guid", "defaultValue": "", "Descripción": "GUID del grupo de seguridad que está en la lista blanca para crear grupos de unificado".

- "nombre": "ClassificationList", "tipo": "System.String", "defaultValue": "", "Descripción": "Una delimitado por comas lista de valores de clasificación válida que se pueden aplicar a grupos unificado".

- "nombre": "UsageGuidelinesUrl", "tipo": "System.String", "defaultValue": "", "Descripción": "Un vínculo a las instrucciones de uso de grupo".

nombre | tipo | defaultValue | Descripción
----------  | ----------  | ---------  | ----------
"EnableGroupCreation"  | "System.Boolean"  | "true"  | "Indicador de tipo boolean que indica si la característica de creación de grupo unificado está activada."
"GroupCreationAllowedGroupId"  | "System.Guid"  | ""  | "GUID del grupo de seguridad que está en la lista blanca para crear grupos unificada".
"ClassificationList"  | "System.String"  | ""  | "Una delimitado por comas lista de valores de clasificación válida que se pueden aplicar a grupos unificada".
"UsageGuidelinesUrl"  | "System.String"  | ""  | "Un vínculo a las instrucciones de uso de grupo".

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar más documentación de Azure Active Directory PowerShell en [Azure Active Directory Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=808260).

Instrucciones adicionales de administrador de programas de Microsoft Roberto de Jong está disponible en el [Blog de grupos de Roberto](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad).

* [Administrar el acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)

* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
