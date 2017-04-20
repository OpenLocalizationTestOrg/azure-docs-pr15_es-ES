<properties
   pageTitle="Tutorial detallado de uso de la vista previa de colaboración de Azure Active Directory B2B | Microsoft Azure"
   description="Colaboración de Azure B2B de Active Directory es compatible con las relaciones de su compañía habilitando socios empresariales de forma selectiva tengan acceso a sus aplicaciones corporativas"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-detailed-walkthrough"></a>Vista previa colaboración de Azure AD B2B: tutorial detallado

Este tutorial describe cómo usar la colaboración de Azure AD B2B. Como administrador de TI de Contoso, queremos compartir aplicaciones con los empleados de las empresas asociadas de tres. Ninguna de las empresas asociadas necesitan tener Azure AD.

- Ana de Partner Simple organigrama
- Bob de medio asociado Org, necesita acceso a un conjunto de aplicaciones
- Carol de Org complejos de Partner, necesita acceso a un conjunto de aplicaciones y la pertenencia a grupos de Contoso

Después de que las invitaciones se envían a los usuarios de partner, se puede configurar en Azure AD para conceder acceso a aplicaciones y pertenencia a grupos a través del portal de Azure. Empecemos agregando Ana.

## <a name="adding-alice-to-the-contoso-directory"></a>Agregar al directorio de Contoso Ana
1. Crear un archivo .csv con los encabezados tal como se muestra, rellenar sólo **correo electrónico**de Ana, **DisplayName**y **InviteContactUsUrl**. **DisplayName** es el nombre que aparece en la invitación y también el nombre que aparece en el directorio de Contoso Azure AD. **InviteContactUsUrl** es una manera de Ana ponerse en contacto con Contoso. En el ejemplo siguiente, InviteContactUsUrl especifica el perfil de LinkedIn de Contoso. Es importante revisar las etiquetas en la primera fila del archivo .csv exactamente como se especifica en la [referencia de formato de archivo CSV](active-directory-b2b-references-csv-file-format.md).  
![Archivo CSV de ejemplo para Ana](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. En el portal de Azure, agregue un usuario en el directorio de Contoso (Active Directory > Contoso > usuarios > Agregar usuario). En el ""tipo de usuario desplegable, seleccione "Usuarios de las empresas asociadas". Cargue el archivo .csv. Asegúrese de que el archivo .csv está cerrado antes de cargar.  
![Cargar un archivo CSV para Ana](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Ana ahora se representa como un usuario externo en el directorio de Azure AD de Contoso.  
![Ana aparece en Azure AD](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. Ana recibe el correo electrónico siguiente.  
![Correo electrónico de invitación de Ana](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Ana hace clic en el vínculo y se le pide que acepte la invitación y para iniciar sesión con las credenciales de su trabajo. Si Ana no está en el directorio de Azure AD, Ana se pide inicio de sesión.  
![Iniciar sesión después de la invitación de Ana](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Ana se redirige al Panel de acceso de aplicación vacía hasta que se ha concedido acceso a aplicaciones.  
![Panel de acceso para Ana](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

Este procedimiento permite la forma más sencilla de colaboración B2B. Como un usuario en el directorio de Contoso Azure AD, Ana puede conceder acceso a las aplicaciones y los grupos a través del portal de Azure. Ahora vamos a agregar Bob, quién necesita obtener acceso a las aplicaciones Moodle y Salesforce.

## <a name="adding-bob-to-the-contoso-directory-and-granting-access-to-apps"></a>Agregar Bob en el directorio de Contoso y conceder acceso a aplicaciones
1. Usar Windows PowerShell con el módulo de Azure AD instalado para buscar la aplicación identificadores de Moodle y Salesforce. Los identificadores se pueden recuperar mediante el cmdlet: `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` Esto incorpora una lista de todas las aplicaciones disponibles en Contoso y sus AppPrincialIds.  
![Recuperar identificadores de Bob](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. Crear un archivo .csv que contiene el correo electrónico y DisplayName, **InviteAppID**, **InviteAppResources**y InviteContactUsUrl de Bob. Rellenar **InviteAppResources** con las AppPrincipalIds de Moodle y Salesforce encontrado de PowerShell, separado por un espacio. Rellenar **InviteAppId** con el mismo AppPrincipalId de Moodle para personalizar el correo electrónico y las páginas con un logotipo de Moodle de inicio de sesión.  
![Archivo CSV de ejemplo para Luis](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. Cargue el archivo .csv a través del Portal de Azure tal como lo ha hecho para Ana. Bob ahora es un usuario externo en el directorio de Azure AD de Contoso.

4. Bob recibe el correo electrónico siguiente.  
![Correo electrónico de invitación de Bob](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. Bob hace clic en el vínculo y se le pide que aceptar la invitación. Después de que ha iniciado sesión en, dirige al Panel de acceso y ya puede usar Moodle y Salesforce.  
![Panel de acceso para Luis](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

Vamos a agregar Carol a continuación, quién tiene acceso a las aplicaciones, así como la pertenencia a grupos en el directorio de Contoso.

## <a name="adding-carol-to-the-contoso-directory-granting-access-to-apps-and-giving-group-membership"></a>Agregar al directorio de Contoso Carol, conceder acceso a aplicaciones y darle la pertenencia a grupos

1. Usar Windows PowerShell con el módulo de Azure AD instalado para buscar los identificadores de aplicación y de grupo de Contoso.
 - Recuperar AppPrincipalId con cmdlet `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`, igual que para Bob
 - Recuperar el Id. de objeto para grupos mediante el cmdlet `Get-MsolGroup | fl DisplayName, ObjectId`. Aparece una lista de todos los grupos de Contoso y sus outfile. También se pueden recuperar los identificadores de grupo como el identificador de objeto en la ficha de propiedades del grupo en el portal de Azure.  
![Recuperar identificadores y grupos de Carol](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. Crear el archivo .csv, rellenar de Carol correo electrónico, DisplayName, InviteAppID, InviteAppResources, **InviteGroupResources**y InviteContactUsUrl. **InviteGroupResources** se rellena la outfile de los grupos MyGroup1 y referencias externas, separados por un espacio.  
![Archivo CSV de ejemplo para Carol](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. Cargue el archivo .csv a través del portal de Azure.

4. Carol es un usuario en el directorio de Contoso y también es un miembro de los grupos MyGroup1 y externos, tal como se muestra en el portal de Azure.  
![Carol aparece en un grupo en Azure AD](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. Carol recibe un correo electrónico que contiene un vínculo para aceptar la invitación. Una vez que inicia sesión, se redirige al Panel de acceso de la aplicación para tener acceso a Moodle y Salesforce.  

Eso es todo para agregar usuarios de empresas asociado en la colaboración de Azure AD B2B. En este tutorial se ha mostrado cómo agregar usuarios, Ana, Bob y Carol al directorio de Contoso con tres archivos .csv independiente. Este proceso puede ser más fácil concentrando los archivos .csv independiente en un único archivo.  
![Archivo CSV de ejemplo para Ana, Bob y Carol](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## <a name="related-articles"></a>Artículos relacionados
Examinar nuestras otros artículos de colaboración de Azure AD B2B:

- [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Cómo funciona](active-directory-b2b-how-it-works.md)
- [Referencia de formato de archivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato de token de usuario externo](active-directory-b2b-references-external-user-token-format.md)
- [Cambios de atributos del objeto de usuario externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitaciones de vista previa actual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
