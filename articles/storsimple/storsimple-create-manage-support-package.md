<properties
   pageTitle="Crear un paquete de compatibilidad de StorSimple | Microsoft Azure"
   description="Aprenda a crear, descifrar y modificar un paquete de soporte técnico de su dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="alkohli" />


# <a name="create-and-manage-a-storsimple-support-package"></a>Crear y administrar un paquete de soporte técnico de StorSimple

## <a name="overview"></a>Información general

Un paquete de soporte técnico de StorSimple es un mecanismo de fácil de usar que recopila todos los registros relevantes para ayudar a Microsoft Support para solucionar los problemas de dispositivo StorSimple. Los registros recopilados cifra y comprimidos.

Este tutorial incluye instrucciones paso a paso para crear y administrar el paquete de compatibilidad.

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>Crear y cargar un paquete de compatibilidad en el portal de clásico de Azure

Puede crear y cargar un paquete de compatibilidad en el sitio de Microsoft Support a través de la página **Mantenimiento** del servicio en el portal de clásico de Azure.

> [AZURE.NOTE] La carga requiere una clave de paso de soporte técnico. Los ingenieros de soporte técnico deben proporcionar esto a usted en un correo electrónico.

Crea un paquete de compatibilidad cifrado y comprimido (archivo .cab) y cargarse en el sitio de soporte técnico. El técnico de soporte a continuación, puede recuperar este paquete desde el sitio de soporte técnico para solucionar el problema.

Realice los pasos siguientes en el portal de clásico para crear un paquete de compatibilidad.

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>Para crear un paquete de compatibilidad en el portal de clásico de Azure

1. Seleccione **dispositivos** > **Mantenimiento**.

2. En la sección **paquete de compatibilidad** , seleccione **crear y cargar el paquete de compatibilidad**.

3. En el cuadro de diálogo **crear y cargar el paquete de compatibilidad** , haga lo siguiente:

    ![Crear paquete de compatibilidad](./media/storsimple-create-manage-support-package/IC740923.png)

    - En el cuadro de texto de la **Clave de paso de soporte técnico** , escriba la clave. Su ingeniería de soporte técnico de Microsoft debe enviar esta clave de paso a usted por correo electrónico.

    - Seleccione la casilla de verificación para proporcionar consentimiento para cargar automáticamente el paquete de compatibilidad en el sitio de Microsoft Support.

    - Haga clic en el icono de verificación ![Icono de comprobación](./media/storsimple-create-manage-support-package/IC740895.png).


## <a name="manually-create-a-support-package"></a>Crear manualmente un paquete de compatibilidad

En algunos casos, deberá crear manualmente el paquete de soporte técnico a través de Windows PowerShell para StorSimple. Por ejemplo:

- Si necesita quitar información confidencial de sus archivos de registro antes de compartir con Microsoft Support.

- Si tiene problemas para cargar el paquete debido a problemas de conectividad.

Puede compartir el paquete de compatibilidad generados manualmente con Microsoft Support a través de correo electrónico. Realice los pasos siguientes para crear un paquete de soporte técnico de Windows PowerShell para StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Para crear un paquete de soporte técnico de Windows PowerShell para StorSimple

1. Para iniciar una sesión de Windows PowerShell como administrador en el equipo remoto que se usa para conectarse a su dispositivo StorSimple, escriba el siguiente comando:

    `Start PowerShell`

2. En la sesión de Windows PowerShell, conectarse a la consola SSAdmin del dispositivo:

    - En el símbolo del sistema, escriba:

        `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`

    1. En el cuadro de diálogo que se abre, escriba la contraseña de administrador de dispositivos. La contraseña predeterminada es:

        `Password1`

        ![Cuadro de diálogo de credenciales de PowerShell](./media/storsimple-create-manage-support-package/IC740962.png)

    2. Seleccione **Aceptar**.
    1. En el símbolo del sistema, escriba:

        `Enter-PSSession $MS`

3. En la sesión que se abre, escriba el comando adecuado.

    - Para los recursos compartidos de red que están protegidos por contraseña, escriba:

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

        Se le pedirá una contraseña, una ruta de acceso a la carpeta de red compartida y a continuación, una frase de contraseña de cifrado (porque está cifrado el paquete de compatibilidad). A continuación, se crea un paquete de compatibilidad en la carpeta especificada.

    - Para recursos compartidos que no están protegidos por contraseña, no necesita la `-Credential` parámetro. Escriba lo siguiente:

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

        Se crea el paquete de compatibilidad para ambos controladores en la carpeta compartida de red especificada. Es un archivo comprimido cifrado que puede enviarse a Microsoft Support para solucionar este problema. Para obtener más información, vea [Soporte técnico de Microsoft del contacto](storsimple-contact-microsoft-support.md).


### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Los parámetros de cmdlet HcsSupportPackage de exportación
Puede usar los siguientes parámetros con el cmdlet HcsSupportPackage de exportación.

| Parámetro            | Obligatorio opcional | Descripción                                                                                                                                                             |
|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `-Path`                 | Obligatorio          | Use esta opción para proporcionar la ubicación de la carpeta de red compartida en la que se coloca el paquete de compatibilidad.                                                                 |
| `-EncryptionPassphrase` | Obligatorio          | Use esta opción para proporcionar una frase de contraseña para ayudar a cifrar el paquete de compatibilidad.                                                                                                        |
| `-Credential`           | Opcional          | Use esta opción para proporcionar las credenciales de acceso de la carpeta de red compartida.                                                                                        |
| `-Force`                | Opcional          | Use esta opción para omitir el paso de confirmación de frase de contraseña de cifrado.                                                                                                                |
| `-PackageTag`           | Opcional          | Use esta opción para especificar un directorio en la *ruta de acceso* en la que se coloca el paquete de compatibilidad. El valor predeterminado es [nombre del dispositivo]-[fecha actual y time:yyyy-MM-dd-HH-mm-ss].       |
| `-Scope`                | Opcional          | Especificar como **clúster** (predeterminado) para crear un paquete de compatibilidad para ambos controladores. Si desea crear un paquete solo para el controlador actual, especifique el **controlador**. |


## <a name="edit-a-support-package"></a>Modificar un paquete de compatibilidad

Una vez que haya generado un paquete de compatibilidad, debe modificar el paquete para quitar información confidencial. Esto puede incluir nombres de volumen, las direcciones IP de dispositivo y nombres de copia de seguridad de los archivos de registro.

> [AZURE.IMPORTANT] Solo puede modificar un paquete de compatibilidad que se generó a través de Windows PowerShell para StorSimple. No se puede editar un paquete creado en el portal de Azure clásico con el servicio Administrador de StorSimple.

Para editar un paquete de compatibilidad antes de cargarlo en el sitio de Microsoft Support, descifrar primero el paquete de compatibilidad, edite los archivos y volver a cifrar. Realice los pasos siguientes.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Para editar un paquete de soporte técnico de Windows PowerShell para StorSimple

1. Generar un paquete de compatibilidad, como se describió anteriormente, para [crear un paquete de soporte técnico de Windows PowerShell para StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).

2. [Descargar el script](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente en el cliente.

3. Importar el módulo de Windows PowerShell. Especificar la ruta de acceso a la carpeta local en la que descargó la secuencia de comandos. Para importar el módulo, escriba:

    `Import-module <Path to the folder that contains the Windows PowerShell script>`

4. Todos los archivos son archivos de *.aes* que se comprimen y cifrados. Para descomprimir y descifrar archivos, escriba:

    `Open-HcsSupportPackage <Path to the folder that contains support package files>`

    Tenga en cuenta que las extensiones de archivo real se muestran ahora para todos los archivos.

    ![Modificar el paquete de compatibilidad](./media/storsimple-create-manage-support-package/IC750706.png)

5. Cuando se le solicite para la frase de contraseña de cifrado, escriba la contraseña que utilizó cuando se creó el paquete de compatibilidad.

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:EncryptionPassphrase: ****

6. Vaya a la carpeta que contiene los archivos de registro. Puesto que los archivos de registro ahora son descomprimir y descifrar, que tienen extensiones de archivo original. Modifique estos archivos para quitar cualquier información específica del cliente, como los nombres de volumen y las direcciones IP de dispositivo y guarde los archivos.

7. Cierre los archivos para comprimir con gzip y los cifra con AES-256. Se trata de velocidad y seguridad en transferir el paquete de compatibilidad en una red. Para comprimir y cifrar archivos, escriba lo siguiente:

    `Close-HcsSupportPackage <Path to the folder that contains support package files>`

    ![Modificar el paquete de compatibilidad](./media/storsimple-create-manage-support-package/IC750707.png)

8. Cuando se le solicite, proporcione una frase de contraseña de cifrado para el paquete de compatibilidad modificada.

        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****

9. Escriba la contraseña nueva, para que puede compartir con Microsoft Support cuando se solicita.


### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Ejemplo: Editar archivos en un paquete de compatibilidad en un recurso compartido protegido con contraseña

En el ejemplo siguiente se muestra cómo descifrar, editar y volver a cifrar un paquete de compatibilidad.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [usar paquetes de soporte técnico y los registros de dispositivo para solucionar problemas de la implementación de dispositivo](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).

- Obtenga información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).
