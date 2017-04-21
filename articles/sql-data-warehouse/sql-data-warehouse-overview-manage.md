<properties
   pageTitle="Administrar bases de datos en el almacén de datos de SQL Azure | Microsoft Azure"
   description="Descripción general de la administración de bases de datos de almacén de datos de SQL. Incluye herramientas de administración, DWUs y rendimiento de escalado, solución de problemas de rendimiento de la consulta, establecer directivas de seguridad y restaurar una base de datos de daños en los datos o de una interrupción regional."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="barbkess;sonyama;"/>

# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Administrar bases de datos en el almacén de datos de SQL Azure

Almacén de datos SQL automatiza muchos aspectos de la administración de las bases de datos. Por ejemplo, para ajustar el rendimiento solo necesita ajustar y pagar el nivel adecuado de recursos de cálculo y, a continuación, permitir el almacén de datos de SQL hacer todo el trabajo de escalado y escalar. 

Sin duda deseará supervisar la carga de trabajo para identificar las necesidades de rendimiento, así como para solucionar problemas de consultas de larga ejecución. También debe realizar algunas tareas de seguridad para administrar permisos para usuarios y los inicios de sesión.

Esta información general describen estos aspectos de la administración de almacén de datos de SQL.

- Herramientas de administración
- Cálculo de escala
- Pausar y reanudar
- Prácticas recomendadas de rendimiento
- Supervisión de consulta
- Seguridad
- Copia de seguridad y restauración

## <a name="management-tools"></a>Herramientas de administración

Puede usar una amplia variedad de herramientas para administrar bases de datos en el almacén de datos de SQL. Administrar bases de datos, desarrollará preferencias de la herramienta para cada tipo de tarea que necesita realizar.

### <a name="azure-portal"></a>Portal de Azure
El [portal de Azure][] es un portal basado en web donde puede crear, actualizar y eliminar las bases de datos y supervisar los recursos de la base de datos. Esta herramienta es ideal si está comenzando a con Azure, administrar un pequeño número de bases de datos, o necesita rápidamente hacer algo.

Para empezar con el portal de Azure, consulte [crear un almacén de datos de SQL (portal de Azure)][].

### <a name="sql-server-data-tools-in-visual-studio"></a>Herramientas de datos SQL Server en Visual Studio
[Herramientas de datos SQL Server][] (SSDT) en Visual Studio permite conectar, administrar y desarrollar las bases de datos. Si es programador familiarizado con otros entornos de desarrollo integrado (IDE) o de Visual Studio, intente usar SSDT en Visual Studio.

SSDT incluye el Explorador de objetos de SQL Server que le permite visualizar, conectar y ejecutar secuencias de comandos en bases de datos de almacén de datos de SQL. Para conectar rápidamente al almacén de datos de SQL, se puede hacer clic en el botón **Abrir en Visual Studio** en la barra de comandos al ver los detalles de la base de datos en el Portal de clásico de Azure.  

Para empezar con SSDT en Visual Studio, vea [Almacén de datos de SQL de consulta Azure con Visual Studio][].

### <a name="command-line-tools"></a>Herramientas de línea de comandos
Herramientas de línea de comandos son ideales para automatizar las cargas de trabajo.  PowerShell y sqlcmd son dos excelentes formas para automatizar los procesos.  Se recomienda estas herramientas para administrar un gran número de servidores lógicos e implementar los cambios de los recursos en un entorno de producción como las tareas necesarias pueden secuencias de comandos y luego automatizadas.

### <a name="dynamic-management-views"></a>Vistas dinámicas de administración 

DMV es la base de la administración de almacén de datos de SQL. Casi toda la información que en el portal se basa en DMV. Para ver una lista de DMV de almacén de datos de SQL, vea [vistas del sistema de almacenamiento de datos de SQL][].

Para obtener más información, vea [Conectar y consulta con sqlcmd][]y [crear una base de datos (PowerShell)][].

## <a name="scale-compute"></a>Cálculo de escala

En el almacén de datos de SQL, puede escalar rápidamente rendimiento out o por crecientes o decrecientes recursos de cálculo de CPU, memoria y ancho de banda de i/OS. Para ajustar el rendimiento, todo lo que debe hacer es ajustar el número de unidades de almacén de datos (DWUs) que asigna el almacén de datos de SQL a la base de datos. Almacén de datos SQL rápidamente realiza el cambio y controla todos los cambios subyacentes a hardware y software.

Para obtener más información sobre la escala DWUs, vea [ajustar el rendimiento][].

##  <a name="pause-and-resume"></a>Pausar y reanudar

Para guardar los costos, puede pausar y reanudar cálculo recursos a petición. Por ejemplo, si no usa la base de datos durante la noche y los fines de semana, puede pausar durante los momentos y reanudar durante el día. No se cobrarán para DWUs mientras está en pausa la base de datos.

Para obtener más información, consulte [Pausar calcular][]y [calcular currículo][].

## <a name="performance-best-practices"></a>Prácticas recomendadas de rendimiento

Al comenzar con una nueva tecnología, descubrir las sugerencias y trucos que funcionan mejor directamente desde el principio puede guardar una gran cantidad de tiempo.  Encontrará las mejores prácticas durante muchos de nuestros temas.

Para ver muchas un resumen de las consideraciones más importantes al desarrollar su carga de trabajo, consulte [Prácticas recomendadas de almacén de datos de SQL][].

## <a name="query-monitoring"></a>Supervisión de consulta

A veces se ejecuta una consulta demasiado larga, pero no está seguro de cuál es el culpable. Almacén de datos de SQL tiene vistas de administración dinámica (DMV) que puede usar para averiguar qué consulta dura demasiada. 

Para buscar consultas de larga duración, vea [supervisar la carga de trabajo con DMV][].

## <a name="security"></a>Seguridad

Para mantener un sistema seguro, debe estar en la alerta y protegerse de cualquier tipo de acceso no autorizado. Un sistema de seguridad debe asegurarse de que las reglas de firewall están en lugar de autorización de manera que solo pueden conectarse a direcciones IP. Necesita la autenticación apropiada de credenciales de usuario. Después de que un usuario está conectado a la base de datos, el usuario solo debe tener permisos para realizar un número mínimo de acciones. Para proteger los datos, puede utilizar el cifrado. También es importante tener auditoría y seguimiento para volver a trazar eventos si hay cualquier actividad sospechosa.

Para obtener información sobre la administración de seguridad, cabeza sobre la [Introducción a la seguridad][].

## <a name="backup-and-restore"></a>Copia de seguridad y restauración

Tener backps confiable de los datos es una parte esencial de cualquier base de datos de producción. Almacén de datos SQL protege los datos por automáticamente una copia de seguridad de las bases de datos activas a intervalos regulares. Estas copias de seguridad le permiten recuperar de escenarios donde ha dañado los datos o se eliminan accidentalmente los datos o la base de datos.  Para la programación de copia de seguridad de datos, directiva de retención y cómo restaurar una base de datos, vea [Restaurar a partir de instantánea][].

## <a name="next-steps"></a>Pasos siguientes
Uso de diseño de base de datos buena principios le será más fácil de administrar las bases de datos en el almacén de datos de SQL. Para obtener más, cabeza sobre la [Introducción al desarrollo][].

<!--Image references-->

<!--Article references-->
[Crear un almacén de datos SQL (Portal de Azure)]: sql-data-warehouse-get-started-provision.md
[Crear una base de datos (PowerShell)]: sql-data-warehouse-get-started-provision-powershell
[connection]: sql-data-warehouse-develop-connections.md
[Almacén de datos de consulta SQL Azure con Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Conectarse y consultar con sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Introducción al desarrollo]: sql-data-warehouse-overview-develop.md
[Supervisar la carga de trabajo con DMV]: sql-data-warehouse-manage-monitor.md
[Cálculo de pausa]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Restaurar a partir de instantánea]: sql-data-warehouse-restore-database-overview.md
[Cálculo de currículo]: sql-data-warehouse-manage-compute-overview.md#resume-compute-performance-bk
[Ajustar el rendimiento]: sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[Información general de seguridad]: sql-data-warehouse-overview-manage-security.md
[Prácticas recomendadas de almacén de datos SQL]: sql-data-warehouse-best-practices.md
[Vistas del sistema de almacenamiento de datos de SQL]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[Herramientas de datos SQL Server]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Portal de Azure]: http://portal.azure.com/
