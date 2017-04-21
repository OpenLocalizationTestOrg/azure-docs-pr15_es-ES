<properties
   pageTitle="Referencia de arquitectura Azure - IaaS: Extensión de Active Directory en Azure | Microsoft Azure"
   description="Cómo implementar una arquitectura de red seguro híbrido con autorización de Active Directory en Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
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
   ms.date="07/19/2016"
   ms.author="telmos"/>

# <a name="extending-active-directory-directory-services-adds-to-azure"></a>Extensión de servicios de directorio de Active Directory (suma) en Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

En este artículo se describe los procedimientos recomendados para ampliar su entorno de Active Directory (AD) a Azure, para proporcionar servicios de autenticación distribuida mediante [Los servicios de dominio de Active Directory (AD DS)][active-directory-domain-services]. Esta arquitectura extiende que se describe en los artículos de la [implementación de una arquitectura de red seguro híbrido en Azure] [ implementing-a-secure-hybrid-network-architecture] e [implementar una arquitectura de red seguro híbrido con acceso a Internet en Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación: [El Administrador de recursos] [ resource-manager-overview] y clásica. Esta arquitectura de referencia utiliza el Administrador de recursos, que Microsoft recomienda para implementaciones nuevas.

Usar AD DS para autenticar las identidades. Estas identidades pueden pertenecer a los usuarios, equipos, aplicaciones u otros recursos que forman parte de un dominio de seguridad. Puede hospedar AD DS local, pero en un escenario híbrido donde se encuentran los elementos de una aplicación en Azure puede ser más eficaz para replicar esta funcionalidad y el repositorio de AD a la nube. Este enfoque puede ayudar a reducir la latencia causada mediante el envío de autenticación y solicitudes de autorización local de la nube a AD DS ejecutando local. 

Hay dos formas para hospedar los servicios de directorio de Azure:

1. Puede usar [Azure Active Directory (AAD)] [ azure-active-directory] para crear un nuevo dominio de AD en la nube y vincularlo a la implementación local dominio de Active Directory. A continuación, configure [Azure AD Connect] [ azure-ad-connect] promesa para replicar identidades mantenidas en el repositorio local en la nube. Tenga en cuenta que el directorio en la nube es **no** una extensión del sistema local, en su lugar es una copia que contiene las mismas identidades. Los cambios realizados en estas identidades locales se copiarán en la nube, pero los cambios realizados en la nube **no** se pueden replicar volver al dominio local. Por ejemplo, restablecimiento de contraseñas deberá realizar local y utilizar Azure AD Connect para copiar el cambio a la nube. Además, tenga en cuenta que la misma instancia de AAD puede estar vinculada a más de una instancia de AD DS; AAD contendrá las identidades de cada repositorio de AD a los que está vinculado.

    AAD es útil para las situaciones donde la red local y una red virtual Azure los recursos de la nube de hospedaje no están directamente relacionados con un túnel VPN o circuito ExpressRoute. Aunque esta solución es simple, podría no ser adecuado para sistemas donde componentes podrían migrar a través del límite de local y la nube como esto podría requerir la nueva configuración de AAD. Además, AAD sólo controladores de autenticación de usuario en lugar de la autenticación del equipo. Algunas aplicaciones y servicios, como SQL Server, pueden requerir autenticación del equipo en cuyo caso esta solución no es adecuada.

2. Puede implementar una máquina virtual ejecuta AD DS como un controlador de dominio en Azure, ampliación de la infraestructura de AD existente desde el centro de datos local. Este enfoque es más habitual para escenarios donde la red local y una red virtual Azure están conectadas por una conexión VPN o ExpressRoute. Esta solución también admite bidireccional replicación permitiéndole realizar cambios en la nube y local, siempre que sea más adecuado. Dependiendo de los requisitos de seguridad, puede ser la instalación de AD en la nube:
    - parte del mismo dominio como ese local mantenidos
    - un nuevo dominio dentro de un bosque compartido
    - un bosque independiente

<!-- we might want to add info on how to choose from the three options above -->

Esta arquitectura se centra en la solución 2, con el mismo dominio de AD DS en Azure y local.

Casos de uso común para esta arquitectura incluyen:

- Aplicaciones híbridas donde ejecutar las cargas de trabajo en parte local y en parte de Azure.

- Aplicaciones y servicios que realizan la autenticación con Active Directory.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

El siguiente diagrama resalta los componentes importantes en esta arquitectura (*haga clic para acercar*). Para obtener más información sobre los elementos atenuados, lea [implementar una arquitectura de red seguro híbrido en Azure] [ implementing-a-secure-hybrid-network-architecture] e [implementar una arquitectura de red seguro híbrido con acceso a Internet en Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[! [0]][0]

- **Red local.** La red local incluye los servidores locales de AD que pueden realizar la autenticación y la autorización para componentes que se encuentran en las instalaciones.

- **Servidores de AD.** Estos son los controladores de dominio implementar servicios de directorio (AD DS) que se ejecuta como máquinas virtuales en la nube. Estos servidores pueden proporcionar autenticación de componentes se ejecutan en su red virtual Azure.

- **Subred de Active Directory.** Los servidores de AD DS se hospedan en una subred independiente. Reglas de GSN protegen los servidores de AD DS y pueden proporcionar un firewall contra el tráfico de forma inesperada.

- **Sincronización de puerta de enlace de azure y AD.**. La puerta de enlace Azure proporciona una conexión entre la red local y VNet de Azure. Puede ser una [conexión VPN] [ azure-vpn-gateway] o [Azure ExpressRoute][azure-expressroute]. Todas las solicitudes de sincronización entre los servidores de AD en la nube y locales pasan a través de la puerta de enlace. Rutas definidas por el usuario (UDRs) controlan el enrutamiento de tráfico de sincronización que pasa directamente al servidor AD en la nube y no pasa por el existente virtual dispositivos de red (NVAs) utilizados en esta situación.

    Para obtener más información acerca de cómo configurar UDRs y la NVAs, vea [implementar una arquitectura de red seguro híbrido en Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Recomendaciones

Esta sección resumen las recomendaciones para la implementación de AD DS en Azure, que cubre:

- Recomendaciones de máquina virtual.

- Recomendaciones de redes.

- Recomendaciones de seguridad. 

- Recomendaciones de Active Directory sitio.

- Recomendaciones de colocación de Active Directory FSMO.

>[AZURE.NOTE] El documento [directrices para implementar Active Directory de Windows Server en Azure máquinas virtuales de Windows] [ ad-azure-guidelines] contiene información más detallada sobre muchos de estos puntos.

### <a name="vm-recommendations"></a>Recomendaciones de VM

Crear máquinas virtuales con recursos suficientes para controlar el volumen de tráfico esperado. Usar el tamaño de los equipos que alojan AD DS local como punto de partida. Supervisar el uso de recursos puede cambiar el tamaño de las máquinas virtuales y reducir si son demasiado grandes. Para obtener más información sobre cómo cambiar el tamaño de los controladores de dominio de AD DS, vea [Planear la capacidad de los servicios de dominio de Active Directory][capacity-planning-for-adds].

Crear un disco virtual de datos independiente para almacenar la base de datos, los registros y SYSVOL AD. No almacene estos elementos en el mismo disco que el sistema operativo. Observe que de forma predeterminada, los discos de datos que se han adjuntado a una máquina virtual utilizan escritura mediante almacenamiento en caché. Sin embargo, este formulario de almacenamiento en caché puede entrar en conflicto con los requisitos de AD DS. Por este motivo, establezca la configuración de *Preferencia de caché de Host* en el disco de datos en *Ninguno*. Para obtener más información, vea la [ubicación de la base de datos de Windows Server AD DS y SYSVOL][adds-data-disks].

Implementar al menos dos máquinas virtuales con AD DS como controladores de dominio a su red virtual Azure por motivos de [disponibilidad](#Availability-considerations) .

### <a name="networking-recommendations"></a>Recomendaciones de redes

Configurar la interfaz de red para cada una de las máquinas virtuales hospedaje AD DS con direcciones IP privadas. Esta configuración admite mejor DNS en cada una de las máquinas virtuales de AD. Para obtener más información, consulte [cómo configurar una dirección IP privada estática en el portal de Azure][set-a-static-ip-address].

> [AZURE.NOTE] No se proporcionan las direcciones IP públicas de máquinas virtuales de AD DS. Vea [Consideraciones de seguridad] [ security-considerations] para obtener más detalles.

Debe asegurarse de que el tráfico puede fluya libremente entre los servidores de AD en la nube y local:

- Agregar reglas de GSN a la subred de AD que permitan el tráfico entrante locales. Para obtener información detallada sobre los puertos que utilizan AD DS, vea [Active Directory y requisitos de puerto de servicios de dominio de directorio activo][ad-ds-ports].

- Asegúrese de que las tablas UDR no enrutar el tráfico de AD DS a través de la NVAs utilizados en esta situación. Para sus propias implementaciones, dependiendo de qué NVAs utiliza, desee redirigir el tráfico.

### <a name="security-recommendations"></a>Recomendaciones de seguridad

AD DS servidores controlen la autenticación y, por tanto, elementos muy confidenciales. Evitar la exposición directa de los servidores de AD DS a Internet. Coloque los servidores de AD DS en una subred independiente, con su propio firewall. Asegúrese de que los puertos necesarios para usar AD DS para la autenticación y la autorización y los servidores de synchronzing permanezcan abiertos, pero cerrar todos los demás puertos. Para obtener más información, vea [Active Directory y requisitos de puerto de servicios de dominio de directorio activo][ad-ds-ports]. Los [componentes de la solución] [ solution-components] sección más adelante en este documento se muestra la GSN reglas que utiliza la solución de ejemplo para abrir los puertos.

Puede usar reglas de GSN para crear un firewall simple. Si necesita una protección más completa que pueda implementar un perímetro de seguridad adicional alrededor de servidores con un par de subredes y NVAs, como se describe en el documento de [implementación de una arquitectura de red seguro híbrido con acceso a Internet en Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

Uso del cifrado de disco BitLocker o Azure para cifrar el disco que aloja la base de datos de AD DS.

### <a name="active-directory-site-recommendations"></a>Recomendaciones de Active Directory sitio

Puede usar sitios de AD DS a los controladores de dominio juntas de grupo que están conectados por un vínculo rápido. Controladores de dominio en el mismo sitio AD DS replican sus cambios de directorio automáticamente y, a continuación, configuración poco es necesaria para controlar la replicación.

PARA controlar el tráfico de replicación entre Azure y su centro de datos local, se recomienda para agregar un sitio de AD DS independiente para representar el espacio de direcciones utilizado por Azure. Puede configurar un vínculo de sitio entre su local AD DS sitios y controlar la replicación entre sitios de manera más eficaz.

También puede usar separación de sitio para aplicar objetos de directiva de grupo diferente (GPO) unido equipos en Azure y aprovechar las ventajas de las aplicaciones que son "sitio cuenta", como el Administrador de configuración de System Center.

### <a name="active-directory-fsmo-placement-recommendations"></a>Recomendaciones de colocación de Active Directory FSMO

Los servidores de operación de maestro único (FSMO) flexibles son controladores de dominio especializadas, reposnsible por coherencia de datos entre diferentes configuraciones en AD DS, que se muestran a continuación.

- **Maestro de esquema**. Se trata de una función de bosques que mantiene la estructura del esquema utilizado por AD DS.

- **Maestro de nombres de dominio**. Se trata de un rol de bosques que administra la información acerca de los nombres de dominio en el bosque.

- **Controlador de dominio principal (PDC)**. Esta es una función de todo el dominio. El PDC controla las actualizaciones de contraseña y bloqueos de cuenta. Otros DC consulta cuando las solicitudes de servicio autenticación tienen contraseñas que no coinciden. El PDC también controla las actualizaciones de directiva de grupo y es el DC de destino para las aplicaciones heredadas y algunas herramientas de administración que realizan algunas operaciones de escritura.

- **Patrón de identificador relativo (RID)**. RID se utilizan para ayudar a identificar los objetos dentro de un directorio. Este servidor es responsable de generar un grupo de todo el dominio de RID para usar todos los servidores de AD dentro del dominio.

- **Función de infraestructura**. Un objeto de un dominio puede hacer referencia a un objeto en otro dominio. Esta función de todo el dominio es responsable de actualizar el SID y el nombre completo en una referencia de objeto de dominios de un objeto. Observe que un servidor de implementar esta función no puede actuar también como un servidor de catálogo Global (catálogo global).

Para obtener más información, consulte [funciones de FSMO de Active Directory en Windows][AD-FSMO-roles-in-windows].

En este escenario, se recomienda que evitar la implementación de funciones FSMO a los controladores de dominio en Azure. 

## <a name="availability-considerations"></a>Consideraciones de disponibilidad

Crear una disponibilidad establecido para los servidores de AD. Asegúrese de que hay al menos dos servidores en el conjunto. Los servidores de AD en la nube deben ser controladores de dominio dentro del mismo dominio. Esto le permitirá la replicación automática entre servidores.

Además, considere la posibilidad de designar servidores como [maestros de operaciones en espera] [ standby-operations-masters] en caso de que se produce un error en la conectividad con un servidor que actúa como una función FSMO.

## <a name="security-considerations"></a>Consideraciones de seguridad

Si todas las tareas de administración de dominio están probable que se deben realizar mediante los controladores de dominio local, considere la posibilidad de que los controladores de dominio en la nube de sólo lectura. Un controlador de dominio de solo lectura solo mantiene un subconjunto de las credenciales de los usuarios (suficiente para realizar la autenticación localmente) y se puede configurar la información en caché únicamente para usuarios específicos. Por lo tanto, si se compromete a un controlador de dominio de solo lectura, solo la información de usuarios en caché se verán afectada, en lugar de los detalles de cada cuenta del dominio. Para obtener más información, vea [Controladores de dominio de sólo lectura][read-only-dc].

Para ayudar a minimizar la vulnerabilidad de cuentas de usuario individual y para impedir intentos de robo, siga el procedimiento recomendado para configurar y mantener contraseñas de usuario en Active Directory. Para obtener más información, consulte [Prácticas recomendadas para la aplicación de directivas de contraseña][best_practices_ad_password_policy]. Además, tenga cuidado de qué grupos de asignar a usuarios. Por ejemplo, no hace ordinarias usuarios miembros del grupo de administradores de empresa, grupo de administradores de esquema y grupo de administradores de dominio.

## <a name="scalability-considerations"></a>Consideraciones de escalabilidad

AD es automáticamente scalable controladores de dominio que forman parte del mismo dominio. Las solicitudes se distribuyen entre todos los controladores de un dominio. Puede agregar otro controlador de dominio y se sincronizará automáticamente con el dominio. No configure un equilibrador de carga independiente para dirigir el tráfico a los controladores del dominio. Asegúrese de que todos los controladores de dominio tienen suficiente memoria y recursos de almacenamiento para controlar la base de datos de dominio; hacen que el controlador de dominio de todas las máquinas virtuales del mismo tamaño.

## <a name="management-considerations"></a>Consideraciones de administración

No copie los archivos de disco duro virtual de controladores de dominio en lugar de realizar copias de seguridad periódicas porque Restaurar puede provocar incoherencias en estado entre controladores de dominio.

Cierre y reinicie una máquina virtual que se ejecuta la función de controlador de dominio en Azure dentro del sistema operativo de invitado en lugar de usar la opción Apagar en el Portal de Azure. Con el Portal de Azure cerrar una máquina virtual hace que la máquina virtual de cancelar la asignación. Esta acción restablece VM-GenerationID, que es conveniente para un controlador de dominio. Cuando se restablece el GenerationID VM, también se restablece el Id. de invocación del repositorio de AD, se descarta el conjunto de RID y SYSVOL está marcada como no autorizados.

## <a name="monitoring-considerations"></a>Consideraciones de supervisión

No puede supervisar y mantener una red de servidores de AD puede provocar problemas como:

- **Error de inicio de sesión**. Error de inicio de sesión puede ocurrir a lo largo del dominio o bosque si se produce un error en una resolución de nombre o la relación de confianza, o si un servidor de catálogo global no puede determinar la pertenencia a grupos universal.

- **Bloqueo de cuenta**. Cuentas de usuario y servicio pueden bloquearse si el controlador de dominio principal no está disponible o se produce un error en la replicación entre varios controladores de dominio.

- **Error de controlador de dominio**. Si la unidad que contiene el archivo Ntds.dit se queda sin espacio en disco, puede que el controlador de dominio deje de funcionar.

- **Error de la aplicación**. Aplicaciones que son críticas para el negocio, como Microsoft Exchange u otra aplicación de correo electrónico, pueden fallar si no se realice correctamente en las consultas de la libreta de direcciones en el directorio.

- **Datos del directorio incoherente**. Si se produce un error en la replicación durante un período de tiempo prolongado, objetos duplicados pueden crearse en el directorio y podrían requerir extensivo diagnóstico y el tiempo para eliminar.

- **Error al crear cuenta**. Un controlador de dominio es no se puede crear cuentas de usuario o el equipo si agota su suministro de ID y el maestro RID no está disponible.

- **Error de la directiva de seguridad**. Si la carpeta compartida SYSVOL no replicar correctamente, los objetos de directiva de grupo y directivas de seguridad no se correctamente aplican a los clientes.

Supervisar los servidores AD cuidadosamente y estar preparado para tomar medidas correctivas rápidamente. Crear una lista de comprobación de tareas a realizar en un intervalo adecuado de supervisión. Por ejemplo puede programar las siguientes tareas críticas diario:

- Examinar y resolver alertas notificadas por los controladores de dominio 

- Compruebe que pueden comunicarse en todos los controladores de dominio y que la replicación funciona.

- Asegúrese de que SYSVOL sigue siendo compartido.

- Corregir problemas de sincronización de hora.

- Comprobar si hay espacio en disco en las unidades físicas usados por AD.

Puede realizar otras tareas habituales menos frecuencia. Por ejemplo, podría revisar las relaciones de confianza y comprobar confianzas obsoletas o rotas semanal y compruebe que todos los controladores de dominio se están ejecutando con los mismos service packs y revisiones de revisión mensualmente.

Para obtener más información, vea [Supervisión de Active Directory][monitoring_ad]. Puede instalar herramientas como [Microsoft Systems Center] [ microsoft_systems_center] del servidor de supervisión (consulte el [diagrama de la arquitectura][architecture]) para ayudar a realizar estas tareas. 

## <a name="solution-components"></a>Componentes de la solución

La solución proporcionada para esta arquitectura crea una red híbrida seguro tal como se describe en los documentos de [implementación de una arquitectura de red seguro híbrido en Azure] [ implementing-a-secure-hybrid-network-architecture] e [implementar una arquitectura de red seguro híbrido con acceso a Internet en Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access], pero con la adición de los siguientes elementos:

- Un grupo de recursos de Azure denominado *nombre base*- dns-c, donde *nombre base* es un prefijo que especifique al implementar la solución.

- Dos máquinas virtuales de Azure llamado *nombre base*-ad1-vm y *nombre base*-ad2-vm, creado en el grupo de recursos de dns - c *nombre base*. Estas máquinas virtuales están configuradas como servidores de AD con los servicios de directorio y DNS instalado y configurado.

- Un GSN denominado *nombre base*-ad-GSN en el grupo de recursos de Azure ntwk - c *nombre base*. Este grupo de recursos es parte de la infraestructura que constituyen la red híbrida seguro, pero la nueva GSN es un complemento que define reglas de seguridad de entrada para los servidores de AD tal como se muestra en la tabla siguiente:


    Prioridad|Nombre|Origen|Destino|Servicio|Acción|
    --------|----|------|-----------|-------|------|
    170|vnet a port53|10.0.0.0/16|Cualquier|Custom(Any/53)|Permitir|
    180|vnet a port88|10.0.0.0/16|Cualquier|Custom(Any/88)|Permitir|
    190|vnet a port135|10.0.0.0/16|Cualquier|Custom(Any/135)|Permitir|
    200|vnet a port137 9|10.0.0.0/16|Cualquier|Custom(Any/137-139)|Permitir|
    210|vnet a port389|10.0.0.0/16|Cualquier|Custom(Any/389)|Permitir|
    220|vnet a port464|10.0.0.0/16|Cualquier|Custom(Any/464)|Permitir|
    230|vnet a rpc dinámicos|10.0.0.0/16|Cualquier|Custom(Any/49152-65535)|Permitir|
    240|ad onprem de port53|192.168.0.0/24|Cualquier|Custom(Any/53)|Permitir|
    250|ad onprem de port88|192.168.0.0/24|Cualquier|Custom(Any/88)|Permitir|
    260|ad onprem de port135|192.168.0.0/24|Cualquier|Custom(Any/135)|Permitir|
    270|ad onprem de port389|192.168.0.0/24|Cualquier|Custom(Any/389)|Permitir|
    280|ad onprem de port464|192.168.0.0/24|Cualquier|Custom(Any/464)|Permitir|
    290|permitir que rdp de administración|10.0.0.128/25|Cualquier|Custom(Any/3389)|Permitir|
    300|permitir que la puerta de enlace|10.0.255.224/27|Cualquier|Custom(Any/Any)|Permitir|
    310|Permitir automática|10.0.255.192/27|Cualquier|Custom(Any/Any)|Permitir|
    320|vnet-denegar|Cualquier|Cualquier|Custom(Any/Any)|Permitir|

    AD DS usa puertos 53, 89, 135, 389 y 464 para aceptar la replicación entrante y el tráfico de autenticación. En esta tabla, el controlador de dominio local está en la 192.168.0.0/24 espacio dirección (puede variar en el espacio de direcciones: especificar esta información como un parámetro a las plantillas de implementar la solución.

    La GSN define también las siguientes reglas de seguridad saliente que habilitar el tráfico de sincronización y la autorización de flujo de nuevo a la red local:

    Prioridad|Nombre|Origen|Destino|Servicio|Acción|
    --------|----|------|-----------|-------|------|
    100|port53 de salida|Cualquier|192.168.0.0/24|Custom(Any/53)|Permitir|
    110|port88 de salida|Cualquier|192.168.0.0/24|Custom(Any/88)|Permitir|
    120|port135 de salida|Cualquier|192.168.0.0/24|Custom(Any/135)|Permitir|
    130|port389 de salida|Cualquier|192.168.0.0/24|Custom(Any/389)|Permitir|
    140|port445 de salida|Cualquier|192.168.0.0/24|Custom(Any/445)|Permitir|
    150|port464 de salida|Cualquier|192.168.0.0/24|Custom(Any/464)|Permitir|
    160|dinámicos de rpc de salida|Cualquier|192.168.0.0/24|Custom(Any/49152-65535)|Permitir|

La secuencia de comandos con la solución también realizar las siguientes tareas:

- Agrega el *nombre base*-ad1-vm y *nombre base*-ad2-vm servidores como controladores de dominio para el dominio. Puede ver estos servidores en la consola de *equipos y usuarios de Active Directory* en el controlador de dominio local:

![[1]][1]

- Crea una nueva subred (10.0.0.0/16) para un sitio de AD denominado Azure VNet Ad sitios al dominio. Este sitio contiene el *nombre base*-ad1-vm y *nombre base*-ad2-vm servidores. 

- Agrega la configuración de transporte entre sitios IP que configure el intervalo de replicación entre el sitio local y los controladores de dominio en la nube. Puede ver la configuración de la subred, sitios y configuración de transporte en la consola de *servidores y sitios de Active Directory* en el controlador de dominio local:

![[2]][2]

## <a name="deployment"></a>Implementación

La solución de ejemplo tiene los siguientes previos:

- Ya ha configurado el dominio local, y que ha configurado DNS y servicios de enrutamiento y acceso remoto para admitir una VPN instalados conectan a la puerta de enlace VPN de Azure.


- Ha instalado la versión más reciente de Azure CLI. [Siga estas instrucciones para obtener detalles][cli-install].

- Si implementa la solución de Windows, debe instalar una herramienta que proporciona un shell fiesta, como [El escritorio GitHub][github-desktop].

>[AZURE.NOTE] Si no tiene acceso a un dominio local existente, puede crear un entorno de prueba mediante la [onpremdeploy.sh] [ onpremdeploy] bash script. Esta secuencia de comandos crea una red y VM en la nube que simula una instalación local básica. Editar esta secuencia de comandos antes de ejecutar y establecer las siguientes variables definidas al principio del archivo:
>
> - **BASE_NAME**. Un prefijo definidas por el usuario para el grupo de recursos y VM creados por el script. Este elemento debe tener **no más de 5 caracteres de** lo contrario, que la secuencia de comandos intentará generar una máquina virtual con un nombre válido y no se realice correctamente.
> 
> - **Suscripción**. Su identificador de suscripción de Azure. El grupo de recursos se creará en esta suscripción.
> 
> - **Ubicación**. La ubicación de Azure en la que desea crear un grupo de recursos, como *eastus* o *westus*.
> 
> - **ADMIN_USER_NAME**. El nombre para la cuenta de administrador en la máquina virtual.
> 
> - **ADMIN_PASSWORD**. La contraseña de la cuenta de administrador.

Realice los pasos siguientes para generar la solución de ejemplo:

1. descargar y editar la [azuredeploy.sh] [ azuredeploy] secuencia de comandos y establezca los parámetros siguientes al principio del archivo:

    - **BASE_NAME**. Un prefijo definidas por el usuario para los grupos de recursos y máquinas virtuales creadas por el script. Como antes, este elemento no debe tener **más de 5 caracteres**.

    - **Suscripción**. Su identificador de suscripción de Azure.

    - **TipoSO**. El sistema operativo (*Windows* o *Linux*) que se utilizará para el nivel de acceso a web, business y datos máquinas virtuales. Observe que todos los servidores de AD creados por la secuencia de comandos ejecutan Windows Server 2012, independientemente de esta configuración.

    - **Nombre_dominio**. El nombre del dominio local. Tenga en cuenta que si utiliza el entorno creado por la secuencia de comandos de onpremdeploy.sh, debe ser *contoso.com*.

    - **Ubicación**. La ubicación de Azure en la que se va a crear los grupos de recursos.

    - **ADMIN_USER_NAME**. El nombre para las cuentas de administrador en las máquinas virtuales distintos.

    - **ADMIN_PASSWORD**. La contraseña de la cuenta de administrador.

    - **ON_PREMISES_PUBLIC_IP**. La dirección IP pública del equipo VPN local.

    - **ON_PREMISES_ADDRESS_SPACE**. El espacio de dirección interna de la red local. Si está utilizando el entorno creado por la secuencia de comandos de onpremdeploy.sh, debe ser 192.168.0.0/16.

    - **VPN_IPSEC_SHARED_KEY**. La clave compartida de IPSec utilizada para establecer la conexión VPN entre la red local y la puerta de enlace VPN de Azure.

    - **ON_PREMISES_DNS_SERVER_ADDRESS**. La dirección IP del servidor DNS local. Si está utilizando el entorno creado por la secuencia de comandos de onpremdeploy.sh, debe ser 192.168.0.4

    - **ON_PREMISES_DNS_SUBNET_PREFIX** El prefijo de dirección de la subred local. Si está utilizando el entorno creado por la secuencia de comandos de onpremdeploy.sh, debe ser 192.168.0.0/24.

    >[AZURE.NOTE] PARA ahorrar tiempo y recursos, la secuencia de comandos no crea los niveles de acceso a datos profesionales o. Si necesita estos elementos, puede quite la siguiente sección de la secuencia de comandos de azuredeploy.sh:
    >
    >
    > ```
    > #### # create biz tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_BIZ_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${BIZ_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${BIZ_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_BIZ_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > #### 
    > #### # create db tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_DB_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${DB_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${DB_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_DB_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ```

2. Abra un símbolo del shell de fiesta y mover a la carpeta que contiene el script azuredeploy.sh.

3. Inicie sesión en su cuenta de Azure. En el shell de fiesta, escriba el siguiente comando:

    ```
    azure login
    ```

    Siga las instrucciones para conectarse a Azure.

4. Ejecute el comando `./azuredeploy.sh`y, a continuación, espere a que el script crea la infraestructura de red.

5. En el símbolo que *Compruebe que se ha creado la VNet*, use el portal de Azure para comprobar que se ha creado un grupo de recursos denominado *nombre base*- ntwk-c y que contiene elementos similares a los que se muestra en la siguiente imagen:

    ![[3]][3]

    >[AZURE.NOTE] En los ejemplos que se muestra, *nombre base* se estableció en *nube* al ejecuta la secuencia de comandos.

    Haga clic en el *nombre base*- vnet VNet, haga clic en *subredes*y compruebe que se han creado las subredes que se muestra a continuación:

    ![[4]][4]

6. En el símbolo en la ventana de shell de fiesta, presione una tecla y espere mientras se crean los niveles de web y equilibrador de carga.

7. En el símbolo que *Compruebe que el nivel de Web se ha creado correctamente*, use el portal de Azure para comprobar que se ha creado un grupo de recursos denominado *nombre base*web-nivel-c y que contiene elementos similares a los que se muestra a continuación:

    ![[5]][5]

8. En el símbolo en la ventana de shell de fiesta, presione una tecla y espere mientras se crean los NVAs.

9. En el símbolo que *Compruebe que la NVA se ha creado correctamente*, use el portal de Azure para comprobar que se ha creado un grupo de recursos denominado *nombre base*administración-c con el siguiente contenido:

    ![[6]][6]

10. En el símbolo en la ventana de shell de fiesta, presione una tecla y espere mientras se crea la jumpbox.

11. En el símbolo que *Compruebe que la jumpbox se ha creado correctamente*, use el portal de Azure para comprobar que los siguientes elementos se han agregado al grupo de recursos de administración - c *nombre base*:

    - Un conjunto de disponibilidad denominado *nombre base*- jb-como.

    - Una máquina virtual había denominada vm - jb *nombre base*.

    - Una interfaz de red llamado *nombre base*- jb-NIC.

12. En el símbolo en la ventana de shell de fiesta, presione una tecla y espere mientras se crean la puerta de enlace VPN de Azure y conexión. Tenga en cuenta que este paso puede tardar hasta 30 minutos en completarse.

13. En el símbolo que *Compruebe que la puerta de enlace VPN se ha creado correctamente*, use el portal de Azure para comprobar que los siguientes elementos se han agregado al grupo de recursos de ntwk - c *nombre base*:

    - Una puerta de enlace de red local denominado lgw en local.
    
    - Una puerta de enlace de red virtual llamado *nombre base*- vpngw.

    - Una conexión de puerta de enlace denominada *nombre base*- vnet-vpnconn. Observe que el estado de esta conexión podría ser *no está conectado* , si todavía no ha configurado el extremo local de la conexión; se abordarán esto más adelante.

14. En el símbolo en la ventana de shell de fiesta, presione una tecla y espere mientras se crean las máquinas virtuales y otros recursos de la DMZ.

15. En el símbolo que *Compruebe que la DMZ se ha creado correctamente*, use el portal de Azure para comprobar que se ha creado un grupo de recursos denominado *nombre base*-dmz-c con el siguiente contenido:

    ![[7]][7]

16. En el símbolo en la ventana de shell de fiesta, presione una tecla. Deben aparecer las preguntas siguientes:

    ```text
    Manual Step...

    Please configure your on-premises network to connect to the Azure VNet

    Make sure that you can connect to the on-premises AD server from the Azure VMs
    ```

    Inicie sesión en el equipo local que se conecta a la puerta de enlace de Azure y configurar la conexión correctamente. Agregar rutas estáticas en el dispositivo de puerta de enlace local que dirige requestsfor el intervalo de direcciones 10.0.0.0/16 a través de la puerta de enlace a la VNet. Los pasos para realizar esta acción varían según cómo se conecta. Vea [implementar una arquitectura de red híbrida con Azure y VPN local] [ implementing-a-hybrid-network-architecture-with-vpn] para obtener más información.

    Tenga en cuenta que puede encontrar la dirección IP pública de la puerta de enlace VPN de Azure mediante el portal de Azure para examinar la puerta de enlace de *nombre base*- vpngw en el grupo de recursos de ntwk - c *nombre base*:

    ![[8]][8]

    Puede determinar si se ha establecido correctamente la conexión consultando el estado de la conexión de vpnconn - vnet *nombre base*. Se debe establecer *conectado*.

    Para probar la conexión, abra una conexión a Escritorio remoto a la jumpbox (10.0.0.254) desde un equipo que se encuentra en su red local.

17. En el símbolo en la ventana de shell de fiesta, presione una tecla. En el siguiente símbolo del sistema, *presione cualquier tecla para actualizar la configuración de VNet de la VNet para que apunten a local DNS*, presione una tecla y espere a que la configuración de DNS para el VNet se actualiza en el valor especificado como parámetro **ON_PREMISES_DNS_SERVER_ADDRESS** en la secuencia de comandos de azuredeploy.sh.

18. En el mensaje, *Compruebe que la configuración del servidor DNS en el VNet se ha actualizado*, utilice el portal de Azure para examinar la configuración de *los servidores DNS* de *nombre base*- vnet VNet en el grupo de recursos de ntwk - c *nombre base*. Se debe establecer a *Custom DNS*y el *servidor DNS principal* debe ser la dirección del servidor DNS local:

    ![[9]][9]

19. En el mensaje *presione cualquier tecla para crear el grupo de recursos para los servidores de AD* en la ventana de shell de fiesta, presione una tecla y espere mientras se crea el grupo de recursos para retener los servidores de AD en la nube.

20. En el mensaje *presione cualquier tecla para crear las máquinas virtuales de los servidores de AD* en la ventana de shell de fiesta, presione una tecla y espere a que las VM que se crea y se agrega al grupo de recursos.

21. Cuando la *presione cualquier tecla para unirse a las máquinas virtuales al dominio local* aparece, vaya al portal de Azure y compruebe que se ha creado un grupo denominado *nombre base*- dns-c y, a continuación, que contiene dos máquinas virtuales (*nombre base*-ad1-vm y *nombre base*-ad2-vm):

    ![[10]][10]

22. En el grupo de recursos de *nombre base*ntwk - c, comprobar que se ha creado un GSN llamado *nombre base*-ad-GSN. Examine las reglas de seguridad entrante y saliente para este GSN. Debe coincidir con las que se muestran en las tablas de los [componentes de la solución] [ solution-components] sección.

23. En el símbolo en la ventana de shell de fiesta, presione una tecla y espere mientras se agregan las máquinas virtuales al dominio local.

24. En la *por favor vaya a local en el servidor de anuncios para comprobar que se han agregado los equipos a los dominios* preguntar, conectarse a su equipo local y utilizar la consola de *equipos y usuarios de Active Directory* para comprobar que se han agregado dos máquinas virtuales al dominio:

    ![[11]][11]

25. En el símbolo en la ventana de shell de fiesta, presione una tecla y espere mientras se crea el sitio de replicación de AD en el dominio.

26. En la *por favor vaya a local en el servidor de anuncios para comprobar que se ha creado el sitio de replicación* preguntar, utilice la consola de *sitios de Active Directory y servicios* para comprobar que se ha creado correctamente, un sitio de replicación denominado *Azure Vnet Ad sitios* junto con un vínculo de transporte entre sitios IP denominado *AzureToOnpremLink*y una subred que hace referencia la VNet:

    ![[12]][12]

27. En el símbolo en la ventana de shell de fiesta, presione una tecla y espere mientras la secuencia de comandos instala Servicios de directorio y DNS en cada una de las máquinas virtuales de AD.

28. Cuando aparezca el mensaje de *Inicio de sesión de Please a cada servidor de Azure AD para comprobar que los servicios de directorio se ha configurado correctamente* , abra una conexión a Escritorio remoto desde un equipo local a la jumpbox (*nombre base*- jb-vm) y abra otra conexión a Escritorio remoto desde el jumpbox al primer servidor AD (*nombre base*-ad1-vm). Inicie sesión con el **nombre_dominio**, **ADMIN_USER_NAME**y **ADMIN_PASSWORD** especificada en la secuencia de comandos de azuredeploy.sh. Con el administrador del servidor, compruebe que las funciones de AD DS y DNS ambos se han agregado. Repita este proceso para el segundo servidor AD (*nombre base*-ad2-vm).

29. En el símbolo en la ventana de shell de fiesta, presione una tecla. Cuando aparezca el mensaje *presione cualquier tecla para establecer la configuración de Azure VNet DNS para que apunten a DNS en Azure* , presione una tecla y permitir la secuencia de comandos actualizar la configuración de DNS para el VNet.

30. Cuando el símbolo del sistema *Compruebe que el DNS VNet configuración ha sido actualizado referencia Azure VM DNS servers* aparece, con la comprobación de portal Azure la configuración de *Los servidores DNS* de *nombre base*- vnet VNet en el grupo de recursos de ntwk - c *nombre base*. Los servidores DNS principales y secundarios ahora deben hacer referencia a las dos máquinas virtuales de AD:

    ![[13]][13]

31. Reinicie cada una de las máquinas virtuales de AD antes de continuar. Este paso es necesario para garantizar que cada uno de ellos recoger la configuración DNS correcta de Azure. Espere a que se están ejecutando dos máquinas virtuales antes de continuar.

32. En el símbolo en la ventana de shell de fiesta, presione una tecla. En el mensaje siguiente, *presione cualquier tecla para aplicar la puerta de enlace UDR a la subred de puerta de enlace (se haya quitado)*, presione una tecla y permitir la secuencia de comandos actualizar la puerta de enlace UDR.

33. Compruebe que la secuencia de comandos se completa correctamente.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre las prácticas recomendadas para [crear un bosque de recursos agrega] [ adds-resource-forest] en Azure.

- Obtenga información sobre los procedimientos recomendados para [crear una infraestructura ADFS] [ adfs] en Azure.

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[architecture]: #architecture_diagram
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[capacity-planning-for-adds]: http://social.technet.microsoft.com/wiki/contents/articles/14355.capacity-planning-for-active-directory-domain-services.aspx
[ad-ds-ports]: https://technet.microsoft.com/library/dd772723(v=ws.11).aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048(v=ws.11).aspx
[powershell-ad]: https://technet.microsoft.com/en-us/library/ee617195.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[cli-install]: https://azure.microsoft.com/documentation/articles/xplat-cli-install
[github-desktop]: https://desktop.github.com/
[sssd-and-active-directory]: https://help.ubuntu.com/lts/serverguide/sssd-ad.html
[set-a-static-ip-address]: https://azure.microsoft.com/documentation/articles/virtual-networks-static-private-ip-arm-pportal/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[adds-data-disks]: https://msdn.microsoft.com/library/azure/jj156090.aspx#BKMK_PlaceDB
[AD-FSMO-recommendations]: #active_directory_FSMO_placement_recommendations
[AD-FSMO-roles-in-windows]: https://support.microsoft.com/en-gb/kb/197132
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[transfer-FSMO-roles]: https://technet.microsoft.com/library/cc816946(v=ws.10).aspx
[view-fsmo-roles]: https://technet.microsoft.com/library/cc816893(v=ws.10).aspx
[read-only-dc]: https://technet.microsoft.com/library/cc732801(v=ws.10).aspx
[AD-sites-and-subnets]: https://blogs.technet.microsoft.com/canitpro/2015/03/03/step-by-step-setting-up-active-directory-sites-subnets-site-links/
[sites-overview]: https://technet.microsoft.com/library/cc782048(v=ws.10).aspx
[implementing-adfs]: ./guidance-iaas-ra-secure-vnet-adfs.md
[onpremdeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/onpremdeploy.sh
[azuredeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/azuredeploy.sh
[solution-components]: #solution_components

[0]: ./media/guidance-iaas-ra-secure-vnet-ad/figure1.png "Proteger la arquitectura de red híbrida con Active Directory"
[1]: ./media/guidance-iaas-ra-secure-vnet-ad/figure2.png "La consola de usuarios de Active Directory y equipos listado las dos máquinas virtuales de Azure como servidores"
[2]: ./media/guidance-iaas-ra-secure-vnet-ad/figure3.png "La consola de sitios de Active Directory y servicios que muestra la configuración de replicación para el sitio en la nube"
[3]: ./media/guidance-iaas-ra-secure-vnet-ad/figure4.png "El contenido del grupo de recursos de nombre base-ntwk-c"
[4]: ./media/guidance-iaas-ra-secure-vnet-ad/figure5.png "Las subredes de la VNet vnet nombre base"
[5]: ./media/guidance-iaas-ra-secure-vnet-ad/figure6.png "Los elementos en el nivel de web"
[6]: ./media/guidance-iaas-ra-secure-vnet-ad/figure7.png "El NVAs en el grupo de recursos de administración de nombre base de c"
[7]: ./media/guidance-iaas-ra-secure-vnet-ad/figure8.png "Los recursos del grupo de recursos de nombre base-dmz-c"
[8]: ./media/guidance-iaas-ra-secure-vnet-ad/figure9.png "Buscar la dirección IP pública de la puerta de enlace VPN de Azure"
[9]: ./media/guidance-iaas-ra-secure-vnet-ad/figure10.png "La configuración del servidor DNS para el * nombre base *-vnet VNet"
[10]: ./media/guidance-iaas-ra-secure-vnet-ad/figure11.png "El * nombre base *-dns-c grupo de recursos que contiene el servidor AD VM"
[11]: ./media/guidance-iaas-ra-secure-vnet-ad/figure12.png "La consola de usuarios de Active Directory y equipos listado el servidor AD máquinas virtuales como miembros del dominio"
[12]: ./media/guidance-iaas-ra-secure-vnet-ad/figure13.png "La consola de sitios de Active Directory y servicios que muestra el sitio de replicación de los servidores de Azure AD"
[13]: ./media/guidance-iaas-ra-secure-vnet-ad/figure14.png "La configuración del servidor DNS para el VNet vnet nombre base hace referencia a los servidores de AD en la nube"