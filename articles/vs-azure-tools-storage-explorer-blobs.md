<properties
    pageTitle="Administrar recursos de almacenamiento de blobs de Windows Azure con el Explorador de almacenamiento (vista preliminar) | Microsoft Azure"
    description="Administrar contenedores de blobs de Windows Azure y BLOB con el Explorador de almacenamiento (vista preliminar)"
    services="storage"
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
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="manage-azure-blob-storage-resources-with-storage-explorer-preview"></a>Administrar recursos de almacenamiento de blobs de Windows Azure con el Explorador de almacenamiento (vista preliminar)

## <a name="overview"></a>Información general

[Almacenamiento de blobs de Windows Azure](./storage/storage-dotnet-how-to-use-blobs.md) es un servicio para almacenar grandes cantidades de datos no estructurados, como texto o binario, que puede tener acceso desde cualquier lugar del mundo a través de HTTP o HTTPS.
Puede usar el almacenamiento de blobs para exponer datos públicamente en el mundo, o para almacenar los datos de la aplicación en privado. En este artículo, aprenderá cómo usar el Explorador de almacenamiento (Preview) para trabajar con contenedores de blob y BLOB.

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este artículo, necesitará la siguiente:

- [Descargue e instale el Explorador de almacenamiento (vista preliminar)](http://www.storageexplorer.com)
- [Conectarse a un servicio o cuenta de almacenamiento de Azure](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Crear un contenedor de blob

Todos los blobs deben estar en un contenedor de blob, que es simplemente una agrupación lógica de BLOB. Una cuenta puede contener un número ilimitado de contenedores y cada contenedor puede almacenar una cantidad ilimitada de BLOB.

Los pasos siguientes muestran cómo crear un contenedor de blob en el Explorador de almacenamiento (vista preliminar).

1.  Abra el Explorador de almacenamiento (vista preliminar).
1.  En el panel izquierdo, expanda la cuenta de almacenamiento en la que desea crear el contenedor de blob.
1.  Contextual **Blob contenedores**y - en el menú contextual - seleccionar **Crear contenedor de Blob**.

    ![Crear menú contextual de contenedores de blob][0]

1.  Aparecerá un cuadro de texto debajo de la carpeta **Contenedores de Blob** . Escriba el nombre para el contenedor de blob. Vea la sección [reglas de nomenclatura de contenedor](./storage/storage-dotnet-how-to-use-blobs.md#create-a-container) para ver una lista de reglas y las restricciones en los contenedores de blobs de nomenclatura.

    ![Crear cuadro de texto de contenedores de Blob][1]

1.  Presione **ENTRAR** cuando haya finalizado para crear el contenedor de blob o **Esc** para cancelar. Una vez que se ha creado correctamente el contenedor de blob, se mostrará en la carpeta **Contenedores de blobs** de la cuenta de almacenamiento seleccionado.

    ![Creada un contenedor de BLOB][2]

## <a name="view-a-blob-containers-contents"></a>Ver contenido del contenedor de blob

Contenedores de BLOB contienen BLOB y carpetas (que también se pueden contener BLOB).

Los pasos siguientes muestran cómo ver el contenido de un contenedor de blob en el Explorador de almacenamiento (vista preliminar):

1.  Abra el Explorador de almacenamiento (vista preliminar).
1.  En el panel izquierdo, expanda la cuenta de almacenamiento que contiene el contenedor de blob que desea ver.
1.  Expanda de la cuenta de almacenamiento **Blobs contenedores**.
1.  Haga clic en el contenedor de blob que desea ver y - en el menú contextual - seleccionar **Abrir el Editor de contenedor Blob**.
También puede haga doble clic en el contenedor de blob que desea ver.

    ![Menú contextual de abrir blob contenedor editor][19]

1.  El panel principal mostrará el contenido del contenedor de blob.

    ![Editor de contenedor de BLOB][3]

## <a name="delete-a-blob-container"></a>Eliminar un contenedor de blob

Contenedores de BLOB pueden crear fácilmente y eliminados según sea necesario. (Para ver cómo eliminar BLOB individual, consulte la sección, [Administrar BLOB en un contenedor de blob](./#managing-blobs-in-a-blob-container)).

Los pasos siguientes muestran cómo eliminar un contenedor de blob en el Explorador de almacenamiento (vista preliminar):

1.  Abra el Explorador de almacenamiento (vista preliminar).
1.  En el panel izquierdo, expanda la cuenta de almacenamiento que contiene el contenedor de blob que desea ver.
1.  Expanda de la cuenta de almacenamiento **Blobs contenedores**.
1.  Haga clic en el contenedor de blobs de Windows que desea eliminar y - en el menú contextual - seleccione **Eliminar**.
También puede presionar **Eliminar** para eliminar el contenedor de blob seleccionado actualmente.

    ![Eliminar el menú contextual del contenedor blob][4]

1.  Seleccione **Sí** en el cuadro de diálogo de confirmación.

    ![Eliminar blob confirmación de contenedor][5]

## <a name="copy-a-blob-container"></a>Copiar un contenedor de blob

Explorador de almacenamiento (vista previa) le permite copiar un contenedor de blob en el Portapapeles y, a continuación, pegue ese contenedor blob en otra cuenta de almacenamiento. (Para ver cómo copiar BLOB individual, consulte la sección, [Administrar BLOB en un contenedor de blob](./#managing-blobs-in-a-blob-container)).

Los pasos siguientes muestran cómo copiar un contenedor de blobs de cuenta de almacenamiento de uno a otro.

1.  Abra el Explorador de almacenamiento (vista preliminar).
1.  En el panel izquierdo, expanda la cuenta de almacenamiento que contiene el contenedor de blob que desea copiar.
1.  Expanda de la cuenta de almacenamiento **Blobs contenedores**.
1.  Haga clic en el contenedor de blob que desea copiar y - en el menú contextual - seleccionar el **Contenedor de blobs de copia**.

    ![Menú contextual de copiar blob contenedor][6]

1.  Haga clic en la cuenta de almacenamiento de "destino" deseada en la que desea pegar el contenedor de blob y - en el menú contextual - seleccionar el **Contenedor de blobs de pegar**.

    ![Menú contextual de pegado blob contenedor][7]

## <a name="get-the-sas-for-a-blob-container"></a>Obtener las asociaciones de seguridad de un contenedor de blob

Una [firma de acceso compartido (SA)](./storage/storage-dotnet-shared-access-signature-part-1.md) proporciona acceso delegado a los recursos en su cuenta de almacenamiento.
Esto significa que puede conceder a que un cliente de permisos limitados a objetos de su cuenta de almacenamiento durante un período específico de tiempo y con un conjunto determinado de permisos, sin tener que compartir su teclas de acceso de la cuenta.

Los pasos siguientes muestran cómo crear una SA para un contenedor de blob:

1.  Abra el Explorador de almacenamiento (vista preliminar).
1.  En el panel izquierdo, expanda la cuenta de almacenamiento que contiene el contenedor de blobs de Windows para la que desea obtener una SA.
1.  Expanda de la cuenta de almacenamiento **Blobs contenedores**.
1.  Haga clic en el contenedor de blob deseada y - en el menú contextual - seleccione **Obtener firma de acceso compartido**.

    ![Obtener el menú contextual de SA][8]

1.  En el cuadro de diálogo de **Firma de acceso compartido** , especificar la directiva, fechas de inicio y vencimiento, zona horaria y tener acceso a los niveles que desee para el recurso.

    ![Obtener opciones de SA][9]

1.  Cuando haya terminado de especificar las opciones de SA, seleccione **crear**.

1.  Un segundo cuadro de diálogo de **Firma de acceso de compartido** se mostrará que muestra el contenedor de blob junto con la dirección URL y cadenas que puede usar para tener acceso al recurso de almacenamiento.
Haga clic en **Copiar** junto a la dirección URL que desea copiar al Portapapeles.

    ![Copiar la URL de SA][10]

1.  Cuando haya terminado, seleccione **Cerrar**.

## <a name="manage-access-policies-for-a-blob-container"></a>Administrar directivas de acceso para un contenedor de blob

Los pasos siguientes muestran cómo administrar (agregar y quitar) las directivas para un contenedor de blobs de acceso:

1.  Abra el Explorador de almacenamiento (vista preliminar).
1.  En el panel izquierdo, expanda la cuenta de almacenamiento que contiene el contenedor de blob cuyas directivas de acceso que desea administrar.
1.  Expanda de la cuenta de almacenamiento **Blobs contenedores**.
1.  Seleccione el contenedor de blob deseada y - en el menú contextual - seleccione **Administrar directivas de acceso**.

    ![Administrar el menú contextual de directivas de acceso][11]

1.  El cuadro de diálogo de **Las directivas de acceso** mostrará una lista de las directivas de acceso ya ha creado para el contenedor seleccionado blob.

    ![Opciones de directiva de acceso][12]        

1.  Siga estos pasos, dependiendo de la tarea de administración de la directiva de acceso:

    - **Agregar una nueva directiva de acceso** : seleccione **Agregar**. Una vez generado, el cuadro de diálogo de **Las directivas de acceso** mostrará la directiva de acceso recién agregado (con la configuración predeterminada).
    - **Editar una directiva de acceso** : realice las modificaciones que desee y seleccione **Guardar**.
    - **Quitar una directiva de acceso** - seleccione **Quitar** junto a la directiva de acceso que desea quitar.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Establecer el nivel de acceso del público para un contenedor de blob

De forma predeterminada, todos los contenedores blob se establecen en "Sin acceso público".

Los pasos siguientes muestran cómo especificar un nivel de acceso del público para un contenedor de blob.

1.  Abra el Explorador de almacenamiento (vista preliminar).
1.  En el panel izquierdo, expanda la cuenta de almacenamiento que contiene el contenedor de blob cuyas directivas de acceso que desea administrar.
1.  Expanda de la cuenta de almacenamiento **Blobs contenedores**.
1.  Seleccione el contenedor de blob deseada y - en el menú contextual - seleccione **Establecer el nivel de acceso del público**.

    ![Establecer el menú de contexto de nivel de acceso del público][13]

1.  En el cuadro de diálogo **Establecer nivel de acceso del público contenedor** , especifique el nivel de acceso que desee.

    ![Establecer las opciones de nivel de acceso del público][14]

1.  Seleccione **Aplicar**.

## <a name="managing-blobs-in-a-blob-container"></a>Administrar BLOB en un contenedor de blob

Una vez que ha creado un contenedor de blob, puede cargar un blob a ese contenedor blobs, descargue un blob en el equipo local, abra un blob en su equipo local y mucho más.

Los pasos siguientes muestran cómo administrar el BLOB (y carpetas) dentro de un contenedor de blob.

1.  Abra el Explorador de almacenamiento (vista preliminar).
1.  En el panel izquierdo, expanda la cuenta de almacenamiento que contiene el contenedor de blob que desee administrar.
1.  Expanda de la cuenta de almacenamiento **Blobs contenedores**.
1.  Haga doble clic en el contenedor de blob que desea ver.
1.  El panel principal mostrará el contenido del contenedor de blob.

    ![Contenedor de blobs de Windows Vista][3]

1.  El panel principal mostrará el contenido del contenedor de blob.

1.  Siga estos pasos según la tarea que desea realizar:

    - **Cargar archivos en un contenedor de blob**

        1.  En la barra de herramientas del panel principal, seleccione **cargar**y, a continuación, en **Cargar archivos** en el menú desplegable.

            ![Cargar el menú de archivos][15]

        1.  En el cuadro de diálogo **cargar archivos** , seleccione el botón de puntos suspensivos (**...**) en el lado derecho del cuadro de texto de **archivos** para seleccionar los archivos que desea cargar.

            ![Opciones de archivos de carga][16]

        1.  Especificar el tipo de **tipo Blob**. El artículo [Introducción a almacenamiento de blobs de Windows Azure con .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explica las diferencias entre los distintos tipos de blobs.

        1.  Opcionalmente, especifique una carpeta de destino en el que se cargarán los archivos seleccionados. Si no existe la carpeta de destino, se creará.

        1.  Seleccione **cargar**.

    - **Cargar una carpeta a un contenedor de blob**

        1.  En la barra de herramientas del panel principal, seleccione **cargar**y, a continuación, **Cargar la carpeta** en el menú desplegable.

            ![Carpeta menú cargar][17]

        1.  En el cuadro de diálogo **Cargar carpeta** , seleccione el botón de puntos suspensivos (**...**) en el lado derecho del cuadro de texto **carpeta** para seleccionar la carpeta cuyo contenido desea cargar.

            ![Cargar opciones de carpeta][18]

        1.  Especificar el tipo de **tipo Blob**. El artículo [Introducción a almacenamiento de blobs de Windows Azure con .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explica las diferencias entre los distintos tipos de blobs.

        1.  Opcionalmente, especifique una carpeta de destino en el que se cargará el contenido de la carpeta seleccionada. Si no existe la carpeta de destino, se creará.

        1.  Seleccione **cargar**.

    - **Descargue un blob en el equipo local**

        1.  Seleccione el blob que desea descargar.

        1.  En la barra de herramientas del panel principal, seleccione **Descargar**.

        1.  En el cuadro de diálogo **Especifique dónde desea guardar el blob descargado** , especifique la ubicación donde desea que el blob descargado y el nombre que desea darle.  

        1.  Seleccione **Guardar**.

    - **Abrir un blob en el equipo local**

        1.  Seleccione el blob que desea abrir.

        1.  En la barra de herramientas del panel principal, seleccione **Abrir**.

        1.  El blob se descargará y abre con la aplicación asociada al tipo de archivo subyacente de blobs.

    - **Copiar un blob en el Portapapeles**

        1.  Seleccione el blob que desea copiar.

        1.  En la barra de herramientas del panel principal, haga clic en **Copiar**.

        1.  En el panel izquierdo, vaya a otro contenedor de blob y haga doble clic en él para ver en el panel principal.

        1.  En la barra de herramientas del panel principal, seleccione **Pegar** para crear una copia del objeto binario.

    - **Eliminar un blob**

        1.  Seleccione el blob que desea eliminar.

        1.  En la barra de herramientas del panel principal, seleccione **Eliminar**.

        1.  Seleccione **Sí** en el cuadro de diálogo de confirmación.

## <a name="next-steps"></a>Pasos siguientes

- Ver el [Explorador de almacenamiento más reciente (vista preliminar) notas de versiones y vídeos](http://www.storageexplorer.com).
- Obtenga información sobre cómo [crear aplicaciones con archivos, tablas, colas y Azure BLOB](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png