<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Dropbox para empresas | Microsoft Azure" 
    description="Aprenda a usar Dropbox para empresas con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Tutorial: Integración de Azure Active Directory con Dropbox para empresas
  
El objetivo de este tutorial es mostrar la integración de Azure y Dropbox para empresas.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino de prueba en Dropbox para empresas
  
Después de completar este tutorial, los usuarios de Azure AD ha asignado a Dropbox para empresas podrá solo inicio de sesión en la aplicación en la lista desplegable para la empresa empresa sitio (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de las aplicaciones de Dropbox para empresas
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Escenario")



##<a name="enabling-the-application-integration-for-dropbox-for-business"></a>Habilitar la integración de las aplicaciones de Dropbox para empresas
  
El objetivo de esta sección es describen cómo habilitar la integración de las aplicaciones de Dropbox para empresas.

###<a name="to-enable-the-application-integration-for-dropbox-for-business-perform-the-following-steps"></a>Para habilitar la integración de las aplicaciones de Dropbox para empresas, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Dropbox para empresas**.

    ![Galería de aplicación] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Dropbox para empresas**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Dropbox para empresas] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox para empresas")

##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar en Dropbox para empresas con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

Como parte de este procedimiento, es necesario para cargar un certificado codificado de base 64 en su lista desplegable de inquilino de empresa. Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Dropbox para empresas** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Dropbox para empresas** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , realice los pasos siguientes:

    una. Inicio de sesión en la lista desplegable de inquilino de empresa. 

    ![Configurar inicio de sesión único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configurar inicio de sesión único")

    b. En el panel de navegación en el lado izquierdo, haga clic en la **Consola de administración**. 

    ![Configurar inicio de sesión único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configurar inicio de sesión único")

    c. En la **Consola de administración**, haga clic en **autenticación** en el panel de navegación izquierdo. 

    ![Configurar inicio de sesión único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configurar inicio de sesión único")

    d. En la sección **Inicio de sesión único** , seleccione **Habilitar inicio de sesión único**y, a continuación, haga clic en **más** para expandir esta sección.  

    ![Configurar inicio de sesión único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configurar inicio de sesión único")

    e. Copie la dirección URL situada junto a **los usuarios puedan iniciar sesión, escriba su dirección de correo electrónico o pueden ir directamente a**. 

    ![Configurar inicio de sesión único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configurar inicio de sesión único")

    f. En el portal de Azure clásico, en el cuadro de texto de la dirección URL de **Inicio de sesión DropBox para empresas** , pegue la dirección URL. 

    ![Configurar inicio de sesión único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configurar inicio de sesión único")  



4. En la página **configurar inicio de sesión único en Dropbox para empresas** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.  

    ![Configurar inicio de sesión único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configurar inicio de sesión único")


5. En la lista desplegable inquilino de empresa, en la sección **Inicio de sesión único** de la página de **autenticación** , realice los pasos siguientes: 

    ![Configurar inicio de sesión único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configurar inicio de sesión único")

    una. Haga clic en **obligatorio**.

    b. En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Dropbox para empresas** , copie el valor de **dirección URL de la página de inicio de sesión** y, a continuación, péguelo en el cuadro de texto **dirección URL de inicio de sesión** .


    c. Crear un archivo **codificado Base 64** de certificado descargado. 

    > [AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).


    d. Haga clic en el botón **"Elegir certificado"** y, a continuación, busque el **archivo de certificado codificado de base 64**.


    e. Haga clic en el botón **"Guardar cambios"** para completar la configuración de su lista desplegable de inquilino de empresa.


6. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** . 

    ![Configurar inicio de sesión único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configurar inicio de sesión único")



##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
El objetivo de esta sección es describen cómo habilitar el usuario de aprovisionamiento de cuentas de usuario de Active Directory en Dropbox para empresas.


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1. En el Portal de clásico Azure, en la página de integración de aplicación de **Dropbox para empresas** , haga clic en **Configurar el aprovisionamiento de usuario** para abrir el cuadro de diálogo **Configurar el aprovisionamiento de usuario** .

2. En el habilitar usuario aprovisionamiento en DropBox para la página de la empresa, haga clic en habilitar el aprovisionamiento de usuario para abrir el inicio de sesión en Dropbox a establecer un vínculo con cuadro de diálogo de Azure AD.  

    ![Aprovisionamiento de usuario] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "Aprovisionamiento de usuario")

3. En el cuadro de diálogo de **Inicio de sesión en Dropbox vincular con Azure AD** , inicie sesión en su lista desplegable de inquilino de empresa. 

    ![Aprovisionamiento de usuario] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "Aprovisionamiento de usuario")



4. Haga clic en **Permitir** para conceder Azure AD para tener acceso a la lista desplegable. 

    ![Aprovisionamiento de usuario] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "Aprovisionamiento de usuario")



5. Para finalizar la configuración, haga clic en el botón **completado** .  

    ![Aprovisionamiento de usuario] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "Aprovisionamiento de usuario")




##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-dropbox-for-business-perform-the-following-steps"></a>Para asignar a los usuarios a Dropbox para empresas, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Dropbox para empresas **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Sí")
  


Ahora debe esperar 10 minutos y compruebe que la cuenta se ha sincronizado en Dropbox para empresas.

Como primer paso de comprobación, puede comprobar el estado de aprovisionamiento, haciendo clic en **paneles** en la página de integración de aplicación de **Dropbox para empresas** en el Portal de clásico de Azure.

![Asignar usuarios] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Asignar usuarios")


Un usuario completado correctamente el aprovisionamiento de ciclo viene indicado por un estado relacionadas.

![Asignar usuarios] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Asignar usuarios")


Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso.
Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)