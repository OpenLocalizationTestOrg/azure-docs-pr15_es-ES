<properties
 pageTitle="Introducción a Azure programador en el portal de Azure | Microsoft Azure"
 description="Introducción a Azure programador en el portal de Azure"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/10/2016"
 ms.author="deli"/>

# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Introducción a Azure programador en el portal de Azure

Es fácil crear tareas programadas en el programador de Azure. En este tutorial, aprenderá a crear una tarea. También verá las capacidades de administración y supervisión del programador.

## <a name="create-a-job"></a>Crear un trabajo

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com/).  

2.  Haga clic en **+ nuevo** > escriba _Programador_ en el cuadro de búsqueda > Seleccionar **Programador** de resultados > haga clic en **crear**.

     ![][marketplace-create]

3.  Vamos a crear una tarea que simplemente visitas http://www.microsoft.com/ con una solicitud GET. En la pantalla de **Trabajo del programador** , escriba la información siguiente:

    1.  **Nombre:**`getmicrosoft`  

    2.  **Suscripción:** Su suscripción de Azure   

    3.  **Trabajo colección:** Seleccione una colección de trabajo existente, o haga clic en **Crear nuevo** > escriba un nombre.

4.  A continuación, en **Configuración de la acción**, defina los siguientes valores:

    1.  **Tipo de acción:**` HTTP`  

    2.  **Método:**`GET`  

    3.  **Dirección URL:**` http://www.microsoft.com`  

      ![][action-settings]

5.  Por último, vamos a definir una programación. El trabajo se podría definir como un único trabajo, pero vamos a elegir una programación de periodicidad:

    1. **Periodicidad**:`Recurring`

    2. **Inicio**: la fecha de hoy

    3. **Repetir cada**:`12 Hours`

    4. **Terminar**: la fecha de dos días de la fecha actual  

      ![][recurrence-schedule]

6.  Haga clic en **crear**

## <a name="manage-and-monitor-jobs"></a>Administrar y supervisar trabajos

Una vez que se crea una tarea, aparece en el panel de Azure principal. Haga clic en el trabajo y se abrirá una ventana nueva con las siguientes pestañas:

1.  Propiedades  

2.  Configuración de la acción  

3.  Programación  

4.  Historial

5.  Usuarios

    ![][job-overview]

### <a name="properties"></a>Propiedades

Estas propiedades de sólo lectura, describen los metadatos de administración para el trabajo del programador.

   ![][job-properties]


### <a name="action-settings"></a>Configuración de la acción

Al hacer clic en un trabajo en la pantalla de **trabajos** le permite configurar ese trabajo. Esto le permite configurar la configuración avanzada, si no ha configurado en el Asistente de creación rápida.

Para todos los tipos de acción, puede cambiar la directiva de reintento y la acción de error.

Para los tipos de acción de trabajo HTTP y HTTPS, puede cambiar el método a cualquier verbo HTTP permitido. También puede agregar, eliminar o cambiar los encabezados y la información de autenticación básica.

Tipos de acción de cola de almacenamiento, puede cambiar la cuenta de almacenamiento, el nombre de cola, el token de SA y el cuerpo.

Para los tipos de acción de bus de servicio, puede cambiar el espacio de nombres, tema/ruta de acceso, configuración de autenticación, tipo de transporte, propiedades del mensaje y el cuerpo del mensaje.

   ![][job-action-settings]

### <a name="schedule"></a>Programación

Esto le permite volver a configurar la programación, si desea cambiar la programación que creó en el Asistente de creación rápida.

Esta es una oportunidad para generar [programaciones complejas y avanzada periodicidad en su trabajo](scheduler-advanced-complexity.md)

Puede cambiar la fecha de inicio y hora, la programación de periodicidad y el final de fecha y hora (si la tarea es periódica.)

   ![][job-schedule]


### <a name="history"></a>Historial

La pestaña de **historial** muestra seleccionados métricas para cada ejecución del trabajo del sistema para la tarea seleccionada. Estas métricas proporcionan valores en tiempo real sobre el estado de su programador:

1.  Estado  

2.  Detalles  

3.  Reintentos

4.  Repetición: 1 º, 2 º, 3 º, etcetera.

5.  Hora de inicio de ejecución  

6.  Hora de finalización de ejecución

   ![][job-history]

Puede hacer clic en un ejecutar para ver los **Detalles del historial**, incluidas la respuesta toda para cada ejecución. Este cuadro de diálogo, también puede copiar la respuesta en el Portapapeles.

   ![][job-history-details]

### <a name="users"></a>Usuarios

Azure Control de acceso basado en roles (RBAC) permite la administración de acceso específico para programador de Azure. Para obtener información sobre cómo usar la pestaña de usuarios, consulte [Control de acceso de Azure Role-Based](../active-directory/role-based-access-control-configure.md)


## <a name="see-also"></a>Vea también

 [¿Qué es programador?](scheduler-intro.md)

 [Programador conceptos, terminología y la jerarquía de entidad](scheduler-concepts-terms.md)

 [Planes y facturación en el programador de Azure](scheduler-plans-billing.md)

 [Cómo crear programaciones complejas y periodicidad avanzada con el programador de Azure](scheduler-advanced-complexity.md)

 [Referencia de la API de REST de programador](https://msdn.microsoft.com/library/mt629143)

 [Referencia del programador cmdlets de PowerShell](scheduler-powershell-reference.md)

 [Programador de alta disponibilidad y confiabilidad](scheduler-high-availability-reliability.md)

 [Códigos de error, los valores predeterminados y los límites de programador](scheduler-limits-defaults-errors.md)

 [Autenticación de salida del programador](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
