<properties
    pageTitle="Glosario de protección de identidad de Azure Active Directory | Microsoft Azure"
    description="Glosario de protección de identidad de Azure Active Directory"
    services="active-directory"
    keywords="protección de la identidad de Azure directorio activo, detección de aplicación de nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, vulnerabilidad, directiva de seguridad, Glosario"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-identity-protection-glossary"></a>Glosario de protección de identidad de Azure Active Directory 


### <a name="at-risk-user"></a>Riesgo (usuario)  
Un usuario con uno o varios de los eventos de riesgo activo. 

### <a name="atypical-sign-in-location"></a>Ubicación de inicio de sesión atípico   
Una sesión de una ubicación geográfica que no es habitual para el usuario específico, los usuarios similares o el inquilino.

### <a name="azure-ad-identity-protection"></a>Protección de la identidad de Azure AD    
Un módulo de seguridad de Azure Active Directory que le ofrece una vista consolidada en eventos de riesgos y posibles vulnerabilidad que afectan a las identidades de la organización.

### <a name="conditional-access"></a>Acceso condicional  
Una directiva para proteger el acceso a los recursos. Reglas de acceso condicional se almacenan en Azure Active Directory y se evalúan por Azure AD antes de conceder acceso al recurso.  Las reglas de ejemplo incluyen restringir el acceso según la ubicación, método de autenticación de usuario o de estado del dispositivo.

### <a name="credentials"></a>Credenciales     
Información que incluye la identificación y prueba de identificación que se usa para obtener acceso a local y recursos de red. Ejemplos de credenciales son los nombres de usuario y contraseñas, tarjetas inteligentes y certificados.

### <a name="event"></a>Evento   
Un registro de una actividad de Azure Active Directory.

### <a name="false-positive-risk-event"></a>Falso positivo (evento de riesgo) 
Estado de evento de riesgo establecer manualmente por un usuario de protección de identidad, que indica que el evento de riesgo se investiga y se ha marcado incorrectamente como un evento de riesgo.

### <a name="identity"></a>Identidad    
Una persona o entidad que debe comprobarse mediante autenticación, basándose en los criterios, como la contraseña o un certificado.

### <a name="identity-risk-event"></a>Evento de riesgo de identidad     
Evento AAD que se ha marcado como irregulares por la protección de identidad y a continuación, puede indicar que se ha comprometido una identidad.

### <a name="ignored-risk-event"></a>Omitido (evento de riesgo)    
Estado de evento de riesgo establecer manualmente por un usuario de protección de identidad, que indica que el evento de riesgo se cierra sin tener que realizar una acción de corrección.

### <a name="impossible-travel-from-atypical-locations"></a>Imposible viaje de ubicaciones atípicos   
Un evento de riesgo activado cuando se detectan dos inicios de sesión para el mismo usuario, donde al menos uno de ellos es desde una ubicación de inicio de sesión atípica, y el tiempo entre los inicios de sesión es menor que el tiempo mínimo que se tardarían física de viajes entre estas ubicaciones.  

###<a name="investigation"></a>Investigación    
El proceso de revisión de las actividades, los registros y otra información relevante relacionada con un evento de riesgo decidir si los pasos de corrección o mitigación son necesarios, entender si y cómo se ha comprometido la identidad y comprender cómo se utiliza la identidad comprometida.

### <a name="leaked-credentials"></a>Credenciales perdidas  

Un evento de riesgo activado cuando se encuentran credenciales del usuario actual (nombre de usuario y contraseña) publicado en la web oscura por nuestros investigadores.

### <a name="mitigation"></a>Mitigación  
Una acción para reducir o eliminar la capacidad de un intruso para explotar una identidad comprometido o dispositivo sin restaurar la identidad o el dispositivo a un estado seguro. Una mitigación no resuelve anterior eventos de riesgos asociados con la identidad o el dispositivo.

### <a name="multi-factor-authentication"></a>Autenticación multifactor 
Método de autenticación que requiere dos o más métodos de autenticación, que pueden incluir algo que el usuario tiene, como un certificado; algo que el usuario sabe, como nombres de usuario, contraseñas o frases; atributos físicos, como una huella digital; y atributos personales, como una firma personal.

### <a name="offline-detection"></a>Detección sin conexión   
La detección de anomalías y evaluación del riesgo de un evento como intento de inicio de sesión tras el hecho de un evento que ya se ha realizado.

### <a name="policy-condition"></a>Condición de directiva    
Parte de una directiva de seguridad que define las entidades (grupos, usuarios, aplicaciones, plataformas de dispositivos, Estados del dispositivo, intervalos de IP, tipos de cliente) incluidos en la directiva o excluir de ella.

### <a name="policy-rule"></a>Regla de directiva     
Parte de una directiva de seguridad que describe las circunstancias que activarían la directiva y las acciones realizadas cuando se activa la directiva.

### <a name="prevention"></a>Prevención  
Una acción para evitar daños a la organización mediante el abuso de una identidad o dispositivo sospechosos o saber para estar en peligro. Una acción de prevención no protege el dispositivo o identidad y no soluciona eventos de riesgo anterior.

### <a name="privileged-user"></a>Privilegios (usuario)
Un usuario que en el momento de un evento de riesgo, permisos de administrador permanentes o temporales para uno o más recursos en Azure Active Directory, por ejemplo, un administrador Global, Administrador de facturación, Administrador de servicios, Administrador de usuario y contraseña de administrador. 

###<a name="real-time"></a>En tiempo real    
Consulte detección en tiempo real.

###<a name="real-time-detection"></a>Detección en tiempo real  
La detección de anomalías y evaluación del riesgo de un evento como intento de inicio de sesión antes del evento puede continuar.

### <a name="remediated-risk-event"></a>Corregir (evento de riesgo)     
Estado de evento de riesgo establece automáticamente por la protección de identidad, que indica que el evento de riesgo se corrigen con las medidas correctivas estándar para este tipo de evento de riesgo. Por ejemplo, cuando se restablece la contraseña de usuario, muchos de los eventos de riesgo que indican que se ha comprometido la contraseña anterior se modifiquen automáticamente.

### <a name="remediation"></a>Corrección     
Una acción para proteger una identidad o un dispositivo anteriormente sospechosos o conocidos que estar en peligro. Acción de corrección restaura la identidad o el dispositivo a un estado seguro y resuelve anterior eventos de riesgos asociados con la identidad o el dispositivo.

### <a name="resolved-risk-event"></a>Resolver (evento de riesgo)   
Estado de evento de riesgo establecer manualmente por un usuario de protección de identidad, que indica que el usuario realiza una acción correspondiente corrección fuera de la protección de identidad y que se debe considerar el evento de riesgo cerrado.

###<a name="risk-event-status"></a>Estado de evento de riesgo    
Una propiedad de un evento de riesgo, que indica si el evento está activo y, si cierra la razón para cerrarlo.

###<a name="risk-event-type"></a>Tipo de evento de riesgo  
Una categoría para el evento de riesgo, que indica el tipo de anomalías que provocó el evento se considera arriesgada.

###<a name="risk-level-risk-event"></a>Nivel de riesgo (evento de riesgo)  
Indicación (alta, Media o baja) de la gravedad del evento de riesgo para ayudar a los usuarios de protección de identidad prioridades a las acciones llevan a reducir el riesgo a su organización. 

###<a name="risk-level-sign-in"></a>Nivel de riesgo (sesión) 
Indicación (alta, Media o baja) de la probabilidad de que un determinado inicio de sesión de, otra persona está intentando utilizar la identidad del usuario.

###<a name="risk-level-user-compromise"></a>Nivel de riesgo (peligro de usuario) 
Indicación (alta, Media o baja) de la probabilidad de que se ha comprometido una identidad.

### <a name="risk-level-vulnerability"></a>Nivel de riesgo (vulnerabilidad)  
Una indicación (alta, Media o baja) de la gravedad de la vulnerabilidad para ayudar a los usuarios de protección de identidad prioridades las acciones llevan para reducir el riesgo a su organización.

### <a name="secure-identity"></a>Seguro (identidad)   
Tomar medidas de corrección como un cambio de contraseña o máquina restaurar para restaurar una identidad potencialmente comprometida a un estado absoluta.

### <a name="security-policy"></a>Directiva de seguridad
Un conjunto de reglas de directiva y condición. Puede aplicar una directiva a entidades como usuarios, grupos, aplicaciones, dispositivos, plataformas de dispositivos, Estados del dispositivo, intervalos de IP y tipos de clientes de Auth2.0. Cuando se habilita una directiva, se evalúa siempre que una entidad incluida en la directiva se emite un símbolo de un recurso.

### <a name="sign-in-v"></a>Inicio de sesión (v) 
Para autenticar a una identidad de Azure Active Directory.

### <a name="sign-in-n"></a>Iniciar sesión (n) 
El proceso o acción de autenticación de una identidad de Azure Active Directory y el evento que capture esta operación.

###<a name="sign-in-from-anonymous-ip-address"></a>Iniciar sesión desde dirección IP anónima    
Desencadena un evento de riesgo después de un inicio de sesión correcta de dirección IP que se ha identificado como una dirección IP de proxy anónimo.

###<a name="sign-in-from-infected-device"></a>Iniciar sesión desde un dispositivo infectado 
Un evento de riesgo activado cuando una sesión procede de una dirección IP que se conoce para usarlo con uno o varios dispositivos peligro, que actualmente están intentando comunicarse con un servidor de robot.

###<a name="sign-in-from-ip-address-with-suspicious-activity"></a>Iniciar sesión desde la dirección IP con actividad sospechosa 
Un evento de riesgo desencadenado después de un inicio de sesión correctamente desde una dirección IP de direcciones con un gran número de intentos de inicio de sesión a través de varias cuentas de usuario durante un período de tiempo breve.

###<a name="sign-in-from-unfamiliar-location"></a>Iniciar sesión desde una ubicación desconocida 
Un evento de riesgo activado cuando un usuario inicia correctamente sesión desde una nueva ubicación (IP, latitud y longitud y ASN).

###<a name="sign-in-risk"></a>Inicio de sesión de riesgo     
Ver riesgo nivel (sesión)

###<a name="sign-in-risk-policy"></a>Directiva de inicio de sesión de riesgo  
Una directiva de acceso condicional que evalúa el riesgo a una sesión específica y se aplica a su eliminación basada en reglas y condiciones predefinidas.

###<a name="user-compromise-risk"></a>Riesgo de peligro de usuario     
Ver riesgo nivel (peligro de usuario)

###<a name="user-risk"></a>Riesgo de usuario    
Ver riesgo nivel (peligro de usuario).

###<a name="user-risk-policy"></a>Directiva de riesgo de usuario     
Una directiva de acceso condicional que considera el inicio de sesión y se aplica a su eliminación basada en reglas y condiciones predefinidas.

###<a name="users-flagged-for-risk"></a>Usuarios marcados para riesgo   
Usuarios que tienen los eventos de riesgo que están activas o corregida

###<a name="vulnerability"></a>Vulnerabilidad    
Una configuración o la condición de Azure Active Directory, lo que hace que el directorio susceptible a ataques o amenazas.


## <a name="see-also"></a>Vea también

- [Protección de la identidad de Azure Active Directory](active-directory-identityprotection.md)
