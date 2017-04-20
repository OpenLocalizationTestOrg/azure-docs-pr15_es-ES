<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con DocuSign | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y DocuSign."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutorial: Integración de Azure Active Directory con DocuSign

El objetivo de este tutorial es mostrar la integración de Azure y DocuSign.
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

- Una suscripción válida de Azure
- Un inquilino en DocuSign



El escenario descrito en este tutorial consta de los siguientes elementos:

1. [Habilitar la integración de aplicación para DocuSign](#enabling-the-application-integration-for-docusign) 


2. [Configurar el inicio de sesión único](#configuring-single-sign-on) 


3. [Configuración de aprovisionamiento de cuentas](#configuring-account-provisioning) 


4. [Asignación de usuarios](#assigning-users) 

    ![Configurar el inicio de sesión único][0]
 

## <a name="enabling-the-application-integration-for-docusign"></a>Habilitar la integración de aplicación para DocuSign

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para DocuSign.

### <a name="to-enable-the-application-integration-for-docusign-perform-the-following-steps"></a>Para habilitar la integración de aplicación para DocuSign, siga estos pasos:

1. En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Configurar el inicio de sesión único][1]

2. En la lista de directorio, seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Configurar el inicio de sesión único][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro ¿Qué desea hacer el cuadro de diálogo, haga clic en **Agregar una aplicación de la Galería**.

    ![Configurar el inicio de sesión único][4]


6. En el cuadro Buscar, escriba **DocuSign**.

    ![Configurar el inicio de sesión único][5]

7. En el panel de resultados, seleccione **DocuSign**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Configurar el inicio de sesión único][6]


## <a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a DocuSign con su cuenta de Azure AD mediante federación basada en el protocolo SAML.


### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1. En el portal de clásico Azure, en la página **DocuSign integración de aplicaciones** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo Configurar inicio de sesión único.

    ![Configurar el inicio de sesión único][7]

2. En la página **¿cómo desea que los usuarios para iniciar sesión DocuSign** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en siguiente.

    ![Configurar el inicio de sesión único][8]

3. En la página **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único][61]

    una. En el cuadro de texto **dirección URL de inicio de sesión** , escriba `https://account.docusign.com/*`.  

    b. En el cuadro de texto **identificador** , escriba `https://account.docusign.com/*`.  
   
    c. Haga clic en **siguiente**. 


    > [AZURE.TIP] El inicio de sesión en dirección URL y los valores de identificador son solo marcadores de posición. Más adelante en este tema, se tratan las instrucciones recuperar los valores reales en su entorno.
 

4. En la página **configurar inicio de sesión único en DocuSign** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único][10]


5. En una ventana de explorador web diferente, inicie sesión en el **portal de administración de DocuSign** como administrador.


6. En el menú de navegación de la izquierda, haga clic en **dominios**.

    ![Configurar el inicio de sesión único][51]

7. En el panel derecho, haga clic en **Notificación de dominio**.

    ![Configurar el inicio de sesión único][52]

8. En el cuadro de diálogo de **notificación de un dominio** , en el cuadro de texto **Nombre de dominio** , escriba el dominio de la compañía y, a continuación, haga clic en **notificación**. Asegúrese de que compruebe el dominio y el estado es activo.

    ![Configurar el inicio de sesión único][53]

9. En el menú de la izquierda, haga clic en **Proveedores de identidades**  

    ![Configurar el inicio de sesión único][54]

10. En el panel derecho, haga clic en **Agregar proveedor de identidades**. 
    
    ![Configurar el inicio de sesión único][55]

11. En la página **Configuración del proveedor de identidades** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único][56]


    una. En el cuadro de texto **nombre** , escriba un nombre único para la configuración. No use espacios.

    b. En el portal de clásico Azure, copie la dirección URL del emisor y péguelo en el cuadro de texto del **Emisor del proveedor de identidades** .

    c. En el portal de clásico Azure, copie la **Dirección URL de inicio de sesión remota**y péguelo en el cuadro de texto **Dirección URL de inicio de sesión del proveedor de identidades** .

    d. En el portal de clásico Azure, copie la **Dirección URL de cierre de sesión remota**y péguelo en el cuadro de texto **Dirección URL de cierre de sesión del proveedor de identidades** .

    e. Seleccione **La solicitud de niveles de autenticación de inicio de sesión**.

    f. Como los **niveles de autenticación enviar solicitud por**, seleccione **Publicar**.

    g. Como **Enviar solicitud de cierre de sesión**, seleccione **Publicar**. 


12. En la sección **Asignación de atributos personalizados** , elija el campo que desee asociar con Azure AD reclamación. En este ejemplo, se asigna la notificación de la **dirección de correo electrónico** con el valor de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Este es el nombre de la reclamación de predeterminado de Azure AD para notificación de correo electrónico. 

    > [AZURE.NOTE] Use el adecuado **identificador de usuario** para asignar al usuario de Azure AD Docusign asignación de usuario. Seleccione el campo nombre propio y escriba el valor apropiado basado en la configuración de la organización.

    ![Configurar el inicio de sesión único][57]

13. En la sección **Certificado del proveedor de identidades** , haga clic en **Agregar el certificado**y, a continuación, cargar el certificado que ha descargado del portal de Azure AD clásico.   

    ![Configurar el inicio de sesión único][58]

14. Haga clic en **Guardar**.

15. En la sección **Proveedores de identidades** , haga clic en **acciones**y, a continuación, haga clic en **extremos**.   

    ![Configurar el inicio de sesión único][59]



10. En el portal de clásico Azure, vuelva a la página de **Configuración de aplicación** . 

16. En **el portal de administración de DocuSign**, en la sección **Ver SAML 2.0 extremos** realizar, los siguientes pasos:

    ![Configurar el inicio de sesión único][60]

    una. Copie la **Dirección URL del emisor de proveedor de servicio**y péguelo en el cuadro de texto **identificador** en el portal de clásico de Azure.

    b. Copie la **Dirección URL de inicio de sesión del proveedor de servicio**y péguelo en el cuadro de texto de **Inicio de sesión en la dirección URL** en el portal de clásico de Azure.

    c.  Haga clic en **Cerrar**  


10. En el portal de Azure clásico, haga clic en **siguiente**. 


15. En el portal de clásico Azure, seleccione la **confirmación de la configuración de inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Aplicaciones][14]

10. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.

    ![Aplicaciones][15]
 

## <a name="configuring-account-provisioning"></a>Configuración de aprovisionamiento de cuentas

El objetivo de esta sección es describen cómo habilitar el usuario de aprovisionamiento de cuentas de usuario de Active Directory para DocuSign.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1. En el **portal de clásica Azure**, en la página **DocuSign integración de aplicaciones** , haga clic en **Configurar el aprovisionamiento de cuenta** para abrir el cuadro de diálogo Configurar el aprovisionamiento de usuario.

    ![Configuración de aprovisionamiento de cuentas][30]

2. En la página de **configuración y las credenciales de administrador** para habilitar automática de aprovisionamiento de usuarios, proporcionar las credenciales de una cuenta de DocuSign con los derechos necesarios y, a continuación, haga clic en **siguiente**. 

    ![Configuración de aprovisionamiento de cuentas][31]

3. En el cuadro de diálogo **conexión de prueba** , haga clic en **Iniciar prueba**y, tras una prueba correcta, haga clic en **siguiente**.

    ![Configuración de aprovisionamiento de cuentas][32]

3. En la página de **confirmación** , haga clic en **completado**.

    ![Configuración de aprovisionamiento de cuentas][33]
 

## <a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

### <a name="to-assign-users-to-docusign-perform-the-following-steps"></a>Para asignar a los usuarios a DocuSign, siga estos pasos:

1. En el **portal de clásica Azure**, cree una cuenta de prueba.

2. En la página **DocuSign integración de aplicaciones** , haga clic en **asignar a los usuarios**.

    ![Asignación de usuarios][40]
 

3. Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Asignación de usuarios][41]


Ahora debe esperar 10 minutos y compruebe que la cuenta se ha sincronizado con DocuSign.

Como primer paso de comprobación, puede comprobar el estado de aprovisionamiento, haciendo clic en el panel en el D en la página de integración de aplicación de DocuSign en el portal de clásico de Azure.

![Asignación de usuarios][42]

Un usuario completado correctamente el aprovisionamiento de ciclo viene indicado por un estado relacionadas:

![Asignación de usuarios][43]


Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso.

Para obtener más detalles sobre el Panel de acceso, vea Introducción al Panel de acceso.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png