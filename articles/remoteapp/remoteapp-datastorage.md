
<properties
    pageTitle="Nunca almacenar datos confidenciales en imágenes personalizadas para RemoteApp de Azure | Microsoft Azure"
    description="Obtenga más información sobre las directrices para almacenar los datos en imágenes personalizadas de RemoteApp de Azure"
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


# <a name="never-store-sensitive-data-on-custom-images"></a>Nunca almacenar datos confidenciales en imágenes personalizadas

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Cuando se hospeda su propia aplicación en Azure RemoteApp, el primer paso es crear una imagen personalizada. Usamos esa imagen personalizada para crear instancias VM que sirven las aplicaciones a los usuarios. La imagen personalizada debe contener sólo aplicaciones y nunca información confidencial que se puede perder, como bases de datos SQL, archivos de personal o archivos de datos especiales como QuickBooks compañía. Todos los datos confidenciales deben encontrarse externos a RemoteApp de Azure en un servidor de archivos, otra VM de Azure, o en SQL Azure. La imagen debe alojar simplemente la aplicación que se conecta al origen de datos y presenta los datos. Revise los [requisitos para las imágenes de Azure RemoteApp](remoteapp-imagereqs.md) para obtener más información. 

Para entender por qué no se debe almacenar información confidencial, debe saber cómo funciona RemoteApp de Azure. Se crean cuando se crea o actualiza varias copias de la imagen o clones entre bastidores una colección. Estas instancias VM son réplicas exactas de la imagen personalizada; Cuando los usuarios inician aplicaciones están conectados a una de estas instancias de máquina virtual. Pero la misma instancia no se garantiza y no importa porque son no persistente. Las instancias VM aloja las aplicaciones no son persistentes y se puede destruir o eliminar según, por ejemplo, durante la actualización de la colección. 

Una vez que se aprovisiona la colección y los usuarios empiezan a conectarse a las máquinas virtuales, los datos de usuario están persistentes y protegido porque se guarda en un almacenamiento independiente dentro de un disco duro virtual que denominamos un [disco de perfil de usuario (UDP)](remoteapp-upd.md), que es el perfil de usuario en c:\users\<PerfilUsuario >. Cuando se inicie una aplicación, la UDP es montaje y se trata como un perfil de usuario local por el sistema operativo. Obtenga más información sobre cómo [RemoteApp de Azure guarda la configuración y datos de usuario](remoteapp-upd.md).

Datos de ejemplo que no deben encontrarse en la imagen:

- Compartir datos de los usuarios para obtener acceso a
- Base de datos SQL o QuickBooks DB
- Todos los datos de D:\

Datos de ejemplo que pueden residen en el perfil predeterminado que se copie UDP de todos los usuarios:

- Archivos de configuración por usuario
- Secuencias de comandos que los usuarios tendrían que mantienen su UDP

Puntos clave:

- Nunca almacén de datos confidenciales que se pueden perder en la imagen cuando se crea una imagen personalizada.
- Datos confidenciales siempre deben residen en un servidor de un archivo independiente, VM independiente de Azure, en la nube y siempre externos a las instancias VM alojar las aplicaciones de Azure RemoteApp. 
- Datos de usuario se guardan y se conserva en el disco de perfil de usuario (UDP)


