<properties
    pageTitle="Agregar un proveedor de recursos de aplicaciones Web a la pila de Azure | Microsoft Azure"
    description="Instrucciones detalladas para implementar aplicaciones Web de pila de Azure"
    services="azure-stack"
    documentationCenter=""
    authors="ccompy, apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>

# <a name="add-a-web-apps-resource-provider-to-azure-stack"></a>Agregar un proveedor de recursos de aplicaciones Web a la pila de Azure

> [AZURE.NOTE] La siguiente información solo se aplica a implementaciones TP1 de pila de Azure.

Agregar un proveedor de recursos de aplicaciones Web a la pila de Azure tiene siete pasos:

1.  Descargar los componentes necesarios.
2.  Crear certificados para usarlo con aplicaciones Web de pila de Azure.
3.  Utilice al instalador descargar, región e instalar aplicaciones Web de pila de Azure. 
4.  Validar la instalación de aplicaciones Web.
5.  Crear registros DNS para el Front-End y equilibradores de carga del servidor de administración.
6.  Registrar el proveedor de recursos de aplicaciones Web recién implementado con BRAZO.
7.  Pruebe el proveedor de recursos de aplicaciones Web.

## <a name="download-required-components"></a>Descargar componentes necesarios

1.  Descargar el [Instalador de vista previa de Azure pila de aplicación de servicio](http://aka.ms/azasinstaller). 
2.  Descargue los [scripts de auxiliar de implementación de Azure pila de aplicación de servicio](http://aka.ms/azashelper). 
3.  Extraer los archivos del archivo zip de secuencias de comandos de aplicación auxiliar, debe haber tres secuencias de comandos:
    - AppServiceCerts.ps1 crear
    - AppServiceDnsRecords.ps1 crear
    - Registrar AppServiceResourceProvider.ps1 

## <a name="create-certificates-to-be-used-by-azure-stack-web-apps"></a>Crear certificados para usarlo con aplicaciones Web de pila de Azure

En este primer script funciona con la entidad emisora de certificados de pila de Azure para crear los 3 certificados que son necesarios por aplicaciones Web. Ejecute la secuencia de comandos en el ClientVM asegurarse de que está ejecutando PowerShell como azurestack\administrator:
1.  En una sesión de PowerShell que se ejecuta como **azurestack\administrator**, ejecute la secuencia de comandos de **AppServiceCerts.ps1 de crear** .  Esto crea tres certificados en la misma carpeta que la secuencia de comandos, que son necesarios por aplicaciones Web.
2.  Escriba una contraseña para proteger los archivos pfx y tome nota del mismo como necesite escribir en el instalador de aplicaciones Web de pila de Azure.

## <a name="use-the-installer-to-download-and-install-azure-stack-web-apps"></a>Utilice al instalador para descargar e instalar las aplicaciones Web de pila de Azure

El instalador de appservice.exe hará lo siguiente:
1.  Preguntar al usuario que acepte los de Microsoft y otros fabricantes CLUF.
2.  Recopilar información de implementación de la pila de Azure.
3.  Crear un contenedor de blob en la cuenta de pila de Azure almacenamiento especificada.
4.  Descargar los archivos necesarios para instalar al proveedor de recursos de la aplicación Web de pila de Azure.
5.  Preparar la instalación para implementar el proveedor de recursos de la aplicación Web en el entorno de pila de Azure.
6.  Cargue los archivos a la cuenta de servicio de aplicación de almacenamiento.
7.  Presentar la información necesaria para empezar la plantilla de administrador de recursos de Azure.

Los siguientes pasos le guiará a través de las fases de instalación:

>[AZURE.NOTE] Debe usar una cuenta con privilegios elevados (Administrador local o de dominio) para ejecutar el programa de instalación. Si iniciar sesión como azurestack\azurestackuser, se le pedirá las credenciales con privilegios elevados. 

1.  Ejecutar appservice.exe como **azurestack\administrator**. 
2.  Haga clic en **implementar mediante el Administrador de recursos de Azure**.

![Instalador de Technical Preview 1 del servicio de aplicación de pila de Azure][1]

3.  Revise y acepte los términos de licencia de versión preliminar de Software de Microsoft y, a continuación, haga clic en **siguiente**.
4.  Revise y acepte los términos de partylicense de terceros y, a continuación, haga clic en **siguiente**.
5.  Revise la información de configuración de servicio de nube de aplicaciones y haga clic en **siguiente**.

![Configuración de nube de servicio de aplicación de pila Azure servicio de aplicación Technical Preview 1][2]

6. Haga clic en **Conectar** (junto al cuadro Azure pila suscripciones).

![Pantalla de configuración de pila Azure aplicación servicio Technical Preview 1 aplicación servicio nube dos][3]

7.  En la ventana de autenticación de pila de Azure proporcionar su **cuenta de administrador de servicios de Azure Active Directory** y la **contraseña**y, a continuación, haga clic en **Iniciar sesión**.
**Nota:** Esta es la cuenta de Azure Active Directory que facilitó al implementado pila de Azure.
    - Haga clic en la **Flecha abajo** en el lado derecho de la casilla junto a **Las suscripciones de pila de Azure** y, a continuación, seleccione la suscripción.

![Selección de suscripción de pila Azure aplicación servicio Technical Preview 1][5]

8.  Haga clic en la **Flecha abajo** en el lado derecho de la casilla junto a **Ubicaciones de la pila de Azure**.
    - Seleccione **Local**.
9. Escriba el **nombre** para el administrador.
10. Escriba una **contraseña** para el administrador.
11. Revise los **Detalles de SQL Server** y realice cambios si es necesario.
12. Revise la **Cuenta de inicio de sesión de administrador de sistema** y realice cambios si es necesario.
13. Escriba la **Contraseña Administrador del sistema**.
14. Haga clic en **siguiente**.  En este momento el instalador ahora comprobar los detalles de la conexión de SQL Server proporciona.

![Selección de suscripción de pila Azure aplicación servicio Technical Preview 1][4]    

15. Haga clic en **Examinar** junto al **Archivo de certificado SSL predeterminado de aplicaciones de Web** y vaya a las aplicaciones Web **. AzureStack.Local** certificado que [creó anteriormente](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
16. Escriba la **contraseña del certificado** que establecer cuando cree los certificados.
17. Haga clic en **Examinar** junto al **Archivo de certificado SSL de recursos proveedor** y vaya a la administración de **. AzureStack.Local** certificado que [creó anteriormente](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
18. Escriba la **contraseña del certificado** que establecer cuando cree los certificados.
19. Haga clic en **Examinar** junto al **Archivo de certificado de raíz de proveedor de recursos** y vaya a la administración de **. AzureStack.Local** certificado que [creó anteriormente](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
20. Haga clic en **siguiente** el instalador verificará la contraseña de certificado proporcionada.

![Detalles del certificado de Technical Preview 1 de servicio de aplicaciones de pila de Azure][6]

La implementación tardará aproximadamente 45 60 minutos en completar.

![Progreso de la instalación de la aplicación servicio pila Azure Technical Preview 1][7]

21. Después de que el programa de instalación finalice correctamente, haga clic en **Salir**.

## <a name="validate-web-apps-installation"></a>Valide la instalación de aplicaciones Web

1.  En el **Equipo de Host de pila de Azure** abrir el **Administrador de Hyper-V**.
2.  Busque la **VM CN0** y **conectarse** a la máquina virtual.
![Administrador de pila Azure aplicación servicio Technical Preview 1 Hyper-V][8]
3.  En el escritorio de este VM haga doble clic en la **Consola de administración de nube de Web**.
![Consola de administración de Technical Preview 1 de servicio de aplicaciones de pila de Azure][9]
4.  Vaya a **los servidores administrados**.
5.  Cuando todos los equipos son continuar **Listo** para el siguiente paso. 
![Estado de pila Azure aplicación servicio Technical Preview 1 servidores administrados][10]

## <a name="create-dns-records-for-the-management-server-and-front-end-load-balancers"></a>Crear registros DNS para el servidor de administración y equilibradores de carga de Front-End
1.  Abra una instancia de PowerShell como **azurestack\administrator**.
2.  Navegue hasta la ubicación de las secuencias de comandos descargado y extraído en el [paso como requisito previo](#Download-Required-Components).
3.  Ejecutar la secuencia de comandos **Crear AppServiceDnsRecords.ps1** , esto crea entradas DNS para permitir las solicitudes de aplicación web y portal de enrutamiento a los servidores front-de fondo.  Durante la implementación de BRAZO de aplicaciones Web, dos equilibradores de carga de Software (SLBs), que se crearon en el proveedor de recursos de red. Apunten a los servidores de administración y los servidores Front-End. El portal y solicitudes basado en ARM al proveedor de recursos de servicio de aplicación de pila de Azure vayan al servidor de administración.

## <a name="register-the-newly-deployed-azure-stack-web-apps-provider-with-arm"></a>Registrar el proveedor de aplicaciones Web de Azure pila recién implementado con BRAZO
1.  Abra una instancia de PowerShell como **azurestack\administrator**.
2.  Navegue hasta la ubicación de las secuencias de comandos descargado y extraído en el [paso como requisito previo](#Download-Required-Components).
3.  Ejecute la secuencia de comandos de **Registrar AppServiceResourceProvider.ps1** . 

>[AZURE.NOTE] Escriba el nombre de usuario y contraseña **exactamente (incluyendo mayúsculas y minúsculas)** que se ha insertado para el **Administrador de máquinas virtuales** y los campos de **contraseña** en la instalación o se producirá un error en el registro de proveedor de recursos.

## <a name="test-drive-azure-stack-web-apps"></a>Aplicaciones Web de prueba unidad pila de Azure

Ahora que ha implementado y registrado el proveedor de recursos de aplicaciones Web, puede probar para asegurarse de que los inquilinos pueden implementar aplicaciones web.

1.  En el portal de la pila de Azure, haga clic en nuevo, haga clic en Web + móvil y haga clic en la aplicación Web.
2.  En el módulo de la aplicación Web, escriba un nombre en el cuadro de la aplicación Web.
3.  En el grupo de recursos, haga clic en nuevo y, a continuación, escriba un nombre en el cuadro de grupo de recursos. 
4.  Haga clic en la ubicación o plan de servicio de aplicaciones y haga clic en crear nuevo.
5.  En el módulo de plan de servicio de aplicaciones, escriba un nombre en el cuadro de plan de servicio de aplicación.
6.  Haga clic en el nivel de precios, haga clic en liberar compartido o compartido compartido, haga clic en seleccionar, haga clic en Aceptar y, a continuación, haga clic en crear.
7.  En menos de un minuto, el icono de la nueva aplicación web aparecerá en el panel. Haga clic en el mosaico.
8.  En el módulo de aplicación web, haga clic en Examinar para ver el sitio Web predeterminado para esta aplicación.


**Implementar un sitio Web WordPress, DNN o Django (opcional)**

1. En el portal de la pila de Azure, haga clic en "+", vaya a Azure Marketplace, implementar un sitio Web de Django y espere a que finalice correctamente. La plataforma de web Django usa una base de datos basadas en sistema de archivos y no requiere que los proveedores de recursos adicionales como SQL o MySQL.  

2. Si también implementa un proveedor de recursos de MySQL, puede implementar un sitio Web de WordPress desde el catálogo de soluciones. Cuando se le solicite para los parámetros de la base de datos, introduzca el nombre de usuario como *User1@Server1* (con el nombre de usuario y el nombre del servidor de su elección).

3. Si también implementa un proveedor de recursos de SQL Server, puede implementar un sitio Web DNN desde el catálogo de soluciones. Cuando se le solicite para los parámetros de la base de datos, seleccione una base de datos en el equipo que ejecuta SQL Server que está conectado a su proveedor de recursos.

## <a name="next-steps"></a>Pasos siguientes

También puede probar otros [plataforma como servicio (PaaS)](azure-stack-tools-paas-services.md), como el [proveedor de recursos de SQL Server](azure-stack-sql-rp-deploy-short.md) y el [proveedor de recursos de MySQL](azure-stack-mysql-rp-deploy-short.md).

<!--Image references-->
[1]: ./media/azure-stack-webapps-deploy/AppService_exe_Start.png
[2]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep1.png
[3]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2.png
[4]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_populated.png
[5]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_SubscriptionSelection.png
[6]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep3_Certificates.png
[7]: ./media/azure-stack-webapps-deploy/AppService_exe_InstallationProgress.png
[8]: ./media/azure-stack-webapps-deploy/HyperV.png
[9]: ./media/azure-stack-webapps-deploy/MMC.png
[10]: ./media/azure-stack-webapps-deploy/ManagedServers.png


<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
