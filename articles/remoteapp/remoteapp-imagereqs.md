
<properties
    pageTitle="Requisitos de la imagen de RemoteApp Azure | Microsoft Azure"
    description="Obtenga más información sobre los requisitos para la creación de imágenes para utilizar con RemoteApp de Azure"
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



# <a name="requirements-for-azure-remoteapp-images"></a>Requisitos para las imágenes de RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

RemoteApp Azure usa una imagen de Windows Server 2012 R2 para hospedar todos los programas que desea compartir con los usuarios. Para crear una imagen personalizada, puede empezar con una imagen existente o [cree uno nuevo](remoteapp-create-custom-image.md).

> [AZURE.TIP] ¿Sabía que su suscripción de RemoteApp de Azure le proporciona acceso a una imagen de Windows Server 2012 R2 en la Galería de Azure VM que puede usar para crear su propia imagen de plantilla? [Eche un vistazo](remoteapp-image-on-azurevm.md).  


Los requisitos de la imagen que se pueden cargar para su uso con Azure RemoteApp son:


- Aplicaciones personalizadas no almacenan datos localmente en la imagen. Estas imágenes son independientes y sólo deben contener aplicaciones.
- La imagen no contiene datos que se pueden perder.
- El tamaño de la imagen debe ser un múltiplo de MB. Si intenta cargar una imagen que no es un múltiplo exacto, se producirá un error en la carga.
- El tamaño de la imagen debe ser 127 GB o más pequeña.
- Debe estar en un archivo de disco duro virtual (archivos VHDX no se admiten actualmente).
- El disco duro virtual no debe ser una máquina virtual de generación 2.
- Puede ser el disco duro virtual de tamaño fijo o expansión dinámica. Se recomienda un disco duro virtual de expansión dinámica porque se tarda menos tiempo para cargar en Azure que un archivo de disco duro virtual de tamaño fijo.
- El disco debe inicializarse con el registro de inicio maestro (MBR) partición estilo. No se admite el estilo de particiones GUID (GPT) de la tabla de partición.
- El disco duro virtual debe contener una única instalación de Windows Server 2012 R2. Puede contener varios volúmenes, pero solo que contiene una instalación de Windows.
- Deben estar instalados el rol de Host de sesión de escritorio remoto (RDSH) y la característica de experiencia de escritorio.
- La función de agente de conexión a Escritorio remoto debe *no* estar instalado.
- El archivo de sistema (CIFRADOS) debe estar deshabilitado.
- La imagen debe estar preparada con Sysprep mediante los parámetros **/oobe /generalize /shutdown** (no utilice el parámetro **/mode:vm** ).
- No se admite la carga de su disco duro virtual de una cadena de instantáneas.

Para obtener más información sobre la creación de imágenes para RemoteApp de Azure, vea [crear una imagen de RemoteApp de Azure](remoteapp-imageoptions.md) .
