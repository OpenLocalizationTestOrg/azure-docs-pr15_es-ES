<properties 
    pageTitle="Instalar el Bloc de notas de Jupyter en su equipo y conectarse a un clúster HDInsight Spark | Microsoft Azure" 
    description="Obtenga información sobre cómo instalar el Bloc de notas de Jupyter localmente en el equipo y conectarse a un clúster de motor de Apache en HDInsight de Azure." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="nitinme"/>


# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-cluster-on-hdinsight-linux"></a>Instalar el Bloc de notas de Jupyter en su equipo y conéctese al clúster de motor de Apache en HDInsight Linux

En este artículo aprenderá cómo instalar Jupyter Bloc de notas, con el PySpark personalizado (por Python) y kernels motor (para Scala) con mágico motor y conecte el portátil a un clúster de HDInsight. Puede haber varias razones para instalar Jupyter en el equipo local, y puede haber algunos de los desafíos físicos. Para obtener una lista de causas y desafíos, consulte la sección [¿por qué debo instalar Jupyter en mi equipo](#why-should-i-install-jupyter-on-my-computer) al final de este artículo.

Existen tres pasos clave en la instalación de Jupyter y la magia de motor en el equipo.

* Instalar el Bloc de notas de Jupyter
* Instalar el kernel PySpark y motor con la magia de motor
* Configurar mágico motor para obtener acceso a clúster de motor en HDInsight

Para obtener más información sobre los kernels personalizados y la magia de motor disponible para los blocs de notas Jupyter con clúster de HDInsight, consulte [Kernels disponibles para los blocs de notas Jupyter con clústeres Apache motor Linux en HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

##<a name="prerequisites"></a>Requisitos previos

No son los requisitos previos descritos a continuación para instalar Jupyter. Estos son para conectar el Bloc de notas de Jupyter a un clúster de HDInsight una vez instalado el Bloc de notas.

- Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster de motor de Apache en HDInsight Linux. Para obtener instrucciones, vea [crear Apache motor clústeres en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Instalar el Bloc de notas de Jupyter en su equipo

Debe instalar Python antes de poder instalar Jupyter blocs de notas. Python y Jupyter están disponibles como parte de la [distribución de Ananconda](https://www.continuum.io/downloads). Cuando se instala Anaconda, instalar realmente una distribución de Python. Una vez instalada Anaconda, agregue la instalación Jupyter ejecutando un comando. Esta sección proporciona las instrucciones que debe seguir.

1. Descargar el [Instalador de Anaconda](https://www.continuum.io/downloads) para su plataforma y ejecute el programa de instalación. Mientras se ejecuta al Asistente de configuración, asegúrese de que seleccionar la opción de agregar Anaconda a la variable de ruta de acceso.

2. Ejecute el comando siguiente para instalar Jupyter.

        conda install jupyter

    Para obtener más información sobre la instalación Jupyter, vea [Instalar Jupyter con Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Instalar la magia de motor y kernels

Para obtener instrucciones sobre cómo instalar la magia de motor, el kernel PySpark y motor, consulte la [documentación de sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) en GitHub.

## <a name="configure-spark-magic-to-access-the-hdinsight-spark-cluster"></a>Configurar mágico motor para tener acceso al clúster HDInsight Spark

En esta sección se configura la magia de motor instalados anteriormente para conectarse a un clúster de motor de Apache debe ya ha creado en HDInsight de Azure.

1. La información de configuración de Jupyter normalmente se almacena en el directorio de inicio de los usuarios. Para localizar el directorio de inicio en cualquier plataforma de sistema operativo, escriba los siguientes comandos.

    Iniciar el shell de Python. En una ventana de comandos, escriba lo siguiente:

        python

    En la consola de Python, escriba el comando siguiente para averiguar el directorio de inicio.

        import os
        print(os.path.expanduser('~'))

2. Desplácese hasta el directorio de inicio y cree una carpeta denominada **.sparkmagic** si aún no existe.

3. Dentro de la carpeta, cree un archivo llamado **config.json** y agregue el siguiente fragmento JSON dentro de ella.

        {
          "kernel_python_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          },
          "kernel_scala_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }

4. Sustituir **{USERNAME}**, **{CLUSTERDNSNAME}**y **{BASE64ENCODEDPASSWORD}** con los valores apropiados. Puede usar un número de utilidades en el lenguaje de programación favorito o en línea para generar una contraseña de base 64 codificado para su contraseña enlazar están. Sería un fragmento de código Python simple para ejecutar desde el símbolo del sistema:

        python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"

5. Iniciar Jupyter. Use el comando siguiente en el símbolo del sistema.

        jupyter notebook

6. Compruebe que puede conectarse al clúster mediante el Bloc de notas de Jupyter y que puede usar la magia de motor disponible con el kernel. Realice los pasos siguientes.

    1. Crear un nuevo bloc de notas. En la esquina derecha, haga clic en **nuevo**. Verá el núcleo predeterminado **Python2** y los dos nuevos kernels que se instalan, **PySpark** y **motor**.

        ![Crear un nuevo bloc de notas de Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Crear un nuevo bloc de notas de Jupyter")

    
        Haga clic en **PySpark**.


    2. Ejecute el siguiente fragmento de código.

            %%sql
            SELECT * FROM hivesampletable LIMIT 5

        Si puede recuperar correctamente el resultado, se prueba la conexión con el clúster de HDInsight.

    >[AZURE.TIP] Si desea actualizar la configuración del Bloc de notas para conectarse a un clúster distinto, actualice la config.json con el nuevo conjunto de valores, como se muestra en el paso 3 anteriores. 

## <a name="why-should-i-install-jupyter-on-my-computer"></a>¿Por qué debo instalar Jupyter en mi equipo?

Puede haber varias razones por posiblemente quiera instalar Jupyter en su equipo y, a continuación, conectarlo a un clúster de motor en HDInsight.

* Aunque Jupyter blocs de notas ya están disponibles en el clúster de motor en HDInsight de Azure, instalar Jupyter en el equipo proporciona la opción de crear los blocs de notas localmente, pruebe la aplicación en un clúster de ejecución y, a continuación, cargar los blocs de notas en el clúster. Para cargar los blocs de notas en el clúster, puede cargar mediante el Bloc de notas de Jupyter que se está ejecutando o el clúster o guardarlos en la carpeta /HdiNotebooks en la cuenta de almacenamiento asociada con el clúster. Para obtener más información acerca de cómo se almacenan los blocs de notas en el clúster, vea [¿dónde se almacenan los blocs de notas de Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Con los blocs de notas disponibles de forma local, puede conectarse a clústeres motor distintos en función de sus requisitos de aplicación.
* Puede usar GitHub para implementar un sistema de control de código fuente y tiene el control de versiones para los blocs de notas. También puede tener un entorno de colaboración donde varios usuarios pueden trabajar con el mismo bloc de notas.
* Puede trabajar con blocs de notas localmente sin tener un clúster hacia arriba. Solo necesita un clúster para probar sus blocs de notas, no a administrar manualmente los blocs de notas o en un entorno de desarrollo.
* Puede resultar más fácil de configurar su entorno de desarrollo local de configurar la instalación de Jupyter en el clúster.  Puede aprovechar las ventajas del software instalado localmente sin configurar uno o más clústeres remotos.

>[AZURE.WARNING] Con Jupyter instalado en su equipo local, varios usuarios pueden ejecutar el mismo bloc de notas en el mismo clúster de motor al mismo tiempo. En este caso, se crean varias sesiones Livio. Si tiene un problema y desea depurar, es una tarea compleja para realizar un seguimiento de qué sesión Livio pertenece a que el usuario.




## <a name="seealso"></a>Vea también


* [Información general: Apache motor en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios

* [Motor con BI: realizar análisis de datos interactivas con motor en HDInsight con herramientas de inteligencia empresarial](hdinsight-apache-spark-use-bi-tools.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight para analizar usando HVAC datos de temperatura de creación](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight de predecir los resultados de la inspección de comida](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Motor Streaming: Motor de uso en HDInsight para generar aplicaciones streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análisis de registro de sitio Web con el motor en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Crear y ejecutar aplicaciones

* [Crear una aplicación independiente de Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Ejecutar trabajos de forma remota en un clúster de motor mediante Livio](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones

* [Usar el complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar el motor Scala aplicaciones](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar el complemento de herramientas de HDInsight para IntelliJ IDEA depurar aplicaciones del motor de forma remota](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utilizar blocs de notas de globo dirigible con un clúster de motor en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponibles para el Bloc de notas de Jupyter en clúster de motor de HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usar paquetes externos con blocs de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Administrar recursos

* [Administrar recursos para el clúster de motor de Apache en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

* [Realizar el seguimiento y depuración trabajos que se ejecutan en un clúster de motor de Apache en HDInsight](hdinsight-apache-spark-job-debugging.md)
