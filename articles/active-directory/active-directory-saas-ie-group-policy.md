<properties
    pageTitle="Cómo implementar la extensión del Panel de acceso para Internet Explorer con directivas de grupo | Microsoft Azure"
    description="Cómo usar la directiva de grupo para implementar el complemento de Internet Explorer para el portal de mis aplicaciones."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/16/2016"
    ms.author="markvi"/>

#<a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Cómo implementar la extensión del Panel de acceso para Internet Explorer con directivas de grupo

En este tutorial se muestra cómo usar la directiva de grupo para instalar la extensión del Panel de acceso de forma remota para Internet Explorer en los equipos de los usuarios. Esta extensión es necesaria para los usuarios de Internet Explorer que tenga que iniciar sesión en aplicaciones que se han configurado con [basada en la contraseña de inicio de sesión único](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

Se recomienda que los administradores automatizan la implementación de esta extensión. En caso contrario, los usuarios tendrán que descargar e instalar la extensión, que es susceptible a errores del usuario y requiere permisos de administrador. Este tutorial describe un método para automatizar las implementaciones de software mediante la directiva de grupo. [Más información sobre la directiva de grupo.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

También está disponible para [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) y [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), ninguno de los cuales requieren permisos de administrador para instalar la extensión del Panel de acceso.

##<a name="prerequisites"></a>Requisitos previos

- Ha configurado [Los servicios de dominio de Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)y ha unido a los equipos de los usuarios a su dominio.
- Debe tener el permiso de "Editar configuración" para modificar objetos de directiva de grupo (GPO). De forma predeterminada, los miembros de los siguientes grupos de seguridad tienen este permiso: los administradores de dominio, los administradores de empresa y los propietarios del creador de directivas de grupo. [Aprende más.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

##<a name="step-1-create-the-distribution-point"></a>Paso 1: Crear el punto de distribución

En primer lugar, debe colocar el paquete de instalación en una ubicación de red que puede tener acceso desde todos los equipos que se desean instalar la extensión de forma remota en. Para ello, siga estos pasos:

1. Inicie sesión en el servidor como administrador

2. En la ventana **Administrador del servidor** , vaya a los **archivos y los servicios de almacenamiento**.

    ![Abra y servicios de almacenamiento de archivos](./media/active-directory-saas-ie-group-policy/files-services.png)

3. Vaya a la pestaña **recursos compartidos** . A continuación, haga clic en **tareas** > **Nuevo recurso compartido...**

    ![Abra y servicios de almacenamiento de archivos](./media/active-directory-saas-ie-group-policy/shares.png)

4. Completar el **Asistente para nueva de compartir** y establecer permisos para asegurarse de que se puede tener acceso desde los equipos de los usuarios. [Más información sobre recursos compartidos.](https://technet.microsoft.com/library/cc753175.aspx)

5. Descargar el paquete de Microsoft Windows Installer (archivo .msi) siguiente: [Extension.msi del Panel de acceso](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)

6. Copie el paquete de instalación en la ubicación deseada en el recurso compartido.

    ![Copiar el archivo .msi que está la opción compartir.](./media/active-directory-saas-ie-group-policy/copy-package.png)

8. Compruebe que los equipos cliente tienen acceso al paquete de instalación desde el recurso compartido. 

##<a name="step-2-create-the-group-policy-object"></a>Paso 2: Crear el objeto de directiva de grupo

1. Inicie sesión en el servidor que hospeda la instalación de los servicios de dominio de Active Directory (AD DS).

2. En el Administrador de servidor, vaya a **Herramientas** > **Administración de directivas de grupo**.

    ![Vaya a Herramientas > Administración de directiva de grupo](./media/active-directory-saas-ie-group-policy/tools-gpm.png)

3. En el panel izquierdo de la ventana de **Administración de directivas de grupo** , ver la jerarquía de unidad organizativa (OU) y determinar qué ámbito desea aplicar la directiva de grupo. Por ejemplo, puede decidir elegir una pequeña OU para implementar unos cuantos usuarios para realizar pruebas o puede elegir una unidad organizativa de nivel superior para implementar toda la organización.

    > [AZURE.NOTE] Si desea crear o editar sus unidades organizativas (OU), cambiar volver al administrador de servidores y vaya a **Herramientas** > **equipos y usuarios de Active Directory**.

4. Una vez que haya seleccionado una unidad organizativa, haga clic en él y seleccione **crear un GPO en este dominio y vincularlo aquí...**

    ![Crear un nuevo GPO](./media/active-directory-saas-ie-group-policy/create-gpo.png)

5. En el símbolo de **Nuevo GPO** , escriba un nombre para el nuevo objeto de directiva de grupo.

    ![Nombre el nuevo GPO](./media/active-directory-saas-ie-group-policy/name-gpo.png)

6. Haga clic en el objeto de directiva de grupo que acaba de crear y seleccione **Editar**.

    ![Modificar el nuevo GPO](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

##<a name="step-3-assign-the-installation-package"></a>Paso 3: Asignar el paquete de instalación

1. Determinar si desea implementar la extensión basada en **Configuración del equipo** o **Configuración de usuario**. Cuando se usa la [Configuración del equipo](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), se instalará la extensión en el equipo con independencia de que los usuarios que inicien sesión en él. Por otro lado, con la [configuración de usuario](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), los usuarios tendrán la extensión instalada para ellos independientemente de los equipos en el que inicien sesión.

2. En el panel izquierdo de la ventana **Editor de administración de directiva de grupo** , vaya a cualquiera de las siguientes rutas de carpeta, dependiendo del tipo de configuración que eligió:
    - `Computer Configuration/Policies/Software Settings/`
    - `User Configuration/Policies/Software Settings/`

3. Haga clic en la **instalación de Software**, a continuación, seleccione **nuevo** > **paquete...**

    ![Crear un nuevo paquete de instalación de software](./media/active-directory-saas-ie-group-policy/new-package.png)

4. Vaya a la carpeta compartida que contiene el paquete de instalación de [paso 1: crear el punto de distribución](#step-1-create-the-distribution-point), seleccione el archivo .msi y haga clic en **Abrir**.

    > [AZURE.IMPORTANT] Si el recurso compartido se encuentra en el mismo servidor, compruebe que tiene acceso el archivo .msi a través de la ruta de acceso del archivo de red, en lugar de la ruta de acceso local.

    ![Seleccione el paquete de instalación de la carpeta compartida.](./media/active-directory-saas-ie-group-policy/select-package.png)

5. En el símbolo de **Instalar Software** , seleccione **asignado** el método de distribución. A continuación, haga clic en **Aceptar**.

    ![Seleccione asignado, haga clic en Aceptar.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

Ahora se implementa la extensión a la unidad organizativa que haya seleccionado. [Más información sobre la instalación de Software de directiva de grupo.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

##<a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Paso 4: Habilitar automáticamente la extensión para Internet Explorer 

Además de ejecutar el programa de instalación, todas las extensiones de Internet Explorer deben habilitarse explícitamente antes de que se puede usar. Siga los pasos siguientes para habilitar la extensión de Panel de acceso mediante Directiva de grupo:

1. En la ventana **Editor de administración de directiva de grupo** , vaya a cualquiera de las siguientes acciones, dependiendo del tipo de configuración que eligió en [paso 3: asignar el paquete de instalación](#step-3-assign-the-installation-package):
    - `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

2. Haga clic en la **Lista de complementos**y seleccione **Editar**.
    ![Editar lista de complementos.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)

3. En la ventana de la **Lista de complementos** , seleccione **activado**. A continuación, en la sección de **Opciones** , haga clic en **Mostrar...**.

    ![Haga clic en habilitar y, a continuación, haga clic en mostrar...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)

4. En la ventana **Mostrar contenido** , siga estos pasos:

    1. Para la primera columna (campo de **Valor de nombre** ), copie y pegue el ID de clase siguientes:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`

    2. Para la segunda columna (campo de **valor** ), escriba el siguiente valor:`1`

    3. Haga clic en **Aceptar** para cerrar la ventana **Mostrar contenido** .

    ![Rellene los valores especificados anteriormente.](./media/active-directory-saas-ie-group-policy/show-contents.png)

5. Haga clic en **Aceptar** para aplicar los cambios y cierre la ventana de la **Lista de complementos** .

La extensión ahora debe habilitarse para los equipos de la OU seleccionada. [Más información sobre el uso de directiva de grupo para habilitar o deshabilitar los complementos de Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

##<a name="step-5-optional-disable-remember-password-prompt"></a>Paso 5 (opcional): desactivar el aviso de "Recordar contraseña"

Cuando los usuarios de inicio de sesión en sitios Web que utilizan la extensión del Panel de acceso, Internet Explorer puede mostrar el siguiente mensaje que le pregunta "¿desea almacenar la contraseña?"

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Si desea evitar que los usuarios vean este aviso, siga los pasos siguientes para evitar que Autocompletar de recordar contraseñas:

1. En la ventana **Editor de administración de directiva de grupo** , vaya a la ruta de acceso que se muestran a continuación. Tenga en cuenta que esta configuración solo está disponible en **Configuración de usuario**.
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`

2. Buscar la configuración denominada **activar la característica Autocompletar para nombres de usuario y contraseñas en formularios**.

    > [AZURE.NOTE] Las versiones anteriores de Active Directory pueden mostrar esta configuración con el nombre **no permitir que Autocompletar guarde las contraseñas**. La configuración para dicha configuración difiere de la configuración que se describe en este tutorial.

    ![No olvide buscar en configuración de usuario.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)

3. Haga clic con el botón secundario en la configuración anterior y seleccione **Editar**.

4. En la ventana **activar la característica Autocompletar para nombres de usuario y contraseñas en formularios**, seleccione **deshabilitado**.

    ![Seleccione Desactivar](./media/active-directory-saas-ie-group-policy/disable-passwords.png)

5. Haga clic en **Aceptar** para aplicar los cambios y cierre la ventana.

Los usuarios ya no podrán almacenar sus credenciales o usar Autocompletar para tener acceso a las credenciales almacenadas previamente. Sin embargo, esta directiva Permitir a los usuarios seguir utilizando Autocompletar para otros tipos de campos de formulario, como campos de búsqueda.

> [AZURE.WARNING] Si se habilita esta directiva después de que los usuarios han decidido almacenar algunas credenciales, esta se directiva *no* borrar las credenciales que ya se han almacenado.

##<a name="step-6-testing-the-deployment"></a>Paso 6: Probar la implementación

Siga los pasos siguientes para comprobar si la implementación de extensión se realizó correctamente:

1. Si implementa mediante la **Configuración del equipo**, inicie sesión en un equipo cliente que pertenece a la unidad organizativa que seleccionó en [paso 2: crear el objeto de directiva de grupo](#step-2-create-the-group-policy-object). Si implementa mediante la **Configuración de usuario**, asegúrese de iniciar sesión como un usuario que pertenece a esa unidad organizativa.

2. Puede tardar un par de inicio de sesión ins para la directiva de grupo cambia a totalmente actualizar con este equipo. Para forzar la actualización, abra una ventana del **símbolo del sistema** y ejecute el siguiente comando:`gpupdate /force`

3. Debe reiniciar el equipo para la instalación se producirá. Arranque puede tardar mucho más tiempo de instalaciones habitual mientras la extensión.

4. Después de reiniciar, abra **Internet Explorer**. En la esquina superior derecha de la ventana, haga clic en **Herramientas** (icono del engranaje) y, a continuación, seleccione **Administrar complementos**.

    ![Vaya a Herramientas > Administrar complementos](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)

5. En la ventana **Administrar complementos** , compruebe que se ha instalado la **Extensión del Panel de acceso** y que se ha establecido su **estado** en **habilitado**.

    ![Compruebe que la extensión del Panel de acceso está instalada y activada.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Artículos relacionados

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
- [Acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [Solución de problemas de la extensión de Panel de acceso a Internet Explorer](active-directory-saas-ie-troubleshooting.md)
