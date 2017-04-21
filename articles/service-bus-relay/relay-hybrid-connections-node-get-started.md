<properties
    pageTitle="Introducción a las conexiones de retransmisión híbrido | Microsoft Azure"
    description="Cómo escribir una aplicación de consola de nodo para conexiones híbrido"
    services="service-bus"
    documentationCenter="node"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="node"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="jotaub"/>

# <a name="get-started-with-relay-hybrid-connections"></a>Introducción a las conexiones de retransmisión híbrido

[AZURE.INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>¿Qué llevar a cabo

Puesto que híbrido conexiones requiere un cliente y un componente de servidor, crearemos dos aplicaciones de consola en este tutorial. Estos son los pasos:

1. Crear un espacio de nombres de retransmisión con el portal de Azure.

2. Crear una conexión de híbrido con el portal de Azure.

3. Escribir a un servidor de aplicación de consola para recibir mensajes.

4. Escribir a un cliente de aplicación de consola para enviar mensajes.

## <a name="prerequisites"></a>Requisitos previos

1. [Node.js](https://nodejs.org/en/) (en este ejemplo usa nodo 7.0).

2. Una suscripción de Azure.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. crear un espacio de nombres con el portal de Azure

Si ya tiene un espacio de nombres de retransmisión creado, ir a la sección [crear una conexión híbrido con el portal de Azure](#2-create-a-hybrid-connection-using-the-azure-portal) .

[AZURE.INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. crear una conexión de híbrido con el portal de Azure

Si ya tiene una conexión de híbrido creada, saltar a la sección [crear una aplicación de servidor](#3-create-a-server-application-listener) .

[AZURE.INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. crear una aplicación de servidor (escucha)

Para escuchar y recibir mensajes de la retransmisión, escribimos una aplicación de consola Node.js.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. crear una aplicación de cliente (remitente)

Para enviar mensajes a la retransmisión, escribimos una aplicación de consola Node.js.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. ejecute las aplicaciones

1. Ejecute la aplicación de servidor.

2. Ejecutar la aplicación cliente y escriba el texto.

3. Asegúrese de que la consola de servidor de aplicaciones envía el texto que escribió en la aplicación cliente.

![aplicaciones de ejecución](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Enhorabuena, ha creado una aplicación de conexiones híbrido-to-end.

## <a name="next-steps"></a>Pasos siguientes:

- [Retransmisión de preguntas más frecuentes](relay-faq.md)
- [Crear un espacio de nombres](relay-create-namespace-portal.md)
- [Introducción a .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Introducción a nodo](relay-hybrid-connections-node-get-started.md)