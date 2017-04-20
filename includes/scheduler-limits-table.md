En la tabla siguiente describe cada uno de los principales cuotas, los límites, valores predeterminados y límites de Azure programador.

|Recursos|Descripción de límite|
|---|---|
|**Tamaño de trabajo**|El tamaño máximo de trabajo es 16 KB. Si una ubicación o una revisión da como resultado un trabajo supera estos límites, se devuelve un código de estado 400 Solicitud incorrecta.|
|**Tamaño de la dirección URL de la solicitud**|Tamaño máximo de la dirección URL de la solicitud es 2048 caracteres.|
|**Tamaño del encabezado de agregado**|Tamaño máximo de encabezado agregado es 4096 caracteres.|
|**Recuento de encabezado**|Número máximo de encabezado es 50 encabezados.|
|**Tamaño del cuerpo**|Tamaño máximo de cuerpo es 8192 caracteres.|
|**Intervalo de repetición**|Intervalo de periodicidad máximo es de 18 meses.|
|**Hora a la hora de inicio**|Máximo "hora a la hora de inicio" es de 18 meses.|
|**Historial de trabajos**|Cuerpo de respuesta máximo almacenado en el historial de trabajos es de 2048 bytes.|
|**Frecuencia**|La cuota de frecuencia máximo predeterminado es 1 hora en una colección de trabajo gratuita y 1 minuto en una colección de trabajo estándar. La frecuencia máxima es configurable en una colección de trabajo para ser menor que el valor máximo. Todos los trabajos de la colección de trabajo se limitan el valor establecido en la colección de trabajo. Si intenta crear un trabajo con una frecuencia superior a la frecuencia máxima en la colección de trabajo solicitud producirá con un código de estado de conflicto 409.|
|**Trabajos**|La cuota de trabajos máximo predeterminado es 5 trabajos en una colección de trabajo gratuita y 50 trabajos en una colección de trabajo estándar. El número máximo de trabajos es configurable en una colección de trabajo. Todos los trabajos de la colección de trabajo se limitan el valor establecido en la colección de trabajo. Si intenta crear más trabajos que la cuota de trabajos máximo, la solicitud falla con un código de estado de conflicto 409.|
|**Retención de historial del trabajo**|Historial de trabajos se conserva hasta 2 meses o hasta la última 1000 ejecuciones.|
|**Retención de trabajo completado y con errores**|Tareas completadas y con errores se conservan para 60 días.|
|**Tiempo de espera**|No hay un tiempo de espera de solicitud (no se puede configurar) estáticas de 60 segundos para acciones HTTP. Para operaciones de ejecución más largas, siga los protocolos asincrónicos de HTTP; Por ejemplo, devolver un 202 inmediatamente pero continuar trabajando en segundo plano.|
