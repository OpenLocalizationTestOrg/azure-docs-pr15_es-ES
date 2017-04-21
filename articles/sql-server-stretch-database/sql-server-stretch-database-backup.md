<properties
    pageTitle="Hacer copia de seguridad habilitados para expandir las bases de datos | Microsoft Azure"
    description="Obtenga información sobre cómo realizar copias de seguridad estirar\-habilitado para bases de datos."
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
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="douglasl"/>

# <a name="backup-stretch-enabled-databases"></a>Copia de seguridad habilitados para expandir las bases de datos

Realizar copias de seguridad de base de datos le ayudan a recuperar entre varios tipos de errores, los errores y desastres.  

-   Debe hacer copia de seguridad de su estirar\-habilitado para bases de datos de SQL Server.  

-   Microsoft Azure copia automáticamente los datos remotos que ha migrado estiramiento de base de datos de SQL Server en Azure.  

>    [AZURE.NOTE] Copia de seguridad es solo una parte de una completa alta disponibilidad y la solución de continuidad empresarial. Para obtener más información acerca de alta disponibilidad, vea [Soluciones de alta disponibilidad](https://msdn.microsoft.com/library/ms190202.aspx).

## <a name="back-up-your-sql-server-data"></a>Realizar copias de seguridad de los datos de SQL Server  

Para realizar copias de seguridad de su estirar\-habilitado para bases de datos de SQL Server, puede seguir usando los métodos de copia de seguridad de SQL Server que utiliza actualmente. Para obtener más información, vea [copia de seguridad y restauración de SQL Server bases de datos](https://msdn.microsoft.com/library/ms187048.aspx).

Copias de seguridad de una base de datos de SQL Server compatibles con estirar contienen solo los datos locales y datos elegibles para la migración en el punto en el tiempo cuando se ejecuta la copia de seguridad. \(Datos elegibles son que no se han migrado, pero se migrarán a Azure en función de la configuración de migración de las tablas.\) Esto se conoce como una copia de seguridad **superficial** y no incluye los datos ya se migran a Azure.  

## <a name="back-up-your-remote-azure-data"></a>Hacer copia de seguridad de sus datos de Azure remotos   

Microsoft Azure copia automáticamente los datos remotos que ha migrado estiramiento de base de datos de SQL Server en Azure.  

### <a name="azure-reduces-the-risk-of-data-loss-with-automatic-backup"></a>Azure reduce el riesgo de pérdida de datos con copias de seguridad automáticas  
El servicio de base de datos de SQL Server estirar en Azure protege las bases de datos remotas con instantáneas de almacenamiento automático al menos cada 8 horas. Conserva cada instantánea durante 7 días para proporcionarle un rango de puntos de restauración posibles.  

### <a name="azure-reduces-the-risk-of-data-loss-with-geo-redundancy"></a>Azure reduce el riesgo de pérdida de datos con geo\-redundancia  
Realizar copias de seguridad de base de datos de Azure se almacenan en geo\-almacenamiento de Azure redundantes (RA\-GRS) y por tanto, geo\-redundantes de forma predeterminada. Geo\-almacenamiento redundante duplica los datos en una región secundaria cientos de millas fuera de la región principal. En regiones primaria y secundarias, los datos se replican cada tres veces, en dominios de error independiente y actualización de dominios. Esto garantiza que los datos son resistentes incluso en el caso de una interrupción regional completa o desastre que representa una de las regiones Azure disponible.

### <a name="stretchRPO"></a>Ajuste de la base de datos reduce el riesgo de pérdida de datos para los datos de Azure conservando filas migrados temporalmente
Después de base de datos de estirar migra elegibles filas desde SQL Server a Azure, conserva las filas de la tabla provisional durante un mínimo de 8 horas. Si se restaura una copia de seguridad de la base de datos de Azure, base de datos de estirar usa las filas que se guardan en la tabla provisional conciliar SQL Server y las bases de datos de Azure.

Después de restaurar una copia de seguridad de los datos de Azure, debe ejecutar el procedimiento almacenado [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) para volver a conectar el estiramiento\-habilitado para la base de datos de SQL Server a la base de datos de Azure remoto. Cuando se ejecuta **sys.sp_rda_reauthorize_db**, estirar base de datos se ajusta automáticamente el servidor SQL Server y las bases de datos de Azure.

Para aumentar el número de horas de datos migrados que base de datos de estirar conserva temporalmente en la tabla provisional, ejecute el procedimiento almacenado [sys.sp_rda_set_rpo_duration](https://msdn.microsoft.com/library/mt707766.aspx) y especifique un número de superiores a 8 horas. Para decidir la cantidad de datos para conservar, tenga en cuenta los siguientes factores:
-   La frecuencia de copias de seguridad de Azure automáticas (al menos cada 8 horas).
-   El tiempo necesario después de un problema para detectar el problema y decide restaurar una copia de seguridad.
-   La duración de la operación de restauración de Azure.

> [AZURE.NOTE] Aumentar la cantidad de datos de la base de datos de estirar conserva temporalmente en la tabla provisional aumenta la cantidad de espacio en SQL Server.

Para comprobar el número de horas de datos de la base de datos de estirar actualmente conserva temporalmente en la tabla provisional, ejecute el procedimiento almacenado [sys.sp_rda_get_rpo_duration](https://msdn.microsoft.com/library/mt707767.aspx).

## <a name="see-also"></a>Vea también

[Administrar y solucionar problemas de base de datos de estirar](sql-server-stretch-database-manage.md)

[Sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Realizar copias de seguridad y restauración de bases de datos SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)
