<properties
    pageTitle="Uso de las aplicaciones de App-V con RemoteApp Azure | Microsoft Azure"
    description="Aprenda a usar aplicaciones de App-V en Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="using-app-v-apps-in-azure-remoteapp"></a>Usar aplicaciones de App-V en Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Puede usar las aplicaciones de App-V en una colección de híbrido RemoteApp de Azure, que requiere la combinación de dominio.

Antes de empezar, asegúrese de instalar al cliente de App-V 5.1 con las últimas actualizaciones. Debe crear una [imagen personalizada](remoteapp-create-custom-image.md) que incluye al cliente de App-V.  

Es fácil de usar la infraestructura de App-V con RemoteApp de Azure. Puesto que una colección de híbrido se implementa en un VNET de Azure que tiene acceso a su controlador de dominio y las máquinas virtuales son la pertenencia a un dominio, puede aprovechar los métodos de infraestructura e implementación de App-v existentes a aplicación de App-V easyily host en Azure RemoteApp. Estas son algunas consideraciones que debe tener en cuenta de en función del tipo de implementación de App-V que tiene actualmente:

| Opciones de configuración |                       | Positivo                                                               | Negativo                                                                                              |
|-----------------------|-----------------------|------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Método de entrega       | Transmisión por secuencias (a petición) | Aplicación siempre es la información más reciente y fresca                                     | Primera latencia de tiempo                                                                                    |
|                       | Montaje               | Más rápida; aplicación ya está presente en la máquina virtual                              | Aumento del - toma el espacio de la imagen (límite de 127 GB)                                                           |
| Almacenamiento de la ubicación de aplicación  | Contenido compartido        | Aplicación se ejecuta en memoria de instancia de RemoteApp de Azure                         | Come memoria y buena conexión a streaming server (archivo) donde se encuentra la aplicación                      |
|                       | Disco (en caché)         | Ejecución rápida. Aplicación no depende de la disponibilidad del origen de contenido | Aumento del - toma el espacio de la imagen (límite de 127 GB)                                                           |
| Identificación             | Usuario                  | Requiere la infraestructura de App-V independiente completa                          |                                                                                                       |
|                       | Global (equipo)      |  Antes de publicar o destino utilizando la publicación de servidor                         |  Necesita actualizar la imagen de Azure si desea actualizar la aplicación (enorme). Ocupa espacio en la imagen. |

 Después de crear la imagen personalizada y la colección híbrido, publique la aplicación, asignar los usuarios y disfrutar de las aplicaciones de App-V existentes alojadas en RemoteApp de Azure en cualquier dispositivo en cualquier lugar.
