<properties
    pageTitle="Azure almacenamiento interesante para blobs | Microsoft Azure"
    description="Niveles de almacenamiento para el almacenamiento de blobs de Windows Azure ofrecen almacenamiento rentable de datos de objeto en función de los patrones de acceso. El nivel de almacenamiento interesante está optimizado para los datos que se accede con menos frecuencia."
    services="storage"
    documentationCenter=""
    authors="michaelhauss"
    manager="vamshik"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="mihauss"/>


# <a name="azure-blob-storage-hot-and-cool-storage-tiers"></a>Almacenamiento de blobs de Windows Azure: Caliente y enfriar niveles de almacenamiento

## <a name="overview"></a>Información general

Almacenamiento de Azure ahora ofrece dos niveles de almacenamiento de blobs almacenamiento (objeto), para que pueda almacenar los datos más rentable según cómo usarlo. El **nivel de almacenamiento de acceso rápido** de Azure está optimizado para almacenar los datos que se tiene acceso con frecuencia. El **nivel de almacenamiento interesantes** de Azure está optimizado para almacenar los datos con poca frecuencia acceder y larga duración. Datos en el nivel de almacenamiento interesantes pueden tolerar una disponibilidad ligeramente inferior, pero aún requieren alta duración y similar tiempo para acceder a y las características de rendimiento como datos de acceso rápidos. Datos interesantes, ligeramente inferior SLA de disponibilidad y costos más altos de access son aceptables compensaciones para tanto los costos de almacenamiento.

En la actualidad, los datos almacenados en la nube es creciendo a un ritmo exponencial. Para administrar los costos de sus necesidades de almacenamiento de expansión, resulta útil organizar los datos según atributos como la frecuencia de acceso y el período de retención planeadas. Los datos almacenados en la nube pueden ser muy diferentes en función de cómo se genera, procesado y acceso a través de su duración. Algunos datos de forma activa es acceder y modificar a lo largo de su duración. Algunos datos se tiene acceso con frecuencia temprano en su duración con soltar considerablemente como la edad de datos de access. Algunos datos permanecen inactivos en la nube y suele, si alguna vez, tiene acceso a la vez almacenarán.

Cada uno de estos escenarios de acceso a datos descritos anteriormente ventajas de un nivel de almacenamiento optimizado para un patrón de acceso determinada diferenciado. Con la introducción de niveles de almacenamiento de acceso rápido y fríos, blobs de Windows Azure almacenamiento direcciones ahora esta necesidad de niveles de almacenamiento diferenciados con separar modelos de precios.

## <a name="blob-storage-accounts"></a>Cuentas de almacenamiento de blobs

**Cuentas de almacenamiento de blobs** son almacenamiento especializado cuentas para almacenar los datos estructurados como BLOB (objetos) en el almacenamiento de Azure. Con cuentas de almacenamiento de blobs, ahora puede elegir entre niveles de almacenamiento de acceso rápido e interesantes que se pueden almacenar los datos interesantes que se accede con menos frecuencia en un almacenamiento de menor costo y almacenan los datos de acceso rápidos más frecuente en un acceso menor coste. Cuentas de almacenamiento de blobs son similares a las cuentas de almacenamiento general existentes y compartan todos los la duración excelente, disponibilidad, escalabilidad y características de rendimiento que usa actualmente, incluido coherencia de API de 100% para blobs de bloque y anexar BLOB.

> [AZURE.NOTE] Cuentas de almacenamiento de blobs sólo bloque de soporte técnico y anexar BLOB y no blobs de página.

Cuentas de almacenamiento de blobs exponen el atributo de **Nivel de acceso** , que le permiten especificar el nivel de almacenamiento como **activa** o **interesante** según los datos almacenados en la cuenta. Si hay un cambio en el patrón de uso de los datos, también puede cambiar entre los niveles de almacenamiento en cualquier momento.

> [AZURE.NOTE] Cambiar el nivel de almacenamiento puede traducirse en cargos adicionales. Vea la sección de [precios y facturación](storage-blob-storage-tiers.md#pricing-and-billing) para obtener más detalles.

Escenarios de uso de ejemplo para el nivel de almacenamiento de acceso rápido se incluyen:

- Datos que está en uso o que puedan acceder a (lectura desde y se escriben en) con frecuencia.
- Datos de prueba de migración de procesamiento y final para el nivel de almacenamiento interesantes.

Escenarios de uso de ejemplo para el nivel de almacenamiento interesantes que se pueden incluyen:

- Copia de seguridad, archivado y conjuntos de datos de recuperación de desastres.
- Contenido multimedia antiguo no verse con frecuencia ya, pero se espera que estén disponibles inmediatamente cuando se tiene acceso.
- Grandes conjuntos de datos que deben estar almacenado costo eficaz mientras se recopilan los datos más para el procesamiento de futuro. (*por ejemplo*, almacenamiento a largo plazo de datos científicos, los datos de telemetría sin formato de un servicio de fabricación)
- (Datos originales sin procesar) que deben conservarse, incluso después de que se ha procesado en forma utilizable final. (*por ejemplo*, archivos sin formato multimedia tras la transcodificación a otros formatos)
- Cumplimiento y archivados datos que se deben almacenar durante mucho tiempo y casi nunca se tiene acceso. (*por ejemplo*, material de archivo de cámara de seguridad, antiguo X-radiografías/Resonance para las organizaciones de salud, las grabaciones de audio y transcripciones de llamadas al cliente para servicios financieros)

Para obtener más información sobre cuentas de almacenamiento, consulte [acerca de Azure cuentas de almacenamiento](storage-create-storage-account.md) .

Para las aplicaciones que requieren solo bloquear o almacenamiento de blobs de datos anexados, se recomienda utilizar cuentas de almacenamiento de blobs, aprovechar el modelo de precios diferenciado de almacenamiento. Sin embargo, comprendemos que esto puede no ser posible en determinadas circunstancias donde con cuentas de almacenamiento general sería la forma, como por ejemplo:

- Debe usar tablas, colas o archivos y desea que su BLOB almacenado en la misma cuenta de almacenamiento. Observe que no existe ninguna ventaja técnica para almacenar en la misma cuenta que no sea que tienen la misma claves compartidas.
- Aún debe usar el modelo de implementación de clásico. Cuentas de almacenamiento de blobs solo están disponibles a través del modelo de implementación de administrador de recursos de Azure.
- Debe usar blobs de página. Cuentas de almacenamiento de blobs de Windows no admiten blobs de página. Normalmente, se recomienda usar bloque BLOB a menos que tenga una necesidad específica de blobs de página.
- Usar una versión de la [API de REST de servicios de almacenamiento](https://msdn.microsoft.com/library/azure/dd894041.aspx) que es anterior a 2014-02-14 o una biblioteca de cliente con una versión inferior 4.x y no se puede actualizar la aplicación.

> [AZURE.NOTE] Cuentas de almacenamiento de blobs actualmente son compatibles con la mayoría de las regiones Azure con más para seguir. Puede encontrar la lista actualizada de regiones disponibles en la página [Servicios de Azure por región](https://azure.microsoft.com/regions/#services) .

## <a name="comparison-between-the-storage-tiers"></a>Comparación entre los niveles de almacenamiento

La siguiente tabla resalta la comparación entre los dos niveles de almacenamiento:

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250">
<col width="250">
<col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Nivel de almacenamiento de acceso rápido</center></strong></td>
    <td><strong><center>Nivel de almacenamiento interesante</center></strong></td
</tr>
<tr>
    <td><strong><center>Disponibilidad</center></strong></td>
    <td><center>99,9%</center></td>
    <td><center>99%</center></td>
</tr>
<tr>
    <td><strong><center>Disponibilidad<br>(Lecturas GRS RA)</center></strong></td>
    <td><center>99,99%</center></td>
    <td><center>99,9%</center></td>
</tr>
<tr>
    <td><strong><center>Cargos de uso</center></strong></td>
    <td><center>Costos más altos de almacenamiento<br>Reducir los costos de transacciones y access</center></td>
    <td><center>Reducir los costos de almacenamiento<br>Costos más altos de access y transacción</center></td>
</tr>
<tr>
    <td><strong><center>Tamaño mínimo de objeto<center></strong></td>
    <td colspan="2"><center>N/A.</center></td>
</tr>
<tr>
    <td><strong><center>Duración mínima de almacenamiento<center></strong></td>
    <td colspan="2"><center>N/A.</center></td>
</tr>
<tr>
    <td><strong><center>Latencia<br>(Tiempo al primer byte)<center></strong></td>
    <td colspan="2"><center>milisegundos.</center></td>
</tr>
<tr>
    <td><strong><center>Objetivos de rendimiento y escalabilidad<center></strong></td>
    <td colspan="2"><center>Iguales que las cuentas de almacenamiento general</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Cuentas de almacenamiento de blobs admiten el mismo performance y destinos escalabilidad como cuentas de almacenamiento general. Para obtener más información, vea [destinos de rendimiento y escalabilidad de almacenamiento de Azure](storage-scalability-targets.md) .

## <a name="pricing-and-billing"></a>Precios y facturación

Cuentas de almacenamiento de blobs usan un modelo de precios de nuevo para el almacenamiento de blobs de Windows basado en el nivel de almacenamiento. Cuando se usa una cuenta de almacenamiento de blobs, se aplican las siguientes consideraciones de facturación:

- **Los costos de almacenamiento**: además de la cantidad de datos almacenados, el costo de almacenamiento de datos varía según el nivel de almacenamiento. El costo por gigabyte es inferior para el nivel de almacenamiento interesantes que para el nivel de almacenamiento de acceso rápido.
- **Los costos de acceso a datos**: datos en el nivel de almacenamiento interesante, se cobrará un cargo de acceso de datos por gigabyte de leer y escribir.
- **Los costes de transacción**: no hay un cargo por transacción para ambos niveles. Sin embargo, el costo por transacción para el nivel de almacenamiento interesante es mayor que para el nivel de almacenamiento de acceso rápido.
- **Los costos de transferencia de datos de replicación geo**: Esto se aplica únicamente a las cuentas con geo replicación configurada, incluidos GRS y RA GRS. Transferencia de datos de replicación geo incurre en un cargo por gigabyte.
- **Transferir costes de datos salientes**: transferencias de datos salientes (datos que se transfieren fuera de un área de Azure) incurre en facturación para el uso de ancho de banda de forma por gigabyte, coherente con cuentas de almacenamiento general.
- **Cambiar el nivel de almacenamiento**: cambiar el nivel de almacenamiento de buenas a caliente provocará un cargo igual a todos los datos existentes en la cuenta de almacenamiento para cada transición de lectura. Por otro lado, cambiar el nivel de almacenamiento de acceso rápido a buenas estará libre de costo.

> [AZURE.NOTE] Para permitir a los usuarios probar la nueva niveles de almacenamiento y validar posterior al lanzamiento funcionalidad, el cargo para cambiar el almacenamiento de nivel de buenas a caliente se cobrará desactivar hasta el 30 de junio de 2016. A partir de 1 de julio de 2016, el cargo se aplicará a todas las transiciones de buenas a caliente. Para obtener más detalles sobre el modelo de precios para el almacenamiento de blobs de cuentas, consulte página [Precios de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/) . Para obtener más detalles sobre los datos de salida cargos de transferencia, consulte página de [Detalles de precios de transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/) .

## <a name="quick-start"></a>Inicio rápido

En esta sección se mostrará los siguientes escenarios con el portal de Azure:

- Cómo crear una cuenta de almacenamiento de blobs de Windows.
- Cómo administrar una cuenta de almacenamiento de blobs de Windows.

### <a name="using-the-azure-portal"></a>Con el portal de Azure

#### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Crear una cuenta de almacenamiento de blobs con el portal de Azure

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).

2. En el menú de concentrador, seleccione **nuevo** > **datos + almacenamiento** > **cuenta de almacenamiento**.

3. Escriba un nombre para su cuenta de almacenamiento.

    Este nombre debe ser único global; se utiliza como parte de la dirección URL utilizada para tener acceso a los objetos de la cuenta de almacenamiento.  

4. Seleccione **El Administrador de recursos** como el modelo de implementación.

    Almacenamiento sólo puede utilizarse con cuentas de almacenamiento de administrador de recursos; Este es el modelo de implementación recomendado para los nuevos recursos. Para obtener más información, consulte la [información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md).  

5. En la lista desplegable Tipo de cuenta, seleccione el **Almacenamiento de blobs**.

    Se trata en el que seleccionar el tipo de cuenta de almacenamiento. Almacenamiento de información no está disponible en almacenamiento general; solo está disponible en la cuenta de tipo de almacenamiento de blobs de Windows.    

    Tenga en cuenta que, cuando se selecciona esta opción, el nivel de rendimiento se establece en estándar. Almacenamiento de información no está disponible con el nivel de rendimiento Premium.

6. Seleccione la opción de replicación de la cuenta de almacenamiento: **LRS**, **GRS**o **RA GRS**. El valor predeterminado es **RA GRS**.

    LRS = almacenamiento redundante local; GRS = almacenamiento geo redundantes (2 regiones); RA GRS es almacenamiento geo redundantes de acceso de lectura (2 regiones con acceso de lectura a la segunda).

    Para obtener más detalles sobre las opciones de replicación de almacenamiento de Azure, consulte [replicación de almacenamiento de Azure](storage-redundancy.md).

7. Seleccione el nivel de almacenamiento adecuada según las necesidades: establecer el **nivel de acceso** a **atractivo** o **activa**. El valor predeterminado es **activa**.

8. Seleccione la suscripción en la que desea crear la nueva cuenta de almacenamiento.

9. Especificar un nuevo grupo de recursos o seleccione un grupo de recursos existente. Para obtener más información sobre los grupos de recursos, vea [información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md).

10. Seleccione la región de su cuenta de almacenamiento.

11. Haga clic en **crear** para crear la cuenta de almacenamiento.

#### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Cambiar el nivel de almacenamiento de una cuenta de almacenamiento de blobs con el portal de Azure

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).

2. Para ir a su cuenta de almacenamiento, seleccione todos los recursos, seleccione su cuenta de almacenamiento.

3. En el módulo de configuración, haga clic en **configuración** para ver o cambiar la configuración de cuenta.

4. Seleccione el nivel de almacenamiento adecuada según las necesidades: establecer el **nivel de acceso** a **atractivo** o **activa**.

5. Haga clic en Guardar en la parte superior de la hoja.

> [AZURE.NOTE] Cambiar el nivel de almacenamiento puede traducirse en cargos adicionales. Vea la sección de [precios y facturación](storage-blob-storage-tiers.md#pricing-and-billing) para obtener más detalles.

## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>Evaluar y migrar a cuentas de almacenamiento de blobs

Es la finalidad de esta sección ayudar a los usuarios realizar una transición suave a con cuentas de almacenamiento de blobs de Windows. Existen dos escenarios de usuario:

- Tiene una cuenta de almacenamiento general y desea evaluar un cambio a una cuenta de almacenamiento de blobs con el nivel de almacenamiento derecha.
- Ha decidido usar una cuenta de almacenamiento de blobs o ya tiene una y desea evaluar si debe utilizar el nivel de almacenamiento de acceso rápido o interesante.

En ambos casos, la primera regla de negocio es estimar el costo de almacenamiento y acceso a los datos almacenados en una cuenta de almacenamiento de blobs y compárelo con los costos actuales.

### <a name="evaluating-blob-storage-account-tiers"></a>Evaluar los niveles de cuenta de almacenamiento de blobs

Para calcular el costo de almacenamiento y acceso a datos almacenados en una cuenta de almacenamiento de blobs, necesita evaluar el patrón de uso existente o aproximar el patrón de uso esperado. En general, le interesará saber:

- Su consumo de almacenamiento: ¿cómo se almacenan datos reales y cómo altera esto mensualmente?
- ¿El patrón de acceso de almacenamiento - la cantidad de datos se está leer y escribir en la cuenta (incluidos los nuevos datos)? ¿Cuántas transacciones se usan para el acceso a datos y qué tipos de transacciones son?

#### <a name="monitoring-existing-storage-accounts"></a>Supervisar las cuentas de almacenamiento existentes

Para supervisar sus cuentas de almacenamiento existente y recopilar estos datos, puede hacer que el uso de análisis de almacenamiento de Azure que realiza el registro y proporciona los datos de métricas para una cuenta de almacenamiento.
Análisis de almacenamiento puede almacenar métricas que incluyen datos de estadísticas y la capacidad de transacción agregado sobre las solicitudes de servicio de almacenamiento de blobs para cuentas de almacenamiento general así como cuentas de almacenamiento de blobs.
Estos datos se almacenan en tablas conocidos en la misma cuenta de almacenamiento.

Para obtener más detalles, vea [Información sobre el análisis de métrica de almacenamiento](https://msdn.microsoft.com/library/azure/hh343258.aspx) y [Esquema de la tabla de métrica de almacenamiento análisis](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [AZURE.NOTE] Cuentas de almacenamiento de blobs exponen el extremo de servicio de la tabla solo para almacenar y tener acceso a los datos de métricas para la cuenta.

Para supervisar el consumo de almacenamiento para el servicio de almacenamiento de blobs, debe habilitar las métricas de capacidad.
Con esta opción habilitada, los datos de capacidad está registrados diaria de servicio de una cuenta de almacenamiento blobs y se registrará como una entrada de tabla que se escribe en la tabla *$MetricsCapacityBlob* dentro de la misma cuenta de almacenamiento.

Para supervisar el patrón de acceso de datos para el servicio de almacenamiento de blobs, debe habilitar las estadísticas de transacciones por horas en un nivel de la API.
Con esta opción habilitada, por la API de transacciones son agregadas cada hora y registradas como una entrada de tabla que se escribe en la tabla *$MetricsHourPrimaryTransactionsBlob* dentro de la misma cuenta de almacenamiento. La tabla *$MetricsHourSecondaryTransactionsBlob* registros las transacciones al extremo secundario en el caso de cuentas de almacenamiento RA GRS.

> [AZURE.NOTE] En caso de que tiene una cuenta de almacenamiento general en el que haya almacenado blobs de página y discos de máquina virtual junto con bloque y anexar datos blob, este proceso de estimación no es aplicable. Esto es porque no tendrá ningún modo de capacidad distintiva y métricas de transacciones en función del tipo de blobs para solo bloquear y anexar BLOB que se puede migrar a una cuenta de almacenamiento de blobs de Windows.

Para obtener una buena aproximación del patrón de acceso y el consumo de datos, se recomienda que elija un período de retención para la métrica de representante de su uso normal y extrapolar.
Una opción es conservar los datos de métricas durante 7 días y recopilar los datos de cada semana para análisis al final del mes.
Otra opción es conservar los datos de métricas durante los últimos 30 días y recopilar y analizar los datos al final del período de 30 días.

Para obtener más información sobre cómo habilitar, recopilar y ver datos de medidas, vea, [métrica de almacenamiento de Azure habilitar y ver datos de métricas](storage-enable-and-view-metrics.md).

> [AZURE.NOTE] Almacenar, acceder y descargar los datos de análisis también se cargarán como datos de usuario habitual.

#### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Uso de medidas de uso para estimar los costos

##### <a name="storage-costs"></a>Costos de almacenamiento

La última entrada de la capacidad métricas tabla *$MetricsCapacityBlob* con la clave de fila *'datos'* muestra la capacidad de almacenamiento consumida por datos de usuario.
La última entrada de la capacidad métricas tabla *$MetricsCapacityBlob* con la clave de fila *'análisis'* muestra la capacidad de almacenamiento consumida por los registros de análisis.

Esta capacidad total consumido por los datos de usuario y los registros de análisis (si está habilitado), a continuación, se pueden utilizar para calcular el costo de almacenamiento de datos en la cuenta de almacenamiento.
El mismo método también se puede utilizar para calcular los costos de almacenamiento de bloque y anexar BLOB en cuentas de almacenamiento general.

##### <a name="transaction-costs"></a>Costes de transacción

La suma de *'TotalBillableRequests'*, en todas las entradas para una API en la tabla de métricas transacción indica el número total de transacciones para la API determinado. *por ejemplo*, el número total de transacciones *'GetBlob'* en un período determinado se puede calcular la suma de las solicitudes de facturables total para todas las entradas con la clave de fila *' usuario; GetBlob'*.

Para calcular los costos de transacción para las cuentas de almacenamiento de blobs, debe desglosar las transacciones en tres grupos, ya que son precio de forma diferente.

- Escribir las transacciones como *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'*y *'CopyBlob'*.
- Eliminar las transacciones como *'DeleteBlob'* y *'DeleteContainer'*.
- Todas las demás transacciones.

Para calcular los costos de transacción para las cuentas de almacenamiento general, debe agregar todas las transacciones con independencia de la API de operación.

##### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Datos de access y la replicación de geo transferir los costos

Mientras el análisis de almacenamiento no proporciona la cantidad de datos, leer y escribir a una cuenta de almacenamiento, puede aproximadamente estimada consultando la tabla de estadísticas de transacciones.
La suma de *'TotalIngress'* en todas las entradas para una API en la tabla de métricas transacción indica la cantidad total de datos de entrada en bytes para la API determinado.
Del mismo modo, la suma de *'TotalEgress'* indica la cantidad total de datos de salida, en bytes.

Para calcular los costos de acceso de datos de cuentas de almacenamiento de blobs, debe desglosar las transacciones en dos grupos.

- La cantidad de datos recuperados de la cuenta de almacenamiento se puede estimar consultando la suma de *'TotalEgress'* para principalmente las operaciones *'GetBlob'* y *'CopyBlob'* .
- Buscando la suma de *'TotalIngress'* principalmente la *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* y *'AppendBlock'* operaciones se puede estimar la cantidad de datos que se escriben en la cuenta de almacenamiento.

También puede calcular el costo de transferencia de datos de replicación geo para cuentas de almacenamiento de blobs usando la estimación de la cantidad de datos que se escriben en caso de una cuenta de almacenamiento GRS o RA GRS.

> [AZURE.NOTE] Para obtener un ejemplo más detallado sobre cómo calcular los costos para usar el nivel de almacenamiento de acceso rápido o bien, eche un vistazo a las preguntas más frecuentes titulados *'¿qué son los niveles de acceso activa y buenas y ¿cómo debo determinar qué uno a use?'* en la [página de precios de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/).

### <a name="migrating-existing-data"></a>Migrar datos existentes

Una cuenta de almacenamiento de blobs especializadas para almacenar solo bloque y anexar BLOB. Cuentas de almacenamiento general existentes, que le permiten almacenar tablas, colas, archivos y discos, así como BLOB, no se puede convertir a cuentas de almacenamiento de blobs de Windows. Para usar los niveles de almacenamiento, debe crear nuevas cuentas de almacenamiento de blobs y migrar los datos existentes a las cuentas recién creadas.
Puede usar los siguientes métodos para migrar los datos existentes a cuentas de almacenamiento de blobs de dispositivos de almacenamiento local, proveedores de almacenamiento de nube de terceros o de sus cuentas de almacenamiento general existente en Azure:

#### <a name="azcopy"></a>AzCopy

AzCopy es una herramienta de línea de comandos de Windows diseñada para la copia de alto rendimiento de datos hacia y desde el almacenamiento de Azure. Puede usar AzCopy para copiar datos a su cuenta de almacenamiento de blobs de sus cuentas de almacenamiento general existente o cargar datos desde sus dispositivos de almacenamiento local en su cuenta de almacenamiento de blobs de Windows.

Para obtener más detalles, consulte [transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md).

#### <a name="data-movement-library"></a>Biblioteca de movimiento de datos

Biblioteca de movimiento de datos de Azure almacenamiento de .NET se basa en el marco de trabajo de movimiento de datos de principales que potencia AzCopy. La biblioteca está diseñada para operaciones de transferencia de datos de alto rendimiento, confiable y fácil similar a AzCopy. Esto le permite aprovechar todas las ventajas de las características proporcionadas por AzCopy en su aplicación nativa sin tener que tratar con ejecutar y supervisar externos instancias de AzCopy.

Para obtener más detalles, vea [Biblioteca de movimiento de datos de almacenamiento de Azure para .net](https://github.com/Azure/azure-storage-net-data-movement)

#### <a name="rest-api-or-client-library"></a>API de REST o la biblioteca de cliente

Puede crear una aplicación personalizada para migrar los datos a una cuenta de almacenamiento de blobs con una de las bibliotecas de cliente de Azure o los servicios de almacenamiento de Azure API de REST. Almacenamiento de Azure proporciona bibliotecas de cliente enriquecido para varios idiomas y plataformas como. NET, Java, C++, Node.JS, PHP, Ruby y Python. Las bibliotecas de cliente ofrecen capacidades avanzadas, como la lógica de reintento, registro y cargas paralelas. También puede desarrollar directamente con la API de REST, puede llamar a cualquier idioma que realiza las solicitudes de HTTP/HTTPS.

Para obtener más detalles, vea [Introducción a almacenamiento de blobs de Windows Azure](storage-dotnet-how-to-use-blobs.md).

> [AZURE.NOTE] BLOB cifrada con cifrado de cliente almacena metadatos relacionados con el cifrado almacenados con el objeto blob. Es fundamental que cualquier mecanismo de copia debe asegurarse de que los metadatos de blobs de Windows y a continuación, especialmente los metadatos relacionados con el cifrado, se conserva. Si copia la BLOB sin metadatos, el contenido de blob no ser recuperable nuevamente. Para obtener más detalles acerca de los metadatos de cifrado, vea [cifrado de Azure almacenamiento de cliente](storage-client-side-encryption.md).

## <a name="faqs"></a>Preguntas más frecuentes

1. **¿Cuentas de almacenamiento disponibles aún existentes?**

    Sí, cuentas de almacenamiento existentes siguen estando disponibles y no cambiarán de precio o funcionalidad.  Que no tiene la posibilidad de elegir un nivel de almacenamiento y no tendrán las capacidades de niveles en el futuro.

2. **¿Por qué y cuándo debo empezar con cuentas de almacenamiento de blobs de Windows?**

    Cuentas de almacenamiento de blobs están especializadas para el almacenamiento de blobs y nos permiten introducir nuevas características blob centrada. En el futuro, cuentas de almacenamiento de blobs son el método recomendado para el almacenamiento de blobs, como funciones como almacenamiento jerárquico en el futuro e interconexión presentarán en función de este tipo de cuenta. Sin embargo, es hasta al que desea migrar en función de sus necesidades empresariales.

3. **¿Puedo convertir mi cuenta de almacenamiento existente a una cuenta de almacenamiento de blobs de Windows?**

    No. Cuenta de almacenamiento de blobs es otro tipo de cuenta de almacenamiento y debe crear nueva y migrar los datos, como se explica anteriormente.

4. **¿Puedo almacenar objetos en dos niveles de almacenamiento en la misma cuenta?**

    El atributo *' nivel de acceso '* que indica el nivel de almacenamiento se establece en un nivel de la cuenta y se aplica a todos los objetos de esa cuenta. No puede establecer el atributo de nivel de acceso a un nivel de objeto.

5. **¿Puedo cambiar el nivel de almacenamiento de mi cuenta de almacenamiento de blobs de Windows?**

    Sí. Podrá cambiar el nivel de almacenamiento estableciendo el atributo *' nivel de acceso '* en la cuenta de almacenamiento. Cambiar el nivel de almacenamiento se aplicará a todos los objetos almacenados en la cuenta. Cambiar el nivel de almacenamiento de acceso rápido a buenas no verá afectado los cargos al cambiar de buenas a caliente provocará un por costo GB para leer todos los datos de la cuenta.

6. **¿Con qué frecuencia se puede cambiar el nivel de almacenamiento de mi cuenta de almacenamiento de blobs de Windows?**

    Mientras se aplica una limitación en la frecuencia con la se puede cambiar el nivel de almacenamiento, tenga en cuenta que cambiar el nivel de almacenamiento de buenas a caliente producirá cargos significativos. No se recomienda cambiar el nivel de almacenamiento con frecuencia.

7. **¿Se los BLOB en el nivel de almacenamiento interesantes que se pueden comportarse de manera diferente a los que en el nivel de almacenamiento de acceso rápido?**

    BLOB en el nivel de almacenamiento de acceso rápido tiene el mismo latencia como BLOB en cuentas de almacenamiento general. BLOB en el nivel de almacenamiento interesantes tiene una latencia similar (en milisegundos) como BLOB en cuentas de almacenamiento general.

    BLOB en el nivel de almacenamiento interesantes tendrá un ligeramente por debajo del nivel de servicio de disponibilidad (SLA) que los BLOB almacenados en el nivel de almacenamiento de acceso rápido. Para obtener más detalles, consulte [SLA de almacenamiento](https://azure.microsoft.com/support/legal/sla/storage).

8. **¿Puedo almacenar blobs de página y discos de máquinas virtuales en cuentas de almacenamiento de blobs de Windows?**

    Cuentas de almacenamiento de blobs sólo bloque de soporte técnico y anexar BLOB y no blobs de página. Discos de Azure máquinas virtuales se copia por blobs de página y como resultado no se pueden usar cuentas de almacenamiento de blobs para almacenar discos de máquina virtual. Sin embargo es posible almacenar copias de seguridad de los discos de máquina virtual como BLOB bloque en una cuenta de almacenamiento de blobs.

9. **¿Tendré que cambiar mis aplicaciones existentes para usar cuentas de almacenamiento de blobs de Windows?**

    Cuentas de almacenamiento de blobs son coherentes con las cuentas de almacenamiento general para bloque API de 100% y anexar BLOB. Siempre y cuando la aplicación está utilizando bloquear BLOB o anexar blobs y está usando la versión de 2014-02-14 de la [API de REST de servicios de almacenamiento](https://msdn.microsoft.com/library/azure/dd894041.aspx) o mayor, a continuación, la aplicación debería funcionar. Si está utilizando una versión anterior del protocolo, deberá actualizar la aplicación para utilizar la nueva versión a fin de trabajar sin problemas con los dos tipos de cuentas de almacenamiento. En general, siempre recomienda utilizar la versión más reciente, independientemente de la cuenta de almacenamiento que escriba usamos.

10. **¿Habrá un cambio en la experiencia del usuario?**

    Cuentas de almacenamiento de blobs son muy similares a una cuenta de almacenamiento general para el bloque de almacenamiento y anexar BLOB y admiten todas las características claves de almacenamiento de Azure, incluida la duración alta y disponibilidad, escalabilidad, rendimiento y seguridad. Distinto de las características y restricciones específicas de cuentas de almacenamiento de blobs y sus niveles de almacenamiento que se ha llamado anteriormente, todo lo demás es el mismo.

## <a name="next-steps"></a>Pasos siguientes

### <a name="evaluate-blob-storage-accounts"></a>Evaluar las cuentas de almacenamiento de blobs

[Comprobar la disponibilidad de cuentas de almacenamiento de blobs por región](https://azure.microsoft.com/regions/#services)

[Evaluar el uso de sus cuentas de almacenamiento actual habilitando métrica de almacenamiento de Azure](storage-enable-and-view-metrics.md)

[Comprobar los precios de almacenamiento de blobs por región](https://azure.microsoft.com/pricing/details/storage/)

[Transferencias de datos de comprobación de precios](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>Empezar a usar cuentas de almacenamiento de blobs

[Introducción a almacenamiento de blobs de Windows Azure](storage-dotnet-how-to-use-blobs.md)

[Mover datos a y desde el almacenamiento de Azure](storage-moving-data.md)

[Transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md)

[Examinar y explorar sus cuentas de almacenamiento](http://storageexplorer.com/)
