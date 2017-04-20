<properties
    pageTitle="Azure Active Directory Domain Services: Unirse a una máquina virtual de servidor de Windows a un dominio administrado | Microsoft Azure"
    description="Unirse a una máquina virtual de Windows Server en servicios de dominio de Active Directory de Azure"
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

# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Unirse a una máquina virtual de Windows Server a un dominio administrado

> [AZURE.SELECTOR]
- [Azure portal clásica - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

En este artículo se muestra cómo unirse a una máquina virtual ejecuta Windows Server 2012 R2 a un dominio de servicios de dominio de Active Directory de Azure administrado, con el portal de clásico de Azure.


## <a name="step-1-create-the-windows-server-virtual-machine"></a>Paso 1: Crear la máquina virtual de Windows Server
Siga las instrucciones descritas en el tutorial de [crear una máquina virtual con Windows en el portal de clásico de Azure](../virtual-machines/virtual-machines-windows-classic-tutorial.md) . Es importante para asegurarse de que el recién creado máquina virtual se ha unido a la misma red virtual que había habilitado Servicios de dominio de Active Directory de Azure. La opción 'Crear rápido' no podrá unirse a la máquina virtual a una red virtual. Por lo tanto, debe usar la opción 'Desde la Galería' para crear la máquina virtual.

Realice los pasos siguientes para crear una máquina virtual de Windows unida a la red virtual en el que se han habilitado Servicios de dominio de Active Directory de Azure.

1. En el portal de clásico Azure, en la barra de comandos en la parte inferior de la ventana, haga clic en **nuevo**.

2. En **calcular**, haga clic en **Máquina Virtual**, haga clic en **Desde la Galería**.

3. La primera pantalla le permite **Elegir una imagen** de la máquina virtual de la lista de imágenes disponibles. Seleccione la imagen correspondiente.

    ![Seleccione imagen](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. La segunda pantalla le permite seleccionar un nombre del equipo, el tamaño y el nombre de usuario administrativo y la contraseña. Use el nivel y tamaño necesarios para ejecutar la aplicación o la carga de trabajo. El nombre de usuario que seleccione aquí es un usuario de administrador local en el equipo. No escriba aquí las credenciales de una cuenta usuario de dominio.

    ![Configuración de máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)

5. La tercera pantalla le permite configurar recursos de disponibilidad, almacenamiento y redes. Asegúrese de que seleccionar la red virtual que había habilitado Servicios de dominio de Active Directory de Azure en la lista desplegable de la **Red de grupo y virtuales o de afinidad de región** . Especifique un **Nombre de DNS del servicio de nube** según corresponda para la máquina virtual.

    ![Seleccione una red virtual para máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [AZURE.WARNING]
    Asegúrese de que se une a la máquina virtual a la misma red virtual en el que se han habilitado Servicios de dominio de Active Directory de Azure. Como resultado, la máquina virtual puede 'vea' el dominio y realizar tareas como unirse al dominio. Si elige crear la máquina virtual en una red virtual diferente, conectar una red virtual a la red virtual en el que se han habilitado Servicios de dominio de Active Directory de Azure.

6. La cuarta pantalla le permite instalar al agente de máquina virtual y configurar algunas de las extensiones disponibles.

    ![Finalizado](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)

7. Después de crear la máquina virtual, el portal clásico muestra la nueva máquina virtual bajo el nodo de **máquinas virtuales de Windows** . Tanto el servicio de nube y máquina virtual se inician automáticamente y su estado aparece como **ejecutando**.

    ![Máquina virtual está en funcionamiento](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Paso 2: Conectar con la máquina virtual de Windows Server con la cuenta de administrador local
Ahora, se conecta a la máquina de virtual de Windows Server recién creada, para unirse al dominio. Use las credenciales de administrador local que especificó al crear la máquina virtual, para conectarse a él.

Realice los pasos siguientes para conectarse a la máquina virtual.

1. Desplácese al nodo de **máquinas virtuales** en el portal de clásico. Seleccione la máquina virtual que creó en el paso 1 y haga clic en **Conectar** en la barra de comandos en la parte inferior de la ventana.

    ![Conectar a máquina virtual de Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. El portal clásico le pregunta si desea abrir o guardar un archivo con una extensión 'RDP', que se usa para conectarse a la máquina virtual. Haga clic para abrir el archivo cuando ha finalizado la descarga.

3. En el símbolo de inicio de sesión, escriba sus **credenciales de administrador local**, que ha especificado al crear la máquina virtual. Por ejemplo, hemos utilizado 'localhost\mahesh' en este ejemplo.

En este momento, debe iniciar sesión en el equipo de virtual de Windows recién creado con credenciales de administrador locales. El siguiente paso es unirse a la máquina virtual al dominio.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>Paso 3: Unirse a la máquina virtual de Windows Server para el dominio administrado AAD DS
Realice los pasos siguientes para unirse a la máquina virtual de Windows Server para el dominio administrado AAD DS.

1. Conectar con el servidor de Windows, tal como se muestra en el paso 2. Desde la pantalla de inicio, abra el **Administrador del servidor**.

2. Haga clic en el **Servidor Local** en el panel izquierdo de la ventana Administrador del servidor.

    ![Iniciar el administrador Server en máquina virtual](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. En la sección **Propiedades** , haga clic en **grupo de trabajo** . En la página de **Propiedades de sistema** , haga clic en **cambiar** para unirse al dominio.

    ![Página de propiedades del sistema](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

4. Especifique el nombre de dominio de los servicios de dominio de Active Directory de Azure administra el dominio en el cuadro de texto **dominio** y haga clic en **Aceptar**.

    ![Especifique el dominio que se unirán](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

5. Le pedirá que introduzca sus credenciales para unirse al dominio. Asegúrese de **Especifique las credenciales de un usuario que pertenecen a los administradores de DC AAD** grupo. Sólo los miembros de este grupo tienen privilegios para unir máquinas al dominio administrado.

    ![Especifique las credenciales para unirse a un dominio](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

6. Puede especificar credenciales en cualquiera de las siguientes maneras:

    - Formato UPN: especifique el sufijo UPN para la cuenta de usuario, como se ha configurado en Azure AD. En este ejemplo, es el sufijo UPN del usuario 'bob' 'bob@domainservicespreview.onmicrosoft.com'.

    - Formato de SAMAccountName: puede especificar el nombre de cuenta en el formato de SAMAccountName. En este ejemplo, el usuario 'bob' necesarias para introducir 'CONTOSO100\bob'.

        > [AZURE.NOTE] **Se recomienda usar el formato UPN para especificar las credenciales.** El atributo SAMAccountName puede ser generado automáticamente si el prefijo del UPN de un usuario es demasiado larga (por ejemplo, 'joereallylongnameuser'). Si varios usuarios que tienen el mismo prefijo UPN (por ejemplo, ' bob') en su inquilino de Azure AD, su formato SAMAccountName puede generado automáticamente por el servicio. En estos casos, el formato UPN puede usarse sujeto a iniciar sesión en el dominio.

7. Después de unirse a un dominio es correcta, verá el siguiente mensaje de bienvenida al dominio. Reinicie el equipo virtual completar la operación de combinación de dominio.

    ![Bienvenido al dominio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>Unirse a un dominio de solución de problemas
### <a name="connectivity-issues"></a>Problemas de conectividad
Si no puede encontrar el dominio de la máquina virtual, consulte los siguientes pasos de solución de problemas:

- Asegúrese de que la máquina virtual está conectada a la misma red virtual que se han habilitado en los servicios de dominio. Si no, la máquina virtual no puede conectarse al dominio y, por tanto, no puede unirse al dominio.

- Si la máquina virtual está conectada a otra red virtual, asegúrese de que esta red virtual está conectada a la red virtual en el que se han habilitado Servicios de dominio.

- Intente hacer ping del dominio con el nombre de dominio del dominio administrado (por ejemplo, ' ping contoso100.com'). Si no puede hacerlo, intente hacer ping a las direcciones IP del dominio que se muestra en la página donde se ha habilitado Servicios de dominio de Active Directory de Azure (por ejemplo, ' ping 10.0.0.4'). Si puede hacer ping a la dirección IP, pero no en el dominio, DNS pueden configurarse incorrectamente. Es posible no ha configurado las direcciones IP del dominio como servidores DNS de la red virtual.

- Intente vaciar la caché del solucionador DNS en la máquina virtual (¡' ipconfig/flushdns').

Si abre el cuadro de diálogo que solicita las credenciales para unirse al dominio, no tiene problemas de conectividad.


### <a name="credentials-related-issues"></a>Problemas relacionados con las credenciales
Si tiene problemas con las credenciales y no puede unirse al dominio, consulte los siguientes pasos.

- Intente usar el formato UPN para especificar las credenciales. SAMAccountName para su cuenta puede estar generado automáticamente si hay varios usuarios con el mismo prefijo UPN en su inquilino o si el prefijo UPN es demasiado largo. Por lo tanto, el formato de SAMAccountName para su cuenta pueden diferir de lo que espera o usar en su dominio local.

- Pruebe a utilizar las credenciales de una cuenta de usuario pertenece al grupo 'AAD DC administradores' para unir máquinas al dominio administrado.

- Asegurarse de que tiene [habilitada la sincronización de contraseñas](active-directory-ds-getting-started-password-sync.md) según los pasos indicados en la Guía de introducción.

- Asegúrese de que utiliza el UPN del usuario que se configuró en Azure AD (por ejemplo, 'bob@domainservicespreview.onmicrosoft.com') para iniciar sesión.

- Asegúrese de que tardó tiempo suficiente para que la sincronización de contraseñas completar como se especifica en la Guía de introducción.


## <a name="related-content"></a>Contenido relacionado

- [Servicios de dominio de Azure AD - Guía de introducción](./active-directory-ds-getting-started.md)

- [Administrar un dominio administrado de servicios de dominio de Active Directory de Azure](./active-directory-ds-admin-guide-administer-domain.md)
