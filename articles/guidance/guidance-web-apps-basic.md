<properties
   pageTitle="Aplicación web básica | Arquitectura de referencia de Azure | Microsoft Azure"
   description="Arquitectura recomendada para una aplicación web básica que se ejecuta en Microsoft Azure."
   services="app-service,app-service\web,sql-database"
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="mwasson"/>

# <a name="azure-reference-architecture-basic-web-application"></a>Arquitectura de referencia de Azure: aplicación web básica

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

En este artículo, se muestra una arquitectura recomendada para una aplicación web básica en Microsoft Azure. La arquitectura implementa un web front-end mediante el [Servicio de aplicación de Azure][app-service], con la [Base de datos de SQL Azure] [ sql-db] en la base de datos. Posteriores artículos de esta serie se basan en esta arquitectura básica, agregar componentes como caché y CDN. 

> [AZURE.NOTE] En este artículo no se centra en el desarrollo de aplicaciones y no supone que cualquier marco de aplicación en particular. En su lugar, el objetivo es comprender cómo encajan los diversos servicios de Azure dentro de la arquitectura de la aplicación.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

![[0]][0]

La arquitectura tiene los siguientes componentes:

- **Grupo de recursos**. Un [grupo de recursos] [ resource-group] es un contenedor lógico para los recursos de Azure. 

- **Aplicación de servicio de aplicación**. [Servicio de aplicaciones de Azure] [ app-service] es una plataforma totalmente administrada para crear e implementar aplicaciones de la nube.     

- **Plan de servicios de aplicación**. Un [plan de servicios de aplicación] [ app-service-plans] proporciona las máquinas virtuales administradas (VM) que hospeda la aplicación. Todas las aplicaciones asociadas con un plan de ejecutan en las mismas instancias de máquina virtual. 

- **Ranuras de implementación.**  Una [franja de implementación] [ deployment-slots] le permite una implementación de la fase y, a continuación, se intercambiar con la implementación de producción. De este modo, se evita implementar directamente en producción. Consulte la sección [administración](#manageability-considerations) de recomendaciones específicas.   

- **Dirección IP.** La aplicación de servicio de aplicación tiene una dirección IP pública y un nombre de dominio. El nombre de dominio es un subdominio de `azurewebsites.net`, como por ejemplo `contoso.azurewebsites.net`. Usar un nombre de dominio personalizado, como por ejemplo `contoso.com`, crear registros DNS que asignan el nombre de dominio personalizado a la dirección IP. Para obtener más información, consulte [configurar un nombre de dominio personalizado en la aplicación de servicio de Azure][custom-domain-name].

- **Base de datos SQL azure**. [Base de datos SQL] [ sql-db] es una relacional base de datos como-servicio en la nube. 

- **Servidor lógico.** En la base de datos de SQL Azure, un servidor lógico aloja las bases de datos. Puede crear varias bases de datos por servidor lógico. 

- **Almacenamiento de Azure.** Crear una cuenta de almacenamiento de Azure con un contenedor de blobs para almacenar registros de diagnóstico. 

- **Azure Active Directory** (Azure AD). Usar Azure AD u otro proveedor de identidades para la autenticación.

## <a name="recommendations"></a>Recomendaciones

### <a name="app-service-plan"></a>Plan de servicios de aplicación

Use los niveles estándar o Premium, pues admiten escalado, Autoescala y SSL, entre otras características. Cada nivel admite varios *tamaños de instancia*, que se diferencian por el número de núcleos y memoria. Puede cambiar el nivel o el tamaño de la instancia después de crear un plan. Para obtener más información acerca de los planes de servicio de aplicación, vea [Aplicación de servicio de precios][app-service-plans-tiers].

Le cobrarán para las instancias en el plan de servicios de aplicación, incluso si se detiene la aplicación. Asegúrese de eliminar los planes que no está usando (por ejemplo, distribuciones de prueba).

### <a name="sql-database"></a>Base de datos SQL

Usar la [versión de V12] [ sql-db-v12] de base de datos de SQL. Base de datos de SQL admite básico, estándar y Premium [niveles de servicio][sql-db-service-tiers], con varios niveles de rendimiento en cada nivel, medida en [Unidades de transacción de base de datos (DTUs)][sql-dtu]. Planear la capacidad y elija un nivel de rendimiento y los niveles que cumpla los requisitos.

### <a name="region"></a>Región

Aprovisionar el plan de servicio de aplicaciones y la base de datos de SQL en la misma región, para reducir la latencia de red. En general, elija una región más cercana a los usuarios. 

El grupo de recursos también tiene un área, que especifica donde se almacenan los metadatos de implementación. Coloque el grupo de recursos y sus recursos en la misma región. Esto puede mejorar la disponibilidad durante la implementación, si hay un problema en algunos centros de datos de Azure.  


## <a name="scalability-considerations"></a>Consideraciones de escalabilidad

### <a name="scaling-the-app-service-app"></a>Ajuste de escala de la aplicación de servicio de aplicaciones

Hay dos formas de ajustar el tamaño de una aplicación de servicio de aplicaciones:

- *Escalar*, lo que significa cambiar el tamaño de la instancia. El tamaño de la instancia determina la memoria, el número de núcleos y almacenamiento en cada instancia de máquina virtual. Puede escalar manualmente cambiando el tamaño de la instancia o el nivel de plan.  

- *Escala horizontal*, lo que significa agregar instancias para manejar el aumento de carga. Cada nivel de precios tiene un número máximo de instancias. Puede escalar manualmente cambiando el recuento de instancia, o usar [ajuste automático] [ web-app-autoscale] tener Azure automáticamente Agregar o quitar instancias, en función de una métrica de rendimiento o la programación.

    - Un perfil de Autoescala define el número máximo y mínimo de instancias. Pueden programar perfiles. Por ejemplo, puede crear perfiles distintos para los días laborables y los fines de semana. 

    - Si lo desea, un perfil tiene reglas para cuando para agregar o quitar instancias, dentro del rango mínimo y mazimum definida por el perfil. Por ejemplo: agregar dos instancias si es de uso de CPU por encima del 70% durante 5 minutos. Cada operación de escala sucede rápidamente &mdash; normalmente en segundos.
    
    - Autoescala reglas incluyen *un período, que es el intervalo que esperar después de una acción de escala antes de realizar otras acciones de escala* . El período permite que el sistema se estabilice antes de la escala de nuevo.
 
Estas son nuestras recomendaciones para escalar una aplicación web:

- Tanto como sea posible, evite escalar hacia arriba y abajo, porque puede desencadenar reinicio de la aplicación. Seleccione un nivel y el tamaño que cumplen los requisitos de rendimiento en carga típica y, a continuación, escalar las instancias para controlar los cambios en el volumen de tráfico.    

- Habilitar el ajuste automático. Si su aplicación tiene una carga de trabajo normal, predecible, cree perfiles para programar los recuentos de instancia antes de tiempo. Si la carga de trabajo no predecible, use el ajuste automático basado en reglas ante los cambios en la carga según se van produciendo. Puede combinar ambos métodos. 

- La CPU suele ser una buena métrica para Autoescala reglas. Sin embargo, debe cargar probar la aplicación, identificar posible botella y basar las reglas Autoescala en esos datos.  

- Establecer un período más corto para agregar instancias y un ya período para quitar instancias. Por ejemplo, establecer 5 minutos para agregar una instancia, pero 60 minutos para quitar una instancia. En carga rápida, es mejor para agregar nuevas instancias rápidamente manejar el tráfico y gradualmente volver escalar.
    
### <a name="scaling-sql-database"></a>Ajuste de escala de base de datos SQL

Si necesita un nivel de rendimiento o nivel de servicio superior para base de datos de SQL, escalar bases de datos individuales, sin tiempo de inactividad de la aplicación. Para obtener más información, vea [cambiar el nivel de rendimiento y niveles de servicio de una base de datos SQL][sql-db-scale]. 

## <a name="availability-considerations"></a>Consideraciones de disponibilidad

En el momento de escritura, el SLA para la aplicación de servicio es 99,95% y el SLA de base de datos de SQL es 99,99% niveles básico, estándar y Premium. 

> [AZURE.NOTE] La aplicación de servicio SLA se aplica a una o varias instancias.  

### <a name="backups"></a>Realizar copias de seguridad

Base de datos SQL proporciona restauración de punto en el tiempo y geo restaurar. Estas características están disponibles en todos los niveles y se habilitan automáticamente. No es necesario programar o administrar las copias de seguridad. 

- Utilice la restauración de punto en el tiempo para [recuperar de los errores humanos][sql-human-error]. Devuelve la base de datos en un momento anterior en el tiempo.

- Utilice geo restaurar para [recuperar de una interrupción del servicio][sql-outage-recovery]. Restaurar una base de datos desde una copia de seguridad geo redundantes.

Para obtener más información sobre estas opciones, consulte [continuidad empresarial de nube y recuperación de base de datos con la base de datos SQL][sql-backup]. 

Aplicación de servicio proporciona una [copia de seguridad y restauración] [ web-app-backup] característica. Sin embargo, tenga en cuenta que los archivos de copia de seguridad incluyen la configuración de la aplicación en texto sin formato. Éstos pueden incluir información confidencial, como cadenas de conexión. Evitar el uso de la característica de copia de seguridad de servicio de aplicaciones para realizar copias de seguridad de las bases de datos SQL, porque exporta la base de datos a un archivo de .bacpac SQL, que utiliza [DTUs][sql-dtu]. En su lugar, use la restauración de punto en el tiempo de base de datos de SQL, descrito anteriormente. 

## <a name="manageability-considerations"></a>Consideraciones de capacidad de administración

Crear grupos de recursos independientes de producción, desarrollo y entornos de prueba. Facilita administrar implementaciones, eliminar distribuciones de prueba y asignar derechos de acceso.

Cuando se asignan recursos a grupos de recursos, considere lo siguiente:

- Ciclo de vida. En general, coloque los recursos con el mismo ciclo de vida en el mismo grupo de recursos. 

- Acceso. Puede usar [el control de acceso basado en roles] [ rbac] (RBAC) para aplicar las directivas de acceso a los recursos en un grupo.

- Facturación. Puede ver los costes para el un grupo de recursos.  

Para obtener más información, vea [información general del Administrador de recursos de Azure][resource-group].

### <a name="deployment"></a>Implementación

Implementación consta de dos pasos:

- Aprovisionamiento de los recursos de Azure. Le recomendamos que use [el Administrador de recursos de Azure plantillas] [ arm-template] para este paso. Plantillas que sea más fácil automatizar las implementaciones a través de PowerShell o la CLI de Azure. 

- Implementación de la aplicación (código, archivos binarios y archivos de contenido). Tiene varias opciones, incluidas implementar desde un repositorio de Git local con Visual Studio o implementación continua de control de código fuente en la nube. Vea [implementar su aplicación de servicio de aplicaciones de Azure][deploy].  
 
Una aplicación de servicio de aplicación siempre tiene una franja de implementación denominado `production`, que representa el sitio de producción. Recomendamos crear un espacio provisional para implementar actualizaciones. Las ventajas de usar un espacio provisional incluyen:

- Puede comprobar la implementación correcta, antes de intercambiar en producción.

- Con un espacio provisional implementación asegura que todas las instancias se caliente antes de que se ha intercambiado en producción. Muchas aplicaciones tienen una preparación significativa y la hora de inicio de fría. 

También se recomienda crear un tercer espacio para mantener la implementación de la última configuración buena conocida. Después de intercambiar ensayo y producción, mueva la implementación de producción anterior (que se encuentra ahora en ensayo) en la franja de última buena conocida. De este modo, si detecta un problema más adelante, puede volver rápidamente a la versión de la última configuración buena conocida. 

![[1]][1]

Si volver a una versión anterior, asegúrese de que los cambios de esquema de la base de datos son compatibles con versiones anteriores.

No use ranuras en la implementación de producción para probar, puesto que todas las aplicaciones en el mismo plan de servicio de aplicación comparten las mismas instancias de máquina virtual. Por ejemplo, las pruebas de carga ralentice el sitio de producción. En su lugar, cree los planes de servicio de aplicación independientes de prueba y producción. Si coloca implementaciones de prueba en un plan independiente, aislar ellos desde la versión de producción. 

### <a name="configuration"></a>Configuración

Almacenar opciones de configuración como la [configuración de la aplicación][app-settings]. Definir la configuración de la aplicación en las plantillas de administrador de recursos, o mediante PowerShell. En tiempo de ejecución, configuración de la aplicación está disponibles para la aplicación como variables de entorno. 

Nunca proteger contraseñas, las teclas de acceso o cadenas de conexión en control de código fuente. En su lugar, pasar estos elementos como parámetros a un script de implementación que almacena estos valores como la configuración de la aplicación. 

Cuando intercambiar una franja de implementación, la configuración de la aplicación se cambian de forma predeterminada. Si necesita diferentes configuraciones de producción y ensayo, puede crear configuración de la aplicación que "limitarse" a un espacio y no suspender. 

### <a name="diagnostics-and-monitoring"></a>Diagnósticos y supervisión

Habilitar el [registro de diagnóstico][diagnostic-logs], incluido el registro de aplicación y el registro del servidor web. Configurar el registro para utilizar el almacenamiento de blobs de Windows. Por motivos de rendimiento, cree una cuenta de almacenamiento independiente para los registros de diagnóstico. No use la misma cuenta de almacenamiento para los registros y los datos de la aplicación. Para obtener orientación sobre el registro de información más detallada, consulte la [Guía de supervisión y diagnóstico][monitoring-guidance]. 

Usar un servicio como [Relic nueva] [ new-relic] o [Aplicación perspectivas] [ app-insights] para supervisar el rendimiento de aplicación y el comportamiento de carga. Tenga en cuenta los [límites de velocidad de datos] [ app-insights-data-rate] para impresiones de aplicación.

Realizar pruebas de carga, usar una herramienta como [Visual Studio Team Services][vsts]. Para obtener una descripción general de análisis de rendimiento en las aplicaciones de la nube, vea [Información básica sobre el análisis de rendimiento][perf-analysis].

Sugerencias para solucionar problemas de la aplicación:

- Use el [módulo de solución de problemas] [ troubleshoot-blade] en el portal de Azure para soluciones de identidad a problemas comunes.

- Habilitar el [registro de transmisión] [ web-app-log-stream] para ver información de registro en prácticamente en tiempo real. 

- El [panel de Kudu] [ kudu] tiene varias herramientas de seguimiento y depuración de la aplicación. Para obtener más información, vea [Herramientas en línea de sitios Web de Azure que debe conocer] [ kudu] (entrada de blog). Puede ponerse en el panel de Kudu desde el Portal de Azure. Abra el módulo de la aplicación y haga clic en **Herramientas**, haga clic en **Kudu**.

- Si utiliza Visual Studio, vea el artículo [solucionar problemas de una aplicación web en la aplicación de servicio de Azure mediante Visual Studio] [ troubleshoot-web-app] para depurar y consejos para solucionar problemas.


## <a name="security-considerations"></a>Consideraciones de seguridad

> [AZURE.NOTE] Esta sección señala algunas consideraciones de seguridad que son específicos de los servicios de Azure descritos en este artículo. No es una lista completa de los procedimientos recomendados de seguridad. Para algunas consideraciones de seguridad adicionales, vea [proteger una aplicación de servicio de la aplicación de Azure][app-service-security].

**Auditoría de la base de datos de SQL**. Auditoría puede ayudarle a mantener el cumplimiento de reglamentaciones y obtener una visión de discrepancias y anomalías que podrían indicar cuestiones empresariales o sospechosos violaciones de seguridad. Consulte [Introducción a la auditoría de la base de datos SQL][sql-audit].

**Ranuras de implementación**. Cada franja de implementación tiene una dirección IP pública. Proteger las ranuras no es de producción mediante [el inicio de sesión de Azure Active Directory][aad-auth], de modo que solo los miembros de sus equipos de DevOps y desarrollo pueden llegar a esos extremos. 

El **registro**. Nunca deben registrar registros contraseñas de usuario u otra información que puede utilizarse para cometer fraude de identidad. Arrastre los detalles de los datos antes de almacenarlos.   

**SSL**. Una aplicación de servicio de aplicación incluye un extremo SSL en un subdominio del `azurewebsites.net` sin costo adicional, con un certificado de caracteres comodín para la `*.azurewebsites.net` dominio.     Si usa un nombre de dominio personalizado, debe proporcionar un certificado que coincida con el dominio personalizado. El enfoque más sencillo es comprar un certificado directamente a través del Portal de Azure. También puede importar certificados de entidades emisoras de certificados. Para obtener más información, vea [comprar y configurar un certificado SSL para el servicio de aplicación de Azure][ssl-cert]. 

Como práctica recomendada de seguridad, la aplicación debería exigir HTTPS, al redirigir las solicitudes HTTP. Puede implementar esta dentro de la aplicación, o usar una regla de reescritura de URL como se describe en [Habilitar HTTPS para una aplicación de servicio de la aplicación de Azure][ssl-redirect].

### <a name="authentication"></a>Autenticación

Se recomienda autenticar a través de un proveedor de identidades (IDP), como Azure AD, Facebook, Google, o Twitter, usando OAuth flujo 2 o OpenID conectar (OIDC) para la autenticación. 

Azure AD le ofrece la capacidad de administrar usuarios y grupos, crear funciones de aplicación, integrar las identidades locales y consumir servicios de back-end, como Office 365 y Skype para la empresa.

Evitar la aplicación administrar credenciales e inicios de sesión de usuario directamente, como crea una gran superficie de ataque potencial. Como mínimo, necesitará tener correo electrónico de confirmación, recuperación de contraseña y la autenticación multifactor; validar la seguridad de la contraseña; y almacena hash de contraseña segura. Los proveedores de identidades grande controlar todas esas cosas por usted y constantemente son supervisión y mejorar sus prácticas de seguridad. 

Considere la posibilidad de usar la [Aplicación de servicio de autenticación] [ app-service-auth] para implementar el flujo de autenticación de OAuth/OIDC. Las ventajas de la aplicación de servicio de autenticación incluyen:

- Fácil de configurar.
    
- Escenarios de autenticación simple, no es necesario código.
    
- Es compatible con delegados autorización; es decir, usando tokens de acceso OAuth consumo de recursos en nombre del usuario.
    
- Proporciona una caché de token integrada.

Algunas limitaciones de la aplicación de servicio de autenticación:  

- Opciones de personalización limitada. 

- Autorización delegados está restringido a un recurso de back-end por inicio de sesión.
    
- Si usa más de un IDP, no hay ningún mecanismo integrado para detección de dominio de inicio.

- Escenarios de varios inquilinos, la aplicación debe implementar la lógica para validar al emisor de tokens.



## <a name="deploying-the-sample-solution"></a>Implementar la solución de ejemplo

Un ejemplo de plantilla de administrador de recursos para esta arquitectura está disponible en GitHub. Descargarlo [aquí][paas-basic-arm-template].

Para implementar la plantilla con PowerShell, ejecute los comandos siguientes:

```
New-AzureRmResourceGroup -Name <resource-group-name> -Location "West US"

$parameters = @{"appName"="<app-name>";"environment"="dev";"locationShort"="uw";"databaseName"="app-db";"administratorLogin"="<admin>";"administratorLoginPassword"="<password>"}
    
New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile .\PaaS-Basic.json -TemplateParameterObject  $parameters
```

Para obtener más información, vea [los recursos de implementación con las plantillas de administrador de recursos de Azure][deploy-arm-template].


## <a name="next-steps"></a>Pasos siguientes

- Puede mejorar el rendimiento y escalabilidad agregando características como almacenamiento en caché, CDN y procesamiento de tareas de ejecución larga en segundo plano. Consulte [la aplicación Web con escalabilidad mejorada](guidance-web-apps-scalability.md).

<!-- links -->

[aad-auth]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[app-insights]: ../application-insights/app-insights-overview.md
[app-insights-data-rate]: ../application-insights/app-insights-pricing.md
[app-service]: https://azure.microsoft.com/en-us/documentation/services/app-service/
[app-service-auth]: ../app-service-api/app-service-api-authentication.md
[app-service-plans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[app-service-plans-tiers]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[app-service-security]: ../app-service-web/web-sites-security.md
[app-settings]: ../app-service-web/web-sites-configure.md
[arm-template]: ../resource-group-overview.md
[custom-domain-name]: ../app-service-web/web-sites-custom-domain-name.md
[deploy]: ../app-service-web/web-sites-deploy.md
[deploy-arm-template]: ../resource-group-template-deploy.md
[deployment-slots]: ../app-service-web/web-sites-staged-publishing.md
[diagnostic-logs]: ../app-service-web/web-sites-enable-diagnostic-log.md
[kudu]: https://azure.microsoft.com/en-us/blog/windows-azure-websites-online-tools-you-should-know-about/
[monitoring-guidance]: ../best-practices-monitoring.md
[new-relic]: http://newrelic.com/
[paas-basic-arm-template]: https://github.com/mspnp/blueprints/tree/master/paas-basic/Paas-Basic/Templates
[perf-analysis]: https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md
[rbac]: ../active-directory/role-based-access-control-what-is.md
[resource-group]: ../resource-group-overview.md
[sla]: https://azure.microsoft.com/en-us/support/legal/sla/
[sql-audit]: ../sql-database/sql-database-auditing-get-started.md
[sql-backup]: ../sql-database/sql-database-business-continuity.md
[sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[sql-db-scale]: ../sql-database/sql-database-scale-up-powershell.md
[sql-db-service-tiers]: ../sql-database/sql-database-service-tiers.md
[sql-db-v12]: ../sql-database/sql-database-v12-whats-new.md
[sql-dtu]: ../sql-database/sql-database-service-tiers.md#understanding-dtus
[sql-human-error]: ../sql-database/sql-database-business-continuity.md#recover-a-database-after-a-user-or-application-error
[sql-outage-recovery]: ../sql-database/sql-database-business-continuity.md#recover-a-database-to-another-region-from-an-azure-regional-data-center-outage
[ssl-redirect]: ../app-service-web/web-sites-configure-ssl-certificate.md#4-enforce-https-on-your-app
[sql-resource-limits]: ../sql-database/sql-database-resource-limits.md
[ssl-cert]: ../app-service-web/web-sites-purchase-ssl-web-site.md
[troubleshoot-blade]: https://azure.microsoft.com/en-us/updates/self-service-troubleshooting-for-app-service-web-apps-customers/
[troubleshoot-web-app]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[vsts]: https://www.visualstudio.com/en-us/features/vso-cloud-load-testing-vs.aspx
[web-app-autoscale]: ../app-service-web/web-sites-scale.md#scaling-to-standard-or-premium-mode
[web-app-backup]: ../app-service-web/web-sites-backup.md
[web-app-log-stream]: ../app-service-web/web-sites-enable-diagnostic-log.md#streamlogs
[0]: ./media/blueprints/paas-basic-web-app.png "Arquitectura de una aplicación web de Azure básico"
[1]: ./media/blueprints/paas-basic-web-app-staging-slots.png "Intercambiar ranuras para producción e implementaciones de ensayo"