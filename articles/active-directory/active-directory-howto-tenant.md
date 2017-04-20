<properties
    pageTitle="Cómo obtener un inquilino de Azure AD | Microsoft Azure"
    description="Cómo obtener a un inquilino de Azure Active Directory para el registro y la creación de aplicaciones."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="terrylan"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/28/2015"
    ms.author="dastrock"/>

# <a name="how-to-get-an-azure-active-directory-tenant"></a>Cómo obtener a un inquilino de Azure Active Directory

En Azure Active Directory (AD Azure), un [inquilino](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) es representante de una organización.  Es una instancia dedicada del servicio de Azure AD que una organización recibe y posee cuando firma para un servicio de nube de Microsoft como Azure, Intune Microsoft u Office 365.  Cada inquilino de Azure AD es distinto e independiente de otros inquilinos de Azure AD.  

Un inquilino aloja los usuarios de una compañía y la información acerca de ellos, sus contraseñas, datos de perfil de usuario, permisos y así sucesivamente.  También contiene grupos, aplicaciones y otra información relativa a una organización y su seguridad.

Para permitir que los usuarios de Azure AD iniciar sesión en la aplicación, debe registrar la aplicación en un inquilino de su elección.  Publicar una aplicación en un inquilino de Azure AD es **totalmente gratuita**.  De hecho, la mayoría de los desarrolladores creará varios inquilinos y aplicaciones para experimentación, desarrollo, ensayo y realizar pruebas.  Si lo desea pueden elegir comprar licencias si desean aprovechar las características avanzadas del directorio de organizaciones que suscribirse y utilizar la aplicación.

¿Por lo tanto, qué tiene que hacer acerca de cómo obtener un inquilino de Azure AD?  El proceso puede ser un poco if diferentes:

- [Tiene una suscripción existente de Office 365](#use-an-existing-office-365-subscription)
- [Tiene una suscripción existente de Azure asociada a una Account de Microsoft](#use-an-msa-azure-subscription)
- [Tiene una suscripción existente de Azure asociada a una cuenta profesional](#use-an-organizational-azure-subscription)
- [No tiene ninguno de los anteriores y desea empezar desde cero](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>Usar una suscripción existente de Office 365
Si tiene una suscripción existente de Office 365, ya dispone de un inquilino de Azure AD! Puede iniciar sesión en el [portal de Azure](https://portal.azure.com) con su cuenta de O365 y empezar a usar Azure AD.

## <a name="use-an-msa-azure-subscription"></a>Usar una suscripción de Azure MSA
Si ha anteriormente se registró para obtener una suscripción de Azure con su Account Microsoft individuales, ya dispone de un inquilino!  Cuando inicie sesión en el [Portal de Azure](https://portal.azure.com), automáticamente se registrará el inquilino de forma predeterminada. Es libre para usar a este inquilino como le convenga - pero desea crear una cuenta de administrador de la organización.

Para ello, siga estos pasos.  Como alternativa, puede crear a un nuevo inquilino y crear un administrador de dicho inquilino siguiendo un proceso similar.

1.  Inicie sesión en el [Portal de Azure](https://portal.azure.com) con su cuenta individual
2.  Vaya a la sección "Azure Active Directory" del portal (que se encuentra en la barra de navegación izquierda, en **Más servicios**)
3.  Debe automáticamente haber iniciado sesión en el "directorio predeterminado", si no puede cambiar directorios haciendo clic en el nombre de su cuenta en la esquina superior derecha.
4.  En la sección **Tareas rápidas** , seleccione **Agregar un usuario**.
5.  En el formulario de usuario agregar, proporcione la siguiente información:

    - Nombre: (elija un valor adecuado)
    - Nombre de usuario: (elija un nombre de usuario para el administrador)
    - Perfil: (rellene los valores apropiados para el nombre, apellido nombre, cargo y departamento)
    - Función: Administrador Global

6.  Cuando haya completado el formulario de usuario agregar y recibir una contraseña temporal para el nuevo usuario administrativo, asegúrese de registrar esta contraseña como necesite iniciar sesión con este nuevo usuario con el fin de cambiar la contraseña. También puede enviar la contraseña directamente al usuario, usando un correo electrónico alternativo.
7.  Haga clic en **crear** para crear el nuevo usuario.
8.  Para cambiar la contraseña temporal, inicie sesión en [https://login.microsoftonline.com](https://login.microsoftonline.com) con esta nueva cuenta de usuario y cambiar la contraseña cuando se solicita.


## <a name="use-an-organizational-azure-subscription"></a>Usar una suscripción de Azure organizativa
Si ha anteriormente se registró para obtener una suscripción de Azure con su cuenta profesional, ya dispone de un inquilino!  En el [Portal de Azure](https://portal.azure.com), debe buscar a un inquilino cuando vaya a "Más servicios" y "Azure Active Directory."  Es libre para usar a este inquilino como le convenga. 


## <a name="start-from-scratch"></a>Empezar desde cero
Si todas las opciones anteriores es galimatías a usted, no se preocupe.  Simplemente visite [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) para registrarse a Azure con una organización de nueva.  Una vez haya completado el proceso, tendrá su propia inquilino de Azure AD con el nombre de dominio que eligió durante el inicio de sesión hacia arriba.  En el [Portal de Azure](https://portal.azure.com), puede encontrar a su inquilino, vaya a "Azure Active Directory" en la navegación de la izquierda

Como parte del proceso de inicio de sesión para Azure, se le pedirá que proporcione la tarjeta de crédito.  Puede continuar con confianza - no se cargará para publicar aplicaciones en Azure AD o creación de inquilinos de nuevos.
