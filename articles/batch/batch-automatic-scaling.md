<properties
    pageTitle="Automáticamente escala nodos de cálculo en un grupo por lotes de Azure | Microsoft Azure"
    description="Activar la escala automática en un grupo de la nube para ajustar dinámicamente el número de nodos de cálculo en el grupo."
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="batch"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="multiple"
    ms.date="10/14/2016"
    ms.author="marsma"/>

# <a name="automatically-scale-compute-nodes-in-an-azure-batch-pool"></a>Cambiar automáticamente la escala de los nodos de cálculo en un grupo por lotes de Azure

Con la escala automática, el servicio de Azure lote dinámicamente puede agregar o quitar nodos de cálculo en un grupo en función de los parámetros que defina. Se pueden ahorrar tiempo y el dinero automáticamente ajustar la cantidad de energía de cálculo utilizado por la aplicación: agregar nodos como aumento de las peticiones de tarea de la tarea y quitarlas cuando disminuir.

Habilitar la escala automática en un grupo de nodos de cálculo asociar una *fórmula Autoescala* que defina, como con la [PoolOperations.EnableAutoScale] [ net_enableautoscale] método en la biblioteca de [.NET lote](batch-dotnet-get-started.md) . El servicio de proceso por lotes, a continuación, utiliza esta fórmula para determinar el número de nodos de cálculo que son necesarias para ejecutar la carga de trabajo. Lote responde a las mediciones de servicio muestras de datos que se recopilan periódicamente y ajusta el número de nodos de cálculo en el grupo en un intervalo configurable en función de la fórmula.

Puede habilitar la escala automática cuando se crea un grupo o en un grupo existente. También puede cambiar una fórmula existente en un grupo de "Autoescala" habilitado. Lote proporciona la capacidad de evaluar las fórmulas antes de asignarlas a grupos, así como supervisar el estado de ejecuciones de escala automático.

## <a name="automatic-scaling-formulas"></a>Fórmulas de escala automático

Una fórmula de escala automático es un valor de cadena que defina que contiene una o varias instrucciones y a continuación, se asigna a de un grupo [autoScaleFormula] [ rest_autoscaleformula] elemento (lote REST) o [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] propiedad (lote. NET). Cuando se asigna a un grupo, el servicio de lote utiliza la fórmula para determinar el número de nodos de cálculo en el grupo de destino para el siguiente intervalo de procesamiento (más intervalos más adelante). La cadena de fórmula no puede superar 8 KB de tamaño, puede incluir hasta 100 instrucciones que están separados por punto y coma y a continuación, pueden incluir saltos de línea y los comentarios.

Puede considerar que las fórmulas de escalado automático como con un Autoescala lote "idioma". Fórmulas instrucciones son expresiones de forma libre que pueden incluir variables definidas por el servicio (variables definidas por el servicio de lote) y variables definidas por el usuario (variables que defina). Pueden realizar diversas operaciones en estos valores mediante funciones, operadores y tipos integrados. Por ejemplo, una instrucción podría tardar la forma siguiente:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Fórmulas generalmente contienen varias instrucciones que lleve a cabo operaciones en los valores que se obtienen en anteriores instrucciones. Por ejemplo, primero obtenemos un valor para `variable1`, a continuación, se pasa a una función para rellenar `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Con estas instrucciones en la fórmula, su objetivo es llegar a un número de nodos de cálculo que se debe escalar el grupo a, el número de **destino** de **nodos dedicados**. Este número puede ser superior, inferior o igual que el número de nodos en el grupo actual. Lote se evalúa como fórmula de Autoescala de la agrupación en un intervalo específico ([intervalos automáticos de escala](#automatic-scaling-interval) se describen a continuación). A continuación, ajustará el número de nodos en el grupo para el número que especifica la fórmula Autoescala en el momento de la evaluación de destino.

Como un ejemplo rápido, esta fórmula dos líneas Autoescala especifica que el número de nodos debe ajustarse según el número de tareas activas, hasta un máximo de 10 nodos de cálculo:

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

En las siguientes secciones de este artículo tratan distintas entidades que conforman las fórmulas Autoescala, incluidas las variables, operadores, operaciones y funciones. Encontrará información sobre cómo obtener varias métricas de recursos y tareas de cálculo en lote. Puede usar estas métricas para ajustar forma inteligente el número de nodos de la agrupación según el estado de tarea y uso de recursos. A continuación, aprenderá a crear una fórmula y activar la escala automática en un grupo mediante el resto del lote y la API de .NET. Deberá finalizar configuración con algunas fórmulas de ejemplo.

> [AZURE.IMPORTANT] Cada cuenta de Azure lote se limita a un número máximo de núcleos (y, por lo tanto, nodos de cálculo) que se pueden usar para el procesamiento. El servicio por lotes creará nodos sólo hasta ese límite principales. Por lo tanto, no puede llegar al número de destino de nodos de cálculo que se especifica mediante una fórmula. Para obtener información sobre cómo ver y aumentar las cuotas de cuenta, consulte [cuotas y límites del servicio del lote de Azure](batch-quota-limit.md) .

## <a name="variables"></a>Variables

Puede utilizar variables **definidas por el servicio** y de **definidas por el usuario** en las fórmulas Autoescala. El servicio de variables definidas por el está integrado en el servicio por lotes: algunos son de lectura y escritura y algunas son de solo lectura. Las variables definidas por el usuario son variables que *se* definen. En la fórmula de ejemplo de dos líneas anterior, `$TargetDedicated` está definida por el servicio de variables, mientras `$averageActiveTaskCount` es una variable definida por el usuario.

La tabla siguiente muestra las variables de lectura y de sólo lectura definidos por el servicio de lote.

Puede **obtener** y **establecer** los valores de las variables definidas por el servicio para administrar el número de nodos de cálculo en un grupo:

| Variables definidas por el servicio de lectura y escritura | Descripción |
| --- | --- |
| $TargetDedicated | El número de **destino** de **dedicado nodos de cálculo** para el grupo. Este es el número de nodos de cálculo que se debe escalar el grupo a. Dado que es posible que un grupo no alcanzar el número de nodos de destino es un número de "destino". Esto puede ocurrir si el número de nodos de destino se modifica nuevamente una evaluación Autoescala posteriores antes de que el grupo ha alcanzado el objetivo inicial. También puede ocurrir si se alcanza una cuota de nodo o principales de la cuenta de proceso por lotes antes de que se ha alcanzado el número de nodos de destino. |
| $NodeDeallocationOption | La acción que se produce cuando se quitan los nodos de cálculo de un grupo. Los valores posibles son:<ul><li>**requeue**--termina tareas inmediatamente y se pone en la cola de trabajo para que se reprograman.<li>**Terminar**--termina tareas inmediatamente y quita de la cola de trabajo.<li>**taskcompletion**--espera de ejecución de tareas para finalizar y quita el nodo del grupo.<li>**retaineddata**--espera para todos los locales se conservan la tarea de datos en el nodo limpiar antes de quitar el nodo del grupo de.</ul> |

Puede **obtener** el valor de estas variables definidas por el servicio para realizar ajustes que se basan en métricas del servicio de lote:

| Variables definidas por el servicio de solo lectura | Descripción |
| --- | --- |
| $CPUPercent | El porcentaje de uso de CPU. |
| $WallClockSeconds | El número de segundos consumida. |
| $MemoryBytes | El promedio de megabytes que se utiliza. |
| $DiskBytes | El promedio de gigabytes utilizados en los discos locales. |
| $DiskReadBytes | El número de bytes leídos. |
| $DiskWriteBytes | El número de bytes escritos. |
| $DiskReadOps | Realiza el recuento de las operaciones de lectura de disco. |
| $DiskWriteOps | Realiza el recuento de operaciones de escritura de disco. |
| $NetworkInBytes | El número de bytes entrantes. |
| $NetworkOutBytes | El número de bytes de salida. |
| $SampleNodeCount | El número de nodos de cálculo. |
| $ActiveTasks | El número de tareas en un estado activo. |
| $RunningTasks | El número de tareas en un estado de ejecución. |
| $PendingTasks | La suma de $ActiveTasks y $RunningTasks. |
| $SucceededTasks | El número de tareas que ha finalizado correctamente. |
| $FailedTasks | El número de tareas que no se pudo. |
| $CurrentDedicated | El número actual de dedicado nodos de cálculo. |

> [AZURE.TIP] Las variables de sólo lectura, definidos por el servicio que se muestran arriba son *objetos* que proporcionan varios métodos para tener acceso a los datos asociados a cada uno. Para obtener más información, vea [obtener datos de ejemplo](#getsampledata) siguientes.

## <a name="types"></a>Tipos de

Estos **tipos** son compatibles en una fórmula.

- doble
- doubleVec
- doubleVecList
- cadena
- marca de hora: marca de tiempo es una estructura compuesta que contiene a los miembros siguientes:

    - año
    - mes (1-12)
    - día (1-31)
    - Weekday (con el formato de número, por ejemplo, 1 para el lunes)
    - hora (en formato de número de 24 horas, por ejemplo, 13 significa 1 P.M.)
    - minuto (00 a 59)
    - segundo (00 a 59)
- timeInterval

    - TimeInterval_Zero
    - TimeInterval_100ns
    - TimeInterval_Microsecond
    - TimeInterval_Millisecond
    - TimeInterval_Second
    - TimeInterval_Minute
    - TimeInterval_Hour
    - TimeInterval_Day
    - TimeInterval_Week
    - TimeInterval_Year

## <a name="operations"></a>Operaciones

Estas **operaciones** se permiten en los tipos de que se muestran más arriba.

| Operación                             | Operadores compatibles   | Tipo de resultado   |
| ------------------------------------- | --------------------- | ------------- |
| doble *operador* doble              | +, -, *, /            | doble            |
| *operador* de doble timeinterval        | *                     | timeInterval      |
| doubleVec *operador* doble           | +, -, *, /            | doubleVec         |
| doubleVec *operador* doubleVec        | +, -, *, /            | doubleVec         |
| timeInterval *operador* doble        | *, /                  | timeInterval      |
| timeInterval *operador* timeinterval  | +, -                  | timeInterval      |
| marca de tiempo de timeInterval *operador*     | +                     | marca de tiempo         |
| marca de tiempo *operador* timeinterval     | +                     | marca de tiempo         |
| marca de tiempo de *operador* de marca de tiempo        | -                     | timeInterval      |
| *operador de*doble                      | -, !                  | doble            |
| *operador*timeinterval                | -                     | timeInterval      |
| doble *operador* doble              | <, < =, ==, > =, >,! =  | doble            |
| *operador* de cadena              | <, < =, ==, > =, >,! =  | doble            |
| marca de tiempo de *operador* de marca de tiempo        | <, < =, ==, > =, >,! =  | doble            |
| timeInterval *operador* timeinterval  | <, < =, ==, > =, >,! =  | doble            |
| doble *operador* doble              | & &, & #124; & #124;      | doble            |

Cuando se prueba double con un operador ternario (`double ? statement1 : statement2`), distinto de cero es **true**y cero es **false**.

## <a name="functions"></a>Funciones

Estas **funciones** de predefinidas están disponibles para su uso en la definición de una fórmula de escala automático.

| Función                          | Tipo de valor devuelto   | Descripción
| --------------------------------- | ------------- | --------- |
| AVG(doubleVecList)                | doble        | Devuelve el valor promedio de todos los valores de la doubleVecList.
| Len(doubleVecList)                | doble        | Devuelve la longitud del vector creado a partir de la doubleVecList.
| LG(Double)                        | doble        | Devuelve el registro de base 2 de los dos.
| LG(doubleVecList)                 | doubleVec     | Devuelve el registro de componentwise base 2 de la doubleVecList. Una vec(double) se debe pasar explícitamente para el parámetro. En caso contrario, se supone que la versión de doble lg(double).
| LN(Double)                        | doble        | Devuelve el logaritmo natural del doble.
| LN(doubleVecList)                 | doubleVec     | Devuelve el registro de componentwise base 2 de la doubleVecList. Una vec(double) se debe pasar explícitamente para el parámetro. En caso contrario, se supone que la versión de doble lg(double).
| log(Double)                       | doble        | Devuelve el registro del doble en base 10.
| log(doubleVecList)                | doubleVec     | Devuelve el registro de componentwise la doubleVecList en base 10. Una vec(double) se debe pasar explícitamente para el parámetro de doble único. En caso contrario, se supone que la versión de doble log(double).
| Max(doubleVecList)                | doble        | Devuelve el valor máximo de la doubleVecList.
| min(doubleVecList)                | doble        | Devuelve el valor mínimo de la doubleVecList.
| NORM(doubleVecList)               | doble        | Devuelve la norma dos del vector creado a partir de la doubleVecList.
| percentil (v doubleVec, doble p) | doble        | Devuelve el elemento de percentil del vector v.
| RAND                            | doble        | Devuelve un valor aleatorio entre 0,0 y 1,0.
| Range(doubleVecList)              | doble        | Devuelve la diferencia entre los valores máximo y mínimo de la doubleVecList.
| STD(doubleVecList)                | doble        | Devuelve la desviación estándar de los valores de la doubleVecList.
| Stop()                            |               | Detiene la evaluación de la expresión de ajuste automático.
| SUM(doubleVecList)                | doble        | Devuelve la suma de todos los componentes de la doubleVecList.
| hora (dateTime de cadena = "")          | marca de tiempo     | Devuelve la marca de tiempo de la hora actual si no se pasan parámetros o la marca de tiempo de la cadena de fecha y hora si se pasa. Formatos de fecha compatibles son DTF W3C y RFC 1123.
| Val (v doubleVec, i doble)        | doble        | Devuelve el valor del elemento que está en ubicación i vector v, con el índice inicial de cero.

Algunas de las funciones que se describen en la tabla anterior pueden aceptar una lista como argumento. La lista de valores separados por comas es cualquier combinación de *doble* y *doubleVec*. Por ejemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

El valor de *doubleVecList* se convierte en una sola *doubleVec* antes de la evaluación. Por ejemplo, si `v = [1,2,3]`, a continuación, llamar a `avg(v)` es equivalente a llamar `avg(1,2,3)`. Llamar a `avg(v, 7)` es equivalente a llamar `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obtener datos de ejemplo

Autoescala fórmulas actuarán en datos de métricas (ejemplos) proporcionados por el lote. Una fórmula que crece o reduce el tamaño del grupo en función de los valores que se obtiene desde el servicio. Las variables definidas por el servicio descritos anteriormente son objetos que proporcionan varios métodos para tener acceso a los datos que está asociados con el objeto. Por ejemplo, la expresión siguiente muestra una solicitud para obtener los últimos cinco minutos de uso de CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Método | Descripción |
| --- | --- |
| GetSample() | La `GetSample()` método devuelve un vector de muestras de datos.<br/><br/>Un ejemplo es 30 segundos de valor de datos de métricas. En otras palabras, las muestras se obtienen cada 30 segundos. Pero, como se indica a continuación, hay un retardo recopila de una muestra y cuando esté disponible en una fórmula. Por lo tanto, no todos los ejemplos durante un período de tiempo determinado esté disponibles para la evaluación de una fórmula.<ul><li>`doubleVec GetSample(double count)`<br/>Especifica el número de muestras para obtener los ejemplos más recientes que se hayan recopilado.<br/><br/>`GetSample(1)`Devuelve la última muestra disponible. Para métricas como `$CPUPercent`, sin embargo, esto no se recomienda porque es imposible saber *cuando* se recopiló la muestra. Es posible reciente o debido a problemas del sistema, puede ser mucho más antigua. Es mejor en estos casos para usar un intervalo de tiempo, como se muestra a continuación.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Especifica un período de tiempo para recopilar datos de ejemplo. Opcionalmente, también especifica el porcentaje de muestras que deben estar disponibles en el período de tiempo solicitado.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`devolverá 20 muestras si están presentes en el historial de CPUPercent todos los ejemplos de los últimos diez minutos. Sin embargo, si el último minuto de historial no estaba disponible, se devolverían solo 18 ejemplos. En este caso:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`¿un error porque sólo 90% de las muestras están disponible.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`¿correctamente.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Especifica un período de tiempo para la recopilación de datos, con una hora de inicio y una hora de finalización.<br/><br/>Como se mencionó anteriormente, hay un retardo recopila de una muestra y cuando esté disponible en una fórmula. Se deben tener en cuenta al usar la `GetSample` método. Vea `GetSamplePercent` debajo.|
| GetSamplePeriod() | Devuelve el período de muestras tomadas en un conjunto de datos de ejemplo histórica. |
| Count() | Devuelve el número total de muestras en el historial de métrica. |
| HistoryBeginTime() | Devuelve la marca de tiempo de la muestra de datos disponibles más antigua para la métrica. |
| GetSamplePercent() |Devuelve el porcentaje de ejemplos en los que están disponibles para un intervalo de tiempo determinado. Por ejemplo:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Dado que la `GetSample` método falla si el porcentaje de muestras devuelto es menor que el `samplePercent` especificado, puede usar el `GetSamplePercent` método para comprobar en primer lugar. A continuación, puede realizar una acción alternativa si muestras insuficientes están presentes, sin detener la evaluación de escala automático.|

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Ejemplos, porcentaje de muestra y el método *GetSample()*

La operación de núcleo de una fórmula de Autoescala es obtener datos de métrica de tareas y recursos y, a continuación, ajustar el tamaño de grupo basado en esos datos. Por lo tanto, es importante tener claro cómo interactúan Autoescala fórmulas con datos de métricas o "ejemplos".

**Ejemplos**

El servicio de lote periódicamente toma *muestras* de tareas y recursos métricas y están disponibles a las fórmulas Autoescala. Estos ejemplos se registran cada 30 segundos por el servicio de lote. Sin embargo, normalmente hay algunas latencia que se produce un retraso entre cuando se registraron esos ejemplos y cuando se ponen a disposición a (y puede leer) Autoescala fórmulas. Además, debido a varios factores como red u otros problemas de infraestructura, muestras no haya grabado para un intervalo concreto. El resultado es "Falta" ejemplos.

**Porcentaje de muestra**

Cuando `samplePercent` se pasa a la `GetSample()` método o la `GetSamplePercent()` se denomina método, "porcentaje" hace referencia a una comparación entre el número total de *posibles* de muestras registrados por el servicio de lote y el número de muestras que están *disponibles* para su fórmula Autoescala.

Veamos un intervalo de tiempo de 10 minutos como ejemplo. Porque se graban muestras cada 30 segundos, dentro de un intervalo de tiempo de 10 minutos, el número total máximo de ejemplos en los que se registran por lotes sería 20 muestras (2 por minuto). Sin embargo, debido a la latencia inherente de mecanismo de informes o algún otro problema dentro de la infraestructura de Azure, es posible que solo 15 muestras que están disponibles para su fórmula Autoescala de lectura. Esto significa que, de ese período de 10 minutos, solo **75 por ciento** del número total de muestras grabado realmente disponible a la fórmula.

**Rangos GetSample() y ejemplo**

Las fórmulas Autoescala va a ser crecimiento y reducción de los grupos--nodos de agregar o quitar nodos. Porque nodos cuestan dinero, desea asegurarse de que las fórmulas usan un método de análisis que se basa en datos suficientes inteligente. Por lo tanto, se recomienda que use un análisis de tendencias de tipo en las fórmulas. Este tipo se reducir y ampliar los grupos que se basa en un *intervalo* de muestras recopiladas.

Para ello, utilice `GetSample(interval look-back start, interval look-back end)` para devolver un **vector** de ejemplos:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Cuando se evalúa la línea anterior por lotes, devolverá un intervalo de muestras como vector de valores. Por ejemplo:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Una vez que ha recopilado el vector de muestras, a continuación, puede usar funciones como `min()`, `max()`, y `avg()` para obtener los valores significativos del rango recopiladas.

Para obtener seguridad adicional, puede forzar una evaluación de fórmulas *no se realice correctamente* si menor que un determinado porcentaje de ejemplo está disponible durante un período de tiempo determinado. Cuando fuerza una evaluación de fórmulas no se realice correctamente, indicar lote dejar más evaluación de la fórmula si el porcentaje de muestras especificado no está disponible, y no se realizará cambia el tamaño del grupo. Para especificar un porcentaje necesario de muestras de la evaluación correctamente, especifique como tercer parámetro a `GetSample()`. A continuación, se especifica un requisito del 75% de ejemplos:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

También es importante, debido a la demora mencionada anteriormente en la disponibilidad de ejemplo, para especificar siempre un intervalo de tiempo con una hora de inicio de aspecto atrás anterior a un minuto. Esto es porque tarda aproximadamente un minuto de ejemplos de propagarse a través del sistema, así que muestras en el rango de `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` a menudo no estarán disponibles. De nuevo, puede usar el parámetro de porcentaje de `GetSample()` a forzar un requisito de porcentaje de ejemplo concreto.

> [AZURE.IMPORTANT] Le **recomendamos encarecidamente** que * *evitar confiar *sólo* en `GetSample(1)` en sus fórmulas Autoescala **. Esto es porque `GetSample(1)` esencialmente dice al servicio de proceso por lotes, "me ofrece la última muestra tiene, independientemente de cuánto tiempo hace obtuvo." Dado que es solo una sola muestra y puede ser un ejemplo anterior, no podrán representante de la imagen de estado de recursos o tareas recientes. Si usa `GetSample(1)`, asegúrese de que es parte de una instrucción más grande y no el punto de datos único que depende de la fórmula.

## <a name="metrics"></a>Métricas

Puede utilizar **recursos** y **tareas** métricas al definir una fórmula. Ajustar el número de destino de nodos dedicados en del grupo basado en los datos de métricas que obtenga y evalúe. Consulte la sección de [Variables](#variables) anterior para obtener más información sobre cada métrica.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td><b>Recursos</b></td>
    <td><p><b>Métrica de recursos</b> se basan en el uso de CPU, el ancho de banda y la memoria de nodos de cálculo, así como el número de nodos.</p>
        <p> Estas variables definidas por el servicio son útiles para realizar ajustes basados en el número de nodos:</p>
    <p><ul>
      <li>$TargetDedicated</li>
            <li>$CurrentDedicated</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Estas variables definidas por el servicio son útiles para realizar ajustes basados en el uso de recursos de nodo:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Tarea</b></td>
    <td><p><b>Métrica de tareas</b> basadas en el estado de tareas, como por ejemplo activo, pendiente y completadas. Las siguientes variables definidas por el servicio son útiles para realizar ajustes de tamaño del grupo en función de las estadísticas de tarea:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Escribir una fórmula Autoescala

Crear una fórmula Autoescala formar instrucciones que utilizan los componentes anteriores y luego combinar las instrucciones en una fórmula completa. En esta sección, crearemos una fórmula de Autoescala de ejemplo que puede llevar a cabo algunas decisiones escalados del mundo real.

En primer lugar, vamos a definir los requisitos de nuestra nueva fórmula Autoescala. La fórmula debe:

1. **Aumentar** el número de destino de nodos de cálculo en un grupo si el uso de CPU es alto.
2. **Reducir** el número de destino de nodos de cálculo en un grupo de uso de CPU es bajo.
3. Restringir siempre el número **máximo** de nodos a 400.

Para *aumentar* el número de nodos durante el uso de CPU elevado, se define la instrucción que rellena una variable definida por el usuario (`$totalNodes`) con un valor que es el 110 por ciento del número de destino actual de nodos, pero solo si el uso de CPU promedio mínimo durante los últimos 10 minutos era por encima del 70%. En caso contrario, se utiliza el valor dedicado actual.

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
```

Para *reducir* el número de nodos durante el uso de CPU bajo, la siguiente instrucción en nuestra fórmula establece el mismo `$totalNodes` variable a 90 por ciento del número de destino actual de nodos si el uso de CPU promedio de los últimos 60 minutos en era un 20 por ciento. En caso contrario, use el valor actual de `$totalNodes` que se rellena en la instrucción anterior.

```
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
```

Ahora limitar el número de destino dedicada de nodos de cálculo en un **máximo** de 400:

```
$TargetDedicated = min(400, $totalNodes)
```

La fórmula completa es:

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
$TargetDedicated = min(400, $totalNodes)
```

## <a name="create-an-autoscale-enabled-pool"></a>Crear un grupo habilitado para ajustar automáticamente

Para crear un nuevo grupo con ajuste automático habilitado, puede usar una de las técnicas siguientes:

**Lote .NET**

1. Crear la agrupación con [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx).
1. Establezca la propiedad [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) en `true`.
1. Establezca la propiedad [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) con la fórmula Autoescala.
1. (Opcional) Establezca la propiedad [CloudPool.AutoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoScaleevaluationinterval.aspx) (15 minutos de forma predeterminada).
1. Confirmar la agrupación con [CloudPool.Commit](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commit.aspx) o [CommitAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commitasync.aspx).

**Lote API de REST**

* [Agregar un fondo a una cuenta](https://msdn.microsoft.com/library/azure/dn820174.aspx): especifique la `enableAutoScale` y `autoScaleFormula` elementos en la convocatoria de la API de REST para configurar la escala automática para un grupo cuando lo cree.

Fragmento de código siguiente crea un grupo habilitado para ajustar automáticamente mediante el [Lote .NET] [ net_api] biblioteca. Fórmula de Autoescala del grupo establece el número de destino de nodos en 5 el lunes y 1 en todos los días de la semana. El [intervalo de ajuste de escala automático](#automatic-scaling-interval) se establece en 30 minutos. En este y otros C# fragmentos de código de este artículo, "myBatchClient" es una instancia correctamente inicializada de [BatchClient][net_batchclient].

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

Además de la API de REST de lote y .NET SDK, puede utilizar cualquiera de los otros [SDK lote](batch-technical-overview.md#batch-development-apis), [cmdlets de PowerShell lote](batch-powershell-cmdlets-get-started.md)y [Lote CLI](batch-cli-get-started.md) para trabajar con ajuste automático.

> [AZURE.IMPORTANT] Al crear un grupo habilitado para ajustar automáticamente, debe **no** especificar el `targetDedicated` parámetro. Además, si desea manualmente cambiar el tamaño de un grupo habilitado Autoescala (por ejemplo, con [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]), primero debe primera **Deshabilitar** automática en el grupo de ajuste de escala, a continuación, cambiar su tamaño.

### <a name="automatic-scaling-interval"></a>Intervalo de ajuste de escala automático

De forma predeterminada, el servicio del lote ajusta el tamaño de la agrupación según su fórmula Autoescala cada **15 minutos**. Este intervalo es configurable, sin embargo, usando las siguientes propiedades de grupo:

* [CloudPool.AutoScaleEvaluationInterval] [ net_cloudpool_autoscaleevalinterval] (lote .NET)
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (API de REST)

El intervalo mínimo es de cinco minutos y el máximo es 168 horas. Si se especifica un intervalo fuera de este intervalo, el servicio del lote devuelve un error de solicitud incorrecta (400).

> [AZURE.NOTE] Ajuste automático no está actualmente pensado para responder a los cambios en menos de un minuto, pero en su lugar, está diseñada para ajustar el tamaño de la agrupación gradualmente mientras se ejecuta una carga de trabajo.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Habilitar el ajuste automático en un grupo existente

Si ya ha creado un grupo con un determinado número de nodos de cálculo mediante el parámetro *targetDedicated* , aún puede habilitar ajuste automático en el grupo. Cada SDK lote proporciona una operación de "Habilitar Autoescala", por ejemplo:

* [BatchClient.PoolOperations.EnableAutoScale] [ net_enableautoscale] (lote .NET)
*  [Activar la escala automática en un grupo de] [ rest_enableautoscale] (API de REST)

Cuando se habilita el ajuste automático en un grupo existente, se aplica lo siguiente:

* Si la escala automática está actualmente **deshabilitado** en el grupo al enviar la solicitud de "Habilitar Autoescala", *debe* especificar una fórmula de Autoescala válido si tiene problemas con la solicitud. *Opcionalmente,* puede especificar un intervalo de evaluación Autoescala. Si no especifica un intervalo, se utiliza el valor predeterminado de 15 minutos.

* Si Autoescala está actualmente **habilitado** en el grupo, puede especificar una fórmula de Autoescala, un intervalo de evaluación o ambos. No puede omitir ambas propiedades.

  * Si especifica un intervalo de evaluación Autoescala nuevo, la programación de evaluación existente se detiene y se inicia una nueva programación. Hora de inicio de la programación nuevo es el momento en que se emitió la petición "Habilitar Autoescala".

  * Si se omiten la fórmula Autoescala o intervalo de evaluación, el servicio de lote continuará utilizando el valor actual de esta configuración.

> [AZURE.NOTE] Si se especifica un valor para el parámetro *targetDedicated* cuando se creó el grupo, se omiten cuando se evalúa la fórmula de escala automático.

Este fragmento de código de C# utiliza el [Lote .NET] [ net_api] biblioteca para habilitar el ajuste automático en un grupo existente:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Actualizar una fórmula Autoescala

Usar la misma solicitud "Habilitar Autoescala" para *Actualizar* la fórmula en un grupo existente habilitado Autoescala (por ejemplo, con [EnableAutoScale] [ net_enableautoscale] en .NET lote). No hay ninguna operación de "Actualizar Autoescala" especial. Por ejemplo, si se ajusta automáticamente ya está habilitada en "myexistingpool" cuando se ejecuta el código siguiente, su fórmula Autoescala se reemplaza con el contenido de `myNewFormula`.

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Actualizar el intervalo de Autoescala

Con una fórmula Autoescala de la actualización, si usa el mismo [EnableAutoScale] [ net_enableautoscale] método para cambiar el intervalo de evaluación Autoescala de un grupo existente Autoescala habilitado. Por ejemplo, para establecer el intervalo de evaluación Autoescala en 60 minutos para un grupo que ya está habilitado para ajustar automáticamente:

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Evaluar una fórmula Autoescala

Puede evaluar una fórmula antes de aplicar a un grupo. De este modo, puede realizar una "prueba ejecutar" de la fórmula para ver cómo se evalúan sus instrucciones antes de introducir la fórmula en producción.

Para evaluar una fórmula Autoescala, primero debe **Habilitar ajuste automático** en el grupo con una **fórmula válida**. Si desea probar una fórmula en un grupo que todavía no tiene ajuste automático habilitado, puede usar la fórmula de una línea `$TargetDedicated = 0` cuando habilita primero ajusta automáticamente. A continuación, use uno de estos procedimientos para evaluar la fórmula que desea probar:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) o [EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)

    Estos métodos de lote .NET requieren el identificador de un grupo existente y una cadena que contiene la fórmula Autoescala a evaluar. Los resultados de la evaluación se encuentran en la instancia de [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) devuelta.

* [Evaluar una fórmula de escala automático](https://msdn.microsoft.com/library/azure/dn820183.aspx)

    En esta solicitud API de REST, especifique el identificador de grupo en el URI y la fórmula Autoescala en el elemento de *autoScaleFormula* del cuerpo de la solicitud. La respuesta de la operación contiene cualquier información de error que podría estar relacionada con la fórmula.

En este [Lote .NET] [ net_api] fragmento de código, nos evaluar una fórmula antes de aplicar a la [CloudPool][net_cloudpool]. Si el grupo carece de ajuste automático habilitado, nos habilitar en primer lugar.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = batchClient.PoolOperations.GetPool("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    pool.EnableAutoScale(
        autoscaleFormula: $"$TargetDedicated = {pool.CurrentDedicated};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScale calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    pool.Refresh();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicated = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        batchClient.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        batchClient.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Evaluación correcta de la fórmula en este fragmento de código producirá un resultado similar al siguiente:

```
AutoScaleRun.Results:
    $TargetDedicated=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obtener información sobre cómo se ejecuta Autoescala

Para asegurarse de que está realizando la fórmula según lo esperado, le recomendamos que compruebe periódicamente los resultados de la "se ejecuta" lote se realiza en el grupo de ajuste automático. Por lo tanto, obtener (o actualizar) una referencia a la agrupación y examinar las propiedades de su última Autoescala ejecutar.

En .NET por lotes, la propiedad [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) tiene varias propiedades que proporciona información sobre la última ejecutar realizadas en el grupo en el servicio de lote de la escala automática.

* [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
* [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)

En la API de REST, la solicitud para [obtener información acerca de un grupo](https://msdn.microsoft.com/library/dn820165.aspx) devuelve información sobre la agrupación, que incluye la información de ejecución en [autoScaleRun](https://msdn.microsoft.com/library/dn820165.aspx#bk_autrun)de la última escala automática.

Fragmento de código de C# siguiente utiliza la biblioteca de .NET lote para imprimir información sobre el ajuste automático última ejecutar en el grupo "myPool":

```csharp
Cloud pool = myBatchClient.PoolOperations.GetPool("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Resultados de ejemplo del fragmento de código anterior:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicated=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Fórmulas de ejemplo Autoescala

Echemos un vistazo a algunas fórmulas que muestran diferentes maneras de ajustar la cantidad de recursos de cálculo en un grupo.

### <a name="example-1-time-based-adjustment"></a>Ejemplo 1: Ajuste de tiempo

Tal vez desee ajustar el tamaño del grupo basándose en el día de la semana y la hora del día, para aumentar o reducir el número de nodos en el grupo en consecuencia.

Esta fórmula obtiene primero la hora actual. Si se trata de un día de la semana (1-5) y de jornada laboral (8 AM a 6 PM), el tamaño de la agrupación de destino se establece en 20 nodos. En caso contrario, se establece en 10 nodos.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicated = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Ejemplo 2: Ajuste de basada en tareas

En este ejemplo, el tamaño del grupo se ajusta en función del número de tareas en la cola. Tenga en cuenta que tanto los comentarios como los saltos de línea son aceptables en las cadenas de la fórmulas.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicated = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Ejemplo 3: Accounting para tareas paralelas

Esto es otro ejemplo que se ajusta el tamaño del grupo en función del número de tareas. Esta fórmula también tiene en cuenta la [MaxTasksPerComputeNode] [ net_maxtasks] valor que se ha definido para el grupo. Esto es especialmente útil en situaciones donde la [ejecución de la tarea paralelas](batch-parallel-node-tasks.md) se ha habilitado en el grupo.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicated * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicated + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicated = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Ejemplo 4: Configurar un tamaño de grupo inicial

Este ejemplo muestra un fragmento de código de C# con una fórmula Autoescala que establece el tamaño del grupo en un determinado número de nodos durante un período de tiempo inicial. A continuación, se ajusta el tamaño de la agrupación según el número de ejecución y activo las tareas una vez transcurrido el período de tiempo inicial.

La fórmula del siguiente fragmento de código:

- Establece el tamaño de grupo inicial en cuatro nodos.
- No ajustar el tamaño del grupo en el primer 10 minutos del ciclo de vida del grupo.
- Después de 10 minutos, obtiene el valor máximo del número de tareas de funcionamiento y activas dentro de los últimos 60 minutos.
  - Si dos valores son 0 (lo que indica que no hay tareas estaban ejecutando o activo en los últimos 60 minutos), el tamaño del grupo se establece en 0.
  - Si cualquier valor es mayor que cero, no hay cambios.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicated = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Pasos siguientes

* [Maximizar el lote de Azure calcular uso de recursos con tareas de nodo simultáneas](batch-parallel-node-tasks.md) contiene detalles acerca de cómo se pueden ejecutar varias tareas simultáneamente en los nodos de cálculo en el grupo. Además de ajuste automático, esta característica puede ayudar a reducir la duración de tarea para algunas cargas de trabajo, lo que le ahorra dinero.

* Otro aumento de eficiencia, asegúrese de que la aplicación lote consulta el servicio de lote de la manera más óptima. En la [consulta el servicio de Azure lote eficazmente](batch-efficient-list-queries.md), aprenderá cómo limitar la cantidad de datos que cruza el cable al consultar el estado de miles de nodos de cálculo o tareas.

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx
