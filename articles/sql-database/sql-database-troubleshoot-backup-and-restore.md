<properties
    pageTitle="Solucionar problemas de copia de seguridad y restaurar con la base de datos de SQL Azure"
    description="Obtenga información sobre cómo recuperar una base de datos de nube de errores e interrupciones mediante réplicas y copias de seguridad de base de datos de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>

# <a name="restore-a-database-to-a-previous-point-in-time-restore-a-deleted-database-or-recover-from-a-data-center-outage"></a>Restaurar una base de datos a un punto anterior en el tiempo, restaurar una base de datos eliminado o recuperarse de una interrupción de centro de datos

Base de datos SQL mantiene réplicas de la base de datos para que se puede recuperar de errores de usuario y las interrupciones. Opciones disponibles dependen del nivel de servicio de base de datos y las opciones que elija. Vea la [Información general de continuidad empresarial](sql-database-business-continuity.md) para obtener detalles y consideraciones de diseño.

## <a name="to-restore-a-database-to-a-previous-point-in-time"></a>Para restaurar una base de datos a un punto anterior en el tiempo
1.  En el [Portal de Azure](https://azure.microsoft.com/), haga clic en **bases de datos SQL**.
2.  Seleccione la base de datos de la lista y, a continuación, haga clic en **Restaurar**.
3.  Escriba un nuevo nombre para la base de datos, elija la fecha y hora para restaurar a partir de y, a continuación, haga clic en **crear.**
4.  Realizar ajustes de aplicación según sea necesario para hacer referencia a la nueva base de datos. Consulte [recuperar una base de datos en un momento](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="to-restore-an-accidentally-deleted-database"></a>Para restaurar una base de datos eliminado accidentalmente
1.  En el [Portal de Azure](https://azure.microsoft.com/), haga clic en **servidores de SQL Server**.
2.  Seleccione el servidor que hospeda la base de datos de la lista.
3.  En el módulo de servidor, desplácese hacia abajo y haga clic en **bases de datos eliminados**.
4.  Seleccione la base de datos para restaurar y, a continuación, haga clic en **crear**.
5.  Realizar ajustes de aplicación según sea necesario para hacer referencia a la nueva base de datos. Consulte [recuperar una base de datos eliminado](sql-database-recovery-using-backups.md#deleted-database-restore).

## <a name="to-recover-from-a-regional-datacenter-outage"></a>Para recuperar de una interrupción en el centro de datos regional
Bases de datos estándar y Premium, si configura replicar geo secundarios, puede recuperar con estos secundarios. Esto le da la capacidad para restaurar una base de datos con menos potencial de pérdida de datos. Para obtener más información, vea [recuperar una base de datos de SQL Azure con copias de seguridad de base de datos automatizada](sql-database-disaster-recovery.md) .

Azure también proporciona copias de seguridad de cada base de datos en una región diferente (una copia de seguridad geo redundantes). Puede crear una nueva base de datos de estas copias de seguridad, que se denomina Geo restaurar, pero es probable que podrá experimentar pérdida de datos si confía en este método solo.

**Para recuperar una base de datos usando geo restaurar:**

- En el [Portal de Azure](https://azure.microsoft.com/), haga clic en **nuevo**, haga clic en **datos y el almacenamiento**y, a continuación, haga clic en **Base de datos de SQL**y, a continuación, seleccione **copia de seguridad** como el origen de la base de datos. Para obtener más información, vea [recuperar una base de datos de SQL Azure de una interrupción](sql-database-disaster-recovery.md) .