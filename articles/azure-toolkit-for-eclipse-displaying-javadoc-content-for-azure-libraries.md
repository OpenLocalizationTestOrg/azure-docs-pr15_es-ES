<properties
    pageTitle="Mostrar el contenido de Javadoc en Eclipse para el paquete de Azure bibliotecas de Java"
    description="Cómo mostrar el contenido de Javadoc para las bibliotecas de Azure en Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>Mostrar el contenido de Javadoc en Eclipse para el paquete de Azure bibliotecas de Java #

Se pueden visualizar el contenido de Javadoc para las bibliotecas de Azure para Java dentro de su entorno Eclipse asociando el contenido de Javadoc a las bibliotecas de Azure para Java. Los pasos siguientes muestran cómo usar esta funcionalidad en Eclipse.

Este procedimiento supone que ya ha agregado la biblioteca de Azure para Java a la ruta de acceso de compilación.

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>Para mostrar el contenido de Javadoc en Eclipse para las bibliotecas de Azure para Java ##

* En el Explorador de proyectos de Eclipse, en la sección **Referencia a bibliotecas** de su proyecto, abrir el menú contextual que para la biblioteca de Azure para JAR de Java. Por ejemplo, **microsoft-windowsazure-api-0.1.0.jar** (el número de versión puede ser diferente, depende de qué versión que ha instalado).
* Haga clic en **Propiedades**.
* En el cuadro de diálogo de **Propiedades** , en el panel izquierdo, haga clic en **Ubicación de Javadoc**. Se muestra el cuadro de diálogo **Ubicación de Javadoc** .
* Puede especificar una **Dirección URL de Javadoc**o un **Javadoc en archivo**.
    * Si decide especificar una **Dirección URL de Javadoc**, use las direcciones URL como **http://dl.windowsazure.com/javadoc** o **http://dl.windowsazure.com/storage/javadoc**.
    * Si decide usar **Javadoc en archivo**, puede especificar un archivo externo o un archivo de área de trabajo.
    Asegúrese de su elección y examinar o validar según sea necesario. En el ejemplo siguiente se asocia a las bibliotecas de Azure para Java el JAR Javadoc correspondiente a la que se ha descargado localmente en una carpeta denominada **c:\MyAzureJARs**.
    ![][ic553487]
* *Opcional*: haga clic en **Validar**. Posibles problemas con la Javadoc JAR podrían mostrarse aquí.
* Haga clic en **Aceptar**.

Una vez asociada a la biblioteca, el contenido de Javadoc debe mostrar su IDE Eclipse. Por ejemplo, si `blob` se define del tipo `CloudBlockBlob` dentro del código, el siguiente es un ejemplo de contenido de Javadoc que aparece cuando se escribe `blob.acquireLease` en el código:

![][ic553488]

## <a name="see-also"></a>Vea también ##

[Kit de herramientas de Azure para Eclipse][]

[Crear una aplicación del mundo Hola para Azure en Eclipse][]

[Instalar el Kit de herramientas de Azure Eclipse][] 

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Crear una aplicación del mundo Hola para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalar el Kit de herramientas de Azure Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png