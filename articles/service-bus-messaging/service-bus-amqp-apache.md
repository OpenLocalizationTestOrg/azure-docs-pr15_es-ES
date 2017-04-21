<properties 
    pageTitle="Cómo instalar Apache Qpid protones-C en una VM Linux | Microsoft Azure"
    description="Cómo crear una máquina virtual Linux de CentOS usando máquinas virtuales de Azure y cómo crear e instalar la biblioteca Apache Qpid protones-C."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="install-apache-qpid-proton-c-on-an-azure-linux-vm"></a>Instalar Apache Qpid protones-C en una máquina virtual Linux de Azure

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Esta sección muestra cómo crear una máquina virtual Linux de CentOS usando máquinas virtuales de Azure y cómo descargar, diseñar e instalar la biblioteca de Apache Qpid protones-C junto con los enlaces de lenguaje Python y PHP. Después de completar estos pasos, podrá ejecutar los ejemplos de Python y PHP incluidos en esta guía.

El primer paso se realiza con el [portal clásica Azure][]. La siguiente captura de pantalla muestra cómo se crea una máquina virtual de CentOS denominada "scott centos":

![Protones en una máquina virtual Linux de Azure][0]

Si ya dispone, el portal muestra lo siguiente:

![Protones en una máquina virtual Linux de Azure][1]

Para iniciar sesión en el equipo, debe conocer el puerto de extremo de SSH. Puede obtener este valor desde el [portal clásica Azure][] seleccionando la VM recién creada y haciendo clic en la pestaña **extremos** . La siguiente captura de pantalla muestra que el puerto SSH público de este equipo está 57146.

![Protones en una máquina virtual Linux de Azure][2]

Ahora puede conectarse con el equipo mediante SSH. Este ejemplo usa la herramienta masilla, como se muestra en la siguiente captura de pantalla:

![Protones en una máquina virtual Linux de Azure][3]

Para las aplicaciones Python y PHP, este ejemplo usa las bibliotecas de cliente de protones de Apache. Estas bibliotecas están disponibles para su descarga desde [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). El archivo Léame en el paquete de distribución explica los pasos necesarios para instalar las dependencias y generar protones. Aquí es un resumen de los pasos:

1.  Editar el archivo de configuración de yum (/ etc/yum.conf) y comentario la exclusión actualizaciones a los encabezados de kernel (\# excluir = kernel\*). Esto es necesario para instalar el compilador gcc.

2.  Instalar paquetes de requisitos previos:

    ```
    # required dependencies 
    yum install gcc cmake libuuid-devel
    
    # dependencies needed for ssl support
    yum install openssl-devel
    
    # dependencies needed for bindings
    yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
    
    # dependencies needed for python docs
    yum install epydoc
    ```

1.  Descargar la biblioteca protones:

    ```
    [azureuser@this-user ~]$ wget http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    --2016-04-17 14:45:03--  http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    Resolving apache.panu.it (apache.panu.it)... 81.208.22.71
    Connecting to apache.panu.it (apache.panu.it)|81.208.22.71|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 868226 (848K) [application/x-gzip]
    Saving to: ‘qpid-proton-0.9.tar.gz’
    
    qpid-proton-0.9.tar.gz                               
    
    100%[====================================================================================================================>] 847.88K   102KB/s    in 8.4s    
    
    2016-04-17 14:45:12 (101 KB/s) - ‘qpid-proton-0.9.tar.gz’ saved [868226/868226]
    ```

1.  Extraer el código de protones desde el archivo de distribución:

    ```
    tar xvfz qpid-proton-0.9.tar.gz
    ```

1.  Crear e instalar el código con los pasos siguientes, tomados del archivo Léame:

    ```
    From the directory where you found this README file:    
    
    mkdir build cd build
            
    # Set the install prefix. You may need to adjust depending on your      
    # system.       
    cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
            
    # Omit the docs target if you do not wish to build or install       
    # documentation.        
    make all docs
            
    # Note that this step will require root privileges.     
    make install
    ```

Después de realizar estos pasos, protones está instalado en el equipo y listos para usarse.

## <a name="next-steps"></a>Pasos siguientes

¿Listo para obtener más información? Visite el siguiente vínculo:

- [Información general de AMQP de Bus de servicio][]

[Información general de AMQP de Bus de servicio]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[Portal de clásico de Azure]: http://manage.windowsazure.com


