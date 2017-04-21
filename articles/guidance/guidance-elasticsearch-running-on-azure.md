<properties
   pageTitle="Ejecuta Elasticsearch en Azure | Microsoft Azure"
   description="Cómo instalar, configurar y ejecutar Elasticsearch en Azure."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>

# <a name="running-elasticsearch-on-azure"></a>Ejecuta Elasticsearch en Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie](guidance-elasticsearch.md). 

## <a name="overview"></a>Información general

Este documento proporciona una breve introducción a la estructura general de Elasticsearch y, a continuación, describe cómo implementar un clúster de Elasticsearch con Azure. Toque en mejores prácticas para implementar un clúster de Elasticsearch concentrarse en los diversos funcional rendimiento y los requisitos de administración de su sistema y teniendo en cuenta cómo deben conducir los requisitos de la configuración y la topología que seleccione.

> [AZURE.NOTE] Esta guía supone algunas familiaridad básica con [Elasticsearch][].

## <a name="the-structure-of-elasticsearch"></a>La estructura de Elasticsearch 

Elasticsearch es una base de datos de documento optimizada para que actúe como un motor de búsqueda. Documentos están serie en formato JSON. Datos están guardados en índices, implementados con [Apache Lucene][], aunque se extrae los detalles de la vista y no es necesario entender totalmente Lucene para usar Elasticsearch.

### <a name="clusters-nodes-indexes-and-shards"></a>Clústeres, nodos, índices y shards

Elasticsearch implementa una arquitectura agrupada que usa sharding para distribuir datos en varios nodos y replicación para proporcionar alta disponibilidad. Documentos se almacenan en índices. El usuario puede especificar qué campos de un documento se usan para identificar dentro de un índice, o el sistema puede generar un campo de clave y valores automáticamente. El índice se usa para física organizar documentos y es que significa que la entidad de seguridad para buscar documentos. 

Un índice contiene un conjunto de shards. Dispersión uniformemente de documentos a través de shards mediante un mecanismo hash basándose en los valores de clave de índice y el número de shards en el índice. 

Los índices se pueden replicar. En este caso se copia cada fragmentar en el índice. Elasticsearch garantiza que cada fragmentar original para un índice (denominados "partes principales") y su réplica residen siempre en distintos nodos. Cuando se agrega o se modifica un documento, todo escribir operaciones se realizan en las partes principales primero y, a continuación, en cada réplica. 

La figura siguiente muestra los aspectos fundamentales de un clúster de Elasticsearch que contiene tres nodos. Se ha creado un índice que consta de dos shards principales con dos réplicas para cada fragmentar (seis shards en todos).

![](media/guidance-elasticsearch/general-cluster1.png)

*Un clúster de Elasticsearch simple que contiene dos nodos principales y dos conjuntos de réplicas*

En este clúster principal partes 1 y 2 de partes principales se encuentran en nodos independientes para ayudar a equilibrar la carga entre ellas. Las réplicas están distribuidas de forma similar. Si se produce un error en un único nodo, el resto de los nodos tiene suficiente información para habilitar el sistema continuar funcionando. Si es necesario, Elasticsearch le asignará un fragmentar réplica para convertirse en un partes principales si las partes principal correspondiente no está disponible.

### <a name="node-roles"></a>Funciones de nodo

Los nodos en un clúster de Elasticsearch pueden realizar las siguientes funciones:

- **Nodo de datos** que puede contener una o más shards que contienen datos de índice.

- **Nodo de cliente** que no contiene los datos de índice pero que trata las solicitudes entrantes realizadas por las aplicaciones cliente el nodo de datos adecuados.
 
- Un **nodo principal** que contiene los datos de índice pero que ejecuta las operaciones de administración de clúster, como el mantenimiento y distribuir información de enrutamiento alrededor del clúster (la lista de las cuales los nodos contienen qué shards), determinar qué nodos están disponibles, reubicar shards como nodos aparecen y desaparecen y coordinar la recuperación después de un error de nodo. Varios nodos pueden configurarse como patrones, pero solo uno realmente se elegido para realizar las funciones principales. Si se produce un error en este nodo, otra elección lleve a cabo y uno de los nodos maestros elegibles se elegido y tomar el control.

> [AZURE.NOTE]El nodo maestro seleccionado es fundamental para el bienestar del clúster. El resto de los nodos ping periódicamente para asegurarse de que esté disponible. Si el nodo de patrón seleccionado también actúa como un nodo de datos, hay una posibilidad de que el nodo puede convertirse en ocupado y éxito para responder a estos ping. En este caso, el patrón se considera que ha fallado y uno de los nodos de patrón se elige en su lugar. 

 La figura siguiente muestra una topología que contiene una mezcla de patrón dedicado, cliente y nodos de datos en un clúster de Elasticsearch.

![](media/guidance-elasticsearch/general-cluster2.png)

*Un clúster de Elasticsearch con tipos de nodos*

### <a name="costs-and-benefits-of-using-client-nodes"></a>Los costos y las ventajas del uso de nodos de cliente

Cuando una aplicación envía una consulta a un clúster de Elasticsearch, el nodo al que se conecta la aplicación es responsable de dirigir el proceso de consulta. El nodo reenvía la solicitud a cada nodo de datos y recopila los resultados, devuelven la información acumulada a la aplicación. Si trata de una consulta agregaciones y otros cálculos, el nodo al que se conecta la aplicación realiza las operaciones necesarias después de recuperar los datos de cada uno de los otros nodos. Este proceso de dispersión y recopilación puede utilizar recursos de memoria y procesamiento considerable.

Uso de nodos de cliente dedicado para realizar estas tareas permite nodos de datos para concentrarse en la administración y el almacenamiento de datos. El resultado es que muchos escenarios que implican agregaciones y consultas complejas pueden beneficiarse del uso de nodos de cliente dedicado. Sin embargo, el impacto del uso de nodos de cliente dedicado es probable que varían según el escenario, la carga de trabajo y el tamaño de clúster. 

> [AZURE.NOTE] Para obtener más información sobre el proceso de ajuste, consulte [ajustar la agregación de datos y rendimiento de la consulta para Elasticsearch en Azure][] .

### <a name="connecting-to-a-cluster"></a>Conectarse a un clúster

Elasticsearch expone una serie de API de REST de creación de aplicaciones cliente y enviar solicitudes a un clúster. Si va a desarrollar aplicaciones mediante .NET Framework, dos niveles superiores API están disponibles: [Elasticsearch.Net & anidada][].

Si está creando aplicaciones cliente con Java, puede usar la [API de cliente de nodo][] crear a cliente nodos dinámicamente y agregarlos al clúster. Crear cliente nodos dinámicamente es adecuado si el sistema utiliza un pequeño número de conexiones de larga duración. Los nodos de cliente con la API de nodo son siempre con el clúster de enrutamiento asignar (los detalles de las cuales los nodos contienen qué shards) por el nodo maestro. Esta información permite la aplicación de Java conectar directamente con los nodos apropiados al indización o consultar datos, reducir el número de saltos que puede ser necesario cuando se usen otras API.

El costo de este enfoque es la sobrecarga de inscribir el nodo cliente en el clúster. Si un gran número de nodos de cliente aparece y desaparece rápidamente, el impacto de mantenimiento y distribuir el mapa ruta de clúster puede hacerse significativo.

La figura siguiente muestra una configuración que usa un equilibrador de carga enrutar solicitudes a un conjunto de nodos de cliente, aunque se puede utilizar la misma estrategia para conectar directamente con los nodos de datos si no se utilizan los nodos de cliente.

![](media/guidance-elasticsearch/general-clientappinstances.png)

*Instancias de la aplicación cliente conectarse a un clúster de Elasticsearch a través del equilibrador de carga de Azure*

> [AZURE.NOTE]Puede usar el [Equilibrio de carga de Azure][] para exponer el clúster a Internet, o puede usar un [equilibrador de carga interno][] si se encuentran las aplicaciones cliente y el clúster por completo en la red virtual privada misma (VNet).

### <a name="node-discovery"></a>Detección de nodo

Elasticsearch se basa en comunicaciones de punto a punto, para descubrir otros nodos en un clúster es una parte importante del ciclo de vida de un nodo. Detección de nodo permite nuevos nodos de datos que se agregarán dinámicamente a un clúster, lo que permite a su vez el clúster escalar transparente. Además, si un nodo de datos no responde a las solicitudes de comunicaciones de otros nodos, puede decidir un nodo maestro que el nodo de datos no se pudo realizar y realice los pasos necesarios para reasignar la shards que se mantiene a otros nodos de operaciones de datos.

Detección de nodo Elasticsearch se administra mediante un módulo de detección. El módulo de detección es un complemento que puede cambiar para usar un mecanismo de descubrimiento diferente. El módulo de detección predeterminado ([Zen][]) provoca un nodo emitir solicitudes de ping para buscar otros nodos en la misma red. Si responden otros nodos, cotilleos para intercambiar información. Un nodo maestro puede distribuir shards al nuevo nodo (si es un nodo de datos) y equilibrar el clúster. El módulo de detección Zen también controla el proceso de elección de patrón y el protocolo para detectar errores de nodo.

Además, si se están ejecutando los nodos Elasticsearch como Azure máquinas virtuales de Windows (VM), mensajería de multidifusión no es compatible. Por este motivo, debe configurar la detección de Zen para usar la mensajería unidifusión y proporcione una lista de nodos de contacto válidos en el archivo de configuración de elasticsearch.yml.

Si va a hospedar un clúster Elasticsearch dentro de una red virtual Azure, puede especificar que privado IP asignada DHCP dirigido cada uno, VM en el clúster debe permanecer asignado (estática). Puede configurar Zen detección unidifusión mensajería con estas direcciones IP estáticas. Si está utilizando máquinas virtuales con direcciones IP dinámicas, tenga en cuenta que si una máquina virtual se detiene y reinicia podría asignarse una nueva dirección IP que detección más difícil. Para controlar este escenario, puede intercambiar el módulo de detección Zen para el [Complemento de la nube de Azure][]. Este complemento utiliza la API de Azure para implementar el mecanismo de detección, que se basa en información de la suscripción de Azure.

> [AZURE.NOTE]La versión actual del complemento de nube de Azure, debe instalar el certificado de administración para su suscripción de Azure en el almacén de claves de Java en el nodo Elasticsearch y proporcione la ubicación y las credenciales para obtener acceso al almacén de claves en el archivo elasticsearch.yml. Este archivo se almacena en texto sin cifrar, por lo que es muy importante que se asegure de que este archivo solo es accesible con la cuenta que ejecuta el servicio de Elasticsearch. 
> 
> Además, es posible que este enfoque no sea compatible con implementaciones del Administrador de recursos de Azure. Para ello, se recomienda que use direcciones IP para los nodos de patrón y utilizar estos nodos para implementar Zen detección unidifusión mensajería en el clúster. En la configuración siguiente (tomada del archivo elasticsearch.yml para un nodo de datos de ejemplo), las direcciones IP de host maestros nodos en el clúster de referencia:

```yaml
discovery.zen.ping.multicast.enabled: false  
discovery.zen.ping.unicast.hosts: ["10.0.0.10","10.0.0.11","10.0.0.12"]
```

## <a name="general-system-guidelines"></a>Directrices generales del sistema

Puede ejecutar Elasticsearch en una gran variedad de equipos, desde un portátil único a un clúster de servidores de gama alta. Sin embargo, los recursos más en cuanto a memoria, sistemas de energía y rápido discos que están disponible mejor es el rendimiento. Las secciones siguientes resumen los requisitos de hardware y software básicos para ejecutar Elasticsearch.

### <a name="memory-requirements"></a>Requisitos de memoria 

Elasticsearch intenta almacenar datos en memoria de velocidad. Debe tener un servidor de producción aloja un nodo para una empresa normal o tamaño medio comercial implementación en Azure entre 14 y 28GB de memoria RAM (D3 o máquinas virtuales D4). **Distribuir la carga a través de más nodos en lugar de crear los nodos con más memoria** (Experimentación ha mostrado que usa nodos más grandes con más memoria puede provocar tiempos de recuperación ampliada en caso de error). Sin embargo, aunque puede crear clústeres con un gran número de nodos pequeños aumentar disponibilidad y rendimiento, también pasa el esfuerzo implicados en la administración y el mantenimiento de un sistema de este tipo.

**Asignar el 50% de la memoria disponible en un servidor al montón Elasticsearch**. Si está utilizando la variable de entorno ES_HEAP_SIZE Linux conjunto antes de ejecutar Elasticsearch. Como alternativa, si está utilizando Windows o Linux, puede especificar tamaño de la memoria en el `Xmx` y `Xms` parámetros al iniciar Elasticseach. Establecer ambos parámetros en el mismo valor para evitar la máquina Virtual de Java (JVM) cambiar el tamaño del montón en tiempo de ejecución. Sin embargo, **no se asignan más de 30 GB**. Use la memoria restante de la caché de archivos del sistema operativo.

> [AZURE.NOTE]Elasticsearch utiliza la biblioteca de Lucene para crear y administrar índices. Estructuras Lucene usar un formato basado en disco y almacenamiento en caché estas estructuras en la caché del sistema de archivos en gran medida mejorará el rendimiento.

Observe que el tamaño máximo montón óptima para Java en un equipo de 64 bits es justo encima de 30GB. Por encima de este tamaño que Java pasa a utilizar un mecanismo extendido para hacer referencia a objetos del montón, que aumenta los requisitos de memoria para cada objeto y reduce el rendimiento. 

También puede realizar la predeterminada Java recolección (marca simultáneas y limpiar) subcarpetas óptima si el tamaño del montón está por encima de 30 GB. No se recomienda actualmente para cambiar a una recolección diferente como Elasticsearch y Lucene sólo se ha probado con el valor predeterminado.

No exceda la asignación de memoria como intercambio memoria principal en disco afectará gravemente al rendimiento. Si es posible, deshabilitar intercambiar completamente (los detalles dependen del sistema operativo). Si esto no es posible habilitar a la configuración de *mlockall* en el archivo de configuración de Elasticsearch (elasticsearch.yml) como sigue:

```yaml
bootstrap.mlockall: true
```

Esta configuración provoca JVM bloquear la memoria e impide que se retiró el sistema operativo.

### <a name="disk-and-file-system-requirements"></a>Requisitos del sistema de archivo y disco

Use discos de datos de copia de almacenamiento para almacenar shards premium. Discos deben ajustarse para que contenga la cantidad máxima de datos previstas en su shards, aunque es posible agregar más discos más adelante. Puede extender un fragmentar en varios discos de un nodo.

> [AZURE.NOTE]Elasticsearch comprime los datos de los campos almacenados mediante el algoritmo de LZ4 y, a continuación, en Elasticsearch 2.0 y posteriores, puede cambiar el tipo de compresión. Puede cambiar el algoritmo de compresión DEFLATE que usa las utilidades *zip* y *gzip* . Esta técnica de compresión puede ser mayor número de recursos, pero debe considerar el uso de datos de registro de archivado. Este enfoque puede ayudar a reducir el tamaño del índice.

No es fundamental que todos los nodos en un clúster tengan la misma capacidad y diseño de disco. Sin embargo, un nodo con una capacidad de disco muy grande en comparación con otros nodos en un clúster atraer más datos y requiere capacidad de procesamiento mayor para controlar este tipo de datos. Por consiguiente puede ser el nodo "caliente" en comparación con otros nodos y, a su vez, esto puede afectar al rendimiento.

Si es posible, utilice RAID 0 (bandas). Otras formas de RAID que implementan paridad y reflejo son necesarios como Elasticsearch proporciona su propia solución de alta disponibilidad en el formulario de réplicas.

> [AZURE.NOTE]Antes de Elasticsearch 2.0.0, también puede implementar bandas en el nivel de software especificando varios directorios en la configuración de *path.data* . En Elasticsearch 2.0.0, ya no se admite esta forma de bandas. En su lugar, pueden asignarse shards diferentes a distintas rutas de acceso, pero todos los archivos en una sola fragmentar se escriben en la misma ruta de acceso. Si necesita bandas, debe bandas de datos en el nivel de hardware o sistema operativo. 

Maximizar el rendimiento de almacenamiento, cada **VM debería tener una cuenta de almacenamiento dedicado premium**.

La biblioteca de Lucene puede utilizar una gran cantidad de archivos para almacenar los datos de índice y Elasticsearch puede abrir un gran número de sockets para la comunicación entre los nodos y con los clientes. Asegúrese de que el sistema operativo está configurado para admitir un número adecuado de descriptores abrir archivo (hasta 64000 si hay suficiente memoria disponible). Tenga en cuenta que la configuración predeterminada de muchas distribuciones de Linux limita el número de descriptores abrir archivo 1024, que es mucho demasiado pequeño.

Elasticsearch utiliza una combinación de memoria asignada (mmap) i/OS y Java nuevo i/OS (NIO) para optimizar simultáneo acceso a archivos de datos y los índices. Si usa Linux, debe configurar el sistema operativo para asegurarse de que hay suficiente memoria virtual con espacio para áreas de mapa de memoria de 256 K.

> [AZURE.NOTE]Muchos Linux distribuciones utilizan de forma predeterminada el programador (CFQ) de cola completamente una feria al organizar para escribir datos en el disco. Este programador no está optimizado para SSDs. Considere volver a configurar el sistema operativo para usar el programador NOOP o el programador de límite, que son más eficaces para SSDs.

### <a name="cpu-requirements"></a>Requisitos de la CPU

Máquinas virtuales de Azure están disponibles en una gran variedad de configuraciones de CPU auxiliares entre 1 y 32 núcleos. Para un nodo de datos, un buen punto de partida es una máquina virtual estándar de la serie DS y seleccione alguno la DS3 (4 núcleos) o D4 (8 núcleos) SKU. La DS3 también proporciona 14GB de RAM, mientras el DS4 incluye 28GB. 

Las series de GS (para el almacenamiento de premium) y G (para almacenamiento estándar) usan procesadores Xeon E5 V3 que pueden resultar útiles para cargas de trabajo que están muy descarga intensiva, como agregaciones a gran escala. Para obtener la información más reciente, visite [tamaños para máquinas virtuales][].

### <a name="network-requirements"></a>Requisitos de red

Elasticsearch requiere un ancho de banda de red entre 1 y 10 Gbps, dependiendo del tamaño y volatilidad de los clústeres que implementa. Elasticsearch migra shards entre los nodos cuando se agregan más nodos a un clúster. Elasticsearch, se supone que en el momento de la comunicación entre todos los nodos es aproximadamente equivalente y no tiene en cuenta las ubicaciones relativas del shards llevan a cabo en los nodos. Además, la replicación puede provocar i/OS de red importantes entre shards. Para ello, **Evite crear clústeres en nodos que están en diferentes regiones**.

### <a name="software-requirements"></a>Requisitos de software

Puede ejecutar Elasticsearch en Windows o en Linux. El servicio Elasticsearch se implementa como una biblioteca de jar de Java y tiene dependencias en otras bibliotecas de Java que se incluyen en el paquete Elasticsearch. Debe instalar Java 7 (actualización de 55 o posterior) o Java 8 (actualización de 20 o posterior) JVM para ejecutar Elasticsearch.

> [AZURE.NOTE]Distinto de los parámetros de memoria *Xmx* y *Xms* (especificado como opciones de línea de comandos para el motor Elasticsearch: consulte [requisitos de memoria][]) no modifique la configuración de JVM predeterminada. Se ha diseñado Elasticsearch con los valores predeterminados; convertirlos pueden provocar Elasticsearch detuned y realizar mal.

### <a name="deploying-elasticsearch-on-azure"></a>Implementar Elasticsearch en Azure

Aunque no es difícil de implementar una instancia de Elasticsearch, creando un número de nodos, instalar y configurar Elasticsearch en cada uno de ellos puede ser un proceso lento y provocar errores. Si está pensando en ejecución Elasticsearch en máquinas virtuales de Azure, tiene tres opciones que pueden ayudar a reducir las posibilidades de errores.

- Uso de la [plantilla](https://azure.microsoft.com/marketplace/partners/elastic/elasticsearchelasticsearch/) de administrador de recursos de Azure en Azure marketplace. Esta plantilla crea elástica. Permite agregar comerciales mejoras como la pantalla, Marvel, monitor y así sucesivamente.

- Usar el tutorial de Azure [plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) para crear el clúster. Esta plantilla puede crear un clúster basado en Windows Server 2012 o Ubuntu Linux 14.0.4. Permite usar experimentación características como el almacenamiento de archivos de Azure. Esta plantilla se usa para la investigación y tareas de prueba en este documento.

- Uso de secuencias de comandos que pueden automatizar o ejecutarse sin supervisión. Secuencias de comandos que pueden crear e implementar un clúster de Elasticsearch están disponibles en el [repositorio de GitHub][elasticsearch-scripts]

## <a name="cluster-and-node-sizing-and-scalability"></a>Clúster y escalabilidad y tamaño de nodo 

Elasticsearch permite a un número de topologías de implementación, diseñada para admitir distintos requisitos y niveles de escala. Esta sección describe algunas topologías comunes y las consideraciones para implementar clústeres basados en estas topologías.

### <a name="elasticsearch-topologies"></a>Topologías Elasticsearch

La siguiente ilustración muestra un punto de partida para diseñar una topología de Elasticsearch para Azure:

![](media/guidance-elasticsearch/general-startingpoint.png)

*Sugerencias de punto de partida para crear un clúster de Elasticsearch con Azure*

Esta topología contiene seis nodos de datos junto con los tres nodos de cliente y tres maestra (solo se elige el nodo maestro, los otros dos están disponibles para elecciones produjera el patrón seleccionado.) Cada nodo se implementa como una máquina virtual independiente. Aplicaciones web de Azure se dirigen a los nodos de cliente a través de un equilibrador de carga. 

En este ejemplo, todos los nodos y las aplicaciones web se encuentran en la misma red virtual que se aislará a ellos desde el exterior. Si el clúster debe estar disponible externamente (posiblemente como parte de una solución híbrida incorpora nuevos clientes local), a continuación, puede usar el equilibrador de carga de Azure para proporcionar una dirección IP pública, pero debe tomar precauciones adicionales de seguridad para evitar el acceso no autorizado al clúster. 

El "saltar cuadro opcional" es una máquina virtual que solo está disponible para los administradores. Este VM tiene una conexión de red en la red virtual, sino también un saliente conexión de red para permitir que iniciar sesión como administrador de una red externa (debe protegerse este inicio de sesión utilizando una contraseña o el certificado). Un administrador puede iniciar sesión en el cuadro saltar y conecte desde allí directamente a cualquiera de los nodos en el clúster. 

Enfoques alternativos incluyen mediante una VPN de sitio a sitio entre una organización y la red virtual, o el uso de circuitos [ExpressRoute][] para conectarse a la red virtual. Estos mecanismos permitan acceso administrativo a clúster sin exponer el clúster a internet.

Para mantener la disponibilidad de la máquina virtual, los nodos de datos se agrupan en el mismo conjunto de disponibilidad de Azure. Del mismo modo, se mantienen los nodos de cliente en otro conjunto de disponibilidad y los nodos patrón se almacenan en un conjunto de disponibilidad de terceros.

Esta topología es relativamente fácil escalar, simplemente agregar más nodos del tipo apropiado y asegúrese de que estén configurados con el mismo nombre de clúster en el archivo elasticsearch.yml. Los nodos cliente también deben ser agregado al grupo de back-end para el equilibrador de carga de Azure.

**Localizar geo clústeres**

**No separe los nodos en un clúster en todas las regiones que esto puede afectar al rendimiento de la comunicación entre nodos** (consulte [requisitos de red][]). Localizar geo datos cerca de los usuarios de diferentes regiones requieren la creación de varios clústeres. En esta situación, debe tener en cuenta cómo (o incluso si) para sincronizar clústeres. Incluyen posibles soluciones:

[Nodos profesional][] son similares a un nodo cliente, salvo que puede participar en varios clústeres Elasticsearch y verlas todas como un clúster grande. Datos aún se administran localmente por cada clúster (las actualizaciones no se propagarán a través de los límites de clúster), pero todos los datos está visible. Puede consultar un nodo profesional, crear y administrar documentos en cualquier clúster. 

Las restricciones principales son un nodo profesional no se pueden usar para crear un nuevo índice y, a los nombres de índice deben ser únicos en todos los clústeres. Por lo tanto, es importante que considere cómo los índices se llamará al diseñar clústeres vayan a tener acceso desde nodos profesional.

Con este mecanismo, cada clúster puede contener los datos que es más probable que pueden acceder a las aplicaciones cliente local, pero estos clientes aún pueden acceder y modificar datos remotos aunque con posible extendido latencia. La siguiente ilustración muestra un ejemplo de esta topología. Se resalta el nodo profesional en el clúster 1; los demás clústeres también pueden tener nodos profesional aunque no se muestran en el diagrama:

![](media/guidance-elasticsearch/general-tribenode.png)

*Una aplicación de cliente acceso a varios clústeres a través de un nodo profesional*

En este ejemplo, la aplicación cliente se conecta al nodo profesional de clúster 1 (ubicar en la misma región), pero este nodo está configurado para poder acceso clúster 2 y 3 de clúster, que puedan estar situados en diferentes regiones. La aplicación cliente puede enviar solicitudes de recuperar o modifican datos en cualquiera de los clústeres.

> [AZURE.NOTE]Los nodos profesional requieren detección multidifusión para conectarse a clústeres, que pueden presentar un problema de seguridad. Vea la sección [detección de nodo][] para obtener más detalles.

- Implementación de replicación geo entre grupos. En este enfoque se propagarán los cambios realizados en cada clúster en casi en tiempo real para clústeres ubicados en otros centros de datos. Complementos de terceros están disponibles para Elasticsearch que son compatibles con esta funcionalidad, como el [Complemento de PubNub cambios][].

- Usar [Elasticsearch instantánea y restaurar módulo][]. Si los datos es muy movimiento lento y sólo se modificación mediante un único clúster, puede utilizar instantáneas para realizar una copia de los datos periódica y restaurar estas instantáneas de otros clústeres (instantáneas pueden almacenarse en el almacenamiento de blobs de Windows Azure si ha instalado el [Complemento de la nube de Azure][]). Sin embargo, esta solución no funciona bien para cambiar rápidamente los datos o si se pueden cambiar datos en más de un clúster.

**Topologías pequeños**

Topologías a gran escala que incluya clústeres de nodos dedicados de patrón, cliente y datos no sea adecuadas para cada escenario. Si va a crear un sistema de desarrollo o producción pequeños, considere el clúster de nodo 3 que se muestra en la siguiente ilustración.

Aplicaciones cliente se conectan directamente a cualquier nodo de datos disponibles en el clúster. El clúster contiene tres shards etiquetados P1 3 (para permitir crecimiento) plus réplicas etiquetadas R1 R3. Tres nodos permiten Elasticsearch distribuir el shards y réplicas de modo que si un único nodo falla no se perderá ningún dato.

![](media/guidance-elasticsearch/general-threenodecluster.png)

*Un clúster de nodo 3 con 3 shards y réplicas*

Si está ejecutando una instalación de desarrollo en un equipo independiente puede configurar un clúster con un solo nodo que actúa como maestro, cliente y almacenamiento de datos. Como alternativa, puede empezar a varios nodos ejecutarse como un clúster en el mismo equipo iniciando más de una instancia de Elasticsearch. La siguiente ilustración muestra un ejemplo.

![](media/guidance-elasticsearch/general-developmentconfiguration.png)

*Una configuración de desarrollo ejecutando varios nodos de Elasticsearch en el mismo equipo*

Tenga en cuenta que ninguna de estas configuraciones independiente se recomiendan para un entorno de producción ya que pueden causar conflictos a menos que su equipo de desarrollo tiene una gran cantidad de memoria y varios discos rápidos. Además, proporcionan no que garantiza ningún alta disponibilidad. Si se produce un error en el equipo, se pierden todos los nodos.

### <a name="scaling-a-cluster-and-data-nodes"></a>Ajuste de escala de los nodos de clúster y datos

Puede ajustar Elasticsearch en dos dimensiones: verticalmente (más grandes, usando máquinas más eficaces) y horizontalmente (reparte la carga en equipos).

**Ajuste de escala de los nodos de datos Elasticsearch verticalmente**

Si va a hospedar un clúster Elasticsearch usando máquinas virtuales de Azure, cada nodo puede corresponden a una máquina virtual. El límite de escalabilidad vertical de un nodo en gran medida se rige por el SKU de la máquina virtual y las restricciones generales se aplican a suscripciones de Azure y cuentas de almacenamiento individuales. 

La página [suscripción Azure y límites de servicio, cuotas y las restricciones](../azure-subscription-service-limits.md) , describe estos límites en detalle, pero en cuanto se refiere a la creación de un clúster de Elasticsearch, los elementos de la siguiente lista son las más pertinentes. 

- Cada cuenta de almacenamiento está restringido a 20.000 IOPS. Cada máquina virtual de clúster debe aprovechar dedicada (preferentemente premium) cuenta de almacenamiento.

- El número de nodos de datos en una red virtual. Si no está utilizando el Administrador de recursos de Azure, hay un límite de 2048 instancias VM por una red virtual. Mientras Esto resultará suficiente para muchos de los casos, si tiene una configuración muy grande con miles de nodos puede ser una limitación.

- Número de cuentas de almacenamiento por suscripción por región. Puede crear hasta 100 cuentas de almacenamiento por suscripción Azure en cada región. Cuentas de almacenamiento se utilizan para mantener los discos virtuales y cada cuenta de almacenamiento tiene un límite de 500 GB de espacio.

- Número de núcleos por suscripción. El límite predeterminado es 20 núcleos por la suscripción, pero esto puede aumentar hasta 10.000 núcleos solicitando un aumento de límite a través de una incidencia de soporte técnico. 

- La cantidad de memoria por tamaño de memoria virtual. Máquinas virtuales de tamaño menores limitada cantidades de memoria disponible (máquinas D1 tiene 3,5 GB y máquinas D2 tiene 7 GB). Estas máquinas no sea adecuadas para los escenarios que requieren Elasticsearch en caché grandes cantidades de datos para obtener un buen rendimiento (agregación de datos o analizar un gran número de documentos durante la recopilación de datos, por ejemplo).

- El número máximo de discos por tamaño de memoria virtual. Esta restricción puede limitar el tamaño y el rendimiento de un clúster. Menos discos significa que pueden mantener menos datos, y puede que el rendimiento disminuya al tener menos discos disponibles para bandas.

- El número de actualiza dominios / dominios por disponibilidad conjunto a errores. Si crea máquinas virtuales con el Administrador de recursos de Azure, cada conjunto de disponibilidad puede asignar hasta 3 dominios de error y actualización de 20. Esta limitación puede afectar a la resistencia de un clúster de gran tamaño es aplicarle frecuentes actualizaciones graduales.

Además, probablemente no debe considerar usar máquinas virtuales con más de 64GB de memoria. Como se describe en la sección [requisitos de memoria][], no debe asignar más de 30 GB de memoria RAM en cada máquina virtual JVM y permitir que el sistema operativo utilizar la memoria restante para el búfer de i/OS.

Con estas restricciones de opinión, siempre debe distribuir los discos virtuales para las máquinas virtuales en un clúster entre cuentas de almacenamiento para reducir las posibilidades de i/OS limitación. En un clúster muy grande, debe diseñar la infraestructura de lógica y dividir en particiones funcionales independientes. Por ejemplo, debe dividir el clúster en suscripciones, aunque este proceso puede producir más complicaciones debido a la necesidad de conectar redes virtuales.

**Escala un clúster Elasticsearch horizontalmente**

Internamente dentro de Elasticsearch, el límite de escalabilidad horizontal es determinado por el número de shards definido para cada índice. Inicialmente, shards muchos pueden asignarse al mismo nodo en un clúster, pero que el volumen de datos crece adicional se pueden agregar nodos y shards puede distribuirse entre estos nodos. En teoría, solo cuando el número de nodos alcanza el número de shards el sistema dejará de escalar horizontalmente.

Al igual que con la escala vertical, hay algunos aspectos que debe tener en cuenta al analizar la implementación de escala horizontal, incluidos:

- El número máximo de máquinas virtuales que se pueden conectar en una red virtual Azure. Esto puede limitar la escalabilidad horizontal para un clúster muy grande. Puede crear un clúster de nodos que abarca más de una red virtual para evitar este límite, pero este método puede producir rendimiento reducido debido a la falta de ubicación de cada nodo con sus homólogos.

- El número de discos por tamaño de memoria virtual. Otra serie y SKU admite diferentes números de discos conectados.
Además, también puede utilizar el almacenamiento efímero incluido con la máquina virtual para proporcionar una cantidad limitada de almacenamiento de datos más rápida, aunque hay implicaciones resistencia y recuperación que debe considerar (vea [configuración resistencia y recuperación de Elasticsearch en Azure] [ elasticsearch-resilience-recovery] para obtener más información). La serie D serie DS, Dv2 serie y serie GS de VM usan SSDs efímero almacenamiento.

Puede considerar la posibilidad de usar [Conjuntos de escala de máquina Virtual] [ vmss] para iniciar y detener máquinas virtuales como exige indica. Sin embargo, este enfoque puede no ser adecuado para un clúster de Elasticsearch por los siguientes motivos:

- Este enfoque es más adecuado para las VM sin estado. Cada vez que agrega o quita un nodo de un clúster de Elasticsearch shards se reasigna para equilibrar la carga, y este proceso puede generar mucho volúmenes de tráfico de red y disco i/OS y puede afectar gravemente tasas de recopilación de datos. Debe evaluar si esta sobrecarga vale más que el beneficio de transformación adicional y recursos de memoria dinámicamente iniciando máquinas virtuales más no estarán disponibles.

- Inicio VM no ocurre instantáneamente y puede tardar varios minutos antes de máquinas virtuales adicionales están disponibles o se cierran. Escala de esta manera sólo se debe utilizar para controlar los cambios prolongados en petición.

- ¿Después de escalado, realmente necesita tener en cuenta escalar? Quitar una máquina virtual de un clúster de Elasticsearch puede ser un proceso intensivo de recursos que requieren que Elasticsearch recupera la shards y réplicas que se encuentran en dicha máquina virtual y los reproduce en uno o varios de los nodos restantes. Quitar VM varias al mismo tiempo podría comprometa la integridad del clúster, lo dificulta la recuperación. Además, muchas implementaciones Elasticsearch aumentan con el tiempo, pero el tipo de datos es tal que suele no comprimir el volumen. Es posible eliminar manualmente los documentos y documentos también pueden configurarse con un valor de TTL (período de vida) después de que expire y se quitarán, pero en muchos casos, es probable que el espacio anteriormente asignado se volverá rápidamente por los documentos nuevos o modificados. Fragmentación dentro de un índice puede producirse cuando se quitan o se modifiquen documentos, en cuyo caso puede usar la Elasticsearch HTTP [optimizar][] API (Elasticsearch 2.0.0 y versiones anteriores) o la [Fuerza la combinación de][] API (Elasticsearch 2.1.0 y versiones posteriores) para realizar desfragmentación.

### <a name="determining-the-number-of-shards-for-an-index"></a>Determinar el número de shards para un índice

El número de nodos en un clúster puede variar con el tiempo, pero el número de shards en un índice es fijo una vez creado el índice. Para agregar o quitar shards requiere volver a indizar los datos: un proceso de creación de un nuevo índice con el número requerido de shards y, a continuación, copiar los datos del índice antiguo al nuevo (que puede utilizar alias para aislar a los usuarios al hecho de que han sido vuelvan a indexar los datos: vea la [agregación de datos de optimización y rendimiento de la consulta para Elasticsearch en Azure][] para obtener más detalles).
Por lo tanto, es importante determinar el número de shards que es probable que requieren antes de crear el primer índice en el clúster. Puede realizar los siguientes pasos para establecer este número:

- Crear un clúster de nodo único usando la misma configuración de hardware que va a implementar en producción.

- Crear un índice que coincida con la estructura que se va a utilizar en producción. Asigne a este índice una sola fragmentar y sin réplicas.

- Agregar una cantidad específica de datos de producción realista al índice.

- Realizar consultas típicas, agregaciones y otras cargas de trabajo en el índice y medir el rendimiento y tiempo de respuesta.

- Si el rendimiento y tiempo de respuesta está dentro de límites aceptables, a continuación, repita el proceso desde el paso 3 (sumar más datos).

- Cuando aparezcan ha alcanzado la capacidad de las partes (iniciar convertirse en aceptable de rendimiento y los tiempos de respuesta), anote el volumen de documentos.

- Extrapolar desde la capacidad de una sola fragmentar para el número de documentos de producción para calcular el número requerido de shards (se debe incluir un margen de error en estos cálculos, como extrapolación no es una ciencia precisa) previsto.

> [AZURE.NOTE]Recuerde que cada fragmentar se implementa como un índice de Lucene que consume memoria, la potencia de CPU e identificadores de archivo. La shards más tiene, más de estos recursos que requieren.

Además, crear más shards puede aumentar escalabilidad (según sus cargas de trabajo y escenario) y puede aumentar el rendimiento de recopilación de datos, pero puede reducir la eficacia de muchas consultas. De forma predeterminada, una consulta consulta cada fragmentar utilizado por un índice (puede utilizar [la distribución personalizada][] para modificar este comportamiento si sabe qué shards los datos que requiere que se encuentran en). 

Este proceso sólo puede generar una estimación de la cantidad de shards y podría no conocer el volumen de documentos que se espera en producción. En este caso, debe determinar el volumen inicial (como anteriormente) y la tasa de crecimiento previsto. Crear un número adecuado de shards que puede controlar el crecimiento de datos para el período hasta que está dispuesto a indizar la base de datos. 

Otras estrategias usados para escenarios como la administración de eventos y registro incluyen el uso de índices graduales.
Crear un nuevo índice para los datos ingeridos cada día y acceder a este índice por un alias que cambia diariamente para que apunten al índice más reciente. Este enfoque permite a los datos más fácilmente edad salida antiguos (puede eliminar índices que contiene la información que ya no es necesario) y conserva el volumen de datos manejables.

Tenga en cuenta que no tiene el número de nodos para que coincida con el número de shards. Por ejemplo, si crea 50 shards, distribuyan a través de 10 nodos inicialmente y, a continuación, agregue más nodos para cambiar la escala del sistema como el volumen de trabajo aumenta. Evitar la creación de un número muy grande de shards en un pequeño número de nodos (1000 shards extendidos por 2 nodos, por ejemplo). Aunque el sistema teóricamente podría escalar a 1.000 nodos con esta configuración, ejecutando 500 shards en un riesgos de nodo único paralizar el rendimiento del nodo.

> [AZURE.NOTE]Para sistemas de recopilación de datos intensivo, considere la posibilidad de usar un número primo de shards. El algoritmo predeterminado que Elasticsearch se utiliza para redirigir documentos a produce shards más separe incluso en este caso.

### <a name="security"></a>Seguridad

De forma predeterminada, Elasticsearch implementa seguridad mínima y no se proporciona ningún medio de autenticación y la autorización. Estos aspectos requieren configuración del sistema operativo y red subyacentes y el uso de complementos y utilidades de terceros. Por ejemplo, [protección][]y [Protección de la búsqueda][].

> [AZURE.NOTE]Pantalla es un complemento proporcionado por elástica para autenticación de usuario, el cifrado de datos, control de acceso basado en roles, el filtrado de IP y auditoría. Puede ser necesario configurar el sistema operativo subyacente para implementar más medidas de seguridad, como el cifrado de disco.

En un sistema de producción, debe considerar cómo:

- Evitar el acceso no autorizado al clúster.
- Identificar y autenticar a los usuarios.
- Autorizar pueden realizar las operaciones que los usuarios autentican.
- Proteger el clúster de dudosos o perjudiciales operaciones.
- Proteger los datos de acceso no autorizado.
- Cumplir los requisitos normativos para la seguridad de los datos comerciales (si procede).

### <a name="securing-access-to-the-cluster"></a>Proteger el acceso al clúster

Elasticsearch es un servicio de red. Los nodos en un clúster de Elasticsearch escuchan solicitudes de cliente entrantes utilizando HTTP y comunican con ellos mediante un canal TCP. Debe tomar medidas para evitar que los clientes no autorizados o servicios puedan enviar solicitudes a través de HTTP y TCP rutas de acceso. Tenga en cuenta los siguientes elementos. 

- Definir grupos de seguridad de la red para limitar el tráfico de red entrante y saliente para una red virtual o VM a solo los puertos específicos.

- Cambiar los puertos predeterminados usados para el acceso de cliente de web (9200) y el acceso de red mediante programación (9300). Usar un firewall para proteger cada nodo malintencionado del tráfico de Internet.

- Según la ubicación y la conectividad de clientes, coloque el clúster de una subred privada con ningún acceso directo a Internet. Si el clúster se debe exponer fuera de la subred, enrutar todas las solicitudes en un servidor de baluarte o un proxy suficientemente consolidado para proteger el clúster.

Si debe proporcionar acceso directo a nodos, usar un servidor proxy de [nginx](http://nginx.org/en/) configurar y autenticación HTTPS.

> [AZURE.NOTE]Usa un servidor proxy como nginx, también puede restringir el acceso a la funcionalidad. Por ejemplo, puede configurar nginx para permitir únicamente las solicitudes a la \_buscar extremo si necesita impedir que los clientes realizar otras operaciones.

Si necesita seguridad más completa de acceso de red, use los complementos de protección de búsqueda o de la protección.

### <a name="identifying-and-authenticating-users"></a>Identificar y autenticar a los usuarios

Todas las solicitudes realizadas por los clientes al clúster se deben autenticar. Además, debe evitar que los nodos no autorizados unirse al clúster como estos pueden proporcionar una puerta trasera en el sistema que omita la autenticación.

Complementos de Elasticsearch están disponibles que puede llevar a cabo diferentes tipos de autenticación, incluidos:

- **Autenticación básica HTTP**. Nombres de usuario y contraseñas se incluyen en cada solicitud. Todas las solicitudes deben estar cifradas mediante SSL/TLS o un nivel de protección equivalente.

- **Integración de LDAP y Active Directory**. Este enfoque requiere que los clientes se asignan roles en grupos LDAP o AD.

- **Autenticación nativa**. Usa las identidades definidas en el propio clúster Elasticsearch.

- **Autenticación TLS**. Utilice la autenticación de TLS en un clúster para autenticar todos los nodos.

- **Filtrado de IP**. Usar el filtrado de IP para evitar que a los clientes de subredes no autorizados a conectarse y también impide nodos de estas subredes unirse al clúster.

### <a name="authorizing-client-requests"></a>Autorizar solicitudes de cliente

Autorización depende el complemento Elasticsearch utilizado para proporcionar este servicio. Por ejemplo, un complemento que proporciona autenticación básica normalmente proporciona características que definen el nivel de autenticación, mientras que un complemento que utiliza LDAP o AD normalmente se asocia a clientes roles y luego asignar derechos de acceso a esos roles. Cuando se utiliza cualquier complemento, debe tener en cuenta los siguientes puntos:

- ¿Necesita restringir las operaciones que puede realizar un cliente? ¿Por ejemplo, debería poder controlar el estado del clúster, o crear y eliminar índices un cliente?

- ¿El cliente se debe restringir a índices específicos? Esto es útil en un multitenant: sin guión por estilo guía. >> situación donde los inquilinos pueden asignarse a su propio conjunto de índices específico y estos índices deben ser accesibles a otros los inquilinos.

- ¿Debe el cliente, puede leer y escribir datos en un índice? Un cliente puede realizar búsquedas para recuperar datos mediante un índice, pero deben evitar que agregar o eliminar datos de ese índice, por ejemplo.

Actualmente, la mayoría de los complementos de seguridad definir el ámbito de las operaciones en el nivel de índice o clúster y no a los subconjuntos de documentos dentro de índices. Esto es por motivos de eficiencia. Por lo tanto, no es fácil limitar las solicitudes de documentos específicos dentro de un índice único. Si establece este nivel de detalle, guardar documentos en índices independientes y utilice alias que se indiza grupo juntos. 

Por ejemplo, en un sistema de personal, si usuario necesita tener acceso a todos los documentos que contienen información sobre los empleados de departamento X, el usuario B requiere acceso a todos los documentos que contienen información sobre los empleados de departamento Y y usuario C requiere acceso a todos los documentos que contienen información sobre los empleados en ambos departamentos, crear dos índices (para X y departamento Y) y un alias que hace referencia a ambos índices. Conceder acceso de lectura de un usuario en el primer índice, conceda acceso de lectura de usuario B al índice de segundo y conceder acceso de lectura a ambos índices mediante el alias de usuario C. Para obtener más información, consulte [Falsificación índice por usuario con alias][].

### <a name="protecting-the-cluster"></a>Proteger el clúster

El clúster puede serlo indebidamente si no está protegido con cuidado. 

**Deshabilitar el scripting en Elasticsearch de consulta dinámica** consulta como pueden producir vulnerabilidad de seguridad. Usar secuencias de comandos nativas preferentemente secuencias de comandos de consulta; una secuencia de comandos nativa es un complemento de Elasticsearch escritas en Java y compiladas en un archivo JAR.

Consulta dinámica scripting ahora está deshabilitado de forma predeterminada; No volver a habilitarla a menos que tenga una buena razón para hacerlo.

**Evite exponer búsquedas de cadena de consulta a los usuarios** , como este tipo de búsqueda permite a los usuarios realizar consultas intensivo no les afectan. Estas búsquedas gravemente pueden afectar al rendimiento del clúster y pueden representar el sistema abierto a ataques DOS. Además, la búsqueda de cadena de consulta puede exponer información potencialmente confidencial.

**Evitar operaciones de consumo una gran cantidad de memoria** como estos pueden provocar excepciones de memoria agotada de Elasticsearch error en un nodo. Operaciones de ejecución larga recursos intensivo también pueden utilizarse para implementar ataques DOS. Algunos ejemplos:

Evitar las solicitudes de búsqueda que intentan cargar campos muy grandes en la memoria (si se ordena una consulta, las secuencias de comandos o facetas en estos campos), como por ejemplo:

- Busca esa consulta varios índices al mismo tiempo.

- Búsquedas que recuperen un gran número de campos. Estas búsquedas pueden escape memoria por causando una gran cantidad de datos de campo en caché. De forma predeterminada, la caché de datos de campo tiene un tamaño ilimitada, pero puede configurar las propiedades de [indices.fielddata.cache.*](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-fielddata.html) en el archivo de configuración de elasticsearch.yml para limitar los recursos disponibles. También puede configurar el [campo datos disyuntor][] para ayudar a evitar que los datos de un solo campo de memoria agote y el [disyuntor de solicitud][] para detener consultas individuales de monopolizando memoria caché. El coste de estos parámetros es aumenta las posibilidades de algunos errores de consultas o el tiempo de espera.
 
> [AZURE.NOTE]Con [Valores del documento][] puede reducir los requisitos de memoria de índices guardar los datos del campo en el disco en lugar de cargar en memoria. Esto puede ayudar a reducir las posibilidades de agote la memoria en un nodo pero con una reducción de velocidad.

> Elasticsearch siempre se supone que tiene memoria suficiente para realizar su carga de trabajo actual. Si no es el caso, puede bloquear el servicio Elasticsearch. Elasticsearch proporciona extremos que devuelven información sobre el uso de recursos (HTTP [gato API][]) y debe supervisar cuidadosamente esta información.

**Esperando durante mucho tiempo para vaciar un segmento de memoria en curso**. Esto puede escape espacio de búfer en memoria.
Si es necesario, [configure la translog][] reducir los umbrales de datos que se ha vaciado a disco.

**Crear índices con grandes cantidades de metadatos**. Un índice que contiene los documentos con una variación de gran tamaño en los nombres de campo puede consumir una gran cantidad de memoria. Para obtener más información, vea [Asignación de explosión][].
  
La definición de una ejecución larga o una operación intensiva de consulta es muy específico del escenario. La carga de trabajo normalmente esperado por un clúster podría tener un perfil completamente diferente de la carga de trabajo en otra. Determinar qué operaciones son aceptables requiere investigación significativa y pruebas de las aplicaciones.

Proactiva, detectar y detener actividades malintencionadas antes de que causen daño significativo o una pérdida de datos.
Considere la posibilidad de usar un sistema de supervisión y notificación de seguridad que puede detectar rápidamente inusuales modelos de datos acceder y generan alertas cuando, por ejemplo, un error de solicitudes de inicio de sesión de usuario, nodos inesperados unirse o abandonar el clúster u operaciones están tardando más de lo esperado. Herramientas que pueden realizar estas tareas incluyen Elasticearch [Monitor][].

### <a name="protecting-the-data"></a>Proteger los datos

Puede proteger inflight datos mediante SSL/TLS, pero Elasticsearch no proporciona ningún formulario integrada de cifrado de datos para la información que se almacena en disco. Recuerde que esta información se almacena en archivos de disco normales y cualquier usuario con acceso a estos archivos puede peligro de los datos que contienen, por ejemplo copiando su propio clúster. Tenga en cuenta los siguientes puntos:

- Proteger los archivos que utiliza Elasticsearch para mantener los datos. No permitir arbitrario acceso de lectura o escritura identidades que no sea el servicio de Elasticsearch.

- Cifrar los datos contenidos en estos archivos mediante el uso de un sistema de archivos cifrados.

> [AZURE.NOTE]Azure ahora admite el cifrado de disco para máquinas virtuales de Windows y Linux. Para obtener más información, vea [cifrado de disco de Azure para Windows y vista previa de máquinas virtuales de IaaS Linux][].

### <a name="meeting-regulatory-requirements"></a>Requisitos normativos de reunión

Requisitos normativos están destinados principalmente con auditoría operaciones para mantener un historial de eventos, operaciones de y garantizar la privacidad de las siguientes opciones para ayudar a evitar que se supervisadas (reproducir) por una agencia externa. En particular, debe considerar cómo:

- Realizar un seguimiento de todas las solicitudes (correcta o no) y todos los intentos de acceso al sistema.

- Cifrar las comunicaciones realizadas por los clientes en el clúster, así como realizadas por el clúster de comunicaciones de nodo a otro. Debe implementar SSL/TLS para todas las comunicaciones de clúster. Elasticsearch también admite cifrados acoplable si su organización tiene requisitos distintos de los disponibles a través de SSL/TLS.

- Almacenar todos los datos de auditoría de forma segura. El volumen de información de auditoría puede crecer muy rápidamente y debe protegerse de manera sólida para evitar la manipulación de la información de auditoría.

- Forma segura datos de auditoría del archivo.

### <a name="monitoring"></a>Supervisión

Supervisión es importante en el nivel de sistema operativo y en el nivel de Elasticsearch.

Para realizar la supervisión en el nivel de sistema operativo mediante herramientas específicas del sistema operativo. En Windows, esto incluye elementos como Monitor de rendimiento con los contadores de rendimiento adecuado, mientras en Linux que puede usar herramientas como *vmstat*, *iostat*y *superior*. Los elementos claves para supervisar en el nivel de sistema operativo incluyen la utilización de la CPU, volúmenes de i/OS de disco, tiempos de espera de disco i/OS y tráfico de red.
En un clúster de Elasticsearch bien ajustado, uso de CPU por el proceso de Elasticsearch debe ser alto y tiempos de espera de i/OS de disco debe ser mínimo.

En el nivel de software, debe supervisar el rendimiento y respuesta los tiempos de solicitudes, junto con los detalles de solicitudes que no. Elasticsearch proporciona una serie de API que puede usar para examinar el rendimiento de diferentes aspectos de un clúster. Las dos API más importantes son *_cluster de estado* y *estadísticas de _nodes*. *_Cluster/estado* API pueden utilizarse para proporcionar una instantánea del estado general de clúster, así como proporcionar información detallada para cada índice, tal como se muestra en el ejemplo siguiente:

`GET _cluster/health?level=indices`

El resultado de ejemplo que se muestra a continuación se generó mediante esta API:

```json
{
    "cluster_name": "elasticsearch",
    "status": "green",
    "timed_out": false,
    "number_of_nodes": 6,
    "number_of_data_nodes": 3,
    "active_primary_shards": 10,
    "active_shards": 20,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 0,
    "delayed_unassigned_shards": 0,
    "number_of_pending_tasks": 0,
    "number_of_in_flight_fetch": 0,
    "indices": {
        "systwo": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        },
        "sysfour": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        }
    }
}
```

Este clúster contiene dos índices denominados *systwo* y *sysfour*. Estadísticas clave para supervisar para cada índice son el estado, active_shards y unassigned_shards. El estado debería aparecer en verde, el número de active_shards debe reflejar las number_of_shards y number_of_replicas y unassigned_shards deben ser cero. 

Si el estado es rojo, parte del índice falta o está dañado. Se puede comprobar si el valor de *active_shards* es menor que *number_of_shards* - (*number_of_replicas* + 1) y unassigned_shards es distinto de cero. Tenga en cuenta que el estado de amarillo indica que un índice se encuentra en estado de transición, ya sea como resultado de la adición más réplicas o shards que se traslada. Una vez finalizada la transición, debe cambiar el estado en verde. 

Si sigue amarillo para un período prolongado o cambia a color rojo, debe tener un aspecto para ver si se han producido los eventos importantes de i/OS (por ejemplo, un error de red o de disco) en el nivel de sistema operativo.

La \_API de nodos estadísticas emite información detallada sobre cada nodo del clúster:

`GET _nodes/stats`

El resultado generado incluye información sobre cómo los índices se almacenan en cada nodo (incluidos los tamaños y números de documentos), tiempo empleado indización de rendimiento, consultas, buscar, combinar, almacenamiento en caché, sistema operativo e información de proceso, estadísticas sobre JVM (incluyendo basura rendimiento) y grupos de subprocesos. Para obtener más información, vea [Supervisión de nodos individuales][].

Si se presenta una proporción considerable de Elasticsearch solicitudes de mensajes de error de *EsRejectedExecutionException* , Elasticsearch no estar al día con el trabajo que se envían a su manera. En esta situación, debe identificar la botella que está causando Elasticsearch a retrasarse. Tenga en cuenta los siguientes elementos:

- Si es la botella debido a una restricción de recursos, como memoria insuficiente asignada a JVM causando un número excesivo de recolección, considere la posibilidad de asignación de recursos adicionales (en este caso, configurar JVM más memoria, el 50% del almacenamiento disponible en el nodo: vea [requisitos de memoria][]).

- Si el clúster se muestra grande tiempos de espera de i/OS y las estadísticas de combinación recopilan para un índice usando la \_estadísticas de nodo API contienen valores grandes, a continuación, el índice es visible de escritura. Revisar los puntos elevados de [recursos de optimización para operaciones de indización](guidance-elasticsearch-tuning-data-ingestion-performance.md#optimizing-resources-for-indexing-operations) para ajustar el rendimiento de indización.

- Limitar las aplicaciones cliente que están realizando operaciones de recopilación de datos y determinar el efecto que tiene en el rendimiento. Si este enfoque muestra mejora significativa, considere que mantiene el acelerador o escalado por reparte la carga de índices de escritura visible en más nodos.
Para obtener más información, consulte [ajuste del rendimiento de recopilación de datos para Elasticsearch en Azure][].

- Si las estadísticas de búsqueda para un índice indican que las consultas tardan mucho tiempo, a continuación, considere la posibilidad de forma en que se optimización las consultas. Tenga en cuenta que puede usar los valores de *query_time_in_millis* y *query_total* notificados las estadísticas de búsqueda para calcular a una guía básica para la eficacia de las consultas; la ecuación *query_time_in_millis* / *query_total* dará un tiempo promedio para cada consulta.

### <a name="tools-for-monitoring-elasticsearch"></a>Herramientas de supervisión Elasticsearch

Una gran variedad de herramientas están disponibles para realizar la supervisión diaria de Elasticsearch de producción. Estas herramientas suelen usan las APIs Elasticsearch subyacente para recopilar información y presentar los detalles de manera que es más fácil observar que los datos sin formato. Ejemplos comunes incluyen [Cabeza de Elasticsearch][], [Bigdesk][], [Kopf][]y [Marvel][].

Cabeza de Elasticsearch, Bigdesk y Kopf ejecutan como complementos para el software de Elasticsearch. Las versiones más recientes de Marvel pueden ejecutarse de forma independiente, pero requieren [Kibana][] proporcionar una captura de datos y el entorno de hospedaje. La ventaja de usar Marvel con Kibana es que puede implementar supervisión en un entorno independiente del clúster Elasticsearch, lo que le permite explorar problemas con Elasticsearch que no sea posible si ejecutan las herramientas de supervisión como parte del software Elasticsearch. Por ejemplo, si Elasticsearch varias veces se produce un error o se ejecuta muy despacio, herramientas que se ejecutan como complementos Elasticsearch también se verán afectadas, lo que dificulta supervisión y diagnóstico.

En el nivel de sistema operativo, puede usar herramientas como la característica de análisis de registro de [Diagnóstico de Azure en el Portal de Azure][] o [Conjunto de administración de las operaciones de Azure][] para capturar los datos de rendimiento para máquinas virtuales Elasticsearch nodos de hospedaje. Otra consiste en usar [Logstash][] para capturar el rendimiento y registrar los datos, almacenar esta información en un clúster de Elasticsearch independiente (no utilice el mismo clúster que está utilizando o la aplicación) y, a continuación, use Kibana para visualizar los datos. Para obtener más información, vea [Diagnósticos de Microsoft Azure con ELK][].

### <a name="tools-for-testing-elasticsearch-performance"></a>Herramientas para probar el rendimiento de Elasticsearch

Si va a pruebas Elasticsearch o someter un clúster de pruebas de rendimiento, hay disponibles otras herramientas. Estas herramientas están diseñadas para utilizarse en un desarrollo o prueba entorno en lugar de producción.
Un ejemplo utilizados con frecuencia es [JMeter Apache][].

JMeter se utiliza para realizar pruebas comparativas y otras pruebas de carga se describe en documentos relacionados con esta guía. [Crear un entorno de prueba de rendimiento para Elasticsearch en Azure][] describe detalladamente cómo configuró y utiliza JMeter.

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Ajuste del rendimiento de recopilación de datos para Elasticsearch en Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Crear un entorno de prueba para Elasticsearch en Azure de rendimiento]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implementing a JMeter Test Plan for Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Ajustar el rendimiento de consulta para Elasticsearch en Azure y agregación de datos]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Configuring Resilience and Recovery on Elasticsearch on Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Running the Automated Elasticsearch Resiliency Tests]: guidance-elasticsearch-configuring-resilience-and-recovery

[Apache JMeter]: http://jmeter.apache.org/
[Apache Lucene]: https://lucene.apache.org/
[Cifrado de disco Azure para Windows y vista previa de máquinas virtuales de Linux IaaS]: ../azure-security-disk-encryption.md
[Equilibrador de carga de Azure]: ../load-balancer/load-balancer-overview.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[equilibrador de carga interno]:  ../load-balancer/load-balancer-internal-overview.md
[Tamaños de máquinas virtuales]: ../virtual-machines/virtual-machines-linux-sizes.md

[Requisitos de memoria]: #memory-requirements
[Requisitos de red]: #network-requirements
[Detección de nodo]: #node-discovery
[Query Tuning]: #query-tuning

[elasticsearch-scripts]: https://github.com/mspnp/azure-guidance/tree/master/scripts/ps
[A Highly Available Cloud Storage Service with Strong Consistency]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx
[Complemento de nube de Azure]: https://www.elastic.co/blog/azure-cloud-plugin-for-elasticsearch
[Diagnóstico de Azure en el Portal de Azure]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[Serie de administración de operaciones de Azure]: https://www.microsoft.com/server-cloud/operations-management-suite/overview.aspx
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/
[Bigdesk]: http://bigdesk.org/
[API de gato]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/cat.html
[configurar la translog]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-translog.html
[ruta personalizada]: https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-routing-field.html
[Valores de documento]: https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html
[Elasticsearch]: https://www.elastic.co/products/elasticsearch
[Cabeza de Elasticsearch]: https://mobz.github.io/elasticsearch-head/
[Elasticsearch.Net & anidada]: http://nest.azurewebsites.net/
[elasticsearch-resilience-recovery]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Instantánea de Elasticsearch y restaurar módulo]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html
[Índice de falsificación por usuario con alias]: https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html
[Disyuntor de datos de campo]: https://www.elastic.co/guide/en/elasticsearch/reference/current/circuit-breaker.html#fielddata-circuit-breaker
[Forzar la combinación]: https://www.elastic.co/guide/en/elasticsearch/reference/2.1/indices-forcemerge.html
[gossiping]: https://en.wikipedia.org/wiki/Gossip_protocol
[Kibana]: https://www.elastic.co/downloads/kibana
[Kopf]: https://github.com/lmenezes/elasticsearch-kopf
[Logstash]: https://www.elastic.co/products/logstash
[Asignación de expansión]: https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion
[Marvel]: https://www.elastic.co/products/marvel
[Diagnósticos de Microsoft Azure con ELK]: http://aka.ms/AzureDiagnosticsElk
[Supervisar los nodos individuales]: https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_monitoring_individual_nodes
[nginx]: http://nginx.org/en/
[API de cliente de nodo]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/client.html
[Optimizar]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/indices-optimize.html
[Complemento de cambios PubNub]: http://www.pubnub.com/blog/quick-start-realtime-geo-replication-for-elasticsearch/
[Disyuntor de solicitud]: https://www.elastic.co/guide/en/elasticsearch/reference/current/circuit-breaker.html#request-circuit-breaker
[Protector de búsqueda]: https://github.com/floragunncom/search-guard
[Pantalla]: https://www.elastic.co/products/shield
[Transport Client API]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/transport-client.html
[nodos profesional]: https://www.elastic.co/blog/tribe-node
[vmss]: https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/
[Monitor]: https://www.elastic.co/products/watcher
[Zen]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html
