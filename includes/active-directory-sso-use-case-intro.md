Las organizaciones usan más aplicaciones de [Software como servicio (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) para la productividad porque herramientas y tecnología de nube son cada vez más disponibles. A medida que crece el número de aplicaciones de SaaS, se convierte en un reto para los administradores administrar las cuentas y los derechos de acceso y para los usuarios a recordar las contraseñas diferentes. Administrar estas aplicaciones individualmente crea trabajo adicional y es menos seguro.


- Empleados que tienen que realizar un seguimiento de muchas contraseñas suelen usar métodos menos seguros de recordar, escribirlas o con las mismas contraseñas entre varias cuentas.

- Cuando llega un nuevo empleado o sale de uno, todas sus cuentas deben proporcionarse o anule la se aprovisione individualmente.

- Además, pueden empezar a usar aplicaciones de SaaS su trabajo sin pasar por empleados TI, lo que significa que va a crear sus propias cuentas en los sistemas que los administradores de TI todavía no lo ha aprobado y no están supervisión.  

Es una solución para todos estos desafíos de inicio de sesión único (SSO). Es la manera más sencilla de administrar varias aplicaciones y proporcionar a los usuarios una experiencia de inicio de sesión coherente. Azure Active Directory (AD Azure) proporciona una solución SSO sólida y tiene muchas aplicaciones integradas previamente disponibles, con tutoriales para administradores configurar una nueva aplicación y comenzar el aprovisionamiento de usuarios rápidamente.


## <a name="how-does-azure-active-directory-integrate-apps"></a>¿Cómo se integra aplicaciones de Azure Active Directory?  

Azure AD permite integrar sus cuentas de preparación y aplicaciones. Esto puede hacerse a través de uno de estos dos enfoques.

- Si la aplicación es previamente integrada en la aplicación de la galería, puede ir a través de ese portal para configurar las aplicaciones y configurar la configuración para permitir SSO. Para cualquier aplicación de la galería, puede empezar por, siga las instrucciones de paso a paso simples realizadas en la Galería de la aplicación y en el portal de Azure para habilitar el inicio de sesión único.

- Si la aplicación no está en la galería, puede configurar la mayoría de aplicaciones de Azure AD como una aplicación personalizada. Esto requiere un poco más experiencia técnica para configurar. Puede agregar cualquier aplicación que admita SAML 2.0 como una aplicación federada o cualquier aplicación que tenga una página de inicio de sesión del basada en HTML como una aplicación SSO de contraseña.

En el caso de que los usuarios han creado sus propias cuentas para las aplicaciones SaaS no administrado por TI, la herramienta de [Detección de la aplicación de nube](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) proporciona una solución. Esta herramienta supervisa el tráfico de web para identificar qué aplicaciones se utilizan en toda la organización y el número de personas con cada uno de ellos. TI puede usar esta información para averiguar qué aplicaciones de los usuarios prefieren y decide que desea integrar en Azure AD SSO.  

Cuando se integra una aplicación en Azure AD, puede asignar identidades de los usuarios de aplicaciones establecidos a sus respectivos identidades de Azure AD.  
