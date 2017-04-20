## <a name="dynamic-service-plan"></a>Plan de servicio dinámico

En el Plan de servicio dinámicos, las aplicaciones de la función se asignará a una instancia de aplicación de la función. Si es necesario más instancias se agregará dinámicamente.
Las instancias pueden abarcar varios recursos, realizar el máximo partido a la infraestructura de Azure disponible. Además, las funciones se ejecutarán en paralelo reduce el tiempo total necesario para procesar solicitudes. Tiempo de ejecución de cada función se agrega hacia arriba, en segundos y agregado por la aplicación de la función que lo contiene. Con costo controlados por el número de instancias, el tamaño de la memoria y tiempo total de ejecución en segundos Gigabyte. Esta es una excelente opción si sus necesidades de cálculo están intermitente o las horas de trabajo suelen ser muy breve, ya que permite solo paga por recursos de cálculo cuando en realidad están en uso.   

### <a name="memory-tier"></a>Nivel de memoria

Dependiendo de la función necesita puede seleccionar la cantidad de memoria necesaria para ejecutarlos en la aplicación de la función (contenedor de funciones).
Las opciones de tamaño de memoria varían de **128 MB a 1536 MB**. El tamaño de la memoria seleccionada corresponde al conjunto de trabajo necesario para todas las funciones que forman parte de la aplicación de la función. Si el código requiere más memoria que el tamaño seleccionado, se cerrará la instancia de aplicación de la función debido a la falta de memoria disponible.

### <a name="scaling"></a>Ajuste de escala

La plataforma de funciones de Azure evaluará el tráfico necesidades, basándose en los desencadenadores configurados, decidir cuándo escalar hacia arriba o hacia abajo. El nivel de detalle de escala es la aplicación de la función. En este caso se ha escalado significa agregar más instancias de una aplicación de la función. Inversamente como tráfico deja de funcionar, instancias de aplicación de la función son deshabilitado-escala finalmente hacia abajo a cero cuando se están ejecutando ninguno.  

### <a name="resource-consumption-and-billing"></a>Consumo de recursos y de facturación

En el dinámico asignación de recursos de modo se hace de forma diferente que el plan de servicio de aplicación estándar, por lo tanto, el modelo de consumo también es diferente, lo que permite un modelo de "pago por uso". Consumo se notificará por la aplicación de la función, solo por vez cuando se está ejecutando el código.  
Multiplica el tamaño de la memoria (en GB) se calcula la cantidad total de tiempo de ejecución (en segundos) para todas las funciones que se ejecuta dentro de la aplicación de esa función. La unidad de consumo será **GB-s (Gigabyte segundos)**.