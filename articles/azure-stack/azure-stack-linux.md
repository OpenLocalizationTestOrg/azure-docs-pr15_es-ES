<properties
    pageTitle="Los invitados Linux en pila Azure | Microsoft Azure"
    description="Obtenga información sobre cómo crear máquinas virtuales basadas en Linux en pila de Azure."
    services="azure-stack"
    documentationCenter=""
    authors="anjayajodha"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anajod"/>
    
# <a name="deploy-linux-virtual-machines-on-azure-stack"></a>Implementar máquinas virtuales de Linux en pila de Azure

Puede implementar máquinas virtuales de Linux en la prueba de concepto de pila de Azure agregando una imagen de Linux en la pila de Azure Marketplace. Varios proveedores de Linux disponga de imágenes que se pueden agregar a una prueba de concepto de pila de Azure o puede crear su propio.

## <a name="download-an-image"></a>Descargar una imagen

 1. Descargar y extraer una imagen compatible con Azure pila de los siguientes vínculos o prepare su propio:
  - [Bitnami](https://bitnami.com/azure-stack)
  - [CentOS](http://olstacks.cloudapp.net/latest/)
  - [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
  - [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
  - [Ubuntu 14.04 LTADOS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTADOS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
  
 2. Extraer la imagen de disco duro virtual si es necesario y [Agregar la imagen en el catálogo de soluciones](azure-stack-add-vm-image.md). Asegúrese de que el `OSType` parámetro se establece en `Linux`.
 
 3. Una vez haya agregado la imagen en el catálogo de soluciones, se crea un elemento del catálogo de soluciones y puede implementar una máquina virtual Linux.
  
## <a name="prepare-your-own-image"></a>Preparar su propia imagen

1. Prepare su propia imagen de Linux mediante una de las siguientes instrucciones:
 - [Distribuciones de centOS](../virtual-machines/virtual-machines-linux-create-upload-centos.md)
 - [Linux Debian](../virtual-machines/virtual-machines-linux-debian-create-upload-vhd.md)
 - [Oracle Linux](../virtual-machines/virtual-machines-linux-oracle-create-upload-vhd.md)
 - [Rojos sombrero Enterprise Linux](../virtual-machines/virtual-machines-linux-redhat-create-upload-vhd.md)
 - [SLES & openSUSE](../virtual-machines/virtual-machines-linux-suse-create-upload-vhd.md)
 - [Ubuntu](../virtual-machines/virtual-machines-linux-create-upload-ubuntu.md)

2. Descargue e instale el [Agente de Linux de Azure](https://github.com/Azure/WALinuxAgent/)

    El agente de Azure Linux versión 2.1.3 o superior es necesario para aprovisionar la VM Linux en pila de Azure. Muchas de las distribuciones enumeradas anteriormente ya incluyen esta versión del agente o superior como un paquete en sus repositorios (normalmente denominado `WALinuxAgent` o `walinuxagent`). Sin embargo, si la versión del paquete de agente Azure es menor que 2.1.3 (es decir, 2.0.18 o inferior), a continuación, debe instalar el agente de forma manual. Puede determinar la versión instalada en el nombre del paquete o ejecutando `/usr/sbin/waagent -version` en la máquina virtual.

    Siga las instrucciones siguientes para instalar al agente de Azure Linux manualmente:

 - Primero, descargue el agente de Azure Linux más reciente de [Github](https://github.com/Azure/WALinuxAgent/releases), ejemplo:

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.0.tar.gz

 - Descomprimir al agente de Azure:

            # tar -vzxf v2.2.0.tar.gz

 - Instalar python setuptools

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools

 - Instalar al agente de Azure:

            # cd WALinuxAgent-2.2.0
            # sudo python setup.py install --register-service

    Sistemas con Python 2.x y 3.x instalado de Python en paralelo que necesite ejecutar el siguiente comando:

        # sudo python3 setup.py install --register-service

    Para obtener más información, vea al agente de Linux [Léame](https://github.com/Azure/WALinuxAgent/blob/master/README.md)de Azure.

3. [Agregar la imagen en el catálogo de soluciones](azure-stack-add-vm-image.md). Asegúrese de que el `OSType` parámetro se establece en `Linux`.

4. Una vez haya agregado la imagen en el catálogo de soluciones, se crea un elemento del catálogo de soluciones y puede implementar una máquina virtual Linux.

## <a name="next-steps"></a>Pasos siguientes

[Preguntas más frecuentes sobre la pila de Azure](azure-stack-faq.md)