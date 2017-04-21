<properties
   pageTitle="P+F de puesta de sol Business Edition y de Web de la base de datos de SQL Azure | Microsoft Azure"
   description="Averigüe cuándo se retirará de las bases de datos Web de SQL Azure y empresa y obtenga más información sobre las características y funcionalidades de los niveles de servicio de nuevo."
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/08/2016"
   ms.author="sstein" />

# <a name="web-and-business-edition-sunset-faq"></a>P+F de puesta de sol Web y Business Edition

Ahora se retiren bases de datos de negocio y Web de SQL Azure. Los niveles de Basic, estándar, Premium y elástica reemplazar las bases de datos Web y empresa retirar.

Para ayudarle con la actualización de bases de datos Web y empresas, el servicio de base de datos SQL recomienda un nivel y rendimiento de nivel de servicio adecuado (nivel de precio) para cada base de datos en función de su carga de trabajo histórico.

**Para obtener recomendaciones de nivel de precios:**

- [Actualización a V12 de base de datos de SQL con el portal de Azure](sql-database-upgrade-server-portal.md)
- [Actualización a V12 de base de datos de SQL con PowerShell](sql-database-upgrade-server-powershell.md)
- [Cambiar el nivel de precio de una base de datos Web o empresa](sql-database-service-tier-advisor.md)



## <a name="why-does-the-azure-portal-show-my-web-and-business-edition-databases-as-retired"></a>¿Por qué el portal de Azure mostrar mi negocio y Web edition bases de datos como retirado?

Porque bases de datos de edición de Web y empresa no estarán disponibles después de septiembre de 2015, el portal de nombres de bases de datos Web y empresa como retirado. La etiqueta retirada también proporciona un aviso que se deben actualizar las bases de datos Web y empresa a estándar, Basic o Premium. Para obtener información detallada sobre la actualización de bases de datos Web o empresa existentes con los nuevos niveles de servicio, consulte [actualizar a V12 de base de datos de SQL Azure](sql-database-upgrade-server-portal.md).

## <a name="which-new-service-tier-is-the-best-choice-to-upgrade-my-existing-web-or-business-database-to"></a>¿El nuevo nivel de servicio es la mejor opción para actualizar la base de datos Web o empresa existente a?

Seleccionar un nuevo servicio nivel y rendimiento nivel apropiado para la base de datos Web o empresa existente depende de los requisitos específicos de características y el rendimiento de la aplicación.

Use las recomendaciones de nivel de precios descritas anteriormente, o para obtener información detallada para ayudarle con la selección de un nuevo nivel de servicio adecuado, vea [actualizar a V12 de base de datos de SQL Azure](sql-database-upgrade-server-portal.md).

## <a name="why-is-microsoft-introducing-new-service-tiers"></a>¿Por qué Microsoft presenta nuevos niveles de servicio?

En función de los comentarios de los clientes, base de datos de SQL Azure presenta nuevos niveles de servicio para ayudar a clientes más fácilmente admitir cargas de trabajo de base de datos relacional. Nuevos niveles están diseñados para ofrecer un rendimiento predecible en una gama de siete niveles para atenuado a sus aplicaciones de transacciones visible. Además, los nuevos niveles ofrecen una variedad de características de continuidad empresarial, un SLA de tiempo activo más segura, mayores tamaños de base de datos para una mejor experiencia de facturación y menos dinero.

## <a name="where-can-i-learn-more-about-the-new-service-tiers"></a>¿Dónde puedo obtener más información sobre los niveles de servicio nuevo?

Para obtener información detallada acerca de los nuevos niveles de servicio y el modelo de rendimiento, vea [niveles de servicio](sql-database-service-tiers.md). Para obtener información sobre precios para los nuevos niveles de servicio, vea [precios de base de datos de SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="what-features-or-functionality-will-not-be-available-in-basic-standard-and-premium"></a>¿Qué características o funciones no estará disponible en Basic, estándar y Premium?

La característica de federaciones se retirará con las ediciones de Web y empresa. Se recomienda usar en su lugar o migrar a [Herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md) para [Base de datos de SQL Azure](sql-database-elastic-scale-get-started.md), que simplifica la creación y administración de una aplicación que usa sharding clientes que necesitan escalado sus bases de datos. Una biblioteca de cliente .NET permite que las aplicaciones definir cómo se asignan los datos a las solicitudes de shards y rutas OLTP a bases de datos correspondientes. Para admitir operaciones de administración que volver a configurar la forma en que los datos se distribuyen entre shards, una plantilla de servicio de nube de Azure se incluye que puede hospedar en su propia suscripción Azure. Además de las [Herramientas de base de datos elástico](sql-database-elastic-scale-get-started.md), Microsoft seguirá crear y publicar la [Guía de patrones y prácticas sharding personalizado](https://msdn.microsoft.com/library/azure/dn764977.aspx) basado en conocimientos de compromisos de cliente detallado. Nuevos clientes que necesitan escala funcionalidad deben desproteger [Herramientas de base de datos elástico](sql-database-elastic-scale-get-started.md) o póngase en contacto con Microsoft Support para evaluar sus opciones.

Microsoft también está cambiando la experiencia de copia de la base de datos con bases de datos de Premium. CREAR previamente como cuota de base de datos de premium estaba limitada, base de datos... COMO una copia de en SQL T crea una base de datos suspende Premium sin recursos reservados, que se cargará a la misma velocidad que una base de datos profesionales. Como cuota premium ahora más libremente disponible, suspende Premium ya no es compatible. Copias de la base de datos ahora se creará con la misma edición y el nivel de rendimiento como el origen y se cargará en consecuencia. Los clientes pueden elegir degradar bases de datos copiados a un nivel de rendimiento o nivel de servicio diferente para reducir su costo si lo desea. Bases de datos existentes suspendido Premium se convertirá a Business edition como parte de esta versión. Se prevé que la introducción del [Punto en el tiempo restaurar](sql-database-recovery-using-backups.md#point-in-time-restore) reducirá la necesidad de hacer copias de seguridad de bases de datos.

## <a name="how-does-basic-standard-and-premium-improve-my-billing-experience"></a>¿Cómo básico, estándar y Premium mejora la experiencia de facturación?

Básico, estándar, bases de datos de SQL Azure de Premium se cargarán por hora y tiene la capacidad de escalar cada base de datos hacia arriba o hacia abajo. Se cargarán en una tasa fija según el más alto nivel y rendimiento de nivel de servicio que elija para cada hora. Además, los niveles de rendimiento (ejemplo: Basic, S1 y P2) se dividen en la factura para que sea más fácil ver el número de días/horas de base de datos contraídos en un mes para cada nivel de rendimiento. Bases de datos Web y empresas seguirán facturará con unidades de base de datos en función del tamaño de la base de datos. Para obtener más información sobre las diferencias entre los niveles de servicio nueva y los precios, visite la [página de precios de base de datos de SQL](https://azure.microsoft.com/pricing/details/sql-database/) .


## <a name="see-also"></a>Vea también

[Base de datos SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)

[Web y precios de la empresa](https://azure.microsoft.com/pricing/details/sql-database/web-business/)

[Niveles de servicio](sql-database-service-tiers.md)

[Actualizar a V12 de base de datos SQL Azure](sql-database-upgrade-server-portal.md)
