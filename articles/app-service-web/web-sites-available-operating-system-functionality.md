<properties 
    pageTitle="Funcionalidad de sistema operativo en la aplicación de servicio de Azure" 
    description="Obtenga más información sobre las funciones de sistema operativo disponibles para aplicaciones web, una aplicación móvil back-ends y aplicaciones de API en la aplicación de servicio de Azure" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="mollybos"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/01/2016" 
    ms.author="cephalin"/>

# <a name="operating-system-functionality-on-azure-app-service"></a>Funcionalidad de sistema operativo en la aplicación de servicio de Azure #

En este artículo se describe la funcionalidad de sistema operativo común de línea base que está disponible para todas las aplicaciones que se ejecutan en el [Servicio de aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Esta funcionalidad incluye archivos y acceso al registro y registros de diagnósticos y red eventos. 

<a id="tiers"></a>
## <a name="app-service-plan-tiers"></a>Niveles de plan de servicio de aplicaciones

Aplicación de servicio se ejecuta aplicaciones cliente en un entorno de hospedaje del inquilino múltiples. Aplicaciones implementadas en los niveles de **Free** y **Shared** ejecutan en procesos de trabajo compartida máquinas virtuales de Windows, mientras implementadas en los niveles **Premium** y **estándar** de aplicaciones que se ejecutan en máquinas virtuales dedicado específicamente para las aplicaciones de un solo cliente.

Porque una experiencia perfecta escala entre diferentes niveles admite la aplicación de servicio, la configuración de seguridad exige para las aplicaciones de servicio de aplicación no cambiará. Así se garantiza que aplicaciones no repente tienen un comportamiento diferente, errores de forma inesperada, cuando cambia de plan de servicios de aplicación desde un nivel a otro.

<a id="developmentframeworks"></a>
## <a name="development-frameworks"></a>Marcos de desarrollo

Niveles de precios de servicio de aplicaciones controlan la cantidad de recursos de cálculo (CPU, almacenamiento en disco, memoria y salida de red) disponibles para las aplicaciones. Sin embargo, la gran variedad de funciones de marco de trabajo disponibles para aplicaciones permanece igual independientemente de los niveles de escala.

Aplicación de servicio es compatible con una amplia variedad de marcos de desarrollo, incluido ASP.NET, ASP clásico, node.js, PHP y Python - que ejecutar como extensiones en IIS. Para simplificar y normalizar la configuración de seguridad, aplicaciones de servicio de aplicaciones normalmente ejecutan los diversos marcos de desarrollo con su configuración predeterminada. Podría haber sido un enfoque a la configuración de aplicaciones personalizar la superficie de API y funcionalidad para cada entorno de desarrollo individuales. En su lugar, el servicio de aplicación toma un enfoque más genérico habilitando una línea de base comunes de funcionalidad de sistema operativo independientemente del entorno de desarrollo de la aplicación.

Las siguientes secciones resumen los tipos de funcionalidad de sistema operativo disponible para las aplicaciones de servicio de aplicación generales.

<a id="FileAccess"></a>
##<a name="file-access"></a>Acceso a archivos

Existen diversas unidades dentro de la aplicación de servicio, incluyendo unidades locales y unidades de red.

<a id="LocalDrives"></a>
### <a name="local-drives"></a>Unidades locales

Básicamente, aplicación es un servicio ejecutando sobre la infraestructura de Azure PaaS (plataforma como servicio). Como resultado, las unidades locales que son "adjunto" a una máquina virtual son los mismos tipos de unidad disponibles para cualquier función de trabajo se ejecuta en Azure. Esto incluye una unidad de sistema operativo (la unidad D:\), una unidad de aplicación que contiene los archivos de cspkg de paquete de Azure utilizados exclusivamente por la aplicación de servicio (y pueden tener acceso a los clientes) y una unidad de "usuario" (la unidad C:\), cuyo tamaño varía según el tamaño de la máquina virtual.

<a id="NetworkDrives"></a>
### <a name="network-drives-aka-unc-shares"></a>Unidades de red (también conocido como recursos compartidos UNC)

Uno de los aspectos de los servicios de aplicación que hace la implementación de la aplicación y el mantenimiento sencillo es que todo el contenido de usuario se almacena en un conjunto de recursos compartidos UNC. En este modelo muy bien asigna el modelo común de almacenamiento de contenido utilizado entornos que tienen varios servidores de equilibrio de carga de hospedaje en local. 

Dentro de la aplicación de servicio, hay número de recursos compartidos UNC creadas en cada centro de datos. Un porcentaje del contenido para todos los clientes en el centro de datos de cada usuario se asigna a cada recurso compartido UNC. Además, todo el contenido de la suscripción de un cliente único siempre se coloca en la misma UNC de archivo de compartir. 

Tenga en cuenta que debido a la forma de los servicios de nube, la máquina virtual específica responsable de alojar un recurso compartido UNC cambiará a lo largo del tiempo. Se garantiza que los recursos compartidos UNC se montaje por distintas máquinas virtuales mientras se ponen hacia arriba y abajo durante el transcurso normal de operaciones de nube. Por este motivo, aplicaciones nunca deben realizar suposiciones modificable que la información de una ruta de acceso UNC máquina permanece estable en el tiempo. En su lugar, debe usar la práctica *falsa* ruta absoluta **D:\home\site** que proporciona la aplicación de servicio. Esta ruta de acceso absoluta falsa proporciona un método portátil, independiente de aplicación y usuario para hacer referencia a la propia aplicación. Usando **D:\home\site**, uno puede transferir archivos compartidos desde la aplicación a la aplicación sin tener que configurar una nueva ruta de acceso absoluta en cada transferencia.

<a id="TypesOfFileAccess"></a>
### <a name="types-of-file-access-granted-to-an-app"></a>Tipos de acceso de archivo para una aplicación

Suscripción de cada cliente tiene una estructura de directorio reservada en un recurso compartido UNC específico dentro de un centro de datos. Un cliente puede tener varias aplicaciones creadas dentro de un centro de datos específicos, por lo que todos los directorios que pertenecen a una suscripción de cliente solo se crean en la misma UNC compartir. El recurso compartido puede incluir directorios como las de contenido, error y registros de diagnóstico y versiones anteriores de la aplicación creada por el control de origen. Según lo esperado, directorios de aplicación del cliente están disponibles para el acceso de lectura y escritura en tiempo de ejecución el de la aplicación código de aplicación.

En las unidades locales adjuntadas a la máquina virtual que se ejecuta una aplicación, servicio de aplicación de reserva un fragmento de espacio en la unidad C:\ para el almacenamiento temporal local de aplicación específica. Aunque una aplicación tiene acceso de lectura y escritura a su propio almacenamiento temporal local, ese almacenamiento realmente no están pensado para utilizarlo directamente por el código de la aplicación. En su lugar, la intención es proporcionar el almacenamiento de archivos temporales de IIS y web marcos de aplicaciones. Aplicación de servicio también limita la cantidad de almacenamiento local temporal disponible para cada aplicación para evitar que aplicaciones individuales se consumo excesivo cantidades de almacenamiento de archivos locales.

Dos ejemplos de cómo la aplicación de servicio usa almacenamiento local temporal son el directorio para los archivos temporales de ASP.NET y el directorio de IIS archivos comprimidos. El sistema de compilación de ASP.NET utiliza el directorio "Archivos temporales de ASP.NET" como una ubicación de la caché de compilación temporal. IIS utiliza el directorio "Archivos comprimidos temporales de IIS" para almacenar los resultados de respuesta comprimida. Ambos tipos de archivo uso (así como otras personas) reasignar en la aplicación de servicio para almacenamiento temporal local de cada aplicación. Esta reasignación garantiza que funcionalidad continúa según lo esperado.

Cada aplicación de servicio de la aplicación se ejecuta como una identidad de proceso aleatorio único trabajador de privilegios mínimos denominada la "identidad del grupo", se describen más aquí: [http://www.iis.net/learn/manage/configuring-security/application-pool-identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Código de la aplicación usa esta identidad para el acceso de solo lectura básico a la unidad de sistema operativo (la unidad D:\). Esto significa que lista comunes estructuras de directorio y leer los archivos comunes de unidad de sistema operativo código de la aplicación. Aunque esto podría aparecer como un amplio algo nivel de acceso, los mismos directorios y archivos sean accesibles al aprovisionar un rol de trabajo en un Azure servicio alojado y lee el contenido de la unidad. 

<a name="multipleinstances"></a>
### <a name="file-access-across-multiple-instances"></a>Acceso a los archivos a través de varias instancias

El directorio de inicio contiene el contenido de una aplicación y código de la aplicación puede escribir en él. Si una aplicación se ejecuta en varias instancias, el directorio de inicio se comparte entre todas las instancias para que todas las instancias de ven el mismo directorio. Por lo tanto, por ejemplo, si una aplicación guarda archivos cargados en el directorio de inicio, los archivos están inmediatamente disponibles para todas las instancias. 

<a id="NetworkAccess"></a>
## <a name="network-access"></a>Acceso a la red
Código de la aplicación puede utilizar TCP/IP y conexiones a los extremos de accesibilidad de Internet que exponen servicios externos de la red de protocolos UDP según para asegurarse de salida. Aplicaciones pueden utilizar estos mismos protocolos para conectarse a servicios de Azure & #151; por ejemplo, mediante el establecimiento de conexiones HTTPS a base de datos de SQL.

También es una capacidad limitada para las aplicaciones establecer una conexión de bucle local y tiene una aplicación escuchar en ese socket bucle local. Esta característica existe principalmente para habilitar las aplicaciones que esperan en bucle local sockets como parte de su funcionalidad. Tenga en cuenta que cada aplicación ve una conexión de bucle "privado"; aplicación "A" no se puede escuchar un socket de bucle local establecido por aplicación "B".

También se admiten canalizaciones con nombre como mecanismo de comunicación entre procesos (IPC) entre distintos procesos que se ejecutan colectivamente una aplicación. Por ejemplo, el módulo FastCGI de IIS se basa en canalizaciones con nombre para coordinar los procesos individuales que se ejecutan páginas PHP.


<a id="Code"></a>
## <a name="code-execution-processes-and-memory"></a>Ejecución de código, procesos y memoria
Como se mencionó anteriormente, ejecutan aplicaciones dentro de los procesos de trabajo de privilegios mínimos mediante una identidad del grupo de aplicaciones aleatorio. Código de la aplicación tiene acceso al espacio de memoria asociado con el proceso de trabajo, así como los procesos secundarios que pueden ser generados por procesos CGI u otras aplicaciones. Sin embargo, una aplicación no puede obtener acceso a la memoria o los datos de otra aplicación incluso si está en la misma máquina virtual.

Pueden ejecutar aplicaciones de secuencias de comandos o páginas escritas con entornos de desarrollo de web compatibles. Aplicación de servicio no configurar cualquier web framework modos más restringido. Por ejemplo, aplicaciones ASP.NET que se ejecuta en la aplicación de servicio se ejecutan en confianza "total" en lugar de un modo más restringido de confianza. Marcos Web, incluidos ASP clásico y ASP.NET, pueden llamar a componentes COM en proceso (pero no fuera de los componentes de proceso COM) como ADO (objetos de datos ActiveX) que están registrados de forma predeterminada en el sistema operativo Windows.

Aplicaciones pueden generar y ejecutar código arbitrario. Está permitida para una aplicación hacer cosas como generar un shell de comandos o ejecutar una secuencia de comandos de PowerShell. Sin embargo, aunque también se puede generar código arbitrario y procesos de una aplicación, scripts y programas ejecutables se limita a los privilegios concedidos al grupo de aplicaciones principal. Por ejemplo, una aplicación puede generar un archivo ejecutable que realiza una llamada de salida HTTP, pero ese mismo ejecutable no se puede intentar desvincular la dirección IP de una máquina virtual desde su NIC. Realizar una llamada de salida de red está autorizado para código con privilegios mínimos, pero intentar configurar red en una máquina virtual requiere privilegios de administrador.


<a id="Diagnostics"></a>
## <a name="diagnostics-logs-and-events"></a>Registros de diagnóstico y eventos
Información de registro es otro conjunto de datos que intentan obtener acceso a algunas aplicaciones. Los tipos de información de registro disponible para el código que se ejecuta en la aplicación de servicio incluye diagnósticos y registrar información generado por una aplicación que también es fácil acceso a la aplicación. 

Por ejemplo, registros de W3C HTTP generados por una aplicación activa están disponibles en un directorio de registro en la ubicación del recurso compartido de red creado para la aplicación, o disponibles en el almacenamiento de blobs si un cliente ha configurado el registro W3C al almacenamiento. La segunda opción permite grandes cantidades de registros que se recopila sin el riesgo de sobrepasan los límites de almacenamiento de archivo asociados a un recurso compartido de red.

En un modo similar, información de diagnóstico en tiempo real desde las aplicaciones de .NET también pueden registrarse utilizando el seguimiento de .NET y la infraestructura de diagnóstico, con opciones para escribir la información de seguimiento en el recurso compartido de red de la aplicación, o como alternativa a una ubicación de almacenamiento de blobs de Windows.

Áreas de diagnósticos de registro y seguimiento que no están disponibles para las aplicaciones son eventos de Windows ETW y comunes registros de eventos de Windows (por ejemplo, sistema, aplicación y seguridad registros de eventos). Puesto que la información de seguimiento ETW puede ser todo el equipo pueden ver (con la derecha ACL), acceso de lectura y escritura a eventos ETW están bloqueados. Los desarrolladores que observe que se llama API para leer y escribir ETW eventos y registros de eventos de Windows comunes parece funcionar, pero es porque la aplicación de servicio es "falsificación" las llamadas para que aparezcan correctamente. En realidad, el código de aplicación no tiene acceso a los datos del evento.

<a id="RegistryAccess"></a>
## <a name="registry-access"></a>Acceso al registro
Aplicaciones tienen acceso de solo lectura a la mayor parte (aunque no todos) del registro de la máquina virtual se están ejecutando en. En la práctica, esto significa que las claves de registro que permita el acceso de solo lectura para el grupo de usuarios local tienen acceso a aplicaciones. Un área del registro que no es compatible actualmente en acceso de lectura y escritura es la HKEY\_actual\_subárbol del usuario.

Se bloquea el acceso de escritura en el registro, incluido el acceso a las claves de registro por usuario. Desde la perspectiva de la aplicación, acceso de escritura en el registro nunca se debería confiar en el entorno de Azure desde aplicaciones pueden (y realizan) se migran a través de distintas máquinas virtuales. El almacenamiento de escritura solo persistente que puede depender en por una aplicación es la estructura de directorio de contenido por aplicación almacenada en los recursos compartidos de UNC de servicio de aplicación. 

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 
 
