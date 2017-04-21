<properties
   pageTitle="Explorar y administrar los recursos de almacenamiento con el Explorador de servidor | Microsoft Azure"
   description="Explorar y administrar los recursos de almacenamiento con el Explorador de servidores"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="tarcher" />

# <a name="browsing-and-managing-storage-resources-with-server-explorer"></a>Explorar y administrar los recursos de almacenamiento con el Explorador de servidores

[AZURE.INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Información general
Si ha instalado las herramientas de Azure para Microsoft Visual Studio, puede ver blob, colas y datos de la tabla de sus cuentas de almacenamiento de Azure. El nodo de almacenamiento de Azure en el Explorador de servidores muestra los datos que está en su cuenta de emulador de almacenamiento local y sus otras cuentas de almacenamiento de Azure.

Para ver el Explorador de servidores en Visual Studio, en la barra de menús, elija **Ver**, **Explorador de servidores**. El nodo de almacenamiento muestra todas las cuentas de almacenamiento que existen en cada suscripción/certificado Azure a que está conectado. Si no aparece la cuenta de almacenamiento, puede agregar, siguiendo las instrucciones [más adelante en este tema](#add-storage-accounts-by-using-server-explorer).

A partir de Azure SDK 2.7, también puede usar el nuevo explorador de nube para ver y administrar los recursos de Azure. Para obtener más información, vea [Administración de recursos de Azure con el Explorador de la nube](./vs-azure-tools-resources-managing-with-cloud-explorer.md) .


## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Ver y administrar recursos de almacenamiento en Visual Studio

Explorador de servidores muestra automáticamente una lista de BLOB, colas y tablas en su cuenta de emulador de almacenamiento. La cuenta de almacenamiento emulador aparece en el Explorador de servidores bajo el nodo de almacenamiento como el nodo de **desarrollo** .

Para ver los recursos de la cuenta de emulador de almacenamiento, expanda el nodo de **desarrollo** . Si no se ha iniciado el emulador almacenamiento cuando expanda el nodo de **desarrollo** , se iniciará automáticamente. Esto puede tardar unos segundos. Puede seguir trabajando en otras áreas de Visual Studio mientras se inicia el emulador de almacenamiento.

Para ver los recursos en una cuenta de almacenamiento, expanda el nodo de la cuenta almacenamiento en el Explorador de servidores. Aparecen los nodos secundarios siguientes:

- BLOB

- Colas

- Tablas

## <a name="work-with-blob-resources"></a>Trabajar con recursos Blob

El nodo de BLOB muestra una lista de contenedores para la cuenta de almacenamiento seleccionado. Contenedores de BLOB contienen archivos blob y se puede organizar estos BLOB en carpetas y subcarpetas. Para obtener más información, vea [cómo usar el almacenamiento de blobs de .NET](./storage/storage-dotnet-how-to-use-blobs.md) .

### <a name="to-create-a-blob-container"></a>Para crear un contenedor de blob

1. Abrir el menú contextual para el nodo **BLOB** y, a continuación, elija **Crear contenedor de Blob**.

1. Escriba el nombre del contenedor de nuevo en el cuadro de diálogo **Crear contenedor de blobs de Windows** y, a continuación, elija **Aceptar**

    >[AZURE.NOTE] El nombre del contenedor de blob debe comenzar con una letra minúscula (a z) o un número (0-9).

### <a name="to-delete-a-blob-container"></a>Para eliminar un contenedor de blob

- Abra el menú contextual para el contenedor de blob que desea quitar y, a continuación, elija **Eliminar**.

### <a name="to-display-a-list-of-the-items-contained-in-a-blob-container"></a>Para mostrar una lista de los elementos contenidos en un contenedor de blob

- Abrir el menú contextual de un nombre de contenedor de blobs de la lista y, a continuación, elija **Vista Blob contenedor**.

    Cuando se ve el contenido de un contenedor de blob, aparece en una ficha conocida como la vista del contenedor de blob.

    ![VST_SE_BlobDesigner](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

    Puede realizar las siguientes operaciones en BLOB mediante los botones en la esquina superior derecha de la vista del contenedor de blob:

    - Escriba un valor de filtro y aplicarlo

    - Actualizar la lista de BLOB en el contenedor

    - Cargar un archivo

    - Eliminar un blob

      >[AZURE.NOTE] Eliminar un archivo desde un contenedor de blob no elimina el archivo subyacente. solo se quita del contenedor de blob.

    - Abrir un blob

    - Guardar un blob en el equipo local

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>Para crear una carpeta o subcarpeta en un contenedor de blob

1. Elija el contenedor de blob en Explorador de servidores. En la ventana de contenedor, elija el botón **Cargar blobs** .

    ![Cargar un archivo a una carpeta de blobs](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

1. En el cuadro de diálogo **Cargar archivo nuevo** , elija el botón **Examinar** para especificar el archivo que desea cargar y, a continuación, escriba un nombre de carpeta en el cuadro **carpeta (opcional)** .

    Puede agregar subcarpetas en las carpetas de contenedor siguiendo el mismo procedimiento. Si no especifica un nombre de carpeta, el archivo se cargarán en el nivel superior del contenedor de blob. El archivo aparece en la carpeta especificada en el contenedor.

    ![Carpeta agregado a un contenedor de blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Haga doble clic en la carpeta o presione ENTRAR para ver el contenido de la carpeta. Cuando esté en la carpeta del contenedor, puede desplazarse a la raíz del contenedor seleccionando el botón **Abrir directorio a primario** (flecha) arriba.

### <a name="to-delete-a-container-folder"></a>Para eliminar una carpeta de contenedor

 - Eliminar todos los archivos en la carpeta

    >[AZURE.NOTE] Dado que las carpetas de contenedores de blob son carpetas virtuales, no puede crear una carpeta vacía ni puede eliminar una carpeta para eliminar el contenido de su archivo. Tendrá que eliminar todo el contenido de una carpeta para eliminar la carpeta.

### <a name="to-filter-blobs-in-a-container"></a>Para filtrar BLOB en un contenedor

Puede filtrar el BLOB que se muestran especificando un prefijo común.

Por ejemplo, si escribe el prefijo `hello` en el texto del filtro cuadro y, a continuación, elija **Ejecutar** (**!**) botón, solo BLOB que comienzan con 'Hola' aparece.

![VST_SE_FilterBlobs](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)


>[AZURE.NOTE] El campo de filtro distingue mayúsculas de minúsculas y no admite el filtrado con caracteres comodín. BLOB solo se pueden filtrar por el prefijo. El prefijo puede incluir un delimitador si está utilizando un delimitador para organizar BLOB en una jerarquía virtual. Por ejemplo, filtrar por el prefijo HelloFabric / devuelve todos los blobs comienzan con esa cadena.

### <a name="to-download-blob-data"></a>Descargar datos blob

- En el **Explorador de servidores**, abra el menú contextual para blobs de uno o más y, a continuación, elija **Abrir**, elija el nombre de blobs y, a continuación, elija el botón **Abrir** o haga doble clic en el nombre de blobs.

    El progreso de la descarga de una blob aparece en la ventana de **Registro de actividad de Azure** .

    El blob se abre en el editor predeterminado para ese tipo de archivo. Si el sistema operativo reconoce el tipo de archivo, el archivo se abre en una aplicación instalada localmente; en caso contrario, le pide que elija una aplicación que es adecuada para el tipo de archivo del objeto binario. El archivo local que se crea al descargar un blob está marcado como de solo lectura.

    Datos BLOB en caché local y comprueba hora de última modificación de blobs en el servicio de blobs. Si se ha actualizado el blob desde última descarga, se descargará. en caso contrario, se cargará el blob desde el disco local. De forma predeterminada se descarga un blob en un directorio temporal. Para descargar BLOB en un directorio específico, abra el menú contextual de los nombres de blobs seleccionado y elija **Guardar como**. Cuando guarde un blob de esta manera, no se abre el archivo blob y se crea el archivo local con los atributos de lectura y escritura.

### <a name="to-upload-blobs"></a>Para cargar BLOB

- Elija el botón **Cargar Blob** cuando el contenedor se abre para su visualización en la vista del contenedor de blob.

    Puede elegir uno o más archivos para cargar y puede cargar archivos de cualquier tipo. El **Registro de actividad de Azure** muestra el progreso de la carga. Para obtener más información sobre cómo trabajar con datos blob, vea [cómo usar el servicio de almacenamiento de blobs de Windows Azure en .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### <a name="to-view-logs-transferred-to-blobs"></a>Para ver los registros que se transfieren a BLOB

- Si va a usar diagnósticos de Azure para registrar datos desde la aplicación de Azure y ha transferido registros a su cuenta de almacenamiento, verá contenedores que se crearon Azure para estos registros. Ver estos registros en el Explorador de servidores es una forma fácil de identificar los problemas con la aplicación, sobre todo si se ha implementado en Azure. Para obtener más información acerca de diagnósticos de Azure, vea [Recopilar datos de registro mediante el uso de diagnósticos de Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="to-get-the-url-for-a-blob"></a>Para obtener la dirección URL para un blob

- Abrir el menú de acceso directo de blobs de Windows y, a continuación, elija **Dirección URL de la copia**.

### <a name="to-edit-a-blob"></a>Para editar un blob

- Seleccione el objeto binario y, a continuación, elija el botón **Abrir Blob** .

    El archivo se descarga en una ubicación temporal y abierto en el equipo local. Debe cargar de nuevo el blob después de realizar cambios.

## <a name="work-with-queue-resources"></a>Trabajar con recursos de cola

Colas de servicios de almacenamiento se hospedan en una cuenta de almacenamiento de Azure y puede usar para permitir la nube roles de servicio para comunicarse entre sí y con otros servicios por un mecanismo de transferencia de mensajes. Puede tener acceso a la cola mediante programación a través de un servicio de nube y a través de un servicio web para los clientes externos. También puede tener acceso a la cola directamente mediante el Explorador de servidores en Visual Studio.

Al desarrollar un servicio de nube que utiliza colas, es recomendable usar Visual Studio para crear colas y trabajar con ellos de forma interactiva mientras desarrollar y probar el código.

En el Explorador de servidor, puede ver las colas de una cuenta de almacenamiento, crear y eliminar colas, abra una cola para ver sus mensajes y agregar los mensajes a una cola. Al abrir una cola para la visualización, puede ver los mensajes individuales y puede realizar las siguientes acciones en la cola mediante los botones en la esquina superior izquierda:

- Actualice la vista de la cola

- Agregar un mensaje a la cola

- Quitar el mensaje de nivel superior de la cola.

- Borrar toda la cola

La imagen siguiente muestra una cola que contiene dos mensajes.

![Visualización de una cola](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Para obtener más información sobre el almacenamiento de servicios de colas, vea [Cómo: utilizar el servicio de almacenamiento de cola](http://go.microsoft.com/fwlink/?LinkID=264702). Para obtener información sobre el servicio web para colas de servicios de almacenamiento, consulte [Conceptos de servicio de cola](http://go.microsoft.com/fwlink/?LinkId=264788). Para obtener información sobre cómo enviar mensajes a una cola de servicios de almacenamiento mediante Visual Studio, vea [Enviar mensajes a una cola de servicios de almacenamiento](https://msdn.microsoft.com/library/azure/jj649344.aspx).

>[AZURE.NOTE] Colas de servicios de almacenamiento son distintas de colas de bus de servicio. Para obtener más información acerca de las colas de bus de servicio, vea colas de Bus de servicio, temas y suscripciones.

## <a name="work-with-table-resources"></a>Trabajar con recursos de tabla

El servicio de almacenamiento de Azure tabla almacena grandes cantidades de datos estructurados. El servicio es un almacén de datos NoSQL que acepta autenticado llamadas desde dentro y fuera de la nube de Azure. Tablas de Azure son ideales para almacenar los datos estructurados y no relacionales.

### <a name="to-create-a-table"></a>Para crear una tabla

1. En el Explorador de servidor, seleccione el nodo **tablas** de la cuenta de almacenamiento y, a continuación, elija **Crear tabla**.

1. En el cuadro de diálogo **Crear tabla** , escriba un nombre para la tabla.

### <a name="to-view-table-data"></a>Para ver los datos de tabla

1. En el Explorador de servidores, abra el nodo de **Azure** y, a continuación, abra el nodo de **almacenamiento** .

1. Abra el nodo de la cuenta de almacenamiento que le interesa y, a continuación, abra el nodo **tablas** para ver una lista de tablas para la cuenta de almacenamiento.

1. Abrir el menú contextual para una tabla y, a continuación, elija **Vista de tabla**.

    ![Una tabla de Azure en el Explorador de soluciones](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

La tabla está organizada por entidades (que se muestra en filas) y propiedades (que se muestra en columnas). Por ejemplo, la siguiente ilustración muestra entidades enumeradas en el **Diseñador de tablas**:

### <a name="to-edit-table-data"></a>Editar datos de tabla

1. En el **Diseñador de tablas**, abra el menú contextual de una entidad (una sola fila) o una propiedad (una sola celda) y, a continuación, elija **Editar**.

    ![Agregar o editar una entidad de tabla](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

    Entidades en una sola tabla no se necesitan tener el mismo conjunto de propiedades (columnas). Tenga en cuenta las siguientes restricciones de visualización y edición de datos de la tabla.
    - No puede ver ni editar datos binarios (tipo byte[]), pero puede guardarlo en una tabla.

    - No puede editar los valores de **PartitionKey** o **RowKey** , porque el almacenamiento de tablas en Azure no admite la operación.

    - No se puede crear una propiedad de marca de tiempo, servicios de almacenamiento de Azure utilizan una propiedad con ese nombre.

    - Si escribe un valor DateTime, debe tener un formato que corresponda a la configuración regional y de idioma de su equipo (por ejemplo, MM/DD/AAAA HH [A.M. | P.M.] para inglés de Estados Unidos).

### <a name="to-add-entities"></a>Para agregar entidades

1. En el **Diseñador de tablas**, elija el botón **Agregar entidad** , que está cerca de la esquina superior derecha de la vista de tabla.

    ![Agregar entidad](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. En el cuadro de diálogo **Agregar entidad** , escriba los valores de las propiedades **PartitionKey** y **RowKey** .

    ![Agregar el cuadro de diálogo de la entidad](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Escriba los valores cuidadosamente porque no se puede cambiar después de cerrar el cuadro de diálogo a menos que se elimine la entidad y vuelva a agregarla.

### <a name="to-filter-entities"></a>Para filtrar entidades

Puede personalizar el conjunto de entidades que aparecen en una tabla si utiliza el generador de consultas.

1. Para abrir el generador de consultas, abra una tabla para la visualización.

1. Elija el botón situado en la barra de herramientas de la vista de tabla.

    Aparece el cuadro de diálogo **Generador de consultas** . La ilustración siguiente muestra una consulta que se está creando en el generador de consultas.

    ![Generador de consultas](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)

1. Cuando haya terminado la creación de la consulta, cierre el cuadro de diálogo. Aparece el formulario de texto resultante de la consulta en un cuadro de texto como un filtro de WCF Data Services.

1. Para ejecutar la consulta, elija el icono de triángulo verde.

    También puede filtrar los datos de la entidad que aparece en el **Diseñador de tablas** si escribe una cadena de filtro de WCF Data Services directamente en el campo de filtro. Este tipo de cadena es similar a una cláusula WHERE de SQL, pero se envía al servidor como una solicitud HTTP. Para obtener información acerca de cómo crear cadenas de filtro, vea [Construir cadenas de filtro para el Diseñador de tablas](https://msdn.microsoft.com/library/azure/ff683669.aspx).

    La siguiente ilustración muestra un ejemplo de una cadena de filtro válido:

    ![VST_SE_TableFilter](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

### <a name="refresh-storage-data"></a>Actualizar los datos de almacenamiento

Cuando el Explorador de servidor se conecta a u obtiene datos desde una cuenta de almacenamiento, puede tardar hasta un minuto completar la operación. Si no puede conectarse, la operación podría tiempo de espera. Mientras se recuperan datos, puede seguir trabajando en otros elementos de Visual Studio. Para cancelar la operación si dura demasiado, elija el botón **Detener actualización** en la barra de herramientas del explorador de servidores.

#### <a name="to-refresh-blob-container-data"></a>Actualizar los datos del contenedor de blob

- Seleccione el nodo de **BLOB** debajo de **almacenamiento** y elija el botón **Actualizar** en la barra de herramientas del explorador de servidores.

- Para actualizar la lista de BLOB que se muestra, elija el botón **Ejecutar** .

#### <a name="to-refresh-table-data"></a>Actualizar los datos de tabla

- Seleccione el nodo de **tablas** debajo de **almacenamiento** y elija el botón **Actualizar** .

- Para actualizar la lista de entidades que se muestra en el **Diseñador de tablas**, elija el botón **Ejecutar** en el **Diseñador de tablas**.

#### <a name="to-refresh-queue-data"></a>Actualizar los datos de cola

- Seleccione el nodo **colas** y, a continuación, elija el botón **Actualizar** .

#### <a name="to-refresh-all-items-in-a-storage-account"></a>Para actualizar todos los elementos de una cuenta de almacenamiento

- Elija el nombre de cuenta y, a continuación, elija el botón **Actualizar** en la barra de herramientas para el Explorador de servidores.

### <a name="add-storage-accounts-by-using-server-explorer"></a>Agregar cuentas de almacenamiento mediante el Explorador de servidores

Hay dos formas de agregar cuentas de almacenamiento mediante el Explorador de servidores. Puede crear una nueva cuenta de almacenamiento en su suscripción de Azure, o puede adjuntar una cuenta de almacenamiento existente.

#### <a name="to-create-a-new-storage-account-by-using-server-explorer"></a>Para crear una nueva cuenta de almacenamiento mediante el Explorador de servidores

1. En el Explorador de servidores, abra el menú contextual para el nodo de almacenamiento y, a continuación, elija crear cuenta de almacenamiento.

    ![Crear una nueva cuenta de almacenamiento de Azure](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Seleccione o escriba la siguiente información para la nueva cuenta de almacenamiento en el cuadro de diálogo **Crear cuenta de almacenamiento** .

    - La suscripción de Azure al que desea agregar la cuenta de almacenamiento.

    - El nombre que desea usar para la nueva cuenta de almacenamiento.

    - La región o el grupo de afinidad (como oeste de Estados Unidos o Asia oriental).

    - El tipo de replicación que desea usar para la cuenta de almacenamiento, como Geo redundantes.

1. Elija **crear**.

    La nueva cuenta de almacenamiento aparece en la lista de **almacenamiento** en el Explorador de soluciones.

#### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>Para adjuntar una cuenta de almacenamiento existente mediante el Explorador de servidor

1. En el Explorador de servidores, abra el menú contextual para el nodo de almacenamiento de Azure y, a continuación, elija **Adjuntar almacenamiento externo**.

    ![Agregar una cuenta de almacenamiento existente](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)

1. Seleccione o escriba la siguiente información para la nueva cuenta de almacenamiento en el cuadro de diálogo **Crear cuenta de almacenamiento** .

    - El nombre de la cuenta de almacenamiento existente que desea adjuntar. Puede escribir un nombre o selecciónelo en la lista.

    - La clave para la cuenta de almacenamiento seleccionado. Este valor normalmente se proporciona automáticamente cuando se selecciona una cuenta de almacenamiento. Si desea que Visual Studio para recordar la clave de cuenta de almacenamiento, active la casilla de clave de cuenta de recordar.

    - Protocolo a usar para conectarse a la cuenta de almacenamiento, como HTTP, HTTPS o un extremo personalizado. Para obtener más información sobre los extremos personalizados, consulte [cómo configurar las cadenas de conexión](https://msdn.microsoft.com/library/azure/ee758697.aspx) .

### <a name="to-view-the-secondary-endpoints"></a>Para ver los extremos secundarios

- Si ha creado una cuenta de almacenamiento mediante la opción de replicación **Geo redundantes de acceso de lectura** , puede ver sus extremos secundarios. Abrir el menú contextual para el nombre de cuenta y, a continuación, elija **Propiedades**.

    ![Extremos de almacenamiento secundarios](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>Para quitar una cuenta de almacenamiento del explorador de servidores

- En el Explorador de servidores, abra el menú contextual para el nombre de cuenta y, a continuación, elija **Eliminar**. Si elimina una cuenta de almacenamiento, también se quita toda la información clave guardado para la cuenta.

    >[AZURE.NOTE] Si elimina una cuenta de almacenamiento del explorador de servidores, no afecta a su cuenta de almacenamiento o cualquier dato que contenga. simplemente se quita la referencia del explorador de servidores. Para eliminar una cuenta de almacenamiento, use el [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

## <a name="next-steps"></a>Pasos siguientes

Para obtener que más información sobre cómo usar los servicios de almacenamiento de Azure, vea [obtener acceso a los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/ee405490.aspx).
