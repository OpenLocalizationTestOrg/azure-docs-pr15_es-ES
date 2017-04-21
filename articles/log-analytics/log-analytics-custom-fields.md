<properties
   pageTitle="Campos personalizados de análisis de registro | Microsoft Azure"
   description="La característica de campos personalizados de análisis de registro le permite crear sus propios campos de búsqueda de datos OMS que agregan a las propiedades de un registro recopilada.  En este artículo se describe el proceso para crear un campo personalizado y se proporciona un tutorial detallado con un evento de ejemplo."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="custom-fields-in-log-analytics"></a>Campos personalizados de análisis de registro

La característica de **Campos personalizados** de análisis de registro le permite ampliar los registros existentes en el repositorio OMS agregando sus propios campos de búsqueda.  Campos personalizados se rellenan automáticamente desde datos extraídos de otras propiedades en el mismo registro.

![Introducción a los campos personalizado](media/log-analytics-custom-fields/overview.png)

Por ejemplo, el siguiente registro de ejemplo con datos útiles ocultas en la descripción del evento.  Extraer datos a propiedades independientes hace que esté disponible para acciones como la ordenación y filtrado.

![Botón de búsqueda de registro](media/log-analytics-custom-fields/sample-extract.png)

>[AZURE.NOTE] En la vista previa, está limitado a 100 campos personalizados en el área de trabajo.  Este límite se expandirá cuando el alcance de esta característica disponibilidad general.

## <a name="creating-a-custom-field"></a>Crear un campo personalizado

Cuando se crea un campo personalizado, análisis de registro debe saber qué datos se utiliza para llenar su valor.  Utiliza una tecnología de Microsoft Research denominado FlashExtract para identificar rápidamente los datos.  En lugar de exigir que proporcione instrucciones explícitas, análisis de registro aprendiendo sobre los datos que desea extraer de ejemplos que proporciona.

Las siguientes secciones proporcionan el procedimiento para crear un campo personalizado.  En la parte inferior de este artículo es un tutorial acerca de la extracción de una muestra.

> [!NOTE] El campo personalizado se rellena como registros que coincidan con los criterios especificados se agregan al almacén de datos OMS, por lo que solo se muestra en los registros que se recopilan después de crea el campo personalizado.  El campo personalizado no se agregará a los registros que ya están en el almacén de datos cuando se crea.

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Paso 1: identificar los registros que tendrán el campo personalizado
El primer paso es identificar los registros que obtendrán el campo personalizado.  Comience con una [búsqueda de registros estándar](log-analytics-log-searches.md) y, a continuación, seleccione un registro para que actúe como el modelo que aprenda de análisis de registro.  Al especificar que se van a extraer datos en un campo personalizado, se abre el **Asistente para la extracción de campos** donde validar y refinar los criterios.

2. Vaya a la **Búsqueda de registros** y usar una [consulta para recuperar los registros](log-analytics-log-searches.md) que tendrá el campo personalizado.
2. Seleccione un registro que usará el análisis de registro para que actúe como un modelo para extraer datos para rellenar el campo personalizado.  Se identifican los datos que desea extraer de este registro y registro análisis usará esta información para determinar la lógica para rellenar el campo personalizado para todos los registros similares.
3. Haga clic en el botón a la izquierda de cualquier propiedad de texto del registro y haga clic en **extraer campos de**.
4. El **Asistente para la extracción de campo se abre**y el registro seleccionado se muestra en la columna de **Ejemplo de principal** .  El campo personalizado se definirá para esos registros con los mismos valores en las propiedades que están seleccionadas.  
5. Si la selección no es exactamente lo que desee seleccionar campos adicionales para restringir los criterios.  Para cambiar los valores de los criterios de campo, debe cancelar y seleccione otro registro que cumplan los criterios que desee.

### <a name="step-2---perform-initial-extract"></a>Paso 2: realizar extraer inicial.
Una vez haya identificado los registros que tendrán el campo personalizado, identificar los datos que desea extraer.  Análisis de registro usará esta información para identificar patrones similares en registros similares.  En el paso después podrá validar los resultados y proporcionan más detalles de análisis de registro para usar en su análisis.

1. Resalte el texto en el registro de ejemplo que desea rellenar el campo personalizado.  A continuación, aparecerá un cuadro de diálogo para proporcionar un nombre para el campo y realizar la extracción inicial.  Los caracteres ** \_CF** se agregará automáticamente.
2. Haga clic en **extraer** para llevar a cabo un análisis de registros recopilados.  
3. Las secciones de **Resumen** y **Los resultados de búsqueda** muestran los resultados del extracto para comprobar su precisión.  **Resumen** muestra los criterios que se usa para identificar registros y un recuento de cada uno de los valores de datos identificados.  **Resultados de búsqueda** proporciona una lista detallada de los registros que coincidan con los criterios.


### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Paso 3: comprobar la exactitud del extracto y crear un campo personalizado

Una vez haya realizado la extracción inicial, análisis de registro mostrará sus resultados basados en datos que se ha recopilado previamente.  Si los resultados de buscar precisos puede crear el campo personalizado sin ningún trabajo adicional.  Si no es así, a continuación, puede refinar los resultados para que el análisis de registro puede mejorar su lógica.

2.  Si los valores de la extracción inicial no son correctos, haga clic en el icono **Editar** junto a un registro incorrecto y seleccione **modificar este resaltado** para modificar la selección.
3.  La entrada se copia en la sección **ejemplos adicionales** debajo del **Ejemplo principal**.  Puede ajustar el resaltado aquí para ayudar a los análisis de registro a comprender la selección que debe realizados.
4.  Haga clic en **extraer** para usar esta nueva información para evaluar todos los registros existentes.  Los resultados se pueden modificar para los registros que no sea el que acaba de modificar en función de esta inteligencia nueva.
5.  Continúe agregando correcciones hasta que todos los registros de extracto identifican correctamente los datos para rellenar el nuevo campo personalizado.
6. Cuando esté satisfecho con los resultados, haga clic en **Guardar extraer** .  El campo personalizado ahora está definido, pero no agregarse a los registros todavía.
7.  Espere a que los registros nuevos que cumplan los criterios para que se recopila y, a continuación, ejecute de nuevo la búsqueda de registros. Registros nuevos deben tener el campo personalizado.
8.  Use el campo personalizado como cualquier otra propiedad de registro.  Puede utilizar agregado y agrupar datos y utilizarlo para generar nueva información.


## <a name="viewing-custom-fields"></a>Ver los campos personalizados
Puede ver una lista de todos los campos personalizados en su grupo de administración desde el icono de **configuración** de los paneles OMS.  Seleccione los **datos** y, a continuación, en **campos personalizados** para obtener una lista de todos los campos personalizados en el área de trabajo.  

![Campos personalizados](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Quitar un campo personalizado
Hay dos formas de quitar un campo personalizado.  La primera es la opción **Quitar** para cada campo al ver la lista completa, como se describió anteriormente.  El otro método es recuperar un registro y haga clic en el botón a la izquierda del campo.  El menú tendrá una opción para eliminar el campo personalizado.

## <a name="sample-walkthrough"></a>Tutorial de ejemplo

La siguiente sección le guía a través de un ejemplo completo de creación de un campo personalizado.  En este ejemplo se extrae el nombre del servicio de eventos de Windows que indican un servicio de cambio de estado.  Esto se basa en eventos creados por el Administrador de Control de servicio en el registro del sistema en equipos con Windows.  Si desea seguir este ejemplo, debe ser [la recopilación de eventos de información para el registro del sistema](log-analytics-data-sources-windows-events.md).

Escriba la siguiente consulta para devolver todos los eventos del servicio de administrador de Control que tiene un ID de 7036 que es el evento que indica un servicio de iniciar o detener.

![Consulta](media/log-analytics-custom-fields/query.png)

A continuación, seleccionamos cualquier registro de eventos 7036 ID.

![Registro de origen](media/log-analytics-custom-fields/source-record.png)

Queremos que el nombre del servicio que aparece en la propiedad **RenderedDescription** y seleccione el botón junto a esta propiedad.

![Extraer campos](media/log-analytics-custom-fields/extract-fields.png)

Se abre el **Asistente para la extracción de campo** y los campos del **registro de eventos** e **IdEvento** están seleccionados en la columna de **Ejemplo de principal** .  Esto indica que el campo personalizado se definirá para eventos desde el registro del sistema con el ID de 7036.  Esto es suficiente, así que no necesitamos seleccionar todos los demás campos.

![Ejemplo de principal](media/log-analytics-custom-fields/main-example.png)

Nos resaltar el nombre del servicio en la propiedad **RenderedDescription** y use el **servicio** para identificar el nombre del servicio.  El campo personalizado se llamará **Service_CF**.

![Título de campo](media/log-analytics-custom-fields/field-title.png)

Vemos que el nombre de servicio se identifica correctamente para algunos registros, pero no para otros usuarios.   La presentación de **Los resultados de búsqueda** que no se ha seleccionado la parte del nombre del **Adaptador de rendimiento de WMI** .  **Resumen** muestra que cuatro registros con servicio **DPRMA** incorrectamente incluirán una palabra adicional y dos registros identifican **Instalador de módulos** en lugar de **Módulos de Windows Installer**.  

![Resultados de búsqueda](media/log-analytics-custom-fields/search-results-01.png)

Empezaremos con el registro de **Adaptador de rendimiento de WMI** .  Haga clic en su icono de edición y, a continuación, en **modificar esta resaltado**.  

![Modificar resaltado](media/log-analytics-custom-fields/modify-highlight.png)

Aumentar el resaltado para incluir la palabra **WMI** y, a continuación, vuelva a ejecutar el extracto.  

![Ejemplo adicional](media/log-analytics-custom-fields/additional-example-01.png)

Podemos ver que se han corregido las entradas para **Adaptador de rendimiento de WMI** y análisis de registro también usa esta información para corregir los registros de **Windows Installer de módulo**.  Podemos ver en la sección **Resumen** aunque esa **DPMRA** se aún no se identifica correctamente.

![Resultados de búsqueda](media/log-analytics-custom-fields/search-results-02.png)

Nos desplácese a un registro con el servicio DPMRA y siga el mismo proceso para corregir dicho registro.

![Ejemplo adicional](media/log-analytics-custom-fields/additional-example-02.png)

 Cuando se ejecuta la extracción, podemos ver que todos los resultados son precisos.

![Resultados de búsqueda](media/log-analytics-custom-fields/search-results-03.png)

Podemos ver que **Service_CF** se crea pero no se ha agregado a los registros.

![Recuento inicial](media/log-analytics-custom-fields/initial-count.png)

Después de algún tiempo transcurrido, por lo que new se recopilan eventos, podemos ver que si el campo **Service_CF** ahora se ha agregado a registros que coincidan con nuestros criterios.

![Resultados finales](media/log-analytics-custom-fields/final-results.png)

Ahora podemos usar el campo personalizado como cualquier otra propiedad de registro.  Para ilustrar esto, creamos una consulta que agrupa por el nuevo campo **Service_CF** para comprobar qué servicios están más activas.

![Agrupar por consulta](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de las [búsquedas de registro](log-analytics-log-searches.md) crear consultas con campos personalizados de criterios.
- Supervisar los [archivos de registro personalizado](log-analytics-data-sources-custom-logs.md) que se debe analizar con campos personalizados.