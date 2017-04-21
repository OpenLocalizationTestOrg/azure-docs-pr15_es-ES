<properties
 pageTitle="Programador de alta disponibilidad y confiabilidad"
 description="Programador de alta disponibilidad y confiabilidad"
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
 ms.topic="article"
 ms.date="08/16/2016"
 ms.author="deli"/>


# <a name="scheduler-high-availability-and-reliability"></a>Programador de alta disponibilidad y confiabilidad

## <a name="azure-scheduler-high-availability"></a>Programador de Azure alta disponibilidad

Como un servicio de la plataforma Windows Azure core, programador de Azure altamente disponible y las características de implementación de servicio geo redundantes y replicación de la configuración regional de geo trabajo.

### <a name="geo-redundant-service-deployment"></a>Implementación de servicio geo redundantes

Programador de Azure está disponible a través de la interfaz de usuario en casi todas las regiones geo que está en Azure hoy. La lista de regiones que está disponible en Azure programador esté [incluido aquí](https://azure.microsoft.com/regions/#services). Si un centro de datos en una región hospedado se procesa disponible, las capacidades de migración tras error del programador de Azure son tales que el servicio está disponible desde el centro de datos de otra.

### <a name="geo-regional-job-replication"></a>Replicación de trabajo de configuración regional de geo

No solo está disponible para las solicitudes de administración, pero su propio trabajo también es replicar geo front-end el programador de Azure. Cuando hay una interrupción en una región, programador de Azure pasa y garantiza que se ejecuta el trabajo desde otro centro de datos en la región geográfica pareja.

Por ejemplo, si ha creado un trabajo en sur Central de EE, programador de Azure aplica automáticamente ese trabajo en Norte Central de EE. Cuando se produce un error en sur Central de EE, programador de Azure garantiza que se ejecuta el trabajo del Norte Central de EE. 

![][1]

Como resultado, programador de Azure garantiza que los datos permanecen en la misma región geográfica más amplia en caso de un error de Azure. Por tanto, no necesita duplicar su trabajo para agregar alta disponibilidad: programador de Azure automáticamente ofrece capacidades de alta disponibilidad de los trabajos.

## <a name="azure-scheduler-reliability"></a>Confiabilidad de Azure programador

Programador de Azure garantiza su propia alta disponibilidad y toma un enfoque diferente a las tareas creadas por el usuario. Por ejemplo, su trabajo puede invocar un extremo HTTP que no está disponible. No obstante, programador de Azure intenta ejecutar su trabajo correctamente, que le da opciones alternativas para tratar los errores de. Programador de Azure hace esto de dos maneras:

### <a name="configurable-retry-policy-via-retrypolicy"></a>Directiva de reintento configurable a través de "retryPolicy"

Programador de Azure le permite configurar una directiva de reintento. De forma predeterminada, si una tarea falla, programador intenta el trabajo nuevo cuatro veces más, en intervalos de 30 segundos. Puede volver a configurar esta directiva intentos para que sea más rigurosa (por ejemplo, diez veces, en intervalos de 30 segundos) o más flexible (por ejemplo, dos veces, en intervalos diarios.)

Como un ejemplo de cuándo esto puede ayudar a, puede crear un trabajo que se ejecuta una vez por semana e invoca un extremo HTTP. Si el extremo HTTP es hacia abajo para unas pocas horas cuando se ejecuta el trabajo, puede que no desea esperar una semana más para el trabajo de volver a ejecutar ya que se producirá un error incluso la directiva predeterminada de reintento. En estos casos, puede volver a configurar la directiva de reintento estándar para volver a intentar cada tres horas (por ejemplo) en lugar de cada 30 segundos.

Para obtener información sobre cómo configurar una directiva de reintento, consulte [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoint-configurability-via-erroraction"></a>Configuración de extremo alternativo a través de "errorAction"

Si el extremo de destino para el trabajo del programador de Azure permanece inaccesible, programador de Azure utiliza el extremo de control de errores alternativo después de seguir su directiva de reintento. Si se ha configurado un extremo de control de errores alternativo, programador de Azure invoca. Con un extremo alternativo sus propios trabajos están altamente disponibles debido a errores.

Por ejemplo, en el diagrama siguiente, programador de Azure sigue su directiva de intentos para acertar un servicio web de Nueva York. Si falla esos reintentos, comprueba si hay una alternativa. A continuación, continúa y empieza a realizar solicitudes a la alternativa con la misma directiva de reintento.

![][2]

Tenga en cuenta que la misma directiva de reintento se aplica a la acción original y la acción error alternativo. También es posible que el tipo de acción de la acción error alternativo ser diferentes de tipo de acción de la acción principal. Por ejemplo, mientras que la acción principal puede invocar un extremo HTTP, la acción de error en su lugar puede ser una cola de almacenamiento, la cola de bus de servicio o la acción de tema de bus de servicio que realiza el registro de errores.

Para obtener información sobre cómo configurar un extremo alternativo, consulte [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## <a name="see-also"></a>Vea también

 [¿Qué es programador?](scheduler-intro.md)

 [Azure programador conceptos, terminología y la jerarquía de entidad](scheduler-concepts-terms.md)

 [Empezar a usar a planificador en el portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en el programador de Azure](scheduler-plans-billing.md)

 [Cómo crear programaciones complejas y periodicidad avanzada con el programador de Azure](scheduler-advanced-complexity.md)

 [Referencia de API de REST de programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de programador Azure](scheduler-powershell-reference.md)

 [Límites de programador Azure, valores predeterminados y códigos de error](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Azure programador](scheduler-outbound-authentication.md)


[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
