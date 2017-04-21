<properties
    pageTitle="Introducción a Azure almacenamiento de cinco minutos | Microsoft Azure"
    description="Se ponerse rápidamente en Microsoft Azure BLOB, tabla y colas con Azure almacenamiento rápido se inicia, Visual Studio y el almacenamiento de Azure emulador. Ejecute la aplicación de almacenamiento de Azure primera en cinco minutos."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="get-started-with-azure-storage-in-five-minutes"></a>Introducción a Azure almacenamiento de cinco minutos

## <a name="overview"></a>Información general

Es fácil Introducción al desarrollo con el almacenamiento de Azure. En este tutorial se muestra cómo obtener una aplicación de almacenamiento de Azure hacia arriba y en marcha rápidamente. Deberá usar las plantillas de inicio rápido, incluidas en el SDK de Azure para .NET. Estos tutoriales rápidos contienen código de listos para ejecutarse que muestra algunos escenarios de programación básicas con el almacenamiento de Azure.

Para obtener más información sobre el almacenamiento de Azure antes de comenzar con el código, vea [Pasos siguientes](#next-steps).

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar, tendrá los siguientes requisitos previos:

1. Para compilar y generar la aplicación, es necesario disponer de una versión de [Visual Studio](https://www.visualstudio.com/) instalada en el equipo.

2. Instalar la última versión de [Azure SDK para .NET](https://azure.microsoft.com/downloads/). El SDK incluye los proyectos de muestra de Azure tutorial, el emulador de almacenamiento de Azure y la [Biblioteca de cliente de almacenamiento de Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

3. Asegúrese de que tiene [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) instalado en su equipo, que es necesaria para los proyectos de muestra de Azure tutorial que se usará en este tutorial.

    Si no está seguro de qué versión de .NET Framework está instalada en su equipo, vea [Cómo: determinar que .NET Framework están instaladas versiones](https://msdn.microsoft.com/vstudio/hh925568.aspx). O bien, presione el botón **Inicio** o la tecla Windows, escriba **Panel de Control**. A continuación, haga clic en **programas** > **programas y características**y determine si .NET Framework 4.5 aparece entre los programas instalados.

4. Necesitará una suscripción de Azure y una cuenta de almacenamiento de Azure.

    - Para obtener una suscripción de Azure, consulte [Prueba gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opciones de compra](https://azure.microsoft.com/pricing/purchase-options/)y [Miembro ofrece](https://azure.microsoft.com/pricing/member-offers/) (para los miembros de MSDN, Microsoft Partner Network, BizSpark y otros programas de Microsoft).
    - Para crear una cuenta de almacenamiento en Azure, consulte [cómo crear una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account).

## <a name="run-your-first-azure-storage-application-against-azure-storage-in-the-cloud"></a>Ejecute la aplicación de almacenamiento de Azure primera contra el almacenamiento de Azure en la nube

Una vez que tenga una cuenta, puede crear una aplicación de almacenamiento de Azure sencilla mediante uno de los proyectos de ejemplo inicia rápida de Azure en Visual Studio. En este tutorial se centra en los proyectos de ejemplo para el almacenamiento de Azure: **almacenamiento de Azure: BLOB**, **almacenamiento de Azure: archivos**, **el almacenamiento de Azure: colas**, y **el almacenamiento de Azure: tablas**:

1. Inicie Visual Studio.
2. En el menú **archivo** , haga clic en **Nuevo proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto** , haga clic en **instalar** > **plantillas** > **Visual C#** > **nube** > **tutoriales** > **Servicios de datos**.
    una. Elija una de las siguientes plantillas: **almacenamiento de Azure: BLOB**, **el almacenamiento de Azure: archivos**, **el almacenamiento de Azure: colas**, o **almacenamiento de Azure: tablas**.
    b. Asegúrese de que **.NET Framework 4.5** está seleccionado como el marco de destino.
    - 3.c. Especifique un nombre para el proyecto y crear la nueva solución de Visual Studio, como se muestra:

    ![Azure guías de inicio rápidos][Image1]

Desea revisar el código fuente antes de ejecutar la aplicación. Para revisar el código, seleccione **Explorador de soluciones** en el menú **Ver** , en Visual Studio. A continuación, haga doble clic en el archivo Program.cs.

A continuación, ejecute la aplicación de ejemplo:

1.  En Visual Studio, seleccione **Explorador de soluciones** en el menú **Ver** . Abra el archivo App.config y comente la cadena de conexión para el emulador de almacenamiento de Azure:

    `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.  Quite los comentarios de la cadena de conexión al servicio de almacenamiento de Azure y proporcione la clave de nombre y el acceso de la cuenta de almacenamiento en el archivo App.config:`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

    Para recuperar la tecla de acceso de la cuenta de almacenamiento, consulte [administrar las claves de acceso de almacenamiento](storage-create-storage-account.md#manage-your-storage-access-keys).

3.  Después de proporcionar el nombre de la cuenta de almacenamiento y tecla de acceso en el archivo App.config, en el menú **archivo** , haga clic en **Guardar todo** para guardar todos los archivos de proyecto.
4.  En el menú **Generar** , haga clic en **Generar solución**.
5.  En el menú **Depurar** , presione **F11** para ejecutar la solución paso a paso o presione **F5** para ejecutar la solución.


## <a name="run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator"></a>Ejecutar la aplicación de almacenamiento de Azure primera localmente en el emulador de almacenamiento de Azure

El [Emulador de almacenamiento de Azure](storage-use-emulator.md) proporciona un entorno local que emula los servicios de blobs de Windows Azure, la cola y la tabla para fines de desarrollo. Puede usar el emulador de almacenamiento para probar la aplicación de almacenamiento localmente, sin necesidad de crear una cuenta de almacenamiento o suscripción de Azure y evitando cualquier costo.

Para probarlo, vamos a crear una aplicación de almacenamiento de Azure sencilla mediante uno de los proyectos de ejemplo inicia rápida de Azure en Visual Studio. En este tutorial se centra en los proyectos de muestra de **Almacenamiento de blobs de Windows Azure**, **Almacenamiento de tablas de Azure**y **Azure almacenamiento de cola** :

1. Inicie Visual Studio.
2. En el menú **archivo** , haga clic en **Nuevo proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto** , haga clic en **instalar** > **plantillas** > **Visual C#** > **nube** > **tutoriales** > **Servicios de datos**.
    una. Elija una de las siguientes plantillas: **almacenamiento de Azure: BLOB**, **el almacenamiento de Azure: archivos**, **el almacenamiento de Azure: colas**, o **almacenamiento de Azure: tablas**.
    b. Asegúrese de que **.NET Framework 4.5** está seleccionado como el marco de destino.
    c. Especifique un nombre para el proyecto y crear la nueva solución de Visual Studio, como se muestra:

    ![Azure guías de inicio rápidos][Image1]

4.  En Visual Studio, seleccione **Explorador de soluciones** en el menú **Ver** . Si ya ha agregado uno, abra el archivo App.config y comente la cadena de conexión para su cuenta de almacenamiento de Azure. A continuación, quite los comentarios de la cadena de conexión para el emulador de almacenamiento de Azure:

    `<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

Desea revisar el código fuente antes de ejecutar la aplicación. Para revisar el código, seleccione **Explorador de soluciones** en el menú **Ver** , en Visual Studio. A continuación, haga doble clic en el archivo Program.cs.

A continuación, ejecute la aplicación de ejemplo en el emulador de almacenamiento de Azure:

1.  Presione el botón **Inicio** o la tecla de Windows, *Microsoft Azure almacenamiento emulador*, buscar e iniciar la aplicación. Cuando se inicia el emulador, verá un icono y una notificación en el área de vista de tareas de Windows.
2.  En Visual Studio, haga clic en **Generar solución** en el menú **Generar** .
3.  En el menú **Depurar** , presione **F11** para ejecutar la solución paso a paso o presione **F5** para ejecutar la solución de principio a fin.

## <a name="next-steps"></a>Pasos siguientes

Vea estos recursos para obtener más información sobre el almacenamiento de Azure:

* [Introducción al almacenamiento de Azure](storage-introduction.md)
* [Empezar a trabajar con el Explorador de almacenamiento de Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Introducción a almacenamiento de blobs de Windows Azure con .NET](storage-dotnet-how-to-use-blobs.md)
* [Introducción a almacenamiento de tablas de Azure con .NET](storage-dotnet-how-to-use-tables.md)
* [Introducción a Azure almacenamiento de cola con .NET](storage-dotnet-how-to-use-queues.md)
* [Introducción a almacenamiento de archivos de Azure en Windows](storage-dotnet-how-to-use-files.md)
* [Transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md)
* [Documentación de almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Biblioteca de cliente de Microsoft Azure almacenamiento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [API de REST de servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
