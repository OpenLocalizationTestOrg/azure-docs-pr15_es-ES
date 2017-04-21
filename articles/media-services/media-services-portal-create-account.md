<properties
    pageTitle=" Crear una cuenta de Azure Media Services con el portal de Azure | Microsoft Azure"
    description="Este tutorial le guiará por los pasos de la creación de una cuenta de Azure Media Services con el portal de Azure."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/24/2016"
    ms.author="juliako"/>


# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Crear una cuenta de Azure Media Services con el portal de Azure

> [AZURE.SELECTOR]
- [Portal](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Para completar este tutorial, necesita una cuenta de Azure. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 

El portal de Azure proporciona una manera de crear rápidamente una cuenta de Azure Media Services (AMS). Puede usar su cuenta para acceder a los servicios de medios que le permite almacenar, cifrar, codificar, administrar y transmitir contenido multimedia en Azure. En el momento de crear una cuenta de Media Services, también cree una cuenta de almacenamiento asociado (o utilice una existente) en la misma región geográfica como la cuenta de Media Services.

En este artículo se explica algunos conceptos comunes y se muestra cómo crear una cuenta de Media Services con el portal de Azure.

## <a name="concepts"></a>Conceptos

Obtener acceso a servicios multimedia requiere dos cuentas asociadas:

- Una cuenta de Media Services. Su cuenta le proporciona acceso a un conjunto de servicios de medios en la nube que están disponibles en Azure. Una cuenta de Media Services no almacenar contenido multimedia real. En su lugar almacena metadatos acerca de los trabajos de procesamiento de medios y contenido multimedia en su cuenta. En el momento de que crear la cuenta, seleccione un área de Media Services disponibles. La región que seleccione es un centro de datos que almacena los registros de metadatos para su cuenta.

    Regiones de Media Services (AMS) disponibles incluyen las siguientes: Europa del Norte, Europa occidental, oeste de Estados Unidos, Estados Unidos oriental, sudeste asiático, asiático, oeste Japón Oriente Japón. Media Services no utiliza grupos de afinidad.
    
    AMS ahora también está disponible en los centros de datos siguientes: sur de Brasil, India oeste, India sur y Central de India. Ahora puede usar el portal de Azure para crear cuentas de servicio de medios o realizar diversas tareas que se describen aquí. Sin embargo, la codificación en directo no está habilitada en estos centros de datos. Además, no todos los tipos de codificación reservado unidades están disponibles en estos centros de datos.
    
    - Sur de Brasil: Solo están disponibles los estándar y unidades reservado codificación básicas.
    - India oeste India sur: 

- Una cuenta de almacenamiento de Azure. Cuentas de almacenamiento deben estar ubicadas en la misma región geográfica como la cuenta de Media Services. Cuando se crea una cuenta de Media Services, puede elegir una cuenta existente de almacenamiento en la misma región o puede crear una nueva cuenta de almacenamiento en la misma región. Si elimina una cuenta de Media Services, no se eliminan los BLOB en su cuenta de almacenamiento relacionados.

## <a name="create-an-ams-account"></a>Crear una cuenta de AMS

Los pasos de esta sección muestran cómo crear una cuenta de AMS.

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. Haga clic en **+ nuevo** > **Web + Mobile** > **Media Services**.

    ![Crear Media Services](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. En **Crear cuenta de servicios de medios** escriba valores requeridos.

    ![Crear Media Services](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. En **Nombre de la cuenta**, escriba el nombre de la nueva cuenta de AMS. Un nombre de cuenta de Media Services es todos los números en minúsculas o letras sin espacios y es de 3 a 24 caracteres de longitud.
    2. En la suscripción, seleccione entre las diferentes suscripciones Azure que tienen acceso a.
    
    2. En el **Grupo de recursos**, seleccione el recurso nuevo o existente.  Un grupo de recursos es una colección de recursos que comparten directivas, permisos y ciclo de vida. Más información [aquí](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. En **ubicación**, seleccione la región geográfica que se usará para almacenar los registros de medios y metadatos para su cuenta de Media Services. Esta área se utilizará para procesar y transmita los elementos multimedia. Solo las regiones Media Services disponibles aparecen en el cuadro de lista desplegable. 
    
    3. En la **Cuenta de almacenamiento**, seleccione una cuenta de almacenamiento para proporcionar el almacenamiento de blobs del contenido multimedia desde su cuenta de Media Services. Puede seleccionar una cuenta existente de almacenamiento en la misma región geográfica como su cuenta de Media Services, o puede crear una cuenta de almacenamiento. Se crea una nueva cuenta de almacenamiento en la misma región. Las reglas de nombres de cuenta de almacenamiento son los mismos que las cuentas de Media Services.

        Obtenga más información sobre el almacenamiento [aquí](storage-introduction.md).

    4. Seleccione **Anclar al panel** para ver el progreso de la implementación de la cuenta.
    
7. Haga clic en **crear** en la parte inferior del formulario.

    Una vez que la cuenta se ha creado correctamente, el estado cambia a **está ejecutando**. 

    ![Configuración de servicios de medios](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Para administrar su cuenta AMS (por ejemplo, cargar vídeos, codificar activos, supervisar el progreso de tarea) Utilice la ventana de **configuración** .

## <a name="manage-keys"></a>Administrar las claves

Necesita el nombre de cuenta y la información de clave principal acceso mediante programación a la cuenta de Media Services.

1. En el portal de Azure, seleccione su cuenta. 

    Aparece la ventana de **configuración** de la derecha. 

2. En la ventana **configuración** , seleccione **teclas**. 

    Las ventanas de **administrar claves** muestra el nombre de cuenta y se muestra la clave principal y secundaria. 
3. Presione el botón Copiar para copiar los valores.
    
    ![Servicios multimedia de teclas](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="next-steps"></a>Pasos siguientes

Ahora puedes cargar archivos a su cuenta de AMS. Para obtener más información, vea [cargar archivos](media-services-portal-upload-files.md).

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


