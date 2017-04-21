
<properties 
    pageTitle="Calcular el uso de ancho de banda de red de RemoteApp de Azure | Microsoft Azure"
    description="Obtenga información sobre los requisitos de ancho de banda de red para sus colecciones de RemoteApp de Azure y aplicaciones."
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

# <a name="estimate-azure-remoteapp-network-bandwidth-usage"></a>Calcular el uso de ancho de banda de red de RemoteApp de Azure 

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

RemoteApp Azure usa el protocolo de escritorio remoto (RDP) para la comunicación entre aplicaciones que se ejecutan en la nube de Azure y sus usuarios. Este artículo proporciona directrices básicas que puede usar para calcular el uso de la red y potencialmente evaluar el uso de ancho de banda de red por usuario RemoteApp de Azure.

Estimación del uso de ancho de banda por usuario es muy compleja y requiere ejecutar varias aplicaciones simultáneamente en escenarios multitarea donde pueden afectar a las aplicaciones de los demás rendimiento en función de su demanda de ancho de banda de red. Incluso el tipo de cliente de escritorio remoto (por ejemplo, cliente de Mac frente a HTML5 cliente) puede producir resultados diferentes de ancho de banda. Para ayudarle a trabajar con estos complicaciones, se deberá dividir los casos de uso en varias de las categorías comunes para replicar escenarios del mundo real. (Donde el escenario real es, por supuesto, una combinación de categorías y difiere por usuario.)

Antes de continuar más - necesita tenga en cuenta que suponemos RDP proporciona una buena a excelente experiencia para la mayoría de los casos de uso en redes con latencia por debajo de 120 ms y ancho de banda más de 5 MB, se basa en la capacidad de RDP para ajustar dinámicamente mediante el ancho de banda de red disponible y el ancho de banda estimado de la aplicación. En este artículo trata más allá de los "mayoría escenarios de uso" para buscar en el borde, donde escenarios comenzarán desenredar y experiencia del usuario comienza a disminuir.

Ahora consulte los siguientes artículos para obtener más información, incluidos los factores que deben contemplarse, recomendaciones de línea base y lo que no hemos incluido en nuestros cálculos.

- [¿Cómo ancho de banda de red y la calidad de la experiencia de trabajo juntos?](remoteapp-bandwidthexperience.md)
- [Probar el uso de ancho de banda de red con algunos escenarios comunes](remoteapp-bandwidthtests.md)
- [Directrices rápidas si no tiene la hora o la capacidad para probar](remoteapp-bandwidthguidelines.md)


## <a name="what-are-we-not-including"></a>¿Qué estamos nos no incluidos?

Al revisar las pruebas propuestas y nuestras recomendaciones generales (y reconocer genéricos), tenga en cuenta que existen varios factores que no se consideran. Por ejemplo, las complicaciones de la experiencia de usuario proporcionadas por la naturaleza asimétrica de carga y descarga ancho de banda. La naturaleza de la mayoría de las redes Wi-Fi asimétrica además afectará el rendimiento y la percepción de la experiencia de usuario. Para escenarios interactivos el tráfico descendente posible prioridades de menor que la precede, que puede aumentar el número de marcos pérdidas de audio o vídeo y, por tanto, afectar perciba el usuario de la experiencia de transmisión por secuencias. Puede ejecutar su propio experimentación para ver qué es adecuado para el caso de uso específicos y la red.

Aunque trataremos redirección de dispositivos, nos no ha tenido en cuenta el impacto de ancho de banda del tráfico de red causado por los dispositivos conectados, como el almacenamiento, impresoras, escáneres, cámaras web y otros dispositivos USB. El efecto de esos dispositivos normalmente picos de temporalmente las necesidades de ancho de banda y desaparece cuando se complete la tarea. Pero si realiza con frecuencia, que la demanda de ancho de banda podría ser muy perceptible.

También no trataremos cómo un usuario puede afectar a otros usuarios en la misma red. Por ejemplo, un usuario consumo 4K vídeo en una red de 100 MB/s significativamente puede afectar a otros usuarios en esa misma red intentar realizar la misma tarea. Desgraciadamente, obtiene cada vez más difícil determinar el impacto de uso simultánea para dar una recomendación común o global sobre el rendimiento del sistema en Agregar. Todo lo podemos decir es que la tecnología de protocolo subyacente hará que el mejor uso del ancho de banda de red disponible, pero tiene sus limitaciones.