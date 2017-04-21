<properties
    pageTitle="Configurar una máquina virtual de SQL Server como un servidor de Bloc de notas de IPython | Microsoft Azure"
    description="Establecer el una máquina Virtual de la ciencia de datos con SQL Server y IPython Server."
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
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-sql-server-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Configurar una máquina virtual de Azure SQL Server como un servidor de Bloc de notas de IPython para análisis avanzado

Este tema muestra cómo crear y configurar una máquina virtual de SQL Server que se usará como parte de un entorno de ciencias datos basados en la nube. La máquina virtual de Windows está configurada con herramientas como el Bloc de notas de IPython, Explorador de almacenamiento de Azure y AzCopy, así como otras utilidades que son útiles para proyectos de ciencias de datos de soporte. Explorador de almacenamiento de Azure y AzCopy, por ejemplo, proporcionan formas adecuado para cargar los datos con el almacenamiento de blobs de Windows Azure desde el equipo local o descargar en el equipo local de almacenamiento de blobs de Windows.

La Galería de Azure máquina virtual incluye varias imágenes que contienen Microsoft SQL Server. Seleccione una imagen de máquina virtual de SQL Server que es adecuada para sus necesidades de datos. Imágenes recomendadas son:

- SQL Server 2012 SP2 Enterprise para pequeñas y medianas tamaños
- SQL Server 2012 SP2 Enterprise optimizado para cargas de trabajo del almacén de datos para los tamaños de datos de gran tamaño a muy grande

 > [AZURE.NOTE] Imagen de SQL Server 2012 SP2 Enterprise **no incluye un disco de datos**. Debe agregar o adjuntar uno o más discos duro virtuales para almacenar los datos. Cuando se crea una máquina virtual Azure, tiene un disco para el sistema operativo asignado a la unidad C y asigna a la unidad D una temporal. No use la unidad D para almacenar datos. Como se indica el nombre, proporciona almacenamiento temporal solo. No ofrece redundancia o copia de seguridad porque no se encuentran en el almacenamiento de Azure.


##<a name="Provision"></a>Conectar con el Portal de clásico de Azure y aprovisionar una máquina virtual de SQL Server

1.  Inicie sesión en el [Portal de Azure clásica](http://manage.windowsazure.com/) con su cuenta.
    Si no tiene una cuenta de Azure, visite [Azure prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).

2.  En el Portal de clásico de Azure, en la parte inferior izquierda de la página web, haga clic en **+ nuevo**, haga clic en **calcular**, haga clic en la **máquina VIRTUAL**y, a continuación, haga clic en **Galería de**.

3.  En la página **crear una máquina Virtual** , seleccione una imagen de máquina virtual que contenga según sus necesidades de datos de SQL Server y, a continuación, haga clic en la flecha en la parte inferior derecha de la página siguiente. Para obtener la información más actualizada en las imágenes de SQL Server compatibles en Azure, vea Establecer de tema de [Introducción a SQL Server en Azure máquinas virtuales de Windows](http://go.microsoft.com/fwlink/p/?LinkId=294720) en la documentación de [SQL Server en Azure máquinas virtuales de Windows](http://go.microsoft.com/fwlink/p/?LinkId=294719) .

    ![Seleccione SQL Server VM][1]

4.  En la primera página de **Configuración de máquina Virtual** , proporcione la siguiente información:

    -   Proporcione un **nombre de la máquina VIRTUAL**.
    -   En el cuadro **Nombre de usuario nuevo** , escriba el nombre de usuario único para la cuenta de administrador local de la máquina virtual.
    -   En el cuadro **Nueva contraseña** , escriba una contraseña segura. Para obtener más información, vea [Contraseñas a seguras](http://msdn.microsoft.com/library/ms161962.aspx).
    -   En el cuadro **Confirmar contraseña** , vuelva a escribir la contraseña.
    -   Seleccione **tamaño** apropiado de la lista desplegable.

     > [AZURE.NOTE] Especifica el tamaño de la máquina virtual durante el aprovisionamiento: A2 es el tamaño mínimo recomendado para cargas de trabajo de producción. El tamaño mínimo recomendado para una máquina virtual es A3 al usar SQL Server Enterprise Edition. Seleccione A3 o superior al usar SQL Server Enterprise Edition. Seleccione A4 al usar SQL Server 2012 o 2014 Enterprise optimizada para las imágenes de las cargas de trabajo de transacciones.
Seleccione A7 al usar SQL Server 2012 o 2014 Enterprise optimizado para las imágenes de cargas de trabajo de almacenamiento de datos. El tamaño seleccionado limita el número de discos de datos que se pueden configurar. Para obtener información más actualizada sobre tamaños de máquina virtual de disponibilidad y el número de discos de datos que puede adjuntar a una máquina virtual, vea [Tamaños de máquina Virtual para Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Para obtener información sobre precios, consulte [Precios de Macines VIrtual](https://azure.microsoft.com/pricing/details/virtual-machines/).

    Haga clic en la flecha siguiente en la parte inferior derecha para continuar.

    ![Configuración de la máquina virtual][2]

5.  En la segunda página de **configuración de máquina Virtual** , configurar recursos de disponibilidad, almacenamiento y redes:

    -   En el cuadro de **Servicio de nube** , elija **crear un nuevo servicio de nube**.
    -   En el cuadro **Nombre de DNS del servicio de nube** , proporcionar la primera parte de un nombre DNS de su elección, para que se complete un nombre en el formato **TESTNAME.cloudapp.net**
    -   En el cuadro de la **Red de grupo y virtuales de región o de AFINIDAD** , seleccione una región donde se va a hospedar la imagen virtual.
    -   En la **Cuenta de almacenamiento**, seleccione una cuenta de almacenamiento existente o seleccione una generada automáticamente.
    -   En el cuadro **Establecer disponibilidad** , seleccione **(ninguno)**.
    -   Lea y acepte la información de precios.

6.  En la sección **EXTREMOS** , haga clic en el menú desplegable vacío en **nombre**y seleccione **MSSQL** y escriba el número de puerto de la instancia del motor de base de datos (**1433** para la instancia predeterminada).

7.  La VM de SQL Server también puede servir como un servidor de Bloc de notas IPython, que se configura en un paso posterior.
    Agregar un nuevo punto final para especificar el puerto para el servidor de Bloc de notas de IPython. Escriba un nombre en la columna **nombre** , seleccione un número de puerto de su elección para el puerto público y 9999 para el puerto privado.

    Haga clic en la flecha siguiente en la parte inferior derecha para continuar.

    ![Seleccione puertos MSSQL y IPython][3]

8.  Acepte la opción de **instalar VM agente** predeterminado activada y haga clic en la marca de verificación en la esquina inferior derecha del Asistente para completar el proceso de aprovisionamiento de VM.

    `![Opciones de VM Final][4]

9.  Espere mientras Azure prepara la máquina virtual. Pasar el estado de la máquina virtual para continuar a través de:

    -   Iniciar (aprovisionamiento)
    -   Detener
    -   Iniciar (aprovisionamiento)
    -   Ejecutar (aprovisionamiento)
    -   Ejecutar


##<a name="RemoteDesktop"></a>Abra la máquina virtual con Escritorio remoto y completar la instalación

1.  Cuando termine de aprovisionamiento, haga clic en el nombre de la máquina virtual para ir a la página de panel. En la parte inferior de la página, haga clic en **Conectar**.

2.  Elija abrir el archivo Mod con el programa de escritorio remoto de Windows (`%windir%\system32\mstsc.exe`).

3.  En el cuadro de diálogo **Seguridad de Windows** , proporcione la contraseña para la cuenta de administrador local que especificó en un paso anterior.
    (Podría solicitado para comprobar las credenciales de la máquina virtual).

4.  La primera vez que inicie sesión en este equipo virtual, es podrán que varios procesos necesite completar, incluida la configuración de su escritorio, las actualizaciones de Windows y finalización de las tareas de configuración inicial de Windows (sysprep). Después de que finalice sysprep de Windows, el programa de instalación de SQL Server completa las tareas de configuración. Estas tareas pueden producir un retraso de unos minutos mientras se completan. `SELECT @@SERVERNAME`no se puede devolver el nombre correcto hasta que finalice la instalación de SQL Server y SQL Server Management Studio no esté visible en la página de inicio.

Una vez que está conectado a la máquina virtual con Escritorio remoto de Windows, la máquina virtual funciona como cualquier otro equipo. Conectarse a la instancia predeterminada de SQL Server con SQL Server Management Studio (que se ejecuta en la máquina virtual) en la forma normal.


##<a name="InstallIPython"></a>Instalar el Bloc de notas de IPython y otras herramientas de soporte

Para configurar la nueva máquina virtual servidor SQL para servir como un servidor IPython Bloc de notas y a continuación, instalar herramientas de soporte adicionales como AzCopy, Explorador de almacenamiento de Azure, los paquetes de datos ciencias Python útiles y otros, se proporciona una secuencia de comandos de personalización especial para usted. Para instalar:

- Haga clic en el icono de **Inicio de Windows** y haga clic en **símbolo del sistema (Administrador)**
- Los siguientes comandos de copiar y pegar en el símbolo del sistema.

        set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
        @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- Cuando se le pida, escriba una contraseña de su elección para el servidor de Bloc de notas de IPython.
- La secuencia de comandos de personalización automatiza varios procedimientos posteriores a la instalación, que incluyen:
    + Instalación y configuración del servidor de Bloc de notas de IPython
    + Abrir puertos TCP en firewall de Windows para los extremos que creó anteriormente:
    + Conectividad remota de SQL Server
    + De conectividad remota de Bloc de notas de IPython server
    + Obtención de blocs de notas de ejemplo IPython y secuencias de comandos SQL
    + Descargar e instalar paquetes de datos ciencias Python útiles
    + Descargar e instalar las herramientas de Azure como AzCopy y Explorador de almacenamiento de Azure  
<br>
- Puede obtener acceso y ejecutar IPython Bloc de notas desde cualquier explorador local o remota con una dirección URL del formulario `https://<virtual_machine_DNS_name>:<port>`, donde puerto es el IPython público seleccionado al hacer el aprovisionamiento de la máquina virtual.
- Servidor de Bloc de notas de IPython se ejecuta como un servicio de fondo y se reinicia automáticamente al reiniciar el equipo virtual.

##<a name="Optional"></a>Conecte el disco de datos según sea necesario

Si la imagen de VM no incluye datos discos, es decir, que no sea unidad C (disco del SO) y la unidad D (temporal en el disco), debe agregar uno o más discos de datos para almacenar los datos. La imagen de máquina virtual para SQL Server 2012 SP2 Enterprise optimizado para cargas de trabajo del almacén de datos viene preconfigurada con discos adicionales para los archivos de datos y de registro de SQL Server.

 > [AZURE.NOTE] No use la unidad D para almacenar datos. Como se indica el nombre, proporciona almacenamiento temporal solo. No ofrece redundancia o copia de seguridad porque no se encuentran en el almacenamiento de Azure.

Para adjuntar discos de datos adicionales, siga los pasos descritos en [cómo adjuntar un disco de datos a una máquina Virtual de Windows](virtual-machines-windows-classic-attach-disk.md), que le guiará a través de:

1. Adjuntar discos vacío a la máquina virtual que se aprovisione en los pasos anteriores
2. Inicialización de los nuevos discos en la máquina virtual


##<a name="SSMS"></a>Conectar con SQL Server Management Studio y habilitar la autenticación de modo mixto

El motor de base de datos de SQL Server no puede usar autenticación de Windows sin entorno de dominio. Para conectar con el motor de base de datos desde otro equipo, configure SQL Server para la autenticación de modo mixto. Autenticación de modo mixto permite la autenticación de SQL Server y la autenticación de Windows. Modo de autenticación de SQL es necesaria para recopilar datos directamente desde las bases de datos de SQL Server VM en [Azure máquina aprendizaje Studio](https://studio.azureml.net) uso del módulo de importar datos.

1.  Mientras está conectado a la máquina virtual mediante Escritorio remoto, utilice el panel de **búsqueda** de Windows y escriba **SQL Server Management Studio** (SMSS). Haga clic para iniciar SQL Server Management Studio (SSMS). Desea agregar un acceso directo a SSMS en el escritorio para usos futuros.

    ![Iniciar SSMS][5]

    La primera vez que abra Management Studio debe crear el entorno de Management Studio de los usuarios. Esto puede tardar unos minutos.

2.  Cuando se abre, Management Studio presenta el cuadro de diálogo **Conectar con el servidor** . En el cuadro **nombre del servidor** , escriba el nombre de la máquina virtual para conectar con el motor de base de datos con el Explorador de objetos.
    (En lugar del nombre de la máquina virtual también puede usar **(local)** o un punto como el **nombre del servidor**. Seleccione **Autenticación de Windows**y deje ** *su\_VM\_nombre*\\su\_local\_administrador** en el cuadro **nombre de usuario** . Haga clic en **Conectar**.

    ![Conectarse al servidor][6]

    <br>

     > [AZURE.TIP] Puede cambiar el modo de autenticación de SQL Server mediante un cambio de clave de registro de Windows o SQL Server Management Studio. Para cambiar el modo de autenticación con el cambio de clave del registro, inicie una **Nueva consulta** y ejecutar la siguiente secuencia de comandos:

        USE master
        go

        EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
        go


    Para cambiar el modo de autenticación usa SQL Server management Studio:

3.  En el **Explorador de objetos de SQL Server Management Studio**, haga clic en el nombre de la instancia de SQL Server (el nombre de la máquina virtual) y, a continuación, haga clic en **Propiedades**.

    ![Propiedades del servidor][7]

4.  En la página de **seguridad** en la **autenticación del servidor**, seleccione **SQL Server y el modo de autenticación de Windows**y, a continuación, haga clic en **Aceptar**.

    ![Seleccione el modo de autenticación][8]

5.  En el cuadro de diálogo de **SQL Server Management Studio** , haga clic en **Aceptar** para confirmar que el requisito de reiniciar SQL Server.

6.  En el **Explorador de objetos**, haga clic en el servidor y, a continuación, haga clic en **reiniciar**. (Si está ejecutando el agente de SQL Server, se debe también reiniciar.)

    ![Reiniciar][9]

7.  En el cuadro de diálogo de **SQL Server Management Studio** , haga clic en **Sí** para aceptar el que desea reiniciar SQL Server.

##<a name="Logins"></a>Crear inicios de sesión de autenticación de SQL Server

Para conectar con el motor de base de datos desde otro equipo, debe crear al menos un inicio de sesión de autenticación de SQL Server.  

Puede crear nuevos inicios de sesión de SQL Server mediante programación o con SQL Server Management Studio. Para crear un nuevo usuario de administrador del sistema con la autenticación de SQL mediante programación, inicie una **Nueva consulta** y ejecutar la siguiente secuencia de comandos. Reemplazar < nuevo nombre de usuario\> y < nueva contraseña\> con la opción de *nombre de usuario* y *contraseña*. 


    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
        CHECK_POLICY = OFF,
        CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';


Ajustar la directiva de contraseñas según sea necesario (el ejemplo código desactiva la caducidad de comprobación y la contraseña de la directiva). Para obtener más información sobre los inicios de sesión de SQL Server, consulte [crear un inicio de sesión](http://msdn.microsoft.com/library/aa337562.aspx).  

Para crear nuevos inicios de sesión de SQL Server con SQL Server Management Studio:

1.  En el **Explorador de objetos de SQL Server Management Studio**, expanda la carpeta de la instancia del servidor en el que desea crear el nuevo inicio de sesión.

2.  Haga clic en la carpeta de **seguridad** , elija **nuevo**y seleccione **Inicio de sesión...**.

    ![Nuevo inicio de sesión][10]

3.  En el cuadro de diálogo de **Inicio de sesión - nuevo** , en la página **General** , escriba el nombre del nuevo usuario en el cuadro **nombre de inicio de sesión** .

4.  Seleccione **autenticación de SQL Server**.

5.  En el cuadro **contraseña** , escriba una contraseña para el nuevo usuario. Escriba la contraseña de nuevo en el cuadro **Confirmar contraseña** .

6.  Para aplicar las opciones de directiva de contraseña para la complejidad y aplicación, seleccione **exigir directivas de contraseña** (recomendado). Esto es una opción de forma predeterminada cuando se selecciona la autenticación de SQL Server.

7.  Para aplicar opciones de directiva de contraseña para la expiración, seleccione **Exigir caducidad de contraseña** (recomendado). Exigir directiva de contraseñas debe estar activada para habilitar esta casilla de verificación. Esto es una opción de forma predeterminada cuando se selecciona la autenticación de SQL Server.

8.  Para forzar el usuario para crear una nueva contraseña tras la primera vez que se utiliza el inicio de sesión, seleccione **el usuario debe cambiar la contraseña en el siguiente inicio de sesión** (se recomienda si este inicio de sesión es de alguien a usar. Si el inicio de sesión es para su propio uso, no seleccione esta opción). Exigir caducidad de la contraseña debe estar activada para habilitar esta casilla de verificación. Esto es una opción de forma predeterminada cuando se selecciona la autenticación de SQL Server.

9.  En la lista de la **base de datos predeterminada** , seleccione una base de datos predeterminada para el inicio de sesión. **principales** es el valor predeterminado para esta opción. Si aún no ha creado una base de datos de usuario, deje este valor establecido en **principal**.

10. En la lista **idioma predeterminado** , deje el **valor predeterminado** como el valor.

    ![Propiedades de inicio de sesión][11]

11. Si esta es la primera conexión que está creando, desea designar este inicio de sesión como administrador de SQL Server. Si es así, en la página de **Roles del servidor** , active **sysadmin**.

    > [AZURE.IMPORTANT] Miembros de la función fija de servidor tienen un control completo del motor de base de datos. Por motivos de seguridad, debe restringir cuidadosamente la pertenencia a este rol.

    ![sysadmin][12]

12. Haga clic en Aceptar.

##<a name="DNS"></a>Determinar el nombre DNS de la máquina virtual

Para conectar con el motor de base de datos de SQL Server desde otro equipo, debe conocer el nombre del sistema de nombres de dominio (DNS) de la máquina virtual.

(Es decir, el nombre de internet que se usa para identificar la máquina virtual. Puede usar la dirección IP, pero puede cambiar la dirección IP cuando Azure mueve recursos para redundancia o mantenimiento. El nombre DNS será estable porque se pueda redirigir a una nueva dirección IP.)

1.  En el Portal de Azure clásica (o desde el paso anterior), seleccione **máquinas virtuales de Windows**.

2.  En la página de **Instancias de máquina VIRTUAL** , en la columna **Nombre de DNS** , busque y copie el nombre DNS para la máquina virtual que aparece precedido por **http://**. (La interfaz de usuario no puede mostrar el nombre completo, pero puede haga clic en él y haga clic en copiar.)

##<a name="cde"></a>Conectar con el motor de base de datos desde otro equipo

1.  En un equipo conectado a internet, abra SQL Server Management Studio.

2.  En el cuadro de diálogo **Conectar con el servidor** o **conectarse al motor de base de datos** , en el cuadro **nombre del servidor** , escriba el nombre DNS de la máquina virtual (determinado en la tarea anterior) y un extremo público número de puerto en el formato de *DNSName, número de puerto* como **tutorialtestVM.cloudapp.net,57500**.

3.  En el cuadro de **autenticación** , seleccione **Autenticación de SQL Server**.

4.  En el cuadro de **Inicio de sesión** , escriba el nombre de inicio de sesión que creó en la tarea anterior.

5.  En el cuadro **contraseña** , escriba la contraseña de inicio de sesión que cree en una tarea anterior.

6.  Haga clic en **Conectar**.

##<a name="amlconnect"></a>Conectar con el motor de base de datos desde el aprendizaje Azure

En fases posteriores del proceso de ciencias grupo datos, usará el [Azure máquina aprendizaje Studio](https://studio.azureml.net) compilar e implementar modelos de aprendizaje del equipo. Para recopilar datos de las bases de datos de SQL Server VM directamente en Azure el aprendizaje de formación o puntuación, use el módulo de **Importar datos** en un nuevo ensayo [Studio de aprendizaje del equipo de Azure](https://studio.azureml.net) . En este tema se abordan con más detalles a través de los vínculos de la Guía de proceso de ciencias de datos de equipo. Para obtener una introducción, vea [¿Qué es Studio de aprendizaje del equipo de Azure?](machine-learning-what-is-ml-studio.md).

2.  En el panel de **Propiedades** del [módulo de importar datos](https://msdn.microsoft.com/library/azure/dn905997.aspx), seleccione la **Base de datos de SQL Azure** en la lista desplegable de **Origen de datos** .

3.  En el cuadro de texto **nombre de servidor de base de datos** , escriba`tcp:<DNS name of your virtual machine>,1433`

4.  Escriba el nombre de usuario SQL en el cuadro de texto **nombre de cuenta de usuario de servidor** .

5.  Escriba la contraseña de usuario de sql en el cuadro de texto de **contraseña de cuenta de usuario de servidor** .

    ![Datos de Azure importar v][13]

##<a name="shutdown"></a>Cierre y desasignar máquina virtual cuando no esté en uso

El precio de máquinas virtuales de Windows Azure como **pagar solo por lo que usa**. Para asegurarse de que no se facturación cuando no se utiliza la máquina virtual, tiene que estar en el estado **detenido (Deallocated)** .

> [AZURE.NOTE] Apagar la máquina virtual desde dentro (con opciones de energía de Windows), se detiene la máquina virtual pero permanece asignada. Para asegurarse de que no está facturada, detener siempre máquinas virtuales desde el [Portal de Azure clásico](http://manage.windowsazure.com/). También puede dejar la máquina virtual a través de Powershell llamando ShutdownRoleOperation con "PostShutdownAction" igual a "StoppedDeallocated".

Para cerrar y desasignar la máquina virtual:

1. Inicie sesión en el [Portal de Azure clásica](http://manage.windowsazure.com/) con su cuenta.  

2. Seleccione **máquinas virtuales** de la barra de navegación izquierda.

3. En la lista de máquinas virtuales de Windows, haga clic en el nombre de la máquina virtual, a continuación, vaya a la página de **panel** .

4. En la parte inferior de la página, haga clic en **Cerrar**.

![Cierre VM][15]

Se cancela la asignación de la máquina virtual pero no se eliminarán. Puede reiniciar el equipo virtual en cualquier momento desde el Portal de clásico de Azure.

## <a name="your-azure-sql-server-vm-is-ready-to-use-whats-next"></a>Está listo para usar su servidor de SQL Azure máquina virtual: ¿qué es la siguiente?

La máquina virtual ahora está lista para usar en sus ejercicios de ciencias de datos. La máquina virtual también está preparada para utilizarlo como un servidor de Bloc de notas de IPython para la exploración y el procesamiento de datos y otras tareas junto con el aprendizaje Azure y el proceso de ciencias de datos de equipo (TDSP).

Los siguientes pasos en el proceso de ciencias de datos se asignan en el [Proceso de ciencias de datos de equipo](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) y pueden incluir pasos que mover los datos a HDInsight, procesarán y escuche ahí en preparación para aprender de los datos con el aprendizaje del equipo de Azure.


[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]:./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png
 
