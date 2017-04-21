<properties
    pageTitle="Servidor de Azure AMF nube vs | Microsoft Azure"
    description="Elija la solución de secutiry de autenticación multifactor derecha para cuando se pregunta qué estoy i intenta proteger y ¿dónde están mis usuarios que se encuentra.  A continuación, elija nube AMF Server o AD FS."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

#<a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Elegir la solución de autenticación multifactor de Azure para usted

Dado que hay varios tipos de autenticación de varios factores de Azure (AMF), nos debemos responda algunas preguntas para averiguar qué versión es la adecuada para usar.  Estas preguntas son:

-   [¿Qué estoy intentando iniciar seguro](#what-am-i-trying-to-secure)
-   [¿Dónde se encuentran los usuarios](#where-are-the-users-located)
- [¿Qué características debo usar?](#what-featured-do-i-need)

Las siguientes secciones proporcionan instrucciones sobre cómo determinar cada una de estas respuestas.

## <a name="what-am-i-trying-to-secure"></a>¿Qué estoy intentando seguro?

Para determinar la solución de comprobación correcta en dos pasos, primero se debemos responder a la pregunta de ¿cuáles son las que intenta seguro con un segundo método de autenticación.  ¿Es una aplicación que se encuentra en Azure?  ¿O un sistema de acceso remoto?  Por determinar qué estamos intentando seguro, nos podemos responder a la pregunta de dónde debe estar habilitada la autenticación multifactor.  


¿Qué se intenta proteger| Autenticación multifactor en la nube|Servidor de autenticación multifactor
------------- | :-------------: | :-------------: |
Aplicaciones de Microsoft de cookies|● |● |
Aplicaciones de SaaS en la Galería de aplicación|● |● |
Aplicaciones de IIS publicadas a través de Proxy de aplicación de Azure AD|● |● |
Aplicaciones de IIS que no se publican a través de Proxy de aplicación de Azure AD | |● |
Acceso remoto como VPN, RDG| |● |



## <a name="where-are-the-users-located"></a>¿Dónde se encuentran los usuarios

A continuación, mostrando dónde se encuentran los usuarios le ayuda a determinar la solución correcta para usar, ya sea en la nube o local con el servidor AMF.



Ubicación de usuario| Autenticación multifactor en la nube|Servidor de autenticación multifactor
------------- | :-------------: | :-------------: |
Azure Active Directory|● | |
Azure AD y utiliza la federación con AD FS de AD local|● |● |
Azure AD y AD mediante la sincronización de directorios de Azure AD Sync, Azure AD Connect - sin sincronización de la contraseña local|● |● |
Azure AD y local AD mediante la sincronización de directorios de Azure AD Sync, Azure AD Connect - con la sincronización de contraseña|● | |
Local de Active Directory| |● |

## <a name="what-features-do-i-need"></a>¿Qué características debo usar?

En la tabla siguiente es una comparación de las características que están disponibles con autenticación multifactor en la nube y con el servidor de autenticación multifactor.

 | Autenticación multifactor en la nube | Servidor de autenticación multifactor
------------- | :-------------: | :-------------: |
Notificación de la aplicación móvil como un segundo factor | ● | ● |
Código de comprobación de la aplicación móvil como un segundo factor | ● | ●
Llamada de teléfono como segundo factor | ● | ●
Unidireccional SMS como segundo factor | ● | ●
SMS bidireccionales como segundo factor |  | ●
Tokens de hardware como segundo factor |  | ●
Contraseñas de aplicaciones para los clientes que no son compatibles con MFA | ● |  
Control de administrador sobre métodos de autenticación | ● | ●
Modo PIN |  | ●
Alerta de fraude | ● | ●
Informes de AMF | ● | ●
Omitir una sola vez |  | ●
Saludos personalizados para las llamadas de teléfono | ● | ●
Identificador de llamada personalizables para las llamadas de teléfono | ● | ●
Direcciones IP de confianza | ● | ●
Recuerde AMF para dispositivos de confianza  | ● |  
Acceso condicional | ● | ●
Caché |  | ●

Ahora que hemos determinado si usar autenticación multifactor de nube o AMF servidor local, podemos comenzar configuración y el uso de la autenticación multifactor de Azure. **¡Seleccione el icono que representa el escenario!**

<center>




[![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>
