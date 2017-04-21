<properties 
    pageTitle="Cómo crear una colección de nube de RemoteApp de Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo crear una implementación de RemoteApp de Azure que guarda los datos en la nube de Azure." 
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
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="how-to-create-a-cloud-collection-of-azure-remoteapp"></a>Cómo crear una colección de nube de RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Existen dos tipos de [colecciones de RemoteApp de Azure](remoteapp-collections.md): 

- Nube: reside totalmente en Azure. Puede guardar todos los datos en la nube (para una colección de nube) o para conectarse a la colección de un VNET y guardar datos.   
- Híbrido: incluye una red virtual para el acceso local - Esto requiere el uso de Azure AD y un entorno de Active Directory local.

Este tutorial le guiará a través del proceso de creación de una colección de la nube. Hay cuatro pasos: 

1.  Crear una colección de RemoteApp de Azure.
2.  También puede configurar la sincronización de directorios. Si está utilizando Azure AD + de Active Directory, debe sincronizar los usuarios, los contactos y las contraseñas de su Active Directory local a su inquilino de Azure AD.
5.  Publicar aplicaciones.
6.  Configurar el acceso de usuario.


**Antes de empezar**

Debe hacer lo siguiente antes de crear la colección:

- [Registrarse](https://azure.microsoft.com/services/remoteapp/) para RemoteApp de Azure. 
- Recopilar información acerca de los usuarios que desea conceder acceso a. Puede ser información de la cuenta de Microsoft o información de cuenta de trabajo de Active Directory para los usuarios.
- Este procedimiento supone que son ambos va a utilizar una de las imágenes de plantilla proporcionadas como parte de la suscripción o que ya ha cargado la imagen de la plantilla que desee usar. Si necesita cargar una imagen de plantilla diferente, puede hacerlo desde la página de imágenes de plantilla. Simplemente haga clic en **cargar una imagen de la plantilla** y siga los pasos del asistente. 
- ¿Desea usar la imagen de Office 365 ProPlus? Consulte información [aquí](remoteapp-officesubscription.md).
- ¿Desea proporcionar aplicaciones personalizadas o LOB programas? Crear una nueva [imagen](remoteapp-imageoptions.md) y usarla en la colección de la nube.
- Averiguar si necesita conectarse a un VNET. Si elige conectarse a un VNET, asegúrese de que cumple las [directrices de tamaño](remoteapp-vnetsizing.md) y que [puede conectarse a RemoteApp](remoteapp-vnet.md). Consulte el [artículo de planeación de VNET ](remoteapp-planvnet.md)para obtener más información.
- Si está usando un VNET, decida si desea unirse a su dominio de Active Directory local.

## <a name="step-1-create-a-cloud-collection---with-or-without-a-vnet"></a>Paso 1: Crear una colección de nube - con o sin un VNET##


Realice los siguientes pasos para **crear una colección de nube**:

1. En el portal de administración, vaya a la página de RemoteApp.
2. Haga clic en **Nueva > crear rápido**.
3. Escriba un nombre para la colección y seleccione su región.
4. Elegir el plan que desea usar - estándar o básica.
5. Elija la plantilla que desea utilizar para esta colección. 

    **Sugerencia:** Su suscripción de RemoteApp viene con [imágenes de plantilla](remoteapp-images.md) que contienen Office 365 u Office 2013 (para uso prueba) programas, otras publicado (por ejemplo, Word) y otras listo para publicar. También puede crear una nueva [imagen](remoteapp-imageoptions.md) y usarla en la colección de la nube.


1. Haga clic en **crear RemoteApp colección**.
    
    **Importante:** Pueden tardar hasta 30 minutos en aprovisionar la colección.

Después de crear la colección de RemoteApp de Azure, haga doble clic en el nombre de la colección. Que se muestre la página de **Inicio rápido** : este es donde haya terminado de configurar la colección.

Realice los siguientes pasos para crear una **colección de VNET + de nube**:

1. En el portal de administración, vaya a la página de RemoteApp de Azure.
2. Haga clic en **nuevo** > **crear con VNET**.
3. Escriba un nombre para la colección.
4. Elegir el plan que desea usar - estándar o básica.
5. Elija la VNET ya ha creado. ¿No sabe cómo hacerlo? Por ahora, los pasos son en el tema [híbrido](remoteapp-create-hybrid-deployment.md) .
6. Decida si quiere unirse a la colección a su dominio. En caso afirmativo, deberá usar AD Connect para integrar Azure AD y el entorno de Active Directory. Que se trata en debajo en el **paso 2**.
6. Haga clic en **crear RemoteApp colección**.


## <a name="step-2-configure-active-directory-directory-synchronization-optional"></a>Paso 2: Configurar la sincronización de directorios de Active Directory (opcional) ##

Si desea utilizar Active Directory, RemoteApp de Azure requiere la sincronización de directorios entre Azure Active Directory y Active Directory local para sincronizar los usuarios, los contactos y las contraseñas en el inquilino de Azure Active Directory. Información sobre la planificación, vea [Configurar Active Directory para RemoteApp de Azure](remoteapp-ad.md) . También puede ir directamente a [AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) para obtener más información.

## <a name="step-3-publish-apps"></a>Paso 3: Publicar aplicaciones ##

Una aplicación de Azure RemoteApp es la aplicación o el programa que desea proporcionar a los usuarios. Se encuentra en la imagen de plantilla que ha cargado para la colección. Cuando un usuario tiene acceso a una aplicación, aparece la aplicación para que se ejecute en su entorno local, pero realmente se ejecuta en una máquina virtual en Azure. 

Antes de que los usuarios pueden obtener acceso a aplicaciones, debe publicarlas: publicar aplicaciones permite los usuarios acceso a las aplicaciones a través del cliente de escritorio remoto.
 
Puede publicar varias aplicaciones a la colección de RemoteApp de Azure. Desde la página de publicación, haga clic en **Publicar** para agregar un programa. O bien puede publicar en el menú **Inicio** de la imagen de la plantilla o especificando la ruta de acceso en la imagen de plantilla para la aplicación. Si elige agregar en el menú **Inicio** , elija la aplicación para publicar. Si decide incluir la ruta de acceso a la aplicación, proporcione un nombre para la aplicación y la ruta de acceso donde está instalado en la imagen de la plantilla.

## <a name="step-4-configure-user-access"></a>Paso 4: Configurar el acceso de usuario ##

Ahora que ha creado la colección, debe agregar los usuarios que desea que puedan usar los recursos remotos. Si está utilizando Active Directory, los usuarios que proporcionan acceso a deben existir en el inquilino de Active Directory asociado con la suscripción se utiliza para crear esta colección.

1.  Desde la página de inicio rápido, haga clic en **Configurar el acceso de usuarios**. 
2.  Escriba la cuenta de trabajo (de Active Directory) o la cuenta de Microsoft que desea conceder acceso.

    **Notas:** 

    Asegúrese de que usa el “user@domain.com” formato.

    Si está utilizando Office 365 ProPlus en la colección, debe utilizar las identidades de Active Directory para los usuarios. Esto le permite validar licencias. 

3.  Después de que los usuarios se validarán, haga clic en **Guardar**.


## <a name="next-steps"></a>Pasos siguientes ##

Eso es todo: se ha creado e implementado la colección de nube de RemoteApp de Azure. El siguiente paso es hacer que los usuarios descargar e instalar al cliente de escritorio remoto. Puede encontrar la dirección URL para el cliente en la página de inicio rápido de Azure RemoteApp. A continuación, que inicie la sesión en el cliente de los usuarios y tener acceso a las aplicaciones que ha publicado.

### <a name="help-us-help-you"></a>Ayúdenos a ayudarle 
¿Sabía que, además de valoración de este artículo y realizar comentarios hacia abajo, a continuación, puede realizar cambios en el artículo? ¿Falta algo? ¿Algún error? ¿Escribe algo que es confuso simplemente? Desplazarse hacia arriba y haga clic en **Editar en GitHub** para realizar cambios: los vaya a nosotros para revisión y, a continuación, una vez se aprobar en ellas, verá los cambios y mejoras aquí.