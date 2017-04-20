## <a name="using-vault-credentials-to-authenticate-with-the-azure-backup-service"></a>Con las credenciales de cámara para autenticar con el servicio de copia de seguridad de Azure

El servidor local (el cliente de Windows o servidor de Windows Server o el Administrador de protección de datos) es necesario autenticar con un depósito de copia de seguridad antes de que puede copiar los datos en Azure. La autenticación se logra utilizando "depósito credenciales". El concepto de credenciales de depósito es similar al concepto de un archivo de "configuración" publicación"que se usa en Azure PowerShell.

### <a name="what-is-the-vault-credential-file"></a>¿Qué es el archivo de credenciales de la cámara?

El archivo de credenciales de depósito es un certificado generado por el portal para cada depósito de copia de seguridad. El portal de carga, a continuación, la clave pública para el servicio de Control de acceso (ACS). La clave privada del certificado estará disponible para el usuario como parte del flujo de trabajo que se expresa como una entrada del flujo de trabajo de registro de equipo. Esto autentica el equipo para enviar los datos de copia de seguridad a un depósito identificado en el servicio de copia de seguridad de Azure.

Se utiliza la credencial de cámara solo durante el flujo de trabajo de registro. Es responsabilidad del usuario para asegurarse de que el archivo de credenciales de la cámara no está comprometido. Si se encuentra en manos de cualquier usuario malintencionado, el archivo de credenciales de cámara puede usarse para registrar otros equipos con el mismo depósito. Sin embargo, como los datos de copia de seguridad se cifran con una contraseña que pertenece al cliente, los datos de copia de seguridad existentes no pueden ser confidencial. Para mitigar este problema, se establecen las credenciales de la cámara para que expire en 48 horas. Puede descargar las credenciales de la cámara de un depósito de copia de seguridad cualquier número de veces, pero solo el archivo más reciente de la credencial depósito es aplicable durante el flujo de trabajo de registro.

### <a name="download-the-vault-credential-file"></a>Descargar el archivo de credenciales de cámara

El archivo de credenciales de la cámara se descargará a través de un canal seguro desde el portal de Azure. El servicio de copia de seguridad de Azure no conoce la clave privada del certificado y la clave privada no se conserva en el portal o el servicio. Realice los pasos siguientes para descargar el archivo de credenciales de la cámara en un equipo local.

1.  Inicie sesión en el [Portal de administración](https://manage.windowsazure.com/)
2.  Haga clic en **Servicios de recuperación** en el panel de navegación izquierdo y seleccione el depósito de copia de seguridad que ha creado. Haga clic en el icono de nube para ir a la vista de inicio rápido de la cámara de copia de seguridad.

    ![Vista rápida](./media/backup-download-credentials/quickview.png)

3.  En la página de inicio rápido, haga clic en **credenciales de descarga depósito**. El portal genera el archivo de credenciales de la cámara, que está disponible para su descarga.

    ![Descargar](./media/backup-download-credentials/downloadvc.png)

4.  El portal genera una credencial de cámara mediante una combinación de nombre de la cámara y la fecha actual. Haga clic en **Guardar** para descargar las credenciales de la cámara en la carpeta de descargas de la cuenta local, o seleccione Guardar como en el menú Guardar para especificar una ubicación para las credenciales de la cámara.

### <a name="note"></a>Nota
- Asegúrese de que las credenciales de la cámara se guarda en una ubicación que se puede acceder desde su equipo. Si se almacena en un recurso compartido de archivo pequeñas y medianas, compruebe los permisos de acceso.
- Se usa el archivo de credenciales de cámara solo durante el flujo de trabajo de registro.
- El archivo de credenciales de depósito caduca después de 48 horas y puede descargarse desde el portal.
- Consulte las [preguntas más frecuentes](../articles/backup/backup-azure-backup-faq.md) de copia de seguridad de Azure para las preguntas en el flujo de trabajo.
