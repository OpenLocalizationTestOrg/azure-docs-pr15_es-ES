<properties
    pageTitle="Conectar el Administrador de configuración para el análisis de registro | Microsoft Azure"
    description="Este artículo le muestra los pasos para conectar el Administrador de configuración para el análisis de registro y comenzar a analizar los datos."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="banders"/>

# <a name="connect-configuration-manager-to-log-analytics"></a>Conectar el Administrador de configuración para el análisis de registro

Puede conectar el Administrador de configuración de System Center para el análisis de registro en OMS para sincronizar datos de la colección de dispositivo. Esto permite que los datos de la implementación del Administrador de configuración disponible en OMS.

Hay un número de pasos necesarios para conectar el Administrador de configuración con OMS, así que aquí encontrará un breve resumen de todo el proceso:

1. En el Portal de administración de Azure, registrar el Administrador de configuración como una aplicación de la aplicación Web o de Web API y asegurarse de que tiene el identificador de cliente y la clave de cliente desde el registro de Azure Active Directory. Vea [portal de usar para crear la aplicación de Active Directory y el servicio principal que puede tener acceso a los recursos](../resource-group-create-service-principal-portal.md) para obtener información detallada sobre cómo realizar este paso.
2. En el Portal de administración de Azure, [proporcione el Administrador de configuración (la aplicación web registrado) con permiso de acceso a OMS](#provide-configuration-manager-with-permissions-to-oms).
3. En Administrador de configuración, [Agregar una conexión con el Asistente para agregar conexión OMS](#add-an-oms-connection-to-configuration-manager).
4. En el Administrador de configuración, puede [actualizar las propiedades de conexión](#update-oms-connection-properties) si la clave de cliente o contraseña nunca expira o se pierde.
5. Con la información desde el portal OMS, [Descargue e instale el agente de supervisión de Microsoft](#download-and-install-the-agent) en el equipo que ejecuta la conexión de servicio del Administrador de configuración punto de función del sistema del sitio. El agente envía los datos del Administrador de configuración a OMS.
6. En OMS, [Importar desde el Administrador de configuración de colecciones](#import-collections) como grupos de equipos.
7. En OMS, ver los datos desde el Administrador de configuración como [grupos de equipos](log-analytics-computer-groups.md).

Puede obtener más información sobre cómo conectar el Administrador de configuración a OMS al [sincronizar datos desde el Administrador de configuración para la serie de administración de operaciones de Microsoft](https://technet.microsoft.com/library/mt757374.aspx).



## <a name="provide-configuration-manager-with-permissions-to-oms"></a>Proporcionar el Administrador de configuración con permisos a OMS

El procedimiento siguiente proporciona el Portal de administración de Azure con permisos para tener acceso a OMS. Más concretamente, debe conceder el *rol de colaborador* para los usuarios en el grupo de recursos. A su vez, que permite el Portal de administración de Azure al que conectarse OMS en Administrador de configuración.

>[AZURE.NOTE] Debe especificar permisos para OMS para el Administrador de configuración. En caso contrario, recibirá un mensaje de error al usar el Asistente para configuración en el Administrador de configuración.


1. Abra el [portal de Azure](https://portal.azure.com/) y haga clic en **Examinar** > **Análisis de registro (OMS)** para abrir el módulo de análisis de registro (OMS).  
2. En el módulo de **Análisis de registro (OMS)** , haga clic en **Agregar** para abrir el módulo de **Área de trabajo de OMS** .  
  ![Módulo OMS](./media/log-analytics-sccm/sccm-azure01.png)
3. En el módulo de **Área de trabajo de OMS** , proporcione la siguiente información y, a continuación, haga clic en **Aceptar**.
  - **Área de trabajo OMS**
  - **Suscripción**
  - **Grupo de recursos**
  - **Ubicación**
  - **Nivel de precios**  
    ![Módulo OMS](./media/log-analytics-sccm/sccm-azure02.png)  

    >[AZURE.NOTE] El ejemplo anterior, crea un nuevo grupo de recursos. El grupo de recursos solo se usa para proporcionar el Administrador de configuración con permisos para el área de trabajo OMS en este ejemplo.

4. Haga clic en **Examinar** > **grupos de recursos** para abrir el módulo de **grupos de recursos** .
5. En el módulo de **grupos de recursos** , haga clic en el grupo de recursos que acaba de crear para abrir el &lt;nombre del grupo de recursos&gt; módulo de configuración.  
  ![módulo de configuración de grupo de recursos](./media/log-analytics-sccm/sccm-azure03.png)
6. En la &lt;nombre del grupo de recursos&gt; módulo de configuración, haga clic en control de acceso (IAM) para abrir el &lt;nombre del grupo de recursos&gt; módulo de los usuarios.  
  ![módulo de los usuarios del grupo de recursos](./media/log-analytics-sccm/sccm-azure04.png)  
7. En la &lt;nombre del grupo de recursos&gt; módulo de usuarios, haga clic en **Agregar** para abrir el módulo de **access agregar** .
8. En el módulo de **access agregar** , haga clic en **Seleccionar una función**y, a continuación, seleccione el rol de **Colaborador** .  
  ![Seleccione una función](./media/log-analytics-sccm/sccm-azure05.png)  
9. Haga clic en **Agregar usuarios**, seleccione el usuario Administrador de configuración, haga clic en **Seleccionar**y, a continuación, haga clic en **Aceptar**.  
  ![Agregar usuarios](./media/log-analytics-sccm/sccm-azure06.png)  


## <a name="add-an-oms-connection-to-configuration-manager"></a>Agregar una conexión de OMS para el Administrador de configuración

Para agregar una conexión de OMS, el Administrador de configuración de entorno debe tener un [punto de conexión de servicio](https://technet.microsoft.com/library/mt627781.aspx) configuradas para el modo en línea.

1. En el área de trabajo de **administración** del Administrador de configuración, seleccione **OMS conector**. Se abre el **Asistente para agregar conexiones de OMS**. Seleccione **siguiente**.

2. En la pantalla **General** , confirme que ha realizado las siguientes acciones y ver detalles de cada elemento y luego seleccione **siguiente**.
  1. En el Portal de administración de Azure, ha registrado Administrador de configuración como una aplicación de la aplicación Web o de Web API y que tiene el [identificador de cliente desde el registro](../active-directory/active-directory-integrating-applications.md).
  2. En el Portal de administración de Azure que haya creado una clave de aplicación para la aplicación registrada en Azure Active Directory.  
  3. En el Portal de administración de Azure, ha proporcionado la aplicación web registrado con permiso de acceso a OMS.  
  ![Conexión a la página de OMS generales del Asistente](./media/log-analytics-sccm/sccm-console-general01.png)

3. En la pantalla de **Azure Active Directory** , configure la conexión a OMS proporcionando el **inquilino** , el **Identificador de cliente** y la **Clave de secreto de cliente** y seleccione **siguiente**.  
  ![Conexión a la página de OMS Asistente Azure Active Directory](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)

4. Si ha realizado todos los procedimientos correctamente, a continuación, la información de la pantalla de **Configuración de la conexión de OMS** aparecerán automáticamente en esta página. Información de la configuración de conexión debe aparecer para su **suscripción de Azure** , el **grupo de recursos de Azure** y el **Área de trabajo del conjunto de aplicaciones de administración de operaciones**.  
  ![Conexión a la página OMS Asistente OMS conexión](./media/log-analytics-sccm/sccm-wizard-configure04.png)

5. El asistente se conecta al servicio de OMS con la información que ha introducido. Seleccione las colecciones de dispositivo que desea sincronizar con OMS y, a continuación, haga clic en **Agregar**.  
  ![Seleccione colecciones](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)

6. Comprobar la configuración de conexión en la pantalla de **Resumen** y luego seleccione **siguiente**. La pantalla de **progreso** muestra el estado de conexión y luego debe **completar**.

>[AZURE.NOTE] OMS debe conectarse al sitio de nivel superior en la jerarquía. Si se conecta OMS a un sitio principal de independiente y, a continuación, agregar un sitio de administración central para su entorno, debe eliminar y volver a crear la conexión de OMS dentro de la nueva jerarquía.

Después de que el Administrador de configuración que ha vinculado a OMS, puede agregar o quitar colecciones y ver las propiedades de la conexión de OMS.

## <a name="update-oms-connection-properties"></a>Actualizar propiedades de conexión de OMS

Si una clave de cliente o contraseña nunca expira o se pierde, deberá actualizar manualmente las propiedades de conexión de OMS.

1. En el Administrador de configuración, vaya a **Servicios en la nube** y luego seleccione **OMS conector** para abrir la página de **Propiedades de conexión de OMS** .
2. En esta página, haga clic en la ficha de **Azure Active Directory** para ver su **inquilino**, **ID de cliente**, **caducidad de la clave secreta cliente**. **Comprobar** la **clave de cliente** si ha caducado.


## <a name="download-and-install-the-agent"></a>Descargar e instalar el agente

1. En el portal OMS, [Descargue el archivo de configuración de agente de OMS](log-analytics-windows-agents.md#download-the-agent-setup-file-from-oms).
2. Instalar y configurar al agente de en el equipo que ejecuta el rol de administrador de configuración servicio conexión punto sitio sistema, use uno de los métodos siguientes:
  - [Instalar al agente mediante el programa de instalación](log-analytics-windows-agents.md#install-the-agent-using-setup)
  - [Instalar al agente mediante la línea de comandos](log-analytics-windows-agents.md#install-the-agent-using-the-command-line)
  - [Instalar al agente mediante DSC en automatización de Azure](log-analytics-windows-agents.md#install-the-agent-using-dsc-in-azure-automation)


## <a name="import-collections"></a>Colecciones de importación

Después de haber agregado al administrador de configuración de una conexión de OMS e instalado el agente en el equipo que ejecuta la conexión de servicio del Administrador de configuración, elija función del sistema del sitio, el siguiente paso es importar colecciones desde el Administrador de configuración de OMS como grupos de equipos.

Después de habilita la importación, se recupera la información de pertenencia colección cada tres horas para mantener la pertenencia a la colección actual. Puede deshabilitar la importación en cualquier momento.

1. En el portal OMS, haga clic en **configuración**.
2. Haga clic en la ficha **Grupos de equipo** y, a continuación, haga clic en la pestaña **SCCM** .
3. Seleccione la **pertenencia a la colección de administrador de configuración de importación** y, a continuación, haga clic en **Guardar**.  
  ![Grupos de equipos - pestaña SCCM](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Ver datos desde el Administrador de configuración

Después de que ha agregado una conexión de OMS para el Administrador de configuración e instalado al agente en el equipo que ejecuta el rol de administrador de configuración servicio conexión punto sitio sistema, se envían datos desde el agente a OMS. En OMS, las colecciones de administrador de configuración aparecen como [grupos de equipos](log-analytics-computer-groups.md). Puede ver los grupos de la página del **Administrador de configuración** en **Grupos de equipo** en la **configuración**.

Después de importarán las colecciones, puede ver se detectaron cuántos equipos con la pertenencia a la colección. También puede ver el número de colecciones que se han importado.

![Grupos de equipos - pestaña SCCM](./media/log-analytics-sccm/sccm-computer-groups02.png)

Al hacer clic en cualquiera de ellos, búsqueda se abrirá en todos los grupos importados o todos los equipos que pertenecen a cada grupo. Con la [Búsqueda de registro](log-analytics-log-searches.md), puede iniciar un análisis detallado de datos del Administrador de configuración.

## <a name="next-steps"></a>Pasos siguientes

- Use [La búsqueda de registro](log-analytics-log-searches.md) para ver información detallada sobre los datos del Administrador de configuración.
