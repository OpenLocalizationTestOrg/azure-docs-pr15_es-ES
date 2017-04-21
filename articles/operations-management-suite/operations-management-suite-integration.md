<properties
   pageTitle="Integración con el conjunto de aplicaciones de administración de operaciones (OMS) | Microsoft Azure"
   description="Además de usar las características estándar de OMS, integrarlo con otras aplicaciones de administración y servicios para proporcionar un entorno híbrido de administración, para proporcionar escenarios de administración personalizado únicos en su entorno, o para proporcionar una administración personalizada experiencia para sus clientes.  Este artículo proporciona una descripción general de las diferentes opciones de integración con OMS y vínculos a artículos que proporciona información técnica detallada."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="bwren" />

# <a name="integrating-with-operations-management-suite-oms"></a>Integración con el conjunto de aplicaciones de administración de operaciones (OMS)

Serie de administración de operaciones es basado en la nube administración solución de Microsoft que le ayuda a administrar y proteger su local y la infraestructura de nube.  Además de usar las características estándar de OMS, integrarlo con otras aplicaciones de administración y servicios para proporcionar un entorno híbrido de administración, para proporcionar escenarios de administración personalizado únicos en su entorno, o para proporcionar una administración personalizada experiencia para sus clientes.  Este artículo proporciona una descripción general de las diferentes opciones para la integración con servicios OMS y vínculos a artículos que proporciona información técnica detallada. 



## <a name="log-analytics"></a>Análisis de registro
Datos de administración recopilados por el análisis de registro se almacenan en un repositorio que no está alojado en Azure.  Todos los datos almacenados en el repositorio está disponible en las búsquedas de registro que proporcionan análisis rápido en muy grandes cantidades de datos.  Los requisitos de integración pueden para rellenar el repositorio con los nuevos datos ponerlo a disposición para análisis, o para extraer datos en el repositorio para proporcionar una visualización nueva o integrar con otra herramienta de administración.

Cada elemento de datos en el repositorio se almacena como un registro.  Al rellenar el repositorio, debe proporcionar a los usuarios el tipo de registro que usa la solución y una descripción de sus propiedades.  Al recuperar datos, necesitará esta información acerca de los datos que está trabajando.

![Rellenar el repositorio OMS](media/operations-management-suite-integration/repository.png)


### <a name="populate-the-log-analytics-repository"></a>Rellenar el repositorio de análisis de registro
Existen varios métodos para rellenar el repositorio OMS.  El método que utilice dependerá de factores como donde se encuentra el origen de datos, el formato de los datos y que necesita compatibilidad con los clientes.  Una vez que los datos se almacenan en el repositorio, no supone ninguna diferencia cómo fue recopilada.

Las secciones siguientes describen las diferentes opciones para rellenar el repositorio OMS.

#### <a name="connected-sources-and-data-sources"></a>Orígenes de datos y orígenes conectados 
Orígenes conectados son las ubicaciones donde se pueden recuperar los datos del repositorio de OMS.  Orígenes de datos y soluciones ejecutan en orígenes conectados y definen los datos específicos que se recopilan.  Si su aplicación escribe datos a uno de estos orígenes de datos, puede recopilar mediante la configuración de origen de datos.  Por ejemplo, si su aplicación crea eventos de registro del sistema, a continuación, puede recopilar el origen de datos del registro del sistema en un agente de Linux.

- [Orígenes de datos en el análisis de registro](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Soluciones

Soluciones amplían las capacidades de OMS.  Una solución posible recopilar datos desde el origen conectado o puede realizar análisis en ya ha recopilado en el repositorio de registros.  Cada solución proporcionado por Microsoft tiene un artículo individual que proporciona los detalles sobre los datos que recopila.

- [Soluciones de análisis de registro](../log-analytics/log-analytics-add-solutions.md)



#### <a name="http-data-collector-api"></a>Selector de datos HTTP API

La API de selector de datos de registro de análisis de HTTP es una API de REST que le permite agregar datos JSON al repositorio de análisis de registro.  Puede aprovechar esta API cuando tenga una aplicación que no proporciona datos a través de uno de los otros orígenes de datos o soluciones.  Puede usar para rellenar el repositorio desde cualquier cliente que puede llamar a la API y no se basa en la programación de la colección de cualquier origen de datos o una solución.

- [Selector de datos HTTP de análisis de registro API](../log-analytics/log-analytics-data-collector-api.md)


### <a name="retrieve-data-from-the-log-analytics-repository"></a>Recuperar datos del repositorio de análisis de registro

Existen varios métodos para recuperar datos del repositorio de OMS.  Que desea que los usuarios para recuperar datos utilizando la consola OMS y proporcionarles diferentes tipos de visualizaciones y análisis.  También puede recuperar los datos de un proceso externo, como otra solución de administración.

#### <a name="log-searches"></a>Búsquedas de registro

Todos los datos almacenados en el repositorio OMS está disponible a través de búsquedas de registro.  Los usuarios pueden realizar sus propias análisis ad hoc en la consola OMS o crear un panel con una visualización para realizar una búsqueda de registro concreto.  Las soluciones pueden contener vistas personalizadas con visualizaciones basados en búsquedas predefinidas.  Puede usar la API de búsqueda de registro para los datos de access en el repositorio OMS desde una herramienta de administración o aplicación externa.  

- [Búsquedas de registro de análisis de registro](../log-analytics/log-analytics-log-searches.md)
- [API de REST de búsqueda de registros de análisis de registro](../log-analytics/log-analytics-log-search-api.md)
- [Cmdlets de análisis de registro](https://msdn.microsoft.com/library/mt188224.aspx)



#### <a name="custom-views"></a>Vistas personalizadas 
El Diseñador de vistas le permite crear vistas personalizadas en la consola OMS que proporcionar a los usuarios con visualización y análisis de los datos de la solución.  Cada vista incluye el icono que se muestra en la página principal de la consola y cualquier número de elementos de visualización que se basan en búsquedas de registro que defina.
  
- [Diseñador de vistas de análisis de registro](../log-analytics/log-analytics-view-designer.md)


#### <a name="power-bi"></a>Power BI

Análisis de registro puede exportar automáticamente datos del repositorio de OMS en Power BI para puede aprovechar sus visualizaciones y herramientas de análisis.  Realiza la exportación en una programación para que los datos que se mantienen actualizados. 

- [Exportar datos de análisis de registro a Power BI](../log-analytics/log-analytics-powerbi.md)




## <a name="automation"></a>Automatización

OMS puede automatizar procesos ante los datos recopilados o para realizar otras funciones de administración.  Puede recopilar datos de la aplicación e insertarlo en el repositorio OMS, o puede automatizar la corrección de un problema conocido en respuesta a los datos que se encuentra en el repositorio. 

![Automatización](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbooks

Runbooks en Azure automatización ejecutar secuencias de comandos de PowerShell y flujos de trabajo en la nube de Azure.  Puede usarlos para administrar recursos en Azure o todos los recursos que se pueden acceder desde la nube.  Runbooks también se pueden ejecutar en un centro de datos local con híbrido Runbook trabajo.  Puede iniciar un runbook desde el portal de Azure o desde procesos externos con un número de métodos como PowerShell o la API de automatización.

- [Iniciar un runbook de automatización de Azure](../automation/automation-starting-a-runbook.md)
- [Cmdlets de automatización de Azure](https://msdn.microsoft.com/library/dn690262.aspx)
- [API de REST de automatización](https://msdn.microsoft.com/library/mt662285.aspx)
- [Automatización .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Alertas

Reglas de alerta automáticamente ejecutan búsquedas de registros según una programación.  Si los resultados que coincidan con determinados criterios la alerta resultante puede iniciar un runbook en Azure automatización o llamar a un webhook que puede iniciar un proceso externo.  Ambas de estas respuestas pueden incluir detalles de la alerta incluidos los datos devueltos en la búsqueda de registros.

- [Alertas de análisis de registro](../log-analytics/log-analytics-alerts.md)
- [API de alerta de análisis de registro](../log-analytics/log-analytics-api-alerts.md)


## <a name="backup-and-site-recovery"></a>Copia de seguridad y recuperación de sitio

Azure copia de seguridad y recuperación de sitios proporcionan servicios para proteger los datos empresariales y garantizar la disponibilidad de los servidores y aplicaciones.  Puede aprovechar estos servicios para llevar a cabo escenarios como proporciona servicios de copia de seguridad para la aplicación o iniciar una migración tras error de una máquina virtual.

- [Cmdlets de copia de seguridad de Azure](https://msdn.microsoft.com/library/mt619253.aspx)
- [Recuperación de sitio de Azure API de REST](https://msdn.microsoft.com/library/azure/mt750497.aspx)
- [Cmdlets de recuperación de sitios de Azure](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Soluciones personalizadas

Puede encapsulados lógica de integración en una solución personalizada para ejecutar en el área de trabajo o en el área de trabajo de un cliente.  La solución puede incluir cualquiera de los métodos de integración en este artículo, además de otros recursos para proporcionar un escenario de administración completa.  Están incluidos en los recursos en la solución de manera que cuando se quita la solución, todos los recursos que creó se quitan de área de trabajo OMS y suscripción de Azure.

Por ejemplo, la solución que podría incluir un runbook de automatización para recopilar y procesar datos y, a continuación, rellenar el repositorio de análisis de registro con la API de selector de datos de HTTP.  También puede incluir una vista personalizada que presenta y analiza los datos recopilados.  

- Creación de soluciones personalizadas (próximamente)    

## <a name="next-steps"></a>Pasos siguientes
- El [SDK de OMS](operations-management-suite-sdk.md) para obtener información técnica sobre la automatización de servicios OMS de referencia.  
