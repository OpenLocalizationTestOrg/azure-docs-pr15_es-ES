<properties
   pageTitle="Usar el análisis de secuencia de Azure con almacén de datos SQL | Microsoft Azure"
   description="Sugerencias para usar el análisis de secuencia de Azure con el almacén de datos de SQL Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Usar el análisis de secuencia de Azure con el almacén de datos SQL

Análisis de secuencia de Azure es un servicio de totalmente administrado proporcionar procesamiento de baja latencia, altamente disponible, scalable eventos compleja sobre la transmisión de datos en la nube. Aprenda los conceptos básicos lea [Introducción a análisis de secuencia de Azure][]. A continuación, aprenda cómo crear una solución de llevar a cabo con análisis de secuencia siguiendo el tutorial de [empezar a usar el análisis de secuencia de Azure][] .

En este artículo, aprenderá cómo usar la base de datos de almacén de datos de SQL Azure como un receptor de salida para los trabajos de análisis de vapor.

## <a name="prerequisites"></a>Requisitos previos

En primer lugar, ejecute los siguientes pasos en el tutorial de [empezar a usar el análisis de secuencia de Azure][] .  

1. Crear una entrada de concentrador de evento
2. Configurar e iniciar la aplicación del generador de eventos
3. Aprovisionar a un trabajo de análisis de secuencia
4. Especificar la entrada de trabajo y consulta

A continuación, cree una base de datos de almacén de datos de SQL Azure

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Especificar la salida de trabajo: base de datos de almacén de datos de SQL Azure

### <a name="step-1"></a>Paso 1

En el trabajo de análisis de secuencia haga clic en **resultados** de la parte superior de la página y, a continuación, haga clic en **Agregar resultado**.

### <a name="step-2"></a>Paso 2

Seleccione la base de datos de SQL y haga clic en siguiente.

![][add-output]

### <a name="step-3"></a>Paso 3
En la siguiente página, escriba los valores siguientes:

- *Alias de salida*: escriba un nombre descriptivo para este resultado de la tarea.
- *Suscripción*:
    - Si la base de datos de almacén de datos de SQL está en la misma suscripción como el trabajo de análisis de secuencia, seleccione Usar base de datos de SQL de suscripción actual.
    - Si la base de datos está en una suscripción diferente, seleccione Usar base de datos de SQL de otra suscripción.
- *Base de datos*: especifique el nombre de una base de datos de destino.
- *Nombre del servidor*: especifique el nombre del servidor para la base de datos especificada. Puede usar el Portal de Azure clásico para buscar lo siguiente.

![][server-name]

- *Nombre de usuario*: especifique el nombre de usuario de una cuenta que tenga permisos de escritura para la base de datos.
- *Contraseña*: proporcione la contraseña para la cuenta de usuario.
- *Tabla*: especifique el nombre de la tabla de destino en la base de datos.

![][add-database]

### <a name="step-4"></a>Paso 4

Haga clic en el botón Comprobar para agregar este resultado de la tarea y compruebe que el análisis de secuencia puede conectarse correctamente a la base de datos.

![][test-connection]

Cuando la conexión a la base de datos se realiza correctamente, verá una notificación en la parte inferior del portal. Puede hacer clic en Probar conexión en la parte inferior para probar la conexión a la base de datos.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información general de integración, consulte [Descripción de la integración de almacén de datos de SQL][].

Para obtener más sugerencias de desarrollo, vea [información general sobre el desarrollo de almacén de datos de SQL][].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introducción a análisis de secuencia de Azure]: ../stream-analytics/stream-analytics-introduction.md
[Empezar a usar el análisis de secuencia de Azure]: ../stream-analytics/stream-analytics-get-started.md
[Información general sobre el desarrollo de almacén de datos SQL]:  ./sql-data-warehouse-overview-develop.md
[Descripción de la integración de almacén de datos SQL]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/
