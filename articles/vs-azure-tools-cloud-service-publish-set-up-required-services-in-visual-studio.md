<properties
   pageTitle="Prepararse para publicar o implemente una aplicación de Visual Studio Azure | Microsoft Azure"
   description="Obtenga información sobre los procedimientos para configurar los servicios de cuenta de almacenamiento y la nube y configure la aplicación de Azure."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="prepare-to-publish-or-deploy-an-azure-application-from-visual-studio"></a>Prepararse para publicar o implemente una aplicación de Azure desde Visual Studio

## <a name="overview"></a>Información general

Antes de publicar un proyecto de servicio de nube, debe configurar los siguientes servicios:

- Un **servicio de nube** para ejecutar las funciones en el entorno de Azure

- Una **cuenta de almacenamiento** que proporciona acceso a los servicios de blobs de Windows, la cola y la tabla.

Use los siguientes procedimientos para configurar estos servicios y configurar la aplicación


## <a name="create-a-cloud-service"></a>Crear un servicio de nube

Para publicar un servicio de nube en Azure, primero debe crear un servicio de nube, que ejecuta los roles en el entorno de Azure. Puede crear un servicio de nube en el [portal de clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), como se describe en la sección **para crear un servicio de nube mediante el portal de clásico Azure**, más adelante en este tema. También puede crear un servicio de nube en Visual Studio utilizando el Asistente para la publicación.

### <a name="to-create-a-cloud-service-by-using-visual-studio"></a>Para crear un servicio de nube mediante Visual Studio

1. Abrir el menú contextual para el proyecto de Azure y elija **Publicar**.

    ![VST_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)

1. Si aún no ha iniciado sesión, inicie sesión con su nombre de usuario y contraseña para la cuenta de Microsoft o profesional que está asociada a su suscripción de Azure.

1. Elija el botón **siguiente** para avanzar a la página de **configuración** .

    ![Configuración común del Asistente para publicación](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)

1. En la lista de **Servicios en la nube** , elija **Crear nuevo**. Aparece el cuadro de diálogo **Crear servicios de Azure** .

1. Escriba el nombre de su servicio de nube. El nombre de la forma parte de la dirección URL del servicio y, por tanto, debe ser único global. El nombre no distingue mayúsculas de minúsculas.

### <a name="to-create-a-cloud-service-by-using-the-azure-classic-portal"></a>Para crear un servicio de nube mediante el portal de clásico de Azure

1. Inicie sesión en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkId=253103) en el sitio Web de Microsoft.

1. (opcional) Para mostrar una lista de servicios de nube que ya ha creado, elija el vínculo de servicios en la nube en el lado izquierdo de la página.

1. Elija la **+** icono en la esquina inferior izquierda de esquina y, a continuación, elija **Servicio de nube** en el menú que aparece. Otra pantalla con dos opciones: **Crear rápida** y **Crear personalizado**, aparece. Si elige **Crear rápido**, puede crear un servicio de nube especificando su dirección URL y la región donde se se hospeda física. Si elige **Crear personalizado**, puede publicar un servicio de nube inmediatamente especificando un paquete (archivo .cspkg), un archivo de configuración (.cscfg) y un certificado. Crear personalizado no es necesario si desea publicar su servicio de nube mediante el comando **Publicar** en un proyecto de Azure. El comando **Publicar** está disponible en el menú contextual para un proyecto de Azure.

1. Elija **Crear rápido** más adelante publicar su servicio de nube mediante Visual Studio.

1. Especifique un nombre para el servicio de nube. La dirección URL completa aparece junto al nombre.

1. En la lista, seleccione la región donde se encuentran la mayoría de los usuarios.

1. En la parte inferior de la ventana, seleccione el vínculo del **Servicio de nube de crear** .

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Una cuenta de almacenamiento proporciona acceso a los servicios de blobs de Windows, la cola y la tabla. Puede crear una cuenta de almacenamiento mediante Visual Studio o el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkId=253103).

### <a name="to-create-a-storage-account-by-using-visual-studio"></a>Para crear una cuenta de almacenamiento mediante Visual Studio

1. En el **Explorador de soluciones**, abra el menú contextual para el nodo de **almacenamiento** y, a continuación, elija **Crear cuenta de almacenamiento**.

    ![Crear una nueva cuenta de almacenamiento de Azure](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)

1. Seleccione o escriba la siguiente información para la nueva cuenta de almacenamiento en el cuadro de diálogo **Crear cuenta de almacenamiento** .
    - La suscripción de Azure al que desea agregar la cuenta de almacenamiento.
    - El nombre que desea usar para la nueva cuenta de almacenamiento.
    - La región o el grupo de afinidad (como oeste de Estados Unidos o Asia oriental).
    - El tipo de replicación que desea usar para la cuenta de almacenamiento, como Geo redundantes.

1. Cuando haya terminado, elija **crear**. La nueva cuenta de almacenamiento aparece en la lista de **almacenamiento** en el **Explorador de servidores**.

### <a name="to-create-a-storage-account-by-using-the-azure-classic-portal"></a>Para crear una cuenta de almacenamiento a través del portal de clásico de Azure

1. Inicie sesión en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkId=253103) en el sitio Web de Microsoft.

1. (Opcional) Para ver sus cuentas de almacenamiento, seleccione el vínculo de **almacenamiento** en el panel en el lado izquierdo de la página.

1. En la esquina inferior izquierda de la página, elija la **+** icono.

1. En el menú que aparece, elija el **almacenamiento**y, a continuación, elija **Crear rápido**.

1. Asigne un nombre que se convierta en una dirección url única a la cuenta de almacenamiento.

1. Asigne un nombre a su servicio de nube. La dirección URL completa aparece junto al nombre.

1. En la lista de regiones, elija una región donde se encuentran la mayoría de los usuarios.

1. Especifique si desea habilitar la replicación de geo. Si habilita la replicación geo, se guardan los datos en varias ubicaciones físicas para reducir la posibilidad de pérdida. Esta característica hace más caro almacenamiento, pero puede reducir el costo habilitando ubicación geográfica cuando se crea la cuenta de almacenamiento en lugar de agregar la característica más adelante. Para obtener más información, vea [Geo replicación](http://go.microsoft.com/fwlink/?LinkId=253108).

1. En la parte inferior de la ventana, elija el vínculo **Crear cuenta de almacenamiento** .

Después de crear la cuenta de almacenamiento, verá las direcciones URL que puede usar para tener acceso a los recursos en cada uno de los servicios de almacenamiento de Azure y también las teclas de acceso principal y secundaria para su cuenta. Utilice estas teclas para autenticar las solicitudes realizadas con los servicios de almacenamiento.

>[AZURE.NOTE] La tecla de acceso secundario proporciona el mismo acceso a su cuenta de almacenamiento, como la tecla de acceso principal y se genera una copia de seguridad debe verse comprometida la clave de acceso principal. Además, se recomienda que vuelva a generar las claves de acceso de forma regular. Puede modificar un valor de cadena de conexión para usar la clave secundaria mientras regenerar la clave principal, a continuación, puede modificarla para usar la clave principal generada mientras regenerar la clave secundaria.

## <a name="configure-your-app-to-use-services-provided-by-the-storage-account"></a>Configurar la aplicación para usar los servicios de la cuenta de almacenamiento

Configure cualquier función que tiene acceso a servicios de almacenamiento para usar los servicios de almacenamiento de Azure que haya creado. Para ello, puede usar varias configuraciones de servicio para el proyecto de Azure. De forma predeterminada, se crean dos en su proyecto de Azure. Mediante el uso de varias configuraciones de servicio, puede usar la misma cadena de conexión en el código, pero tiene un valor distinto de una cadena de conexión en cada configuración del servicio. Por ejemplo, puede usar una configuración de servicio para ejecutar y depurar la aplicación localmente mediante el emulador de almacenamiento de Azure y una configuración de servicio diferente para publicar su aplicación para Azure. Para obtener más información acerca de las configuraciones de servicio, vea [Configurar su Azure proyecto usando varias configuraciones de servicio](vs-azure-tools-multiple-services-project-configurations.md).

### <a name="to-configure-your-application-to-use-services-that-the-storage-account-provides"></a>Para configurar la aplicación para usar los servicios que proporciona la cuenta de almacenamiento

1. En Visual Studio, abra la solución de Azure. En el Explorador de soluciones, abra el menú contextual para cada rol del proyecto Azure que tiene acceso a los servicios de almacenamiento y elija **Propiedades**. En el editor de Visual Studio, se muestra una página con el nombre de la función. La página muestra los campos de la pestaña **configuración** .

1. En las páginas de propiedades de la función, seleccione **configuración**.

1. En la lista de **Configuración del servicio** , elija el nombre de la configuración de servicio que desea editar. Si desea realizar cambios en todas las configuraciones de servicio para esta función, puede elegir **Todas las configuraciones**.  Para obtener más información acerca de cómo actualizar las configuraciones de servicio, vea la sección **Administrar cadenas de conexión para las cuentas de almacenamiento** en el tema [configurar los Roles de un servicio de nube de Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Para modificar cualquier configuración de la cadena de conexión, elija **...** botón junto a la configuración. Aparece el cuadro de diálogo **Crear cadena de conexión de almacenamiento** .

1. En **Conectar usando**, elija la opción de **la suscripción** .

1. En la lista de **suscripción** , seleccione la suscripción. Si la lista de suscripciones no incluye el elemento que desee, elija el vínculo de **Descarga de configuración de publicación** .

1. En la lista **nombre de la cuenta** , elija el nombre de la cuenta de almacenamiento. Herramientas de Azure obtiene automáticamente las credenciales de cuenta de almacenamiento utilizando el archivo .publishsettings. Para especificar manualmente las credenciales de cuenta de almacenamiento, elija la opción **cambiar manualmente las credenciales** y, a continuación, continúe con este procedimiento. Puede obtener el nombre de la cuenta de almacenamiento y la clave principal desde el [portal de clásico de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885). Si no desea especificar el almacenamiento de configuración de la cuenta de forma manual, elija el botón **Aceptar** para cerrar el cuadro de diálogo.

1. Elija el vínculo de credenciales de **cuenta de almacenamiento de entrar** .

1. En el cuadro **nombre de la cuenta** , escriba el nombre de su cuenta de almacenamiento.

    >[AZURE.NOTE] Iniciar sesión en el [portal de clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885)y, a continuación, elija el botón de **almacenamiento** . El portal muestra una lista de cuentas de almacenamiento. Si elige una cuenta, se abre una página para el mismo. Puede copiar el nombre de la cuenta de almacenamiento de esta página. Si está utilizando una versión anterior del portal clásica, el nombre de su cuenta de almacenamiento aparece en la vista de **Cuentas de almacenamiento** . Para copiar este nombre, resalte en la ventana de **Propiedades** de esta vista y, a continuación, elija las teclas Ctrl-C. Pegue el nombre en Visual Studio, elija el cuadro de texto **nombre de la cuenta** y, a continuación, elija las teclas Ctrl + V.

1. En el cuadro **clave de cuenta** , escriba la clave principal, o copiar y pegar desde el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
    Para copiar esta clave:

    1. En la parte inferior de la página de la cuenta de almacenamiento adecuado, elija el botón **Administrar claves** .

    1. En la página **Administrar teclas de acceso** , seleccione el texto de la tecla de acceso principal y, a continuación, elija las teclas Ctrl + C.

    1. En herramientas de Azure, pegue la clave en el cuadro **clave de cuenta** .

    1. Debe seleccionar una de las siguientes opciones para determinar cómo el servicio tendrá acceso a la cuenta de almacenamiento:
        - **Usar HTTP**. Esta es la opción estándar. Por ejemplo, `http://<account name>.blob.core.windows.net`.
        - **HTTPS de uso** de una conexión segura. Por ejemplo, `https://<accountname>.blob.core.windows.net`.
        - **Especificar personalizados extremos** para cada uno de los tres servicios. A continuación, puede escribir estos extremos en el campo para el servicio específico.

        >[AZURE.NOTE] Si crea extremos personalizados, puede crear una cadena de conexión más compleja. Cuando utiliza este formato de cadena, puede especificar los extremos de servicio de almacenamiento que incluyen un nombre de dominio personalizado que se han registrado para su cuenta de almacenamiento con el servicio de blobs. También puede conceder acceso a los recursos de blobs de Windows en un solo contenedor a través de una firma de acceso compartido. Para obtener más información sobre cómo crear extremos personalizados, vea [Configurar las cadenas de conexión de almacenamiento de Azure](storage-configure-connection-string.md).

1. Para guardar los cambios de la cadena de conexión, elija el botón **Aceptar** y, a continuación, elija el botón **Guardar** en la barra de herramientas. Después de guardar estos cambios, puede obtener el valor de esta cadena de conexión en el código mediante [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Cuando se publica la aplicación en Azure, elija la configuración del servicio que contiene la cuenta de Azure almacenamiento de la cadena de conexión. Después de que se publica la aplicación, compruebe que la aplicación funciona como se esperaba con los servicios de almacenamiento de Azure

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de cómo publicar aplicaciones para Azure desde Visual Studio, consulte [publicar un servicio de nube con las herramientas de Azure](vs-azure-tools-publishing-a-cloud-service.md).
