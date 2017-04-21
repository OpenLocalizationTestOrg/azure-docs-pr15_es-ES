<properties
    pageTitle="Estirar información general de la base de datos | Microsoft Azure"
    description="Obtenga información sobre cómo expandir base de datos migra los datos fríos transparente y de forma segura en la nube de Microsoft Azure."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="stretch-database-overview"></a>Estirar información general de la base de datos

Base de datos extendido migre los datos fríos transparente y de forma segura en la nube de Microsoft Azure.

Si desea empezar a trabajar con bases de datos de estirar inmediatamente, vea [Introducción al ejecutar la base de datos habilitar estirar Asistente para](sql-server-stretch-database-wizard.md).

## <a name="what-are-the-benefits-of-stretch-database"></a>¿Cuáles son las ventajas de la base de datos de estirar?
Ajuste de la base de datos proporciona las siguientes ventajas:

### <a name="provides-cost-effective-availability-for-cold-data"></a>Proporciona costo\-disponibilidad eficaces para datos inactivos
Estirar frías y calientes transacciones dinámicamente datos desde SQL Server a Microsoft Azure con la base de datos de SQL Server estirar. A diferencia de almacenamiento de datos fría típico, los datos siempre están en línea y disponible a la consulta. Puede proporcionar más escalas de tiempo de retención de datos sin interrumpir el banco para tablas de gran tamaño como histórico pedidos de cliente. Beneficiarse de bajo coste de Azure en lugar de costosas en\-locales de almacenamiento. Elija el nivel de precios y configurar las opciones en el Portal de Azure para mantener el control de costos. Escalar hacia arriba o hacia abajo, según sea necesario. Visite la página de [Precios de base de datos de SQL Server estirar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) para obtener información detallada.

### <a name="doesnt-require-changes-to-queries-or-applications"></a>No requiere cambios en las consultas o las aplicaciones
Obtener acceso a los datos de SQL Server sin problemas con independencia de si se trata\-locales o se ajusta a la nube.  Establecer la directiva que determina dónde se almacenan los datos y SQL Server controla el movimiento de datos en segundo plano. Toda la tabla está siempre en línea y puede consultar. Y, estirar base de datos no requiere cambios en las consultas existentes o aplicaciones: la ubicación de los datos es completamente transparente para la aplicación.

### <a name="streamlines-on-premises-data-maintenance"></a>Simplifica en\-locales de mantenimiento de datos
Reducir en\-locales de mantenimiento y almacenamiento para sus datos. Copias de seguridad de sus en\-local datos ejecutan más rápidamente y finalización en la ventana de mantenimiento. Copias de seguridad de la parte de la nube de los datos se ejecutan automáticamente. Su en\-reducción de necesidades de almacenamiento local. Almacenamiento de Azure puede ser menor que agregar a activado al 80%\-locales SSD.

### <a name="keeps-your-data-secure-even-during-migration"></a>Conserva los datos seguros incluso durante la migración
Disfrute de tranquilidad como estirar las aplicaciones más importantes de forma segura en la nube. Siempre cifrados SQL Server proporciona cifrado de sus datos en movimiento. Fila nivel de seguridad y otras características avanzadas de seguridad de SQL Server también trabajan con la base de datos de estirar para proteger los datos.

## <a name="what-does-stretch-database-do"></a>¿Qué hace estiramiento de base de datos?
Después de habilitar estiramiento de base de datos para una instancia de SQL Server, una base de datos y al menos una tabla, base de datos de estirar silenciosamente comienza a migrar los datos y a Azure.

-   Si los datos y se almacenan en una tabla independiente, puede migrar toda la tabla.

-   Si la tabla contiene datos frías y caliente, puede especificar una función de filtro para seleccionar las filas que se van a migrar.

**No tienes que cambiar consultas existentes y aplicaciones de cliente.** Sigues teniendo acceso transparente a los datos locales y remotos, incluso durante la migración de datos. Hay una cantidad pequeña de latencia de consultas remotas, pero solo encontrará esta latencia al consultar los datos fríos.

**Que base de datos de estirar garantiza que no se pierden datos** si se produce un error durante la migración. También tiene lógica de reintento para tratar problemas de conexión que se pueden producir durante la migración. Una vista de administración dinámica proporciona el estado de la migración.

**Pausar la migración de datos** para solucionar problemas en el servidor local o para maximizar el ancho de banda de red disponible.

![Información general de la base de datos extendido][StretchOverviewImage1]

## <a name="is-stretch-database-for-you"></a>¿Es la base de datos de estirar para usted?
Si puede realizar las siguientes afirmaciones, estirar base de datos puede ayudar a satisfacer sus necesidades y resolver los problemas.

|Si es una toma de decisiones|Si es un administrador|
|------------------------------|-------------------|
|Tengo que mantener los datos de transacciones por mucho tiempo.|El tamaño de las tablas se sacar partido de control.|
|A veces se debe consultar los datos fríos.|Mis usuarios dicen que quieren acceder a los datos inactivos, pero solo casi nunca se utilizan.|
|Tengo aplicaciones, incluidas aplicaciones anteriores, que no desee actualizar.|Tengo que mantener adquiriendo y agregar más espacio de almacenamiento.|
|Deseo encontrar una manera de ahorrar dinero en almacenamiento.|No puedo hacer copia de seguridad o restaurar como tablas de gran tamaño en el contrato SLA.|

## <a name="what-kind-of-databases-and-tables-are-candidates-for-stretch-database"></a>¿Qué tipo de bases de datos y tablas son candidatos para expandir base de datos?
Estirar destinos transacciones bases de datos con grandes cantidades de datos inactivos, que normalmente se almacenan en un pequeño número de tablas. Estas tablas pueden contener más de un millones de filas.

Si usa la característica de tabla temporal de SQL Server 2016, usar la base de datos de estirar para migrar todo o parte de la tabla de historial asociado a costo\-eficaz de almacenamiento en Azure. Para obtener más información, vea [Administrar retención de datos históricos en una versión sistema tablas temporales](https://msdn.microsoft.com/library/mt637341.aspx).

Use estirar Asesor de base de datos, una característica de SQL Server 2016 asesor, identificar bases de datos y tablas de base de datos de estirar. Para obtener más información, consulte [Identificar bases de datos y las tablas de base de datos de estirar](sql-server-stretch-database-identify-databases.md). Para obtener más información sobre posibles problemas de bloqueo, vea [limitaciones para expandir base de datos](sql-server-stretch-database-limitations.md).

## <a name="test-drive-stretch-database"></a>Versión de prueba de base de datos de estirar
**Prueba estiramiento de base de datos con la base de datos de ejemplo AdventureWorks.** Para obtener la base de datos de ejemplo AdventureWorks, descargue al menos el archivo de base de datos y el archivo de secuencias de comandos y ejemplos desde [aquí](https://www.microsoft.com/download/details.aspx?id=49502). Después de restaurar la base de datos de ejemplo en una instancia de SQL Server 2016, descomprima el archivo ejemplos y abra el archivo de estirar DB ejemplos de la carpeta DB estirar. Ejecutar las secuencias de comandos de este archivo para comprobar el espacio utilizado por los datos antes y después de habilitar la base de datos de estirar, para realizar un seguimiento del progreso de la migración de datos y confirme puede seguir consultar los datos existentes e insertar los nuevos datos durante y después de la migración de datos.

## <a name="next-step"></a>Siguiente paso
**Identificar las bases de datos y tablas que son candidatos para expandir base de datos.** Descargar Asesor de actualización de SQL Server 2016 y ejecute el Asesor de base de datos de estirar para identificar las bases de datos y tablas que son candidatos para expandir base de datos. Estirar Asesor de base de datos también identifica los problemas de bloqueo. Para obtener más información, consulte [Identificar bases de datos y las tablas de base de datos de estirar](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png
