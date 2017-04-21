<properties
    pageTitle="Configurar siempre en el grupo de disponibilidad de Azure VM automáticamente - Administrador de recursos"
    description="Crear un grupo de disponibilidad siempre con Azure máquinas virtuales en modo de administrador de recursos de Azure. Este tutorial utiliza principalmente la interfaz de usuario para crear automáticamente toda la solución."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-always-on-availability-group-in-azure-vm-automatically---resource-manager"></a>Configurar siempre en el grupo de disponibilidad de Azure VM automáticamente - Administrador de recursos

> [AZURE.SELECTOR]
- [Administrador de recursos: plantilla](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Administrador de recursos: Manual](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Clásica: interfaz de usuario](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Clásica: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

Este tutorial to-end muestra cómo crear un grupo de disponibilidad de SQL Server con el Administrador de recursos de Azure máquinas virtuales de Windows. El tutorial utiliza aspas Azure para configurar una plantilla. Se revise la configuración predeterminada, escriba la configuración requerida y actualizar los módulos en el portal a medida que vaya a través de este tutorial.

Al final del curso, su solución de grupo de disponibilidad de SQL Server en Azure tendrá los siguientes elementos:

- Una red virtual que contenga varias subredes, incluidos un front-end y una subred de back-end

- Controlador de dominio dos con un dominio de Active Directory (AD)

- Dos SQL Server las máquinas virtuales implementado a la subred back-end y unido al dominio de Active Directory

- Un clúster de nodo 3 WSFC con el modelo de quórum de mayoría de nodo

- Un grupo de disponibilidad con dos réplicas sincrónico confirmación de una base de datos de disponibilidad

La siguiente ilustración es una representación gráfica de la solución.

![Arquitectura de laboratorio de prueba para AG en Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Todos los recursos en esta solución pertenecen a un único grupo de recursos.

En este tutorial se supone lo siguiente:

- Ya tiene una cuenta de Azure. Si no tiene una, [iniciar sesión en una cuenta de prueba](http://azure.microsoft.com/pricing/free-trial/).

- Ya sabe cómo aprovisionar una VM de SQL Server desde la Galería de máquina virtual mediante la interfaz gráfica. Para obtener más información, vea [el aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-windows-portal-sql-server-provision.md)

- Ya tiene una sólida comprensión de los grupos de disponibilidad. Para obtener más información, consulte [siempre en grupos de disponibilidad (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Si está interesado en el uso de grupos de disponibilidad con SharePoint, vea también [configurar SQL Server 2012 siempre en grupos de disponibilidad para SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).

En este tutorial usará el portal de Azure para:

- Seleccione el la plantilla siempre en el portal

- Revisar la configuración de la plantilla y actualizar algunas opciones de configuración para su entorno

- Monitor Azure tal como crea todo el entorno

- Conectarse a uno de los controladores de dominio y, a continuación, en uno de los servidores de SQL

[AZURE.INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]


## <a name="provision-the-cluster-from-the-gallery"></a>Aprovisionar el clúster de la Galería

Azure proporciona una imagen de la Galería para la solución completa. Para encontrar la plantilla:

1.  Inicie sesión en el portal de Azure con su cuenta.
1.  En el portal Azure, haga clic en **+ nuevo.** El portal abrirá el nuevo módulo.
1.  En el nuevo módulo busque **AlwaysOn**.
![Buscar plantilla AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
1.  En los resultados de búsqueda busque **AlwaysOn clúster de SQL Server**.
![Plantilla de AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
1.  En **Seleccionar un modelo de implementación** , elija **Administrador de recursos**.

### <a name="basics"></a>Conceptos básicos

Haga clic en **conceptos básicos** y configure las siguientes opciones:

- **Nombre de usuario administrador** es una cuenta de usuario con permisos de administrador de dominio y un miembro de la función de servidor fija sysadmin de SQL Server en ambas instancias de SQL Server. En este tutorial use el **Administrador de dominio**.

- **Contraseña** es la contraseña de la cuenta de administrador de dominio. Usar una contraseña compleja. Confirme la contraseña.

- **Suscripción** es la que facturará Azure para ejecutar todos los recursos implementados para el grupo de disponibilidad. Puede especificar una suscripción diferente si su cuenta tiene varias suscripciones.

- **Grupo de recursos** es el nombre para el grupo que todos los recursos de Azure crean por este tutorial pertenecerá. En este tutorial use **SQL-HA-c**. Para obtener más información, vea (información general del Administrador de recursos de Azure) [recursos-grupo-overview.md / #-grupos de recursos].

- **Ubicación** es la región de Azure donde se crearán los recursos de este tutorial. Seleccione una región para hospedar la infraestructura de Azure.

A continuación se muestra el aspecto que tendrá el módulo de **conceptos básicos** :

![Conceptos básicos](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

- Haga clic en **Aceptar**.

### <a name="domain-and-network-settings"></a>Configuración de dominios y redes

Esta plantilla de la Galería de Azure, crea un nuevo dominio con controladores de dominio nuevo. También se crea una nueva red y dos subredes. La plantilla no permite la creación de los servidores en un dominio ya existente o una red virtual. El siguiente paso es configurar el dominio y la red.

En el módulo de **configuración de dominios y redes** , revise los valores preestablecidos para la configuración de dominios y redes:

- **Nombre del dominio raíz del bosque** es el nombre de dominio que se utilizará para el dominio de Active Directory que va a hospedar el clúster. Para el tutorial de uso **contoso.com**.

- **Nombre de red virtual** es el nombre de red de la red virtual Azure. En este tutorial, use **autohaVNET**.

- **Nombre de subred del controlador de dominio** es el nombre de una parte de la red virtual que hospeda el controlador de dominio. En este tutorial use **subred 1**. Esta subred usará dirección prefijo **10.0.0.0/24**.

- **Nombre de subred de SQL Server** es el nombre de una parte de la red virtual que hosts los servidores SQL Server y el archivo compartan a auxiliares. En este tutorial use **subred 2**. Esta subred usará dirección prefijo **10.0.1.0/26**.

Para obtener más información acerca de las redes virtuales en [Azure vea información general de red Virtual](../virtual-network/virtual-networks-overview.md).  

La **configuración de dominio y la red** debería tener este aspecto:

![Configuración de dominios y redes](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Si es necesario, puede cambiar estos valores. En este tutorial usamos los valores preestablecidos.

- Revise la configuración y haga clic en **Aceptar**.

###<a name="availability-group-settings"></a>configuración del grupo de disponibilidad

En **Configuración del grupo de disponibilidad** , revise los valores preestablecidos para el grupo de disponibilidad y la escucha.

- **Nombre del grupo de disponibilidad** es el nombre de recurso de grupo para el grupo de disponibilidad. En este tutorial use **ag de Contoso**.

- **nombre de la escucha de disponibilidad grupo** se usa en el clúster y equilibrador de carga interno. Clientes que se conectan a SQL Server pueden utilizar este nombre para conectarse a la réplica correspondiente de la base de datos. En este tutorial utilizar **escucha de Contoso**.

-  **puerto de escucha del grupo de disponibilidad** especifica que utilizará el puerto TCP escucha de SQL Server. En este tutorial, use el puerto predeterminado, **1433**.

Si es necesario, puede cambiar estos valores. En este tutorial, use los valores preestablecidos.  

![configuración del grupo de disponibilidad](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

- Haga clic en **Aceptar**.

###<a name="vm-size-storage-settings"></a>Tamaño de la memoria virtual, la configuración de almacenamiento

En **tamaño de memoria virtual, la configuración de almacenamiento** elegir un tamaño de la máquina virtual de SQL Server y revisar el resto de opciones.

- **Tamaño de la máquina virtual de SQL Server** es el tamaño de Azure máquina virtual para los servidores de SQL. Elija un tamaño de máquina virtual apropiado para su carga de trabajo. Si está creando este entorno para el tutorial use **DS2**. Para las cargas de trabajo de producción elija un tamaño de la máquina virtual que permita la carga de trabajo. Muchas cargas de trabajo de producción requieren **DS4** o mayor. La plantilla se crear dos máquinas virtuales de este tamaño e instalar a SQL Server en cada uno. Para obtener más información, vea [tamaños para máquinas virtuales](virtual-machines-linux-sizes.md).

>[AZURE.NOTE]Azure instalará de SQL Server Enterprise Edition. El coste depende de la edición y el tamaño de la máquina virtual. Para obtener información detallada sobre los costes actuales, vea [máquinas virtuales de precios](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

- **Tamaño de máquina virtual de controlador de dominio** es el tamaño de la máquina virtual para los controladores de dominio. En este tutorial use **D2**.

- **Tamaño de archivo compartir auxiliar máquina virtual** es el tamaño de máquina virtual para el auxiliar de recurso compartido de archivos. En este tutorial use **A1**.

- **Cuenta de almacenamiento de SQL** es el nombre de la cuenta de almacenamiento para almacenar los datos de SQL Server y los discos del sistema operativo. En este tutorial, use **alwaysonsql01**.

- **Cuenta de almacenamiento DC** es el nombre de la cuenta de almacenamiento para los controladores de dominio. En este tutorial, use **alwaysondc01**.

- **Tamaño de disco de datos de SQL Server** en TB es el tamaño del disco de datos de SQL Server en TB. Especifique un número comprendido entre 1 y 4. Este es el tamaño del disco de datos que se va a unir cada servidor SQL Server. En este tutorial use **1**.

- **Optimización de almacenamiento** establece opciones de configuración de almacenamiento para las máquinas virtuales de SQL Server en función del tipo de carga de trabajo. Todos los servidores de SQL en este escenario usar almacenamiento premium con caché de host de disco Azure solo lectura. Además, puede optimizar la configuración de SQL Server para la carga de trabajo, elija uno de estos tres valores:

    - **Carga de trabajo general** no establece ninguna configuración específica

    - **Procesamiento transaccional** establece el indicador de traza 1117 y 1118

    - **Almacenamiento de datos** establece la marca de seguimiento 1117 y 610

En este tutorial use **carga de trabajo General**.

![Configuración de almacenamiento de tamaño VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

- Revise la configuración y haga clic en **Aceptar**.

####<a name="a-note-about-storage"></a>Una nota sobre almacenamiento

Optimizaciones adicionales dependen del tamaño de los discos de datos de SQL Server. Para cada terabyte de datos de un disco, Azure agrega un almacenamiento adicional de premium 1 TB (SSD). Cuando un servidor requiere de 2 TB o más, en la plantilla se crea un grupo de almacenamiento en cada servidor SQL Server. Un grupo de almacenamiento es una forma de virtualización de almacenamiento en varios discos están configurados para proporcionar mayor capacidad, resistencia y rendimiento.  A continuación, la plantilla crea un espacio de almacenamiento en el grupo de almacenamiento y presenta como un único de datos para el sistema operativo. La plantilla designa este disco como el disco de datos de SQL Server. La plantilla ajusta el bloque de almacenamiento para SQL Server con la configuración siguiente:

- Tamaño de las bandas es el valor de interleave del disco virtual. Para cargas de trabajo de transacciones se establece a 64 KB. Para las cargas de trabajo de almacenamiento de datos, el valor es 256 KB.

- La resistencia es sencilla (sin resistencia).

>[AZURE.NOTE] Almacenamiento de Azure premium es redundante localmente y conserva los tres copias de los datos dentro de una única región, por lo que no es necesaria la resistencia adicional en el bloque de almacenamiento.

- Número de columnas es igual al número de discos en el grupo de almacenamiento.

Para obtener información sobre el almacenamiento de grupos de almacenamiento y espacio vea:

- [Información general de espacios de almacenamiento](http://technet.microsoft.com/library/hh831739.aspx).

- [Conjuntos de almacenamiento y copia de seguridad de Windows Server](http://technet.microsoft.com/library/dn390929.aspx)

Para obtener más información acerca de prácticas recomendadas de configuración de SQL Server, consulte [prácticas recomendadas de rendimiento para SQL Server en máquinas virtuales de Windows Azure](virtual-machines-windows-sql-performance.md)


###<a name="sql-server-settings"></a>Configuración de SQL Server

En **configuración de SQL Server** revise y modifique el prefijo de nombre de la máquina virtual de SQL Server, versión de SQL Server, cuenta de servicio de SQL Server y contraseña y el SQL auto revisiones de la programación de mantenimiento.

- **Prefijo de nombre de SQL Server** se usa para crear un nombre para cada servidor SQL Server. En este tutorial use **ag de Contoso**. Los nombres de SQL Server será *Contoso-ag-0* y *Contoso-ag-1*.

- **Versión de SQL Server** es la versión de SQL Server. En este tutorial use **2014 de SQL Server**. También puede elegir **SQL Server 2012** o **SQL Server 2016**.

- **Nombre de usuario de cuenta de servicio de SQL Server** es el nombre de cuenta de dominio para el servicio SQL Server. En este tutorial use **sqlservice**.

- **Contraseña** es la contraseña para la cuenta de servicio de SQL Server.  Usar una contraseña compleja. Confirme la contraseña.

- **Programación de mantenimiento de SQL automática revisiones** identifica la semana que Azure revisará automáticamente los servidores de SQL. En este tutorial, escriba **domingo**.

- **SQL automática revisiones de hora de inicio de mantenimiento** es la hora del día de la región de Azure cuando revisiones automáticas comenzará.

>[AZURE.NOTE]La ventana de revisiones para cada VM es escalonada una hora. Una máquina virtual solo está modificada en un momento para evitar la interrupción de los servicios.

![Configuración de SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Revise la configuración y haga clic en **Aceptar**.

###<a name="summary"></a>Resumen

En la página Resumen Azure valide la configuración. También puede descargar la plantilla. Revise el resumen. Haga clic en **Aceptar**.

###<a name="buy"></a>Comprar

Este módulo final contiene **términos de uso**y la **política de privacidad**. Revise esta información. Cuando esté listo para Azure empezar a crear las máquinas virtuales y todos los otros recursos necesarios para el grupo de disponibilidad, haga clic en **crear**.

El portal de Azure creará el grupo de recursos y todos los recursos.

##<a name="monitor-deployment"></a>Implementación del Monitor

Supervisar el progreso de la implementación desde el portal de Azure. Un icono que representa la implementación automáticamente está anclado a Azure panel portal.

![Panel de Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

##<a name="connect-to-sql-server"></a>Conectarse a SQL Server

Las nuevas instancias de SQL Server se ejecutan en máquinas virtuales que no tiene conexiones a internet. Sin embargo, los controladores de dominio tiene una conexión a internet. Para conectarse a los servidores de SQL Server con Escritorio remoto, primera RDP a uno de los controladores de dominio. En el controlador de dominio abra una segunda RDP a SQL Server.

A RDP en el controlador de dominio principal, siga estos pasos:

1.  Panel portal Azure muy que la implementación se ha realizado correctamente.

1.  Haga clic en **recursos**.

1.  En la hoja de **recursos** , haga clic en **dc de ad principal** que es el nombre del equipo de la máquina virtual para el controlador de dominio principal.

1.  En el módulo de **dc de ad principal** , haga clic en **Conectar**. El explorador le preguntará si desea abrir o guardar el objeto de conexión remota. Haga clic en **Abrir**.
![Conectarse a DC](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/13-ad-primary-dc-connect.png)
1.  **Conexión a Escritorio remoto** puede advertir que no se puede identificar el Editor de esta conexión remota. Haga clic en **Conectar**.

1.  Seguridad de Windows en el que se le pide que introduzca sus credenciales para conectarse a la dirección IP del controlador de dominio principal. Haga clic en **usar otra cuenta**. Nombre de **usuario** , escriba **contoso\DomainAdmin**. Esta es la cuenta que eligió en nombre de usuario administrador. Utilice la contraseña compleja que eligió al configurar la plantilla.

1.  **Escritorio remoto** puede advertir que el equipo remoto no se pudo autenticar debido a problemas con su certificado de seguridad. Se muestran el nombre del certificado de seguridad. Si ha seguido el tutorial el nombre será **dc.contoso.com de primario de ad**. Haga clic en **Sí**.

Ahora está conectado al controlador de dominio principal. RDP a SQL Server, siga estos pasos:

1.  En el controlador de dominio, abra **Conexión a Escritorio remoto**.

1.  Para el **equipo**, escriba el nombre de uno de los servidores de SQL. En este tutorial, escriba **0 de SQL Server**.

1.  Use la misma cuenta de usuario y la contraseña que utilizó para RDP en el controlador de dominio.

Ahora está conectado con RDP a SQL Server. Puede Abra SQL Server management studio, conectarse a la instancia predeterminada de SQL Server y comprobar que la disponibilidad de información grupo se ha configurado.


