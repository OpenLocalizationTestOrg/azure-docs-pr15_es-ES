<properties
    pageTitle="Implementación de alta disponibilidad entre geográficos AD FS en Azure con el administrador del tráfico de Azure | Microsoft Azure"
    description="En este documento aprenderá cómo implementar AD FS en Azure para alta disponibilidad."
    keywords="AD fs con el Administrador de tráfico de Azure, adfs con el Administrador de tráfico de Azure, geográficos, entre varios centros de datos, centros de datos geográficos, entre varios centros de datos geográficos, implementar AD FS en azure, implementar adfs azure, adfs azure, azure ad fs, implementar adfs, implementar ad fs, adfs en azure, implementar adfs en azure, implementar AD FS en azure, adfs azure, introducción a AD FS, Azure, AD FS en Azure, iaas , ADFS, mover adfs en azure"
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
    ms.date="09/01/2016"
    ms.author="anandy;billmath"/>
    
#<a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>Implementación de alta disponibilidad entre geográficos AD FS en Azure con el administrador del tráfico de Azure

[Implementación de AD FS en Azure](active-directory-aadconnect-azure-adfs.md) proporciona instrucciones paso a paso sobre cómo implementar una infraestructura de AD FS simple para su organización en Azure. Este artículo proporciona los siguientes pasos para crear una implementación de AD FS entre geográfica en Azure con el [Administrador de tráfico de Azure](../traffic-manager/traffic-manager-overview.md). Administrador de tráfico Azure ayuda a crear una vista geográfica alta disponibilidad y la infraestructura de AD FS de alto rendimiento para su organización haciendo uso de la gama de métodos de enrutamiento satisfacer necesidades distintas de la infraestructura.

Permite una infraestructura de AD FS entre geográficos altamente disponible:

* **Eliminación de punto de error:** Con las capacidades de migración tras error del Administrador de tráfico de Azure, se puede conseguir una infraestructura de AD FS altamente disponible incluso cuando uno de los centros de datos en un elemento del globo terráqueo deja de funcionar
* **Rendimiento mejorado:** Puede usar la implementación sugerida en este artículo para proporcionar una infraestructura de AD FS de alto rendimiento que puede ayudar a los usuarios autenticar con mayor rapidez. 

##<a name="design-principles"></a>Principios de diseño

![Diseño general](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

Principios de diseño básicos será mismos tal y como aparece en principios de diseño en el artículo de la implementación de AD FS en Azure. El diagrama anterior muestra una extensión simple de la implementación básica para otra región geográfica. A continuación se muestran algunos puntos para tener en cuenta cuando se amplía la implementación de la nueva región geográfica

* **De red Virtual:** Debe crear una nueva red virtual en la región geográfica que desea distribuir la infraestructura adicional de AD FS. En el diagrama anterior consulte Geo1 VNET y Geo2 VNET como las dos redes virtuales de cada región geográfica.

* **Controladores de dominio y servidores de AD FS de nuevo VNET geográfica:** Se recomienda para implementar los controladores de la nueva región geográfica para que los servidores de AD FS en la nueva región no tenga que ponerse en contacto con un controlador de dominio en otro lejos de red para completar una autenticación y lo que mejora el rendimiento de dominio.

* **Cuentas de almacenamiento:** Cuentas de almacenamiento están asociadas a una región. Dado que va a implementar máquinas en una nueva región geográfica, tendrá que crear nuevas cuentas de almacenamiento que se utilizará en la región.  

* **Grupos de seguridad de red:** Como no se pueden usar cuentas de almacenamiento, los grupos de seguridad de red creados en una región en otra región geográfica. Por lo tanto, deberá crear nueva red grupos de seguridad similares a los de la primera región geográfica para subred INT y DMZ en la nueva región geográfica.

* **Etiquetas de DNS para las direcciones IP públicas:** Administrador de tráfico Azure puede hacer referencia a extremos sólo a través de las etiquetas DNS. Por lo tanto, deberá crear etiquetas DNS para las direcciones IP públicas de equilibradores de carga externos.

* **Manager de tráfico de azure:** Administrador de tráfico de Microsoft Azure le permite controlar la distribución de tráfico de usuario a los extremos de servicio ejecuta en centros de datos distintos todo el mundo. Administrador de Azure tráfico funciona en el nivel DNS. Usa las respuestas de DNS para dirigir el tráfico de usuario final a distribuida globalmente extremos. A continuación, se conectan los clientes a esos extremos directamente. Con diferentes opciones de enrutamiento de rendimiento, Weighted y prioridad, que pueda elegir la opción de distribución están diseñadas especialmente para las necesidades de su organización. 

* **Net V a V red conectividad entre dos regiones:** No debe tener conectividad entre las redes virtuales propiamente dicho. Puesto que cada red virtual tiene acceso a los controladores de dominio y servidor AD FS y WAP en Sí, puede trabajar sin cualquier conectividad entre las redes virtuales en diferentes regiones. 

##<a name="steps-to-integrate-azure-traffic-manager"></a>Pasos para integrar Manager de tráfico de Azure

###<a name="deploy-ad-fs-in-the-new-geographical-region"></a>Implementar AD FS en la nueva región geográfica
Siga los pasos y las directrices de [implementación de AD FS en Azure](active-directory-aadconnect-azure-adfs.md) para implementar la misma topología de la nueva región geográfica.

###<a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>Etiquetas DNS para las direcciones IP públicas de equilibradores de carga de Internet opuestas (público)
Como se mencionó anteriormente, el Administrador de tráfico de Azure sólo puede hacer referencia a las etiquetas DNS como extremos y, por tanto, es importante crear etiquetas DNS para las direcciones IP públicas de equilibradores de carga externos. Debajo de la captura de pantalla muestra cómo puede configurar la etiqueta DNS para la dirección IP pública. 

![Etiqueta DNS](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

###<a name="deploying-azure-traffic-manager"></a>Implementación de Azure Manager tráfico

Siga los pasos siguientes para crear un perfil de administrador de tráfico. Para obtener más información, puede hacer referencia a [administrar un perfil de administrador de tráfico de Azure](../traffic-manager/traffic-manager-manage-profiles.md).

1. **Crear un perfil de administrador de tráfico:** Asigne un nombre único a su perfil de administrador de tráfico. Este nombre del perfil de forma parte del nombre DNS y actúa como un prefijo de la etiqueta de nombre de dominio de administrador de tráfico. El nombre o se agregará el prefijo a. trafficmanager.net para crear una etiqueta DNS para el tráfico de su administrador. La siguiente captura de pantalla muestra al administrador de tráfico prefijo DNS que se establece como mysts y etiqueta DNS resultante será mysts.trafficmanager.net. 

    ![Creación de perfil de administrador de tráfico](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
 
2. **Método de enrutamiento de tráfico:** Hay tres opciones de enrutamiento disponibles en el Administrador de tráfico:

    * Prioridad 
    * Rendimiento
    * Ponderado
    
    **El rendimiento** es la opción recomendada para lograr la infraestructura de AD FS de alta capacidad de respuesta. Sin embargo, puede elegir cualquier método de enrutamiento que mejor se adapte a sus necesidades de implementación. La funcionalidad de AD FS no se ve afectada por la opción de distribución seleccionada. Para obtener más información, consulte [métodos de enrutamiento de tráfico de administrador de tráfico](../traffic-manager/traffic-manager-routing-methods.md) . En el ejemplo de captura de pantalla anterior puede ver el método de **rendimiento** seleccionado.
   
3.  **Configurar extremos:** En la página Administrador de tráfico, haga clic en los extremos y seleccione Agregar. Se abrirá una página de extremo de agregar similar a la siguiente captura de pantalla
 
    ![Configurar extremos](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
 
    Para las entradas diferentes, siga las directrices siguientes:

    **Tipo:** Seleccione extremo Azure mientras se tendrá que señala a una dirección IP pública del Azure.

    **Nombre:** Crear un nombre que desea asociar con el extremo. Esto no es el nombre DNS y no tiene relación con los registros DNS.

    **Tipo de recurso de destino:** Seleccione la dirección IP pública como el valor de esta propiedad. 

    **Recursos de destino:** Se dará una opción para elegir entre distintas etiquetas DNS que tiene disponible en la suscripción. Elegir la etiqueta DNS para a.

    Agregar extremo para cada región geográfica que desea que el Administrador de tráfico de Azure para enrutar el tráfico a.
    Para obtener más información e instrucciones detalladas acerca de cómo agregar o configurar extremos en el Administrador de tráfico, consulte [Agregar, deshabilitar, habilitar o eliminar puntos finales](../traffic-manager/traffic-manager-endpoints.md)
    
4. **Configurar sondeo:** En la página Administrador de tráfico, haga clic en configuración. En la página Configuración, debe cambiar la configuración del monitor para sondear al puerto HTTP 80 y /adfs/probe de ruta de acceso relativa

    ![Configurar sondeo](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 

    >[AZURE.NOTE] **Asegúrese de que el estado de los extremos es en línea una vez completada la configuración**. Si todos los extremos están en estado 'Degradado', Administrador de tráfico de Azure hará un intento de mejor para enrutar el tráfico suponiendo que los diagnósticos es correcto y que todos los extremos son accesibles.

5. **Los registros DNS de modificación para el administrador del tráfico de Azure:** El servicio de federación debe ser un CNAME en el nombre de Azure tráfico administrador DNS. Crear un CNAME en los registros DNS públicos para que quien está intentando alcanzar el servicio de federación llegue el Administrador de tráfico de Azure.

    Por ejemplo, para señalar la fs.fabidentity.com de servicio de federación al administrador de tráfico, necesarias para actualizar el registro de recursos DNS para que sea lo siguiente:

    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

##<a name="test-the-routing-and-ad-fs-sign-in"></a>Probar el enrutamiento y en el inicio de sesión de AD FS   

###<a name="routing-test"></a>Prueba de enrutamiento

Una prueba muy básica para la ruta sería intente hacer ping al nombre DNS del servicio de federación desde un equipo en cada región geográfica. Según el método de enrutamiento seleccionado, el extremo que realmente hace ping se reflejarán en la interfaz de ping. Por ejemplo, si seleccionó el enrutamiento de rendimiento, se alcanzó el extremo más cercano de la región del cliente. A continuación se muestra la instantánea de dos ping dos equipos de cliente de región diferente, uno en la región de Asia oriental y otro en Estados Unidos oeste. 

![Prueba de enrutamiento](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

###<a name="ad-fs-sign-in-test"></a>AD FS inicio de sesión de prueba

La manera más sencilla de probar AD FS es mediante la página IdpInitiatedSignon.aspx. Para poder hacer que, es necesario para habilitar la IdpInitiatedSignOn en las propiedades de AD FS. Siga los pasos siguientes para comprobar la configuración de AD FS
 
1. Ejecutar la debajo de cmdlet en el servidor de AD FS, con PowerShell, que establezca habilitado. Establecer AdfsProperties - EnableIdPInitiatedSignonPage $true
2. Desde cualquier equipo externos acceso https://<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. Debe ver la página de AD FS como las siguientes:

    ![Prueba ADFS - desafío de autenticación](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)

    y en el inicio de sesión correctamente, le proporcionará un mensaje de éxito tal como se muestra a continuación:

    ![Prueba ADFS - autenticación correcta](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)
 
##<a name="related-links"></a>Vínculos relacionados
* [Implementación de AD FS básica en Azure](active-directory-aadconnect-azure-adfs.md)
* [Administrador de tráfico de Microsoft Azure](../traffic-manager/traffic-manager-overview.md)
* [Métodos de enrutamiento de tráfico de tráfico administrador](../traffic-manager/traffic-manager-routing-methods.md)

##<a name="next-steps"></a>Pasos siguientes
* [Administrar un perfil de administrador de tráfico de Azure](../traffic-manager/traffic-manager-manage-profiles.md)
* [Agregar, deshabilitar, habilitar o eliminar extremos](../traffic-manager/traffic-manager-endpoints.md) 

