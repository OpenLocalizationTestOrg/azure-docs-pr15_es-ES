<properties
    pageTitle="Introducción a Microsoft Azure | Microsoft Azure"
    description="¿Nuevo en Microsoft Azure? Obtener una descripción general de los servicios que ofrece con ejemplos de cómo son útiles."
    services=" "
    documentationCenter=".net"
    authors="rboucher"
    manager="carolz"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2015"  
    ms.author="robb"/>

# <a name="introducing-microsoft-azure"></a>Introducción a Microsoft Azure

Microsoft Azure es la plataforma de aplicación de Microsoft para la nube pública.  El objetivo de este artículo es ofrecerle una base para comprender los fundamentos de Azure, incluso si no conoce nada acerca de la nube informática.

**Cómo leer este artículo**

Azure crece todo el tiempo que resulte fácil se sobrecargue.  Comience con los servicios básicos, que aparecen en primer lugar en este artículo y a continuación, vaya a servicios adicionales. No significa que no puede usar los servicios de adicionales por ellas mismas, pero los servicios básicos forman el componente principal de una aplicación que se ejecuta en Azure.

**Enviar comentarios**

Son importante sus comentarios. En este artículo debe dar una descripción general eficaz de Azure. Si no es así, comuníquese con nosotros en la sección comentarios en la parte inferior de la página. Dar algunos detalles en espera ver y cómo mejorar el artículo.  


## <a name="the-components-of-azure"></a>Componentes de Azure

Azure agrupa los servicios en categorías en varios ayudas visuales como la [¿Qué es Azure Infographic](https://azure.microsoft.com/documentation/infographics/azure/) y en el Portal de administración. El Portal de administración es usa para administrar servicios mayoría (aunque no todos) en Azure.

Este artículo utiliza una **organización diferente** a hablar sobre los servicios basados en función similar y para llamar a servicios importantes que forman parte de grandes.  

![Componentes de Azure](./media/fundamentals-introduction-to-azure/AzureComponentsIntroNew780.png)   
 *Ilustración: Azure proporciona acceso a Internet servicios de aplicación que se ejecuta en centros de datos de Azure.*

## <a name="management-portal"></a>Portal de administración
Azure tiene una interfaz de web denominada el [Portal de administración](http://manage.windowsazure.com) que permite a los administradores acceder y administrar más, pero no todas las funciones de Azure.  Normalmente, Microsoft publica el portal de interfaz de usuario más reciente en la versión beta antes de retirar uno más antiguo. El elemento más reciente se denomina ["Portal de vista previa de Azure"](https://portal.azure.com/).

Normalmente es una superposición de tiempo cuando ambos portales están activas. Mientras los servicios principales aparecerán en ambos portales, no toda la funcionalidad esté disponible en ambos. Servicios más recientes pueden mostrarse en los servicios primera y versiones anteriores portal más recientes y funcionalidad sólo puede existir en el elemento anterior.  El mensaje aquí es que si no puede encontrar algo en el portal antiguo, la casilla el más reciente y viceversa.



## <a name="compute"></a>Calcular

Una de las cosas más básicas que es una plataforma de nube es ejecutar aplicaciones. Cada uno de los modelos de cálculo Azure tiene su propio papel.

Puede usar estas tecnologías de forma independiente o combinarlas según sea necesario para crear la base adecuada para la aplicación. El método que elija depende de qué problemas que intenta solucionar.


### <a name="azure-virtual-machines"></a>Máquinas virtuales de Windows Azure

![Azure máquinas virtuales de Windows ROBBCSIART_TEST](./media/fundamentals-introduction-to-azure/mscsiart_VirtualMachinesIntroNew_12345.png)   
*Ilustración: Máquinas virtuales de Windows Azure proporciona un control completo sobre instancias de máquina virtual en la nube.*

La capacidad de crear una máquina virtual a petición, ya sea desde una imagen estándar o desde una proporcionados, puede ser muy útil. Este método, conocido como infraestructura como servicio (IaaS) es lo que proporciona máquinas virtuales de Azure. La figura 2 muestra una combinación de la ejecución de una máquina Virtual (VM) y cómo crear uno nuevo con un disco duro virtual.  

Para crear una máquina virtual, especifique qué disco duro virtual para utilizar y tamaño de la máquina virtual.  A continuación, pagar durante el tiempo que se está ejecutando la máquina virtual. Pagar por minutos y solo mientras se está ejecutando, aunque haya un cargo mínima de almacenamiento para mantener el disco duro virtual disponible. Azure ofrece una galería de cotizaciones VHD (denominada "imágenes") que contienen un sistema operativo de inicio para iniciar desde. Esto incluye opciones de Microsoft y asociados, como Windows Server y Linux, SQL Server, Oracle y muchos más. Es gratuita crear VHD e imágenes y, a continuación, cargarlos usted mismo. Incluso puede cargar VHD que contienen solo los datos y, a continuación, acceder a ellos desde su máquinas virtuales en ejecución.

Independientemente del origen desde el disco duro virtual, puede almacenar los cambios realizados mientras se está ejecutando una máquina virtual de forma continua. La próxima vez que cree una máquina virtual de ese disco duro virtual, cosas recoger donde la dejó. Los VHD que los equipos virtuales se almacenan en blobs de Azure almacenamiento, que hablar más adelante.  Esto significa que obtiene redundancia para asegurarse de que sus máquinas virtuales no desaparecen debido a errores de hardware y disco. También es posible copiar el disco duro virtual modificado fuera de Azure y luego ejecuta localmente.

La aplicación se ejecuta dentro de una o más máquinas virtuales, dependiendo de cómo lo creó antes o decidir crearlo desde cero.

Este enfoque bastante general a la informática en nube puede usarse para solucionar muchos problemas diferentes.

**Escenarios de máquina virtual**

1.  **Desarrollo y prueba** : puede usar para crear una plataforma de desarrollo y pruebas económica que puede cerrar cuando haya terminado de usarlo. También puede crear y ejecutar aplicaciones que usan los idiomas y bibliotecas que le guste. Estas aplicaciones pueden utilizar cualquiera de las opciones de administración de datos que proporciona Azure y, a continuación, también puede usar SQL Server u otro DBMS que se ejecutan en máquinas virtuales de uno o más.
2.  **Mover aplicaciones a Azure (elevación y MAYÚS)** - "Elevación y Mayús" se refiere a mover la aplicación mucho igual que lo haría un elevador para mover un objeto grande.  "Levantar" el disco duro virtual desde el centro de datos local y "Mayús" en Azure y ejecutarlo allí.  Normalmente tendrá que trabajar un poco para quitar dependencias en otros sistemas. Si hay demasiados, puede elegir la opción 3 en su lugar.  
3.  **Ampliar su centro de datos** - usar máquinas virtuales de Azure como una extensión de su centro de datos local, ejecuta SharePoint u otras aplicaciones. Para ello, es posible crear dominios de Windows en la nube mediante la ejecución de Active Directory en máquinas virtuales de Azure. Puede usar una red Virtual Azure (mencionado más adelante) para unir su red local y su red en Azure.



### <a name="web-apps"></a>Aplicaciones Web

![ROBBCSIART_TEST de aplicaciones Web de Azure](./media/fundamentals-introduction-to-azure/mscsiart_AzureWebsitesIntroNew_12345.png)   
 *Ilustración: Azure aplicaciones Web se ejecuta una aplicación de sitio Web en la nube sin tener que administrar el servidor web subyacente.*

Una de las cosas más comunes que los usuarios realizan en la nube es ejecutar aplicaciones web y sitios Web. Máquinas virtuales de Windows Azure lo permite, pero aún plantea la responsabilidad de administración de uno o más máquinas virtuales y los sistemas operativos subyacentes. Las funciones de servicios web de nube pueden hacerlo, pero implementar y mantenimiento aún tiene trabajo administrativo.  ¿Qué sucede si desea que un sitio Web cuando alguien se encarga del trabajo administrativo para usted?

Esto es exactamente lo que proporciona Web Apps. En este modelo de cálculo ofrece un entorno web administrados mediante el portal de administración de Azure, así como las API de. Puede mover una aplicación de sitio Web existente en aplicaciones Web sin cambios, o puede crear uno nuevo directamente en la nube. Una vez que se ejecuta un sitio Web, puede agregar o quitar instancias dinámicamente, confiar en aplicaciones Web de Azure cargar equilibrar las solicitudes entre ellos. Aplicaciones de Azure ofrece una opción compartida, en el sitio Web se ejecuta en una máquina virtual con otros sitios, y una opción estándar que permite a un sitio para que se ejecute en su propia VM. La opción estándar también le permite aumentar el tamaño (informática power) de las instancias si es necesario.

Para el desarrollo, aplicaciones Web es compatible con. NET, PHP, Node.js, Java y Python junto con la base de datos SQL y MySQL (ClearDB, un asociado de Microsoft) para almacenamiento relacional. También proporciona compatibilidad integrada para varias aplicaciones populares, incluidos WordPress, Joomla y Drupal. El objetivo es proporcionar una plataforma de bajo costo, scalable y muy útil para crear aplicaciones web y sitios Web en la nube pública.


**Escenarios de aplicaciones Web**

Aplicaciones de Web está pensado para ser útil para empresas, los desarrolladores y organismos de diseño web. Para empresas, es una solución fácil de administrar, scalable, muy segura y altamente disponible para ejecutar sitios Web de presencia. Cuando necesita configurar un sitio Web, es mejor comenzar con aplicaciones Web de Azure y continúe con los servicios de nube una vez que se necesita una característica que no está disponible. Consulte el final de la sección "Cálculo" para obtener vínculos que pueden ayudarle a elegir entre las opciones.

### <a name="cloud-services"></a>Servicios de nube
![Servicio de nube de Azure](./media/fundamentals-introduction-to-azure/CloudServicesIntroNew.png)   
*Ilustración: Servicios de nube de Azure le ofrece un lugar para ejecutar código personalizado altamente scalable en una plataforma de un entorno de servicio (PaaS)*

Suponga que desea crear una aplicación de nube que puede admitir una gran cantidad de usuarios simultáneas, no requiere mucho administración y nunca deja de funcionar. Es posible que un proveedor de software establecido, por ejemplo, que ha decidido adoptar Software como servicio (SaaS) mediante la creación de una versión de una de las aplicaciones en la nube. O bien, es posible que un inicio de crear una aplicación de consumidor que espera crecerá rápidamente. ¿Si desea crear en Azure, debe utilizar el modelo de ejecución?

Aplicaciones Web de Azure permite crear este tipo de aplicación web, pero hay algunas restricciones. No tiene acceso administrativo, por ejemplo, lo que significa que no se puede instalar software arbitrario. Máquinas virtuales de Windows Azure le ofrece una gran cantidad de flexibilidad, incluyendo el acceso administrativo y por supuesto puede usarlo para crear una aplicación muy scalable, pero tendrá que controlar muchos aspectos de la administración y confiabilidad usted mismo. ¿Qué desea que es una opción que tendrá el control tenga pero también controla la mayor parte del trabajo necesario para la administración y la confiabilidad.

Esto es exactamente lo que se proporcionan servicios de nube de Azure. Esta tecnología está diseñada expresamente para admitir scalable, confiable y aplicaciones de administración de bajo y de un ejemplo de lo que normalmente llama plataforma como servicio (PaaS). Para utilizarla, cree una aplicación mediante la tecnología que elija, como C#, Java, PHP, Python, Node.js o algo más. A continuación, se ejecuta el código en máquinas virtuales (denominados instancias) ejecute una versión de Windows Server.

Pero estas máquinas virtuales son distintas de los que cree con Azure máquinas virtuales de Windows. Para un elemento, Azure propio administra, mediante acciones como instalar revisiones del sistema operativo y automáticamente distribuir nuevas revisiones imágenes. Esto significa que su aplicación no debería mantener el estado en web o trabajo instancias de rol; en su lugar se mantendrán en una de las opciones de administración de datos de Azure descritas en la sección siguiente. Azure también supervisa estos VM, reiniciar cualquier error. Puede configurar los servicios en la nube para crear automáticamente instancias más o menos en respuesta a petición. Esto le permite controlar un mayor uso y, a continuación, escalar para que no está pagando tantos cuando hay menos uso.

Tiene dos funciones que puede elegir al crear una instancia, ambos basados en Windows Server. La principal diferencia entre los dos es que una instancia de una función web ejecuta IIS, mientras que una instancia de una función de trabajo no. Sin embargo, ambos se administran de la misma manera, y es comunes de una aplicación para utilizar ambos. Por ejemplo, una instancia de funciones web puede aceptar solicitudes de usuarios y luego pasarlos a una instancia de funciones de trabajo para el procesamiento. Para ajustar la aplicación hacia arriba o hacia abajo, puede solicitar que Azure crear varias instancias de cada rol o apagar instancias existentes. Y similares a Azure máquinas virtuales de Windows, está cargada solo para el tiempo que se está ejecutando cada instancia de rol web o de trabajo.

**Escenarios de servicios de nube**

Servicios de nube son ideales para admitir escalado masiva cuando necesita más control sobre la plataforma que las aplicaciones Web de Azure pero no es necesario un control sobre el sistema operativo subyacente.

#### <a name="choosing-a-compute-model"></a>Elegir un modelo de cálculo
La página de [aplicaciones Web de Azure, servicios en la nube y comparación de máquinas virtuales de Windows](./app-service-web/choose-web-site-cloud-service-vm.md) proporciona información más detallada sobre cómo elegir un modelo de cálculo.



## <a name="data-management"></a>Administración de datos

Aplicaciones necesitan datos y los distintos tipos de aplicaciones requieren diferentes tipos de datos. Por este motivo, Azure proporciona varias formas diferentes para almacenar y administrar datos. Azure ofrece muchas opciones de almacenamiento, pero todos están diseñados para el almacenamiento de muy resistente.  Con cualquiera de estas opciones, siempre hay 3 copias de los datos que se mantenga sincronizados en un centro de datos Azure--6 Si permite Azure usar geo redundancia para hacer una copia de seguridad al centro de datos de otra al menos 300 millas.     


### <a name="in-virtual-machines"></a>En máquinas virtuales
Ya se ha mencionado la posibilidad de ejecutar SQL Server u otro DBMS en una máquina virtual creada con Azure máquinas virtuales de Windows. Da cuenta que esta opción no está limitada a sistemas relacionales; También está libre para ejecutar NoSQL tecnologías como MongoDB y Casandra. La ejecución de su propio sistema de base de datos es sencillo TI duplica está acostumbrados en nuestro propio centros de datos- pero también requiere el control de la administración de esa DBMS.  En otras opciones de Azure controla más de la administración para usted o todas.

De nuevo, el estado de la máquina Virtual y los discos de datos adicionales, crear o cargar usan almacenamiento de blobs (que hablar más adelante).  


### <a name="azure-sql-database"></a>Base de datos SQL Azure
![Base de datos SQL Azure almacenamiento](./media/fundamentals-introduction-to-azure/StorageAzureSQLDatabaseIntroNew.png)   

*Ilustración: Base de datos de SQL Azure proporciona un servicio de base de datos relacional administradas en la nube.*

Para el almacenamiento relacional, Azure proporciona la característica de base de datos de SQL. No permitir que los nombres por engañarlo. Esto es diferente a una base de datos de SQL típica proporcionados por SQL Server que se ejecutan en Windows Server.  

Anteriormente denominado SQL Azure, base de datos de SQL Azure ofrece todas las características claves de una base de datos relacional sistema de administración, incluyendo transacciones atómicas, acceso a datos simultáneas con varios usuarios con integridad de datos, las consultas de ANSI SQL y un modelo de programación familiar. Como SQL Server, la base de datos SQL se puede acceder con el marco de trabajo de entidad, ADO.NET, JDBC y otros datos familiares tecnologías de acceso a. También es compatible con la mayoría de lenguaje de SQL T, junto con las herramientas de SQL Server como SQL Server Management Studio. Para cualquier usuario familiarizado con SQL Server (u otra base de datos relacional), usando la base de datos SQL es sencillo.

Base de datos de SQL no es solo un DBMS en la nube de TI de un servicio PaaS. Aún controlar los datos y quién puede tener acceso, pero se ocupa base de datos SQL administrativo Simplifique el trabajo, como la administración de la infraestructura de hardware y mantener automáticamente el software de base de datos y el sistema operativo actualizado. Base de datos SQL también proporciona alta disponibilidad, copias de seguridad automáticas, en un momento restauración capacidades y pueden replicar copias por regiones geográficas.  


**Escenarios de base de datos SQL**

Si está creando una aplicación de Azure (con cualquiera de los modelos de cálculo) que necesita almacenamiento relacional, base de datos SQL puede ser una buena opción. Aplicaciones que se ejecutan fuera de la nube también pueden utilizar este servicio, sin embargo, hay muchas otras situaciones. Por ejemplo, los datos almacenados en la base de datos SQL pueden tener acceso desde sistemas de otro cliente, incluidos equipos de sobremesa, portátiles, tabletas y teléfonos. Y dado que proporciona alta disponibilidad incorporada mediante la replicación, usando la base de datos SQL puede ayudar a minimizar el tiempo de inactividad.


### <a name="tables"></a>Tablas
![Tablas de almacenamiento de Azure](./media/fundamentals-introduction-to-azure/StorageTablesIntroNew.png)  

*Ilustración: Tablas de Azure ofrece una manera de NoSQL plana para almacenar los datos.*

Esta característica se denomina diferentes términos como es parte de una característica de mayor denominada "Almacenamiento de Azure". Si ve "tablas", "Tablas Azure" o "almacenamiento tablas", es lo mismo.  

Y no se preocupe por el nombre: esta tecnología no proporciona almacenamiento relacional. De hecho, es un ejemplo de un enfoque NoSQL denominado almacén de clave y valor. Tablas de Azure le permiten una aplicación a almacenar las propiedades de varios tipos, como cadenas, enteros y fechas. Una aplicación, a continuación, puede recuperar un grupo de propiedades al proporcionar una clave única para dicho grupo. Mientras operaciones complejas como no se admiten combinaciones, tablas ofrecen acceso rápido a los datos de tipo. También están muy scalable, con una sola tabla puede contener tanto como un terabyte de datos. Y tablas que coincidan con su simplificar, son normalmente menos costosas de almacenamiento de SQL la base de datos relacional.

**Escenarios para tablas**

Suponga que desea crear una aplicación de Azure que necesita acceder rápidamente a escribió datos, quizás una gran cantidad de ella, pero no es necesario realizar consultas SQL complejas en estos datos. Por ejemplo, suponga que está creando una aplicación de consumidor que necesita para almacenar la información de perfil de cliente para cada usuario. La aplicación se va a ser muy popular, por lo que necesita permitir una gran cantidad de datos, pero que no hará mucho con estos datos más allá de almacenamiento, a continuación, recuperarla en formas muy sencillas. Esto es exactamente el tipo de escenario donde las tablas de Azure tiene sentido.


### <a name="blobs"></a>BLOB
![Almacenamiento de Azure BLOB](./media/fundamentals-introduction-to-azure/StorageBlobsIntroNew.png)    
*Ilustración: Azure BLOB proporciona datos binarios no estructurados.*  

Azure BLOB (nuevo "almacenamiento de blobs de" y acaba "almacenamiento de Blobs" son lo mismo) está diseñado para almacenar datos binarios no estructurados. Como tablas, BLOB proporciona almacenamiento económico y un blob único puede ser tan grande como 1TB (un terabyte). Aplicaciones de Azure también pueden utilizar unidades de Azure, que permiten BLOB proporcionar almacenamiento permanente para un sistema de archivos de Windows en una instancia de Azure. La aplicación ve archivos normales de Windows, pero el contenido se almacena realmente en un blob.

Almacenamiento de blobs utiliza muchas otras características Azure (incluidos máquinas virtuales de Windows), para que pueda administrar por supuesto las cargas de trabajo.

**Escenarios para Blobs**

Una aplicación que almacena los archivos de vídeo, masivos u otra información binario puede usar BLOB para el almacenamiento simple y económico. BLOB también es usado junto con otros servicios como la red de entrega de contenido, que se hablará más adelante.  

### <a name="import--export"></a>Para importar y exportar
![Servicio de exportación de importación de Azure](./media/fundamentals-introduction-to-azure/ImportExportIntroNew.png)  

*Ilustración: Azure importar / exportar proporciona la capacidad de enviar una unidad de disco dura física o de Azure para datos de forma masiva rápido y económico importar o exportar.*  

A veces desea mover una gran cantidad de datos en Azure. Que podría tardar mucho tiempo, tal vez días y usar una gran cantidad de ancho de banda. En estos casos, puede usar Azure importar o exportar, que le permite enviar cifrado Bitlocker 3,5" SATA unidades de disco duro directamente a los centros de datos de Azure, donde Microsoft transferirá los datos en el almacenamiento de blobs para usted.  Una vez completada la carga, Microsoft incluye las unidades.  También puede solicitar que grandes cantidades de datos de almacenamiento de blobs exportarán en unidades de disco duro y se enviarán a usted a través del correo.

**Escenarios para importar o exportar**

- **Migración de datos de gran tamaño** - siempre tiene grandes cantidades de datos (Terabytes) que desee cargar en Azure, el servicio de importación o exportación suele ser más rápido y económico tal vez que transferir en internet. Una vez BLOB los datos, puede procesar en otros formatos como almacenamiento de la tabla o una base de datos de SQL.

- **Recuperación de datos de archivado** - puede usar importar o exportar para tiene Microsoft transferencia grande cantidades de datos almacenan en el almacenamiento de blobs de Windows Azure a un dispositivo de almacenamiento que envíe y, a continuación, se ese dispositivo entregado volver a una ubicación que desee. Dado que esto llevará algún tiempo, no es una buena opción para la recuperación. Es mejor para que no necesita obtener acceso rápido a los datos archivados.


### <a name="file-service"></a>Servicio de archivos
![Servicio de archivo de Azure](./media/fundamentals-introduction-to-azure/FileServiceIntroNew.png)    
*Ilustración: Servicios de archivos de Azure proporciona SMB \\ \\ecursoCompartido rutas de aplicaciones que se ejecutan en la nube.*

En las instalaciones, es frecuente tener grandes cantidades de almacenamiento de archivos accesible a través del protocolo de bloque de mensajes del servidor (SMB) mediante un \\ \\ecursoCompartido formato. Azure ahora tiene un servicio que le permite utilizar este protocolo en la nube. Aplicaciones que se ejecutan en Azure pueden usarlo para compartir archivos entre máquinas virtuales con el sistema de archivos familiar API como ReadFile y escritura. Además, también se pueden acceder a los archivos a la vez a través de una interfaz REST, que le permite acceder a los recursos compartidos locales cuando también configurar una red virtual. Archivos Azure se basa en el servicio de blobs de Windows, por lo que hereda la misma disponibilidad, duración, escalabilidad y redundancia de geo integrado en el almacenamiento de Azure.

**Escenarios en los archivos de Azure**

- **Migrar aplicaciones existentes a la nube** - su sea más fácil migrar aplicaciones a la nube que utilizan recursos compartidos de archivos para compartir datos entre las partes de la aplicación en local. Cada VM se conecta al recurso compartido de archivos y, a continuación, puede leer y escribir como lo haría con un archivo local recurso compartido de archivos.

- **Configuración de la aplicación compartida** - un patrón común para las aplicaciones distribuidas es que los archivos de configuración en una ubicación centralizada donde puede tener acceso desde varias máquinas virtuales. Estos archivos de configuración se pueden almacenados en un recurso compartido de archivos de Azure y leer todas las instancias de aplicación. También se puede administrar la configuración mediante la interfaz REST, que permite el acceso a los archivos de configuración en todo el mundo.

- **Compartir diagnóstico** - puede guardar y compartir archivos de diagnóstico como registros, estadísticas y volcados. Estos archivos disponibles a través de la interfaz el SMB y el resto permite aplicaciones usar una gran variedad de herramientas de análisis de procesamiento y analizar los datos de diagnóstico.

- **Prueba/desarrollo/depuración** - cuando los desarrolladores o los administradores trabajan en máquinas virtuales en la nube, a menudo necesitan un conjunto de herramientas o utilidades. Instalar y distribuir estas utilidades en cada máquina virtual lleva mucho tiempo. Con archivos de Azure, un desarrollador o un administrador puede almacenar sus herramientas Favoritos en un archivo compartido y conectarse a ellos desde cualquier máquina virtual.



## <a name="networking"></a>Redes

Azure se ejecuta hoy en muchos centros de datos extendido por el mundo. Al ejecutar una aplicación o almacenar datos, puede seleccionar uno o varios de estos centros de datos para usar. También puede conectarse a los centros de datos de varias maneras con los siguientes servicios.


### <a name="virtual-network"></a>Red virtual
![VirtualNetwork](./media/fundamentals-introduction-to-azure/VirtualNetworkIntroNew.png)   

*Ilustración: Redes virtuales proporciona una red privada en la nube para diversos servicios pueden hablar entre sí o a los recursos locales si configura una VPN entre local conexión.*  


Una forma útil de usar una nube pública es tratar como una extensión de su propio centro de datos.

Ya puede crear máquinas virtuales a petición, quitarlos (y dejar pagando) cuando ya no se necesitan, puede tener informática power solo cuando lo desee. Y desde máquinas virtuales de Azure le permite crear máquinas virtuales con SharePoint, Active Directory y otro software conocidos en local, este enfoque puede trabajar con las aplicaciones que ya tiene.

Para hacer esto realmente útil, sin embargo, los usuarios deberían poder tratar estas aplicaciones como si se estuvieran ejecutando en su propio centro de datos. Esto es exactamente lo que permite una red Virtual Azure. Usar un dispositivo de puerta de enlace VPN, un administrador puede configurar una red privada virtual (VPN) entre su red local y sus máquinas virtuales que se implementan en una red virtual en Azure. Debido a sus propio v4 las direcciones IP se asigna a la nube máquinas virtuales, que aparezcan en su propia red. Los usuarios de su organización pueden tener acceso a las aplicaciones de esas máquinas virtuales contienen como si se estuvieran ejecutando localmente.

Para obtener más información acerca del diseño y creación de una red virtual que le convenga, consulte [Red Virtual](./virtual-network/virtual-networks-overview.md).

### <a name="express-route"></a>Ruta de Express

![ExpressRoute](./media/fundamentals-introduction-to-azure/ExpressRouteIntroNew.png)   

*Ilustración: Utiliza una red Virtual de Azure ExpressRoute, pero enruta conexiones a través de más rápidas dedicadas líneas en lugar de Internet pública.*  

Si necesita más ancho de banda o la seguridad de una red Virtual de Azure puede proporcionar la conexión, puede buscar en ExpressRoute. En algunos casos, ExpressRoute también puede ahorrarle dinero. Necesitará una red virtual en Azure, pero los vínculos entre Azure y su sitio usa una conexión dedicada que no vaya a través de Internet pública. Para utilizar este servicio, debe tener un acuerdo con un proveedor de servicios de red o un proveedor de servicios de exchange.

Configuración una ExpressRoute conexión requiere más tiempo y planear, por lo que desea iniciar con una VPN de sitio a otro, posteriormente, migrar a una conexión de ExpressRoute.

Para obtener más información sobre ExpressRoute, vea [Información general técnica de ExpressRoute](./expressroute/expressroute-introduction.md).

### <a name="traffic-manager"></a>Administrador de tráfico

![TrafficManager](./media/fundamentals-introduction-to-azure/TrafficManagerIntroNew.png)   

*Ilustración: Administrador de tráfico Azure le permite enrutar el tráfico global en el servicio en función de reglas inteligentes.*

Si está ejecutando la aplicación de Azure en varios centros de datos, puede usar el Administrador de tráfico de Azure para enrutar solicitudes de los usuarios de forma inteligente en varias instancias de la aplicación. También puede redirigir el tráfico a los servicios que no se ejecuta en Azure como son accesibles desde internet.  

Una aplicación de Azure con los usuarios de solo una parte del mundo puede ejecutarse en un único centro de datos de Azure. Una aplicación con usuarios distribuidas en el mundo, sin embargo, es más probable que ejecutar en varios centros de datos, quizás incluso todos. En esta situación segunda, boca un problema: ¿cómo inteligentemente dirigir a los usuarios a instancias de la aplicación? La mayoría de los casos, probablemente desee cada usuario para tener acceso al centro de datos más cercano a ella, ya que es probable que dará le la mejor hora para la respuesta. Pero, ¿qué sucede si esa instancia de la aplicación es sobrecarga o no esté disponible? En este caso, estaría bien dirigir su solicitud automáticamente al centro de datos de otra. Esto es exactamente lo que debe hacerse por el administrador del tráfico de Azure.

El propietario de una aplicación define reglas que especifican cómo se deben dirigir solicitudes de usuarios a centros de datos y luego se basa en el Administrador de tráfico para llevar a cabo estas reglas. Por ejemplo, los usuarios normalmente dirigirá al centro de datos de Azure más cercano, pero que se envían a otro cuando el tiempo de respuesta de su centro de datos predeterminado supera el tiempo de respuesta de otros centros de datos. Para las aplicaciones distribuidas globalmente con muchos usuarios, es útil tener un servicio integrado para tratar problemas como los siguientes.

Administrador de tráfico usa el servicio de nombres de directorio (DNS) redirigir a los usuarios a los extremos de servicio, pero aún más el tráfico no vaya a través del Administrador de tráfico una vez que se conecte. Evita que el Administrador de tráfico ser una botella que puede ralentizar las comunicaciones de servicio.


## <a name="developer-services"></a>Servicios de desarrollador
Azure ofrece una serie de herramientas para ayudar a los desarrolladores y profesionales de TI crear y mantener las aplicaciones en la nube.  

### <a name="azure-sdk"></a>SDK de Azure
En 2008, la primera versión preliminar de Azure compatible solo .NET development. En la actualidad, sin embargo, puede crear aplicaciones de Azure en prácticamente cualquier idioma. Microsoft ofrece actualmente SDK específicos de idioma para .NET, Java, PHP, Node.js, Ruby y Python. También hay un SDK Azure general que proporciona soporte básico para cualquier idioma, como C++.  

Estos SDK ayudan a crear, implementar y administrar aplicaciones de Azure. Están disponibles en [www.microsoftazure.com](https://azure.microsoft.com/downloads/) o GitHub, y se pueden usar con Visual Studio y Eclipse. Azure también ofrece herramientas de línea de comandos que se pueden usar con cualquier entorno de desarrollo o editor, incluyendo las herramientas para implementar aplicaciones en Azure de sistemas Linux y Macintosh.

Junto con su ayuda para generar aplicaciones de Azure, estos SDK también proporcionan bibliotecas de cliente que le ayudarán a crear software que usa los servicios de Azure. Por ejemplo, podría crear una aplicación que lee y escribe Azure BLOB o crear una herramienta que implementa aplicaciones Azure a través de la interfaz de administración de Azure.

### <a name="visual-studio-team-services"></a>Servicios de equipo de Visual Studio

Visual Studio Team Services es un nombre de marketing cubierta por un número de servicios que ayudan a desarrollar aplicaciones en la Azure.

Para evitar confusiones - no proporciona una versión hospedada o basada en Web de Visual Studio. Todavía necesita su copia local de ejecución de Visual Studio. Pero ofrece muchas otras herramientas que pueden ser muy útiles.

Incluye un sistema de control de origen hospedado denominado servicio de Team Foundation, que ofrece control de versiones y seguimiento de elementos de trabajo.  Incluso puede usar Git para control de versiones si prefiere. Y puede variar en el sistema de control de código fuente que usa project. Se puede crear ilimitado grupo privado proyectos accesibles desde cualquier parte del mundo.  

Visual Studio Team Services proporciona un servicio de prueba de carga. Puede ejecutar pruebas de carga creadas en Visual Studio en máquinas virtuales en la nube. Especificar el número total de usuarios que desea cargar prueba con y Visual Studio Team Services determinará automáticamente cuántos agentes son necesarias, control de los equipos virtuales necesarios y ejecutar las pruebas de carga. Si está suscrito a MSDN, obtendrá miles de minutos de usuario gratuitos cada mes de prueba de carga.

Visual Studio Team Services también ofrece soporte técnico para el desarrollo ágil con características como crea la integración continua, los paneles de Kanban y salas de equipo virtual.

**Escenarios de servicios del equipo de Visual Studio**

Visual Studio Team Services es una buena opción para empresas que necesitan colaborar en todo el mundo y no dispone de la infraestructura en lugar de hacerlo. Puede obtener el programa de instalación en minutos, elija un sistema de control de código fuente y empezar a escribir el código y la creación de ese día.  Las herramientas de equipo proporcionan un lugar para junto y colaboración y las herramientas adicionales le proporcionan el análisis es necesario comprobar y ajustar la aplicación rápidamente.

Pero las organizaciones que ya tienen un sistema local pueden probar nuevos proyectos en Visual Studio Team Services para ver si es más eficaz.   

### <a name="application-insights"></a>Información de la aplicación

![Información de la aplicación](./media/fundamentals-introduction-to-azure/ApplicationInsights.png)  

*Ilustración: Rendimiento de monitores perspectivas de aplicación y el uso de la aplicación web o un dispositivo directo.*

Cuando haya publicado su aplicación: si se ejecuta en dispositivos móviles, escritorios o exploradores web - perspectivas de aplicación indica cómo lo hacen y qué hacen los usuarios con él. Le ayudará a mantener un recuento de errores y respuesta lenta, alerta si las cifras cruzada aceptables umbrales y ayudarle a diagnosticar los problemas.

Al desarrollar una nueva característica, plan medir el éxito con los usuarios. Analizando patrones de uso, comprender qué funciona mejor para sus clientes y mejorar su aplicación en cada ciclo de desarrollo.

Aunque se hospeda en Azure, perspectivas de aplicación funciona con una amplia y creciente gama de aplicaciones, en y desactivar Azure. Web J2EE y ASP.NET están cubiertas aplicaciones, así como iOS, Android, OSX y Windows aplicaciones. Telemetría se envía desde un SDK creado con la aplicación, podrá analizar y mostrar en el servicio de información de la aplicación en Azure.

Si desea que el análisis más especializado, exporte la secuencia de telemetría para una base de datos, o para Power BI o cualquier otra herramienta.

**Escenarios de aplicación perspectivas**

Desarrollo de una aplicación. Puede ser una aplicación web o una aplicación de dispositivo o una aplicación de dispositivo con un servidor web.

* Ajustar el rendimiento de la aplicación después de haberla publicado o mientras se encuentra en la prueba de carga.  Aplicación perspectivas agrega telemetría de todas las instancias instaladas y presenta gráficos de tiempos de respuesta, solicitud y recuentos de excepción, tiempos de respuesta de dependencia y otros indicadores de rendimiento. Estos ayudan a ajustar el rendimiento de la aplicación. Puede insertar código para informar de más datos específicos si lo necesita.
* Detectar y diagnosticar problemas en la aplicación activa. Puede obtener alertas por correo electrónico si indicadores de rendimiento entre los umbrales aceptables. Puede investigar sesiones de usuario específico, por ejemplo, para ver la solicitud que se ha producido una excepción.
* Realizar un seguimiento de uso para evaluar el éxito de cada característica nueva. Cuando diseñe un caso de usuario nuevo, plan medir cuánto se utiliza y si los usuarios alcanzar sus objetivos esperados. Perspectivas de aplicación proporciona los datos de uso básico como vistas de la página web y se puede insertar código para realizar un seguimiento de la experiencia del usuario con más detalle.

### <a name="automation"></a>Automatización
Nadie le gusta perder tiempo realizando los mismos procesos manuales y otra vez. Automatización de Azure proporciona una manera de crear, supervisar, administrar e implementar recursos en su entorno de Azure.  

Automatización utiliza "runbooks", que utiliza flujos de trabajo de Windows PowerShell (en comparación con PowerShell solo normal) en segundo plano. Runbooks están diseñados para ejecutarse sin intervención del usuario. Flujos de trabajo de PowerShell permite el estado de un script de guardarse en puntos a lo largo de la forma. Si se produce un error, no tiene que iniciar una secuencia de comandos desde el principio. Reinícielo en el último punto de control. Esto le ahorra mucho trabajo que se trata de realizar la secuencia de comandos controlar todos los posibles errores.

**Escenarios de automatización**

Automatización de Azure es una buena opción para automatizar las tareas manuales, larga duración, errores y repiten con frecuencia en Azure.


### <a name="api-management"></a>Administración de la API

Creación y publicación de Interfaces de programador de aplicaciones (API) en internet es una manera de proporcionar servicios a las aplicaciones. Si estos servicios son puede volver a vender (por ejemplo, los datos del tiempo), una organización puede permitir que otros proveedores de terceros tener acceso a los mismos servicios por una cuota. Medida que escala a más partners, normalmente deberá optimizar y controlar el acceso.  Es podrán que algunos socios incluso tenga los datos en un formato diferente.

Administración de Azure API facilita organizaciones publicar API para socios, empleados y los desarrolladores de terceros a escala y de forma segura. Proporciona un extremo API diferente y actúa como un proxy para llamar al extremo real proporcionando servicios como almacenamiento en caché, transformación, regulación, control de acceso y agregación de análisis.

**Escenarios de administración de la API**

Supongamos que su organización tiene un conjunto de dispositivos que se tienen llamar a un servicio central para obtener datos, por ejemplo, una empresa de envío que tiene dispositivos de cada unidad móvil de viaje.  Por supuesto la empresa desea configurar un sistema para realizar un seguimiento de su propio camiones para poder predecir y actualizar los tiempos de entrega confiable. Se puede saber cuántas camiones tiene y planear adecuadamente.  Cada unidad móvil tendrá un dispositivo que vuelve a una ubicación central con su posicionamiento y la velocidad de datos y tal vez más.

Un cliente de la empresa de envío probablemente también beneficiarse de obtener datos de esta ubicación.  El cliente puede utilizar para saber qué productos tienen viajar, donde salen, cuánto se pagando determinadas itinerario (si se combina con lo pagan para enviar). Si la empresa de envío agrega este datos ya, muchos clientes pueden pagar por él.  Pero, a continuación, necesita proporciona una manera de ofrecer a los clientes los datos de la empresa de envío. Una vez que proporcionan acceso a los clientes, no pueden tener control sobre la frecuencia con la que es consultar los datos. Tienen proporcionar reglas sobre quién puede tener acceso a los datos. Todas estas reglas tendría que ser integrado en su API externa. Esto es donde puede ayudar a API administración.  


## <a name="identity-and-access"></a>Acceso e identidad

Trabajar con la identidad es parte de la mayoría de las aplicaciones. Saber quién es un usuario le permite una aplicación decida cómo debe interactuar con dicho usuario. Azure proporciona servicios para ayudar a realizar un seguimiento de identidad, así como integrar con stores identidad puede que esté utilizando.


### <a name="active-directory"></a>Active Directory

Al igual que la mayoría de los servicios de directorio, Azure Active Directory almacena información acerca de los usuarios y las organizaciones que pertenecen. Permite a los usuarios iniciar sesión y luego suministra tokens que pueden presentar a aplicaciones para demostrar su identidad. También permite la sincronización de la información de usuario con Windows Server Active Directory ejecuta de forma local en su red local. Si los mecanismos y los formatos de datos utilizados por Azure Active Directory no idénticos con los que se utilizan en Active Directory de Windows Server, las funciones que realiza son muy similares.

Es importante comprender que Azure Active Directory está diseñado principalmente para su uso con las aplicaciones de la nube. Puede usar aplicaciones que se ejecutan en Azure, por ejemplo, o en otras plataformas de nube. También se usa con las aplicaciones de nube de Microsoft, como las de Office 365. Sin embargo, si desea ampliar su centro de datos en la nube con máquinas virtuales de Azure y Azure una red Virtual, Azure Active Directory no la mejor elección. En su lugar, deseará ejecutar Active Directory de Windows Server en máquinas virtuales.

Para permitir que las aplicaciones de acceso a la información que contiene, Azure Active Directory proporciona API de REST denominado Azure Active Directory Graph. Esta API permite aplicaciones que se ejecutan en los objetos de directorio de plataforma access y las relaciones entre ellas.  Por ejemplo, una aplicación autorizada puede utilizar esta API para obtener información sobre un usuario, los grupos que pertenece y otra información. Aplicaciones también pueden ver las relaciones entre los usuarios sus social graph-dejar que ellos trabajar de manera más inteligente con las conexiones interpersonales.

Otra funcionalidad de este servicio, Azure Active el Control de acceso de directorio, facilita una aplicación para aceptar la información de identidad de Facebook, Google, Windows Live ID y otros proveedores de identidades populares. En lugar de que la aplicación para comprender los diversos formatos y protocolos usados por cada uno de estos proveedores, Control de acceso se traduce todas ellas en un solo formato común. También le permite una aplicación aceptar inicios de sesión de uno o más dominios de Active Directory. Por ejemplo, un proveedor que proporciona una aplicación de SaaS puede usar el Control de acceso de Azure Active Directory para dar a los usuarios en cada uno de su clientes inicio de sesión único para la aplicación.

Servicios de directorio son un respaldo principales de la informática en local. No debería ser sorprendente que también son importantes en la nube.

### <a name="multi-factor-authentication"></a>Autenticación multifactor
![Azure autenticación multifactor](./media/fundamentals-introduction-to-azure/MFAIntroNew.png)   

*Ilustración: Autenticación multifactor proporciona la funcionalidad de la aplicación para comprobar más de un formulario de identificación*

Seguridad siempre es importante. Autenticación multifactor (AMF) le ayuda a garantizar que solo los usuarios acceso a sus cuentas. AMF (también conocido como dos factores autenticación o "2FA") requiere que los usuarios proporcionan dos de estos tres métodos de comprobación de identidad para inicios de sesión de usuario y las transacciones.

- Algo sabe (normalmente una contraseña)
- Algo que tiene (un dispositivo de confianza no fácilmente duplicado, como un teléfono)
- Algo que va (biométrica)

Así que cuando un usuario inicia sesión, puede pedirles que también verificar su identidad con una aplicación móvil, una llamada de teléfono o un mensaje de texto en combinación con su contraseña. De forma predeterminada, Azure Active Directory admite el uso de contraseñas como único método de autenticación para inicios de sesión de usuario. Puede usar AMF junto con Azure AD o directorios y aplicaciones personalizadas mediante el SDK MFA. También puede usar junto con aplicaciones locales mediante el uso de servidor de autenticación multifactor.

**Escenarios de AMF**

Protección de inicio de sesión de cuentas importantes, como inicios de sesión de banco y acceso de código fuente donde entrada no autorizado podría tener una propiedad alta de finanzas o intelectual coste.   






## <a name="mobile"></a>Móvil

Si va a crear una aplicación para un dispositivo móvil, puede ayudar a Azure almacenar datos en la nube, autenticar a los usuarios y enviar notificaciones de inserción sin tener que escribir una gran cantidad de código personalizado.

Aunque puede generar por supuesto el back-end para una aplicación móvil con máquinas virtuales de Windows, servicios de nube o aplicaciones Web, puede pasar mucho menos tiempo escribir los componentes del servicio subyacente mediante servicios de Azure.


### <a name="mobile-apps"></a>Aplicaciones móviles

![Aplicaciones móviles](./media/fundamentals-introduction-to-azure/MobileServicesIntroNew.png)

*Ilustración: Aplicaciones móviles proporciona funcionalidad comúnmente requerida con las aplicaciones de la interfaz con dispositivos móviles.*

Aplicaciones de Azure Mobile ofrece muchas funciones útiles que le pueden ahorrar tiempo al crear un back-end para una aplicación móvil. Permite hacer simple de aprovisionamiento y administración de datos almacenados en una base de datos de SQL. Con código de servidor puede usar fácilmente las opciones de almacenamiento de datos adicionales como almacenamiento de blobs o MongoDB. Aplicaciones móviles proporciona soporte técnico para las notificaciones, aunque en algunos casos puede utilizar notificación Hubs tal como se describe a continuación.  El servicio también tiene un API de REST que puede llamar la aplicación móvil para trabajar. Aplicaciones móviles también proporciona la capacidad para autenticar a los usuarios a través de Microsoft y Active Directory, así como otros proveedores de identidades conocidos como Facebook, Twitter y Google.   


Puede utilizar otros servicios de Azure como Bus de servicio y las funciones de trabajo y conectarse a sistemas locales. Incluso puede consumir 3ª complementos de terceros desde la tienda de Azure (por ejemplo, SendGrid para el correo electrónico) para proporcionar funcionalidad adicional.


Bibliotecas de cliente nativo para Android, iOS, HTML o JavaScript, Windows Phone y la tienda Windows que sea más fácil desarrollar aplicaciones en todas las principales plataformas de dispositivos móviles. API de REST le permite usar la funcionalidad de datos y la autenticación de servicios móviles con aplicaciones en distintas plataformas. Varias aplicaciones cliente puede crear copias de un único servicio móvil para que puedan proporcionar una experiencia de usuario coherente en todos los dispositivos.

Dado que Azure admite escala masiva ya, puede controlar el tráfico como la aplicación sea más popular.  Supervisión y el registro se admiten para ayudar a solucionar problemas y administrar el rendimiento.


### <a name="notification-hubs"></a>Notificación Hubs

![NotificationHubs](./media/fundamentals-introduction-to-azure/NotificationHubsIntroNew.png)  

*Ilustración: Notificación Hubs proporciona funcionalidad comúnmente requerida con las aplicaciones de la interfaz con dispositivos móviles.*

Aunque puede escribir código para realizar las notificaciones en las aplicaciones móviles de Azure, Hubs de notificación está optimizado para difusión millones de notificaciones de inserción altamente personalizadas en cuestión de minutos.  No tiene que preocuparse por detalles como operador móvil o el fabricante del dispositivo. Se pueden destinar individual o millones de usuarios con una sola llamada API.

Notificación Hubs está diseñado para funcionar con cualquier back-end. Puede usar las aplicaciones móviles de Azure, un back-end personalizado en la nube que se ejecutan en cualquier proveedor o un servidor local.

**Escenarios de concentrador de notificación** Si está escribiendo un juego móvil donde reproductores tomó activa, debe notificar a jugador 2 que jugador 1 terminado su activar. Si eso es todo lo que debe hacer, solo puede usar las aplicaciones de Mobile. Pero si tenía 100000 usuarios jugar a un juego y que desea enviar una vez confidencial oferta gratuita a todos los usuarios, Hubs de notificación es la mejor opción.

Puede enviar noticias, material deportivo eventos y notificaciones de anuncio de producto para millones de usuarios con baja latencia. Las empresas pueden notificar a sus empleados sobre comunicaciones confidenciales nuevo de tiempo, como clientes potenciales, por lo que no tienen empleados constantemente comprobar correo electrónico u otras aplicaciones para mantenerse informado. También puede enviar un-: contraseñas necesario para la autenticación multifactor.




## <a name="back-up"></a>Copia de seguridad
Cada empresa necesita hacer copia de seguridad y restaurar los datos. Puede usar Azure hacer copia de seguridad y restauración de la aplicación ya sea en la nube o local. Azure ofrece diferentes opciones para ayudar a según el tipo de copia de seguridad.

### <a name="site-recovery"></a>Recuperación de sitio

Recuperación de sitio de Azure (anteriormente Administrador de recuperación de Hyper-V) puede ayudar a proteger aplicaciones importantes coordinar la replicación y la recuperación de sitios. Recuperación de sitio proporciona la capacidad para proteger aplicaciones basadas en Hyper-v, VMWare o SAN a su propio sitio secundario, al sitio del anfitrión o a Azure y evitar los gastos y la complejidad de crear y administrar su propios ubicación secundaria. Azure cifra los datos y comunicaciones y tiene la opción de habilitar el cifrado de datos en descanso demasiado.

Continuamente supervisa el estado de los servicios y ayudan a automatizar la recuperación ordenada de servicios en caso de una interrupción en el sitio en el centro de datos principal. Máquinas virtuales se puede poner en modo organizado para ayudar a restaurar el servicio rápidamente, incluso para cargas de trabajo de varios niveles complejos.

Recuperación de sitio funciona con las tecnologías existentes como réplica Hyper-V, System Center y SQL Server siempre en. Consulte [Introducción a la recuperación de sitio de Azure](site-recovery/site-recovery-overview.md) para obtener más detalles.

### <a name="azure-backup"></a>Copia de seguridad de Azure
![Copia de seguridad de Azure](./media/fundamentals-introduction-to-azure/AzureBackupIntroNew.png)  

*Ilustración: Azure copia datos de servidores de Windows local en la nube.*  

Azure copia datos desde servidores locales que ejecutan Windows Server en la nube. Puede administrar las copias de seguridad directamente desde las herramientas de copia de seguridad de Windows Server 2012, Windows Server 2012 Essentials o System Center 2012 - Administrador de protección de datos. Como alternativa, puede utilizar a un agente de copia de seguridad especializado.

Datos es más seguros porque las copias de seguridad se cifran antes de la transmisión y almacenan cifrado en Azure y protegido por un certificado que cargar. El servicio usa la misma protección de datos redundantes y altamente disponibles que se encuentra en el almacenamiento de Azure.  Puede realizar una copia de archivos y carpetas de forma regular o inmediatamente, ejecuta completos o incrementales. Después de que se realizan copias de seguridad de los datos en la nube, los usuarios autorizados pueden recuperar fácilmente las copias de seguridad a cualquier servidor. También ofrece las directivas de retención de datos configurable, compresión de datos y datos de transferencia límite para que pueda administrar el costo para almacenar y transferir datos.

**Escenarios de copia de seguridad de Azure**

Si usted ya está usando Windows Server o centro de sistema, copia de seguridad de Azure es una solución natural para la copia de seguridad de su sistema de archivos de servidores, máquinas virtuales y las bases de datos de SQL Server.  Funciona con archivos cifrados, dispersión y comprimidos. Existen algunas limitaciones, por lo que debería [comprobar los requisitos previos de la copia de seguridad de Azure](http://technet.microsoft.com/library/dn296608.aspx) en primer lugar.



## <a name="messaging-and-integration"></a>Integración y mensajería

Independientemente de lo que está haciendo, código con frecuencia necesita interactuar con otro tipo de código.  En algunos casos, todo lo que necesitamos es mensajería en cola básica. En los demás casos, se requieren interacciones más complejas. Azure proporciona varias maneras diferentes para resolver estos problemas. Figura 5 muestra las opciones.

### <a name="queues"></a>Colas
![Retransmisión de Bus de servicio de Azure](./media/fundamentals-introduction-to-azure/QueuesIntroNew.png)

*Ilustración: Colas permiten un acoplamiento entre partes de una aplicación flexible y facilitan el ajuste de escala.*  

Cola es una idea sencilla: una aplicación coloca un mensaje de una cola y finalmente se lee el mensaje por otra aplicación. Si su aplicación necesita simplemente este servicio sencillo, colas de Azure posible la mejor opción.

Debido a la manera en que la Azure aumentó transcurso del tiempo, colas de almacenamiento de Azure y Bus de servicio ofrecen servicios de cola similares. En el documento bastante técnico [colas de Azure y colas de Bus de servicio - en comparación y Contrasted](http://msdn.microsoft.com/library/azure/hh767287.aspx)se tratan los motivos por qué ¿desea usar una encima de la otra.  En muchos casos, cualquiera funcionará.

**Escenarios de cola**

Hoy es un uso común de colas permitir a una instancia de funciones web comunicarse con una instancia de funciones de trabajo en la misma aplicación de servicios en la nube.

Por ejemplo, suponga que crea una aplicación de Azure para compartir vídeo. La aplicación consta de código PHP que se ejecuta en una función de web que permite a los usuarios cargar y ver vídeos, junto con una función de trabajador implementado en C# que traduce vídeo cargado en varios formatos.

Cuando una instancia de funciones web Obtiene un nuevo vídeo de un usuario, puede almacenar el vídeo en un blob, a continuación, enviar un mensaje a un rol de trabajo a través de una cola que indica dónde encontrar este vídeo nuevo. Una función de trabajo instancia-it no importa qué será de uno, a continuación, lea el mensaje de la cola y llevar a cabo la traducción requerido vídeo en segundo plano.

Estructurar una aplicación de este modo permite procesamiento asincrónico, y también facilita la aplicación a escala, ya que el número de instancias de la función de web y trabajador rol puede variar de forma independiente. También puede usar el tamaño de la cola como un desencadenador para ajustar el número de funciones de trabajo hacia arriba y hacia abajo. Muy alto, y agregar más funciones. Cuando se obtiene inferior, puede reducir el número de la ejecución de funciones para ahorrar dinero.  

Puede utilizar esta misma trama entre diferentes partes de la aplicación incluso si no utilizan funciones web y trabajador.  Le permite ajustar el tamaño de los elementos de cada lado de la cola hacia arriba y hacia abajo como demanda y requiere tiempo de procesamiento.


### <a name="service-bus"></a>Bus de servicio
Si se ejecutan en la nube, en el centro de datos, en un dispositivo móvil o en otro lugar, aplicaciones necesitan interactuar. El objetivo de Bus de servicio de Azure es permiten a aplicaciones que se ejecutan casi cualquier intercambio de datos.

Además de las colas (uno a uno) se ha descrito anteriormente, Bus de servicio proporciona a otros métodos de comunicación.

#### <a name="service-bus-relay"></a>Retransmisión de Bus de servicio
![Retransmisión de Bus de servicio de Azure](./media/fundamentals-introduction-to-azure/ServiceBusRelayIntroNew.png)

*Ilustración: Retransmisión de Bus de servicio permite la comunicación entre aplicaciones en caras diferentes de un firewall.*

Bus de servicio permite la comunicación directa a través de su servicio de retransmisión, que proporciona una manera segura interactuar a través del firewall. Transmisiones de Bus de servicio permiten a aplicaciones para comunicarse mediante el intercambio de mensajes a través de un extremo hospedada en la nube, en lugar de localmente.

**Escenarios de retransmisión de Bus de servicio**

Aplicaciones que se comunican a través de Bus de servicio posible Azure aplicaciones o software que se ejecuta en otra plataforma de nube. También pueden ser aplicaciones que se ejecutan fuera de la nube, sin embargo. Por ejemplo, piense en una línea aérea que implementa servicios de reserva en equipos dentro de su propio centro de datos. La línea aérea debe exponen estos servicios a muchos clientes, incluyendo quioscos de verificación en aeropuertos, terminales de agente de reserva e incluso podría teléfonos de los clientes. Podría usar Bus de servicio para ello, crear interacciones acoplamiento flexible entre las distintas aplicaciones.

#### <a name="service-bus-topics-and-subscriptions"></a>Las suscripciones y temas de Bus de servicio
![Temas de Bus de servicio de Azure](./media/fundamentals-introduction-to-azure/ServiceBusTopicsSubsIntroNew.png)   
 *Ilustración: Temas de Bus de servicio permite varias aplicaciones publicar mensajes y otras aplicaciones para suscribirse para recibir mensajes que cumplen un criterio específico.*

Bus de servicio proporciona un mecanismo de publicación y suscripción denominado temas y suscripciones. Con publish-subscribe, una aplicación puede enviar mensajes a un tema, mientras que otras aplicaciones pueden crear suscripciones a este tema. Esto permite la comunicación de uno a varios entre un conjunto de aplicaciones, que permite que el mismo mensaje leerse con varios destinatarios.

**Temas de Bus de servicio y escenarios de suscripciones**

En cualquier momento configurar donde hay muchos mensajes que son importantes, pero solo necesitan distintos sistemas descendentes escuchar los diferentes subconjuntos de las comunicaciones, tema de Bus de servicio y suscripciones son una buena opción.


### <a name="biztalk-services"></a>Servicios de BizTalk
![Servicios de BizTalk](./media/fundamentals-introduction-to-azure/BizTalkServicesIntroNew.png)   
 *Ilustración: Servicios BizTalk proporciona la capacidad para transformar formatos de mensajes XML en la nube.*

A veces necesita conectar sistemas que se comuniquen con diferentes formatos de mensajería. Es común para la empresa para tener esquemas diferentes de la base de datos y mensajería formatos, incluso cuando esté disponible un estándar común XML. En lugar de escribir una gran cantidad de código personalizado, que puede usar BizTalk Server local para integrar distintos sistemas.  Servicios de BizTalk Azure proporciona el mismo tipo de servicio, pero en la nube. Puede pagar solo lo que utilice y no preocuparse de escala como tendría que local.


**Escenarios de BizTalk Services**

Interacciones de negocio a negocio (B2B) suelen requieran este tipo de traducción.  Por ejemplo, una empresa crear aviones necesita ordenar los elementos de su varios proveedores de elementos. Tendrá muchos de los proveedores de elementos.  Los pedidos se deberían automatizados para ir directamente a partir de los sistemas de creadores de avión en los sistemas de proveedores.  Ninguna empresa desea cambiar sus sistemas principales y los formatos de mensaje y no es muy probable que esos formatos son iguales. Servicios de BizTalk puede tomar mensajes y traducir entre los nuevos formatos de ambas formas. En el proveedor de avión puede hacer el trabajo para traducir o pueden varios proveedores, dependiendo de quién desea tener más control y la cantidad de traducción necesaria.     


## <a name="compute-assistance"></a>Calcular asistencia
Azure proporciona ayuda para los servicios que no es necesario ejecutar todo el tiempo.  

### <a name="scheduler"></a>Programador

![Programador de Azure](./media/fundamentals-introduction-to-azure/SchedulerIntroNew.png)   
*Ilustración: Programador de Azure proporciona una manera de programar trabajos en un momento determinado durante un período específico.*

A veces aplicaciones solo debe ejecutar en un momento determinado. En Azure, se ahorrará dinero con este tipo de aplicación en lugar de permitir que una aplicación simplemente siga ejecutando esperando datos procesar 24 x 7. Programador de Azure le permite programar cuándo se debe ejecutar una aplicación, basándose en el intervalo de tiempo o un calendario. Es confiable y comprobar que se ejecuta un proceso incluso si hay errores de centro de datos, equipo y red. Usar la API de REST de programador para administrar estas acciones.

Cuando se produce una alarma programada, programador envía mensajes de HTTP o HTTPS a un extremo específico o puede devolver un mensaje de una cola de almacenamiento.  Así que debe tener la aplicación tienen un extremo accesible o tiene supervisar una cola de almacenamiento. A continuación, una vez que obtiene el mensaje, puede realizar la acción que está programado para.

**Escenarios de programador**

- Acciones de aplicación periódica: por ejemplo, un servicio periódicamente puede obtener datos de twitter y recopilar los datos en una fuente normal.
- Mantenimiento diario: registro procesamiento o eliminación, realizar copias de seguridad y otra forma intermitente programación tareas.
- Tareas que se ejecutan por la noche.
- Tareas de aplicaciones Web como diaria eliminación de registros, realizar copias de seguridad y otras tareas de mantenimiento. Un administrador puede optar por la copia de seguridad de su base de datos a la 1 A.M. todos los días para los próximos meses 9, por ejemplo.

La API del programador le permite crear, actualizar, eliminar, ver y administrar colecciones de trabajo y las tareas programadas mediante programación.





## <a name="performance"></a>Rendimiento

Rendimiento siempre es importante para una aplicación. Aplicaciones suelen tener acceso a los mismos datos una y otra vez. Una forma de mejorar el rendimiento es mantener una copia de los datos más cerca a la aplicación, minimizar el tiempo necesario para recuperarla. Azure proporciona servicios diferentes para hacerlo.


### <a name="azure-caching"></a>Almacenamiento en caché de Azure

![Almacenamiento en caché de Azure](./media/fundamentals-introduction-to-azure/AzureCacheIntroNew.png)   
 **Ilustración: Una aplicación de Azure puede datos en la memoria caché e incluso dividirlo entre muchas funciones de trabajo**

Acceso a los datos almacenados en cualquiera de administración de datos de Azure base de datos SQL de servicios, tablas o Blobs-es bastante rápido. Aún tener acceso a datos almacenados en memoria es más rápido. Por este motivo, mantener una copia en memoria de datos frecuente puede mejorar el rendimiento de la aplicación. Puede usar en la memoria caché de Azure para realizar esta acción.


Una aplicación de servicios de nube puede almacenar datos en caché y recuperar directamente sin necesidad de tener acceso al almacenamiento persistente. La caché puede mantenerse dentro de máquinas virtuales de la aplicación o proporcionar máquinas virtuales dedicadas exclusivamente al almacenamiento en caché. En cualquier caso, la caché puede distribuirse, con los datos que contiene la vista a través de varias máquinas virtuales en un centro de datos de Azure.

Azure tiene un número de tecnologías de caché diferente que se haya desplazado a lo largo del tiempo. En el orden en que se introducen, hay compartido, función, administrada y Redis caché. La caché compartido es una tecnología antigua y no debe crear nuevas implementaciones con él. La caché administrada tiene las mismas características de la caché de rol, pero como servicio administrado fuera del Portal de administración de Azure. La caché Redis está en vista previa. La implementación Redis tiene el mayor número de características y se recomienda al escribir un nuevo código de almacenamiento en caché.


**Escenarios de caché de Azure**

Una aplicación que lee repetidamente un catálogo de productos puede beneficiarse del uso de este tipo de almacenamiento en caché, por ejemplo, ya que los datos necesita estarán disponible más rápidamente. La tecnología también es compatible con el bloqueo, dejar que pueden utilizarse con la lectura y escritura, así como datos de sólo lectura. Y aplicaciones ASP.NET pueden utilizar el servicio para almacenar datos de sesión con un cambio de configuración.

### <a name="content-delivery-network"></a>Red de entrega de contenido
![CDN de Azure](./media/fundamentals-introduction-to-azure/CDNIntroNew.png)   
 **Ilustración: Pueden ser caché copias de un blob en sitios de todo el mundo.**

Suponga que necesite para almacenar datos blob que pueden acceder a los usuarios de todo el mundo. ¿Es un vídeo de la última coincidencia de copa del mundo, por ejemplo, las actualizaciones de controladores o un libro electrónico popular. Almacenar una copia de los datos en varios centros de datos de Azure le ayudará a, pero si hay una gran cantidad de usuarios, probablemente no es suficiente. Para obtener un rendimiento aún mejor, puede usar la CDN de Azure.

La CDN tiene docenas de sitios de todo el mundo, cada una de ellas almacenar copias de blobs de Azure. La primera vez que un usuario en alguna parte del mundo tiene acceso a un determinado blob, la información que contiene se copia desde un centro de datos de Azure en almacenamiento CDN local en esa ubicación geográfica. Después de esto, accesos de esa parte del mundo usará la copia blob en caché en la CDN-no necesitan para ir hacia el centro de datos de Azure más cercano. El resultado es más rápido acceso a datos con frecuencia a los usuarios en cualquier lugar del mundo.

**Escenarios CDN**

Es común utilizar CDN con Media Services para reproducir vídeo en todo el mundo. Vídeo es normalmente grande y requiere una gran cantidad de ancho de banda.  Media Services se descritas en otra parte de esta página.



## <a name="big-data-and-big-compute"></a>Big Data y cálculo grande

### <a name="hdinsight-hadoop"></a>HDInsight (Hadoop)
![HDInsight](./media/fundamentals-introduction-to-azure/HDInsightIntroNew.png)   
 **Ilustración: HDInsight ayuda con el procesamiento masivo de grandes cantidades de datos**

Para muchos de los años, ha realizado la mayor parte del análisis de datos en datos relacionales almacenados en un almacén de datos creado con un DBMS relacional. Este tipo de análisis de empresa es importante y va a resultar mucho tiempo. Pero, ¿qué ocurre si los datos que desea analizar es tan grande que bases de datos relacionales simplemente no pueden controlar? ¿Y supongamos que los datos no relacionales? Es posible que los registros de servidor en un centro de datos, por ejemplo, los datos de eventos históricos de sensores o algo más. En casos así, tiene lo que se conoce como un problema de datos grande. Necesita otro enfoque.

La tecnología principal hoy para analizar datos grandes es Hadoop. Un Apache abrir el proyecto de origen, esta tecnología almacena datos mediante el sistema de archivos distribuido de Hadoop (HDFS), a continuación, le permite a los desarrolladores crear trabajos MapReduce para analizar los datos. HDFS distribuye datos entre varios servidores, entonces fragmentos ejecuciones del trabajo MapReduce en cada uno de ellos, lo que permite a los datos grandes se procesa en paralelo.

HDInsight es el nombre del servicio de Azure Hadoop Apache. HDInsight permite HDFS almacenar datos en el clúster y distribuir a través de varias VM. También se distribuye la lógica de un trabajo MapReduce en esas máquinas virtuales. Al igual que con Hadoop local, datos procesado localmente la lógica y los datos funciona en están en la misma VM- y en paralelo para mejorar el rendimiento. HDInsight también puede almacenar datos en depósito de almacenamiento de Azure (ASV), que usa BLOB.  Usar ASV le permite ahorrar dinero porque puede eliminar el clúster HDInsight cuando no esté en uso, pero mantener los datos en la nube.

HDinsight es compatible con otros componentes del ecosistema Hadoop, incluida la sección y cerdo. Microsoft ha creado también componentes que resulte más fácil trabajar con datos producidos por HDInsight con las herramientas de BI tradicionales, como el adaptador HiveODBC y el Explorador de datos que funcionan con Excel.

### <a name="high-performance-computing-big-compute"></a>Informática (cálculo grande) de alto rendimiento

Una de las formas más atractivas a utilizar una plataforma de nube es ejecutar (HPC) de informática de alto rendimiento y otras aplicaciones "Calcular grande". Ejemplos de aplicaciones de ingeniería especializadas creadas para usar la interfaz estándar de paso de mensajes (MPI), así como aplicaciones una paralelas denominadas, dichos modelos de riesgo financiero.

La esencia de cálculo grande está ejecutando código en varios equipos al mismo tiempo. En Azure, esto significa que se ejecutan muchos virtual máquinas simultáneamente, todos en paralelo para solucionar un problema. Esta acción requiere alguna forma a los recursos y para programar aplicaciones, por ejemplo, para distribuir su trabajo en estos casos. Paquete de HPC gratuita de Microsoft y otras soluciones de clúster de cálculo pueden realizar bien en Azure, sacar provecho de Azure Servicios de infraestructura y proceso para agregar capacidad a petición a un clúster de cálculo local o ejecutar aplicaciones calcular grande por completo en la nube.

Azure proporciona un rango de VM tamaños de instancia con diferentes configuraciones de CPU núcleos, memoria, capacidad de disco y otras características para satisfacer los requisitos de las diferentes aplicaciones. El trabajo de instancias de A8 y A9 recientemente introducido bien para muchos calcular cargas de trabajo intensivo y aplicaciones de MPI paralelas en particular, porque tienen alta velocidad, CPU multinúcleo y grandes cantidades de memoria. En algunas configuraciones de las instancias de aprovechar las ventajas de una red de baja latencia y alto rendimiento aplicación en la nube que incluye la tecnología de access (RDMA) de memoria directa remoto para una eficacia máxima de las aplicaciones de MPI paralelas.

Azure ofrece también un tamaño grande para calcular los desarrolladores de aplicaciones y socios un conjunto completo de capacidades de cálculo, servicios, opciones de arquitectura y herramientas de desarrollo. Azure es compatible con un tamaño grande para calcular flujos de trabajo personalizados relacionados con los flujos de trabajo de datos especializado y núcleos de cálculo de trabajo y patrones que pueden escalar a miles de la programación de tareas.



## <a name="media"></a>Multimedia

![Servicios multimedia de Azure](./media/fundamentals-introduction-to-azure/MediaServicesIntroNew.png)   
 **Ilustración: Media Services es una plataforma para las aplicaciones que proporcionan vídeos y otros medios a clientes de todo el mundo.**

Vídeo hoy constituye una gran parte del tráfico de Internet y el porcentaje se aún mayor mañana. Sin embargo, proporcionar vídeo en la web no simple. Hay muchas variables, como el algoritmo de codificación y la resolución de pantalla de la pantalla del usuario. Vídeo también tiende a tener ráfagas en petición, como un pico sábado por la noche cuando muchas personas decide que le gustaría ver una película en línea.

Su popularidad, es un lugar seguro seguro que se crearán muchas nuevas aplicaciones que usar vídeo. Aunque todos ellos tendrá que no resolver algunos de los mismos problemas y la realización de cada uno de ellos solucionar los problemas en su propia hace ningún sentido. Un enfoque mejor es crear una plataforma que proporciona soluciones comunes para muchas aplicaciones usar. Y la creación de esta plataforma en la nube tiene algunas ventajas de borrar. Puede ser ampliamente disponible en una base de pago, y también puede controlar los cambios en la demanda que a menudo orientará aplicaciones de vídeo.

Azure Media Services soluciona este problema. Proporciona un conjunto de componentes de nube que facilitan la vida de personas creando y ejecutando aplicaciones mediante los discos de vídeo y otros.

Como se muestra en la figura, Media Services proporciona un conjunto de componentes para aplicaciones que funcionan con vídeo y otros elementos multimedia. Por ejemplo, incluye un medio de recopilación de componente para cargar el vídeo en Media Services (donde se almacenan en Azure BLOB), un componente de codificación que admite varios formatos de vídeo y audio, un componente de protección de contenido que proporciona la administración de derechos digitales, un componente para insertar anuncios en una secuencia de vídeo, componentes de transmisión y más. Partners de Microsoft también pueden proporcionar los componentes de la plataforma y tiene Microsoft distribuir los componentes y facturación en su nombre.

Pueden ejecutar aplicaciones que utilizan esta plataforma en Azure o cualquier otro sitio. Por ejemplo, una aplicación de escritorio para una casa de producción de vídeo puede permitir que los usuarios cargar vídeo en Media Services, a continuación, procesar él de varias formas. Como alternativa, un servicio de administración de contenido basado en la nube que se ejecuta en Azure podría depender de Media Services para procesar y distribuir el vídeo. Siempre que se ejecuta y lo hace, cada aplicación elige los componentes que se debe usar, obtener acceso a ellos a través de interfaces REST.

Para distribuir produce, una aplicación puede utilizar la CDN de Azure CDN de otro, o simplemente enviar bits directamente a los usuarios. Sin embargo, hay obtiene, vídeo creado con Media Services se puede utilizar en distintos sistemas de cliente, incluyendo Windows, Macintosh, HTML 5, iOS, Android, Windows Phone, Flash y Silverlight. El objetivo es que sea más fácil crear aplicaciones multimedia moderna.

**Referencias**

Para obtener una vista más visual del funcionamiento de Media Services, descargue el [Póster de Azure Media Services][Azure Media Services Poster].

## <a name="commerce"></a>Comercio

El aumento de Software como servicio es transformar cómo creamos aplicaciones. También es transformar cómo vendemos aplicaciones. Desde una aplicación de SaaS reside en la nube, tiene sentido que sus clientes potenciales deben buscar soluciones en línea. Y este cambio se aplica a datos así como las aplicaciones. ¿Por qué no deberían buscar personas en la nube los conjuntos de datos disponibles en el mercado? Microsoft las direcciones de estos problemas con la [Azure Marketplace](https://azure.microsoft.com/marketplace/).

![Comercio de Azure](./media/fundamentals-introduction-to-azure/CommerceIntroNew.png)   
 **Ilustración: Azure Marketplace y el almacenamiento de Azure le permiten buscar y comprar aplicaciones de Azure y conjuntos de datos comercial y utilizarlas como parte de las aplicaciones de Azure.**

La diferencia entre los dos es que Marketplace está fuera del Portal de administración de Azure, pero se puede acceder desde la tienda dentro del portal. Para buscar las aplicaciones de Azure que se ajusten a sus necesidades, pueden buscar clientes potenciales. Los clientes pueden buscar comercial conjuntos de datos, incluidos datos demográficos, datos financieros, datos geográficos y más. Cuando encuentra algo que le guste, que pueden acceder a él desde el proveedor, directamente a través de las ubicaciones web Marketplace o almacén o en algunos casos desde el Portal de administración. Aplicaciones también pueden utilizar la API de búsqueda de Bing mediante el catálogo de soluciones dar acceso a los resultados de búsquedas de web.


**Escenarios de comercio**

SendGrid es una aplicación en el almacén de Azure que le permite enviar correo electrónico. Ofrece funciones adicionales como entrega confiable y estadísticas.  Puede comprar esta aplicación y los servicios relacionados en lugar de intentar crear dicha infraestructura usted mismo.  


## <a name="getting-started"></a>Introducción

Ahora que tiene el panorama, el siguiente paso es crear su primera aplicación de Azure. Elegir el idioma de [obtener el SDK adecuado](/downloads/)e inténtelo de nuevo. Nube informática es el nuevo valor predeterminado: empezar ahora.


[Azure Media Services Poster]: http://azure.microsoft.com/documentation/infographics/media-services/
