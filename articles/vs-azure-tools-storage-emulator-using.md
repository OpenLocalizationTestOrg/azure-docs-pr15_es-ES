<properties 
   pageTitle="Configurar y utilizar el emulador almacenamiento con Visual Studio | Microsoft Azure"
   description="Configurar y utilizar el emulador almacenamiento con Visual Studio"
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

# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Configurar y utilizar el emulador almacenamiento con Visual Studio

[AZURE.INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Información general
El entorno de desarrollo de Azure SDK incluye el emulador de almacenamiento, una utilidad que simula los servicios de almacenamiento Blob, colas y tabla disponibles en Azure en el equipo de desarrollo local. Si está creando un servicio de nube que emplea los servicios de almacenamiento de Azure o escribir cualquier aplicación externa que llama a los servicios de almacenamiento, puede probar el código localmente en el emulador de almacenamiento. Las herramientas de Azure para Microsoft Visual Studio integrar Administración del emulador almacenamiento en Visual Studio. Las herramientas de Azure inicializar la base de datos de almacenamiento emulador usarlo por primera vez, se inicia el servicio de almacenamiento emulador al ejecutar o depurar el código de Visual Studio y proporciona acceso de solo lectura a los datos de almacenamiento emulador mediante el Explorador de almacenamiento de Azure.

Para obtener información detallada sobre el emulador de almacenamiento, incluidos los requisitos del sistema y las instrucciones de configuración personalizada, consulte [usar el emulador de almacenamiento de Azure para prueba y desarrollo](./storage/storage-use-emulator.md).

>[AZURE.NOTE] Existen algunas diferencias de funcionalidad entre la simulación de emulador de almacenamiento y los servicios de almacenamiento de Azure. Vea [las diferencias entre el emulador de almacenamiento y servicios de almacenamiento de Azure](./storage/storage-use-emulator.md) en la documentación de Azure SDK para obtener información sobre las diferencias específicas.

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Configurar una cadena de conexión para el emulador de almacenamiento

Para obtener acceso a emulador de almacenamiento de código dentro de una función, desea configurar una cadena de conexión que señala al emulador de almacenamiento y que más tarde puede cambiarse para que apunten a una cuenta de almacenamiento de Azure. Una cadena de conexión es un valor de configuración que puede leer su rol en tiempo de ejecución para conectarse a una cuenta de almacenamiento. Para obtener más información sobre cómo crear cadenas de conexión, consulte [configuración de la aplicación de Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

>[AZURE.NOTE] Puede devolver una referencia a la cuenta de almacenamiento emulador desde el código mediante la propiedad **DevelopmentStorageAccount** . Este método funciona correctamente si desea obtener acceso a emulador de almacenamiento de su código, pero si piensa publicar su aplicación en Azure, debe crear una cadena de conexión para acceder a su cuenta de almacenamiento de Azure y modifique el código para usar esa cadena de conexión antes de publicarla. Si cambia entre la cuenta de emulador de almacenamiento y una cuenta de almacenamiento de Azure con frecuencia, una cadena de conexión simplificará este proceso.

## <a name="initializing-and-running-the-storage-emulator"></a>Iniciando y ejecutando el emulador de almacenamiento

Puede especificar que, al ejecutar o depurar el servicio en Visual Studio, Visual Studio inicia automáticamente el emulador de almacenamiento. En el Explorador de soluciones, abra el menú contextual para el proyecto de **Azure** y elija **Propiedades**. En la pestaña de **desarrollo** , en la lista **Iniciar emulador de almacenamiento de Azure** , elija **True** (si no se ha establecido por ese valor).

La primera vez que ejecute o depurar el servicio de Visual Studio, el emulador almacenamiento inicia un proceso de inicialización. Este proceso reserva puertos locales para el emulador de almacenamiento y crea la base de datos de almacenamiento emulador. Cuando haya finalizado, este proceso no es necesario volver a ejecutar a menos que se elimina la base de datos de almacenamiento emulador.

>[AZURE.NOTE] A partir de la versión de junio de 2012 de las herramientas de Azure, ejecuta el emulador de almacenamiento, de forma predeterminada, en SQL Express LocalDB. En versiones anteriores de las herramientas de Azure, se ejecuta el emulador almacenamiento frente a una instancia predeterminada de SQL Express 2005 o 2008, que debe instalar antes de poder instalar el SDK de Azure. También puede ejecutar el emulador de almacenamiento en una instancia con nombre de SQL Express o un nombre o una instancia predeterminada de Microsoft SQL Server. Si necesita configurar el emulador de almacenamiento para que se ejecute en una instancia distinta de la instancia de forma predeterminada, vea [usar el emulador de almacenamiento de Azure para desarrollo y pruebas](./storage/storage-use-emulator.md).

El emulador de almacenamiento proporciona una interfaz de usuario para ver el estado de los servicios de almacenamiento local y para iniciar, detener y restablecer. Una vez que se ha iniciado el servicio de almacenamiento emulador, puede mostrar la interfaz de usuario o iniciar o detener el servicio haciendo clic en el icono del área de notificación para el emulador de Microsoft Azure en la barra de tareas de Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Ver datos de emulador de almacenamiento en el Explorador de servidores

El nodo de almacenamiento de Azure en el Explorador de servidor le permite ver los datos y cambiar la configuración de datos de tablas y blobs de sus cuentas de almacenamiento, incluidos el emulador de almacenamiento. Para obtener más información, consulte [exploración y administración de recursos de almacenamiento con el Explorador de servidores](https://msdn.microsoft.com/library/azure/ff683677.aspx) .
