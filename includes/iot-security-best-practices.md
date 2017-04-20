# <a name="internet-of-things-security-best-practices"></a>Recomendaciones de seguridad de Internet de las cosas

Para proteger un Internet de las cosas (IoT) infraestructura requiere una estrategia de seguridad en profundidad rigurosa. Esta estrategia requiere que proteger los datos en la nube, proteger la integridad de los datos mientras estaba en tránsito por la internet pública y suministrar dispositivos de forma segura. Cada capa construye una mayor garantía de seguridad en toda la infraestructura.

## <a name="secure-an-iot-infrastructure"></a>Asegurar una infraestructura IoT

Esta estrategia de seguridad en profundidad se puede desarrollar y ejecutar con la participación activa de los distintos actores involucrados con la fabricación, el desarrollo y la implementación de dispositivos IoT e infraestructura. Siguiente es una descripción general de estos reproductores.  

- **Fabricante/integrador del hardware IoT**: normalmente, son los fabricantes de hardware IoT implementado, integradores de ensamblar el hardware de distintos fabricantes o proveedores que el hardware para una implementación de IoT fabricado o integrado por otros proveedores.
- **Desarrollador de soluciones IoT**: el desarrollo de una solución de IoT se realiza normalmente por un programador de soluciones. Este desarrollador puede parte de un equipo interno o un integrador de sistemas (SI) especializado en esta actividad. El desarrollador de soluciones IoT puede desarrollar diversos componentes de la solución IoT desde el principio, integrar diversos componentes de código abierto o estándar o adoptar soluciones preconfiguradas con adaptación secundaria.
- **Implementador de IoT solución**: tras IoT una solución se desarrolla, es necesario que se implementen en el campo. Esto implica la implementación de hardware, interconexión de dispositivos y la implementación de soluciones de dispositivos de hardware o la nube.
- **Operador de IoT solución**: tras IoT la solución se implementa, requiere supervisión, upgrades y mantenimiento de operaciones a largo plazo. Esto puede hacerse por un equipo interno que comprende especialistas de tecnología de la información, las operaciones de hardware y equipos de mantenimiento y especialistas en dominio que supervisar el comportamiento correcto de la infraestructura general de IoT.

Las secciones siguientes proporcionan las mejores prácticas para cada uno de estos reproductores para ayudar a desarrollar, implementar y operar una infraestructura segura de IoT.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricante/integrador del hardware IoT

Éstas son las recomendaciones para los fabricantes de hardware IoT e integradores de hardware.

- **Requisitos mínimos de hardware ámbito**: el diseño de hardware debe incluir las características mínimo necesarias para el funcionamiento del hardware y nada más. Un ejemplo es incluir puertos USB sólo si es necesario para el funcionamiento del dispositivo. Estas características adicionales de abren el dispositivo de los vectores de ataque no deseados que deben evitarse.
- **Asegúrese de hardware a prueba de manipulaciones**: generación de mecanismos para detectar la manipulación física, como la apertura de la tapa del dispositivo o quitar una parte del dispositivo. Estas señales de manipulación puede ser parte de la secuencia de datos que se cargan en la nube, en el que se avisa a los operadores de estos eventos.
- **Generar alrededor de hardware seguro**: permite si CV, generar características de seguridad como un almacenamiento seguro y cifrado o arrancar la funcionalidad basada en el módulo de plataforma segura (TPM). Estas características hacen de dispositivos más seguro y protegen la infraestructura general de IoT.
- **Realizar las actualizaciones de seguridad**: actualizaciones de Firmware durante la vida útil del dispositivo son inevitables. Creación de dispositivos con rutas de acceso seguras para upgrades y aseguramiento criptográfico de versiones de firmware permitirá que el dispositivo esté seguro durante y después de las actualizaciones.

## <a name="iot-solution-developer"></a>Desarrollador de soluciones IoT

Los siguientes son los procedimientos recomendados para los programadores de soluciones de IoT:

- **Metodología de desarrollo de software seguro de seguimiento**: desarrollo de software seguro requiere principio a pensar acerca de la seguridad, desde el inicio del proyecto hasta su implementación, pruebas e implementación. Las opciones de plataformas, lenguajes y herramientas están influenciadas con esta metodología. El ciclo de vida de desarrollo de seguridad de Microsoft proporciona un enfoque paso a paso para la creación de software seguro.
- **Elegir el software de código abierto con cuidado**: software de código abierto proporciona la oportunidad de desarrollar rápidamente soluciones. Al elegir el software de código abierto, tenga en cuenta el nivel de actividad de la Comunidad para cada componente de código abierto. Una activa comunidad garantiza que el software es compatible y que los problemas son descubiertos y dirigidos. Alternativamente, un software de código abierto oscura e inactivo podría no ser compatible y probablemente no se detectarán problemas.
- **Integrar con cuidado**: existen muchos errores de seguridad de software en el límite de API y las bibliotecas. Funcionalidad que puede no ser necesaria para la implementación actual aún podría estar disponible a través de un nivel de API. Para garantizar la seguridad general, asegúrese de comprobar todas las interfaces de los componentes está integrados para defectos de seguridad.      

## <a name="iot-solution-deployer"></a>Implementador de solución IoT

Las siguientes son recomendaciones para implementadores de solución IoT:

- **Implementar hardware de forma segura**: IoT implementaciones pueden requerir hardware para implementar en ubicaciones no seguras, como en espacios públicos o sin supervisión de configuraciones regionales. En tales situaciones, asegúrese de que la implementación de hardware es a prueba de manipulaciones en la medida máxima. Si USB u otros puertos están disponibles en el hardware, asegúrese de que se tratarán en forma segura. Pueden utilizar muchos de los vectores de ataque como puntos de entrada.
- **Proteger claves de autenticación**: durante la implementación, cada dispositivo requiere ID y asociados claves de autenticación generadas por el servicio de nube. Mantener estas claves físicamente seguros incluso después de la implementación. Puede utilizarse cualquier clave comprometida por un dispositivo malintencionado haga pasar por un dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solución IoT

Éstas son las recomendaciones para operadores de solución IoT:

- **Mantener el sistema actualizado**: asegúrese de que todos los controladores de dispositivos y sistemas operativos de dispositivos se actualizan a las versiones más recientes. Si activa Actualizaciones automáticas en Windows 10 (IoT u otros SKU), Microsoft mantiene actualizados, que proporciona un sistema operativo seguro para dispositivos IoT. Mantener otros sistemas operativos (como Linux) actualizado garantiza que también están protegidos frente a ataques malintencionados.
- **Proteger contra la actividad malintencionada**: si lo permite el sistema operativo, instale las últimas capacidades antivirus y antimalware en cada sistema operativo del dispositivo. Esto puede ayudar a mitigar las amenazas más externas. Tomen las medidas adecuadas para proteger los sistemas operativos más modernos frente a las amenazas.
- **Auditoría con frecuencia**: infraestructura de auditoría IoT para problemas relacionados con la seguridad es clave al responder a incidentes de seguridad. Mayoría de sistemas operativos proporcionan el registro de sucesos integrados que se debe revisar con frecuencia para asegurarse de que no se ha producido ninguna infracción de seguridad. Información de auditoría puede enviarse como una secuencia independiente de telemetría al servicio de nube donde pueden analizarse.
- **Proteger físicamente la infraestructura IoT**: se inician los peores ataques de seguridad contra infraestructura IoT con acceso físico a los dispositivos. Es una práctica de seguridad importante proteger contra el uso malintencionado de los puertos USB y otro acceso físico. Una clave para descubrir las infracciones que se hayan producido está registrando de acceso físico, como el uso del puerto USB. De nuevo, Windows 10 (IoT y otros SKU) permite un registro detallado de estos eventos.
- **Credenciales de nube proteger**: credenciales de autenticación de nube utilizadas para configurar y utilizar una implementación IoT posiblemente son la forma más fácil de acceder y poner en peligro un sistema IoT. Proteger las credenciales si cambia la contraseña con frecuencia y abstenerse de utilizar estas credenciales en equipos públicos.

Capacidades de diferentes dispositivos IoT varían. Algunos dispositivos pueden ser equipos que ejecutan sistemas operativos de escritorio comunes y algunos dispositivos podrían estar ejecutando sistemas operativos de muy ligero. Las prácticas recomendadas de seguridad descritas anteriormente pueden ser aplicables a estos dispositivos en distintos grados. Si se proporciona, seguridad e implementación recomendaciones adicionales de los fabricantes de estos dispositivos deben seguirse.

Posible que algunos dispositivos heredados y restringidas no se hayan diseñado específicamente para la implementación de IoT. Estos dispositivos podrían carecer de la capacidad de cifrar datos, conectar con Internet o proporcionar auditoría avanzada. En estos casos, una puerta de enlace de campo moderna y segura puede agregar datos de dispositivos heredados y proporcionar la seguridad necesaria para conectar estos dispositivos a través de Internet. Puertas de enlace de campo pueden proporcionar autenticación segura, la negociación de sesiones cifradas, recepción de comandos desde la nube y muchas otras características de seguridad.
