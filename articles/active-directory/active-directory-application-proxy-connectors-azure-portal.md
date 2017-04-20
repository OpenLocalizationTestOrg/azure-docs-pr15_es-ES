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


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups---public-preview"></a>Publicar aplicaciones en ubicaciones de uso de los grupos de conector - Public Preview y redes independientes

> [AZURE.SELECTOR]
- [Portal de Azure](active-directory-application-proxy-connectors-azure-portal.md)
- [Portal de clásico de Azure](active-directory-application-proxy-connectors.md)


Grupos de conector son útiles para distintas situaciones, incluidos:

- Sitios con varios centros de datos interconectados. En este caso, desea conservar tanto el tráfico del centro de datos como sea posible, como los vínculos de centro de datos cruzados son muy costosas y lentas. Puede implementar los conectores en cada centro de datos para proporcionar servicio solo las aplicaciones que residen en el centro de datos. Este enfoque minimiza vínculos de centro de datos cruzados y ofrece una experiencia completamente transparente a los usuarios.
- Administración de aplicaciones instaladas en redes aisladas que no forman parte de la red corporativa principal. Puede usar grupos de conector para instalar conectores dedicados en redes aisladas también aislar aplicaciones a la red.
- Para las aplicaciones instaladas en IaaS para el acceso a la nube, los grupos de conector proporcionan un servicio común para proteger el acceso a todas las aplicaciones. Grupos de conector no crear dependencia adicional en su red corporativa o fragmentar la experiencia de aplicación. Conectores se pueden instalar en cada centro de datos de la nube y servir solo las aplicaciones que residen en esta red. Puede instalar varios conectores para lograr una alta disponibilidad.
- Soporte para entornos de varios bosques en el que pueden implementados por bosque conectores específicos y establecer para servir aplicaciones específicas.
- Grupos de conector pueden usarse en los sitios de recuperación bien detectar migración tras error o como copia de seguridad para el sitio principal.
- Grupos de conector también pueden utilizarse para proporcionar el servicio de varias empresas de un sola inquilino.

## <a name="prerequisite-create-your-connectors"></a>Requisito previo: Crear los conectores
Para agrupar los conectores, debe asegurarse de que [múltiples conectores instalados](active-directory-application-proxy-enable.md). Cuando se instala un nuevo conector, une automáticamente el grupo de conector **predeterminado** .

## <a name="step-1-create-connector-groups"></a>Paso 1: Crear grupos de conector
Puede crear todos los grupos de conector que desee. Creación de grupos de conector se realiza en el [portal de Azure](https://portal.azure.com).

1. Seleccione **Azure Active Directory** para ir al panel de administración para el directorio. Desde allí, seleccione **aplicaciones empresariales** > **proxy de la aplicación**.

2. Seleccione el botón de **Grupos de conector** . Aparece el módulo de nuevo grupo de conector.

3. Asigne un nombre a su nuevo grupo de conector y luego use el menú desplegable para seleccionar qué conectores pertenecen a este grupo.

4. Seleccione **Guardar** cuando finalice el conector grupo.

## <a name="step-2-assign-applications-to-your-connector-groups"></a>Paso 2: Asignar aplicaciones a los grupos de conector
El último paso es configurar cada aplicación para el grupo de conector que servirá a esta.

1. En el panel de administración para el directorio, seleccione **aplicaciones empresariales** > **todas las aplicaciones** > la aplicación que desea asignar a un grupo de conector > **Proxy de la aplicación**.
2. En **grupo de conector**, use el menú desplegable para seleccionar el grupo que desee utilizar la aplicación.
3. Seleccione **Guardar** para aplicar el cambio.


## <a name="see-also"></a>Vea también

- [Habilitar a Proxy de aplicación](active-directory-application-proxy-enable.md)
- [Habilitar inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
- [Solucionar problemas que tiene con Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

Para las últimas noticias y actualizaciones, consulte el [blog de Proxy de aplicación](http://blogs.technet.com/b/applicationproxyblog/)
