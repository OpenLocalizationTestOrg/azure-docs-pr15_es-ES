<properties
    pageTitle="Respaldo de las distribuciones de Linux | Microsoft Azure"
    description="Aprenda a Linux en distribuciones de respaldo de Azure, incluyendo las directrices para Ubuntu, OpenLogic, Oracle y SUSE."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager"
    />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="szark"/>



#<a name="linux-on-azure-endorsed-distributions"></a>Linux en distribuciones de respaldo de Azure

> [AZURE.NOTE] Si tiene unos momentos, Ayúdenos a mejorar la documentación de Azure Linux VM tomando esta [Encuesta rápida](https://aka.ms/linuxdocsurvey) de su experiencia. Cada respuesta nos ayuda a realizar el trabajo de ayuda.

Las imágenes de Linux en la Galería de Azure o Marketplace proporcionadas por un número de socios y estamos trabajando con diversos Comunidades Linux para agregar tipos aún más a la lista de distribución de apoyo. Mientras tanto, para distribuciones no está disponibles en la galería, siempre puede traer-su-propietario-Linux siguiendo las instrucciones de [esta página](virtual-machines-linux-classic-create-upload-vhd.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="supported-distributions--versions"></a>Distribuciones admitidas y versiones ##

La siguiente tabla enumeran las distribuciones y versiones Linux que son compatibles en Azure. Consulte también [para las imágenes de Linux en Microsoft Azure de soporte técnico](https://support.microsoft.com/en-us/kb/2941892) para obtener información detallada.

Los controladores de servicios de integración de Linux (vertical) para Hyper-V y Azure son módulos de kernel de Microsoft contribuye directamente a la precede kernel de Linux.  Los controladores vertical están integrados en kernel de la distribución de forma predeterminada, o para antiguos RHEL CentOS basados en/distribuciones están disponibles como una descarga independiente [aquí](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409).  Consulte [este artículo](virtual-machines-linux-create-upload-generic.md#linux-kernel-requirements) para obtener más información acerca de los controladores de vertical.

El agente de Azure Linux ya está preinstalado en las imágenes de la Galería de Azure y normalmente están disponible del repositorio de paquete de la distribución.  Código fuente puede encontrarse en [GitHub](https://github.com/azure/walinuxagent).

Distribución.|Versión|Controladores|Agente
---|---|---|---
CentOS por OpenLogic | CentOS 6.3 + 7.0 + | CentOS 6.3: [descarga vertical](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 +: Núcleo | Paquete: [OpenLogic repo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) en "WALinuxAgent" <br/>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) | 494.4.0+ | En el núcleo | Código fuente: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Debian | Debian 7.9 +, 8.2 + | En el núcleo | Paquete: repo en "waagent" <br/>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent)
Oracle Linux | 6.4 +, 7.0 + | En el núcleo | Paquete: repo en "WALinuxAgent" <br/>Código fuente: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
Rojos sombrero Enterprise Linux | RHEL 6,7 + 7.1 + | En el núcleo|Paquete: repo en "WALinuxAgent" <br/>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent)
SUSE Linux Enterprise | 11 de SLES Service Pack 4, SLES 12 SP1 + y <p> SLES para SAP 11 SP3 + | En el núcleo | Paquete: En la [Nube: herramientas](https://build.opensuse.org/project/show/Cloud:Tools) repo en "agente de azure python" <br/>Código fuente: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE | openSUSE 13.2 + | En el núcleo | Paquete: En la [Nube: herramientas](https://build.opensuse.org/project/show/Cloud:Tools) repo en "agente de azure python" <br/>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent)
Ubuntu|Ubuntu 12.04, 14.04, 16.04, 16,10 | En el núcleo | Paquete: repo en "walinuxagent" <br/>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent)


## <a name="partners"></a>Socios

### <a name="openlogic"></a>OpenLogic
[http://www.openlogic.com/Azure](http://www.openlogic.com/azure)

OpenLogic es un proveedor líder de soluciones de código abierto para la nube y el centro de datos. OpenLogic ayuda a cientos de líderes de la empresa en una amplia gama de industrias adquirir, soporte técnico y software Abrir origen de control de forma segura. OpenLogic ofrece soporte técnico de comerciales y compensación alguna de 600 paquetes de Abrir origen con una copia de la Comunidad de experto OpenLogic, incluido el soporte de nivel empresarial para CentOS así como el partner de inicio para proporcionar imágenes basadas en CentOS en Azure.

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/Running-coreos/cloud-Providers/Azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Desde el sitio Web de CoreOS:

*CoreOS está diseñado para la seguridad, la coherencia y confiabilidad. En lugar de instalar paquetes a través de yum o apt, CoreOS utiliza Linux contenedores para administrar los servicios de un nivel superior de abstracción. Código de un servicio único y todas las dependencias están incluidas dentro de un contenedor que se puede ejecutar en uno o varios equipos de CoreOS.*


### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/Debian-images-Microsoft-Azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ es una empresa de servicios, especializado en el desarrollo e implementación de soluciones profesionales mediante el uso de software gratuito y de consultoría independiente. Como especialistas líderes de código abierto, Credative tiene reconocimiento internacional con muchos departamentos de TI con su apoyo. Junto con Microsoft, Credativ actualmente está preparando imágenes Debian correspondientes para Debian 8 (Jessie) y Debian antes de 7 (Wheezy), que están diseñadas especialmente para ejecutarse en Azure y pueden administrarse fácilmente a través de la plataforma. Credativ, también admiten el mantenimiento a largo plazo y la actualización de las imágenes Debian para Azure a través de sus centros de soporte de Abrir origen.

### <a name="oracle"></a>Oracle
[http://www.Oracle.com/technetwork/topics/cloud/FAQ-1963009.HTML](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Estrategia de Oracle es ofrecer una amplia gama de soluciones de nubes públicas y privadas, mientras ofrece a los clientes alternativas y flexibilidad en cómo implementar software de Oracle en nubes de Oracle, así como otras nubes.  Asociación de Oracle con Microsoft permite a los clientes a implementar el software de Oracle en nubes públicas y privadas de Microsoft con la confianza de certificación y soporte técnico de Oracle.  Compromiso y la inversión en soluciones de nube públicos y privados de Oracle de Oracle se ha modificado.

### <a name="red-hat"></a>Sombrero rojo
[http://www.redhat.com/en/partners/Strategic-Alliance/Microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

El proveedor del mundo líder de soluciones de código abierto, sombrero rojo le ayuda a más de un 90% de las empresas Fortune 500 resolver desafíos de negocio, alinear su TI y estrategias empresariales, y a prepararse para el futuro de la tecnología. Para ello, rojo sombrero proporcionar soluciones seguras a través de un modelo de empresa abierto y un modelo de suscripción asequible y predecible.

### <a name="suse"></a>SUSE
[http://www.SuSE.com/SuSE-Linux-Enterprise-Server-on-Azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server en Azure es una plataforma comprobada que proporciona confiabilidad superior y la seguridad de la informática en nube. Plataforma de Linux flexible de SUSE integra sin problemas con los servicios de nube de Azure para ofrecer un entorno de nube fácil de administrar. Y con más de 9,200 aplicaciones certificadas más de 1.800 proveedores de software independientes para SUSE Linux Enterprise Server, SUSE garantiza que ejecutan admitidos en el centro de datos de cargas de trabajo se pueden implementar con confianza en Azure.

### <a name="canonical"></a>Canónico
[http://www.Ubuntu.com/cloud/Azure](http://www.ubuntu.com/cloud/azure)

Ingeniería canónica y éxito del Ubuntu de comunidad abierta gobernanza unidad de cliente, el servidor y la informática en nube, incluidos los servicios de nube personal para los consumidores. Visión de canónica de una plataforma unificada gratuita en Ubuntu, desde el teléfono a la nube, con una familia de interfaces coherentes para el teléfono, tableta, TV y escritorio, hace Ubuntu la primera opción para instituciones diversas proveedores de nube pública a los creadores de electrónica de consumo y favorita entre técnicos individuales.

Los programadores y centros de ingeniería todo el mundo, canónica se posiciona exclusivamente para colaborar con los creadores de hardware, proveedores de contenido y los desarrolladores de software para ofrecer soluciones de Ubuntu en el mercado, de PC a los servidores y dispositivos de mano.

