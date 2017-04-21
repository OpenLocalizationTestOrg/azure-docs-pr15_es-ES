<properties
    pageTitle="¿Qué es la base de datos SQL? Introducción a la base de datos SQL | Microsoft Azure"
    description="Obtener una introducción a la base de datos SQL: detalles técnicos y las capacidades de Microsoft relacionales (RDBMS) de sistema de administración en la nube de base de datos."
    keywords="Introducción a sql, introducción a sql, ¿qué es la base de datos sql"
    services="sql-database"
    documentationCenter=""
    authors="shontnew"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="shkurhek"/>

# <a name="what-is-sql-database-introduction-to-sql-database"></a>¿Qué es la base de datos SQL? Introducción a la base de datos SQL

Base de datos de SQL es un servicio de base de datos relacionales en la nube basada en el motor de Microsoft SQL Server líder en el mercado, con las capacidades críticas. Base de datos SQL ofrece performance predecible, escalabilidad sin el tiempo de inactividad, la continuidad empresarial y la protección de datos: todos con administración cerca de cero. Puede centrarse en el desarrollo de aplicaciones rápida y acelerar el tiempo en el mercado, en lugar de administrar máquinas virtuales y la infraestructura. Porque se basa en el motor de [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) , es compatible con la base de datos SQL existente herramientas de SQL Server, bibliotecas y API, lo que facilita la mover y extender a la nube.

En este artículo es una introducción a los conceptos básicos de base de datos de SQL y características relacionadas con el rendimiento, escalabilidad y capacidad de administración, con vínculos a explorar detalles. Si está listo para ir en, puede [crear la primera base de datos SQL](sql-database-get-started.md) o [crear un grupo de la base de datos elásticos](sql-database-elastic-pool-create-portal.md) en minutos. Si desea un análisis más profundo, vea este vídeo de 30 minutos.

> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajustar el rendimiento y escala sin tiempo de inactividad

Bases de datos SQL está disponible en Basic, estándar y Premium *niveles de servicio*. Cada nivel de servicio ofrece [diferentes niveles de rendimiento y las capacidades](sql-database-service-tiers.md) para admitir ligero a cargas de trabajo de base de datos de pesado. Puede crear su primera aplicación en una base de datos pequeña para pagar un mes, a continuación, [cambiar el nivel de servicio](sql-database-scale-up.md) manualmente o mediante programación en cualquier momento con la aplicación de virus en todo el mundo, sin el tiempo de inactividad para la aplicación o sus clientes.

Para muchas empresas y aplicaciones, poder crear bases de datos y rendimiento de la base de datos única de marcado hacia arriba o hacia abajo a petición es suficiente, especialmente si los patrones de uso son relativamente predecibles. Pero si tiene patrones de uso imprevistos, que puede será difícil de administrar costos y su modelo de negocios.

[Elásticos grupos](sql-database-elastic-pool.md) en la base de datos SQL resolver este problema. El concepto es sencillo. Asignar a un grupo de rendimiento y de pago para el rendimiento del grupo en conjunto en lugar de rendimiento de la base de datos. No es necesario marcar el rendimiento de la base de datos hacia arriba o hacia abajo. Las bases de datos en el grupo, denominado *elásticos bases de datos*, escalar automáticamente hacia arriba y hacia abajo para satisfacer la demanda. Utilizan elásticos bases de datos, pero no superen los límites del grupo, por lo que el costo permanece predecible incluso si no uso de la base de datos. Aún más, puede [Agregar y quitar las bases de datos al grupo](sql-database-elastic-pool-manage-portal.md), ajuste de escala de la aplicación de un conjunto de bases de datos a miles, todo dentro de un presupuesto que puede controlar. Para obtener más información acerca de los patrones de diseño para las aplicaciones SaaS usando grupos elásticos, vea [Modelos de diseño para aplicaciones de SaaS inquilino múltiples con la base de datos de SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

En ambos casos que vaya: simples o elástico: no está bloqueado. Puede fusionar bases de datos con los grupos de elásticos de la base de datos y cambiar los niveles de servicio de bases de datos y grupos para crear diseños innovadoras. Además, con la potencia y alcance de Azure, puede combinar y hacer coincidir servicios Azure con la base de datos de SQL para satisfacer sus necesidades de diseño de la aplicación moderna única, eficacia de costo y recursos de unidad y desbloquear nuevas oportunidades de negocio.

Pero, ¿cómo se puede comparar el rendimiento relativo de las bases de datos y grupos de la base de datos? ¿Cómo puede saber el derecho haga clic en Detener al marcar hacia arriba y abajo? La respuesta es la unidad de transacción de base de datos (DTU) para bases de datos y la DTU elástica (eDTU) para elásticos bases de datos y grupos de la base de datos. Consulte [Opciones de base de datos SQL y rendimiento: comprender qué está disponible en cada nivel de servicio](sql-database-service-tiers.md) para obtener más detalles.

## <a name="keep-your-app-and-business-running"></a>Mantener su aplicación y la empresa en funcionamiento

Sector líder 99,99% disponibilidad nivel acuerdo de servicio de Azure [(SLA)](http://azure.microsoft.com/support/legal/sla/), con tecnología de una red mundial de centros de datos administrado por Microsoft, le ayuda a mantener su aplicación de 24/7. Con cada base de datos SQL, aprovechar las ventajas de protección de datos integrados, tolerancia a errores y protección de datos en caso contrario, tendrá que diseñar, comprar, crear y administrar. Aun así, según las necesidades de su empresa, puede solicitar niveles adicionales de protección para garantizar que la aplicación y su empresa pueden recuperar rápidamente un desastre, un error o algo más. Con la base de datos de SQL, cada nivel de servicio ofrece un menú diferente de características que puede utilizar para ir hacia arriba y ejecute y manténgase esa forma. Puede usar la restauración de punto en el tiempo para devolver una base de datos a un estado anterior, anteriores hasta 35 días. Además, si el centro de datos que aloja las bases de datos experimenta una interrupción, puede migración tras error a réplicas de base de datos en una región diferente. O bien, puede utilizar réplicas para actualizaciones o reubicación de diferentes regiones.

![Geo-replicación de base de datos SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)


Para obtener más información sobre las características de continuidad empresarial diferente para niveles de servicio diferente, vea [Continuidad empresarial](sql-database-business-continuity.md) .

## <a name="secure-your-data"></a>Proteger los datos
SQL Server tiene un tradicionales sólido de seguridad de datos que se mantiene la base de datos de SQL con las características que limitan el acceso, protección los datos y supervisar la actividad. Para un breve resumen de las opciones de seguridad que tiene en la base de datos de SQL, vea [proteger la base de datos SQL](sql-database-security.md) . Consulte el [Centro de seguridad de base de datos de SQL y el motor de base de datos de SQL Server](https://msdn.microsoft.com/library/bb510589) para obtener una vista más completa de características de seguridad. Y visite el [Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/security/) para obtener información sobre la seguridad de plataforma de Azure.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha leído una introducción a la base de datos de SQL y responde la pregunta "¿Cuál es la base de datos SQL?", está listo para:

- Consulte la [página de precios](https://azure.microsoft.com/pricing/details/sql-database/) para base de datos único y comparaciones de costo elásticos de la base de datos y calculadoras.
- Obtenga más información sobre [grupos elásticos](sql-database-elastic-pool.md).
- Introducción al [crear la primera base de datos](sql-database-get-started.md).
- [Conectarse y consultar con SSMS](sql-database-connect-query-ssms.md)
- Crear su primera aplicación en C#, Java, Node.js, PHP, Python o Ruby: [bibliotecas de conexión de base de datos de SQL y SQL Server](sql-database-libraries.md)
- Ver un índice de los títulos y las descripciones de [todos los temas de servicio de base de datos de sql Azure](sql-database-index-all-articles.md).
