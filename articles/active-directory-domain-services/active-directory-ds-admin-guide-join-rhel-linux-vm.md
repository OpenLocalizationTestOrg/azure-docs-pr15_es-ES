<properties
    pageTitle="Azure Active Directory Domain Services: Unirse a una VM RHEL a un dominio administrado | Microsoft Azure"
    description="Unirse a una máquina virtual rojos sombrero Enterprise Linux en servicios de dominio de Active Directory de Azure"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/02/2016"
    ms.author="maheshu"/>

# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Unirse a una máquina virtual de rojo sombrero Enterprise Linux 7 a un dominio administrado
En este artículo se muestra cómo unirse a una máquina virtual de Red sombrero Enterprise Linux (RHEL) 7 a un dominio administrado de servicios de dominio de Active Directory de Azure.

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Aprovisionar una máquina virtual rojos sombrero Enterprise Linux
Realice los pasos siguientes para aprovisionar una máquina virtual de RHEL 7 con el portal de Azure.

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).

    ![Panel de portal de Azure](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)

2. Haga clic en **nuevo** en el panel izquierdo y escriba **Sombrero rojo** en la barra de búsqueda, como se muestra en la siguiente captura de pantalla. Entradas de Red sombrero Enterprise Linux aparecen en los resultados de búsqueda. Haga clic en **rojo sombrero Enterprise Linux 7.2**.

    ![Seleccione RHEL en los resultados](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)

3. Los resultados de búsqueda en el panel de **todo lo que** deben aparecer la imagen de Red sombrero Enterprise Linux 7.2. Haga clic en **rojo sombrero Enterprise Linux 7.2** para ver más información sobre la imagen de la máquina virtual.

    ![Seleccione RHEL en los resultados](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)

4. En el panel de **Red sombrero Enterprise Linux 7.2** , debería ver más información sobre la imagen de la máquina virtual. En la lista desplegable **Seleccione un modelo de implementación** , seleccione **clásico**. A continuación, haga clic en el botón **crear** .

    ![Ver detalles de la imagen](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)

5. En el panel **Crear VM** , escriba el **Nombre de Host** para el nuevo equipo virtual. También puede especificar un nombre de usuario de administrador local en el campo **nombre de usuario** y una **contraseña**. También puede usar una clave SSH para autenticar al usuario administrador local. Seleccionar un **Nivel de precios** para la máquina virtual.

    ![Crear VM - detalles básicos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)

6. Haga clic en **Configuración opcional**. En el panel **Configuración opcional** , haga clic en **red**.

    ![Crear VM: configurar una red virtual](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)

7. Esto incorpora un panel titulado **red**. En el panel de la **red** , haga clic en **Una red Virtual** para seleccionar la red virtual a la que se debe implementar la VM Linux. Se abre el panel de **Red Virtual** . En el panel de la **Red Virtual** , elija la opción **utilizar una red virtual existente** . A continuación, seleccione la red virtual en el que los servicios de dominio de Azure AD está disponible. En este ejemplo, hemos elegido la red virtual 'MyPreviewVNet'.

    ![Crear VM: seleccione una red virtual](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)

8. En el panel **Configuración opcional** , haga clic en el botón **Aceptar** .

    ![Crear VM - red virtual seleccionada](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)

9. Ya está listo para crear la máquina virtual. En el panel **Crear VM** , haga clic en el botón **crear** .

    ![Crear VM - listo](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)

10. Implementación de la máquina virtual nueva basada en la imagen de RHEL 7.2 debe comenzar.

  ![Crear VM - implementación iniciado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)

11. Después de unos minutos, la máquina virtual se deben implementar correctamente y preparada para su uso.

  ![Crear VM - implementado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)



## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Conectarse a la máquina virtual de Linux recientemente creada de forma remota
La máquina virtual de RHEL 7.2 se haya aprovisionado en Azure. La siguiente tarea es conectarse remotamente a la máquina virtual.

**Conectarse a la máquina virtual de RHEL 7.2** Siga las instrucciones en el artículo [cómo iniciar sesión en una máquina virtual con Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

El resto de los pasos se supone que utiliza el cliente PuTTY SSH para conectarse a la máquina virtual RHEL. Para obtener más información, consulte la [página de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Abra el programa masilla.

2. Escriba el **Nombre de Host** de la máquina virtual RHEL recién creada. En este ejemplo, nuestra máquina virtual tiene el nombre de host 'contoso rhel.cloudapp .net'. Si no está seguro del nombre de host de la máquina virtual, consulte el panel VM en el portal de Azure.

    ![Conectar puTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)

3. Inicie sesión en la máquina virtual usando las credenciales de administrador local que especificó cuando se creó la máquina virtual. En este ejemplo, hemos usado el "mahesh" de la cuenta de administrador local.

    ![Inicio de sesión masilla](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalar paquetes necesarios en el equipo virtual Linux
Después de conectar con la máquina virtual, la siguiente tarea es instalar paquetes necesarios para unirse a un dominio en la máquina virtual. Realice los pasos siguientes:

1. **Instalar realmd:** El paquete de realmd se usa para unirse a un dominio. En su masilla terminal, escriba el siguiente comando:

    SUDO yum instalar realmd

    ![Instalar realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    Después de unos minutos, obtener debe instalar el paquete de realmd en la máquina virtual.

    ![realmd instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)

3. **Instalar sssd:** El paquete realmd depende de sssd para realizar operaciones de unión de dominio. En su masilla terminal, escriba el siguiente comando:

    SUDO yum instalar sssd

    ![Instalar sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    Después de unos minutos, obtener debe instalar el paquete de sssd en la máquina virtual.

    ![realmd instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)

4. **Instalar kerberos:** En su masilla terminal, escriba el siguiente comando:

    SUDO yum instalar krb5 estación de trabajo krb5-bibliotecas

    ![Instalar kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

    Después de unos minutos, obtener debe instalar el paquete de realmd en la máquina virtual.

    ![Kerberos instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Unirse a la máquina virtual Linux al dominio administrado
Ahora que los paquetes necesarios están instalados en el equipo virtual Linux, la siguiente tarea es unirse a la máquina virtual para el dominio administrado.

1. Descubra el dominio de servicios de dominio de AAD administrado. En su masilla terminal, escriba el siguiente comando:

    SUDO territorio descubrir CONTOSO100.COM

    ![Descubrir territorio](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

    Si no puede encontrar su dominio administrado **territorio descubrir** , asegúrese de que el dominio es accesible desde el equipo virtual (pruebe ping). Asegúrese de que la máquina virtual se ha implementado en realidad a la misma red virtual en el que está disponible el dominio administrado.

2. Iniciar kerberos. En su masilla terminal, escriba el siguiente comando. Asegúrese de que especifica un usuario que pertenece al grupo 'AAD DC administradores'. Sólo estos usuarios pueden unirse a equipos en el dominio administrado.

    kinitbob@CONTOSO100.COM

    ![Kinit](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    Asegúrese de que especifique el nombre de dominio en letras mayúsculas, se produce un error en kinit más.

3. Unirse a la máquina al dominio. En su masilla terminal, escriba el siguiente comando. Especifique el mismo usuario especificado en el paso anterior ('kinit').

    combinación de territorio SUDO--CONTOSO100.COM -U detallado'bob@CONTOSO100.COM'

    ![Combinación de territorio](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Debe obtener un mensaje ("correctamente inscrito máquina en territorio") cuando el equipo está conectado correctamente al dominio administrado.


## <a name="verify-domain-join"></a>Comprobar la unión a un dominio
Para comprobar rápidamente si el equipo se ha unido correctamente al dominio administrado. Conectarse a la recién dominio unido VM RHEL mediante SSH y una cuenta de usuario de dominio y Active para ver si la cuenta de usuario se ha resuelto.

1. En su masilla terminal, escriba el comando siguiente para conectarse a la recién dominio unido máquina virtual RHEL mediante SSH. Usar una cuenta de dominio que pertenece al dominio administrado (por ejemplo, 'bob@CONTOSO100.COM' en este caso.)

    SSH -l bob@CONTOSO100.COM rhel.cloudapp.net de contoso

2. En su masilla terminal, escriba el comando siguiente para ver si el directorio de inicio se ha inicializado correctamente.

    PWD

3. En su masilla terminal, escriba el comando siguiente para ver si la pertenencia a grupos se resuelven correctamente.

    Id.

La salida de ejemplo de estos comandos siguiente:

![Comprobar la unión a un dominio](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)


## <a name="troubleshooting-domain-join"></a>Unirse a un dominio de solución de problemas
Consulte el artículo de [solución de problemas unirse a un dominio](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join) .


## <a name="related-content"></a>Contenido relacionado
- [Servicios de dominio de Azure AD - Guía de introducción](./active-directory-ds-getting-started.md)

- [Unirse a una máquina virtual de Windows Server a un dominio administrado de servicios de dominio de Active Directory de Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Cómo iniciar sesión en una máquina virtual con Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

- [Instalar Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)

- [Rojos sombrero Enterprise Linux 7 - Guía de integración de Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
