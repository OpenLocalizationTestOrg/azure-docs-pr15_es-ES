#<a name="data-management-and-business-analytics"></a>Administración de datos y análisis de negocio

Administrar y analizar los datos en la nube son tan importante como está en cualquier otra. Para permitir que hacerlo, Azure proporciona una gama de tecnologías para trabajar con datos relacionales y no relacionales. En este artículo se presenta cada una de las opciones. 

##<a name="table-of-contents"></a>Tabla de contenido      

- [Almacenamiento de blobs](#blob)
- [Ejecuta un DBMS en una máquina Virtual](#dbinvm)
- [Base de datos SQL](#sqldb)
    - [Sincronización de datos SQL](#datasync)
    - [Uso de máquinas virtuales de informes de datos de SQL](#datarpt)
- [Almacenamiento de tablas](#tblstor)
- [Hadoop](#hadoop)

## <a name="blob"></a>Almacenamiento de blobs

La palabra "blob" es el nombre corto para "objeto binario" y, a continuación, se describen exactamente lo que un objeto binario es: un conjunto de datos binarios. Aún aunque estén simples, BLOB es muy útil. [Figura 1](#Fig1) ilustra los conceptos básicos de almacenamiento de blobs de Windows Azure.

<a name="Fig1"></a>![Diagrama de BLOB][blobs]
 
**Figura 1: El almacenamiento de blobs de Windows Azure almacena datos binarios - BLOB - en contenedores.**

Para usar BLOB, primero crear una *cuenta de almacenamiento*de Azure. Como parte de esto, especifique el centro de datos de Azure que almacenará los objetos que se crean con esta cuenta. Donde vive, cada blob que crear pertenece a algunos contenedor en su cuenta de almacenamiento. Para obtener acceso a un blob, una aplicación proporciona una dirección URL con el formulario:

http://&lt;*StorageAccount*&gt;.blob.core.windows.net/&lt;*contenedor*&gt;/&lt;*BlobName*&gt;

&lt;*StorageAccount* &gt; es un identificador único que se asigna cuando se crea una nueva cuenta de almacenamiento, mientras &lt; *contenedor* &gt; y &lt; *BlobName* &gt; son los nombres de un contenedor específico y un blob dentro de ese contenedor. 

Azure proporciona dos tipos diferentes de BLOB. Las opciones son:

- *Bloque* blobs, cada una de las cuales puede contener un máximo de 200 gigabytes de datos. Como su nombre sugiere, un blob bloque se subdivide varios bloques. Si se produce un error durante la transferencia de un blob bloque, puede reanudar la retransmisión con el bloque más reciente en lugar de volver a enviar el blob completo. Bloque BLOB es un enfoque bastante general al almacenamiento y hoy son del tipo de blob utilizadas con más frecuencia.

- BLOB de *página* , que puede ser tan grande en un terabyte. BLOB de página está diseñado para acceso aleatorio y, por lo tanto, cada una de ellas se divide en un número de páginas. Una aplicación es gratuita leer y escribir páginas individuales al azar en el blob. En Azure máquinas virtuales de Windows, por ejemplo, máquinas virtuales crear usar blobs de página como almacenamiento persistente para discos de sistemas operativos y discos de datos.

Si elige bloque BLOB o blobs de página, aplicaciones pueden tener acceso a datos blob de varias formas diferentes. Las opciones son las siguientes:

- Directamente a través de un RESTful (es decir, basado en HTTP) protocolo de acceso. Aplicaciones de Azure y aplicaciones externas, incluidas las aplicaciones que se ejecutan en las instalaciones, pueden usar esta opción.
- Uso de la biblioteca de cliente de almacenamiento de Azure, que proporciona una interfaz de más fáciles de desarrollador sobre el protocolo de acceso de blobs de REST sin formato. Una vez más, aplicaciones de Azure y aplicaciones externas pueden acceder a BLOB con esta biblioteca.
- Utilizar unidades de Azure, una opción que permite a una aplicación de Azure tratar un blob de página como una unidad local con un sistema de archivos NTFS. Con la aplicación, blob de página es similar a un sistema de archivos de Windows ordinario acceder a ellas mediante i/OS de archivo estándar. De hecho, lee y escribe se envían a los blobs de página subyacente que implementa la unidad de Azure. 

Para evitar errores de hardware y mejorar la disponibilidad, se duplica cada blob a través de tres equipos en un centro de datos de Azure. Escribir en un blob actualiza todas las copias de tres, de modo que lecturas posteriores no verá resultados incoherentes. También puede especificar que se deben copiar datos de un blob a otro centro de datos de Azure en la misma geo pero al menos 500 millas. Esta copia, denominado *replicación geo*, pasa dentro de unos minutos de una actualización con el blob y es útil para la recuperación.

Datos en BLOB también pueden ofrecerse a través de la *Red de entrega de contenido (CDN)*de Azure. Almacenamiento en caché copias de los datos de blob en docenas de servidores de todo el mundo, la CDN puede acelerar el acceso a la información que se tiene acceso a varias veces. 

Simple que aparecen, BLOB es la mejor elección en muchas situaciones. Almacenar y secuencias de vídeo y audio son ejemplos claros, igual que las copias de seguridad y otros tipos de archivo de datos. Los desarrolladores también pueden usar BLOB para cualquier tipo de datos no estructurados que le guste. Tener una forma sencilla de almacenar y obtener acceso a datos binarios puede ser muy útil.


## <a name="dbinvm"></a>Ejecuta un DBMS en una máquina Virtual

Hoy, muchas aplicaciones se basan en algún tipo de sistema de administración de la base de datos (DBMS). Sistemas relacionales como SQL Server son suelen usar elección, pero no relacionales enfoques, conocidos como tecnologías de *NoSQL* , obtener más populares cada día. Para permitir que las aplicaciones de nube use estas opciones de administración de datos, máquinas virtuales de Azure le permite ejecutar un DBMS (relacional o NoSQL) en una máquina virtual. [Figura 2](#Fig2) muestra el aspecto con SQL Server.

<a name="Fig2"></a>![Diagrama de SQL Server en una máquina Virtual][SQLSvr-vm]
 
**Figura 2: Máquinas virtuales de Windows Azure permite ejecutar un DBMS en una máquina virtual, con persistencia de BLOB.**

A los desarrolladores y los administradores de la base de datos, este escenario se parece mucho a ejecutar el mismo software en su propio centro de datos. En el ejemplo que se muestra aquí, por ejemplo, pueden usarse casi todas las capacidades de SQL Server y tiene acceso administrativo completo al sistema. También tiene la responsabilidad de administrar el servidor de base de datos, por supuesto, como si éste se ejecuta localmente.

Como se muestra en la [figura 2](#Fig2) , las bases de datos parecen estar almacenados en el disco local de la máquina virtual ejecuta el servidor. En segundo plano, sin embargo, cada uno de esos discos se escribe en un blobs de Windows Azure. (Es similar a utilizar una SAN en su propio centro de datos, con un blob mucho actuando como un LUN). Como con cualquier blobs de Windows Azure, los datos que contiene se duplica tres veces dentro de un centro de datos y, si así lo solicita, geo replicar a otro centro de datos en la misma región. También es posible usar opciones como reflejo para mayor confiabilidad de base de datos de SQL Server. 

Otra forma de usar SQL Server en una máquina virtual es crear una aplicación híbrida, donde los datos se encuentra en Azure mientras la lógica de la aplicación ejecuta en local. Por ejemplo, esto podría sentido cuando aplicaciones que se ejecutan en varias ubicaciones o en diferentes dispositivos móviles deben compartir los mismos datos. Para establecer la comunicación entre la lógica de base de datos y local de nube más sencilla, una organización puede usar una red Virtual Azure para crear una conexión de red privada virtual (VPN) entre un centro de datos de Azure y su propio centro de datos local.


## <a name="sqldb"></a>Base de datos SQL

Para muchas personas, ejecuta un DBMS en una máquina virtual es la primera opción que llegue a la cuenta de administración de datos estructurados en la nube. Sin embargo, no se es la única opción, ni es siempre la mejor opción. En algunos casos, la administración de datos utilizando una plataforma como un enfoque de servicio (PaaS) tiene más sentido. Azure proporciona una tecnología de PaaS denominada base de datos de SQL que le permite hacer esto para datos relacionales. [Figura 3](#Fig3) ilustra esta opción. 

<a name="Fig3"></a>![Diagrama de base de datos SQL][SQL-db]
 
**Figura 3: Base de datos SQL proporciona un servicio de almacenamiento relacional PaaS compartido.**

Base de datos SQL no da su propia instancia de SQL Server física de cada cliente. En su lugar, proporciona un servicio de múltiples arrendatario, con un servidor de base de datos SQL lógico de cada cliente. Todos los clientes compartan la capacidad de proceso y almacenamiento que proporciona el servicio. Y, al igual que con el almacenamiento de blobs, se almacena todos los datos de la base de datos SQL en tres equipos independientes dentro de un centro de datos Azure, ofreciendo integrado alta disponibilidad (HA) de las bases de datos.

Una aplicación de la base de datos SQL se parece mucho a SQL Server. Aplicaciones emitir consultas SQL en tablas relacionales, utilice procedimientos almacenados de SQL T y ejecutar transacciones en varias tablas. Y como aplicaciones tener acceso a la base de datos de SQL mediante el protocolo de flujo de datos tabulares (TDS), el mismo protocolo que se utiliza para acceder a SQL Server, pueden trabajar con datos mediante el marco de entidad, ADO.NET, JDBC y otras interfaces de acceso a datos familiar. 

Pero, dado que la base de datos de SQL es un servicio de nube que se ejecutan en los centros de datos de Azure, no necesita para administrar cualquiera de los aspectos físicos del sistema, como el uso de disco. También no debe preocuparse por actualizando el software o controlando otras tareas administrativas bajo nivel. Cada organización cliente aún controla las bases de datos, por supuesto, incluidos los esquemas y los inicios de sesión de usuario, pero muchas de las tareas administrativas más comunes relacionadas se hacen para usted. 

Mientras base de datos SQL mucho es similar a SQL Server para las aplicaciones, no se comporta exactamente igual que un DBMS que se ejecuta en una máquina física o virtual. Porque se ejecuta en hardware compartidos, su rendimiento varía con la carga de hardware todos sus clientes. Esto significa que el rendimiento de, por ejemplo, un procedimiento almacenado en la base de datos SQL puede variar de un día a la siguiente. 

En la actualidad, base de datos SQL le permite crear una base de datos manteniendo 150 gigabytes. Si necesita trabajar con bases de datos mayores, el servicio proporciona una opción denominada *federación*. Para ello, un administrador de la base de datos crea dos o más *miembros de la federación*, cada una de las cuales es una base de datos independiente con su propio esquema. Datos se propagan a través de estos miembros, algo que se denomina *sharding*, con cada miembro asignada una única *clave de federación*. Deben seleccionar una aplicación emite consultas SQL en los datos especificando la clave de federación que identifica al miembro de federación la consulta como destino. Esto permite usar un enfoque relacional tradicional con grandes cantidades de datos. Como siempre, hay ventajas y desventajas; consultas ni las transacciones pueden abarcar a miembros de federación, por ejemplo. Pero, cuando un servicio PaaS relacional es la mejor opción y estas desventajas son aceptables, usando la federación de SQL puede ser una buena solución.

Base de datos SQL puede ser usada por aplicaciones que se ejecutan en Azure o en otra parte, como en el centro de datos local. Esto resulta útil para aplicaciones de nube que necesitan datos relacionales, así como las aplicaciones que pueden beneficiarse de almacenar los datos en la nube local. Una aplicación móvil podría depender de base de datos de SQL para administrar compartidos datos relacionales, por ejemplo, como es posible que una aplicación de inventario que se ejecuta en varios distribuidores todo el mundo.

¿Pensar en base de datos SQL provoca un problema obvio (e importante): cuándo se debe ejecutar SQL Server en una máquina virtual y cuando es una mejor opción de base de datos SQL? Como de costumbre, hay compensaciones y lo qué enfoque es mejor depende de los requisitos. 

Una forma sencilla de pensar es ver base de datos de SQL como para las nuevas aplicaciones, mientras SQL Server en una máquina virtual es la mejor opción cuando está moviendo una existente local aplicación en la nube. También puede ser útil mirar esta decisión de manera más específica, sin embargo. Por ejemplo, la base de datos de SQL es más fácil de usar, ya que apenas hay configuración y administración. Pero con SQL Server en una máquina virtual puede tener un rendimiento más predecible: no es un servicio compartido - y también es compatible con bases de datos no federado mayores de base de datos de SQL. Aún así, base de datos de SQL proporciona integradas de réplica de datos y procesamiento, proporcionando un DBMS de alta disponibilidad con muy poco trabajo. Mientras SQL Server le proporciona mayor control y un amplio conjunto de opciones, es más fácil configurado y mucho menos trabajo para administrar la base de datos de SQL.

Por último, es importante señalar que la base de datos de SQL no es el servicio de datos de PaaS solo disponible en Azure. Los partners de Microsoft proporcionan también otras opciones. Por ejemplo, ClearDB ofrece un MySQL PaaS ofreciendo mientras Cloudant vende una opción de NoSQL. Servicios de datos de PaaS son la solución correcta en muchos casos, y así que este enfoque para la administración de datos es una parte importante de Azure.


### <a name="datasync"></a>Sincronización de datos SQL

Aunque la base de datos SQL mantener tres copias de cada base de datos dentro de un centro de datos de Azure único, no replicar automáticamente datos entre los centros de datos de Azure. En su lugar, proporciona sincronización de datos de SQL, un servicio que puede usar para realizar esta acción. [Figura 4](#Fig4) muestra el aspecto.

<a name="Fig4"></a>![Diagrama de sincronización de datos SQL][SQL-datasync]
 
**Figura 4: Sincronización de datos SQL sincroniza los datos en la base de datos de SQL con datos de otro Azure y centros de datos local.**

Como se muestra en el diagrama, sincronización de datos de SQL puede sincronizar datos entre diferentes ubicaciones. Supongamos que está ejecutando una aplicación en varios centros de datos de Azure, por ejemplo, con los datos almacenados en la base de datos de SQL. Puede usar la sincronización de datos de SQL para mantener los datos sincronizados. Sincronización de datos de SQL también puede sincronizar datos entre un centro de datos de Azure y una instancia de SQL Server que se ejecuta en un centro de datos local. Esto puede ser útil para mantener una copia local de datos usados por aplicaciones locales y una copia de la nube usado por aplicaciones que se ejecutan en Azure. Y aunque no se muestra en la ilustración, sincronización de datos de SQL también puede utilizarse para sincronizar los datos entre la base de datos de SQL y SQL Server que se ejecuta en una máquina virtual en Azure o cualquier otro sitio.

Sincronización puede ser bidireccional y determinar qué datos se sincronizan exactamente y con qué frecuencia se realiza. (No atómica sincronización entre las bases de datos, sin embargo - siempre hay por lo menos algún retraso). Y a pesar de que se utiliza, la configuración de sincronización con la sincronización de datos de SQL es completamente configuración condicionada por el; No hay ningún código para escribir.


### <a name="datarpt"></a>Uso de máquinas virtuales de informes de datos de SQL

Una vez que una base de datos contiene datos, alguien probable que desee crear informes con esos datos. Azure puede ejecutar SQL Server Reporting Services (SSRS) en Azure máquinas virtuales, que es equivalente al ejecutar SQL Server Reporting Services local. A continuación, puede usar SSRS para ejecutar informes sobre los datos almacenados en una base de datos de SQL Azure.  [Figura 5](#Fig5) muestra cómo funciona el proceso.

<a name="Fig5"></a>![Diagrama de informes de SQL][SQL-report]
 
**Figura 5: SQL Server Reporting Services en un máquinas virtuales de Azure en ejecución proporciona servicios de generación de informes de datos de base de datos de SQL. .**

Antes de que un usuario puede ver un informe, alguien define dicho informe aspecto (paso 1). Con SSRS en una máquina virtual, esto puede hacerse mediante uno de dos herramientas: herramientas de datos de SQL Server, parte de SQL Server 2012 o su predecesor, Business Intelligence (BI) Development Studio. Al igual que con SSRS, estas definiciones de informes se expresan en lenguaje de definición de informe (RDL). Después de han creado los archivos RDL para un informe, se cargan en una máquina virtual en la nube (paso 2). Ahora está lista para usar la definición de informe.

A continuación, un usuario de la aplicación accede el informe (paso 3). La aplicación pasa esta solicitud VM SSRS (paso 4), la base de datos SQL u otros orígenes de datos para obtener los datos que necesita de contactos (paso 5). SSRS usa estos datos y los archivos RDL relevantes para representar el informe (paso 6), a continuación, devuelve el informe a la aplicación (paso 7), que muestra al usuario (paso 8).

Incrustar un informe en una aplicación, el escenario se muestra aquí, no es la única opción. También es posible ver informes en el Administrador de informes en la máquina virtual de SharePoint en la máquina virtual o en otras formas. Informes también pueden combinarse, con un informe que contiene un vínculo a otro.

SSRS en una máquina virtual de Azure le ofrece la funcionalidad completa como una solución de informes en la nube. Informes pueden utilizar cualquier origen de datos admitido por SSRS. Aplicaciones y los informes pueden incluir código incrustado o ensamblados para admitir comportamientos personalizados. Representación y ejecución del informe son rápidos como motor y contenido de servidor de informes se ejecutan juntos en el mismo servidor virtual.



## <a name="tblstor"></a>Almacenamiento de tablas

Datos relacionales están útiles en muchos casos, pero no siempre es la mejor elección. Si la aplicación necesita acceso rápido y sencillo a muy grandes cantidades de datos estructurados, por ejemplo, una base de datos relacional no funcionen correctamente. Una tecnología NoSQL es probable que sea una mejor opción.

Almacenamiento de tablas de Azure es un ejemplo de este tipo de enfoque de NoSQL. A pesar de su nombre, el almacenamiento de tablas no admite tablas relacionales estándar. En su lugar, proporciona lo que se conoce como una *clave y valor almacenar*, asociar un conjunto de datos con una clave determinada, a continuación, lo que permite el acceso a una aplicación que los datos al proporcionar la clave. [Figura 6](#Fig6) muestra los conceptos básicos.

<a name="Fig6"></a>![Diagrama de almacenamiento de tablas][SQL-tblstor]
 
**Figura 6: Almacenamiento de tablas de Azure es un almacén de clave y valor que proporciona, simple acceso rápido a grandes cantidades de datos.**

Como blobs, cada tabla está asociado con una cuenta de almacenamiento de Azure. Tablas también se denominan mucho como blobs, con una dirección URL del formulario

http://&lt;*StorageAccount*&gt;.table.core.windows.net/&lt;*nombre de tabla*&gt;

Como se muestra en la ilustración, cada tabla se divide en un número de particiones, cada una de las cuales se pueden almacenar en un equipo distinto. (Esta es una forma de sharding, como ocurre con la federación de SQL.) Aplicaciones de Azure y aplicaciones que se ejecutan en otro sitio pueden acceder a una tabla mediante el Protocolo OData REST o la biblioteca de cliente de almacenamiento de Azure.

Cada partición de una tabla contiene varias *entidades*, que contiene hasta 255 *Propiedades*. Cada propiedad tiene un nombre, un tipo (como binario, Bool, DateTime, entero o cadena) y un valor. A diferencia de almacenamiento relacional, estas tablas no tienen ningún esquema fijo y, así que diferentes entidades de la misma tabla pueden contener propiedades con diferentes tipos. Una entidad podría tener solo una propiedad de cadena que contiene un nombre, por ejemplo, mientras que otra entidad en la misma tabla tiene dos propiedades de entero que contiene un número de ID de cliente y una calificación de crédito.

Para identificar una entidad determinada dentro de una tabla, una aplicación proporciona la clave de la entidad. La tecla tiene dos partes: una *clave de partición* que identifica una partición específica y una *clave de la fila* que identifica una entidad de esa partición. En la [figura 6](#Fig6), por ejemplo, el cliente solicita la entidad con la clave de partición A y la clave de la fila 3 y almacenamiento de tablas devuelve esa entidad, incluidas todas las propiedades que contiene.

Esta estructura le permite tablas a ser grande: una sola tabla puede contener hasta 100 terabytes de datos, y permite el acceso rápido a los datos que contienen. Que también aporta limitaciones, sin embargo. Por ejemplo, no hay ninguna compatibilidad transacciones actualizaciones que abarcan tablas o incluso particiones en una sola tabla. Solo puede agrupar un conjunto de actualizaciones de una tabla en una transacción atómica si todas las entidades implicadas están en la misma partición. Hay también ninguna forma de una tabla de consulta basándose en el valor de sus propiedades ni existe compatibilidad para combinaciones en varias tablas. Y a diferencia de las bases de datos relacionales, tablas no tienen soporte para procedimientos almacenados.

Almacenamiento de tablas de Azure es una buena opción para las aplicaciones que necesitan acceso rápido y económico a grandes cantidades de datos estructurados. Por ejemplo, una aplicación de Internet que almacena información de perfil para una gran cantidad de usuarios puede utilizar tablas. Acceso rápido es importante en esta situación, y la aplicación probablemente no necesita toda la potencia de SQL. ¿Renunciar a esta funcionalidad a ganar la velocidad y tamaño puede a veces sentido y por lo tanto el almacenamiento de tablas es la solución correcta para algunos problemas.


## <a name="hadoop"></a>Hadoop

Se han ido acumulando organizaciones datawarehouses durante décadas. Estas colecciones de información almacenada con mayor frecuencia en tablas relacionales, permitan a los usuarios trabajar con y aprenda de los datos de diferentes maneras. Con SQL Server, por ejemplo, es habitual usar herramientas como SQL Server Analysis Services para hacer esto.

Pero suponga que desea realizar análisis de datos no relacionales. Los datos pueden tardar varios formularios: información de sensores o etiquetas de radio frecuencia ID, archivos de registro en granjas de servidores, datos de secuencia de clics generadas por aplicaciones web, las imágenes de los dispositivos de diagnósticos médicos y mucho más. Estos datos también podrían ser muy grandes, demasiado grande para utilizar eficazmente con un almacén de datos tradicional. Problemas de datos grande como esta, raro unos pocos años, han pasado bastante común.

Para analizar este tipo de datos grandes, nuestro sector en gran medida ha llegado en una única solución: la tecnología de código abierto Hadoop. Hadoop se ejecuta en un clúster de máquinas físicas o virtuales, separándose entre los datos funciona a través de esos equipos y procesamiento en paralelo. Más máquinas Hadoop tiene que use, más rápido puede completar cualquier cosa trabajar está realizando cada tarea.

Este tipo de problema es una solución natural para la nube pública. En lugar de mantener máquinas virtuales de una gran cantidad de servidores que podrían sentarse inactivo gran parte de la hora, ejecute Hadoop en la nube le permite crear local (y pagar) solo cuando los necesite. Aún mejor, más y más grandes datos que desea analizar con Hadoop se crea en la nube, lo que le ahorra el problema de moverlo. Para ayudarle a aprovechar estos sinergias, Microsoft proporciona un servicio de Hadoop en Azure. [Figura 7](#Fig7) muestra los componentes más importantes de este servicio.

<a name="Fig7"></a>![Diagrama de hadoop][hadoop]

**Figura 7: Hadoop en Azure ejecuta trabajos de MapReduce procesan datos en paralelo con varios equipos virtuales.**

Para utilizar Hadoop en Azure, primero solicita esta plataforma de nube para crear un clúster de Hadoop, que especifica el número de máquinas virtuales que necesita. Configurar un clúster de Hadoop es una tarea no sencilla y lo que permite Azure hacer por usted tenga sentido. Cuando haya terminado con el clúster, apaga. No es necesario para pagar recursos de cálculo que no está usando.

Una aplicación de Hadoop, normalmente denominada *trabajo*, usa un modelo de programación que se conoce como *MapReduce*. Como se muestra en la ilustración, la lógica de un trabajo MapReduce se ejecuta simultáneamente en máquinas virtuales de muchos. Mediante el procesamiento de datos en paralelo, Hadoop puede analizar datos mucho más rápido que solo equipo soluciones.

En Azure, los datos un MapReduce trabajo funciona en es normalmente se mantiene el almacenamiento de blobs de Windows. Sin embargo, en Hadoop, MapReduce trabajos pasar datos que se almacenan en el *Sistema de archivos distribuido Hadoop (HDFS)*. HDFS es similar al almacenamiento de blobs de algunas formas; duplica datos en varios servidores físicos, por ejemplo. En lugar de duplicar esta funcionalidad, Hadoop en Azure expone en su lugar el almacenamiento de blobs de Windows a través de la API HDFS, como se muestra en la ilustración. Mientras la lógica en un trabajo MapReduce considera que tiene acceso a archivos HDFS normales, el trabajo en realidad está trabajando con datos que se transmite a él desde BLOB. Y para admitir el caso de que se ejecutan varios trabajos sobre los mismos datos, Hadoop en Azure también permiten copiar los datos de BLOB en HDFS completa ejecutando en las máquinas virtuales. 

MapReduce trabajos se suelen escribir en Java hoy, un método que admita Hadoop en Azure. Microsoft también ha agregado compatibilidad para crear trabajos MapReduce en otros idiomas, incluidos C#, F # y JavaScript. El objetivo es realizar más fácilmente accesibles para un grupo más grande de desarrolladores esta tecnología de datos grande.

Junto con HDFS y MapReduce, Hadoop incluye otras tecnologías que permiten a los usuarios analizar datos sin tener que escribir un trabajo MapReduce a sí mismos. Por ejemplo, cerdo es un lenguaje de alto nivel diseñado para analizar datos grandes, mientras que subárbol ofrece un lenguaje similar a SQL denominado HiveQL. Cerdo y subárbol realmente generan trabajos MapReduce procesan datos HDFS, pero ocultar esta complejidad de sus usuarios. Ambos se proporcionan con Hadoop en Azure.

Microsoft también proporciona un controlador HiveQL para Excel. Usando un complemento de Excel, analistas de negocios pueden crear consultas HiveQL (y, por tanto, MapReduce trabajos) directamente desde Excel, a continuación, procesar y visualizar los resultados con PowerPivot y otras herramientas de Excel. Hadoop en Azure incluye otras tecnologías, como el equipo de aprendizaje bibliotecas Mahout, el sistema de minería de datos de gráfico Pegasus y mucho más.

Análisis de datos grande es importante y lo Hadoop también es importante. Proporcionando Hadoop como un servicio administrado en Azure, junto con vínculos a herramientas familiares como Excel, Microsoft pretende realizar esta tecnología accesible para un amplio conjunto de usuarios.

Con más amplitud, datos de todo tipo están importantes. Esto es por Azure incluye una gama de opciones de análisis de negocio y administración de datos. Cualquier aplicación que está intentando crear, es probable que encontrará algo en esta plataforma de nube funcionará para usted.

[blobs]: ./media/cloud-storage/Data_01_Blobs.png
[SQLSvr-vm]: ./media/cloud-storage/Data_02_SQLSvrVM.png
[SQL-db]: ./media/cloud-storage/Data_03_SQLdb.png
[SQL-datasync]: ./media/cloud-storage/Data_04_SQLDataSync.png
[SQL-report]: ./media/cloud-storage/Data_05_SQLReporting.png
[SQL-tblstor]: ./media/cloud-storage/Data_06_TblStorage.png
[hadoop]: ./media/cloud-storage/Data_07_Hadoop.png
