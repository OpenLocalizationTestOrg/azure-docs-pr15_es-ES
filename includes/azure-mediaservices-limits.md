Recursos|Límite predeterminado|Límite máximo
---|---|---
Cuentas de Azure Media Services (AMS) en una sola suscripción||25
Activos por cuenta AMS||1.000.000<sup>1</sup>
Encadenado tareas por proyecto||30
Activos por tarea||50
Activos por trabajo||100
Tareas por cuenta AMS ||50.000<sup>2</sup>
Localizadores únicos asociados a la vez un activo||5<sup>4</sup>
Canales directo por cuenta AMS </p></td>|5</p></td>|<sup>1</sup> de n/a.
Programas en estado de detención por canal </p></td>|50</p></td>|<sup>1</sup> de n/a.
Programas en ejecución estado por canal </p></td>|3</p></td>|3
Extremos streaming en estado por cuenta AMS de ejecución</p></td>|2</p></td>|<sup>1</sup> de n/a.
Unidades de streaming por streaming extremo </p></td>|10 </p></td>|<sup>1</sup> de n/a.
Unidades de codificación por cuenta AMS </p></td>|25</p></td>|<sup>1</sup> de n/a.
Cuentas de almacenamiento | |1.000<sup>5</sup>
Directivas de || 1.000.000<sup>6</sup>

<sup>1</sup> puede solicitar para actualizar los límites de esta cuota abriendo una incidencia de soporte técnico. No crear más cuentas AMS a aumentar los límites, en su lugar enviar una incidencia de soporte técnico.

<sup>2</sup> este número incluye trabajos en cola, terminados, activos y cancelados. No incluye tareas eliminadas. Puede eliminar los trabajos antiguos con **IJob.Delete** o la solicitud HTTP **Eliminar** .

<sup>3</sup> al realizar una solicitud a entidades de trabajo de lista, se devuelve un máximo de 1.000 por solicitud. Si necesita realizar un seguimiento de todos los trabajos enviados, puede usar el principio o saltar como se describe en [las opciones de consulta de sistema de OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> localizadores no están diseñadas para administrar el control de acceso de cada usuario. Para asignar distintos derechos de acceso a los usuarios individuales, use las soluciones de administración de derechos digitales (DRM).

<sup>5</sup> las cuentas de almacenamiento deben ser de la misma suscripción de Azure.

<sup>6</sup> hay un límite de 1.000.000 directivas para distintas directivas de AMS (por ejemplo, para la directiva de localizador o ContentKeyAuthorizationPolicy). Debe usar el mismo ID de directiva si está utilizando siempre los mismos días / permisos de acceso / etcetera.
