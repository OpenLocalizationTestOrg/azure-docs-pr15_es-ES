<properties
    pageTitle="Planear la actualización a V12 de base de datos de SQL | Microsoft Azure"
    description="Describe los preparativos y limitaciones implicados en la actualización a la versión V12 de Azure base de datos SQL."
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
    ms.date="10/24/2016"
    ms.author="genemi"/>


# <a name="plan-and-prepare-to-upgrade-to-sql-database-v12"></a>Planear y prepararse para actualizar a V12 de base de datos de SQL


Este tema describe la planificación y la preparación que debe realizar para actualizar las bases de datos de SQL Azure de versión V11 a V12.


Un nuevo [Portal de Azure](https://portal.azure.com/) está disponible para la actualización a V12.


La siguiente tabla enumeran otros temas de ayuda para V12.


| Título y un vínculo | Descripción del contenido |
| :--- | :--- |
| [Novedades de V12 de base de datos de SQL](sql-database-v12-whats-new.md) | Describe los detalles de cómo V12 hace más cerca de SQL Azure Azure paridad completa con Microsoft SQL Server. |
| [Crear una base de datos en V12 de base de datos de SQL](sql-database-get-started.md) | Describe cómo crear una nueva base de datos de SQL Azure en la versión V12. Describe diversas opciones más allá de solo una base de datos vacía. |


## <a name="plan-ahead"></a>Planificar con antelación


Las subsecciones siguientes describen la toma de decisiones que debe resolver antes de realizar una acción para actualizar la base de datos de SQL Azure a V12 y aprendizaje.

### <a name="version-clarification"></a>Explicación de la versión


Este documento se refiere a la actualización de la base de datos de SQL de Microsoft Azure de versión V11 a V12. Más formal los números de versión están cerca de los siguientes dos valores, notificado por la instrucción Transact-SQL **seleccione @@version; ** :


- 12.0.2000.8 *(o un bit superior, V12)*
- 11.0.9228.18 *(V11)*
 - A veces V11 fue también conocida como SAWA v2.

### <a name="service-tier-planning"></a>Plan de nivel de servicio


Comenzando con V12, base de datos de SQL Azure admitirá sólo los niveles de servicio denominados básico, estándar y Premium. No se admite el nivel de servicio Web y empresa en V12. Por lo tanto, si tiene previsto actualizar la base de datos de SQL Azure que está actualmente en el nivel de servicio Web o empresa, debe decidir qué debe tener su nuevo nivel de servicio.


Para obtener información detallada sobre los niveles de servicio básico, estándar y Premium, consulte:

- [Niveles de servicio de base de datos SQL](sql-database-service-tiers.md)
- [Actualizar bases de datos de negocio Web y de base de datos SQL a nuevos niveles de servicio](sql-database-upgrade-server-portal.md)



### <a name="review-the-geo-replication-configuration"></a>Revisar la configuración de replicación Geo


Si la base de datos de SQL Azure está configurada para replicación Geo, debe documentar su configuración actual y detener la replicación de Geo, antes de iniciar las acciones de preparación de actualización. Después de que finalice la actualización debe volver a configurar la base de datos para la replicación de Geo.


La estrategia es dejar intacto el origen y probar en una copia de la base de datos.


## <a name="preparation-actions"></a>Acciones de preparación


Cuando haya terminado la planificación, puede realizar los pasos de acción que se describen en las subsecciones siguientes para prepararse para la fase de actualización final.


Una descripción detallada de la fase de actualización final está disponible en los temas de ayuda que estén vinculadas a la parte superior de este tema de ayuda.


### <a name="service-tier-actions"></a>Acciones de nivel de servicio


No se admite el servicio Web y empresa precios nivel en V12.


Si la base de datos de SQL Azure de V11 es una base de datos Web o empresa, ofrece el proceso de actualización cambiar la base de datos a un nivel compatible. La actualización recomienda un nivel que se ajusta el historial de cargas de trabajo de la base de datos. Sin embargo, puede elegir cualquier nivel compatible que le guste.


Puede reducir los pasos necesarios durante la actualización cambiando la base de datos V11 lejos el nivel de Web y empresa antes de iniciar la actualización. Puede hacerlo con el nuevo [Portal de Azure](https://portal.azure.com/).


Si no está seguro de qué nivel de servicio para cambiar a, el nivel de S2 del nivel estándar puede ser una elección razonable inicial. Cualquier nivel menor tendrá menos recursos que el nivel de Web y empresa tenía.


### <a name="suspend-geo-replication-during-upgrade"></a>Suspender la replicación de Geo durante la actualización


No puede ejecutar la actualización a V12 si Geo replicación está activa en la base de datos. En primer lugar debe volver a configurar la base de datos para dejar de usar Geo replicación.


Una vez finalizada la actualización puede configurar la base de datos para volver a usar Geo replicación.


### <a name="open-ports-on-an-azure-vm-for-client-connectivity"></a>Puertos abiertos en una máquina virtual de Azure la conectividad de cliente


Si el programa cliente se conecta a V12 de base de datos de SQL mientras el cliente se ejecuta en una máquina virtual (VM) en Azure, debe abrir los siguientes intervalos de puerto en la máquina virtual:

- 11000 11999
- 14000 a 14999


Haga clic [aquí](sql-database-develop-direct-route-ports-adonet-v12.md) para obtener detalles acerca de los puertos para V12 de base de datos de SQL. Los puertos son necesarias mejoras de rendimiento en V12 de base de datos de SQL.


##<a id="limitations"></a>Limitaciones durante y después de actualizar a V12


### <a name="portals-for-v12"></a>Portales para V12


Hay tres portales de Azure y cada una tiene distintas capacidades respecto V12 de base de datos de SQL.


- [http://portal.Azure.com/](https://portal.azure.com/)<br/>Este Portal Azure es nuevo y sigue en estado de vista previa. Este portal no está todavía en disponibilidad de total General (GA). Este portal:
 - Puede administrar su servidor de V12 y la base de datos.
 - Puede actualizar su base de datos de V11 V12.


- [http://Manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>Este Portal clásico de Azure podría ser desaparezca. Este portal:
 - Puede administrar su servidor de V12 y la base de datos.
 - Puede *no* actualización de la base de datos de su V11 a V12.


- (http://*suServidor*. database.windows.net)<br/>Portal clásico de base de datos SQL Azure:
 - Puede*no* administrar servidores V12.


Le recomendamos que conectarse a las bases de datos de SQL Azure con Visual Studio 2015 (VS2015). VS2015 puede utilizarse para tareas como las siguientes:


- Para ejecutar una instrucción Transact-SQL.
- Para diseñar un esquema.
- Desarrollar una base de datos, en línea o sin conexión.


O, en su lugar, de forma gratuita puede descargar [2015 de comunidad de Visual Studio](https://www.visualstudio.com/vs/community/), que es una versión completa de VS2015.


En el Portal antiguos de Azure clásica, en la página de la base de datos, puede hacer clic en **Abrir en Visual Studio** para iniciar VS2015 en el equipo para la conexión a la base de datos de SQL Azure.


Para otra alternativa, puede usar SQL Server Management Studio (SSMS) 2014 con [CU6](http://support.microsoft.com/kb/3031047/) para conectarse a la base de datos de SQL Azure. Más detalles se encuentran en esta entrada de blog:<br/>[Actualizaciones de la base de datos de SQL Azure de herramientas de cliente](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


> [AZURE.IMPORTANT] Se recomienda usar siempre la versión más reciente de Management Studio permanezca sincronizado con las actualizaciones de Microsoft Azure y base de datos de SQL. [Actualizar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


### <a name="limitation-during-upgrade-to-v12"></a>Limitación *durante la* actualización a V12


La base de datos de V11 sigue estando disponible para el acceso a los datos durante la actualización a V12. Sin embargo, existen dos limitaciones a tener en cuenta.


| Limitación | Descripción |
| :--- | :--- |
| Duración de actualización | La duración de una actualización depende del tamaño, la edición y el número de bases de datos en el servidor. El proceso de actualización puede ejecutar horas a días para servidores especialmente para los servidores que tiene bases de datos:<br/><br/>*Mayor de 50 GB o<br/> * En un nivel de servicio no premium<br/><br/>Creación de nuevas bases de datos en el servidor durante la actualización también puede aumentar la duración de la actualización. |
| Sin replicación Geo | No se admite la replicación de geo en un servidor de V12 actualmente participa en una actualización de V11. |
| Base de datos está brevemente disponible en la fase final de la actualización a V12 | Las bases de datos que pertenecen a su servidor V11 siguen estando disponibles durante el proceso de actualización. Sin embargo, la conexión con el servidor y las bases de datos es brevemente disponible en la fase final, cuando el modificador sobre comienza desde V11 a la V12 listo.<br/><br/>Cambiar largo período puede variar de 40 segundos a 5 minutos. Para la mayoría de los servidores, cambiar sobre debe completar dentro de 90 segundos. Cambiar el tiempo aumenta para servidores que tiene un gran número de bases de datos o cuando las bases de datos tienen cargas de trabajo de escritura pesados. |


### <a name="limitation-after-upgrade-to-v12"></a>Limitación de *después de* actualizar a V12


| Limitación | Descripción |
| :--- | :--- |
| No podrá volver a V11 | Después de un actualización en contexto, el resultado no se pueden invertir ni deshacer. |
| Nivel de Web o empresa | Una vez que la actualización se inicia, el servidor para la nueva base de datos de V12 ya no pueda reconocer o acepte el nivel de servicio Web o empresa. |



### <a name="export-and-import-after-upgrade-to-v12"></a>Exportar e importar *después de* actualizar a V12


Puede exportar o importar una base de datos de V12 mediante el [Portal de Azure](https://portal.azure.com/). O bien, puede exportar o importar con cualquiera de las siguientes herramientas:


- SQL Server Management Studio (SSMS)
- Visual Studio de 2015
- Marco de la aplicación de nivel de datos (DacFx)


Sin embargo, para usar las herramientas, primero debe tener o instalar las actualizaciones más recientes para asegurarse de que admiten las nuevas características de V12:


- [Actualización acumulativa 6 para SQL Server Management Studio de 2014](http://support2.microsoft.com/kb/3031047)
- [Actualización de febrero de 2015 para la base de datos SQL Server de herramientas de Visual Studio de 2013](https://msdn.microsoft.com/data/hh297027)
- [Marco de aplicación de nivel de datos (DacFx) de febrero de 2015 para V12 de base de datos SQL Azure](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] Se actualizan los vínculos de herramienta anteriores de 2 de marzo de 2015 o después. Le recomendamos que use estas actualizaciones más recientes de estas herramientas.


#### <a name="automated-export"></a>Exportación automatizada


Exportación automatizada sigue estando disponible como vista previa.  No hay actualizaciones de la herramienta de cliente son necesarios para usar automatizado exportar.  Si elige el archivo resultante e importar a un servidor local, las actualizaciones de herramientas mencionadas son necesarias para importar correctamente.


### <a name="restore-to-v12-of-a-deleted-v11-database"></a>Restaurar a V12 una base de datos eliminado V11

El siguiente escenario explica que se puede restaurar una base de datos de SQL Azure de V11 eliminada en un servidor de base de datos de SQL de V12 Azure.

1. Suponga que tiene un servidor de base de datos de SQL de V11 Azure. <br/> En el servidor tiene una base de datos en el nivel de servicio Web y empresa obsoleto.
2. Eliminar la base de datos.
3. Actualizar el servidor a V12.
4. A continuación se restaura la base de datos en el servidor. <br/> La base de datos, con ello, se actualiza a V12, en el nivel de S0 del nivel de servicio estándar.
5. Puede cambiar la base de datos a cualquier nivel de servicio admitido si S0 no es su preferencia.


### <a name="powershell-cmdlets"></a>Cmdlets de PowerShell


Cmdlets de PowerShell están disponibles para iniciar, detener o supervisar una actualización a V12 de base de datos de SQL Azure de V11 o cualquier otra versión de pre-V12.

- [Actualización a V12 de base de datos de SQL con PowerShell](sql-database-upgrade-server-powershell.md)

Documentación de referencia acerca de estos cmdlets de PowerShell, vea:


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Inicio AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Detener AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


El cmdlet Stop significa Cancelar, no haga una pausa. No hay ninguna manera de reanudar una actualización, que no sea de nuevo desde el principio. El cmdlet Stop limpia y libera todos los recursos adecuados.


## <a name="failure-resolution"></a>Resolución de errores


Si la actualización falla por cualquier motivo impar, la base de datos de V11 permanece activa y disponible como normal.


## <a name="related-links"></a>Vínculos relacionados


- [Características de vista previa](https://azure.microsoft.com/services/preview/) de Microsoft Azure


<!--Anchors-->
[Subheading 1]: #subheading-1
