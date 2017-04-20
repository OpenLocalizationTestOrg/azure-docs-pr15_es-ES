<properties
    pageTitle="Sincronización de Azure AD Connect: Administrador de servicios de sincronización UI | Microsoft Azure"
    description="Entender la ficha de operaciones en el Administrador de servicios de sincronización de Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Sincronización de Azure AD Connect: Administrador de servicios de sincronización

[Operaciones](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Conectores](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Diseñador de Metaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Búsqueda en Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

La ficha operaciones muestra los resultados de las operaciones más recientes. Esta pestaña es clave para comprender y solucionar problemas.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Comprender la información visible en la ficha operaciones
La mitad superior muestra todas las líneas en orden crónica. De forma predeterminada, el registro de operaciones conserva información acerca de los siete últimos días, pero puede cambiar esta configuración con el [Programador](active-directory-aadconnectsync-feature-scheduler.md). Desea buscar cualquier ejecución que no se muestra un estado correcto. Puede cambiar la ordenación, haga clic en los encabezados.

La columna **estado** es la información más importante y muestra el problema más grave para una ejecución. Aquí encontrará un breve resumen de los estados más comunes en orden de prioridad para investigar (donde * indicar varias cadenas de error posibles).

Estado | Comentario
--- | ---
detenido: * | No se pudo completar la ejecución. Por ejemplo, si el sistema remoto está inactivo y no se puede contactar.
límite de error detenido | Hay más de 5000 errores. La ejecución se ha detenido automáticamente debido a la gran cantidad de errores.
completadas -\*-errores | Completar la ejecución, pero hay errores (menos de 5.000) que deben investigar.
completadas -\*-advertencias | La ejecución completado, pero algunos datos no está en el estado esperado. Si se producen errores, este mensaje es normalmente solo un síntoma. Hasta que se han resuelto los errores, no debe investigar advertencias.
éxito | No hay problemas.

Cuando se selecciona una fila, la parte inferior se actualiza para mostrar los detalles de la que se ejecutan. En el extremo izquierdo de la parte inferior, es posible que tenga una lista que indica **paso #**. Esta lista solo aparece si tiene varios dominios de su bosque donde cada dominio está representado por un paso. Encontrará el nombre de dominio en el encabezado de la **partición**. En **Las estadísticas de sincronización**, puede encontrar más información sobre el número de cambios procesados. Puede hacer clic en los vínculos para obtener una lista de los objetos modificados. Si tiene objetos con errores, esos errores se muestran bajo los **Errores de sincronización**.

## <a name="troubleshoot-errors-in-operations-tab"></a>Solucionar problemas en la ficha operaciones
![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png)  
Cuando se producen errores, tanto el objeto de error y el propio error son vínculos que se proporcionan más información.

Empiece haciendo clic en la cadena de error (**sincronización-regla-error-función-activa** en la imagen). En primer lugar se presentan con una descripción general del objeto. Para ver el error real, haga clic en el botón **De la pila**. Esta traza proporciona información de nivel de depuración para el error.

**Sugerencia:** Puede secundario en el cuadro **información de la pila de llamadas** , elija **Seleccionar todo**y **Copiar**. A continuación, puede copiar la pila y busque el error en el editor favorito, como el Bloc de notas.

- Si el error es de **SyncRulesEngine**, la información de la pila de llamada primero tiene una lista de todos los atributos del objeto. Desplácese hacia abajo hasta que vea el encabezado **InnerException = >**.  
![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png)  
La línea tras muestra el error. En la imagen anterior, el error es de un Fabrikam de regla de sincronización personalizado creado.

Si el error no proporciona suficiente información, es hora para ver los datos en Sí. Puede hacer clic en el vínculo con el identificador de objeto y [seguir a un objeto y sus datos a través del sistema](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## <a name="next-steps"></a>Pasos siguientes
Más información sobre la configuración de [sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
