<properties
    pageTitle="Lista de cuentas de almacenamiento de Azure"
    description="Administrar la configuración de la cuenta de almacenamiento con el Kit de herramientas de Azure para Eclipse"
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->

# <a name="azure-storage-account-list"></a>Lista de cuentas de almacenamiento de Azure #

Cuentas de Azure almacenamiento habilitar ubicaciones de descarga que se utilizará para el JDK, el servidor de aplicaciones y los componentes arbitrarios, así como para almacenar el estado cuando se usa el almacenamiento en caché. Eclipse mantiene una lista de cuentas de almacenamiento conocidos que están disponibles para los proyectos en el área de trabajo de Eclipse. Para abrir el cuadro de diálogo **Cuentas de almacenamiento** , que se usa para administrar esa lista en Eclipse, haga clic en **ventana**, haga clic en **Preferencias**y, a continuación, expanda **Azure**y, a continuación, haga clic en **Cuentas de almacenamiento**.

La siguiente imagen muestra el cuadro de diálogo **Cuentas de almacenamiento** .

![][ic719496]

También se puede abrir este cuadro de diálogo desde un vínculo de **cuentas** en los cuadros de diálogo que utilizan cuentas de almacenamiento, como las siguientes:

* La ficha **JDK** del cuadro de diálogo **Configuración del servidor** .
* La ficha **servidor** del cuadro de diálogo **Configuración del servidor** .
* El cuadro de diálogo **Agregar componente** .
* El cuadro de diálogo de propiedades de **almacenamiento en caché** .

## <a name="to-import-your-storage-accounts-using-a-publish-settings-file"></a>Para importar sus cuentas de almacenamiento con un archivo de configuración de publicación ##

1. En el cuadro de diálogo **Cuentas de almacenamiento** , haga clic en **Importar desde un archivo de configuración de publicación**.
2. (Omitir este paso si ya ha guardado un archivo de configuración de publicación en el equipo local). En el cuadro de diálogo **Información de la suscripción de importación** , haga clic en **Descargar el archivo de configuración de publicación**. Si todavía no ha iniciado sesión en su cuenta de Azure, le pedirá que inicie sesión en. A continuación, le pedirá que guarde un Azure publicar el archivo de configuración. (Puede pasar por alto las instrucciones resultantes que se muestra en las páginas de inicio de sesión - se proporcionan en el portal de Azure y destinados a los usuarios de Visual Studio). Para guardarlo en el equipo local.
3. Aún en el cuadro de diálogo **Información de la suscripción de importación** , haga clic en el botón **Examinar** , seleccione el archivo de configuración de publicación que ha guardado localmente anteriormente y, a continuación, haga clic en **Abrir**.
4. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Importar información de la suscripción** .

## <a name="to-create-a-new-storage-account"></a>Para crear una nueva cuenta de almacenamiento ##

1. En el cuadro de diálogo **Cuentas de almacenamiento** , haga clic en **Agregar**.
2. En el cuadro de diálogo **Agregar una cuenta de almacenamiento** , haga clic en **nuevo**.
3. En el cuadro de diálogo **Nueva cuenta de almacenamiento** , especifique valores para lo siguiente:
    * Nombre de la cuenta de almacenamiento.
    * Ubicación de la cuenta de almacenamiento.
    * Descripción de la cuenta de almacenamiento.
    * La suscripción a la que pertenece la cuenta de almacenamiento.
4. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Nueva cuenta de almacenamiento** .

Puede tardar varios minutos para crear su cuenta de almacenamiento. Después de crear, haga clic en **Aceptar** para cerrar el cuadro de diálogo **Agregar una cuenta de almacenamiento** , y se agregará la nueva cuenta de almacenamiento a la lista de cuentas de almacenamiento disponible.

## <a name="to-add-an-existing-storage-account-to-the-list"></a>Para agregar una cuenta existente de almacenamiento a la lista ##

1. Si no dispone de una cuenta de almacenamiento de Azure, crear una siguiendo los pasos indicados en la lista **para crear una nueva sección de cuenta de almacenamiento** por encima. (Como alternativa, puede crear una nueva cuenta de almacenamiento en el [Portal de administración de Azure][].)
2. En el cuadro de diálogo **Cuentas de almacenamiento** , haga clic en **Agregar**.
3. En el cuadro de diálogo **Agregar una cuenta de almacenamiento** , escriba valores de **nombre** y una **Tecla de acceso**. La clave de nombre y el acceso de la cuenta debe ser una cuenta de almacenamiento de Azure existente. Utilice la sección de **almacenamiento** del [Portal de administración de Azure][] para ver los nombres de cuenta de almacenamiento y las teclas. El cuadro de diálogo **Agregar una cuenta de almacenamiento** tendrá un aspecto similar al siguiente.

    ![][ic719497]

4. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Agregar una cuenta de almacenamiento** .

## <a name="to-modify-a-storage-account-to-use-a-new-access-key"></a>Para modificar una cuenta de almacenamiento para usar una nueva clave de acceso ##

1. En el cuadro de diálogo **Cuentas de almacenamiento** , haga clic en la cuenta de almacenamiento que desea editar y, a continuación, haga clic en **Editar**.
2. En el cuadro de diálogo **Editar la tecla de acceso de la cuenta de almacenamiento** , modifique el valor de la **Tecla de acceso** .
3. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de **Editar la tecla de acceso de la cuenta de almacenamiento** .

## <a name="to-remove-a-storage-account-from-the-list-maintained-in-eclipse"></a>Para quitar una cuenta de almacenamiento de la lista que se mantienen en Eclipse ##

1. En el cuadro de diálogo **Cuentas de almacenamiento** , haga clic en la cuenta de almacenamiento que desea editar y, a continuación, haga clic en **Quitar**.
2. Haga clic en **Aceptar** cuando se le solicite para quitar la cuenta de almacenamiento.

>[AZURE.NOTE] Solo quitar la cuenta de almacenamiento mediante el cuadro de diálogo **Cuentas de almacenamiento** quita de la lista de cuentas de almacenamiento pueden visualizar en Eclipse. No se quita la cuenta de almacenamiento de su suscripción de Azure. Además, la cuenta de almacenamiento podría aparecer nuevamente en la lista después Eclipse vuelve a cargar los detalles de la suscripción.

## <a name="see-also"></a>Vea también ##

[Kit de herramientas de Azure para Eclipse][]

[Instalar el Kit de herramientas de Azure Eclipse][] 

[Crear una aplicación del mundo Hola para Azure en Eclipse][]

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Portal de administración de Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Crear una aplicación del mundo Hola para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalar el Kit de herramientas de Azure Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png
