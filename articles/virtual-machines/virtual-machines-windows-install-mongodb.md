<properties
    pageTitle="Instalar MongoDB en una máquina virtual de Windows | Microsoft Azure"
    description="Obtenga información sobre cómo instalar MongoDB en una máquina virtual de Azure ejecutan Windows Server 2012 R2 creadas con el modelo de implementación de administrador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalar y configurar MongoDB en una máquina virtual de Windows en Azure
[MongoDB](http://www.mongodb.org) es una base de datos de NoSQL popular de código abierto y de alto rendimiento. En este artículo le guía a través de la instalación y configuración de MongoDB en una máquina virtual de Windows Server 2012 R2 (VM) en Azure. También puede [instalar MongoDB en una máquina virtual de Linux en Azure](virtual-machines-linux-install-mongodb.md).


## <a name="prerequisites"></a>Requisitos previos

Antes de instalar y configurar MongoDB, debe crear una máquina virtual y agregue un disco de datos a ella. Consulte los artículos siguientes para crear una máquina virtual y agregar un disco de datos:

- [Crear una máquina virtual de servidor de Windows con el portal de Azure](virtual-machines-windows-hero-tutorial.md) o [crear una máquina virtual de servidor de Windows con PowerShell de Azure](virtual-machines-windows-ps-create.md)
- [Adjuntar un disco de datos a una máquina virtual de servidor de Windows mediante el portal de Azure](virtual-machines-windows-attach-disk-portal.md) o [adjuntar un disco de datos a una máquina virtual de servidor de Windows PowerShell de Azure](https://msdn.microsoft.com/library/mt603673.aspx)
    
Para comenzar la instalación y configuración MongoDB, [inicie sesión en la máquina virtual de servidor de Windows](virtual-machines-windows-connect-logon.md) mediante Escritorio remoto.


## <a name="install-mongodb"></a>Instalar MongoDB

> [AZURE.IMPORTANT] No están habilitadas las características de seguridad de MongoDB, como la autenticación y enlaces de direcciones IP, de forma predeterminada. Características de seguridad deben estar habilitadas antes de implementar MongoDB para un entorno de producción. Para obtener más información, vea [seguridad de MongoDB y la autenticación](http://www.mongodb.org/display/DOCS/Security+and+Authentication).

1. Después de que se ha conectado a su máquina virtual con Escritorio remoto, abra el Explorador de Internet en el menú **Inicio** en la máquina virtual.

2. Seleccione **usar recomendada seguridad, privacidad y configuración de compatibilidad** cuando se abre por primera vez Internet Explorer y haga clic en **Aceptar**.

3. Configuración de seguridad mejorada de Internet Explorer está habilitada de forma predeterminada. Agregar el sitio Web de MongoDB a la lista de sitios permitidos:

    - Seleccione el icono de **Herramientas** en la esquina superior derecha.
    - En **Opciones de Internet**, seleccione la pestaña **seguridad** y, a continuación, seleccione el icono **Sitios de confianza** .
    - Haga clic en el botón **sitios** . Agregar _https://\*. mongodb.org_ a la lista de sitios de confianza y a continuación, cierre el cuadro de diálogo.

    ![Configuración de seguridad de Internet Explorer](./media/virtual-machines-windows-install-mongodb/configure-internet-explorer-security.png)

4. Vaya a la página de [descargas de MongoDB -](http://www.mongodb.org/downloads) (http://www.mongodb.org/downloads).

5. De forma predeterminada, debe seleccionar la edición de **Servidor de la Comunidad** y la última versión de estable actual para Windows Server 2008 R2 de 64 bits y versiones posteriores. Para descargar el programa de instalación, haga clic en **Descargar (msi)**.

    ![Descargar el instalador de MongoDB](./media/virtual-machines-windows-install-mongodb/download-mongodb.png)

    Una vez completada la descarga, ejecute al instalador.

6. Lea y acepte el contrato de licencia. Cuando se le solicite, seleccione instalar **completado** .

7. En la última pantalla, haga clic en **instalar**.


## <a name="configure-the-vm-and-mongodb"></a>Configurar la máquina virtual y MongoDB

1. Las variables de ruta de acceso no se actualizan por el instalador de MongoDB. Sin la MongoDB `bin` ubicación de la variable de ruta de acceso, debe especificar la ruta de acceso completa cada vez que use un ejecutable MongoDB. Para agregar la ubicación a la variable de ruta de acceso:

    - Haga clic en el menú **Inicio** y seleccione **sistema**.
    - Haga clic en **Configuración avanzada del sistema**y, a continuación, haga clic en **Variables de entorno**.
    - En **variables del sistema**, seleccione la **ruta de acceso**y, a continuación, haga clic en **Editar**.

    ![Configurar las variables de ruta de acceso](./media/virtual-machines-windows-install-mongodb/configure-path-variables.png)

    Agregar la ruta de acceso a su MongoDB `bin` carpeta. MongoDB normalmente se instala en `C:\Program Files\MongoDB`. Compruebe la ruta de instalación en la máquina virtual. En el ejemplo siguiente se agrega el predeterminado MongoDB instalar ubicación a la `PATH` variable:

    ```
    ;C:\Program Files\MongoDB\Server\3.2\bin
    ```

    > [AZURE.NOTE] Asegúrese de agregar el punto y coma inicial (`;`) para indicar que va a agregar una ubicación para su `PATH` variable.

2. Crear directorios de datos y de registro de MongoDB en el disco de datos. En el menú **Inicio** , seleccione **símbolo del sistema**. Los ejemplos siguientes, crean los directorios en la unidad F:

    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```

3. Iniciar una instancia de MongoDB con el comando siguiente, ajustar la ruta de acceso a los datos y los directorios de registro según corresponda:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```

    Puede tardar varios minutos para MongoDB asignar los archivos de diario e iniciar la escucha de conexiones. Todos los mensajes de registro indican que el archivo *F:\MongoLogs\mongolog.log* como `mongod.exe` server se inicia y asigna archivos de diario.

    > [AZURE.NOTE] El símbolo permanece concentrado en esta tarea mientras se está ejecutando la instancia de MongoDB. Cerrar la ventana de símbolo del sistema para seguir ejecutando MongoDB. O bien, instale MongoDB como servicio, tal como se describe en el siguiente paso.

4. Para una experiencia de MongoDB más rigurosa, instale el `mongod.exe` como un servicio. Creación de un servicio significa que no es necesario dejar el símbolo cada vez que desee usar MongoDB. Crear el servicio de manera, ajusta la ruta de acceso para los directorios de datos y de registro:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log `
        --logappend  --install
    ```

    El comando anterior crea un servicio denominado MongoDB, con una descripción de "DB Mongo". También se especifican los parámetros siguientes:

    - La `--dbpath` opción especifica la ubicación del directorio de datos.
    - La `--logpath` opción debe usarse para especificar un archivo de registro, porque el servicio en ejecución no tiene una ventana de comandos para mostrar el resultado.
    - La `--logappend` opción especifica que reiniciar el servicio hace salida anexar al archivo de registro existente.

  Para iniciar el servicio de MongoDB, ejecute el siguiente comando:

    ```
    net start MongoDB
    ```

    Para obtener más información sobre cómo crear el servicio de MongoDB, consulte [configurar un servicio de Windows para MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Probar la instancia de MongoDB

Con MongoDB ejecuta como una sola instancia o instalado como un servicio, ahora puede comenzar a crear y usar las bases de datos. Para iniciar el shell de administración de MongoDB, abrir otra ventana de símbolo del sistema en el menú **Inicio** y escriba el siguiente comando:

```
mongo  
```

Lista de las bases de datos con la `db` comando. Insertar datos como sigue:

```
db.foo.insert( { a : 1 } )
```

Buscar datos como sigue:

```
db.foo.find()
```

El resultado es similar al siguiente ejemplo:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Salir del `mongo` de consola como sigue:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Configurar el firewall y reglas de grupo de seguridad de red
Ahora que MongoDB está instalado y en ejecución, abra un puerto en Firewall de Windows para que pueda conectarse remotamente a MongoDB. Para crear una nueva regla de entrada para permitir que el puerto TCP 27017, abra un símbolo del sistema de PowerShell administrativo y escriba el siguiente comando:

```powerShell
New-NetFirewallRule -DisplayName "Allow MongoDB" -Direction Inbound `
    -Protocol TCP -LocalPort 27017 -Action Allow
```

También puede crear la regla mediante la herramienta de administración gráfica de **Firewall de Windows con seguridad avanzada** . Crear una nueva regla de entrada para permitir que el puerto TCP 27017.

Si es necesario, cree una regla de grupo de seguridad de la red para permitir el acceso a MongoDB desde fuera de la subred de una red virtual Azure existente. Puede crear las reglas del grupo de seguridad de red mediante el [portal de Azure](virtual-machines-windows-nsg-quickstart-portal.md) o [PowerShell de Azure](virtual-machines-windows-nsg-quickstart-powershell.md). Al igual que con las reglas de Firewall de Windows, permitir que el puerto TCP 27017 a la interfaz de red virtual de su VM MongoDB.

> [AZURE.NOTE] El puerto TCP 27017 es el puerto predeterminado usado por MongoDB. Puede cambiar este puerto mediante la `--port` parámetro al iniciar `mongod.exe` manualmente o de un servicio. Si cambia el puerto, asegúrese de actualizar las reglas de Firewall de Windows y grupo de seguridad de red en los pasos anteriores.


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo instalar y configurar MongoDB en la máquina virtual de Windows. Ahora puede acceder a los MongoDB en la máquina virtual de Windows, siguiendo los temas avanzados en la [documentación de MongoDB](https://docs.mongodb.com/manual/).
