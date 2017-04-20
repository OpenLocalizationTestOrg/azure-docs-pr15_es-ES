<properties 
    pageTitle="Guía para programadores administración pública de Azure" 
    description="Proporciona una comparación de características e instrucciones sobre cómo desarrollar aplicaciones para la administración pública de Azure" 
    services="" 
    cloud="gov"
    documentationCenter="" 
    authors="Joharve2" 
    manager="Chrisnie" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="azure-government" 
    ms.date="10/29/2015" 
    ms.author="jharve"/>


#  <a name="microsoft-azure-government-developer-guide"></a>Guía de programador de administración pública de Microsoft Azure 

<p> Administración pública de Microsoft Azure es un física y red instancia aislado de Microsoft Azure.  Esta guía para programadores le proporcionará detalles sobre las diferencias que los desarrolladores de aplicaciones y los administradores necesarias para interactuar y trabajar con estas áreas independientes de Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## <a name="in-this-topic"></a>En este tema


+ [Información general](#Overview)
+ [Guía para programadores](#Guidance)
+ [Características están disponibles en la administración pública de Microsoft Azure](#Features)
+ [Asignación de extremos](#Endpoint)
+ [Pasos siguientes](#next)


## <a name="Overview"></a>Información general

Administración pública de Microsoft Azure es una instancia independiente del servicio de Microsoft Azure las necesidades de seguridad y cumplimiento de organismos federales de Estados Unidos, estado y gobiernos locales y los proveedores de soluciones. Gobierno Azure ofrece físico y aislamiento de implementaciones no gobierno de red y filtrado personal de Estados Unidos. 

Microsoft proporciona varias herramientas para crear e implementar aplicaciones de nube global Microsoft Azure (servicio "Global") y los servicios de administración pública de Microsoft Azure de Microsoft.

Al crear e implementar aplicaciones a los desarrolladores de servicios de administración pública de Azure, en lugar del servicio Global, necesita conocer las diferencias claves de los dos servicios.  Específicamente alrededor de instalar y configurar su entorno de programación, configure los extremos, aplicaciones de escritura y su implementación como servicios al gobierno de Azure.

La información en este documento resume las diferencias y suplemento de la información disponible en el sitio de la(http://www.azure.com/gov "Administración pública de Azure") [Administración pública de Azure]y la [Biblioteca técnica de Microsoft Azure]de(http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") en MSDN. Oficial también puede encontrar información en muchas otras ubicaciones, como la [Microsoft Azure Trust Center] (https://azure.microsoft.com/support/trust-center/ "Microsoft Azure Trust Center" /), [Centro de documentación de Azure](https://azure.microsoft.com/documentation/) y en [Azure Blogs] (https://azure.microsoft.com/blog/ "Azure Blogs" /). 

Este contenido está destinado a partners y desarrolladores que están implementando para la administración pública de Microsoft Azure.



## <a name="Guidance"></a>Guía para programadores
Dado que la mayoría del contenido técnico que están disponible actualmente se supone que se desarrollan aplicaciones para el servicio Global en lugar de la administración pública de Microsoft Azure, es importante para asegurarse de que los desarrolladores son conscientes de las diferencias clave para las aplicaciones desarrolladas para estar hospedadas en la administración pública de Azure.

- En primer lugar, hay diferencias en las características y servicios, esto significa que determinadas características que se encuentran en regiones específicos del servicio Global no esté disponibles en la administración pública de Azure.

- En segundo término, para las características que se ofrecen en la administración pública de Azure, hay diferencias de configuración del servicio Global.  Por lo tanto, debe revisar el código de ejemplo, las configuraciones y pasos para asegurarse de que son crear y ejecutar en el entorno de servicios de nube de administración pública de Azure.


## <a name="Features"></a>Características están disponibles en la administración pública de Microsoft Azure
Gobierno Azure actualmente tiene los siguientes servicios disponibles en regiones nos GOV IOWA y nos GOV VIRGINIA:

- Máquinas virtuales de Windows
- Servicios de nube
- Almacenamiento de información
- Active Directory
- Programador
- Red virtual
- Base de datos SQL
- Archivos de Azure
- Media Services
- Administrador de tráfico
- Bus de servicio
- StorSimple
- Redis caché
- Copia de seguridad de Azure
- Automatización
- ExpressRoute
- etcetera.

Existen otros servicios y más servicios se agregará de forma continua.  Para obtener la lista más reciente de servicios, consulte la [página de las regiones](https://azure.microsoft.com/regions/#services) que resaltará cada área disponibles y sus servicios.  

Actualmente, nos GOV Iowa y nos GOV Virginia son los centros de datos admite la administración pública de Azure.  Consulte la página de regiones encima de los centros de datos actual y los servicios disponibles.

## <a name="Endpoint"></a>Asignación de extremos

Utilice la siguiente tabla para guiar al asignar extremos públicos de Microsoft Azure y base de datos SQL a extremos específicos de administración pública de Azure.


Tipo de servicio|Azure público|Administración pública de Azure
---|---|---
Portal de administración|Manage.windowsazure.com|Manage.windowsazure.us
General|*. windows.net|*. usgovcloudapi.net
Principales|*. Core.Windows.NET.|*. core.usgovcloudapi.net
Calcular|*. cloudapp.net|*. usgovcloudapp.net
Almacenamiento de blobs|*. blob.core.windows.net|   *. blob.core.usgovcloudapi.net
Almacenamiento de cola|*. queue.core.windows.net|*. queue.core.usgovcloudapi.net
Almacenamiento de tablas|*. table.core.windows.net|*. table.core.usgovcloudapi.net
Administración del servicio|Management.Core.Windows.NET|Management.Core.usgovcloudapi.NET
Base de datos SQL|*. database.windows.net|*. database.usgovcloudapi.net
Extremo de equilibrio de carga ARM|https://Management.Windows.NET|https://Management.usgovcloudapi.NET  

* Para la autenticación de ARM a través Azure AD, hacer referencia [Autenticar solicitudes de administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn790557.aspx)

## <a name="next"></a>Pasos siguientes

Si le interesa aprender más y acerca de la administración pública de Azure, aprovechar algunos de los siguientes vínculos.

- **[Suscribirse a una prueba](https://azuregov.microsoft.com/trial/azuregovtrial)**

- **[Adquirir y tener acceso a la administración pública de Azure](http://azure.com/gov)**

- **[Información general de la administración pública de Azure](/azure-government-overview)**

- **[Blog de Azure gobierno](http://blogs.msdn.com/b/azuregov/)**

- **[Cumplimiento de Azure](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md
