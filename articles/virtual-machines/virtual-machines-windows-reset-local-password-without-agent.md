<properties
   pageTitle="Restablecer una contraseña de Windows local cuando no está instalado el agente de invitado Azure | Microsoft Azure"
   description="Cómo restablecer la contraseña de una cuenta de usuario de Windows local cuando el agente de invitado Azure no está instalado o funcionando en una máquina virtual"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="iainfou"/>

# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>Cómo restablecer la contraseña local de Windows Azure VM
Puede restablecer la contraseña de Windows local de una máquina virtual en el uso de Azure [portal Azure o Azure PowerShell](virtual-machines-windows-reset-rdp.md) siempre que está instalado el agente de Azure invitado. Este método es la forma principal para restablecer una contraseña para una máquina virtual de Azure. Si tiene problemas con el agente de Azure invitado no responde o se ha podido instalar después de cargar una imagen personalizada, puede restablecer manualmente una contraseña de Windows. Este artículo describe cómo restablecer la contraseña de una cuenta local adjuntando el disco virtual de origen OS a otra VM. 

> [AZURE.WARNING] Utilice este proceso solamente como último recurso. Siempre intente restablecer una contraseña con el [portal de Azure o Azure PowerShell](virtual-machines-windows-reset-rdp.md) en primer lugar.


## <a name="overview-of-the-process"></a>Información general del proceso
Los pasos principales para realizar una local restablecimiento de contraseña para una máquina virtual de Windows en Azure cuando no hay ningún acceso al agente de Azure invitado es como sigue:

- Eliminar la máquina virtual de origen. Se conservan los discos virtuales.
- Adjuntar el disco de sistema operativo de la máquina virtual de origen a otra VM dentro de su suscripción de Azure. Este VM se conoce como la máquina virtual de solución de problemas.
- Uso de la máquina virtual de solución de problemas, cree algunos archivos de configuración en el disco de sistema operativo de la máquina virtual de origen.
- Desasociar disco de sistema operativo de la máquina virtual de la máquina virtual de solución de problemas.
- Usar una plantilla de administrador de recursos para crear una máquina virtual, usando el disco virtual original.
- Cuando se inicia la nueva máquina virtual, los archivos de configuración que cree actualizan la contraseña del usuario necesario.


## <a name="detailed-steps"></a>Pasos detallados
Siempre intente restablecer una contraseña con el [portal de Azure o Azure PowerShell](virtual-machines-windows-reset-rdp.md) antes de intentar los siguientes pasos. Asegúrese de que tiene una copia de seguridad de la máquina virtual antes de empezar. 

1. Eliminar la máquina virtual afectada en el portal de Azure. La eliminación de la máquina virtual solo elimina los metadatos, la referencia de la máquina virtual dentro de Azure. Los discos virtuales se conservan cuando se elimina la máquina virtual:

    - Seleccione la máquina virtual en el portal de Azure, haga clic en *Eliminar*:

    ![Eliminar la máquina virtual existente](./media/virtual-machines-windows-reset-local-password-without-guest-agent/delete_vm.png)

2. Adjuntar el disco de sistema operativo de la máquina virtual de origen a la máquina virtual de solución de problemas. La máquina virtual de solución de problemas debe estar en la misma región como disco de sistema operativo de la máquina virtual de origen (como `West US`):

    - Seleccione la máquina virtual de solución de problemas en el portal de Azure. Haga clic en *discos* | *adjuntar existente*:

    ![Adjuntar disco existente](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_attach_existing.png)

    Seleccione *El archivo de disco duro virtual* y, a continuación, seleccione la cuenta de almacenamiento que contiene la máquina virtual de origen:

    ![Seleccione la cuenta de almacenamiento](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_storageaccount.PNG)

    Seleccione el contenedor de origen. El contenedor de origen suele ser *VHD*:

    ![Seleccionar el contenedor de almacenamiento](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_container.png)

    Seleccione el sistema operativo de disco duro virtual para adjuntar. Haga clic en *Seleccionar* para completar el proceso:

    ![Seleccione el disco virtual de origen](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_source_vhd.png)

3. Conectarse a la máquina virtual de solución de problemas con Escritorio remoto y asegúrese de que está visible disco de sistema operativo de la máquina virtual de origen:

    - Seleccione la máquina virtual de solución de problemas en el portal de Azure y haga clic en *Conectar*.
    - Abra el archivo RDP que descarga. Escriba el nombre de usuario y contraseña de la máquina virtual de solución de problemas.
    - En el Explorador de archivos, busque el disco de datos adjuntos. Si el origen disco duro virtual de máquina virtual es el disco únicamente los datos adjuntado a la máquina virtual de solución de problemas, debe ser la unidad F::

    ![Ver datos adjuntos de un disco](./media/virtual-machines-windows-reset-local-password-without-guest-agent/troubleshooting_vm_fileexplorer.png)

4. Crear `gpt.ini` en `\Windows\System32\GroupPolicy` en la unidad de la máquina virtual de origen (si existe gpt.ini, cambie a gpt.ini.bak):

    > [AZURE.WARNING] Asegúrese de no crear accidentalmente los siguientes archivos en C:\Windows, la unidad de sistema operativo de la máquina virtual de solución de problemas. Crear los siguientes archivos en la unidad de sistema operativo para el origen de la máquina virtual que se adjunta como un disco de datos.

    - Agregue las siguientes líneas en el `gpt.ini` archivo que creó:

    ```
    [General]
    gPCFunctionalityVersion=2
    gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
    Version=1
    ```

    ![Crear gpt.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_gpt_ini.png)
 
5. Crear `scripts.ini` en `\Windows\System32\GroupPolicy\Machine\Scripts`. Asegúrese de que se muestran las carpetas ocultas. Si es necesario, cree la `Machine` o `Scripts` carpetas.

    - Agregue las siguientes líneas la `scripts.ini` archivo que creó:

    ```
    [Startup]
    0CmdLine=C:\Windows\System32\FixAzureVM.cmd
    0Parameters=
    ```

    ![Crear scripts.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_scripts_ini.png)
 
6. Crear `FixAzureVM.cmd` en `\Windows\System32` con el siguiente contenido, reemplazar `<username>` y `<newpassword>` con sus propios valores:

    ```
    NET USER <username> <newpassword>
    ```

    ![Crear FixAzureVM.cmd](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_fixazure_cmd.png)

    Debe cumplir los requisitos de complejidad de contraseña configurada para la máquina virtual al definir la nueva contraseña.

7. En el portal de Azure, desconecte el disco de la máquina virtual de solución de problemas:

    - Seleccione la máquina virtual de solución de problemas en el portal de Azure, haga clic en *discos*.
    - Seleccione el disco de datos adjunto en el paso 2, haga clic en *Desasociar*:

    ![Desasociar disco](./media/virtual-machines-windows-reset-local-password-without-guest-agent/detach_disk.png)

8. Antes de crear una máquina virtual, obtener el URI en el disco de sistema operativo de origen:

    - Seleccione la cuenta de almacenamiento en el portal de Azure, haga clic en *BLOB*.
    - Seleccione el contenedor. El contenedor de origen suele ser *VHD*:

    ![Seleccione blobs de la cuenta de almacenamiento](./media/virtual-machines-windows-reset-local-password-without-guest-agent/select_storage_details.png)

    Seleccione el origen de disco duro virtual de VM OS y haga clic en el botón *Copiar* junto al nombre de la *dirección URL* :

    ![Copiar disco URI](./media/virtual-machines-windows-reset-local-password-without-guest-agent/copy_source_vhd_uri.png)

9. Crear una máquina virtual desde el disco de sistema operativo de la máquina virtual de origen:

    - Use [esta plantilla de administrador de recursos de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) para crear una máquina virtual desde un disco duro virtual especializado. Haga clic en el `Deploy to Azure` botón para abrir el portal de Azure los detalles de la plantilla rellenados.
    - Si desea conservar toda la configuración anterior de la máquina virtual, seleccione *Editar plantilla* para proporcionar su VNet existente, subred, adaptador de red o IP pública.
    - En la `OSDISKVHDURI` cuadro de texto de parámetro, pegar obtener el URI de su disco duro virtual de origen en el paso anterior:

    ![Crear una máquina virtual a partir de plantilla](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_new_vm_from_template.png)

10. Después de que la nueva máquina virtual se está ejecutando, conectarse a la máquina virtual de uso de escritorio remoto con la nueva contraseña especificada en el `FixAzureVM.cmd` secuencia de comandos.

11. Desde el equipo remoto a la nueva máquina virtual, quite los archivos siguientes para limpiar el entorno:

    - De %windir%\System32
        - quitar FixAzureVM.cmd
    - Desde %windir%\System32\GroupPolicy\Machine\
        - Quitar scripts.ini
    - Desde %windir%\System32\GroupPolicy
        - quitar gpt.ini (si gpt.ini existía antes y cambiar el nombre a gpt.ini.bak, cambie el nombre del archivo de .bak a gpt.ini)

## <a name="next-steps"></a>Pasos siguientes
Si aún no puede conectarse con Escritorio remoto, consulte la [Guía de solución de problemas de RDP](virtual-machines-windows-troubleshoot-rdp-connection.md). La [Guía de solución de problemas de RDP detallada](virtual-machines-windows-detailed-troubleshoot-rdp.md) examina métodos en lugar de pasos específicos de solución de problemas. También puede [Abrir una solicitud de soporte técnico de Azure](https://azure.microsoft.com/support/options/) para ayuda práctica.