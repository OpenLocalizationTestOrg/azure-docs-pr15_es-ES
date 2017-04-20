<properties
    pageTitle="Introducción al almacenamiento | Microsoft Azure"
    description="Información general sobre el almacenamiento de Azure, almacenamiento de datos en línea de Microsoft en la nube. Obtenga información sobre cómo usar la mejor solución de almacenamiento de nube disponibles en las aplicaciones."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="introduction-to-microsoft-azure-storage"></a>Introducción al almacenamiento de Azure

## <a name="overview"></a>Información general

Almacenamiento de Azure es la solución de almacenamiento de la nube para aplicaciones modernas que se basan en la duración, disponibilidad y escalabilidad para satisfacer las necesidades de sus clientes. Lea este artículo, los desarrolladores, los profesionales de TI y Decisión empresarial creadores pueden obtener información acerca de:

- ¿Qué es el almacenamiento de Azure y cómo puede aprovechar las ventajas de la misma en la nube, móvil, servidor y aplicaciones de escritorio
- ¿Qué tipo de datos puede almacenar con los servicios de almacenamiento de Azure: blob de datos (objeto), datos de la tabla de NoSQL, mensajes de la cola y recursos compartidos de archivos.
- ¿Cómo se administra el acceso a los datos en el almacenamiento de Azure
- ¿Cómo los datos de almacenamiento de Azure se realizan resistentes a través de redundancia y replicación
- Dónde ir junto a crear su primera aplicación de almacenamiento de Azure

Para ponerse en marcha con el almacenamiento de Azure rápidamente, consulte [Introducción a Azure almacenamiento de cinco minutos](storage-getting-started-guide.md).

Para obtener más información sobre herramientas, bibliotecas y otros recursos para trabajar con el almacenamiento de Azure, vea [Pasos siguientes](#next-steps) .

## <a name="what-is-azure-storage"></a>¿Qué es el almacenamiento de Azure?

Permite nuevos escenarios para aplicaciones que requieren almacenamiento scalable, resistente y altamente disponible para sus datos, que es exactamente por qué Microsoft ha desarrollado Azure almacenamiento de informática en nube. Además de lo que permite a los desarrolladores generar aplicaciones a gran escala para admitir escenarios de nuevos, almacenamiento de Azure proporciona la base de almacenamiento para Azure máquinas virtuales, FE más su potencia.

Almacenamiento de Azure es masivamente scalable, por lo que puede almacenar y proceso cientos de terabytes de datos para admitir los escenarios grande de datos necesarios para análisis científico, financieros y aplicaciones multimedia. O bien, puede almacenar las pequeñas cantidades de datos necesarios para un sitio Web de pequeña empresa. Donde se encuentran sus necesidades, puede pagar sólo los datos que está almacenando. Almacenamiento de Azure actualmente almacena decenas de trillones de objetos de cliente único y controla millones de solicitudes por segundo en promedio.

Almacenamiento de Azure es elástico, por lo que puede diseñar aplicaciones para una gran audiencia global y ajustar las aplicaciones según sea necesario - tanto en términos de la cantidad de datos almacenados y el número de solicitudes realizadas en él. Pagar solo por lo que usa y solo cuando se usa.

Almacenamiento de Azure usa un sistema de partición automática que automáticamente equilibra la carga de los datos según el tráfico. Esto significa que como las peticiones de crecimiento de su aplicación, el almacenamiento de Azure asigna automáticamente los recursos adecuados para cumplir con ellos.

Almacenamiento de Azure es accesible desde cualquier lugar del mundo, desde cualquier tipo de aplicación, si se está ejecutando en la nube, en el escritorio, en un servidor local o en un teléfono móvil o el dispositivo de tableta. Puede usar el almacenamiento de Azure en escenarios móviles donde la aplicación almacena un subconjunto de datos en el dispositivo y sincroniza con un conjunto completo de los datos almacenados en la nube.

Almacenamiento de Azure es compatible con los clientes con una amplia variedad de sistemas operativos (incluido Windows y Linux) y una variedad de lenguajes de programación (incluyendo. NET, Java, Node.js, Python, Ruby, PHP y C++ y lenguajes de programación móviles) para el desarrollo de forma conveniente. Almacenamiento de Azure también expone recursos de datos a través de la API de REST simple, que están disponibles para cualquier cliente capaz de enviar y recibir datos a través de HTTP/HTTPS.

Almacenamiento de Azure Premium ofrece soporte de disco de alto rendimiento, baja latencia para i/OS intensivas cargas de trabajo en Azure máquinas virtuales de Windows. Con el almacenamiento de Azure Premium, puede adjuntar varios discos de datos persistente a una máquina virtual y configurarlos para satisfacer sus requisitos de rendimiento. Cada disco de datos se guarda en un disco de SSD en el almacenamiento de Azure Premium para obtener el máximo rendimiento de i/OS. Consulte [almacenamiento Premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](storage-premium-storage.md) para obtener más detalles.

## <a name="introducing-the-azure-storage-services"></a>Introducción a los servicios de almacenamiento de Azure

Almacenamiento de Azure proporciona los siguientes cuatro servicios: Blob almacenamiento, almacenamiento de tablas, almacenamiento en la cola y almacenamiento de archivos.

- Almacenamiento de blobs almacena los datos de objeto no estructurados. Un blob puede ser cualquier tipo de texto o datos binarios, como un documento, el archivo multimedia o el instalador de la aplicación. Almacenamiento de blobs también se conoce como almacenamiento de objetos.
- Almacenamiento de tablas almacena los conjuntos de datos estructurados. Almacenamiento de tablas es un almacén atributo de clave de datos, lo que permite el desarrollo rápido y acceso rápido a grandes cantidades de datos.
- Almacenamiento de cola proporciona mensajería confiable para el procesamiento de flujo de trabajo y para la comunicación entre los componentes de servicios en la nube.
- Almacenamiento de archivos ofrece almacenamiento compartido de aplicaciones heredadas mediante el protocolo SMB estándar. Máquinas virtuales de Windows Azure y servicios de nube pueden compartir datos de archivo en componentes de la aplicación a través de recursos compartidos montados y aplicaciones de local pueden tener acceso a los datos del archivo en un recurso compartido a través del servicio de archivo API de REST.

Una cuenta de almacenamiento de Azure es una cuenta de segura que le proporciona acceso a los servicios de almacenamiento de Azure. Su cuenta de almacenamiento proporciona el espacio de nombres único para sus recursos de almacenamiento. La imagen siguiente muestra las relaciones entre los recursos de almacenamiento de Azure en una cuenta de almacenamiento:

![Recursos de almacenamiento de Azure](./media/storage-introduction/storage-concepts.png)

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

[AZURE.INCLUDE [storage-versions-include](../../includes/storage-versions-include.md)]

## <a name="blob-storage"></a>Almacenamiento de blobs

Para que los usuarios con grandes cantidades de datos no estructurados objeto almacenar en la nube, almacenamiento de blobs ofrece una solución rentable y scalable. Puede usar el almacenamiento de blobs para almacenar contenido como:

- Documentos
- Datos sociales, como fotos, vídeos, música y blogs
- Copias de seguridad de archivos, equipos, bases de datos y dispositivos
- Imágenes y texto para aplicaciones web
- Datos de configuración de aplicaciones de nube
- Datos grandes, como registros y otras grandes conjuntos de datos

Cada blob se organiza en un contenedor. Contenedores también proporcionan una manera útil para asignar las directivas de seguridad a grupos de objetos. Una cuenta de almacenamiento puede contener cualquier número de contenedores y un contenedor puede contener cualquier número de blobs, hasta el límite de capacidad de 500 TB de la cuenta de almacenamiento.  

Tres tipos de ofertas de almacenamiento de blobs de manchas, bloquear BLOB, anexar BLOB y blobs de página (discos).

- Bloque BLOB está optimizado para la transmisión y el almacenamiento de objetos de la nube y es una buena opción para almacenar los documentos, archivos multimedia, etcetera las copias de seguridad.
- Anexar BLOB son similar a bloque BLOB, pero están optimizado para operaciones de datos anexados. Un blob de datos anexados se puede actualizar solo agregando un nuevo bloque al final. Anexar BLOB es una buena opción para escenarios como registro, donde deben escribirse al final del blob nuevos datos.
- BLOB de página está optimizado para representar IaaS discos y auxiliares aleatorio escribe y pueden ser hasta 1 TB. Conectado a una red de Azure máquina virtual IaaS disco es un disco duro virtual almacenado como un blob de página.

Para grandes conjuntos de datos donde las restricciones de red que carga o descarga de datos con el almacenamiento de blobs durante la conexión realista, puede enviar una unidad de disco duro a Microsoft para importar o exportar datos directamente desde el centro de datos. Consulte [usar el servicio de importación o exportación de Microsoft Azure para transferir datos de almacenamiento de blobs](storage-import-export-service.md).

## <a name="table-storage"></a>Almacenamiento de tablas

Las aplicaciones modernas a menudo demandan almacena datos con mayor escalabilidad y flexibilidad que las generaciones anteriores de software necesario. Almacenamiento de tablas ofrece almacenamiento altamente disponible, masivamente scalable, para que la aplicación puede cambiar automáticamente la escala para satisfacer la demanda de usuario. Almacenamiento de tablas es almacén clave o atributo de Microsoft, tiene un diseño schemaless, lo que diferentes de bases de datos relacionales tradicionales. Con un almacén de datos schemaless, es fácil adaptar los datos como las necesidades de su evolucionar de aplicación. Almacenamiento de tablas es fácil de usar, por lo que los desarrolladores pueden crear aplicaciones rápidamente. Acceso a datos es rápido y rentable para todos los tipos de aplicaciones.  Almacenamiento de tablas suele ser significativamente inferior en costo tradicional SQL para similares volúmenes de datos.

Almacenamiento de tablas es un almacén de clave de atributo, lo que significa que se almacena cada valor en una tabla con un nombre de propiedad escrito. El nombre de propiedad puede usarse para filtrar y especificar criterios de selección. Una colección de propiedades y sus valores constan de una entidad. Dado que schemaless almacenamiento de tablas, dos entidades en la misma tabla pueden contener diferentes colecciones de propiedades y las propiedades pueden ser de diferentes tipos.

Puede usar el almacenamiento de tablas para almacenar flexible conjuntos de datos, como los datos de usuario para las aplicaciones web, libretas de direcciones, la información del dispositivo y cualquier otro tipo de metadatos que requiere el servicio.  Puede almacenar cualquier número de entidades en una tabla y una cuenta de almacenamiento puede contener cualquier número de tablas, hasta el límite de capacidad de la cuenta de almacenamiento.

Como BLOB y colas, los desarrolladores pueden administrar y tabla de access protocolos de almacenamiento mediante el resto estándar, pero el almacenamiento de tablas también admite un subconjunto del Protocolo OData, simplificar las capacidades de consulta avanzada y habilitar JSON y AtomPub (basado en XML) formatos.

Para las aplicaciones basadas en Internet de hoy, las bases de datos NoSQL como almacenamiento de tablas ofrecen una alternativa más popular a bases de datos relacionales tradicionales.

## <a name="queue-storage"></a>Almacenamiento de cola

En el diseño de aplicaciones de escala, con qué frecuencia se separan componentes de la aplicación, para que puedan ampliar de forma independiente. Almacenamiento de cola proporciona una solución de mensajería confiable para la comunicación asincrónica entre componentes de la aplicación, si se están ejecutando en la nube, en el escritorio, en un servidor local o en un dispositivo móvil. Almacenamiento de cola también es compatible con administrar tareas asincrónicas y la creación de flujos de trabajo del proceso.

Una cuenta de almacenamiento puede contener cualquier número de colas. Una cola puede contener cualquier número de mensajes, hasta el límite de capacidad de la cuenta de almacenamiento. Mensajes individuales pueden hasta 64 KB de tamaño.

## <a name="file-storage"></a>Almacenamiento de archivos

Azure almacenamiento de archivos ofrece basada en nube SMB recursos compartidos de archivos, que puede migrar aplicaciones heredadas que se basan en recursos compartidos de archivos de Azure rápidamente y sin reescribe costosas. Con el almacenamiento de archivos de Azure, aplicaciones que se ejecutan en máquinas virtuales de Windows Azure o los servicios de nube pueden montaje de un recurso compartido de archivos en la nube, como una aplicación de escritorio montajes un recurso compartido de SMB típica. Cualquier número de componentes de la aplicación puede, a continuación, montaje y tener acceso a la cuota de almacenamiento de archivo al mismo tiempo.

Dado que un recurso compartido de almacenamiento de archivos es un recurso compartido de archivos SMB estándar, aplicaciones que se ejecutan en Azure pueden tener acceso a datos en el recurso compartido a través de APIs I/O del sistema de archivos. Por lo tanto, pueden aprovechar los desarrolladores su código existente y habilidades para migrar las aplicaciones existentes. Los profesionales de TI pueden usar cmdlets de PowerShell para crear, montaje y administrar recursos compartidos de almacenamiento de archivos como parte de la administración de aplicaciones de Azure.

Al igual que los servicios de almacenamiento de Azure, almacenamiento de archivos expone una API de REST para tener acceso a datos en un recurso compartido. Aplicaciones de local pueden llamar a la API de REST para tener acceso a datos en un recurso compartido de archivos de almacenamiento de archivos. De este modo, la empresa puede elegir migrar algunas aplicaciones heredadas a Azure y seguir ejecutando otros usuarios desde dentro de su propia organización. Tenga en cuenta que un recurso compartido de archivos de montaje solo es posible que las aplicaciones que se ejecutan en Azure; una aplicación local solo puede tener acceso a recurso compartido de archivos a través de la API de REST.

Aplicaciones distribuidas también pueden usar el almacenamiento de archivos para almacenar y compartir datos de aplicación útil, desarrollo y herramientas de prueba. Por ejemplo, una aplicación puede almacenar archivos de configuración y datos de diagnóstico como registros, métricas y bloqueo vuelca en un archivo de almacenamiento compartir para que estén disponibles para varias máquinas virtuales o roles. Los programadores y los administradores pueden almacenar utilidades que necesitan para crear o administrar una aplicación en un recurso compartido de almacenamiento de archivos que está disponible para todos los componentes, en lugar de instalarlos en cada máquina virtual o una instancia de la función.

## <a name="access-to-blob-table-queue-and-file-resources"></a>Acceso a Blob, la tabla, la cola y recursos de archivo

De forma predeterminada, solo el propietario de la cuenta de almacenamiento puede tener acceso a los recursos en la cuenta de almacenamiento. La seguridad de los datos, se debe autenticar cada solicitud realizada contra recursos en su cuenta. Autenticación se basa en un modelo de clave compartida. También puede configurarse BLOB para admitir autenticación anónima.

Su cuenta de almacenamiento se asigna dos claves de acceso privado de creación que se usan para la autenticación. Tiene dos claves garantiza que la aplicación sigue estando disponible cuando se regenera periódicamente las claves como una práctica común de administración de claves de seguridad.

Si necesita permitir a los usuarios acceso a los recursos de almacenamiento controlado, a continuación, puede crear una firma de acceso compartido. Una firma de acceso compartido (SA) es un símbolo que se puede anexar a una dirección URL que posibilita el acceso delegado a un recurso de almacenamiento. Cualquier persona que posee el token puede acceder al recurso apunta a con los permisos que se especifica, para el período de tiempo que sea válido. A partir de la versión 2015-04-05, almacenamiento Azure es compatible con dos tipos de firmas acceso compartido: SA de servicio y asociaciones de seguridad de la cuenta.

El servicio SA delega el acceso a un recurso en sólo uno de los servicios de almacenamiento: el servicio de blobs, cola, tabla o archivo.

Una cuenta SA delega el acceso a los recursos de uno o varios de los servicios de almacenamiento. Puede delegar el acceso a las operaciones de nivel de servicio que no están disponibles con un servicio SA. También puede delegar acceso para leer, escribir y eliminar operaciones en contenedores de blobs, tablas, colas y recursos compartidos de archivos que no se permiten con un servicio SA.

Por último, puede especificar que un contenedor y su BLOB o un blob específico, están disponibles para el acceso público. Si indica que un contenedor o blob es público, cualquiera puede leer de forma anónima; no se requiere autenticación.  BLOB y públicos contenedores es útil para exponer recursos como medios y documentos que se hospedan en sitios Web.  Para reducir la latencia de la red para una audiencia global, puede almacenar en caché los datos de blob usados por los sitios Web con la CDN de Azure.

Para obtener más información sobre las firmas de acceso compartido, consulte [Uso compartido acceso firmas (SA)](storage-dotnet-shared-access-signature-part-1.md) . Para obtener más información sobre el acceso seguro a su cuenta de almacenamiento, consulte [administrar el acceso anónimo lectura contenedores y BLOB](storage-manage-access-to-resources.md) y la [autenticación de los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx) .

## <a name="replication-for-durability-and-high-availability"></a>Replicación de alta disponibilidad y la duración

Los datos de su cuenta de Microsoft Azure almacenamiento siempre se duplica para garantizar alta disponibilidad y la duración. Duplicación de copia de los datos, dentro del mismo centro de datos o a un segundo centro de datos, dependiendo de la opción de replicación que elija. Replicación protege los datos y conserva el tiempo de la aplicación en caso de errores de hardware transitorias. Si los datos se repliquen en un segundo centro de datos, también protege los datos frente a un error grave en la ubicación principal.

La replicación garantiza que su cuenta de almacenamiento cumple con el [Contrato de nivel de servicio (SLA) para el almacenamiento](https://azure.microsoft.com/support/legal/sla/storage/) incluso en el caso de errores. Vea que el SLA para obtener información sobre el almacenamiento de Azure garantías de disponibilidad y la duración. 

Cuando se crea una cuenta de almacenamiento, puede seleccionar una de las siguientes opciones de replicación:  

- **Almacenamiento redundante local (LRS).** Almacenamiento redundante local mantiene tres copias de los datos. LRS se duplica tres veces dentro de un centro de datos único en una sola región. LRS protege los datos de errores de hardware normal, pero no desde el error de un centro de datos.  

    LRS se ofrece con un descuento. Por duración máxima, recomendamos que use almacenamiento geo redundantes, que se describen a continuación.


- **Almacenamiento de zona redundantes (ZRS).** Almacenamiento de zona redundantes mantiene tres copias de los datos. ZRS se duplica tres veces en todas las instalaciones de dos o tres, dentro de una única región o a través de dos regiones, lo que proporciona mayor duración que LRS. ZRS garantiza que los datos se resistentes dentro de una única región.  

    ZRS proporciona un mayor nivel de duración que LRS; Sin embargo, para la duración máxima, se recomienda que use almacenamiento geo redundantes, que se describen a continuación.  

    > [AZURE.NOTE] ZRS está disponible sólo para blobs de bloque y solo se admite para las versiones de 2014-02-14 y versiones posteriores.
    >
    > Una vez que ha creado su cuenta de almacenamiento y seleccionado ZRS, no se puede convertir uso para cualquier otro tipo de réplica, o viceversa.

- **Almacenamiento Geo redundantes (GRS)**. GRS mantiene seis copias de sus datos. Con GRS, los datos se duplica tres veces dentro de la región principal y también se duplica tres veces en una región secundaria cientos de millas fuera de la región principal, proporcionar el nivel de duración. Si se produce un error en la región principal, el almacenamiento de Azure hará failover en la región secundario. GRS garantiza que los datos están resistentes en dos regiones independientes.

    Para obtener información acerca de los pares de primaria y secundarias por región, vea [Áreas de Azure](https://azure.microsoft.com/regions/).

- **Almacenamiento de acceso de lectura geo redundantes (RA GRS)**. Almacenamiento de acceso de lectura geo redundantes duplica los datos en una ubicación geográfica secundaria y también proporciona acceso de lectura a los datos en la ubicación secundaria. Almacenamiento de acceso de lectura geo redundantes le permite acceder a los datos desde el principal o la ubicación secundaria, en caso de que una ubicación no está disponible. Almacenamiento de acceso de lectura geo redundantes es la opción predeterminada para su cuenta de almacenamiento predeterminada cuando lo cree. 

    > [AZURE.IMPORTANT] Puede cambiar cómo los datos se repliquen después de crear la cuenta de almacenamiento, a menos que especifique ZRS al crear la cuenta. Sin embargo, tenga en cuenta que puede provocar a una transferencia de datos de una sola vez adicionales si cambia de LRS a GRS o GRS RA de costo.

Para obtener más información acerca de las opciones de replicación de almacenamiento, consulte [replicación de almacenamiento de Azure](storage-redundancy.md) .

Para información sobre precios para replicación de la cuenta de almacenamiento, consulte [Precios de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/). Para obtener más información sobre qué servicios están disponibles en cada región, vea [Regiones de Azure](https://azure.microsoft.com/regions/#services) .

Para obtener detalles arquitectónicas sobre duración con el almacenamiento de Azure, consulte [SOSP papel - almacenamiento de Azure: A altamente disponible almacenamiento servicio de nube con texto en negrita coherencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).


## <a name="transferring-data-to-and-from-azure-storage"></a>Transferir datos hacia y desde el almacenamiento de Azure

Puede usar la utilidad de línea de comandos AzCopy para copiar blob, archivo y datos de una tabla en su cuenta de almacenamiento o entre cuentas de almacenamiento. Para obtener más información, consulte [transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md) .

AzCopy se basa en la [Biblioteca de movimiento de datos de Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), que está disponible actualmente en la vista previa.

El servicio de importación o exportación de Azure proporciona una manera de importar los datos blob o exportar datos de blobs de su cuenta de almacenamiento a través de un disco duro que haya enviado al centro de datos de Azure. Para obtener más información sobre el servicio de importación o exportación, consulte [usar el servicio de importación o exportación de Microsoft Azure a transferir datos al almacenamiento de blobs de Windows](storage-import-export-service.md).

## <a name="pricing"></a>Precios

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

## <a name="storage-apis-libraries-and-tools"></a>Herramientas, bibliotecas y las API de almacenamiento

Recursos de almacenamiento de Azure pueden tener acceso a cualquier idioma que puede hacer que las solicitudes HTTP/HTTPS. Además, el almacenamiento de Azure ofrece bibliotecas de programación para varios idiomas populares. Estas bibliotecas simplifican muchos aspectos del trabajo con el almacenamiento de Azure controlar detalles como invocación sincrónico y asincrónico, los lotes de operaciones, administración de excepciones, reintentos automáticos, comportamiento operativo y así sucesivamente. Bibliotecas ya están disponibles para los siguientes idiomas y plataformas con otras personas de la canalización de:

### <a name="azure-storage-data-services"></a>Servicios de datos de almacenamiento de Azure

- [API de REST de servicios de almacenamiento](http://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Biblioteca de cliente de almacenamiento para .NET, Windows Phone y tiempo de ejecución de Windows](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Biblioteca de cliente de almacenamiento para C++](https://github.com/Azure/azure-storage-cpp)
- [Biblioteca de cliente de almacenamiento para Java o Android](/develop/java/)
- [Biblioteca de cliente de almacenamiento para Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
- [Biblioteca de cliente de almacenamiento para PHP](/develop/php/)
- [Biblioteca de cliente de almacenamiento para Ruby](/develop/ruby/)
- [Biblioteca de cliente de almacenamiento para Python](/develop/python/)
- [Cmdlets de almacenamiento para PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt269418.aspx)

### <a name="azure-storage-management-services"></a>Servicios de administración de almacenamiento de Azure

- [Referencia de la API de REST de proveedor de servicios de almacenamiento recursos](https://msdn.microsoft.com/library/azure/mt163683.aspx)
- [Biblioteca de cliente de proveedor de recursos de almacenamiento para .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx)
- [Cmdlets de proveedor de recursos de almacenamiento para PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt607151.aspx)
- [API de REST de administración de servicio de almacenamiento (clásico)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-services"></a>Servicios de movimiento de datos de almacenamiento de Azure

- [API de REST de servicio de importación o exportación de almacenamiento](https://msdn.microsoft.com/library/azure/dn529096.aspx)
- [Biblioteca de cliente de movimiento de datos de almacenamiento para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### <a name="tools-and-utilities"></a>Herramientas y utilidades

- [Explorador de almacenamiento de Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
- [Herramientas de cliente de almacenamiento de Azure](storage-explorers.md)
- [Herramientas y SDK de azure](https://azure.microsoft.com/tools/)
- [Almacenamiento de Azure emulador](http://www.microsoft.com/download/details.aspx?id=43709)
- [Azure PowerShell](../powershell-install-configure.md)
- [Utilidad de línea de comandos de AzCopy](http://aka.ms/downloadazcopy)

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el almacenamiento de Azure, explore estos recursos:

### <a name="documentation"></a>Documentación

- [Documentación de almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)

### <a name="for-administrators"></a>Para los administradores

- [Usar PowerShell Azure con el almacenamiento de Azure](storage-powershell-guide-full.md)
- [Utilice CLI Azure con almacenamiento de Azure](storage-azure-cli.md)

### <a name="for-net-developers"></a>Para desarrolladores de .NET

- [Introducción a almacenamiento de blobs de Windows Azure con .NET](storage-dotnet-how-to-use-blobs.md)
- [Introducción a almacenamiento de tablas de Azure con .NET](storage-dotnet-how-to-use-tables.md)
- [Introducción a almacenamiento de Azure cola mediante .NET](storage-dotnet-how-to-use-queues.md)
- [Introducción a almacenamiento de archivos de Azure en Windows](storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>Para desarrolladores de Java o Android

- [Cómo usar el almacenamiento de blobs de Java](storage-java-how-to-use-blob-storage.md)
- [Cómo usar el almacenamiento de tablas de Java](storage-java-how-to-use-table-storage.md)
- [Cómo usar el almacenamiento de la cola de Java](storage-java-how-to-use-queue-storage.md)
- [Cómo usar el almacenamiento de archivos de Java](storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>Para desarrolladores de Node.js

- [Cómo usar el almacenamiento de blobs de Node.js](storage-nodejs-how-to-use-blob-storage.md)
- [Cómo usar el almacenamiento de tablas de Node.js](storage-nodejs-how-to-use-table-storage.md)
- [Cómo usar el almacenamiento de la cola de Node.js](storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Para desarrolladores de PHP

- [Cómo usar el almacenamiento de blobs de PHP](storage-php-how-to-use-blobs.md)
- [Cómo usar el almacenamiento de tablas de PHP](storage-php-how-to-use-table-storage.md)
- [Cómo usar el almacenamiento de la cola de PHP](storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Para desarrolladores ideogramas y transcripción fonética

- [Cómo usar el almacenamiento de blobs de rubí](storage-ruby-how-to-use-blob-storage.md)
- [Cómo usar el almacenamiento de tablas de rubí](storage-ruby-how-to-use-table-storage.md)
- [Cómo usar el almacenamiento de la cola de rubí](storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Para desarrolladores de Python

- [Cómo usar el almacenamiento de blobs de Python](storage-python-how-to-use-blob-storage.md)
- [Cómo usar el almacenamiento de tablas de Python](storage-python-how-to-use-table-storage.md)
- [Cómo usar el almacenamiento de la cola de Python](storage-python-how-to-use-queue-storage.md)
- [Cómo usar el almacenamiento de archivos de Python](storage-python-how-to-use-file-storage.md)
