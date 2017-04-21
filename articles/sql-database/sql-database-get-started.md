<properties
    pageTitle="Tutorial de base de datos SQL: crear una base de datos SQL | Microsoft Azure"
    description="Obtenga información sobre cómo configurar un servidor de base de datos SQL lógico, regla de firewall del servidor, base de datos SQL y datos de ejemplo. Además, aprenda a conectar con las herramientas cliente, configurar usuarios y configurar una regla de firewall de la base de datos."
    keywords="tutorial de base de datos de SQL, crear una base de datos de sql"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/07/2016"
    ms.author="carlrab"/>


# <a name="sql-database-tutorial-create-a-sql-database-in-minutes-by-using-the-azure-portal"></a>Tutorial de base de datos SQL: crear una base de datos SQL en minutos mediante el portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

En este tutorial, aprenderá a usar el portal de Azure para:

- Crear una base de datos de SQL Azure con datos de ejemplo.
- Crear una regla de firewall de nivel de servidor para una única dirección IP o para un intervalo de direcciones IP.

Puede realizar estas tareas mediante [C#](sql-database-get-started-csharp.md) o [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

## <a name="create-your-first-azure-sql-database"></a>Crear la primera base de datos de SQL Azure 

1. Si actualmente no está conectado, conecte el [portal de Azure](http://portal.azure.com).
2. Haga clic en **nuevo**, haga clic en **datos + almacenamiento**y, a continuación, busque la **Base de datos de SQL**.

    ![Nueva base de datos de sql 1](./media/sql-database-get-started/sql-database-new-database-1.png)

3. Haga clic en **Base de datos SQL** para abrir el módulo de base de datos de SQL. El contenido de este módulo varía según el número de las suscripciones y los objetos existentes (como servidores existentes).

    ![Nueva base de datos de sql 2](./media/sql-database-get-started/sql-database-new-database-2.png)

4. En el cuadro de texto **nombre de la base de datos** , proporcione un nombre para la primera base de datos - como "mi base de datos". Una marca de verificación verde indica que ha proporcionado un nombre válido.

    ![Nueva base de datos de sql 3](./media/sql-database-get-started/sql-database-new-database-3.png)

5. Si tiene varias suscripciones, seleccione una suscripción.
6. En **el grupo de recursos**, haga clic en **Crear nuevo** y proporcione un nombre para el primer grupo de recursos - como "Mi de grupo de recursos". Una marca de verificación verde indica que ha proporcionado un nombre válido.

    ![Nueva base de datos de sql 4](./media/sql-database-get-started/sql-database-new-database-4.png)

7. En **Seleccionar origen**, haga clic en la **muestra** y haga clic en **AdventureWorksLT [V12]**en **Seleccionar ejemplo** .

    ![Nueva base de datos de sql 5](./media/sql-database-get-started/sql-database-new-database-5.png)

8. En **servidor**, haga clic en **Configurar la configuración necesaria**.

    ![Nueva base de datos de sql 6](./media/sql-database-get-started/sql-database-new-database-6.png)

9. En el módulo de servidor, haga clic en **crear un nuevo servidor**. Se crea una base de datos de SQL Azure dentro de un objeto de servidor, que puede ser un servidor nuevo o en un servidor existente.

    ![Nueva base de datos de sql 7](./media/sql-database-get-started/sql-database-new-database-7.png)

10. Revise el módulo de **servidor de nuevo** para comprender la información que necesita para proporcionar a su servidor nuevo.

    ![Nueva base de datos de sql 8](./media/sql-database-get-started/sql-database-new-database-8.png)

11. En el cuadro de texto **nombre de servidor** , proporcione un nombre para el primer servidor - como "Mi nuevo-server-objeto". Una marca de verificación verde indica que ha proporcionado un nombre válido.

    ![Nueva base de datos de sql 9](./media/sql-database-get-started/sql-database-new-database-9.png)
 
12. En el **Inicio de sesión de administración de servidor**, proporcione un nombre de usuario para el inicio de sesión de administrador para este servidor, como "-administración-"Mi cuenta. Este inicio de sesión se conoce como el inicio de sesión principal de servidor. Una marca de verificación verde indica que ha proporcionado un nombre válido.

    ![Nueva base de datos de sql 10](./media/sql-database-get-started/sql-database-new-database-10.png)

13. En **contraseña** y **Confirmar contraseña**, proporcione una contraseña para el servidor de cuenta de inicio de sesión principal - como "p@ssw0rd1". Una marca de verificación verde indica que ha proporcionado una contraseña válida.

    ![Nueva base de datos de sql 11](./media/sql-database-get-started/sql-database-new-database-11.png)
 
14. En **ubicación**, seleccione un centro de datos apropiado para su ubicación - como "Australia Oriental".

    ![Nueva base de datos de sql 12](./media/sql-database-get-started/sql-database-new-database-12.png)

15. En ** V12 crear servidor (la actualización más reciente), observe que solo tiene la opción de crear una versión actual del servidor de SQL Azure.

    ![Nueva base de datos de sql 13](./media/sql-database-get-started/sql-database-new-database-13.png)

16. Observe que, de forma predeterminada, la casilla de verificación para los **Servicios de azure permitir acceso a servidor** está seleccionada y no se puede cambiar aquí. Esta es una opción avanzada. Puede cambiar esta configuración en la configuración de firewall de servidor para este objeto de servidor, aunque para la mayoría de los escenarios no es necesario.

    ![Nueva base de datos de sql 14](./media/sql-database-get-started/sql-database-new-database-14.png)

17. En el nuevo módulo de servidor, revise las selecciones y, a continuación, haga clic en **Seleccionar** para seleccionar el servidor nuevo para la nueva base de datos.

    ![Nueva base de datos de sql 15](./media/sql-database-get-started/sql-database-new-database-15.png)

18. En el módulo de base de datos de SQL, en el **nivel de precios**, haga clic en **S2 estándar** y, a continuación, haga clic en **básica** para elegir el nivel de precio económico para su primera base de datos. Siempre puede cambiar el nivel de precios más adelante.

    ![Nueva base de datos de sql 16](./media/sql-database-get-started/sql-database-new-database-16.png)

19. En el módulo de la base de datos de SQL, revise las selecciones y, a continuación, haga clic en **crear** para crear su primer servidor y base de datos. Se validarán los valores que proporcionan y se inicia la implementación.

    ![Nueva base de datos de sql 17](./media/sql-database-get-started/sql-database-new-database-17.png)

20. En el portal de herramientas, haga clic en los elementos de **notificaciones** para comprobar el estado de la implementación.

    ![Nueva base de datos de sql 18](./media/sql-database-get-started/sql-database-new-database-18.png)

>[AZURE.IMPORTANT]Cuando se complete la implementación, el nuevo servidor de SQL Azure y la base de datos se crean en Azure. No podrá conectarse a su nuevo servidor o base de datos con herramientas de SQL Server hasta que haya creado una regla de firewall del servidor para abrir el firewall de la base de datos SQL para conexiones desde fuera de Azure.

[AZURE.INCLUDE [Create server firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha completado este tutorial de base de datos SQL y crea una base de datos con algunos datos de ejemplo, ya está listo para explorar con las herramientas de favoritas.

- Si ya está familiarizado con Transact-SQL y SQL Server Management Studio (SSMS), obtenga información sobre cómo [Conectar y consulta una base de datos SQL con SSMS](sql-database-connect-query-ssms.md).

- Si conoce Excel, obtenga información sobre cómo [conectarse a una base de datos SQL Azure con Excel](sql-database-connect-excel.md).

- Si está listo para comenzar a codificar, elija el lenguaje de programación en [las bibliotecas de conexiones de base de datos de SQL y SQL Server](sql-database-libraries.md).

- Si desea mover las bases de datos de SQL Server local a Azure, consulte [migrar una base de datos a la base de datos de SQL](sql-database-cloud-migrate.md) para obtener más información.

- Si desea cargar algunos datos en una nueva tabla desde un archivo CSV con la herramienta de línea de comandos BCP, vea [cargar datos en la base de datos de SQL desde un archivo CSV con BCP](sql-database-load-from-csv-with-bcp.md).

- Si desea iniciar la exploración de seguridad de base de datos de SQL Azure, vea [Introducción a la seguridad](sql-database-get-started-security.md)


## <a name="additional-resources"></a>Recursos adicionales

[¿Qué es la base de datos SQL?](sql-database-technical-overview.md)
