<properties 
    pageTitle="¿Qué es RemoteApp de Azure? | Microsoft Azure" 
    description="Obtenga información sobre cómo compartir aplicaciones y recursos para cualquier dispositivo a través de RemoteApp de Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="what-is-azure-remoteapp"></a>¿Qué es RemoteApp de Azure?

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Azure RemoteApp incorpora la funcionalidad del programa de Microsoft RemoteApp local, con la copia de los servicios de escritorio remoto, en Azure. RemoteApp Azure le ayuda a proporcionar acceso remoto seguro a aplicaciones desde muchos dispositivos de otro usuario. Azure RemoteApp básicamente aloja sesiones de Terminal Server no persistente en la nube y llegar a usarlas y compartirlos con sus usuarios.

Con RemoteApp de Azure puede compartir aplicaciones y recursos con usuarios en prácticamente cualquier dispositivo. Se hospeda las aplicaciones en la nube, lo que significa que ocuparse de hardware y escala para satisfacer las necesidades de usuario. Todo lo que tiene que hacer es cargar las aplicaciones que desea compartir y, a continuación, obtener los usuarios usar dichas aplicaciones. [Los usuarios acceder a mantener sus propios dispositivos](remoteapp-clients.md), mientras se encarga de administrar todo el contenido a través del portal de Azure. Incluso tiene la opción de usando sus credenciales corporativos, lo que le permite garantizar la seguridad de aplicaciones y los datos.

Siga leyendo para obtener más información acerca de RemoteApp de Azure, o bien, si ya hemos seguro que [Pruébelo ahora](https://azure.microsoft.com/services/remoteapp/).

¿Tienes alguna pregunta sobre RemoteApp de Azure? Consulte nuestras [preguntas más frecuentes](remoteapp-faq.md).

RemoteApp de Azure es parte de la [Infraestructura de Escritorio Virtual de Microsoft](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**¡Nuevo!** ¿Desea obtener más información acerca de RemoteApp de Azure? ¿O bien, listo para validar RemoteApp de Azure en escala? Unirse a nuestro semanal [Preguntar a los expertos seminario Web](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website).

## <a name="azure-remoteapp-collections"></a>Colecciones de RemoteApp de Azure
Existen dos tipos de [colecciones de RemoteApp de Azure](remoteapp-collections.md):


- Una **colección de nube** hospedado en y almacena los datos para los programas en la nube. Los usuarios pueden obtener acceso a aplicaciones iniciando sesión con su cuenta de Microsoft o corporativas credenciales sincronizados o federado con Azure Active Directory.

    Elija una colección de nube cuando la aplicación que desea compartir no requiere una conexión a cualquier recurso de una red privada de su empresa (por ejemplo, a través de un dispositivo VPN). Si la aplicación usa recursos en Internet, OneDrive o Azure, una colección de nube funcionará para usted. También es la más rápida de crear.

- Una **colección de híbrido** se hospeda en y almacena datos en la nube de Azure, pero también permite los usuarios acceso a datos y recursos almacenados en su red local. Los usuarios pueden acceder a aplicaciones iniciando sesión con sus credenciales corporativas sincronizados o federado con Azure Active Directory.

    Elija una colección de híbrido si se requiere una conexión a los recursos de una red privada de su empresa. Por ejemplo, si la aplicación necesita obtener acceso a uno de estos procedimientos:

    - Servidores de archivos que se encuentra en una intranet
    - Quicken
    - Bases de datos detrás de un firewall

    Esto generalmente es más útil para grandes empresas con una gran cantidad de recursos en sus redes privadas que no se puede mover a la nube.

Las diferentes colecciones tienen diferentes opciones, incluidas las redes, para averiguar [qué colección](remoteapp-collections.md) funciona mejor para usted. 


### <a name="updating-your-collection"></a>Actualización de la colección
Una de las diferencias clave entre las colecciones híbrido y nube es cómo se administran las actualizaciones de software. Con una colección de nube que utiliza la imagen de Office 365 ProPlus u Office 2013 preinstalada, no tiene que preocuparse por las actualizaciones. El servicio se mantiene propio y se implementa actualizaciones de forma periódica, aplicaciones y el sistema operativo.

Para colecciones de híbridos, así como las colecciones de nube que use una imagen de la plantilla personalizada, es responsable de mantenimiento de la imagen y las aplicaciones. Para las imágenes de dominio, puede controlar las actualizaciones mediante herramientas como Windows Update, directiva de grupo o System Center.

Después de actualizar la imagen de la plantilla personalizada, se cargue la nueva imagen en la nube de Azure y, a continuación, actualizar la colección para usar la nueva imagen. (Puede hacerlo desde la página de Azure RemoteApp de **Inicio rápido** o en el panel.)

Para obtener más información, vea [actualizar la colección](remoteapp-update.md) .

## <a name="supported-remoteapp-clients"></a>Clientes compatibles de RemoteApp
RemoteApp Azure es compatible con las aplicaciones cliente de RemoteApp para Windows y Windows RT, así como las aplicaciones de escritorio remoto de Microsoft para Mac, iOS y Android. Los usuarios pueden usar estas aplicaciones en su teléfono móvil o calcular dispositivos para tener acceso a los nuevos programas de RemoteApp de Azure.

Para obtener más información acerca de los clientes, vea [obtener acceso a sus aplicaciones de Azure RemoteApp](remoteapp-clients.md) .

## <a name="next-steps"></a>Pasos siguientes
¡Vaya! ¡Pruébelo! Estos artículos de ayuda empezar con RemoteApp de Azure:

- [¿Qué tipo de colección debe para RemoteApp de Azure?](remoteapp-collections.md)
- [Crear una imagen de RemoteApp de Azure](remoteapp-imageoptions.md)
- [Cómo crear una colección de nube de RemoteApp de Azure](remoteapp-create-cloud-deployment.md)
- [Cómo crear una colección de híbrido de RemoteApp de Azure](remoteapp-create-hybrid-deployment.md)
- [¿Cómo licencias funciona en RemoteApp de Azure?](remoteapp-licensing.md)
- [Prácticas recomendadas para usar RemoteApp de Azure](remoteapp-bestpractices.md)
- [Preguntas más frecuentes de RemoteApp de Azure](remoteapp-faq.md)
 

### <a name="help-us-help-you"></a>Ayúdenos a ayudarle 
¿Sabía que, además de valoración de este artículo y realizar comentarios hacia abajo, a continuación, puede realizar cambios en el artículo? ¿Falta algo? ¿Algún error? ¿Escribir algo que es simplemente confuso? Desplazarse hacia arriba y haga clic en **Editar en GitHub** o en **Editar** para realizar cambios: los vaya a nosotros para revisión y, a continuación, una vez se aprobar en ellas, verá los cambios y mejoras aquí.