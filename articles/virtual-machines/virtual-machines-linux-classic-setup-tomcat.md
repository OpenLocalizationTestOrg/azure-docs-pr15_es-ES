<properties
    pageTitle="Configurar Apache Tomcat en una máquina virtual de Linux | Microsoft Azure"
    description="Obtenga información sobre cómo configurar Apache Tomcat7 con una máquina virtual (VM) Azure con Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="how-to-set-up-tomcat7-on-a-linux-virtual-machine-with-microsoft-azure"></a>Cómo configurar Tomcat7 en una máquina Virtual de Linux con Microsoft Azure

Apache Tomcat (o simplemente Tomcat, anteriormente también Tomcat Yakarta) es un servidor web de origen abierto y el contenedor de servlets desarrollado por la Apache Software Foundation (ASF). Tomcat implementa el servidor de Java y las especificaciones de páginas JavaServer (JSP) de Sun Microsystems y proporciona un entorno de servidor de web HTTP Java puro en el que se ejecute código Java. En la configuración más sencilla, Tomcat se ejecuta en un proceso de sistema operativo único. Este proceso ejecuta una máquina virtual de Java (JVM). Cada solicitud HTTP desde un explorador que Tomcat se procesa como un subproceso independiente en el proceso de Tomcat.  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


En esta guía, instalará tomcat7 en una imagen de Linux e implementarlo en Microsoft Azure.  

Aprenderá:  

-   Cómo crear una máquina virtual en Azure.
-   Cómo preparar la máquina virtual para tomcat7.
-   Cómo instalar tomcat7.

Se supone que el lector ya tiene una suscripción de Azure.  Si no puede registrarse para una prueba gratuita de [http://azure.microsoft.com](https://azure.microsoft.com/). Si tiene una suscripción a MSDN, consulte [precios especiales de Microsoft Azure: MSDN, MPN y ventajas de Bizspark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Para obtener más información acerca de Azure, consulte [¿Qué es Azure?](https://azure.microsoft.com/overview/what-is-azure/).

En este tema se supone que tiene conocimientos básicos de tomcat y Linux.  

##<a name="phase-1-create-an-image"></a>Fase 1: Crear una imagen
En esta fase, creará una máquina virtual mediante una imagen de Linux en Azure.  

###<a name="step-1-generate-an-ssh-authentication-key"></a>Paso 1: Generar una clave de autenticación SSH
SSH es una herramienta muy importante para los administradores del sistema. Sin embargo, la configuración de seguridad de acceso basada en una contraseña determinada humanos no es un procedimiento recomendado. Los usuarios malintencionados pueden dividir en su sistema basado en un nombre de usuario y una contraseña no segura.

La buena noticia es que hay una manera de dejar abierta acceso remoto y no debe preocuparse de contraseñas. Consiste en el método de autenticación con asimétricos. Clave privada del usuario es la que se concede la autenticación. Incluso puede bloquear la cuenta del usuario para no permitir la autenticación de contraseña por completo.

Otra ventaja de este método es que no necesita contraseñas diferentes para iniciar sesión en diferentes servidores. Puede autenticar utilizando la clave privada personal en todos los servidores, lo que evita que tenga que recordar varias contraseñas.

También es posible que inicie sesión con la que se requiere una contraseña con este método.

Siga estos pasos para generar la clave de autenticación SSH.

1.  Descargue e instale puttygen desde la siguiente ubicación: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.  Ejecute PUTTYGEN. EXE.
3.  Haga clic en **Generar** para generar las claves. En el proceso puede aumentar aleatoriedad al mover el mouse sobre el área en blanco en la ventana.  
![][1]
4.  Después del proceso de generar Puttygen.exe mostrará la clave generada. Por ejemplo:  
![][2]
5.  Seleccione y copie la clave pública en **clave** y guardarlo en un archivo llamado publicKey.pem. No haga clic en **Guardar clave pública**, porque el formato de archivo guardado de la clave pública es diferente de la clave pública que queremos.
6.  Haga clic en **Guardar clave privada** y guárdelo en un archivo llamado privateKey.ppk.

###<a name="step-2-create-the-image-in-the-azure-portal"></a>Paso 2: Crear la imagen en el portal de Azure.
En el [portal de Azure](https://portal.azure.com/), haga clic en **nuevo** en la barra de tareas para crear una imagen, elija la imagen de Linux según sus necesidades. En el ejemplo siguiente se utiliza la imagen de Ubuntu 14.04.
![][3]

Para **Nombre de Host** especificar el nombre de la dirección URL que clientes de Internet y se usará para acceder a este equipo virtual. Definir la última parte del nombre DNS, por ejemplo tomcatdemo, y Azure generará la dirección URL como tomcatdemo.cloudapp.net.  

Para la **Clave de autenticación SSH**, copie el valor de clave desde el archivo **publicKey.pem** , que contiene la clave pública generada por puttygen.  
![][4]

Configurar otras opciones según sea necesario y, a continuación, haga clic en crear.  

##<a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Fase 2: Preparar la máquina virtual para Tomcat7
En esta fase, se configura un extremo para el tráfico de tomcat y, a continuación, conectarse a su nueva máquina virtual.
###<a name="step-1-open-the-http-port-to-allow-web-access"></a>Paso 1: Abrir el puerto HTTP para permitir el acceso de web
Extremos en Azure constan de protocolo (TCP o UDP), junto con un puerto público y privado. El puerto privado es la que el servicio está escuchando en la máquina virtual. El puerto público es el que el servicio de nube Azure está escuchando externamente para el tráfico entrante, basados en Internet.  

El puerto TCP 8080 es el número de puerto predeterminado en qué escucha tomcat. Abrir este puerto con un extremo de Azure le permitirá y otro acceso a los clientes de Internet a tomcat páginas.  

1.  En el portal de Azure, haga clic en **Examinar** -> **Máquina Virtual**y, a continuación, haga clic en la máquina virtual que ha creado.  
![][5]
2.  Para agregar un punto final a su equipo virtual, haga clic en el cuadro **extremos** .
![][6]
3.  Haga clic en **Agregar**.  
    1.  Para el **punto final**, escriba un nombre para el extremo en el extremo y, a continuación, escriba 80 en **Puerto público**.  

        Si se establece en 80, no necesita incluir el número de puerto en la dirección URL que le permite acceder a tomcat. Por ejemplo, http://tomcatdemo.cloudapp.net.    

        Si se establece en otro valor, como 81, debe agregar el número de puerto para la dirección URL para tener acceso a tomcat. Por ejemplo, http://tomcatdemo.cloudapp.net:81 /.
    2.  Escriba 8080 en puerto privado. De forma predeterminada, tomcat escucha en el puerto TCP 8080. Si ha cambiado el valor predeterminado escuchar el puerto de tomcat, debe actualizar puerto privado para que sea que igual que el servicio tomcat escuchar el puerto.  
    ![][7]

4.  Haga clic en **Aceptar** para agregar el punto final a la máquina virtual.



###<a name="step-2-connect-to-the-image-you-created"></a>Paso 2: Conectar con la imagen que ha creado.
Puede elegir cualquier herramienta SSH para conectarse a su equipo virtual. En este ejemplo, usamos Putty.  

En primer lugar, obtener el nombre DNS de su máquina virtual desde el portal de Azure. **Haga clic en Examinar** -> **máquinas virtuales** -> el nombre de la máquina virtual -> **Propiedades**y a continuación, busque en el campo **Nombre de dominio** de la ventana de **Propiedades** .  

Obtener el número de puerto para conexiones de SSH desde el campo **SSH** . Aquí tenemos un ejemplo.  
![][8]

Descargar Putty desde [aquí](http://www.putty.org/) .  

Tras la descarga, haga clic en el archivo ejecutable PUTTY. EXE. Configurar las opciones básicas con el nombre de host y puerto número que se obtienen de las propiedades de la máquina virtual. Aquí tenemos un ejemplo:  
![][9]

En el panel izquierdo, haga clic en **conexión** -> **SSH** -> **Auth** y, a continuación, haga clic en **Examinar** para especificar la ubicación del archivo **privateKey.ppk** , que contiene la clave privada generado por puttygen en la fase 1: crear una imagen. Aquí tenemos un ejemplo:  
![][10]

Haga clic en **Abrir**. Puede ser una alerta por un cuadro de mensaje. Si ha configurado el nombre DNS y número de puerto correctamente, haga clic en **Sí**.
![][11]  


Verá lo siguiente:  
![][12]

Escriba el nombre de usuario especificado cuando creó la máquina virtual en la fase 1: crear una imagen. Verá algo parecido a lo siguiente:  
![][13]





##<a name="phase-3-install-software"></a>La fase 3: Instalar el software
En esta fase, instale el entorno de tiempo de ejecución de Java, tomcat y otros componentes de tomcat.  

###<a name="java-runtime-environment"></a>Entorno de tiempo de ejecución de Java
Tomcat escrito en Java. Existen dos tipos de Kit de desarrollo de Java (JDKs) (OpenJDK y Oracle JDK) y puede elegir la que desee.  

>AZURE. Nota: Ambos JDKs tienen casi el mismo código para las clases de la API de Java, pero el código de la máquina virtual es realmente diferente. Cuando se trata de bibliotecas, OpenJDK suele usar bibliotecas abiertas mientras Oracle suele utilizar los cerrados. Pero JDK Oracle tiene más clases y algunos fijan errores y Oracle JDK es más estable que OpenJDK.

Los siguientes comandos descargar el JDKs diferentes.  

Abrir jdk   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  

jdk de Oracle  

-   Para descargar el JDK desde el sitio Web de Oracle:  

        wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-   Para crear un directorio que contenga los archivos JDK:  

        sudo mkdir /usr/lib/jvm  

-   Para extraer los archivos JDK en el directorio/usr/biblioteca/jvm /:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-   Para configurar JDK Oracle como JVM predeterminado:  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####<a name="test"></a>Prueba:
Puede utilizar un comando parecido al siguiente para comprobar si el entorno de tiempo de ejecución de Java está instalado correctamente:  

    java -version  

Si instaló abrir jdk, verá un mensaje similar al siguiente:![][14]

Si instaló oracle jdk, verá un mensaje similar al siguiente:![][15]

###<a name="tomcat7"></a>Tomcat7
Con el comando siguiente para instalar tomcat7:  

    sudo apt-get install tomcat7  

Si no está utilizando tomcat7, use la variación de este comando adecuada.  

####<a name="test"></a>Prueba:

Para comprobar si tomcat7 está instalado correctamente, busque el nombre DNS de su servidor de tomcat (http://tomcatexample.cloudapp.net/ es la dirección URL de ejemplo en este artículo). Si ve una página como la siguiente, tiene tomcat7 instalado correcto.
![][16]


###<a name="install-other-tomcat-components"></a>Instalar otros componentes Tomcat
Existen otros componentes tomcat opcional que también se pueden instalar.  

Use el comando **sudo apt caché búsqueda tomcat7** para ver todos los componentes disponibles. Los comandos siguientes son ejemplos instalar algunas partes útiles.  

    sudo apt-get install tomcat7-admin      #admin web applications
    sudo apt-get install tomcat7-user         #tools to create user instances  

##<a name="phase-4-configure-tomcat"></a>Fase 4: Configurar Tomcat
En esta fase, administrar tomcat.
###<a name="start-and-stop-tomcat7"></a>Iniciar y detener tomcat7
El servidor de tomcat7 se iniciará automáticamente cuando se instala. También puede iniciar, usted mismo con el siguiente comando:   

    sudo /etc/init.d/tomcat7 start

Para dejar de tomcat7:  

    sudo /etc/init.d/tomcat7 stop

Para ver el estado de tomcat7:  

    sudo /etc/init.d/tomcat7 status

Para reiniciar los servicios de tomcat:  

    sudo /etc/init.d/tomcat7 restart

###<a name="tomcat-administration"></a>Administración de Tomcat
Puede editar el archivo de configuración de usuario de Tomcat para configurar sus credenciales de administrador con el siguiente comando:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Aquí tenemos un ejemplo:  
![][17]  

>AZURE. Nota: Crear una contraseña segura para el nombre de usuario de administración.  

Después de editar este archivo, debe reiniciar servicios de tomcat7 con el comando siguiente para asegurarse de que los cambios surtan efecto:  

    sudo /etc/init.d/tomcat7 restart  

Abra el explorador y escriba la dirección URL **http://<your tomcat server DNS name>html/manager/**. En el ejemplo de este artículo, la dirección URL es http://tomcatexample.cloudapp.net/manager/html.  

Después de conectar, verá algo similar al siguiente:  
![][18]

##<a name="common-issues"></a>Problemas comunes

###<a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>No puede acceder a la máquina virtual con Tomcat y Moodle de Internet

-   **Síntoma**  
Se está ejecutando Tomcat pero no puede ver la página predeterminada de Tomcat con el explorador.
-   **Caso de posibles raíz**   
    1.  El puerto de escucha tomcat no es igual que el puerto privado del extremo de su máquina virtual para el tráfico de tomcat.  

        Compruebe la configuración de extremo de los puertos públicos y privados y asegúrese de que el puerto privado es que igual que el servicio tomcat escuchar el puerto. Consulte la fase 1: Crear una imagen para obtener instrucciones sobre cómo configurar los extremos de la máquina virtual.  

        Para determinar el puerto de escucha tomcat, abra /etc/httpd/conf/httpd.conf (lanzamiento sombrero rojo) o /etc/tomcat7/server.xml (Debian lanzamiento). De forma predeterminada, el puerto de escucha tomcat es 8080. Aquí tenemos un ejemplo:  

            <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

        Si está utilizando una máquina virtual como Debian o Ubuntu y desea cambiar el valor predeterminado puerto de Tomcat escuchar (por ejemplo, 8081), también debe abrir el puerto para el sistema operativo. En primer lugar, abra el perfil:  

            sudo vi /etc/default/tomcat7  

        A continuación, elimine la última línea y cambie "no" a "Sí".  

            AUTHBIND=yes

    2.  El firewall ha deshabilitado el puerto de escucha de tomcat.

        Si solo puede ver la página predeterminada de Tomcat del host local, el problema es más probable que el firewall ha bloqueado el puerto que escucha por tomcat. Puede usar la herramienta w3m para explorar la página web. Los siguientes comandos instalación w3m y vaya a la página predeterminada de Tomcat:  

            sudo yum  install w3m w3m-img
            w3m http://localhost:8080  

-   **Solución**
    1. Si el servicio tomcat escucha puerto no es el mismo que el puerto privado del extremo del tráfico en la máquina virtual, es necesario cambiar el puerto privado para que sea que igual que el servicio tomcat escuchar el puerto.   

    2.  Si el problema está causado por el firewall/iptables, agregue las siguientes líneas a /etc/sysconfig/iptables:  

            -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
            -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

        Tenga en cuenta que la segunda línea solo es necesaria para el tráfico https.  

        Asegúrese de que este está por encima de todas las líneas que globalmente desea restringir el acceso, como las siguientes:  

            -A INPUT -j REJECT --reject-with icmp-host-prohibited  

        Para volver a cargar el iptables, ejecute el siguiente comando:  

            service iptables restart  

        Esto se ha probado en CentOS 6.3.

###<a name="permission-denied-when-upload-you-project-files-to-varlibtomcat7webapps"></a>Permiso denegado al cargar archivos a /var/lib/tomcat7/webapps el proyecto /  

-   **Síntoma**  
Cuando se utiliza cualquier cliente SFTP (por ejemplo, FileZilla) para conectarse a su equipo virtual y vaya a /var/lib/tomcat7/webapps/para publicar el sitio, recibe un mensaje de error similar al siguiente:  

        status: Listing directory /var/lib/tomcat7/webapps
        Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
        Error:  /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
        Error:  File transfer failed

-   **Caso de posibles raíz** No tiene permisos para tener acceso a la carpeta /var/lib/tomcat7/webapps.  
-   **Solución**  
Necesita obtener permiso de la cuenta raíz. Puede cambiar la propiedad de la carpeta de raíz en el nombre de usuario que utilizó cuando el equipo de aprovisionamiento. Aquí tenemos un ejemplo con el nombre de cuenta de azureuser:  

        sudo chown azureuser -R /var/lib/tomcat7/webapps

    Use la opción -R para aplicar los permisos para todos los archivos dentro de un directorio demasiado.  

    Tenga en cuenta que este comando también funciona para los directorios. La opción -R cambia los permisos para todos los archivos y directorios dentro del directorio. Aquí tenemos un ejemplo:  

        sudo chown -R username:group directory  

    Este comando cambia la propiedad (usuario y grupo) para todos los archivos y directorios dentro de directorio y propio directorio.  

    Solo el siguiente comando cambia los permisos del directorio de carpeta pero deja los archivos y carpetas dentro del directorio únicamente.  

        sudo chown username:group directory



[1]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
