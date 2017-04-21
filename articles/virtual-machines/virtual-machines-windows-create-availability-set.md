<properties
    pageTitle="Crear un conjunto de disponibilidad VM | Microsoft Azure"
    description="Obtenga información sobre cómo crear una disponibilidad para sus máquinas virtuales con Azure portal o PowerShell utilizando el modelo de implementación de administrador de recursos."
    keywords="conjunto de disponibilidad"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>


# <a name="create-an-availability-set"></a>Crear un conjunto de disponibilidad 

Cuando se establece la con el portal, si desea que su máquina virtual forme parte de una disponibilidad, debe crear la disponibilidad que establecer primero.

Para obtener más información sobre la creación y uso de conjuntos de disponibilidad, consulte [Administrar la disponibilidad de máquinas virtuales de Windows](virtual-machines-windows-manage-availability.md).


## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>Usar el portal para crear una conjunto antes de crear sus máquinas virtuales de disponibilidad

1. En el menú de concentrador, haga clic en **Examinar** y seleccione **conjuntos de disponibilidad**.

2. En el **módulo de conjuntos de disponibilidad**, haga clic en **Agregar**.

    ![Captura de pantalla que muestra el botón Agregar para crear la disponibilidad de un nueva conjunto.](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)

3. En el módulo **establece crear disponibilidad** , complete la información de su conjunto.

    ![Captura de pantalla que muestra la información que necesitar para crear el conjunto de disponibilidad.](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)

    - **Nombre** : el nombre debe ser caracteres 1-80 formados por números, letras, períodos, caracteres de subrayado y guiones. El primer carácter debe ser una letra o un número. El último carácter debe ser una letra, número o subrayado.
    - **Dominios de error** , dominios de error definen el grupo de máquinas virtuales que comparten un modificador de origen y de red power comunes. De forma predeterminada, las máquinas virtuales están separadas por hasta tres dominios de error y se pueden cambiar entre 1 y 3.
    - **Actualizar dominios** - actualización cinco dominios se asignan de forma predeterminada y se puede establecer en entre 1 y 20. Actualizar los dominios indican grupos de máquinas virtuales y hardware físico subyacente que puede reiniciar al mismo tiempo. Por ejemplo, si se especifica cinco actualizar dominios, cuando hay más de cinco máquinas virtuales configuradas dentro de un conjunto único de disponibilidad, la sexta máquina virtual se colocarán en el mismo dominio de actualización que la primera máquina virtual, el séptimo en la misma Ud. como la segunda máquina virtual y así sucesivamente. El orden de la reiniciar no sean secuencial, pero se trata de actualizar solo un dominio a la vez.
    - **Suscripción** : seleccione la suscripción para usar si tiene más de uno.
    - **Grupo de recursos** : seleccione un grupo de recursos existente haciendo clic en la flecha y seleccione un grupo de recursos de la lista hacia abajo. También puede crear un nuevo grupo de recursos, escriba un nombre. El nombre puede contener cualquiera de los siguientes caracteres: letras, números, puntos, guiones, caracteres de subrayado y apertura o paréntesis de cierre. El nombre no puede terminar en punto. Todas las máquinas virtuales en el grupo de disponibilidad es necesario crear en el mismo grupo de recursos que el conjunto de disponibilidad.
    - **Ubicación** : seleccione una ubicación en la lista desplegable.

4. Cuando termine de escribir la información, haga clic en **crear**. Una vez creado el grupo de disponibilidad, puede ver en la lista después de actualizar el portal.

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>Usar el portal para crear una máquina virtual y una disponibilidad establecer al mismo tiempo

Si va a crear una nueva máquina virtual con el portal, también puede crear una nueva disponibilidad establecer para la máquina virtual mientras se crea la primera VM en el conjunto.

![Captura de pantalla que muestra el proceso para crear una nueva disponibilidad establecer mientras crea la máquina virtual.](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)


## <a name="add-a-new-vm-to-an-existing-availability-set"></a>Agregar una nueva máquina virtual a un conjunto existente de disponibilidad

Para cada VM adicional que cree que debe pertenecer en el conjunto, asegúrese de que cree en el mismo **grupo de recursos** y, a continuación, seleccione la disponibilidad existente establecida en el paso 3. 

![Captura de pantalla que muestra cómo seleccionar una disponibilidad existente establecer para su máquina virtual.](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)



## <a name="use-powershell-to-create-an-availability-set"></a>Usar PowerShell para crear un conjunto de disponibilidad

En este ejemplo se crea una disponibilidad establecer en el grupo de recursos de **RMResGroup** en la ubicación de **Estados Unidos oeste** . Esto debe hacerse antes de crear la primera VM que se incluirá en el conjunto.

    New-AzureRmAvailabilitySet -ResourceGroupName "RMResGroup" -Name "AvailabilitySet03" -Location "West US"
    
Para obtener más información, consulte [AzureRmAvailabilitySet de nuevo](https://msdn.microsoft.com/library/mt619453.aspx).


## <a name="troubleshooting"></a>Solución de problemas

- Cuando se crea una máquina virtual, si no es el conjunto de disponibilidad que desee en la lista desplegable en el portal puede que lo haya creado en otro grupo de recursos. Si no conoce el grupo de recursos para su disponibilidad establece, vaya al menú de concentrador y haga clic en Examinar > disponibilidad establece para ver una lista de los conjuntos de disponibilidad y grupos de recursos que pertenecen.


## <a name="next-steps"></a>Pasos siguientes

Agregar almacenamiento adicional para su máquina virtual agregando un [disco de datos](virtual-machines-windows-attach-disk-portal.md).
