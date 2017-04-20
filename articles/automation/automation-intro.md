<properties
    pageTitle="¿Qué es la automatización de Azure | Microsoft Azure"
    description="Obtenga información sobre qué valor proporciona automatización de Azure y obtenga respuestas a preguntas comunes para que puede empezar a crear, usar runbooks y DSC de automatización de Azure."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="¿Qué es la automatización, automatización de azure, ejemplos de automatización de azure"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="05/10/2016"
    ms.author="magoedte;bwren"/>

# <a name="azure-automation-overview"></a>Información general de automatización Azure

Automatización de Microsoft Azure proporciona una manera para los usuarios automatizar las tareas manuales, larga duración, errores y repiten con frecuencia que se realizan habitualmente en un entorno de nube y enterprise. Ahorra tiempo y aumenta la confiabilidad de las tareas administrativas normales e incluso programar que se realicen automáticamente a intervalos regulares. Puede automatizar procesos usando runbooks o automatizar la administración de la configuración con la configuración de estado deseado. Este artículo proporciona información general breve de automatización de Azure y preguntas más frecuentes. Puede hacer referencia a otros artículos de esta biblioteca para obtener información detallada sobre los distintos temas.


## <a name="automating-processes-with-runbooks"></a>Automatización de procesos con runbooks

Un runbook es un conjunto de tareas que lleve a cabo un proceso automatizado de automatización de Azure. Puede ser un proceso simple como iniciar una máquina virtual y crear una entrada de registro, o puede tener un runbook compleja que combina otros runbooks más pequeños para llevar a cabo un proceso complejo a través de varios recursos o incluso varios nubes y en entornos locales.  

Por ejemplo, se podría tener un proceso manual existente para truncar una base de datos SQL si se aproxima al tamaño máximo que incluye varios pasos como la conexión al servidor, conectarse a la base de datos, obtener el tamaño actual de la base de datos, compruebe si ha superado el umbral truncarlo y notificar al usuario. En lugar de realizar manualmente cada uno de estos pasos, puede crear un runbook que realiza todas estas tareas como un único proceso. ¿Iniciar runbook, proporcionar la información requerida, como el nombre del servidor SQL, el nombre de base de datos y el correo electrónico del destinatario y luego sentarse mientras se complete el proceso. 


## <a name="what-can-runbooks-automate"></a>¿Qué puede automatizar runbooks?

Runbooks en Azure automatización basados en Windows PowerShell o flujo de trabajo de Windows PowerShell, por lo que lo hacen todo lo que puede hacer PowerShell. Si una aplicación o servicio tiene una API, un runbook puede trabajar con él. Si tiene un módulo de PowerShell para la aplicación, puede cargar ese módulo en automatización de Azure e incluir los cmdlets en su runbook. Azure runbooks automatización ejecutar en la nube de Azure y puede tener acceso a los recursos de la nube o recursos externos que pueden tener acceso desde la nube. Mediante [Híbrido Runbook trabajador](automation-hybrid-runbook-worker.md), runbooks puede ejecutar en el centro de datos local para administrar recursos locales. 


## <a name="getting-runbooks-from-the-community"></a>Obtener runbooks de la Comunidad

La [Galería de Runbook](automation-runbook-gallery.md#runbooks-in-runbook-gallery) contiene runbooks de Microsoft y la Comunidad que puede utilizar sin cambios en su entorno o personalizarlos para sus propios fines. También son útiles para como referencias para aprender a crear su propio runbooks. Incluso puede contribuir su propio runbooks a la galería que crea que otros usuarios pueden resultarle útiles. 


## <a name="creating-runbooks-with-azure-automation"></a>Creación de Runbooks con la automatización de Azure 

Puede [crear su propio runbooks](automation-creating-importing-runbook.md) desde cero o modificar runbooks desde la [Galería de Runbook](http://msdn.microsoft.com/library/azure/dn781422.aspx) de sus propios requisitos. Existen tres [tipos de runbook](automation-runbook-types.md) que puede elegir en función de su experiencia de PowerShell y los requisitos. Si prefiere trabajar directamente con el código de PowerShell, puede usar un [runbook de PowerShell](automation-runbook-types.md#powershell-runbooks) o [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) que editar sin conexión o con el [editor de texto](http://msdn.microsoft.com/library/azure/dn879137.aspx) en el portal de Azure. Si prefiere editar un runbook sin se expongan en el código subyacente, puede crear un [gráfico runbook](automation-runbook-types.md#graphical-runbooks) con el [editor gráfico](automation-graphical-authoring-intro.md) en el portal de Azure. 

¿Prefiere ver para lectura? Eche un vistazo a la debajo de vídeo de la sesión de Microsoft Ignite de mayo de 2015. Nota: Mientras los conceptos y las características descritas en este vídeo son correctas, que automatización de Azure progreso mucho puesto que se registra en este vídeo, se ahora tiene una interfaz de usuario más amplia en el portal de Azure y es compatible con funciones adicionales.

> [AZURE.VIDEO microsoft-ignite-2015-automating-operational-and-management-tasks-using-azure-automation]


## <a name="automating-configuration-management-with-desired-state-configuration"></a>Automatización de administración de la configuración con la configuración de estado deseado 

[DSC de PowerShell](https://technet.microsoft.com/library/dn249912.aspx) es una plataforma de administración que permite administrar, implementar y exigir la configuración de hosts físicos y máquinas virtuales usando una sintaxis PowerShell descriptiva. Puede definir configuraciones en un DSC extraer servidor central que se pueden recuperar y aplicar automáticamente los equipos de destino. DSC proporciona un conjunto de cmdlets de PowerShell que puede usar para administrar las configuraciones y los recursos.  

[DSC de automatización de Azure](automation-dsc-overview.md) es una solución basada en la nube para DSC de PowerShell que proporciona servicios que necesita para entornos de empresa.  Puede administrar los recursos DSC en automatización de Azure y aplicar configuraciones a máquinas virtuales o físicos que recuperarlos desde un servidor de extracción DSC en la nube de Azure.  También proporciona servicios de generación de informes que informar de los eventos importantes como cuando se han desviado nodos desde su configuración asignado y una nueva configuración se ha aplicado. 


## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Crear sus propias configuraciones DSC con la automatización de Azure

[Configuraciones de DSC](automation-dsc-overview.md#azure-automation-dsc-terms) especificar el estado de un nodo deseado.  Varios nodos pueden aplicar la misma configuración para asegurarse de que todos ellos mantengan un estado idénticos.  Puede crear una configuración con cualquier texto editor en el equipo local y, a continuación, importarla a automatización de Azure donde puede compilar y aplicarlo nodos.


## <a name="getting-modules-and-configurations"></a>Obtener módulos y configuraciones 

Puede obtener [módulos de PowerShell](automation-runbook-gallery.md#modules-in-powershell-gallery) que contiene los cmdlets que puede usar en sus runbooks y configuraciones DSC desde la [Galería de PowerShell](http://www.powershellgallery.com/). Puede iniciar esta galería desde el portal de Azure e Importar módulos directamente en automatización de Azure, o puede descargar e importarlos manualmente. No puede instalar los módulos directamente desde el portal de Azure, pero se pueden descargar instalarlos como lo haría con cualquier otro módulo. 


## <a name="example-practical-applications-of-azure-automation"></a>Aplicaciones prácticas de ejemplo de automatización de Azure 

Siguientes son unos pocos ejemplos de qué son los tipos de situaciones de automatización con la automatización de Azure. 

* Crear y copiar máquinas virtuales en diferentes suscripciones de Azure. 
* Programar copias de archivos desde un equipo local a un contenedor de almacenamiento de blobs de Windows Azure. 
* Automatizar las funciones de seguridad como denegar solicitudes de un cliente cuando se detecta un ataque de denegación de servicio. 
* Asegúrese de que equipos continuamente alinean con la directiva de seguridad configurados.
* Administrar la implementación continua de código de la aplicación a través de la nube y en la infraestructura local. 
* Crear un bosque de Active Directory en Azure para su entorno. 
* Truncar una tabla en una base de datos SQL si DB se acerque al tamaño máximo. 
* Actualizar la configuración del entorno de un sitio Web de Azure de forma remota. 


## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>¿Cómo se relacionan con la automatización Azure con otras herramientas de automatización?

[Automatización de administración de servicios (SMA)](http://technet.microsoft.com/library/dn469260.aspx) está pensado para automatizar tareas de administración en la nube privada. Está instalado localmente en el centro de datos como un componente de [Microsoft Azure Pack](https://www.microsoft.com/en-us/server-cloud/). Automatización de Azure y SMA utilizan el mismo formato runbook basado en Windows PowerShell y el flujo de trabajo de Windows PowerShell, pero SMA no admite [runbooks gráfica](automation-graphical-authoring-intro.md).  

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) está pensado para automatización de recursos local. Utiliza un formato diferente runbook de automatización de Azure y automatización de administración de servicio y tiene una interfaz gráfica para crear runbooks sin necesidad de utilizar secuencias de comandos. Su runbooks se compone de actividades de paquetes de integración que se hayan escrito específicamente para Orchestrator. 


## <a name="where-can-i-get-more-information"></a>¿Dónde puedo obtener más información? 

Una gran variedad de recursos están disponibles obtener más información sobre la automatización de Azure y crear sus propios runbooks. 

* **Biblioteca de automatización de Azure** es donde se encuentra ahora mismo. Los artículos de esta biblioteca proporcionan documentación completa sobre la configuración y administración de automatización de Azure y para crear su propio runbooks. 
* [Cmdlets de PowerShell de Azure](http://msdn.microsoft.com/library/jj156055.aspx) proporciona información para automatizar las operaciones de Azure con Windows PowerShell. Runbooks usar estos cmdlets para trabajar con recursos de Azure. 
* [Blog de administración](https://azure.microsoft.com/blog/tag/azure-automation/) proporciona la información más reciente en la automatización de Azure y otras tecnologías de administración de Microsoft. Deberá suscribirse a este blog para mantenerse al día con la información más reciente del equipo de automatización de Azure. 
* [Foro de automatización](http://go.microsoft.com/fwlink/p/?LinkId=390561) le permite publicar preguntas sobre la automatización de Azure solucionarse por Microsoft y la Comunidad de automatización. 
* [Cmdlets de automatización de Azure](https://msdn.microsoft.com/library/mt244122.aspx) proporciona información para automatizar tareas de administración. Contiene cmdlets para administrar cuentas de automatización, activos, runbooks, DSC.


## <a name="can-i-provide-feedback"></a>¿Puedo proporcionar comentarios? 

**Por favor, envíenos sus comentarios!** Si está buscando una solución de automatización de Azure runbook o un módulo de integración, publique una solicitud de secuencia de comandos en el centro de secuencia de comandos. Si tiene comentarios o característica solicitudes de automatización de Azure, publíquelos en [Voz de usuario](http://feedback.windowsazure.com/forums/34192--general-feedback). Muchas gracias. 


