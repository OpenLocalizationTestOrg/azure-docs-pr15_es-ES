<properties
    pageTitle="¿Qué es una máquina Virtual de ciencias de datos? | Microsoft Azure"
    description="Obtenga información sobre los escenarios clave, características y cómo empezar a trabajar con datos ciencias máquinas virtuales, un entorno y el Kit de herramientas listo para análisis."
    keywords="herramientas de ciencias de datos, máquina virtual de ciencias de datos, herramientas para ciencias de datos, linux ciencias de datos"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="bradsev" />


# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Introducción a la basada en nube datos ciencias Máquina Virtual para Linux y Windows

La máquina Virtual de ciencias de datos es una imagen de máquina virtual personalizada en la nube de Microsoft Azure diseñado específicamente para hacer ciencias de datos. Tiene muchas ciencias populares de datos y otras herramientas preinstalada y preconfigurada para estimular la creación de aplicaciones inteligentes para análisis avanzado. Está disponible en Windows Server 2012 o en versiones de basado en OpenLogic 7.2 CentOS Linux. 

En este tema se describe lo que puede hacer con la máquina virtual de ciencias de datos, se describe algunos de los escenarios clave para el uso de la máquina virtual, detalla las características clave disponibles en las versiones de Windows y Linux y proporciona instrucciones sobre cómo empezar a trabajar con ellos.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>¿Qué puedo hacer con la máquina Virtual de ciencias de datos?

El objetivo de la máquina Virtual de ciencias de datos es proporcionar a los datos profesionales en todos los niveles de aptitudes y funciones con un entorno de ciencias datos sin dificultades. Este VM guarda mucho tiempo dedicará si tenía resumen un entorno comparables sus propias. En su lugar, inicie el proyecto de ciencias datos inmediatamente en una instancia VM recién creada. 

La máquina virtual de ciencias de datos está diseñada y configurada para trabajar con los escenarios de uso generales. Puede escalar su entorno hacia arriba o hacia abajo el proyecto cambien las necesidades. Es posible usar el idioma preferido para las tareas de ciencias de datos de programa. Puede instalar otras herramientas y personalizar el sistema de sus necesidades.
 
## <a name="key-scenarios"></a>Escenarios clave
Esta sección sugiere algunos escenarios clave para el que se puede implementar la máquina virtual de ciencias de datos.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Escritorio de análisis preconfigurados en la nube

La máquina virtual de ciencias de datos proporciona una configuración de línea base para equipos de ciencias datos para reemplazar sus escritorios locales con un equipo de escritorio de nube administrado. Esta línea base garantiza que todos los científicos de datos en un equipo tienen una configuración coherente con la que se va a comprobar experimentación y promover la colaboración. También disminuye los costos, reducir la carga de sysadmin y guárdelo en el tiempo necesario para evaluar, instalar y mantener los diversos paquetes de software es necesarios realizar análisis avanzado.  

### <a name="data-science-training-and-education"></a>Educación y formación de ciencias de datos

Formadores Enterprise y profesores que enseñar a ciencia datos clases suelen proporcionan una imagen de máquina virtual para asegurarse de que sus estudiantes tienen una configuración coherente y que los ejemplos funcionen predecible. La máquina virtual de ciencias de datos, se crea un entorno de a petición con una configuración coherente que facilita los desafíos de incompatibilidad y soporte técnico. Casos en estos entornos necesiten generarse con frecuencia, especialmente para cursos más cortas, beneficiarán considerablemente.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacidad elástica a petición para proyectos de gran escala

Datos ciencias hackathons/competiciones o exploración y modelado de datos a gran escala requieren escalado de capacidad de hardware, normalmente de corta duración. La máquina virtual de ciencias de datos puede ayudar a replicar el entorno de ciencias datos rápidamente a petición, en escala los servidores que permiten experimentación que requieren alta potencia recursos informáticos para ejecutarse.

### <a name="short-term-experimentation-and-evaluation"></a>Evaluación y experimentación a corto plazo

La máquina virtual de ciencias de datos puede ser usado para evaluar u obtenga información sobre herramientas como Microsoft R Server, SQL Server, Visual Studio tools, Jupyter, profundidad de aprendizaje / m toolkits y nuevas herramientas populares de la Comunidad con un mínimo esfuerzo de instalación. Puesto que la máquina virtual de ciencias de datos se puede configurar rápidamente, se puede aplicar en otros casos de uso a corto plazo como replicar experimentación publicados, ejecutar demostraciones siguientes tutoriales de sesiones en línea o los tutoriales de conferencia.


## <a name="whats-included-in-the-data-science-vm"></a>¿Qué se incluye en la máquina virtual de ciencias de datos?

La máquina Virtual de ciencias de datos tiene muchas herramientas de ciencias datos populares ya instalado y configurado. También incluye herramientas que hacen que sea fácil trabajar con varios productos de datos y análisis de Azure. Puede explorar y generar modelos predictivos en conjuntos de datos a gran escala utilizando Microsoft R Server o SQL Server 2016. Un host de otras herramientas de la Comunidad de código abierto y de Microsoft también están blocs de notas y código incluyen, así como ejemplo. En la tabla siguiente detalla y compara los componentes principales que se incluyen en las ediciones de Windows y Linux de la máquina Virtual de ciencias de datos.


|**Edición de Windows** | **Edición de Linux** |
|----------------|---------------|
|Microsoft R Server Developer Edition | Microsoft R Server Developer Edition |
|Anaconda Python 2.7, 3.5 | Anaconda Python 2.7, 3.5 |
|Servidor de Bloc de notas de Jupyter (R, Python) | JupyterHub: Blocs de notas Jupyter multiusuario (R, Python, Julia) |
|SQL Server 2016 Developer Edition: El análisis en base de datos Scalable con servicios de R | Postgres, ardilla SQL (herramienta de base de datos), los controladores de SQL Server y línea de comandos (bcp, sqlcmd) |
|Edición de la Comunidad de Visual Studio (IDE) de 2015 </br> -Azure HDInsight (Hadoop), lago de datos, herramientas de datos de SQL Server </br> -Node.js, Python y R tools para Visual Studio |IDE y editores </br> -Eclipse con el complemento del Kit de herramientas de Azure </br> -Emacs (con See, auctex) gedit |
|Escritorio de Power BI | -- |
|Herramientas de aprendizaje de equipo </br> -Integración con el aprendizaje Azure </br> -CNTK (profundidad aprendizaje/AI) </br> -Xgboost (popular herramienta de m en competiciones de ciencias de datos) </br> -Wabbit Vowpal (estudiante rápido en línea) </br> -Cascabel (visual de los datos de inicio rápido y herramientas de análisis) </br> -Mxnet (profundidad aprendizaje/AI) | Herramientas de aprendizaje de equipo </br> -Integraciones con el aprendizaje Azure </br> -CNTK (profundidad aprendizaje/AI) </br> -Xgboost (popular herramienta de m en competiciones de ciencias de datos) </br> -Wabbit Vowpal (estudiante rápido en línea) </br> -Cascabel (visual de los datos de inicio rápido y herramientas de análisis)  |
| SDK para acceder a Azure y conjunto de aplicaciones de inteligencia de Cortana de servicios | SDK para acceder a Azure y conjunto de aplicaciones de inteligencia de Cortana de servicios |
| Herramientas de movimiento de datos y administración de recursos de Azure y datos grande: Explorador de almacenamiento de Azure, CLI, PowerShell, AdlCopy (lago de datos de Azure), AzCopy, dtui (para DocumentDB), Microsoft Data Management Gateway | Herramientas de movimiento de datos y administración de recursos de Azure y datos grande: Explorador de almacenamiento de Azure, CLI |
| GIT, complemento de Visual Studio Team Services | GIT |
| Puerto de Windows de más populares utilidades de línea de comandos de Linux/Unix accesibles a través de GitBash símbolo | -- |



## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>Cómo empezar a trabajar con Windows datos ciencias VM

- Crear una instancia de la máquina virtual de Windows, desplácese a [esta página](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) y seleccionar el botón verde de la **Máquina Virtual de crear** .
- Inicie sesión en la máquina virtual desde el escritorio remoto con las credenciales especificadas cuando creó la máquina virtual.
- Para descubrir e iniciar las herramientas disponibles, haga clic en el menú **Inicio** .


## <a name="get-started-with-the-linux-data-science-vm"></a>Introducción a la máquina virtual de Linux datos ciencia

- Crear una instancia de la máquina virtual en Linux (basado en OpenLogic CentOS) desplazarse a [esta página](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) y seleccionar el botón de la **Máquina Virtual de crear** .
- Inicie sesión en la máquina virtual desde un cliente SSH, como Putty o SSH comando, con las credenciales especificadas al crear la máquina virtual.
- En el indicador shell, escriba información de más de dsvm.
- Para un escritorio gráfica, descargar el cliente de X2Go para la plataforma cliente [aquí](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) y siga las instrucciones en el documento de Linux datos ciencias VM [aprovisionar la máquina Virtual de Linux datos ciencias](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client).


## <a name="next-steps"></a>Pasos siguientes

### <a name="for-the-windows-data-science-vm"></a>Para Windows datos ciencia VM

- Para obtener más información sobre cómo ejecutar herramientas específicas disponibles en la versión de Windows, vea [aprovisionar la máquina Virtual de Microsoft datos ciencia](machine-learning-data-science-provision-vm.md) y
-  Para obtener más información sobre cómo realizar diversas tareas necesarias para su proyecto de ciencias de datos en la máquina virtual de Windows, consulte [Diez cosas que puede hacer en la máquina Virtual de ciencias de datos](machine-learning-data-science-vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Para la ciencia Linux datos VM

- Para obtener más información sobre cómo ejecutar herramientas específicas disponibles en la versión de Linux, consulte [aprovisionar la máquina Virtual de Linux datos ciencias](machine-learning-data-science-linux-dsvm-intro.md).
- Para obtener un tutorial que muestra cómo realizar diversas tareas comunes de ciencias datos con Linux VM, vea [ciencias de datos en la máquina Virtual Linux datos ciencias](machine-learning-data-science-linux-dsvm-walkthrough.md).
