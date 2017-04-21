<properties
    pageTitle="Crear una VM Linux con el Portal de Azure | Microsoft Azure"
    description="Crear una VM Linux con el Portal de Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Crear una VM Linux en Azure con el Portal


Este artículo le muestra cómo usar el [portal de Azure](https://portal.azure.com/) para crear una máquina Virtual Linux.

Los requisitos son:

- [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/)

- [Archivos de clave de SSH públicos y privados](virtual-machines-linux-mac-create-ssh-keys.md)


1. Ha iniciado sesión en el portal de Azure con la identidad de la cuenta de Azure, haga clic en **+ nuevo** en la esquina superior izquierda:

    ![Screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. Haga clic en **máquinas virtuales** en el **catálogo de soluciones de** lista de imágenes de **Ubuntu Server 14.04 LTADOS** desde **Aplicaciones destacadas** .  Compruebe en la parte inferior que el modelo de implementación es `Resource Manager` y, a continuación, haga clic en **crear**.

    ![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. En la página de **conceptos básicos** , escriba:
    - un nombre para la máquina virtual
    - un nombre de usuario para el usuario Admin
    - establecer el tipo de autenticación para **clave pública de SSH**
    - la clave pública SSH como una cadena (desde su `~/.ssh/` directory)
    - un recurso de nombre de grupo o seleccione un grupo existente

    y haga clic en **Aceptar** para continuar y elija el tamaño de la memoria virtual; debe tener un aspecto similar al siguiente:

    ![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

4. Elija el tamaño de **DS1** , que se instala Ubuntu en un SSD Premium y haga clic en **Seleccionar** para configurar parámetros.

    ![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

5. En **configuración**, deje los valores predeterminados para los valores de almacenamiento y de red y haga clic en **Aceptar** para ver el resumen.  Observe el tipo de disco se ha establecido en Premium SSD eligiendo DS1, la **S** marca SSD.

    ![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

6. Confirmar la configuración de su nueva VM Ubuntu y haga clic en **Aceptar**.

    ![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

7. Abrir el panel del Portal y en **interfaces de red** , elija su NIC

    ![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

8. Abrir el menú de direcciones IP pública en la configuración de NIC

    ![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

9. SSH en la dirección IP pública con la clave pública de SSH

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado una VM Linux rápidamente a usar para realizar pruebas o demostración. Para crear una VM Linux personalizado para su infraestructura, puede seguir uno de estos artículos.

- [Crear una VM Linux en Azure con plantillas](virtual-machines-linux-cli-deploy-templates.md)
- [Crear un SSH protegidos Linux VM en Azure con plantillas](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Crear una VM Linux mediante la CLI de Azure](virtual-machines-linux-create-cli-complete.md)
