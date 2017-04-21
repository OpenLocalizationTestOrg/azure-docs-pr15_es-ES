<properties
    pageTitle="Registrar características de análisis para proveedores de servicios | Microsoft Azure"
    description="Análisis de registro puede ayudar a los proveedores de servicios administrados (MSP), grandes empresas, proveedores de software independientes (ISV) y proveedores de servicios de hospedaje administración y supervisión los servidores de infraestructura de nube o local del cliente."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="richrund"/>

# <a name="log-analytics-features-for-service-providers"></a>Características de análisis de registro para proveedores de servicios

Análisis de registro puede ayudar a los proveedores de servicios administrados (MSP), grandes empresas, proveedores de software independientes (ISV) y proveedores de servicios de hospedaje administrar y supervisar los servidores de infraestructura de nube o local del cliente. 

Grandes empresas compartan muchas similitudes con proveedores de servicios, especialmente cuando hay un equipo de TI centralizado que es responsable de la administración de TI de muchas empresas diferentes. Para simplificar, este documento usa el término *proveedor de servicios* pero también está disponible para empresas y otros clientes de la misma funcionalidad.

## <a name="cloud-solution-provider"></a>Proveedor de soluciones de nube

Asociados y proveedores de servicios que forman parte del programa de [Proveedor de soluciones de nube (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , el análisis de registro es uno de los servicios de Azure disponibles en una suscripción de CSP. 

Para el análisis de registro, se habilitan las siguientes capacidades en suscripciones de *Proveedor de soluciones de nube* .

Como un *Proveedor de soluciones de nube* puede:

+ Crear áreas de trabajo de análisis de registro de suscripción de un inquilino (cliente).
+ Áreas de trabajo de Access creados por los inquilinos. 
+ Agregar y quitar el acceso de usuario al área de trabajo con la administración de usuarios de Azure. Cuando en área de trabajo del inquilino en el portal OMS página de administración de usuarios en la configuración no está disponible
  - Análisis de registro no es compatible con acceso basado en roles todavía - ofreciendo un usuario `reader` permisos en el portal de Azure les permite realizar cambios de configuración en el portal OMS

Para iniciar sesión en la suscripción del inquilino, debe especificar el identificador del inquilino. El identificador del inquilino suele ser la última parte de la dirección de correo electrónico que se usa para iniciar sesión.

+ En el portal de OMS, agregue `?tenant=contoso.com` en la dirección URL del portal. Por ejemplo,`mms.microsoft.com/?tenant=contoso.com`
+ En PowerShell, use la `-Tenant contoso.com` parámetro cuando se usa `Add-AzureRmAccount` cmdlet
+ El identificador del inquilino se agrega automáticamente al usar el `OMS portal` vínculo desde el portal de Azure para abrir e inicie sesión en el portal OMS del área de trabajo seleccionada

Como un *cliente* de un proveedor de soluciones de nube puede:

+ Crear áreas de trabajo de análisis de registro en una suscripción de CSP
+ Áreas de trabajo de Access creadas por el CSP
  -  Usar el `OMS portal` vínculo desde el portal de Azure para abrir e inicie sesión en el portal OMS del área de trabajo seleccionada
+ Ver y usar la página de administración de usuario en configuración en el portal OMS

>[AZURE.NOTE] Las soluciones de copia de seguridad y recuperación de sitio para el análisis de registro no son capaces de conectarse a un depósito de servicios de recuperación y no se puede configurar en una suscripción de CSP.

## <a name="managing-multiple-customers-using-log-analytics"></a>Administrar a varios clientes mediante el análisis de registro 

Se recomienda que cree un área de trabajo de análisis de registro de cada cliente que se encarga de administrar. Proporciona un área de trabajo de análisis de registro:

+ Ubicación geográfica para almacenar los datos. 
+ Detalle de facturación 
+ Aislamiento de datos 
+ Configuración única

Mediante la creación de un área de trabajo por cliente, es posible separar los datos de cada cliente y de seguimiento también el uso de cada cliente.

Más información sobre cuándo y por qué crear varias áreas de trabajo se describe en [administrar el acceso para iniciar el análisis] (log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Pueden automatizar la creación y configuración de áreas de trabajo de cliente con [PowerShell](log-analytics-powershell-workspace-configuration.md), [plantillas de administrador de recursos](log-analytics-template-workspace-configuration.md), o con la [API de REST](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

Permite el uso de plantillas de administrador de recursos para la configuración del área de trabajo que tenga una configuración maestra que se pueden usar para crear y configurar áreas de trabajo. Puede estar seguro de que cuando se crean áreas de trabajo para los clientes se configuran automáticamente a sus necesidades. Cuando actualiza los requisitos, la plantilla se actualiza y, a continuación, volver a aplicar las áreas de trabajo existentes. Este proceso garantiza que las áreas de trabajo existentes cumplen los estándares nuevos.    

Al administrar varias áreas de trabajo de análisis de registro, se recomienda la integración de cada área de trabajo con el sistema de vales existente consola de operaciones con la funcionalidad de [alertas](log-analytics-alerts.md) . Mediante la integración con los sistemas existentes, puede continuar personal de soporte técnico seguir sus procesos habituales. Análisis de registro con regularidad comprueba cada área de trabajo con los criterios de la alerta que especifique y genera una alerta cuando es necesario hacer nada.

Para los informes de nivel ejecutivos que resumir datos en todas las áreas de trabajo puede usar la integración entre el análisis de registro y [PowerBI](log-analytics-powerbi.md). Si necesita integrar con otro sistema de generación de informes, puede usar la API de búsqueda (mediante PowerShell o [resto](log-analytics-log-search-api.md)) para ejecutar consultas y exportar los resultados de búsqueda.

## <a name="next-steps"></a>Pasos siguientes

+ Automatizar la creación y configuración de áreas de trabajo con [plantillas de administrador de recursos](log-analytics-template-workspace-configuration.md)
+ Automatizar la creación de áreas de trabajo con [PowerShell](log-analytics-powershell-workspace-configuration.md) 
+ Uso de [alertas](log-analytics-alerts.md) para integrar con sistemas existentes
+ Generar informes de resumen con [PowerBI](log-analytics-powerbi.md)
