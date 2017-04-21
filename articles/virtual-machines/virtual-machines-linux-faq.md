<properties
    pageTitle="Preguntas más frecuentes sobre máquinas virtuales de Linux | Microsoft Azure"
    description="Proporciona respuestas a algunas de las preguntas comunes sobre máquinas virtuales de Linux creadas con el modelo de administrador de recursos."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Preguntas más frecuentes sobre Linux máquinas virtuales de Windows

Este artículo trata algunas preguntas comunes sobre máquinas virtuales de Linux creadas en Azure utiliza el modelo de implementación de administrador de recursos. En la versión de Windows de este tema, vea [preguntas más frecuentes sobre máquinas virtuales de Windows](virtual-machines-windows-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>¿Qué puedo ejecutar en una máquina virtual de Azure?

Todos los suscriptores pueden ejecutar software de servidor en una máquina virtual Azure. Para obtener más información, vea [Linux en distribuciones de Azure-Endorsed](virtual-machines-linux-endorsed-distros.md)


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>¿Cuánto almacenamiento puedo usar con una máquina virtual?

Cada disco de datos puede ser hasta 1 TB. El número de discos de datos que puede usar depende del tamaño de la máquina virtual. Para obtener más información, vea [tamaños para máquinas virtuales](virtual-machines-linux-sizes.md).

Una cuenta de almacenamiento de Azure proporciona almacenamiento para el disco de sistema operativo y los discos de datos. Cada disco es un archivo .vhd almacenado como un blob de página. Para detalles de los precios, consulte [Detalles de precios de almacenamiento](https://azure.microsoft.com/pricing/details/storage/).


## <a name="how-can-i-access-my-virtual-machine"></a>¿Cómo puedo acceder a mi máquina virtual?

Establecer una conexión remota para iniciar sesión en la máquina virtual, mediante Secure Shell (SSH). Vea las instrucciones sobre cómo conectar [de Windows](virtual-machines-linux-ssh-from-windows.md) o [de Linux y Mac](virtual-machines-linux-mac-create-ssh-keys.md). De forma predeterminada, SSH permite un máximo de 10 conexiones simultáneas. Puede aumentar este número al editar el archivo de configuración.


Si tiene problemas, consulte [solucionar problemas de Secure Shell (SSH) conexiones](virtual-machines-linux-troubleshoot-ssh-connection.md).


## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>¿Puedo usar el disco temporal (/ desarrollo/sdb1) para almacenar los datos?

No use el disco temporal (/ desarrollo/sdb1) para almacenar datos. Solo hay es para el almacenamiento temporal. Corre el riesgo de perder los datos que no se pueden recuperar.


## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>¿Puedo copiar o clonar una máquina virtual de Azure existente?

Sí. Para obtener instrucciones, consulte [cómo crear una copia de una máquina virtual de Linux en el modelo de implementación de administrador de recursos](virtual-machines-linux-copy-vm.md).


## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>¿Por qué no puedo ver Canadá Central y las regiones Canadá oriental mediante el Administrador de recursos de Azure?

Las dos nuevas áreas de Canadá Central y Canadá oriental no se registran automáticamente para la creación de la máquina virtual para suscripciones de Azure existentes. Este registro se realiza automáticamente cuando se implementa una máquina virtual a través del portal de Azure para cualquier otra región con el Administrador de recursos de Azure. Después de implementar una máquina virtual a cualquier región de Azure, nuevas áreas deben estar disponibles para las siguientes máquinas virtuales de Windows.


## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>¿Puedo agregar una NIC a mi VM después de crearlo?

No. Agregar una NIC solo puede realizarse en el momento de creación.


## <a name="are-there-any-computer-name-requirements"></a>¿Hay los requisitos de nombre de equipo?

Sí. El nombre del equipo puede tener un máximo de 64 caracteres de longitud. Consulte [las directrices de nomenclatura de infraestructura](virtual-machines-linux-infrastructure-naming-guidelines.md) para más información sobre nombres de los recursos.


## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>¿Cuáles son los requisitos de nombre de usuario cuando se crea una máquina virtual?

Nombres de usuario deben ser 1-64 caracteres de longitud.

No se permiten los siguientes nombres de usuario:

<table>
    <tr>
        <td style="text-align:center">Administrador </td><td style="text-align:center"> Administrador </td><td style="text-align:center"> usuario </td><td style="text-align:center"> Usuario1</td>
    </tr>
    <tr>
        <td style="text-align:center">prueba </td><td style="text-align:center"> usuario2 </td><td style="text-align:center"> Prueba1 </td><td style="text-align:center"> usuario3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> un</td>
    </tr>
    <tr>
        <td style="text-align:center">ACTUser  </td><td style="text-align:center"> ADM </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> ASPNET</td>
    </tr>
    <tr>
        <td style="text-align:center">copia de seguridad </td><td style="text-align:center"> consola </td><td style="text-align:center"> David </td><td style="text-align:center"> invitado</td>
    </tr>
    <tr>
        <td style="text-align:center">John </td><td style="text-align:center"> propietario </td><td style="text-align:center"> raíz </td><td style="text-align:center"> servidor</td>
    </tr>
    <tr>
        <td style="text-align:center">SQL </td><td style="text-align:center"> soporte técnico </td><td style="text-align:center"> Support_388945a0 </td><td style="text-align:center"> sistema</td>
    </tr>
    <tr>
        <td style="text-align:center">Test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> User4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>¿Cuáles son los requisitos de contraseña al crear una máquina virtual?

Las contraseñas deben tener 72 6 caracteres de longitud y reunirse 3 de los requisitos de 4 complejidad:

- Tiene caracteres inferiores
- Tiene caracteres superiores
- Tener un dígito
- Tiene un carácter especial (Regex coincidir [\W_])

No se permiten las siguientes contraseñas:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Word de PA$</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">¡Contraseña!</td>
        <td style="text-align:center">Contraseña1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">¡ILOVEYOU!</td>
    </tr>
</table>
