<properties
   pageTitle="Preguntas más frecuentes (p+f sobre) el centro de seguridad de Azure | Microsoft Azure"
   description="Este artículo responde preguntas acerca del centro de seguridad de Azure."
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
   ms.date="10/27/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-frequently-asked-questions-faq"></a>Preguntas más frecuentes (p+f sobre) el centro de seguridad de Azure

Este artículo responde preguntas acerca del centro de seguridad de Azure, un servicio que le ayuda a evitar, detectar y responder a las amenazas con una mayor visibilidad y control sobre la seguridad de los recursos de Microsoft Azure.

## <a name="general-questions"></a>Preguntas generales

### <a name="what-is-azure-security-center"></a>¿Qué es el centro de seguridad de Azure?
Centro de seguridad de Azure le ayuda a evitar, detectar y responder a las amenazas con una mayor visibilidad y control sobre la seguridad de los recursos de Azure. Proporciona seguridad integrada supervisión y la directiva de administración de las suscripciones, ayuda a detecta amenazas que pueden incluirse en caso contrario, problemas y funciona con un amplio ecosistema de soluciones de seguridad.

### <a name="how-do-i-get-azure-security-center"></a>¿Cómo puedo obtener el centro de seguridad de Azure?
Centro de seguridad de Azure está habilitado con su suscripción a Microsoft Azure y tener acceso desde el [portal de Azure](https://azure.microsoft.com/features/azure-portal/). ([Inicie sesión en el portal](https://portal.azure.com), seleccione **Examinar**y desplácese al **Centro de seguridad**).  

## <a name="billing"></a>Facturación

### <a name="how-does-billing-work-for-azure-security-center"></a>¿Cómo funciona facturación para el centro de seguridad de Azure?
Centro de seguridad se ofrece en dos niveles: gratuitas y estándar.

El nivel gratuito le permite establecer directivas de seguridad y recibir alertas de seguridad, incidencias y recomendaciones que le guiarán a lo largo del proceso de configuración de controles necesarios. Con el nivel de libre también puede supervisar el estado de seguridad de los recursos de Azure y soluciones de socios integradas con su suscripción de Azure.

El nivel estándar proporciona las detecciones de características más avanzadas de nivel gratuita: amenazas inteligencia, análisis de comportamiento, análisis de bloqueo y detección de anomalías. Una prueba gratuita de 90 días de la capa estándar está disponible. Para actualizar, seleccione el nivel de precios en la [Directiva de seguridad](security-center-policies.md#setting-security-policies-for-subscriptions). Para obtener más información, consulte [Centro de seguridad de precios](security-center-pricing.md) .

## <a name="data-collection"></a>Recopilación de datos

Centro de seguridad recopila los datos de sus máquinas virtuales para evaluar su estado de seguridad, proporcionar recomendaciones de seguridad y alertarle sobre amenazas. Cuando acceda por primera vez el centro de seguridad, recopilación de datos está habilitada en todas las máquinas virtuales de su suscripción. Se recomienda la recopilación de datos, pero puede cambiar de opinión salida, [Deshabilitar la recopilación de datos](#how-do-i-disable-data-collection) en la directiva de centro de seguridad.

### <a name="how-do-i-disable-data-collection"></a>¿Cómo puedo deshabilitar la recopilación de datos?

Puede deshabilitar la **recopilación de datos** para una suscripción en la directiva de seguridad en cualquier momento. ([Inicio de sesión en el portal de Azure](https://portal.azure.com), seleccione **Examinar**, seleccione **Centro de seguridad**y seleccione **Directiva**.)  Seleccione una suscripción, un nuevo módulo se abre y proporciona la opción para desactivar la **recopilación de datos** . Seleccione la opción **Eliminar agentes** en la cinta de opciones superior para eliminar a agentes de los equipos virtuales existentes.

> [AZURE.NOTE] Directivas de seguridad se pueden establecer en el nivel de suscripción de Azure y un nivel de grupo de recursos, pero debe seleccionar una suscripción para desactivar la recopilación de datos.

### <a name="how-do-i-enable-data-collection"></a>¿Cómo habilitar la recopilación de datos?
Puede habilitar la recopilación de datos de sus suscripciones Azure en la directiva de seguridad. Para habilitar la recopilación de datos, [inicie sesión en el portal de Azure](https://portal.azure.com), seleccione **Examinar**, seleccione **Centro de seguridad**y seleccione la **Directiva**. **Recopilación de datos** a **en** y configurar las cuentas de almacenamiento donde desee que se recopilan a datos (vea la pregunta "[donde se almacenan mis datos?](#where-is-my-data-stored)"). Cuando se habilita la **recopilación de datos** , recopila automáticamente información de eventos y configuración de seguridad de todos los equipos virtuales admitidos en la suscripción.

> [AZURE.NOTE] Se pueden establecer directivas de seguridad en el nivel de suscripción de Azure y un nivel de grupo de recursos, pero configuración de recopilación de datos se produce en el nivel de suscripción solo.

### <a name="what-happens-when-data-collection-is-enabled"></a>¿Qué sucede cuando se habilita la recopilación de datos?
Recopilación de datos se habilita mediante el agente de supervisión de Azure y la extensión de supervisión de seguridad de Azure. La extensión de supervisión de seguridad de Azure busca diversos configuración relevantes de seguridad y lo envía a seguimientos de [Seguimiento de eventos de Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Además, el sistema operativo crea entradas de registro de eventos.  El agente de supervisión de Azure lee las entradas de registro de eventos y realiza un seguimiento de ETW y los copia a su cuenta de almacenamiento para el análisis.  Esta es la cuenta de almacenamiento configurados en la directiva de seguridad. Para obtener más información acerca de la cuenta de almacenamiento, consulte la pregunta "[donde se almacenan mis datos?](#where-is-my-data-stored)"

### <a name="does-the-monitoring-agent-or-security-monitoring-extension-impact-the-performance-of-my-servers"></a>¿Afecta a la extensión agente de supervisión o supervisión de seguridad el rendimiento de mi servidor?
El agente y la extensión consume un importe nominal de recursos del sistema y debería tener poco impacto en el rendimiento.

### <a name="where-is-my-data-stored"></a>¿Dónde se almacenan mis datos?
Para cada región en la que tiene máquinas virtuales que ejecutan, elija la cuenta de almacenamiento donde se almacenan los datos recopilados de esas máquinas virtuales. Esto facilita la mantener datos en la misma área geográfica con fines de soberanía de datos y privacidad. Elija la cuenta de almacenamiento para una suscripción en la directiva de seguridad. ([Inicio de sesión en el portal de Azure](https://portal.azure.com), seleccione **Examinar**, seleccione **Centro de seguridad**y seleccione **Directiva**.) Al hacer clic en una suscripción, se abrirá un nuevo módulo. Seleccione **cuentas de almacenamiento de elegir** para seleccionar una región.

> [AZURE.NOTE] Se pueden establecer directivas de seguridad en el nivel de suscripción de Azure y un nivel de grupo de recursos, pero seleccionar una región para su cuenta de almacenamiento se produce en el nivel de suscripción solo.

Para obtener más información sobre el almacenamiento de Azure y cuentas de almacenamiento, consulte [Documentación de almacenamiento](https://azure.microsoft.com/documentation/services/storage/) y [Azure sobre cuentas de almacenamiento](../storage/storage-create-storage-account.md).

## <a name="using-azure-security-center"></a>Mediante el centro de seguridad de Azure

### <a name="what-is-a-security-policy"></a>¿Qué es una directiva de seguridad?
Una directiva de seguridad define el conjunto de controles que se recomiendan para recursos dentro de la suscripción especificada o grupo de recursos. En el centro de seguridad de Azure, defina las directivas para suscripciones de Azure y grupos de recursos según los requisitos de seguridad de su empresa y el tipo de aplicaciones o confidencialidad de los datos de cada suscripción.

Por ejemplo, recursos utilizados para la prueba o desarrollo pueden tener distintos requisitos de seguridad de los empleados para aplicaciones de producción. Del mismo modo, las aplicaciones con datos regulados como PII (información de identificación personal) pueden requerir un nivel superior de seguridad. Las directivas de seguridad habilitadas en el centro de seguridad de Azure guiarán recomendaciones de seguridad y supervisión. Para obtener más información acerca de las directivas de seguridad, consulte [supervisión en el centro de seguridad de Azure de estado de seguridad](security-center-monitoring.md).

> [AZURE.NOTE] En caso de conflicto entre la directiva de nivel de suscripción y directivas de nivel de grupo de recursos, la directiva de nivel de grupo de recursos tiene prioridad.

### <a name="who-can-modify-a-security-policy"></a>¿Quién puede modificar una directiva de seguridad?
Directivas de seguridad se configuran para cada suscripción o grupo de recursos. Para modificar una directiva de seguridad en el nivel de la suscripción o el nivel de grupo de recursos, debe ser propietario o colaborador de suscripción.

Para obtener información sobre cómo configurar una directiva de seguridad, vea [configuración de directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>¿Qué es una recomendación de seguridad?
Centro de seguridad de Azure analiza el estado de seguridad de los recursos de Azure. Cuando se identifica potencial vulnerabilidad de seguridad, se crean las recomendaciones. Las recomendaciones le guiará a través del proceso de configurar el control necesario. Algunos ejemplos son:

- Aprovisionamiento de antimalware para ayudar a identificar y quitar software malintencionado
- Configuración de [Grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md) y reglas para controlar el tráfico a máquinas virtuales
- Aprovisionamiento de un servidor de aplicaciones web para ayudar a defender contra ataques dirige a las aplicaciones web
- Implementar las actualizaciones de sistema que faltan
- Escribir direcciones en las configuraciones de sistema operativo que no coinciden con las previsiones recomendadas

Aquí se muestran únicamente recomendaciones que estén habilitadas en directivas de seguridad.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>¿Cómo se puede ver el estado actual de seguridad de Mis recursos Azure?
Un icono de **estado de los recursos** en el módulo de **Centro de seguridad** muestra la posición de seguridad general de su entorno desglosado por máquinas virtuales de Windows, aplicaciones web y otros recursos. Cada recurso tiene un mostrando indicador si se han identificado ninguna vulnerabilidad de seguridad posibles. Haga clic en el icono de estado de recursos muestra los recursos e identifica donde se necesita atención o que exista problemas.

### <a name="what-triggers-a-security-alert"></a>¿Qué activa una alerta de seguridad?
Centro de seguridad de Azure recopila, analiza y fusibles datos del registro de los recursos de Azure, la red y soluciones de socios como antimalware y los firewalls. Cuando se detectan amenazas, se crea una alerta de seguridad. Detección de algunos ejemplos:

- Comprometido máquinas virtuales de Windows comunicarse con las direcciones IP malintencionadas conocidas
- Avanzadas malware detectado utilizando el informe de errores de Windows
- Ataques contra máquinas virtuales de Windows
- Alertas de seguridad de soluciones de seguridad integradas de socios como antimalware o los Firewalls de aplicación Web

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>¿Cuál es la diferencia entre amenazas detectado y avisa en Microsoft Security Response Center frente a centro de seguridad de Azure?
El centro de respuesta de seguridad de Microsoft (MSRC) realiza la supervisión de la red de Azure y la infraestructura de seguridad select y recibe quejas de inteligencia y abuso amenaza de terceros. Cuando se convierte en MSRC tenga en cuenta que se tiene acceso a los datos del cliente por personas no autorizadas o ilegal o que uso del cliente de Azure no cumple con los términos de uso aceptable, un administrador de incidencias de seguridad notifica al cliente. Notificación suele enviando un correo electrónico a los contactos de seguridad especificados en el centro de seguridad de Azure o el propietario de la suscripción de Azure si no se especifica un contacto de seguridad.

Centro de seguridad es un servicio de Azure que continuamente supervisa el entorno del cliente Azure y se aplica el análisis para detectar automáticamente una amplia gama de actividad potencialmente malintencionado. Estas detecciones aparecen como las alertas de seguridad en el panel Centro de seguridad.

### <a name="how-are-permissions-handled-in-azure-security-center"></a>¿Cómo se administran los permisos en el centro de seguridad de Azure?
Centro de seguridad de Azure es compatible con acceso basado en roles. Para obtener más información sobre el control de acceso basado en roles (RBAC) en Azure, consulte [Control de acceso basado en roles de Azure Active Directory](../active-directory/role-based-access-control-configure.md).

Cuando un usuario abre el centro de seguridad, sólo recomendaciones y se verá alertas relacionadas con el usuario tiene acceso a los recursos. Esto significa que los usuarios solo verán los elementos relacionados con recursos que el usuario se asigna el rol de propietario, Colaborador o lector a la suscripción o grupo de recursos que pertenece un recurso.

Si necesita:

- **Editar una directiva de seguridad**, debe ser propietario o colaborador de la suscripción.
- **Aplicar una recomendación**, debe ser propietario o colaborador de la suscripción.
- **Tiene visibilidad sobre el estado de seguridad a través de todas las suscripciones**, debe ser propietario, Colaborador o lector (Administrador de TI, grupo de seguridad) de cada suscripción.
- **Tiene visibilidad del estado de seguridad de los recursos**, debe ser un grupo de recursos propietario, Colaborador o lector (DevOps).

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>¿Los recursos que Azure supervisados por el centro de seguridad de Azure?
Centro de seguridad de Azure supervisa los siguientes recursos de Azure:

- Máquinas virtuales (VM) (incluidos los [Servicios de nube](../cloud-services/cloud-services-choose-me.md))
- Redes virtuales Azure
- Servicio SQL Azure
- Las soluciones de asociados integran con su suscripción de Azure como un firewall de aplicación web de máquinas virtuales y en el [Entorno de aplicación de servicio](../app-service/app-service-app-service-environments-readme.md)

## <a name="virtual-machines"></a>Máquinas virtuales de Windows

### <a name="what-types-of-virtual-machines-will-be-supported"></a>¿Qué tipos de máquinas virtuales serán compatibles?
Supervisión de estado de seguridad y recomendaciones están disponibles para máquinas virtuales (VM) creadas con ambos [estándar y los modelos de implementación de administrador de recursos](../azure-classic-rm.md).

Máquinas virtuales de Windows compatibles:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

Máquinas virtuales de Linux compatibles:

- Versiones de Ubuntu 12.04, 14.04, 16.04
- Debian versiones 7, 8
- CentOS versiones 6. \*, 7.*
- Versiones de red sombrero Enterprise Linux (RHEL) 6. \*, 7.*
- SUSE Linux Enterprise Server (SLES) versiones 11. \*, 12.*
- Versiones de Oracle Linux 6. \*, 7.*

Máquinas virtuales que se ejecuta en un servicio de nube también son compatibles. Funciones de web y trabajador en producción supervisadas ranuras de servicios de nube solo. Para obtener más información sobre el servicio de nube, consulte [Introducción a servicios en la nube](../cloud-services/cloud-services-choose-me.md).

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>¿Por qué no reconoce el centro de seguridad de Azure la solución antimalware que se ejecuta en mi máquina virtual de Azure?

Centro de seguridad de Azure sólo tiene visibilidad antimalware instalado mediante las extensiones de Azure. Por ejemplo, el centro de seguridad no es capaz de detectar antimalware que estaba preinstalado en una imagen que ha proporcionado o si ha instalado antimalware en sus máquinas virtuales con sus propios procesos (como sistemas de administración de la configuración).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>¿Por qué obtengo el mensaje "No se encontraron datos digitalizar" para mi VM?

Puede tardar algún tiempo (generalmente menos de una hora) para análisis de datos rellenar una vez habilitada la recopilación de datos en el centro de seguridad de Azure. Análisis no se rellenarán para máquinas virtuales en un estado de detención.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>¿Por qué obtengo el mensaje "Agente VM es falta?"

El agente de VM debe instalarse en máquinas virtuales para habilitar la recopilación de datos. El agente de VM se instala de forma predeterminada para máquinas virtuales que se implementan de Azure Marketplace. Para obtener información sobre cómo instalar el agente de VM en otras máquinas virtuales, consulte la entrada de blog [VM agente y extensiones](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
