<properties 
    pageTitle="Implementar DocumentDB y las aplicaciones del servicio Web de Azure aplicación mediante una plantilla de administrador de recursos de Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo implementar una cuenta de DocumentDB, aplicaciones de Azure aplicación de servicio Web y una aplicación web de ejemplo con una plantilla de administrador de recursos de Azure." 
    services="documentdb, app-service\web" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/02/2016" 
    ms.author="hawong"/>

# <a name="deploy-documentdb-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Implementar DocumentDB y las aplicaciones del servicio Web de Azure aplicación mediante una plantilla de administrador de recursos de Azure

En este tutorial se muestra cómo usar una plantilla de administrador de recursos de Azure para implementar e integrar [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/), una aplicación web de [Servicio de la aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) y una aplicación web de ejemplo.

Uso de plantillas de administrador de recursos de Azure, puede automatizar fácilmente la implementación y configuración de los recursos de Azure.  Este tutorial muestra cómo implementar una aplicación web y configure automáticamente la información de conexión de cuenta de DocumentDB.

Después de completar este tutorial, podrá responder a las preguntas siguientes:  

-   ¿Cómo puedo usar una plantilla de administrador de recursos de Azure para implementar e integrar una cuenta de DocumentDB y una aplicación web en la aplicación de servicio de Azure?
-   ¿Cómo puedo usar una plantilla de administrador de recursos de Azure para implementar e integrar una cuenta de DocumentDB, una aplicación web en la aplicación de servicio Web Apps y una aplicación Webdeploy?

<a id="Prerequisites"></a>
## <a name="prerequisites"></a>Requisitos previos
> [AZURE.TIP] Mientras este tutorial no supone la experiencia anterior con plantillas de administrador de recursos de Azure o JSON, si desea modificar las opciones de implementación, o las plantillas que se hace referencia, conocimiento de cada una de estas áreas será necesario.

Antes de seguir las instrucciones de este tutorial, asegúrese de que tiene los siguientes:

- Una suscripción de Azure. Azure es una plataforma de suscripción.  Para obtener más información acerca de cómo obtener una suscripción, vea [Opciones de compra](https://azure.microsoft.com/pricing/purchase-options/), [Miembro ofrece](https://azure.microsoft.com/pricing/member-offers/)o [Versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).

##<a id="CreateDB"></a>Paso 1: Descargar los archivos de plantilla ##
Vamos a empezar a descargar los archivos de plantilla que se utilizará en este tutorial.

1. Descargar la plantilla de [crear una cuenta de DocumentDB, aplicaciones Web e implementar un ejemplo de aplicación de demostración](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) a una carpeta local (por ejemplo, C:\DocumentDBTemplates). Esta plantilla implementará una cuenta de DocumentDB, una aplicación de servicio de aplicación web y una aplicación web.  La aplicación web para conectar con la cuenta DocumentDB configurará automáticamente.

2. Descargar la plantilla de [ejemplo de aplicaciones Web y crear una cuenta de DocumentDB](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) a una carpeta local (por ejemplo, C:\DocumentDBTemplates). Esta plantilla implementará una cuenta de DocumentDB, una aplicación de servicio de aplicación web y modificar configuración del sitio de la aplicación para exponer fácilmente DocumentDB información de conexión, pero no incluye una aplicación web.  

<a id="Build"></a>
##<a name="step-2-deploy-the-documentdb-account-app-service-web-app-and-demo-application-sample"></a>Paso 2: Implementar la cuenta DocumentDB, ejemplo de aplicación de web app y demostración de servicio de aplicaciones

Ahora vamos a implementar nuestra primera plantilla.

> [AZURE.TIP] La plantilla no valida que el nombre de la aplicación web y el nombre de cuenta de DocumentDB especificados son un) válida y b) está disponible.  Se recomienda que compruebe la disponibilidad de los nombres que se planea suministrar antes de enviar la implementación.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com), haga clic en nuevo y busque "Implementación de la plantilla".
    ![Captura de pantalla de la interfaz de usuario de implementación de plantilla](./media/documentdb-create-documentdb-website/TemplateDeployment1.png)

2. Seleccione el elemento de implementación de la plantilla y haga clic en **crear**
    ![captura de pantalla de la interfaz de usuario de implementación de plantilla](./media/documentdb-create-documentdb-website/TemplateDeployment2.png)

3.  Haga clic en **Editar plantilla**, pegue el contenido del archivo de plantilla DocDBWebsiteTodo.json y haga clic en **Guardar**.
    ![Captura de pantalla de la interfaz de usuario de implementación de plantilla](./media/documentdb-create-documentdb-website/TemplateDeployment3.png)

4. Haga clic en **Editar parámetros**, proporcionar los valores para cada uno de los parámetros obligatorios y haga clic en **Aceptar**.  Los parámetros son como sigue:

    1. Nombre del sitio: Especifica el nombre de la aplicación de servicio de aplicaciones web y se usa para crear la dirección URL que usará para tener acceso a la aplicación web (por ejemplo, si se especifica "mydemodocdbwebapp", la dirección URL en la que se tiene acceso a la aplicación web estará mydemodocdbwebapp.azurewebsites.net).

    2. HOSTINGPLANNAME: Especifica el nombre del plan de hospedaje de servicio de aplicación para crear.

    3. UBICACIÓN: Especifica la ubicación de Azure en la que desea crear la web y DocumentDB recursos de la aplicación.

    4. DATABASEACCOUNTNAME: Especifica el nombre de la cuenta de DocumentDB para crear.   

    ![Captura de pantalla de la interfaz de usuario de implementación de plantilla](./media/documentdb-create-documentdb-website/TemplateDeployment4.png)

5. Elija un grupo de recursos existente o proporcionar un nombre para crear un nuevo grupo de recursos y elija una ubicación para el grupo de recursos.
    ![Captura de pantalla de la interfaz de usuario de implementación de plantilla](./media/documentdb-create-documentdb-website/TemplateDeployment5.png)
  
6.  Haga clic en **condiciones legales de revisión**, **compra**y, a continuación, haga clic en **crear** para empezar la implementación.  Seleccione **Anclar en escritorio** para que la implementación resultante es fácilmente visible en la página principal del portal Azure.
    ![Captura de pantalla de la interfaz de usuario de implementación de plantilla](./media/documentdb-create-documentdb-website/TemplateDeployment6.png)

7.  Cuando finalice la implementación, se abrirá el módulo de grupo de recursos.
    ![Captura de pantalla del módulo de grupo de recursos](./media/documentdb-create-documentdb-website/TemplateDeployment7.png)  

8.  Para usar la aplicación, simplemente vaya a la dirección URL de web app (en el ejemplo anterior, la dirección URL sería http://mydemodocdbwebapp.azurewebsites.net).  Verá la siguiente aplicación web:

    ![Aplicación de ejemplo Todo](./media/documentdb-create-documentdb-website/image2.png)

9. Continuar y crear un par de tareas en la aplicación web y, a continuación, volver a la hoja de grupo de recursos en el portal de Azure. Haga clic en el recurso de la cuenta de DocumentDB en la lista de recursos y, a continuación, haga clic en **Explorador de consulta**.
    ![Captura de pantalla de resumen de lente con la aplicación web de myotherdocumentdbwebapp resaltada](./media/documentdb-create-documentdb-website/TemplateDeployment8.png)  

10. Ejecute la consulta predeterminada, "SELECT *FROM c" y comprobar los resultados.  Observe que la consulta recuperado la representación JSON de los elementos de todo que creó en el paso 7 anterior.  No dude en experimentar con las consultas; Por ejemplo, intente ejecutar seleccione* de c.isComplete de dónde c = true para devolver todos los elementos de todo que se han marcado como completados.

    ![Captura de pantalla de los módulos de explorador de consulta y los resultados que muestra los resultados de la consulta](./media/documentdb-create-documentdb-website/image5.png)

11. No dude en explorar la experiencia de portal de DocumentDB o modificar la aplicación de ejemplo Todo.  Cuando esté listo, vamos a implementar otra plantilla.
    
<a id="Build"></a> 
## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Paso 3: Implementar el ejemplo de aplicación de cuenta y web de documento

Ahora vamos a implementar nuestra segunda plantilla.  Esta plantilla es muy útil para mostrar cómo insertar información de conexión de DocumentDB como punto final de la cuenta y la clave principal en una aplicación web como la configuración de la aplicación o como una cadena de conexión personalizada. Por ejemplo, quizás tiene su propia aplicación web que le gustaría implementar con una cuenta de DocumentDB y la información de conexión que se rellena automáticamente durante la implementación.

> [AZURE.TIP] La plantilla no valida que el nombre de la aplicación web y el nombre de cuenta de DocumentDB especificados son un) válida y b) está disponible.  Se recomienda que compruebe la disponibilidad de los nombres que se planea suministrar antes de enviar la implementación.

1. En el [Portal de Azure](https://portal.azure.com), haga clic en nuevo y busque "Implementación de la plantilla".
    ![Captura de pantalla de la interfaz de usuario de implementación de plantilla](./media/documentdb-create-documentdb-website/TemplateDeployment1.png)

2. Seleccione el elemento de implementación de la plantilla y haga clic en **crear**
    ![captura de pantalla de la interfaz de usuario de implementación de plantilla](./media/documentdb-create-documentdb-website/TemplateDeployment2.png)

3.  Haga clic en **Editar plantilla**, pegue el contenido del archivo de plantilla DocDBWebSite.json y haga clic en **Guardar**.
    ![Captura de pantalla de la interfaz de usuario de implementación de plantilla](./media/documentdb-create-documentdb-website/TemplateDeployment3.png)

4. Haga clic en **Editar parámetros**, proporcionar los valores para cada uno de los parámetros obligatorios y haga clic en **Aceptar**.  Los parámetros son como sigue:

    1. Nombre del sitio: Especifica el nombre de la aplicación de servicio de aplicaciones web y se usa para crear la dirección URL que usará para tener acceso a la aplicación web (por ejemplo, si se especifica "mydemodocdbwebapp", la dirección URL en la que se tiene acceso a la aplicación web estará mydemodocdbwebapp.azurewebsites.net).

    2. HOSTINGPLANNAME: Especifica el nombre del plan de hospedaje de servicio de aplicación para crear.

    3. UBICACIÓN: Especifica la ubicación de Azure en la que desea crear la web y DocumentDB recursos de la aplicación.

    4. DATABASEACCOUNTNAME: Especifica el nombre de la cuenta de DocumentDB para crear.   

    ![Captura de pantalla de la interfaz de usuario de implementación de plantilla](./media/documentdb-create-documentdb-website/TemplateDeployment4.png)

5. Elija un grupo de recursos existente o proporcionar un nombre para crear un nuevo grupo de recursos y elija una ubicación para el grupo de recursos.
    ![Captura de pantalla de la interfaz de usuario de implementación de plantilla](./media/documentdb-create-documentdb-website/TemplateDeployment5.png)
  
6.  Haga clic en **condiciones legales de revisión**, **compra**y, a continuación, haga clic en **crear** para empezar la implementación.  Seleccione **Anclar en escritorio** para que la implementación resultante es fácilmente visible en la página principal del portal Azure.
    ![Captura de pantalla de la interfaz de usuario de implementación de plantilla](./media/documentdb-create-documentdb-website/TemplateDeployment6.png)

7.  Cuando finalice la implementación, se abrirá el módulo de grupo de recursos.
    ![Captura de pantalla del módulo de grupo de recursos](./media/documentdb-create-documentdb-website/TemplateDeployment7.png)  

8. Haga clic en el recurso de la aplicación Web en la lista de recursos y, a continuación, haga clic en **configuración de la aplicación**
    ![captura de pantalla del grupo de recursos](./media/documentdb-create-documentdb-website/TemplateDeployment9.png)  

9. Observe cómo hay presentes en el extremo de DocumentDB y cada una de las claves principales de DocumentDB configuración de la aplicación.
    ![Captura de pantalla de configuración de la aplicación](./media/documentdb-create-documentdb-website/TemplateDeployment10.png)  

10. No dude en continuar explorar el Portal de Azure o siga uno de nuestros DocumentDB [ejemplos](http://go.microsoft.com/fwlink/?LinkID=402386) para crear su propia aplicación DocumentDB.

    
    
<a name="NextSteps"></a>
## <a name="next-steps"></a>Pasos siguientes

¡Felicidades! Ha implementado DocumentDB, la aplicación de servicio de aplicación web y una aplicación web de ejemplo con plantillas de administrador de recursos de Azure.

- Para obtener más información sobre DocumentDB, haga clic en [aquí](http://azure.com/docdb).
- Para obtener más información sobre las aplicaciones de servicio Web de Azure aplicación, haga clic en [aquí](http://go.microsoft.com/fwlink/?LinkId=325362).
- Para obtener más información acerca de las plantillas de administrador de recursos de Azure, haga clic en [aquí](https://msdn.microsoft.com/library/azure/dn790549.aspx).


## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Encontrará una guía para el cambio del portal antiguo en el nuevo portal: [referencia para navegar por el Portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.
 
