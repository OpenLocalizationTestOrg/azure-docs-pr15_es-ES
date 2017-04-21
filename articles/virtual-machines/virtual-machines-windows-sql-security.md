<properties
    pageTitle="Consideraciones de seguridad de SQL Server en Azure | Microsoft Azure"
    description="En este tema se refiere a recursos creados con el modelo de implementación clásica y proporciona instrucciones generales sobre la seguridad de SQL Server se ejecuta en una máquina Virtual de Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
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
    ms.date="06/24/2016"
    ms.author="jroth" />

# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Consideraciones de seguridad de SQL Server en máquinas virtuales de Windows Azure
 
Este tema incluye directrices generales de seguridad que ayudan a establecer acceso seguro a instancias de SQL Server en una máquina virtual de Azure. Sin embargo, para garantizar la mejor protección a las instancias de la base de datos de SQL Server en Azure, se recomienda implementar las prácticas de seguridad local tradicional además de las mejores prácticas de seguridad para Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Para obtener más información sobre las prácticas de seguridad de SQL Server, consulte [SQL Server 2008 R2 prácticas recomendadas de seguridad - operativas y las tareas administrativas](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

Azure cumple con varias normas de la industria y estándares que le permite crear una solución compatible con SQL Server que se ejecuta en una máquina Virtual. Para obtener información sobre el cumplimiento de reglamentaciones con Azure, consulte [Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/).

A continuación se muestra una lista de recomendaciones de seguridad que debe tener en cuenta al configurar y conectarse a la instancia de SQL Server en una máquina virtual de Azure.

## <a name="considerations-for-managing-accounts"></a>Consideraciones de administración de cuentas:

- Crear una cuenta de administrador local único que no se denomina **Administrador**.

- Use complejas contraseñas seguras para todas las cuentas. Para obtener más información sobre cómo crear una contraseña segura, consulte el artículo de [sugerencias para crear un contraseñas seguras](http://windows.microsoft.com/en-us/windows-vista/Tips-for-creating-a-strong-password) .

- De forma predeterminada, Azure selecciona autenticación de Windows durante la instalación de la máquina Virtual de SQL Server. Por lo tanto, el inicio de sesión de **SA** está deshabilitada y se ha asignado una contraseña por el programa de instalación. Le recomendamos que debe ser el inicio de sesión **SA** no se usan o habilitado. Los siguientes son estrategias alternativas si se desea un inicio de sesión de SQL:
    - Crear una cuenta SQL que sea miembro de sysadmin.
    - Si debe usar un inicio de sesión de **SA** , habilitar el inicio de sesión y cambiarle el nombre y asignar una contraseña nueva.
    - Ambas opciones mencionadas anteriormente requieren un cambio el modo de autenticación para **SQL Server y el modo de autenticación de Windows**. Para obtener más información, vea [Cambiar el modo de autenticación de servidor](https://msdn.microsoft.com/library/ms188670.aspx).

## <a name="considerations-for-securing-connections-to-azure-virtual-machine"></a>Consideraciones para proteger las conexiones de Azure Máquina Virtual:

- Considere la posibilidad de utilizar [Una red Virtual Azure](../virtual-network/virtual-networks-overview.md) para administrar las máquinas virtuales en lugar de públicos puertos RDP.

- Utilizar un [Grupo de seguridad de la red](../virtual-network/virtual-networks-nsg.md) (GSN) para permitir o denegar el tráfico de red de la máquina virtual. Si desea usar un GSN y tiene un extremo ACL ya en su lugar, quite primero el extremo ACL. Para obtener información acerca de cómo hacerlo, vea [Administrar Control listas de acceso (ACL) para extremos con PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

- Si está utilizando extremos, quite los extremos de la máquina virtual si no se utilizan. Para obtener instrucciones sobre cómo usar las ACL con extremos, consulte [administrar las ACL de un extremo](../virtual-network/virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).

- Habilitar una opción de conexión cifrada de una instancia de motor de base de datos de SQL Server en Azure máquinas virtuales de Windows. Configurar la instancia de SQL server con un certificado de firma. Para obtener más información, vea [Habilitar conexiones cifradas en el motor de base de datos](https://msdn.microsoft.com/library/ms191192.aspx) y la [Sintaxis de la cadena de conexión](https://msdn.microsoft.com/library/ms254500.aspx).

- Si debe tener acceso a sus máquinas virtuales únicamente de una red específica, use Firewall de Windows para restringir el acceso a ciertas direcciones IP o subredes.

## <a name="next-steps"></a>Pasos siguientes

Si también está interesado en las prácticas recomendadas de rendimiento, consulte [Prácticas recomendadas de rendimiento para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-performance.md).

Para otros temas relacionados con la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en máquinas virtuales de Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
