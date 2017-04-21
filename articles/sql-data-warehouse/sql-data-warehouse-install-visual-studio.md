<properties
   pageTitle="Instalar Visual Studio y SSDT para el almacén de datos SQL | Microsoft Azure"
   description="Instalar Visual Studio y las herramientas de desarrollo de SQL Server (SSDT) para el almacén de datos SQL Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="install-visual-studio-2015-and-ssdt-for-sql-data-warehouse"></a>Instalar 2015 de Visual Studio y SSDT para el almacén de datos SQL

Para desarrollar aplicaciones para el almacén de datos de SQL, se recomienda usar 2015 de Visual Studio con la versión más reciente de SQL Server Data Tools (SSDT).  5 de actualización de Visual Studio 2013 con SSDT también se admite por motivos de compatibilidad.  

Usar Visual Studio con SSDT le permitirá usar el Explorador de objetos de SQL Server visualmente Explorar tablas, vistas, procedimientos almacenados y muchos otros objetos en el almacén de datos de SQL, así como ejecutar consultas.

> [AZURE.NOTE] Almacén de datos SQL aún no admite proyectos de base de datos de Visual Studio.  Esta característica se agregará en una versión futura.

## <a name="step-1-install-visual-studio-2015"></a>Paso 1: Instalar Visual Studio de 2015

Siga estos vínculos para descargar e instalar Visual Studio de 2015. Si ya tiene 2013 de Visual Studio o 2015 instalado, puede omitir al paso 2, instalar SSDT.

1. [Descargar Visual Studio de 2015][].
2. Siga a la Guía de [Instalación de Visual Studio][] en MSDN y elija las configuraciones predeterminadas.

## <a name="step-2-install-ssdt"></a>Paso 2: Instalar SSDT

Para instalar SSDT para Visual Studio simplemente marque una actualización SSDT desde dentro de Visual Studio siguiendo estos pasos.

1. En Visual Studio, haga clic en **Herramientas** / **extensiones y actualizaciones...**  /  **Actualizaciones**
2. Seleccione **Las actualizaciones de productos** y, a continuación, busque **Herramientas de actualización de la base de datos de Microsoft SQL Server**

Si no encuentra una actualización, debe tener instalada la versión más reciente.  Para confirmar que está instalado SSDT, haga clic en **Ayuda** / **Acerca de Microsoft Visual Studio** y busque herramientas de datos de SQL Server en la lista.  La última versión de SSDT es 14.0.60525.0.  Si la opción de instalar no está disponible en Visual Studio, o bien puede visitar la página de [Descarga de SSDT][] para descargar e instalar SSDT manualmente.

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene la última versión de SSDT, está listo para [conectarse][] a su almacén de datos de SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[conectar]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Descargar Visual Studio de 2015]: https://www.visualstudio.com/downloads/
[Instalación de Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Descargar SSDT]: https://msdn.microsoft.com/library/mt204009.aspx
