<properties
    pageTitle="Cómo crear una colección de híbrido para RemoteApp de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo crear una implementación de RemoteApp que se conecta a la red interna."
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

# <a name="how-to-create-a-hybrid-collection-for-azure-remoteapp"></a>Cómo crear una colección de híbrido para RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Existen dos tipos de colecciones de RemoteApp de Azure:

- Nube: reside totalmente en Azure. Puede guardar todos los datos en la nube (para una colección de nube) o para conectarse a la colección de un VNET y guardar datos.   
- Híbrido: incluye una red virtual para el acceso local - Esto requiere el uso de Azure AD y un entorno de Active Directory local.

¿No sabe que necesita? Consulte [¿qué tipo de colección necesita para RemoteApp de Azure](remoteapp-collections.md).

Este tutorial le guiará a través del proceso de creación de una colección de híbrido. Hay ocho pasos:

1.  Decidir qué [imagen](remoteapp-imageoptions.md) que desea usar para la colección. Puede crear una imagen personalizada o usar una de las imágenes de Microsoft que se incluye con la suscripción.
2. Configurar una red virtual. Consulte la información de [planificación VNET](remoteapp-planvnet.md) y [tamaño](remoteapp-vnetsizing.md) .
2.  Crear una colección.
2.  Unirse a la colección de su dominio local.
3.  Agregar una imagen de la plantilla a la colección.
4.  Configurar la sincronización de directorios. RemoteApp de Azure requiere que integre con Azure Active Directory por cualquier 1) configurar Azure Active Directory Sync con la opción de sincronización de la contraseña o (2) configuración Azure Active Directory Sync sin la opción de sincronización de la contraseña, pero usa un dominio federado para AD FS. Consulte la [información de configuración de Active Directory con RemoteApp](remoteapp-ad.md).
5.  Publicar aplicaciones de RemoteApp.
6.  Configurar el acceso de usuario.

**Antes de empezar**

Debe hacer lo siguiente antes de crear la colección:

- [Registrarse](https://azure.microsoft.com/services/remoteapp/) para RemoteApp de Azure.
- Crear una cuenta de usuario en Active Directory para usar como la cuenta de servicio de RemoteApp de Azure. Restringir los permisos para esta cuenta de manera que solo puede unirse a equipos al dominio.
- Recopilar información acerca de la red local: información y detalles del dispositivo VPN la dirección IP.
- Instale el módulo de [PowerShell de Azure](../powershell-install-configure.md) .
- Recopilar información acerca de los usuarios que desea conceder acceso a. Necesitará el nombre principal de usuario de Azure Active Directory (por ejemplo, name@contoso.com) para cada usuario. Asegúrese de que coincida con el UPN entre Azure AD y Active Directory.
- Elija la imagen de la plantilla. Una imagen de la plantilla de Azure RemoteApp contiene las aplicaciones y los programas que desea publicar para los usuarios. Para obtener más información, consulte [Opciones de imagen de RemoteApp de Azure](remoteapp-imageoptions.md) .
- ¿Desea usar la imagen de Office 365 ProPlus? Consulte información [aquí](remoteapp-officesubscription.md).
- [Configurar Active Directory para RemoteApp](remoteapp-ad.md).



## <a name="step-1-set-up-your-virtual-network"></a>Paso 1: Configurar una red virtual
Puede implementar una colección de híbrido que utiliza una red virtual Azure existente, o puede crear una nueva red virtual. Una red virtual le permite a los datos de access de los usuarios de su red local a través de los recursos remotos de RemoteApp. Utiliza una red virtual Azure le da el acceso de red directa de la colección a otros servicios de Azure y máquinas virtuales implementados a la red virtual.

Asegúrese de que revisar la información de [planificación de VNET](remoteapp-planvnet.md) y el [tamaño VNET](remoteapp-vnetsizing.md) antes de crear su VNET.

### <a name="create-an-azure-vnet-and-join-it-to-your-active-directory-deployment"></a>Crear un VNET de Azure y unirse a la implementación de Active Directory

Iniciar mediante la creación de una [red virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). Para ello, en la pestaña de la **red** en el portal de Azure. Debe conectarse a su red virtual de la implementación de Active Directory que se sincroniza en el inquilino de Azure Active Directory.

Para obtener más información, vea [crear una red virtual con el portal de Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) .

### <a name="make-sure-your-virtual-network-is-ready-for-azure-remoteapp"></a>Asegúrese de que su red virtual está listo para RemoteApp de Azure
Antes de crear la colección, ¡asegúrese de que la nueva red virtual está lista. Se pueden validar haciendo lo siguiente:

1. Crear una máquina virtual Azure dentro de la subred de la red virtual que acaba de crear para RemoteApp.
2. Usar Escritorio remoto para conectarse a la máquina virtual. (Haga clic en **Conectar**.)
3. Unirse a para la misma implementación de Active Directory que desea usar para RemoteApp.

¿Trabajó? ¡La red virtual y subred están listos para Azure RemoteApp!

Puede encontrar más información sobre cómo crear máquinas virtuales de Windows Azure y conectarse a ellas con Escritorio remoto [aquí](https://msdn.microsoft.com/library/azure/jj156003.aspx).

## <a name="step-2-create-an-azure-remoteapp-collection"></a>Paso 2: Crear una colección de RemoteApp de Azure ##



1. En el [portal de Azure](http://manage.windowsazure.com), vaya a la página de RemoteApp de Azure.
2. Haga clic en **Nueva > crear con VNET**.
3. Escriba un nombre para la colección.
4. Elegir el plan que desea usar - estándar o básica.
5. Elija su VNET de la lista desplegable de la lista y, a continuación, en la subred.
6. Elija esta opción para unirse a su dominio.
5. Haga clic en **crear RemoteApp colección**.

Después de crear la colección de RemoteApp de Azure, haga doble clic en el nombre de la colección. Que se muestre la página de **Inicio rápido** : este es donde haya terminado de configurar la colección.

¿Algo fallar? Consulte la [información de solución de problemas de colección de híbrido](remoteapp-hybridtrouble.md).

## <a name="step-3-link-your-collection-to-the-local-domain"></a>Paso 3: Establecer el vínculo de la colección al dominio local ##


1. En la página de **Inicio rápido** , haga clic en **unirse a un dominio local**.
2. Agregar la cuenta de servicio de Azure RemoteApp a su dominio de Active Directory local. Necesitará el nombre de dominio, la unidad organizativa, el nombre de usuario de cuenta de servicio y la contraseña.

    Esta es la información recopilada si ha seguido los pasos de [Configurar Active Directory para RemoteApp de Azure](remoteapp-ad.md).


## <a name="step-4-link-to-an-azure-remoteapp-image"></a>Paso 4: Vínculo a una imagen de RemoteApp de Azure ##

Una imagen de la plantilla de Azure RemoteApp contiene los programas que desea compartir con los usuarios. Puede crear una nueva [imagen de la plantilla](remoteapp-imageoptions.md) o establecer un vínculo a una imagen existente (uno ya importados o cargado en Azure RemoteApp). También puede vincular a una de las [imágenes de plantilla](remoteapp-images.md) de RemoteApp de Azure que contienen Office 365 o programas de Office 2013 (para uso prueba).

Si va a cargar la nueva imagen, debe escribir el nombre y elija la ubicación de la imagen. En la siguiente página del asistente, verá un conjunto de cmdlets de PowerShell: copiar y ejecutar estos cmdlets desde el símbolo de Windows PowerShell elevado para cargar la imagen especificada.

Si se vincula a una imagen de plantilla existente, solo tiene que especificar el nombre de la imagen, la ubicación y la suscripción de Azure asociada.



## <a name="step-5-configure-active-directory-directory-synchronization"></a>Paso 5: Configurar la sincronización de directorios de Active Directory ##

RemoteApp de Azure requiere que integre con Azure Active Directory por cualquier 1) configurar Azure Active Directory Sync con la opción de sincronización de la contraseña o (2) configuración Azure Active Directory Sync sin la opción de sincronización de la contraseña, pero usa un dominio federado para AD FS.

Desproteger [AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) : este artículo le ayudará a configurar la integración de directorios de 4 pasos.

Para planear información y conocer los pasos detallados, vea [Guía de sincronización de directorios](http://msdn.microsoft.com//library/azure/hh967642.aspx) .

## <a name="step-6-publish-apps"></a>Paso 6: Publicar aplicaciones ##

Una aplicación de Azure RemoteApp es la aplicación o el programa que desea proporcionar a los usuarios. Se encuentra en la imagen de plantilla que ha cargado para la colección. Cuando un usuario tiene acceso a una aplicación, parece que se ejecutan en su entorno local, pero realmente se ejecuta en Azure.

Antes de que los usuarios pueden obtener acceso a aplicaciones, debe publicarlos: permite los usuarios obtener acceso a las aplicaciones a través del cliente de escritorio remoto.

Puede publicar varias aplicaciones a la colección. Desde la página de publicación, haga clic en **Publicar** para agregar una aplicación. O bien puede publicar en el menú **Inicio** de la imagen de la plantilla o especificando la ruta de acceso en la imagen de plantilla para la aplicación. Si elige agregar en el menú **Inicio** , elija el programa para agregar. Si decide incluir la ruta de acceso a la aplicación, proporcione un nombre para la aplicación y la ruta de acceso donde está instalado en la imagen de la plantilla.

## <a name="step-7-configure-user-access"></a>Paso 7: Configurar el acceso de usuario ##

Ahora que ha creado la colección, debe agregar los usuarios que desea que puedan usar los recursos remotos. Los usuarios que proporcionan acceso a deben existir en el inquilino de Active Directory asociado con la suscripción se utiliza para crear esta colección de RemoteApp de Azure.

1.  Desde la página de inicio rápido, haga clic en **Configurar el acceso de usuarios**.
2.  Escriba la cuenta de trabajo (de Active Directory) o la cuenta de Microsoft que desea conceder acceso.

    **Notas:**

    Asegúrese de que usa el “user@domain.com” formato.

    Si está utilizando Office 365 ProPlus en la colección, debe utilizar las identidades de Active Directory para los usuarios. Esto le permite validar licencias.


3.  Una vez que los usuarios se validarán, haga clic en **Guardar**.


## <a name="next-steps"></a>Pasos siguientes ##
Eso es todo: se ha creado e implementado la colección de híbrido RemoteApp de Azure. El siguiente paso es hacer que los usuarios descargar e instalar al cliente de escritorio remoto. Puede encontrar la dirección URL para el cliente en la página de inicio rápido de Azure RemoteApp. A continuación, que inicie la sesión en el cliente de los usuarios y tener acceso a las aplicaciones que ha publicado.



### <a name="help-us-help-you"></a>Ayúdenos a ayudarle
¿Sabía que, además de valoración de este artículo y realizar comentarios hacia abajo, a continuación, puede realizar cambios en el artículo? ¿Falta algo? ¿Algún error? ¿Escribir algo que es simplemente confuso? Desplazarse hacia arriba y haga clic en **Editar en GitHub** para realizar cambios: los vaya a nosotros para revisión y, a continuación, una vez se aprobar en ellas, verá los cambios y mejoras aquí.
