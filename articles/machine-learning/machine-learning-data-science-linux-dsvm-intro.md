<properties
    pageTitle="Aprovisionar la máquina Virtual de ciencias de datos de Linux | Microsoft Azure"
    description="Configurar y crear una máquina Virtual de Linux datos ciencias en Azure hacer el análisis y aprendizaje del equipo."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="bradsev" />

# <a name="provision-the-linux-data-science-virtual-machine"></a>Aprovisionar la máquina Virtual de ciencias de datos de Linux

La máquina Virtual de Linux datos ciencias es una máquina virtual Azure que viene con un conjunto de herramientas preinstalados. Estas herramientas se utilizan para realizar análisis de datos y aprendizaje del equipo. Son los componentes de software clave incluidos:

- Microsoft R Server Developer Edition
- Distribución de Python anaconda (versiones 2.7 y 3.5), incluidas las bibliotecas de análisis de datos populares
- JupyterHub - un servidor de Bloc de notas Jupyter multiusuario auxiliares R, Python, kernels Julia
- Explorador de almacenamiento de Azure
- Azure línea de comandos de interfaz de administración de recursos de Azure
- Base de datos de PostgresSQL
- Herramientas de aprendizaje de equipo
    - [Kit de herramientas de cálculo de red (CNTK)](https://github.com/Microsoft/CNTK): un profundo Kit de herramientas de software de Microsoft Research de aprendizaje.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): un equipo rápido auxiliares técnicas como en línea, hash, allreduce, reducciones, learning2search, activo, el sistema de aprendizaje y aprendizaje interactivo.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): una herramienta que permite la implementación rápida y precisa árbol aumentada.
    - [Cascabel](http://rattle.togaware.com/) (el R analítica herramienta para obtener fácilmente): una herramienta que permite la introducción a análisis de datos y el equipo R fácil con la exploración de datos gráfica de aprendizaje y modelado con la generación automática de código R.
- Azure SDK de Java, Python, node.js, Ruby, PHP
- Bibliotecas de R y Python para usar en Azure el aprendizaje y otros servicios de Azure
- Herramientas de desarrollo y editores (Eclipse, Emacs, gedit, vi)

Procedimiento ciencias datos implica la iteración en una secuencia de tareas:

1. Buscar, cargar y preprocesamiento de datos
2. Crear y probar modelos
3. Implementar los modelos para su consumo en aplicaciones inteligentes

Científicos datos usar varias herramientas para realizar estas tareas. Puede llevar mucho tiempo para buscar las versiones adecuadas del software y para descargar, compilar e instalar estas versiones.

La máquina Virtual de Linux datos ciencias puede facilitar considerablemente esta carga. Puede usarlo para reactivar su proyecto de análisis. Le permite trabajar en tareas en varios idiomas, incluidos R, Python, SQL, Java y C++. Eclipse proporciona un IDE para desarrollar y probar el código que sea fácil de usar. El SDK de Azure incluidos en la máquina virtual le permite crear sus aplicaciones mediante los distintos servicios en Linux para la plataforma de nube de Microsoft. Además, tiene acceso a otros idiomas como Ruby, Perl, PHP y node.js también previamente instaladas.

No hay ningún cargo de software para esta imagen de máquina virtual de ciencias de datos. Pagar solo las cuotas de uso de hardware de Azure se evalúan en función del tamaño de la máquina virtual que se aprovisiona con la imagen de la máquina virtual. Obtener más detalles sobre las cuotas de cálculo se pueden encontrar en la [máquina virtual lista página en Azure Marketplace ](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).


## <a name="prerequisites"></a>Requisitos previos

Antes de poder crear una máquina Virtual de Linux datos ciencias, debe tener el siguiente:

- **Suscripción de un Azure**: para obtener uno, consulte la [versión de prueba gratuita de obtener Azure](https://azure.microsoft.com/free/).
- **Cuenta de almacenamiento de un Azure**: para crear uno, vea [crear una cuenta de almacenamiento de Azure](storage-create-storage-account.md#create-a-storage-account). Como alternativa, puede crearse la cuenta de almacenamiento como parte del proceso de creación de la máquina virtual, si no desea usar una cuenta existente.


## <a name="create-your-linux-data-science-virtual-machine"></a>Crear la máquina Virtual de ciencias de datos de Linux

Estos son los pasos para crear una instancia de la máquina Virtual Linux datos ciencias:

1.  Vaya a la máquina virtual de lista en el [portal de Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
2.   Haga clic en **crear** (en la parte inferior) para que aparezca el asistente. ![configurar datos-ciencia-vm](./media/machine-learning-data-science-linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3.   Las secciones siguientes proporcionan las entradas para cada uno de los pasos del asistente (enumerados en la parte derecha de la figura anterior) utilizadas para crear la máquina Virtual de Microsoft datos ciencias. Estas son las entradas necesarias para configurar cada uno de estos pasos:

    una. **Conceptos básicos**:

  - **Nombre**: nombre de su servidor de ciencias de datos que está creando.
  - **Nombre de usuario**: primera cuenta de inicio de sesión Id.
  - **Contraseña**: primera contraseña de la cuenta (puede utilizar SSH de clave pública en lugar de contraseña).
  - **Suscripción**: si tiene más de una suscripción, seleccione el uno en el que el equipo está crearse y facturado. Debe tener privilegios de creación de recursos para esta suscripción.
  - **Grupo de recursos**: puede crear una nueva o usar un grupo existente.
  - **Ubicación**: seleccione el centro de datos que es más apropiado. Normalmente es el centro de datos que tenga la mayoría de los datos o más cercano a su ubicación física para el acceso de red más rápido.

    b. **Tamaño**:

  - Seleccione uno de los tipos de servidor que satisfaga sus requisitos funcionales y las restricciones de costos. Seleccione **Ver todos** para ver más opciones de tamaño de la máquina virtual.

    c. **Configuración**:

  - **Tipo de disco**: elija **Premium** si prefiere una unidad de estado sólido (SSD). En caso contrario, elija **estándar**.
  - **Cuenta de almacenamiento**: puede crear una nueva cuenta de almacenamiento de Azure en la suscripción, o usar uno existente en la misma ubicación que ha elegido en el paso de **conceptos básicos** del asistente.
  - **Otros parámetros**: en la mayoría de los casos, usa los valores predeterminados. Para tener en cuenta los valores no predeterminados, mantenga el mouse sobre el vínculo informativo para obtener ayuda sobre los campos específicos.

    d. **Resumen**:

  - Compruebe que toda la información que haya escrito es correcta.

    e. **Comprar**:

  - Para iniciar el proceso de aprovisionamiento, haga clic en **comprar**. Se proporciona un vínculo a los términos de la transacción. La máquina virtual no tiene los cargos adicionales más allá del cálculo para el tamaño del servidor que eligió en el paso de **tamaño** .

El aprovisionamiento debe tener aproximadamente 10 a 20 minutos. El estado de la información de aprovisionamiento se muestra en el portal de Azure.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Cómo obtener acceso a la máquina Virtual de Linux datos ciencia

Después de crea la máquina virtual, puede firmar a ella mediante SSH. Use las credenciales de cuenta que ha creado en la sección de **conceptos básicos** del paso 3 para la interfaz de texto. En Windows, puede descargar una herramienta de cliente SSH como [Putty](http://www.putty.org). Si prefiere un escritorio gráfico (sistema de Windows de X), puede usar X11 reenvío en Putty o instalar al cliente de X2Go.

>[AZURE.NOTE] El cliente de X2Go realizado significativamente mejor que X11 reenviar en la prueba. Se recomienda usar al cliente de X2Go una interfaz gráfica de escritorio.


## <a name="installing-and-configuring-x2go-client"></a>Instalación y configuración de cliente de X2Go

La VM Linux ya está aprovisionado con X2Go server y está listo para aceptar las conexiones de cliente. Para conectarse a la versión de escritorio gráficos de Linux VM, realice lo siguiente en el cliente:

1. Descargue e instale al cliente de X2Go para la plataforma cliente desde [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Ejecutar al cliente de X2Go y seleccione **Nueva sesión**. Se abre una ventana de configuración con múltiples fichas. Especifique los siguientes parámetros de configuración:
    * **Ficha de sesión**:
        - **Host**: el nombre de host o la dirección IP de su máquina virtual ciencias de datos de Linux.
        - **Inicio de sesión**: nombre de usuario en la VM Linux.
        - **Puerto de SSH**: Déjelo en 22, el valor predeterminado.
        - **Tipo de sesión**: cambie el valor a XFCE. Actualmente la VM Linux solo admite escritorio XFCE.
    * **Ficha de medios**: puede desactivar la compatibilidad con sonido y el cliente de impresión si no necesita usarlas.
    * **Carpetas compartidas**: si quiere directorios de los equipos cliente en la VM Linux, agregue los directorios del equipo cliente que desea compartir con la máquina virtual en esta pestaña.

Después de iniciar sesión en la máquina virtual mediante el cliente SSH o el escritorio gráfica XFCE a través del cliente de X2Go, ya está listo para empezar a usar las herramientas que están instaladas y configuradas en la máquina virtual. En XFCE, puede ver los accesos directos del menú de aplicaciones y escritorio iconos para muchas de las herramientas.


## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Herramientas instaladas en la máquina Virtual Linux datos ciencia

### <a name="microsoft-r-open"></a>Abrir Microsoft R
R es uno de los idiomas más populares de análisis de datos y aprendizaje del equipo. Si desea usar R para su análisis, la máquina virtual tiene Microsoft R abrir (MRO) con la biblioteca central de matemáticas (MKL). La MKL optimiza operaciones matemáticas comunes en algoritmos analítica. MRO es compatible con CRAN R al 100 por cien y cualquiera de las bibliotecas de R publicadas en CRAN se puede instalar en la MRO. Puede editar los programas de R en uno de los editores predeterminados, como vi, Emacs o gedit. También puede descargar y utilizar otros IDE, como [RStudio](http://www.rstudio.com). Para su comodidad, se proporciona una secuencia de comandos simple (installRStudio.sh) en el directorio **/dsvm/tools** que se instala RStudio. Si está utilizando el editor de Emacs, nota que el Emacs empaquetar See (Emacs habla estadísticas), que simplifica el trabajo con archivos de R dentro del editor de Emacs ha preinstalado.

Para iniciar R, simplemente escriba **R** en el shell. Se abrirá un entorno interactivo. Para desarrollar el programa R, suele utilizar un editor como vi o Emacs o gedit y, a continuación, ejecute los scripts de R. Si instala RStudio, tiene un entorno de IDE gráfico completa para desarrollar el programa R.

También es una secuencia de comandos de R para instalar los [paquetes de principio 20 R](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) si lo desea. Se pueden ejecutar esta secuencia de comandos después de que se encuentra en la interfaz interactiva R, que se pueden especificar (como se mencionó) escribiendo **R** en la consola.  

### <a name="python"></a>Python
Para el desarrollo con Python, se ha instalado la distribución de Python Anaconda 2.7 y 3.5. Esta distribución contiene la Python base junto con unos 300 de los paquetes de análisis de matemáticas, ingeniería y datos más populares. Puede usar los editores de texto de forma predeterminada. Además, puede usar Spyder, un IDE Python que se incluye con las distribuciones de Python Anaconda. Spyder necesita un gráfico de escritorio o X11 reenvío. En el escritorio gráfico se proporciona un acceso directo a Spyder.

Puesto que tenemos Python 2.7 y 3.5, debe activar específicamente la versión deseada de Python que desee trabajar en la sesión actual. El proceso de activación, Establece la variable de ruta de acceso a la versión deseada de Python.

Para activar Python 2.7, ejecute lo siguiente desde la consola de:

    source /anaconda/bin/activate root

Python 2.7 se instala en */anaconda/bin*.

Para activar Python 3.5, ejecute lo siguiente desde la consola de:

    source /anaconda/bin/activate py35


Python 3.5 se instala en */anaconda/envs/py35/bin*.

Para llamar a una sesión interactiva de Python, escriba **python** en la consola. Si se encuentran en una interfaz gráfica o tiene X11 reenvío conjunto hacia arriba, puede escribir **spyder** para iniciar el IDE Python.

### <a name="jupyter-notebook"></a>Bloc de notas de Jupyter

La distribución de Anaconda también incluye un bloc de notas de Jupyter, un entorno para compartir código y análisis. El Bloc de notas de Jupyter se tiene acceso a través de JupyterHub. Iniciar sesión con su nombre de usuario de Linux local y la contraseña.

El servidor de Bloc de notas de Jupyter se configuró previamente con Python 2, 3 Python y kernels R. Hay un icono de escritorio denominado "Bloc de notas de Jupyter" iniciar el explorador para tener acceso al servidor de Bloc de notas. Si se encuentra en la máquina virtual mediante un cliente SSH o X2Go, también puede visitar [https://localhost:8000 /](https://localhost:8000/) para acceder al servidor de Bloc de notas de Jupyter.

>[AZURE.NOTE] Continúe si recibe las advertencias de certificado.

El servidor de Bloc de notas de Jupyter puede tener acceso desde cualquier host. Escriba *https://\<nombre VM DNS o la dirección IP\>: 8000 /*

>[AZURE.NOTE] Puerto 8000 se abre en el firewall de forma predeterminada cuando se aprovisiona la máquina virtual.

Nos hemos empaquetados blocs de notas de ejemplo--Python en una y otra en R. Puede ver el vínculo a los ejemplos en la página de inicio del Bloc de notas después de autenticar al Bloc de notas Jupyter utilizando su nombre de usuario de Linux local y la contraseña. Puede crear un nuevo bloc de notas, seleccione **nuevo**y, a continuación, el núcleo de idioma correspondiente. Si no ve el botón **nuevo** , haga clic en el icono de **Jupyter** en la parte superior izquierda para ir a la página principal del servidor de Bloc de notas.


### <a name="ides-and-editors"></a>IDE y editores

Tiene una opción de varios editores de código. Esto incluye vi/VIM, Emacs, gEdit y Eclipse. gEdit Eclipse son editores gráficos y debe haber iniciado sesión en un equipo de escritorio gráfica usarlas. Los editores tienen escritorio y aplicaciones accesos directos del menú Inicio de ellos.

**VIM** y **Emacs** son editores de texto. En Emacs, nos hemos instalado un paquete de complementos denominado Emacs habla estadísticas (See) que facilita el trabajo con R dentro del editor de Emacs. Puede encontrar más información en [See](http://ess.r-project.org/).

**Eclipse** es un código abierto, IDE extensible que admita varios idiomas. La edición de los desarrolladores de Java es la instancia instalada en la máquina virtual. Existen complementos para varios idiomas populares que se pueden instalar para ampliar el entorno Eclipse. También tenemos un complemento instalado en Eclipse llamado **Kit de herramientas de Azure para Eclipse**. Permite crear, desarrollar, probar e implementar aplicaciones de Azure mediante el entorno de desarrollo Eclipse que admita idiomas como Java. También es un **Azure SDK de Java** que permite el acceso a diferentes servicios de Azure desde dentro de un entorno de Java. Obtener más información sobre el Kit de herramientas de Azure para Eclipse se puede encontrar en [El Kit de herramientas de Azure para Eclipse](../azure-toolkit-for-eclipse.md).

**Látex** se instala mediante el paquete de texlive junto con un paquete de Emacs complemento [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) , lo que simplifica la creación de los documentos látex Emacs.  

### <a name="databases"></a>Bases de datos

#### <a name="postgres"></a>Postgres
La base de datos de origen abierto **Postgres** está disponible en la máquina virtual, con los servicios que se ejecutan y initdb ya han completado. Necesitará crear bases de datos y los usuarios. Para obtener más información, consulte la [documentación de Postgres](https://www.postgresql.org/docs/).  

####  <a name="graphical-sql-client"></a>Gráfica SQL client
Se ha proporcionado **Ardilla SQL**, un cliente gráfico de SQL, para conectarse a diferentes bases de datos (como Microsoft SQL Server, Postgres y MySQL) y ejecutar consultas SQL. Se puede ejecutar desde una sesión de escritorio gráfica (con el cliente de X2Go, por ejemplo). Para llamar a ardilla SQL, puede iniciarlo desde el icono en el escritorio o ejecutar el comando siguiente en la consola.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Antes de utilizar por primera vez, configure los controladores y los alias de la base de datos. Los controladores JDBC se encuentran en:

*/usr/share/Java/jdbcdrivers*

Para obtener más información, vea [Ardilla SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Herramientas de línea de comandos para tener acceso a Microsoft SQL Server

El paquete de controladores ODBC para SQL Server también se suministra con dos herramientas de línea de comandos:

**bcp**: masa de utilidad bcp copia datos entre una instancia de Microsoft SQL Server y un archivo de datos en un formato especificado por el usuario. La herramienta bcp puede usarse para importar grandes cantidades de filas nuevas en tablas de SQL Server, o para exportar datos de tablas en archivos de datos. Para importar datos en una tabla, debe usar un archivo de formato creado para la tabla o comprender la estructura de la tabla y los tipos de datos que son válidos para sus columnas.

Para obtener más información, vea [Conectar con bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**SQLCMD**: puede especificar instrucciones Transact-SQL con la utilidad sqlcmd, así como procedimientos de sistema y archivos en el símbolo de comandos. Esta herramienta utiliza ODBC para ejecutar lotes Transact-SQL.

Para obtener más información, vea [Conectar con sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

>[AZURE.NOTE] Existen algunas diferencias en esta utilidad entre plataformas Linux y Windows. Consulte la documentación para obtener información detallada.


#### <a name="database-access-libraries"></a>Bibliotecas de acceso de base de datos

Hay bibliotecas disponibles en R y Python a bases de datos de access.

- En R, el paquete **RODBC** o un paquete de **dplyr** permite la consulta o ejecutar instrucciones SQL en el servidor de base de datos.
- En Python, la biblioteca de **pyodbc** proporciona acceso a la base de datos con ODBC como la capa subyacente.  

Para acceder a **Postgres**:

- Desde R: Utilice el paquete **RPostgreSQL**.
- Desde Phyton: Usar la biblioteca **psycopg2** .


### <a name="azure-tools"></a>Herramientas de Azure
Las herramientas de Azure siguientes están instaladas en la máquina virtual:

- **Interfaz de línea de comandos de Azure**: CLI Azure le permite crear y administrar recursos de Azure a través de comandos de shell. Para llamar a las herramientas de Azure, escriba **Ayuda azure**. Para obtener más información, consulte la [página de la documentación de Azure CLI](../virtual-machines-command-line-tools.md).
- **Explorador de Microsoft Azure almacenamiento**: Microsoft Azure almacenamiento Explorer es una herramienta gráfica que se utiliza para navegar por los objetos que haya almacenado en su cuenta de almacenamiento de Azure y para cargar y descargar los datos de Azure BLOB. Explorador de almacenamiento puede tener acceso desde el icono de acceso directo de escritorio. Puede invocarla desde un indicador de shell escribiendo **StorageExplorer**. Debe haber iniciado sesión desde un cliente de X2Go o tiene X11 reenvío conjunto hacia arriba.
- **Bibliotecas de Azure**: los siguientes son algunas de las bibliotecas preinstaladas.

 - **Python**: las bibliotecas de Azure el en Python que se instala son **azure**, **azureml**, **pydocumentdb**y **pyodbc**. Con las tres primeras bibliotecas, puede obtener acceso a servicios de almacenamiento de Azure, aprendizaje del equipo de Azure y Azure DocumentDB (NoSQL base de datos de Azure). La biblioteca de la cuarta, pyodbc (junto con el controlador ODBC para Microsoft SQL Server), permite el acceso a SQL Server, la base de datos de SQL Azure y el almacenamiento de datos de SQL Azure desde Phyton mediante una interfaz ODBC. Especifique la **lista de puntos** para ver todas las bibliotecas de la lista. Asegúrese de ejecutar este comando en entornos de 3,5 y 2.7 Python.
 - **R**: bibliotecas relacionados con la Azure en R instaladas son **AzureML** y **RODBC**.
 - **Java**: la lista de bibliotecas de Azure Java puede encontrarse en el directorio **/dsvm/sdk/AzureSDKJava** en la máquina virtual. Las bibliotecas claves son Azure almacenamiento y administración de las API, DocumentDB y JDBC controladores para SQL Server.  

Puede acceder al [portal de Azure](https://portal.azure.com) desde el explorador Firefox preinstalado. En el portal de Azure, puede crear, administrar y supervisar los recursos de Azure.

### <a name="azure-machine-learning"></a>Aprendizaje del equipo de Azure

Aprendizaje de máquina Azure es un servicio de nube de totalmente administrado que le permite crear, implementar y compartir las soluciones de análisis predictiva. Crear su experimentación y modelos de Azure máquina aprendizaje Studio. Puede tener acceso desde un explorador web en la máquina virtual de ciencias de datos con una visita a [Microsoft Azure aprendizaje](https://studio.azureml.net).

Después de iniciar sesión en Azure máquina aprendizaje Studio, tendrá acceso a un lienzo experimentación donde puede crear un flujo lógico de los algoritmos de aprendizaje del equipo. También tienen acceso a un bloc de notas de Jupyter hospedado en Azure el aprendizaje y puede trabajar sin problemas con la experimentación en Studio de aprendizaje del equipo. Controle la máquina modelos integrados ajustando en una interfaz de servicios web de aprendizaje. Esto permite a los clientes escritos en cualquier lenguaje invocar predicciones desde el equipo modelos de aprendizaje. Para obtener más información, consulte la [documentación de aprendizaje del equipo](https://azure.microsoft.com/documentation/services/machine-learning/).

Puede crear también los modelos de R o Python en la máquina virtual y, a continuación, implementarlo en producción a Azure el aprendizaje. Nos hemos instalado bibliotecas en R (**AzureML**) y Python (**azureml**) para habilitar esta funcionalidad.

Para obtener información sobre cómo implementar modelos en R y Python en aprendizaje de máquina de Azure, consulte [Diez cosas que puede hacer en la máquina Virtual de ciencias de datos](machine-learning-data-science-vm-do-ten-things.md) (en particular, la sección "crear modelos con R o Python y operativa ellos mediante el aprendizaje Azure").

>[AZURE.NOTE] Se escribieron estas instrucciones para la versión de Windows de la máquina virtual de ciencias de datos. Pero proporciona la información sobre cómo implementar modelos a Azure máquina aprendizaje hay aplicable VM Linux.

### <a name="machine-learning-tools"></a>Herramientas de aprendizaje de equipo

La máquina virtual se suministra con machine unos algoritmos que se han compilado previamente y previamente instalado localmente y herramientas de aprendizaje. Se incluyen:

* **CNTK** (Cálculo red Kit de herramientas de Microsoft Research): un profundo Kit de herramientas de aprendizaje.
* **Vowpal Wabbit**: un algoritmo de aprendizaje rápido en línea.
* **xgboost**: una herramienta que proporciona algoritmos de árbol aumentada optimizada.
* **Python**: Anaconda Python incluye algoritmos de aprendizaje de máquina con bibliotecas, como obtener Scikit. Puede instalar otras bibliotecas mediante la `pip install` comando.
* **R**: una amplia biblioteca de funciones de aprendizaje del equipo está disponible para R. Algunas de las bibliotecas que están preinstaladas son lm, glm, randomForest, rpart. Otras bibliotecas de se pueden instalar ejecutando:

        install.packages(<lib name>)

Aquí encontrará más información acerca de las herramientas de aprendizaje de máquina tres primera en la lista.

#### <a name="cntk"></a>CNTK
Se trata de un origen abierto, profundo Kit de herramientas de aprendizaje. Es una herramienta de línea de comandos (cntk) y ya está en la ruta de acceso.

Para ejecutar un ejemplo básico, ejecute los comandos siguientes en el shell:

    # Copy samples to your home directory and execute cntk
    cp -r /dsvm/tools/CNTK-2016-02-08-Linux-64bit-CPU-Only/Examples/Other/Simple2d cntkdemo
    cd cntkdemo/Data
    cntk configFile=../Config/Simple.cntk

El resultado del modelo se encuentra en *~/cntkdemo/Output/Models*.

Para obtener más información, vea la sección CNTK de [GitHub](https://github.com/Microsoft/CNTK)y [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).


#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit es una máquina sistema que usa técnicas como en línea, hash, allreduce, reducciones, learning2search, activas, aprendizaje y aprendizaje interactivo.

Para ejecutar la herramienta en un ejemplo muy básico, haga lo siguiente:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Existen otros, más grandes demostraciones en el directorio. Para obtener más información sobre VW, consulte [esta sección de GitHub](https://github.com/JohnLangford/vowpal_wabbit)y [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost
Se trata de una biblioteca que está diseñada y optimizada para algoritmos aumentada (árbol). El objetivo de esta biblioteca es insertar los límites de cálculo de equipos en los extremos necesarios para proporcionar a gran escala árbol incremento que scalable, portátil y precisa.

Se proporciona como una línea de comandos, así como una biblioteca de R.

Para usar esta biblioteca en R, puede iniciar una sesión de R interactiva (escribiendo **R** en el shell) y cargar la biblioteca.

Este es un ejemplo sencillo que se puede ejecutar en el símbolo del sistema de R:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Para ejecutar la línea de comandos xgboost, estos son los comandos para ejecutar en el shell:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


Un archivo .model se escribe en el directorio especificado. Puede encontrar información acerca de este ejemplo de demostración [en GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Para obtener más información sobre xgboost, consulte la [página de la documentación de xgboost](https://xgboost.readthedocs.org/en/latest/)y su [repositorio de Github](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Sonajero
Sonajero (o de lápiz **T**de **R** **A**nalytical **T** **L**ganar **E**asily) usa la exploración gráfico de datos y modelado. Presenta estadísticos y visuales resúmenes de los datos de las transformaciones que se puede modelar fácilmente, crea sin supervisión y supervisados modelos de los datos, presenta el rendimiento de modelos gráficamente, y conjuntos de datos nuevos de la puntuación. También genera código R, replicar las operaciones en la interfaz de usuario que se pueden ejecutar directamente en R o utilizar como punto de partida para realizar un análisis.

Para ejecutar sonajero, debe estar en una gráfico de inicio de sesión en sesión de escritorio. En terminal, escriba ```R``` a entrar en el entorno de R. En el símbolo de R, escriba los siguientes comandos:

    library(rattle)
    rattle()

Ahora una interfaz gráfica se abre con un conjunto de fichas. Estos son los pasos de inicio rápido en sonajero necesario para utilizar un conjunto de datos de ejemplo meteorología y cree un modelo. En algunos de los pasos siguientes, deberá instalar y cargar algunos paquetes R necesarios que ya no están en el sistema automáticamente.

>[AZURE.NOTE] Si no tiene acceso para instalar el paquete en el directorio del sistema (opción predeterminada), puede ver un mensaje en la ventana de la consola de R instalar paquetes a su biblioteca personal. Responder *y* si ve estas indicaciones.

1. Haga clic en **Ejecutar**.
2. Un cuadro de diálogo aparece, que le pregunta si desea usar el conjunto de datos de ejemplo meteorología. Haga clic en **Sí** para cargar el ejemplo.
3. Haga clic en la ficha **modelo** .
4. Haga clic en **Ejecutar** para crear un árbol de decisión.
5. Haga clic en **dibujar** para mostrar el árbol de decisión.
6. Haga clic en el botón de radio **bosque** y haga clic en **Ejecutar** para crear un bosque aleatorio.
7. Haga clic en la pestaña de **evaluación** .
8. Haga clic en el botón de radio de **riesgo** y haga clic en **Ejecutar** para mostrar dos trazados de rendimiento de riesgo (acumulado).
9. Haga clic en la ficha de **registro** para mostrar el código de generar R para las operaciones anteriores.
(Debido a un error en la versión actual de sonajero, necesita insertar un *#* carácter delante *Exportar... este registro* en el texto del registro.)
10. Haga clic en el botón **Exportar** para guardar el archivo de script de R denominado *weather_script. R* a la carpeta principal.

Puede salir sonajero y R. Ahora puede modificar el script R generado o usarla como está ejecutarlo en cualquier momento para repetir todo lo que se ha realizado dentro de la interfaz de usuario cascabel. Especialmente para principiantes en R, esto es una forma sencilla de realizar análisis rápidamente y aprendizaje de una sencilla interfaz gráfica al generar automáticamente un código de R para modificar o el de la máquina.


## <a name="next-steps"></a>Pasos siguientes
Esto es cómo puede continuar su aprendizaje y la exploración:

* El tutorial de [ciencias de datos en la máquina Virtual Linux datos ciencias](machine-learning-data-science-linux-dsvm-walkthrough.md) muestra cómo realizar varias tareas comunes de ciencias datos con Linux datos ciencias VM aprovisionado aquí. 
* Explorar las distintas herramientas de ciencias datos sobre la ciencia datos VM probando las herramientas descritas en este artículo. También puede ejecutar *más de dsvm de información* en la consola dentro de la máquina virtual para una introducción básica y punteros para obtener más información acerca de las herramientas instaladas en la máquina virtual.  
* Aprenda a crear soluciones analíticas de llevar a cabo sistemáticamente mediante el [Proceso de ciencias de datos de equipo](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Visite la [Galería de Cortana análisis](http://gallery.cortanaanalytics.com) de datos y aprendizaje ejemplos análisis máquina que utilizan el conjunto de análisis de Cortana.
