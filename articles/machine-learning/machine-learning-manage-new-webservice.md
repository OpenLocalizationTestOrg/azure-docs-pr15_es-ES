<properties
    pageTitle="Administrar un servicio Web con el portal de Azure máquina aprendizaje Web (biofeedback) | Microsoft Azure"
    description="Administrar el acceso a áreas de trabajo de aprendizaje del equipo de Azure e implementar y administrar servicios web de API de m"
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="v-donglo"/>


# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Administrar un servicio Web con el portal de servicios Web de Azure máquina aprendizaje

Puede administrar su equipo aprendizaje nuevo y servicios Web clásica con el portal de servicios Web de Microsoft Azure máquina aprendizaje. Puesto que servicios Web clásica y servicios Web nueva se basan en las tecnologías subyacentes, tiene las capacidades de administración ligeramente diferente para cada uno de ellos.

En el portal de servicios Web de aprendizaje de máquina puede:

- Controlar cómo se utiliza el servicio web.
- Configurar la descripción, actualizar las claves para lo servicios web (solo nuevo), actualice su almacenamiento cuenta clave (solo para el nuevo), habilitar el registro y habilitar o deshabilitar los datos de ejemplo.
- Eliminar el servicio web.
- Crear, eliminar o actualizar facturación planes (solo nuevo).
- Agregar y eliminar los extremos (solo clásico)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="manage-new-web-services"></a>Administrar servicios Web nueva

Para administrar los servicios Web nuevo:

1.  Inicie sesión en el portal de [Servicios de Web de aprendizaje de Microsoft Azure equipo](https://services.azureml.net/quickstart) con su cuenta de Microsoft Azure: usar la cuenta que está asociada a la suscripción de Azure.
2.  En el menú, haga clic en **Servicios Web**.

Esto muestra una lista de servicios Web implementados para su suscripción. 

Para administrar un servicio Web, haga clic en servicios Web. Desde la página de servicios Web se puede:

- Haga clic en el servicio web para administrar.
- Haga clic en la facturación Plan para el servicio web para actualizarla.
- Eliminar un servicio web.
- Copiar un servicio web e implementar para otra región.

Al hacer clic en un servicio web, se abrirá la página del tutorial de servicio web. La página del tutorial de servicio web tiene dos opciones de menú que le permiten administrar el servicio web:

- **Panel** - le permite ver el uso del servicio Web.
- **Configurar** - le permite agregar texto descriptivo, actualice la clave para la cuenta de almacenamiento asociada con el servicio Web y habilitar o deshabilitar los datos de ejemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Cómo se utiliza el servicio web de supervisión ###

Haga clic en la pestaña **panel** .

Desde el panel, puede ver el uso general del servicio Web durante un período de tiempo. Puede seleccionar el período de ver desde el menú desplegable de período en la esquina superior derecha de los gráficos de uso. El panel muestra la siguiente información:

- **Solicitudes largo del tiempo** muestra un gráfico de paso del número de solicitudes durante el período de tiempo seleccionado. Puede ayudar a identificar si se producen picos en uso.
- **Las solicitudes de respuesta de la solicitud** se muestra el número total de llamadas de solicitud de respuesta que el servicio ha recibido en el período de tiempo seleccionado y error cuántos de ellos.
- **Tiempo medio de respuesta de solicitud calcular** muestra un promedio del tiempo necesario para ejecutar las solicitudes recibidas.
- **Las solicitudes de lote** muestra el número total de solicitudes de lote el servicio ha recibido en el período de tiempo seleccionado y error cuántos de ellos.
- **Latencia promedio de trabajo** muestra un promedio del tiempo necesario para ejecutar las solicitudes recibidas.
- **Errores** , se muestra el número total de errores que se han producido en las llamadas al servicio web.
- **Servicios de costos** muestra los cargos del plan de facturación asociado con el servicio.

### <a name="configuring-the-web-service"></a>Configuración del servicio web ###

Haga clic en la opción de menú **Configurar** .

Puede actualizar las siguientes propiedades:

* **Descripción** permite escribir una descripción para el servicio Web.
* **Título** le permite escribir un título para el servicio Web
* **Teclas** le permite girar las claves API primaria y secundarias.
* **Clave de cuenta de almacenamiento** le permite actualizar la clave para la cuenta de almacenamiento asociada con los cambios de servicio Web. 
* **Datos de ejemplo habilitar** le permite proporcionar los datos de ejemplo que puede usar para probar el servicio de respuesta de la solicitud. Si ha creado el servicio web en equipo aprendizaje Studio, los datos de ejemplo se toman de los datos de su utilizados para entrenar el modelo. Si ha creado el servicio mediante programación, los datos se toman de los datos de ejemplo proporcionada como parte del paquete JSON.

### <a name="managing-billing-plans"></a>Administrar planes de facturación ###

Haga clic en la opción de menú **planes** desde la página de tutoriales rápidos de servicios web. También puede hacer clic en el plan asociado con el servicio Web específico para administrar el plan.

* **Nuevo** le permite crear un nuevo plan.
* **Plan de agregar o quitar instancia** permite "escalar" un plan existente para agregar capacidad.
* **Actualización/degradación** permite "escalar" un plan existente para agregar capacidad.
* **Eliminar** permite eliminar un plan.

Haga clic en un plan para ver su escritorio. El panel le ofrece instantánea o plan de uso durante un período de tiempo seleccionado. Para seleccionar el período de tiempo para ver, haga clic en la lista desplegable de **período** en la esquina superior derecha del panel. 

El panel plan proporciona la información siguiente:

* **Descripción del plan de** muestra información sobre los costes y asociados con el plan de capacidad.
* **Plan de uso** muestra el número de transacciones y horas de cálculo que se haya aplicado con el plan.
* **Servicios Web** muestra el número de servicios Web que utilicen este plan.
* **Principio por llamadas al servicio Web** muestra los servicios de Web de principio cuatro que se realizan las llamadas que se cargan con el plan.
* **Servicios Web de principio por calcular Hrs** muestra los servicios de Web de principio cuatro que están usando los recursos de cálculo que se cargan con el plan.

## <a name="manage-classic-web-services"></a>Administrar servicios Web clásica

> [AZURE.NOTE] Los procedimientos de esta sección son relevantes para la administración de servicios web de clásico a través del portal de servicios Web de Azure máquina aprendizaje. Para obtener información sobre cómo administrar servicios Web clásica a través de estudio de aprendizaje de equipo y el portal de clásico Azure, consulte [administrar un área de trabajo de aprendizaje del equipo de Azure](machine-learning-manage-workspace.md).

Para administrar los servicios Web clásica:

1.  Inicie sesión en el portal de [Servicios de Web de aprendizaje de Microsoft Azure equipo](https://services.azureml.net/quickstart) con su cuenta de Microsoft Azure: usar la cuenta que está asociada a la suscripción de Azure.
2.  En el menú, haga clic en **Los servicios Web clásica**.

Para administrar un servicio Web clásico, haga clic en **Los servicios Web clásica**. Desde la página de los servicios Web clásica puede:

- Haga clic en el servicio web para ver los extremos asociados.
- Eliminar un servicio web.

Al administrar un servicio Web clásico, administrar cada uno de los extremos por separado. Al hacer clic en un servicio web en la página de servicios Web, se abrirá la lista de extremos asociado con el servicio. 

En la página de extremo de servicio de Web clásico, puede agregar y eliminar los extremos del servicio. Para obtener más información sobre cómo agregar extremos, vea [Crear extremos](machine-learning-create-endpoint.md).

Haga clic en uno de los extremos para abrir la página del tutorial de servicio web. En la página tutorial, hay dos opciones de menú que le permiten administrar el servicio web:

- **Panel** - le permite ver el uso del servicio Web.
- **Configurar** - le permite agregar texto descriptivo, activar el registro de errores y desactivar, actualización de la clave para el almacenamiento de la cuenta asociada con el servicio Web y habilitar y deshabilitar los datos de ejemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Cómo se utiliza el servicio web de supervisión ###

Haga clic en la pestaña **panel** .

Desde el panel, puede ver el uso general del servicio Web durante un período de tiempo. Puede seleccionar el período de ver desde el menú desplegable de período en la esquina superior derecha de los gráficos de uso. El panel muestra la siguiente información:

- **Solicitudes largo del tiempo** muestra un gráfico de paso del número de solicitudes durante el período de tiempo seleccionado. Puede ayudar a identificar si se producen picos en uso.
- **Las solicitudes de respuesta de la solicitud** se muestra el número total de llamadas de solicitud de respuesta que el servicio ha recibido en el período de tiempo seleccionado y error cuántos de ellos.
- **Tiempo medio de respuesta de solicitud calcular** muestra un promedio del tiempo necesario para ejecutar las solicitudes recibidas.
- **Las solicitudes de lote** muestra el número total de solicitudes de lote el servicio ha recibido en el período de tiempo seleccionado y error cuántos de ellos.
- **Latencia promedio de trabajo** muestra un promedio del tiempo necesario para ejecutar las solicitudes recibidas.
- **Errores** , se muestra el número total de errores que se han producido en las llamadas al servicio web.
- **Servicios de costos** muestra los cargos del plan de facturación asociado con el servicio.

### <a name="configuring-the-web-service"></a>Configuración del servicio web ###

Haga clic en la opción de menú **Configurar** .

Puede actualizar las siguientes propiedades:

* **Descripción** permite escribir una descripción para el servicio Web. Descripción es un campo obligatorio.
* **Registro** permite habilitar o deshabilitar el registro en el extremo de errores. Para obtener más información sobre el registro, vea Habilitar [registro para los servicios web de aprendizaje del equipo](machine-learning-web-services-logging.md).
* **Datos de ejemplo habilitar** le permite proporcionar los datos de ejemplo que puede usar para probar el servicio de respuesta de la solicitud. Si ha creado el servicio web en equipo aprendizaje Studio, los datos de ejemplo se toman de los datos de su utilizados para entrenar el modelo. Si ha creado el servicio mediante programación, los datos se toman de los datos de ejemplo proporcionada como parte del paquete JSON.

## <a name="grant-or-suspend-access-to-web-services-for-users-in-the-portal"></a>Conceder o suspender el acceso a servicios Web para los usuarios en el portal

El portal clásico Azure puede permitir o denegar el acceso a usuarios específicos.

### <a name="access-for-users-of-new-web-services"></a>Acceso a los usuarios de los servicios Web nueva

Para permitir que otros usuarios a trabajar con los servicios Web en el portal de servicios Web de Azure máquina aprendizaje, debe agregarlos como co-administradores de su suscripción de Azure.

Inicie sesión en el [portal de clásica Azure](https://manage.windowsazure.com/) con su cuenta de Microsoft Azure: usar la cuenta que está asociada a la suscripción de Azure.

1. En el panel de navegación, haga clic en **configuración**, haga clic en **administradores**.
2. En la parte inferior de la ventana, haga clic en **Agregar**. 
3. En el cuadro de diálogo ADD A CO-ADMINISTRATOR, escriba la dirección de correo electrónico de la persona que desea agregar como compañero administrador y, a continuación, seleccione la suscripción que desea que el administrador para obtener acceso a compañeros.
4. Haga clic en **Guardar**.

### <a name="access-for-users-of-classic-web-services"></a>Acceso a los usuarios de los servicios Web clásica

Para administrar un área de trabajo:

Inicie sesión en el [portal de clásica Azure](https://manage.windowsazure.com/) con su cuenta de Microsoft Azure: usar la cuenta que está asociada a la suscripción de Azure.

1. En el panel de servicios de Microsoft Azure, haga clic en **El aprendizaje**.
1. Haga clic en el área de trabajo que desee administrar.
1. Haga clic en la ficha **Configurar** .

En la pestaña Configuración, puede suspender el acceso al área de trabajo de aprendizaje de máquina haciendo clic en **Denegar**. Los usuarios ya no podrán abrir el área de trabajo en equipo aprendizaje Studio. Para restaurar el acceso, haga clic en **Permitir**.

Para usuarios específicos:

Para administrar cuentas adicionales que tienen acceso al área de trabajo en equipo aprendizaje Studio, haga clic en **iniciar sesión Studio de m** en la pestaña **panel** . Se abrirá el área de trabajo en equipo aprendizaje Studio. A continuación, haga clic en la pestaña **configuración** y, a continuación, **los usuarios**. Puede hacer clic en **Invitar a más usuarios** para conceder a los usuarios acceso al área de trabajo, o seleccione un usuario y haga clic en **Quitar**.

> [AZURE.NOTE] El vínculo **iniciar sesión m Studio** abre máquina aprendizaje Studio usando actualmente ha iniciado sesión en la Account de Microsoft. El Microsoft Account que usó para iniciar sesión en el portal de clásico Azure para crear un área de trabajo no tiene permiso para abrir dicha área de trabajo automáticamente. Para abrir un área de trabajo, debe haber iniciado sesión en la Account de Microsoft que se ha definido como propietario del área de trabajo o debe recibir una invitación del propietario para unir el área de trabajo.
