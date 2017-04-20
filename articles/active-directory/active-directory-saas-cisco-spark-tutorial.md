<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con Cisco motor | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y motor de Cisco."
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
    ms.date="10/20/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Tutorial: Integración de Azure Active Directory con Cisco motor

En este tutorial, aprenderá a integrar Cisco motor con Azure Active Directory (AD Azure).

Integración de Cisco motor con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Cisco motor
- Puede habilitar los usuarios automáticamente obtener firmado en Cisco motor (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Cisco motor, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un **Motor de Cisco** inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba. El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar motor Cisco de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-cisco-spark-from-the-gallery"></a>Agregar motor Cisco de la Galería
Para configurar la integración de motor de Cisco en Azure AD, debe agregar motor Cisco desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar motor Cisco de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Cisco motor**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)

7. En el panel de resultados, seleccione **Motor de Cisco**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
En esta sección, configurar y probar el inicio de sesión único Azure AD con motor Cisco en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en Cisco motor a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en Cisco motor.
Asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Cisco motor establecida esta relación de vínculo. Para configurar y probar el inicio de sesión único de Azure AD con Cisco motor, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un motor de Cisco probar usuario](#creating-a-cisco-spark-test-user)** - tener un equivalente de Britta Simon de motor de Cisco que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación de motor de Cisco.

Aplicación de motor de Cisco espera las afirmaciones SAML que contenga atributos específicos. Configure los siguientes atributos de esta aplicación. Puede administrar los valores de estos atributos en la pestaña **"Atrributes"** de la aplicación. La siguiente captura de pantalla muestra un ejemplo de este.

![Configurar el inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**Para configurar el inicio de sesión único Azure AD con Cisco motor, siga estos pasos:**

1. En el portal de clásico Azure, en la página de integración de aplicación **Cisco motor** , en el menú de la parte superior, haga clic en **atributos**.
     
    ![Configurar el inicio de sesión único][5]


2. En el cuadro de diálogo **atributos de token SAML** , realice los pasos siguientes:

    una. Haga clic en **Agregar usuario atributo** para abrir el cuadro de diálogo **Agregar usuario Attribure** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
    
    b. En el cuadro de texto **Nombre de atributo** , escriba **uid**.
    
    c. En la lista **Valor del atributo** , seleccione **user.userprincipal**.
    
    d. Haga clic en **Finalizar**. A continuación, **Aplicar cambios** en la parte inferior de la página.

3. En el menú de la parte superior, haga clic en **Inicio rápido**.

    ![Configurar el inicio de sesión único][6]

4. En el portal clásico, en la página de integración de aplicación **Cisco motor** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][7] 

5. En la página **¿cómo le gustaría usuarios a iniciar sesión el motor de Cisco** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)

6. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)


    una. En el cuadro de texto de inicio de sesión en la dirección URL, escriba una dirección URL mediante el siguiente patrón: `https://web.ciscospark.com/#/signin`.

    b. Haga clic en **siguiente**.


7. En la página **configurar inicio de sesión único en Cisco motor** , haga clic en **descargar metadatos**y, a continuación, guarde el archivo en el equipo.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)

8. Inicie sesión con sus credenciales de administrador total en [Administración de colaboración de nube de Cisco](https://admin.ciscospark.com/) .
9. Seleccione **configuración** y en la sección **autenticación** , haga clic en **Modificar**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)

10. Seleccione **integrar un proveedor de identidades 3 º. (Avanzado)** y vaya a la pantalla siguiente.
11. Haga clic en **Descargar el archivo de metadatos** y guarde el archivo en el equipo.
12. En la página **Importar Idp metadatos** , arrastre y coloque el archivo de metadatos de Azure AD en la página o use la opción del explorador de archivos para localizar y cargar el archivo de metadatos de Azure AD. A continuación, seleccione **Requerir certificado firmado por una entidad emisora de certificados en los metadatos (más seguro)** y haga clic en **siguiente**. 

    ![Configurar el inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)

13. Seleccione **Probar SSO de conexión**y, cuando se abre una nueva pestaña del explorador, autenticar con Azure AD iniciando sesión.
14. Volver a la pestaña **Administración de colaboración de nube de Cisco** de explorador. Si la prueba se realizó correctamente, seleccione **esta prueba fue correcta. Habilitar la opción de inicio de sesión único** y haga clic en **siguiente**.

7. En el portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
    
    ![En el inicio de sesión único de Azure AD][10]

8. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
    
    ![En el inicio de sesión único de Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba en el portal de clásico denominado a Britta Simon.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:
 
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-cisco-spark-test-user"></a>Crear un usuario de prueba de motor de Cisco

En esta sección, creará un usuario denominado a Britta Simon de motor de Cisco. En esta sección, creará un usuario denominado a Britta Simon de motor de Cisco.

1. Vaya a la [Administración de colaboración de nube de Cisco](https://admin.ciscospark.com/) con sus credenciales de administrador total.
2. Haga clic en **usuarios** y, a continuación, en **Administrar usuarios**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. En la ventana **Administrar usuario** , seleccione **Agregar o modificar usuarios manualmente** y haga clic en **siguiente**.
4. Seleccione **nombres y direcciones de correo electrónico**. A continuación, rellene el cuadro de texto como:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**

    b. En el cuadro de texto **Apellido** , escriba **Simon**

    c. En el cuadro de texto **dirección de correo electrónico** , escriba**britta.simon@contoso.com**

5. Haga clic en el signo más para agregar a Britta Simon. A continuación, haga clic en **siguiente**.
6. En la ventana **Agregar servicios para los usuarios** , haga clic en **Guardar** y, a continuación, en **Finalizar**.

### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a Cisco motor.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Cisco motor, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Motor de Cisco**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de todos los usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]


### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico de motor de Cisco en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación de motor de Cisco.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png
