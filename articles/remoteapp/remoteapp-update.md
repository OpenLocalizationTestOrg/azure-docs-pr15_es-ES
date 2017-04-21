<properties
   pageTitle="Actualizar la colección de RemoteApp de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo actualizar la colección de RemoteApp de Azure"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="update-a-collection-in-azure-remoteapp"></a>Actualizar una colección de RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Vendrá uno, inevitable, cuando tenga que actualizar las aplicaciones o la imagen en la colección de RemoteApp de Azure. Si usa una de las imágenes que se incluye con la suscripción de RemoteApp de Azure en la colección de una nube o híbrido, todas las actualizaciones se administran por RemoteApp de Azure, por lo puede estar fácil.

Sin embargo, si está utilizando una imagen personalizada (que generó desde cero o que ha creado modificando una de nuestras imágenes), es responsable de mantenimiento de la imagen y las aplicaciones. Si necesita actualizar la imagen o alguna de las aplicaciones que hay dentro de ella, debe crear una nueva versión actualizada de la imagen y, a continuación, reemplazar la imagen existente en la colección con esta nueva imagen actualizada.

¿Por lo tanto, qué tiene que hacer acerca de cómo actualizar la colección? Es bastante sencillo:

1. Actualice la imagen que ha usado en la colección. Aplicar las revisiones o actualizaciones necesarias y, a continuación, guárdelo con un nombre nuevo.
2. [Cargar](remoteapp-uploadimage.md) o [Importar](remoteapp-image-on-azurevm.md) que RemoteApp de imagen.
3. Ahora, en la página de la colección, haga clic en **Actualizar**.
4. Elija la nueva imagen de la lista de **Imágenes de plantilla** .
4. Aquí es la parte complicada: debe decidir cómo tratar con todos los usuarios que actualmente se están utilizando una aplicación de la colección. Tiene las siguientes opciones:
    - **Conceder a los usuarios 60 minutos después de la actualización**. Tan pronto como finalice la actualización, RemoteApp de Azure se mostrará un mensaje a los usuarios indicándoles que guarde su trabajo y cierre la sesión y vuelva a iniciarla. Después de 60 minutos, todos los usuarios activos que no han iniciado sesión desactivar se registrará automáticamente. Los usuarios pueden inmediatamente vuelva a iniciarla.
    - **Desconectar inmediatamente a los usuarios**. Cuando la actualización haya terminado, cierre la sesión todos los usuarios automáticamente sin ninguna advertencia. Si elige esta opción, los usuarios pueden perder datos. Sin embargo, puede volver a conectarse a la aplicación inmediatamente.

1. Haga clic en la marca de verificación para iniciar la actualización.
