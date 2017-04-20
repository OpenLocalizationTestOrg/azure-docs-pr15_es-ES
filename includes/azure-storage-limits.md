Recursos|Límite predeterminado
---|---
Número de cuentas de almacenamiento por suscripción|200<sup>1</sup>
TB por cuenta de almacenamiento|500 TB
Número máximo de contenedores de blob, BLOB, recursos compartidos de archivos, tablas, colas, entidades o mensajes por la cuenta de almacenamiento|Solo el límite es la capacidad de cuenta de 500 TB de almacenamiento
Tamaño máximo de un contenedor de blob único, tabla o cola|500 TB
Capacidad máxima número de bloques de un blob bloque o anexar blob|50.000
Tamaño máximo de un bloque en un blob bloque o anexar blob|4 MB
Tamaño máximo de un blob bloque o anexar blob|50.000 x 4 MB (aproximadamente 195 GB) 
Tamaño máximo de un blob de página |1 TB
Tamaño máximo de una entidad de tabla|1 MB
Número máximo de propiedades de una entidad de tabla|252
Tamaño máximo de un mensaje de una cola|64 KB
Tamaño máximo de un archivo compartido|5 TB
Tamaño máximo de un archivo en un archivo compartido|1 TB
Número máximo de archivos en un archivo compartido|Solo el límite es la capacidad total de 5 TB de recurso compartido de archivos
Max 8 KB IOPS por acción|1000
Número máximo de archivos en un archivo compartido|Solo el límite es la capacidad total de 5 TB de recurso compartido de archivos
Número máximo de contenedores de blob, BLOB, recursos compartidos de archivos, tablas, colas, entidades o mensajes por la cuenta de almacenamiento|Solo el límite es la capacidad de cuenta de 500 TB de almacenamiento
Número máximo de directivas de acceso almacenadas por contenedor, uso compartido de archivos, tabla o cola|5
Solicitar tasa total (suponiendo que el tamaño del objeto de 1KB) por cuenta de almacenamiento|Hasta 20.000 IOPS, entidades por segundo o mensajes por segundo
Rendimiento de destino para blob único|Hasta 60 MB por segunda o hasta 500 solicitudes por segundo
Rendimiento de destino para sola cola (mensajes de 1 KB)|Hasta 2000 mensajes por segundo
Rendimiento de destino para partición de tabla (entidades de 1 KB)|Hasta 2000 entidades por segundo
Rendimiento de destino para compartir de un solo archivo|Un máximo de 60 MB por segundo
Max entrada<sup>2</sup> por cuenta de almacenamiento (nos regiones)|10 GB/s si GRS/ZRS<sup>3</sup> habilitada, 20 GB/s para LRS
Max salida<sup>2</sup> por cuenta de almacenamiento (nos regiones)|20 GB/s si RA-GRS/GRS/ZRS<sup>3</sup> habilitada, 30 GB/s para LRS
Max entrada<sup>2</sup> por cuenta de almacenamiento (Europea y regiones de Asia)|5 GB/s si GRS/ZRS<sup>3</sup> habilitada, 10 GB/s para LRS
Max salida<sup>2</sup> por cuenta de almacenamiento (Europea y regiones de Asia)|10 GB/s si RA-GRS/GRS/ZRS<sup>3</sup> habilitada, 15 GB/s para LRS

<sup>1</sup> Esto incluye cuentas de almacenamiento estándar y Premium. Si necesita más de 200 cuentas de almacenamiento, realizar una solicitud a través de [Soporte técnico de Azure](https://azure.microsoft.com/support/faq/). El equipo de almacenamiento de Azure revisará su análisis de rentabilidad y puede aprobar hasta 250 cuentas de almacenamiento. 

<sup>2</sup> *Entrada* se refiere a todos los datos (solicitudes) que se envían a una cuenta de almacenamiento. *Salida* se refiere a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.  

<sup>3</sup> Opciones de replicación de almacenamiento de Azure incluyen:

- **RA GRS**: almacenamiento geo redundantes de acceso de lectura. Si está habilitada la RA GRS, destinos de salida para la ubicación secundaria son idénticos a los de la ubicación principal.
- **GRS**: almacenamiento Geo redundantes. 
- **ZRS**: almacenamiento zona redundantes. Disponible solo para blobs de bloque. 
- **LRS**: almacenamiento redundante local. 

