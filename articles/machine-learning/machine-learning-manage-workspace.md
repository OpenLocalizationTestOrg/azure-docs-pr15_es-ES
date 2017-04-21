<properties
    pageTitle="Administrar un área de trabajo de aprendizaje de equipo | Microsoft Azure"
    description="Administrar el acceso a áreas de trabajo de aprendizaje del equipo de Azure e implementar y administrar servicios web de API de m"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="garye"/>


# <a name="manage-an-azure-machine-learning-workspace"></a>Administrar un área de trabajo de aprendizaje del equipo de Azure

>[AZURE.NOTE] Los procedimientos descritos en este artículo son relevantes para los servicios Web clásico de Azure máquina aprendizaje. Para obtener información sobre la administración de servicios Web en el portal de servicios Web de aprendizaje de equipo, consulte [administrar un servicio Web con el portal de servicios Web de Azure máquina aprendizaje](machine-learning-manage-new-webservice.md).

Con el portal de clásico Azure, puede administrar sus áreas de trabajo de aprendizaje para:

- Controlar cómo se utiliza el área de trabajo
- Configurar el área de trabajo para permitir o denegar el acceso
- Administrar servicios Web creados en el área de trabajo
- Eliminar el área de trabajo

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Además, la pestaña panel proporciona información general sobre el uso del área de trabajo y una vista rápida de la información del área de trabajo.  

> [AZURE.TIP] En Azure máquina aprendizaje Studio, en la pestaña **Servicios WEB** , puede agregar, actualizar o eliminar un servicio Web de aprendizaje del equipo.

Para administrar un área de trabajo:

1.  Inicie sesión en el [portal de clásica Azure](https://manage.windowsazure.com/) con su cuenta de Microsoft Azure: usar la cuenta que está asociada a la suscripción de Azure.
2.  En el panel de servicios de Microsoft Azure, haga clic en **El aprendizaje**.
3.  Haga clic en el área de trabajo que desee administrar.

La página de área de trabajo tiene tres pestañas:

- **Panel** - le permite ver el uso de área de trabajo y la información
- **Configurar** - le permite administrar el acceso al área de trabajo
- **Servicios WEB** : le permite administrar servicios Web que se han publicado en esta área de trabajo

## <a name="to-monitor-how-the-workspace-is-being-used"></a>Para controlar cómo se utiliza el área de trabajo

Haga clic en la pestaña **panel** .

Desde el panel, puede ver el uso general del espacio de trabajo y obtener una vista rápida de la información del área de trabajo.

- El gráfico **calcular** muestra los recursos de cálculo que está usando el área de trabajo. Puede cambiar la vista para mostrar relativa o absoluta valores y se puede cambiar el intervalo de tiempo que se muestren en el gráfico.
- **Información general de uso** muestra el almacenamiento de Azure que está usando el área de trabajo.
- **Resumida** proporciona un resumen de información de área de trabajo y vínculos útiles.

> [AZURE.NOTE] El vínculo **iniciar sesión m Studio** abre máquina aprendizaje Studio usando actualmente ha iniciado sesión en la Account de Microsoft. El Microsoft Account que usó para iniciar sesión en el portal de clásico Azure para crear un área de trabajo no tiene permiso para abrir dicha área de trabajo automáticamente. Para abrir un área de trabajo, debe haber iniciado sesión en la Account de Microsoft que se ha definido como propietario del área de trabajo o debe recibir una invitación del propietario para unir el área de trabajo.


## <a name="to-grant-or-suspend-access-for-users"></a>Para conceder o suspender el acceso de usuarios ##

Haga clic en la ficha **Configurar** .

En la pestaña configuración puede:

- Suspender el acceso al área de trabajo de aprendizaje de máquina haciendo clic en Denegar. Los usuarios ya no podrán abrir el área de trabajo en equipo aprendizaje Studio. Para restaurar el acceso, haga clic en permitir.

Para administrar cuentas adicionales que tienen acceso al área de trabajo en equipo aprendizaje Studio, haga clic en **iniciar sesión Studio de m** en la pestaña **panel** (vea la nota anterior sobre **iniciar sesión Studio v**). Se abrirá el área de trabajo en equipo aprendizaje Studio. A continuación, haga clic en la pestaña **configuración** y, a continuación, **los usuarios**. Puede hacer clic en **Invitar a más usuarios** para conceder a los usuarios acceso al área de trabajo, o seleccione un usuario y haga clic en **Quitar**.


## <a name="to-manage-web-services-in-this-workspace"></a>Administrar servicios web en esta área de trabajo

Haga clic en la pestaña **Servicios WEB** .

Esto muestra una lista de servicios web publicados desde esta área de trabajo.
Para administrar un servicio web, haga clic en el nombre de la lista para abrir la página del servicio Web.

Un servicio Web puede tener uno o más extremos definidos.

- Puede definir más extremos además del extremo de "Predeterminado". Para agregar el punto final, haga clic en **Administrar extremos** en la parte inferior del panel para abrir el portal de servicios Web de Azure máquina aprendizaje.

- Para eliminar un punto final (no se puede eliminar el extremo "Predeterminado"), haga clic en la casilla de verificación al principio de la fila del extremo y a continuación, haga clic en **Eliminar**. Quita el extremo de servicio Web.

    > [AZURE.NOTE] Si una aplicación está utilizando el extremo del servicio web cuando se elimina el extremo, la aplicación recibirá un error la próxima vez que intenta obtener acceso al servicio.

Haga clic en el nombre de un extremo de servicio Web para abrirlo. 

Desde el panel, puede ver el uso general del servicio Web durante un período de tiempo. Puede seleccionar el período de ver desde el menú desplegable de período en la esquina superior derecha de los gráficos de uso. El panel muestra la siguiente información:

- **Solicitudes largo del tiempo** muestra un gráfico de paso del número de solicitudes durante el período de tiempo seleccionado. Puede ayudar a identificar si se producen picos en uso.
- **Las solicitudes de respuesta de la solicitud** se muestra el número total de llamadas de solicitud de respuesta que el servicio ha recibido en el período de tiempo seleccionado y error cuántos de ellos.
- **Tiempo medio de respuesta de solicitud calcular** muestra un promedio del tiempo necesario para ejecutar las solicitudes recibidas.
- **Las solicitudes de lote** muestra el número total de solicitudes de lote el servicio ha recibido en el período de tiempo seleccionado y error cuántos de ellos.
- **Latencia promedio de trabajo** muestra un promedio del tiempo necesario para ejecutar las solicitudes recibidas.
- **Errores** , se muestra el número total de errores que se han producido en las llamadas al servicio web.
- **Servicios de costos** muestra los cargos del plan de facturación asociado con el servicio.

Desde la página Configurar, puede actualizar las siguientes propiedades:

* **Descripción** permite escribir una descripción para el servicio Web. Descripción es un campo obligatorio.
* **Registro** permite habilitar o deshabilitar el registro en el extremo de errores. Para obtener más información sobre el registro, vea Habilitar [registro para los servicios web de aprendizaje del equipo](machine-learning-web-services-logging.md).
* **Datos de ejemplo habilitar** le permite proporcionar los datos de ejemplo que puede usar para probar el servicio de respuesta de la solicitud. Si ha creado el servicio web en equipo aprendizaje Studio, los datos de ejemplo se toman de los datos de su utilizados para entrenar el modelo. Si ha creado el servicio mediante programación, los datos se toman de los datos de ejemplo proporcionada como parte del paquete JSON.

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md
