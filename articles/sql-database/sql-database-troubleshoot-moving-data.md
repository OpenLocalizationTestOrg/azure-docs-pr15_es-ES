<properties
    pageTitle="Mover las bases de datos entre servidores, entre las suscripciones y dentro y fuera de Azure."
    description="Pasos rápidos para copiar, mover y migrar datos y bases de datos en la base de datos de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>Mover las bases de datos entre servidores, entre las suscripciones y dentro y fuera de Azure

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]
##<a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Para mover una base de datos a un servidor diferente en la misma suscripción
- En el [Portal de Azure](https://portal.azure.com), haga clic en **bases de datos SQL**, seleccione una base de datos de la lista y, a continuación, haga clic en **Copiar**. Para obtener más información, vea [Copiar una base de datos de SQL Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Para mover una base de datos entre suscripciones
- En el [Portal de Azure](https://portal.azure.com), haga clic en **servidores SQL Server** y, a continuación, seleccione el servidor que hospeda la base de datos de la lista. Haga clic en **mover**y, a continuación, seleccione los recursos para mover y la suscripción para ir a.

## <a name="to-migrate-a-sql-database-into-azure"></a>Migrar una base de datos SQL Azure
- Determinar la compatibilidad de la base de datos y, a continuación, elija el método de migración más adecuada según sus necesidades. Siga las instrucciones y las opciones de [migrar una base de datos de SQL Server](sql-database-cloud-migrate.md).

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>Para crear una copia de una base de datos para su uso fuera de Azure
- [Exportar un archivo de MOCHILA.](sql-database-export.md)
