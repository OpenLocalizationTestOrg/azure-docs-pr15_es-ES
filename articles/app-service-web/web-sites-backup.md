<properties 
    pageTitle="Realizar copias de seguridad de la aplicación en Azure" 
    description="Obtenga información sobre cómo crear copias de seguridad de sus aplicaciones de servicio de aplicación de Azure." 
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

# <a name="back-up-your-app-in-azure"></a>Realizar copias de seguridad de la aplicación en Azure


La característica de copia de seguridad y restauración de [Servicio de la aplicación de Azure](../app-service/app-service-value-prop-what-is.md) le permite crear fácilmente las copias de seguridad de la aplicación manual o automáticamente. Puede restaurar la aplicación a un estado anterior o crear una nueva aplicación basada en una de las copias de seguridad de la aplicación original. 

Para obtener información sobre cómo restaurar una aplicación de copia de seguridad, consulte [restaurar una aplicación en Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>
## <a name="what-gets-backed-up"></a>¿Qué Obtiene copia de seguridad 
Servicio de aplicación puede realizar la siguiente información:

* Configuración de la aplicación
* Contenido del archivo
* Las bases de datos de SQL Azure o bases de datos MySQL Azure (ClearDB) conectados a su aplicación (puede elegir las que desea incluir en la copia de seguridad)

Esta información se copia en la cuenta de almacenamiento de Azure y el contenedor que especifique. 

> [AZURE.NOTE] Cada copia de seguridad es una copia sin conexión completa de la aplicación, no una actualización incremental.

<a name="requirements"></a>
## <a name="requirements-and-restrictions"></a>Requisitos y restricciones

* La característica de copia de seguridad y restauración requiere el plan de servicios de aplicación en el nivel **estándar** o superior. Para obtener más información sobre la escala de su plan de servicio de aplicaciones para usar un nivel superior, vea [cambiar la escala de una aplicación en Azure](web-sites-scale.md). Observe que el nivel de **Premium** permite a un mayor número de copias de seguridad diarias de nivel **estándar** .
* Necesita una cuenta de almacenamiento de Azure y contenedor en la misma suscripción como la aplicación que desea hacer copia de seguridad. Para obtener más información sobre las cuentas de almacenamiento de Azure, consulte los [vínculos](#moreaboutstorage) al final de este artículo.
* Copias de seguridad pueden ser de hasta 10GB de contenido de la aplicación y la base de datos. Si el tamaño de la copia de seguridad supera este límite, obtendrá un error. 

<a name="manualbackup"></a>
## <a name="create-a-manual-backup"></a>Crear una copia de seguridad manual

2. En el [Portal de Azure](https://portal.azure.com), desplácese hasta el módulo de la aplicación, seleccione **configuración**y, a continuación, **las copias de seguridad**. Se mostrará el módulo de **copias de seguridad** .
    
    ![Página de las copias de seguridad][ChooseBackupsPage]

    >[AZURE.NOTE]Si ve el mensaje siguiente, haga clic en él para actualizar su plan de servicio de la aplicación antes de continuar con las copias de seguridad.
Para obtener más información, vea [cambiar la escala de una aplicación en Azure](web-sites-scale.md) .  
    >![Elija la cuenta de almacenamiento](./media/web-sites-backup/01UpgradePlan.png)

3. En el módulo de **copias de seguridad** , haga clic en **almacenamiento: no se ha configurado** para configurar una cuenta de almacenamiento.

    ![Elija la cuenta de almacenamiento][ChooseStorageAccount]
    
4. Elija el destino de copia de seguridad, seleccione una **Cuenta de almacenamiento** y el **contenedor**. La cuenta de almacenamiento debe pertenecer a la misma suscripción a la aplicación que desea hacer copia de seguridad. Si lo desea, puede crear una nueva cuenta de almacenamiento o un nuevo contenedor en los módulos respectivos. Cuando haya terminado, haga clic en **Seleccionar**.
    
    ![Elija la cuenta de almacenamiento](./media/web-sites-backup/02ChooseStorageAccount1.png)
    
5. En el módulo de **Configurar opciones de copia de seguridad** que queda abierto, haga clic en **Configuración de la base de datos**, seleccione las bases de datos que desea incluir en las copias de seguridad (base de datos SQL o MySQL) y haga clic en **Aceptar**.  

    ![Elija la cuenta de almacenamiento](./media/web-sites-backup/03ConfigureDatabase.png)

    > [AZURE.NOTE]  Para que una base de datos aparecen en esta lista, su cadena de conexión debe existir en la sección de **cadenas de conexión** del módulo de **configuración de la aplicación** de la aplicación.

6. En el módulo de **Configurar opciones de copia de seguridad** , haga clic en **Guardar**.  

7. En la barra de comandos del módulo de **copias de seguridad** , haga clic en **Copia de seguridad**.
    
    ![Botón BackUpNow][BackUpNow]
    
    Verá un mensaje de progreso durante el proceso de copia de seguridad.

Después de haber configurado una cuenta de almacenamiento y el contenedor de copias de seguridad, puede hacer que un manual de copia de seguridad en cualquier momento.  

<a name="automatedbackups"></a>
## <a name="configure-automated-backups"></a>Configurar copias de seguridad automatizadas

1. En el módulo de **copias de seguridad** , haga clic en **programación: no se ha configurado**. 

    ![Elija la cuenta de almacenamiento](./media/web-sites-backup/05ScheduleBackup.png)
    
1. En el módulo de **Configuración de la programación de copia de seguridad** , establezca **Programar una copia de seguridad** **en**, a continuación, configure la programación de copia de seguridad como desee y haga clic en **Aceptar**.
    
    ![Habilitar las copias de seguridad automatizadas][SetAutomatedBackupOn]
    
4. En el módulo de **Configuración de copia de seguridad** que queda abierto, haga clic en **Configuración de almacenamiento**y elegir el destino de copia de seguridad, seleccione una **Cuenta de almacenamiento** y el **contenedor**. La cuenta de almacenamiento debe pertenecer a la misma suscripción a la aplicación que desea hacer copia de seguridad. Si lo desea, puede crear una nueva cuenta de almacenamiento o un nuevo contenedor en los módulos respectivos. Cuando haya terminado, haga clic en **Seleccionar**.
    
    ![Elija la cuenta de almacenamiento](./media/web-sites-backup/02ChooseStorageAccount1.png)
    
5. En el módulo de **Configurar opciones de copia de seguridad** , haga clic en **Configuración de la base de datos**, seleccione las bases de datos que desea incluir en las copias de seguridad (base de datos SQL o MySQL) y haga clic en **Aceptar**.  

    ![Elija la cuenta de almacenamiento](./media/web-sites-backup/03ConfigureDatabase.png)

    > [AZURE.NOTE]  Para que una base de datos aparecen en esta lista, su cadena de conexión debe existir en la sección de **cadenas de conexión** del módulo de **configuración de la aplicación** de la aplicación.

6. En el módulo de **Configurar opciones de copia de seguridad** , haga clic en **Guardar**.  

<a name="partialbackups"></a>
## <a name="backup-just-part-of-your-app"></a>Copia de seguridad sólo una parte de la aplicación

A veces no desea que la copia de seguridad de todo el contenido de la aplicación. Estos son algunos ejemplos:

-   [Configurar copias de seguridad semanales](web-sites-backup.md#configure-automated-backups) de la aplicación que contiene contenido estático que nunca cambia, como imágenes o antiguo entradas de blog.
-   Su aplicación tiene más de 10GB de contenido (que es la cantidad máxima para hacer una copia en un momento).
-   No desea realizar copias de seguridad de los archivos de registro.

Copias de seguridad parciales le permiten elegir exactamente los archivos que desea hacer copia de seguridad.

### <a name="exclude-files-from-your-backup"></a>Excluir archivos de la copia de seguridad

Para excluir archivos y carpetas de las copias de seguridad, cree un `_backup.filter` archivo en la carpeta D:\home\site\wwwroot de la aplicación y especifique la lista de archivos y carpetas que desee excluir allí. Una forma sencilla para tener acceso a esto es a través de la [Consola de Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console). 

Suponga que tiene una aplicación que contiene los archivos de registro y las imágenes estáticas de años anteriores que nunca va a cambiar. Ya tiene una copia de seguridad completa de la aplicación que incluye las imágenes anteriores. Ahora desea copia de seguridad de la aplicación cada día, pero no desea pagar para almacenar archivos de registro o los archivos de imagen estática que no cambian.

![La carpeta de registros][LogsFolder]
![las imágenes de carpeta][ImagesFolder]
    
Los pasos siguientes muestran cómo podrían excluir estos archivos de la copia de seguridad.

1. Vaya a `http://{yourapp}.scm.azurewebsites.net/DebugConsole` e identificar las carpetas que desea excluir de las copias de seguridad. En este ejemplo, ¿desea excluir los siguientes archivos y carpetas que se muestran en la interfaz de usuario:

        D:\home\site\wwwroot\Logs
        D:\home\LogFiles
        D:\home\site\wwwroot\Images\2013
        D:\home\site\wwwroot\Images\2014
        D:\home\site\wwwroot\Images\brand.png

    [AZURE.NOTE] La última línea muestra que puede excluir archivos de personas, así como las carpetas.

2. Cree un archivo llamado `_backup.filter` y colocar la lista anterior en el archivo, pero eliminar `D:\home`. Lista de un directorio o archivo por línea. Por lo que debe ser el contenido del archivo:

    \site\wwwroot\Logs \LogFiles \site\wwwroot\Images\2013 \site\wwwroot\Images\2014 \site\wwwroot\Images\brand.png

3. Cargar este archivo a la `D:\home\site\wwwroot\` directorio de su sitio con [ftp](web-sites-deploy.md#ftp) o cualquier otro método. Si lo desea, puede crear el archivo directamente en `http://{yourapp}.scm.azurewebsites.net/DebugConsole` e insertar el contenido.

4. Ejecutar copias de seguridad de la misma manera que lo haría normalmente, [manualmente](#create-a-manual-backup) o [automáticamente](#configure-automated-backups).

Ahora, todos los archivos y carpetas que se especifican en `_backup.filter` quedarán excluidos de la copia de seguridad. En este ejemplo, los archivos de registro y los archivos de imagen 2013 y 2014 ya no se copiarán, así como brand.png.

>[AZURE.NOTE] Restaurar parciales copias de seguridad del sitio de la misma manera que lo haría [restaurar una copia de seguridad normal](web-sites-restore.md). El proceso de restauración lo correcto.
>
>Cuando se restaura una copia de seguridad, todo el contenido del sitio se reemplaza con lo que se encuentra en la copia de seguridad. Si es un archivo en el sitio, pero no en la copia de seguridad se elimina. Pero cuando se restaura una copia de seguridad parcial, cualquier contenido que se encuentra en uno de los directorios bloqueados o cualquier archivo en la lista negra, se queda como está.

<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>¿Cómo se almacenan las copias de seguridad

Después de realizar una o varias copias de seguridad de la aplicación, las copias de seguridad estará visibles en la hoja de **contenedores** de su cuenta de almacenamiento, así como la aplicación. En la cuenta de almacenamiento, cada copia de seguridad se compone de un archivo .zip que contiene los datos de copia de seguridad y un archivo .xml que contiene la información del contenido del archivo zip. Puede descomprima y busque estos archivos si desea tener acceso a las copias de seguridad sin realmente la restauración de una aplicación.

La copia de seguridad de base de datos para la aplicación se almacena en la raíz del archivo .zip. Para una base de datos SQL, esto es un archivo de MOCHILA (sin extensión de archivo) y se puede importar. Para crear una nueva base de datos SQL en función de la exportación de MOCHILA, consulte [importar un archivo de MOCHILA para crear una nueva base de datos de usuario](http://technet.microsoft.com/library/hh710052.aspx).

> [AZURE.WARNING] La modificación de cualquiera de los archivos en el contenedor **websitebackups** puede provocar la copia de seguridad se convierten en no válido y, por tanto, no-pueda restaurar.

<a name="nextsteps"></a>
## <a name="next-steps"></a>Pasos siguientes
Para obtener información sobre cómo restaurar una aplicación desde una copia de seguridad, consulte [restaurar una aplicación en Azure](web-sites-restore.md). También puede hacer copia de seguridad y restaurar aplicaciones de servicio de la aplicación con la API de REST (consulte el [Resto de uso para realizar copias de seguridad y restaurar aplicaciones de servicio de aplicaciones](websites-csm-backup.md)).

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.


<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
 
