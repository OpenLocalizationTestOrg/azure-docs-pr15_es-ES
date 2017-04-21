<properties
   pageTitle="Auditoría en el almacén de datos SQL Azure | Microsoft Azure"
   description="Introducción a la auditoría en el almacén de datos de SQL Azure"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="auditing-in-azure-sql-data-warehouse"></a>Auditoría en el almacén de datos SQL Azure

> [AZURE.SELECTOR]
- [Auditoría](sql-data-warehouse-auditing-overview.md)
- [Detección de amenazas](sql-data-warehouse-security-threat-detection.md)

Almacén de datos SQL de auditoría le permite al registro de eventos de la base de datos a una auditoría inicie sesión en su cuenta de almacenamiento de Azure. Auditoría puede ayudarle a mantener el cumplimiento de reglamentaciones, comprender la actividad de la base de datos y obtenga información sobre discrepancias y anomalías que podrían indicar cuestiones empresariales o sospechosos violaciones de seguridad. También el almacén de datos SQL de auditoría se integra con Microsoft Power BI para explorar en profundidad informes y análisis.

Herramientas de auditoría habilitar y facilitar estándares de cumplimiento pero no garantizar el cumplimiento. Para obtener más información acerca de los programas de Azure que son compatibles con el cumplimiento de estándares, consulte el <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centro de confianza de Azure</a>.

+ [Conceptos básicos de auditoría de base de datos]
+ [Configurar la auditoría de la base de datos]
+ [Analizar los registros de auditoría e informes]

##<a id="subheading-1"></a>Conceptos básicos de auditoría de base de datos de almacén de datos de SQL Azure


Auditoría de base de datos de almacén de datos de SQL le permite:

- **Conservar** un rastro de auditoría de los eventos seleccionados. Puede definir categorías de acciones de la base de datos para auditoría.
- **Informe** sobre la actividad de la base de datos. Puede usar informes preconfigurados y un panel para empezar rápidamente con la actividad y los informes de eventos.
- **Analizar** informes. Puede encontrar tendencias, actividad inusual y eventos sospechosos.

Puede configurar la auditoría en las siguientes categorías de evento:

**SQL sencilla** y **SQL parametrizadas** para que los registros de auditoría recopilada se clasifican como  

- **Acceso a datos**
- **Cambios de esquema (DDL)**
- **Cambios de datos (DML)**
- **Cuentas, roles y permisos (DCL)**
- **Procedimiento almacenado**, **Inicio de sesión** y **administración de transacciones**.

Para cada categoría de evento de auditoría de **éxito** y las operaciones de **error** se configuran por separado.

Para obtener más información sobre las actividades y eventos de auditoría, vea la <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Referencia de formato de registro de auditoría (descarga de archivos de documento)</a>.

Registros de auditoría se almacenan en su cuenta de almacenamiento de Azure. Puede definir un período de retención de registro de auditoría.

Puede definir una directiva de auditoría para una base de datos o como una directiva de servidor predeterminado. Una directiva de auditoría del servidor predeterminado se aplicará a todas las bases de datos en un servidor que no tiene un reemplazo base de datos directivas de auditorías específicas definidas por el.

Antes de establecer arriba auditoría verificación auditoría si está utilizando un ["con clientes"](sql-data-warehouse-auditing-downlevel-clients.md).


##<a id="subheading-2"></a>Configurar la auditoría de la base de datos

1. Inicie el <a href="https://portal.azure.com" target="_blank">Portal de Azure</a>.

2. Vaya a la hoja de configuración de la base de datos de almacén de datos SQL o SQL Server que quiera auditar. Haga clic en el botón **configuración** en la parte superior y, a continuación, en el módulo de configuración y a continuación, seleccione **auditoría**.

    ![][1]

3. En el módulo de configuración de auditoría, en primer lugar, desactive la casilla de verificación **Heredar configuración de auditoría del servidor** . Esto le permite especificar la configuración de una base de datos determinado.

    ![][2]

4. A continuación, habilitar la auditoría haciendo clic en el botón **en** .

    ![][3]

5. En el módulo de configuración de auditoría, seleccione **Detalles de almacenamiento** para abrir el módulo de almacenamiento de registros de auditoría. Seleccione la cuenta de almacenamiento de Azure donde se guardarán los registros y el período de retención. **Sugerencia:** Use la misma cuenta de almacenamiento para todas las bases de datos de auditoría para obtener el máximo provecho de las plantillas de informes preconfigurados.

    ![][4]

6. Haga clic en el botón **Aceptar** para guardar los detalles de configuración de almacenamiento.


7. En **El registro por evento**, haga clic en **correcto** y **erróneo** para registrar todos los eventos o elija categorías de eventos individuales.


8. Si está configurando auditoría para una base de datos, debe modificar la cadena de conexión de cliente para asegurarse de datos de auditoría se genera correctamente. Consulte el tema [Modificar FDQN de servidor en la cadena de conexión](sql-data-warehouse-auditing-downlevel-clients.md) para conexiones de cliente de nivel inferior.

9. Haga clic en **Aceptar**.


##<a id="subheading-3">Analizar los registros de auditoría e informes</a>

Registros de auditoría se agregan en una colección de tablas de tienda con un prefijo **SQLDBAuditLogs** en la cuenta de almacenamiento de Azure elegida durante la instalación. Puede ver los archivos de registro mediante una herramienta como <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">El Explorador de almacenamiento de Azure</a>.

Una plantilla de informe de panel preconfigurados está disponible como una <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">hoja de cálculo de Excel descargable</a> para ayudarle a analizar rápidamente los datos del registro. Para usar la plantilla en los registros de auditoría, necesita Excel 2013 o posterior y Power Query, que puede descargar <a href="http://www.microsoft.com/download/details.aspx?id=39379">aquí</a>.

La plantilla contiene los datos de ejemplo ficticio y puede configurar Power Query para importar el registro de auditoría directamente desde su cuenta de almacenamiento de Azure.

Para obtener instrucciones detalladas sobre cómo trabajar con la plantilla de informe, lea <a href="http://go.microsoft.com/fwlink/?LinkId=506731">Cómo a (descarga de documento)</a>.

![][5]


##<a id="subheading-4">Procedimientos recomendados para uso en producción</a>
La descripción de esta sección se refiere a las capturas de pantalla anterior. Puede utilizar el <a href="https://portal.azure.com" target="_blank">Portal de Azure</a> o <a href= "https://manage.windowsazure.com/" target="_bank">Clásica Portal clásico de Azure</a> .


##<a id="subheading-5"></a>Regeneración de clave de almacenamiento

En producción es probable que actualizar las claves de almacenamiento periódicamente. Al actualizar las claves necesita volver a guardar la directiva. El proceso es como sigue:.


1. En el módulo de configuración de auditoría (descrito anteriormente en el conjunto de la sección de auditoría) cambiar la **Tecla de acceso de almacenamiento** de *principal* *secundario* y **Guardar**.
![][4]
2. Vaya a la hoja de configuración de almacenamiento y **regenerar** la *Clave principal de Access*.

3. Vaya al módulo de configuración de auditoría, cambie la **Tecla de acceso de almacenamiento** de *secundaria* a *principal* y pulse **Guardar**.

4. Volver a la interfaz de usuario de almacenamiento y **regenerar** la *Clave de acceso secundaria* (como preparación para el siguiente ciclo de actualización de claves.

##<a id="subheading-6"></a>Automatización
Existen varios cmdlets de PowerShell que puede usar para configurar la auditoría en la base de datos de SQL Azure. Para obtener acceso a los cmdlets de auditoría debe ejecutar PowerShell en modo de administrador de recursos de Azure.

> [AZURE.NOTE] El módulo de [Administrador de recursos de Azure](https://msdn.microsoft.com/library/dn654592.aspx) está en vista previa. No pueden proporcionar las mismas capacidades de administración que el módulo Azure.

Cuando esté en modo de administrador de recursos de Azure, ejecute `Get-Command *AzureSql*` para obtener una lista de los cmdlets disponibles.


<!--Anchors-->
[Conceptos básicos de auditoría de base de datos]: #subheading-1
[Configurar la auditoría de la base de datos]: #subheading-2
[Analizar los registros de auditoría e informes]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->
