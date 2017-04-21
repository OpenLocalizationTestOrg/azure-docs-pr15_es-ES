<properties 
    pageTitle="Utilice las teclas SSH con Windows para máquinas virtuales de Linux | Microsoft Azure" 
    description="Obtenga información sobre cómo generar y utilizar SSH teclas en un equipo Windows para conectarse a una máquina virtual de Linux en Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="squillace" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="rasquill"/>

# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Cómo usar SSH claves con Windows en Azure

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Mac o Linux](virtual-machines-linux-mac-create-ssh-keys.md)

Cuando se conecta a Linux máquinas virtuales (VM) Azure, debe utilizar [cifrado de clave pública](https://wikipedia.org/wiki/Public-key_cryptography) para proporcionar una forma más segura de iniciar sesión en su VM Linux. Este proceso incluye un intercambio de claves público y privado con el comando de shell seguro (SSH) para autenticar usted mismo en lugar de un nombre de usuario y contraseña. Las contraseñas son vulnerables a fuerza bruta ataques, especialmente en máquinas virtuales a través de Internet como servidores web. Este artículo proporciona una descripción general de claves SSH y cómo generar las claves apropiadas en un equipo Windows.


## <a name="overview-of-ssh-and-keys"></a>Información general sobre SSH y claves

Puede deben iniciar sesión en su VM Linux mediante el uso de claves públicas y privadas:

- La **clave pública** se coloca en su VM Linux o cualquier otro servicio que desea usar con cifrado de clave pública.
- La **clave privada** es lo que presenta a su VM Linux al iniciar sesión, para verificar su identidad. Proteger esta clave privada. No compartir.

Estas claves públicas y privadas pueden usarse en varias máquinas virtuales y servicios. No es necesario un par de claves para cada VM o servicio que desea tener acceso. Para obtener información más detallada, vea [cifrado de clave pública](https://wikipedia.org/wiki/Public-key_cryptography).

SSH es un protocolo de conexión cifrada que permite inicios de sesión seguros en conexiones no seguras. Es el protocolo de conexión predeterminado para máquinas virtuales de Linux hospedado en Azure. Aunque SSH propio proporciona una conexión cifrada, uso de contraseñas con conexiones de SSH aún deja la máquina virtual expuesto a fuerza bruta ataques o adivinar contraseñas. Un método más seguro y preferido, para conectarse a una máquina virtual con SSH es mediante estas claves públicas y privadas, también conocido como SSH teclas.

Si no desea usar las teclas SSH, aún puede iniciar sesión a sus máquinas virtuales de Linux utilizando una contraseña. Si su máquina virtual no se expone a Internet, el uso de contraseñas puede ser suficiente. Sin embargo, todavía necesita administrar sus contraseñas para cada VM Linux y mantener las directivas de contraseña correcto y prácticas, como la longitud mínima de contraseña y actualizándolos con regularidad. El uso de claves SSH reduce la complejidad de la administración de credenciales individuales en máquinas virtuales de varios.


## <a name="windows-packages-and-ssh-clients"></a>Paquetes de Windows y los clientes SSH

Conectarse a y administrar máquinas virtuales de Linux en Azure mediante un cliente **ssh** . Equipos con Windows normalmente no tiene un cliente **ssh** instalado. Se incluyen Windows SSH clientes habituales que puede instalar en los siguientes paquetes:

- [GIT para Windows](https://git-for-windows.github.io/)
- [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)

> [AZURE.NOTE] La actualización más reciente de aniversario de 10 de Windows incluye Bash para Windows. Esta característica le permite ejecutar el subsistema de Windows de utilidades de Linux y acceso como un cliente SSH. Bash para Windows está en desarrollo y se considera una versión beta. Para obtener más información acerca de Bash para Windows, vea [Bash en Ubuntu en Windows](https://msdn.microsoft.com/commandline/wsl/about).


## <a name="which-key-files-do-you-need-to-create"></a>¿Qué archivos claves necesita crear?

Azure requiere al menos 2048 bits, **ssh rsa** formato claves públicas y privadas. Si administra los recursos Azure utiliza el modelo de implementación estándar, también debe generar un PEM (`.pem` archivo).

Estos son los escenarios de implementación y los tipos de archivos que se utilizan en cada una:

1. claves **SSH rsa** son necesarias para cualquier implementación con el [portal de Azure](https://portal.azure.com)e implementaciones del Administrador de recursos con la [CLI de Azure](../xplat-cli-install.md).
    - Estas teclas suelen que mayoría todos los usuarios necesitan.
2. `.pem`es necesario crear máquinas virtuales con el [portal de clásico](https://manage.windowsazure.com)de archivo. Estas teclas también son compatibles con implementaciones estándar que utilizan la [CLI de Azure](../xplat-cli-install.md).
    - Solo necesita crear estas teclas adicionales y certificados, si administra los recursos creados con el modelo de implementación de clásico.


## <a name="install-git-for-windows"></a>Instalar Git para Windows

La sección anterior incluye varios paquetes que incluyen la `openssl` herramienta para Windows. Esta herramienta es necesaria para crear las claves públicas y privadas. Los ejemplos siguientes describen cómo instalar y usar **Git para Windows**, aunque puede elegir cualquier paquete que prefiera. **GIT para Windows** le proporciona acceso a algunas herramientas de software de código abierto adicional ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) y utilidades que pueden resultar útiles al trabajar con máquinas virtuales de Linux.

1. Descargar e instalar **Git para Windows** de la siguiente ubicación: [https://git-for-windows.github.io/](https://git-for-windows.github.io/).

2. Acepte las opciones predeterminadas durante el proceso de instalación a menos que necesite específicamente cambiarlos.

3. Ejecutar **Git fiesta** desde el **menú Inicio** > **Git** > **Git intensiva**. La consola tiene un aspecto similar al siguiente ejemplo:

    ![Shell de Bash de GIT para Windows](./media/virtual-machines-linux-ssh-from-windows/git-bash-window.png)


## <a name="create-a-private-key"></a>Crear una clave privada

1. En la ventana de **Fiesta Git** , utilice `openssl.exe` para crear una clave privada. En el ejemplo siguiente se crea una clave denominada `myPrivateKey` y certificado denominado `myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    El resultado es similar al siguiente ejemplo:

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

2. Responda a las indicaciones para país, ubicación, nombre de la organización, etcetera.

3. La nueva clave privada y el certificado se crean en el directorio de trabajo actual. Prácticas recomendadas de seguridad, debe configurar los permisos en su clave privada para que solo puede tener acceso a:

    ```bash
    chmod 0600 myPrivateKey
    ```

4. Si también necesita administrar recursos clásica, convierta el `myCert.pem` a `myCert.cer` (codificado DER X509 certificado). Realizar este paso opcional solo si necesita administrar específicamente antiguos recursos clásico. 

    Convertir el certificado mediante el siguiente comando:

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Crear una clave privada para deformar

PuTTY es un cliente SSH comunes para Windows. Es libre para utilizar a cualquier cliente SSH que desee. Para usar PuTTY, debe crear un tipo de clave: una clave privada PuTTY (PPK) adicional. Si no desea usar PuTTY, omitir esta sección.

En el ejemplo siguiente se crea esta clave privada adicional específicamente para PuTTY usar:

1. Use **Git fiesta** para convertir la clave privada en una clave privada RSA que pueden comprender PuTTYgen. En el ejemplo siguiente se crea una clave denominada `myPrivateKey_rsa` de la clave existente denominada `myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    Prácticas recomendadas de seguridad, debe configurar los permisos en su clave privada para que solo puede tener acceso a:

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```

2. Descargar y ejecutar PuTTYgen desde la siguiente ubicación: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

3. Haga clic en el menú: **archivo** > **carga una clave privada**

4. Busque la clave privada (`myPrivateKey_rsa` en el ejemplo anterior). El directorio predeterminado al iniciar **Fiesta Git** es `C:\Users\%username%`. Cambiar el filtro de archivos para mostrar **todos los archivos (\*.\*)**:

    ![Cargar la clave privada existente en PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/load-private-key.png)

5. Haga clic en **Abrir**. Un mensaje indica que se ha importado correctamente la clave:

    ![Tecla importado correctamente a PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/successfully-imported-key.png)

6. Haga clic en **Aceptar** para cerrar el símbolo del sistema.

7. La clave pública se muestra en la parte superior de la ventana de **PuTTYgen** . Copie y pegue esta clave pública en el portal de Azure o administrador de recursos de Azure plantilla cuando se crea una VM Linux. También puede hacer clic en **Guardar clave pública** para guardar una copia en su equipo:

    ![Guardar archivo de clave pública masilla](./media/virtual-machines-linux-ssh-from-windows/save-public-key.png)

    En el ejemplo siguiente se muestra cómo copiar y pegar esta clave pública en el portal de Azure cuando se crea una VM Linux. La clave pública normalmente se almacena en `~/.ssh/authorized_keys` en su nueva máquina virtual.

    ![Usar clave pública cuando se crea una máquina virtual en el portal de Azure](./media/virtual-machines-linux-ssh-from-windows/use-public-key-azure-portal.png)

7. En el **PuTTYgen**, haga clic en **Guardar clave privada**:

    ![Guardar archivo de clave privada de PuTTY](./media/virtual-machines-linux-ssh-from-windows/save-ppk-file.png)

    > [AZURE.WARNING] Un mensaje le pregunta si desea continuar sin escribir una frase de contraseña para la clave. Una frase de contraseña es como una contraseña conectada a su clave privada. Incluso si alguien obtener su clave privada, todavía no podrán autenticarse con sólo la clave. También tienen la frase de contraseña. Sin una frase de contraseña si alguien obtiene su clave privada, puede iniciar sesión en cualquier máquina virtual o servicio que utilice esa clave. Recomendamos que crear una frase de contraseña. Sin embargo, si ha olvidado la contraseña, no hay ninguna forma de recuperarla.

    Si desea escribir una frase de contraseña, haga clic en **No**, escriba una frase de contraseña en la ventana principal de PuTTYgen y, a continuación, haga clic en **Guardar clave privada** nuevamente. En caso contrario, haga clic en **Sí** para continuar sin proporcionar la frase de contraseña opcional.

8. Escriba un nombre y una ubicación para guardar el archivo PPK.


## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Usar Putty a SSH para un equipo Linux

Nuevamente, PuTTY es un cliente SSH comunes para Windows. Es libre para utilizar a cualquier cliente SSH que desee. Los siguientes pasos explican cómo usar su clave privada para autenticar con la máquina virtual de Azure con SSH. Los pasos son similares en otros clientes de clave SSH en cuanto a la necesidad de cargar su clave privada para autenticar la conexión SSH.

1. Descargar y ejecutar putty desde la siguiente ubicación: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Rellene el nombre de host o la dirección IP de la máquina virtual desde el portal de Azure:

    ![Abrir nueva conexión masilla](./media/virtual-machines-linux-ssh-from-windows/putty-new-connection.png)

3. Antes de seleccionar **abierto**, haga clic en **conexión** > **SSH** > ficha**Auth** . Busque y seleccione su clave privada:

    ![Seleccione la clave privada PuTTY para la autenticación](./media/virtual-machines-linux-ssh-from-windows/putty-auth-dialog.png)

4. Haga clic en **Abrir** para conectarse a su equipo virtual
 

## <a name="next-steps"></a>Pasos siguientes
También puede generar claves públicas y privadas [usando OS X y Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Para obtener más información acerca de Bash para Windows y las ventajas de herramientas de sistemas operativos disponibles en su equipo con Windows, vea [Bash en Ubuntu en Windows](https://msdn.microsoft.com/commandline/wsl/about).

Si tiene dificultades para usar SSH para conectarse a sus máquinas virtuales de Linux, consulte [solucionar problemas de SSH conexiones a una máquina virtual Linux de Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).