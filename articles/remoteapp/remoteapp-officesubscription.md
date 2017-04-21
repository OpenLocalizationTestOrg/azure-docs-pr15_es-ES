
<properties 
    pageTitle="Cómo utilizar la suscripción de Office 365 con RemoteApp de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo puede usar su suscripción de Office 365 en Azure RemoteApp compartir aplicaciones de Office."
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-your-office-365-subscription-with-azure-remoteapp"></a>Cómo utilizar la suscripción de Office 365 con RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

¿Sabía que puede usar su suscripción de Office 365 existente en Azure RemoteApp compartir aplicaciones de Office desde la nube? Siga leyendo para obtener información sobre su Office 365 + opciones RemoteApp Azure, incluidos vínculos a artículos sobre Office 365 que le ayudarán a aprovechar al máximo su suscripción.

## <a name="how-do-i-use-office-365-accounts-for-azure-remoteapp"></a>¿Cómo se puede usar cuentas de Office 365 para RemoteApp de Azure?
Consulte el artículo nuevo de Peter para toda la información: [cómo usar RemoteApp de Azure con cuentas de usuario de Office 365](remoteapp-o365user.md)

## <a name="can-i-use-my-office-365-subscription-to-run-office-applications-in-azure-remoteapp"></a>¿Puedo usar mi suscripción de Office 365 para ejecutar aplicaciones de Office en RemoteApp de Azure?

¡Sí! De hecho, con su suscripción de Office 365 es la única manera de poner las aplicaciones de Office a RemoteApp de Azure.

(Nota: si la implementación de Azure RemoteApp se entrega por un socio de hospedaje, es posible puedan proporcionarle basados en un [Contrato de licencia de proveedor de servicio](http://www.microsoft.com/en-us/Licensing/licensing-programs/spla-program.aspx)de licencias de Office)


Lo mejor de su suscripción de Office 365 es que le permite usar la misma licencia de usuario en muchas plataformas y diferentes entornos, incluyendo la nube de Azure. Al usar aplicaciones de Office en Azure RemoteApp no necesita adquirir licencias adicionales o configurar las licencias existentes de ninguna forma especial. Todo lo que necesita es una suscripción de Office 365 que incluye [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx).

Office 365 ProPlus habilita la [activación de equipo compartido](https://technet.microsoft.com/library/Dn782860.aspx) : esta característica permite a activación temporal en función de usuario de Office en virtual y entornos de nube como RemoteApp de Azure (y servicios de escritorio remoto).

¿Qué planes de Office 365 incluyen Office 365 ProPlus? Consulte la tabla [disponibilidad de servicio dentro de cada plan](https://technet.microsoft.com/library/office-365-plan-options.aspx) . Tenga en cuenta que no todos los planes incluyen Office 365 ProPlus (por ejemplo, el plan de Office 365 empresa). Si su plan no es así, considere la posibilidad de actualizar a un plan que hace (por ejemplo, Office 365 educación E3).

## <a name="ok-so-how-are-my-office-365-proplus-licenses-used-with-azure-remoteapp"></a>Bien, ¿es Mi Office 365 licencias ProPlus usadas con RemoteApp de Azure?

Cada licencia de usuario para Office 365 le permite un único usuario activar aplicaciones de Office en un máximo de 5 equipos plus tabletas y teléfonos. Cada activación está registrado con el usuario hasta que desactivar Office en el dispositivo. (Los usuarios pueden administrar sus dispositivos en el [portal de Office 365](https://portal.office365.com/)).

Con RemoteApp de Azure un único usuario puede iniciar sesión en varios equipos en el mismo día sin darse cuenta. Eso es porque el servicio automáticamente administra y escalas de recursos en la nube, mientras ve el usuario solo las aplicaciones y los programas que ha compartido. Para este escenario de Office 365 ProPlus ofrece un modo de activación de equipo compartido, esto significa que el usuario no tiene que hacer cualquier administración de licencias para obtener acceso a los recursos y que los equipos individuales no cuentan para el límite de activación de 5 equipo.

Tan (el administrador) asigna licencias de Office 365 ProPlus a los usuarios, pueden usar Office en sus dispositivos personales, así como a través de la colección de RemoteApp de Azure.

## <a name="which-office-applications-can-i-use-with-office-365-and-azure-remoteapp"></a>¿Qué aplicaciones de Office ¿usar con Office 365 y Azure RemoteApp?

Puede usar su suscripción de Office 365 para activar y compartir Office 2013 en implementaciones de RemoteApp de Azure. Actualmente se no admite el uso de otras versiones de Office con RemoteApp de Azure. Esto incluye Office 2003, Office 2007, Office 2010 y Office 2016.

## <a name="what-about-visio-pro-or-project-pro"></a>¿Qué sucede con Visio Pro o Project Pro?

La imagen de Office 365 ProPlus incluida en la suscripción de RemoteApp incluye Visio Pro y Project Pro. Pero no puede usar su suscripción de Office 365 ProPlus a activar los programas: cada uno de ellos tiene su propia licencia. Se puede activar en el [portal de Office 365](https://portal.office365.com/). 

No tiene licencia para estos programas si no desea usarlos. Activar solo los programas que desea usar - y omitir las demás. Podrá verlos en la imagen, pero no se pueden utilizar. 

## <a name="how-do-i-get-started-with-office-365-and-azure-remoteapp"></a>¿Cómo empezar con Office 365 y Azure RemoteApp?

Ahora que conoce los detalles de las licencias de Office 365, vamos a empezar a usarlo en Azure RemoteApp: es muy fácil:

Cuando se crea la colección de RemoteApp de Azure, usar la imagen de **Office 365 ProPlus (requerido suscripción)** .

![Imagen de RemoteApp Azure con Office 365 Pro Plus](./media/remoteapp-officesubscription/remoteapp-officeimage.png)


Esta imagen contiene la última versión de Windows Server y Office 365 ProPlus. Después de configurar la colección (incluyendo aplicaciones publicación), los usuarios simplemente inicie sesión en Azure RemoteApp (mediante su cliente de RemoteApp) y proporcionar sus credenciales de Office 365 para las aplicaciones de Office. Licencias se envían automáticamente sin ningún conjunto hacia arriba o requiere la administración.

## <a name="can-i-create-a-custom-image-with-office-365-proplus"></a>¿Puedo crear una imagen personalizada con Office 365 ProPlus?

Puede crear una imagen personalizada para la colección que contiene Office 365 ProPlus. Existen 2 opciones: usar la imagen de la Galería de Azure que le proporcionamos o puede crear su propia imagen personalizada e instalar Office 365 ProPlus allí.

### <a name="use-the-azure-gallery-image"></a>Usar la imagen de la Galería de Azure

La manera más sencilla de implementar Office 365 ProPlus a una colección es [empezar con una de las imágenes de la Galería de Azure](remoteapp-image-on-azurevm.md) incluidos en la suscripción de RemoteApp de Azure. Asegúrese de que elegir la imagen de **Windows Server escritorio remoto con Office 365 ProPlus preinstalada** . A continuación, instalar cualquier otras aplicaciones que desee en la imagen y, a continuación, ya está listo ir.

### <a name="use-a-custom-image"></a>Usar una imagen personalizada

Siempre puede crear una imagen personalizada: puede crear una [Máquina virtual de Azure](remoteapp-image-on-azurevm.md) o [Cree la imagen localmente](remoteapp-create-custom-image.md) y cárguelo en Azure. En cualquier caso, asegúrese de que instalar Office 365 ProPlus utilizando el nodo de activación de equipo compartido. Use la [Herramienta de implementación de Office](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx) y siga las [instrucciones](https://technet.microsoft.com/library/Dn782858.aspx) para la instalación.  

### <a name="disable-automatic-updates-for-office-365-proplus-in-your-custom-image---important"></a>Deshabilitar actualizaciones automáticas para Office 365 en su imagen personalizada - importante

La imagen personalizada se usa por RemoteApp de Azure como una plantilla para agregar recursos adicionales como la demanda de los usuarios aumenta. Para evitar retrasos y problemas de conexión, deshabilitar actualizaciones automáticas de Office en la imagen. Si no lo hace, todos los recursos creados con esa plantilla se actualización automáticamente cuando se inicia. En su lugar, use el proceso de Azure RemoteApp estándar para actualizar la imagen personalizada. Tenga cuidado de esa forma actualizar las aplicaciones de Office una vez en la imagen de la plantilla y, a continuación, le permiten Azure RemoteApp de obtener las actualizaciones a los usuarios.

Para deshabilitar las actualizaciones automáticas, agregue lo siguiente al archivo de configuración de la herramienta de implementación de Office:

        <Updates Enabled="FALSE" />

Ahora el archivo de configuración debe contener estas líneas:
    
        <Display Level="NONE" AcceptEULA="TRUE" />
        <Property Name="SharedComputerLicensing" Value="1" />
        <Updates Enabled="FALSE" />

## <a name="so-how-can-i-update-an-image-with-office-365-proplus"></a>¿Cómo puedo actualizar una imagen con Office 365 ProPlus?

Hay muchas razones para actualizar la imagen de la colección. Estas son unos pocos:

- Obtener las últimas actualizaciones de Windows 
- Obtener las últimas actualizaciones de la aplicación Office 365 ProPlus
- Actualizar la aplicación personalizada
- Cambiar otras opciones de configuración para la propia imagen

Para ver los pasos de llevar a cabo para actualizar la colección para usar la imagen que haya actualizado, vaya [aquí](remoteapp-update.md). Pero, para obtener información sobre cómo actualizar la imagen y Office 365, consulte la siguiente información.

Tiene dos opciones para actualizar su imagen, reemplazar la imagen con una nueva completamente o manualmente actualizar la imagen existente.

### <a name="replace-your-image-with-the-latest-azure-gallery-image--add-customizations"></a>Reemplazar la imagen con la última imagen de la Galería de Azure + agregar personalizaciones
Con esta opción, se le permiten Microsoft ocuparse de las actualizaciones de Windows Server y Office 365 ProPlus. En lugar de actualizar la imagen existente, debe crear una imagen completamente nueva basada en la última imagen de la galería. A continuación, repita los pasos que realizó antes para personalizar la imagen: instalar aplicaciones personalizadas, modifique la configuración de la imagen, etcetera.

La Galería de imágenes se actualiza periódicamente, por lo puede estar fácil saber que las aplicaciones de Windows Server y Office 365 ProPlus están actualizadas. Por supuesto, el equilibrio es que tendrá que aplicar las personalizaciones cada vez que reciba una nueva imagen. Puede crear secuencias de comandos para automatizar la configuración de las personalizaciones.

### <a name="manually-update-your-existing-image"></a>Actualizar manualmente la imagen existente

Con esta opción, se usan las herramientas estándar de Windows para aplicar actualizaciones a la imagen. Para Office 365, use la herramienta de implementación de Office para descargar e instalar las últimas actualizaciones o versiones de Office 365 ProPlus.

> [AZURE.IMPORTANT] Recuerde: deshabilitar las actualizaciones automáticas de Office 365 ProPlus.

¿Necesita más información sobre el uso de la herramienta de implementación de Office actualizaciones?

- [Implementar haga clic en ejecutar para productos de Office 365 mediante la herramienta de implementación de Office](https://technet.microsoft.com/library/JJ219423.aspx)
- [Implementación y actualización de Office 365 ProPlus mediante la herramienta de implementación de Office](https://channel9.msdn.com/Events/Ignite/2015/BRK3168) (vídeo)
- [Configurar las opciones de actualización para Office 365](https://technet.microsoft.com/library/dn761708.aspx)
