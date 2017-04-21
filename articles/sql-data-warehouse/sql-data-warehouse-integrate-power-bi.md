<properties
   pageTitle="Usar Power BI con el almacén de datos SQL | Microsoft Azure"
   description="Sugerencias para usar Power BI con el almacén de datos de SQL Azure para desarrollar soluciones."
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

# <a name="use-power-bi-with-sql-data-warehouse"></a>Usar Power BI con almacén de datos SQL
Como con la base de datos de SQL Azure, conexión directa de SQL datos almacén permite usuario aprovechar eficaces aplicar lógico junto con las capacidades analíticas de Power BI.  Con una conexión directa, las consultas se envían a su almacén de datos de SQL Azure en tiempo real como explorar los datos.  Esto, junto con la escala del almacén de datos de SQL, permite a los usuarios crear informes dinámicos en minutos de terabytes de datos.  Además, la introducción del botón Abrir en Power BI permite a los usuarios conectarse directamente Power BI a su almacén de datos de SQL sin recopilar información de otros elementos de Azure.

Al usar conexión directa, Nota:

+ Especifique el nombre de servidor completo al conectar (consulte a continuación para obtener más detalles)
+ Asegúrese de que las reglas de firewall para la base de datos están configuradas para "Permitir el acceso a servicios de Azure".
+ Cada acción como selección de una columna o agregar un filtro directamente consultará el almacén de datos
+ Mosaicos se actualizan cada 15 minutos aproximadamente (no es necesario actualizar programará)
+ Preguntas y respuestas no está disponible para la conexión directa de conjuntos de datos
+ Cambios de esquema no se seleccionen automáticamente
+ Todas las consultas de conexión directa le tiempo de espera después de 2 minutos

Pueden cambiar estas restricciones y notas a mejorar la experiencia. A continuación se detallan los pasos para conectar.  

## <a name="using-the-open-in-power-bi-button"></a>Con el botón "Abrir en Power BI"
Es la manera más sencilla de mover entre el almacén de datos SQL y Power BI con el botón Abrir en Power BI. Este botón le permite perfecta empezar a crear nuevos paneles en Power BI.  

1.  Para empezar a desplazarse a la instancia de almacén de datos SQL en el Portal de clásico de Azure.
2.  Haga clic en el botón "Abrir en Power BI".
3.  Si no puedes iniciar sesión directamente, o si no tiene una cuenta de Power BI, debe iniciar sesión.  
4.  Se le dirigirá a la página de conexión del almacén de datos SQL, con la información de su almacén de datos de SQL rellenado previamente.
5.  Después de escribir sus credenciales se totalmente conectará el almacén de datos de SQL.

## <a name="connecting-through-the-power-bi-portal"></a>Conectarse a través del portal de Power BI
Además de usar el botón Abrir en Power BI, los usuarios también pueden conectarse a su almacén de datos de SQL a través del Portal de Power BI.

1.  Haga clic en obtener datos en la parte inferior del panel de navegación.
2.  Seleccione 'Bases de datos'.
3.  Una vez en la página de bases de datos, seleccione 'Almacén de datos de SQL Azure' y, a continuación, haga clic en 'Conectar'.
4.  Escriba la información de conexión necesarios.  El nombre del servidor y el nombre de la base de datos pueden encontrarse en el Portal de Azure.
5.  Se dirigirá a la página principal de Power BI y después de realizar una nueva entrada en 'Conjuntos de datos' su conexión aparecerá con el nombre de la instancia.  
6.   Puede hacer clic en el nuevo conjunto de datos para explorar todas las tablas y vistas en la base de datos. Selección de una columna, le enviará una consulta en el origen, crear dinámicamente su visual. Estos elementos visuales pueden guardar en un nuevo informe y anclados vuelva al panel del.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
