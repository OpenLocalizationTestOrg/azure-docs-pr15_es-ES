
<properties
    pageTitle="Cargar una imagen personalizada para RemoteApp de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo cargar una imagen personalizada para RemoteApp de Azure"
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
    ms.author="ericor" />



# <a name="upload-a-custom-image-for-azure-remoteapp"></a>Cargar una imagen personalizada para RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Ahora que ha creado la imagen de la plantilla personalizada o actualizaron con cambios, necesita cargar imagen a la biblioteca de imágenes de RemoteApp de Azure. Siga estos pasos.


## <a name="before-you-start"></a>Antes de empezar

1.      Compruebe que la imagen personalizada cumple los [requisitos de imagen](remoteapp-imagereqs.md) y los [requisitos de la aplicación](remoteapp-appreqs.md).
2.      Instale el [módulo de PowerShell de Azure](../powershell-install-configure.md).

## <a name="step-by-step-on-how-to-upload-custom-image"></a>Paso a paso acerca de cómo cargar imagen personalizada

1.      Abra el Portal de administración de Azure y vaya a la página de RemoteApp.
2.      En la pestaña **imágenes de plantilla** , haga clic en **cargar** en la parte inferior de la página.
4.      Escriba un nombre descriptivo para la imagen y especifique la ubicación de la cuenta de almacenamiento. Asegúrese de que la ubicación de la misma ubicación de la colección de RemoteApp o una ubicación donde desea crear uno.
5.      Cuando se le solicite, descargue la secuencia de comandos en su equipo local.
6.      Copiar los parámetros de comando en el cuadro de texto en el Portapapeles.
7.      Abra una ventana de Windows PowerShell elevado.
8.      En la ventana de Windows PowerShell elevada, desplácese hasta el mismo directorio donde haya descargado la secuencia de comandos.
9.      Pegue el comando copiado y presione **ENTRAR**.

    Se iniciará el proceso de carga y duración dependerán de muchos factores como la velocidad de red y el tamaño de la imagen

11.    Si la carga no se realiza correctamente a causa de interrupción de la red o cosas que, siempre puede reanudar el proceso de carga que comenzó. Para reanudar una carga, ejecute el script nuevo con la misma línea de comandos.

> [AZURE.WARNING] Nunca modifique la secuencia de comandos de carga. Se han implementado comprobaciones específicas para asegurarse de que la imagen cumple los requisitos de la imagen y los requisitos de la aplicación.

## <a name="common-problems"></a>Problemas comunes

- Asegúrese de que usar Windows PowerShell, no Azure PowerShell. Debe instalar el módulo de PowerShell de Azure porque ciertos módulos son necesarias durante el proceso de carga.
- Nunca alterar la secuencia de comandos, las validaciones estén disponibles para su comodidad.
- Si el archivo de disco duro virtual obtiene bloqueado durante la carga, copie el archivo o moverla a una nueva carga de ubicación y el intento de nuevo. Puede haber algún proceso de Windows que impide la carga.  
