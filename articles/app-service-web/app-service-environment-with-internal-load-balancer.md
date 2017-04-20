<properties
    pageTitle="Crear y usar un equilibrador de carga interno con un entorno de servicio de la aplicación | Microsoft Azure"
    description="Crear y usar un ASE con un ILB"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Usar un equilibrador de carga interno con un entorno de servicio de aplicaciones #

La característica Environments(ASE) de servicio de aplicación es una opción de servicio Premium del servicio de aplicación de Azure que ofrece una funcionalidad mejorada de la configuración que no está disponible en las marcas de varios inquilinos.  La característica ASE esencialmente implementa el servicio de aplicación de Azure en su Network(VNet) Virtual de Azure.  Para obtener un conocimiento mayor de las capacidades de aplicación de servicio entornos leer la [¿Qué es un entorno de servicio de la aplicación] [ WhatisASE] documentación.  Si no conoce las ventajas del funcionamiento en un VNet leer las [P+F de red Virtual de Azure][virtualnetwork].  


## <a name="overview"></a>Información general ##


Un ASE se puede implementar con un extremo de accesibilidad de internet o con una dirección IP en su VNet.  Para establecer la dirección IP a la dirección VNet debe implementar su ASE con un Balancer(ILB) de carga interno.  Cuando su ASE está configurado con un ILB proporciona:

- su propio dominio o subdominio.  Para que sea fácil, este documento asume subdominio pero puede configurar en ambos casos.  
- el certificado usado para HTTPS
- Administración de DNS para el subdominio.  


En cambio, puede hacer cosas como:

- aplicaciones de la intranet de host, como la línea de aplicaciones empresariales, bien en la nube que se tiene acceso a través de un sitio a sitio o ExpressRoute VPN
- aplicaciones de host en la nube que no aparecen en los servidores DNS públicos
- crear aplicaciones de back-end de internet aislado que sus aplicaciones front-end pueden integrar de forma segura con


#### <a name="disabled-functionality"></a>Funcionalidad deshabilitada ####

Hay algunas cosas que no puede hacer cuando se usa un ASE ILB.  Incluyen las cosas:

- usar IPSSL
- asignar direcciones IP a aplicaciones específicas
- comprar y usar un certificado con una aplicación a través del portal.  Por supuesto aún puede obtener certificados directamente con una entidad emisora de certificados y usarlo con sus aplicaciones, no a través del portal de Azure.


## <a name="creating-an-ilb-ase"></a>Crear un ASE ILB ##

Crear un ASE ILB no es muy diferente de crear un ASE normalmente.  Para más información sobre la creación de un ASE lea [cómo crear un entorno de servicio de la aplicación][HowtoCreateASE].  El proceso para crear un ASE ILB es el mismo entre crear un VNet durante la creación de ASE o seleccionar una VNet ya existente.  Para crear un ASE ILB: 

1.  En el portal de Azure, seleccione **Nuevo -> Web + Mobile -> entorno de aplicación de servicio**
2.  Seleccione la suscripción
3.  Seleccionar o crear un grupo de recursos
4.  Seleccione o cree una VNet
5.  Crear una subred si selecciona una VNet
6.  Seleccione **Virtual o ubicación de red-> configuración de VNet** y establezca el tipo de VIP a interno
7.  Proporcione el nombre de subdominio (será el subdominio que se usa para las aplicaciones creadas en este ASE)
8.  Haga clic en Aceptar y, a continuación, crear


![][1]


En el módulo de red Virtual hay una opción de configuración de VNet.  Permite seleccionar entre una VIP externo o interno VIP.  El valor predeterminado es externo.  Si ha establecido como externo su ASE usará a una VIP accesibles de internet.  Si selecciona interno, su ASE se configurará con un ILB en una dirección IP dentro de su VNet.  


Después de seleccionar interno, se elimina la posibilidad de agregar más direcciones IP para su ASE y en su lugar debe proporcionar el subdominio de la ASE.  En un ASE con una VIP externo se utiliza el nombre de la ASE en el subdominio para aplicaciones creadas en ese ASE.  
Si su ASE se denominaba ***contosotest*** y la aplicación en que se denominaba ASE ***mytest*** después el subdominio sería el formato ***contosotest.p.azurewebsites.net*** y la dirección URL de esa aplicación sería ***mytest.contosotest.p.azurewebsites.net***.  
Si define el tipo de VIP a interno, su Nom no se usa en el subdominio para la ASE.  Especificar el subdominio explícitamente.  Si su subdominio era ***contoso.corp.net*** y realiza una aplicación que ASE había denominado ***timereporting*** la dirección URL de esa aplicación sería ***timereporting.contoso.corp.net***.


## <a name="apps-in-an-ilb-ase"></a>Aplicaciones en un ASE ILB ##

Crear una aplicación en un ASE ILB es lo mismo que crear una aplicación en un ASE normalmente.  

1. En el portal de Azure, seleccione **Nuevo -> Web + Mobile -> Web** o **móvil** o **Aplicación API**
2. Escriba el nombre de aplicación
2. Seleccione la suscripción
3. Seleccione o cree el grupo de recursos
4. Seleccione o cree la aplicación de servicio Plan(ASP).  Si crea un nuevo archivo ASP, a continuación, seleccione su ASE como la ubicación y desea que su ASP que se creen en el grupo de trabajo.  Cuando se crea el ASP selecciona su ASE como la ubicación y el grupo de trabajo.  Al especificar el nombre de la aplicación se muestra que el subdominio en el nombre de la aplicación se reemplaza por el subdominio para su ASE.   
5. Seleccione crear.  Debe seleccionar la casilla de verificación **Anclar al panel** si desea que la aplicación que se muestran en el panel.  

![][2]


En el nombre de la aplicación se actualice el nombre de subdominio para reflejar el subdominio de su ASE.  


## <a name="post-ilb-ase-creation-validation"></a>Validación de creación de ILB ASE de publicación ##

Un ASE ILB es ligeramente diferente de la ASE ILB.  Como ya se ha indicado que necesita para administrar sus propios DNS y también debe proporcionar su propio certificado para las conexiones HTTPS.  


Después de crear su ASE observará que el subdominio muestra el subdominio que especificó y no hay un nuevo elemento en el menú de **configuración** denominado **Certificado de ILB**.  Se crea la ASE con un certificado autofirmado lo que facilita la prueba HTTPS.  El portal le indicará que debe proporcionar su propio certificado para HTTPS pero esto es la unidad que tenga un certificado que va a su subdominio.  

![][3]


Si está simplemente probando cosas y no sabe cómo crear un certificado, puede usar la aplicación de consola de MMC de IIS para crear un certificado autofirmado.  Una vez que se crea puede exportar como un archivo .pfx y luego cárguelo en la interfaz de usuario de certificado ILB. Cuando se tiene acceso a un sitio protegido con un certificado autofirmado, el explorador le dará una advertencia de que el sitio que tiene acceso no es seguro porque no se puede validar el certificado.  Si desea evitar que advertencia necesita un certificado debidamente firmado que coincida con el subdominio y tiene una cadena de confianza que se reconoce su explorador.

![][6]

Si desea probar el flujo con sus propios certificados y probar el acceso HTTP y HTTPS a su ASE:

1.  Vaya a la interfaz de usuario de ASE después de haber creado ASE **ASE -> Configuración -> ILB certificados**
2.  Configurar certificado ILB seleccionando archivo pfx de certificado y proporcione la contraseña.  Este paso le llevará algún tiempo para procesar y se mostrará el mensaje que está en curso una operación de escala.
3.  Obtener la dirección ILB su ASE (**ASE -> Propiedades -> dirección IP Virtual**)
4.  Crear una aplicación web en ASE después de creación 
5.  Crear una máquina virtual si no tiene uno en ese VNET (no en la misma subred que el salto ASE o cosas)
6.  Configurar DNS para el subdominio.  Puede utilizar un carácter comodín con su subdominio en DNS o si desea hacer algunas pruebas sencillas, editar el archivo de hosts en la máquina virtual para establecer el nombre de la aplicación web a la dirección VIP IP.  Si su ASE tenía el nombre de subdominio. ilbase.com y realiza la mytestapp de la aplicación web de modo que sería dirigido a mytestapp.ilbase.com después que establecer en el archivo hosts.  (En Windows el archivo hosts está en C:\Windows\System32\drivers\etc\)
7.  Use un explorador en máquina y vaya a http://mytestapp.ilbase.com (o lo que es el nombre de la aplicación web con su subdominio)
8.  Use un explorador en máquina y vaya a https://mytestapp.ilbase.com, deberá aceptar la falta de seguridad, si usa un certificado autofirmado.  


La dirección IP de su ILB aparece en las propiedades como la dirección IP Virtual

![][4]


## <a name="using-an-ilb-ase"></a>Usar un ASE ILB ##

#### <a name="network-security-groups"></a>Grupos de seguridad de red ####

Un ASE ILB permite aislamiento de red para las aplicaciones de las aplicaciones no sean accesibles o incluso conocidos por internet.  Esta es una excelente para el hospedaje de sitios de la intranet como línea de aplicaciones empresariales.  Cuando necesite restringir el acceso incluso más aún puede usar Groups(NSGs) de seguridad de red para controlar el acceso en el nivel de red. 


Si desea usar NSGs para restringir aún más el acceso debe asegurarse de que no se interrumpe la comunicación que el ASE necesarias para funcionar.  Aunque el acceso HTTP/HTTPS es sólo a través de la ILB usado por la ASE la ASE todavía depende de recursos fuera de la VNet.  Para ver qué acceso de red sigue siendo necesario buscar la información en el documento en [Controlar el tráfico de entrada a un entorno de servicio de la aplicación] [ ControlInbound] y el documento en [Detalles de la configuración de red para entornos de aplicación de servicio con ExpressRoute][ExpressRoute].  


Para configurar su NSGs que debe saber la dirección IP que utiliza Azure para administrar su ASE.  Esta dirección IP también es la dirección IP saliente de su ASE si hace solicitudes de internet.  Para buscar esta dirección IP dirección vaya a **Configuración -> Propiedades** y busque la **Dirección de IP salientes**.  

![][5]


#### <a name="general-ilb-ase-management"></a>Administración de general ILB ASE ####

Administrar un ASE ILB es prácticamente igual que un ASE normalmente.  Debe cambiar la escala de los grupos de trabajo para alojar más instancias ASP y escalar los servidores Front-End para controlar la mayor cantidad de tráfico HTTP/HTTPS.  Para obtener información general sobre cómo administrar la configuración de un ASE, lea el documento sobre la [configuración de un entorno de servicio de la aplicación][ASEConfig].  


Los elementos de administración adicionales son la administración de certificados y administración de DNS.  Debe obtener y cargar el certificado usado para HTTPS después de la creación de ILB ASE y reemplazarla antes de que caduque.  Porque Azure es el propietario del dominio base podemos proporcionar certificados para ASEs con una VIP externos.  Dado que el subdominio utilizado por un ASE ILB puede ser cualquier cosa, debe proporcionar su propio certificado para HTTPS. 


#### <a name="dns-configuration"></a>Configuración de DNS ####

Cuando se usa a una VIP externos Azure administra el DNS.  Cualquier aplicación creada en su ASE se agrega automáticamente a DNS de Azure que es un DNS público.  En un ASE ILB debe administrar sus propios DNS.  Para un determinado subdominio como contoso.corp.net debe crear DNS A registros que apuntan a su dirección de ILB para:

    * 
    publicar *.SCM ftp 


## <a name="getting-started"></a>Introducción
Todos los artículos y cómo-de para entornos de aplicación de servicio están disponibles en el [archivo Léame para entornos de aplicaciones de servicio](../app-service/app-service-app-service-environments-readme.md).

Para empezar con entornos de servicio de aplicación, vea [Introducción a entornos de aplicación de servicio][WhatisASE]

Para obtener más información acerca de la plataforma de servicio de la aplicación de Azure, vea [Servicio de aplicación de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
