## <a name="download-install-and-register-the-azure-backup-agent"></a>Descargar, instalar y registrar al agente de copia de seguridad de Azure

Después de crear el depósito de copia de seguridad de Azure, debe instalarse un agente en cada uno de los equipos de Windows (Windows Server, el cliente de Windows, Administrador de protección de datos de System Center server o equipo servidor de copia de seguridad de Azure) que permite la copia de seguridad de datos y aplicaciones para Azure.

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com/)

2. Haga clic en **Servicios de recuperación**y luego seleccione el depósito de copia de seguridad que desea registrar con un servidor. Aparece la página de inicio rápido para ese depósito de copia de seguridad.

    ![Inicio rápido](./media/backup-install-agent/quickstart.png)

3. En la página de inicio rápido, haga clic en la opción **de Windows Server o el Administrador de protección de datos de System Center o el cliente de Windows** en **Agente de descarga**. Haga clic en **Guardar** para copiarlo en el equipo local.

    ![Guardar agente](./media/backup-install-agent/agent.png)

4. Una vez instalado el agente, haga doble clic en MARSAgentInstaller.exe para iniciar la instalación del agente de copia de seguridad de Azure. Elija la carpeta de instalación y la carpeta Borrador requerido para el agente. La ubicación de caché especificada debe tener espacio libre que se encuentra al menos del 5% de los datos de copia de seguridad.

5.  Si usa un servidor proxy para conectarse a internet, en la pantalla de **configuración de Proxy** , escriba los detalles del servidor proxy. Si utiliza a un proxy autenticado, escriba los detalles de nombre y contraseña de usuario en esta pantalla.

6.  El agente de copia de seguridad de Azure instala .NET Framework 4.5 y Windows PowerShell (si aún no está disponible) para completar la instalación.

7.  Una vez instalado el agente, haga clic en el botón **Continuar para el registro** para continuar con el flujo de trabajo.

    ![Registrar](./media/backup-install-agent/register.png)

8. En la pantalla de las credenciales de la cámara, busque y seleccione el archivo de credenciales de cámara que se ha descargado anteriormente.

    ![Credenciales de cámara](./media/backup-install-agent/vc.png)

    El archivo de credenciales de cámara solo es válido para 48 horas (después de descargarlo desde el portal). Si encuentra algún error en este pantalla (por ejemplo "archivo de credenciales de depósito proporcionado ha expirado"), iniciar la sesión en el portal de Azure y descargar de nuevo el archivo de credenciales de la cámara.

    Asegúrese de que el archivo de credenciales de la cámara está disponible en una ubicación que puede tener acceso a la aplicación de configuración. Si se producen errores relacionados de acceso, copie el archivo de credenciales de la cámara a una ubicación temporal en este equipo y vuelva a intentar la operación.

    Si se produce un error de credenciales de cámara no válido (por ejemplo, "credenciales de la cámara no válido proporcionadas") el archivo está dañado o no tienen las últimas credenciales asociadas con el servicio de recuperación. Reintentar la operación después de descargar un nuevo archivo de credenciales de depósito desde el portal. Este error suele aparecer si el usuario hace clic en la opción de **credenciales de la cámara de descarga** en el portal de Azure, seguidas rápido. En este caso, solo el segundo archivo de credenciales de depósito es válido.

9. En la pantalla de **configuración de cifrado** , puede generar una frase de contraseña o proporcione una frase de contraseña (mínimo de 16 caracteres). Recuerde guardar la frase de contraseña en una ubicación segura.

    ![Cifrado](./media/backup-install-agent/encryption.png)

    > [AZURE.WARNING] Si la frase de contraseña es perdido u olvidado; Microsoft no puede ayudar a recuperar los datos de copia de seguridad. El usuario final posee la frase de contraseña de cifrado y Microsoft no tiene visibilidad en la frase de contraseña usado por el usuario final. Guarde el archivo en una ubicación segura sea necesario durante una operación de recuperación.

10. Una vez que haga clic en el botón **Finalizar** , el equipo está registrado correctamente en la cámara y ya está listo para iniciar copias de Microsoft Azure.

11. Cuando se usa la copia de seguridad de Microsoft Azure independiente puede modificar la configuración especificada durante el flujo de trabajo de registro haciendo clic en la opción de **Cambiar las propiedades** en el complemento mmc de copia de seguridad de Azure.

    ![Cambiar las propiedades](./media/backup-install-agent/change.png)

    Como alternativa, al utilizar el Administrador de protección de datos, puede modificar la configuración especificada durante el flujo de trabajo de registro haciendo clic en la opción **Configurar** seleccionando **en línea** en la pestaña **administración** .

    ![Configurar la copia de seguridad de Azure](./media/backup-install-agent/configure.png)
