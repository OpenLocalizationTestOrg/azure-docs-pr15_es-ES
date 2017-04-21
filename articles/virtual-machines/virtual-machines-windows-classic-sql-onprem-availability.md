<properties
    pageTitle="Extender local siempre en grupos de disponibilidad en Azure | Microsoft Azure"
    description="Este tutorial usa recursos creados con el modelo de implementación clásica y describe cómo utilizar al asistente Agregar réplica en SQL Server Management Studio (SSMS) para agregar una réplica siempre en el grupo de disponibilidad en Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Extender local siempre en grupos de disponibilidad en Azure

Siempre en grupos de disponibilidad proporcionan alta disponibilidad para grupos de base de datos agregando réplicas secundarias. Permiten estas réplicas errores en bases de datos en caso de un error. Además puede utilizarse para descargar las cargas de trabajo de lecturas o tareas de copia de seguridad.

Puede extender grupos de disponibilidad local a Microsoft Azure aprovisionamiento uno o más máquinas virtuales de Azure con SQL Server y, a continuación, agregarlas como réplicas a los grupos de disponibilidad local.

En este tutorial, se supone que tiene los siguientes:

- Una suscripción activa de Azure. Puede [suscribirse a una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Un grupo existente de siempre en disponibilidad local. Para obtener más información sobre grupos de disponibilidad, consulte [Siempre en grupos de disponibilidad](https://msdn.microsoft.com/library/hh510230.aspx).

- Conectividad entre la red local y la red virtual Azure. Para obtener más información sobre la creación de esta red virtual, vea [Configurar una VPN de sitio a sitio en el portal de clásico de Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="add-azure-replica-wizard"></a>Agregar Asistente para crear réplicas de Azure

Esta sección muestra cómo usar el **Asistente para agregar réplica de Azure** para ampliar la solución siempre en el grupo de disponibilidad para incluir réplicas Azure.

1. Desde dentro de SQL Server Management Studio, expanda **Siempre en alta disponibilidad** > **Grupos de disponibilidad** > **[nombre del grupo disponibilidad]**.

1. Haga clic en **Disponibilidad réplicas**y luego haga clic en **Agregar réplica**.

1. De forma predeterminada, se muestra la **Réplica agregar Asistente para grupo de disponibilidad** . Haga clic en **siguiente**.  Si ha seleccionado la **no mostrar esta página nuevo** opción en la parte inferior de la página durante un inicio anterior de este asistente, no se mostrará esta pantalla.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)

1. Deberá conectarse a todas las réplicas secundarias existentes. Puede hacer clic en **Conectar...** junto a cada réplica o hacer clic en **Conectar todos...** en la parte inferior de la pantalla. Después de la autenticación, haga clic en **siguiente** para pasar a la siguiente pantalla.

1. En la página **Especificar réplicas** , varias fichas se muestran en la parte superior: **réplicas**, **extremos**, **Preferencias de copia de seguridad**y **escucha**. Desde la ficha **réplicas** , haga clic en **Agregar réplica de Azure...** Para iniciar al Asistente para agregar réplica de Azure.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)

1. Si ha instalado uno antes de, seleccione un certificado de administración de Azure existente desde el almacén de certificados local de Windows. Seleccione o escriba el identificador de una suscripción de Azure si ha utilizado anterior. Puede hacer clic en descargar para descargar e instalar un certificado de administración de Azure y descargar la lista de suscripciones con una cuenta de Azure.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)

1. Llenará cada campo en la página con los valores que se usan para crear la máquina Virtual (VM de Azure) que va a hospedar la réplica.

  	|Configuración|Descripción|
|---|---|
|**Imagen**|Seleccione la combinación de sistema operativo y SQL Server deseada|
|**Tamaño de memoria virtual**|Seleccione el tamaño de la máquina virtual que mejor se adapte a sus necesidades empresariales|
|**Nombre de VM**|Especifique un nombre único para la nueva máquina virtual. El nombre debe contener entre 3 y 15 caracteres, puede contienen solo letras, números y guiones y debe comenzar con una letra y termine con una letra o un número.|
|**Nombre de usuario VM**|Especifique un nombre de usuario que se convertirá en la cuenta de administrador en la máquina virtual|
|**Contraseña de administrador VM**|Especificar una contraseña para la nueva cuenta|
|**Confirmar contraseña**|Confirme la contraseña de la nueva cuenta|
|**Red virtual**|Especifique la red virtual Azure que debe usar la nueva máquina virtual. Para obtener más información sobre redes virtuales, vea [Introducción a la red Virtual](../virtual-network/virtual-networks-overview.md).|
|**Subred de red virtual**|Especifique la subred de una red virtual que debe usar la nueva máquina virtual|
|**Dominio**|Confirme que el valor rellenarán del dominio es correcto|
|**Nombre de usuario de dominio**|Especifique una cuenta que está en el grupo de administradores locales en los nodos de clúster local|
|**Contraseña**|Especifique la contraseña para el nombre de usuario de dominio|

1. Haga clic en **Aceptar** para validar la configuración de implementación.

1. Condiciones legales se muestran a continuación. Lea y haga clic en **Aceptar** si acepta estos términos.

1. Aparecerá la página **Especificar réplicas** de nuevo. Compruebe la configuración de la nueva réplica de Azure en las pestañas **réplicas**, **extremos**y **Preferencias de copia de seguridad** . Modificar la configuración para satisfacer sus necesidades empresariales.  Para obtener más información sobre los parámetros contenidos en estas fichas, vea [Especificar una página de réplicas (disponibilidad asistente o agregar réplica Asistente para nuevo grupo)](https://msdn.microsoft.com/library/hh213088.aspx). Tenga en cuenta que no se puede crear escucha con la ficha escucha para grupos de disponibilidad que contienen réplicas Azure. Además, si ya ha creado una escucha antes de iniciar al asistente, recibirá un mensaje que indica que no se admite en Azure. Veamos cómo crear agentes de escucha en la sección **crear un detector de grupo de disponibilidad** .

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)

1. Haga clic en **siguiente**.

1. Seleccione el método de sincronización de datos que desea usar en la página **Seleccionar la sincronización inicial de datos** y haga clic en **siguiente**. Para la mayoría de los escenarios, seleccione **Sincronización completa de datos**. Para obtener más información sobre los métodos de sincronización de datos, consulte la [Página Seleccionar inicial de sincronización de datos (siempre en disponibilidad grupo Asistentes)](https://msdn.microsoft.com/library/hh231021.aspx).

1. Revisar los resultados en la página **validación** . Corregir problemas pendientes y vuelva a ejecutar la validación si es necesario. Haga clic en **siguiente**.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)

1. Revise la configuración en la página de **Resumen** y luego haga clic en **Finalizar**.

1. Inicia el proceso de aprovisionamiento. Cuando el asistente finalice correctamente, haga clic en **Cerrar** para salir del asistente.

>[AZURE.NOTE] El Asistente para agregar réplica de Azure, se crea un archivo de registro en Users\nombre Name\AppData\Local\SQL Server\AddReplicaWizard. Este archivo de registro puede usarse para solucionar el error réplica Azure implementaciones. Si el asistente no ejecutar ninguna acción, todas las operaciones anteriores se deshacen, incluida la eliminación de la máquina virtual de preparación.

## <a name="create-an-availability-group-listener"></a>Crear una escucha de grupo de disponibilidad

Después de crear el grupo de disponibilidad, debe crear una escucha para los clientes para conectarse a las réplicas. Escucha dirige conexiones entrantes a principal o una réplica secundaria de solo lectura. Para obtener más información sobre los agentes de escucha, consulte [configurar un detector de ILB para siempre en disponibilidad de los grupos de Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

## <a name="next-steps"></a>Pasos siguientes

Además de usar el **Asistente para agregar réplica de Azure** para extender su siempre en grupo disponibilidad en Azure, también puede mover algunas cargas de trabajo de SQL Server completamente en Azure. Para obtener más información, vea [el aprovisionamiento de un equipo Virtual con SQL Server en Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para otros temas relacionados con la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-server-iaas-overview.md).
