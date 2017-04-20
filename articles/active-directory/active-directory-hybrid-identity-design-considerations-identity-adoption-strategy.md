<properties
    pageTitle="Azure Active Directory híbrido identidad consideraciones de diseño - definir una estrategia de adopción de identidad híbrida | Microsoft Azure"
    description="Con control de acceso condicional Azure Active Directory comprueba las condiciones específicas que elegir para autenticar al usuario y antes de permitir el acceso a la aplicación. Una vez que se cumplen las condiciones, el usuario autenticado y permite el acceso a la aplicación."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definir una estrategia de adopción de identidad híbrido

En esta tarea, deberá definir la estrategia de adopción de identidad híbrido para la solución de identidad híbrido cumplir con los requisitos empresariales que se describen en:

- [Determinar la necesidad de negocio](active-directory-hybrid-identity-design-considerations-business-needs.md)
- [Determinar los requisitos de sincronización de directorios](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
- [Determinar los requisitos de autenticación multifactor](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definir la estrategia de necesidades empresariales
Debe determinar el negocio de las organizaciones las primeras direcciones de tarea.  Esto puede ser muy amplia y arrastre del ámbito puede ocurrir si no tiene cuidado.  En el principio Simplifique pero recuerde siempre planear un diseño que acomodar y facilitar el cambio en el futuro.  Independientemente de si es un diseño simple o un archivo muy complejas, Azure Active Directory es la plataforma de Microsoft Identity compatible con aplicaciones de Office 365, Microsoft Online Services y la nube.

## <a name="define-an-integration-strategy"></a>Definir una estrategia de integración
Microsoft tiene tres escenarios de integración principal que son las identidades de la nube, identidades sincronizadas y las identidades federadas.  Debería considerar la adopción de una de estas estrategias de integración.  La estrategia que elija puede variar y puede incluir las decisiones para elegir uno, ¿qué tipo de experiencia de usuario que desea proporcionar, tiene algunas de la infraestructura existente ya en el lugar y ¿qué es la más rentables.  
 
![](./media/hybrid-id-design-considerations/integration-scenarios.png)

Se muestran los escenarios definidos en la figura anterior:

- **Las identidades de nube**: se trata de identidades que existen únicamente en la nube.  ¿En el caso de Azure AD residen específicamente en su directorio de Azure AD.
- **Sincronizado**: se trata de identidades que existen local y en la nube.  Uso de Azure AD Connect, estos usuarios se creen o Unidos con cuentas de Azure AD existentes.  Hash de la contraseña del usuario se ha sincronizado desde el entorno local en la nube en lo que se denomina hash de la contraseña.  Cuando sincroniza utilizando la única condición es que si un usuario está deshabilitado en el entorno local, puede tardar hasta 3 horas para que muestre en Azure AD del estado de la cuenta.  Esto es debido al intervalo de tiempo de sincronización.
- **Federado**: estas identidades existen dos local y en la nube.  Uso de Azure AD Connect, estos usuarios se creen o Unidos con cuentas de Azure AD existentes.  
 
>[AZURE.NOTE]
Para obtener más información acerca de la sincronización opciones leer [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

La siguiente tabla le ayudará a determinar las ventajas y desventajas de cada una de las siguientes estrategias:

| Estrategia         | Ventajas                                                                                                                                                                                                                                                  | Desventajas                                                                                                                                                                                                                                                                                                                                                  |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Identidades de nube** | Fácil de administrar para pequeñas empresas. <br> No hay nada que instalar hardware adicional No en local es necesario<br>Deshabilitar fácilmente si el usuario deja la empresa                                                                                                   | Los usuarios tendrán que el inicio de sesión al acceder a las cargas de trabajo en la nube <br> Las contraseñas pueden o no pueden ser el mismo para identidades de nube y local                                                                                                                                                                                                                     |
| **Sincronizar**     | Contraseña local se autenticar ambos local y directorios de la nube <br>Más fácil de administrar para las organizaciones pequeñas, medianas o grandes <br>Los usuarios pueden tener inicio de sesión único (SSO) para algunos recursos <br> Método preferido de Microsoft para la sincronización <br> Más fácil de administrar | Algunos clientes pueden ser reacios a sincronizar sus directorios con la nube de vencimiento policía específicas de la empresa                                                                                                                                                                                                                                                  |
| **Federado**        | Los usuarios pueden tener inicio de sesión único (SSO) <br>Si un usuario finaliza o abandona, la cuenta puede estar deshabilitada inmediatamente y revoca el acceso,<br> Es compatible con escenarios avanzados que no se pueden realizar con sincronizados                                           | Pasos más para instalar y configurar <br> Mantenimiento superior <br> Puede requerir hardware adicional para la infraestructura de STS <br> Pueden requerir hardware adicional para instalar al servidor de federación. Se requiere software adicional si se usa AD FS <br> Requerir configuración amplia para SSO <br> Tareas críticas el punto de error si el servidor de federación es hacia abajo, los usuarios no podrán autenticar |

### <a name="client-experience"></a>Experiencia del cliente
La estrategia que use determinará la experiencia de inicio de sesión del usuario.  Las tablas siguientes proporcionan información sobre lo que los usuarios deben esperar su experiencia de inicio de sesión sea.  Tenga en cuenta que no todos los proveedores de identidades federadas admitan SSO en todos los escenarios.

**Aplicaciones de red privada y unido dominios**:
 

|                              | Identidad sincronizada      | Identidad federada                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Exploradores Web                 | Autenticación basada en formularios | inicio de sesión único, a veces debe proporcionar el identificador de la organización |
| Outlook                      | Solicitud de credenciales     | Solicitud de credenciales                                       |
| Skype empresarial (Lync)    | Solicitud de credenciales     | inicio de sesión único en para Lync, se le solicita las credenciales de Exchange   |
| SkyDrive Pro                 | Solicitud de credenciales     | inicio de sesión único                                               |
| Office Pro Plus suscripción | Solicitud de credenciales     | inicio de sesión único                                               |

**Externos o de fuentes de confianza**:

|                              | Identidad sincronizada      | Identidad federada                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Exploradores Web                 | Autenticación basada en formularios |  Autenticación basada en formularios |
| Outlook, Skype empresarial (Lync), Skydrive Pro, suscripción de Office| Solicitud de credenciales     | Solicitud de credenciales                                       |
| Exchange ActiveSync    | Solicitud de credenciales     | inicio de sesión único en para Lync, se le solicita las credenciales de Exchange   |
| Aplicaciones móviles                 | Solicitud de credenciales     | Solicitud de credenciales                                               |

Si ha decidido de tarea 1 que tiene un 3 º IdP o son ir para utilizar uno para proporcionar la federación con Azure AD de terceros, debe tener en cuenta las siguientes capacidades compatibles:
- Cualquier proveedor de SAML 2.0 que es compatible con el perfil SP interacción puede admitir autenticación de Azure AD y aplicaciones asociadas
- Es compatible con autenticación pasiva, lo que facilita auth SPO, OWA, etcetera.
- Se admiten clientes de Exchange Online a través de la SAML 2.0 mejorada cliente perfil (ECP)

También debe conocer qué capacidades no estarán disponibles:

- Sin la compatibilidad de WS-confianza/federación romperá todos los demás clientes activos
 - Esto significa que no hay cliente de Lync, el cliente de OneDrive, suscripción de Office, Office Mobile antes de Office 2016
- Transición de Office a autenticación pasiva permitirá que admita IdPs puros SAML 2.0, pero soporte seguirá apareciendo en una base de cliente por cliente


>[AZURE.NOTE]
Para la lista más actualizada lea el artículo de http://aka.ms/ssoproviders.

## <a name="define-synchronization-strategy"></a>Definir la estrategia de sincronización
En esta tarea definirá las herramientas que se utilizará para sincronizar de la organización local datos a la nube y topología debe usar.  Dado que la mayoría de organizaciones utilizan Active Directory, se proporciona información sobre el uso de Azure AD Connect para tratar cuestiones anteriores en detalle.  Para entornos que no tienen Active Directory, hay información sobre el uso de FIM 2010 R2 o 2016 MIM para planear la estrategia de este.  Sin embargo, en futuras versiones de Azure AD Connect admitirá directorios LDAP, así que, dependiendo de su escala de tiempo, esta información puede ayudar a.

###<a name="synchronization-tools"></a>Herramientas de sincronización
En los años, varias herramientas de sincronización tienen existía y se utilizan para varios escenarios.  Actualmente Azure AD Connect es la ir a la mejor herramienta para todos los escenarios compatibles.  Sincronización de AAD y sincronización de directorios son también sigue alrededor e incluso puede haber en su entorno ahora. 

>[AZURE.NOTE]
Para obtener información más reciente sobre las capacidades compatibles de cada herramienta, lea el artículo de la [comparación de herramientas de integración de directorios](active-directory-hybrid-identity-design-considerations-tools-comparison.md) .  

### <a name="supported-topologies"></a>Topologías compatibles
Al definir una estrategia de sincronización, se debe determinar la topología que se usa. Según la información que se ha determinado en el paso 2 para determinar qué topología es la adecuada para usar. El único bosque único topología de Azure AD es más comunes y consta de un único bosque de Active Directory y una única instancia de Azure AD.  Esto se va a utilizar en la mayoría de los casos y es la topología esperada al usar instalación Express conectar de Azure AD tal como se muestra en la figura siguiente.
 
![](./media/hybrid-id-design-considerations/single-forest.png)Bosque único escenario es muy común para las organizaciones grandes y pequeñas incluso tener varios bosques, tal como se muestra en la figura 5.

>[AZURE.NOTE]
Para obtener más información acerca de las diferentes local y topologías de Azure AD con Azure AD Connect sincronización lea el artículo [topologías para Azure AD Connect](active-directory-aadconnect-topologies.md).


![](./media/hybrid-id-design-considerations/multi-forest.png) 

Escenario de varios bosque

Si este el caso, a continuación, en la topología de múltiples bosques solo Azure AD debe tener en cuenta si se cumplen los siguientes elementos:

- Los usuarios tienen identidad solo 1 de todos los bosques: la sección Identificación de usuarios a continuación describe en más detalle.
- El usuario se autentica al bosque donde se encuentra su identidad
- UPN y anclaje de origen (id inmutable) proceden este bosque
- Todos los bosques tienen acceso a Azure AD Connect: Esto significa que no es necesario ser el dominio unido y se puede colocar en una DMZ si esto facilita este.
- Los usuarios tienen sólo un buzón
- El bosque que hospeda el buzón de un usuario tiene la mejor calidad de datos para los atributos visibles en lista Global de direcciones (GAL) de Exchange
- Si no hay ningún buzón en el usuario, cualquier bosque puede utilizarse para contribuir estos valores
- Si tiene un buzón vinculado, a continuación, también hay otra cuenta en un bosque diferente que se usa para iniciar sesión.

>[AZURE.NOTE]
Objetos que existen en ambos local y en la nube "conectados" a través de un identificador único. En el contexto de sincronización de directorios, este identificador único se conoce como la SourceAnchor. En el contexto de inicio de sesión único, esto se conoce como la ImmutableId. [Conceptos de diseño de Azure AD Connect](active-directory-aadconnect-design-concepts.md#sourceanchor) para obtener más consideraciones sobre el uso de SourceAnchor.

Si las respuestas anteriores no son verdaderas y tiene más de una cuenta activa o más de un buzón, Azure AD Connect se seleccione uno y omitir el otro.  Si ha vinculado buzones pero ninguna otra cuenta, estas cuentas no se exportarán a Azure AD y que el usuario no será un miembro de los grupos.  Esto es diferente de cómo era en el pasado con la sincronización de directorios y es compatible con los intencionado mejor estos escenarios de varios bosques. Un escenario de varios bosque se muestra en la siguiente ilustración.
 
![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png) 
 
**Varios bosques varios escenario de Azure AD**

Se recomienda que solo un único directorio de Azure AD para una organización, pero es compatible una relación de 1:1 se mantiene entre un servidor de sincronización de Azure AD Connect y un directorio de Azure AD.  Para cada instancia de Azure AD, necesitará una instalación de Azure AD Connect.  Además, Azure AD por diseño se aislará y los usuarios en una instancia de Azure AD no podrán ver los usuarios en otra instancia.

Es posible y compatibles para conectarse a una instancia local de Active Directory a múltiples directorios de Azure AD tal como se muestra en la figura siguiente:

![](./media/hybrid-id-design-considerations/single-forest-flitering.png) 
 

**Bosque único escenario de filtrado**

Para hacer esto siguiente debe ser verdadero:

- Los servidores de sincronización de Azure AD Connect deben estar configurados para el filtrado para que cada uno de ellos tenga un conjunto de objetos se excluyen mutuamente.  Esto lo hace, por ejemplo, especificar el ámbito de cada servidor de un dominio en particular o una unidad organizativa.
- Solo se puede registrar un dominio DNS en un único directorio de Azure AD así el UPN de los usuarios locales en AD debe utilizar espacios de nombres independientes
- Los usuarios de una instancia de Azure AD solo podrán ver los usuarios de su instancia.  No podrán ver los usuarios en las demás instancias
- Solo uno de los directorios de Azure AD puede habilitar híbrida de Exchange con las instalaciones AD
- Exclusividad mutuo también se aplica para reescritura.  De esta forma, algunas características de reescritura no compatibles con esta topología ya que estos suponen una configuración local única.  Esto incluye:
 - Reescritura de grupo con la configuración predeterminada
 - Reescritura de dispositivo


Tenga en cuenta que la siguiente no es compatible y no debe seleccionarse como una implementación:

- No se admite para tener varios servidores de sincronización de Azure AD Connect conectarse en el mismo directorio de Azure AD incluso si se han configurado para sincronizar mutuamente conjunto de objeto
- No es compatible para sincronizar el mismo usuario en varios directorios de Azure AD. 
- Además, no es compatible para realiza un cambio para hacer que los usuarios en una Azure AD aparezca como contactos en el directorio de Azure AD otra configuración. 
- También es compatible para modificar la sincronización de Azure AD Connect para conectarse a varios directorios de Azure AD.
- Directorios de Azure AD están aislado de diseño. Es compatible para cambiar la configuración de sincronización de Azure AD Connect para leer datos de otro directorio de Azure AD en un intento de crear una GAL común y unificada entre los directorios. También no es compatible para exportar los usuarios como contactos a otro locales con Azure AD Connect la sincronización de AD.


>[AZURE.NOTE]
Si su organización restringe los equipos de la red de la conexión a Internet, este artículo enumeran los extremos (FQDN, IPv4 y IPv6 intervalos de direcciones) que se deben incluir en la salida permite listas y la zona de sitios de confianza de Internet Explorer del cliente de equipos para asegurarse de que sus equipos correctamente pueden usar Office 365. Para obtener más información, lea [las direcciones URL de Office 365 y los intervalos de direcciones IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).

## <a name="define-multi-factor-authentication-strategy"></a>Definir la estrategia de autenticación multifactor
En esta tarea definirá la estrategia de autenticación multifactor.  La autenticación multifactor Azure viene en dos versiones diferentes.  Una es basada en nube y la otra es local con el servidor de Azure AMF.  En función de la evaluación por encima de la puede determinar qué solución es la correcta para su estrategia.  Utilice la siguiente tabla para determinar qué opción de diseño que mejor satisfacer los requisitos de seguridad de su empresa:

Opciones de diseño de varios factores:

| Activo para proteger                                               | AMF en la nube | AMF local |
|---------------------------------------------------------------|------------------|----------------|
| Aplicaciones de Microsoft                                                | Sí              | Sí            |
| Aplicaciones de SaaS en la Galería de aplicación                                  | Sí              | Sí            |
| Aplicaciones de IIS publicadas a través de Proxy de aplicación de Azure AD         | Sí              | Sí            |
| Aplicaciones de IIS no publicadas a través del Proxy de aplicación de Azure AD | No               | Sí            |
| Acceso remoto como VPN, RDG                                     | No               | Sí            |

Aunque se puede que haya decidido por una solución para su estrategia, necesita usar la evaluación de los anteriores en donde se encuentran los usuarios.  Esto puede provocar la solución cambiar.  Utilice la siguiente tabla para ayudarle a determinar esto:

| Ubicación de usuario                                                       | Opción de diseño preferido                 |
|---------------------------------------------------------------------|-----------------------------------------|
| Azure Active Directory                                              | Multi FactorAuthentication en la nube |
| Azure AD y utiliza la federación con AD FS de AD local             | Ambos                                    |
| Azure AD y local AD con Azure AD no Connect sincronización de la contraseña | Ambos                                    |
| Azure AD y locales con Azure AD Connect con la sincronización de contraseña  | Ambos                                    |
| AD local                                                      | Servidor de autenticación multifactor      |

>[AZURE.NOTE]
También debe asegurarse de que la opción de diseño de autenticación multifactor que seleccionó es compatible con las características que son necesarias para el diseño.  Para obtener más información, lea [Elegir la solución de seguridad de varios factores para usted](../multi-factor-authentication-get-started.md#what-am-i-trying-to-secure).

## <a name="multi-factor-auth-provider"></a>Proveedor de autenticación multifactor
Autenticación multifactor está disponible para los administradores globales que tienen un inquilino de Azure Active Directory de forma predeterminada. Sin embargo, si desea ampliar la autenticación multifactor a todos los usuarios o desea a los administradores globales pueden sacar partido características como el portal de administración, saludos personalizados e informes, debe comprar y configurar el proveedor de autenticación multifactor.

>[AZURE.NOTE]
También debe asegurarse de que la opción de diseño de autenticación multifactor que seleccionó es compatible con las características que son necesarias para el diseño. 

##<a name="next-steps"></a>Pasos siguientes
[Determinar los requisitos de protección de datos](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Vea también
[Información general de las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)
