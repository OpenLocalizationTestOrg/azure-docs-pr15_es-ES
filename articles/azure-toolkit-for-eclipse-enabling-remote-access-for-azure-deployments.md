<properties
    pageTitle="Habilitar el acceso remoto para las implementaciones de Azure en Eclipse"
    description="Obtenga información sobre cómo habilitar el acceso remoto para implementaciones Azure con el Kit de herramientas de Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->

# <a name="enabling-remote-access-for-azure-deployments-in-eclipse"></a>Habilitar el acceso remoto para las implementaciones de Azure en Eclipse

Para ayudar a solucionar problemas de las instalaciones, puede habilitar y usar acceso remoto para conectarse a la máquina virtual de la implementación de hospedaje. La funcionalidad de acceso remoto se basa en el protocolo de escritorio remoto (RDP). Puede configurar el acceso remoto para su implementación después de que haya publicado en Azure, o si está utilizando Eclipse con un sistema operativo Windows, puede configurar el acceso remoto antes de publicar en Azure. Tenga en cuenta que necesitará a un cliente de escritorio remoto que sea compatible con el sistema operativo para conectarse a la máquina virtual de la implementación de Azure.

## <a name="how-to-enable-remote-access-before-you-deploy-to-azure"></a>Cómo habilitar el acceso remoto antes de implementar en Azure

> [AZURE.NOTE] Para habilitar el acceso remoto antes de implementar la aplicación en Azure, debe estar ejecutándose Eclipse en Windows.

La imagen siguiente muestra las propiedades de **Acceso remoto al** cuadro de diálogo que se utiliza para habilitar el acceso remoto.

![][ic719494]

Hay dos formas de mostrar el cuadro de diálogo de propiedades de **Acceso remoto** :

* Haga clic en el vínculo **Opciones avanzadas** en la sección **Acceso remoto** del cuadro de diálogo **publicar en Azure** .
* Abra el cuadro de diálogo **Propiedades** del proyecto Azure.

Cuando se crea un nuevo proyecto de implementación de Azure, el proyecto no tendrá acceso remoto habilitado de forma predeterminada. Sin embargo, puede habilitar fácilmente acceso remoto especificando el nombre de usuario y contraseña en el cuadro de diálogo **publicar en Azure** . La contraseña de acceso remoto está cifrada con certificados X.509. Si no utiliza proporcionar su propio certificado, el cifrado se basa en un certificado autofirmado suministrado con el complemento de Azure para Eclipse. Este certificado autofirmado se encuentra en la carpeta de **certificados** de su proyecto Azure, almacenado como un archivo de certificado público (SampleRemoteAccessPublic.cer) y como un archivo de certificado de intercambio de información Personal (PFX) (SampleRemoteAccessPrivate.pfx). La segunda sección contiene la clave privada para el certificado y tiene una contraseña de forma predeterminada, **Contraseña1**. Sin embargo, dado que esta contraseña es de dominio público, se recomienda el certificado predeterminado solo para aprender, no para una implementación de producción. Tan distinto de con fines de aprendizaje, cuando desee habilitado sesiones remotas para las implementaciones, se debe hacer clic en el vínculo **Avanzadas** en el cuadro de diálogo **publicar en Azure** para especificar su propio certificado. Tenga en cuenta que tendrá que cargar la versión PFX del certificado al servicio hospedado en el Portal de administración de Azure, por lo que Azure puede descifrar la contraseña del usuario.

El resto del tutorial muestra cómo habilitar el acceso remoto para un proyecto de implementación de Azure que creó con acceso remoto desactivado. Para el propósito de este tutorial, se creará un nuevo certificado autofirmado y su archivo .pfx tendrá una contraseña de su elección. También tiene la opción de uso de un certificado emitido por una entidad emisora de certificados.

## <a name="how-to-enable-remote-access-after-you-have-deployed-to-azure"></a>Cómo habilitar el acceso remoto después de que ha implementado en Azure

Para habilitar el acceso remoto después de que ha implementado en Azure, realice los siguientes pasos:

1. Inicie sesión en el portal de administración de Azure con su cuenta de Azure
1. En la lista de **Servicios en la nube**, seleccione el servicio de nube implementada
1. En la página web de servicio de nube, haga clic en el vínculo **Configurar**
1. En la parte inferior de la página Configuración, haga clic en el vínculo **remoto**
1. Cuando aparezca el cuadro de diálogo emergente:
    * Especifique el rol para el que desea habilita el acceso remoto
    * Haga clic para seleccionar la casilla de verificación **Habilitar Escritorio remoto**
    * Especifique un nombre de usuario y contraseña que desea usar para el acceso remoto
    * Seleccione el certificado para usar
1. Haga clic en **Aceptar** 

Verá un mensaje que indica que el cambio de configuración está en curso, que puede tardar unos minutos en completarse. Cuando haya completado el cambio de configuración, siga los pasos de la sección **Iniciar forma remota** más adelante en este artículo.
    
## <a name="how-to-enable-remote-access-in-your-package"></a>Cómo habilitar el acceso remoto en el paquete

1. En el panel de Eclipse Explorador de proyectos, haga clic en el proyecto de Azure y haga clic en **Propiedades**.

1. En el cuadro de diálogo **Propiedades** , expanda **Azure** en el panel izquierdo y haga clic en **Acceso remoto**.

1. En el cuadro de diálogo **Acceso remoto** , asegúrese de **que habilitar todos los roles Aceptar conexiones a Escritorio remoto con estas credenciales de inicio de sesión** está activada.

1. Especifique un nombre de usuario para la conexión a Escritorio remoto.

1. Especifique y confirme la contraseña para el usuario. Los valores de nombre y contraseña de usuario establecidos en este cuadro de diálogo se utilizará cuando realiza una conexión a Escritorio remoto. (Tenga en cuenta que se trata de una contraseña independiente de su contraseña PFX).

1. Especifique la fecha de expiración de la cuenta de usuario.

1. Haga clic en **nuevo** para crear un nuevo certificado autofirmado. (Como alternativa, puede seleccionar un certificado de su sistema de archivo o área de trabajo a través de los botones del **área de trabajo** o el **sistema de archivos** , respectivamente, pero para el propósito de este tutorial se creará un nuevo certificado.)

    * En el cuadro de diálogo **Nuevo certificado** , especifique y confirme la contraseña que usará para el archivo PFX.

    * Acepte el valor de **Nombre (CN)**, o use un nombre personalizado.

    * Especificar la ruta de acceso y nombre de archivo donde se guardará el certificado nuevo, en el formulario de .cer. Para este paso y el siguiente paso, puede usar la carpeta de **certificados** de su proyecto Azure, pero está libre para elegir otra ubicación. Para el propósito de este tutorial, usaremos **c:\mycert\mycert.cer**. (Cree la carpeta **c:\mycert** antes de continuar, o usar una carpeta existente si lo desea).

    * Especificar la ruta de acceso y nombre de archivo donde se guardará el nuevo certificado y su clave privada, en el formulario de .pfx. Para el propósito de este tutorial, usaremos **c:\mycert\mycert.pfx**. El cuadro de diálogo de **Nuevo certificado** debe ser similar al siguiente (actualizar las rutas de acceso de la carpeta si no utiliza **c:\mycert**):

        ![][ic712275]

    * Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Nuevo certificado** .

1. El cuadro de diálogo de **Acceso remoto** debe ser similar al siguiente:</p>

    ![][ic719495]

1. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de **Acceso remoto** .
    
Reconstruir la aplicación con la compilación establecer para que su implementación en la nube.

## <a name="to-log-in-remotely"></a>Para iniciar sesión forma remota

Cuando la instancia de rol está lista, puede iniciar sesión remotamente la máquina virtual que hospeda la aplicación.

* Si está utilizando Eclipse en Windows y habían seleccionado la opción de **Inicio de escritorio remoto en implementar** durante la implementación de Azure, aparecerá una pantalla de inicio de sesión de conexión a Escritorio remoto cuando se inicie la implementación. Cuando se le pida el nombre de usuario y contraseña, escriba los valores que haya especificado para el usuario remoto y se puede iniciar la sesión.

* Otra forma de iniciar sesión remotamente es a través del <a href="http://go.microsoft.com/fwlink/?LinkID=512959">Portal de administración de Azure</a>:

    * En la vista de **Los servicios de nube** del portal de administración de Azure, haga clic en el servicio de nube, haga clic en **instancias**y, a continuación, haga clic en una instancia específica y, a continuación, haga clic en el botón **Conectar** . El botón **Conectar** aparece como la siguiente en la barra de comandos:

        ![][ic659273]

    * Después de hacer clic en el botón **Conectar** , se le pedirá que abre un archivo RDP. Abra el archivo y siga las indicaciones. (Se podría también guardar este archivo en su equipo local y, a continuación, ejecute el archivo haciendo doble clic en registro de remoto a su equipo virtual sin necesidad de ir primero el portal de administración).

    * Cuando se le pida el nombre de usuario y contraseña, escriba los valores que haya especificado para el usuario remoto y se puede iniciar la sesión.

> [AZURE.NOTE] Si se encuentra en un sistema operativo, debe usar a un cliente de escritorio remoto que sea compatible con el sistema operativo y siga los pasos para configurar a que los clientes con la configuración en el archivo RDP que ha descargado.

## <a name="see-also"></a>Vea también

[Kit de herramientas de Azure para Eclipse][]

[Crear una aplicación del mundo Hola para Azure en Eclipse][]

[Instalar el Kit de herramientas de Azure Eclipse][] 

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Crear una aplicación del mundo Hola para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalar el Kit de herramientas de Azure Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png
