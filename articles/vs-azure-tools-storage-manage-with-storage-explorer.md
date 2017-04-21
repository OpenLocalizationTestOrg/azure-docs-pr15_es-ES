<properties
    pageTitle="Introducción con el Explorador de almacenamiento (vista preliminar) | Microsoft Azure"
    description="Administrar recursos de almacenamiento de Azure con el Explorador de almacenamiento (vista preliminar)"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="getting-started-with-storage-explorer-preview"></a>Introducción con el Explorador de almacenamiento (vista preliminar)

## <a name="overview"></a>Información general 

Explorador de Microsoft Azure almacenamiento (vista preliminar) es una aplicación independiente que le permite trabajar fácilmente con los datos de almacenamiento de Azure en Windows, OS X y Linux. En este artículo, aprenderá las diversas maneras de conectarse y administrar las cuentas de almacenamiento de Azure.

![Explorador de almacenamiento de Microsoft Azure (vista preliminar)][15]

## <a name="prerequisites"></a>Requisitos previos

- [Descargue e instale el Explorador de almacenamiento (vista preliminar)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Conectarse a un servicio o cuenta de almacenamiento

Explorador de almacenamiento (vista preliminar) proporciona un innumerables formas de conectarse a cuentas de almacenamiento. Esto incluye conexión a cuentas de almacenamiento asociadas con las suscripciones de Azure, conectarse a cuentas de almacenamiento y servicios compartidos de otras suscripciones Azure e incluso conectar y administrar almacenamiento local de uso del almacenamiento de Azure:

- [Conectarse a una suscripción de Azure](#connect-to-an-azure-subscription) - administrar recursos de almacenamiento que pertenecen a su suscripción de Azure.
- [Trabajar con el almacenamiento de desarrollo local](#work-with-local-development-storage) - administrar almacenamiento local de uso del almacenamiento de Azure. 
- [Adjuntar a almacenamiento externo](#attach-or-detach-an-external-storage-account) - administrar recursos de almacenamiento que pertenecen a otra suscripción Azure con nombre de la cuenta y la clave de la cuenta de almacenamiento.
- [Cuenta de almacenamiento adjuntar con SA](#attach-storage-account-using-sas) - administrar recursos de almacenamiento que pertenecen a otra suscripción Azure con una SA.
- [Servicio de adjuntar usa SA](#attach-service-using-sas) - administrar un servicio de almacenamiento específico (contenedor de blob, cola o tabla) que pertenecen a otra suscripción Azure con una SA.

## <a name="connect-to-an-azure-subscription"></a>Conectarse a una suscripción de Azure

> [AZURE.NOTE] Si no tiene una cuenta de Azure, puede [registrarse para una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. En el Explorador de almacenamiento (vista preliminar), seleccione **configuración de la cuenta de Azure**. 

    ![Configuración de la cuenta de Azure][0]

1. Ahora, el panel izquierdo mostrará todas las cuentas de Microsoft que ha iniciado sesión en. Para conectarse a otra cuenta, seleccione **Agregar una cuenta**y a continuación, siga los cuadros de diálogo Iniciar sesión con una cuenta de Microsoft que está asociada con al menos una suscripción activa de Azure.

    ![Agregar una cuenta][1]

1. Una vez que inicie sesión correctamente con una cuenta de Microsoft, el panel izquierdo se llenará con las suscripciones de Azure asociadas con esa cuenta. Seleccione las suscripciones de Azure con los que desea trabajar y, a continuación, seleccione **Aplicar**. (Seleccionar alterna **todas las suscripciones para** seleccionar todas o ninguna de las suscripciones de Azure enumeradas).

    ![Seleccione suscripciones de Azure][3]

1. Ahora, el panel izquierdo mostrará las cuentas de almacenamiento asociadas a las suscripciones de Azure seleccionadas.

    ![Suscripciones de Azure seleccionadas][4]

## <a name="work-with-local-development-storage"></a>Trabajar con el almacenamiento de desarrollo local

Explorador de almacenamiento (vista previa) le permite trabajar con almacenamiento local de uso del almacenamiento de Azure. Esto le permite escribir código contra y probar el almacenamiento sin necesidad de tener una cuenta de almacenamiento implementada en Azure (desde la cuenta de almacenamiento que se está emula el emulador de almacenamiento de Azure).

>[AZURE.NOTE] El emulador de almacenamiento de Azure es compatible actualmente solo para Windows. 

1. En el panel izquierdo del explorador de almacenamiento (vista preliminar), expanda la **(Local y adjuntas** > **Cuentas de almacenamiento** > nodo**(Development)** .

    ![Nodo de desarrollo local][21]

1. Si todavía no ha instalado el emulador de almacenamiento de Azure, le pedirá que hacerlo a través de una barra de información. Si se muestra la barra de información, seleccione **Descargar la versión más reciente**e instale el emulador. 

    ![Descargar pregunta emulador de almacenamiento de Azure][22]

1. Una vez instalado el emulador, tendrá la capacidad de crear y trabajar con tablas, colas y BLOB local. Para obtener información sobre cómo trabajar con cada tipo de cuenta de almacenamiento, seleccione el vínculo correspondiente a continuación:

    - [Administrar recursos de almacenamiento de blobs de Windows Azure](./vs-azure-tools-storage-explorer-blobs.md)
    - Administrar recursos de almacenamiento de Azure archivo compartir - *próximamente*
    - Administrar recursos de almacenamiento de Azure cola - *próximamente*
    - Administrar recursos de almacenamiento de tablas de Azure - *próximamente*

## <a name="attach-or-detach-an-external-storage-account"></a>Asociar o desasociar una cuenta de almacenamiento externo

Explorador de almacenamiento (vista preliminar) proporciona la capacidad para adjuntar a cuentas de almacenamiento externo para que se pueden compartir fácilmente cuentas de almacenamiento. En esta sección se explica cómo adjuntar a (y desasociar) cuentas de almacenamiento externo.

### <a name="get-the-storage-account-credentials"></a>Obtener las credenciales de cuenta de almacenamiento

Para compartir una cuenta de almacenamiento externo, el propietario de la cuenta debe primero obtener las credenciales - nombre de la cuenta y clave - para la cuenta y, a continuación, compartir esa información con la persona que desea adjuntar a esa cuenta (externa). Obtener las credenciales de cuenta de almacenamiento puede realizarse a través del portal de Azure siguiendo estos pasos: 

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com).
1.  Seleccione **Examinar**.
1.  Seleccione **cuentas de almacenamiento**.
1.  En el módulo de **Cuentas de almacenamiento** , seleccione la cuenta de almacenamiento deseada.
1.  En el módulo de **configuración** de la cuenta de almacenamiento seleccionado, seleccione **las teclas de acceso**.

    ![Opción de las teclas de acceso][5]
    
1.  En el módulo de **teclas de acceso** , copie los valores de **Nombre de la cuenta de almacenamiento** y **1 de clave** para usar cuando se conecta a la cuenta de almacenamiento. 

    ![Teclas de acceso][6]

### <a name="attach-to-an-external-storage-account"></a>Asociar a una cuenta de almacenamiento externo
Para adjuntar a una cuenta de almacenamiento externo, tendrá el nombre y la clave de la cuenta. La sección *obtener las credenciales de cuenta de almacenamiento* explica cómo obtener estos valores desde el portal de Azure. Sin embargo, tenga en cuenta que, en el portal, la clave de cuenta se denomina "clave 1" por tanto en el Explorador de almacenamiento (vista previa) le pide una clave de cuenta, deberá escribir (o pegar) el valor de "clave 1". 
 
1.  En el Explorador de almacenamiento (vista preliminar), seleccione **Conectar con el almacenamiento de Azure**.

    ![Conectar con la opción de almacenamiento de Azure][23]

1.  En el cuadro de diálogo **conectarse al almacenamiento de Azure** , especifique la clave de cuenta ("clave 1" valor desde el portal de Azure) y, a continuación, seleccione **siguiente**.

    ![Conectarse al cuadro de diálogo de almacenamiento de Azure][24] 

1.  En el cuadro de diálogo **Adjuntar almacenamiento externo** , escriba el nombre de la cuenta de almacenamiento en el cuadro **nombre de cuenta** , especifique la configuración que desee y seleccione **siguiente** cuando haya terminado. 

    ![Adjuntar el cuadro de diálogo de almacenamiento externo][8]

1.  En el cuadro de diálogo **Resumen de conexión** , compruebe la información. Si desea cambiar nada, seleccione **Atrás** y vuelva a escribir la configuración que desee. Una vez que haya terminado, seleccione **Conectar**.

1.  Una vez conectado, se mostrará la cuenta de almacenamiento externo con el texto **(externo)** anexado al nombre de la cuenta de almacenamiento. 

    ![Resultado de la conexión a una cuenta de almacenamiento externo][9]

### <a name="detach-from-an-external-storage-account"></a>Desasociar desde una cuenta de almacenamiento externo

1.  Haga clic en la cuenta de almacenamiento externo que desea desconectar y - en el menú contextual - seleccione **separar**.

    ![Desasociar de la opción de almacenamiento][10]

1.  Cuando aparezca el cuadro de mensaje de confirmación, seleccione **Sí** para confirmar la desasociación desde la cuenta de almacenamiento externo.

## <a name="attach-storage-account-using-sas"></a>Adjuntar la cuenta de almacenamiento mediante SA

Una [sa (firma de acceso compartido)](storage/storage-dotnet-shared-access-signature-part-1.md) le ofrece la administración de una suscripción de Azure la capacidad para conceder acceso a una cuenta de almacenamiento de forma temporal sin tener que proporcionar sus credenciales de suscripción de Azure. 

Para ilustrar esto, supongamos que el usuario es un administrador de una suscripción de Azure y el usuario desea permitir al usuario b tener acceso a una cuenta de almacenamiento durante un tiempo limitado con determinados permisos:

1. El usuario genera una SA (formado por la cadena de conexión para la cuenta de almacenamiento) durante un período de tiempo específico y con los permisos que desee.
1. El usuario a comparte las asociaciones de seguridad con la persona que desee tener acceso a la cuenta de almacenamiento - usuario b, en nuestro ejemplo.  
1. Usuario b utiliza el Explorador de almacenamiento (Preview) para adjuntar a la cuenta de usuario mediante las asociaciones de seguridad suministrado. 

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>Obtener una SA para la cuenta que desea compartir

1.  En el Explorador de almacenamiento (vista previa), haga clic en la cuenta de almacenamiento que desee compartir y - en el menú contextual - seleccione **Obtener firma de acceso compartido**.

    ![Obtener la opción de menú contextual de SA][13]

1. En el cuadro de diálogo de **Firma de acceso compartido** , especifique el período de tiempo y los permisos que desee para la cuenta y seleccione **crear**.

    ![Cuadro de diálogo de SA][14]
 
1. Un segundo cuadro de diálogo de **Firma de acceso de compartido** aparecerá muestra las asociaciones de seguridad. Haga clic en **Copiar** al lado de la **Cadena de conexión** para copiar al Portapapeles. Seleccione **Cerrar** para cerrar el cuadro de diálogo.

### <a name="attach-to-the-shared-account-using-the-sas"></a>Adjuntar a la cuenta compartida con las asociaciones de seguridad

1.  En el Explorador de almacenamiento (vista preliminar), seleccione **Conectar con el almacenamiento de Azure**.

    ![Conectar con la opción de almacenamiento de Azure][23]

1.  En el cuadro de diálogo **conectarse al almacenamiento de Azure** , especifique la cadena de conexión y, a continuación, seleccione **siguiente**.

    ![Conectarse al cuadro de diálogo de almacenamiento de Azure][24] 

1.  En el cuadro de diálogo **Resumen de conexión** , compruebe la información. Si desea cambiar nada, seleccione **Atrás** y vuelva a escribir la configuración que desee. Una vez que haya terminado, seleccione **Conectar**.

1.  Una vez conectada, se mostrará la cuenta de almacenamiento con el texto (SA) anexado al nombre de cuenta que ha suministrado.

    ![Resultado de adjuntos a una cuenta usando SA][17]

## <a name="attach-service-using-sas"></a>Adjuntar SA de servicio

La sección [cuenta de almacenamiento adjuntar con SA](#attach-storage-account-using-sas) muestra cómo un administrador de Azure suscripción puede conceder acceso temporal a una cuenta de almacenamiento generando (y uso compartido) una SA para la cuenta de almacenamiento. Asimismo, puede generarse una SA para un servicio específico (contenedor de blob, cola o tabla) dentro de una cuenta de almacenamiento.  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>Generar una SA para el servicio que desea compartir

En este contexto, un servicio puede ser un contenedor de blob, cola o tabla. Las siguientes secciones explican cómo generar las asociaciones de seguridad para el servicio de lista:

- [Obtener las asociaciones de seguridad de un contenedor de blob](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- Obtener las asociaciones de seguridad de un archivo compartido - *próximamente*
- Obtener las asociaciones de seguridad de una cola - *próximamente*
- Ver las asociaciones de seguridad de una tabla - *próximamente*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>Adjuntar al servicio cuenta compartida con las asociaciones de seguridad

1.  En el Explorador de almacenamiento (vista preliminar), seleccione **Conectar con el almacenamiento de Azure**.

    ![Conectar con la opción de almacenamiento de Azure][23]

1.  En el cuadro de diálogo **conectarse al almacenamiento de Azure** , especifique el URI de SAS y, a continuación, seleccione **siguiente**.

    ![Conectarse al cuadro de diálogo de almacenamiento de Azure][24] 

1.  En el cuadro de diálogo **Resumen de conexión** , compruebe la información. Si desea cambiar nada, seleccione **Atrás** y vuelva a escribir la configuración que desee. Una vez que haya terminado, seleccione **Conectar**.

1.  Una vez conectada, el servicio recién adjunto se mostrará bajo el nodo **(Servicio SA)** .

    ![Resultado de asociar a un servicio compartido con SA][20]

## <a name="search-for-storage-accounts"></a>Búsqueda de cuentas de almacenamiento

Si tiene una lista larga de cuentas de almacenamiento, una forma rápida de buscar una cuenta de almacenamiento en particular es usar el cuadro de búsqueda en la parte superior del panel izquierdo. 

Mientras está escribiendo en el cuadro de búsqueda, el panel izquierdo mostrará sólo las cuentas de almacenamiento que coinciden con el valor de búsqueda que haya escrito hasta que apuntan. La siguiente captura de pantalla muestra un ejemplo donde He buscado todas las cuentas de almacenamiento, donde el nombre de la cuenta de almacenamiento contiene el texto "tarcher".

![Búsqueda de la cuenta de almacenamiento][11]
    
Para borrar la búsqueda, seleccione el botón **x** en el cuadro de búsqueda.

## <a name="next-steps"></a>Pasos siguientes
- [Administrar recursos de almacenamiento de blobs de Windows Azure con el Explorador de almacenamiento (vista preliminar)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
