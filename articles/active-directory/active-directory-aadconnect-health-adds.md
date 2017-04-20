
<properties
    pageTitle="Conecte mediante Azure AD estado con AD DS | Microsoft Azure"
    description="Esta es la página de estado de conexión de Azure AD que se describe cómo supervisar AD DS."
    services="active-directory"
    documentationCenter=""
    authors="arluca"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="arluca"/>

# <a name="using-azure-ad-connect-health-with-ad-ds"></a>Conecte mediante Azure AD estado con AD DS
La siguiente documentación es específica a la supervisión de servicios de dominio de Active Directory con su estado de conexión de Azure AD. Las versiones compatibles de AD DS son: Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2.

Para obtener más información sobre la supervisión de AD FS con su estado de conexión de Azure AD, consulte [Uso de Azure AD conectar Health con AD FS](active-directory-aadconnect-health-adfs.md). Además, para obtener información sobre la supervisión de Azure AD Connect (sincronizar) con su estado de conexión de Azure AD consulte [Usando Azure AD conectar estado de sincronización](active-directory-aadconnect-health-sync.md).

![Azure AD Connect estado para AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-entry.png)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>Las alertas de Azure AD conectan estado para AD DS
La sección de alertas de Azure AD conectar estado para AD DS, se proporciona una lista de activas y resueltos alertas relacionadas con los controladores de dominio. Seleccionar una alerta activa o resuelta abre un nuevo módulo con información adicional, junto con los pasos de solución y vínculos a la documentación correspondiente. Cada tipo de alerta puede tener una o varias instancias que corresponden a cada uno de los controladores de dominio afectados por dicha alerta en concreto. En la parte inferior del módulo de alerta, haga doble clic en un controlador de dominio afectado para abrir un módulo adicional con más detalles acerca de esa instancia de alerta.

En este módulo, puede habilitar las notificaciones de correo electrónico para las alertas y cambiar el intervalo de tiempo en la vista. Expandir el intervalo de tiempo le permite ver alertas resueltos anteriores.

![Error de sincronización de Azure AD Connect](./media/active-directory-aadconnect-health/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>Panel controladores de dominio
Este panel proporciona una vista de topología de su entorno, junto con las mediciones operativas clave y el estado de cada uno de los controladores de dominio supervisado. Las mediciones presentadas ayudan a identificar rápidamente cualquier controlador de dominio que podría requerir investigación. De forma predeterminada, se muestra únicamente un subconjunto de las columnas. Sin embargo, puede encontrar el conjunto completo de columnas disponibles, haga doble clic en el comando columnas. Seleccionar las columnas que más importante, convierta este panel en una única y fácil colocar para ver el estado de su entorno de AD DS.

![Controladores de dominio](./media/active-directory-aadconnect-health/aadconnect-health-adds-domainsandsites-dashboard.png)

Controladores de dominio se pueden agrupar por sus respectivo dominio o sitio, lo cual resulta útil para comprender la topología de entorno. Por último, si hace doble clic en el encabezado del módulo, el panel aumenta al máximo para utilizar inmobiliaria disponible de la pantalla. Esta vista más grande es útil cuando se muestran varias columnas.

## <a name="replication-status-dashboard"></a>Panel de estado de replicación
Este panel proporciona una vista de estado de replicación y la topología de replicación de los controladores de dominio supervisado. Se muestra el estado del intento de replicación más reciente, junto con la documentación útil para cualquier error que se encuentra. Haga doble clic en un controlador de dominio con el error, abra un nuevo módulo con información como: detalles sobre el error, se recomienda pasos de solución y vínculos a documentación de solución de problemas.

![Estado de la replicación](./media/active-directory-aadconnect-health/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>Supervisión
Esta característica proporciona gráficas tendencias de contadores de rendimiento que se recopilan continuamente de cada uno de los controladores de dominio supervisado. Rendimiento de un controlador de dominio se pueden comparar fácilmente en todos los otros controladores de dominio supervisado del bosque. Además, puede ver varios contadores de rendimiento en paralelo, lo que resulta útil cuando la solución de problemas en su entorno.

![Supervisión](./media/active-directory-aadconnect-health/aadconnect-health-adds-monitoring.png)

De forma predeterminada, nos hemos valor cuatro contadores; Sin embargo, puede incluir otros usuarios haciendo clic en el comando filtrar y seleccionar o anular la selección de cualquier contador de rendimiento que desee. Además, hacer doble clic en un gráfico de contador de rendimiento para abrir un nuevo módulo, que incluye puntos de datos para cada uno de los controladores de dominio supervisado.

## <a name="related-links"></a>Vínculos relacionados

* [Azure AD Connect estado](active-directory-aadconnect-health.md)
* [Azure AD Connect instalación de agente de estado](active-directory-aadconnect-health-agent-install.md)
* [Operaciones de estado de conexión de Azure AD](active-directory-aadconnect-health-operations.md)
* [Conecte mediante Azure AD Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando el estado de conexión de Azure AD para sincronizar](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect Health preguntas más frecuentes](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect historial de versiones de estado](active-directory-aadconnect-health-version-history.md)
