<properties
    pageTitle="Usar un espacio aislado de Hadoop para obtener información sobre Hadoop | Microsoft Azure"
    description="Para empezar a aprender sobre cómo usar el ecosistema Hadoop, puede configurar un espacio aislado de Hadoop de Hortonworks en una máquina virtual Azure. "
    keywords="emulador hadoop, hadoop recinto de seguridad"
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    authors="nitinme"
    documentationCenter=""
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="nitinme"/>

# <a name="get-started-in-the-hadoop-ecosystem-with-a-hadoop-sandbox-on-a-virtual-machine"></a>Comenzar a trabajar en el ecosistema Hadoop con un espacio aislado de Hadoop en una máquina virtual

Obtenga información sobre cómo instalar el recinto de seguridad de Hadoop de Hortonworks en una máquina virtual para obtener información sobre el ecosistema Hadoop. El espacio aislado proporciona un entorno de desarrollo local para obtener información sobre Hadoop, el sistema de archivos distribuido Hadoop (HDFS) y el envío de trabajos.

## <a name="prerequisites"></a>Requisitos previos

* [VirtualBox de Oracle](https://www.virtualbox.org/)

Una vez que está familiarizado con Hadoop, puede empezar a usar Hadoop en Azure mediante la creación de un clúster de HDInsight. Para obtener más información sobre cómo empezar a trabajar, vea [Introducción a Hadoop en HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

## <a name="download-and-install-the-virtual-machine"></a>Descargar e instalar la máquina virtual

1. Desde [http://hortonworks.com/downloads/#sandbox](http://hortonworks.com/downloads/#sandbox), seleccione el elemento de __Descargar para VIRTUALBOX__ para HDP 2,4 en espacio aislado Hortonworks. Se le pedirá registrar con Hortonworks antes de comenzar la descarga.

    ![Imagen del vínculo para descargar Hortonworks recinto de seguridad para VirtualBox](./media/hdinsight-hadoop-emulator-get-started/download-sandbox.png)

2. En la misma página web, seleccione la __Guía de instalación de VirtualBox__ para HDP 2,4 en espacio aislado Hortonworks. Esto descargue un archivo PDF que contiene las instrucciones de instalación para la máquina virtual.

    ![Ver a la Guía de instalación](./media/hdinsight-hadoop-emulator-get-started/view-install-guide.png)

## <a name="start-the-virtual-machine"></a>Iniciar la máquina virtual

1. Iniciar VirtualBox, seleccione el espacio aislado Hortonworks, seleccione __Iniciar__y __Empezar a Normal__.

    ![Inicio normal](./media/hdinsight-hadoop-emulator-get-started/normal-start.png)

2. Una vez que termine el proceso de inicio de la máquina virtual, mostrará las instrucciones de inicio de sesión. Abra un navegador web y vaya a la dirección URL que aparece (normalmente http://127.0.0.1:8888).

## <a name="set-passwords"></a>Establezca las contraseñas

1. En el paso __Introducción__ de la página de espacio aislado Hortonworks, seleccione __Opciones de vista avanzada__. Use la información de esta página para iniciar sesión en el espacio aislado mediante SSH. Use el nombre y la contraseña proporcionada.

    > [AZURE.NOTE] Si no tiene instalado un cliente SSH, puede usar el SSH basados en web situado en la máquina virtual en __http://localhost:4200 /__.

    La primera vez que se conecta a través de SSH, le pedirá para cambiar la contraseña de la cuenta raíz. Escriba una contraseña nueva, que se utilizará cuando inicie sesión mediante SSH en el futuro.

2. Una vez que inicie sesión, escriba el siguiente comando:

        ambari-admin-password-reset
    
    Cuando se le solicite, proporcione una contraseña para la cuenta de administrador de Ambari. Se utilizará cuando se tiene acceso a la interfaz de usuario de Web Ambari.

## <a name="use-the-hive-command"></a>Use el comando de la sección

1. Desde una conexión SSH al recinto de seguridad, utilice el comando siguiente para iniciar el shell de sección:

        hive

2. Una vez que se ha iniciado el shell, use las siguientes acciones para ver las tablas que se proporcionan con el recinto de seguridad:

        show tables;

3. Use las siguientes acciones para recuperar las 10 filas desde el `sample_07` tabla:

        select * from sample_07 limit 10;

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a usar Visual Studio con recinto de seguridad de Hortonworks](hdinsight-hadoop-emulator-visual-studio.md)
* [Los cables de recinto de seguridad de Hortonworks de aprendizaje](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Tutorial de Hadoop: Introducción a HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)