<properties
    pageTitle="Modelo de aplicación Web de múltiples arrendatarios | Microsoft Azure"
    description="Buscar información general de arquitectura y patrones de diseño que describen cómo implementar una aplicación web de varios inquilinos de Azure."
    services=""
    documentationCenter=".net"
    authors="wadepickett" 
    manager="wpickett"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/05/2015"
    ms.author="wpickett"/>

# <a name="multitenant-applications-in-azure"></a>Aplicaciones multiempresa en Azure

Una aplicación multiempresa es un recurso compartido que permite a usuarios independientes, o "inquilinos," para ver la aplicación como si fuese su propio. Un escenario típico que permite a una aplicación multiempresa es uno en el que todos los usuarios de la aplicación que desee personalizar la experiencia del usuario, pero en caso contrario, tiene los mismos requisitos empresariales básicas. Ejemplos de aplicaciones multiempresa grandes son Office 365, Outlook.com y visualstudio.com.

Desde la perspectiva del desarrollador de una aplicación, las ventajas de varias empresas principalmente se relacionan con eficacia operativa y costo. Una versión de la aplicación puede satisfacer las necesidades de muchos inquilinos o clientes, lo que permite la consolidación de sistema tareas de administración como supervisar, ajustar el rendimiento, mantenimiento del software y las copias de seguridad de datos.

El siguiente proporciona una lista de los objetivos y los requisitos desde la perspectiva del proveedor más significativo.

- **Aprovisionamiento**: debe poder aprovisionar inquilinos de nuevos para la aplicación.  Para obtener aplicaciones multiempresa con una gran cantidad de inquilinos, es normalmente es necesario automatizar este proceso habilitando el aprovisionamiento de autoservicio.
- **Mantenimiento**: debe poder actualizar la aplicación y realizar otras tareas de mantenimiento mientras utilizan varios inquilinos.
- **Supervisión**: debe poder supervisar la aplicación en todo momento para identificar los problemas y solucionarlos. Esto incluye la supervisión de cómo cada inquilino es mediante la aplicación.

Una aplicación de multiempresa correctamente implementada proporciona las siguientes ventajas a los usuarios.

- **Aislamiento**: las actividades de inquilinos individuales no afectan el uso de la aplicación otros inquilinos. Los inquilinos no pueden acceder a los datos de los demás. Parece que el inquilino como si tuvieran uso exclusivo de la aplicación.
- **Disponibilidad**: inquilinos individuales desea que la aplicación esté constantemente disponible, tal vez con garantías definidas en un SLA. De nuevo, las actividades de otras inquilinos no deben afectar a la disponibilidad de la aplicación.
- **Escalabilidad**: la aplicación se adapta para satisfacer la demanda de inquilinos individuales. La presencia y las acciones de otros inquilinos no deberían afectar al rendimiento de la aplicación.
- **Costos**: costos son menores que ejecute una aplicación dedicada, solo inquilino porque multiempresa permite el uso compartido de recursos.
- **Personalización**. La capacidad para personalizar la aplicación para un inquilino individual de varias formas, como agregar o quitar características, cambiar colores y logotipos o incluso agregar su propio código o script.

En resumen, si bien hay muchas consideraciones que debe tener en cuenta para proporcionar un servicio de gran escalabilidad, también hay un número de los objetivos y requisitos que sean comunes a muchas aplicaciones multiempresa. Algunos no puede ser importante en escenarios específicos, y la importancia de los objetivos individuales y requisitos variará en cada escenario. Como un proveedor de la aplicación multiempresa, también tendrá objetivos y los requisitos, como por ejemplo, los inquilinos objetivos y requisitos, rentabilidad, facturación, varios niveles de servicio, aprovisionamiento, supervisión de mantenimiento y automatización de la reunión.

Para obtener más información sobre otras consideraciones de diseño de una aplicación multiempresa, vea [aloja una aplicación de múltiples arrendatarios en Azure][]. Para obtener información sobre patrones de arquitectura de datos comunes de aplicaciones de base de datos de varios inquilinos software como servicio (SaaS), vea [Modelos de diseño para aplicaciones de SaaS inquilino múltiples con la base de datos de SQL Azure](./sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure proporciona muchas características que le permiten solucionar los problemas claves que se encuentran al diseñar un sistema multiempresa.

**Aislamiento**

- Sitio Web de segmento inquilinos por encabezados de Host, con o sin comunicación SSL
- Sitio Web de segmento inquilinos por parámetros de consulta
- Servicios Web en funciones de trabajo
    - Funciones de trabajo. que normalmente procesar datos en el servidor de una aplicación.
    - Funciones Web que suelen actúan como el front-end para las aplicaciones.

**Almacenamiento de información**

Administración de datos como base de datos de SQL Azure o el almacenamiento de Azure servicios como el servicio de la tabla que proporciona servicios de almacenamiento de grandes cantidades de datos no estructurados y el servicio de blobs de Windows que ofrece servicios para almacenar grandes cantidades de texto no estructurado o datos binarios como vídeo, audio e imágenes.

- Proteger Multitenant base de datos de SQL adecuado inicios de sesión de SQL Server por inquilino.
- Con tablas de Azure para aplicación recursos especificando una directiva de acceso de nivel de contenedor, puede que la posibilidad de ajustar los permisos sin tener que emitir nueva dirección URL para los recursos protegidos con firmas de acceso compartido.
- Azure colas para colas de Azure de recursos de aplicación se usan generalmente para procesamiento de unidad en nombre de los inquilinos, pero también pueden utilizarse para distribuir el trabajo necesario para aprovisionamiento o de administración.
- Colas de Bus de servicio para los recursos de la aplicación que inserta trabajar a un compartido un servicio, puede usar una sola cola donde cada remitente inquilino solo tenga permisos (como derivado de reclamaciones emitidos de ACS) para actualizar en cola, mientras que solo los receptores del servicio tienen permiso para extraer de la cola de los datos procedentes de varios inquilinos.


**Conexión y servicios de seguridad**

- Azure Bus de servicio, una infraestructura de mensajería que se encuentra entre las aplicaciones que les permite intercambiar mensajes de una forma de escala mejorada acoplamiento flexible y resistencia.

**Servicios de redes**

Azure proporciona varios servicios de red que admiten la autenticación y mejoran la capacidad de las aplicaciones hospedadas. Estos servicios incluyen las siguientes:

- Azure le permite de una red Virtual que aprovisionar y administrar redes privadas virtuales (VPN) en Azure así como vincularlas de forma segura con la infraestructura de TI en local.
- Administrador de tráfico de red virtual le permite cargar saldo el tráfico entrante en los distintos servicios de Azure hospedados si se están ejecutando en el mismo centro de datos o a través de los centros de datos distintos todo el mundo.
- Azure Active Directory (AD Azure) es un servicio moderna basada en el resto, que proporciona funciones de control de acceso y administración de identidades para las aplicaciones de la nube. Uso de Azure AD para recursos de la aplicación Azure AD a proporciona una forma sencilla de autenticación y autorización de usuarios para obtener acceso a sus aplicaciones web y servicios permitiendo que las características de autenticación y autorización para ser un factor de su código.
- Bus de servicios de Azure proporciona una mensajería segura y capacidad de flujo de datos para distribuir y aplicaciones híbridas, como la comunicación entre Azure alojado aplicaciones y local aplicaciones y servicios, sin necesidad de infraestructuras complejas de firewall y seguridad. Mediante retransmisión de Bus de servicio para los recursos de la aplicación a los servicios que se exponen como extremos puede pertenecer al inquilino (por ejemplo, hospedado fuera del sistema, como local), o pueden ser servicios aprovisionados específicamente para el inquilino (porque datos confidenciales específicas del inquilino viajan a través de ellos).



**Aprovisionamiento de recursos**

Azure proporciona a varias maneras de inquilinos nuevo de aprovisionamiento de la aplicación. Para obtener aplicaciones multiempresa con una gran cantidad de inquilinos, es normalmente es necesario automatizar este proceso habilitando el aprovisionamiento de autoservicio.

- Funciones de trabajo permiten aprovisionar y la disposición por inquilino recursos (por ejemplo, cuando un nuevo inquilino signos arriba o se cancela), recopilar métricas de medición usar y administrar escala siguiendo una programación determinada o en respuesta a la intersección de los umbrales de indicadores clave de rendimiento. Esta misma función también puede utilizarse para distribuir actualizaciones y actualizaciones de la solución.
- Azure BLOB puede utilizarse proporcionando cálculo o previamente inicializado paquetes, imágenes de disco duro virtual y otros recursos de servicio de recursos de almacenamiento para los inquilinos nuevos mientras proporciona directivas de acceso de nivel de contenedor proteger el cálculo.
- Opciones de recursos de base de datos SQL de aprovisionamiento de un inquilino incluyen:

    -   DDL en secuencias de comandos o incrustados como recursos en ensamblados
    -   SQL Server 2008 R2 CAD los paquetes implementado mediante programación.
    -   Copiar una base de datos de referencia principales
    -   Uso de la base de datos de importación y exportación proporcionando nuevas bases de datos desde un archivo.



<!--links-->

[Hospedar una aplicación de varios inquilino de Azure]: http://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: http://msdn.microsoft.com/library/windowsazure/hh689716
