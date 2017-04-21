<properties 
   pageTitle="Proteger las zonas DNS y registros | Microsoft Azure" 
   description="Cómo proteger zonas DNS y conjuntos de registros de DNS de Microsoft Azure." 
   services="dns" 
   documentationCenter="na" 
   authors="jtuliani" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/20/2016"
   ms.author="jtuliani"/>

# <a name="how-to-protect-dns-zones-and-records"></a>Cómo proteger los registros y zonas DNS

Registros y zonas DNS son recursos esenciales. Eliminar una zona DNS o incluso simplemente un único registro DNS puede dar lugar a una interrupción del servicio total.  Por lo tanto, es importante que crítica zonas y registros DNS están protegidos contra cambios accidentales o no autorizados.

En este artículo se explica cómo Azure DNS le permite proteger su zonas DNS y registros de dichos cambios.  Aplique dos eficaces características de seguridad proporcionadas por el Administrador de recursos de Azure: [control de acceso basado en roles](../active-directory/role-based-access-control-what-is.md) y [bloqueos de recursos](../resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Control de acceso basado en roles

Azure Control de acceso basado en roles (RBAC) permite la administración de acceso específico para usuarios de Azure, grupos y recursos. Using RBAC, puede conceder exactamente con la cantidad de acceso que necesitan los usuarios para realizar su trabajo. Para obtener más información acerca de cómo RBAC le ayuda a administrar el acceso, vea [¿Qué es el Control de acceso basado en roles](../active-directory/role-based-access-control-what-is.md).

### <a name="the-dns-zone-contributor-role"></a>La función 'Colaborador de zona DNS'

La función 'Colaborador de zona DNS' es una función integrada proporcionada por Azure para administración de recursos DNS.  Asignar permisos de colaborador de zona DNS a un usuario o grupo permite administrar recursos DNS, pero no los recursos de cualquier otro tipo de ese grupo.

Por ejemplo, suponga que el grupo de recursos 'myzones' contiene cinco zonas de Contoso Corporation. Conceder permisos de 'Colaborador de zona DNS' a ese grupo de recursos de administrador DNS, habilita control total sobre las zonas DNS. También evita la concesión de permisos innecesarios, por ejemplo el administrador DNS no puede crear o detener máquinas virtuales de Windows.

La manera más sencilla de asignar permisos de RBAC es [a través del portal de Azure](../active-directory/role-based-access-control-configure.md).  Abra el módulo de 'Control de acceso (IAM)' para el grupo de recursos, a continuación, haga clic en 'Agregar', y elija la función 'Colaborador de zona DNS' y los usuarios requeridos o grupos para conceder permisos.

![Nivel de grupo de recursos RBAC a través del portal de Azure](./media/dns-protect-zones-recordsets/rbac1.png)

Permisos también pueden ser [concedido con PowerShell Azure](../active-directory/role-based-access-control-manage-access-powershell.md):

    # Grant 'DNS Zone Contributor' permissions to all zones in a resource group
    New-AzureRmRoleAssignment -SignInName <user email address> -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName <resource group name>

El comando equivalente también está [disponible mediante la CLI de Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

    # Grant 'DNS Zone Contributor' permissions to all zones in a resource group
    azure role assignment create --signInName  <user email address> --roleName "DNS Zone Contributor" --resourceGroup <resource group name>

### <a name="zone-level-rbac"></a>Nivel de zona RBAC

Pueden aplicar reglas de Azure RBAC una suscripción a un grupo de recursos o a un recurso individual. En el caso de DNS de Azure, ese recurso puede ser una zona DNS individual, o incluso un conjunto de registros individual.

Por ejemplo, suponga que el grupo de recursos 'myzones' contiene la zona contoso.com y una subzona 'customers.contoso.com' en el que se crean los registros CNAME para cada cuenta de cliente.  La cuenta utilizada para administrar estos registros CNAME debe asignar permisos para crear registros en la zona 'customers.contoso.com' sólo, no debería tener acceso a las demás zonas.

Pueden conceder permisos de RBAC de nivel de zona a través del portal de Azure.  Abra el módulo 'Control de acceso (IAM)' de la zona, a continuación, haga clic en 'Agregar', y elija la función 'Colaborador de zona DNS' y los usuarios requeridos o grupos para conceder permisos.

![RBAC de nivel de zona DNS a través del portal de Azure](./media/dns-protect-zones-recordsets/rbac2.png)

Permisos también pueden ser [concedido con PowerShell Azure](../active-directory/role-based-access-control-manage-access-powershell.md):

    # Grant 'DNS Zone Contributor' permissions to a specific zone
    New-AzureRmRoleAssignment -SignInName <user email address> -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName <resource group name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones

El comando equivalente también está [disponible mediante la CLI de Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

    # Grant 'DNS Zone Contributor' permissions to a specific zone
    azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>


### <a name="record-set-level-rbac"></a>Registro establece nivel RBAC

Podemos ir un paso más. Tenga en cuenta el Administrador de correo de Contoso Corporation, quién necesita obtener acceso a los registros MX y TXT en el vértice de la zona de 'contoso.com'.  No necesita tener acceso a los demás registros MX o TXT o a cualquier registro de cualquier otro tipo.  DNS Azure le permite asignar permisos en el nivel de conjunto de registros a exactamente con los registros que el Administrador de correo tiene acceso a.  El Administrador de correo se concede exactamente con el control que necesita y no puede realizar otros cambios.
  
Permisos de nivel de conjunto de registros RBAC pueden configurarse a través del portal de Azure, con el botón 'Usuarios' el módulo de conjunto de registros:

![Registro establece nivel RBAC a través del portal de Azure](./media/dns-protect-zones-recordsets/rbac3.png)

Permisos de nivel de conjunto de registros RBAC también pueden [conceder con PowerShell Azure](../active-directory/role-based-access-control-manage-access-powershell.md):

    # Grant permissions to a specific record set
    New-AzureRmRoleAssignment -SignInName <user email address> -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>" 

El comando equivalente también está [disponible mediante la CLI de Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

    # Grant permissions to a specific record set
    azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

### <a name="custom-roles"></a>Funciones personalizadas

La función 'Colaborador de zona DNS' integrada permite control total sobre un recurso DNS. También es posible crear su propio cliente las funciones de Azure, para proporcionar control más preciso incluso.

Considere la posibilidad de nuevo el ejemplo en el que se crea un registro CNAME en la zona 'customers.contoso.com' para cada cuenta de cliente de Contoso Corporation.  La cuenta utilizada para administrar estos CNAME debe tener permiso para administrar sólo los registros CNAME.  A continuación, es no se puede modificar los registros de otros tipos (como cambiar registros MX) o realizar operaciones de nivel de zona, como la eliminación de zona.

En el ejemplo siguiente se muestra una definición de la función personalizada para la administración de registros CNAME solo:

    {
        "Name": "DNS CNAME Contributor",
        "Id": "",
        "IsCustom": true,
        "Description": "Can manage DNS CNAME records only.",
        "Actions": [
            "Microsoft.Network/dnsZones/CNAME/*",
            "Microsoft.Network/dnsZones/read",
            "Microsoft.Authorization/*/read",
            "Microsoft.Insights/alertRules/*",
            "Microsoft.ResourceHealth/availabilityStatuses/read",
            "Microsoft.Resources/deployments/*",
            "Microsoft.Resources/subscriptions/resourceGroups/read",
            "Microsoft.Support/*"
        ],
        "NotActions": [
        ],
        "AssignableScopes": [
            "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
        ]
    }

La propiedad de acciones define los siguientes permisos específicas de DNS:

- `Microsoft.Network/dnsZones/CNAME/*`concede un control completo sobre registros CNAME
- `Microsoft.Network/dnsZones/read`concede permiso para leer las zonas DNS, pero no se puede modificar, lo que le permite ver la zona en la que se va a crear el registro CNAME.

Las acciones restantes se copian desde el [rol de colaborador de zona DNS integrado](../active-directory/role-based-access-built-in-roles.md#dns-zone-contributor).

>[AZURE.NOTE] Usando una función RBAC personalizada para evitar la eliminación de conjuntos de registros mientras sigue permitiendo que actualizarlos no es un control eficaz. Impide que los conjuntos de registro que se elimina, pero ellos impedir que se modifique.  Modificaciones permitidas incluyen agregar y eliminar registros de conjunto de registros, incluida la eliminación de todos los registros para salir de un conjunto de registros 'empty'. Esto tiene el mismo efecto que eliminar el registro establecer desde un punto de vista de resolución DNS.

Definiciones de función personalizado actualmente no se puede definir a través del portal de Azure. Una función personalizada en función de esta definición de la función pueden crearse con Azure PowerShell:

    # Create new role definition based on input file
    New-AzureRmRoleDefinition -InputFile <file path>

También se pueden crear mediante la CLI de Azure:

    # Create new role definition based on input file
    azure role create –inputfile <file path>

La función se puede asignar a continuación, en la misma manera que las funciones integradas, como se describe más adelante en este artículo.

Para obtener más información sobre cómo crear, administrar y asignar roles personalizados, vea [Funciones personalizadas en Azure RBAC](../active-directory/role-based-access-control-custom-roles.md).

## <a name="resource-locks"></a>Bloqueos de recursos

Además de RBAC, Administrador de recursos de Azure es compatible con otro tipo de control de seguridad, es decir, la capacidad de recursos 'lock'. Donde reglas RBAC le permiten controlar las acciones de usuarios y grupos específicos, bloqueos de recursos se aplican al recurso y son eficaces en todos los usuarios y roles. Para obtener más información, consulte [los recursos de bloqueo con el Administrador de recursos de Azure](../resource-group-lock-resources.md).

Existen dos tipos de bloqueo de recursos: **DoNotDelete** como de **sólo lectura**. Estos se pueden aplicar a una zona DNS, o a un conjunto de registros individual.  Las secciones siguientes describen varios escenarios comunes y cómo les ayude a usar bloqueos de recursos.

### <a name="protecting-against-all-changes"></a>Protección contra todos los cambios

Para evitar que los cambios realizados, aplicar un bloqueo de sólo lectura a la zona.  Esto impide los conjuntos de registros nuevos conjuntos de registros creados y existentes se modificado o eliminado.

Bloqueos de recursos en el nivel de zona pueden crearse a través del portal de Azure.  En el módulo de zona DNS, haga clic en 'Bloqueos' después 'Agregar':

![Bloqueos de recursos en el nivel de zona a través del portal de Azure](./media/dns-protect-zones-recordsets/locks1.png)

Nivel de zona recursos bloqueos también pueden crearse a través de Azure PowerShell:

    # Lock a DNS zone
    New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name> 

Configurar bloqueos de recursos Azure no es actualmente compatible a través de la CLI de Azure.

### <a name="protecting-individual-records"></a>Protección de registros individuales
Para evitar que un registro DNS existente Establecer contra la modificación, aplicar un bloqueo de sólo lectura para el conjunto de registros.

>[AZURE.NOTE] Aplicar un bloqueo DoNotDelete a un conjunto de registros no es un control eficaz. Impide que el conjunto de eliminación de registros, pero impedir que se modifique.  Modificaciones permitidas incluyen agregar y eliminar registros de conjunto de registros, incluida la eliminación de todos los registros para salir de un conjunto de registros 'empty'. Esto tiene el mismo efecto que eliminar el registro establecer desde un punto de vista de resolución DNS.

Bloqueos de recursos en el nivel de conjunto de registros pueden actualmente solo se pueden configurar mediante PowerShell de Azure.  No admiten en el portal de Azure o CLI de Azure.

    # Lock a DNS record set
    New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name>/<record set name> -ResourceType Microsoft.Network/DNSZones/<record type> -ResourceGroupName <resource group name> 

### <a name="protecting-against-zone-deletion"></a>Protección contra eliminación de zona

Cuando se elimina una zona de DNS de Azure, también se eliminan todos los conjuntos de registros en la zona.  Esta operación no se puede deshacer.  Eliminar accidentalmente una zona crítica tiene el potencial para tener un impacto empresariales importantes.  Por lo tanto, es muy importante proteger contra eliminación accidental zona.

Aplicar un bloqueo DoNotDelete a una zona impide que la zona de eliminarse.  Sin embargo, dado que recursos secundarios heredan los bloqueos, también evita que los conjuntos de registros en la zona de eliminarse, que puede ser no deseado.  Además, como se describe en la nota anterior, también es ineficaz puesto que aún se pueden quitar registros de los conjuntos de registro existentes.

Como alternativa, considere la posibilidad de aplicar un bloqueo DoNotDelete a un conjunto de registros de la zona, como el conjunto de registros de inicio de autoridad.  Puesto que no se puede eliminar la zona sin eliminar también los conjuntos de registros, esto protege contra eliminación de zona, permitiendo conjuntos de registros dentro de la zona modificarse libremente. Si intenta eliminar la zona, Administrador de recursos de Azure detecta esto también eliminaría el conjunto de registros de inicio de autoridad y bloquea la llamada porque el inicio de autoridad está bloqueada.  No se eliminan conjuntos de registros.

El siguiente comando PowerShell crea un bloqueo DoNotDelete contra el registro de inicio de autoridad de la zona:

    # Protect against zone delete with DoNotDelete lock on the record set
    New-AzureRmResourceLock -LockLevel DoNotDelete -LockName <lock name> -ResourceName <zone name>/@ -ResourceType Microsoft.Network/DNSZones/SOA -ResourceGroupName <resource group name> 

Otra manera de evitar el borrado accidental zona está utilizando una función personalizada para garantizar el operador y usadas para administrar las zonas de cuentas de servicio no tienen permisos de eliminación zona. Cuando necesite eliminar una zona, puede aplicar una eliminación de dos pasos, primero conceder permisos para eliminar zona (en el ámbito de la zona, para evitar la eliminación de la zona incorrecta) y segundo para eliminar la zona.

En este segundo enfoque tiene la ventaja de que funciona para todas las zonas que tiene acceso a estas cuentas, sin tener que recordar crear los bloqueos. Tiene la desventaja que cualquier cuenta que tenga permisos de eliminación de zona, como el propietario de la suscripción, puede eliminar aún accidentalmente una zona crítica.

Es posible utilizar ambos enfoques, bloqueos de recursos y funciones personalizadas, al mismo tiempo, como un enfoque de defensa en profundidad para la protección de zona DNS.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre cómo trabajar con RBAC, vea [Introducción a la administración de acceso en el portal de Azure](../active-directory/role-based-access-control-what-is.md). 
- Para obtener más información sobre cómo trabajar con bloqueos de recursos, vea [recursos de bloqueo con el Administrador de recursos de Azure](../resource-group-lock-resources.md).
- Para obtener más información sobre cómo proteger los recursos de Azure, vea [Consideraciones de seguridad para el Administrador de recursos de Azure](../best-practices-resource-manager-security.md).
