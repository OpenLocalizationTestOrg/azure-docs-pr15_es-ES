<properties
    pageTitle="Configurar la integración de Azure depósito clave para SQL Server en máquinas virtuales de Azure (jefe de recursos)"
    description="Aprenda a automatizar la configuración de cifrado de SQL Server para su uso con depósito de clave de Azure. En este tema se explica cómo usar Azure clave depósito integración con máquinas virtuales de SQL Server creadas con el Administrador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Configurar la integración de Azure depósito clave para SQL Server en máquinas virtuales de Azure (jefe de recursos)

> [AZURE.SELECTOR]
- [Administrador de recursos](virtual-machines-windows-ps-sql-keyvault.md)
- [Clásico](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Información general
Existen varias características de cifrado de SQL Server, como el [cifrado de datos transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), el [cifrado de nivel de columna (borrar)](https://msdn.microsoft.com/library/ms173744.aspx)y [cifrado de copia de seguridad](https://msdn.microsoft.com/library/dn449489.aspx). Estas formas de cifrado requieren administrar y almacenar las claves de cifrado que se usan para el cifrado. El servicio de depósito de clave de Azure (AKV) está diseñado para mejorar la seguridad y la administración de estas teclas en una ubicación segura y altamente disponible. El [Conector de SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permite a SQL Server usar estas teclas de la cámara de clave de Azure.

Si hay que ejecuta SQL Server con local equipos son los [pasos que puede seguir para tener acceso a Azure clave depósito desde el equipo de SQL Server local](https://msdn.microsoft.com/library/dn198405.aspx). Pero, para SQL Server en máquinas virtuales de Azure, puede ahorrar tiempo utilizando la característica de *Integración de depósito de clave de Azure* .

Cuando esta característica está habilitada, automáticamente el conector de SQL Server se instala, configura el proveedor EKM para tener acceso a Azure clave depósito y crea la credencial para que pueda tener acceso a su cámara. Si busca en los pasos de la documentación mencionada anteriormente en local, puede ver que esta característica automatiza los pasos 2 y 3. Lo único que tendría que hacer manualmente es crear las teclas y depósito clave. Desde allí, toda la configuración de la VM SQL es automático. Una vez completada la instalación, esta característica puede ejecutar instrucciones SQL T para empezar a cifrar sus bases de datos o realizar copias de seguridad como lo haría normalmente.

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>Habilitar y configurar la integración de AKV
Puede habilitar la integración de AKV durante el aprovisionamiento o configurar para máquinas virtuales existentes.

### <a name="new-vms"></a>Nuevas máquinas virtuales
Si el aprovisionamiento de una nueva máquina virtual de SQL Server con el Administrador de recursos, el portal de Azure ofrece un paso para habilitar la integración de Azure clave depósito. La característica de Azure clave depósito sólo está disponible para la empresa, programador y evaluación de las ediciones de SQL Server.

![Integración de SQL Azure depósito clave](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Para obtener un tutorial detallado de aprovisionamiento, vea [aprovisionar una máquina virtual de SQL Server en el Portal de Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Máquinas virtuales existentes
Existentes máquinas virtuales de SQL Server, seleccione la máquina virtual de SQL Server. A continuación, seleccione la sección **configuración de SQL Server** del módulo de **configuración** .

![Integración de SQL AKV para máquinas virtuales existentes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

En el módulo de **configuración de SQL Server** , haga clic en el botón **Editar** en la sección de integración automatizada depósito de clave.

![Configurar la integración de AKV SQL para máquinas virtuales existentes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

Cuando haya terminado, haga clic en el botón **Aceptar** en la parte inferior de la hoja de **configuración de SQL Server** para guardar los cambios.

>[AZURE.NOTE] También puede configurar la integración de AKV usando una plantilla. Para obtener más información, vea [Tutorial rápido Azure plantilla para la integración de Azure clave depósito](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]
