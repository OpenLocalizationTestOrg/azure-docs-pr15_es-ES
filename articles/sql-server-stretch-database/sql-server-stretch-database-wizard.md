<properties
    pageTitle="Introducción al ejecutar la base de datos de habilitar estirar Asistente para | Microsoft Azure"
    description="Aprenda a configurar una base de datos para expandir base de datos mediante la ejecución de la base de datos de habilitar para estirar asistente."
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
    ms.topic="hero-article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="get-started-by-running-the-enable-database-for-stretch-wizard"></a>Para empezar, la ejecución de la base de datos de habilitar para estirar Asistente

Para configurar una base de datos para expandir base de datos, ejecute la base de datos de habilitar para estirar asistente.  Este tema describe la información que tenga que escribir y las opciones que se deben realizar en el asistente.

Para obtener más información acerca de la base de datos de estirar, vea [Expandir base de datos](sql-server-stretch-database-overview.md).

 >   [AZURE.NOTE] Más adelante, si deshabilita la base de datos de estirar, recuerde que la deshabilitación de la base de datos de estirar para una tabla o una base de datos no elimina el objeto remoto. Si desea eliminar la tabla remota o la base de datos remota, tiene que ocupa a través del portal de administración de Azure. Los objetos remotos seguirán contraer costos Azure hasta que se eliminen manualmente. 

## <a name="launch-the-wizard"></a>Iniciar el Asistente

1.  En SQL Server Management Studio, en el Explorador de objetos, seleccione la base de datos en la que desea habilitar estirar.

2.  Derecha\-haga clic en y seleccione **las tareas**, a continuación, seleccione **estirar**y, a continuación, seleccione **Habilitar** para iniciar el asistente.

## <a name="Intro"></a>Introducción
Revise el propósito del asistente y los requisitos previos.

Los requisitos previos importantes incluyen las siguientes:

-   Debe ser un administrador para cambiar la configuración de la base de datos.
-   Debe tener una suscripción de Microsoft Azure.
-   SQL Server tiene que ser capaces de comunicarse con el servidor remoto de Azure.

![Página de introducción del Asistente para la base de datos de estirar][StretchWizardImage1]

## <a name="Tables"></a>Seleccionar las tablas
Seleccione las tablas que desea habilitar para estirar.

Tablas con muchas filas aparecen en la parte superior de la lista ordenada. Antes de que el asistente muestra la lista de tablas, los analiza para tipos de datos que no son compatibles actualmente con estiramiento de base de datos.

![Página Seleccionar tablas del Asistente para la base de datos de estirar][StretchWizardImage2]

|Columna|Descripción|
|----------|---------------|
|(sin título)|Active la casilla de verificación de esta columna para habilitar la tabla seleccionada para estirar.|
|**Nombre**|Especifica el nombre de la columna de la tabla.|
|(sin título)|Un símbolo de esta columna puede representar una advertencia que no\'t impedir la habilitación de la tabla seleccionada para estirar. También puede representar un problema de bloqueo que evita la habilitación de la tabla seleccionada para estirar \- por ejemplo, porque la tabla utiliza un tipo de datos no admitidos. Desplace el puntero sobre el símbolo para mostrar más información en una información sobre herramientas. Para obtener más información, consulte [limitaciones para expandir base de datos](sql-server-stretch-database-limitations.md).|
|**Se ajusta**|Indica si la tabla ya está habilitada para estirar.|
|**Migrar**|Puede migrar una tabla completa (**Toda la tabla**), o puede especificar un filtro en una columna existente en la tabla. Si desea usar una función de filtro diferentes para seleccionar filas para migrar, ejecute la instrucción ALTER TABLE para especificar la función filter después de salir del asistente. Para obtener más información sobre la función de filtro, vea [Seleccionar filas para migrar mediante una función de filtro](sql-server-stretch-database-predicate-function.md). Para obtener más información sobre cómo aplicar la función, vea [Habilitar estiramiento de base de datos de una tabla](sql-server-stretch-database-enable-table.md) o [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Filas**|Especifica el número de filas de la tabla.|
|**Tamaño (KB)**|Especifica el tamaño de la tabla en KB.|

## <a name="Filter"></a>Opcionalmente, proporcione un filtro de fila

Si desea proporcionar una función de filtro para seleccionar las filas para migrar, realice lo siguiente en la página **Seleccionar tablas** .

1.  En la lista **Seleccione las tablas que desea ajustar** , haga clic en **Toda la tabla** en la fila de la tabla. Se abre el cuadro de diálogo **Seleccionar filas para estirar** .

    ![Definir una función de filtro][StretchWizardImage2a]

2.  En el cuadro de diálogo **Seleccionar filas para estirar** , seleccione **Elegir filas**.

3.  En el **campo nombre**, proporcione un nombre para la función de filtro.

4.  Para la cláusula **Where** , seleccione una columna de la tabla, seleccione un operador y proporcionar un valor.

5. Haga clic en **Comprobar** para probar la función. Si la función devuelve resultados de la tabla - es decir, si hay filas para migrar que cumplen la condición - la prueba de informes de **éxito**.

    >   [AZURE.NOTE] El cuadro de texto que muestra la consulta de filtro es de solo lectura. No se puede modificar la consulta en el cuadro de texto.

6.  Haga clic en listo para volver a la página **seleccionadas tablas** .

La función filter se crea en SQL Server solo cuando finalice el asistente. Hasta entonces, puede volver a la página **Seleccionar tablas** para cambiar o cambiar el nombre de la función filter.

![Seleccione página tablas después de definir una función de filtro][StretchWizardImage2b]

Si desea utilizar otro tipo de función de filtro para seleccionar filas para migrar, siga uno de los siguientes procedimientos.  

-   Salir del asistente y ejecute la instrucción ALTER TABLE para habilitar estirar para la tabla y especificar una función de filtro. Para obtener más información, vea [Habilitar estiramiento de base de datos de una tabla](sql-server-stretch-database-enable-table.md).  

-   Ejecute la instrucción ALTER TABLE para especificar una función de filtro después de salir del asistente. Para ver los pasos necesarios, vea [Agregar una función de filtro después de ejecutar al Asistente](sql-server-stretch-database-predicate-function.md#addafterwiz).

## <a name="Configure"></a>Configurar la implementación de Azure

1.  Inicie sesión con una cuenta de Microsoft en Microsoft Azure.

    ![Inicie sesión en Azure - Asistente para la base de datos de estirar][StretchWizardImage3]

2.  Seleccione la suscripción existente de Azure para expandir base de datos.

3.  Seleccione un área de Azure.
    -   Si crea un nuevo servidor, el servidor se crea en esta área.  
    -   Si tiene servidores existentes en la región seleccionada, el asistente muestra ellos al elegir **servidor existente**.

    Para minimizar la latencia, seleccione la región de Azure en la que se encuentra el SQL Server. Para obtener más información acerca de las regiones, vea [Áreas de Azure](https://azure.microsoft.com/regions/).

4.  Especifique si desea usar un servidor existente o crear un nuevo servidor de Azure.

    Si está asociada a Active Directory en su servidor de SQL Azure Active Directory, si lo desea puede usar una cuenta de servicio federado para SQL Server para comunicarse con el servidor remoto de Azure. Para obtener más información sobre los requisitos para esta opción, vea [Modificar establecer las opciones de base (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

    -   **Crear nuevo servidor**

        1.  Crear un inicio de sesión y una contraseña para el administrador del servidor.

        2.  Si lo desea, use una cuenta de servicio federado para SQL Server para comunicarse con el servidor remoto de Azure.

        ![Crear nuevo servidor Azure - Asistente para la base de datos de estirar][StretchWizardImage4]

    -   **Servidor existente**

        1.  Seleccione el servidor de Azure existente.

        2.  Seleccione el método de autenticación.

            -   Si selecciona **Autenticación de SQL Server**, proporcione el inicio de sesión de administrador y la contraseña.

            -   Seleccione **Autenticación integrada de Active Directory** para usar una cuenta de servicio federado para SQL Server para comunicarse con el servidor remoto de Azure. Si el servidor seleccionado no se integra con Azure Active Directory, esta opción no aparece.

        ![Seleccione servidor de Azure existente - Asistente para la base de datos de estirar][StretchWizardImage5]

## <a name="Credentials"></a>Proteger las credenciales
Debe tener una clave principal de base de datos para proteger las credenciales de base de datos de estirar para conectarse a la base de datos remota.  

Si ya existe una clave principal de base de datos, escriba la contraseña para el mismo.  

![Proteger las credenciales página del Asistente para la base de datos de estirar][StretchWizardImage6b]

Si la base de datos no tiene una clave principal existente, escriba una contraseña segura para crear una clave principal de base de datos.  

![Proteger las credenciales página del Asistente para la base de datos de estirar][StretchWizardImage6]

Para obtener más información acerca de la clave principal de base de datos, vea [Crear clave principal (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) y [crear una clave principal de base de datos](https://msdn.microsoft.com/library/aa337551.aspx). Para obtener más información acerca de la credencial que crea el asistente, vea [Crear base de datos de ámbito de la CREDENCIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Seleccione dirección IP
Usar el intervalo de direcciones IP de subred (recomendado) o la dirección IP pública de SQL Server, para crear una regla de firewall en Azure que permite a SQL Server comunicarse con el servidor remoto de Azure.

La dirección o direcciones IP que proporciona en esta página indican al servidor de Azure para permitir que los datos entrantes, consultas y operaciones de administración iniciadas por SQL Server para pasar a través del firewall de Azure. El asistente no cambia nada en la configuración del firewall en SQL Server.

![Seleccione la página de dirección IP del Asistente para la base de datos de estirar][StretchWizardImage7]

## <a name="Summary"></a>Resumen
Revise los valores que ha introducido y las opciones que seleccionó en el asistente y los costos estimados de Azure. A continuación, seleccione **Finalizar** para habilitar estirar.

![Página de resumen del Asistente para la base de datos de estirar][StretchWizardImage8]

## <a name="Results"></a>Resultados
Revise los resultados.

Para supervisar el estado de la migración de datos, consulte [Monitor y solucionar problemas de migración de datos (base de datos de estirar)](sql-server-stretch-database-monitor.md).

![Página de resultados del Asistente para la base de datos de estirar][StretchWizardImage9]

## <a name="KnownIssues"></a>El Asistente de solución de problemas
**Error en el Asistente para la base de datos de estirar.**
Si Expandir base de datos no se habilitan todavía en el servidor y ejecutar al asistente sin el sistema de permisos de administrador para habilitarlo, se produce un error en el asistente. Pida al administrador del sistema para habilitar la base de datos de estirar en la instancia del servidor local y ejecute al Asistente de nuevo. Para obtener más información, vea [como requisito previo: permiso para habilitar la base de datos de estiramiento en el servidor](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## <a name="next-steps"></a>Pasos siguientes
Habilitar tablas adicionales para expandir base de datos. Supervisar la migración de datos y administrar estirar\-habilitado para bases de datos y tablas.

-   [Habilitar estiramiento de base de datos de una tabla](sql-server-stretch-database-enable-table.md) para habilitar tablas adicionales.

-   [Monitor y solucionar problemas de migración de datos](sql-server-stretch-database-monitor.md) para ver el estado de la migración de datos.

-   [Pausar y reanudar la base de datos de estirar](sql-server-stretch-database-pause.md)

-   [Administrar y solucionar problemas de base de datos de estirar](sql-server-stretch-database-manage.md)

-   [Copia de seguridad habilitados para expandir las bases de datos](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Vea también

[Habilitar estiramiento de base de datos para una base de datos](sql-server-stretch-database-enable-database.md)

[Habilitar la base de datos de ampliación de una tabla](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png
