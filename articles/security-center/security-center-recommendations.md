<properties
   pageTitle="Administrar las recomendaciones de seguridad en el centro de seguridad de Azure | Microsoft Azure"
   description="Este documento le guiará por cómo recomendaciones en Centro de seguridad de Azure ayudar a proteger los recursos de Azure y mantenerse cumple con las directivas de seguridad."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# <a name="managing-security-recommendations-in-azure-security-center"></a>Administrar las recomendaciones de seguridad en el centro de seguridad de Azure

Este documento explica cómo usar recomendaciones en Centro de seguridad de Azure para ayudar a proteger los recursos de Azure.

> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo.  No es una guía paso a paso.

## <a name="what-are-security-recommendations"></a>¿Cuáles son las recomendaciones de seguridad?
Centro de seguridad periódicamente analiza el estado de seguridad de los recursos de Azure. Cuando el centro de seguridad se identifica posibles vulnerabilidad de seguridad, crea recomendaciones. Las recomendaciones le guiará a través del proceso de configuración de los controles necesarios.

## <a name="implementing-security-recommendations"></a>Implementación de recomendaciones de seguridad

### <a name="set-recommendations"></a>Conjunto de recomendaciones

En [configuración de directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md), aprenda a:

- Configurar directivas de seguridad.
- Activar la recopilación de datos.
- Elija qué recomendaciones para ver como parte de la directiva de seguridad.

Directiva recomendaciones centro actual alrededor de las actualizaciones del sistema, reglas de línea base, programas antimalware, [grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md) en subredes y interfaces de red, auditoría de base de datos SQL, cifrado de datos transparente de base de datos SQL y los firewalls de aplicación web.  [Configurar directivas de seguridad](security-center-policies.md) , se proporciona una descripción de cada opción de recomendación.

### <a name="monitor-recommendations"></a>Monitor recomendaciones
Después de establecer una directiva de seguridad, el centro de seguridad analiza el estado de seguridad de los recursos para identificar los puntos débiles potenciales. El mosaico de **recomendaciones** en el módulo de **Centro de seguridad** le permite saber el número total de recomendaciones identificada por el centro de seguridad.

![Mosaico de recomendaciones][1]

Para ver los detalles de cada recomendación:

1. Haga clic en el **mosaico de recomendaciones** en el módulo de **Centro de seguridad** . Se abre el módulo de **recomendaciones** .

Las recomendaciones se muestran en un formato de tabla, donde cada línea representa una recomendación determinada. Las columnas de esta tabla son:

- **Descripción**: se explica la recomendación y qué debe hacerse para solucionarlo.
- **Recursos**: enumera los recursos a los que se aplica esta recomendación.
- **Estado**: describe el estado actual de la recomendación:
    - **Abrir**: la recomendación todavía no ha tratado todavía.
    - **En curso**: la recomendación actualmente se aplica a los recursos y, a continuación, se requiere ninguna acción por el usuario.
    - **Resuelto**: ya se ha completado la recomendación (en este caso, la línea estará atenuada).
- **Gravedad**: describe la gravedad de esa recomendación particular:
    - **Alto**: una vulnerabilidad existe con un recurso significativo (por ejemplo, una aplicación, una máquina virtual o un grupo de seguridad de la red) y necesita atención.
    - **Medio**: existe una vulnerabilidad y no críticos o adicionales es necesario para eliminarla o para completar un proceso.
    - **Bajo**: existe una vulnerabilidad que debe solucionarse pero no requiere atención inmediata. (De forma predeterminada, no se presentan recomendaciones bajos, pero puede filtrar recomendaciones bajos si desea verlos.)

Utilice la siguiente tabla como referencia para ayudarle a entender las recomendaciones disponibles y lo que cada uno de ellos hará si se aplica.

> [AZURE.NOTE] Desea conocer la [clásica y modelos de implementación de administrador de recursos](../azure-classic-rm.md) para los recursos de Azure.

|Recomendación|Descripción|
|-----|-----|
|[Habilitar la recopilación de datos para suscripciones](security-center-enable-data-collection.md)|Se recomienda activar la recopilación de datos en la directiva de seguridad para cada una de las suscripciones y todos los equipos virtuales (VM) en las suscripciones.|
|[Corregir la vulnerabilidad OS](security-center-remediate-os-vulnerabilities.md)|Recomienda alinear las configuraciones de sistema operativo con las reglas de la configuración recomendada, por ejemplo, no permitir guardar contraseñas.|
|[Aplicar actualizaciones del sistema](security-center-apply-system-updates.md)|Se recomienda implementar la seguridad del sistema que faltan y las actualizaciones críticas en máquinas virtuales.|
|[Reiniciar después de las actualizaciones del sistema](security-center-apply-system-updates.md#reboot-after-system-updates)|Se recomienda reiniciar una máquina virtual para completar el proceso de aplicar actualizaciones del sistema.|
|[Agregar un servidor de aplicaciones web](security-center-add-web-application-firewall.md)|Se recomienda implementar un servidor de aplicaciones web (WAFS) para los extremos de la web. Puede proteger varias aplicaciones web en el centro de seguridad agregando estas aplicaciones para las implementaciones WAFS existentes. WAFS (creados con el modelo de implementación de administrador de recursos) tendrán que implementará en una red virtual independiente. Dispositivos WAFS (creados con el modelo de implementación clásico) están limitados a usar un grupo de seguridad de la red. Esta compatibilidad se extiende a una implementación completamente personalizada de un dispositivo WAFS (clásico) en el futuro. Centro de seguridad se recomienda que incluya un WAFS para ayudar a defender contra ataques dirige a sus aplicaciones web en máquinas virtuales y en el entorno de servicio de aplicación (ASE). Para obtener más información sobre ASE, consulte la [Documentación del entorno de aplicación de servicio](../app-service/app-service-app-service-environments-readme.md). |
|[Finalizar la protección de la aplicación](security-center-add-web-application-firewall.md#finalize-application-protection)|Para completar la configuración de un WAFS, el tráfico debe se transfiere al dispositivo WAFS. Seguir esta recomendación completarán los cambios necesarios de instalación.|
|[Agregue un Firewall de generación siguiente](security-center-add-next-generation-firewall.md)|Se recomienda agregar un Firewall de siguiente generación (NGFW) de un asociado de Microsoft para aumentar la protección de la seguridad.|
|[Enrutar el tráfico a través de NGFW solo](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Se recomienda configurar reglas de grupo (GSN) de seguridad de red que forzar el tráfico a su máquina virtual a través de su NGFW.|
|[Instalar Endpoint Protection](security-center-install-endpoint-protection.md)|Se recomienda que incluya los programas antimalware VM (sólo VM de Windows).|
|[Resolver las alertas de estado de Endpoint Protection](security-center-resolve-endpoint-protection-health-alerts.md)|Se recomienda que resolver errores de protección de extremo.|
|[Habilitar a grupos de seguridad de red en subredes o máquinas virtuales de Windows](security-center-enable-network-security-groups.md)|Se recomienda que habilite NSGs en subredes o máquinas virtuales.|
|[Restringir el acceso a través de Internet opuestas extremo](security-center-restrict-access-through-internet-facing-endpoints.md)|Se recomienda que configurar reglas de tráfico entrante para NSGs.|
|[Habilitar el servidor de auditoría de SQL](security-center-enable-auditing-on-sql-servers.md)|Se recomienda activar la auditoría en los servidores de SQL Azure (servicio SQL Azure; no incluye ejecutando en sus máquinas virtuales SQL).|
|[Habilitar la auditoría de SQL de la base de datos](security-center-enable-auditing-on-sql-databases.md)|Se recomienda activar la auditoría en bases de datos de SQL Azure (servicio SQL Azure; no incluye ejecutando en sus máquinas virtuales SQL).|
|[Habilitar el cifrado de datos transparente en bases de datos SQL](security-center-enable-transparent-data-encryption.md)|Se recomienda habilitar el cifrado de bases de datos SQL (solo en el servicio SQL Azure).|
|[Habilitar el agente VM](security-center-enable-vm-agent.md)|Le permite ver qué máquinas virtuales requieren el agente de máquina virtual. El agente de VM debe instalarse en máquinas virtuales para aprovisionar de detección, análisis de línea base y programas antimalware de revisiones. El agente de VM se instala de forma predeterminada para máquinas virtuales que se implementan de Azure Marketplace. El artículo [VM agente y extensiones: parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) proporciona información sobre cómo instalar al agente de máquina virtual.|
| [Aplicar el cifrado de disco](security-center-apply-disk-encryption.md) |Se recomienda que cifre los discos VM mediante el cifrado de disco de Azure (Windows y máquinas virtuales de Linux). Cifrado se recomienda para volúmenes de sistema operativo y datos en la máquina virtual.|
|[Proporcionar información de contacto de seguridad](security-center-provide-security-contact-details.md) | Información de contacto de recomienda proporciona seguridad para cada uno de sus suscripciones. Información de contacto es un número de teléfono y dirección de correo electrónico. La información se utilizará en contacto con usted en caso de nuestro equipo de seguridad que están en el peligro de los recursos. |
| [Actualizar la versión del SO](security-center-update-os-version.md) | Se recomienda que actualice la versión del sistema operativo (SO) de su servicio de nube a la versión más reciente disponible para su familia OS.  Para obtener más información acerca de los servicios en la nube, vea la [información general sobre servicios de nube](../cloud-services/cloud-services-choose-me.md). |
| [Evaluación de vulnerabilidad no instalada](security-center-vulnerability-assessment-recommendations.md) | Recomienda instalar una solución de evaluación de vulnerabilidad en la máquina virtual. |
| [Corregir la vulnerabilidad](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Permite ver vulnerabilidad de sistema y la aplicación detecta la solución de evaluación de vulnerabilidad instalada en su máquina virtual. |

Puede filtrar y descartar recomendaciones.

1. Haga clic en **filtro** en el módulo de **recomendaciones** . Se abre el módulo de **filtro** y seleccione los valores de gravedad y el estado que desea ver.

    ![Recomendaciones de filtro][2]

2. Si determina que no es aplicable de recomendación, puede descartar la recomendación y, a continuación, filtrarlos fuera de la vista. Hay dos formas para descartar una recomendación. Haga clic con el botón secundario del mouse en un elemento y, a continuación, seleccione **Descartar**es una forma. La otra es desplace el puntero sobre un elemento, haga clic en los suspensivos puntos que aparecen a la derecha y, a continuación, seleccione **Descartar**. Puede ver las recomendaciones descartadas haciendo clic en **filtro**y, a continuación, seleccione **Dismissed**.

    ![Cerrar la recomendación][3]

### <a name="apply-recommendations"></a>Aplicar recomendaciones
Después de revisar todas las recomendaciones, decida que uno debe aplicar en primer lugar. Le recomendamos que use la gravedad como primero se debe aplicar el parámetro principal para evaluar qué recomendaciones.

En la tabla de recomendaciones anteriores, seleccione una recomendación y recorra como un ejemplo de cómo aplicar una recomendación.

## <a name="see-also"></a>Vea también
En este documento, se han introducido recomendaciones de seguridad en el centro de seguridad. Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Supervisión en el centro de seguridad de Azure de estado de seguridad](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Supervisar las soluciones de asociados con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de Azure](security-center-faq.md) : preguntas más frecuentes sobre el uso del servicio de búsqueda.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : buscar entradas de blog sobre seguridad de Azure y cumplimiento.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
