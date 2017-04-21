<properties
   pageTitle="Administrar las plantillas de ancho de banda StorSimple | Microsoft Azure"
   description="Describe cómo administrar plantillas de ancho de banda de StorSimple, que le permiten controlar el consumo de ancho de banda."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storsimple-bandwidth-templates"></a>Usar el servicio Administrador de StorSimple para administrar plantillas de ancho de banda de StorSimple

## <a name="overview"></a>Información general

Plantillas de ancho de banda permiten configurar el uso de ancho de banda de red a través de varias programaciones de hora del día a los datos desde el dispositivo StorSimple en la nube de nivel.

Las programaciones de ancho de banda puede:

- Especifique las programaciones de ancho de banda personalizados según los usos de la red de carga de trabajo.

- Administración centralizada y reutilizar las programaciones en varios dispositivos de forma fácil y transparente.

> [AZURE.NOTE] Esta característica está disponible sólo para dispositivos físicos de StorSimple y no para dispositivos virtuales.

Todas las plantillas de ancho de banda de su servicio se muestran en un formato tabular y contienen la información siguiente:

- **Nombre** : un nombre único asignado a la plantilla de ancho de banda cuando se creó.

- **Programación** : el número de programaciones contenidos en una plantilla de ancho de banda determinado.

- **Utilizado por** : el número de volúmenes usando las plantillas de ancho de banda.

Use **la página Administrador de StorSimple servicio** en el portal de clásico Azure para administrar plantillas de ancho de banda.

También puede encontrar información adicional para ayudar a configurar plantillas de ancho de banda en:

- Preguntas y respuestas acerca de las plantillas de ancho de banda
- Prácticas recomendadas para plantillas de ancho de banda

## <a name="add-a-bandwidth-template"></a>Agregar una plantilla de ancho de banda

Realice los pasos siguientes para crear una nueva plantilla de ancho de banda.

#### <a name="to-add-a-bandwidth-template"></a>Para agregar una plantilla de ancho de banda

1. En **la página Administrador de StorSimple servicio,** haga clic en **Agregar o Editar plantilla de ancho de banda**.

2. En el cuadro de diálogo **Agregar o Editar plantilla de ancho de banda** :

   1. En la lista desplegable **plantilla** , seleccione **Crear nuevo** para agregar una nueva plantilla de ancho de banda.
   2. Especifique un nombre único para la plantilla de ancho de banda.

3. Definir una **programación de ancho de banda**. Para crear una programación:

   1. En la lista desplegable, elija los días de la semana en para que la programación está configurada. Puede seleccionar varios días seleccionando las casillas de verificación situadas antes de los días correspondientes en la lista.
   2. Seleccione la opción de **Todo el día** si se aplica la programación de todo el día. Cuando esté activada esta opción, ya no puede especificar una **Hora de inicio** o una **Hora de finalización**. Se ejecuta la programación de 12:00 A.M. a 11:59 PM.
   3. En la lista desplegable, seleccione una **Hora de inicio**. Esto es cuando comenzará la programación.
   4. En la lista desplegable, seleccione una **Hora de finalización**. Esto es cuando se detendrá la programación.

         > [AZURE.NOTE] No se permiten programaciones superpuestas. Si las horas de inicio y finalización se traducirán en un programa de superposición, verá un mensaje de error al efecto.

   5. Especifique la **tasa de ancho de banda**. Este es el ancho de banda en Megabits por segundo (Mbps) usado por el dispositivo StorSimple en operaciones relacionadas con la nube (cargas y descargas). Proporcione un número comprendido entre 1 y 1.000 para este campo.

   6. Haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). La plantilla que haya creado se agregará a la lista de plantillas de ancho de banda en la página de **configuración** de servicio.

    ![Crear nueva plantilla de ancho de banda](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)

4. Haga clic en **Guardar** en la parte inferior de la página y, a continuación, haga clic en **Sí** cuando se le solicite confirmación. Esto guarde los cambios de configuración que haya realizado.

## <a name="edit-a-bandwidth-template"></a>Editar una plantilla de ancho de banda

Siga estos pasos para editar una plantilla de ancho de banda.

### <a name="to-edit-a-bandwidth-template"></a>Para editar una plantilla de ancho de banda

1. Haga clic en **Agregar o Editar plantilla de ancho de banda**.

2. En el cuadro de diálogo **Agregar o Editar plantilla de ancho de banda** :

   1. En la lista desplegable de **plantilla** , elija una plantilla de ancho de banda existente que desea modificar.
   2. Complete los cambios. (Puede modificar cualquiera de las configuraciones existentes.)
   3. Haga clic en el icono de verificación ![Icono de comprobación](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Verá la plantilla modificada en la lista de plantillas de ancho de banda en la página de configuración de servicio.

3. Para guardar los cambios, haga clic en **Guardar** en la parte inferior de la página. Haga clic en **Sí** cuando se le solicite confirmación.

> [AZURE.NOTE] No podrá guardar los cambios, si la programación modificada se superpone con una programación existente en la plantilla de ancho de banda que desea modificar.

## <a name="delete-a-bandwidth-template"></a>Eliminar una plantilla de ancho de banda

Siga estos pasos para eliminar una plantilla de ancho de banda.

#### <a name="to-delete-a-bandwidth-template"></a>Para eliminar una plantilla de ancho de banda

1. En la lista tabular de las plantillas de ancho de banda de su servicio, seleccione la plantilla que desea eliminar. Aparecerá un icono de eliminar (**x**) al extremo derecho de la plantilla seleccionada. Haga clic en el icono **x** para eliminar la plantilla.

2. Se le pedirá confirmación. Haga clic en **Aceptar** para continuar.

Si la plantilla está en uso por cualquier volúmenes, no podrá eliminarlo. Verá un mensaje de error que indica que la plantilla está en uso. Aparece un cuadro de diálogo de mensaje de error le informa de que se deben quitar todas las referencias a la plantilla.

Puede eliminar todas las referencias a la plantilla de acceso a la página de **Volumen contenedores** y modificando los contenedores de volumen que use esta plantilla para que use otra plantilla o usar una configuración personalizada o ilimitado de ancho de banda. Cuando todas las referencias se han quitado, puede eliminar la plantilla.

## <a name="use-a-default-bandwidth-template"></a>Usar una plantilla de ancho de banda predeterminada

Una plantilla de ancho de banda predeterminada se proporciona y usa contenedores de volumen de forma predeterminada para aplicar controles de ancho de banda al acceder a la nube. La plantilla predeterminada también sirve como una referencia lista para los usuarios que creen sus propias plantillas. Se muestran los detalles de esta plantilla predeterminada:

- **Nombre** : ilimitadas noches y los fines de semana

- **Programación** : una única programación del lunes al viernes que aplica la velocidad de ancho de banda de 1 Mbps entre 8 A.M. y 5 hora del dispositivo. El ancho de banda se establece en sin límite para el resto de la semana.

Se puede modificar la plantilla predeterminada. Se realiza un seguimiento del uso de esta plantilla (incluidos versiones editadas).

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Crear una plantilla de ancho de banda de todo el día que comienza en un momento determinado

Siga este procedimiento para crear una programación que se inicia en un momento determinado y ejecuta todo el día. En el ejemplo, la programación comienza en 9 AM por la mañana y se ejecuta hasta 9 la Mañana siguiente. Es importante que tenga en cuenta que las horas de inicio y finalización de la programación de un determinado ambos se incluirán en la misma programación de 24 horas y no puede abarcar varios días. Si necesita configurar plantillas de ancho de banda que ocupan varios días, debe usar varias programaciones (como se muestra en el ejemplo).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Para crear una plantilla de ancho de banda de todo el día

1. Crear una programación que comienza en 9 AM por la mañana y se ejecuta hasta la medianoche.

2. Agregar otra programación. Configurar la segunda programación para ejecutar desde medianoche hasta las 9 A.M. por la mañana.

3. Guardar la plantilla de ancho de banda.

La programación de composición, a continuación, se iniciará en uno de su elección y ejecutar todo el día.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Preguntas y respuestas acerca de las plantillas de ancho de banda

**Q**. ¿Qué sucede a los controles de ancho de banda cuando haya entre las programaciones? (Una programación ha finalizado y otra aún no ha iniciado).

**A**. En estos casos, no también se utilizará ningún control de ancho de banda. Esto significa que el dispositivo puedan usar ancho de banda ilimitado cuando niveles de datos en la nube.

**Q**. ¿Puede modificar las plantillas de ancho de banda en un dispositivo sin conexión?

**A**. No podrá modificar las plantillas de ancho de banda en contenedores de volúmenes si el dispositivo correspondiente está desconectado.

**Q**. ¿Puede editar una plantilla de ancho de banda asociada a un contenedor de volumen cuando los volúmenes asociados están sin conexión?

**A**. Puede modificar una plantilla de ancho de banda asociada a un contenedor de volumen cuyos volúmenes están desconectados. Tenga en cuenta que cuando los volúmenes sin conexión, datos no se pueden niveles desde el dispositivo a la nube.

**Q**. ¿Puede eliminar una plantilla predeterminada?

**A**. Aunque puede eliminar una plantilla predeterminada, no es una buena idea hacerlo. El uso de una plantilla predeterminada, incluidas las versiones editadas, se realiza un seguimiento. Los datos de seguimiento se analizan y a lo largo del tiempo, se usan para mejorar la plantilla predeterminada.

**Q**. ¿Cómo determinar que es necesario modificar las plantillas de ancho de banda?

**A**. Uno de los signos que necesite modificar las plantillas de ancho de banda es al empezar a ver la red ralentizar o reventado varias veces en un día. Si esto sucede, supervisar la red de almacenamiento y uso consultando los gráficos de rendimiento y rendimiento de la red.

Identificar la hora del día y los contenedores de volumen en el que se produce la botella en la red desde los datos de rendimiento de red. Si esto sucede cuando se está en niveles de datos en la nube (Obtenga esta información de rendimiento para todos los contenedores de volumen para el dispositivo a la nube), debe modificar las plantillas de ancho de banda asociadas con los contenedores de volumen.

Después de las plantillas de modificación en uso, debe supervisar la red nuevamente para latencia significativa. Si estos todavía existen, deberá volver a examinar las plantillas de ancho de banda.

**Q**. ¿Qué sucede si tienen varios contenedores de volumen en mi dispositivo programa esa superposición pero diferentes límites se aplican a cada uno?

**A**. Supongamos que tiene un dispositivo con 3 contenedores de volumen. Las programaciones asociadas a estos contenedores completamente superponer. Para cada uno de estos contenedores, los límites de ancho de banda usados son 5, 10 y 15 Mbps respectivamente. Cuando i/OS se producen en todos los contenedores al mismo tiempo, se puede aplicar el mínimo de los límites de ancho de banda de 3: en este caso, 5 Mbps como estos saliente solicitudes de i/OS compartir la misma cola.

## <a name="best-practices-for-bandwidth-templates"></a>Prácticas recomendadas para plantillas de ancho de banda

Siga estos procedimientos recomendados para su dispositivo StorSimple:

- Configurar plantillas de ancho de banda en el dispositivo para habilitar variable límite del rendimiento de red por el dispositivo en otro momento del día. Estas plantillas de ancho de banda cuando se utiliza con las programaciones de copia de seguridad eficaz pueden aprovechar el ancho de banda de red adicional para operaciones de nube durante las horas.

- Calcular el ancho de banda real necesario para una implementación concreta en función del tamaño de la implementación y el objetivo de tiempo de recuperación necesarios (RTO).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).
