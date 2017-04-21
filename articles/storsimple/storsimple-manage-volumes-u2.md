<properties
   pageTitle="Administrar los volúmenes StorSimple (U2) | Microsoft Azure"
   description="Se explica cómo agregar, modificar, supervisar y eliminar volúmenes StorSimple y cómo poner sin conexión si es necesario."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/28/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-volumes-update-2"></a>Usar el servicio Administrador de StorSimple para administrar volúmenes (actualización 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Información general

En este tutorial se explica cómo usar el servicio Administrador de StorSimple para crear y administrar volúmenes en el dispositivo de StorSimple y un dispositivo virtual de StorSimple con Update 2 instalado.

El servicio Administrador de StorSimple es una extensión en el portal de clásica Azure que le permite administrar la solución de StorSimple desde una interfaz web única. Además de administrar volúmenes, puede usar el servicio Administrador de StorSimple para crear y administrar StorSimple servicios, ver y administrar dispositivos, ver alertas y ver y administrar las directivas de copia de seguridad y el catálogo de copia de seguridad.

## <a name="volume-types"></a>Tipos de volumen

Pueden ser StorSimple volúmenes:

- **Localmente anclados volúmenes**: datos en estos volúmenes permanecen en el dispositivo StorSimple local en todo momento.
- **Volúmenes en niveles**: pueden supresión de datos en estos volúmenes a la nube.

Un volumen archivado es un tipo de volumen en niveles. El tamaño del fragmento desduplicación mayor usado para volúmenes de archivado permite que el dispositivo transferir segmentos más grandes de datos en la nube. 

Si es necesario, puede cambiar el volumen del tipo de local a niveles o de niveles local. Para obtener más información, vaya a [cambiar el tipo de volumen](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Volúmenes localmente anclados

Volúmenes localmente anclados son totalmente aprovisionados volúmenes que no nivel datos en la nube, lo que garantiza local garantiza datos principal, independiente de conectividad de la nube. Datos de volúmenes localmente anclados no desduplicados y comprimir; Sin embargo, se desduplicados instantáneas de volúmenes anclados localmente. 

Localmente anclados volúmenes se configurarán por completo; por lo tanto, debe tener suficiente espacio en el dispositivo cuando se crean. Puede aprovisionar volúmenes localmente anclados hasta un tamaño máximo de 8 TB en el dispositivo StorSimple 8100 y en el dispositivo 8600 de 20 TB. StorSimple se reserva el espacio restante local en el dispositivo para instantáneas, metadatos y procesamiento de datos. Puede aumentar el tamaño de un volumen localmente anclado en el espacio máximo disponible, pero no puede reducir el tamaño de un volumen una vez creado.

Cuando se crea un volumen localmente anclado, se reduce el espacio disponible para la creación de volúmenes en niveles. Lo contrario también es cierto: si tiene volúmenes en niveles existentes, el espacio disponible para crear localmente anclados volúmenes será inferior a los límites máximos indicados anteriormente. Para obtener más información sobre volúmenes locales, consulte las [preguntas más frecuentes sobre volúmenes localmente anclados](storsimple-local-volume-faq.md).   

### <a name="tiered-volumes"></a>Volúmenes en niveles

Volúmenes en niveles son thin aprovisionados volúmenes en los que los datos de acceso más frecuente sean locales en el dispositivo y menos datos utilizados con frecuencia es niveles automáticamente en la nube. Aprovisionamiento fino es una tecnología de virtualización en la que almacenamiento disponible aparece supere los recursos físicos. En lugar de reservar suficiente espacio de almacenamiento por adelantado, StorSimple utiliza aprovisionamiento fina asignar suficiente espacio para satisfacer requisitos actuales. La naturaleza de almacenamiento de nube elástica facilita este enfoque porque StorSimple puede aumentar o disminuir el almacenamiento de la nube para satisfacer las necesidades cambiantes.

Si está utilizando el volumen en niveles de datos archivados, seleccionar la casilla de verificación **usar este volumen para menos frecuente datos archivados** cambia el tamaño del fragmento deduplicación para el volumen a 512 KB. Si no selecciona esta opción, el volumen correspondiente en niveles utiliza un tamaño de fragmentos de 64 KB. Un tamaño de fragmento desduplicación permite que el dispositivo acelerar a la transferencia de datos de archivado de gran tamaño en la nube.

>[AZURE.NOTE] Archivado volúmenes creados con una versión 2 anteriores a la actualización de StorSimple se importará como en niveles con la casilla de verificación archivo seleccionada.

### <a name="provisioned-capacity"></a>Capacidad de preparación

Consulte la siguiente tabla para la capacidad máxima de preparación para cada tipo de dispositivo y volumen. (Tenga en cuenta que localmente anclados volúmenes no están disponibles en un dispositivo virtual).

|             | Tamaño máximo de niveles de volumen | Máximo localmente anclada tamaño de volumen |
|-------------|----------------------------|------------------------------------|
| **Dispositivos físicos** |       |       |
| 8100                 | 64 TB | 8 TB |
| 8600                 | 64 TB | 20 TB |
| **Dispositivos virtuales**  |       |       |
| 8010                | 30 TB | N/A.   |
| 8020               | 64 TB | N/A.   |

## <a name="the-volumes-page"></a>La página de volúmenes

La página de **volúmenes** le permite administrar los volúmenes de almacenamiento que se incluye en el dispositivo de Microsoft Azure StorSimple para su iniciadores (servidores). Muestra la lista de volúmenes en el dispositivo StorSimple.

 ![Página de volúmenes](./media/storsimple-manage-volumes-u2/VolumePage.png)

Un volumen consta de una serie de atributos:

- **Nombre de volumen** : un nombre descriptivo que debe ser únicos y ayuda a identificar el volumen. Este nombre también se usa en informes de supervisión cuando aplica un filtro en un volumen específico.

- **Estado** : puede ser en línea o sin conexión. Si un volumen está sin conexión, no está visible para iniciadores (servidores) que se permiten el acceso usar el volumen.

- **Capacidad** : especifica la cantidad total de datos que se pueden almacenar el iniciador (servidor). Localmente anclados volúmenes se configurarán por completo y residen en el dispositivo StorSimple. Thin aprovisionados volúmenes en niveles y los datos se desduplicados. Con volúmenes de thin aprovisionados, el dispositivo no asigna previamente capacidad de almacenamiento físico internamente o en la nube según la capacidad de volumen configurado. La capacidad de volumen se asignan y consumida a petición.

- **Tipo** : indica si el volumen está **en niveles** (opción predeterminada) o **anclados localmente**.

- **Copia de seguridad** : indica si existe una directiva de copia de seguridad predeterminados para el volumen.

- **Access** : especifica los iniciadores (servidores) que se permiten el acceso a este volumen. Iniciadores que no son miembros de registro de control de acceso (ACR) que está asociado con el volumen no verán el volumen.

- **Supervisión** : Especifica si se está controlando un volumen. Un volumen tendrá supervisión habilitado de forma predeterminada cuando se crea. Supervisión obtendrá, sin embargo, se deshabilita para un duplicado de volumen. Para habilitar la supervisión de un volumen, siga las instrucciones en el [Monitor de un volumen](#monitor-a-volume). 

Utilice las instrucciones de este tutorial para realizar las siguientes tareas:

- Agregar un volumen 
- Modificar un volumen 
- Cambiar el tipo de volumen
- Eliminar un volumen 
- Poner un volumen sin conexión 
- Supervisar un volumen 

## <a name="add-a-volume"></a>Agregar un volumen

[Crear un volumen](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume) durante la implementación de la solución StorSimple. Agregar un volumen es un procedimiento similar.

#### <a name="to-add-a-volume"></a>Para agregar un volumen

1. En la página de **dispositivos** , seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volumen** .

2. Seleccione un contenedor de volumen de la lista y haga doble clic en él para acceder a los volúmenes asociados al contenedor.

3. Haga clic en **Agregar** en la parte inferior de la página. Agregar que un asistente de volumen.

     ![Agregar el Asistente para configuración básica de volumen](./media/storsimple-manage-volumes-u2/TieredVolEx.png)

4. En el asistente Agregar un volumen, en **Configuración básica**, haga lo siguiente:

  1. Proporcione un **nombre** para el volumen.
  2. Seleccione un **Tipo de uso** de la lista desplegable. Para cargas de trabajo que requieren datos estén disponibles localmente en el dispositivo en todo momento, seleccione **Anclado localmente**. Para los demás tipos de datos, seleccione **en niveles**. (**En niveles** es el valor predeterminado).
  3. Si ha seleccionado **en niveles** en el paso 2, puede seleccionar la casilla de verificación **usar este volumen para menos frecuente datos archivados** para configurar un volumen archivado.
  4. Escriba la **Capacidad de aprovisionar** para el volumen en GB o TB. Consulte [Provisioned capacidad](#provisioned-capacity) para el tamaño máximo de cada tipo de dispositivo y volumen. Mire la **Capacidad disponible** para determinar cuánto almacenamiento está disponible en el dispositivo.

5. Haga clic en el icono de flecha![Icono de flecha](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Si está configurando un volumen localmente anclado, verá el siguiente mensaje.

    ![Cambiar el mensaje de tipo de volumen](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
   
5. Haga clic en el icono de flecha ![icono de flecha](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png)nuevamente para ir a la página **Configuración adicional** .

    ![Agregar opciones de configuración adicionales del Asistente de volumen](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>

6. En **Configuración adicional**, agregue un nuevo registro de control de acceso (ACR):
  
  1. Seleccione un registro de control de acceso (ACR) de la lista desplegable. Como alternativa, puede agregar un nuevo ACR. ACRs determinar qué hosts pueden tener acceso a los volúmenes que coincidan con el host IQN con la que aparece en el registro. Si no especifica un ACR, verá el siguiente mensaje.

        ![Especificar ACR](./media/storsimple-manage-volumes-u2/SpecifyACR.png)

  2. Le recomendamos que seleccione la casilla de verificación **Habilitar una copia de seguridad predeterminado para este volumen** .
  3. Haga clic en el icono de verificación ![Icono de comprobación](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) Para crear el volumen con la configuración especificada.

Ahora está listo para usar el volumen nuevo.

>[AZURE.NOTE] Si crea un volumen localmente anclado y, a continuación, crear otro volumen localmente anclada inmediatamente después, la creación de volumen trabajos se ejecutan secuencialmente. La primera tarea de creación de volumen debe finalizar para que pueda empezar la siguiente tarea de creación de volumen.

## <a name="modify-a-volume"></a>Modificar un volumen

Modificar un volumen cuando tenga que expandir o cambiar los hosts que tener acceso al volumen.

> [AZURE.IMPORTANT] 
>
> - Si modifica el tamaño del volumen en el dispositivo, debe modificarse en el host también el tamaño del volumen. 
> - Los pasos del host que se describen aquí son para Windows Server 2012 (2012R2). Procedimientos para Linux u otros sistemas operativos de host será diferentes. Consulte las instrucciones de sistema operativo host cuando se modifica el volumen en un host de otro sistema operativo. 

#### <a name="to-modify-a-volume"></a>Para modificar un volumen

1. En la página de **dispositivos** , seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volumen** .

2. Seleccione un contenedor de volumen de la lista y haga doble clic en él para ver los volúmenes asociados con el contenedor.

3. Seleccione un volumen y en la parte inferior de la página, haga clic en **Modificar**. Se inicia el Asistente Modificar volumen.

4. En el Asistente de volumen modificar, en **Configuración básica**, puede hacer lo siguiente:

  - Editar el **nombre**.
  - Convertir el **Tipo de uso** de localmente anclados a niveles o de niveles a localmente anclados (consulte [cambiar el tipo de volumen](#change-the-volume-type) para obtener más información).
  - Aumentar la **capacidad de aprovisionar**. Solo se puede aumentar la **Capacidad se aprovisione** . No puede comprimir un volumen después de crearlo.

5. En **Configuración adicional**, puede modificar el ACR, siempre que el volumen está sin conexión. Si el volumen está en línea, debe ponerlo sin conexión en primer lugar. Antes de modificar el ACR, consulte los pasos de [tomar un volumen sin conexión](#take-a-volume-offline) .

    > [AZURE.NOTE] No puede cambiar la opción **Habilitar una copia de seguridad predeterminados** para el volumen.

6. Guardar los cambios, haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png). El portal clásico Azure mostrará un mensaje de volumen actualización. Mostrará un mensaje de éxito cuando se ha actualizado correctamente el volumen.

7. Si se expande un volumen, siga los pasos siguientes en el equipo del host de Windows:

   1. Vaya a **administración de equipos** ->**administración de disco**.
   2. Haga clic en **Administración de discos** y seleccione **Volver a examinar los discos**.
   3. En la lista de discos, seleccione el volumen que haya actualizado, con el botón secundario y, a continuación, seleccione **Extender volumen**. Se inicia el Asistente para extender volumen. Haga clic en **siguiente**.
   4. Complete el asistente, acepte los valores predeterminados. Una vez finalizado el asistente, el volumen debe mostrar el aumento de tamaño.

    >[AZURE.NOTE] Si expanda un volumen localmente anclado y otro localmente había anclada volumen inmediatamente después, ejecutan secuencialmente los trabajos de expansión de volumen. La primera tarea de expansión de volumen debe finalizar para que pueda empezar la siguiente tarea de expansión de volumen.

![Vídeo disponible](./media/storsimple-manage-volumes-u2/Video_icon.png) **vídeo disponible**

Para ver un vídeo que muestra cómo expandir un volumen, haga clic en [aquí](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="change-the-volume-type"></a>Cambiar el tipo de volumen

Puede cambiar el tipo de volumen de niveles a anclados localmente o desde localmente anclados a niveles. Sin embargo, esta conversión no debe ser una repetición frecuentes. Algunas de las razones para convertir un volumen de niveles a localmente anclados son:

- Garantía local relativa a la disponibilidad de los datos y rendimiento
- Eliminación de latencia de la nube y problemas de conectividad de la nube.

Normalmente, estas son pequeñas volúmenes existentes que desea tener acceso con frecuencia. Un volumen localmente anclado se aprovisiona totalmente cuando se crea. Si va a convertir un volumen en niveles a un volumen localmente anclado, StorSimple comprueba que tiene suficiente espacio en el dispositivo antes de iniciar la conversión. Si tiene suficiente espacio, recibirá un error y se cancelará la operación. 

> [AZURE.NOTE] Antes de comenzar la conversión de niveles a localmente anclados, asegúrese de tener en cuenta los requisitos de espacio de sus otras cargas de trabajo. 

Desea cambiar un volumen localmente anclado a un volumen en niveles si necesita más espacio para aprovisionar otros volúmenes. Al convertir el volumen localmente anclado a niveles, la capacidad disponible en el dispositivo de incremento por el tamaño de la capacidad de lanzamiento. Si tiene problemas de conectividad evitar la conversión de un volumen del tipo local al tipo de niveles, el volumen local va a mostrar propiedades de un volumen en niveles hasta que se complete la conversión. Esto es porque podrían hayan esparcido algunos datos en la nube. Estos datos derramados seguirá ocupar espacio local en el dispositivo que no se puede liberar hasta que se reinicia y completar la operación.

>[AZURE.NOTE] Convertir un volumen puede tardar algún tiempo y no se puede cancelar una conversión después de que se inicia. El volumen permanece en línea durante la conversión y puede realizar copias de seguridad, pero no se puede expandir o restaurar el volumen mientras produce la conversión.  

Conversión de una en niveles a un volumen localmente anclada negativo puede afectar al rendimiento del dispositivo. Además, los siguientes factores pueden aumentar el tiempo que se tarda en completar la conversión:

- No hay suficiente ancho de banda.

- No hay ninguna copia de seguridad actual.

Para minimizar los efectos de estos factores:

- Revise el ancho de banda directivas y asegúrese de que esté disponible un ancho de banda Mbps 40 dedicado.
- Programar la conversión para las horas.
- Tomar una instantánea de la nube antes de iniciar la conversión.

Si va a convertir varios volúmenes (compatible con diferentes cargas de trabajo), debe priorizar la conversión de volumen para volúmenes mayor prioridad se convierten en primer lugar. Por ejemplo, debe convertir volúmenes que hospedan máquinas virtuales (VM) o volúmenes con cargas de trabajo SQL antes de convertir volúmenes con las cargas de trabajo del recurso compartido de archivos.

#### <a name="to-change-the-volume-type"></a>Para cambiar el tipo de volumen

1. En la página de **dispositivos** , seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volumen** .

2. Seleccione un contenedor de volumen de la lista y haga doble clic en él para ver los volúmenes asociados con el contenedor.

3. Seleccione un volumen y en la parte inferior de la página, haga clic en **Modificar**. Se inicia el Asistente Modificar volumen.

4. En la página **Configuración básica** , cambie el tipo de uso, seleccione el nuevo tipo de la lista desplegable **Tipo de uso** .

    - Si va a cambiar el tipo a **localmente anclados**, StorSimple comprueba si hay suficiente capacidad.
    - Si va a cambiar el tipo a **en niveles** y utilizará este volumen para archivados datos, seleccione la casilla de verificación **usar este volumen para menos frecuente datos archivados** .

        ![Casilla de verificación Archivar](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)

5. Haga clic en el icono de flecha ![icono de flecha](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) para ir a la página **Configuración adicional** . Si está configurando un volumen localmente anclado, aparece el siguiente mensaje.

    ![Cambiar el mensaje de tipo de volumen](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)

6. Haga clic en el icono de flecha ![icono de flecha](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) Para continuar.

7. Haga clic en el icono de verificación ![Icono de comprobación](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) Para iniciar el proceso de conversión. El portal de Azure mostrará un mensaje de volumen actualización. Mostrará un mensaje de éxito cuando se ha actualizado correctamente el volumen.

## <a name="take-a-volume-offline"></a>Poner un volumen sin conexión

Debe desconectar un volumen al que va a modificar o eliminar. Cuando un volumen está sin conexión, no está disponible para el acceso de lectura y escritura. Debe tener el volumen sin conexión en el host, así como en el dispositivo. 

#### <a name="to-take-a-volume-offline"></a>Poner un volumen sin conexión

1. Asegúrese de que el volumen en cuestión no está en uso antes de tomar de sin conexión.

2. Tomar el volumen sin conexión en el host de la primera. Esto elimina cualquier riesgo de daños en los datos en el volumen. Para pasos específicos, consulte las instrucciones para su sistema operativo de host.

3. Cuando el host está sin conexión, pase el volumen del dispositivo sin conexión realizando los siguientes pasos:

  1. En la página de **dispositivos** , seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volumen** . La ficha **Volumen contenedores** listas en un formato tabular todos los contenedores de volumen que están asociados con el dispositivo.
  2. Seleccione un contenedor de volumen y haga clic en él para mostrar la lista de todos los volúmenes dentro del contenedor.
  3. Seleccione un volumen y haga clic en **trabajar sin conexión**.
  4. Cuando se le solicite confirmación, haga clic en **Sí**. El volumen ahora debería ser sin conexión.

    Después de un volumen está sin conexión, estará disponible la opción **Poner en línea** .

> [AZURE.NOTE] El comando **Tomar sin conexión** , envía una solicitud al dispositivo para poner el volumen sin conexión. Si hosts sigue usando el volumen, el resultado es conexiones interrumpidas pero no desconectar el volumen producirá. 

## <a name="delete-a-volume"></a>Eliminar un volumen

> [AZURE.IMPORTANT] Puede eliminar un volumen únicamente si está sin conexión.

Complete los pasos siguientes para eliminar un volumen.

#### <a name="to-delete-a-volume"></a>Para eliminar un volumen

1. En la página de **dispositivos** , seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volumen** .

2. Seleccione el contenedor de volumen que tiene el volumen que desea eliminar. Haga clic en el contenedor de volumen para obtener acceso a la página de **volúmenes** .

3. Todos los volúmenes asociados a este contenedor se muestran en un formato tabular. Comprobar el estado del volumen que desea eliminar. Si el volumen que desea eliminar no está sin conexión, ponerlo sin conexión en primer lugar, siguiendo los pasos de [tomar un volumen sin conexión](#take-a-volume-offline).

4. Después de que el volumen está sin conexión, haga clic en **Eliminar** en la parte inferior de la página.

5. Cuando se le solicite confirmación, haga clic en **Sí**. Ahora se eliminará el volumen y la página de **volúmenes** mostrará la lista actualizada de volúmenes dentro del contenedor.

    >[AZURE.NOTE]Si elimina un volumen localmente anclado, puede que el espacio disponible para los nuevos volúmenes no se pueden actualizar inmediatamente. El servicio Administrador de StorSimple actualiza periódicamente el espacio local disponible. Le recomendamos que espere unos minutos antes de intentar crear el nuevo volumen.<br> Además, si elimina un volumen localmente anclado y, a continuación, eliminar otro localmente anclada volumen inmediatamente después, la eliminación de volumen trabajos se ejecutan secuencialmente. La primera tarea de eliminación de volumen debe finalizar para que pueda empezar la siguiente tarea de eliminación de volumen.
 
## <a name="monitor-a-volume"></a>Supervisar un volumen

Supervisión de volumen le permite recopilar estadísticas O relacionados con/para un volumen. Control está habilitado de forma predeterminada para los primeros 32 volúmenes que cree. Supervisión de volúmenes adicionales está desactivada de forma predeterminada. Supervisión de volúmenes duplicados también está deshabilitada de forma predeterminada.

Realice los pasos siguientes para habilitar o deshabilitar la supervisión de un volumen.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Para habilitar o deshabilitar el control de volumen

1. En la página de **dispositivos** , seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volumen** .

2. Seleccione el contenedor de volumen en la que reside el volumen y, a continuación, haga clic en el contenedor de volumen para obtener acceso a la página de **volúmenes** .

3. Todos los volúmenes asociados a este contenedor aparecen en la presentación tabular. Haga clic en y seleccione el volumen o clonar de volumen.

4. En la parte inferior de la página, haga clic en **Modificar**.

5. En el Asistente Modificar volumen, en **Configuración básica**, seleccione **Habilitar** o **Deshabilitar** de la lista desplegable de **supervisión** .

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [clonar un volumen StorSimple](storsimple-clone-volume.md).

- Obtenga información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).

 
