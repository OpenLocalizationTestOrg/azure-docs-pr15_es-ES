<properties
   pageTitle="Notas de la versión de catálogo de datos Azure | Microsoft Azure"
   description="Notas de la versión para el catálogo de datos de Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-release-notes"></a>Notas de la versión de catálogo de datos Azure

## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Notas de 20 de noviembre de 2015 la versión del catálogo de datos de Azure

### <a name="opening-data-sources-in-power-bi-desktop"></a>Orígenes de datos de apertura en Power BI Desktop

Cuando se usa la opción "Abrir en Power BI Desktop" desde el portal de **Catálogo de datos de Azure** , los usuarios pueden encontrar uno de dos problemas en la aplicación de escritorio de Power BI:

- Se muestra un cuadro de diálogo con el título "No se puede a abrir documento"
- Se abre la aplicación Power BI Desktop, pero el archivo parece vacío

Para cada caso, se puede resolver el problema al descargar e instalar la última versión de Power BI Desktop de [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Notas de 13 de noviembre de 2015 la versión del catálogo de datos de Azure

### <a name="registering-and-connecting-to-teradata"></a>Registrar y conectarse a Teradata

Al conectarse a orígenes de datos de Teradata deben tener instalado al controlador ODBC Teradata correcto a los usuarios que coinciden con los bits (32 bits o 64 bits) del software que se usa.

En esta fecha de lanzamiento ADC, el más reciente [controlador ODBC Teradata para windows (versión 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) es compatible con Office 2013, pero no con Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Notas de 13 de julio de 2015 la versión del catálogo de datos de Azure

### <a name="registering-and-connecting-to-oracle-database"></a>Registrar y conectarse a la base de datos de Oracle

Al conectarse a orígenes de datos de base de datos de Oracle usuarios deben tener instalado a los controladores correctos de Oracle que coinciden con los bits (32 bits o 64 bits) del software que se usa.

-   Al registrar los orígenes de datos de Oracle en un equipo con Windows de 32 bits, se usan los controladores de Oracle de 32 bits
-   Al registrar los orígenes de datos de Oracle en un equipo que ejecuta Windows de 64 bits, se usan los controladores de Oracle de 64 bits
-   Al conectarse a orígenes de datos de Oracle con Excel en un equipo que ejecuta la versión de 32 bits de Microsoft Office, incluido en Windows de 64 bits, los controladores de Oracle de 32 bits se utilizará
-   Al conectarse a orígenes de datos de Oracle con Excel en un equipo que ejecuta la versión de 64 bits de Microsoft Office, se usan los controladores de Oracle de 64 bits

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Registrar y conectarse a SQL Server Reporting Services

Soporte técnico para orígenes de datos de SQL Server Reporting Services (SSRS) está actualmente limitado los servidores de modo nativo. Soporte técnico para SSRS en modo de SharePoint se agregará en una versión posterior.

### <a name="opening-data-assets-in-excel"></a>Activos de datos de apertura en Excel

Al abrir los activos de datos en Microsoft Excel desde el portal de **Catálogo de datos de Azure** , los usuarios que le pregunte si con un cuadro de diálogo **Aviso de seguridad de Microsoft Excel** . Esto es estándar, pueden seleccionar usuarios y comportamiento esperado **Habilitar** para continuar.

Para obtener más información, vea [Habilitar o deshabilitar las alertas de seguridad acerca de vínculos y archivos de sitios Web sospechosos](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Configuración de proxy y la directiva y los datos de origen de registro

Los usuarios pueden encontrarse con una situación donde puede iniciar sesión en el portal del catálogo de datos de Azure, pero al intentar iniciar sesión en la herramienta de registro de origen de datos que se encuentra en un mensaje de error que impide que iniciar sesión.

Hay dos causas posibles para este comportamiento de problema:

**Causa 1: configuración de los servicios de federación de Active Directory** La herramienta de registro de origen de datos utiliza la autenticación de formularios para validar los inicios de sesión de usuario en Active Directory. Para iniciar sesión correctamente, debe habilitarse la autenticación de formularios en la directiva de autenticación Global un administrador de Active Directory.

En algunos casos, este error puede ocurrir únicamente cuando el usuario está en la red de la empresa o solo cuando el usuario se conecta desde fuera de la red de la compañía. La directiva de autenticación Global permite métodos de autenticación habilitarse por separado para intranet y extranets conexiones. Si no está habilitada la autenticación de formularios de la red desde la que el usuario se conecta, pueden producirse errores de inicio de sesión.

Para obtener más información, consulte [Configuración de directivas de autenticación](https://technet.microsoft.com/library/dn486781.aspx).

**Causa 2: configuración de proxy de red** Si la red corporativa utiliza un servidor proxy, es posible que la herramienta de registro no podrá conectar con Azure Active Directory a través del proxy. Los usuarios pueden asegurarse de que la herramienta de registro editando el archivo de configuración de la herramienta, agregar esta sección al archivo:


      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Para localizar el archivo RegistrationTool.exe.config, inicie la herramienta de registro y, a continuación, abra la utilidad de administrador de tareas de Windows. En la ficha Detalles del Administrador de tareas, haga clic en RegistrationTool.exe y elija Abrir ubicación de archivo en el menú emergente.
