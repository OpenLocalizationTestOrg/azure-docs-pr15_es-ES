
<properties 
    pageTitle="Opciones de migración de RemoteApp de Azure | Microsoft Azure" 
    description="Obtenga información sobre las opciones de migración de RemoteApp de Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/06/2016" 
    ms.author="elizapo" />

# <a name="options-for-migrating-out-of-azure-remoteapp"></a>Opciones de migración de RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Si ha detenido usando RemoteApp de Azure debido a la [presentación de jubilación](https://go.microsoft.com/fwlink/?linkid=821148) o que ha terminado la evaluación, necesita migrar quitarlas Azure RemoteApp a otro servicio de aplicación. Existen dos métodos diferentes para migrar: una administración automática (a menudo denominadas infraestructura como servicio [IaaS]) implementación o una totalmente administrada (a menudo denominada plataforma como servicio) o el Software como servicio [PaaS y SaaS] oferta. 

Autoservicio IaaS es una implementación de personal que se administra, gestionada y su propiedad, implementar directamente en máquinas virtuales (VM) o sistemas físicos. En el otro extremo, un PaaS y SaaS totalmente gestionado ofreciendo es más como RemoteApp de Azure: un socio proporciona una capa de servicio sobre una solución de acceso remoto que controla operativas y mantenimiento, mientras que, como cliente, siga algunas imagen y aplicación de administración de.

Siga leyendo para obtener más información, junto con ejemplos de las diferentes opciones de hospedaje.    

## <a name="self-managed-iaas-solutions"></a>Soluciones de administración automática (IaaS)

### <a name="rds-on-iaas"></a>**RDS en IaaS** 
Puede implementar una implementación nativa de basada en sesión servicios de escritorio remoto (en Windows Server) usando RemoteApp o escritorios local o en un entorno hospedado (como en máquinas virtuales de Azure). RDS IaaS implementaciones es la mejores para los clientes que ya conocidos y que tienen experiencia técnica existente con implementaciones de RDS. 

> [AZURE.NOTE]
> Debe con Software Assurance (SA) de licencias de acceso de cliente RDS para usar esta opción de implementación de licencias por volumen.

Implementar RDS en máquinas virtuales de Azure es más fácil que nunca, cuando se usa implementación y revisiones plantillas (obtener una [Visión general](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/) y, a continuación, [vaya a conseguirlas](https://aka.ms/rdautomation)). Puede obtener las mismas capacidades de escalado elásticas con recursos de modelo de implementación clásica Azure (no los recursos de modelo de recursos de Azure) dentro de Azure RemoteApp mediante el uso de la [secuencia de comandos de ajuste de escala automático](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76), aunque hay más configuraciones y personalizaciones. Cuando se implementa RDS en máquinas virtuales de Azure, soporte técnico se proporciona a través de [Soporte técnico de Azure](https://azure.microsoft.com/support/plans/), el mismo profesionales de soporte técnico compatibles con RemoteApp de Azure. Puede obtener costo cálculos basados en su uso existente, póngase en contacto con [Soporte técnico de Azure](https://azure.microsoft.com/support/plans/), o puede realizar cálculos con un pronto se lanzaron Calculadora de costo.  Además, con máquinas virtuales de serie N (actualmente en privado preview), puede agregar vGPU - escuchar más información sobre cómo agregar vGPU y sobre cómo aprovechar [las mejoras de RDS en Windows Server 2016](https://myignite.microsoft.com/videos/2794) en nuestra sesión Ignite.   

Tenemos guías de implementación paso a paso de [Windows Server 2012 R2](http://aka.ms/rdsonazure) y [Windows Server 2016](http://aka.ms/rdsonazure2016) ayudar con la implementación. Consulte el [blog de escritorio remoto](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services) para obtener las últimas noticias.
 
### <a name="citrix-on-iaas"></a>**Citrix en IaaS** 
Una implementación de XenApp basada en sesión o XenDesktop puede ser de Citrix nativa implementado local o en un entorno hospedado (como en máquinas virtuales de Azure). 

Consulte la Guía de implementación paso a paso, [Citrix XA 7.6 en Azure](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx), para obtener más información. Obtenga más información acerca de [Citrix en Azure](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx), incluyendo una calculadora de precios. También puede encontrar un [contacto Citrix](http://citrix.com/English/contact/index.asp) abordar las opciones con.

## <a name="fully-managed-paassaas-offerings"></a>Totalmente administradas ofertas (PaaS y SaaS)

### <a name="citrix-cloud"></a>**Nube de Citrix** 
[Solución de nube existente Citrix](https://www.citrix.com/products/citrix-cloud/)idéntico arquitectura a Citrix XenApp Express. Citrix ofrece una [promoción de descuento de 50%](https://www.citrix.com/blogs/2016/10/03/special-promotion-for-microsoft-azure-remoteapp-customers/) a los clientes existentes de RemoteApp de Azure. 

### <a name="citrix-xenapp-express-in-tech-preview"></a>**Citrix XenApp Express (en Technical preview)**
[Registrar para su Technical preview](http://now.citrix.com/remoteapp)y ver su [sesión Ignite](https://myignite.microsoft.com/videos/2792) (empezando por minuto 20:30). XenApp Express es arquitectura idéntica a la nube de Citrix excepto incluye administración simplificada interfaz de usuario y otras características y funcionalidades que son similares a RemoteApp de Azure. 

Más información sobre [Citrix XenApp Express](http://now.citrix.com/remoteapp).   

### <a name="citrix-service-provider-program"></a>**Programa de proveedor de servicio de Citrix** 
El programa de proveedores de servicio de Citrix facilita para proveedores de servicios de ofrecer la simplificar de a SMB, la informática en nube virtual ofreciendo los servicios que quieren en un modelo de fácil y de pago. Proveedores de servicios de Citrix crecer su negocio con Microsoft SPLA y expandir sus inversiones en plataformas RDS con cualquier dispositivo, acceso desde cualquier lugar, el más amplio soporte de aplicaciones, una experiencia, mayor seguridad y escalabilidad. A su vez, proveedores de servicios de Citrix atraer más suscriptores, aumente la satisfacción del cliente y reducir los costos de operaciones. [Más información](http://www.citrix.com/products/service-providers.html) o [Buscar a un asociado](https://www.citrix.com/buy/partnerlocator.html).

### <a name="microsoft-hosted-service-provider"></a>**Proveedor de servicios de hospedado de Microsoft** 
Socios de hospedaje normalmente ofrecen totalmente administrado había hospedado de escritorio de Windows y servicio de la aplicación, que puede incluir la administración de los recursos de Azure, sistemas operativos, aplicaciones y departamento de soporte técnico con el partner de licencias de contratos con Microsoft y otros proveedores de software, además de un contrato de licencia de proveedor de servicio para permitir reventa de licencia de acceso de suscriptor (SAL). La información siguiente proporciona detalles y la información de contacto para algunos de los anfitriones especializadas en la asistencia a los clientes con la migración de RemoteApp de Azure. Consulte [la lista actual de proveedores de servicio hospedado](http://aka.ms/rdsonazurecertified) que haya completado el RDS en IaaS evaluación y la ruta de aprendizaje.  

#### <a name="aspex"></a>**ASPEX**
[ASPEX](http://www.aspex.be/en) especializado en proveedores de software independientes transición a la nube y ISV' buscan para optimizar sus instalaciones de nube actual. ASPEX ofrece una amplia gama de servicios administrados, devops y servicios de consultoría.  

Ubicación principal: Amberes, Bélgica

Región de operación: Europa occidental

Estado del Partner: [plata](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)

Proveedor de servicios de nube de Microsoft: Sí

Ofrecen soluciones de RemoteApp y escritorio basada en sesión: Sí, ambos

Soluciones de migración de Azure RemoteApp: Sí, [Obtenga más información](https://www.aspex.be/en/azure-remote-apps)

**Contacto:**

- Teléfono: +3232202198
- Correo:[info@aspex.be](mailto:info@aspex.be)
- Web: [http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>**Conexlink (nombre de la plataforma: MyCloudIT)**
[MyCloudIT](http://www.mycloudit.com) es una plataforma de automatización para empresas de TI simplificar, optimizar y ajustar el tamaño de la migración y la entrega de equipos de escritorio remotos, aplicaciones remotas y la infraestructura en la nube de Microsoft Azure. 

La plataforma MyCloudIT reduce el tiempo de implementación al 95%, Azure costo 30% y desplaza toda infraestructura de TI de sus clientes en la nube en cuestión de unas pocas pulsaciones de teclas. Partners ahora pueden administrar a los clientes de un panel de mandos global, los usuarios finales de servicio del mundo como nunca antes y aumentar los ingresos sin agregar sobrecarga adicional o amplia formación Azure.  

Ubicación principal: Dallas, Texas, EE

Región de operación: en todo el mundo

Estado del Partner: [oro](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)

Proveedor de servicios de nube de Microsoft: Sí

Ofrecen soluciones de RemoteApp y escritorio basada en sesión: Sí, ambos

Soluciones de migración de Azure RemoteApp: Sí, [Obtenga más información](https://mycloudit.com/remote-app-microsoft/)

**Contacto:**
- Tomás Garoutte, VP de desarrollo empresarial

   Teléfono: 972-218-0741

   Correo electrónico:[brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>**Acuutech**
[Acuutech](http://www.acuutech.com) especializado en soluciones hospedado escritorio, realiza todo el escritorio y aplicaciones ISV experiencias basadas en la tecnología de Microsoft a una base de clientes global de Azure y sus propios centros de datos.

Ubicación principal: Londres, Reino Unido; Singapur; Houston, TX

Región de operación: en todo el mundo

Estado del Partner: oro

Proveedor de servicios de nube de Microsoft: Sí

Ofrecen soluciones de RemoteApp y escritorio basada en sesión: Sí, ambos

Soluciones de migración de Azure RemoteApp: Sí, [Obtenga más información](http://www.acuutech.com/ara-migration/)

**Contacto:**

- Reino Unido:

  5/6 York casa, carretera Langston,

  Loughton, Essex IG10 3TQ
  
  Teléfono: + 44 (0) 20 8502 2155
 
- Singapur:

  Calle Cecil 100, #09-02, 
  
  El globo, Singapur 069532
  
  Teléfono: + 65 6709 4933
 
- América del Norte: 

  3601 S. Sandman St.
  
  Suite 200, Houston, TX 77098
  
  Teléfono: +1 713 691 0800

## <a name="need-more-help"></a>¿Necesita más ayuda?
¿Aún necesita ayuda para seleccionar o tiene más preguntas? Use uno de los métodos siguientes para obtener ayuda. 

1.  Un correo electrónico a [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2.  Póngase en contacto con [soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Iniciar abriendo un [caso de soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
3.  Comuníquese con nosotros. [Buscar un número de venta local](https://azure.microsoft.com/overview/sales-number/).
