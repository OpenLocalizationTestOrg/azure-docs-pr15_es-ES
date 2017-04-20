<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con la nube Lifesize | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y nube de Lifesize."
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
    ms.date="10/04/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Tutorial: Integración de Azure Active Directory con la nube Lifesize

En este tutorial, aprenderá a integrar Lifesize nube con Azure Active Directory (AD Azure).

Integración de nube Lifesize con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a la nube de Lifesize
- Puede habilitar los usuarios automáticamente obtener firmado en nube Lifesize (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Lifesize nube, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Una nube Lifesize inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar Lifesize nube desde la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-lifesize-cloud-from-the-gallery"></a>Agregar Lifesize nube desde la Galería
Para configurar la integración de nube Lifesize en Azure AD, debe agregar Lifesize nube desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar Lifesize nube desde la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory][1]
2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Lifesize nube**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)

7. En el panel de resultados, seleccione **Nube Lifesize**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
En esta sección, configurar y probar Azure AD inicio de sesión único con Lifesize nube basada en un usuario de prueba denominado a "Britta Simon".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en nube Lifesize a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en nube Lifesize.

Asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en nube Lifesize establecida esta relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con Lifesize nube, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
3. **[Crear una nube Lifesize probar usuario](#creating-a-lifesize-cloud-test-user)** - tener un equivalente de Britta Simon en nube Lifesize que está vinculado a la representación de Azure AD de ella.
4. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD

En esta sección, habilitar inicio de sesión único de Azure AD en el portal de clásico y configurar el inicio de sesión único en la aplicación de nube de Lifesize.


**Para configurar el inicio de sesión único Azure AD con Lifesize nube, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de aplicación de **Nube de Lifesize** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .
     
    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo le gustaría usuarios a iniciar sesión nube Lifesize** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png) 

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de nube Lifesize mediante el siguiente patrón: **https://login.lifesizecloud.com/ls/?acs**.
    
    b. Haga clic en **siguiente**
 
4. En la página **configurar inicio de sesión único en nube Lifesize** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


5. Para obtener el SSO configurado para la aplicación, inicio de sesión en la aplicación de nube de Lifesize con privilegios de administrador.

6. En la esquina superior derecha, haga clic en su nombre y, a continuación, haga clic en la **Configuración avanzada**

    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

7. En la configuración avanzada ahora haga clic en el vínculo **Configuración de SSO** . Se abrirá la página de configuración de SSO para la instancia.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

8. Ahora puede configurar los valores siguientes en el interfaz de usuario de configuración de SSO.    

    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    • Copie el valor de dirección URL del emisor de Azure AD y pegarlo en el cuadro de texto de **Emisor del proveedor de identidades** .

    • Copie el valor de dirección URL de inicio de sesión remota de Azure AD y pegarlo en el cuadro de texto **Dirección URL de inicio de sesión** .

    • Abra el certificado descargado en el Bloc de notas y copie el contenido del certificado, excluyendo las líneas comenzar y certificado de finalización, pegue esto en el cuadro de texto de **Certificado X.509** .

    • En la asignación de atributos de SAML para el cuadro de texto **nombre** escriba el valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**

    • En la asignación de atributos de SAML **Apellido** cuadro de texto escriba el valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**

    • En la asignación de atributos de SAML para el cuadro de texto de **correo electrónico** especifique el valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

9. Para comprobar la configuración, puede hacer clic en el botón de **prueba** .

    > [AZURE.NOTE] Para realizar pruebas correcta debe completar al Asistente para configuración de Azure AD y también proporcionar acceso a los usuarios o grupos que pueden realizar la prueba.
    
10. Habilitar el SSO haciendo clic en el botón **Habilitar SSO** .

11. Ahora haga clic en el botón de **actualización** para que se guarden todos los valores. Esto generará el valor de RelayState. Copie el valor de RelayState que se genera en el cuadro de texto. Debemos este valor en los siguientes pasos.

12. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
    
    ![En el inicio de sesión único de Azure AD][10]

13. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
 
    ![En el inicio de sesión único de Azure AD][11]

14. Ahora inicie sesión en el Portal de administración de Azure **https://portal.azure.com** con credenciales de administrador

15. Haga clic en el vínculo **Más servicios** en el panel de navegación izquierdo
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)

16. Búsqueda de Azure Active Directory y haga clic en el vínculo de **Azure Active Directory**
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)

17. Encontrará todas las aplicaciones de SaaS debajo del botón de **Aplicaciones empresariales** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)

18. Ahora haga clic en el vínculo de **Todas las aplicaciones** de la siguiente hoja
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)

19. Busque la aplicación Lifesize para el que desea configurar la RelayState. 
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)

20. Ahora haga clic en vínculo de **Inicio de sesión único** en el módulo

    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)

21. Verá que la casilla de verificación **Mostrar avanzadas de configuración de la dirección URL** . Haga clic en la casilla de verificación.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
    
22. Configurar la RelayState para la aplicación, que puede ver en la página de configuración de Lifesize aplicación SSO. 

    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)

23. Guardar la configuración.

### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba en el portal de clásico denominado a Britta Simon.


![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , siga estos pasos:  ![crear un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , siga estos pasos: ![crear un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-an-lifesize-cloud-test-user"></a>Crear un usuario de prueba de nube de Lifesize

En esta sección, creará un usuario llamado a Britta Simon en nube Lifesize. Nube de Lifesize admite aprovisionamiento automática de usuario. Tras la autenticación correcta en Azure AD, el usuario se establecerá automáticamente en la aplicación. 


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a la nube Lifesize.

![Asignar usuario][200] 

**Para asignar a Britta Simon en nube Lifesize, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Lifesize nube**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 

3. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203]

4. En la lista de usuarios, seleccione **Bárbara Menéndez**.

5. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]


### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

En esta sección, pruebe su Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el icono de nube de Lifesize en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación de nube de Lifesize.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png
