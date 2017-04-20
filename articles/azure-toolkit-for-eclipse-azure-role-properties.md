<properties
    pageTitle="Propiedades de la función de Azure"
    description="Obtenga información sobre cómo usar el Kit de herramientas de Azure para Eclipse para establecer la configuración de la función de Azure."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690945.aspx -->

# <a name="azure-role-properties"></a>Propiedades de la función de Azure #

Pueden establecer varias opciones de configuración de su rol Azure dentro del Kit de herramientas de Azure para Eclipse.

## <a name="configuring-azure-role-properties"></a>Configurar propiedades de la función de Azure ##

Configurar las propiedades de la función de Azure se logra a través de los cuadros de diálogo de propiedades para la función de trabajo. Abra el menú contextual de la función en el panel del explorador de proyectos de Eclipse y seleccione el menú **Azure** . (Si no ve el rol en el Explorador de proyectos Eclipse, expanda el proyecto de Azure en el Explorador de proyectos).

![][ic789599]

En este tema, se describen las diversas propiedades que se pueden establecer en los cuadros de diálogo de **Propiedades** . Tenga en cuenta que muchas propiedades se rellenan automáticamente cuando se crea un nuevo proyecto de implementación de Azure.

Las páginas de propiedades siguientes están disponibles para las funciones de Azure.

* [Propiedades de la máquina virtual](#virtual_machine_properties)
* [Propiedades de almacenamiento en caché](#caching_properties)
* [Propiedades de certificados](#certificates_properties)
* [Propiedades de componentes](#components_properties)
* [Propiedades de depuración](#debugging_properties)
* [Propiedades de extremos](#endpoints_properties)
* [Propiedades de variables de entorno](#environment_variables_properties)
* [Equilibrio de carga y propiedades de la sesión afinidad (también denominado "sesiones permanentes")](#session_affinity_properties)
* [Propiedades de almacenamiento local](#local_storage_properties)
* [Propiedades de configuración de servidor](#server_configuration_properties)
* [Propiedades de descarga SSL](#ssl_offloading_properties)
    
<a name="virtual_machine_properties"></a>
### <a name="virtual-machine-properties"></a>Propiedades de la máquina virtual ###

Abrir el menú contextual para la función en el panel de Eclipse Explorador de proyectos, haga clic en **Azure**y, a continuación, haga clic en **Propiedades**, y que tienen la capacidad de cambiar el tamaño de la máquina virtual y también cambiar el número de instancias, tal como se muestra en la siguiente imagen.

![][ic719499]

>[AZURE.NOTE] Sólo Windows: cuando se establece el número de instancias en un valor mayor que 1, y también configurar un servidor de aplicaciones, el Kit de herramientas permite solo 1 instancia de rol para que se ejecute en el emulador, independientemente de esta configuración. Esto es para evitar conflictos de enlace de puerto entre las instancias de servidor diferente (por ejemplo, todos intenta enlazar al puerto 8080) cuando se ejecutan en el mismo equipo. Se mantiene la configuración de número de instancia deseada, pero entra en vigor solo cuando se implementa en la nube.

<a name="caching_properties"></a> 
### <a name="caching-properties"></a>Propiedades de almacenamiento en caché ###

Abrir el menú contextual de la función en el panel del explorador de proyectos de Eclipse, haga clic en **Azure**y, a continuación, haga clic en **caché**. En este cuadro de diálogo, puede habilitar la caché encuentran memcache compatible con nombre, lo que le permite acelerar las aplicaciones web.

![][ic719483]

En la página de propiedades de **almacenamiento en caché** , puede especificar la configuración global de la siguiente:

* Si se habilita el almacenamiento en caché comparten ubicación.
* el tamaño de caché como un porcentaje de memoria.
* el nombre de la cuenta de almacenamiento para guardar el estado de la caché cuando la aplicación se ejecuta como un servicio de nube o ninguno si no desea guardar el estado de la memoria caché. (El nombre de la cuenta de almacenamiento no se utiliza cuando se ejecuta la aplicación en el emulador de cálculo). Si establece el nombre de la cuenta de almacenamiento **(auto)** (que es el valor predeterminado), la configuración del almacenamiento en caché usar automáticamente la misma cuenta de almacenamiento como la que se selecciona en el cuadro de diálogo **publicar en Azure** .

>[AZURE.NOTE] La opción **(auto)** tendrá el efecto deseado solo si publica su implementación mediante el Kit de herramientas Eclipse Asistente para la publicación. Si en su lugar de publicar el archivo .cspkg manualmente mediante un mecanismo externo, como el [Portal de administración de Azure][], la implementación no funcionará correctamente.

El cuadro de diálogo siguiente muestra las propiedades de una caché.

![][ic719501]

* **Nombre:** El nombre de la caché que se encuentran.
* **Número de puerto:** Número de puerto que se va a usar para la caché.
* **Directiva de caducidad:** Uno de los siguientes valores que especifica cuando una clave en la memoria caché expira.
    * **Absoluta:** La clave caduca cuando se alcance la hora especificada por **minutos Live** .
    * **NeverExpires:** La clave no tiene una fecha de caducidad.
    * **SlidingWindow:** La clave caduca si no se ha accedido durante el tiempo especificado por **minutos Live**; cada vez que se tiene acceso a, se restablece el reloj de expiración.
* **Minutos Live:** Número máximo de minutos para una clave de memcached Live sujetos a la directiva de expiración.
* **Alta disponibilidad con copias de seguridad replicadas en instancias de otra función:** Si está habilitado, ayuda a proporciona alta disponibilidad utilizando replica copias de seguridad en instancias de la función diferente. Tenga en cuenta que al menos dos instancias de la función deben estar en vigor para la implementación para que funcione esta característica.

Para agregar una nueva caché, haga clic en el botón **Agregar** en la página de propiedades de **almacenamiento en caché** y se abrirá un cuadro de diálogo **Configurar caché denominada** . Proporcionar los valores de las propiedades que se ha descrito anteriormente.

Para modificar una caché con nombre, seleccione la memoria caché y haga clic en el botón **Editar** en la página de propiedades de **almacenamiento en caché** . Se abrirá un cuadro de diálogo que le permite modificar las propiedades de la memoria caché. Haga clic en **Aceptar** para guardar los valores de la memoria caché.

Para eliminar una caché, seleccione la memoria caché y haga clic en el botón **Quitar** en la página de propiedades de **almacenamiento en caché** y, a continuación, haga clic en **Sí** para confirmar la eliminación.

Para obtener más información sobre cómo usar el almacenamiento en caché, vea [cómo usar Co-located almacenamiento en caché][].

<a name="certificates_properties"></a> 
### <a name="certificates-properties"></a>Propiedades de certificados ###

Abrir el menú contextual de la función en el panel del explorador de proyectos de Eclipse, haga clic en **Azure**y, a continuación, haga clic en **certificados**.

![][ic710964]

En este cuadro de diálogo, puede agregar o quitar certificados que hace referencia el proyecto Eclipse. Observe que los certificados que se enumeran aquí no se almacena automáticamente dentro de cualquier almacén de claves de Java y, por tanto, no están disponibles automáticamente para cualquier uso desde dentro de una aplicación de Java. Solo se registran con Azure para que puede precargados en las ventanas de certificado almacenar en máquinas virtuales de su implementación y utilizará posteriormente por otro software de Windows. Actualmente, la única característica del Kit de herramientas que usa los certificados que se hace referencia a esta forma en el cuadro de diálogo **certificados** es [Descarga SSL][], debido a su dependencia en Internet Information Services (IIS) y la aplicación solicitar enrutamiento (ARR), que requieren el certificado correcto a disposición de esta manera.

Cuando se implementa el proyecto en Azure mediante el Asistente para la publicación, le pedirá para que apunten a los archivos de intercambio de información Personal (PFX) correspondientes a estos certificados, junto con sus contraseñas para cargarlos automáticamente al servicio de Azure, pero solo si no se han cargado allí previamente.

<a name="components_properties"></a> 
### <a name="components-properties"></a>Propiedades de componentes ###

Abrir el menú contextual de la función en el panel del explorador de proyectos de Eclipse, haga clic en **Azure**y, a continuación, haga clic en **componentes**. En este cuadro de diálogo, tiene la posibilidad de agregar, modificar, o quitar los componentes de la función, así como para cambiar el orden en que se procesan.

![][ic719502]

La característica de componentes le permite agregar dependencias a su proyecto de implementación de Azure, como los proyectos de aplicación de Java, archivos especiales e instrucciones de línea de comandos ejecutable que son necesarias para su implementación.

Para cada componente puede especificar:

* El paso que realizará al importar el componente en el proyecto de implementación de Azure cuando se genera.
* El paso que realizará al implementar ese componente en la nube de Azure.

>[AZURE.NOTE] Cuando se especifican los archivos de componente o líneas de comandos, tenga en cuenta que la implementación se publicará a una máquina virtual de Windows, por lo que los pasos personalizados deben ser válidos para un sistema operativo basado en Windows. 

Componentes tienen las siguientes propiedades:

* **Importar:** Método que se indica cómo se importarán el componente en el proyecto, cuando se genera el proyecto. Puede ser uno de los siguientes valores:
    * **copia:** El componente se copia de la ruta de acceso local especificada por la propiedad **de** directorio de **approot** del rol.
    * **OÍDO:** El componente es un archivo de enterprise Java (AURICULAR) importado de un proyecto de aplicación de empresa en la ruta de acceso local especificada por la propiedad **de** . (Esto se detecta automáticamente el Kit de herramientas en función de la naturaleza del proyecto en esa ubicación).
    * **JAR:** El componente es un archivo de Java (JAR) y se importa desde un proyecto de Java en especificado por la propiedad **de** la ruta de acceso local. (Esto se detecta automáticamente el Kit de herramientas en función de la naturaleza del proyecto en esa ubicación).
    * **Ninguno:** Para importar el componente se realiza ninguna acción. Esto es aplicable cuando el componente se supone que ya esté presente en el directorio de **approot** del rol, o cuando el componente es simplemente una instrucción ejecutable de línea de comandos, como se especifica en la propiedad **como** cuando el método de **implementación** es **ejecutivo**.
    * **Guerra:** El componente es un archivo de aplicación web de Java (guerra) y se importa de un proyecto Web dinámico especificado por la propiedad **de** la ruta de acceso local. (Esto se detecta automáticamente el Kit de herramientas en función de la naturaleza del proyecto en esa ubicación).
    * **zip:** El componente es un archivo zip y se importa al comprimir el directorio o el archivo especificado por la propiedad **de** .
* **Desde:** Ruta de acceso de origen en el equipo local a la carpeta o archivo que representa los elementos que desee importar a la implementación. Variables de entorno de Windows pueden usarse en esta propiedad. Todos los componentes que se pueden importar se importarán en el directorio de **approot** del rol cuando se genera el proyecto.
    
    Tenga en cuenta que tiene la capacidad de implementar un componente de una descarga cuando se implementa en la nube (no el emulador cálculo). Ver información relacionada debajo de información sobre cómo agregar un componente.    
    
* **As:** Nombre de archivo en el que el componente se importado **approot** directorio del rol y finalmente implementado en la nube de Azure. Deje la propiedad en blanco para mantener el nombre de la misma como está en el equipo local. (Para componentes ejecutables, es decir, aquellos cuyo método de **implementar** se establece en **ejecución**, puede ser una instrucción de línea de comandos de Windows arbitraria.)

    >[AZURE.IMPORTANT] Si usa caracteres de espacio para este valor, se tratará de forma diferente en función del método de implementación. Si el método de implementación es **ejecutivo**, se interpretará espacios como separadores de argumentos de la línea de comandos y no como parte del nombre de archivo. Para todos los demás implementar métodos, espacios se interpretará como parte del nombre de archivo.
    
* **Implementar:** Método que indica la acción que se aplica al componente cuando se inicia la implementación. Puede ser uno de los siguientes valores:
    * **copia:** El componente se copia en la ruta de destino especificada por la propiedad **a** .
    * **ejecutivo:** El componente es una instrucción de línea de comandos de Windows ejecutable que se ejecuta en el contexto de la ruta especificada por la propiedad **a** , en el momento en que se inicia la implementación.
    * **Ninguno:** Ninguna acción se aplica al componente cuando se inicie la implementación.
    * **zip:** El componente es descomprimido en la ruta de destino especificada por la propiedad **a** . Este método está disponible solo cuando la propiedad de **importación** esté **zip**.
* **To:** Ruta de destino en la máquina virtual donde se implementará el componente. Variables de entorno de Windows pueden usarse en esta propiedad y rutas de acceso de archivo son relativas **approot**.
    
Para agregar un nuevo componente, haga clic en el botón **Agregar** en la página de propiedades de **componentes** y se abrirá un cuadro de diálogo de **Componente del rol de Azure** . Proporcionar los valores de las propiedades que se ha descrito anteriormente. 

A continuación muestra un ejemplo para agregar un nuevo componente de guerra.

![][ic719503]

Cuando se implementa en la nube (no emulador de cálculo), si desea distribuir el componente de descarga, compruebe que esté activada la opción **en la nube, en lugar de incluir en el paquete de implementación de** . Si desea descargar desde su cuenta de almacenamiento de Azure, seleccione la cuenta de almacenamiento de la lista desplegable de **cuenta de almacenamiento** (puede hacer clic en el vínculo de **cuentas** para modificar lo que está en la lista), parcialmente que se rellena el campo **dirección URL** y, a continuación, rellene el resto de la dirección URL. Si no desea usar el almacenamiento de Azure, seleccione **(ninguno)** en la lista desplegable de **cuenta de almacenamiento** y escriba la dirección URL para el componente en el campo **dirección URL** . Especifique uno de los métodos siguientes:

* **copia:** El componente de descarga se copia en la ruta de destino especificada por la ruta de acceso **A directorios** .
* **mismo:** El mismo método usado para **implementar de descarga** en cuanto a la **implementación del paquete**.
* **zip:** El componente de descarga es descomprimido en la ruta de destino especificada por la ruta de acceso **A directorios** .

Para modificar un componente, seleccione el componente y haga clic en el botón **Editar** en la página de propiedades de **componentes** . Se abrirá un cuadro de diálogo que le permite modificar las propiedades del componente. Haga clic en **Aceptar** para guardar los valores de componente.

Para eliminar un componente, seleccione el componente y haga clic en el botón **Quitar** en la página de propiedades de **componentes** y, a continuación, haga clic en **Sí** para confirmar la eliminación.

Componentes se procesan en el orden indicado. Use los botones **Subir** y **Bajar** para organizar el orden.

>[AZURE.NOTE] La característica de configuración del servidor se basa en componentes físicos. Los componentes no se pueden quitar ni modificar sin eliminar la configuración del servidor correspondiente. Se le pedirá que al intentar realizar cambios en estos componentes.

<a name="debugging_properties"></a> 
### <a name="debugging-properties"></a>Propiedades de depuración ###

Abrir el menú contextual de la función en el panel del explorador de proyectos de Eclipse, haga clic en **Azure**y, a continuación, haga clic en **depuración**. En este cuadro de diálogo se tienen la capacidad para habilitar o Deshabilitar depuración remota, así como crear configuraciones de depuración, como se muestra en la siguiente imagen.

![][ic719504]

Para obtener información acerca de depuración, vea [Depurar aplicaciones de Azure en Eclipse][].

<a name="endpoints_properties"></a> 
### <a name="endpoints-properties"></a>Propiedades de extremos ###

Abrir el menú contextual de la función en el panel del explorador de proyectos de Eclipse, haga clic en **Azure**y, a continuación, haga clic en **extremos**. En este cuadro de diálogo, tiene la posibilidad de crear un extremo, así como editar o eliminar un punto final, tal como se muestra en la siguiente imagen.

![][ic719505]

Para agregar un extremo, haga clic en el botón **Agregar** en la página de propiedades de **extremos** y se abrirá un cuadro de diálogo **Agregar extremo** .

![][ic710897]

Escriba un nombre para el punto final, seleccione el tipo ( **entrada**, **interno**o **InstanceInput**) y especifique el puerto público y privado. Haga clic en **Aceptar** para guardar los nuevos valores de punto final.

Según el tipo de extremo, puede usar los intervalos de puertos como sigue:

* Para un extremo de instancia de entrada, el puerto público puede ser un intervalo de puertos (por ejemplo, **2000 2010**) y el puerto privado es un valor fijo.
* Para un extremo interno, no se utiliza el puerto público y el puerto privado puede ser un rango o en blanco o establecer un asterisco para indicar que se establece automáticamente por Azure.
* Extremos de entrada, el puerto público puede ser un valor fijo y el puerto privado puede ser un valor fijo o en blanco o en un asterisco para indicar establece automáticamente Azure.

Si desea usar un número de puerto único en lugar de un rango, deje el cuadro de texto para el final del rango blanco.

Para puertos que se establecen en automático, si se necesitan determinar el puerto que se utiliza durante la ejecución, la aplicación puede utilizar la API de tiempo de ejecución del servicio de Azure, que se describe en el [paquete de com.microsoft.windowsazure.serviceruntime resumen][].

Para ver cómo se pueden usar los extremos de entrada de instancia para ayudarle con la depuración de una implementación de instancias múltiples, vea [Depurar una instancia de rol específico en una implementación de instancias múltiples][].

Para modificar un punto final, seleccione el punto final y haga clic en el botón **Editar** en la página de propiedades de **extremos** . Se abrirá un cuadro de diálogo que le permite modificar el nombre del extremo, tipo y puertos públicos y privados. Haga clic en **Aceptar** para guardar los valores de extremo modificada.

Para eliminar un punto final, seleccione el punto final y haga clic en el botón **Quitar** en la página de propiedades de **extremos** y, a continuación, haga clic en **Sí** para confirmar la eliminación.

Para configurar correctamente algunas de las funciones (como la descarga de almacenamiento en caché, depuración remota, afinidad de la sesión o SSL) habilitadas por el usuario en una función, el Kit de herramientas puede configurar automáticamente extremos especiales que se mostrarán junto con los extremos definidas por el usuario. El Kit de herramientas impide que los usuarios editen o eliminen como automáticamente generado extremos mientras está habilitada la característica asociada.

<a name="environment_variables_properties"></a> 
### <a name="environment-variables-properties"></a>Propiedades de variables de entorno ###

Abrir el menú contextual de la función en el panel del explorador de proyectos de Eclipse, haga clic en **Azure**y, a continuación, haga clic en **Variables de entorno**. En este cuadro de diálogo, tiene la posibilidad de crear una variable de entorno, así como modificar o quitar una variable de entorno, tal como se muestra en la siguiente imagen.

![][ic719506]

Variables de entorno están disponibles para el script de inicio cuando se inicia el rol.

>[AZURE.NOTE] Al especificar variables de entorno, tenga en cuenta que la implementación se publicará a una máquina virtual de Windows, por lo que las variables de entorno deben ser válidas para un sistema operativo basado en Windows.

Como un ejemplo de una variable de entorno que están disponibles cuando se inicia la función, cree una nueva variable de entorno haciendo clic en el botón **Agregar** . A continuación se muestra una variable de entorno denominada **MyRoleVersion** que se crea y se le asigna el valor **1.0**.

![][ic659268]

En el código de jsp, puede mostrar el valor mediante la `System.getenv` método:

    <body>
      <b> Hello World!</b>
      <p>Running role version: <%= System.getenv("MyRoleVersion") %></p>
    </body>

Es el resultado de este resultado cuando se ejecuta la aplicación:

![][ic552233]

Para modificar una variable de entorno, seleccione la variable de entorno y haga clic en el botón **Editar** en la página de propiedades de **Variables de entorno** . Se abrirá un cuadro de diálogo que le permite modificar las propiedades de variables de entorno. Haga clic en **Aceptar** para guardar el entorno de valores de variables.

Para eliminar una variable de entorno, seleccione la variable de entorno y haga clic en el botón **Quitar** en la página de propiedades de **Variables de entorno** y, a continuación, haga clic en **Sí** para confirmar la eliminación.

Para configurar correctamente algunas de las funciones (por ejemplo, la configuración del servidor, depuración remota o almacenamiento Local) habilitada por el usuario en una función, el Kit de herramientas puede configurar automáticamente las variables de entorno especiales que se mostrarán junto con las variables de entorno definidas por el usuario. El Kit de herramientas impide que los usuarios editen o eliminen como automáticamente genera variables de entorno mientras está habilitada la característica asociada.

<a name="session_affinity_properties"></a> 
### <a name="load-balancing--session-affinity-aka-sticky-sessions-properties"></a>Equilibrio de carga y propiedades de la sesión afinidad (también denominado "sesiones permanentes") ###

Abrir el menú contextual de la función en el panel del explorador de proyectos de Eclipse, haga clic en **Azure**y, a continuación, haga clic en **Equilibrio de carga**. En este cuadro de diálogo, tiene la capacidad para habilitar o deshabilitar la afinidad de la sesión, como se muestra en la siguiente imagen.

![][ic719492]

Para obtener información relacionada, vea [Afinidad de la sesión][]. Además, observe el comportamiento de esta característica en el contexto de descarga SSL, como se describe en la [Descarga de SSL][].

<a name="local_storage_properties"></a> 
### <a name="local-storage-properties"></a>Propiedades de almacenamiento local ###

Abrir el menú contextual de la función en el panel del explorador de proyectos de Eclipse, haga clic en **Azure**y, a continuación, haga clic en **Almacenamiento Local**. En este cuadro de diálogo, tiene la capacidad de crear, modificar o quitar almacenamiento local temporal de la máquina virtual que está ejecutando la aplicación. Valores específicos se pueden establecer el tamaño de almacenamiento local, así como si el contenido se conserva cuando la función es recicla, tal como se muestra en la siguiente imagen.

![][ic719508]

Opcionalmente, también puede especificar una variable de entorno que corresponde al almacenamiento local.

De forma predeterminada, todo lo que se implementa en Azure se colocan (y descomprimir) en la carpeta **approot** de la instancia de la función. Mientras que las implementaciones más sencillas ajustar allí incluso después de descomprimir el espacio asignado para el directorio **approot** es limitada y no está bien definido (menos de 1 GB es razonable regla general). Por lo tanto, para asegurarse de que Azure asigna suficiente espacio en disco para implementaciones más grandes que podrían no caber en la carpeta **approot** , debe configurar un recurso de almacenamiento local mediante el cuadro de diálogo de **Almacenamiento Local** . Para que una forma sencilla de hacerlo, vea [Implementar implementaciones grandes][].

Puede hacer referencia fácilmente el recurso de almacenamiento de secuencias de comandos de inicio (por ejemplo, su **startup.cmd**) mediante la variable de entorno automáticamente asociada por el Kit de herramientas Eclipse al recurso, como se muestra en el cuadro de diálogo de **Almacenamiento Local** . Dicha variable de entorno contendrá la ruta de acceso completa del recurso local que ha configurado en el momento en que se ejecuta la secuencia de comandos de inicio. 

Para modificar un recurso de almacenamiento local, seleccione el recurso de almacenamiento local y haga clic en el botón **Editar** en la página de propiedades de **Almacenamiento Local** . Se abrirá un cuadro de diálogo que le permite modificar las propiedades del recurso de almacenamiento local. Haga clic en **Aceptar** para guardar los valores de recursos de almacenamiento local.

Para eliminar un recurso de almacenamiento local, seleccione el recurso de almacenamiento local y haga clic en el botón **Quitar** en la página de propiedades de **Almacenamiento Local** y, a continuación, haga clic en **Sí** para confirmar la eliminación.

<a name="server_configuration_properties"></a> 
### <a name="server-configuration-properties"></a>Propiedades de configuración de servidor ###

Abrir el menú contextual de la función en el panel del explorador de proyectos de Eclipse, haga clic en **Azure**y, a continuación, haga clic en **Configuración del servidor**. En este cuadro de diálogo, tienen la capacidad de agregar, quitar y modificar el servidor de aplicaciones de JDK y Java usado por la implementación, así como agregar o quitar las aplicaciones (como archivos de guerra, JAR o AURICULAR) usadas por la implementación.

### <a name="jdk-configuration"></a>Configuración de JDK ###

Este cuadro de diálogo le permite especificar el paquete JDK para su implementación. Si está utilizando Eclipse en Windows, puede especificar el paquete JDK localmente usar cuando se ejecuta en el emulador de Azure y tiene la opción para implementar la instalación local de Azure. En sistemas operativos Windows no, la configuración de JDK emulador no es aplicable y no se puede implementar la JDK instalado localmente ya que no es compatible con Windows. Sin embargo, independientemente del sistema operativo que está utilizando, siempre puede elegir entre los paquetes de JDK parte 3ª para implementar Azure o señale su propio paquete compatible con Windows JDK desde una ubicación de descarga alternativo.

A continuación se muestra un ejemplo de cómo puede especificar un JDK en Windows:

![][ic780647]

Si está utilizando Eclipse en Windows, puede especificar un JDK para usar con el emulador de cálculo; Para ello, asegúrese de **que usar el JDK desde esta ruta de acceso para probar localmente** está activada en la sección de **implementación de emulador** . A continuación, especifique la ruta de acceso local a su JDK; puede ir a diferentes JDK si lo que desea usar no aparece seleccionada automáticamente. También tiene la opción para implementar el JDK su servicio de nube Azure; Para ello, seleccione la opción **implementar mi JDK local (cargar automáticamente al almacenamiento de nube)** en la sección de **implementación de nube** .

Nota: En sistemas operativos Windows no, la configuración de **implementación de emulador** y la opción **implementar mi JDK local** no están disponibles. En el ejemplo siguiente se muestra que especifica un JDK en un equipo Mac o en otro sistema operativo de Windows no son compatible:

![][ic789643]

Independientemente del sistema operativo que está conectado, tiene las dos opciones de **implementación de nube** para el origen y el tipo de su paquete JDK siguientes:

* **Implementar un paquete JDK parte 3ª disponible en Azure** 
* **Implementar desde una descarga personalizada** 

Si está utilizando la opción **implementar un 3 º paquete JDK disponible de Azure** :

1. Active la casilla de verificación denominada **implementar un 3 º paquete JDK disponible de Azure**.
1. En la lista desplegable, seleccione el paquete JDK de fiesta 3ª que está disponible en Azure.
1. La ficha **JDK** tendrá un aspecto similar al siguiente en Windows:  ![][ic780648] 
    y tendrá un aspecto similar al siguiente en Mac OS u otros admiten sistemas operativos de Windows no: ![][ic789643]
1. Haga clic en **Aceptar** para guardar los cambios.
1. Cuando se le solicite para aceptar el contrato de licencia de la 3ª JDK paquete proveedores, revise los términos de licencia. Suponiendo que acepte los términos, haga clic en **Sí** para cerrar el cuadro de diálogo **Aceptar contrato de licencia** .
    Tenga en cuenta que se puede personalizar la lógica subyacente que aparecen los elementos en la lista desplegable para la opción de **implementar un 3 º paquete JDK disponible de Azure** . Para personalizar los elementos, en el cuadro de diálogo **JDK** , haga clic en el vínculo **Personalizar** . Esto cerrará la página de propiedades **JDK** y abra el archivo de **componentsets.xml** en Eclipse, que puede modificar según sea necesario. Documentación de **componentsets.xml** se incluye en el propio archivo **componentsets.xml** .

Si está utilizando la opción de **implementar una JDK desde una descarga personalizada** :

1. Crear una postal de su directorio de instalación de JDK, garantizar que el nodo del directorio secundario de la estructura ZIP y no su contenido. Tome nota del nombre del directorio, como se lo necesitará más adelante pero tenga en cuenta que esta instalación JDK se implementará en una máquina virtual de Windows.
1. Cargue el ZIP en su cuenta de almacenamiento de Azure como blob. Para ello, puede utilizar una herramienta disponible externamente para cargar blobs con el almacenamiento de Azure. Se recomienda usar un blob privada. Tome nota de la dirección URL de blobs de contenido ZIP.
1. Marque la casilla denominada **implementar un JDK desde una descarga personalizada**.
    Si desea descargar desde su cuenta de almacenamiento de Azure, seleccione la cuenta de almacenamiento de la lista desplegable de **cuenta de almacenamiento** (puede hacer clic en el vínculo de **cuentas** para modificar lo que está en la lista), parcialmente que se rellena el campo **dirección URL** y, a continuación, rellene el resto de la dirección URL. Si no desea usar el almacenamiento de Azure, seleccione **(ninguno)** en la lista desplegable de **cuenta de almacenamiento** y escriba la dirección URL para su descarga JDK en el campo **URL** . Si usa el almacenamiento de Azure, nombres de blobs de Windows en la dirección URL deben ser minúsculas.
1. Asegúrese de que el cuadro de texto **JAVA_HOME** hace referencia al nombre del directorio correcto. De forma predeterminada, hará referencia el mismo nombre de directorio JDK como el valor que ha elegido para su uso local. Pero, si el directorio de la postal tiene un nombre diferente (por ejemplo, debido a que utiliza una versión diferente), actualice el nombre del directorio en el cuadro de texto **JAVA_HOME** , ya que esta configuración se utilizará en la nube (no en el emulador cálculo).
1. Haga clic en **Aceptar** para guardar los cambios.

Eso es todo. Ahora, cuando se genera para la nube, observe el tamaño del paquete será mucho más pequeño, el proceso de generación normalmente tendrá menos tiempo y la implementación automáticamente cuando se publican en la nube debería tomarse menos tiempo. Tenga en cuenta que las opciones de **implementar mi JDK local (cargar automáticamente al almacenamiento de nube)** o **implementar un JDK desde una descarga personalizada** en vigor son solo cuando se implementa la aplicación en la nube. No tienen ningún efecto en la experiencia de emulador cálculo; la versión local de los componentes aún se utilizará cuando se implementa en el emulador de cálculo. 

### <a name="server-configuration"></a>Configuración del servidor ###

A continuación se muestra un ejemplo de cómo puede especificar un servidor de aplicaciones.

![][ic796926]

Compruebe que la casilla de verificación **implementar un servidor de este tipo** está seleccionada y, a continuación, elija el tipo de servidor de aplicaciones que desea usar.

Para especificar un servidor para la implementación de la nube, puede aprovechar las ventajas de las siguientes opciones:

1. **Implementar un 3 º servidor disponible en Azure** - esto es especialmente aplicable en escenarios de desarrollo o prueba donde eficiencia de implementación y simplificar es una prioridad y el servidor no requiere una configuración personalizada. O cuando desee usar uno de esos servidores como punto de partida pero incluye personalización de servidor apropiado de pasos de lógica de inicio de la implementación.
1. **Implementar desde una descarga personalizada** - es aplicable especialmente en escenarios de producción si tiene un servidor especialmente preparado y configurado de manera que desea usar en la nube.
1. **Implementar la instalación de servidor local** - esto es especialmente aplicable en si la instalación de servidor local está configurado personalizado para su uso. Si elige esta opción, también debe especificar ruta de acceso del servidor local en el cuadro de texto **ruta de acceso de servidor Local** .

Si está utilizando la opción **implementar un 3 º servidor disponible en Azure** :

1. Active la casilla de verificación denominada **implementar un 3 º servidor disponible en Azure**.
1. En el menú desplegable, seleccione el software de servidor que desea usar con la implementación en la nube. Recuerde que, si ya ha especificado un tipo de servidor para usar una versión anterior, se limitarán elegir un servidor de nube que se encuentra en la misma familia de ese tipo de servidor. Pero si no ha escogido un tipo de servidor, puede elegir entre cualquiera de los servidores que están disponibles actualmente en Azure y se selecciona automáticamente el tipo de servidor para usted.
1. Haga clic en **Aceptar** para guardar los cambios.

Si utiliza la opción **implementar desde una descarga personalizada** :

1. Asegúrese de que ya ha seleccionado un tipo de servidor según los pasos anteriores. Esto indica el complemento cómo implementar el servidor de su descarga personalizada, como debe ser de la misma familia como el tipo de servidor seleccionado.
1. Active la casilla de verificación denominada **implementar desde una descarga personalizada**.
    Si desea descargar desde su cuenta de almacenamiento de Azure, seleccione la cuenta de almacenamiento de la lista desplegable de **cuenta de almacenamiento** (puede hacer clic en el vínculo de **cuentas** para modificar lo que está en la lista), parcialmente que se rellena el campo **dirección URL** y, a continuación, rellene el resto de la dirección URL al servidor descargar ZIP (cuando se usa el almacenamiento de Azure, nombres de blobs de Windows en la dirección URL debe ser minúscula). Si no desea usar el almacenamiento de Azure, seleccione **(ninguno)** en la lista desplegable de **cuenta de almacenamiento** y escriba la dirección URL para la descarga de servidor postal en el campo **URL** . El ZIP contiene una carpeta secundaria que representa el directorio de instalación de servidor de aplicaciones. Por ejemplo, si está utilizando una postal de Apache Tomcat 7.0.35, dentro del zip sería la carpeta secundaria que representa el directorio de instalación, como **apache-tomcat-7.0.35**. 
1. Especificar el valor de la variable del entorno de directorio de inicio. El valor predeterminado será el valor utilizado para el servidor de aplicaciones local, en caso de haberlos, pero puede especificar un valor diferente si el servidor de aplicaciones de nube es diferente de su servidor de aplicación local. Sin embargo, debe asegurarse de que el servidor de aplicaciones de nube es la misma familia como el servidor de tipo seleccionado anteriormente.
    Si actualiza su zip de servidor de aplicación de nube en el futuro, puede cambiar manualmente la configuración del directorio de inicio, o bien, para que el nuevo que coincida con la configuración local (si se ha cambiado el servidor de aplicaciones local demasiado).
1. Haga clic en **Aceptar** para guardar los cambios.

Puede personalizar la lógica subyacente para que los elementos aparecen en la ficha **servidor** de la página de propiedades de la **Configuración del servidor** . Esta es una característica avanzada que es posible que tenga si sus necesidades extiendan más allá de los valores predeterminados o si desea agregar otros servidores. Para personalizar la lógica, en el cuadro de diálogo **servidor** , haga clic en el vínculo **Personalizar** . Esto se cierre la página de propiedades de la **Configuración del servidor** y abra el archivo de **componentsets.xml** en Eclipse, que puede modificar según sea necesario para extender la plantilla de configuración del servidor. Documentación de **componentsets.xml** se incluye en el propio archivo **componentsets.xml** .

Si está utilizando la opción **implementar mi servidor local (cargar automáticamente al almacenamiento de nube)** :

1. Active la casilla de verificación denominada **implementar mi servidor local (cargar automáticamente al almacenamiento de nube)**.
1. En la lista desplegable de **cuenta de almacenamiento** , haga clic en **(automático)**. Si especifica aquí **(auto)** , el Kit de herramientas Eclipse utiliza la misma cuenta de almacenamiento para su servidor como seleccionados para su implementación en el cuadro de diálogo **publicar en Azure** .
1. Haga clic en **Aceptar** para guardar los cambios.

Seleccione una ruta de instalación de servidor en el equipo en el cuadro de texto de la **ruta de acceso de servidor Local** si se cumple alguna de las condiciones siguientes:

* Desea probar la implementación en el emulador (se aplica solo a Windows).
* Desea implementar el servidor instalado localmente en la nube.
* Desea usar una descarga de servidor personalizado de su propio en la nube, en el caso, compruebe que la opción **implementar mi servidor local (cargar automáticamente al almacenamiento de nube)** está seleccionada por encima.

Si ninguna de las opciones anteriores se aplican a su situación, la configuración de servidor local es opcional.

### <a name="applications-configuration"></a>Configuración de aplicaciones ###

A continuación se muestra un ejemplo de cómo puede especificar una aplicación.

![][ic719512]

Haga clic en **Agregar** para agregar otra aplicación o en **Quitar** para quitar una aplicación. Por motivos de eficiencia, si desea usar una descarga para el origen de una aplicación cuando se implementa en la nube, use las [Propiedades de componentes](#components_properties) para especificar una dirección URL, cuenta de almacenamiento, etcetera. 

A partir de abril de 2014, las aplicaciones se cargan automáticamente en la misma cuenta de almacenamiento (en el contenedor **eclipsedeploy** ) como el elemento seleccionado para su implementación. La lógica de inicio de la implementación contiene un paso que primero descarga esas aplicaciones desde esa cuenta de almacenamiento. Esto significa que puede actualizar las aplicaciones en la implementación sin necesidad de reconstruir y volver a todo el paquete manualmente cargando nuevas versiones de la aplicación directamente en esa cuenta de almacenamiento (por ejemplo usando el portal de Azure), reemplazar los archivos de guerra cargado originalmente allí el Kit de herramientas. A continuación, simplemente inicie reciclaje de todas las instancias de función con el portal de administración de Azure nuevo o a través de utilidades de la línea de comandos. (Desencadenante rol reciclaje directamente desde dentro del Kit de herramientas Eclipse no es actualmente compatible.)

### <a name="notes-about-server-configuration"></a>Notas acerca de la configuración del servidor ###

Los cambios realizados a través de la página de propiedades de la **Configuración del servidor** se reflejan en la `<component>` elementos del archivo package.xml.

Cuando utiliza la **cargar automáticamente...** u opciones de **implementación de descargar...** para el JDK o servidor de aplicaciones y están creando para la nube (no el emulador cálculo) y está conectado a la red, quizás observe generar mensajes como los siguientes en el resultado de la consola, como el generador de Ant comprueba la disponibilidad de la descarga:

`[windowsazurepackage] Verifying blob availability (https://example.blob.core.windows.net/temp/tomcat6.zip)...` 

Si ha seleccionado la opción **implementar de descarga...** , se puede mostrar la siguiente advertencia, pero seguirá la compilación:

`[windowsazurepackage] warning: Failed to confirm blob availability! Make sure the URL and/or the access key is correct (https://example.blob.core.windows.net/temp/tomcat6.zip).` 

Esta advertencia es la única indicación de que no se ha comprobado la disponibilidad de la descarga. Así que si falla una implementación en la nube por algún motivo, compruebe si recibe esta advertencia.

Si desea deshabilitar la comprobación de descarga (por ejemplo, si se siente innecesariamente disminuye la compilación), establezca la `verifydownloads` atributo a `false` en la `<windowsazurepackage>` elemento de package.xml: 

`<windowsazurepackage verifydownloads="false" ...>` 

Si ha seleccionado la opción **cargar automáticamente...** , a continuación, en la ventana de consola, verá los mensajes de generación informar del progreso de la carga cada 5 segundos, siempre que una carga es necesario.

<a name="ssl_offloading_properties"></a> 
### <a name="ssl-offloading-properties"></a>Propiedades de descarga SSL ###

Abrir el menú contextual de la función en el panel del explorador de proyectos de Eclipse, haga clic en **Azure**y, a continuación, haga clic en **Descarga de SSL**. 

![][ic719481]

En este cuadro de diálogo, puede habilitar descarga SSL, lo que le permite habilitar fácilmente la compatibilidad de transferencia de hipertexto protocolo seguro (HTTPS) en la implementación de Java en Azure, sin necesidad de configurar SSL en el servidor de aplicaciones Java. Para obtener más información, consulte [Descarga de SSL][] y [cómo usar descarga de SSL][].

## <a name="see-also"></a>Vea también ##

[Kit de herramientas de Azure para Eclipse][]

[Instalar el Kit de herramientas de Azure Eclipse][]

[Crear una aplicación del mundo Hola para Azure en Eclipse][]

[Propiedades del proyecto de Azure][]

[Lista de cuentas de almacenamiento de Azure][]

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portal de administración de Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Propiedades del proyecto de Azure]: http://go.microsoft.com/fwlink/?LinkID=699524
[Lista de cuentas de almacenamiento de Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[Resumen del paquete com.Microsoft.windowsazure.serviceruntime]: http://azure.github.io/azure-sdk-for-java/com/microsoft/windowsazure/serviceruntime/package-summary.html
[Crear una aplicación del mundo Hola para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Depuración de una instancia de rol específico en una implementación de instancias múltiples]: http://go.microsoft.com/fwlink/?LinkID=699535#debugging_specific_role_instance
[Depuración de aplicaciones de Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Implementar implementaciones grandes]: http://go.microsoft.com/fwlink/?LinkID=699536
[Cómo usar comparten ubicación de almacenamiento en caché]: http://go.microsoft.com/fwlink/?LinkID=699542
[Cómo usar la descarga de SSL]: http://go.microsoft.com/fwlink/?LinkID=699545
[Instalar el Kit de herramientas de Azure Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Afinidad de la sesión]: http://go.microsoft.com/fwlink/?LinkID=699548
[Descarga de SSL]: http://go.microsoft.com/fwlink/?LinkID=699549

<!-- IMG List -->

[ic789599]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789599.png
[ic719499]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719499.png
[ic719483]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719483.png
[ic719501]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719501.png
[ic710964]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710964.png
[ic719502]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719502.png
[ic719503]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719503.png
[ic719504]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719504.png
[ic719505]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719505.png
[ic710897]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710897.png
[ic719506]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719506.png
[ic659268]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic659268.png
[ic552233]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic552233.png
[ic719492]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719492.png
[ic719508]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719508.png
[ic780647]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780647.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic780648]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780648.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic796926]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic796926.png
[ic719512]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719512.png
[ic719481]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719481.png
