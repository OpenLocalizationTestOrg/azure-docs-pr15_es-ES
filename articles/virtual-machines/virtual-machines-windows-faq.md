<properties
    pageTitle="Preguntas más frecuentes sobre máquinas virtuales de Windows | Microsoft Azure"
    description="Proporciona respuestas a algunas de las preguntas comunes sobre máquinas virtuales de Windows creadas con el modelo de administrador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Preguntas más frecuentes sobre máquinas virtuales de Windows 


Este artículo trata algunas preguntas comunes sobre máquinas virtuales de Windows creadas en Azure utiliza el modelo de implementación de administrador de recursos. En la versión de Linux de este tema, vea [preguntas más frecuentes sobre Linux máquinas virtuales de Windows](virtual-machines-linux-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>¿Qué puedo ejecutar en una máquina virtual de Azure?

Todos los suscriptores pueden ejecutar software de servidor en una máquina virtual Azure. Para obtener información sobre la política de soporte técnico para ejecutar el software de servidor de Microsoft en Azure, consulte el [software de servidor de Microsoft que admiten para Azure máquinas virtuales de Windows](https://support.microsoft.com/kb/2721672)

Algunas versiones de Windows 7 y Windows 8.1 están disponibles para los suscriptores de beneficio de Azure de MSDN y suscriptores de desarrollo de MSDN y probar de pago por uso para las tareas de desarrollo y pruebas. Para obtener información detallada, incluidas las instrucciones y limitaciones, vea [imágenes de cliente de Windows para los suscriptores de MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>¿Cuánto almacenamiento puedo usar con una máquina virtual?

Cada disco de datos puede ser hasta 1 TB. El número de discos de datos que puede usar depende del tamaño de la máquina virtual. Para obtener más información, vea [tamaños para máquinas virtuales](virtual-machines-windows-sizes.md).

Una cuenta de almacenamiento de Azure proporciona almacenamiento para el disco de sistema operativo y los discos de datos. Cada disco es un archivo .vhd almacenado como un blob de página. Para detalles de los precios, consulte [Detalles de precios de almacenamiento](https://azure.microsoft.com/pricing/details/storage/).


## <a name="how-can-i-access-my-virtual-machine"></a>¿Cómo puedo acceder a mi máquina virtual?

Establecer una conexión remota con conexión a Escritorio remoto (RDP) para una máquina virtual de Windows. Para obtener instrucciones, consulte [cómo conectar e iniciar sesión en una máquina virtual Azure con Windows](virtual-machines-windows-connect-logon.md). Se admite un máximo de dos conexiones simultáneas, a menos que el servidor está configurado como un host de sesión de servicios de escritorio remoto.  


Si tiene problemas con Escritorio remoto, vea [solucionar problemas de escritorio remoto conexiones a un equipo de Virtual de basado en Windows Azure](virtual-machines-windows-troubleshoot-rdp-connection.md). 

Si ya está familiarizado con Hyper-V, puede que esté buscando una herramienta similar a VMConnect. Azure no ofrece una herramienta similar porque no se admite el acceso de consola a una máquina virtual.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>¿Puedo usar el disco temporal (la unidad D: de forma predeterminada) para almacenar los datos?

No use el disco temporal para almacenar los datos. Solo es almacenamiento temporal, por lo que debería evitar el riesgo de perder los datos que no se pueden recuperar. Pérdida de datos puede ocurrir cuando la máquina virtual se mueve a un host diferente. Cambiar el tamaño de una máquina virtual, actualizando el host o un error de hardware en el host es algunas de las razones que puede mover una máquina virtual.

Si tiene una aplicación que necesita usar la letra D:, puede asignar las letras de unidad para que el disco temporal utiliza un número distinto de D:. Para obtener instrucciones, vea [cambiar la letra del disco temporal de Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>¿Cómo puedo cambiar la letra del disco temporal?

Puede cambiar la letra moviendo el archivo de página y reasignar letras de unidad, pero debe asegurarse de que siga los pasos en un orden específico. Para obtener instrucciones, vea [cambiar la letra del disco temporal de Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>¿Puedo agregar una máquina virtual existente a un conjunto de disponibilidad?

No. Si desea que su máquina virtual forme parte de un conjunto de disponibilidad, debe crear la máquina virtual dentro del conjunto. Actualmente no hay una manera de agregar una máquina virtual a una disponibilidad establecer después de que se ha creado.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>¿Puedo cargar una máquina virtual en Azure?

Sí. Para obtener instrucciones, vea [cargar una imagen de máquina virtual de Windows Azure](virtual-machines-windows-upload-image.md)

## <a name="can-i-resize-the-os-disk"></a>¿Puedo cambiar el tamaño de disco de sistema operativo?

Sí. Para obtener instrucciones, consulte [cómo expandir la unidad de sistema operativo de una máquina Virtual en un grupo de recursos de Azure](virtual-machines-windows-expand-os-disk.md).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>¿Puedo copiar o clonar una máquina virtual de Azure existente?

Sí. Para obtener instrucciones, consulte [cómo crear una copia de una máquina virtual de Windows en el modelo de implementación de administrador de recursos](virtual-machines-windows-vhd-copy.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>¿Por qué no puedo ver Canadá Central y las regiones Canadá oriental mediante el Administrador de recursos de Azure?

Las dos nuevas áreas de Canadá Central y Canadá oriental no se registran automáticamente para la creación de la máquina virtual para suscripciones de Azure existentes. Este registro se realiza automáticamente cuando se implementa una máquina virtual a través del portal de Azure para cualquier otra región con el Administrador de recursos de Azure. Después de implementar una máquina virtual a cualquier región de Azure, nuevas áreas deben estar disponibles para las siguientes máquinas virtuales de Windows.

## <a name="does-azure-support-linux-vms"></a>¿Admite Azure máquinas virtuales de Linux?

Sí. Para crear rápidamente una VM Linux pruebe, consulte [crear una máquina virtual de Linux en Azure con el Portal](virtual-machines-linux-quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>¿Puedo agregar una NIC a mi VM después de crearlo?

No. Agregar una NIC solo puede realizarse en el momento de creación.

## <a name="are-there-any-computer-name-requirements"></a>¿Hay los requisitos de nombre de equipo?

Sí. El nombre del equipo puede tener un máximo de 15 caracteres de longitud. Consulte [las directrices de nomenclatura de infraestructura](virtual-machines-windows-infrastructure-naming-guidelines.md) para más información sobre nombres de los recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>¿Cuáles son los requisitos de nombre de usuario cuando se crea una máquina virtual?

Nombres de usuario pueden tener un máximo de 20 caracteres de longitud y no puede terminar en punto ("."). 

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

Las contraseñas deben tener 123 8 caracteres de longitud y reunirse 3 de los requisitos de 4 complejidad:

- Tiene caracteres inferiores
- Tiene caracteres superiores
- Tener un dígito
- Tiene un carácter especial (Regex coincidir [\W_])

No se permiten las siguientes contraseñas:

<table>
    <tr>
        <td style="text-align:center">abc@123</td><td style="text-align:center">P@$$w0rd</td><td style="text-align:center">P@ssw0rd</td><td style="text-align:center">P@ssword123</td><td style="text-align:center">Word de PA$</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td><td style="text-align:center">¡Contraseña!</td><td style="text-align:center">Contraseña1</td><td style="text-align:center">Password22</td><td style="text-align:center">¡ILOVEYOU!</td>
    </tr>
</table>
