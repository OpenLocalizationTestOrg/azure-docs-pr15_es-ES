<properties
    pageTitle="Trabajar con conectores de Proxy de aplicación de Azure AD | Microsoft Azure"
    description="Explica cómo crear y administrar grupos de conectores de Proxy de aplicación de Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/09/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicar aplicaciones en redes independientes y ubicaciones usando grupos de conector

> [AZURE.SELECTOR]
- [Portal de Azure](active-directory-application-proxy-connectors-azure-portal.md)
- [Portal de clásico de Azure](active-directory-application-proxy-connectors.md)


Grupos de conector son útiles para distintas situaciones, incluidos:

- Sitios con varios centros de datos interconectados. En este caso, desea conservar tanto el tráfico del centro de datos como sea posible dado vínculos entre centros de datos suelen ser muy costosas y lentas. Puede implementar los conectores en cada centro de datos para proporcionar servicio solo las aplicaciones que residen en el centro de datos. Este enfoque minimiza vínculos de centro de datos cruzados y ofrece una experiencia completamente transparente a los usuarios.
- Administración de aplicaciones instaladas en redes aisladas que no forman parte de la red corporativa principal. Puede usar grupos de conector para instalar conectores dedicados en redes aisladas también aislar aplicaciones a la red.
- Para las aplicaciones instaladas en IaaS para el acceso a la nube, los grupos de conector proporcionan un servicio común para proteger el acceso a todas las aplicaciones. Grupos de conector no crear dependencia adicional en su red corporativa o fragmentar la experiencia de aplicación. Conectores se pueden instalar en cada centro de datos de la nube y servir solo las aplicaciones que residen en esta red. Puede instalar varios conectores para lograr una alta disponibilidad.
- Soporte para entornos de varios bosques en el que pueden implementados por bosque conectores específicos y establecer para servir aplicaciones específicas.
- Grupos de conector pueden usarse en los sitios de recuperación bien detectar migración tras error o como copia de seguridad para el sitio principal.
- Grupos de conector también pueden utilizarse para proporcionar el servicio de varias empresas de un sola inquilino.

## <a name="prerequisite-create-your-connectors"></a>Requisito previo: Crear los conectores
Para agrupar los conectores, tiene que hacer que [instalado varios conectores](active-directory-application-proxy-enable.md)y ese nombre ellos y agruparlos. Por último, debe asignarlos a aplicaciones específicas.

## <a name="step-1-create-connector-groups"></a>Paso 1: Crear grupos de conector
Puede crear todos los grupos de conector que desee. Creación de grupos de conector se realiza en el portal de clásico de Azure.

1. Seleccione el directorio y haga clic en **Configurar**.  
    ![Proxy de aplicación, configure la captura de pantalla, haga clic en administrar grupos de conector](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

2. En Proxy de la aplicación, haga clic en **Administrar grupos de conector** y cree un nuevo grupo de conector asignando un nombre de grupo.  
    ![Captura de pantalla de aplicación proxy conector grupos - nombre nuevo grupo](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## <a name="step-2-assign-connectors-to-your-groups"></a>Paso 2: Asignar conectores a los grupos
Una vez que se crean los grupos de conector, mueva los conectores al grupo adecuado.

1. **Proxy de aplicación**, haga clic en **Administrar los conectores**.
2. En el **grupo**, seleccione el grupo que quiera para cada conector. Puede llevar los conectores hasta 10 minutos a estar activo en el grupo nuevo.  
    ![Captura de pantalla de aplicación proxy conectores - Seleccionar grupo en menú desplegable](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## <a name="step-3-assign-applications-to-your-connector-groups"></a>Paso 3: Asignar aplicaciones a los grupos de conector
El último paso es configurar cada aplicación para el grupo de conector que servirá a esta.

1. En el portal de clásico Azure, en el directorio, seleccione la aplicación que desea asignar al grupo y haga clic en **Configurar**.
2. En el **grupo de conector**, seleccione el grupo que desee utilizar la aplicación. Este cambio se aplicará inmediatamente.  
    ![Captura de pantalla de aplicación proxy conector grupo - Seleccionar grupo en menú desplegable](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)


## <a name="see-also"></a>Vea también

- [Habilitar a Proxy de aplicación](active-directory-application-proxy-enable.md)
- [Habilitar inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
- [Solucionar problemas que tiene con Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

Para las últimas noticias y actualizaciones, consulte el [blog de Proxy de aplicación](http://blogs.technet.com/b/applicationproxyblog/)
