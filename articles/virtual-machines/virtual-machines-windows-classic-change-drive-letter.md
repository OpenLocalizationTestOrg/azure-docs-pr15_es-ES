<properties
    pageTitle="Asegúrese de la unidad D: de una máquina virtual de un disco de datos | Microsoft Azure"
    description="Describe cómo cambiar las letras de unidad de una máquina virtual de Windows para que pueda utilizar la unidad D: como una unidad de datos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Usar la unidad D: como una unidad de datos en una máquina virtual de Windows 

Si la aplicación debe usar la unidad D para almacenar datos, siga estas instrucciones para usar una letra de unidad diferente para el disco temporal. Nunca utilice el disco temporal para almacenar datos que necesite para mantener.

Si cambia el tamaño o **Detener (Deallocate)** una máquina virtual, esto puede desencadenar una ubicación de la máquina virtual a un nuevo hipervisor. Un evento de mantenimiento planificado o también puede activar esta ubicación. En este escenario, el disco temporal se la asignará a la primera letra de unidad disponible. Si tiene una aplicación que requiere específicamente la unidad D:, debe seguir estos pasos para mover el archivo pagefile.sys temporalmente, adjuntar un nuevo disco de datos y asignar la letra D y a continuación, mueva el archivo pagefile.sys en la unidad temporal. Cuando haya finalizado, Azure no tendrán volver D: si se mueve la máquina virtual a un hipervisor diferente.

Para obtener más información acerca de cómo Azure usa el disco temporal, consulte [Descripción de la unidad temporal en Microsoft Azure máquinas virtuales de Windows](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="attach-the-data-disk"></a>Conecte el disco de datos

En primer lugar, deberá adjuntar el disco de datos a la máquina virtual. 

- Para usar el portal, consulte [cómo adjuntar un disco de datos en el portal de Azure](virtual-machines-windows-attach-disk-portal.md)
- Para usar el portal clásico, consulte [cómo adjuntar un disco de datos a una máquina virtual de Windows](virtual-machines-windows-classic-attach-disk.md). 


## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Mover temporalmente pagefile.sys en la unidad C

1. Conectarse a la máquina virtual. 

2. Haga clic en el menú **Inicio** y seleccione **sistema**.

3. En el menú de la izquierda, seleccione **Configuración avanzada del sistema**.

4. En la sección de **rendimiento** , seleccione **configuración**.

5. Seleccione la ficha **Opciones avanzadas** .

5. En la sección **memoria Virtual** , seleccione **cambiar**.

6. Seleccione la unidad **C** y, a continuación, haga clic en **Tamaño administrado por el sistema** y, a continuación, haga clic en **establecer**.

7. Seleccione la unidad **D** y, a continuación, haga clic en **sin archivo de paginación** y, a continuación, haga clic en **establecer**.

8. Haga clic en aplicar. Obtendrá una advertencia de que el equipo se debe reiniciar para que los cambios surtan efecto.

9. Reinicie el equipo virtual.




## <a name="change-the-drive-letters"></a>Cambiar las letras de unidad 

1. Una vez que se reinicia la máquina virtual, iniciar sesión en la máquina virtual.

2. Haga clic en el menú **Inicio** y escriba **diskmgmt.msc** y presione ENTRAR. Se iniciará la administración de disco.

3. Haga clic en **d.**, la unidad de almacenamiento temporal y seleccione **Cambiar letra y rutas de acceso**.

4. En letra de unidad, seleccione la unidad **G** y, a continuación, haga clic en **Aceptar**. 

5. Haga clic en el disco de datos y seleccione **cambiar la unidad de letra y rutas de acceso**.

6. En la letra de unidad, seleccione la unidad **D** y, a continuación, haga clic en **Aceptar**. 

7. Haga clic en **G**, la unidad de almacenamiento temporal y seleccione **Cambiar letra y rutas de acceso**.

8. En letra de unidad, seleccione la unidad **E** y, a continuación, haga clic en **Aceptar**. 

> [AZURE.NOTE] Si su máquina virtual tiene otros discos o unidades, use el mismo método para reasignar las letras de unidad de los otros discos y unidades. Desea que la configuración de disco sea:  
>- C: disco OS  
>- D: datos de un disco  
>- E: disco temporal



## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Retroceder pagefile.sys en la unidad de almacenamiento temporal 

1. Haga clic en el menú **Inicio** y seleccione **sistema**

2. En el menú de la izquierda, seleccione **Configuración avanzada del sistema**.

3. En la sección de **rendimiento** , seleccione **configuración**.

4. Seleccione la ficha **Opciones avanzadas** .

5. En la sección **memoria Virtual** , seleccione **cambiar**.

6. Seleccione la unidad de sistema operativo **C** y, a continuación, haga clic en **establecer** **ningún archivo de paginación** .

7. Seleccione la unidad de almacenamiento temporal **E** y, a continuación, haga clic en **Tamaño administrado por el sistema** y, a continuación, haga clic en **establecer**.

8. Haga clic en **Aplicar**. Obtendrá una advertencia de que el equipo se debe reiniciar para que los cambios surtan efecto.

9. Reinicie el equipo virtual.




## <a name="next-steps"></a>Pasos siguientes
- Puede aumentar el espacio de almacenamiento a su equipo virtual adjuntando [un disco de datos adicionales](virtual-machines-windows-attach-disk-portal.md).



