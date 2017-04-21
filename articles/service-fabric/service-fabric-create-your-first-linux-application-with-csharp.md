<properties
   pageTitle="Crear su primera aplicación de servicio tela en Linux mediante C# | Microsoft Azure"
   description="Crear e implementar una aplicación de servicio tela mediante C#"
   services="service-fabric"
   documentationCenter="csharp"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="csharp"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="subramar"/>


# <a name="create-your-first-azure-service-fabric-application"></a>Crear su primera aplicación tela de servicio de Azure

> [AZURE.SELECTOR]
- [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
- [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)

Servicio tela proporciona SDK para generar servicios en Linux en .NET Core y Java. En este tutorial, veremos cómo crear una aplicación para Linux y generar un servicio mediante C# (.NET núcleo).

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar, asegúrese de que haya [configurado su entorno de desarrollo de Linux](service-fabric-get-started-linux.md). Si usa Mac OS X, puede [configurar un entorno de un solo cuadro Linux en una máquina virtual mediante Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Creación de la aplicación

Una aplicación de servicio tela puede contener uno o más servicios, cada una con una función específica en la entrega de la funcionalidad de la aplicación. El SDK de tela de servicio para Linux incluye un generador de [Yeoman](http://yeoman.io/) que hace que sea fácil para crear su primer servicio y agregar más adelante. Vamos a usar a Yeoman para crear una aplicación con un solo servicio.

1. En un terminal, escriba el comando siguiente para comenzar a crear el scaffolding:`yo azuresfcsharp`

2. Nombre de la aplicación.

3. Elija el tipo de su primer servicio y asígnele el nombre. Para el propósito de este tutorial, elegimos un servicio de Actor confiable.

  ![Generador de servicio tela Yeoman C#][sf-yeoman]

>[AZURE.NOTE] Para obtener más información acerca de las opciones, vea [Introducción al modelo de programación tela de servicio](service-fabric-choose-framework.md).

## <a name="build-the-application"></a>Generar la aplicación

Las plantillas de servicio tela Yeoman incluyen un script de compilación que puede usar para generar la aplicación desde el terminal (después de desplazarse a la carpeta de la aplicación).

  ```bash
 cd myapp 
 ./build.sh 
  ```

## <a name="deploy-the-application"></a>Implementar la aplicación

Una vez creado la aplicación, puede implementar al clúster local mediante la CLI de Azure.

1. Conectar con el clúster de tela de servicio local.

    ```bash
    azure servicefabric cluster connect
    ```

2. Utilice la secuencia de comandos en la plantilla de instalación para copiar el paquete de aplicación en el almacén de imágenes del clúster, registrar el tipo de aplicación y crear una instancia de la aplicación.

    ```bash
    ./install.sh
    ```

3. Abra un explorador y vaya al servicio tela explorador en http://localhost:19080/explorador (reemplazar host local con la dirección IP privada de la máquina virtual si usa a Vagrant en Mac OS X).

4. Expanda el nodo aplicaciones y observe que ahora hay una entrada para el tipo de aplicación y otro para la primera instancia de ese tipo.

## <a name="start-the-test-client-and-perform-a-failover"></a>Inicie al cliente de prueba y realizar una migración tras error

Proyectos de actor no haga nada en su propia. Requieren otro servicio o cliente enviarles mensajes. La plantilla de actor incluye una secuencia de comandos de pruebas sencillas que puede usar para interactuar con el servicio de actor.

1. Ejecute la secuencia de comandos mediante la utilidad de inspección para ver el resultado del servicio de actor.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. En el Explorador de tela de servicio, busque el nodo aloja la réplica principal para el servicio de actor. En la captura de pantalla siguiente, es el nodo 3.

    ![Buscar la réplica principal en el Explorador de tela de servicio][sfx-primary]

3. Haga clic en el nodo que se encuentra en el paso anterior, seleccione **desactivar (reiniciar)** en el menú de acciones. Esta acción reinicia uno de los cinco nodos en el clúster local forzar un error a una réplica secundaria que se ejecuta en otro. Cuando realice esta acción, preste atención a la salida desde el cliente de prueba y observe que el contador sigue aumentando a pesar de la migración tras error.


## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre los actores confiable](service-fabric-reliable-actors-introduction.md)
- [Interacción con clústeres de servicio tela mediante la CLI de Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
