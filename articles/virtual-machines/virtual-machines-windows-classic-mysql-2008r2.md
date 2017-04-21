<properties
    pageTitle="Crear una VM que ejecuta MySQL | Microsoft Azure"
    description="Crear una máquina virtual Azure ejecutan Windows Server 2012 R2 y la base de datos MySQL utiliza el modelo de implementación clásico."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="cynthn"/>


# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2012-r2"></a>Instalar MySQL en una máquina virtual creada con el modelo de implementación clásica que ejecutan Windows Server 2012 R2

[MySQL](http://www.mysql.com) es popular Abrir origen de una base de datos SQL. En este tutorial se muestra cómo instalar y ejecutar la versión de la Comunidad de MySQL 5.6.23 como servidor MySQL en una máquina virtual ejecuta Windows Server 2012 R2. Para obtener instrucciones sobre cómo instalar MySQL en Linux, consulte: [cómo instalar MySQL en Azure](virtual-machines-linux-mysql-install.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="create-a-virtual-machine-running-windows-server-2012-r2"></a>Crear una máquina virtual ejecuta Windows Server 2012 R2

Si todavía no tiene una máquina virtual que ejecutan Windows Server 2012 R2, puede usar este [tutorial](virtual-machines-windows-classic-tutorial.md) para crear la máquina virtual. 

## <a name="attach-a-data-disk"></a>Adjuntar un disco de datos

Después de crear la máquina virtual, opcionalmente, puede adjuntar un disco de datos adicionales. Se recomienda para cargas de trabajo de producción y para evitar quedarse sin espacio en la unidad de sistema operativo (C:), que incluye el sistema operativo.

Consulte [cómo adjuntar un disco de datos a una máquina virtual de Windows](virtual-machines-windows-classic-attach-disk.md) y siga las instrucciones para adjuntar un disco vacío. Establezca la configuración de caché de host en **Ninguno** o **de solo lectura**.

## <a name="log-on-to-the-virtual-machine"></a>Inicie sesión en la máquina virtual

A continuación, deberá [iniciar sesión en la máquina virtual](virtual-machines-windows-classic-connect-logon.md) para que pueda instalar MySQL.

##<a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Instalar y ejecutar MySQL Community Server en la máquina virtual

Siga estos pasos para instalar, configurar y ejecutar la versión de la Comunidad de servidor MySQL:

> [AZURE.NOTE] Estos pasos son para la 5.6.23.0 versión de la Comunidad de MySQL y Windows Server 2012 R2. Su experiencia puede ser diferente para diferentes versiones de MySQL o Windows Server.

1.  Después de que se ha conectado a la máquina virtual mediante Escritorio remoto, haga clic en **Internet Explorer** desde la pantalla de inicio.
2.  Seleccione el botón **Herramientas** en la esquina superior derecha (el icono de rueda cogged) y, a continuación, haga clic en **Opciones de Internet**. Haga clic en la pestaña **seguridad** , haga clic en el icono **Sitios de confianza** y, a continuación, haga clic en el botón **sitios** . Agregar http://*. mysql.com a la lista de sitios de confianza. Haga clic en * *Cerrar**y, a continuación, haga clic en * *Aceptar**.
3.  En la barra de direcciones de Internet Explorador, escriba http://dev.mysql.com/downloads/mysql/.
4.  Utilizar el sitio de MySQL para buscar y descargar la versión más reciente del instalador MySQL para Windows. Al elegir al instalador de MySQL, descargue la versión que tiene la completa (por ejemplo, la mysql-installer-Comunidad-5.6.23.0.msi con un tamaño de archivo de 282.4 MB) del conjunto de archivos y a continuación, guarde el programa de instalación.
5.  Cuando el instalador haya finalizado la descarga, haga clic en **Ejecutar** para iniciar la instalación.
6.  En la página **Contrato de licencia** , acepte el contrato de licencia y haga clic en **siguiente**.
7.  En la página **Elegir un tipo de instalación** , haga clic en el tipo de instalación que desee y, a continuación, haga clic en **siguiente**. Pasos siguientes asume la selección del tipo de configuración de **servidor únicamente** .
8.  En la página de **instalación** , haga clic en **Ejecutar**. Cuando termine la instalación, haga clic en **siguiente**.
9.  En la página **Configuración del producto** , haga clic en **siguiente**.
10. En la página **tipo y las redes** , especifique las opciones configuración deseada tipo y la conectividad, incluido el puerto TCP si es necesario. Seleccione **Mostrar opciones avanzadas**y, a continuación, haga clic en **siguiente**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_TypeNetworking.png)

11. En la página **cuentas y funciones** , especifique una contraseña segura de raíz de MySQL. Agregar cuentas de usuario de MySQL adicionales según sea necesario y, a continuación, haga clic en **siguiente**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AccountsRoles_Filled.png)

12. En la página de **Servicio de Windows** , especifique los cambios a la configuración predeterminada para ejecutar el servidor MySQL como un servicio de Windows según sea necesario y, a continuación, haga clic en **siguiente**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_WindowsService.png)

13. En la página de **Opciones avanzadas** , especifique los cambios en las opciones de registro según sea necesario y, a continuación, haga clic en **siguiente**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AdvOptions.png)

14. En la página **Aplicar configuración del servidor** , haga clic en **Ejecutar**. Una vez completados los pasos de configuración, haga clic en **Finalizar**.
15. En la página **Configuración del producto** , haga clic en **siguiente**.
16. En la página **Instalación completada** , haga clic en **Registro de copiar al Portapapeles** si desea examinar más adelante y, a continuación, haga clic en **Finalizar**.
17. Desde la pantalla de inicio, escriba **mysql**y, a continuación, haga clic en **MySQL 5.6 línea de comandos de cliente**.
18. Escriba la contraseña de raíz que especificó en el paso 11 y aparecerá con la indicación donde puede emitir comandos para configurar MySQL. Para los detalles de los comandos y la sintaxis, vea [MySQL manuales de referencia](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_CommandPrompt.png)

19. También puede configurar predeterminado configuración del servidor, como las unidades y directorios de base y datos con entradas en el archivo de 5.6\my-default.ini C:\Program Files (x86) \MySQL\MySQL Server. Para obtener más información, vea [5.1.2 valores predeterminados de configuración de servidor](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Configurar extremos

Si desea que el servicio de servidor MySQL esté disponible para los equipos cliente MySQL en Internet, debe configurar un extremo para el puerto TCP en el que está escuchando el servicio de servidor MySQL y cree una regla adicional de Firewall de Windows. Este es el puerto TCP 3306 a menos que especifique un puerto diferente en la página **tipo y redes** (paso 10 del procedimiento anterior).


> [AZURE.NOTE] Debe considerar las implicaciones de seguridad de hacerlo, ya que esto hará que el servicio de servidor MySQL que disponible para todos los equipos de Internet. Puede definir el conjunto de direcciones IP de origen que se puede utilizar el extremo con una lista de Control de acceso (ACL). Para obtener más información, vea [cómo establecer extremos a una máquina Virtual](virtual-machines-windows-classic-setup-endpoints.md).


Para configurar un extremo para el servicio de servidor MySQL:

1.  En el portal de Azure clásico, haga clic en **máquinas virtuales de Windows**, haga clic en el nombre de la máquina virtual de MySQL y, a continuación, haga clic en **extremos**.
2.  En la barra de comandos, haga clic en **Agregar**.
3.  En la página **Agregar un extremo para una máquina virtual** , haga clic en la flecha derecha.
4.  Si está utilizando el puerto TCP MySQL de 3306 predeterminado, haga clic en **MySQL** en **nombre**y, a continuación, haga clic en la marca de verificación.
5.  Si está utilizando un puerto TCP diferente, escriba un nombre único en **nombre**. Seleccione **TCP** en protocolo, escriba el número de puerto en **Puerto público** y **Puerto a privado**y, a continuación, haga clic en la marca de verificación.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Agregar una regla de Firewall de Windows para permitir el tráfico de MySQL

Para agregar una regla de Firewall de Windows que permite MySQL tráfico de Internet, ejecute el siguiente comando en un símbolo de Windows PowerShell elevado en la máquina virtual del servidor MySQL.

    New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public


    
## <a name="test-your-remote-connection"></a>Probar la conexión remota


Para probar la conexión al servicio de servidor MySQL que se ejecuta en la máquina virtual Azure remota, primero debe determinar el nombre DNS correspondiente al servicio de nube que contiene la máquina virtual ejecuta servidor MySQL.

1.  En el portal de Azure clásico, haga clic en **máquinas virtuales de Windows**, haga clic en el nombre de la máquina virtual del servidor MySQL y, a continuación, haga clic en **panel**.
2.  Desde el panel de la máquina virtual, observe el valor de **Nombre de DNS** en la sección **Eche un vistazo rápido** . Aquí tenemos un ejemplo:

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_DNSName.png)

3.  Desde un equipo local que ejecuta MySQL o el cliente de MySQL, ejecute el comando siguiente para iniciar sesión como un usuario de MySQL.

        mysql -u <yourMysqlUsername> -p -h <yourDNSname>

    Por ejemplo, para la dbadmin3 de nombre de usuario de MySQL y el nombre DNS de testmysql.cloudapp.net para la máquina virtual, utilice el siguiente comando.

        mysql -u dbadmin3 -p -h testmysql.cloudapp.net


## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de cómo ejecutar MySQL, consulte la [Documentación de MySQL](http://dev.mysql.com/doc/).
