<properties
    pageTitle="Crear una imagen de RemoteApp de Azure | Microsoft Azure"
    description="Obtenga más información sobre las opciones disponibles para la creación de imágenes para RemoteApp de Azure"
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



# <a name="create-an-azure-remoteapp-image"></a>Crear una imagen de RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Azure RemoteApp utiliza imágenes para mantener las aplicaciones que se comparte con los usuarios. (Se tomar la imagen y usarla para crear máquinas virtuales - es lo que el acceso de los usuarios cuando inicie sesión en Azure RemoteApp.) Para crear una colección de Azure RemoteApp con su elección de aplicaciones, ya sea nube o híbrido, iniciar mediante la creación de una imagen con las aplicaciones instaladas. A continuación, cree una colección que utiliza la imagen, asignar a los usuarios a la colección y publicar aplicaciones para esos usuarios.

Tiene varias opciones para la creación o el uso de las imágenes. El [requisito](remoteapp-imagereqs.md) básico para una imagen es que ejecutan Windows Server 2012 R2 y tiene instalado el rol de Host de sesión de escritorio remoto (RDSH). ¿Cómo obtener es lo que interesante.

Cuando se trata de imágenes tiene las siguientes opciones:

- Puede importar y usar una [imagen basada en una máquina virtual Azure](remoteapp-image-on-azurevm.md). Es conveniente para las aplicaciones de línea de negocio que requieren una configuración personalizada. Puede personalizar la imagen para que trabaje para la aplicación.
- Puede [crear y cargar una imagen personalizada](remoteapp-create-custom-image.md). Esto es útil si ya tiene una imagen que use para su implementación local de servicios de escritorio remoto.
- Puede usar una de las [imágenes de plantilla](remoteapp-images.md) incluidos en la suscripción de RemoteApp. Estas imágenes se crean y mantienen el equipo de RemoteApp y contienen algunas aplicaciones estándares (por ejemplo, el conjunto de aplicaciones de Office) que esté disponible para los usuarios. Tenga en cuenta que la imagen de Office 365 Pro Plus puede usarse en un entorno de producción.

Independientemente de dónde obtendrá la imagen o cómo crear, querrá asegurarse de que conoce los [requisitos de la aplicación](remoteapp-appreqs.md) para asegurarse de que la aplicación funciona bien en RemoteApp. A continuación, el siguiente paso es crear una colección de [nube](remoteapp-create-cloud-deployment.md) o [híbrido](remoteapp-create-hybrid-deployment.md) .
