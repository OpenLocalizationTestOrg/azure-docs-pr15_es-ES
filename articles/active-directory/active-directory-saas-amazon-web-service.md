<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con el servicio de Web de Amazon (AWS) | Microsoft Azure"
    description="Aprenda a usar servicios Web de Amazon (AWS) con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más."
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-amazon-web-service-aws"></a>Tutorial: Integración de Azure Active Directory con el servicio de Web de Amazon (AWS)

El objetivo de este tutorial es mostrar cómo integrar el servicio Web de Amazon (AWS) con Azure Active Directory (AD Azure).  
Integración de servicios Web de Amazon (AWS) con Azure AD proporciona las siguientes ventajas: 

- Puede controlar en Azure AD quién tiene acceso al servicio de Web de Amazon (AWS) 
- Puede habilitar los usuarios automáticamente obtener iniciado sesión para el servicio de Web de Amazon (AWS) (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos 

Para configurar la integración de Azure AD con el servicio de Web de Amazon (AWS), necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un servicio Web de Amazon (AWS) inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.  
El escenario descrito en este tutorial consta de tres pilares básicos:

1. Agregar el servicio Web de Amazon (AWS) de la Galería 
2. Configurar y probar Azure AD de sesión único


## <a name="adding-amazon-web-service-aws-from-the-gallery"></a>Agregar el servicio Web de Amazon (AWS) de la Galería
Para configurar la integración de servicio de Web de Amazon (AWS) en Azure AD, debe agregar el servicio Web de Amazon (AWS) de la galería a la lista de aplicaciones de SaaS administradas.

### <a name="to-add-amazon-web-service-aws-from-the-gallery-perform-the-following-steps"></a>Para agregar el servicio Web de Amazon (AWS) de la galería, realice los pasos siguientes:

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1] 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior. 
   
    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página. 
   
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**. 
   
    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba el **Servicio Web de Amazon (AWS)**.
   
    ![Aplicaciones][5]

7. En el panel de resultados, seleccione el **Servicio Web de Amazon (AWS)**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Aplicaciones][6]



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con servicio de Web de Amazon (AWS) en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en el servicio de Web de Amazon (AWS) a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en el servicio de Web de Amazon (AWS).  
Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor de **nombre de usuario** en el servicio de Web de Amazon (AWS).
 
Para configurar y probar Azure AD inicio de sesión único con el servicio de Web de Amazon (AWS), debe completar los siguientes bloques de creación:

1. **[Configurar Azure AD inicio de sesión único](#configuring-azure-ad-single-single-sign-on)** : para habilitar los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Creación de un servicio Web de Amazon (AWS) probar usuario](#creating-a-halogen-software-test-user)** - tener un equivalente de Britta Simon en el servicio de Web de Amazon (AWS) que está vinculado a la representación de Azure AD de sus.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configurar el inicio de sesión único solo de Azure AD

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación de servicio Web de Amazon (AWS).  
La aplicación de servicio Web de Amazon (AWS) espera las afirmaciones SAML en un formato específico, lo que requiere que agregue asignaciones de atributos personalizados a la configuración de **atributos de token saml** . La siguiente captura de pantalla muestra un ejemplo de este.


![Configurar el inicio de sesión único][27]

**Para configurar el inicio de sesión único Azure AD con el servicio de Web de Amazon (AWS), realice los pasos siguientes:**

1. En el portal de clásico Azure, en la página de integración de aplicación de **Servicio Web de Amazon (AWS)** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][7]

2. En la página **¿cómo desea que los usuarios de inicio de sesión para el servicio de Web de Amazon (AWS)** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único][8]

3. En la página de diálogo **Configurar opciones de aplicación** , haga clic en siguiente. 

    ![Establecer la configuración de la aplicación][9]
 
4. En la página **configurar inicio de sesión único en el servicio de Web de Amazon (AWS)** , haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos localmente en el equipo.

    ![Configurar el inicio de sesión único][10]

5. En una ventana de explorador diferente, inicio de sesión en el sitio de su empresa de servicio Web de Amazon (AWS) como administrador.

6. Haga clic en **Inicio de la consola**.

    ![Configurar el inicio de sesión único][11]

7. Haga clic en **administración de identidades y acceso**. 

    ![Configurar el inicio de sesión único][12]

8. Haga clic en **Proveedores de identidades**y, a continuación, haga clic en **Crear proveedor**. 

    ![Configurar el inicio de sesión único][13]

9. En la página de diálogo **Configurar proveedor** , realice los pasos siguientes: 

    ![Configurar el inicio de sesión único][14]

     una. Como **Tipo de proveedor**, seleccione **SAML**.

     b. En el cuadro de texto **Nombre del proveedor** , escriba un nombre de proveedor (por ejemplo: *WAAD*).

     c. Para cargar el archivo descargado metadatos, haga clic en **Elegir archivo**.

     d. Haga clic en el **siguiente paso**.


10. En la página de diálogo **Comprobar la información del proveedor** , haga clic en **crear**. 

    ![Configurar el inicio de sesión único][15]

11. Haga clic en **funciones**y, a continuación, haga clic en **Crear nuevo rol**. 

    ![Configurar el inicio de sesión único][16]

12. En el cuadro de diálogo **Definir nombre de rol** , realice los pasos siguientes: 

    ![Configurar el inicio de sesión único][17]

    una. En el cuadro de texto **Nombre de la función** , escriba un nombre de función (por ejemplo: *usuario de prueba*).

    b. Haga clic en el **siguiente paso**.

13. En el cuadro de diálogo **Seleccionar tipo de rol** , realice los pasos siguientes: 

    ![Configurar el inicio de sesión único][18]

    una. Seleccione el **rol para el acceso de proveedor de identidades**.

    b. En la sección **conceder Web inicio de sesión único (WebSSO) acceso a los proveedores SAML** , haga clic en **Seleccionar**.


14. En el cuadro de diálogo **Establecer confianza** , realice los pasos siguientes:  

    ![Configurar el inicio de sesión único][19]
     
     una. Como proveedor SAML, seleccione el proveedor SAML que haya creado previousley (por ejemplo: *WAAD*) 

     b. Haga clic en el **siguiente paso**.


15. En el cuadro de diálogo **Comprobar la confianza de rol** , haga clic en **Siguiente paso**. 

    ![Configurar el inicio de sesión único][32]


16. En el cuadro de diálogo **Adjuntar directiva** , haga clic en **Siguiente paso**.  

    ![Configurar el inicio de sesión único][33]


17. En el cuadro de diálogo de **revisión** , siga estos pasos:   

    ![Configurar el inicio de sesión único][34]

     una. Copie el valor de la **Función obtener** .

     b. Copie el valor de obtener **Entidades de confianza** .

     c. Haga clic en **crear rol**. 

18. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![¿Qué es Azure AD Connect][20]

19. En la página de **confirmación de inicio de sesión único** , haga clic en **completado** para cerrar el cuadro de diálogo **configurar inicio de sesión único** .

    ![¿Qué es Azure AD Connect][22]


20. En el menú de la parte superior, haga clic en **atributos** para abrir el cuadro de diálogo **Atributos de Token SAML** . 

    ![Configurar el inicio de sesión único][21]

21. Haga clic en **Agregar usuario atributo**. 

    ![Configurar el inicio de sesión único][23]

22. En el cuadro de diálogo Agregar atributo de usuario, realice los pasos siguientes. 

    ![Configurar el inicio de sesión único][24] 

    una. En el cuadro de texto **Nombre de atributo** , escriba **https://aws.amazon.com/SAML/Attributes/Role**.

    b. En el cuadro de texto del **Valor del atributo** , escriba **[el valor de rol obtener], [el valor de confianza entidad obtener]**.

    >[AZURE.TIP] Estos son los valores que ha copiado en el cuadro de diálogo Revisar cuando haya creado su rol. 

    c. Haga clic en **completado** para cerrar el cuadro de diálogo **Agregar atributo de usuario** .

23. Haga clic en **Agregar usuario atributo**. 

    ![Configurar el inicio de sesión único][23]


24. En el cuadro de diálogo Agregar atributo de usuario, realice los pasos siguientes. 

    ![Configurar el inicio de sesión único][25]


     una. En el cuadro de texto **Nombre de atributo** , escriba **https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     b. En el cuadro de texto del **Valor del atributo** , escriba o seleccione **user.userprincipalname** desde la lista desplegable.
     
    ![Configurar el inicio de sesión único][35]
    

     c. Haga clic en **completado** para cerrar el cuadro de diálogo **Agregar atributo de usuario** .


25. Haga clic en **Aplicar cambios**. 

    ![Configurar el inicio de sesión único][26]





### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD

Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.

![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png) 
 
4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**. 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png) 

  1. Como tipo de usuario, seleccione Nuevo usuario de su organización.
  2. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.
  3. Haga clic en siguiente.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el **Último nombre** txtbox, tipo, **Simon**.
  
    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.
  
    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png) 
 
8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.
  
    b. Haga clic en **Finalizar**.   
  
 
### <a name="creating-a-amazon-web-service-aws-test-user"></a>Crear un usuario de prueba de servicio Web de Amazon (AWS)

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en el servicio de Web de Amazon (AWS).

### <a name="to-create-a-user-called-britta-simon-in-amazon-web-service-aws-perform-the-following-steps"></a>Para crear un usuario llamado a Britta Simon en el servicio de Web de Amazon (AWS), siga estos pasos:

1. Inicie sesión como administrador en el sitio de su empresa de **Servicio Web de Amazon (AWS)** .

2. Haga clic en el icono de **Inicio de la consola** . 

    ![Configurar el inicio de sesión único][11]

3. Haga clic en la identidad y administración de acceso. 

    ![Configurar el inicio de sesión único][28]

4. En el panel, haga clic en usuarios y, a continuación, haga clic en crear nuevos usuarios. 

    ![Configurar el inicio de sesión único][29]

5. En el cuadro de diálogo Crear usuario, siga estos pasos: 

    ![Configurar el inicio de sesión único][30]

     una. En los cuadros de texto **Escriba los nombres de usuario** , escriba el nombre de usuario de Brita Simon (userprincipalname) en Azure AD.

     b. Haga clic en **crear**.




### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso al servicio de Web de Amazon (AWS).

![Asignar usuario][31]

**Para asignar a Britta Simon a CloudPassage, siga estos pasos:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][26]

2. En la lista de aplicaciones, seleccione el **Servicio Web de Amazon (AWS)**.

    ![Asignar usuario][27]

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][25]

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][29]

### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.  
Al hacer clic en el mosaico de servicio Web de Amazon (AWS) en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación de servicio Web de Amazon (AWS).


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service/user_attributes_01.png






















