# <a name="compute"></a>Calcular

Azure le permiten implementar y supervisar el código de aplicación que se ejecuta dentro de un centro de datos de Microsoft. Cuando se crea una aplicación y ejecuta en Azure, el código y el conjunto de configuración se denomina una Azure hospedan el servicio. Servicios alojados son fáciles de administrar, escalar hacia arriba y abajo, vuelva a configurar y actualizar con las nuevas versiones de código de la aplicación. En este artículo se centra en la Azure hospedado modelo de aplicación de servicio.<a id="compare" name="compare"></a>

## Tabla de contenido<a id="_GoBack" name="_GoBack"></a>

-   [Ventajas del modelo de aplicación de Azure][]
-   [Conceptos básicos de servicio hospedado][]
-   [Consideraciones de diseño de servicio hospedado][]
-   [Diseño de la aplicación de escala][]
-   [Definición de servicio hospedado y configuración][]
-   [El archivo de definición de servicio][]
-   [El archivo de configuración de servicio][]
-   [Crear e implementar un servicio hospedado][]
-   [Referencias][]

## <a id="benefits"> </a>Ventajas del modelo de aplicación de azure

Cuando se implementa la aplicación como un servicio hospedado, Azure crea una o más máquinas virtuales de Windows (VM) que contienen el código de la aplicación y las máquinas virtuales inicia en equipos físicos que reside en uno de los centros de datos de Azure. Las solicitudes de cliente a la aplicación hospedada escribir el centro de datos, el equilibrio de carga distribuye estas solicitudes igualmente a las máquinas virtuales. Mientras la aplicación se hospeda en Azure, obtiene tres ventajas clave:

-   **Alta disponibilidad.** Alta disponibilidad significa que Azure garantiza que la aplicación se está ejecutando tanto como sea posible y puede responder a solicitudes de cliente. Si termina la aplicación (debido a una excepción no controlada, por ejemplo), Azure detecte y hará automáticamente volver a iniciar la aplicación. Si el equipo ejecuta la aplicación en experiencias algún tipo de error de hardware, a continuación, Azure también detecte y automáticamente crear una nueva máquina virtual en otro equipo físico de trabajo y ejecutar el código desde allí. Nota: En el orden de su aplicación para obtener el contrato de nivel de servicio de Microsoft de 99,95% disponible, debe tener al menos dos máquinas virtuales con el código de la aplicación. Esto permite una máquina virtual procesar las solicitudes de cliente mientras Azure mueve el código de una error VM a una máquina virtual nueva, buena.

-   **Escalabilidad.** Azure le permite fácilmente y cambiar dinámicamente el número de máquinas virtuales de ejecutar el código de la aplicación para controlar la carga real que se coloca en la aplicación. Esto le permite ajustar la aplicación de la carga de trabajo que sus clientes se colocación en él mientras pagando solo por las máquinas virtuales que necesite cuando los necesite. Cuando desee cambiar el número de máquinas virtuales, Azure responde en cuestión de minutos para que sea posible cambiar dinámicamente el número de máquinas virtuales con tantas veces como desee.

-   **Capacidad de administración.** Dado que Azure es una plataforma como una oferta de servicio (PaaS), administra la infraestructura (propio hardware, electricidad y redes) necesaria para mantener los equipos que ejecutan. Azure también administra la plataforma de asegurarse de que un sistema operativo actualizado con todas las las revisiones correctas y actualizaciones de seguridad, así como actualizaciones de componente como el .NET Framework e Internet Information Server. Dado que todas las máquinas virtuales están ejecutando Windows Server 2008, Azure proporciona características adicionales, como la supervisión de diagnóstico, soporte de escritorio remoto, firewalls y configuración de almacén de certificados. Todas estas características se proporcionan sin ningún costo adicional. De hecho, cuando se ejecuta la aplicación en Azure, se incluye la licencia de sistema operativo (SO) de Windows Server 2008. Dado que todas las máquinas virtuales están ejecutando Windows Server 2008, cualquier código que se ejecuta en Windows Server 2008 funciona bien cuando se ejecuta en Azure.

## <a id="concepts"> </a>Hospedado conceptos básicos de servicio

Cuando la aplicación se implementa como un servicio hospedado en Azure, se ejecuta como uno o más *roles.* Una *función* simplemente hace referencia a la configuración y los archivos de la aplicación. Puede definir una o más funciones para la aplicación, cada una con su propio conjunto de archivos de la aplicación y la configuración. Para cada rol en la aplicación, puede especificar el número de máquinas virtuales o *instancias de la función*, para ejecutar. La figura siguiente muestra dos ejemplos sencillos de una aplicación modelar como un servicio hospedado con funciones y las instancias de función.

##### <a name="figure-1-a-single-role-with-three-instances-vms-running-in-an-azure-data-center"></a>Figura 1: Una única función con tres instancias (VM) se ejecuta en un centro de datos de Azure

![imagen][0]

##### <a name="figure-2-two-roles-each-with-two-instances-vms-running-in-an-azure-data-center"></a>Figura 2: Dos funciones, cada una con dos instancias (VM), ejecute en un centro de datos de Azure

![imagen][1]

Las instancias de función normalmente procesan las solicitudes de cliente de Internet al centro de datos a través de lo que se denomina un *extremo de entrada*. Una única función puede tener extremos de 0 o más entradas. Cada extremo indica un protocolo (HTTP, HTTPS o TCP) y un puerto. Es común para configurar una función para que tenga dos extremos de entrada: HTTP escuchando en los puertos 80 y HTTPS en el puerto 443. La siguiente ilustración muestra un ejemplo de dos funciones diferentes con distintos extremos entradas dirigir las solicitudes de cliente en ellos.

![imagen][2]

Cuando se crea un servicio hospedado en Azure, se asigna una dirección IP públicamente accesible que los clientes pueden usar para tener acceso a él. Al crear el servicio hospedado también debe seleccionar un prefijo de dirección URL que está asignado a dicha dirección IP. Este prefijo debe ser único como esencialmente reservar el *prefijo*. cloudapp.net URL para que nadie más lo puede tener. Clientes comunicarse con las instancias de la función mediante la dirección URL. Normalmente, no se distribuya o publique el *prefijo*de Azure. cloudapp.net URL. En su lugar, se comprar un nombre DNS de su registrador de DNS de elección y configure el nombre DNS para redirigir los clientes a la dirección URL de Azure. Para obtener más detalles, vea [configurar un nombre de dominio personalizado en Azure][].

## <a id="considerations"> </a>Hospedado consideraciones de diseño de servicio

Al diseñar una aplicación para que se ejecute en un entorno de nube, hay varias consideraciones que se deben tener como latencia alta disponibilidad y escalabilidad.

Decidir dónde ubicar el código de la aplicación es un factor importante cuando se ejecuta un servicio hospedado en Azure. Es común para implementar la aplicación a los centros de datos más cercanos a los clientes para reducir la latencia y obtener el mejor rendimiento posible.
Sin embargo, puede elegir un centro de datos más cerca de su empresa o más cercana a los datos si tiene algunos problemas jurisdictional o legales acerca de los datos y que pertenece. Hay seis centros de datos todo el mundo pueda hospedar el código de la aplicación. La siguiente tabla muestra las ubicaciones disponibles:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**País o región**

</td>
<td style="width: 200px;">
**Regiones subcarpetas**

</td>
</tr>
<tr>
<td>
Estados Unidos

</td>
<td>
Sur Central y Norte Central

</td>
</tr>
<tr>
<td>
Europa

</td>
<td>
Norte y oeste

</td>
</tr>
<tr>
<td>
Asia

</td>
<td>
Sudeste & oriental

</td>
</tr>
</tbody>
</table>
Al crear un servicio hospedado, seleccione una región subcarpetas que indica la ubicación en la que desea que el código para ejecutar.

Para lograr alta disponibilidad y escalabilidad, es muy importante que los datos de la aplicación se mantienen en un repositorio central que sea accesible para varias instancias de la función. Para ayudarle a esto, Azure ofrece varias opciones de almacenamiento como blobs, tablas y base de datos de SQL. Consulte el artículo de [Ofertas de almacenamiento de datos en Azure][] para obtener más información sobre estas tecnologías de almacenamiento. La figura siguiente muestra cómo el equilibrador de carga dentro del centro de datos de Azure distribuye las solicitudes de cliente a las instancias de la función diferente que tiene acceso al mismo almacenamiento de datos.

![imagen][3]

Normalmente, en el que desea buscar el código de la aplicación y los datos en el mismo centro de datos como permite para baja latencia (mejor rendimiento) cuando el código de la aplicación tiene acceso a los datos. Además, no se cargando para ancho de banda cuando se mueven datos dentro del mismo centro de datos.

## <a id="scale"> </a>Diseño de la aplicación de escala

A veces, es aconsejable tener una sola aplicación (por ejemplo, un sitio web simple) y que hospedado en Azure. Pero con frecuencia, la aplicación puede consistir en varias funciones que funcionan juntas. Por ejemplo, en la siguiente ilustración, hay dos instancias de la función de sitio Web, tres instancias de la función de procesamiento y una instancia de la función Generador de informes. Estas funciones todos trabajan juntas y el código de todos ellos se puede empaquetar juntos e implementar como una sola unidad hasta Azure.

![imagen][4]

La razón principal para dividir una aplicación en diferentes funciones los que se ejecute en su propio conjunto de instancias de la función (es decir, VM) es escalar los roles de forma independiente. Por ejemplo, durante las vacaciones, muchos clientes pueden ser comprar productos de su empresa, por lo que desea aumentar el número de instancias de la función ejecutar su rol de sitio Web, así como el número de instancias de la función ejecuta el rol de procesamiento. Después de las vacaciones, puede obtener una gran cantidad de productos devueltos, por lo que todavía tendrá una gran cantidad de instancias de sitio Web pero menos instancias de procesamiento. Durante el resto del año, puede que tenga sólo algunos sitios Web y procesamiento de instancias. A lo largo de todo esto, puede que tenga una única instancia del generador de informes. La flexibilidad de implementaciones basado en roles en Azure le permite adaptar fácilmente la aplicación a sus necesidades empresariales.

Es común tienen el rol de instancias dentro de su servicio hospedado comunican entre sí. Por ejemplo, la función de sitio Web acepta pedido de un cliente pero, a continuación, se descarga el orden de procesamiento de las instancias de la función procesamiento. La mejor manera de pasar el formulario de trabajo un conjunto de instancias de la función a otro conjunto de instancias es mediante la tecnología cola de Azure, el servicio de cola o colas de Bus de servicio. El uso de una cola es una parte esencial de la historia aquí. La cola permite que el servicio hospedado escalar sus funciones por separado que permite equilibrar la carga de trabajo con costo. Si el número de mensajes en la cola aumenta con el tiempo, puede escalar el número de instancias de la función de procesamiento. Si el número de mensajes en la cola disminuye el tiempo, puede escalar el número de instancias de la función de procesamiento. De este modo, solo se pagando por las instancias de controlar la carga de trabajo real.

La cola también proporciona confiabilidad. Cuando reduzca el número de instancias de la función de procesamiento, Azure decide qué instancias para terminar. Puede decidir terminar una instancia que esté en medio de un mensaje de cola de procesamiento. Sin embargo, dado que el procesamiento de mensaje no finaliza correctamente, el mensaje se hace visible nuevamente para otra instancia de rol en el orden de procesamiento que se recopila y procesa. Debido a la visibilidad del mensaje de cola, se garantiza que los mensajes se procesan en algún momento. La cola también actúa como equilibrador de carga mediante la distribución eficaz de sus mensajes a las instancias de rol todos los mensajes de solicitud de él.

Para las instancias de función del sitio Web, puede supervisar el tráfico entrante en ellos y decide ajustar el número de ellos hacia arriba o abajo también. La cola le permite ampliar el número de instancias de la función de sitio Web independientemente de las instancias de la función procesamiento. Esto es muy eficaces y le ofrece una gran cantidad de flexibilidad. Por supuesto, si su aplicación consta de las funciones adicionales, puede agregar colas adicionales como el conducto para la comunicación entre ellos para poder aprovechar la misma escala y ventajas de costo.

## <a id="defandcfg"> </a>Alojado definición del servicio y configuración

Implementación de un servicio hospedado en Azure requiere que también tiene un archivo de definición de servicio y un archivo de configuración de servicio. Ambos archivos son archivos XML y permiten especificar opciones de implementación de su servicio hospedado de mediante declaración. El archivo de definición de servicio describe todas las funciones que componen el servicio hospedado y cómo se comunican. El archivo de configuración de servicio describe el número de instancias de cada rol y configuraciones que se usan para configurar cada instancia de la función.

## <a id="def"> </a>El archivo de definición de servicio

Como se mencionó anteriormente, la definición del servicio (CSDEF) archivo es un archivo XML que describe las distintas funciones que componen la aplicación completa. El esquema completo para el archivo XML pueden encontrarse aquí: [] [http://msdn.microsoft.com/library/windowsazure/ee758711.aspx].
El archivo CSDEF contiene un elemento WebRole o WorkerRole para cada rol que desee en la aplicación. Implementar un rol como un web (con el elemento WebRole) significa que el código se ejecutará en una instancia de la función que contiene Windows Server 2008 y Internet Information Server (IIS).
Implementar una función como una función de trabajador (con el elemento WorkerRole) significa que la instancia de rol tendrá Windows Server 2008 en él (no se instalará IIS).

Por supuesto puede crear e implementar una función de trabajo que utiliza otro mecanismo para escuchar las solicitudes entrantes de web (por ejemplo, el código podría crear y usar un HttpListener .NET). Puesto que todas las instancias de la función están ejecutando Windows Server 2008, el código puede realizar las operaciones que normalmente están disponibles para una aplicación que se ejecuta en Windows Server
2008.

Para cada rol, indican el tamaño deseado de VM que deben utilizar instancias de esa función. La siguiente tabla muestra los distintos tamaños VM disponibles hoy y los atributos de cada uno:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**Tamaño de memoria virtual**

</td>
<td>
**CPU**

</td>
<td>
**RAM**

</td>
<td>
**Disco**

</td>
<td>
**Red de recursos asignadas i/OS**

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extra pequeño**

</td>
<td>
1.0 1 GHz

</td>
<td>
768 MB

</td>
<td>
20GB

</td>
<td>
\~5 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Pequeña**

</td>
<td>
1 de 1,6 GHz

</td>
<td>
1,75 GB

</td>
<td>
225GB

</td>
<td>
\~100 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Medio**

</td>
<td>
2 de 1,6 GHz

</td>
<td>
3,5 GB

</td>
<td>
490GB

</td>
<td>
\~200 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Grande**

</td>
<td>
4 de 1,6 GHz

</td>
<td>
7 GB

</td>
<td>
1TB

</td>
<td>
\~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Muy grande**

</td>
<td>
8 de 1,6 GHz

</td>
<td>
14 GB

</td>
<td>
2TB

</td>
<td>
\~800 Mbps

</td>
</tr>
</tbody>
</table>
Se encarga de cada hora cada VM usar como una instancia de la función y también se aplican a todos los datos que las instancias de la función enviar fuera del centro de datos. No se cargan datos al centro de datos. Para obtener más información, vea [] [precios de Azure]. En general, es aconsejable usar varias instancias de rol pequeña en lugar de unas grandes instancias para que la aplicación sea más flexible para el error. Después de todo, las instancias de la función menos tiene, más desastrosos un error en alguno de los es una aplicación general. Además, como se mencionó anteriormente, debe implementar al menos dos instancias para cada función con el fin de obtener el contrato de nivel de servicio de 99,95% Microsoft proporciona.

El archivo de definición (CSDEF) de servicio también es donde se especificaría muchos atributos sobre cada rol en la aplicación. Estas son algunas de los elementos más útiles disponibles:

-   **Certificados**. Usar certificados para cifrar datos o si el servicio web compatible con SSL. Los certificados deben cargarse en Azure. Para obtener más información, vea [Administración de certificados en Azure][]. Esta configuración XML instala certificados cargados previamente en el almacén de certificados de la instancia de la función para que sean utilizables por el código de la aplicación.

-   **Nombres de valor de configuración**. Para los valores que desee que las aplicaciones para leer mientras se ejecuta en una instancia de la función. El valor real de los valores de configuración se establece en el archivo de configuración (CSCFG) de servicio que se puede actualizar en cualquier momento sin necesidad de tener que volver a implementar el código. De hecho, puede codificar las aplicaciones de manera para detectar los valores de configuración modificada sin que se produzca tiempo de inactividad.

-   **Extremos de entrada**. Aquí puede especificar los extremos HTTP, HTTPS o TCP (con puertos) que desea exponer al exterior a través de su *prefijo*. cloadapp.net URL. Cuando Azure implementa su rol, configurará el firewall de la instancia de rol automáticamente.

-   **Extremos internos**. Aquí puede especificar cualquier HTTP o TCP extremos que desea expuestas a otras instancias de la función que se implementan como parte de la aplicación. Extremos internos permiten todas las instancias de la función dentro de la aplicación de hablar entre sí, pero no tienen acceso a las instancias de cualquier función que están fuera de la aplicación.

-   **Módulos de importación**. Opcionalmente, estos instalación componentes útiles en las instancias de la función. Existen componentes para supervisar diagnóstico, escritorio remoto y Azure Connect (que permite la instancia de la función acceso a los recursos locales a través de un canal seguro).

-   **Almacenamiento local**. Así asigna un subdirectorio en la instancia de rol para utilizar la aplicación. Se describe con más detalle en el artículo de [Ofertas de almacenamiento de datos de Azure][] .

-   **Tareas de inicio**. Tareas de inicio le ofrecen una manera de instalar los componentes necesarios en una instancia de funciones como que se inician. Las tareas se pueden ejecutar con privilegios elevados como administrador si es necesario.

## <a id="cfg"> </a>El archivo de configuración de servicio

El archivo de configuración (CSCFG) de servicio es un archivo XML que describe la configuración que se pueden cambiar sin implementar la aplicación. El esquema completo para el archivo XML pueden encontrarse aquí: [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][].
El archivo CSCFG contiene un elemento de la función de cada rol en la aplicación. Estos son algunos de los elementos que puede especificar en el archivo CSCFG:

-   **Versión del SO**. Este atributo le permite seleccionar la versión de sistema operativo (SO) que desee utilizada para todas las instancias de función que se ejecuta el código de la aplicación. Este sistema operativo se conoce como el *sistema operativo invitado*y cada nueva versión incluye las revisiones de seguridad y actualizaciones disponibles en el momento en que se publicó el sistema operativo de invitado. Si establece el valor del atributo osVersion como "\*", luego Azure actualizará automáticamente el sistema operativo de invitado en cada una de las instancias de la función cuando estén disponibles nuevas versiones de sistema operativo de invitado. Sin embargo, puede optar por actualizaciones automáticas seleccionando la versión del SO invitado específico. Por ejemplo, establecer el atributo osVersion en un valor de "WA-invitado-OS-2,8\_201109-01" hace que todas las instancias de la función obtener lo que se describe en esta página web: [http://msdn.microsoft.com/library/hh560567.aspx][]. Para obtener más información acerca de las versiones de sistema operativo de invitado, consulte [Administrar actualizaciones para el sistema operativo de invitados de Azure].

-   **Instancias**. El valor de este elemento indica el número de instancias de la función que desee aprovisiona ejecuta el código para una función en particular. Dado que puede cargar un nuevo archivo CSCFG en Azure (sin implementar su aplicación), es trivial sencillo cambiar el valor de este elemento y cargar un nuevo archivo CSCFG para aumentar o reducir el número de instancias de la función ejecutar el código de la aplicación de forma dinámica. Esto le permite escalar fácilmente la aplicación hacia arriba o hacia abajo hasta la carga de trabajo real satisfacer demandas mientras también controla cuánto le cobrarán para ejecutar las instancias de la función.

-   **Configuración de valores de configuración**. Este elemento indica los valores de configuración (como se define en el archivo CSDEF). Su rol puede leer estos valores mientras se está ejecutando. Estos valores de la configuración se usan normalmente cadenas de conexión a la base de datos SQL o para el almacenamiento de Azure, pero se pueden usar para cualquier propósito que desee.

## <a id="hostedservices"> </a>Crear e implementar un servicio hospedado

Creación de un servicio hospedado requiere en primer lugar vaya al [Portal de administración de Azure] y proporcionando un servicio hospedado especificando un prefijo DNS y el centro de datos que desee en última instancia el código que se ejecuta en. A continuación, en el entorno de desarrollo, crear el archivo de definición (CSDEF) de servicio, generar el código de la aplicación y el paquete (zip) todos estos archivos en un archivo de paquete (CSPKG) de servicio. También debe preparar el archivo de configuración (CSCFG) de servicio. Para implementar su rol, cargue los archivos CSPKG y CSCFG con la API de administración de servicio de Azure. Una vez implementado, Azure, se instancias de la función de aprovisionamiento en el centro de datos (según los datos de configuración), extraer el código de aplicación del paquete, cópiela en las instancias de la función y las instancias de inicio. Ahora, el código está en funcionamiento.

La figura siguiente muestra los archivos CSPKG y CSCFG que crear en el equipo de desarrollo. El archivo CSPKG contiene el archivo CSDEF y el código de dos funciones. Después de cargar los archivos CSPKG y CSCFG con la API de administración de servicio de Azure, Azure crea las instancias de la función en el centro de datos. En este ejemplo, el archivo CSCFG indica que Azure debe crear tres instancias de la función \#1 y dos instancias de la función \#2.

![imagen][5]

Para obtener más información sobre cómo implementar, actualizar y a continuación, volver a configurar sus funciones, vea el artículo de [implementar y actualizar aplicaciones de Azure][] .<a id="Ref" name="Ref"></a>

## <a id="references"> </a>Referencias

-   [Creación de un servicio hospedado de Azure][]

-   [Administrar servicios alojados en Azure][]

-   [Migrar aplicaciones de Azure][]

-   [Configurar una aplicación de Azure][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Escrito por Jeffrey Richter (Wintellect)</p>

</div>

  [Ventajas del modelo de aplicación de Azure]: #benefits
  [Conceptos básicos de servicio hospedado]: #concepts
  [Consideraciones de diseño de servicio hospedado]: #considerations
  [Diseño de la aplicación de escala]: #scale
  [Definición de servicio hospedado y configuración]: #defandcfg
  [El archivo de definición de servicio]: #def
  [El archivo de configuración de servicio]: #cfg
  [Crear e implementar un servicio hospedado]: #hostedservices
  [Referencias]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configurar un nombre de dominio personalizado en Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
  [Ofertas de almacenamiento de datos de Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Azure Pricing]: http://www.windowsazure.com/pricing/calculator/
  [Administración de certificados en Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.Microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.Microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Administrar las actualizaciones a los invitados Azure OS]: http://msdn.microsoft.com/library/ee924680.aspx
  [Portal de administración de Azure]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Implementación y actualización de aplicaciones de Azure]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
  [Creación de un servicio hospedado de Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Administrar servicios alojados en Azure]: http://msdn.microsoft.com/library/gg433038.aspx
  [Migrar aplicaciones de Azure]: http://msdn.microsoft.com/library/gg186051.aspx
  [Configurar una aplicación de Azure]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
