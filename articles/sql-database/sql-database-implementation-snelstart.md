<properties
   pageTitle="Base de datos de SQL Azure Azure caso práctico - Snelstart | Microsoft Azure"
   description="Obtenga más información sobre cómo SnelStart usa la base de datos SQL expandir rápidamente sus servicios de empresa a una velocidad de 1.000 bases de SQL Azure nuevo al mes"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/08/2016"
   ms.author="carlrab"/>

# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Con Azure, SnelStart ha ampliado rápidamente sus servicios de empresa a una velocidad de 1.000 bases de SQL Azure nuevo por mes.

![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart hace popular financiera y empresarial-software de administración para pequeñas y medianas empresas (SMB) en los países bajos. Sus 55.000 clientes son atendidas por un personal de los 110 empleados, incluyendo personal de TI de 35. Moviendo de software de escritorio a un software-como-servicio (SaaS) oferta integrada en Azure, SnelStart realizan la mayoría de servicios integrados, automatizar la administración mediante el entorno familiar de C# y optimización del rendimiento y escalabilidad ni empresas sobre o debajo de aprovisionamiento usando grupos de elásticos de la base de datos. Uso de Azure proporciona SnelStart los cambiantes de los clientes móviles entre local y la nube.

> [AZURE.VIDEO azure-sql-database-case-study-snelstart]

##<a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>¿Por qué SnelStart servicios ampliados desde el escritorio en la nube

> "Trabajar con Azure significa que podemos ofrecer software más rápido, rápidamente ante las peticiones de cliente y escalar soluciones cuando demandas aumentan".

> — Henry sido, arquitecto de Software

SnelStart ha encontrado una empresa de software correcta de años, con un modelo de desarrollo tradicional: código, empaquetar, enviar y repetir. Con el tiempo, el ritmo de cambio aumentaron con mayor rapidez y más rápido. Reglamentaciones cambian con frecuencia, y los clientes necesarias formas más fácil de procesar registros financieros y colaborar con sus contables y la administración pública para estar al día con los cambios.

"Envío software para clientes costosas y limitación,"según fue Henry sido, arquitecto de software. "Producción, embalaje y los gastos de envío limitada con qué frecuencia podríamos lanzamiento software. Tenemos a actualizaciones de paquete para envíos periódicos, pero que se les hizo difícil satisfacer las necesidades de nuestros clientes. Nos no podemos nunca asegurarse de que nuestros clientes actualizados a la última versión del producto. Por lo tanto, hemos tenido que admiten varias versiones, realizar el trabajo de soporte técnico muy difícil bien."

Sido agrega, "queremos una forma de programar y lanzar actualizaciones en un acelerado pace, por lo que podemos innovar más rápido y crear nuevos servicios para nuestros clientes. Nos también desean una manera de automatizar procesos más para simplificar las necesidades de administración de la empresa de nuestros clientes."

Para SnelStart, la solución fue ampliar sus servicios al convertirse en un proveedor de SaaS basado en la nube. La plataforma de base de datos de SQL Azure contribuido SnelStart acceder a ellos sin la carga de TI principal que hubiera requiere una solución de infraestructura como-servicio (IaaS).

El modelo de nube también permite SnelStart corregir los errores y proporcionar características nuevas rápidamente, sin necesidad de descargar y actualizar el software de clientes. Función sido, "Using Azure cloud services nos permite rápidamente actuar cambiar requisitos de terceros. En lugar de enviar una nueva versión a miles de clientes, podemos adaptar la información enviada desde nuestra aplicación de escritorio a nuevos formatos requeridos por proveedores de terceros."

##<a name="a-modern-company-with-traditional-roots"></a>Una empresa moderna con raíces tradicionales

SnelStart es una empresa moderna, ágil alta tecnología con raíces humilde citas a 1964, cuando los fundadores inicia la empresa como fabricante de las partes de instrumentos musicales. El centro de la empresa de software de SnelStart había iniciado realmente le gana en la década de 1980 durante la proliferación del equipo. La empresa necesitaba una alternativa mejor que el software de contabilidad disponible en el momento para tardó asuntos en sus propias manos. En 1982, crea la base de lo que se vuelvan SnelStart software de contabilidad de la empresa. Desde el principio, el software se ha terminado de su velocidad y simplificar, tanto para que la compañía finalmente cambiado el foco y reinventado en una compañía de software.

En 1995, SnelStart había publicado su primera aplicación de contabilidad para Windows. La aplicación, integrada en bases de datos de Microsoft Visual Basic 1.0 y Microsoft Access, contribuido a aumentar al cliente base a más de 5.000 usuarios.

En la actualidad, SnelStart es un proveedor importante de una línea de negocio (LOB) y la aplicación de administración de la empresa destinadas a SMB neerlandés y emprendedores propia. Como dice Carlo Kuip, arquitecto de TI, "nuestro objetivo es proporcionar automatización de 100 por cien de servicios de administración de la empresa para nuestros clientes".

##<a name="optimizing-performance-and-cost-with-elastic-pools"></a>Optimizar el rendimiento y el costo con agrupaciones elásticas

SnelStart era un gran escala pionero de grupos de elásticos de la base de datos. Grupos elásticos ayudar a la compañía limitar los costos y administrar los requisitos de rendimiento de forma más eficaz. Función sido, "usando grupos de elásticos de la base de datos, podemos ayudarle a mejorar rendimiento según las necesidades de los clientes, sin aprovisionamiento en exceso. Si se tenía proporcionando en función de la carga de recursos asignadas, sería muy costosa. En su lugar, la opción para compartir recursos entre varias bases de datos de uso bajo nos permite crear una solución que funciona bien y rentable. "

##<a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Ayuda de bases de datos de SQL Azure containerize datos de aislamiento y seguridad 

Base de datos de SQL Azure permite SnelStart fácilmente y transparente mover los clientes datos de administración de la empresa en Azure local. La base de datos de SQL Azure es un contenedor conveniente que proporciona aislamiento, un límite de autenticación, autorización y fáciles capacidades de copia de seguridad y restauración. Bases de datos proporcionan un modelo de conceptual adecuado para la administración de la empresa. Según Carlo Kuip, arquitecto de TI "elementos dentro de este límite de contenedor contienen datos confidenciales que es fundamentales para su empresa y almacenar dichos elementos en una base de datos aislado mantiene bien protegido. Podemos administrar autorización en el nivel de base de datos e incluso automatizar la administración y el escalado de estas bases de datos sin necesidad de base de datos administradores en personal."

Almacén de datos de SQL Azure también desempeña una función en el artículo SnelStart, seguridad y administración de empresa a recopilar datos de telemetría, como detección de intrusiones, el registro de actividad de usuario y la conectividad.

##<a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure sobrecarga quita de modo que los desarrolladores pueden pasar más tiempo ofrecer valor 

El modelo de la plataforma Windows Azure había quitado sobrecarga de infraestructura y habilitado SnelStart automatizar las implementaciones con las bibliotecas de administración de C#. Como se indica Kuip, "pudimos creciendo nuestras operaciones actuales con un personal muy pequeño mientras simultáneamente aumentar las opciones de recuperación de desastres, velocidad y escalabilidad de nuestros clientes. MAYÚS al desarrollo de servicios liberada los recursos para centrarse en los nuevos servicios y características, en lugar de simplemente actualizar código existente para mantener el ritmo con las nuevas normas o códigos de impuestos." Agrega "mediante la automatización de administración y el uso de la oferta, podemos ofrecer más valor para nuestros clientes sin tener que realizar grandes inversiones en personal de operaciones de SaaS." Por ejemplo, mediante el uso de grupos de la base de datos de Azure y elástica, SnelStart pudo agregar una gran variedad de nuevas características, incluida la integración de datos del cliente más sólida con bancos, facturación nuevos servicios, comprobaciones de fondo de pequeñas empresas y servicios de correo electrónico.

> "En solo los primeros meses de 2016, ampliamos nuestras implementaciones de base de datos de SQL Azure desde unos 5.500 a más de 12.000 y actualmente agregamos aproximadamente 1.000 bases de datos al mes".

> — Henry sido, arquitecto de Software

Administración de la base de datos está automatizado aún más mediante la característica de trabajos elástico. Como se indica Kuip, "altamente agradecemos la detección automática de bases de datos en una instancia del DB de SQL [servidor]." Esto permite SnelStart ejecutar las operaciones de administración a través de sus clientes aumenta dinámicamente sin sobrecarga adicional.

SnelStart también es desarrollar una API que actúa como intermediario entre los datos del cliente y aplicaciones creadas por socios de software de terceros. Como Estados Kuip, "esta API permitirá otros proveedores agregar funcionalidad a nuestro software, como la eliminación de la entrada de datos para las facturas y otros documentos." Los clientes ya no tendrán que escribir manualmente las facturas en su software de contabilidad de pequeñas empresas. el software de SnelStart exchange directamente a la información necesaria. Esto permite a los clientes para unirse a sus tareas de administración de la empresa con el ecosistema de información que surgen de transformación digital de la industria.  

##<a name="how-azure-services-enable-saas-for-snelstart"></a>Cómo los servicios de Azure permiten SaaS para SnelStart

Mediante el uso de Azure, SnelStart puede servir de sus clientes y sus contables más sin problemas en la nube. Por ejemplo, los clientes y contables puedan compartir información accediendo directamente a la API del cliente de SnelStart hospedado en Azure. Estados de Kuip, "estos servicios reutilizables están disponibles a nuestras aplicaciones web de orientados al cliente y proporcionan infraestructura y funciones para permitir el acceso a la administración de la empresa para los clientes y al software de terceros usados por nuestros clientes comunes. Ejemplos que proporciona funciones de configuración de productos, administración de las reglas de firewall y administración de procesos de ejecución larga como copias de seguridad."

> Nuestro objetivo es proporcionar automatización de 100 por cien de servicios de administración de la empresa para nuestros clientes." 

> : Carlo Kuip, arquitecto de TI

Además, los servicios web SnelStart permiten clientes y contables acceder fácilmente a los datos en grupos elásticos de base de datos de SQL Azure. En este modelo SaaS, junto con la elasticidad de la base de datos y el Administrador de recursos de Azure proporciona SnelStart con las características de escalabilidad que completan cada implementación de Azure. La implementación totalmente automatizada mediante las bibliotecas de administración de C#.

![Arquitectura de SnelStart](./media/sql-database-implementation-snelstart/figure1.png)

Figura 1. A partir de junio de 2016, SnelStart tiene más de 11.000 bases de datos y grupos de más de 50 elásticos de la base de datos
 
##<a name="simplicity-from-the-cloud"></a>Simplificar de la nube

Desde el cambio a una solución basada en nube Azure, SnelStart ha sido capaz de crecimiento del cliente rápida mientras ofrece características innovadoras y servicios de soporte técnico. Función sido, "con Azure, podemos ofrecemos actualizaciones casi continua para nuestros clientes, sin expandir nuestro personal de operaciones. Y obtenemos todas las demás excelentes Azure características, como la recuperación de desastres y escalabilidad: agrupados en. "

> "Cuando se fueron realmente sobre en Redmond... He recibido una llamada de desarrollador en los países bajos, llamar a mí sobre un problema concreto. Hemos podido obtener Microsoft para ofrecer un cambio en su entorno de producción en 48 horas para solucionar el problema."

> — Henry sido, arquitecto de Software

SnelStart también se aprecia la sólida asociación que han desarrollado con el equipo de base de datos de SQL de Microsoft Azure. Como los Estados de Kuip, "tenemos discusiones en características y cómo usar la tecnología, lo cual es agradecemos a ambos lados".
El objetivo inmediato para SnelStart es siguen creciendo a sus clientes satisfechos base. Como ha indica que "Sin las limitaciones técnicas y recursos que tenemos como fabricantes independientes de software, hay sin límite de cuánto podemos ampliar."


## <a name="more-information"></a>Más información

- Para obtener más información acerca de los grupos de la base de datos Azure elástico, vea [grupos de elásticos de la base de datos](sql-database-elastic-pool.md).

- Para obtener más información sobre los roles de Web y roles de trabajo, vea [funciones de trabajo](../fundamentals-introduction-to-azure.md#compute). 

- Para obtener más información sobre el almacenamiento de datos de SQL Azure, consulte el [Almacén de datos SQL](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)

- Para obtener más información sobre SnelStart, consulte [SnelStart](http://www.snelstart.nl).


