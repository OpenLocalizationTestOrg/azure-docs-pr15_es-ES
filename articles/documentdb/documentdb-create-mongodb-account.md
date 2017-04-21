<properties 
    pageTitle="Crear una cuenta de DocumentDB con compatibilidad con el protocolo de MongoDB | Microsoft Azure" 
    description="Obtenga información sobre cómo crear una cuenta de DocumentDB con compatibilidad con el protocolo de MongoDB, ahora disponible para la vista previa." 
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/20/2016" 
    ms.author="anhoh"/>

# <a name="how-to-create-a-documentdb-account-with-protocol-support-for-mongodb-using-the-azure-portal"></a>Cómo crear una cuenta de DocumentDB con compatibilidad con el protocolo de MongoDB con el portal de Azure

Para crear una cuenta de Azure DocumentDB con compatibilidad con el protocolo de MongoDB, debe:

- Tiene una cuenta de Azure. Puede obtener una [cuenta gratuita de Azure](https://azure.microsoft.com/free/) si aún no tiene una.

## <a name="create-the-account"></a>Crear la cuenta  

Para crear una cuenta de DocumentDB con compatibilidad con el protocolo de MongoDB, realice los pasos siguientes.

1. En una ventana nueva, inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **nuevo**, haga clic en **datos + almacenamiento**, haga clic en **Ver todo**y, a continuación, busque la categoría de **datos + almacenamiento** "DocumentDB protocolo". Haga clic en **DocumentDB - compatibilidad con el protocolo de MongoDB**.

    ![Captura de pantalla del catálogo de soluciones y datos + almacenamiento buscar aspas, resaltado DocumentDB - compatibilidad con el protocolo de MongoDB, base de datos de Mongo](./media/documentdb-create-mongodb-account/marketplacegallery2.png)

3. Como alternativa, en la categoría de **datos + almacenamiento** , bajo **almacenamiento**, haga clic en **más**y, a continuación, haga clic en **cargar más** una o varias veces para mostrar **DocumentDB - compatibilidad con el protocolo de MongoDB**. Haga clic en **DocumentDB - compatibilidad con el protocolo de MongoDB**.

    ![Captura de pantalla de la Marketplace y datos + almacenamiento aspas, resaltado DocumentDB - compatibilidad con el protocolo de MongoDB, base de datos de Mongo](./media/documentdb-create-mongodb-account/marketplacegallery1.png)

4. En el módulo **DocumentDB - compatibilidad con el protocolo de MongoDB (vista previa)** , haga clic en **crear** para iniciar el proceso de suscripción de vista previa.

    ![La DocumentDB - compatibilidad con el protocolo MongoDB módulo en el portal de Azure](./media/documentdb-create-mongodb-account/marketplacegallery3.png)

5. En el módulo **DocumentDB cuenta** , haga clic en **registrarse para obtener una vista previa**. Lea la información y, a continuación, haga clic en **Aceptar**.

    ![El inicio de sesión hasta módulo hoy de vista previa para DocumentDB - compatibilidad con el protocolo de MongoDB en el portal de Azure](./media/documentdb-create-mongodb-account/registerforpreview.png)

6.  Después de aceptar los términos de vista previa, volverá a módulo de crear.  En el módulo **DocumentDB cuenta** , especifique la configuración que desee para la cuenta.

    ![Captura de pantalla de la nueva DocumentDB con compatibilidad con el protocolo de MongoDB placa](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.png)


    - En el cuadro **ID** , escriba un nombre para identificar la cuenta.  Cuando se valida el **identificador** , aparece una marca de verificación verde en el cuadro **ID** . El valor del **identificador** se convierte en el nombre de host en el URI. El **identificador** puede contener sólo letras minúsculas, números y el '-' caracteres y debe estar entre 3 y 50 caracteres. Tenga en cuenta que *documents.azure.com* se anexa al nombre del extremo que elija, el resultado de las cuales se convertirá en el extremo de la cuenta.

    - Para la **suscripción**, seleccione la suscripción de Azure que desea usar para la cuenta. Si su cuenta sólo tenga una suscripción, esa cuenta está activada de forma predeterminada.

    - En el **Grupo de recursos**, seleccione o cree un grupo de recursos para la cuenta.  De forma predeterminada, se selecciona un grupo de recursos existente en la suscripción de Azure.  Sin embargo, puede seleccionar para crear un nuevo grupo de recursos a la que desea agregar la cuenta. Para obtener más información, consulte [con el portal de Azure para administrar los recursos de Azure](resource-group-portal.md).

    - Use la **ubicación** para especificar la ubicación geográfica en la que se va a hospedar la cuenta.
    
    - Opcional: Verificación **Anclar en escritorio**. Si anclados a paneles, siga **paso 8** a continuación para ver el panel de navegación izquierdo de la nueva cuenta.

7.  Una vez que se haya configurado las nuevas opciones de cuenta, haga clic en **crear**.  Puede tardar unos minutos para crear la cuenta.  Si anclado en el panel, puede supervisar el progreso de aprovisionamiento en la Startboard.  
    ![Captura de pantalla de la ventana de crear desde la Startboard - creador de base de datos en línea](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-3.png)  

    Si no está anclado en el panel, puede supervisar su progreso del concentrador de notificaciones.  

    ![Crear bases de datos rápidamente: captura de pantalla de concentrador notificaciones, que muestra que está creando la cuenta de DocumentDB](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-4.png)  

    ![Captura de pantalla del concentrador de notificaciones, que muestra que la cuenta de DocumentDB se ha creado correctamente y se implementa en un grupo de recursos - notificación de creador de base de datos en línea](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-5.png)

8.  Para obtener acceso a la nueva cuenta, haga clic en **DocumentDB (NoSQL)** en el menú de la izquierda. En la lista de DocumentDB normal y DocumentDB con cuentas de soporte técnico de protocolo de Mongo, haga clic en el nombre de su cuenta nueva.

9.  Ahora está listo para usar con la configuración predeterminada. 

    ![Captura de pantalla del módulo de cuenta predeterminada](./media/documentdb-create-mongodb-account/defaultaccountblades.png)
    

## <a name="next-steps"></a>Pasos siguientes


- Obtenga información sobre cómo [conectarse](documentdb-connect-mongodb-account.md) a una cuenta de DocumentDB con protocolo de soporte técnico para MongoDB.

 
