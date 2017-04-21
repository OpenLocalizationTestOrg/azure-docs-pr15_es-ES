<properties 
   pageTitle="StorSimple localmente anclados volúmenes preguntas más frecuentes | Microsoft Azure"
   description="Proporcionan respuestas a las preguntas más frecuentes sobre volúmenes StorSimple anclado localmente."
   services="storsimple"
   documentationCenter="NA"
   authors="manuaery"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/16/2016"
   ms.author="manuaery" />

# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple localmente anclados volúmenes: preguntas más frecuentes (P+F)

## <a name="overview"></a>Información general

Los siguientes son preguntas y respuestas que podría tener al crear un volumen StorSimple localmente anclado, convertir un volumen en niveles en un volumen localmente anclado (y viceversa), o realizar copias de seguridad y restaurar un volumen localmente anclado.

Preguntas y respuestas se organizan en las siguientes categorías

- Creación de un volumen localmente anclado
- Copia de seguridad de un equipo local anclada
- Convertir un volumen en niveles a un volumen localmente anclado
- Restaurar un volumen localmente anclado
- Error sobre un volumen localmente anclado

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Preguntas sobre la creación de un volumen localmente anclado

**PREGUNTAS.** ¿Qué es el tamaño máximo de un volumen localmente anclado que puedo crear en los dispositivos de 8000 serie?

**Respuestas** pueden aprovisionar volúmenes localmente anclados 8,5 TB o volúmenes en niveles en el dispositivo 8100 hasta 200 TB. En el dispositivo 8600 más grande, puede aprovisionar volúmenes localmente anclados 22,5 TB o volúmenes en niveles hasta 500 TB.

**PREGUNTAS.** Recientemente he actualizado mi dispositivo 8100 a 2 de actualización y al intentar crear un volumen localmente anclado, el tamaño máximo disponible sólo está 6 TB y no 8,5 TB. ¿Por qué no puedo crear un volumen 8,5 TB?

**Respuestas** pueden aprovisionar volúmenes localmente anclados hasta 8.5 TB o niveles volúmenes de hasta 200 TB en el dispositivo 8100. Si el dispositivo ya ha niveles volúmenes, a continuación, en el espacio disponible para crear un volumen localmente anclado será proporcionalmente inferior a este límite máximo. Por ejemplo, si ya se hayan aprovisionado el 100 TB de volúmenes en niveles en el dispositivo 8100 (que es la mitad de la capacidad en niveles), a continuación, el tamaño máximo de un volumen local que puede crear en el dispositivo 8100 proporcionalmente disminuirá a 4 TB (aproximadamente la mitad de la máxima localmente anclados capacidad de volumen).

Espacio en el dispositivo local se usa para hospedar el conjunto de trabajo de volúmenes en niveles, se reduce el espacio disponible para crear un volumen localmente anclado si el dispositivo ha niveles volúmenes. Por el contrario, reduce el espacio disponible para volúmenes en niveles creando un volumen localmente anclado proporcionalmente. La siguiente tabla resume los niveles de capacidad disponibles en los dispositivos 8100 y 8600 cuando se crean volúmenes anclados localmente.

|Capacidad de preparación volúmenes localmente anclada|Capacidad disponible estén configurados para volúmenes en niveles - 8100|Capacidad disponible estén configurados para volúmenes en niveles - 8600|
|-----|------|------|
|0 | 200 GB | 500 TB |
|1 TB | 176.5 TB | 477.8 TB|
|4 TB | 105.9 TB | 411.1 TB |
|8.5 TB | TB 0 | 311.1 TB|
|10 TB | NOD | 277.8 TB |
|15 TB | NOD | 166.7 TB |
|22.5 TB | NOD | TB 0 |


**PREGUNTAS.** ¿Por qué crear volumen localmente anclada una operación larga? 

**A.** Grosor aprovisionados volúmenes anclados localmente. Para crear espacio en los niveles locales del dispositivo, algunos datos de volúmenes en niveles existentes pueden insertarse en la nube durante el proceso de aprovisionamiento. Y dado que esto depende del tamaño del volumen que se aprovisiona, los datos existentes en el dispositivo y el ancho de banda disponible en la nube, el tiempo necesario para crear un volumen local puede ser varias horas.

**PREGUNTAS.** ¿Cuánto tiempo tarda para crear un volumen localmente anclado?

**A.** Porque volúmenes localmente anclados grosor aprovisionados, algunos datos existentes de volúmenes en niveles podrían insertarse en la nube durante el proceso de aprovisionamiento. Por lo tanto, el tiempo necesario para crear un volumen localmente anclado depende de varios factores, incluido el tamaño del volumen, los datos en el dispositivo y el ancho de banda disponible. En un dispositivo recién instalado que no tiene volúmenes, el tiempo para crear un volumen localmente anclado es unos 10 minutos por terabyte de datos. Sin embargo, la creación de volúmenes locales puede tardar unas horas en función de los factores mencionados en un dispositivo que está en uso.

**PREGUNTAS.** Deseo crear un volumen localmente anclado. ¿Hay cualquier procedimientos recomendados que deba conocer?

**A.** Volúmenes localmente anclados son adecuados para las cargas de trabajo que requieren garantías locales de datos en todo momento y dependen de latencia en la nube. Teniendo en cuenta el uso de volúmenes locales por cualquiera de las cargas de trabajo, tenga en cuenta lo siguiente:

- Grosor aprovisionados volúmenes localmente anclados y crear volúmenes locales afecta el espacio disponible para volúmenes en niveles. Por lo tanto, le recomendamos que empiece con volúmenes de menor tamaño y escala como aumenta el requisito.

- Aprovisionamiento de volúmenes locales es una operación larga que podría requerir la inserción de datos existentes de volúmenes en niveles en la nube. Como resultado, puede experimentar un rendimiento reducido en estos volúmenes.

- Aprovisionamiento de volúmenes locales es una operación mucho tiempo. El tiempo real que implica depende de varios factores: el tamaño de los datos en el dispositivo y el ancho de banda disponible, el volumen está configurado. Si no ha realizado la copia de los volúmenes existentes en la nube, la creación de volumen es más lenta. Le recomendamos que siga instantáneas de nube de sus volúmenes existentes antes de aprovisionar un volumen local.
 
- Puede convertir volúmenes en niveles existentes en volúmenes localmente anclados y esta conversión implica aprovisionamiento de espacio en el dispositivo para el volumen resultante localmente anclado (además de incorporar datos en niveles [Si cualquier] de la nube). Nuevamente, se trata de una operación larga que depende de factores que hemos descrito anteriormente. Le recomendamos que haga una copia de sus volúmenes existentes antes de la conversión como el proceso disminuirá incluso si no se copian de volúmenes existentes. El dispositivo también puede experimentar un rendimiento reducido durante este proceso.
    
Para obtener más información sobre cómo [crear un volumen localmente anclado](storsimple-manage-volumes-u2.md#add-a-volume)

**PREGUNTAS.** ¿Puedo crear múltiples volúmenes localmente anclados al mismo tiempo?

**A.** Sí, pero los trabajos de creación y expansión de volumen localmente anclada se procesan secuencialmente.

Grosor aprovisionados volúmenes localmente anclados y esto requiere la creación de un espacio local en el dispositivo (Esto puede ocasionar los datos existentes de volúmenes en niveles que se pueden insertar en la nube durante el proceso de aprovisionamiento). Por lo tanto, si el trabajo de aprovisionamiento en curso, otras tareas de creación de volumen local se pondrán en cola hasta que haya finalizado el trabajo.

Del mismo modo, si un volumen local existente es expandirse o un volumen en niveles se convierte en un volumen localmente anclado, a continuación, la creación de un nuevo volumen localmente anclada en cola hasta que se complete la tarea anterior. Expandir el tamaño de un volumen localmente anclado implica la expansión del espacio local existente para ese volumen. Conversión de una en niveles localmente anclados volumen también implica la creación de espacio local para resultante localmente anclados volumen. En ambas de estas operaciones, creación o expansión de espacio local es un valor long ejecutar trabajo.

Puede ver estas tareas en la página **trabajos** del servicio de administrador de StorSimple de Azure. El trabajo que actualmente se está procesando se actualiza continuamente para reflejar el progreso de aprovisionamiento de espacio. Las tareas restantes de volumen localmente anclada está marcada como en ejecución, pero se ha quedado atascado su progreso y que se seleccionan en el orden en que están en la cola.

**PREGUNTAS.** He eliminado un volumen localmente anclado. ¿Por qué no puedo ver el espacio recuperado reflejado en el espacio disponible al intentar crear un nuevo volumen? 

**A.** Si elimina un volumen localmente anclado, puede que el espacio disponible para los nuevos volúmenes no se pueden actualizar inmediatamente. El servicio de administrador de StorSimple actualiza el espacio local disponible aproximadamente cada hora. Le recomendamos que espere una hora antes de intentar crear el nuevo volumen.

**PREGUNTAS.** ¿Son compatibles con volúmenes anclados localmente en el dispositivo de la nube?

**A.** No se admiten volúmenes anclados localmente en el dispositivo de nube (8010 y 8020 dispositivos anteriormente denominados el dispositivo virtual de StorSimple).

**PREGUNTAS.** ¿Puedo usar los cmdlets de PowerShell de Azure para crear y administrar volúmenes localmente anclados? 

**A.** No, no puede crear volúmenes localmente anclados a través de los cmdlets de PowerShell de Azure (cualquier volumen que se crea mediante PowerShell de Azure en niveles). También le recomendamos que no usar los cmdlets de PowerShell de Azure para modificar las propiedades de un volumen localmente anclada, ya que tendrá el efecto no deseado de modificar el tipo de volumen a niveles.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Preguntas sobre la copia de seguridad de un volumen localmente anclado

**PREGUNTAS.** ¿Son instantáneas locales de volúmenes localmente anclados compatibles?

**A.** Sí, puede tomar instantáneas locales de sus volúmenes anclados localmente. Sin embargo, le recomendamos encarecidamente que periódicamente copia los volúmenes localmente anclados con instantáneas de nube para asegurarse de que sus datos están protegidos en la posibilidad de que un desastre.

**PREGUNTAS.** ¿Hay cualquier directrices para administrar instantáneas locales para volúmenes localmente anclados?

**A.** Instantáneas locales frecuentes junto con una alta tasa de renovación de datos en el volumen localmente anclada pueden provocar que el espacio local en el dispositivo para consumir rápidamente y resultados en datos de volúmenes en niveles que se inserta en la nube. Por lo tanto, le recomendamos que minimice el número de copias instantáneas locales.

**PREGUNTAS.** He recibido una alerta que indica que se podrían invalidar mi instantáneas locales de volúmenes anclados localmente. ¿Cuándo puede ocurrir?

**A.** Instantáneas locales frecuentes junto con una alta tasa de renovación de datos en el volumen localmente anclada pueden provocar que el espacio local en el dispositivo para consumir rápidamente. Si los niveles locales del dispositivo se utilizan mucho, puede ocasionar una interrupción de nube ampliado el dispositivo llenen y escribe entrantes al volumen puede ocasionar invalidación de las instantáneas (como no hay espacio para actualizar las instantáneas para hacer referencia a los bloques de datos que han sido sobrescribir anteriores). En este caso la escritura en el volumen seguirá representarse, pero las instantáneas locales podrían no ser válidas. No hay sin afectar a las instantáneas de nube existente.

Advertencia de alerta es para notificarle que esta situación puede surgir y asegúrese de que direcciones, revisar las programaciones de instantáneas locales tomar instantáneas locales menos frecuentes o eliminar antiguas instantáneas locales que ya no son necesarias los mismos de forma oportuna.

Si se invalidarán la instantáneas locales, recibirá una alerta de información que se le informa de que se ha invalidarán las instantáneas locales para la directiva de copia de seguridad específica junto a la lista de las marcas de tiempo de las instantáneas locales que se invalidarán. Estas instantáneas será eliminada automáticamente y ya no podrá verlos en la página de **Copia de seguridad de catálogos** en el portal de clásica Azure.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Preguntas sobre cómo convertir un volumen en niveles en un volumen localmente anclado

**PREGUNTAS.** Al convertir un volumen en niveles en un volumen localmente anclado estoy observar la algunos lentitud en el dispositivo. ¿Por qué sucede esto? 

**A.** El proceso de conversión consta de dos pasos: 

  1. Aprovisionamiento de espacio en el dispositivo para el pronto-al--convertirse localmente había anclada volumen.
  2. Descargar todos los datos en niveles de la nube para garantizar local garantiza.

Estos dos pasos son largos ejecutando operaciones que dependen del tamaño del volumen que se convierten los datos en el dispositivo y el ancho de banda disponible. Como algunos datos de volúmenes en niveles existentes podrían supresión en la nube como parte del proceso de aprovisionamiento, el dispositivo podría experimentar un rendimiento reducido durante este período. Además, el proceso de conversión puede ser más lento si:

- No se copiaron volúmenes existentes a la nube. por lo que le recomendamos que los volúmenes antes de iniciar una conversión de copia de seguridad.

- Se han aplicado las directivas límites de ancho de banda, que podría limitar el ancho de banda disponible a la nube. por lo tanto, se recomienda que tiene un 40 Mbps dedicado o conexión más a la nube.

- El proceso de conversión puede tardar varias horas debido a los factores múltiplo descritos anteriormente; por lo tanto, le recomendamos que realice esta operación durante horas no picos o en un fin de semana para evitar el impacto en los consumidores de finalización.

Para obtener más información sobre cómo [convertir un volumen a un volumen localmente anclado en niveles](storsimple-manage-volumes-u2.md#change-the-volume-type)

**PREGUNTAS.** ¿Puedo cancelar la operación de conversión de volumen?

**A.** No, no puede cancelar la operación de conversión una vez iniciada. Como se describe en la pregunta anterior, tenga en cuenta de los posibles problemas de rendimiento que podría encontrarse durante el proceso y siga los procedimientos recomendados enumerados anteriormente, cuando planea la conversión.

**PREGUNTAS.** ¿Qué ocurre con mi volumen si se produce un error en la operación de conversión?

**A.** Conversión de volumen puede fallar debido a problemas de conectividad de la nube. Finalmente, el dispositivo puede dejar el proceso de conversión después de una serie de intentos para pasar datos en niveles de la nube. En este caso, el tipo de volumen seguirán el tipo de volumen de origen antes de la conversión, y:

- Para recibir una notificación del error de conversión de volumen, se producirá una alerta crítica. Obtener más información sobre [alertas relacionadas con volúmenes localmente anclados](storsimple-manage-alerts.md#locally-pinned-volume-alerts)

- Si va a convertir un en niveles a un volumen localmente anclado, el volumen seguirá provocar propiedades de un volumen en niveles como datos aún podrían estar en la nube. Le recomendamos que resolver los problemas de conectividad y vuelva a intentar la operación de conversión.
 
- Del mismo modo, cuando falla la conversión de un equipo local anclada a un volumen en niveles, aunque se marcará el volumen como un volumen localmente anclado, funcionará como un volumen en niveles (porque los datos se hayan esparcido en la nube). Sin embargo, este seguirá ocupar espacio en los niveles locales del dispositivo. Este espacio no estarán disponible para otros volúmenes anclados localmente. Le recomendamos que vuelva a intentar esta operación para asegurarse de que la conversión de volumen está completa y puede reclamar el espacio en el dispositivo local.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Preguntas sobre cómo restaurar un volumen localmente anclado

**PREGUNTAS.** ¿Volúmenes localmente anclada restaurado al instante?

**A.** Sí, volúmenes localmente anclados se restauran al instante. Tan pronto como la información de metadatos para el volumen se extrae de la nube como parte de la operación de restauración, el volumen se ponga en línea y se puede acceder por el host. Sin embargo, garantías locales para el volumen de datos no aparecerá hasta que se ha descargado todos los datos de la nube y, a continuación, puede experimentar reducen el rendimiento en estos volúmenes de la duración de la restauración.

**PREGUNTAS.** ¿Cuánto tiempo tarda en restaurar un volumen localmente anclado?

**A.** Volúmenes localmente anclados se restauran al instante y poner en línea en cuanto se recupera la información de metadatos de volumen de la nube, mientras siguen los datos del volumen que se descarguen en segundo plano. Esta última parte de la operación de restauración: obtener las garantías locales para los datos de volumen, es una operación larga y podría tardar varias horas para todos los datos se realicen local nuevo. El tiempo necesario para completar la misma depende de varios factores, como el tamaño del volumen que se está restaurando y el ancho de banda disponible. Si se ha eliminado el volumen original que se está restaurando, se tendrá más tiempo para crear el espacio local en el dispositivo como parte de la operación de restauración.

**PREGUNTAS.** Necesito Restaurar mi volumen localmente anclada existente a una instantánea anterior (efectuar cuando se niveles el volumen). ¿Se pueden restaurar el volumen como niveles en este caso?

**A.** No, se restauran el volumen como un volumen localmente anclado. Aunque las fechas de instantánea en la hora cuando el volumen se niveles, al restaurar volúmenes existentes, StorSimple siempre se utiliza el tipo de volumen en el disco tal y como existe actualmente.

**PREGUNTAS.** Ampliados Mi volumen localmente anclada recientemente, pero ahora necesito restaurar los datos a una hora en que el volumen se menor tamaño. ¿Se restaurar el tamaño del volumen actual y se necesita ampliar el tamaño del volumen una vez completada la restauración?

**A.** Sí, la restauración se el tamaño del volumen y necesite ampliar el tamaño del volumen una vez completada la restauración.

**PREGUNTAS.** ¿Puedo cambiar el tipo de un volumen durante la restauración?

**A.** No, no puede cambiar el tipo de volumen durante la restauración.

- Volúmenes que se han eliminado se restauran como el tipo que se almacena en la instantánea.

- Volúmenes existentes se restauran en función de su tipo actual, independientemente del tipo que se almacena en la instantánea (consulte las dos preguntas anterior).
 
**PREGUNTAS.** Necesito Restaurar mi volumen anclada localmente, pero se ha seleccionado un punto incorrecto en instantánea del tiempo. ¿Puedo cancelar la operación de restauración actual?

**A.** Sí, puede cancelar una operación de restauración en curso. El estado del volumen volverá al estado en el inicio de la restauración. Sin embargo, se perderá cualquier escritura que se ha realizado en el volumen mientras la restauración estaba en curso.

**PREGUNTAS.** Inicia una operación de restauración en uno de Mis volúmenes localmente anclados y ahora ver una instantánea de mi catálogo de trabajo pendiente no recollect crear. ¿Qué se se usa?

**A.** Esta es la instantánea temporal que se crea antes de la operación de restauración y se usa para deshacer en caso de que la restauración se cancela o se produce un error. No elimine esta instantánea; se eliminará automáticamente cuando finalice la restauración. Esto puede ocurrir si el trabajo de restauración solo localmente ha anclado volúmenes o una mezcla de volúmenes localmente anclados y en niveles. Si el trabajo de restauración incluye solo en niveles volúmenes, no se producirá este comportamiento.

**PREGUNTAS.** ¿Clonar un volumen localmente anclado?

**A.** Sí, puede hacerlo. Sin embargo, el volumen localmente anclado pueden clonar como un volumen en niveles de forma predeterminada. Para obtener más información acerca de cómo [clonar un volumen localmente anclado](storsimple-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Preguntas sobre errores sobre un volumen localmente anclado

**PREGUNTAS.** Necesito conmutar el dispositivo a otro dispositivo físico. ¿Mi volúmenes localmente anclados error más como anclados localmente o en niveles?

**A.** Según la versión del software del dispositivo de destino, volúmenes localmente anclados error sobre como:

- Anclados localmente si el dispositivo de destino está ejecutando StorSimple 8000 series actualización 2
- Niveles de si el dispositivo de destino está ejecutando StorSimple 8000 serie actualiza 1.x
- Niveles de si el dispositivo de destino es el dispositivo de nube (actualización de versión 2 del software o actualizar 1.x)

Obtener más información sobre [migración y DR de localmente anclados volúmenes entre versiones](storsimple-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**PREGUNTAS.** ¿Se restauran al instante volúmenes localmente anclados durante la recuperación (DR)?

**A.** Sí, se restauran volúmenes localmente anclados al instante durante la migración tras error. Tan pronto como la información de metadatos para el volumen se extrae de la nube como parte de la operación de migración tras error, el volumen se conecte el dispositivo de destino y se puede acceder por el host. Mientras tanto, los datos del volumen continuarán descargándose en segundo plano y puede experimentar un rendimiento reducido en estos volúmenes de la duración de la migración tras error.

**PREGUNTAS.** Ver el trabajo de migración tras error completado, ¿cómo puedo seguimiento del progreso de localmente anclada volumen que se está restaurando en el dispositivo de destino?

**A.** Durante una operación de migración tras error, el trabajo de migración tras error se marca como una vez complete todos los volúmenes en el conjunto de migración tras error se han restaurado y poner en línea en el dispositivo de destino al instante. Esto incluye cualquier volumen localmente anclada que podría haber fallado Sin embargo, garantías locales de los datos solo estará disponibles cuando se ha descargado todos los datos para el volumen. Puede realizar un seguimiento de este curso para cada volumen localmente anclada que se ha producido un cargo supervisar los trabajos de restauración correspondientes que se crean como parte de la migración tras error. Solo se creará estos trabajos de restauración individuales para volúmenes anclados localmente.

**PREGUNTAS.** ¿Puedo cambiar el tipo de un volumen durante la migración tras error?

**A.** No, no puede cambiar el tipo de volumen durante un error. Si producen errores sobre a otro dispositivo físico que se está ejecutando StorSimple 8000 series actualizar 2, los volúmenes de error sobre según el tipo de volumen que se almacena en la instantánea. Al cambiar a cualquier otra versión de dispositivo, consulte la pregunta anterior en el tipo de volumen después de un error.

**PREGUNTAS.** ¿Conmutar un contenedor de volumen con volúmenes anclados localmente en el dispositivo de la nube?

**A.** Sí, puede hacerlo. Los volúmenes localmente anclados error sobre como volúmenes en niveles. Obtener más información sobre [migración y DR de localmente anclados volúmenes entre versiones](storsimple-device-failover-disaster-recovery.md#considerations-for-device-failover)


