<properties
    pageTitle="Azure Active Directory Domain Services: Escenarios de implementación | Microsoft Azure"
    description="Escenarios de implementación de servicios de dominio de Active Directory de Azure"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>


# <a name="deployment-scenarios-and-use-cases"></a>Escenarios de implementación y casos de uso
En esta sección, veremos algunos escenarios y casos de uso que aprovecharse de los servicios de dominio de Azure Active Directory (AD).

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Proteger, facilitar la administración de máquinas virtuales de Windows Azure
Puede usar servicios de dominio de Azure Active Directory para administrar sus Azure máquinas virtuales de una manera simplificada. Máquinas virtuales de Windows Azure pueden unirse al dominio administrado, permitiéndole utilice sus credenciales de AD corporativas para iniciar sesión. Este enfoque ayuda a evitar problemas de administración de credenciales como el mantenimiento de cuentas de administrador local en cada uno de sus máquinas virtuales Azure.

Máquinas virtuales de servidor que se unen al dominio administrado también pueden administrar y proteger mediante la directiva de grupo. Puede aplicar seguridad requerida previsiones a los equipos virtuales Azure y bloquear conforme a las directrices de seguridad de la empresa. Por ejemplo, puede usar las capacidades de administración de directiva de grupo para restringir los tipos de aplicaciones que se pueden iniciar en estas máquinas virtuales.

![Administración simplificada de máquinas virtuales de Windows Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Los servidores y otras infraestructuras alcanza el final del ciclo de vida, Contoso está moviendo muchas aplicaciones que se hospeda actualmente local en la nube. Su estándar de TI actual, se exige que servidores que alojan aplicaciones corporativas deben Unidos a un dominio y administrar por medio de directiva de grupo. Contoso prefiere dominio combinación máquinas virtuales de Windows implementada en Azure, Administrador de TI para facilitar la administración. Como resultado, los administradores y usuarios pueden iniciar sesión con sus credenciales corporativas. Al mismo tiempo, máquinas se pueden configurar para cumplir con las líneas de base de seguridad necesarias mediante la directiva de grupo. Contoso preferiría no tiene que implementar, supervisar y administrar los controladores de dominio en Azure proteger máquinas virtuales de Windows Azure. Por lo tanto, los servicios de dominio de Azure AD es muy apropiado para este caso de uso.

**Notas de la implementación**

Tenga en cuenta los siguientes puntos importantes para este escenario de implementación:

- Dominios administrados proporcionadas servicios de dominio de Active Directory de Azure proporcionan una sola estructura OU (unidad organizativa) plana predeterminada. Todos los equipos de dominio residen en una única unidad organizativa plana. Sin embargo, puede crear unidades organizativas personalizadas.

- Servicios de dominio de Azure AD admite simple directiva de grupo en el formulario de un GPO integrado cada para los usuarios y equipos de contenedores. No puede destinar GP por unidad organizativa o departamento, realizar el filtrado de WMI o crear GPO personalizados.

- Servicios de dominio de Azure AD es compatible con el esquema básico de objeto de equipo de AD. No puede extender el esquema del objeto de equipo.


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Elevación y MAYÚS una aplicación local que utiliza la autenticación de enlace LDAP a servicios de infraestructura de Azure

![Enlace LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso tiene una aplicación local que se compró en fabricantes independientes de software hace varios años. La aplicación está en modo de mantenimiento con el proveedor de software y solicitar cambios en la aplicación es muy caro, de Contoso. Esta aplicación tiene un cliente basada en web que recopila las credenciales de usuario mediante un formulario web y, a continuación, autentica a los usuarios a través de un enlace LDAP a Active Directory corporativo. Contoso desea migrar esta aplicación a los servicios de infraestructura de Azure. Es conveniente que la aplicación funciona como, sin necesidad de los cambios. Además, los usuarios deben poder autenticar con sus credenciales de corporativas existentes y sin tener que realizar acciones de forma diferente a los usuarios de reciclaje. En otras palabras, los usuarios finales debe ser oblivious de donde se ejecuta la aplicación y la migración debe ser transparente a ellos.

**Notas de la implementación**

Tenga en cuenta los siguientes puntos importantes para este escenario de implementación:

- Asegúrese de que la aplicación no necesita modificar o escribir en el directorio. No se admite el acceso de escritura LDAP a dominios administrados proporcionada servicios de dominio de Active Directory de Azure.

- No puede cambiar las contraseñas directamente en el dominio administrado. Los usuarios pueden cambiar su contraseña o bien con mecanismo de cambio de contraseña de autoservicio de Azure AD o en el directorio local. Estos cambios están disponibles y sincronizados automáticamente en el dominio administrado.


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Elevación y MAYÚS una aplicación local que usa LDAP lee para acceder al directorio de servicios de infraestructura de Azure
Contoso tiene una aplicación de línea de negocio (LOB) local que se ha desarrollado casi hace diez años. Esta aplicación es directorio cuenta y se ha diseñado para funcionar con Windows Server AD. La aplicación utiliza LDAP (Protocolo ligero de acceso a directorios) para leer o atributos de información acerca de los usuarios desde Active Directory. La aplicación no modificar atributos o escribir en caso contrario, en el directorio. Contoso desea migrar esta aplicación a los servicios de infraestructura de Azure y retirar el hardware local de antigüedad hospedando esta aplicación. No puede volver a escribir la aplicación para usar las API como la API de Azure AD gráfico basada en el resto de directorio moderna. Por lo tanto, una opción de elevación y MAYÚS se desea que la aplicación se puede migrar para ejecutar en la nube, sin modificar el código o volver a escribir la aplicación.

**Notas de la implementación**

Tenga en cuenta los siguientes puntos importantes para este escenario de implementación:

- Asegúrese de que la aplicación no necesita modificar o escribir en el directorio. No se admite el acceso de escritura LDAP a dominios administrados proporcionada servicios de dominio de Active Directory de Azure.

- Asegúrese de que la aplicación no necesita un esquema de Active Directory extendida personalizado. Extensiones de esquema no son compatibles con servicios de dominio de Active Directory de Azure.


## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migrar una aplicación de servicio o daemon locales a servicios de infraestructura de Azure
Algunas aplicaciones constan de varios niveles, donde uno de los niveles necesita realizar llamadas autenticadas a un nivel de back-end como un nivel de base de datos. Cuentas de servicio de Active Directory se usan generalmente para estos casos de uso. Puede elevación y MAYÚS dichas aplicaciones a servicios de infraestructura de Azure y usar servicios de dominio de Azure AD para las necesidades de la identidad de estas aplicaciones. Puede usar la misma cuenta de servicio que se ha sincronizado desde el directorio local a Azure AD. Como alternativa, puede crear una OU personalizada y, a continuación, crear una cuenta de servicio independiente en esa OU para implementar dichas aplicaciones.

![Cuenta de servicio utilizando WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso tiene una aplicación de cámara de software personalizado que incluye un web front-end, SQL server y un servidor back-end FTP. Autenticación de Windows integrada de cuentas de servicio se utiliza para autenticar el front-end web en el servidor FTP. El front-end web está configurado para ejecutarse como una cuenta de servicio. El servidor está configurado para autorizar el acceso de la cuenta de servicio para web front-end. Contoso prefiere no tiene que implementar una máquina virtual de controlador de dominio en la nube para mover esta aplicación a los servicios de infraestructura de Azure. Del Contoso Administrador de TI puede implementar los servidores web front-end, SQL server y el servidor FTP a máquinas virtuales de Windows Azure. Estos equipos, a continuación, se unen a un dominio administrado de servicios de dominio de Active Directory de Azure. A continuación, puede usar la misma cuenta de servicio en su directorio local para la autenticación de la aplicación. Esta cuenta de servicio se sincroniza con el dominio de servicios de dominio de Active Directory de Azure administrado y está disponible para su uso.

**Notas de la implementación**

Tenga en cuenta los siguientes puntos importantes para este escenario de implementación:

- Asegúrese de que la aplicación utiliza el nombre de usuario y la contraseña para la autenticación. Autenticación de certificado o tarjeta inteligente según no es compatible con servicios de dominio de Active Directory de Azure.

- No puede cambiar las contraseñas directamente en el dominio administrado. Los usuarios pueden cambiar su contraseña o bien con mecanismo de cambio de contraseña de autoservicio de Azure AD o en el directorio local. Estos cambios están disponibles y sincronizados automáticamente en el dominio administrado.


## <a name="azure-remoteapp"></a>RemoteApp de Azure
RemoteApp Azure permite el Administrador de Contoso crear una colección de dominio. Esta característica permite aplicaciones remotas servidas por RemoteApp de Azure ejecutar en equipos unidos a un dominio y tener acceso a otros recursos que usan autenticación de Windows integrada. Contoso puede usar servicios de dominio de Active Directory de Azure para proporcionar un dominio administrado utilizado por colecciones de Azure RemoteApp unido al dominio.

![RemoteApp de Azure](./media/active-directory-domain-services-scenarios/azure-remoteapp.png)

Para obtener más información acerca de este escenario de implementación, vea el artículo de Blog de servicios de escritorio remoto titulado [elevación y MAYÚS las cargas de trabajo con RemoteApp de Azure y los servicios de dominio de Azure AD](http://blogs.msdn.com/b/rds/archive/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services.aspx).
