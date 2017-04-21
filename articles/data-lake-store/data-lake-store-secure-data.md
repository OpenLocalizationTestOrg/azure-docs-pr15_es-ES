<properties 
   pageTitle="Proteger los datos almacenados en el almacén de lago de datos de Azure | Microsoft Azure" 
   description="Obtenga información sobre cómo proteger los datos en el almacén de lago de datos de Azure con los grupos y las listas de control de acceso" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/29/2016"
   ms.author="nitinme"/>

# <a name="securing-data-stored-in-azure-data-lake-store"></a>Proteger los datos almacenados en el almacén de lago de datos de Azure

Proteger los datos en el almacén de lago de datos de Azure es un enfoque de tres pasos.

1. Iniciar mediante la creación de grupos de seguridad de Azure Active Directory (AAD). Estos grupos de seguridad se usan para implementar el control de acceso basado en roles (RBAC) en el Portal de Azure. Para obtener más información, vea [Control de acceso basado en roles en Microsoft Azure](../active-directory/role-based-access-control-configure.md).

2. Asignar a los grupos de seguridad AAD a la cuenta de la tienda de lago de datos de Azure. Controla el acceso a la cuenta de almacén de datos lago de las operaciones de portal y la administración de las API o portal.

3. Asignar a los grupos de seguridad AAD como acceso a las listas de control (ACL) en el sistema de archivos de datos de la tienda lago.

4. Además, también puede establecer un intervalo de direcciones IP para los clientes que pueden tener acceso a los datos de almacén de datos de lago.

Este artículo ofrece instrucciones sobre cómo usar el portal de Azure para realizar las tareas anteriores. Para obtener información detallada sobre cómo implementa el almacén de datos de lago seguridad a nivel de cuenta y los datos, vea [seguridad en el almacén de lago de datos de Azure](data-lake-store-security-overview.md). Para obtener información sobre cómo se implementan ACL de almacén de lago de datos de Azure indicamos, vea [Información general de Control de acceso en el almacén de datos de lago](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).
- **Cuenta de un almacén de lago de datos de Azure**. Para obtener instrucciones sobre cómo crear uno, consulte [Introducción a la tienda de lago de datos de Azure](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Crear grupos de seguridad de Azure Active Directory

Para obtener instrucciones sobre cómo crear grupos de seguridad AAD y cómo agregar usuarios al grupo, vea [Administrar grupos de seguridad de Azure Active Directory](../active-directory/active-directory-accessmanagement-manage-groups.md).

## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Asignar usuarios o grupos de seguridad a las cuentas de almacén de lago de datos de Azure

Cuando se asigna a los usuarios o grupos de seguridad para cuentas de la tienda de lago de datos de Azure, puede controlar el acceso a las operaciones de administración de la cuenta mediante las API de administrador de recursos de Azure y Azure portal. 

1. Abrir una cuenta de la tienda de lago de datos de Azure. En el panel izquierdo, haga clic en **Examinar**, haga clic en **Almacén de datos de lago**y en el módulo de almacén de lago de datos, haga clic en el nombre de la cuenta a la que desea asignar a un usuario o grupo de seguridad.

2. En el módulo de almacén de datos lago cuenta, haga clic en **configuración**. En el módulo de **configuración** , haga clic en **usuarios**.

    ![Asignar el grupo de seguridad de cuenta de la tienda de lago de datos de Azure] (./media/data-lake-store-secure-data/adl.select.user.icon.png "Asignar el grupo de seguridad de cuenta de la tienda de lago de datos de Azure")

3. El módulo de **usuario** predeterminada muestra el grupo de **administradores de la suscripción** como propietario. 

    ![Agregar usuarios y roles] (./media/data-lake-store-secure-data/adl.add.group.roles.png "Agregar usuarios y roles")
 
    Hay dos formas de agregar un grupo y asignar roles relevantes.

    * Agregar un usuario o grupo a la cuenta y, a continuación, asigne un rol, o
    * Agregar una función y, a continuación, asignar a los usuarios o grupos a función.

    En esta sección, veremos el primer enfoque, agregar un grupo y, a continuación, asignación de roles. Puede realizar pasos similares para seleccionar una función y, a continuación, asignar a grupos a esa función.
    
4. En el módulo de **usuarios** , haga clic en **Agregar** para abrir el módulo de **access agregar** . En el módulo de **access agregar** , haga clic en **Seleccionar una función**y, a continuación, seleccione una función para el usuario o grupo.

     ![Agregar una función para el usuario] (./media/data-lake-store-secure-data/adl.add.user.1.png "Agregar una función para el usuario")

    El **propietario** y el rol de **Colaborador** proporcionan acceso a una amplia variedad de funciones de administración de la cuenta de lago de datos. Para los usuarios que interactúan con los datos en el lago de datos, puede agregarlos a la función **lector **. El ámbito de estas funciones se limita a las operaciones de administración relacionadas con la cuenta de la tienda de lago de datos de Azure.

    Datos permisos de sistema de archivos individuales de operaciones definan qué pueden hacer los usuarios. Por lo tanto, un usuario con una función lector puede solo ver configuración administrativa asociada con la cuenta pero puede potencialmente leer y escribir datos en función de los permisos de sistema de archivos que les ha asignado. Permisos de sistema de archivos de datos de la tienda lago se describen al [asignar el grupo de seguridad como ACL al sistema de archivos de almacenamiento de lago de datos de Azure](#filepermissions).



5. En el módulo de **access agregar** , haga clic en **Agregar usuarios** para abrir el módulo de **Agregar usuarios** . En este módulo, busque el grupo de seguridad que creó anteriormente en Azure Active Directory. Si tiene una gran cantidad de grupos para buscar desde, use el cuadro de texto en la parte superior para filtrar en el nombre del grupo. Haga clic en **Seleccionar**.

    ![Agregar un grupo de seguridad] (./media/data-lake-store-secure-data/adl.add.user.2.png "Agregar un grupo de seguridad")

    Si desea agregar un grupo o usuario que no se muestra, puede invitar a ellas mediante el icono **Invitar** y especificando la dirección de correo electrónico para el usuario o grupo.

6. Haga clic en **Aceptar**. Debería ver el grupo de seguridad agregado como se muestra a continuación.

    ![Grupo de seguridad agregado] (./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de seguridad agregado")

7. El grupo de seguridad del usuario ahora tiene acceso a la cuenta de la tienda de lago de datos de Azure. Si desea proporcionar acceso a usuarios específicos, puede agregarlos al grupo de seguridad. Del mismo modo, si desea revocar el acceso de un usuario, puede quitarlos del grupo de seguridad. También puede asignar a varios grupos de seguridad a una cuenta. 

## <a name="filepermissions"></a>Asignar usuarios o grupos de seguridad como ACL al sistema de archivos de almacenamiento de lago de datos de Azure

Asignando grupos de seguridad del usuario en el sistema de archivos de lago de datos de Azure, establezca el control de acceso en los datos almacenados en el almacén de lago de datos de Azure.

1. En el módulo de almacén de datos lago cuenta, haga clic en **Explorador de datos**.

    ![Crear directorios de almacén de datos de lago cuenta] (./media/data-lake-store-secure-data/adl.start.data.explorer.png "Crear directorios de datos lago cuenta")

2. En el módulo de **Explorador de datos** , haga clic en el archivo o carpeta que desea configurar las ACL y, a continuación, haga clic en **acceso**. Para asignar ACL a un archivo, debe hacer clic en **acceso** de la hoja de **Vista previa del archivo** .

    ![Establecer ACL en el sistema de archivos de datos lago] (./media/data-lake-store-secure-data/adl.acl.1.png "Establecer ACL en el sistema de archivos de datos lago")

3. El módulo de **Access** muestra el acceso estándar y personalizado ya asignado a la raíz. Haga clic en el icono **Agregar** para agregar las ACL de nivel personalizado.

    ![Lista estándar y acceso personalizado] (./media/data-lake-store-secure-data/adl.acl.2.png "Lista estándar y acceso personalizado")

    * **Acceder estándar** es el acceso de estilo UNIX, donde puede especificar lectura, escribir, ejecutar (rwx) tres clases de usuario diferentes: propietario, grupo y otras personas.
    * **Acceso personalizado** corresponde a las ACL de POSIX que le permite establecer permisos para usuarios con nombre específico o grupos y no solo el archivo propietario o grupo. 
    
    Para obtener más información, vea [HDFS ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Para obtener más información sobre cómo se implementan ACL de almacén de lago de datos, vea [Control de acceso en el almacén de datos de lago](data-lake-store-access-control.md).

4. Haga clic en el icono de **Agregar** para abrir el módulo de **Agregar acceso de personalizado** . En este módulo, haga clic en **Seleccionar usuario o grupo**y, a continuación, en el módulo de **Seleccionar usuario o grupo** , busque el grupo de seguridad que creó anteriormente en Azure Active Directory. Si tiene una gran cantidad de grupos para buscar desde, use el cuadro de texto en la parte superior para filtrar en el nombre del grupo. Haga clic en el grupo que desea agregar y, a continuación, haga clic en **Seleccionar**.

    ![Agregar un grupo] (./media/data-lake-store-secure-data/adl.acl.3.png "Agregar un grupo")

5. Haga clic en **Seleccionar permisos**, seleccione los permisos y si desea asignar los permisos de forma predeterminada ACL, tener acceso a ACL o ambos. Haga clic en **Aceptar**.

    ![Asignar permisos de grupo] (./media/data-lake-store-secure-data/adl.acl.4.png "Asignar permisos de grupo")

    Para obtener más información sobre los permisos en el almacén de datos de lago y ACL del acceso o de forma predeterminada, consulte [Control de acceso en el almacén de datos de lago](data-lake-store-access-control.md).


6. En el módulo de **Agregar acceso de personalizada** , haga clic en **Aceptar**. El grupo recién agregado, con los permisos asociados, ahora se mostrarán en el módulo de **Access** .

    ![Asignar permisos de grupo] (./media/data-lake-store-secure-data/adl.acl.5.png "Asignar permisos de grupo")

    > [AZURE.IMPORTANT] En la versión actual, sólo puede tener 9 entradas en **Acceso personalizado**. Si desea agregar más de 9 usuarios, debe crear grupos de seguridad, agregar usuarios a grupos de seguridad, agregue proporcionan acceso a los grupos de seguridad de la cuenta de almacén de datos lago.

7. Si es necesario, también puede modificar los permisos de acceso después de haber agregado al grupo. Active o desactive la casilla de verificación para cada tipo de permiso (leer, escribir, ejecutar) en función de si desea quitar o asignar ese permiso al grupo de seguridad. Haga clic en **Guardar** para guardar los cambios o **Descartar** para deshacer los cambios.

## <a name="set-ip-address-range-for-data-access"></a>Establecer el intervalo de direcciones IP para el acceso a datos

Almacén de lago de datos de Azure le permite bloquear aún más el acceso a su almacén de datos en el nivel de red. Puede habilitar el firewall, especifique una dirección IP o definir un intervalo de direcciones IP para los clientes de confianza. Una vez habilitada, solo los clientes que tienen las direcciones IP de rango definido pueden conectarse a la tienda.

![Configuración del firewall y acceso IP] (./media/data-lake-store-secure-data/firewall-ip-access.png "Configuración del firewall y la dirección IP")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Quitar grupos de seguridad para una cuenta de la tienda de lago de datos de Azure

Al quitar grupos de seguridad de cuentas de la tienda de lago de datos de Azure, solo se cambia el acceso a las operaciones de administración de la cuenta mediante las API de administrador de recursos de Azure y Azure Portal.

1. En el módulo de almacén de datos lago cuenta, haga clic en **configuración**. En el módulo de **configuración** , haga clic en **usuarios**.

    ![Asignar el grupo de seguridad para la cuenta de lago de datos de Azure] (./media/data-lake-store-secure-data/adl.select.user.icon.png "Asignar el grupo de seguridad para la cuenta de lago de datos de Azure")

2. En el módulo de **usuarios** , haga clic en el grupo de seguridad que desea quitar.

    ![Grupo de seguridad para quitar] (./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de seguridad para quitar")

3. En el módulo para el grupo de seguridad, haga clic en **Quitar**.

    ![Quitar el grupo de seguridad] (./media/data-lake-store-secure-data/adl.remove.group.png "Quitar el grupo de seguridad")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Quitar grupo de seguridad ACL de sistema de archivos de almacenamiento de lago de datos de Azure

Al quitar grupos de seguridad de las ACL de sistema de archivos de almacenamiento de lago de datos de Azure, cambiar el acceso a los datos del almacén de datos lago.

1. En el módulo de almacén de datos lago cuenta, haga clic en **Explorador de datos**.

    ![Crear directorios de datos lago cuenta] (./media/data-lake-store-secure-data/adl.start.data.explorer.png "Crear directorios de datos lago cuenta")

2. En el módulo de **Explorador de datos** , haga clic en el archivo o carpeta que desea quitar las ACL y, a continuación, en el módulo de cuenta, haga clic en el icono de **Access** . Para quitar ACL para un archivo, debe hacer clic en **acceso** de la hoja de **Vista previa del archivo** .

    ![Establecer ACL en el sistema de archivos de datos lago] (./media/data-lake-store-secure-data/adl.acl.1.png "Establecer ACL en el sistema de archivos de datos lago")

3. En el módulo de **Access** , en la sección **Acceso personalizada** , haga clic en el grupo de seguridad que desea quitar. En el módulo de **Access personalizada** , haga clic en **Quitar** y, a continuación, haga clic en **Aceptar**.

    ![Asignar permisos de grupo] (./media/data-lake-store-secure-data/adl.remove.acl.png "Asignar permisos de grupo")


## <a name="see-also"></a>Vea también

- [Introducción a Azure lago de almacén de datos](data-lake-store-overview.md)
- [Copiar datos de Azure almacenamiento de Blobs al almacén de datos lago](data-lake-store-copy-data-azure-storage-blob.md)
- [Usar el análisis de datos de Azure lago con el almacén de datos de lago](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar Azure HDInsight con el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Empezar a trabajar con el almacén de datos lago con PowerShell](data-lake-store-get-started-powershell.md)
- [Empezar a trabajar con el almacén de lago de datos mediante el SDK de .NET](data-lake-store-get-started-net-sdk.md)
- [Registros de diagnóstico de acceso para el almacén de datos de lago](data-lake-store-diagnostic-logs.md)
