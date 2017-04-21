<properties
   pageTitle="Registrar datos del almacén de datos lago en catálogo de datos de Azure | Microsoft Azure"
   description="Registrar datos del almacén de datos lago en catálogo de datos de Azure"
   services="data-lake-store,data-catalog" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Registrar datos del almacén de datos lago en catálogo de datos de Azure

En este artículo aprenderá cómo integrar lago almacén de datos de Azure con el catálogo de datos de Azure para hacer que los datos reconocible dentro de una organización mediante la integración con el catálogo de datos. Para obtener más información sobre el catálogo de datos, vea el [Catálogo de datos de Azure](../data-catalog/data-catalog-what-is-data-catalog.md). Para conocer los escenarios en los que puede usar el catálogo de datos, vea [escenarios comunes de catálogo de datos de Azure](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

- **Habilitar la suscripción Azure** de vista previa de datos lago almacén público. Vea [las instrucciones](data-lake-store-get-started-portal.md#signup).

- **Cuenta de la tienda de lago de datos de azure**. Siga las instrucciones en [Introducción a Azure datos lago tienda con el Portal de Azure](data-lake-store-get-started-portal.md). En este tutorial, deje que nosotros crear una cuenta de almacén de datos lago denominada **datacatalogstore**. 

    Una vez haya creado la cuenta, cargar un conjunto de datos de ejemplo en ella. En este tutorial, deje que nosotros cargar todos los archivos .csv en la carpeta **AmbulanceData** en el [Repositorio de Azure datos lago Git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Puede usar a varios clientes, como [El Explorador de almacenamiento de Azure](http://storageexplorer.com/), para cargar los datos en un contenedor de blob.

- **Catálogo de datos de azure**. Su organización ya debe tener un catálogo de datos de Azure creado para su organización. Se permite sólo un catálogo para cada organización.

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Almacén de lago de registrar datos como origen de para el catálogo de datos

>[AZURE.VIDEO adcwithadl] 

1. Vaya a `https://azure.microsoft.com/services/data-catalog`y haga clic en **empezar**.

2. Inicie sesión en el portal de catálogo de datos de Azure y haga clic en **publicar datos**.

    ![Registrar un origen de datos] (./media/data-lake-store-with-data-catalog/register-data-source.png "Registrar un origen de datos")

3. En la siguiente página, haga clic en **Iniciar aplicación**. Esto Descargue el archivo de manifiestos de aplicación en el equipo. Haga doble clic en el archivo manifiesto para iniciar la aplicación.

4. En la página principal, haga clic en **iniciar sesión**e introduzca sus credenciales.

    ![Pantalla de bienvenida] (./media/data-lake-store-with-data-catalog/welcome.screen.png "Pantalla de bienvenida")

5. En la página Seleccionar un origen de datos, seleccione **Lago de datos de Azure**y, a continuación, haga clic en **siguiente**.

    ![Seleccione el origen de datos] (./media/data-lake-store-with-data-catalog/select-source.png "Seleccione el origen de datos")

6. En la página siguiente, especifique el nombre de almacén de lago de datos que desea registrar en el catálogo de datos. Deje el resto de opciones como predeterminado y, a continuación, haga clic en **Conectar**.

    ![Conectarse al origen de datos] (./media/data-lake-store-with-data-catalog/connect-to-source.png "Conectarse al origen de datos")

7. La siguiente página se puede dividir en los siguientes segmentos.

    una. El cuadro **Servidor jerarquía** representa la estructura de carpetas de la cuenta de almacén de datos lago. **$Root** representa la raíz de la cuenta de almacén de datos lago y **AmbulanceData** la carpeta creada en la raíz de la cuenta de almacén de datos lago.

    b. El cuadro **objetos disponibles** enumera los archivos y carpetas en la carpeta **AmbulanceData** .

    c. **Objetos sean registrado cuadro** enumera los archivos y carpetas que desea registrar en el catálogo de datos de Azure.

    ![Estructura de datos de vista] (./media/data-lake-store-with-data-catalog/view-data-structure.png "Estructura de datos de vista")

8. En este tutorial, debe registrar todos los archivos en el directorio. Para ello, haga clic en el botón (![mover objetos](./media/data-lake-store-with-data-catalog/move-objects.png "mover objetos")) para mover todos los archivos al cuadro **objetos a registrar** . 

    Dado que los datos se registrará en un catálogo de datos de toda la organización, es un enfoque recomienda agregar algunos metadatos que puede utilizar más adelante para localizar rápidamente los datos. Por ejemplo, puede agregar una dirección de correo electrónico para el propietario de datos (por ejemplo, uno que se está cargando los datos) o agregar una etiqueta para identificar los datos. Debajo de la captura de pantalla muestra una etiqueta que se agregan a los datos.

    ![Estructura de datos de vista] (./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Estructura de datos de vista")

    Haga clic en **registrarse**.

8. La siguiente captura de pantalla indica que los datos correctamente está registrados en el catálogo de datos.

    ![Registro completado] (./media/data-lake-store-with-data-catalog/registration-complete.png "Estructura de datos de vista")

9. Haga clic en el **Portal de vista** para volver al portal de catálogo de datos y comprobar que es ahora pueden acceder a los datos registrados desde el portal. Para buscar los datos, puede usar la etiqueta utilizada al registrar los datos.

    ![Buscar datos en el catálogo] (./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Buscar datos en el catálogo")

10. Ahora puede realizar operaciones como agregar anotaciones y la documentación a los datos. Para obtener más información, vea los siguientes vínculos.
    * [Realizar anotaciones en orígenes de datos en el catálogo de datos](../data-catalog/data-catalog-how-to-annotate.md)
    * [Orígenes de datos de documento en el catálogo de datos](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Vea también

* [Realizar anotaciones en orígenes de datos en el catálogo de datos](../data-catalog/data-catalog-how-to-annotate.md)
* [Orígenes de datos de documento en el catálogo de datos](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrar el almacén de datos de lago con otros servicios de Azure](data-lake-store-integrate-with-other-services.md)
