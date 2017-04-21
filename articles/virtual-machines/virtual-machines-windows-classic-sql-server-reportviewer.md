<properties 
    pageTitle="Usar ReportViewer en un sitio Web | Microsoft Azure"
    description="Este tema describe cómo crear un sitio Web de Windows Azure con dicho Visual Studio control que muestra un informe almacenado en una máquina Virtual de Microsoft Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management" />
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Usar ReportViewer en un sitio Web alojado en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Puede crear un sitio Web de Windows Azure con dicho Visual Studio control que muestra un informe almacenado en una máquina Virtual de Microsoft Azure. El control ReportViewer es en una aplicación Web que crear mediante la plantilla de aplicación Web de ASP.NET.

>[AZURE.IMPORTANT] Las plantillas de aplicación Web de ASP.NET MVC no admiten el control ReportViewer.

Para incorporar ReportViewer en su sitio Web de Windows Azure, debe completar las siguientes tareas.

- **Agregar** Ensamblados al paquete de implementación

- **Configurar** Autenticación y la autorización

- **Publicar** la aplicación Web de ASP.NET a Azure

## <a name="prerequisites"></a>Requisitos previos

Revise la sección "recomendación General y los procedimientos recomendados" en [SQL Server Business Intelligence en Azure máquinas virtuales de Windows](virtual-machines-windows-classic-ps-sql-bi.md).

>[AZURE.NOTE] Controles ReportViewer se envían con Visual Studio, Standard Edition o posterior. Si está utilizando Web Developer Express Edition, debe instalar el [Visor de MICROSOFT informe 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) para usar las características de tiempo de ejecución de ReportViewer.
>
>ReportViewer configurado en modo de procesamiento local no es compatible con Microsoft Azure.

Revise las notas del [control del Visor de informes de Reporting Services y máquina virtual de Microsoft Azure basado en servidores de informes](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx).

## <a name="adding-assemblies-to-the-deployment-package"></a>Agregar ensamblados al paquete de implementación

Cuando se hospeda la aplicación ASP.NET en local, los ensamblados de ReportViewer suelen instalarse directamente en la caché ensamblados global () del servidor IIS durante la instalación de Visual Studio y se pueden acceder directamente por la aplicación. Sin embargo, cuando se hospeda la aplicación de ASP.NET en la nube, Microsoft Azure no permite nada para instalarse en la GAC, por lo que debe asegurarse de que los ensamblados ReportViewer están disponibles localmente para la aplicación. Puede hacer esto mediante la adición de referencias a ellos en su proyecto y configurarlos para que se copian localmente.

En el modo de procesamiento remoto, el control ReportViewer usa los siguientes ensamblados:

- **Microsoft.ReportViewer.WebForms.dll**: contiene el código de ReportViewer, debe usar ReportViewer en la página. Cuando coloca un control ReportViewer en una página ASP.NET en su proyecto, se agrega una referencia para este ensamblado a su proyecto.

- **Microsoft.ReportViewer.Common.dll**: contiene clases utilizadas por el control ReportViewer en tiempo de ejecución. No se agrega automáticamente a su proyecto.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Para agregar una referencia a Microsoft.ReportViewer.Common

- Haga clic en nodo de **referencias** de su proyecto y seleccione **Agregar referencia**, seleccione el conjunto en la ficha .NET y haga clic en **Aceptar**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Los ensamblados localmente tener acceso a la aplicación de ASP.NET

1. En la carpeta **referencias** , haga clic en el conjunto de Microsoft.ReportViewer.Common para que se muestren sus propiedades en el panel Propiedades.

1. En el panel Propiedades, establezca **Copia Local** en True.

1. Repita los pasos 1 y 2 para Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Paquete de idioma de ReportViewer

1. Instalar el paquete redistribuible de Microsoft informe Visor 2012 Runtime adecuado desde el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=317386).

1. Seleccione el idioma de la lista desplegable y la página se redirige a la página del centro de descarga correspondiente.

1. Haga clic en **Descargar** para iniciar la descarga de ReportViewerLP.exe.

1. Después de descargar ReportViewerLP.exe, haga clic en **Ejecutar** para instalar inmediatamente, o haga clic en **Guardar** para guardarlo en su equipo. Si hace clic en **Guardar**, recuerde que el nombre de la carpeta donde se guarda el archivo.

1. Busque la carpeta donde guardó el archivo. Haga clic con el botón ReportViewerLP.exe, haga clic en **Ejecutar como administrador**y, a continuación, haga clic en **Sí**.

1. Después de ejecutar ReportViewerLP.exe, verá el c:\windows\assembly tiene el recurso archivos **Microsoft.ReportViewer.Webforms.Resources** y **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Para configurar para localizar el control ReportViewer

1. Descargue e instale el paquete redistribuible de Microsoft informe Visor 2012 Runtime siguiendo las instrucciones anteriores especificadas.

1. Crear <language> carpeta en el proyecto y copie el ensamblado de recursos asociados archivos allí. Los archivos de ensamblado de recursos se copien son: **Microsoft.ReportViewer.Webforms.Resources.dll** y **Microsoft.ReportViewer.Common.Resources.dll**. Seleccione los archivos de ensamblado de recursos y, en el panel Propiedades, establezca **Copiar en el directorio de salida** **Copiar siempre"**.

1. Establecer la referencia cultural & UICulture para el proyecto web. Para obtener más información sobre cómo configurar la referencia cultural y la referencia cultural de la interfaz de usuario de una página Web de ASP.NET, vea [Cómo: establecer la referencia cultural y la referencia cultural de la interfaz de usuario para la globalización de la página Web de ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Configuración de autenticación y la autorización

ReportViewer debe usar las credenciales adecuadas para autenticar con el servidor de informes y las credenciales deben estar autorizadas por el servidor de informes para tener acceso a los informes que desee. Para obtener información sobre la autenticación, consulte las notas del [control del Visor de informes de Reporting Services y máquina virtual de Microsoft Azure basado en servidores de informes](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publicar la aplicación Web de ASP.NET en Azure

Para obtener instrucciones sobre cómo publicar una aplicación Web de ASP.NET a Azure, vea [Cómo: migrar y publicar una aplicación Web en Azure desde Visual Studio](../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) y [empezar a trabajar con aplicaciones Web y ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).

>[AZURE.IMPORTANT] Si el comando de agregar un proyecto de implementación de Azure o agregar un proyecto de servicio de nube de Azure no aparece en el menú contextual del explorador de soluciones, debe cambiar el marco de destino del proyecto a .NET Framework 4.
>
>Los dos comandos proporcionan esencialmente la misma funcionalidad. Uno o el otro comando aparecerá en el menú contextual según la versión de Microsoft Azure SDK ha instalado.

## <a name="resources"></a>Recursos

[Informes de Microsoft](http://go.microsoft.com/fwlink/?LinkId=205399)

[Inteligencia empresarial de SQL Server en máquinas virtuales de Windows Azure](virtual-machines-windows-classic-ps-sql-bi.md)

[Usar PowerShell para crear una máquina virtual de Azure con un servidor de informes de modo nativo](virtual-machines-windows-classic-ps-sql-report.md)

[Control del Visor de informes de servicios y Microsoft Azure máquina virtual según servidores de informes](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)
