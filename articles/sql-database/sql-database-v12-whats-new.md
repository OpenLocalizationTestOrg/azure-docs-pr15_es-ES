<properties
    pageTitle="Novedades de V12 de base de datos de SQL | Microsoft Azure"
    description="Describe por qué le vendrá sistemas empresariales que están utilizando la base de datos de SQL Azure en la nube con la actualización a versión V12 ahora."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="genemi"/>


# <a name="whats-new-in-sql-database-v12"></a>Novedades de V12 de base de datos de SQL


Este tema describe las muchas ventajas que tiene la nueva versión de V12 de base de datos de SQL Azure sobre versión V11.


Seguimos agregar características a V12. Así que le recomendamos que, visite nuestra página de actualizaciones de servicio Web para Azure y use los filtros:


- Filtrado por el [servicio de base de datos de SQL](https://azure.microsoft.com/updates/?service=sql-database).
- Filtrado por disponibilidad General [anuncios (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) para las características de base de datos de SQL.


La información más reciente sobre los límites de recursos para la base de datos SQL documentada en:<br/>[Límites de recursos de base de datos SQL azure](sql-database-resource-limits.md).


## <a name="increased-application-compatibility-with-sql-server"></a>Aplicación mayor compatibilidad con SQL Server


Un objetivo clave de V12 de base de datos de SQL era para mejorar la compatibilidad con Microsoft SQL Server 2014 y para mantener la compatibilidad cuando se publican nuevas versiones de SQL Server. Entre otras áreas V12 logra paridad con SQL Server en el área importante de la programación. Por ejemplo:

- [Compatibilidad integrada con JSON](https://msdn.microsoft.com/library/dn921897.aspx)

- [Funciones de la ventana](http://msdn.microsoft.com/library/ms189798.aspx)con [sobre](http://msdn.microsoft.com/library/ms189461.aspx)

- [Los índices XML](http://msdn.microsoft.com/library/bb934097.aspx) y [selectivos índices XML](http://msdn.microsoft.com/library/jj670104.aspx)

- [Seguimiento de cambios](http://msdn.microsoft.com/library/bb933875.aspx)

- [SELECCIONAR... EN](http://msdn.microsoft.com/library/ms188029.aspx)

- [Búsqueda de texto](http://msdn.microsoft.com/library/ms142571.aspx)

- [MODIFICAR la configuración de la base de datos en el ámbito (Transact-SQL)](http://msdn.microsoft.com/library/mt629158.aspx)

Consulte [aquí](sql-database-transact-sql-information.md) para el conjunto pequeño de funciones aún no admitidas en la base de datos de SQL.


### <a name="compatibility-level-130"></a>Nivel de compatibilidad 130


> [AZURE.IMPORTANT] A partir de **junio 2016**, tienen *recién* creado bases de datos en V12 de base de datos de SQL Azure su nivel de compatibilidad comenzar en 130, que coincide con Microsoft SQL Server 2016 disponibilidad general.
> 
> Puede usar `ALTER DATABASE YourDatabase SET COMPATIBILITY_LEVEL = 120` si lo prefiere.
> 
> Bases de datos creadas antes de junio de 2016 no tiene su nivel de compatibilidad ha cambiado por este cambio de forma predeterminada. Ni el nivel de una base de datos ha cambiado con la actualización de V11 a V12.



Para obtener una explicación de cómo puede comparar las consultas más importantes entre las últimas frente a nivel de compatibilidad anterior, consulte:

- [Rendimiento mejorado de la consulta con 130 de nivel de compatibilidad de base de datos SQL Azure](sql-database-compatibility-level-query-performance-130.md)



## <a name="more-premium-performance-new-performance-levels"></a>Más performance premium, nuevos niveles de rendimiento


En V12, hemos aumentado a unidades de la transacción de base de datos (DTUs) asignado a todos los niveles de rendimiento Premium por 25% sin costo adicional. Aún mejor rendimiento se puede realizar con las nuevas características como:


- Compatibilidad con en memoria [columnstore índices](http://msdn.microsoft.com/library/gg492153.aspx).
- [Tabla de partición por filas](http://msdn.microsoft.com/library/ms187802.aspx) con las mejoras de [Truncar tabla](http://msdn.microsoft.com/library/ms177570.aspx)relacionadas.
- La disponibilidad de administración dinámica [(DMV)](http://msdn.microsoft.com/library/ms188754.aspx) para ayudar a supervisar y ajustar el rendimiento de las vistas.


### <a name="reliable-performance"></a>Rendimiento fiable


Si el programa cliente se conecta a V12 de base de datos de SQL mientras el cliente se ejecuta en una máquina virtual (VM) en Azure, debe abrir los siguientes intervalos de puerto en la máquina virtual:

- 11000 11999
- 14000 a 14999


Haga clic [aquí](sql-database-develop-direct-route-ports-adonet-v12.md) para obtener detalles acerca de los puertos para V12 de base de datos de SQL. Los puertos son necesarias mejoras de rendimiento en V12 de base de datos de SQL.


## <a name="better-support-for-cloud-saas-vendors"></a>Mejor compatibilidad para nube SaaS proveedores


Solo en V12, se publican el nuevo nivel de rendimiento estándar S3 y la versión preliminar de pública de [grupos de elásticos de la base de datos](sql-database-elastic-pool.md). Grupos de base de datos elástico es una solución diseñada para nube SaaS proveedores.  Grupos de elásticos de la base de datos, puede:


- Compartir DTUs entre las bases de datos a reducir los costes para un gran número de bases de datos.
- Ejecutar [trabajos elásticos de la base de datos](sql-database-elastic-jobs-overview.md) para administrar bases de datos de escala.


## <a name="security-enhancements"></a>Mejoras de seguridad


La seguridad es un objetivo principal para cualquier persona que se ejecuta su empresa en la nube. Las características de seguridad más recientes publicadas en V12 incluyen:


- [Seguridad a nivel de fila](http://msdn.microsoft.com/library/dn765131.aspx) (RLS)
- [Datos dinámicos Masking](sql-database-dynamic-data-masking-get-started.md)
- [Bases de datos de contenido](http://msdn.microsoft.com/library/ff929188.aspx)
- [Funciones de aplicación](http://msdn.microsoft.com/library/ms190998.aspx) administrado con conceder, denegar, REVOCAR
- [Cifrado de datos transparente](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) (TDE)
- [Conectarse a la base de datos SQL mediante la autenticación de Azure Active Directory](sql-database-aad-authentication.md)
 - Base de datos SQL ahora es compatible con la autenticación de Azure Active Directory, un mecanismo de conectarse a la base de datos SQL usando las identidades de Azure Active Directory (AD Azure). Con la autenticación de Azure Active Directory puede administrar de forma centralizada las identidades de los usuarios de la base de datos y otros servicios de Microsoft en una ubicación central.
- [Siempre cifrados](https://msdn.microsoft.com/library/mt163865.aspx) (en la vista previa) permite cifrado transparente a aplicaciones y permite a los clientes cifrar datos confidenciales dentro de las aplicaciones cliente sin tener que compartir las claves de cifrado con la base de datos de SQL.


## <a name="increased-business-continuity-when-recovery-is-needed"></a>Mayor continuidad empresarial cuando es necesaria la recuperación


V12 ofrece objetivos de punto de recuperación mejorados (RPOs) y los tiempos de recuperación estimada (ERT):


| Característica de continuidad empresarial | Versión anterior | V12 |
| :-- | :-- | :-- |
| Restaurar geo | • RPO < 24 horas.<br/>• Insertar < 12 horas. | • RPO < 1 hora.<br/>• Insertar < 12 horas. |
| Replicación de Geo Active | • RPO < 5 minutos.<br/>• Insertar < 1 hora. | Segundos de • RPO < 5.<br/>Segundos de • insertar < 30. |


Para obtener más información, vea [continuidad empresarial de base de datos de SQL](sql-database-business-continuity.md) .


## <a name="more-reasons-to-upgrade-now"></a>Más motivos para actualizar ahora


Hay muchas razones de por qué los clientes deberían actualizar ahora a V12 de base de datos de SQL Azure V11:


- V12 de base de datos de SQL tiene una lista larga de las características más allá de las características de V11.
- Seguimos agregar nuevas características a V12, pero no hay nuevas características se agregará a V11.
- La mayoría de nuevas características se publican V12 de base de datos de SQL antes de lanzamiento para Microsoft SQL Server.


## <a name="are-you-using-v12-already"></a>¿Ya usa V12?


Una forma sencilla de ver si tiene una base de datos o el servidor lógico que se ejecuta en una versión anterior del servicio de base de datos SQL es hacer lo siguiente:


1. Vaya al [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **Examinar**.
3. Haga clic en **servidores de SQL Server**.
4. El icono junto a su servidor o base de datos indica el artículo:
 - ![Icono para un servidor v12](./media/sql-database-v12-whats-new/v12_icon.png) **V12 de servidor lógico**
 - ![Icono de servidor de versiones anteriores](./media/sql-database-v12-whats-new/earlier_icon.png) **servidor lógico de versión anterior**


Otra técnica para determinar la versión es para ejecutar la `SELECT @@version;` instrucción en la base de datos y ver los resultados similares a las:


- **12**.0.2000.10 &nbsp; *(versión V12)*
- **11**.0.9228.18 &nbsp; *(versión V11)*


Una base de datos de V12 se puede alojar en un servidor lógico de V12. Y un servidor V12 puede hospedar sólo V12 bases de datos.


Si aún no se están ejecutando en V12, puede actualizar el servidor lógico siguiendo los pasos de [actualizar a V12 de base de datos de SQL en su lugar](sql-database-v12-plan-prepare-upgrade.md).


## <a name="V12AzureSqlDbPreviewGaTable"></a>Regiones de disponibilidad generales


- 31 de julio de 2015, todas las regiones tenían ha promovido a disponibilidad General (GA).
- V12 se publicó en diciembre de 2014, pero solo en el estado de vista previa.

[Términos adicionales de uso de las vistas previas de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
