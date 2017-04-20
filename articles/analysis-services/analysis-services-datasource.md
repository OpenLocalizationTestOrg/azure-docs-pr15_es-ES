<properties
   pageTitle="Conexiones de origen de datos | Microsoft Azure"
   description="Describe las conexiones de origen de datos de modelos de datos de Azure Analysis Services."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="owend"/>

# <a name="datasource-connections"></a>Conexiones de origen de datos

Modelos de datos de Azure Analysis Services pueden requerir proveedores de datos distintos al conectarse a determinados orígenes de datos. En algunos casos, los modelos tabulares de conectarse a orígenes de datos con proveedores nativos como SQL Server Native Client (SQLNCLI11) pueden devolver un error.

Por ejemplo; Si tiene en memoria o consulta directa del modelo de datos que se conecta a un origen de datos de nube como base de datos de SQL Azure, si usa proveedores nativos que no sea SQLOLEDB, puede ver el mensaje de error: **"El proveedor no está registrado 'SQLNCLI11.1'"**.

O bien, si tiene un modelo de DirectQuery conectarse a orígenes de datos local, si utiliza los proveedores nativos puede ver el mensaje de error: **"Error al crear el conjunto de filas OLE DB. Sintaxis incorrecta cerca de 'Límite' "**.

## <a name="data-source-providers"></a>Proveedores de datos de origen

Los siguientes proveedores de origen de datos son compatibles en memoria o dirigir modelos de datos de consulta cuando se conecte a local u orígenes de datos en la nube:

|               | **Origen de datos**                     | **En la memoria**                            |  **Consulta directa**                                           |
|---------------------------|-------------------------------|---------------------------------------------|---------------------------------------------|
| **Nube**                     | Almacén de datos SQL Azure      | Proveedor de datos de .NET framework para SQL Server | Proveedor de datos de .NET framework para SQL Server |
|                           | Base de datos SQL Azure            | Proveedor de datos de .NET framework para SQL Server | Proveedor de datos de .NET framework para SQL Server |
| **Local** (a través de la puerta de enlace) | SQL Server                    | SQL Server Native Client 11.0               | Proveedor de datos de .NET framework para SQL Server |
|                           |  SQL Server                             | Proveedor OLE DB de Microsoft para SQL Server    |   Proveedor de datos de .NET framework para SQL Server                                          |
|                           |  SQL Server                             | Proveedor de datos de .NET framework para SQL Server |  Proveedor de datos de .NET framework para SQL Server                                           |
|                           | Oracle                        | Proveedor OLE DB de Microsoft para Oracle        | Proveedor de datos de Oracle para .NET               |
|                           |  Oracle                             | Proveedor de datos de Oracle para .NET               | Proveedor de datos de Oracle para .NET                                            |
|                           | Teradata                      | Proveedor OLE DB para Teradata                | Proveedor de datos de Teradata para .NET             |
|                           |  Teradata                             | Proveedor de datos de Teradata para .NET             |  Proveedor de datos de Teradata para .NET                                            |
|                           | Sistema de plataforma de análisis | Proveedor de datos de .NET framework para SQL Server | Proveedor de datos de .NET framework para SQL Server |


> [AZURE.NOTE] Asegúrese de que están instalados los proveedores de 64 bits cuando se utiliza la puerta de enlace local.

Al migrar un modelo tabular de Analysis Services de SQL Server local a Azure Analysis Services, puede ser necesario cambiar el proveedor.

**Para especificar un proveedor de origen de datos**

1. En SSDT > **Explorador de modelos tabulares** > **Orígenes de datos**, haga clic en una conexión de origen de datos y, a continuación, haga clic en **Modificar origen de datos**.

2. **Editar conexión**, haga clic en **Avanzadas** para abrir la ventana de propiedades de avance.

3. En **Establecer propiedades avanzadas** > **proveedores**y, a continuación, seleccione el proveedor adecuado.

## <a name="impersonation"></a>Suplantación
En algunos casos, puede ser necesario especificar una cuenta de suplantación distintos. Cuenta de suplantación se puede especificar en SSDT o SSMS.

Para los orígenes de datos local:

- Si utiliza la autenticación de SQL, suplantación debe ser la cuenta de servicio.
- Si utiliza la autenticación de Windows, establecer contraseña de usuario de Windows. Para SQL Server, autenticación de Windows con una cuenta de suplantación específica es compatible únicamente en la memoria para modelos de datos.

Para orígenes de datos de la nube:

- Si utiliza la autenticación de SQL, suplantación debe ser la cuenta de servicio.


## <a name="next-steps"></a>Pasos siguientes

Si dispone de orígenes de datos locales, asegúrese de instalar la [puerta de enlace local](analysis-services-gateway.md). Para obtener más información sobre la administración de su servidor en SSDT o SSMS, consulte [administrar su servidor](analysis-services-manage.md).
