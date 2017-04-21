<properties
    pageTitle="Escalado con la base de datos de SQL Azure | Microsoft Azure"
    description="Software como desarrolladores de servicio (SaaS) puede crear fácilmente elásticos, scalable bases de datos en la nube mediante estas herramientas"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove"/>

# <a name="scaling-out-with-azure-sql-database"></a>Escalar con la base de datos de SQL Azure

Puede escalar fácilmente las bases de datos de SQL Azure con las herramientas de **Base de datos elástico** . Estas herramientas y características le permiten utilizar los recursos de base de datos de prácticamente ilimitada de **Base de datos de SQL Azure** para crear soluciones para cargas de trabajo de transacciones y Software especialmente como aplicaciones de servicio (SaaS). Elásticas características de base de datos se componen de las siguientes acciones:

* [Biblioteca de cliente de base de datos elástico](sql-database-elastic-database-client-library.md): la biblioteca de cliente es una característica que le permite crear y mantener bases de datos sharded.  Consulte [Introducción a las herramientas de base de datos elástico](sql-database-elastic-scale-get-started.md).
* [Herramienta de combinación de la división de bases de datos elástico](sql-database-elastic-scale-overview-split-and-merge.md): mueve los datos entre las bases de datos sharded. Esto es útil para mover datos desde una base de datos de varios inquilino para una base de datos solo inquilino (o viceversa). Consulte [database elástico tutorial de la herramienta de combinación de división](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Base de datos elástico trabajos](sql-database-elastic-jobs-overview.md) (vista previa): utilice los trabajos para administrar un gran número de bases de datos de SQL Azure. Fácilmente realizar operaciones administrativas como cambios de esquema, administración de credenciales, las actualizaciones de datos de referencia, recopilación de datos de rendimiento o la colección de telemetría de inquilino (cliente) con trabajos.
* [Consulta de base de datos elástico](sql-database-elastic-query-overview.md) (vista previa): permite ejecutar una consulta de Transact-SQL que se extiende por varias bases de datos. Esto permite conexión con herramientas de informes, como Excel, PowerBI, una plantilla, etcetera.
* [Transacciones elásticas](sql-database-elastic-transactions-overview.md): esta característica le permite ejecutar las transacciones que abarcan varias bases de datos en la base de datos de SQL Azure. Transacciones de base de datos elástico están disponibles para aplicaciones .NET utilizando ADO .NET e integran con la experiencia de programación familiar con las [clases System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

El siguiente gráfico muestra una arquitectura que incluye las **características de elásticos de la base de datos** en relación con un conjunto de bases de datos.

En este gráfico, los colores de la base de datos representan los esquemas. Bases de datos con el mismo color comparten el mismo esquema.

1. Un conjunto de **bases de datos de SQL Azure** se hospedan en Azure con arquitectura de sharding.
2. La **biblioteca de cliente de base de datos elástico** se usa para administrar un conjunto de partes.
3. Un subconjunto de las bases de datos se colocan en un **grupo elásticos de la base de datos**. (Consulte [¿Qué es un grupo?](sql-database-elastic-pool.md)).
4. Un **trabajo de base de datos elástico** ejecuta programadas o ad-hoc T-SQL secuencias de comandos en todas las bases de datos.
5. La **herramienta de combinación de división** se utiliza para mover los datos de partes de uno a otro.
6. La **consulta de base de datos elástico** le permite escribir una consulta que se extiende por todas las bases de datos en el conjunto de partes.
7. **Transacciones elásticas** le permite ejecutar las transacciones que abarcan varias bases de datos. 


![Elásticos herramientas de base de datos][1]


## <a name="why-use-the-tools"></a>¿Por qué usar las herramientas?

Escala y elasticidad lograr para aplicaciones de nube ha sido sencillo para máquinas virtuales y el almacenamiento de blobs--simplemente agregar o restar unidades o aumentan power. Pero sigue siendo un reto para el procesamiento de datos con estado en bases de datos relacionales. Retos surgido en estos escenarios:

* Crecimiento y reducción de capacidad para el elemento de la base de datos relacional de su carga de trabajo.
* Administración de puntos de conexión que pueden surgir que afectan a un subconjunto específico de datos, como un especialmente ocupado cliente final (inquilino).

Tradicionalmente, escenarios como los siguientes se han tratado invirtiendo en servidores de base de datos de gran escala para la aplicación. Sin embargo, esta opción está limitada en la nube dónde se realizará todo el procesamiento de hardware de mercancías predefinido. En su lugar, distribución de datos y procesamiento a través de muchas bases de datos estructurados idéntica (un patrón de escalado conocido como "sharding") proporciona una alternativa enfoques de escalado tradicionales, tanto en términos de costo y elasticidad.

## <a name="horizontal-and-vertical-scaling"></a>Escala horizontal y vertical

La figura siguiente muestra las dimensiones horizontales y verticales de escala, que son las formas básicas que pueden ampliarse las bases de datos elásticos.

![Horizontal o Vertical escala][2]

Escala horizontal se refiere a agregando o quitando bases de datos para ajustar el rendimiento general o capacidad. También se denomina "escala". Sharding, en el que los datos se dividen en una colección de bases de datos idéntica estructuradas es una manera común para implementar el ajuste de escala horizontal.  

Escala vertical hace referencia a aumentar o disminuir el nivel de rendimiento de una base de datos individual, también se conoce como "escalado."

Muchas aplicaciones de base de datos de escala de la nube utiliza una combinación de estas dos estrategias. Por ejemplo, un Software como una aplicación de servicio puede usar escala horizontal para aprovisionar a nuevos clientes finales y escala vertical para permitir que la base de datos de cada cliente final aumentar o reducir los recursos según sea necesario por la carga de trabajo.

* Escala horizontal se administra mediante la [biblioteca de cliente de base de datos elástico](sql-database-elastic-database-client-library.md).

* Escala vertical se realiza mediante los cmdlets de PowerShell de Azure para cambiar el nivel de servicio, o colocando las bases de datos en un grupo de elástico.

## <a name="sharding"></a>Sharding

*Sharding* es una técnica para distribuir grandes cantidades de datos estructurados idéntica en un número de bases de datos independientes. Es especialmente popular con los desarrolladores de nube creación de Software como las ofertas de servicio (SAAS) para los clientes finales o empresas. Estos clientes finales a menudo se denominan "inquilinos". Sharding podrían ser necesarios para varios motivos:  

* La cantidad total de datos es demasiado grande para que quepa dentro de las restricciones de una base de datos
* El rendimiento de las transacciones de la carga de trabajo total supera las capacidades de una base de datos
* Inquilinos pueden requerir aislamiento físico de otros, por lo que son necesarias las bases de datos independientes para cada inquilino
* Las distintas secciones de una base de datos que necesite residen en diferentes ubicaciones geográficas de cumplimiento, rendimiento o geopolíticos motivos.

En otros escenarios, como la recopilación de datos desde dispositivos distribuidos, sharding puede utilizarse para rellenar un conjunto de bases de datos que se organizan temporalmente. Por ejemplo, una base de datos independiente puede estar dedicado a cada día o semana. En ese caso, la clave de sharding puede ser un número entero que representa la fecha (presente en todas las filas de las tablas sharded) y consultas recuperar información para un intervalo de fechas deben dirigirse por la aplicación al subconjunto de bases de datos que cubra el intervalo en cuestión.

Sharding funciona mejor cuando todas las transacciones en una aplicación pueden restringirse a un único valor de una clave de sharding. Que garantiza que todas las transacciones locales en una base de datos.

## <a name="multi-tenant-and-single-tenant"></a>Múltiples inquilino y solo inquilino

Algunas aplicaciones utilizan el enfoque más sencillo de creación de una base de datos independiente para cada inquilino. Este es el **patrón de sharding inquilino único** que proporciona aislamiento, capacidad de copia de seguridad y restauración y ajuste de escala en el nivel de detalle del inquilino de recursos. Con sharding solo inquilino, cada base de datos está asociado con un valor de Id. de inquilinos específico (o el valor de clave de cliente), pero dicha clave no siempre es necesario presente en los datos en Sí. Es responsabilidad de la aplicación para enrutar cada solicitud a la base de datos correspondiente y la biblioteca de cliente puede simplificar este proceso.

![Solo inquilino frente a múltiples arrendatarios][4]

Otros escenarios empaquetar varios inquilinos en bases de datos, en lugar de aislamiento en bases de datos independientes. Esto es típico **trama de múltiples arrendatarios sharding** – y pueden estar dirigida por el hecho de que una aplicación administra grandes cantidades de inquilinos muy pequeños. En sharding de múltiples arrendatario, las filas de las tablas de base de datos están diseñadas para realizar una clave que identifica el identificador de inquilinos o sharding. De nuevo, el nivel de aplicación es el responsable de enrutamiento solicitud del inquilino a la base de datos apropiado y esto puede que sea compatible con la biblioteca de cliente de base de datos elástico. Además, seguridad a nivel de fila se puede usar para filtrar las filas que se puede tener acceso cada inquilino: para obtener más información, consulte [aplicaciones de múltiples arrendatarios con herramientas de base de datos elásticas y seguridad a nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md). Puede ser necesario redistribuir datos entre las bases de datos con el modelo de múltiples arrendatarios sharding y, a continuación, esto es más fácil con la herramienta de combinación de división elásticos de la base de datos. Para obtener más información acerca de los patrones de diseño para las aplicaciones SaaS usando grupos elásticos, vea [Modelos de diseño para aplicaciones de SaaS inquilino múltiples con la base de datos de SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Mover los datos de varias bases de datos de usuarios de un solo

Al crear una aplicación de SaaS, es típico para ofrecer una versión de prueba del software de clientes potenciales. En este caso, es rentable para usar una base de datos de varios inquilino para los datos. Sin embargo, cuando un cliente potencial se convierte en un cliente, es mejor una base de datos de un solo inquilino porque proporciona un mejor rendimiento. Si el cliente había creado datos durante el período de prueba, use la [herramienta de combinación de división](sql-database-elastic-scale-overview-split-and-merge.md) para mover los datos de múltiples arrendatarios a la nueva base de datos de inquilinos de solo.

## <a name="next-steps"></a>Pasos siguientes

Para una aplicación de ejemplo que muestra la biblioteca de cliente, vea [Introducción a Datababase elásticos herramientas](sql-database-elastic-scale-get-started.md).

Para convertir bases de datos existentes para utilizar las herramientas, vea [migrar bases de datos existentes a escalado](sql-database-elastic-convert-to-use-elastic-tools.md).

Para ver los detalles del grupo de servidores de base de datos elástico, vea [Consideraciones de precio y rendimiento de un grupo de la base de datos elástico](sql-database-elastic-pool-guidance.md)o crear un nuevo grupo con el [tutorial](sql-database-elastic-pool-create-portal.md).  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

