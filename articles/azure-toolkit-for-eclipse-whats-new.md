<properties
    pageTitle="Novedades en el Kit de herramientas de Azure para Eclipse"
    description="Obtenga información sobre las características más recientes en el Kit de herramientas de Azure para Eclipse."
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
    ms.date="08/26/2016" 
    ms.author="robmcm;asirveda;martinsawicki"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh694270.aspx -->

# <a name="whats-new-in-the-azure-toolkit-for-eclipse"></a>Novedades en el Kit de herramientas de Azure para Eclipse

## <a name="azure-toolkit-for-eclipse-releases"></a>Kit de herramientas de Azure Eclipse lanzamientos

Este artículo contiene información sobre las varias versiones y las actualizaciones más recientes para el Kit de herramientas de Azure para Eclipse.

> [AZURE.NOTE] También es un Kit de herramientas de Azure para el IDE IntelliJ. Para obtener más información, vea el [Kit de herramientas de Azure para IntelliJ].

### <a name="august-26-2016"></a>26 de agosto de 2016

El Kit de herramientas de Azure para Eclipse - versión de 2016 agosto incluye las siguientes mejoras:

* **Distribuciones de JDK personalizado**. El Kit de herramientas de Azure para Eclipse admite especificar e implementar una versión JDK arbitraria al contenedor de la aplicación Web de Azure:
  - Además de JDKs proporcionados por Azure, también puede elegir entre una gran variedad de versiones de OpenJDK Zulú ofrecerse en Azure con los sistemas de Azul.
  - También puede especificar su propia distribución JDK si carga como un archivo ZIP a su cuenta de almacenamiento.
* **Mejoras en la vista del explorador de Azure**:
  - Soporte técnico para la administración de la máquina Virtual con el nuevo modelo de administrador de recursos de Azure: lista, crear y eliminar máquinas virtuales basados en el Administrador de recursos sin salir del IDE.
  - Soporte técnico para la administración de cuenta de almacenamiento blob mediante el Administrador de recursos de Azure, que complementa la funcionalidad de administración de cuentas de almacenamiento "clásico" existente.
* **Controlador de JDBC de Microsoft 6.0 para SQL Server**. Esta actualización incluye el controlador JDBC más reciente de Microsoft SQL Server (6.0), que es ahora incluyen como una biblioteca que puede agregar fácilmente a los proyectos de Java, reemplazar, con ello, la versión más antigua.

### <a name="june-29-2016"></a>29 de junio de 2016

El Kit de herramientas de Azure para Eclipse - versión de junio de 2016 incluye las siguientes mejoras:

* **Requisito de Java 8**. El Kit de herramientas de Azure para Eclipse ahora requiere Java 8, aunque este requisito es solo para el Kit de herramientas: las aplicaciones pueden seguir usando todas las versiones de Java que son compatibles con Azure.
* **Compatibilidad con la última JDKs Java**. Las últimas versiones de la JDKs Java ahora son compatibles con el Kit de herramientas de Azure para Eclipse.
* **Compatibilidad con Azure SDK v2.9.1**. La última versión del SDK de Azure ahora es el requisito previo mínimo para el Kit de herramientas de Azure para Eclipse.
* **Ejemplos de integrado**. Ahora, el Kit de herramientas de Azure para Eclipse características varias aplicaciones de ejemplo para ayudar a los desarrolladores a empezar.
* **Integración de herramientas de HDInsight**. Herramientas de HDInsight de Azure ahora están incluidos con el Kit de herramientas de Azure para Eclipse. Para obtener más información, vea [HDInsight de complemento de herramientas para Eclipse].
* **Depuración de aplicaciones Web de Java remota**. El Kit de herramientas de Azure para Eclipse ahora es compatible con la depuración remota de aplicaciones web de Java en la aplicación de servicio de Azure.
* **Compatibilidad con la versión de Eclipse Luna.** La nueva versión mínima de IDE Eclipse de necesaria es Luna.

### <a name="april-12-2016"></a>12 de abril de 2016

El Kit de herramientas de Azure para Eclipse - versión de 2016 de abril incluye las siguientes mejoras:

* **Compatibilidad con Azure SDK v2.9.0**. La última versión del SDK de Azure ahora es el requisito previo mínimo para el Kit de herramientas de Azure para Eclipse.
* **Uso vario, mejoras de rendimiento y la capacidad de respuesta relacionadas con el soporte técnico de la aplicación Web de Azure**. Un número de optimización del rendimiento en cómo el Kit de herramientas se comunica con Azure resultado en una interfaz de usuario más rápida.
* **Capacidad para eliminar un contenedor de la aplicación Web existente en Azure desde dentro de Eclipse**. El Kit de herramientas de Azure para Eclipse permite eliminar un contenedor de Azure Web existente sin salir de Eclipse.

### <a name="march-7-2016"></a>7 de marzo de 2016

El Kit de herramientas de Azure para Eclipse - versión de 2016 de marzo incluye las siguientes mejoras:

* **Compatibilidad con la implementación rápida de las aplicaciones de Java ligeras**. El Kit de herramientas de Azure para Eclipse ahora es compatible con la implementación rápida de las aplicaciones de Java ligeras en contenedores de aplicación Web de Azure, para implementar aplicaciones Java ahora tarda a segundos en lugar de minutos.
* **Soporte técnico para la administración de la aplicación Web mediante la vista del explorador de Azure**. Ahora admite la vista del explorador de Azure en el Kit de herramientas de lista, iniciar y detener aplicaciones Web de Azure.
* **Distribuciones Tomcat actualizado, embarcadero y Zulú OpenJDK**. El Kit de herramientas de Azure para Eclipse proporciona compatibilidad con versiones actualizadas de Tomcat, embarcadero y OpenJDK zulú para implementaciones de Java en servicios de nube de Azure.

### <a name="january-4-2016"></a>4 de enero de 2016

El Kit de herramientas de Azure para Eclipse - versión de enero de 2016 incluye las siguientes mejoras:

* **Compatibilidad con las actualizaciones de Zulú OpenJDK**. Para obtener más información, consulte la [página web de sistemas de Azul para el OpenJDK Zulú].
* **Actualizar Tomcat y distribuciones embarcadero**. Se han actualizado las distribuciones embarcadero y Tomcat que están disponibles en Microsoft Azure para su uso con el Kit de herramientas de Azure para Eclipse.
* **Característica de paridad entre Eclipse y herramientas de IntelliJ para Azure**. El Kit de herramientas de Azure para Eclipse y el [Kit de herramientas de Azure para IntelliJ] admite ahora el mismo conjunto de características.

### <a name="september-1-2015"></a>1 de septiembre de 2015

El Kit de herramientas de Azure para Eclipse - septiembre de 2015 versión incluye las siguientes mejoras:

* **Compatibilidad con las actualizaciones de Zulú OpenJDK**. Para obtener más información, consulte la [página web de sistemas de Azul para el OpenJDK Zulú].
* **Actualizar Tomcat y distribuciones embarcadero**. Se han actualizado las distribuciones embarcadero y Tomcat que están disponibles en Microsoft Azure para su uso con el Kit de herramientas de Azure para Eclipse. (Estas distribuciones permite a los desarrolladores crear desarrollo rápido y probar proyectos con el Kit de herramientas de Azure para Eclipse.
* **Compatibilidad con referencias de Tomcat y embarcadero actualizadas automáticamente**. Además de las versiones específicas de Tomcat y embarcadero que están disponibles en Azure, los desarrolladores ahora pueden hacer referencia a una distribución conocida como la "más recientes (actualización automática)", que actualizará automáticamente a la última distribución de cada versión principal de embarcadero o Tomcat la próxima vez que las instancias de la función son reciclados. (Reciclaje se produce automáticamente, pero los desarrolladores pueden activar manualmente un reciclaje a través del portal de Azure). Esta nueva característica significa que no tienen los desarrolladores a implementar su aplicación para que pueda tener el software de servidor actualizado. (
*  Esta funcionalidad está actualmente destinada solo con fines de pruebas y desarrollo o aplicaciones no críticas y no se recomienda para producción.)
* **Vista del explorador de azure BLOB, colas y tablas en el almacenamiento de Azure**. Esto permite a los desarrolladores realizar un conjunto de tareas comunes con sus artefactos almacenamiento directamente desde IDE Eclipse. Por ejemplo: eliminar, cargar o descargar BLOB.

### <a name="august-1-2015"></a>1 de agosto de 2015

El Kit de herramientas de Azure para Eclipse - agosto del de 2015 versión incluye las siguientes mejoras:

* **Administración de claves de aplicación perspectivas instrumentación**. Esta actualización le permite adquirir, crear y administrar las claves de instrumentación perspectivas de aplicación directamente desde el IDE Eclipse.
* **Controlador de Microsoft JDBC 4.1 para SQL Server**. Esta actualización incluye compatibilidad con el controlador JDBC más reciente de Microsoft SQL Server.
* **Versión 2.7 del SDK de Azure**. Esta actualización más reciente en el SDK de Azure es el nuevo requisito previo para el Kit de herramientas cuando está instalado en Windows. (Tenga en cuenta que esto no es necesario en sistemas operativos Windows no).
* **Soporte técnico para la versión 7 Zulú OpenJDK actualizar**. Para obtener más información, consulte la [página web de sistemas de Azul para el OpenJDK Zulú].

### <a name="may-1-2015"></a>1 de mayo de 2015

El Kit de herramientas de Azure para Eclipse - versión de mayo de 2015 incluye las siguientes mejoras:

* **Mejorar la selección del servidor de interfaz de usuario**. Esta versión simplifica el uso del Kit de herramientas en sistemas operativos Windows no.
* **Soporte técnico para proyectos de experto**. Esta versión admite experto en proyectos como aplicaciones, que puede implementar el Kit de herramientas en Azure y configurar perspectivas de aplicación.
* **Versión 2.6 del SDK de Azure**. Esta actualización más reciente en el SDK de Azure es el nuevo requisito previo para el Kit de herramientas cuando está instalado en Windows. (Tenga en cuenta que esto no es necesario en sistemas operativos Windows no).
* **Actualización de implementación en lugar de volver a publicar**. Si se vuelve a publicar un proyecto de implementación cuando la versión anterior ya está activa, el Kit de herramientas usa ahora la funcionalidad de actualización de implementación de Azure en lugar de apagar la implementación anterior y volver a publicar desde cero igual que en el pasado. Esto permite su servicio de nube para ejecutarse sin interrupciones siempre que sea posible, que permite lograr alta disponibilidad incluso durante una actualización y acelera el proceso de volver a publicar.
* **Soporte técnico para la última versión de 8 Zulú OpenJDK - actualizar 40**. Para obtener más información, consulte la [página web de sistemas de Azul para el OpenJDK Zulú].

### <a name="march-9-2015"></a>9 de marzo de 2015

El Kit de herramientas de Azure para Eclipse - versión de marzo de 2015 incluye las siguientes mejoras:

* **Soporte para Mac, Ubuntu y otras variantes de Linux**. Esta versión del Kit de herramientas de Azure para Eclipse agrega compatibilidad con Mac OS varias plataformas y Unix, por lo que los programadores pueden instalar el Kit de herramientas para crear, configurar y publicar los proyectos de Java a los servicios de nube de Azure (PaaS) de Eclipse ejecutando en sistemas operativos Windows.

>[AZURE.NOTE] Esta función está en la vista previa y no se recomienda para su uso en entornos de producción. No hay ningún contrato de nivel de servicio (SLA) de atención al cliente, pero es agradecemos y aconseja todos los comentarios.

* **Complemento de la nueva información de la aplicación**. Los programadores ahora son capaces de configurar telemetría servidor automático con información de la aplicación en Azure.
* **Automatización de la implementación de línea de comandos para ant**. Esta característica permite a los desarrolladores automatizar la publicación de versiones más recientes de sus instalaciones de Ant fuera Eclipse. Una secuencia de comandos pregenerada se configura automáticamente para un proyecto tras la primera vez se ha implementado desde Eclipse y posteriores implementaciones pueden utilizar la secuencia de comandos para automatizar las implementaciones a través de la línea de comandos sólo.
* **Disponibilidad de tomcat y embarcadero en Azure para su implementación más fácil y rápida**. Los programadores ahora pueden hacer referencia a varias versiones de Tomcat y embarcadero que están disponibles en Azure directamente en su lugar de tener que cargar un servidor Java a sus cuentas (o mediante el Kit de herramientas), así que no es necesario para cargar un servidor Java para escenarios Introducción rápidos.
* **Método abreviado para publicación aplicaciones web de Java a los servicios de nube de Azure**. Para reducir la curva de aprendizaje para escenarios sencillos de prueba y desarrollo, los desarrolladores ahora pueden publicar aplicaciones Java más directamente a Azure. En lugar de tener que ir a través del proceso de creación y configuración de un proyecto de implementación de Azure, se implementará aplicaciones con una instancia predeterminada de la versión 8 de Tomcat y Zulú JVM (OpenJDK).

### <a name="january-30-2015"></a>30 de enero de 2015

El Kit de herramientas de Azure para Eclipse - versión de enero de 2015 incluye las siguientes mejoras:

* **Soporte de aplicación de IBM® WebSphere® Server Core de libertad**. Esta versión agrega el núcleo de libertad de servidor de IBM WebSphere aplicación a la lista de servidores de aplicación compatibles desde la que se puede implementar a Azure el Kit de herramientas. Esta adición más reciente expande la lista actual de servidores de aplicaciones que son compatibles con &quot;del cuadro&quot; el Kit de herramientas que ya incluyen varias versiones de Tomcat, embarcadero, JBoss y GlassFish.
* **Inclusión de aplicación perspectivas SDK**. Esta biblioteca recién lanzado API de cliente (v0.9.0) ahora es parte del paquete para bibliotecas de Azure de Java.
* **Paquete para bibliotecas de Azure de Java actualizado**. Esta actualización incluye bibliotecas de Azure para Java v0.7.0 y v2.0.0 de la API de cliente de almacenamiento, así como la v0.9.0 de aplicación perspectivas SDK recién creado.

### <a name="november-12-2014"></a>12 de noviembre de 2014

El Kit de herramientas de Azure para Eclipse - versión de noviembre de 2014 incluye las siguientes mejoras:

* **Soporte técnico para Azure SDK 2,5**. Esta actualización más reciente en el SDK de Azure es el nuevo requisito previo para el Kit de herramientas.
* **Soporte técnico para obtener una versión actualizada de OpenJDK Zulú v1.8, v1.7 y v1.6 paquetes**. Para obtener más información, consulte la [página web de sistemas de Azul para el OpenJDK Zulú].
* **Compatibilidad con los nuevos tamaños D estándar para los servicios de nube**, que ofrezcan mayor rendimiento y recursos de memoria adicionales. Para obtener más información, vea [Máquina Virtual y los tamaños de servicio de nube para Azure].

### <a name="october-17-2014"></a>17 de octubre de 2014

El Kit de herramientas de Azure para Eclipse - versión de octubre de 2014 incluye las siguientes mejoras:

* **Mejoras de rendimiento publicar en escenarios de la nube**. Carga de información de la suscripción es más rápido cuando los usuarios tienen varias suscripciones y cuentas de almacenamiento.
* **Compatibilidad con una versión actualizada del paquete v1.8 Zulú OpenJDK**. Para obtener más información, consulte la [página web de sistemas de Azul para el OpenJDK Zulú].
* **Soporte técnico para dejar obsoletos versiones anteriores de 3ª JDKs de terceros**. Paquetes de JDK desusados ya no aparecerá en el menú desplegable para los nuevos proyectos de implementación. Proyectos existentes que hace referencia a los paquetes de JDK desusados seguirá posibilidad de hacerlo a la vez que se está, pero se recomienda actualizar dichos proyectos para confiar en la última.
* **Versión actualizada del paquete para bibliotecas de Azure para biblioteca de API de cliente de Java**. Para obtener más información, vea la [API del cliente de Microsoft Azure].
* **Correcciones de errores.** Esta versión contiene un número de varias correcciones de errores que se basa en informes de usuario y pruebas.

### <a name="august-5-2014"></a>5 de agosto de 2014

El Kit de herramientas de Azure para Eclipse - versión de agosto de 2014 incluye las siguientes mejoras

* **Compatibilidad con Azure SDK 2,4.** Las versiones anteriores del Kit de herramientas Eclipse no funcionará con este SDK reciente.
* **Las versiones actualizadas de la v1.6 Zulú OpenJDK paquetes 1.7 y v1.8.** Para obtener más información, consulte la [página web de sistemas de Azul para el OpenJDK Zulú].
* **Versión actualizada del paquete para bibliotecas de Azure para biblioteca de API de cliente de Java.** Para obtener más información, vea la [API del cliente de Microsoft Azure].
* **Compatibilidad con más reciente publicar el formato de archivo de configuración.** Se agregó soporte para la versión 2.0 del formato de archivo de configuración de publicación.
* **Cambios en la arquitectura detrás de publicar en función de la nube.** El Kit de herramientas está utilizando la API de cliente de Azure recientes de Microsoft para Java para su soporte de publicar en la nube.
* **Correcciones de errores.** Esta versión contiene un número de usuario solicitado correcciones de errores.

### <a name="june-12-2014"></a>12 de junio de 2014

El Kit de herramientas de Azure para Eclipse - versión de junio de 2014 es una actualización de servicio secundaria que proporciona las siguientes mejoras:

* **Compatibilidad con la v1.8 paquete Zulú OpenJDK.** Para obtener más información, consulte la [página web de sistemas de Azul para el OpenJDK Zulú].
* **Versiones actualizadas de la v1.6 Zulú OpenJDK y 1,7 paquetes.** Para obtener más información, consulte la [página web de sistemas de Azul para el OpenJDK Zulú].
* **Versión actualizada del paquete para bibliotecas de Azure para biblioteca de API de cliente de Java.** Para obtener más información, vea la [API del cliente de Microsoft Azure].
* **Correcciones de errores.** Esta versión contiene un número de usuario solicitado correcciones de errores.

### <a name="april-4-2014"></a>4 de abril de 2014

El complemento de Azure para Eclipse - versión de abril de 2014 ha publicado. Se trata de una actualización que acompaña a la versión de 2.3 de SDK de Azure, que es un requisito previo y se descargará automáticamente cuando instale el complemento. Esta actualización incluye características nuevas, correcciones de errores y algunas mejoras de facilidad de uso basados en comentarios como obtener una vista previa de febrero de 2014:

* **Compatibilidad con la versión de Azure SDK 2.3.** El complemento de Azure para Eclipse - versión de abril de 2014 requiere Azure SDK 2.3. Cuando se usa el complemento de nuevo, si todavía no tiene de Azure SDK 2.3, le pedirá para permitir la instalación. No use Azure SDK 2.3 con versiones anteriores del complemento.
* **Actualización de las aplicaciones sin implementación de paquete completo.** Al implementar aplicaciones de Java que forman parte de su proyecto, el complemento ahora automáticamente las cargas ellos en su cuenta de almacenamiento seleccionada para que pueda actualizarla y las instancias de la función para implementar los bits de aplicación más recientes sin tener que volver a crear y volver a todo el paquete de reciclaje.
* **Tomcat 8 es ahora un servidor de aplicaciones reconocido.** Si selecciona un directorio de instalación de Tomcat 8 en el equipo en la ficha **servidor** del cuadro de diálogo de **Proyecto de implementación de Azure** , el complemento ahora automáticamente lo detecta y pueda implementar Tomcat 8 de forma automática, similar a las versiones anteriores de Tomcat ya en la lista.
* **Actualizaciones de paquete azul Zulú OpenJDK: actualización de v1.7 51 y v1.6 actualizan 47.** De forma eficaz con esta versión, actualización de paquete del sistema Azul Zulú abrir JDK versión 7 51 está disponible. También, los paquetes de v6 Zulú abrir JDK iniciar esté disponible, comenzando por la actualización 47. Estas actualizaciones son además del paquete de versión 7 Zulú abrir JDK estaba disponible una actualización 45, 40 y actualización 25.
* **Compatibilidad con tamaño A8 y Máquina Virtual de A9 Microsoft Azure.** Ahora puede implementar un servicio de nube en la memoria alta A8 y los tamaños de la máquina Virtual de A9. Para obtener más información sobre estos tamaños VM, vea [Máquina Virtual y los tamaños de servicio de nube de Azure].
* **Redirección automática de HTTP a HTTPS para las funciones de SSL.** Cuando su servicio de nube contiene solo las funciones HTTPS, si la solicitud de usuario especifica HTTP, automáticamente le redirigirá a HTTPS. No es necesario para crear una función independiente para controlar las solicitudes de HTTP.
* **Express emulador utilizado para emulación local.** El emulador de Azure Express ahora se usa como el emulador al depurar las aplicaciones localmente.
* **El nombre ha cambiado Azure como Microsoft Azure.** Pantallas de la interfaz de usuario ahora reflejan que haya nombre cambiado y ya no se denomina Azure Azure.

### <a name="february-6-2014"></a>6 de febrero de 2014

El complemento de Azure para Eclipse: febrero de 2014 Preview ha publicado. Esta actualización incluye características nuevas, correcciones de errores y algunas mejoras de facilidad de uso basados en comentarios como obtener una vista previa de octubre de 2013:

* **Compatibilidad con la descarga de SSL.** Descarga de Sockets Layer (SSL) seguro se ha agregado como una función, lo que le permite habilitar fácilmente la compatibilidad de transferencia de hipertexto protocolo seguro (HTTPS) en la implementación de Java en Azure, sin necesidad de configurar SSL en el servidor de aplicaciones Java. Esto es especialmente importante en afinidad de la sesión o se han autenticado escenarios de comunicación. Por ejemplo, cuando se usa el filtro de servicio de Control de acceso (ACS), ya que es compatible con el Kit de herramientas. Para obtener más información, consulte [Descarga de SSL] y [cómo usar descarga de SSL].
* **GlassFish 4 es ahora un servidor de aplicaciones reconocido.** Si selecciona un directorio de instalación de GlassFish 4 en el equipo en la ficha **servidor** del cuadro de diálogo de **Proyecto de implementación de Azure** , el complemento ahora automáticamente lo detecta y pueda implementar GlassFish OSE 4 de forma automática, similar a la versión GlassFish OSE 3 ya están en la lista.
* **Actualización de paquete azul Zulú OpenJDK 45.** De forma eficaz con esta versión, actualización de Zulú (paquete de abrir JDK versión 7) del sistema Azul 45 ahora está disponible; es además de la actualización disponible anteriormente 40 y actualización de 25.
* **Soporte técnico para 'auto' para privado puertos de extremo.** Puede establecer un puerto privado en automático para los extremos de entrada y extremos internos para permitir a Azure asignar un puerto a ese extremo automáticamente. Anteriormente sólo se puede asignar a un número de puerto específico.
* **Soporte técnico para personalizar el nombre del certificado (CN) en la interfaz de usuario de la creación de un certificado autofirmado.** Anteriormente, se usó el mismo nombre codificado para todos los certificados nuevos; Ahora puede especificar su propio nombre de certificado para ayudar a distinguir entre varios certificados en el portal de Azure para fines distintos.
* **Barra de herramientas de azure:** La barra de herramientas de Azure tiene una actualizada con los cambios siguientes: 
    * ![][ic710876]Este icono se agregó para el **Nuevo proyecto de implementación de Azure**.
    * ![][ic710877]Este icono se agregó como un acceso directo al cuadro de diálogo de creación de un certificado autofirmado.
* **Compatibilidad con el tamaño de la máquina Virtual de A5 Azure.** Ahora puede implementar un servicio de nube en la memoria de gran tamaño de la máquina Virtual de A5. Para obtener más información sobre el tamaño de la máquina virtual, vea [Máquina Virtual y los tamaños de servicio de nube de Azure].
* **Soporte técnico de Microsoft Windows Server 2012 R2.** Ahora puede seleccionar Windows Server 2012 R2 como el sistema operativo de nube.

### <a name="october-22-2013"></a>22 de octubre de 2013

El complemento de Azure para Eclipse: octubre de 2013 Preview ha publicado. Esta actualización incluye características nuevas, correcciones de errores y algunas mejoras de facilidad de uso basados en comentarios como obtener una vista previa de septiembre de 2013:

* **Compatibilidad con la versión de Azure SDK 2.2.** El complemento de Azure para Eclipse - octubre de 2013 Preview es compatible con Azure SDK 2.2. ¿Seguirán funcionando con Azure SDK 2.1 el complemento e instalará automáticamente Azure SDK 2.2 si ya no tiene al menos Azure SDK 2.1 instalado.
* **Actualización de paquete azul Zulú OpenJDK 40.** Como fecha de anuncio de septiembre de 2013 obtener una vista previa, el complemento ahora se permite usando un JDK proporcionados por terceros directamente en Azure, sin necesidad de cargar su propios JDK. En la versión de octubre de 2013, actualización de Zulú (paquete de abrir JDK versión 7) del sistema de Azul 40 ahora está disponible; Esto es además de la actualización publicada originalmente 25.
* **Vínculo de implementación de nube en el registro de actividad.** En el registro de actividad de Azure cuando la implementación tiene un estado de **publicación**, puede hacer clic **Published** dado que ahora es un vínculo a la implementación; a continuación, se abrirá la implementación en el explorador. (El estado **publicado** se previamente marcado **ejecuta**.)
* **Selección de sistema operativo de destino en momento de la publicación.** El cuadro de diálogo **publicar en Azure** contiene un nuevo campo, el **Sistema operativo de destino**, que proporciona una manera más reconocible para establecer su sistema operativo de destino.
* **Sobrescribir automática implementación anterior.** El cuadro de diálogo **publicar en Azure** contiene una nueva casilla, **implementación anterior de sobrescritura**. Si esta opción está activada, cuando se publique la implementación nueva sobrescribirá automáticamente la implementación anterior; ¿no experimenta &quot;409 conflicto&quot; problemas cuando se publica en la misma ubicación sin primera anular la publicación de la implementación anterior.
* **Embarcadero 9 es ahora un servidor de aplicaciones reconocido.** Si selecciona un directorio de instalación embarcadero 9 en el equipo en la ficha **servidor** del cuadro de diálogo de **Proyecto de implementación de Azure** , el complemento ahora automáticamente lo detecta y pueda implementar embarcadero 9 de forma automática, similar a las versiones anteriores de embarcadero ya están en la lista.
* **Agregar una función en el menú contextual de proyecto.** El menú contextual del proyecto **Azure** ahora contiene un nuevo elemento de menú **Agregar rol**, lo que proporciona una rápida y más forma reconocible agregar una nueva función a su proyecto de Azure.
* **Actualización para el paquete para las bibliotecas de Azure para biblioteca de Java.** Esto se basa en la versión 0.4.6 de la [API de cliente de Microsoft Azure].

### <a name="september-25-2013"></a>25 de septiembre de 2013

El complemento de Azure para Eclipse: septiembre de 2013 Preview ha publicado. Esta actualización incluye características nuevas, correcciones de errores y algunas mejoras de facilidad de uso basados en comentarios como obtener una vista previa de agosto de 2013:

* **Capacidad para implementar el paquete de Azul Zulú OpenJDK disponible en Azure.** Se ha agregado una nueva opción cuando se especifica el JDK para usar con la implementación de Azure. Con esta opción, puede implementar un paquete de terceros JDK directamente en la nube de Azure, sin tener que cargar sus propios. Sistemas de azul proporciona la primera como paquete Zulú llamado, en función de la OpenJDK, que ahora se puede implementar mediante esta opción.
* **Actualización para el paquete para las bibliotecas de Azure para biblioteca de Java.** Esto se basa en la versión 0.4.5 de la [API de cliente de Microsoft Azure].

### <a name="august-1-2013"></a>1 de agosto de 2013

El complemento de Azure para Eclipse: agosto de 2013 Preview ha publicado. Se trata de una actualización que acompaña a la versión 2.1 de SDK Azure, que es un requisito previo y se descargará automáticamente cuando instale el complemento. Esta actualización incluye características nuevas, correcciones de errores y algunas mejoras de facilidad de uso basados en comentarios como obtener una vista previa de julio de 2013:

* **Eliminación de opciones para incluir la JDK local y el servidor de aplicaciones local como parte del paquete de implementación.** Descargar el JDK y servidor de aplicaciones de almacenamiento de nube durante la implementación es preferible incrustar estos componentes en el paquete desde descargar los resultados de elementos de menor tamaño de paquete de implementación, tiempos de implementación y facilitar el mantenimiento. Como resultado, las opciones para incluir la JDK y se han quitado el servidor de aplicaciones en el paquete de implementación. Los proyectos existentes que se han configurado para incluir los JDK local y el servidor de aplicaciones local como parte del paquete de implementación se convertirán automáticamente para el JDK de carga de automática y servidor de aplicaciones para el almacenamiento de la nube.
* **Compatibilidad con la versión 2.1 de Azure SDK.** El complemento de Azure para Eclipse: agosto de 2013 Preview requiere Azure SDK 2.1. No utilice la vista previa de agosto de 2013 con versiones anteriores de Azure SDK y no utilice Azure SDK 2.1 con versiones anteriores de complemento de Azure para Eclipse.
* **Compatibilidad con la versión de Eclipse Kepler.** Relacionado con esto, la nueva versión mínima necesaria IDE Eclipse es Indigo. El complemento de Azure para Eclipse oficialmente ya no se ha probado en Helios.

### <a name="july-3-2013"></a>3 de julio de 2013

El complemento de Azure para Eclipse: julio de 2013 Preview ha publicado. Esta actualización incluye características nuevas, correcciones de errores y algunas mejoras de facilidad de uso basados en comentarios como obtener una vista previa de mayo de 2013:

* **Capacidad para crear una nueva cuenta de almacenamiento.** Se ha agregado un botón de **nuevo** en el cuadro de diálogo **Agregar una cuenta de almacenamiento** . Esto le permite crear una cuenta de almacenamiento dentro del complemento Eclipse, sin necesidad de iniciar sesión en el portal de administración de Azure. (Ya debe tener una suscripción de Azure para usar esta característica.) Para obtener más información acerca de cómo crear una nueva cuenta de almacenamiento, consulte [crear una nueva cuenta de almacenamiento].
* **Nuevo &quot;(auto)&quot; opción de cuenta de almacenamiento que se utiliza para la distribución automática de JDK y servidor y de almacenamiento en caché.** Cuando se usa la opción **cargar automáticamente** el JDK y servidor de aplicaciones, ahora puede especificar **(auto)** para la cuenta de almacenamiento y la dirección URL que se utilizará al cargar el JDK y servidor de aplicaciones, o cuando se utiliza el almacenamiento en caché de Azure. A continuación, estas características utilizará automáticamente la misma cuenta de almacenamiento que seleccionó en el cuadro de diálogo **publicar en Azure** . Se ha actualizado el tutorial de [creación de una aplicación Hola a todos de Azure en Eclipse] para usar la nueva opción **(automático)** .
* **Capacidad para establecer los extremos de servicio de Azure.** Especifique los extremos de servicio que determinan si la aplicación se implementa en y administrada por la plataforma de Windows Azure global, Azure a través de 21Vianet en China o privadas plataforma Windows Azure. Para obtener más información, vea [Extremos de servicio de Azure].
* **Grandes implementaciones pueden especificar un recurso de almacenamiento local.** En caso de que su implementación es demasiado grande para que se incluyan en la carpeta de approot predeterminada, ahora puede especificar un recurso de almacenamiento local como destino de implementación de su JDK y servidor de aplicaciones. Para obtener más información, vea [Implementar implementaciones grandes].
* **Compatibilidad con tamaños A6 y A7 Azure Virtual Machine.** Ahora puede implementar un servicio de nube a la memoria alta A6 y A7 Virtual Machine tamaños. Para obtener más información sobre estos tamaños, vea [Máquina Virtual y los tamaños de servicio de nube para Azure].
* **Actualización para el paquete para las bibliotecas de Azure para biblioteca de Java.** Esto se basa en la versión 0.4.4 de la [API de cliente de Microsoft Azure].

### <a name="may-1-2013"></a>1 de mayo de 2013

El complemento de Azure para Eclipse - posible 2013 Preview ha publicado. Se trata de una actualización importante que acompaña a la versión de Azure SDK 2.0, que es un requisito previo y se descargará automáticamente cuando instale el complemento. Esta versión incluye nuevas características, correcciones de errores y algunas mejoras de facilidad de uso basados en comentarios como obtener una vista previa de febrero de 2013:

* **Carga automática de la implementación de almacenamiento de Azure, JDK y servidor de aplicaciones.** Una nueva opción automáticamente las cargas de la JDK seleccionado y el servidor de aplicaciones, cuando sea necesario, a una cuenta de almacenamiento de Azure especificado e implementa estos componentes desde allí, en lugar de incrustar en el paquete de implementación o tener la carga de usuario, a continuación, manualmente. Esta característica solicitada comúnmente puede mejorar la facilidad de implementación de los componentes de servidor y JDK, especialmente para usuarios principiantes. Para un tutorial que utiliza estas opciones, vea [crear una aplicación Hola a todos de Azure en Eclipse].
* **Centralizadas seguimiento de la cuenta de almacenamiento y capacidad de cuentas de almacenamiento referencia más fácilmente (a través de un control de lista desplegable).** Esto se aplica a varias características que dependen de almacenamiento, como por ejemplo JDK y la implementación de componentes de servidor y el almacenamiento en caché. Para obtener más información, vea la [Lista de cuentas de almacenamiento de Azure].
* **Configuración de acceso remoto simplificado publicar en el Asistente para la nube.** Todo lo que necesita hacer es de tipo en un nombre de usuario y contraseña para habilitar el acceso remoto o deje en blanco para mantener el acceso remoto deshabilitado.
* **Actualización para el paquete para las bibliotecas de Azure para biblioteca de Java.** Esto se basa en la versión 0.4.2 de la [API de cliente de Microsoft Azure].
* **Compatibilidad con las sesiones en Windows Server 2012.** Anteriormente, sesiones permanentes trabajó únicamente en Windows Server 2008 R2, ahora ambas afinidad de la sesión de soporte técnico de sistema operativo destinos en la nube.
* **Mejoras de rendimiento de carga de paquete.** Aunque el JDK y servidor de aplicaciones están incrustadas en el paquete de implementación, puede ser la parte de la carga del proceso de implementación aproximadamente dos veces más rápida en comparación con versiones anteriores.

### <a name="february-8-2013"></a>8 de febrero de 2013

El complemento de Azure para Eclipse: febrero de 2013 Preview ha publicado. Esto es una actualización secundaria que incluye correcciones de errores, mejoras de facilidad de uso basados en comentarios y algunas características nuevas, como obtener una vista previa de 2012 de noviembre:

* Soporte técnico para implementar JDKs, servidores de aplicaciones y arbitrario otros componentes de Azure público o privado blob descargas de almacenamiento en lugar de incluirlos en el paquete de implementación al implementar en la nube.
* Capacidad para cambiar el orden en que se procesan componentes definidas por el usuario de una función, mediante la adición de los botones **Subir** y **Bajar** en la sección de **componentes** de las **Propiedades de la función de Azure**.
* Una actualización de la biblioteca de **paquete para las bibliotecas de Azure para Java** , en función de la versión 0.4.0 de la [API de cliente de Microsoft Azure].

### <a name="november-5-2012"></a>5 de noviembre de 2012

El complemento de Azure para Eclipse: noviembre de 2012 Preview ha publicado. Se trata de una actualización importante que incluye un número de características nuevas, así como adicionales correcciones de errores y mejoras de facilidad de uso basados en comentarios como obtener una vista previa de septiembre de 2012:

* Compatibilidad de Microsoft Windows Server 2012 como el sistema operativo de nube.
* Compatibilidad con Azure asistencia comparten ubicación de almacenamiento en caché para clientes de memcached.
* Inclusión de las bibliotecas de cliente Apache Qpid JMS para aprovechar la mensajería basada en AMQP de Azure.
* Un asistente **Nuevo proyecto** mejorado, con una página nueva al final que proporciona a los usuarios la capacidad de habilitar rápidamente diversas características clave comunes en su proyecto: sesiones permanentes, almacenamiento en caché y depuración remota.
* Reducción automática de las instancias de función 1 cuando se ejecuta en el emulador de cálculo, para evitar conflictos de enlace de puerto entre instancias del servidor.

### <a name="september-28-2012"></a>28 de septiembre de 2012

El complemento de Azure para Eclipse: septiembre de 2012 Preview ha publicado. Esta actualización de servicio incluye un número adicionales de correcciones de errores como obtener una vista previa en agosto de 2012, así como algunas mejoras de facilidad de uso basados en comentarios en las características existentes:

* Compatibilidad con Microsoft Windows 8 y Microsoft Windows Server 2012 como el sistema operativo de desarrollo, resolver problemas que anteriormente impide que el complemento funciona correctamente en los sistemas operativos.
* Compatibilidad mejorada para especificar intervalos de puertos de extremo.
* Correcciones de errores relacionados con las rutas de acceso que contienen espacios.
* Mejoras de menú de contexto de rol para acelerar el acceso a la configuración específica de la función.
* Hacer pequeños ajustes en el Asistente para **la publicación en la nube** y un número adicionales de correcciones de errores.

### <a name="august-28-2012"></a>28 de agosto de 2012

El complemento de Azure para Eclipse: agosto de 2012 Preview ha publicado. Esta actualización de servicio incluye correcciones de errores adicionales como obtener una vista previa en julio de 2012, así como varias mejoras de comentarios condicionada por el uso de las características existentes:

* En el cuadro de diálogo Filtro de servicios de Control de acceso de Azure:
    * **Opción para incrustar el certificado de firma** en un archivo de la aplicación guerra, para simplificar la implementación de la nube.
    * **Opción para crear un certificado autofirmado** dentro de la interfaz de usuario de filtro de ACS. Para obtener información sobre el filtro de servicios de Control de acceso de Azure, vea [cómo autenticar usuarios Web con Azure Access Control Service utilizando Eclipse].
* En el Asistente para proyectos de implementación de Azure (también se aplica a la página de propiedades de configuración del servidor del rol):
    * **Detección automática de la ubicación de JDK** en su equipo (que se puede reemplazar si lo desea).
    * **Detección automática del tipo de servidor** cuando se selecciona el directorio de instalación de servidor de aplicaciones.

### <a name="july-15-2012"></a>15 de julio de 2012

El complemento de Azure para Eclipse: julio de 2012 vista previa, que cubre una serie de los errores de prioridad más alta encontrado o notificado por usuarios después del lanzamiento de junio de 2012, se ha publicado. Es solo una actualización de servicio, no incluidas características nuevas.

### <a name="june-7-2012"></a>7 de junio de 2012

Ha publicado Azure complemento para Eclipse - CTP de junio de 2012. Las nuevas características incluyen:

* **Asistente para nuevo proyecto de implementación de Azure:** Le permite seleccionar su JDK, servidor de aplicaciones Java y aplicaciones de Java directamente en la interfaz del asistente mejorada. Incluido en la lista del cuadro de configuraciones de servidor que puede elegir son Tomcat 6, 7 Tomcat, GlassFish OSE 3, embarcadero 7, 8 embarcadero, JBoss 6 y 7 JBoss (independiente). Además, puede personalizar la lista de configuraciones de servidor. Esta mejora de la interfaz de usuario es una alternativa a arrastrar y soltar archivos comprimidos y copiar a través de scripts de inicio, que antes era el enfoque principal. Este método funciona bien todavía, pero es probable que se utilizará sólo para escenarios más avanzados.
* **Página de propiedades del rol de configuración del servidor:** Le permite cambiar fácilmente la JDKs, servidores de aplicaciones de Java y aplicaciones asociadas con la implementación después de haber creado el proyecto. Para obtener más información, vea [Propiedades de configuración del servidor].
* **&quot;Publicar en nube&quot; asistente:** proporciona una forma sencilla para implementar el proyecto Azure directamente desde Eclipse automatizar visible-elevación previamente manual de obtención de credenciales, iniciar sesión en el Portal de administración de Azure, cargar un paquete, etcetera. Para obtener un ejemplo de cómo implementar directamente el proyecto en Azure, vea [crear una aplicación Hola a todos de Azure en Eclipse].
* **Barra de herramientas de azure:** Una barra de herramientas de Azure está ahora disponible en Eclipse que contiene botones que llaman a las características siguientes:
    * ![][ic710879]**Ejecutar en Azure emulador**: se ejecuta el proyecto en el emulador.
    * ![][ic710880]**Restablecer emulador de Azure**: restablece el emulador.
    * ![][ic710881]**Crear paquete de nube de Azure**: compila el paquete para su implementación.
    * ![][ic710876]**Nuevo proyecto de implementación de Azure**: crea un nuevo proyecto de implementación de Azure.
    * ![][ic710882]**Publicar en la nube de Azure**: publica un proyecto en Azure.
    * ![][ic710883]**Quitar**: elimina la implementación.
    * Muchos de estos botones de la barra de herramientas de Azure se usan en la [creación de una aplicación Hola a todos de Azure en Eclipse].
* **Azure bibliotecas para Java:** Ahora disponible como parte del paquete solo para las bibliotecas de Azure para biblioteca de Java en Eclipse, que acompaña a la instalación del complemento y que contiene todas las dependencias necesarias también. Basta con agregar una referencia a la biblioteca en el proyecto de Java y no es necesario descargar nada por separado. Para obtener más información, vea [instalar el Kit de herramientas de Azure para Eclipse].
* **Microsoft JDBC controlador 4.0 para SQL Server disponibles durante la instalación del complemento:** Durante la instalación del complemento nuevo, se puede instalar la versión más reciente del controlador JDBC de Microsoft SQL Server.
* **Filtro de servicio de Control de acceso de azure disponibles durante la instalación del complemento:** Este nuevo componente, incluida en una biblioteca de Eclipse en el Kit de herramientas, habilita la aplicación web de Java perfecta sacar partido de autenticación de servicio de Control de acceso (ACS) de Azure con varios proveedores de identidades, como Google, Live.com y Yahoo!. No debe escribir lógica de autenticación usted mismo, simplemente configurar algunas opciones y permitir que el filtro de hacer el trabajo pesado de que los usuarios puedan iniciar sesión en de ACS. Solo podrá centrarse en escribir el código que proporciona a los usuarios acceso a los recursos basados en su identidad, como devuelve el filtro dentro del objeto de solicitud para la aplicación. Para obtener un tutorial acerca de cómo utilizar el filtro de ACS, consulte [cómo autenticar usuarios Web con Azure Access Control Service utilizando Eclipse].
* **La detección automática de los requisitos previos de Azure SDK 1.7:** Cuando se crea un nuevo proyecto de implementación de Azure, Azure SDK 1.7 se descargará automáticamente si ya no está instalado.
* **Extremos de instancia:** Permite el acceso de extremo directa de puerto para la comunicación con instancias de la función de equilibrio de carga. Extremos de instancia se pueden agregar a través de la interfaz de usuario, disponible a través de la página [Propiedades de extremos] extremos. Esto ayuda a habilitar la depuración remota y diagnósticos JMX concreta calculan instancias que se ejecutan en la nube en escenarios con multi-implementaciones de instancia. 
* **Componentes de interfaz de usuario:** Hace que sea más fácil para los usuarios avanzados configurar las dependencias de proyecto entre las funciones de Azure individuales en el proyecto y otros recursos externos como proyectos de aplicación de Java; También facilita describir su lógica de implementación. Para obtener más información, vea [Propiedades de componentes].
* **Actualización automática de versiones anteriores de proyectos:** Cuando abre un área de trabajo que tiene el proyecto Azure creado con una versión anterior del complemento, los proyectos antiguos se mostrarán en Eclipse como cerrado, porque las versiones anteriores de proyectos no son compatibles con la nueva versión. Si intenta abrir uno de estos proyectos antiguos, se iniciará un Asistente para la actualización. Si acepta la actualización, se crea un nuevo proyecto con **_Upgraded** anexado al nombre y se actualizan automáticamente para que funcione con la nueva versión. Puede cambiar el nombre del nuevo proyecto según sea necesario. Como parte de la actualización, el proyecto original no se modificará (y permanecerá cerrado).

### <a name="december-10-2011"></a>10 de diciembre de 2011

Ha publicado Azure complemento para Eclipse - CTP de diciembre de 2011. Las nuevas características incluyen:

* **Afinidad de sesión (&quot;sesiones permanentes&quot;) soporte técnico:** Ayuda para habilitar el seguimiento de estado, agrupadas aplicaciones Java con solo una casilla de verificación única. Para obtener más información, vea [Afinidad de la sesión].
* **Realizado previamente ejemplos de secuencia de comandos de inicio:** Para los más populares Java servidores (Tomcat, embarcadero, JBoss, GlassFish), que pueden simplemente copiar/pegar desde el directorio de ejemplos de su proyecto a la secuencia de comandos de inicio.
* **Resultados de inicio del emulador en tiempo real:** Ahora puede ver la ejecución de todos los pasos de la secuencia de comandos de inicio en una ventana de consola dedicado, que muestra el progreso y errores en la secuencia de comandos mientras se ejecuta en Azure.
* **Java.exe automática, atenuado supervisión:** Cuando java.exe deja de funcionar, mediante un script de ligero, prefabricado incluido automáticamente en la implementación que forzar reciclaje rol.
* **Depuración de interfaz de usuario de configuración de aplicación de Java remoto:** Le permite habilitar fácilmente depurador remoto de Eclipse tener acceso a la aplicación de Java que se ejecuta en el emulador o en la nube de Azure, para que pueda recorrer y depurar el código de Java en tiempo real. Para obtener más información, vea [Depurar aplicaciones de Azure en Eclipse].
* **Configuración de recursos de almacenamiento local interfaz de usuario:** Así que ya no tiene configurar recursos locales manipular el XML directamente. Esta característica permite el acceso a la ruta de acceso eficaz de los recursos locales cuando se implementa a través de una variable de entorno que se puede hacer referencia directamente desde la secuencia de comandos de inicio. Para obtener más información, vea [Propiedades de almacenamiento Local].
* **Configuración de variables de entorno interfaz de usuario:** Así que ya no debe establecer variables de entorno mediante la edición manual de la configuración de XML. Para obtener más información, vea [Propiedades de variables de entorno].
* **Controlador JDBC de SQL Azure:** Obtiene instalado mediante el complemento como una biblioteca de Eclipse perfectamente integrada, habilitar más fácil programar en SQL Azure. 
* **Acceso rápido de menú contextual a la interfaz de usuario de la configuración de rol**: simplemente haga clic en la carpeta de la función y haga clic en **Propiedades**.
* **Los iconos de carpeta de proyecto y rol de Azure personalizado:** Para mejorar la visibilidad y facilitar la exploración dentro de su área de trabajo y el proyecto.

## <a name="see-also"></a>Vea también ##

Para obtener más información sobre los Toolkits de Azure para Java IDE, consulte los siguientes vínculos:

- [Kit de herramientas de Azure para Eclipse]
  - [Instalar el Kit de herramientas de Azure Eclipse]
  - [Crear una aplicación Web de llamadas internacionales de saludo para Azure en Eclipse]
  - *Novedades en el Kit de herramientas de Azure para Eclipse (en este artículo)*
- [Kit de herramientas de Azure para IntelliJ]
  - [Instalar el Kit de herramientas de Azure IntelliJ]
  - [Crear una aplicación Web de llamadas internacionales de saludo para Azure en IntelliJ]
  - [Novedades en el Kit de herramientas de Azure para IntelliJ]

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure].

<!-- URL List -->

[Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Crear una aplicación Web de llamadas internacionales de saludo para Azure en Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Crear una aplicación Web de llamadas internacionales de saludo para Azure en IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalar el Kit de herramientas de Azure Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalar el Kit de herramientas de Azure IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[What's New in the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novedades en el Kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547

[Página web de sistemas de azul para el OpenJDK Zulú]: http://go.microsoft.com/fwlink/?LinkId=402457
[Extremos de servicio de Azure]: http://go.microsoft.com/fwlink/?LinkID=699526
[Lista de cuentas de almacenamiento de Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[Propiedades de componentes]: http://go.microsoft.com/fwlink/?LinkID=699525#components_properties
[Crear una aplicación del mundo Hola para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Depuración de aplicaciones de Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Implementar implementaciones grandes]: http://go.microsoft.com/fwlink/?LinkID=699536
[Propiedades de extremos]: http://go.microsoft.com/fwlink/?LinkID=699525#endpoints_properties
[Propiedades de variables de entorno]: http://go.microsoft.com/fwlink/?LinkID=699525#environment_variables_properties
[Complemento de herramientas de HDInsight para Eclipse]: ./hdinsight/hdinsight-apache-spark-eclipse-tool-plugin.md
[Cómo autenticar a los usuarios de la Web con el servicio de Control de acceso de Azure con Eclipse]: http://go.microsoft.com/fwlink/?LinkID=264703
[Cómo usar la descarga de SSL]: http://go.microsoft.com/fwlink/?LinkID=699545
[Instalar el Kit de herramientas de Azure Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propiedades de almacenamiento local]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties
[API de cliente de Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=280397
[Propiedades de configuración de servidor]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Afinidad de la sesión]: http://go.microsoft.com/fwlink/?LinkID=699548
[Descarga de SSL]: http://go.microsoft.com/fwlink/?LinkID=699549
[Para crear una nueva cuenta de almacenamiento]: http://go.microsoft.com/fwlink/?LinkID=699528#create_new
[Máquina virtual y los tamaños de servicio de nube de Azure]: http://go.microsoft.com/fwlink/?LinkId=466520

<!-- IMG List -->

[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710877]: ./media/azure-toolkit-for-eclipse-whats-new/ic710877.png
[ic710879]: ./media/azure-toolkit-for-eclipse-whats-new/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-whats-new/ic710880.png
[ic710881]: ./media/azure-toolkit-for-eclipse-whats-new/ic710881.png
[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710882]: ./media/azure-toolkit-for-eclipse-whats-new/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-whats-new/ic710883.png
