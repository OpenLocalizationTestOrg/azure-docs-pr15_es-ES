<properties
    pageTitle="Introducción a la sincronización de datos de las bases de datos de SQL"
    description="Este tutorial le ayuda a empezar a trabajar con la sincronización de datos de SQL Azure (vista preliminar)."
    services="sql-database"
    documentationCenter=""
    authors="jennieHubbard"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/11/2016"
    ms.author="jhubbard"/>


#<a name="getting-started-with-azure-sql-data-sync-preview"></a>Introducción a la sincronización de datos SQL Azure (vista preliminar)
En este tutorial, aprenderá los fundamentos de la sincronización de datos de SQL Azure con el Portal de clásico de Azure.

En este tutorial se supone mínima experiencia anterior con SQL Server y la base de datos de SQL Azure. En este tutorial, crear un grupo de sincronización híbrido (SQL Server e instancias de base de datos SQL) totalmente configurado y sincronizar en la programación establecida.

> [AZURE.NOTE] La documentación técnica completa para la sincronización de datos de SQL Azure, anteriormente se encuentra en MSDN, está disponible como un archivo .pdf. Descargarlo [aquí](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

## <a name="step-1-connect-to-the-azure-sql-database"></a>Paso 1: Conectar con la base de datos SQL Azure

1. Inicie sesión en el [Portal de clásico](http://manage.windowsazure.com).

2. En el panel izquierdo, haga clic en **Bases de datos de SQL** .

3. Haga clic en **sincronizar** en la parte inferior de la página. Al hacer clic en sincronizar, aparece una lista que muestra las acciones que puede agregar - **Nuevo grupo de sincronización** y **Nuevo agente de sincronización**.

4. Para iniciar al Asistente de nuevo agente de sincronización de datos de SQL, haga clic en **Nuevo agente de sincronización**.

5. Si todavía no lo ha agregado a un agente anteriormente, **haga clic en descargar aquí**.

    ![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)


## <a name="step-2-add-a-client-agent"></a>Paso 2: Agregar a un agente de cliente
Este paso es necesario únicamente si va a tener una base de datos de SQL Server local incluido en el grupo de sincronización. Si su grupo de sincronización tiene sólo instancias de base de datos de SQL, vaya al paso 4.

<a id="InstallRequiredSoftware"></a>
### <a name="step-2a-install-the-required-software"></a>Paso 2a: instalar el software necesario
Asegúrese de que tiene instalado en el equipo donde instale el agente de cliente.

- **.NET framework 4.0**

 Instale .NET Framework 4.0 desde [aquí](http://go.microsoft.com/fwlink/?linkid=205836).

- **Tipos CLR de Microsoft SQL Server 2008 R2 SP1 sistema (x86)**

 Instalar a la tipos CLR de Microsoft SQL Server 2008 R2 SP1 sistema (x86) desde [aquí](http://www.microsoft.com/download/en/details.aspx?id=26728)

- **Microsoft SQL Server 2008 R2 SP1 compartido objetos de administración (x86)**

 Instalar los objetos de Microsoft SQL Server 2008 R2 SP1 compartido administración (x86) desde [aquí](http://www.microsoft.com/download/en/details.aspx?id=26728)



<a id="InstallClient"></a>
### <a name="step-2b-install-a-new-client-agent"></a>Paso 2 mil millones: instalar un nuevo agente de cliente

Siga las instrucciones de [instalación de un agente de cliente (sincronización de datos de SQL)](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf) para instalar al agente.



<a id="RegisterSSDb"></a>
### <a name="step-2c-finish-the-new-sql-data-sync-agent-wizard"></a>Paso 2c: finalice el Asistente de nuevo agente de sincronización de datos de SQL

1.  Volver al Asistente de nuevo agente de sincronización de datos de SQL.
2.  Asigne al agente de un nombre significativo.
3.  En la lista desplegable, seleccione la **región** (centro de datos) para hospedar a este agente.
4.  En la lista desplegable, seleccione la **suscripción** a este agente de host.
5.  Haga clic en la flecha derecha.



## <a name="step-3-register-a-sql-server-database-with-the-client-agent"></a>Paso 3: Registrar una base de datos de SQL Server con el agente de cliente

Una vez instalado el agente de cliente, registrar cada base de datos de SQL Server local que desea incluir en un grupo de sincronización con el agente.
Para registrar una base de datos con el agente, siga las instrucciones de [registrar una base de datos de SQL Server con un agente de cliente](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).



## <a name="step-4-create-a-sync-group"></a>Paso 4: Crear un grupo de sincronización


<a id="StartNewSGWizard"></a>
### <a name="step-4a-start-the-new-sync-group-wizard"></a>Paso 4: iniciar el Asistente para nuevo grupo de sincronización

1.  Volver al [Portal clásico](http://manage.windowsazure.com).
2.  Haga clic en **bases de datos de SQL**.
3.  Haga clic en **Agregar sincronización** en la parte inferior de la página, a continuación, seleccione Nuevo grupo de sincronización de la bandeja.

    ![Imagen2](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)



<a id=""></a>
### <a name="step-4b-enter-the-basic-settings"></a>Paso 4b: introducir la configuración básica


1.  Escriba un nombre descriptivo para el grupo de sincronización.
2.  En la lista desplegable, seleccione la **región** (centro de datos) para hospedar este grupo de sincronización.
3. Haga clic en la flecha derecha.

    ![Image3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)


<a id="DefineHubDB"></a>
### <a name="step-4c-define-the-sync-hub"></a>Paso 4c: definir el concentrador de sincronización

1. En la lista desplegable, seleccione la instancia de base de datos de SQL para que actúe como concentrador de grupo de sincronización.
2. Escriba las credenciales para esta instancia de base de datos SQL - **Concentrador de nombre de usuario** y **Contraseña de concentrador**.
3. Espere a que la sincronización de datos de SQL para confirmar el nombre de usuario y la contraseña. Aparecerá una marca de verificación verde a la derecha de la contraseña cuando se confirman las credenciales.
4. En la lista desplegable, seleccione la directiva de **Resolución de conflictos** .

 **Concentrador gana** - sobrescritura cambios en la misma hacer referencia a cualquier cambio que se escriben en la escritura de la base de datos de concentrador para las bases de datos de referencia, registro de base de datos. Funcionalidad, esto significa que el primer escrito en el hub de cambio se propaga a las otras bases de datos.


 **Cliente gana** - cambios escritos en el hub de sobrescriben los cambios en las bases de datos de referencia. Funcionalidad, esto significa que el último cambio escrito en el hub es la mantienen y se propaga a las otras bases de datos.

5.  Haga clic en la flecha derecha.

    ![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<a id="AddRefDB"></a>
### <a name="step-4d-add-a-reference-database"></a>Paso 4d: agregar una base de datos de referencia


Repita este paso para cada base de datos adicional que desea agregar al grupo de sincronización.

1. En la lista desplegable, seleccione la base de datos para agregar.

    Bases de datos en la lista desplegable incluyen ambas bases de datos de SQL Server que se han registrado con el agente y las instancias de base de datos de SQL.
2.  Escriba las credenciales para esta base de datos - **nombre de usuario** y **contraseña**.
3.  En la lista desplegable, seleccione la **Dirección de la sincronización** de esta base de datos.

    **Bidireccional** : los cambios en la base de datos de referencia se escriben en la base de datos de concentrador y cambios a la base de datos de concentrador se escriben en la base de datos de referencia.

    **Sincronización del concentrador** - la base de datos recibe actualizaciones del concentrador. No enviar los cambios al concentrador.

    **Sincronizar con el Hub** - la base de datos envía actualizaciones al concentrador. Cambios en el Hub de no se escriben en esta base de datos.

4.  Para terminar de crear el grupo de sincronización, haga clic en la marca de verificación en la esquina inferior derecha del asistente. Espere a que la sincronización de datos de SQL confirmar las credenciales. Una marca de verificación verde indica que se ha confirmado las credenciales.

5.  Haga clic en la marca de verificación una segunda vez. Vuelve a la página de **sincronización** en bases de datos de SQL. Este grupo de sincronización aparece ahora con otros grupos de sincronización y agentes.

    ![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)


## <a name="step-5-define-the-data-to-sync"></a>Paso 5: Definir los datos para sincronizar

Sincronización de datos de SQL Azure le permite seleccionar tablas y columnas para sincronizar. Si desea filtrar una columna para que solo las filas con valores específicos (como por ejemplo, edad > = 65) sincronizar, use el portal de sincronización de datos de SQL en Azure y la documentación en Seleccione las tablas, columnas y filas para sincronizar para definir los datos para sincronizar.

1.  Volver al [Portal clásico](http://manage.windowsazure.com).
2.  Haga clic en **bases de datos de SQL**.
3.  Haga clic en la pestaña **sincronización** .
4.  Haga clic en el nombre de este grupo de sincronización.
5.  Haga clic en la pestaña **Reglas de sincronización** .
6.  Seleccione la base de datos que desea proporcionar el esquema de grupo de sincronización.
7.  Haga clic en la flecha derecha.
8.  Haga clic en **Actualizar esquema**.
9.  Para cada tabla de la base de datos, seleccione las columnas para incluir en las sincronizaciones.
    - No se pueden seleccionar columnas con tipos de datos no admitidos.
    - Si no se seleccionan columnas de una tabla, la tabla no se incluye en el grupo de sincronización.
    - Para seleccionar o anular la selección de todas las tablas, haga clic en seleccionar en la parte inferior de la pantalla.
10. Haga clic en **Guardar**y espere a que el grupo de sincronización a fin de aprovisionamiento.
11. Para volver a la página de inicio de la sincronización de datos, haga clic en la flecha atrás en la esquina superior izquierda de la pantalla (encima del nombre del grupo de sincronización).

    ![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## <a name="step-6-configure-your-sync-group"></a>Paso 6: Configurar el grupo de sincronización

Siempre puede sincronizar un grupo de sincronización, haga clic en sincronizar en la parte inferior de la página de inicio de la sincronización de datos.
Para sincronizar en una programación, configure el grupo de sincronización.

1.  Volver al [Portal clásico](http://manage.windowsazure.com).
2.  Haga clic en **bases de datos de SQL**.
3.  Haga clic en la pestaña **sincronización** .
4.  Haga clic en el nombre de este grupo de sincronización.
5.  Haga clic en la ficha **Configurar** .
6.  **SINCRONIZACIÓN AUTOMÁTICA**
    - Para configurar el grupo de sincronización para sincronizar en la frecuencia de un conjunto, haga clic en **activado**. Todavía puede sincronizar a petición, haga clic en sincronizar.
    - Haga clic en **desactivar** para configurar el grupo de sincronización para sincronizar solo al hacer clic en sincronizar.
7.  **FRECUENCIA DE SINCRONIZACIÓN**
    - Si la sincronización automática está activada, establezca la frecuencia de sincronización. La frecuencia debe estar entre 5 minutos y 1 mes.
8.  Haga clic en **Guardar**.

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

Enhorabuena. Ha creado un grupo de sincronización que incluye una instancia de base de datos de SQL y una base de datos de SQL Server.

## <a name="next-steps"></a>Pasos siguientes
Para obtener información adicional sobre la base de datos de SQL y sincronización de datos de SQL, consulte:

* [Descargar la documentación técnica completa de sincronización de datos de SQL](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)
* [Información general de base de datos SQL](sql-database-technical-overview.md)
* [Administración de ciclo de vida de la base de datos](https://msdn.microsoft.com/library/jj907294.aspx)
 

 
