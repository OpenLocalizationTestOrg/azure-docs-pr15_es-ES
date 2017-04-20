<properties
   pageTitle="Informes de Azure Active Directory: vista previa | Microsoft Azure"
   description="Enumera los distintos informes disponibles para la vista previa de Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="markusvi"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="markvi"/>

# <a name="azure-active-directory-reporting---preview"></a>Informes de Azure Active Directory: vista previa

> [AZURE.SELECTOR]
- [Portal de Azure](active-directory-reporting-azure-portal.md)
- [Portal de clásico de Azure](active-directory-reporting-guide.md)

*Esta documentación forma parte de [Azure Active Directory Reporting guía](active-directory-reporting-guide.md).*

Con los informes en la vista previa de Azure Active Directory, obtenga toda la información que necesita para determinar cómo se hace su entorno. [¿Qué es la vista previa?](active-directory-preview-explainer.md)

Hay dos áreas principales de informes:

- **Las actividades de inicio de sesión** : información sobre el uso de las aplicaciones administradas y las actividades de inicio de sesión de usuario

- **Registros de auditoría** – información de actividad de sistema de usuarios y administración de grupo, sus aplicaciones administradas y actividades de directorio

Según el ámbito de los datos que está buscando, se puede obtener acceso a estos informes, haga clic en **usuarios y grupos** o **aplicaciones empresariales** , en la lista de servicios en el [portal de Azure](https://portal.azure.com).

## <a name="sign-in-activities"></a>Actividades de inicio de sesión

### <a name="user-sign-in-activities"></a>Actividades de inicio de sesión de usuario

Con la información proporcionada por el informe de inicio de sesión de usuario, encontrar respuestas a preguntas como:

- ¿Qué es el modelo de inicio de sesión de un usuario?
- ¿Cuántos usuarios han usuarios han iniciado sesión la última semana?
- ¿Qué es el estado de estos complementos de inicio de sesión?

El punto de entrada a los datos es el gráfico de inicio de sesión de usuario en la sección **información general** en **usuarios y grupos**.

 ![Informes] (./media/active-directory-reporting-azure-portal/05.png "Informes")

El gráfico de inicio de sesión de usuario muestra agregaciones semanales de inicio de sesión ins para todos los usuarios en un período de tiempo determinado. El valor predeterminado para el período de tiempo es 30 días.

![Informes] (./media/active-directory-reporting-azure-portal/02.png "Informes")

Al hacer clic en un día en el gráfico de inicio de sesión, obtener una lista detallada de las actividades de inicio de sesión.

![Informes] (./media/active-directory-reporting-azure-portal/03.png "Informes")

Cada fila de la lista de las actividades de inicio de sesión tendrá información detallada sobre el inicio de sesión seleccionado como:

- ¿Quién ha iniciado sesión en?

- ¿Cuál fue el UPN relacionado?

- ¿Qué aplicaciones ha sido el destino de la sesión?

- ¿Qué es la dirección IP de la sesión?

- ¿Cuál fue el estado de la sesión?

### <a name="usage-of-managed-applications"></a>Uso de las aplicaciones administradas

Con una vista centrada en la aplicación de los datos de inicio de sesión, puede responder a preguntas como:

- ¿Quién es con mis aplicaciones?

- ¿Cuáles son las aplicaciones de 3 superiores de su organización?

- He recientemente despliegan las en una aplicación. ¿Cómo hace?


El punto de entrada a los datos es las aplicaciones de 3 superiores de la organización en el último informe de 30 días en la sección de **información general** en **aplicaciones empresariales**.

 ![Informes] (./media/active-directory-reporting-azure-portal/06.png "Informes")


Agregaciones semanal del gráfico uso la aplicación de firmar ins para las aplicaciones de 3 principales en un período de tiempo determinado. El valor predeterminado para el período de tiempo es 30 días.

![Informes] (./media/active-directory-reporting-azure-portal/78.png "Informes")

Si lo desea, puede establecer el foco en una aplicación específica.

![Informes] (./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Informes")


Al hacer clic en un día en el gráfico de uso de la aplicación, obtener una lista detallada de las actividades de inicio de sesión.


![Informes] (./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Informes")



La opción de **inicios de sesión** le ofrece una descripción completa de todos los eventos de inicio de sesión a sus aplicaciones.

![Informes] (./media/active-directory-reporting-azure-portal/85.png "Informes")

Mediante el selector de columna, puede seleccionar los campos de datos que desea mostrar.

![Informes] (./media/active-directory-reporting-azure-portal/column_chooser.png "Informes")



### <a name="filtering-sign-ins"></a>Filtrado inicios de sesión

Puede filtrar inicios de sesión por un intervalo de tiempo para limitar la cantidad de datos que se muestran.

![Informes] (./media/active-directory-reporting-azure-portal/927.png "Informes")


Otro método para filtrar las entradas de las actividades de inicio de sesión es buscar entradas específicas.
El método de búsqueda le permite definir el ámbito de los complementos de inicio de sesión alrededor de determinados **usuarios**, **grupos** o **aplicaciones**.


![Informes] (./media/active-directory-reporting-azure-portal/84.png "Informes")

## <a name="audit-logs"></a>Registros de auditoría

Los registros de auditoría de Azure Active Directory proporcionan registros de actividades de sistema de cumplimiento.

Existen tres categorías de auditoría de las actividades relacionadas en el portal de Azure:

- Usuarios y grupos   

- Aplicaciones

- Directorio   


Para obtener una lista completa de las actividades de informe de auditoría, consulte la [lista de eventos de informe de auditoría](active-directory-reporting-audit-events.md#list-of-audit-report-events).


El punto de entrada a todos los datos de auditoría es **registros de auditoría** en la sección de **actividad** de **Azure Active Directory**.


![Auditoría] (./media/active-directory-reporting-azure-portal/61.png "Auditoría")


Un registro de auditoría tiene una vista de lista que muestra los actores (quién), las actividades (qué) y los objetivos.


![Auditoría] (./media/active-directory-reporting-azure-portal/345.png "Auditoría")


Haciendo clic en un elemento de la vista de lista, puede obtener más detalles sobre ella.

![Auditoría] (./media/active-directory-reporting-azure-portal/873.png "Auditoría")




### <a name="users-and-groups-audit-logs"></a>Registros de auditoría de usuarios y grupos


Con los informes de auditoría basadas en grupos y usuario, puede obtener respuestas a preguntas como:

- ¿Qué tipos de actualizaciones que hubiera aplicado a los usuarios?

- ¿Cuántos usuarios se han cambiado?

- ¿Cuántos contraseñas se han cambiado?

- ¿Lo que ha hecho en un directorio un administrador?

- ¿Qué son los grupos que se han agregado?

- ¿Hay grupos con los cambios de la suscripción?

- ¿Han cambiado los propietarios del grupo?

- ¿Qué licencias asignadas a un grupo o un usuario?


Si desea revisar los datos de auditoría relacionada con los usuarios y grupos, puede encontrar una vista filtrada en **registros de auditoría** en la sección de la **actividad** de **los usuarios y grupos**.


![Auditoría] (./media/active-directory-reporting-azure-portal/93.png "Auditoría")


### <a name="application-audit-logs"></a>Registros de auditoría de aplicación

Informes de auditoría con basado en la aplicación, puede obtener respuestas a preguntas como:

- ¿Cuáles son las aplicaciones que se han agregado o actualizado?

- ¿Cuáles son las aplicaciones que se han quitado?

- ¿Ha cambiado principal de servicio para una aplicación?

- ¿Se cambiaron los nombres de las aplicaciones?

- ¿Quién ha dado su consentimiento a la aplicación?


Si desea revisar los datos de auditoría que están relacionado con las aplicaciones, puede encontrar una vista filtrada en **registros de auditoría** en la sección de la **actividad** de **las aplicaciones empresariales**.


![Auditoría] (./media/active-directory-reporting-azure-portal/134.png "Auditoría")


### <a name="filtering-audit-logs"></a>Filtrar registros de auditoría

Puede filtrar un informe de auditoría por un intervalo de tiempo para limitar la cantidad de datos que se muestran.

![Auditoría] (./media/active-directory-reporting-azure-portal/324.png "Auditoría")

Otro método para filtrar las entradas de un registro de auditoría es buscar entradas específicas.

![Auditoría] (./media/active-directory-reporting-azure-portal/237.png "Auditoría")

## <a name="next-steps"></a>Pasos siguientes

Consulte el [Azure Active Directory Guía de informes](active-directory-reporting-guide.md).
