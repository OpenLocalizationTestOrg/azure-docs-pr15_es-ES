
<properties
    pageTitle="Con estado de conexión de Azure AD sync | Microsoft Azure"
    description="Esta es la página de estado de conexión de Azure AD que se describe cómo supervisar la sincronización de Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="using-azure-ad-connect-health-for-sync"></a>Usando el estado de conexión de Azure AD para sincronizar
La siguiente documentación es específica a la supervisión de Azure AD Connect (sincronizar) con su estado de conexión de Azure AD.  Para obtener información sobre la supervisión de AD FS con su estado de conexión de Azure AD vea [Utilizando Azure AD conectar estado con AD FS](active-directory-aadconnect-health-adfs.md). Además, para obtener información sobre la supervisión de servicios de dominio de Active Directory con su estado de conexión de Azure AD vea [Utilizando Azure AD conectar estado con AD DS](active-directory-aadconnect-health-adds.md).

![Azure AD Connect estado de sincronización](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Alertas de Azure AD conectar de estado de sincronización
La Azure AD conectar alertas de estado de la sección de sincronización se proporciona la lista de alertas activas. Cada alerta incluye información relevante, pasos de solución y vínculos a la documentación relacionada. Seleccionando una alerta activa o resuelta, verá un nuevo módulo con información adicional, así como los pasos que puede realizar para resolver la alerta y vínculos a documentación adicional. También puede ver datos históricos sobre las alertas que se han resuelto en el pasado.

Seleccionando una alerta que se proporcionará con información adicional, así como los pasos que puede realizar para resolver la alerta y vínculos a documentación adicional.

![Error de sincronización de Azure AD Connect](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Evaluación limitado de alertas
Si Azure AD Connect no está usando la configuración predeterminada (por ejemplo, si el atributo de filtrado se cambia de la configuración predeterminada para una configuración personalizada), el agente de mantenimiento de conexión de Azure AD no cargará los eventos de error relacionados con Azure AD Connect.

Esto limita la evaluación de alertas por el servicio. ¿Se verá una pancarta que indica esta condición en el Portal de Azure en el servicio.

![Azure AD Connect estado de sincronización](./media/active-directory-aadconnect-health-sync/banner.png)

Puede cambiar esta opción haciendo clic en "Configuración" y que permite agente de estado de conexión de Azure AD cargar todos los registros de errores.

![Azure AD Connect estado de sincronización](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>Información de sincronización
Los administradores con frecuencia desea saber sobre el tiempo que se tarda en sincronizar cambios a Azure AD y la cantidad de efectuar cambios. Esta característica proporciona una forma fácil de visualizar esto usando la debajo de gráficos:   

- Latencia de las operaciones de sincronización
- Tendencia de cambio de objeto

### <a name="sync-latency"></a>Latencia de sincronización
Esta característica proporciona una línea de tendencia gráfica de latencia de las operaciones de sincronización para los conectores (importar, exportar, etc.).  Esto proporciona una manera rápida y fácil de entender no solo la latencia de las operaciones (mayores si tiene un amplio conjunto de cambios que se producen), sino también una forma para detectar anomalías en la latencia que puede requerir investigación.

![Latencia de sincronización](./media/active-directory-aadconnect-health-sync/synclatency02.png)

De forma predeterminada, se muestra solo la latencia de la operación 'Exportar' para el conector de Azure AD.  Para ver más operaciones en el conector o para ver las operaciones de otros conectores, haga clic en el gráfico, seleccione Editar gráfico o haga clic en el botón "Editar gráfico de latencia" y elija la operación específica y conectores.

### <a name="sync-object-changes"></a>Sincronizar cambios de objeto
Esta característica proporciona una línea de tendencia gráfica del número de cambios que se evalúa y exportarse a Azure AD.  En la actualidad, es difícil intenta recopilar esta información de los registros de sincronización.  El gráfico le ofrece, no solo una manera más sencilla de supervisar el número de cambios que se producen en su entorno, sino también una representación visual de los errores que se producen.

![Latencia de sincronización](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>Informe de errores de sincronización de nivel de objeto (vista preliminar)
Esta característica proporciona un informe sobre los errores de sincronización que pueden ocurrir cuando se sincronizan los datos de identidad entre Windows Server AD y Azure AD con Azure AD Connect.

- El informe trata sobre errores registrados por el cliente de sincronización (Azure AD Connect versión 1.1.281.0 o posterior)
- Incluye los errores que ocurrieron en la última operación de sincronización en el motor de sincronización. ("Exportar" en el conector de AD Azure.)
- Azure AD conectar estado agente de sincronización debe tener conectividad saliente de los puntos finales necesarios para el informe incluir los datos más recientes. Vea la [sección requisitos](active-directory-aadconnect-health-agent-install.md#Requirements) para obtener información detallada.
- El informe está **actualizado después de cada 30 minutos** usando los datos cargados por agente de estado de conexión de Azure AD para sincronizar.
Proporciona las siguientes capacidades claves

    - Clasificación de errores
    - Lista de objetos con error por categoría
    - Todos los datos sobre los errores en un solo lugar
    - Comparación de objetos con error debido a un conflicto en paralelo
    - Descargar el informe de errores como un CVS (próximamente)

### <a name="categorization-of-errors"></a>Clasificación de errores
El informe clasifica los errores de sincronización existente en las siguientes categorías:

| Categoría | Descripción |
| -------------- | ----------- |
| Atributo duplicado | Errores al Azure AD Connect intenta crear o actualización objetos con valores duplicados de uno o más atributos de Azure AD debe ser único en un inquilino, por ejemplo, proxyAddresses, UserPrincipalName. |
| Error de coincidencia de datos | Error cuando se produce un error en la coincidencia de software para que coincida con los objetos que se producen errores de sincronización. |
| Error de validación de datos | Errores debido a datos no válidos, como caracteres no admitidos en atributos críticas como UserPrincipalName, dar formato a los errores que se producirá un error de validación antes de que se escriben en Azure AD.|
| Atributo grande | Errores de uno o más atributos son mayores que el tamaño permitido, la longitud o la cuenta.|
| Otros | Todos los demás errores que no caben en las categorías anteriores. Basado en comentarios, esta categoría se dividirá en subcategorías.

![Resumen de informe de errores de sincronización](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![categorías de informe de errores de sincronización](./media/active-directory-aadconnect-health-sync/errorreport02.png)

### <a name="list-of-objects-with-error-per-category"></a>Lista de objetos con error por categoría
En cada categoría de taladro le proporcionará la lista de objetos con el error de esa categoría.
![Lista de informe de errores de sincronización](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>Detalles del error
Después de datos está disponible en la vista detallada de cada error

- Identificadores de *Objeto de Active Directory* implicado
- Identificadores de *Objeto de Azure AD* implicado (si procede)
- Descripción de error y cómo corregir
- Artículos relacionados

![Detalles del informe de Error de sincronización](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>Descargar el informe de errores como CSV
Esta funcionalidad está disponible próximamente. Esté atento más actualizaciones.



## <a name="related-links"></a>Vínculos relacionados
* [Solución de errores durante la sincronización](active-directory-aadconnect-troubleshoot-sync-errors.md)
* [Duplicar atributo resistencia](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect estado](active-directory-aadconnect-health.md)
* [Azure AD Connect instalación de agente de estado](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect operaciones de mantenimiento](active-directory-aadconnect-health-operations.md)
* [Conecte mediante Azure AD estado con AD FS](active-directory-aadconnect-health-adfs.md)
* [Conecte mediante Azure AD estado con AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect mantenimiento Preguntas más frecuentes](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect historial de versiones de estado](active-directory-aadconnect-health-version-history.md)
