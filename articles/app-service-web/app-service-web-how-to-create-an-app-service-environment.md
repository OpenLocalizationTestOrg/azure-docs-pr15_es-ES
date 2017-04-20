<properties 
    pageTitle="Cómo crear un entorno de servicio de aplicaciones" 
    description="Descripción del flujo de creación para entornos de servicio de aplicación" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/22/2016" 
    ms.author="ccompy"/>

# <a name="how-to-create-an-app-service-environment"></a>Cómo crear un entorno de servicio de aplicaciones #

### <a name="overview"></a>Información general ###

Entornos de servicio de la aplicación (ASE) son una opción de servicio Premium del servicio de aplicación de Azure que ofrece una funcionalidad mejorada de la configuración que no está disponible en las marcas de varios inquilinos.  La característica ASE esencialmente implementa el servicio de aplicación de Azure en una red virtual de un cliente.  Para obtener un conocimiento mayor de las capacidades de aplicación de servicio entornos leer la [¿Qué es un entorno de servicio de la aplicación] [ WhatisASE] documentación.

### <a name="before-you-create-your-ase"></a>Antes de crear su ASE ###

Es importante tener en cuenta las cosas que no se puede cambiar.  Se muestran los aspectos que no puede cambiar sobre su ASE después de crearlo:

- Ubicación
- Suscripción
- Grupo de recursos
- VNet utilizado
- Subred utilizado 
- Tamaño de subred

Al elegir un VNet y especificar una subred, asegúrese de que es lo suficientemente grande como para adaptarse a cualquier aumento futuro.  

### <a name="creating-an-app-service-environment"></a>Crear un entorno de servicio de aplicaciones ###

Hay dos maneras de obtener acceso a la interfaz de usuario de la creación de ASE.  Puede ser encontrados en búsquedas en Azure Marketplace para ***Entorno de servicio de aplicación*** o a través de nuevo -> Web + Mobile -> entorno de aplicación de servicio.  Para crear un ASE:

1. Proporcionar el nombre de su ASE.  El nombre que se especifica la ASE se usará para las aplicaciones que creó en el ASE.  Si el nombre de la ASE es appsvcenvdemo sería el nombre del subdominio. *appsvcenvdemo.p.azurewebsites.net*.  Si creó una aplicación denominada *mytestapp* , por tanto, sería accesible en *mytestapp.appsvcenvdemo.p.azurewebsites.net*.  No puede usar el espacio en blanco en el nombre de su ASE.  Si utiliza mayúsculas en el nombre, el nombre de dominio será la versión en minúsculas total de ese nombre.  Si utiliza un ILB, a continuación, su Nom no se utiliza en el subdominio, pero en su lugar se especifica explícitamente durante la creación de ASE

    ![][1]

2. Seleccione la suscripción.  La suscripción se utiliza para su ASE también es el que se creará con todas las aplicaciones en ese ASE.  No se puede colocar su ASE en un VNet que está en otra suscripción

3. Seleccione o especifique un nuevo grupo de recursos.  El grupo de recursos que se utiliza para su ASE debe ser el mismo que se utiliza para su VNet.  Si selecciona una preexistente VNet la selección de grupo de recursos para su ASE se actualizará para reflejar de su VNet.

    ![][2]

4. Realice las selecciones de red Virtual y la ubicación.  Puede crear un nuevo VNet o seleccionar una VNet existente.  Si selecciona una nueva VNet puede especificar un nombre y una ubicación. El nuevo VNet tendrá la 192.268.250.0/23 intervalo dirección y una subred con el nombre **predeterminado** que se define como 192.168.250.0/24.  Simplemente también puede seleccionar un clásico preexistente o VNet del Administrador de recursos.  La selección del tipo de la dirección VIP determina si su ASE se puede acceder directamente desde internet (externo), o si usa un equilibrador de carga interno (ILB).  Para obtener más información sobre ellos leer [mediante un equilibrador de carga interno con un entorno de servicio de la aplicación][ILBASE].  Si selecciona un tipo de VIP de externo puede seleccionar cuántas direcciones IP externas se crea el sistema con fines de IPSSL.  Si selecciona interno debe especificar el subdominio que usará el ASE.  Puede implementar ASEs en redes virtuales que utilizan *ambos* intervalos de direcciones pública, espacios de direcciones *o* RFC1918 (es decir direcciones privadas).  Para usar una red virtual con un intervalo de direcciones pública, debe crear el VNet antes de tiempo.  Cuando se selecciona un VNet preexistente debe crear una nueva subred durante la creación de ASE.  **No puede usar una subred creada previamente en el portal.  Puede crear un ASE con una subred existente si crea su ASE usando una plantilla de administrador de recursos.**  Para crear un ASE desde un uso de la plantilla de la información, [crear un entorno de servicio de aplicación de la plantilla] [ ILBAseTemplate] y aquí, [crear un entorno de servicio de aplicación ILB desde plantilla][ASEfromTemplate].

### <a name="details"></a>Detalles ###

Se crea un ASE con sus colegas 2 y termina front-2.  Los servidores Front end actúe como los extremos HTTP/HTTPS y enviar tráfico a los trabajadores que son las funciones que las aplicaciones de host.   Puede ajustar la cantidad después de la creación de ASE y puede incluso configurar Autoescala reglas en los conjuntos de recursos.  Para obtener más detalles de escalado manual, administración y supervisión de un entorno de servicio de aplicación vaya aquí: [cómo configurar un entorno de servicio de aplicación][ASEConfig] 

En la subred usada por la ASE puede haber solo la uno ASE.  No se puede utilizar la subred cualquier cosa que no sea la ASE

### <a name="after-app-service-environment-creation"></a>Después de la creación del entorno de aplicación de servicio ###

Después de la creación de ASE puede ajustar:

- Cantidad de servidores Front end (mínimo: 2)
- Cantidad de trabajadores (mínimo: 2)
- Cantidad de direcciones IP disponibles para IP SSL
- Calcular el tamaño de recursos utilizados por termina frontal o trabajadores (tamaño mínimo de Front-End es P2)


Hay más detalles sobre manual escala, administración y supervisión de aplicación de servicio entornos aquí: [cómo configurar un entorno de servicio de aplicación][ASEConfig] 

Para obtener información sobre el ajuste automático hay una guía aquí: [cómo configurar Autoescala para un entorno de servicio de aplicación][ASEAutoscale]

Existen dependencias adicionales que no están disponibles para la personalización, como la base de datos y el almacenamiento.  Estos son gestionó Azure y vienen con el sistema.  El almacenamiento de sistema admite hasta 500 GB para todo el entorno de servicio de la aplicación y la base de datos se ajusta en Azure según sea necesario por la escala del sistema.


## <a name="getting-started"></a>Introducción
Todos los artículos y cómo-de para entornos de aplicación de servicio están disponibles en el [archivo Léame para entornos de aplicaciones de servicio](../app-service/app-service-app-service-environments-readme.md).

Para empezar con entornos de servicio de aplicación, vea [Introducción a entornos de aplicación de servicio][WhatisASE]

Para obtener más información acerca de la plataforma de servicio de la aplicación de Azure, vea [Servicio de aplicación de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-create-ilb-ase-resourcemanager/
