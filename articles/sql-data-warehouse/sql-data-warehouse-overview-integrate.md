<properties
   pageTitle="Crear soluciones integradas con el almacén de datos SQL | Microsoft Azure"
   description="Herramientas y asociados con las soluciones que se integran en el almacén de datos de SQL. "
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/31/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="leverage-other-services-with-sql-data-warehouse"></a>Aprovechar otros servicios de almacenamiento de datos de SQL
Además de su funcionalidad principal, almacenamiento de datos de SQL permite a los usuarios aprovechar muchos de los otros servicios en Azure junto a él.  Más concretamente, hemos tomado actualmente pasos para integrar de forma con los siguientes elementos:

+ Power BI
+ Generador de datos de Azure
+ Aprendizaje del equipo de Azure
+ Análisis de secuencia de Azure

Estamos trabajando para conectar con más servicios en el ecosistema de Azure.

##<a name="power-bi"></a>Power BI
Integración de Power BI le permite aprovechar la potencia de cálculo de almacén de datos de SQL con los informes dinámicos y de visualización de Power BI. Integración de Power BI actualmente incluye:

+ **Conexión directa**: más avanzadas de conexión con aplicar la lógica en almacén de datos de SQL.  Proporciona un análisis más rápido a mayor escala.
+ **Abrir en Power BI**: el botón 'Abrir en Power BI' pasa la información de instancia para Power BI, lo que permite una conexión más transparente.

Consulte la [documentación de Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx) para obtener más información o [integrar con Power BI](./sql-data-warehouse-integrate-power-bi.md) .

##<a name="azure-data-factory"></a>Generador de datos de Azure
Generador de datos de Azure ofrece a los usuarios una plataforma administrada para crear complejas canalizaciones extraer carga.  Integración del almacén de datos de SQL con el generador de datos de Azure incluye lo siguiente:

+ **Procedimientos almacenados**: organizar la ejecución de procedimientos almacenados en el almacén de datos de SQL.
+ **Copiar**: usar ADF para mover datos en el almacén de datos de SQL.  Esta operación puede usar mecanismo de movimiento de datos estándar del ADF o PolyBase en segundo plano. 

Vea la [documentación del generador de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/) para obtener más información o [integración con el generador de datos de Azure](./sql-data-warehouse-integrate-azure-data-factory.md) .

##<a name="azure-machine-learning"></a>Aprendizaje del equipo de Azure
Aprendizaje de máquina Azure es un servicio de análisis totalmente administrado que permite a los usuarios crear modelos complejos sacar provecho de un amplio conjunto de herramientas predictivas.  Almacén de datos SQL es compatible como origen y destino para estos modelos con las siguientes funciones:

+ **Leer datos:** Unidad modelos a escala con T-SQL contra el almacén de datos de SQL.
+ **Escribir datos:** Guardar los cambios de cualquier modelo al almacén de datos de SQL.

Consulte [integración con el aprendizaje Azure](./sql-data-warehouse-integrate-azure-machine-learning.md) o la [documentación de aprendizaje de Azure equipo](https://azure.microsoft.com/services/machine-learning/) para obtener más información.

##<a name="azure-stream-analytics"></a>Análisis de secuencia de Azure
Análisis de secuencia de Azure es una infraestructura compleja, totalmente gestionada para procesar y otros datos de eventos generados Azure evento concentrador de.  Permite la integración con el almacén de datos SQL para la transmisión de datos para un modo eficaz se procesan y almacenan junto a habilitar análisis más profundo, más avanzadas de datos relacionales.  

+ **Salida de trabajo:** Enviar resultados de trabajos de análisis de secuencia directamente al almacén de datos de SQL.

Consulte [integración con el análisis de secuencia de Azure](./sql-data-warehouse-integrate-azure-stream-analytics.md) o la [documentación de análisis de secuencia de Azure](https://azure.microsoft.com/documentation/services/stream-analytics/) para obtener más información.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->
