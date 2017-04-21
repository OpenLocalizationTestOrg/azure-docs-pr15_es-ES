<properties
    pageTitle="¿Qué es el SDK de .NET de Azure"
    description="Obtenga información sobre lo que se incluye en el SDK de .NET de Azure."
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor="mollybos"
    services=""/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="rachelap"/>

# <a name="what-is-the-azure-sdk-for-net"></a>¿Qué es el SDK de Azure para .NET?

## <a name="overview"></a>Información general

El SDK de Azure para .NET es un conjunto de herramientas de Visual Studio, herramientas de línea de comandos, archivos binarios de tiempo de ejecución y bibliotecas de cliente que le ayudarán a desarrollar, probar e implementación aplicaciones que se ejecutan en Azure. Este artículo describe lo que se obtiene al instalar el SDK. Puede descargar el SDK desde la [página de descargas de Azure](https://azure.microsoft.com/downloads/).

El SDK de Azure para .NET también incluye [bibliotecas de cliente de servicios de Azure consumir](http://go.microsoft.com/fwlink/?LinkId=510472). Estas bibliotecas se instalan por separado mediante NuGet.

##<a id="included"></a>¿Qué se incluye en el SDK de Azure para .NET

El SDK de Azure para .NET instala los siguientes productos:

- [Edición de la Comunidad de Visual Studio de 2015](#vwd)
- [Microsoft Azure almacenamiento emulador](#stgemulator)
- [Herramientas de Microsoft Azure almacenamiento de información](#stgtools)
- [Herramientas de creación de Microsoft Azure](#auth)
- [Emulador de Microsoft Azure](#emulator)
- [Herramientas de HDInsight para Visual Studio y Microsoft subárbol controlador ODBC](#hdinsight)
- [Bibliotecas de Microsoft Azure para .NET](#libraries)
- [SDK de aplicación móvil de Microsoft Azure](#mobile)
- [PowerShell de Microsoft Azure](#ps)
- [Herramientas de Microsoft Azure de Microsoft Visual Studio](#tools)
- [Microsoft ASP.NET y Web Tools para Visual Studio](#wte)
- [Lago de datos de Microsoft Azure Tools para Visual Studio](#datalake)

###<a id="vwd"></a>Edición de la Comunidad de Visual Studio de 2015

Si no tiene Visual Studio en el equipo, el SDK instalará [Visual Studio Comunidad Edition 2015](https://www.visualstudio.com/products/visual-studio-community-vs).

###<a id="stgemulator"></a>Microsoft Azure almacenamiento emulador

El [Emulador de almacenamiento de Azure](http://msdn.microsoft.com/library/hh403989.aspx) utiliza una instancia de SQL Server y el sistema de archivos local para simular almacenamiento de Azure (colas, tablas, BLOB), por lo que puede probar localmente.

###<a id="stgtools"></a>Herramientas de Microsoft Azure almacenamiento de información

Esto instala [AzCopy](http://aka.ms/AzCopy), una herramienta de línea de comandos que puede utilizar para transferir datos hacia y desde una cuenta de almacenamiento de Azure.

###<a id="auth"></a>Herramientas de creación de Microsoft Azure

Esto incluye lo siguiente:

* La [herramienta de línea de comandos de CSPack](http://msdn.microsoft.com/library/gg432988.aspx) para crear paquetes de distribución.
* la [herramienta de línea de comandos CSEncrypt](http://msdn.microsoft.com/library/hh404001.aspx) para cifrar las contraseñas que se usan para tener acceso a las instancias de función de servicio de nube a través de una conexión a Escritorio remoto.
* Archivos binarios de tiempo de ejecución que proyectos de servicio de nube requieren para comunicarse con su entorno de tiempo de ejecución y diagnósticos de. Estos archivos binarios no están disponibles en paquetes de NuGet.

###<a id="emulator"></a>Emulador de Microsoft Azure

El [Emulador de Azure](http://msdn.microsoft.com/library/dn339018.aspx) simula el entorno de servicio de nube para que puede probar proyectos de servicio de nube localmente en el equipo antes de implementar en Azure.

###<a id="hdinsight"></a>Herramientas de HDInsight para Visual Studio y Microsoft subárbol controlador ODBC

Herramientas HDInsight en el Explorador de servidor le permiten desplazarse por las bases de datos de la sección y cuentas de almacenamiento vinculado para clústeres HDInsight, crear tablas y crear y enviar consultas de la sección. Para obtener más información, vea [Introducción al uso HDInsight Hadoop Tools para Visual Studio](hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

###<a id="libraries"></a>Bibliotecas de Microsoft Azure para .NET

Esto incluye lo siguiente:

* Paquetes de NuGet para el almacenamiento de Azure, Bus de servicio y almacenamiento en caché que se almacenan en el equipo para que Visual Studio puede crear nube nuevos proyectos de servicio mientras sin conexión.
* Un complemento de Visual Studio que permite la [Caché en función](http://msdn.microsoft.com/library/dn386103.aspx) de proyectos ejecutar localmente en Visual Studio.

###<a id="mobile"></a>SDK de aplicación móvil de Microsoft Azure

Herramientas para trabajar con [Aplicaciones de Azure aplicación de servicio móvil](app-service-mobile/app-service-mobile-value-prop.md).

###<a id="ps"></a>PowerShell de Microsoft Azure

PowerShell Azure le permite [automatizar la creación de un entorno Azure e implementación](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

###<a id="tools"></a>Herramientas de Microsoft Azure de Microsoft Visual Studio

Esto le permite trabajar con los recursos, principalmente servicios en la nube y máquinas virtuales de Windows Azure:

* [Crear, abrir y publicar los proyectos de servicio de nube](cloud-services/cloud-services-dotnet-get-started.md).
* [Crear los paquetes de implementación de proyectos de servicio de nube](http://msdn.microsoft.com/library/ff683672.aspx).
* [Crear máquinas virtuales de Azure al crear nuevos proyectos web](virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md).
* [Las secuencias de comandos de PowerShell crear al crear nuevas máquinas virtuales](http://msdn.microsoft.com/library/dn642480.aspx).
* [Ver y administrar la configuración del proyecto de servicio de nube de windows de propiedades del proyecto de Visual Studio](http://msdn.microsoft.com/library/ee405486.aspx).
* Ver y administrar [servicios en la nube](http://msdn.microsoft.com/library/ff683675.aspx), [máquinas virtuales](http://msdn.microsoft.com/library/jj131259.aspx)y [Bus de servicio](http://msdn.microsoft.com/library/jj149828.aspx) en el Explorador de servidores.
* [Ejecutar en modo de depuración remota para los servicios en la nube y máquinas virtuales](http://msdn.microsoft.com/library/ff683670.aspx).
* [Automatizar el aprovisionamiento de recursos con proyectos de implementación de grupo de recursos de Azure](https://msdn.microsoft.com/library/dn872471.aspx)

###<a id="wte"></a>Herramientas de servicio de aplicaciones de Microsoft para Visual Studio

Esto le permite trabajar con sitios Web de Azure:

* [Proyectos de publicación web a sitios Web de Azure](app-service-web/web-sites-dotnet-get-started.md).
* [Publicar proyectos de aplicación de consola a WebJobs de Azure](app-service-web/websites-dotnet-deploy-webjobs.md).
* [Recursos de sitio Web de Azure crear y base de datos SQL al crear un nuevo proyecto web o al publicar un proyecto de web](app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
* [Secuencias de comandos de PowerShell Crear implementación al crear nuevos sitios Web](http://msdn.microsoft.com/library/dn642480.aspx).
* [Administrar y solucionar problemas de sitios Web de Azure en Explorador de servidores](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement).
* [Ejecutar en modo de depuración de forma remota para sitios Web y WebJobs](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug).

>[AZURE.NOTE] No tiene que instalar el SDK de Azure para .NET y usar estas características; También se incluyen en actualizaciones de Visual Studio.

##<a id="datalake"></a>Lago de datos de Microsoft Azure Tools para Visual Studio

Para obtener más información, vea [Tutorial: desarrollar secuencias de comandos de SQL U con datos lago Tools para Visual Studio](data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

##<a id="notincluded"></a>Lo que no se incluye al instalar el SDK de Azure para .NET

Hay algunas cosas que desee para el desarrollo de Azure y que no se incluyen al instalar el SDK. El más importante es las siguientes:

* [Bibliotecas de cliente](http://go.microsoft.com/fwlink/?LinkId=510472).

    El SDK de Azure incluye bibliotecas de cliente de otros servicios de Azure, pero no todas ellas se instalan al instalar el SDK. Si necesita una biblioteca de cliente que no se instala el SDK de la aplicación, se puede ir desde [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Si su aplicación utiliza una biblioteca de cliente que instalar el SDK, es una buena práctica actualizar con la versión actual en NuGet.org.

    **Copias locales de las bibliotecas de cliente.** El SDK de Azure para .NET se copia en el equipo los paquetes de NuGet para algunas bibliotecas de cliente de Azure, como almacenamiento, Bus de servicio de almacenamiento en caché. Estas bibliotecas cliente se incluyen automáticamente en los nuevos proyectos de servicio de nube, para que los paquetes de NuGet locales habilitar Visual Studio para crear proyectos, aunque no esté conectado a Internet. Bibliotecas de cliente generalmente se actualizan con más frecuencia se publican nuevas versiones SDK, para las bibliotecas de cliente en NuGet.org suelen ser más reciente que obtiene con el SDK.

    **Plantillas de proyectos que incluyen las bibliotecas de cliente.** Plantillas de proyecto solo [Servicio de nube de Azure](cloud-services/cloud-services-dotnet-get-started.md) y servicio de la aplicación de Azure [Aplicaciones Mobile](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) incluyen automáticamente algunas bibliotecas de cliente. Para otras bibliotecas o en otras plantillas, instale los [paquetes de NuGet biblioteca cliente](http://go.microsoft.com/fwlink/?LinkId=510472) que necesita.

* [Plantillas de proyecto de aplicaciones móviles](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

    Plantillas de aplicaciones móviles están disponibles solo en Visual Studio 2013 actualización 2 y posterior. No están disponibles en Visual Studio 2012 o versiones anteriores pero no en Visual Studio 2013 actualización 1 o anterior, incluso si se instala el SDK de Azure para .NET.

##<a id="faq"></a>Preguntas más frecuentes

- [Muchas características de Azure ya están en Visual Studio. ¿Debo instalar el SDK de Azure para .NET?](#azinvs)
- [Quiero una biblioteca de cliente. ¿Tengo que instalar el SDK de Azure para .NET conseguirlo?](#clientlib)
- [¿Dónde se puede encontrar las versiones anteriores de Azure SDK para .NET?](#olderversions)
- [¿Qué es la directiva de ciclo de vida de versiones del SDK para .NET Azure?](#lifecycle)
- [¿Qué versiones del sistema operativo invitado es el SDK de Azure para .NET compatibles con?](#guestos)
- [¿Cómo se puede desinstalar el SDK de Azure para .NET?](#uninstall)

###<a id="azinvs"></a>Muchas características de Azure ya están en Visual Studio. ¿Debo instalar el SDK de Azure para .NET?

Es una buena práctica para instalar el SDK si desea desarrollar para Azure con las herramientas más recientes. Si se prefiere no instalar el SDK, puede hacerlo si se cumplen las condiciones siguientes:

* Ha instalado la última [Actualización de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5).
* Desarrolla solo para los sitios Web de Azure o servicios de móvil, no para los servicios de nube o máquinas virtuales de Windows.
* Su aplicación no utiliza almacenamiento, o utiliza almacenamiento pero no es necesario el emulador de almacenamiento o la herramienta de AzCopy.

###<a id="clientlib"></a>Quiero una biblioteca de cliente. ¿Tengo que instalar el SDK de Azure para .NET conseguirlo?

El SDK instala bibliotecas de cliente sólo para que pueda crear nube proyectos de servicio aunque no esté conectado a Internet. Bibliotecas de cliente actual están disponibles en los paquetes de NuGet en [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Para obtener más información, vea [lo que no se incluye al instalar el SDK de Azure para .NET](#notincluded) anteriormente en este documento.

###<a id="olderversions"></a>¿Dónde se puede encontrar las versiones anteriores de Azure SDK para .NET?

Para las versiones anteriores, consulte que el [SDK de Azure para .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) página de descargas.

###<a id="lifecycle"></a>¿Qué es la directiva de ciclo de vida de versiones del SDK para .NET Azure?

Vea la [Directiva de ciclo de vida de soporte técnico de los servicios de nube de Microsoft Azure](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

###<a id="guestos"></a>¿Qué versiones del sistema operativo invitado es el SDK de Azure para .NET compatibles con?

Vea [versiones del sistema operativo invitado Azure y matriz de compatibilidad SDK](http://msdn.microsoft.com/library/ee924680.aspx).

###<a id="uninstall"></a>¿Cómo se puede desinstalar el SDK de Azure para .NET?

Cada elemento mostrado en este artículo en [lo que se incluye en el SDK de Azure para .NET](#included) es un programa independiente en la lista de programas instalados en el Panel de Control de Windows, **programas y características**.  No hay ninguna manera desinstalarlos como grupo; debe desinstalar cada programa individualmente.

Cuando tiene el SDK de Azure para .NET ya instalado e instalar una versión nueva, normalmente es necesario desinstalar el antiguo. En la mayoría de los casos, la instalación del SDK actualiza un programa existente en lugar de agregar una nueva y cambiar el antiguo.

Sin embargo, si desea quitar no más larga-necesario residuos de una versión anterior, desinstale únicamente los programas que especifican el número de versión anterior y desinstalar solo si está presente el mismo programa con una versión más reciente. Por ejemplo, después de actualizar de 2,5 a 2.6 es posible que vea versiones 2.5 y 2.6 de "Herramientas de Microsoft Azure de Microsoft Visual Studio 2013", y puede desinstalar la versión 2.5. Pero solo puede ver la versión 2.5 de "Herramientas de creación de Microsoft Azure" y no es seguro desinstalar.

Observe que los números de versión de títulos de los programas que se muestra en **programas y características** pueden ser confusos.  Por ejemplo, SDK versión 2.6 incluye "Microsoft Azure Mobile aplicación SDK versión 1.0" Si instala el SDK de 2013 de Visual Studio y "Microsoft Azure Mobile aplicación SDK versión 2.0" de Visual Studio de 2015; en este caso, la versión no es la versión SDK pero un indicador de que el programa de versión de Visual Studio se aplica a.

En este artículo no se enumeran todos los programas que incluyen todas las versiones anteriores de Azure SDK; hay otros programas que puede desinstalar de versiones anteriores de SDK, porque las versiones anteriores de SDK incluyen programas que se omiten de versiones posteriores. Por ejemplo, versión 2.5 instala "Herramientas del Administrador de recursos Azure para Visual Studio", pero que no está en la lista de este artículo porque ya no aparecerá como un programa independiente en **programas y características**.  Este artículo solo enumeran los programas que se incluyen en el SDK de Azure para .NET versión 2.6.

> **Nota:** Algunos de los programas que incluye el SDK también se pueden instalar por separado en otros contextos y pueden que se necesiten incluso si no necesita el SDK completo. El mismo puede ser verdadero de los programas que se instalaron con versiones anteriores de SDK, pero en versiones posteriores de SDK se omitieron. Al desinstalar programas, tenga cuidado de no quitar algo que son necesarios en el equipo.



##<a id="versions"></a>Versiones

Para ver qué versión es actual o para descargar las versiones anteriores, consulte la página de [Azure SDK para .NET historial de versiones](https://azure.microsoft.com/downloads/archive-net-downloads/) .

##<a id="resources"></a>Recursos

Para descargar el SDK de Azure actual para .NET o una biblioteca de cliente, consulte la [página de descargas de Azure](https://azure.microsoft.com/downloads/).

Para el SDK de Azure para código fuente de .NET, incluidas las bibliotecas de cliente, vea [GitHub.com/Azure](https://github.com/azure/).

Documentación de referencia de la biblioteca de cliente de Azure, vea [Referencia de .NET de Azure](https://azure.microsoft.com/documentation/api/).
