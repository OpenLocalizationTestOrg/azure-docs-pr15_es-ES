<properties
   pageTitle="Requisitos previos técnicos para crear una imagen de máquina virtual de Azure Marketplace | Microsoft Azure"
   description="Comprender los requisitos para crear e implementar una imagen de máquina virtual a Azure Marketplace para que otras personas para comprar."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="04/29/2016"
  ms.author="hascipio; v-divte"/>

# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Requisitos previos técnicos para crear una imagen de máquina virtual de Azure Marketplace
Lea detenidamente antes de comenzar el proceso de y comprender dónde y por qué se lleva a cabo cada paso. Tanto como sea posible, se debe preparar la información de su organización y otros datos, descargue las herramientas necesarias o crear componentes técnicos antes de comenzar el proceso de creación de la oferta. Estos elementos debería borrar de revisión de este artículo.  

## <a name="download-needed-tools--applications"></a>Es necesario descargar herramientas y aplicaciones
Que debería tener a mano antes de comenzar el proceso de lo siguiente:

- Dependiendo del sistema operativo de destino, instale la [herramienta de la línea de comandos de Linux](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) o [cmdlets de PowerShell de Azure](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) desde la página de [Descargas de Azure](https://azure.microsoft.com/downloads/) .
- Instale el Explorador de Azure almacenamiento de CodePlex.
- Descargar e instalar la herramienta de prueba de certificación para Azure certificada:
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Tiene un equipo basado en Windows para ejecutar la herramienta de certificación. Si no tiene un equipo basado en Windows disponible, puede ejecutar la herramienta en una máquina virtual de Windows Azure.

## <a name="platforms-supported"></a>Plataformas compatibles
Puede desarrollar basados en Azure máquinas virtuales de Windows o Linux. Algunos elementos del proceso de publicación, como la creación de un compatible con Azure disco duro virtual (disco duro virtual): usar diferentes herramientas y pasos según el sistema operativo que está utilizando:  

- Si está utilizando Linux, consulte la sección "Crear un disco duro virtual de Azure compatible (basada en Linux)" de la [Guía de publicación de imágenes de máquina Virtual](marketplace-publishing-vm-image-creation.md).
- Si usa Windows, consulte la sección "Crear un disco duro virtual de Azure compatible (basado en Windows)" de la [Guía de publicación de imágenes de máquina Virtual](marketplace-publishing-vm-image-creation.md).

> [AZURE.NOTE] Necesita acceso a un equipo basado en Windows:
- Ejecutar la herramienta de validación de certificados.
- Crear la dirección URL de firma de disco duro virtual compartido access para la presentación de certificación de disco duro virtual.

## <a name="develop-your-vhd"></a>Desarrollar su disco duro virtual
Puede desarrollar VHD de Azure en la nube o local:

- Desarrollo de nube significa que se realizan todos los pasos de desarrollo de forma remota en un disco duro virtual residente en Azure.
- Desarrollo de local requiere descargar un disco duro virtual y desarrollar mediante infraestructura local. Aunque es posible, se recomienda no se. Tenga en cuenta que desarrollar para Windows o SQL local requiere que tenga las claves de licencia locales pertinentes. No puede incluir ni instalar a SQL Server después de crear una máquina virtual. También debe basar su oferta en una imagen SQL aprobada desde el portal de Azure. Si decide desarrollar local, debe realizar algunos pasos distinta si estaba desarrollar en la nube. Puede encontrar información relevante en [crear una imagen de máquina virtual local](marketplace-publishing-vm-image-creation-on-premise.md).

## <a name="next-steps"></a>Pasos siguientes
Revisar los requisitos previos y completar las tareas necesarias, puede avanzar a la creación de la oferta de imagen de la máquina virtual tal como se describe en la [Guía de publicación de imágenes de máquina Virtual](marketplace-publishing-vm-image-creation.md).

## <a name="see-also"></a>Vea también
- [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)
- [Crear una máquina virtual ejecuta Windows en el portal de vista previa de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
