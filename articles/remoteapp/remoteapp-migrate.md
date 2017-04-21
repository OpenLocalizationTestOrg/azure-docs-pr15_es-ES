
<properties
    pageTitle="Migrar datos de usuario de RemoteApp de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo migrar los datos de usuario y RemoteApp de Azure."
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



# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>Cómo migrar datos dentro y fuera de RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Puede usar muchas diferentes herramientas y métodos para transferir [datos de usuarios](remoteapp-upd.md) de dentro y fuera de Azure RemoteApp. Estos son algunos métodos:

- Copiar y pegar mediante el uso compartido del Portapapeles
- Copiar archivos y datos en un servidor de archivos
- Copiar archivos a OneDrive para la empresa a través de un explorador
- Copiar archivos mediante el redireccionamiento

>[AZURE.NOTE] 
> No se puede habilitar la OneDrive para empresas o consumidores agentes de sincronización - [no son compatibles](remoteapp-onedrive.md) con RemoteApp de Azure.

## <a name="use-copy-and-paste-in-file-explorer"></a>Usar copiar y pegar en el Explorador de archivos

Copiar y pegar utilizando el Portapapeles está habilitada en RemoteApp implementaciones [de forma predeterminada](remoteapp-redirection.md). Esto permite a los usuarios copiar archivos entre sus aplicaciones de PC y RemoteApp locales. A menudo, durante el curso normal de uso de las aplicaciones de RemoteApp, usuarios guarden archivos en sus UPDs - mover que datos fuera RemoteApp están fáciles:

1. [Publicar el Explorador de archivos como una aplicación](remoteapp-publish.md) de una colección de RemoteApp. (Tenga en cuenta que se trata de una tarea administrativa).
2. Dirigir a los usuarios para iniciar la aplicación de explorador de archivos que ha publicado y usarlo para copiar y pegar archivos en su UDP y fuera de ella.

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>Cargar archivos y datos en un servidor de archivos mediante copia de archivos de red estándar

A menudo las organizaciones usan servidores de archivos para almacenar datos generales. Si conoce el nombre del servidor o la ubicación, los usuarios pueden examinar la red local para el servidor y, a continuación, copie sus archivos, al igual que hicieron anteriormente. Nuevo que desee publicar el Explorador de archivos en RemoteApp y, a continuación, compartirlo con los usuarios.

>[AZURE.NOTE] 
> El servidor de archivos debe estar en la red enrutable RemoteApp implementada en.

## <a name="copy-files-to-onedrive-for-business"></a>Copiar archivos a OneDrive para la empresa
Aunque no se puede habilitar el agente de sincronización de empresa en RemoteApp de OneDrive, puede seguir copiar archivos de su UDP a OneDrive para la empresa a través de un explorador. 

1. Publicar el Explorador de archivos para RemoteApp y, a continuación, indique a los usuarios acceso a los archivos a través de dicha aplicación. 
2. Es más fácil transferir archivos si se comprimen, por lo que los usuarios deben crear un archivo .zip que contiene todos los archivos para ir a OneDrive para la empresa.
3. Pida a los usuarios vaya al portal de Office 365 y, a continuación, vaya a OneDrive y cargar el archivo .zip.

## <a name="copy-files-by-using-drive-redirection"></a>Copiar archivos mediante la redirección de unidad

Si ha habilitado la [redirección de unidades](remoteapp-redirection.md), ya ha creado una unidad asignada para los usuarios. En este caso, puede comprimir sus archivos en la unidad redirigida y, a continuación, guardarlos en su equipo local.