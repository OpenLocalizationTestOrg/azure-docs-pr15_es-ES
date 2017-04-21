
   * Inicie sesión en la cuenta de Azure, escriba sus credenciales.

     Este método es más rápido y fácil, pero si usa este método no podrá ver la base de datos de SQL Azure o servicios para dispositivos móviles en la ventana del **Explorador de servidores** .

     En el **Explorador de servidores**, haga clic en el botón **Conectar a Azure** . Haga clic en el nodo de **Azure** y, a continuación, haga clic en **conectarse a Azure** en el menú contextual es una alternativa.

   * Instalar un certificado de administración que permite el acceso a su cuenta.

     En el **Explorador de servidores**, haga clic en el nodo de **Azure** y, a continuación, haga clic en **Administrar suscripciones** en el menú contextual. En el cuadro de diálogo **Administrar suscripciones de Azure** , haga clic en la ficha **certificados** y, a continuación, haga clic en **Importar**. Siga las instrucciones para descargar y, a continuación, importar un archivo de suscripción (también conocido como archivo *.publishsettings* ) para su cuenta de Azure.

     > [AZURE.NOTE] Descargar el archivo de suscripción a una carpeta fuera de los directorios de código de origen (por ejemplo, en la carpeta de descargas) y eliminarlo una vez completada la importación. Un usuario malintencionado que obtiene acceso al archivo de suscripción, puede editar, crear y eliminar los servicios de Azure.

    Para obtener más información, vea [conectarse a Azure de Visual Studio](http://go.microsoft.com/fwlink/?LinkId=324796).
