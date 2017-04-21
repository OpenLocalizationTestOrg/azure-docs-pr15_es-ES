<properties 
    pageTitle="Aprovisionar la máquina Virtual de ciencias de datos de Microsoft | Microsoft Azure" 
    description="Configurar y crear una máquina Virtual de ciencias de datos en Azure hacer el análisis y aprendizaje del equipo." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="bradsev" />


# <a name="provision-the-microsoft-data-science-virtual-machine"></a>Aprovisionar la máquina Virtual de ciencias de datos de Microsoft

La máquina Virtual de Microsoft datos ciencias es una imagen de Azure máquina virtual () previamente instalado y configurado con conocidas herramientas que son usados para el análisis de datos y aprendizaje del equipo. Las herramientas incluidas son:

- Microsoft R Server Developer Edition
- Distribución de Python anaconda
- Jupyter Bloc de notas (R, kernels Python)
- Edición de la Comunidad de Visual Studio
- Escritorio de Power BI
- SQL Server 2016 Developer Edition
- Herramientas de aprendizaje de equipo
    - [Kit de herramientas de cálculo de red (CNTK)](https://github.com/Microsoft/CNTK): un profundo Kit de herramientas de software de Microsoft Research de aprendizaje.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): un equipo rápido auxiliares técnicas como en línea, hash, allreduce, reducciones, learning2search, activo, el sistema de aprendizaje y aprendizaje interactivo.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): una herramienta que permite la implementación rápida y precisa árbol aumentada.
    - [Cascabel](http://rattle.togaware.com/) (el R analítica herramienta para obtener fácilmente): una herramienta que permite la introducción a análisis de datos y el equipo R fácil con la exploración de datos gráfica de aprendizaje y modelado con la generación automática de código R.
    - [mxnet](https://github.com/dmlc/mxnet): un marco de aprendizaje detallado diseñado para la eficiencia y flexibilidad
- Bibliotecas de R y Python para usar en Azure el aprendizaje y otros servicios de Azure
- GIT incluidos Git fiesta para trabajar con repositorios de código de origen incluidas GitHub, Visual Studio Team Services
- Puertos de Windows varias populares Linux de línea de comandos de utilidades de (incluidos awk, sed, perl, grep, buscar, wget, enrollar etcetera) accesible a través del símbolo del sistema. 


Procedimiento ciencias datos implica la iteración en una secuencia de tareas: buscar, cargar y preprocesamiento datos, crear y probar modelos e implementar los modelos para su consumo en aplicaciones inteligentes. Científicos datos utilizan una variedad de herramientas para realizar estas tareas. Puede llevar mucho tiempo para buscar las versiones adecuadas de software y, a continuación, descargar e instalar. La máquina Virtual de Microsoft datos ciencias puede facilitar esta carga proporcionando una imagen listos para usar que se puede aprovisionar en Azure con todas las herramientas populares varias previamente instalado y configurado. 

La máquina Virtual de Microsoft datos ciencias jump-starts el proyecto de análisis. Le permite trabajar en tareas en varios idiomas incluidos R, Python, SQL y C#. Visual Studio proporciona un IDE para desarrollar y probar el código que sea fácil de usar. El SDK de Azure incluidos en la máquina virtual le permite crear sus aplicaciones con diversos servicios de plataforma de nube de Microsoft. 

No hay ningún cargo de software para esta imagen de máquina virtual de ciencias de datos. Solo paga para las cuotas de uso de Azure depende de que el tamaño de la máquina virtual que aprovisiona. Obtener más detalles sobre las cuotas de cálculo pueden encontrarse en la sección de detalles de precios en la página de la [Máquina Virtual de ciencias de datos](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) . 


## <a name="prerequisites"></a>Requisitos previos

Antes de poder crear una máquina Virtual de Microsoft datos ciencias, debe tener el siguiente:

- **Suscripción de un Azure**: para obtener uno, consulte la [versión de prueba gratuita de obtener Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Cuenta de almacenamiento de un Azure**: para crear uno, vea [crear una cuenta de almacenamiento de Azure](storage-create-storage-account.md#create-a-storage-account). Como alternativa, puede crearse la cuenta de almacenamiento como parte del proceso de creación de la máquina virtual si no desea usar una cuenta existente.


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Crear la máquina Virtual de ciencias de datos de Microsoft

Estos son los pasos para crear una instancia de la máquina Virtual de Microsoft datos ciencias:

1.  Vaya a la máquina virtual de lista en [el portal de Azure](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2.   Seleccione el botón **crear** en la parte inferior para ir a un asistente. ![configurar datos-ciencia-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.   El asistente utilizado para crear la máquina Virtual de Microsoft datos ciencias requiere **entradas** para cada uno de los **cinco pasos** enumerados en la parte derecha de esta ilustración. Estas son las entradas necesarias para configurar cada uno de estos pasos:
    
    1.   **Conceptos básicos**
        1.   **Nombre**: nombre de su servidor de ciencias de datos que está creando.
        2.   **Nombre de usuario**: id de inicio de sesión de cuenta de administrador.
        3.   **Contraseña**: contraseña de la cuenta de administrador.
        4.   **Suscripción**: si tiene más de una suscripción, seleccione el uno en el que el equipo está crearse y facturado.
        5.   **Grupo de recursos**: puede crear una nueva o usar un grupo existente.
        6.   **Ubicación**: seleccione el centro de datos que es más apropiado. Normalmente es el centro de datos que tenga la mayoría de los datos o más cercano a su ubicación física para el acceso de red más rápido.
         
    2.   **Tamaño**: seleccione uno de los tipos de servidor que satisfaga sus requisitos funcionales y las restricciones de costos. Puede obtener más opciones de tamaños VM seleccionando "Ver todo".
    
    3.   **Configuración**:
        1.   **Tipo de disco**: elija Premium si prefiere una unidad de estado sólido (SSD), elija else "Estándar".
        2.   **Cuenta de almacenamiento**: puede crear una nueva cuenta de almacenamiento de Azure en la suscripción o usar una existente en la misma *ubicación* que ha elegido en el paso de **conceptos básicos** del asistente.
        3.   **Otros parámetros**: normalmente se utilizan solo los valores predeterminados. Puede mantener el mouse sobre el vínculo informativo para obtener ayuda sobre los campos específicos en caso de que desee tener en cuenta el uso de los valores no predeterminados.

    4.   **Resumen**: Compruebe que toda la información que haya escrito es correcta.
    5.   **Comprar**: haga clic en **comprar** para iniciar el proceso de aprovisionamiento. Se proporciona un vínculo a los términos de la transacción. La máquina virtual no tiene los cargos adicionales más allá del cálculo para el tamaño del servidor que eligió en el paso de **tamaño** . 


>[AZURE.NOTE] El aprovisionamiento debe tener aproximadamente 10 a 20 minutos. El estado de la información de aprovisionamiento se muestra en el portal de Azure.

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Cómo obtener acceso a la máquina Virtual de Microsoft datos ciencia

Una vez creada la máquina virtual, puede escritorio remoto a ella usando las credenciales de cuenta de administrador que ha configurado en la sección anterior de **conceptos básicos** . 

Una vez que la máquina virtual se creará y aprovisionará, ya está listo para empezar a usar las herramientas que están instaladas y configuradas en él. Hay mosaicos del menú Inicio y los iconos del escritorio para muchas de las herramientas. 

## <a name="how-to-create-a-strong-password-on-the-jupyter-notebook-server"></a>Cómo crear una contraseña segura en el servidor de Bloc de notas de Jupyter 

Para crear su propia contraseña segura para el servidor de Bloc de notas de Jupyter instalado en el equipo, ejecute el siguiente comando desde un símbolo en la máquina Virtual de ciencias de datos.

    c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

Elija una contraseña segura cuando se le solicite.

Vea el hash de contraseña en el formato "sha1:xxxxxx" en el resultado. Copie este hash de contraseña y reemplace el valor de hash existente que está en el archivo de configuración de Bloc de notas que se encuentra en: **C:\ProgramData\jupyter\jupyter_notebook_config.py** con un nombre de parámetro ***c.NotebookApp.password***.

Reemplazar solo el elemento (xxxxxx) del valor hash existente que está dentro de las comillas. Los presupuestos y la ***sha1:*** prefijo para el valor del parámetro deben conservarse.

Por último, debe detener y reiniciar el servidor Jupyter, que se ejecuta en la máquina virtual como una tarea programada de windows denominada **Start_IPython_Notebook**. Si no se acepta la nueva contraseña después de reiniciar esta tarea, intente eliminar todos los procesos de python ejecutados desde el Administrador de tareas y, a continuación, reinicie la tarea programada. Como alternativa, intente reiniciar el equipo virtual.

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Herramientas instaladas en la máquina Virtual de Microsoft datos ciencia

### <a name="microsoft-r-server-developer-edition"></a>Microsoft R Server Developer Edition
Si desea usar R para su análisis, la máquina virtual tiene instalada la edición para programadores de Microsoft R Server. Microsoft R Server es una plataforma de análisis de manera amplia implementación de clase empresarial basándose en R que es compatible, scalable y segura. Admitir una gran variedad de estadísticas de datos grande, el modelado de predicción y capacidades de aprendizaje, R servidor es compatible con la completa gama de análisis: obtener información detallada, análisis, visualización y modelado. Uso y la ampliación Abrir origen R, Microsoft R servidor es totalmente compatible con las secuencias de comandos de R, funciones y paquetes CRAN, para analizar datos de escala de empresa. También trata las limitaciones en memoria de abrir R origen agregando el procesamiento paralelo y bloques de datos. Esto le permite ejecutar análisis de datos mucho mayor de lo que cabe en la memoria principal.  Incluido en la máquina virtual de Visual Studio Comunidad Edition contiene las herramientas de R de extensión de Visual Studio que proporciona un IDE completo para trabajar con R. También puede descargar y utilizar otros IDE, así como [RStudio](http://www.rstudio.com). 

### <a name="python"></a>Python
Para el desarrollo con Python, se ha instalado la distribución de Python Anaconda 2.7 y 3.5. Esta distribución contiene la Python base junto con unos 300 de los paquetes de análisis de matemáticas, ingeniería y datos más populares. Puede usar herramientas de Python para Visual Studio (PTVS) que se instala en la edición de la Comunidad de Visual Studio 2015 o uno de los IDE incluidos con Anaconda como inactivo o Spyder. Puede iniciar una de las siguientes opciones de búsqueda en la barra de búsqueda (**Win** + tecla**S** ).

>[AZURE.NOTE] Para señalar las herramientas de Python para Visual Studio en Anaconda Python 2.7 y 3.5, tiene que crear entornos personalizados para cada versión. Para establecer estas rutas de acceso del entorno de Visual Studio 2015 Comunidad Edition, vaya a **Herramientas** -> **Herramientas Python** -> **Entornos de Python** y, a continuación, haga clic en **+ personalizado**. 

Anaconda Python 2.7 se instala en C:\Anaconda y Anaconda Python 3.5 está instalado en c:\Anaconda\envs\py35. Para conocer los pasos detallados, consulte [documentación de PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) . 

### <a name="jupyter-notebook"></a>Bloc de notas de Jupyter
Distribución de anaconda también incluye un bloc de notas de Jupyter, un entorno para compartir código y análisis. Un servidor de Bloc de notas de Jupyter se ha configurado previamente con Python 2, 3 Python y kernels R. Hay un icono de escritorio denominado "Bloc de notas de Jupyter iniciar el explorador para tener acceso al servidor de Bloc de notas. Si se encuentra en la máquina virtual a través de escritorio remoto, también puede visitar [https://localhost:9999 /](https://localhost:9999/) para acceder al servidor de Bloc de notas de Jupyter al iniciar sesión la máquina virtual.
 
>[AZURE.NOTE] Continúe si recibe las advertencias de certificado. 

Nos hemos empaqueta los blocs de notas de ejemplo en Python y R. Los blocs de notas Jupyter muestran cómo trabajar con Microsoft R Server, SQL Server 2016 R Services (análisis de base de datos), Python y otras tecnologías de Azure una vez que inicie sesión en Jupyter. Puede ver el vínculo a los ejemplos en la página de inicio del Bloc de notas después de autenticar al Bloc de notas Jupyter utilizando la contraseña que ha creado en un paso anterior. 

### <a name="visual-studio-2015-community-edition"></a>Edición Visual Studio 2015 Comunidad
Visual Studio Community edition instalado en la máquina virtual. Es una versión gratuita de IDE popular de Microsoft que puede usar para fines de evaluación y para los equipos pequeños. Puede comprobar los términos de licencia [aquí](https://www.visualstudio.com/support/legal/mt171547).  Abra Visual Studio haciendo doble clic en el icono del escritorio o el menú **Inicio** . También puede buscar programas con **ganan** + **S** e introducir "Visual Studio". Una vez que se pueden crear proyectos en lenguajes como C#, Python, R, node.js. Complementos también se instalan que hacen que sea práctico trabajar con servicios de Azure como catálogo de datos de Azure HDInsight de Azure (Hadoop, motor) y lago de datos de Azure. 

>[AZURE.NOTE] Puede recibir un mensaje que indica que ha caducado el período de evaluación. Escriba sus credenciales de cuenta de Microsoft o crear una nueva cuenta gratuita para obtener acceso a la edición de comunidad Visual Studio. 

### <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer edition
Una versión de desarrollador de 2016 de SQL Server con servicios de R para ejecutar análisis de base de datos se proporciona en la máquina virtual. R servicios proporcionan una plataforma para desarrollar e implementar aplicaciones inteligentes. Puede usar el idioma de R enriquecido y eficaz y los paquetes de muchos de la Comunidad para crear modelos y generar predicciones para sus datos de SQL Server. Puede mantener el análisis de cerca de los datos porque R servicios (en bases de datos) integrar el idioma R con SQL Server. Esto elimina los costos y riesgos de seguridad asociados con el movimiento de datos.

>[AZURE.NOTE] La edición de desarrollador de SQL Server 2016 sólo puede utilizarse para fines de desarrollo y prueba. Necesita una licencia para ejecutar en producción. 

Puede obtener acceso a SQL server, inicie **SQL Server Management Studio**. El nombre de la máquina virtual se rellena como el nombre del servidor. Usar autenticación de Windows cuando inició sesión como administrador en Windows. Una vez que se encuentre en SQL Server Management Studio puede crear otros usuarios, cree bases de datos, importar datos y ejecutar consultas SQL. 

Para habilitar el análisis de base de datos mediante Microsoft R, ejecute el siguiente comando como una acción en SQL Server management studio después de iniciar sesión como administrador del servidor de tiempo. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
        
        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure 
Varias herramientas de Azure están instalados en la máquina virtual:

- Hay un acceso directo de escritorio para tener acceso a la documentación del SDK de Azure. 
- **AzCopy**: usar para mover datos dentro y fuera de su cuenta de Microsoft Azure almacenamiento. Para ver el uso, escriba **Azcopy** en el símbolo del sistema para ver el uso. 
- **Explorador de Microsoft Azure almacenamiento**: utilizado para navegar por los objetos que haya almacenado en su cuenta de almacenamiento de Azure y transferir datos hacia y desde el almacenamiento de Azure. Puede escribir **El Explorador de almacenamiento** en la búsqueda o buscar en el menú Inicio de Windows para obtener acceso a esta herramienta. 
- **Adlcopy**: utilizado para mover datos a lago de datos de Azure. Para ver el uso, escriba **adlcopy** en un símbolo del sistema. 
- **dtui**: usar para mover datos hacia y desde DocumentDB de Azure, una base de datos NoSQL en la nube. En el símbolo del sistema, escriba **dtui** . 
- **Microsoft Data Management Gateway**: permite mover datos entre orígenes de datos local y la nube. Se usa dentro de herramientas como generador de datos de Azure. 
- **Microsoft Azure Powershell**: una herramienta para administrar los recursos de Azure en el lenguaje de secuencias de comandos también está instalado en su máquina virtual de Powershell. 

###<a name="power-bi"></a>Power BI

Para facilitar la creación de paneles y excelentes visualizaciones, se ha instalado el **Power BI Desktop** . Use esta herramienta para extraer datos de orígenes diferentes, para crear los informes y paneles y publicarlos en la nube. Para obtener más información, consulte el sitio de [Power BI](http://powerbi.microsoft.com) . Puede encontrar Power BI desktop en el menú Inicio. 

>[AZURE.NOTE] Necesita una cuenta de Office 365 para tener acceso a Power BI. 

## <a name="additional-microsoft-development-tools"></a>Herramientas de desarrollo de Microsoft adicionales
El [**Instalador de plataforma Web de Microsoft**](https://www.microsoft.com/web/downloads/platform.aspx) puede utilizarse para ver y descargar otras herramientas de desarrollo de Microsoft. También es un acceso directo a la herramienta que se incluye en el escritorio de la máquina Virtual de Microsoft datos ciencias.  

## <a name="important-directories-on-the-vm"></a>Directorios importantes en la máquina virtual

| Elemento                          | Directorio |
| ------------------------------| ---------------- |
|Configuraciones de servidor de Jupyter Bloc de notas | C:\ProgramData\jupyter |
|Directorio de inicio de ejemplos de Bloc de notas de Jupyter| c:\dsvm\notebooks|
|Otros ejemplos | c:\dsvm\samples|
| Anaconda (predeterminado: Python 2.7) | c:\Anaconda |
| Entorno de Python 3.5 anaconda | c:\Anaconda\envs\py35|
|Directorio de instancia R Server independiente (instancia R predeterminado) | C:\Program Files\Microsoft SQL Server\130\R_SERVER |
| Directorio de instancia R servidor de base de datos | C:\Program Files\Microsoft SQL Server\MSSQL13. MSSQLSERVER\R_SERVICES |
| Otras herramientas | c:\dsvm\tools|

>[AZURE.NOTE] Instancias de Microsoft datos ciencias Máquina Virtual creado antes 1.5.0 (antes del 3 de septiembre de 2016) utilizan una estructura de directorio ligeramente diferente a la especificada en la tabla anterior. 

## <a name="next-steps"></a>Pasos siguientes
Estos son algunos pasos siguientes para continuar su aprendizaje y la exploración. 

* Explore las diversas herramientas de ciencias datos sobre la ciencia datos VM haciendo clic en el menú Inicio y la comprobación de las herramientas que aparece en el menú.
* Vaya a **C:\Program Files\Microsoft SQL Server\130\R_SERVER\library\RevoScaleR\demoScripts** de ejemplos de uso de la biblioteca de RevoScaleR en R que admita el análisis de datos a escala de empresa.  
* Lea el artículo: [10 cosas que puede hacer en la máquina Virtual de ciencias de datos](http://aka.ms/dsvmtenthings)
* Aprenda a crear soluciones analíticas de llevar a cabo sistemáticamente mediante el [Proceso de ciencias de datos de equipo](https://azure.microsoft.com/documentation/learning-paths/data-science-process/).
* Visite la [Galería de inteligencia de Cortana](http://gallery.cortanaintelligence.com) de datos y aprendizaje ejemplos análisis máquina que utilizan el conjunto de aplicaciones de inteligencia de Cortana. También proporcionamos un icono en el menú **Inicio** y en el escritorio de la máquina virtual a esta galería.

