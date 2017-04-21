<properties
   pageTitle="Implementación de ADFS en Azure | Microsoft Azure"
   description="Cómo implementar una arquitectura de red seguro híbrido con autorización de servicio de federación de Active Directory en Azure."
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
   ms.date="10/13/2016"
   ms.author="telmos"/>

# <a name="implementing-active-directory-federation-services-adfs-in-azure"></a>Implementación de servicios de federación de Active Directory (ADFS) en Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

En este artículo se describe los procedimientos recomendados para implementar una red híbrida seguro que su red local extiende en Azure y que usa [Los servicios de federación de Active Directory (ADFS)] [ active-directory-federation-services] para realizar autenticación federada y la autorización de los componentes se ejecutan en la nube. Esta arquitectura extiende la estructura descrita por [Ampliar Active Directory a Azure][implementing-active-directory].

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación: [El Administrador de recursos] [ resource-manager-overview] y clásica. Esta arquitectura de referencia utiliza el Administrador de recursos, que Microsoft recomienda para implementaciones nuevas.

ADFS puede ejecutar local, pero en un escenario híbrido donde las aplicaciones se encuentran en Azure puede ser más eficaz para implementar esta funcionalidad en la nube. Casos de uso común para esta arquitectura incluyen:

- Aplicaciones híbridas donde ejecutar las cargas de trabajo en parte local y en parte de Azure.

- Soluciones que utilizan federada autorización para exponer aplicaciones web para las organizaciones asociadas.

- Sistemas que admiten el acceso de exploradores web que se ejecutan fuera de la organización.

- Sistemas que permiten a los usuarios acceso a aplicaciones web mediante una conexión desde dispositivos externos autorizados como equipos remotos, los blocs de notas y otros dispositivos móviles. 

Para obtener más información sobre cómo funciona ADFS, consulte [Introducción de servicios de federación de Active Directory][active-directory-federation-services-overview]. Además, el artículo de la [implementación de ADFS en Azure] [ adfs-intro] contiene una introducción detallada paso a paso para la implementación de ADFS en Azure.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

El siguiente diagrama resalta los componentes importantes en esta arquitectura (*haga clic para acercar*). Para obtener más información acerca de cualquier elemento que no está relacionado con ADFS, lea [implementar una arquitectura de red seguro híbrido en Azure][implementing-a-secure-hybrid-network-architecture], [implementar una arquitectura de red seguro híbrido con acceso a Internet en Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]e [implementar una arquitectura de red seguro híbrido con las identidades de Active Directory en Azure][implementing-active-directory]:

[! [0]][0]

>[AZURE.NOTE] Este diagrama muestra los siguientes casos de uso:
>
>- Código de aplicación que se ejecuta dentro de una organización asociada tiene acceso a una aplicación web hospedada dentro de su VNet de Azure.
>
>- Un usuario externo, registrado (con las credenciales almacenadas dentro de suma) acceso a una aplicación web hospedada dentro de su VNet de Azure.
>
>- Un usuario puede conectarse a su VNet con un dispositivo autorizado y ejecute una aplicación web hospedada dentro de su VNet de Azure.
>
>Además, esta arquitectura se centra en federación pasiva, donde los servidores de federación decidan cómo y cuándo para autenticar un usuario. El usuario debe proporcionar información de inicio de sesión cuando se inicia la ejecución de una aplicación. Esto es el mecanismo más usado por los exploradores web e implica un protocolo que redirige el explorador a un sitio donde el usuario puede proporcionar sus credenciales. ADFS también admite la federación de active según la cual una aplicación asume la responsabilidad para proporcionar las credenciales sin interacción del usuario, pero en este caso está fuera del alcance de esta arquitectura.

- **Agrega subred.** Los servidores agrega dentro de su propia subred. Reglas de GSN ayudan a proteger los servidores de suma y pueden proporcionar un firewall contra el tráfico de forma inesperada.

- **AGREGA servidores.** Estos son los controladores de dominio que se ejecuta como máquinas virtuales en la nube. Estos servidores pueden proporcionar autenticación de identidades locales dentro del dominio.

- **Subred ADFS.** Los servidores ADFS puede estar dentro de su propia subred con reglas de GSN que actúe como un firewall.

- **Servidores de ADFS.** Los servidores ADFS proporcionan autenticación y la autorización federado. En esta arquitectura, realicen las siguientes tareas:

    - Pueden recibir tokens de seguridad que contiene reclamaciones realizados por un servidor de federación de asociado en nombre de un usuario asociado. ADFS puede comprobar que estos tokens sean válidos antes de pasar las notificaciones de la aplicación web que se ejecuta en Azure. La aplicación web corporativa (en Azure) puede usar estas notificaciones para autorizar solicitudes. En este escenario, la aplicación web corporativo es el *usuario de confianza*y es la responsabilidad del servidor de federación de asociado a emitir dice que se entienden por la aplicación web corporativa. Los servidores de federación asociado se denominan *asociados de cuenta* porque envíen solicitudes de acceso en el nombre de cuentas autenticadas en la organización del asociado. Los servidores ADFS se denominan *asociados de recurso* porque proporcionan acceso a los recursos (en este caso, aplicaciones web).

    - Puede autenticar (a través de suma y el [Servicio de registro de Active Directory dispositivo][ADDRS]) y autorizar las solicitudes entrantes de los usuarios externos que ejecuta un explorador web o un dispositivo que necesita obtener acceso a sus aplicaciones web corporativas. 

    Los servidores ADFS están configurados como un conjunto de servidores, tiene acceso a través de un equilibrador de carga de Azure. Esta estructura le ayuda a mejorar la disponibilidad y escalabilidad. Además, tenga en cuenta que no se exponen los servidores ADFS directamente a Internet, en su lugar todo el tráfico Internet se filtra a través de los servidores de proxy de aplicación web ADFS y una DMZ.

- **Subred de proxy ADFS.** Los servidores proxy ADFS pueden estar dentro de su propia subred con reglas de GSN protección. Los servidores de esta subred se exponen en Internet a través de un conjunto de dispositivos de red virtual que proporcionan un firewall entre su red virtual Azure e Internet.

- **ADFS web application (WAP) los servidores proxy.** Estos equipos funcionan como servidores ADFS para las solicitudes entrantes de las organizaciones asociadas y dispositivos externos. Los servidores WAP actúen como un filtro, la protección de los servidores ADFS del acceso directo de Internet público. Como ocurre con los servidores ADFS, implementación el WAP servidores en una granja de servidores con equilibrio de carga proporciona mayor disponibilidad y escalabilidad que la implementación de un conjunto de servidores independientes.

    >[AZURE.NOTE] Para obtener información detallada sobre la instalación de servidores WAP, vea [instalar y configurar el servidor a Proxy de aplicaciones de Web][install_and_configure_the_web_application_proxy_server]

- **Organización de Partner.** Se trata de una organización del asociado de ejemplo, que se ejecuta una aplicación web que solicita el acceso a la aplicación web que se ejecuta en Azure. El servidor de federación de la organización asociada autentica las solicitudes localmente y envía el token de seguridad que contiene reclamaciones a ADFS ejecuta en Azure. ADFS en Azure valida el token de seguridad y, si son válidos puede pasar las notificaciones a la aplicación web se ejecuta en Azure para autorizar a ellos.

    >[AZURE.NOTE] También puede configurar un túnel de VPN con Azure puerta de enlace para proporcionar acceso directo a ADFS para socios de confianza. Las solicitudes recibidas desde estos socios no pasan a través de los servidores WAP.

## <a name="recommendations"></a>Recomendaciones

Esta sección resumen las recomendaciones para implementar ADFS en Azure, que cubre:

- Recomendaciones de máquina virtual.

- Recomendaciones de redes.

- Recomendaciones de disponibilidad.

- Recomendaciones de seguridad.

- Recomendaciones de instalación de ADFS.

- Recomendaciones de confianza de ADFS.

### <a name="vm-recommendations"></a>Recomendaciones de VM

Crear máquinas virtuales con recursos suficientes para controlar el volumen de tráfico esperado. Usar el tamaño de los equipos existentes hospedaje ADFS local como punto de partida. Supervisar el uso de recursos. Puede cambiar el tamaño de las máquinas virtuales y reducir si son demasiado grandes.

Seguir las recomendaciones incluidas en la [ejecución de una máquina virtual de Windows en Azure][vm-recommendations].

### <a name="networking-recommendations"></a>Recomendaciones de redes

Configurar la interfaz de red para cada una de las máquinas virtuales ADFS y WAP servidores con direcciones IP privadas de hospedaje.

No se proporcionan las direcciones IP públicas de máquinas virtuales de ADFS. Para obtener más información, vea [Consideraciones de seguridad][security-considerations].

Establecer la dirección IP de los servidores DNS preferidos y secundarias para las interfaces de red para cada ADFS y WAP VM hacer referencia a las VM agrega (que debe estar ejecutándose DNS). Este paso es necesario habilitar cada VM para unirse al dominio.

### <a name="availability-recommendations"></a>Recomendaciones de disponibilidad

Crear una granja de servidores ADFS con al menos dos servidores para aumentar la disponibilidad del servicio ADFS.

Usar cuentas de almacenamiento diferente para cada VM ADFS en el conjunto de servidores. Este enfoque ayuda a garantizar que un error en una cuenta de almacenamiento única no hace que el conjunto completo de acceder.

Crear conjuntos de disponibilidad de Azure independiente para ADFS y máquinas virtuales de WAP. Asegúrese de que hay al menos dos máquinas virtuales en cada conjunto. Cada conjunto de disponibilidad debe tener al menos dos dominios de actualización y dos errores.

Configurar los equilibradores de carga para las máquinas virtuales de ADFS y máquinas virtuales de WAP como sigue:

- Use un equilibrador de carga de Azure para proporcionar acceso externo a las VM WAP y un equilibrador de carga interno para distribuir la carga entre los servidores ADFS en el conjunto de servidores ADFS.

- Sólo pasan tráfico que aparecen en el puerto 443 (HTTPS) a los servidores ADFS/WAP.

- Dar el equilibrador de carga de una dirección IP estática.

- Crear un sondeo de mantenimiento mediante el protocolo TCP en lugar de HTTPS. Puede hacer ping puerto 443 para comprobar que funciona un servidor de ADFS.

    >[AZURE.NOTE] Servidores de ADFS utilizan el protocolo de indicación de nombre de servidor (SNI), así que intenta sondeo emplea un extremo HTTPS desde el equilibrador de carga se produce un error.

- Agregar un registro DNS *A* para el dominio del equilibrador de carga ADFS. 

    Especifique la dirección IP del equilibrador de carga y asígnele un nombre en el dominio (como adfs.contoso.com). Este es el nombre que los clientes y los servidores WAP tener acceso a la granja de servidores ADFS.

### <a name="security-recommendations"></a>Recomendaciones de seguridad

Evitar la exposición directa de los servidores ADFS a Internet. Los servidores ADFS son equipos unidos a un dominio con autorización completa para conceder el token de seguridad. Si un servidor de ADFS está comprometido, un usuario malintencionado puede emitir tokens de acceso completo a todas las aplicaciones web y los servidores de federación que están protegidos por ADFS. Si el sistema debe administrar solicitudes de usuarios externos no necesariamente conexión de sitios de confianza de partner, use servidores WAP manejar estas solicitudes. Para obtener más información, vea [ubicación de un Proxy de servidor de federación][where-to-place-an-fs-proxy].

Servidores de ADFS lugar y WAP de subredes independientes con sus propios servidores de seguridad. Puede usar reglas de GSN para definir las reglas de firewall. Si necesita una protección más completa que pueda implementar un perímetro de seguridad adicional alrededor de servidores con un par de subredes y NVAs, como se describe en el documento de [implementación de una arquitectura de red seguro híbrido con acceso a Internet en Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]. Tenga en cuenta que todos los servidores de seguridad deben permitir el tráfico en el puerto 443 (HTTPS).

Restringir el acceso directo a los servidores de ADFS y WAP. Solo DevOps personal debería poder conectarse.

No unirse al dominio los servidores WAP.

### <a name="adfs-installation-recommendations"></a>Recomendaciones de instalación de ADFS

El artículo [implementar una granja de servidores de federación] [ Deploying_a_federation_server_farm] proporciona instrucciones detalladas para instalar y configurar ADFS. Antes de configurar el primer servidor ADFS en el conjunto de servidores, realice las siguientes tareas:

1. Obtener un certificado de confianza pública para realizar la autenticación del servidor. El *nombre de asunto* debe contener el nombre que los clientes de acceso al servicio de federación. Esto puede ser el nombre DNS registrado para el equilibrador de carga, por ejemplo, *adfs.contoso.com* (evitar el uso de nombres de caracteres comodín como **. contoso.com*, por motivos de seguridad). Usar el mismo certificado en todas las máquinas virtuales de servidor ADFS. Puede adquirir un certificado de una entidad de certificación, pero si su organización usa servicios de certificados de Active Directory puede crear su propio. 

    Se utiliza el *nombre de asunto alternativo* por el DRS para habilitar el acceso desde dispositivos externos. Debe tener el formulario *enterpriseregistration.contoso.com*.

    Para obtener más información, vea [obtener y configurar un certificado SSL para ADFS][adfs_certificates].

2. En el controlador de dominio, generar una nueva clave de raíz para el servicio de distribución de claves. Establecer el tiempo eficaces que la hora actual menos 10 horas (esta configuración reduce el tiempo de retraso que puede ocurrir en distribuir y sincronización de teclas en el dominio). Este paso es necesario para admitir la creación de la cuenta de servicio de grupo que se usan para ejecutar el servicio ADFS. El siguiente comando Powershell muestra un ejemplo de cómo hacerlo:

    ```powershell
    Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
    ```

3. Agregar cada servidor de ADFS VM al dominio.

>[AZURE.NOTE] Para instalar ADFS, el controlador de dominio que se ejecuta el emulador función FSMO para el dominio debe ser accesible desde las máquinas virtuales de ADFS y en ejecución.

### <a name="adfs-trust-recommendations"></a>Recomendaciones de confianza de ADFS

Establecer la confianza de federación entre la instalación de ADFS y los servidores de federación de las organizaciones asociadas. Configurar las notificaciones filtrado y asignación obligatorio. Este proceso requiere:

- DevOps personal **de cada organización asociada** para agregar una confianza de fiesta de confianza para las aplicaciones web accesibles a través de los servidores ADFS.

- DevOps personal **de su organización** para configurar la confianza de proveedor de notificaciones para habilitar los servidores ADFS confiar en las notificaciones que las organizaciones asociadas ofrecen.

- DevOps personal **de su organización** para configurar ADFS para pasar notificaciones en aplicaciones web de su organización.

    Para obtener más información, vea [Establecer la confianza de federación][establishing-federation-trust].

Publicar aplicaciones web de su organización y que estén disponibles para sus socios externos utilizando autenticación previa a través de los servidores WAP. Para obtener más información, consulte [publicar aplicaciones mediante la autenticación previa ADFS][publish_applications_using_AD_FS_preauthentication]

Tenga en cuenta que es compatible con ADFS aumento y transformación token. Azure Active Directory no ofrece esta característica. Con ADFS, al configurar las relaciones de confianza, puede:

- Configurar las transformaciones de Reclamación de las reglas de autorización. Por ejemplo, puede asignar la seguridad de un grupo desde una representación de una organización no sean de Microsoft partner algo que puede autorizar que agrega en su organización.

- Transformar notificaciones de un formato a otro. Por ejemplo, puede asignar de SAML 2.0 SAML 1.1 si la aplicación solo admite notificaciones de SAML 1.1. 

## <a name="availability-considerations"></a>Consideraciones de disponibilidad

Puede usar SQL Server o la base de datos internos de Windows (WID) para guardar la información de configuración de ADFS. WID redundancia básico. Cambios se escriben directamente a solo una de las bases de datos ADFS en el clúster ADFS, mientras que los demás servidores utilizan la replicación de extracción para mantener actualizados sus bases de datos. Uso de SQL Server puede proporcionar redundancia de base de datos completa y alta disponibilidad con migración tras error clústeres o reflejo.

## <a name="security-considerations"></a>Consideraciones de seguridad

ADFS usa el protocolo HTTPS, asegúrese de que las reglas de GSN para la subred que contiene el web VM permiso HTTPS solicitudes de nivel. Estas solicitudes pueden proceden de la red local, las subredes que contiene el nivel de web, nivel empresarial, nivel de datos, DMZ privado, DMZ pública y la subred que contiene los servidores ADFS.

Tenga en cuenta con un conjunto de dispositivos de red virtual que registra información detallada sobre el tráfico atraviesa el borde de la red virtual con fines de auditoría.

## <a name="scalability-considerations"></a>Consideraciones de escalabilidad

Las siguientes consideraciones, resumidas del documento [Planear la implementación de ADFS][plan-your-adfs-deployment], proporcionar un punto de partida para granjas de servidores ADFS de tamaño:

- Si tiene menos de 1.000 usuarios, no crear servidores ADFS, pero en su lugar, instale ADFS en cada uno de los servidores de suma en la nube (asegúrese de que tiene al menos dos servidores de suma, para mantener la disponibilidad). Crear un solo servidor WAP.

- Si tiene entre 1000 y 15000 usuarios, cree dos servidores ADFS y dos servidores WAP.

- Si tiene entre los usuarios de 15000 y 60000, crear entre tres y cinco servidores ADFS y al menos dos servidores WAP.

Estas cifras suponen que usa dos máquinas virtuales de núcleo cuádruple (D4_v2 estándar o mejor) para hospedar los servidores de Azure.

Tenga en cuenta que si está utilizando la base de datos internos de Windows para almacenar los datos de configuración de ADFS, está limitados a ocho servidores ADFS en el conjunto de servidores. Si prevé la necesidad de más, a continuación, usar SQL Server. Para obtener más información, vea [La función de la base de datos de configuración de ADFS][adfs-configuration-database].

## <a name="management-considerations"></a>Consideraciones de administración

DevOps personal debe estar preparado para realizar las siguientes tareas:

- Administración de los servidores de federación (administración de la granja de servidores ADFS, administrar la directiva de confianza en los servidores de federación y administrar los certificados usados por los servicios de federación).

- Administración de los servidores WAP (administración de la granja WAP, administración de certificados WAP).

- Administrar aplicaciones web (configurar usuarios de confianza, métodos de autenticación y las asignaciones de reclamaciones).

- Copia de seguridad de los componentes ADFS.

## <a name="monitoring-considerations"></a>Consideraciones de supervisión

El [Paquete de administración de Microsoft System Center para Active Directory Federation servicios 2012 R2] [ oms-adfs-pack] proporciona la supervisión proactiva y reactiva de la implementación de ADFS para el servidor de federación. Este módulo de administración supervisa:

- Eventos que la ADFS servicio registros de los registros de eventos ADFS.

- Los datos de rendimiento que recopilan los contadores de rendimiento de ADFS. 

- El estado general de la ADFS sistema y las aplicaciones web (usuarios de confianza) y proporciona alertas para problemas críticos y advertencias.

## <a name="solution-components"></a>Componentes de la solución

Una secuencia de comandos de solución de ejemplo, [Implementar ReferenceArchitecture.ps1][solution-script], está disponible que puede usar para implementar la arquitectura que sigue las recomendaciones descritas en este artículo. Esta secuencia de comandos utiliza plantillas de administrador de recursos de Azure. Las plantillas están disponibles como un conjunto de bloques de creación fundamentales, cada una de las cuales realiza una acción específica, como crear un VNet o configurar un GSN. El propósito de la secuencia de comandos es organizar la implementación de la plantilla.

Las plantillas se con parámetros con los parámetros que llevan a cabo en archivos independientes de JSON. Puede modificar los parámetros de estos archivos para configurar la implementación para satisfacer sus propios requisitos. No es necesario modificar las plantillas de sí mismos. Tenga en cuenta que no debe cambiar los esquemas de los objetos en los archivos de parámetros.

Al editar las plantillas, crear objetos que siguen las convenciones de nomenclatura descritas en [Recomendado convenciones de nomenclatura para los recursos de Azure][naming-conventions].

La solución de ejemplo crea y configura el entorno en la nube que abarcan DMZ de subred y servidores, la subred ADFS y servidores, subred proxy ADFS y, agrega, nivel de web, nivel de empresa y componentes de nivel de acceso a datos, puerta de enlace VPN y nivel de administración. La solución de ejemplo también incluye una configuración opcional para crear un entorno local simulada.

Las secciones siguientes describen los elementos de las instalaciones y configuraciones de la nube.

### <a name="on-premises-components"></a>Componentes de local

>[AZURE.NOTE] Estos componentes no son el enfoque principal de la arquitectura que se describe en este documento y se proporcionan simplemente para dar a una oportunidad de probar el entorno de nube de forma segura, en lugar de usar un entorno de producción real. Por este motivo, esta sección solo resumen los archivos de parámetro de clave. Puede modificar la configuración, como las direcciones IP o los tamaños de las máquinas virtuales, pero es aconsejable dejar muchos de los demás parámetros sin cambios.

Este entorno consta de un bosque de AD para un dominio llamado contoso.com. El dominio contiene dos servidores agrega con direcciones IP 192.168.0.4 y 192.168.0.5. Estos dos servidores también ejecutan el servicio DNS. La cuenta de administrador local en ambas máquinas virtuales se denomina `testuser` con contraseña `AweS0me@PW`. Además, se establece la configuración de una puerta de enlace VPN para conectarse a la VNet en la nube. Puede modificar la configuración mediante la edición de los siguientes archivos JSON ubicados en los [**parámetros o los**] [ on-premises-folder] carpeta:

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**. Este archivo define el espacio de direcciones de red para el entorno local.

- ** [virtualMachines adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. Este archivo contiene la configuración para las VM local agrega servicios de hospedaje. De forma predeterminada, se crean dos máquinas virtuales de *estándar-DS3-v2* .

- ** [virtualNetworkGateway.parameters.json] [ on-premises-virtualnetworkgateway-parameters] ** y ** [connection.parameters.json][on-premises-connection-parameters]**. Estos archivos contienen la configuración de la conexión VPN a la puerta de enlace VPN de Azure en la nube, incluida la clave compartida que se utilizará para proteger el tráfico atraviesa la puerta de enlace.

Los archivos en la carpeta restantes contienen la información de configuración utilizada para crear el dominio local con esta infraestructura. Usarlos para instalar agrega, configuración DNS, cree un bosque y configurar la replicación de sitios para el bosque.

### <a name="cloud-components"></a>Componentes de nube

Estos componentes forman el núcleo de esta arquitectura. Los [**parámetros o azure**] [ azure-folder] carpeta contiene los archivos de parámetro siguiente para configurar estos componentes:

- ** [virtualNetwork.parameters.json][vnet-parameters]**. Este archivo define la estructura de la VNet para las máquinas virtuales y otros componentes en la nube. Incluye valores, como el nombre, el espacio de direcciones, subredes y las direcciones de los servidores DNS necesarios. Tenga en cuenta que las direcciones DNS se muestra en este ejemplo hacer referencia a las direcciones IP de los servidores DNS locales y también en el servidor DNS de Azure predeterminado. Modificar estas direcciones para hacer referencia a su propia configuración DNS si no usa el entorno local de ejemplo:

    ```json
    {
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "ra-adfs-network-rg",
                "addressPrefixes": [
                    "10.0.0.0/16"
                ],
                "subnets": [
                    {
                        "name": "dmz-private-in",
                        "addressPrefix": "10.0.0.0/27"
                    },
                    {
                        "name": "dmz-private-out",
                        "addressPrefix": "10.0.0.32/27"
                    },
                    {
                        "name": "dmz-public-in",
                        "addressPrefix": "10.0.0.64/27"
                    },
                    {
                        "name": "dmz-public-out",
                        "addressPrefix": "10.0.0.96/27"
                    },
                    {
                        "name": "mgmt",
                        "addressPrefix": "10.0.0.128/25"
                    },
                    {
                        "name": "GatewaySubnet",
                        "addressPrefix": "10.0.255.224/27"
                    },
                    {
                        "name": "web",
                        "addressPrefix": "10.0.1.0/24"
                    },
                    {
                        "name": "biz",
                        "addressPrefix": "10.0.2.0/24"
                    },
                    {
                        "name": "data",
                        "addressPrefix": "10.0.3.0/24"
                    },
                    {
                        "name": "adds",
                        "addressPrefix": "10.0.4.0/27"
                    },
                    {
                        "name": "adfs",
                        "addressPrefix": "10.0.5.0/27"
                    },
                    {
                        "name": "proxy",
                        "addressPrefix": "10.0.6.0/27"
                    }
                ],
                "dnsServers": [
                    "192.168.0.4",
                    "192.168.0.5",
                    "168.63.129.16"
                ]
            }
        }
    }
    ```

- ** [virtualMachines adds.parameters.json ] [ virtualmachines-adds-parameters] **. Este archivo configura las VM ejecuta agrega en la nube. La configuración consta de dos máquinas virtuales. Debe cambiar el nombre de usuario administrador y la contraseña en el `virtualMachineSettings` sección y, opcionalmente, puede modificar el tamaño de máquina virtual para que coincidan con los requisitos del dominio:

    Para obtener más información, vea [Ampliación de Active Directory para Azure][extending-ad-to-azure].

    ```json
            "virtualMachinesSettings": {
                "value": {
                    "namePrefix": "ra-adfs-ad",
                    "computerNamePrefix": "aad",
                    "size": "Standard_DS3_v2",
                    "osType": "Windows",
                    "adminUsername": "testuser",
                    "adminPassword": "AweS0me@PW",
                    "osAuthenticationType": "password",
                    "nics": [
                        {
                            "isPublic": "false",
                            "subnetName": "adds",
                            "privateIPAllocationMethod": "Static",
                            "startingIPAddress": "10.0.4.4",
                            "enableIPForwarding": false,
                            "dnsServers": [
                            ],
                            "isPrimary": "true"
                        }
                    ],
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "2012-R2-Datacenter",
                        "version": "latest"
                    },
                    "dataDisks": {
                        "count": 1,
                        "properties": {
                            "diskSizeGB": 127,
                            "caching": "None",
                            "createOption": "Empty"
                        }
                    },
                    "osDisk": {
                        "caching": "ReadWrite"
                    },
                    "extensions": [
                    ],
                    "availabilitySet": {
                        "useExistingAvailabilitySet": "No",
                        "name": "ra-adfs-as"
                    }
                }
            },
            "virtualNetworkSettings": {
                "value": {
                    "name": "ra-adfs-vnet",
                    "resourceGroup": "ra-adfs-network-rg"
                }
            },
            "buildingBlockSettings": {
                "value": {
                    "storageAccountsCount": 2,
                    "vmCount": 2,
                    "vmStartIndex": 1
                }
            }
        }
    ```

- ** [Agregar-agrega-dominio-controller.parameters.json][add-adds-domain-controller-parameters]**. Este archivo contiene la configuración para crear el dominio CONTOSO que ocupan los servidores agrega. Utiliza extensiones personalizadas que establecen el dominio y agregar los servidores agrega a él. A menos que cree servidores agrega adicionales (en cuyo caso debe agregarlas a la `vms` matriz), cambiar sus nombres predeterminada o si desea crear un dominio con un nombre diferente, no es necesario modificar este archivo.

- ** [equilibrador de carga adfs.parameters.json] [loadbalancer-adfs-parameters] ** El archivo contiene dos conjuntos de parámetros de configuración. La `virtualMachineSettings` sección define las máquinas virtuales que hospeda el servicio ADFS en la nube. De forma predeterminada, la secuencia de comandos crea dos de estos máquinas virtuales en el mismo conjunto de disponibilidad:

    ```json
        "virtualMachinesSettings": {
            "value": {
                "namePrefix": "ra-adfs-adfs",
                "computerNamePrefix": "adfs",
                "size": "Standard_DS1_v2",
                "osType": "windows",
                "adminUsername": "testuser",
                "adminPassword": "AweS0me@PW",
                "osAuthenticationType": "password",
                "nics": [
                    {
                        "isPublic": "false",
                        "subnetName": "adfs",
                        "privateIPAllocationMethod": "Static",
                        "startingIPAddress": "10.0.5.4",
                        "isPrimary": "true",
                        "enableIPForwarding": false,
                        "dnsServers": [ ]
                    }
                ],
                "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version": "latest"
                },
                "dataDisks": {
                    "count": 1,
                    "properties": {
                        "diskSizeGB": 128,
                        "caching": "None",
                        "createOption": "Empty"
                    }
                },
                "osDisk": {
                    "caching": "ReadWrite"
                },
                "extensions": [ ],
                "availabilitySet": {
                    "useExistingAvailabilitySet": "No",
                    "name": "ra-adfs-adfs-vm-as"
                }
            }
        }
        ...
        "buildingBlockSettings": {
            "value": {
                "storageAccountsCount": 2,
                "vmCount": 2,
                "vmStartIndex": 1
            }
        }
    ```

    La `loadBalancerSettings` sección proporciona la descripción del equilibrador de carga para estas máquinas virtuales. El equilibrador de carga pasa tráfico que aparece en el puerto 443 (HTTPS) a una u otra de las máquinas virtuales:

    ```json
        "loadBalancerSettings": {
            "value": {
                "name": "ra-adfs-adfs-lb",
                "frontendIPConfigurations": [
                    {
                        "name": "ra-adfs-adfs-lb-fe",
                        "loadBalancerType": "internal",
                        "internalLoadBalancerSettings": {
                            "privateIPAddress": "10.0.5.30",
                            "subnetName": "adfs"
                        }
                    }
                ],
                "backendPools": [
                    {
                        "name": "ra-adfs-adfs-lb-bep",
                        "nicIndex": 0
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "https-rule",
                        "frontendPort": 443,
                        "backendPort": 443,
                        "protocol": "Tcp",
                        "backendPoolName": "ra-adfs-adfs-lb-bep",
                        "frontendIPConfigurationName": "ra-adfs-adfs-lb-fe",
                        "probeName": "https-probe",
                        "enableFloatingIP": false
                    }
                ],
                "probes": [
                    {
                        "name": "https-probe",
                        "port": 443,
                        "protocol": "Tcp",
                        "requestPath": null
                    }
                ],
                "inboundNatRules": [ ]
            }
        },
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "johns-adfs-network-rg"
            }
        }
    ```

- ** [adfs-granja-dominio-join.parameters.json ] [ adfs-farm-domain-join-parameters] **. Este archivo contiene la configuración utilizada para agregar los servidores ADFS en el dominio CONTOSO. Solo necesita modificar este archivo si ha creado otros servidores ADFS (actualizar el `vms` de matriz en este caso), o ha cambiado el nombre de dominio.

- ** [gmsa.parameters.json][gmsa-parameters]**, ** [first.parameters.json de granja de servidores de adfs][adfs-farm-first-parameters]**, y ** [rest.parameters.json de granja de servidores de adfs][adfs-farm-rest-parameters]**. La secuencia de comandos utiliza la configuración de estos archivos para crear la granja de servidores ADFS. 

    El archivo *gmsa.parameters.json* contiene la configuración de la cuenta de servicio de grupo administrado utilizada por el servicio ADFS. Puede modificar este archivo si desea cambiar el nombre de la cuenta o el dominio.

    El archivo de *conjunto de servidores de adfs de first.parameters.json* contiene la información necesaria para crear la granja de servidores ADFS y agregar el primer servidor. Si ha cambiado el dominio o nombre de la cuenta de servicio de grupo administrado, debe actualizar este archivo.

    El archivo de *conjunto de servidores de adfs de rest.parameters.json* se usa para agregar los demás servidores ADFS para el conjunto de servidores. De nuevo, si ha cambiado el dominio o nombre de la cuenta de servicio de grupo administrado, debe actualizar este archivo. Actualizar la `vms` de matriz si ha creado otros servidores ADFS.

- ** [equilibrador de carga adfsproxy.parameters.json][loadBalancer-adfsproxy-parameters]**. Este archivo es similar en la estructura y el contenido en el archivo *adfs.parameters.json del equilibrador de carga* . Contiene los datos para generar el equilibrador de carga y servidores proxy ADFS.

- ** [first.parameters.json de granja de servidores de adfsproxy][adfsproxy-farm-first-parameters]**, y ** [rest.parameters.json de granja de servidores de adfsproxy][adfsproxy-farm-rest-parameters]**. La secuencia de comandos utiliza la configuración de estos archivos para crear la granja de servidores proxy ADFS. 

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. Este archivo contiene los valores que se usan para crear la puerta de enlace VPN de Azure en la nube que se usa para conectarse a la red local. Debe modificar la `sharedKey` valor en el `connectionsSettings` sección que coincida con el dispositivo VPN local. Para obtener más información, vea [implementar una arquitectura de red híbrida con Azure y VPN local][hybrid-azure-on-prem-vpn].

- ** [dmz private.parameters.json] [ dmz-private-parameters] ** y ** [dmz public.parameters.json ] [ dmz-public-parameters] **. Estos archivos configuran entrante (público) salientes (privadas) laterales y de las máquinas virtuales que forman parte de la DMZ, proteger los servidores de la nube. Para obtener más información sobre estos elementos y su configuración, vea [implementar una DMZ entre Azure e Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- ** [web.parameters de equilibrador de carga de json][loadBalancer-web-parameters]**, ** [biz.parameters de equilibrador de carga de json][loadBalancer-biz-parameters]**, y ** [data.parameters de equilibrador de carga de json][loadBalancer-data-parameters]**. Estos archivos parámetros contienen las especificaciones de la máquina virtual de los niveles de acceso a web, business y datos y configurar equilibradores de carga para cada nivel. Estas son las VM que hospedan las aplicaciones web y bases de datos y a continuación, realizan las cargas de trabajo de la empresa para la organización. Puede modificar el tamaño y el número de máquinas virtuales en cada nivel según sus necesidades.

- ** [virtualMachines mgmt.parameters.json][virtualMachines-mgmt-parameters]**. Este archivo contiene la configuración de la casilla salto y administración máquinas virtuales. Solo es posible tener inicio de sesión y acceso administrativo a las VM en la web, business y niveles de datos desde el cuadro saltar. De forma predeterminada, la secuencia de comandos crea una sola *Standard_DS1_v2* VM, pero se puede modificar este archivo para crear máquinas virtuales más grandes o más si la carga de trabajo de administración es probable que sea significativa.

## <a name="solution-deployment"></a>Implementación de soluciones

La solución supone los siguientes requisitos previos:

- Tiene una suscripción existente de Azure en la que puede crear grupos de recursos.

- Haya descargado e instalado la versión más reciente de Powershell de Azure. Consulte [aquí] [ azure-powershell-download] para obtener instrucciones.

Para ejecutar la secuencia de comandos que se implementa la solución:

1. Mover a carpeta apropiada en el equipo local y cree las subcarpetas siguientes:

    - Secuencias de comandos

    - Las secuencias de comandos y parámetros

    - Parámetros de secuencias de comandos y línea

    - Parámetros de secuencias de comandos o Azure

2. Descargar la [Implementación ReferenceArchitecture.ps1] [ solution-script] archivo a la carpeta de secuencias de comandos

3. Descargar el contenido de los [parámetros o los] [ on-premises-folder] a la carpeta de secuencias de comandos y parámetros/local:

4. Descargar el contenido de los [parámetros o azure] [ azure-folder] a la carpeta de secuencias de comandos y parámetros/Azure.

5. Editar el archivo de implementación ReferenceArchitecture.ps1 en la carpeta de secuencias de comandos y cambie las siguientes líneas para especificar los grupos de recursos que se deben creados o utilizados para mantener los recursos creados por la secuencia de comandos:

    ```powershell
    # Azure Onpremise Deployments
        $onpremiseNetworkResourceGroupName = "ra-adfs-onpremise-rg"
        
        # Azure ADDS Deployments
        $azureNetworkResourceGroupName = "ra-adfs-network-rg"
        $workloadResourceGroupName = "ra-adfs-workload-rg"
        $securityResourceGroupName = "ra-adfs-security-rg"
        $addsResourceGroupName = "ra-adfs-adds-rg"
        $adfsResourceGroupName = "ra-adfs-adfs-rg"
        $adfsproxyResourceGroupName = "ra-adfs-proxy-rg"
    ```

6. Editar los archivos de parámetros en las carpetas de secuencias de comandos y parámetros/Azure y secuencias de comandos, parámetros o local. Actualizar las referencias de grupo de recursos en estos archivos para que coincida con los nombres de los grupos de recursos asignados a las variables en el archivo de implementación ReferenceArchitecture.ps1. La siguiente tabla muestra qué archivos parámetro hacen referencia a qué grupo de recursos. Observe que los grupos *ra-adfs-carga de trabajo-c*, *ra-adfs-seguridad-c*, *ra-adfs-agrega-c*, *ra-adfs-adfs-c*y *ra-adfs-proxy-c* solo se usan en la secuencia de comandos de PowerShell y no aparecen en los archivos de parámetros.

  	|Grupo de recursos|Archivos de parámetros|
  	|--------------|--------------|
  	|RA-adfs-local-c|parameters\onpremise\connection.Parameters.JSON<br /> parameters\onpremise\virtualMachines-ADDS.Parameters.JSON<br />parameters\onpremise\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\onpremise\virtualNetwork.Parameters.JSON<br />parameters\onpremise\virtualNetworkGateway.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON
  	|RA-adfs-red-c|parameters\onpremise\connection.Parameters.JSON<br />parameters\azure\dmz-Private.Parameters.JSON<br />parameters\azure\dmz-Public.Parameters.JSON<br />parameters\azure\loadBalancer-ADFS.Parameters.JSON<br />parameters\azure\loadBalancer-adfsproxy.Parameters.JSON<br />parameters\azure\loadBalancer-Biz.Parameters.JSON<br />parameters\azure\loadBalancer-Data.Parameters.JSON<br />parameters\azure\loadBalancer-Web.Parameters.JSON<br />parameters\azure\virtualMachines-ADDS.Parameters.JSON<br />parameters\azure\virtualMachines-Mgmt.Parameters.JSON<br />parameters\azure\virtualNetwork-with-onpremise-and-Azure-DNS.Parameters.JSON<br />parameters\azure\virtualNetwork.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON (*dos veces*)

    Además, establecer la configuración de la local y la nube componentes, como se describe en la sección [componentes de la solución] [componentes de la solución].

7. Abra una ventana de PowerShell de Azure, mover a la carpeta de secuencias de comandos y ejecute el siguiente comando:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    Reemplazar `<subscription id>` con su identificador de suscripción de Azure.

    Para `<location>`, especifique un área de Azure, como por ejemplo `eastus` o `westus`.

    La `<mode>` parámetro puede tener uno de los siguientes valores:

    - `Onpremise`, para crear el entorno local simulada.

    - `Infrastructure`, para crear la infraestructura de VNet y abandonarán el cuadro en la nube.

    - `CreateVpn`, para generar la puerta de enlace de red virtual Azure y conectarse a la red local.

    - `AzureADDS`, para crear las máquinas virtuales que actúe como agrega servidores, implementar Active Directory en estas máquinas virtuales y crear el dominio en la nube.

    - `AdfsVm`Para crear las máquinas virtuales de ADFS y unirse a ellas en el dominio en la nube.

    - `ProxyVm`Para crear al proxy ADFS máquinas virtuales y unirse a ellas en el dominio en la nube.

    - `Prepare`, que realiza todas las tareas anteriores. **Esta es la opción recomendada si está creando una implementación completamente nueva y no tiene una infraestructura local existente.**

    >[AZURE.NOTE] También puede ejecutar la secuencia de comandos con un `<mode>` parámetro de `Workload` para crear el web, empresa y máquinas virtuales de nivel de datos y la red. Esta configuración no se incluye como parte de la `Prepare` modo.

    Si utiliza la `Prepare` opción, la secuencia de comandos toma varias horas en completarse y termina con el mensaje *preparación se ha completado. Instale el certificado a todas las máquinas virtuales de proxy y ADFS.*

8.  Reinicie el cuadro saltar (*ra-adfs-administración-vm1* en el grupo de *ra-adfs-seguridad-c* ) para permitir que su configuración DNS surta efecto.

9.  [Obtener un certificado SSL para ADFS] [ adfs_certificates] e instalar este certificado en las máquinas virtuales de ADFS. Tenga en cuenta que puede conectarse a las máquinas virtuales de ADFS a través del cuadro de salto. Las direcciones IP que son *10.0.5.4* y *10.0.5.5*. El nombre de usuario predeterminado es *contoso\testuser* con contraseña *AweSome@PW*.

    >[AZURE.NOTE] Los comentarios en la secuencia de comandos de implementar ReferenceArchitecture.ps1 en este momento proporcionan instrucciones detalladas para crear un certificado autofirmado y entidad emisora utilizando la `makecert` comando. Sin embargo, no utilice los certificados generados por makecert en un entorno de producción.

10. Ejecute el siguiente comando Powershell para configurar la granja de servidores ADFS:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Adfs
    ```

11. En el cuadro saltar, vaya a *https://adfs.contoso.com/adfs/ls/idpinitiatedsignon.htm* para probar la instalación de ADFS (puede aparecer una advertencia de certificado, puede pasar por alto para esta prueba). Compruebe que aparece la página de inicio de sesión de Contoso Corporation. Iniciar sesión como *contoso\testuser* con contraseña *AweS0me@PW*.

12. Instale el certificado SSL en el servidor proxy ADFS máquinas virtuales. Las direcciones IP que son *10.0.6.4* y *10.0.6.5*.

13. Ejecute el siguiente comando Powershell para configurar el primer servidor proxy ADFS:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy1
    ```

14. Siga las instrucciones que se muestra la secuencia de comandos para probar la instalación del primer servidor proxy ADFS.

15. Ejecute el siguiente comando Powershell para configurar el segundo servidor proxy ADFS primera:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy2
    ```

16. Siga las instrucciones que se muestra la secuencia de comandos para probar la configuración de proxy ADFS completa.

## <a name="next-steps"></a>Pasos siguientes

- [Azure Active Directory][aad].

- [Azure Active Directory B2C][aadb2c].

<!-- links -->

[vm-recommendations]: ./guidance-compute-single-vm.md#Recommendations
[naming-conventions]: ./guidance-naming-conventions.md
[implementing-active-directory]: ./guidance-identity-adds-extend-domain.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[DRS]: https://technet.microsoft.com/library/dn280945.aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048.aspx
[ADDRS]: https://technet.microsoft.com/library/dn486831.aspx
[plan-your-adfs-deployment]: https://msdn.microsoft.com/library/azure/dn151324.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[create_service_account_for_adfs_farm]: https://technet.microsoft.com/library/dd807078.aspx
[import_server_authentication_certificate]: https://technet.microsoft.com/library/dd807088.aspx
[adfs-configuration-database]: https://technet.microsoft.com/en-us/library/ee913581(v=ws.11).aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[establishing-federation-trust]: https://blogs.msdn.microsoft.com/alextch/2011/06/27/establishing-federation-trust/
[Deploying_a_federation_server_farm]: https://technet.microsoft.com/library/dn486775.aspx
[install_and_configure_the_web_application_proxy_server]: https://technet.microsoft.com/library/dn383662.aspx
[publish_applications_using_AD_FS_preauthentication]: https://technet.microsoft.com/library/dn383640.aspx
[managing-adfs-components]: https://technet.microsoft.com/library/cc759026.aspx
[oms-adfs-pack]: https://www.microsoft.com/download/details.aspx?id=41184
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[aad]: https://azure.microsoft.com/documentation/services/active-directory/
[aadb2c]: https://azure.microsoft.com/documentation/services/active-directory-b2c/
[adfs-intro]: ../active-directory/active-directory-aadconnect-azure-adfs.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/connection.parameters.json
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-public.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetworkGateway.parameters.json
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-adds.parameters.json
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[loadbalancer-adfs-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfs.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/add-adds-domain-controller.parameters.json
[gmsa-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/gmsa.parameters.json
[adfs-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-first.parameters.json
[adfs-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-rest.parameters.json
[adfs-farm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-domain-join.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-mgmt.parameters.json
[loadBalancer-adfsproxy-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfsproxy.parameters.json
[adfsproxy-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-first.parameters.json
[adfsproxy-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-rest.parameters.json
[0]: ./media/guidance-iaas-ra-secure-vnet-adfs/figure1.png "Proteger la arquitectura de red híbrida con Active Directory"
