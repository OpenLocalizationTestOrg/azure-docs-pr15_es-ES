<properties
    pageTitle="Instalar IIS en su primera máquina virtual de Windows | Microsoft Azure"
    description="Experimentar con la primera máquina virtual de Windows mediante la instalación de IIS y abrir el puerto 80 con el portal de Azure."
    keywords=""
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="cynthn"/>

# <a name="experiment-with-installing-a-role-on-your-windows-vm"></a>Experimentar con la instalación de una función en la máquina virtual de Windows
    
Una vez que la primera máquina virtual (VM) marcha, puede mover instalar software y servicios. En este tutorial, vamos a utilizar el administrador del servidor en la máquina virtual de servidor de Windows para instalar IIS. A continuación, vamos a crear un grupo de seguridad de red (GSN) con el portal de Azure para abrir el puerto 80 para el tráfico IIS. 

Si aún no ha creado su primera VM, debe volver a [crear su primera máquina virtual de Windows en el portal de Azure](virtual-machines-windows-hero-tutorial.md) antes de continuar con este tutorial.

## <a name="make-sure-the-vm-is-running"></a>Asegúrese de que se está ejecutando la máquina virtual

1. Abra el [portal de Azure](https://portal.azure.com).
2. En el menú de concentrador, haga clic en **máquinas virtuales de Windows**. Seleccione la máquina virtual de la lista.
3. Si el estado es **detenido (Deallocated)**, haga clic en el botón **Inicio** , en el módulo de **aspectos básicos** de la máquina virtual. Si el estado se está **ejecutando**, puede mover al siguiente paso.

## <a name="connect-to-the-virtual-machine-and-sign-in"></a>Conectarse a la máquina virtual e inicie sesión

1.  En el menú de concentrador, haga clic en **máquinas virtuales de Windows**. Seleccione la máquina virtual de la lista.

3. En el módulo de la máquina virtual, haga clic en **Conectar**. Esto crea y descarga un archivo de protocolo de escritorio remoto (RDP archivo) que es como un acceso directo para conectarse a su equipo. Desea guardar el archivo en el escritorio para facilitar el acceso. **Abrir** este archivo para conectarse a su máquina virtual.

    ![Captura de pantalla del portal Azure que muestra cómo conectarse a su máquina virtual](./media/virtual-machines-windows-hero-tutorial/connect.png)

4. Se recibe una advertencia que es el RDP desde un editor desconocido. Esto es normal. En la ventana de escritorio remoto, haga clic en **Conectar** para continuar.

    ![Captura de pantalla de una advertencia sobre un editor desconocido](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)

5. En la ventana de seguridad de Windows, escriba el nombre de usuario y la contraseña de la cuenta local que creó cuando creó la máquina virtual. El nombre de usuario se introduce como *vmname*& #92; *nombre de usuario*, a continuación, haga clic en **Aceptar**.

    ![Captura de pantalla de cómo escribir el nombre de máquina virtual, el nombre de usuario y la contraseña](./media/virtual-machines-windows-hero-tutorial/credentials.png)
    
6.  Se recibe una advertencia que no se puede comprobar el certificado. Esto es normal. Haga clic en **Sí** para comprobar la identidad de la máquina virtual y finalizar la sesión.

    ![Captura de pantalla que muestra un mensaje acerca de comprobar la identidad de la máquina virtual](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)


Si tiene problemas cuando intenta conectar, consulte [solucionar problemas de escritorio remoto conexiones a un equipo de Virtual de basado en Windows Azure](virtual-machines-windows-troubleshoot-rdp-connection.md).


## <a name="install-iis-on-your-vm"></a>Instalar IIS en la máquina virtual

Ahora que ha iniciado sesión la máquina virtual, se instalará un rol de servidor para que pueda experimentar más.

1. Abra el **Administrador del servidor** si aún no está abierto. Haga clic en el menú **Inicio** y, a continuación, haga clic en **Administrador del servidor**.
2. En el **Administrador del servidor**, seleccione **Servidor Local** en el panel izquierdo. 
3. En el menú, seleccione **Administrar** > **Agregar funciones y características**.
4. En el agregar funciones y características Asistente, en la página **Tipo de instalación** , elija **basado en roles o característica de instalación**y, a continuación, haga clic en **siguiente**.

    ![Captura de pantalla que muestra la ficha Agregar funciones y características de Asistente de tipo de instalación](./media/virtual-machines-windows-hero-tutorial/role-wizard.png)

5. Seleccione la máquina virtual desde el grupo de servidores y haga clic en **siguiente**.
6. En la página de **Roles del servidor** , seleccione **Servidor Web (IIS)**.

    ![Captura de pantalla que muestra la ficha Agregar funciones y características Asistente para funciones de servidor](./media/virtual-machines-windows-hero-tutorial/add-iis.png)

7. En el menú emergente sobre cómo agregar características necesarias para IIS, asegúrese de que está seleccionada la opción **incluye herramientas de administración** y, a continuación, haga clic en **Agregar características**. Cuando se cierra la ventana emergente, haga clic en **siguiente** en el asistente.

    ![Captura de pantalla que muestra emergente confirme si desea agregar el rol IIS](./media/virtual-machines-windows-hero-tutorial/confirm-add-feature.png)

8. En la página características, haga clic en **siguiente**.
9. En la página del **Rol de servidor Web (IIS)** , haga clic en **siguiente**. 
10. En la página **Servicios del rol** , haga clic en **siguiente**. 
11. En la página de **confirmación** , haga clic en **instalar**. 
12. Una vez completada la instalación, haga clic en **Cerrar** en el asistente.



## <a name="open-port-80"></a>Abrir el puerto 80 

Fin de la máquina virtual Aceptar el tráfico de entrada en el puerto 80, debe agregar una regla de entrada para el grupo de seguridad de la red. 

1. Abra el [portal de Azure](https://portal.azure.com).
2. En **máquinas virtuales de Windows** , seleccione la máquina virtual que ha creado.
3. En la configuración de máquinas virtuales de Windows, seleccione **interfaces de red** y, a continuación, seleccione la interfaz de red existente.

    ![Captura de pantalla que muestra la configuración de la máquina virtual para las interfaces de red](./media/virtual-machines-windows-hero-tutorial/network-interface.png)

4. **Aspectos básicos** de la interfaz de red, haga clic en el **grupo de seguridad de red**.

    ![Captura de pantalla que muestra la sección Essentials para la interfaz de red](./media/virtual-machines-windows-hero-tutorial/select-nsg.png)

5. En el módulo de **Essentials** para la GSN, debe tener una regla de entrada de predeterminado existente de **rdp permitir predeterminado** que le permite iniciar sesión en la máquina virtual. Se agregará otra regla de entrada para permitir el tráfico IIS. Haga clic en **regla de seguridad entrante**.

    ![Captura de pantalla que muestra la sección Essentials para la GSN](./media/virtual-machines-windows-hero-tutorial/inbound.png)

6. En **reglas de seguridad de entrada**, haga clic en **Agregar**.

    ![Captura de pantalla que muestra el botón para agregar una regla de seguridad](./media/virtual-machines-windows-hero-tutorial/add-rule.png)

7. En **reglas de seguridad de entrada**, haga clic en **Agregar**. Escriba el intervalo de puertos **80** y asegúrese de que está seleccionado **Permitir** . Cuando haya terminado, haga clic en **Aceptar**.

    ![Captura de pantalla que muestra el botón para agregar una regla de seguridad](./media/virtual-machines-windows-hero-tutorial/port-80.png)
 
Para obtener más información sobre NSGs, reglas de entrada y salida, vea [Permitir el acceso externo a su máquina virtual con el portal de Azure](virtual-machines-windows-nsg-quickstart-portal.md)
 
## <a name="connect-to-the-default-iis-website"></a>Conectar con el sitio Web IIS de forma predeterminada

1. En el portal de Azure, haga clic en **máquinas virtuales de Windows** y, a continuación, seleccione la máquina virtual.
2. En el módulo **Essentials** , copie la **dirección IP pública**.

    ![Captura de pantalla que muestra dónde encontrar la dirección IP pública de la máquina virtual](./media/virtual-machines-windows-hero-tutorial/ipaddress.png)

2. Abra un explorador y en la barra de direcciones, escriba la dirección IP pública similar a esta: http://<publicIPaddress> y haga clic en **ENTRAR** para ir a la dirección.
3. El explorador debe abrir la página web IIS de forma predeterminada. Parece que algo parecido a esto:

    ![Captura de pantalla que muestra el aspecto de la página predeterminada de IIS en un explorador](./media/virtual-machines-windows-hero-tutorial/iis-default.png)

    

## <a name="next-steps"></a>Pasos siguientes

- También puede experimentar con [asociar un disco de datos](virtual-machines-windows-attach-disk-portal.md) a su equipo virtual. Discos de datos proporcionan más espacio de almacenamiento para la máquina virtual.
