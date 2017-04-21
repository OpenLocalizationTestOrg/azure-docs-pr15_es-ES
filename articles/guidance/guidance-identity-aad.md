<properties
   pageTitle="Implementación de Azure Active Directory | Microsoft Azure"
   description="Cómo implementar una arquitectura de red seguro híbrido con Azure Active Directory."
   services="guidance,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="telmos"/>

# <a name="implementing-azure-active-directory"></a>Implementación de Azure Active Directory

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artículo describe los procedimientos recomendados para la integración de bosques y dominios de Active Directory (AD) locales con Azure Active Directory para proporcionar autenticación basada en nube identidad.

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación: [El Administrador de recursos] [ resource-manager-overview] y clásica. Esta arquitectura de referencia utiliza el Administrador de recursos, que Microsoft recomienda para implementaciones nuevas.

Puede usar los servicios de directorio y de identidad, como los servicios de directorio de AD (AD DS) para autenticar las identidades. Estas identidades pueden pertenecer a los usuarios, equipos, aplicaciones u otros recursos que forman parte de un límite de seguridad. Puede hospedar directorio y servicios de identidad local, pero en un escenario híbrido donde se encuentran los elementos de una aplicación en Azure puede ser más eficaz para ampliar esta funcionalidad en la nube. Este enfoque puede ayudar a reducir la latencia causada mediante el envío de autenticación y solicitudes de autorización local de la nube a los servicios de identidad y directorio de ejecución local.

Azure proporciona dos soluciones para implementar los servicios de directorio y de identidad en la nube:

1. Puede usar [Azure Active Directory (AAD)] [ azure-active-directory] para crear un dominio de Active Directory en la nube y vincularlo a la implementación local dominio de Active Directory. AAD le permite configurar el inicio de sesión único (SSO) para los usuarios que ejecutan aplicaciones de acceso a través de la nube. AAD utiliza [Azure AD Connect] [ azure-ad-connect] ejecutando local para replicar objetos e identidades mantenidos en el repositorio local en la nube.

    También puede implementar AAD sin usar un directorio local. En este caso, AAD actúa como el origen principal de información de identidad todo en lugar de replicar desde un directorio local que contiene los datos.

    Tenga en cuenta que el directorio en la nube es **no** una extensión de un directorio local. En su lugar, es una copia que contiene los mismos objetos y las identidades. Los cambios realizados en estos elementos local se copian en la nube, pero los cambios realizados en la nube **no** replicarse en el dominio local.

    >[AZURE.NOTE] Las ediciones de Azure Active Directory Premium admiten reescritura de contraseñas de los usuarios, habilitar los usuarios locales realizar el restablecimiento de contraseñas de autoservicio en la nube. Esta es la única información que se sincroniza AAD volver al directorio local. Para obtener más información acerca de las diferentes ediciones de AAD y sus características, vea [Ediciones de Azure Active Directory][aad-editions].

2. Puede implementar una VM que ejecuta AD DS como un controlador de dominio en Azure, ampliación de la infraestructura de AD existente (incluidos AD DS y AD FS) desde el centro de datos local. Este enfoque es más habitual para escenarios donde la red local y una red virtual Azure están conectadas por una conexión VPN o ExpressRoute. Esta solución también admite bidireccional replicación permitiéndole realizar cambios en la nube y local, siempre que sea más adecuado.

Esta arquitectura se centra en la solución 1. Para obtener más información acerca de la segunda solución, consulte [Ampliación de Active Directory para Azure][guidance-adds].

Casos de uso común para esta arquitectura incluyen:

- Proporcionar SSO para los usuarios finales de SaaS y otras aplicaciones en la nube, con las mismas credenciales que se especifican para las aplicaciones locales.

- Publicación local aplicaciones web a través de la nube para proporcionar acceso a los usuarios remotos que pertenecen a su organización.

- Implementación de capacidades de autoservicio para los usuarios finales, como restablecer las contraseñas y delegar la administración de grupo. 

    >[AZURE.NOTE] Estas funciones se pueden controlar un administrador a través de la directiva de grupo.

- Situaciones donde la red local y una red virtual Azure aplicaciones de nube de hospedaje no están directamente relacionados con un túnel VPN o circuito ExpressRoute.

Tenga en cuenta que AAD no proporciona todas las funciones de AD. Por ejemplo, AAD actualmente solo controladores de autenticación de usuario en lugar de la autenticación del equipo. Algunas aplicaciones y servicios, como SQL Server, pueden requerir autenticación del equipo en cuyo caso esta solución no es adecuada. Además, AAD no sea adecuado para sistemas donde componentes podrían migrar a través del límite de local y la nube en lugar de sólo se copian.

> [AZURE.NOTE] Para obtener una explicación detallada del funcionamiento de Azure Active Directory, vea [Microsoft Active Directory explicación][aad-explained].

## <a name="architecture-diagram"></a>Diagrama de arquitectura

El siguiente diagrama resalta los componentes importantes en esta arquitectura. Para obtener más información sobre los elementos de la carga de trabajo en Azure, lea [Ejecuta máquinas virtuales para una arquitectura de N niveles en Azure][implementing-a-multi-tier-architecture-on-Azure]:

> [AZURE.NOTE] Para simplificar, este diagrama muestra las conexiones directamente relacionadas con AAD solo y describir redirección de solicitud de explorador web u otro protocolo tráfico relacionado que puede producirse como parte del proceso de federación de identidad y autenticación. Por ejemplo, un usuario (local o remota) normalmente tiene acceso a una aplicación web mediante un explorador y la aplicación web transparente puede redirigir el navegador web para autenticar la solicitud a través de AAD. Una vez autenticada, la solicitud se puede pasar a la aplicación web junto con la información de identidad adecuada.

[! [0]][0]

- **El inquilino de azure Active Directory**. Se trata de una instancia de AAD creado por su organización. Actúa como un servicio de directorio simple para aplicaciones de nube (contiene objetos copiados de la local AD) y proporciona servicios de identidad.

- **Subred del nivel de web**. Esta subred contiene máquinas virtuales que implementan una aplicación personalizada basada en nube desarrollada por su organización y para que AAD que puede actuar como un agente de identidad.

- **Local servidor AD DS**. Su organización probablemente ya tiene un servicio de directorio existentes como AD DS. Puede sincronizar muchos de los elementos en el directorio de AD DS (como usuarios y grupos) con AAD para habilitar AAD usar esta información para autenticar las identidades.

- **Servidor de sincronización de azure AD Connect**. Se trata de un equipo local que ejecuta el servicio de sincronización de Azure AD Connect. Instale este servicio con el software de Azure AD Connect. El servicio de sincronización de Azure AD Connect sincroniza información (usuarios, grupos, contactos, etc.) en las instalaciones AD a AAD en la nube. Por ejemplo, puede aprovisionar o anular los usuarios y grupos locales y estos cambios se propagarán a AAD. El servicio de sincronización de Azure AD Connect pasa información al inquilino AAD.

    >[AZURE.NOTE] Por motivos de seguridad, las contraseñas de usuario no se almacenan directamente en AAD. En su lugar, AAD contiene un hash cada contraseña. Esto es suficiente para verificar la contraseña de un usuario. Si un usuario requiere el restablecimiento de contraseña, debe ser realizadas en local y el nuevo hash enviado a AAD. Ediciones de AAD Premium incluyen características que pueden automatizar esta tarea para permitir a los usuarios restablecer sus contraseñas.

## <a name="recommendations"></a>Recomendaciones

Esta sección resumen las recomendaciones para implementar AAD como sigue.

- AD Connect
- Seguridad

### <a name="azure-ad-connect-sync-service-recommendations"></a>Recomendaciones de servicio de sincronización de Azure AD Connect

La sincronización se encarga de garantizar que información de identidad de los usuarios en la nube es coherente con la llevan a cabo de forma local. El propósito del servicio de sincronización de Azure AD Connect es mantener esta coherencia. Los siguientes puntos resumen de recomendaciones para implementar el servicio de sincronización de Azure AD Connect:

- Antes de implementar la sincronización de Azure AD Connect, debe determinar los requisitos de sincronización de la organización (lo que debe sincronizar de los dominios y con qué frecuencia. El artículo [determinar requisitos de sincronización de directorios] [ aad-sync-requirements] describe los puntos que debe tener en cuenta.

- Puede ejecutar el servicio de sincronización de Azure AD Connect mediante una máquina virtual o un equipo hospedado en local. Dependiendo de la volatilidad de la información en el directorio de AD, una vez que se ha realizado la sincronización inicial con AAD la carga en el servicio de sincronización de Azure AD Connect suele ser alto. Usar una máquina virtual permite escalar más fácilmente el servidor (supervisar la actividad como se describe en la sección [supervisión consideraciones](#monitoring-considerations) para determinar si es necesario).

- Si tiene varios dominios locales en un bosque, puede almacenar y sincronizar la información para todo el bosque en un único inquilino AAD (este es el enfoque recomendado). Filtrar la información de identidades que se producen en más de un dominio para que cada identidad sólo aparece una vez en AAD en lugar de ser duplicado de esto puede provocar incoherencias cuando se sincronizan los datos. Este enfoque requiere la implementación de varios servidores de sincronización de Azure AD Connect. Para obtener más información, consulte el escenario de varios AAD en la sección [topología consideraciones](#topology-considerations) . 

- Use los filtros para limitar los datos almacenados en AAD a solo lo que es necesarios. Por ejemplo, su organización no recomendable almacenar información sobre cuentas inactivas o no personal en AAD. El filtrado puede ser basadas en grupos, basado en el dominio, basado en la unidad organizativa o basado en atributos, y puede combinar filtros para generar reglas más complejas. Por ejemplo, puede seleccionar para sincronizar solo objetos mantenidos en un dominio que tienen un valor específico en un atributo seleccionado. Para obtener información detallada, consulte [sincronización de Azure AD Connect: configurar el filtrado de][aad-filtering].

- Para implementar alta disponibilidad para el servicio de sincronización de AD Connect, ejecute un servidor de ensayo secundario. Para obtener más información, consulte la sección [Consideraciones de topología](#topology-considerations) .

### <a name="security-recommendations"></a>Recomendaciones de seguridad

Los elementos siguientes resumen las recomendaciones de seguridad principal para la implementación de AAD, dependiendo de los requisitos de su organización:

- Administrar contraseñas de los usuarios.

    Si está utilizando una edición premium de AAD, puede habilitar contraseña reescritura de AAD al directorio local al realizar una instalación personalizada de Azure AD Connect:

    [! [9]][9]

    Esta característica permite a los usuarios restablecer sus contraseñas desde dentro del portal de Azure, pero sólo debe habilitarse después de revisar la directiva de seguridad de contraseña de su organización. Por ejemplo, puede restringir los usuarios que pueden cambiar sus contraseñas, y puede adaptar la experiencia de administración de la contraseña. Para obtener más información, consulte [Personalización de la administración de contraseñas para satisfacer las necesidades de su organización][aad-password-management].

- Mantener la protección de aplicaciones locales que se puede acceder de forma externa.

    Use el Proxy de aplicación Azure AD para proporcionar acceso controlado a las aplicaciones web locales de los usuarios externos a través de AAD. Este enfoque protege sus aplicaciones, no expone directamente a Internet. Solo los usuarios que tienen credenciales válidas en el directorio de Azure pueden llegar a las aplicaciones. Para obtener más información, vea el artículo [Habilitar Proxy de aplicación en el portal de Azure][aad-application-proxy].

- Supervisión activa AAD signos de actividad sospechosa.

    Considere usar AAD Premium P2 edition, que incluye la protección de la identidad de AAD. Protección de identidad usa algoritmos de aprendizaje de máquina adaptación y heurística para detectar anomalías y eventos que pueden indicar que se ha comprometido una identidad de riesgos. Por ejemplo, que puede detectar actividades potencialmente irregulares como irregulares sesión actividades, inicios de sesión de fuentes desconocidas o de direcciones IP con actividad sospechosa o inicios de sesión de los dispositivos que pueden infección. Con estos datos, protección de identidad genera informes y alertas que le permite investigar estos eventos riesgos y tomar la acción de reducción o la corrección correspondiente. Para obtener más información, consulte [Protección de identidad de Azure Active Directory][aad-identity-protection].

    También puede usar la característica de informes de AAD en el portal de Azure para supervisar sospechosas y otras actividades relacionadas con la seguridad que se producen dentro del sistema. AAD puede generar una serie de informes de resumen:

    [! [17]][17]

    Para obtener más información sobre cómo usar estos informes, consulte [Azure Active Directory Reporting guía][aad-reporting-guide].

## <a name="topology-considerations"></a>Consideraciones de topología

Si va a integrar un directorio local con AAD, configure Azure AD Connect para implementar una topología que mejor coincida con los requisitos de su organización. Topologías que admita Azure AD Connect incluyen las siguientes:

- **Bosque único, único directorio AAD**. En esta topología, use Azure AD Connect para sincronizar objetos y la información de identidad en uno o más dominios en un único bosque con un solo usuario AAD local. Esta es la topología predeterminada implementada con la instalación rápida de Azure AD Connect.

    [! [1]][1]

    No crear varios servidores de sincronización de Azure AD Connect para conectar dominios diferentes en el mismo bosque local al mismo inquilino AAD a menos que esté ejecutando un servidor de sincronización de Azure AD Connect en modo de ensayo (consulte servidor de ensayo a continuación).

- **Múltiples bosques, único directorio AAD**. Use esta topología si tiene más de un bosque local. Puede consolidar la información de identidad para que cada usuario único se representa una vez en el directorio AAD, incluso si existe el mismo usuario en más de un bosque. Todos los bosques usan el mismo servidor de sincronización de Azure AD Connect. El servidor de sincronización de Azure AD Connect no tiene que ser parte de un dominio, pero debe ser accesible para todos los bosques:

    [! [2]][2]

    En esta topología, no use independiente Azure AD Connect sincronizar servidores conectarse cada bosque local a un inquilino AAD único. Esto puede dar lugar a información de identidad duplicados en AAD si los usuarios están presentes en más de un bosque.

- **Múltiples bosques, topologías independientes**. Este enfoque permite combinar información de identidad de bosques independientes en un sola inquilino AAD. Esta estrategia es útil si se combinen bosques de diferentes organizaciones (después de una fusión o adquisición, por ejemplo) y la información de identidad para cada usuario se guarda en un solo bosque:

    Si se sincronizan las GAL en cada bosque, un usuario de un bosque puede haber en otro como contacto. Esto puede ocurrir si, por ejemplo, su organización ha implementado GALSync con el Administrador de identidad de Forefront 2010 o administrador de identidad de Microsoft 2016. En este escenario, puede especificar que los usuarios deben identificarse por su atributo de *correo* . También puede hacer coincidir identidades con los atributos *ObjectSID* y *msExchMasterAccountSID* . Esto es útil si tiene uno o más bosques de recursos con cuentas deshabilitadas.

- **Servidor de ensayo**. En esta configuración, ejecuta una segunda instancia del servidor de sincronización de Azure AD Connect en paralelo con la primera. Esta estructura admite escenarios como:

    - Alta disponibilidad.

    - Probar e implementar una nueva configuración del servidor de sincronización de Azure AD Connect.

    - Introducción a un servidor nuevo y para retirar una configuración antigua. 

    En estos casos, la segunda instancia se ejecuta en *modo de ensayo*. El servidor de los registros importados objetos y datos de sincronización en su base de datos, pero no pasa los datos a AAD. Solo cuando se deshabilita el modo provisional el servidor empezar a escribir datos AAD y también inicia contraseña realizar escritura-realiza una copia en los directorios local en su caso:

    [! [4]][4]

    Para obtener más información, vea [sincronización de Azure AD Connect: tareas operativas y consideraciones][aad-connect-sync-operational-tasks].

- **AAD varios directorios**. Se recomienda que cree un único directorio AAD para una organización, pero puede haber situaciones dónde debe información de particiones a través de directorios AAD independientes. En este caso, debe asegurarse de que cada objeto del bosque local aparece solo en un directorio AAD, para evitar problemas de reescritura de sincronización y la contraseña.

    Para implementar este escenario, configure independiente Azure AD Connect sincronizar servidores para cada directorio AAD y use los filtros para cada servidor de sincronización de Azure AD Connect funciona en un conjunto de objetos se excluyen mutuamente: 

    [! [5]][5]

Para obtener más información acerca de estas topologías, consulte [topologías para Azure AD Connect][aad-topologies].

## <a name="user-sign-in-considerations"></a>Consideraciones de inicio de sesión de usuario

De forma predeterminada, el servicio AAD supone que los usuarios inician sesión proporcionando la misma contraseña que utilicen local y el servidor de sincronización de Azure AD Connect configura la sincronización de contraseñas entre el dominio local y AAD. Para muchas organizaciones, esto es adecuado, pero debe tener en cuenta las directivas existentes y la infraestructura de su organización. Por ejemplo:

- La directiva de seguridad de su organización puede impedir que la sincronización de hash de contraseña en la nube.

- Puede solicitar que los usuarios experimentar SSO transparente (sin contraseña adicional solicita) al acceso a los recursos de la nube de dominio unido equipos en la red corporativa.

- Es posible que su organización ya tenga ADFS o un tercero federación proveedor implementado. Puede configurar AAD para utilizar esta infraestructura para implementar la autenticación y SSO en lugar de con información de contraseña en la nube.

Para obtener más información, vea [Azure AD conectar de inicio de sesión las opciones de][aad-user-sign-in].

## <a name="azure-ad-application-proxy-considerations"></a>Consideraciones de proxy de aplicación de Azure AD

Use Azure AD para proporcionar acceso a aplicaciones locales.

- Exponer aplicaciones web local mediante el componente de proxy de aplicación de Azure AD para administrar los conectores de proxy de aplicación. El conector de proxy de aplicación abre una conexión de red saliente al proxy de aplicación de Azure AD. Las solicitudes de los usuarios remotos se enrutan desde AAD a través de esta conexión para las aplicaciones web. Este mecanismo quita la necesidad de abrir puertos de entrada en el firewall local, reducción del ámbito de ataque expuesto por su organización.

Para obtener más información, consulte [aplicaciones de publicar mediante proxy de aplicación de Azure AD][aad-application-proxy].

## <a name="object-synchronization-considerations"></a>Consideraciones de sincronización de objeto

La configuración predeterminada de Azure AD Connect sincroniza objetos desde el directorio local de AD basada en el conjunto de reglas que se especifican en el artículo [sincronización de Azure AD Connect: descripción de la configuración predeterminada][aad-connect-sync-default-rules]. Solo los objetos que cumplen estas reglas se sincronizan, otras personas se pasan por alto. Por ejemplo, los objetos de usuario deben tener un atributo único *sourceAnchor* y el atributo *accounEnabled* debe rellenarse. Objetos de usuario que no tiene un atributo *sAMAccountName* o que comienzan con el texto *AAD_* o *MSOL_* no están sincronizados. Azure AD Connect aplica a varias reglas a los objetos de usuario, contacto, grupo, ForeignSecurityPrincipal y equipo. Si necesita modificar el conjunto de reglas predeterminado, use el Editor de reglas de sincronización instalada con Azure AD Connect (también documentado en [sincronización de Azure AD Connect: descripción de la configuración predeterminada][aad-connect-sync-default-rules]).

Puede definir sus propios filtros para limitar los objetos que se sincronizarán con el dominio o unidad organizativa. O implementar filtrado personalizadas más complejas, como se describe en [sincronización de Azure AD Connect: configurar el filtrado de][aad-filtering].

## <a name="security-considerations"></a>Consideraciones de seguridad

Use el control de acceso condicional denegar las solicitudes de autenticación de forma inesperada:

- Desencadenar la [Autenticación multifactor de Azure (AMF)] [ azure-multifactor-authentication] si un usuario intenta conectarse desde una ubicación no es de confianza (como a través de Internet) en lugar de una red de confianza.

- Use el tipo de plataforma de dispositivo del usuario (iOS, Android, Windows Mobile, Windows) para determinar la directiva de acceso a las aplicaciones y características.

- Registrar el estado de habilitado o deshabilitado de dispositivos de los usuarios y a continuación, incorporar esta información en las comprobaciones de directiva de acceso. Por ejemplo, si la pérdida o robo de teléfono de un usuario se registrará como deshabilitado para impedir que se utiliza para tener acceso.

- Controlar el nivel de acceso a un usuario en función de la pertenencia a grupos. Usar [reglas de pertenencia dinámica AAD] [ aad-dynamic-membership-rules] para simplificar la administración de grupo. Para una breve introducción sobre cómo funciona esto, vea [Introducción a dinámico pertenencias a grupos][aad-dynamic-memberships].

- Utilice las directivas de acceso condicional riesgo con protección de la identidad de AAD para proporcionar protección avanzada en función de las actividades de inicio de sesión inusuales u otros eventos.

Para obtener más información, consulte [acceso condicional de Azure Active Directory][aad-conditional-access].

## <a name="scalability-considerations"></a>Consideraciones de escalabilidad

Escalabilidad se resuelve mediante el servicio AAD y la configuración del servidor de sincronización de Azure AD Connect:

- Para el servicio AAD, no tiene que configurar las opciones para implementar escalabilidad. El servicio AAD admite escalabilidad basado en réplicas. AAD implementa una única réplica principal, que controla las operaciones de escritura, y varias réplicas secundarias de solo lectura. AAD transparente redirige escribe intento contra réplicas secundarias en la réplica principal. AAD proporciona la coherencia. Todos los cambios realizados a la réplica principal se propagan a las réplicas secundarias. Como la mayoría de las operaciones contra AAD es lecturas en lugar de escritura, esta arquitectura escala bien.

    Para obtener más información, vea [Azure AD: Mostrar opciones avanzadas de nuestro directorio de nube geo redundantes, altamente disponibles y distribuidos][aad-scalability].

- Para el servidor de sincronización de Azure AD Connect, debe determinar el número de objetos, es probable que sincronizar desde el directorio local. Si tiene menos de 100000 objetos, puede usar el software de SQL Server Express LocalDB predeterminado proporcionado con Azure AD Connect. Si tiene un mayor número de objetos, debe instalar una versión de producción de SQL Server y realizar una instalación personalizada de la especificación de Azure AD Connect que debe utilizar una instancia existente de SQL Server.

## <a name="availability-considerations"></a>Consideraciones de disponibilidad

Como ocurre con los problemas de escalabilidad, disponibilidad abarca el servicio AAD y la configuración de Azure AD Connect:

- El servicio AAD está diseñado para proporcionar alta disponibilidad. No hay ninguna opción disponibilidad configurable por el usuario. Es distribuido geo y se ejecuta en varios datos centros ver todo el mundo, con la migración tras error automatizada. Si un centro de datos no está disponible, AAD garantiza que los datos de directorio están disponibles para el acceso a la instancia en al menos dos más centros de datos regional disperso.

    >[AZURE.NOTE] El SLA para AAD básico y Premium garantías al menos un 99,9% disponibilidad de servicios de. No hay ningún SLA para el nivel de AAD gratuito. Para obtener más información, vea el [SLA de Azure Active Directory][sla-aad].

- Para aumentar la disponibilidad del servidor de sincronización de Azure AD Connect puede ejecutar una segunda instancia de ensayo modo, como se describe en la sección [topología consideraciones](#topology-considerations) . 

    Además, si no está utilizando la instancia de SQL Server Express LocalDB que viene con Azure AD Connect, debe considerar alta disponibilidad para SQL Server. Tenga en cuenta que la solución de disponibilidad alta solo admitida clústeres de SQL. Soluciones como reflejo y siempre en no son compatibles con Azure AD Connect.

    Para otras consideraciones sobre el mantenimiento de la disponibilidad del servidor de sincronización de Azure AD Connect y cómo recuperar después de un error, consulte [sincronización de Azure AD Connect: tareas operativas y consideraciones - recuperación][aad-sync-disaster-recovery].

## <a name="management-considerations"></a>Consideraciones de administración

Hay dos aspectos administrando AAD:

1. Administración de AAD en la nube.

2. Mantenimiento de los servidores de sincronización de Azure AD Connect.

AAD proporciona las siguientes opciones de administración de dominios y directorios de la nube:

- [Módulo de PowerShell de Azure Active Directory][aad-powershell]. Utilice este módulo si necesita Azure AD tareas administrativas comunes, como la administración de usuarios, administración de dominios y para configurar el inicio de sesión único de secuencias de comandos.

- Módulo de administración de Azure AD en el portal de Azure. Este módulo proporciona una vista interactiva de administración del directorio y le permite controlar y configurar la mayoría de los aspectos de AAD.

    [! [10]][10]

Azure AD Connect instala las siguientes herramientas que utiliza para mantener los servicios de sincronización de Azure AD Connect de sus equipos locales:

- La consola de Microsoft Azure Active Directory conectar. Esta herramienta permite modificar la configuración del servidor de sincronización de Azure AD, personalizar cómo se produce la sincronización, habilitar o deshabilitar el modo de ensayo y cambie el inicio de sesión en modo de usuario (puede habilitar AD FS sesión usando su infraestructura local).

- El Administrador de servicios de sincronización. Usar la ficha *operaciones* en esta herramienta para administrar el proceso de sincronización y detectar si cualquier parte del proceso de error. Puede activar sincronizaciones manualmente con esta herramienta. 

    [! [12]][12]

    La ficha *conectores* le permite controlar las conexiones para los dominios (local y en la nube) al que se asocia el motor de sincronización:

    [! [13]][13]

-  El Editor de reglas de sincronización. Use esta herramienta para personalizar el modo en que se transforman los objetos cuando se copian entre un directorio local y AAD en la nube. Esta herramienta permite especificar atributos adicionales y objetos para la sincronización y filtros para determinar qué instancias deben o no se sincronizarán.

    Para obtener más información, vea la sección Editor de reglas de sincronización en el documento [sincronización de Azure AD Connect: descripción de la configuración predeterminada][aad-connect-sync-default-rules].

La página [sincronización de Azure AD Connect: procedimientos recomendados para cambiar la configuración predeterminada] [ aad-sync-best-practices] contiene información y sugerencias para la administración de Azure AD Connect adicionales.

## <a name="monitoring-considerations"></a>Consideraciones de supervisión

Realizar la supervisión de estado mediante una serie de agentes instalados local:

- Azure AD Connect instala a un agente que captura información acerca de las sincronizaciones. Use el módulo Azure Active Directory conectar estado en el portal de Azure para supervisar el mantenimiento y rendimiento de Azure AD Connect. Para obtener más información, vea [Usar Azure AD conectar estado de sincronización][aad-health].

- Para supervisar el mantenimiento de los dominios de AD DS y directorios de Azure, instale la Azure AD conectar de mantenimiento de agente de AD DS en un equipo dentro del dominio local. Use el módulo Azure Active Directory conectar estado en el portal de Azure al monitor de AD DS. Para obtener más información, consulte [Uso de Azure AD conectar estado con AD DS][aad-health-adds]

- Instale el estado de conexión AD Azure para agente de AD FS supervisar el mantenimiento de AD FS que se ejecuta en local y use el módulo Azure Active Directory conectar estado en el portal de Azure para supervisar AD DS. Para obtener más información, consulte [Uso de Azure AD conectar estado con AD FS][aad-health-adfs]

Para obtener más información sobre cómo instalar los agentes de estado de conexión de AD y sus requisitos, vea [Azure AD conectar estado agente de instalación de][aad-agent-installation].

## <a name="sample-solution"></a>Solución de ejemplo

Esta sección documenta los pasos necesarios para crear una solución de ejemplo que puede usar para probar la configuración de AAD. La solución consta de los siguientes elementos:

- Una aplicación web de n niveles, siguiendo la estructura descrita por [Ejecuta máquinas virtuales para una arquitectura de N niveles en Azure][implementing-a-multi-tier-architecture-on-Azure].

- Un equipo cliente de prueba. Se recomienda usar otra VM para este equipo. La configuración de este VM no es importante, siempre y cuando tenga acceso de administrador y se puede conectar a la aplicación web de capas.

- Un entorno local simulada que contiene su propio dominio creada con AD DS.

Se muestran los escenarios que muestran estos pasos:

- Habilitar el acceso a la aplicación web de n niveles con en la nube a usuarios externos, AAD que permite la autenticación de contraseña.

- Permitir el acceso a la aplicación web de n niveles que se ejecuta en la nube a los usuarios que ejecutan dentro de la organización con AAD que permite la autenticación de contraseña.

### <a name="prerequisites"></a>Requisitos previos

Siga los pasos, suponen los siguientes requisitos previos:

- Tiene una suscripción existente de Azure en la que puede crear grupos de recursos.

- Ha instalado la [interfaz de línea de comandos de Azure][azure-cli].

- Haya descargado e instalado la versión más reciente. Consulte [aquí] [ azure-powershell-download] para obtener instrucciones.

- Ha instalado una copia de la [makecert] [ makecert] utilidad en el equipo cliente de prueba.

- Tener acceso a un equipo de desarrollo que tiene instalado Visual Studio.

### <a name="create-the-n-tier-web-application-architecture"></a>Crear la arquitectura de la aplicación web de capas

1. Cree una carpeta denominada `Scripts` que contiene una subcarpeta denominada `Parameters`.

2. Descargar la [Implementación ReferenceArchitecture.ps1] [ solution-script] secuencia de comandos de PowerShell a la carpeta de secuencias de comandos.

3. En la carpeta parámetros, cree otra subcarpeta denominada Windows.

4. Descargar los archivos siguientes a la carpeta de Windows parámetros:

    - [virtualNetwork.parameters.json][vnet-parameters-windows]

    - [networkSecurityGroup.parameters.json][nsg-parameters-windows]

    - [webTierParameters.json][webtier-parameters-windows]

    - [businessTierParameters.json][businesstier-parameters-windows]

    - [dataTierParameters.json][datatier-parameters-windows]

    - [managementTierParameters.json][managementtier-parameters-windows]

5. Editar el archivo de **Implementación ReferenceArchitecture.ps**1 en la carpeta de secuencias de comandos y cambie la línea siguiente para especificar el grupo de recursos que se debe creado o utilizado para mantener la máquina virtual y los recursos creados por la secuencia de comandos:

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-ntier-rg"
        ```

6. Modificar los siguientes archivos en la carpeta **parámetros o ventana**y establecer la `resourceGroup` valor en la `virtualNetworkSettings` sección en cada uno de estos archivos para ser igual que especificó en el archivo de script de **Implementación ReferenceArchitecture.ps1** .

    - networkSecurityGroup.parameters.json

    - webTierParameters.json

    - businessTierParameters.json

    - dataTierParameters.json

    - managementTierParameters.json

7. Abra una ventana de PowerShell de Azure, mover a la carpeta de secuencias de comandos y ejecute el siguiente comando:

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows ntier
        ```

    Reemplazar **<subscription id>** con su identificador de suscripción de Azure.

    Para **<location>**, especifique un área de Azure, como *eastus* o *westus*.

8. Cuando haya finalizado la secuencia de comandos, utilice el portal de Azure para obtener la dirección IP pública del equilibrador de carga de nivel de web (*ra aad n web libras*):

    [! [18]][18]

9. Inicie sesión en su equipo de cliente de prueba (o VM) y compruebe que tiene acceso el nivel de web con Internet Explorer para ir a la dirección IP pública del equilibrador de carga de nivel de web. Debe aparecer la página IIS predeterminada.

### <a name="simulate-configuration-of-a-public-web-site"></a>Simular la configuración de un sitio web público

>[AZURE.NOTE] Los siguientes pasos simulan asociar el nivel de web con el nombre de DNS www.contoso.com modificando el archivo hosts en el equipo cliente de prueba. Además, las máquinas virtuales de nivel de web están configuradas con certificados autofirmados y falso autoridad de hospedaje. Esto es para probar únicamente y **que no debe utilizar estas técnicas en un entorno de producción**.

1. En el equipo cliente de prueba, editar el archivo **C:\Windows\System32\drivers\etc\hosts** mediante el Bloc de notas y agregue una entrada que asocia el nombre www.contoso.com a la dirección IP pública del equilibrador de carga de nivel de web:

        ```text
        # Copyright (c) 1993-2009 Microsoft Corp.
        #
        # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
        #
        # This file contains the mappings of IP addresses to host names. Each
        # entry should be kept on an individual line. The IP address should
        # be placed in the first column followed by the corresponding host name.
        # The IP address and the host name should be separated by at least one
        # space.
        #
        # Additionally, comments (such as these) may be inserted on individual
        # lines or following the machine name denoted by a '#' symbol.
        #
        # For example:
        #
        #      102.54.94.97     rhino.acme.com          # source server
        #       38.25.63.10     x.acme.com              # x client host
        
        # localhost name resolution is handled within DNS itself.
        #   127.0.0.1       localhost
        #   ::1             localhost
        
        52.165.38.64    www.contoso.com
        ```

2. Compruebe que ahora puede ir a www.contoso.com desde el equipo cliente de prueba. La misma página IIS predeterminada debería aparecer como antes.

3. En el equipo cliente de prueba, abra un símbolo del sistema como administrador y usar la herramienta makecert a c
4. crear un certificado de entidad emisora de certificados de raíz falso:

        ```
        makecert -sky exchange -pe -a sha256 -n "CN=MyFakeRootCertificateAuthority" -r -sv MyFakeRootCertificateAuthority.pvk MyFakeRootCertificateAuthority.cer -len 2048
        ```

    Comprobar que se crean los archivos siguientes:

    - MyFakeRootCertificateAuthority.cer

    - MyFakeRootCertificateAuthority.pvk

4. Ejecute el comando siguiente para instalar la entidad de certificación raíz de prueba:

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. Utilice la entidad emisora de prueba para generar un certificado de www.contoso.com:

        ```
        makecert -sk pkey -iv MyFakeRootCertificateAuthority.pvk -a sha256 -n "CN=www.contoso.com" -ic MyFakeRootCertificateAuthority.cer -sr localmachine -ss my -sky exchange -pe
        ```

6. Ejecute el comando **mmc** y agregue el complemento de certificados para la cuenta de equipo para el equipo local.

7. En el */Certificates (equipo Local) / Personal/certificados/* almacenar, exporte el certificado de www.contoso.com con su clave privada a un archivo llamado www.contoso.com.pfx:

    [! [20]][20]

8. Vuelva al portal de Azure y conectar con el nivel de administración VM (*ra aad n administración vm1*). El nombre de usuario predeterminado es *usuario de prueba* con contraseña *AweS0me@PW*:

    [! [21]][21]
    
9. Desde el nivel de administración de VM, conectarse a cada uno de la capa de web máquinas virtuales por separado con el *usuario de prueba* de nombre de usuario y contraseña *AweS0me@PW*y realizar las siguientes tareas. Tenga en cuenta que las máquinas virtuales tienen la dirección IP de direcciones privadas 10.0.1.4, 10.0.1.5 y 10.0.1.6:

    >[AZURE.NOTE] El nivel de web máquinas virtuales solo tiene direcciones IP. Solo puede conectarse a ellas mediante el uso de la máquina virtual de nivel de administración.

    1. Copie los archivos *www.contoso.com.pfx* y *MyFakeRootCertificateAuthority.cer* desde el equipo cliente de prueba.
    
    2. Abra un símbolo del sistema como administrador, mover a la carpeta que contiene los archivos que ha copiado y ejecute los comandos siguientes:
    
        ```
        certutil.exe -privatekey -importPFX my www.contoso.com.pfx NoExport

        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

    3. Ejecutar la `mmc` comando, agregar el complemento de certificados para la cuenta de equipo para el equipo local y compruebe que se han instalado los certificados siguientes:

        - \Certificates (equipo local) \Personal\Certificates\ www.contoso.com

        - Authorities\Certificates\MyFakeRootCertificateAuthority de certificación de raíz de \Trusted \Certificates (equipo local)

    4. Inicie la consola del Administrador de Internet Information Services (IIS) y vaya al *sitio Web de Sitios\Nombre* en el equipo.

    5. En el panel de *acciones* , haga clic en *los enlaces*y agregar un enlace de https utilizando el certificado SSL de www.contoso.com:

        [! [22]][22]

10. Vuelva al equipo cliente de prueba y compruebe que puede buscar ahora https://www.contoso.com.

### <a name="create-an-azure-active-directory-tenant"></a>Cree a un inquilino de Azure Active Directory

1. Con el portal de Azure, cree un inquilino de Azure Active Directory como *myaadname*. onmicrosoft.com, donde *myaadname* es un nombre de directorio seleccionado por el usuario.

2. Agregar un usuario denominado *Administrador* con la función GlobalAdmin en el directorio. Anote la contraseña recién generada.

3. Usar Internet Explorer, vaya a https://account.activedirectory.windowsazure.com/ e iniciar sesión como admin@ *myaadname*. onmicrosoft.com. Cambiar la contraseña cuando se le solicite.

### <a name="create-and-deploy-a-test-web-application"></a>Crear e implementar una aplicación web de prueba

1. Con Visual Studio en el equipo de desarrollo, cree una aplicación Web ASP.NET denominada ContosoWebApp1 (use .NET Framework 4.5.2):

    [! [23]][23]

2. Seleccione la plantilla *MVC* , cambie la autenticación a *las cuentas de trabajo y escuela*y especifique el nombre de su inquilino AAD. No crear un servicio de aplicación en la nube.

    [! [24]][24]

3. Crear y ejecutar la aplicación para probar la autenticación. En la página de inicio de sesión, escriba el nombre de cuenta admin@ *myaadname*. onmicrosoft.com, proporcione la contraseña y, a continuación, haga clic en *iniciar sesión en*:

    [! [25]][25]

4. Compruebe que AAD le pide permiso para iniciar sesión y leer su perfil y empieza a continuación, ejecuta la aplicación web con la identidad de administrador.

5. Cierre Internet Explorer y vuelva a Visual Studio.

6. Abra el archivo web.config y, en la `<appSettings>` sección, cambie el valor de la clave de *ida: PostLogoutRedirectUri* *https://www.contoso.com:443 /*. Guarde el archivo.

7. En la ventana *Explorador de soluciones* , haga clic en el proyecto ContosoWebApp1, haga clic en *Publicar*.

8. En la ventana de la *Publicación Web* , haga clic en *personalizado*. Crear un perfil personalizado denominado *ContosoWebApp1*.

9. En la página de *conexión* , establezca el *método de publicación* en el *Sistema de archivos* y especifique una carpeta denominada *ContosoWebApp*, ubicado en una ubicación adecuada en el equipo de desarrollo.

10. En la página *configuración* , establezca la *configuración* en la *versión*.

11. En la página de *vista previa* , haga clic en *Publicar*.

12. Conectarse a cada servidor web por separado (mediante el nivel de administración de VM) y realice las siguientes tareas:

    1. Copie la carpeta *ContosoWebApp* y su contenido desde el equipo de desarrollo a la carpeta *C:\inetpub* .

    2. Con la consola del Administrador de Internet Information Services (IIS), vaya al *sitio Web de Sitios\Nombre* en el equipo.

    3. En el panel de *acciones* , haga clic en *Configuración de base*y cambiar la ruta de acceso física del sitio web a *%SystemDrive%\inetpub\ContosoWebApp*:

        [! [26]][26]

### <a name="publish-the-test-web-application-through-aad"></a>Publicar la aplicación web de prueba a través de AAD

1. Inicie sesión en el portal de Azure y vaya al directorio de AAD.

2. En la ficha *aplicaciones* , haga clic en la aplicación ContosoWebApp1.

3. Compruebe que la aplicación se ha agregado correctamente en el directorio y, a continuación, haga clic en la ficha *Configurar* .

4. Cambiar la *Dirección URL de inicio de sesión activado* a https://www.contoso.com:443 y establecer la *Dirección URL de respuesta* a https://www.contoso.com:443 (la misma dirección de URL).

5. Guardar la configuración.

6. En el equipo cliente de prueba, vaya a https://www.contoso.com. Compruebe que AAD le solicitará las credenciales y, a continuación, inicie sesión en.

>[AZURE.NOTE] Éste es el extremo del primer escenario: permitir el acceso a la aplicación web de n niveles con en la nube a usuarios externos, AAD que permite la autenticación de contraseña.

### <a name="create-a-simulated-on-premises-environment-with-active-directory"></a>Crear un entorno local simulado con Active Directory

El entorno local contiene dos controladores de dominio para el `contoso.com` dominio y dos servidores para hospedar Azure AD Connect sincronización servicio. Los servidores para hospedar Azure AD Connect no se unen por dominio.

1. En el Explorador de archivos, vuelva a la carpeta de secuencias de comandos que contiene la secuencia de comandos usado para crear la aplicación web de capas.

2. En la carpeta parámetros, agregue otra carpeta de sub denominado `Onpremise`.

3. Descargar los archivos siguientes a la carpeta de parámetros/local:

    - [Agregar-agrega-dominio-controller.parameters.json][add-adds-domain-controller-parameters]

    - [crear-agrega-bosque-extension.parameters.json][create-adds-forest-extension-parameters]

    - [virtualMachines adc.parameters.json][virtualMachines-adc-parameters]

    - [adc de virtualMachines de joindomain.parameters.json][virtualMachines-adc-joindomain-parameters]

    - [virtualMachines adds.parameters.json][virtualMachines-adds-parameters]

    - [virtualNetwork.parameters.json][virtualNetwork-parameters]

    - [dns.parameters.json agrega virtualNetwork][virtualNetwork-adds-dns-parameters]

5. Editar el archivo de implementación ReferenceArchitecture.ps1 en la carpeta de secuencias de comandos y cambie la línea siguiente para especificar el grupo de recursos que se debe creado o utilizado para mantener la máquina virtual y los recursos creados por la secuencia de comandos:

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-onpremise-rg"
        ```

6. Modificar los siguientes archivos en la carpeta parámetros/local y establecer la `resourceGroup` valor en el `virtualNetworkSettings` sección en cada uno de estos archivos para ser igual que especificó en el archivo de script de implementación ReferenceArchitecture.ps1.

    - virtualMachines adds.parameters.json

    - virtualMachines adc.parameters.json

    - virtualNetwork.parameters.json

    - dns.parameters.json agrega virtualNetwork

7. Abra una ventana de PowerShell de Azure, mover a la carpeta de secuencias de comandos y ejecute el siguiente comando:

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows onpremise
        ```

    Reemplazar `<subscription id>` con su identificador de suscripción de Azure.

    Para `<location>`, especifique un área de Azure, como por ejemplo `eastus` o `westus`.

### <a name="install-and-configure-the-azure-ad-connect-sync-service"></a>Instalar y configurar el servicio de sincronización de Azure AD Connect

La configuración que se muestra en estos pasos se compone de dos instancias del servicio de sincronización de Azure AD Connect. La primera está activa, mientras que el segundo está configurado en modo provisional para proporcionar alta disponibilidad y failover rápido si se produce un error en el primer servidor.

1. Con el portal de Azure, vaya al grupo de recursos contiene las máquinas virtuales de los servicios de sincronización de Azure AD Connect (*ra-aad-local-c* de forma predeterminada) y conectarse a *ra aad directo adc vm1* VM. Inicie sesión como *usuario de prueba* con contraseña *AweS0me@PW*.

2. Descargar Azure AD Connect desde [aquí][aad-connect-download].

3. Ejecute al instalador de Azure AD Connect y realizar una instalación mediante la opción *Personalizar* .

    >[AZURE.NOTE] No puede usar la opción *Configuración rápida* porque la máquina virtual que aloja el servicio de sincronización de Azure AD Connect no está unido al dominio.

4. En la página *instalar componentes necesarios* , deje en blanco para aceptar las opciones predeterminadas de los valores de configuración opcionales.

5. En la página *de inicio de sesión de usuario* , seleccione *La sincronización de contraseñas*.

6. En la página *conectarse a Azure AD* , escriba admin@ *myaadname*. onmicrosoft.com, donde *myaadname* es el nombre de su inquilino AAD. Escriba la contraseña de la cuenta de administrador.

7. En la página *conectarse a los directorios* , especifique contoso.com para el bosque (escriba el valor en porque no aparece en la lista desplegable), escriba CONTOSO\testuser para el nombre de usuario, especifique AweS0me@PW la contraseña y, a continuación, haga clic en *Agregar directorio*.

8. En la página de *configuración de inicio de sesión de Azure AD* , acepte el valor predeterminado para el nombre de usuario principal. Active *continuar sin cualquier dominio comprobado*.

    >[AZURE.NOTE] El directorio de contoso.com aparecen como *No verificados*. Para comprobar un nombre de dominio, debe crear un registro TXT para el registrador de nombre de dominio. En este ejemplo, el dominio local no está registrado externamente. Para obtener más información, vea [Agregar un nombre de dominio personalizado para Azure Active Directory][aad-custom-directory].

9. En la página *dominios y OU filtrado* , seleccione *sincronizar todos los dominios y unidades organizativas* (opción predeterminada).

10. En la página *identifica los usuarios* , acepte los valores predeterminados.

11. En la página *usuarios y dispositivos de filtro* , seleccione *sincronizar todos los usuarios y dispositivos* (opción predeterminada).

12. En la página *características opcionales* , seleccione *reescritura de contraseña*.

13. En la página *listo para configurar* , seleccione *iniciar el proceso de sincronización cuando se complete la configuración*, pero dejar *Habilitar el modo de ensayo* sin seleccionar.

14. Compruebe que el proceso de configuración se completa sin errores y, a continuación, salir del instalador.

15. Desde el portal de Azure, conectar *ra aad directo adc vm2* VM. Inicie sesión como *usuario de prueba* con contraseña *AweS0me@PW*.

16. Descargar Azure AD Connect y, a continuación, ejecute al instalador.

17. Recorra el asistente y responder como se describe en los pasos 3 a 12.

18. En la página *listo para configurar* , seleccione *iniciar el proceso de sincronización cuando se complete la configuración*y **Seleccione también** *activar el modo de ensayo*. Esto hace que el servicio de sincronización de Azure AD Connect para recuperar los detalles sobre las cuentas y los objetos del dominio contoso.com y guardarlos en su base de datos, pero no transmite estos detalles en el inquilino de AAD.

14. Compruebe que el proceso de configuración se completa sin errores y, a continuación, salir del instalador.

### <a name="test-the-aad-configuration"></a>Probar la configuración de AAD

1. Con el portal de Azure, cambie al directorio de AAD, abra el módulo Azure Active Directory, haga clic en *usuarios y grupos*y, a continuación, haga clic en *todos los usuarios* para mostrar la lista de usuarios y grupos sincronizados con el directorio. Debería ver usuarios para las cuentas siguientes:

    - administración (admin@ *myaadname*. onmicrosoft.com)

    - Cuenta de servicio de sincronización de directorio local (Sync_ADC1_*nnnnnnnnnnnn*@*myaadname*. onmicrosoft.com)

    - Cuenta de servicio de sincronización de directorio local (Sync_ADC2_*nnnnnnnnnnnn*@*myaadname*. onmicrosoft.com)

2. En el portal de Azure, vaya al grupo de recursos contiene las máquinas virtuales de los controladores de dominio de AD DS (*ra-aad-local-c* de forma predeterminada) y conectarse a *ra aad directo ad vm1* VM. Inicie sesión como *usuario de prueba* con contraseña *AweS0me@PW*.

3. Con la consola de *equipos y usuarios de Active Directory* , agregue un nuevo usuario de dominio Diane Tibbot, un nombre de inicio de sesión dianet@contoso.com. Especificar una contraseña de su elección. Convertir el usuario a un miembro del grupo de administradores locales (que es por lo que puede sesión localmente como este usuario más adelante: los equipos solo en el dominio son DC).

4. Cambiar a la *ra aad directo adc vm1* VM, abra una ventana de PowerShell y ejecute los comandos siguientes:

        ```[powershell]
        Import-Module ADSync
        Start-ADSyncSyncCycle -PolicyType Delta
        ```

    Compruebe que el comando devuelve *correcto*.

    >[AZURE.NOTE] Este paso es necesario porque de forma predeterminada, el proceso de sincronización está programado para ejecutarse en intervalos de 30 minutos. Estos comandos forzar una sincronización inmediatamente.

5. Vuelva al portal de Azure, cambie al directorio de AAD, abrir el módulo Azure Active Directory, haga clic en *usuarios y grupos*y, a continuación, haga clic en *todos los usuarios*. Ahora debe ver Diane Tibbot (dianet@ *myaadname*. onmicrosoft.com) aparecen en la lista de usuarios.

6. En el módulo Azure Active Directory, haga clic en *Aplicaciones empresariales*y, a continuación, haga clic en *todas las aplicaciones*. Haga clic en la aplicación *ContosoWebApp1* y, a continuación, haga clic en *usuarios y grupos*. En la barra de herramientas, haga clic en *Agregar*. Haga clic en *usuarios y grupos*y a continuación, seleccione *Diane Tibbot*. Haga clic en *asignar*.

7. Usar Internet Explorer, vaya a la https://account.activedirectory.windowsazure.com sitios. Inicie sesión como dianet@ *myaadname*. onmicrosoft.com con la contraseña especificada anteriormente.

    >[AZURE.NOTE] Haga clic en el icono de ContosoWebApp en la lista de aplicaciones. AAD intenta encontrar la aplicación web en la dirección DNS enumerada públicamente por www.contoso.com, que es diferente de la dirección del equilibrador de carga de su nivel de web.

8. Haga clic en la ficha *perfil* . Deben mostrarse los detalles del usuario (si se ha especificado alguna cuando se creó).

    >[AZURE.NOTE] Si hace clic en *Cambiar contraseña*, no tiene permiso para realizar esta tarea, como esta operación debe estar habilitada por un administrador en primer lugar. Para obtener más información, vea [Introducción a la administración de contraseñas][aad-password-management].

### <a name="enable-on-premises-users-to-run-the-application-by-using-authentication-through-aad"></a>Permitir que los usuarios locales ejecutar la aplicación utilizando la autenticación a través de AAD

1. Volver a la máquina virtual de controlador de dominio *ra aad directo ad vm1* .

2. Abra la consola del *Administrador de DNS* y agregue un nuevo registro de host para www.contoso.com. Especifique la dirección IP pública del equilibrador de carga de nivel de web.

3. Copie el archivo *MyFakeRootCertificateAuthority.cer* desde el equipo cliente de prueba (creó este archivos en el procedimiento de [simulación de la configuración de un sitio web público](#simulate-configuration-of-a-public-web-site)
    
4. Abra un símbolo del sistema como administrador, mover a la carpeta que contiene el archivo que acaba de copiar y ejecute el siguiente comando:

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. Usar Internet Explorer, vaya a https://www.contoso.com. Compruebe que aparece la página de inicio de sesión de AAD para la aplicación web de ContosoWebApp1.

6. Inicie sesión como dianet@ *myaadname*. onmicrosoft.com. La aplicación debería ejecutar e inicie sesión correctamente.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre las prácticas recomendadas para [extender su dominio local agrega Azure][adds-extend-domain]

- Obtenga información sobre las prácticas recomendadas para [crear un bosque de recursos agrega] [ adds-resource-forest] en Azure

<!-- links -->
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[aad-explained]: https://youtu.be/tj_0d4tR6aM
[aad-editions]: ../active-directory/active-directory-editions.md
[guidance-adds]: ./guidance-iaas-ra-secure-vnet-ad.md
[sla-aad]: https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/
[azure-multifactor-authentication]: ../multi-factor-authentication/multi-factor-authentication.md
[aad-conditional-access]: ../active-directory//active-directory-conditional-access.md
[aad-dynamic-membership-rules]: ../active-directory/active-directory-accessmanagement-groups-with-advanced-rules.md
[aad-dynamic-memberships]: https://youtu.be/Tdiz2JqCl9Q
[aad-user-sign-in]: ../active-directory/active-directory-aadconnect-user-signin.md
[aad-sync-requirements]: ../active-directory/active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md
[aad-topologies]: ../active-directory/active-directory-aadconnect-topologies.md
[aad-filtering]: ../active-directory/active-directory-aadconnectsync-configure-filtering.md
[aad-scalability]: https://blogs.technet.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/
[aad-connect-sync-default-rules]: ../active-directory/active-directory-aadconnectsync-understanding-default-configuration.md
[aad-identity-protection]: ../active-directory/active-directory-identityprotection.md
[aad-password-management]: ../active-directory/active-directory-passwords-customize.md
[aad-application-proxy]: ../active-directory/active-directory-application-proxy-enable.md
[aad-connect-sync-operational-tasks]: ../active-directory/active-directory-aadconnectsync-operations.md#staging-mode
[aad-custom-domain]: ../active-directory/active-directory-add-domain.md
[aad-powershell]: https://msdn.microsoft.com/library/azure/mt757189.aspx
[aad-sync-disaster-recovery]: ../active-directory/active-directory-aadconnectsync-operations.md#disaster-recovery
[aad-sync-best-practices]: ../active-directory/active-directory-aadconnectsync-best-practices-changing-default-configuration.md
[aad-adfs]: ../active-directory/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs
[aad-health]: ../active-directory/active-directory-aadconnect-health-sync.md
[aad-health-adds]: ../active-directory/active-directory-aadconnect-health-adds.md
[aad-health-adfs]: ../active-directory/active-directory-aadconnect-health-adfs.md
[aad-agent-installation]: ../active-directory/active-directory-aadconnect-health-agent-install.md
[aad-reporting-guide]: ../active-directory/active-directory-reporting-guide.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-powershell-download]: ../powershell-install-configure.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/Deploy-ReferenceArchitecture.ps1
[vnet-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/webTier.parameters.json
[businesstier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/businessTier.parameters.json
[datatier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/dataTier.parameters.json
[managementtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/managementTier.parameters.json
[makecert]: https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/add-adds-domain-controller.parameters.json
[create-adds-forest-extension-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/create-adds-forest-extension.parameters.json
[virtualMachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adds.parameters.json
[virtualNetwork-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork.parameters.json
[virtualNetwork-adds-dns-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork-adds-dns.parameters.json
[virtualMachines-adc-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc.parameters.json
[virtualMachines-adc-joindomain-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc-joindomain.parameters.json
[aad-connect-download]: http://www.microsoft.com/download/details.aspx?id=47594
[aad-custom-directory]: ../active-directory/active-directory-add-domain.md
[aad-password-management]: ../active-directory/active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[0]: ./media/guidance-identity-aad/figure1.png "Arquitectura de identidad de nube con Azure Active Directory"
[1]: ./media/guidance-identity-aad/figure2.png "Un solo bosque, una topología de directorio AAD"
[2]: ./media/guidance-identity-aad/figure3.png "Varios bosques, topología de directorio AAD único"
[4]: ./media/guidance-identity-aad/figure5.png "Topología de servidor de ensayo"
[5]: ./media/guidance-identity-aad/figure6.png "Topología de varios AAD directorios"
[6]: ./media/guidance-identity-aad/figure7.png "Seleccionar una instalación personalizada de Azure sincronización de AD conectar con una instancia específica de SQL Server"
[7]: ./media/guidance-identity-aad/figure8.png "Especificar el método SSO para inicio de sesión de usuario"
[8]: ./media/guidance-identity-aad/figure9.png "Especificar el dominio y OU opciones de filtrado"
[9]: ./media/guidance-identity-aad/figure10.png "Habilitar reescritura de contraseña"
[10]: ./media/guidance-identity-aad/figure11.png "El módulo de administración de Azure AD en el portal"
[11]: ./media/guidance-identity-aad/figure12.png "La consola de Azure AD Connect"
[12]: ./media/guidance-identity-aad/figure13.png "La ficha de operaciones en el Administrador de servicios de sincronización"
[13]: ./media/guidance-identity-aad/figure14.png "La ficha conectores en el Administrador de servicios de sincronización"
[14]: ./media/guidance-identity-aad/figure15.png "El Editor de reglas de sincronización"
[15]: ./media/guidance-identity-aad/figure16.png "El módulo Azure Active Directory conectar estado en el portal de Azure que muestra el estado de sincronización"
[16]: ./media/guidance-identity-aad/figure17.png "El módulo Azure Active Directory conectar estado en el portal de Azure que muestra el estado de AD DS"
[17]: ./media/guidance-identity-aad/figure18.png "Informes de seguridad disponibles en el portal de Azure"
[18]: ./media/guidance-identity-aad/figure19.png "El portal de Azure resaltado de la dirección IP pública del equilibrador de carga de nivel de web"
[19]: ./media/guidance-identity-aad/figure20.png "Usar Internet Explorer para ir a la dirección IP pública del equilibrador de carga de nivel de web"
[20]: ./media/guidance-identity-aad/figure21.png "El complemento de certificados que muestra el certificado www.contoso.com"
[21]: ./media/guidance-identity-aad/figure22.png "Conectar con el nivel de administración VM"
[22]: ./media/guidance-identity-aad/figure23.png "Crear el enlace HTTPS para el sitio web predeterminado"
[23]: ./media/guidance-identity-aad/figure24.png "Crear la aplicación web de ContosoWebApp1"
[24]: ./media/guidance-identity-aad/figure25.png "Establecer las propiedades de autenticación de la aplicación web de ContosoWebApp1"
[25]: ./media/guidance-identity-aad/figure26.png "Iniciar sesión en Azure AAD desde la aplicación web de ContosoWebApp1"
[26]: ./media/guidance-identity-aad/figure27.png "Cambiar la carpeta para el sitio web predeterminado"