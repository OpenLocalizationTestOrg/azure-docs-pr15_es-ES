<properties
    pageTitle="Resultados del análisis de secuencia: opciones de almacenamiento, análisis | Microsoft Azure"
    description="Obtenga más información sobre opciones de resultados de datos de análisis de secuencia incluidos Power BI para los resultados del análisis de identificación."
    keywords="transformación de datos, los resultados del análisis, opciones de almacenamiento de datos"
    services="stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage"
    documentationCenter="" 
    authors="jeffstokes72"
    manager="jhubbard" 
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>Resultados del análisis de secuencia: opciones de almacenamiento, análisis

Cuando cree una tarea de análisis de secuencia, considere la posibilidad de cómo se consume los datos resultantes. ¿Cómo ver los resultados de la tarea de análisis de secuencia y donde almacenará?

Para habilitar una variedad de modelos de aplicación, análisis de secuencia de Azure tiene diferentes opciones para almacenar resultados y ver los resultados del análisis. Esto hace que sea fácil ver la salida de trabajo y proporciona flexibilidad para el consumo y el almacenamiento de la salida de trabajo de almacenamiento de datos y otros fines. Ningún resultado configurado en el trabajo debe existir antes de que la tarea se inicia y eventos Iniciar flujo. Por ejemplo, si utiliza el almacenamiento de blobs como salida, el trabajo no creará una cuenta de almacenamiento automáticamente. Debe ser creados por el usuario antes de que se inicia el trabajo ASA.

## <a name="azure-data-lake-store"></a>Almacén de lago de datos de Azure

Análisis de secuencia admite [Lago almacén de datos de Azure](https://azure.microsoft.com/services/data-lake-store/). Este almacenamiento le permite almacenar datos de cualquier tamaño, tipo y recopilación de velocidad para análisis operativa y exploración. En este momento, la creación y la configuración de almacén de datos lago salidas es compatible únicamente en el Portal de clásico de Azure. Más allá, análisis de secuencia debe estar autorizado para obtener acceso al almacén de datos lago. Obtener más información sobre cómo iniciar sesión en la vista previa de la tienda de lago de datos (si es necesario) y la autorización se describe en el [artículo de salida de lago de datos](stream-analytics-data-lake-output.md).

### <a name="authorize-an-azure-data-lake-store"></a>Autorizar un almacén de lago de datos de Azure

Cuando se selecciona el almacenamiento de datos lago como un resultado en el portal de administración de Azure, le pedirá que lo autorizar una conexión a un almacén de lago de datos existente.  

![Autorizar el almacén de datos de lago](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

A continuación, rellene las propiedades para la salida de almacén de datos lago tal como se muestra a continuación:

![Autorizar el almacén de datos de lago](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

La siguiente tabla enumera los nombres de propiedad y sus descripciones necesarios para crear un almacén de datos lago de salida.

<table>
<tbody>
<tr>
<td><B>NOMBRE DE PROPIEDAD</B></td>
<td><B>DESCRIPCIÓN</B></td>
</tr>
<tr>
<td>Alias de salida</td>
<td>Se trata de un nombre descriptivo que se usan en las consultas para dirigir el resultado de la consulta a este almacén de lago de datos.</td>
</tr>
<tr>
<td>Nombre de la cuenta</td>
<td>El nombre de la cuenta de almacenamiento de lago de datos que desea enviar el resultado. Aparecerá una lista desplegable de cuentas de la tienda de lago de datos a la que el usuario que ha iniciado sesión en el portal tiene acceso a.</td>
</tr>
<tr>
<td>Trama de prefijo del ruta [<I>opcional</I>]</td>
<td>La ruta de acceso para escribir los archivos dentro de la cuenta de la tienda de lago de datos especificada. <BR>{date}, {time}<BR>Ejemplo 1: carpeta 1/registros / {date} / {time}<BR>Ejemplo 2: carpeta 1/registros / {date}</td>
</tr>
<tr>
<td>Formato de fecha [<I>opcional</I>]</td>
<td>Si el token de fecha se usa en la ruta de acceso de prefijo, puede seleccionar el formato de fecha en la que se organizan los archivos. Ejemplo: Aaaa/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [<I>opcional</I>]</td>
<td>Si el token de tiempo se usa en la ruta de acceso de prefijo, especifique el formato de hora en que se organizan los archivos. Actualmente, el único valor admitido es HH.</td>
</tr>
<tr>
<td>Formato de serialización de evento</td>
<td>Formato de serialización de datos de salida. JSON, CSV y Avro son compatibles.</td>
</tr>
<tr>
<td>Codificación</td>
<td>Si el formato CSV o JSON, se debe especificar una codificación. UTF-8 es el formato de codificación compatible solo en este momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Sólo aplicable para serialización CSV. Análisis de secuencia es compatible con un número de delimitadores comunes para serializar datos CSV. Valores admitidos son coma, punto y coma, espacio, ficha y barra vertical.</td>
</tr>
<tr>
<td>Formato</td>
<td>Sólo aplicable para serialización JSON. Línea separado indica que se da formato al resultado haciendo que cada objeto JSON separado por una nueva línea. Matriz especifica que el resultado tendrá formato como una matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Renovar autorización de almacén de datos lago

Debe volver a autenticar su cuenta de almacén de datos lago si ha cambiado su contraseña desde su trabajo se creó o autenticado por última vez.

![Autorizar el almacén de datos de lago](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  


## <a name="sql-database"></a>Base de datos SQL

[Base de datos de SQL Azure](https://azure.microsoft.com/services/sql-database/) puede usarse como salida para los datos relacionales de naturaleza o aplicaciones que dependen de contenido que se hospeda en una base de datos relacional. Tareas de análisis de secuencia escribirá a una tabla existente en una base de datos de SQL Azure.  Tenga en cuenta que el esquema de la tabla debe coincidir exactamente con los campos y los tipos de salida de su trabajo. Un [Almacén de datos de SQL Azure](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) también puede especificarse como un resultado a través de la base de datos SQL opción de salida también (es decir, una característica de vista previa). La siguiente tabla enumera los nombres de propiedad y sus descripciones para crear una base de datos SQL de salida.

| Nombre de propiedad | Descripción |
|---------------|-------------|
| Alias de salida | Se trata de un nombre descriptivo que se usan en las consultas para dirigir el resultado de la consulta para esta base de datos. |
| Base de datos | El nombre de la base de datos que desea enviar los resultados |
| Nombre del servidor | El nombre del servidor de base de datos SQL |
| Nombre de usuario | El nombre de usuario que tiene acceso a escribir en la base de datos |
| Contraseña | La contraseña para conectarse a la base de datos |
| Tabla | El nombre de la tabla donde se escribirá el resultado. El nombre de tabla distingue mayúsculas de minúsculas y el esquema de esta tabla debe coincidir exactamente con el número de campos y los tipos de generados por la salida de trabajo. |

> [AZURE.NOTE] La oferta de base de datos de SQL Azure es compatible actualmente para la salida de trabajo en el análisis de secuencia. Sin embargo, no se admite una máquina Virtual de Azure ejecuta SQL Server con una base de datos adjuntado. Esto está sujeto a cambios en futuras versiones.

## <a name="blob-storage"></a>Almacenamiento de blobs

Almacenamiento de blobs ofrece una solución rentable y scalable para almacenar grandes cantidades de datos no estructurados en la nube.  Para obtener una introducción sobre el almacenamiento de blobs de Windows Azure y su uso, consulte la documentación en [cómo usar BLOB](../storage/storage-dotnet-how-to-use-blobs.md).

La siguiente tabla enumera los nombres de propiedad y sus descripciones para crear un resultado de blobs de Windows.

<table>
<tbody>
<tr>
<td>NOMBRE DE PROPIEDAD</td>
<td>DESCRIPCIÓN</td>
</tr>
<tr>
<td>Alias de salida</td>
<td>Se trata de un nombre descriptivo que se usan en las consultas para dirigir el resultado de la consulta para el almacenamiento de blobs.</td>
</tr>
<tr>
<td>Cuenta de almacenamiento</td>
<td>El nombre de la cuenta de almacenamiento que desea enviar el resultado.</td>
</tr>
<tr>
<td>Clave de cuenta de almacenamiento</td>
<td>La clave secreta asociada con la cuenta de almacenamiento.</td>
</tr>
<tr>
<td>Contenedor de almacenamiento</td>
<td>Los contenedores proporcionan una agrupación lógica para blobs almacenado en el servicio de Microsoft Azure Blob. Cuando se carga un blob al servicio Blob, debe especificar un contenedor de blob.</td>
</tr>
<tr>
<td>Trama de prefijo del ruta [opcional]</td>
<td>La ruta de acceso de archivo que se utiliza para escribir el BLOB dentro del contenedor especificado.<BR>Dentro de la ruta de acceso, puede utilizar una o varias instancias de las siguientes 2 variables para especificar la frecuencia con la que se escribe BLOB:<BR>{date}, {time}<BR>Ejemplo 1: cluster1/registros / {date} / {time}<BR>Ejemplo 2: cluster1/registros / {date}</td>
</tr>
<tr>
<td>Formato de fecha [opcional]</td>
<td>Si el token de fecha se usa en la ruta de acceso de prefijo, puede seleccionar el formato de fecha en la que se organizan los archivos. Ejemplo: Aaaa/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Si el token de tiempo se usa en la ruta de acceso de prefijo, especifique el formato de hora en que se organizan los archivos. Actualmente, el único valor admitido es HH.</td>
</tr>
<tr>
<td>Formato de serialización de evento</td>
<td>Formato de serialización de datos de salida.  JSON, CSV y Avro son compatibles.</td>
</tr>
<tr>
<td>Codificación</td>
<td>Si el formato CSV o JSON, se debe especificar una codificación. UTF-8 es el formato de codificación compatible solo en este momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Sólo aplicable para serialización CSV. Análisis de secuencia es compatible con un número de delimitadores comunes para serializar datos CSV. Valores admitidos son coma, punto y coma, espacio, ficha y barra vertical.</td>
</tr>
<tr>
<td>Formato</td>
<td>Sólo aplicable para serialización JSON. Línea separado indica que se da formato al resultado haciendo que cada objeto JSON separado por una nueva línea. Matriz especifica que el resultado tendrá formato como una matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

## <a name="event-hub"></a>Hub de evento

[Evento Hubs](https://azure.microsoft.com/services/event-hubs/) es una gran escalabilidad publicación suscripción ingestor del evento. Pueden recopilar millones de eventos por segundo.  Un uso de un concentrador de evento como resultado es el resultado de una tarea de análisis de secuencia será la entrada de otro flujo de trabajo.

Hay algunos parámetros que son necesarios para configurar los flujos de datos de evento concentrador como salida.

| Nombre de propiedad | Descripción |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de salida | Se trata de un nombre descriptivo que se usan en las consultas para dirigir el resultado de la consulta a este concentrador de evento. |
| Namespace de Bus de servicio | Un espacio de nombres de Bus de servicio es un contenedor para un conjunto de entidades de mensajería. Cuando crea un nuevo evento de concentrador, también crea un espacio de nombres de Bus de servicio |
| Hub de evento | El nombre de la salida de concentrador de evento |
| Nombre de la directiva de evento concentrador | La directiva de acceso compartido, que puede crearse en la pestaña configurar de concentrador de evento. Cada directiva de acceso compartido tendrá un nombre, los permisos que ha configurado, y las teclas de acceso |
| Clave de directiva de concentrador de evento | Tecla de acceso compartido utilizada para autenticar el acceso al espacio de nombres de Bus de servicio |
| Columna de clave de partición [opcional] | Esta columna contiene la clave de partición para la salida de concentrador de evento. |
| Formato de serialización de evento | Formato de serialización de datos de salida.  JSON, CSV y Avro son compatibles. |
| Codificación | CSV y JSON, UTF-8 es el formato de codificación compatible solo en este momento |
| Delimitador | Sólo aplicable para serialización CSV. Análisis de secuencia es compatible con un número de delimitadores comunes para serializar datos en formato CSV. Valores admitidos son coma, punto y coma, espacio, ficha y barra vertical. |
| Formato | Sólo aplicable para tipo JSON. Línea separado indica que se da formato al resultado haciendo que cada objeto JSON separado por una nueva línea. Matriz especifica que el resultado tendrá formato como una matriz de objetos JSON. |

## <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com/) puede usarse como un resultado para una tarea de análisis de secuencia para proporcionar una experiencia de visualización enriquecido de resultados del análisis. Esta función puede usarse para paneles operativos, generación de informes y métrica condicionada por el informe.

### <a name="authorize-a-power-bi-account"></a>Autorizar una cuenta de Power BI

1.  Cuando se selecciona Power BI como un resultado en el portal de administración de Azure, se le pedirá para autorizar a un usuario de BI de Power existente o para crear una nueva cuenta de Power BI.  

    ![Autorizar usuario de Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  

2.  Crear una cuenta nueva si no aún tiene una, y luego haga clic en autorizar ahora.  Se presenta una pantalla como la siguiente.  

    ![Cuenta de Azure Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  

3.  En este paso, proporcione la cuenta profesional o educativa para autorizar el resultado de Power BI. Si no se ya dispone para Power BI, seleccione Iniciar ahora. La cuenta de trabajo o escuela que uso para Power BI podría ser diferente a la cuenta de suscripción de Azure que ha iniciado sesión.

### <a name="configure-the-power-bi-output-properties"></a>Configurar las propiedades de salida de Power BI

Una vez autenticada la cuenta de Power BI, puede configurar las propiedades de la salida de Power BI. La tabla siguiente es la lista de nombres de propiedad y su descripción para configurar la salida de Power BI.

| Nombre de propiedad | Descripción |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de salida | Se trata de un nombre descriptivo que se usan en las consultas para dirigir el resultado de la consulta para este resultado PowerBI. |
| Área de trabajo de grupo | Para habilitar el uso compartido de datos con otros usuarios de Power BI puede seleccionar grupos dentro de su cuenta de Power BI o elija "Mi área de trabajo" Si no desea escribir en un grupo.  Actualizar un grupo existente requiere renovando la autenticación de Power BI. | 
| Nombre del conjunto de datos | Proporcione un nombre de conjunto de datos que se desea que use el resultado de Power BI |
| Nombre de tabla | Proporcione un nombre de tabla en el conjunto de datos de la salida de Power BI. Actualmente, los resultados de Power BI de trabajos de análisis de secuencia solo pueden tener una tabla en un conjunto de datos |

Para un análisis de configuración de un resultado de Power BI y paneles, consulte el artículo de [análisis de secuencia de Azure y Power BI](stream-analytics-power-bi-dashboard.md) .

> [AZURE.NOTE] No crear explícitamente el conjunto de datos y la tabla en el panel de Power BI. El conjunto de datos y la tabla se rellenará automáticamente cuando se inicia el trabajo y el trabajo inicia salida bombeo en Power BI. Tenga en cuenta que si la consulta de trabajo no genera ningún resultado, el conjunto de datos y la tabla no se creará. También tenga en cuenta que si Power BI ya tiene un conjunto de datos y una tabla con el mismo nombre que se proporcionan en este trabajo de análisis de secuencia, se sobrescribirán los datos existentes.

### <a name="renew-power-bi-authorization"></a>Renovar autorización de Power BI

Debe volver a autenticar su cuenta de Power BI si ha cambiado su contraseña desde su trabajo se creó o autenticado por última vez. Si se configura la autenticación multifactor (AMF) en su inquilino de Azure Active Directory (AAD) también debe renovar autorización de Power BI cada 2 semanas. Un síntoma de este problema es ningún resultado de trabajo y un "error de usuario de autenticar" en los registros de operación:

  ![Error de token de actualización Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Para resolver este problema, detenga el trabajo en ejecución y vaya a la salida de Power BI.  Haga clic en el vínculo "Autorización de renovar" y reiniciar el trabajo desde la última vez detenido para evitar la pérdida de datos.

  ![Power BI renovar autorización](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Almacenamiento de tablas

[Almacenamiento de tablas de Azure](../storage/storage-introduction.md) ofrece almacenamiento altamente disponible, masivamente scalable, para que una aplicación puede cambiar automáticamente la escala para satisfacer la demanda de usuario. Almacenamiento de tablas es clave o atributo almacén de Microsoft cuál puede aprovechar para datos estructurados con menos restricciones en el esquema. Almacenamiento de tablas de Azure puede utilizarse para almacenar los datos de persistencia y recuperación eficaz.

La siguiente tabla enumera los nombres de propiedad y sus descripciones para crear un resultado de la tabla.

| Nombre de propiedad | Descripción |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de salida | Se trata de un nombre descriptivo que se usan en las consultas para dirigir el resultado de la consulta para el almacenamiento de la tabla. |
| Cuenta de almacenamiento | El nombre de la cuenta de almacenamiento que desea enviar el resultado. |
| Clave de cuenta de almacenamiento | Tecla de acceso asociada a la cuenta de almacenamiento. |
| Nombre de tabla | El nombre de la tabla. La tabla obtener creará si no existe. |
| Clave de partición | El nombre de la columna de resultados que contiene la clave de partición. La clave de partición es un identificador único para la partición dentro de una tabla dada que la primera parte de la clave principal de una entidad de formularios. Es un valor de cadena que puede estar hasta 1 KB de tamaño. |
| Clave de fila | El nombre de la columna de resultados que contiene la clave de la fila. La clave de la fila es un identificador único para una entidad dentro de una partición determinada. Formularios de la segunda parte de la clave principal de una entidad. La clave de la fila es un valor de cadena que puede estar hasta 1 KB de tamaño. |
| Tamaño del lote | El número de registros para una operación por lotes. Normalmente, el valor predeterminado es suficiente para la mayoría de los trabajos, consulte la [especificación de operación del lote de tabla](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para obtener más detalles acerca de cómo modificar esta configuración. |

## <a name="service-bus-queues"></a>Colas de Bus de servicio

[Colas de Bus de servicio](https://msdn.microsoft.com/library/azure/hh367516.aspx) ofrecen un primer de entrega de mensajes de la primera salida (FIFO) a uno o varios de los consumidores competencia. Normalmente, los mensajes se espera que se recibió y procesados por los receptores en el orden temporal en el que se agregaron a la cola y cada mensaje se ha recibido y procesado por los consumidores de un solo mensaje.

La siguiente tabla enumera los nombres de propiedad y sus descripciones para la creación de una cola de salida.

| Nombre de propiedad | Descripción |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de salida | Se trata de un nombre descriptivo que se usan en las consultas para dirigir el resultado de la consulta a esta cola de Bus de servicio. |
| Namespace de Bus de servicio | Un espacio de nombres de Bus de servicio es un contenedor para un conjunto de entidades de mensajería. |
| Nombre de la cola | El nombre de la cola de Bus de servicio. |
| Nombre de la directiva de cola | Cuando se crea una cola, también puede crear directivas de acceso compartido en la pestaña configurar cola. Cada directiva de acceso compartido tiene un nombre, los permisos que ha configurado, y las teclas de acceso. |
| Clave de la directiva de cola | Tecla de acceso compartido utilizada para autenticar el acceso al espacio de nombres de Bus de servicio |
| Formato de serialización de evento | Formato de serialización de datos de salida.  JSON, CSV y Avro son compatibles. |
| Codificación | CSV y JSON, UTF-8 es el formato de codificación compatible solo en este momento |
| Delimitador | Sólo aplicable para serialización CSV. Análisis de secuencia es compatible con un número de delimitadores comunes para serializar datos en formato CSV. Valores admitidos son coma, punto y coma, espacio, ficha y barra vertical. |
| Formato | Sólo aplicable para tipo JSON. Línea separado indica que se da formato al resultado haciendo que cada objeto JSON separado por una nueva línea. Matriz especifica que el resultado tendrá formato como una matriz de objetos JSON. |

## <a name="service-bus-topics"></a>Temas de Bus de servicio

Aunque colas de Bus de servicio proporcionan un método de comunicación de uno a uno del remitente al receptor, [Servicio Bus temas](https://msdn.microsoft.com/library/azure/hh367516.aspx) se proporciona un formulario de uno a varios de comunicación.

La siguiente tabla enumera los nombres de propiedad y sus descripciones para crear un resultado de la tabla.

| Nombre de propiedad | Descripción |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de salida | Se trata de un nombre descriptivo que se usan en las consultas para dirigir el resultado de la consulta a este tema de Bus de servicio. |
| Namespace de Bus de servicio | Un espacio de nombres de Bus de servicio es un contenedor para un conjunto de entidades de mensajería. Cuando crea un nuevo evento de concentrador, también crea un espacio de nombres de Bus de servicio |
| Nombre del tema | Temas son mensajería entidades, similares a las colas y hubs de evento. Que están diseñados para recopilar secuencias de eventos de un número de distintos dispositivos y servicios. Cuando se crea un tema, también se le dé un nombre específico. Los mensajes enviados a un tema no estará disponibles a menos que se crea una suscripción, lo asegurarse de que hay una o varias suscripciones en el tema |
| Nombre de la directiva de tema | Al crear un tema, también puede crear directivas de acceso compartido en la pestaña configurar tema. Cada directiva de acceso compartido tendrá un nombre, los permisos que ha configurado, y las teclas de acceso |
| Clave de la directiva de tema | Tecla de acceso compartido utilizada para autenticar el acceso al espacio de nombres de Bus de servicio |
| Formato de serialización de evento | Formato de serialización de datos de salida.  JSON, CSV y Avro son compatibles. |
| Codificación | Si el formato CSV o JSON, se debe especificar una codificación. UTF-8 es el formato de codificación compatible solo en este momento |
| Delimitador | Sólo aplicable para serialización CSV. Análisis de secuencia es compatible con un número de delimitadores comunes para serializar datos en formato CSV. Valores admitidos son coma, punto y coma, espacio, ficha y barra vertical. |

## <a name="documentdb"></a>DocumentDB

[DocumentDB de Azure](https://azure.microsoft.com/services/documentdb/) es un totalmente administrados NoSQL documento base de datos servicio que ofrece consultas y transacciones sobre datos libres de esquema, rendimiento predecible y confiable y desarrollo rápido.

La siguiente tabla enumera los nombres de propiedad y sus descripciones para crear un resultado de DocumentDB.

<table>
<tbody>
<tr>
<td>NOMBRE DE PROPIEDAD</td>
<td>DESCRIPCIÓN</td>
</tr>
<tr>
<td>Nombre de la cuenta</td>
<td>El nombre de la cuenta DocumentDB.  También puede ser el punto final de la cuenta.</td>
</tr>
<tr>
<td>Clave de cuenta</td>
<td>La tecla de acceso compartido para la cuenta de DocumentDB.</td>
</tr>
<tr>
<td>Base de datos</td>
<td>El nombre de la base de datos DocumentDB.</td>
</tr>
<tr>
<td>Patrón de nombre de la colección</td>
<td>El modelo de nombre de colección para las colecciones de usarse. El formato de nombre de la colección puede crearse mediante el token opcional {partición}, donde iniciar particiones de 0.<BR>Por ejemplo Las siguientes son entradas válidas:<BR>MyCollection {partición}<BR>MyCollection<BR>Tenga en cuenta que colecciones deben existir antes de que el trabajo de análisis de secuencia se inicia y no se crearán automáticamente.</td>
</tr>
<tr>
<td>Clave de partición</td>
<td>El nombre del campo en eventos de salida que se utiliza para especificar la clave de partición de salida entre colecciones.</td>
</tr>
<tr>
<td>Identificador de documento</td>
<td>El nombre del campo en eventos de salida se utiliza para especificar la clave principal que insertar o actualizar las operaciones se basan en.</td>
</tr>
</tbody>
</table>


## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
Ya conoce a análisis de secuencia, un servicio de transmisión de análisis de datos de Internet de lo administrado. Para obtener más información sobre este servicio, consulte:

- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
