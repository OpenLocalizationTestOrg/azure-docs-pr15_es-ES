# <a name="internet-of-things-security-architecture"></a>Arquitectura de seguridad de Internet de las cosas

Al diseñar un sistema, es importante comprender las amenazas potenciales a dicho sistema y agregar las defensas adecuadas en consecuencia, tal como está diseñado y diseñado el sistema. Es especialmente importante diseñar el producto desde el principio teniendo en mente la seguridad porque la comprensión de cómo un atacante podría ser capaz de poner en peligro un sistema de ayuda a tomar medidas paliativas adecuadas que estén en su lugar desde el principio. 

## <a name="security-starts-with-a-threat-model"></a>La seguridad comienza con un modelo de amenazas
 
Microsoft ha utilizada para sus productos de modelos de amenazas y ha hecho disponible públicamente del proceso de modelado de amenazas de la empresa. La experiencia de la empresa que presenta la modelización ventajas inesperadas más allá de la comprensión de cuáles son las amenazas más inmediata sobre. Por ejemplo, también crea una vía en una mesa redonda con otras personas fuera del equipo de desarrollo, que puede dar lugar a nuevas ideas y mejoras en el producto.
  
El objetivo del modelado de amenazas es entender cómo un atacante podría ser capaz de poner en peligro un sistema y asegúrese de que son medidas paliativas adecuadas en su lugar. Fuerzas de modelado de amenazas del diseño del equipo a tener en cuenta factores atenuantes como el sistema está diseñado en lugar de después de que un sistema se implementa. Este hecho es muy importante, ya que no es factible, instalación de accesorios de defensas de seguridad a un sinfín de dispositivos en el campo es propensa a errores y dejaría a los clientes en peligro.

Muchos equipos de desarrollo realizan un excelente trabajo capturar los requisitos funcionales para el sistema que benefician a los clientes. Sin embargo, la identificación de maneras no obvio que alguien podría utilizar indebidamente el sistema resulta más compleja. El modelado de amenazas puede ayudar a los equipos de desarrollo a comprender lo que podría hacer un atacante y por qué. El modelado de amenazas es un proceso estructurado que crea una discusión acerca de la seguridad en las decisiones de diseño en el sistema, así como cambios en el diseño que se realizan a lo largo de la forma en que la seguridad de impacto. Mientras que un modelo de amenazas es simplemente un documento, esta documentación también representa un medio ideal para garantizar la continuidad del conocimiento, retención de las lecciones aprendidas y ayuda nuevo equipo incorporado rápidamente. Finalmente, un resultado del modelado de amenazas es que le permite considerar otros aspectos de seguridad, como los compromisos de seguridad que desee proporcionar a sus clientes. Estos compromisos junto con el modelado de amenazas informaremos y prueba de su solución de Internet de las cosas (IoT) de la unidad.
 

### <a name="when-to-threat-model"></a>Cuándo se debe modelar las amenazas

[Threat modeling](http://www.microsoft.com/security/sdl/adopt/threatmodeling.aspx) ofrece el mayor valor si se incorpora a la fase de diseño. Cuando está diseñando, tendrá la mayor flexibilidad para realizar cambios para eliminar las amenazas. Eliminar las amenazas por diseño es el resultado deseado. Es mucho más sencillo que agregar mitigaciones, probarlas y asegurando siguen siendo actuales y además, su eliminación no siempre es posible. Resulta más difícil eliminar amenazas como un producto se vuelve más maduro y a su vez en última instancia requerirá más trabajo y equilibrios mucho más difíciles que el modelo desde el principio en el desarrollo de amenazas.

### <a name="what-to-threat-model"></a>¿Qué modelo de amenaza

Debe modelo la solución como un conjunto de subprocesos y también se centran en las siguientes áreas:

- Las características de seguridad y privacidad
- Las funciones cuyos errores están relacionados con la seguridad
- Las características que interactúan con un límite de confianza 

### <a name="who-threat-models"></a>Que los modelos de amenazas

El modelado de amenazas es un proceso como cualquier otro.  Es una buena idea para tratar el documento de modelo de amenazas como cualquier otro componente de la solución y validarla. Muchos equipos de desarrollo realizan un excelente trabajo capturar los requisitos funcionales para el sistema que benefician a los clientes. Sin embargo, la identificación de maneras no obvio que alguien podría utilizar indebidamente el sistema resulta más compleja. El modelado de amenazas puede ayudar a los equipos de desarrollo a comprender lo que podría hacer un atacante y por qué.

### <a name="how-to-threat-model"></a>Cómo el modelo de amenazas

El proceso de modelado de amenazas se componen de cuatro pasos. los pasos son:

- La aplicación del modelo
- Enumerar las amenazas
- Mitigar las amenazas
- Validar los factores atenuantes

#### <a name="the-process-steps"></a>Los pasos del proceso

Tres reglas básicas para tener en cuenta al elaborar un modelo de amenaza:

1. Crear un diagrama de arquitectura de referencia. 
2. Iniciar la primera respiración. Obtener una visión general y comprender el sistema como un todo, antes de adentrarnos en profundidad.  Esto ayuda a garantizar que le indicamos en los lugares adecuados.
3. Dirigir el proceso, no permita que el proceso la unidad. Si encuentra algún problema en la fase de modelado y desea explorarlo, ¡adelante!  No te sientas necesitará seguir estos pasos ciegamente.  

#### <a name="threats"></a>Amenazas

Los elementos principales de cuatro de un modelo de amenazas son:

- Procesos (servicios web, servicios de Win32, * nix daemons, etcetera. Tenga en cuenta que se pueden abstraer algunas entidades complejas (por ejemplo, puertas de enlace de campo y sensores) como un proceso cuando un técnico desglose en estas áreas no es posible.
- Los almacenes de datos (dondequiera que los datos se almacenan como un archivo de configuración o base de datos)
- Flujo de datos (donde mueve datos entre los otros elementos de la aplicación)
- Entidades externas (cualquier cosa que interactúa con el sistema, pero no está bajo el control de la aplicación, algunos ejemplos son los usuarios y las fuentes de satélite)

Todos los elementos en el diagrama arquitectónico están sujetos a diversas amenazas; utilizaremos el mnemotécnico STRIDE. Leer [Threat Modeling de nuevo, STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) para saber más acerca de los elementos STRIDE.

Diferentes elementos del diagrama de aplicaciones están sujeto a ciertas amenazas STRIDE:

- Los procesos están sujetos a STRIDE
- Flujos de datos están sujetos a TID
- Almacenes de datos están sujetos a TID y a veces R, si los almacenes de datos son archivos de registro.
- Entidades externas están sujetas a SRD

## <a name="security-in-iot"></a>Seguridad en IoT

Dispositivos conectados de propósito especial tienen un número significativo de posibles áreas de superficie de interacción y patrones de interacción, todos los cuales deben tener en cuenta para proporcionar un marco para la protección de acceso digital para esos dispositivos. El término "acceso digital" se utiliza aquí para distinguir de las operaciones que se llevan a cabo mediante la interacción directa del dispositivo donde se proporciona seguridad de acceso a través del control de acceso físico. Por ejemplo, poner el dispositivo en una sala con un bloqueo en la puerta. Mientras no se denegará el acceso físico mediante hardware y software, pueden adoptarse medidas para impedir el acceso físico líderes a la interferencia del sistema. 

A medida que exploramos los patrones de interacción, veremos "control de dispositivo" y "dispositivo datos" con el mismo nivel de atención. "Control de dispositivo" se puede clasificar como cualquier información que se proporciona a un dispositivo mediante cualquiera de las partes con el fin de cambiar o influir en su comportamiento hacia su estado o el estado de su entorno. "Datos de dispositivo" se pueden clasificar como cualquier información que emite un dispositivo a cualquier otra parte sobre su estado y el estado observado de su entorno.
   
Con el fin de optimizar las prácticas recomendadas de seguridad, se recomienda que una típica arquitectura IoT dividirse en varios componentes y zonas como parte de las amenazas de ejercicio. Estas zonas se describen completamente en esta sección e incluyen:

-   Dispositivo,
-   Puerta de enlace de campo,
-   La nube de puertas de enlace, y
-   Servicios.

Las zonas son amplias a segmentar una solución; a menudo, cada zona tiene sus propios requisitos de datos y la autenticación y la autorización. Las zonas también pueden utilizarse para daños de aislamiento y limitar el impacto de las zonas de confianza baja en zonas mayor confianza.

Cada zona está separada por un límite de confianza, se observa como la línea de puntos roja en el diagrama siguiente. Representa una transición de información desde un origen a otro. Durante esta transición, la información podría ser sujeto a suplantación, alteración, repudio, revelación de información, denegación de servicio y elevación de privilegios (STRIDE).

![Las zonas de seguridad de IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Los componentes utilizados dentro de cada límite también se someten a STRIDE, permitiendo un completo 360 vista de la solución de modelado de amenazas. En las secciones siguientes se explica cada de los componentes y problemas de seguridad específicos y soluciones que deben poner en marcha.

Las secciones que sigue analizan componentes estándar que normalmente se encuentran en estas zonas.

### <a name="the-device-zone"></a>La zona del dispositivo

El entorno del dispositivo es el espacio físico inmediato alrededor del dispositivo donde física acceso o "red local" peer-to-peer acceso digital al dispositivo es factible. Una "red local" se supone que es una red que es único y aislado desde – pero potencialmente bridged a – la Internet pública e incluye cualquier tecnología inalámbrica de corto alcance que permite la comunicación peer-to-peer de dispositivos. Lo hace *no* incluyen cualquier tecnología de virtualización de red creando la ilusión de dicha red local y no también incluyen redes de operadores públicos que requieren los dos dispositivos se comuniquen a través del espacio de la red pública si fueran a entrar en una relación de comunicación peer-to-peer.

### <a name="the-field-gateway-zone"></a>La zona de puerta de enlace de campo

Puerta de enlace de campo es un dispositivo de dispositivo o algún software de equipo de servidor de uso general que actúa como activador de comunicación y, posiblemente, como un sistema de control de dispositivo y el concentrador de procesamiento de datos del dispositivo. La zona de puerta de enlace de campo incluye la puerta de enlace de campo y todos los dispositivos que están conectados a él. Como su nombre indica, puertas de enlace de campo actúan fuera tratamiento de datos dedicado, suelen ubicación enlazada, potencialmente están sujetos a intrusiones físicas y tendrán limitaciones para la redundancia operativa. Todo para decir que una puerta de enlace de campo normalmente es algo uno puede tocar y sabotear al saber cuál es su función. 

Una puerta de enlace de campo es diferente de un enrutador de tráfico simple en que ha tenido un papel activo en la administración de acceso y el flujo de información, lo que significa que se trata de una aplicación dirigido entidad y conexión de red o sesión de terminal. Un dispositivo NAT o firewall, por el contrario, no se califican como puertas de enlace de campo Puesto que no son de conexión explícita o terminales de sesión, pero en su lugar un conexiones de ruta (o bloque) o sesiones realizadas a través de ellos. La puerta de enlace de campo tiene dos áreas diferenciadas de superficie. Uno enfrenta a los dispositivos conectados a ella y representa el interior de la zona, y el otro enfrenta a todas las partes externas y es el borde de la zona.   

### <a name="the-cloud-gateway-zone"></a>La zona de puerta de enlace de nube

Puerta de enlace de nube es un sistema que permite la comunicación remota desde y hacia dispositivos o puertas de enlace de campo desde varios sitios diferentes a través del espacio de la red pública, normalmente hacia un control basado en la nube y el sistema de análisis de datos, una federación de tales sistemas. En algunos casos, una puerta de enlace de nube puede inmediatamente facilitan el acceso a los dispositivos especiales de terminales como tabletas o teléfonos. En el contexto que se tratan aquí, "nube" sirve para hacer referencia a un sistema de procesamiento de datos dedicado que no está enlazado al mismo sitio que los dispositivos conectados o puertas de enlace de campo. También en una zona de la nube, impiden el acceso físico destino medidas operativas y no se expone necesariamente a una infraestructura de "nube pública".  

Una puerta de enlace de nube potencialmente puede asignarse en una superposición de virtualización de la red para aislar la puerta de enlace de la nube y todos sus dispositivos conectados o puertas de enlace de campo desde cualquier otro tráfico de red. La puerta de enlace de nube es un sistema de control de dispositivo ni un procesamiento ni dispositivo de almacenamiento de datos del dispositivo; las instalaciones de la interfaz con la puerta de enlace de nube. La zona de puerta de enlace de nube incluye el gateway de nube junto con todas las puertas de enlace de campo y dispositivos conectados directa o indirectamente a ella. El borde de la zona es un área de superficie distintos donde todos los usuarios externos comunican a través de.

### <a name="the-services-zone"></a>La zona de servicios

Un "servicio" se define para este contexto como cualquier componente de software o el módulo que está interactuando con dispositivos a través de una puerta de enlace de nube o campo para la recopilación de datos y análisis, así como para el comando y control.  Los servicios son mediadores. Actúen bajo su identidad hacia las puertas de enlace y otros subsistemas, almacenarán y analizan datos, autónomamente emitir comandos a los dispositivos basados en opiniones de datos o programaciones y exponen información y controlan funciones a usuarios finales autorizados.

### <a name="information-devices-vs-special-purpose-devices"></a>Dispositivos de información frente a los dispositivos especiales

Equipos, teléfonos y las tabletas son principalmente dispositivos de información interactiva. Los teléfonos y las tabletas se optimizan explícitamente en maximizar la duración de la batería. Preferiblemente desactivar parcialmente al no inmediatamente interactuar con una persona, o si no se proporciona servicios como reproducir música o guiar a su propietario a una ubicación concreta. Desde una perspectiva de sistemas, estos dispositivos de tecnología de información principalmente actúan como proxies hacia las personas. Son "actuadores de personas" que sugieren acciones y "personas sensores" recopilación de datos proporcionados. 

Dispositivos especiales, de los sensores de temperatura simple a las líneas de producción de fábrica complejas con miles de componentes dentro de ellos, son diferentes. Ámbito de estos dispositivos están mucho más en su finalidad y aunque proporcionan alguna interfaz de usuario, en gran medida el ámbito para interactuar con o que se integren los activos en el mundo físico. Medir y notificar circunstancias medioambientales, activar válvulas, controlan servos, alarmas de sonido, interruptor de las luces y realizar muchas otras tareas. Ayudan a trabajar para que un dispositivo de información es demasiado genérico, demasiado caro, demasiado grande o demasiado frágil. El propósito concreto inmediatamente dicta su diseño técnico como bien el presupuesto monetario disponible para su producción y la operación de vida prevista. La combinación de estos dos factores clave limita el cálculo de presupuesto de energía, espacio físico y, por tanto, el almacenamiento disponible, operativo disponible y capacidades de seguridad.  

Si algo "va mal" con dispositivos controlables automatizados o remotos, por ejemplo, defectos físicos o lógica de control de defectos de manipulación y deliberada intrusiones no autorizadas. Pueden destruir los lotes de producción pueden ser saqueados o incendio en edificios y las personas pueden ser herido o incluso mueren. Esto es, por supuesto, una clase totalmente diferente de los daños que alguien ampliarse límite de tarjetas de crédito robadas. La barra de seguridad para dispositivos que hacen cosas que mover y también para los datos de sensor que finalmente provoca en los comandos que causan las cosas mover, debe ser mayor que en cualquier escenario de banca o comercio electrónico. 


### <a name="device-control-and-device-data-interactions"></a>Control de dispositivo y las interacciones de datos del dispositivo

Dispositivos conectados de propósito especial tienen un número significativo de posibles áreas de superficie de interacción y patrones de interacción, todos los cuales deben tener en cuenta para proporcionar un marco para la protección de acceso digital para esos dispositivos. El término "acceso digital" se utiliza aquí para distinguir de las operaciones que se llevan a cabo mediante la interacción directa del dispositivo donde se proporciona seguridad de acceso a través del control de acceso físico. Por ejemplo, poner el dispositivo en una sala con un bloqueo en la puerta. Mientras no se denegará el acceso físico mediante hardware y software, pueden adoptarse medidas para impedir el acceso físico líderes a la interferencia del sistema. 
 
A medida que exploramos los patrones de interacción, veremos "control de dispositivo" y "dispositivo datos" con el mismo grado de atención durante el modelado de amenazas. "Control de dispositivo" se puede clasificar como cualquier información que se proporciona a un dispositivo mediante cualquiera de las partes con el fin de cambiar o influir en su comportamiento hacia su estado o el estado de su entorno. "Datos de dispositivo" se pueden clasificar como cualquier información que emite un dispositivo a cualquier otra parte sobre su estado y el estado observado de su entorno. 

## <a name="threat-modeling-the-azure-iot-reference-architecture"></a>La arquitectura de referencia de Azure IoT de modelado de amenazas

Microsoft utiliza el marco mencionado anteriormente para la modelización de Azure IoT de amenazas. En la sección siguiente, por tanto, utilizamos el ejemplo concreto de la arquitectura de referencia de Azure IoT para demostrar cómo piense para IoT de modelado de amenazas y cómo tratar las amenazas identificadas. En nuestro caso hemos identificado cuatro áreas principales de enfoque:

-   Dispositivos y orígenes de datos,
-   Transporte de datos
-   Dispositivo y el procesamiento de los eventos, y
-   Presentación

![Amenazas de Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png) 

El siguiente diagrama proporciona una vista simplificada de la arquitectura de Microsoft IoT mediante un modelo de diagrama de flujo de datos que es utilizado por la herramienta de modelado de amenazas de Microsoft:

![Amenazas para IoT Azure utilizando la herramienta de modelado de amenazas de MS](media/iot-security-architecture/iot-security-architecture-fig3.png)

Es importante destacar que la arquitectura separa las capacidades del dispositivo y la puerta de enlace. Esto permite al usuario aprovechar los dispositivos de puerta de enlace que son más seguros: son capaces de comunicarse con el gateway de nube mediante protocolos seguros, que normalmente requiere una mayor sobrecarga de procesamiento que podría proporcionar un dispositivo nativo - como un termostato - por sí mismo. En la zona de servicios de Azure, suponemos que la puerta de enlace de nube se representa mediante el servicio de Azure IoT concentrador.

### <a name="device-and-data-sourcesdata-transport"></a>Fuentes de dispositivo y datos o datos de transporte

Esta sección examina la arquitectura descrita anteriormente a través de la lente del modelado de amenazas y ofrece una visión general de cómo nos dirigimos a algunos de los problemas inherentes. Nos centraremos en los elementos principales de un modelo de amenazas:

- Procesos (aquellos bajo nuestro control y elementos externos)
- Comunicación (también denominada flujos de datos)
- Almacenamiento (también denominado almacenes de datos)

#### <a name="processes"></a>Procesos

En cada una de las categorías descritas en la arquitectura Azure IoT, intentamos mitigar una serie de amenazas distintas en las diferentes fases que existe información en: proceso, comunicación y almacenamiento de información. A continuación ofrecemos una visión general de los más comunes para la categoría de "proceso", seguido de una descripción general de cómo estos podrían mitigar mejor: 

**Suplantación de identidad (S)**: un atacante puede extraer material de clave criptográfica de un dispositivo, ya sea en el nivel de software o hardware, y posteriormente el sistema con un dispositivo físico o virtual diferente bajo la identidad del dispositivo el material de clave de acceso ha sido tomado de. Un buen ejemplo es que los controles remotos que puede activar cualquier TV y que son herramientas populares bromista.

**Denegación de servicio (D)**: un dispositivo puede representarse incapaces de funcionamiento o comunicación interfiriendo con frecuencias de radio o hilos de corte. Por ejemplo, una cámara de vigilancia que tenía su conexión de red o alimentación eliminado intencionadamente no informará de los datos, en absoluto.

**Alteración (T)**: un atacante puede parcial o totalmente reemplazar el software que se ejecuta en el dispositivo, podría permitir el software reemplazado aprovechar la identidad original del dispositivo si el material de clave o las funciones criptográficas manteniendo los materiales clave estaban disponibles para el programa ilícito. Por ejemplo, un atacante puede aprovechar extraído material clave para interceptar y suprimir los datos desde el dispositivo en la ruta de comunicación y reemplazarlo por datos falsos que se autentican con el material de clave robado.

**Divulgación de información (I)**: si el dispositivo está ejecutando software manipulado, dicho software manipulado puede provocar pérdidas de datos a personas no autorizadas. Por ejemplo, un atacante puede aprovechar material clave extraído para inyectar a sí mismo en la ruta de comunicación entre el dispositivo y controlador o campo puerta de enlace o gateway de nube con sifón la información.

**Elevación de privilegios (E)**: un dispositivo que realiza una función específica puede ser obligado a hacer algo más. Por ejemplo, puede inducir a una válvula que está programada para abrir a mitad de camino para abrir completamente.

| **Componente** | **Amenaza** | **Mitigación**                                                                                                                                                | **Riesgo**                                                                                                                                                                                                    | **Implementación**                                                                                                                                                                                                                                                                                                                                     |
|---------------|------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dispositivo        | S          | Asignación de identidad para el dispositivo y autenticar el dispositivo                                                                                                | Reemplazo de dispositivo o parte del dispositivo con otro dispositivo. ¿Cómo sabemos que estamos hablando al dispositivo correcto?                                                                                           | Autenticar el dispositivo, mediante seguridad de capa de transporte (TLS) o IPSec. Infraestructura debe admitir mediante clave previamente compartida (PSK) en los dispositivos que no pueden controlar la criptografía asimétrica completa. Aprovechan AD Azure, [OAuth](http://www.rfc-editor.org/in-notes/internet-drafts/draft-ietf-ace-oauth-authz-01.txt)                             |
|               | TRID       | Aplicar mecanismos a prueba de manipulaciones para el dispositivo, por ejemplo, haciéndola muy difícil Imposible extraer claves y otro material cifrado desde el dispositivo. | El riesgo es que si alguien es alteración del dispositivo (interferencia física). ¿Cómo estamos seguro, ese dispositivo no ha alterado.                                                                                 | La mitigación más eficaz es una capacidad de módulo (TPM) de plataforma de confianza que permite almacenar las claves en circuitos en chip especiales del que no se pueden leer las claves, pero sólo pueden utilizarse para operaciones criptográficas que utilizan la clave pero nunca revelen la clave. Cifrado de la memoria del dispositivo. Administración de claves para el dispositivo. Firmar el código. |
|               | E          | Tener control de acceso del dispositivo. Esquema de autorización.                                                                                                    | Si el dispositivo permite acciones individuales que se deben realizar en función de comandos desde una fuente externa, o incluso en peligro de sensores, permitirá el ataque a realizar operaciones no accesible. | Tener esquema de autorización para el dispositivo                                                                                                                                                                                                                                                                                                             |
| Puerta de enlace de campo | S          | Autenticación de la puerta de enlace de campo a puerta de enlace de nube (cert base, PSK, reclamación basada,...)                                                                           | Si alguien puede imitar el campo puerta de enlace, a continuación, puede presentarse como cualquier dispositivo.                                                                                                                               | TLS RSA/PSK, dire, [RFC 4279](https://tools.ietf.org/html/rfc4279). Los mismos asuntos clave de almacenamiento de información y la certificación de dispositivos en general: mejor caso es usar TPM. Extensión de 6LowPAN para IPSec admitir redes inalámbricas de sensores (WSN).                                                                                                              |
|               | TRID       | ¿Proteger la puerta de enlace de campo contra la manipulación (TPM)?                                                                                                            | Suplantación de ataques que engañan el pensamiento de puerta de enlace de nube que está conectando a la puerta de enlace de campo podría resultar en la revelación de información y manipulación de datos                                                             | Del memoria cifrado, TPM, autenticación.                                                                                                                                                                                                                                                                                                              |
|               | E          | Mecanismo de control de acceso de puerta de enlace de campo                                                                                                                    |                                                                                                                                                                                                             |                                                                                                                                                                                                                                                                                                                                                        |

A continuación presentamos algunos ejemplos de amenazas de esta categoría:

Suplantación: Un atacante puede extraer material de claves criptográfica de un dispositivo, ya sea en el software o hardware y posteriormente acceso de que el sistema con un dispositivo físico o virtual diferente bajo la identidad del dispositivo del material de claves se ha tomado.

**Denegación de servicio**: un dispositivo puede representarse incapaces de funcionamiento o comunicación interfiriendo con frecuencias de radio o hilos de corte. Por ejemplo, una cámara de vigilancia que tenía su conexión de red o alimentación eliminado intencionadamente no informará de los datos, en absoluto.

**Manipulación**: un atacante puede parcial o totalmente reemplazar el software que se ejecuta en el dispositivo, podría permitir el software reemplazado aprovechar la identidad original del dispositivo si el material de clave o las funciones criptográficas manteniendo los materiales clave estaban disponibles para el programa ilícito.

**Manipulación**: una fotografía de un pasillo de este tipo podría tener como objetivo una cámara de vigilancia que muestra una imagen del espectro visible de un pasillo vacío. Un sensor de humo o incendio podría informar alguien que posea un mechero debajo de él. En cualquier caso, el dispositivo puede ser de técnicamente plena confianza hacia el sistema, pero se presentará información manipulada.

**Manipulación**: un atacante puede aprovechar material de clave extraído para interceptar y suprimir los datos desde el dispositivo en la ruta de comunicación y reemplazarlo por datos falsos que se autentican con el material de clave robado.

**Manipulación**: un atacante puede parcial o totalmente reemplazar el software que se ejecuta en el dispositivo, podría permitir el software reemplazado aprovechar la identidad original del dispositivo si el material de clave o las funciones criptográficas manteniendo los materiales clave estaban disponibles para el programa ilícito.
   
**Revelación de información**: si el dispositivo está ejecutando software manipulado, dicho software manipulado puede provocar pérdidas de datos a personas no autorizadas.

**Revelación de información**: un atacante puede aprovechar material clave extraído para inyectar a sí mismo en la ruta de comunicación entre el dispositivo y un controlador o el campo puerta de enlace o gateway de nube con sifón la información.

**Denegación de servicio**: puede que el dispositivo esté desactivado o convertido en un modo donde la comunicación no es posible (que es intencional en muchas máquinas industriales).

**Manipulación**: el dispositivo puede configurarse para funcionar en un estado desconocido para el sistema de control (fuera de los parámetros de calibrado conocidos) y, por tanto, proporcionar datos que se pueden interpretar

**La elevación de privilegios**: un dispositivo que realiza una función específica puede ser obligado a hacer algo más. Por ejemplo, puede inducir a una válvula que está programada para abrir a mitad de camino para abrir completamente.

**Denegación de servicio**: el dispositivo puede convertirse en un estado donde no es posible la comunicación.

**Manipulación**: el dispositivo puede configurarse para funcionar en un estado desconocido para el sistema de control (fuera de los parámetros de calibrado conocidos) y, por tanto, proporcionar datos que se pueden interpretar.
 
**Alteración o suplantación de repudio**: si no protegidos (que raramente es el caso de los controles remotos de consumidor) un atacante puede manipular el estado de un dispositivo de forma anónima. Un buen ejemplo es que los controles remotos que puede activar cualquier TV y que son herramientas populares bromista.

#### <a name="communication"></a>Comunicación

Amenazas alrededor de la ruta de comunicación entre dispositivos, dispositivos y puertas de enlace de campo y puerta de enlace de dispositivo y la nube. La siguiente tabla tiene alguna orientación alrededor de sockets abiertos en el dispositivo VPN:

| **Componente**               | **Amenaza** | **Mitigación**                                      | **Riesgo**                                                                                                      | **Implementación**                                                                                                                                                                                                                                                                                                                                                               |
|-----------------------------|------------|-----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dispositivo concentrador de IoT              | TID        | (D) TLS (PSK/RSA) para cifrar el tráfico             | Espionaje o interferir la comunicación entre el dispositivo y la puerta de enlace                             | Seguridad en el nivel de protocolo. Con protocolos personalizados, necesitamos saber cómo protegerlos. En la mayoría de los casos, la comunicación tiene lugar desde el dispositivo al concentrador IoT (dispositivo inicia la conexión).                                                                                                                                                                 |
| Dispositivo de               | TID        | (D) TLS (PSK/RSA) para cifrar el tráfico.            | Lectura de datos en tránsito entre dispositivos. Manipulación de los datos. Sobrecarga del dispositivo con nuevas conexiones | Seguridad en el nivel de protocolo (MQTT, AMQP, HTTP/CoAP. Con protocolos personalizados, necesitamos saber cómo protegerlos. La reducción de la amenaza de DoS es del mismo nivel de dispositivos a través de una puerta de enlace de nube o campo y tiene el Acta única como clientes hacia la red. La interconexión puede resultar en una conexión directa entre los interlocutores después de haber sido negociada por la puerta de enlace |
| Dispositivo de entidad externa      | TID        | Emparejamiento de seguro de la entidad externa al dispositivo | Interceptación de la conexión con el dispositivo. Interferir la comunicación con el dispositivo                     | Emparejamiento de forma segura la entidad externa al dispositivo Bluetooth/CNC LE. Controlar el panel operacional del dispositivo (físico)                                                                                                                                                                                                                                                  |
| Puerta de enlace de nube de puerta de enlace de campo | TID        | TLS (PSK/RSA) para cifrar el tráfico.               | Espionaje o interferir la comunicación entre el dispositivo y la puerta de enlace                             | Seguridad en el nivel de protocolo (MQTT, AMQP, HTTP/CoAP). Con protocolos personalizados, necesitamos saber cómo protegerlos.                                                                                                                                                                                                                                                       |
| Dispositivo puerta de enlace de nube        | TID        | TLS (PSK/RSA) para cifrar el tráfico.               | Espionaje o interferir la comunicación entre el dispositivo y la puerta de enlace                             | Seguridad en el nivel de protocolo (MQTT, AMQP, HTTP/CoAP). Con protocolos personalizados, necesitamos saber cómo protegerlos.                                                                                                                                                                                                                                                       |

A continuación presentamos algunos ejemplos de amenazas de esta categoría:

**Denegación de servicio**: los dispositivos limitados suelen ser amenazados DoS cuando escucha activamente para las conexiones entrantes o datagramas no solicitados en una red, ya que un atacante puede abrir muchas conexiones en paralelo y no dan servicio o dan servicio muy lentamente o el dispositivo puede ser desbordado por el tráfico no solicitado. En ambos casos, el dispositivo puede eficazmente representará no funciona en la red.

**Suplantación de identidad, revelación de información**: los dispositivos limitados y dispositivos especiales suelen tienen instalaciones de seguridad uno para todos como protección de PIN o contraseña o enteramente confían en que confía en la red, lo que significa que otorga acceso a la información cuando un dispositivo está en la misma red y dicha red a menudo sólo está protegida por una clave compartida. Esto significa que cuando se revela el secreto compartido de red o dispositivo, es posible controlar el dispositivo u observar los datos emitidos desde el dispositivo.  

**Suplantación**: un atacante puede interceptar o parcialmente reemplazar la difusión y suplantar el originador (hombre en el medio)

**Manipulación**: un atacante puede interceptar o parcialmente reemplazar la difusión y enviar información falsa 

**La divulgación de información:** un atacante puede interceptar una difusión y obtener información sin autorización **de denegación de servicio:** un intruso atasco de la señal de difusión y denegar la distribución de la información

#### <a name="storage"></a>Almacenamiento de información

Cada puerta de enlace de dispositivo y el campo tiene algún tipo de almacenamiento (temporal de Queue Server los datos, el almacenamiento de imágenes del sistema operativo).

| **Componente**                            | **Amenaza** | **Mitigación**                       | **Riesgo**                                                                                                                                                                                                                                                                                                                | **Implementación**                                                                                                                                                     |
|------------------------------------------|------------|--------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Almacenamiento de información de dispositivo                           | TRID       | Encriptación de almacenamiento de información, firma los registros | Leer datos desde el almacenamiento de información (datos PII), manipulación de datos de telemetría. Alteración en la cola o comando control datos almacenados en caché. Manipulación de los paquetes de actualización de firmware o configuración mientras en caché o en la cola local puede dar lugar a componentes de sistema operativo o del sistema se ve comprometidos                                         | Cifrado, el código de autenticación de mensajes (MAC) o la firma digital. Donde el control de acceso seguro, posibles a través del acceso a los recursos controlar listas (ACL) o los permisos. |
| Imagen de sistema operativo del dispositivo                          | TRID       |                                      | Manipulación de SO / reemplazar los componentes de SO                                                                                                                                                                                                                                                                         | Partición del sistema operativo de sólo lectura, firmado de imagen del sistema operativo, cifrado                                                                                                                    |
| Almacenamiento de información de puerta de enlace de campo (Queue los datos) | TRID       | Encriptación de almacenamiento de información, firma los registros | Leer datos desde el almacenamiento de información (datos PII), manipulación de datos de telemetría, alteración en la cola o comando control datos almacenados en caché. Manipulación de paquetes de actualización de firmware o configuración (destinados a dispositivos o puerta de enlace de campo) mientras en caché o en la cola local puede dar lugar a componentes de sistema operativo o del sistema se ve comprometidos | BitLocker                                                                                                                                                              |
| Imagen del sistema operativo de puerta de enlace de campo                   | TRID       |                                      | Manipulación de SO / reemplazar los componentes de SO                                                                                                                                                                                                                                                                          | Partición del sistema operativo de sólo lectura, firmado de imagen del sistema operativo, cifrado                                                                                                                    |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Zona de puerta de enlace de procesamiento/nube dispositivos y eventos

Una puerta de enlace de nube es el sistema que permite la comunicación remota desde y hacia dispositivos o puertas de enlace de campo desde varios sitios diferentes a través del espacio de la red pública, normalmente hacia un control basado en la nube y el sistema de análisis de datos, una federación de tales sistemas. En algunos casos, una puerta de enlace de nube puede inmediatamente facilitan el acceso a los dispositivos especiales de terminales como tabletas o teléfonos. En el contexto que se tratan aquí, "nube" sirve para hacer referencia a un sistema de procesamiento de datos dedicado que no está enlazado al mismo sitio que los dispositivos conectados o puertas de enlace de campo y en evitar medidas operativas acceso físico de destino pero no necesariamente a una infraestructura de "nube pública".  Una puerta de enlace de nube potencialmente puede asignarse en una superposición de virtualización de la red para aislar la puerta de enlace de la nube y todos sus dispositivos conectados o puertas de enlace de campo desde cualquier otro tráfico de red. La puerta de enlace de nube es un sistema de control de dispositivo ni un procesamiento ni dispositivo de almacenamiento de datos del dispositivo; las instalaciones de la interfaz con la puerta de enlace de nube. La zona de puerta de enlace de nube incluye el gateway de nube junto con todas las puertas de enlace de campo y dispositivos conectados directa o indirectamente a ella.

Puerta de enlace de nube es principalmente una pieza integrada personalizada de software que se ejecuta como un servicio con los extremos expuestos al que conectan dispositivos y puerta de enlace de campo. Por lo tanto debe diseñarse teniendo en cuenta la seguridad. Siga el proceso [SDL](http://www.microsoft.com/sdl) para diseñar y crear este servicio. 

#### <a name="services-zone"></a>Zona de servicios

Un sistema de control (o controlador) es una solución de software que se conecta a un dispositivo o una puerta de enlace de campo o puerta de enlace de nube para el control de uno o varios dispositivos o para recopilar, almacenar o analizar los datos del dispositivo para fines de control posterior o presentación. Sistemas de control son las únicas entidades en el ámbito de esta discusión que inmediatamente se puede facilitar la interacción con personas. La excepción son superficies de control físicas intermedias en dispositivos, como un conmutador que permite a una persona desactivar el dispositivo o cambiar otras propiedades, y de que no hay ningún equivalente funcional que se puede acceder digitalmente. 

Superficies de control físicas intermedias son aquellos donde algún tipo de administración lógica restringe la función de la superficie física que puede iniciarse remotamente una función equivalente o puede evitarse la entrada está en conflicto con entrada remota – estas superficies de control depositadas son conceptualmente conectado a un sistema de control local que aprovecha la misma funcionalidad subyacente como cualquier otro sistema de control remoto que se pueden conectar a los dispositivos en paralelo. Amenazas principales de la informática en nube puede leerse en la página de [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/research/top-threats/) .


## <a name="additional-resources"></a>Recursos adicionales

Consulte los siguientes artículos para obtener información adicional:

- [Herramienta de modelado de amenazas SDL](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
- [Arquitectura de referencia de Microsoft Azure IoT](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
 
