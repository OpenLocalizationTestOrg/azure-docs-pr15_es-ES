<properties
    pageTitle="Solución de problemas de la extensión de Panel de acceso a Internet Explorer | Microsoft Azure"
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

#<a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Solución de problemas de la extensión de Panel de acceso a Internet Explorer

En este artículo le ayudará a solucionar los problemas siguientes:

- Está no tiene acceso a las aplicaciones a través del portal de mis aplicaciones al usar Internet Explorer.
- Vea el mensaje "Instalar Software" aunque ya ha instalado el software.

Si es administrador, vea también: [cómo implementar la extensión del Panel de acceso para Internet Explorer con directivas de grupo](active-directory-saas-ie-group-policy.md)

##<a name="run-the-diagnostic-tool"></a>Ejecutar la herramienta de diagnóstico

Puede diagnosticar problemas de instalación con la extensión del Panel de acceso, descargar y ejecutar la herramienta de diagnóstico del Panel de acceso:

1. [Haga clic aquí para descargar la herramienta de diagnóstico.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Abra el archivo y haga clic en botón **extraer todos** .

    ![Presione extraer todos](./media/active-directory-saas-ie-troubleshooting/extract1.png)

3. A continuación, presione el botón **extraer** para continuar.

    ![Presione extracto](./media/active-directory-saas-ie-troubleshooting/extract2.png)

4. Para ejecutar la herramienta, haga clic en el archivo denominado **AccessPanelExtensionDiagnosticTool**y después seleccione **Abrir con > Microsoft Windows Script Host de**.

    ![Abrir con > basado de Microsoft Windows Script Host](./media/active-directory-saas-ie-troubleshooting/open_tool.png)

5. A continuación, verá la siguiente ventana de diagnóstico, que describe lo que podría ser un problema con la instalación.

    ![Un ejemplo de la ventana de diagnóstico](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)

6. Haga clic en "**Sí**" para permitir que el programa corrija los problemas que se han encontrado.

7. Para guardar estos cambios, cerrar cada ventana de Internet Explorer y, a continuación, abra el Explorador de Internet.<br />Si aún no puede acceder a sus aplicaciones, intente los siguientes pasos.

##<a name="check-that-the-access-panel-extension-is-enabled"></a>Compruebe que está habilitada la extensión del Panel de acceso

Para comprobar que está habilitada la extensión del Panel de acceso en Internet Explorer:

1. En Internet Explorer, haga clic en el **icono de engranaje situado** en la esquina superior derecha de la ventana. A continuación, seleccione **Opciones de Internet**.<br />(En versiones anteriores de Internet Explorer lo puede encontrar en **Herramientas > Opciones de Internet**.

    ![Vaya a Herramientas > Opciones de Internet](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)

2. Haga clic en la ficha **programas** y luego haga clic en el botón **Administrar complementos** .

    ![Haga clic en Administrar complementos](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)

3. En este cuadro de diálogo, seleccione la **Extensión de Panel de acceso** y, a continuación, haga clic en el botón **Habilitar** .

    ![Haga clic en habilitar](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)

4. Para guardar los cambios, cierre cada ventana de Internet Explorer y, a continuación, abra el Explorador de Internet.

##<a name="enable-extensions-for-inprivate-browsing"></a>Habilitar las extensiones de exploración de InPrivate

Si está utilizando el modo de exploración de InPrivate:

1. En Internet Explorer, haga clic en el **icono de engranaje situado** en la esquina superior derecha de la ventana. A continuación, seleccione **Opciones de Internet**.<br />(En versiones anteriores de Internet Explorer lo puede encontrar en **Herramientas > Opciones de Internet**.

    ![Un ejemplo de la ventana de diagnóstico](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)

2. Vaya a la pestaña **privacidad** , a continuación, **desactive** la casilla de verificación **Deshabilitar las barras de herramientas y extensiones cuando se inicia la exploración de InPrivate**</p>

    ![Desactive las extensiones y deshabilitar las barras de herramientas cuando se inicia la exploración de InPrivate](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)

3. Para guardar los cambios, cierre cada ventana de Internet Explorer y, a continuación, abra el Explorador de Internet.

##<a name="uninstall-the-access-panel-extension"></a>Desinstale la extensión de Panel de acceso

Para desinstalar la extensión del Panel de acceso desde su equipo:

1. En el teclado, presione la **tecla Windows** para abrir el menú Inicio. Cuando se abre el menú, puede escribir nada para realizar una búsqueda. Escriba "Panel de Control" y, a continuación, abra el **Panel de Control** cuando aparece en los resultados de búsqueda.

    ![Buscar el Panel de Control](./media/active-directory-saas-ie-troubleshooting/search_sm.png)

2. En la esquina superior derecha del Panel de Control, cambie la opción **Ver por** iconos **grandes**. A continuación, busque y haga clic en el botón de **programas y características** .

    ![Cambio de la vista para mostrar iconos grandes](./media/active-directory-saas-ie-troubleshooting/control_panel.png)

3. En la lista, seleccione la **Extensión de Panel de acceso**y a continuación, haga clic en el botón **desinstalar** .

    ![Haga clic en desinstalar](./media/active-directory-saas-ie-troubleshooting/uninstall.png)

4. A continuación, puede intentar instalar la extensión de nuevo para ver si se ha resuelto el problema.

Si tiene problemas al desinstalar la extensión, también puede quitar con la herramienta [Microsoft repararlo](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Artículos relacionados

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
- [Acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [Cómo implementar la extensión del Panel de acceso para Internet Explorer con directivas de grupo](active-directory-saas-ie-group-policy.md)
