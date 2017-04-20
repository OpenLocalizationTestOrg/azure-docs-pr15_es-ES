<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Learningpool | Microsoft Azure" 
    description="Aprenda a usar Learningpool con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-learningpool"></a>Tutorial: Integración de Azure Active Directory con Learningpool
  
El objetivo de este tutorial es mostrar la integración de Azure y Learningpool.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Learningpool activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Learningpool será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Learningpool (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Learningpool
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-learningpool-tutorial/IC791166.png "Escenario")
##<a name="enabling-the-application-integration-for-learningpool"></a>Habilitar la integración de aplicación para Learningpool
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Learningpool.

###<a name="to-enable-the-application-integration-for-learningpool-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Learningpool, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-learningpool-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-learningpool-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-learningpool-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Learningpool**.

    ![Galería de aplicación] (./media/active-directory-saas-learningpool-tutorial/IC795073.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Learningpool**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Learningpool] (./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Learningpool con su cuenta de Azure AD mediante federación basada en el protocolo SAML.
  
La aplicación de Learningpool espera las afirmaciones SAML en un formato específico, lo que requiere que agregue asignaciones de atributos personalizados a la configuración de **atributos de token saml** .  
La siguiente captura de pantalla muestra un ejemplo de este.

![Atributos de Token SAML] (./media/active-directory-saas-learningpool-tutorial/IC795074.png "Atributos de Token SAML")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Learningpool** , en el menú de la parte superior, haga clic en **atributos** para abrir el cuadro de diálogo **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-learningpool-tutorial/IC795075.png "Atributos")

2.  Para agregar las asignaciones de atributo obligatorio, realice los pasos siguientes:

    ###  

  	|Nombre del atributo                |Valor del atributo            |
  	|------------------------------|---------------------------|

     urn: oid:1.2.840.113556.1.4.221 | User.userPrincipalName
  	|-------------------------------|--------------------------|  
     urn: oid:2.5.4.42|User.givenName   
  	|urn: oid:0.9.2342.19200300.100.1.3|User.Mail
  	|urn: oid:2.5.4.4|User.Surname

    1.  Para cada fila de datos de la tabla anterior, haga clic en **Agregar usuario atributo**.
    2.  En el cuadro de texto **Nombre de atributo** , escriba el nombre del atributo que se muestra de la fila.
    3.  En la lista **Valor del atributo** , seleccione el valor del atributo que se muestra de la fila.
    4.  Haga clic en **Finalizar**.

3.  Haga clic en **Aplicar cambios**.

4.  En el explorador, haga clic en **Atrás** para volver a abrir el cuadro de diálogo de **Inicio rápido** .

5.  Haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar Singel inicio de sesión] (./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configurar Singel inicio de sesión")

6.  En la página **¿cómo desea que los usuarios para iniciar sesión Learningpool** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configurar el inicio de sesión único")

7.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Learningpool inicio de sesión en la URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión la aplicación Learningpool (por ejemplo: https://parliament.preview.learningpool.com/auth/shibboleth/index.php) y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configurar la dirección URL de la aplicación")

8.  En la página **configurar inicio de sesión único en Learningpool** , para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configurar el inicio de sesión único")

9.  Reenviar ese archivo de metadatos a su equipo de soporte técnico de Learningpool.

    >[AZURE.NOTE]Inicio de sesión único debe estar habilitado en el equipo de soporte técnico Learningpool.

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Learningpool, debe aprovisionar en Learningpool.
  
No hay ningún elemento de acción para configurar Learningpool de aprovisionamiento de usuarios.  
Los usuarios tendrán que se cree el equipo de soporte técnico de su Learningpool.

>[AZURE.NOTE]Puede usar cualquier otros Learningpool usuario cuenta herramientas de creación o API proporcionan por Learningpool a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-learningpool-perform-the-following-steps"></a>Para asignar a los usuarios a Learningpool, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Learningpool **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-learningpool-tutorial/IC795081.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-learningpool-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).