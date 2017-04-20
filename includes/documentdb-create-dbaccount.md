1.  Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2.  En la Jumpbar, haga clic en **nuevo**, haga clic en **datos + almacenamiento**y, a continuación, haga clic en **DocumentDB (NoSQL)**.

    ![Captura de pantalla del portal de Azure, resaltado más servicios y DocumentDB (NoSQL)](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)  

3. En el módulo de la **nueva cuenta** , especifique la configuración que desee para la cuenta de DocumentDB.

    ![Captura de pantalla del módulo DocumentDB nueva](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

    - En el cuadro **ID** , escriba un nombre para identificar la cuenta DocumentDB.  Cuando se valida el **identificador** , aparece una marca de verificación verde en el cuadro **ID** . El valor del **identificador** se convierte en el nombre de host en el URI. El **identificador** puede contener sólo letras minúsculas, números y el '-' caracteres y debe estar entre 3 y 50 caracteres. Tenga en cuenta que *documents.azure.com* se anexa al nombre del extremo que elija, el resultado es el extremo de la cuenta DocumentDB.

    - En el cuadro de la **API de NoSQL** , seleccione **DocumentDB**.  

    - Para la **suscripción**, seleccione la suscripción de Azure que desea usar para la cuenta de DocumentDB. Si su cuenta sólo tenga una suscripción, esa cuenta está activada de forma predeterminada.

    - En el **Grupo de recursos**, seleccione o cree un grupo de recursos para su cuenta de DocumentDB.  De forma predeterminada, se crea un nuevo grupo de recursos. Para obtener más información, consulte [con el portal de Azure para administrar los recursos de Azure](../articles/azure-portal/resource-group-portal.md).

    - Use la **ubicación** para especificar la ubicación geográfica en la que se va a hospedar su cuenta de DocumentDB. 

4.  Una vez que se haya configurado las nuevas opciones de cuenta de DocumentDB, haga clic en **crear**. Para comprobar el estado de la implementación, active el hub de notificaciones.  

    ![Crear bases de datos rápidamente: captura de pantalla de concentrador notificaciones, que muestra que está creando la cuenta de DocumentDB](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)  

    ![Captura de pantalla del concentrador de notificaciones, que muestra que la cuenta de DocumentDB se ha creado correctamente y se implementa en un grupo de recursos - notificación de creador de base de datos en línea](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)

5.  Después de crea la cuenta de DocumentDB, está listo para su uso con la configuración predeterminada. La coherencia predeterminada de la cuenta de DocumentDB se establece en **sesión**.  Puede ajustar la coherencia predeterminada haciendo clic en **Coherencia predeterminada** en el menú de recursos. Para obtener más información sobre los niveles de coherencia ofrecidos por DocumentDB, vea [niveles de coherencia en DocumentDB](../articles/documentdb/documentdb-consistency-levels.md).

    ![Captura de pantalla de la hoja de grupo de recursos: comience el desarrollo de aplicaciones](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-6.png)  

    ![Captura de pantalla de la placa de nivel de coherencia: coherencia de sesión](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-7.png)  

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md
