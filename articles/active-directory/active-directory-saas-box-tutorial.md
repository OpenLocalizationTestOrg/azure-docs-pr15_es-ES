<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con cuadro | Microsoft Azure" 
    description="¡Obtenga información sobre cómo usar el cuadro con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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




#<a name="tutorial-azure-active-directory-integration-with-box"></a>Tutorial: Integración de Azure Active Directory con cuadro


  
El objetivo de este tutorial es mostrar la integración de Azure y cuadro.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino de prueba en el cuadro
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado al cuadro podrán al inicio de sesión único en la aplicación en el sitio de su empresa de cuadro (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de las aplicaciones de cuadro
2.  Configurar el inicio de sesión único
3.  Configuración de usuario y el aprovisionamiento de grupo
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-box-tutorial/IC769537.png "Escenario")



##<a name="enabling-the-application-integration-for-box"></a>Habilitar la integración de las aplicaciones de cuadro
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicaciones para el cuadro.

###<a name="to-enable-the-application-integration-for-box-perform-the-following-steps"></a>Para habilitar la integración de aplicación cuadro, realice los pasos siguientes:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-box-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-box-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-box-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  **En el **cuadro de búsqueda**, escribe.**

    ![Galería de aplicación] (./media/active-directory-saas-box-tutorial/IC701023.png "Galería de aplicación")

7.  En el panel de resultados, seleccione el **cuadro**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Cuadro] (./media/active-directory-saas-box-tutorial/IC701024.png "Cuadro")



##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar al cuadro con su cuenta de Azure AD mediante federación basada en el protocolo SAML. Como parte de este procedimiento, debe cargar metadatos en Box.com.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **cuadro** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-box-tutorial/IC769538.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión el cuadro** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-box-tutorial/IC769539.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de **Dirección URL del inquilino de cuadro de** texto, escriba la dirección URL de inquilinos de cuadro (por ejemplo: https://<mydomainname>. box.com) y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-box-tutorial/IC669826.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en el cuadro** , para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, en el archivo de datos local en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-box-tutorial/IC669824.png "Configurar inicio de sesión único")

5.  Reenviar ese archivo de metadatos al cuadro de soporte técnico. El equipo de soporte técnico necesita configura un inicio de sesión único para usted.

6.  Seleccione la configuración de inicio de sesión único de confirmación y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-box-tutorial/IC769540.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
El objetivo de esta sección es describen cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory al cuadro.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1. En el portal de clásico Azure, en la página de integración de aplicación de **cuadro** , haga clic en **Configurar el aprovisionamiento de usuario** para abrir el cuadro de diálogo **Configurar el aprovisionamiento de usuario** . 

    ![Habilitar el aprovisionamiento de usuario automática] (./media/active-directory-saas-box-tutorial/IC769541.png "Habilitar el aprovisionamiento de usuario automática")

2. En la página de diálogo **Habilitar al cuadro de aprovisionamiento de usuarios** , haga clic en **Habilitar el aprovisionamiento de usuario**. 

    ![Habilitar el aprovisionamiento de usuario automática] (./media/active-directory-saas-box-tutorial/IC769544.png "Habilitar el aprovisionamiento de usuario automática")

3. En la página **iniciar sesión en conceder acceso al cuadro** , indique las credenciales necesarias y, a continuación, haga clic en **autorizar**. 

    ![Habilitar el aprovisionamiento de usuario automática] (./media/active-directory-saas-box-tutorial/IC769546.png "Habilitar el aprovisionamiento de usuario automática")


4. Haga clic en **conceder acceso al cuadro** para autorizar esta operación y volver al portal clásico Azure. 

    ![Habilitar el aprovisionamiento de usuario automática] (./media/active-directory-saas-box-tutorial/IC769549.png "Habilitar el aprovisionamiento de usuario automática")


5. En la página **Opciones de aprovisionamiento** , las casillas de verificación de **los tipos de objeto aprovisionar** le permiten seleccionar si o no agrupar objetos aprovisionados al cuadro además de los objetos de usuario.  Para obtener más información, consulte "Asignar usuarios y grupos de sección" a continuación.


6. Para finalizar la configuración, haga clic en el botón Finalizar. 

    ![Habilitar el aprovisionamiento de usuario automática] (./media/active-directory-saas-box-tutorial/IC769551.png "Habilitar el aprovisionamiento de usuario automática")



##<a name="assigning-a-test-user"></a>Asignar a un usuario de prueba
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-box-perform-the-following-steps"></a>Para asignar a los usuarios al cuadro, realice los pasos siguientes:

1. En el portal de Azure clásico, cree una cuenta de prueba.

2. En la página de integración de aplicación de **cuadro **, haga clic en **asignar a los usuarios**. 

    ![Asignar usuarios] (./media/active-directory-saas-box-tutorial/IC769552.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación. 

    ![Sí] (./media/active-directory-saas-box-tutorial/IC767830.png "Sí")
  
Ahora debe esperar 10 minutos y compruebe que la cuenta se ha sincronizado al cuadro.

Como primer paso de comprobación, puede comprobar el estado de aprovisionamiento, haciendo clic en el panel en el D en la página de integración de aplicación de cuadro en el portal de clásico de Azure.

![Panel] (./media/active-directory-saas-box-tutorial/IC769553.png "Panel")

Un usuario completado correctamente el aprovisionamiento de ciclo viene indicado por un estado relacionadas:

![Estado de integración] (./media/active-directory-saas-box-tutorial/IC769555.png "Estado de integración")


En el inquilino de cuadro usuarios sincronizados se muestran en **Administrar usuarios** en la **Consola de administración**.

![Estado de integración] (./media/active-directory-saas-box-tutorial/IC769556.png "Estado de integración")


##<a name="assigning-users-and-groups"></a>Asignar los usuarios y grupos

La **cuadro > usuarios y grupos** ficha en el portal de clásico Azure le permite especificar qué usuarios y grupos se deben conceder acceso al cuadro. Asignación de un usuario o grupo hace que se produzca lo siguiente:

* Azure AD permite que el usuario asignado (ya sea por asignación directa o pertenencia a grupos) para autenticar al cuadro. Si un usuario no está asignado, no permite iniciar sesión en el cuadro de Azure AD y devolverá un error en la página de inicio de sesión de Azure AD.

* Un mosaico de la aplicación de cuadro se agrega al [iniciador de aplicaciones](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)del usuario.

* Si se habilita el aprovisionamiento automático, se agregan a la cola de aprovisionamiento aprovisionamiento automáticamente a los asignados usuarios y grupos.

    * Si solo objetos de usuario se han configurado para aprovisionar, a continuación, se colocan todos los usuarios asignado directamente en la cola de aprovisionamiento y todos los usuarios que pertenecen a los grupos asignados se pondrán en la cola de aprovisionamiento. 
    
    * Si agrupar objetos se han configurado para aprovisionar, todos los objetos de grupo asignado se proporciona al cuadro, así como todos los usuarios que sean miembros de dichos grupos. La pertenencia a grupos y usuarios se conserva cuando se escriben en el cuadro.
    
Puede usar el **atributos > Inicio de sesión único** ficha para configurar los atributos de usuario (o reclamaciones) se presentan al cuadro durante la autenticación basada en SAML y la **atributos > aprovisionamiento** ficha para configurar cómo atributos de usuario y de grupo fluyan de Azure AD al cuadro durante el aprovisionamiento de operaciones. Consulte los recursos siguientes para obtener más información.


## <a name="additional-resources"></a>Recursos adicionales

* [Personalizar notificaciones emitidos en el token de SAML](active-directory-saml-claims-customization.md)
* [Aprovisionamiento: Personalizar las asignaciones de atributo](active-directory-saas-customizing-attribute-mappings.md)
* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
