<properties 
    pageTitle="Restaurar una aplicación en Azure" 
    description="Obtenga información sobre cómo restaurar la aplicación desde una copia de seguridad." 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/06/2016" 
    ms.author="cephalin"/>

# <a name="restore-an-app-in-azure"></a>Restaurar una aplicación en Azure

Este artículo le muestra cómo restaurar una aplicación de [Servicio de la aplicación de Azure](../app-service/app-service-value-prop-what-is.md) que ha copiado previamente (consulte [copia de seguridad de la aplicación en Azure](web-sites-backup.md)). Puede restaurar la aplicación con su bases de datos vinculadas (base de datos SQL o MySQL) a petición a un estado anterior o crear una nueva aplicación basada en una copia de seguridad de la aplicación original. Crear una nueva aplicación que se ejecuta en paralelo con la versión más reciente puede ser útil para A y B pruebas.

Restaurar a partir de las copias de seguridad está disponible para las aplicaciones que se ejecuta en nivel **estándar** y **Premium** . Para obtener información acerca del escalado de la aplicación, vea [cambiar la escala de una aplicación en Azure](web-sites-scale.md). Nivel de **Premium** permite a un mayor número de copias de seguridad diarias llevarse a cabo a nivel **estándar** .

<a name="PreviousBackup"></a>
## <a name="restore-an-app-from-an-existing-backup"></a>Restaurar una aplicación desde una copia de seguridad existente

1. En el módulo de **configuración** de la aplicación en el Portal de Azure, haga clic en **copias de seguridad** para mostrar el módulo de **copias de seguridad** . A continuación, haga clic en **Restaurar ahora** en la barra de comandos. 
    
    ![Seleccione Restaurar ahora][ChooseRestoreNow]

3. En el módulo **Restaurar** , seleccione primero el origen de la copia de seguridad. 

    ![](./media/web-sites-restore/021ChooseSource.png)
    
    La opción de **copia de seguridad de aplicación** muestra todas las copias de seguridad de la aplicación actual y fácilmente puede seleccionar uno. 
    La opción de **almacenamiento** le permite seleccionar cualquier archivo ZIP desde cualquier cuenta de almacenamiento de Azure y contenedor en su suscripción existente. 
    Si está intentando restaurar una copia de seguridad de otra aplicación, use la opción de **almacenamiento** .

4. A continuación, especifique el destino de la restauración de la aplicación de **destino de restaurar**.

    ![](./media/web-sites-restore/022ChooseDestination.png)
    
    >[AZURE.WARNING] Si elige **Sobrescribir**, se borrarán todos los datos existentes en la aplicación actual. Antes de hacer clic en **Aceptar**, asegúrese de que es exactamente lo desee hacer.
    
    Puede seleccionar **Una aplicación existente** para restaurar la copia de seguridad de la aplicación a otra aplicación en el mismo grupo de recursos. Antes de usar esta opción, debe ya ha creado otra aplicación en el grupo de recursos con la que coinciden con la configuración de la base de datos a la que se definen en la copia de seguridad de la aplicación. 
    
5. Haga clic en **Aceptar**.

<a name="StorageAccount"></a>
## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Descargar o eliminar una copia de seguridad de una cuenta de almacenamiento
    
1. En la página principal **Examinar** módulo del Portal de Azure, seleccione **cuentas de almacenamiento**.
    
    Se mostrará una lista de sus cuentas de almacenamiento existente. 
    
2. Seleccione la cuenta de almacenamiento que contiene la copia de seguridad que desea descargar o eliminar.
    
    Se mostrará el módulo de la cuenta de almacenamiento.

3. En el módulo accountn de almacenamiento, seleccione el contenedor que desee
    
    ![Ver contenedores][ViewContainers]

4. Seleccione el archivo de copia de seguridad que desea descargar o eliminar.

    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)

5. Haga clic en **Descargar** o **Eliminar** según lo desee hacer.  

<a name="OperationLogs"></a>
## <a name="monitor-a-restore-operation"></a>Monitor de una operación de restauración
    
1. Para ver los detalles sobre el éxito o error de la operación de restauración de la aplicación, vaya a la hoja de **Registro de auditoría** en el portal de Azure. 
    
    El módulo de **registros de auditoría** muestra todas las operaciones, junto con el nivel, estado, recursos y detalles de la hora.
    
2. Desplácese hacia abajo para encontrar el que quiera restaurar el funcionamiento y haga clic en él para seleccionarlo.

El módulo de detalles mostrará la información disponible relacionada con la operación de restauración.
    
## <a name="next-steps"></a>Pasos siguientes

También puede hacer copia de seguridad y restaurar aplicaciones de servicio de la aplicación con la API de REST (consulte el [Resto de uso para realizar copias de seguridad y restaurar aplicaciones de servicio de aplicaciones](websites-csm-backup.md)).

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png
 
