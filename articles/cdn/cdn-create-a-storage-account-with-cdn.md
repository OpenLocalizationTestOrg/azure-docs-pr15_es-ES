<properties
    pageTitle="Integrar una cuenta de almacenamiento con CDN | Microsoft Azure"
    description="Obtenga información sobre cómo usar la red de entrega de contenido (CDN) de Azure para proporcionar contenido de ancho de banda, almacenamiento en caché BLOB desde el almacenamiento de Azure."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>


# <a name="integrate-a-storage-account-with-cdn"></a>Integrar una cuenta de almacenamiento con CDN

CDN puede habilitarse al contenido de la memoria caché de su almacenamiento de Azure. Ofrece a los desarrolladores una solución global para proporcionar contenido de banda ancha por caché BLOB y contenido estático de instancias de cálculo en los nodos físicos en los Estados Unidos, Europa, Asia, Australia y América del sur.


## <a name="step-1-create-a-storage-account"></a>Paso 1: Crear una cuenta de almacenamiento

Use el procedimiento siguiente para crear una nueva cuenta de almacenamiento para una suscripción de Azure. Una cuenta de almacenamiento proporciona acceso a los servicios de almacenamiento de Azure. La cuenta de almacenamiento representa el nivel superior del espacio de nombres para tener acceso a cada uno de los componentes del servicio de almacenamiento de Azure: Blob servicios, servicios de cola y servicios de tabla. Para obtener más información, consulte la [Introducción a Microsoft Azure almacenamiento](../storage/storage-introduction.md).

Para crear una cuenta de almacenamiento, debe ser administrador del servicio o un administrador de colaboración para la suscripción asociada.

> [AZURE.NOTE] Hay varios métodos que puede usar para crear una cuenta de almacenamiento, incluido el Portal de Azure Powershell.  En este tutorial, se va a usar el Portal de Azure.  

**Para crear una cuenta de almacenamiento para una suscripción de Azure**

1.  Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2.  En la esquina superior izquierda, seleccione **nuevo**. En el cuadro de diálogo **nuevo** , seleccione **datos + almacenamiento**, haga clic en **cuenta de almacenamiento**.

    Aparece el módulo **crear cuenta de almacenamiento** .

    ![Crear cuenta de almacenamiento][create-new-storage-account]

4. En el campo **nombre** , escriba un nombre de subdominio. Esta entrada puede contener 3-24 letras minúsculas y números.

    Este valor se convierte en el nombre de host en el URI que se utiliza para dirigir recursos blobs, cola o tabla para la suscripción. Para un recurso de contenedor en el servicio de blobs de direcciones, debe usar un URI en el siguiente formato, donde * &lt;StorageAccountLabel&gt; * hace referencia al valor que escribió en **Escriba una dirección URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **Importante:** La etiqueta de dirección URL el subdominio de la cuenta de almacenamiento URI de formularios y debe ser única entre todos los servicios alojados en Azure.

    Este valor también se usa como el nombre de la cuenta de almacenamiento en el portal de o al obtener acceso a esta cuenta mediante programación.

5. Deje los valores predeterminados para el **modelo de implementación**, el **tipo de cuenta**, el **rendimiento**y **replicación**. 

6. Seleccione la **suscripción** que se utilizará con la cuenta de almacenamiento.

7. Seleccione o cree un **Grupo de recursos**.  Para obtener más información sobre los grupos de recursos, vea [información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md#resource-groups).

8. Seleccione una ubicación para su cuenta de almacenamiento.

8. Haga clic en **crear**. El proceso de creación de la cuenta de almacenamiento podría tardar varios minutos en completarse.


## <a name="step-2-create-a-new-cdn-profile"></a>Paso 2: Crear un nuevo perfil CDN

Un perfil CDN es una colección de extremos CDN.  Cada perfil contiene uno o más extremos CDN.  Puede usar varios perfiles para organizar sus extremos CDN por dominio de internet, aplicación web o cualquier otro criterio.

> [AZURE.TIP] Si ya tiene un perfil CDN que desea usar para este tutorial, vaya al [paso 3](#step-3-create-a-new-cdn-endpoint).

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="step-3-create-a-new-cdn-endpoint"></a>Paso 3: Crear un nuevo extremo CDN

**Para crear un nuevo punto final CDN para su cuenta de almacenamiento**

1. En el [Portal de administración de Azure](https://portal.azure.com), vaya a su perfil CDN.  Se puede ha anclado se al panel en el paso anterior.  Si no es así, puede encontrar, haciendo clic en **Examinar**y luego **perfiles CDN**y en el perfil que se va a agregar el extremo.

    Aparece el módulo de perfil CDN.

    ![Perfil CDN][cdn-profile-settings]

2. Haga clic en el botón **Agregar extremo** .

    ![Agregar botón de punto final][cdn-new-endpoint-button]

    Aparece el módulo de **Agregar un extremo** .

    ![Agregar módulo de punto final][cdn-add-endpoint]

3. Escriba un **nombre** para este extremo CDN.  Este nombre se usará para tener acceso a los recursos almacenados en caché en el dominio `<endpointname>.azureedge.net`.

4. En la lista desplegable **tipo de origen** , seleccione *almacenamiento*.  

5. En la lista desplegable de **nombre de host de origen** , seleccione la cuenta de almacenamiento.

6. Deje los valores predeterminados para la **ruta de acceso de origen**, **encabezado de host de origen**y **puerto de origen o de protocolo**.  Debe especificar al menos un protocolo (HTTP o HTTPS).

    > [AZURE.NOTE] Esta configuración permite que todos los contenedores públicamente visibles en su cuenta de almacenamiento de almacenamiento en caché en la CDN.  Si desea limitar el ámbito a un solo contenedor, utilice la **ruta de acceso de origen**.  Tenga en cuenta que el contenedor debe tener su visibilidad establecer al público.

7. Haga clic en el botón **Agregar** para crear el extremo de nuevo.

8. Una vez creado el extremo, aparece en una lista de los extremos del perfil. La vista de lista muestra la dirección URL para tener acceso a contenido de la caché, así como el dominio de origen.

    ![Extremo CDN][cdn-endpoint-success]

    > [AZURE.NOTE] El extremo no inmediatamente estará disponible para su uso.  Puede tardar hasta 90 minutos para el registro en propagarse a través de la red CDN. Los usuarios que intenten utilizar el nombre de dominio CDN inmediatamente pueden recibir el código de estado 404 hasta que el contenido esté disponible a través de la CDN.


## <a name="step-4-access-cdn-content"></a>Paso 4: Acceso CDN contenido

Para obtener acceso a contenido almacenado en la CDN, use la dirección URL de CDN proporcionados en el portal. La dirección de un blob en caché será similar al siguiente:

http://<*EndpointName*\>.azureedge.net/ <*myPublicContainer*\>/<*BlobName*\>

> [AZURE.NOTE] Después de habilitar el acceso CDN a una cuenta de almacenamiento o servicio alojado, todos los objetos disponibles públicamente son elegibles para CDN almacenamiento en caché. Si modifica un objeto que actualmente se almacena en caché la CDN, el nuevo contenido no estará disponible a través de la CDN hasta que la CDN actualiza su contenido cuando finalice el período de tiempo de vida contenido en caché.

## <a name="step-5-remove-content-from-the-cdn"></a>Paso 5: Quitar el contenido de la CDN

Si ya no desea un objeto en la red de entrega de contenido (CDN) de Azure en caché, puede realizar uno de los siguientes pasos:

-   Puede hacer que el contenedor privado en lugar de público. Para obtener más información, vea [administrar el acceso anónimo lectura contenedores y BLOB](../storage/storage-manage-access-to-resources.md) .
-   Puede deshabilitar o eliminar el extremo CDN mediante el Portal de administración.
-   Puede modificar el servicio hospedado ya no responder a solicitudes para el objeto.

Un objeto ya en caché en la CDN permanecerá en caché hasta que el período de tiempo de vida para el objeto o se elimina el extremo. Cuando expira el período de tiempo de vida, la CDN comprueba si el extremo CDN sigue siendo válido y el objeto todavía accesible de forma anónima. Si no es así, ya no se caché el objeto.


## <a name="additional-resources"></a>Recursos adicionales

-   [Cómo asignar CDN contenido a un dominio personalizado](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png
