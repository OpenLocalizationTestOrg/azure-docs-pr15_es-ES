<properties
   pageTitle="Servicio de centro de seguridad de Azure y base de datos de SQL Azure | Microsoft Azure"
   description="Este artículo le muestra cómo el centro de seguridad puede ayudar a proteger las bases de datos en la base de datos de SQL Azure."
   services="sql-database"
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
   ms.date="10/18/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-and-azure-sql-database-service"></a>Servicio de centro de seguridad de Azure y base de datos de SQL Azure

[Centro de seguridad de Azure](https://azure.microsoft.com/documentation/services/security-center/) le ayuda a evitar, detectar y responder a las amenazas. Proporciona seguridad integrada supervisión y la directiva de administración de las suscripciones de Azure, ayuda a detecta amenazas que pueden incluirse en caso contrario, problemas y funciona con un amplio ecosistema de soluciones de seguridad.

Este artículo le muestra cómo el centro de seguridad puede ayudar a proteger las bases de datos en la base de datos de SQL Azure.

## <a name="why-use-security-center"></a>¿Por qué usar el centro de seguridad?

Centro de seguridad le ayuda a proteger los datos en la base de datos SQL proporcionando visibilidad a la seguridad de todos los servidores y bases de datos. Con el centro de seguridad, puede:

- Definir las directivas de auditoría y cifrado de base de datos de SQL.
- Supervisar la seguridad de los recursos de la base de datos SQL a través de todas las suscripciones.
- Identificar y solucionar problemas de seguridad rápidamente.
- Integrar las alertas de [detección de amenaza de base de datos de SQL Azure](../sql-database/sql-database-threat-detection-get-started.md).

Además de proteger los recursos de la base de datos de SQL, centro de seguridad también proporciona la supervisión de seguridad y administración de máquinas virtuales de Windows Azure, Cloud Services, servicios de aplicación, redes virtuales y mucho más. Obtenga más información sobre el centro de seguridad [aquí](security-center-intro.md).

## <a name="prerequisites"></a>Requisitos previos

Para empezar con el centro de seguridad, debe tener una suscripción a Microsoft Azure. El nivel de libre de centro de seguridad está habilitado con su suscripción. Para obtener más información acerca del centro de seguridad gratuitos y niveles estándares, consulte [Precios de centro de seguridad](https://azure.microsoft.com/pricing/details/security-center/).

Centro de seguridad es compatible con acceso basado en roles. Para obtener más información sobre el control de acceso basado en roles (RBAC) en Azure, consulte [Control de acceso basado en roles de Azure Active Directory](../active-directory/role-based-access-control-configure.md). Las preguntas más frecuentes de centro de seguridad proporciona información sobre [cómo se administran los permisos en el centro de seguridad](security-center-faq.md#how-are-permissions-handled-in-azure-security-center).

## <a name="access-security-center"></a>Centro de seguridad de Access

Obtener acceso a centro de seguridad desde el [portal de Azure](https://azure.microsoft.com/features/azure-portal/). [Inicie sesión en el portal](https://portal.azure.com/) y seleccione la **opción de centro de seguridad**.

![Opción de centro de seguridad][1]

Se abre el módulo de **Centro de seguridad** .
![Módulo de centro de seguridad][2]

## <a name="set-security-policy"></a>Establecer la directiva de seguridad

Una directiva de seguridad define el conjunto de controles que se recomienda para los recursos dentro de la suscripción especificada o grupo de recursos. En el centro de seguridad, defina las directivas de sus suscripciones o grupos de recursos según las necesidades de seguridad de su empresa y el tipo de aplicaciones o confidencialidad de los datos de cada suscripción.

Puede establecer una directiva para mostrar recomendaciones de auditoría de SQL y cifrado de datos transparente de SQL (TDE).

- Cuando se activan **SQL auditoría y detección de amenazas**, centro de seguridad en el que se recomienda que auditoría del acceso a la base de datos de Azure habilitarse de cumplimiento, detección y con fines de investigación avanzadas.
- Cuando se activa el **cifrado de datos transparente de SQL**, centro de seguridad recomienda que el cifrado al resto habilitarse para la base de datos de SQL Azure, copias de seguridad asociados y los archivos de registro de transacciones.

Para establecer una directiva de seguridad, seleccione el icono de **Directiva** en el módulo de centro de seguridad. En el módulo de **directivas de seguridad** , seleccione la suscripción en la que desea habilitar la directiva de seguridad. Seleccione la **Directiva de prevención** y **activar las recomendaciones de seguridad que desea usar en esta suscripción** .
![Directiva de seguridad][3]

Para obtener más información, vea [establecer directivas de seguridad](security-center-policies.md).

## <a name="manage-security-recommendation"></a>Administrar la recomendación de seguridad

Centro de seguridad periódicamente analiza el estado de seguridad de los recursos de Azure. Cuando el centro de seguridad se identifica posibles vulnerabilidad de seguridad, crea recomendaciones. Las recomendaciones le guiará a través del proceso de configuración de los controles necesarios.

Después de establecer una directiva de seguridad, el centro de seguridad analiza el estado de seguridad de los recursos para identificar los puntos débiles potenciales. Las recomendaciones se muestran en un formato de tabla, donde cada línea representa una recomendación determinada. Utilice la siguiente tabla como referencia para ayudarle a entender las recomendaciones disponibles para la base de datos de SQL Azure, y lo que hace cada recomendación si se aplica. Seleccionar una recomendación le lleva a un artículo que explica cómo implementar la recomendación en el centro de seguridad.

| Recomendación | Descripción |
| ----- | ----- |
| [Habilitar la detección de auditoría y amenazas en servidores SQL Server](security-center-enable-auditing-on-sql-servers.md) | Se recomienda activar detección de auditoría y amenazas para servidores de base de datos de SQL. (Solo servicio de base de datos de SQL. No incluye ejecutando en sus máquinas virtuales de Microsoft SQL Server). |
| [Habilitar la detección de auditoría y amenazas en bases de datos SQL](security-center-enable-auditing-on-sql-databases.md) | Se recomienda activar detección de auditoría y amenazas para las bases de datos de la base de datos de SQL. (Solo servicio de base de datos de SQL. No incluye ejecutando en sus máquinas virtuales de Microsoft SQL Server). |
| [Habilitar el cifrado de datos transparente](security-center-enable-transparent-data-encryption.md) | Se recomienda habilitar el cifrado de bases de datos SQL. (Solo servicio de base de datos SQL). |

Para ver las recomendaciones para los recursos de Azure, seleccione el icono de **recomendaciones** en el módulo de centro de seguridad. En el módulo de **recomendaciones** , seleccione una recomendación para ver detalles. En este ejemplo, vamos a seleccione **Habilitar auditoría y detección de amenazas en servidores SQL Server**.

![Recomendaciones][4]

Como se muestra a continuación, en el centro de seguridad se muestra los servidores de SQL Server donde no está habilitada la detección de auditoría y amenazas. Después de activar la auditoría, puede configurar la configuración de detección de amenazas y la configuración de correo electrónico para recibir alertas de seguridad. Detección de amenazas le avisa cuando detecta actividades irregulares de la base de datos que indican posibles amenazas de seguridad de la base de datos. Las alertas se muestran en el panel Centro de seguridad.
![Detección de auditoría y amenazas][5]

Siga los pasos de [empezar a trabajar con detección de amenaza de base de datos de SQL](../sql-database/sql-database-threat-detection-get-started.md) para activar y configurar la detección de amenazas y para configurar la lista de mensajes de correo electrónico que va a recibir alertas de seguridad tras la detección de actividades irregulares.

Para obtener más información acerca de las recomendaciones, vea [administrar las recomendaciones de seguridad](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Supervisar el mantenimiento de seguridad

Después de habilitar [las directivas de seguridad](security-center-policies.md) para los recursos de la suscripción, el centro de seguridad analizará la seguridad de los recursos para identificar los puntos débiles potenciales.  Puede ver el estado de los recursos de seguridad en el mosaico de **Mantenimiento de seguridad de recursos** . Al hacer clic en **datos** en el mosaico de **Mantenimiento de seguridad de recursos** , el módulo de **Recursos de datos** se abre con recomendaciones de SQL para problemas, como el cifrado de datos de auditoría y transparente no están habilitadas. También tiene recomendaciones para el estado general de la base de datos.
![Estado de seguridad de recursos][6]

Para obtener más información, vea [supervisión de estado de seguridad](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Administrar y responder a las alertas de seguridad

Centro de seguridad automáticamente recopila, analiza y se integra datos de registro de [Detección de amenaza de SQL Azure](../sql-database/sql-database-threat-detection-get-started.md), así como otros recursos de Azure, para detectar amenazas reales y reducir los falsos positivos. En Centro de seguridad, se muestra una lista de las alertas de seguridad prioridades junto con la información que necesita para investigar rápidamente el problema y recomendaciones acerca de cómo corregir un ataque.

Para ver alertas, seleccione el icono de **las alertas de seguridad** en el módulo de centro de seguridad. En el módulo de **las alertas de seguridad** , seleccione una alerta para obtener más información sobre los eventos que activó la alerta y qué, si existe, pasos que debe seguir para corregir un ataque. En este ejemplo, seleccionaremos **la inserción de SQL posibles**.
![Alertas de seguridad][7]

Como se muestra a continuación, centro de seguridad proporciona detalles adicionales que ofrecen una perspectiva de lo que activó la alerta, el recurso de destino, cuando sea aplicable la dirección IP de origen y recomendaciones sobre cómo corregir.
![Posible inyección de SQL][8]

Para obtener más información, consulte [administrar y responder a las alertas de seguridad](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Pasos siguientes

- [Preguntas más frecuentes de centro de seguridad](security-center-faq.md) : preguntas más frecuentes sobre el uso del servicio de búsqueda.
- [Guía de planificación y operaciones del centro de seguridad](security-center-planning-and-operations-guide.md) : seguir un conjunto de pasos y tareas para optimizar el uso del centro de seguridad en función de los requisitos de seguridad y el modelo de administración de la nube de su organización.
- [Datos del centro de seguridad](security-center-data-security.md) : Obtenga información sobre cómo el centro de seguridad recopila y procesa datos sobre los recursos de Azure, incluida la información de configuración, metadatos, registros de eventos, archivos de descarga de bloqueo y.
- [Administración de incidencias de seguridad](security-center-incident.md) : Obtenga información sobre cómo usar la capacidad de alerta de seguridad en el centro de seguridad para ayudarle a controlar las incidencias de seguridad.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
