<properties
    pageTitle="Solucionar problemas de conexión SSH a una máquina virtual | Microsoft Azure"
    description="Cómo solucionar problemas, como 'SSH conexión Error' o ' SSH rechazado ' para una máquina virtual de Azure con Linux."
    keywords="SSH conexión rechazada, ssh error, azure ssh SSH error de conexión"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Solucionar problemas de conexión SSH a una máquina virtual de Linux de Azure falla, errores, o se ha rechazado
Existen varias razones que se producen errores de Shell seguro (SSH), errores de conexión SSH, o SSH se ha rechazado cuando intenta conectar con una máquina virtual (VM) de Linux. Este artículo le ayudará a buscar y corregir los problemas. Puede usar el portal de Azure, Azure CLI o la extensión de acceso de VM para Linux para solucionar problemas de conexión.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si necesita más ayuda en cualquier punto de este artículo, póngase en contacto con los expertos de Azure en [los foros de desbordamiento de pila y Azure de MSDN](http://azure.microsoft.com/support/forums/). Como alternativa, puede abrir un incidente de soporte de Azure. Vaya el [sitio de soporte de Azure](http://azure.microsoft.com/support/options/) y seleccione **obtener soporte técnico**. Para obtener información sobre el uso de soporte técnico de Azure, lea las [preguntas más frecuentes de soporte técnico de Microsoft Azure](http://azure.microsoft.com/support/faq/).


## <a name="quick-troubleshooting-steps"></a>Pasos rápidos de solución de problemas
Después de cada paso de solución de problemas, intente volver a conectarse a la máquina virtual.

1. Restablecer la configuración de SSH.
2. Restablecer las credenciales para el usuario.
3. Compruebe que las reglas de [Grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md) permitan el tráfico SSH.
    - Asegúrese de que existe una regla de grupo de seguridad de la red para permitir el tráfico SSH (de forma predeterminada, el puerto TCP 22).
    - No puede usar la redirección de puertos / asignación sin usar un equilibrador de carga de Azure.
4. Comprobar el [estado de los recursos VM](../resource-health/resource-health-overview.md). 
    - Asegúrese de que la máquina virtual de informes es correcto.
    - Si dispone de diagnósticos de arranque habilitados, compruebe que la máquina virtual no es los informes de errores de inicio en los registros.
5. Reinicie la máquina virtual.
6. Volver a la máquina virtual.

Continúe leyendo para obtener más pasos para solucionar problemas y explicaciones.


## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Métodos disponibles para solucionar problemas de conexión SSH

Puede restablecer las credenciales o configuración de SSH mediante uno de los métodos siguientes:

- [Portal de azure](#using-the-azure-portal) - excelente si necesita restablecer rápidamente la configuración de SSH o SSH clave y no tiene instaladas las herramientas de Azure.
- [Comandos de azure CLI](#using-the-azure-cli) - si ya está en la línea de comandos rápidamente restablecer la configuración de SSH o credenciales.
- [Extensión de azure VMAccessForLinux](#using-the-vmaccess-extension) : crear y volver a los archivos de definición de json para restablecer las credenciales de usuario o configuración de SSH.

Después de cada paso de solución de problemas, intente volver a conectarse a su máquina virtual. Si aún no puede conectarse, pruebe el siguiente paso.


## <a name="using-the-azure-portal"></a>Con el portal de Azure
El portal de Azure ofrece una manera rápida de restablecer las credenciales de usuario o configuración de SSH sin instalar las herramientas en el equipo local.

Seleccione la máquina virtual en el portal de Azure. Desplácese hacia abajo hasta la sección de **soporte técnico + solución de problemas** y seleccione **Restablecer la contraseña** en el siguiente ejemplo:

![Restablecer la configuración de SSH o credenciales en el portal de Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Restablecer la configuración de SSH
Como primer paso, seleccione `Reset SSH configuration only` en el menú desplegable de **modo** como se muestra en la captura de pantalla anterior, haga clic en el botón **Restablecer** . Una vez completada esta acción, intente acceder a su máquina virtual de nuevo.

### <a name="reset-ssh-credentials-for-a-user"></a>Restablecer credenciales SSH para un usuario
Para restablecer las credenciales de un usuario existente, seleccione `Reset SSH public key` o `Reset password` en el menú desplegable de **modo** como se muestra en la captura de pantalla anterior. Especificar el nombre de usuario y una tecla SSH o la nueva contraseña y luego haga clic en el botón **Restablecer** .

También puede crear un usuario con privilegios de sudo en la máquina virtual de este menú. Escriba un nuevo nombre de usuario y contraseña asociada o SSH clave y, a continuación, haga clic en el botón **Restablecer** .


## <a name="using-the-azure-cli"></a>Uso de la CLI de Azure
Si no lo ha hecho ya, [instale la CLI de Azure y conectarse a su suscripción de Azure](../xplat-cli-install.md). Asegúrese de que está utilizando el modo de administrador de recursos como sigue:

```
azure config mode arm
```

Si ha creado y carga una imagen de disco Linux personalizada, asegúrese de que el [Agente de Microsoft Azure Linux](virtual-machines-linux-agent-user-guide.md) versión 2.0.5 o posterior está instalado. Para máquinas virtuales creadas con la Galería de imágenes, esta extensión acceso ya instalada y configurada para usted.

### <a name="reset-ssh-configuration"></a>Restablecer la configuración de SSH
La configuración SSHD que esté mal configurada o el servicio ha encontrado un error. Puede restablecer SSHD para asegurarse de que la configuración de SSH propio es válida. Restablecer SSHD debe ser el primer paso de solución de problemas que realizar.

En el ejemplo siguiente se restablece SSHD en una máquina virtual denominada `myVM` en el grupo de recursos denominado `myResourceGroup`. Use sus propios nombres de grupo de recursos y VM como sigue:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Restablecer credenciales SSH para un usuario
Si aparece SSHD funcione correctamente, puede restablecer la contraseña de un usuario giver. En el ejemplo siguiente se restablece las credenciales de `myUsername` en el valor especificado en `myPassword`, en la máquina virtual denominada `myVM` en `myResourceGroup`. Utilice sus propios valores como sigue:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Si utiliza la autenticación de clave SSH, puede restablecer la clave SSH para un usuario determinado. En el ejemplo siguiente se actualiza la clave SSH almacenada en `~/.ssh/azure_id_rsa.pub` para el usuario denominado `myUsername`, en la máquina virtual denominada `myVM` en `myResourceGroup`. Utilice sus propios valores como sigue:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-file ~/.ssh/azure_id_rsa.pub
```


## <a name="using-the-vmaccess-extension"></a>Con la extensión VMAccess
La extensión de acceso a VM para Linux lee en un archivo de json que define acciones para llevar a cabo. Estas acciones incluyen restablecer SSHD, restablecer una clave SSH o agregar un usuario. Seguirá pudiendo usar el CLI de Azure llamar a la extensión VMAccess, pero puede reutilizar los archivos de json en varias VM si lo desea. Este enfoque le permite crear un repositorio de archivos de json que puede llamar para dada escenarios.

### <a name="reset-sshd"></a>Restablecer SSHD
Cree un archivo llamado `PrivateConf.json` con el contenido siguiente:

```bash
{  
    "reset_ssh":"True"
}
```

Con la CLI de Azure, a continuación, llame a la `VMAccessForLinux` extensión para restablecer su conexión SSHD especificando su archivo json. En el ejemplo siguiente se restablece SSHD en la máquina virtual denominada `myVM` en `myResourceGroup`. Utilice sus propios valores como sigue:

```bash
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Restablecer credenciales SSH para un usuario
Si aparece SSHD funcione correctamente, puede restablecer las credenciales de un usuario giver. Para restablecer la contraseña de un usuario, cree un archivo llamado `PrivateConf.json`. En el ejemplo siguiente se restablece las credenciales de `myUsername` en el valor especificado en `myPassword`. Escriba las siguientes líneas en su `PrivateConf.json` de archivo, con sus propios valores:

```bash
{
    "username":"myUsername", "password":"myPassword"
}
```

O para restablecer la clave SSH para un usuario, cree primero un archivo llamado `PrivateConf.json`. En el ejemplo siguiente se restablece las credenciales de `myUsername` en el valor especificado en `myPassword`, en la máquina virtual denominada `myVM` en `myResourceGroup`. Escriba las siguientes líneas en su `PrivateConf.json` de archivo, con sus propios valores:

```bash
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Después de crear el archivo de json, utilice CLI Azure para llamar a la `VMAccessForLinux` extensión para restablecer sus credenciales de usuario SSH especificando su archivo json. En el ejemplo siguiente se restablece credenciales en la máquina virtual denominada `myVM` en `myResourceGroup`. Utilice sus propios valores como sigue:

```
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```


## <a name="restart-a-vm"></a>Reiniciar una máquina virtual
Si tiene restablecer las credenciales de usuario y la configuración de SSH o ha encontrado un error al hacerlo, puede intentar reiniciar la máquina virtual de la dirección subyacente problemas de cálculo.

### <a name="azure-portal"></a>Portal de Azure
Para reiniciar una máquina virtual con el portal de Azure, seleccione la máquina virtual y haga clic en el ***reinicie** botón como en el ejemplo siguiente:

![Reiniciar una máquina virtual en el portal de Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>CLI de Azure
En el ejemplo siguiente se reinicia la máquina virtual denominada `myVM` en el grupo de recursos denominado `myResourceGroup`. Utilice sus propios valores como sigue:

```bash
azure vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Implementar una máquina virtual
Puede implementar una máquina virtual a otro nodo de Azure, que puede corregir los problemas de red subyacentes. Para obtener información sobre cómo implementar una máquina virtual, vea [implementar máquina virtual para el nuevo nodo de Azure](virtual-machines-windows-redeploy-to-new-node.md).

> [AZURE.NOTE] Cuando termine de esta operación, se perderán los datos de disco efímeros y direcciones IP dinámicas que están asociadas a la máquina virtual se actualizará.

### <a name="azure-portal"></a>Portal de Azure
Para redistribuir una máquina virtual con el portal de Azure, seleccione su VM y desplácese hacia abajo hasta la sección de **soporte técnico + solución de problemas** . Haga clic en el botón **Redistribuir** como en el ejemplo siguiente:

![Implementar una máquina virtual en el portal de Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>CLI de Azure
En el ejemplo siguiente se implementa de nuevo la máquina virtual denominada `myVM` en el grupo de recursos denominado `myResourceGroup`. Utilice sus propios valores como sigue:

```bash
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Máquinas virtuales creadas mediante el modelo de implementación de clásico

Siga estos pasos para resolver los errores más comunes de conexión SSH para máquinas virtuales que se crearon usando el modelo de implementación clásico. Después de cada paso, vuelva a conectar a la máquina virtual.

- Restablecer el acceso remoto desde el [portal de Azure](https://portal.azure.com). En el portal de Azure, seleccione la máquina virtual y haga clic en el botón **Restablecer remoto...** .

- Reinicie la máquina virtual. En el [portal de Azure](https://portal.azure.com), seleccione la máquina virtual y haga clic en el botón **reiniciar** .

    -O-

    En el [portal de clásica Azure](https://manage.windowsazure.com), seleccione **máquinas virtuales** > **instancias** > **reiniciar**.

- Volver a la máquina virtual a un nuevo nodo de Azure. Para obtener información sobre cómo implementar una máquina virtual, vea [implementar máquina virtual para el nuevo nodo de Azure](virtual-machines-windows-redeploy-to-new-node.md).

    Cuando termine de esta operación, se perderán los datos de disco efímeros y direcciones IP dinámicas que están asociadas a la máquina virtual se actualizará.

- Siga las instrucciones de [cómo restablecer una contraseña o SSH para máquinas virtuales basadas en Linux](virtual-machines-linux-classic-reset-access.md) :
    - Restablecer la contraseña o clave SSH.
    - Crear una cuenta de usuario de _sudo_ .
    - Restablecer la configuración de SSH.

- Comprobar el estado de los recursos de la máquina virtual los problemas de la plataforma.<br>
  Seleccione la VM y desplácese hacia abajo de la **configuración de** > **Comprobar estado**.


## <a name="additional-resources"></a>Recursos adicionales

- Si todavía no ha podido SSH a su máquina virtual después de seguir los pasos después, consulte [más pasos de solución de problemas](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md) para revisar los pasos adicionales para resolver el problema.

- Para obtener más información sobre cómo solucionar problemas de acceso de la aplicación, vea [solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual Azure](virtual-machines-linux-troubleshoot-app-connection.md)

- Para obtener más información acerca de cómo solucionar máquinas virtuales que se crearon usando el modelo de implementación clásica, vea [cómo restablecer una contraseña o SSH para máquinas virtuales basadas en Linux](virtual-machines-linux-classic-reset-access.md).
