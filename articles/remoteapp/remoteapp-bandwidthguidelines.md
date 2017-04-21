<properties 
    pageTitle="Azure RemoteApp ancho de banda - directrices generales | Microsoft Azure"
    description="Comprender algunas reglas de ancho de banda de red básica para las aplicaciones y sus colecciones de RemoteApp de Azure."
    services="remoteapp"
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />
    
# <a name="azure-remoteapp-network-bandwidth---general-guidelines-if-you-cant-test-your-own"></a>Azure RemoteApp ancho de banda - directrices generales (si no puede probar su propio)

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Si no tiene la hora o la capacidad para ejecutar las [pruebas de ancho de banda de red](remoteapp-bandwidthtests.md) para RemoteApp de Azure, aquí tiene algunas instrucciones bastante genéricas que pueden ayudarle a estimar el ancho de banda por usuario.

Si tiene una mezcla de estos casos, no es recomendable nada menor que (o igual que) 10 MB/s como el ancho de banda de red mínimo para aplicaciones moderna conectado a Internet en un entorno remoto. (Aunque, como se indicó, esto no garantiza una mejor que la experiencia del usuario promedio.)

## <a name="complex-powerpoint-simple-powerpoint"></a>PowerPoint compleja, PowerPoint simple

Azure RemoteApp funciona mejor en LAN de 100 MB. En el perfil de red de 10 MB/s, cuando vibración encima 120 ms es más del 5%, el usuario verá una experiencia promedio. A 1 MB/s los diferentes es obvios - por ejemplo, en una presentación con diapositivas, el usuario que no vea transiciones animadas en absoluto porque marcos se pasan por alto.

## <a name="internet-explorer-mixed-pdf-pdf-text"></a>Internet Explorer, mixto PDF, PDF, texto

Perfil de red de 10 MB/s está cerca LAN en la mayoría de los aspectos. 1 MB/s le proporcionará una experiencia de Aceptar, aunque puede haber cierta vibración cuando un usuario se desplaza mientras hay imágenes en la pantalla.

## <a name="flash-video-youtube"></a>Flash video (YouTube)

100 MB/s LAN proporciona la mejor experiencia, mientras que 10 MB/s es aceptable (es decir, mantener la velocidad de marco, pero aumenta de vibración). A 1 MB/s, vibración es muy alta y perceptibles.

## <a name="word-typing-word-remote-input"></a>Word escritura (entrada remota de Word)
Se trata de un escenario de uso de ancho de banda bajo. A 256 KB/s le proporcionamos tan buena de una experiencia de LAN.

## <a name="learn-more"></a>Aprende más
- [Calcular el uso de ancho de banda de red de RemoteApp de Azure](remoteapp-bandwidth.md)

- [¿Azure RemoteApp - cómo ancho de banda de red y la calidad de la experiencia de trabajo juntos?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp - tseting el uso de ancho de banda de red con algunos escenarios comunes](remoteapp-bandwidthtests.md)