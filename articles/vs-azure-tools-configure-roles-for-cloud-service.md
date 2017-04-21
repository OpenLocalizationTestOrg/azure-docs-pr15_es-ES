<properties
   pageTitle="Configurar los Roles de un servicio de nube Azure con Visual Studio | Microsoft Azure"
   description="Obtenga información sobre cómo instalar y configurar los roles para los servicios de nube de Azure mediante Visual Studio."
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

# <a name="configure-the-roles-for-an-azure-cloud-service-with-visual-studio"></a>Configurar los Roles de un servicio de nube Azure con Visual Studio

Puede tener un servicio de nube Azure trabajador o funciones web. Para cada rol debe definir cómo se configura ese rol y también configurar la ejecución de esa función. Para obtener más información acerca de las funciones de servicios en la nube, consulte el vídeo de [Introducción a los servicios de nube de Azure](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). La información de su servicio de nube se almacena en los archivos siguientes:

- **ServiceDefinition.csdef**

    El archivo de definición de servicio define la configuración de tiempo de ejecución de su servicio de nube incluidos qué funciones son necesarias, extremos y tamaño de la máquina virtual. Ninguno de los datos almacenados en este archivo se puede cambiar cuando se usa la función.

- **ServiceConfiguration.cscfg**

    El archivo de configuración de servicio configura cuántas instancias de una función se ejecutan y los valores de la configuración definida para una función. Los datos almacenados en este archivo pueden cambiarse mientras se está ejecutando su rol.

Para poder almacenar distintos valores para esta configuración para la ejecución de su rol, puede tener varias configuraciones de servicio. Puede usar una configuración de servicio diferente para cada entorno de implementación. Por ejemplo, puede establecer la cadena de conexión de cuenta de almacenamiento para usarlo en una configuración de servicio local almacenamiento Azure local y crear otra configuración de servicio para usar el almacenamiento de Azure en la nube.

Cuando se crea un nuevo servicio de nube de Azure en Visual Studio, dos configuraciones de servicio se crean de forma predeterminada. Estas configuraciones se agregan a su proyecto de Azure. Las configuraciones se denominan:

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## <a name="configure-an-azure-cloud-service"></a>Configurar un servicio de nube de Azure

Puede configurar un servicio de nube Azure desde el Explorador de soluciones de Visual Studio, como se muestra en la siguiente ilustración.

![Configurar el servicio de nube](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### <a name="to-configure-an-azure-cloud-service"></a>Para configurar un servicio de nube de Azure

1. Para configurar cada rol en su proyecto Azure desde el **Explorador de soluciones**, abra el menú contextual para la función en el proyecto de Azure y, a continuación, elija **Propiedades**.

    En el editor de Visual Studio, se muestra una página con el nombre de la función. La página muestra los campos de la pestaña **configuración** .

1. En la lista de **Configuración del servicio** , elija el nombre de la configuración de servicio que desea editar.

    Si desea realizar cambios en todas las configuraciones de servicio para esta función, puede elegir **Todas las configuraciones**.

    >[AZURE.IMPORTANT] Si elige una configuración de servicio específicos, algunas propiedades están deshabilitados porque solo se pueden establecer para todas las configuraciones. Para editar estas propiedades, debe elegir todas las configuraciones.

    Ahora puede elegir una ficha para actualizar las propiedades habilitadas en esa vista.

## <a name="change-the-number-of-role-instances"></a>Cambiar el número de instancias de función

Para mejorar el rendimiento de su servicio de nube, puede cambiar el número de instancias de una función que se están ejecutando en función del número de usuarios o la carga que se espera para una función en particular. Cuando se ejecuta el servicio de nube en Azure, se crea una máquina virtual distinta para cada instancia de una función. Esto afectará a la facturación de la implementación de este servicio de nube. Para obtener más información sobre facturación, vea [comprender su factura de Microsoft Azure](billing/billing-understand-your-bill.md).

### <a name="to-change-the-number-of-instances-for-a-role"></a>Para cambiar el número de instancias de una función

1. Elija la pestaña **configuración** .

1. En la lista de **Configuración del servicio** , elija la configuración del servicio que desea actualizar.

    >[AZURE.NOTE] Puede establecer el recuento de instancia para una configuración de servicio específico o para todas las configuraciones de servicio.

1. En el cuadro de texto **número de instancia** , escriba el número de instancias que desea iniciar para esta función.

    >[AZURE.NOTE] Cada instancia se ejecuta en una máquina virtual distinta al publicar su servicio de nube en Azure.

1. Elija el botón **Guardar** en la barra de herramientas para guardar los cambios en el archivo de configuración de servicio.

## <a name="manage-connection-strings-for-storage-accounts"></a>Administrar las cadenas de conexión para las cuentas de almacenamiento

Puede agregar, quitar o modificar las cadenas de conexión para las configuraciones de servicio. Es recomendable cadenas de conexión diferentes configuraciones de servicio diferente. Por ejemplo, imaginemos una cadena de conexión local para una configuración de servicio local que tiene un valor de `UseDevelopmentStorage=true`. También puede configurar una configuración de servicio de nube que se usa una cuenta de almacenamiento en Azure.

>[AZURE.WARNING] Al especificar la información de clave de cuenta de Azure almacenamiento de una cadena de conexión de la cuenta de almacenamiento, esta información se almacena localmente en el archivo de configuración de servicio. Sin embargo, esta información actualmente no guardada como texto cifrado.

Usa un valor diferente para cada configuración de servicio, es necesario utilizar cadenas de conexión diferentes en el servicio de nube o modificar el código al publicar su servicio de nube en Azure. Puede utilizar el mismo nombre para la cadena de conexión en el código y el valor debe ser diferente, en función de la configuración del servicio que selecciona cuando se crea el servicio de nube o cuando se publica.

### <a name="to-manage-connection-strings-for-storage-accounts"></a>Administrar las cadenas de conexión para las cuentas de almacenamiento

1. Elija la pestaña **configuración** .

1. En la lista de **Configuración del servicio** , elija la configuración del servicio que desea actualizar.

    >[AZURE.NOTE] Puede actualizar las cadenas de conexión para una configuración de servicio específico, pero si necesita agregar o eliminar una cadena de conexión debe seleccionar todas las configuraciones.

1. Para agregar una cadena de conexión, seleccione el botón de **Opción de agregar** . Se agrega una nueva entrada a la lista.

1. En el cuadro de texto **nombre** , escriba el nombre que desea usar para la cadena de conexión.

1. En la lista desplegable **tipo** , elija la **Cadena de conexión**.

1. Para cambiar el valor de la cadena de conexión, seleccione el botón de puntos suspensivos (...). Aparece el cuadro de diálogo **Crear cadena de conexión de almacenamiento** .

1. Para usarlo almacenamiento local cuenta, elija el botón de opción **emulador de Microsoft Azure almacenamiento** y, a continuación, elija el botón **Aceptar** .

1. Para usar una cuenta de almacenamiento en Azure, elija el botón de opción de **la suscripción** y seleccione la cuenta de almacenamiento deseada.

1. Para usar credenciales personalizadas, seleccione el botón de opciones **manualmente las credenciales** . Escriba el nombre de la cuenta de almacenamiento y la clave principal o el segunda. Para obtener información sobre cómo crear una cuenta de almacenamiento y cómo introducir los detalles de la cuenta de almacenamiento en el cuadro de diálogo **Crear cadena de conexión de almacenamiento** , vea [Preparar a publicar o implemente una aplicación de Azure de Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Para eliminar una cadena de conexión, seleccione la cadena de conexión y, a continuación, elija el botón de **Opción Quitar** .

1. Elija el icono **Guardar** en la barra de herramientas para guardar los cambios en el archivo de configuración de servicio.

1. Para obtener acceso a la cadena de conexión en el archivo de configuración de servicio, debe obtener el valor de la opción de configuración. El código siguiente muestra un ejemplo donde se crea el almacenamiento de blobs y cargados con una cadena de conexión de datos `MyConnectionString` desde el archivo de configuración de servicio cuando un usuario elige **Button1** en la página Default.aspx en el rol web para un servicio de nube de Azure. Agregue las siguientes instrucciones using al Default.aspx.cs:

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Abra Default.aspx.cs en la vista Diseño y agregue un botón desde el cuadro de herramientas. Agregue el código siguiente a la `Button1_Click` método. Este código utiliza `GetConfigurationSettingValue` para obtener el valor del archivo de configuración de servicio para la cadena de conexión. A continuación, se crea un blob en la cuenta de almacenamiento que se hace referencia en la cadena de conexión `MyConnectionString` y por último el programa agrega texto al objeto binario.

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Agregar una configuración personalizada para usar en el servicio de nube de Azure

Una configuración personalizada en el archivo de configuración de servicio le permiten agregar un nombre y un valor para una cadena para una configuración de servicio específico. Puede usar esta opción para configurar una característica en el servicio de nube al leer el valor de la configuración y usar este valor para controlar la lógica en el código. Puede cambiar estos valores de configuración de servicio sin tener que volver a crear el paquete de servicio o cuando se ejecuta el servicio de nube. Puede comprobar el código para las notificaciones de cuando cambia un valor. Vea el [Evento RoleEnvironment.Changing](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Puede agregar, quitar o modificar una configuración personalizada para las configuraciones de servicio. Puede que desee valores diferentes para estas cadenas configuraciones de servicio diferente.

Usa un valor diferente para cada configuración de servicio, es necesario utilizar cadenas diferentes en el servicio de nube o modificar el código al publicar su servicio de nube en Azure. Puede utilizar el mismo nombre de la cadena en el código y el valor debe ser diferente, en función de la configuración del servicio que selecciona cuando se crea el servicio de nube o cuando se publica.

### <a name="to-add-custom-settings-to-use-in-your-azure-cloud-service"></a>Para agregar una configuración personalizada para usar en el servicio de nube de Azure

1. Elija la pestaña **configuración** .

1. En la lista de **Configuración del servicio** , elija la configuración del servicio que desea actualizar.

    >[AZURE.NOTE] Puede actualizar cadenas para una configuración de servicio específico, pero si necesita agregar o eliminar una cadena, debe seleccionar **Todas las configuraciones**.

1. Para agregar una cadena de, seleccione el botón **Agregar configuración** . Se agrega una nueva entrada a la lista.

1. En el cuadro de texto **nombre** , escriba el nombre que desea utilizar para la cadena.

1. En la lista desplegable **tipo** , elija **cadena**.

1. Para agregar o cambiar el valor de la cadena, en el cuadro de texto **valor** , escriba el nuevo valor.

1. Para eliminar una cadena, seleccione la cadena y, a continuación, elija el botón de **Opción Quitar** .

1. Elija el botón **Guardar** en la barra de herramientas para guardar los cambios en el archivo de configuración de servicio.

1. Para obtener acceso a la cadena en el archivo de configuración de servicio, debe obtener el valor de la opción de configuración.

    Debe asegurarse de que el siguiente mediante instrucciones se han añadido al Default.aspx.cs al igual que en el procedimiento anterior.

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Agregue el código siguiente a la `Button1_Click` método para tener acceso a esta cadena de la misma manera que tiene acceso a una cadena de conexión. El código, a continuación, puede realizar algunos código específico en función del valor de la cadena de configuración para el archivo de configuración de servicio que se usa.

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Administrar almacenamiento local para cada instancia de función

Puede agregar almacenamiento de sistema de archivos local para cada instancia de una función. Puede almacenar datos locales aquí que no es necesario tener acceso a otras funciones. Todos los datos que no es necesario guardar en la tabla, blob o almacenamiento de base de datos SQL pueden almacenarse en aquí. Por ejemplo, podría usar este almacenamiento local para almacenar los datos que necesitan volver a utilizar. Otras instancias de una función no pueden acceder a los datos almacenados. 

Configuración de almacenamiento local se aplica a todas las configuraciones de servicio. Solo puede agregar, quitar o modificar almacenamiento local para todas las configuraciones de servicio.

### <a name="to-manage-local-storage-for-each-role-instance"></a>Para administrar el almacenamiento local para cada instancia de función

1. Elija la pestaña **Almacenamiento Local** .

1. En la lista de **Configuración del servicio** , seleccione **Todas las configuraciones**.

1. Para agregar una entrada de almacenamiento local, elija el botón **Agregar almacenamiento Local** . Se agrega una nueva entrada a la lista.

1. En el cuadro de texto **nombre** , escriba el nombre que desea usar para este almacenamiento local.

1. En el cuadro de texto **tamaño** , escriba el tamaño en MB que necesita para esta almacenamiento local.

1. Para quitar los datos en este almacenamiento local cuando la máquina virtual para esta función es recicla, active la casilla de verificación **Limpiar en función de reciclaje** .

1. Para editar una entrada de almacenamiento local existente, seleccione la fila que deberá actualizar. Puede editar los campos, como se describe en los pasos anteriores.

1. Para eliminar una entrada de almacenamiento local, seleccione la entrada de almacenamiento en la lista y, a continuación, elija el botón **Quitar almacenamiento Local** .

1. Para guardar los cambios a los archivos de configuración de servicio, seleccione el icono **Guardar** en la barra de herramientas.

1. Para obtener acceso al almacenamiento local que ha agregado en el archivo de configuración de servicio, debe obtener el valor de la configuración de recurso local. Use las siguientes líneas de código para tener acceso a este valor y cree un archivo llamado **MyStorageTest.txt** y escribir una línea de datos de prueba en ese archivo. Puede agregar este código en el `Button_Click` método que se utilizó en los procedimientos anteriores:

1. Debe asegurarse de que el siguiente mediante instrucciones se agregan al Default.aspx.cs:

    ```
    using System.IO;
    using System.Text;
    ```

1. Agregue el código siguiente a la `Button1_Click` método. Esto crea el archivo en el almacenamiento local y escribe datos de prueba en ese archivo.

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. (Opcional) Para ver el archivo que ha creado cuando se ejecuta el servicio de nube localmente, realice los siguientes pasos:

  1. Ejecute la función web y seleccione **Button1** para asegurarse de que el código interior `Button1_Click` se llama.

  1. En el área de notificación, abra el menú contextual para el icono de Azure y elija **Mostrar IU de emulador calcular**. Aparece el cuadro de diálogo de **Azure calcular emulador** .

  1. Seleccione el rol de web.

  1. En la barra de menús, elija **Herramientas**, **Abrir almacén local**. Aparece una ventana del explorador de Windows.

  1. En la barra de menús, escriba **MyStorageTest.txt** en el cuadro de texto **Buscar** y luego elija **ENTRAR** para iniciar la búsqueda.

    El archivo se muestra en los resultados de búsqueda.

  1. Para ver el contenido del archivo, abrir el menú contextual para el archivo y elija **Abrir**.

## <a name="collect-cloud-service-diagnostics"></a>Recopilar diagnósticos de servicio de nube

Puede recopilar datos de diagnóstico para el servicio de nube de Azure. Estos datos se agregan a una cuenta de almacenamiento. Es recomendable cadenas de conexión diferentes configuraciones de servicio diferente. Por ejemplo, imaginemos una cuenta de almacenamiento local para una configuración de servicio local que tiene un valor de UseDevelopmentStorage = true. También puede configurar una configuración de servicio de nube que se usa una cuenta de almacenamiento en Azure. Para obtener más información acerca de diagnósticos de Azure, vea recopilar datos de registro mediante el uso de diagnósticos de Azure.

>[AZURE.NOTE] La configuración del servicio local ya está configurada para usar recursos locales. Si usa la configuración del servicio de nube para publicar su servicio de nube de Azure, la cadena de conexión que especifique cuando se publica también se usa para la cadena de conexión de diagnósticos a menos que haya especificado una cadena de conexión. Si empaqueta su servicio de nube mediante Visual Studio, no se cambia la cadena de conexión en la configuración del servicio.

### <a name="to-collect-cloud-service-diagnostics"></a>Recopilar diagnósticos de servicio de nube

1. Elija la pestaña **configuración** .

1. En la lista de **Configuración del servicio** , elija la configuración del servicio que desea actualizar o elija **Todas las configuraciones**.

    >[AZURE.NOTE] Puede actualizar la cuenta de almacenamiento para una configuración de servicio específico, pero si desea habilitar o deshabilitar diagnósticos debe elegir todas las configuraciones.

1. Para habilitar diagnósticos, active la casilla de verificación **Habilitar diagnósticos** .

1. Para cambiar el valor de la cuenta de almacenamiento, seleccione el botón de puntos suspensivos (...).

    Aparece el cuadro de diálogo **Crear cadena de conexión de almacenamiento** .

1. Para usar una cadena de conexión local, elija la opción de almacenamiento de Azure emulador y, a continuación, elija el botón **Aceptar** .

1. Para usar una cuenta de almacenamiento asociada a su suscripción de Azure, elija la opción de **la suscripción** .

1. Para usar una cuenta de almacenamiento para la cadena de conexión local, elija la opción **cambiar manualmente las credenciales** .

    Para obtener más información sobre cómo crear una cuenta de almacenamiento y cómo introducir los detalles de la cuenta de almacenamiento en el cuadro de diálogo **Crear cadena de conexión de almacenamiento** , vea [Preparar a publicar o implemente una aplicación de Azure de Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Elija la cuenta de almacenamiento que desea usar en el **nombre de la cuenta**.

    Si se encuentra manualmente introducir sus credenciales de cuenta de almacenamiento, copie o escriba su clave principal en **clave de cuenta**. Esta clave se puede copiar desde el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Para copiar esta clave, siguiendo estos pasos de la vista de **Cuentas de almacenamiento** en el [portal de clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885):
    
  1. Seleccione la cuenta de almacenamiento que desea usar para su servicio de nube.

  1. Elija el botón **Administrar las teclas de acceso** que se encuentra en la parte inferior de la pantalla. Aparece el cuadro de diálogo **Administrar las teclas de acceso** .

  1. Para copiar la tecla de acceso, elija el botón **Copiar al Portapapeles** . Ahora puede pegar esta clave en el campo de **clave de cuenta** .

1. Usar la cuenta de almacenamiento que proporciona, como la cadena de conexión para diagnósticos (y almacenamiento en caché) al publicar su servicio de nube en Azure, seleccione la casilla de verificación **Actualizar desarrollo almacenamiento cadenas de conexión para diagnósticos y almacenamiento en caché con el almacenamiento de Azure credenciales cuando se publica en Azure** .

1. Elija el botón **Guardar** en la barra de herramientas para guardar los cambios en el archivo de configuración de servicio.

## <a name="change-the-size-of-the-virtual-machine-used-for-each-role"></a>Cambiar el tamaño de la máquina virtual que se utilizarán para cada rol

Puede establecer el tamaño de la máquina virtual de cada rol. Solo puede establecer este tamaño para todas las configuraciones de servicio. Si selecciona un tamaño de la máquina, a continuación, menos núcleos de CPU, memoria y disco local se asigna almacenamiento. Ancho de banda asignado también es más pequeño. Para obtener más información sobre estos tamaños y los recursos asignados, vea [tamaños de servicios en la nube](cloud-services/cloud-services-sizes-specs.md).

Los recursos necesarios para cada máquina virtual en Azure afecta al costo de la ejecución de su servicio de nube en Azure. Para obtener más información sobre la facturación de Azure, vea [comprender su factura de Microsoft Azure](billing/billing-understand-your-bill.md).

### <a name="to-change-the-size-of-the-virtual-machine"></a>Para cambiar el tamaño de la máquina virtual

1. Elija la pestaña **configuración** .

1. En la lista de **Configuración del servicio** , seleccione **Todas las configuraciones**.

1. Para seleccionar el tamaño de la máquina virtual para este rol, elija el tamaño apropiado de la lista de **tamaño de memoria virtual** .

1. Elija el botón **Guardar** en la barra de herramientas para guardar los cambios en el archivo de configuración de servicio.

## <a name="manage-endpoints-and-certificates-for-your-roles"></a>Administrar certificados para los roles y extremos

Configurar redes extremos especificando el protocolo, el número de puerto y, para HTTPS, la información del certificado SSL. HTTP, HTTPS y TCP, compatible con versiones anteriores de junio de 2012. Es compatible con la versión de junio de 2012 dichos protocolos y UDP. No puede usar UDP para extremos de entrada en el emulador de cálculo. Puede usar ese protocolo solo para los extremos internos.

Para mejorar la seguridad de su servicio de nube de Azure, puede crear extremos que usen el protocolo HTTPS. Por ejemplo, si tiene un servicio de nube que se utiliza por los clientes para pedidos de compra, desea asegurarse de que su información es segura mediante SSL.

También puede agregar extremos que se pueden usar internamente o externamente. Extremos externos se denominan extremos de entrada. Un extremo de entrada permite otro punto de acceso a los usuarios de su servicio de nube. Si tiene un servicio WCF, desea exponer un extremo interno para un rol de web tener acceso a este servicio.

>[AZURE.IMPORTANT] Solo puede actualizar extremos para todas las configuraciones de servicio.

Si agrega extremos HTTPS, debe usar un certificado SSL. Para hacer esto puede asociar certificados con su rol para todas las configuraciones de servicio y usarlos en los extremos.

>[AZURE.IMPORTANT] Estos certificados no se formatean con el servicio. Debe cargar sus certificados por separado en Azure a través del [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

Todos los certificados de administración asociar las configuraciones de servicio solo se aplican cuando se ejecuta el servicio de nube en Azure. Cuando se ejecuta el servicio de nube en el entorno de desarrollo local, se utiliza un certificado estándar administrado por el emulador cálculo Azure.

### <a name="to-add-a-certificate-to-a-role"></a>Para agregar un certificado a un rol

1. Elija la ficha **certificados** .

1. En la lista de **Configuración del servicio** , seleccione **Todas las configuraciones**.

    >[AZURE.NOTE] Para agregar o quitar certificados, debe seleccionar todas las configuraciones. Puede actualizar el nombre y la huella digital de una configuración de servicio específico si es necesario.

1. Para agregar un certificado para esta función, seleccione el botón **Agregar certificado** . Se agrega una nueva entrada a la lista.

1. En el cuadro de texto **nombre** , escriba el nombre del certificado.

1. En la lista **Ubicación del almacén** , seleccione la ubicación para el certificado que desea agregar.

1. En la lista **Nombre de almacén** , seleccione el almacén que desea utilizar para seleccionar el certificado.

1. Para agregar el certificado, seleccione el botón de puntos suspensivos (...). Aparece el cuadro de diálogo de **Seguridad de Windows** .

1. Elija el certificado que desea utilizar en la lista y, a continuación, elija el botón **Aceptar** .

    >[AZURE.NOTE] Al agregar un certificado del almacén de certificados, todos los certificados intermedios se agregan automáticamente a las opciones de configuración para usted. Estos certificados intermedios también deben cargarse en Azure para configurar correctamente el servicio para SSL.

1. Para eliminar un certificado, seleccione el certificado y, a continuación, elija el botón **Quitar certificado** .

1. Elija el icono **Guardar** en la barra de herramientas para guardar los cambios en los archivos de configuración de servicio.

### <a name="to-manage-endpoints-for-a-role"></a>Administrar los extremos de una función

1. Elija la ficha **extremos** .

1. En la lista de **Configuración del servicio** , seleccione **Todas las configuraciones**.

1. Para agregar un punto final, elija el botón **Agregar extremo** . Se agrega una nueva entrada a la lista.

1. En el cuadro de texto **nombre** , escriba el nombre que desea utilizar para este extremo.

1. En la lista **tipo** , elija el tipo de extremo que necesita.

1. Elija el protocolo del extremo que necesita de la lista de **protocolo** .

1. Si se trata de un extremo de entrada, en el cuadro de texto **Puerto público** , especifique el puerto público para usar.

1. En el cuadro de texto **Puerto a privado** , escriba el puerto privado.

1. Si el extremo requiere el protocolo https, elija un certificado en la lista **Nombre del certificado SSL** .

    >[AZURE.NOTE] Esta lista muestra los certificados que haya agregado para esta función en la ficha **certificados** .

1. Elija el botón **Guardar** en la barra de herramientas para guardar los cambios en los archivos de configuración de servicio.

## <a name="next-steps"></a>Pasos siguientes
Más información sobre proyectos Azure en Visual Studio mediante la lectura de [la configuración de un proyecto de Azure](vs-azure-tools-configuring-an-azure-project.md). Más información sobre el esquema del servicio de nube lea [Referencia del esquema](https://msdn.microsoft.com/library/azure/dd179398).
