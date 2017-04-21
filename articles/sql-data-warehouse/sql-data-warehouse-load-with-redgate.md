<properties
   pageTitle="Usar Studio de plataforma de datos de Redgate para cargar los datos en el almacén de datos SQL | Microsoft Azure"
   description="Obtenga información sobre cómo usar Studio de plataforma de datos de Redgate para escenarios de almacenamiento de datos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/13/2016"
   ms.author="mausher;barbkess"/>


# <a name="load-data-with-redgate-data-platform-studio"></a>Cargar datos con Redgate datos plataforma Studio

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)
- [Generador de datos](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

En este tutorial se muestra cómo usar [Studio de plataforma de datos de Redgate](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS) para mover los datos de un servidor de SQL local al almacén de datos de SQL Azure. Studio de plataforma de datos se aplica el más adecuado correcciones de compatibilidad y las optimizaciones, por lo que es la manera más rápida de empezar a trabajar con el almacén de datos de SQL.

> [AZURE.NOTE] [Redgate](http://www.red-gate.com) es un hora larga partner de Microsoft que proporciona varias herramientas de SQL Server. Esta característica en Studio de plataforma de datos se realizó disponible libremente para uso comercial y no comercial.

## <a name="before-you-begin"></a>Antes de empezar
### <a name="create-or-identify-resources"></a>Crear o identificar recursos

Antes de comenzar este tutorial, debe tener:

- **Base de datos local SQL Server**: los datos que desea importar al almacén de datos de SQL deben proceder de un servidor local SQL Server (versión 2008R2 o posterior). Datos plataforma Studio no puede importar datos directamente desde una base de datos de SQL Azure o archivos de texto.
- **Cuenta de almacenamiento de Azure**: datos plataforma Studio fases los datos en el almacenamiento de blobs de Windows Azure antes de cargarlo en el almacén de datos de SQL. La cuenta de almacenamiento debe estar usando el modelo de implementación de "Administrador de recursos" (predeterminado) en lugar del modelo de implementación "Clásico". Si no tiene una cuenta de almacenamiento, obtenga información sobre cómo crear una cuenta de almacenamiento. 
- **Almacén de datos SQL**: este tutorial mueve los datos del servidor de SQL Server local al almacén de datos de SQL, por lo que debe tener un almacén de datos en línea. Si no dispone de un almacén de datos, obtenga información sobre cómo crear un almacén de datos de SQL Azure.

> [AZURE.NOTE] Si la cuenta de almacenamiento y el almacén de datos se crean en la misma región mejora el rendimiento.

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>Paso 1: Iniciar sesión en Studio de plataforma de datos con su cuenta de Azure
Abra el explorador web y vaya al sitio Web [Studio de plataforma de datos](https://www.dataplatformstudio.com/) . Inicie sesión con la misma cuenta de Azure que usó para crear el almacén de datos y la cuenta de almacenamiento. Si su dirección de correo electrónico está asociado con un trabajo o escuela y una cuenta de Microsoft, asegúrese de elegir la cuenta que tiene acceso a los recursos.

> [AZURE.NOTE] Si esta es la primera vez que usa datos plataforma Studio, deberá conceder permiso de la aplicación para administrar los recursos de Azure.

## <a name="step-2-start-the-import-wizard"></a>Paso 2: Iniciar al Asistente para importación
En la pantalla principal de DPS, seleccione la importación de vínculo de almacén de datos de SQL Azure para iniciar al Asistente para importar.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>Paso 3: Instalar la puerta de enlace de datos plataforma Studio
Para conectarse a la base de datos de SQL Server local, debe instalar la puerta de enlace de DPS. La puerta de enlace es un agente de cliente que proporciona acceso a su entorno local, extrae los datos y carga a su cuenta de almacenamiento. Los datos nunca se pasan a través de los servidores de Redgate. Para instalar la puerta de enlace:

1.  Haga clic en el vínculo de **Crear la puerta de enlace**
2. Descargar e instalar la puerta de enlace con el programa de instalación proporcionado

![][2]

> [AZURE.NOTE] La puerta de enlace se puede instalar en cualquier equipo con acceso a la red a la base de datos de SQL Server de origen. Tener acceso a la base de datos de SQL Server que usan autenticación de Windows con las credenciales del usuario actual.

Una vez instalado, el estado de la puerta de enlace cambia a conectado y seleccione siguiente.

## <a name="step-4-identify-the-source-database"></a>Paso 4: Identificar la base de datos de origen
En el cuadro de texto *Escriba el nombre del servidor* , escriba el nombre del servidor que hospeda la base de datos y seleccione **siguiente**. A continuación, en el menú desplegable, seleccione la base de datos que desea importar los datos.

![][3]

DPS inspecciona la base de datos seleccionada para las tablas importar. De forma predeterminada, DPS importa todas las tablas de la base de datos. Puede seleccionar o anular la selección de tablas, expanda el vínculo de todas las tablas. Seleccione el botón siguiente para avanzar.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>Paso 5: Elegir una cuenta de almacenamiento para los datos de la fase
DPS le pide una ubicación organizar los datos. Elija una cuenta existente de almacenamiento de la suscripción y seleccione **siguiente**.

> [AZURE.NOTE] DPS creará un nuevo contenedor de blob en la cuenta de almacenamiento elegida y utilizar una carpeta distinta para cada importación.

![][4]

## <a name="step-6-select-a-data-warehouse"></a>Paso 6: Seleccione un almacén de datos
A continuación, seleccione una base de datos de [Almacén de datos de SQL Azure](http://aka.ms/sqldw) en línea para importar los datos. Una vez que haya seleccionado la base de datos, debe introducir las credenciales para conectarse a la base de datos y seleccione **siguiente**.

![][5]

> [AZURE.NOTE] DPS combina las tablas de datos de origen en el almacén de datos. DPS le avisa si el nombre de tabla requiere sobrescribir las tablas existentes en el almacén de datos. Opcionalmente, puede eliminar los objetos existentes en el almacén de datos haciendo clic en eliminar todos los objetos existentes antes de importar.

## <a name="step-7-import-the-data"></a>Paso 7: Importar los datos
DPS confirma que desea importar los datos. Simplemente haga clic en el botón de importación de inicio para comenzar la importación de datos.

![][6]

DPS muestra una visualización que muestra el progreso de la extracción y cargar los datos de SQL Server local y el progreso de la importación en el almacén de datos de SQL.

![][7]

Una vez completada la importación, DPS muestra un resumen de la importación de datos y un informe de cambio de las correcciones de compatibilidad que se han realizado.

![][8]

## <a name="next-steps"></a>Pasos siguientes
Para explorar los datos en el almacén de datos de SQL, empiece por la visualización:

- [Almacén de datos SQL Azure de consulta (Visual Studio)][]
- [Visualizar datos con Power BI][]

Para obtener más información sobre Studio de plataforma de datos de Redgate:

- [Visite la página principal del DPS](http://www.dataplatformstudio.com/)
- [Ver una demostración de DPS en Channel9](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Para obtener información general sobre otras formas de migrar y cargar los datos en el almacén de datos de SQL, vea:

- [Migrar la solución al almacén de datos de SQL][]
- [Cargar datos en el almacén de datos de SQL Azure](./sql-data-warehouse-overview-load.md)

Para obtener más sugerencias de desarrollo, vea la [información general sobre el desarrollo de almacén de datos de SQL](./sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Almacén de datos SQL Azure de consulta (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualizar datos con Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrar la solución al almacén de datos de SQL]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md