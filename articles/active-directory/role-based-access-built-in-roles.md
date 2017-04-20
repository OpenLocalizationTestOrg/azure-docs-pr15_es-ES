<properties
    pageTitle="RBAC: Funciones integradas | Microsoft Azure"
    description="Este tema describe integrada en funciones de control de acceso basado en roles (RBAC)."
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
    ms.date="08/25/2016"
    ms.author="kgremban"/>

#<a name="rbac-built-in-roles"></a>RBAC: Funciones integradas

Azure Control de acceso basado en roles (RBAC) incluye las siguientes funciones integradas que se pueden asignar a los usuarios, grupos y servicios. No puede modificar las definiciones de funciones integradas. Sin embargo, puede crear [funciones personalizadas en Azure RBAC](role-based-access-control-custom-roles.md) para satisfacer las necesidades específicas de su organización.

## <a name="roles-in-azure"></a>Funciones de Azure

En la tabla siguiente proporciona una breve descripción de las funciones integradas. Haga clic en el nombre de la función para ver la lista detallada de las **acciones** y **notactions** para la función. La propiedad de **acciones** especifica las acciones permitidas en Azure recursos. Cadenas de acción pueden usar caracteres comodín. La propiedad **notactions** especifica las acciones que se excluyen de las acciones permitidas.

>[AZURE.NOTE] Las definiciones de función de Azure en constante evolución. En este artículo se mantiene como actualizado como sea posible, pero siempre puede encontrar las últimas definiciones de roles en Azure PowerShell. Usar los cmdlets `(get-azurermroledefinition "<role name>").actions` o `(get-azurermroledefinition "<role name>").notactions` según corresponda.

| Nombre de función | Descripción |
| --------- | ----------- |
| [API administración servicio colaborador](#api-management-service-contributor) | Puede administrar los servicios de administración de la API |
| [Aplicación perspectivas componente colaborador](#application-insights-component-contributor) | Puede administrar los componentes de aplicación perspectivas |
| [Operador de automatización](#automation-operator) | Puede iniciar, detener, suspender y reanudar trabajos |
| [BizTalk colaborador](#biztalk-contributor) | Puede administrar los servicios de BizTalk |
| [ClearDB MySQL DB colaborador](#cleardb-mysql-db-contributor) | Puede administrar bases de datos ClearDB MySQL |
| [Colaborador](#contributor) | Puede administrar todos los elementos excepto access. |
| [Colaborador del generador de datos](#data-factory-contributor) | Puede crear y administrar fábricas de datos y recursos secundarios dentro de ellos. |
| [DevTest prácticas usuario](#devtest-labs-user) | Puede ver todo el contenido y conectar máquinas virtuales de inicio, reinicio y apagado |
| [Colaborador de zona DNS](#dns-zone-contributor) | Puede administrar registros y zonas DNS |
| [Cuenta de DocumentDB colaborador](#documentdb-account-contributor) | Puede administrar cuentas de DocumentDB |
| [Sistemas inteligentes cuenta colaborador](#intelligent-systems-account-contributor) | Puede administrar cuentas de sistemas inteligentes |
| [Colaborador de red](#network-contributor) | Puede administrar todos los recursos de red |
| [Nuevo Relic APM cuenta colaborador](#new-relic-apm-account-contributor) | Puede administrar aplicaciones y cuentas de administración de rendimiento de aplicaciones de Relic nueva |
| [Propietario](#owner) | Puede administrar todo, incluido el acceso |
| [Lector](#reader) | Puede ver todo el contenido, pero no puede realizar cambios |
| [Redis colaborador de caché](#redis-cache-contributor) | Puede administrar Redis caché |
| [Programador trabajo colecciones colaborador](#scheduler-job-collections-contributor) | Puede administrar colecciones de trabajo del programador |
| [Colaborador de servicio de búsqueda](#search-service-contributor) | Puede administrar los servicios de búsqueda |
| [Administrador de seguridad](#security-manager) | Puede administrar los componentes de seguridad, directivas de seguridad y máquinas virtuales |
| [Colaborador de DB de SQL](#sql-db-contributor) | Puede administrar bases de datos SQL, pero no sus directivas relacionadas con la seguridad |
| [Administrador de seguridad SQL](#sql-security-manager) | Puede administrar las directivas relacionadas con la seguridad de SQL Server y bases de datos |
| [Colaborador SQL Server](#sql-server-contributor) | Puede administrar servidores SQL Server y bases de datos, pero no sus directivas relacionadas con la seguridad |
| [Colaborador de la cuenta de almacenamiento clásica](#classic-storage-account-contributor) | Puede administrar cuentas de almacenamiento clásica |
| [Colaborador de la cuenta de almacenamiento](#storage-account-contributor) | Puede administrar cuentas de almacenamiento |
| [Administrador de acceso de usuario](#user-access-administrator) | Puede administrar el acceso de usuario a recursos de Azure |
| [Colaborador clásica Máquina Virtual](#classic-virtual-machine-contributor) | Puede administrar máquinas virtuales de Windows clásico, pero no la red o almacenamiento cuenta virtual al que están conectados |
| [Colaborador de máquina virtual](#virtual-machine-contributor) | Puede administrar máquinas virtuales, pero no la red o almacenamiento cuenta virtual al que están conectados |
| [Red clásica colaborador](#classic-network-contributor) | Puede administrar redes virtuales clásicas y direcciones IP reservada |
| [Colaborador de Plan de Web](#web-plan-contributor) | Puede administrar los planes de web |
| [Sitio Web colaborador](#website-contributor) | Puede administrar sitios Web, pero no los planes de web al que están conectados |

## <a name="role-permissions"></a>Permisos de la función
Las tablas siguientes describen los permisos específicos para cada rol. Esto puede incluir **acciones**, que conceder permisos y **NotActions**, que les restringir.

### <a name="api-management-service-contributor"></a>API administración servicio colaborador
Puede administrar los servicios de administración de la API

| **Acciones** | |
| ------- | ------ |
| Microsoft.ApiManagement/Service/* | Crear y administrar los servicios de administración de la API |
| Microsoft.Authorization/*/read | Autorización de lectura |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="application-insights-component-contributor"></a>Aplicación perspectivas componente colaborador
Puede administrar los componentes de aplicación perspectivas

| **Acciones** | |
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Insights/components/* | Crear y administrar los componentes de perspectivas |
| Microsoft.Insights/webtests/* | Crear y administrar pruebas web |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="automation-operator"></a>Operador de automatización
Puede iniciar, detener, suspender y reanudar trabajos

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.Automation/automationAccounts/jobs/read | Leer automatización trabajos de cuenta |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Reanudar un trabajo de la cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Detener una tarea de la cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Leer automatización de flujos de trabajo de cuenta |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspender una tarea de la cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobs/write | Escribir una automatización de trabajos de cuenta |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Leer una programación de trabajo de la cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Leer una programación de trabajo de la cuenta de automatización |
| Microsoft.Automation/automationAccounts/read | Leer cuentas de automatización |
| Microsoft.Automation/automationAccounts/runbooks/read | Leer runbooks de automatización |
| Microsoft.Automation/automationAccounts/schedules/read | Leer automatización programaciones de cuenta |
| Microsoft.Automation/automationAccounts/schedules/write | Escribir una automatización de programaciones de cuenta |
| Microsoft.Insights/components/* | Crear y administrar los componentes de perspectivas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="biztalk-contributor"></a>BizTalk colaborador
Puede administrar los servicios de BizTalk

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.BizTalkServices/BizTalk/* | Crear y administrar los servicios de BizTalk |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB colaborador
Puede administrar bases de datos ClearDB MySQL

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |
| successbricks.cleardb/Databases/* | Crear y administrar bases de datos ClearDB MySQL |

### <a name="contributor"></a>Colaborador
Puede administrar todos los elementos excepto access

| **Acciones** ||
| ------- | ------ |
| * | Crear y administrar recursos de todos los tipos |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Authorization/*/Delete | No se puede eliminar roles y las asignaciones de roles |  
| Microsoft.Authorization/*/Write | No puede crear roles y las asignaciones de roles |

### <a name="data-factory-contributor"></a>Colaborador del generador de datos
Crear y administrar fábricas de datos y recursos secundarios dentro de ellos.

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.DataFactory/dataFactories/* | Crear y administrar fábricas de datos y recursos secundarios dentro de ellos. |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="devtest-labs-user"></a>DevTest prácticas usuario
Puede ver todo el contenido y conectar máquinas virtuales de inicio, reinicio y apagado

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.Compute/availabilitySets/read | Leer las propiedades de conjuntos de disponibilidad |
| Microsoft.Compute/virtualMachines/*/read | Leer las propiedades de una máquina virtual (tamaños VM, estado de tiempo de ejecución, las extensiones VM, etcetera) |
| Microsoft.Compute/virtualMachines/deallocate/action | Desasignar máquinas virtuales de Windows |
| Microsoft.Compute/virtualMachines/read | Leer las propiedades de una máquina virtual |
| Microsoft.Compute/virtualMachines/restart/action | Reiniciar máquinas virtuales |
| Microsoft.Compute/virtualMachines/start/action | Iniciar máquinas virtuales |
| Microsoft.DevTestLab/*/read | Leer las propiedades de un laboratorio |
| Microsoft.DevTestLab/labs/createEnvironment/action | Crear un entorno de prueba |
| Microsoft.DevTestLab/labs/formulas/delete | Eliminar fórmulas |
| Microsoft.DevTestLab/labs/formulas/read | Fórmulas de lectura |
| Microsoft.DevTestLab/labs/formulas/write | Agregar o modificar fórmulas |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Evaluar laboratorio directivas |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Unirse a un grupo de direcciones de back-end de equilibrador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Unirse a un equilibrador de carga de reglas NAT entrantes |
| Microsoft.Network/networkInterfaces/*/read | Leer las propiedades de una interfaz de red (por ejemplo, todos los equilibradores de carga que forma parte de la interfaz de red) |
| Microsoft.Network/networkInterfaces/join/action | Unirse a una máquina Virtual para una interfaz de red |
| Microsoft.Network/networkInterfaces/read | Lea las interfaces de red |
| Microsoft.Network/networkInterfaces/write | Escribir las interfaces de red |
| Microsoft.Network/publicIPAddresses/*/read | Leer las propiedades de una dirección IP pública |
| Microsoft.Network/publicIPAddresses/join/action | Unirse a una dirección IP pública |
| Microsoft.Network/publicIPAddresses/read | Leer las direcciones IP de red públicas |
| Microsoft.Network/virtualNetworks/subnets/join/action | Unirse a una red virtual |
| Microsoft.Resources/deployments/operations/read | Operaciones de implementación de lectura |
| Microsoft.Resources/deployments/read | Implementaciones de lectura |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Storage/storageAccounts/listKeys/action | Teclas de cuenta de almacenamiento de lista |

### <a name="dns-zone-contributor"></a>Colaborador de zona DNS
Puede administrar registros y zonas DNS.

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/\*/leer | Funciones de lectura y las asignaciones de roles |
| Microsoft.Insights/alertRules/\* | Crear y administrar reglas de alertas |
| Microsoft.Network/dnsZones/\* | Crear y administrar registros y zonas DNS |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el estado de los recursos |
| Microsoft.Resources/deployments/\* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Support/\* | Crear y administrar vales de soporte técnico |


### <a name="documentdb-account-contributor"></a>Cuenta de DocumentDB colaborador
Puede administrar cuentas de DocumentDB

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.DocumentDb/databaseAccounts/* | Crear y administrar cuentas de DocumentDB |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="intelligent-systems-account-contributor"></a>Sistemas inteligentes cuenta colaborador
Puede administrar cuentas de sistemas inteligentes

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.IntelligentSystems/accounts/* | Crear y administrar cuentas de sistemas inteligentes |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="network-contributor"></a>Colaborador de red
Puede administrar todos los recursos de red

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Network/* | Crear y administrar redes |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="new-relic-apm-account-contributor"></a>Nuevo Relic APM cuenta colaborador
Puede administrar aplicaciones y cuentas de administración de rendimiento de aplicaciones de Relic nueva

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |
| NewRelic.APM/accounts/* | Crear y administrar cuentas de administración de rendimiento de Relic nueva aplicación |

### <a name="owner"></a>Propietario
Puede administrar todo, incluido el acceso

| **Acciones** ||
| ------- | ------ |
| * | Crear y administrar recursos de todos los tipos |

### <a name="reader"></a>Lector
Puede ver todo el contenido, pero no puede realizar cambios

| **Acciones** ||
| ------- | ------ |
| * / leer | Leer los recursos de todos los tipos, excepto la información confidencial. |

### <a name="redis-cache-contributor"></a>Redis colaborador de caché
Puede administrar Redis caché

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.Cache/redis/* | Crear y administrar Redis caché |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="scheduler-job-collections-contributor"></a>Programador trabajo colecciones colaborador
Puede administrar colecciones de trabajo del programador

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |  
| Microsoft.Scheduler/jobcollections/* | Crear y administrar colecciones de trabajo |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico  |

### <a name="search-service-contributor"></a>Colaborador de servicio de búsqueda
Puede administrar los servicios de búsqueda

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |  
| Microsoft.Search/searchServices/* | Crear y administrar los servicios de búsqueda |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico  |

### <a name="security-manager"></a>Administrador de seguridad
Puede administrar los componentes de seguridad, directivas de seguridad y máquinas virtuales

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.ClassicCompute/*/read | Leer información de configuración clásica calcular máquinas virtuales de Windows |
| Microsoft.ClassicCompute/virtualMachines/*/write | Escribir la configuración para máquinas virtuales |
| Microsoft.ClassicNetwork/*/read | Lea la información de configuración de red clásica  |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |  
| Microsoft.Security/* | Crear y administrar las directivas y componentes de seguridad |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico  |

### <a name="sql-db-contributor"></a>Colaborador de DB de SQL
Puede administrar bases de datos SQL, pero no sus directivas relacionadas con la seguridad

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funciones de lectura y las asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Sql/servers/databases/* | Crear y administrar bases de datos SQL |
| Microsoft.Sql/servers/read | Leer los servidores SQL |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | No puede modificar las directivas de auditoría |
| Microsoft.Sql/servers/databases/auditingSettings/* | No se puede editar la configuración de auditoría |
| Microsoft.Sql/servers/databases/auditRecords/read  | No se puede leer los registros de auditoría |
| Microsoft.Sql/servers/databases/connectionPolicies/* | No puede modificar las directivas de conexión |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | No se puede editar datos masking directivas |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | No puede modificar las directivas de alerta de seguridad |
| Microsoft.Sql/servers/databases/securityMetrics/* | No se puede modificar las medidas de seguridad |

### <a name="sql-security-manager"></a>Administrador de seguridad SQL
Puede administrar las directivas relacionadas con la seguridad de SQL Server y bases de datos

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorización de Microsoft de lectura |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas de perspectivas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Sql/servers/auditingPolicies/* | Crear y administrar las directivas de auditoría de SQL server |
| Microsoft.Sql/servers/auditingSettings/* | Crear y administrar la configuración de auditoría de SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Crear y administrar directivas de auditoría de base de datos SQL server |
| Microsoft.Sql/servers/databases/auditingSettings/* | Crear y administrar la configuración de auditoría de base de datos SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read | Registros de auditoría de lectura |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Crear y administrar las directivas de conexión de base de datos SQL server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Crear y administrar masking directivas de datos de base de datos SQL server |
| Microsoft.Sql/servers/databases/read | Bases de datos SQL de lectura |
| Microsoft.Sql/servers/databases/schemas/read | Esquemas de base de datos de lectura SQL server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Columnas de la tabla de base de datos de lectura SQL server |
| Microsoft.Sql/servers/databases/schemas/tables/read | Tablas de base de datos de servidor de SQL de lectura |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Crear y administrar directivas de alerta de seguridad de base de datos de servidor SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Crear y administrar las medidas de seguridad de base de datos SQL server |
| Microsoft.Sql/servers/read | Leer los servidores SQL |
| Microsoft.Sql/servers/securityAlertPolicies/* | Crear y administrar directivas de alerta de seguridad de servidor SQL |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="sql-server-contributor"></a>Colaborador SQL Server
Puede administrar servidores SQL Server y bases de datos, pero no sus directivas relacionadas con la seguridad

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorización de lectura|
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas de perspectivas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Sql/servers/* | Crear y administrar servidores SQL Server |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | No puede modificar las directivas de auditoría de SQL server |
| Microsoft.Sql/servers/auditingSettings/* | No se puede editar la configuración de auditoría de SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | No se puede editar directivas de auditoría de base de datos SQL server |
| Microsoft.Sql/servers/databases/auditingSettings/* | No se puede editar la configuración de auditoría de base de datos SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read  | No se puede leer los registros de auditoría |
| Microsoft.Sql/servers/databases/connectionPolicies/* | No puede modificar las directivas de conexión de base de datos SQL server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | No se puede editar masking directivas de datos de base de datos SQL server |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | No se puede editar directivas de alerta de seguridad de base de datos de servidor SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | No se puede modificar los parámetros de seguridad de base de datos SQL server |
| Microsoft.Sql/servers/securityAlertPolicies/* | No se puede editar directivas de alerta de seguridad de servidor SQL |

### <a name="classic-storage-account-contributor"></a>Colaborador de la cuenta de almacenamiento clásica
Puede administrar cuentas de almacenamiento clásica

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorización de lectura |
| Microsoft.ClassicStorage/storageAccounts/* | Crear y administrar cuentas de almacenamiento |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas de perspectivas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |  
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="storage-account-contributor"></a>Colaborador de la cuenta de almacenamiento
Puede administrar cuentas de almacenamiento, pero no acceder a ellos.

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer todos los autorización |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas de perspectivas |
| Microsoft.Insights/diagnosticSettings/* | Administrar la configuración de diagnóstico |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |  
| Microsoft.Storage/storageAccounts/* | Crear y administrar cuentas de almacenamiento |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="user-access-administrator"></a>Administrador de acceso de usuario
Puede administrar el acceso de usuario a recursos de Azure

| **Acciones** ||
| ------- | ------ |
| * / leer | Leer los recursos de todos los tipos, excepto la información confidencial. |
| Microsoft.Authorization/* | Administrar la autorización |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="classic-virtual-machine-contributor"></a>Colaborador clásica Máquina Virtual
Puede administrar máquinas virtuales de Windows clásico pero no la red o almacenamiento cuenta virtual al que están conectados

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorización de lectura |
| Microsoft.ClassicCompute/domainNames/* | Crear y administrar los nombres de dominio de cálculo clásica |
| Microsoft.ClassicCompute/virtualMachines/* | Crear y administrar máquinas virtuales de Windows |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | Unirse a grupos de seguridad de red |
| Microsoft.ClassicNetwork/reservedIps/link/action | Vincular direcciones IP reservada |
| Microsoft.ClassicNetwork/reservedIps/read | Lectura reservado las direcciones IP |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Unirse a redes virtuales |
| Microsoft.ClassicNetwork/virtualNetworks/read | Redes virtuales de lectura |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Leer discos de cuenta de almacenamiento |
| Microsoft.ClassicStorage/storageAccounts/images/read | Leer imágenes de la cuenta de almacenamiento |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Teclas de cuenta de almacenamiento de lista |
| Microsoft.ClassicStorage/storageAccounts/read | Cuentas de almacenamiento clásico de lectura |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas de perspectivas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="virtual-machine-contributor"></a>Colaborador de máquina virtual
Puede administrar máquinas virtuales pero no la red o almacenamiento cuenta virtual al que están conectados

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorización de lectura |
| Microsoft.Compute/availabilitySets/* | Crear y administrar conjuntos de disponibilidad de cálculo |
| Microsoft.Compute/locations/* | Crear y administrar ubicaciones de cálculo |
| Microsoft.Compute/virtualMachines/* | Crear y administrar máquinas virtuales de Windows |
| Microsoft.Compute/virtualMachineScaleSets/* | Crear y administrar conjuntos de escala de máquina virtual |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas de perspectivas |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Unirse a grupos de dirección de back-end de puerta de enlace de aplicaciones de red |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Unirse a grupos de direcciones de back-end de equilibrador de carga |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Unirse a equilibrador de carga de grupos de NAT de entrada |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Unirse a equilibrador de carga NAT reglas de entrada |
| Microsoft.Network/loadBalancers/read | Equilibradores de carga de lectura |
| Microsoft.Network/locations/* | Crear y administrar ubicaciones de red |
| Microsoft.Network/networkInterfaces/* | Crear y administrar interfaces de red |
| Microsoft.Network/networkSecurityGroups/join/action | Unirse a grupos de seguridad de red |
| Microsoft.Network/networkSecurityGroups/read | Leer los grupos de seguridad de red |
| Microsoft.Network/publicIPAddresses/join/action | Unirse a las direcciones IP de red públicas |
| Microsoft.Network/publicIPAddresses/read | Leer las direcciones IP de red públicas |
| Microsoft.Network/virtualNetworks/read | Redes virtuales de lectura |
| Microsoft.Network/virtualNetworks/subnets/join/action | Unirse a subredes de una red virtual |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |  
| Microsoft.Storage/storageAccounts/listKeys/action | Teclas de cuenta de almacenamiento de lista |
| Microsoft.Storage/storageAccounts/read | Cuentas de almacenamiento de lectura |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="classic-network-contributor"></a>Red clásica colaborador
Puede administrar redes virtuales clásicas y direcciones IP reservada

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorización de lectura |
| Microsoft.ClassicNetwork/* | Crear y administrar redes clásicas |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas de perspectivas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |  
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

### <a name="web-plan-contributor"></a>Colaborador de Plan de Web
Puede administrar los planes de web

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorización de lectura |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas de perspectivas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |  
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |
| Microsoft.Web/serverFarms/* | Crear y administrar conjuntos de servidores |

### <a name="website-contributor"></a>Sitio Web colaborador
Puede administrar sitios Web pero no los planes de web al que están conectados

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorización de lectura |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas de perspectivas |
| Microsoft.Insights/components/* | Crear y administrar los componentes de perspectivas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Estado de lectura de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar las implementaciones de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer los grupos de recursos |  
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |
| Microsoft.Web/certificates/* | Crear y administrar los certificados de sitio Web |
| Microsoft.Web/listSitesAssignedToHostName/read | Leer sitios asignados a un nombre de host |
| Microsoft.Web/serverFarms/join/action | Unirse a conjuntos de servidores |
| Microsoft.Web/serverFarms/read | Leer conjuntos de servidores |
| Microsoft.Web/sites/* | Crear y administrar sitios Web |

## <a name="see-also"></a>Vea también
- [Control de acceso basado en roles](role-based-access-control-configure.md): Introducción a RBAC en el portal de Azure.
- [Funciones personalizadas en Azure RBAC](role-based-access-control-custom-roles.md): Obtenga información sobre cómo crear funciones personalizadas a sus necesidades de acceso.
- [Crear un acceso cambiar informe histórico](role-based-access-control-access-change-history-report.md): realizar un seguimiento de cambiar las asignaciones de roles en RBAC.
- [Solución de problemas de Control de acceso basado en roles](role-based-access-control-troubleshooting.md): obtener sugerencias para solucionar problemas comunes.
