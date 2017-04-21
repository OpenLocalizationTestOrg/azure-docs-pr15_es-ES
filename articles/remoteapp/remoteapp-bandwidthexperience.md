<properties 
    pageTitle="¿Azure RemoteApp - cómo ancho de banda de red y la calidad de la experiencia de trabajo juntos? | Microsoft Azure"
    description="Obtenga información sobre cómo puede afectar al ancho de banda en Azure RemoteApp calidad del usuario de experiencia."
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

# <a name="azure-remoteapp---how-do-network-bandwidth-and-quality-of-experience-work-together"></a>¿Azure RemoteApp - cómo ancho de banda de red y la calidad de la experiencia de trabajo juntos?

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Cuando se encuentre el [ancho de banda general](remoteapp-bandwidth.md) necesario para RemoteApp de Azure, tenga en cuenta los siguientes factores: forman parte de un sistema dinámico que afecta a la experiencia global del usuario. 

- **Ancho de banda de red disponible y condiciones actuales de red** - un conjunto de parámetros (pérdidas de latencia, vibración) en la misma red en un momento dado puede influir en la aplicación transmisión experiencia, lo que significa una disminución experiencia general del usuario. El ancho de banda disponible en su red es una función de congestión, pérdida aleatorio, latencia porque todos estos parámetros afectan el mecanismo de control de congestión, que a su vez controles la velocidad de transmisión para evitar conflictos.  Por ejemplo, una red con pérdida o con latencia alta hará que el usuario experiencia incorrectas incluso en una red con ancho de banda de 1000 MB. La latencia y pérdida varían según el número de usuarios que están en la misma red y qué hacen los usuarios (por ejemplo, ver vídeos, descargar o cargar archivos grandes, imprimir).
- **Escenario de uso** - la experiencia depende de qué hacen los usuarios individualmente y como un grupo en la misma red. Por ejemplo, una diapositiva de lectura requiere sólo un marco único que se actualicen; Si el usuario skims y desplaza el cursor sobre el contenido de un documento de texto, que necesitan un mayor número de marcos que se actualicen por segundo. La comunicación y hacia atrás en el servidor en este escenario finalmente consume más ancho de banda de red. También tenga en cuenta un ejemplo extremo: varios usuarios ver vídeos de alta definición (como resolución de 4K), manteniendo llamadas de conferencia de alta definición, juegos de vídeo 3D o trabajar en los sistemas de CAD. Todas estas pueden hacer incluso una red de ancho de banda alto realmente prácticamente inutilizable.
- **Resolución de pantalla y el número de pantallas** - más ancho de banda de red se requiere para pantallas más grandes de actualización completa de pantallas más pequeñas. La tecnología subyacente realiza un buen trabajo de codificación y transmitir sólo las regiones de las pantallas que se han actualizado, pero de vez en cuando, es necesario actualizar toda la pantalla. Cuando el usuario tiene una pantalla de resolución superior (por ejemplo resolución de 4K), dicha actualización requiere más ancho de banda de red que una pantalla con resolución inferior (por ejemplo, 1024x768px). Esta misma lógica se aplica si usa más de una pantalla para la redirección. Ancho de banda necesario aumentar el número de pantallas.
- **Redirección del Portapapeles y dispositivo** - se trata de un problema no muy obvio, pero en muchos casos si un usuario guarda un gran bloque de datos en el Portapapeles, lleva algo de tiempo para esa información transferir desde el cliente de escritorio remoto en el servidor. La experiencia de nivel inferior puede verse afectada por la experiencia de enviar el contenido del Portapapeles en sentido ascendente. Lo mismo se aplica para la redirección de dispositivo - si un escáner o cámara web genera una gran cantidad de datos que se debe enviar precede al servidor o necesita recibir un documento grande, una impresora o almacenamiento local debe estar disponible para una aplicación en la nube para copiar un archivo grande, los usuarios podrían observar marcos colocadas o temporalmente "inmovilizada" vídeo porque los datos necesarios para la redirección de dispositivo aumenta el ancho de banda de red necesidades. 

Al evaluar las necesidades de ancho de banda de red, asegúrese de tener en cuenta todos estos factores funciona como un sistema.

Ahora, vuelva al [artículo de ancho de banda de red principal](remoteapp-bandwidth.md)o pasar a probar el [ancho de banda de red](remoteapp-bandwidthtests.md).

## <a name="learn-more"></a>Aprende más
- [Calcular el uso de ancho de banda de red de RemoteApp de Azure](remoteapp-bandwidth.md)

- [Azure RemoteApp - probar el uso de ancho de banda de red con algunos escenarios comunes](remoteapp-bandwidthtests.md)

- [Azure RemoteApp ancho de banda - directrices generales (si no puede probar su propio)](remoteapp-bandwidthguidelines.md)