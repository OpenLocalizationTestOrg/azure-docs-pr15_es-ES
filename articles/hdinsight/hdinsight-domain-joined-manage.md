<properties
    pageTitle="Administrar dominio une HDInsight clústeres | Microsoft Azure"
    description="Obtenga información sobre cómo administrar clústeres HDInsight de dominio"
    services="hdinsight"
    documentationCenter=""
    authors="saurinsh"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/25/2016"
    ms.author="saurinsh"/>

# <a name="manage-domain-joined-hdinsight-clusters-preview"></a>Administrar dominio une HDInsight clústeres (vista preliminar)



Obtenga información sobre los usuarios y los roles de HDInsight de dominio y cómo administrar clústeres de HDInsight de dominio.

## <a name="users-of-domain-joined-hdinsight-clusters"></a>Usuarios de dominio une HDInsight clústeres

Un clúster de HDInsight que no se ha unido dominio tiene dos cuentas de usuario que se crean durante la creación de clúster:

- **Administración de Ambari**: esta cuenta es también conocida como *usuario de Hadoop* o *HTTP*. Esta cuenta puede utilizarse para iniciar sesión en Ambari en https://&lt;nombreDeClúster >. azurehdinsight.net. También puede usarse para ejecutar consultas en vistas de Ambari, ejecutar trabajos mediante herramientas externas (es decir, PowerShell, Templeton, Visual Studio) y autenticar con el controlador ODBC de sección y herramientas de BI (es decir, Excel, PowerBI o una plantilla).

- **Usuario SSH**: esta cuenta puede usarse con SSH y ejecutar comandos sudo. Tiene privilegios de raíz para las máquinas virtuales de Linux.

Un clúster de HDInsight dominio tiene tres nuevos usuarios además de Ambari administrador y usuario SSH.

- **Administración de Cazador**: esta es la cuenta de administrador local de Apache Cazador. No es un usuario de dominio de active directory. Esta cuenta puede usarse para configurar directivas y realizar otros administradores de los usuarios o los administradores delegados (para que los usuarios pueden administrar las directivas). De forma predeterminada, el nombre de usuario es *Administrador* y la contraseña es la misma que la contraseña de administrador de Ambari. La contraseña se puede actualizar de la página de configuración de Cazador.

- **Usuario del dominio de administración de clúster**: esta cuenta es un usuario de dominio de active directory designado como el Administrador de clúster de Hadoop incluidos Ambari y Cazador. Debe proporcionar las credenciales de este usuario durante la creación de un clúster. Este usuario tiene los privilegios siguientes:

    - Unirse a equipos en el dominio y colóquelos dentro de la OU que especifique durante la creación de clúster.
    - Crear a entidades de servicio en la unidad organizativa que especifique durante la creación de clúster. 
    - Crear entradas DNS inversas.

    Tenga en cuenta que los demás usuarios de AD también tienen estos privilegios. 

    Hay algunos puntos finales en el clúster (por ejemplo, Templeton) que no están administrados por Cazador y, por tanto, no son seguras. Estos puntos finales están bloqueados para todos los usuarios excepto el usuario del dominio de administración de clúster. 

- **Normal**: durante la creación del clúster, puede proporcionar varios grupos de active directory. Los usuarios a estos grupos se sincronizarán con Cazador y Ambari. Estos usuarios son los usuarios de dominio y tendrán acceso a los extremos de Cazador administrados solo (por ejemplo, Hiveserver2). Todas las directivas RBAC y auditoría habrá aplicables a estos usuarios.

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>Funciones de dominio une HDInsight clústeres

HDInsight de dominio tiene los siguientes roles:

- Administrador de clústeres
- Operador de clúster
- Administrador del servicio
- Operador de servicio
- Usuario de clúster

**Para ver los permisos de estas funciones**

1. Abra la interfaz de usuario de administración de Ambari.  Vea [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Roles**.
3. Haga clic en el signo de interrogación azul para ver los permisos:

    ![Permisos de roles de HDInsight de dominio](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Abra el interfaz de usuario de administración de Ambari

1. Inicie sesión el [portal de Azure](https://portal.azure.com).
2. Abra el clúster de HDInsight en un módulo. Consulte [clústeres de lista y mostrar](hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Haga clic en **panel** en el menú superior para abrir Ambari.
4. Inicie sesión en Ambari con el nombre de usuario de dominio de clúster administrador y la contraseña.
5. Haga clic en el menú desplegable de **administración** de la esquina superior derecha y, a continuación, haga clic en **Administrar Ambari**.

    ![HDInsight de dominio administrar Ambari](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    La interfaz de usuario tiene el siguiente aspecto:

    ![Administración de HDInsight Ambari dominio interfaz de usuario](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Lista de los usuarios de dominio que se sincronizan desde Active Directory

1. Abra la interfaz de usuario de administración de Ambari.  Vea [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **usuarios**. Deberá ver todos los usuarios que se sincronizan desde Active Directory al clúster HDInsight.

    ![Dominio une los usuarios de lista de interfaz de usuario de administración de HDInsight Ambari](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Lista de los grupos de dominio que se sincronizan desde Active Directory

1. Abra la interfaz de usuario de administración de Ambari.  Vea [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **grupos**. Deberá ver todos los grupos que se sincronizan desde Active Directory al clúster HDInsight.

    ![Dominio conjunto de los grupos de lista de interfaz de usuario de administración de HDInsight Ambari](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)


## <a name="configure-hive-views-permissions"></a>Configurar los permisos de vistas de sección

1. Abra la interfaz de usuario de administración de Ambari.  Vea [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **vistas**.
3. Haga clic en la **sección** para mostrar los detalles.

    ![Administración de HDInsight Ambari dominio vistas de sección de interfaz de usuario](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. Haga clic en el vínculo de la **Vista de sección** para configurar las vistas de sección.
5. Desplácese hacia abajo hasta la sección **permisos** .

    ![Configurar los permisos de dominio une HDInsight Ambari administración vistas de sección de interfaz de usuario](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Haga clic en **Agregar usuario** o **Grupo agregar**y, a continuación, especifique los usuarios o grupos a los que pueden usar vistas de sección. 

## <a name="configure-users-for-the-roles"></a>Configurar usuarios para los roles

 Para ver una lista de funciones y sus permisos, vea [funciones de dominio une HDInsight clústeres](#roles-of-domain---joined-hdinsight-clusters).

1. Abra la interfaz de usuario de administración de Ambari.  Vea [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Roles**.
3. Haga clic en **Agregar usuario** o **Grupo de agregar** para asignar los usuarios y grupos de funciones diferentes.
 
## <a name="next-steps"></a>Pasos siguientes

- Para configurar un clúster de dominio HDInsight, consulte [Configurar dominio HDInsight clústeres](hdinsight-domain-joined-configure.md).
- Para configurar las directivas de la sección y ejecutar consultas de sección, vea [Configurar subárbol directivas para clústeres HDInsight unido al dominio](hdinsight-domain-joined-run-hive.md).
- Para ejecutar consultas de subárbol con SSH en clústeres HDInsight de dominio, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-domain-joined-hdinsight-cluster).
