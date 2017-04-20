<properties
    pageTitle="Servicios de federación de Active Directory en Azure | Microsoft Azure"
    description="En este documento aprenderá cómo implementar AD FS en Azure para alta disponibilidad."
    keywords="implementar AD FS en azure, implementar adfs azure, adfs azure, azure ad fs, implementar adfs, implementar ad fs, adfs en azure, implementar adfs en azure, implementar AD FS en azure, adfs azure, introducción a AD FS, Azure, AD FS en Azure, iaas, ADFS, mover adfs en azure"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/03/2016"
    ms.author="anandy;billmath"/>

# <a name="ad-fs-deployment-in-azure"></a>Implementación de AD FS en Azure 

AD FS proporciona la federación de identidades simplificado, protegidos y capacidades de inicio de sesión único (SSO) Web. La federación con Azure AD o O365 permite a los usuarios autenticar con credenciales local y tener acceso a todos los recursos en la nube. Por tanto, es importante tener una infraestructura de AD FS altamente disponible para garantizar el acceso a los recursos en ambos local y en la nube. Implementación de AD FS en Azure puede ayudar a lograr la alta disponibilidad necesaria con esfuerzos mínimos.
Existen varias ventajas de la implementación de AD FS en Azure, a continuación se muestran algunas de ellas:

* **Alta disponibilidad** : con la potencia de los conjuntos de disponibilidad de Azure, se asegura una infraestructura altamente disponible.
* **Fácil de escala** : necesita más performance? Migrar fácilmente a los equipos más eficaces con unos pocos clics en Azure
* **Redundancia de cruz Geo** con Azure Geo redundancia puede estar seguro de que su infraestructura está altamente disponible en todo el mundo
* **Fácil de administrar** : con opciones de administración muy simplificado en Azure portal de administración de la infraestructura es muy fácil y complicaciones 

## <a name="design-principles"></a>Principios de diseño

![Diseño de la distribución](./media/active-directory-aadconnect-azure-adfs/deployment.png)

El diagrama anterior muestra la topología recomendada básica para empezar a implementar la infraestructura de AD FS en Azure. A continuación se muestran los principios de los distintos componentes de la topología:

* **DC / servidores de ADFS**: si tiene menos de 1.000 usuarios simplemente puede instalar el rol de AD FS en los controladores de dominio. Si no desea ninguna repercusión en los controladores de dominio o si tiene más de 1.000 usuarios, a continuación, implementar AD FS en servidores independientes.
* **Servidor de WAP** – es necesario implementar servidores Proxy de la aplicación Web, para que los usuarios pueden llegar a AD FS cuando no se encuentran en la red de la empresa también.
* **DMZ**: servidores Proxy de aplicación de la Web se pondrán en la DMZ y se permite el acceso de solo TCP/443 entre la DMZ y la subred interna.
* **Equilibradores de carga**: para asegurarse de alta disponibilidad de los servidores de AD FS y Proxy de la aplicación Web, se recomienda utilizar un equilibrador de carga interno para servidores de AD FS y equilibrador de carga de Azure para servidores Proxy de la aplicación Web.
* **Conjuntos de disponibilidad**: para proporcionar redundancia a la implementación de AD FS, se recomienda agrupar máquinas virtuales de dos o más de un conjunto de disponibilidad para cargas de trabajo similares. Esta configuración garantiza que durante un evento de mantenimiento planificado o, al menos una máquina virtual estará disponible
* **Cuentas de almacenamiento**: se recomienda tener dos cuentas de almacenamiento. Tener una cuenta de almacenamiento solo puede conducir a la creación de un punto de error y puede provocar la implementación no esté disponible en un escenario poco probable que la cuenta de almacenamiento deja de funcionar. Dos cuentas de almacenamiento le ayudará a asociar una cuenta de almacenamiento para cada línea de error.
* **Separación de red**: se deberían implementar servidores Proxy de la aplicación Web en una red DMZ independiente. Puede dividir una red virtual en dos subredes y, a continuación, implementar los servidores Proxy de la aplicación Web en una subred aislado. Puede configurar las opciones de grupo de seguridad de red para cada subred y permitir solo requiere comunicación entre las dos subredes. Se proporcionan más detalles por escenario de implementación más adelante

##<a name="steps-to-deploy-ad-fs-in-azure"></a>Pasos para implementar AD FS en Azure

Los pasos que se mencionan en esta sección describen la guía para implementar el debajo de infraestructura de AD FS representada en Azure.

### <a name="1-deploying-the-network"></a>1. implementar la red

Como se describió anteriormente, se puede bien crear dos subredes en una única red virtual o bien crear dos redes virtuales completamente diferentes (VNet). En este artículo se centrará en implementar una única red virtual y dividirlo en dos subredes. Esto es actualmente un enfoque más sencillo como dos VNets independientes, se requeriría un VNet a VNet puerta de enlace para las comunicaciones.

**1.1 crear red virtual**

![Crear una red virtual](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)
    
En el portal de Azure, seleccione una red virtual y puede implementar la red virtual y una subred inmediatamente con un solo clic. Subred INT también se define y está ahora listo para máquinas virtuales que se agregará.
El siguiente paso es agregar otra subred a la red, es decir, la subred DMZ. Para crear la subred DMZ, simplemente

* Seleccione la red recién creada
* En las propiedades, seleccione subred
* En la subred panel, haga clic en el botón Agregar
* Proporcione la información de espacio de nombre y dirección de subred para crear la subred

![Subred](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)


![Subred DMZ](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. creación de la red de grupos de seguridad**

Un grupo de seguridad de la red (GSN) contiene una lista de reglas de la lista de Control de acceso (ACL) que permitir o denegar el tráfico de red de las instancias de máquina virtual en una red Virtual. NSGs se pueden asociadas con subredes o instancias VM individuales en esa subred. Cuando un GSN está asociada a una subred, se aplican las reglas ACL a todas las instancias de máquina virtual de esa subred.
Con el fin de esta guía, vamos a crear dos NSGs: uno para una red interna y una DMZ. Tendrá la etiqueta NSG_INT y NSG_DMZ respectivamente.

![Crear GSN](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

Una vez creado el GSN, será 0 entrantes y 0 las reglas de salida. Una vez que los roles de los servidores respectivos están instalados y en funcionamiento, se pueden realizar las reglas de entrada y salidas según el nivel de seguridad deseado.

![Inicializar GSN](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

Después de crean el NSGs, asociar NSG_INT subred INT y NSG_DMZ con subred DMZ. Una captura de pantalla de ejemplo se muestra a continuación:

![Configurar GSN](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* Haga clic en subredes para abrir el panel para subredes
* Seleccione la subred para asociarla con la GSN 

Después de la configuración, el panel para subredes debe ser similar a continuación:

![Subredes después GSN](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. Crear conexión a local**

Se necesita una conexión a local para implementar el controlador de dominio (DC) en azure. Azure ofrece diversas opciones de conectividad para conectar su infraestructura local en la infraestructura de Azure.

* Sitio de punto
* Una red virtual de sitio a otro
* ExpressRoute

Se recomienda usar ExpressRoute. ExpressRoute le permite crear conexiones privadas entre los centros de datos de Azure y la infraestructura en sus instalaciones o en un entorno de la ubicación. Conexiones de ExpressRoute no vaya a través de Internet pública. Ofrecen más confiabilidad, velocidades, latencia menor y mayor seguridad que las conexiones habituales en Internet.
Mientras se recomienda usar ExpressRoute, puede elegir cualquier método de conexión más adecuado para su organización. Para obtener más información sobre ExpressRoute y las distintas opciones de conectividad con ExpressRoute, lea la [información general técnica de ExpressRoute](https://aka.ms/Azure/ExpressRoute).

### <a name="2-create-storage-accounts"></a>2. crear cuentas de almacenamiento

Para mantener la alta disponibilidad y evitar depender de una cuenta de almacenamiento única, puede crear dos cuentas de almacenamiento. Dividir los equipos en cada conjunto de disponibilidad en dos grupos y asigne a cada grupo de una cuenta de almacenamiento independiente.

![Crear cuentas de almacenamiento](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. crear conjuntos de disponibilidad

Para cada función (DC/AD FS y WAP), crear conjuntos de disponibilidad que contendrán 2 equipos como mínimo. Esto le ayudará a conseguir una mayor disponibilidad de cada rol. Al crear la disponibilidad de conjuntos, es fundamental decidir en lo siguiente:
* **Dominios de error**: máquinas virtuales en el mismo dominio errores comparten el mismo origen de energía y el conmutador de red física. Se recomienda un mínimo de 2 dominios de error. El valor predeterminado es 3 y puede dejarlo como está con el fin de esta implementación
* **Actualizar dominios**: equipos que pertenecen al mismo dominio de actualización se reinician juntos durante una actualización. Desea tener mínimo de 2 actualizar los dominios. El valor predeterminado es 5 y puede dejarlo como está con el fin de esta implementación

![Conjuntos de disponibilidad](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

Crear los siguientes conjuntos de disponibilidad

| Conjunto de disponibilidad | Función | Dominios de error | Actualizar los dominios |
|:----------------:|:----:|:-----------:|:-----------|
| contosodcset | DC/ADFS | 3 | 5 |
| contosowapset | WAP | 3 | 5 |

### <a name="4--deploy-virtual-machines"></a>4. implementar máquinas virtuales
El siguiente paso es implementar máquinas virtuales de Windows que va a hospedar las diferentes funciones de la infraestructura. Se recomienda un mínimo de dos equipos en cada conjunto de disponibilidad. Crear seis máquinas virtuales de Windows para la implementación básica.

| Equipo | Función | Subred | Conjunto de disponibilidad | Cuenta de almacenamiento | Dirección IP |
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|contosodc1|DC/ADFS|INT|contosodcset|contososac1|Estático|
|contosodc2|DC/ADFS|INT|contosodcset|contososac2|Estático|
|contosowap1|WAP|DMZ|contosowapset|contososac1|Estático|
|contosowap2|WAP|DMZ|contosowapset|contososac2|Estático|

Como habrá observado, se ha especificado ningún GSN. Esto es porque azure le permite usar GSN en el nivel de subred. A continuación, puede controlar el tráfico de red del equipo utilizando la GSN individual asociado, ya sea la subred o bien el objeto NIC. Leer de activado más [¿Qué es un grupo de seguridad de la red (GSN)](https://aka.ms/Azure/NSG).
Dirección IP estática se recomienda si administra el DNS. Puede usar el DNS de Azure y en los registros DNS de su dominio, consulte las nuevas máquinas por su FQDN de Azure.
El panel de la máquina virtual debe ser similar a continuación una vez completada la implementación:

![Máquinas virtuales implementados](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5. configurar el controlador de dominio o servidores de AD FS
 Para autenticar las solicitudes entrantes, AD FS deberá ponerse en contacto con el controlador de dominio. Para guardar el viaje costoso de Azure en DC local para la autenticación, se recomienda implementar una réplica del controlador de dominio en Azure. Para lograr alta disponibilidad, es aconsejable crear un conjunto de disponibilidad al menos 2 controladores de dominio.

|Controlador de dominio|Función|Cuenta de almacenamiento|
|:-----:|:-----:|:-----:|
|contosodc1|Réplica|contososac1|
|contosodc2|Réplica|contososac2|

* Promover a los dos servidores como controladores de dominio de réplica con DNS
* Configure los servidores de AD FS instalando el rol de AD FS mediante el administrador del servidor.

###<a name="6---deploying-internal-load-balancer-ilb"></a>6. implementar equilibrador de carga interno (ILB)

**6.1. crear el ILB**

Para implementar un ILB, seleccione equilibradores de carga en el portal de Azure y haga clic en Agregar (+).
>[AZURE.NOTE] Si no ve **Equilibradores de carga** en el menú, haga clic en **Examinar** en la esquina inferior izquierda del portal y desplácese hasta que vea **Equilibradores de carga**.  A continuación, haga clic en la estrella amarilla para agregarlo al menú. Ahora, seleccione el icono de equilibrador de carga de nuevo para abrir el panel para empezar la configuración del equilibrador de carga.

![Examinar equilibrador de carga](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **Nombre**: dar cualquier nombre adecuado para el equilibrador de carga
* **Combinación**: puesto que este equilibrador de carga se colocarán delante de los servidores de AD FS y está pensado para conexiones de red interna solo, seleccione "Interna"
* **Una red virtual**: elija la red virtual donde va a implementar su AD FS
* **Subred**: elija aquí la subred interna
* **Asignación de direcciones IP**: dinámico

![Equilibrador de carga interno](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)
 
Tras hacer clic en crear y se implementa el ILB, debería ver en la lista de equilibradores de carga:

![Equilibradores de carga después de ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)
 
Siguiente paso consiste en configurar el grupo de back-end y el sondeo de back-end.

**6.2. Configurar grupo de back-end ILB**

Seleccione el ILB recién creado en el panel de equilibradores de carga. Se abrirá el panel de configuración. 
1.  Seleccione grupos de back-end en el panel de configuración
2.  En el panel Agrupación de back-end de agregar, haga clic en la máquina virtual de agregar
3.  Aparecerá una pantalla donde puede elegir conjunto de disponibilidad
4.  Elija el conjunto de disponibilidad de AD FS

![Configurar grupo de back-end ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)
 
**6.3. configuración de sondeo**

En el panel de configuración ILB, seleccione sondeos.
1.  Haga clic en Agregar
2.  Proporcionar detalles de sondeo una. **Nombre**: b de nombre de sondeo. **Protocolo**: c TCP. **Puerto**: 443 (HTTPS) d. **Intervalo**: 5 (valor predeterminado): este es el intervalo en el que se ILB se sondeo los equipos en el grupo e de back-end. **Umbral de mal estado**: 2 (predeterminado val ue): este es el umbral de errores consecutivos sondeo tras la cual ILB declarar un equipo en el grupo de back-end no responde y dejar de enviar tráfico a él.

![Configurar el sondeo ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)
 
**6.4. crear reglas de equilibrio de carga**

Con el fin de equilibrar el tráfico de forma eficaz, debe configurarse la ILB con reglas de equilibrio de carga. Para crear una regla, equilibrio de carga 
1.  Seleccione la regla en el panel de configuración de la ILB equilibrio de carga
2.  Haga clic en Agregar en el panel de la regla de equilibrio de carga
3.  En el panel de la regla de equilibrio de carga de agregar una. **Nombre**: proporcione un nombre para la regla b. **Protocolo**: seleccione c TCP. **Puerto**: 443 d.. **Puerto de back-end**: 443 e. **Grupo de back-end**: seleccione el grupo que ha creado para AD FS clúster f anteriores. **Sondeo**: seleccione el sondeo creado anteriormente para servidores de AD FS

![Configurar ILB equilibrio de reglas](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5. actualizar DNS con ILB**

Vaya al servidor DNS y crear un CNAME para la ILB. El registro CNAME debería para el servicio de federación con la dirección IP que apunta a la dirección IP de la ILB. Por ejemplo, si la dirección de ILB DIP es 10.3.0.8 y el servicio de federación instalado es fs.contoso.com, a continuación, cree un CNAME para fs.contoso.com apuntando a 10.3.0.8.
Esto se asegurará de que toda la comunicación relacionada con el fin de fs.contoso.com arriba en el ILB y se enrutan correctamente.

###<a name="7---configuring-the-web-application-proxy-server"></a>7. la configuración del servidor Proxy de la aplicación Web

**7.1. configuración de los servidores Proxy de la aplicación Web para llegar a los servidores de AD FS**

Con el fin de garantizar que puedan llegar a los servidores de AD FS detrás de la ILB servidores Proxy de la aplicación Web, cree un registro en la %systemroot%\system32\drivers\etc\hosts para el ILB. Observe que el nombre distintivo (DN) debe ser el nombre de servicio de federación, por ejemplo fs.contoso.com. Y la entrada IP debe ser de dirección IP de ILB (10.3.0.8 como en el ejemplo).

**7.2. instalar el rol de Proxy de aplicación Web**

Después de asegurarse de que los servidores Proxy de aplicación Web son puedos llegar a los servidores de AD FS detrás ILB, a continuación puede instalar a los servidores Proxy de la aplicación Web. Servidores Proxy de la aplicación Web no unirse al dominio. Instalar las funciones de Proxy de aplicación Web en los dos servidores Proxy de la aplicación Web seleccionando el rol de acceso remoto. El administrador del servidor le guiará para completar la instalación de WAP.
Para obtener más información sobre cómo implementar WAP, lea [instalar y configurar el servidor de Proxy de aplicaciones Web](https://technet.microsoft.com/library/dn383662.aspx).

###<a name="8---deploying-the-internet-facing-public-load-balancer"></a>8. implementar Internet opuestas equilibrador de carga (pública)

**8.1. crear Internet opuestas equilibrador de carga (pública)**
 
En el portal de Azure, seleccione equilibradores de carga y, a continuación, haga clic en Agregar. En el panel de equilibrador de carga de crear, escriba la información siguiente
1. **Nombre**: nombre para el equilibrador de carga
2. **Combinación**: público: esta opción indica Azure que este equilibrador de carga necesita una dirección pública.
3. **Dirección IP**: crear una nueva dirección IP (dinámica)

![Equilibrador de carga hacia Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

Después de la implementación, el equilibrador de carga aparecerán en la lista de equilibradores de carga.

![Lista de equilibrador de carga](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)
 
**8.2. asignar una etiqueta DNS a la dirección IP pública**

Haga clic en la entrada del equilibrador de carga recién creado en el panel de equilibradores de carga para que aparezca el panel de configuración. Seguir pasos para configurar la etiqueta DNS para la dirección IP pública:
1.  Haga clic en la dirección IP pública. Se abrirá el panel de la dirección IP pública y su configuración
2.  Haga clic en configuración
3.  Proporcionar una etiqueta DNS. Esto se convertirá en la etiqueta DNS pública que puede tener acceso desde cualquier lugar, por ejemplo contosofs.westus.cloudapp.azure.com. Puede agregar una entrada en el DNS externo para el servicio de federación (como fs.contoso.com) que se resuelve en la etiqueta DNS del equilibrador de carga externo (contosofs.westus.cloudapp.azure.com).

![Configurar internet opuestas equilibrador de carga](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Configurar opuestas equilibrador de carga (DNS) de internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3. Configurar grupo de back-end para Internet opuestas (público) equilibrador de carga** 

Siga los mismos pasos crear equilibrador de carga interno para configurar el grupo de back-end para Internet opuestas equilibrador de carga (pública) como la disponibilidad para los servidores WAP. Por ejemplo, contosowapset.

![Configurar grupo de back-end del equilibrador de carga de Internet opuestas](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)
 
**8,4. configurar sondeo**

Siga los mismos pasos de configuración del equilibrador de carga interno para configurar el sondeo para el grupo de back-end de servidores WAP.

![Configurar el sondeo del equilibrador de carga de Internet opuestas](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)
 
**8.5. crear reglas de equilibrio de carga**

Siga los mismos pasos ILB para configurar el equilibrio de carga regla para TCP 443.

![Configurar reglas de equilibrio del equilibrador de carga de Internet opuestas](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)
 
###<a name="9---securing-the-network"></a>9. protección de la red

**9.1. proteger la subred interna**

En general, necesita las siguientes reglas para proteger eficazmente la subred interna (en el orden, como se indica a continuación)

|Regla|Descripción|Flujo|
|:----|:----|:------:|
|AllowHTTPSFromDMZ| Permitir la comunicación HTTPS de DMZ | Entrada |
|DenyInternetOutbound| No tiene acceso a internet | Saliente |

![Reglas de acceso INT (entrante)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

[comentario]: <> (![reglas de acceso INT (entrante)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png)) [comentario]: <> (![reglas de acceso INT (salientes)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))
 
**9.2. proteger la subred DMZ**

|Regla|Descripción|Flujo|
|:----|:----|:------:|
|AllowHTTPSFromInternet| Permitir HTTPS desde internet a la DMZ | Entrada|
|DenyInternetOutbound|  Nada excepto HTTPS a internet está bloqueado | Saliente |

![Reglas de acceso EXT (entrante)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

[comentario]: <> (![reglas de acceso EXT (entrante)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png)) [comentario]: <> (![reglas de acceso EXT (salientes)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))

>[AZURE.NOTE] Si la autenticación de certificado de usuario del cliente (autenticación TLS de cliente con X509 certificados de usuario) es necesario, AD FS requiere TCP puerto 49443 estar habilitadas para el acceso de entrada.

###<a name="10--test-the-ad-fs-sign-in"></a>10. probar el inicio de sesión de AD FS

La manera más sencilla es probar AD FS es mediante la página IdpInitiatedSignon.aspx. Para poder hacer que, es necesario para habilitar la IdpInitiatedSignOn en las propiedades de AD FS. Siga los pasos siguientes para comprobar la configuración de AD FS
1.  Ejecutar la debajo de cmdlet en el servidor de AD FS, con PowerShell, que establezca habilitado.
    Establecer AdfsProperties - EnableIdPInitiatedSignonPage $true 
2.  Desde cualquier equipo externos acceso https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx  
3.  Debe ver la página de AD FS como las siguientes:

![Página de inicio de sesión de prueba](./media/active-directory-aadconnect-azure-adfs/test1.png)

En el inicio de sesión correctamente, le proporcionará un mensaje de éxito tal como se muestra a continuación:

![Probar el éxito](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Plantilla de implementación de AD FS en Azure

La plantilla implementa una configuración de 6 máquina 2 controladores de dominio, AD FS y WAP.

[AD FS en la plantilla de implementación de Azure](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

Puede usar una red virtual existente o crear un nuevo VNET durante la implementación de esta plantilla. A continuación se muestran los distintos parámetros disponibles para personalizar la implementación con la descripción del uso del parámetro en el proceso de implementación. 

| Parámetro | Descripción |
|:--------|:-----|
|Ubicación| Región para implementar los recursos en, por ejemplo, Oriente nosotros. |
|StorageAccountType| El tipo de la cuenta de almacenamiento creada|
|VirtualNetworkUsage| Indica si una red virtual nueva se creará o usar una existente|
|VirtualNetworkName| El nombre de la red Virtual para crear, obligatorio en el uso de una red virtual nuevo o existente|
|VirtualNetworkResourceGroupName| Especifica el nombre del grupo de recursos donde reside la red virtual existente. Cuando se utiliza una red virtual existente, se convertirá en un parámetro obligatorio para que la implementación puedan encontrar el identificador de la red virtual existente|
|VirtualNetworkAddressRange| El intervalo de direcciones de la nueva VNET, obligatorio si crear una nueva red virtual|
|InternalSubnetName| El nombre de la subred interna, obligatoria en ambas opciones de uso de red virtual (nuevos o existentes)|
|InternalSubnetAddressRange| El rango de la dirección de la subred interna, que contiene los servidores controladores de dominio y ADFS, obligatorio si crear una nueva red virtual.|
|DMZSubnetAddressRange| El rango de la dirección de la subred dmz, que contiene los servidores proxy de aplicaciones de Windows, obligatorio si crear una nueva red virtual.|
|DMZSubnetName| El nombre de la subred interna, obligatoria en ambas opciones de uso de red virtual (nuevos o existentes). |
|ADDC01NICIPAddress| La dirección IP interna del primer controlador de dominio, esta dirección IP se asignará estática al DC y debe ser una dirección ip válida dentro de la subred interna|
|ADDC02NICIPAddress| La dirección IP interna del segundo controlador de dominio, esta dirección IP se asignará estática al DC y debe ser una dirección ip válida dentro de la subred interna|
|ADFS01NICIPAddress| La dirección IP interna del primer servidor ADFS, esta dirección IP se asignará estática al servidor de ADFS y debe ser una dirección ip válida dentro de la subred interna|
|ADFS02NICIPAddress| La dirección IP interna del segundo servidor ADFS, esta dirección IP se asignará estática al servidor de ADFS y debe ser una dirección ip válida dentro de la subred interna|
|WAP01NICIPAddress| La dirección IP interna del primer servidor WAP, esta dirección IP se asignará estática al servidor WAP y debe ser una dirección ip válida dentro de la subred DMZ|
|WAP02NICIPAddress| La dirección IP interna del segundo servidor WAP, esta dirección IP se asignará estática al servidor WAP y debe ser una dirección ip válida dentro de la subred DMZ|
|ADFSLoadBalancerPrivateIPAddress| La dirección IP interna del equilibrador de carga ADFS, esta dirección IP se asignará estática al equilibrador de carga y debe ser una dirección ip válida dentro de la subred interna|
|ADDCVMNamePrefix| Prefijo de nombre de la máquina virtual para controladores de dominio|
|ADFSVMNamePrefix| Prefijo de nombre de la máquina virtual para servidores ADFS|
|WAPVMNamePrefix| Prefijo de nombre de la máquina virtual para servidores WAP|
|ADDCVMSize| El tamaño de la memoria virtual de los controladores de dominio|
|ADFSVMSize| El tamaño de la memoria virtual de los servidores ADFS|
|WAPVMSize| El tamaño de la memoria virtual de los servidores WAP|
|AdminUserName| El nombre del administrador local de los equipos virtuales|
|AdminPassword| La contraseña de la cuenta de administrador local de los equipos virtuales|

## <a name="additional-resources"></a>Recursos adicionales
* [Conjuntos de disponibilidad](https://aka.ms/Azure/Availability ) 
* [Equilibrador de carga de Azure](https://aka.ms/Azure/ILB)
* [Equilibrador de carga interno](https://aka.ms/Azure/ILB/Internal)
* [Equilibrador de carga con conexión a Internet](https://aka.ms/Azure/ILB/Internet)
* [Cuentas de almacenamiento](https://aka.ms/Azure/Storage )
* [Redes virtuales Azure](https://aka.ms/Azure/VNet)
* [AD FS y vínculos de Proxy de aplicación Web](http://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>Pasos siguientes

* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
* [Configurar y administrar el uso de Azure AD Connect de AD FS](active-directory-aadconnectfed-whatis.md)
* [Implementación de alta disponibilidad entre geográficos AD FS en Azure con el administrador del tráfico de Azure](active-directory-adfs-in-azure-with-azure-traffic-manager.md)




